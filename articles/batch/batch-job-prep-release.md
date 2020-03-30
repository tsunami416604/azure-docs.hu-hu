---
title: Feladatok létrehozása & számítási csomópontokon végzett teljes feladatok előkészítéséhez - Azure Batch
description: Feladatszintű előkészítési feladatok segítségével minimalizálhatja az Azure Batch számítási csomópontjaira történő adatátvitelt, és felszabadíthatja a feladatokat a csomópont karbantartásához a feladat befejezésekor.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: d9f6f015c210592d5d8053b1b34d5357bb357629
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586784"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Feladat-előkészítési és feladatkiadási feladatok futtatása kötegelt számítási csomópontokon

 Az Azure Batch-feladat gyakran igényel valamilyen formában a beállítás, mielőtt a feladatok végrehajtása, és a feladat utáni karbantartás, amikor a feladatok befejeződtek. Előfordulhat, hogy le kell töltenie a közös feladatbemeneti adatokat a számítási csomópontokra, vagy a feladat befejezése után fel kell töltenie a feladat kimeneti adatait az Azure Storage-ba. A **feladatok előkészítésével** és **a feladatok kiadási** feladataival végrehajthatja ezeket a műveleteket.

## <a name="what-are-job-preparation-and-release-tasks"></a>Mik azok a feladatok előkészítésével és felszabadítási feladataival?
A feladat feladatainak futtatása előtt a feladat-előkészítési feladat fut az összes számítási csomóponton, amely legalább egy feladat futtatására van ütemezve. A feladat befejezése után a feladatkiadási feladat a készlet minden olyan csomópontján fut, amely legalább egy feladatot végrehajtott. A szokásos Kötegelt feladatokhoz megfelelően megadhat egy parancssort, amelyet meg kell hívni, amikor egy feladat-előkészítési vagy kiadási feladat fut.

A feladat-előkészítési és kiadási feladatok olyan jól ismert Batch feladatfunkciókat kínálnak, mint a fájlletöltés ([erőforrásfájlok),][net_job_prep_resourcefiles]emelt szintű végrehajtás, egyéni környezeti változók, maximális végrehajtási időtartam, újrapróbálkozások száma és a fájlmegőrzési idő.

A következő szakaszokban megtudhatja, hogyan használhatja a [Batch .NET][api_net] könyvtárban található [JobPreparationTask][net_job_prep] és [JobReleaseTask][net_job_release] osztályokat.

> [!TIP]
> A feladatok előkészítésével és a kiadási feladatokkal kapcsolatban különösen hasznosak a "megosztott készlet" környezetekben, ahol a számítási csomópontok készlete megmarad a feladatfuttatások között, és számos feladat használja.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Mikor kell használni a feladat-előkészítési és kiadási feladatokat?
A munkaelőkészítési és kiadási feladatok a következő helyzetekre felelnek meg:

**Közös feladatadatok letöltése**

A kötegelt feladatok gyakran közös adathalmazt igényelnek a feladat feladataihoz. A napi kockázatelemzési számításokban például a piaci adatok munkaspecifikusak, mégis közösek a feladat összes feladatára. Ezt a piaci adatokat, gyakran több gigabájt méretű, minden számítási csomópontra csak egyszer kell letölteni, hogy a csomóponton futó feladatok használhassák. Egy **feladat-előkészítési feladat** segítségével töltse le ezeket az adatokat minden csomópontra a feladat egyéb feladatainak végrehajtása előtt.

**Feladat és feladat kimenetének törlése**

Egy "megosztott készlet" környezetben, ahol a készlet számítási csomópontjai nem szűnnek meg a feladatok között, előfordulhat, hogy törölnie kell a feladatadatokat a futtatások között. Előfordulhat, hogy lemezterületet kell megtakarítana a csomópontokon, vagy teljesítenie kell a szervezet biztonsági házirendjeit. A **feladatkiadási feladat segítségével** törölheti a feladat-előkészítési feladat által letöltött vagy a feladat végrehajtása során létrehozott adatokat.

**Naplómegőrzés**

