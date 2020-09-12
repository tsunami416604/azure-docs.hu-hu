---
title: Képzés elküldése egy számítási célra
titleSuffix: Azure Machine Learning
description: A gépi tanulási modell tanítása különböző képzési környezetekben (számítási célok). Könnyedén válthat a képzési környezetek között. A képzés helyi elindítása. Ha vertikális felskálázásra van szüksége, váltson át egy felhőalapú számítási célra.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: da48b593b8f645566b2f9775fabc5d8e62e625b6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661577"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>Képzés elküldése egy számítási célra

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan használhatja a különböző képzési környezeteket ([számítási célok](concept-compute-target.md)) a gépi tanulási modell betanításához.

A betanítás során gyakori, hogy a helyi számítógépen indul el, és később a betanítási szkriptet más számítási célra futtatja. A Azure Machine Learning használatával különböző számítási célokból futtathat parancsfájlokat anélkül, hogy módosítani kellene a betanítási szkriptet.

Mindössze annyit kell tennie, hogy a **parancsfájl futtatási konfigurációján**belül minden számítási cél esetében meghatározza a környezetet.  Ha ezt követően egy másik számítási célra szeretné futtatni a betanítási kísérletet, adja meg az adott számítás futtatási konfigurációját.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes fiókot a virtuális gép létrehozásának megkezdése előtt. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma
* A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md), `ws`
* Egy számítási cél `my_compute_target` .  Számítási cél létrehozása a rel:
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Mi az a parancsfájl-futtatási konfiguráció?

A betanítási kísérletet egy [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) -objektummal küldi el.  Ez az objektum az alábbiakat tartalmazza:

* **source_directory**: a betanítási parancsfájlt tartalmazó forrás könyvtára
* **parancsfájl**: a betanítási parancsfájl azonosítása
* **run_config**: a [futtatási konfiguráció](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true), amely meghatározza, hogy hol történjen a képzés. A alkalmazásban `run_config` meg kell adnia a számítási célt és azt a környezetet, amelyet a betanítási parancsfájl futtatásakor kíván használni.  

## <a name="whats-an-environment"></a>Mi az a környezet?

Azure Machine Learning [környezetek](concept-environments.md) a gépi tanulási képzést végző környezet beágyazását jelentik. Megadják a Python-csomagokat, a környezeti változókat és a szoftver beállításait a képzés és a pontozási szkriptek köré. A futtatási időpontokat (Python, Spark vagy Docker) is megadják.  

A környezetek az  `run_config` a objektumon belül vannak megadva `ScriptRunConfig` .

## <a name="train-your-model"></a><a id="submit"></a>A modell betanítása

A betanítási futtatást elküldő kód mintája megegyezik a számítási célok összes típusával:

1. Kísérlet létrehozása a futtatáshoz
1. Hozzon létre egy környezetet, amelyben a parancsfájl futni fog
1. Hozzon létre egy parancsfájl-futtatási konfigurációt, amely a számítási célra és a környezetre hivatkozik
1. A Futtatás elküldése
1. Várjon, amíg a Futtatás befejeződik

Vagy a következőket teheti:

* A kísérletet egy olyan objektummal küldje el, amely `Estimator` az [ml modellek becslések-vel való betanítását](how-to-train-ml-models.md)mutatja.
* HyperDrive-Futtatás küldése a [hiperparaméter finomhangolásához](how-to-tune-hyperparameters.md).
* Kísérlet küldése a [vs Code bővítmény](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)használatával.

## <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet a munkaterületen.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>Környezet létrehozása

A kurátori környezetek Python-csomagok gyűjteményeit tartalmazzák, és alapértelmezés szerint a munkaterületen érhetők el. Ezeket a környezeteket a gyorsítótárazott Docker-rendszerképek alkotják, ami csökkenti a Futtatás előkészítési költségeit. Távoli számítási cél esetén a következő népszerű kurátori környezetek egyikét használhatja a kezdéshez:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

A környezetekkel kapcsolatos további információkért és részletekért lásd: [Create & Azure Machine learning-környezetek használata](how-to-use-environments.md).
  
### <a name="local-compute-target"></a>Helyi számítási cél

Ha a számítási cél a **helyi gép**, Ön felelős annak biztosításáért, hogy az összes szükséges csomag elérhető legyen a Python-környezetben, ahol a szkript fut.  A használatával az `python.user_managed_dependencies` aktuális Python-környezetet (vagy a Pythont a megadott elérési úton) használhatja.

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>Parancsfájl futtatási konfigurációjának létrehozása

Most, hogy van egy számítási cél ( `compute_target` ) és egy környezet ( `my_environment` ), hozzon létre egy parancsfájl-futtatási konfigurációt, amely futtatja a saját könyvtárában található képzési parancsfájlt ( `train.py` ) `project_folder` :

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

Előfordulhat, hogy a futtatási keretrendszert is be szeretné állítani.

* HDI-fürt esetén:
    ```python
    src.run_config.framework = "pyspark"
    ```

* Távoli virtuális gép esetén:
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>A kísérlet elküldése

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> A betanítási Futtatás elküldésekor létrejön a betanítási parancsfájlokat tartalmazó könyvtár pillanatképe, amelyet a rendszer elküld a számítási célra. A munkaterületen található kísérlet részeként is tárolja. Ha módosítja a fájlokat, és újra elküldi a futtatást, csak a módosított fájlok lesznek feltöltve.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> További információ a pillanatképekről: [Pillanatképek](concept-azure-machine-learning-architecture.md#snapshots).


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. További információ: git- [integráció Azure Machine Learninghoz](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Jegyzetfüzet-példák

Tekintse meg ezeket a jegyzetfüzeteket a különböző számítási célokból származó képzésekre:
* [használati útmutató – azureml/képzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [oktatóanyagok/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: a betanítási modell](tutorial-train-models-with-aml.md) felügyelt számítási célt használ a modellek betanításához.
* Ismerje meg, hogyan [javíthatja hatékonyan a hiperparaméterek beállítása](how-to-tune-hyperparameters.md) a jobb modellek létrehozásához? View = Azure-ml-a&megőrzése – nézet = true)
* A betanított modellel megtudhatja, [Hogyan és hol helyezheti üzembe a modelleket](how-to-deploy-and-where.md).
* Tekintse meg a [RunConfiguration Class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py&preserve-view=true) SDK-referenciát.
* [Azure Machine Learning használata az Azure Virtual Networks használatával](how-to-enable-virtual-network.md)