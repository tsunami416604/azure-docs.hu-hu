---
title: Nagy számú feladat beküldése - Azure Batch | Microsoft dokumentumok
description: Nagyon nagy számú feladat hatékony beküldése egyetlen Azure Batch-feladatban
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: c3857e512da5fe4fceefa5f735ddc65f73e11623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026047"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Nagy számú feladat küldése kötegelt feldolgozásba

Nagyméretű Azure Batch-számítási feladatok futtatásakor érdemes lehet több tízezer, több százezer vagy még több feladatot küldeni egyetlen feladatba. 

Ez a cikk útmutatást és néhány kódpéldát ad a nagy számú feladatok, amelyek jelentősen megnövekedett átviteli egyetlen kötegelt feladat. A feladatok elküldése után a feladathoz megadott készleten a kötegvárólistába kerülnek feldolgozásra.

## <a name="use-task-collections"></a>Feladatgyűjtemények használata

A Batch API-k olyan módszereket biztosítanak, amelyeket a feladatok *gyűjteményként*való hatékony hozzáadására, egyenként biztosít. Nagy számú feladat hozzáadásakor a megfelelő módszereket vagy túlterheléseket kell használnia a feladatok gyűjteményként való hozzáadásához. A feladatgyűjteményt általában úgy hozhatja létre, hogy a feladatokat a feladat bemeneti fájljainak vagy paramétereinek egy készletén keresztül iterate-ként definiálja.

Az egy hívásban hozzáadható feladatgyűjtemény maximális mérete a használt Batch API-tól függ:

* A következő kötegelt API-k a gyűjteményt **100 feladatra**korlátozzák. A korlát a tevékenységek méretétől függően kisebb lehet , például ha a tevékenységek nagy számú erőforrásfájlt vagy környezeti változót tartalmaznak.

    * [REST API](/rest/api/batchservice/task/addcollection)
    * [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Node.js API](/javascript/api/@azure/batch/task?view=azure-node-latest)

  Ezen API-k használatakor meg kell adnia a logikát a feladatok számának felosztásához a gyűjteménykorlát eléréséhez, valamint a hibák és az újrapróbálkozások kezeléséhez, ha a feladatok hozzáadása sikertelen lesz. Ha egy feladatgyűjtemény túl nagy ahhoz, hogy hozzáadja, a kérelem hibát generál, és újra meg kell próbálni kevesebb feladattal.

