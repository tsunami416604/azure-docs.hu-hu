---
title: Linux futtatása virtuálisgép-számítási csomópontokon - Azure Batch | Microsoft dokumentumok
description: Ismerje meg, hogyan dolgozhatja fel a párhuzamos számítási számítási számítási feladatokat linuxos virtuális gépek készletein az Azure Batch-ben.
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
ms.openlocfilehash: 977504f41e93e37ae2c5ce9bdb1182a1cfe0a3fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252284"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Linux-számítási csomópontok kiépítése kötegkészletekben

Az Azure Batch használatával párhuzamos számítási számítási feladatok futtatásához linuxos és Windows virtuális gépeken is futtathat. Ez a cikk részletezi, hogyan hozhat létre Linux-számítási csomópontok készleteit a Batch szolgáltatásban a [Batch Python][py_batch_package] és a [Batch .NET][api_net] ügyfélkódtárak használatával.

> [!NOTE]
> Az alkalmazáscsomagok az összes 2017. július 5. után létrehozott Batch-készleten támogatottak. A 2016. március 10. és 2017. július 5. között létrehozott Batch-készletek esetében csak akkor támogatottak, ha a készlet felhőszolgáltatás-konfigurációval lett létrehozva. A 2016. március 10. előtt létrehozott Batch-készletek nem támogatják az alkalmazáscsomagokat. További információkat az alkalmazások a Batch-csomópontokon alkalmazáscsomagok használatával történő központi telepítéséről a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.
>
>

## <a name="virtual-machine-configuration"></a>Virtuális gép konfigurációja
Amikor létrehoz egy számítási csomópontokat a Batch-ben, két lehetőség közül választhatja ki a csomópont méretét és az operációs rendszert: a Felhőszolgáltatások konfigurációja és a virtuálisgép-konfiguráció.

A **Cloud Services-konfiguráció***kizárólag* windowsos számítási csomópontok létrehozására használható. Az elérhető számítási csomópontok méretei a [Felhőszolgáltatások méretei](../cloud-services/cloud-services-sizes-specs.md)között, az elérhető operációs rendszerek pedig az [Azure Guest OS-kiadások és az SDK-kompatibilitási mátrix](../cloud-services/cloud-services-guestos-update-matrix.md)ban találhatók. Amikor olyan készletet hoz létre, amely az Azure Cloud Services-csomópontokat tartalmazza, adja meg a csomópont méretét és az operációs rendszer családját, amelyek a korábban említett cikkekben találhatók. A Windows számítási csomópontok készletei esetében a Cloud Services a leggyakrabban használatos.

**A Virtuálisgép-konfiguráció** Linux és Windows rendszerképeket is biztosít a számítási csomópontokhoz. Az elérhető számítási csomópontméretek az Azure -ban (Linux) lévő [virtuális gépek méretei](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) és az Azure (Windows) virtuális gépek [méretei](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) között vannak felsorolva. Amikor olyan készletet hoz létre, amely virtuálisgép-konfigurációs csomópontokat tartalmaz, meg kell adnia a csomópontok méretét, a virtuálisgép-lemezkép-hivatkozást és a csomópontokra telepítendő Batch-csomópontügynök termékváltozatát.

### <a name="virtual-machine-image-reference"></a>Virtuális gép lemezképének hivatkozása

A Batch szolgáltatás [virtuálisgép-méretezési készleteket](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) használ a számítási csomópontok biztosításához a virtuális gép konfigurációjában. Megadhat egy lemezképet az [Azure Marketplace-en,][vm_marketplace]vagy megadhat egy egyéni lemezképet, amelyet készített. Az egyéni képekről további információt [a Készlet létrehozása a Megosztott képtárral című témakörben talál.](batch-sig-images.md)

Virtuálisgép-lemezkép-hivatkozás konfigurálásakor meg kell adnia a virtuálisgép-lemezkép tulajdonságait. A következő tulajdonságok szükségesek a virtuális gép lemezkép-hivatkozásának létrehozásakor:

| **Képhivatkozási tulajdonságok** | **Példa** |
| --- | --- |
| Közzétevő |Canonical |
| Ajánlat |UbuntuServer |
| SKU |18.04-LTS |
| Verzió |legújabb |

