---
title: Metrikák, riasztások és diagnosztikai naplók - Azure Batch | Microsoft dokumentumok
description: Az Azure Batch-fiók erőforrásaihoz, például a készletekhez és a feladatokhoz diagnosztikai naplóeseményeket rögzíthet és elemezhet.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254861"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Kötegmetrikák, riasztások és naplók diagnosztikai értékeléshez és figyeléshez

 
Ebből a cikkből megtudhatja, hogyan figyelheti a Batch-fiók ot az [Azure Monitor](../azure-monitor/overview.md)funkcióival. Az Azure Monitor [metrikákat](../azure-monitor/platform/data-platform-metrics.md) és [diagnosztikai naplókat](../azure-monitor/platform/platform-logs-overview.md) gyűjt a Batch-fiókban lévő erőforrásokhoz. Gyűjtse össze és használja fel ezeket az adatokat a Batch-fiók figyelése és a problémák diagnosztizálása érdekében. [Metrikariasztásokat](../azure-monitor/platform/alerts-overview.md) is konfigurálhat, így értesítést kaphat, ha egy metrika elér egy megadott értéket. 

## <a name="batch-metrics"></a>Kötegmetrikák

Metrikák az Azure-telemetriai adatok (más néven teljesítményszámlálók) által kibocsátott Azure-erőforrások, amelyek az Azure Monitor szolgáltatás által felhasznált. Például egy Batch-fiók mutatói a következők: Készletlétrehozási események, Alacsony prioritású csomópontszám és Feladat-befejezési események. 

Tekintse meg [a támogatott Batch mérőszámok listáját.](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)

A mérőszámok a következők:

* Alapértelmezés szerint engedélyezve van minden Batch-fiókban további konfiguráció nélkül
* 1 percenként létrehozva
* Nem maradt meg automatikusan, de van egy 30 napos gördülő történelem. A diagnosztikai naplózás részeként megőrizheti a tevékenységmetrikákat.

### <a name="view-metrics"></a>Metrikák megtekintése

Tekintse meg a Batch-fiók metrikák az Azure Portalon. A fiók **áttekintő** lapja alapértelmezés szerint a kulcscsomópontot, az alapvető és a feladatmérőszámokat jeleníti meg. 

Az összes Batch-fiók mutatójának megtekintése: 

