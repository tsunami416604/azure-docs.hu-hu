---
title: Kötegelt előrejelzések futtatása big data-adatokon
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan juthat el aszinkron módon nagy mennyiségű adatról az Azure Machine Learning ParallelRunStep használatával. A ParallelRunStep a teljes dobozból kiindulva biztosít párhuzamos feldolgozási lehetőségeket, és optimalizálja a nagy átviteli sebességű, tűzvédelmi és felejtési következtetéseket a big data használati esetekhez.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477187"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Kötegelt következtetés futtatása nagy mennyiségű adaton az Azure Machine Learning használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan dolgozhat fel nagy mennyiségű adatot aszinkron módon és párhuzamosan az Azure Machine Learning használatával. Az itt ismertetett ParallelRunStep képesség nyilvános előzetes verzióban érhető el. Ez egy nagy teljesítményű és nagy átviteli rendszerű módja a következtetések létrehozásának és az adatok feldolgozásának. Ez biztosítja az aszinkron képességek ki a dobozból.

A ParallelRunStep segítségével egyszerű az offline következtetések nagy fürtjeire skálázható a több terabájt nyi termelési adatokon, ami nagyobb termelékenységet és optimalizált költséget eredményez.

Ebben a cikkben a következő feladatokat ismerheti meg:

> * Hozzon létre egy távoli számítási erőforrást.
> * Írjon egy egyéni következtetési parancsfájlt.
> * Hozzon létre egy [gépi tanulási folyamatot](concept-ml-pipelines.md) az [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) adatkészleten alapuló előre betanított rendszerkép-besorolási modell regisztrálásához. 
> * A modell használatával kötegelt következtetéseket futtathatja az Azure Blob storage-fiókjában elérhető mintaképeken. 

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

* Az irányított rövid útmutató, töltse ki a [telepítő oktatóanyag,](tutorial-1st-experiment-sdk-setup.md) ha még nem rendelkezik egy Azure Machine Learning-munkaterület vagy notebook virtuális gép. 

* A saját környezet és függőségek kezeléséhez tekintse meg a [útmutatót](how-to-configure-environment.md) a saját környezet konfigurálásához. Futtassa `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` a környezetben a szükséges függőségek letöltéséhez.

## <a name="set-up-machine-learning-resources"></a>Gépi tanulási erőforrások beállítása

A következő műveletek beállítják a kötegkövetkeztetési folyamat futtatásához szükséges erőforrásokat:

- Hozzon létre egy adattár, amely rámutat egy blob tároló, amely a következtetéseket tartalmazó lemezképeket.
- Állítsa be az adathivatkozásokat bemenetként és kimenetként a kötegkövetkeztetési folyamat lépéséhez.
- Állítson be egy számítási fürtöt a kötegkövetkeztetési lépés futtatásához.

### <a name="create-a-datastore-with-sample-images"></a>Adattár létrehozása mintaképekkel

Az MNIST kiértékelési készlet beolvasása a nyilvános blobtárolóból `sampledata` egy nevű `pipelinedata`fiókon. Hozzon létre egy `mnist_datastore`adattár a nevét, amely rámutat arra, hogy a tároló. A következő hívásban `register_azure_blob_container`a `overwrite` jelző `True` beállításával felülírja a korábban ezzel a névvel létrehozott adattaránkat. 

Ezt a lépést módosíthatja úgy, hogy a blobtárolóra mutasson, ha saját értékeket ad meg a `datastore_name` `container_name`számára. `account_name`

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

Ezután adja meg a munkaterület alapértelmezett adattárát kimeneti adattárként. Akkor használja a következtetés kimenet.

A munkaterület létrehozásakor az [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) és a [Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) alapértelmezés szerint a munkaterülethez csatlakozik. Az Azure Files a munkaterület alapértelmezett adattára, de a Blob storage-t adattárként is használhatja. További információ: [Azure storage options](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Adatbevitelek és -kimenetek konfigurálása

Most konfigurálnia kell az adatbeviteleket és -kimeneteket, többek között a következőket:

- A bemeneti lemezképeket tartalmazó könyvtár.
- Az előre betanított modell tankönyvtára.
- A címkéket tartalmazó könyvtár.
- A kimenet könyvtára.

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)Az Azure Machine Learning ben az adatok feltárására, átalakítására és kezelésére szolgáló osztály. Ennek az osztálynak [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py)két típusa van: és . Ebben a példában a `FileDataset` kötegkövetkeztetési lépés bemeneteként fogja használni. 

> [!NOTE] 
> `FileDataset`a kötegelt következtetésben nyújtott támogatás egyelőre az Azure Blob storage-ra korlátozódik. 

Az egyéni következtetési parancsfájlban más adatkészletekre is hivatkozhat. Használhatja például a parancsfájl címkéinek elérésére a `Dataset.register` képek `Dataset.get_by_name`címkézésére a és a használatával.

