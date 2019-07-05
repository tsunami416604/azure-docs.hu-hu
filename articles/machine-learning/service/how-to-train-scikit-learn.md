---
title: Betanítása és regisztrálja a scikit-modellek további
titleSuffix: Azure Machine Learning service
description: Ez a cikk bemutatja, hogyan betanítása és regisztrálja a scikit-további modell Azure Machine Learning szolgáltatás használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: d2c9b104d1fe9333221bc20e7e23b436358c9ece
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514024"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Betanítása és ipari méretekben Scikit további modellek regisztrálása az Azure Machine Learning szolgáltatás

Ez a cikk bemutatja, hogyan betanítása és regisztrálnia kell egy Scikit további modell Azure Machine Learning szolgáltatás használatával. Használja a népszerű [Írisz adatkészletet](https://archive.ics.uci.edu/ml/datasets/iris) egyéni iris flower képek osztályozásához [scikit-további](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) osztály.

Egy nyílt forráskódú számítási keretrendszert, általában használt gépi tanulási Scikit további. Az Azure Machine Learning szolgáltatás gyors horizontális felskálázása lehetséges nyílt forráskódú betanítási feladatokat a rugalmas felhőalapú számítási erőforrások használatával. Is nyomon követheti a betanítási futtatás, a verzió modellek üzembe helyezése a modelleket, és még sok más.

Akár az alapoktól a Scikit-további modell fejleszt, vagy már meglévő modell üzembe a felhőben, Azure Machine Learning szolgáltatás segítségével éles használatra kész modelleket.

## <a name="prerequisites"></a>Előfeltételek

Ez a kód futtatása ezekben a környezetekben valamelyikét:
 - Az Azure Machine Learning Notebook Virtuálisgép - letöltések vagy nem szükséges telepítés

    - Végezze el a [felhőalapú notebook rövid](quickstart-run-cloud-notebook.md) hozhat létre egy dedikált notebook server előre betöltött az SDK-t és a mintaadattárat.
    - A notebook server minták mappájában található befejeződött, és a bővített Jegyzetfüzet az ebben a könyvtárban: **útmutatóval-to-használat – azureml > képzési > train-hyperparameter-tune-deploy-with-sklearn** mappát.

 - A saját Jupyter Notebook server

    - [Telepítse az Azure Machine Learning SDK a Pythonhoz](setup-create-workspace.md#sdk)
    - [Munkaterület-konfigurációs fájl létrehozása](setup-create-workspace.md#write-a-configuration-file)
    - [A minta parancsfájl letöltése](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn) `train_iris.py`
    - Is megtalálhatja a befejezett [Jupyter Notebook verzió](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb) Ez az útmutató a GitHub-minták oldalon. A notebook tartalmaz egy kibontott szakaszban szerepelnek a intelligens hiperparaméter finomhangolása és beolvasása a legjobb modellt elsődleges metrikák alapján.

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

Hozzon létre egy kísérletet és a egy mappát a betanítási szkriptekhez tárolásához. Ebben a példában egy "sklearn-iris" nevű kísérlet létrehozása.

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Töltse fel az adatkészlet és parancsfájlok

A [adattárolója](how-to-access-data.md) olyan hely, ahol adatok tárolásának és csatlakoztatására, vagy másolja az adatokat a számítási célnak férhetnek hozzá. Az egyes munkaterületeken alapértelmezett adattárolót biztosít. Töltse fel az adatokat és a betanítási szkriptekhez az adattárral, hogy könnyen hozzáférhetők betanítás során.

1. Az adatok a következő könyvtár létrehozásakor.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Az alapértelmezett adattárral töltse fel az Írisz adatkészletet.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Feltöltés, a Scikit-további tanítási szkriptet, `train_iris.py`.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Hozzon létre, vagy szerezze be egy számítási célnak

Hozzon létre egy számítási célnak, a Scikit-további feldolgozás futtathatók. Scikit csak támogatja egyetlen csomópont, további számítási Processzor.

A következő kód létrehoz egy felügyelt Azure Machine Learning compute (AmlCompute) a távoli képzési számítási erőforrás. Létrehozás a AmlCompute körülbelül 5 percet vesz igénybe. Ha az ilyen nevű AmlCompute már a munkaterületen, ez a kód kihagyja a létrehozás folyamatát.

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

A számítási célokhoz további információkért lásd: a [mi egy számítási célnak](concept-compute-target.md) cikk.

## <a name="create-a-scikit-learn-estimator"></a>Hozzon létre egy Scikit további estimator

A [Scikit további estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) állapotára, a Scikit-további egyszerű módszert kínál a betanítási feladat a számítási célt. Keresztül valósul a [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) osztály, amely egycsomópontos CPU képzési támogatásához használható.

Ha a tanítási szkriptet van szüksége, további pip vagy conda-csomagok futtatása, a nevük keresztül adja át az eredményül kapott docker-rendszerképet a telepített csomagokat használhat a `pip_packages` és `conda_packages` argumentumokat.

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

## <a name="submit-a-run"></a>Küldje el a Futtatás

A [objektumot futtatni](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) a futtatási előzmények a felületet biztosít, a feladat futása közben és után befejeződött.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

A Futtatás hajtja végre, a következő szakaszokra végighalad:

- **Felkészülés**: Docker-rendszerkép a TensorFlow estimator alapján jön létre. A rendszerkép feltöltött a munkaterület container Registry tárolóregisztrációs adatbázisba, és későbbi futtatások gyorsítótárazza. Naplók a is rendszer streamként továbbítja a futtatási előzmények és megtekinthetők a folyamat állapotának monitorozásához.

- **Méretezés**: A fürt próbál vertikális felskálázás, ha a Batch AI-fürt csomópontjait a Futtatás végrehajtásához, mint a jelenleg elérhető igényel.

- **Futó**: A parancsfájl mappában található összes parancsfájl töltenek fel a számítási célnak, adattárak csatlakoztatva van, vagy másolja és a entry_script hajtja végre. Az stdout adatsorból kimenetek és a. / logs mappában a rendszer streamként továbbítja a futtatási előzmények és a Futtatás figyelésére használható.

- **Utófeldolgozási**: A. / kimenete a Futtatás mappába másolja a rendszer a futtatási előzményekben.

## <a name="save-and-register-the-model"></a>Mentse, és regisztrálja a modellt

Ha a modell már betanított, mentheti, és regisztrálja a munkaterülethez. Modell regisztrálását lehetővé teszi a tároló és verzió egyszerűsítése érdekében a munkaterület a modellek [kezelés és üzembe helyezési modell](concept-model-management-and-deployment.md).

Adja hozzá a következő kódot a tanítási szkriptet, train_iris.py menteni a modell. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Regisztrálja a modellt, a munkaterület az alábbi kódra.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben betanított és a egy Scikit további modell Azure Machine Learning szolgáltatásban regisztrált.

* Ismerje meg, hogyan helyezhet üzembe modelleket, továbbléphet a következőre a [üzembe helyezési modell](how-to-deploy-and-where.md) cikk.

* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)

* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)