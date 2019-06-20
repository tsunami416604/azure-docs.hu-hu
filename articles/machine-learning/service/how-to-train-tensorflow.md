---
title: Betanítása és TensorFlow modellek regisztrálása
titleSuffix: Azure Machine Learning service
description: Ez a cikk bemutatja, hogyan betanítása és regisztrálnia kell egy TensorFlow modell Azure Machine Learning szolgáltatás használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: e1f587785b06f10bab42a425b0910d4f25fddf9f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165542"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Betanítása és ipari méretekben TensorFlow-modellek regisztrálása az Azure Machine Learning szolgáltatás

Ez a cikk bemutatja, hogyan betanítása és regisztrálnia kell egy TensorFlow modell Azure Machine Learning szolgáltatás használatával. Használja a népszerű [MNIST adatkészlet](http://yann.lecun.com/exdb/mnist/) felhasználásával Neurális hálózat használatával kézzel írott számjegyek besorolása a [TensorFlow Python-kódtár](https://www.tensorflow.org/overview).

Tensorflow-hoz egy olyan nyílt forráskódú számítási keretrendszer, általánosan használt Neurális hálózatok (DNN) létrehozása. Az Azure Machine Learning szolgáltatás gyors horizontális felskálázása lehetséges nyílt forráskódú betanítási feladatokat a rugalmas felhőalapú számítási erőforrások használatával. Is nyomon követheti a betanítási futtatás, a verzió modellek üzembe helyezése a modelleket, és még sok más.

Akár az alapoktól a TensorFlow modell fejleszt, vagy már meglévő modell üzembe a felhőben, Azure Machine Learning szolgáltatás segítségével éles használatra kész modelleket.

## <a name="prerequisites"></a>Előfeltételek

Ez a kód futtatása ezekben a környezetekben valamelyikét:

 - Az Azure Machine Learning Notebook Virtuálisgép - letöltések vagy nem szükséges telepítés

     - Végezze el a [felhőalapú notebook rövid](quickstart-run-cloud-notebook.md) hozhat létre egy dedikált notebook server előre betöltött az SDK-t és a mintaadattárat.
    - A notebook server minták mappájában található befejeződött, és a bővített Jegyzetfüzet az ebben a könyvtárban: **útmutatóval-to-használat – azureml > képzés a deep learning > train-hyperparameter-tune-deploy-with-tensorflow**mappát. 
 
 - A saját Jupyter Notebook server

     - [Telepítse az Azure Machine Learning SDK a Pythonhoz](setup-create-workspace.md#sdk)
    - [Munkaterület-konfigurációs fájl létrehozása](setup-create-workspace.md#write-a-configuration-file)
    - [Töltse le a minta parancsfájlok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` és `utils.py`
     
    Is megtalálhatja a befejezett [Jupyter Notebook verzió](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) Ez az útmutató a GitHub-minták oldalon. A notebook intelligens hiperparaméter finomhangolása, a modell üzembe helyezése és a notebook widgetek kibontott szakaszokat tartalmazza.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz által a szükséges python-csomagok betöltése, a munkaterület inicializálása, egy kísérlet létrehozásától és feltöltése a betanítási adatok és a betanítási szkriptekhez beállítja a tanítási kísérlet.

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

A [Azure Machine Learning szolgáltatás munkaterület](concept-workspace.md) a szolgáltatás a legfelső szintű erőforrás. Is tartalmaz egy központi helyen hoz létre minden összetevő dolgozhat. A Python SDK-ban, hozzáférhet a munkaterület-összetevők létrehozásával egy [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum.

A munkaterület-objektum létrehozása a `config.json` létrehozott fájlt a [előfeltételeknél](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet és a egy mappát a betanítási szkriptekhez tárolásához. Ebben a példában egy "tf-mnist" nevű kísérlet létrehozása.

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Töltse fel az adatkészlet és parancsfájlok

A [adattárolója](how-to-access-data.md) olyan hely, ahol adatok tárolásának és csatlakoztatására, vagy másolja az adatokat a számítási célnak férhetnek hozzá. Az egyes munkaterületeken alapértelmezett adattárolót biztosít. Töltse fel az adatokat és a betanítási szkriptekhez az adattárral, hogy könnyen hozzáférhetők betanítás során.

1. Töltse le helyben a MNIST adatkészlet.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Töltse fel a MNIST-adatkészlet az alapértelmezett adattárral.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Töltse fel a TensorFlow tanítási szkriptet `tf_mnist.py`, és a segítő fájl `utils.py`.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Hozzon létre egy számítási célnak

Hozzon létre egy számítási célnak, a TensorFlow feladat futtatását. Ebben a példában az Azure Machine Learning GPU-kompatibilis számítási fürt létrehozása.

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

A számítási célokhoz további információkért lásd: a [mi egy számítási célnak](concept-compute-target.md) cikk.

## <a name="create-a-tensorflow-estimator"></a>Hozzon létre egy TensorFlow estimator

A [TensorFlow estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) egy TensorFlow-betanítási feladatot egy számítási célnak a állapotára egyszerű módszert kínál.

Az általános biztosítják a TensorFlow estimator [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztály, amely bármely keretrendszer támogatásához használható. Az általános estimator használó modellek betanítása kapcsolatos további információkért lásd: [modelleket taníthat be az Azure Machine Learning estimator használatával](how-to-train-ml-models.md)

Ha a tanítási szkriptet van szüksége, további pip vagy conda-csomagok futtatása, a nevük keresztül adja át az eredményül kapott docker-rendszerképet a telepített csomagokat használhat a `pip_packages` és `conda_packages` argumentumokat.

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

## <a name="submit-a-run"></a>Küldje el a Futtatás

A [objektumot futtatni](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) a futtatási előzmények a felületet biztosít, a feladat futása közben és után befejeződött.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

A Futtatás hajtja végre, a következő szakaszokra végighalad:

- **Felkészülés**: Docker-rendszerkép a TensorFlow estimator alapján jön létre. A rendszerkép feltöltött a munkaterület container Registry tárolóregisztrációs adatbázisba, és későbbi futtatások gyorsítótárazza. Naplók a is rendszer streamként továbbítja a futtatási előzmények és megtekinthetők a folyamat állapotának monitorozásához.

- **Méretezés**: A fürt próbál vertikális felskálázás, ha a Batch AI-fürt csomópontjait a Futtatás végrehajtásához, mint a jelenleg elérhető igényel.

- **Futó**: A parancsfájl mappában található összes parancsfájl töltenek fel a számítási célnak, adattárak csatlakoztatva van, vagy másolja és a entry_script hajtja végre. Az stdout adatsorból kimenetek és a. / logs mappában a rendszer streamként továbbítja a futtatási előzmények és a Futtatás figyelésére használható.

- **Utófeldolgozási**: A. / kimenete a Futtatás mappába másolja a rendszer a futtatási előzményekben.

## <a name="register-or-download-a-model"></a>Regisztráljon vagy egy modell letöltése

Ha a modell már betanított, regisztrálhatja a munkaterülethez. Modell regisztrálását lehetővé teszi a tároló és verzió egyszerűsítése érdekében a munkaterület a modellek [kezelés és üzembe helyezési modell](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

A modell egy helyi példányát futtató objektum segítségével is letöltheti. A képzési szkriptben `mnist-tf.py`, a TensorFlow képernyővédő objektum továbbra is fennáll, a modell egy helyi mappába (helyi, a számítási célnak). Ha szeretné letölteni a Futtatás objektum segítségével.

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

A [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator is támogatja az elosztott képzési Processzor és GPU-fürtök között. Könnyedén futtathat elosztott TensorFlow-feladatok és az Azure Machine Learning szolgáltatás fogja kezelni a vezénylési az Ön számára.

Az Azure Machine Learning szolgáltatás elosztott képzési két módszert támogat, a tensorflow-hoz:

- [MPI-alapú](https://www.open-mpi.org/) elosztott képzési használatával a [Horovod](https://github.com/uber/horovod) keretrendszer
- Natív [elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed) metoda paraméter használatával

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) Uber által fejlesztett elosztott betanítás egy nyílt forráskódú keretrendszer van. Biztosít egy elosztott GPU TensorFlow-feladatok egyszerű elérési útját.

Horovod használatához adja meg egy [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objektum a `distributed_training` paraméter, a TensorFlow-konstruktor. Ez a paraméter biztosítja, hogy Horovod könyvtár telepítve van-e a tanítási szkriptet a használatra.

```Python
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

A paraméter a kiszolgáló módszert használja, adja meg egy [ `TensorflowConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) objektum a `distributed_training` paraméter, a TensorFlow-konstruktor.

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

#### <a name="define-cluster-specifications-in-tfconfig"></a>"TF_CONFIG" fürt specifikációk meghatározása

Emellett a hálózati címek és portok, a fürt a [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), így az Azure Machine Learning beállítja a `TF_CONFIG` környezeti változót az Ön számára.

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

A tensorflow-hoz a magas szintű [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API-t, TensorFlow elemzi a `TF_CONFIG` változót, és az Ön számára a fürt specifikációja buildek.

A betanítási tensorflow-hoz az alacsonyabb szintű core API-k, elemezni a `TF_CONFIG` változót és a build a `tf.train.ClusterSpec` a betanítási kódban.

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

Ebben a cikkben betanított és a egy TensorFlow modell Azure Machine Learning szolgáltatásban regisztrált. Megtudhatja, hogyan helyezhet üzembe modelleket, és továbbléphet a modell üzembe helyezési című cikkben.

> [!div class="nextstepaction"]
> [Hogyan és hol érdemes a modellek üzembe helyezése](how-to-deploy-and-where.md)
