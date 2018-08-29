---
title: Metrikák, a riasztások és a diagnosztikai naplók az Azure Batch |} A Microsoft Docs
description: Jegyezze fel, és elemezze a diagnosztikai naplót eseményeire az Azure Batch-fiók erőforrásokat, például a készletek és tevékenységek.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 85bda9e1a936f19a37be2f2c1c42f948cab727d6
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122467"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-metrikák, a riasztások és a naplókat a további diagnosztikai kiértékeléséhez és figyeléséhez

Ez a cikk azt ismerteti, hogyan szolgáltatását használja, amely a Batch-fiók figyelése [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Az Azure Monitor gyűjt [metrikák](../monitoring-and-diagnostics/monitoring-overview-metrics.md) és [diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) az erőforrásokat a Batch-fiókban. Összegyűjtheti, és számos módon figyelheti a Batch-fiókhoz, és diagnosztizálhatja a problémákat a feldolgozásukhoz. Beállíthatja úgy is [metrikákhoz kapcsolódó riasztások](../monitoring-and-diagnostics/monitoring-overview-alerts.md#alerts-on-azure-monitor-data) így értesítést kapjon a metrika eléri a megadott értéket. 

## <a name="batch-metrics"></a>Batch-metrikák

Metrikák az Azure-erőforrások, amelyek használják az Azure Monitor szolgáltatás által kibocsátott (más néven teljesítményszámlálók) Azure telemetriai adatokat. A Batch-fiókokban példa mérőszámok közé tartozik: készlet létrehozása esemény, alacsony prioritású csomópontok száma és a feladat befejezése események. 

Tekintse meg a [Batch támogatott mérőszámok listája](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftbatchbatchaccounts).

Metrikák a következők:

* Minden egyes Batch-fiókban, további konfiguráció nélkül alapértelmezés szerint engedélyezve
* Generált 1 percenként
* Nem maradnak meg automatikusan, de van egy 30 napos gördülő előzményei. Tevékenységi metrikák megőrizheti a részeként [diagnosztikai naplózás](#work-with-diagnostic-logs).

### <a name="view-metrics"></a>Metrikák megtekintése

A Batch-fiók metrikák megtekintése az Azure Portalon. A **áttekintése** alapértelmezés szerint a fiók jeleníti meg a fő csomópont, a core és a feladat metrikák oldala. 

Az összes Batch-fiók metrikákat tekinthet meg: 

1. Kattintson a portál **minden szolgáltatás** > **Batch-fiókok**, majd kattintson a Batch-fiók nevére.
2. A **figyelés**, kattintson a **metrikák**.
3. Válassza ki egy vagy több metrikákat. Ha azt szeretné, válassza a további erőforrás-mérőszámok segítségével a **előfizetések**, **erőforráscsoport**, **erőforrástípus**, és **erőforrás** a legördülő menük.

    ![Batch-metrikák](media/batch-diagnostics/metrics-portal.png)

Metrikák programozott módon lekéréséhez használja az Azure Monitor API-kat. Lásd a [lekérése az Azure Monitor-metrikák .NET-tel](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-alerts"></a>A Batch metrikákhoz kapcsolódó riasztások

Igény szerint állítsa be közel valós idejű *metrikákhoz kapcsolódó riasztások* , amely indítható el, ha egy adott mérőszám értéke átlép egy küszöbértéket, hozzárendelt. A riasztást állít elő egy [értesítési](../monitoring-and-diagnostics/insights-alerts-portal.md) úgy dönt, hogy ha a riasztás "aktív" (amikor áthaladnak a küszöbértéket, és a riasztási feltétel nem teljesül), valamint amikor azt "megoldódott" (amikor újra áthaladnak a küszöbértéket, és a feltétel nem már teljesül). 

Például érdemes metrikariasztás konfigurálása, ha az alacsony prioritású magok száma egy bizonyos szintre csökken, így módosíthatja a készletek az összeállításban.

Metrikariasztás konfigurálása a portálon:

1. Kattintson a **Minden szolgáltatás** > **Batch-fiókok** lehetőségre, majd a Batch-fiók nevére.
2. A **figyelés**, kattintson a **riasztási szabályok** > **metrikariasztás hozzáadása**.
3. Válassza ki a metrika, egy riasztási feltétel (például ha egy metrika egy időszakban meghalad egy bizonyos értéket) és egy vagy több értesítés.

A közel valós idejű riasztási használatával is konfigurálhatja a [REST API-val](https://docs.microsoft.com/rest/api/monitor/). További információkért lásd: [az újabb metrikákhoz kapcsolódó riasztások használata az Azure-szolgáltatásokhoz az Azure Portalon](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)
## <a name="batch-diagnostics"></a>Batch-diagnosztika

Diagnosztikai naplók az Azure-erőforrások, amelyek ismertetik az egyes erőforrások a művelet által kibocsátott információkat tartalmaznak. A Batch gyűjtse össze a következő naplók kapcsolódnak:

* **Szolgáltatási naplók** egyes Batch-erőforrások teljes élettartama alatt az Azure Batch szolgáltatás által kibocsátott események, például egy készlettel vagy feladattal. 

* **Metrikák** naplók a fiók szintjén. 

Alapértelmezés szerint nem engedélyezettek a beállítások a diagnosztikai naplók gyűjtésének engedélyezéséhez. Explicit módon a figyelni kívánt minden egyes Batch-fiókhoz tartozó diagnosztikai beállítások engedélyezése.

### <a name="log-destinations"></a>Napló célhelyek

Gyakran előfordul, hogy válassza ki az Azure Storage-fiók log céljaként. Az Azure Storage-naplók tárolásához, a fiók létrehozása-naplók gyűjtésének engedélyezése előtt. A Batch-fiók egy storage-fiókot társított, ha kiválaszthatja, hogy a fiók log céljaként. 

A diagnosztikai naplók számára más választható célok:

* Kötegelt diagnosztikai napló események a Stream- [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Az Event Hubs fogadására képes több millió esemény / másodperc, amely, átalakíthatja és tárolhatja bármilyen valós idejű elemzési szolgáltató segítségével. 

* Diagnosztikai naplók küldése [Azure Log Analytics](../log-analytics/log-analytics-overview.md), ahol elemezheti az Operations Management Suite (OMS) portálra, és exportálhatja is azokat elemzés a Power bi-ban vagy az Excel.

> [!NOTE]
> Ön további költségekkel járhat tárolásához, vagy az Azure-szolgáltatások diagnosztikai naplóadatokat feldolgozni. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Diagnosztikai naplók a Batchben gyűjtésének engedélyezéséhez

1. Kattintson a portál **minden szolgáltatás** > **Batch-fiókok**, majd kattintson a Batch-fiók nevére.
2. A **figyelés**, kattintson a **diagnosztikai naplók** > **diagnosztika bekapcsolása**.
3. A **diagnosztikai beállítások**, adjon meg egy nevet a beállítás, és válassza ki a naplócél (meglévő tárolási fiók, az Eseményközpont vagy a Log Analytics). Válassza ki az egyik vagy mindkét **ServiceLog** és **AllMetrics**.

    Amikor kiválaszt egy tárfiókot, igény szerint adatmegőrzési szabály beállításához. Ha nem adja meg a megőrzési napok száma, adatok megőrződnek a storage-fiók élettartama során.

4. Kattintson a **Save** (Mentés) gombra.

    ![Batch-diagnosztika](media/batch-diagnostics/diagnostics-portal.png)

Az Erőforrásnapló-gyűjtés engedélyezése más lehetőségek a következők: a portálon az Azure Monitor használatával diagnosztikai beállítások konfigurálása, használja a [Resource Manager-sablon](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md), vagy az Azure PowerShell vagy az Azure parancssori felület. Lásd: [gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).


### <a name="access-diagnostics-logs-in-storage"></a>Hozzáférés diagnosztikai naplók storage-ban

Ha archiválja kötegelt diagnosztikai naplókat egy tárfiókban, egy storage-tárolót a storage-fiókban jön létre, amint a kapcsolódó esemény következik be. Blobok jönnek létre a következő elnevezési mintának megfelelően:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Példa:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Mindegyik PT1H.json blob fájl tartalmazza a blob URL-CÍMBEN megadott egy órán belül bekövetkezett események JSON-formátumú (például h = 12). Az aktuális órában az események az előfordulásukkor lesznek a PT1H.json fájlhoz fűzve. A perc értéke (m = 00) mindig 00, mert a diagnosztikai naplóesemények óránként külön blobokba vannak osztva. (UTC szerint is minden esetben.)


További információ a tárfiókot a diagnosztikai naplók a séma: [archiválása az Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

A Storage API-k használatával programozott módon hozzáférni az a tárfiókban lévő naplókat. 

### <a name="service-log-events"></a>Szolgáltatás bejelentkezési események
Az Azure szolgáltatási naplók a Batchben, ha a gyűjtött, a Batch-készlettel vagy feladattal például egyedi erőforrásokat élettartama során az Azure Batch szolgáltatás által kibocsátott események tartalmaznak. Minden egyes köteg által kibocsátott naplóz JSON formátumban. Például ez a minta törzse **készlet létrehozása esemény**:

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

| **Szolgáltatás bejelentkezési események** |
| --- |
| [Készlet létrehozása](batch-pool-create-event.md) |
| [Készlet törlésének indítása](batch-pool-delete-start-event.md) |
| [Készlet törlése kész](batch-pool-delete-complete-event.md) |
| [Készlet átméretezésének indítása](batch-pool-resize-start-event.md) |
| [Készlet átméretezése kész](batch-pool-resize-complete-event.md) |
| [Zahájení úlohy](batch-task-start-event.md) |
| [A feladat befejezése](batch-task-complete-event.md) |
| [A feladat sikertelen](batch-task-fail-event.md) |



## <a name="next-steps"></a>További lépések

* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* Tudjon meg többet [Batch-megoldások figyelési](monitoring-overview.md).
