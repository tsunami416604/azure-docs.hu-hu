---
title: 'Oktatóanyag: ML-folyamatok kötegelt pontozáshoz'
titleSuffix: Azure Machine Learning
description: Ebben az oktatóanyagban egy gépi tanulási folyamatot hoz létre egy lemezképbesorolási modell kötegelt pontozásának végrehajtásához. Az Azure Machine Learning lehetővé teszi, hogy az infrastruktúra és az automatizálás helyett a gépi tanulásra összpontosítson.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: laobri
ms.date: 03/11/2020
ms.openlocfilehash: 1ccd7a7f33c6ee5cab8b7173d8eb93365b6cb587
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472220"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Oktatóanyag: Azure Machine Learning-folyamat létrehozása kötegelt pontozáshoz

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan hozhat létre egy folyamatot az Azure Machine Learningben egy kötegelt pontozási feladat futtatásához. A gépi tanulási folyamatok gyorsatással, hordozhatósággal és újrafelhasználással optimalizálják a munkafolyamatot, így ön az infrastruktúra és az automatizálás helyett a gépi tanulásra összpontosíthat. Miután létrehoz egy folyamatot, és közzétesz egy FOLYAMATot, konfigurálhat egy REST-végpontot, amely bármely platform bármely HTTP-tárából elindíthatja a folyamatot. 

