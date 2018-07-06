---
title: Az Azure Monitor újabb metrikákhoz kapcsolódó riasztások támogatott erőforrások
description: Támogatási metrikákat és naplókat közel valós idejű metrikákhoz kapcsolódó riasztások újabb Azure-hivatkozás.
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 01c0b5897ab47a2a5091646aed1977779cf0234c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868028"
---
# <a name="supported-metrics-and-creation-methods-for-new-metric-alerts"></a>Új metrikákhoz kapcsolódó riasztások támogatott mérőszámok és létrehozásának módszerei
Az Azure Monitor mostantól támogatja a [új metrika riasztástípus](monitoring-overview-unified-alerts.md) amely jelentős előnyökkel jár a korábbi keresztül [klasszikus metrikariasztásokat](insights-alerts-portal.md). A régi riasztások támogatása a [mérőszámok listája nagy](monitoring-supported-metrics.md). Az újabb riasztások egy nagyobb listában (növekvő) részét támogatja. Ez a cikk adott részére sorolja fel. 

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, REST-támogatással
Jelenleg csak az Azure Portalon létrehozhat újabb metrikákhoz kapcsolódó riasztások [REST API-val](https://docs.microsoft.com/en-us/rest/api/monitor/metricalerts/createorupdate) vagy [Resource Manager-sablonok](monitoring-create-metric-alerts-with-templates.md). Konfigurálás a PowerShell és az Azure parancssori felület (Azure CLI 2.0) használatával újabb riasztások támogatása hamarosan elérhető lesz.

## <a name="metrics-and-dimensions-supported"></a>Metrikák és a támogatott méretek
Újabb metrikákhoz kapcsolódó riasztások támogatja a dimenziók használó metrikákhoz riasztásokat. Dimenziók segítségével szűrheti a metrika a megfelelő szintre. Az összes támogatott mérőszámok együtt alkalmazható dimenziók fel és az ábrázolt [Azure Monitor - metrikák Explorer (előzetes verzió)](monitoring-metric-charts.md).

A következő teljes listája megtalálható az Azure monitor metrika források által az újabb riasztások támogatott:

|Erőforrás típusa  |Támogatott méretek  | Rendelkezésre álló metrikák|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Igen        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Igen   | [Az Automation-fiókok](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | –| [Batch-fiókok](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    –     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    –     | [Virtuális gépek](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   –      |[A Virtual Machine scale sets](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Igen| [Tárolócsoportok](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.DataFactory/datafactories| Igen| [A Data Factoryk V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Igen     |[Data Factoryk v2-ben](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   –      |[DB, MySQL-hez](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    –     | [DB for postgresql-hez](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Igen      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nem | [Tárolók](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     –    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    –     | [Az Application Gateway átjárók](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | –| [DNS-zónák](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (csak a Standard termékváltozatok)| Igen| [Terheléselosztók](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  –       |[Nyilvános IP-cím Addreses](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | – | [Kapacitások](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   –      |[Keresési szolgáltatások](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Igen       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Igen     | [Tárfiókok](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Igen    | [BLOB szolgáltatás](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [Fájlszolgáltatások](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [Queue szolgáltatások](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) és [Table szolgáltatások](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  –       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    –     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (előzetes verzió) | Igen|[Log Analytics-munkaterületek](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>Log Analytics naplók és mérőszámok riasztások 

Újabb metrikákhoz kapcsolódó riasztások a népszerű Log Analytics naplók, metrikák Tevékenységnaplók előzetes verziójából származó részeként mérőszámokként kinyert is használható.  
- [Teljesítményszámlálók](../log-analytics/log-analytics-data-sources-performance-counters.md) Windows és Linux rendszerű gépek
- [Szívverés rekordok az ügynök állapota](../operations-management-suite/oms-solution-agenthealth.md)
- [Frissítéskezelés](../operations-management-suite/oms-solution-update-management.md) rekordok
 
> [!NOTE]
> Adott metrika és/vagy a dimenzió csak megjelenik létezik-e adatokat, a választott időszakban. A munkaterületeket, USA keleti RÉGIÓJA, USA Középnyugati Régiójában és Nyugat-Európa rendelkező ügyfelek, akik az előzetes verzió feliratkozott a metrikák érhetők el. Ha szeretné az előzetes verzió része, Regisztrálás [a felmérés](https://aka.ms/MetricLogPreview).

Az alábbi lista a Log Analytics-log-alapú metrika források támogatják:

Metrika neve/részletei  |Támogatott méretek  | Napló típusa  |
|---------|---------|---------|
|Average_Avg. Lemez mp/Olvasás     |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_Avg. Lemez mp/írás     |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_Current Lemezvárólista hossza   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_Disk/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_Disk átvitel/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|   Average_ % szabad terület    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_Available (MB)     |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_ véglegesített % bájt használatban    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Fogadott Average_Bytes/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|  Average_Bytes küldött/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|  Average_Bytes összes/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|  Average_ processzoridő    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|   Average_Processor várólistájának hossza    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|   Average_ % szabad Inode-OK   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_ % szabad terület   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Foglalt Inode-OK Average_ %  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Foglalt hely Average_ %   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Disk olvasott bájt/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Disk/mp |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Disk átvitel/mp |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Disk Zapsané Bajty/s   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Disk Lemezírások/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Free (MB) |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Logical bájt/mp |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_ rendelkezésre álló memória % |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_ rendelkezésre álló Lapozóterület % |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Foglalt memória Average_ %  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Foglalt Lapozóterület Average_ %  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Available álló memória    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Available lapozási memória  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Page/mp |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Page Lemezírások/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Pages/sec  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Used álló Lapozóterület |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Used memória (MB) |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Total átvitt bájtok    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Total fogadott bájtok száma   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Total bájtok    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Átvitt Average_Total csomagok  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Fogadott Average_Total csomag |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Total Rx hibák    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Total Tx hibák    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Total ütközések   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Avg. Lemez mp/Olvasás |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Avg. Lemez mp/átvitel |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Avg. Lemez mp/írás    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Physical bájt/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Védett módú Average_Pct    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Felhasználói idő Average_Pct  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Used memória KB |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    A megosztott memória Average_Virtual  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_ DPC idő % |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_ inaktivitási idő %    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_ megszakítási idő %   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_ % IO várakozási idő |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_ %-ban a processzoron idő    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Védett módú Average_ %  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_ processzoridő   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Felhasználói idő Average_ %    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Free fizikai memória   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Free hely a Lapozófájlokban |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Free virtuális memória    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Processes  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    A Lapozófájlokban tárolt Average_Size    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Uptime |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Average_Users  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux-teljesítményszámláló      |
|    Szívverés  |     Igen – számítógép, OSType, verzióját és SourceComputerId    |   Szívverés rekordok |
|    Frissítés |     Igen – számítógép, a termék, a besorolás, a UpdateState, nem kötelező & jóváhagyott    |   Frissítéskezelés |



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
