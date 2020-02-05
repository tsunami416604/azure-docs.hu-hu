---
title: Linux futtatása virtuális gépek számítási csomópontjain – Azure Batch | Microsoft Docs
description: Ismerje meg, hogyan dolgozhat párhuzamos számítási feladatait a Azure Batch Linux rendszerű virtuális gépek készletén.
services: batch
documentationcenter: python
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3691790b2e47ef43c6742fa912aff8d7777900f8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023701"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Linuxos számítási csomópontok kiépítése a Batch-készletekben

A Azure Batch használatával párhuzamos számítási feladatokat futtathat Linux és Windows rendszerű virtuális gépeken is. Ez a cikk részletesen ismerteti, hogyan hozhat létre Linux számítási csomópontokat a Batch szolgáltatásban a [Batch Python][py_batch_package] és a [Batch .net][api_net] ügyféloldali kódtárak használatával.

> [!NOTE]
> Az alkalmazáscsomagok az összes 2017. július 5. után létrehozott Batch-készleten támogatottak. A 2016. március 10. és 2017. július 5. között létrehozott Batch-készletek esetében csak akkor támogatottak, ha a készlet felhőszolgáltatás-konfigurációval lett létrehozva. A 2016. március 10. előtt létrehozott Batch-készletek nem támogatják az alkalmazáscsomagokat. További információkat az alkalmazások a Batch-csomópontokon alkalmazáscsomagok használatával történő központi telepítéséről a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.
>
>

## <a name="virtual-machine-configuration"></a>Virtuális gép konfigurációja
Amikor számítási csomópontok készletét hozza létre a Batchben, két lehetőség közül választhat, amelyekből kiválaszthatja a csomópont méretét és operációs rendszerét: Cloud Services konfigurációt és a virtuális gép konfigurációját.

A **Cloud Services-konfiguráció***kizárólag* windowsos számítási csomópontok létrehozására használható. Az elérhető számítási csomópontok méretei a [Cloud Services méretében](../cloud-services/cloud-services-sizes-specs.md)vannak felsorolva, és a rendelkezésre álló operációs rendszerek az [Azure vendég operációs rendszer kiadásaiban és az SDK-kompatibilitási mátrixban](../cloud-services/cloud-services-guestos-update-matrix.md)szerepelnek. Ha Azure Cloud Services csomópontokat tartalmazó készletet hoz létre, akkor a csomópont méretét és az operációsrendszer-családot kell megadnia, amelyek a korábban említett cikkekben vannak leírva. A Windows számítási csomópontok készletei esetében a Cloud Services leggyakrabban használatos.

A **virtuálisgép-konfiguráció** Linux-és Windows-rendszerképeket is biztosít a számítási csomópontokhoz. Az elérhető számítási csomópontok méretei az Azure-beli [virtuális gépek](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) és az Azure-ban (Windows) lévő [virtuális gépek méretei](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) szerint vannak felsorolva. Ha virtuális gépek konfigurációs csomópontjait tartalmazó készletet hoz létre, meg kell adnia a csomópontok méretét, a virtuális gép képhivatkozását és a csomópontokra telepítendő batch-csomóponti ügynök SKU-t.

### <a name="virtual-machine-image-reference"></a>Virtuális gép képének referenciája

A Batch szolgáltatás [virtuálisgép-méretezési csoportokat](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) használ a számítási csomópontok biztosításához a virtuális gép konfigurációjában. Megadhat egy rendszerképet az [Azure piactéren][vm_marketplace], vagy megadhat egy előkészített egyéni rendszerképet. További információ az egyéni lemezképekről: [készlet létrehozása a megosztott rendszerkép](batch-sig-images.md)-katalógussal.

A virtuálisgép-rendszerképek hivatkozásának konfigurálásakor meg kell adnia a virtuális gép rendszerképének tulajdonságait. A virtuálisgép-rendszerkép hivatkozásának létrehozásakor a következő tulajdonságokat kell megadnia:

| **Képhivatkozás tulajdonságai** | **Példa** |
| --- | --- |
| Gyártó/kiadó |Canonical |
| Ajánlat |UbuntuServer |
| SKU (Cikkszám) |14.04.4-LTS |
| Verzió |legutóbbi |

