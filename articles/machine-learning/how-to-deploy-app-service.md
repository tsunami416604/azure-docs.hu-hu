---
title: Ml-modellek üzembe helyezése az Azure App Service szolgáltatásban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezhet üzembe egy modellt az Azure App Service-ben egy webalkalmazásra az Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 3e6cfde20d9f4d56af836e06b0c9a84010dea47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282817"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Gépi tanulási modell üzembe helyezése az Azure App Service szolgáltatásban (előzetes verzió)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan helyezhet üzembe egy modellt az Azure Machine Learningweb-alkalmazásként az Azure App Service-ben.

> [!IMPORTANT]
> Bár az Azure Machine Learning és az Azure App Service általánosan elérhető, a machine learning szolgáltatásból az App Service-be egy modell üzembe helyezésének lehetősége előzetes verzióban érhető el.

Az Azure Machine Learning segítségével Docker-rendszerképeket hozhat létre betanított gépi tanulási modellekből. Ez a lemezkép tartalmaz egy webszolgáltatás, amely adatokat fogad, elküldi a modell, majd visszaadja a választ. Az Azure App Service a lemezkép üzembe helyezésére használható, és a következő funkciókat biztosítja:

* Speciális [hitelesítés](/azure/app-service/configure-authentication-provider-aad) a fokozott biztonság érdekében. A hitelesítési módszerek közé tartozik az Azure Active Directory és a többtényezős auth.
* [Automatikus skálázás](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) az újratelepítés nélkül.
* [TLS támogatás](/azure/app-service/configure-ssl-certificate-in-code) az ügyfelek és a szolgáltatás közötti biztonságos kommunikációhoz.

Az Azure App Service által nyújtott funkciókról az App Service áttekintése című témakörben olvashat [bővebben.](/azure/app-service/overview)

