---
title: Feladatok futtatása az Azure Batch számítási erőforrások hatékony - használandó párhuzamos |} Microsoft Docs
description: Növelje a hatékonyság és az alacsonyabb költségek Azure Batch-készlet minden egyes csomópontján kevesebb számítási csomópontok és futó egyidejű feladatok használatával
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5106bbbb073908af7e7e8f045fa6fb60e8a306f4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316912"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Egyidejűleg köteg használati maximalizálhatja a feladatokat futtató számítási csomópontjain 

Több feladat fut egyidejűleg az Azure Batch-készlet egyes számítási csomópontjain, kihasználhatja a csomópontok a készlet kevesebb erőforrás-használata. Bizonyos munkaterhelések esetén ez eredményezhet rövidebb feladat idővel és az alacsonyabb költségek.

Bizonyos esetekben kihasználhassa egyetlen feladat dedikálni minden csomópont-erőforrást, miközben Számos esetben ezek az erőforrások megosztása több feladat engedélyezése előnyei:

* **Minimalizálja a adatátvitel** amikor feladatok képesek megoszthatják az adatokat. Ebben a forgatókönyvben jelentősen csökkentheti adatok adatátviteli díjakkal megosztott adat másolása az csomópontok kevesebb és feladatok végrehajtása minden egyes csomóponton párhuzamosan. Ez különösen igaz, ha minden csomópont lehet másolni az adatokat át kell a földrajzi régiók között.
* **Memóriahasználat maximalizálva** olvasható feladatokhoz szükséges, amikor nagy mennyiségű memóriát, de csak időszakokban rövid idő, valamint a változó időpontokban végrehajtása során. Kevesebb, de nagyobb, a számítási csomópontok hatékonyan tudja kezelni az ilyen igényeiben jelentkező több memória is alkalmazhat. Ezek a csomópontok kellene több feladat minden egyes csomóponton párhuzamosan futó, de minden feladat időt vesz igénybe a csomópontok címtér memória különböző időpontokban.
* **Csomópont számú korlátok kiküszöböléséhez** csomópontok közötti kommunikáció esetén kötelező a készlet belül. Csomópontok közötti kommunikációra konfigurálva készletek jelenleg legfeljebb 50 számítási csomópontok. Ha ilyen készlet minden egyes csomópontja képes feladatok párhuzamos végrehajtására, nagyobb számos feladatot egyszerre hajtható végre.
* **Egy a helyszíni számítási fürt replikálása**, például amikor először helyezi át a számítási környezet az Azure-bA. Ha a jelenlegi helyszíni megoldás egyes számítási csomópontjain több feladat végrehajtása során, növelheti az, hogy a konfigurálás történő jobban csomópont feladatok maximális száma.

## <a name="example-scenario"></a>Példaforgatókönyv
Például a párhuzamos feladat a végrehajtás előnyeit mutatja be, tegyük fel, hogy a feladat alkalmazás rendelkezik-e a CPU és memória úgy, hogy [szabványos\_D1](../cloud-services/cloud-services-sizes-specs.md) elegendőek csomópontjai. De a feladat befejezéséhez szükséges időt, a csomópontok 1000 van szükség.

A szabványos helyett\_D1 csomópontok 1 Processzormagok, használhat [szabványos\_D14](../cloud-services/cloud-services-sizes-specs.md) csomópontokat, amelyek 16 mag, és engedélyezze a párhuzamos feladat a végrehajtás. Ezért *16-szer kevesebb csomópontok* használható--1000 csomópont helyett csak 63 lenne szükséges. Emellett minden egyes csomópont nagy alkalmazásfájlok vagy referenciaadatok szükségesek, ha feladat időtartama és hatékonyságát újra növelése, mert a rendszer átmásolja az adatokat csak 63 csomópontokra.

## <a name="enable-parallel-task-execution"></a>A párhuzamos feladat a végrehajtás engedélyezése
A párhuzamos végrehajtásához a számítási csomópontok a készlet szinten konfigurálnia. A Batch .NET könyvtárhoz, állítsa be a [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] tulajdonságot, ha a készletet hoz létre. Ha a Batch REST API-t használ, állítsa be a [maxTasksPerNode] [ rest_addpool] elem a kérés törzsében készlet létrehozása során.

Az Azure Batch teszi maximális tevékenységek maximális száma legfeljebb négy alkalommal (4 x) csomópont magok száma. Például, ha az alkalmazáskészlet úgy van konfigurálva, a csomópont a méretezés "Nagy" (négy magok), majd `maxTasksPerNode` lesz beállítva: 16 lehet beállítani. További részletek az egyes, a csomópont méretű magok száma: [Felhőszolgáltatások mérete](../cloud-services/cloud-services-sizes-specs.md). További információ a szolgáltatásra vonatkozó korlátozások: [kvótái és korlátai az Azure Batch szolgáltatás](batch-quota-limit.md).

