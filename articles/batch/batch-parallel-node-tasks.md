---
title: Feladatok futtatása Azure Batch a számítási erőforrások hatékony - párhuzamos |} A Microsoft Docs
description: Növelheti a hatékonyságot és a költségek csökkentését Azure Batch-készlet minden csomópontján kevesebb számítási csomópontok és futó párhuzamos feladatok használatával
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6c4fd63213eaaf402bd72b1754ba50a9ea35e965
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475431"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Maximalizálja a Batch használatát, egyidejűleg feladatokat futtató számítási csomópontok 

Több tevékenység fut egyidejűleg az Azure Batch-készletben minden számítási csomóponton, kihasználhatja a készletben lévő csomópontok kevesebb erőforrás-használata. Bizonyos számítási feladatokhoz emiatt a rövidebb feladat idővel és alacsony költségek mellett.

Bár bizonyos esetekben előnyös dedikálni minden csomópont-erőforrás, egy feladat, több olyan helyzet lehetővé teszi az erőforrások megosztásához több feladat előnyei:

* **Minimalizálja a adatátvitel** mikor feladatok tudnak adatokat megosztani. Ebben a forgatókönyvben jelentősen csökkentheti adatátviteli díjak másolása a megosztott adatok kisebb számú csomópontra, és a feladatok végrehajtása minden egyes csomóponton párhuzamosan. Ez különösen igaz, ha minden egyes csomópont kell másolni az adatokat át kell vinni a földrajzi régiók között.
* **Memóriahasználat maximalizálása** amikor a tevékenységek elvégzésére a nagy mennyiségű memóriát, de csak rövid ideig időt, és a végrehajtása során változó időpontokban során. Hatékonyan kezelni az ilyen adatforgalmi csúcsokhoz több memória kevesebb, de nagyobb méretű, számítási csomópontok is alkalmazhat. Ezek a csomópontok kellene minden egyes csomóponton párhuzamosan futó feladatokhoz, de mindegyik tevékenység a csomópontok bőséges memória igénybe különböző időpontokban.
* **Csomópont szám korlátok csökkentése** szükséges a készletben lévő csomópontok közötti kommunikáció esetén. Jelenleg a készletek, csomópontok közötti kommunikációra konfigurálva korlátozva, 50 számítási csomópontok. Ha egy készletben minden egyes csomópont hajtható végre a feladatok párhuzamos, nagyobb számos feladatot egyszerre is végrehajthatók.
* **Egy helyi számítási fürt replikálása**, például ha Ön először helyezze át a számítási környezet az Azure-bA. Ha a jelenlegi helyszíni megoldás egyes számítási csomópontjain több feladatot hajt végre, növelheti a jobban tükrözi az adott konfigurációját csomópontban feladatok maximális számát.

## <a name="example-scenario"></a>Példaforgatókönyv
Például a párhuzamos tevékenység-végrehajtás előnyeit mutatja be, legyen mondjuk, hogy a tevékenységhez tartozó alkalmazást rendelkezik-e a CPU és memória úgy, hogy [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md) csomópontok elegendőek. Azonban ahhoz, hogy a feladat befejezéséhez szükséges idő, 1000, ezek a csomópontok van szükség.

Standard helyett\_D1 csomópontok 1 processzormaggal rendelkező, használhat [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) csomópontok 16 maggal rendelkezik, és a párhuzamos tevékenység-végrehajtás engedélyezése. Ezért *16-szor kevesebb csomópontot* használható – 1000 csomópont helyett csak 63 lenne szükséges. Emellett ha nagy méretű fájlok vagy hivatkozási adatokkal minden egyes csomópont esetében szükséges, és a feladat időtartama, valamint a hatékonyság vannak újra jobb, mivel csak 63 csomópontok az adatokat másolja.

## <a name="enable-parallel-task-execution"></a>Párhuzamos tevékenység-végrehajtás engedélyezése
Párhuzamos tevékenység-végrehajtás a számítási csomópontok a készlet szintjén konfigurálnia. A .NET-kódtár, állítsa be a [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] tulajdonság egy készlet létrehozásakor. Ha a Batch REST API-t használ, állítsa be a [maxTasksPerNode] [ rest_addpool] elem készlet létrehozása során, a kérelem törzsében.

Az Azure Batch lehetővé teszi, hogy akár négy alkalommal állítsa be a csomópontonkénti maximális feladatok (4 x) a node-magok számát. Például, ha a készlet csomópontjai állítottak méretezés "Nagy" (négy magot), majd `maxTasksPerNode` 16 lehet beállítani. További információ a csomópontok méretei mindegyike magok száma: [méretű felhőszolgáltatások](../cloud-services/cloud-services-sizes-specs.md). Szolgáltatási korlátok további információkért lásd: [kvóták és korlátozások az Azure Batch szolgáltatás](batch-quota-limit.md).

> [!TIP]
> Ügyeljen arra, hogy figyelembe kell venni a `maxTasksPerNode` értéke, ha hozhat létre egy [automatikus skálázási képletet] [ enable_autoscaling] a készlethez. Például, amely kiértékeli a képletet `$RunningTasks` jelentősen befolyásolhatja, csomópontonkénti tevékenységek növekedése. Lásd: [automatikusan méretezni a számítási csomópontok az Azure Batch-készletben](batch-automatic-scaling.md) további információt.
>
>

