---
title: Tároló munkaterhelések – Azure Batch | Microsoft Docs
description: Ismerje meg, hogyan futtathat alkalmazásokat a tároló rendszerképeiből Azure Batchon.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 08/09/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: a7de2ba66ccfb5e3f3bce688e68698d90fe2eaf6
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68941333"
---
# <a name="run-container-applications-on-azure-batch"></a>Tároló-alkalmazások futtatása Azure Batch

Azure Batch lehetővé teszi nagy mennyiségű kötegelt számítási feladat futtatását és méretezését az Azure-ban. A Batch-feladatok közvetlenül futtathatók a Batch-készletben lévő virtuális gépeken (csomópontokon), de beállíthat egy batch-készletet is a feladatok Docker-kompatibilis tárolókban való futtatásához a csomópontokon. Ebből a cikkből megtudhatja, hogyan hozhat létre egy olyan számítási csomópontok készletét, amelyek támogatják a futó tárolók feladatait, majd a készleten futtatott tárolók feladatait. 

Ismernie kell a tárolók fogalmait, valamint a Batch-készlet és-feladatok létrehozását. A kód példák a Batch .NET és a Python SDK-kat használják. Más batch SDK-kat és eszközöket is használhat, beleértve a Azure Portal, a tárolók számára engedélyezett batch-készletek létrehozását és a tárolók feladatainak futtatását is.

## <a name="why-use-containers"></a>Miért érdemes tárolókat használni?

A tárolók használata egyszerű módszert kínál a Batch-feladatok futtatására anélkül, hogy a környezetet és a függőségeket az alkalmazások futtatására kellene kezelnie. A tárolók a különböző környezetekben futtatható, könnyű, hordozható és önmagukban álló egységekként telepítik az alkalmazásokat. Például helyileg hozhat létre és tesztelheti a tárolókat, majd feltöltheti a tároló rendszerképét egy Azure-ban vagy máshol található beállításjegyzékbe. A tároló üzembe helyezési modellje biztosítja, hogy az alkalmazás futtatási környezete mindig megfelelően legyen telepítve és konfigurálva az alkalmazás futtatásához. A Batch tároló-alapú feladatai kihasználhatják a nem tároló jellegű feladatok funkcióit is, beleértve az alkalmazások csomagjait és az erőforrás-fájlok és a kimeneti fájlok kezelését. 

## <a name="prerequisites"></a>Előfeltételek

* **SDK-verziók**: A Batch SDK-k a következő verziók szerint támogatják a tároló lemezképeit:
    * Batch REST API 2017-es verzió -09-01.6.0
    * Batch .NET SDK-verzió 8.0.0
    * Batch Python SDK 4,0-es verzió
    * Batch Java SDK 3,0-es verzió
    * Batch Node. js SDK 3,0-es verzió

* **Fiókok**: Az Azure-előfizetésében létre kell hoznia egy batch-fiókot és opcionálisan egy Azure Storage-fiókot.

* **Egy támogatott VM-rendszerkép**: A tárolók csak a virtuálisgép-konfigurációval létrehozott készletekben támogatottak, az alábbi, "támogatott virtuálisgép-rendszerképek" című szakaszban található képekből. Ha egyéni lemezképet ad meg, tekintse meg a következő szakaszban található szempontokat, valamint a [felügyelt egyéni lemezkép használata a virtuális gépek készletének létrehozásához](batch-custom-images.md)című témakör követelményeit. 

### <a name="limitations"></a>Korlátozások

* A Batch csak a Linux-készleteken futó tárolók esetében biztosít RDMA támogatást

* A Windows-tárolók számítási feladataihoz javasolt a készlethez tartozó többmagos virtuálisgép-méret kiválasztása

## <a name="supported-virtual-machine-images"></a>Támogatott virtuálisgép-rendszerképek

