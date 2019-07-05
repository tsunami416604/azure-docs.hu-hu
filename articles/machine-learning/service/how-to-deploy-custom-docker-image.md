---
title: Egyéni Docker-rendszerkép használata modell üzembe helyezése
titleSuffix: Azure Machine Learning service
description: Útmutató egyéni Docker-rendszerkép használata az Azure Machine Learning szolgáltatás modellek üzembe helyezésekor. Betanított modell üzembe helyezésekor, egy Docker-rendszerképet gazdagép a lemezképet, webkiszolgáló és a szolgáltatás futtatásához szükséges más összetevők jön létre. Azure Machine Learning szolgáltatáshoz biztosít egy alapértelmezett lemezkép, miközben használhatja a saját rendszerképét is.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/05/2019
ms.openlocfilehash: 29fdb200075a5b5843944a7a890cc2f8ad61f1ee
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543858"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>Modell üzembe helyezése egy egyéni Docker-rendszerkép használata

Ismerje meg, hogyan lehet egyéni Docker-rendszerkép használata az Azure Machine Learning szolgáltatással betanított modellek üzembe helyezésekor.

Amikor telepít egy webszolgáltatás vagy az IoT Edge-eszköz betanított modell, egy Docker-rendszerképet jön létre. Ez a rendszerkép tartalmazza a modellt, conda-környezet és a modell használatához szükséges eszközök. A webkiszolgáló egy webszolgáltatás, valamint az Azure IoT Hub használatához szükséges összetevő telepítésekor a bejövő kérések kezelésére is tartalmaz.

Az Azure Machine Learning szolgáltatás biztosít egy alapértelmezett Docker-rendszerképet, így nem kell aggódnia, hozzon létre egyet. Is használhatja az Ön által létrehozott, egyéni rendszerkép egy _alaplemezkép_. Alapképet lemezkép központi telepítés létrehozásakor a kiindulási pontként szolgál. Az alapul szolgáló operációs rendszer és összetevők biztosít. A telepítési folyamat további összetevők, például a modell, conda-környezet és más eszközök, majd hozzáadja a rendszerkép üzembe helyezése előtt.

Általában egy egyéni lemezkép készítése, ha meg szeretné összetevő verziók szabályozhatja, vagy üzembe helyezés során időt. Például érdemes egy Python-, Conda vagy más összetevő adott verziójának egységesen ezeket. Érdemes azt is, a modellt, ahol a telepítési folyamat hosszú ideig tart a szükséges szoftverek telepítéséhez. A szoftver telepítése az alaprendszerképet létrehozásakor, az azt jelenti, hogy nem kell telepíteni az egyes központi telepítések.

> [!IMPORTANT]
> Amikor egy modell üzembe helyezésével, alapvető összetevői, például a webalkalmazás-kiszolgáló vagy az IoT Edge-összetevőket nem lehet felülírni. Ezek az összetevők biztosítják, amelyek tesztelése, és támogatja a Microsoft által ismert működő környezethez.

> [!WARNING]
> A Microsoft nem lehet egyéni rendszerkép által okozott problémák elhárításához. Ha problémákat tapasztal, előfordulhat, hogy megkérdezi, hogy az alapértelmezett lemezkép vagy a rendszerképeket a Microsoft biztosít annak ellenőrzéséhez, hogy a probléma csak a rendszerkép használatára.

Ez a dokumentum két szakaszra van osztva:

