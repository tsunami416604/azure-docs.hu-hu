---
title: Erőforrás-szolgáltatók az Azure-szolgáltatások által
description: Felsorolja a Azure Resource Manager összes erőforrás-szolgáltatói névterét, és megjeleníti az adott névtérhez tartozó Azure-szolgáltatást.
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 64c1d3c6f30a4f817abbea5001852df3eeeb9327
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109349"
---
# <a name="resource-providers-for-azure-services"></a>Erőforrás-szolgáltatók az Azure-szolgáltatásokhoz

Ez a cikk bemutatja, hogyan képezhetők le az erőforrás-szolgáltatói névterek az Azure-szolgáltatásokhoz.

## <a name="match-resource-provider-to-service"></a>Erőforrás-szolgáltató egyeztetése a szolgáltatással

| Erőforrás-szolgáltatói névtér | Azure-szolgáltatás |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | Core |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [API Management](../../api-management/index.yml) |
| Microsoft.AppConfiguration | Core |
| Microsoft.Attestation | Azure-igazolási szolgáltatás |
| Microsoft.Authorization | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [Automatizálás](../../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft.AzureStack | Core |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft.Billing | [Cost Management és számlázás](/azure/billing/) |
| Microsoft.BingMaps | [Bing Maps](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure Blockchain szolgáltatás](/azure/blockchain/workbench/) |
| Microsoft. Blueprint | [Azure-tervrajzok](/azure/governance/blueprints/) |
| Microsoft. BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft.Cache | [Azure Cache for Redis](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | Core |
| Microsoft.Cdn | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [Tanúsítványok App Service](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Klasszikus üzembe helyezési modell virtuális gép |
| Microsoft. ClassicInfrastructureMigrate | Klasszikus üzembe helyezési modell migrálása |
| Microsoft.ClassicNetwork | Klasszikus üzembe helyezési modell virtuális hálózata |
| Microsoft.ClassicStorage | Klasszikus üzembe helyezési modell tárterülete |
| Microsoft.ClassicSubscription | Klasszikus üzemi modell |
| Microsoft.CognitiveServices | [Cognitive Services](/azure/cognitive-services/) |
| Microsoft.Commerce | Core |
| Microsoft.Compute | [Virtual Machines](/azure/virtual-machines/)<br />[Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/) |
| Microsoft.Consumption | [Cost Management](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Container Instances](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Container Registry](/azure/container-registry/) |
| Microsoft.ContainerService | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft.CostManagement | [Cost Management](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Cost Management](/azure/cost-management/) |
| Microsoft.CustomerLockbox | Microsoft Azure Ügyfélszéf |
| Microsoft.CustomProviders | [Egyéni Azure-szolgáltatók](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Data Box Edge](../../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Data Catalog](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Store](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Azure Database Migration Service](/azure/dms/) |
| Microsoft. DataShare | [Azure-adatmegosztás](/azure/data-share/) |
| Microsoft.DBforMariaDB | [Azure Database for MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Azure Database for MySQL](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Azure Database for PostgreSQL](/azure/postgresql/) |
| Microsoft. DesktopVirtualization | [Windows rendszerű virtuális asztal](/azure/virtual-desktop/) |
| Microsoft.DeploymentManager | [Azure-telepítéskezelő](../templates/deployment-manager-overview.md) |
| Microsoft.Devices | [IoT Hub](/azure/iot-hub/)<br />[IoT Hub eszközregisztrációs szolgáltatás](/azure/iot-dps/) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure dev-helyek](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [App Service](/azure/app-service/) |
| Microsoft.EnterpriseKnowledgeGraph | Enterprise Knowledge Graph |
| Microsoft.EventGrid | [Event Grid](/azure/event-grid/) |
| Microsoft.EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Microsoft.Features | [Azure Resource Manager](../index.yml) |
| Microsoft.Genomics | [Microsoft Genomics](/azure/genomics/) |
| Microsoft.GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA az Azure-ban](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Azure dedikált HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [Azure API a FHIR-hez](../../healthcare-apis/index.yml) |
| Microsoft. HybridCompute | [Azure-ív](../../azure-arc/index.yml) |
| Microsoft. HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft.ImportExport | [Azure import/export](../../storage/common/storage-import-export-service.md) |
| Microsoft. bepillantások | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [IoT Central](/azure/iot-central/) |
| Microsoft. IoTSpaces | [Azure digitális Twins](../../digital-twins/index.yml) |
| Microsoft.KeyVault | [Key Vault](../../key-vault/index.yml) |
| Microsoft.Kusto | [Azure-Adatkezelő](../../data-explorer/index.yml) |
| Microsoft.LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Machine Learning szolgáltatás](../../machine-learning/index.yml) |
| Microsoft.ManagedIdentity | [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. ManagedServices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft.Management | [Management Groups](/azure/governance/management-groups/) |
| Microsoft.Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft. Marketplace | Core |
| Microsoft.MarketplaceApps | Core |
| Microsoft.MarketplaceOrdering | Core |
| Microsoft.Media | [Médiaszolgáltatások](../../media-services/index.yml) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft.MixedReality | [Azure térbeli horgonyok](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Virtuális hálózat](../../virtual-network/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Application Gateway](../../application-gateway/index.yml)<br />[Azure DNS](../../dns/index.yml)<br />[ExpressRoute](../../expressroute/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure-bástya](/azure/bastion/) |
| Microsoft.NotificationHubs | [Értesítési központ](../../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. peering | Microsoft Azure peering szolgáltatás |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.Portal | [Azure Portalra](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Site Recovery](../../site-recovery/index.yml) |
| Microsoft.Relay | [Azure Relay](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Azure-erőforrás gráf](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | [Azure Service Health](../../service-health/index.yml)|
| Microsoft.Resources | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | Core |
| Microsoft.Scheduler | [Scheduler](/azure/scheduler/) |
| Microsoft.Search | [Azure Search](../../search/index.yml) |
| Microsoft.Security | [Security Center](../../security-center/index.yml) |
| Microsoft. SecurityInsights | [Azure Sentinel](/azure/sentinel/) |
| Microsoft. SerialConsole | [Azure soros konzol](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Szolgáltatásbusz](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric rácsvonal](../../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Azure Signaler szolgáltatás](../../azure-signalr/index.yml) |
| Microsoft.SiteRecovery | [Site Recovery](../../site-recovery/index.yml) |
| Microsoft.Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../sql-database/index.yml)<br />[SQL Data Warehouse](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [SQL Server az Azure Virtual Machines szolgáltatásban](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft.StorageCache | [Azure HPC-gyorsítótár](/azure/hpc-cache/) |
| Microsoft.StorageSync | [Storage](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft.Subscription | Core |
| microsoft.support | Core |
| Microsoft.TimeSeriesInsights | [Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft.VirtualMachineImages | [Azure-rendszerkép-szerkesztő](../../virtual-machines/linux/image-builder-overview.md) |
| Microsoft. VisualStudio | [Azure-DevOps](/azure/devops/?view=azure-devops) |
| Microsoft.VMwareCloudSimple | [Azure VMware-megoldás a CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [APP SERVICE](../../app-service/index.yml)<br />[Functions](../../azure-functions/index.yml) |
| Microsoft.WindowsIoT | [Windows 10 IoT Core Services](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="next-steps"></a>Következő lépések

További információ az erőforrás-szolgáltatókkal kapcsolatban: [Azure Resource Providers és types](resource-providers-and-types.md)
