# AWS VPC Design

This repository contains the CloudFormation template to create a VPC in AWS environment. All the components which are created as part of this template are described below.

## Components

### VPC

Template creates a virtual private cloud within AWS for a given CIDR range and with a default [DHCP Options](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) set.

### Subnets

- Creates two public subnets in two different availability zones respectively. They talk to internet through InternetGatway
- Creates two private subnets in two different availability zones respectively. They talk to internet through NAT Gatway (defined in public subnets)

### InternetGateway

Template creates an InternetGateway for public subnets to talk to public internet.

### RouteTable

- Creates a route table and route through the InternetGateway to make it public. Two public subnets are also attached to this public route table.
- Createa route table and route through the NAT Gateway. Two private subnets are also attached to this route table.

### NACLs (Network Access Controls Lists)

- Creates an NACL with access control to only allow HTTP and HTTPs traffic from internet and attach it to two public subnets.
- Creates an NACL with access control to only allow HTTP, HTTPs, and RDS MySQL traffic and attach it to two private subnets.

### NAT Gateway

Template creates two NAT Gateway in each public subnets. This is used by resources in private subnets to talk to internet.

### Cluster

Template creates and ECS cluster of launch type FARGATE. It uses FARGATE_SPOT capacity providers for cost optimizations

### VPC Endpoints

Template creates 3 VPC Endpoints for ECR, S3, and CloudWatch (logs). If a resource/service needs to access to another AWS service, these endpoints allows a container in the private subnet to talk to either ECR, S3, or CloudWatch directly without needing to go via the NAT gateway, public ips, internet, VPN, DirectConnect etc. It goes through the AWS backbone network, privately and securely.

## Deploy CloudFormation

Current Repository uses GitHub Actions to deploy the cloudformation template in the AWS. Triggers are disabled and it is manual at the moment.

Once you have made the necessary change in either CloudFormation template or the GitHub Action [workflow file](https://github.com/ashish246/aws-vpc-design/blob/main/.github/workflows/main.yml), then navigate to the main page of GitHub repository > Click `Actions` > In the left sidebar, click `Deploy CloudFormation Stacks` > Click `Run Workflow`.

You should see the pre-filled data > Validate/update the input parameters > `Run Workflow`.
