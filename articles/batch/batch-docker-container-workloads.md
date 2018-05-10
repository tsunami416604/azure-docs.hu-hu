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
ms.date: 05/07/2018
ms.author: danlep
ms.openlocfilehash: 8c9f772c9d3908e450961239797f6ce2bd4982e4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="run-container-applications-on-azure-batch"></a>Azure Batch tároló alkalmazások futtatásához

Az Azure Batch lehetővé teszi futtatásához és méretezéséhez számítási feladatok Azure batch nagy számú. Eddig kötegelt feladatok közvetlenül a virtuális gépek (VM) a Batch-készlet futtatta, de most már beállíthat egy Batch-készlet Docker-tárolókban lévő feladatok futtatásához. Ez a cikk bemutatja, hogyan használhatja a kötegelt .NET SDK-készlet számítási csomópontok, amely támogatja a tárolóban futó feladatok létrehozása, és a tároló feladatok futtatásáról a készletet.

Tárolók anélkül, hogy olyan környezetet és az alkalmazások futtatásához függőségek kezeléséhez kötegelt feladatok futtatása egyszerű módot nyújt. Tárolók könnyű, hordozható, ellenállni egység, amely a különböző környezetek futtatható alkalmazások telepítése. Például összeállítása és tesztelése a helyi tárolója, majd a tároló Rendszerkép feltöltése az Azure rendszerben vagy máshol beállításjegyzékbeli. A tároló üzembe helyezési modellel biztosítja, hogy a futtatókörnyezet, az alkalmazás megfelelően mindig telepítve és konfigurálva van, függetlenül attól, ahol a alkalmazás üzemeltetéséhez. Tároló-alapú feladatok kötegben is kihasználhatják nem-tárolófigyelő feladatokhoz alkalmazáscsomagok és erőforrás-fájlok és a kimeneti fájlok felügyeleti funkcióit. 

Ez a cikk Docker-tároló fogalmakat és a Batch-készlet és a .NET SDK használatával feladat létrehozása ismeretét feltételezi. A kódrészleteket úgy van kialakítva, hogy egy ügyfél hasonló alkalmazásban használható a [DotNetTutorial minta](batch-dotnet-get-started.md), és a tároló támogatja a kötegelt kell kód példát.


## <a name="prerequisites"></a>Előfeltételek

* **SDK-verzió**: A kötegelt SDK-k támogatása tároló képek től a következő verziók:
    * Batch REST API verziója 2017-09-01.6.0
    * Batch .NET SDK verzió 8.0.0
    * Kötegelt Python SDK 4.0-s verziója
    * Kötegelt Java SDK 3.0-s verzió
    * Kötegelt Node.js SDK 3.0-s verzió

* **Fiókok**: az Azure-előfizetéséhez, a Batch-fiók, és opcionálisan egy Azure Storage-fiók létrehozásához szüksége.

* **A támogatott Virtuálisgép-lemezkép**: tárolók esetén támogatottak a virtuálisgép-konfiguráció létre készletek képek a következő szakaszban részletes, "támogatott virtuálisgép-lemezképeket." Ha megad egy egyéni lemezképet, az alkalmazás Azure Active Directory kell használnia [(az Azure AD) hitelesítési](batch-aad-auth.md) ahhoz, hogy a tároló-alapú alkalmazásokat és szolgáltatásokat futtathatnak. 


## <a name="supported-virtual-machine-images"></a>Támogatott virtuálisgép-rendszerképek

Kell használnia egy támogatott Windows vagy Linux kép VM-készlet létrehozása számítási csomópontok tároló munkaterhelésekhez.

### <a name="windows-images"></a>Windows-lemezképek

Windows tároló munkaterhelések esetén kötegelt jelenleg támogatja az egyéni lemezképek Docker futó Windows virtuális gépek létrehozásakor, vagy használhatja a Windows Server 2016 Datacenter tárolók lemezkép az Azure piactérről. Ez a rendszerkép tartozik, amely kompatibilis a `batch.node.windows amd64` csomópont ügynök SKU-azonosítója. A tároló támogatott típus jelenleg csak Docker.

### <a name="linux-images"></a>Linux-lemezképek

