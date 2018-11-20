---
title: A virtuális gép futtatása Linux számítási csomópontok – Azure Batch |} A Microsoft Docs
description: Útmutató a Linux rendszerű virtuális gépek az Azure Batch-készleteket a párhuzamos számítási feladatok feldolgozásához.
services: batch
documentationcenter: python
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 45407fb2eca5527c8b24f199c9470311a0e5d6a9
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976757"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Linuxos számítási csomópontok Batch-készletekben kiépítése

Azure Batch segítségével párhuzamos számítási feladatok futtatásához a Windows és a Linuxos virtuális gépek. Ez a cikk részletesen, hogyan hozhat létre Linuxos számítási csomópontokból álló készletek a Batch szolgáltatásban mind a [Batch Python] [ py_batch_package] és [Batch .NET] [ api_net]klienskódtárak.

> [!NOTE]
> Az alkalmazáscsomagok az összes 2017. július 5. után létrehozott Batch-készleten támogatottak. A 2016. március 10. és 2017. július 5. között létrehozott Batch-készletek esetében csak akkor támogatottak, ha a készlet felhőszolgáltatás-konfigurációval lett létrehozva. A 2016. március 10. előtt létrehozott Batch-készletek nem támogatják az alkalmazáscsomagokat. További információkat az alkalmazások a Batch-csomópontokon alkalmazáscsomagok használatával történő központi telepítéséről a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.
>
>

## <a name="virtual-machine-configuration"></a>Virtuálisgép-konfiguráció
Amikor számítási csomópontok készletét a Batch szolgáltatásban létrehozott, amelyből válassza ki a csomópont méretét és az operációs rendszer két lehetősége van: Cloud Services-konfigurációt és a virtuális gép konfigurációját.

A **Cloud Services-konfiguráció** *kizárólag* windowsos számítási csomópontok létrehozására használható. Rendelkezésre álló számítási csomópontok méretét felsorolt [méretű felhőszolgáltatások](../cloud-services/cloud-services-sizes-specs.md), és a rendelkezésre álló operációs rendszeren szerepelnek a [Azure vendég operációs rendszer kiadásai és SDK-kompatibilitási mátrixot](../cloud-services/cloud-services-guestos-update-matrix.md). Azure Cloud Services csomópontokat tartalmazó készletet hoz létre, amikor a csomópont méretét és az operációsrendszer-család a korábban említett cikkekben leírt adja meg. Windows-készletek számítási csomópontok, a Felhőszolgáltatások leggyakrabban szolgál.

