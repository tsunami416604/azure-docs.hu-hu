---
title: Batch-előrejelzések futtatása big data
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan lehet aszinkron módon kikövetkeztetni nagy mennyiségű adattal a Azure Machine Learning ParallelRunStep használatával. A ParallelRunStep párhuzamos feldolgozási képességeket biztosít a dobozból, és optimalizálja a nagy adatátviteli sebesség, a tűz és a felejtsd a Big-adatok használati eseteit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 08/14/2020
ms.custom: Build2020, devx-track-python
ms.openlocfilehash: 8e8d0a13bc01e95311345154648ecb00b624c4bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905617"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Batch-következtetés futtatása nagy mennyiségű adattal a Azure Machine Learning használatával


Ebből a cikkből megtudhatja, hogyan futtathatja Azure Machine Learning-modelljét párhuzamosan, gyorsan értékelve nagy mennyiségű adatot. 

A nagyméretű adathalmazokra vagy bonyolult modellekkel való következtetés időigényes lehet. A `ParallelRunStep` osztály lehetővé teszi, hogy párhuzamosan hajtsa végre a feldolgozást, ami a teljes eredmények gyorsabb elérését eredményezheti. Még akkor is, ha az egyszeri értékelés futtatása meglehetősen gyors, sok esetben (objektumok észlelése, videó-feldolgozás, természetes nyelvi feldolgozás stb.) számos értékelés futtatására van szükség. 

A segítségével `ParallelRunStep` egyszerűen méretezheti a Batch-következtetéseket a nagyméretű gépekre. Ezek a fürtök terabájtos strukturált vagy strukturálatlan adatok kezelésére képesek a jobb termelékenység és az optimalizált díj mellett.

Ez a cikk a következő feladatokat ismerteti:

> 1. Gépi tanulási erőforrások beállítása.
> 1. Állítsa be a Batch-adatok bemeneteit és kimenetét.
> 1. Készítse elő az előre betanított képbesorolási modellt a [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) adatkészlet alapján. 
> 1.  Írja be a következtetési parancsfájlt.
> 1. Hozzon létre egy [gépi tanulási folyamatot](concept-ml-pipelines.md) , amely ParallelRunStep tartalmaz, és futtassa a Batch következtetést a MNIST-tesztelési lemezképeken. 
> 1. A Batch-következtetések újraküldése új adatbevitelsel és paraméterekkel. 
> 1. Tekintse meg az eredményeket.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes fiókot a virtuális gép létrehozásának megkezdése előtt. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* Az interaktív gyors útmutatóhoz végezze el a [telepítési oktatóanyagot](tutorial-1st-experiment-sdk-setup.md) , ha még nem rendelkezik Azure Machine learning munkaterülettel. 

* A saját környezetének és függőségeinek kezeléséhez tekintse meg a saját helyi környezet konfigurálásának [útmutatója](how-to-configure-environment.md) című témakört.

## <a name="set-up-machine-learning-resources"></a>Gépi tanulási erőforrások beállítása

Az alábbi műveletek a Batch-következtetési folyamat futtatásához szükséges gépi tanulási erőforrásokat határozzák meg:

- Kapcsolódjon egy munkaterülethez.
- Meglévő számítási erőforrás létrehozása vagy csatolása.

