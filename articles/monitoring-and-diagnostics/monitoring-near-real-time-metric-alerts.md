---
title: Majdnem valós idejű metrika riasztások az Azure-figyelő |} Microsoft Docs
description: Megtudhatja, hogyan közel valós idejű metrika riasztások a figyelheti az Azure-erőforrás metrikáit mérete legyen 1 perces gyakorisággal.
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 15b9b0b69f3805b3e3af1d3973fd3a77bea62ab9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="use-the-newer-metric-alerts-for-azure-services-in-azure-portal"></a>Az újabb metrika riasztások használni az Azure-portálon az Azure szolgáltatáshoz
Az Azure használatát támogatja közel valós idejű metrika riasztások nevű új riasztási típus. 

Közel valós idejű metrika riasztások Miben különböznek a [klasszikus metrika riasztások](insights-alerts-portal.md) néhány módon:

- **Továbbfejlesztett késés**: közel valós idejű metrika riasztások egy percenként gyakorisággal futtatható. Régebbi metrika riasztások, 5 perces gyakorisággal végezni.
- **A többdimenziós metrikák támogatása**: figyelmeztetik a dimenzionális mérőszámokat, lehetővé téve a metrika az érdekes szegmens figyelése.
- **Metrika feltételek teljesebb körű vezérlése**: gazdagabb riasztási szabályok a közel valós idejű metrika riasztásokat definiálhat. A riasztások a maximális, minimális, átlagos, és az összes érték a mérőszámok figyelésére alkalmas.
- **Egyszerre több metrikák figyelését**: közel valós idejű metrika riasztások több metrikák (jelenleg legfeljebb két metrikák) kezelhető egyetlen szabállyal képes figyelni. Riasztást vált ki, ha mindkét metrikák számítógépeinek a megadott időszakra vonatkozó megsértik a a vonatkozó küszöbértéket.
- **Moduláris értesítési rendszer**: közel valós idejű metrika riasztások használata [művelet csoportok](monitoring-action-groups.md). Művelet csoportokat hozhat létre moduláris műveletek. A riasztási szabályok több művelet csoportok is felhasználhatja.
- **Metrikák naplókból**: A beérkező népszerű naplóadatok [Naplóelemzési](../log-analytics/log-analytics-overview.md), metrikák Azure monitorra kinyerhetők, és a közel valós időben értesítést.


## <a name="metrics-and-dimensions-supported"></a>Metrikák és a támogatott méretek
Közel valós idejű metrika riasztások támogatja a dimenziók metrikák vonatkozó riasztások elküldésére. A metrika a megfelelő szintre szűréséhez használja a dimenziók is. Az összes támogatott metrikák mellett alkalmazható dimenziók felfedezte, és a ábrázolt [Azure figyelője – Metrikaböngésző (előzetes verzió)](monitoring-metric-charts.md).

Az Azure-alapú figyelőt metrika adatforrások által támogatott közel valós idejű metrika riasztások teljes listája itt található:

