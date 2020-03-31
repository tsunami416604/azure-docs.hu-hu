---
title: Feladatok és csomópontok állapotainak megszámlálása - Azure Batch | Microsoft dokumentumok
description: Számolja meg az Azure Batch-feladatok állapotát, és számolja a számítási csomópontokat a Batch-megoldások kezeléséhez és figyeléséhez.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a7b58e96918d26851812aa96c18043121c081e94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023922"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Kötegmegoldások figyelése feladatok és csomópontok állapot szerinti számlálásával

A nagyméretű Azure Batch-megoldások figyeléséhez és kezeléséhez pontos számú erőforrásra van szüksége a különböző állapotokban. Az Azure Batch hatékony műveleteket biztosít a *Batch-feladatok* és a számítási csomópontok ezek nek a be- és *számítási számokhoz*való beszerezéséhez. Ezeket a műveleteket használja a potenciálisan időigényes listalekérdezések helyett, amelyek részletes információkat adnak vissza a feladatok vagy csomópontok nagy gyűjteményeiről.

* [A Feladatszámlálók][rest_get_task_counts] bekésezése a feladat aktív, futó és befejezett feladatainak, valamint a sikeres vagy sikertelen feladatokösszesített számát kapja. 

  Az egyes állapotok feladatainak számlálásával könnyebben megjelenítheti a feladat előrehaladását a felhasználó számára, vagy észlelheti a projektet befolyásoló váratlan késéseket vagy hibákat. A Feladatszámlálók beszerzése a Batch Service API 2017-06-01.5.1-es verziójától, valamint a kapcsolódó SDK-któl és eszközöktől érhető el.

* [Listakészlet-csomópontok száma][rest_get_node_counts] leveszi a dedikált és alacsony prioritású számítási csomópontok száma minden készlet, amely a különböző állapotokban: létrehozása, tétlen, offline, preempted, újraindítás, újraimaging, indítás, és mások. 

  Az egyes állapotok csomópontjainak számlálásával meghatározhatja, hogy rendelkezik-e a feladatok futtatásához szükséges számítási erőforrásokkal, és azonosíthatja a készletekkel kapcsolatos lehetséges problémákat. A listakészlet-csomópontok száma a Batch Service API 2018-03-01.6.1-es verziójától és a kapcsolódó SDK-któl és eszközöktől érhető el.

Ha a szolgáltatás olyan verzióját használja, amely nem támogatja a feladatszám- vagy csomópontszámlálási műveleteket, használjon listalekérdezést az erőforrások megszámlálásához. Listalekérdezéssel más Batch-erőforrásokkal, például alkalmazásokkal, fájlokkal és feladatokkal kapcsolatos információkat is kaphat. A szűrők listalekérdezésekre történő alkalmazásáról a [Lekérdezések létrehozása a Batch-erőforrások hatékony listázásához című témakörben](batch-efficient-list-queries.md)talál további információt.

## <a name="task-state-counts"></a>A feladat állapota számít

A Feladatok száma begetése művelet a következő állapotok szerint számolja a feladatokat:

- **Aktív** – Várólistán lévő és futtatható feladat, de jelenleg nincs hozzárendelve egy számítási csomóponthoz. A tevékenység `active` akkor is az, ha olyan [fölérendelt tevékenységtől függ,](batch-task-dependencies.md) amely még nem fejeződött be. 
- **Futó** – Egy számítási csomóponthoz rendelt feladat, de még nem fejeződött be. Egy feladat akkor `running` számít bele, `preparing` ha `running`az állapota vagy a , amint azt a [Feladatművelet információinak beszereznie][rest_get_task] jelzi.
- **Befejezett** – Olyan feladat, amely már nem futtatható, mert vagy sikeresen befejeződött, vagy sikertelenül fejeződött be, és kimerítette az újrapróbálkozási korlátot is. 
- **Sikerült** - Olyan feladat, amelynek `success`végrehajtása a feladat végrehajtása. A Batch a executionInfo tulajdonság `TaskExecutionResult` tulajdonságának ellenőrzésével határozza meg, hogy egy feladat sikeres vagy sikertelen [volt-e.][rest_get_exec_info]
- **Nem sikerült** Olyan feladat, amelynek `failure`végrehajtása a feladat végrehajtása.

