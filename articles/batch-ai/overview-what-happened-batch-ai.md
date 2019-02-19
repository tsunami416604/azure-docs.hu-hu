---
title: Mi történik az Azure Batch AI? | Microsoft Docs
description: További tudnivalók az Azure Batch AI, mi történik, és az Azure Machine Learning szolgáltatás számítási lehetőségek.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 2/14/2019
ms.author: garye
ms.openlocfilehash: 44c0eec97f63897173ecde170ec4ed926db8bcaa
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342813"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Mi történik az Azure Batch AI?

**Az Azure Batch AI szolgáltatás márciusi kivonás alatt áll.** Az ipari méretekben tanítási és pontozási a Batch AI képességeit már elérhetők [Azure Machine Learning szolgáltatás](../machine-learning/service/overview-what-is-azure-ml.md), amely óta általánosan elérhető 2018. December 4.

Sok más gépi tanulási funkciókat, valamint az Azure Machine Learning szolgáltatás tartalmaz egy felhőalapú, felügyelt számítási célt képzés, üzembe helyezése és pontozás a machine learning-modellek. A számítási célnak nevezzük [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Indítsa el a migrálást, és jelenleg is használó](#migrate). Az Azure Machine Learning szolgáltatás segítségével kezelheti a [Python SDK-k](../machine-learning/service/quickstart-create-workspace-with-python.md), parancssori felületet, és a [az Azure portal](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Támogatási idővonal

| Dátum | A Batch AI szolgáltatás részletei |
| ---- |-----------------|
| December&nbsp;14&#x2c;&nbsp;2018.| A meglévő Azure Batch AI előfizetéseket, mielőtt is használhatja. Azonban nem **új előfizetések** is lehetséges, és nincs új befektetések kerül sor.|
| March&nbsp;31&#x2c;&nbsp;2019 | Ezt a dátumot követően a meglévő Batch AI-előfizetések nem fog működni. |

## <a name="how-does-azure-machine-learning-service-compare"></a>Az Azure Machine Learning szolgáltatás összehasonlítása
Egy felhőalapú szolgáltatás, amellyel betanításához, üzembe helyezése, automatizálhatja és felügyelheti a machine learning-modellek, minden a felhőbeli biztosít széles körű skála. Egy magas szintű ismeretekkel lekérése a [ebben a cikkben áttekintjük az Azure Machine Learning szolgáltatás](../machine-learning/service/overview-what-is-azure-ml.md).
 

Egy tipikus modell fejlesztési életciklus beletartozik az adat-előkészítési, a képzés és a Kísérletezési és a egy üzembe helyezési fázisnak. A teljes körű ciklus is vezényli a Machine Learning-folyamatokat használ.

![folyamatábrája](./media/overview-what-happened-batch-ai/lifecycle.png)


[További információ a szolgáltatás működése és a főbb fogalmait](../machine-learning/service/concept-azure-machine-learning-architecture.md). A modell betanítási munkafolyamat fogalmak számos hasonlóak a meglévő fogalmait a Batch AI. 

Pontosabban a következő térképét hogyan gondolja őket:
 
|A Batch AI szolgáltatás|  Azure Machine Learning szolgáltatás|
|:--:|:---:|
|Munkaterület|Munkaterület|
|Fürt|   Számítási típus `AmlCompute`|
|Fájlkiszolgálók|Adattárolók|
|Kísérletek|Kísérletek|
|Feladatok|Fut (lehetővé teszi, hogy a beágyazott fut)|
 
Íme egy másik nézet ugyanazon tábla, amely segít a további elemek megjelenítése:
 
**Batch AI-hierarchia**
![folyamatábrája](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
**Az Azure Machine Learning szolgáltatás hierarchia**
![folyamatábrája](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

### <a name="platform-capabilities"></a>Platform képességei
Az Azure Machine Learning szolgáltatás tárházaként nagyszerű új funkciókat, beleértve egy teljes körű képzési -> üzembe helyezési vermek, amely az AI-fejlesztéshez használhatja bármely Azure-erőforrások kezelése nélkül. Ez a táblázat hasonlítja össze a két szolgáltatás között képzést funkció támogatása.

|Szolgáltatás|BatchAI szolgáltatás|Azure Machine Learning szolgáltatás|
|-------|:-------:|:-------:|
|Virtuális gép méretének kiválasztása |PROCESSZOR ÉS GPU    |PROCESSZOR ÉS GPU-T. Emellett támogatja a FPGA következtetési|
|Mesterséges Intelligencia üzemkész fürt (illesztőprogramok, Docker, stb.)|  Igen |Igen|
|Csomópont-előkészítő| Igen|    Nem|
|Operációsrendszer-család kiválasztása   |Részleges    |Nem|
|Dedikált és LowPriority virtuális gépek  |Igen    |Igen|
|Automatikus méretezés   |Igen    |Igen (alapértelmezés)|
|Várakozási idő az automatikus skálázáshoz  |Nem |Igen|
|SSH    |Igen|   Igen|
|Fürt feladatszintű csatlakoztatás |Igen (FileShares, Blobok, az NFS, egyéni)   |Igen (csatlakoztatni, vagy töltse le az adattár)|
|Elosztott betanítás|  Igen |Igen|
|Feladat-végrehajtási mód|    Virtuális gép vagy a tároló|    Tároló|
|Egyéni Tárolórendszerképet|    Igen |Igen|
|Bármely eszközkészlet    |Igen    |Igen (futtassa Python-szkriptet)|
|JobPreparation|    Igen |még nem|
|Feladatszintű csatlakoztatás |Igen (FileShares, Blobok, az NFS, egyéni)   |Igen (FileShares, Blobok)|
|Feladat figyelése     |GetJob keresztül|    Futtatási előzmények (részletesebb információk, egyéni modult, hogy további metrikák push) keresztül|
|Feladatnaplók és fájlok/modellek beolvasása |   ListFiles és a Storage API-kon keresztül  |Összetevő szolgáltatáson keresztül|
 |Tensorboard támogatása   |Nem|    Igen|
|Virtuális gép családi szintje szerinti kvóták |Igen    |Igen (az előző kapacitás átvitelre)|
 
Az előző táblázatban kívül is BatchAI hagyományosan nem támogatott szolgáltatásokat az Azure Machine Learning szolgáltatásban.

|Szolgáltatás|BatchAI szolgáltatás|Azure Machine Learning szolgáltatás|
|-------|:-------:|:-------:|
|Környezet előkészítése    |Nem |Igen (Conda előkészítése és feltöltése az ACR-be)|
|Hiperparaméter finomhangolása  |Nem|    Igen|
|Modellkezelés   |Nem |Igen|
|Operationalization/Deployment| Nem  |AKS és ACI-n keresztül|
|Adatok előkészítése   |Nem |Igen|
|Számítási céljainak    |Azure-beli virtuális gépek  |Helyi, BatchAI (a AmlCompute), a DataBricks, a HDInsight|
|Automatizált Machine Learning |Nem|    Igen|
|Folyamatok  |Nem |Igen|
|Batch-pontozás  |Igen    |Igen|
|Támogatási portál vagy a parancssori felületen|    Igen |Igen|


### <a name="programming-interfaces"></a>Programozási felületek

Ez a táblázat minden egyes szolgáltatáshoz elérhető különböző alkalmazásprogramozási felületek mutat be.
    
|Szolgáltatás|BatchAI szolgáltatás|Azure Machine Learning szolgáltatás|
|-------|:-------:|:-------:|
|SDK    |Java, C#, Python, Nodejs   |Python (futtatási konfigurációs alapú és a közös keretrendszer alapján estimator)|
|parancssori felület    |Igen    |még nem|
|Azure Portal   |Igen    |Igen (kivéve a feladat elküldése)|
|REST API   |Igen    |Igen, de elosztott mikroszolgáltatás-alapú között|




## <a name="why-migrate"></a>Miért érdemes migrálni?

Frissítése az előzetes verzió BatchAI szolgáltatásból a GA'ed Azure Machine Learning szolgáltatáshoz biztosít révén könnyebben használható, például Estimators és adattárainak fogalmakat jobb felhasználói élményt. Általánosan elérhető Azure-szolgáltatási szintű SLA-k és az ügyfél-támogatási is garantálja.

Az Azure Machine Learning szolgáltatás lehetőségeit is elérhetővé teszi az új funkciók, például automatikus ML, a Hiperparaméter finomhangolása és a gépi Tanulási folyamatok, amelyek hasznosak lehetnek a legtöbb nagy léptékű AI-számítási. Lehetővé teszi egy külön szolgáltatás közötti váltás nélkül használhassa a betanított modell üzembe helyezése segítségével végezze el a data science hurok az adatok (a Data Prep SDK használatával) egészen operacionalizálás és a modell figyelése.

<a name="migrate"></a>

## <a name="how-do-i-migrate"></a>Hogyan kell áttelepíteni az adatokat?

Akkor kövesse a lépéseket, amelyek segítenek a két szolgáltatás között térkép parancsokat az áttelepítési útmutatóban, azt javasoljuk, hogy ismeri az Azure Machine Learning szolgáltatás révén a hozzá tartozó dokumentáció első időt töltött] (.. / machine-learning/service/overview-what-is-azure-ml.md) többek között a [oktatóanyag a Python](../machine-learning/service/tutorial-train-models-with-aml.md).

Az alkalmazások a fennakadások elkerülése érdekében, és kihasználhatják a legújabb funkciók, a következő lépésekkel 2019. március 31. előtt:

1. Hozzon létre egy Azure Machine Learning szolgáltatás munkaterületet, és első lépések:
    + [Python-alapú a rövid útmutató](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Az Azure portal alapján rövid útmutatójában](../machine-learning/service/quickstart-get-started.md)

1. Állítsa be egy [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) modell betanítása.

1. A parancsfájlok használata az Azure Machine Learning Compute frissítése.


### <a name="sdk"></a>SDK

Aktuális SDK támogatja az Azure Machine Learning szolgáltatás több Python SDK keresztül történik. A fő SDK körülbelül kéthetente frissül, és a következő paranccsal PyPi telepíthető:

```python
pip install --upgrade azureml-sdk[notebooks]
```

Állítsa be a környezetet, és telepítse az SDK-ban ezek [a rövid útmutató](../machine-learning/service/quickstart-create-workspace-with-python.md#install-the-sdk)

A megnyitott egy jupyter notebookot a kernel a megfelelő conda-környezetre mutató, a következő hogyan képezze le a két szolgáltatás szereplő parancsok:


### <a name="create-a-workspace"></a>Munkaterület létrehozása
A használatával egy configuration.json BatchAI munkaterület inicializálása fogalmát hasonlóképpen leképez egy konfigurációs fájlban, az Azure ML használatával.

Batch AI kész is ezzel a módszerrel:

```python
sys.path.append('../../..')
import utilities as utils

cfg = utils.config.Configuration('../../configuration.json')
client = utils.config.create_batchai_client(cfg)

utils.config.create_resource_group(cfg)
_ = client.workspaces.create(cfg.resource_group, cfg.workspace, cfg.location).result()
```

Azure Machine Learning service:

```python
from azureml.core.workspace import Workspace

ws = Workspace.from_config()
print('Workspace name: ' + ws.name, 
      'Azure region: ' + ws.location, 
      'Subscription id: ' + ws.subscription_id, 
      'Resource group: ' + ws.resource_group, sep = '\n')
```

Emellett is létrehozhat munkaterület úgy közvetlenül a konfigurációs paramétereket, például a azureml.core munkaterület importálása

```python
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

A megfelelő függvényeket az AML-munkaterület osztályt a részletes dokumentációhoz érhető el.


#### <a name="create-a-compute-cluster"></a>Számítási fürt létrehozása
Az Azure Machine Learning támogatja több számítási célnak, amelyek némelyike kezeli a szolgáltatás és mások csatolható a munkaterülethez (például) Egy HDInsight-fürt vagy egy távoli virtuális Gépen, további információk érhetők el itt). A számítási fürt képezi le egy AmlCompute fürt létrehozása az Azure Machine Learning egy BatchAI létrehozásának lehetőségét. A Amlcompute létrehozása eltarthat hogyan adja át a paramétereket lévő BatchAI hasonló számítási konfigurációban. Megjegyzés: az egyik dolog, hogy az automatikus skálázás alapértelmezés szerint engedélyezve van a AmlCompute fürtön mivel ki van kapcsolva az BatchAI alapértelmezés szerint.

Batch AI kész is ezzel a módszerrel:

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

Az Azure Machine Learning szolgáltatás próbálja ki:

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

A megfelelő függvényeket az AmlCompute osztályt a részletes dokumentációhoz érhető el. Vegye figyelembe, hogy a fenti konfigurációban csak vm_size és max_nodes kötelező, és a tulajdonságok, például a virtuális hálózatok a többi speciális konfiguráció.


### <a name="monitoring-status-of-your-cluster"></a>A fürt állapotának figyelése
Ez az egyszerűbb, az Azure Machine Learning, az alábbi.

Batch AI kész is ezzel a módszerrel:

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

Az Azure Machine Learning szolgáltatás próbálja ki:

```python
gpu_cluster.get_status().serialize()
```

#### <a name="getting-reference-to-a-storage-account"></a>Bevezetés a hivatkozást a tárfiókra
A blob, például egy adattárolási fogalmát lekérdezi egyszerűsített Azure ml használatával az adattár-objektumot. Alapértelmezés szerint az Azure gépi tanulás munkaterületet hoz létre egy storage-fiókot, de saját tárhely is is csatlakoztatható, munkaterület létrehozása során. 

Batch AI kész is ezzel a módszerrel:

```python
azure_blob_container_name = 'batchaisample'
blob_service = BlockBlobService(cfg.storage_account_name, cfg.storage_account_key)
blob_service.create_container(azure_blob_container_name, fail_on_exist=False)
```

Az Azure Machine Learning szolgáltatás próbálja ki:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)
```

További tárfiókok regisztrál, vagy egy másik regisztrált datastore-hivatkozás beszerzése bővebben itt található.


#### <a name="downloading-and-uploading-data"></a>Töltsön le és az adatok feltöltése 
Mindkét szolgáltatással a tárfiókba, a fent az adattárhoz hivatkozás használatával könnyedén tölthet fel az adatokat. A BatchAI azt is üzembe helyezheti a tanítási szkriptet a fájlmegosztáson részeként bár látni fogja, hogyan is megadhat, az Azure Machine Learning esetében a feladat konfiguráció részeként.

Batch AI kész is ezzel a módszerrel:

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


Az Azure Machine Learning szolgáltatás próbálja ki:

```python
import os
import urllib
os.makedirs('./data', exist_ok=True)
download_url = 'https://s3.amazonaws.com/img-datasets/mnist.npz'
urllib.request.urlretrieve(download_url, filename='data/mnist.npz')

ds.upload(src_dir='data', target_path='mnist_dataset', overwrite=True, show_progress=True)

path_on_datastore = ' mnist_dataset/mnist.npz' ds_data = ds.path(path_on_datastore) print(ds_data)
```

#### <a name="create-an-experiment"></a>Kísérlet létrehozása
Ahogy korábban említettük Azure ML rendelkezik egy hasonló BatchAI kísérlet fogalma. Minden kísérlet akkor is rendelkezik az egyes futtatások, hogyan már feladatok BatchAI hasonló. Az Azure Machine Learning lehetővé teszi, hogy a hierarchia minden szülő futtatja, az egyes gyermek futtatások alatt.

Batch AI kész is ezzel a módszerrel:

```python
experiment_name = 'tensorflow_experiment'
experiment = client.experiments.create(cfg.resource_group, cfg.workspace, experiment_name).result()
```

Az Azure Machine Learning szolgáltatás próbálja ki:

```python
from azureml.core import Experiment

experiment_name = 'tensorflow_experiment'
experiment = Experiment(ws, name=experiment_name)
```


#### <a name="submit-a-job"></a>Feladat elküldése
Miután létrehozott egy kísérletet, rendelkezik egy Futtatás be néhány eltérő módot. Ebben a példában azt próbálja létrehozni a deep learning-modellben tensorflow-hoz és a egy Azure Machine Learning Estimator fogja használni ehhez. Egy Estimator az alapul szolgáló futtatási konfigurációs, amely megkönnyíti a futtatások elküldeni, és a jelenleg támogatott Pytorch és tensorflow-hoz csak egyszerűen használható burkoló funkció. Keresztül adattárainak fogalma, látni fogja emellett milyen egyszerű válik a csatlakoztatási elérési út megadása 

Batch AI kész is ezzel a módszerrel:

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

A feladat elküldése az BatchAI magát a létrehozás függvény keresztül történik.

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

A teljes adatait (beleértve a azt a fájlmegosztást a fenti kellett feltöltött mnist_replica.py fájlt) a betanítási kódrészlet az BatchAI notebook github mintatárból itt található.

Az Azure Machine Learning szolgáltatás próbálja ki:

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

E képzési kódrészlet (beleértve a tf_mnist_replica.py fájlt) a teljes adatait az Azure Machine Learning minta notebook github-adattárat itt található. Maga az adattár vagy csatlakoztathatók az egyes csomópontokon, illetve magán a csomóponton a betanítási adatok tölthető le. További részleteket az adattárhoz, az a estimator hivatkozó jelenleg itt tart. 

Futtatás az Azure ML elküldése a küldés funkció keresztül történik.

```python
run = experiment.submit(estimator)
print(run)
```

Egy másik módja, a Futtatás futtatási konfigurációs – különösen hasznos, ha egy egyéni oktatókörnyezet meghatározása használatával paramétereinek megadása. További részleteket a notebook példa Itt találhatja. 

#### <a name="monitor-your-run"></a>A Futtatás figyelése
Miután egy futtatás, vagy megvárhatja, hogy végezze el, vagy az Azure Machine Learning mutatnak, amelyek közvetlenül a kódból hívhat Jupyter-widgetek használatával figyelheti. Bármely korábbi futtatás kontextusában kérje le a különböző a munkaterületen kísérletek ismétlése által és az egyes futó minden egyes kísérleten belülről.

Batch AI kész is ezzel a módszerrel:

```python
utils.job.wait_for_job_completion(client, cfg.resource_group, cfg.workspace, 
                                  experiment_name, job_name, cluster_name, 'stdouterr', 'stdout-wk-0.txt')

files = client.jobs.list_output_files(cfg.resource_group, cfg.workspace, experiment_name, job_name,
                                      models.JobsListOutputFilesOptions(outputdirectoryid='stdouterr')) 
for f in list(files):
    print(f.name, f.download_url or 'directory')
```


Az Azure Machine Learning szolgáltatás próbálja ki:

```python
run.wait_for_completion(show_output=True)

from azureml.widgets import RunDetails
RunDetails(run).show()
```

Hogyan szeretné betölteni a widgeten a jegyzetfüzet, a naplók valós időben való pillanatképe a következő: ![Widget figyelési diagramja](./media/overview-what-happened-batch-ai/monitor.png)



#### <a name="delete-a-cluster"></a>Fürt törlése
Fürt törlése nagyon egyszerű. Emellett az Azure Machine Learning lehetővé teszi, hogy frissítse a fürtöt, a jegyzetfüzet belül abban az esetben, skálázza fel a nagyobb számú csomópontra, vagy növelje az inaktív várakozási idő előtt, hogy a fürt vertikális. A Microsoft nem engedélyezi, hogy a fürt, másrészt, Virtuálisgép-méretének módosítása ráfordítást igényel hatékonyan a háttérrendszer az új központi telepítést.

Batch AI kész is ezzel a módszerrel:
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

Az Azure Machine Learning szolgáltatás próbálja ki:

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="support"></a>Támogatás

BatchAI van nyár kivonása. március 31-én kerül piacra, és regisztráljon a szolgáltatáshoz, kivéve ha szerepel az engedélyezési listán által támogatásának kivételt hozna létre az új előfizetések már blokkolja.  Keresse fel az Azure Batch AI Training előzetes verziójának AzureBatchAITrainingPreview@service.microsoft.com , ha bármilyen kérdése van, vagy ha visszajelzést szeretne küldeni a migrálás az Azure Machine Learning szolgáltatáshoz.

Az Azure Machine Learning szolgáltatás egy olyan általánosan elérhető szolgáltatás. Ez azt jelenti, hogy a vállalt szolgáltatásszint-szerződéssel és a különféle által támogatott csomagok közül választhat származik.

Azure-infrastruktúra a BatchAI szolgáltatás vagy az Azure Machine Learning szolgáltatás révén a díjszabás nem változhat, csak az ár mindkét esetben a mögöttes számítási díj szerint. További információkért lásd: a [díjkalkulátor](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Régiónkénti rendelkezésre állás, a két szolgáltatás között itt tekinthet meg: https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai, machine-learning-szolgáltatás és régiók = all.


## <a name="next-steps"></a>További lépések

+ Olvassa el a [Azure Machine Learning szolgáltatás áttekintése](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Konfigurálja a modell betanítása és számítási célt](../machine-learning/service/how-to-set-up-training-targets.md) Azure Machine Learning szolgáltatással.

+ Tekintse át a [Azure ütemterve](https://azure.microsoft.com/updates/) további más Azure-szolgáltatás frissítések.
