---
title: Feladatok párhuzamos futtatása a számítási erőforrások optimalizálása érdekében - Azure Batch
description: Növelje a hatékonyságot és csökkentse a költségeket kevesebb számítási csomópont használatával és egyidejű feladatok futtatásával az Azure Batch-készlet minden csomópontján
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5465771cb97ef9d8d5c451a6bafc61c4621d3c4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023633"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Feladatok egyidejű futtatása a Batch számítási csomópontok használatának maximalizálása érdekében 

Ha egyszerre több feladatot futtat az Azure Batch-készlet minden számítási csomópontján, maximalizálhatja az erőforrás-használatot a készlet kisebb számú csomópontján. Egyes számítási feladatok esetén ez rövidebb munkaidőt és alacsonyabb költségeket eredményezhet.

Míg egyes esetekben előnyös, ha egy csomópont összes erőforrását egyetlen tevékenységre ajánlja, számos esetben több tevékenység számára is előnyös, ha több tevékenység osztozik ezeken az erőforrásokon:

* **Az adatátvitel minimalizálása,** ha a feladatok képesek megosztani az adatokat. Ebben a forgatókönyvben jelentősen csökkentheti az adatátviteli díjakat, ha a megosztott adatokat kisebb számú csomópontra másolja, és minden csomóponton párhuzamosan hajt végre feladatokat. Ez különösen akkor érvényes, ha az egyes csomópontokra másolandó adatokat földrajzi régiók között kell átvinni.
* **A memóriahasználat maximalizálása,** ha a feladatok nagy mennyiségű memóriát igényelnek, de csak rövid ideig és változó időpontokban a végrehajtás során. Kevesebb, de nagyobb számítási csomópontot alkalmazhat több memóriával az ilyen csúcsok hatékony kezeléséhez. Ezek a csomópontok volna több feladat fut párhuzamosan minden csomóponton, de minden feladat kihasználná a csomópontok bőséges memória különböző időpontokban.
* **A csomópontszám-korlátok mérséklése,** ha egy készleten belül csomópontközi kommunikációra van szükség. Jelenleg a csomópontok közötti kommunikációra konfigurált készletek legfeljebb 50 számítási csomópontra korlátozódnak. Ha egy ilyen készlet minden csomópontja képes párhuzamosan feladatokat végrehajtani, több feladat hajtható végre egyszerre.
* **A helyszíni számítási fürt replikálása,** például amikor először helyez át egy számítási környezetet az Azure-ba. Ha az aktuális helyszíni megoldás számítási csomópontonként több feladatot hajt végre, növelheti a csomópontfeladatok maximális számát, hogy jobban tükrözze ezt a konfigurációt.

## <a name="example-scenario"></a>Példaforgatókönyv
Például a párhuzamos feladat-végrehajtás előnyeinek szemléltetésére tegyük fel, hogy a feladatalkalmazás rendelkezik a PROCESSZOR és a memória követelmények, hogy [a\_standard D1](../cloud-services/cloud-services-sizes-specs.md) csomópontok elegendőek. De annak érdekében, hogy befejezze a munkát a szükséges időben, 1000 ilyen csomópontok van szükség.

Az 1\_PROCESSZORmaggal rendelkező szabványos D1-csomópontok használata helyett használhat 16 maggal rendelkező [Szabványos\_D14-csomópontokat,](../cloud-services/cloud-services-sizes-specs.md) és engedélyezheti a párhuzamos feladatvégrehajtást. Ezért *16-szor kevesebb csomópont* ot lehetne használni – 1000 csomópont helyett csak 63-ra lenne szükség. Továbbá, ha nagy alkalmazásfájlokvagy referenciaadatok szükségesek az egyes csomópontokhoz, a feladat időtartama és hatékonysága ismét javul, mivel az adatok másolása csak 63 csomópontra történik.

## <a name="enable-parallel-task-execution"></a>Párhuzamos feladat végrehajtásának engedélyezése
Számítási csomópontokat konfigurálhat a készlet szintjén a párhuzamos feladatvégrehajtáshoz. A Batch .NET könyvtárral állítsa be a [CloudPool.MaxTasksPerComputeNode][maxtasks_net] tulajdonságot készlet létrehozásakor. Ha a Batch REST API-t használja, állítsa be a [maxTasksPerNode][rest_addpool] elemet a kérelem törzsében a készlet létrehozása során.

Az Azure Batch lehetővé teszi, hogy a feladatok csomópontonkénti beállítása legfeljebb (4x) az alapvető csomópontok száma. Ha például a készlet "Large" (négy mag) méretű csomópontokkal `maxTasksPerNode` van konfigurálva, akkor 16-ra állítható. Azonban, függetlenül attól, hogy hány mag a csomópont, nem lehet több, mint 256 feladatok csomópontonként. Az egyes csomópontméretek magjainak számáról a [Felhőszolgáltatások méretei](../cloud-services/cloud-services-sizes-specs.md)című témakörben talál részleteket. A szolgáltatási korlátokról további információt [az Azure Batch szolgáltatás kvótái és korlátai című](batch-quota-limit.md)témakörben talál.

> [!TIP]
> Ügyeljen arra, hogy `maxTasksPerNode` vegye figyelembe az értéket, amikor [automatikus skálázási képletet][enable_autoscaling] hoz létre a készlethez. Egy kiértékelt `$RunningTasks` képletet például jelentősen befolyásolhatja a csomópontonkénti tevékenységek száma. További információ: [A számítási csomópontok automatikus méretezése az Azure Batch-készletben](batch-automatic-scaling.md) további információkért.
>
>

