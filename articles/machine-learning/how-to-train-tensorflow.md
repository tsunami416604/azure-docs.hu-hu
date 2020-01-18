---
title: Neurális hálózat betanítása a TensorFlow
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan futtathat TensorFlow-betanítási szkripteket a méretezés Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 698850897622d0eec85eb295da95ebb945440cdd
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263897"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>TensorFlow mély tanulási modellt készíthet Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan futtathatja a [TensorFlow](https://www.tensorflow.org/overview) -betanítási szkripteket Azure Machine learning [TensorFlow kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) -osztályának használatával. Ez a példa egy TensorFlow-modellt vezet be, és regisztrálja a kézzel írt számjegyeket egy mély neurális hálózat (DNN) használatával.

Függetlenül attól, hogy a TensorFlow-modellt fejleszti az alapoktól, vagy egy [meglévő modellt](how-to-deploy-existing-model.md) hoz létre a felhőbe, a Azure Machine learning használatával kibővítheti a nyílt forráskódú képzési feladatokat az éles modellek létrehozásához, üzembe helyezéséhez, verzióhoz és figyeléséhez.

További információ a [Deep learning és a Machine learning](concept-deep-learning-vs-machine-learning.md)szolgáltatásról.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:

 - Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

     - Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
    - A notebook-kiszolgáló minták Deep learning mappájában keresse meg a befejezett és kibontott jegyzetfüzetet, ehhez a következő könyvtárra navigálva: **útmutató – használat – azureml > ml-keretrendszerek > tensorflow > üzembe helyezés > Train-hiperparaméter-Tune-Deploy-with-tensorflow** mappában. 
 
 - Saját Jupyter Notebook-kiszolgáló

    - [Telepítse a Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)-t.
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    - [Töltse le `mnist-tf.py` és `utils.py` a minta parancsfájl-fájlokat](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow)
     
    Az útmutató egy befejezett [Jupyter notebook verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) is megtalálhatja a GitHub-minták lapon. A jegyzetfüzet tartalmaz kibővített szakaszt az intelligens hiperparaméter hangolás, a modell üzembe helyezése és a notebook widgetek számára.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz a betanítási kísérletet a szükséges Python-csomagok betöltésével, egy munkaterület inicializálásával, egy kísérlet létrehozásával, valamint a betanítási adat és a betanítási parancsfájlok feltöltésével állítja be.

### <a name="import-packages"></a>Csomagok importálása

Először importálja a szükséges Python-kódtárakat.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum létrehozásával érheti el a munkaterület összetevőit.

