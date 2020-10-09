---
title: Feladatok párhuzamos futtatása a számítási erőforrások optimalizálásához
description: A hatékonyság és az alacsonyabb költségek növelése kevesebb számítási csomópont használatával és egyidejű feladatok futtatása egy Azure Batch készlet minden csomópontján
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 3c3a81aa624ccc67c0f9e8ec23e5ef9b8e61c724
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850999"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Feladatok futtatása párhuzamosan a Batch számítási csomópontok használatának maximalizálása érdekében

Ha egynél több feladatot futtat egyszerre a Azure Batch készlet minden számítási csomópontján, az erőforrás-használatot a készlet kisebb számú csomópontján is maximalizálhatja. Bizonyos munkaterhelések esetében ez rövidebb feladathoz és alacsonyabb költséghatékonysághoz vezethet.

Habár egyes forgatókönyvek kihasználják az összes csomópont erőforrásának egyetlen feladathoz való kiosztását, számos esetben előnyös lehet, ha több feladat is lehetővé teszi az erőforrások megosztását:

* **Minimalizálja az adatátvitelt** , ha a feladatok képesek megosztani az adatok megosztását. Ebben az esetben jelentősen csökkentheti az adatátviteli díjakat a megosztott adatok kisebb számú csomópontra másolásával és a feladatok párhuzamos végrehajtásával az egyes csomópontokon. Ez különösen akkor érvényes, ha az egyes csomópontokra másolandó adatforgalomot át kell vinni a földrajzi régiók között.
* A **memória használatának maximalizálása** , ha a feladatok nagy mennyiségű memóriát igényelnek, de csak rövid időszakokban és a végrehajtás során változó időpontokban. Kevesebb, de nagyobb számítási csomópontot is alkalmazhat több memóriával az ilyen tüskék hatékony kezelésére. Ezek a csomópontok több, párhuzamosan futó feladattal rendelkeznek minden csomóponton, de minden feladat különböző időpontokban kihasználja a csomópontok bőséges memóriáját.
* A **csomópontok számának csökkentése** , ha csomópontok közötti kommunikációra van szükség a készleten belül. A csomópontok közötti kommunikációra konfigurált készletek jelenleg 50 számítási csomópontra korlátozódnak. Ha egy adott készlet minden csomópontja képes párhuzamosan végrehajtani a feladatokat, több feladat is végrehajtható egyszerre.
* Helyszíni **számítási fürt replikálása**, például amikor először helyez át egy számítási környezetet az Azure-ba. Ha a jelenlegi helyszíni megoldás több feladatot hajt végre egy számítási csomóponton, akkor a csomópontok maximális számát növelheti a konfiguráció alaposabb tükrözéséhez.

## <a name="example-scenario"></a>Példaforgatókönyv
Példa a párhuzamos feladatok végrehajtásának előnyeinek szemléltetésére: tegyük fel, hogy a feladathoz szükséges CPU-és memória-követelmények olyanok, mint a [szabványos \_ D1](../cloud-services/cloud-services-sizes-specs.md) -csomópontok. Azonban ahhoz, hogy a feladatot a szükséges időn belül be lehessen fejezni, a csomópontok 1 000-ra van szükség.

A standard \_ D1-es és 1 CPU-csomópontok használata helyett olyan [szabványos \_ D14](../cloud-services/cloud-services-sizes-specs.md) -csomópontokat használhat, amelyekben 16 mag van, és lehetővé teszi a párhuzamos feladatok végrehajtását. Ezért *16 alkalommal kevesebb csomópontot* lehet használni – a 1 000 csomópontok helyett csak 63 szükséges. Emellett, ha nagyméretű alkalmazásfájlok vagy hivatkozási adatok szükségesek az egyes csomópontokhoz, a feladatok időtartama és a hatékonyság ismét javul, mivel az adatok csak 63 csomópontokra másolódnak.

