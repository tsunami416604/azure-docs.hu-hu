---
title: Scikit betanítása – a gépi tanulási modellek megismerése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan futtathatja a scikit-oktatóanyagokat Azure Machine Learningon.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 07/24/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ce052fa80d8aa0d30d1de7268b82e727c952cab1
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648200"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Scikit-modellek készítése Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan futtathatja a scikit-oktatóanyagokat Azure Machine Learning használatával.

Az ebben a cikkben ismertetett parancsfájlok az írisz virág-rendszerképek besorolására szolgálnak a gépi tanulási modellek scikit-Learn [Iris-adatkészleten](https://archive.ics.uci.edu/ml/datasets/iris)alapuló létrehozásához.

Legyen szó akár a gépi tanulás scikit, akár a modellről, akár egy meglévő modellt hoz létre a felhőben, a Azure Machine Learning segítségével rugalmas Felhőbeli számítási erőforrásokkal bővítheti a nyílt forráskódú képzési feladatokat. A Azure Machine Learning használatával előkészítheti, üzembe helyezheti, telepítheti és figyelheti a termelési szintű modelleket.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot ezen környezetek bármelyikén:
 - Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

    - Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md)  egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
    - A notebook-kiszolgáló minták betanítási mappájában keresse meg a befejezett és kibontott jegyzetfüzetet, ehhez a következő könyvtárra navigálva: **How-to-use-azureml > ml-keretrendszerek > scikit-learn > training > Train-hiperparaméter-Tune-Deploy-a-sklearn** mappában.

 - Saját Jupyter Notebook-kiszolgáló

    - [Telepítse a Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)-t.
    - [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz a betanítási kísérletet a szükséges Python-csomagok betöltésével, egy munkaterület inicializálásával, egy kísérlet létrehozásával, valamint a betanítási adat és a betanítási parancsfájlok feltöltésével állítja be.

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való együttműködéshez. A Python SDK-ban egy objektum létrehozásával érheti el a munkaterület összetevőit [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) .

Hozzon létre egy munkaterület-objektumot az `config.json` [Előfeltételek szakaszban](#prerequisites)létrehozott fájlból.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```


### <a name="prepare-scripts"></a>Parancsfájlok előkészítése

Ebben az oktatóanyagban a **train_iris. a.** a betanítási szkriptet már megadták [Önnek.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py) A gyakorlatban a kód módosítása nélkül is elvégezheti az egyéni betanítási szkriptek futtatását, és futtathatja azt az Azure ML-ben.

Megjegyzések:
- A megadott betanítási szkript bemutatja, hogyan naplózhat néhány mérőszámot az Azure ML-re a `Run` szkripten belüli objektum használatával.
- A megadott betanítási parancsfájl példákat használ a  `iris = datasets.load_iris()` függvényből.  A saját adatok esetében előfordulhat, hogy olyan lépéseket kell használnia, mint például az [adatkészlet és a parancsfájlok feltöltése](how-to-train-keras.md#data-upload) , hogy az adatok elérhetők legyenek a képzés során.

### <a name="define-your-environment"></a>Adja meg a környezetet.

#### <a name="create-a-custom-environment"></a>Hozzon létre egy egyéni környezetet.

A Conda-környezet (sklearn-env. YML) szerzője.
A Conda-környezet jegyzetfüzetből való írásához a sort a ```%%writefile sklearn-env.yml``` cella tetején adhatja hozzá.

```yaml
name: sklearn-training-env
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

myenv = Environment.from_conda_specification(name = "myenv", file_path = "sklearn-env.yml")
myenv.docker.enabled = True
```

#### <a name="use-a-curated-environment"></a>Kurátori környezet használata
Az Azure ML előre összeállított, kiszervezett tároló környezeteket biztosít, ha nem szeretne saját rendszerképet létrehozni. További információ: [itt](resource-curated-environments.md).
Ha egy kurátori környezetet szeretne használni, a következő parancsot futtathatja inkább:

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial")
```

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig létrehozása

Ez a ScriptRunConfig a helyi számítási célra küldi el a feladatot a végrehajtáshoz.

```python
from azureml.core import ScriptRunConfig

sklearnconfig = ScriptRunConfig(source_directory='.', script='train_iris.py')
sklearnconfig.run_config.environment = myenv
```

Ha egy távoli fürtön szeretné elküldeni a küldést, módosíthatja run_config. Target értéket a kívánt számítási célra.

### <a name="submit-your-run"></a>A Futtatás beküldése
```python
from azureml.core import Experiment

run = Experiment(ws,'train-sklearn').submit(config=sklearnconfig)
run.wait_for_completion(show_output=True)

```

> [!WARNING]
> Azure Machine Learning a teljes forrás könyvtár másolásával futtatja a betanítási parancsfájlokat. Ha olyan bizalmas adatokkal rendelkezik, amelyeket nem szeretne felvenni, használja a [. ignore fájlt](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) , vagy ne adja meg a forrás könyvtárában. Ehelyett egy [adattár](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)használatával férhet hozzá az adataihoz.

A Python-környezet testreszabásával kapcsolatos további információkért lásd: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md). 

## <a name="what-happens-during-run-execution"></a>Mi történik a Futtatás végrehajtásakor
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

A korábban regisztrált modell ugyanúgy helyezhető üzembe, mint bármely más regisztrált modell Azure Machine Learningban, függetlenül attól, hogy milyen kalkulátort használt a betanításhoz. Az üzembe helyezési útmutató egy szakaszt tartalmaz a modellek regisztrálásához, de közvetlenül kihagyhatja a központi telepítéshez szükséges [számítási cél létrehozását](how-to-deploy-and-where.md#choose-a-compute-target) , mivel már rendelkezik regisztrált modellel.

### <a name="preview-no-code-model-deployment"></a>Előnézet Nem kód modell telepítése

A hagyományos üzembe helyezési útvonal helyett a scikit-Learn kód nélküli üzembe helyezési funkció (előzetes verzió) is használható. Az összes beépített scikit-modell típushoz nem használható a kód modell üzembe helyezése. Ha a fentiekben látható módon regisztrálja a modellt `model_framework` , `model_framework_version` és a `resource_configuration` paramétereket is használja, egyszerűen használhatja a [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) statikus függvényt a modell üzembe helyezéséhez.

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
