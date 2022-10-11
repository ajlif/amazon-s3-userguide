# Creating an S3 on Outposts bucket<a name="S3OutpostsCreateBucket"></a>

With Amazon S3 on Outposts, you can create S3 buckets on your AWS Outposts and easily store and retrieve objects on premises for applications that require local data access, local data processing, and data residency\. S3 on Outposts provides a new storage class, S3 Outposts \(`OUTPOSTS`\), which uses the Amazon S3 APIs, and is designed to store data durably and redundantly across multiple devices and servers on your AWS Outposts\. You communicate with your Outpost bucket by using an access point and endpoint connection over a virtual private cloud \(VPC\)\. You can use the same APIs and features on Outpost buckets as you do on Amazon S3 buckets, including access policies, encryption, and tagging\. You can use S3 on Outposts through the AWS Management Console, AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST API\. For more information, see [What is Amazon S3 on Outposts?](S3onOutposts.md)

**Note**  
The AWS account that creates the bucket owns it and is the only one that can commit actions to it\. Buckets have configuration properties, such as Outpost, tag, default encryption, and access point settings\. The access point settings include the virtual private cloud \(VPC\), the access point policy for accessing the objects in the bucket, and other metadata\. For more information, see [S3 on Outposts specifications](S3OnOutpostsRestrictionsLimitations.md#S3OnOutpostsSpecifications)\.

The following examples show you how to create an S3 on Outposts bucket by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), and AWS SDK for Java\.

## Using the S3 console<a name="S3OutpostsCreateBucketConsole"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts buckets**\.

1. Choose **Create Outposts bucket**\.

1. For **Bucket name**, enter a Domain Name System \(DNS\)\-compliant name for your bucket\.

   The bucket name must:
   + Be unique within the AWS account, the Outpost, and the AWS Region that the Outpost is homed to\.
   + Be 3–63 characters long\.
   + Not contain uppercase characters\.
   + Start with a lowercase letter or number\.

     After you create the bucket, you can't change its name\. For information about naming buckets, see [Bucket naming rules](bucketnamingrules.md)\.
**Important**  
Avoid including sensitive information such as account numbers in the bucket name\. The bucket name is visible in the URLs that point to the objects in the bucket\.

1. For **Outpost**, choose the Outpost where you want the bucket to reside\. 

1. Under **Bucket Versioning**, set the S3 Versioning state for your S3 on Outposts bucket to one of the following options:
   + **Disable** \(default\) – The bucket remains unversioned\.
   + **Enable** – Enables S3 Versioning for the objects in the bucket\. All objects added to the bucket receive a unique version ID\.

   For more information about S3 Versioning, see [Managing S3 Versioning for your S3 on Outposts bucket](S3OutpostsManagingVersioning.md)\.

1. \(Optional\) Add any **optional tags** that you would like to associate with the Outposts bucket\. You can use tags to track criteria for individual projects or groups of projects, or to label your buckets by using cost\-allocation tags\.

   By default, all objects stored in your Outposts bucket are stored by using server\-side encryption with Amazon S3 managed encryption keys \(SSE\-S3\)\. You can also explicitly choose to store objects by using server\-side encryption with customer\-provided encryption keys \(SSE\-C\)\. To change the encryption type, you must use the REST API, AWS Command Line Interface \(AWS CLI\), or AWS SDKs\.

1. In the **Outposts access point settings** section, enter the access point name\.

   S3 on Outposts access points simplify managing data access at scale for shared datasets in S3 on Outposts\. Access points are named network endpoints that are attached to Outposts buckets that you can use to perform S3 object operations\. For more information, see [Access points](S3OutpostsWorkingBuckets.md#S3OutpostsAP)\. 

   Access point names must be unique within the account for this Region and Outpost, and comply with the [Access points restrictions and limitations](access-points-restrictions-limitations.md)\.

1. Choose the **VPC** for this Amazon S3 on Outposts access point\. 

   If you don't have a VPC, choose **Create VPC**\. For more information, see [Creating access points restricted to a virtual private cloud](access-points-vpc.md)\.

   A virtual private cloud \(VPC\) enables you to launch AWS resources into a virtual network that you define\. This virtual network closely resembles a traditional network that you would operate in your own data center, with the benefits of using the scalable infrastructure of AWS\. 

1. \(Optional for an existing VPC\) Choose an **Endpoint subnet** for your endpoint\. 

   A subnet is a range of IP addresses in your VPC\. If you don't have the subnet that you want, choose **Create subnet**\. For more information, see [Networking for S3 on Outposts](S3OutpostsNetworking.md)\. 

1. \(Optional for an existing VPC\) Choose an **Endpoint security group** for your endpoint\.

   A [security group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html) acts as a virtual firewall to control inbound and outbound traffic\.

1. \(Optional for an existing VPC\) Choose the **Endpoint access type**:
   + **Private** – To be used with the VPC\.
   + **Customer owned IP** – To be used with a customer\-owned IP address pool \(CoIP pool\) from within your on\-premises network\.

1. \(Optional\) Specify the **Outpost access point policy**\. The console automatically displays the **Amazon Resource Name \(ARN\)** for the access point, which you can use in the policy\.

1. Choose **Create Outposts bucket**\.
**Note**  
It can take up to 5 minutes for your Outposts endpoint to be created and your bucket to be ready to use\. To configure additional bucket settings, choose **View details**\.

## Using the AWS CLI<a name="S3OutpostsCreateBucketCLI"></a>

**Example**  
The following example creates an S3 on Outposts bucket \(`s3-outposts:CreateBucket`\) by using the AWS CLI\. To run this command, replace the `user input placeholders` with your own information\.  

```
aws s3control create-bucket --bucket example-outpost-bucket --outpost-id op-01ac5d28a6a232904
```

## Using the AWS SDK for Java<a name="S3OutpostsCreateBucketJava"></a>

**Example**  
The following example creates an S3 on Outposts bucket \(`s3-outposts:CreateBucket`\) by using the SDK for Java\.   

```
import com.amazonaws.services.s3control.model.*;

public String createBucket(String bucketName) {

    CreateBucketRequest reqCreateBucket = new CreateBucketRequest()
            .withBucket(bucketName)
            .withOutpostId(OutpostId)
            .withCreateBucketConfiguration(new CreateBucketConfiguration());

    CreateBucketResult respCreateBucket = s3ControlClient.createBucket(reqCreateBucket);
    System.out.printf("CreateBucket Response: %s%n", respCreateBucket.toString());

    return respCreateBucket.getBucketArn();

}
```