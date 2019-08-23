---
title: Ml modellek üzembe helyezése Azure App Service (előzetes verzió)
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan helyezhet üzembe egy modellt egy webalkalmazásban a Azure App Serviceban a Azure Machine Learning szolgáltatás használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: ada2a19de12c2f3f6b23fcc3d759afb0c747d37d
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897403"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Gépi tanulási modell üzembe helyezése Azure App Service (előzetes verzió)

Megtudhatja, hogyan helyezhet üzembe egy modellt a Azure Machine Learning szolgáltatásból webalkalmazásként a Azure App Serviceban.

> [!IMPORTANT]
> Noha a Azure Machine Learning szolgáltatás és a Azure App Service egyaránt általánosan elérhető, a modellnek a Machine Learning szolgáltatásból való üzembe helyezésének lehetősége a App Service előzetes verzióban érhető el.

A Azure Machine Learning szolgáltatással Docker-rendszerképeket hozhat létre a képzett gépi tanulási modellekből. Ez a rendszerkép olyan webszolgáltatást tartalmaz, amely fogadja az adatok fogadását, elküldi a modellbe, majd visszaadja a választ. A Azure App Service használható a lemezkép üzembe helyezéséhez, és a következő funkciókat biztosítja:

* Speciális [hitelesítés](/azure/app-service/configure-authentication-provider-aad) a fokozott biztonsághoz. A hitelesítési módszerek közé tartozik a Azure Active Directory és a multi-Factor Auth is.
* [](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) Az autoskálázást anélkül, hogy újra kellene telepíteni.
* [SSL-támogatás](/azure/app-service/app-service-web-ssl-cert-load) az ügyfelek és a szolgáltatás közötti biztonságos kommunikációhoz.

A Azure App Service által nyújtott szolgáltatásokkal kapcsolatos további információkért tekintse meg a [app Service áttekintését](/azure/app-service/overview).

