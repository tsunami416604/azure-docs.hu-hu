---
title: "Majdnem valós idejű metrika riasztások az Azure-figyelő |} Microsoft Docs"
description: "Megtudhatja, hogyan közel valós idejű metrika riasztások a figyelheti az Azure-erőforrás metrikáit mérete legyen 1 perces gyakorisággal."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: 
ms.openlocfilehash: 88995b1f3350fe485e28efccc93779ae0a42eb97
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Közel valós idejű metrika riasztások (előzetes verzió)
Az Azure használatát támogatja közel valós idejű metrika riasztások (előzetes verzió) nevű új riasztási típus. Ez a funkció jelenleg nyilvános előzetes verziójához.

Közel valós idejű metrika riasztások eltér a rendszeres metrika riasztások néhány módon:

- **Továbbfejlesztett késés**: közel valós idejű metrika riasztások figyelheti a mérete legyen egy percig gyakorisággal metrika értékek változásait.
- **Metrika feltételek teljesebb körű vezérlése**: gazdagabb riasztási szabályok a közel valós idejű metrika riasztásokat definiálhat. A riasztások a maximális, minimális, átlagos, és az összes érték a mérőszámok figyelésére alkalmas.
- **Metrikák naplókból**: A beérkező népszerű naplóadatok [Naplóelemzési](../log-analytics/log-analytics-overview.md), metrikák Azure monitorra kinyerhetők, és közel valós időben értesítést
- **Egyszerre több metrikák figyelését**: közel valós idejű metrika riasztások több metrikák (jelenleg legfeljebb két metrikák) kezelhető egyetlen szabállyal képes figyelni. Riasztást vált ki, ha mindkét metrikák számítógépeinek a megadott időszakra vonatkozó megsértik a a vonatkozó küszöbértéket.
- **Moduláris értesítési rendszer**: közel valós idejű metrika riasztások használata [művelet csoportok](monitoring-action-groups.md). Művelet csoportokat hozhat létre moduláris műveletek. A riasztási szabályok több művelet csoportok is felhasználhatja.

> [!NOTE]
> A közel valós idejű metrika riasztás jelenleg nyilvános előzetes verziójában van. Mérés naplók funkciókat és *korlátozott* nyilvános előzetes verziójához. A funkció és a felhasználói élmény van változhat.
>

## <a name="metrics-and-dimensions-supported"></a>Metrikák és a támogatott méretek
Közel valós idejű metrika riasztások támogatja a dimenziók metrikák vonatkozó riasztások elküldésére. A metrika a megfelelő szintre szűréséhez használja a dimenziók is. Az összes támogatott metrikák mellett alkalmazható dimenziók felfedezte, és a ábrázolt [Azure figyelője – Metrikaböngésző (előzetes verzió)](monitoring-metric-charts.md).

Az Azure-alapú figyelőt metrika adatforrások által támogatott közel valós idejű metrika riasztások teljes listája itt található:

|Metrika neve/részletei  |Támogatott méretek  |
|---------|---------|
|Microsoft.ApiManagement/service     | Igen        |
|Microsoft.Automation/automationAccounts     |     –    |
|Microsoft.Automation/automationAccounts     |   –      |
|Microsoft.Cache/Redis     |    –     |
|Microsoft.Compute/virtualMachines     |    –     |
|Microsoft.Compute/virtualMachineScaleSets     |   –      |
|Microsoft.DataFactory/factories     |   –      |
|Microsoft.DBforMySQL/servers     |   –      |
|Microsoft.DBforPostgreSQL/servers     |    –     |
|Microsoft.EventHub/namespaces     |   –      |
|Microsoft.Logic/workflows     |     –    |
|Microsoft.Network/applicationGateways     |    –     |
|Microsoft.Network/publicipaddresses     |  –       |
|Microsoft.Search/searchServices     |   –      |
|Microsoft.ServiceBus/namespaces     |  –       |
|Microsoft.Storage/storageAccounts     |    Igen     |
|Microsoft.Storage/storageAccounts/services     |     Igen    |
|Microsoft.StreamAnalytics/streamingjobs     |  –       |
|Microsoft.CognitiveServices/accounts     |    –     |


A naplók, metrikák jelenleg a következő népszerű OMS naplókat támogatja:
- [Teljesítményszámlálók](../log-analytics/log-analytics-data-sources-performance-counters.md) Windows és Linux-gépekhez
- Szívverés-rekordok gépek
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
> Adott metrika és/vagy a dimenzió csak jelenik meg van-e az adatok választott időszak

## <a name="create-a-near-real-time-metric-alert"></a>Riasztás létrehozása, a közel valós idejű metrika
Jelenleg közel valós idejű metrika riasztásokat csak az Azure-portálon hozhat létre. PowerShell Azure parancssori felület (CLI), és az Azure figyelő REST API-k használatával közel valós idejű metrika riasztások konfigurálásának a támogatását hamarosan elérhető.

A felhasználói élmény, a közel valós idejű metrika riasztások létrehozásához át lett helyezve az új **riasztások (előzetes verzió)** lap. Akkor is, ha az aktuális riasztások oldal **hozzáadása közel valós idejű metrika riasztás**, ekkor a **riasztások (előzetes verzió)** lap.

Riasztás létrehozása, a közel valós idejű metrika lásd: [riasztási szabályt létrehozni az Azure portálon](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-near-real-time-metric-alerts"></a>Közel valós idejű metrika riasztások kezelése
Miután létrehozta a közel valós idejű metrika riasztások, a riasztás ismertetett lépések segítségével kezelheti [a riasztásokat az Azure portálon](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

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

* További információ az új [(előzetes verzió) élmény riasztások](monitoring-overview-unified-alerts.md).
* További tudnivalók [riasztások jelentkezzen be Azure riasztások (előzetes verzió)](monitor-alerts-unified-log.md).
* További tudnivalók [értesítések az Azure-ban](monitoring-overview-alerts.md).
