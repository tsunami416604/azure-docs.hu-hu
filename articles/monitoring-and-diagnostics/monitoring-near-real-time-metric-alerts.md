---
title: Metrikákhoz kapcsolódó riasztások az Azure monitorban támogatott erőforrások
description: Referencia a támogatási metrikákat és naplókat a további metrikákhoz kapcsolódó riasztások az Azure monitorban
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 102f087f31aa304aaaee49e0e39ff8c6874127b4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966796"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Metrikákhoz kapcsolódó riasztások az Azure monitorban támogatott erőforrások

Az Azure Monitor mostantól támogatja a [új metrika riasztástípus](monitoring-overview-unified-alerts.md) amely jelentős előnyökkel jár a korábbi keresztül [klasszikus metrikariasztásokat](insights-alerts-portal.md). Metrikák érhetők el a [az Azure-szolgáltatások nagy lista](monitoring-supported-metrics.md). Az újabb riasztások az erőforrástípus (növekvő) részét támogatja. Ez a cikk adott részére sorolja fel.

A népszerű Log Analytics-naplók, metrikák, naplók, metrikák részeként kinyert is használhatja újabb metrikákhoz kapcsolódó riasztások 
- [Teljesítményszámlálók](../log-analytics/log-analytics-data-sources-performance-counters.md) Windows és Linux rendszerű gépek
- [Szívverés rekordok az ügynök állapota](../operations-management-suite/oms-solution-agenthealth.md)
- [Frissítéskezelés](../operations-management-suite/oms-solution-update-management.md) rekordok
- [Eseményadatok](../log-analytics/log-analytics-data-sources-windows-events.md) naplók
 
> [!NOTE]
> Adott metrika és/vagy a dimenzió csak megjelenik létezik-e adatokat, a választott időszakban. Ezek a metrikák az Azure Log Analytics-munkaterületek az USA keleti RÉGIÓJA, USA Középnyugati Régiójában és Nyugat-Európa rendelkező ügyfelek számára érhetők el. A Log Analytics mérőszámainak jelenleg nyilvános előzetes verzióban érhető el, és változhat.

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, REST-támogatással
Jelenleg csak az Azure Portalon létrehozhat újabb metrikákhoz kapcsolódó riasztások [REST API-val](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate) vagy [Resource Manager-sablonok](monitoring-create-metric-alerts-with-templates.md). Konfigurálása a PowerShell és az Azure CLI 2.0-s és újabb verziók használata újabb riasztások támogatása hamarosan elérhető lesz.

## <a name="metrics-and-dimensions-supported"></a>Metrikák és a támogatott méretek
Újabb metrikákhoz kapcsolódó riasztások támogatja a dimenziók használó metrikákhoz riasztásokat. Dimenziók segítségével szűrheti a metrika a megfelelő szintre. Az összes támogatott mérőszámok együtt alkalmazható dimenziók fel és az ábrázolt [Azure Monitor - metrikák Explorer (előzetes verzió)](monitoring-metric-charts.md).

A következő teljes listája megtalálható az Azure monitor metrika források által az újabb riasztások támogatott:

|Erőforrás típusa  |Támogatott méretek  | Rendelkezésre álló metrikák|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Igen        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Igen   | [Az Automation-fiókok](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | –| [Batch-fiókok](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    –     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    –     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    –     | [Virtuális gépek](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   –      |[A Virtual Machine scale sets](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Igen| [Tárolócsoportok](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Igen | [Felügyelt fürtöket](monitoring-supported-metrics.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Igen| [A Data Factoryk V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Igen     |[Data Factoryk v2-ben](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   –      |[DB, MySQL-hez](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    –     | [DB for postgresql-hez](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Igen      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nem | [tárolók](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     –    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    –     | [Az Application Gateway átjárók](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/expressRouteCircuits | – |  [Express Route-Kapcsolatcsoportok](monitoring-supported-metrics.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/dnsZones | –| [DNS-zónák](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (csak a Standard termékváltozatok)| Igen| [Terheléselosztók](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  –       |[Nyilvános IP-cím Addreses](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | – | [Kapacitások](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Network/trafficManagerProfiles | Igen | [Traffic Manager-profilok](monitoring-supported-metrics.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.Search/searchServices     |   –      |[Keresési szolgáltatások](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Igen       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Igen     | [Tárfiókok](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Igen    | [BLOB szolgáltatás](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [Fájlszolgáltatások](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [Queue szolgáltatások](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) és [Table szolgáltatások](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  –       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Igen | [App Service-csomagok](monitoring-supported-metrics.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Igen | [App Services](monitoring-supported-metrics.md#microsoftwebsites-excluding-functions) és [funkciók](monitoring-supported-metrics.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | Igen | [Az App Service-tárolóhely](monitoring-supported-metrics.md#microsoftwebsitesslots)|
|Microsoft.OperationalInsights/workspaces| Igen|[Log Analytics-munkaterületek](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces)|



## <a name="payload-schema"></a>Adattartalom-séma

A POST művelet a következő JSON-adattartalom és séma tartalmazza a közelében újabb metrikákhoz kapcsolódó riasztások, amikor egy megfelelően konfigurált összes [műveletcsoport](monitoring-action-groups.md) szolgál:

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
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
          "metricValue": 1.0
        },
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

* További információ az új [élmény riasztások](monitoring-overview-unified-alerts.md).
* Ismerje meg [naplóriasztások az Azure-ban](monitor-alerts-unified-log.md).
* Ismerje meg [riasztások az Azure-ban](monitoring-overview-alerts.md).
