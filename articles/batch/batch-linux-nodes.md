---
title: Futtatási Linux virtuális gép számítási csomópontok - Azure Batch |} Microsoft Docs
description: 'Útmutató: a Linux virtuális gépek az Azure Batch készleteinek a párhuzamos számítási feladatok feldolgozásához.'
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
ms.openlocfilehash: f3faa9e811216cc930354b76903519a66f3d3587
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128811"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Linux számítási csomópontok kötegelt készletek kiépítése

Azure Batch segítségével párhuzamos számítási feladatok futtatásához a Linux és a Windows virtuális gépeken. Ez a cikk részletesen létrehozása Linux számítási csomópontok készleteinek a Batch szolgáltatás használatával is a [kötegelt Python] [ py_batch_package] és [Batch .NET] [ api_net]klienskódtárak segítségével.

> [!NOTE]
> Az alkalmazáscsomagok az összes 2017. július 5. után létrehozott Batch-készleten támogatottak. A 2016. március 10. és 2017. július 5. között létrehozott Batch-készletek esetében csak akkor támogatottak, ha a készlet felhőszolgáltatás-konfigurációval lett létrehozva. A 2016. március 10. előtt létrehozott Batch-készletek nem támogatják az alkalmazáscsomagokat. További információkat az alkalmazások a Batch-csomópontokon alkalmazáscsomagok használatával történő központi telepítéséről a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.
>
>

## <a name="virtual-machine-configuration"></a>Virtuálisgép-konfiguráció
Számítási csomópontok készletét kötegben létrehozásakor, amelyből a csomópont méretét és az operációs rendszer két választási lehetősége van: Felhő konfigurálása és a virtuális gép konfigurációját.

A **Cloud Services-konfiguráció** *kizárólag* windowsos számítási csomópontok létrehozására használható. Elérhető számítási csomópont méretek szereplő [Felhőszolgáltatások mérete](../cloud-services/cloud-services-sizes-specs.md), és a rendelkezésre álló operációs rendszeren jelennek meg a [Azure vendég operációs rendszereinek kiadásait és SDK-kompatibilitási mátrixát](../cloud-services/cloud-services-guestos-update-matrix.md). Azure Cloud Services csomópontok tartalmazó készletet hoz létre, amikor a csomópont méretének és az operációsrendszer-család a korábban említett cikkekben leírt adja meg. A Windows készleteinek számítási csomópontokat, a Cloud Services leggyakrabban szolgál.

