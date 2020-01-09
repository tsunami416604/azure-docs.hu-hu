---
title: Újrafelhasználható ML-környezetek létrehozása
titleSuffix: Azure Machine Learning
description: Környezetek létrehozása és kezelése modellek képzéséhez és üzembe helyezéséhez. Python-csomagok és egyéb beállítások kezelése a környezetben.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: af6848e85db5d2a557835b063a499e3439557eb6
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690428"
---
# <a name="reuse-environments-for-training--deployment-with-azure-machine-learning"></a>Környezetek újrafelhasználása & üzembe helyezésének betanításához Azure Machine Learningsal.
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet Azure Machine Learning [környezeteket](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) , így nyomon követheti és reprodukálhatja projektjei szoftveres függőségeit.

A szoftveres függőségek kezelése a fejlesztők közös feladata. Szeretné biztosítani, hogy a buildek megismételhetők legyenek a sok kézi szoftveres konfiguráció nélkül. A helyi fejlesztésre, például a pip-re és a Conda vonatkozó megoldásokkal a Azure Machine Learning Environments osztály megoldást kínál a helyi és az elosztott Felhőbeli fejlesztésre.

A cikkben szereplő példák a következőket mutatják be:

* Környezet létrehozása és a csomagok függőségeinek meghatározása
* Környezetek lekérése és frissítése
* Környezet használata képzéshez
* Környezet használata a webszolgáltatások üzembe helyezéséhez

A környezetek Azure Machine Learningban való működésének részletes áttekintését lásd a [fogalmi cikkben](concept-environments.md) .

## <a name="prerequisites"></a>Előfeltételek

* A Azure Machine Learning SDK for Python [telepítve van](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md).

## <a name="create-an-environment"></a>Környezet létrehozása

Több módon is létrehozhat környezetet a kísérletekhez.

### <a name="use-curated-environment"></a>Kurátori környezet használata

Kiválaszthatja, hogy melyik kurátori környezetből kezdődjön. 

* A __AzureML minimális__ környezete minimális csomagokat tartalmaz, amelyek lehetővé teszik a Futtatás nyomon követését és az eszközök feltöltését. Használhatja kiindulási pontként a saját környezetében.

* A __AzureML-oktatóanyag__ környezet általános adatelemzési csomagokat tartalmaz, például a Scikit, a pandák és a Matplotlib, valamint a AzureML-SDK-csomagok nagyobb készletét.

A megrendelt környezetek a gyorsítótárazott Docker-rendszerképekkel támogatottak, így csökkentve a Futtatás előkészítésének költségeit.

Használja a __Environment. Get__ metódust az egyik kurátori környezet kiválasztásához:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

