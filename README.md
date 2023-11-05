# aws-iam-initial-roles
First Roles deployed to an AWS Account so it can be Accessed and Managed


Identity/Management Account

ACCOUNT_ID: 000000000000
RESOURCE: IAM User
NAME: terraform-multiaccount
POLICY:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::111111111111:role/terraform-multiaccount-role"
        }
    ]
}
```
Shared-Services Account:

ID: 1111111111111

IAM Role 

terraform-multiaccount-role

arn:aws:iam::aws:policy/PowerUserAccess
arn:aws:iam::aws:policy/AmazonDynamoDBFullAccess
arn:aws:iam::aws:policy/AmazonS3FullAccess

Custom Policy

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": [
                "arn:aws:iam::222222222222:role/terraform-role",
                "arn:aws:iam::333333333333:role/terraform-role",
                "arn:aws:iam::444444444444:role/terraform-role",
                "arn:aws:iam::555555555555:role/terraform-role"
            ]
        }
    ]
}

Trust policy

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::000000000000:user/terraform-multiaccount"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}

Workload Accounts

Dev: 222222222222

Test: 333333333333

Prod: 444444444444

IAM Role

terraform-role

Policy

arn:aws:iam::aws:policy/PowerUserAccess
arn:aws:iam::aws:policy/AmazonDynamoDBFullAccess
arn:aws:iam::aws:policy/AmazonRoute53FullAccess
arn:aws:iam::aws:policy/AmazonS3FullAccess
arn:aws:iam::aws:policy/CloudFrontFullAccess
arn:aws:iam::aws:policy/AWSCertificateManagerFullAccess

Trust policy

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::111111111111:role/terraform-multiaccount-role"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}

DNS Management Account:

ID: 555555555555

IAM Role

terraform-role

Policy

arn:aws:iam::aws:policy/PowerUserAccess
arn:aws:iam::aws:policy/AmazonDynamoDBFullAccess
arn:aws:iam::aws:policy/AmazonRoute53FullAccess
arn:aws:iam::aws:policy/AmazonS3FullAccess

Trust policy

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::111111111111:role/terraform-multiaccount-role"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}



AWS Credentials file



[terraform]
aws_access_key_id=AKI.........HOW
aws_secret_access_key=rZR3I.......a9lh48



AWS Config file



[profile shared-services]
role_arn=arn:aws:iam::111111111111:role/terraform-multiaccount-role
source_profile=terraform
region=us-east-1
output=json



Terraform Provider



provider "aws" {
  region = "us-east-1"
  profile = "shared-services"
  assume_role {
    role_arn = "arn:aws:iam::${lookup(local.account_mapping, local.env)}:role/terraform-role"
  }
}

locals {
  env = var.env
  account_mapping = {
    dev : 222222222222
    test : 333333333333
    uat : 444444444444
  }
}
