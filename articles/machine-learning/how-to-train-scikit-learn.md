---
title: Scikit betanítása – a gépi tanulási modellek megismerése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan futtathatja a scikit-oktatóanyagokat Azure Machine Learningon.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 28401b5900640ed7228d7c7caad0cebbabf00a65
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91532720"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Scikit betanítása – a modellek Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan futtathatja a scikit-oktatóanyagokat Azure Machine Learning használatával.

Az ebben a cikkben ismertetett parancsfájlok az írisz virág-rendszerképek besorolására szolgálnak a gépi tanulási modellek scikit-Learn [Iris-adatkészleten](https://archive.ics.uci.edu/ml/datasets/iris)alapuló létrehozásához.

Legyen szó akár a gépi tanulás scikit, akár a modellről, akár egy meglévő modellt hoz létre a felhőben, a Azure Machine Learning segítségével rugalmas Felhőbeli számítási erőforrásokkal bővítheti a nyílt forráskódú képzési feladatokat. A Azure Machine Learning használatával előkészítheti, üzembe helyezheti, telepítheti és figyelheti a termelési szintű modelleket.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:
 - Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

    - Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md)  egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
    - A notebook-kiszolgáló minták betanítási mappájában keresse meg a befejezett és kibontott jegyzetfüzetet a következő könyvtárra való navigálással: **How-to-use-azureml > ml-keretrendszerek > scikit-learn > Train-hiperparaméter-Tune-Deploy-a-sklearn** mappában.

 - Saját Jupyter Notebook-kiszolgáló

    - [Telepítse az Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) -t (>= 1.13.0).
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz a betanítási kísérletet a szükséges Python-csomagok betöltésével, a munkaterület inicializálásával, a képzési környezet definiálásával és a betanítási parancsfájl előkészítésével állítja be.

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy objektum létrehozásával érheti el a munkaterület összetevőit [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) .