## <a name="enable-parallel-task-execution"></a>Párhuzamos feladatok végrehajtásának engedélyezése
A számítási csomópontokat a készlet szintjén konfigurálhatja párhuzamos feladatok végrehajtásához. A Batch .NET-kódtár használatával készlet létrehozásakor állítsa be a [CloudPool. TaskSlotsPerNode][maxtasks_net] tulajdonságot. Ha a Batch-REST API használja, állítsa a [taskSlotsPerNode][rest_addpool] elemet a kérelem törzsében a készlet létrehozása során.

Azure Batch lehetővé teszi, hogy a csomópontok száma legfeljebb (4x) a csomóponti magok számát adja meg. Ha például a készlet a "Large" (négy mag) méretű csomópontokkal van konfigurálva, akkor előfordulhat, `taskSlotsPerNode` hogy 16 értékre van állítva. Azonban függetlenül attól, hogy a csomópont hány magot tartalmaz, a csomópontok száma legfeljebb 256 feladat lehet. A csomópontok méretével kapcsolatos további részletekért lásd: [Cloud Services méretei](../cloud-services/cloud-services-sizes-specs.md). A szolgáltatási korlátokkal kapcsolatos további információkért lásd: [kvóták és korlátozások a Azure batch szolgáltatáshoz](batch-quota-limit.md).

> [!TIP]
> Ügyeljen arra, hogy a `taskSlotsPerNode` készlethez tartozó [autoskálázási képlet][enable_autoscaling] létrehozásakor vegye figyelembe az értéket. Előfordulhat például, hogy egy olyan képletet, amely kiértékeli, a `$RunningTasks` csomópontok számának növekedése jelentősen befolyásolhatja. További információ: [számítási csomópontok automatikus méretezése egy Azure batch-készletben](batch-automatic-scaling.md) .
>
>

> [!NOTE]
> Az `taskSlotsPerNode` Element és a [TaskSlotsPerNode][maxtasks_net] tulajdonság csak a készlet létrehozási idején állítható be. A készlet már létrejötte után nem módosítható.
>
>

## <a name="distribution-of-tasks"></a>Feladatok eloszlása
Ha a készletben lévő számítási csomópontok egyszerre hajtják végre a feladatokat, fontos megadnia, hogyan szeretné elosztani a feladatokat a készlet csomópontjai között.

A [CloudPool. TaskSchedulingPolicy][task_schedule] tulajdonság használatával megadhatja, hogy a tevékenységek egyenletesen legyenek hozzárendelve a készlet összes csomópontján ("terjesztés"). Azt is megadhatja, hogy a lehető legtöbb feladat legyen hozzárendelve az egyes csomópontokhoz, mielőtt a feladatok hozzá vannak rendelve a készlet egy másik csomópontjára ("csomagolás").

Példa erre a funkcióra, vegye figyelembe a [standard \_ D14](../cloud-services/cloud-services-sizes-specs.md) -csomópontok készletét (a fenti példában), amely [CloudPool. TaskSlotsPerNode][maxtasks_net] értékkel van konfigurálva. 16. Ha a [CloudPool. TaskSchedulingPolicy][task_schedule] konfigurálva van egy [ComputeNodeFillType][fill_type] , az maximalizálja az egyes csomópontok 16 maggal való *használatát, és*lehetővé teszi az automatikus [skálázási készlet](batch-automatic-scaling.md) számára a nem használt csomópontok kivágását a készletből (a csomópontok nélkül hozzárendelt feladatokat). Ez csökkenti az erőforrás-használatot, és pénzt takarít meg.

## <a name="variable-slots-per-task"></a>Változó tárolóhelyek/feladat
A feladat az [CloudTask. RequiredSlots][taskslots_net] tulajdonsággal határozható meg, hogy hány tárolóhelyre van szükség a számítási csomóponton való futtatáshoz, az alapértelmezett érték pedig 1. Ha a feladatok különböző súlyozással rendelkeznek a számítási csomópont erőforrás-használatával kapcsolatban, beállíthatja a változó feladat tárolóhelyét, így minden számítási csomópont ésszerű számú párhuzamosan futó feladatot rendelkezhet a rendszererőforrások, például a processzor vagy a memória túlnyomó része nélkül.

