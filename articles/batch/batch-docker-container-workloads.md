---
title: Tárolókhoz kapcsolódó számítási feladatok Azure Batch |} A Microsoft Docs
description: Megtudhatja, hogyan tárolórendszerképek az Azure Batch alkalmazások futtatását.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 10/24/2018
ms.author: danlep
ms.openlocfilehash: 458b0f7bbf581c7f2490a8122f351dac612b4ff0
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155620"
---
# <a name="run-container-applications-on-azure-batch"></a>Az Azure Batch tárolóalkalmazások futtatásához

Az Azure Batch lehetővé teszi futtathat és méretezhet nagyszámú kötegelt számítási feladatok Azure-ban. Batch-feladatok futtathat közvetlenül a virtuális gépek (csomópontok) Batch-készlet, de is állíthat be a Batch-készlet feladatok futtatása a Docker-kompatibilis tárolók a csomópontokon. Ez a cikk bemutatja, hogyan hozhat létre, amely támogatja a futó tároló feladatokat, és futtassa a tároló feladatok a készletben lévő számítási csomópontok készletét. 

A tároló fogalmakat és hogyan hozhat létre egy Batch-készlet és a feladat ismernie kell. Hitelesítésikód-példák a Batch .NET-es és a Python SDK-k használata. Tároló-kompatibilis Batch-készletek létrehozása és a tároló feladatok egyéb Batch SDK-k és eszközök, többek között az Azure Portalon is használhatja.

## <a name="why-use-containers"></a>Miért érdemes használni a tárolók?

Tárolók használatával környezet és alkalmazások futtatását a függőségek kezelése nélkül futtathat a Batch-feladatok egyszerű módot kínál. Tárolók üzembe alkalmazások könnyű, hordozható, önellátó egységek több eltérő környezetben futtatható. Például készíthet és a egy tároló helyi tesztelése, majd töltse fel a tárolórendszerképet a regisztrációs adatbázisba, az Azure-ban vagy máshol. A tároló üzembe helyezési modell biztosítja, hogy a futtatókörnyezet az alkalmazás mindig megfelelően telepítve és konfigurálva van az alkalmazás üzemeltetése bárhol. Tárolóalapú feladatok Batch szolgáltatásban is kihasználhatják az működő feladatokat, beleértve az alkalmazáscsomagokat és erőforrásfájlok és a kimeneti fájlokat felügyeleti funkcióit. 

## <a name="prerequisites"></a>Előfeltételek

* **SDK-verziókra**: A Batch SDK-k támogatása tárolórendszerképek kezdődően a következő verziókat:
    * Batch – REST API-verzió 2017-09-01.6.0
    * A Batch .NET SDK-val 8.0.0 verzió
    * A Batch Python SDK 4.0-s verziója
    * Batch Java SDK 3.0-s verzió
    * Batch Node.js SDK 3.0-s verzió

* **Fiókok**: az Azure-előfizetésében, hozzon létre egy Batch-fiókot, és opcionálisan egy Azure Storage-fiók szükséges.

* **Támogatott Virtuálisgép-rendszerkép**: tárolók csak a virtuálisgép-konfigurációval létrehozott készletek esetén használhatók a következő szakaszban részletes rendszerképekből "támogatott virtuálisgép-rendszerképek." Ha megad egy egyéni rendszerképet, tekintse meg a következő szakaszban a szempontok és a követelményeket a [felügyelt egyéni lemezképek használatával hozzon létre egy virtuálisgép-készletek](batch-custom-images.md). 

### <a name="limitations"></a>Korlátozások

* Batch RDMA-támogatást biztosít csak Linux-készletek futó tárolók

* A Windows-tárolókhoz kapcsolódó számítási feladatot azt javasoljuk, hogy a Többmagos virtuális gép méretét a készlet kiválasztása

## <a name="supported-virtual-machine-images"></a>Támogatott virtuálisgép-lemezképek