Linux tároló munkaterhelések esetén kötegelt jelenleg támogatja a következő Linux terjesztésekről Docker futó virtuális gépek létrehozásakor csak egyéni lemezképek: Ubuntu 16.04 LTS vagy CentOS 7.3. Ha a saját egyéni Linux képet, lásd az útmutatást [felügyelt egyéni lemezképet használ a virtuális gépek készlet létrehozása](batch-custom-images.md).

A Docker támogatásához telepíteni [Docker Community Edition (CE)](https://www.docker.com/community-edition) vagy [Docker Enterprise Edition EE](https://www.docker.com/enterprise-edition).

Ha azt szeretné, Azure névhasználati környezet vagy a portok HV virtuális gép méretét GPU teljesítményének előnyeit, telepítendő NVIDIA illesztőprogramok a lemezképre. Is kell telepíteni és futtatni a Docker motor eszközt NVIDIA Feldolgozóegységekkel a [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

Az Azure RDMA hálózati eléréséhez használja az RDMA-kompatibilisek-e Virtuálisgép-méretek, például a A8, A9, H16r, H16mr vagy NC24r. Az Azure piactérről CentOS 7.3 HPC és Ubuntu 16.04 LTS képek szükséges RDMA az illesztőprogramok telepítése. MPI alkalmazásokat és szolgáltatásokat futtathatnak további konfigurálásra lehet szükség. Lásd: [használja az RDMA-kompatibilis vagy a GPU-t példány a Batch-készlet](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Korlátozások

* Kötegelt csak a futó Linux készletek tárolók RDMA-támogatást nyújt.


## <a name="authenticate-using-azure-active-directory"></a>Hitelesítés az Azure Active Directory használatával

A Batch-készlet létrehozásához egy egyéni Virtuálisgép-lemezkép használatakor az ügyfélalkalmazást kell hitelesítenie, az Azure AD integrált hitelesítés (megosztott kulcsos hitelesítés nem működik). Az alkalmazás futtatása előtt győződjön meg arról, hogy regisztrálja az Azure AD hozzá egy identitásának meghatározásához, és adja meg az egyéb alkalmazások engedélyei.

Emellett egy egyéni Virtuálisgép-lemezkép használatakor kell biztosítania IAM hozzáférés-vezérlés a Virtuálisgép-lemezkép eléréséhez alkalmazására. Az Azure portálon kattintson **összes erőforrás**, válassza ki a tároló-lemezképet, és a **hozzáférés-vezérlés (IAM)** szakasz a lemezkép lapon kattintson **hozzáadása**. Az a **engedélyek hozzáadása** adja meg azokat a **szerepkör**, a **való hozzáférés hozzárendelése**, jelölje be **Azure AD-felhasználó, csoport vagy alkalmazás**, ezt a  **Válassza ki** adja meg az alkalmazás nevét.

Az alkalmazás átadni egy Azure AD hitelesítési jogkivonatot a kötegelt ügyfél létrehozásakor. Fejlesztői kötegelt .NET SDK használatával, használjon [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_)leírtak szerint [hitelesítéséhez kötegelt szolgáltatási megoldások és az Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>A készlet létrehozásához egy Virtuálisgép-lemezkép hivatkozik

Az alkalmazás kódjában adjon meg egy hivatkozást a Virtuálisgép-lemezkép a számítási csomópontok a készlet létrehozásában. Hozzon létre ehhez az [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) objektum. A következő módszerek valamelyikével használni kívánt lemezkép adhatja meg:

* Ha egy egyéni rendszerképet használ, adja meg az Azure Resource Manager erőforrás-azonosítót a virtuálisgép-lemezkép. A lemezkép-azonosító formátuma elérési út a következő példában látható módon:

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    A lemezkép-azonosító az Azure portálról beszerzéséhez nyissa meg a **összes erőforrás**, válassza ki az egyéni lemezképet, és a a **áttekintése** szakasz mutatja be a lemezképet, másolja át az elérési utat **erőforrás-azonosító**.

* Használata egy [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based) lemezképet, adja meg a lemezkép leíró paraméterek csoportja: a közzétevő ajánlattípus, SKU, és az képet, a [virtuálisgép-rendszerképek listája](batch-linux-nodes.md#list-of-virtual-machine-images):

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Batch-készlet tároló konfigurációja

Ahhoz, hogy a Batch-készlet tároló alkalmazásokat és szolgáltatásokat futtathatnak, meg kell adnia [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) a készlet beállításait [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objektum.

Létrehozhat egy tároló-kompatibilis készlet vagy anélkül prefetched tároló lemezképek, a következő példákban látható módon. A lekéréses (vagy előzetes betöltési) folyamat lehetővé teszi a Docker-központ vagy az interneten egy másik tárolóban beállításjegyzék tároló képek előzetes betöltése. A tároló képek prefetching előnye, hogy feladatok első indításakor fut nem rendelkeznek letölteni a tároló kép vár. A tároló konfigurációjának lekéri a virtuális gépek tároló lemezképek, a készlet létrehozásakor. A készlet a futó feladatok majd hivatkozhat tároló képek listájának, és a tároló futtassa beállítások.



### <a name="pool-without-prefetched-container-images"></a>Készlet prefetched tároló képek nélkül

Egy tároló-kompatibilis készlet prefetched tároló képek nélküli konfigurálásához meghatározása `ContainerConfiguration` és `VirtualMachineConfiguration` objektumokat a következő példában látható módon. Ez, és a következő példák azt feltételezik, hogy egy egyéni Ubuntu 16.04 LTS lemezképet használ telepítve Docker-motorhoz.

```csharp
// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

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

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>Előzetesen lehívott a tároló konfigurációjának lemezképei lapok

Előzetesen lehívott lapok tároló képek a készlet, adja hozzá a tároló lemezképek listáját (`containerImageNames`) számára a `ContainerConfiguration`, és nevezze el a képlista. Az alábbi példa feltételezi, hogy egy egyéni Ubuntu 16.04 LTS lemezképet használ, és előzetesen lehívott lapok egy TensorFlow lemezkép [Docker Hub](https://hub.docker.com). Ebben a példában a Virtuálisgép-gazda készlet csomópontjain futó start feladat tartalmazza. Előfordulhat, hogy olyan start feladatot futtat az állomáson, például olyan fájlkiszolgálót, mely elérhető a tárolókat az csatlakoztatni.

```csharp
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

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Előzetesen lehívott lapok képek a magánfelhő tároló beállításjegyzékből

Akkor is is előzetesen lehívott lapok tároló képek hitelesítik magukat a személyes tárolót beállításjegyzék-kiszolgálón. A következő példában a `ContainerConfiguration` és `VirtualMachineConfiguration` objektumok egy egyéni Ubuntu 16.04 LTS lemezképpel, és előzetesen lehívott lapok egy titkos TensorFlow lemezképet egy saját Azure-tárolót beállításjegyzékből.

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

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>A feladat tároló beállításai

A számítási csomópontok tároló feladatok futtatásához meg kell adnia a tároló-specifikus beállításokat, például a beállítások, a használandó képek és a beállításjegyzék feladat.

Használja a `ContainerSettings` tulajdonság a feladat osztályok tárolót vonatkozó beállítások konfigurálása. Ezek a beállítások határozzák meg a [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) osztály.

Tároló-lemezképek, a feladatok futtatásakor a [felhő feladat](/dotnet/api/microsoft.azure.batch.cloudtask) és [manager feladat](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) tároló igénylése. Azonban a [feladat indítása](/dotnet/api/microsoft.azure.batch.starttask), [feladat előkészítése tevékenységet](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), és [feladat kiadása tevékenység](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nem igényelnek tároló beállításokat (Ez azt jelenti, hogy futtathatók legyenek egy tároló környezeten belül vagy közvetlenül a csomópont).

A tároló beállításait, az alábbi könyvtárak rekurzív konfigurálása során a `AZ_BATCH_NODE_ROOT_DIR` (az Azure Batch könyvtárak a csomóponton gyökér) vannak leképezve a tárolóba, minden tevékenység környezeti változók vannak leképezve a tároló és a feladat parancssori a tároló végrehajtása.

A kód például a [előzetesen lehívott a tároló konfigurációjának lemezképei lapok](#prefetch-images-for-container-configuration) bemutatta, hogyan adhatja meg a tároló konfigurációjának kezdő tevékenység. Az alábbi példakód bemutatja, hogyan adhatja meg olyan feladatra, felhőalapú tároló konfigurációjának:

```csharp
// Simple container task command

string cmdLine = "<my-command-line>";

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
