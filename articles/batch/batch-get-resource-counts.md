---
title: Feladatok és a csomópontok – Azure Batch száma |} A Microsoft Docs
description: Azure Batch-feladatok állapotának száma, és a számítási csomópontok, kezelhetők és figyelhetők a Batch-megoldások.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 06/29/2018
ms.author: danlep
ms.openlocfilehash: f4bad3d7058e82a246afce9502d275c7d485cb88
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009174"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Feladatok és az állapot szerint a csomópontok számolását Batch-megoldások monitorozása

Figyelése és kezelése az Azure Batch-megoldások nagyméretű, pontos száma állapotait erőforrásainak kell. Az Azure Batch beolvasni ezeket a Batch hatékony műveleteket biztosít *feladatok* és *számítási csomópontok*. Időrabló API-hívások helyett ezeket a műveleteket használatával feladatok vagy a csomópontok nagy gyűjteményeknek kapcsolatos részletes információkat ad vissza.

* [Feladat száma beolvasása] [ rest_get_task_counts] lekérdezi egy összesített száma a feladat aktív, a futó és a befejezett feladatok és a feladatok sikeres vagy sikertelen. 

  Által az egyes feladatok számában, könnyebben feladatok előrehaladásának megjelenítése egy felhasználó számára, vagy is azonosíthatja a nem várt késedelmeket és a hibák, amelyek befolyásolhatják a feladatot. Get-feladat száma Batch szolgáltatás API-verzió 2017-06-01.5.1 és a kapcsolódó SDK-k és eszközök kezdődően érhető el.

* [Készlet csomópont Counts listában] [ rest_get_node_counts] lekérdezi dedikált és az alacsony prioritású készlet számítási csomópontjain egyes lévő állapotait: létrehozását, üresjárati, offline állapotú, leállított, újraindítás, rendszerképének alaphelyzetbe állítása, elindítása és mások. 

  Az egyes csomópontok számolását, megállapítható, ha rendelkezik a megfelelő számítási erőforrásokat a feladatok futtatását, és a készletekben lehetséges problémáinak meghatározásában. Lista készlet csomópont száma Batch szolgáltatás API-verzió 2018-03-01.6.1 és a kapcsolódó SDK-k és eszközök kezdődően érhető el.

