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
ms.openlocfilehash: da5c1181f9c4d311bdeabe837435ae4e0eb3dc1a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
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
```

## <a name="configure-credentials"></a>Hitelesítő adatok konfigurálása
Adja hozzá az alábbi kódot a parancsfájljához, és cserélje a `FILL-IN-HERE` értéket a megfelelő értékekre:

```Python
# credentials used for authentication
aad_client_id = 'FILL-IN-HERE'
aad_secret = 'FILL-IN-HERE'
aad_tenant = 'FILL-IN-HERE'
subscription_id = 'FILL-IN-HERE'

# credentials used for storage
storage_account_name = 'FILL-IN-HERE'
storage_account_key = 'FILL-IN-HERE'

# specify the credentials used to remote login your GPU node
admin_user_name = 'FILL-IN-HERE'
admin_user_password = 'FILL-IN-HERE'
```

Ne feledje, hogy a hitelesítő adatok forráskódba való foglalása nem ajánlott gyakorlat, és csak azért alkalmazzuk itt, hogy egyszerűbb legyen az útmutató.
Javasoljuk, hogy használjon inkább környezeti változókat vagy különálló konfigurációs fájlt.

## <a name="create-batch-ai-client"></a>Batch AI-ügyfél létrehozása

Az alábbi kód létrehoz egy szolgáltatáshitelesítési objektumot és egy Batch AI-ügyfelet:

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=aad_client_id, secret=aad_secret, tenant=aad_tenant)

batchai_client = batchai.BatchAIManagementClient(
    credentials=creds, subscription_id=subscription_id)
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
Ez az útmutató szemléltetésképpen Azure-fájlmegosztást használ a betanítási feladathoz tartozó betanítási adatok és szkriptek tárolásához.

1. Hozzon létre egy `batchaiquickstart` nevű fájlmegosztást.

```Python
from azure.storage.file import FileService
azure_file_share_name = 'batchaiquickstart'
service = FileService(storage_account_name, storage_account_key)
service.create_share(azure_file_share_name, fail_on_exist=False)
```

2. Hozzon létre egy `mnistcntksample` nevű könyvtárat a megosztásban.

```Python
mnist_dataset_directory = 'mnistcntksample'
service.create_directory(azure_file_share_name, mnist_dataset_directory,
                         fail_on_exist=False)
```
3. Töltse le a [mintacsomagot](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D), és bontsa ki ebbe a könyvtárba. Az alábbi kód feltölti a szükséges fájlokat az Azure-fájlmegosztásba:

```Python
for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt',
          'ConvNet_MNIST.py']:
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f)
```

## <a name="create-gpu-cluster"></a>GPU-fürt létrehozása

Hozzon létre egy Batch AI-fürtöt. A példában a fürt egyetlen STANDARD_NC6 virtuálisgép-csomópontból áll. Ez a virtuálisgép-méret egy NVIDIA K80 GPU-val rendelkezik. Csatlakoztassa a fájlmegosztást egy `azurefileshare` nevű könyvtárként. A mappa teljes elérési útja a GPU számítási csomóponton: `$AZ_BATCHAI_MOUNT_ROOT/azurefileshare`.

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
                    azure_file_url='https://{0}/{1}'.format(
                        service.primary_endpoint, azure_file_share_name),
                    relative_mount_path=relative_mount_point)],
        ),
    ),
)
batchai_client.clusters.create(resource_group_name, cluster_name,
                               parameters).result()
```

## <a name="get-cluster-status"></a>A fürt állapotának lekérése

A fürt állapotát az alábbi paranccsal monitorozhatja:

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; Leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count))
```

Az előző kód az alábbihoz hasonló egyszerű fürtlefoglalási információkat jelenít meg:

```
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving: 0
```

A fürt akkor áll készen, amikor a csomópontok le lettek foglalva és befejeződött az előkészítés (lásd a `nodeStateCounts` attribútumot). Ha hiba történt, akkor az `errors` attribútum tartalmazza a hiba leírását.

## <a name="create-training-job"></a>Betanítási feladat létrehozása

Miután a fürt elkészült, konfigurálja és küldje el a betanítási feladatot:

```Python
job_name = 'myjob'

parameters = models.job_create_parameters.JobCreateParameters(
    # The job and cluster must be created in the same location
    location=cluster.location,
    # The cluster this job will run on
    cluster=models.ResourceId(id=cluster.id),
    # The number of VMs in the cluster to use
    node_count=1,
    # Write job's standard output and execution log to Azure File Share
    std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(
        relative_mount_point),
    # Configure location of the training script and MNIST dataset
    input_directories=[models.InputDirectory(
        id='SAMPLE',
        path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(
            relative_mount_point, mnist_dataset_directory))],
    # Specify location where generated model will be stored
    output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")],
    # Container configuration
    container_settings=models.ContainerSettings(
        image_source_registry=models.ImageSourceRegistry(
            image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')),
    # Toolkit specific settings
    cntk_settings=models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
)

# Create the job
batchai_client.jobs.create(resource_group_name, job_name, parameters).result()
```

## <a name="monitor-job"></a>Feladat monitorozása
A feladat állapotát az alábbi kóddal ellenőrizheti:

```Python
job = batchai_client.jobs.get(resource_group_name, job_name)

print('Job state: {0} '.format(job.execution_state.name))
```

Az eredmény ehhez hasonlóan fog kinézni: `Job state: running`.

Az `executionState` tartalmazza a feladat jelenlegi végrehajtási állapotát:
* `queued`: a feladat arra vár, hogy fürtcsomópontok elérhetők legyenek
* `running`: a feladat fut
* `succeeded` (vagy `failed`) : a feladat befejeződött, és annak eredményét az `executionInfo` tartalmazza

## <a name="list-stdout-and-stderr-output"></a>Az stdout és az stderr kimenetek listázása
Listázza a létrehozott stdout-, stderr- és naplófájlokat az alábbi kóddal:

```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="stdouterr"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="list-generated-model-files"></a>Létrehozott modellfájlok listázása
Listázza a létrehozott modellfájlokat az alábbi kóddal:
```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="MODEL"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="delete-resources"></a>Erőforrások törlése

Törölje a feladatot az alábbi kóddal:
```Python
batchai_client.jobs.delete(resource_group_name, job_name)
```

Törölje a fürtöt az alábbi kóddal:
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```

Törölje az összes lefoglalt erőforrást az alábbi kóddal:
```Python
resource_management_client.resource_groups.delete('myresourcegroup')
```
## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megismerhette, hogyan futtathat CNTK-betanítási feladatot egy Batch AI-fürtön az Azure Python SDK használatával. Ha többet szeretne tudni a Batch AI különböző eszközkészletekkel történő használatáról, tekintse meg a [betanítási módszereket](https://github.com/Azure/BatchAI).