**Virtuálisgép-konfiguráció** biztosít a Linux és Windows-rendszerképeket is a számítási csomópontokon. Rendelkezésre álló számítási csomópontok méretét felsorolt [az Azure-beli virtuális gépek méretei](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux), és [az Azure-beli virtuális gépek méretei](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Virtuális géppel konfigurált csomópontokat tartalmazó készletet hoz létre, amikor a csomópontok, a virtuális gép képhivatkozását és a Batch-csomóponti ügynök SKU a csomópontokra telepítendő méretét kell megadnia.

### <a name="virtual-machine-image-reference"></a>Virtuálisgép-lemezkép-hivatkozás
A Batch szolgáltatás által használt [a virtual machine scale sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) biztosít a számítási csomópontok virtuálisgép-konfiguráció. Megadhat egy képet a [Azure Marketplace-en][vm_marketplace], vagy adjon meg egy Ön által előkészített egyéni rendszerképet. Egyéni rendszerképek kapcsolatos további információkért lásd: [készlet létrehozása egyéni rendszerképpel](batch-custom-images.md).

A virtuális gép képhivatkozását konfigurálásakor adja meg a virtuálisgép-lemezkép tulajdonságait. A következő tulajdonságok szükség, amikor létrehoz egy virtuális gép képhivatkozását:

| **Lemezkép-hivatkozás tulajdonságai** | **Példa** |
| --- | --- |
| Közzétevő |Canonical |
| Ajánlat |UbuntuServer |
| SKU |14.04.4-LTS |
| Verzió |legújabb |

> [!TIP]
> Ezek a tulajdonságok és a Marketplace-rendszerképek listázásához többet is megtudhat [navigálása és kiválasztása Linux virtuálisgép-lemezképek az Azure-ban a parancssori felület vagy PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ne feledje, hogy nem minden Marketplace-rendszerképek a Batch jelenleg kompatibilis. További információkért lásd: [csomóponti ügynök SKU](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Csomóponti ügynök SKU
A Batch-csomóponti ügynök egy olyan program, a készlet minden csomópontján lefut, és a csomópont és a Batch szolgáltatás között a parancs és vezérlés felületet biztosít. Több módon implementálható a csomópont-ügynök, néven SKU-k, a különböző operációs rendszerekhez. Lényegében amikor létrehoz egy virtuális gép konfigurációja, először adja meg a virtuális gép képhivatkozását, és megadhatja a csomóponti ügynök telepítéséhez a képen. Minden csomóponti ügynök SKU általában több virtuálisgép-lemezképek kompatibilis. Íme néhány példa a csomóponti ügynök SKU-k:

* 14.04-es Batch.node.ubuntu
* Batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Nem minden virtuálisgép-rendszerképek érhetők el a piactéren kompatibilisek a jelenleg rendelkezésre álló Batch csomópontjainak ügynökeit. A Batch SDK-k segítségével listázhatja ki a rendelkezésre álló csomóponti ügynök SKU-k és a virtuálisgép-lemezképek, amellyel kompatibilisek. Tekintse meg a [lista a virtuálisgép-lemezképek](#list-of-virtual-machine-images) újabb ebben a cikkben további információkat és példákat futásidőben érvényes rendszerképek listájának lekéréséhez.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Hozzon létre egy Linux-készlet: a Batch Python
A következő kódrészlet azt mutatja be a példa bemutatja, hogyan használhatja a [a Microsoft Azure Batch ügyféloldali kódtára a Pythonhoz] [ py_batch_package] Ubuntu Server-készlet létrehozása a számítási csomópontok. Segédanyagok a Batch Python modulban található [azure.batch csomag] [ py_batch_docs] olvasás a Docs.

Ez a kódrészlet létrehoz egy [ImageReference] [ py_imagereference] explicit módon, és adja meg a tulajdonságait (közzétevő, ajánlat, Termékváltozat, verziója) egyes. Az éles kódban javasoljuk azonban, hogy használjon a [list_node_agent_skus] [ py_list_skus] metódus használatával állapítsa meg, és válassza ki az elérhető rendszerképet és a csomópont ügynök Termékváltozat kombinációk futásidőben.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Ahogy korábban említettük, azt javasoljuk, hogy létrehozása helyett a [ImageReference] [ py_imagereference] explicit módon, használhatja a [list_node_agent_skus] [ py_list_skus] dinamikusan válassza ki a jelenleg támogatott csomópont ügynök/Piactéri lemezkép kombinációk metódust. A következő Python-kódrészlet bemutatja, hogyan ezt a módszert használja.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Hozzon létre egy Linux-készlet: a Batch .NET
A következő kódrészlet azt mutatja be a példa bemutatja, hogyan használhatja a [Batch .NET] [ nuget_batch_net] ügyféloldali kódtár, az Ubuntu Server-készlet létrehozása a számítási csomópontok. Annak a [Batch .NET-dokumentáció] [ api_net] a docs.microsoft.com webhelyen.

A következő kódban kódrészletet a [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] metódust jelenleg válassza ki a listából azon támogatott Piactéri lemezkép és a csomópont ügynök Termékváltozat kombinációk. Ezzel a technikával kívánatos, mert a támogatott kombinációk a lista időről időre változhat. Leggyakrabban a támogatott kombinációk kerülnek.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Bár az előző kódrészlettel a [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] metódus dinamikusan listázása, és válassza ki a támogatott kép és a csomópont ügynök Termékváltozat kombinációit (ajánlott), beállíthatja úgy is egy [ImageReference] [ net_imagereference] explicit módon:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Virtuálisgép-lemezképek listája
Az alábbi táblázat a piactéren virtuálisgép-lemezképek, amelyek kompatibilisek a rendelkezésre álló Batch csomópontjainak ügynökeit, ez a cikk utolsó frissítésekor. Fontos megjegyezni, hogy a lista tehát nem végleges, mert a képek és csomópontjainak ügynökeit előfordulhat, hogy hozzáadásának vagy eltávolításának bármikor. Javasoljuk, hogy a Batch-alkalmazások és szolgáltatások mindig használjon [list_node_agent_skus] [ py_list_skus] (Python) vagy [ListNodeAgentSkus] [ net_list_skus] () A Batch .NET) használatával állapítsa meg, és válassza ki a jelenleg rendelkezésre álló termékváltozatok.

> [!WARNING]
> Az alábbi lista bármikor változhatnak. Mindig használja a **lista csomóponti ügynök SKU** kompatibilis virtuális gépek és csomóponti ügynök SKU-k listáját, amikor a kötegelt feladatok futtatása a Batch API-k által választható módszerek.
>
>

| **Közzétevő** | **Ajánlat** | **Lemezkép Termékváltozata** | **Verzió** | **Csomóponti ügynök SKU-azonosítója** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| kötegelt | Megjelenítés – centos73 | Megjelenítés | legújabb | Batch.node.centos 7 |
| kötegelt | Megjelenítés – windows2016 | Megjelenítés | legújabb | batch.node.windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | legújabb | Batch.node.ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | legújabb | 14.04-es Batch.node.ubuntu |
| credativ | Debian | 9 | legújabb | Batch.node.debian 9 |
| credativ | Debian | 8 | legújabb | 8 Batch.node.debian |
| microsoft-ads | Linux-data-adatelemzési-vm | linuxdsvm | legújabb | Batch.node.centos 7 |
| microsoft-ads | standard-data-adatelemzési-vm | standard-data-adatelemzési-vm | legújabb | batch.node.windows amd64 |
| a Microsoft azure-batch | centos-tároló | 7 – 4 | legújabb | Batch.node.centos 7 |
| a Microsoft azure-batch | centos-tároló-rdma-t | 7 – 4 | legújabb | Batch.node.centos 7 |
| a Microsoft azure-batch | ubuntu-server-tároló | 16-04-lts | legújabb | Batch.node.ubuntu 16.04 |
| a Microsoft azure-batch | ubuntu-server-tároló-rdma | 16-04-lts | legújabb | Batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-az-tárolók | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012 – Datacenter-smalldisk | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1-smalldisk | legújabb | batch.node.windows amd64 |
| OpenLogic | CentOS | 7.4 | legújabb | Batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | legújabb | Batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | legújabb | Batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | legújabb | Batch.node.centos 7 |
| Oracle | Oracle Linux | 7.4 | legújabb | Batch.node.centos 7 |
| SUSE | SLES-HPC | 12 – SP2 | legújabb | Batch.node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Kapcsolódás az SSH Linux-csomópontok
A fejlesztés során, vagy a hibaelhárítás során szükség lehet arra szükségesek, hogy jelentkezzen be a készletben lévő csomópontok. Windows számítási csomópontok, eltérően szeretne csatlakozni a Linux-csomópontok távoli asztal protokoll (RDP) nem használható. Ehelyett a Batch szolgáltatás lehetővé teszi a távoli kapcsolat minden egyes csomóponton SSH-hozzáférést.

A következő Python-kódrészlet létrehoz egy felhasználót egy készletet, amely a távoli kapcsolat szükséges minden egyes csomópontján. Ezután jelenít meg a secure shell-(SSH) kapcsolat az egyes csomópontok állapotinformációit.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Itt látható az előző kód négy Linux-csomópontokat tartalmazó készletet a minta kimenete:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Egy jelszó helyett nyilvános SSH-kulcsot a felhasználó létrehozott egy csomóponton is megadhat. A Python SDK-t használja a **ssh_public_key** paraméterrel [ComputeNodeUser][py_computenodeuser]. A .NET-ben, használja a [ComputeNodeUser][net_computenodeuser].[ SshPublicKey] [ net_ssh_key] tulajdonság.

## <a name="pricing"></a>Díjszabás
Az Azure Batch az Azure Cloud Services és az Azure Virtual Machines technológia épül. Maga a Batch szolgáltatás ingyenes, ami azt jelenti, csak a számítási erőforrások díjkötelesek, hogy a Batch megoldások fogyasztanak érhető el. Ha úgy dönt **Cloud Services-konfiguráció**, hogy alapján lesznek kiszámlázva a [Cloud Services díjszabása] [ cloud_services_pricing] struktúra. Ha úgy dönt **virtuálisgép-konfiguráció**, hogy alapján lesznek kiszámlázva a [Virtual Machines díjszabása] [ vm_pricing] struktúra. 

A Batch-csomópontokon használatával alkalmazások központi telepítésekor [alkalmazáscsomagok](batch-application-packages.md), akkor is díjkötelesek az Azure Storage-erőforrások, hogy az alkalmazáscsomagok felhasználását. Általánosságban véve az Azure Storage szolgáltatási költségeit minimálisak. 

## <a name="next-steps"></a>További lépések

A [Python-mintakódot] [ github_samples_py] a a [azure-batch-samples] [ github_samples] tárházban a Githubon tartalmaz parancsprogramokat, amelyek bemutatják, hogyan hajthat végre közös kötegelt műveletek, például a készlet, feladat és a létrehozás. A [információs] [ github_py_readme] , amely társul, a Python minták a szükséges csomagok telepítésével kapcsolatos részleteket is tartalmaz.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: https://docs.microsoft.com/python/api/azure.batch.models.computenodeuser
[py_imagereference]: https://docs.microsoft.com/python/api/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python#list-node-agent-skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
