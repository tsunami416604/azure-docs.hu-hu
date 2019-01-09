---
title: Batch-előrejelzések futtatunk nagy mennyiségű adat
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan előrejelzéseket batch aszinkron módon történik a nagy mennyiségű adat Azure Machine Learning szolgáltatás használatával.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: adac498b2f1e3331497c08f41558575c06b5823c
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102941"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-service"></a>Batch-előrejelzések futó nagy méretű adatkészleteket az Azure Machine Learning szolgáltatással

Ebből a cikkből megismerheti, hogyan adatelemzésre nagy mennyiségű adat az aszinkron módon történik, az Azure Machine Learning szolgáltatás használatával fogjuk.

Batch előrejelzési (vagy kötegelt pontozási) biztosít a költséghatékony következtetésekhez aszinkron alkalmazások páratlan átviteli sebességgel. Batch-előrejelzési folyamatok üzemi környezetben elérhető adatokat több terabájtnyi következtetésekhez végrehajtásához méretezheti. Batch-előrejelzési adatokat fire és elfelejt nagy méretű adatok gyűjteménye, nagy átviteli sebességet van optimalizálva.

>[!TIP]
> Ha a rendszer a közel valós idejű feldolgozás (egy dokumentumon vagy kis számú dokumentumok gyors feldolgozásához) van szüksége, használja a [valós idejű pontozási](how-to-consume-web-service.md) batch előrejelzési helyett.

