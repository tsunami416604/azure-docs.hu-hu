---
title: Ml-modellek üzembe helyezése az Azure Functions-alkalmazásokban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan helyezhet üzembe egy modellt az Azure Functions alkalmazásba az Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: d03a3d482d147d3bc69354ee09dfe0b187610a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927443"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Gépi tanulási modell üzembe helyezése az Azure Functions szolgáltatásban (előzetes verzió)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan helyezhet üzembe egy modellt az Azure Machine Learning ből függvényalkalmazásként az Azure Functionsben.

> [!IMPORTANT]
> Bár az Azure Machine Learning és az Azure Functions általánosan elérhető, a machine learning szolgáltatás a funkciók hoz egy modellt előzetes verzióban.

Az Azure Machine Learning segítségével Docker-rendszerképeket hozhat létre betanított gépi tanulási modellekből. Az Azure Machine Learning most már rendelkezik az előzetes verziófunkcióval, amelyekkel ezeket a gépi tanulási modelleket függvényalkalmazásokká alakíthatja, amelyek [telepíthetők az Azure Functionsbe.](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információt a [Munkaterület létrehozása](how-to-manage-workspace.md) című cikkben talál.
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* A munkaterületen regisztrált gépi tanulási modell. Ha nem rendelkezik modellel, használja a [Képbesorolás oktatóanyag: vonat modell](tutorial-train-models-with-aml.md) betanításához és regisztrációhoz.

    > [!IMPORTANT]
    > A cikkben szereplő kódrészletek feltételezik, hogy a következő változókat állította be:
    >
    > * `ws`- Az Azure Machine Learning-munkaterület.
    > * `model`- A regisztrált modell, amely et telepíti.
    > * `inference_config`- A következtetés konfigurációja a modell.
    >
    > A változók beállításáról a [Modellek üzembe helyezése az Azure Machine Learning szolgáltatással](how-to-deploy-and-where.md)című témakörben talál további információt.

## <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre

Üzembe helyezés előtt meg kell határoznia, hogy mi szükséges a modell webszolgáltatásként való futtatásához. Az alábbi lista a központi telepítéshez szükséges alapvető elemeket ismerteti:

* Egy __beviteli parancsfájl__. Ez a parancsfájl elfogadja a kérelmeket, a modell használatával pontozza a kérelmet, és visszaadja az eredményeket.

    > [!IMPORTANT]
    > A bejegyzésparancsfájl a modellre jellemző; meg kell értenie a bejövő kérelemadatok formátumát, a modell által várt adatok formátumát és az ügyfeleknek visszaadott adatok formátumát.
    >
    > Ha a kérelem adatok olyan formátumban, amely nem használható a modell, a parancsfájl átalakíthatja azt elfogadható formátumban. Azt is átalakíthatja a választ, mielőtt visszatérne az ügyfélnek.
    >
    > Alapértelmezés szerint a függvények csomagolásakor a program szövegként kezeli a bemenetet. Ha a bemenet nyers bájtjainak (például a Blob-eseményindítóknak) a felhasználásában szeretne vásárolni, az AMLRequest segítségével fogadja el a [nyers adatokat.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data)


* **Függőségek**, például segítő parancsfájlok vagy Python/Conda csomagok szükségesek a bejegyzési parancsfájl vagy modell futtatásához

Ezek az entitások következtetési __konfigurációba__vannak ágyazva. A következtetés konfigurációja a bejegyzésparancsfájlra és más függőségekre hivatkozik.

> [!IMPORTANT]
> Az Azure Functions használatával használható következtetési konfiguráció létrehozásakor egy környezeti objektumot kell [használnia.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) Kérjük, vegye figyelembe, hogy ha egyéni környezetet határoz meg, akkor az azureml-defaults-t a >= 1.0.45 verzióval kell hozzáadnia pip-függőségként. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat. A következő példa bemutatja egy környezeti objektum létrehozását és használatát következtetési konfigurációval:
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

A környezetekkel kapcsolatos további információkért olvassa el a [Környezetek létrehozása és kezelése a betanításhoz és telepítéshez című témakört.](how-to-use-environments.md)

A konfigurációval kapcsolatos további információkért lásd: Modellek telepítése az Azure Machine Learning használatával című [témakörben.](how-to-deploy-and-where.md)

> [!IMPORTANT]
> A Functions szolgáltatásba való telepítéskor nem kell __központi telepítési konfigurációt létrehoznia.__

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Telepítse az SDK előnézeti csomagját a funkciók támogatásához

Az Azure Functions csomagjainak létrehozásához telepítenie kell az SDK előzetes csomag.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>A rendszerkép létrehozása

Az Azure Functionsben üzembe helyezett Docker-rendszerkép létrehozásához használja [az azureml.contrib.functions.package vagy](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) az adott csomagfüggvényt az eseményindítóhoz, amelyet használni szeretne. A következő kódrészlet bemutatja, hogyan hozhat létre új csomagot egy blob-eseményindítóval a modellből és a következtetéskonfigurációból:

> [!NOTE]
> A kódrészlet azt feltételezi, hogy `model` egy regisztrált `inference_config` modellt tartalmaz, és amely tartalmazza a következtetési környezet konfigurációját. További információ: Modellek telepítése az Azure Machine Learning használatával című [témakörben.](how-to-deploy-and-where.md)

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Amikor `show_output=True`a Docker-buildfolyamat kimenete megjelenik. Miután a folyamat befejeződött, a rendszerkép az Azure Container Registry a munkaterületre. Miután a rendszerkép már elkészült, a hely az Azure Container Registry jelenik meg. A visszaadott hely `<acrinstance>.azurecr.io/package@sha256:<hash>`formátumú.

> [!NOTE]
> A függvények csomagolása jelenleg támogatja a HTTP-eseményindítók, Blob-eseményindítók és a Service bus eseményindítók. Az eseményindítókról az [Azure Functions kötései](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns)című témakörben talál további információt.

> [!IMPORTANT]
> Mentse a helyadatokat, ahogy az a lemezkép telepítésekor használatos.

## <a name="deploy-image-as-a-web-app"></a>Lemezkép webalkalmazásként történő telepítése

1. A következő paranccsal lekérni a bejelentkezési hitelesítő adatokat az Azure Container Registry, amely tartalmazza a lemezképet. Cserélje `<myacr>` le a korábban visszaadott értékre: `package.location` 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    A parancs kimenete hasonló a következő JSON-dokumentumhoz:

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

    Mentse a __felhasználónév__ és az egyik __jelszó__értékét.

1. Ha még nem rendelkezik erőforráscsoporttal vagy alkalmazásszolgáltatási csomaggal a szolgáltatás üzembe helyezéséhez, a következő parancsok bemutatják, hogyan hozhat létre mindkettőt:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    Ebben a példában egy Linux`--sku B1` _alapszintű_ tarifacsomag ( ) használatos.

    > [!IMPORTANT]
    > Az Azure Machine Learning által létrehozott rendszerképek `--is-linux` Linuxot használnak, ezért a paramétert kell használnia.

1. Hozza létre a webes feladat tárolásához használandó tárfiókot, és vegye le a kapcsolati karakterláncot. Cserélje `<webjobStorage>` le a használni kívánt névre.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. A függvényalkalmazás létrehozásához használja a következő parancsot. Cserélje `<app-name>` le a használni kívánt névre. Cserélje `<acrinstance>` `<imagename>` le és az `package.location` értékeket vissza korábban. Cserélje `<webjobStorage>` le az előző lépésből származó tárfiók nevére:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Ezen a ponton a függvényalkalmazás létrejött. Azonban mivel nem adta meg a blob-eseményindító vagy hitelesítő adatok a blob eseményindító vagy hitelesítő adatokat az Azure Container Registry, amely a rendszerképet tartalmaz, a függvényalkalmazás nem aktív. A következő lépésekben adja meg a kapcsolati karakterláncot és a tároló beállításjegyzékének hitelesítési adatait. 

1. Hozza létre a blob-eseményindító tárterülethez használandó tárfiókot, és vegye le a kapcsolati karakterláncot. Cserélje `<triggerStorage>` le a használni kívánt névre.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactiv
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Rögzítse ezt a kapcsolati karakterláncot a függvényalkalmazásszámára. Majd később használjuk, amikor kérjük`<triggerConnectionString>`

1. Hozza létre a tárolók a bemeneti és kimeneti a tárfiókban. Cserélje `<triggerConnectionString>` le a korábban visszaadott kapcsolati karakterláncra:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Az eseményindító kapcsolati karakterlánc társításához a függvényalkalmazáshoz a következő paranccsal. Cserélje `<app-name>` le a függvényalkalmazás nevére. Cserélje `<triggerConnectionString>` le a korábban visszaadott kapcsolati karakterláncra:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. A létrehozott tárolóhoz társított címkét a következő paranccsal kell beolvasnia. Cserélje `<username>` le a tároló beállításjegyzékéből korábban visszaadott felhasználónévre:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Mentse el a visszaadott értéket, `imagetag` akkor a következő lépésben lesz használva.

1. Ha a függvényalkalmazást a tároló beállításjegyzékének eléréséhez szükséges hitelesítő adatokkal szeretné biztosítani, használja a következő parancsot. Cserélje `<app-name>` le a függvényalkalmazás nevére. Cserélje `<acrinstance>` `<imagetag>` le és az az előző lépésben az AZ CLI hívásának értékeit. Cserélje `<username>` `<password>` ki és írja be a korábban beolvasott ACR bejelentkezési adatokat:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Ez a parancs a következő JSON-dokumentumhoz hasonló információkat ad vissza:

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

Ezen a ponton a függvényalkalmazás megkezdi a lemezkép betöltését.

> [!IMPORTANT]
> A kép betöltése több percig is eltarthat. Az Azure Portal használatával figyelheti a folyamatot.

## <a name="test-the-deployment"></a>Az üzemelő példány tesztelése

Miután a kép betöltődött, és az alkalmazás elérhetővé vált, az alábbi lépésekkel indíthatja el az alkalmazást:

1. Hozzon létre egy olyan szövegfájlt, amely a score.py fájl által elvárt adatokat tartalmazza. A következő példa egy 10 számból álló tömböt váró score.py működik:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > Az adatok formátuma attól függ, hogy a score.py és a modell mire számít.

2. A következő paranccsal töltse fel ezt a fájlt a bemeneti tárolóba az eseményindító tárolási blob korábban létrehozott. Cserélje `<file>` le az adatokat tartalmazó fájl nevére. Cserélje `<triggerConnectionString>` le a korábban visszaadott kapcsolati karakterláncra. Ebben a `input` példában a korábban létrehozott bemeneti tároló neve. Ha más nevet használt, cserélje le ezt az értéket:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    A parancs kimenete hasonló a következő JSON-hoz:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. A függvény által létrehozott kimenet megtekintéséhez használja a következő parancsot a létrehozott kimeneti fájlok listázásához. Cserélje `<triggerConnectionString>` le a korábban visszaadott kapcsolati karakterláncra. Ebben a `output` példában a korábban létrehozott kimeneti tároló neve. Ha más nevet használt, cserélje le ezt az értéket::

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    A parancs kimenete hasonló `sample_input_out.json`a hoz.

4. A fájl letöltéséhez és a tartalom vizsgálatához használja a következő parancsot. Cserélje `<file>` le az előző parancs által visszaadott fájlnévre. Cserélje `<triggerConnectionString>` le a korábban visszaadott kapcsolati karakterláncra: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    A parancs befejezése után nyissa meg a fájlt. A modell által visszaadott adatokat tartalmazza.

A blob-eseményindítók használatával kapcsolatos további információkért tekintse meg az [Azure Blob storage-cikk által aktivált függvény létrehozása című témakört.](/azure/azure-functions/functions-create-storage-blob-triggered-function)

## <a name="next-steps"></a>További lépések

* Ismerje meg a Functions alkalmazás konfigurálását a [Functions](/azure/azure-functions/functions-create-function-linux-custom-image) dokumentációban.
* További információ a Blob storage-eseményindítók [Az Azure Blob storage-kötések.](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)
* [Telepítse a modellt az Azure App Service szolgáltatásba.](how-to-deploy-app-service.md)
* [Webszolgáltatásként üzembe helyezett ml-modell felhasználása](how-to-consume-web-service.md)
* [API-referencia](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
