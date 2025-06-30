# AWS Video Streaming Infrastructure Documentation

## Project Overview
This CloudFormation template configures a comprehensive AWS-based infrastructure for video streaming, supporting both live and pre-recorded video content. The setup enables ingestion, processing, packaging, and delivery of video streams with scalability and efficiency.

## AWS Services Configured

### 1. **AWS S3**
- **Purpose**: Stores raw and transcoded video content.
- **Details**:
  - Buckets configured for raw video uploads (`RawBucket`), transcoded output storage (`ProcessedBucket`), and web hosting (`WebBucket`).
  - Secure access with IAM policies for authorized uploads and retrievals.
  - CORS enabled on `ProcessedBucket` for content delivery.
  - `WebBucket` configured for static website hosting with public read access.

### 2. **AWS SDK**
- **Purpose**: Facilitates programmatic video uploads to S3.
- **Details**:
  - Enables integration with applications for seamless upload workflows via presigned URLs.
  - Supports secure authentication and data transfer to `RawBucket`.

### 3. **AWS MediaLive**
- **Purpose**: Ingests live streams via the SRT (Secure Reliable Transport) protocol.
- **Details**:
  - Configured to handle real-time video ingestion.
  - Supports high-quality, low-latency live streaming.

### 4. **AWS MediaConvert**
- **Purpose**: Transcodes pre-recorded videos for adaptive bitrate streaming.
- **Details**:
  - Converts raw videos into HLS format with multiple resolutions (e.g., 1080p, 720p).
  - Job template (`MovieHLS`) configured with CBR and QVBR encoding settings.
  - Outputs stored in `ProcessedBucket`.

### 5. **AWS MediaPackage**
- **Purpose**: Packages live streams for delivery.
- **Details**:
  - Prepares live video streams for playback with adaptive streaming protocols (e.g., HLS, DASH).
  - Ensures compatibility with a wide range of devices.

### 6. **AWS CloudFront**
- **Purpose**: Caches and delivers pre-recorded and live video content.
- **Details**:
  - Acts as a Content Delivery Network (CDN) with `ProcessedBucket` as the origin.
  - Configured with HTTPS-only access and HTTP/2 support.
  - Uses Origin Access Control for secure S3 access.

### 7. **AWS Lambda**
- **Purpose**: Automates workflows for video processing and status updates.
- **Details**:
  - `GeneratePresignedUrl`: Creates presigned URLs for secure S3 uploads.
  - `TriggerMediaConvert`: Initiates MediaConvert jobs upon S3 uploads.
  - `UpdateUploadStatus`: Updates upload status in DynamoDB.
  - `QueryUploadStatus`: Retrieves upload status from DynamoDB.

### 8. **AWS API Gateway**
- **Purpose**: Provides APIs for video upload and status querying.
- **Details**:
  - `MovieUploadApi`: Handles presigned URL generation for video uploads.
  - `MovieStatusApi`: Allows querying of upload and processing status.

### 9. **AWS DynamoDB**
- **Purpose**: Tracks video upload and processing status.
- **Details**:
  - Table (`MovieUploadStatus`) stores file metadata and status with `FileName` as the partition key.
  - Pay-per-request billing for cost efficiency.

## Architecture Diagram
The following Mermaid flowchart illustrates the video streaming workflow. A live link to the diagram: [Mermaid Live Link](https://mermaid.live/edit#pako:eNptVFtv2jAU_iuWp765VYBy86RJQMqtoHWlbNJCHww5BYvEjmwHykr_-xwnzcLUPOHvnO_COU7e8EaGgCneKpbs0JO_Esg-V1doqUGhiTCg2MZwKfJCL8jwZ3R9_e28TCLJQo1-8hDkGfWD3sMEjZiBIztRNJcHDnlLL-HPBd0Rf6SgOGi0MMyk-owGnzDzWs4sM1Xacqzv9CbiIPdghfxgxuJ1yCgagbDBDTwo0HwrIFyqqMgwuOTclZws1ilPnLtXrReN6jB0jvtO6sNLo-Xj7IyGwaJB0SM79tPNHkzhOnStT4pvt6Cs7ai0XSahJf_n-xllXFIKbA4hZwMpDqBMNat_EiyWfj8HRsWyMherMgk-yhc7unC-q67JUiraVc8cHLvmgQI3gqlcn9E0qLYVPuPZopCfOsbCSLsc9D01SWrO6N6N7UHJDWgNYTm80nkQyTQcKikK3_tcBdTBqlgrA8LKzIJ_fcjn2ii-TrOVFd4zx_Ih4jaaRsZe3F7F5Bes0Vhqw8U2x-YuloWLQI6edWjkPECElwoLc4pK9iZiWvvwgthRoxceRfTLcNjteh6xwez9o-uIbfbF4frIQ7Oj9eT1a4WNeqRPBsQnd2RIRmRMJmRK7smMzDNR24mJfXl5iKlRKRAcg4pZdsRvmcoKmx3EsMLU_gyZ2q_wSrxbTsLEbynjD5qS6XaH6QuLtD2l7rb4nNnPQlyiyv5ZUAOZCoNpp95xIpi-4VdM663bm6bnec1mu9lutWu2eMK01r5pdGrdbq3ebbUb9VbrneA_ztW7abdqHQveNlvNrtet19__AjAbdR4).

## Architecture Summary
The CloudFormation template orchestrates the following workflow:
1. Users request presigned URLs via `MovieUploadApi` to upload videos to `RawBucket`.
2. `GeneratePresignedUrl` Lambda generates secure upload URLs.
3. S3 uploads trigger `UpdateUploadStatus` to log status in DynamoDB and `TriggerMediaConvert` to start transcoding.
4. MediaConvert processes videos using the `MovieHLS` template, storing outputs in `ProcessedBucket`.
5. Live streams are ingested via MediaLive using the SRT protocol.
6. MediaPackage packages live streams for adaptive streaming.
7. CloudFront caches and delivers both live and pre-recorded content to users.
8. Users query upload status via `MovieStatusApi`, which retrieves data from DynamoDB.
9. `WebBucket` hosts a frontend for user interaction.

## Deployment Notes
- **Status**: The CloudFormation stacks are fully deployed and operational.
- **Scalability**: The infrastructure leverages AWS auto-scaling (e.g., DynamoDB pay-per-request, Lambda concurrency).
- **Security**: IAM roles, Origin Access Control, and public access restrictions ensure secure operations.
- **Monitoring**: Use AWS CloudWatch for performance and error tracking.

## Next Steps
- Add the Mermaid live link to the diagram section once available.
- Monitor performance using AWS CloudWatch.
- Test video upload, transcoding, and streaming workflows.
- Optimize CloudFront caching, MediaConvert settings, and Lambda timeouts based on usage patterns.
