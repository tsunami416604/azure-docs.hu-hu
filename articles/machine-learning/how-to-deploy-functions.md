---
title: Ml modellek üzembe helyezése Azure Functions alkalmazásokban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezhet üzembe modelleket Azure Functions alkalmazásokban a Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: 77e23467551df8d72fd999049c490600eff11825
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763640"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Gépi tanulási modell üzembe helyezése Azure Functions (előzetes verzió)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Megtudhatja, hogyan helyezhet üzembe egy modellt Azure Machine Learning alkalmazásból Azure Functions.

> [!IMPORTANT]
> Noha a Azure Machine Learning és az Azure Functions általánosan elérhető, az Machine Learning Service for functions-modell csomagjának lehetősége előzetes verzióban érhető el.

A Azure Machine Learning segítségével Docker-rendszerképeket hozhat létre a képzett gépi tanulási modellekből. Azure Machine Learning mostantól az előzetes verzió funkciójának használatával felépítheti ezeket a gépi tanulási modelleket a Function appsbe, amelyek üzembe helyezhetők [a Azure Functionsban](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információt a [Munkaterület létrehozása](how-to-manage-workspace.md) című cikkben talál.
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-vel.
* A munkaterületen regisztrált, betanított gépi tanulási modell. Ha nem rendelkezik modellel, használja a [képbesorolási oktatóanyagot: a betanítási modell](tutorial-train-models-with-aml.md) betanítása és regisztrálása.

    > [!IMPORTANT]
    > A cikkben szereplő kódrészletek azt feltételezik, hogy a következő változókat állította be:
    >
    > * `ws` – a Azure Machine Learning munkaterülete.
    > * `model` – a regisztrált modell, amelyet telepíteni kíván.
    > * `inference_config` – a modellre vonatkozó következtetési konfiguráció.
    >
    > A változók beállításával kapcsolatos további információkért lásd: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Az üzembe helyezés előkészítése

A telepítés előtt meg kell határoznia, hogy mire van szükség a modell webszolgáltatásként való futtatásához. Az alábbi lista a központi telepítéshez szükséges alapvető elemeket ismerteti:

* Egy __bejegyzési parancsfájl__. Ez a szkript fogadja a kéréseket, a modell használatával szerzi a kérést, és visszaadja az eredményeket.

    > [!IMPORTANT]
    > A bejegyzési parancsfájl a modellre jellemző. meg kell ismernie a bejövő kérelmek adatainak formátumát, a modell által várt adatformátumot, valamint az ügyfeleknek visszaadott adatformátumot.
    >
    > Ha a kérelem adatai olyan formátumban vannak, amely nem használható a modellben, a parancsfájl elfogadható formátumba alakíthatja át. A válasz is át lehet alakítani, mielőtt visszatért az ügyfélhez.
    >
    > Alapértelmezés szerint a függvények csomagolásakor a rendszer a bemenetet szövegként kezeli. Ha érdekli a bemenet (például blob-eseményindítók) nyers bájtjainak használata, a [AMLRequest használatával fogadja el a nyers adatokat](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data).


* **Függőségek**, például segítő parancsfájlok vagy Python/Conda csomagok, amelyek a belépési parancsfájl vagy modell futtatásához szükségesek

Ezek az entitások egy __következtetési konfigurációba__vannak ágyazva. A következtetési konfiguráció a bejegyzési parancsfájlra és más függőségekre hivatkozik.

> [!IMPORTANT]
> Ha Azure Functions-vel való használatra vonatkozó következtetési konfigurációt hoz létre, [környezeti](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) objektumot kell használnia. Vegye figyelembe, hogy ha egyéni környezetet határoz meg, akkor a > = 1.0.45 verzióval rendelkező azureml kell hozzáadnia pip-függőségként. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat. Az alábbi példa bemutatja, hogyan hozható létre egy környezeti objektum, és hogyan használhatja azt egy következtetési konfigurációval:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

További információ a környezetekről: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md).

További információ a konfigurációval kapcsolatban: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

> [!IMPORTANT]
> A függvények telepítésekor nem kell létrehoznia __központi telepítési konfigurációt__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Az SDK előzetes csomagjának telepítése a függvények támogatásához

Azure Functions csomagok létrehozásához telepítenie kell az SDK előzetes csomagját.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>A rendszerkép létrehozása

A Azure Functions rendszerbe állított Docker-rendszerkép létrehozásához használja a [azureml... a. functions. functions. package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) vagy az adott Package függvényt a használni kívánt triggerhez. A következő kódrészlet azt mutatja be, hogyan hozhat létre egy új csomagot a modellből és a következtetések konfigurációjában a blob triggerrel:

