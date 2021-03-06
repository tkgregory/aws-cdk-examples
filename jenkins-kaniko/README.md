# Jenkins for Kaniko

Sample Jenkins implementation in CDK, demonstrating concepts from the article *[Using Jenkins and Kaniko to build Docker images in AWS](https://tomgregory.com/using-jenkins-and-kaniko-to-build-docker-images-in-aws/)*.

Includes all the AWS resources required to run Kaniko:

* Kaniko build context S3 bucket
* Kaniko task definition
* Kaniko security group
* Kaniko task role
* Kaniko execution role
* Kaniko target repository

Environment variables for all the relevant resources are also added to the Jenkins ECS task, ready to be
used in a Jenkins pipeline.

## Deploying

Install required npm packages:

`npm install`

Ensure you have the following environment variables set:
* `CDK_DEFAULT_ACCOUNT=<your-aws-account-id>`
* `CDK_DEFAULT_REGION=<aws-region>`

Decide what values (if any) you want to pass for these optional context parameters.

* **certificateArn** is the ARN of a Certificate Manager certificate to be attached to the load balancer listener.
  If this isn't provided you won't be able to access your Jenkins instance.
* **hostedZoneName** is the name of a Route 53 hosted zone into which a `jenkins` CNAME record will be added e.g. set
  to `tomgregory.com` to register a CNAME record `jenkins.tomgregory.com` pointing at the load balancer DNS record

Then run this command:

`cdk deploy --context certificateArn=<certificate-arn> --context hostedZoneName=<hosted-zone-name>`

(you may be prompted to first run a `cdk bootstrap` command)

Once your stack has been created, you can:

1. access Jenkins by entering the admin password from the ECS task logs
1. in the setup wizard choose not to install any plugins, as the required plugins are already installed 
1. create a new user
1. on the main Jenkins page you can run the provided *kaniko-example* job to see Kaniko in action 
   (this may take around 10 minutes to complete).

## Cleanup

Don't forget to remove your deployment to avoid unnecessary costs. 

1. within the AWS console remove any images from the *kaniko-demo* ECR repository 
1. delete the CloudFormation stack from within the AWS console or run this command:

`cdk destroy --context certificateArn=<certificate-arn> --context hostedZoneName=<hosted-zone-name>`
