---
title: Nagy számú feladat elküldése
description: Nagyon nagy számú feladat hatékony beküldése egyetlen Azure Batch feladatban
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 0be30e1a413a224d566db535d369a0b285b1f668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117403"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Nagy számú feladat elküldése egy batch-feladatba

Nagyméretű Azure Batch számítási feladatok futtatásakor előfordulhat, hogy több tízezer, több százezer vagy akár több feladatot szeretne elküldeni egyetlen feladatnak. 

Ez a cikk útmutatást és példákat tartalmaz arra vonatkozóan, hogy nagy számú feladatot küldjön egy batch-feladathoz jelentős mértékben megnövekedett átviteli sebességgel. A feladatok elküldése után megadják a Batch-várólistát a feladathoz megadott készlet feldolgozásához.

## <a name="use-task-collections"></a>Feladatok gyűjteményének használata

A Batch API-k olyan metódusokat biztosítanak, amelyekkel a feladatok hatékonyan hozzáadhatók *gyűjteményként*egy adott időpontban. Nagy mennyiségű feladat hozzáadásakor a megfelelő módszereket vagy túlterheléseket kell használnia a feladatok gyűjteményként való hozzáadásához. Általában úgy hozhat létre egy feladatot, hogy meghatározza a feladatokat, és megismétli a feladathoz tartozó bemeneti fájlok vagy paraméterek körét.

Az egyetlen hívásban felvehető feladat-gyűjtemény maximális mérete a használt batch API-val függ:

* A következő batch API-k az 100-es **feladatokra**korlátozzák a gyűjteményt. A korlát lehet kisebb a feladatok méretétől függően – például ha a feladatok nagy számú erőforrás-fájllal vagy környezeti változóval rendelkeznek.

    * [REST API](/rest/api/batchservice/task/addcollection)
    * [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Node.js API](/javascript/api/@azure/batch/task?view=azure-node-latest)

  Ezen API-k használatakor meg kell adnia a feladatok számának a gyűjtési korlátnak megfelelő számát, valamint a hibák és az újrapróbálkozások kezelését, ha a feladatok hozzáadása sikertelen. Ha egy feladatsor túl nagy a hozzáadáshoz, a kérelem hibát generál, és kevesebb feladattal újra próbálkozni fog.

