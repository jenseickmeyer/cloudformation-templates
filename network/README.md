# Network

## VPC

The template [vpc.yaml](vpc.yaml) defines a _Virtual Private Cloud_ (VPC) spread across 3 _Availability Zones_ (AZs). In each AZ a public and private subnet is created. By default each subnet provides 256 IPs (`/16`). Routing from the private subnets to the public Internet is established via a NAT Gateway in one of public subnets.

A set of _Network ACLs_ is configured to provide an additional layer of security to the network:

* Only HTTP/HTTPS connections are allowed from the Internet to the public subnets
* SSH connections to the public subnets are only allowed from a configurable source address
* Only HTTP and SSH connections are allowed from the public subnets to the private subnets
* Only HTTP/HTTPS connections are allowed to the Internet

Additionally, two _Security Groups_ are configured:

* Bastion Host SG: Allows SSH connections from the Internet
* Internal SSH SG: Allows SSH connections from the Bastion Host

### Setup

To create a VPC based on this template the following AWS CLI command can be used:

```
aws cloudformation create-stack --stack-name my-vpc --template-body file://vpc.yaml
```

To create a new VPC which allows SSH access only to your machine use the following commands:

```
my_ip=$(curl -s ipecho.net/plain)

aws cloudformation create-stack --stack-name my-vpc --template-body file://vpc.yaml --parameters ParameterKey=SourceCidrIp,ParameterValue=$my_ip/32
```

### Parameters

The template does not require mandatory parameters. Reasonable default values are assigned. However, the following things can be configured:

* The CIDR block associated with the VPC
* The CIDR blocks for the public and private subnets. These must be coming from the VPC CIDR block
* The kind of NAT setup (none, single)
* The CIDR block from which SSH connections are allowed

### Outputs

The stack exports lots of outputs which can be imported by other stacks, for example

* The VPC ID
* The IDs of the public and private subnets
* The IDs of the route tables
* The IDs of the security groups
