---
title: Többpéldányos feladatok használatával történő futtatása MPI alkalmazások – Azure Batch |} Microsoft Docs
description: Ismerje meg, hogyan hajthat végre a többpéldányos feladattípust használó Azure Batch a Message Passing Interface (MPI) alkalmazások.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 5/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0fb5ea21c6403369cbcb60df58c0f70a57a61d4e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Többpéldányos feladatok használatával történő kötegelt Message Passing Interface (MPI) alkalmazások futtatása

Többpéldányos feladatok lehetővé teszik több számítási csomópont az Azure Batch feladat egyidejű futtatására. Ezek a feladatok lehetővé teszik a nagy teljesítményű számítástechnikai forgatókönyvekhez hasonlóan a Message Passing Interface (MPI) alkalmazások kötegben. Ebből a cikkből megtanulhatja a többpéldányos feladatok végrehajtásához a [Batch .NET] [ api_net] könyvtárban.

> [!NOTE]
> A jelen cikk példái a Batch .NET, MS-MPI, összpontosítson, és a számítási csomópontok Windows, a Microsofttól többpéldányos feladat fogalmak vonatkoznak más platformok és technológiák (a Python és a Linux-csomópont, például Intel MPI).
>
>

## <a name="multi-instance-task-overview"></a>Többpéldányos feladat áttekintése
A kötegelt, minden feladata a normál esetben – egyetlen számítási csomóponton végre egy feladat több feladat elküldését, és az a Batch szolgáltatás ütemezések minden egyes csomóponton végrehajtandó feladatot. A tevékenység konfigurálásával azonban **többpéldányos beállítások**, hozzon létre egy elsődleges feladat és több résztevékenység több csomóponton majd végrehajtott köteg biztosítják.

![Többpéldányos feladat áttekintése][1]

Amikor egy feladat többpéldányos beállításokkal egy feladathoz, a kötegelt többpéldányos feladatok egyedi több lépéseket végzi el:

1. A Batch szolgáltatás létrehoz egy **elsődleges** és több **résztevékenység** többpéldányos beállítások alapján. A feladatok (elsődleges és minden résztevékenység) teljes száma a számának egyeznie kell **példányok** (számítási csomópontok) a többpéldányos beállításokat ad meg.
2. Kötegelt jelöl ki, mint a számítási csomópontok közül a **fő**, és az ütemezések végrehajtandó fő elsődleges feladat. A számítási csomópontok lefoglalt a többpéldányos tevékenységhez, a csomópontonkénti egy részfeladatnál annak regisztrálása a többi végrehajtása résztevékenység ütemezés.
3. Az elsődleges kiszolgáló és az összes résztevékenység letölteni egy **közös erőforrásfájlok** a többpéldányos beállításokat ad meg.
4. A közös erőforrás után fájlok le vannak töltve, az elsődleges és résztevékenység hajtható végre a **koordinációs parancs** a többpéldányos beállításokat ad meg. A koordinációs parancs általában használt csomópontok előkészítése a feladat végrehajtása. Ilyen lehet például a háttér-szolgáltatás indítása (például [Microsoft MPI][msmpi_msdn]tartozó `smpd.exe`) és annak ellenőrzése, hogy a csomópontok készen áll a csomópontok közötti üzenetek.
5. Az elsődleges feladat végrehajtása a **alkalmazás parancs** a fő csomóponton *után* a tranzakciókoordináció-parancs sikeresen befejeződött az elsődleges és az összes résztevékenység. Az alkalmazás parancsot a parancssorban a többpéldányos feladat magát, és csak az elsődleges feladatot futtatja. Az egy [MS-MPI][msmpi_msdn]-alapú megoldás, ahol végrehajtani a a MPI-kompatibilis alkalmazások használata azt `mpiexec.exe`.

