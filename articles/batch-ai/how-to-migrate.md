---
title: Áttelepíteni a Batch AI az Azure Machine Learning szolgáltatás
description: Ismerje meg, hogyan telepíthet át az Azure Machine Learning szolgáltatáshoz a AMLcompute és a kód hogyan képezi le az Azure Machine Learning szolgáltatásból kód.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: 5bc01ad6811cc5661d74770ce36281c9acba8912
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57196263"
---
# <a name="migrating-from-batch-ai-to-azure-machine-learning-service"></a>Batch AI-ről az Azure Machine Learning szolgáltatáshoz

**Az Azure Batch AI szolgáltatás márciusi kivonás alatt áll.** Az ipari méretekben tanítási és pontozási a Batch AI képességeit már elérhetők [Azure Machine Learning szolgáltatás](../machine-learning/service/overview-what-is-azure-ml.md), amely óta általánosan elérhető 2018. December 4.

Sok más gépi tanulási funkciókat, valamint az Azure Machine Learning szolgáltatás tartalmaz egy felhőalapú, felügyelt számítási célt képzés, üzembe helyezése és pontozás a machine learning-modellek. A számítási célnak nevezzük [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Indítsa el a migrálást, és jelenleg is használó](#migrate). Az Azure Machine Learning szolgáltatás segítségével kezelheti a [Python SDK-k](../machine-learning/service/quickstart-create-workspace-with-python.md), parancssori felületet, és a [az Azure portal](../machine-learning/service/quickstart-get-started.md).

Az előzetes verzió a Batch AI verzióról a GA'ed Azure Machine Learning szolgáltatáshoz biztosít révén könnyebben használható, például Estimators és adattárainak fogalmakat jobb felhasználói élményt. Általánosan elérhető Azure-szolgáltatási szintű SLA-k és az ügyfél-támogatási is garantálja.

Az Azure Machine Learning szolgáltatás is teszi elérhetővé az új funkciók például automatizált, machine learning, a hiperparaméter finomhangolása és a gépi Tanulási folyamatok, amelyek hasznosak lehetnek a legtöbb nagy léptékű AI-számítási. Egy különálló szolgáltatás közötti váltás nélkül helyezheti üzembe az alkalmazásokat a betanított modell segítségével végezze el a data science hurok az adatok (a Data Prep SDK-val) egészen operacionalizálás és a modell figyelése.

## <a name="start-migrating"></a>A migrálás megkezdése
Az alkalmazások a fennakadások elkerülése érdekében, és kihasználhatják a legújabb funkciók, a következő lépésekkel 2019. március 31. előtt:

1. Hozzon létre egy Azure Machine Learning szolgáltatás munkaterületet, és első lépések:
    + [Python-alapú a rövid útmutató](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Az Azure portal alapján rövid útmutatójában](../machine-learning/service/quickstart-get-started.md)

1. Telepítse a [az Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) és [adat-előkészítési SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install). 

1. Állítsa be egy [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) modell betanítása.

1. A parancsfájlok használata az Azure Machine Learning Compute frissítése. A következő szakaszok bemutatják, hogyan közös kód, az Azure Machine Learning Batch AI Maps kódot használja. 


## <a name="create-workspaces"></a>Munkaterületek létrehozása
Egy configuration.json használata az Azure Batch AI munkaterület inicializálása fogalma hasonlóképpen leképez egy konfigurációs fájlban, az Azure Machine Learning szolgáltatás használatával.

A **Batch AI**, így kész is:

```python
sys.path.append('../../..')
import utilities as utils

cfg = utils.config.Configuration('../../configuration.json')
client = utils.config.create_batchai_client(cfg)

utils.config.create_resource_group(cfg)
_ = client.workspaces.create(cfg.resource_group, cfg.workspace, cfg.location).result()
```

**Az Azure Machine Learning szolgáltatás**, próbálja ki:

```python
from azureml.core.workspace import Workspace

ws = Workspace.from_config()
print('Workspace name: ' + ws.name, 
      'Azure region: ' + ws.location, 
      'Subscription id: ' + ws.subscription_id, 
      'Resource group: ' + ws.resource_group, sep = '\n')
```

Emellett is létrehozhat munkaterület közvetlenül a konfigurációs paraméterek megadásával

```python
from azureml.core import Workspace
# Create the workspace using the specified parameters
ws = Workspace.create(name = workspace_name,
                      subscription_id = subscription_id,
                      resource_group = resource_group, 
                      location = workspace_region,
                      create_resource_group = True,
                      exist_ok = True)
ws.get_details()

# write the details of the workspace to a configuration file to the notebook library
ws.write_config()
```

További információ az Azure Machine Learning-munkaterület osztály a [SDK-forrásdokumentáció](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).


## <a name="create-compute-clusters"></a>Számítási fürtök létrehozása
Az Azure Machine Learning támogatja több számítási célnak, amelyek némelyike kezeli a szolgáltatás és mások csatolható a munkaterülethez (például) Egy HDInsight-fürtöt, vagy egy távoli virtuális Gépen. További információ a különböző [számítási céljainak](../machine-learning/service/how-to-set-up-training-targets.md). A számítási fürt képezi le egy AmlCompute fürt létrehozása az Azure Machine Learning szolgáltatás létrehozása egy Azure Batch AI fogalma. A Amlcompute létrehozása eltarthat hogyan adja át a paramétereket az Azure Batch AI hasonló számítási konfigurációban. Megjegyzés: az egyik dolog, hogy az automatikus skálázás alapértelmezés szerint engedélyezve van a AmlCompute fürtön mivel ki van kapcsolva az Azure Batch AI alapértelmezés szerint.

A **Batch AI**, így kész is:

```python
nodes_count = 2
cluster_name = 'nc6'

parameters = models.ClusterCreateParameters(
    vm_size='STANDARD_NC6',
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=nodes_count)
    ),
    user_account_settings=models.UserAccountSettings(
        admin_user_name=cfg.admin,
        admin_user_password=cfg.admin_password or None,
        admin_user_ssh_public_key=cfg.admin_ssh_key or None,
    )
)
_ = client.clusters.create(cfg.resource_group, cfg.workspace, cluster_name, parameters).result()
```

A **Azure Machine Learning szolgáltatás**, próbálja ki:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
gpu_cluster_name = "nc6"

# Verify that cluster does not exist already
try:
    gpu_cluster = ComputeTarget(workspace=ws, name=gpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           vm_priority='lowpriority',
                                                           min_nodes=1,
                                                           max_nodes=2,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    gpu_cluster = ComputeTarget.create(ws, gpu_cluster_name, compute_config)

gpu_cluster.wait_for_completion(show_output=True)
```

További tudnivalók a AMLCompute osztályt a [SDK-forrásdokumentáció](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py). Vegye figyelembe, hogy a fenti konfigurációban, csak vm_size és max_nodes kötelező, és a többi a tulajdonságok, például a virtuális hálózatok csak a speciális fürttelepítéshez.

## <a name="monitor-status-of-your-cluster"></a>A fürt állapotának figyelése
Ez az egyszerűbb az Azure Machine Learning szolgáltatás módon lentebb látható.

A **Batch AI**, így kész is:

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

A **Azure Machine Learning szolgáltatás**, próbálja ki:

```python
gpu_cluster.get_status().serialize()
```

## <a name="get-reference-to-a-storage-account"></a>Získat odkaz nA Storage-fiók
A blob, például egy adattárolási fogalmát lekérdezi egyszerűsített az Azure Machine Learning szolgáltatás használatával az adattár-objektumot. Alapértelmezés szerint az Azure Machine Learning szolgáltatás munkaterületet hoz létre egy storage-fiókot, de saját tárhely is is csatlakoztatható, munkaterület létrehozása során. 

A **Batch AI**, így kész is:

```python
azure_blob_container_name = 'batchaisample'
blob_service = BlockBlobService(cfg.storage_account_name, cfg.storage_account_key)
blob_service.create_container(azure_blob_container_name, fail_on_exist=False)
```

A **Azure Machine Learning szolgáltatás**, próbálja ki:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)
```

További tudnivalók a további tárfiókok regisztrál, vagy egy másik regisztrált adattárolója a hivatkozás beszerzése a [Azure Machine Learning szolgáltatás dokumentációja](../machine-learning/service/how-to-access-data.md#access).


## <a name="download-and-upload-data"></a>Töltse le és az adatok feltöltése 
Mindkét szolgáltatással a tárfiókba, a fent az adattárhoz hivatkozás használatával könnyedén tölthet fel az adatokat. Azure Batch AI mi is üzembe helyezhet a tanítási szkriptet a fájlmegosztáson részeként bár látni fogja, hogyan is megadhat, az Azure Machine Learning szolgáltatás a feladat konfiguráció részeként.

A **Batch AI**, így kész is:

```python
mnist_dataset_directory = 'mnist_dataset'
utils.dataset.download_and_upload_mnist_dataset_to_blob(
    blob_service, azure_blob_container_name, mnist_dataset_directory)

script_directory = 'tensorflow_samples'
script_to_deploy = 'mnist_replica.py'

blob_service.create_blob_from_path(azure_blob_container_name,
                                   script_directory + '/' + script_to_deploy, 
                                   script_to_deploy)
```


A **Azure Machine Learning szolgáltatás**, próbálja ki:

```python
import os
import urllib
os.makedirs('./data', exist_ok=True)
download_url = 'https://s3.amazonaws.com/img-datasets/mnist.npz'
urllib.request.urlretrieve(download_url, filename='data/mnist.npz')

ds.upload(src_dir='data', target_path='mnist_dataset', overwrite=True, show_progress=True)

path_on_datastore = ' mnist_dataset/mnist.npz' ds_data = ds.path(path_on_datastore) print(ds_data)
```

## <a name="create-experiments"></a>Kísérletek létrehozása
Ahogy korábban említettük Azure Machine Learning szolgáltatás rendelkezik egy Azure Batch AI hasonló kísérlet fogalma. Minden egyes kísérlet akkor is rendelkezik egyes fut le, hogyan van feladatok az Azure Batch AI hasonló. Az Azure Machine Learning szolgáltatás lehetővé teszi, hogy a hierarchia minden szülő futtatja, az egyes gyermek futtatások alatt.

A **Batch AI**, így kész is:

```python
experiment_name = 'tensorflow_experiment'
experiment = client.experiments.create(cfg.resource_group, cfg.workspace, experiment_name).result()
```

A **Azure Machine Learning szolgáltatás**, próbálja ki:

```python
from azureml.core import Experiment

experiment_name = 'tensorflow_experiment'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="submit-jobs"></a>Feladatok elküldése
Miután létrehozott egy kísérletet, rendelkezik egy Futtatás be néhány eltérő módot. Ebben a példában azt próbálja létrehozni a deep learning-modellben tensorflow-hoz és a egy Azure Machine Learning szolgáltatás Estimator fogja használni ehhez. Egy [Estimator](../machine-learning/service/how-to-train-ml-models.md) az alapul szolgáló futtatási konfigurációs, amely megkönnyíti a futtatások elküldeni, és a jelenleg támogatott Pytorch és tensorflow-hoz csak egyszerűen csak egy burkoló funkció. Keresztül adattárainak fogalma, látni fogja emellett milyen egyszerű válik a csatlakoztatási elérési út megadása 

A **Batch AI**, így kész is:

```python
azure_file_share = 'afs'
azure_blob = 'bfs'
args_fmt = '--job_name={0} --num_gpus=1 --train_steps 10000 --checkpoint_dir=$AZ_BATCHAI_OUTPUT_MODEL --log_dir=$AZ_BATCHAI_OUTPUT_TENSORBOARD --data_dir=$AZ_BATCHAI_INPUT_DATASET --ps_hosts=$AZ_BATCHAI_PS_HOSTS --worker_hosts=$AZ_BATCHAI_WORKER_HOSTS --task_index=$AZ_BATCHAI_TASK_INDEX'

parameters = models.JobCreateParameters(
     cluster=models.ResourceId(id=cluster.id),
     node_count=2,
     input_directories=[
        models.InputDirectory(
            id='SCRIPT',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, script_directory)),
        models.InputDirectory(
            id='DATASET',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, mnist_dataset_directory))],
     std_out_err_path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
     output_directories=[
        models.OutputDirectory(
            id='MODEL',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Models'),
        models.OutputDirectory(
            id='TENSORBOARD',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Logs')
     ],
     mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
                        cfg.storage_account_name, azure_file_share_name),
                    relative_mount_path=azure_file_share)
            ],
            azure_blob_file_systems=[
                models.AzureBlobFileSystemReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    container_name=azure_blob_container_name,
                    relative_mount_path=azure_blob)
            ]
        ),
     container_settings=models.ContainerSettings(
         image_source_registry=models.ImageSourceRegistry(image='tensorflow/tensorflow:1.8.0-gpu')),
     tensor_flow_settings=models.TensorFlowSettings(
         parameter_server_count=1,
         worker_count=nodes_count,
         python_script_file_path='$AZ_BATCHAI_INPUT_SCRIPT/'+ script_to_deploy,
         master_command_line_args=args_fmt.format('worker'),
         worker_command_line_args=args_fmt.format('worker'),
         parameter_server_command_line_args=args_fmt.format('ps'),
     )
)
```

A feladat elküldése az Azure Batch AI magát a létrehozás függvény keresztül történik.

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

A teljes információkat a betanítási kódtöredék (beleértve a mnist_replica.py fájl azt a fenti fájlmegosztást kellett feltöltött) található a [Azure Batch AI sample notebook github-adattárat](https://github.com/Azure/BatchAI/tree/2238607d5a028a0c5e037168aefca7d7bb165d5c/recipes/TensorFlow/TensorFlow-GPU-Distributed).

A **Azure Machine Learning szolgáltatás**, próbálja ki:

```python
from azureml.train.dnn import TensorFlow

