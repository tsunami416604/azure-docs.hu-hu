---
title: Tároló-munkaterhelések – Azure Batch
description: Ismerje meg, hogyan futtathat és skálázhat alkalmazásokat az Azure Batch tárolólemezképekről. Hozzon létre egy számítási csomópontokkészletét, amelyek támogatják a tárolófeladatok futtatását.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/02/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 81f4e753ffbaaefd5761c9396a6533bac9f212c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254842"
---
# <a name="run-container-applications-on-azure-batch"></a>Tárolóalkalmazások futtatása az Azure Batch-en

Az Azure Batch lehetővé teszi, hogy nagyszámú kötegelt számítástechnikai feladatot futtasson és méretezz az Azure-ban. A kötegelt feladatok közvetlenül futtathatók a kötegkészletben lévő virtuális gépeken (csomópontokon), de beállíthat egy batch készletet a Feladatok futtatásához a Docker-kompatibilis tárolókban a csomópontokon. Ez a cikk bemutatja, hogyan hozhat létre egy számítási csomópontok készletét, amelyek támogatják a tárolófeladatok futtatását, majd futtathat tárolófeladatokat a készleten.

Ismernie kell a tároló fogalmait, és hogyan hozhat létre kötegkészletet és feladatot. A kódpéldák a Batch .NET és a Python SDK-k használata. Más Batch SDK-k és eszközök, köztük az Azure Portal használatával is létrehozhat tárolóalapú batch készleteket, és futtathat tárolófeladatokat.

## <a name="why-use-containers"></a>Miért érdemes konténereket használni?

A tárolók használatával egyszerűen futtathatja a Batch-feladatokat anélkül, hogy egy környezetet és függőségeket kellene kezelnie az alkalmazások futtatásához. A tárolók könnyű, hordozható, önellátó egységekként telepítik az alkalmazásokat, amelyek több különböző környezetben is futtathatók. Például hozzon létre és teszteljen egy tárolót helyileg, majd töltse fel a tárolólemezképet egy beállításjegyzékbe az Azure-ban vagy máshol. A tároló központi telepítési modellje biztosítja, hogy az alkalmazás futásidejű környezete mindig megfelelően legyen telepítve és konfigurálva, bárhol is tárolja az alkalmazást. A Batch tárolóalapú feladatai a nem tárolófeladatok funkcióit is kihasználhatják, beleértve az alkalmazáscsomagokat, valamint az erőforrásfájlok és kimeneti fájlok kezelését.

## <a name="prerequisites"></a>Előfeltételek

* **SDK-verziók:** A Batch SDK-k a következő verzióktól támogatják a tárolórendszerképeket:
    * Batch REST API 2017-09-01.6.0-s verziója
    * Batch .NET SDK 8.0.0-s verzió
    * Batch Python SDK 4.0-s verzió
    * Batch Java SDK 3.0-s verzió
    * Batch Node.js SDK 3.0-s verzió

* **Fiókok:** Az Azure-előfizetésben létre kell hoznia egy Batch-fiókot és szükség esetén egy Azure Storage-fiókot.

* **Támogatott virtuálisgép-lemezkép:** A tárolók csak a virtuális gép konfigurációjával létrehozott készletekben támogatottak a következő szakaszban részletezett " Támogatott virtuálisgép-lemezképek" című képekből. Ha egyéni lemezképet ad meg, olvassa el a következő szakaszban található szempontokat és a [Felügyelt egyéni lemezkép használata a virtuális gépek készletének létrehozásához](batch-custom-images.md)című témakör követelményeit.

### <a name="limitations"></a>Korlátozások

* A Batch csak linuxos készleteken futó tárolók rdma-támogatást nyújt

* A Windows tárolók számítási feladataihoz ajánlott többmagos virtuális gépméretet választani a készlethez

## <a name="supported-virtual-machine-images"></a>Támogatott virtuálisgép-lemezképek

