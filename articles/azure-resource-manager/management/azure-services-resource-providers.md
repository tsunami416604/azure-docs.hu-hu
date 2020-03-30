---
title: Erőforrás-szolgáltatók az Azure-szolgáltatások szerint
description: Felsorolja az Azure Resource Manager összes erőforrás-szolgáltatónévterét, és megjeleníti az azure-szolgáltatást a névtérhez.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 9cf18a1e4c0dd40ae9106d32547ba4795ea9fbb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472962"
---
# <a name="resource-providers-for-azure-services"></a>Az Azure-szolgáltatások erőforrás-szolgáltatói

Ez a cikk bemutatja, hogyan erőforrás-szolgáltató névterek leképezése az Azure-szolgáltatásokhoz.

## <a name="match-resource-provider-to-service"></a>Erőforrás-szolgáltató egyeztetése a szolgáltatással

| Erőforrásszolgáltató névtere | Azure-szolgáltatás |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | Core |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [API Management](../../api-management/index.yml) |
| Microsoft.AppConfiguration | Core |
| Microsoft.AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Azure tanúsítványszolgáltatás |
| Microsoft.Authorization | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [Automatizálás](../../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft.AzureData | SQL Server rendszerleíró adatbázis |
| Microsoft.AzureStack | Core |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft.Számlázás | [Költségkezelés és számlázás](/azure/billing/) |
| Microsoft.BingMaps | [Bing Térképek](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure Blockchain Service](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Azure Blueprints](/azure/governance/blueprints/) |
| Microsoft.BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft.Cache | [Azure Cache for Redis](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | Core |
| Microsoft.Cdn | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft.Tanúsítványregisztráció | [App Service-tanúsítványok](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft.ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Klasszikus üzembe helyezési modell virtuális gép |
| Microsoft.ClassicInfrastructureMigrate | Klasszikus üzembe helyezési modell áttelepítése |
| Microsoft.ClassicNetwork | Klasszikus üzembe helyezési modell virtuális hálózat |
| Microsoft.ClassicStorage | Klasszikus üzembe helyezési modell tárolása |
| Microsoft.ClassicElőfizetés | Klasszikus üzemi modell |
| Microsoft.CognitiveServices | [Kognitív szolgáltatások](/azure/cognitive-services/) |
| Microsoft.Commerce | Core |
| Microsoft.Compute | [Virtuális gépek](/azure/virtual-machines/)<br />[Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/) |
| Microsoft.Fogyasztás | [Cost Management](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Tárolópéldányok](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Tároló beállításjegyzéke](/azure/container-registry/) |
| Microsoft.ContainerService | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft.CostManagement | [Cost Management](/azure/cost-management/) |
| Microsoft.CostManagementExports | [Cost Management](/azure/cost-management/) |
| Microsoft.CustomerLockbox | Ügyfélszéf a Microsoft Azure-hoz |
| Microsoft.CustomProviders | [Azure Custom Providers](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Data Catalog](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake analitikai szolgáltatás](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [2. generációs Azure Data Lake Storage](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Azure Database Migration Service](/azure/dms/) |
| Microsoft.DataShare | [Azure Data Share](/azure/data-share/) |
| Microsoft.DBforMariaDB | [Azure Database for MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Azure Database for MySQL](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Azure Database for PostgreSQL](/azure/postgresql/) |
| Microsoft.DesktopVirtualization | [Windows Virtual Desktop](/azure/virtual-desktop/) |
| Microsoft.DeploymentManager | [Azure-telepítéskezelő](../templates/deployment-manager-overview.md) |
| Microsoft.Devices | [Azure IoT Hub](/azure/iot-hub/)<br />[Azure IoT Hub Device Provisioning Service](/azure/iot-dps/) |
| Microsoft.DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.DigitalTwins | [Azure Digital Twins](../../digital-twins/about-digital-twins.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [APP SERVICE](/azure/app-service/) |
| Microsoft.EnterpriseKnowledgeGraph | Vállalati tudásgrafikon |
| Microsoft.EventGrid | [Event Grid](/azure/event-grid/) |
| Microsoft.EventHub | [Eseményközpontok](../../event-hubs/index.yml) |
| Microsoft.Szolgáltatások | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestKonfiguráció | [Azure szabályzat](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA on Azure Large Instances](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Azure Dedicated HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [Azure API for FHIR](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft.ImportExport | [Azure Import/Export](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.KeyVault | [Key Vault](../../key-vault/index.yml) |
| Microsoft.Kubernetes | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft.Kusto | [Azure Data Explorer](../../data-explorer/index.yml) |
| Microsoft.LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft.Karbantartás | [Azure karbantartás](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedServices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft.Management | [Felügyeleti csoportok](/azure/governance/management-groups/) |
| Microsoft.Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft.Piactér | Core |
| Microsoft.MarketplaceApps | Core |
| Microsoft.MarketplaceRendelés | Core |
| Microsoft.Media | [Médiaszolgáltatások](../../media-services/index.yml) |
| Microsoft.Áttelepítés | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft.MixedReality | [Azure Spatial Anchors](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](/azure/bastion/)<br />[Azure DDoS védelem](../../virtual-network/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[Terheléselosztó](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Forgalomkezelő](../../traffic-manager/index.yml)<br />[Virtuális hálózat](../../virtual-network/index.yml)<br />[Virtuális WAN](../../virtual-wan/index.yml)<br />[VPN-átjáró](../../vpn-gateway/index.yml)<br /> |
| Microsoft.NotificationHubs | [Értesítési központ](../../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.Társviszony-létesítés | [Azure-társviszony-létesítési szolgáltatás](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure szabályzat](../../governance/policy/index.yml) |
| Microsoft.Portal | [Azure-portál](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Az Azure webhely helyreállítása](../../site-recovery/index.yml) |
| Microsoft.RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Azure Relay](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Azure Resource Graph](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | [Azure Service Health](../../service-health/index.yml)|
| Microsoft.Resources | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | Core |
| Microsoft.Scheduler | [Scheduler](/azure/scheduler/) |
| Microsoft.Keresés | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft.Security | [Security Center](../../security-center/index.yml) |
| Microsoft.SecurityInsights | [Azure Sentinel](/azure/sentinel/) |
| Microsoft.SerialConsole | [Azure soros konzol windowshoz](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Azure SignalR szolgáltatás](../../azure-signalr/index.yml) |
| Microsoft.SoftwarePlan | Licenc |
| Microsoft.Megoldások | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../sql-database/index.yml)<br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [SQL Server az Azure Virtual Machines szolgáltatásban](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [Tárterület](../../storage/index.yml) |
| Microsoft.StorageCache | [Azure HPC Cache](/azure/hpc-cache/) |
| Microsoft.StorageSync | [Tárterület](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft.Subscription | Core |
| microsoft.support | Core |
| Microsoft.Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft.VirtualMachineImages | [Azure Image Builder](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft.VMwareCloudSimple | [Azure VMware Solution by CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [APP SERVICE](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft.WindowsIoT | [Windows 10 IoT alapvető szolgáltatások](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadFigyelő | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="next-steps"></a>További lépések

Az erőforrás-szolgáltatókról az [Azure-erőforrás-szolgáltatókról és -típusokról](resource-providers-and-types.md) talál további információt.
