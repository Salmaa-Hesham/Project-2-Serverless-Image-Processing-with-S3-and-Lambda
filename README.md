# Project-2-Serverless-Image-Processing-with-S3-and-Lambda

## Overview

This project implements a **serverless image processing application** using AWS managed services. 

Users upload images through an API endpoint. The images are stored in Amazon S3, which triggers a processing workflow. The images are resized and/or watermarked using AWS Lambda and then saved to a separate S3 bucket. Metadata about each processed image is stored in Amazon DynamoDB.

The architecture follows an **event-driven serverless design**.

---

## Architecture

### Services Used

- **Amazon S3**
  - Stores original uploaded images
  - Stores processed images

- **AWS Lambda**
  - Executes image processing (resize / watermark)

- **Amazon API Gateway**
  - Exposes an API endpoint for image uploads

- **Amazon DynamoDB**
  - Stores metadata about processed images

- **AWS Step Functions**
  - Orchestrates processing workflow steps

---

## Architecture Flow

1. User uploads image via **API Gateway**
2. Image is stored in **S3 (Original Images bucket)**
3. S3 event triggers:
   - **Step Functions** to orchestrate workflow
   - Which invokes **Lambda** for processing
4. Lambda:
   - Resizes / watermarks the image
   - Stores processed image in **S3 (Processed Images bucket)**
   - Saves metadata to **DynamoDB**

---

## Event-Driven Design

The system is built using an event-driven architecture:

- S3 triggers the processing workflow
- Lambda runs only when needed
- No servers are provisioned or managed

This ensures automatic scaling and cost efficiency.

---

## Security – IAM Configuration

This project applies the principle of **least privilege** using dedicated IAM roles for each service.

### 1. Lambda Execution Role

The Lambda function is assigned an IAM role with permissions to:

- Read from the **Original S3 bucket**
  - `s3:GetObject`
- Write to the **Processed S3 bucket**
  - `s3:PutObject`
- Store image metadata in **DynamoDB**
  - `dynamodb:PutItem`
  - `dynamodb:UpdateItem`
- Write logs to **CloudWatch**
  - `logs:CreateLogGroup`
  - `logs:CreateLogStream`
  - `logs:PutLogEvents`

---

### 2. Step Functions Role

Step Functions has an IAM role that allows it to:

- Invoke the Lambda function
  - `lambda:InvokeFunction`

This enables secure orchestration of the image processing workflow.

---

### 3. S3 Security

- Public access is blocked on both buckets.
- Access is restricted to the Lambda execution role.
- Bucket policies enforce controlled access. 

- Pay-per-use cost model
- Secure and maintainable design