Az Azure Machine Learning-adatkészletekről az [Adatkészletek létrehozása és elérése (előzetes verzió) című témakörben](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)talál további információt.

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)az objektumok köztes adatok átvitelére szolgálnak a folyamatlépései között. Ebben a példában azt a kimenetek következtetése.

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

Az Azure Machine Learningben *a számítási* (vagy *számítási cél)* azokra a gépekre vagy fürtökre utal, amelyek a gépi tanulási folyamat számítási lépéseit hajtják végre. Futtassa a következő kódot egy CPU-alapú [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) cél létrehozásához.

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

[Töltse le az előre betanított lemezkép-besorolási modellt,](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)majd bontsa ki a `models` címtárba.

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

Ezután regisztrálja a modellt a munkaterülettel, hogy a távoli számítási erőforrás elérhető legyen.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Írja meg a következtetési szkriptet

>[!Warning]
>A következő kód csak egy minta, amely a [minta notebook](https://aka.ms/batch-inference-notebooks) használ. Létre kell hoznia a saját forgatókönyvet a forgatókönyvhöz.

A *parancsfájlnak* két függvényt kell tartalmaznia:
- `init()`: Ezt a funkciót a későbbi következtetések költséges vagy gyakori előkészítéséhez használja. Például használja a modell betöltéséhez egy globális objektumba. Ez a függvény csak egyszer lesz meghívva a folyamat kezdetén.
-  `run(mini_batch)`: A függvény minden `mini_batch` példányhoz futni fog.
    -  `mini_batch`: A párhuzamos futtatási lépés elindítja a futtatási metódust, és a metódus argumentumaként egy listát vagy a Pandas DataFrame-et adja át. Minden bejegyzés min_batch lesz - a fájl elérési útja, ha a bemenet egy FileDataset, a Pandas DataFrame, ha a bemenet egy TabularDataset.
    -  `response`: a run() metódusnak Pandas DataFrame-et vagy tömböt kell visszaadnia. A append_row output_action esetében ezek a visszaadott elemek hozzáfűzve lesznek a közös kimeneti fájlba. A summary_only esetében az elemek tartalmát a program figyelmen kívül hagyja. Az összes kimeneti művelet esetében minden visszaadott kimeneti elem a bemeneti mini-köteg bemeneti elemének egy sikeres futtatását jelzi. Győződjön meg arról, hogy elegendő adat szerepel a futtatási eredmény a bemeneti eredmény leképezéséhez. Run kimenet lesz írva a kimeneti fájlt, és nem garantált, hogy annak érdekében, akkor használja néhány kulcsot a kimeneti leképezése a bemeneti.

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

### <a name="how-to-access-other-files-in-source-directory-in-entry_script"></a>A forráskönyvtárban lévő többi fájl elérése entry_script

Ha a bejegyzési parancsfájllal azonos könyvtárban van egy másik fájl vagy mappa, az aktuális munkakönyvtár megkeresésével hivatkozhat rá.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>A ParallelRunStep-et tartalmazó folyamat létrehozása és futtatása

Most már mindent meg kell építeni a csővezeték.

### <a name="prepare-the-run-environment"></a>A futtatási környezet előkészítése

Először adja meg a parancsfájl függőségeit. Ezt az objektumot később használja a folyamatlépés létrehozásakor.

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

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Adja meg a kötegkövetkeztetési folyamat lépésparamétereit

`ParallelRunConfig`az Azure Machine Learning-folyamaton belül `ParallelRunStep` az újonnan bevezetett kötegkövetkeztetési példány fő konfigurációja. A parancsfájl burkolására és a szükséges paraméterek konfigurálására használható, beleértve az alábbi paraméterek mindegyikét:
- `entry_script`: Felhasználói parancsfájl helyi fájlelérési útként, amely több csomóponton párhuzamosan fog futni. Ha `source_directory` van ilyen, használjon relatív elérési utat. Ellenkező esetben használjon olyan elérési utat, amely elérhető a számítógépen.
- `mini_batch_size`: A mini-batch mérete egyetlen `run()` hívásra átadott. (nem kötelező; az `10` alapértelmezett érték a `1MB` FileDataset és a TabularDataset fájljai.)
    - A `FileDataset`esetén a minimális értékű fájlok `1`száma. Több fájlt is egyesíthet egyetlen minikötegbe.
    - A `TabularDataset`, ez az adatok mérete. A példaértékek `1024KB` `10MB`a `1GB`, `1024`, és . Az ajánlott `1MB`érték a . A mini-batch-től `TabularDataset` soha nem lépi át a fájlhatárokat. Ha például .csv fájlja van különböző méretben, a legkisebb fájl 100 KB, a legnagyobb pedig 10 MB. Ha a `mini_batch_size = 1MB`beállítás , majd a fájlok mérete kisebb, mint 1 MB lesz kezelni, mint egy mini-batch. Az 1 MB-nál nagyobb méretű fájlok több minikötegre lesznek felosztva.
- `error_threshold`: A feldolgozás során `TabularDataset` figyelmen kívül `FileDataset` kell hagyni az ilyen rekordhibák és fájlhibák számát. Ha a teljes bemenet hibaszáma meghaladja ezt az értéket, a feladat megszakad. A hibaküszöb a teljes bemenetre, és nem a `run()` metódusnak küldött egyedi minikötegekre van. A tartomány `[-1, int.max]`. Az `-1` alkatrész azt jelzi, figyelmen kívül hagyva az összes hibát a feldolgozás során.
- `output_action`: Az alábbi értékek egyike jelzi a kimenet rendszerezését:
    - `summary_only`: A felhasználói parancsfájl tárolja a kimenetet. `ParallelRunStep`csak a hibaküszöb kiszámításához használja a kimenetet.
    - `append_row`: Az összes bemeneti fájl hoz létre csak egy fájlt a kimeneti mappában, hogy hozzáfűzi az összes kimenetet soron ként elválasztva. A fájlnév `parallel_run_step.txt`a .
- `source_directory`: A számítási célon végrehajtandó összes fájlt tartalmazó mappák elérési útjai (nem kötelező).
- `compute_target`: `AmlCompute` Csak támogatott.
- `node_count`: A felhasználói parancsfájl futtatásához használandó számítási csomópontok száma.
- `process_count_per_node`: A folyamatok száma csomópontonként.
- `environment`: A Python-környezet definíciója. Beállíthatja, hogy egy meglévő Python-környezetet használjon, vagy ideiglenes környezetet állítson be a kísérlethez. A definíció felelős a szükséges alkalmazásfüggőségek beállításáért is (nem kötelező).
- `logging_level`: Részletesség naplózása. A növekvő részletességben lévő `WARNING` `INFO`értékek `DEBUG`a következők: , , és . (nem kötelező, az `INFO`alapértelmezett érték )
- `run_invocation_timeout`: `run()` A metódus meghívási időmeghosszabbítása másodpercben. (nem kötelező; `60`az alapértelmezett érték )

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

### <a name="create-the-pipeline-step"></a>A folyamatlépés létrehozása

Hozza létre a folyamatlépés a parancsfájl, a környezeti konfiguráció és a paraméterek használatával. Adja meg a számítási célt, amely már csatolt a munkaterülethez, mint a parancsfájl végrehajtásának célja. A `ParallelRunStep` kötegelt következtetési folyamat lépésének létrehozásához használja, amely az alábbi paraméterek mindegyikét alkalmazza:
- `name`: A lépés neve a következő elnevezési korlátozásokkal: egyedi, 3-32\[karakter,\]és regex ^ a-z ([-a-z0-9]*[a-z0-9])?$.
- `models`: Nulla vagy több modellnév már regisztrálva van az Azure Machine Learning modell beállításjegyzékében.
- `parallel_run_config`: `ParallelRunConfig` Egy objektum, akorábban meghatározottak szerint.
- `inputs`: Egy vagy több egygépelt Azure Machine Learning-adatkészletek.
- `output`: `PipelineData` A kimeneti könyvtárnak megfelelő objektum.
- `arguments`: A felhasználói parancsfájlnak átadott argumentumok listája (nem kötelező).
- `allow_reuse`: A lépés nek újra fel kell-e használnia a korábbi eredményeket, ha azonos beállításokkal/bemenetekkel fut. Ha ez `False`a paraméter, akkor a folyamat végrehajtása során mindig új futtatás jön létre ehhez a lépéshez. (nem kötelező; az `True`alapértelmezett érték .)

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
> A fenti lépés `azureml-contrib-pipeline-steps`attól függ , ahogy azt az Előfeltételek című részben [leírtak szerint.](#prerequisites) 

### <a name="submit-the-pipeline"></a>A folyamat beküldése

Most futtassa a vezetéket. Először hozzon létre egy [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) objektumot a munkaterületi hivatkozás és a létrehozott folyamatlépés használatával. A `steps` paraméter lépések tömbje. Ebben az esetben csak egy lépés van a kötegelt pontozáshoz. Ha több lépésből álló folyamatokat szeretne építeni, helyezze a lépéseket sorrendbe ebben a tömbben.

Ezután használja `Experiment.submit()` a függvényt a folyamat végrehajtásra történő elküldéséhez.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-parallel-run-job"></a>A párhuzamos futtatási feladat figyelése

A kötegelt következtetési feladat befejezése hosszú időt vehet igénybe. Ez a példa jupyter widget használatával figyeli a folyamatot. A projekt előrehaladását a következők használatával is kezelheti:

* Azure Machine Learning Stúdió. 
* Konzol kimenete [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) az objektumból.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>További lépések

Ha látni szeretné, hogy ez a folyamat végponttól végpontig működik, próbálja meg a [kötegkövetkeztetési jegyzetfüzetet.](https://aka.ms/batch-inference-notebooks) 

A ParallelRunStep hibakeresésére és hibaelhárítási útmutatására [vonatkozó](how-to-debug-parallel-run-step.md)útmutatóban talál.

A folyamatok hibakeresésére és hibaelhárítására vonatkozó útmutatást az [útmutatóban talál.](how-to-debug-pipelines.md)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

