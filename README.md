name: Deploy to AKS

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Azure CLI
        uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: AKS Get Credentials
        run: |
          az aks get-credentials --resource-group ${{ secrets.AZURE_RESOURCE_GROUP }} --name ${{ secrets.AKS_CLUSTER_NAME }}

      - name: Create Kubernetes secret with PFX file
        env:
          PFX_BASE64: ${{ secrets.PFX_BASE64 }}
        run: |
          echo "$PFX_BASE64" | base64 -d > certificate.pfx
          kubectl create secret generic my-pfx-secret --from-file=certificate.pfx


name: Deploy to AKS

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Azure CLI
        uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: AKS Get Credentials
        run: |
          az aks get-credentials --resource-group ${{ secrets.AZURE_RESOURCE_GROUP }} --name ${{ secrets.AKS_CLUSTER_NAME }}

      - name: Create Kubernetes secret with PFX file
        run: |
          kubectl create secret generic my-pfx-secret --from-file=certificate.pfx=resources/your-certificate.pfx
