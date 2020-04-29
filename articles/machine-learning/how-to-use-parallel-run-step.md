---
title: Batch-előrejelzések futtatása big data
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan lehet aszinkron módon kikövetkeztetni nagy mennyiségű adattal a Azure Machine Learning ParallelRunStep használatával. A ParallelRunStep párhuzamos feldolgozási képességeket biztosít a dobozból, és optimalizálja a nagy adatátviteli sebesség, a tűz és a felejtsd a Big-adatok használati eseteit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 01/15/2020
ms.custom: Ignite2019
ms.openlocfilehash: 3d283d1094336b928869aa281b4a640d7a62dd94
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79477187"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Batch-következtetés futtatása nagy mennyiségű adattal a Azure Machine Learning használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Megtudhatja, hogyan dolgozhat fel aszinkron módon és párhuzamosan nagy mennyiségű adatfeldolgozást Azure Machine Learning használatával. Az itt ismertetett ParallelRunStep-képesség nyilvános előzetes verzióban érhető el. Nagy teljesítményű és nagy átviteli sebességű módszer az adatok előállítására és feldolgozására. Aszinkron funkciókat biztosít a dobozból.

A ParallelRunStep segítségével egyszerűen méretezheti az offline következtetéseket a nagy mennyiségű, terabájtos üzemi adatokkal rendelkező gépekre, ami jobb termelékenységet és optimalizált költségeket eredményez.

Ez a cikk a következő feladatokat ismerteti:

> * Hozzon létre egy távoli számítási erőforrást.
> * Egyéni következtetési parancsfájlt írhat.
> * Hozzon létre egy [gépi tanulási folyamatot](concept-ml-pipelines.md) , amely a [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) adatkészlet alapján regisztrálja az előre betanított képbesorolási modellt. 
> * A modell használatával futtathat batch-következtetéseket az Azure Blob Storage-fiókjában elérhető mintaképeken. 

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* Az interaktív gyors útmutatóhoz végezze el a [telepítési oktatóanyagot](tutorial-1st-experiment-sdk-setup.md) , ha még nem rendelkezik Azure Machine learning munkaterülettel vagy notebook virtuális géppel. 

* A saját környezetének és függőségeinek kezeléséhez tekintse meg a saját környezet konfigurálásának [útmutatója](how-to-configure-environment.md) című témakört. A `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` szükséges függőségek letöltéséhez futtassa a környezetet a környezetben.

## <a name="set-up-machine-learning-resources"></a>Gépi tanulási erőforrások beállítása

A következő műveletek a Batch-következtetési folyamat futtatásához szükséges erőforrásokat határozzák meg:

- Hozzon létre egy adattárolót, amely olyan blob-tárolóra mutat, amely tartalmaz képeket a következtetésekhez.
- Állítsa be az adathivatkozásokat bemenetként és kimenetként a Batch következtetési folyamat lépéséhez.
- Állítson be egy számítási fürtöt a Batch következtetési lépés futtatásához.

### <a name="create-a-datastore-with-sample-images"></a>Adattár létrehozása minta-lemezképekkel

Szerezze be a MNIST kiértékelését a nyilvános blob `sampledata` -tárolóból egy `pipelinedata`nevű fiókban. Hozzon létre egy adattárt `mnist_datastore`a tárolóhoz tartozó névvel. A következő hívásával `register_azure_blob_container`, amely a jelzőt úgy `True` állítja be, hogy felülírja a `overwrite` korábban ezzel a névvel létrehozott adattárat. 

Ezt a lépést módosíthatja úgy, hogy a blob-tárolóra mutasson `datastore_name`, ha a, `container_name`a és `account_name`a értékeit is megadja.

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Ezután adja meg a munkaterület alapértelmezett adattárát kimeneti adattárként. Ezt fogja használni a következtetési kimenethez.

