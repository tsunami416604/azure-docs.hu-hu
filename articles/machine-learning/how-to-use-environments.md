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
ms.openlocfilehash: 9d6c823b5dc85676b15188c8f1783e50e5ed441b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717814"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Környezetek újrafelhasználása a betanításhoz és üzembe helyezéshez Azure Machine Learning használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet Azure Machine Learning [környezeteket](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py). A környezetek segítségével nyomon követheti és reprodukálhatja projektjei szoftveres függőségeit.

A szoftveres függőségek kezelése a fejlesztők közös feladata. Biztosítani szeretné, hogy a buildek reprodukálása kiterjedt manuális szoftveres konfiguráció nélkül történjen. A Azure Machine Learning `Environment` osztály olyan helyi fejlesztési megoldásokhoz, mint a pip és a Conda, és megoldást kínál a helyi és az elosztott Felhőbeli fejlesztésre is.

A cikkben szereplő példák a következőket mutatják be:

* Hozzon létre egy környezetet, és határozza meg a csomagok függőségeit.
* Környezetek beolvasása és frissítése.
* Oktatási környezet használata.
* Környezet használata a webszolgáltatások telepítéséhez.

A környezetek Azure Machine Learningban való működésének magas szintű áttekintését lásd: [Mi az a ml-környezet?](concept-environments.md).

## <a name="prerequisites"></a>Előfeltételek

