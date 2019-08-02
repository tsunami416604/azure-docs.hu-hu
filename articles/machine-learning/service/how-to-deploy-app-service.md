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
ms.openlocfilehash: 11e8b26c81d3a8f672abbeafc153df73d063cb6e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612235"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Gépi tanulási modell üzembe helyezése Azure App Service (előzetes verzió)

Megtudhatja, hogyan helyezhet üzembe egy modellt a Azure Machine Learning szolgáltatásból webalkalmazásként a Azure App Serviceban.

> [!IMPORTANT]
> Noha a Azure Machine Learning szolgáltatás és a Azure App Service egyaránt általánosan elérhető, a modellnek a Machine Learning szolgáltatásból való üzembe helyezésének lehetősége a App Service előzetes verzióban érhető el.

A Azure Machine Learning szolgáltatással Docker-rendszerképeket hozhat létre a képzett gépi tanulási modellekből. Ez a rendszerkép olyan webszolgáltatást tartalmaz, amely fogadja az adatok fogadását, elküldi a modellbe, majd visszaadja a választ. A Azure App Service használható a lemezkép üzembe helyezéséhez, és a következő funkciókat biztosítja:

* [SSL-támogatás](/azure/app-service/app-service-web-ssl-cert-load) az ügyfelek és a szolgáltatás közötti biztonságos kommunikációhoz.
* [](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) Az újbóli üzembe helyezése nélkül bővítse több példányra.
* [Speciális hitelesítés](/azure/app-service/configure-authentication-provider-aad) a fokozott biztonsághoz.

A Azure App Service által nyújtott szolgáltatásokkal kapcsolatos további információkért tekintse meg a [app Service áttekintését](/azure/app-service/overview).

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning szolgáltatás munkaterületén. További információt a [Munkaterület létrehozása](setup-create-workspace.md) című cikkben talál.
* A munkaterületen regisztrált, betanított gépi tanulási modell. Ha nem rendelkezik modellel, használja a képbesorolási [oktatóanyagot:](tutorial-train-models-with-aml.md) a betanítási modell betanítása és regisztrálása.
* A modellből létrehozott Docker-rendszerkép. Ha nincs rendszerképe, használja a [rendszerkép besorolását: modell üzembe helyezése](tutorial-deploy-models-with-aml.md) egy létrehozásához.

## <a name="deploy-image-as-a-web-app"></a>Rendszerkép üzembe helyezése webalkalmazásként

1. A [Azure Portal](https://portal.azure.com)válassza ki Azure Machine learning munkaterületét. Az __Áttekintés__ szakaszban a __beállításjegyzék__ -hivatkozás használatával férhet hozzá a munkaterülethez tartozó Azure Container Registryhoz.

    ![A munkaterület áttekintését bemutató képernyőkép](media/how-to-deploy-app-service/workspace-overview.png)

2. A Azure Container Registry válassza ki a __tárak__elemet, majd válassza ki a telepíteni kívánt __lemezkép nevét__ . A telepíteni kívánt verzióhoz válassza a __...__ bejegyzést, majd __telepítse__a webalkalmazásba.

    ![Képernyőkép az ACR-ről egy webalkalmazásra való üzembe helyezésről](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. A webalkalmazás létrehozásához adja meg a hely nevét, az előfizetést, az erőforráscsoportot, majd válassza ki az App Service-csomagot/-helyet. Végül válassza a __Létrehozás__lehetőséget.

    ![Az új webalkalmazás párbeszédpanel képernyőképe](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>A webalkalmazás használata

A [Azure Portal](https://portal.azure.com)válassza ki az előző lépésben létrehozott webalkalmazást. Az __Áttekintés__ szakaszban másolja az __URL-címet__. Ez az érték a szolgáltatás __alap URL-címe__ .

![A webalkalmazás áttekintését bemutató képernyőkép](media/how-to-deploy-app-service/web-app-overview.png)

A modellre irányuló kérelmeket továbbító webszolgáltatás a következő `{baseurl}/score`helyen található:. Például: `https://mywebapp.azurewebsites.net/score`. A következő Python-kód azt mutatja be, hogyan lehet beküldeni az adott URL-címet és megjeleníteni a választ:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

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