A következő kód használatával listázhatja a kurátori környezeteket és azok csomagjait:
```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Ne indítsa el a saját környezet nevét a _AzureML_ előtaggal. A kiszolgált környezetek számára van fenntartva.

### <a name="instantiate-an-environment-object"></a>Környezeti objektum példányának létrehozása

Környezet manuális létrehozásához importálja a környezeti osztályt az SDK-ból, és hozzon létre egy környezeti objektumot a következő kóddal.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Conda és pip specifikációs fájlok

Conda-specifikációból vagy pip-követelményekből álló fájlból is létrehozhat környezetet.
Használja a [from_conda_specification ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) vagy a [from_pip_requirements ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) metódust, és adja meg a környezet nevét és a kívánt fájl elérési útját a metódus argumentumban.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>Meglévő Conda-környezet

Ha rendelkezik egy meglévő Conda-környezettel a helyi számítógépen, a szolgáltatás megoldást kínál a környezeti objektumok létrehozására. Ezzel a módszerrel újra felhasználhatja a helyi interaktív környezetet távoli futtatások esetén.

A következő kód egy környezeti objektumot hoz létre a meglévő Conda-környezetből `mycondaenv` a [from_existing_conda_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) metódussal.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>Környezetek automatikus létrehozása

Hozzon létre automatikusan egy környezetet a beküldési () metódus beküldésével. A betanítási kísérlet elküldésekor az új környezet létrehozása több percet is igénybe vehet, a szükséges függőségek méretétől függően. 

Ha nem ad meg környezetet a futtatási konfigurációban a Futtatás elküldése előtt, a rendszer létrehoz egy alapértelmezett környezetet az Ön számára.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

Hasonlóképpen, ha [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objektumot használ a betanításhoz, a kalkulátor-példányt közvetlenül is futtathatja Futtatás nélkül, anélkül, hogy meg kellene adnia egy környezetet. A `Estimator` objektum már beágyazza a környezetet és a számítási célt.


## <a name="add-packages-to-an-environment"></a>Csomagok hozzáadása egy környezethez

Csomagokat adhat hozzá egy Conda-, Pip-vagy Private Wheel-fájlokat tartalmazó környezethez. Adja meg az egyes csomagok függőségeit a [CondaDependency osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)használatával, és adja hozzá a környezet PythonSection.

### <a name="conda-and-pip-packages"></a>Conda és pip csomagok

Ha egy csomag elérhető egy Conda-csomag adattárában, ajánlott a Conda a pip-en keresztül telepíteni. Ennek az az oka, hogy a Conda-csomagok általában előre elkészített bináris fájlokkal rendelkeznek, amelyek megbízhatóbb telepítést tesznek elérhetővé.

Az alábbi példa a `scikit-learn`, a speciális verziójú 0.21.3 és `pillow` csomagot adja hozzá a környezethez, `myenv` a [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) és [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) metódusokkal.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Privát Wheel Files

A privát pip-fájlokat használhatja úgy, hogy először a munkaterület-tárolóba tölti fel a statikus [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) metódus használatával, majd rögzíti a tárolási URL-címet, és átadja az URL-címet a `add_pip_package()` metódusnak.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Környezetek kezelése

Kezelheti a környezeteket, így frissítheti, nyomon követheti és újra felhasználhatja őket a számítási célok és a munkaterület többi felhasználója között.

### <a name="register-environments"></a>Környezetek regisztrálása

A rendszer automatikusan regisztrálja a környezetet a munkaterületen, amikor elküld egy futtatást vagy üzembe helyez egy webszolgáltatást. A környezetet manuálisan is regisztrálhatja a [Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) metódus használatával. Ezzel a művelettel a környezet egy, a felhőben nyomon követett és verziószámozás alatt álló entitásba kerül, és megosztható a munkaterület felhasználói között.

A következő kód regisztrálja a környezetet `myenv`a munkaterületre, `ws`.

```python
myenv.register(workspace=ws)
```

Ha első alkalommal használja a képzést vagy üzembe helyezést, a környezet regisztrálva lesz a számítási célra létrehozott és üzembe helyezett munkaterületen. A környezeteket a szolgáltatás gyorsítótárazza. A gyorsítótárazott környezetek újrafelhasználása sokkal kevesebb időt vesz igénybe, mint egy új szolgáltatás vagy egy frissített verzió használata.

### <a name="get-existing-environments"></a>Meglévő környezetek beolvasása

A környezeti osztály olyan metódusokat kínál, amelyek lehetővé teszik a meglévő környezetek lekérését a munkaterületen név alapján, listaként vagy a hibaelhárítási vagy naplózási célból, valamint a reprodukálhatóságot.

#### <a name="view-list-of-environments"></a>A környezetek listájának megtekintése

Tekintse meg a munkaterületen található környezeteket [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-), majd válassza ki az egyiket az újbóli felhasználáshoz.

#### <a name="get-environment-by-name"></a>Környezet beolvasása név alapján

Egy adott környezetet a név és a verzió alapján is lekérhet.
A következő kód a [Get ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) metódust használja a környezet `1`ának lekérésére, `myenv` a `ws` munkaterületen.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>Meghatározott környezet betanítása

Ahhoz, hogy a képzés befejeződése után egy adott futtatáshoz használt környezet elérhető legyen, használja az [get_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) metódust a Run osztályban.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Meglévő környezet frissítése

Ha olyan meglévő környezetet módosít, mint például a Python-csomag hozzáadása, a rendszer a Futtatás, a modell telepítése vagy a környezet manuális regisztrálása során létrehozza a környezet új verzióját. A verziószámozás lehetővé teszi a környezet változásainak időbeli megtekintését.

Meglévő környezet Python-csomag verziójának frissítéséhez határozza meg a csomag pontos verziószámát. Ellenkező esetben a Azure Machine Learning a környezet létrehozásakor újrahasznosítja a meglévő környezetet a csomag verziójával.

### <a name="debug-the-image-build"></a>A rendszerkép összeállításának hibakeresése

Ez a példa a [build ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) metódust használja a környezet Docker-rendszerképként történő manuális létrehozására, és a rendszerkép-buildek kimeneti naplóit a [wait_for_completion ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)használatával figyeli. A beépített rendszerkép ezután a munkaterület Azure Container Registry alatt jelenik meg, amely a hibakereséshez hasznos.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker és környezetek

 A Azure Machine Learning `Environments` osztály [DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) lehetővé teszi, hogy részletesen testreszabja és vezérelje a vendég operációs rendszert, amelyben a betanítási futtatást végrehajtja.

Amikor `enable` Docker-t, a szolgáltatás létrehoz egy Docker-rendszerképet, és létrehoz egy Python-környezetet a Docker-tárolóban lévő specifikációk szerint. Ez további elkülönítést és reprodukálhatóságot biztosít a képzések futtatásához.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

A kiépítés után a Docker-rendszerkép alapértelmezés szerint a munkaterülethez társított Azure Container Registry jelenik meg.  Az adattár neve *azureml/azureml_\<uuid\>* formátumú. Az egyedi azonosító (*UUID*) rész a környezeti konfigurációból kiszámított kivonatnak felel meg. Ez lehetővé teszi a szolgáltatás számára annak meghatározását, hogy az adott környezetnek megfelelő rendszerkép már létezik-e az újrafelhasználáshoz.

Emellett a szolgáltatás automatikusan a Ubuntu Linux-alapú [alaplemezképek](https://github.com/Azure/AzureML-Containers)egyikét használja, és telepíti a megadott Python-csomagokat. Az alaprendszerkép rendelkezik CPU-és GPU-verziókkal. Azure Machine Learning automatikusan észleli, hogy melyik verziót kell használni.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

> [!NOTE]
> Ha az egyéni Docker-rendszerképek használatakor `environment.python.user_managed_dependencies=False`t ad meg, a szolgáltatás Conda-környezetet hoz létre a rendszerképben, és végrehajtja a futtatást az adott környezetben, nem pedig az alaprendszerképre telepített Python-kódtárak használata helyett. Állítsa a paramétert úgy, hogy a saját telepített csomagok használatára `True`.

## <a name="using-environments-for-training"></a>Környezetek használata képzéshez

A betanítási futtatáshoz össze kell állítania a környezetét, a [számítási célt](concept-compute-target.md) , valamint a Python-szkriptek futtatási konfigurációba való betanítását; futtatások küldésére szolgáló burkoló objektum.

A betanítási kísérlet elküldésekor egy új környezet létrehozása több percet is igénybe vehet, a szükséges függőségek méretétől függően. A környezeteket a szolgáltatás gyorsítótárazza, így mindaddig, amíg a környezet definíciója változatlan marad, a teljes telepítési idő csak egyszer merül fel.

A következő helyi parancsfájl futtatási példája azt mutatja be, hogy hová szeretné használni a [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) a burkoló objektumként.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> A futtatási előzmények letiltásához vagy a pillanatképek futtatásához használja a `ScriptRunConfig.run_config.history`alatti beállítást.

Ha nem adja meg a környezetet a futtatási konfigurációban, a szolgáltatás létrehoz egy alapértelmezett környezetet, amely a futtatásának elküldésekor jön létre.

### <a name="train-with-an-estimator"></a>Betanítás kalkulátorral

Ha [kalkulátort](how-to-train-ml-models.md) használ a betanításhoz, egyszerűen beküldheti a kalkulátor-példányt közvetlenül, mivel már bezárta a környezetet és a számítási célt.

A következő kód egy scikit-modellen alapuló, egycsomópontos képzésre vonatkozó becslést használ, és feltételezi, hogy egy korábban létrehozott számítási célobjektum, `compute_target` és adattár objektum, `ds`.

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="using-environments-for-web-service-deployment"></a>Környezetek használata a webszolgáltatások üzembe helyezéséhez

Használhat környezeteket webszolgáltatásként való üzembe helyezéskor. Ez lehetővé teszi egy reprodukálható, csatlakoztatott munkafolyamatot, ahol a modell betanítását, tesztelését és üzembe helyezését pontosan ugyanazokkal a könyvtárakkal végezheti el, mint a képzési és következtetési számításokban.

Webszolgáltatások üzembe helyezéséhez egyesítse a környezetet, a következtetéseket, a pontozási parancsfájlokat és a regisztrált modellt a telepítési objektumban [()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). További információ a [webszolgáltatások üzembe helyezéséről](how-to-deploy-and-where.md).

Ebben a példában feltételezzük, hogy elvégezte a betanítási kísérletet, és ezt a modellt egy Azure Container instance (ACI) szolgáltatásban szeretné üzembe helyezni. A webszolgáltatás létrehozásakor a modell-és pontozási fájlok csatlakoztatva vannak a rendszerképhez, és a Azure Machine Learning következtetési verem bekerül a rendszerképbe.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Jegyzetfüzetek – példa

Ez a [példa](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) a jelen cikkben ismertetett fogalmakat és metódusokat mutatja be.

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: a betanítási modell](tutorial-train-models-with-aml.md) felügyelt számítási célt használ a modellek betanításához.
* A betanított modellel megtudhatja, [Hogyan és hol helyezheti üzembe a modelleket](how-to-deploy-and-where.md).
* Tekintse meg a [környezeti osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) SDK-referenciáját.
