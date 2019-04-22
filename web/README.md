# Web

## Load Balancer

The template [web-load-balancer.yaml](web-load-balancer.yaml) creates an [Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html) which can be shared across multiple web stacks created via [web-target-group.yaml](web-target-group.yaml). The load balancer is configured to redirect all traffic to HTTPS.

To create a load balancer on this stack the following command can be used:

```
aws cloudformation create-stack --stack-name web-load-balancer \
                                --template-body file://web-load-balancer.yaml \
                                --parameters ParameterKey=NetworkStackName,ParameterValue=my-vpc \
                                             ParameterKey=DefaultCertificateArn,ParameterValue=arn:aws:acm:us-east-1:123456789012:certificate/b4c305ad-c024-463c-a834-72822374a9b6
```

The `DefaultCertificateArn` is necessary to configure the default HTTPS listener. It should be possible to reference any valid ACM certificate. The certificate used for the actual web site is configuresd with the appropriate web stack.

## Web Servers

The template [web-target-group.yaml](web-target-group.yaml) creates an [Auto Scaling Group](https://aws.amazon.com/ec2/autoscaling/) of web servers which are placed in a target group and are associated with a load balancer created by the template [web-load-balancer.yaml](web-load-balancer.yaml).

Since all web traffic must be encrypted it is necessary to create a ACM certificate for the domain served by the web servers. The ARN of this certificate is a mandatory input for creating a stack based on this template.

```
aws cloudformation create-stack --stack-name web-target-group \
                                --template-body file://web-target-group.yaml \
                                --parameters ParameterKey=NetworkStackName,ParameterValue=my-vpc \
                                             ParameterKey=LoadBalancerStackName,ParameterValue=my-load-balancer \
                                             ParameterKey=Hostname,ParameterValue=www.example.com \
                                             ParameterKey=CertificateArn,ParameterValue=arn:aws:acm:us-east-1:123456789012:certificate/b4c305ad-c024-463c-a834-72822374a9b6
```