A következő lépésekben hozzon létre egy [machine learning-folyamat](concept-ml-pipelines.md) regisztrálni egy imagenet számítógépes látástechnológiai modellel ([kezdetek-V3](https://arxiv.org/abs/1512.00567)). Ezután használja ki a pretrained modellt kötegelt pontozási a lemezkép érhető el az Azure Blob storage-fiókban. Ezek a lemezképek használt pontozási címke nélküli a lemezképeket a [épít](http://image-net.org/) adatkészlet.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree).

- Állítsa be a fejlesztési környezetet az Azure Machine Learning SDK telepítése. További információkért lásd: [a fejlesztési környezet konfigurálása az Azure Machine Learning](how-to-configure-environment.md).

- Hozzon létre egy Azure Machine Learning-munkaterületet, amely tárolja az folyamat-erőforrások. A következő kódot használhatja, vagy további beállításokért lásd: [munkaterület konfigurációs fájl létrehozása](how-to-configure-environment.md#workspace).

  ```python
  ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
  ```

## <a name="set-up-machine-learning-resources"></a>Állítsa be a machine learning-erőforrások

Állítsa be a folyamat futtatásához szükséges erőforrásokat az alábbi lépéseket:

- Elérni az adattárhoz, amely már rendelkezik a modell imagenet, bemeneti címke és képek pontszámot rendelni az (ez már be van állítva az Ön számára).
- Egy adattár beállítása a kimenetek tárolásához.
- Konfigurálása `DataReference` objektumokat, hogy az adatok az előző állomásához mutasson.
- Állítsa be a számítási gépek vagy fürtök, ahol a folyamat lépései futni fog.

### <a name="access-the-datastores"></a>Hozzáférés az adattárolók

Első lépésként elérni az adattárhoz, amely rendelkezik a modell, a címkék és a képek.

Egy nyilvános blob-tárolóba, nevű használni kívánt *sampledata*, a a *pipelinedata* fiókot, amely tárolja a lemezképeket a épít értékelési készletből. Az adattár neve a nyilvános tárolókban *images_datastore*. Az adattároló regisztrálja a munkaterülethez:

```python
# Public blob container details
account_name = "pipelinedata"
datastore_name="images_datastore"
container_name="sampledata"
 
batchscore_blob = Datastore.register_azure_blob_container(ws,
                      datastore_name=datastore_name,
                      container_name= container_name,
                      account_name=account_name,
                      overwrite=True)
```

Következő lépésként állítsa be az alapértelmezett adattár használata a kimenetek.

A munkaterület létrehozásakor [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) és [a Blob storage-](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) alapértelmezés szerint a munkaterülethez csatlakozik. Az Azure Files a munkaterülethez tartozó alapértelmezett datastore, de is használhatja a Blob storage-adattárhoz. További információkért lásd: [az Azure tárolási lehetőségeinek](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Adatok hivatkozások konfigurálása

Most már hivatkozhat az adatokat a folyamatban, bemeneteként a folyamat lépéseit.

Egy adatforrás egy adott folyamat képviseli egy [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objektum. A `DataReference` objektum él vagy érhető el egy adattárolót adatok mutat. Szükséges `DataReference`  objektumokat a könyvtár, bemeneti képekhez, a címtár, amely a pretrained modellben tárolja, a címkék és a kimeneti könyvtárat.

```python
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

Az Azure Machine Learning *számítási* (vagy *számítási célt*) a gépek vagy fürtök, amelyek a machine learning folyamatban, a számítási lépésekkel vonatkozik. Létrehozhat például egy `Azure Machine Learning compute`.

```python
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
                     vm_size = vm_size, # NC6 is GPU-enabled
                     vm_priority = 'lowpriority', # optional
                     min_nodes = compute_min_nodes, 
                     max_nodes = compute_max_nodes)

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

Töltse le a imagenet számítógépes látástechnológiai modellel (InceptionV3) származó <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Bontsa ki azt a `models` almappába.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Regisztrálja a modellt

Itt látható, hogyan regisztrálja a modellt:

```python
import shutil
from azureml.core.model import Model

# register downloaded model 
model = Model.register(
        model_path = "models/inception_v3.ckpt",
        model_name = "inception", # This is the name of the registered model
        tags = {'pretrained': "inception"},
        description = "Imagenet trained tensorflow inception",
        workspace = ws)
```

## <a name="write-your-scoring-script"></a>A pontozó szkript írása

>[!Warning]
>Az alábbi kód látható csak egy minta mi található a [batch_score.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) használják a [minta notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb). A forgatókönyvnek a saját pontozó szkript létrehozása kell.

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

A folyamat létrehozását, így most már összességében tudnivalót rendelkezik.

### <a name="prepare-the-run-environment"></a>A futtatási környezet előkészítése

Adja meg a parancsfájlt a conda-függőségeket. Később szüksége lesz az objektum, a folyamat lépés létrehozásakor.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Adja meg a paramétert a folyamat

Hozzon létre egy folyamatot a paraméter használatával egy [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objektum alapértelmezett értékkel.

```python
batch_size_param = PipelineParameter(
                    name="param_batch_size", 
                    default_value=20)
```

### <a name="create-the-pipeline-step"></a>A folyamat lépés létrehozása

A folyamat lépés létrehozása a parancsfájl, a környezet konfigurációját és a paraméterek használatával. Adja meg a számítási célnak, akkor már csatlakoztatva a munkaterülethez parancsfájlja céljaként. Használat [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) hozhat létre a folyamatot. lépés.

```python
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

Most futtathatja a folyamatot, és vizsgálja meg a kimenetet naplóadatokat. A kimenet az egyes bemeneti kép megfelelő pontszámot tartalmaz.

```python
# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

import pandas as pd
df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>A folyamat közzététele

Miután a Futtatás eredményét elégedettek vagyunk, a folyamat közzétételét, így is futtatható legyen más bemeneti értékekkel később. Amikor közzétesz egy folyamatot, egy REST-végpont kap. Ez a végpont fogad el, a folyamat már beépíteni, a paraméterek készletével együtt hajtják [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", 
    description="Batch scoring using Inception v3 model", 
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>A folyamat ismételt futtatásához használható a REST-végpont használatával

Újra futtathatja a folyamatot, szüksége lesz egy Azure Active Directory hitelesítési fejléc tokent leírtak szerint [AzureCliAuthentication osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint, 
        headers=aad_token, 
        json={"ExperimentName": "batch_scoring",
               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
from azureml.pipeline.core.run import PipelineRun
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>További lépések

Tekintse meg a működő – teljes körű, próbálja meg a kötegelt pontozási jegyzetfüzetben [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

