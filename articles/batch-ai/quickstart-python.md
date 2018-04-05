---
title: Azure rövid útmutató – CNTK-betanítás a Batch AI és a Python használatával | Microsoft Docs
description: Rövid áttekintést kaphat arról, hogyan futtathat CNTK-betanítási feladatokat a Batch AI és a Python SDK használatával
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: f535c9adf4926f29ae9cade6382debedab73937d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>CNTK-betanítási feladatok futtatása az Azure Python SDK-val

Ez a rövid útmutató részletesen bemutatja, hogyan futtatható az Azure Python SDK használatával egy Microsoft Cognitive Toolkit (CNTK) betanítási feladat a Batch AI-szolgáltatással. 

Ebben a példában a kézírásos rendszerképek MNIST-adatbázisát használja a konvolúciós neurális hálózat (CNN) betanításához egy egycsomópontos GPU-fürtön. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

* Azure Python SDK – lásd a [telepítési utasításokat](/python/azure/python-sdk-azure-install)

* Azure Storage-fiók – lásd: [Azure Storage-fiók létrehozása](../storage/common/storage-create-storage-account.md)

* Az Azure Active Directory-szolgáltatásnév hitelesítő adatai – lásd: [Szolgáltatásnév létrehozása a parancssori felülettel](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

* Egyszer regisztrálja a Batch AI erőforrás-szolgáltatót az előfizetésben az Azure Cloud Shell vagy az Azure CLI használatával. A szolgáltató regisztrációja akár 15 percet is igénybe vehet.

  ```azurecli
  az provider register -n Microsoft.BatchAI
  az provider register -n Microsoft.Batch
  ```


## <a name="configure-credentials"></a>Hitelesítő adatok konfigurálása
Hozza létre ezeket a paramétereket a Python-szkriptben a saját értékei behelyettesítésével:

```Python
# credentials used for authentication
client_id = 'my_aad_client_id'
secret = 'my_aad_secret_key'
token_uri = 'my_aad_token_uri'
subscription_id = 'my_subscription_id'

# credentials used for storage
storage_account_name = 'my_storage_account_name'
storage_account_key = 'my_storage_account_key'

# specify the credentials used to remote login your GPU node
admin_user_name = 'my_admin_user_name'
admin_user_password = 'my_admin_user_password'
```

Az ajánlott eljárások szerint minden hitelesítő adatot egy külön konfigurációs fájlban kell tárolni, ami ebben a példában nem látható. Tekintse meg a [módszereket](https://github.com/Azure/BatchAI/tree/master/recipes) a konfigurációs fájl létrehozásához. 

## <a name="authenticate-with-batch-ai"></a>Hitelesítés a Batch AI segítségével

Az Azure Batch AI eléréséhez hitelesítenie kell az Azure Active Directory használatával. Alul található a kód a szolgáltatással való hitelesítés (hozzon létre egy `BatchAIManagementClient` objektumot) a szolgáltatásnév hitelesítő adatainak és az előfizetés azonosítójának használatával:

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=client_id, secret=secret, token_uri=token_uri)

batchai_client = batchai.BatchAIManagementClient(credentials=creds,
                                         subscription_id=subscription_id
)
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

A Batch AI-fürtök és feladatok Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az alábbi kódrészlet létrehoz egy erőforráscsoportot:

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'

resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)

resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>Azure-fájlmegosztás előkészítése
Illusztrációs célok esetében ez a rövid útmutató az Azure-fájlmegosztást használja a betanítási feladatok betanítási adatainak és szkriptjeinek tárolására. 

1. Hozzon létre egy *batchaiquickstart* nevű fájlmegosztást.

  ```Python
  from azure.storage.file import FileService 
 
  azure_file_share_name = 'batchaiquickstart' 
 
  service = FileService(storage_account_name, storage_account_key) 
 
  service.create_share(azure_file_share_name, fail_on_exist=False)
  ``` 
 
2. Hozzon létre egy *mnistcntksample* könyvtárat a megosztásban 

  ```Python
  mnist_dataset_directory = 'mnistcntksample' 
 
  service.create_directory(azure_file_share_name, mnist_dataset_directory, fail_on_exist=False) 
  ```
3. Töltse le a [mintacsomagot](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D), és bontsa ki. Töltse fel a tartalmát abba a könyvtárba, amelyben a Python-szkriptet fogja végrehajtani.

  ```Python
  for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt', 'ConvNet_MNIST.py']:     
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f) 
  ```

## <a name="create-gpu-cluster"></a>GPU-fürt létrehozása