## <a name="distribution-of-tasks"></a>A feladatok elosztása
Ha a készlet számítási csomópontjai egyidejűleg hajthatnak végre feladatokat, fontos megadni, hogyan szeretné elosztani a feladatokat a készlet csomópontjai között.

A [CloudPool.TaskSchedulingPolicy][task_schedule] tulajdonság használatával megadhatja, hogy a feladatokat egyenletesen kell hozzárendelni a készlet összes csomópontja között ("terjedés"). Azt is megadhatja, hogy a lehető legtöbb feladatot kell hozzárendelni az egyes csomópontokhoz, mielőtt a feladatokat a készlet egy másik csomópontjához rendelnék ("csomagolás").

Példaként, hogy ez a szolgáltatás értékes, fontolja meg a [standard\_D14-csomópontok](../cloud-services/cloud-services-sizes-specs.md) készletét (a fenti példában), amely egy [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 16 értékkel van konfigurálva. Ha a [CloudPool.TaskSchedulingPolicy][task_schedule] [egy ComputeNodeFillType][fill_type] of *Pack*csomaggal van konfigurálva, akkor az maximalizálná az egyes csomópontok mind a 16 magjának használatát, és lehetővé tenné, hogy egy [automatikus skálázási készlet](batch-automatic-scaling.md) metszeni a készlet fel nem használt csomópontjait (a hozzárendelt feladatok nélküli csomópontokat). Ez minimálisra csökkenti az erőforrás-felhasználást, és pénzt takarít meg.

## <a name="batch-net-example"></a>Példa a köteg .NET
Ez [a Batch .NET][api_net] API-kódkódrészlet egy olyan készlet létrehozására irányuló kérelmet jelenít meg, amely csomópontonként legfeljebb négy feladatot tartalmazó négy csomópontot tartalmaz. Olyan feladatütemezési házirendet határoz meg, amely minden csomópontot tevékenységekkel tölt ki, mielőtt feladatokat rendelne a készlet egy másik csomópontjához. A gyűjtők Batch .NET API használatával történő hozzáadásáról a [BatchClient.PoolOperations.CreatePool][poolcreate_net]című témakörben talál további információt.

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

## <a name="batch-rest-example"></a>Példa kötegrestelési restara
Ez [a Batch REST][api_rest] API-kódrészlet egy olyan készlet létrehozására irányuló kérelmet jelenít meg, amely csomópontonként legfeljebb négy feladatot tartalmazó nagy csomópontot tartalmaz. A készletek REST API használatával történő hozzáadásáról további információt a [Készlet hozzáadása egy fiókhoz][rest_addpool]című témakörben talál.

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
> Az elemet `maxTasksPerNode` és a [MaxTasksPerComputeNode][maxtasks_net] tulajdonságot csak készletlétrehozási időpontban állíthatja be. Nem módosíthatók, ha már létrehozott egy készletet.
>
>

## <a name="code-sample"></a>Kódminta
A [GitHub ParallelNodeTasks][parallel_tasks_sample] projektje a [CloudPool.MaxTasksPerComputeNode][maxtasks_net] tulajdonság használatát mutatja be.

Ez a C# konzolalkalmazás a [Batch .NET][api_net] könyvtár at használja egy vagy több számítási csomópontos készlet létrehozásához. Konfigurálható számú feladatot hajt végre ezeken a csomópontokon a változó terhelés szimulálása érdekében. Az alkalmazás kimenete határozza meg, hogy mely csomópontok hajtják végre az egyes feladatokat. Az alkalmazás a feladat paramétereinek és időtartamának összegzését is tartalmazza. A mintaalkalmazás két különböző futa kimenetének összefoglaló része az alábbiakban jelenik meg.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A mintaalkalmazás első végrehajtása azt mutatja, hogy egyetlen csomóponttal a készletben, és az alapértelmezett beállítás egy feladat csomópontonként, a feladat időtartama több mint 30 perc.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

A minta második futtatása a feladat időtartamának jelentős csökkenését mutatja. Ennek az az oka, hogy a készlet csomópontonként négy feladattal volt konfigurálva, ami lehetővé teszi, hogy a párhuzamos feladat végrehajtás a feladat közel negyedében befejeződjen.

> [!NOTE]
> A fenti összegzésekben a projekt időtartamai nem tartalmazzák a készlet létrehozási idejét. A fenti feladatok mindegyike korábban létrehozott készletek, amelyek számítási csomópontjai a *beküldési* időpontban az alapjárati állapotban voltak.
>
>

## <a name="next-steps"></a>További lépések
### <a name="batch-explorer-heat-map"></a>Batch Explorer hőtérkép
[A Batch Explorer][batch_labs] egy ingyenes, gazdag funkcionalitású, önálló ügyféleszköz az Azure Batch-alkalmazások létrehozásához, hibakereséséhez és figyeléséhez. A Batch Explorer tartalmaz egy *Hőtérkép* funkciót, amely a feladatvégrehajtás megjelenítését biztosítja. A [ParallelTasks][parallel_tasks_sample] mintaalkalmazás végrehajtásakor a Heat Map funkcióval könnyedén megjelenítheti a párhuzamos feladatok végrehajtását az egyes csomópontokon.


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

