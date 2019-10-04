---
title: A metrikus riasztások által támogatott erőforrások Azure Monitor
description: A metrikus riasztások támogatási mérőszámait és naplóit ismertető Azure Monitor
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 37ef1cda37e1799a4dc488947e6c9ed4c9ad4055
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636188"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>A metrikus riasztások által támogatott erőforrások Azure Monitor

A Azure Monitor mostantól támogatja az [új metrikai riasztás típusát](../../azure-monitor/platform/alerts-overview.md) , amely jelentős előnnyel jár a régebbi [klasszikus metrikai riasztásokkal](../../azure-monitor/platform/alerts-classic.overview.md)szemben. A metrikák az [Azure-szolgáltatások nagyméretű listájához](../../azure-monitor/platform/metrics-supported.md)érhetők el. Az újabb riasztások támogatják az erőforrástípusok (növekvő) részhalmazát. Ez a cikk felsorolja az adott részhalmazt.

Újabb metrikai riasztásokat is használhat a Log Analytics munkaterületen tárolt népszerű naplófájlokban, amelyeket metrikaként Kinyer. További információkért tekintse meg [a naplók metrikai riasztásait](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, REST-támogatás
Jelenleg csak a Azure Portal, [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)vagy [Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md)-sablonokban hozhatók létre újabb metrikai riasztások. Az újabb riasztások a PowerShell és az Azure CLI 2,0-es és újabb verzióival való konfigurálásának támogatása hamarosan elérhető lesz.

## <a name="metrics-and-dimensions-supported"></a>A metrikák és a méretek támogatottak
Az újabb metrikai riasztások támogatják a dimenziókat használó mérőszámok riasztásait. Méretek használatával szűrheti a metrikát a megfelelő szintre. Az összes támogatott mérőszám és a vonatkozó dimenziók megtekinthetők és megjeleníthetők [Azure monitor-Metrikaböngészőból](../../azure-monitor/platform/metrics-charts.md).

Itt látható az újabb riasztások által támogatott Azure monitor metrikai források teljes listája:

|Erőforrás típusa  |Támogatott méretek  | Elérhető metrikák|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Igen        | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Igen   | [Automation-fiókok](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | –| [Batch-fiókok](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    –     |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    –     | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    –     | [Virtuális gépek](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   –      |[Virtuálisgép-méretezési csoportok](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Igen| [Tároló csoportok](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Igen | [Felügyelt fürtök](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Igen| [Az adatgyárak v1-es verziója](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Igen     |[Az adatgyár v2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   –      |[A MySQL-hez készült adatbázis](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    –     | [PostgreSQL-hez készült adatbázis](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs    | –     |[IoT Hub metrikák](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)
|Microsoft.Devices/provisioningServices    | Igen     |[DPS-metrikák](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)
|Microsoft.EventHub/namespaces     |  Igen      |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nem | [Boltívek](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     –    |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    –     | [Application Gateway-átjárók](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | –| [DNS-zónák](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | – |  [Express Route-áramkörök](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/loadBalancers (csak standard SKU-hoz)| Igen| [Terheléselosztó](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  –       |[Nyilvános IP-címek](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Igen | [Traffic Manager profilok](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Igen|[Munkaterületek Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.PowerBIDedicated/capacities | – | [Kapacitások](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   –      |[Keresési szolgáltatások](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Igen       |[Szolgáltatásbusz](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Igen     | [Tárfiókok](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Igen    | [Blob Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Fájlszolgáltatások](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [üzenetsor-szolgáltatások](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) és [Table Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  –       | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Igen | [App Service csomagok](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Igen | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) és [függvények](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | Igen | [App Service tárolóhelyek](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|


## <a name="payload-schema"></a>Hasznos adatok sémája

> [!NOTE]
> Használhatja továbbá a Common [Alert sémát](https://aka.ms/commonAlertSchemaDocs)is, amely lehetővé teszi, hogy a webhook-integrációk esetében egyetlen bővíthető és egységesített riasztási adattartalom legyen a Azure monitor összes riasztási szolgáltatásában. [Ismerje meg a riasztási séma általános definícióit.](https://aka.ms/commonAlertSchemaDefinitions)


A POST művelet a következő JSON-adattartalomot és sémát tartalmazza az összes újabb metrikai riasztáshoz, ha egy megfelelő módon konfigurált [műveleti csoport](../../azure-monitor/platform/action-groups.md) van használatban:

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

* További információ az új [riasztási élményről](../../azure-monitor/platform/alerts-overview.md).
* További információ a [log-riasztásokról az Azure-ban](../../azure-monitor/platform/alerts-unified-log.md).
* További információ [Az Azure-](../../azure-monitor/platform/alerts-overview.md)beli riasztásokról.