* A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure Machine learning munkaterület](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Környezet létrehozása

Az alábbi fejezetek több módszert mutatnak be, amelyekkel környezetek hozhatók létre a kísérletekhez.

### <a name="use-a-curated-environment"></a>Kurátori környezet használata

Kiválaszthatja az egyik kurátori környezetet a következő kezdéshez: 

* A _AzureML minimális_ környezete minimális csomagokat tartalmaz, amelyek lehetővé teszik a Futtatás nyomon követését és az eszközök feltöltését. Használhatja kiindulási pontként a saját környezetében.

* A _AzureML-oktatóanyag_ környezet általános adatelemzési csomagokat tartalmaz. A csomagok közé tartozik a Scikit-Learn, a pandák, a Matplotlib és a azureml-SDK csomagok nagyobb készlete.

A beérkező környezetek a gyorsítótárazott Docker-rendszerképekkel támogatottak. Ez a biztonsági mentés csökkenti a Futtatás előkészítésének költségeit.

A `Environment.get` módszer használatával válassza ki az egyik kurátori környezetet:

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
>  Ne indítsa el a saját környezet nevét a _AzureML_ előtaggal. Ez az előtag a kiszolgált környezetek számára van fenntartva.

### <a name="instantiate-an-environment-object"></a>Környezeti objektum példányának létrehozása

Környezet manuális létrehozásához importálja a `Environment` osztályt az SDK-ból. Ezután használja a következő kódot egy környezeti objektum létrehozásához.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Conda és pip specifikációs fájlok használata

Conda-specifikációból vagy pip-követelményekből álló fájlból is létrehozhat környezetet. Használja a [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) metódust vagy a [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) metódust. A metódus argumentumban adja meg a környezet nevét és a kívánt fájl elérési útját.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Meglévő Conda-környezetek használata

Ha meglévő Conda-környezettel rendelkezik a helyi számítógépen, akkor a szolgáltatás használatával létrehozhat egy környezeti objektumot. Ezzel a stratégiával újra felhasználhatja a helyi interaktív környezetet távoli futtatásokon.

A következő kód egy környezeti objektumot hoz létre a meglévő Conda-környezetből `mycondaenv`. A [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) metódust használja.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Környezetek automatikus létrehozása

Környezet automatikus létrehozása a betanítási Futtatás elküldésével. Küldje el a futtatást a `submit()` metódus használatával. A betanítási kísérlet elküldésekor az új környezet létrehozása több percet is igénybe vehet. A létrehozás időtartama a szükséges függőségek méretétől függ. 

Ha nem ad meg környezetet a futtatási konfigurációban a Futtatás elküldése előtt, akkor a rendszer létrehoz egy alapértelmezett környezetet.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

Hasonlóképpen, ha egy [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objektumot használ a betanításhoz, közvetlenül is elküldheti a kalkulátor-példányt futtatásra anélkül, hogy környezetet kellene megadnia. A `Estimator` objektum már magában foglalja a környezetet és a számítási célt.

## <a name="add-packages-to-an-environment"></a>Csomagok hozzáadása egy környezethez

Csomagokat adhat hozzá egy környezethez Conda, Pip vagy Private Wheel fájlok használatával. Minden csomag-függőséget a [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) osztály használatával határozhat meg. Adja hozzá a környezet `PythonSection`ához.

### <a name="conda-and-pip-packages"></a>Conda és pip csomagok

Ha egy csomag elérhető egy Conda-csomagban, akkor javasoljuk, hogy a pip telepítése helyett a Conda-telepítést használja. A Conda-csomagok általában előre elkészített bináris fájlokkal rendelkeznek, amelyek megbízhatóbb telepítést tesznek elérhetővé.

A következő példa hozzáadja a környezetet. Hozzáadja `scikit-learn`0.21.3 verzióját. Emellett hozzáadja a `pillow` csomagot, `myenv`. A példa a [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) metódust és a [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) metódust használja.

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

A titkos pip-fájlok használatával először töltse fel őket a munkaterület-tárolóba. Ezeket egy statikus [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) módszerrel töltheti fel. Ezután rögzítse a tárolási URL-címet, és adja át az URL-címet a `add_pip_package()` metódusnak.

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

A rendszer automatikusan regisztrálja a környezetet a munkaterületen, amikor elküld egy futtatást vagy üzembe helyez egy webszolgáltatást. A környezetet manuálisan is regisztrálhatja a [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) metódus használatával. Ezzel a művelettel a környezet egy olyan entitásba kerül, amely a felhőben nyomon követhető és verzióban van. Az entitás megosztható a munkaterület felhasználói között.

A következő kód regisztrálja a `myenv` környezetet a `ws` munkaterületre.

```python
myenv.register(workspace=ws)
```

Ha a környezetet első alkalommal használja a betanítás vagy az üzembe helyezés során, az regisztrálva van a munkaterületen. Ezt követően a számítási célra építjük és helyezik üzembe. A szolgáltatás gyorsítótárazza a környezeteket. A gyorsítótárazott környezetek újrafelhasználása sokkal kevesebb időt vesz igénybe, mint egy új szolgáltatás vagy egy frissített verzió használata.

### <a name="get-existing-environments"></a>Meglévő környezetek beolvasása

A `Environment` osztály olyan metódusokat kínál, amelyek lehetővé teszik a meglévő környezetek lekérését a munkaterületen. A környezeteket a név, a lista vagy egy adott tanítási Futtatás alapján kérheti le. Ez az információ hasznos lehet a hibaelhárításhoz, a naplózáshoz és a reprodukálhatósághoz.

#### <a name="view-a-list-of-environments"></a>A környezetek listájának megtekintése

A munkaterület környezeteit a [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) osztály használatával tekintheti meg. Ezután válasszon ki egy környezetet az újrafelhasználáshoz.

#### <a name="get-an-environment-by-name"></a>Környezet beszerzése név szerint

Egy adott környezetet a név és a verzió alapján is lekérhet. A következő kód a [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) metódust használja az `myenv`-környezet `1`ének lekérésére a `ws` munkaterületen.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Futtatási specifikus környezet betanítása

A betanítás befejezése után egy adott futtatáshoz használt környezet beszerzéséhez használja a `Run` osztály [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) metódusát.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Meglévő környezet frissítése

Tegyük fel, hogy egy meglévő környezetet módosít, például egy Python-csomag hozzáadásával. A rendszer ezután létrehozza a környezet új verzióját, amikor elküld egy futtatást, üzembe helyez egy modellt, vagy manuálisan regisztrálja a környezetet. A verziószámozás segítségével megtekintheti a környezet változásait az idő múlásával.

Egy Python-csomag meglévő környezetben való frissítéséhez határozza meg a csomag verziószámát. Ha nem a pontos verziószámot használja, akkor Azure Machine Learning a meglévő környezetet az eredeti csomag verziójával fogja újra felhasználni.

### <a name="debug-the-image-build"></a>A rendszerkép összeállításának hibakeresése

A következő példa a [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) metódust használja a környezet Docker-rendszerképként történő manuális létrehozására. A [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)használatával figyeli a rendszerkép-összeállítás kimeneti naplóit. A beépített rendszerkép ekkor megjelenik a munkaterület Azure Container Registry-példányában. Ez az információ hasznos lehet a hibakereséshez.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Docker engedélyezése

 A Azure Machine Learning `Environment` osztály [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) segítségével részletesen testreszabhatja és szabályozhatja a vendég operációs rendszert, amelyen futtatja a képzést.

Ha `enable` Docker-t, a szolgáltatás létrehoz egy Docker-rendszerképet. Egy olyan Python-környezetet is létrehoz, amely a Docker-tárolóban lévő specifikációkat használja. Ez a funkció további elkülönítést és reprodukálhatóságot biztosít a képzések futtatásához.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Alapértelmezés szerint az újonnan létrehozott Docker-rendszerkép megjelenik a munkaterülethez társított tároló-beállításjegyzékben.  Az adattár neve *azureml/azureml_\<uuid\>* formátumú. A név egyedi azonosítója (*UUID*) a környezeti konfiguráció alapján számított kivonatnak felel meg. Ez a levelezés lehetővé teszi, hogy a szolgáltatás meghatározhatja, hogy az adott környezethez tartozó rendszerkép már létezik-e az újrafelhasználáshoz.

Emellett a szolgáltatás automatikusan a Ubuntu Linux-alapú [alaplemezképek](https://github.com/Azure/AzureML-Containers)egyikét használja. Telepíti a megadott Python-csomagokat. Az alaprendszerkép processzor-és GPU-verziókkal rendelkezik. Azure Machine Learning automatikusan észleli, hogy melyik verziót kell használni.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
# Alternatively, you can specify the contents of dockerfile of your base image
with open("docker_file_of_your_base_image", "r") as f:
    dockerfile_contents_of_your_base_image=f.read()
myenv.docker.base_dockerfile=dockerfile_contents_of_your_base_image 
```

> [!NOTE]
> Ha az egyéni Docker-rendszerképek használatakor `environment.python.user_managed_dependencies=False`t ad meg, akkor a szolgáltatás Conda-környezetet hoz létre a rendszerképben. Az alaprendszerképre telepített Python-kódtárak használata helyett a futtatást fogja végrehajtani a környezetben. Állítsa a paramétert úgy, hogy a saját telepített csomagok használatára `True`.

## <a name="use-environments-for-training"></a>Környezetek használata képzéshez

A betanítási futtatáshoz össze kell állítania a környezetét, a [számítási célt](concept-compute-target.md), valamint a Python-szkriptek futtatási konfigurációját. Ez a konfiguráció egy olyan burkoló objektum, amely a futtatások küldésére szolgál.

A betanítási kísérlet elküldésekor egy új környezet létrehozása több percet is igénybe vehet. Az időtartam a szükséges függőségek méretétől függ. A környezeteket a szolgáltatás gyorsítótárazza. Ha a környezet definíciója változatlan marad, csak egyszer kell megfizetnie a teljes telepítési időt.

A következő helyi parancsfájl futtatási példája azt mutatja be, hogy hol szeretné használni a [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) burkoló objektumként.

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

Ha nem adja meg a környezetet a futtatási konfigurációban, akkor a szolgáltatás egy alapértelmezett környezetet hoz létre, amikor elküldi a futtatását.

### <a name="use-an-estimator-for-training"></a>Kalkulátor használata képzéshez

Ha [becslést](how-to-train-ml-models.md) használ a betanításhoz, akkor közvetlenül is elküldheti a kalkulátor-példányt. A környezet és a számítási cél már be van ágyazva.

A következő kód egy egycsomópontos képzésre vonatkozó becslést használ. Egy `scikit-learn` modell távoli számítási folyamatán fut. Azt feltételezi, hogy korábban létrehozott egy számítási cél objektumot, egy `compute_target`és egy adattár-objektumot, `ds`.

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

## <a name="use-environments-for-web-service-deployment"></a>Környezetek használata a webszolgáltatások üzembe helyezéséhez

A környezeteket webszolgáltatásként való üzembe helyezéskor is használhatja. Ez a funkció lehetővé teszi a reprodukálható, csatlakoztatott munkafolyamatot. Ebben a munkafolyamatban a modell betanítását, tesztelését és üzembe helyezését ugyanazokat a kódtárakat használva végezheti el, mint a betanítási számítási és a következtetési számításokban.

Webszolgáltatások üzembe helyezéséhez egyesítse a környezetet, a következtetéseket, a pontozási parancsfájlt és a regisztrált modellt a telepítési objektumban, [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). További információt a [modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)című témakörben talál.

Ebben a példában feltételezzük, hogy elvégezte a betanítási futtatást. Most szeretné telepíteni ezt a modellt a Azure Container Instances. A webszolgáltatás létrehozásakor a modell-és pontozási fájlok csatlakoztatva vannak a rendszerképhez, és a rendszer hozzáadja a Azure Machine Learning következtetési veremet a rendszerképhez.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Jegyzetfüzetek – példa

Ez a [példa](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) a jelen cikkben ismertetett fogalmakat és metódusokat mutatja be.

[Modell üzembe helyezése egyéni Docker-alaprendszerkép használatával](how-to-deploy-custom-docker-image.md) bemutatja, hogyan helyezhet üzembe egy modellt egy egyéni Docker-alaprendszerkép használatával.

Ez a [példában szereplő jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) bemutatja, hogyan helyezhet üzembe egy Spark-modellt webszolgáltatásként.

## <a name="create-and-manage-environments-with-the-cli"></a>Környezetek létrehozása és kezelése a parancssori felülettel

A [Azure Machine learning CLI](reference-azure-machine-learning-cli.md) a Python SDK legtöbb funkcióját tükrözi. A segítségével környezeteket hozhat létre és kezelhet. Az ebben a szakaszban ismertetett parancsok az alapszintű funkciókat mutatják be.

A következő parancs a megadott könyvtár alapértelmezett környezeti definíciójának fájljait összefoglalja. Ezek a fájlok JSON-fájlok. Ugyanúgy működnek, mint a megfelelő osztály az SDK-ban. A fájlok használatával létrehozhat olyan új környezeteket, amelyek egyéni beállításokkal rendelkeznek. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Futtassa a következő parancsot egy környezet egy adott címtárból való regisztrálásához.

```azurecli-interactive
az ml environment register -d myenvdir
```

Futtassa az alábbi parancsot az összes regisztrált környezet listázásához.

```azurecli-interactive
az ml environment list
```

Töltse le a regisztrált környezetet a következő parancs használatával.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Következő lépések

* Ha felügyelt számítási célt kíván használni a modell betanításához, tekintse meg az [oktatóanyag: modell](tutorial-train-models-with-aml.md)betanítása című témakört.
* A betanított modellel megtudhatja, [Hogyan és hol helyezheti üzembe a modelleket](how-to-deploy-and-where.md).
* Tekintse meg a [`Environment` Class SDK-referenciát](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* A cikkben ismertetett fogalmakkal és módszerekkel kapcsolatos további információkért tekintse meg a [példa notebookot](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments).