Előfordulhat, hogy meg szeretné tartani a feladatok által létrehozott naplófájlok másolatát, vagy esetleg összeomlási memóriaképfájlokat szeretne létrehozni, amelyeket sikertelen alkalmazások hozhatnak létre. Ilyen esetekben egy **feladatkiadási feladat** használatával tömörítheti és feltöltheti ezeket az adatokat egy [Azure Storage-fiókba.][azure_storage]

> [!TIP]
> A naplók és más feladat- és feladatkimeneti adatok megőrzésének másik módja az [Azure Batch-fájlkonvenciók](batch-task-output.md) könyvtár használata.
>
>

## <a name="job-preparation-task"></a>Feladat-előkészítési feladat


A feladat feladatainak végrehajtása előtt a Batch végrehajtja a feladat-előkészítési feladatot minden feladatfuttatására ütemezett számítási csomóponton. Alapértelmezés szerint a Batch megvárja a feladat-előkészítési feladat befejezését, mielőtt futtatna a csomóponton végrehajtandó feladatokat. Azonban beállíthatja, hogy a szolgáltatás ne várjon. Ha a csomópont újraindul, a feladat-előkészítési feladat újrafut. Ezt a viselkedést le is tilthatja. Ha van egy feladat, amelynek feladat-előkészítési feladata van, és a feladatkezelő feladat konfigurálva van, a feladat-előkészítési feladat a feladatkezelő feladat előtt fut, ugyanúgy, mint az összes többi feladat esetében. A feladat-előkészítési feladat mindig először fut.

A feladat-előkészítési feladat végrehajtása csak olyan csomópontokon történik, amelyek egy feladat futtatására vannak ütemezve. Ez megakadályozza az előkészítési feladat szükségtelen végrehajtását abban az esetben, ha egy csomóponthoz nincs hozzárendelve feladat. Ez akkor fordulhat elő, ha egy feladat hoz a feladatok száma kisebb, mint a készletben lévő csomópontok száma. Akkor is érvényes, ha [az egyidejű feladat végrehajtás](batch-parallel-node-tasks.md) engedélyezve van, így néhány csomópont tétlen marad, ha a feladatszáma alacsonyabb, mint a teljes lehetséges egyidejű feladatok száma. Ha nem fut a feladat-előkészítési feladat tétlen csomópontokon, kevesebb pénzt költhet adatátviteli díjakra.

> [!NOTE]
> [A JobPreparationTask][net_job_prep_cloudjob] különbözik [a CloudPool.StartTask fájltól,][pool_starttask] mivel a JobPreparationTask végrehajtása az egyes feladatok kezdetén történik, míg a StartTask csak akkor hajt ható le, ha egy számítási csomópont először csatlakozik egy készlethez, vagy újraindul.
>


>## <a name="job-release-task"></a>Feladatkiadási feladat

Miután egy feladat befejezettként van megjelölve, a feladatkiadási feladat végrehajtása a készlet minden olyan csomópontján, amely legalább egy feladatot végrehajtott. A feladat befejezettként való megjelölése a megszüntetési kérelem kiadásával. A Batch szolgáltatás ezután a feladat állapotát *a lefújásra állítja,* leállítja a feladathoz társított aktív vagy futó feladatokat, és futtatja a feladatkiadási feladatot. A feladat ezután a *befejezett* állapotba kerül.

> [!NOTE]
> A feladat törlése a feladatkiadási feladatot is végrehajtja. Ha azonban egy feladat már le van állítva, a kiadási feladat nem fut le másodszor, ha a feladatot később törlik.

A feladatok kiadási feladatai legfeljebb 15 percig futhatnak, mielőtt a Batch szolgáltatás leállítja őket. További információt a [REST API referenciadokumentációjában](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)talál.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Feladatelőkészítési és kiadási feladatok a Batch .NET segítségével
Feladat-előkészítési feladat használatához rendeljen hozzá egy [JobPreparationTask][net_job_prep] objektumot a feladat [CloudJob.JobPreparationTask][net_job_prep_cloudjob] tulajdonságához. Hasonlóképpen inicializálja a [JobReleaseTask,és][net_job_release] rendelje hozzá a feladat [CloudJob.JobReleaseTask][net_job_prep_cloudjob] tulajdonsága a feladat kiadási feladatának beállításához.

Ebben a kódrészletben `myBatchClient` a [BatchClient][net_batch_client]egy `myPool` példánya, és a Batch-fiókban lévő készlet.

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

