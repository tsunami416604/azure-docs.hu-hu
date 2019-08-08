---
title: Modellek üzembe helyezése egyéni Docker-lemezképpel
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhat egyéni Docker-rendszerképet a Azure Machine Learning szolgáltatási modelljeinek üzembe helyezése során. A betanított modell telepítésekor a rendszer létrehoz egy Docker-rendszerképet, amely a szolgáltatás futtatásához szükséges lemezképet, webkiszolgálót és egyéb összetevőket tárolja. Míg Azure Machine Learning szolgáltatás alapértelmezett rendszerképet biztosít Önnek, saját rendszerkép is használható.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/11/2019
ms.openlocfilehash: 0025f488f6a9b0af4e05a8bdf3ddf36c089d4d9f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856119"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>Modell üzembe helyezése egyéni Docker-rendszerkép használatával

Megtudhatja, hogyan használhat egyéni Docker-rendszerképet a betanított modellek a Azure Machine Learning szolgáltatással való üzembe helyezése során.

Ha egy webszolgáltatáshoz vagy IoT Edge eszközhöz helyez üzembe egy betanított modellt, a rendszer létrehoz egy Docker-rendszerképet. Ez a rendszerkép tartalmazza a modell, a Conda-környezet és a modell használatához szükséges eszközöket. Továbbá tartalmaz egy webkiszolgálót, amely a beérkező kérelmeket webszolgáltatásként való üzembe helyezéskor kezeli, és az Azure IoT Hub való együttműködéshez szükséges összetevőket.

Azure Machine Learning a szolgáltatás egy alapértelmezett Docker-rendszerképet biztosít, így nem kell aggódnia a létrehozásával kapcsolatban. Használhat egy alaprendszerképként létrehozott egyéni rendszerképet is. Alapszintű rendszerképet kell használni a központi telepítés rendszerképének létrehozásakor. A mögöttes operációs rendszert és összetevőket tartalmazza. Az üzembe helyezési folyamat ezután további összetevőket (például a modellt, a Conda-környezetet és más eszközöket) helyez el a lemezképbe a telepítés előtt.

Általában egyéni rendszerképet kell létrehoznia, ha az összetevő-verziókat szeretné vezérelni, vagy időt takarít meg az üzembe helyezés során. Előfordulhat például, hogy a Python, a Conda vagy más összetevő egy adott verziójára szeretne szabványosítani. Előfordulhat, hogy a modellhez szükséges szoftvert is telepítenie kell, ahol a telepítési folyamat hosszú időt vesz igénybe. Ha az alaprendszerkép létrehozásakor telepíti a szoftvert, azt jelenti, hogy nem kell telepítenie az összes központi telepítéshez.

> [!IMPORTANT]
> Modell telepítésekor nem bírálhatja felül a fő összetevőket, például a webkiszolgálót vagy a IoT Edge összetevőket. Ezek az összetevők olyan ismert munkahelyi környezetet biztosítanak, amelyet a Microsoft tesztelt és támogat.

> [!WARNING]
> Előfordulhat, hogy a Microsoft nem tud segíteni az egyéni rendszerkép által okozott problémák elhárításában. Ha problémákba ütközik, előfordulhat, hogy az alapértelmezett lemezképet vagy a Microsoft által biztosított lemezképek valamelyikét kell használnia annak ellenőrzéséhez, hogy a probléma kifejezetten a lemezképre vonatkozik-e.

Ez a dokumentum két részre oszlik:

* Egyéni rendszerkép létrehozása: Információt nyújt a rendszergazdáknak és a DevOps az Egyéni rendszerképek létrehozásáról és a hitelesítés konfigurálásáról egy Azure Container Registry az Azure CLI és a Machine Learning parancssori felület használatával.
* Egyéni rendszerkép használata: Információt nyújt az adatszakértőknek és a DevOps/MLOps az Egyéni rendszerképek használatával, amikor betanított modellt telepít a Python SDK-ból vagy a ML CLI-ből.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning szolgáltatás munkacsoportja. További információt a [Munkaterület létrehozása](how-to-manage-workspace.md) című cikkben talál.
* A [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* A [az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* A [Azure Machine learning CLI](reference-azure-machine-learning-cli.md)-bővítménye.
* Az interneten elérhető [Azure Container Registry](/azure/container-registry) vagy más Docker-beállításjegyzék.
* A jelen dokumentum lépései azt feltételezik, hogy a modell üzembe helyezésének részeként már ismeri a __következtetési konfigurációs__ objektum létrehozását és használatát. További információ: a telepítésének előkészítése című rész, [ahol a üzembe helyezés és az útmutató](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-image"></a>Egyéni lemezkép létrehozása

Az ebben a szakaszban található információk azt feltételezik, hogy Azure Container Registry használ a Docker-rendszerképek tárolásához. A következő ellenőrzőlista használható egyéni rendszerképek létrehozásához Azure Machine Learning szolgáltatáshoz:

* A Azure Machine Learning szolgáltatás munkaterülethez vagy önálló Azure Container Registryhoz létrehozott Azure Container Registry fogja használni?

    Ha a munkaterülethez tartozó __tároló beállításjegyzékében__tárolt rendszerképeket használ, nem kell hitelesítenie magát a beállításjegyzékben. A hitelesítést a munkaterület kezeli.

    > [!WARNING]
    > A munkaterülethez tartozó Azure Container Rzegistry akkor jön létre, amikor a munkaterülettel __először betanít vagy üzembe helyez egy modellt__ . Ha létrehozott egy új munkaterületet, de nem képzett vagy nem hozott létre modellt, akkor a munkaterülethez nem tartozik Azure Container Registry.

    A munkaterület Azure Container Registry nevének beolvasásával kapcsolatos információkért tekintse meg a jelen cikk [tároló beállításjegyzékének](#getname) beolvasása című szakaszát.

    __Önálló tároló__-beállításjegyzékben tárolt rendszerképek használata esetén konfigurálnia kell egy egyszerű szolgáltatásnevet, amely legalább olvasási hozzáféréssel rendelkezik. Ezután megadhatja az egyszerű szolgáltatás AZONOSÍTÓját (felhasználónevét) és jelszavát, amely a beállításjegyzékből származó lemezképeket használ. Ez alól kivételt képez, ha nyilvánosan elérhetővé teszi a tároló-beállításjegyzéket.

    A privát Azure Container Registry létrehozásával kapcsolatos információkért lásd: [saját tároló beállításjegyzékének létrehozása](/azure/container-registry/container-registry-get-started-azure-cli).

    További információ az egyszerű szolgáltatások Azure Container Registry használatával történő használatáról: [Azure Container Registry hitelesítés az egyszerű szolgáltatásokkal](/azure/container-registry/container-registry-auth-service-principal).

* Azure Container Registry és rendszerkép adatai: Adja meg a rendszerkép nevét bárkinek, akinek használni szeretné. Például egy nevű `myregistry`beállításjegyzékbeli nevű `myimage`rendszerképet a rendszer a rendszerképnek a modell központi `myregistry.azurecr.io/myimage` telepítésére való használatakor hivatkozik.

* Rendszerképekre vonatkozó követelmények: Azure Machine Learning a szolgáltatás csak a következő szoftvereket biztosító Docker-rendszerképeket támogatja:

    * Ubuntu 16,04 vagy újabb.
    * Conda 4.5. # vagy nagyobb.
    * Python 3.5. # vagy 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Tároló beállításjegyzék-adatainak beolvasása

Ebből a szakaszból megtudhatja, hogyan kérheti le a Azure Machine Learning szolgáltatás munkaterülethez tartozó Azure Container Registry nevét.

> [!WARNING]
> A munkaterülethez tartozó Azure Container Registry akkor jön létre, amikor a munkaterületen __először betanít vagy telepít egy modellt__ . Ha létrehozott egy új munkaterületet, de nem képzett vagy nem hozott létre modellt, akkor a munkaterülethez nem tartozik Azure Container Registry.

Ha már betanított vagy telepített modelleket a Azure Machine Learning szolgáltatással, a rendszer létrehoz egy tároló-beállításjegyzéket a munkaterülethez. A tároló-beállításjegyzék nevének megkereséséhez kövesse az alábbi lépéseket:

1. Nyisson meg egy új rendszerhéjt vagy egy parancssort, és az alábbi paranccsal hitelesítheti magát az Azure-előfizetésében:

    ```azurecli-interactive
    az login
    ```

    Az előfizetések hitelesítéséhez kövesse az utasításokat.

2. Használja a következő parancsot a munkaterület tároló-beállításjegyzékének listázásához. Cserélje `<myworkspace>` le a helyére a Azure Machine learning szolgáltatás-munkaterület nevét. Cserélje `<resourcegroup>` le a helyére a munkaterületet tartalmazó Azure-erőforráscsoportot:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    A visszaadott információ az alábbi szöveghez hasonló:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Az `<registry_name>` érték a munkaterület Azure Container Registry neve.

### <a name="build-a-custom-image"></a>Egyéni rendszerkép létrehozása

Az ebben a szakaszban ismertetett lépések végigvezetik az egyéni Docker-rendszerkép létrehozásán a Azure Container Registry.

1. Hozzon létre egy nevű `Dockerfile`új szövegfájlt, és használja a következő szöveget a tartalomként:

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

2. Egy rendszerhéjból vagy parancssorból a következő paranccsal hitelesítheti magát a Azure Container Registry. A `<registry_name>` helyére írja be annak a tároló-beállításjegyzéknek a nevét, amelyben a rendszerképet tárolni szeretné:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. A Docker feltöltéséhez és a létrehozásához használja a következő parancsot. A `<registry_name>` helyére írja be annak a tároló-beállításjegyzéknek a nevét, amelyben a rendszerképet tárolni szeretné:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    A létrehozási folyamat során a rendszer továbbítja az adatokat a parancssorba. Ha a Build sikeres, az alábbihoz hasonló üzenet jelenik meg:

    ```text
    Run ID: cda was successful after 2m56s
    ```

A rendszerképek Azure Container Registry használatával történő létrehozásával kapcsolatos további információkért lásd: [tároló lemezképének létrehozása és futtatása Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli) feladatokkal

A meglévő lemezképek Azure Container Registryra való feltöltésével kapcsolatos további információkért lásd: [az első rendszerkép leküldése egy privát Docker-tároló beállításjegyzékbe](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-image"></a>Egyéni rendszerkép használata

Egyéni rendszerkép használatához a következő információk szükségesek:

* A __rendszerkép neve__. Például `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` a Microsoft által biztosított alapszintű Docker-rendszerkép elérési útja.
* Ha a rendszerkép egy __privát tárházban__található, a következő információkra lesz szüksége:

    * A beállításjegyzék __címe__. Például: `myregistry.azureecr.io`.
    * Egy egyszerű szolgáltatásnév __felhasználóneve__ és __jelszava__ , amely olvasási hozzáféréssel rendelkezik a beállításjegyzékhez.

    Ha nem rendelkezik ezekkel az információkkal, beszéljen a rendszerképet tartalmazó Azure Container Registry rendszergazdájához.

### <a name="publicly-available-images"></a>Nyilvánosan elérhető lemezképek

A Microsoft számos Docker-rendszerképet biztosít egy nyilvánosan elérhető adattáron, amely az ebben a szakaszban ismertetett lépésekkel használható:

| Image | Leírás |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure Machine Learning szolgáltatás alapszintű képe |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | A ONNX futtatókörnyezetet tartalmazza. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | A ONNX Runtime és a CUDA összetevőket tartalmazza. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | ONNX futtatókörnyezetet és TensorRT tartalmaz. |

> [!TIP]
> Mivel ezek a lemezképek nyilvánosan elérhetők, nem kell megadnia a szükséges címeket, felhasználónevet vagy jelszót.

> [!IMPORTANT]
> A CUDA vagy TensorRT használó Microsoft-rendszerképeket csak Microsoft Azure szolgáltatásokban kell használni.

> [!TIP]
>__Ha a modell Azure Machine learning számítási__feladatokra van betanítva, az Azure Machine learning SDK __1.0.22 vagy újabb verziójával__ , a rendszer a betanítás során létrehoz egy rendszerképet. A rendszerkép nevének felderítéséhez használja `run.properties["AzureML.DerivedImageName"]`a következőt:. Az alábbi példa bemutatja, hogyan használhatja ezt a rendszerképet:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Rendszerkép használata az Azure Machine Learning SDK-val

Egyéni rendszerkép használatához állítsa a `base_image` [következtetési konfigurációs objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) tulajdonságát a rendszerkép címeként:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

Ez a formátum a munkaterülethez és a nyilvánosan elérhető tároló-beállításjegyzékek Azure Container Registryban tárolt rendszerképekre is használható. Az alábbi kód például a Microsoft által biztosított alapértelmezett rendszerképet használja:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Ha olyan rendszerképet szeretne használni a __saját tároló__ -beállításjegyzékből, amely nem szerepel a munkaterületén, meg kell adnia a tárház nevét és a felhasználónevet és a jelszót:

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>Rendszerkép használata a Machine Learning parancssori felülettel

> [!IMPORTANT]
> A Machine Learning CLI jelenleg a munkaterülethez vagy a nyilvánosan elérhető adattárakhoz tartozó Azure Container Registry képeket is használhat. Önálló privát beállításjegyzékből származó lemezképeket nem használhat.

Ha a Machine Learning parancssori felület használatával helyez üzembe egy modellt, olyan következtetési konfigurációs fájlt ad meg, amely az egyéni lemezképre hivatkozik. A következő JSON-dokumentum bemutatja, hogyan hivatkozhat egy rendszerképre egy nyilvános tároló beállításjegyzékében:

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

Ezt a fájlt a `az ml model deploy` paranccsal lehet használni. A `--ic` paraméter a következtetési konfigurációs fájl megadására szolgál.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

A modellek ML parancssori felülettel történő üzembe helyezésével kapcsolatos további információkért tekintse meg az [Azure Machine learning Service cikk CLI](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) -bővítményének "modell regisztrálása, profilkészítés és központi telepítése" című szakaszát.

## <a name="next-steps"></a>További lépések

* További információ a [telepítéséről és](how-to-deploy-and-where.md)a módjáról.
* Ismerje meg, hogyan lehet [gépi tanulási modelleket betanítani és üzembe helyezni az Azure-folyamatok használatával](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
