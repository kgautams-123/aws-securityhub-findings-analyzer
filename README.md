# AWS SecurityHub Findings Summarizer with AI-Powered Analysis

## Overview
This solution automates the collection and analysis of AWS SecurityHub findings using Amazon Bedrock's Claude 3 Sonnet model. It generates daily email reports containing AI-powered analysis of security findings, helping security teams quickly understand and respond to security issues across AWS accounts.

## Features
- **Automated Daily Reports**: Scheduled for Monday-Friday at 11 AM IST (5:30 AM UTC)
- **AI-Powered Analysis**: Leverages Claude 3 Sonnet for intelligent finding summarization
- **Comprehensive Reporting**: 
  - HTML formatted email summary
  - Plain text alternative
  - Detailed CSV attachment
- **Finding Classification**: Organizes findings by severity (CRITICAL, HIGH, MEDIUM)
- **Multi-Account Support**: Aggregates findings across AWS accounts

## Architecture Components
- **AWS Lambda**: Python 3.12 runtime for findings processing
- **Amazon EventBridge**: Scheduled trigger for the Lambda function
- **Amazon Bedrock**: AI-powered analysis using Claude 3 Sonnet
- **Amazon SES**: Email delivery service
- **AWS SecurityHub**: Security findings source

## Prerequisites
1. **AWS Account Access**
   - SecurityHub enabled
   - SES in production mode
   - Bedrock access configured
   - Appropriate IAM permissions

2. **Email Configuration**
   - Verified SES sender email
   - Verified SES recipient email

## Deployment Instructions

### Using AWS Console
1. **Access CloudFormation**
   - Log into AWS Console
   - Navigate to CloudFormation service

2. **Create Stack**
   - Click "Create stack" (with new resources)
   - Choose "Upload a template file"
   - Upload the provided template.yml

3. **Configure Stack**
   - Stack name: `security-hub-summarizer` (or preferred name)
   - Parameters:
     - SenderEmail: Verified SES sender address
     - RecipientEmail: Verified SES recipient address
     - FindingsHours: Lookback period (default: 24)

4. **Review and Create**
   - Review configuration
   - Acknowledge IAM resource creation
   - Click "Create stack"

### Using AWS CLI
```bash
aws cloudformation create-stack \
  --stack-name security-hub-summarizer \
  --template-body file://template.yml \
  --capabilities CAPABILITY_IAM \
  --parameters \
    ParameterKey=SenderEmail,ParameterValue=sender@example.com \
    ParameterKey=RecipientEmail,ParameterValue=recipient@example.com \
    ParameterKey=FindingsHours,ParameterValue=24
```

## Email Report Format

### Summary Table
```
| Account ID | Critical | High | Medium | Total |
|------------|----------|------|--------|-------|
| 123456789  |    5     |  10  |   15   |  30   |
| Total      |    5     |  10  |   15   |  30   |
```

### Report Components
1. Statistical Summary
2. AI-Generated Analysis
3. Detailed CSV Attachment
4. Bedrock Analysis Disclaimer


## Disclaimer
The security findings summary is AI-generated using Amazon Bedrock based on SecurityHub findings. Review all findings and take appropriate action based on your security requirements.

---

**Note**: Replace placeholder email addresses with actual verified SES addresses before deployment.

For questions or issues, please open a GitHub issue or contact your AWS account team.

Citations:
[1] https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/31009598/8d5c55a2-5f27-4ac0-9ba9-fa4cb2421f94/Securityhub_24hours_bedrock.yml
[2] https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/31009598/dc24e007-1e35-4f0f-a7ef-4939c3a1e9b4/securityhub_24hours_bedrock.py
