import {
  CallHandler,
  ExecutionContext,
  Injectable,
  NestInterceptor,
  NotFoundException,
} from '@nestjs/common';
import { Cache } from 'cache-manager';
import { InjectCache } from '@nestjs/cache-manager';
import { Observable, of } from 'rxjs';
import { map, tap } from 'rxjs/operators';

@Injectable()
export class CacheInterceptor implements NestInterceptor {
  constructor(@InjectCache() private cacheManager: Cache) {}

  async intercept(context: ExecutionContext, next: CallHandler): Promise<Observable<any>> {
    const request = context.switchToHttp().getRequest();
    const { method, params, body } = request;
    const cacheKey = 'products';

    if (method === 'GET') {
      if (params.id) {
        // Get single product
        const cachedItem = await this.cacheManager.get(`product_${params.id}`);
        if (cachedItem) {
          return of(cachedItem);
        }
      } else {
        // Get all products
        const cachedProducts = await this.cacheManager.get(cacheKey);
        if (cachedProducts) {
          return of(cachedProducts);
        }
      }
    }

    return next.handle().pipe(
      tap(async (response) => {
        if (method === 'POST') {
          // Add product
          let cachedProducts = (await this.cacheManager.get(cacheKey)) || [];
          cachedProducts = Array.isArray(cachedProducts) ? cachedProducts : [];
          cachedProducts.push(response);
          await this.cacheManager.set(cacheKey, cachedProducts);

          await this.cacheManager.set(`product_${response.id}`, response);
        } else if (method === 'PUT') {
          // Edit product
          let cachedProducts = (await this.cacheManager.get(cacheKey)) || [];
          cachedProducts = cachedProducts.map((product) =>
            product.id === params.id ? { ...product, ...body } : product,
          );
          await this.cacheManager.set(cacheKey, cachedProducts);

          await this.cacheManager.set(`product_${params.id}`, { ...body, id: params.id });
        } else if (method === 'DELETE') {
          // Delete product
          let cachedProducts = (await this.cacheManager.get(cacheKey)) || [];
          cachedProducts = cachedProducts.filter((product) => product.id !== params.id);
          await this.cacheManager.set(cacheKey, cachedProducts);

          await this.cacheManager.del(`product_${params.id}`);
        } else if (method === 'GET' && params.id) {
          // Cache single product get response
          await this.cacheManager.set(`product_${params.id}`, response);
        } else if (method === 'GET') {
          // Cache all products get response
          await this.cacheManager.set(cacheKey, response);
        }
      }),
    );
  }
}
