---
title: Scikit betanítása – a gépi tanulási modellek megismerése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan futtathat scikit – tanuljon nagyvállalati szinten Azure Machine Learning SKlearn kalkulátor-osztály használatával. A példaként szolgáló szkriptek írisz virág-képeket osztályoznak a gépi tanulási modellek scikit-Learn Iris-adatkészleten alapuló létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: ea466486509c4b5dadc48ef830c9f05ec42ab5b3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814860"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Scikit-modellek készítése Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan futtathatja a scikit – tanulja meg nagyvállalati szinten a Azure Machine Learning [SKlearn kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) -osztályának használatával. 

Az ebben a cikkben ismertetett parancsfájlok az írisz virág-rendszerképek besorolására szolgálnak a gépi tanulási modellek scikit-Learn [Iris-adatkészleten](https://archive.ics.uci.edu/ml/datasets/iris)alapuló létrehozásához.

Legyen szó akár a gépi tanulás scikit, akár a modellről, akár egy meglévő modellt hoz létre a felhőben, a Azure Machine Learning segítségével rugalmas Felhőbeli számítási erőforrásokkal bővítheti a nyílt forráskódú képzési feladatokat. A Azure Machine Learning használatával előkészítheti, üzembe helyezheti, telepítheti és figyelheti a termelési szintű modelleket.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:
 - Azure Machine Learning notebook VM – nincs szükség letöltésre vagy telepítésre

    - Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
    - A notebook-kiszolgáló minták betanítási mappájában keresse meg a befejezett és kibontott jegyzetfüzetet a következő könyvtárra való navigálással: **How-to-use-azureml > ml-keretrendszerek > scikit-learn > training > Train-hiperparaméter-Tune-Deploy-with-sklearn** mappa.

 - Saját Jupyter Notebook-kiszolgáló

    - [Telepítse a Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)-t.
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).
    - Az adatkészlet és a minta parancsfájl letöltése 
        - [Írisz-adatkészlet](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn)
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

A [Azure Machine learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektum létrehozásával érheti el a munkaterület összetevőit.

Hozzon létre egy munkaterület-objektumot az [Előfeltételek szakaszban](#prerequisites)létrehozott `config.json` fájlból.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Machine learning-kísérlet létrehozása

Hozzon létre egy kísérletet és egy mappát a betanítási szkriptek tárolásához. Ebben a példában hozzon létre egy "sklearn-Iris" nevű kísérletet.

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Betanítási szkript előkészítése

Ebben az oktatóanyagban már meg van biztosítva a **train_iris. a.** a betanítási szkript. A gyakorlatban a kód módosítása nélkül is elvégezheti az egyéni betanítási szkriptek futtatását, és futtathatja azt az Azure ML-ben.

Az Azure ML követési és metrikái képességeinek használatához vegyen fel egy kis mennyiségű Azure ML-kódot a betanítási szkriptbe.  A **train_iris.** a betanítási szkript azt mutatja be, hogyan naplózhat néhány mérőszámot az Azure ml-re a szkripten belüli `Run` objektum használatával.

A megadott betanítási parancsfájl a `iris = datasets.load_iris()` függvényből származó példákat használ.  A saját adatok esetében előfordulhat, hogy olyan lépéseket kell használnia, mint például az [adatkészlet és a parancsfájlok feltöltése](how-to-train-keras.md#data-upload) , hogy az adatok elérhetők legyenek a képzés során.

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

A [scikit-Learn kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) egyszerű módszert kínál a scikit képzési feladatok számítási célra való elindítására. A szolgáltatás a [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) osztályon keresztül valósítható meg, amely az egycsomópontos CPU-képzések támogatásához használható.

Ha a betanítási szkript további pip-vagy Conda-csomagokat igényel, akkor az eredményül kapott Docker-rendszerképre is telepíthetők, ha a `pip_packages` és `conda_packages` argumentumokkal átadják a nevüket.

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

Regisztrálja a modellt a munkaterületen a következő kóddal.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>További lépések


Ebben a cikkben egy kerasz-modellt képzett és regisztrált Azure Machine Learningon. A modellek üzembe helyezésének megismeréséhez folytassa a modell üzembe helyezésével kapcsolatos cikket.

> [!div class="nextstepaction"]
> [Modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)
* [A futtatási metrikák nyomon követése a betanítás során](how-to-track-experiments.md)
* [Hiperparaméterek beállítása hangolása](how-to-tune-hyperparameters.md)
* [Betanított modell üzembe helyezése](how-to-deploy-and-where.md)
* [Az Azure-ban elosztott Deep learning-képzés hivatkozási architektúrája](/azure/architecture/reference-architectures/ai/training-deep-learning)