Hozzon létre egy munkaterület-objektumot az [Előfeltételek szakaszban](#prerequisites)létrehozott `config.json` fájlból.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Mélyreható tanulási kísérlet létrehozása

Hozzon létre egy kísérletet és egy mappát a betanítási szkriptek tárolásához. Ebben a példában hozzon létre egy "TF-mnist" nevű kísérletet.

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Fájl adatkészletének létrehozása

A `FileDataset` objektum egy vagy több fájlra hivatkozik a munkaterület adattárában vagy a nyilvános URL-címekben. A fájlok bármilyen formátumúak lehetnek, és a osztály lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számítási feladatokhoz. `FileDataset`létrehozásával létrehoz egy hivatkozást az adatforrás helyére. Ha az adatkészletbe átalakításokat alkalmazott, azokat az adatkészletben is tárolja a rendszer. Az adattárolók a meglévő helyükön maradnak, így nem merülnek fel extra tárolási költségek. További információért tekintse [meg a `Dataset` csomag útmutatóját](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) .

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

A `register()` módszer használatával regisztrálja az adatkészletet a munkaterületen, hogy másokkal is megoszthatók legyenek, újra felhasználható a különböző kísérletekben, és nevük alapján a betanítási szkriptben.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Számítási cél létrehozása

Hozzon létre egy számítási célt a TensorFlow-feladatokhoz a futtatásához. Ebben a példában hozzon létre egy GPU-kompatibilis Azure Machine Learning számítási fürtöt.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

A számítási célokkal kapcsolatos további információkért tekintse meg a [Mi az a számítási cél](concept-compute-target.md) című cikket.

## <a name="create-a-tensorflow-estimator"></a>TensorFlow-kalkulátor létrehozása

A [TensorFlow kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) egyszerű módszert kínál a TensorFlow-betanítási feladatok számítási célra való elindítására.

A TensorFlow-kalkulátor az általános [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályon keresztül valósul meg, amely bármilyen keretrendszer támogatásához használható. További információ a képzési modellekről az általános kalkulátor használatával: [modellek betanítása Azure Machine learning a kalkulátor használatával](how-to-train-ml-models.md)

Ha a betanítási szkript további pip-vagy Conda-csomagokat igényel, akkor az eredményül kapott Docker-rendszerképre is telepíthetők, ha a `pip_packages` és `conda_packages` argumentumokkal átadják a nevüket.

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> A **Tensorflow 2,0** támogatása a Tensorflow kalkulátor osztályhoz lett hozzáadva. További információt a [blogbejegyzésben](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) talál.

A Python-környezet testreszabásával kapcsolatos további információkért lásd: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Futtatás küldése

A [Run objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biztosítja a felületet a futtatási előzményekhez, miközben a feladatot futtatja, és a művelet befejeződött.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

A Futtatás végrehajtásakor a következő szakaszokon halad végig:

- **Előkészítés**: a Docker-rendszerkép létrehozása a TensorFlow-kalkulátor alapján történik. A rendszer feltölti a rendszerképet a munkaterület tároló-Hivatalához, és a gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók a futtatási előzményekre is továbbítva lesznek, és a folyamat figyelésére is megtekinthetők.

- **Skálázás**: a fürt akkor kísérli meg a skálázást, ha a Batch AI fürthöz több csomópont szükséges a jelenleg elérhető futtatáshoz.

- **Futtatás**: a rendszer a parancsfájl mappájában lévő összes parancsfájlt feltölti a számítási célra, az adattárakat csatlakoztatja vagy másolja, és a entry_script végre lesz hajtva. Az stdout és a./Logs mappa kimeneteit a rendszer a futtatási előzményekre továbbítja, és a Futtatás figyelésére használható.

- **Utómunka**: a Futtatás./outputs mappáját a rendszer átmásolja a futtatási előzményekbe.

## <a name="register-or-download-a-model"></a>Modell regisztrálása vagy letöltése

A modell kiképzése után regisztrálhatja azt a munkaterületre. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében. A paraméterek `model_framework`, `model_framework_version`és `resource_configuration`paraméterek megadásával elérhetővé válik a nem kód modell telepítése. Ez lehetővé teszi a modell közvetlen üzembe helyezését webszolgáltatásként a regisztrált modellből, a `ResourceConfiguration` objektum pedig meghatározza a webszolgáltatás számítási erőforrását.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

A modell helyi másolatát a Run objektum használatával is letöltheti. A betanítási parancsfájl `mnist-tf.py`ban egy TensorFlow-megtakarítási objektum a modellt egy helyi mappába (a számítási cél számára) őrzi meg. A Futtatás objektum használatával letöltheti a másolatokat.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Elosztott betanítás

A [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) kalkulátor a processzor-és a GPU-fürtökön egyaránt támogatja az elosztott képzést. Könnyedén futtathatja az elosztott TensorFlow-feladatokat, és Azure Machine Learning felügyeli az Ön számára.

A Azure Machine Learning a TensorFlow-ben az elosztott képzés két módszerét támogatja:

- [MPI-alapú](https://www.open-mpi.org/) elosztott képzések a [Horovod](https://github.com/uber/horovod) -keretrendszer használatával
- Natív [elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed) a paraméter-kiszolgáló metódus használatával

### <a name="horovod"></a>Horovod

A [Horovod](https://github.com/uber/horovod) egy nyílt forráskódú keretrendszer az Über által fejlesztett elosztott képzésekhez. Az elosztott GPU TensorFlow feladatok egyszerű elérési útját kínálja.

A Horovod használatához meg kell adnia egy [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objektumot a `distributed_training` paraméterhez a TensorFlow konstruktorban. Ez a paraméter biztosítja, hogy a Horovod-függvénytár telepítve legyen a betanítási parancsfájlban.

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]')
```

### <a name="parameter-server"></a>Paraméter-kiszolgáló

Futtathat olyan [natív elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed)is, amely a paraméter-kiszolgálói modellt használja. Ebben a metódusban a paraméter-kiszolgálók és a feldolgozók fürtje között kell betanítania. A dolgozók kiszámítják a színátmeneteket a betanítás során, míg a paraméter-kiszolgálók összesítik a színátmeneteket.

A paraméter-kiszolgáló metódus használatához a TensorFlow konstruktorban meg kell adnia egy [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) objektumot a `distributed_training` paraméterhez.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]')

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Adja meg a fürt specifikációit a következőben: "TF_CONFIG"

Szükség van a [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)-fürt hálózati címeire és portjaira is, így Azure Machine learning beállítja az `TF_CONFIG` környezeti változót.

A `TF_CONFIG` környezeti változó egy JSON-karakterlánc. Íme egy példa a paraméter-kiszolgáló változóra:

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

A TensorFlow magas szintű [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) API-ja a TensorFlow elemzi a `TF_CONFIG` változót, és létrehozza a fürt specifikációját.

A TensorFlow alsóbb szintű alapszintű API-jai a betanításhoz, a `TF_CONFIG` változó elemzéséhez és a `tf.train.ClusterSpec` kiépítéséhez a képzési kódban.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deployment"></a>Üzembe helyezés

A korábban regisztrált modell ugyanúgy helyezhető üzembe, mint bármely más regisztrált modell Azure Machine Learningban, függetlenül attól, hogy milyen kalkulátort használt a betanításhoz. Az üzembe helyezési útmutató egy szakaszt tartalmaz a modellek regisztrálásához, de közvetlenül kihagyhatja a központi telepítéshez szükséges [számítási cél létrehozását](how-to-deploy-and-where.md#choose-a-compute-target) , mivel már rendelkezik regisztrált modellel.

### <a name="preview-no-code-model-deployment"></a>Előnézet Nem kód modell telepítése

A hagyományos üzembe helyezési útvonal helyett a kód nélküli üzembe helyezési funkciót (előzetes verzió) is használhatja a Tensorflow. Ha a fentiekben látható módon regisztrálja a modellt a `model_framework`, `model_framework_version`és `resource_configuration` paraméterekkel, egyszerűen használhatja az `deploy()` statikus függvényt a modell üzembe helyezéséhez.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

A teljes körű [útmutató](how-to-deploy-and-where.md) a Azure Machine learning nagyobb részletességgel történő üzembe helyezését ismerteti.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy TensorFlow-modellt oktatott és regisztrált, és megismerte az üzembe helyezési lehetőségeket. Ezekről a cikkekről további tudnivalókat talál a Azure Machine Learningról.

* [A futtatási metrikák nyomon követése a betanítás során](how-to-track-experiments.md)
* [Hiperparaméterek beállítása hangolása](how-to-tune-hyperparameters.md)
* [Az Azure-ban elosztott Deep learning-képzés hivatkozási architektúrája](/azure/architecture/reference-architectures/ai/training-deep-learning)