* A következő API-k sokkal nagyobb feladatgyűjteményeket támogatnak – csak a beküldő ügyfél RAM-rendelkezésre állása korlátozza. Ezek az API-k transzparens módon kezelik a feladatgyűjtemény "adattömbökre" való felosztását az alacsonyabb szintű API-k hoz, és újrapróbálkozások, ha a feladatok hozzáadása sikertelen.

    * [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Java API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [Azure Batch CLI bővítmény](batch-cli-templates.md) Batch CLI sablonokkal
    * [Python SDK-bővítmény](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>A feladatelküldés átviteli idejének növelése

Eltarthat egy ideig, amíg egy feladat nagy gyűjteményét adja hozzá – például akár 1 percet is, amíg 20 000 feladatot a .NET API-n keresztül ad hozzá. A Batch API-tól és a munkaterheléstől függően javíthatja a feladatátviteli-átatot az alábbiak közül egy vagy több módosításával:

* **Tevékenységméret** – Nagy feladatok hozzáadása hosszabb időt vesz igénybe, mint a kisebbek hozzáadása. A gyűjtemény egyes feladatainak méretének csökkentése érdekében egyszerűsítheti a feladat parancssorát, csökkentheti a környezeti változók számát, vagy hatékonyabban kezelheti a feladatvégrehajtás követelményeit. Például ahelyett, hogy nagy számú erőforrásfájlt használna, telepítse a feladatfüggőségeket egy [indítási feladat](batch-api-basics.md#start-task) használatával a készleten, vagy használjon [alkalmazáscsomagot](batch-application-packages.md) vagy [Docker-tárolót.](batch-docker-container-workloads.md)

* **Párhuzamos műveletek száma** – A Batch API-tól függően növelje az átviteli értéket a Batch-ügyfél egyidejű műveleteimaximális számának növelésével. Konfigurálja ezt a beállítást a [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) tulajdonsággal `threads` a .NET API-ban, vagy a metódusok paraméterével, például [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) a Batch Python SDK-bővítményben. (Ez a tulajdonság nem érhető el a natív Batch Python SDK.) Alapértelmezés szerint ez a tulajdonság 1-re van állítva, de magasabbra van állítva a műveletek átviteli teljesítményének javítása érdekében. A megnövekedett átviteli teljesítményt a hálózati sávszélesség és a processzor teljesítményének fogyasztásával cserélheti ki. A feladat átviteli értéke akár 100-szorosára `MaxDegreeOfParallelism` is `threads`nő. A gyakorlatban az egyidejű műveletek számát 100 alatt kell beállítani. 
 
  Az Azure Batch CLI bővítmény batch sablonokkal növeli az egyidejű műveletek számát automatikusan a rendelkezésre álló magok száma alapján, de ez a tulajdonság nem konfigurálható a CLI-ben. 

* **HTTP-kapcsolatkorlátok** – Az egyidejű HTTP-kapcsolatok száma nagy számú feladat hozzáadásakor szabályozhatja a Batch-ügyfél teljesítményét. A HTTP-kapcsolatok száma bizonyos API-k esetén korlátozott. Ha például a .NET API-val fejleszt, a ServicePointManager.DefaultConnectionLimit tulajdonság alapértelmezés szerint 2.2.When developing with the .NET API, for example, the [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) property is set to 2 by default. Azt javasoljuk, hogy növelje az értéket a párhuzamos műveletek számának közeli vagy annál nagyobb számára.

## <a name="example-batch-net"></a>Példa: Köteg .NET

A következő C# kódrészletek a Batch .NET API-val nagyszámú feladat hozzáadásakor konfigurálandó beállításokat jelenítik meg.

A feladatátviteli sebesség növeléséhez növelje a [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet) [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) tulajdonságának értékét. Példa:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Az [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) vagy az [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) metódus megfelelő túlterhelésével vegyen fel egy feladatgyűjteményt a feladatba. Példa:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Példa: Batch CLI kiterjesztés

Az Azure Batch CLI-bővítmények [batch CLI-sablonokkal](batch-cli-templates.md)való használatával hozzon létre egy feladatsablont JSON-fájllal, amely [egy feladat-előállítót](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md)tartalmaz. A feladat-előállító egyetlen feladatdefinícióból konfigurálja egy feladat kapcsolódó feladatainak gyűjteményét.  

Az alábbiakban egy mintafeladat-sablon egy egydimenziós parametrikus sweep feladat nagy számú feladat - ebben az esetben 250.000. A feladat parancssora `echo` egyszerű parancs.

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
Ha feladatot szeretne futtatni a sablonnal, olvassa [el az Azure Batch CLI-sablonok és a fájlátvitel használata](batch-cli-templates.md)című témakört.

## <a name="example-batch-python-sdk-extension"></a>Példa: Batch Python SDK-bővítmény

Az Azure Batch Python SDK-bővítmény használatához először telepítse a Python SDK-t és a bővítményt:

```
pip install azure-batch
pip install azure-batch-extensions
```

`BatchExtensionsClient` Az SDK-bővítményt használó beállítás:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Feladathoz adandó feladatok gyűjteményének létrehozása. Példa:


```python
tasks = list()
# Populate the list with your tasks
...
```

Adja hozzá a feladatgyűjteményt a [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)használatával. Állítsa `threads` be a paramétert az egyidejű műveletek számának növelésére:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

A Batch Python SDK-bővítmény is támogatja a feladat paramétereinek hozzáadását a feladathoz egy Feladat-előállító JSON-specifikáció használatával. Konfigurálja például a feladat paramétereit egy parametrikus söpréshez hasonlóhoz, mint az előző Batch CLI sablonban például:

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
                        "retentionTime": "PT1H"
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

Adja hozzá a feladat paramétereit a feladathoz. Állítsa `threads` be a paramétert az egyidejű műveletek számának növelésére:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>További lépések

* További információ az Azure Batch CLI bővítmény [Batch CLI-sablonokkal](batch-cli-templates.md)való használatáról.
* További információ a [Batch Python SDK-bővítményről.](https://pypi.org/project/azure-batch-extensions/)