* A következő API-k sokkal nagyobb feladatokat támogatnak – csak a RAM rendelkezésre állása korlátozza a beküldő ügyfélen. Ezek az API-k transzparens módon kezelik a feladatok gyűjteményét az alsó szintű API-k számára, és újrapróbálkoznak, ha a feladatok hozzáadása sikertelen.

    * [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Java API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * [CLI-bővítmény Azure batch](batch-cli-templates.md) batch CLI-sablonokkal
    * [Python SDK-bővítmény](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>A feladat beküldési teljesítményének növelése

Több időbe telik, amíg a feladatok nagy részét felvesszük egy feladathoz, például akár 1 percet is igénybe vehet, hogy a .NET API-n keresztül 20 000-feladatokat vegyen fel. A Batch API-tól és a számítási feladattól függően javíthatja a feladatok átviteli sebességét, ha módosítja a következők valamelyikét:

* **Feladat mérete** – a nagyméretű feladatok hozzáadása hosszabb időt vesz igénybe, mint a kisebbek hozzáadása. A gyűjtemények egyes feladatainak méretének csökkentéséhez egyszerűsítheti a feladat parancssorát, csökkentheti a környezeti változók számát, vagy hatékonyabban kezelheti a feladatok végrehajtásának követelményeit. Ha például nagy mennyiségű erőforrást kíván használni, telepítse a feladat függőségeit a készlet [indítási feladatával](batch-api-basics.md#start-task) , vagy használjon [alkalmazáscsomag](batch-application-packages.md) vagy [Docker-tárolót](batch-docker-container-workloads.md).

* **Párhuzamos műveletek száma** – a Batch API-tól függően növelje az átviteli sebességet úgy, hogy növeli az egyidejű műveletek maximális számát a Batch-ügyfél számára. Konfigurálja ezt a beállítást a .NET API [BatchClientParallelOptions. maxanalyticsunits](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) tulajdonságával, vagy az `threads` olyan metódusok paraméterével, mint például a [TaskOperations. add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) a Batch Python SDK-bővítményben. (Ez a tulajdonság nem érhető el a natív batch Python SDK-ban.) Alapértelmezés szerint ez a tulajdonság 1 értékre van állítva, de magasabbra van állítva a műveletek átviteli sebességének növelése érdekében. A hálózati sávszélesség és a CPU-teljesítmény növelésével kihasználhatja a megnövekedett adatátvitelt. A feladat átviteli sebessége a vagy `MaxDegreeOfParallelism` `threads`a 100-szor értékével nő. A gyakorlatban a 100 alatti párhuzamos műveletek számát kell beállítania. 
 
  A Batch-sablonokkal Azure Batch CLI bővítmény az elérhető magok száma alapján automatikusan növeli az egyidejű műveletek számát, de ez a tulajdonság nem konfigurálható a CLI-ben. 

* **Http-kapcsolat korlátai** – az EGYidejű http-kapcsolatok száma szabályozhatja a Batch-ügyfél teljesítményét, amikor nagy számú feladatot ad hozzá. A HTTP-kapcsolatok száma bizonyos API-kkal korlátozott. A .NET API-val való fejlesztéskor például a [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) tulajdonság értéke alapértelmezés szerint 2. Azt javasoljuk, hogy az értéket a párhuzamos műveletek számánál közelebbi vagy nagyobb számra növelje.

## <a name="example-batch-net"></a>Példa: Batch .NET

A következő C# kódrészletek a Batch .NET API-val nagy számú feladat hozzáadásakor konfigurált beállításokat jelenítik meg.

A feladatok átviteli sebességének növeléséhez növelje a BatchClient [maxanalyticsunits](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) tulajdonságának értékét [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet). Például:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
Vegyen fel egy feladatot a feladatba a [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) vagy a [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) metódus megfelelő túlterhelése alapján. Például:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>Példa: batch CLI-bővítmény

A [Batch CLI-sablonokkal](batch-cli-templates.md)rendelkező Azure batch CLI-bővítmények használatával hozzon létre egy feladat- [előállítót](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md)tartalmazó sablon JSON-fájlt. A feladat-előállító egy adott feladathoz kapcsolódó feladatok gyűjteményét konfigurálja egy adott feladat-definícióból.  

A következő példa egy minta feladatot tartalmazó sablon egy nagy mennyiségű feladattal rendelkező egydimenziós parametrikus sweep-feladathoz – ebben az esetben 250 000. A feladat parancssora egy egyszerű `echo` parancs.

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
Ha feladatot szeretne futtatni a sablonnal, tekintse meg a következőt: [Azure batch CLI-sablonok és fájlátvitel használata](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Példa: batch Python SDK-bővítmény

A Python SDK-bővítmény Azure Batch használatához először telepítse a Python SDK-t és a bővítményt:

```
pip install azure-batch
pip install azure-batch-extensions
```

Az SDK- `BatchExtensionsClient` bővítményt használó a beállítása:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Feladathoz hozzáadandó tevékenységek gyűjteményének létrehozása. Például:


```python
tasks = list()
# Populate the list with your tasks
...
```

Adja hozzá a feladat-gyűjteményt a [Task. add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)használatával. Állítsa be `threads` a paramétert az egyidejű műveletek számának növeléséhez:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

A Batch Python SDK bővítmény a feladat-előállító JSON-specifikációjának használatával is támogatja a feladat-paraméterek feladathoz való hozzáadását. Például az előző batch CLI-sablonban a következőhöz hasonló, a paraméteres sweephöz tartozó feladatok paramétereinek konfigurálása:

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

Adja hozzá a feladatok paramétereit a feladatokhoz. Állítsa be `threads` a paramétert az egyidejű műveletek számának növeléséhez:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>További lépések

* További információ az Azure Batch CLI-bővítmény [Batch CLI-sablonokkal](batch-cli-templates.md)történő használatáról.
* További információ a [Batch PYTHON SDK bővítményről](https://pypi.org/project/azure-batch-extensions/).