A következő .NET kódminta bemutatja, hogyan lehet a feladatszámokat állapot szerint beolvasni: 

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

A REST és más támogatott nyelvek hez hasonló minta használatával lekéri a feladatok számát egy feladathoz. 

> [!NOTE]
> Batch Service API-verziók 2018-08-01.7.0 előtt is ad vissza egy `validationStatus` tulajdonságot a Feladatszámak begetése válaszban. Ez a tulajdonság azt jelzi, hogy a Batch ellenőrizte-e az állapotszámokat a List Tasks API-ban jelentett állapotokkal való konzisztenciát. Az érték `validated` csak azt jelzi, hogy a Batch legalább egyszer ellenőrizte a konzisztenciát a feladathoz. A `validationStatus` tulajdonság értéke nem jelzi, hogy a Feladatszámlálók bekerülési értéke jelenleg naprakész-e.
>

## <a name="node-state-counts"></a>A csomópont állapota számít

A listakészlet csomópontjainak száma művelet az egyes készletek ben lévő következő állapotok szerint számolja a számítási csomópontokat. Külön összesítő szám dedikált csomópontok és alacsony prioritású csomópontok minden készletben.

- **Létrehozása** – Egy Azure által lefoglalt virtuális gép, amely még nem kezdett el csatlakozni a készlethez.
- **Tétlen** – egy rendelkezésre álló számítási csomópont, amely jelenleg nem fut egy feladat.
- **LeavingPool** – Olyan csomópont, amely elhagyja a készletet, vagy azért, mert a felhasználó kifejezetten eltávolította, vagy mert a készlet átméretezése vagy automatikus leskálázása.
- **Kapcsolat nélküli** – Olyan csomópont, amelyet a Batch nem használhat új tevékenységek ütemezéséhez.
- **Preempted** – egy alacsony prioritású csomópont, amely azért lett eltávolítva a készletből, mert az Azure visszaszerezte a virtuális gép. Egy `preempted` csomópont újrainicializálható, ha csere alacsony prioritású virtuálisgép-kapacitás érhető el.
- **Újraindítás** – Egy újrainduló csomópont.
- **Reimaging** - Az a csomópont, amelyen az operációs rendszer újratelepítése folyamatban van.
- **Futás** – Olyan csomópont, amely egy vagy több feladatot futtat (a kezdő feladaton kívül).
- **Indítás** – Olyan csomópont, amelyen a Batch szolgáltatás elindul. 
- **StartTaskFailed** - Olyan csomópont, amelyen a [kezdő feladat][rest_start_task] sikertelen volt, és kimerítette az összes újrapróbálkozást, és amelyen `waitForSuccess` be van állítva a kezdési feladaton. A csomópont nem használható feladatok futtatásához.
- **Ismeretlen** – Olyan csomópont, amely elvesztette a kapcsolatot a Batch szolgáltatással, és amelynek állapota nem ismert.
- **Használhatatlan** – Olyan csomópont, amely hibák miatt nem használható feladatvégrehajtáshoz.
- **WaitingForStartTask** – Olyan csomópont, amelyen a `waitForSuccess` kezdő feladat elindult, de be van állítva, és a kezdési feladat még nem fejeződött be.

A következő C# kódrészlet bemutatja, hogyan listázhatok az aktuális fiók összes készletének csomópontszámait:

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
A következő C# kódrészlet bemutatja, hogyan listázható az aktuális fiók adott készletének csomópontszáma.

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
A REST és más támogatott nyelvek hez hasonló minta használatával lekéri a készletek csomópontszámát.
 
## <a name="next-steps"></a>További lépések

* A Batch szolgáltatás fogalmairól és funkcióiról további információt [a Batch funkcióinak áttekintésében](batch-api-basics.md) talál. A cikk ismerteti az elsődleges Batch-erőforrásokat, például a készleteket, a számítási csomópontokat, a feladatokat és a feladatokat, és áttekintést nyújt a szolgáltatás szolgáltatásairól.

* A kötegelt erőforrásokat tartalmazó lekérdezésekre alkalmazott szűrőkről a [Lekérdezések létrehozása a Kötegerőforrások hatékony listázásához című témakörben](batch-efficient-list-queries.md)talál további információt.


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

