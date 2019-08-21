---
title: Mély tanulási neurális hálózat betanítása a TensorFlow
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan futtathat TensorFlow-betanítási szkripteket Azure Machine Learning szolgáltatás használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 9b65a9b7440922d2b1d7a02a79cc6d0811a1d9fc
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639347"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>TensorFlow mély tanulási modellt készíthet Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan futtathatja a [TensorFlow](https://www.tensorflow.org/overview) -betanítási szkripteket Azure Machine learning [TensorFlow kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) -osztályának használatával. Ez a példa egy TensorFlow-modellt vezet be, és regisztrálja a kézzel írt számjegyeket egy mély neurális hálózat (DNN) használatával.

Akár TensorFlow-modellt fejleszt ki az alapoktól, akár egy [meglévő modellt](how-to-deploy-existing-model.md) hoz létre a felhőbe, a Azure Machine learning használatával kibővítheti a nyílt forráskódú képzési feladatokat az éles modellek létrehozásához, üzembe helyezéséhez, verziójának és figyeléséhez. .

További információ a [Deep learning és a Machine learning](concept-deep-learning-vs-machine-learning.md)szolgáltatásról.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:

 - Azure Machine Learning notebook VM – nincs szükség letöltésre vagy telepítésre

     - Fejezze be [az oktatóanyagot: Az SDK-val](tutorial-1st-experiment-sdk-setup.md) és a minta adattárral előre betöltött dedikált jegyzetfüzet-kiszolgáló létrehozásához beállíthatja a környezetet és a munkaterületet.
    - A notebook-kiszolgáló minták mély tanulási mappájában keresse meg a befejezett és kibontott jegyzetfüzetet a következő könyvtárra való navigálással: **How-to-use-azureml > Training-with-Deep-learning > Train-hiperparaméter-Tune-Deploy-with-tensorflow** mappa. 
 
 - Saját Jupyter Notebook-kiszolgáló

    - [Telepítse a Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)-t.
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    - [A minta parancsfájl fájljainak letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` és`utils.py`
     
    Az útmutató egy befejezett [Jupyter notebook verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) is megtalálhatja a GitHub-minták lapon. A jegyzetfüzet tartalmaz kibővített szakaszt az intelligens hiperparaméter hangolás, a modell üzembe helyezése és a notebook widgetek számára.

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
```

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine learning szolgáltatás munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum létrehozásával érheti el a munkaterület összetevőit.

Hozzon létre egy munkaterület- `config.json` objektumot az [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból.

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

### <a name="upload-dataset-and-scripts"></a>Adatkészlet és parancsfájlok feltöltése

Az [adattár](how-to-access-data.md) olyan hely, ahol az adatok tárolása és elérése az adatoknak a számítási célra való csatlakoztatásával vagy másolásával történik. Mindegyik munkaterület alapértelmezett adattárt biztosít. Töltse fel az adatok és a betanítási szkripteket az adattárba, hogy azok könnyen elérhetők legyenek a betanítás során.

1. Töltse le helyileg a MNIST adatkészletet.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Töltse fel a MNIST-adatkészletet az alapértelmezett adattárba.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Töltse fel a TensorFlow-betanítási szkriptet `tf_mnist.py`, és a segítő `utils.py`fájlt.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Hozzon létre egy számítási célnak

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

A TensorFlow kalkulátor az általános [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályon keresztül valósul meg, amely bármely keretrendszer támogatásához használható. További információ a képzési modellekről az általános kalkulátor használatával: [modellek betanítása Azure Machine learning a kalkulátor használatával](how-to-train-ml-models.md)

Ha a képzési szkriptnek további pip-vagy Conda-csomagokat kell futtatnia, akkor az eredményül kapott Docker-rendszerképre is telepítheti a csomagokat `pip_packages` , `conda_packages` ha a és az argumentumokat át szeretné adni a nevüknek.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Futtatás küldése

A [Run objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biztosítja a felületet a futtatási előzményekhez, miközben a feladatot futtatja, és a művelet befejeződött.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

A Futtatás végrehajtásakor a következő szakaszokon halad végig:

- **Felkészülés**: A Docker-rendszerkép létrehozása a TensorFlow-kalkulátor alapján történik. A rendszer feltölti a rendszerképet a munkaterület tároló-Hivatalához, és a gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók a futtatási előzményekre is továbbítva lesznek, és a folyamat figyelésére is megtekinthetők.

- **Méretezés**: A fürt akkor kísérli meg a skálázást, ha a Batch AI-fürthöz több csomópont szükséges a jelenleg elérhető futtatáshoz.

- **Futtatás**: A rendszer a parancsfájl mappájában lévő összes parancsfájlt feltölti a számítási célra, az adattárakat csatlakoztatja vagy másolja, és a entry_script hajtja végre. Az stdout és a./Logs mappa kimeneteit a rendszer a futtatási előzményekre továbbítja, és a Futtatás figyelésére használható.

- **Feldolgozás utáni**: A Futtatás./outputs mappáját a rendszer átmásolja a futtatási előzményekbe.

## <a name="register-or-download-a-model"></a>Modell regisztrálása vagy letöltése

A modell kiképzése után regisztrálhatja azt a munkaterületre. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében.

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

A modell helyi másolatát a Run objektum használatával is letöltheti. A betanítási `mnist-tf.py`parancsfájlban egy TensorFlow-megtakarító objektum megőrzi a modellt egy helyi mappába (helyi a számítási célra). A Futtatás objektum használatával letöltheti a másolatokat.

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

A [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) kalkulátor a CPU-és a GPU-fürtökön egyaránt támogatja az elosztott képzést. Könnyedén futtathatja az elosztott TensorFlow-feladatokat, és a Azure Machine Learning szolgáltatás fogja felügyelni az Ön számára.

A Azure Machine Learning szolgáltatás a TensorFlow-ben kétféleképpen terjeszthetők ki az elosztott képzést:

- [MPI-alapú](https://www.open-mpi.org/) elosztott képzések a [Horovod](https://github.com/uber/horovod) -keretrendszer használatával
- Natív [elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed) a paraméter-kiszolgáló metódus használatával

### <a name="horovod"></a>Horovod

A [Horovod](https://github.com/uber/horovod) egy nyílt forráskódú keretrendszer az Über által fejlesztett elosztott képzésekhez. Az elosztott GPU TensorFlow feladatok egyszerű elérési útját kínálja.

A Horovod használatához meg kell adnia [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) egy objektumot `distributed_training` a paraméterhez a TensorFlow konstruktorban. Ez a paraméter biztosítja, hogy a Horovod-függvénytár telepítve legyen a betanítási parancsfájlban.

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
                      use_gpu=True)
```

### <a name="parameter-server"></a>Paraméterkiszolgáló

Futtathat [natív elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed), melyik a paraméter kiszolgálómodellt használ. Ezzel a módszerrel, betanítását paraméter kiszolgálók és a munkavállalók fürtök között. A feldolgozók kiszámítása során képzés, a átmenetekhez, a paraméter kiszolgálók összesíteni az átmenetek során.

A paraméter-kiszolgáló metódus használatához a TensorFlow konstruktorban meg kell `distributed_training` adni egy [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) objektumot a paraméterhez.

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
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>A fürt specifikációinak meghatározása a "TF_CONFIG"

Szükség van a fürt [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)hálózati címeire és portjaira is, így Azure Machine learning beállítja a `TF_CONFIG` környezeti változót.

A `TF_CONFIG` környezeti változót a JSON-karakterláncot. Íme egy példa a változó paraméter kiszolgálóhoz:

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

A TensorFlow magas szintű [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) API-ja esetében a TensorFlow elemzi a `TF_CONFIG` változót, és létrehozza a fürt specifikációját.

A TensorFlow alsóbb szintű alapszintű API-jai a betanításhoz, a `TF_CONFIG` változó elemzéséhez és a `tf.train.ClusterSpec` betanítási kódban való létrehozásához.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy TensorFlow-modellt oktatott és regisztrált. Ha meg szeretné tudni, hogyan helyezhet üzembe egy modellt egy GPU-t használó fürtön, folytassa a GPU-modell üzembe helyezési cikkével.

> [!div class="nextstepaction"]
> [Modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)
* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
* [Az Azure-ban elosztott Deep learning-képzés hivatkozási architektúrája](/azure/architecture/reference-architectures/ai/training-deep-learning)
