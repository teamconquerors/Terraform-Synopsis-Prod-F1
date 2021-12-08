# Terraform Synopsis Project

## Provisioning AWS EKS cluster using Terraform as IAAC and deploying Nginx web page using Helm.

## Terraform Server
##### In order to provision infrastructure on AWS, either a user or instance must have an IAM role with administratorAccess role attached to it.
This role will grant the user/instance access to all AWS resources. If running Terraform script as a user, we can add the users acces_key and access_key_id. For best practice, we do not pass any authentication information directly on the script/CLI. Instead, we can install AWS CLI and run AWS configure to pass our credentials
https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html
  and run AWS configure to pass user credentials. 
  
### Infrastructure
##### VPC : For best practice we will deploy our application within a custom AWS VPC to further secure our infrastructure. Doing so means we can have control over which of the infrastructure is exposed to the internet. Our cluster will be provisioned in the private subnet to limit internet access, for end user access an  internet facing LoadBalancer will be provision which can also be granted a DNS alias with route53. By default, "enable DNS hostname" is turned off, so we will need to enable it for our application to be accessed on the LoadBalancer. EKS requires that 4 subnets are provisioned in 2 different AZ's, 2 public subnets and 2 Private subnets. We must also associate the EKS cluster with the VPC and subnets using cluster tags as referenced by AWS:
https://aws.amazon.com/premiumsupport/knowledge-center/eks-vpc-subnet-discovery/
https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eks_cluster

#### infrastructure list
2) 2 public subnets
3) 2 private subnets
4) 1 Internet Gateway
5) 1 Nat Gateway 
6) EIP for Nat gateway
7) public/private Rout Tables
8) EKS cluster 
9) Security Group 
10) Worker Nodes
11) S3 bucket to store terraform statefile remotely 
12) DynamoDB to lock statefile, ensuring that no two people are using the statefile simultaneously.  

## Terraform Server


## What to do?
### 1) Deploy s3 bucket: 
###### S3 bucket must first be provisioned before all other infrastructure. Reason being that we will need to store the Statefile remotely to allow infrastructure to be managed as a team. Storing the statefile in an s3 bucket also allows us to encrypt our bucket using KMS and locking the statefile with dynamodb tables. AS best practice this should always be done as the statefile functions to store any critical information about the infrastructure and so only certain individuals should have access to it. In order to provision S3 first we must store S3 script in a separate directory. 
https://www.terraform.io/docs/language/settings/backends/s3.html
- #### Provisioning S3
- 1) CD into S3 directory and run the following commands. 
- 2) "terraform init"  - initiates working directory and downloads any provider dependencies. 
- 3) " terraform validate"  - validates syntax 
- 4) "terraform fmt" checks variables
- 5) " terraform plan -out s3.plan "  - for best practice we want to append the terraform plan output to the .plan file to ensure that we are able to roll back in any instance.
- 6) "terraform apply s3.plan --auto-approve"   - the apply option will now make changes to our provider and --auto-approve skips the approval step. 


## 2) Deploy Infrastructure
#### with our s3 bucket provisioned. We can now create the rest of our infrastructure. This directory must also be initiated with "terraform init" as we will need to create the terraform backend in the S3 bucket. The main.tf file holds all resources that are to be created. Once again as per best practice we must make our code reusable by implementing variables whenever possible on the objects within our resource blocks. Making Code reusable means a varaible.tf file is required to declare our variables and a .tfvars file is needed to define the variables which can be called in the main.tf file as "var.<variableName>". A provider.tf file functions to hold the provider block and an output.tf file for all resources we will require the output of.

  ## Provision infrastructure
  - 1) cd into synopsis directory
  - 2) "terraform init"
  - 3) "terraform validate"
  - 4) "terraform fmt"
  - 5) "terraform plan -out eks.plan"
  - 6) "terraform apply s3.plan --auto-approve" 
  - 7) run following command on profile that created cluster
to give it privilege to manage cluster : aws eks update-kubeconfig --region us-east-1 --name <clusterName>
  




