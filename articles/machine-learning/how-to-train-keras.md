---
title: Vonat mélytanulás Keras modellek
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan taníthatja be és regisztrálhatja a TensorFlow-n futó Keras-mély neurális hálózati besorolási modellt az Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: ba7976d602412037578d0a324916718b2d515aac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269964"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Keras-besorolási modell betanítása és regisztrálása az Azure Machine Learning segítségével
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk bemutatja, hogyan betanítása és regisztrálása a Keras besorolási modell a TensorFlow-ra épülve az Azure Machine Learning használatával. A népszerű [MNIST adatkészletet](http://yann.lecun.com/exdb/mnist/) használja a kézzel írt számjegyek osztályozására egy mély neurális hálózat (DNN) használatával, amelya [TensorFlow](https://www.tensorflow.org/overview)tetején futó [Keras Python-könyvtár](https://keras.io) használatával készült.

A Keras egy magas szintű neurális hálózati API, amely képes más népszerű DNN-keretrendszerek tetején futtatni a fejlesztés egyszerűsítése érdekében. Az Azure Machine Learning segítségével gyorsan kiskálázhatja a képzési feladatokat a rugalmas felhőalapú számítási erőforrások használatával. Nyomon követheti a betanítási futtatásokat, a verziómodelleket, a modellek üzembe helyezését és még sok mást is.

Akár egy Keras-modellt fejleszt az alapoktól kezdve, akár egy meglévő modellt hoz a felhőbe, az Azure Machine Learning segítségével éles környezetre kész modelleket hozhat létre.

A gépi tanulás és a mélytanulás közötti különbségekről a [koncepcionális cikkben](concept-deep-learning-vs-machine-learning.md) olvashat.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot az alábbi környezetek valamelyikén:

- Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

     - Töltse ki az [oktatóanyag: Telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) hozzon létre egy dedikált notebook-kiszolgáló előre betöltött az SDK és a mintatár.
    - A jegyzetfüzet-kiszolgáló mintamappájában keressen egy kitöltött és kibontott jegyzetfüzetet a következő könyvtárba navigálva: **hogyan használható azureml > a betanítás-mély tanulással > a vonat-hiperparaméter-tune-deploy-with-keras** mappába.

 - Saját Jupyter Notebook szerver

    - [Telepítse az Azure Machine Learning SDK-t.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
    - [Munkaterület-konfigurációs fájl létrehozása](how-to-configure-environment.md#workspace).
    - [Töltse le a minta parancsfájlfájlokat,](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` és`utils.py`

    Az útmutató elkészült [Jupyter Notebook-verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) is megtalálhatja a GitHub minták lapján. A jegyzetfüzet tartalmaz kibővített szakaszok, amelyek intelligens hyperparameter tuning, modell üzembe helyezés, és a notebook widgetek.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz úgy állítja be a betanítási kísérletet, hogy betölti a szükséges python-csomagokat, inicializálja a munkaterületet, létrehoz egy kísérletet, és feltölti a betanítási adatokat és a betanítási parancsfájlokat.

### <a name="import-packages"></a>Csomagok importálása

Először importálja a szükséges Python-kódtárakat.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

Az [Azure Machine Learning-munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Ez biztosítja az Ön számára egy központosított hely, ahol az összes létrehozott műtermék. A Python SDK-ban egy [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum létrehozásával érheti el a munkaterületi összetevőket.

Hozzon létre egy `config.json` munkaterületi objektumot az [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból.

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy "keras-mnist" nevű kísérletet a munkaterületen.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>TensorFlow-becslés létrehozása és Keras importálása

A [TensorFlow-becslés](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) egyszerű módot biztosít a TensorFlow képzési feladatok számítási célra való indítására. Mivel a Keras a TensorFlow tetején fut, használhatja a TensorFlow-becslést, és importálhatja a Keras-könyvtárat az `pip_packages` argumentum mal.

Először az adatokat a munkaterületi `Dataset` adattárból az osztály használatával.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

A TensorFlow-becslés az általános [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályon keresztül valósul meg, amely bármely keretrendszer támogatására használható. Ezenkívül hozzon létre `script_params` egy szótárat, amely tartalmazza a DNN hiperparaméter-beállításokat. Az általános becslést használó betanítási modellekről további információt az [Azure Machine Learning használatával a becsléssel rendelkező modellek betanítása című](how-to-train-ml-models.md) témakörben talál.

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
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

## <a name="submit-a-run"></a>Futtatás beküldése

A [Futtatás objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biztosítja a kapcsolatot a futtatási előzményekhez a feladat futása közben és befejezése után.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

A futtatás végrehajtásakor a következő szakaszokon megy keresztül:

- **Előkészítés:** A Docker-rendszerkép a TensorFlow-becslés nek megfelelően jön létre. A rendszerképet a rendszer feltölti a munkaterület tárolójának rendszerleíró adatbázisába, és gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók is streamelt a futtatási előzmények, és megtekinthető konkretizált a folyamat figyelése érdekében.

- **Méretezés**: A fürt megkísérli a felskálázást, ha a Batch AI-fürtnek a jelenleg rendelkezésre állónál több csomópontra van szüksége a futtatás végrehajtásához.

- **Futtatás:** A parancsfájlmappában lévő összes parancsfájl feltöltésre kerül a számítási célba, az adattárak csatlakoztatva vagy másolva, és a entry_script végrehajtása. Az stdout és a ./logs mappa kimenetei a futtatási előzményekbe kerülnek, és a futtatás figyelésére használhatók.

- **Utófeldolgozás:** A futtatás ./outputok mappája átkerül a futtatási előzményekbe.

## <a name="register-the-model"></a>Regisztrálja a modellt

Miután betanította a DNN-modellt, regisztrálhatja azt a munkaterületre. A modellregisztráció lehetővé teszi a modellek tárolását és verziózását a munkaterületen a [modellkezelés és -telepítés egyszerűsítése érdekében.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> Az imént regisztrált modell ugyanúgy van üzembe helyezve, mint bármely más regisztrált modell az Azure Machine Learningben, függetlenül attól, hogy melyik betanításhoz használt becslést használja. Az üzembe helyezési útmutató tartalmaz egy szakaszt a modellek regisztrálása, de közvetlenül átugorhatja [a számítási cél](how-to-deploy-and-where.md#choose-a-compute-target) létrehozása a központi telepítéshez, mivel már rendelkezik egy regisztrált modell.

A modell helyi példányát is letöltheti. Ez további modellérvényesítési munka helyi szinten való munkavégzéshez lehet hasznos. A betanítási `mnist-keras.py`parancsfájlban a TensorFlow-megtakarító objektum a modellt egy helyi mappában (a számítási cél helyi) marad. A Futtatás objektum segítségével letöltheti a másolatot az adattárból.

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

Ebben a cikkben betanított és regisztrált egy Keras-modellt az Azure Machine Learningen. Ha meg szeretné tudni, hogyan telepíthet egy modellt, folytassa a modell üzembe helyezési cikket.

> [!div class="nextstepaction"]
> [Modellek üzembe helyezése és helye](how-to-deploy-and-where.md)
* [Futási mérőszámok nyomon követése edzés közben](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Betanított modell telepítése](how-to-deploy-and-where.md)
* [Referenciaarchitektúra az elosztott mélytanulási képzéshez az Azure-ban](/azure/architecture/reference-architectures/ai/training-deep-learning)