script_params={
    '--num_gpus': 1,
    '--train_steps': 500,
    '--input_data': ds_data.as_mount()

}

estimator = TensorFlow(source_directory=project_folder,
                       compute_target=gpu_cluster,
                       script_params=script_params,
                       entry_script='tf_mnist_replica.py',
                       node_count=2,
                       worker_count=2,
                       parameter_server_count=1,   
                       distributed_backend='ps',
                       use_gpu=True)
```

A teljes körű információkat a betanítási kódtöredék (beleértve a tf_mnist_replica.py fájl) megtalálható a [Azure Machine Learning szolgáltatás minta notebook github-adattárat](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/distributed-tensorflow-with-parameter-server). Maga az adattár vagy csatlakoztathatók az egyes csomópontokon, illetve magán a csomóponton a betanítási adatok tölthető le. További részleteket az adattárhoz, az a estimator hivatkozó szerepel a [Azure Machine Learning szolgáltatás dokumentációja](../machine-learning/service/how-to-access-data.md#access). 

Küldjön egy Futtatás az Azure Machine Learning szolgáltatás egy submit funkción keresztül.

```python
run = experiment.submit(estimator)
print(run)
```

Egy másik módja, a Futtatás futtatási konfigurációs – különösen hasznos, ha egy egyéni oktatókörnyezet meghatározása használatával paramétereinek megadása. További részleteket talál a jelen [minta AmlCompute notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-amlcompute/train-on-amlcompute.ipynb). 

## <a name="monitor-runs"></a>A figyelő futtatása
Miután egy futtatás, vagy megvárhatja, hogy végezze el, vagy az Azure Machine Learning szolgáltatás mutatnak, amelyek közvetlenül a kódból hívhat Jupyter-widgetek használatával figyelheti. Bármely korábbi futtatás kontextusában kérje le a különböző a munkaterületen kísérletek ismétlése által és az egyes futó minden egyes kísérleten belülről.

A **Batch AI**, így kész is:

```python
utils.job.wait_for_job_completion(client, cfg.resource_group, cfg.workspace, 
                                  experiment_name, job_name, cluster_name, 'stdouterr', 'stdout-wk-0.txt')

