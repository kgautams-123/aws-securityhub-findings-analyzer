# AWS SecurityHub Findings Summarizer with AI-Powered Analysis

## Overview

This solution automates the collection and analysis of AWS SecurityHub findings using Amazon Bedrock's Claude 3 Sonnet model. It provides daily email reports with AI-generated insights, helping security teams quickly understand their security posture across AWS accounts.

## Key Features

- **Automated Daily Reports**: Runs Monday-Friday at 11 AM IST (5:30 AM UTC)
- **AI-Powered Analysis**: Uses Claude 3 Sonnet for intelligent finding summarization
- **Multi-Account Support**: Aggregates findings across AWS accounts
- **Severity-Based Classification**: Categorizes findings by CRITICAL, HIGH, and MEDIUM severity
- **Detailed CSV Reports**: Includes comprehensive finding details for further analysis
- **HTML/Text Email Format**: Provides both HTML and plain text email formats

## Architecture Details

![Architecture Diagram]

### Components
1. **AWS Lambda**
   - Runtime: Python 3.12
   - Memory: 256MB
   - Timeout: 300 seconds
   - Environment Variables:
     - SENDER_EMAIL
     - RECIPIENT_EMAIL
     - BEDROCK_MODEL_ID
     - FINDINGS_HOURS

2. **Amazon EventBridge**
   - Schedule: cron(30 5 ? * MON-FRI *)
   - Triggers Lambda function daily

3. **Amazon Bedrock**
   - Model: Claude 3 Sonnet
   - Used for AI analysis of findings

4. **Amazon SES**
   - Handles email delivery
   - Supports attachments and HTML formatting

5. **AWS SecurityHub**
   - Source of security findings
   - Filtered for FAILED compliance status

## Prerequisites

### AWS Services
1. **SecurityHub**
   - Must be enabled in all monitored regions
   - Security standards enabled as needed

2. **Amazon SES**
   - Production access required
   - Verified sender and recipient email addresses
   - Appropriate sending limits

3. **Amazon Bedrock**
   - Access to Claude 3 Sonnet model
   - Appropriate model invocation limits

4. **IAM Permissions**
   - SecurityHub read access
   - SES send email permissions
   - Bedrock model invocation rights
   - CloudWatch Logs access

## Detailed Installation Steps

1. **Prepare Environment**
   ```bash
   git clone <repository-url>
   cd security-hub-summarizer
   ```

2. **Verify SES Email Addresses**
   ```bash
   aws ses verify-email-identity --email-address sender@example.com
   aws ses verify-email-identity --email-address recipient@example.com
   ```

3. **Deploy CloudFormation Stack**
   ```bash
   aws cloudformation create-stack \
     --stack-name security-hub-summarizer \
     --template-body file://aws-securityhub-findings-analyzer.yml \
     --capabilities CAPABILITY_IAM \
     --parameters \
       ParameterKey=SenderEmail,ParameterValue=sender@example.com \
       ParameterKey=RecipientEmail,ParameterValue=recipient@example.com \
       ParameterKey=FindingsHours,ParameterValue=24
   ```

## Configuration Details

### Lambda Environment Variables
```yaml
SENDER_EMAIL: Email address for sending reports
RECIPIENT_EMAIL: Email address receiving reports
BEDROCK_MODEL_ID: anthropic.claude-3-sonnet-20240229-v1:0
FINDINGS_HOURS: Number of hours to look back (default: 24)
```

### SecurityHub Filters
```python
filters = {
    'RecordState': [{'Value': 'ACTIVE', 'Comparison': 'EQUALS'}],
    'ComplianceStatus': [{'Value': 'FAILED', 'Comparison': 'EQUALS'}],
    'SeverityLabel': [
        {'Value': 'CRITICAL', 'Comparison': 'EQUALS'},
        {'Value': 'HIGH', 'Comparison': 'EQUALS'},
        {'Value': 'MEDIUM', 'Comparison': 'EQUALS'}
    ]
}
```

## Email Report Format

### Summary Table
```
| Account ID | Critical | High | Medium | Total |
|------------|----------|------|--------|-------|
| 123456789  |    5     |  10  |   15   |  30   |
| Total      |    5     |  10  |   15   |  30   |
```

### Report Sections
1. Statistical Summary
2. AI-Generated Analysis
3. Detailed CSV Attachment
4. Disclaimer

---
**Note**: Customize email templates, finding filters, and configuration based on your organization's requirements.
