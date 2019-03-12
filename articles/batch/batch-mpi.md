---
title: Többpéldányos tevékenységek használata Azure Batch - MPI-alkalmazások futtatásához |} A Microsoft Docs
description: Ismerje meg, hogyan hajtható végre a Message Passing Interface (MPI) alkalmazásokhoz az Azure Batchben a többpéldányos feladattípus használatával.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 06/12/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 826b0d2e56034a99130962f259c5c4297a31daf7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537269"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Többpéldányos tevékenységek használata Message Passing Interface (MPI) applications futtatása a Batchben

Többpéldányos tevékenységek lehetővé teszik az Azure Batch-feladat futtatása több számítási csomóponton egyidejűleg. Ezek a feladatok lehetővé teszik a nagy teljesítményű feldolgozási forgatókönyveket, például a Message Passing Interface (MPI) alkalmazásokhoz, a Batch szolgáltatásban. Ebből a cikkből megismerheti, hogyan hajtható végre a többpéldányos tevékenységek használata a [Batch .NET] [ api_net] könyvtár.

> [!NOTE]
> Bár ebben a cikkben szereplő példák a Batch .NET-MS-MPI, és Windows számítási csomópontok, itt tárgyalt fogalmakat többpéldányos tevékenység alkalmazhatók más platformot és technológiát, (a Python és a Linux-csomópontokat, például az Intel MPI-t).
>
>

## <a name="multi-instance-task-overview"></a>Többpéldányos tevékenység áttekintése
A Batch szolgáltatásban, minden feladata a normál esetben egy egyetlen számítási csomóponton--elküldése több tevékenységet egy feladathoz, és a Batch szolgáltatás ütemezi minden egyes csomóponton végrehajtandó feladatot. Azonban a tevékenység konfigurálásával **többpéldányos beállítások**, megadhatja a Batch szolgáltatást, hozzon létre egy elsődleges feladat, és több alfeladatra, majd több csomóponton végrehajtott.

![Többpéldányos tevékenység áttekintése][1]

Többpéldányos beállítások egy feladat elküldésekor egy feladatot a Batch a többpéldányos tevékenységek egyedi több lépéseket hajtja végre:

1. A Batch szolgáltatás létrehoz egy **elsődleges** és több **altevékenység** többpéldányos beállítások alapján. Szám (elsődleges, továbbá minden altevékenység) feladatok teljes száma megegyezik **példányok** (számítási csomópontok) a többpéldányos beállításokat ad meg.
2. A Batch jelöl ki, mint a számítási csomópontok egyikét a **fő**, és ütemezi a a fő végrehajtása elsődleges feladata. Hajtsa végre a számítási csomópontok, a többpéldányos tevékenység, a csomópontonkénti egy alfeladat lefoglalt további része az alfeladatok ütemezés.
3. Az elsődleges és minden altevékenység letöltése bármely **közös erőforrásfájlok** a többpéldányos beállításokat ad meg.
4. A közös erőforrások után fájlok le vannak töltve, az elsődleges altevékenység hajtható végre, és a **koordináció parancs** a többpéldányos beállításokat ad meg. A tranzakciókoordináció-parancs általában segítségével előkészítheti a csomópontokat a feladat végrehajtása. Ide tartozhatnak a háttérben futó szolgáltatások indítása (például [Microsoft MPI][msmpi_msdn]a `smpd.exe`) és annak ellenőrzése, hogy a csomópontok készen áll a csomópontok közötti üzenetek feldolgozására.
5. Az elsődleges tevékenység lefut a **alkalmazás parancs** a fő csomópont *után* a tranzakciókoordináció-parancs sikeresen befejeződött az elsődleges és minden altevékenység. Az alkalmazás parancsot a parancssorban a többpéldányos tevékenység magát, és csak az elsődleges tevékenység által végrehajtott. Az egy [MS-MPI][msmpi_msdn]-alapú megoldás, ez az, ahol végre, az MPI-kompatibilis alkalmazást a `mpiexec.exe`.