files = client.jobs.list_output_files(cfg.resource_group, cfg.workspace, experiment_name, job_name,
                                      models.JobsListOutputFilesOptions(outputdirectoryid='stdouterr')) 
for f in list(files):
    print(f.name, f.download_url or 'directory')
```


A **Azure Machine Learning szolgáltatás**, próbálja ki:

```python
run.wait_for_completion(show_output=True)

from azureml.widgets import RunDetails
RunDetails(run).show()
```

Hogyan szeretné betölteni a widgeten a jegyzetfüzet, a naplók valós időben való pillanatképe a következő: ![Widget figyelési diagramja](./media/overview-what-happened-batch-ai/monitor.png)



## <a name="edit-clusters"></a>Fürtök szerkesztése
Fürt törlése nagyon egyszerű. Emellett az Azure Machine Learning szolgáltatás lehetővé teszi, hogy frissítse a fürtöt, a jegyzetfüzet belül abban az esetben, skálázza fel a nagyobb számú csomópontra, vagy növelje az inaktív várakozási idő előtt, hogy a fürt vertikális. A Microsoft nem engedélyezi, hogy a fürt, másrészt, Virtuálisgép-méretének módosítása ráfordítást igényel hatékonyan a háttérrendszer az új központi telepítést.

A **Batch AI**, így kész is:
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

A **Azure Machine Learning szolgáltatás**, próbálja ki:

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="get-support"></a>Támogatás kérése

A Batch AI van nyár kivonása. március 31-én kerül piacra, és regisztráljon a szolgáltatáshoz, kivéve ha szerepel az engedélyezési listán által támogatásának kivételt hozna létre az új előfizetések már blokkolja.  Fel velünk a kapcsolatot, [Azure Batch AI Training előzetes verziójának](mailto:AzureBatchAITrainingPreview@service.microsoft.com) , ha bármilyen kérdése van, vagy ha visszajelzést szeretne küldeni a migrálás az Azure Machine Learning szolgáltatáshoz.

Az Azure Machine Learning szolgáltatás egy olyan általánosan elérhető szolgáltatás. Ez azt jelenti, hogy a vállalt szolgáltatásszint-szerződéssel és különböző támogatási csomagok, amelyek közül választhatnak, származik.

Azure-infrastruktúra az Azure Batch AI szolgáltatás vagy az Azure Machine Learning szolgáltatás révén a díjszabás nem változhat, csak az ár mindkét esetben a mögöttes számítási díj szerint. További információkért lásd: a [díjkalkulátor](https://azure.microsoft.com/pricing/details/machine-learning-service/).

A régiónkénti rendelkezésre állás, a két szolgáltatás között megtekintheti a [az Azure portal](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>További lépések

+ Olvassa el a [Azure Machine Learning szolgáltatás áttekintése](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Konfigurálja a modell betanítása és számítási célt](../machine-learning/service/how-to-set-up-training-targets.md) Azure Machine Learning szolgáltatással.

+ Tekintse át a [Azure ütemterve](https://azure.microsoft.com/updates/) további más Azure-szolgáltatás frissítések.
