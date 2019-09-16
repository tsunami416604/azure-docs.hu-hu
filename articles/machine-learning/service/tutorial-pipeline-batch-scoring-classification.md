---
title: 'Oktatóanyag: Azure ML-folyamatok kötegelt pontozáshoz'
titleSuffix: Azure Machine Learning
description: Hozzon létre egy ML-folyamatot a Batch-pontozás a képbesorolási modellen való futtatásához. A gépi tanulási folyamatok gyorsabbá, hordozhatóságot és újrahasznosítást tesznek jobbá a munkafolyamatban, így az infrastruktúra és az automatizálás helyett a szaktudásra, a gépi tanulásra koncentrálhat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/05/2019
ms.openlocfilehash: 15a11ba74262ec5a354f0cb3fe22c09167c8d5a6
ms.sourcegitcommit: d15b23e23328ce7502dd3d2846b49fd2d6d8209c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005390"
---
# <a name="use-azure-machine-learning-pipelines-for-batch-scoring"></a>Azure Machine Learning folyamatok használata a Batch pontozáshoz

Ebben az oktatóanyagban Azure Machine Learning folyamatokat használ a Batch-pontozási feladatok futtatásához. Ez a példa az előre betanított megerősítő [-v3](https://arxiv.org/abs/1512.00567) típusú, a nem címkézett képek besorolására szolgáló Tensorflow modellt használja. Egy folyamat létrehozása és közzététele után beállíthat egy REST-végpontot, hogy bármely platformon bármely HTTP-könyvtárból aktiválható legyen a folyamat.

A gépi tanulási folyamatok gyorsabbá, hordozhatóságot és újrahasznosítást tesznek jobbá a munkafolyamatban, így az infrastruktúra és az automatizálás helyett a szaktudásra, a gépi tanulásra koncentrálhat. [További információ az ml-folyamatokról](concept-ml-pipelines.md).

Ez az oktatóanyag a következő feladatokat ismerteti:

> [!div class="checklist"]
> * Munkaterület konfigurálása és mintaadatok letöltése
> * Adatobjektumok létrehozása a lekéréshez és a kimeneti adatmennyiséghez
> * A modell letöltése, előkészítése és regisztrálása a munkaterületen
> * Számítási célok kiépítése és pontozási szkript létrehozása
> * Folyamat létrehozása, futtatása és közzététele
> * REST-végpont engedélyezése a folyamat számára

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="prerequisites"></a>Előfeltételek

* Ha még nem rendelkezik Azure Machine Learning munkaterülettel vagy notebook virtuális géppel, fejezze be [a telepítési oktatóanyag 1. részét](tutorial-1st-experiment-sdk-setup.md) .
* A telepítési oktatóanyag befejezése után nyissa meg az **oktatóanyagok/tutorial-pipeline-batch-Scoring-Classification. ipynb** jegyzetfüzetet ugyanazzal a notebook-kiszolgálóval.

Ez az oktatóanyag a [githubon](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) is elérhető, ha saját [helyi környezetben](how-to-configure-environment.md#local)szeretné futtatni. Futtassa `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` a parancsot a szükséges csomagok beszerzéséhez.

## <a name="configure-workspace-and-create-datastore"></a>Munkaterület konfigurálása és adattár létrehozása

Munkaterület-objektum létrehozása a meglévő Azure Machine Learning munkaterületről. 
+ A [munkaterület](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) egy olyan osztály, amely elfogadja az Azure-előfizetést és az erőforrás-információkat. Létrehoz egy felhőalapú erőforrást is a modell futtatásának figyelésére és nyomon követésére. 

+ `Workspace.from_config()`beolvassa a **config. JSON** fájlt, és betölti a hitelesítési adatokat `ws`egy nevű objektumba. A `ws` a kód további részében használható ebben az oktatóanyagban.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Adattár létrehozása minta lemezképekhez

Szerezze be a nyilvános blob-tárolóból `sampledata` származó ImageNet próbaverzióját a fiókban. `pipelinedata` A `register_azure_blob_container()` hívással a név `images_datastore`alatt elérhetővé válik az adat a munkaterületen. Ezután adja meg a munkaterület alapértelmezett adattárát kimeneti adattárként, amelyet a folyamat kimenetének pontozásához használ.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-data-objects"></a>Adatobjektumok létrehozása

A folyamatok `DataReference` létrehozásakor az objektumok a munkaterület-adattárolók adatainak beolvasására szolgálnak, és `PipelineData` az objektumok a közbenső adatok átvitelére szolgálnak a folyamat lépései között.

> [!Important]
> Ez a Batch-pontozási példa csak egy folyamat lépését használja, de a használati esetekben több lépésből áll, a tipikus folyamat a következőket fogja tartalmazni:
>
> 1. Objektumok `DataReference` használata **bemenetként** a nyers adatok beolvasásához, bizonyos átalakítások végrehajtásához, majd `PipelineData` egy objektum **üzembe helyezéséhez** .
>
> 2. Az előző lépés `PipelineData` **kimeneti objektumát** a következő lépésekhez tartozó *bemeneti objektumként*használja.

Ebben a forgatókönyvben olyan `DataReference` objektumokat hoz létre, amelyek megfelelnek a bemeneti lemezképek és a besorolási címkék (y-test értékek) adattár-könyvtárainak. Hozzon létre egy `PipelineData` objektumot a Batch pontozási kimeneti adatokat is.

```python
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download"
                            )

label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download"                          
                         )

output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

## <a name="download-and-register-the-model"></a>A modell letöltése és regisztrálása

Töltse le az előre betanított Tensorflow modellt, hogy azt a folyamat batch pontozásához használja. Először hozzon létre egy helyi könyvtárat, ahol a modellt tárolja, majd töltse le és csomagolja ki.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Most regisztrálja a modellt a munkaterületen, amely lehetővé teszi, hogy egyszerűen lekérje azt a folyamat során. A statikus függvényben a `model_name` paraméter a modell megkereséséhez használt kulcs az SDK-ban. `register()`

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-remote-compute-target"></a>Távoli számítási cél létrehozása és csatolása

Mivel a ML-folyamatok nem futtathatók helyileg, futtatni kell őket a felhőalapú erőforrásokon. Ezekre a távoli számítási célokra hivatkozunk, amelyek újrafelhasználható virtuális számítási környezetek, amelyekben kísérleteket és ML-munkafolyamatokat futtatnak. Futtassa a következő kódot egy GPU-t támogató [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) cél létrehozásához, és csatolja azt a munkaterülethez. A számítási célokkal kapcsolatos további információkért tekintse meg a [fogalmi cikket](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target) .


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Pontozási parancsfájl írása

A pontozás végrehajtásához létre kell hoznia egy batch- `batch_scoring.py`pontozási parancsfájlt, és az aktuális könyvtárba kell írnia. A parancsfájl bemeneti képeket fogad, alkalmazza a besorolási modellt, és az előrejelzéseket egy eredmény-fájlba írja.

A szkript `batch_scoring.py` a következő paramétereket veszi át, amelyek az oktatóanyag későbbi részében létrehozott folyamat lépésből származnak:

- `--model_name`: a használt modell neve
- `--label_dir`: a `labels.txt` fájlt tároló könyvtár 
- `--dataset_path`: a bemeneti képeket tartalmazó könyvtár
- `--output_dir`: a szkript az adatokat és a kimenetet `results-label.txt` a következő könyvtárba fogja futtatni
- `--batch_size`: a modell futtatásához használt batch-méret

A folyamatok infrastruktúrája a `ArgumentParser` osztály használatával továbbítja a paramétereket a folyamat lépéseibe. Például az első argumentum `--model_name` alatti kódban a tulajdonság azonosítója `model_name`van megadva. A `main()` függvényben ez a tulajdonság a használatával `Model.get_model_path(args.model_name)`érhető el.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3
from azureml.core.model import Model

slim = tf.contrib.slim

parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
parser.add_argument('--model_name', dest="model_name", required=True)
parser.add_argument('--label_dir', dest="label_dir", required=True)
parser.add_argument('--dataset_path', dest="dataset_path", required=True)
parser.add_argument('--output_dir', dest="output_dir", required=True)
parser.add_argument('--batch_size', dest="batch_size", type=int, required=True)

args = parser.parse_args()

image_size = 299
num_channel = 3

# create output directory if it does not exist
os.makedirs(args.output_dir, exist_ok=True)


def get_class_label_dict(label_file):
    label = []
    proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


class DataIterator:
    def __init__(self, data_dir):
        self.file_paths = []
        image_list = os.listdir(data_dir)
        self.file_paths = [data_dir + '/' + file_name.rstrip() for file_name in image_list]
        self.labels = [1 for file_name in self.file_paths]

    @property
    def size(self):
        return len(self.labels)

    def input_pipeline(self, batch_size):
        images_tensor = tf.convert_to_tensor(self.file_paths, dtype=tf.string)
        labels_tensor = tf.convert_to_tensor(self.labels, dtype=tf.int64)
        input_queue = tf.train.slice_input_producer([images_tensor, labels_tensor], shuffle=False)
        labels = input_queue[1]
        images_content = tf.read_file(input_queue[0])

        image_reader = tf.image.decode_jpeg(images_content, channels=num_channel, name="jpeg_reader")
        float_caster = tf.cast(image_reader, tf.float32)
        new_size = tf.constant([image_size, image_size], dtype=tf.int32)
        images = tf.image.resize_images(float_caster, new_size)
        images = tf.divide(tf.subtract(images, [0]), [255])

        image_batch, label_batch = tf.train.batch([images, labels], batch_size=batch_size, capacity=5 * batch_size)
        return image_batch


def main(_):
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)
    count = 0

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
        with open(out_filename, "w") as result_file:
            i = 0
            while count < total_size and not coord.should_stop():
                test_images_batch = sess.run(test_images)
                file_names_batch = test_feeder.file_paths[i * args.batch_size:
                                                          min(test_feeder.size, (i + 1) * args.batch_size)]
                results = sess.run(probabilities, feed_dict={input_images: test_images_batch})
                new_add = min(args.batch_size, total_size - count)
                count += new_add
                i += 1
                for j in range(new_add):
                    result_file.write(os.path.basename(file_names_batch[j]) + ": " + label_dict[results[j]] + "\n")
                result_file.flush()
            coord.request_stop()
            coord.join(threads)

        shutil.copy(out_filename, "./outputs/")

if __name__ == "__main__":
    tf.app.run()

```

> [!TIP]
> Az ebben az oktatóanyagban található folyamat csak egyetlen lépésből áll, és egy fájlba írja a kimenetet, de a többlépéses folyamatok `ArgumentParser` esetében a segítségével megadhat egy könyvtárat, amely a kimeneti adatokat ír a következő lépésekhez. Tekintse meg a [jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) , amely példát mutat be az adatátvitelre `ArgumentParser` több folyamat lépései között a tervezési minta használatával.

## <a name="build-and-run-the-pipeline"></a>A folyamat létrehozása és futtatása

A folyamat futtatása előtt létre kell hoznia egy objektumot, amely meghatározza a parancsfájl `batch_scoring.py`által igényelt Python-környezetet és függőségeket. A fő függőség szükséges a Tensorflow, de az SDK-ból `azureml-defaults` is telepítheti a háttérben futó folyamatokat. Hozzon `RunConfiguration` létre egy objektumot a függőségek használatával, és a Docker és a Docker-GPU támogatását is megadhatja.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import CondaDependencies, RunConfiguration

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>A folyamat parametrizálja

Definiáljon egy egyéni paramétert a folyamathoz a köteg méretének szabályozásához. Miután közzétette és kitette a folyamatot egy REST-végponton keresztül, a konfigurált paraméterek is elérhetők lesznek, és a JSON-adattartalomban megadhatók a folyamat HTTP-kérelemmel való újrafuttatásakor.

Hozzon `PipelineParameter` létre egy objektumot, amely engedélyezi ezt a viselkedést, és adjon meg egy nevet és egy alapértelmezett értéket.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>A folyamat lépés létrehozása

A folyamat lépés egy olyan objektum, amely mindent magában foglal, amire szüksége lehet egy folyamat futtatásához, beleértve a következőket:

* környezeti és függőségi beállítások
* a folyamat futtatásához használandó számítási erőforrás
* bemeneti és kimeneti adatok, valamint bármely egyéni paraméter
* hivatkozás a lépés során futtatandó parancsfájlra vagy SDK-logikára

Több olyan osztály is létezik, amely a szülő osztálytól [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) örököl, hogy segítséget nyújtson egy lépés létrehozásához bizonyos keretrendszerek és stackek használatával. Ebben a példában a [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) osztály használatával határozhatja meg a lépés logikáját egyéni Python-parancsfájl használatával. Vegye figyelembe, hogy ha a parancsfájlhoz tartozó argumentum vagy bemenet a lépés lépésének vagy kimenetének, akkor azt `arguments` a `output` tömbben **, valamint a vagy** a `input` paraméterben **is meg kell határozni** . 

Egy objektum hivatkozása a `outputs` tömbben egy későbbi folyamat lépésének **bemenetként** válik elérhetővé, olyan helyzetekben, ahol több lépés is van.

```python
from azureml.pipeline.steps import PythonScriptStep

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_scoring.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config
)
```

A különböző lépéshez tartozó osztályok listáját a [Steps csomagban](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)tekintheti meg.

### <a name="run-the-pipeline"></a>A folyamat futtatása

Most futtatja a folyamatot. Először hozzon `Pipeline` létre egy objektumot a munkaterület-hivatkozással, és az Ön által létrehozott folyamat lépést. A `steps` paraméter a lépések tömbje, és ebben az esetben a Batch pontozásnak csak egy lépése van. Több lépésből álló folyamatok létrehozásához hajtsa végre a lépéseket ebben a tömbben.

Ezután a `Experiment.submit()` függvény használatával küldje el a folyamatot végrehajtásra. Az egyéni paramétert `param_batch_size`is megadhatja. A `wait_for_completion` függvény a folyamat létrehozása során megjeleníti a naplókat, ami lehetővé teszi az aktuális folyamat megtekintését.

> [!IMPORTANT]
> Az első folyamat futása nagyjából **15 percet**vesz igénybe, mivel az összes függőséget le kell tölteni, a rendszer létrehoz egy Docker-rendszerképet, és a Python-környezetet kiépíti/létrehozta. Az újbóli futtatása jelentősen kevesebb időt vesz igénybe, mivel ezek az erőforrások újra felhasználhatók. A teljes futási idő azonban az egyes folyamatokban futó parancsfájlok és folyamatok munkaterheléstől függ.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Kimenet letöltése és áttekintése

Futtassa a következő kódot a `batch_scoring.py` szkriptből létrehozott kimeneti fájl letöltéséhez, majd vizsgálja meg a pontozás eredményét.

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
```

<div>
<style scoped> .dataframe pedig tbody tr th: csak a-típusú {függőleges igazítás: középre;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Fájlnév</th>
      <th>Előrejelzés</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102. JPEG</td>
      <td>Rhodesian Ridgeback</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103. JPEG</td>
      <td>állvány</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104. JPEG</td>
      <td>írógép-billentyűzet</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105. JPEG</td>
      <td>selymes terrier</td>
    </tr>
    <tr>
      <td>4</td>
      <td>ILSVRC2012_val_00000106. JPEG</td>
      <td>Windsor-nyakkendő</td>
    </tr>
    <tr>
      <td>5</td>
      <td>ILSVRC2012_val_00000107. JPEG</td>
      <td>betakarító</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108. JPEG</td>
      <td>hegedű</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109. JPEG</td>
      <td>hangszóró</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110. JPEG</td>
      <td>kötény</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111. JPEG</td>
      <td>Amerikai homár</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-rest-endpoint"></a>Közzététel és Futtatás REST-végpontról

Futtassa a következő kódot a folyamat közzétételéhez a munkaterületen. A portál munkaterületén láthatja a folyamat metaadatait, beleértve a futtatási előzményeket és az időtartamokat is. A folyamatot manuálisan is futtathatja a portálról.

Emellett a folyamat közzététele lehetővé teszi a REST-végpontok számára a folyamat újrafuttatását bármely platform bármely HTTP-könyvtárából.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Ahhoz, hogy a folyamat a REST-végpontról fusson, először egy OAuth2 tulajdonos típusú hitelesítési fejlécre van szüksége. Ez a példa interaktív hitelesítést használ az illusztrációs célokra, de az automatizált vagy a fej nélküli hitelesítést igénylő üzemi forgatókönyvek esetében használja a szolgáltatás-elvi hitelesítést a [jegyzetfüzetben leírtak](https://aka.ms/pl-restep-auth)szerint.

A szolgáltatás-elvi hitelesítés magában foglalja az **alkalmazások regisztrálását** **Azure Active Directoryban**, az ügyfél titkos kulcsának létrehozását, majd a szolgáltatás egyszerű **szerepkörének** megadását a Machine learning-munkaterülethez. Ezután a [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) osztály használatával kezelheti az hitelesítési folyamatot. 

Mindkettő `InteractiveLoginAuthentication` `AbstractAuthentication`és `ServicePrincipalAuthentication` a öröklése`get_authentication_header()` , és mindkét esetben ugyanúgy használja a függvényt, hogy beolvassa a fejlécet.

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

A REST URL-cím `endpoint` beolvasása a közzétett folyamat objektumának tulajdonságában. A REST URL-címet a munkaterületen is megtalálhatja a portálon. Hozzon létre egy HTTP POST-kérelmet a végpontnak a hitelesítési fejléc megadásával. Emellett adjon hozzá egy JSON-adattartalom-objektumot a kísérlet nevével és a Batch size paraméterrel. Emlékeztetőként a rendszer átadja `param_batch_size` `batch_scoring.py` a parancsfájlt, mert azt `PipelineParameter` objektumként definiálta a lépés konfigurációjában.

A Futtatás elindítására vonatkozó kérelem elvégzése. A futtatási azonosító értékének beszerzéséhez nyissa meg a `Id` kulcsot a válasz szótárban.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Az új Futtatás állapotának figyeléséhez használja a futtatási azonosítót. Ez egy újabb 10-15 perces futtatást hajt végre, és az előző folyamat futtatásához hasonlóan fog kinézni, így ha nem kell egy másik folyamatot futtatnia, kihagyhatja a teljes kimenet megtekintését.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ne hajtsa végre ezt a szakaszt, ha más Azure Machine Learning oktatóanyagok futtatását tervezi.

### <a name="stop-the-notebook-vm"></a>A notebook virtuális gép leállítása

Ha Felhőbeli jegyzetfüzet-kiszolgálót használt, állítsa le a virtuális gépet, ha nem használja a költségeket.

1. A munkaterületen válassza a **notebook virtuális gépek**lehetőséget.
1. Válassza ki a virtuális gépet a listából.
1. Válassza a **Leállítás**lehetőséget.
1. Ha készen áll a kiszolgáló ismételt használatára, válassza az **Indítás**lehetőséget.

### <a name="delete-everything"></a>Mindent törölni

Ha nem tervezi a létrehozott erőforrások használatát, törölje őket, így nem számítunk fel díjat.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen.
1. Válassza ki a listában az Ön által létrehozott erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Adja meg az erőforráscsoport nevét. Ezután válassza a **Törlés** elemet.

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet is. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a Machine learning-folyamatok oktatóanyagában a következő feladatokat végezte el:

> [!div class="checklist"]
> * Egy távoli GPU számítási erőforráson való futtatáshoz szükséges környezeti függőségekkel rendelkező folyamat létrehozása
> * Létrehozott egy pontozási parancsfájlt a Batch-előrejelzések futtatásához egy előre betanított Tensorflow-modellel
> * Közzétett egy folyamatot, és engedélyezte, hogy a REST-végpontról fusson

Tekintse meg a [Jegyzetfüzet-tárházat](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) , amely további példákat tartalmaz a gépi tanulási SDK-val történő folyamatok létrehozásához.