> [!IMPORTANT]
> Ha szüksége van a telepített modellhez használt pontozási adatok naplózására, vagy a pontozás eredményeire, ehelyett az Azure Kubernetes-szolgáltatásra kell telepítenie. További információ: [Adatok gyűjtése a termelési modellekről](how-to-enable-data-collection.md).

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

    > [!IMPORTANT]
    > Az Azure Machine Learning SDK nem biztosít leutat a webszolgáltatás számára az adatkészletek eléréséhez. Ha az üzembe helyezett modellnek szüksége van a központi telepítésen kívül tárolt adatok eléréséhez, például egy Azure Storage-fiókban, egyéni kódmegoldást kell kifejlesztenie a megfelelő SDK használatával. Például az [Azure Storage SDK pythonhoz.](https://github.com/Azure/azure-storage-python)
    >
    > Egy másik alternatíva, amely működhet a forgatókönyv [kötegelt előrejelzések,](how-to-use-parallel-run-step.md)amely hozzáférést biztosít az adattárak hoz a pontozáskor.

    A bejegyzési parancsfájlokkal kapcsolatos további információkért lásd: Modellek telepítése az Azure Machine Learning használatával című [témakörben.](how-to-deploy-and-where.md)

* **Függőségek**, például segítő parancsfájlok vagy Python/Conda csomagok szükségesek a bejegyzési parancsfájl vagy modell futtatásához

Ezek az entitások következtetési __konfigurációba__vannak ágyazva. A következtetés konfigurációja a bejegyzésparancsfájlra és más függőségekre hivatkozik.

> [!IMPORTANT]
> Amikor egy következtetési konfigurációt hoz létre az Azure App Service használatával való használatra, egy környezeti objektumot kell [használnia.](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) Kérjük, vegye figyelembe, hogy ha egyéni környezetet határoz meg, akkor az azureml-defaults-t a >= 1.0.45 verzióval kell hozzáadnia pip-függőségként. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat. A következő példa bemutatja egy környezeti objektum létrehozását és használatát következtetési konfigurációval:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
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
> Az Azure App Service-be való üzembe helyezéskor nem kell __központi telepítési konfigurációt létrehoznia.__

## <a name="create-the-image"></a>A rendszerkép létrehozása

Az Azure App Service-be üzembe helyezett Docker-rendszerkép létrehozásához használja a [Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)alkalmazást. A következő kódrészlet bemutatja, hogyan hozhat létre új lemezképet a modellből és a következtetéskonfigurációból:

> [!NOTE]
> A kódrészlet azt feltételezi, hogy `model` egy regisztrált `inference_config` modellt tartalmaz, és amely tartalmazza a következtetési környezet konfigurációját. További információ: Modellek telepítése az Azure Machine Learning használatával című [témakörben.](how-to-deploy-and-where.md)

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Amikor `show_output=True`a Docker-buildfolyamat kimenete megjelenik. Miután a folyamat befejeződött, a rendszerkép az Azure Container Registry a munkaterületre. Miután a rendszerkép már elkészült, a hely az Azure Container Registry jelenik meg. A visszaadott hely `<acrinstance>.azurecr.io/package:<imagename>`formátumú. Például: `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Mentse a helyadatokat, ahogy az a lemezkép telepítésekor használatos.

## <a name="deploy-image-as-a-web-app"></a>Lemezkép webalkalmazásként történő telepítése

1. A következő paranccsal lekérni a bejelentkezési hitelesítő adatokat az Azure Container Registry, amely tartalmazza a lemezképet. Csere `<acrinstance>` a korábban visszaadott értékre: `package.location`

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

    Ebben a példában egy __alapszintű__ tarifacsomag (`--sku B1`) használatos.

    > [!IMPORTANT]
    > Az Azure Machine Learning által létrehozott rendszerképek `--is-linux` Linuxot használnak, ezért a paramétert kell használnia.

1. A webalkalmazás létrehozásához használja a következő parancsot. Cserélje `<app-name>` le a használni kívánt névre. Cserélje `<acrinstance>` `<imagename>` ki és a `package.location` korábban visszaadott értékeket:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    Ez a parancs a következő JSON-dokumentumhoz hasonló információkat ad vissza:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > Ezen a ponton a webalkalmazás létrejött. Azonban mivel nem adta meg a hitelesítő adatokat az Azure Container Registry, amely tartalmazza a lemezképet, a webalkalmazás nem aktív. A következő lépésben adja meg a tároló beállításjegyzékhitelesítési adatait.

1. Ha a webalkalmazás nak meg szeretné adni a tároló beállításjegyzékének eléréséhez szükséges hitelesítő adatokat, használja a következő parancsot. Cserélje `<app-name>` le a használni kívánt névre. Cserélje `<acrinstance>` `<imagename>` le és az `package.location` értékeket vissza korábban. Cserélje `<username>` `<password>` ki és írja be a korábban beolvasott ACR bejelentkezési adatokat:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

Ezen a ponton a webalkalmazás megkezdi a lemezkép betöltését.

> [!IMPORTANT]
> A kép betöltése több percig is eltarthat. A folyamat figyeléséhez használja a következő parancsot:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Miután a kép bevan töltve, és a hely `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`aktív, a napló egy üzenetet jelenít meg, amely a .

A lemezkép telepítése után a következő paranccsal megtalálhatja a gazdanevet:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Ez a parancs a következő állomásnévhez hasonló információkat ad vissza: . `<app-name>.azurewebsites.net` Ezt az értéket használja a szolgáltatás __alap URL-címének__ részeként.

## <a name="use-the-web-app"></a>A Web App használata

A kéréseket a modellnek kézbesítő `{baseurl}/score`webszolgáltatás a helyen található. Például: `https://<app-name>.azurewebsites.net/score`. A következő Python-kód bemutatja, hogyan küldhet adatokat az URL-címre, és hogyan jelenítheti meg a választ:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>További lépések

* Ismerje meg a webalkalmazás konfigurálását az [App Service Linux-on](/azure/app-service/containers/) dokumentációjában.
* További információ a méretezésről az [Első lépések az Automatikus skálázás azure-ban.](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)
* [TLS/SSL-tanúsítvány használata az Azure App Service szolgáltatásban.](/azure/app-service/configure-ssl-certificate-in-code)
* [Konfigurálja az App Service-alkalmazást az Azure Active Directory bejelentkezési használatára.](/azure/app-service/configure-authentication-provider-aad)
* [Webszolgáltatásként üzembe helyezett ml-modell felhasználása](how-to-consume-web-service.md)
