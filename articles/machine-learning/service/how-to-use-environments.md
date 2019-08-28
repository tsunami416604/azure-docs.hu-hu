---
title: Környezetek létrehozása, használata és kezelése modellek képzéséhez és üzembe helyezéséhez
titleSuffix: Azure Machine Learning service
description: Környezetek létrehozása és kezelése modellek képzéséhez és üzembe helyezéséhez. Python-csomagok és egyéb beállítások kezelése a környezetben.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: seodec18
ms.openlocfilehash: e506259b980c0aaf0300c0bb0a1aa0803171643a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098950"
---
# <a name="create-and-manage-environments-for-training-and-deployment"></a>Környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez

Ebből a cikkből megtudhatja, hogyan hozhat létre [](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) és kezelhet Azure Machine learning környezeteket, így nyomon követheti és reprodukálhatja projektjei szoftveres függőségeit.

A szoftveres függőségek kezelése a fejlesztők közös feladata. Szeretné biztosítani, hogy a buildek megismételhetők legyenek a sok kézi szoftveres konfiguráció nélkül. A helyi fejlesztésre, például a pip-re és a Conda vonatkozó megoldásokkal a Azure Machine Learning Service Environments osztály megoldást kínál a helyi és az elosztott Felhőbeli fejlesztéshez.

A cikkben szereplő példák a következőket mutatják be:

* Környezet létrehozása és a csomagok függőségeinek meghatározása
* Környezetek lekérése és frissítése
* Környezet használata képzéshez
* Környezet használata a webszolgáltatások üzembe helyezéséhez

## <a name="what-are-environments"></a>Mik azok a környezetek?

A környezetek megadják a Python-csomagokat, a környezeti változókat és a szoftver beállításait a képzés és a pontozási szkriptek, valamint a futtatási idők (Python, Spark vagy Docker) köré. Ezek a Azure Machine Learning munkaterületen található felügyelt és verzióval rendelkező entitások, amelyek lehetővé teszik a reprodukálható, naplózható és hordozható gépi tanulási munkafolyamatokat a különböző számítási célok között.

A helyi számítási feladatokhoz környezeti objektumokat is használhat, hogy kifejlessze a betanítási parancsfájlt, újrahasznosítsa ugyanezt a környezetet Azure Machine Learning számítási modelleken a Modelles képzések esetében, és még a modellt is üzembe helyezheti ugyanazzal a környezettel.

A következő ábra azt szemlélteti, hogy ugyanaz a környezeti objektum használható a futtatási konfigurációjában a betanításhoz, valamint a webszolgáltatások központi telepítésére vonatkozó következtetési és telepítési konfigurációban is.

![A Machine learning-munkafolyamatban található környezet ábrája](./media/how-to-use-environments/ml-environment.png)

### <a name="types-of-environments"></a>A környezetek típusai

A környezetek széles körben két kategóriába oszthatók: **felhasználó által felügyelt** és **rendszer által felügyelt**.

A felhasználó által felügyelt környezetek esetében Ön felelős a környezet beállításában és minden csomag telepítéséhez, amelyet a képzési szkriptnek szüksége van a számítási célra. A Conda nem fogja megtekinteni a környezetet, vagy semmit sem telepít Önnek. 

