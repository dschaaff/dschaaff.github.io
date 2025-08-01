# Curriculum Vitae

{{< details "Table of Contents" >}}

<!-- TOC -->

- [Curriculum Vitae](#curriculum-vitae)
- [Daniel Schaaff](#daniel-schaaff)
  - [Professional Summary](#professional-summary)
  - [Professional Experience](#professional-experience)
    - [Principal DevOps Engineer, Cordial - San Diego, CA - July 2025 to Present](#principal-devops-engineer-cordial---san-diego-ca---july-2025-to-present)
    - [Lead DevOps Engineer, Cordial - San Diego, CA - July 2020 to July 2025](#lead-devops-engineer-cordial---san-diego-ca---july-2020-to-july-2025)
    - [Senior DevOps Engineer, Cordial - San Diego, CA - February 2019 to July 2020](#senior-devops-engineer-cordial---san-diego-ca---february-2019-to-july-2020)
    - [Lead System Operations Engineer, Verve - Carlsbad, CA - December 2018 to February 2019](#lead-system-operations-engineer-verve---carlsbad-ca---december-2018-to-february-2019)
    - [Senior DevOps Engineer, Gimbal - San Diego, CA - May 2017 to December 2017](#senior-devops-engineer-gimbal---san-diego-ca---may-2017-to-december-2017)
    - [DevOps and IT Operations Manager, KnuEdge - San Diego, CA - September 2014 to May 2017](#devops-and-it-operations-manager-knuedge---san-diego-ca---september-2014-to-may-2017)
    - [Network Administrator, Choose Chicago - Chicago, IL - April 2013 to September 2013](#network-administrator-choose-chicago---chicago-il---april-2013-to-september-2013)
  - [Education and Certifications](#education-and-certifications)

<!-- /TOC -->

{{< /details >}}

# Daniel Schaaff

## Professional Summary

Proven DevOps leader looking to leave his mark on the world through technology and relationships.

## Professional Experience

### Principal DevOps Engineer, Cordial - San Diego, CA - July 2025 to Present

### Lead DevOps Engineer, Cordial - San Diego, CA - July 2020 to July 2025

### Senior DevOps Engineer, Cordial - San Diego, CA - February 2019 to July 2020

### Lead System Operations Engineer, Verve - Carlsbad, CA - December 2018 to February 2019

Worked to improve software delivery velocity, pay down technical debt, and align operations with product priorities.

- Migrated Clojure and Ruby based servers into Docker orchestrated by AWS ECS
- Utilized lambda to seamlessly autoscale ECS cluster instances in and out as containers scale in and out
- Implemented infrastructure as code using Terraform, replacing a mix of Puppet code, CloudFormation, and standalone scripts giving a single view into the infrastructure
- Designed and built robust continuous delivery pipelines for Clojure, Java Springboot, Golang, React and Rails based microservices, simplifying the path to production
- Modernized the Jenkins infrastructure with pipelines, web hooks, and AWS spot fleet based build agents
- Empowered developers to own their software deployments, reducing reliance on tickets with the ops team
- Built a continuous integration environment, helping developers get early feedback on code changes
- Designed and built a unified logging pipeline with Fluentd, s3, Elasticsearch, and Elastalert
- Used Docker to run scaleable integration tests in isolated environments within software delivery pipelines
- Greatly reduced AWS costs through a mixture of workload rightsizing, spot instance utilization, resource cleanup, and autoscaling
- Developed custom monitoring for a variety of resources, including database migrations and s3 objects
- Greatly improved observability through metrics, application performance monitoring, and centralized logging leading to the resolution of a number of long standing bugs
- Worked directly with software engineering teams to align ops priorities with product priorities
- Designed and deployed on call rotation and incident post mortem processes

### Senior DevOps Engineer, Gimbal - San Diego, CA - May 2017 to December 2017

Worked to modernize the Gimbal build and delivery pipeline to increase velocity, reliability, and efficiency.

- Completed migration of Java microservices to Docker and ECS
- Moved from monolithic, all or nothing, deployment methodology to delivering each service independently as needed
- Cut down release and deploy cycle from a full day to under an hour greatly, increasing the speed and simplicity of delivering Gimbal applications
- Redesigned a streamlined CI/CD infrastructure utilizing Jenkins pipelines, Jenkins shared libraries, and dynamic EC2 build slaves leading to lower EC2 costs and faster build/test feedback cycles
- Implemented continuous delivery pipeline for Java and Rails projects with automatic delivery to development and staging environments based on git tags/branches with manual promotion to production environments
- Moved team from configuring AWS manually via the console to terraform code, leading to increased consistency between environments, fewer configuration errors, simple rollback plans, and clear audit trails
- Redesigned Chef cookbook management to utilize independent git repos, tagged releases, integration tests, and auto promotion to AWS OpsWorks stacks
- Modified Gimbal Manager Ruby on Rails application to accept dynamic configuration by environment variables and run in a portable Docker environment
- Automated delivery of iOS SDK releases to Cocoapods.
- Moved monitoring from basic Cloudwatch integrations to in-depth metric collection and reporting with Datadog
- Implemented a highly resilient data pipeline based on Fluentd and Elasticsearch
- Worked directly with developers to ensure infrastructure and application development operated in a lock-step manner while delivering on the product vision

### DevOps and IT Operations Manager, KnuEdge - San Diego, CA - September 2014 to May 2017

Led internal IT from a cost centered, reactive orientation to a proactive role in supporting, developing, and deploying KnuEdge products and technologies.

- Led network design and buildout of two remote offices in under three months
- Led move of San Diego office and datacenter to a new location with minimal downtime and business interruption
- Led infrastructure as code initiative bringing faster change, better documentation, reusable components, and clear audit trails
- Automated the deployment of Linux and Windows across bare metal, VMWare, and AWS
- Championed culture of openness, sharing, and empathy while increasing collaboration across the company through knowledge wikis, pull requests, ChatOps, knowledge shares and an open first philosophy
- Implemented Puppet for configuration management across Linux, macOS, and Windows
- Developed both public and private Puppet modules for managing/deploying applications and services
- Designed and implemented testing pipeline for Puppet code utilizing RSpec, Bamboo, and Travis reducing faults and increasing confidence in deployments
- Automated migration from Puppet 3 to new Puppet 4 master with no interruption to services
- Developed CI pipeline for managing macOS software utilizing Munki, AutoPKG, Reposado, and Bamboo ensuring vulnerabilities were patched quickly and software remained up to date
- Designed and deployed central logging infrastructure using syslog-ng, Logstash, Elasticsearch, and Kibana
- Deployed and managed AWS infrastructure using Terraform
- Utilized and supported LXC and Docker containers both for production services and development workflows

### Network Administrator, Choose Chicago - Chicago, IL - April 2013 to September 2013

Led key initiatives as Network Administrator at Choose Chicago improving network performance, reliability, and security while providing world class support to end users.

- Implemented VLANs in a multi vendor environment isolating end user and backend server traffic into separate broadcast domains, reducing broadcast traffic in the network core by 50%
- Implemented Cisco Gateway Load Balancing Protocol providing a fully redundant default gateway to clients and servers
- Reconfigured Spanning Tree Protocol on all switches to elect the core network switch as the root bridge and optimize traffic paths
- Designed and implemented an access control list safely securing the guest WiFi VLAN
- Installed and configured SharePoint 2013 and Office Web Apps Server farms
- Designed and configured a SharePoint workflow to automate the approval process for purchasing and tracking airline tickets company wide
- Migrated web applications from SharePoint 2010 to 2013 and setup external access via HTTPS
- Migrated the Choose Chicago main office LAN to a new IP addressing scheme to address a shortage of free addresses and reconfigured routing company wide
- Installed, configured, and deployed Windows update and deployment services on the server 2012 platform to simplify the management of client machines
- Maintained a highly virtualized environment with VMware ESXi and NexSan SAN storage appliances
- Maintained WAN and LAN networks across 3 office locations
- Performed the day to day operation and maintenance of Active Directory, Exchange 2010, and other key Windows servers

## Education and Certifications

Most recent certifications available on [Credly](https://www.credly.com/users/daniel-schaaff/badges)

- Bachelors of Science in Network Communications Management - DeVry University
- AWS Certified Solutions Architect Professional
- AWS Certified Solutions Architect Professional
- Puppet Certified Professional
- CCNA
