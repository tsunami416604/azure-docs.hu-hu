---
title: Metrikák, riasztások és diagnosztikai naplók
description: A diagnosztikai napló eseményeinek rögzítése és elemzése Azure Batch fiók erőforrásaihoz, például a készletekhez és a feladatokhoz.
ms.topic: article
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: 7f75a8302c8ba368138e6c8edee6c6069c5031d8
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117301"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-metrikák, riasztások és naplók a diagnosztika kiértékeléséhez és figyeléséhez

 
Ez a cikk ismerteti, hogyan figyelheti a Batch-fiókokat a [Azure monitor](../azure-monitor/overview.md)szolgáltatásainak használatával. A Azure Monitor a Batch-fiók erőforrásaihoz tartozó [mérőszámokat](../azure-monitor/platform/data-platform-metrics.md) és [diagnosztikai naplókat](../azure-monitor/platform/platform-logs-overview.md) gyűjt. Gyűjtsön és fogyasszon el különböző módon ezeket az adatokat a Batch-fiók figyeléséhez és a problémák diagnosztizálásához. A [metrikai riasztásokat](../azure-monitor/platform/alerts-overview.md) úgy is konfigurálhatja, hogy értesítést kapjon, ha egy metrika eléri a megadott értéket. 

## <a name="batch-metrics"></a>Batch-metrikák

A metrikák az Azure-erőforrások által kibocsátott Azure telemetria-adatok (más néven teljesítményszámlálók), amelyeket a Azure Monitor szolgáltatás használ. A Batch-fiókokban szereplő mérőszámok például a következők: készlet létrehozása események, alacsony prioritású csomópontok száma, valamint a feladat teljes eseményei. 