A következő támogatott Windows-vagy Linux-rendszerképek egyikével hozzon létre egy virtuálisgép-számítási csomópontok készletét a tároló munkaterhelések számára. A Batch-kompatibilis Piactéri rendszerképekkel kapcsolatos további információkért lásd: [virtuálisgép-lemezképek listája](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Windows-rendszerképek

A Windows-tárolók számítási feladataihoz a Batch jelenleg a **Windows Server 2016 Datacenter és** a tárolók rendszerképét támogatja az Azure Marketplace-en. Windows rendszeren csak a Docker-tárolók lemezképei támogatottak.

Létrehozhat egyéni lemezképeket is a Docker-t futtató virtuális gépekről Windows rendszeren.

### <a name="linux-images"></a>Linux-rendszerképek

A Linux-tárolók számítási feladataihoz a Batch jelenleg a Microsoft Azure Batch által közzétett alábbi Linux-rendszerképeket támogatja az Azure Marketplace-en:

* **CentOS Azure Batch tároló-készletekhez**

* **CentOS (RDMA-illesztőprogramokkal) Azure Batch tároló készletekhez**

* **Ubuntu-kiszolgáló Azure Batch tároló-készletekhez**

* **Ubuntu-kiszolgáló (RDMA-illesztőprogramokkal) Azure Batch Container poolokhoz**

Ezeket a lemezképeket csak Azure Batch készletekben való használatra támogatja a rendszer. Ezek a szolgáltatások:

* Egy előre telepített [Moby](https://github.com/moby/moby) Container Runtime 

* Előre telepített NVIDIA GPU-illesztőprogramok az Azure N sorozatú virtuális gépek üzembe helyezésének egyszerűsítéséhez

* Az Ön által választott rendszerképek előre telepített RDMA-illesztőprogramokkal vagy anélkül. Ezek az illesztőprogramok lehetővé teszik a készlet csomópontjai számára az Azure RDMA-hálózat elérését, ha RDMA-kompatibilis virtuálisgép-méretekre telepítik őket. 

Létrehozhat egyéni lemezképeket is a Docker-t futtató virtuális gépekről a Batch szolgáltatással kompatibilis Linux-disztribúciók egyikén. Ha úgy dönt, hogy saját linuxos rendszerképet ad meg, tekintse meg a [felügyelt egyéni rendszerkép használata a virtuális gépek készletének létrehozásához](batch-custom-images.md)című témakör utasításait.

Egyéni rendszerképek Docker-támogatásához telepítse a [Docker Community Edition (CE)](https://www.docker.com/community-edition) vagy a [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition)szolgáltatást.

Az egyéni Linux-lemezképek használatának további szempontjai:

* Ha egyéni rendszerkép használata esetén szeretné kihasználni az Azure N-sorozatok GPU-teljesítményét, telepítse az NVIDIA-illesztőprogramokat. Emellett telepítenie kell az NVIDIA GPU-ra, az [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker)-ra.

* Az Azure RDMA-hálózat eléréséhez használjon RDMA-kompatibilis VM-méretet. A szükséges RDMA-illesztőprogramok a Batch által támogatott CentOS HPC-és Ubuntu-lemezképekben vannak telepítve. Az MPI-munkaterhelések futtatásához további konfigurálásra lehet szükség. Lásd: [RDMA-kompatibilis vagy GPU-kompatibilis példányok használata a Batch](batch-pool-compute-intensive-sizes.md)-készletben.


## <a name="container-configuration-for-batch-pool"></a>A Batch-készlet tároló-konfigurációja

Ha engedélyezni szeretné a Batch-készlet számára a tároló-munkaterhelések futtatását, meg kell adnia a [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) beállításait a készlet [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objektumában. (Ez a cikk a Batch .NET API-referenciára mutató hivatkozásokat tartalmaz. A megfelelő beállítások a [Batch Python](/python/api/azure.batch) API-ban találhatók.)

A következő példákban látható módon létrehozhat egy tárolóval rendelkező készletet, amely előre beolvasott tároló-rendszerképekkel vagy anélkül is létrehozható. A lekérési (vagy a prefektusi) folyamat lehetővé teszi a tároló lemezképek előzetes betöltését a Docker hub-ból vagy egy másik, az interneten található tároló-beállításjegyzékből. A legjobb teljesítmény érdekében a Batch-fiókkal megegyező régióban használjon [Azure Container registryt](../container-registry/container-registry-intro.md) .

A tároló lemezképek előzetes beolvasásának előnye, hogy amikor a feladatok először futnak, nem kell megvárniuk a tároló lemezképének letöltését. A tároló konfigurációja lekéri a tároló lemezképeit a virtuális gépekre a készlet létrehozásakor. A készleten futó feladatok ezután hivatkozhatnak a tároló lemezképek és a tároló-futtatási beállítások listájára.


### <a name="pool-without-prefetched-container-images"></a>Készlet rendszerképének előhívása nélkül

Ha egy tárolóval kompatibilis készletet szeretne konfigurálni a tároló lemezképének előzetes beolvasása nélkül, adja meg az alábbi Python-példában látható módon definiált `ContainerConfiguration` és `VirtualMachineConfiguration` objektumokat. Ez a példa az Ubuntu Servert használja Azure Batch Container pools rendszerképhez a piactéren.


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


### <a name="prefetch-images-for-container-configuration"></a>A tárolók konfigurációjának rendszerképei

A tároló lemezképének a készleten való kivonásához adja hozzá a tároló`container_image_names`lemezképek (Python) listáját `ContainerConfiguration`a következőhöz:. 

A következő alapszintű Python-példa bemutatja, hogyan lehet megtekinteni a szabványos Ubuntu Container-rendszerképet a [Docker hub](https://hub.docker.com)-ból.

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

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


Az alábbi C# példa feltételezi, hogy egy TensorFlow-rendszerképet szeretne kiállítani a [Docker hub](https://hub.docker.com)-ból. Ez a példa egy indítási feladatot tartalmaz, amely a virtuális gépen fut a készlet csomópontjain. Előfordulhat, hogy egy indítási feladatot futtat a gazdagépen, például egy olyan fájlkiszolgáló csatlakoztatásához, amely elérhető a tárolóból.

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


### <a name="prefetch-images-from-a-private-container-registry"></a>Rendszerképek a privát tároló beállításjegyzékből

A tároló rendszerképeit is megtekintheti a privát tárolók beállításjegyzék-kiszolgálójának hitelesítésével. A következő példában a és `ContainerConfiguration` `VirtualMachineConfiguration` az objektumok egy privát TensorFlow-képet is kimutatnak egy privát Azure Container registryből. A Képhivatkozás ugyanaz, mint az előző példában.

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

## <a name="container-settings-for-the-task"></a>A feladat tárolójának beállításai

Ha tároló-feladatot szeretne futtatni egy tárolót használó készleten, adja meg a tárolóra vonatkozó beállításokat. A beállítások közé tartozik a használni kívánt rendszerkép, a beállításjegyzék és a tároló futtatási beállításai.

* A feladattípusok `ContainerSettings` tulajdonságával konfigurálhatja a tárolóra vonatkozó beállításokat. Ezeket a beállításokat a [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) osztály határozza meg. Vegye figyelembe, `--rm` hogy a tároló beállítás nem igényel `--runtime` további beállítást, mert a Batch által gondoskodik. 

* Ha tároló lemezképeken futtat feladatokat, a [Cloud Task](/dotnet/api/microsoft.azure.batch.cloudtask) és a Feladatkezelő [feladat](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) tároló-beállításokat igényel. Azonban a [Start tevékenység](/dotnet/api/microsoft.azure.batch.starttask), a [feladat-előkészítési feladat](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)és a [feladat kiadása feladat](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nem igényli a tárolók beállításait (azaz a tároló környezetben vagy közvetlenül a csomóponton futhatnak).

### <a name="container-task-command-line"></a>Tároló feladat parancssora

Egy tároló feladatának futtatásakor a Batch automatikusan a [Docker Create](https://docs.docker.com/engine/reference/commandline/create/) paranccsal hoz létre egy tárolót a feladatban megadott rendszerkép használatával. A Batch ezután a tárolóban ellenőrzi a feladat végrehajtását. 

Akárcsak a nem tároló batch-feladatokhoz, beállíthatja a parancssort egy tároló feladathoz. Mivel a Batch automatikusan létrehozza a tárolót, a parancssor csak azt a parancsot vagy parancsokat határozza meg, amelyek a tárolóban fognak futni.

Ha egy batch-feladat tároló-képe [BelépésiPont](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) -parancsfájllal van konfigurálva, beállíthatja, hogy a parancssor az alapértelmezett BelépésiPont használja, vagy felülbírálja: 

* A tároló rendszerképének alapértelmezett BELÉPÉSIPONT használatához állítsa a feladat parancssorát az üres karakterláncra `""`.

* Az alapértelmezett BelépésiPont felülbírálásához, vagy ha a rendszerkép nem rendelkezik BelépésiPont, állítson be egy megfelelő parancssort a tárolóhoz, például `/app/myapp` vagy. `/bin/sh -c python myscript.py`

A választható [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) további argumentumok, amelyeket a `docker create` Batch a tároló létrehozásához és futtatásához használ. Ha például a tároló munkakönyvtárát szeretné beállítani, állítsa be a `--workdir <directory>` beállítást. További lehetőségekért tekintse meg a Docker- [létrehozási](https://docs.docker.com/engine/reference/commandline/create/) referenciát.

### <a name="container-task-working-directory"></a>Tároló-feladat munkakönyvtára

A Batch-tároló feladat a tároló egyik munkakönyvtárában fut, amely nagyon hasonlít a címtár batch-készletéhez a normál (nem tárolós) feladathoz. Vegye figyelembe, hogy ez a munkakönyvtár különbözik a [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) , ha a rendszerképben van konfigurálva, vagy az alapértelmezett`C:\` tároló munkakönyvtára (Windows `/` -tárolón vagy Linux-tárolón). 

Batch-tároló feladathoz:

* Az összes könyvtár rekurzív `AZ_BATCH_NODE_ROOT_DIR` módon a gazdagép csomópontján (Azure batch könyvtárak gyökerében) van leképezve a tárolóba.
* Az összes feladat környezeti változó le van képezve a tárolóba
* A csomóponton a `AZ_BATCH_TASK_WORKING_DIR` feladat munkakönyvtára ugyanúgy van beállítva, mint a normál feladatokhoz, és a tárolóba van rendelve. 

Ezek a leképezések lehetővé teszik, hogy a tárolókkal kapcsolatos feladatok ugyanúgy működjenek, mint a nem tároló jellegű feladatok. Például alkalmazásokat telepíthet az alkalmazáscsomag használatával, hozzáférhet az Azure Storage-ból származó erőforrás-fájlokhoz, a feladat környezeti beállításaival, a tároló leállása után pedig megőrzi a feladat kimeneti fájljait.

### <a name="troubleshoot-container-tasks"></a>Tároló-feladatok hibakeresése

Ha a tároló feladata nem a várt módon fut, előfordulhat, hogy le kell kérnie a WORKDIR vagy a BELÉPÉSIPONT konfigurációjának adatait. A konfiguráció megtekintéséhez futtassa a Docker- [rendszerkép vizsgálata](https://docs.docker.com/engine/reference/commandline/image_inspect/) parancsot. 

Ha szükséges, módosítsa a tároló feladat beállításait a rendszerkép alapján:

* Abszolút elérési utat ad meg a feladat parancssorában. Ha a feladat parancssorában a rendszerkép alapértelmezett BELÉPÉSIPONT van használatban, győződjön meg arról, hogy az abszolút elérési út be van állítva.

* A feladat tároló-futtatási beállításaiban módosítsa a munkakönyvtárat úgy, hogy az megfeleljen a rendszerkép WORKDIR. Adja meg `--workdir /app`például a következőt:.

## <a name="container-task-examples"></a>Példák a tárolók feladatára

A következő Python-kódrészlet egy alapszintű parancssort mutat be, amely egy, a Docker hub-ból húzott fiktív rendszerképből létrehozott tárolóban fut. Itt a `--rm` tároló lehetőség eltávolítja a tárolót a feladat befejezése után, és a `--workdir` beállítás egy munkakönyvtárat állít be. A parancssor felülbírálja a tároló BELÉPÉSIPONT egy egyszerű rendszerhéj-paranccsal, amely egy kisméretű fájlt ír a gazdagépen található feladat-munkakönyvtárba. 

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

A következő C# példa egy felhőalapú feladat alapszintű tároló-beállításait mutatja be:

```csharp
// Simple container task command

string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>További lépések

* Lásd még: a [Batch hajógyár](https://github.com/Azure/batch-shipyard) eszközkészlete a tárolók számítási feladatainak egyszerű üzembe helyezését a [hajógyári receptek](https://github.com/Azure/batch-shipyard/tree/master/recipes)alapján Azure Batch.

* A Docker CE Linux rendszeren való telepítésével és használatával kapcsolatos további információkért tekintse meg a [Docker](https://docs.docker.com/engine/installation/) dokumentációját.

* További információ az egyéni lemezképek használatáról: [felügyelt egyéni lemezkép használata virtuális gépek készletének létrehozásához](batch-custom-images.md).

* További információ a [Moby](https://mobyproject.org/)-projektről, a tároló-alapú rendszerek létrehozási keretrendszeréről.