A munkaterület létrehozásakor a [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) és a [blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) alapértelmezés szerint a munkaterülethez van csatolva. Azure Files a munkaterület alapértelmezett adattára, de a blob Storage-t is használhatja adattárként. További információ: [Azure Storage-beállítások](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Adatbemenetek és kimenetek konfigurálása

Most be kell állítania az adatbemeneteket és kimeneteket, beleértve a következőket:

- A bemeneti képeket tartalmazó könyvtár.
- Az a könyvtár, ahol az előre betanított modellt tárolják.
- A címkéket tartalmazó könyvtár.
- A kimenet könyvtára.

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)a egy olyan osztály, amely a Azure Machine Learningban található adatelemzést, átalakítást és felügyeletet végzi. Ennek az osztálynak két típusa [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) van [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py): és. Ebben a példában a rendszer bemenetként `FileDataset` fogja használni a Batch következtetési folyamatának lépését. 

> [!NOTE] 
> `FileDataset`a Batch-következtetések támogatása jelenleg az Azure Blob Storage-ra korlátozódik. 

Az egyéni következtetési parancsfájlban más adatkészletekre is hivatkozhat. Használhatja például a parancsfájlban lévő feliratok elérését a képek címkézéséhez a és `Dataset.register` `Dataset.get_by_name`a használatával.

További információ a Azure Machine Learning adatkészletekről: [adatkészletek létrehozása és elérése (előzetes verzió)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)az objektumok a köztes adatátviteli folyamat lépései között használatosak. Ebben a példában a következtetést a kimenetek megjelenítéséhez használja.

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>Számítási cél beállítása

Azure Machine Learning a *számítási* (vagy *számítási cél*) a gépi tanulási folyamat számítási lépéseit végrehajtó gépekre vagy fürtökre vonatkozik. Futtassa a következő kódot egy CPU-alapú [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) -cél létrehozásához.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

## <a name="prepare-the-model"></a>A modell előkészítése

[Töltse le az előre betanított rendszerkép besorolási modelljét](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz), majd bontsa ki a `models` könyvtárat.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Ezután regisztrálja a modellt a munkaterületén, hogy elérhető legyen a távoli számítási erőforrás számára.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Következtetési parancsfájl írása

