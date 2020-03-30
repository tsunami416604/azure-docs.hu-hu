---
title: MPI-alkalmazások futtatásához többpéldányos feladatok használata - Azure Batch | Microsoft dokumentumok
description: Ismerje meg, hogyan hajthatja végre a Message Passing Interface (MPI) alkalmazások at a többpéldányos feladattípus használatával az Azure Batch.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 03/13/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1896fea3c401299b4f77235ab3c02d85708b7041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023667"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Mpi-alkalmazások futtatása kötegelt üzenetátadási felület (MPI) műveletek hez többpéldányos feladatok használatával

A többpéldányos feladatok lehetővé teszik egy Azure Batch-feladat egyidejű futtatását több számítási csomóponton. Ezek a feladatok nagy teljesítményű számítási forgatókönyveket tesznek lehetővé, például az MPI-alkalmazásokat a Batch-ben. Ebből a cikkből megtudhatja, hogyan hajthat végre többpéldányos feladatokat a [Batch .NET][api_net] függvénytár használatával.

> [!NOTE]
> Míg a jelen cikkben szereplő példák a Batch .NET, MS-MPI és Windows számítási csomópontokra összpontosítanak, az itt tárgyalt többpéldányos feladatfogalmak más platformokra és technológiákra is vonatkoznak (például Python és Intel MPI Linux csomópontokon).
>
>

## <a name="multi-instance-task-overview"></a>Többpéldányos feladat – áttekintés
A Kötegben minden feladat általában egyetlen számítási csomóponton hajtva végre – több feladatot küld egy feladatnak, és a Batch szolgáltatás minden feladatot egy csomóponton hajt végre. Egy feladat **többpéldányos beállításainak**konfigurálásával azonban megkell mondania a Batchnek, hogy hozzon létre egy elsődleges feladatot és több alfeladatot, amelyeket ezután több csomóponton hajt végre.

![Többpéldányos feladat – áttekintés][1]

Amikor többpéldányos beállításokat rendelkező feladatot küld el egy feladatnak, a Batch többpéldányos feladatokra vonatkozó aneszteziáns lépéseket hajt végre:

1. A Batch szolgáltatás egy **elsődleges** és több **alfeladatot** hoz létre a többpéldányos beállítások alapján. A feladatok teljes száma (elsődleges plusz az összes altevékenység) megegyezik a többpéldányos beállításokban megadott **példányok** (számítási csomópontok) számával.
2. A Batch a számítási csomópontok egyikét jelöli **meg főkiszolgálóként,** és ütemezi a főkiszolgálón végrehajtandó elsődleges feladatot. Ütemezi az alfeladatok at végrehajtani a többpéldányos feladathoz lefoglalt számítási csomópontok fennmaradó részében, csomópontonként egy alfeladatot.
3. Az elsődleges és az összes altevékenység letölti a többpéldányos beállításokban megadott **közös erőforrásfájlokat.**
4. A közös erőforrásfájlok letöltése után az elsődleges és az altevékenységek végrehajtják a többpéldányos beállításokban megadott **koordinációs parancsot.** A koordinációs parancs általában a csomópontok előkészítésére szolgál a feladat végrehajtására. Ez magában foglalhatja a háttérszolgáltatások (például a [Microsoft MPI)][msmpi_msdn]indítását `smpd.exe`és annak ellenőrzését, hogy a csomópontok készen állnak-e a csomópontok közötti üzenetek feldolgozására.
5. Az elsődleges feladat végrehajtja az **alkalmazás parancsot** a főcsomóponton, *miután* a koordinációs parancsot az elsődleges és az összes altevékenység sikeresen befejezte. Az alkalmazásparancs maga a többpéldányos feladat parancssora, és csak az elsődleges feladat hajtja végre. [Ms-MPI][msmpi_msdn]alapú megoldásesetén itt hajtja végre az MPI-kompatibilis alkalmazást a használatával. `mpiexec.exe`

