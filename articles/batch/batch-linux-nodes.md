---
title: Linux futtatása virtuális gépek számítási csomópontjain
description: Ismerje meg, hogyan dolgozhat párhuzamos számítási feladatait a Azure Batch Linux rendszerű virtuális gépek készletén.
ms.topic: how-to
ms.date: 06/01/2018
ms.custom: H1Hack27Feb2017, devx-track-python
ms.openlocfilehash: bc4c98472609ef906d59159fb31d396071bf6a8d
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853007"
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

A Batch szolgáltatás [virtuálisgép-méretezési csoportokat](../virtual-machine-scale-sets/overview.md) használ a számítási csomópontok biztosításához a virtuális gép konfigurációjában. Megadhat egy rendszerképet az [Azure piactéren][vm_marketplace], vagy megadhat egy előkészített egyéni rendszerképet. További információ az egyéni lemezképekről: [készlet létrehozása a megosztott rendszerkép](batch-sig-images.md)-katalógussal.

A virtuálisgép-rendszerképek hivatkozásának konfigurálásakor meg kell adnia a virtuális gép rendszerképének tulajdonságait. A virtuálisgép-rendszerkép hivatkozásának létrehozásakor a következő tulajdonságokat kell megadnia:

| **Képhivatkozás tulajdonságai** | **Példa** |
| --- | --- |
| Publisher |Canonical |
| Ajánlat |UbuntuServer |
| Termékváltozat |18,04 – LTS |
| Verzió |legújabb |

