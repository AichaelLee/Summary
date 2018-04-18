## How do I monitor the cross-region replication of my Amazon S3 objects?
Amazon Simple Storage Service (Amazon S3) offers cross-region replication, a bucket-level feature that enables automatic, asynchronous copying of objects across buckets in different AWS Regions. Currently, AWS customers can retrieve the replication status of their objects manually or use an Amazon S3 inventory to generate metrics on a daily or weekly basis. To help customers more proactively monitor the replication status of their Amazon S3 objects, AWS offers the Cross-Region Replication Monitor (CRR Monitor) solution.

This solution automatically checks the replication status of Amazon S3 objects across different AWS Regions, providing near real-time metrics as well as failure notifications to help customers proactively identify failures and troubleshoot problems. This feature helps companies minimize latency in accessing objects in different geographic regions, meet compliance requirements, and for operational purposes.
The following sections describe key considerations and recommendations for implementing CRR Monitor and assumes basic knowledge of Amazon S3 cross-region replication.
General Best Practices
Monitoring the replication status of your Amazon S3 objects can help you to identify failures in near real-time and troubleshoot them immediately. With this in mind, consider the following S3 cross-region replication best practices:

Factors such as object size and network traffic can occasionally cause replication delays, so measure the speed and latency of your replication method to make sure it satisfies your company’s recovery point objective (RPO) requirements.
Monitor both source and destination locations (S3 buckets) to confirm that all objects are replicated successfully.
Compare your object hash values to ensure the replicated object data was not modified.
For efficiency, use a solution that implements thresholds and alert only on failure exceptions, rather than one that examines the replication status of millions of objects.
AWS Solution
AWS offers a simple solution that automatically monitors the replication status of your Amazon S3 objects. The diagram below presents the CRR monitor architecture you can deploy in minutes using the solution's implementation guide and accompanying AWS CloudFormation template.

 crr-monitor-architecture-overview
When an object is added to the source S3 bucket, AWS CloudTrail triggers an Amazon CloudWatch event that delivers the status information to an Amazon SNS topic.
Amazon SNS sends the data to an Amazon SQS queue.
When a replicated object is added to the destination bucket, it triggers an Amazon CloudWatch event and Amazon SNS, and status information is sent back to the Amazon SQS queue in the monitor region.
AWS Lambda functions process the data in the queue to verify that the object was replicated successfully. 
Status data is stored in Amazon DynamoDB, and the data is sent through Amazon Kinesis Firehose to an S3 bucket.
Deploy Solution
Implementation Guide
Download PDF Implementation Guide

What you'll accomplish:

Deploy CRR Monitor  using AWS CloudFormation. The CloudFormation template will automatically launch and configure the components necessary to proactively monitor your data replication across multiple geographical regions.
Automatically receive email notification if your data replication fails. CRR Monitor publishes a message to an Amazon SNS topic which is sent to an email address you specify.

What you'll need before starting:
An AWS account: You will need an AWS account to begin provisioning resources. Sign up for AWS.

Skill level: This solution is intended for IT infrastructure and networking professionals who have practical experience configuring Amazon S3 cross-region replication on the AWS Cloud.

Cross-Region Replication: You will need cross-region replication set up in your account to deploy this solution.
Solution FAQs
Q: Can I deploy the Cross-Region Replication Monitor in any AWS Region?

Customers can deploy the CRR Monitor in any AWS Region that supports AWS Lambda and Amazon Kinesis Firehose.

Q: What happens if object replication fails?

If the object replication fails, an AWS Lambda function logs the event in Amazon CloudWatch, triggering a CloudWatch alarm that sends an Amazon Simple Notification Service (Amazon SNS) notification to the subscriber.

Q: Do I have to use Amazon Kinesis Firehose to archive my data?  

The solution uses a Firehose delivery stream as part of an optional solution feature to archive replication data in Amazon S3. You can choose to disable this feature (due to regional availability or other reasons) during initial configuration of the AWS CloudFormation template. If you disable data archiving, you can use the Amazon S3 inventory feature to get a daily or weekly replication status for your objects.  

[link](https://d1.awsstatic-china.com/aws-answers/answers-images/crr-monitor-architecture-overview.ffc9cffc90e08ffcb502c3b4890b161da7dabbd6.png)