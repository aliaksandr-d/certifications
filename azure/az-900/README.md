# AZ-900: Microsoft Azure Fundamentals

## Understand cloud concepts (15-20%) 

### Describe the benefits and considerations of using cloud services
#### Understand terms such as high availability, scalability, elasticity, agility, fault tolerance, and disaster recovery
##### High Availability (HA)
The ability of the application to continue running in a healthy state, without significant downtime. By "healthy state," we mean the application is responsive, and users can connect to the application and interact with it. 

##### Scalability
Increase or decrease the resources and services used based on the demand or workload at any given time. Vertical Scaling (aka "scaling up) - add more resources to existing servers. Horizontal Scaling (aka "scaling out) - add more servers.

##### Vertical Scaling (aka "scaling up")
The process of adding resources to increase the power of an existing server (e.g. adding a faster CPU, additional CPUs, more memory).

##### Horizontal Scaling (aka "scaling out")
The process of adding more servers that function together as one unit (e.g. adding more servers).

##### Elasticity
Automatically add or remove resources based on demand.

##### Cloud Agility
Cloud agility is the ability to rapidly change an IT infrastructure in order to adapt to the evolving needs of the business (e.g. if your service peaks one month, you can scale to demand and pay a larger bill for the month. If the following month the demand drops, you can reduce the used resources and be charged less).

##### Fault Tolerance
Redundancy is often built into cloud services architecture so if one component fails, a backup component takes its place. This is referred to as fault tolerance and it ensures that your customers aren't impacted when an unexpected accident occurs.

##### Disaster Recovery
The ability to recover from rare but major incidents: non-transient, wide-scale failures, such as service disruption that affects an entire region. Disaster recovery includes data backup and archiving, and may include manual intervention, such as restoring a database from backup. 

#### Understand the principles of economies of scale
Economies of scale is the ability to do things more efficiently or at a lower-cost per unit when operating at a larger scale (e.g. the ability to acquire hardware at a lower cost than if a single user or smaller business were purchasing it, cloud providers can also make deals with local governments and utilities to get tax savings, lower pricing on power, cooling, and high-speed network connectivity between sites). 

#### Understand the differences between Capital Expenditure (CapEx) and Operational Expenditure (OpEx)
##### Capital Expenditure (CapEx)
CapEx is the spending of money on physical infrastructure up front, and then deducting that expense from your tax bill over time. CapEx is an upfront cost, which has a value that reduces over time.

##### Operational Expenditure (OpEx)
OpEx is spending money on services or products now and being billed for them now. You can deduct this expense from your tax bill in the same year. There is no upfront cost, you pay for a service or product as you use it. 

#### Understand the consumption-based model

### Describe the differences between Infrastructure-as-a-Service (IaaS), Platform-as-a-Service (PaaS) and Software-as-a-Service (SaaS)
#### Describe Infrastructure-as-a-Service (IaaS)
Infrastructure as a Service is the most flexible category of cloud services. It aims to give you complete control over the hardware that runs your application (IT infrastructure servers and virtual machines (VMs), storage, networks, and operating systems). Instead of buying hardware, with IaaS, you rent it. It's an instant computing infrastructure, provisioned and managed over the internet. 

#### Describe Platform-as-a-Service (PaaS)
PaaS provides an environment for building, testing, and deploying software applications. The goal of PaaS is to help you create an application quickly without managing the underlying infrastructure. For example, when deploying a web application using PaaS, you don't have to install an operating system, web server, or even system updates. PaaS is a complete development and deployment environment in the cloud. 

#### Describe Software-as-a-Service (SaaS)
SaaS is software that is centrally hosted and managed for the end customer. It is usually based on an architecture where one version of the application is used for all customers, and licensed through a monthly or annual subscription. Office 365, Skype, and Dynamics CRM Online are perfect examples of SaaS software. 

#### Compare and contrast the three different service type
|      | User | Cloud |
|------|------|-------|
| IaaS | Purchase, installation, configuration, and management of their own software operating systems, middleware, and applications. | Responsible for ensuring that the underlying cloud infrastructure (such,as virtual machines, storage, and networking) is available for the user. |
| PaaS | Responsible for the development of their own applications. | Responsible for operating system management, and network and service configuration. |
| SaaS | Users just use the application software; they are not responsible for any maintenance or management of that software. | The cloud provider is responsible for the provision, management, and maintenance of the application software. |

### Describe the differences between public, private and hybrid cloud models
#### describe public cloud
#### describe private cloud
#### describe hybrid cloud
#### compare and contrast the three different cloud models

