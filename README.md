# WebApplication1

This is the **main.yml** file for executing the github action workflow to create a .NET 8 Web API Docker image and upload it to AWS ECR 

```yaml
name: Build and Push Docker Image

on:
  push:
    branches: [ master ]

env:
  ECR_REGISTRY: public.ecr.aws/x6y4g2f4  # Your AWS ECR Registry
  IMAGE_NAME: dotnet8webapirepo  # Replace with your image name
  AWS_REGION: us-east-1  # Replace with your AWS region

jobs:
  build_and_push:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v1

    - name: Set up AWS CLI
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ${{ env.AWS_REGION }}

    - name: Login to Amazon ECR
      run: |
        aws ecr-public get-login-password --region ${{ env.AWS_REGION }} | docker login --username AWS --password-stdin ${{ env.ECR_REGISTRY }}
    - name: Build, tag, and push image to Amazon ECR
      run: |
        docker build -t ${{ env.ECR_REGISTRY }}/${{ env.IMAGE_NAME }}:latest .
        docker push ${{ env.ECR_REGISTRY }}/${{ env.IMAGE_NAME }}:latest
```