### <a name="configure-workspace"></a>Munkaterület konfigurálása

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. `Workspace.from_config()` beolvassa a config.jsfájlt, és betölti a részleteket egy ws nevű objektumba.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> Ez a kódrészlet arra vár, hogy a munkaterület-konfiguráció az aktuális könyvtárba vagy a szülőbe legyen mentve. A munkaterület létrehozásával kapcsolatos további információkért lásd: [Azure Machine learning munkaterületek létrehozása és kezelése](how-to-manage-workspace.md). A konfiguráció fájlra való mentésével kapcsolatos további információkért lásd: [munkaterület konfigurációs fájljának létrehozása](how-to-configure-environment.md#workspace).

### <a name="create-a-compute-target"></a>Számítási cél létrehozása

Azure Machine Learning a *számítási* (vagy *számítási cél*) a gépi tanulási folyamat számítási lépéseit végrehajtó gépekre vagy fürtökre vonatkozik. Futtassa a következő kódot egy CPU-alapú [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true) -cél létrehozásához.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

## <a name="configure-inputs-and-output"></a>Bemenetek és kimenet konfigurálása

### <a name="create-a-datastore-with-sample-images"></a>Adattár létrehozása minta-lemezképekkel

Szerezze be a MNIST kiértékelését a nyilvános blob-tárolóból `sampledata` egy nevű fiókban `pipelinedata` . Hozzon létre egy adattárt a `mnist_datastore` tárolóhoz tartozó névvel. A következő hívásával `register_azure_blob_container` , amely a `overwrite` jelzőt úgy állítja be, hogy `True` felülírja a korábban ezzel a névvel létrehozott adattárat. 

Ezt a lépést módosíthatja úgy, hogy a blob-tárolóra mutasson, ha a, a és a értékeit is megadja `datastore_name` `container_name` `account_name` .

```python
from azureml.core import Datastore
from azureml.core import Workspace

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Ezután adja meg a munkaterület alapértelmezett adattárát kimeneti adattárként. Ezt fogja használni a következtetési kimenethez.

A munkaterület létrehozásakor a [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)   és a [blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)   alapértelmezés szerint a munkaterülethez van csatolva. Azure Files a munkaterület alapértelmezett adattára, de a blob Storage-t is használhatja adattárként. További információ: [Azure Storage-beállítások](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>Adatbemenetek létrehozása

A Batch-következtetés bemenetei azok az adatok, amelyeket párhuzamos feldolgozásra kíván particionálni. Egy batch-következtetési folyamat fogadja az adatbemeneteket [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true) .

`Dataset` Az Azure Machine Learning-ban lévő adatelemzési,-átalakítási és-kezelési szolgáltatás. Két típus létezik: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) és [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true) . Ebben a példában bemenetként fog használni `FileDataset` . `FileDataset` lehetőséget biztosít a fájlok letöltésére vagy csatlakoztatására a számítási feladatokhoz. Adatkészlet létrehozásával az adatforrás helyére mutató hivatkozást hozhat létre. Ha az adatkészlethez bármilyen albeállítási átalakítást alkalmazott, akkor a rendszer az adatkészletben tárolja azokat is. Az adattárolók a meglévő helyükön maradnak, így nem merülnek fel extra tárolási költségek.

További információ a Azure Machine Learning adatkészletekről: [adatkészletek létrehozása és elérése (előzetes verzió)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

```python
from azureml.core.dataset import Dataset

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

Ha dinamikus adatbevitelt szeretne használni a Batch következtetési folyamatának futtatásakor, megadhatja a bemeneteket a következőként: `Dataset` [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) . Megadhatja a bemeneti adatkészletet minden alkalommal, amikor újra elküld egy batch-következtetési folyamat futtatását.

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>A kimenet létrehozása

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) az objektumok a köztes adatátviteli folyamat lépései között használatosak. Ebben a példában a következtetést a kimenetre használja.

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", datastore=def_data_store)
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

Ezután regisztrálja a modellt a munkaterületén, hogy elérhető legyen a számítási erőforrás számára.

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
>A következő kód csak egy minta, amelyet a [mintaként használt jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run) használ. Saját parancsfájlt kell létrehoznia a forgatókönyvhöz.

