---
title: Küldje el a feladatok – Azure Batch nagy számú |} A Microsoft Docs
description: Hogyan lehet hatékonyan elküldeni egy egyetlen Azure Batch-feladat tevékenységeit nagyon nagy számú
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: 3e20aeb0e21eca5e4ac25206d638036f94a58202
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749695"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Nagy számú feladatot egy Batch-feladat elküldése

Ha nagyméretű Azure Batch számítási feladatot futtat, érdemes több ezer, több száz, több ezer vagy egy feladat még további feladatok elküldéséhez. 

Ez a cikk nyújt útmutatást és néhány hitelesítésikód-példák nagyszámú feladatokról, amelyek jelentősen nagyobb átviteli sebességet, hogy egyetlen Batch-feladat elküldéséhez. Feladatokat az elküldésüket követően, adja meg a Batch-várólista feldolgozása, adja meg a feladatot a készleten.

## <a name="use-task-collections"></a>A feladat collections használata

A Batch API-k módszert hatékonyan tevékenységeket ad hozzá egy feladatot, adjon meg egy *gyűjtemény*, egyenként mellett. Feladatok nagy számú való hozzáadásakor, használjon a megfelelő módszerek vagy túlterheléssel gyűjteményként feladatok hozzáadásával. Egy feladat gyűjtemény általában a feladatok definiálásával, mivel a feladat bemeneti fájlok vagy a paraméterek ciklustevékenység összeállítani.

A feladat-gyűjteményt, amely egyetlen hívással is hozzáadhat maximális mérete attól függ, hogy a Batch API-t használja:

* A következő kötegelt API-k korlátozza a gyűjteményt, amelyben **100 tevékenységet**. Lehet, hogy a feladatok – például méretétől függően kisebb, ha a feladatok sok erőforrás-fájlokat vagy a környezeti változókat a korlátot.

    * [REST API](/rest/api/batchservice/task/addcollection)
    * [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Node.js API](/javascript/api/azure-batch/task?view=azure-node-latest)

  Ezen API-k használatakor meg kell adnia a logikai osztani a felel meg a fizetési korlátot, és a hibák és az újrapróbálkozások kezelésére, abban az esetben feladatok hozzáadása nem sikerül feladatok számát. Ha egy feladat gyűjtemény túl nagy ahhoz, hogy adja hozzá, a kérelem hibát generál, és meg kell ismételni ismét az kevesebb a tevékenység.

