---
title: Modellek üzembe helyezése egyéni Docker-lemezképpel
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az egyéni Docker-alaprendszerképet az Azure Machine Learning-modellek üzembe helyezésekor. Bár az Azure Machine Learning alapértelmezett alaplemezképet biztosít az Ön számára, használhatja a saját alaplemezképet is.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/16/2020
ms.openlocfilehash: a237beb72e35a236e353c58db520a8d611fdfdcd
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618003"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Modell üzembe helyezése egyéni Docker-alaprendszerkép használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan használhatja az egyéni Docker-alaprendszerképet az Azure Machine Learning betanított modellek üzembe helyezésekor.

Amikor egy betanított modellt telepít egy webszolgáltatásra vagy ioT Edge-eszközre, létrejön egy csomag, amely egy webkiszolgálót tartalmaz a bejövő kérelmek kezeléséhez.

Az Azure Machine Learning egy alapértelmezett Docker alaprendszerképet biztosít, így nem kell aggódnia a létrehozása miatt. Azure Machine __Learning-környezetek__ segítségével is választhat ki egy adott alaplemezképet, vagy egy egyéni, amely et biztosít.

Az alaplemezkép a központi telepítéshez létrehozott lemezkép létrehozásakor kiindulási pontként szolgál. Ez biztosítja az alapul szolgáló operációs rendszer és az összetevők. A központi telepítési folyamat ezután további összetevőket, például a modellt, a conda környezetet és más eszközöket ad hozzá a lemezképhez a rendszerkép üzembe helyezése előtt.

Általában hozzon létre egy egyéni alaprendszerképet, ha a Docker használatával szeretné kezelni a függőségeket, szigorúbban szabályozhatja az összetevő-verziókat, vagy időt takaríthat meg a telepítés során. Előfordulhat például, hogy a Python, a Conda vagy más összetevő egy adott verzióján szeretne szabványosítani. Előfordulhat, hogy a modell által igényelt szoftvert is telepíteni szeretné, ahol a telepítési folyamat hosszú időt vesz igénybe. A szoftver telepítése az alaplemezkép létrehozásakor azt jelenti, hogy nem kell telepítenie az egyes központi telepítésekhez.

> [!IMPORTANT]
> Modell telepítésekor nem bírálhatja felül az alapvető összetevőket, például a webkiszolgálót vagy az IoT Edge-összetevőket. Ezek az összetevők a Microsoft által tesztelt és támogatott ismert munkakörnyezetet biztosítanak.

> [!WARNING]
> Előfordulhat, hogy a Microsoft nem tud segíteni az egyéni lemezképek által okozott problémák elhárításában. Ha problémákat tapasztal, előfordulhat, hogy a rendszer az alapértelmezett lemezképet vagy a Microsoft által biztosított képek egyikét kéri annak megállapítására, hogy a probléma a képre jellemző-e.

Ez a dokumentum két részre van osztva:

