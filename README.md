# **AWS Serverless Website Monitoring Application.**

#


**Introduction:**

The AWS Serverless Monitoring Project aims to establish a scalable and efficient system for monitoring the status of a website using serverless architecture provided by Amazon Web Services (AWS). This documentation outlines the setup, configuration, and utilization of AWS services like AWS Lambda, Amazon CloudWatch, Amazon S3, and AWS CloudTrail to automate the monitoring process.

**Project Overview:**

In a rapidly evolving digital landscape, ensuring the availability and performance of web services is crucial for businesses and organizations. This project addresses the need for a robust monitoring system capable of periodically checking the status of a website and providing insights into its health through automated processes.

**Purpose:**

The primary objective of this project is to demonstrate a serverless monitoring architecture that can efficiently monitor the status of a specified website, retrieve HTTP response codes, and store relevant logs for analysis and future reference. Leveraging AWS services allows for a cost-effective, scalable, and easily maintainable solution without the overhead of managing infrastructure.

**Key Components:**

The key components of this serverless monitoring setup include:

**AWS Lambda** : Executes code to perform HTTP requests and retrieve website status codes.

**Amazon CloudWatch** : Manages scheduled triggers to execute Lambda functions and stores logs.

**Amazon S3** : Stores CloudTrail logs and potentially other data if required.

**AWS CloudTrail** : Captures API calls for monitoring and auditing purposes.

**AWS Simple Notification Service (SNS):** Sends notifications/alerts based on defined triggers or events.

## CloudFormation Template Explanation:

AWSTemplateFormatVersion: '2023-11-17'

Resources:

WebsiteStatusLambdaFunction:

Type: AWS::Lambda::Function

Properties:

Code:

ZipFile: |

import requests

def lambda\_handler(event, context):

url = event['https://www.linkedin.com/feed/']

response = requests.get(url)

status\_code = response.status\_code

return {

'statusCode': status\_code

}

Handler: index.lambda\_handler

Role: !GetAtt WebsiteStatusLambdaRole.Arn

Runtime: python3.8

WebsiteStatusLambdaRole:

Type: AWS::IAM::Role

Properties:

AssumeRolePolicyDocument:

Version: '2023-11-17'

Statement:

- Effect: Allow

Principal:

Service: lambda.amazonaws.com

Action: sts:AssumeRole

Policies:

- PolicyName: WebsiteStatusLambdaPolicy

PolicyDocument:

Version: '2023-11-17'

Statement:

- Effect: Allow

Action:

- logs:CreateLogGroup

- logs:CreateLogStream

- logs:PutLogEvents

Resource: arn:aws:logs:\*:\*:\*

WebsiteStatusSchedule:

Type: AWS::Events::Rule

Properties:

ScheduleExpression: rate(5 minutes)

State: ENABLED

Targets:

- Arn: !GetAtt WebsiteStatusLambdaFunction.Arn

Id: "TargetFunctionV1"

WebsiteStatusLogsGroup:

Type: AWS::Logs::LogGroup

Properties:

LogGroupName: /aws/lambda/website-status

CloudTrail:

Type: AWS::CloudTrail::Trail

Properties:

S3BucketName: Website

IsLogging: true

EnableLogFileValidation: true

IncludeGlobalServiceEvents: true

Outputs:

WebsiteStatusFunctionArn:

Value: !GetAtt WebsiteStatusLambdaFunction.Arn

**Cost Estimation:**

**AWS Lambda:** Lambda pricing depends on the number of requests and the duration of the execution. The free tier includes 1 million free requests per month and 400,000 GB-seconds of compute time per month. Beyond the free tier, Lambda pricing is based on the number of requests and the duration of execution.

**Amazon CloudWatch:** CloudWatch offers free tier usage for basic monitoring and logging. Beyond the free tier, costs can accrue based on the volume of logs stored, custom metrics, alarms, etc.

**Amazon S3:** S3 pricing is based on storage used, data transfer out, requests made, and any additional features utilized (like encryption, cross-region replication, etc.). S3 also offers a free tier with limited usage.

**AWS CloudTrail:** CloudTrail is priced based on the number of events recorded and the volume of data in those events. There is a free tier available, but beyond that, costs can increase with the number of events and data stored.

**Designer View:**

![template1-designer](https://github.com/ramutc/serverlessmonitoring/assets/151390614/996c158d-8585-448d-99cb-a4bf9340ea88)
