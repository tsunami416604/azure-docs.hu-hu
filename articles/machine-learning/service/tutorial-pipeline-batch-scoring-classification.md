---
title: 'Oktatóanyag: Gépi tanulási folyamatok kötegelt pontozáshoz'
titleSuffix: Azure Machine Learning
description: Hozzon létre egy gépi tanulási folyamatot a Batch-pontozás futtatásához Azure Machine Learning képbesorolási modellben. A gépi tanulási folyamatok gyorsabbá, hordozhatósággal és újrafelhasználással optimalizálják a munkafolyamatot, így az infrastruktúra és az automatizálás helyett a szakértelemre koncentrálhat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/05/2019
ms.openlocfilehash: 978cfa7926e7a035494aae11351c15a45c0251e4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350433"
---
# <a name="use-a-machine-learning-pipeline-for-batch-scoring"></a>Gépi tanulási folyamat használata kötegelt pontozáshoz

Ebben az oktatóanyagban a Batch-pontozási feladatok futtatásához a Azure Machine Learning egy folyamatát használja. A példa a megcímkézetlen rendszerképek besorolására szolgáló, az előre betanított [kezdeti és a v3-](https://arxiv.org/abs/1512.00567) as típusú, a rendszerhez tartozó neurális hálózat Tensorflow modelljét használja. A folyamat létrehozása és közzététele után beállíthat egy REST-végpontot, amely segítségével bármely platformon bármely HTTP-könyvtárból aktiválhatja a folyamatot.

A gépi tanulási folyamatok gyorsabbá, hordozhatósággal és újrafelhasználással optimalizálják a munkafolyamatot, így az infrastruktúra és az automatizálás helyett a szakértelemre koncentrálhat. [További információ a gépi tanulási folyamatokról](concept-ml-pipelines.md).

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * Munkaterület konfigurálása és mintaadatok letöltése
> * Adatobjektumok létrehozása a lekéréshez és a kimeneti adatmennyiséghez
> * A modell letöltése, előkészítése és regisztrálása a munkaterületen
> * Számítási célok kiépítése és pontozási szkript létrehozása
> * Folyamat létrehozása, futtatása és közzététele
> * REST-végpont engedélyezése a folyamat számára

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="prerequisites"></a>Előfeltételek

* Ha még nem rendelkezik Azure Machine Learning munkaterülettel vagy notebook virtuális géppel, fejezze be [a telepítési oktatóanyag 1. részét](tutorial-1st-experiment-sdk-setup.md).
* Amikor befejezte a telepítési oktatóanyagot, az *oktatóanyagok/tutorial-pipeline-batch-Scoring-Classification. ipynb* notebook megnyitásához használja ugyanazt a notebook-kiszolgálót.

Ha a telepítési oktatóanyagot saját [helyi környezetben](how-to-configure-environment.md#local)szeretné futtatni, a [githubon](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials)is elérheti az oktatóanyagot. Futtassa `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` a parancsot a szükséges csomagok beszerzéséhez.

## <a name="configure-workspace-and-create-a-datastore"></a>Munkaterület konfigurálása és adattár létrehozása

Munkaterület-objektum létrehozása a meglévő Azure Machine Learning munkaterületről.

- A [munkaterület](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) egy olyan osztály, amely elfogadja az Azure-előfizetést és az erőforrás-információkat. A munkaterület létrehoz egy felhőalapú erőforrást is, amellyel figyelheti és nyomon követheti a modell futtatását. 
- `Workspace.from_config()` beolvassa a `config.json` fájlt, majd betölti a hitelesítési adatokat egy `ws` nevű objektumba. Az oktatóanyag során a `ws` objektumot használjuk a kódban.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Adattár létrehozása minta lemezképekhez

A `pipelinedata` fiókban szerezze be a ImageNet próbaverzió nyilvános adatmintáját az `sampledata` nyilvános blob-tárolóból. Hívja meg a `register_azure_blob_container()` értéket, hogy az adat elérhető legyen a munkaterület számára a következő néven: `images_datastore`. Ezután állítsa be a munkaterület alapértelmezett adattárát kimeneti adattárként. A kimeneti adattárral szerzi be a folyamat kimenetét.

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

Folyamat létrehozásakor a `DataReference` objektum beolvassa az adatokat a munkaterület adattárból. A `PipelineData` objektum A köztes adatátvitelt A folyamat lépései között hajtja végre.

> [!Important]
> Az oktatóanyagban szereplő batch-pontozási példa csak egy folyamat lépését használja. A több lépésből álló használati esetekben a szokásos folyamat a következő lépéseket fogja tartalmazni:
>
> 1. A nyers adatok beolvasásához használja a `DataReference` objektumokat *bemenetként* , végezze el az átalakítást, majd a `PipelineData` objektum *kimenetét* .
>
> 2. Használja a `PipelineData` *kimeneti objektumot* az előző lépésben *bemeneti objektumként*. Ismételje meg a műveletet a következő lépésekhez.

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

Töltse le az előképzésen átadott Tensorflow modellt egy folyamat batch-pontozási funkciójának használatához. Először hozzon létre egy helyi könyvtárat, ahol a modellt tárolja. Ezután töltse le és bontsa ki a modellt.

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

Ezután regisztrálja a modellt a munkaterületen, így egyszerűen lekérheti a modellt a folyamat során. A statikus függvényben a `model_name` paraméter a modell megkereséséhez használt kulcs az SDK-ban. `register()`

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>A távoli számítási cél létrehozása és csatolása

A gépi tanulási folyamatokat nem lehet helyileg futtatni, így Felhőbeli erőforrásokon vagy *távoli számítási célokon*futtathatja őket. A távoli számítási cél egy újrafelhasználható virtuális számítási környezet, amelyben kísérleteket és gépi tanulási munkafolyamatokat futtat. 

Futtassa a következő kódot egy GPU-t támogató [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) cél létrehozásához, majd csatolja azt a munkaterülethez. További információ a számítási célokról: [fogalmi cikk](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).


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

A pontozás végrehajtásához hozzon létre egy `batch_scoring.py` nevű batch-pontozási parancsfájlt, majd írja be az aktuális könyvtárba. A parancsfájl bemeneti képeket fogad, alkalmazza a besorolási modellt, majd az előrejelzéseket egy eredmény-fájlba írja.

A `batch_scoring.py` parancsfájl a következő paramétereket fogadja el, amelyek az oktatóanyag későbbi részében létrehozott folyamat lépésből származnak:

- `--model_name`: A használt modell neve.
- `--label_dir`: A `labels.txt` fájl tárolására szolgáló könyvtár.
- `--dataset_path`: A bemeneti képeket tartalmazó könyvtár.
- `--output_dir`: A `results-label.txt` fájl kimeneti könyvtára, miután a parancsfájl futtatta a modellt az adattípuson.
- `--batch_size`: A modell futtatásához használt batch-méret.

A folyamat-infrastruktúra a `ArgumentParser` osztály használatával továbbítja a paramétereket a folyamat lépéseibe. A következő kódban például az első `--model_name` argumentum adja meg a `model_name` tulajdonságot. A `main()` függvényben `Model.get_model_path(args.model_name)` a tulajdonság elérésére szolgál.


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
> Az oktatóanyagban szereplő folyamat csak egyetlen lépésből áll, és egy fájlba írja a kimenetet. A többlépéses folyamatok esetében `ArgumentParser` használatával is definiálhat egy könyvtárat, amely a kimeneti adatokat ír a következő lépésekhez. Ha például az `ArgumentParser` kialakítási minta használatával kívánja átadni az adattovábbítást több folyamat lépései között, tekintse meg a [jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-and-run-the-pipeline"></a>A folyamat létrehozása és futtatása

A folyamat futtatása előtt hozzon létre egy objektumot, amely meghatározza a Python-környezetet, és létrehozza azokat a függőségeket, amelyekhez a `batch_scoring.py` parancsfájl szükséges. A fő függőség Tensorflow, de az SDK-ból is telepítheti a `azureml-defaults` elemet a háttérbeli folyamatokhoz. Hozzon létre egy `RunConfiguration` objektumot a függőségek használatával. Továbbá a Docker és a Docker-GPU támogatását is megadhatja.

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

Definiáljon egy egyéni paramétert a folyamathoz a köteg méretének szabályozásához. A folyamat közzététele és a REST-végponton keresztüli közzététel után a konfigurált paraméterek is elérhetővé válnak. A JSON-adattartalomban egyéni paramétereket is megadhat, ha a folyamatot HTTP-kérelemmel futtatja újra.

Hozzon létre egy `PipelineParameter` objektumot a viselkedés engedélyezéséhez, valamint a név és az alapértelmezett érték megadásához.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>A folyamat lépés létrehozása

A folyamat lépés egy olyan objektum, amely minden olyan objektumot magában foglal, amely a folyamat futtatásához szükséges, beleértve a következőket:

* környezeti és függőségi beállítások
* a folyamat futtatásához használandó számítási erőforrás
* bemeneti és kimeneti adatok, valamint bármely egyéni paraméter
* Hivatkozás a lépés során futtatandó parancsfájlra vagy SDK-logikára

Több osztály örökli a [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py)szülő osztályt. Kiválaszthatja az osztályokat, hogy meghatározott keretrendszerek vagy stackek használatával hozzon létre egy lépést. Ebben a példában a [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) osztály használatával határozhatja meg a lépés logikáját egyéni Python-parancsfájl használatával. Ha a parancsfájlhoz tartozó argumentum vagy a lépés kimenete vagy a lépéshez tartozó érték, akkor az argumentumot a `arguments` tömbben, *valamint* a `input` vagy a `output` paraméterben *kell megadni.* 

Olyan helyzetekben, ahol több lépés is van, az `outputs` tömbben lévő objektum-hivatkozás egy későbbi folyamat lépésének *bemenetként* válik elérhetővé.

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

A különböző lépésekhez használható osztályok listáját a [Steps csomagban](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)tekintheti meg.

### <a name="run-the-pipeline"></a>A folyamat futtatása

Most futtassa a folyamatot. Először hozzon létre egy `Pipeline` objektumot a munkaterület-hivatkozás és az Ön által létrehozott folyamat lépés használatával. A `steps` paraméter a lépések tömbje. Ebben az esetben a Batch pontozásnak csak egy lépése van. Több lépésből álló folyamatok létrehozásához helyezze a lépéseket sorrendben ebben a tömbben.

Ezután a `Experiment.submit()` függvénnyel küldje el a folyamatot a folyamat végrehajtásához. Az egyéni paramétert `param_batch_size`is megadhatja. A `wait_for_completion` függvény a folyamat létrehozása során megjeleníti a naplókat. A naplók segítségével megtekintheti az aktuális folyamatot.

> [!IMPORTANT]
> Az első folyamat futtatása körülbelül *15 percet*vesz igénybe. Minden függőséget le kell tölteni, létre kell hozni egy Docker-rendszerképet, és a Python-környezet kiépítve és létrehozva. A folyamat újbóli futtatása jelentősen kevesebb időt vesz igénybe, mivel ezek az erőforrások a létrehozás helyett újra felhasználhatók. A folyamat teljes futási ideje azonban a parancsfájlok és az egyes folyamatokban futó folyamatok mennyiségétől függ.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Kimenet letöltése és áttekintése

Futtassa a következő kódot a `batch_scoring.py` parancsfájlból létrehozott kimeneti fájl letöltéséhez. Ezután vizsgálja meg a pontozás eredményeit.

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

## <a name="publish-and-run-from-a-rest-endpoint"></a>Közzététel és Futtatás REST-végpontról

Futtassa a következő kódot a folyamat közzétételéhez a munkaterületen. A Azure Portal munkaterületén láthatja a folyamat metaadatait, beleértve a futtatási előzményeket és az időtartamokat is. A folyamatot manuálisan is futtathatja a portálról.

A folyamat közzététele lehetővé teszi egy REST-végpont használatát, amellyel bármilyen platformon futtathatja a folyamatot bármely HTTP-könyvtárból.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

A folyamat REST-végpontból való futtatásához szüksége van egy OAuth2-tulajdonos típusú hitelesítési fejlécre. Az alábbi példa interaktív hitelesítést használ (illusztrációs célokra), de az automatizált vagy a fej nélküli hitelesítést igénylő üzemi forgatókönyvek esetében használja az egyszerű szolgáltatás hitelesítését a [jegyzetfüzetben leírtak](https://aka.ms/pl-restep-auth)szerint.

Az egyszerű szolgáltatás hitelesítése magában foglalja az *alkalmazás regisztrációjának* létrehozását *Azure Active Directory*-ben. Először létrehoz egy ügyfél-titkos kulcsot, majd megadja a szolgáltatás elsődleges *szerepkörének hozzáférését* a Machine learning-munkaterülethez. A hitelesítési folyamat kezeléséhez használja a [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) osztályt. 

A `InteractiveLoginAuthentication` és a `ServicePrincipalAuthentication` egyaránt örökli a `AbstractAuthentication` értéket. Mindkét esetben használja a `get_authentication_header()` függvényt ugyanúgy, ahogy beolvassa a fejlécet:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

A REST URL-cím beolvasása a közzétett folyamat objektum `endpoint` tulajdonságával. A REST URL-címet a munkaterületen található Azure Portal is megtalálhatja. 

Hozzon létre egy HTTP POST-kérelmet a végpontnak. Adja meg a hitelesítési fejlécet a kérelemben. Adjon hozzá egy olyan JSON-adattartalom objektumot, amely rendelkezik a kísérlet nevével és a Batch size paraméterrel. Az oktatóanyag korábbi részében leírtak szerint a `param_batch_size` átkerül a `batch_scoring.py` parancsfájlba, mert `PipelineParameter` objektumként lett meghatározva a lépés konfigurációjában.

A Futtatás elindítására vonatkozó kérelem elvégzése. Vegyen fel kódot a `Id` kulcs eléréséhez a válasz szótárában a futtatási azonosító értékének lekéréséhez.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Az új Futtatás állapotának figyeléséhez használja a futtatási azonosítót. Az új Futtatás újabb 10-15 percig tart. 

Az új Futtatás az oktatóanyag korábbi részében futtatott folyamathoz hasonlóan fog kinézni. Dönthet úgy, hogy nem jeleníti meg a teljes kimenetet.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ne hajtsa végre ezt a szakaszt, ha más Azure Machine Learning oktatóanyagokat szeretne futtatni.

### <a name="stop-the-notebook-vm"></a>A notebook virtuális gép leállítása

Ha Felhőbeli notebook-kiszolgálót használt, a költségek csökkentéséhez állítsa le a virtuális gépet, ha a Your ' e nem használja:

1. A munkaterületen válassza a **notebook virtuális gépek**lehetőséget.
1. A virtuális gépek listájában válassza ki a leállítani kívánt virtuális gépet.
1. Válassza a **Leállítás**lehetőséget.
1. Ha készen áll a kiszolgáló ismételt használatára, válassza az **Indítás**lehetőséget.

### <a name="delete-everything"></a>Mindent törölni

Ha nem tervezi a létrehozott erőforrások használatát, törölje őket, így nem számítunk fel díjat:

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok**lehetőséget.
1. Az erőforráscsoportok listájában válassza ki a létrehozott erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Adja meg az erőforráscsoport nevét. Ezután válassza a **Törlés**lehetőséget.

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet is. Jelenítse meg a munkaterület tulajdonságait, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a Machine learning-folyamatok oktatóanyagában a következő feladatokat végezte el:

> [!div class="checklist"]
> * Egy távoli GPU számítási erőforráson való futtatáshoz szükséges környezeti függőségekkel rendelkező folyamat létrehozása.
> * Létrehozott egy pontozási parancsfájlt a kötegelt előrejelzések futtatásához egy előre elkészített Tensorflow-modell használatával.
> * Közzétett egy folyamatot, és engedélyezte, hogy a REST-végpontról fusson.

Ha további példákat szeretne a folyamatok létrehozásához a Machine learning SDK használatával, tekintse meg a [Jegyzetfüzet-tárházat](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
