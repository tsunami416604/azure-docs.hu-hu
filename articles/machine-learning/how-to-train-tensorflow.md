---
title: TensorFlow-modell betanítása és üzembe helyezése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan futtathat TensorFlow-betanítási parancsfájlokat az Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2bbd81f3858aa78b9e0e2d610c0fdb0a67816c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228312"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Az Azure Machine Learning segítségével nagy méretekben hozhat létre TensorFlow-mélytanulási modellt
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk bemutatja, hogyan futtathatja a [TensorFlow-betanítási](https://www.tensorflow.org/overview) parancsfájlokat az Azure Machine Learning [TensorFlow-becslési](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) osztályával. Ez a példa bevonatozése és regisztrál egy TensorFlow-modellt a kézzel írt számjegyek besorolásához egy mély neurális hálózat (DNN) használatával.

Akár egy TensorFlow-modellt fejleszt az alapoktól kezdve, akár egy [meglévő modellt](how-to-deploy-existing-model.md) hoz a felhőbe, az Azure Machine Learning segítségével kibővítheti a nyílt forráskódú betanítási feladatokat éles környezetben modellek létrehozásához, üzembe helyezéséhez, verziójához és figyeléséhez.

További információ a [mélytanulásról és a gépi tanulásról.](concept-deep-learning-vs-machine-learning.md)

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot az alábbi környezetek valamelyikén:

 - Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

     - Töltse ki az [oktatóanyag: Telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) hozzon létre egy dedikált notebook-kiszolgáló előre betöltött az SDK és a mintatár.
    - A minták deep learning mappában a jegyzetfüzet-kiszolgálón, keressen egy kitöltött és kibontott jegyzetfüzetet a címtárba való navigálással: **how-to-use-azureml > ml-keretrendszerek > a tensorflow-> üzembe helyezési > a vonat-hiperparaméter-tune-deploy-with-tensorflow** mappát. 
 
 - Saját Jupyter Notebook szerver

    - [Telepítse az Azure Machine Learning SDK-t.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
    - [Munkaterület-konfigurációs fájl létrehozása](how-to-configure-environment.md#workspace).
    - [Töltse le a minta parancsfájlfájlokat,](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` és`utils.py`
     
    Az útmutató elkészült [Jupyter Notebook-verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) is megtalálhatja a GitHub minták lapján. A jegyzetfüzet tartalmaz kibővített szakaszok, amelyek intelligens hyperparameter tuning, modell üzembe helyezés, és a notebook widgetek.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz úgy állítja be a betanítási kísérletet, hogy betölti a szükséges Python-csomagokat, inicializálja a munkaterületet, létrehoz egy kísérletet, és feltölti a betanítási adatokat és a betanítási parancsfájlokat.

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

Az [Azure Machine Learning-munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Ez biztosítja az Ön számára egy központosított hely, ahol az összes létrehozott műtermék. A Python SDK-ban egy [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum létrehozásával érheti el a munkaterületi összetevőket.

Hozzon létre egy `config.json` munkaterületi objektumot az [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Mélytanulási kísérlet létrehozása

Hozzon létre egy kísérletet és egy mappát a betanítási parancsfájlok tárolására. Ebben a példában hozzon létre egy "tf-mnist" nevű kísérletet.

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Fájladatkészlet létrehozása

Egy `FileDataset` objektum egy vagy több fájlra hivatkozik a munkaterületi adattárban vagy a nyilvános URL-címeken. A fájlok bármilyen formátumúak lehetnek, és az osztály lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számításhoz. A létrehozásával `FileDataset`hivatkozást hozhat létre az adatforrás helyére. Ha bármilyen átalakítást alkalmazott az adatkészletre, azok is az adatkészletben lesznek tárolva. Az adatok a meglévő helyen maradnak, így nem merül fel további tárolási költség. További információkért tekintse meg `Dataset` [a](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) csomagútmutatót.

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

A `register()` módszerrel regisztrálhatja az adatkészletet a munkaterületre, hogy azok megoszthatók legyenek másokkal, újra felhasználhatók legyenek a különböző kísérletek ben, és név szerint hivatkozva a betanítási parancsfájlban.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Számítási cél létrehozása

Hozzon létre egy számítási célt a TensorFlow-feladat futtatásához. Ebben a példában hozzon létre egy GPU-kompatibilis Azure Machine Learning számítási fürt.

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

A számítási célokról további információt a [számítási célcikkben](concept-compute-target.md) talál.

## <a name="create-a-tensorflow-estimator"></a>TensorFlow-becslés létrehozása

A [TensorFlow-becslés](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) egyszerű módot biztosít a TensorFlow képzési feladat elindítására egy számítási célon.

A TensorFlow-becslés az általános [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályon keresztül valósul meg, amely bármely keretrendszer támogatására használható. Az általános becslést használó betanítási modellekről további információt az [Azure Machine Learning használatával a becsléssel rendelkező modellek betanítása című](how-to-train-ml-models.md) témakörben talál.

Ha a betanítási parancsfájl további pip- vagy conda csomagok futtatásához szükséges, a csomagok at `pip_packages` az `conda_packages` eredményül kapott Docker-rendszerképre telepítheti, ha átadja a nevüket a és az argumentumokat.

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
> A **Tensorflow 2.0** támogatása hozzá lett adva a Tensorflow estimator osztályhoz. További információt a [blogbejegyzésben](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) talál.

A Python-környezet testreszabásáról további információt a [Környezetek létrehozása és kezelése betanításhoz és üzembe helyezéshez című témakörben talál.](how-to-use-environments.md) 

## <a name="submit-a-run"></a>Futtatás beküldése

A [Futtatás objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biztosítja a kapcsolatot a futtatási előzményekhez a feladat futása közben és befejezése után.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

A futtatás végrehajtásakor a következő szakaszokon megy keresztül:

- **Előkészítés:** A Docker-lemezkép a TensorFlow-becslésnek megfelelően jön létre. A rendszerképet a rendszer feltölti a munkaterület tárolójának rendszerleíró adatbázisába, és gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók is streamelt a futtatási előzmények, és megtekinthető konkretizált a folyamat figyelése érdekében.

- **Méretezés**: A fürt megkísérli a felskálázást, ha a Batch AI-fürtnek a jelenleg rendelkezésre állónál több csomópontra van szüksége a futtatás végrehajtásához.

- **Futtatás:** A parancsfájlmappában lévő összes parancsfájl feltöltésre kerül a számítási célba, az adattárak csatlakoztatva vagy másolva, és a entry_script végrehajtása. Az stdout és a ./logs mappa kimenetei a futtatási előzményekbe kerülnek, és a futtatás figyelésére használhatók.

- **Utófeldolgozás:** A futtatás ./outputok mappája átkerül a futtatási előzményekbe.

## <a name="register-or-download-a-model"></a>Modell regisztrálása vagy letöltése

Miután betanította a modellt, regisztrálhatja azt a munkaterületre. A modellregisztráció lehetővé teszi a modellek tárolását és verziózását a munkaterületen a [modellkezelés és -telepítés egyszerűsítése érdekében.](concept-model-management-and-deployment.md) A paraméterek megadásával `model_framework` `model_framework_version`, `resource_configuration`és a , no-code modell központi telepítés elérhetővé válik. Ez lehetővé teszi, hogy közvetlenül üzembe helyezze a `ResourceConfiguration` modellt webszolgáltatásként a regisztrált modellből, és az objektum határozza meg a webszolgáltatás számítási erőforrását.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

A modell helyi példányát is letöltheti a Futtatás objektum használatával. A betanítási parancsfájlban `mnist-tf.py`a TensorFlow-megtakarító objektum a modellt egy helyi mappában (a számítási cél helyi) marad. A Futtatás objektummal letöltheti a másolatot.

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

A [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) becslés támogatja a CPU- és GPU-fürtök közötti elosztott oktatást is. Egyszerűen futtathatja az elosztott TensorFlow-feladatokat, és az Azure Machine Learning kezeli a vezénylési az Ön számára.

Az Azure Machine Learning két elosztott képzési módszert támogat a TensorFlow-ban:

- [MPI-alapú](https://www.open-mpi.org/) elosztott képzés a [Horovod keretrendszer](https://github.com/uber/horovod) használatával
- Natív [elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed) a kiszolgáló paramétermetódusával

### <a name="horovod"></a>Horovod

[A Horovod](https://github.com/uber/horovod) az Uber által kifejlesztett, elosztott képzés nyílt forráskódú keretrendszere. Egyszerű utat kínál az elosztott GPU TensorFlow feladatokhoz.

Horovod használatához adjon [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) meg egy `distributed_training` objektumot a TensorFlow konstruktorparaméteréhez. Ez a paraméter biztosítja, hogy a Horovod könyvtár telepítve van a betanítási parancsfájlban való használatra.

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
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>Paraméterkiszolgáló

Natív [elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed)is futtatható, amely a paraméterkiszolgáló-modellt használja. Ebben a módszerben paraméterkiszolgálók és dolgozók fürtjében kell betanítást. A dolgozók a betanítás során kiszámítják a színátmeneteket, míg a paraméterkiszolgálók összesítik a színátmeneteket.

A paraméterkiszolgáló metódusának használatához [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) adjon `distributed_training` meg egy objektumot a TensorFlow konstruktorban lévő paraméterhez.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Fürtspecifikációk meghatározása a "TF_CONFIG"

A fürt hálózati címére és portjaira is szüksége van, így az [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)Azure Machine Learning beállítja a `TF_CONFIG` környezeti változót.

A `TF_CONFIG` környezeti változó egy JSON-karakterlánc. Íme egy példa egy paraméterkiszolgáló változójára:

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

A TensorFlow magas [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) szintű API-ja esetén a `TF_CONFIG` TensorFlow elemzi a változót, és létrehozza a fürtspecifikációt.

A TensorFlow alacsonyabb szintű alap API-k a képzés, elemezze a `TF_CONFIG` változót, és létrehozza a `tf.train.ClusterSpec` betanítási kódot.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>TensorFlow-modell üzembe helyezése

Az imént regisztrált modell ugyanúgy telepíthető, mint bármely más regisztrált modell az Azure Machine Learningben, függetlenül attól, hogy melyik betanítási becslést használta. Az üzembe helyezési útmutató tartalmaz egy szakaszt a modellek regisztrálása, de közvetlenül átugorhatja [a számítási cél](how-to-deploy-and-where.md#choose-a-compute-target) létrehozása a központi telepítéshez, mivel már rendelkezik egy regisztrált modell.

## <a name="preview-no-code-model-deployment"></a>(Előzetes verzió) Kód nélküli modell központi telepítése

Ahelyett, hogy a hagyományos központi telepítési útvonal, használhatja a kód nélküli központi telepítési szolgáltatás (előzetes verzió) a Tensorflow. A modell regisztrálásával a fent `model_framework` `model_framework_version`látható `resource_configuration` a , és a `deploy()` paramétereket, egyszerűen használja a statikus függvényt a modell üzembe helyezéséhez.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

A teljes [útmutató](how-to-deploy-and-where.md) részletesebben ismerteti az Azure Machine Learning üzembe helyezését.

## <a name="next-steps"></a>További lépések

Ebben a cikkben betanított és regisztrált egy TensorFlow-modellt, és megismerkedett az üzembe helyezési lehetőségekkel. Tekintse meg ezeket az egyéb cikkeket, ha többet szeretne megtudni az Azure Machine Learningről.

* [Futási mérőszámok nyomon követése edzés közben](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Referenciaarchitektúra az elosztott mélytanulási képzéshez az Azure-ban](/azure/architecture/reference-architectures/ai/training-deep-learning)
