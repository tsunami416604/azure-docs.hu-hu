---
title: Hozzon létre feladatokat, feladatok és a számítási csomópontok - Azure Batch egy teljes feladat előkészítése |} Microsoft Docs
description: Feladat szintű előkészítő feladatok minimalizálása érdekében az adatok átvitele Azure Batch számítási csomópontok használja, és felszabadíthatja a csomópont karbantartási feladat befejezésére, a feladatok.
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
ms.openlocfilehash: 543c03c22b31389c3d6e048cc9f13c24add5aae7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30314721"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Futtatási feladat előkészítése és a feladat kiadási tevékenységek a Batch számítási csomópontjain

 Egy Azure kötegelt gyakran néhány adatot kér a telepítő a feladatokat hajtja végre a rendszer, és karbantartási utáni feladat, a feladatok befejezése előtt. Szükség lehet a számítási csomópontok közös tevékenység bemeneti adatok letöltése, vagy töltse fel a tevékenység kimeneti adatok Azure Storage a feladat befejezése után. Használhat **előkészítő feladat** és **kiadás feladat** feladatok a műveletek végrehajtására.

## <a name="what-are-job-preparation-and-release-tasks"></a>Mi feladat előkészítése és a feladatok kiadási?
Mielőtt egy feladat tevékenységeit futtatja, a feladat előkészítése tevékenység ütemezett legalább egy feladat minden számítási csomóponton fut. Ha a feladat befejeződött, a feladat kiadása tevékenység fut, a készlet, amely legalább egy feladat végrehajtása minden egyes csomóponton. Csakúgy, mint a normál kötegelt feladatok, megadhatja a parancssorból hívható meg a feladat előkészítése vagy kiadási feladat futtatásakor.

Feladat előkészítése és a kiadási tevékenységek megszokott kötegelt feladat szolgáltatásokat kínálnak például fájl letöltése ([erőforrásfájlok][net_job_prep_resourcefiles]), emelt szintű végrehajtási, egyéni környezeti változókat, végrehajtási maximális időtartam, újrapróbálkozások maximális számát és fájl adatmegőrzési időtartam.

A következő szakaszban megtudhatja, hogyan használatára a [JobPreparationTask] [ net_job_prep] és [JobReleaseTask] [ net_job_release] osztályok találhatók az [Batch .NET] [ api_net] könyvtár.

> [!TIP]
> Feladat előkészítése és a kiadási tevékenységek hasznosak lehetnek "megosztás készlet" környezetben, amelyben számítási csomópontok készlete közötti feladat futtatása továbbra is fennáll, és sok feladatok által használt.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Mikor érdemes használni a feladat előkészítése és a kiadási tevékenységek
Feladat előkészítése és a feladat kiadása feladatok esetében a remekül beválik, ha az alábbi helyzetekben:

**Általános feladat adatok letöltése**

Kötegelt feladatok gyakran megkövetelik a közös adatkészletet a feladat tevékenységeit bemenetként. Például napi kockázati elemzés számítások piaci adata feladat-specifikus, a feladat összes tevékenységében még közös. A piacon adatok gyakran számos gigabájtig terjedhetnek, le kell tölteni minden számítási csomópont csak egyszer, hogy a csomóponton futó minden feladatot használhatja azt. Használja a **feladat előkészítése tevékenységet** az adatok letöltése minden csomópont más tevékenységek a feladat végrehajtása előtt.

**Feladat- és kimeneti törlése**

"Megosztás készlet" környezetben, ahol nincsenek leszerelt feladatok között a készlet számítási csomópontokat, előfordulhat, hogy törölni szeretne feladatadatok fusson. Szükség lehet a csomópontokon lemezterületet takarítson meg, vagy megfelelnek a szervezet biztonsági házirendjeivel. Használja a **feladat kiadása tevékenység** tölti le a feladat előkészítése tevékenység, vagy a feladat a végrehajtás során előállított adatokat törli.

**Napló megőrzési**