Az alábbi támogatott Windows- vagy Linux-lemezképek egyikével hozzon létre virtuálisgép-számítási csomópontok készletét a tároló-számítási feladatokhoz. A Batch-el kompatibilis Marketplace-lemezképekről a [virtuálisgép-lemezképek listájában](batch-linux-nodes.md#list-of-virtual-machine-images)talál további információt.

### <a name="windows-support"></a>Windows-támogatás

A Batch támogatja a tárolótámogatási kijelöléssel rendelkező Windows-kiszolgálólemezképeket. Ezek a képskunevek általában a `-with-containers` `-with-containers-smalldisk`vagy a segítségével vannak utófixálva. Emellett [az API-t az összes támogatott lemezképek batch](batch-linux-nodes.md#list-of-virtual-machine-images) jelöli a `DockerCompatible` képesség, ha a rendszerkép támogatja a Docker-tárolók.

A Windows docker-t futtató virtuális gépekről is létrehozhat egyéni lemezképeket.

### <a name="linux-support"></a>Linux-támogatás

Linux-tároló-számítási feladatok esetén a Batch jelenleg a Microsoft Azure Batch által az Azure Piactéren közzétett linuxos lemezképeket támogatja anélkül, hogy egyéni lemezképre lenne szükség.

#### <a name="vm-sizes-without-rdma"></a>Virtuális gép méreterdma nélkül

- Publisher:`microsoft-azure-batch`
  - Ajánlat:`centos-container`
  - Ajánlat:`ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>Virtuális gép méreterdma-val

- Publisher:`microsoft-azure-batch`
  - Ajánlat:`centos-container-rdma`
  - Ajánlat:`ubuntu-server-container-rdma`

Ezek a rendszerképek csak az Azure Batch-készletekben való használatra támogatottak, és a Docker-tároló konsisza végrehajtására vannak igazítva. Ezek jellemzője:

* Előre telepített Docker-kompatibilis [Moby-tároló](https://github.com/moby/moby) futásidejű

* Előre telepített NVIDIA GPU-illesztőprogramok és NVIDIA tárolófutási idő az Azure N sorozatú virtuális gépeken való üzembe helyezés egyszerűsítése érdekében

* Előre telepített/előre konfigurált lemezkép, amely támogatja az Infiniband RDMA virtuális `-rdma`gép méreteket a . Jelenleg ezek a képek nem támogatják az SR-IOV IB/RDMA virtuális gép méretét.

Egyéni rendszerképeket is létrehozhat a Dockert futtató virtuális gépekről a Batch-el kompatibilis Linux-disztribúciók egyikén. Ha úgy dönt, hogy saját egyéni Linux-lemezképet biztosít, olvassa el a [Felügyelt egyéni lemezkép használata a virtuális gépek készletének létrehozásához](batch-custom-images.md)című témakör utasításait.

Egyéni lemezkép Docker-támogatásához telepítse a [Docker Community Edition (CE)](https://www.docker.com/community-edition) vagy [a Docker Enterprise Edition (EE) rendszert.](https://www.docker.com/enterprise-edition)

Az egyéni Linux-lemezképek használatával kapcsolatos további szempontok:

* Az Azure N sorozatú méretek GPU-teljesítményének egyéni lemezképek használata esetén történő kihasználása érdekében telepítse elő az NVIDIA illesztőprogramokat. Emellett telepítenie kell a Docker Engine Segédprogramot az NVIDIA GPU-khoz, [az NVIDIA Docker-t.](https://github.com/NVIDIA/nvidia-docker)

* Az Azure RDMA-hálózat eléréséhez használjon RDMA-kompatibilis virtuális gép mérete. A szükséges RDMA-illesztőprogramok a Batch által támogatott CentOS HPC és Ubuntu lemezképeken vannak telepítve. Az MPI-munkaterhelések futtatásához további konfigurációra lehet szükség. Lásd: [RDMA-képes vagy GPU-képes példányok használata a batch készletben](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Tárolókonfiguráció a kötegkészlethez

Ahhoz, hogy egy batch készlet futtathassa a tároló-munkaterheléseket, meg kell adnia a [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) beállításait a készlet [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objektumában. (Ez a cikk a Batch .NET API hivatkozására mutató hivatkozásokat tartalmaz. A megfelelő beállítások a [Batch Python](/python/api/overview/azure/batch) API-ban találhatók.)

Létrehozhat egy tároló-kompatibilis készletet előre bekért tárolólemezképekkel vagy anélkül, ahogy az alábbi példákban látható. A lekéréses (vagy előzetes betöltési) folyamat lehetővé teszi a tárolórendszerképek előzetes betöltését a Docker Hubról vagy egy másik tároló beállításjegyzékéből az interneten. A legjobb teljesítmény érdekében használjon [egy Azure-tároló beállításjegyzékugyanabban](../container-registry/container-registry-intro.md) a régióban, mint a Batch-fiók.

A tárolórendszerképek előzetes betöltésének előnye, hogy amikor a feladatok először futnak, nem kell megvárniuk a tárolórendszerkép letöltését. A tárolókonfiguráció lekéri a tárolórendszerképeket a virtuális gépekre a készlet létrehozásakor. A készleten futó feladatok ezután hivatkozhatnak a tárolórendszerképek és a tárolófuttatási beállítások listájára.


### <a name="pool-without-prefetched-container-images"></a>Készlet előre bekért tárolólemezképek nélkül

Egy tároló-kompatibilis készlet konfigurálása előre letöltött `ContainerConfiguration` `VirtualMachineConfiguration` tárolólemezképek nélkül, határozza meg és objektumok, ahogy az a következő Python-példában látható. Ez a példa az Ubuntu Server for Azure Batch tárolókészletek a Marketplace-ről.


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


### <a name="prefetch-images-for-container-configuration"></a>Előhívási lemezképek a tároló konfigurációjához

A tárolórendszerképek előhívásához adja hozzá a`container_image_names`tárolórendszerképek listáját `ContainerConfiguration`( Pythonban) a hoz.

A következő alapvető Python-példa bemutatja, hogyan lehet előre beolvasni egy szabványos Ubuntu-tárolórendszerképet a [Docker Hubból.](https://hub.docker.com)

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


A következő C# példa feltételezi, hogy elő szeretne tölteni egy TensorFlow-rendszerképet a [Docker Hubból.](https://hub.docker.com) Ebben a példában egy indítási feladat, amely a készlet csomópontok on a virtuális gép gazdagép. Futtathat egy indítási feladatot a gazdagépen, például egy fájlkiszolgáló csatlakoztatásához, amely elérhető a tárolókból.

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


### <a name="prefetch-images-from-a-private-container-registry"></a>Lemezképek előzetes betöltése egy privát tároló rendszerleíró adatbázisából

A tárolórendszerképeket úgy is előre behívhatja, hogy hitelesíti őket egy privát tároló beállításjegyzék-kiszolgálójára. A következő példában `ContainerConfiguration` `VirtualMachineConfiguration` a és az objektumok előzetesen egy privát TensorFlow-rendszerképet egy privát Azure-tároló beállításjegyzékből. A képhivatkozás megegyezik az előző példában láthatóval.

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

## <a name="container-settings-for-the-task"></a>A feladat tárolóbeállításai

Ha tárolófeladatot szeretne futtatni egy tárolókompatibilis készleten, adja meg a tárolóspecifikus beállításokat. A beállítások közé tartoznak a használandó lemezkép, a beállításjegyzék és a tárolófuttatási beállítások.

* A `ContainerSettings` feladatosztályok tulajdonságával konfigurálhatja a tárolóspecifikus beállításokat. Ezeket a beállításokat a [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) osztály határozza meg. Vegye figyelembe, hogy a `--rm` tároló `--runtime` beállítás nem igényel további lehetőséget, mivel a Batch gondoskodik róla.

* Ha a feladatokat tárolólemezképeken futtatja, a [felhőbeli feladat](/dotnet/api/microsoft.azure.batch.cloudtask) és a [feladatkezelő feladat](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) tárolóbeállításokat igényel. A [kezdési feladat](/dotnet/api/microsoft.azure.batch.starttask), a [feladat-előkészítési feladat](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)és a [feladatkiadási feladat](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) azonban nem igényel tárolóbeállításokat (azaz futtathatók egy tárolókörnyezetben vagy közvetlenül a csomóponton).

### <a name="container-task-command-line"></a>Konténerfeladat parancssora

Tárolófeladat futtatásakor a Batch automatikusan a [docker create](https://docs.docker.com/engine/reference/commandline/create/) parancsot használja egy tároló létrehozásához a feladatban megadott lemezkép használatával. A Batch ezután szabályozza a feladat végrehajtását a tárolóban.

A nem tárolóköteg-feladatokhoz is parancssort kell beállítani egy tárolófeladathoz. Mivel a Batch automatikusan létrehozza a tárolót, a parancssor csak a tárolóban futó parancsot vagy parancsokat adja meg.

Ha egy Batch feladat tárolólemezképe [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) parancsfájllal van konfigurálva, beállíthatja, hogy a parancssor az alapértelmezett ENTRYPOINT-ot használja, vagy felülbírálja azt:

* A tárolólemezkép alapértelmezett ENTRYPOINT parancsának használatához állítsa a `""`feladat parancssorát az üres karakterláncra .

* Az alapértelmezett ENTRYPOINT felülbírálásához, vagy ha a képen nincs ENTRYPOINT, állítson be egy, `/app/myapp` `/bin/sh -c python myscript.py`a tárolónak megfelelő parancssort, például vagy .

A választható [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) további argumentumok, amelyeket a Batch a `docker create` tároló létrehozásához és futtatásához használ. Ha például a tároló munkakönyvtárát szeretné `--workdir <directory>` beállítani, állítsa be a beállítást. Tekintse meg a [docker hozzon létre](https://docs.docker.com/engine/reference/commandline/create/) hivatkozást a további lehetőségeket.

### <a name="container-task-working-directory"></a>Tárolófeladat munkakönyvtára

A Batch tárolófeladat végrehajtása a tároló ban lévő munkakönyvtárban történik, amely nagyon hasonlít a batch-alapú könyvtárhoz egy normál (nem tároló) feladathoz. Vegye figyelembe, hogy ez a munkakönyvtár eltér a [WORKDIR-től,](https://docs.docker.com/engine/reference/builder/#workdir) `C:\` ha a lemezképben van konfigurálva, vagy az alapértelmezett tároló munkakönyvtárától (Windows-tárolón vagy `/` Linux-tárolón).

Kötegelt tárolófeladat esetén:

* Az összes könyvtár rekurzív `AZ_BATCH_NODE_ROOT_DIR` alatt a gazdagép csomópont (a gyökere az Azure Batch-könyvtárak) vannak leképezve a tárolóba
* Az összes feladatkörnyezeti változó le van képezve a tárolóba
* A csomóponton `AZ_BATCH_TASK_WORKING_DIR` lévő feladatmunkakönyvtár ugyanaz, mint egy normál feladathoz, és a tárolóba van leképezve.

Ezek a leképezések lehetővé teszik, hogy a tárolófeladatok at ugyanúgy dolgozzon, mint a nem tárolófeladatokat. Például alkalmazásokat telepíthet alkalmazáscsomagok használatával, erőforrásfájlokat érhet el az Azure Storage-ból, feladatkörnyezet-beállításokat használhat, és a tároló leállítása után megőrizheti a feladatkimeneti fájlokat.

### <a name="troubleshoot-container-tasks"></a>Konténerfeladatok – problémamegoldás

Ha a tárolófeladat nem a várt módon fut, előfordulhat, hogy a tárolórendszerkép WORKDIR- vagy ENTRYPOINT-konfigurációjával kapcsolatos információkat kell beszereznie. A konfiguráció megtekintéséhez futtassa a [docker-rendszerkép-vizsgálat](https://docs.docker.com/engine/reference/commandline/image_inspect/) parancsot.

Szükség esetén módosítsa a tárolófeladat beállításait a lemezkép alapján:

* Abszolút elérési út megadása a feladat parancssorában. Ha a kép alapértelmezett ENTRYPOINT-ját használja a feladat parancssorához, győződjön meg arról, hogy abszolút elérési út van beállítva.

* A feladat tárolófuttatási beállításaiban módosítsa a munkakönyvtárat úgy, hogy az megfeleljen a lemezképWORKDIR-jének. Például állítsa `--workdir /app`be a .

## <a name="container-task-examples"></a>Példák a tárolófeladatra

A következő Python-kódrészlet egy alapparancs-sort jelenít meg egy, a Docker Hubból leállított fiktív rendszerképből létrehozott tárolóban futó tárolóban. Itt a `--rm` tároló beállítás eltávolítja a tárolót a `--workdir` feladat befejezése után, és a beállítás beállítja a munkakönyvtárat. A parancssor felülbírálja a tároló ENTRYPOINT-ot egy egyszerű parancsértelmezővel, amely egy kis fájlt ír az állomás feladatmunkakönyvtárába.

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

A következő C# példa egy felhőalapú feladat alapvető tárolóbeállításait mutatja be:

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

* Tekintse meg a [Batch Hajógyár](https://github.com/Azure/batch-shipyard) eszközkészletet a tárolószámítási feladatok egyszerű üzembe helyezéséhez az Azure Batch-en a [Hajógyár receptjein](https://github.com/Azure/batch-shipyard/tree/master/recipes)keresztül.

* A Docker CE Linuxon történő telepítéséről és használatáról a [Docker](https://docs.docker.com/engine/installation/) dokumentációjában olvashat bővebben.

* Az egyéni lemezképek használatáról a [Felügyelt egyéni lemezkép használata virtuális gépkészlet létrehozásához](batch-custom-images.md)című témakörben talál további információt.

* Tudjon meg többet a [Moby projektről,](https://mobyproject.org/)amely a tárolóalapú rendszerek létrehozásának keretrendszere.