> [!NOTE]
> Habár funkcionálisan különálló, a "többpéldányos tevékenység" nem egy egyedi feladat típusa, például a [StartTask] [ net_starttask] vagy [JobPreparationTask] [ net_jobprep]. A többpéldányos tevékenység egyszerűen egy szabványos Batch-feladat ([CloudTask] [ net_task] a Batch .NET) amelyek többpéldányos beállítások vannak konfigurálva. Ez a cikk ezt nevezzük a **többpéldányos tevékenység**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Többpéldányos tevékenységek követelményei
Többpéldányos tevékenységek elvégzésére egy készletet a **csomópontok közötti kommunikáció engedélyezve**, és a **le van tiltva egyidejű feladat a végrehajtás**. Egyidejű feladat a végrehajtás letiltásához állítsa be a [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool) tulajdonságot 1-re.

> [!NOTE]
> A batch [korlátok](batch-quota-limit.md#other-limits) egy készletet, amelyen a csomópontok közötti kommunikáció engedélyezett méretét.


Ez a kódrészlet bemutatja, hogyan hozhat létre egy készletet a Batch .NET-kódtár használatával többpéldányos tevékenységek.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> Ha egy többpéldányos tevékenység futtatásakor az egy készletben lévő csomópontok kommunikációs le van tiltva, vagy a egy *maxTasksPerNode* 1-nél nagyobb értéket, a feladat soha nem ütemezett--határozatlan ideig az "aktív" állapotban marad. 


### <a name="use-a-starttask-to-install-mpi"></a>MPI telepítse a StartTask használatával
MPI-alkalmazások futtatásához és a egy többpéldányos tevékenység, először egy MPI-implementáció (MS-MPI vagy Intel MPI-t, a példában) telepítéséhez a készlet számítási csomópontjain. Ez egy jó ideje, hogy az egy [StartTask][net_starttask], amely végrehajtja, amikor egy csomópont csatlakozik a készlethez, vagy újraindult. Ez a kódrészlet létrehoz egy StartTask, amely meghatározza az MS-MPI telepítőcsomagját, mint egy [erőforrásfájl][net_resourcefile]. A kezdő tevékenység parancssorát a csomópontra az erőforrás-fájl letöltése után. Ebben az esetben a parancssorból végzi MS-MPI felügyelet nélküli telepítéséhez.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Távoli közvetlen memória-hozzáféréses (RDMA)
Ha egy [RDMA-kompatibilis mérete](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a9-es a Batch-készletben a számítási csomópontok, például az MPI-alkalmazás kihasználhatja az Azure nagy teljesítményű, kis késleltetésű távoli közvetlen memória-hozzáféréses (RDMA) hálózati.

Keresse meg a "RDMA-kompatibilis" a következő cikkekben megadott méret:

* **CloudServiceConfiguration** készletek

  * [A Cloud Services méretek](../cloud-services/cloud-services-sizes-specs.md) (csak Windows)
* **VirtualMachineConfiguration** készletek

  * [Az Azure-beli virtuális gépek méretei](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Az Azure-beli virtuális gépek méretei](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> RDMA előnyeit [Linux számítási csomópontok](batch-linux-nodes.md), kell használnia **Intel MPI** a csomópontokon. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Hozzon létre egy többpéldányos tevékenység a Batch .NET használatával
Most, hogy a készlet követelményeket és az MPI-csomag telepítése már szó, hozzuk létre a többpéldányos tevékenység. Ebben a kódrészletben hozunk létre egy standard [CloudTask][net_task], majd konfigurálja a [MultiInstanceSettings] [ net_multiinstance_prop] tulajdonság. Ahogy korábban említettük, a többpéldányos tevékenység nincs egy egyedi feladat típusa, de egy standard szintű Batch task többpéldányos beállítások konfigurálva.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Elsődleges feladat és részfeladatok gyakran
A többpéldányos beállítások egy feladat létrehozásakor meg kell adnia a száma, amelyek a feladat végrehajtásához. A feladat egy feladat elküldésekor a Batch szolgáltatás létrehoz egy **elsődleges** feladat, és elegendő **altevékenység** , amelyek együtt megfelelnek a megadott csomópontok számát.

Ezek a feladatok vannak hozzárendelve a tartomány 0 és az egész azonosító *numberOfInstances* – 1. A feladat-azonosító: 0 elsődleges feladata, és minden egyéb azonosítói a következők altevékenység. Például ha egy feladat a következő többpéldányos beállításokat hoz létre, elsődleges feladata volna 0 azonosítót, és az alfeladatok rendelkeznie azonosítók 1 – 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Fő csomópont
Egy többpéldányos tevékenység elküldésekor a Batch szolgáltatás egyik számítási csomópontján jelöli ki a "master" csomópontra, és ütemezi a főcsomóponton hajthat végre elsődleges feladata. Altevékenységek ütemezett hajthat végre a csomópontok a többpéldányos tevékenység számára lefoglalt része.

## <a name="coordination-command"></a>Koordinációs parancs
A **koordináció parancs** az elsődleges által végrehajtott és altevékenység.

A tranzakciókoordináció-parancs meghívása blokkolja a--a Batch nem hajtható végre az alkalmazás parancsot addig, amíg a tranzakciókoordináció-parancs sikeresen visszatért az összes alfeladatok. A tranzakciókoordináció-parancsot kell ezért indítsa el a szükséges háttér szolgáltatások, győződjön meg arról, hogy használatra készen állnak, és zárja be. Ha például a koordináció parancs 7 a SMPD szolgáltatás elindul a csomóponton, MS-MPI verziójával megoldás majd kilép:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Figyeljük meg `start` a koordináció parancs. Ez azért szükséges, mert a `smpd.exe` alkalmazás végrehajtása után azonnal nem ad vissza. A használata nélkül a [start] [ cmd_start] parancsot, a koordináció parancs nem adna vissza, és ezért megakadályozza az alkalmazás a parancs futtatását.

## <a name="application-command"></a>Alkalmazás parancs
Az elsődleges feladat, és minden altevékenység végzett koordináció parancs végrehajtása, miután a többpéldányos tevékenység parancssorát elsődleges feladat *csak*. Erre a **alkalmazás parancs** csatornától való megkülönböztetés a tranzakciókoordináció-parancsot.

MS-MPI-alkalmazások, az alkalmazás-parancs segítségével hajtsa végre az MPI-kompatibilis alkalmazás `mpiexec.exe`. Például a következő 7-es verzió MS-MPI használatával a megoldás egy alkalmazás parancsot:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Mivel az MS-MPI `mpiexec.exe` használja a `CCP_NODES` alapértelmezés szerint változó (lásd: [környezeti változók](#environment-variables)) a példa alkalmazás parancssor fenti nem tartalmazza azt.
>
>

## <a name="environment-variables"></a>Környezeti változók
A Batch hoz létre több [környezeti változók] [ msdn_env_var] többpéldányos tevékenységek a számítási csomópontokon egy többpéldányos tevékenység számára lefoglalt jellemző. A koordináció és alkalmazás parancssorok ezeket a környezeti változókat is lehet hivatkozni, hiszen használhatja a parancsfájlokat és a programok, hajtsa végre.

Az alábbi környezeti változókat a többpéldányos tevékenységek a Batch szolgáltatás használatra jönnek létre:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Ezek a részletek és a többi Batch számítási csomópont környezeti változói, beleértve azok tartalmát, és látható-e, lásd: [számítási csomópont környezeti változói][msdn_env_var].

> [!TIP]
> A Batch Linux MPI-kódmintát tartalmaz egy példát, hogyan több ezeket a környezeti változókat is használható. A [tranzakciókoordináció-cmd] [ coord_cmd_example] Bash-szkript letölti általános alkalmazást, és bemeneti fájlok az Azure Storage-ból, lehetővé teszi, hogy a fő csomópont egy hálózati fájlrendszer (NFS) megosztást, és konfigurálja a többi csomópont NFS-ügyfélként a többpéldányos tevékenység számára.
>
>

## <a name="resource-files"></a>Erőforrásfájlok
Nincsenek Erőforrásfájlok többpéldányos tevékenységek figyelembe venni két készletnyi: **közös erőforrásfájlok** , amely *összes* feladatok letöltése (mind az elsődleges és részfeladatok gyakran), és a **Erőforrásfájlok** a többpéldányos tevékenység, amely megadott *csak az elsődleges* letöltések feladat.

Megadhat egy vagy több **közös erőforrásfájlok** a többpéldányos tevékenység beállításaiban. A rendszer letölti a közös erőforrás fájlokat [Azure Storage](../storage/common/storage-introduction.md) az egyes csomópontok **feladat megosztott könyvtár** az elsődleges és minden altevékenység. Hozzáférhet a feladat megosztott könyvtárat az alkalmazás- és koordinációs parancssor használatával a `AZ_BATCH_TASK_SHARED_DIR` környezeti változót. A `AZ_BATCH_TASK_SHARED_DIR` elérési útja megegyezik a többpéldányos tevékenység lefoglalva minden csomóponton, így egyetlen koordináció parancsot az elsődleges és minden altevékenység között megoszthatók. A Batch nem "könyvtárban" távoli hozzáférés értelemben, de egy csatlakoztatási szeretné használni, vagy a környezeti változók. a tipp a korábban említett pont megosztásához.

Az erőforrásfájlokat, amelyek akkor adja meg a többpéldányos tevékenység magát a rendszer letölti a tevékenység munkakönyvtárának, `AZ_BATCH_TASK_WORKING_DIR`, alapértelmezés szerint. Ahogy már említettük, közös Erőforrásfájlok szakembereket csak az elsődleges tevékenység letölt a többpéldányos tevékenység magát a megadott erőforrás-fájlok.

> [!IMPORTANT]
> Mindig használja a környezeti változók `AZ_BATCH_TASK_SHARED_DIR` és `AZ_BATCH_TASK_WORKING_DIR` , tekintse meg ezeket a könyvtárakat a a parancssorában. Ne próbálja meg manuálisan hozza létre az elérési utakat.
>
>

## <a name="task-lifetime"></a>A feladat élettartama
Az elsődleges feladat élettartama azt szabályozza, hogy a teljes többpéldányos tevékenység élettartama. Ha az elsődleges kilép, minden altevékenységhez megszűnik. Az elsődleges kilépési kódja a kilépési kód, a feladat, és ezért azt határozza meg a sikeres vagy sikertelen a feladat újrapróbálkozási célokra.

Ha az alfeladatok bármelyike sikertelen, nem nulla értékű visszatérési kód kilép például a teljes többpéldányos tevékenység sikertelen lesz. A többpéldányos tevékenység ezután leállt, és újra próbálkozik, akár az újrapróbálkozások számát.

Ha töröl egy többpéldányos tevékenység, az elsődleges és minden altevékenység is törli a Batch szolgáltatás által. Összes alfeladat könyvtárak, és a fájlokat törli a számítási csomópontokra, csak egy szabványos feladat lehet.

[TaskConstraints] [ net_taskconstraints] egy többpéldányos tevékenység, mint például a [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime] [ net_taskconstraint_maxwallclock], és [RetentionTime] [ net_taskconstraint_retention] tulajdonságai között, egy szabványos tevékenység, és az elsődleges és minden altevékenység összes régió megfelel. Azonban ha módosítja a [RetentionTime] [ net_taskconstraint_retention] a többpéldányos tevékenység hozzáadása feladathoz, a módosítás után a tulajdonság csak az elsődleges feladat lesz alkalmazva. Minden altevékenységhez továbbra is használhatja az eredeti [RetentionTime][net_taskconstraint_retention].

Egy számítási csomóponton legutóbbi tevékenységek listájának alfeladat azonosítója jeleníti meg, ha a közelmúltbeli tevékenység egy többpéldányos tevékenység része volt.

## <a name="obtain-information-about-subtasks"></a>Altevékenységek kapcsolatos információ
A Batch .NET-kódtár használatával altevékenység információk beszerzéséhez hívja meg a [CloudTask.ListSubtasks] [ net_task_listsubtasks] metódust. Ez a módszer minden altevékenység a, és a számítási csomóponton, amely a feladatok kapcsolatos információkat ad vissza. Az ezt az információt megadhatja, hogy minden egyes alfeladat gyökérkönyvtár, a készlet azonosítójával, a jelenlegi állapotában, kilépési kód és további. Ezeket az információkat használhatja együtt a [PoolOperations.GetNodeFile] [ poolops_getnodefile] az alfeladat fájlok beszerzéséhez. Vegye figyelembe, hogy ez a metódus nem ad vissza információkat az elsődleges feladat (azonosító: 0).

> [!NOTE]
> Ha másként nincs jelezve, a Batch .NET-metódusokat, amelyek a művelethez a többpéldányos [CloudTask] [ net_task] magát a alkalmazni *csak* elsődleges feladathoz. Például, ha meghívja a [CloudTask.ListNodeFiles] [ net_task_listnodefiles] egy többpéldányos tevékenység metódus visszaadott csak az elsődleges tevékenység fájlokat.
>
>

A következő kódrészletet bemutatja, hogyan alfeladat információkat, valamint a fájl tartalmának kérhet a csomópontok, amelyen végrehajtották.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Kódminta
A [MultiInstanceTasks] [ github_mpi] kód minta a Githubon bemutatja, hogyan futtathat egy többpéldányos tevékenység használatával egy [MS-MPI] [ msmpi_msdn] alkalmazás a A Batch számítási csomópontokon. Kövesse a [előkészítési](#preparation) és [végrehajtási](#execution) a minta futtatásához.

### <a name="preparation"></a>Előkészítés
1. Az első két lépésekkel [fordítása és a egy egyszerű MS-MPI-program futtatása][msmpi_howto]. Ez megfelel a következő lépés az előfeltételek.
2. Hozhat létre egy *kiadási* verzióját a [MPIHelloWorld] [ helloworld_proj] MPI mintaprogram. Ez a program a többpéldányos tevékenység által a számítási csomópontokon fog futni.
3. Hozzon létre egy zip fájlt tartalmazó `MPIHelloWorld.exe` (mely Ön készített a 2. lépés), és `MSMpiSetup.exe` (amely letöltött 1. lépés). A zip-fájlt, a következő lépésben egy alkalmazáscsomagot, feltölthet.
4. Használja a [az Azure portal] [ portal] , hozzon létre egy Batch [alkalmazás](batch-application-packages.md) "MPIHelloWorld" néven, és adja meg a "1.0", mint az előző lépésben létrehozott zip-fájlt a virtuálisalkalmazás-csomag. Lásd: [töltheti fel és kezelheti az alkalmazások](batch-application-packages.md#upload-and-manage-applications) további információt.

> [!TIP]
> Hozhat létre egy *kiadási* verziója `MPIHelloWorld.exe` , hogy nem kell semmilyen további függőségeket tartalmaznak (például `msvcp140d.dll` vagy `vcruntime140d.dll`) az alkalmazáscsomagban.
>
>

### <a name="execution"></a>Futtatási
1. Töltse le a [azure-batch-samples] [ github_samples_zip] a Githubról.
2. Nyissa meg a MultiInstanceTasks **megoldás** Visual Studio 2017-ben. A `MultiInstanceTasks.sln` megoldás a fájl:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Adja meg a Batch- és tárolási fiók hitelesítő adatait a `AccountSettings.settings` a a **Microsoft.Azure.Batch.Samples.Common** projekt.
4. **Létrehozhatja és futtathatja** MultiInstanceTasks megoldás hajtsa végre a MPI mintaalkalmazás a Batch-készlet számítási csomópontjain.
5. *Nem kötelező*: Használja a [az Azure portal] [ portal] vagy [Batch Explorer] [ batch_labs] a minta-készlet, feladatok és tevékenységek vizsgálata ("MultiInstanceSamplePool"," MultiInstanceSampleJob","MultiInstanceSampleTask") az erőforrások törlése előtt.

> [!TIP]
> Letöltheti a [Visual Studio Community] [ visual_studio] ingyenesen, ha nem rendelkezik a Visual Studio.
>
>

A kimeneti `MultiInstanceTasks.exe` a következőhöz hasonló:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>További lépések
* A cikk ismerteti a Microsoft HPC és az Azure Batch csapatának blogja [MPI támogatja a Linux az Azure Batch][blog_mpi_linux], és használatával kapcsolatos tartalmaz [OpenFOAM] [ openfoam] a Batch használatával. Python-Kódminták a annak a [OpenFOAM példa a Githubon][github_mpi].
* Ismerje meg, hogyan [Linuxos számítási csomópontok készleteinek létrehozása](batch-linux-nodes.md) használható az Azure Batch MPI-megoldások.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: https://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: https://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Többpéldányos áttekintése"
