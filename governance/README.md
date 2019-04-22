# Governance

## CloudTrail
The service CloudTrail logs every interaction of users - individual or technical ones - with AWS. It doesn't matter if the interaction has been via the AWS Console, the CLI or any SDK.

Any CloudTrail logs are stored in a S3 bucket. The logs of various accounts can be logged into one central S3 bucket. The trails for each account are configured to use this central S3 bucket. However, the bucket policy of this S3 bucket must be configured to allow any linked account to write data. The `AWSCloudTrailWrite` statement of the policy must add the following `Resource` item for each account:

```yaml
- Fn::Sub: "arn:aws:s3:::${TrailBucket}/AWSLogs/123456789012/*"
```

The CloudFormation stack based on the template [cloudtrail-master.yaml](cloudtrail-master.yaml) needs to be created in the AWS account which will collect all the CloudTrail logs. Access to this account needs to be restricted as much as possible to ensure that the logs can not be modified by attackers to cover their tracks.

For each additional account that should log their trails to this central bucket the bucket policy needs to be updated.

Additionally, a stack based on the template [cloudtrail-linked.yaml](cloudtrail-linked.yaml) has to be created in each linked account.


### Organization Trail
For an AWS Organization CloudTrail can be activated automatically for all linked accounts by creating an [organization trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/creating-trail-organization.html). Unfortunately, this is currently not supported by CloudFormation. Therefore, the CLI needs to be used to create it. The following commands can be used to get the ID of the organization, create the CloudFormation stack and updating the trail for the organization:

```bash
ORGANIZATION_ID=$(aws organizations describe-organization --query Organization.Id --output text)
aws cloudformation create-stack --stack-name organization-trail \
                                --template-body file://cloudtrail-organization.yaml \
                                --parameters ParameterKey=OrganizationId,ParameterValue=${ORGANIZATION_ID} ParameterKey=BucketName,ParameterValue=com.example.cloudtrail
aws cloudtrail update-trail --name organization-trail \
                            --is-organization-trail
```
