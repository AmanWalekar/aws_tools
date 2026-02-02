# Running get_top_sqs_queue.exe

Quick guide for running the pre-built Windows executable.

## Prerequisites

- Windows operating system
- AWS credentials configured (see [Authentication](#authentication) below)
- IAM permissions: `sqs:ListQueues` and `sqs:GetQueueAttributes`

## Quick Start

1. **Configure AWS credentials** (if not already done):
   ```powershell
   aws configure
   ```

2. **Run the executable**:
   ```powershell
   .\get_top_sqs_queue.exe
   ```

## Command Line Options

```powershell
# Use a specific AWS profile
.\get_top_sqs_queue.exe --profile production

# Specify AWS region
.\get_top_sqs_queue.exe --region us-west-2

# Combine options
.\get_top_sqs_queue.exe --profile production --region us-east-1

# Show top 20 queues instead of default 10
.\get_top_sqs_queue.exe --top 20


```

### Available Flags

- `--profile <name>`: AWS profile name to use (overrides AWS_PROFILE environment variable)
- `--region <region>`: AWS region to query (overrides AWS_REGION environment variable)
- `--top <number>`: Number of top queues to display (default: 10)
- `--workers <number>`: Number of concurrent workers (default: 50)
- `--timeout <duration>`: Overall timeout (default: 10m)

## Authentication

The executable uses the AWS SDK credential chain. It will automatically check for credentials in this order:

1. **Environment Variables** (highest priority)
   ```powershell
   $env:AWS_ACCESS_KEY_ID="your-access-key"
   $env:AWS_SECRET_ACCESS_KEY="your-secret-key"
   $env:AWS_REGION="us-east-1"
   ```

2. **AWS Profile** (via `--profile` flag or `AWS_PROFILE` environment variable)
   ```powershell
   $env:AWS_PROFILE="production"
   .\get_top_sqs_queue.exe
   ```

3. **AWS Credentials File** (`%USERPROFILE%\.aws\credentials`)
   ```ini
   [default]
   aws_access_key_id = YOUR_ACCESS_KEY
   aws_secret_access_key = YOUR_SECRET_KEY
   ```

4. **AWS Config File** (`%USERPROFILE%\.aws\config`)
   ```ini
   [profile production]
   region = us-east-1
   ```

## Output

The executable will:
1. List all SQS queues in the specified region
2. Fetch message counts for each queue (with progress updates)
3. Display the top 10 queues (or number specified with `--top`) in a formatted table

Example output:
```
Fetching list of queues...
Found 2500 queues. Fetching message counts...
Progress: 50/2500 queues processed...
Progress: 100/2500 queues processed...
...
Processed 2500 queues (ok=2495, failed=5).

=== Top SQS Queues by Message Count (Table) ===
Rank    Queue Name                    Messages    Not Visible    Created (UTC)              Last Modified (UTC)
1       my-queue-name                 12345       0              2024-01-15T10:30:00Z      2024-01-20T14:22:00Z
2       another-queue                 8900        5              2024-01-10T08:15:00Z      2024-01-19T09:10:00Z
...
```

## Troubleshooting

**Error: "No AWS credentials provider configured"**
- Run `aws configure` to set up credentials
- Or use `--profile <name>` flag
- Or set `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables

**Error: "AWS authentication failed"**
- Verify credentials: `aws sts get-caller-identity`
- Check that your credentials file is at `%USERPROFILE%\.aws\credentials`
- Ensure IAM permissions include `sqs:ListQueues` and `sqs:GetQueueAttributes`

**No queues found**
- Verify you're querying the correct region: `.\get_top_sqs_queue.exe --region us-east-1`
- Check that your AWS account has SQS queues in that region

**Timeout errors**
- Increase timeout: `.\get_top_sqs_queue.exe --timeout 20m`
- Reduce workers if hitting rate limits: `.\get_top_sqs_queue.exe --workers 25`