A parancsfájlnak két függvényt *kell tartalmaznia* :
- `init()`: Használja ezt a funkciót bármilyen költséges vagy közös felkészüléshez a későbbi következtetésekhez. Használhatja például a modell betöltését egy globális objektumba. Ezt a függvényt a rendszer csak egyszer hívja meg a folyamat elején.
-  `run(mini_batch)`: A függvény minden `mini_batch` példánynál futni fog.
    -  `mini_batch`: `ParallelRunStep` meghívja a Run metódust, és egy listát vagy `DataFrame` a pandák argumentumot ad át a metódusnak. A mini_batch minden bejegyzése egy fájl elérési útja lesz, ha a bemenet `FileDataset` vagy egy Panda, `DataFrame` Ha a bemenet a `TabularDataset` .
    -  `response`: a Run () metódusnak egy pandák `DataFrame` vagy egy tömböt kell visszaadnia. Append_row output_action esetében ezek a visszaadott elemek a közös kimeneti fájlba vannak hozzáfűzve. Summary_only esetén a rendszer figyelmen kívül hagyja az elemek tartalmát. Az összes kimeneti művelet esetében minden visszaadott kimeneti elem a bemeneti elem egy sikeres futtatását jelzi a bemenet mini-batchben. Győződjön meg arról, hogy a Futtatás eredményében elegendő adat szerepel a kimenet leképezése a kimeneti eredmény futtatásához. A futtatási kimenet a kimeneti fájlban lesz megírva, és nem garantált, hogy sorrendben legyenek, a kimenetben lévő egyes kulcsokat kell használnia a bemenethez való leképezéshez.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
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

Ha egy másik fájl vagy mappa található a következtetési parancsfájllal megegyező könyvtárban, hivatkozhat rá az aktuális munkakönyvtár megkeresésével.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>ParallelRunStep tartalmazó folyamat létrehozása és futtatása

Most már mindent megtalál, amire szüksége lehet: az adatbemenetek, a modell, a kimenet és a következtetési szkript. Hozzunk létre egy ParallelRunStep tartalmazó batch-következtetési folyamatot.

### <a name="prepare-the-environment"></a>A környezet előkészítése

Először adja meg a parancsfájl függőségeit. Ez lehetővé teszi a pip-csomagok telepítését, valamint a környezet konfigurálását.

Mindig tartalmazza a **azureml-Core** és a **azureml-DataSet-Runtime [pandák, Fuse]** részt a pip csomag listájában. Ha egyéni Docker-rendszerképet használ (user_managed_dependencies = true), telepítenie kell a Conda is.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.15.2", "pillow", 
                                                          "azureml-core", "azureml-dataset-runtime[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>Paraméterek megadása a ParallelRunConfig használatával

`ParallelRunConfig` a `ParallelRunStep` Azure Machine learning folyamaton belüli példány fő konfigurációja. Ezzel a paranccsal becsomagolhatja a parancsfájlt, és konfigurálhatja a szükséges paramétereket, beleértve az alábbi bejegyzéseket:
- `entry_script`: Egy felhasználói parancsfájl helyi fájl elérési útjaként, amely több csomóponton párhuzamosan fog futni. Ha `source_directory` van ilyen, használjon relatív elérési utat. Ellenkező esetben használja a gépen elérhető bármely elérési utat.
- `mini_batch_size`: A mini-batch egyetlen hívásnak átadott mérete `run()` . (nem kötelező; az alapértelmezett érték a és a rendszerhez tartozó `10` fájlok `FileDataset` `1MB` `TabularDataset` .)
    - A esetében `FileDataset` a minimális értékkel rendelkező fájlok száma `1` . Több fájlt is egyesítheti egyetlen mini-kötegbe.
    - A esetében `TabularDataset` a mérete az adatmennyiség. Az értékek például a következők:,, `1024` `1024KB` `10MB` és `1GB` . A javasolt érték: `1MB` . A mini-batch-ból `TabularDataset` soha nem lesz keresztben a fájl határa. Ha például. csv fájlokkal rendelkezik, amelyek különböző méretűek, a legkisebb fájl 100 KB, a legnagyobb pedig 10 MB. Ha be van állítva `mini_batch_size = 1MB` , akkor az 1 MB-nál kisebb méretű fájlok egyetlen mini batch-ként lesznek kezelve. Az 1 MB-nál nagyobb méretű fájlok több mini-kötegre lesznek felosztva.
