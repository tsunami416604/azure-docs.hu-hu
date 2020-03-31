---
title: Metrikariasztások támogatott erőforrásai az Azure Monitorban
description: Hivatkozás a támogatási metrikákra és naplókra az Azure Monitorban
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: e4dfc33756a287b6d2f6e41edded3332ca85241d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274930"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Metrikariasztások támogatott erőforrásai az Azure Monitorban

Az Azure Monitor mostantól támogatja az [új metrikariasztási típust,](../../azure-monitor/platform/alerts-overview.md) amely jelentős előnyökkel jár a régebbi [klasszikus metrikariasztásokhoz.](../../azure-monitor/platform/alerts-classic.overview.md) Metrikák érhetők el [az Azure-szolgáltatások nagy listájához.](../../azure-monitor/platform/metrics-supported.md) Az újabb riasztások támogatják az erőforrástípusok (növekvő) részhalmazát. Ez a cikk felsorolja ezt a részhalmazt.

Újabb metrikariasztásokat is használhat a log analytics-munkaterületben metrikaként kinyert népszerű naplóadatokon. További információkért tekintse meg [a Naplók metrikariasztásait.](../../azure-monitor/platform/alerts-metric-logs.md)

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, REST támogatás
Jelenleg csak az Azure Portalon, a REST [API-ban](https://docs.microsoft.com/rest/api/monitor/metricalerts/)vagy az [Erőforrás-kezelő sablonokban](../../azure-monitor/platform/alerts-metric-create-templates.md)hozhat létre újabb metrikariasztásokat. Hamarosan a PowerShell és az Azure CLI 2.0-s és újabb verzióival konfigurálható újabb riasztások használata.

## <a name="metrics-and-dimensions-supported"></a>Támogatott mutatók és dimenziók
Újabb metrikariasztások támogatják a dimenziókat használó metrikák riasztását. A dimenziók segítségével a megfelelő szintre szűrheti a metrikát. Az Azure [Monitor – Metrics Explorer](../../azure-monitor/platform/metrics-charts.md)összes támogatott metrika és a vonatkozó dimenziók felderíthetők és megjeleníthetők.

Az alábbiakban az újabb riasztások által támogatott Azure-figyelő metrikaforrásainak teljes listáját olvashatja:

|Erőforrás típusa  |Támogatott dimenziók |Többerőforrásos riasztások| Elérhető mérőszámok|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Igen| Nem | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.AppPlatform/Tavasz |Nem| Igen|
|Microsoft.Automation/automationAccounts | Igen| Nem | [Automation-fiókok](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/A| Nem | [Batch-fiókok](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Igen| Nem |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic|Nem|Igen|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/blobServices|Nem|Igen|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/fileServices|Nem|Igen|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/queueServices|Nem|Igen|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/tableServices|Nem|Igen| |
|Microsoft.CognitiveServices/fiókok| N/A | Nem | [Kognitív szolgáltatások](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Igen | Igen | [Virtuális gépek](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |N/A | Igen |[Virtuálisgép-méretezési készletek](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Igen| Nem | [Tárolócsoportok](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Igen | Nem | [Felügyelt fürtök](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Igen | Igen | |
|Microsoft.DataFactory/datafactories| Igen| Nem | [Adatgyárak V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/gyárak |Igen | Nem |[Adatgyárak V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DataShare/fiókok |Nem| Igen|
|Microsoft.DBforMySQL/kiszolgálók |N/A| Nem |[DB a MySQL-hez](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/szerverek |N/A | Nem | [DB a PostgreSQL-hez](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | N/A | Nem |[IoT Hub metrikák](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Igen | Nem |[DPS-mérőszámok](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/tartományok|Nem|Igen| |
|Microsoft.EventGrid/témakörök |Igen | Nem |[Eseményrácstémakörök](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Igen| Nem |[Eseményközpontok fürtjei](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/névterek |Igen| Nem |[Eseményközpontok](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nem |Nem |[Tárolók](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/munkafolyamatok |N/A | Nem |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/munkaterületek|Igen| Nem | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |Igen| Nem | [Az Azure NetApp kapacitáskészletei](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes |Igen| Nem | [Azure NetApp-kötetek](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|N/A| Nem |  |
|Microsoft.Network/dnsZones | N/A| Nem | [DNS-zónák](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/A | Nem |[Express Route-kapcsolatcsoportok](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (csak szabványos termékkészletekhez)| Igen| Nem | [Terheléselosztók](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways|Nem|Igen|
|Microsoft.Network/privateEndpoints|Nem|Igen|
|Microsoft.Network/privateLinkServices|Nem|Igen|
|Microsoft.Network/publicipaddresses |N/A | Nem |[Nyilvános IP-címek](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Igen | Nem | [Traffic Manager-profilok](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/munkaterületek| Igen | Nem | [Log Analytics-munkaterületek](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/névterek | Igen | Nem | [Továbbítók](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft.Társviszony-létesítési/társviszony-létesítési szolgáltatások|Nem|Igen|
|Microsoft.PowerBIDedikált/kapacitások | N/A | Nem | [Kapacitások](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |N/A|Nem | [Keresési szolgáltatások](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/névterek |Igen| Nem |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |   Nem | Igen |
|Microsoft.Sql/servers/databases    | Nem | Igen |
|Microsoft.Storage/storageAccounts |Igen | Nem | [Tárfiókok](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Igen| Nem | [Blob Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Fájlszolgáltatások,](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) [Várólista-szolgáltatások](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) és [Táblaszolgáltatások](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |N/A| Nem | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.Microsoft.VMWareCloudSimple/virtualMachines |Igen|Nem |[CloudSimple-virtuálisgépek](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | Igen | Nem | [App Service-környezet többszerepköres készletek](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Igen | Nem | [App Service-környezet munkavégző készletei](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Igen | Nem | [App Szolgáltatási csomagok](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/webhelyek | Igen | Nem | [Alkalmazásszolgáltatások](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) és [-funkciók](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slot | Igen | Nem | [App Service-bővítőhelyek](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Hasznos adatséma

> [!NOTE]
> A [közös riasztási séma](https://aka.ms/commonAlertSchemaDocs)is használható, amely biztosítja azt az előnyt, hogy egyetlen bővíthető és egységes riasztási hasznos adataz Azure Monitor összes riasztási szolgáltatásában, a webhook-integrációk hoz. [Ismerje meg a gyakori riasztási sémadefiníciók.](https://aka.ms/commonAlertSchemaDefinitions)


A POST művelet a következő JSON-tartalom- és sémát tartalmazza az újabb metrikaközeli riasztásokhoz, ha megfelelően konfigurált [műveletcsoportot](../../azure-monitor/platform/action-groups.md) használ:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>További lépések

* További információ az új [riasztások élményéről.](../../azure-monitor/platform/alerts-overview.md)
* Ismerje meg [a naplóriasztásokat az Azure-ban.](../../azure-monitor/platform/alerts-unified-log.md)
* További információ [az Azure-beli riasztásokról.](../../azure-monitor/platform/alerts-overview.md)