> [!IMPORTANT]
> Ha szüksége van az üzembe helyezett modellel használt pontozási-adatnapló vagy a pontozás eredményeinek naplózására, ehelyett üzembe kell helyeznie az Azure Kubernetes Service-t. További információ: [adatgyűjtés az éles modelleken](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning szolgáltatás munkaterületén. További információt a [Munkaterület létrehozása](how-to-manage-workspace.md) című cikkben talál.
* A munkaterületen regisztrált, betanított gépi tanulási modell. Ha nem rendelkezik modellel, használja a képbesorolási [oktatóanyagot:](tutorial-train-models-with-aml.md) a betanítási modell betanítása és regisztrálása.

    > [!IMPORTANT]
    > A cikkben szereplő kódrészletek azt feltételezik, hogy a következő változókat állította be:
    >
    > * `ws`– Azure Machine Learning munkaterület.
    > * `model`– A rendszerbe állított regisztrált modell.
    > * `inference_config`– A modellre vonatkozó következtetési konfiguráció.
    >
    > A változók beállításával kapcsolatos további információkért lásd: [modellek üzembe helyezése a Azure Machine learning szolgáltatással](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Üzembe helyezés előkészítése

A telepítés előtt meg kell határoznia, hogy mire van szükség a modell webszolgáltatásként való futtatásához. Az alábbi lista a központi telepítéshez szükséges alapvető elemeket ismerteti:

* Egy __bejegyzési parancsfájl__. Ez a szkript fogadja a kéréseket, a modell használatával szerzi a kérést, és visszaadja az eredményeket.

    > [!IMPORTANT]
    > A bejegyzési parancsfájl a modellre jellemző. meg kell ismernie a bejövő kérelmek adatainak formátumát, a modell által várt adatformátumot, valamint az ügyfeleknek visszaadott adatformátumot.
    >
    > Ha a kérelem adatai olyan formátumban vannak, amely nem használható a modellben, a parancsfájl elfogadható formátumba alakíthatja át. A válasz is át lehet alakítani, mielőtt visszatért az ügyfélhez.

    > [!IMPORTANT]
    > A Azure Machine Learning SDK nem biztosít lehetőséget a webszolgáltatás számára az adattár vagy az adatkészletek elérésére. Ha a központi telepítésen kívül tárolt adatokat szeretné elérni az üzembe helyezett modellel, például egy Azure Storage-fiókban, egyéni kódot kell létrehoznia a megfelelő SDK használatával. Például a Pythonhoz készült [Azure Storage SDK](https://github.com/Azure/azure-storage-python)-t.
    >
    > Egy másik alternatíva, amely a forgatókönyv esetében is működhet, a [Batch-előrejelzések](how-to-run-batch-predictions.md), amelyek a pontozáskor hozzáférést biztosítanak az adattárolóhoz.

    A beléptetési parancsfájlokkal kapcsolatos további információkért lásd: [modellek üzembe helyezése a Azure Machine learning szolgáltatással](how-to-deploy-and-where.md).

* **Függőségek**, például segítő parancsfájlok vagy Python/Conda csomagok, amelyek a belépési parancsfájl vagy modell futtatásához szükségesek

Ezek az entitások egy következtetési __konfigurációba__vannak ágyazva. A következtetési konfiguráció a bejegyzési parancsfájlra és más függőségekre hivatkozik.

> [!IMPORTANT]
> Ha Azure App Service-vel való használatra vonatkozó következtetési konfigurációt hoz létre, [környezeti](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) objektumot kell használnia. Az alábbi példa bemutatja, hogyan hozható létre egy környezeti objektum, és hogyan használhatja azt egy következtetési konfigurációval:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

További információ a környezetekről: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md).

További információ a konfigurációról: [modellek üzembe helyezése a Azure Machine learning szolgáltatással](how-to-deploy-and-where.md).

> [!IMPORTANT]
> A Azure App Service való telepítésekor nem kell létrehoznia __központi telepítési konfigurációt__.

## <a name="create-the-image"></a>A rendszerkép létrehozása

A Azure App Service üzembe helyezett Docker-rendszerkép létrehozásához használja a [Model. package csomagot](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-). A következő kódrészlet bemutatja, hogyan hozhat létre egy új rendszerképet a modellből és a következtetések konfigurálásával:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Ekkor `show_output=True`megjelenik a Docker-létrehozási folyamat kimenete. A folyamat befejeződése után a rendszerkép a munkaterülethez tartozó Azure Container Registryban lett létrehozva.

## <a name="deploy-image-as-a-web-app"></a>Rendszerkép üzembe helyezése webalkalmazásként

1. A [Azure Portal](https://portal.azure.com)válassza ki Azure Machine learning munkaterületét. Az __Áttekintés__ szakaszban a __beállításjegyzék__ -hivatkozás használatával férhet hozzá a munkaterülethez tartozó Azure Container Registryhoz.

    [![A munkaterület áttekintését bemutató képernyőkép](media/how-to-deploy-app-service/workspace-overview.png)](media/how-to-deploy-app-service/workspace-overview-expanded.png)

2. A Azure Container Registry válassza ki a __tárak__elemet, majd válassza ki a telepíteni kívánt __lemezkép nevét__ . A telepíteni kívánt verzióhoz válassza a __...__ bejegyzést, majd __telepítse__a webalkalmazásba.

    [![Képernyőkép az ACR-ről egy webalkalmazásra való üzembe helyezésről](media/how-to-deploy-app-service/deploy-to-web-app.png)](media/how-to-deploy-app-service/deploy-to-web-app-expanded.png)

3. A webalkalmazás létrehozásához adja meg a hely nevét, az előfizetést, az erőforráscsoportot, majd válassza ki az App Service-csomagot/-helyet. Végül válassza a __Létrehozás__lehetőséget.

    ![Az új webalkalmazás párbeszédpanel képernyőképe](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>A webalkalmazás használata

A [Azure Portal](https://portal.azure.com)válassza ki az előző lépésben létrehozott webalkalmazást. Az __Áttekintés__ szakaszban másolja az __URL-címet__. Ez az érték a szolgáltatás __alap URL-címe__ .

[![A webalkalmazás áttekintését bemutató képernyőkép](media/how-to-deploy-app-service/web-app-overview.png)](media/how-to-deploy-app-service/web-app-overview-expanded.png)

A modellre irányuló kérelmeket továbbító webszolgáltatás a következő `{baseurl}/score`helyen található:. Például: `https://mywebapp.azurewebsites.net/score`. A következő Python-kód azt mutatja be, hogyan lehet beküldeni az adott URL-címet és megjeleníteni a választ:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

print(headers)
    
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

* A webalkalmazás konfigurálásával kapcsolatos további információkért tekintse [meg a Linux](/azure/app-service/containers/) -dokumentáció app Serviceát.
* A skálázással kapcsolatos további információkért lásd: [az automatikus skálázás első lépései az Azure-ban](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* Az SSL-támogatással kapcsolatos további információkért lásd: [SSL-tanúsítvány használata a Azure app Serviceban](/azure/app-service/app-service-web-ssl-cert-load).
* A hitelesítéssel kapcsolatos további információkért lásd: [a app Service alkalmazás konfigurálása Azure Active Directory bejelentkezés használatára](/azure/app-service/configure-authentication-provider-aad).
* [Webszolgáltatásként üzembe helyezett gépi Tanulási modell felhasználása](how-to-consume-web-service.md)