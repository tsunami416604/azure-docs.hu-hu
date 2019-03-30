---
title: Metrikákhoz kapcsolódó riasztások az Azure monitorban támogatott erőforrások
description: Referencia a támogatási metrikákat és naplókat a további metrikákhoz kapcsolódó riasztások az Azure monitorban
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 69de0f34f735c30fbd99b3266dc0151128fb73eb
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669316"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Metrikákhoz kapcsolódó riasztások az Azure monitorban támogatott erőforrások

Az Azure Monitor mostantól támogatja a [új metrika riasztástípus](../../azure-monitor/platform/alerts-overview.md) amely jelentős előnyökkel jár a korábbi keresztül [klasszikus metrikariasztásokat](../../azure-monitor/platform/alerts-classic.overview.md). Metrikák érhetők el a [az Azure-szolgáltatások nagy lista](../../azure-monitor/platform/metrics-supported.md). Az újabb riasztások az erőforrástípus (növekvő) részét támogatja. Ez a cikk adott részére sorolja fel.

Használhatja a újabb metrikákhoz kapcsolódó riasztások a népszerű Log Analytics naplók, metrikák, ki kell olvasni. További információkért tekintse meg [metrika riasztások naplók](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, REST-támogatással
Jelenleg csak az Azure Portalon létrehozhat újabb metrikákhoz kapcsolódó riasztások [REST API-val](https://docs.microsoft.com/rest/api/monitor/metricalerts/), vagy [Resource Manager-sablonok](../../azure-monitor/platform/alerts-metric-create-templates.md). Konfigurálása a PowerShell és az Azure CLI 2.0-s és újabb verziók használata újabb riasztások támogatása hamarosan elérhető lesz.

## <a name="metrics-and-dimensions-supported"></a>Metrikák és a támogatott méretek
Újabb metrikákhoz kapcsolódó riasztások támogatja a dimenziók használó metrikákhoz riasztásokat. Dimenziók segítségével szűrheti a metrika a megfelelő szintre. Az összes támogatott mérőszámok együtt alkalmazható dimenziók fel és az ábrázolt [Azure Monitor - Metrikaböngésző](../../azure-monitor/platform/metrics-charts.md).

A következő teljes listája megtalálható az Azure monitor metrika források által az újabb riasztások támogatott:

|Erőforrás típusa  |Támogatott méretek  | Rendelkezésre álló metrikák|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Igen        | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Igen   | [Az Automation-fiókok](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | –| [Batch-fiókok](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    –     |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    –     | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    –     | [Virtuális gépek](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   –      |[A Virtual machine scale sets](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Igen| [Tárolócsoportok](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Igen | [Felügyelt fürtöket](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Igen| [A Data Factoryk V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Igen     |[Data Factoryk v2-ben](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   –      |[DB, MySQL-hez](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    –     | [DB for postgresql-hez](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs    | –     |[Az IoT Hub-metrikák](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)
|Microsoft.Devices/provisioningServices    | Igen     |[DPS-metrikák](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)
|Microsoft.EventHub/namespaces     |  Igen      |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nem | [tárolók](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     –    |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    –     | [Application Gateway-átjárók](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | –| [DNS-zónák](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | – |  [Express Route-Kapcsolatcsoportok](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (csak a Standard termékváltozatok)| Igen| [Terheléselosztók](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  –       |[Nyilvános IP-címek](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Igen | [Traffic Manager-profilok](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Igen|[Log Analytics-munkaterületek](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.PowerBIDedicated/capacities | – | [Kapacitások](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   –      |[Keresési szolgáltatások](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Igen       |[Szolgáltatásbusz](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Igen     | [Tárfiókok](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Igen    | [BLOB szolgáltatás](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Fájlszolgáltatások](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [Queue szolgáltatások](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) és [Table szolgáltatások](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  –       | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Igen | [App Service-csomagok](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Igen | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) és [funkciók](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | Igen | [Az App Service-tárolóhely](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|


## <a name="payload-schema"></a>Adattartalom-séma

A POST művelet a következő JSON-adattartalom és séma tartalmazza a közelében újabb metrikákhoz kapcsolódó riasztások, amikor egy megfelelően konfigurált összes [műveletcsoport](../../azure-monitor/platform/action-groups.md) szolgál:

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
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
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

* További információ az új [élmény riasztások](../../azure-monitor/platform/alerts-overview.md).
* Ismerje meg [naplóriasztások az Azure-ban](../../azure-monitor/platform/alerts-unified-log.md).
* Ismerje meg [riasztások az Azure-ban](../../azure-monitor/platform/alerts-overview.md).
