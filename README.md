# ElasticBeanStalk

**Description:**

Elastic Beanstalk can quickly deploy and manage applications in the AWS Cloud without having the knowledge of infrastructure that runs the application. It automatically handles the details of capacity provisioning, load balancing, scaling, and application health monitoring.

The first step in using Elastic Beanstalk is to create an application, which represents your web application in AWS. In Elastic Beanstalk an application serves as a container for the environments that run your web app, and versions of your web app's source code, saved configurations, logs, and other artifacts that you create while using Elastic Beanstalk.

AWS Elastic Beanstalk makes it easy to create new environments for your application. You can create and manage separate environments for development, testing, and production use, and you can deploy any version of your application to any environment. Environments can be long-running or temporary. When you terminate an environment, you can save its configuration to recreate it later.

**Infrastructure for Prod & Dev/Stage:**
![Alt Text](https://github.com/prakhya79/ElasticBeanStalk/blob/master/prodinfra.png)

![Alt Text](https://github.com/prakhya79/ElasticBeanStalk/blob/master/devinfra.png)

**Capabilities:**

Ibexlabs Elastic Beanstalk product creates an application(on the choice among PHP, NodeJS, Python, Java (Tomcat) application environments for Dev, Stage or Prod. If we have already an existing application we can create different environments alone for the application.

This product will launch an Aurora cluster with RDS MySQL as the Database. Only one instance will be launched if you choose development or staging when specifying EnvironmentName. Whereas if you choose production, two instances will be launched in three different Availability Zones (AZs), this is known as read-replica. It is created to increase availability, Aurora Replicas can be used as failover targets. That is, if the primary instance fails, an Aurora Replica is promoted to the primary instance. For the more secure purpose, it was designed as even if you delete the Environment RDS will not be deleted. The AuroraDBUsername, AuroraDBPassword, and DatabaseName will be needed to connect into the AuroraRDSDBInstance.

>**Note:** Aurora will only launch for regions with more than 2 availability zones (AZs) because it needs at least 3 AZs as it replicates the data across all the AZs (2 copies in each AZ, hence there will be 6 copies of your data stored across the region).

For a more secure purpose, it was designed as even if you delete the Environment RDS will not be deleted.

An Elastic Beanstalk environment would be provisioned with ec2 instances launched in private subnets(makes servers not exposed to the world) under auto-scaling(that monitors your applications and automatically adjusts capacity to maintain steady, predictable performance at the lowest possible cost) behind load-balancer in public subnets(to distribute the incoming traffic across multiple targets, such as Amazon EC2 instances and exposes application) with enhanced health monitoring, All-at-once deployment policy, patch version updates for patching the elastic beanstalk environment. Also, an S3 bucket will be provisioned for archiving artifacts of further deployments.

We have several options for how deployments are processed, including deployment policies (All at once, Rolling, Rolling with additional batch, and Immutable). By default, your environment uses all-at-once deployments.

-   All at once – Deploy the new version to all instances simultaneously. All instances in your environment are out of service for a short time while the deployment occurs.
    
-   Rolling – Deploy the new version in batches. Each batch is taken out of service during the deployment phase, reducing your environment's capacity by the number of instances in a batch.
    
-   Rolling with additional batch – Deploy the new version in batches, but first launch a new batch of instances to ensure full capacity during the deployment process.
    
-   Immutable – Deploy the new version to a fresh group of instances by performing an immutable update.
    

Enhanced health reporting is a feature that is enabled by default on your environment to allow AWS Elastic Beanstalk to gather additional information about resources in your environment. Elastic Beanstalk analyzes the information gathered to provide a better picture of overall environmental health and aid in the identification of issues that can cause your application to become unavailable.

We may update the Route53 domain name, hosted zone id, and SSL Certificate generated with the domain name so that we can map load balancer endpoint to the route53 hosted zone.

SSL Certificate can be generated by following this [documentation](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-public.html "https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-public.html")

For example, if we have a nodejs application for a dev environment for the customer named ibexlabs, the application that's running in the environment would be exposed in the below endpoint.

[http://ibexlabs-dev-nodejs.ibexlabs.com](http://ibexlabs-dev-nodejs.ibexlabs.com/ "http://ibexlabs-dev-nodejs.ibexlabs.com/")

Once after elastic beanstalk application is created, we may update the environment variables on beanstalk console that are specific to the application at All Applications> {Application-Name} > {Environment-Name} > Configuration > Modify software.

We can add .ebextensions with source code to configure your environment and customize the AWS resources that it contains. Environment variables for the beanstalk environment can be updated as shown below in any configuration file of .ebextensions folder.

*  Option_settings: 
      * aws:elasticbeanstalk:application:environment:
    *  DB_Host: Some_host_name

For more information regarding .ebextensions please go through the [documentation](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html "https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html").

Keeping the sensitive information that is required to update the configuration with near the source code isn’t recommended, AWS recommends to store the sensitive information SSM parameter store(service that helps you arrange your data in a systematic hierarchical format for better reference. Data can be of any type like Passwords, Keys, URLs or Strings. Data can be stored as encrypted or as plain text), helps you to store data in the key-value format and can be called from configuration files of .ebextensions folder.

### 

**Patching**

Managed platform updates are enabled by default to apply patch version updates for both minor and patch version updates. Patch version updates provide bug fixes and performance improvements and can include minor configuration changes to the on-instance software, scripts, and configuration options. Minor version updates provide support for new Elastic Beanstalk features. The update would be applied to the maintenance window that we define.