> [!NOTE]
> Habár funkcionálisan különböző, a "többpéldányos" feladata nem egy egyedi feladat típusát. például a [StartTask] [ net_starttask] vagy [JobPreparationTask][net_jobprep]. A többpéldányos feladata egyszerűen egy szabványos kötegelt feladat ([CloudTask] [ net_task] a Batch .NET) amelynek többpéldányos beállítások lettek konfigurálva. Ez a cikk azt tekintse meg ezt a **többpéldányos feladat**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Többpéldányos feladatok követelményei
Többpéldányos feladatok elvégzéséhez a készlet **engedélyezett csomópontok közötti kommunikáció**, és a **egyidejű feladat a végrehajtás letiltja**. Egyidejű feladat a végrehajtás letiltásához állítsa be a [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool#Microsoft_Azure_Batch_CloudPool_MaxTasksPerComputeNode) 1 tulajdonságot.

> [!NOTE]
> Kötegelt [korlátok](batch-quota-limit.md#other-limits) , amelyen a csomópontok közötti kommunikáció engedélyezve van a készlet mérete.


A kódrészletet bemutatja, hogyan többpéldányos feladatokhoz a Batch .NET könyvtár használata készlet létrehozása.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> Ha a többpéldányos feladat futtatásakor a készletbe, a fürtök csomóponton belüli kommunikációjához közötti kommunikáció le van tiltva, vagy az egy *maxTasksPerNode* 1-nél nagyobb értéket, a feladat soha nem ütemezett--határozatlan ideig az "active" állapotban marad. 
>
> Többpéldányos feladatokat hajthat végre 2015. December 14. után létrehozott készletek csomópontján.
>
>

### <a name="use-a-starttask-to-install-mpi"></a>MPI telepítése egy StartTask használatával
Többpéldányos feladatokkal MPI alkalmazások futtatásához először telepítendő (MS-MPI vagy Intel MPI, például) MPI megvalósítást a készlet számítási csomópontjain. Ez az egy időben használatára egy [StartTask][net_starttask], amely hajt végre, amikor a csomópont csatlakozik egy készletet, vagy újraindítják. A kódrészletet, amely meghatározza az MS-MPI telepítőcsomagját, mint egy StartTask hoz létre egy [erőforrásfájl][net_resourcefile]. A kezdő tevékenység Parancssor végrehajtása után az erőforrás-csomóponthoz le. Ebben az esetben a parancssor MS-MPI felügyelet nélküli telepítés hajt végre.

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

### <a name="remote-direct-memory-access-rdma"></a>Távoli közvetlen memória-hozzáférés (RDMA)
Ha úgy dönt, egy [RDMA-kompatibilis mérete](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) A9 a Batch-készlet számítási csomópontjainak, például a MPI alkalmazás előnyeit az Azure nagy teljesítményű, alacsony késésű távoli közvetlen memória-hozzáférés (RDMA) hálózati.

Keresse meg az "RDMA-kompatibilis" a következő cikkekben megadott méret:

* **CloudServiceConfiguration** készletek

  * [A Felhőszolgáltatások mérete](../cloud-services/cloud-services-sizes-specs.md) (csak Windows)
* **VirtualMachineConfiguration** készletek

  * [Az Azure virtuális gépek méretei](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Az Azure virtuális gépek méretei](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> RDMA előnyeit [Linux számítási csomópontok](batch-linux-nodes.md), kell használnia **Intel MPI** a csomóponton. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>A Batch .NET többpéldányos feladat létrehozása
Most, hogy azt már kezelt készlet követelményeinek és MPI csomag telepítése, hozzon létre a többpéldányos feladat. Az ezt a kódrészletet standard létrehozhatunk [CloudTask][net_task], majd konfigurálja a [MultiInstanceSettings] [ net_multiinstance_prop] tulajdonság. Amint azt korábban említettük, a többpéldányos feladat nincs a különböző feladattípust, de egy szabványos kötegelt feladat többpéldányos beállításokat.

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

## <a name="primary-task-and-subtasks"></a>Elsődleges feladatok és részfeladatok
Amikor létrehoz egy feladatot többpéldányos beállításait, adja meg, amelyek a tevékenység számítási csomópontok száma. Amikor a feladat egy feladathoz, a Batch szolgáltatás létrehoz egy **elsődleges** feladatot, és elég **résztevékenység** együtt megfelelő a megadott csomópontok száma.

Ezek a feladatok hozzárendelt azonosító egy egész számot 0 értékre közé *numberOfInstances* – 1. 0 azonosítójú feladat elsődleges feladata, és minden egyéb azonosítók résztevékenység. Például létrehozhat egy feladat a következő többpéldányos beállításai, ha az elsődleges feladat kellene 0 azonosítót, és a résztevékenység azonosítók 1 – 9 rendelkeznie.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Fő csomópont
Amikor a többpéldányos feladat, a Batch szolgáltatás a számítási csomópontok egyikét jelöli ki a "fő" csomópont, és az ütemezések a elsődleges feladat végrehajtása a fő csomóponton. A résztevékenység vannak ütemezve, a többi csomópontot a többpéldányos feladathoz hozzárendelt hajtható végre.

## <a name="coordination-command"></a>Koordinációs parancs
A **koordinációs parancs** futtatja az elsődleges és altevékenység.

A koordinációs parancs meghívása blokkolja--kötegelt nem hajtható végre: az alkalmazás parancsot mindaddig, amíg a tranzakciókoordináció-parancs sikeresen visszatért az összes résztevékenység. A koordinációs parancsot kell ezért szükséges háttér szolgáltatások indítása, győződjön meg arról, hogy azok készen áll a használatra, és zárja be. Például a koordinációs parancs 7 elindítja a SMPD szolgáltatást a csomóponton, MS-MPI verziójával megoldás majd kilép:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Vegye figyelembe a használatát `start` koordinációs parancsban. Ez azért szükséges, mert a `smpd.exe` végrehajtása után azonnal alkalmazás tér vissza. A nélkül a [start] [ cmd_start] parancs, a tranzakciókoordináció-parancs nem alakítanák vissza, és ezért megakadályozza az alkalmazás parancs futtatását.

## <a name="application-command"></a>Alkalmazás parancs
Az elsődleges feladat és összes résztevékenység végzett a tranzakciókoordináció-parancs végrehajtása, ha a többpéldányos feladat parancssori elsődleges feladat végrehajtása *csak*. Ez közvetlen telepítésnek a **alkalmazás parancs** segítségével különböztetheti meg egymástól a tranzakciókoordináció-utasításból.

MS-MPI-alkalmazások esetében az alkalmazás parancs segítségével hajtható végre a MPI-kompatibilis alkalmazás a `mpiexec.exe`. Például ez az alkalmazás parancs használatával a MS-MPI 7-es verzió megoldás:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Mivel az MS-MPI `mpiexec.exe` használja a `CCP_NODES` változó alapértelmezés szerint (lásd: [környezeti változók](#environment-variables)) a példa alkalmazás parancssori műveletet a fenti nem tartalmazza azt.
>
>

## <a name="environment-variables"></a>Környezeti változók
Kötegelt hoz létre több [környezeti változók] [ msdn_env_var] adott többpéldányos feladatok egy többpéldányos feladathoz hozzárendelt számítási csomóponton. A koordinációs és alkalmazás parancssorokat hivatkozhat ezen környezeti változókkal, a parancsfájlok és az általuk programok is.

A következő környezeti változók többpéldányos feladatok a Batch szolgáltatás használatra jönnek létre:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Ezek a részletes és az egyéb kötegelt számítási csomópont környezeti változókat, beleértve azok tartalmát, és látható, a következő témakörben: [számítási csomópont környezeti változók][msdn_env_var].

> [!TIP]
> A kötegelt Linux MPI kódminta egy példával hogyan ezen környezeti változókkal számos használható-e. A [tranzakciókoordináció-cmd] [ coord_cmd_example] Bash parancsfájlok letölti közös alkalmazást, és a bemeneti fájlok az Azure Storage, lehetővé teszi, hogy a hálózati fájlrendszer (NFS) megosztott helyén a fő csomópont, és konfigurálja a többpéldányos feladathoz hozzárendelt NFS-ügyfelek, a többi csomópont.
>
>

## <a name="resource-files"></a>Erőforrásfájlok
Két csoportját kell figyelembe venni a többpéldányos feladatokhoz Erőforrásfájlok: **közös erőforrásfájlok** , amely *összes* feladatok letöltése (mind az elsődleges és részfeladatok), és a **erőforrásfájlok** a többpéldányos feladat, amely megadott *csak az elsődleges* letöltések feladat.

Megadhat egy vagy több **közös erőforrásfájlok** feladat többpéldányos beállításai. A közös erőforrás fájlokat szeretne letölteni a [Azure Storage](../storage/common/storage-introduction.md) az egyes csomópontok **feladat megosztott könyvtár** az elsődleges és az összes résztevékenység. Akkor érhető el a feladat megosztott könyvtár az alkalmazás- és koordinációs parancssorokat a `AZ_BATCH_TASK_SHARED_DIR` környezeti változó. A `AZ_BATCH_TASK_SHARED_DIR` elérési út azonos a többpéldányos feladathoz hozzárendelt minden csomóponton, így egyetlen koordinációs parancsot az elsődleges és minden résztevékenység közötti megoszthatja. Kötegelt nem "könyvtárban" távelérés értelemben, de csatlakoztatási használni, vagy pont, ahogy azt korábban említettük, a környezeti változók tipp a korábbi megosztásához.

Erőforrás-fájlok adja meg, hogy maga a többpéldányos feladat letöltődnek a feladatütemezési munkakönyvtár, `AZ_BATCH_TASK_WORKING_DIR`, alapértelmezés szerint. Ahogy azt korábban említettük, közös erőforrásfájlokat, ellentétben a csak az elsődleges feladat maga a többpéldányos feladat a megadott erőforrás-fájlokat tölti le.

> [!IMPORTANT]
> A környezeti változók mindig használjon `AZ_BATCH_TASK_SHARED_DIR` és `AZ_BATCH_TASK_WORKING_DIR` ezeket a könyvtárakat a parancssor a hivatkozik. Ne kísérelje meg az elérési utak manuálisan összeállításához.
>
>

## <a name="task-lifetime"></a>Feladatütemezés élettartama
Az elsődleges feladatütemezés élettartama határozza meg a teljes többpéldányos feladatütemezés élettartama. Amikor az elsődleges kilép, az összes a résztevékenység leállítása van. A kilépési kód elsősorban a kilépési kódot, a feladat, és ezért azt határozza meg, a sikeres vagy sikertelen volt-e a feladatot az újrapróbálkozási céljából.

Ha bármelyik a résztevékenység sikertelen, kilép egy nem nulla visszatérési kóddal, például a teljes többpéldányos feladat sikertelen. A többpéldányos feladat majd lezárva, és újra, akár az Újrapróbálkozási korlát.

A többpéldányos tevékenység törlésekor az elsődleges és az összes résztevékenység is törli a Batch szolgáltatás. Az összes végző részfeladat a könyvtárak és a fájlok törlődnek a számítási csomópontok, ugyanúgy, mint a szabványos feladat.

[TaskConstraints] [ net_taskconstraints] többpéldányos feladathoz, mint például a [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime] [ net_taskconstraint_maxwallclock], és [RetentionTime] [ net_taskconstraint_retention] tulajdonságait, amelyek figyelembe véve, egy szabványos tevékenység, és az elsődleges és az összes résztevékenység vonatkozik. Azonban ha módosítja a [RetentionTime] [ net_taskconstraint_retention] a többpéldányos feladat hozzáadása a feladathoz, a módosítás után a tulajdonság csak az elsődleges feladat lesz alkalmazva. Az összes a résztevékenység továbbra is az eredeti [RetentionTime][net_taskconstraint_retention].

A számítási csomópont legutóbbi tevékenységek listájának tükrözi egy részfeladatnál annak regisztrálása azonosítóját, ha a legutóbbi feladat többpéldányos feladat része volt.

## <a name="obtain-information-about-subtasks"></a>Résztevékenység kapcsolatos információkhoz
A Batch .NET könyvtár használatával résztevékenység információk beszerzéséhez hívja meg a [CloudTask.ListSubtasks] [ net_task_listsubtasks] metódust. Ez a módszer minden résztevékenység információkat, és a számítási csomópont, amely a feladatok végrehajtása adatait adja vissza. Ezekből az adatokból meghatározhatja, hogy minden részfeladatnál annak regisztrálása a gyökérkönyvtár, a készlet azonosítóját, a jelenlegi állapotában, kilépési kód, és több. Ez az információ együtt használható a [PoolOperations.GetNodeFile] [ poolops_getnodefile] a részfeladatnál annak regisztrálása a fájlok beszerzéséhez. Vegye figyelembe, hogy ez a metódus nem ad vissza adatokat az elsődleges feladat (azonosító: 0).

> [!NOTE]
> Hacsak másként nem jelezzük, Batch .NET-metódusokat, amelyek működnek a többpéldányos [CloudTask] [ net_task] maga alkalmazása *csak* az elsődleges tevékenységhez. Például, ha meghívja a [CloudTask.ListNodeFiles] [ net_task_listnodefiles] metódus a többpéldányos tevékenységen, csak az elsődleges feladat fájlok vannak adott vissza.
>
>

A következő kódrészletet bemutatja, hogyan részfeladatnál annak regisztrálása információkhoz, valamint a fájl tartalmának kér a csomópontokat, amelyeken végre.

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
A [MultiInstanceTasks] [ github_mpi] kód mintát a Githubon bemutatja, hogyan használja a többpéldányos feladat futtatásához egy [MS-MPI] [ msmpi_msdn] alkalmazás Batch számítási csomópontjain. Kövesse a [előkészítése](#preparation) és [végrehajtási](#execution) a minta futtatásához.

### <a name="preparation"></a>Előkészítése
1. Első két kövesse a [fordítása, és egyszerű MS-MPI program futtatása][msmpi_howto]. Ez megfelel a következő lépés az előfeltételeket.
2. Build egy *kiadás* verzióját a [MPIHelloWorld] [ helloworld_proj] minta MPI program. Ez az a program, amely fog futni a számítási csomópontok a többpéldányos feladat által.
3. Hozzon létre egy zip fájlt tartalmazó `MPIHelloWorld.exe` (melyet beépített 2. lépés) és `MSMpiSetup.exe` (amely letöltött 1. lépés). A zip-fájl a következő lépésben egy alkalmazás csomag fogja feltölteni.
4. Használja a [Azure-portálon] [ portal] létrehozza [alkalmazás](batch-application-packages.md) "MPIHelloWorld" nevezik, és adja meg az "1.0" alkalmazáscsomag mint az előző lépésben létrehozott zip fájlt. Lásd: [alkalmazások kezelését és feltöltését](batch-application-packages.md#upload-and-manage-applications) további információt.

> [!TIP]
> Build egy *kiadás* verziójának `MPIHelloWorld.exe` , hogy nem kell további függőségeit tartalmazza (például `msvcp140d.dll` vagy `vcruntime140d.dll`) a alkalmazáscsomagban.
>
>

### <a name="execution"></a>Végrehajtás
1. Töltse le a [azure-köteg-minták] [ github_samples_zip] a Githubról.
2. Nyissa meg a MultiInstanceTasks **megoldás** a Visual Studio 2015-öt vagy újabb. A `MultiInstanceTasks.sln` megoldásfájlt található:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Adja meg a kötegelt és a tároló hitelesítő adatait a `AccountSettings.settings` a a **Microsoft.Azure.Batch.Samples.Common** projekt.
4. **Létrehozása és futtatása** mintaalkalmazást a MultiInstanceTasks megoldás a MPI végrehajtásához a Batch-készlet számítási csomópontjai.
5. *Nem kötelező*: használja a [Azure-portálon] [ portal] vagy [BatchLabs] [ batch_labs] megvizsgálhatja a minta-készlet, a feladat és a feladat (" MultiInstanceSamplePool","MultiInstanceSampleJob","MultiInstanceSampleTask") az erőforrások törlése előtt.

> [!TIP]
> Letöltheti a [Visual Studio Community] [ visual_studio] szabad, ha nem rendelkezik a Visual Studio.
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
* A cikk ismerteti a Microsoft HPC & Azure Batch csapat blogja [MPI támogatja az Azure Batch Linux][blog_mpi_linux], és használatával kapcsolatos tartalmaz [OpenFOAM] [ openfoam] kötegelt. A Python-Kódminták megtalálhatja a [OpenFOAM példa a Githubon][github_mpi].
* Megtudhatja, hogyan [létrehozása Linux számítási csomópontok készleteinek](batch-linux-nodes.md) használható az Azure Batch MPI megoldások.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchLabs/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
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
