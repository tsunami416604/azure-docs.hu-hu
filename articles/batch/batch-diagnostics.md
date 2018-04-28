---
title: Metrikákat, a riasztások és a diagnosztikai naplók az Azure Batch |} Microsoft Docs
description: Jegyezze fel, és elemzi az Azure Batch-fiók erőforrásokhoz, mint a készletek és a feladatok diagnosztikai naplózási eseményeket.
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
ms.openlocfilehash: e64d272695c4e47c972df040d1c1c2a63bf3dddd
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Kötegelt metrikákat, a riasztások és a naplókat a további diagnosztikai kiértékelési és figyelés

Ez a cikk azt ismerteti, hogyan szolgáltatását használja, a Batch-fiók figyeléséhez [Azure figyelő](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Az Azure adatokat gyűjt [metrikák](../monitoring-and-diagnostics/monitoring-overview-metrics.md) és [diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) a Batch-fiókhoz az erőforrásokra. Gyűjtse össze és felhasználhatják ezeket az adatokat a különböző módszereket a Batch-fiók figyelése és diagnosztizálása problémák. Beállíthatja úgy is [metrika riasztások](../monitoring-and-diagnostics/monitoring-overview-alerts.md#alerts-on-azure-monitor-data) , értesítést kapjon a metrika eléri a megadott értéket. 

## <a name="batch-metrics"></a>Kötegelt metrikák

Adatok gyűjtése le az Azure-erőforrások, amelyek az Azure-figyelő szolgáltatás által felhasznált által kibocsátott (más néven teljesítményszámlálók) Azure telemetriai adatokat. A Batch-fiók példa mérőszámok közé tartozik: készlet létrehozása események, alacsony prioritású csomópontok száma és teljes feladathoz kapcsolódó eseményekről. 

Tekintse meg a [támogatott kötegelt metrikák listája](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftbatchbatchaccounts).

Adatok gyűjtése le van:

* Alapértelmezés szerint engedélyezett a minden további konfiguráció nélkül Batch-fiók
* Generált minden 1 perc
* Nem maradnak meg automatikusan, de egy 30 napos működés közbeni előzmények rendelkezik. Részeként is megőrizni a tevékenységi metrikák [diagnosztikai naplózás](#work-with-diagnostic-logs).

### <a name="view-metrics"></a>Nézet metrikák

A Batch-fiókhoz metrikák megtekintése az Azure portálon. A **áttekintése** lapon, az a fiók alapértelmezés szerint a kulcs csomópont, a core és a feladat metrikák jeleníti meg. 

Összes Batch-fiók metrikát megtekintése: 

1. A portálon kattintson **minden szolgáltatás** > **Batch-fiókok**, majd kattintson a Batch-fiók nevét.
2. A **figyelés**, kattintson a **metrikák**.
3. Jelöljön ki legalább egy, a metrikákat. Ha azt szeretné, válassza a további erőforrás metrikák használatával a **előfizetések**, **erőforráscsoport**, **erőforrástípus**, és **erőforrás** legördülő lista.

    ![Kötegelt metrikák](media/batch-diagnostics/metrics-portal.png)

Metrikák programozott módon lekéréséhez használja az Azure-figyelő API-kat. Lásd például: [.NET metrikák beolvasása Azure figyelő](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-alerts"></a>Kötegelt metrika riasztások

Nem kötelező lépésként beállíthatja közel valós időben *metrika riasztások* , amely indul el, ha a megadott metrika értékét a hozzárendelt küszöbérték keverve használ. A riasztást állít elő egy [értesítési](../monitoring-and-diagnostics/insights-alerts-portal.md) úgy dönt, ha a riasztás "aktív" (Ha a küszöb van átlépte, és a riasztási feltétel nem teljesül), valamint ha a "Megoldásig" (Ha újra áthaladnak a küszöbérték és a következő feltételt: nincs már teljesül). 

Például előfordulhat, hogy szeretne egy metrika riasztást konfigurálhat, ha a kis prioritású virtuális gép magok száma csökken bizonyos szintjét, így módosíthatja az adott összeállításban a globálisnév-készletek.

A metrika-riasztások konfigurálása a portál:

1. Kattintson a **Minden szolgáltatás** > **Batch-fiókok** lehetőségre, majd a Batch-fiók nevére.
2. A **figyelés**, kattintson a **riasztási szabályok** > **metrika riasztás hozzáadása**.
3. Jelölje be a metrika egy riasztási feltétel (például ha egy metrika időszakon belül meghaladja egy adott értéket) és egy vagy több értesítés.

Beállíthatja azt is, a közel valós idejű riasztási használatával a [REST API](). További információkért lásd: [újabb metrika riasztások használni az Azure-portálon az Azure szolgáltatáshoz](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)
## <a name="batch-diagnostics"></a>Kötegelt diagnosztika

Azure-erőforrások az egyes erőforrások műveletet leíró által kibocsátott diagnosztikai naplók tartalmazzák. A kötegelt gyűjtse össze a következő naplók kapcsolódnak:

* **Szolgáltatás** egyedi kötegelt erőforrás élettartama során az Azure Batch szolgáltatás által kibocsátott események, például egy készletet vagy a feladatot. 

* **Metrikák** naplók a fiók szintjén. 

Alapértelmezés szerint nem engedélyezettek a diagnosztikai naplók gyűjteménye engedélyezésére szolgáló beállítások. Explicit módon engedélyezze a diagnosztikát minden figyelni kívánt Batch-fiókhoz.

### <a name="log-destinations"></a>Napló célok

Egy gyakori forgatókönyv, hogy egy Azure Storage-fiók jelölje meg célhelyként napló. Naplók tárolására az Azure Storage, hozzon létre a fiókot az naplók gyűjtésének engedélyezése előtt. A storage-fiók társított a Batch-fiók, ha dönthet úgy, hogy a fiók napló célként. 

A diagnosztikai naplók más választható célok:

* Adatfolyam-kötegelt diagnosztikai naplóeseményeket egy [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md). Az Event Hubs fogadására képes több millió esemény / másodperc, amely akkor átalakíthatja és tárolhatja bármilyen valós idejű elemzési szolgáltató használatával. 

* A diagnosztikai naplók küldése [Azure Naplóelemzés](../log-analytics/log-analytics-overview.md), amelyen az Operations Management Suite (OMS) portálon elemezheti őket, vagy a Power bi-ban vagy az Excel elemzés céljából exportálhatja őket.

> [!NOTE]
> Tárolja, vagy az Azure-szolgáltatásokkal diagnosztikai naplófájl adatok feldolgozása további költségekkel járhat. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Az kötegelt diagnosztikai naplók gyűjtésének engedélyezése

1. A portálon kattintson **minden szolgáltatás** > **Batch-fiókok**, majd kattintson a Batch-fiók nevét.
2. A **figyelés**, kattintson a **diagnosztikai naplók** > **a diagnosztika bekapcsolásához**.
3. A **diagnosztikai beállítások**, adjon meg egy nevet a beállítás, és válassza a napló cél (meglévő tárolási fiók, az Event Hubs vagy Naplóelemzési). Válassza ki az egyik vagy mindkét **ServiceLog** és **AllMetrics**.

    Válasszon egy tárfiókot, opcionálisan állítja egy megőrzési házirend. Ha nem adja meg egy megőrzési napok száma, adatok megőrzi a tárfiók élettartama során.

4. Kattintson a **Save** (Mentés) gombra.

    ![Kötegelt diagnosztika](media/batch-diagnostics/diagnostics-portal.png)

Naplózási gyűjtés engedélyezése az egyéb lehetőségei közé tartozik: Azure figyelő használni a portál a diagnosztikai beállítások konfigurálásához használja a [Resource Manager-sablon](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md), vagy az Azure PowerShell vagy az Azure parancssori felület használata. Lásd: [gyűjtése és felhasználása az Azure-erőforrások naplóadatait](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs).


### <a name="access-diagnostics-logs-in-storage"></a>Tárolási bejelentkezik hozzáférést diagnosztika

Amikor archivál kötegelt diagnosztikai naplók tárfiókokban, tárolót létrejön a tárfiók, amint egy kapcsolódó esemény akkor következik be. Blobok az alábbi elnevezési mintája alapján jönnek létre:

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
PT1H.json fájlját tartalmazza JSON-formátumú események, a blob URL-címben megadott órán belül (például h = 12). Az aktuális órában az események az előfordulásukkor lesznek a PT1H.json fájlhoz fűzve. A perc értéket (m = 00) mindig 00, mivel diagnosztikai alkalmazásnapló-események az egyes blobok óránként van felosztva. (Az összes idő (UTC) is.)


A tárfiók a diagnosztikai naplók a séma kapcsolatos további információkért lásd: [archív Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

A Storage API-k segítségével fér hozzá a tárfiókban lévő naplókat programozott módon. 

### <a name="service-log-events"></a>Szolgáltatás bejelentkezési események
Azure Batch szolgáltatás naplók, ha gyűjtött, például egy készletet vagy a feladat egyedi kötegelt erőforrás élettartama során az Azure Batch szolgáltatás által kibocsátott eseményeket tartalmaz. Minden egyes kötegelt által kibocsátott naplóz JSON formátumban. Például ez az a szervezet egy minta **készlet esemény létrehozása**:

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

A Batch szolgáltatás jelenleg a következő szolgáltatás bejelentkezési események bocsát ki. Ez a lista nem lehet teljes, mivel további események is hozzá vannak adva ez a cikk utolsó frissítése óta.

| **Szolgáltatás bejelentkezési események** |
| --- |
| [Alkalmazáskészlet létrehozása](batch-pool-create-event.md) |
| [Készlet törlése indítása](batch-pool-delete-start-event.md) |
| [Teljes készlet törlése](batch-pool-delete-complete-event.md) |
| [Készlet átméretezési indítása](batch-pool-resize-start-event.md) |
| [Teljes készlet átméretezése](batch-pool-resize-complete-event.md) |
| [A feladat indítása](batch-task-start-event.md) |
| [A feladat befejezése](batch-task-complete-event.md) |
| [A feladat sikertelen](batch-task-fail-event.md) |



## <a name="next-steps"></a>További lépések

* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* További információ [kötegelt megoldások figyelési](monitoring-overview.md).
