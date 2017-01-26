# Alfresco One on the AWS Cloud
## Quick Start Reference Deployment

This new Quick Start automatically deploys an Alfresco One cluster on the AWS Cloud. The Quick Start was created by AWS in partnership with Alfresco Software, to integrate solutions and services from both companies.
Alfresco One is an Enterprise Content Management (ECM) system that is used for document and case management, project collaboration, web content publishing, and compliant records management. The flexible compute, storage, and database services that AWS offers make it an ideal platform for Alfresco One. This Quick Start presents an enterprise-grade Alfresco One configuration that you can adapt to virtually any scenario, and scale up, down, or out, depending on your use case.

![Quick Start Alfresco One Design Architecture](https://d3ulk6ur3a3ha.cloudfront.net/partner-network/QuickStart/datasheets/alfresco-architecture-diagram.png)

The Quick Start architecture includes the following components:
* Highly available Alfresco One servers and Index servers deployed across two Availability Zones within your selected region. The servers take advantage of Auto Scaling and Elastic Load Balancing, with CPU-based thresholds that you can customize.
* Amazon Simple Storage Service (Amazon S3) for shared file storage.
* Amazon Relational Database System (Amazon RDS) for MySQL with Multi-AZ enabled for the shared database. If you expect your database to exceed 6 TB, you can also switch to Amazon Aurora.

The Alfresco One deployment is automated by nested AWS CloudFormation templates. The templates call custom Chef cookbooks to automate installation and configuration tasks for Alfresco One. The Quick Start gives you a option to either build a new AWS environment for Alfresco One or use your existing AWS environment.


Deployment Guide: https://s3.amazonaws.com/quickstart-reference/alfresco/one/latest/doc/alfresco-one-on-the-aws-cloud.pdf


*Demo:* Watch a 6 minute demo here https://youtu.be/zEmoI9WwIXo
