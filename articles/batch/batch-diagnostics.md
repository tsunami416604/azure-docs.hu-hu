---
title: Metrikák, riasztások és diagnosztikai naplók – Azure Batch | Microsoft Docs
description: Jegyezze fel, és elemezze a diagnosztikai naplót eseményeire az Azure Batch-fiók erőforrásokat, például a készletek és tevékenységek.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 68d5976a5a79dbde88b7f80b02b39793ffc86de9
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254861"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-metrikák, a riasztások és a naplókat a további diagnosztikai kiértékeléséhez és figyeléséhez

 
Ez a cikk ismerteti, hogyan figyelheti a Batch-fiókokat a [Azure monitor](../azure-monitor/overview.md)szolgáltatásainak használatával. A Azure Monitor a Batch-fiók erőforrásaihoz tartozó [mérőszámokat](../azure-monitor/platform/data-platform-metrics.md) és [diagnosztikai naplókat](../azure-monitor/platform/platform-logs-overview.md) gyűjt. Összegyűjtheti, és számos módon figyelheti a Batch-fiókhoz, és diagnosztizálhatja a problémákat a feldolgozásukhoz. A [metrikai riasztásokat](../azure-monitor/platform/alerts-overview.md) úgy is konfigurálhatja, hogy értesítést kapjon, ha egy metrika eléri a megadott értéket. 

## <a name="batch-metrics"></a>Batch-metrikák

Metrikák az Azure-erőforrások, amelyek használják az Azure Monitor szolgáltatás által kibocsátott (más néven teljesítményszámlálók) Azure telemetriai adatokat. A Batch-fiókokban példa mérőszámok közé tartozik: készlet létrehozása esemény, alacsony prioritású csomópontok száma és a feladat befejezése események. 

