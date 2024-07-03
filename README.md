import { Injectable } from '@nestjs/common';
import { TableClient, TableTransaction } from '@azure/data-tables';

@Injectable()
export class ProductService {
  private readonly tableClient: TableClient;
  private readonly counterTableClient: TableClient;

  constructor() {
    const connectionString = process.env.AZURE_STORAGE_CONNECTION_STRING;
    this.tableClient = TableClient.fromConnectionString(connectionString, 'products');
    this.counterTableClient = TableClient.fromConnectionString(connectionString, 'counters');
  }

  private async getCurrentCounter(partitionKey: string): Promise<number> {
    try {
      const entity = await this.counterTableClient.getEntity('counterPartition', partitionKey);
      return entity.counter;
    } catch (error) {
      if (error.statusCode === 404) {
        // If the counter doesn't exist, initialize it to 0
        return 0;
      }
      throw error;
    }
  }

  private async incrementCounter(partitionKey: string): Promise<number> {
    let retryCount = 0;
    const maxRetries = 5;

    while (retryCount < maxRetries) {
      try {
        const entity = await this.counterTableClient.getEntity('counterPartition', partitionKey);
        const newCounter = entity.counter + 1;

        const transaction = new TableTransaction();
        transaction.updateEntity(
          {
            partitionKey: 'counterPartition',
            rowKey: partitionKey,
            counter: newCounter,
            etag: entity.etag,
          },
          'Merge',
        );

        await this.counterTableClient.submitTransaction(transaction.actions);

        return newCounter;
      } catch (error) {
        if (error.statusCode === 404) {
          // If the counter doesn't exist, initialize and create it
          const initialCounter = 1;
          await this.counterTableClient.createEntity({
            partitionKey: 'counterPartition',
            rowKey: partitionKey,
            counter: initialCounter,
          });
          return initialCounter;
        }
        retryCount++;
      }
    }
    throw new Error('Failed to increment counter after multiple retries');
  }

  public async createProduct(partitionKey: string, product: any): Promise<any> {
    const newRowKey = await this.incrementCounter(partitionKey);
    const productEntity = {
      partitionKey,
      rowKey: newRowKey.toString(),
      ...product,
    };

    await this.tableClient.createEntity(productEntity);
    return productEntity;
  }
}
