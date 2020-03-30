---
title: Újrafelhasználható ml-környezetek létrehozása
titleSuffix: Azure Machine Learning
description: Hozzon létre és kezelje a modellek betanításához és üzembe helyezéséhez. Python-csomagok és a környezet egyéb beállításainak kezelése.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 3b1fc5dc427a8a9a1987b0ef916b99edb25e292a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063983"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Környezetek újrafelhasználása betanításhoz és üzembe helyezéshez az Azure Machine Learning használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet Azure Machine [Learning-környezeteket.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) A környezetekkel nyomon követheti és reprodukálhatja a projektek szoftverfüggőségeit a fejlődésük során.

A szoftverfüggőség-kezelés gyakori feladat a fejlesztők számára. Biztosítani szeretné, hogy a buildek reprodukálhatók legyenek kiterjedt manuális szoftverkonfiguráció nélkül. Az Azure `Environment` Machine Learning osztály helyi fejlesztési megoldások, például a pip és a Conda, és ez megoldást nyújt mind a helyi és az elosztott felhőfejlesztés.

A cikkben szereplő példák bemutatják, hogyan:

* Hozzon létre egy környezetet, és adja meg a csomagfüggőségeket.
* Környezetek beolvasása és frissítése.
* Használjon környezetet a képzéshez.
* Használjon környezetet a webszolgáltatás telepítéséhez.

A környezetek Azure Machine Learningben való működésének magas szintű áttekintését a [Mik azok a gépi tanulási környezetek.](concept-environments.md)

## <a name="prerequisites"></a>Előfeltételek

