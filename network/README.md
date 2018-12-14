# Network

## VPC

The template [vpc.yaml](vpc.yaml) defines a _Virtual Private Cloud_ (VPC) spread across 3 _Availability Zones_ (AZs). In each AZ a public and private subnet is created. By default each subnet provides 256 IPs (`/16`). Routing from the private subnets to the public Internet is established via a NAT Gateway in one of public subnets.

To create a VPC based on this template the following AWS CLI command can be used:

```
aws cloudformation create-stack --stack-name my-vpc --template-body file://vpc.yaml --parameters ParameterKey=VPCName,ParameterValue=my-vpc
```
