---
title: Betanítási Futtatás konfigurálása
titleSuffix: Azure Machine Learning
description: A gépi tanulási modell tanítása különböző képzési környezetekben (számítási célok). Könnyedén válthat a képzési környezetek között. A képzés helyi elindítása. Ha vertikális felskálázásra van szüksége, váltson át egy felhőalapú számítási célra.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: f93b6ab43e1dbf9230c92d22f8fb22ca48eb720e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275761"
---
# <a name="configure-and-submit-training-runs"></a>Betanítási futtatások konfigurálása és beküldése

Ebből a cikkből megtudhatja, hogyan konfigurálhat és küldhet be Azure Machine Learning-futtatásokat a modellek betanításához.

Ha betanítást végez, gyakori, hogy elindítsa a helyi számítógépen, majd később kibővíti a felhőalapú fürtöt. A Azure Machine Learning használatával különböző számítási célokból futtathat parancsfájlokat anélkül, hogy módosítani kellene a betanítási szkriptet.

Mindössze annyit kell tennie, hogy a **parancsfájl futtatási konfigurációján**belül minden számítási cél esetében meghatározza a környezetet.  Ha ezt követően egy másik számítási célra szeretné futtatni a betanítási kísérletet, adja meg az adott számítás futtatási konfigurációját.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes fiókot a virtuális gép létrehozásának megkezdése előtt. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma
* A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) (>= 1.13.0)
* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md), `ws`
* Egy számítási cél `my_compute_target` .  Számítási cél létrehozása a rel:
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Mi az a parancsfájl-futtatási konfiguráció?
A [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) használatával konfigurálhatja a betanítási futtatáshoz szükséges információkat a kísérlet részeként.

A betanítási kísérletet egy ScriptRunConfig-objektummal küldi el.  Ez az objektum az alábbiakat tartalmazza:

* **source_directory**: a betanítási parancsfájlt tartalmazó forrás könyvtára
* **parancsfájl**: a futtatandó betanítási szkript
* **compute_target**: a futtatandó számítási cél
* **környezet**: a parancsfájl futtatásakor használandó környezet
* és néhány további konfigurálható lehetőség is (további információt a [dokumentációban](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) talál)

## <a name="train-your-model"></a><a id="submit"></a>A modell betanítása

A betanítási futtatást elküldő kód mintája megegyezik a számítási célok összes típusával:

1. Kísérlet létrehozása a futtatáshoz
1. Hozzon létre egy környezetet, amelyben a parancsfájl futni fog
1. ScriptRunConfig létrehozása, amely meghatározza a számítási célt és a környezetet
1. A Futtatás elküldése
1. Várjon, amíg a Futtatás befejeződik

Vagy a következőket teheti:

* HyperDrive-Futtatás küldése a [hiperparaméter finomhangolásához](how-to-tune-hyperparameters.md).
* Kísérlet küldése a [vs Code bővítmény](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)használatával.

## <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet a munkaterületen.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Számítási cél kiválasztása

Válassza ki azt a számítási célt, amelyen a betanítási parancsfájl futni fog. Ha nincs megadva számítási cél a ScriptRunConfig, vagy ha az `compute_target='local'` Azure ml a parancsfájlt helyileg fogja végrehajtani. 

A cikkben szereplő mintakód azt feltételezi, hogy már létrehozott egy számítási célt `my_compute_target` az "Előfeltételek" szakaszból.

## <a name="create-an-environment"></a>Környezet létrehozása
Azure Machine Learning [környezetek](concept-environments.md) a gépi tanulási képzést végző környezet beágyazását jelentik. Megadják a Python-csomagokat, a Docker-rendszerképet, a környezeti változókat és a szoftver beállításait a képzés és a pontozási szkriptek köré. Emellett a futtatókörnyezeteket (Python, Spark vagy Docker) is megadják.