Ha a szolgáltatás, amely nem támogatja a feladat száma vagy a csomópont száma műveletek egy verziót használ, inkább listalekérdezés ezek az erőforrások számát. Listalekérdezés is használhatja a többi Batch-erőforrásokat, például alkalmazások, a fájlok és a feladatok információt szeretne kapni. Szűrők alkalmazása listázó lekérdezések kapcsolatos további információkért lásd: [listázásához létrehozás lekérdezések Batch-erőforrások hatékony](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Megszámolja a feladat állapota

A feladat száma beolvasása művelettel counts feladatok a következő állapotok szerint:

- **Aktív** – egy feladatot, amely aszinkron, és képes futtatása, de jelenleg nincs hozzárendelve egy számítási csomóponton. Egy feladat egyben `active` Ha [függő egy fölérendelt tevékenység](batch-task-dependencies.md) , amely még nem fejeződött be. 
- **Futó** – egy feladatot, amely hozzá lett rendelve egy számítási csomóponton, de még nem fejeződött be. Egy tevékenység fog számítani `running` Ha állapotában, vagy `preparing` vagy `running`, amint azt a [feladat információinak lekérése] [ rest_get_task] műveletet.
- **Befejezett** – egy feladatot, amely már nem jogosult szeretne futtatni, mert vagy fejeződött be sikeresen, vagy sikertelenül befejeződött és is kimerültek az újrapróbálkozások számát. 
- **Sikeres** -egy feladat, amelynek a feladat a végrehajtás eredménye `success`. Batch-e feladat sikeres vagy sikertelen ellenőrzésével meghatározza, hogy a `TaskExecutionResult` tulajdonságát a [executionInfo] [ rest_get_exec_info] tulajdonság.
- **Nem sikerült** egy feladatot, amelynek a feladat a végrehajtás eredménye `failure`.

A következő .NET-kódmintát bemutatja, hogyan kérheti le a feladat hibás állapot szerint: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

REST esetében hasonló mintát és más támogatott nyelv segítségével feladat számát egy feladat lekérése. 
 

### <a name="consistency-checking-for-task-counts"></a>Konzisztencia-ellenőrzés a feladat száma

A Batch biztosítja, hogy a további ellenőrzés a feladat állapota hibás ellen a rendszer több összetevőből konzisztencia-ellenőrzést végez. A nem túl valószínű esetben, hogy a konzisztencia-ellenőrzés hibákat talál, a Batch javítja a konzisztencia-ellenőrzés eredményei alapján a feladatok száma beolvasása művelet eredményét.

A `validationStatus` a válaszban a tulajdonság azt jelzi, hogy Batch végrehajtani a konzisztencia-ellenőrzést. Ha a Batch nem jelentkezett állapotban tartani a rendszer a tényleges állapotok beleszámít a `validationStatus` tulajdonsága `unvalidated`. Teljesítménybeli megfontolások miatt az a Batch nem hajtja végre a konzisztencia-ellenőrzést, ha a feladat magában foglalja a több mint 200 000 feladatokat, ezért a `validationStatus` tulajdonsága `unvalidated` ebben az esetben. (A feladatok száma nem áll nem feltétlenül megfelelő ebben az esetben is korlátozott adatvesztés nem valószínű, mivel.) 

A feladat állapota megváltozik, amikor az aggregációs folyamat dolgozza fel a módosítás néhány másodpercen belül. A feladat száma beolvasása művelet adott időtartamon belül a frissített feladat számát jeleníti meg. Azonban ha az aggregációs folyamat egy feladat állapotmódosításra tévesztések, majd ezt a módosítást nincs regisztrálva a következő érvényesítési pass-ig. Ebben az időszakban feladat számát a kihagyott esemény miatt némileg pontatlan lehet, de azok a következő érvényesítési fázist a szabálymezők.

## <a name="node-state-counts"></a>Csomópont állapota száma

A lista készlet csomópont Counts műveletek számát számítási csomópontok minden készlet a következő állapotok szerint. Külön összesített száma a dedikált csomópontokat és minden készlet alacsony prioritású csomópontok biztosítják.

- **Létrehozás** – az Azure által kiosztott virtuális Gépet, amely még nem indult egy készlet csatlakozni.
- **Üresjárati** – egy rendelkezésre álló számítási csomópont, amely jelenleg nem fut egy feladat.
- **LeavingPool** -csomópont, amely a készlet hagyják el, mert a felhasználónak explicit módon távolítja el, vagy a készlet átméretezés vagy az automatikus skálázás nem működik.
- **Kapcsolat nélküli** -csomópont a Batch nem használható új feladatok ütemezését.
- **A leállított** – egy alacsony prioritású csomópontot a készletből lett eltávolítva, mert az Azure a virtuális gép visszaigényelt. A `preempted` csomópont lehet újrainicializálni, ha helyettesítő alacsony prioritású virtuális gép kapacitás érhető el.
- **Rendszer újraindítása** -tartalmazó csomópont újraindul.
- **Rendszerképének alaphelyzetbe állítása** -csomópont, amelyen az operációs rendszer újratelepítése.
- **Futó** – (nem az indítási tevékenység) egy vagy több feladat futtató csomópont.
- **Kezdési** -csomópont, amelyen a Batch szolgáltatás indítása folyamatban van. 
- **StartTaskFailed** -csomópont, amelyen a [kezdő tevékenység] [ rest_start_task] sikertelen volt, és az összes újrapróbálkozás kimerül, és amelyen `waitForSuccess` az indítási tevékenység van beállítva. A csomópont már nem használható a feladatok futtatásához.
- **Ismeretlen** - csomópont, amely megszakadt a kapcsolat a Batch szolgáltatással, és amelynek állapot nem ismert.
- **Használhatatlan** -csomópont, amely nem használható fel a feladat-végrehajtási hibák miatt.
- **WaitingForStartTask** -csomópont, amelyen az indítási tevékenység fut, de `waitForSuccess` van állítva, és az indítási feladat nem fejeződött be.

Az alábbi C#-kódrészlet bemutatja, hogyan listázhatja a csomópont száma a jelenlegi fiókot az összes készletek:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
Az alábbi C#-kódrészlet bemutatja, hogyan listázhatja a csomópont száma a jelenlegi fiókot a megadott készlet.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```
REST esetében hasonló mintát és más támogatott nyelv segítségével csomópont hibás készletek lekérése.
 
## <a name="next-steps"></a>További lépések

* A Batch szolgáltatás fogalmairól és funkcióiról további információt [a Batch funkcióinak áttekintésében](batch-api-basics.md) talál. A cikk ismerteti az elsődleges Batch-erőforrásokat, például a készletek, számítási csomópontok, feladatok és tevékenységek, és a szolgáltatás funkcióit.

* További információ a szűrők alkalmazását a lekérdezéseket, amelyek a Batch-erőforrások listázása: [listázásához létrehozás lekérdezések Batch-erőforrások hatékony](batch-efficient-list-queries.md).


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get#executionInfo
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