Egy tulajdonsággal rendelkező készlet esetében például `taskSlotsPerNode = 8` elküldheti a több magot igénylő CPU-igényes feladatokat a alkalmazásban `requiredSlots = 8` , míg más feladatokat is `requiredSlots = 1` . Ha ez a vegyes munkaterhelés a készletre van ütemezve, a CPU-igényes feladatok kizárólag a számítási csomóponton futnak, míg más tevékenységek egyidejűleg (legfeljebb nyolc feladattal) futhatnak más csomópontokon. Ez segít a számítási csomópontok közötti terheléselosztásban és az erőforrás-használat hatékonyságának növelésében.

> [!TIP]
> A változó feladat tárolóhelyek használata esetén előfordulhat, hogy a több szükséges tárolóhelytel rendelkező nagyméretű feladatok átmenetileg nem lesznek ütemezve, mert nem áll rendelkezésre elegendő tárolóhely a számítási csomóponton, még akkor is, ha az egyes csomópontokon még mindig tétlen tárolóhelyek találhatók. Ezeknek a feladatoknak a feladatait a feladatok prioritásának növelésével növelheti, hogy a csomópontokon elérhető bővítőhelyek is versenyképesek legyenek.
>
> A Batch szolgáltatás a [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) is kibocsátja, ha nem tud ütemezni egy feladatot, miközben a szükséges bővítőhelyek elérhetetlenné válnak. Ezt az eseményt figyelheti arra, hogy felderítse a lehetséges feladatok ütemezésének megakadt hibáját, és ennek megfelelően végezze el a mérséklést.
>

> [!NOTE]
> Ne határozza meg `requiredSlots` , hogy a tevékenység a készletnél nagyobb legyen `taskSlotsPerNode` . Ez azt eredményezi, hogy a feladat soha nem futtatható. A Batch szolgáltatás jelenleg nem hajtja végre ezt az ellenőrzést a feladatok elküldésekor, mert előfordulhat, hogy a feladathoz nem tartozik készlet a beküldési időpontban, vagy a letiltási/ismételt engedélyezéssel eltérő készletre változik.
>

## <a name="batch-net-example"></a>Batch .NET-példa
A következő [Batch .net][api_net] API-kódrészletek bemutatják, hogyan hozhat létre csomóponton több feladattal rendelkező készletet, és hogyan küldhet el feladatokat a szükséges tárolóhelyekkel.