**Virtuálisgép-konfiguráció** biztosít a Linux és a Windows lemezképek számítási csomópontjain. Elérhető számítási csomópont méretek szereplő [az Azure virtuális gépek méretei](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) és [az Azure virtuális gépek méretei](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Amikor virtuálisgép-konfiguráció csomópontot tartalmazó készletet hoz létre, meg kell adnia a csomópontokat, a virtuális gép Képhivatkozás és a kötegelt csomópont ügynök SKU csomópontjain telepítendő méretét.

### <a name="virtual-machine-image-reference"></a>Virtuális gép Képhivatkozás
A Batch szolgáltatás által használt [virtuálisgép-méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) arra, hogy a virtuális gép konfigurációját a számítási csomópontok. Megadhatja, hogy a kép a [Azure piactér][vm_marketplace], vagy adjon meg egy előkészített egyéni lemezképet. Egyéni lemezképek kapcsolatos további tudnivalókért lásd: [készlet létrehozása az egyéni lemezkép](batch-custom-images.md).

A virtuális gép Képhivatkozás konfigurálásakor adja meg a virtuálisgép-lemezkép tulajdonságait. A következő tulajdonságok szükség, amikor egy virtuális gép Képhivatkozás létrehoz:

| **Kép hivatkozás tulajdonságai** | **Példa** |
| --- | --- |
| Közzétevő |Canonical |
| Ajánlat |UbuntuServer |
| SKU |14.04.4-LTS |
| Verzió |legújabb |

> [!TIP]
> Ezeket a tulajdonságokat, és hogyan listázhat a piactéren elérhető rendszerkép kapcsolatos részletesebb [keresse meg és jelölje be Linux virtuális gép képfájljait az Azure-ban a parancssori felületen vagy a PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vegye figyelembe, hogy nem minden piactéren elérhető rendszerkép kompatibilisek jelenleg kötegelt. További információkért lásd: [csomópont ügynök SKU](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Csomópont ügynök SKU
A kötegelt csomópont ügynök egy olyan program, a készlet minden egyes csomópontján fut, és a parancs-és-ellenőrzés felületet, a csomópont és a Batch szolgáltatás között. Nincsenek a csomópont ügynök SKU, úgynevezett különböző operációs rendszerek különböző implementációja. Alapvetően egy virtuálisgép-konfiguráció létrehozásakor először adja meg a virtuális gép képhivatkozás, és adja a csomópont ügynök, a lemezkép telepítéséhez. Minden csomópont ügynök SKU általában több virtuálisgép-lemezkép kompatibilis. Íme néhány példa a csomópont ügynök SKU:

* 14.04 Batch.node.ubuntu
* Batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Nem minden virtuálisgép-rendszerképek a piactéren rendelkezésre álló kompatibilisek a jelenleg rendelkezésre álló kötegben csomópontjainak ügynökeit. A kötegelt SDK-k segítségével kilistázhatja a rendelkezésre álló csomópont ügynök SKU és a virtuálisgép-lemezképeket, amelyekhez kompatibilis. Tekintse meg a [lista a virtuálisgép-rendszerképek](#list-of-virtual-machine-images) további információt és példákat a futási időben érvényes képek listájának beolvasása az ebben a cikkben később.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Linux-készlet létrehozása: kötegelt Python
A következő kódrészletet szemlélteti, hogyan használható a [Microsoft Azure Batch ügyféloldali kódtára a Pythonhoz] [ py_batch_package] Ubuntu Server készlet létrehozása a számítási csomópontok. A kötegelt Python modul ismertető dokumentációjában található [azure.batch csomag] [ py_batch_docs] a dokumentumok olvasáskor.

Ezt a kódrészletet hoz létre egy [ImageReference] [ py_imagereference] explicit módon meghatározza az egyes tulajdonságát (közzétevő, offer, SKU, verziója). Az éles kódban, azonban azt javasoljuk, hogy használja a [list_node_agent_skus] [ py_list_skus] metódus használatával állapítsa meg és jelölje ki a rendelkezésre álló kép és a csomópont ügynök SKU kombinációval futásidőben.

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

Ahogy korábban említettük, azt javasoljuk, hogy létrehozása helyett a [ImageReference] [ py_imagereference] explicit módon, használhatja a [list_node_agent_skus] [ py_list_skus] dinamikusan választhatnak a jelenleg támogatott csomópont ügynök/Piactéri lemezkép kombinációk metódust. A következő Python részlet szemlélteti ezt a módszert használja.

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

## <a name="create-a-linux-pool-batch-net"></a>Linux-készlet létrehozása: Batch .NET
A következő kódrészletet szemlélteti, hogyan használható a [Batch .NET] [ nuget_batch_net] ügyféloldali kódtár Ubuntu Server készlet létrehozása a számítási csomópontok. Megtalálhatja az [Batch .NET referenciadokumentációt] [ api_net] docs.microsoft.com.

A következő kódban részlet a [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] mód közül jelenleg támogatott Piactéri lemezkép-csomópont ügynök SKU kombinációkat. Ez a módszer nem kívánatos, mert a lista támogatott kombinációk időnként változhat. A leggyakrabban a támogatott kombinációk kerülnek.

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

Bár az előző részlet használ a [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] metódus dinamikus listában, és válassza ki a támogatott kép és a csomópont ügynök SKU kombinációk (ajánlott), beállíthatja úgy is egy [ImageReference] [ net_imagereference] explicit módon:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Virtuálisgép-rendszerképek listája
A következő táblázat a piactér virtuálisgép-lemezképeket, amelyek kompatibilisek a rendelkezésre álló kötegben csomópontjainak ügynökeit, ez a cikk legutóbbi frissítésekor. Fontos megjegyezni, hogy a lista létrehozási nem végleges mert képek és csomópontjainak ügynökeit előfordulhat, hogy hozzáadására vagy eltávolítására bármikor. Javasoljuk, hogy a Batch-alkalmazások és szolgáltatások mindig [list_node_agent_skus] [ py_list_skus] (Python) vagy [ListNodeAgentSkus] [ net_list_skus] () Batch .NET) használatával állapítsa meg és válassza ki a jelenleg elérhető termékváltozatok.

> [!WARNING]
> Az alábbi lista bármikor módosíthatja. Mindig a **lista csomópont ügynök SKU** metódusok kompatibilis virtuális gép és csomópont ügynök SKU listáját, amikor a kötegelt feladatok futtatása a kötegelt API-k érhető el.
>
>

| **Közzétevő** | **Ajánlat** | **Kép Termékváltozat** | **Verzió** | **Csomópont ügynök SKU-azonosítója** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| kötegelt | Megjelenítés-centos73 | Megjelenítés | legújabb | Batch.node.centos 7 |
| kötegelt | Megjelenítés-windows2016 | Megjelenítés | legújabb | batch.node.windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | legújabb | Batch.node.ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | legújabb | 14.04 Batch.node.ubuntu |
| credativ | Debian | 9 | legújabb | Batch.node.debian 9 |
| credativ | Debian | 8 | legújabb | 8 Batch.node.debian |
| microsoft-ads | Linux-adatok-tudományos-vm | linuxdsvm | legújabb | Batch.node.centos 7 |
| microsoft-ads | Standard-adatok-tudományos-vm | Standard-adatok-tudományos-vm | legújabb | batch.node.windows amd64 |
| Microsoft-azure-köteg | centos-tároló | 7-4 | legújabb | Batch.node.centos 7 |
| Microsoft-azure-köteg | centos-tároló-rdma | 7-4 | legújabb | Batch.node.centos 7 |
| Microsoft-azure-köteg | ubuntu-kiszolgáló-tároló | 16-04-es lts verzió | legújabb | Batch.node.ubuntu 16.04 |
| Microsoft-azure-köteg | ubuntu-kiszolgáló-tároló-rdma | 16-04-es lts verzió | legújabb | Batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-adatközpont-smalldisk | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-adatközpont-az-tárolók | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012 – Datacenter-smalldisk | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | legújabb | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1-smalldisk | legújabb | batch.node.windows amd64 |
| OpenLogic | CentOS | 7.4 | legújabb | Batch.node.centos 7 |
| OpenLogic | A HPC-centOS | 7.4 | legújabb | Batch.node.centos 7 |
| OpenLogic | A HPC-centOS | 7.3 | legújabb | Batch.node.centos 7 |
| OpenLogic | A HPC-centOS | 7.1 | legújabb | Batch.node.centos 7 |
| Oracle | Oracle Linux | 7.4 | legújabb | Batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | legújabb | Batch.node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Csatlakozzon SSH használt Linux-csomópontok
A fejlesztés során, vagy a hibaelhárítás során szükség lehet arra a készlet csomópontjain bejelentkezéshez szükséges. Windows számítási csomópontokat, eltérően Remote Desktop Protocol (RDP) nem használható Linux csomópontok való kapcsolódáshoz. Ehelyett a Batch szolgáltatás lehetővé teszi, hogy a távoli kapcsolat minden egyes csomóponton SSH-elérést.

A következő Python kódrészletet felhasználót hoz létre a készletbe, amely pedig szükséges a távoli kapcsolat minden egyes csomóponton. Hogy majd kiírja a secure shell (SSH) minden csomópont-kapcsolódási információt.

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

Itt egy minta kimenet az előző kód készlet, amely négy Linux csomópontokat tartalmazza:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

A jelszó helyett nyilvános SSH-kulcs a felhasználó létrehozásakor, a csomópont is megadhat. A Python SDK használatára a **ssh_public_key** paraméter [ComputeNodeUser][py_computenodeuser]. A .NET, használja a [ComputeNodeUser][net_computenodeuser].[ Az SshPublicKey] [ net_ssh_key] tulajdonság.

## <a name="pricing"></a>Díjszabás
Az Azure Batch Azure felhőalapú szolgáltatásairól és az Azure virtuális gépek technológiára épül. A Batch szolgáltatás tartományregisztráció ingyenesen, ami azt jelenti, hogy van szó, csak a számítási erőforrások, hogy a kötegelt megoldások felhasználását. Ha úgy dönt, **Felhőszolgáltatások konfigurálása**, alapján van szó a [Felhőszolgáltatások árképzési] [ cloud_services_pricing] struktúra. Ha úgy dönt, **virtuálisgép-konfiguráció**, alapján van szó a [virtuális gépek díjszabása] [ vm_pricing] struktúra. 

Ha a központi telepítés a Batch-csomópontokat használja [alkalmazáscsomagok](batch-application-packages.md), van is szó, az Azure Storage-erőforrások, hogy az alkalmazáscsomagok felhasználását. Általában az Azure Storage-költségeket minimálisak. 

## <a name="next-steps"></a>További lépések
### <a name="batch-python-tutorial"></a>Python-útmutató a Batchhez
Részletesebb oktatóanyag használatával kötegelt Python használatával kapcsolatban, tekintse meg [Ismerkedés az Azure Batch Python-ügyfél a](batch-python-tutorial.md). A kiegészítő [kódminta] [ github_samples_pyclient] egy segítő függvényt tartalmaz `get_vm_config_for_distro`, amely bemutatja, hogy egy másik módszer a virtuális gép konfigurációjának.

### <a name="batch-python-code-samples"></a>Kötegelt Python-Kódminták
A [Python Kódminták] [ github_samples_py] a a [azure-köteg-minták] [ github_samples] GitHub tárházából parancsfájlok, amelyek bemutatják, hogyan hajthat végre tartalmaz közös kötegelt műveletek, például a készletbe, a feladat és a feladat létrehozása. A [információs] [ github_py_readme] , amely a Python kísérik minták rendelkezik a szükséges csomagokat telepítésével kapcsolatos adatokat.

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
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