* Hozzon létre egy egyéni alaprendszerképet: Tájékoztatást nyújt a rendszergazdák nak és a DevOps-nak az egyéni lemezkép létrehozásáról és az Azure Container Registry hitelesítésének konfigurálásáról az Azure CLI és a Machine Learning CLI használatával.
* Modell üzembe helyezése egyéni alaplemezkép használatával: Tájékoztatást nyújt az adatszakértők és a DevOps / ML-mérnökök egyéni lemezképek használatával kapcsolatban a Python SDK-ból vagy ml CLI-ből betanított modell üzembe helyezésekor.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning munkacsoport. További információt a [Munkaterület létrehozása](how-to-manage-workspace.md) című cikkben talál.
* Az [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Az [Azure Machine Learning CLI-bővítménye.](reference-azure-machine-learning-cli.md)
* Egy [Azure Container Registry](/azure/container-registry) vagy más Docker-beállításjegyzék, amely elérhető az interneten.
* A jelen dokumentum lépései feltételezik, hogy ismeri a __következtetéskonfigurációs__ objektum létrehozását és használatát a modell központi telepítésének részeként. További információt a Hol és hogyan "Felkészülés az üzembe [helyezésre"](how-to-deploy-and-where.md#prepare-to-deploy)című részében talál.

## <a name="create-a-custom-base-image"></a>Egyéni alapkép létrehozása

Ebben a szakaszban található információk feltételezik, hogy egy Azure Container Registry docker-rendszerképek tárolására használja. Az alábbi ellenőrzőlista használata az Azure Machine Learning egyéni lemezképeinek létrehozásához:

* Fogja használni az Azure Machine Learning-munkaterülethez létrehozott Azure Container Registry- t vagy egy önálló Azure Container Registry-t?

    Ha a __munkaterület tárolóbeállításjegyzékében__tárolt lemezképeket használja, nem kell hitelesítenie magát a beállításjegyzékben. A hitelesítést a munkaterület kezeli.

    > [!WARNING]
    > Az Azure Container Registry a munkaterület __jön létre az első alkalommal, amikor betanítása vagy üzembe helyezése egy modell__ a munkaterület használatával. Ha létrehozott egy új munkaterületet, de nem tanított be, vagy nem hozott létre egy modellt, nem létezik Azure Container Registry a munkaterülethez.

    Az Azure Container Registry nevének beolvasásáról a munkaterülethez a tároló [névének beolvasása](#getname) című témakörben olvashat.

    Önálló __tárolóbeállításjegyzékben__tárolt lemezképek használatakor olyan egyszerű szolgáltatásszolgáltatást kell konfigurálnia, amely legalább olvasási hozzáféréssel rendelkezik. Ezután adja meg a szolgáltatás egyszerű azonosítóját (felhasználónevét) és jelszavát bárkinek, aki a beállításjegyzékből származó lemezképeket használ. A kivétel az, ha a tároló beállításjegyzék nyilvánosan hozzáférhetővé teszi.

    A privát Azure Container Registry létrehozásáról a [Privát tároló beállításjegyzékének létrehozása című](/azure/container-registry/container-registry-get-started-azure-cli)témakörben talál.

    A szolgáltatásnévi tagok Azure Container Registry használatával kapcsolatos további tudnivalókért olvassa el az [Azure Container Registry hitelesítésegyszerűkkel című témakört.](/azure/container-registry/container-registry-auth-service-principal)

* Azure Container Registry és a rendszerkép adatai: Adja meg a lemezkép nevét, hogy bárki, akinek szüksége van a használatához. Például egy `myimage`nevű , nevű beállításjegyzékben tárolt `myregistry`lemezképre hivatkozik, mint `myregistry.azurecr.io/myimage` amikor a lemezképet modelltelepítéshez használja.

* Rendszerképre vonatkozó követelmények: Az Azure Machine Learning csak a következő szoftvereket biztosító Docker-lemezképeket támogatja:

    * Ubuntu 16.04 vagy nagyobb.
    * Conda 4.5.# vagy nagyobb.
    * Python 3.5.# vagy 3.6.#.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Tárolóbeállításjegyzék adatainak beszereznie

Ebben a szakaszban megtudhatja, hogyan szerezheti be az Azure Machine Learning-munkaterülethez az Azure Container-beállításjegyzék nevét.

> [!WARNING]
> Az Azure Container Registry a munkaterület __jön létre az első alkalommal, amikor betanítása vagy üzembe helyezése egy modell__ a munkaterület használatával. Ha létrehozott egy új munkaterületet, de nem tanított be, vagy nem hozott létre egy modellt, nem létezik Azure Container Registry a munkaterülethez.

Ha már betanított vagy üzembe helyezett modellek et az Azure Machine Learning használatával, egy tároló beállításjegyzék jött létre a munkaterületre. A tárolóbeállításjegyzék nevének megkereséséhez kövesse az alábbi lépéseket:

1. Nyisson meg egy új rendszerhéjat vagy parancssort, és a következő paranccsal hitelesítse magát az Azure-előfizetésében:

    ```azurecli-interactive
    az login
    ```

    Kövesse az utasításokat az előfizetés hitelesítéséhez.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. A következő paranccsal listázza a munkaterület tárolóbeállításjegyzékét. Cserélje `<myworkspace>` le az Azure Machine Learning munkaterületének nevét. Cserélje `<resourcegroup>` le a munkaterületet tartalmazó Azure-erőforráscsoportra:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    A visszaadott információ hasonló a következő szöveghez:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Az `<registry_name>` érték a munkaterület Azure Container Registry neve.

### <a name="build-a-custom-base-image"></a>Egyéni alapkép létrehozása

Ebben a szakaszban az egyéni Docker-rendszerkép létrehozása az Azure Container Registry lépései.

1. Hozzon létre egy `Dockerfile`új szövegfájlt , és a következő szöveget használja tartalomként:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Egy rendszerhéjból vagy parancssorból a következőkkel hitelesítheti magát az Azure Container Registry. Cserélje `<registry_name>` le a lemezkép tárolására kívánt tárolóbeállítás-jegyzék nevére:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. A Docker-fájl feltöltéséhez és létrehozásához használja a következő parancsot. Cserélje `<registry_name>` le annak a tárolóbeállítás-beállításjegyzéknek a nevére, amelyben a lemezképet tárolni szeretné:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > Ebben a példában `:v1` a rendszer egy címke van alkalmazva a képre. Ha nincs megadva címke, `:latest` a program alkalmazza a címke alkalmazását.

    A létrehozási folyamat során az adatok a parancssorba való visszautasításra továbbítódnak. Ha a build sikeres, a következő höz hasonló üzenet jelenik meg:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Az Azure Container-beállításjegyzékkel készített lemezképek létrehozásáról további információt a [Tárolórendszerkép létrehozása és futtatása az Azure Container Registry Tasks használatával című](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli) témakörben talál.

A meglévő lemezképek Azure Container Registry rendszerbe való feltöltésével kapcsolatos további tudnivalókért olvassa el [az első rendszerkép leküldése egy privát Docker-tároló beállításjegyzékébe című témakört.](/azure/container-registry/container-registry-get-started-docker-cli)

## <a name="use-a-custom-base-image"></a>Egyéni alapkép használata

Egyéni lemezkép használatához a következő adatokra van szükség:

* A __kép neve__. Például `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` a Microsoft által biztosított alapvető Docker-lemezkép elérési útja.

    > [!IMPORTANT]
    > A létrehozott egyéni képek esetében mindenképpen adja meg a képhez használt címkéket. Ha például a képet egy adott címkével `:v1`hozták létre, például . Ha a kép létrehozásakor nem használt egy adott `:latest` címkét, a rendszer egy címke lett alkalmazva.

* Ha a rendszerkép __magántárházban__található, a következő adatokra van szüksége:

    * A rendszerleíró adatbázis __címe__. Például: `myregistry.azureecr.io`.
    * Egyszerű __szolgáltatásfelhasználónév__ és __jelszó,__ amely olvasási hozzáféréssel rendelkezik a beállításjegyzékhez.

    Ha nem rendelkezik ezzel az információval, forduljon a rendszerképet tartalmazó Azure Container Registry rendszergazdájához.

### <a name="publicly-available-base-images"></a>Nyilvánosan elérhető alapképek

A Microsoft számos docker-lemezképet biztosít egy nyilvánosan elérhető tárházban, amely az ebben a szakaszban ismertetett lépésekkel használható:

| Kép | Leírás |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Egyszerű lemezkép az Azure Machine Learninghez |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | ONNX futásidőt tartalmaz a CPU-következtetésekhez |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Tartalmazza az ONNX futásidőt és a CUDA GPU-t |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | ONNX futásidőt és TensorRT GPU-t tartalmaz |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | ONNX futásidejű t és OpenVINO-t tartalmaz a Movidius<sup> </sup> <sup>TM</sup> MyriadX VVP-n alapuló Intel Vision Accelerator designhoz |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | ONNX futásidőt és OpenVINO-t tartalmaz Intel<sup> </sup> Movidius<sup>TM</sup> USB stick-hez |

Az ONNX-futásidejű alaplemezképekről további információt a GitHub-tárházban található [ONNX Runtime dockerfile szakaszban](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) talál.

> [!TIP]
> Mivel ezek a képek nyilvánosan elérhetők, használatuk során nem kell megadnia a címet, a felhasználónevet vagy a jelszót.

További információ: [Azure Machine Learning-tárolók.](https://github.com/Azure/AzureML-Containers)

> [!TIP]
>__Ha a modell be van tanítva az Azure Machine Learning Compute,__ az Azure Machine Learning SDK __1.0.22-es vagy újabb verziójával,__ egy lemezkép jön létre a betanítás során. A kép nevének felderítéséhez `run.properties["AzureML.DerivedImageName"]`használja a használatát. A következő példa bemutatja, hogyan kell használni ezt a képet:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Lemezkép használata az Azure Machine Learning SDK-val

Ha az **Azure Container-beállításjegyzékben**tárolt lemezképet vagy **nyilvánosan elérhető tárolóbeállítás-nyilvántartást**szeretne használni, állítsa be a következő [környezeti](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) attribútumokat:

+ `docker.enabled=True`
+ `docker.base_image`: Állítsa be a rendszerleíró adatbázist és a lemezkép elérési útját.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Ha olyan __magántároló-beállításjegyzékből__ szeretne lemezképet használni, amely `docker.base_image_registry` nem a munkaterületen található, meg kell adnia a tárház címét, valamint a felhasználónevet és a jelszót:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

Hozzá kell adnia azureml-defaults verzióval >= 1.0.45 pip függőség. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat. Azt is be kell állítania inferencing_stack_version tulajdonság a környezetben a "legújabb", ez telepíti az egyes apt csomagok szükséges webszolgáltatás. 

A környezet meghatározása után használja azt egy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) objektummal a következtetési környezet meghatározásához, amelyben a modell és a webszolgáltatás futni fog.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Ezen a ponton folytathatja az üzembe helyezést. A következő kódrészlet például helyileg telepítegy webszolgáltatást a következtetés konfigurációja és az egyéni lemezkép használatával:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

A központi telepítésről további információt a [Modellek üzembe helyezése az Azure Machine Learning szolgáltatással](how-to-deploy-and-where.md)című témakörben talál.

A Python-környezet testreszabásáról további információt a [Környezetek létrehozása és kezelése betanításhoz és üzembe helyezéshez című témakörben talál.](how-to-use-environments.md) 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Kép használata a Machine Learning CLI-vel

> [!IMPORTANT]
> Jelenleg a Machine Learning CLI használhatja az Azure Container Registry lemezképek a munkaterület vagy a nyilvánosan elérhető adattárak. Nem használhatja az önálló magánnyilvántartásokból származó képeket.

Mielőtt egy modellt a Machine Learning CLI használatával telepítene, hozzon létre egy [olyan környezetet,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) amely az egyéni lemezképet használja. Ezután hozzon létre egy olyan konfigurációs fájlt, amely a környezetre hivatkozik. A környezetet közvetlenül a következtetés konfigurációs fájljában is megadhatja. A következő JSON-dokumentum bemutatja, hogyan hivatkozzon egy lemezképre egy nyilvános tároló beállításjegyzékében. Ebben a példában a környezet a következő szövegközi:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Ez a fájl `az ml model deploy` a parancs. A `--ic` paraméter a következtetés konfigurációs fájljának megadására szolgál.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

A m.l.- cli használatával modell üzembe helyezéséről az [Azure Machine Learning-cikk CLI-bővítmény](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) "modellregisztráció, profilkészítés és üzembe helyezés" című szakaszában olvashat bővebben.

## <a name="next-steps"></a>További lépések

* További információ a [Telepíthető helyről és ezek módjáról.](how-to-deploy-and-where.md)
* Ismerje meg, hogyan [taníthat be és helyezhet üzembe gépi tanulási modelleket az Azure Pipelines használatával.](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)