> [!TIP]
> További információt ezekről a tulajdonságokról és a Piactéri rendszerképek listázásáról és az Azure-beli [linuxos virtuálisgép-rendszerképek kiválasztásáról a CLI vagy a PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)használatával című témakörben talál. Vegye figyelembe, hogy a Piactéri lemezképek jelenleg nem kompatibilisek a Batch szolgáltatással. További információ: [Node Agent SKU](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Csomópont-ügynök SKU
A Batch-csomóponti ügynök egy olyan program, amely a készlet minden egyes csomópontján fut, és a parancs-és vezérlési felületet biztosítja a csomópont és a Batch szolgáltatás között. A csomópont-ügynök különböző implementációkat mutat be különböző operációs rendszerekhez. Alapvetően a virtuálisgép-konfiguráció létrehozásakor először meg kell adnia a virtuális gép rendszerképének hivatkozását, majd meg kell adnia a rendszerképre telepítendő csomópont-ügynököt. Az egyes csomópontok ügynökei általában több virtuálisgép-lemezképtel kompatibilisek. Íme néhány példa a Node Agent-SKU-ra:

* Batch. Node. Ubuntu 18,04
* Batch. Node. CentOS 7
* Batch. Node. Windows amd64

> [!IMPORTANT]
> Nem minden, a piactéren elérhető virtuálisgép-rendszerkép kompatibilis a jelenleg elérhető batch Node-ügynökökkel. A Batch SDK-k segítségével listázhatja az elérhető csomópont-ügynök SKU-kat, valamint azokat a virtuálisgép-lemezképeket, amelyekkel kompatibilisek. A jelen cikk későbbi részében található [virtuálisgép-rendszerképek listájáról](#list-of-virtual-machine-images) további információt és példákat talál az érvényes rendszerképek listájának lekéréséhez futásidőben.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Linux-készlet létrehozása: batch Python
A következő kódrészlet azt mutatja be, hogyan használható a [Python Microsoft Azure batch ügyféloldali kódtára][py_batch_package] az Ubuntu Server számítási csomópontok készletének létrehozásához. A Batch Python-modul dokumentációja [azure.batCH csomagban][py_batch_docs] található a docs olvasásához.

Ez a kódrészlet explicit módon létrehoz egy [ImageReference][py_imagereference] , és meghatározza a hozzá tartozó tulajdonságokat (közzétevő, ajánlat, SKU, verzió). Az éles kódban azonban azt javasoljuk, hogy a [list_supported_images][py_list_supported_images] metódus használatával határozza meg és válassza ki az elérhető rendszerkép-és csomópont-ügynök SKU-kombinációit futásidőben.

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
vm_size = "STANDARD_D2_V3"
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
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Ahogy azt korábban említettük, javasoljuk, hogy explicit módon hozza létre a [ImageReference][py_imagereference] , és a [list_supported_images][py_list_supported_images] metódus használatával dinamikusan válassza ki a jelenleg támogatott csomópont-ügynök/piactér képkombinációt. A következő Python-kódrészlet a módszer használatát mutatja be.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Linux-készlet létrehozása: Batch .NET
A következő kódrészlet azt mutatja be, hogyan használható a [Batch .net][nuget_batch_net] ügyféloldali kódtár az Ubuntu Server számítási csomópontok készletének létrehozásához. A [Batch .net-referenciák dokumentációját][api_net] a docs.microsoft.com találja.

A következő kódrészlet a [PoolOperations][net_pool_ops]használja. [ListSupportedImages][net_list_supported_images] módszer a jelenleg támogatott Piactéri rendszerkép és a csomóponti ügynök SKU-kombinációinak listájából való kiválasztásához. Ez a módszer azért szükséges, mert a támogatott kombinációk listája időről időre változhat. A rendszer leggyakrabban a támogatott kombinációkat adja hozzá.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

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

Bár az előző kódrészlet a [PoolOperations][net_pool_ops]használja. A [ListSupportedImages][net_list_supported_images] metódust dinamikusan listázhatja és kiválaszthatja a támogatott rendszerkép és a csomópont-ügynök SKU-kombinációi közül (ajánlott), emellett explicit módon is konfigurálhat egy [ImageReference][net_imagereference] :

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Virtuálisgép-lemezképek listája
A Batch szolgáltatás és a hozzájuk tartozó csomópont-ügynökök által támogatott Piactéri virtuálisgép-rendszerképek listájának beszerzéséhez használja a [list_supported_images][py_list_supported_images] (Python), a [ListSupportedImages][net_list_supported_images] (Batch .net) vagy a megfelelő API-t a választott nyelvi SDK-ban.

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
Azure Batch az Azure Cloud Servicesra és az Azure Virtual Machines technológiára épül. Maga a Batch szolgáltatás díjmentesen érhető el, ami azt jelenti, hogy kizárólag a Batch-megoldások által felhasznált számítási erőforrások (és a hozzájuk kapcsolódó költségek) után kell fizetni. Ha **Cloud Services konfigurációt**választ, a [Cloud Services díjszabási][cloud_services_pricing] struktúra alapján kell fizetnie. A **virtuális gép konfigurációjának**kiválasztásakor a [Virtual Machines díjszabási][vm_pricing] struktúra alapján kell fizetnie.

Ha [alkalmazáscsomag](batch-application-packages.md)használatával helyezi üzembe az alkalmazásokat a Batch-csomópontokon, akkor az alkalmazáscsomag által felhasznált Azure Storage-erőforrásokért is fizetnie kell.

## <a name="next-steps"></a>Következő lépések

A GitHubon az [Azure-batch-Samples][github_samples] adattárában található [Python-kód][github_samples_py] olyan parancsfájlokat tartalmaz, amelyek bemutatják, hogyan hajtható végre az általános batch-műveletek, például a készlet, a feladat és a feladat létrehozása. A Python-mintákhoz tartozó [readme][github_py_readme] tartalmazza a szükséges csomagok telepítésének részleteit.

[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_mgmt]: /dotnet/api/overview/azure/batch
[api_rest]: /rest/api/batchservice/
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: /dotnet/api/microsoft.azure.batch.imagereference
[net_list_supported_images]: /dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
[net_pool_ops]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: /rest/api/batchservice/pool/add
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_supported_images]: /python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