Előfordulhat, hogy szeretné megőrizni a feladatok által létrehozott naplófájlok másolatát, vagy esetleg összeomlási memóriaképek sikertelen alkalmazások által létrehozott. Használja a **feladat kiadása tevékenység** ebben az esetben tömörítéséhez, és töltse fel ezeket az adatokat egy [Azure Storage] [ azure_storage] fiók.

> [!TIP]
> Másik módja megőrizni a naplók és egyéb feladat- és kimeneti adatok használata a [Azure Batch fájl egyezmények](batch-task-output.md) könyvtár.
> 
> 

## <a name="job-preparation-task"></a>Feladat előkészítése tevékenység
Egy feladat feladatok végrehajtása, mielőtt kötegelt ütemezett feladat futtatása minden számítási csomóponton a feladat előkészítése tevékenység hajtja végre. Alapértelmezés szerint a Batch szolgáltatás megvárja, a feladat előkészítése tevékenység befejezésének végrehajtásához a csomóponton ütemezett feladatok futtatása előtt. Azonban a szolgáltatást, nem a Várakozás konfigurálhatja. Ha a csomópont újraindul, a feladat előkészítése tevékenység fut újra, de le is tilthatja ezt a viselkedést.

A feladat előkészítése tevékenység csak a csomópontokra, amelyeket az ütemezett feladat végrehajtása. Ez megakadályozza, hogy a szükségtelen egy előkészítő feladat végrehajtása, ha egy csomópont nem hozzá van rendelve egy feladatot. Ez akkor fordulhat elő, ha a feladat feladatok száma nem éri el a készletben csomópontok száma. Akkor is érvényes, ha [egyidejű feladat a végrehajtás](batch-parallel-node-tasks.md) van engedélyezve, amely hagyja néhány csomópont tétlen if a feladatok száma nem éri el az összes lehetséges egyidejű feladatok. Nem fut a feladat előkészítése tevékenység üresjárati csomóponton, tölthet adatok adatátviteli költségek kevesebb pénz.

> [!NOTE]
> [JobPreparationTask] [ net_job_prep_cloudjob] eltér az [CloudPool.StartTask] [ pool_starttask] abban a JobPreparationTask elején minden feladatot hajt végre, mivel StartTask végrehajtja, csak ha a számítási csomópont először csatlakozik egy készletet vagy újraindul.
> 
> 

## <a name="job-release-task"></a>Feladat kiadása tevékenység
Ha egy feladat fejeződött be van jelölve, a feladat kiadása tevékenység végrehajtása a készletben legalább egy feladat végrehajtott minden egyes csomóponton. Megszakítási kérelmet befejezte megjelölése az egy feladatot. A Batch szolgáltatás majd feladat állapotba állítása *leáll*, a feladathoz társított minden aktív vagy futó feladatot megszakítja, és a feladat kiadása tevékenységet futtatja. A feladat majd áttér az *befejeződött* állapotát.

> [!NOTE]
> Feladat törlése a feladat kiadása tevékenységet is végez. Azonban ha egy feladat már meg lett szakítva, a kiadás feladat nem fut még egyszer a feladat később törlésekor.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Előkészítő feladat, és felszabadíthatja a feladatok a Batch .NET kódtárral
A feladat előkészítése tevékenység használatához hozzárendelése egy [JobPreparationTask] [ net_job_prep] a feladathoz objektum [CloudJob.JobPreparationTask] [ net_job_prep_cloudjob] tulajdonság. Ehhez hasonlóan inicializálni egy [JobReleaseTask] [ net_job_release] , és rendelje hozzá a projekthez [CloudJob.JobReleaseTask] [ net_job_prep_cloudjob] tulajdonság beállításához a feladat kiadása tevékenységet.

A következő kódrészletet `myBatchClient` példánya [BatchClient][net_batch_client], és `myPool` van egy meglévő készlet belül a Batch-fiók.

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

