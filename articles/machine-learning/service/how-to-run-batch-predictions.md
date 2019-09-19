---
title: Kötegelt előrejelzések futtatása nagy mennyiségű adatfolyamatokkal
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan teheti aszinkron módon a Batch-előrejelzéseket nagy mennyiségű adattal a Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 07/12/2019
ms.openlocfilehash: 3997f327bd6512eeee2cb5e7a0af802f12d1727a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034299"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-pipelines"></a>A Batch-előrejelzések futtatása Azure Machine Learning folyamatokkal rendelkező nagyméretű adatkészleteken

Ebből a cikkből megtudhatja, hogyan hajthat végre nagy mennyiségű adattal kapcsolatos előrejelzéseket aszinkron módon a ML-folyamatok Azure Machine Learning használatával.

A Batch-előrejelzés (vagy a kötegelt pontozás) költséghatékony következtetést biztosít, és az aszinkron alkalmazások esetében páratlan átviteli sebességgel rendelkezik. Batch-előrejelzési folyamatok üzemi környezetben elérhető adatokat több terabájtnyi következtetésekhez végrehajtásához méretezheti. A Batch-előrejelzés nagy adatgyűjtési sebességre, valamint a nagy adatgyűjtemények felébresztésére és előrejelzésére van optimalizálva.

>[!TIP]
> Ha a rendszer kis késleltetésű feldolgozást igényel (egyetlen dokumentum feldolgozásához vagy a dokumentumok kis halmazának gyors létrehozásához), a kötegelt előrejelzés helyett [valós idejű pontozást](how-to-consume-web-service.md) használjon.