* Az [Azure Machine Learning SDK pythonhoz](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Egy Azure Machine Learning-munkaterület](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Környezet létrehozása

A következő szakaszok azt ismertetik, hogy miként hozhat létre környezetet a kísérletekhez.

### <a name="use-a-curated-environment"></a>Válogatott környezet használata

A következőkkel kezdheti az egyik válogatott környezetet: 

* Az _AzureML-Minimal_ környezet ben egy minimális csomagkészletet, amely lehetővé teszi a futtatáskövetés és az eszközök feltöltése. Használhatja, mint a kiindulási pont a saját környezetben.

* Az _AzureML-oktatóanyag-környezet_ közös adatelemzési csomagokat tartalmaz. Ezek a csomagok közé tartozik a Scikit-Learn, a Pandák, a Matplotlib és az azureml-sdk csomagok nagyobb készlete.

A válogatott környezeteket gyorsítótárazott Docker-rendszerképek támasztják alá. Ez a háttér csökkenti a futtatási előkészítési költséget.

A `Environment.get` módszerrel választhatja ki az egyik válogatott környezetet:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

A válogatott környezeteket és csomagjaikat a következő kód dal sorathatja fel:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Ne indítsa el a saját _AzureML_ környezet nevét az AzureML-előtaggal. Ez az előtag a kurátori környezetekben van fenntartva.

### <a name="instantiate-an-environment-object"></a>Környezeti objektum példányosítása

Környezet manuális létrehozásához importálja az `Environment` osztályt az SDK-ból. Ezután a következő kód segítségével példányosítani egy környezeti objektumot.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Conda- és pip specifikációs fájlok használata

Környezetet conda specifikációból vagy pip-követelmények fájlból is létrehozhat. Használja [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) a módszert [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) vagy a módszert. A metódus argumentumban adja meg a környezet nevét és a kívánt fájl elérési útját.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Meglévő Conda-környezetek használata

Ha a helyi számítógépen van egy meglévő Conda-környezet, akkor a szolgáltatás segítségével létrehozhat egy környezeti objektumot. Ezzel a stratégiával újra felhasználhatja a helyi interaktív környezetet távoli futtatásokon.

A következő kód létrehoz egy környezeti objektumot `mycondaenv`a meglévő Conda környezetből. A [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) módszert használja.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Környezetek automatikus létrehozása

Automatikusan hozzon létre egy környezetet egy betanítási futtatás elküldésével. Küldje el a `submit()` futtatást a metódus használatával. Beküldéskor az új környezet létrehozása több percet is igénybe vehet. A létrehozás időtartama a szükséges függőségek méretétől függ. 

Ha a futtatás elküldése előtt nem ad meg környezetet a futtatási konfigurációban, akkor egy alapértelmezett környezet jön létre.

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

Hasonlóképpen, ha egy [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objektumot használ a betanításhoz, közvetlenül elküldheti a becslőpéldányt futtatásként, környezet megadása nélkül. Az `Estimator` objektum már beágyazi a környezetet és a számítási célt.

## <a name="add-packages-to-an-environment"></a>Csomagok hozzáadása környezethez

A Conda, pip vagy private wheel files használatával csomagokat adhat a környezethez. Adja meg az egyes [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) csomagfüggőségeket az osztály használatával. Adja hozzá a környezethez. `PythonSection`

### <a name="conda-and-pip-packages"></a>Conda és pip csomagok

Ha egy csomag elérhető egy Conda csomagtárban, akkor azt javasoljuk, hogy a Pip telepítése helyett a Conda telepítését használja. A Conda csomagok általában előre elkészített bináris fájlokkal rendelkeznek, amelyek megbízhatóbbá teszik a telepítést.

A következő példa hozzáadódik a környezethez. Hozzáteszi, verzió 0.21.3 a `scikit-learn`. Azt is `pillow` hozzáteszi, `myenv`a csomag, . A példa [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) a módszert, illetve a [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) módszert használja.

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

>[!IMPORTANT]
> Ha ugyanazt a környezetdefiníciót használja egy másik futtatáshoz, az Azure Machine Learning szolgáltatás újrafelhasználja a környezet gyorsítótárazott lemezképét. Ha például ```numpy```nem rögzített csomagfüggőséggel rendelkező környezetet hoz létre, akkor az adott környezet a _környezet létrehozásakor_telepített csomagverziót fogja használni. Is, minden jövőbeli környezetben egyező felbontású továbbra is a régi verziót. További információ: [Környezetépítés, gyorsítótárazás és újrafelhasználás.](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)

### <a name="private-wheel-files"></a>Privát kerékfájlok

A privát pipkerék-fájlokat úgy használhatja, hogy először feltölti őket a munkaterületi tárhelyre. Statikus [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) módszerrel töltheti fel őket. Ezután rögzítse a tárolási URL-címet, és adja át az URL-t a `add_pip_package()` metódusnak.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Környezetek kezelése

A környezeteket kezelheti, hogy frissíthesse, nyomon követhesse és újra felhasználhassa őket a számítási célok között és a munkaterület más felhasználóival.

### <a name="register-environments"></a>Környezetek regisztrálása

A környezet automatikusan regisztrálva lesz a munkaterületen, amikor egy fut, vagy üzembe helyez egy webszolgáltatás. A környezetet manuálisan is [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) regisztrálhatja a módszer rel. Ez a művelet a környezetet egy olyan entitássá alakítja, amely et a felhőben nyomon követik és verziószámba adják. Az entitás megosztható a munkaterület felhasználói között.

A következő kód `myenv` regisztrálja `ws` a környezetet a munkaterületre.

```python
myenv.register(workspace=ws)
```

Amikor először használja a környezetet a betanítás vagy üzembe helyezés során, a munkaterület regisztrálva van. Ezután a számítási célpontra épül és telepítve van. A szolgáltatás gyorsítótárazza a környezeteket. A gyorsítótárazott környezet újrafelhasználása sokkal kevesebb időt vesz igénybe, mint egy új vagy frissített szolgáltatás használata.

### <a name="get-existing-environments"></a>Meglévő környezetek beszereznie

Az `Environment` osztály olyan módszereket kínál, amelyek lehetővé teszik a munkaterület meglévő környezetei nek beolvasását. A környezeteket név, lista vagy egy adott betanítási futtatás alapján is lekérheti. Ez az információ hibaelhárításhoz, naplózáshoz és reprodukálhatósághoz hasznos.

#### <a name="view-a-list-of-environments"></a>Környezetek listájának megtekintése

Tekintse meg a környezeteket a [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) munkaterületen az osztály használatával. Ezután válassza ki az újrafelhasználni kívánt környezetet.

#### <a name="get-an-environment-by-name"></a>Környezet beszerezni e nevét

Egy adott környezetet is beszerezhet név és verzió szerint. A következő kód [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) a módszer `1` rel `myenv` a `ws` környezet munkaterületen lévő verziójának beolvasásához használja.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Futásspecifikus környezet betanítása

A betanítás befejezése után egy adott futtatáshoz használt környezet [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) leképezéséhez használja az osztályban lévő `Run` módszert.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Meglévő környezet frissítése

Tegyük fel, hogy módosítja a meglévő környezetben, például egy Python-csomag hozzáadásával. Ezután létrejön a környezet új verziója, amikor beküld egy futtatást, telepít egy modellt, vagy manuálisan regisztrálja a környezetet. A verziószámozás lehetővé teszi a környezet időbeli változásainak megtekintését.

Python-csomag verziójának frissítéséhez egy meglévő környezetben adja meg a csomag verziószámát. Ha nem használja a pontos verziószámot, majd az Azure Machine Learning újra felhasználja a meglévő környezetet az eredeti csomagverziókkal.

### <a name="debug-the-image-build"></a>Hibakeresés a kép build

A következő példa [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) a módszer segítségével manuálisan hozzon létre egy környezetet, mint egy Docker-rendszerkép. Figyeli a kimeneti naplók a rendszerkép [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)build segítségével . A beépített lemezkép ezután megjelenik a munkaterület Azure Container Registry példányában. Ez az információ hasznos a hibakereséshez.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Docker engedélyezése

 Az [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) Azure Machine `Environment` Learning-osztály lehetővé teszi, hogy finoman testre szabhatja és szabályozhatja a vendég operációs rendszer, amelyen futtatja a betanítást.

A Docker engedélyezésekor a szolgáltatás egy Docker-rendszerképet hoz létre. Emellett létrehoz egy Python-környezetet, amely a Docker-tárolón belüli specifikációkat használja. Ez a funkció további elkülönítést és reprodukálhatóságot biztosít a betanítási futtatásokhoz.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Alapértelmezés szerint az újonnan létrehozott Docker-rendszerkép megjelenik a munkaterülethez társított tárolóbeállításjegyzékben.  A tárház neve *azureml/azureml_\<uuid\>* formában áll. A név egyedi azonosító *(uuid)* része a környezeti konfigurációból kiszámított kivonatnak felel meg. Ez a levelezés lehetővé teszi a szolgáltatás számára annak meghatározását, hogy az adott környezethez már létezik-e képkép újrahasználatra.

Ezenkívül a szolgáltatás automatikusan használja az Ubuntu Linux-alapú [alapképek egyikét.](https://github.com/Azure/AzureML-Containers) Telepíti a megadott Python-csomagokat. Az alapkép CPU- és GPU-verzióval rendelkezik. Az Azure Machine Learning automatikusan észleli, hogy melyik verziót kell használni.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Egyéni Docker-fájlt is megadhat. Legegyszerűbb en az Azure Machine Learning alaplemezlespézképeinek egyikéről a Docker ```FROM``` paranccsal, majd adja hozzá saját egyéni lépéseit. Ezt a módszert akkor használja, ha nem Python-csomagokfüggőségként való telepítéséhez kell telepítenie.

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>Felhasználó által felügyelt függőségek használata

Bizonyos esetekben az egyéni alaprendszerkép már tartalmazhat egy Python-környezetet a használni kívánt csomagokkal.

Alapértelmezés szerint az Azure Machine Learning szolgáltatás létrehoz egy Conda-környezetet a megadott függőségekkel, és az alaplemezképre telepített Python-kódtárak használata helyett ebben a környezetben hajtja végre a futtatást. 

A saját telepített csomagok használatához állítsa `Environment.python.user_managed_dependencies = True`be a paramétert. Győződjön meg arról, hogy az alaprendszerkép tartalmaz egy Python-értelmező, és a csomagokat a betanítási parancsfájl igényeinek.

Például futtatni egy alap Miniconda környezetben, amely NumPy csomag telepítve van, először adja meg a Dockerfile egy lépéssel a csomag telepítéséhez. Ezután állítsa a felhasználó `True`által felügyelt függőségeket a rendszerre. 

A `Environment.python.interpreter_path` változó beállításával megadhat egy útvonalat egy adott Python-értelmezőhöz a lemezképen belül.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>Környezetek használata képzéshez

Egy betanítási futtatás elküldéséhez kombinálnia kell a környezetet, a [számítási célt](concept-compute-target.md)és a betanítási Python-parancsfájlt egy futtatási konfigurációba. Ez a konfiguráció egy burkoló objektum, amely fut ok-küldésre szolgál.

Beküldéskor egy betanítási sorozat, az új környezet létrehozása több percet is igénybe vehet. Az időtartam a szükséges függőségek méretétől függ. A környezeteket a szolgáltatás gyorsítótárazza. Mindaddig tehát, amíg a környezetdefiníció változatlan marad, a teljes beállítási idő csak egyszer merül fel.

A következő helyi parancsfájl futtatása [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) példa mutatja, hogy hol szeretné használni, mint a burkoló objektum.

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
> A futtatási előzmények letiltásához vagy pillanatképek futtatásához használja a beállítását a területen. `ScriptRunConfig.run_config.history`

Ha nem adja meg a környezetet a futtatási konfigurációban, akkor a szolgáltatás létrehoz egy alapértelmezett környezetet a futtatás elküldésekor.

### <a name="use-an-estimator-for-training"></a>Estimator használata képzéshez

Ha [bevezetőt](how-to-train-ml-models.md) használ az edzéshez, akkor közvetlenül is elküldheti a becslési példányt. Már magában foglalja a környezetet és a számítási célt.

A következő kód egy egycsomópontos betanítási futtatás becslését használja. Egy `scikit-learn` modell távoli számítási rendszerén fut. Feltételezi, hogy korábban létrehozott egy számítási `compute_target`célobjektumot , `ds`és egy adattárobjektumot, .

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

## <a name="use-environments-for-web-service-deployment"></a>Környezetek használata a webszolgáltatások telepítéséhez

A környezeteket a modell webszolgáltatásként történő üzembe helyezésekor használhatja. Ez a funkció reprodukálható, összekapcsolt munkafolyamatot tesz lehetővé. Ebben a munkafolyamatban betaníthatja, tesztelheti és üzembe helyezheti a modellt ugyanazon kódtárak használatával a betanítási számításban és a következtetési számításban is.

Webszolgáltatás üzembe helyezéséhez egyesítse a környezetet, a számítási következtetéseket, a [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)pontozási parancsfájlt és a regisztrált modellt a központi telepítési objektumban. További információ: [Modellek üzembe helyezése és helye.](how-to-deploy-and-where.md)

Ebben a példában tegyük fel, hogy elvégzett egy betanítási futtatást. Most szeretné üzembe helyezni, hogy a modell az Azure Container instances. A webszolgáltatás létrehozásakor a modell és a pontozási fájlok a rendszerképhez vannak csatlakoztatva, és az Azure Machine Learning-következtetések verem hozzáadódik a rendszerképhez.

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

## <a name="example-notebooks"></a>Példa jegyzetfüzetekre

Ez [a példa notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) kibontja a jelen cikkben bemutatott fogalmak és módszerek.

[Egy modell üzembe helyezése egy egyéni Docker-alaprendszerkép használatával](how-to-deploy-custom-docker-image.md) bemutatja, hogyan telepíthet egy modellt egy egyéni Docker alaprendszerkép használatával.

Ez [a példajegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) bemutatja, hogyan telepíthet egy Spark-modellt webszolgáltatásként.

## <a name="create-and-manage-environments-with-the-cli"></a>Környezetek létrehozása és kezelése a CLI-vel

Az [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) tükrözi a Python SDK legtöbb funkcióját. Segítségével környezeteket hozhat létre és kezelhet. Az ebben a szakaszban tárgyalt parancsok bemutatják az alapvető funkciókat.

A következő parancs felállványolja a fájlokat egy alapértelmezett környezeti definícióhoz a megadott könyvtárban. Ezek a fájlok JSON fájlok. Úgy működnek, mint a megfelelő osztály az SDK-ban. A fájlok segítségével egyéni beállításokkal rendelkező új környezeteket hozhat létre. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Futtassa a következő parancsot egy környezet adott könyvtárból történő regisztrálásához.

```azurecli-interactive
az ml environment register -d myenvdir
```

Futtassa a következő parancsot az összes regisztrált környezet listázásához.

```azurecli-interactive
az ml environment list
```

Töltsön le egy regisztrált környezetet a következő paranccsal.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>További lépések

* Ha felügyelt számítási célt szeretne használni egy modell betanításához, olvassa el [az Oktatóanyag: Modell betanítása.](tutorial-train-models-with-aml.md)
* Miután betanított modellt, ismerje meg, [hogyan és hol kell telepíteni modelleket.](how-to-deploy-and-where.md)
* Az [ `Environment` osztály SDK-hivatkozásának](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)megtekintése .
* A jelen cikkben ismertetett fogalmakról és módszerekről a [példajegyzetfüzetben olvashat bővebben.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)
