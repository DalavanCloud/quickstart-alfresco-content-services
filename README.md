Quick Start Alfresco One
========================

This Quick Start reference deployment includes architectural considerations and configurations used to build an Alfresco One environment on the Amazon Web Services (AWS) cloud. We discuss how to build and configure the necessary AWS services, such as Amazon Elastic Compute Cloud (Amazon EC2) and Amazon Virtual Private Cloud (Amazon VPC) to deploy a highly available Alfresco One cluster across separate AWS Availability Zones.

Deployment Guide: http://docs.aws.amazon.com/quickstart/

*Disclaimer:* This CloudFormation template should be considered for illustrative and reference purposes. No warranty is expressed or implied. Improvements and any other contributions are encouraged and appreciated.

*Notes:*
* Extensive documentation about how to use this template is also available at http://aws.amazon.com/quiskstart/
* GovCloud and China regions are not supported in this release.
* Chef-Alfresco is used to configure each instance, not Chef knowledge is required.

*Demo:* Watch a 6' minutes demo here https://youtu.be/zEmoI9WwIXo

Overview
--------------------

This template will instantiate a 2-node Alfresco cluster with 2 dedicated Index servers the following capabilities:
* All Alfresco and Index nodes will be placed inside a Virtual Private Cloud (VPC) each node in a different AZ, same Region.
* An Elastic Load Balancer instance with "sticky" sessions based on the Tomcat JSESSIONID.
* Shared S3 ContentStore
* MySQL database on RDS instances in Multi-AZ mode.
* Each Alfresco and Index node will be in a separate Availability Zone.
* We use a pre-baked AMI. Our official Alfresco One AMI published in the AWS Marketplace, based on CentOS7.
* Auto-scaling rules that will add extra Alfresco and Index nodes when certain performance thresholds are reached.

Requirements
-----------
* An AWS Account with privileges to create resources
* BYOL or Alfresco One trial license with cluster supported (contact Alfresco Sales if needed)
* Subscribe to CentOS7 AMI in AWS Marketplace (more details below)
* Alfresco CloudFormation template that you can find in this GitHub repo.


Considerations for Production Environments
------------------------------------------
* Do a proper Sizing based on https://www.alfresco.com/resources/whitepapers/alfresco-one-51-sizing-guide
* Choose proper instance type (EC2/RDS)
* Choose proper number of nodes for your deployment
* Index storage, proper EBS volumes must be set on index instances, it has ephemeral storage by default
* HTTPS is not set in ELB as endpoint but it is allowed by default in Security Groups
* Autoscaling custom setup is set by default based on CPU
* General Testing / Upgrades
* No OS Hardening for NAT and Servers is in place
* Solr 4 in Alfresco 5.1 has not applied any specific tuning, i.e.: rerank or sharding
* CloudFront WAF is not included

Best Practices Applied
----------------------
* AWS Networking Security
	* VPC, Bastion hosts, Public and Private Subnets
* RDS SSL
* EC2 Instance Profile
* S3 SSL
* S3 Server Side Encryption AES-256
* Alfresco DB connection tuning
* Memory tuning in Tomcat
* Tomcat separation per Application (Alfresco repo, Alfresco Share and Solr)
* Haproxy + Nginx protect Tomcats

Troubleshooting:
----------------
### SSH access
* First, jump to one of your Bastion servers (note: ec2-user):

```
ssh -i your-priv-key.pem ec2-user@BASTION-PUBLIC-IP
```

* Copy your private SSH key to that NAT from your workstation:

```
scp -i your-priv-key.pem your-priv-key.pem ec2-user@BASTION-PUBLIC-IP:/home/ec2-user
```

* In the NAT server, set the appropriate permissions to the private key file:

```
chmod 400 your-priv-key.pem
```

* From the NAT server, connect to any Alfresco or Index server (note: centos user!):

```
ssh -i your-priv-key.pem centos@ALFRESCO-OR-INDEX-SERVER-IP
```

### Starting and Stopping Services

When you are logged in to the servers as root user, you can stop, start, or restart the Alfresco-related services on the Alfresco One and Index servers as follows:
```
systemctl {start,stop,restart} tomcat-alfresco
systemctl {start,stop,restart} tomcat-share
systemctl {start,stop,restart} tomcat-solr
systemctl {start,stop,restart} haproxy
systemctl {start,stop,restart} nginx
```

Note: On the Index servers, only tomcat-solr and tomcat-alfresco are available. And only tomcat-share, tomcat-alfresco, haproxy and nginx is available on the Alfresco servers.


### Logs and Configuration Files Location
* Logs for Alfresco and Share on the Alfresco Instances
```
/var/log/tomcat-{share,alfresco}
```
* Logs for Alfresco (for index tracking) and Solr on the Alfresco Index instances
```
/var/log/tomcat-{solr,alfresco}
```
* Alfresco configuration files (any instance):
```
/usr/share/tomcat/shared/classes/alfresco-global.properties
/usr/share/tomcat/shared/classes/alfresco/extension/
```
* For Share
```
/usr/share/tomcat/shared/classes/alfresco/web-extension/
```
* JAVA_OPTS configuration variable for Tomcats:
```
/etc/sysconfig/tomcat-alfresco
/etc/sysconfig/tomcat-share
/etc/sysconfig/tomcat-solr
```
* server.xml for each Tomcat:
```
/etc/tomcat-alfresco/server.xml
/etc/tomcat-share/server.xml
/etc/tomcat-solr/server.xml
```
* All Tomcats in the servers uses this common files:
```
/etc/tomcat/catalina.policy
/etc/tomcat/catalina.properties
/etc/tomcat/context.xml
/etc/tomcat/tomcat-users.xml
/etc/tomcat/web.xml
```
* Solr configuration and default location:
```
/usr/share/tomcat/alf_data/
/usr/share/tomcat/alf_data/solrhome/workspace-SpacesStore/conf/solrcore.properties
/usr/share/tomcat/alf_data/solrhome/archive-SpacesStore/conf/solrcore.properties
/usr/share/tomcat/alf_data/solrContentStore (gzipped content)
/usr/share/tomcat/alf_data/solr4Backup/
```
* Logging properties for each Tomcat:
```
/usr/share/tomcat-alfresco/conf/logging.properties
/usr/share/tomcat-share/conf/logging.properties
/usr/share/tomcat-solr/conf/logging.properties
```
* Alfresco nodes come with Nginx and HAProxy:
```
/etc/nginx/nginx.conf
/var/log/nginx/
/etc/haproxy/haproxy.cfg
/var/log/haproxy/
```

License
-------
   Copyright 2016 Alfresco Software, Ltd.
   Copyright 2016 Amazon Web Services, Inc.

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

This work may reference software licensed under other open source licenses, please refer to these respective works for more information on license terms.