> [!NOTE]
> Bár funkcionálisan elkülönül, a "többpéldányos feladat" nem egyedi feladattípus, például a [StartTask][net_starttask] vagy a [TaskPreparationTask][net_jobprep]. A többpéldányos feladat egyszerűen egy szabványos Batch feladat[(CloudTask][net_task] a batch .NET-ben), amelynek többpéldányos beállításai konfigurálva vannak. Ebben a cikkben ezt **többpéldányos feladatként**hivatkozunk.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>A többpéldányos feladatokra vonatkozó követelmények
A többpéldányos feladatokhoz olyan készletre van szükség , amelyen **a csomópontok közötti kommunikáció engedélyezve van**, és az egyidejű **feladatvégrehajtás le van tiltva**. Az egyidejű feladatvégrehajtás letiltásához állítsa a [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool) tulajdonságot 1-re.

> [!NOTE]
> A köteg [korlátozza](batch-quota-limit.md#pool-size-limits) a csomópontok közötti kommunikációt engedélyező készlet méretét.


Ez a kódrészlet bemutatja, hogyan hozhat létre készletet többpéldányos feladatokhoz a Batch .NET függvénytár használatával.

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
> Ha megpróbál futtatni egy többpéldányos feladatot egy olyan készletben, ahol a kommunikáció csomópontlevan tiltva, vagy 1-nél nagyobb *maxTasksPerNode* értékkel, a feladat soha nem lesz ütemezve – határozatlan ideig "aktív" állapotban marad. 


### <a name="use-a-starttask-to-install-mpi"></a>MpI telepítése StartTask segítségével
Ha többpéldányos feladattal szeretne MPI-alkalmazásokat futtatni, először telepítenie kell egy MPI-implementációt (például MS-MPI vagy Intel MPI) a készlet számítási csomópontjaira. Ez egy jó alkalom, hogy egy [StartTask][net_starttask], amely végrehajtja, ha egy csomópont csatlakozik egy készlethez, vagy újraindul. Ez a kódrészlet létrehoz egy StartTask feladatot, amely az MS-MPI telepítőcsomagot [erőforrásfájlként][net_resourcefile]adja meg. A kezdő feladat parancssora az erőforrásfájl csomópontra való letöltése után történik. Ebben az esetben a parancssor az MS-MPI felügyelet nélküli telepítését hajtja végre.

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
Ha [rdma-kompatibilis méretet](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) választ, például Az A9-et a batch készlet számítási csomópontjaihoz, az MPI-alkalmazás kihasználhatja az Azure nagy teljesítményű, alacsony késleltetésű távoli közvetlen memória-hozzáférési (RDMA) hálózatát.

Keresse meg az "RDMA képes" értékben megadott méreteket a következő cikkekben:

* **CloudServiceKonfigurációs** készletek

  * [Méretek felhőszolgáltatásokhoz](../cloud-services/cloud-services-sizes-specs.md) (csak Windows)
* **VirtualMachineConfiguration-készletek**

  * [Virtuális gépek méretei az Azure-ban](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Virtuális gépek méretei az Azure-ban](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Az RDMA [linuxos számítási csomópontokon](batch-linux-nodes.md)való kihasználásához **az Intel MPI-t** kell használnia a csomópontokon. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Többpéldányos feladat létrehozása a Batch .NET segítségével
Most, hogy már lefedte a készlet követelmények és MPI-csomag telepítése, hozzuk létre a többpéldányos feladat. Ebben a kódrészletben létrehozunk egy szabványos [CloudTask-][net_task]et, majd konfiguráljuk a [MultiInstanceSettings][net_multiinstance_prop] tulajdonságát. Ahogy korábban említettük, a többpéldányos feladat nem különálló feladattípus, hanem egy többpéldányos beállításokkal konfigurált szabványos Batch feladat.

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

## <a name="primary-task-and-subtasks"></a>Elsődleges feladat és altevékenységek
Amikor egy feladat többpéldányos beállításait hozza létre, megadja a feladat végrehajtásához szükséges számítási csomópontok számát. Amikor elküldi a feladatot egy feladatnak, a Batch szolgáltatás létrehoz egy **elsődleges** feladatot, és elegendő **alfeladatot,** amelyek együttesen megfelelnek a megadott csomópontok számának.

Ezek a feladatok 0 és *numberOfInstances* - 1 tartományban vannak hozzárendelve egy egész azonosítóhoz. A 0 azonosítóval rendelkező feladat az elsődleges feladat, és az összes többi azonosító altevékenység. Ha például a következő többpéldányos beállításokat hozza létre egy feladathoz, az elsődleges feladat azonosítója 0 lesz, és az altevékenységek 1-től 9-ig azonosítóval rendelkeznek.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Fő csomópont
Amikor többpéldányos feladatot küld el, a Batch szolgáltatás a számítási csomópontok egyikét "fő" csomópontként jelöli meg, és ütemezi a főcsomóponton végrehajtandó elsődleges feladatot. Az alfeladatok végrehajtása a többpéldányos feladathoz lefoglalt csomópontok fennmaradó részén lesz ütemezve.

## <a name="coordination-command"></a>Koordinációs parancs
A **koordinációs parancsot** az elsődleges és az alfeladatok is végrehajtják.

A koordinációs parancs meghívása blokkolva van - a Batch nem hajtja végre az alkalmazásparancsot, amíg a koordinációs parancs nem adott vissza sikeresen az összes altevékenységhez. A koordinációs parancsnak ezért el kell indítania a szükséges háttérszolgáltatásokat, ellenőriznie kell, hogy azok készen állnak-e a használatra, majd ki kell lépnie. Az MS-MPI 7-es verzióját használó megoldás koordinációs parancsa például elindítja az SMPD szolgáltatást a csomóponton, majd kilép:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Vegye figyelembe, `start` hogy ezt a koordinációs parancsot használja. Erre azért van `smpd.exe` szükség, mert az alkalmazás nem ad vissza közvetlenül a végrehajtás után. A [start][cmd_start] parancs használata nélkül ez a koordinációs parancs nem tér ne vissza, ezért letiltaná az alkalmazásparancs futtatását.

## <a name="application-command"></a>Alkalmazás parancs
Miután az elsődleges feladat és az összes altevékenység befejezte a koordinációs parancs végrehajtását, a többpéldányos feladat parancssorát *csak*az elsődleges feladat hajtja végre. Ezt nevezzük **alkalmazásparancsnak,** hogy megkülönböztessük a koordinációs parancstól.

MS-MPI alkalmazások esetén az alkalmazásparanccsal hajtsa `mpiexec.exe`végre az MPI-kompatibilis alkalmazást a alkalmazással. Például, itt van egy alkalmazás parancs a megoldás segítségével MS-MPI 7-es verzió:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Mivel az MS-MPI alapértelmezés szerint `mpiexec.exe` használja a `CCP_NODES` változót (lásd: Környezeti [változók),](#environment-variables)a fenti példaalkalmazás parancssora kizárja azt.
>
>

## <a name="environment-variables"></a>Környezeti változók
A Batch többpéldányos feladatokhoz tartozó [környezeti változókat][msdn_env_var] hoz létre a többpéldányos feladathoz lefoglalt számítási csomópontokon. A koordinációs és alkalmazásparancssorok hivatkozhatnak ezekre a környezeti változókra, csakúgy, mint az általuk végrehajtott parancsfájlokra és programokra.

A batch szolgáltatás a következő környezeti változókat hozta létre többpéldányos feladatok hozásaként:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Ezekről és a többi Batch számítási csomópont környezeti változójának részletes adatait, beleértve azok tartalmát és láthatóságát, [lásd: Számítási csomópont környezeti változói.][msdn_env_var]

> [!TIP]
> A Batch Linux MPI-kód minta tartalmaz egy példát, hogy több ilyen környezeti változók használhatók. A [koordinációs-cmd][coord_cmd_example] Bash parancsfájl letölti a közös alkalmazás- és bemeneti fájlokat az Azure Storage-ból, engedélyezi a hálózati fájlrendszer (NFS) megosztását a fő csomóponton, és nfs-ügyfélként konfigurálja a többpéldányos feladathoz rendelt többi csomópontot.
>
>

## <a name="resource-files"></a>Erőforrásfájlok
A többpéldányos feladatokhoz két erőforrásfájlt kell figyelembe venni: az *összes* feladat letöltéséhez (elsődleges és altevékenységeket egyaránt) letöltött **közös erőforrásfájlokat,** valamint magát a többpéldányos tevékenységhez megadott **erőforrásfájlokat,** amelyeket *csak az elsődleges* feladat tölt le.

Egy vagy több **általános erőforrásfájlt** megadhat a feladat többpéldányos beállításaiban. Ezek a gyakori erőforrásfájlok az [Azure Storage-ból](../storage/common/storage-introduction.md) töltődnek le az egyes csomópontok **feladat megosztott könyvtárába** az elsődleges és az összes altevékenység. A feladat megosztott könyvtárát alkalmazás- és koordinációs `AZ_BATCH_TASK_SHARED_DIR` parancssorokból érheti el a környezeti változó használatával. Az `AZ_BATCH_TASK_SHARED_DIR` elérési út megegyezik a többpéldányos feladathoz rendelt minden csomóponton, így egyetlen koordinációs parancsot oszthat meg az elsődleges és az összes altevékenység között. A Batch nem "osztja meg" a könyvtárat távelérési értelemben, de csatlakoztatási vagy megosztási pontként is használhatja, ahogy azt a környezeti változók tippjében korábban említettük.

A többpéldányos feladathoz megadott erőforrásfájlok alapértelmezés szerint a program `AZ_BATCH_TASK_WORKING_DIR`letöltődik a feladat munkakönyvtárába. Mint említettük, a gyakori erőforrásfájlokkal ellentétben csak az elsődleges feladat tölti le a többpéldányos feladathoz megadott erőforrásfájlokat.

> [!IMPORTANT]
> Mindig használja a `AZ_BATCH_TASK_SHARED_DIR` környezeti `AZ_BATCH_TASK_WORKING_DIR` változókat, és olvassa el ezeket a könyvtárakat a parancssorokban. Ne próbálja meg manuálisan megépíteni az elérési utakat.
>
>

## <a name="task-lifetime"></a>Feladat élettartama
Az elsődleges feladat élettartama szabályozza a teljes többpéldányos feladat élettartamát. Az elsődleges kilépéskor az összes altevékenység leáll. Az elsődleges kilépési kódja a feladat kilépési kódja, és ezért a feladat sikeressének vagy sikertelensének meghatározására szolgál újrapróbálkozási célokra.

Ha az altevékenységek bármelyike sikertelen, a nulla nélküli visszatérési kóddal kilépve például a teljes többpéldányos feladat sikertelen lesz. A többpéldányos feladat ezután leáll, és újra próbálkozik, az újrapróbálkozási korlátig.

Többpéldányos feladat törlésekor a Batch szolgáltatás az elsődleges és az összes alfeladatot is törli. Az alfeladat-könyvtárak és azok fájljai ugyanúgy törlődnek a számítási csomópontokból, mint egy szabványos feladat esetében.

A többpéldányos tevékenységek, például a [MaxTaskRetryCount][net_taskconstraint_maxretry], a [MaxWallClockTime][net_taskconstraint_maxwallclock]és [a RetentionTime][net_taskconstraint_retention] tulajdonságok [feladatkényszereit][net_taskconstraints] a rendszer egy szabványos feladathoz hasonlóan tiszteletben tartja, és az elsődleges és az összes altevékenységre vonatkozik. Ha azonban módosítja a [RetentionTime][net_taskconstraint_retention] tulajdonságot a többpéldányos feladat hozzáadása után, ez a módosítás csak az elsődleges feladatra lesz alkalmazva. Az összes altevékenység továbbra is az eredeti [Megőrzési időt][net_taskconstraint_retention]használja.

A számítási csomópont legutóbbi feladatlistája egy altevékenység azonosítóját tükrözi, ha a legutóbbi feladat egy többpéldányos feladat része volt.

## <a name="obtain-information-about-subtasks"></a>Az altevékenységekkel kapcsolatos információk beszerzése
Ha az altevékenységekről a Batch .NET kódtár használatával szeretne információt szerezni, hívja meg a [CloudTask.ListSubtasks metódust.][net_task_listsubtasks] Ez a módszer az összes altevékenységadatait, valamint a feladatokat elvégzett számítási csomópont adatait adja vissza. Ebből az információból meghatározhatja az egyes altevékenységek gyökérkönyvtárát, a készletazonosítóját, aktuális állapotát, kilépési kódját stb. Ezt az információt a [PoolOperations.GetNodeFile][poolops_getnodefile] metódussal együtt használhatja az alfeladat fájljainak beszerzéséhez. Ne feledje, hogy ez a módszer nem ad vissza információt az elsődleges feladathoz (0-as azonosító).

> [!NOTE]
> Eltérő rendelkezés hiányában a többpéldányos [CloudTask-on][net_task] működő Batch .NET metódusok *csak* az elsődleges feladatra vonatkoznak. Ha például a [CloudTask.ListNodeFiles][net_task_listnodefiles] metódust többpéldányos feladaton hívja meg, csak az elsődleges feladat fájljait adja vissza a rendszer.
>
>

A következő kódrészlet bemutatja, hogyan szerezhetbe altevékenység-információkat, valamint kérheti a fájl tartalmát azoktól a csomópontoktól, amelyeken végrehajtották.

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
A [GitHubOn található MultiInstanceTasks][github_mpi] kódminta bemutatja, hogyan használhat többpéldányos feladatot egy [MS-MPI][msmpi_msdn] alkalmazás batch számítási csomópontokon történő futtatásához. Kövesse az [Előkészítés](#preparation) és [végrehajtás](#execution) lépéseket a minta futtatásához.

### <a name="preparation"></a>Előkészítés
1. Kövesse az első két lépést [a Hogyan kell lefordítani és futtatni egy egyszerű MS-MPI programot][msmpi_howto]. Ez megfelel a következő lépés előfeltételeinek.
2. Az [MPIHelloWorld][helloworld_proj] minta MPI-program *kiadási* verziójának létrehozása. Ez az a program, amelyet a többpéldányos feladat számítási csomópontokon fog futtatni.
3. Hozzon létre egy `MPIHelloWorld.exe` zip fájlt, amely `MSMpiSetup.exe` tartalmazza (amelyet a 2. lépés) és (amelyet letöltött 1. lépés). Ezt a zip-fájlt a következő lépésben alkalmazáscsomagként fogja feltölteni.
4. Az [Azure Portalon][portal] hozzon létre egy [Batch-alkalmazást](batch-application-packages.md) "MPIHelloWorld" nevű, és adja meg az előző lépésben létrehozott zip-fájlt az alkalmazáscsomag "1.0" verziójaként. További információt az [Alkalmazások feltöltése és kezelése](batch-application-packages.md#upload-and-manage-applications) című témakörben talál.

> [!TIP]
> Hozzon *Release* létre `MPIHelloWorld.exe` egy release verziót, hogy ne kelljen további `msvcp140d.dll` függőségeket (például vagy `vcruntime140d.dll`) felvennie az alkalmazáscsomagba.
>
>

### <a name="execution"></a>Futtatási
1. Töltse le az [azure-batch-mintákat][github_samples_zip] a GitHubról.
2. Nyissa meg a MultiInstanceTasks **megoldást** a Visual Studio 2019-ben. A `MultiInstanceTasks.sln` megoldásfájl a következő helyen található:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Adja meg a Batch és `AccountSettings.settings` storage fiók hitelesítő adatait a **Microsoft.Azure.Batch.Samples.Common** projektben.
4. A MultiInstanceTasks megoldás **létrehozása és futtatása** az MPI mintaalkalmazás kötegkészlet ben lévő számítási csomópontokon történő végrehajtásához.
5. *Nem kötelező*: Az [Azure Portalon][portal] vagy a [Batch Explorer használatával][batch_labs] vizsgálja meg a mintakészlet, feladat és feladat ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") az erőforrások törlése előtt.

> [!TIP]
> A [Visual Studio-közösség][visual_studio] ingyenesen letölthető, ha nem rendelkezik Visual Studio-val.
>
>

A `MultiInstanceTasks.exe` kimenet a következőhöz hasonló:

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
* A Microsoft HPC & Azure Batch Team blog ismerteti [a Linux MPI-támogatását az Azure Batch-en,][blog_mpi_linux]és információkat tartalmaz az [OpenFOAM][openfoam] batch használatával kapcsolatban. Az OpenFOAM példához python-kódmintákat találhat a [GitHubon.][github_mpi]
* Ismerje meg, hogyan [hozhat létre Linux-számítási csomópontok készleteit](batch-linux-nodes.md) az Azure Batch MPI-megoldásokban való használatra.

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

[1]: ./media/batch-mpi/batch_mpi_01.png "Többpéldányos áttekintés"
