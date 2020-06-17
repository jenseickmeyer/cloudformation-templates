# API Gateway Configuration

## Custom Domains

The AWS API Gateway supports two two endpoint configurations for custom domains: Edge and Regional. In both cases a valid SSL certificate is needed to set up the custom domain. The CloudFormation configuration differs slightly which is why there are two different templates.

In both cases a custom domain, the SSL certificate managed by the AWS Certificate Manager and a DNS record in Route 53 are created.

To set up the custom domain for the Edge endpoint configuration the following AWS CLI command can be used.

```
aws cloudformation create-stack --stack-name my-custom-domain \
                                --template-body file://custom-domain-edge.yaml  \
                                --parameters ParameterKey=HostedZoneId,ParameterValue=Z2CV1LKAQAGQWM \
                                             ParameterKey=Domain,ParameterValue=api.example.com \
                                --region us-east-1
```

To set up the custom domain for the Regional endpoint configuration the following AWS CLI command can be used.

```
aws cloudformation create-stack --stack-name my-custom-domain \
                                --template-body file://custom-domain-regional.yaml  \
                                --parameters ParameterKey=HostedZoneId,ParameterValue=Z2CV1LKAQAGQWM \
                                             ParameterKey=Domain,ParameterValue=api.example.com
```
