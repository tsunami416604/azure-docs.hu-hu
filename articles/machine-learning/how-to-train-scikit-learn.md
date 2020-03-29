---
title: Scikit-learn gépi tanulási modellek betanítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan futtathatja scikit-learn képzési parancsfájljait nagyvállalati méretekben az Azure Machine Learning SKlearn estimator osztály használatával. A példaszkriptek osztályozzák az íriszvirág-képeket, hogy a scikit-learn írisz-adatkészlete alapján gépi tanulási modellt hozzanak létre.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: bdd2cc400c3df75742689258caea8cb87ee8ccc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942263"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Scikit-learn modelleket hozhat létre nagy méretekben az Azure Machine Learning segítségével
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan futtathatja a scikit-learn képzési parancsfájlok nagyvállalati méretekben az Azure Machine Learning [SKlearn estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) osztály használatával. 

A cikkben szereplő példaparancsfájlok az íriszvirág-képek osztályozására szolgálnak, hogy a scikit-learn [írisz-adatkészletén](https://archive.ics.uci.edu/ml/datasets/iris)alapuló gépi tanulási modellt hozzanak létre.

Akár egy gépi tanulási scikit-learn modellt próbál beképezni az alapoktól kezdve, akár egy meglévő modellt hoz a felhőbe, az Azure Machine Learning segítségével kioszthatja a nyílt forráskódú betanítási feladatokat rugalmas felhőalapú számítási erőforrások használatával. Az Azure Machine Learning használatával éles szintű modelleket hozhat létre, helyezhet üzembe, és figyelhet.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot az alábbi környezetek valamelyikén:
 - Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

    - Töltse ki az [oktatóanyag: Telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) hozzon létre egy dedikált notebook-kiszolgáló előre betöltött az SDK és a mintatár.
    - A minták képzési mappában a notebook-kiszolgálón, keressen egy kitöltött és kibontott jegyzetfüzetet a címtárba navigálva: **hogyan lehet használni-azureml > ml-keretrendszerek, > scikit-learn > betanítási > a vonat-hiperparaméter-tune-deploy-with-sklearn** mappát.

 - Saját Jupyter Notebook szerver

    - [Telepítse az Azure Machine Learning SDK-t.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
    - [Munkaterület-konfigurációs fájl létrehozása](how-to-configure-environment.md#workspace).
    - Az adatkészlet és a mintaparancsfájl letöltése 
        - [írisz adatkészlet](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Az útmutató elkészült [Jupyter Notebook-verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) is megtalálhatja a GitHub minták lapján. A jegyzetfüzet tartalmaz egy kibővített szakaszt, amely intelligens hiperparaméter-hangolási és a legjobb modell elsődleges metrikák által történő lekérése.

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz úgy állítja be a betanítási kísérletet, hogy betölti a szükséges python-csomagokat, inicializálja a munkaterületet, létrehoz egy kísérletet, és feltölti a betanítási adatokat és a betanítási parancsfájlokat.

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

Az [Azure Machine Learning-munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Ez biztosítja az Ön számára egy központosított hely, ahol az összes létrehozott műtermék. A Python SDK-ban egy [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum létrehozásával érheti el a munkaterületi összetevőket.

Hozzon létre egy `config.json` munkaterületi objektumot az [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Machine Learning-kísérlet létrehozása

Hozzon létre egy kísérletet és egy mappát a betanítási parancsfájlok tárolására. Ebben a példában hozzon létre egy "sklearn-iris" nevű kísérletet.

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Betanítási parancsfájl előkészítése

Ebben az oktatóanyagban a **train_iris.py** betanítási parancsfájl már biztosított. A gyakorlatban képesnek kell lennie arra, hogy bármilyen egyéni betanítási parancsfájlt, ahogy van, és futtassa az Azure ML-vel anélkül, hogy módosítania kell a kódot.

Az Azure ML-követés és metrikák képességeinek használatához adjon hozzá egy kis mennyiségű Azure ML-kódot a betanítási parancsfájlban.  A **train_iris.py** betanítási parancsfájl bemutatja, hogyan naplózhat bizonyos metrikákat az Azure ML-futás a `Run` parancsfájlon belüli objektum használatával.

A megadott betanítási parancsfájl `iris = datasets.load_iris()` a függvény ből származó példaadatokat használ.  A saját adatok, előfordulhat, hogy olyan lépéseket kell [használnia, mint az adatkészlet feltöltése és a parancsfájlok,](how-to-train-keras.md#data-upload) hogy az adatok elérhetővé a betanítás során.

Másolja a **train_iris.py** betanítási parancsfájlt a projektkönyvtárába.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Számítási cél létrehozása vagy bekésezése

Hozzon létre egy számítási célt a scikit-learn feladat futtatásához. Scikit-learn csak támogatja az egyetlen csomópont, CPU-számítástechnika.

A következő kód létrehoz egy Azure Machine Learning felügyelt számítási (AmlCompute) a távoli betanítási számítási erőforrás. Az AmlCompute létrehozása körülbelül 5 percet vesz igénybe. Ha az AmlCompute ezzel a névvel már a munkaterületen, ez a kód kihagyja a létrehozási folyamatot.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

A számítási célokról további információt a [számítási célcikkben](concept-compute-target.md) talál.

## <a name="create-a-scikit-learn-estimator"></a>Scikit-learn becslő létrehozása

A [scikit-learn estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) egy egyszerű módja annak, hogy indítson egy scikit-learn képzési munkát a számítási cél. Ez az [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) osztályon keresztül valósul meg, amely egycsomópontos CPU-betanításának támogatására használható.

Ha a betanítási parancsfájl további pip- vagy conda-csomagokfuttatásához szükséges, a csomagok at az `pip_packages` `conda_packages` eredményül kapott docker-rendszerképre telepítheti, ha átadja a nevüket a és az argumentumokat.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```


A Python-környezet testreszabásáról további információt a [Környezetek létrehozása és kezelése betanításhoz és üzembe helyezéshez című témakörben talál.](how-to-use-environments.md) 

## <a name="submit-a-run"></a>Futtatás beküldése

A [Futtatás objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biztosítja a kapcsolatot a futtatási előzményekhez a feladat futása közben és befejezése után.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

A futtatás végrehajtásakor a következő szakaszokon megy keresztül:

- **Előkészítés:** A Docker-rendszerkép a TensorFlow-becslés nek megfelelően jön létre. A rendszerképet a rendszer feltölti a munkaterület tárolójának rendszerleíró adatbázisába, és gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók is streamelt a futtatási előzmények, és megtekinthető konkretizált a folyamat figyelése érdekében.

- **Méretezés**: A fürt megkísérli a felskálázást, ha a Batch AI-fürtnek a jelenleg rendelkezésre állónál több csomópontra van szüksége a futtatás végrehajtásához.

- **Futtatás:** A parancsfájlmappában lévő összes parancsfájl feltöltésre kerül a számítási célba, az adattárak csatlakoztatva vagy másolva, és a entry_script végrehajtása. Az stdout és a ./logs mappa kimenetei a futtatási előzményekbe kerülnek, és a futtatás figyelésére használhatók.

- **Utófeldolgozás:** A futtatás ./outputok mappája átkerül a futtatási előzményekbe.

## <a name="save-and-register-the-model"></a>A modell mentése és regisztrálása

Miután betanította a modellt, mentheti és regisztrálhatja azt a munkaterületre. A modellregisztráció lehetővé teszi a modellek tárolását és verziózását a munkaterületen a [modellkezelés és -telepítés egyszerűsítése érdekében.](concept-model-management-and-deployment.md)

Adja hozzá a következő kódot a betanítási parancsfájlhoz , train_iris.py, a modell mentéséhez. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Regisztrálja a modellt a munkaterületre a következő kóddal. A paraméterek megadásával `model_framework` `model_framework_version`, `resource_configuration`és a , no-code modell központi telepítés elérhetővé válik. Ez lehetővé teszi, hogy közvetlenül üzembe helyezze a [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) modellt webszolgáltatásként a regisztrált modellből, és az objektum határozza meg a webszolgáltatás számítási erőforrását.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Környezet

Az imént regisztrált modell ugyanúgy telepíthető, mint bármely más regisztrált modell az Azure Machine Learningben, függetlenül attól, hogy melyik betanítási becslést használta. Az üzembe helyezési útmutató tartalmaz egy szakaszt a modellek regisztrálása, de közvetlenül átugorhatja [a számítási cél](how-to-deploy-and-where.md#choose-a-compute-target) létrehozása a központi telepítéshez, mivel már rendelkezik egy regisztrált modell.

### <a name="preview-no-code-model-deployment"></a>(Előzetes verzió) Kód nélküli modell központi telepítése

Ahelyett, hogy a hagyományos központi telepítési útvonal, használhatja a kód nélküli központi telepítési szolgáltatás (előzetes)scikit-learn. No-code modell központi telepítés minden beépített scikit-learn modelltípusok támogatott. A modell regisztrálásával a fent `model_framework` `model_framework_version`látható `resource_configuration` a , és a [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) paramétereket, egyszerűen használja a statikus függvényt a modell üzembe helyezéséhez.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

MEGJEGYZÉS: Ezek a függőségek az előre elkészített scikit-learn következtetéstárolóban találhatók.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

A teljes [útmutató](how-to-deploy-and-where.md) részletesebben ismerteti az Azure Machine Learning üzembe helyezését.


## <a name="next-steps"></a>További lépések

Ebben a cikkben betanított és regisztrált egy scikit-learn modellt, és megismerkedett a telepítési lehetőségekkel. Tekintse meg ezeket az egyéb cikkeket, ha többet szeretne megtudni az Azure Machine Learningről.

* [Futási mérőszámok nyomon követése edzés közben](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Referenciaarchitektúra az elosztott mélytanulási képzéshez az Azure-ban](/azure/architecture/reference-architectures/ai/training-deep-learning)