> [!TIP]
> Ügyeljen arra, hogy figyelembe kell venni a `maxTasksPerNode` érték-hoz egy [automatikus skálázás képlet] [ enable_autoscaling] a készlethez. Például megadó képlet `$RunningTasks` jelentős mértékben befolyásolhatja, megnövelheti a tevékenységek maximális száma. Lásd: [automatikus méretezési számítási csomópontok az Azure Batch-készlet](batch-automatic-scaling.md) további információt.
>
>

## <a name="distribution-of-tasks"></a>Tevékenységek eloszlása
Készlet számítási csomópontjain egyidejűleg végrehajtható feladatok, ha fontos határozza meg, hogyan legyen elosztva a csomópontok a készlet a feladatokat.

Használatával a [CloudPool.TaskSchedulingPolicy] [ task_schedule] tulajdonság, megadhatja, hogy feladatok egyenletes legyen hozzárendelve a készletben ("terjednek") található összes csomópont. Vagy megadhatja, hogy a lehető legtöbb feladatok hozzá kell rendelni minden csomópont előtt tevékenységeket rendel a készlet ("csomagolási") egy másik csomópontjára.

Például hogyan értékes-e ezt a szolgáltatást, vegye figyelembe a körét [szabványos\_D14](../cloud-services/cloud-services-sizes-specs.md) csomópontok (a fenti példában), amelynek része a [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] 16-os értéket. Ha a [CloudPool.TaskSchedulingPolicy] [ task_schedule] van konfigurálva egy [ComputeNodeFillType] [ fill_type] a *Pack*, azt ehhez maximalizálhatja használatát az egyes csomópontok összes 16 maggal és engedélyezése egy [automatikus skálázás készlet](batch-automatic-scaling.md) nem használt csomópontok a készlet (csomópontok nélkül a kijelölt feladatok) törlésére. Ez minimalizálja az erőforrás-használatát, és menti a pénz.

## <a name="batch-net-example"></a>Batch .NET – példa
Ez [Batch .NET] [ api_net] API kódrészletet látható négy tevékenységek maximális száma legfeljebb négy csomópont nagy tartalmazó készlet létrehozása a kérést. Meghatározza a Feladatütemező házirendet, amely minden csomóponton tölti feladatok előtti feladatok hozzárendelése a készlet egy másik csomópontjára. A készletek hozzáadása a Batch .NET API használatával további információkért lásd: [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Batch REST – példa
Ez [Batch REST] [ api_rest] API kódrészletben láthatja, négy tevékenységek maximális száma legfeljebb két nagy csomópontot tartalmazó készlet létrehozása a kérést. A készletek hozzáadása a REST API használatával további információkért lásd: [a készlet hozzáadása partner][rest_addpool].

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
> Beállíthatja a `maxTasksPerNode` elem és [MaxTasksPerComputeNode] [ maxtasks_net] tulajdonság csak a készlet létrehozás időpontjában. Ezeket nem lehet módosítani a készlet létrehozása után.
>
>

## <a name="code-sample"></a>Kódminta
A [ParallelNodeTasks] [ parallel_tasks_sample] a Githubon projekt használatát mutatja a [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] tulajdonság.

A C# Konzolalkalmazás használja a [Batch .NET] [ api_net] legalább egy számítási csomópontok a készlet létrehozása a könyvtárban. Konfigurálható számos feladatot azokat a csomópontokat, változó terhelés szimulálása a hajtja végre. Az alkalmazás kimenete határozza meg, mely csomópontok végrehajtott minden tevékenység. Az alkalmazás is a feladat paramétereit és időtartama összegzését tartalmazza. A két különböző frissítési kísérletei során a mintaalkalmazás eredményének összefoglaló részét alatt jelenik meg.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A mintaalkalmazás első végrehajtása azt mutatja, hogy a készlet és az alapértelmezett beállítás, csomópontonként egy feladat egyetlen csomóponton a feladat időtartama van több mint 30 perc.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

A második, a minta azt mutatja be a Futtatás jelentősen csökkentheti a feladat időtartama. Ennek oka az, a készlet négy tevékenységek maximális száma, ami lehetővé teszi a párhuzamos feladat végrehajtása a feladat befejezéséhez majdnem idő negyedévében lett konfigurálva.

> [!NOTE]
> A feladat időtartamok a fenti összesítések nem tartalmazzák a készlet létrehozásának ideje. A fenti feladatok minden számítási csomópontjainak volt a korábban létrehozott készletek lett küldve a *üresjáratban* állapot küldése során.
>
>

## <a name="next-steps"></a>További lépések
### <a name="batchlabs-heat-map"></a>BatchLabs Hőtérkép
A [BatchLabs][batch_labs] egy ingyenes, számos funkcióval ellátott, különálló ügyféleszköz Azure Batch-alkalmazások létrehozásához, hibakereséséhez és monitorozásához. BatchLabs tartalmaz egy *Hőtérkép* képi megjelenítés feladat végrehajtása a szolgáltatás. Ha végrehajtása a [ParallelTasks] [ parallel_tasks_sample] mintaalkalmazást, a Hőtérkép szolgáltatás használatával egyszerűen jelenítheti meg minden egyes csomóponton párhuzamos tevékenységek végrehajtása.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchLabs/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

