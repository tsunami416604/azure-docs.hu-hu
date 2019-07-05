---
title: Az Azure Resource Manager erőforrás-szolgáltatók Azure-szolgáltatások által
description: Felsorolja az összes erőforrás-szolgáltatói névterek az Azure Resource Manager, és bemutatja az Azure-szolgáltatás a névtér számára.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/25/2019
ms.author: tomfitz
ms.openlocfilehash: 5f5ffbd9b1f2bde08023d1758792fab54c2885f7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442609"
---
# <a name="resource-providers-for-azure-services"></a>Erőforrás-szolgáltatók az Azure-szolgáltatásokhoz

Ez a cikk bemutatja, hogyan erőforrás-szolgáltatói névterek leképezése az Azure-szolgáltatásokat.

## <a name="match-resource-provider-to-service"></a>Service erőforrás-szolgáltató egyezés

| Erőforrás-szolgáltató névtere | Azure-szolgáltatás |
| --------------------------- | ------------- |
| Microsoft.AAD | [Az Azure Active Directory Domain Servicesben](../active-directory-domain-services/index.yml) |
| microsoft.aadiam | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Addons | mag |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure Advisor](../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [Az API Management](../api-management/index.yml) |
| Microsoft.AppConfiguration | mag |
| Microsoft.Authorization | [Azure Resource Manager](index.yml) |
| Microsoft.Automation | [Automatizálás](../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../active-directory-b2c/index.yml) |
| Microsoft.AzureStack | mag |
| Microsoft.Batch | [Batch](../batch/index.yml) |
| Microsoft.Billing | [Billing](/azure/billing/) |
| Microsoft.BingMaps | [A Bing térképek](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.BizTalkServices | [BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md) |
| Microsoft.Blockchain | [Az Azure Blockchain-szolgáltatás](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Az Azure-tervek](/azure/governance/blueprints/) |
| Microsoft.BotService | [Az Azure Bot Service](/azure/bot-service/) |
| Microsoft.Cache | [Azure Cache for Redis](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | mag |
| Microsoft.Cdn | [Content Delivery Network](../cdn/index.yml) |
| Microsoft.CertificateRegistration | [App Service-tanúsítványok](../app-service/web-sites-purchase-ssl-web-site.md) |
| Microsoft.ClassicCompute | Klasszikus üzemi modell virtuális gépek |
| Microsoft.ClassicInfrastructureMigrate | Klasszikus üzemi modellt migrálás |
| Microsoft.ClassicNetwork | Klasszikus üzemi modellt használó virtuális hálózatokhoz |
| Microsoft.ClassicStorage | Klasszikus üzemi modell storage |
| Microsoft.ClassicSubscription | Klasszikus üzemi modell |
| Microsoft.CognitiveServices | [Cognitive Services](/azure/cognitive-services/) |
| Microsoft.Commerce | mag |
| Microsoft.Compute | [Virtuális gépek](/azure/virtual-machines/) |
| Microsoft.Consumption | [A Cost Management](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Container Instances szolgáltatásban](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Tárolóregisztrációs adatbázis](/azure/container-registry/) |
| Microsoft.ContainerService | [Az Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft.ContentModerator | [Azure Content Moderator](../cognitive-services/content-moderator/index.yml) |
| Microsoft.CostManagement | [A Cost Management](/azure/cost-management/) |
| Microsoft.CustomerInsights | Customer Insights |
| Microsoft.CustomerLockbox | Ügyfél kulcstároló, Microsoft Azure |
| Microsoft.CustomProviders | [Az Azure egyéni szolgáltatók](../managed-applications/custom-providers-overview.md) |
| Microsoft.DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Data Box Edge](../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Az Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Data Catalog](/azure/data-catalog/) |
| Microsoft.DataFactory | [A Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [A Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Store](../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Azure Database Migration Service](/azure/dms/) |
| Microsoft.DBforMariaDB | [Azure Database for MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Azure Database for MySQL](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Azure Database for PostgreSQL](/azure/postgresql/) |
| Microsoft.DeploymentManager | [Az Azure Deployment Manager](deployment-manager-overview.md) |
| Microsoft.Devices | [IoT Hub](/azure/iot-hub/)<br />[IoT Hub eszközregisztrációs szolgáltatás](/azure/iot-dps/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [App Service](/azure/app-service/) |
| Microsoft.EnterpriseKnowledgeGraph | Vállalati Tudásbázis Graph |
| Microsoft.EventGrid | [Event Grid](/azure/event-grid/) |
| Microsoft.EventHub | [Event Hubs](../event-hubs/index.yml) |
| Microsoft.Features | [Azure Resource Manager](index.yml) |
| Microsoft.Genomics | [A Microsoft Genomics](/azure/genomics/) |
| Microsoft.GuestConfiguration | [Azure Policy](../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA on Azure](../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Az Azure dedikált HSM](../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [Azure API for FHIR](../healthcare-apis/index.yml) |
| Microsoft.ImportExport | [Az Azure Import/Export](../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.Intune | [Intune-ban](/intune/) |
| Microsoft.IoTCentral | [IoT-központ](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Azure Digital Twins](../digital-twins/index.yml) |
| Microsoft.KeyVault | [Key Vault](../key-vault/index.yml) |
| Microsoft.Kusto | [Azure Data Explorer](../data-explorer/index.yml) |
| Microsoft.LabServices | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.LocationBasedServices | [Azure Maps](../azure-maps/index.yml) |
| Microsoft.LocationServices | mag |
| Microsoft.LogAnalytics | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.Logic | [Logic Apps](../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningCompute | [Machine Learning-szolgáltatás](../machine-learning/service/index.yml) |
| Microsoft.MachineLearningModelManagement | [Machine Learning-szolgáltatás](../machine-learning/service/index.yml) |
| Microsoft.MachineLearningServices | [Machine Learning-szolgáltatás](../machine-learning/service/index.yml) |
| Microsoft.ManagedIdentity | [Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedLab | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.Management | [Felügyeleti csoportok](/azure/governance/management-groups/) |
| Microsoft.Maps | [Azure Maps](../azure-maps/index.yml) |
| Microsoft.Marketplace | mag |
| Microsoft.MarketplaceApps | mag |
| Microsoft.MarketplaceOrdering | mag |
| Microsoft.Media | [Médiaszolgáltatások](../media-services/index.yml) |
| Microsoft.Migrate | [Az Azure Migrate](../migrate/migrate-overview.md) |
| Microsoft.MixedReality | [Az Azure térbeli horgonyok](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp-fájlok](../azure-netapp-files/index.yml) |
| Microsoft.Network | [Virtual Network](../virtual-network/index.yml)<br />[Load Balancer](../load-balancer/index.yml)<br />[Application Gateway](../application-gateway/index.yml)<br />[Azure DNS](../dns/index.yml)<br />[ExpressRoute](../expressroute/index.yml)<br />[VPN Gateway](../vpn-gateway/index.yml)<br />[Traffic Manager](../traffic-manager/index.yml)<br />[A Network Watcher](../network-watcher/index.yml)<br />[Az Azure-tűzfal](../firewall/index.yml)<br />[Az Azure bejárati ajtajának szolgáltatás](../frontdoor/index.yml)<br />[Az Azure megerősített](/azure/bastion/) |
| Microsoft.NotificationHubs | [Értesítési központ](../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Az Azure Migrate](../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../governance/policy/index.yml) |
| Microsoft.Portal | [Azure Portal](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Site Recovery](../site-recovery/index.yml) |
| Microsoft.Relay | [Azure Relay](../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Azure Resource Graph](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | mag |
| Microsoft.Resources | [Azure Resource Manager](index.yml) |
| Microsoft.SaaS | mag |
| Microsoft.Scheduler | [Scheduler](/azure/scheduler/) |
| Microsoft.Search | [Azure Search](../search/index.yml) |
| Microsoft.Security | [A Security Center](../security-center/index.yml) |
| Microsoft.ServiceBus | [Szolgáltatásbusz](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric háló](../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Azure SignalR Service](../azure-signalr/index.yml) |
| Microsoft.SiteRecovery | [Site Recovery](../site-recovery/index.yml) |
| Microsoft.Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../sql-database/index.yml) |
| Microsoft.SqlVirtualMachine | [SQL Server az Azure Virtual Machines szolgáltatásban](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [Storage](../storage/index.yml) |
| Microsoft.StorageSync | [Storage](../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Stream Analytics](../stream-analytics/index.yml) |
| Microsoft.Subscription | mag |
| microsoft.support | mag |
| Microsoft.TimeSeriesInsights | [Time Series Insights](../time-series-insights/index.yml) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft.VMwareCloudSimple | [Azure VMware-megoldás által CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [APP SERVICE](../app-service/index.yml)<br />[Functions](../azure-functions/index.yml) |
| Microsoft.WindowsDefenderATP | [Windows Defender Komplex veszélyforrások elleni védelem](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) |
| Microsoft.WindowsIoT | [Windows 10 IoT Core Services](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure Monitor](../azure-monitor/index.yml) |

## <a name="next-steps"></a>További lépések

Erőforrás-szolgáltatók kapcsolatos további információkért lásd: [Azure-erőforrás-szolgáltatók és típusaik](resource-manager-supported-services.md)