Mint korábban említettük, a kiadási feladat végrehajtása, ha egy feladat leáll vagy töröl. Feladat leállítása a [JobOperations.TerminateJobAsync segítségével.][net_job_terminate] Feladat törlése a [JobOperations.DeleteJobAsync fájllal.][net_job_delete] Általában akkor szakít meg vagy töröl egy feladatot, amikor a feladatok befejeződnek, vagy ha elérte a megadott időtúlot.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Kódminta a GitHubon
A feladat előkészítéséhez és a feladatok kiadásához tekintse meg a [MunkaprepKiadás][job_prep_release_sample] mintaprojektet a GitHubon. Ez a konzolalkalmazás a következőket teszi:

1. Két csomónos készletet hoz létre.
2. Feladat létrehozása feladatelőkészítéssel, kiadással és szabványos feladatokkal.
3. Futtatja a feladat-előkészítési feladatot, amely először írja a csomópontazonosítót egy szövegfájlba egy csomópont "megosztott" könyvtárában.
4. Minden csomóponton futtat egy feladatot, amely a feladatazonosítóját ugyanabba a szövegfájlba írja.
5. Miután az összes feladat befejeződött (vagy elérte az időtúllépést), kinyomtatja az egyes csomópontok szövegfájljának tartalmát a konzolra.
6. A feladat befejezése után futtatja a feladatkiadási feladatot, hogy törölje a fájlt a csomópontról.
7. Kinyomtatja a feladatelőkészítésével és felszabadításával kapcsolatos feladatok kilépési kódjait minden olyan csomóponthoz, amelyen végrehajtották.
8. Szünetelteti a végrehajtást, hogy lehetővé tegye a feladat és/vagy a készlet törlésének megerősítését.

A mintaalkalmazás ból származó kimenet hasonló a következőhöz:

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
> Az új készletben lévő csomópontok változólétrehozása és kezdési ideje miatt (egyes csomópontok készen állnak a feladatokra, mielőtt mások), eltérő kimenetjelenhet meg. Pontosabban, mivel a feladatok gyorsan befejeződnek, a készlet egyik csomópontja végrehajthatja a feladat összes feladatát. Ebben az esetben észre fogja venni, hogy a feladat előkészítésére és kiadására vonatkozó feladatok nem léteznek a feladatokat nem elvégzett csomóponthoz.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Feladat-előkészítési és kiadási feladatok vizsgálata az Azure Portalon
A mintaalkalmazás futtatásakor az [Azure Portalon][portal] megtekintheti a feladat tulajdonságait és feladatait, vagy akár letöltheti a feladat feladatai által módosított megosztott szövegfájlt is.

Az alábbi képernyőkép az **Előkészítési feladatok panelt** mutatja az Azure Portalon a mintaalkalmazás futtatása után. Keresse meg a *JobPrepReleaseSampleJob* tulajdonságokat a feladatok befejezése után (de a feladat és a készlet törlése előtt), majd kattintson az **Előkészítési feladatok** vagy **a Feladatok feloldása** parancsra a tulajdonságaik megtekintéséhez.

![Feladat-előkészítési tulajdonságok az Azure Portalon][1]

## <a name="next-steps"></a>További lépések
### <a name="application-packages"></a>Alkalmazáscsomagok
A feladat-előkészítési feladat mellett a Batch [alkalmazáscsomagok](batch-application-packages.md) szolgáltatásával is előkészítheti a számítási csomópontokat a feladat végrehajtására. Ez a szolgáltatás különösen olyan alkalmazások telepítéséhez hasznos, amelyek nem igényelnek telepítőt, sok (100+) fájlt tartalmazó alkalmazásokat vagy szigorú verziókövetést igénylő alkalmazásokat.

### <a name="installing-applications-and-staging-data"></a>Alkalmazások és átmeneti adatok telepítése
Ez az MSDN fórumbejegyzés áttekintést nyújt a csomópontok futási feladatokra való előkészítésének számos módszeréről:

[Alkalmazások és átmeneti adatok telepítése batch számítási csomópontokon][forum_post]

Az Azure Batch-csapat egyik tagja által írt, számos technikát, amelyek segítségével alkalmazások és adatok számítási csomópontok.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
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
