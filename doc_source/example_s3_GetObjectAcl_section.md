# Get the ACL of an Amazon S3 object using an AWS SDK<a name="example_s3_GetObjectAcl_section"></a>

The following code examples show how to get the access control list \(ACL\) of an S3 object\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ C\+\+ ]

**SDK for C\+\+**  
 To learn how to set up and run this example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/cpp/example_code/s3#code-examples)\. 
  

```
bool AwsDoc::S3::GetBucketAcl(const Aws::String &bucketName,
                              const Aws::Client::ClientConfiguration &clientConfig) {
    Aws::S3::S3Client s3_client(clientConfig);

    Aws::S3::Model::GetBucketAclRequest request;
    request.SetBucket(bucketName);

    Aws::S3::Model::GetBucketAclOutcome outcome =
            s3_client.GetBucketAcl(request);

    if (!outcome.IsSuccess()) {
        const Aws::S3::S3Error &err = outcome.GetError();
        std::cerr << "Error: GetBucketAcl: "
                  << err.GetExceptionName() << ": " << err.GetMessage() << std::endl;
    }
    else {
        Aws::Vector<Aws::S3::Model::Grant> grants =
                outcome.GetResult().GetGrants();

        for (auto it = grants.begin(); it != grants.end(); it++) {
            Aws::S3::Model::Grant grant = *it;
            Aws::S3::Model::Grantee grantee = grant.GetGrantee();

            std::cout << "For bucket " << bucketName << ": "
                      << std::endl << std::endl;

            if (grantee.TypeHasBeenSet()) {
                std::cout << "Type:          "
                          << GetGranteeTypeString(grantee.GetType()) << std::endl;
            }

            if (grantee.DisplayNameHasBeenSet()) {
                std::cout << "Display name:  "
                          << grantee.GetDisplayName() << std::endl;
            }

            if (grantee.EmailAddressHasBeenSet()) {
                std::cout << "Email address: "
                          << grantee.GetEmailAddress() << std::endl;
            }

            if (grantee.IDHasBeenSet()) {
                std::cout << "ID:            "
                          << grantee.GetID() << std::endl;
            }

            if (grantee.URIHasBeenSet()) {
                std::cout << "URI:           "
                          << grantee.GetURI() << std::endl;
            }

            std::cout << "Permission:    " <<
                      GetPermissionString(grant.GetPermission()) <<
                      std::endl << std::endl;
        }
    }

    return outcome.IsSuccess();
}

//! Routine which converts a built-in type enumeration to a human-readable string.
/*!
 \sa GetGranteeTypeString()
 \param type Type enumeration.
*/

Aws::String GetGranteeTypeString(const Aws::S3::Model::Type &type) {
    switch (type) {
        case Aws::S3::Model::Type::AmazonCustomerByEmail:
            return "Email address of an AWS account";
        case Aws::S3::Model::Type::CanonicalUser:
            return "Canonical user ID of an AWS account";
        case Aws::S3::Model::Type::Group:
            return "Predefined Amazon S3 group";
        case Aws::S3::Model::Type::NOT_SET:
            return "Not set";
        default:
            return "Type unknown";
    }
}

//! Routine which converts a built-in type enumeration to a human-readable string.
/*!
 \sa GetPermissionString()
 \param permission Permission enumeration.
*/

Aws::String GetPermissionString(const Aws::S3::Model::Permission &permission) {
    switch (permission) {
        case Aws::S3::Model::Permission::FULL_CONTROL:
            return "Can list objects in this bucket, create/overwrite/delete "
                   "objects in this bucket, and read/write this "
                   "bucket's permissions";
        case Aws::S3::Model::Permission::NOT_SET:
            return "Permission not set";
        case Aws::S3::Model::Permission::READ:
            return "Can list objects in this bucket";
        case Aws::S3::Model::Permission::READ_ACP:
            return "Can read this bucket's permissions";
        case Aws::S3::Model::Permission::WRITE:
            return "Can create, overwrite, and delete objects in this bucket";
        case Aws::S3::Model::Permission::WRITE_ACP:
            return "Can write this bucket's permissions";
        default:
            return "Permission unknown";
    }

    return "Permission unknown";
}
```
+  For API details, see [GetObjectAcl](https://docs.aws.amazon.com/goto/SdkForCpp/s3-2006-03-01/GetObjectAcl) in *AWS SDK for C\+\+ API Reference*\. 

------
#### [ Go ]

**SDK for Go V2**  
 To learn how to set up and run this example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/gov2/s3#code-examples)\. 
  

```
package main

import (
	"context"
	"flag"
	"fmt"

	"github.com/aws/aws-sdk-go-v2/config"
	"github.com/aws/aws-sdk-go-v2/service/s3"
)

// S3GetObjectAclAPI defines the interface for the GetObjectAcl function.
// We use this interface to test the function using a mocked service.
type S3GetObjectAclAPI interface {
	GetObjectAcl(ctx context.Context,
		params *s3.GetObjectAclInput,
		optFns ...func(*s3.Options)) (*s3.GetObjectAclOutput, error)
}

// FindObjectAcl gets the access control list (ACL) for an Amazon Simple Storage Service (Amazon S3) bucket object
// Inputs:
//     c is the context of the method call, which includes the AWS Region
//     api is the interface that defines the method call
//     input defines the input arguments to the service call.
// Output:
//     If success, a GetObjectAclOutput object containing the result of the service call and nil
//     Otherwise, nil and an error from the call to GetObjectAcl
func FindObjectAcl(c context.Context, api S3GetObjectAclAPI, input *s3.GetObjectAclInput) (*s3.GetObjectAclOutput, error) {
	return api.GetObjectAcl(c, input)
}

func main() {
	bucket := flag.String("b", "", "The bucket containing the object")
	objectName := flag.String("o", "", "The bucket object to get ACL from")
	flag.Parse()

	if *bucket == "" || *objectName == "" {
		fmt.Println("You must supply a bucket (-b BUCKET) and object (-o OBJECT)")
		return
	}

	cfg, err := config.LoadDefaultConfig(context.TODO())
	if err != nil {
		panic("configuration error, " + err.Error())
	}

	client := s3.NewFromConfig(cfg)

	input := &s3.GetObjectAclInput{
		Bucket: bucket,
		Key:    objectName,
	}

	result, err := FindObjectAcl(context.TODO(), client, input)
	if err != nil {
		fmt.Println("Got an error getting ACL for " + *objectName)
		return
	}

	fmt.Println("Owner:", *result.Owner.DisplayName)
	fmt.Println("")
	fmt.Println("Grants")

	for _, g := range result.Grants {
		fmt.Println("  Grantee:   ", *g.Grantee.DisplayName)
		fmt.Println("  Type:      ", string(g.Grantee.Type))
		fmt.Println("  Permission:", string(g.Permission))
		fmt.Println("")
	}
}
```
+  For API details, see [GetObjectAcl](https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/service/s3#Client.GetObjectAcl) in *AWS SDK for Go API Reference*\. 

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
 To learn how to set up and run this example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/s3#code-examples)\. 
  

```
suspend fun getBucketACL(objectKey: String, bucketName: String) {

    val request = GetObjectAclRequest {
        bucket = bucketName
        key = objectKey
    }

    S3Client { region = "us-east-1" }.use { s3 ->
        val response = s3.getObjectAcl(request)
        response.grants?.forEach { grant ->
            println("Grant permission is ${grant.permission}")
        }
    }
}
```
+  For API details, see [GetObjectAcl](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation) in *AWS SDK for Kotlin API reference*\. 

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 To learn how to set up and run this example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/s3/s3_basics#code-examples)\. 
  

```
class ObjectWrapper:
    """Encapsulates S3 object actions."""
    def __init__(self, s3_object):
        """
        :param s3_object: A Boto3 Object resource. This is a high-level resource in Boto3
                          that wraps object actions in a class-like structure.
        """
        self.object = s3_object
        self.key = self.object.key

    def get_acl(self):
        """
        Gets the ACL of the object.

        :return: The ACL of the object.
        """
        try:
            acl = self.object.Acl()
            logger.info(
                "Got ACL for object %s owned by %s.",
                self.object.key, acl.owner['DisplayName'])
        except ClientError:
            logger.exception("Couldn't get ACL for object %s.", self.object.key)
            raise
        else:
            return acl
```
+  For API details, see [GetObjectAcl](https://docs.aws.amazon.com/goto/boto3/s3-2006-03-01/GetObjectAcl) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](UsingAWSSDK.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.