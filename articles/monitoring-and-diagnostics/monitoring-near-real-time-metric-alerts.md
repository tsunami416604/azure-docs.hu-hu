---
title: Újabb metrika riasztásait az Azure-figyelő támogatott források |} Microsoft Docs
description: A támogatási metrikák és a naplókat a közel valós idejű metrika riasztásokat az újabb Azure hivatkozás.
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
ms.date: 04/27/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: c4a4a82eedc41b7690af005faecc1505257183ab
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="newer-metric-alerts-for-azure-services-in-the-azure-portal"></a>Azure-szolgáltatásokat az Azure portálon újabb metrika riasztásai
Azure figyelő mostantól támogatja az Új riasztási típust. Az újabb riasztások Miben különböznek a [klasszikus metrika riasztások](insights-alerts-portal.md) néhány módon:

- **Továbbfejlesztett késés**: újabb metrika riasztások egy percenként gyakorisággal futtatható. Régebbi metrika riasztások, 5 perces gyakorisággal végezni. Napló riasztásokat továbbra is fennáll a hosszabb, mint 1 perces késleltetési idő miatt tölti be a naplók vesz igénybe. 
- **A többdimenziós metrikák támogatása**: figyelmeztetik a dimenzionális mérőszámokat, hogy lehetővé teszi a figyelheti egy csak a metrika az érdekes szegmens. 
- **Metrika feltételek teljesebb körű vezérlése**: gazdagabb riasztási szabályokat adhat meg. Az újabb riasztások a maximális, minimális, átlagos, és az összes érték a mérőszámok figyelésére alkalmas. 
- **Egyszerre több metrikák figyelését**: figyelheti a (jelenleg legfeljebb két metrikák) kezelhető egyetlen szabállyal több metrikákat. Riasztást vált ki, ha mindkét metrikák megsértik a megfelelő küszöbértéket a megadott időszakban. 
- **Értesítési rendszer jobb**: minden újabb riasztás használja [művelet csoportok](monitoring-action-groups.md), amely megnevezett értesítések és a műveletek a több riasztás is használhatnak. Klasszikus metrika riasztások és értesítések. Naplóelemzési régebbi. ne használjon művelet csoportok. 
- **A naplók metrikák** (korlátozott nyilvános előzetes verzió): adatok, amelyek Log Analytics mostantól napló kicsomagolja és Azure figyelő metrikák alakítja át és más metrikákkal hasonlóan a majd riasztást. 

Az Azure portál egy újabb metrika riasztás létrehozása, lásd: [riasztási szabályt létrehozni az Azure portálon](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal). A létrehozás után kezelheti a riasztás ismertetett lépések segítségével [a riasztásokat az Azure portálon](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).


## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, a parancssori felület, a többi támogatja
Jelenleg csak az Azure-portálon, létrehozhat újabb metrika riasztások [REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricalerts/createorupdate) vagy [Resource Manager-sablonok](monitoring-create-metric-alerts-with-templates.md). PowerShell-lel újabb riasztások konfigurálásának a támogatását, és az az Azure parancssori felület (Azure CLI 2.0) hamarosan.

## <a name="metrics-and-dimensions-supported"></a>Metrikák és a támogatott méretek
Újabb metrika riasztások támogatja a dimenziók metrikák vonatkozó riasztások elküldésére. A metrika a megfelelő szintre szűréséhez használja a dimenziók is. Az összes támogatott metrikák mellett alkalmazható dimenziók felfedezte, és a ábrázolt [Azure figyelője – Metrikaböngésző (előzetes verzió)](monitoring-metric-charts.md).

Az Azure figyelő metrika források támogatja az újabb riasztások teljes listája itt található:

|Erőforrás típusa  |Támogatott méretek  | Voltak elérhetők metrikák|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Igen        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Igen   | [Automation-fiók](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | –| [Batch-fiókok](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    –     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    –     | [Virtuális gépek](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   –      |[Virtuálisgép-méretezési csoportok](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Igen| [Tárolócsoportok](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.DataFactory/datafactories| Igen| [Adat-előállítók 1-es verzió](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Igen     |[Adat-előállítók V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   –      |[A MySQL adatbázis](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    –     | [PostgreSQL DB](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Igen      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nem | [Tárolók](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     –    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    –     | [Alkalmazásátjárót](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | –| [DNS-zónák](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|(Csak a Standard termékváltozat) Microsoft.Network/loadBalancers| Igen| [Terheléselosztók](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  –       |[Nyilvános IP-cím Addreses](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | – | [Kapacitás](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   –      |[Keresési szolgáltatások](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Igen       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Igen     | [Tárfiókok](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Igen    | [BLOB-szolgáltatások](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [Fájlszolgáltatások](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [szolgáltatások várólistára](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) és [Table szolgáltatások](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  –       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    –     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (előzetes verzió) | Igen|[Napló Analytics munkaterületek](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>Napló Analytics naplók és riasztások metrikák 

Is használhatja, naplók Preview metrikák részeként metrikák kibontott népszerű Naplóelemzési naplók újabb metrika riasztások.  
- [Teljesítményszámlálók](../log-analytics/log-analytics-data-sources-performance-counters.md) Windows és Linux-gépekhez
- [Szívverés-rekordok az ügynök állapota](../operations-management-suite/oms-solution-agenthealth.md)
- [Frissítéskezelés](../operations-management-suite/oms-solution-update-management.md) rekordok
 
> [!NOTE]
> Adott metrika és/vagy a dimenzió csak akkor jelenik meg van-e az adatok választott időszak. USA keleti régiója, Nyugat középső Régiójában és Nyugat-Európában munkaterületeket az ügyfelek, akik az előzetes feliratkozott a fenti metrikák érhetők el. Ha azt szeretné, ez az előzetes kiadás egy részét, jelentkezzen [a felmérés](https://aka.ms/MetricLogPreview).

Az alábbi lista a Naplóelemzési napló alapú metrika források támogatott:

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
| Fogadott Average_Bytes/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
|  Küldött Average_Bytes/mp    |     Igen – számítógép, ObjectName, InstanceName, Számláló_elérési_útja & SourceSystem    |   Windows teljesítményszámláló      |
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



## <a name="payload-schema"></a>Hasznos séma

A POST műveletet tartalmaz a következő JSON-adattartalmat és séma közelében újabb metrika riasztást küld, ha egy megfelelően konfigurált összes [művelet csoport](monitoring-action-groups.md) szolgál:

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
