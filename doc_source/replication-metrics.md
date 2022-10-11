# Monitoring progress with replication metrics and Amazon S3 event notifications<a name="replication-metrics"></a>

S3 Replication metrics provide detailed metrics for the replication rules in your replication configuration\. With replication metrics, you can monitor minute\-by\-minute progress of replication by tracking bytes pending, operations pending, and replication latency\. Additionally, you can set up Amazon S3 Event Notifications to receive replication failure events to assist in troubleshooting any configuration issues\.

When enabled, S3 Replication metrics publish the following metrics to Amazon CloudWatch:

**Bytes Pending Replication**—The total number of bytes of objects pending replication for a given replication rule\.

**Replication Latency**—The maximum number of seconds by which the replication destination buckets are behind the source bucket for a given replication rule\.

**Operations Pending Replication**—The number of operations pending replication for a given replication rule\. Operations include objects, delete markers, tags, ACLs, and Object Lock operations\.

**Note**  
S3 Replication metrics are billed at the same rate as Amazon CloudWatch custom metrics\. For information, see [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)\.

S3 Replication metrics are turned on automatically when you enable S3 Replication Time Control \(S3 RTC\)\. S3 RTC includes other features such as a service level agreement \(SLA\) and notifications for missed thresholds\. For more information, see [Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)](replication-time-control.md)\.

**Topics**
+ [Enabling S3 Replication metrics](#enabling-replication-metrics)
+ [Receiving replication failure events with Amazon S3 event notifications](#replication-metrics-events)
+ [Viewing replication metrics using the Amazon S3 console](viewing-replication-metrics.md)
+ [Amazon S3 replication failure reasons](replication-failure-codes.md)

## Enabling S3 Replication metrics<a name="enabling-replication-metrics"></a>

You can start using S3 Replication metrics with a new or existing replication rule\. You can choose to apply your replication rule to an entire S3 bucket, or to Amazon S3 objects with a specific prefix or tag\.

This topic provides instructions for enabling S3 Replication metrics in your replication configuration when buckets are owned by the same or different AWS accounts\.

To enable replication metrics using the AWS Command Line Interface \(AWS CLI\), you must add a replication configuration to the source bucket with `Metrics` enabled\. In this example configuration, objects under the prefix *Tax* are replicated to the destination bucket *DOC\-EXAMPLE\-BUCKET*, and metrics are generated for those objects\.

```
{
    "Rules": [
        {
            "Status": "Enabled",
            "Filter": {
                "Prefix": "Tax"
            },
            "Destination": {
                "Bucket": "arn:aws:s3:::DOC-EXAMPLE-BUCKET",
                "Metrics": {
                    "Status": "Enabled"
                }
            },
            "Priority": 1
        }
    ],
    "Role": "IAM-Role-ARN"
}
```

For full instructions on creating replication rules, see [Configuring replication for source and destination buckets owned by the same account](replication-walkthrough1.md)\.

For more information about viewing replication metrics in the S3 console, see [Viewing replication metrics using the Amazon S3 console](viewing-replication-metrics.md)\.

## Receiving replication failure events with Amazon S3 event notifications<a name="replication-metrics-events"></a>

Amazon S3 event notifications can notify you in instances when objects do not replicate to their destination AWS Region\. Amazon S3 events are available through Amazon Simple Queue Service \(Amazon SQS\), Amazon Simple Notification Service \(Amazon SNS\), or AWS Lambda\. For more information, see [Configuring Amazon S3 event notifications](https://docs.aws.amazon.com/AmazonS3/latest/dev/NotificationHowTo.html)\.

For a list of failure codes captured by Amazon S3 event notifications, see [Amazon S3 replication failure reasons](replication-failure-codes.md)\.