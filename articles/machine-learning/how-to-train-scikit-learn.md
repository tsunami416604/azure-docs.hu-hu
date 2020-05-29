---
title: Scikit betanítása – a gépi tanulási modellek megismerése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan futtathatja a scikit – Ismerje meg nagyvállalati szinten a Azure Machine Learning SKlearn kalkulátor osztály használatával. A példaként szolgáló szkriptek írisz virág-képeket osztályoznak a gépi tanulási modellek scikit-Learn Iris-adatkészleten alapuló létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: c842e0ea126ffe4c3328c4fd237e2891d6cff229
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84168794"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Scikit-modellek készítése Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan futtathatja a scikit – Ismerje meg nagyvállalati szinten a Azure Machine Learning [SKlearn kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) osztály használatával. 

Az ebben a cikkben ismertetett parancsfájlok az írisz virág-rendszerképek besorolására szolgálnak a gépi tanulási modellek scikit-Learn [Iris-adatkészleten](https://archive.ics.uci.edu/ml/datasets/iris)alapuló létrehozásához.

Legyen szó akár a gépi tanulás scikit, akár a modellről, akár egy meglévő modellt hoz létre a felhőben, a Azure Machine Learning segítségével rugalmas Felhőbeli számítási erőforrásokkal bővítheti a nyílt forráskódú képzési feladatokat. A Azure Machine Learning használatával előkészítheti, üzembe helyezheti, telepítheti és figyelheti a termelési szintű modelleket.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:
 - Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

    - Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
    - A notebook-kiszolgáló minták betanítási mappájában keresse meg a befejezett és kibontott jegyzetfüzetet, ehhez a következő könyvtárra navigálva: **How-to-use-azureml > ml-keretrendszerek > scikit-learn > training > Train-hiperparaméter-Tune-Deploy-a-sklearn** mappában.

 - Saját Jupyter Notebook-kiszolgáló

    - [Telepítse a Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)-t.
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    - Az adatkészlet és a minta parancsfájl letöltése 
        - [Írisz-adatkészlet](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Az útmutató egy befejezett [Jupyter notebook verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) is megtalálhatja a GitHub-minták lapon. A jegyzetfüzet tartalmaz egy kibővített szakaszt, amely az intelligens hiperparaméter hangolását és a legjobb modell elsődleges metrikák általi beolvasását ismerteti.

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

A [Azure Machine learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy objektum létrehozásával érheti el a munkaterület összetevőit [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Hozzon létre egy munkaterület-objektumot az `config.json` [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Machine Learning-kísérlet létrehozása

Hozzon létre egy kísérletet és egy mappát a betanítási szkriptek tárolásához. Ebben a példában hozzon létre egy "sklearn-Iris" nevű kísérletet.

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

experiment = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Betanítási szkript előkészítése

Ebben az oktatóanyagban már meg van biztosítva a **train_iris. a.** a betanítási szkript. A gyakorlatban a kód módosítása nélkül is elvégezheti az egyéni betanítási szkriptek futtatását, és futtathatja azt az Azure ML-ben.

Az Azure ML követési és metrikái képességeinek használatához vegyen fel egy kis mennyiségű Azure ML-kódot a betanítási szkriptbe.  A **train_iris.** a betanítási szkript azt mutatja be, hogyan lehet naplózni néhány mérőszámot az Azure ml-re a `Run` szkripten belüli objektum használatával.

A megadott betanítási parancsfájl példákat használ a `iris = datasets.load_iris()` függvényből.  A saját adatok esetében előfordulhat, hogy olyan lépéseket kell használnia, mint például az [adatkészlet és a parancsfájlok feltöltése](how-to-train-keras.md#data-upload) , hogy az adatok elérhetők legyenek a képzés során.

Másolja a **train_iris.** Copy betanítási szkriptet a projekt könyvtárába.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Számítási cél létrehozása vagy beszerzése

Hozzon létre egy számítási célt a scikit-Learn-feladatok futtatásához. Scikit – a csak egyetlen csomópontot támogat, a CPU-számítástechnikaot.

A következő kód létrehoz egy Azure Machine Learning felügyelt számítást (AmlCompute) a távoli képzési számítási erőforráshoz. A AmlCompute létrehozása körülbelül 5 percet vesz igénybe. Ha az adott névvel rendelkező AmlCompute már szerepel a munkaterületen, akkor ez a kód kihagyja a létrehozási folyamatot.

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

A számítási célokkal kapcsolatos további információkért tekintse meg a [Mi az a számítási cél](concept-compute-target.md) című cikket.

## <a name="create-a-scikit-learn-estimator"></a>Scikit létrehozása – Learn kalkulátor

A [scikit-Learn kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) egyszerű módszert kínál a scikit képzési feladatok számítási célra való elindítására. Ez az osztályon keresztül valósul meg [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) , amely az egycsomópontos CPU-képzések támogatásához használható.

Ha a képzési szkriptnek további pip-vagy Conda-csomagokat kell futtatnia, akkor az eredményül kapott Docker-rendszerképre is telepítheti a csomagokat, ha a és az argumentumokat át szeretné adni a nevüknek `pip_packages` `conda_packages` .

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py',
                    pip_packages=['joblib']
                   )
```


A Python-környezet testreszabásával kapcsolatos további információkért lásd: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Futtatás küldése

A [Run objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biztosítja a felületet a futtatási előzményekhez, miközben a feladatot futtatja, és a művelet befejeződött.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

A Futtatás végrehajtásakor a következő szakaszokon halad végig:

- **Előkészítés**: a Docker-rendszerkép létrehozása a TensorFlow-kalkulátor alapján történik. A rendszer feltölti a rendszerképet a munkaterület tároló-Hivatalához, és a gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók a futtatási előzményekre is továbbítva lesznek, és a folyamat figyelésére is megtekinthetők.

- **Skálázás**: a fürt akkor kísérli meg a skálázást, ha a Batch AI fürthöz több csomópont szükséges a jelenleg elérhető futtatáshoz.

- **Futtatás**: a rendszer a parancsfájl mappájában lévő összes parancsfájlt feltölti a számítási célra, az adattárakat csatlakoztatja vagy másolja, és a entry_script végre lesz hajtva. Az stdout és a./Logs mappa kimeneteit a rendszer a futtatási előzményekre továbbítja, és a Futtatás figyelésére használható.

- **Utómunka**: a Futtatás./outputs mappáját a rendszer átmásolja a futtatási előzményekbe.

## <a name="save-and-register-the-model"></a>A modell mentése és regisztrálása

A modell kiképzése után mentheti és regisztrálhatja azt a munkaterületen. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében.

A modell mentéséhez adja hozzá a következő kódot a betanítási parancsfájlhoz, train_iris. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Regisztrálja a modellt a munkaterületen a következő kóddal. A paraméterek `model_framework` , a és a `model_framework_version` , a `resource_configuration` kód nélküli üzembe helyezés lehetőség megadásával elérhetővé válik. Ez lehetővé teszi a modell közvetlen üzembe helyezését webszolgáltatásként a regisztrált modellből, és az [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) objektum meghatározza a webszolgáltatás számítási erőforrását.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Üzembe helyezés

A korábban regisztrált modell ugyanúgy helyezhető üzembe, mint bármely más regisztrált modell Azure Machine Learningban, függetlenül attól, hogy milyen kalkulátort használt a betanításhoz. Az üzembe helyezési útmutató egy szakaszt tartalmaz a modellek regisztrálásához, de közvetlenül kihagyhatja a központi telepítéshez szükséges [számítási cél létrehozását](how-to-deploy-and-where.md#choose-a-compute-target) , mivel már rendelkezik regisztrált modellel.

### <a name="preview-no-code-model-deployment"></a>Előnézet Nem kód modell telepítése

A hagyományos üzembe helyezési útvonal helyett a scikit-Learn kód nélküli üzembe helyezési funkció (előzetes verzió) is használható. Az összes beépített scikit-modell típushoz nem használható a kód modell üzembe helyezése. Ha a fentiekben látható módon regisztrálja a modellt `model_framework` , `model_framework_version` és a `resource_configuration` paramétereket is használja, egyszerűen használhatja a [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) statikus függvényt a modell üzembe helyezéséhez.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Megjegyzés: ezeket a függőségeket az előre elkészített scikit-Learn következtetési tároló tartalmazza.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

A teljes körű [útmutató](how-to-deploy-and-where.md) a Azure Machine learning nagyobb részletességgel történő üzembe helyezését ismerteti.


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a scikit-Learn modell betanítását és regisztrálását, valamint az üzembe helyezési lehetőségek megismerését ismertetjük. Ezekről a cikkekről további tudnivalókat talál a Azure Machine Learningról.

* [A futtatási metrikák nyomon követése a betanítás során](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Az Azure-ban elosztott Deep learning-képzés hivatkozási architektúrája](/azure/architecture/reference-architectures/ai/training-deep-learning)