Tekintse meg a [támogatott batch-metrikák listáját](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

A metrikák a következők:

* Alapértelmezés szerint engedélyezve van minden batch-fiókban, további konfiguráció nélkül
* Létrehozva 1 percenként
* A rendszer nem őrzi meg automatikusan, de egy 30 napos előzményt tartalmaz. A tevékenység-metrikák a diagnosztikai naplózás részeként is megmaradhatnak.

### <a name="view-metrics"></a>Metrikák megtekintése

Megtekintheti a Batch-fiók metrikáit a Azure Portalban. A fiók **áttekintő** lapja alapértelmezés szerint a fő csomópont, a mag és a feladat mérőszámait jeleníti meg. 

Az összes batch-fiók metrikájának megtekintése: 

1. A portálon kattintson a **minden szolgáltatás** > **Batch-fiókok**elemre, majd kattintson a Batch-fiók nevére.
2. A **figyelés**területen kattintson a **metrikák**elemre.
3. Válasszon ki egy vagy több mérőszámot. Ha szeretné, válassza a további erőforrás-metrikák lehetőséget az **előfizetések**, az **erőforráscsoport**, az **Erőforrás típusa**és az **erőforrás** legördülő lista használatával.
    * A Count-alapú metrikák esetében (például a "dedikált mag darabszáma" vagy az "alacsony prioritású csomópontok száma") az "átlag" összesítést használja. Eseményvezérelt metrikák esetén (például "a készlet átméretezése kész események") használja a "Count" összesítést.

> [!WARNING]
> Ne használja a "Sum" összesítést, amely hozzáadja a diagram adott időszakában fogadott adatpontok értékeit.
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

A metrikák programozott beolvasásához használja a Azure Monitor API-kat. Lásd például: [Azure monitor mérőszámok beolvasása a .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)-tel.

## <a name="batch-metric-reliability"></a>Batch-metrika megbízhatósága

A metrikák a trendek és az adatok elemzésére szolgálnak. A metrikai kézbesítés nem garantált, és a rendelésen kívüli kézbesítés, adatvesztés és/vagy ismétlődés vonatkozik rájuk. Nem ajánlott egyetlen eseményt riasztás vagy trigger függvények használatára használni. A riasztási küszöbértékek beállításával kapcsolatos további részletekért tekintse meg a [Batch metrika riasztások](#batch-metric-alerts) című szakaszt.

Az elmúlt 3 percben kibocsátott metrikák továbbra is összevonva lehetnek. Ebben az időkeretben a mérőszám értékeit lehet jelenteni.

## <a name="batch-metric-alerts"></a>Batch metrikus riasztások

Megadhatja a közel valós idejű *metrikai riasztásokat* , amelyek akkor aktiválódnak, ha egy adott metrika értéke átlép egy hozzárendelt küszöbértéket. A riasztás létrehoz egy [értesítést](../monitoring-and-diagnostics/insights-alerts-portal.md) , amelyet akkor választ, ha a riasztás "aktiválva van" (ha a küszöbérték túlnyúlik, és a riasztás feltétele teljesül), valamint a "feloldva" (ha a küszöbértéket meghaladják, és a feltétel már nem teljesül). Az egyazon adatpontokon alapuló riasztások nem ajánlottak, mivel a mérőszámok a rendelésen kívüli kézbesítés, az adatvesztés és/vagy a Duplikálás esetén érvényesek. A riasztásoknak az ilyen következetlenségekhez szükséges küszöbértékeket kell használniuk.

Előfordulhat például, hogy egy metrikus riasztást szeretne beállítani, ha az alacsony prioritású alapszám egy bizonyos szintre esik, így beállíthatja a készletek összeállítását. Azt javasoljuk, hogy legalább 10 percet állítson be, ahol a riasztások akkor aktiválódnak, ha az alacsony prioritású átlagos alapértékek a teljes időszak küszöbértéke alá csökkennek. A riasztások 1-5 perces időszakra nem ajánlottak, mivel a metrikák továbbra is összevonva lehetnek.

Metrikai riasztás konfigurálása a portálon:

1. Kattintson a **minden szolgáltatás** > **Batch-fiókok**elemre, majd kattintson a Batch-fiók nevére.
2. A **figyelés**területen kattintson a **riasztási szabályok** > **metrikus riasztás hozzáadása**elemre.
3. Válassza ki a mérőszámot, a riasztási feltételt (például ha egy metrika meghaladja az adott értéket egy adott időszakban), és egy vagy több értesítést.

A [REST API](https://docs.microsoft.com/rest/api/monitor/)használatával a közel valós idejű riasztást is konfigurálhatja. További információ: [riasztások áttekintése](../azure-monitor/platform/alerts-overview.md). A feladatok, a feladatok vagy a készletekre vonatkozó információk a riasztásokban való felvételéhez tekintse meg az [Azure monitor riasztásokkal rendelkező eseményekre adott válaszokban](../azure-monitor/learn/tutorial-response.md) szereplő keresési lekérdezéseket ismertető témakört.

## <a name="batch-diagnostics"></a>Batch-diagnosztika

A diagnosztikai naplók az egyes erőforrások működését leíró Azure-erőforrások által kibocsátott információkat tartalmazzák. A Batch szolgáltatásban a következő naplók gyűjthetők:

* A **szolgáltatás naplózza** a Azure batch szolgáltatás által kibocsátott eseményeket egy különálló batch-erőforrás, például egy készlet vagy feladat élettartama során. 

* A **metrikák** a fiók szintjén vannak naplózva. 

A diagnosztikai naplók gyűjtését engedélyező beállítások alapértelmezés szerint nincsenek engedélyezve. Explicit módon engedélyezze a diagnosztikai beállításokat minden figyelni kívánt batch-fiókhoz.

### <a name="log-destinations"></a>Naplók célhelyei

Gyakori forgatókönyv egy Azure Storage-fiók kiválasztása a napló célhelyként. Ha a naplókat az Azure Storage-ban szeretné tárolni, hozza létre a fiókot a naplók gyűjtésének engedélyezése előtt. Ha a Batch-fiókhoz társított egy Storage-fiókot, a fiókot a napló célhelyként is kiválaszthatja. 

További választható célhelyek a diagnosztikai naplókhoz:

* A Batch diagnosztikai napló eseményeinek továbbítása egy [Azure Event hub](../event-hubs/event-hubs-what-is-event-hubs.md)-ba. A Event Hubs másodpercenként több millió eseményt képes befogadni, amelyet később bármilyen valós idejű elemzési szolgáltató használatával átalakíthat és tárolhat. 

* Diagnosztikai naplók küldése [Azure monitor naplókba](../log-analytics/log-analytics-overview.md), ahol elemezheti őket, vagy exportálhatja őket elemzésre Power bi vagy Excelben.

> [!NOTE]
> Az Azure-szolgáltatásokkal további költségek is felmerülhetnek a diagnosztikai naplózási információk tárolásához vagy feldolgozásához. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Batch diagnosztikai naplók gyűjtésének engedélyezése

1. A portálon kattintson a **minden szolgáltatás** > **Batch-fiókok**elemre, majd kattintson a Batch-fiók nevére.
2. A **figyelés**területen kattintson a **diagnosztikai naplók** > **bekapcsolása a diagnosztika**elemre.
3. A **diagnosztikai beállítások**területen adja meg a beállítás nevét, és válassza ki a napló célhelyét (meglévő Storage-fiók, Event Hub vagy Azure monitor napló). Válassza a **ServiceLog** és a **AllMetrics**lehetőséget.

    Amikor kijelöl egy Storage-fiókot, opcionálisan beállíthat egy adatmegőrzési szabályt. Ha nem ad meg napokat a megőrzéshez, a rendszer a Storage-fiók élettartama alatt megőrzi az adatok mennyiségét.

4. Kattintson a **Save** (Mentés) gombra.

    ![Batch-diagnosztika](media/batch-diagnostics/diagnostics-portal.png)

További lehetőségek a naplók gyűjtésének engedélyezéséhez: használja a Azure Monitor a portálon a diagnosztikai beállítások konfigurálásához, egy [Resource Manager-sablon](../azure-monitor/platform/diagnostic-settings-template.md)használatához, vagy használja a Azure PowerShell vagy az Azure CLI-t. Lásd: [adatok gyűjtése és felhasználása az Azure-erőforrásokból](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Diagnosztikai naplók elérése a Storage-ban

Ha egy Storage-fiókba archiválja a Batch-diagnosztikai naplókat, akkor a rendszer egy tárolót hoz létre a Storage-fiókban, amint egy kapcsolódó esemény következik be. A Blobok a következő elnevezési mintának megfelelően jönnek létre:

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
Minden `PT1H.json` blob-fájl JSON-formázott eseményeket tartalmaz, amelyek a blob URL-címében megadott órán belül történtek `h=12`(például:). A jelen órában az eseményeket a rendszer hozzáfűzi a `PT1H.json` fájlhoz, ahogy azok bekövetkeznek. A perc értéke (`m=00`) mindig `00`, mivel a diagnosztikai napló eseményeinek az egyes blobokra való felosztása óránként történik. (Az összes alkalommal UTC-ben van.)

Az alábbi példa egy `PoolResizeCompleteEvent` `PT1H.json` naplófájlban lévő bejegyzésre mutat. A dedikált és alacsony prioritású csomópontok aktuális és megcélzott számával, valamint a művelet kezdési és befejezési időpontjával kapcsolatos információkat tartalmaz:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

További információ a Storage-fiókban található diagnosztikai naplók sémájáról: az [Azure diagnosztikai naplóinak archiválása](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). A Storage-fiókban lévő naplók programozott módon való eléréséhez használja a Storage API-kat. 

### <a name="service-log-events"></a>Szolgáltatás-naplózási események
Azure Batch a szolgáltatás naplóit, ha összegyűjtöttük, az adott batch-erőforrás, például egy készlet vagy feladat élettartama során a Azure Batch szolgáltatás által kibocsátott eseményeket tartalmazza. A Batch által kibocsátott összes eseményt JSON formátumban naplózza a rendszer. Ez például egy minta- **készlet létrehozási eseményének**törzse:

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

A Batch szolgáltatás jelenleg a következő szolgáltatás-naplózási eseményeket bocsátja ki. Előfordulhat, hogy a lista nem teljes, mert a cikk utolsó frissítése óta további események is hozzáadhatók.

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



## <a name="next-steps"></a>További lépések

* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* További információ a [Batch-megoldások figyeléséről](monitoring-overview.md).