### <a name="create-pool"></a>Készlet létrehozása
Ez a kódrészlet egy olyan készlet létrehozására vonatkozó kérést mutat be, amely négy olyan csomópontot tartalmaz, amelyek egy csomóponton engedélyezett négy feladattal rendelkeznek. Meghatározza azt a feladatütemezés-házirendet, amely minden egyes csomópontot kitölti a feladatok a készlet egy másik csomópontjára való hozzárendelését megelőzően. A készleteknek a Batch .NET API használatával történő hozzáadásával kapcsolatos további információkért lásd: [BatchClient. PoolOperations. CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-task-with-required-slots"></a>Feladat létrehozása a szükséges tárolóhelyekkel
Ez a kódrészlet egy nem alapértelmezett állapotú feladatot hoz létre `requiredSlots` . Ez a feladat csak akkor fut le, ha rendelkezésre áll elegendő szabad tárolóhely a számítási csomóponton.
```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Számítási csomópontok listázása a feladatok és a bővítőhelyek futtatásához
Ez a kódrészlet a készlet összes számítási csomópontját listázza, és kinyomtatja az egyes csomópontokon futó feladatok és feladatok tárolóhelyek számát.
```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Feladathoz tartozó tevékenységek felsorolása
Ez a kódrészlet beolvassa a feladat számát a feladathoz, amely magában foglalja a feladatok és a feladatok tárolóhelyek számát a feladat állapotában.
```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch – REST-példa
Ez a [Batch Rest][api_rest] API-kódrészlet egy olyan készlet létrehozására vonatkozó kérést mutat be, amely két nagy csomópontot tartalmaz, és legfeljebb négy feladattal rendelkezik. A készletek a REST API használatával történő hozzáadásával kapcsolatos további információkért lásd [a készlet hozzáadása egy fiókhoz][rest_addpool]című témakört.

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

Ez a kódrészlet egy olyan feladat hozzáadására vonatkozó kérést mutat be, amely nem alapértelmezett `requiredSlots` . Ez a feladat csak akkor fut le, ha rendelkezésre áll elegendő szabad tárolóhely a számítási csomóponton.
```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample"></a>Kódminta
A GitHubon a [ParallelNodeTasks][parallel_tasks_sample] -projekt az [CloudPool. TaskSlotsPerNode][maxtasks_net] tulajdonság használatát mutatja be.

Ez a C# konzolos alkalmazás a [Batch .net][api_net] -függvénytárat használja egy vagy több számítási csomóponttal rendelkező készlet létrehozásához. Konfigurálható számú feladatot hajt végre ezeken a csomópontokon a változó terhelésének szimulálása érdekében. Az alkalmazás kimenete határozza meg, hogy mely csomópontok hajtják végre az egyes feladatokat. Az alkalmazás a feladatok paramétereinek és időtartamának összegzését is tartalmazza. A minta alkalmazás két különböző futtatásának kimenetének összefoglaló része alább látható.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A minta alkalmazás első végrehajtása azt mutatja, hogy a készlet egyetlen csomópontja és egy feladat alapértelmezett beállítása egy csomóponton, a feladat időtartama több mint 30 percet vesz igénybe.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

A minta második futtatása a feladatok időtartamának jelentős csökkenését mutatja. Ennek az az oka, hogy a készletet egy csomóponton belül négy feladattal konfigurálták, ami lehetővé teszi a párhuzamos feladatok végrehajtását a feladat közel egynegyedében való végrehajtásához.

> [!NOTE]
> A fenti összegzésekben a feladatok időtartama nem tartalmazza a készlet létrehozásának idejét. A fenti feladatok mindegyike olyan korábban létrehozott készletekbe lett elküldve, amelyek számítási csomópontjai *tétlen* állapotban voltak a beküldési időpontban.
>
>

## <a name="next-steps"></a>Következő lépések
### <a name="batch-explorer-heat-map"></a>Batch Explorer Heat Térkép
A [Batch Explorer][batch_labs] egy ingyenes, gazdag funkcionalitású, önálló ügyfél-eszköz, amely a Azure batch alkalmazások létrehozását, hibakeresését és figyelését segíti elő. Batch Explorer tartalmaz egy *hő-hozzárendelési* funkciót, amely vizualizációt biztosít a feladatok végrehajtásához. A [ParallelTasks][parallel_tasks_sample] -minta alkalmazás végrehajtásakor a Heat Map funkcióval egyszerűen megjelenítheti a párhuzamos feladatok végrehajtását az egyes csomópontokon.


[api_net]: /dotnet/api/microsoft.azure.batch
[api_rest]: /rest/api/batchservice/
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[enable_autoscaling]: /rest/api/batchservice/pool/enableautoscale
[fill_type]: /dotnet/api/microsoft.azure.batch.common.computenodefilltype
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: /dotnet/api/microsoft.azure.batch.cloudpool
[rest_addpool]: /rest/api/batchservice/pool/add
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: /dotnet/api/microsoft.azure.batch.pooloperations
[task_schedule]: /dotnet/api/microsoft.azure.batch.cloudpool
[taskslots_net]: /dotnet/api/microsoft.azure.batch.cloudtask.requiredslots