Az alábbiak egyikét használja a Windows támogatott, vagy virtuálisgép-készlet létrehozása Linux-rendszerképeket számítási csomópontok tárolókhoz kapcsolódó számítási feladatok számára. Marketplace-rendszerképek, amelyek kompatibilisek a Batch-Csel kapcsolatos további információkért lásd: [virtuálisgép-rendszerképek listája](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Windows-rendszerképek

Windows-tárolókhoz kapcsolódó számítási feladatok, a Batch jelenleg támogatja a **Windows Server 2016 Datacenter tárolókkal** rendszerképet az Azure piactéren. A Windows támogatott csak Docker-tárolórendszerképekhez.

Egyéni rendszerképek Docker futó Windows virtuális gépek is létrehozhat.

### <a name="linux-images"></a>Linux-rendszerképek

A Linux-tárolókhoz kapcsolódó számítási feladatok a Batch jelenleg a következő típusú Linux-rendszerképeket a Microsoft Azure Batch az Azure Marketplace-en közzétett támogatja:

* **Az Azure Batch-készletekben tároló centOS**

* **Az Azure Batch-készletekben tároló (az RDMA-illesztőprogramok) centOS**

* **Az Azure Batch-készletekben tároló Ubuntu Server**

* **Ubuntu Server (az RDMA-illesztőprogramok) az Azure Batch-készletekben tároló**

Ezek a lemezképek használata az Azure Batch-készletek csak támogatottak. Szolgáltatás:

* Egy előre telepített [Moby](https://github.com/moby/moby) tároló-futtatókörnyezet 

* Előre telepített NVIDIA GPU-illesztőprogramokat, N-sorozatú Azure virtuális gépeken a központi telepítés lehetséges egyszerűsítési módjai

* Képek vagy anélkül előre telepített RDMA drivers; illesztőprogramok engedélyezése készlet csomópontok elérik az Azure RDMA hálózati telepítésekor az RDMA-kompatibilis Virtuálisgép-méretek  

A Linux-disztribúció, amely kompatibilis a Batch-Csel egyikén Docker futó virtuális gépekről egyéni rendszerképeket is létrehozhat. Ha a saját egyéni Linux-rendszerképek megadását választja, tekintse meg a található [felügyelt egyéni lemezképek használatával hozzon létre egy virtuálisgép-készletek](batch-custom-images.md).

A Docker-támogatás, egyéni rendszerképen, telepítse [a Docker Community Edition (CE)](https://www.docker.com/community-edition) vagy [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Egy egyéni Linux-rendszerképek a további szempontok:

* Azure N-sorozat méretei a GPU-teljesítményét előnyeinek kihasználása, ha egyéni lemezkép alapján, előre NVIDIA illesztőprogram telepítése. Emellett szüksége NVIDIA gpu-kat, a Docker Engine segédprogram telepítéséhez [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Szeretné elérni az Azure RDMA-hálózatot, egy RDMA-kompatibilis Virtuálisgép-méretet használja. A CentOS HPC és Batch által támogatott Ubuntu-rendszerképek szükség rdma-t az illesztőprogramok telepítése. További konfigurációs MPI számítási feladatok futtatásához szükség lehet. Lásd: [használata RDMA-kompatibilis vagy a GPU-kompatibilis példányok a Batch-készlet](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Batch-készlet tároló konfigurációja

Ahhoz, hogy a Batch-készlet, tárolókhoz kapcsolódó számítási feladatok futtatásához, meg kell adnia [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) a készlet beállításainak [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objektum. (Ez a cikk hivatkozásokat a Batch .NET API-referenciáját. Kapcsolódó beállítások szerepelnek a [Batch Python](/python/api/azure.batch) API.)

Létrehozhat egy tároló-kompatibilis készlet vagy anélkül prefetched tárolórendszerképek, a következő példákban szemléltetett módon. A lekéréses (vagy előzetes betöltési) folyamat előzetes betöltése a tárolólemezképeket a Docker Hub vagy az interneten egy másik tároló-beállításjegyzék teszi lehetővé. A legjobb teljesítményhez használjon egy [az Azure container registry](../container-registry/container-registry-intro.md) a Batch-fiók ugyanabban a régióban.

A tárolórendszerképek prefetching előnye, hogy feladatokat első indításakor fut ne kelljen várja meg, töltse le a tároló rendszerképét. A tároló konfiguráció lekéri a tárolórendszerképeket a virtuális gépek a készlet létrehozásakor. A készleten futó feladatok ezután hivatkozhatnak a tárolólemezképek listája és a tároló futtatási beállításai.


### <a name="pool-without-prefetched-container-images"></a>Készlet prefetched tárolórendszerképek nélkül

Egy tároló-kompatibilis készlet nélkül prefetched tárolórendszerképek konfigurálásához definiálása `ContainerConfiguration` és `VirtualMachineConfiguration` objektumokat a következő Python-példában látható módon. Ebben a példában használja az Ubuntu Server készletek tárolórendszerképet az Azure Batch a piactéren.


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


### <a name="prefetch-images-for-container-configuration"></a>Képek tároló konfigurációjának előzetes betöltése

Előzetes betöltése a tárolórendszerképeket a készleten, adja hozzá a tárolólemezképek listája (`container_image_names`, Python), a `ContainerConfiguration`. 

Ez a alapvető Python példa bemutatja, hogyan előzetes betöltése a standard Ubuntu tárolórendszerkép [Docker Hub](https://hub.docker.com).

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


A következő C# példa feltételezi, hogy szeretné-e egy TensorFlow-lemezképet előzetes betöltése [Docker Hub](https://hub.docker.com). Ebben a példában az indítási tevékenység, amely a készlet csomópontjain fut a Virtuálisgép-gazda tartalmazza. Előfordulhat, hogy futtatja az indítási tevékenység a gazdagépen, például csatlakoztatása egy fájlkiszolgálón, amely a tárolók elérhető lesz.

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


### <a name="prefetch-images-from-a-private-container-registry"></a>Előzetes betöltése a rendszerképek a privát tárolóregisztrációs adatbázis

Továbbá által egy privát beállításjegyzék-kiszolgálóra való hitelesítése előzetes betöltése tárolórendszerképek. A következő példában a `ContainerConfiguration` és `VirtualMachineConfiguration` objektumok előzetes betöltése egy privát TensorFlow-rendszerképet egy privát Azure container registryből. A lemezkép-hivatkozás megegyezik az előző példában látható módon.

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

Tároló-feladatok futtatása a számítási csomópontokon, meg kell adnia tároló-specifikus beállítások, például a tároló futtatásához lehetőségekről, a rendszerképek és a beállításjegyzék.

Használja a `ContainerSettings` tároló-specifikus beállítások konfigurálása a feladat osztályok tulajdonságát. Ezek a beállítások határozzák meg a [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) osztály.

Ha futtatja a feladatokat a tárolórendszerképek az [felhőbeli tevékenység](/dotnet/api/microsoft.azure.batch.cloudtask) és [feladatkezelői tevékenység](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) tároló igénylése. Azonban a [kezdő tevékenység](/dotnet/api/microsoft.azure.batch.starttask), [feladat-előkészítési tevékenység](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), és [feladatkiadási tevékenység](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nem igénylik a tároló beállításait (azaz futtathatják a tároló környezeten belül, vagy közvetlenül a csomóponton).

A választható [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) további argumentumok vannak a `docker create` parancsot, hogy a feladat fut-e a tároló létrehozása.

### <a name="container-task-working-directory"></a>Tároló tevékenység munkakönyvtárának

Az Azure Batch tároló tevékenység parancssorának egy működő könyvtárba, amely nagyon hasonlít egy normál (nem tároló) a feladat beállítja a Batch a környezetet a tárolóban hajtja végre:

* Az alábbi könyvtárak rekurzív a `AZ_BATCH_NODE_ROOT_DIR` (az Azure Batch-könyvtár a csomóponton gyökér) vannak leképezve a tárolóba
* Az összes környezeti változók vannak leképezve a tárolóba
* Az alkalmazás munkakönyvtár értéke ugyanaz, mint a rendszeres feladatként, így használhatja a szolgáltatások, például az alkalmazáscsomagokat és az erőforrások fájljainak

Batch módosítja az alapértelmezett munkakönyvtár a tárolóban, mert a tipikus tároló munkakönyvtárból különböző helyen futtatja a tevékenységet (például `c:\` alapértelmezés szerint a Windows-tárolók vagy `/` Linux vagy egy másik könyvtárat, ha a tároló rendszerképét konfigurálva). Győződjön meg arról, hogy a tárolóalkalmazások megfelelően Batch környezetében futnak, tegye a következők egyikét: 

* Győződjön meg arról, hogy a tevékenység parancssora (vagy a tároló munkakönyvtár) határozza meg az abszolút elérési út, ha már nincs konfigurálva a a módszerrel.

* A feladat containersettings beállítást a tároló futtatási beállításai a állítson be egy munkakönyvtárba. Például: `--workdir /app`.

Az alábbi Python a Docker Hubból kéri le egy Ubuntu tárolóban futó alapszintű parancssor látható. Itt a `--rm` tároló működtethető, lehetőség eltávolítja a tárolót, a feladat befejezése után.

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

Az alábbi C#-példa egy felhőbeli tevékenység alapszintű tárolóbeállítások látható:

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

* Is megtekintheti a [Batch hajógyárnak](https://github.com/Azure/batch-shipyard) eszközkészlet az egyszerű telepítés a tárolók számítási feladatait az Azure Batch [hajógyárnak receptek](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* További információk a telepítésével és a Docker CE használata Linux rendszeren: a [Docker](https://docs.docker.com/engine/installation/) dokumentációját.

* Egyéni rendszerképek használatával további információkért lásd: [felügyelt egyéni lemezképek használatával hozzon létre egy virtuálisgép-készletek ](batch-custom-images.md).

* Tudjon meg többet a [Moby projekt](https://mobyproject.org/), olyan keretrendszer, tárolóalapú rendszer.