* Hozzon létre egy egyéni rendszerképet: A rendszergazdák és a fejlesztési és üzemeltetési információkat biztosít egyéni rendszerkép létrehozása és a egy Azure Container Registry használatával, az Azure CLI-vel és a Machine Learning parancssori hitelesítésének konfigurálása.
* Egyéni kép használata: Az Adatszakértők és a fejlesztési és üzemeltetési/MLOps információt nyújt az egyéni lemezképek, a Python SDK-t vagy az ML parancssori felületének betanított modell üzembe helyezésekor.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning szolgáltatás munkacsoporthoz. További információkért lásd: a [hozzon létre egy munkaterületet](setup-create-workspace.md) cikk.
* The Azure Machine Learning SDK. Python SDK-t című szakaszában talál további információt a [hozzon létre egy munkaterületet](setup-create-workspace.md#sdk) cikk.
* A [az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* A [az Azure Machine Learning CLI-bővítmény](reference-azure-machine-learning-cli.md).
* Egy [Azure Container Registry](/azure/container-registry) vagy másik Docker-jegyzék, amely az interneten érhető el.
* A jelen dokumentumban leírt lépések azt feltételezik, hogy Ön ismeri a létrehozásával és használatával egy __következtetésekhez konfigurációs__ objektum modell központi telepítésének részeként. További információ "üzembe helyezés előkészítése" című szakaszában talál [az üzembe helyezés, és hogyan](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-image"></a>Egyéni lemezkép létrehozása

Ebben a szakaszban található információk azt feltételezi, hogy egy Azure Container Registry segítségével Docker-rendszerképek tárolásához. A következő ellenőrzőlista használható tervezése során az Azure Machine Learning szolgáltatás egyéni rendszerképek létrehozásához:

* Használja az Azure Container Registry létrehozása az Azure Machine Learning szolgáltatás munkaterületén vagy egy önálló Azure Container Registry?

    Amikor lemezképek használatával tárolja a __a munkaterület a tárolóregisztrációs adatbázis__, nem kell hitelesíteni a regisztrációs adatbázisba. Hitelesítés a munkaterület kezeli.

    > [!WARNING]
    > A munkaterület az Azure Container Rzegistry van __jön létre, betanítását, vagy a modell üzembe helyezése__ a munkaterület használatával. Ha már létrehozott egy új munkaterületet, de nem betanított vagy létrehozott egy modellt, nem az Azure Container Registry a munkaterületet fog léteznek.

    Lekérésével kapcsolatos információk az Azure Container Registry a munkaterület nevére, tekintse meg a [Get tárolóregisztrációs adatbázis nevét](#getname) című szakaszát.

    Amikor rendszerképekből tárolja egy __önálló tárolóregisztrációs adatbázis__, szüksége lesz egy legalább olvasási hozzáféréssel rendelkezik egyszerű szolgáltatás konfigurálása. Majd adja meg a szolgáltatásnév-Azonosítót (felhasználónév) és a jelszót bárki által használt rendszerképek a beállításjegyzékből. A kivétel az, ha elvégezte a tároló-beállításjegyzék nyilvánosan elérhető-e.

    Egy privát Azure Container Registry létrehozásával kapcsolatos információkért lásd: [hozzon létre egy privát tárolójegyzékben](/azure/container-registry/container-registry-get-started-azure-cli).

    Információ a szolgáltatásnevek használata az Azure Container Registryvel: [Azure Tárolóregisztrációs adatbázis hitelesítési szolgáltatásnevekkel](/azure/container-registry/container-registry-auth-service-principal).

* Az Azure Container Registry- és képfájlok információkat: Adja meg a rendszerkép nevének bárki használja azt. Például nevű kép `myimage`nevű beállításjegyzékben tárolt `myregistry`, mint hivatkozott `myregistry.azurecr.io/myimage` a rendszerkép használata esetén a modell üzembe helyezése

* A rendszerképre vonatkozó követelmények: Az Azure Machine Learning szolgáltatás csak a Docker-rendszerképek, amelyek a következő szoftvereket támogatja:

    * Ubuntu 16.04 vagy nagyobb.
    * Conda 4.5. # vagy nagyobb.
    * Python 3.5-ös verzióját. # vagy 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Tároló-beállításjegyzék-információk lekérése

Ebben a szakaszban megtudhatja, hogyan tehet szert az Azure Container Registry neve az Azure Machine Learning szolgáltatás a munkaterülethez.

> [!WARNING]
> Az Azure Container Registry a munkaterület __jön létre, betanítását, vagy a modell üzembe helyezése__ a munkaterület használatával. Ha már létrehozott egy új munkaterületet, de nem betanított vagy létrehozott egy modellt, nem az Azure Container Registry a munkaterületet fog léteznek.

Ha már betanított vagy üzembe helyezve az Azure Machine Learning szolgáltatás használatával, a munkaterület egy tároló-beállításjegyzéket hoztuk létre. A tárolóregisztrációs adatbázis nevét, használja az alábbi lépéseket:

1. Nyisson meg egy új shell vagy az parancssor és a hitelesítést az Azure-előfizetéshez a következő paranccsal:

    ```azurecli-interactive
    az login
    ```

    Kövesse az utasításokat az előfizetéshez való hitelesítéséhez.

2. A következő paranccsal listázhatja a tároló-beállításjegyzékbe a munkaterület számára. Cserélje le `<myworkspace>` az Azure Machine Learning munkaterület szolgáltatásnévvel. Cserélje le `<resourcegroup>` együtt az Azure-erőforráscsoportot, amely tartalmazza a munkaterülethez:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    A visszaadott információ az alábbi szöveghez hasonlít:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    A `<registry_name>` értéke az Azure Container Registry a munkaterület nevét.

### <a name="build-a-custom-image"></a>Egyéni rendszerkép készítése

Az ebben a szakaszban az útmutatóban egyéni Docker-rendszerkép létrehozása az Azure Container Registry a lépéseket.

1. Hozzon létre egy új szöveges fájlt `Dockerfile`, és használja a következő szöveg tartalma:

    ```text
    FROM ubuntu:16.04

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-4.5.12-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y python=3.6 && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Rendszerhéj vagy a parancssorból használja a következő hitelesítéséhez az Azure Container registrybe. Cserélje le a `<registry_name>` szeretné tárolni a rendszerképet a tároló-beállításjegyzék nevére:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. A docker-fájl feltöltése, és építse fel, használja a következő parancsot. Cserélje le `<registry_name>` szeretné tárolni a rendszerképet a tároló-beállításjegyzék nevére:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    Az összeállítási folyamat során információk adatfolyamként történő vissza a parancssorhoz. Ha a build sikeres, az alábbi szöveghez hasonló üzenetet kapja:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Az Azure Container Registry rendszerképek további információkért lásd: [létrehozása és futtatása egy tárolórendszerképet az Azure Container Registry feladatok használatával](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

További információ a meglévő rendszerképek feltöltése egy Azure Container registrybe: [az első rendszerkép leküldése egy privát Docker-tárolójegyzék](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-image"></a>Egyéni rendszerkép használata

Egyéni rendszerkép használatára, szüksége van a következő információkat:

* A __lemezképnév__. Ha például `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` alapszintű Docker-rendszerkép a Microsoft által biztosított elérési útját.
* Ha a kép egy __privát tárház__, a következő információk szükségesek:

    * A beállításjegyzék __cím__. Például: `myregistry.azureecr.io`.
    * Egyszerű szolgáltatás __felhasználónév__ és __jelszó__ , amely olvasási hozzáféréssel rendelkezik a regisztrációs adatbázisba.

    Ha ezt az információt, a rendszergazda az Azure Container Registry a rendszerképet tartalmazó beszéljenek nem rendelkeznek.

### <a name="publicly-available-images"></a>Nyilvánosan elérhető lemezképek

A Microsoft több docker-rendszerképeket tartalmazza egy nyilvánosan elérhető tárház, amely a jelen szakaszban ismertetett lépések használhatók:

| Image | Leírás |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Az Azure Machine Learning szolgáltatás alapszintű kép |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | Az ONNX-futtatókörnyezet tartalmazza. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | Az ONNX-futtatókörnyezet és a CUDA összetevőket tartalmazza. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | Az ONNX-futtatókörnyezet és TensorRT tartalmazza. |

> [!TIP]
> Mivel ezek a lemezképek nyilvánosan elérhető, nem kell egy címe, felhasználónév vagy jelszó szolgáltatni használja őket.

> [!IMPORTANT]
> Microsoft-lemezképek, amelyek CUDA vagy TensorRT csak a Microsoft Azure-szolgáltatásokra kell használni.

> [!TIP]
>__Ha a modell tanítása az Azure Machine Learning Compute__révén __1.0.22 verzió vagy annál nagyobb__ , az Azure Machine Learning SDK-lemezkép létrehozása betanítás során. Fedezze fel a lemezkép nevét, használja a `run.properties["AzureML.DerivedImageName"]`. A következő példa bemutatja, hogyan használhatja ezt a képet:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Lemezképek használata az Azure Machine Learning SDK-val

Egyéni rendszerkép használatához állítsa a `base_image` tulajdonságát a [következtetésekhez konfigurációs objektum](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) a lemezkép-címére:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

Ez a formátum mindkét a a munkaterületet, és a tároló beállításjegyzékekhez, amelyek nyilvánosan elérhetők az Azure Container Registryben tárolt rendszerképek esetében működik. A következő kód például a Microsoft által biztosított alapértelmezett kép használja:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

A rendszerkép használatához egy __privát tárolójegyzékben__ , amely nem szerepel a munkaterülethez, meg kell adnia a címet, a tárházat és a egy felhasználónevet és jelszót:

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>Egy rendszerkép használata a Machine Learning parancssori felület

> [!IMPORTANT]
> Jelenleg a Machine Learning parancssori rendszerképeit is használhatják az Azure Container Registry a munkaterületet, vagy a nyilvánosan elérhető tárházak számára. A privát beállításjegyzékek önálló képeket nem használható.

Ha üzembe helyezéséhez a Machine Learning parancssori felület használatával, meg kell adnia egy következtetésekhez konfigurációs fájlt, amely az egyéni rendszerkép hivatkozik. A következő JSON-dokumentum való hivatkozáshoz egy nyilvános tároló-beállításjegyzéket egy kép mutatja be:

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

Ez a fájl szolgál a `az ml model deploy` parancsot. A `--ic` paraméterrel adja meg a következtetésekhez konfigurációs fájlt.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Egy modellt az ML parancssori felület használatával történő telepítéséről további információkért lásd: "a modell regisztrálását, a profilkészítési, és a központi telepítés" szakaszában a [CLI-bővítmény az Azure Machine Learning szolgáltatás](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) cikk.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [az üzembe helyezés, és hogyan](how-to-deploy-and-where.md).
* Ismerje meg, hogyan [Train és üzembe helyezése Azure folyamatok használatával a gépi tanulási modellek](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