## <a name="distribution-of-tasks"></a>Feladatok megoszlása
A készlet számítási csomópontjain egyidejűleg feladatokat hajthat végre, ha fontos adja meg, hogyan legyen elosztva a készletben lévő csomópontok a feladatokat.

Használatával a [CloudPool.TaskSchedulingPolicy] [ task_schedule] tulajdonságot használja, megadhatja, hogy feladatokat egyenletesen lehet hozzárendelni a készletben ("terjesztés") lévő összes csomópont. Vagy megadhatja, hogy a lehető legtöbb feladatokat rendel hozzá minden egyes csomópont előtt feladatok vannak rendelve egy másik csomópont a készletben ("csomagolási").

Például, hogy ez a funkció csak értékes, fontolja meg a készlet [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) csomópontok (a fenti példában), amelynek része a [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] 16 értékét. Ha a [CloudPool.TaskSchedulingPolicy] [ task_schedule] van konfigurálva egy [ComputeNodeFillType] [ fill_type] , *csomag*, azt ehhez maximalizálása érdekében minden 16 maggal, az egyes csomópontok használatáért, és lehetővé teszi egy [automatikus skálázási készlet](batch-automatic-scaling.md) Ha fel nem használt csomópontok (csomópont minden olyan feladat rendelhető hozzá nélkül) a készletből. Ez csökkenti az erőforrás-használatot, és pénzt takarít.

## <a name="batch-net-example"></a>A Batch .NET típusú példát
Ez [Batch .NET] [ api_net] API kódrészlet azt mutatja be, négy tevékenységet csomópontonként legfeljebb négy csomópontot tartalmazó készletet létrehozására irányuló kérelem. Azt adja meg a Feladatütemező szabályzatot, amely minden egyes csomópont betelik előtt rendelne tevékenységeket a készletben egy másik csomópont feladatait. Készletek hozzáadása a Batch .NET API használatával a további információkért lásd: [BatchClient.PoolOperations.CreatePool][poolcreate_net].

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
Ez [Batch REST] [ api_rest] API kódrészlet azt mutatja be, csomópontonként négy tevékenységet, mely legfeljebb két nagy méretű csomópontot tartalmazó készletet létrehozására irányuló kérelem. Készletek hozzáadásával a REST API-val kapcsolatos további információkért lásd: [készlet hozzáadása fiókhoz][rest_addpool].

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
> Beállíthatja a `maxTasksPerNode` elem és [MaxTasksPerComputeNode] [ maxtasks_net] tulajdonság csak a készlet létrehozásakor. Azok a készlet már létrehozását követően nem módosítható.
>
>

## <a name="code-sample"></a>Kódminta
A [ParallelNodeTasks] [ parallel_tasks_sample] projektet a Githubon használatát illusztrálja a [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] tulajdonság.

A C# Konzolalkalmazás használja a [Batch .NET] [ api_net] erőforrástár-készlet létrehozása egy vagy több számítási csomópontokkal. A változó terhelés szimulálására csomópontokon konfigurálható számos feladat végrehajtása. Az alkalmazás kimenetének adja meg, melyik csomópontokon végrehajtott minden egyes tevékenységhez. Az alkalmazás emellett a feladat paramétereit és időtartama. A két különböző fut, a mintaalkalmazás kimenete összefoglaló része alatt jelenik meg.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Az első végrehajtás annak a mintaalkalmazásnak mutatja, hogy egy egyetlen csomóponttal, a készlet és a egy feladat csomópontonként, az alapértelmezett beállítás a feladat időtartama van több mint 30 perc.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

A második Futtatás a minta azt mutatja be, jelentősen csökkentheti a feladat időtartama. Ennek az oka csomópontonként, ami lehetővé teszi a párhuzamos tevékenység-végrehajtás szinte negyedév az idő a csomóponthasználat négy tevékenységet a készletben lett konfigurálva.

> [!NOTE]
> A feladat időtartamok a fenti összesítések nem tartalmazzák a készlet létrehozásakor. A fenti feladatok mindegyike el lett küldve a korábban létrehozott készletek, számítási csomópontjainak voltak a *tétlen* időben benyújtása állapot.
>
>

## <a name="next-steps"></a>További lépések
### <a name="batch-explorer-heat-map"></a>A Batch Explorer Hőtérkép
[A Batch Explorer] [ batch_labs] egy ingyenes, számos funkcióval ellátott, különálló ügyféleszköz létrehozásához, hibakereséséhez és monitorozásához az Azure Batch-alkalmazások segítségével. A Batch Explorer tartalmaz egy *Hőtérkép* funkció képi megjelenítés, a feladat a végrehajtás biztosít. Amikor végrehajtása a [ParallelTasks] [ parallel_tasks_sample] mintaalkalmazást, a Hőtérkép funkció segítségével könnyen megjelenítheti az egyes csomópontokon párhuzamos tevékenységek végrehajtására.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