- `error_threshold`: A rendszer figyelmen kívül hagyja a hibák számát a (z `TabularDataset` ) és a (z) esetében a `FileDataset` feldolgozás során. Ha a teljes bemenethez tartozó hibák száma meghaladja ezt az értéket, a rendszer megszakítja a feladatot. A hiba küszöbértéke a teljes bemenetre vonatkozik, nem a metódusnak eljuttatott egyes mini-batch esetében `run()` . A tartomány `[-1, int.max]` . A `-1` rész azt jelzi, hogy a rendszer figyelmen kívül hagyja az összes hibát a feldolgozás során.
- `output_action`: Az alábbi értékek egyike azt jelzi, hogyan lesz rendszerezve a kimenet:
    - `summary_only`: A felhasználói parancsfájl a kimenetet fogja tárolni. `ParallelRunStep` a csak a hiba küszöbértékének kiszámításához használja a kimenetet.
    - `append_row`: Minden bemenet esetében csak egy fájl lesz létrehozva a kimeneti mappában, hogy az összes kimenetet sor szerint elválasztva fűzze hozzá.
- `append_row_file_name`: Append_row output_action kimeneti fájljának testreszabása (nem kötelező; az alapértelmezett érték `parallel_run_step.txt` ).
- `source_directory`: A számítási célra végrehajtandó összes fájlt tartalmazó mappák elérési útja (nem kötelező).
- `compute_target`: Csak `AmlCompute` a támogatott.
- `node_count`: A felhasználói parancsfájl futtatásához használandó számítási csomópontok száma.
- `process_count_per_node`: A folyamatok száma egy csomóponton. Az ajánlott eljárás az, ha a GPU vagy a CPU egyik csomópontjának száma (opcionális; alapértelmezett érték) van beállítva `1` .
- `environment`: A Python-környezet definíciója. Beállíthatja egy meglévő Python-környezet használatára vagy egy ideiglenes környezet beállítására. A definíció a szükséges alkalmazás-függőségek (opcionális) beállítására is felelős.
- `logging_level`: Naplózási részletesség. A részletességgel bővülő értékek a következők: `WARNING` , `INFO` és `DEBUG` . (nem kötelező; az alapértelmezett érték: `INFO` )
- `run_invocation_timeout`: A `run()` metódus meghívásának időtúllépése másodpercben. (nem kötelező; az alapértelmezett érték: `60` )
- `run_max_try`: Maximális számú próbálkozás a `run()` mini batch számára. A nem `run()` sikerült, ha kivétel keletkezik, vagy ha a rendszer nem ad vissza semmit, ha `run_invocation_timeout` a szolgáltatás elérte az értéket (opcionális; az alapértelmezett érték `3` ). 

A (z),,,, és as értéket megadhatja, `mini_batch_size` `node_count` hogy a `process_count_per_node` `logging_level` `run_invocation_timeout` `run_max_try` `PipelineParameter` folyamat futásának újraküldésekor a paraméterek értékei finomhangolása megtörténjen. Ebben a példában a `PipelineParameter` és a és a () `mini_batch_size` értéket fogja használni, `Process_count_per_node` Ha később újra elküld egy futtatást. 

Ez a példa feltételezi, hogy a `digit_identification.py` korábban tárgyalt parancsfájlt használja. Ha saját parancsfájlt használ, `source_directory` ennek megfelelően módosítsa a és a `entry_script` paramétereket.

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory='.',
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>A ParallelRunStep létrehozása

