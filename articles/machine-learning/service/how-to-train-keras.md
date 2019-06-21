---
title: Betanítása és TensorFlow futó Keras-modellek regisztrálása
titleSuffix: Azure Machine Learning service
description: Ez a cikk bemutatja, hogyan betanítása és regisztrálnia kell egy TensorFlow futó Keras-modell Azure Machine Learning szolgáltatás használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/07/2019
ms.custom: seodec18
ms.openlocfilehash: e070b80f86cb6c8b1d9e7575e19022b5cb08f340
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165555"
---
# <a name="train-and-register-keras-models-at-scale-with-azure-machine-learning-service"></a>Betanítása és a méretezett Keras-modellek regisztrálása az Azure Machine Learning szolgáltatás

Ez a cikk bemutatja, hogyan képzése, és regisztrálja a Keras modellt, a tensorflow-hoz készült Azure Machine Learning szolgáltatás használatával. Használja a népszerű [MNIST adatkészlet](http://yann.lecun.com/exdb/mnist/) felhasználásával Neurális hálózat (DNN) használatával kézzel írott számjegyek besorolása a [Keras Python-kódtár](https://keras.io) a futó [TensorFlow](https://www.tensorflow.org/overview) .

Keras egy olyan magas szintű Neurális hálózati API képes futtatni a felső részén más elterjedt DNN-keretrendszerek használatával leegyszerűsítheti a fejlesztést. Az Azure Machine Learning szolgáltatás gyors horizontális felskálázása lehetséges betanítási feladatokat a rugalmas felhőalapú számítási erőforrások használatával. Is nyomon követheti a betanítási futtatás, a verzió modellek üzembe helyezése a modelleket, és még sok más.

Akár az alapoktól a Keras modell fejleszt, vagy már meglévő modell üzembe a felhőben, Azure Machine Learning szolgáltatás segítségével éles használatra kész modelleket.

## <a name="prerequisites"></a>Előfeltételek

Ez a kód futtatása ezekben a környezetekben valamelyikét:

 - Az Azure Machine Learning Notebook Virtuálisgép - letöltések vagy nem szükséges telepítés

     - Végezze el a [felhőalapú notebook rövid](quickstart-run-cloud-notebook.md) hozhat létre egy dedikált notebook server előre betöltött az SDK-t és a mintaadattárat.
    - A notebook server minták mappájában található befejeződött, és a bővített Jegyzetfüzet az ebben a könyvtárban: **útmutatóval-to-használat – azureml > képzés a deep learning > train-hyperparameter-tune-deploy-with-keras** a mappa. 
 
 - A saját Jupyter Notebook server

     - [Telepítse az Azure Machine Learning SDK a Pythonhoz](setup-create-workspace.md#sdk)
    - [Munkaterület-konfigurációs fájl létrehozása](setup-create-workspace.md#write-a-configuration-file)
    - [Töltse le a minta parancsfájlok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` és `utils.py`
     
    Is megtalálhatja a befejezett [Jupyter Notebook verzió](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) Ez az útmutató a GitHub-minták oldalon. A notebook intelligens hiperparaméter finomhangolása, a modell üzembe helyezése és a notebook widgetek kibontott szakaszokat tartalmazza.

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

Hozzon létre egy kísérletet és a egy mappát a betanítási szkriptekhez tárolásához. Ebben a példában egy "keras-mnist" nevű kísérlet létrehozása.

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
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

1. Töltse fel a Keras tanítási szkriptet `keras_mnist.py`, és a segítő fájl `utils.py`.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Hozzon létre egy TensorFlow estimator és Keras importálása

A [TensorFlow estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) állapotára TensorFlow betanítási feladatokat a számítási célnak egyszerű módszert kínál. Keras futtatható, TensorFlow felett fut, használhatja a TensorFlow estimator, és importálja a Keras könyvtár használatával a `pip_packages` argumentum.

Az általános biztosítják a TensorFlow estimator [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztály, amely bármely keretrendszer támogatásához használható. Az általános estimator használó modellek betanítása kapcsolatos további információkért lásd: [modelleket taníthat be az Azure Machine Learning estimator használatával](how-to-train-ml-models.md)

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
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

## <a name="register-the-model"></a>Regisztrálja a modellt

Ha a modell már betanított, regisztrálhatja a munkaterülethez. Modell regisztrálását lehetővé teszi a tároló és verzió egyszerűsítése érdekében a munkaterület a modellek [kezelés és üzembe helyezési modell](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

A modell egy helyi példányát is letölthető. Ez lehet hasznos, ha további modell érvényesítési helyileg munkát végző. A képzési szkriptben `mnist-keras.py`, a TensorFlow képernyővédő objektum továbbra is fennáll, a modell egy helyi mappába (helyi, a számítási célnak). A Futtatás objektum segítségével töltse le az adattár egy példányát.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben betanított és a egy Keras-modell Azure Machine Learning szolgáltatásban regisztrált. Megtudhatja, hogyan helyezhet üzembe modelleket, és továbbléphet a modell üzembe helyezési című cikkben.

> [!div class="nextstepaction"]
> [Hogyan és hol érdemes a modellek üzembe helyezése](how-to-deploy-and-where.md)