## Understand core Azure services (30-35%)

### Understand the core Azure architectural components
#### describe Regions•describe Availability Zones
#### describe Resource Groups
#### describe Azure Resource Manager
#### describe the benefits and usage of core Azure architectural components

### Describe some of the core products available in Azure
#### describe products available for Compute such as Virtual Machines, Virtual Machine Scale Sets, App Service Functions, Azure Container Instances (ACI) and Azure Kubernetes Service (AKS)
#### describe products available for Networking such as Virtual Network, Load Balancer, VPN Gateway, Application Gateway and Content Delivery Network
#### describe products available for Storage such as Blob Storage, Disk Storage, File Storage, and Archive Storage
#### describe products available for Databases such as Cosmos DB, Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database Migration service
#### describe the Azure Marketplace and its usage scenarios

### Describe some of the solutions available on Azure
#### describe Internet of Things (IoT) and products that are available for IoT on Azure such as IoT Hub and IoT Central
#### describe Big Data and Analytics and products that are available for Big Data and Analytics such as SQL Data Warehouse, HDInsight, and Azure Databricks
#### describe Artificial Intelligence (AI) and products that are available for AI such as Azure Machine Learning Service and Studio
#### describe Serverless computing and Azure products that are available for serverless computing such as Azure Functions, Logic Apps, and Event Grid
#### describe DevOps solutions available on Azure such as Azure DevOps and Azure DevTest Labs
#### describe the benefits and outcomes of using Azure solutions

### Understand Azure management tools
#### understand Azure tools such as Azure Portal, Azure PowerShell, Azure CLI and Cloud Shell
#### understand Azure Advisor

## Understand security, privacy, compliance, and trust (25-30%)

### Understand securing network connectivity in Azure
#### describe Network Security Groups (NSG)
#### describe Application Security Groups (ASG)
#### describe User Defined Rules (UDR)
#### describe Azure Firewall
#### describe Azure DDoS Protection
#### choose an appropriate Azure security solution

### Describe core Azure Identity services
#### understand the difference between authentication and authorization
#### describe Azure Active Directory
#### describe Azure Multi-Factor Authentication

### Describe security tools and features of Azure
#### describe Azure Security Center
#### understand Azure Security Center usage scenarios
#### describe Key Vault
#### describe Azure Information Protection (AIP)
#### describe AzureAdvanced Threat Protection (ATP)

### Describe Azure governance methodologies
#### describe policies and initiatives with Azure Policy
#### describe Role-Based Access Control (RBAC)
#### describe Locks
#### describe Azure Advisor security assistance
#### describe Azure Blueprints

### Understand monitoring and reporting options in Azure
#### describe Azure Monitor
#### describe Azure Service Health
#### understand the use cases and benefits of Azure Monitor and Azure Service Health

### Understand privacy, compliance and data protection standards in Azure
#### understand industry compliance terms such as GDPR, ISO and NIST
#### understand the Microsoft Privacy Statement
#### describe the Trust center
#### describe the Service Trust Portal
#### describe Compliance Manager
#### determine if Azure is compliant for a business need
#### understand Azure Government cloudservices
#### describe Azure China cloud services

## Understand Azure pricing and support (20-25%)

### Understand Azure subscriptions
#### describe an Azure subscription
#### understand the uses and options with Azure subscriptions such access control and offer types
#### understand subscription management using Management groups

### Understand planning and management of costs
#### understand options for purchasing Azure products and services
#### understand options around Azure Free account
#### understand the factors affecting costs such as resource types, services, locations, ingress and egress traffic
#### understand Zones for billing purposes
#### understand the Pricing calculator
#### understand the Total Cost of Ownership (TCO) calculator
#### understand best practices for minimizing Azure costs such as performing cost analysis, creating spending limits and quotas, using tags to identify cost owners, using Azure reservations and using Azure Advisor recommendations
#### describe Azure Cost Management

### Understand the support options available with Azure
#### understand support plans that are available such as Dev, Standard, Professional Direct and Premier
#### understand how to open a support ticket
#### understand available support channels outside of support plan channels
#### describe the Knowledge Center

### Describe Azure Service Level Agreements (SLAs)
#### describe a Service Level Agreement (SLA)
#### understand Composite SLAs
#### understand how to determine an appropriate SLA for an application

### Understand service lifecycle in Azure
#### understand public and private preview features
#### understand the term General Availability (GA)
#### understand how to monitor feature updates and product changes