Hozza létre a ParallelRunStep a parancsfájl, a környezeti konfiguráció és a paraméterek használatával. Adja meg azt a számítási célt, amely már a munkaterülethez van csatolva a következtetési parancsfájl végrehajtásának céljaként. `ParallelRunStep`A paranccsal hozhatja létre a Batch-következtetési folyamat lépését, amely a következő paramétereket veszi figyelembe:
- `name`: A lépés neve a következő elnevezési korlátozásokkal: Unique, 3-32 karakter, és regex ^ \[ a-z \] ([-a-Z0-9] * [a-Z0-9])? $.
- `parallel_run_config`: A `ParallelRunConfig` korábban definiált objektum.
- `inputs`: Egy vagy több, egybeírt Azure Machine Learning-adathalmaz párhuzamos feldolgozásra particionálva.
- `side_inputs`: Egy vagy több olyan hivatkozási adat vagy adatkészlet, amelyet nem kell particionálni.
- `output`: `PipelineData` A kimeneti könyvtárnak megfelelő objektum.
- `arguments`: A felhasználói parancsfájlnak átadott argumentumok listája. A unknown_args használatával kérheti le őket a belépési parancsfájlban (nem kötelező).
- `allow_reuse`: Azt határozza meg, hogy a lépés felhasználja-e az előző eredményeket, ha ugyanazokkal a beállításokkal/bemenetekkel futnak. Ha ez a paraméter `False` , akkor a folyamat végrehajtása során a rendszer mindig új futtatást hoz létre ehhez a lépéshez. (nem kötelező; az alapértelmezett érték: `True` .)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>A folyamat létrehozása és futtatása

Most futtassa a folyamatot. Először hozzon létre egy [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py&preserve-view=true) objektumot a munkaterület-hivatkozás és a létrehozott folyamat lépés használatával. A `steps` paraméter a lépések tömbje. Ebben az esetben a Batch-következtetések csak egyetlen lépésből állnak. Több lépésből álló folyamatok létrehozásához helyezze a lépéseket sorrendben ebben a tömbben.

Ezután a függvény használatával `Experiment.submit()` küldje el a folyamatot végrehajtásra.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>A Batch-következtetési feladatok figyelése

A Batch-következtetési feladatok végrehajtása hosszú időt is igénybe vehet. Ez a példa egy Jupyter Widget használatával figyeli a folyamat előrehaladását. A feladatok előrehaladását a használatával is figyelheti:

* Azure Machine Learning Studio. 
* Konzol kimenete az [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py&preserve-view=true) objektumból.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>Futtatás újraküldése új adatbevitelsel és paraméterekkel

Mivel a bemeneteket és több konfigurálást hajtott végre `PipelineParameter` , újra elküldheti a Batch-következtetést egy másik adatkészlet-bemenettel, és a paraméterek finomhangolását anélkül, hogy teljesen új folyamatot kellene létrehoznia. Ugyanazt az adattárolót fogja használni, de csak egyetlen rendszerképet használ adatbemenetként.

```python
path_on_datastore = mnist_blob.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```
## <a name="view-the-results"></a>Az eredmények megtekintése

A fenti Futtatás eredményei az `DataStore` `PipelineData` objektumban megadott kimeneti adatként vannak írva, amelyek ebben az esetben a *következtetések*. A rendszer az alapértelmezett blob-tárolóban tárolja az eredményeket, és megtekintheti a Storage-fiókját, és megtekintheti Storage Explorer, a fájl elérési útja a következő: azureml-blobtárhely-*GUID*/azureml/*RunId* / *output_dir*.

A találatok megtekintéséhez letöltheti ezeket az adatfájlokat is. Alább látható a mintakód az első 10 sor megtekintéséhez.

```python
import pandas as pd
import tempfile

batch_run = pipeline_run.find_step_run(parallelrun_step.name)[0]
batch_output = batch_run.get_output_data(output_dir.name)

target_dir = tempfile.mkdtemp()
batch_output.download(local_path=target_dir)
result_file = os.path.join(target_dir, batch_output.path_on_datastore, parallel_run_config.append_row_file_name)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10) 
```

## <a name="next-steps"></a>További lépések

Ha szeretné látni, hogy ez a folyamat teljes körűen működjön, próbálja ki a [Batch következtetéseit tartalmazó jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run). 

A ParallelRunStep kapcsolatos hibakeresési és hibaelhárítási útmutatásért lásd: [útmutató.](how-to-debug-parallel-run-step.md)

[A folyamatok](how-to-debug-pipelines.md)hibakereséséhez és hibaelhárításához tekintse meg a útmutató útmutatását.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