Hozzon létre egy Batch AI-fürtöt. A példában a fürt egyetlen STANDARD_NC6 virtuálisgép-csomópontból áll. Ez a virtuálisgép-méret egy NVIDIA K80 GPU-val rendelkezik. Csatlakoztassa a fájlmegosztást egy *azurefileshare* nevű mappához. A mappa teljes elérési útja a GPU számítási csomóponton: $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare' 
 
parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus', 
 
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6', 
 
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
         admin_user_name=admin_user_name,
         admin_user_password=admin_user_password), 
 
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
         manual=models.ManualScaleSettings(target_node_count=1)
     ), 
 
    # Configure each node in the cluster
    node_setup=models.NodeSetup( 
 
        # Mount shared volumes to the host
         mount_volumes=models.MountVolumes(
             azure_file_shares=[
                 models.AzureFileShareReference(
                     account_name=storage_account_name,
                     credentials=models.AzureStorageCredentialsInfo(
         account_key=storage_account_key),
         azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
               storage_account_name, azure_file_share_name),
                  relative_mount_path = relative_mount_point)],
         ), 
    ), 
) 
batchai_client.clusters.create(resource_group_name, cluster_name, parameters).result() 
```

## <a name="get-cluster-status"></a>A fürt állapotának lekérése

A fürt állapotát az alábbi paranccsal monitorozhatja: 

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count)) 
```

Az előző kód alapszintű fürtlefoglalási információkat jelenít meg az alábbiakhoz hasonlóan:

```Shell
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving 0
 
```  

A fürt akkor áll készen, amikor a csomópontok le lettek foglalva és befejeződött az előkészítés (lásd a `nodeStateCounts` attribútumot). Ha hiba történt, akkor az `errors` attribútum tartalmazza a hiba leírását.

## <a name="create-training-job"></a>Betanítási feladat létrehozása

Miután a fürt elkészült, konfigurálja és küldje el a képzési feladatot. 

```Python
job_name = 'myjob' 
 
parameters = models.job_create_parameters.JobCreateParameters( 
 
     # Location where the job will run
     # Ideally this should be co-located with the cluster.
     location='eastus', 
 
     # The cluster this job will run on
     cluster=models.ResourceId(cluster.id), 
 
     # The number of VMs in the cluster to use
     node_count=1, 
 
     # Override the path where the std out and std err files will be written to.
     # In this case we will write these out to an Azure Files share
     std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point), 
 
     input_directories=[models.InputDirectory(
         id='SAMPLE',
         path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(relative_mount_point, mnist_dataset_directory))], 
 
     # Specify directories where files will get written to 
     output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")], 
 
     # Container configuration
     container_settings=models.ContainerSettings(
        models.ImageSourceRegistry(image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')), 
 
     # Toolkit specific settings
     cntk_settings = models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
 ) 
 
# Create the job 
batchai_client.jobs.create(resource_group_name, job_name, parameters).result() 
```

## <a name="monitor-job"></a>Feladat monitorozása
A feladat állapotát az alábbi paranccsal vizsgálhatja meg: 
 
```Python
job = batchai_client.jobs.get(resource_group_name, job_name) 
 
print('Job state: {0} '.format(job.execution_state.name))
```

Az eredmény ehhez hasonlóan fog kinézni: `Job state: running`.

Az `executionState` tartalmazza a feladat jelenlegi végrehajtási állapotát:
* `queued`: a feladat arra vár, hogy fürtcsomópontok elérhetők legyenek
* `running`: a feladat fut
* `succeeded` (vagy `failed`) : a feladat befejeződött és az `executionInfo` részleteket tartalmaz az eredményről
 
## <a name="list-stdout-and-stderr-output"></a>Az stdout és az stderr kimenetek listázása
Használja az alábbi parancsot az stdout és az stderr naplófájlokhoz tartozó hivatkozások listázásához:

```Python
files = batchai_client.jobs.list_output_files(resource_group_name, job_name, models.JobsListOutputFilesOptions("stdouterr")) 
 
for file in list(files):
     print('file: {0}, download url: {1}'.format(file.name, file.download_url)) 
```
## <a name="delete-resources"></a>Erőforrások törlése

Használja az alábbi parancsot a feladat törléséhez:
```Python
batchai_client.jobs.delete(resource_group_name, job_name) 
```

Használja az alábbi parancsot a fürt törléséhez:
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```
## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megismerhette, hogyan futtathat CNTK-betanítási feladatot egy Batch AI-fürtön az Azure Python SDK használatával. Ha többet szeretne tudni a Batch AI különböző eszközkészletekkel történő használatáról, tekintse meg a [betanítási módszereket](https://github.com/Azure/BatchAI).
