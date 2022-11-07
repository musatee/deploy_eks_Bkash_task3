# About the project
It's a simple Java Spring Boot application. A continuous Integration & deployment (CI/CD) process is implemented on this project using Jenkins. 

Each time any changes made to this spring boot application code base enables Jenkins to build spring boot docker image , push to docker hub & deploy the newly created image to EKS cluster. 

## Setting up of an EKS cluster
I've setup the EKS cluster using terraform.
***github repo:*** https://github.com/musatee/EKS_cluster_using_Terraform.git 