1. A portálon kattintson a **Minden szolgáltatás** > **batch-fiók elemre,** majd a Batch-fiók nevére.
2. A **Figyelés**csoportban kattintson **a Mérőszámok**elemre.
3. Jelöljön ki egy vagy több mérőszámot. Ha szeretné, válasszon további erőforrás-mutatókat az **Előfizetések**, **Az Erőforrás csoport**, az Erőforrás **típusa**és az **Erőforrás** legördülő menü használatával.
    * A számláló-alapú metrikák (például a "dedikált magszámláló" vagy "alacsony prioritású csomópont száma" használja az "Átlagos" összesítés. Eseményalapú metrikák (például a "Készlet átméretezése teljes események" esetén használja a "Count" aggregációt.

> [!WARNING]
> Ne használja az "Összeg" összesítést, amely összeadja a diagram időszakában kapott összes adatpont értékét
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Metrikák programozott beolvasásához használja az Azure Monitor API-kat. Lásd például az [Azure Monitor metrikák lekérése a .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)használatával című témakört.

## <a name="batch-metric-reliability"></a>Kötegelt metrika megbízhatósága

A mérőszámok trend- és adatelemzésre szolgálnak. A metrikakézbesítés nem garantált, és a rendelésen kívüli kézbesítés, az adatvesztés és/vagy a párhuzamosságok függvénye. Egyetlen esemény használata a riasztásokhoz vagy a függvények aktiválásához nem ajánlott. Tekintse meg a [Batch metrika riasztások](#batch-metric-alerts) szakasz további részleteket, hogyan állíthat be küszöbértékeket a riasztási.

Az elmúlt 3 percben kibocsátott mérőszámok még mindig összesíthetők. Ebben az időszakban a metrikaértékek aluljelentésre kerülhetnek.

## <a name="batch-metric-alerts"></a>Kötegelt metrikus riasztások

Szükség esetén konfigurálja a közel valós idejű *metrikariasztásokat,* amelyek akkor aktiválódnak, ha egy megadott metrika értéke átlépi a hozzárendelt küszöbértéket. A riasztás létrehoz egy [értesítést,](../monitoring-and-diagnostics/insights-alerts-portal.md) amelyet akkor választ, ha a riasztás "Aktiválva" (amikor a küszöbértéket átlépik, és a riasztási feltétel teljesül), valamint amikor "Megoldódott" (amikor a küszöbértéket újra átlépi, és a feltétel már nem teljesül). Az egyes adatpontokon alapuló riasztás nem ajánlott, mivel a metrikák rendelésen kívüli kézbesítés, adatvesztés és/vagy párhuzamosságok tárgyát képezik. A riasztási kell használni a küszöbértékeket, hogy figyelembe ezeket az inkonzisztenciák.

Például érdemes lehet beállítani egy metrika riasztást, ha az alacsony prioritású magok száma esik egy bizonyos szintre, így módosíthatja a készletek összetételét. Ajánlott 10 vagy több perc, amikor riasztások kiváltó, ha az átlagos alacsony prioritású magszám a küszöbérték alá esik a teljes időszakra. Nem ajánlott 1–5 perces időszakon riasztást adni, mivel a metrikák továbbra is összesítésre lehetnek.

Metrikariasztás konfigurálása a portálon:

1. Kattintson **a Minden szolgáltatás** > **Batch-fiók elemre,** majd a Batch-fiók nevére.
2. A **Figyelés**csoportban kattintson **a Riasztási szabályok** > **Riasztás hozzáadása riasztás**elemre.
3. Válasszon ki egy metrikát, egy riasztási feltételt (például ha egy metrika egy adott értéket túllép egy adott időszakban), és egy vagy több értesítést.

Közel valós idejű riasztást is konfigurálhat a [REST API használatával.](https://docs.microsoft.com/rest/api/monitor/) További információt a [Riasztások – áttekintés című témakörben](../azure-monitor/platform/alerts-overview.md)talál. Ha feladat-, feladat- vagy készletspecifikus információkat szeretne felvenni a riasztásokba, tekintse meg a keresési lekérdezések adatait az [Események megválaszolása az Azure Monitor-értesítésekkel című témakörben.](../azure-monitor/learn/tutorial-response.md)

## <a name="batch-diagnostics"></a>Batch-diagnosztika

A diagnosztikai naplók az Egyes erőforrások működését leíró Azure-erőforrások által kibocsátott információkat tartalmazzák. A Batch esetében a következő naplókat gyűjtheti:

* **Szolgáltatás naplók** események az Azure Batch szolgáltatás által kibocsátott élettartama alatt egy adott Batch-erőforrás, például egy készlet vagy feladat. 

* **Metrikák** naplók a fiók szintjén. 

A diagnosztikai naplók gyűjtését engedélyező beállítások alapértelmezés szerint nincsenek engedélyezve. Explicit módon engedélyezze a diagnosztikai beállításokat minden figyelni kívánt Batch-fiókhoz.

### <a name="log-destinations"></a>Úti célok naplózása

Gyakori forgatókönyv, hogy válasszon ki egy Azure Storage-fiókot a napló cél. Naplók tárolására az Azure Storage-ban, hozza létre a fiókot, mielőtt lehetővé teszi a naplók gyűjtését. Ha hozzárendelt egy tárfiókot a Batch-fiókhoz, kiválaszthatja azt a fiókot a napló célhelyeként. 

A diagnosztikai naplók egyéb választható célállomásai:

* Kötegelt diagnosztikai naplóesemények streamelése egy [Azure Event Hub ba.](../event-hubs/event-hubs-what-is-event-hubs.md) Az Event Hubs másodpercenként több millió eseményt tud befizetni, amelyeket aztán bármely valós idejű elemzési szolgáltató használatával átalakíthat és tárolhat. 

* Diagnosztikai naplók küldése az [Azure Monitor naplóiba,](../log-analytics/log-analytics-overview.md)ahol elemezheti őket, vagy exportálhatja őket elemzésre a Power BI-ban vagy az Excelben.

> [!NOTE]
> Az Azure-szolgáltatásokkal való diagnosztikai naplóadatok tárolása vagy feldolgozása további költségekkel járhat. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Kötegelt diagnosztikai naplók gyűjtésének engedélyezése

1. A portálon kattintson a **Minden szolgáltatás** > **batch-fiók elemre,** majd a Batch-fiók nevére.
2. A **Figyelés**csoportban kattintson a **Diagnosztikai naplók** > **diagnosztikai bekapcsolása gombra.**
3. A **Diagnosztikai beállítások**párbeszédpanelen adja meg a beállítás nevét, és válasszon egy naplócélt (meglévő tárfiók, Event Hub vagy Azure Monitor naplók). Válassza ki a **ServiceLog** és az **AllMetrics lehetőséget.**

    Amikor kiválaszt egy tárfiókot, szükség esetén be egy adatmegőrzési szabályzatot. Ha nem adja meg a megőrzési napok számát, az adatok a tárfiók élettartama alatt megőrződnek.

4. Kattintson a **Mentés** gombra.

    ![Batch-diagnosztika](media/batch-diagnostics/diagnostics-portal.png)

A naplógyűjtés engedélyezésének egyéb lehetőségei a következők: az Azure Monitor használata a portálon a diagnosztikai beállítások konfigurálásához, [egy Resource Manager-sablon](../azure-monitor/platform/diagnostic-settings-template.md)használata , vagy az Azure PowerShell vagy az Azure CLI használata. lásd: [Naplóadatok gyűjtése és felhasználása az Azure-erőforrásokból.](../azure-monitor/platform/platform-logs-overview.md)


### <a name="access-diagnostics-logs-in-storage"></a>Diagnosztikai naplók elérése a tárolóban

Ha archiválja a Batch diagnosztikai naplókat egy tárfiókban, egy tártároló jön létre a tárfiókban, amint egy kapcsolódó esemény bekövetkezik. A blobok a következő elnevezési minta szerint jönnek létre:

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
Minden `PT1H.json` blobfájl JSON-formátumú eseményeket tartalmaz, amelyek a blob URL-címében megadott órán belül történtek (például). `h=12` A jelen órában az események `PT1H.json` a fájlhoz kerülnek, amint azok bekövetkeznek. A perc`m=00`érték ( `00`) mindig , mivel a diagnosztikai napló események óránként ida ba vannak osztva. (Minden alkalommal UTC-ben van.)

Az alábbi példa `PoolResizeCompleteEvent` egy `PT1H.json` naplófájlban lévő bejegyzést mutat be. Információkat tartalmaz a dedikált és alacsony prioritású csomópontok aktuális és célszámára, valamint a művelet kezdési és befejezési időpontjára vonatkozóan:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

A tárfiókdiagnosztikai naplók sémájáról az [Archív Azure diagnosztikai naplók](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account)című témakörben talál további információt. A tárfiókban lévő naplók programozott eléréséhez használja a Storage API-kat. 

### <a name="service-log-events"></a>Szolgáltatásnapló eseményei
Az Azure Batch service logs, ha összegyűjtött, tartalmazza az Azure Batch szolgáltatás által az egyes Batch-erőforrások, például egy készlet vagy feladat élettartama alatt kibocsátott eseményeket. A Batch által kibocsátott minden esemény JSON formátumban van naplózva. Például ez egy **mintakészlet létrehozási eseményének törzse:**

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

A Batch szolgáltatás jelenleg a következő szolgáltatásnapló-eseményeket bocsátja ki. Előfordulhat, hogy ez a lista nem teljes, mivel a cikk legutóbbi frissítése óta további események et adhattunk hozzá.

| **Szolgáltatásnapló eseményei** |
| --- |
| [Készlet létrehozása](batch-pool-create-event.md) |
| [Készlet törlése kezdete](batch-pool-delete-start-event.md) |
| [Készlet törlése kész](batch-pool-delete-complete-event.md) |
| [Készlet átméretezési kezdete](batch-pool-resize-start-event.md) |
| [Készlet átméretezése kész](batch-pool-resize-complete-event.md) |
| [Tevékenység kezdete](batch-task-start-event.md) |
| [Feladat befejezve](batch-task-complete-event.md) |
| [A feladat sikertelen](batch-task-fail-event.md) |



## <a name="next-steps"></a>További lépések

* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* További információ a [Batch-megoldások figyeléséről.](monitoring-overview.md)