|Erőforrás típusa  |Támogatott méretek  | Voltak elérhetők metrikák|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Igen        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Igen   | [Automation-fiók](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | –| [Batch-fiókok](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    –     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    –     | [Virtuális gépek](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   –      |[Virtuálisgép-méretezési csoportok](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.DataFactory/factories     |   Igen     |[Adat-előállítók V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   –      |[A MySQL adatbázis](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    –     | [DB for PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Igen      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.Logic/workflows     |     –    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    –     | [Alkalmazásátjárót](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/publicipaddresses     |  –       |[Nyilvános IP-cím Addreses](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Search/searchServices     |   –      |[Keresési szolgáltatások](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Igen       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Igen     | [Tárfiókok](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Igen    | [BLOB-szolgáltatások](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [Fájlszolgáltatások](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [szolgáltatások várólistára](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) és [Table szolgáltatások](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  –       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    –     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (előzetes verzió) | Igen|[Napló Analytics munkaterületek](#support-for-oms-logs-as-metrics-for-alerting)|


## <a name="create-a-newer-metric-alert"></a>Egy újabb metrika riasztás létrehozása
Jelenleg csak az Azure-portálon vagy REST API újabb metrika riasztásokat hozhat létre. PowerShell, az Azure parancssori felület (CLI) használatával közel valós idejű metrika riasztások konfigurálásának a támogatását hamarosan elérhető.

Az Azure portál egy újabb metrika riasztás létrehozása, lásd: [riasztási szabályt létrehozni az Azure portálon](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-newer-metric-alerts"></a>Újabb metrika riasztások kezelése
Miután létrehozta a közel valós idejű metrika riasztások, a riasztás ismertetett lépések segítségével kezelheti [a riasztásokat az Azure portálon](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="support-for-oms-logs-as-metrics-for-alerting"></a>OMS naplók, mert így metrikák és támogatása

Közel valós idejű metrika riasztások a népszerű OMS-naplók metrikák naplók Preview metrikák részeként, kibontott is használható.  
- [Teljesítményszámlálók](../log-analytics/log-analytics-data-sources-performance-counters.md) Windows és Linux-gépekhez
- [Szívverés-rekordok az ügynök állapota](../operations-management-suite/oms-solution-agenthealth.md)
- [Frissítéskezelés](../operations-management-suite/oms-solution-update-management.md) rekordok

Ez a teljes OMS naplóalapú indexeltnézet metrika olyan adatforrások listáját, amelyek közel valós idejű metrika riasztások támogatottak:

Metrika neve/részletei  |Támogatott méretek  | Napló típusa  |
|---------|---------|---------|
|Average_Avg. Lemez mp/Olvasás     |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_Avg. Lemez mp/írás     |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_Current Lemezvárólista hossza   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_Disk lemezolvasások/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_Disk átvitel/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|   Average_ % szabad terület    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_Available memória (MB)     |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_ % előjegyzett memória    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
| Average_Bytes Received/sec    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|  Average_Bytes Sent/sec    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|  Average_Bytes száma/s    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|  Average_ a processzor kihasználtsága (%)    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|   Average_Processor várólistájának hossza    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|   Average_ % szabad Inode-OK   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_ % szabad terület   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Foglalt Inode-OK Average_ %  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Foglalt hely Average_ %   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Disk olvasott bájt/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Disk lemezolvasások/mp |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Disk átvitel/mp |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Disk írt bájt/mp   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Disk/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Free mérete (MB) |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Logical bájt/mp |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_ rendelkezésre álló memória % |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_ rendelkezésre álló Lapozóterület % |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Foglalt memória Average_ %  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Foglalt Lapozóterület Average_ %  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Available memória MB-ban    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Available álló lapozófájl-kapacitás  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Page lemezolvasások/mp |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Page/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Pages/sec  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Memória (MB) Lapozóterület Average_Used |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Used memória (MB) |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Küldött bájtok Average_Total    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Fogadott bájtok Average_Total   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Total bájt    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Total csomagok  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Fogadott Average_Total csomagok |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Total a Rx hibák    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Total Tx hibák    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Total ütközések   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Avg. Lemez mp/Olvasás |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Avg. Lemez mp/átvitel |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Avg. Lemez mp/írás    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Physical bájt/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Pct védett módú használatának aránya    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Pct felhasználói idő  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Used memória KB |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Virtual megosztott memória  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_ DPC idő % |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_ % üresjárati idő    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Megszakítási idő Average_ %   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_ % IO várakozási idő |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_ futtatásával töltött idő %    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_ % védett módú használatának aránya  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_ a processzor kihasználtsága (%)   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Felhasználói idő Average_ %    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Free fizikai memória   |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Free hely a Lapozófájlokban |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Free virtuális memória    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Processes  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Lapozófájlokban tárolt Average_Size    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Uptime |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Average_Users  |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Linux teljesítményszámláló      |
|    Szívverés  |     Igen – számítógép, OSType, verziót és SourceComputerId    |   Szívverés-rekordok |
|    Frissítés |     Igen – számítógép, a termék, a besorolás, a UpdateState, nem kötelező & jóváhagyott    |   Frissítéskezelés |

> [!NOTE]
> Adott metrika és/vagy a dimenzió csak akkor jelenik meg van-e az adatok választott időszak. USA keleti régiója, Nyugat középső Régiójában és Nyugat-Európában munkaterületeket az ügyfelek, akik az előzetes feliratkozott a fenti metrikák érhetők el. Ha azt szeretné, ez az előzetes kiadás egy részét, jelentkezzen [a felmérés](https://aka.ms/MetricLogPreview).


## <a name="payload-schema"></a>Hasznos séma

A POST műveletet tartalmazza a következő JSON-adattartalmat és a séma összes közel valós idejű metrika riasztást küld, ha megfelelően konfigurált [művelet csoport](monitoring-action-groups.md) szolgál:

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
* További tudnivalók [riasztások jelentkezzen be Azure](monitor-alerts-unified-log.md).
* További tudnivalók [értesítések az Azure-ban](monitoring-overview-alerts.md).
