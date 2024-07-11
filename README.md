## Remediation of violated file in CASB IaaS AWS Data Scanning 

## Purpose
Remediation of violated file from the source bucket. The lambda function is responsible for moving the violated file to the destination bucket and deleting it from the source bucket.

## Permissions required
1. To use this Remediation Lambda function, please assign required privileges to the cloudsoc role. Please assign "SNS:Publish“ to proceed.
2. Permission for LambdaExecutionRole:
    Note: When you will create the lambda function it will create an Execution role, above permissions are for that Execution Role as it will need to move the file to destination bucket and delet it from source bucket.
    Ref: https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "logs:CreateLogGroup",
                "Resource": "arn:aws:logs:<region>>:<account>>:*"
            },
            {
                "Effect": "Allow",
                "Action": [
                    "logs:CreateLogStream",
                    "logs:PutLogEvents"
                ],
                "Resource": [
                    "arn:aws:logs:<region>>:<account>:log-group:/aws/lambda/AWSRemediationLambda:*"
                ]
            },
            {
                "Sid": "putObject",
                "Effect": "Allow",
                "Action": [
                    "s3:PutObject"
                ],
                "Resource": [
                    "arn:aws:s3:::<sourcebucket>/*",
                    "arn:aws:s3:::<destinationbucket>/*"
                ]
            },
            {
                "Sid": "getObject",
                "Effect": "Allow",
                "Action": [
                    "s3:DeleteObject",
                    "s3:ListBucket",
                    "s3:GetObject"
                ],
                "Resource": [
                    "*"
                ]
            }
        ]
    }


## Pre-requisites
- Enable AWS IaaS Polling [It is a flag based feature and you need to reach out to the CASB team to get it enabled for your tenant]
        Create SNS where CloudSoc will publish alert message in case of file scanning violation.
        Edit CASB IaaS AWS Connection and enable Publish Alerts
        Update SNS ARN in CloudSoc Connection --> Publish ALert.
- Polling Enable for Bucket where remediation has to be done.
        GoTo IaaS Storage Select the bucket, Right Click and Enable Polling.
- Create S3 Bucket in AWS where you want to move the violated files.

## Execution Steps
1. Download the git files
2. Extract Lambda-function.zip 
3. Open file s3.json
4. Update the destination bucket name where you want to move violated files.
5. Add the lambda function in your AWS Environment.
    Ref: https://docs.aws.amazon.com/lambda/latest/dg/python-package.html
6. Subscribe the Lambda Function with SNS, created in pre-requisite step and updated in CASB AWS Connection to Publish Alerts.
    Ref: https://docs.aws.amazon.com/sns/latest/dg/lambda-console.html


## Resources Required
1. SNS - To Publish Alert
2. S3 Bucket - To move violated files.
3. Lambda Function to move and delete violated file.

## Note:
1. The Remediation is only supported for AWS Single Account.
2. File will be moved to the bucket but for now the directory hierarchy of source bucket is not maintained.
