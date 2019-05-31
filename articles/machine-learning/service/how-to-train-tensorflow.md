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
ms.date: 05/28/2019
ms.custom: seodec18
ms.openlocfilehash: f3d675d0eac1255974995fd7717192ec6a21bac1
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400228"
---
# <a name="use-azure-machine-learning-service-to-train-and-register-tensorflow-models"></a>Az Azure Machine Learning szolgáltatás segítségével betanítása és TensorFlow modellek regisztrálása

Ez a cikk bemutatja, hogyan betanítása és regisztrálnia kell egy TensorFlow modell Azure Machine Learning szolgáltatás használatával. Fogjuk használni a népszerű [MNIST adatkészlet](http://yann.lecun.com/exdb/mnist/) besorolására kézírásos számjegy, a tensorflow-hoz készült Neurális hálózat használatával.

Az Azure Machine Learning szolgáltatással, képes lesz gyors horizontális felskálázása a nyílt forráskódú betanítási feladatok rugalmas felhőalapú számítási erőforrások használatával. Azt is nyomon követheti a betanítási futtatás, a verzió modellek, modelleket és még sok más központi telepítése. Akár az alapoktól a TensorFlow modell fejleszt, vagy már meglévő modell üzembe a felhőben, Azure Machine Learning szolgáltatás készségesen segít Önnek, éles használatra kész modelleket.

## <a name="prerequisites"></a>Előfeltételek

- Telepítse az Azure Machine Learning SDK a Pythonhoz
- Nem kötelező: Munkaterület-konfigurációs fájl létrehozása
- Töltse le a [minta parancsfájlok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` és `utils.py`

Kövesse a [Python SDK-beállítási útmutató](setup-create-workspace.md#sdk) kapcsolatos lépésenkénti útmutatót egy környezet felépítésének lépésein. A mintafájlok képzési találhatók a [minták GitHub-oldalon](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) mellett ez az útmutató a kiterjesztett, Juypter Notebook verzióját.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

### <a name="import-packages"></a>Csomagok importálása

Először szükségünk lesz importálja a szükséges Python-kódtárakat.

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

A munkaterület objektum a szolgáltatás a legfelső szintű erőforrás. Is tartalmaz egy központi helyen hoz létre minden összetevő dolgozhat.

Ha elvégezte a nem kötelező lépése az [előfeltételeknél](#prerequisites), használhat `Workspace.from_config()` hozhat létre gyorsan egy munkaterület-objektumot a a konfigurációs fájlban tárolt adatokból.

```Python
ws = Workspace.from_config()
```

Is is létrehozhat munkaterület explicit módon.

```Python
ws = Workspace.create(name='<workspace-name>',
                      subscription_id='<azure-subscription-id>',
                      resource_group='<choose-a-resource-group>',
                      create_resource_group=True,
                      location='<select-location>' # For example: 'eastus2'
                      )
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet és a egy mappát a betanítási szkriptekhez tárolásához. Ebben a példában egy "tf-mnist" nevű kísérlet létrehozása

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Töltse fel az adatkészlet és parancsfájlok

A [adattárolója](how-to-access-data.md) olyan hely, ahol adatok tárolásának és csatlakoztatására, vagy másolja az adatokat a számítási célnak férhetnek hozzá. Az egyes munkaterületeken alapértelmezett adattárolót biztosít. Az adatok és a betanítási szkriptekhez, hogy könnyen hozzáférhetők betanítás során fog fel.

1. Töltse le helyben a MNIST-adatkészlet

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

Hozzon létre egy számítási célnak, a TensorFlow feladat futtatását. Ebben a példában létrehozunk egy GPU-kompatibilis AmlCompute fürtöt. Rendelkezésre álló képzési listáját számítási céljainak, lásd: [Ez a cikk](how-to-set-up-training-targets.md#compute-targets-for-training)

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

## <a name="create-a-tensorflow-estimator"></a>Hozzon létre egy TensorFlow estimator

A [TensorFlow estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) egy TensorFlow-betanítási feladatot egy számítási célnak a állapotára egyszerű módszert kínál. Ez automatikusan biztosít egy docker-rendszerképet, amely rendelkezik a telepített tensorflow-hoz.

Nevük keresztül adja át az eredményül kapott docker-rendszerkép további pip vagy conda-csomagokat is felvehet a `pip_packages` és `conda_packages` argumentumokat.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='tf_mnist.py',
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Küldje el a Futtatás

A [objektumot futtatni](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) a futtatási előzmények a felületet biztosít, a feladat futása közben és után befejeződött.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

A Futtatás hajtja végre, akkor fog áthaladni a következő szakaszokra:

- **Felkészülés**: Docker-rendszerkép a TensorFlow estimator alapján jön létre. A rendszerkép feltöltött a munkaterület container Registry tárolóregisztrációs adatbázisba, és későbbi futtatások gyorsítótárazza. Naplók a is rendszer streamként továbbítja a futtatási előzmények és megtekinthetők a folyamat állapotának monitorozásához.

- **Skálázás**: A fürt vertikális felskálázása esetén megkísérli az a Batch AI-fürt csomópontjait a Futtatás végrehajtásához, mint a jelenleg elérhető igényel.

- **Futó**: A parancsfájl mappában található összes parancsfájl töltenek fel a számítási célnak, adattárak csatlakoztatva van, vagy másolja és a entry_script hajtja végre. Az stdout adatsorból kimeneti és a. / logs mappában a rendszer streamként továbbítja a futtatási előzmények és a Futtatás figyelésére használható.

- **Utófeldolgozási**: A. / kimenete a Futtatás mappába másolja a rendszer a futtatási előzményekben.

## <a name="register-or-download-a-model"></a>Regisztráljon vagy egy modell letöltése

Ha a modell már betanított, regisztrálhatja a munkaterülethez. Modell regisztrálását lehetővé teszi a tároló és verzió egyszerűsítése érdekében a munkaterület a modellek [kezelés és üzembe helyezési modell](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

A modell egy helyi példányát futtató objektum segítségével is letöltheti. A képzési szkriptben `mnist-tf.py`, a TensorFlow képernyővédő objektum továbbra is fennáll, a modell egy helyi mappába (helyi, a számítási célnak). A Futtatás objektum használatával egy másolatának letöltése.

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

* [MPI-alapú](https://www.open-mpi.org/) elosztott képzési használatával a [Horovod](https://github.com/uber/horovod) keretrendszer
* Natív [elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed) metoda paraméter használatával

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) Uber által fejlesztett elosztott betanítás egy nyílt forráskódú keretrendszer van. Biztosít egy elosztott GPU TensorFlow-feladatok egyszerű elérési útját.

Horovod használatához adja meg `mpi` számára a `distributed_training` paraméter, a TensorFlow estimator konstruktor. A tanítási szkriptet használhat Horovod lesz telepítve.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Paraméterkiszolgáló

Futtathat [natív elosztott TensorFlow](https://www.tensorflow.org/deploy/distributed), melyik a paraméter kiszolgálómodellt használ. Ezzel a módszerrel, betanítását paraméter kiszolgálók és a munkavállalók fürtök között. A feldolgozók kiszámítása során képzés, a átmenetekhez, a paraméter kiszolgálók összesíteni az átmenetek során.

A paraméter a kiszolgáló módszert használja, adja meg a `ps` számára a `distributed_training` paraméter, a TensorFlow estimator konstruktor.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='ps',
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Vegye figyelembe a `TF_CONFIG`

Biztosítani kell a hálózati címek és portok, a fürt a [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), így az Azure Machine Learning beállítja a `TF_CONFIG` környezeti változót az Ön számára.

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

A tensorflow-hoz a magas szintű [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, a tensorflow-hoz fogja elemezni a `TF_CONFIG` változót és a fürt létrehozási specifikációja az Ön számára.

A betanítási tensorflow-hoz az alacsonyabb szintű core API-k, elemezni a `TF_CONFIG` változót és a build a `tf.train.ClusterSpec` a betanítási kódban. A [ebben a példában](https://aka.ms/aml-notebook-tf-ps), ezért teheti **a tanítási szkriptet** módon:

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
