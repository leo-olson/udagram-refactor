# Udagram Refactor

Udagram is a simple cloud application developed alongside the Udacity Cloud Engineering Nanodegree. It allows users to register and log into a web client, post photos to the feed, and process photos using an image filtering microservice.

The project is split into three parts:
1. [The Simple Frontend](/udacity-c3-frontend)
A basic Ionic client web application which consumes the RestAPI Backend. 
2. [The RestAPI Feed Backend](/udacity-c3-restapi-feed), a Node-Express feed microservice.
3. [The RestAPI User Backend](/udacity-c3-restapi-user), a Node-Express user microservice.

## Getting Started

The following tools need to be installed on your machine:

Docker
AWS CLI
Kubectl
KubeOne
Furthermore, you need to have:

an Amazon Web Services account
a DockerHub account

### Clone the repository
Clone the repository on your local machine

### S3 Bucket Setup

Save the following policy in the Bucket policy editor:
```
{
 "Version": "2012-10-17",
 "Id": "Policy1565786082197",
 "Statement": [
 {
 "Sid": "Stmt1565786073670",
 "Effect": "Allow",
 "Principal": {
 "AWS": "__YOUR_USER_ARN__"
 },
 "Action": [
 "s3:GetObject",
 "s3:PutObject"
 ],
 "Resource": "__YOUR_BUCKET_ARN__/*"
 }
 ]
}
```
Modify the variables __YOUR_USER_ARN__ and __YOUR_BUCKET_ARN__ by your own data.

CORS configuration
Save the following configuration in the CORS configuration Editor:
```
<?xml version="1.0" encoding="UTF-8"?>
 <CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
 <CORSRule>
 <AllowedOrigin>*</AllowedOrigin>
 <AllowedMethod>GET</AllowedMethod>
 <AllowedMethod>POST</AllowedMethod>
 <AllowedMethod>DELETE</AllowedMethod>
 <AllowedMethod>PUT</AllowedMethod>
 <MaxAgeSeconds>3000</MaxAgeSeconds>
 <AllowedHeader>Authorization</AllowedHeader>
 <AllowedHeader>Content-Type</AllowedHeader>
 </CORSRule>
</CORSConfiguration>
```


### Local Deployment

Docker is used to start the application on the local environment

The variables below need to be added to your environment:
```
POSTGRESS_USERNAME=udagram
POSTGRESS_PASSWORD=local
POSTGRESS_DB=udagram
POSTGRESS_HOST=db
WT_SECRET=mySecret
AWS_BUCKET=__YOUR_AWS_BUCKET_NAME__
AWS_REGION=__YOUR_AWS_BUCKET_REGION__
AWS_PROFILE=__YOUR_AWS_PROFILE__
```
Replace the variables __YOUR_AWS_BUCKET_NAME__, __YOUR_AWS_BUCKET_REGION__ and __YOUR_AWS_PROFILE__ by your own information

Build the images by running:
```
docker-compose -f docker-compose-build.yaml build --parallel
```
Start the application and services:
```
docker-compose up
```
The application is now running at http://localhost:8100

### AWS Deployment using Terraform and Kubeone
Provision the infrastructure
Add the following variables to your environment
```
AWS_ACCESS_KEY_ID=__YOUR_AWS_ACCES_KEY_ID__
AWS_SECRET_ACCESS_KEY=__YOUR_AWS_SECRET_ACCESS_KEY__
```
Move to the directory deployment/k8s/infrastructure and run the following command:
```
terraform init
```
Edit the values aws_region and ssh_public_key_file in the file terraform.tfvars by your own data.
Store the terraform variables below in a file named terraform.tfvars:
```
cluster_name = "udagram"
aws_region = "__YOUR_AWS_REGION__"
worker_os = "ubuntu"
ssh_public_key_file = "~/.ssh/id_rsa.pub"
Modify __YOUR_AWS_REGION__ by your AWS region.
```
Provision the infrastructure on AWS by executing:
```
terraform appl
Once it's created, create the Terraform state that will be used by KubeOne:
```
terraform output -json > tf.json
```
Install Kubernetes
```
kubeone install config.yaml --tfjson tf.json
```
Setup Kubeone config
```
export KUBECONFIG=$PWD/alexbox-kubeconfig
or 
kubectl —kubeconfig=alexbox-kubeconfig
```
