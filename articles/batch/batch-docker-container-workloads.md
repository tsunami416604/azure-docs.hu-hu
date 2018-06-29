---
title: Azure Batch tároló munkaterhelések |} Microsoft Docs
description: Megtudhatja, hogyan tároló képek Azure Batch alkalmazások futtatását.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 06/04/2018
ms.author: danlep
ms.openlocfilehash: 8ef9d5a8e5212f6715769eecf4fde92a6d0b9d44
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060517"
---
# <a name="run-container-applications-on-azure-batch"></a>Azure Batch tároló alkalmazások futtatásához

Az Azure Batch lehetővé teszi futtatásához és méretezéséhez számítási feladatok Azure batch nagy számú. Kötegelt feladatok futtatható közvetlenül a virtuális gépek (csomópontok) a Batch-készlet, de is beállíthat a Batch-készlet Docker-kompatibilis tárolókban lévő feladatok futtatása a csomóponton. Ez a cikk bemutatja, hogyan hozzon létre, amelyek támogatják a tárolóban futó feladatot, és futtassa a tároló feladatok a készlet számítási csomópontok készlete. 

A tároló fogalmak és a Batch-készlet és a feladat létrehozása tisztában kell lennie. A Kódminták Batch .NET és Python SDK-k használhatók. Tároló-kompatibilis Batch-címkészletek létrehozása és a tároló feladatok futtatásához egyéb kötegelt SDK-k és eszközök, beleértve az Azure-portálon is használja.

## <a name="why-use-containers"></a>Tárolók miért érdemes használni?

Tárolók anélkül, hogy olyan környezetet és az alkalmazások futtatásához függőségek kezeléséhez kötegelt feladatok futtatása egyszerű módot nyújt. Tárolók telepítése alkalmazások könnyű, hordozható, ellenállni egységek több eltérő környezetben futtatható. Például összeállítása és tesztelése a helyi tárolója, majd a tároló Rendszerkép feltöltése az Azure rendszerben vagy máshol beállításjegyzékbeli. A tároló üzembe helyezési modellel biztosítja, hogy a futtatókörnyezet, az alkalmazás megfelelően mindig telepítve és konfigurálva van bárhol a alkalmazás üzemeltetéséhez. Tároló-alapú feladatok kötegben is kihasználhatják nem-tárolófigyelő feladatokhoz alkalmazáscsomagok és erőforrás-fájlok és a kimeneti fájlok felügyeleti funkcióit. 

## <a name="prerequisites"></a>Előfeltételek

* **SDK-verzió**: A kötegelt SDK-k támogatása tároló képek től a következő verziók:
    * Batch REST API verziója 2017-09-01.6.0
    * Batch .NET SDK verzió 8.0.0
    * Kötegelt Python SDK 4.0-s verziója
    * Kötegelt Java SDK 3.0-s verzió
    * Kötegelt Node.js SDK 3.0-s verzió

* **Fiókok**: az Azure-előfizetéséhez, a Batch-fiók, és opcionálisan egy Azure Storage-fiók létrehozásához szüksége.

* **A támogatott Virtuálisgép-lemezkép**: tárolók esetén támogatottak a virtuálisgép-konfiguráció létre készletek képek a következő szakaszban részletes, "támogatott virtuálisgép-lemezképeket." Ha egyéni lemezkép ad meg, tekintse meg a következő részben ismertetett szempontok és a követelmények [felügyelt egyéni lemezképet használ a virtuális gépek készlet létrehozása](batch-custom-images.md). 

### <a name="limitations"></a>Korlátozások

* Kötegelt csak a futó Linux készletek tárolók RDMA-támogatást nyújt.

## <a name="supported-virtual-machine-images"></a>Támogatott virtuálisgép-rendszerképek