> [!NOTE]
> A kódrészlet feltételezi, hogy `model` tartalmaz egy regisztrált modellt, és az `inference_config` tartalmazza a következtetési környezet konfigurációját. További információ: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

`show_output=True`kor megjelenik a Docker-létrehozási folyamat kimenete. A folyamat befejeződése után a rendszerkép a munkaterülethez tartozó Azure Container Registryban lett létrehozva. A rendszerkép felépítése után megjelenik a Azure Container Registry helye. A visszaadott hely formátuma `<acrinstance>.azurecr.io/package@sha256:<hash>`.

> [!NOTE]
> A függvények csomagolása jelenleg támogatja a HTTP-eseményindítókat, a blob-eseményindítókat és a Service Bus-eseményindítókat. További információ az eseményindítókkal kapcsolatban: [Azure functions kötések](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob?tabs=csharp#trigger---blob-name-patterns).

> [!IMPORTANT]
> Mentse a hely adatait, ahogy azt a lemezkép telepítésekor használják.

## <a name="deploy-image-as-a-web-app"></a>Rendszerkép üzembe helyezése webalkalmazásként

1. Használja az alábbi parancsot a rendszerképet tartalmazó Azure Container Registry bejelentkezési hitelesítő adatainak beszerzéséhez. Cserélje le a `<acrinstance>` értéket a korábban `package.location`visszaadott értékre: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    A parancs kimenete a következő JSON-dokumentumhoz hasonló:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Mentse a __Felhasználónév__ és az egyik __jelszó__értékét.

1. Ha még nem rendelkezik erőforráscsoport-vagy app Service-csomaggal a szolgáltatás telepítéséhez, a következő parancsok azt mutatják be, hogyan hozható létre egyszerre:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    Ebben a példában egy _linuxos prémium_ szintű díjszabási szintet (`--sku EP1`) használunk.

    > [!IMPORTANT]
    > A Azure Machine Learning által létrehozott rendszerképek Linux rendszert használnak, ezért a `--is-linux` paramétert kell használnia.

1. A Function alkalmazás létrehozásához használja a következő parancsot. Cserélje le a `<app-name>`t a használni kívánt névre. Cserélje le a `<acrinstance>` és a `<imagename>` értéket a korábban visszaadott `package.location` értékekre:

    ```azurecli-interactive
    az storage account create --name 
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    > [!IMPORTANT]
    > Ezen a ponton a Function alkalmazás létrejött. Mivel azonban nem biztosította a blob triggerhez vagy a hitelesítő adatokhoz tartozó kapcsolódási karakterláncot a képet tartalmazó Azure Container Registryhoz, a Function alkalmazás nem aktív. A következő lépésekben meg kell adnia a kapcsolatok karakterláncát és a tároló-beállításjegyzék hitelesítési adatait. 

1. Hozza létre az triggerként használandó Storage-fiókot, és szerezze be a kapcsolódási karakterláncot.

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name triggerStorage --query connectionString --output tsv
    ```
    Jegyezze fel ezt a kapcsolódási karakterláncot a Function alkalmazás számára történő biztosításhoz. Később fogjuk használni `<triggerConnectionString>`

1. Hozza létre a tárolót a bemenethez és a kimenethez a Storage-fiókban. 

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. A létrehozott tárolóhoz társított címkét a következő parancs használatával kell lekérnie:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Az alábbi `imagetag` jelenik meg a legutóbbi címkén.

1. A következő parancs használatával biztosíthatja a Function alkalmazást a tároló-beállításjegyzék eléréséhez szükséges hitelesítő adatokkal. Cserélje le a `<app-name>`t a használni kívánt névre. Cserélje le a `<acrinstance>` és a `<imagetag>` értéket az előző lépésben az az CLI hívás értékével. Cserélje le a `<username>` és a `<password>`t a korábban beolvasott ACR bejelentkezési adatokkal:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Ez a parancs a következő JSON-dokumentumhoz hasonló adatokat ad vissza:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

Ezen a ponton a Function alkalmazás elkezdi betölteni a rendszerképet.

> [!IMPORTANT]
> A rendszerkép betöltése előtt több percet is igénybe vehet. Az Azure Portalon nyomon követheti a folyamat előrehaladását.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan konfigurálhatja a functions alkalmazást a [functions](/azure/azure-functions/functions-create-function-linux-custom-image) dokumentációjában.
* További információ a blob Storage eseményindítók [Azure Blob Storage-kötésekről](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [A modell üzembe helyezése Azure app Service](how-to-deploy-app-service.md).
* [Webszolgáltatásként üzembe helyezett ML-modell felhasználása](how-to-consume-web-service.md)
* [API-referencia](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)