* A következő API-kat támogatja sokkal nagyobb a feladat-gyűjtemények – csak a RAM-MAL rendelkezésre állás, a küldő ügyfél által korlátozott. Ezen API-k átlátható módon kezelni a feladat-gyűjtemény "adattömbökbe" az alacsonyabb szintű API-k és az újrapróbálkozások osztani, ha a feladatok hozzáadása nem sikerül.

    * [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Java API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Az Azure Batch CLI-bővítmény](batch-cli-templates.md) a Batch parancssori felületi sablonjainak
    * [Python SDK extension](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Feladat küldése az átviteli sebesség növelése

Nagyméretű tevékenységek gyűjteményei, például hozzá egy feladat - némi időt vehet igénybe, a másolatot 1 perc a 20 000 hozzáadandó feladatok a .NET API-n keresztül. Attól függően, a Batch API-t és a számítási feladatok is javítható az átviteli feladat módosítása egy vagy több, a következő:

* **Tevékenység-méret** – nagy méretű feladatok hozzáadása kisebbeket hozzáadása hosszabb ideig tart. Minden tevékenység egy gyűjtemény méretének csökkentése érdekében egyszerűsítése a tevékenység parancssora, csökkentse a környezeti változók számát, vagy a feladat a végrehajtás hatékonyabb érdekében. Például sok erőforrás-fájlok helyett tevékenységfüggőségek használatával telepíthet egy [kezdő tevékenység](batch-api-basics.md#start-task) készlet vagy használata egy [alkalmazáscsomag](batch-application-packages.md) vagy [Docker-tároló](batch-docker-container-workloads.md).

* **Párhuzamos műveletek számát** – attól függően, a Batch API-növekedés átviteli sebesség, egyidejű a Batch-ügyfél-műveletek maximális számának növelésével. Konfigurálja a beállítás használatával a [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) tulajdonság a .NET API-ban, vagy a `threads` módszerek, például paraméter [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)a Batch Python SDK-bővítményben. (Ez a tulajdonság nem érhető el a natív Batch Python SDK.) Alapértelmezés szerint ez a tulajdonság értéke 1, de magasabb átviteli sebesség a műveletek javítása érdekében állítsa be. A kereskedelmi nagyobb átviteli sebesség a felhasználó hálózati sávszélesség és néhány CPU-teljesítményre. Akár 100-szor feladat átviteli növeli a `MaxDegreeOfParallelism` vagy `threads`. A gyakorlatban kell beállítania a 100 alatti párhuzamos műveletek számát. 
 
  Az Azure Batch CLI-bővítmény Batch-sablonokkal növeli az elérhető magok száma alapján automatikusan párhuzamos műveletek számát, de ez a tulajdonság nem konfigurálható a CLI-ben. 

* **HTTP-kapcsolat korlátai** – HTTP egyidejű kapcsolatok száma képes szabályozni a Batch-ügyfél teljesítményét, amikor a bővíti a feladatok nagy számú. A HTTP-kapcsolatok száma az egyes API-kkal korlátozódik. Ha a fejlesztés a .NET API-t, például a a [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) tulajdonság értéke 2, alapértelmezés szerint. Azt javasoljuk, hogy növelje az értéket egy közeli vagy nagyobb, mint a párhuzamos műveletek számát.

## <a name="example-batch-net"></a>Példa: Batch .NET

Az alábbi C# kódrészletek bemutatják a beállítások konfigurálása a feladatokat a Batch .NET API használatával nagy számú hozzáadásakor.

A feladat átviteli sebesség növelése érdekében értékének növelése a [Maxanalyticsunits](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) tulajdonságát a [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Példa:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
A feladat gyűjtemény hozzáadása a feladathoz, a megfelelő túlterhelési használatával a [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) vagy [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) metódust. Példa:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Példa: A Batch CLI-bővítmény

Az Azure Batch CLI bővítményekkel [Batch parancssori felületi sablonjainak](batch-cli-templates.md), hozzon létre egy feladatot a sablon JSON-fájlt, amely tartalmaz egy [feladat gyári](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). A feladat gyári konfigurálja egy adott feladat meghatározása a kapcsolódó feladatok gyűjteménye.  

A feladatok – ebben az esetben 250 000 nagy számú egydimenziós paraméteres feladat mintasablon feladat a következő: A tevékenység parancssora egy egyszerű `echo` parancsot.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```
A feladat futtatásához a sablont, tekintse meg a [használata az Azure Batch parancssori felületi sablonjainak és fájlátviteli](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Példa: Batch Python SDK-bővítmény

Az Azure Batch Python SDK-bővítmény használatához először telepítse a Python SDK-t és a bővítmény:

```
pip install azure-batch
pip install azure-batch-extensions
```

Állítsa be a `BatchExtensionsClient` , amely az SDK-bővítményt használ:

```python

client = batch.BatchExtensionsClient(base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Hozzon létre egy feladat hozzáadása a feladatok tevékenységek gyűjteményei. Példa:


```python
tasks=list()
# Populate the list with your tasks
...

```

Adja hozzá a feladat gyűjtemény használatával [Task.add_colection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python). Állítsa be a `threads` paraméter segítségével növeli a párhuzamos műveletek számát:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

A Batch Python SDK-bővítmény hozzáadása feladat paraméterei feladathoz egy JSON-specifikáció használatával egy feladat Factory is támogatja. Például a paraméteres hasonló az előző feladat paramétereinek konfigurálása [Batch CLI sablon](#example-batch-cli-template) példa:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

A feladat paraméterek hozzáadása a feladathoz. Állítsa be a `threads` paraméter segítségével növeli a párhuzamos műveletek számát:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>További lépések

* Tudjon meg többet az Azure Batch CLI-bővítmény használata [Batch parancssori felületi sablonjainak](batch-cli-templates.md).
* Tudjon meg többet a [Batch Python SDK-bővítmény](https://pypi.org/project/azure-batch-extensions/).