>[!Warning]
>A következő kód csak egy minta, amelyet a [mintaként használt jegyzetfüzet](https://aka.ms/batch-inference-notebooks) használ. Saját parancsfájlt kell létrehoznia a forgatókönyvhöz.

A parancsfájlnak két függvényt *kell tartalmaznia* :
- `init()`: Használja ezt a funkciót bármilyen költséges vagy közös felkészüléshez a későbbi következtetésekhez. Használhatja például a modell betöltését egy globális objektumba. Ezt a függvényt a rendszer csak egyszer hívja meg a folyamat elején.
-  `run(mini_batch)`: A függvény minden `mini_batch` példánynál futni fog.
    -  `mini_batch`: A párhuzamos futtatási lépés meghívja a Run metódust, és egy listát vagy pandák DataFrame ad át argumentumként a metódusnak. Min_batch minden bejegyzése – egy fájl elérési útja, ha a bemenet egy FileDataset, egy Panda DataFrame, ha a bemenet TabularDataset.
    -  `response`: a Run () metódusnak egy Panda DataFrame vagy egy tömböt kell visszaadnia. Append_row output_action esetében ezek a visszaadott elemek a közös kimeneti fájlba vannak hozzáfűzve. Summary_only esetén a rendszer figyelmen kívül hagyja az elemek tartalmát. Az összes kimeneti művelet esetében minden visszaadott kimeneti elem a bemeneti elem egy sikeres futtatását jelzi a bemenet mini-batchben. Győződjön meg arról, hogy elegendő adat szerepel a futtatási eredményben, hogy leképezi a bemenetet az eredmény futtatásához. A futtatási kimenet a kimeneti fájlban lesz megírva, és nem garantált, hogy sorrendben legyenek, a kimenetben lévő egyes kulcsokat kell használnia a bemenethez való leképezéshez.

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

### <a name="how-to-access-other-files-in-source-directory-in-entry_script"></a>Más fájlok elérése a forrás könyvtára entry_script

Ha a bejegyzési parancsfájllal megegyező könyvtárban van egy másik fájl vagy mappa, az aktuális munkakönyvtár megkeresésével hivatkozhat rá.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>ParallelRunStep tartalmazó folyamat létrehozása és futtatása

Most már mindent megtalál, amire szüksége lehet a folyamat létrehozásához.

### <a name="prepare-the-run-environment"></a>A futtatási környezet előkészítése

Először adja meg a parancsfájl függőségeit. Ezt az objektumot később kell használni, amikor létrehozza a folyamat lépését.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Adja meg a Batch-következtetési folyamat lépésének paramétereit

`ParallelRunConfig`a Azure Machine Learning folyamaton belül az újonnan bevezetett batch-következtetési `ParallelRunStep` példány fő konfigurációja. Ezzel a paranccsal becsomagolhatja a parancsfájlt, és konfigurálhatja a szükséges paramétereket, beleértve az alábbi paramétereket:
- `entry_script`: Egy felhasználói parancsfájl helyi fájl elérési útjaként, amely több csomóponton párhuzamosan fog futni. Ha `source_directory` van ilyen, használjon relatív elérési utat. Ellenkező esetben használja a gépen elérhető bármely elérési utat.
- `mini_batch_size`: A mini-batch egyetlen `run()` hívásnak átadott mérete. (nem `10` kötelező; az alapértelmezett érték a FileDataset és `1MB` a TabularDataset fájl.)
    - A `FileDataset`esetében a minimális értékkel rendelkező fájlok száma `1`. Több fájlt is egyesítheti egyetlen mini-kötegbe.
    - A `TabularDataset`esetében a mérete az adatmennyiség. Az értékek például `1024`a `1024KB`következők `10MB`:, `1GB`, és. A javasolt érték: `1MB`. A mini-batch- `TabularDataset` ból soha nem lesz keresztben a fájl határa. Ha például. csv fájlokkal rendelkezik, amelyek különböző méretűek, a legkisebb fájl 100 KB, a legnagyobb pedig 10 MB. Ha be van `mini_batch_size = 1MB`állítva, akkor az 1 MB-nál kisebb méretű fájlok egyetlen mini batch-ként lesznek kezelve. Az 1 MB-nál nagyobb méretű fájlok több mini-kötegre lesznek felosztva.
- `error_threshold`: A rendszer figyelmen kívül hagyja a `TabularDataset` hibák számát a ( `FileDataset` z) és a (z) esetében a feldolgozás során. Ha a teljes bemenethez tartozó hibák száma meghaladja ezt az értéket, a rendszer megszakítja a feladatot. A hiba küszöbértéke a teljes bemenetre vonatkozik, nem pedig a `run()` metódusnak eljuttatott egyes mini-kötegekhez. A tartomány `[-1, int.max]`. A `-1` rész azt jelzi, hogy a rendszer figyelmen kívül hagyja az összes hibát a feldolgozás során.
- `output_action`: Az alábbi értékek egyike azt jelzi, hogyan lesz rendszerezve a kimenet:
    - `summary_only`: A felhasználói parancsfájl a kimenetet fogja tárolni. `ParallelRunStep`a csak a hiba küszöbértékének kiszámításához használja a kimenetet.
    - `append_row`: Minden bemeneti fájl esetében csak egy fájl lesz létrehozva a kimeneti mappában, hogy az összes kimenetet sor szerint elválasztva fűzze hozzá. A fájl neve lesz `parallel_run_step.txt`.
- `source_directory`: A számítási célra végrehajtandó összes fájlt tartalmazó mappák elérési útja (nem kötelező).
- `compute_target`: Csak `AmlCompute` a támogatott.
- `node_count`: A felhasználói parancsfájl futtatásához használandó számítási csomópontok száma.
- `process_count_per_node`: A folyamatok száma egy csomóponton.
- `environment`: A Python-környezet definíciója. Konfigurálhatja egy meglévő Python-környezet használatára, vagy létrehozhat egy ideiglenes környezetet a kísérlethez. A definíció a szükséges alkalmazás-függőségek (opcionális) beállítására is felelős.
- `logging_level`: Naplózási részletesség. A részletességgel bővülő értékek a `WARNING`következők `INFO`:, `DEBUG`és. (nem `INFO`kötelező; az alapértelmezett érték:)
- `run_invocation_timeout`: A `run()` metódus meghívásának időtúllépése másodpercben. (nem kötelező; az `60`alapértelmezett érték:)

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>A folyamat lépésének létrehozása

Hozza létre a folyamat lépéseit a parancsfájl, a környezeti konfiguráció és a paraméterek használatával. Adja meg a munkaterülethez már csatolt számítási célt a parancsfájl végrehajtásának céljaként. A `ParallelRunStep` paranccsal hozhatja létre a Batch-következtetési folyamat lépését, amely a következő paramétereket veszi figyelembe:
- `name`: A lépés neve a következő elnevezési korlátozásokkal: Unique, 3-32 karakter, és regex ^\[a-z\]([-a-Z0-9] * [a-Z0-9])? $.
- `models`: Nulla vagy több modell neve már regisztrálva van a Azure Machine Learning modell beállításjegyzékében.
- `parallel_run_config`: A `ParallelRunConfig` korábban definiált objektum.
- `inputs`: Egy vagy több begépelt Azure Machine Learning adathalmaz.
- `output`: A `PipelineData` kimeneti könyvtárnak megfelelő objektum.
- `arguments`: A felhasználói parancsfájlnak átadott argumentumok listája (nem kötelező).
- `allow_reuse`: Azt határozza meg, hogy a lépés felhasználja-e az előző eredményeket, ha ugyanazokkal a beállításokkal/bemenetekkel futnak. Ha ez a paraméter `False`, akkor a folyamat végrehajtása során a rendszer mindig új futtatást hoz létre ehhez a lépéshez. (nem `True`kötelező; az alapértelmezett érték:.)

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

>[!Note]
> A fenti lépéstől függ `azureml-contrib-pipeline-steps`, az [Előfeltételek](#prerequisites)szakaszban leírtak szerint. 

### <a name="submit-the-pipeline"></a>A folyamat elküldése

Most futtassa a folyamatot. Először hozzon létre [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) egy objektumot a munkaterület-hivatkozás és a létrehozott folyamat lépés használatával. A `steps` paraméter a lépések tömbje. Ebben az esetben a Batch pontozásnak csak egy lépése van. Több lépésből álló folyamatok létrehozásához helyezze a lépéseket sorrendben ebben a tömbben.

Ezután a `Experiment.submit()` függvény használatával küldje el a folyamatot végrehajtásra.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-parallel-run-job"></a>A párhuzamos futtatási feladatok figyelése

A Batch-következtetési feladatok végrehajtása hosszú időt is igénybe vehet. Ez a példa egy Jupyter Widget használatával figyeli a folyamat előrehaladását. A feladatok előrehaladását a használatával is kezelheti:

* Azure Machine Learning Studio. 
* Konzol kimenete az [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) objektumból.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>További lépések

Ha szeretné látni, hogy ez a folyamat teljes körűen működjön, próbálja ki a [Batch következtetéseit tartalmazó jegyzetfüzetet](https://aka.ms/batch-inference-notebooks). 

A ParallelRunStep kapcsolatos hibakeresési és hibaelhárítási útmutatásért lásd: [útmutató.](how-to-debug-parallel-run-step.md)

[A folyamatok](how-to-debug-pipelines.md)hibakereséséhez és hibaelhárításához tekintse meg a útmutató útmutatását.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