A példa egy előre betanított [Inception-V3](https://arxiv.org/abs/1512.00567) konvolúciós neurális hálózati modelltensaxolás i címkézetlen lemezképek besorolásához használ. [További információ a gépi tanulási folyamatokról.](concept-ml-pipelines.md)

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * Munkaterület konfigurálása 
> * Mintaadatok letöltése és tárolása
> * Adatkészlet-objektumok létrehozása adatok beolvasásához és kimenetéhez
> * A modell letöltése, előkészítése és regisztrálása a munkaterületen
> * Számítási célok kiépítése és pontozási parancsfájl létrehozása
> * Az `ParallelRunStep` osztály használata aszinkronizálási kötegpontozáshoz
> * Folyamat létrehozása, futtatása és közzététele
> * REST-végpont engedélyezése a folyamathoz

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

## <a name="prerequisites"></a>Előfeltételek

* Ha még nem rendelkezik Azure Machine Learning-munkaterülettel vagy virtuális notebook-géppel, töltse ki [a telepítési oktatóanyag első részét.](tutorial-1st-experiment-sdk-setup.md)
* Amikor befejezte a telepítő oktató, használja ugyanazt a notebook-kiszolgálót az *oktatóanyagok/machine-learning-pipelines-advanced/tutorial-pipeline-batch-scoring-classification.ipynb* notebook megnyitásához.

Ha a telepítő oktatóanyagot saját [helyi környezetben](how-to-configure-environment.md#local)szeretné futtatni, akkor elérheti a [GitHub oktatóanyagát.](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) Fuss, `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps pandas requests` hogy a szükséges csomagokat.

## <a name="configure-workspace-and-create-a-datastore"></a>Munkaterület konfigurálása és adattár létrehozása

Hozzon létre egy munkaterületi objektumot a meglévő Azure Machine Learning-munkaterületről.

- A [munkaterület](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) olyan osztály, amely elfogadja az Azure-előfizetési és erőforrás-adatokat. A munkaterület egy felhőalapú erőforrást is létrehoz, amely a modellfuttatások figyelésére és nyomon követésére használható. 
- `Workspace.from_config()`beolvassa `config.json` a fájlt, majd betölti `ws`a hitelesítésrészleteit egy objektumnevű objektumba. Az `ws` objektum az oktatóanyag ban használatos a kódban.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

## <a name="create-a-datastore-for-sample-images"></a>Adattár létrehozása mintaképekhez

A `pipelinedata` fiókban az ImageNet kiértékelése nyilvános `sampledata` adatok minta a nyilvános blob tárolóból. Hívás, `register_azure_blob_container()` hogy az adatok elérhetők legyenek a munkaterület en néven. `images_datastore` Ezután állítsa be a munkaterület alapértelmezett adattár, mint a kimeneti adattár. A kimeneti adattár segítségével pontozhatja a kimenetet a folyamatban.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Adatkészlet-objektumok létrehozása

Folyamatok létrehozásakor `Dataset` az objektumok a munkaterületi adattárakból `PipelineData` történő adatok olvasására szolgálnak, az objektumok pedig a köztes adatok átvitelére szolgálnak a folyamat lépései között.

> [!Important]
> Az oktatóanyagban szereplő kötegelt pontozási példa csak egy folyamatlépést használ. Több lépésből álló használati esetekben a tipikus folyamat a következő lépéseket fogja tartalmazni:
>
> 1. Az `Dataset` objektumok beviteleként nyers adatok beolvasása, átalakítás `PipelineData` végrehajtása, majd egy objektum *kimenete.* *inputs*
>
> 2. `PipelineData` Az előző lépés *kimeneti objektumát* *használja bemeneti objektumként.* Ismételje meg a következő lépésekhez.

Ebben a forgatókönyvben `Dataset` olyan objektumokat hoz létre, amelyek megfelelnek a bemeneti lemezképek és a besorolási címkék (y-teszt értékek) adattárkönyvtárainak. A kötegelt pontozási kimeneti adatokhoz is létrehozhat egy `PipelineData` objektumot.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/*.txt"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Ezután regisztrálja az adatkészleteket a munkaterületre.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>A modell letöltése és regisztrálása

Töltse le az előre betanított Tensorflow-modellt kötegelt pontozáshoz egy folyamatban. Először hozzon létre egy helyi könyvtárat, ahol tárolja a modellt. Ezután töltse le és bontsa ki a modellt.

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

Ezután regisztrálja a modellt a munkaterületre, így könnyen lekérheti a modellt a folyamat során. A `register()` statikus függvényben `model_name` a paraméter az a kulcs, amelyet a modell sdk-ben való megkereséséhez használ.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>A távoli számítási cél létrehozása és csatolása

A gépi tanulási folyamatok nem futtathatók helyileg, ezért felhőalapú erőforrásokon vagy *távoli számítási célokon*futtathatja őket. A távoli számítási cél egy újrafelhasználható virtuális számítási környezet, ahol kísérleteket és gépi tanulási munkafolyamatokat futtat. 

Futtassa a következő kódot [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) egy GPU-kompatibilis cél létrehozásához, majd csatolja a munkaterülethez. A számítási célokról további információt a [koncepcionális cikkben](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)talál.


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

A pontozáshoz hozzon létre egy `batch_scoring.py`batch pontozási parancsfájlt, amelyet a programnak kell létrehoznia, majd írja be az aktuális könyvtárba. A parancsfájl bemeneti lemezképeket, alkalmazza a besorolási modellt, majd az előrejelzéseket egy eredményfájlba adja ki.

A `batch_scoring.py` parancsfájl a következő paramétereket veszi `ParallelRunStep` igénybe, amelyek a később létrehozott dokumentumokból átjutnak:

- `--model_name`: A használt modell neve.
- `--labels_name`: A `Dataset` `labels.txt` fájlt tartalmazó fájl neve.

A csővezeték-infrastruktúra `ArgumentParser` az osztály segítségével adja át a paramétereket a folyamat lépések. A következő kódban például az `--model_name` első argumentum `model_name`a tulajdonságazonosítót kapja. A `init()` függvényben `Model.get_model_path(args.model_name)` a tulajdonság elérésére szolgál.


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

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict():
    label = []
    proto_as_ascii_lines = tf.gfile.GFile("labels.txt").readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_name', dest="labels_name", required=True)
    args, _ = parser.parse_known_args()

    workspace = Run.get_context(allow_offline=False).experiment.workspace
    label_ds = Dataset.get_by_name(workspace=workspace, name=args.labels_name)
    label_ds.download(target_path='.', overwrite=True)

    label_dict = get_class_label_dict()
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> Az oktatóanyagban lévő folyamat csak egy lépésből áll, és a kimenetet egy fájlba írja. Többlépéses folyamatok esetén könyvtár `ArgumentParser` definiálására is használható a kimeneti adatok írásához a következő lépésekbe történő bevitelhez. A `ArgumentParser` tervezési minta használatával több folyamatlépés között történő adatátadást a [jegyzetfüzet ben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)láthat.

## <a name="build-the-pipeline"></a>A csővezeték létrehozása

A folyamat futtatása előtt hozzon létre egy objektumot, amely `batch_scoring.py` meghatározza a Python-környezetet, és létrehozza a parancsfájl által igényelt függőségeket. A fő függőség szükséges Tensorflow, de `azureml-defaults` a háttér folyamatok is telepíthető. Objektum `RunConfiguration` létrehozása a függőségek használatával. Adja meg a Docker és a Docker-GPU támogatását is.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>A parancsfájl burkolórendszerének létrehozása

Hozza létre a folyamatlépés a parancsfájl, a környezeti konfiguráció és a paraméterek használatával. Adja meg a munkaterülethez már csatolt számítási célt.

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>A folyamatlépés létrehozása

A folyamatlépés olyan objektum, amely mindent magába foglal, amire a folyamat futtatásához szüksége van, beleértve a következőket:

* Környezeti és függőségi beállítások
* A folyamat futtatásához a számítási erőforrás
* Bemeneti és kimeneti adatok, valamint az egyéni paraméterek
* Hivatkozás parancsfájlra vagy SDK-logikára a lépés során

Több osztály örökli a [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py)szülőosztályt. Kiválaszthatja az osztályokat, hogy meghatározott keretrendszereket vagy halmokat használjon egy lépés létrehozásához. Ebben a példában `ParallelRunStep` az osztály segítségével határozza meg a lépéslogikát egy egyéni Python-parancsfájl használatával. Ha a parancsfájl argumentuma a lépés bemenete vagy a lépés kimenete, akkor az argumentumot `input` *mind* a tömbben, *mind* a `arguments` `output` paraméterben meg kell határozni. 

Olyan esetekben, ahol egynél több lépés van, a `outputs` tömbben egy objektumhivatkozás válik elérhetővé egy későbbi folyamatlépés *bemeneteként.*

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

batch_score_step = ParallelRunStep(
    name="parallel-step-test",
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    models=[model],
    arguments=["--model_name", "inception",
               "--labels_name", "label_ds"],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

A különböző lépéstípusokhoz használható osztályok listáját a lépések [csomagjában tetszés szerint](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

## <a name="submit-the-pipeline"></a>A folyamat beküldése

Most futtassa a vezetéket. Először hozzon létre egy `Pipeline` objektumot a munkaterületi hivatkozás és a létrehozott folyamatlépés használatával. A `steps` paraméter lépések tömbje. Ebben az esetben csak egy lépés van a kötegelt pontozáshoz. Ha több lépésből álló folyamatokat szeretne építeni, helyezze a lépéseket sorrendbe ebben a tömbben.

Ezután használja `Experiment.submit()` a függvényt a folyamat végrehajtásra történő elküldéséhez. Megadhatja az egyéni `param_batch_size`paramétert is. A `wait_for_completion` függvény a folyamat összeállítási folyamata során adja ki a naplókat. A naplók segítségével megtekintheti az aktuális előrehaladást.

> [!IMPORTANT]
> Az első csővezeték-futtatás nagyjából *15 percet*vesz igénybe. Minden függőséget le kell tölteni, létre jön egy Docker-rendszerkép, és a Python-környezet kivan építve és létrehozva. A folyamat újra futtatása lényegesen kevesebb időt vesz igénybe, mert ezeket az erőforrásokat a létrehozott helyett újra felhasználja. Azonban a folyamat teljes futási ideje a parancsfájlok munkaterhelésétől és az egyes folyamatlépésekben futó folyamatoktól függ.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Kimenet letöltése és áttekintése

Futtassa a következő kódot a `batch_scoring.py` parancsfájlból létrehozott kimeneti fájl letöltéséhez. Ezután fedezze fel a pontozási eredményeket.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>REST-végpont közzététele és futtatása

Futtassa a következő kódot a folyamat közzétételéhez a munkaterületen. Az Azure Machine Learning-stúdió ban a munkaterületen megtekintheti a folyamat metaadatait, beleértve a futtatási előzményeket és az időtartamokat. A folyamatot manuálisan is futtathatja a stúdióból.

A folyamat közzététele lehetővé teszi, hogy a REST-végpont, amely segítségével futtathatja a folyamatot bármely HTTP-tár bármely platformon.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

A folyamat futtatásához a REST-végpont, szüksége van egy OAuth2 bemutatóra szóló típusú hitelesítési fejléc. A következő példa interaktív hitelesítést használ (szemléltetéscéljából), de a legtöbb olyan éles környezetben, amely automatikus vagy fej nélküli hitelesítést igényel, használja az egyszerű szolgáltatás hitelesítését [a jelen cikkben leírtak szerint.](how-to-setup-authentication.md)

Az egyszerű szolgáltatás hitelesítése magában foglalja *az alkalmazásregisztráció* létrehozását az *Azure Active Directoryban.* Először létrehoz egy ügyféltitkos kulcsot, majd hozzáférést biztosít a szolgáltatás egyszerű *szerepkör-hozzáférés* a gépi tanulási munkaterülethez. Az [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) osztály segítségével kezelheti a hitelesítési folyamatot. 

[`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) Mindkettő, `ServicePrincipalAuthentication` és `AbstractAuthentication`öröklik a . Mindkét esetben ugyanúgy [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) használja a függvényt a fejléc beolvasásához:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

A REST URL-címbe késelése a közzétett folyamatobjektum `endpoint` tulajdonságából. A REST-URL-cím a munkaterületen az Azure Machine Learning stúdióban is megtalálható. 

Http POST-kérelem létrehozása a végpontra. Adja meg a hitelesítési fejlécet a kérelemben. Adjon hozzá egy JSON hasznos adatobjektumot, amely rendelkezik a kísérlet nevével és a kötegméret paraméterrel. Amint azt korábban `param_batch_size` az oktatóanyag, `batch_scoring.py` átjut a parancsfájlba, mert a lépés konfigurációban `PipelineParameter` objektumként definiálta.

Kérje meg a futtatás indítását. A kulcs nak `Id` a válaszszótárból való eléréséhez a kulcs eléréséhez kódot is mellékeljen a futtatási azonosító értékének leszámítolásához.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

A futtatási azonosító segítségével figyelheti az új futtatás állapotát. Az új futás újabb 10-15 perces befejezést tesz le. 

Az új futtatás hasonló lesz az oktatóanyag korábbi futtatásához. Dönthet úgy, hogy nem tekinti meg a teljes kimenetet.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ne fejezze be ezt a szakaszt, ha más Azure Machine Learning-oktatóanyagok futtatását tervezi.

### <a name="stop-the-compute-instance"></a>A számítási példány leállítása

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Mindent töröljön

Ha nem kívánja használni a létrehozott erőforrásokat, törölje azokat, így nem kell fizetnie:

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok**lehetőséget.
1. Az erőforráscsoportok listájában jelölje ki a létrehozott erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Adja meg az erőforráscsoport nevét. Ezután válassza a **Törlés gombot.**

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet. Jelenítse meg a munkaterület tulajdonságait, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

Ebben a gépi tanulási folyamatok oktatóanyagában a következő feladatokat végezte el:

> [!div class="checklist"]
> * Környezeti függőségekkel rendelkező folyamatot épített ki egy távoli GPU-számítási erőforráson való futtatáshoz.
> * Létrehozott egy pontozási parancsfájlt a kötegelt előrejelzések futtatásához egy előre betanított Tensorflow-modell használatával.
> * Közzétett egy folyamatot, és lehetővé tette, hogy rest-végpontról fusson.

További példákat, hogyan hozhat létre folyamatokat a gépi tanulási SDK használatával, tekintse meg a [jegyzetfüzettárban.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
