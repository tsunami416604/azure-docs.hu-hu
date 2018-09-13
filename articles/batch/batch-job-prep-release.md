---
title: Fel a feladatokat és a teljes feladat a számítási csomópontokon – Azure Batch-feladatok létrehozása |} A Microsoft Docs
description: Feladatszintű előkészítő feladatok Azure Batch számítási csomópontokon való adatátvitel minimalizálása érdekében használni, és kiadási tevékenységek a feladat befejezésekor a csomópont karbantartásához.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: da69cc22fbb071ce3fa4b2c53aaf0b1ec4ba5e46
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35927577"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Futtatási feladat-előkészítési és a feladatkiadási tevékenységeket a Batch számítási csomópontokon

 Az Azure Batch-feladatok gyakran szükséges a telepítő valamilyen, előtt a feladatok végrehajtása, és karbantartási utáni feladat, amikor a feladat befejeződött. Szüksége lehet, hogy töltse le a gyakori tevékenység bemeneti adatokat a számítási csomópontokra, vagy töltse fel a feladat kimeneti adatokat az Azure Storage a feladat befejezése után. Használhat **a feladat-előkészítési** és **kiadási feladat** feladatokat a műveletek végrehajtására.

## <a name="what-are-job-preparation-and-release-tasks"></a>Milyen feladat-előkészítési és kiadási tevékenységek?
Feladatok futtatása, mielőtt a feladat-előkészítési tevékenység fut, legalább egy feladat futtatására ütemezett összes számítási csomóponton. Ha a feladat befejeződött, a feladatkiadási tevékenységet, amely legalább egy tevékenységet a készletben lévő minden egyes csomóponton fut. Csakúgy, mint normál Batch-tevékenységek, egy feladat-előkészítési vagy kiadási tevékenység futtatásakor meg kell hívni a parancssorból is megadhat.

Feladat-előkészítési és -kiadási tevékenységek ismerős kötegelt feladat szolgáltatásokat kínálnak például fájlokat tölthet le ([erőforrásfájlok][net_job_prep_resourcefiles]), emelt szintű végrehajtási, egyéni környezeti változókat, maximális végrehajtási időtartamát, próbálkozzon újra száma, és a fájlmegőrzési időt.

A következő szakaszokban elsajátíthatja, hogyan használható a [JobPreparationTask] [ net_job_prep] és [JobReleaseTask] [ net_job_release] a találhatóosztályok[ A Batch .NET] [ api_net] könyvtár.

> [!TIP]
> Feladat-előkészítési és -kiadási tevékenységek hasznosak lehetnek a "megosztás készlet" környezetekben, amelyben számítási csomópontok készletét közötti feladatfuttatás továbbra is fennáll, és számos feladat által használt.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Mikor használja a feladat-előkészítési és -kiadási tevékenységek
Feladat-előkészítési és a feladatkiadási tevékenységeket is jó megoldás lehet a következő helyzetekben:

**Töltse le a gyakori feladat adatai**

Batch-feladatok gyakran van szükség egy közös adatkészletet bemenetként a feladat tevékenységei. Például a napi kockázati elemzés számításokban, piaci adatok használata feladat-specifikus, még gyakori feladatok a feladat. A piaci adatokat, gyakran több GB méretű, kell letölteni minden számítási csomópont csak egyszer, hogy a csomóponton futó összes tevékenység is használhassák. Használja a **feladat-előkészítési tevékenység** , töltse le ezeket az adatokat minden csomóponton, mielőtt a feladat végrehajtása a más feladatok.

**Feladatok és tevékenységek kimenetének törlése**

"Megosztás készlet" környezetben, ahol a készlet számítási csomópontjait nem állnak leszerelt feladatok között, szükség lehet törölni a feladatadatok futtatásai között. Szüksége lehet takaríthat meg lemezterületet a csomópontjai, és megfelelnek a szervezet biztonsági házirendjében. Használja a **feladatkiadási tevékenység** törli az adatokat tölti le a feladat-előkészítési tevékenységet, vagy a feladat a végrehajtás során.

**Napló megőrzése**

