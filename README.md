# Learn Terraform Provider Versioning

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/.../...)

This repo is a companion repo to the [Lock and Upgrade Provider Versions Learn guide](https://learn.hashicorp.com/tutorials/terraform/provider-versioning), containing Terraform configuration files to provision a S3 bucket.

**Do not** clone the Learn Terraform Provider Versioning repository. You can skip that step as the files are already included here.

This tutorial uses AWS S3 bucket. It should be free to create the bucket, but I'm not sure about this. To be safe, skip the `terraform apply` steps.

Add changes to version control as you go through the steps in the tutorial. This will make it easier to see which configuration settings have changed.

**Warning:** Not sure if all the AWS resources provisioned in this tutorial are covered by the free tier. There may be some small charges. If you leave resources provisioned and don't `terraform destroy` at the end, there definitely will be charges.

## AWS CLI Credentials
You will need to add your AWS Access Keys to the AWS CLI client. Configure the AWS CLI from the terminal. Follow the prompts to input your AWS Access Key ID and Secret Access Key.

```bash
$ aws configure
```

The configuration process stores your credentials in a file at `~/.aws/credentials` within the Gitpod workspace.

## Mocking AWS

Instead of applying changes to the real AWS, we can create a fake, local version of AWS and test our configuration against this fake version. This will mean no real resources are provisioned in AWS.

### Docker

Run the following docker command in the terminal.

```bash
docker run --rm -it -p 4566:4566 -p 4571:4571 localstack/localstack
```

This will pull and run a docker container containing an image that will mock AWS calls for us.

### Configure Resource Provider

In `main.tf`, replace the `provider "aws"` block

```bash
provider "aws" {
  region  = "us-west-2"
}
```

with the following:

```bash
provider "aws" {
  region                      = "us-west-2"
  access_key                  = "mock_access_key"
  secret_key                  = "mock_secret_key"
  skip_credentials_validation = true
  skip_metadata_api_check     = true
  skip_requesting_account_id  = true
  s3_force_path_style         = true

  endpoints {
    apigateway     = "http://localhost:4566"
    cloudformation = "http://localhost:4566"
    cloudwatch     = "http://localhost:4566"
    dynamodb       = "http://localhost:4566"
    es             = "http://localhost:4566"
    firehose       = "http://localhost:4566"
    iam            = "http://localhost:4566"
    kinesis        = "http://localhost:4566"
    lambda         = "http://localhost:4566"
    route53        = "http://localhost:4566"
    redshift       = "http://localhost:4566"
    s3             = "http://localhost:4566"
    secretsmanager = "http://localhost:4566"
    ses            = "http://localhost:4566"
    sns            = "http://localhost:4566"
    sqs            = "http://localhost:4566"
    ssm            = "http://localhost:4566"
    stepfunctions  = "http://localhost:4566"
    sts            = "http://localhost:4566"
    ec2            = "http://localhost:4566"
  }
}
```

Log-in to AWS again, using the fake credientials

```
access_key: mock_access_key
secret_key: mock_secret_key
```
