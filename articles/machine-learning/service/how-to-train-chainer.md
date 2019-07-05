---
title: Betanítása és Chainer modellek regisztrálása
titleSuffix: Azure Machine Learning service
description: Ez a cikk bemutatja, hogyan képzése, és regisztrálja a Chainer modellt az Azure Machine Learning szolgáltatás használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 5057a8cf6c8769761a1a1a9f513ec46d152289ab
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67488671"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>Betanítása és ipari méretekben Chainer modellek regisztrálása az Azure Machine Learning szolgáltatás

Ez a cikk bemutatja, hogyan képzése, és regisztrálja a Chainer modellt az Azure Machine Learning szolgáltatás használatával. Használja a népszerű [MNIST adatkészlet](http://yann.lecun.com/exdb/mnist/) felhasználásával Neurális hálózat (DNN) használatával kézzel írott számjegyek besorolása a [Chainer Python-kódtár](https://Chainer.org) a futó [numpy](https://www.numpy.org/).

Chainer egy olyan magas szintű Neurális hálózati API képes futtatni a felső részén más elterjedt DNN-keretrendszerek használatával leegyszerűsítheti a fejlesztést. Az Azure Machine Learning szolgáltatás gyors horizontális felskálázása lehetséges betanítási feladatokat a rugalmas felhőalapú számítási erőforrások használatával. Is nyomon követheti a betanítási futtatás, a verzió modellek üzembe helyezése a modelleket, és még sok más.

Akár az alapoktól a Chainer modell fejleszt, vagy már meglévő modell üzembe a felhőben, Azure Machine Learning szolgáltatás segítségével éles használatra kész modelleket.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="prerequisites"></a>Előfeltételek

Ez a kód futtatása ezekben a környezetekben valamelyikét:

- Az Azure Machine Learning Notebook Virtuálisgép - letöltések vagy nem szükséges telepítés

    - Végezze el a [felhőalapú notebook rövid](quickstart-run-cloud-notebook.md) hozhat létre egy dedikált notebook server előre betöltött az SDK-t és a mintaadattárat.
    - A notebook server minták mappájában található egy befejezett jegyzetfüzet és a fájlok a **how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer** mappát.  A notebook intelligens hiperparaméter finomhangolása, a modell üzembe helyezése és a notebook widgetek kibontott szakaszokat tartalmazza.

- A saját Jupyter Notebook server

    - [Telepítse az Azure Machine Learning SDK a Pythonhoz](setup-create-workspace.md#sdk)
    - [Munkaterület-konfigurációs fájl létrehozása](setup-create-workspace.md#write-a-configuration-file)
    - Töltse le a minta parancsfájl [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - Is megtalálhatja a befejezett [Jupyter Notebook verzió](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) Ez az útmutató a GitHub-minták lapon. A notebook intelligens hiperparaméter finomhangolása, a modell üzembe helyezése és a notebook widgetek kibontott szakaszokat tartalmazza.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz által a szükséges python-csomagok betöltése, a munkaterület inicializálása, egy kísérlet létrehozásától és feltöltése a betanítási adatok és a betanítási szkriptekhez beállítja a tanítási kísérlet.

### <a name="import-packages"></a>Csomagok importálása

Először importálja a azureml.core Python kódtár ad megjelenítése a verziószámot.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine Learning szolgáltatás munkaterület](concept-workspace.md) a szolgáltatás a legfelső szintű erőforrás. Is tartalmaz egy központi helyen hoz létre minden összetevő dolgozhat. A Python SDK-ban, hozzáférhet a munkaterület-összetevők létrehozásával egy [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum.

A munkaterület-objektum létrehozása a `config.json` létrehozott fájlt a [előfeltételeknél](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Hozzon létre egy projektet könyvtárat
Hozzon létre egy könyvtárat, amely tartalmazza a szükséges kódot, hogy kell-e a távoli erőforrás elérését a helyi gépen. Ez magában foglalja a tanítási szkriptet és a további fájlokat a tanítási szkriptet függ.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Készítse elő a tanítási szkriptet

Ebben az oktatóanyagban a tanítási szkriptet **chainer_mnist.py** már az Ön számára biztosított. A gyakorlatban majd bármely egyéni tanítási szkriptet, és futtassa az Azure ML segítségével a kód módosítása nélkül kell lennie.

Az Azure Machine Learning követési és metrikák képességeit szeretné használni, fel kell vennie a tanítási szkriptet az Azure Machine Learning kódot a kisebb mennyiségű.  A tanítási szkriptet **chainer_mnist.py** bemutatja, hogyan bizonyos metrikák bejelentkezni az Azure ML futtatni. Ehhez az Azure ML eléréséhez `Run` a parancsfájl-objektummal.

Másolja ki a tanítási szkriptet **chainer_mnist.py** be a projektkönyvtárba.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet és a egy mappát a betanítási szkriptekhez tárolásához. Ebben a példában egy "chainer-mnist" nevű kísérlet létrehozása.

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Hozzon létre, vagy szerezze be egy számítási célnak

Szüksége lesz egy [számítási célt](concept-compute-target.md) a modell betanításához. Ebben az oktatóanyagban az távoli képzési számítási erőforrás által felügyelt Azure ML compute (AmlCompute) fogja használni.

**Létrehozási, AmlCompute körülbelül 5 percet vesz igénybe**. Ha az ilyen nevű AmlCompute már a munkaterületen, ez a kód kihagyja a létrehozás folyamatát.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

A számítási célokhoz további információkért lásd: a [mi egy számítási célnak](concept-compute-target.md) cikk.

## <a name="create-a-chainer-estimator"></a>Hozzon létre egy Chainer estimator

A [Chainer estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) Chainer betanítási feladatokat a számítási célnak a állapotára egyszerű módszert kínál.

A Chainer estimator biztosítják az általános [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztály, amely bármely keretrendszer támogatásához használható. Az általános estimator használó modellek betanítása kapcsolatos további információkért lásd: [modelleket taníthat be az Azure Machine Learning estimator használatával](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Küldje el a Futtatás

A [objektumot futtatni](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) a futtatási előzmények a felületet biztosít, a feladat futása közben és után befejeződött.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

A Futtatás hajtja végre, a következő szakaszokra végighalad:

- **Felkészülés**: Docker-rendszerkép a Chainer estimator alapján jön létre. A rendszerkép feltöltött a munkaterület container Registry tárolóregisztrációs adatbázisba, és későbbi futtatások gyorsítótárazza. Naplók a is rendszer streamként továbbítja a futtatási előzmények és megtekinthetők a folyamat állapotának monitorozásához.

- **Méretezés**: A fürt próbál vertikális felskálázás, ha a Batch AI-fürt csomópontjait a Futtatás végrehajtásához, mint a jelenleg elérhető igényel.

- **Futó**: A parancsfájl mappában található összes parancsfájl töltenek fel a számítási célnak, adattárak csatlakoztatva van, vagy másolja és a entry_script hajtja végre. Az stdout adatsorból kimenetek és a. / logs mappában a rendszer streamként továbbítja a futtatási előzmények és a Futtatás figyelésére használható.

- **Utófeldolgozási**: A. / kimenete a Futtatás mappába másolja a rendszer a futtatási előzményekben.

## <a name="save-and-register-the-model"></a>Mentse, és regisztrálja a modellt

Ha a modell már betanított, mentheti, és regisztrálja a munkaterülethez. Modell regisztrálását lehetővé teszi a tároló és verzió egyszerűsítése érdekében a munkaterület a modellek [kezelés és üzembe helyezési modell](concept-model-management-and-deployment.md).

Adja hozzá a következő kódot a tanítási szkriptet **chainer_mnist.py**, a modell mentéséhez. 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

Regisztrálja a modellt, a munkaterület az alábbi kódra.

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>További lépések

Ebben a cikkben Azure Machine Learning szolgáltatásban egy Chainer modell tanítása. 

* Ismerje meg, hogyan helyezhet üzembe modelleket, továbbléphet a következőre a [üzembe helyezési modell](how-to-deploy-and-where.md) cikk.

* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)

* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