Az alábbi támogatott a Windows vagy Linux képek készlet létrehozásához egy virtuális gép számítási csomópontjain tároló munkaterhelésekhez. Kötegelt kompatibilis piactéren elérhető rendszerkép kapcsolatos további információkért lásd: [virtuálisgép-rendszerképek listája](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Windows-lemezképek

A Windows tároló munkaterhelésekhez, a Batch-jelenleg támogatja a **Windows Server 2016 adatközpont tárolók** lemezkép az Azure piactéren. Windows csak Docker-tároló lemezképek támogatottak.

Egyéni lemezképek Docker futó Windows virtuális gépek is létrehozhat.

### <a name="linux-images"></a>Linux-lemezképek

Linux-tároló munkaterhelések esetén a kötegelt jelenleg a következő Linux-lemezképek, a Microsoft Azure Batch az Azure piactéren által közzétett támogatja:

* **Az Azure Batch tároló készletek centOS**

* **Az Azure Batch tároló készletek (az RDMA-illesztőprogramok) – centOS**

* **Az Azure Batch tároló készletek Ubuntu Server**

* **Ubuntu Server (RDMA-illesztőprogramok) Azure Batch tároló készletek**

Ezeket a lemezképeket használja az Azure Batch-készletek csak támogatottak. Szolgáltatás:

* Egy előre telepített [Moby](https://github.com/moby/moby) tároló futásidejű 

* Előre telepített NVIDIA GPU-illesztőprogramokat, egyszerűsítésére telepítési N-sorozat Azure virtuális gépeken

* Képek vagy anélkül előre telepített RDMA illesztőprogramok; Ezeket az illesztőprogramokat a készlet csomópontok elérik az Azure RDMA hálózati RDMA-kompatibilisek-e Virtuálisgép-méretek gazdagépfürtre engedélyezése  

A Linux terjesztéseket, amely kompatibilis a kötegelt egyikén Docker futó virtuális gépek is létrehozhat egyéni lemezképek. Ha a saját egyéni Linux képet, lásd az útmutatást [felügyelt egyéni lemezképet használ a virtuális gépek készlet létrehozása](batch-custom-images.md).

Telepítse az Docker-támogatást az egyéni lemezképet, [Docker Community Edition (CE)](https://www.docker.com/community-edition) vagy [Docker Enterprise Edition EE](https://www.docker.com/enterprise-edition).

További szempontokról Linux egyéni lemezkép használatával:

* Azure N-sorozat méretek GPU teljesítményének kihasználásához Ha egyéni lemezkép használatával, előre telepítenie NVIDIA illesztőprogramok. Is, akkor telepítenie kell a Docker motor eszközt az NVIDIA Feldolgozóegységekkel [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Az Azure RDMA hálózati eléréséhez, használja az RDMA-kompatibilisek-e Virtuálisgép-méretet. A szükséges RDMA-illesztőprogramok a CentOS HPC és Ubuntu képek támogatja a kötegelt vannak telepítve. MPI alkalmazásokat és szolgáltatásokat futtathatnak további konfigurálásra lehet szükség. Lásd: [használja az RDMA-kompatibilis vagy a GPU-t példány a Batch-készlet](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Batch-készlet tároló konfigurációja

Ahhoz, hogy a Batch-készlet tároló alkalmazásokat és szolgáltatásokat futtathatnak, meg kell adnia [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) a készlet beállításait [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objektum. (Ez a cikk hivatkozások a Batch .NET API-hivatkozás. Kapcsolódó beállítások a [kötegelt Python](/python/api/azure.batch) API.)

Létrehozhat egy tároló-kompatibilis készlet vagy anélkül prefetched tároló lemezképek, a következő példákban látható módon. A lekéréses (vagy előzetes betöltési) folyamat lehetővé teszi a Docker központ vagy az interneten egy másik tárolóban beállításjegyzék tároló képek előzetes betöltése. A legjobb teljesítmény érdekében használjon egy [Azure tároló beállításjegyzék](../container-registry/container-registry-intro.md) a Batch-fiók ugyanabban a régióban.

A tároló képek prefetching előnye, hogy feladatok első indításakor fut nem rendelkeznek letölteni a tároló kép vár. A tároló konfigurációjának lekéri a virtuális gépek tároló lemezképek, a készlet létrehozásakor. A készlet a futó feladatok majd hivatkozhat tároló képek listájának, és a tároló futtassa beállítások.


### <a name="pool-without-prefetched-container-images"></a>Készlet prefetched tároló képek nélkül

Egy tároló-kompatibilis készlet prefetched tároló képek nélküli konfigurálásához meghatározása `ContainerConfiguration` és `VirtualMachineConfiguration` objektumokat a következő Python példában látható módon. Ebben a példában a Ubuntu Server kiszolgálóját használja az Azure Batch tároló készletek kép a piactéren.


```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            container_configuration=container_conf,
            node_agent_sku_id='batch.node.ubuntu 16.04'),
        vm_size='STANDARD_D1_V2',
        target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>Előzetesen lehívott a tároló konfigurációjának lemezképei lapok

Előzetesen lehívott lapok tároló képek a készlet, adja hozzá a tároló lemezképek listáját (`container_image_names`, a Python) számára a `ContainerConfiguration`. 

A következő alapvető Python példa bemutatja, hogyan előzetesen lehívott lapok a szabványos Ubuntu tároló lemezkép [Docker Hub](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


A következő példa C# példa feltételezi, hogy szeretné-e előzetesen lehívott lapok egy TensorFlow lemezkép [Docker Hub](https://hub.docker.com). Ebben a példában a Virtuálisgép-gazda készlet csomópontjain futó start feladat tartalmazza. Előfordulhat, hogy olyan start feladatot futtat az állomáson, például olyan fájlkiszolgálót, mely elérhető a tárolókat az csatlakoztatni.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Előzetesen lehívott lapok képek a magánfelhő tároló beállításjegyzékből

Akkor is is előzetesen lehívott lapok tároló képek hitelesítik magukat a személyes tárolót beállításjegyzék-kiszolgálón. A következő példában a `ContainerConfiguration` és `VirtualMachineConfiguration` objektumok egy titkos TensorFlow lemezképet egy saját Azure-tárolót beállításjegyzékből előzetesen lehívott lapok. A Képhivatkozás megegyezik az előző példában látható módon.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```


## <a name="container-settings-for-the-task"></a>A feladat tároló beállításai

A számítási csomópontok tároló feladatok futtatásához meg kell adnia a tároló-specifikus beállításokat, például a beállítások, a használandó képek és a beállításjegyzék feladat.

Használja a `ContainerSettings` tulajdonság a feladat osztályok tárolót vonatkozó beállítások konfigurálása. Ezek a beállítások határozzák meg a [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) osztály.

Tároló-lemezképek, a feladatok futtatásakor a [felhő feladat](/dotnet/api/microsoft.azure.batch.cloudtask) és [manager feladat](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) tároló igénylése. Azonban a [feladat indítása](/dotnet/api/microsoft.azure.batch.starttask), [feladat előkészítése tevékenységet](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), és [feladat kiadása tevékenység](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nem igényelnek tároló beállításokat (Ez azt jelenti, hogy futtathatók legyenek egy tároló környezeten belül vagy közvetlenül a csomópont).

Az Azure Batch tároló feladat parancssorának egy könyvtárat a tárolóban, nagyon hasonló a kötegelt állít be egy normál (nem-tárolófigyelő) tevékenység környezet hajtja végre:

* Az alábbi könyvtárak rekurzív a `AZ_BATCH_NODE_ROOT_DIR` (az Azure Batch könyvtárak a csomóponton gyökér) vannak leképezve a tárolóba
* Minden tevékenység környezeti változók van leképezve a tárolóba
* Az alkalmazás munkakönyvtár értéke rendszeres feladatok, ugyanúgy, hogy használhassa a szolgáltatások, mint a alkalmazáscsomagok és erőforrás-fájlok

Kötegelt változik, az alapértelmezett munkakönyvtár a tárolóban, mert a feladat fut-e a tipikus tároló belépési pont eltérő helyre (például `c:\` Windows tárolóba, alapértelmezés szerint vagy `/` Linux rendszeren). Győződjön meg arról, hogy a feladat parancssori vagy tároló belépési pont határoz meg abszolút elérési utat, ha már nincs konfigurálva ily módon.

Az alábbi Python kódrészletben láthatja az Ubuntu tároló Docker Hub lekért futó alapvető parancssor. Futtatás tároló lehetőségek állnak rendelkezésére további argumentumokat a `docker create` parancsot, amely a feladat futtatása. Itt a `--rm` beállítással eltávolítja a tároló, a feladat befejezése után.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='ubuntu', 
    container_run_options='--rm')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/echo hello',
    container_settings=task_container_settings
)

```

Az alábbi C# példa bemutatja a felhő feladat alapvető tároló beállításai:

```csharp
// Simple container task command

string cmdLine = "c:\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>További lépések

* Lásd még: a [kötegelt hajógyárnak](https://github.com/Azure/batch-shipyard) eszközkészletét tároló munkaterhelések Azure Batch keresztül egyszerű telepítés [hajógyárnak receptet](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* A telepítéssel és használattal Docker CE Linux további információkért lásd: a [Docker](https://docs.docker.com/engine/installation/) dokumentációját.

* További információ az egyéni lemezképek használatával, lásd: [felügyelt egyéni lemezképet használ a virtuális gépek készlet létrehozása ](batch-custom-images.md).

* További információ a [Moby projekt](https://mobyproject.org/), tároló-alapú rendszerek létrehozása keretét.