Meghatározhatja saját környezetét, vagy használhat egy Azure ML-beli kiszervezett környezetet is. A válogatott [környezetek](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#use-a-curated-environment) előre definiált környezetek, amelyek alapértelmezés szerint a munkaterületen elérhetők. Ezeket a környezeteket a gyorsítótárazott Docker-rendszerképek alkotják, ami csökkenti a Futtatás előkészítési költségeit. A rendelkezésre álló, kitalált környezetek teljes listájáért tekintse meg [Azure Machine learning kurátori környezeteket](https://docs.microsoft.com/azure/machine-learning/resource-curated-environments) .

Távoli számítási cél esetén a következő népszerű kurátori környezetek egyikét használhatja a kezdéshez:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

A környezetekkel kapcsolatos további információkért és részletekért lásd: [Create & Azure Machine learning-környezetek használata](how-to-use-environments.md).
  
### <a name="local-compute-target"></a>Helyi számítási cél

Ha a számítási cél a **helyi gép**, Ön felelős annak biztosításáért, hogy az összes szükséges csomag elérhető legyen a Python-környezetben, ahol a szkript fut.  A használatával az `python.user_managed_dependencies` aktuális Python-környezetet (vagy a Pythont a megadott elérési úton) használhatja.

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>A parancsfájl futtatási konfigurációjának létrehozása

Most, hogy van egy számítási cél ( `my_compute_target` ) és egy környezet ( `myenv` ), hozzon létre egy parancsfájl-futtatási konfigurációt, amely futtatja a saját könyvtárában található képzési parancsfájlt ( `train.py` ) `project_folder` :

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)
```

Ha nem ad meg környezetet, a rendszer létrehoz egy alapértelmezett környezetet az Ön számára.

Ha olyan parancssori argumentumokkal rendelkezik, amelyeket át szeretne adni a betanítási szkriptnek, akkor megadhatja azokat a **`arguments`** ScriptRunConfig konstruktor paraméterén keresztül, például: `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` .

Ha szeretné felülbírálni a futtatáshoz engedélyezett alapértelmezett maximális időt, ezt a paraméterrel teheti meg **`max_run_duration_seconds`** . A rendszer megkísérli automatikusan megszakítani a futtatást, ha ez az érték hosszabb időt vesz igénybe.

### <a name="specify-a-distributed-job-configuration"></a>Elosztott feladatok konfigurációjának meghatározása
Ha elosztott betanítási feladatot szeretne futtatni, adja meg az elosztott feladatokhoz tartozó konfigurációt a **`distributed_job_config`** paraméterhez. A támogatott konfigurációs típusok közé tartozik az [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true), a [TensorflowConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true). 

További információ és példák az elosztott Horovod, a TensorFlow és a PyTorch feladatok futtatásáról:

* [TensorFlow-modellek betanítása](https://docs.microsoft.com/azure/machine-learning/how-to-train-tensorflow#distributed-training)
* [PyTorch-modellek betanítása](https://docs.microsoft.com/azure/machine-learning/how-to-train-pytorch#distributed-training)

## <a name="submit-the-experiment"></a>A kísérlet elküldése

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> A betanítási Futtatás elküldésekor létrejön a betanítási parancsfájlokat tartalmazó könyvtár pillanatképe, amelyet a rendszer elküld a számítási célra. A munkaterületen található kísérlet részeként is tárolja. Ha módosítja a fájlokat, és újra elküldi a futtatást, csak a módosított fájlok lesznek feltöltve.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> További információ a pillanatképekről: [Pillanatképek](concept-azure-machine-learning-architecture.md#snapshots).

> [!IMPORTANT]
> **Speciális mappák** Két mappa, *kimenet* és *napló*, a Azure Machine learning speciális kezelést kap. Ha a betanítás során a rendszer a gyökérkönyvtárhoz viszonyított *kimeneteket* és *naplókat* tartalmazó mappákba ír fájlokat `./outputs` , `./logs` a fájlok automatikusan feltöltve lesznek a futtatási előzményekbe, hogy a Futtatás befejezése után hozzáférhessenek hozzájuk.
>
> Az összetevőknek a betanítás során történő létrehozásához (például a Model Files, az ellenőrzőpontok, az adatfájlok vagy a képek kirajzolása) írja ezeket a `./outputs` mappába.
>
> Hasonlóképpen bármilyen naplót írhat a betanításból a `./logs` mappába. Azure Machine Learning [TensorBoard-integrációjának](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) kihasználása érdekében ügyeljen arra, hogy a TensorBoard-naplókat a mappába írja. Amíg a Futtatás folyamatban van, el tudja indítani a TensorBoard, és továbbíthatja ezeket a naplókat.  Később is visszaállíthatja a naplókat az előző futtatások bármelyikéről.
>
> Ha például le szeretné tölteni a *kimenet* mappába a helyi gépre írt fájlt a távoli képzés futtatása után: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. További információ: git- [integráció Azure Machine Learninghoz](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Jegyzetfüzet-példák

Tekintse meg ezeket a jegyzetfüzeteket a futtatások konfigurálására példákat a különböző képzési forgatókönyvekhez:
* [használati útmutató – azureml/képzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [használati útmutató – azureml/ml – keretrendszerek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [oktatóanyagok/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: a betanítási modell](tutorial-train-models-with-aml.md) felügyelt számítási célt használ a modellek betanításához.
* Megtudhatja, hogyan taníthat modelleket konkrét ML-keretrendszerekkel, például a [Scikit-Learn](how-to-train-scikit-learn.md), a [TensorFlow](how-to-train-tensorflow.md)és a [PyTorch](how-to-train-pytorch.md).
* Ismerje meg, hogy miként lehet [hatékonyan hangolni a hiperparaméterek beállítása](how-to-tune-hyperparameters.md) a jobb modellek létrehozásához.
* A betanított modellel megtudhatja, [Hogyan és hol helyezheti üzembe a modelleket](how-to-deploy-and-where.md).
* Tekintse meg a [ScriptRunConfig Class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) SDK-referenciát.
* [Azure Machine Learning használata az Azure Virtual Networks használatával](how-to-enable-virtual-network.md)