Érdemes tárol egy másolatot a tevékenységek által létrehozott naplófájlokat, vagy talán összeomlási memóriaképek fájljait, hogy a hibás alkalmazásokat hozhat létre. Használja a **feladatkiadási tevékenység** tömörítése és töltheti fel az ilyen esetekben egy [Azure Storage] [ azure_storage] fiókot.

> [!TIP]
> Másik módja, hogy továbbra is fennáll, naplók és egyéb feladatok és tevékenységek kimeneti adatok használata a [Azure Batch File Conventions](batch-task-output.md) könyvtár.
> 
> 

## <a name="job-preparation-task"></a>Feladat előkészítése tevékenység
Feladatok végrehajtását, mielőtt Batch végrehajtja a feladat-előkészítési tevékenység minden számítási csomóponton, ütemezett feladat futtatása. Alapértelmezés szerint a Batch szolgáltatás megvárja, amíg a feladat-előkészítési tevékenység befejezésének ütemezve, hogy a csomóponton a feladat futtatása előtt. A szolgáltatás ne várjon azonban konfigurálhatja. A csomópont újraindul, ha a feladat-előkészítési tevékenység fut újra, de letilthatja ezt a viselkedést.

A feladat-előkészítési tevékenység végrehajtása csak a csomópontok, ütemezett feladat futtatása. Ez megakadályozza, hogy egy előkészítési tevékenység szükségtelen végrehajtásának abban az esetben, ha egy csomópont nincs hozzárendelve egy feladatot. Ez akkor fordulhat elő, ha a feladatok száma kisebb, mint a készletben működő csomópontok számát. Azt is vonatkozik, amikor [egyidejű feladat a végrehajtás](batch-parallel-node-tasks.md) van engedélyezve, amely hagy egyes csomópontok üresjárati if a tevékenységek száma kisebb, mint az összes lehetséges egyidejű feladatok. A feladat-előkészítési tevékenység nem futtat üresjáratban csomópontok, kevesebb kiadás képes költeni adatforgalmi díjat.

> [!NOTE]
> [JobPreparationTask] [ net_job_prep_cloudjob] eltér [CloudPool.StartTask] [ pool_starttask] abban, hogy a JobPreparationTask elején minden feladatot hajt végre, mivel a StartTask csak amikor számítási csomópontok először csatlakozik a készlethez végrehajtja vagy újraindul.
> 
> 

## <a name="job-release-task"></a>Feladat kiadása tevékenység
Miután a feladat befejezettként van megjelölve, a feladatkiadási tevékenység végrehajtása, amely legalább egy tevékenységet a készletben lévő minden egyes csomóponton. Hogy befejezettként egy feladatot egy megszakítási kérelmet kiállításával. A Batch szolgáltatás majd állítja be a feladat állapotának *megszakítást okozó*, bármely feladathoz hozzárendelt aktív vagy futó tevékenységek leáll, és futtatja a feladatkiadási tevékenység. A feladat majd áttér a *befejeződött* állapota.

> [!NOTE]
> Feladat törlése a feladatkiadási tevékenységet is végez. Azonban ha egy feladat már meg lett szakítva, a feladatkiadási tevékenység nem fut, másodszor Ha később a feladatot törölték.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Előkészítő feladat és a kiadási tevékenységek a Batch .NET használatával
Feladat-előkészítési tevékenységet használ, rendeljen hozzá egy [JobPreparationTask] [ net_job_prep] objektum a feladat [CloudJob.JobPreparationTask] [ net_job_prep_cloudjob] tulajdonság. Ehhez hasonlóan inicializálása egy [JobReleaseTask] [ net_job_release] és rendelje hozzá a feladat [CloudJob.JobReleaseTask] [ net_job_prep_cloudjob] tulajdonságát állítsa a feladat feladatkiadási tevékenység.