Tekintse meg a [támogatott batch-metrikák listáját](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metrikák a következők:

* Minden egyes Batch-fiókban, további konfiguráció nélkül alapértelmezés szerint engedélyezve
* Generált 1 percenként
* Nem maradnak meg automatikusan, de van egy 30 napos gördülő előzményei. A tevékenység-metrikák a diagnosztikai naplózás részeként is megmaradhatnak.

### <a name="view-metrics"></a>Metrikák megtekintése

A Batch-fiók metrikák megtekintése az Azure Portalon. A fiók **áttekintő** lapja alapértelmezés szerint a fő csomópont, a mag és a feladat mérőszámait jeleníti meg. 

Az összes Batch-fiók metrikákat tekinthet meg: 

1. A portálon kattintson a **minden szolgáltatás** > **Batch-fiókok**elemre, majd kattintson a Batch-fiók nevére.
2. A **figyelés**területen kattintson a **metrikák**elemre.
3. Válassza ki egy vagy több metrikákat. Ha szeretné, válassza a további erőforrás-metrikák lehetőséget az **előfizetések**, az **erőforráscsoport**, az **Erőforrás típusa**és az **erőforrás** legördülő lista használatával.
    * A Count-alapú metrikák esetében (például a "dedikált mag darabszáma" vagy az "alacsony prioritású csomópontok száma") az "átlag" összesítést használja. Eseményvezérelt metrikák esetén (például "a készlet átméretezése kész események") használja a "Count" összesítést.

> [!WARNING]
> Ne használja a "Sum" összesítést, amely hozzáadja a diagram adott időszakában fogadott adatpontok értékeit.
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Metrikák programozott módon lekéréséhez használja az Azure Monitor API-kat. Lásd például: [Azure monitor mérőszámok beolvasása a .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)-tel.

## <a name="batch-metric-reliability"></a>Batch-metrika megbízhatóság

Metrikák trendelemzés és az adatok elemzési célokra szolgálnak. Metrika kézbesítési nem garantált, és a kimenő soron kívüli kézbesítési, adatvesztés és/vagy ismétlődést használatára. Egyetlen eseményeket a riasztás vagy az eseményindító függvények használata nem ajánlott. A riasztási küszöbértékek beállításával kapcsolatos további részletekért tekintse meg a [Batch metrika riasztások](#batch-metric-alerts) című szakaszt.

Az elmúlt 3 perc kibocsátott metrikák továbbra is lehetséges, hogy összesíti. Ez idő alatt a metrikaértékek is vezethet.

## <a name="batch-metric-alerts"></a>A Batch metrikákhoz kapcsolódó riasztások

Megadhatja a közel valós idejű *metrikai riasztásokat* , amelyek akkor aktiválódnak, ha egy adott metrika értéke átlép egy hozzárendelt küszöbértéket. A riasztás létrehoz egy [értesítést](../monitoring-and-diagnostics/insights-alerts-portal.md) , amelyet akkor választ, ha a riasztás "aktiválva van" (ha a küszöbérték túlnyúlik, és a riasztás feltétele teljesül), valamint a "feloldva" (ha a küszöbértéket meghaladják, és a feltétel már nem teljesül). Egyetlen adatpont alapuló riasztások nem ajánlott, mivel a metrikák out soron kívüli kézbesítési, az adatvesztést és/vagy duplikált vonatkozik. Riasztási kell, használja a küszöbértékek áthidalhatók az ezeket az inkonzisztenciákat.

Például érdemes metrikariasztás konfigurálása, ha az alacsony prioritású magok száma egy bizonyos szintre csökken, így módosíthatja a készletek az összeállításban. Javasoljuk, hogy állítsa be legalább 10 percig, ahol riasztásokat indítás, ha az átlagos alacsony prioritású magok száma a küszöbérték érték alá csökken, a teljes időszakra. Egy 1 – 5 percnél hosszabb ideig a riasztást, metrikák továbbra is lehetséges, hogy összesítés nem ajánlott.

Metrikariasztás konfigurálása a portálon:

1. Kattintson a **Minden szolgáltatás** > **Batch-fiókok** lehetőségre, majd a Batch-fiók nevére.
2. A **figyelés**területen kattintson a **riasztási szabályok** > **metrikai riasztás hozzáadása**lehetőségre.
3. Válassza ki a metrika, egy riasztási feltétel (például ha egy metrika egy időszakban meghalad egy bizonyos értéket) és egy vagy több értesítés.

A [REST API](https://docs.microsoft.com/rest/api/monitor/)használatával a közel valós idejű riasztást is konfigurálhatja. További információ: [riasztások áttekintése](../azure-monitor/platform/alerts-overview.md). A feladatok, a feladatok vagy a készletekre vonatkozó információk a riasztásokban való felvételéhez tekintse meg az [Azure monitor riasztásokkal rendelkező eseményekre adott válaszokban](../azure-monitor/learn/tutorial-response.md) szereplő keresési lekérdezéseket ismertető témakört.

## <a name="batch-diagnostics"></a>Batch-diagnosztika

Diagnosztikai naplók az Azure-erőforrások, amelyek ismertetik az egyes erőforrások a művelet által kibocsátott információkat tartalmaznak. A Batch gyűjtse össze a következő naplók kapcsolódnak:

* A **szolgáltatás naplózza** a Azure batch szolgáltatás által kibocsátott eseményeket egy különálló batch-erőforrás, például egy készlet vagy feladat élettartama során. 

* A **metrikák** a fiók szintjén vannak naplózva. 

Alapértelmezés szerint nem engedélyezettek a beállítások a diagnosztikai naplók gyűjtésének engedélyezéséhez. Explicit módon a figyelni kívánt minden egyes Batch-fiókhoz tartozó diagnosztikai beállítások engedélyezése.

### <a name="log-destinations"></a>Napló célhelyek

Gyakran előfordul, hogy válassza ki az Azure Storage-fiók log céljaként. Az Azure Storage-naplók tárolásához, a fiók létrehozása-naplók gyűjtésének engedélyezése előtt. A Batch-fiók egy storage-fiókot társított, ha kiválaszthatja, hogy a fiók log céljaként. 

A diagnosztikai naplók számára más választható célok:

* A Batch diagnosztikai napló eseményeinek továbbítása egy [Azure Event hub](../event-hubs/event-hubs-what-is-event-hubs.md)-ba. Az Event Hubs fogadására képes több millió esemény / másodperc, amely, átalakíthatja és tárolhatja bármilyen valós idejű elemzési szolgáltató segítségével. 

* Diagnosztikai naplók küldése [Azure monitor naplókba](../log-analytics/log-analytics-overview.md), ahol elemezheti őket, vagy exportálhatja őket elemzésre Power bi vagy Excelben.

> [!NOTE]
> Ön további költségekkel járhat tárolásához, vagy az Azure-szolgáltatások diagnosztikai naplóadatokat feldolgozni. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Diagnosztikai naplók a Batchben gyűjtésének engedélyezéséhez

1. A portálon kattintson a **minden szolgáltatás** > **Batch-fiókok**elemre, majd kattintson a Batch-fiók nevére.
2. A **figyelés**területen kattintson a **diagnosztikai naplók** > **a diagnosztika bekapcsolása**elemre.
3. A **diagnosztikai beállítások**területen adja meg a beállítás nevét, és válassza ki a napló célhelyét (meglévő Storage-fiók, Event Hub vagy Azure monitor napló). Válassza a **ServiceLog** és a **AllMetrics**lehetőséget.

    Amikor kiválaszt egy tárfiókot, igény szerint adatmegőrzési szabály beállításához. Ha nem adja meg a megőrzési napok száma, adatok megőrződnek a storage-fiók élettartama során.

4. Kattintson a **Save** (Mentés) gombra.

    ![Batch-diagnosztika](media/batch-diagnostics/diagnostics-portal.png)

További lehetőségek a naplók gyűjtésének engedélyezéséhez: használja a Azure Monitor a portálon a diagnosztikai beállítások konfigurálásához, egy [Resource Manager-sablon](../azure-monitor/platform/diagnostic-settings-template.md)használatához, vagy használja a Azure PowerShell vagy az Azure CLI-t. Lásd: [adatok gyűjtése és felhasználása az Azure-erőforrásokból](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Hozzáférés diagnosztikai naplók storage-ban

Ha archiválja kötegelt diagnosztikai naplókat egy tárfiókban, egy storage-tárolót a storage-fiókban jön létre, amint a kapcsolódó esemény következik be. Blobok jönnek létre a következő elnevezési mintának megfelelően:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Példa:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Minden `PT1H.json` blob-fájl JSON-formázott eseményeket tartalmaz, amelyek a blob URL-címében megadott órán belül történtek (például `h=12`). A jelen órában az eseményeket a rendszer a `PT1H.json` fájlhoz fűzi, ahogy azok bekövetkeznek. A percben megadott érték (`m=00`) mindig `00`, mivel a diagnosztikai naplóbeli események óránként egyedi blobokra vannak bontva. (UTC szerint is minden esetben.)

Az alábbi példa egy `PT1H.json` naplófájlban található `PoolResizeCompleteEvent` bejegyzést mutat be. A dedikált és alacsony prioritású csomópontok aktuális és megcélzott számával, valamint a művelet kezdési és befejezési időpontjával kapcsolatos információkat tartalmaz:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

További információ a Storage-fiókban található diagnosztikai naplók sémájáról: az [Azure diagnosztikai naplóinak archiválása](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). A Storage API-k használatával programozott módon hozzáférni az a tárfiókban lévő naplókat. 

### <a name="service-log-events"></a>Szolgáltatás bejelentkezési események
Az Azure szolgáltatási naplók a Batchben, ha a gyűjtött, a Batch-készlettel vagy feladattal például egyedi erőforrásokat élettartama során az Azure Batch szolgáltatás által kibocsátott események tartalmaznak. Minden egyes köteg által kibocsátott naplóz JSON formátumban. Ez például egy minta- **készlet létrehozási eseményének**törzse:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

A Batch szolgáltatás jelenleg a következő szolgáltatás bejelentkezési eseményeket bocsát ki. Ez a lista nem lehet teljes, mivel további események is bővült, mivel ez a cikk utolsó frissítés.

| **Szolgáltatás-naplózási események** |
| --- |
| [Készlet létrehozása](batch-pool-create-event.md) |
| [Készlet törlésének kezdete](batch-pool-delete-start-event.md) |
| [Készlet törlése kész](batch-pool-delete-complete-event.md) |
| [Készlet átméretezésének kezdete](batch-pool-resize-start-event.md) |
| [A készlet átméretezése befejeződött](batch-pool-resize-complete-event.md) |
| [Feladat indítása](batch-task-start-event.md) |
| [A feladat befejeződött](batch-task-complete-event.md) |
| [Sikertelen feladat](batch-task-fail-event.md) |



## <a name="next-steps"></a>Következő lépések

* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* További információ a [Batch-megoldások figyeléséről](monitoring-overview.md).