> [!TIP]
> Ezekről a tulajdonságokról és a Marketplace-lemezképek listázásáról a Navigálás funkcióban olvashat bővebben, [és kiválaszthatja a Linux virtuálisgép-lemezképeket az Azure-ban a CLI-vel vagy a PowerShell-lel.](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Vegye figyelembe, hogy jelenleg nem minden Marketplace-rendszerkép kompatibilis a Batch-el. További információ: [Node agent SKU](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Csomópontügynök Termékváltozat
A Batch csomópont ügynök egy olyan program, amely fut minden csomópont a készletben, és biztosítja a parancs-és vezérlő felület között a csomópont és a Batch szolgáltatás. A csomópontügynök, más néven sk-ek különböző implementációi léteznek a különböző operációs rendszerekhez. Lényegében, amikor létrehoz egy virtuális gép konfigurációja, először adja meg a virtuális gép képére hivatkozást, majd adja meg a csomópont ügynök telepíteni a lemezképre. Általában minden csomópont ügynök Termékváltozat kompatibilis több virtuálisgép-lemezképek. Íme néhány példa a csomópontügynök-skus-okra:

* batch.node.ubuntu 18.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Nem minden virtuálisgép-lemezkép érhető el a Marketplace-en kompatibilis a jelenleg elérhető Batch-csomópont ügynökök. A Batch SDK-k segítségével sorolja fel a rendelkezésre álló csomópontügynök-skus-okat és azokat a virtuálisgép-lemezképeket, amelyekkel kompatibilisek. A cikk későbbi részében a [virtuális gép lemezképeinek listáját](#list-of-virtual-machine-images) további információkért és példákból megtudhatja, hogyan olvashatbe érvényes lemezképek listáját futásidőben.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Linux-készlet létrehozása: Batch Python
A következő kódrészlet bemutatja, hogyan használhatja a [Microsoft Azure Batch Client Library for Python][py_batch_package] egy Ubuntu Server számítási csomópontok készletének létrehozásához. A Batch Python-modul referenciadokumentációja megtalálható [az azure.batch csomag][py_batch_docs] a Dokumentumok olvasása.

Ez a kódrészlet explicit módon létrehoz egy [ImageReference ot,][py_imagereference] és meghatározza az egyes tulajdonságait (közzétevő, ajánlat, termékváltozat). Az éles kódban azonban azt javasoljuk, hogy használja a [list_supported_images][py_list_supported_images] módszer segítségével határozza meg, és válasszon a rendelkezésre álló lemezkép és csomópont ügynök Termékváltozat kombinációk futásidőben.

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

Mint korábban említettük, azt javasoljuk, hogy ahelyett, hogy az [ImageReference][py_imagereference] explicit módon, használja a [list_supported_images][py_list_supported_images] módszer dinamikusan válasszon a jelenleg támogatott csomópontügynök/Piactér rendszerkép-kombinációk. A következő Python-kódrészlet bemutatja, hogyan használhatja ezt a módszert.

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
A következő kódrészlet egy példát mutat be arra, hogyan lehet a [Batch .NET][nuget_batch_net] ügyfélkódtár segítségével ubuntu server számítási csomópontok készletét létrehozni. A Batch [.NET referenciadokumentáció][api_net] t a docs.microsoft.com találja.

A következő kódrészlet a [PoolOperations műveletet][net_pool_ops]használja. [ListSupportedImages][net_list_supported_images] metódus kiválasztásához a jelenleg támogatott Marketplace-lemezkép és csomópont ügynök Termékváltozat-kombinációk listájából. Ez a módszer azért kívánatos, mert a támogatott kombinációk listája időről időre változhat. Leggyakrabban támogatott kombinációk kerülnek hozzáadásra.

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

Bár az előző kódrészlet a [PoolOperations műveletet][net_pool_ops]használja. [ListSupportedImages][net_list_supported_images] metódus dinamikusan listát, és válasszon a támogatott lemezkép és csomópont ügynök Termékváltozat kombinációk (ajánlott), akkor is konfigurálhatja [az ImageReference][net_imagereference] explicit módon:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Virtuálisgép-lemezképek listája
A Batch szolgáltatás és a hozzájuk tartozó csomópontügynökök összes támogatott Marketplace virtuálisgép-lemezképeinek listájának beszerzéséhez használja ki a [list_supported_images][py_list_supported_images] (Python), [ListSupportedImages][net_list_supported_images] (Batch .NET) vagy a megfelelő API-t az Ön által választott megfelelő nyelven.

## <a name="connect-to-linux-nodes-using-ssh"></a>Csatlakozás Linux-csomópontokhoz SSH használatával
A fejlesztés során vagy a hibaelhárítás során szükség lehet a készlet csomópontjaira való bejelentkezésre. A Windows számítási csomópontjaival ellentétben a Távoli asztali protokoll (RDP) nem használható Linux-csomópontokhoz való csatlakozáshoz. Ehelyett a Batch szolgáltatás lehetővé teszi az SSH-hozzáférést minden csomóponton a távoli kapcsolathoz.

A következő Python-kódkódrészlet létrehoz egy felhasználót a készlet minden csomópontján, amely a távoli kapcsolathoz szükséges. Ezután kinyomtatja a biztonságos rendszerhéj (SSH) kapcsolatadatait az egyes csomópontokhoz.

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

Az alábbiakban négy Linux-csomópontot tartalmazó készlet előző kódjának kimenete látható:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Jelszó helyett megadhat egy SSH nyilvános kulcsot, amikor létrehoz egy felhasználót egy csomóponton. A Python SDK-ban használja a **ssh_public_key** [paramétert a ComputeNodeUser .][py_computenodeuser] A .NET-ben használja a [ComputeNodeUser][net_computenodeuser]. [SshPublicKey][net_ssh_key] tulajdonság.

## <a name="pricing"></a>Díjszabás
Az Azure Batch az Azure Cloud Services és az Azure Virtual Machines technológiára épül. Maga a Batch szolgáltatás díjmentesen érhető el, ami azt jelenti, hogy csak a Batch-megoldások által felhasznált számítási erőforrásokért (és az ezzel járó kapcsolódó költségekért) számítunk fel díjat. Ha a Cloud Services Configuration lehetőséget **választja,** a [Felhőszolgáltatások díjszabási struktúrája][cloud_services_pricing] alapján számítunk fel díjat. Ha a **Virtuálisgép-konfiguráció lehetőséget választja,** a [virtuális gépek díjszabási struktúrája][vm_pricing] alapján kell fizetnie.

Ha alkalmazásokat telepít a [Batch-csomópontokalkalmazás-csomagok](batch-application-packages.md)használatával, akkor is díjat számítunk fel az Azure Storage-erőforrások, amelyek az alkalmazáscsomagok által használt.

## <a name="next-steps"></a>További lépések

A [Python-kódminták][github_samples_py] a GitHub [azure-batch-samples][github_samples] tárházában parancsfájlokat tartalmaznak, amelyek bemutatják, hogyan hajthatja végre a gyakori Batch-műveleteket, például a készlet, a feladat és a feladat létrehozása. A Python-mintákat kísérő [README][github_py_readme] részletesen ismerteti a szükséges csomagok telepítésének részleteit.

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
[net_list_supported_images]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
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
[py_list_supported_images]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
