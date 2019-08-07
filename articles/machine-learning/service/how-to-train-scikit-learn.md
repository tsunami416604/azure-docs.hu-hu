---
title: Gépi tanulási modellek betanítása a scikit-Learn
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan futtathat scikit – tanuljon nagyvállalati szinten Azure Machine Learning SKlearn kalkulátor-osztály használatával. A példaként szolgáló szkriptek írisz virág-képeket osztályoznak a gépi tanulási modellek scikit-Learn Iris-adatkészleten alapuló létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 98c04c50bc4a52e9b2e4e267895fdd94888885f5
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775164"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Scikit-modellek készítése Azure Machine Learning szolgáltatással méretezéssel

Ebből a cikkből megtudhatja, hogyan futtathatja a scikit – tanulja meg nagyvállalati szinten a Azure Machine Learning [SKlearn kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) -osztályának használatával. 

Az ebben a cikkben ismertetett parancsfájlok az írisz virág-rendszerképek besorolására szolgálnak a gépi tanulási modellek scikit-Learn [Iris](https://archive.ics.uci.edu/ml/datasets/iris)-adatkészleten alapuló létrehozásához.

Legyen szó akár a gépi tanulás scikit, akár a modellről, akár egy meglévő modellt hoz létre a felhőben, a Azure Machine Learning segítségével rugalmas Felhőbeli számítási erőforrásokkal bővítheti a nyílt forráskódú képzési feladatokat. A Azure Machine Learning használatával előkészítheti, üzembe helyezheti, telepítheti és figyelheti a termelési szintű modelleket.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:
 - Azure Machine Learning notebook VM – nincs szükség letöltésre vagy telepítésre

    - Fejezze be [az oktatóanyagot: Az SDK-val](tutorial-1st-experiment-sdk-setup.md) és a minta adattárral előre betöltött dedikált jegyzetfüzet-kiszolgáló létrehozásához beállíthatja a környezetet és a munkaterületet.
    - A notebook-kiszolgálón a Samples Training mappában keresse meg a kitöltött és kibontott jegyzetfüzetet, ehhez a következő könyvtárra navigálva: **How-to-use-azureml > training > Train-hiperparaméter-Tune-Deploy-sklearn** mappa.

 - Saját Jupyter Notebook-kiszolgáló

    - [A Pythonhoz készült Azure Machine Learning SDK telepítése](setup-create-workspace.md#sdk)
    - [Munkaterület-konfigurációs fájl létrehozása](setup-create-workspace.md#write-a-configuration-file)
    - Az adatkészlet és a minta parancsfájl letöltése 
        - [Írisz-adatkészlet](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Az útmutató egy befejezett [Jupyter notebook verzióját](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) is megtalálhatja a GitHub-minták lapon. A jegyzetfüzet tartalmaz egy kibővített szakaszt, amely az intelligens hiperparaméter hangolását és a legjobb modell elsődleges metrikák általi beolvasását ismerteti.

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

### <a name="create-a-machine-learning-experiment"></a>Machine learning-kísérlet létrehozása

Hozzon létre egy kísérletet és egy mappát a betanítási szkriptek tárolásához. Ebben a példában hozzon létre egy "sklearn-Iris" nevű kísérletet.

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Adatkészlet és parancsfájlok feltöltése

Az [adattár](how-to-access-data.md) olyan hely, ahol az adatok tárolása és elérése az adatoknak a számítási célra való csatlakoztatásával vagy másolásával történik. Mindegyik munkaterület alapértelmezett adattárt biztosít. Töltse fel az adatok és a betanítási szkripteket az adattárba, hogy azok könnyen elérhetők legyenek a betanítás során.

1. Hozza létre az adataihoz tartozó könyvtárat.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Töltse fel az írisz-adatkészletet az alapértelmezett adattárba.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Töltse fel a scikit-Learn tanítási `train_iris.py`szkriptet.

    ```Python
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

A [scikit-Learn kalkulátor](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) egyszerű módszert kínál a scikit képzési feladatok számítási célra való elindítására. Ez az [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) osztályon keresztül valósul meg, amely az egycsomópontos CPU-képzések támogatásához használható.

Ha a képzési szkriptnek további pip-vagy Conda-csomagokat kell futtatnia, akkor az eredményül kapott Docker-rendszerképre is telepítheti a csomagokat `pip_packages` , `conda_packages` ha a és az argumentumokat át szeretné adni a nevüknek.

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

- **Felkészülés**: A Docker-rendszerkép létrehozása a TensorFlow-kalkulátor alapján történik. A rendszer feltölti a rendszerképet a munkaterület tároló-Hivatalához, és a gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók a futtatási előzményekre is továbbítva lesznek, és a folyamat figyelésére is megtekinthetők.

- **Méretezés**: A fürt akkor kísérli meg a skálázást, ha a Batch AI-fürthöz több csomópont szükséges a jelenleg elérhető futtatáshoz.

- **Futtatás**: A rendszer a parancsfájl mappájában lévő összes parancsfájlt feltölti a számítási célra, az adattárakat csatlakoztatja vagy másolja, és a entry_script hajtja végre. Az stdout és a./Logs mappa kimeneteit a rendszer a futtatási előzményekre továbbítja, és a Futtatás figyelésére használható.

- **Feldolgozás utáni**: A Futtatás./outputs mappáját a rendszer átmásolja a futtatási előzményekbe.

## <a name="save-and-register-the-model"></a>A modell mentése és regisztrálása

A modell kiképzése után mentheti és regisztrálhatja azt a munkaterületen. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében.

A modell mentéséhez adja hozzá a következő kódot a betanítási szkripthez (train_iris. a). 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Regisztrálja a modellt a munkaterületen a következő kóddal.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben a gépi tanulás és a besorolási modell betanítása és regisztrálása a scikit használatával című témakörben Azure Machine Learning szolgáltatáson keresztül.

* A modellek üzembe helyezésének megismeréséhez folytassa a [modell üzembe](how-to-deploy-and-where.md) helyezésével kapcsolatos cikket.

* [Hiperparaméterek beállítása hangolása](how-to-tune-hyperparameters.md).

* A [futtatási metrikák nyomon követése a betanítás során](how-to-track-experiments.md).

* További információ a [Deep learning és a Machine learning](concept-deep-learning-vs-machine-learning.md)szolgáltatásról.