A rendszer által felügyelt környezetek akkor használhatók, ha azt szeretné, hogy a [Conda](https://conda.io/docs/) kezelje a Python-környezetet és a parancsfájlok függőségeit. A szolgáltatás alapértelmezés szerint ezt a típusú környezetet feltételezi, mivel a távoli számítási célok nem manuálisan konfigurálhatók.

## <a name="prerequisites"></a>Előfeltételek

* A Azure Machine Learning SDK for Python [telepítve van](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* Egy [Azure Machine learning szolgáltatás](how-to-manage-workspace.md)munkaterülete.

## <a name="create-an-environment"></a>Környezet létrehozása

Több módon is létrehozhat környezetet a kísérletekhez.

### <a name="instantiate-an-environment-object"></a>Környezeti objektum példányának létrehozása

Környezet manuális létrehozásához importálja a környezeti osztályt az SDK-ból, és hozzon létre egy környezeti objektumot a következő kóddal.

```python
from azureml.core import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Conda és pip specifikációs fájlok

Conda-specifikációból vagy pip-követelményekből álló fájlból is létrehozhat környezetet.
Használja az [from_conda_specification ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) vagy a [from_pip_requirements ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) metódust, és adja meg a környezet nevét és a kívánt fájl elérési útját a metódus argumentumában.

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

A következő egy környezeti objektumot hoz létre a meglévő Conda-környezetből `mycondaenv` a [from_existing_conda_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) metódussal.

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

Hasonlóképpen, ha a betanításhoz egy [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objektumot használ, a kalkulátor-példányt közvetlenül is futtathatja Futtatás nélkül, a környezet megadását nem kell megadnia, mert az `Estimator` objektum már magában foglalja a környezetet és a számítási célt.


## <a name="add-packages-to-an-environment"></a>Csomagok hozzáadása egy környezethez

Csomagokat adhat hozzá egy Conda-, Pip-vagy Private Wheel-fájlokat tartalmazó környezethez. Adja meg az egyes csomagok függőségeit a [CondaDependency osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)használatával, és adja hozzá a környezet PythonSection.

### <a name="conda-and-pip-packages"></a>Conda és pip csomagok

Ha egy csomag elérhető egy Conda-csomag adattárában, ajánlott a Conda a pip-en keresztül telepíteni. Ennek az az oka, hogy a Conda-csomagok általában előre elkészített bináris fájlokkal rendelkeznek, amelyek megbízhatóbb telepítést tesznek elérhetővé.

Az alábbi példa a `scikit-learn` [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) és `pillow` `myenv` [ametódusokhasználatávaladjahozzáa0.21.3-tésacsomagot`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) a környezethez.

```python
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Privát Wheel Files

A privát pip-fájlok a saját munkaterület-tárolóba való feltöltésével statikus [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) módszerrel, majd a tárolási URL-cím rögzítésével, valamint az URL-cím a `add_pip_package()` metódusba való továbbításával használhatók.

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

A következő regisztrálja a környezetet `myenv`, és a `ws`munkaterületre.

```python
myenv.register(workspace=ws)
```

Ha első alkalommal használja a képzést vagy üzembe helyezést, a környezet regisztrálva lesz a számítási célra létrehozott és üzembe helyezett munkaterületen. A környezeteket a szolgáltatás gyorsítótárazza. A gyorsítótárazott környezetek újrafelhasználása sokkal kevesebb időt vesz igénybe, mint egy új szolgáltatás vagy egy frissített verzió használata.

### <a name="get-existing-environments"></a>Meglévő környezetek beolvasása

A környezeti osztály olyan metódusokat kínál, amelyek lehetővé teszik a meglévő környezetek lekérését a munkaterületen név, lista vagy adott tanítási Futtatás alapján. Hibaelhárítási és naplózási célokra, reprodukálhatóság

#### <a name="view-list-of-environments"></a>A környezetek listájának megtekintése

Tekintse meg a munkaterületen található környezeteket a [listával ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-), majd válassza ki az egyiket az újbóli használathoz.

```python
from azureml.core import Environment
list("workspace_name")
```

#### <a name="get-environment-by-name"></a>Környezet beolvasása név alapján

Egy adott környezetet a név és a verzió alapján is lekérhet.
A következő kód a [Get ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) metódust használja a környezet `1` `myenv` verziójának lekéréséhez a `ws` munkaterületen.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>Meghatározott környezet betanítása

Ahhoz, hogy a képzés befejeződése után egy adott futtatáshoz használt környezet elérhető legyen, használja a [get_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--)metódust a Run osztályban.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Meglévő környezet frissítése

Ha olyan meglévő környezetet módosít, mint például a Python-csomag hozzáadása, a rendszer létrehozza a környezet új verzióját, amikor futtatja a futtatást, a modell üzembe helyezését vagy a környezet manuális regisztrálását. A verziószámozás lehetővé teszi a környezet változásainak időbeli megtekintését.

Meglévő környezet Python-csomag verziójának frissítéséhez határozza meg a csomag pontos verziószámát. Ellenkező esetben a Azure Machine Learning a környezet létrehozásakor újrahasznosítja a meglévő környezetet a csomag verziójával.

### <a name="debug-the-image-build"></a>A rendszerkép összeállításának hibakeresése

Ez a példa a [build ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) metódust használja a környezet Docker-rendszerképként történő manuális létrehozására, és a [wait_for_completion ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)használatával figyeli a rendszerkép-összeállítás kimeneti naplóit. A beépített rendszerkép ezután a munkaterület Azure Container Registry alatt jelenik meg, amely a hibakereséshez hasznos.

```python
from azureml.core import Image
build = env.build()
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker és környezetek

 A [](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) Azure Machine learning `Environments` osztály DockerSection lehetővé teszi annak a vendég operációs rendszernek a testreszabását és vezérlését, amelyben a betanítási futtatást végrehajtja.

`enable` A Docker esetében a szolgáltatás létrehoz egy Docker-rendszerképet, és létrehoz egy Python-környezetet a saját specifikációi között a Docker-tárolóban. Ez további elkülönítést és reprodukálhatóságot biztosít a képzések futtatásához.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

A kiépítés után a Docker-rendszerkép alapértelmezés szerint a munkaterülethez társított Azure Container Registry jelenik meg.  Az adattár neve *azureml/azureml_\<\>UUID*formátumú. Az egyedi azonosító (*uuuid*) rész a környezeti konfiguráció alapján számított kivonatnak felel meg. Ez lehetővé teszi a szolgáltatás számára annak meghatározását, hogy az adott környezetnek megfelelő rendszerkép már létezik-e az újrafelhasználáshoz.

Emellett a szolgáltatás automatikusan a Ubuntu Linux-alapú alaplemezképek egyikét használja, és telepíti a megadott Python-csomagokat. [](https://github.com/Azure/AzureML-Containers) Az alaprendszerkép rendelkezik CPU-és GPU-verziókkal, és beállíthatja a GPU- `gpu_support=True`rendszerképet.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"

# Specify GPU image
myenv.docker.gpu_support=True
```

> [!NOTE]
> Ha egyéni Docker-rendszerkép használatakor megadja `environment.python.user_managed_dependencies=False` a szolgáltatást, a szolgáltatás létrehoz egy Conda-környezetet a rendszerképben, és végrehajtja a futtatást az adott környezetben, nem pedig az alaprendszerképre telepített Python-kódtárak használata helyett. A paramétert `True` a saját telepített csomagjainak használatára állíthatja be.

## <a name="using-environments-for-training"></a>Környezetek használata képzéshez

A betanítási futtatáshoz össze kell állítania a környezetét, a [számítási célt](concept-compute-target.md) , valamint a Python-szkriptek futtatási konfigurációba való betanítását; futtatások küldésére szolgáló burkoló objektum.

A betanítási kísérlet elküldésekor egy új környezet létrehozása több percet is igénybe vehet, a szükséges függőségek méretétől függően. A környezeteket a szolgáltatás gyorsítótárazza, így mindaddig, amíg a környezet definíciója változatlan marad, a teljes telepítési idő csak egyszer merül fel.

A következő egy helyi parancsfájl-futtatási példa, amelyben a [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) -t használja burkoló objektumként.

```python
from azureml.core import Environment, ScriptRunConfig, Experiment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.compute_target = "local"

# Attach environment to run config
runconfig.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> A futtatási előzmények letiltásához vagy a pillanatképek futtatásához használja a következő beállítást `ScriptRunConfig.run_config.history`:.

Ha nem adja meg a környezetet a futtatási konfigurációban, a szolgáltatás létrehoz egy alapértelmezett környezetet, amely a futtatásának elküldésekor jön létre.

### <a name="train-with-an-estimator"></a>Az egy estimator betanítása

Ha kalkulátort használ a [](how-to-train-ml-models.md) betanításhoz, egyszerűen beküldheti a kalkulátor-példányt közvetlenül, mivel már bezárta a környezetet és a számítási célt.

A következőkben egy scikit-modellen alapuló, egycsomópontos képzésre vonatkozó becslést használunk, amely egy korábban létrehozott számítási cél objektumot `compute_target` és adattár- `ds`objektumot feltételez.

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

Használhat környezeteket webszolgáltatásként való üzembe helyezéskor. Ez lehetővé teszi egy reprodukálható, csatlakoztatott munkafolyamatot, ahol a modell betanítását, tesztelését és üzembe helyezését pontosan ugyanazokkal a kódtárakkal végezheti el, mint a képzési és következtetési számításokban.

Webszolgáltatások üzembe helyezéséhez egyesítse a környezetet, a következtetéseket, a pontozási parancsfájlokat és a regisztrált modellt a telepítési objektumban [()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-). További információ a [webszolgáltatások üzembe helyezéséről](how-to-deploy-and-where.md).

Ebben a példában feltételezzük, hogy elvégezte a betanítási futtatást az Azure Container instance (ACI) szolgáltatásban való üzembe helyezéshez. A webszolgáltatás létrehozásakor a modell-és pontozási fájlok csatlakoztatva vannak a rendszerképhez, és a rendszer hozzáadja a Azure Machine Learning következtetési veremet a rendszerképhez.

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

## <a name="example-notebooks"></a>Példa notebookok

Ez a [példa](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) a jelen cikkben ismertetett fogalmakat és metódusokat mutatja be.

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: A modell](tutorial-train-models-with-aml.md) betanítása felügyelt számítási célt használ a modellek betanításához.
* A betanított modellel megtudhatja, [Hogyan és hol helyezheti üzembe](how-to-deploy-and-where.md)a modelleket.
* Tekintse meg a [környezeti osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) SDK-referenciáját.