Hozzon létre egy munkaterület-objektumot az `config.json` [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Parancsfájlok előkészítése

Ebben az oktatóanyagban a **train_iris. a.** a betanítási szkriptet már megadták [Önnek.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py) A gyakorlatban a kód módosítása nélkül is elvégezheti az egyéni betanítási szkriptek futtatását, és futtathatja azt az Azure ML-ben.

Megjegyzések:
- A megadott betanítási szkript bemutatja, hogyan naplózhat néhány mérőszámot az Azure ML-re a `Run` szkripten belüli objektum használatával.
- A megadott betanítási parancsfájl példákat használ a  `iris = datasets.load_iris()` függvényből.  Ha saját adatait szeretné használni és elérni, tekintse meg a [hogyan kell betanítani az adatkészletekkel](how-to-train-with-datasets.md) , hogy az adatokat elérhetővé tegye a képzés során.

### <a name="define-your-environment"></a>A környezet meghatározása

Ha meg szeretné határozni, hogy melyik Azure ML- [környezet](concept-environments.md) tartalmazza a betanítási parancsfájl függőségeit, definiálhat egyéni környezetet, vagy használhatja az Azure ml-t.

#### <a name="create-a-custom-environment"></a>Egyéni környezet létrehozása

Saját egyéni környezet létrehozásához adja meg a Conda függőségeit egy YAML-fájlban; Ebben a példában a fájl neve `conda_dependencies.yml` .

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Hozzon létre egy Azure ML-környezetet ebből a Conda-környezeti specifikációból. A környezet egy Docker-tárolóba lesz csomagolva futásidőben.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

További információ a környezetek létrehozásáról és használatáról: [szoftverek környezetének létrehozása és használata Azure Machine Learningban](how-to-use-environments.md).

#### <a name="use-a-curated-environment"></a>Kurátori környezet használata
Ha nem szeretné, hogy a saját rendszerképe ne legyen létrehozva, az Azure ML-ben előre összeállított, kiépített környezetek is elérhetők. További információ: [itt](resource-curated-environments.md).
Ha egy kurátori környezetet szeretne használni, a következő parancsot futtathatja inkább:

```python
sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

## <a name="configure-and-submit-your-training-run"></a>A betanítási Futtatás konfigurálása és elküldése

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig létrehozása
Hozzon létre egy ScriptRunConfig objektumot a betanítási feladatok konfigurációs adatainak megadásához, beleértve a betanítási parancsfájlt, a használni kívánt környezetet és a futtatáshoz szükséges számítási célt.
A program a (z `arguments` ) paraméterben megadott paraméterekkel adja át a betanítási parancsfájl összes argumentumát.

A következő kód egy ScriptRunConfig-objektumot konfigurál a feladatok végrehajtásához a helyi gépen.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Ha ehelyett egy távoli fürtön szeretné futtatni a feladatot, megadhatja a kívánt számítási célt a `compute_target` ScriptRunConfig paraméteréhez.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>A Futtatás beküldése
```python
from azureml.core import Experiment

run = Experiment(ws,'train-iris').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning a teljes forrás könyvtár másolásával futtatja a betanítási parancsfájlokat. Ha olyan bizalmas adatokkal rendelkezik, amelyeket nem szeretne felvenni, használja a [. ignore fájlt](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) , vagy ne adja meg a forrás könyvtárában. Ehelyett egy Azure ML- [adathalmaz](how-to-train-with-datasets.md)használatával férhet hozzá az adataihoz.

### <a name="what-happens-during-run-execution"></a>Mi történik a Futtatás végrehajtásakor
A Futtatás végrehajtásakor a következő szakaszokon halad végig:

- **Előkészítés**: a Docker-rendszerkép a definiált környezetnek megfelelően jön létre. A rendszer feltölti a rendszerképet a munkaterület tároló-Hivatalához, és a gyorsítótárba helyezi a későbbi futtatásokhoz. A naplók a futtatási előzményekre is továbbítva lesznek, és a folyamat figyelésére is megtekinthetők. Ha helyette egy kurátori környezet van megadva, akkor a rendszer a kiválasztott gyorsítótárazott rendszerképet fogja használni.

- **Skálázás**: a fürt akkor kísérli meg a skálázást, ha a Batch AI fürthöz több csomópont szükséges a jelenleg elérhető futtatáshoz.

- **Futtatás**: a rendszer a parancsfájl mappájában lévő összes parancsfájlt feltölti a számítási célra, az adattárakat csatlakoztatja vagy másolja, és `script` végrehajtja a szolgáltatást. Az stdout és a **./logs** mappa kimeneteit a rendszer a futtatási előzményekre továbbítja, és a Futtatás figyelésére használható.

- **Utómunka**: a Futtatás **./outputs** mappáját a rendszer átmásolja a futtatási előzményekbe.

## <a name="save-and-register-the-model"></a>A modell mentése és regisztrálása

A modell kiképzése után mentheti és regisztrálhatja azt a munkaterületen. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében.

A modell mentéséhez adja hozzá a következő kódot a betanítási parancsfájlhoz, train_iris. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Regisztrálja a modellt a munkaterületen a következő kóddal. A paraméterek `model_framework` , a és a `model_framework_version` , a `resource_configuration` kód nélküli üzembe helyezés lehetőség megadásával elérhetővé válik. A kód nélküli telepítés lehetővé teszi, hogy közvetlenül a modelljét webszolgáltatásként telepítse a regisztrált modellből, és az [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py&preserve-view=true) objektum meghatározza a webszolgáltatás számítási erőforrását.

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

A most regisztrált modell ugyanúgy telepíthető, mint bármely más, az Azure ML-ben regisztrált modell. Az üzembe helyezési útmutató egy szakaszt tartalmaz a modellek regisztrálásához, de közvetlenül kihagyhatja a központi telepítéshez szükséges [számítási cél létrehozását](how-to-deploy-and-where.md#choose-a-compute-target) , mivel már rendelkezik regisztrált modellel.

### <a name="preview-no-code-model-deployment"></a>Előnézet Nem kód modell telepítése

A hagyományos üzembe helyezési útvonal helyett a scikit-Learn kód nélküli üzembe helyezési funkció (előzetes verzió) is használható. Az összes beépített scikit-modell típushoz nem használható a kód modell üzembe helyezése. Ha a fentiekben látható módon regisztrálja a modellt `model_framework` , `model_framework_version` és a `resource_configuration` paramétereket is használja, egyszerűen használhatja a [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) statikus függvényt a modell üzembe helyezéséhez.

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


## <a name="next-steps"></a>További lépések

Ebben a cikkben a scikit-Learn modell betanítását és regisztrálását, valamint az üzembe helyezési lehetőségek megismerését ismertetjük. Ezekről a cikkekről további tudnivalókat talál a Azure Machine Learningról.

* [A futtatási metrikák nyomon követése a betanítás során](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