Amint azt korábban említettük, a kiadási tevékenység végrehajtása, ha egy feladat megszakadt, vagy törölték. Egy feladat leáll [JobOperations.TerminateJobAsync][net_job_terminate]. A feladat törlése [JobOperations.DeleteJobAsync][net_job_delete]. Általában leáll, vagy törli a feladatot, feladatainak befejezésekor, vagy amikor eléri a beállított időkorlát.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>A Githubon kódminta
Tekintse meg a feladat előkészítése, és felszabadíthatja a feladatokat a művelet, tekintse meg a [JobPrepRelease] [ job_prep_release_sample] mintaprojektet a Githubon. Ezt a konzolalkalmazást a következőket teszi:

1. Egy készletet hoz létre két "kicsi" csomópont.
2. Létrehoz egy feladatot a feladat előkészítése, a kiadás és a szabványos feladatok.
3. A csomópont-azonosító először ír a fájlt egy csomópont "megosztott" directory feladat előkészítése tevékenység fut.
4. Futtat egy feladatot, amely a azonos szöveges fájlba ír, a tevékenység azonosítója minden egyes csomóponton.
5. Miután minden feladat befejeződött (vagy az időkorlát elérése), jelenít meg a konzol minden csomópont szöveges fájl tartalmát.
6. A feladat befejezése után futtatja a fájl törléséhez a csomópontról kiadás feladata.
7. A feladat előkészítése és a kiadási feladatokat az egyes csomópontok, amelyeken végre kilépési kódot jelenít meg.
8. Engedélyezi a feladat és/vagy a készlet törlési megerősítés végrehajtási szünetel.

A minta-alkalmazás kimenete az alábbihoz hasonló:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
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
> A változó létrehozása és a kezdési idő a csomópontok egy új készletet (az egyes csomópontok állnak készen a többi előtt feladatok), mert a különböző kimeneti jelenhet meg. Pontosabban a feladatok gyorsan végezze el, mert a készlet csomópontok egyikét futhat-e a feladat feladatokat. Ha ez történik, megfigyelheti, hogy a feladat előkészítése és a kiadási tevékenységek nem léteznek, amelyek nincsenek feladatok végrehajtása a csomópont.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Vizsgálja meg a feladat előkészítése és a kiadási feladatokat az Azure-portálon
Futtassa a mintaalkalmazást, használhatja a [Azure-portálon] [ portal] a feladatot, és a feladatok tulajdonságainak megtekintéséhez, vagy akár töltse le a megosztott szövegfájlt, amely a munka feladatai módosítja.

Az alábbi képernyőfelvételen a **előkészítő feladatok panel** a mintaalkalmazás futását követően az Azure portálon. Keresse meg a *JobPrepReleaseSampleJob* tulajdonságok a feladatok befejezése után (de a feladat és a készlet törlése előtt), majd **előkészítő feladatok** vagy **kiadási tevékenységek** a tulajdonságok megtekintéséhez.

![Azure-portálon feladattulajdonság előkészítése][1]

## <a name="next-steps"></a>További lépések
### <a name="application-packages"></a>Alkalmazáscsomagok
A feladat előkészítése tevékenység mellett is használhatja a [alkalmazáscsomagok](batch-application-packages.md) köteg számítási csomópontok előkészítése a feladat végrehajtása a szolgáltatás. Ez a szolgáltatás különösen fontos telepítőhöz, alkalmazásokat, amelyek számos fájljait (100 +) vagy szigorú verziókezelést igénylő alkalmazások nem igénylő alkalmazások központi telepítéséhez.

### <a name="installing-applications-and-staging-data"></a>Alkalmazások telepítése és átmeneti adatokat
Az MSDN fórum bejegyzése a csomópont előkészítése az éppen futó feladatok többféle módszer áttekintése:

[Alkalmazások telepítése és átmeneti adatokat a Batch számítási csomópontjain][forum_post]

Egy Azure Batch-csapat tagjai által írt, hogy miként használhatja, alkalmazások és adatok számítási csomópontjain telepítendő különböző módszereket.

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