A következő lépésekben létre fog hozni egy [Machine learning-folyamatot](concept-ml-pipelines.md) egy előre képzett számítógépes jövőkép-modell (a[kezdetektől a v3](https://arxiv.org/abs/1512.00567)) regisztrálásához. Ezután az előre betanított modell használatával kötegelt pontozást hajthat végre az Azure Blob Storage-fiókban elérhető rendszerképeken. Ezek a lemezképek használt pontozási címke nélküli a lemezképeket a [épít](http://image-net.org/) adatkészlet.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

- Állítsa be a fejlesztési környezetet az Azure Machine Learning SDK telepítése. További információkért lásd: [a fejlesztési környezet konfigurálása az Azure Machine Learning](how-to-configure-environment.md).

- Hozzon létre egy Azure Machine Learning-munkaterületet, amely tárolja az folyamat-erőforrások. A következő kódot használhatja, vagy további beállításokért lásd: [munkaterület konfigurációs fájl létrehozása](how-to-configure-environment.md#workspace).

  ```python
  from azureml.core import Workspace
  ws = Workspace.create(name = '<workspace-name>',
                        subscription_id = '<subscription-id>',
                        resource_group = '<resource-group>',
                        location = '<workspace_region>',
                        exist_ok = True
                        )
  ```

## <a name="set-up-machine-learning-resources"></a>Állítsa be a machine learning-erőforrások

A következő lépésekkel állíthatja be a folyamat futtatásához szükséges erőforrásokat:

- Elérni az adattárhoz, amely már rendelkezik a modell imagenet, bemeneti címke és képek pontszámot rendelni az (ez már be van állítva az Ön számára).
- Egy adattár beállítása a kimenetek tárolásához.
-  `DataReference`Konfigurálja úgy az objektumokat, hogy az előző adattárolókban lévő értékekre mutassanak.
- Állítsa be a számítási gépek vagy fürtök, ahol a folyamat lépései futni fog.

### <a name="access-the-datastores"></a>Hozzáférés az adattárolók

Első lépésként elérni az adattárhoz, amely rendelkezik a modell, a címkék és a képek.

Használjon egy *SampleData*nevű nyilvános BLOB-tárolót a *pipelinedata* -fiókban, amely a ImageNet kiértékelési csoport képeit tárolja. Az adattár neve a nyilvános tárolókban *images_datastore*. Az adattároló regisztrálja a munkaterülethez:

```python
from azureml.core import Datastore

account_name = "pipelinedata"
datastore_name = "images_datastore"
container_name = "sampledata"

batchscore_blob = Datastore.register_azure_blob_container(ws,
                                                          datastore_name=datastore_name,
                                                          container_name=container_name,
                                                          account_name=account_name,
                                                          overwrite=True)
```

Ezután állítsa be, hogy az alapértelmezett adattárt használja a kimenetekhez.

A munkaterület létrehozásakor a [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) és a [blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) alapértelmezés szerint a munkaterülethez van csatolva. Azure Files a munkaterület alapértelmezett adattára, de a blob Storage-t is használhatja adattárként. További információ: [Azure Storage-beállítások](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Adatok hivatkozások konfigurálása

Most már hivatkozhat az adatokat a folyamatban, bemeneteként a folyamat lépéseit.

Egy adatforrás egy adott folyamat képviseli egy [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objektum.  `DataReference`Az objektum olyan adatokra mutat, amelyek a-ben vagy egy adattárból érhetők el. Szüksége van `DataReference`  a bemeneti lemezképekhez használt könyvtárra, a könyvtárra, amelyben az előkészített modell tárolódik, a címkék és a kimeneti könyvtár könyvtára.

```python
from azureml.data.data_reference import DataReference

input_images = DataReference(datastore=batchscore_blob,
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")

model_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")

label_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")

output_dir = PipelineData(name="scores",
                          datastore=def_data_store,
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>Állítsa be a számítási célnak

Azure Machine Learning a *számítási* (vagy *számítási cél*) a gépi tanulási folyamat számítási lépéseit végrehajtó gépekre vagy fürtökre vonatkozik. Létrehozhat például egy Azure Machine Learning-számítást a [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py) osztály használatával.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
        vm_size=vm_size,  # NC6 is GPU-enabled
        vm_priority='lowpriority',  # optional
        min_nodes=compute_min_nodes,
        max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws,
                                          compute_name,
                                          provisioning_config)

    compute_target.wait_for_completion(
        show_output=True,
        min_node_count=None,
        timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>A modell előkészítése

A pretrained modell használata előtt kell a modell letöltése és a munkaterülethez való regisztrálásához.

### <a name="download-the-pretrained-model"></a>Töltse le a pretrained modell

Töltse le a imagenet számítógépes látástechnológiai modellel (InceptionV3) származó <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Ezután bontsa ki az `models` almappába.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url = "http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Regisztrálja a modellt

A modell regisztrálása:

```python
import shutil
from azureml.core.model import Model

# register downloaded model
model = Model.register(
    model_path="models/inception_v3.ckpt",
    model_name="inception",  # This is the name of the registered model
    tags={'pretrained': "inception"},
    description="Imagenet trained tensorflow inception",
    workspace=ws)
```

## <a name="write-your-scoring-script"></a>A pontozó szkript írása

>[!Warning]
>A következő kód csak egy minta, amely a [minta notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb)által használt [batch_score.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) másolási fájlban található. A forgatókönyvhöz létre kell hoznia egy saját pontozási szkriptet.

A `batch_score.py` parancsfájl bemeneti képekhez fogadja *dataset_path*, imagenet modellek *model_dir,* , és arcokhoz *eredmények-label.txt* , *output_dir*.

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here

def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)
    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                        num_classes=classes_num,
                                                        is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")

        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>Hozza létre és futtassa a kötegelt pontozási folyamat

### <a name="prepare-the-run-environment"></a>A futtatási környezet előkészítése

Adja meg a parancsfájlt a conda-függőségeket. Erre az objektumra később szüksége lesz, amikor létrehozza a folyamat lépését.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

cd = CondaDependencies.create(
    pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Adja meg a paramétert a folyamat

Hozzon létre egy folyamat paramétert egy alapértelmezett értékkel rendelkező [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objektum használatával.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(
    name="param_batch_size",
    default_value=20)
```

### <a name="create-the-pipeline-step"></a>A folyamat lépés létrehozása

Hozza létre a folyamat lépéseit a parancsfájl, a környezeti konfiguráció és a paraméterek használatával. Adja meg a számítási célnak, akkor már csatlakoztatva a munkaterülethez parancsfájlja céljaként. Használat [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) hozhat létre a folyamatot. lépés.

```python
from azureml.pipeline.steps import PythonScriptStep
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images,
               "--model_name", "inception",
               "--label_dir", label_dir,
               "--output_dir", output_dir,
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>A folyamat futtatása

Most futtassa a folyamatot, és vizsgálja meg az előállított kimenetet. A kimenetnek az egyes bemeneti képekhez tartozó pontszáma van.

```python
import pandas as pd
from azureml.pipeline.core import Pipeline

# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(
    pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>A folyamat közzététele

Miután meggyőződött a Futtatás eredményéről, tegye közzé a folyamatot, hogy később különböző bemeneti értékekkel futtasson. Egy folyamat közzétételekor egy REST-végpontot kap. Ez a végpont fogadja a folyamat meghívását a [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py)használatával már beépített paraméterek készletével.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring",
    description="Batch scoring using Inception v3 model",
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>A folyamat újrafuttatása a REST-végpont használatával

A folyamat újrafuttatásához szüksége lesz egy Azure Active Directory hitelesítési fejléc-tokenre, amelyet a [AzureCliAuthentication osztályban](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)ismertet.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>További lépések

A teljes körű működés megjelenítéséhez próbálja ki a Batch pontozási jegyzetfüzetet a [githubban](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines), vagy lépjen az [Azure architektúra központba](/azure/architecture/reference-architectures/ai/batch-scoring-python) , és tekintse meg a minta megoldás architektúráját.
