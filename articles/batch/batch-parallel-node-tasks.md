---
title: Párhuzamosan futtathatja a feladatokat a számítási erőforrások hatékony használatához – Azure Batch | Microsoft Docs
description: A hatékonyság és az alacsonyabb költségek növelése kevesebb számítási csomópont használatával és egyidejű feladatok futtatása egy Azure Batch készlet minden csomópontján
services: batch
documentationcenter: .net
author: ju-shim
manager: gwallace
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: jushiman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a47cbbf11117197d6d00d532fb0321d284c56b7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026807"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Feladatok futtatása párhuzamosan a Batch számítási csomópontok használatának maximalizálása érdekében 

Ha egynél több feladatot futtat egyszerre a Azure Batch készlet minden számítási csomópontján, az erőforrás-használatot a készlet kisebb számú csomópontján is maximalizálhatja. Bizonyos munkaterhelések esetében ez rövidebb feladathoz és alacsonyabb költséghatékonysághoz vezethet.

Habár egyes forgatókönyvek kihasználják az összes csomópont erőforrásának egyetlen feladathoz való kiosztását, számos esetben előnyös lehet, ha több feladat is lehetővé teszi az erőforrások megosztását:

* **Minimalizálja az adatátvitelt** , ha a feladatok képesek megosztani az adatok megosztását. Ebben az esetben jelentősen csökkentheti az adatátviteli díjakat a megosztott adatok kisebb számú csomópontra másolásával és a feladatok párhuzamos végrehajtásával az egyes csomópontokon. Ez különösen akkor érvényes, ha az egyes csomópontokra másolandó adatforgalomot át kell vinni a földrajzi régiók között.
* A **memória használatának maximalizálása** , ha a feladatok nagy mennyiségű memóriát igényelnek, de csak rövid időszakokban és a végrehajtás során változó időpontokban. Kevesebb, de nagyobb számítási csomópontot is alkalmazhat több memóriával az ilyen tüskék hatékony kezelésére. Ezek a csomópontok több, párhuzamosan futó feladattal rendelkeznek minden csomóponton, de minden feladat különböző időpontokban kihasználja a csomópontok bőséges memóriáját.
* A **csomópontok számának csökkentése** , ha csomópontok közötti kommunikációra van szükség a készleten belül. A csomópontok közötti kommunikációra konfigurált készletek jelenleg 50 számítási csomópontra korlátozódnak. Ha egy adott készlet minden csomópontja képes párhuzamosan végrehajtani a feladatokat, több feladat is végrehajtható egyszerre.
* Helyszíni **számítási fürt replikálása**, például amikor először helyez át egy számítási környezetet az Azure-ba. Ha a jelenlegi helyszíni megoldás több feladatot hajt végre egy számítási csomóponton, akkor a csomópontok maximális számát növelheti a konfiguráció alaposabb tükrözéséhez.

## <a name="example-scenario"></a>Példaforgatókönyv
Példa a párhuzamos feladatok végrehajtásának előnyeinek szemléltetésére: tegyük fel, hogy a feladathoz olyan CPU-és memória-követelmények tartoznak, mint a [szabványos\_D1](../cloud-services/cloud-services-sizes-specs.md) -csomópontok. Azonban ahhoz, hogy a feladatot a szükséges időn belül be lehessen fejezni, a csomópontok 1 000-ra van szükség.

A standard\_D1-csomópontok használata helyett 1 CPU mag van, használhat [standard\_D14](../cloud-services/cloud-services-sizes-specs.md) -csomópontokat, amelyek mindegyike 16 maggal rendelkezik, és lehetővé teszi a párhuzamos feladatok végrehajtását. Ezért *16 alkalommal kevesebb csomópontot* lehet használni – a 1 000 csomópontok helyett csak 63 szükséges. Emellett, ha nagyméretű alkalmazásfájlok vagy hivatkozási adatok szükségesek az egyes csomópontokhoz, a feladatok időtartama és a hatékonyság ismét javul, mivel az adatok csak 63 csomópontokra másolódnak.

## <a name="enable-parallel-task-execution"></a>Párhuzamos feladatok végrehajtásának engedélyezése
A számítási csomópontokat a készlet szintjén konfigurálhatja párhuzamos feladatok végrehajtásához. A Batch .NET-kódtár használatával készlet létrehozásakor állítsa be a [CloudPool. MaxTasksPerComputeNode][maxtasks_net] tulajdonságot. Ha a Batch-REST API használja, állítsa a [maxTasksPerNode][rest_addpool] elemet a kérelem törzsében a készlet létrehozása során.

