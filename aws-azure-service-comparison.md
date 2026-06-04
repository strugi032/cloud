# AWS to Azure Service Comparison

> [!NOTE]
> These are approximate equivalents and not always 1:1 mappings. Services may differ in features, implementation, and pricing models.

| Service Category | AWS Offering | Closest Azure Equivalent | Notes |
| :---- | :---- | :---- | :---- |
| Hierarchical Structure | AWS Accounts | Azure Subscriptions | |
| Hierarchical Structure | AWS Organizational Units (OUs) | Azure Management Groups | |
| Enterprise Governance | AWS Organizations / Control Tower | Azure Management Groups / Azure Policy | |
| Identity Management | IAM | Microsoft Entra ID \+ Azure RBAC | |
| Single Sign-On | IAM Identity Center | Microsoft Entra ID | |
| User Directory | IAM users / groups | Entra users / groups | |
| Secret Storage | Secrets Manager | Azure Key Vault | |
| Configuration Store | SSM Parameter Store | Azure App Configuration / Key Vault | |
| Key Management | AWS KMS | Azure Key Vault Managed HSM / Keys | |
| Hardware Security | CloudHSM | Azure Cloud HSM / Managed HSM | |
| Virtual Servers | EC2 | Azure Virtual Machines | |
| Compute Scaling | Auto Scaling Groups | Virtual Machine Scale Sets | |
| Event-Driven Functions | Lambda | Azure Functions | |
| Application Hosting | Elastic Beanstalk | Azure App Service | |
| Container Management | ECS | Azure Container Apps / AKS | |
| Managed Kubernetes | EKS | AKS | |
| Serverless Container Runtime | Fargate | Azure Container Apps / Azure Container Instances | |
| Batch Processing | AWS Batch | Azure Batch | |
| Simple VPS | Lightsail | Azure VM / App Service | |
| Hybrid cloud | AWS Outposts | Azure Stack / Azure Arc | |
| VMware | VMware Cloud on AWS | Azure VMware Solution | |
| Object storage | S3 | Azure Blob Storage | |
| Block storage | EBS | Azure Managed Disks | |
| File storage | EFS | Azure Files | |
| Windows file storage | FSx for Windows | Azure Files / Azure NetApp Files | |
| Lustre filesystem | FSx for Lustre | Azure Managed Lustre | |
| Archive storage | S3 Glacier | Azure Archive Storage | |
| Backup | AWS Backup | Azure Backup | |
| Disaster recovery | Elastic Disaster Recovery | Azure Site Recovery | |
| CDN | CloudFront | Azure Front Door / Azure CDN | |
| DNS | Route 53 | Azure DNS | |
| Global routing | Route 53 latency routing / Global Accelerator | Azure Front Door / Traffic Manager | |
| VPC | VPC | Virtual Network | |
| Subnets | VPC Subnets | VNet Subnets | |
| Security groups | Security Groups | Network Security Groups | |
| NACLs | Network ACLs | NSG rules / Azure Firewall | |
| NAT | NAT Gateway | Azure NAT Gateway | |
| Private connectivity | PrivateLink | Azure Private Link | |
| Site-to-site VPN | AWS Site-to-Site VPN | Azure VPN Gateway | |
| Dedicated connection | Direct Connect | ExpressRoute | |
| Transit networking | Transit Gateway | Azure Virtual WAN / VNet peering | |
| Load balancer L4 | Network Load Balancer | Azure Load Balancer | |
| Load balancer L7 | Application Load Balancer | Azure Application Gateway | |
| WAF | AWS WAF | Azure Web Application Firewall | |
| DDoS protection | AWS Shield | Azure DDoS Protection | |
| Firewall | AWS Network Firewall | Azure Firewall | |
| API gateway | API Gateway | Azure API Management | |
| Event bus | EventBridge | Azure Event Grid | |
| Queue | SQS | Azure Queue Storage / Service Bus Queue | |
| Pub/Sub | SNS | Azure Service Bus Topic / Event Grid | |
| Streaming | Kinesis | Azure Event Hubs | |
| Kafka | MSK | Azure Event Hubs Kafka endpoint / Confluent on Azure | |
| Workflow | Step Functions | Azure Logic Apps / Durable Functions | |
| Scheduler | EventBridge Scheduler | Azure Logic Apps / Automation / Functions timer | |
| Relational DB | RDS | Azure SQL / Azure Database services | |
| MySQL | RDS MySQL / Aurora MySQL | Azure Database for MySQL | |
| PostgreSQL | RDS PostgreSQL / Aurora PostgreSQL | Azure Database for PostgreSQL | |
| SQL Server | RDS SQL Server | Azure SQL Managed Instance / SQL Server on Azure VM | |
| Oracle | RDS Oracle | Oracle Database@Azure / Oracle on VM | |
| NoSQL key-value | DynamoDB | Azure Cosmos DB | |
| Document DB | DocumentDB | Azure Cosmos DB | |
| Redis | ElastiCache Redis | Azure Managed Redis / Azure Cache for Redis | |
| Memcached | ElastiCache Memcached | No exact managed equivalent; use Redis or self-managed | |
| Data warehouse | Redshift | Azure Synapse Analytics | |
| Data lake | S3 \+ Lake Formation | Azure Data Lake Storage \+ Microsoft Purview | |
| ETL | AWS Glue | Azure Data Factory / Synapse Pipelines | |
| Analytics query | Athena | Azure Synapse Serverless SQL | |
| Big data | EMR | Azure HDInsight / Databricks | |
| Databricks | Databricks on AWS | Azure Databricks | |
| Search | OpenSearch Service | Azure AI Search | |
| AI & Machine Learning | Amazon SageMaker AI | Azure Machine Learning | |
| Management & Monitoring | CloudWatch | Azure Monitor | |
| Infrastructure as Code | CloudFormation | Azure Bicep / ARM Templates | |
| Developer Tools | AWS CodeCommit / CodePipeline | Azure DevOps / GitHub | |
| IoT | IoT Core | Azure IoT Hub | |