> [!TIP]
> További információt ezekről a tulajdonságokról és a Piactéri rendszerképek listázásáról és az Azure-beli [linuxos virtuálisgép-rendszerképek kiválasztásáról a CLI vagy a PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)használatával című témakörben talál. Vegye figyelembe, hogy a Piactéri lemezképek jelenleg nem kompatibilisek a Batch szolgáltatással. További információ: [Node Agent SKU](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Csomópont-ügynök SKU
A Batch-csomóponti ügynök egy olyan program, amely a készlet minden egyes csomópontján fut, és a parancs-és vezérlési felületet biztosítja a csomópont és a Batch szolgáltatás között. A csomópont-ügynök különböző implementációkat mutat be különböző operációs rendszerekhez. Alapvetően a virtuálisgép-konfiguráció létrehozásakor először meg kell adnia a virtuális gép rendszerképének hivatkozását, majd meg kell adnia a rendszerképre telepítendő csomópont-ügynököt. Az egyes csomópontok ügynökei általában több virtuálisgép-lemezképtel kompatibilisek. Íme néhány példa a Node Agent-SKU-ra:

* Batch. Node. Ubuntu 14,04
* Batch. Node. CentOS 7
* Batch. Node. Windows amd64

> [!IMPORTANT]
> Nem minden, a piactéren elérhető virtuálisgép-rendszerkép kompatibilis a jelenleg elérhető batch Node-ügynökökkel. A Batch SDK-k segítségével listázhatja az elérhető csomópont-ügynök SKU-kat, valamint azokat a virtuálisgép-lemezképeket, amelyekkel kompatibilisek. A jelen cikk későbbi részében található [virtuálisgép-rendszerképek listájáról](#list-of-virtual-machine-images) további információt és példákat talál az érvényes rendszerképek listájának lekéréséhez futásidőben.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Linux-készlet létrehozása: batch Python
A következő kódrészlet azt mutatja be, hogyan használható a [Python Microsoft Azure batch ügyféloldali kódtára][py_batch_package] az Ubuntu Server számítási csomópontok készletének létrehozásához. A Batch Python modul dokumentációja az [Azure. batch csomagban][py_batch_docs] található a docs beolvasásához.

Ez a kódrészlet explicit módon létrehoz egy [ImageReference][py_imagereference] , és meghatározza a hozzá tartozó tulajdonságokat (közzétevő, ajánlat, SKU, verzió). Az éles kódban azonban azt javasoljuk, hogy a [list_node_agent_skus][py_list_skus] metódus használatával határozza meg és válassza ki az elérhető rendszerkép-és csomópont-ügynök SKU-kombinációit futásidőben.

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
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="14.04.2-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Ahogy azt korábban említettük, javasoljuk, hogy explicit módon hozza létre a [ImageReference][py_imagereference] , és a [list_node_agent_skus][py_list_skus] metódus használatával dinamikusan válassza ki a jelenleg támogatott csomópont-ügynök/piactér képkombinációt. A következő Python-kódrészlet a módszer használatát mutatja be.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(
    agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id=ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Linux-készlet létrehozása: Batch .NET
A következő kódrészlet azt mutatja be, hogyan használható a [Batch .net][nuget_batch_net] ügyféloldali kódtár az Ubuntu Server számítási csomópontok készletének létrehozásához. A [Batch .net-referenciák dokumentációját][api_net] a docs.microsoft.com találja.

A következő kódrészlet a [PoolOperations][net_pool_ops]használja. [ListNodeAgentSkus][net_list_skus] módszer a jelenleg támogatott Piactéri rendszerkép és a csomóponti ügynök SKU-kombinációinak listájából való kiválasztásához. Ez a módszer azért szükséges, mert a támogatott kombinációk listája időről időre változhat. A rendszer leggyakrabban a támogatott kombinációkat adja hozzá.

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

Bár az előző kódrészlet a [PoolOperations][net_pool_ops]használja. A [ListNodeAgentSkus][net_list_skus] metódust dinamikusan listázhatja és kiválaszthatja a támogatott rendszerkép és a csomópont-ügynök SKU-kombinációi közül (ajánlott), emellett explicit módon is konfigurálhat egy [ImageReference][net_imagereference] :

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Virtuálisgép-lemezképek listája
A következő táblázat felsorolja azokat a Piactéri virtuálisgép-rendszerképeket, amelyek a cikk utolsó frissítésekor a rendelkezésre álló batch-csomóponti ügynökökkel kompatibilisek. Fontos megjegyezni, hogy ez a lista nem végleges, mert a képek és a csomópont-ügynökök bármikor hozzáadhatók vagy eltávolíthatók. Javasoljuk, hogy a Batch-alkalmazások és-szolgáltatások mindig a [list_node_agent_skus][py_list_skus] (Python) vagy a [ListNodeAgentSkus][net_list_skus] (Batch .net) szolgáltatást használják az aktuálisan elérhető SKU-i meghatározásához és kiválasztásához.

> [!WARNING]
> A következő lista bármikor megváltozhat. Mindig használja a Batch API-k **List csomópont-ügynök SKU** metódusait a Batch-feladatok futtatásakor a kompatibilis virtuális gép és a csomópont-ügynök SKU-jának listázásához.
>
>

| **Közzétevő** | **Ajánlat** | **Rendszerkép SKU** | **Verzió** | **Csomóponti ügynök SKU-azonosítója** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| batch | renderelés – centos73 | renderelési | legutóbbi | Batch. Node. CentOS 7 |
| batch | renderelés – windows2016 | renderelési | legutóbbi | Batch. Node. Windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | legutóbbi | Batch. Node. Ubuntu 16,04 |
| Canonical | UbuntuServer | 14.04.5-LTS | legutóbbi | Batch. Node. Ubuntu 14,04 |
| Credativ | Debian | 9 | legutóbbi | Batch. Node. Debian 9 |
| Credativ | Debian | 8 | legutóbbi | Batch. Node. Debian 8 |
| Microsoft – ADS | Linux – adattudomány – virtuális gép | linuxdsvm | legutóbbi | Batch. Node. CentOS 7 |
| Microsoft – ADS | Standard – adattudomány – virtuális gép | Standard – adattudomány – virtuális gép | legutóbbi | Batch. Node. Windows amd64 |
| Microsoft-Azure-batch | CentOS – tároló | 7-4 | legutóbbi | Batch. Node. CentOS 7 |
| Microsoft-Azure-batch | CentOS-Container-RDMA | 7-4 | legutóbbi | Batch. Node. CentOS 7 |
| Microsoft-Azure-batch | Ubuntu – kiszolgáló – tároló | 16-04 – LTS | legutóbbi | Batch. Node. Ubuntu 16,04 |
| Microsoft-Azure-batch | Ubuntu-Server-Container-RDMA | 16-04 – LTS | legutóbbi | Batch. Node. Ubuntu 16,04 |
| MicrosoftWindowsServer | WindowsServer | 2016 – Datacenter | legutóbbi | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016 – Datacenter – smalldisk | legutóbbi | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016 – Datacenter – tárolók | legutóbbi | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | legutóbbi | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | legutóbbi | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | legutóbbi | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012 – Datacenter – smalldisk | legutóbbi | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008 – R2 – SP1 | legutóbbi | Batch. Node. Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008 – R2 – SP1 – smalldisk | legutóbbi | Batch. Node. Windows amd64 |
| OpenLogic | CentOS | 7,4 | legutóbbi | Batch. Node. CentOS 7 |
| OpenLogic | CentOS – HPC | 7,4 | legutóbbi | Batch. Node. CentOS 7 |
| OpenLogic | CentOS – HPC | 7.3 | legutóbbi | Batch. Node. CentOS 7 |
| OpenLogic | CentOS – HPC | 7.1 | legutóbbi | Batch. Node. CentOS 7 |
| Oracle | Oracle – Linux | 7,4 | legutóbbi | Batch. Node. CentOS 7 |
| SUSE | SLES – HPC | 12 – SP2 | legutóbbi | Batch. Node. openSUSE 42,1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Kapcsolódás Linux-csomópontokhoz SSH használatával
A fejlesztés során vagy a hibaelhárítás során előfordulhat, hogy be kell jelentkeznie a készletben lévő csomópontokra. A Windows számítási csomópontjaitól eltérően az RDP protokoll (RDP) nem használható Linux-csomópontokhoz való kapcsolódáshoz. Ehelyett a Batch szolgáltatás engedélyezi az SSH-hozzáférést az egyes csomópontokon a távoli kapcsolathoz.

A következő Python-kódrészlet létrehoz egy felhasználót a készlet minden egyes csomópontján, amely a távoli kapcsolatok esetében szükséges. Ezután kinyomtatja a Secure Shell-(SSH-) kapcsolatok adatait az egyes csomópontokhoz.

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

Itt látható egy négy Linux-csomópontot tartalmazó készlet előző kódjának kimenete:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Jelszó helyett egy nyilvános SSH-kulcsot is megadhat, amikor felhasználót hoz létre egy csomóponton. A Python SDK-ban használja a **ssh_public_key** paramétert a [ComputeNodeUser][py_computenodeuser]. A .NET-ben használja a [ComputeNodeUser][net_computenodeuser]. [SshPublicKey][net_ssh_key] tulajdonság.

## <a name="pricing"></a>Díjszabás
Azure Batch az Azure Cloud Servicesra és az Azure Virtual Machines technológiára épül. Maga a Batch szolgáltatás díjmentesen elérhető, ami azt jelenti, hogy csak a Batch-megoldások által felhasznált számítási erőforrásokért kell fizetnie. Ha **Cloud Services konfigurációt**választ, a [Cloud Services díjszabási][cloud_services_pricing] struktúra alapján kell fizetnie. A **virtuális gép konfigurációjának**kiválasztásakor a [Virtual Machines díjszabási][vm_pricing] struktúra alapján kell fizetnie. 

Ha [alkalmazáscsomag](batch-application-packages.md)használatával helyezi üzembe az alkalmazásokat a Batch-csomópontokon, akkor az alkalmazáscsomag által felhasznált Azure Storage-erőforrásokért is fizetnie kell. Általánosságban elmondható, hogy az Azure Storage-költségek minimálisak. 

## <a name="next-steps"></a>Következő lépések

A GitHubon az [Azure-batch-Samples][github_samples] adattárában található [Python-kód][github_samples_py] olyan parancsfájlokat tartalmaz, amelyek bemutatják, hogyan hajtható végre az általános batch-műveletek, például a készlet, a feladat és a feladat létrehozása. A Python-mintákhoz tartozó [readme][github_py_readme] tartalmazza a szükséges csomagok telepítésének részleteit.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