Ez a kódrészlet a `myBatchClient` példánya [BatchClient][net_batch_client], és `myPool` van egy meglévő készletet a Batch-fiókon belül.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Ahogy korábban említettük, amikor egy feladat megszakadt, vagy törölte a feladatkiadási tevékenység hajtja végre. A feladat leállítása [JobOperations.TerminateJobAsync][net_job_terminate]. A feladat törlése [JobOperations.DeleteJobAsync][net_job_delete]. Általában leáll, vagy egy feladat törlése, ha a feladat befejeződött, vagy ha elérte, hogy meghatározta időtúllépés.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Példa kódja a Githubon
Feladat-előkészítési és kiadási tevékenységek működés megtekintéséhez tekintse meg a [JobPrepRelease] [ job_prep_release_sample] mintaprojektet a Githubon. A Konzolalkalmazás a következőket teszi:

1. Létrehoz egy készletet két csomópont.
2. Létrehoz egy feladatot a feladat-előkészítési, kiadási és a szabványos tevékenységeket.
3. Futtatja a feladat-előkészítési tevékenység, amely először írja a csomópont-azonosító egy csomópont "megosztott" könyvtárat a fájlt.
4. Futtat egy feladatot, amely ugyanazt a szöveges fájlt ír a Feladatazonosítót minden egyes csomóponton.
5. Miután minden feladat befejeződött (vagy a időtúllépését), jelenít meg a konzol egyes csomópontok szöveges fájl tartalmát.
6. A feladat befejezése után futtatja a feladatkiadási tevékenység a fájl törléséhez a csomópontról.
7. Megjeleníti a kilépési kódot, a feladatok előkészítési és -kiadási tevékenységek, amelyen végrehajtott minden egyes csomópont esetében.
8. Szünetel a végrehajtási, hogy a feladat és/vagy a készlet törlésének megerősítését.

A mintaalkalmazás kimenete az alábbihoz hasonló:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> A csomópontok egy új készletet (az egyes csomópontok állnak készen a többi előtt feladatok) változó létrehozása és a kezdési idő, mert eltérő kimeneti jelenhet meg. Pontosabban a feladatok gyorsan befejeződhessen, mert a készlet egyik csomópontján előfordulhat, hogy hajtsa végre a feldolgozás feladatokat. Ha ez történik, láthatja, hogy a feladat-előkészítési és a feladatkiadási tevékenységeket, amelyek nincsenek feladatok végrehajtása a csomópont nem létezik.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Vizsgálja meg a feladat-előkészítési és a feladatkiadási tevékenységeket az Azure Portalon
A mintaalkalmazás futtatásakor használhatja a [az Azure portal] [ portal] megtekintheti a tulajdonságait, a feladatot és annak tevékenységeit, vagy akár le a feladat tevékenységei által módosított, megosztott szövegfájl.

Az alábbi képernyőképen a **előkészítő feladatok panelen** egy, a mintaalkalmazás futtatása után az Azure Portalon. Keresse meg a *JobPrepReleaseSampleJob* tulajdonságok a feladatok elvégzése után (de még mielőtt törölhetné a feladatot és készletet), és kattintson a **előkészítési** vagy **kiadási tevékenységek**tulajdonságaik megtekintéséhez.

![Feladat-előkészítési tulajdonságai az Azure Portalon][1]

## <a name="next-steps"></a>További lépések
### <a name="application-packages"></a>Alkalmazáscsomagok
A feladat-előkészítési tevékenység mellett is használhatja a [alkalmazáscsomagok](batch-application-packages.md) a feladat végrehajtása fel a számítási csomópontok Batch szolgáltatás. Ez a szolgáltatás különösen hasznos telepítő, (több mint 100) sok fájlt tartalmazó alkalmazások vagy szigorú verziókövetés igénylő alkalmazások nem igénylő alkalmazások üzembe helyezéséhez.

### <a name="installing-applications-and-staging-data"></a>Alkalmazások telepítése és az adatok
Ebben az MSDN-fórum bejegyzése nyújt áttekintést, többféle módszer áll rendelkezésre a futó tevékenységeket a csomópontok előkészítése:

[Alkalmazások telepítése és az adatok a Batch számítási csomópontokon][forum_post]

Az Azure Batch-csapat tagjainak egyike által írt, azt számos módszert ismertet, amelyek segítségével helyezhet üzembe alkalmazásokat és az adatok számítási csomópontokra.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
