# Elastic Compute Cloud (EC2)

## Creating an EC2 instance.

Before you can create your EC2 instance, you may want to create a vpc first, or alternatively you can use the default VPC. Before proceeding to read this section, it is recommended that you read the [VPC page](vpc.md) first.

- To create an EC2 instance, it is as simply as selecting an AMI, an instance size and then configuring instance details.
- Select the VPC and subnet to which it belongs, and whether to auto assign a public IP.
- If you use the Wizard, it will create a security group for you. You can use this group, or you can create a new group or configure this one.
- The default security group will allow any address to connect to TCP port 22, which is used by SSH.

### Quotas
Default EC2 resource limit for a new account is 20 instances
