---
title: Erőforrás-szolgáltatók az Azure-szolgáltatások által
description: Felsorolja a Azure Resource Manager összes erőforrás-szolgáltatói névterét, és megjeleníti az adott névtérhez tartozó Azure-szolgáltatást.
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: d4dea1dc5ee46d9be7bbc92166535aa93db46275
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85506803"
---
# <a name="resource-providers-for-azure-services"></a>Erőforrás-szolgáltatók az Azure-szolgáltatásokhoz

Ez a cikk bemutatja, hogyan képezhetők le az erőforrás-szolgáltatói névterek az Azure-szolgáltatásokhoz.

## <a name="match-resource-provider-to-service"></a>Erőforrás-szolgáltató egyeztetése a szolgáltatással

| Erőforrás-szolgáltatói névtér | Azure-szolgáltatás |
| --------------------------- | ------------- |
| Microsoft. HRE | [Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/index.yml) |
| Microsoft. addons | Core |
| Microsoft. ADHybridHealthService<sup>1</sup> | [Azure Active Directory](/azure/active-directory/) |
| Microsoft. Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft. AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft. ApiManagement | [API Management](../../api-management/index.yml) |
| Microsoft. AppConfiguration | [Azure App Configuration](../../azure-app-configuration/index.yml) |
| Microsoft. AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft. igazolás | Azure-igazolási szolgáltatás |
| Microsoft. Authorization<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft. Automation | [Automatizálás](../../automation/index.yml) |
| Microsoft. AutonomousSystems | [Autonóm rendszerek](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft. AVS | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft. AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft. AzureData | SQL Server beállításjegyzék |
| Microsoft. AzureStack | Core |
| Microsoft. AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft. számlázás<sup>1</sup> | [Költségkezelés és számlázás](/azure/billing/) |
| Microsoft. BingMaps | [Bing Térképek](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft. Blockchain | [Azure Blockchain Service](/azure/blockchain/workbench/) |
| Microsoft. BlockchainTokens | [Azure Blockchain Tokens](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft. Blueprint | [Azure Blueprints](/azure/governance/blueprints/) |
| Microsoft. BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft. cache | [Azure Cache for Redis](/azure/azure-cache-for-redis/) |
| Microsoft. Capacity | Core |
| Microsoft. CDN | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft. CertificateRegistration | [Tanúsítványok App Service](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Klasszikus üzembe helyezési modell virtuális gép |
| Microsoft. ClassicInfrastructureMigrate | Klasszikus üzembe helyezési modell migrálása |
| Microsoft. ClassicNetwork | Klasszikus üzembe helyezési modell virtuális hálózata |
| Microsoft. ClassicStorage | Klasszikus üzembe helyezési modell tárterülete |
| Microsoft. ClassicSubscription<sup>1</sup> | Klasszikus üzemi modell |
| Microsoft. CognitiveServices | [Cognitive Services](/azure/cognitive-services/) |
| Microsoft. Commerce<sup>1</sup> | Core |
| Microsoft.Compute | [Virtuális gépek](/azure/virtual-machines/)<br />[Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/) |
| Microsoft.<sup>1</sup> . felhasználás | [Költségkezelés](/azure/cost-management/) |
| Microsoft. ContainerInstance | [Tárolópéldányok](/azure/container-instances/) |
| Microsoft. ContainerRegistry | [Container Registry](/azure/container-registry/) |
| Microsoft. Tárolószolgáltatás | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft. CostManagement<sup>1</sup> | [Költségkezelés](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Költségkezelés](/azure/cost-management/) |
| Microsoft. CustomerLockbox | [Microsoft Azure Ügyfélszéf](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft. CustomProviders | [Azure Custom Providers](../custom-providers/overview.md) |
| Microsoft. DataBox | [Azure Data Box](/azure/databox/) |
| Microsoft. DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft. Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft. DataCatalog | [Data Catalog](/azure/data-catalog/) |
| Microsoft. DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft. DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft. Data Lake Store | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft. DataMigration | [Azure Database Migration Service](/azure/dms/) |
| Microsoft. DataProtection | Adatvédelem |
| Microsoft. DataShare | [Azure Data Share](/azure/data-share/) |
| Microsoft. DBforMariaDB | [Azure Database for MariaDB](/azure/mariadb/) |
| Microsoft. DBforMySQL | [Azure Database for MySQL](/azure/mysql/) |
| Microsoft. DBforPostgreSQL | [Azure Database for PostgreSQL](/azure/postgresql/) |
| Microsoft. DeploymentManager | [Azure-telepítéskezelő](../templates/deployment-manager-overview.md) |
| Microsoft. DesktopVirtualization | [Windows Virtual Desktop](/azure/virtual-desktop/) |
| Microsoft. Devices | [Azure IoT Hub](/azure/iot-hub/)<br />[Azure IoT Hub Device Provisioning Service](/azure/iot-dps/) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft. DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft. segédösszetevője | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. DigitalTwins | [Azure Digital Twins](../../digital-twins/about-digital-twins.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft. DomainRegistration | [App Service](/azure/app-service/) |
| Microsoft. EnterpriseKnowledgeGraph | Enterprise Knowledge Graph |
| Microsoft. EventGrid | [Event Grid](/azure/event-grid/) |
| Microsoft. EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Microsoft. features<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft. GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. HanaOnAzure | [SAP HANA on Azure Large Instances](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft. HardwareSecurityModules | [Azure Dedicated HSM](../../dedicated-hsm/index.yml) |
| Microsoft. HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft. HealthcareApis | [Azure API for FHIR](../../healthcare-apis/index.yml) |
| Microsoft. HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft. HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft. ImportExport | [Azure Import/Export](../../storage/common/storage-import-export-service.md) |
| Microsoft. bepillantások | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. IoTCentral | [Azure IoT Central](/azure/iot-central/) |
| Microsoft. IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft. kulcstartó | [Key Vault](../../key-vault/index.yml) |
| Microsoft. Kubernetes | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft. KubernetesConfiguration | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Microsoft. LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft. MachineLearning | [Machine Learning Studio](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft. Maintenance | [Azure-karbantartás](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft. ManagedIdentity | [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. ManagedServices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft. Management | [Felügyeleti csoportok](/azure/governance/management-groups/) |
| Microsoft. Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft. Marketplace | Core |
| Microsoft. MarketplaceApps | Core |
| Microsoft. MarketplaceOrdering<sup>1</sup> | Core |
| Microsoft. Media | [Media Services](../../media-services/index.yml) |
| Microsoft. Migrálás | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft. MixedReality | [Azure Spatial Anchors](/azure/spatial-anchors/) |
| Microsoft. NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](/azure/bastion/)<br />[Azure DDoS Protection](../../virtual-network/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[Virtual Network](../../virtual-network/index.yml)<br />[Virtuális WAN](../../virtual-wan/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br /> |
| Microsoft. NotificationHubs | [Notification Hubs](../../notification-hubs/index.yml) |
| Microsoft. ObjectStore | Objektum tárolója |
| Microsoft. OffAzure | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft. OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. peering | [Azure Peering Service](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. Portal<sup>1</sup> | [Azure Portal](/azure/azure-portal/) |
| Microsoft. PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft. PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft. PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft. Quantum | [Azure Quantum](https://azure.microsoft.com/services/quantum/) |
| Microsoft. Recoveryservices szolgáltatónál | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft. RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft. Relay | [Azure Relay](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft. ResourceGraph<sup>1</sup> | [Azure Resource Graph](/azure/governance/resource-graph/) |
| Microsoft. ResourceHealth | [Azure Service Health](../../service-health/index.yml) |
| Microsoft. Resources<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft. SaaS | Core |
| Microsoft. Scheduler | [Scheduler](/azure/scheduler/) |
| Microsoft. Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft. Security | [Security Center](../../security-center/index.yml) |
| Microsoft. SecurityInsights | [Azure Sentinel](/azure/sentinel/) |
| Microsoft. SerialConsole<sup>1</sup> | [Windows rendszerhez készült Azure soros konzol](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft. ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft. ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft. Services | Core |
| Microsoft. SignalRService | [Azure SignalR szolgáltatás](../../azure-signalr/index.yml) |
| Microsoft. SoftwarePlan | Licenc |
| Microsoft. Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../azure-sql/database/index.yml)<br /> [Felügyelt Azure SQL-példány](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. SqlVirtualMachine | [SQL Server az Azure Virtual Machines szolgáltatásban](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft. StorageSync | [Storage](../../storage/index.yml) |
| Microsoft. StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft. StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft. előfizetés | Core |
| Microsoft. support<sup>1</sup> | Core |
| Microsoft. szinapszis | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft. token | Jogkivonat |
| Microsoft. VirtualMachineImages | [Azure Image Builder](../../virtual-machines/linux/image-builder-overview.md) |
| Microsoft. VisualStudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft. VMware | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft. VMwareCloudSimple | [Azure VMware Solution by CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft. VSOnline | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft. Web | [APP SERVICE](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft. WindowsESU | Bővített biztonsági frissítések |
| Microsoft. WindowsIoT | [Windows 10 IoT Core Services](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft. WorkloadMonitor<sup>1</sup> | [Azure Monitor](../../azure-monitor/index.yml) |

<sup>1</sup> alapértelmezés szerint regisztrálva

## <a name="next-steps"></a>További lépések

Az erőforrás-szolgáltatókkal kapcsolatos további információkért, beleértve az erőforrás-szolgáltatók regisztrálásának módját, lásd: [Azure erőforrás-szolgáltatók és-típusok](resource-providers-and-types.md)