Azure Batch lehetővé teszi, hogy a csomópontok száma legfeljebb (4x) legyen a fő csomópontok számánál. Ha például a készlet a "Large" (négy mag) méretű csomópontokkal van konfigurálva, akkor a `maxTasksPerNode` 16-ra lehet beállítani. Függetlenül attól, hogy a csomópont hány magot tartalmaz, a csomópontok száma legfeljebb 256 feladat lehet. A csomópontok méretével kapcsolatos további részletekért lásd: [Cloud Services méretei](../cloud-services/cloud-services-sizes-specs.md). A szolgáltatási korlátokkal kapcsolatos további információkért lásd: [kvóták és korlátozások a Azure batch szolgáltatáshoz](batch-quota-limit.md).

> [!TIP]
> Ügyeljen arra, hogy a készlethez tartozó [autoskálázási képlet][enable_autoscaling] létrehozásakor vegye figyelembe a `maxTasksPerNode` értéket. Egy `$RunningTasks` kiértékelésére szolgáló képlet például jelentősen befolyásolhatja a feladatok egy csomóponton belüli növekedését. További információ: [számítási csomópontok automatikus méretezése egy Azure batch-készletben](batch-automatic-scaling.md) .
>
>

## <a name="distribution-of-tasks"></a>Feladatok eloszlása
Ha a készletben lévő számítási csomópontok egyszerre hajtják végre a feladatokat, fontos megadnia, hogyan szeretné elosztani a feladatokat a készlet csomópontjai között.

A [CloudPool. TaskSchedulingPolicy][task_schedule] tulajdonság használatával megadhatja, hogy a tevékenységek egyenletesen legyenek hozzárendelve a készlet összes csomópontján ("terjesztés"). Azt is megadhatja, hogy a lehető legtöbb feladat legyen hozzárendelve az egyes csomópontokhoz, mielőtt a feladatok hozzá vannak rendelve a készlet egy másik csomópontjára ("csomagolás").

Példa erre a funkcióra, vegye figyelembe a [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) -csomópontok készletét (a fenti példában), amely a [CloudPool. MaxTasksPerComputeNode][maxtasks_net] értékkel van konfigurálva. 16. Ha a [CloudPool. TaskSchedulingPolicy][task_schedule] konfigurálva van egy [ComputeNodeFillType][fill_type] , az maximalizálja az egyes csomópontok 16 maggal való *használatát, és*lehetővé teszi az automatikus [skálázási készlet](batch-automatic-scaling.md) számára a nem használt csomópontok kivágását a készletből (a csomópontok nélkül hozzárendelt feladatokat). Ez csökkenti az erőforrás-használatot, és pénzt takarít meg.

## <a name="batch-net-example"></a>Batch .NET-példa
Ez a [Batch .net][api_net] API-kódrészlet egy olyan készlet létrehozására vonatkozó kérést mutat be, amely egy csomóponton legfeljebb négy feladattal rendelkező négy csomópontot tartalmaz. Meghatározza azt a feladatütemezés-házirendet, amely minden egyes csomópontot kitölti a feladatok a készlet egy másik csomópontjára való hozzárendelését megelőzően. A készleteknek a Batch .NET API használatával történő hozzáadásával kapcsolatos további információkért lásd: [BatchClient. PoolOperations. CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
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
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> A `maxTasksPerNode` elem és a [MaxTasksPerComputeNode][maxtasks_net] tulajdonság csak a készlet létrehozási idején állítható be. A készlet már létrejötte után nem módosítható.
>
>

## <a name="code-sample"></a>Kódminta
A GitHubon a [ParallelNodeTasks][parallel_tasks_sample] -projekt az [CloudPool. MaxTasksPerComputeNode][maxtasks_net] tulajdonság használatát mutatja be.

Ez C# a konzolszoftver a [Batch .net][api_net] -kódtár használatával hoz létre egy vagy több számítási csomóponttal rendelkező készletet. Konfigurálható számú feladatot hajt végre ezeken a csomópontokon a változó terhelésének szimulálása érdekében. Az alkalmazás kimenete határozza meg, hogy mely csomópontok hajtják végre az egyes feladatokat. Az alkalmazás a feladatok paramétereinek és időtartamának összegzését is tartalmazza. A minta alkalmazás két különböző futtatásának kimenetének összefoglaló része alább látható.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A minta alkalmazás első végrehajtása azt mutatja, hogy a készlet egyetlen csomópontja és egy feladat alapértelmezett beállítása egy csomóponton, a feladat időtartama több mint 30 percet vesz igénybe.

```
Nodes: 1
Node size: large
Max tasks per node: 4
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


[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

