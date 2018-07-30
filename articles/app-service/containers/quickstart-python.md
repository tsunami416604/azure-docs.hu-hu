---
title: Python-alkalmazás üzembe helyezése az Azure Web App for Containersben
description: Python-alkalmazást futtató Docker-rendszerkép üzembe helyezése a Web App for Containersben.
keywords: azure app service, webalkalmazás, python, docker, tároló
services: app-service
author: cephalin
manager: jeconnoc
ms.service: app-service
ms.devlang: python
ms.topic: quickstart
ms.date: 07/13/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 5686266774603413fc255c53a0d1ad30f9baa8eb
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173860"
---
# <a name="deploy-a-python-web-app-in-web-app-for-containers"></a>Python-webalkalmazás üzembe helyezése a Web App for Containersben

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez a rövid útmutató webalkalmazások létrehozását és egyszerű Flask-alkalmazások üzembe helyezését mutatja be egyéni Docker Hub-rendszerkép használatával. Az [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) létrehozhatja a webalkalmazást.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>
* <a href="https://www.docker.com/community-edition" target="_blank">A Docker Community Edition telepítése</a>
* <a href="https://hub.docker.com/" target="_blank">Regisztráció Docker Hub-fiókra</a>

## <a name="download-the-sample"></a>A minta letöltése

Futtassa a következő parancsokat egy terminálablakban. Ezzel klónozza a mintaalkalmazást a helyi gépre, és a mintakódot tartalmazó könyvtárba lép.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Az adattár _/app_ mappájában egy egyszerű Flask-alkalmazás és az alábbi három dolgot megadó _Docker-fájl_ található:

- A [tiangolo/uwsgi-nginx-flask:python3.6-alpine3.7](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/) alaprendszerkép használata.
- A tárolónak a 8000-es porton kell figyelnie.
- Az `/app` könyvtár másolása a tároló `/app` könyvtárába.

A konfiguráció az [alaprendszerképre vonatkozó útmutatás](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/) szerint történik.

## <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa az alkalmazást egy Docker-tárolóban.

```bash
docker build --rm -t flask-quickstart .
docker run --rm -it -p 8000:8000 flask-quickstart
```

Nyisson meg egy webböngészőt, majd keresse meg a mintaalkalmazást a `http://localhost:8000` címen.

Az oldalon látható mintaalkalmazáson ekkor a **Hello World** üzenetnek kell megjelennie.

![A helyileg futó mintaalkalmazás](media/quickstart-python/localhost-hello-world-in-browser.png)

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a tároló leállításához.

## <a name="deploy-image-to-docker-hub"></a>Rendszerkép telepítése a Docker Hubra

Jelentkezzen be Docker Hub-fiókjába. Az utasításokat követve adja meg Docker Hub hitelesítő adatait.

```bash
docker login
```

Címkézze fel a rendszerképet, és küldje le a Docker Hub-fiókjában található új _nyilvános_ adattárba, amelynek neve a következő: `flask-quickstart`. A *\<dockerhub_id>* helyére írja be Docker Hub-azonosítóját.

```bash
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

> [!NOTE]
> `docker push` létrehoz egy nyilvános adattárat, ha a megadott adattár nem található. Ez a rövid útmutató feltételezi, hogy rendelkezik nyilvános adattárral a Docker Hubon. Ha inkább privát adattárat szeretne használni, akkor a későbbiekben konfigurálnia kell Docker Hub hitelesítő adatait az Azure App Service-ben. Lásd a [webalkalmazás létrehozását](#create-a-web-app) ismertető témakört.

Miután a rendszerkép leküldése befejeződött, használatba is veheti az Azure-webalkalmazásban.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Az [ az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) paranccsal hozzon létre egy [webalkalmazást](../app-service-web-overview.md) a `myAppServicePlan` App Service-csomagban. Az *\<app name>* helyett adjon meg egy globálisan egyedi alkalmazásnevet, a *\<dockerhub_id>* helyett pedig Docker Hub-azonosítóját.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name <dockerhub_id>/flask-quickstart
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Ha már korábban töltött fel adatokat privát adattárba, akkor emellett az App Service-ben konfigurálnia kell Docker Hub hitelesítő adatait. További információkat a [Docker Hubról származó privát rendszerkép használatát](tutorial-custom-docker-image.md#use-a-private-image-from-docker-hub-optional) ismertető témakörben olvashat.

### <a name="specify-container-port"></a>Tárolóport megadása

A _Docker-fájlban_ megadottak szerint a tároló a 8000-es porton figyel. Ahhoz, hogy az App Service a megfelelő porthoz tudja irányítani a kéréseket, meg kell adnia a *WEBSITES_PORT* alkalmazásbeállítást.

A Cloud Shellben futtassa az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) parancsot.


```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings WEBSITES_PORT=8000
```

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

```bash
http://<app_name>.azurewebsites.net/
```

![Az Azure-ban futó mintaalkalmazás](media/quickstart-python/hello-world-in-browser.png)

> [!NOTE]
> A webalkalmazás elindítása hosszabb időt vesz igénybe, mivel a Docker Hub-rendszerképet le kell tölteni és el kell indítani az alkalmazás első igénylésekor. Ha először hosszú várakozási idő után hibát észlel, frissítse a lapot.

**Gratulálunk!** Egy Python-alkalmazást futtató egyéni Docker-rendszerképet helyezett üzembe a Web App for Containersben.

## <a name="update-locally-and-redeploy"></a>Frissítés helyileg és ismételt üzembe helyezés

Egy helyi szövegszerkesztővel nyissa meg a `app/main.py` fájlt a Python-alkalmazásban, majd módosítsa kissé annak szövegét a `return` utasítás mellett:

```python
return 'Hello, Azure!'
```

Hozza létre újra a rendszerképet, majd ismételten küldje le a Docker Hubra.

```bash
docker build --rm -t flask-quickstart .
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

Indítsa újra az alkalmazást a Cloud Shellben. Az alkalmazás újraindítása biztosítja, hogy minden beállítás alkalmazva lesz, és a rendszer a legújabb tárolót kéri le a tárolóregisztrációs adatbázisból.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

Az App Service-ben várjon körülbelül 15 másodpercet a frissített rendszerkép lekérésére. Váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse az oldalt.

![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-azure-web-app"></a>Az Azure-webalkalmazás kezelése

Lépjen az [Azure Portalra](https://portal.azure.com), és tekintse meg a létrehozott webalkalmazást.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/quickstart-python/app-service-list.png)

Alapértelmezés szerint a portálon a webalkalmazás **Áttekintés** oldala jelenik meg. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/quickstart-python/app-service-detail.png)

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Python és PostgreSQL](tutorial-docker-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Egyéni rendszerképek használata](tutorial-custom-docker-image.md)