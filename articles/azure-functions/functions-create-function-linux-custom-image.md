---
title: Függvény létrehozása Linux rendszerben egyéni rendszerkép használatával (előzetes verzió) | Microsoft Docs
description: Megismerheti, hogyan hozhat létre egyéni Linux-rendszerképeken futó Azure Functions-függvényeket.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: fab67b503d060c8c01b5a3692c8a07b24c425c78
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437406"
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Függvény létrehozása Linux rendszerben egyéni rendszerkép használatával (előzetes verzió)

Az Azure Functions lehetővé teszi, hogy a függvényeit Linux rendszerben egy saját egyéni tárolóban üzemeltesse. Emellett [egy alapértelmezett Azure App Service-tárolóban is üzemeltetheti](functions-create-first-azure-function-azure-cli-linux.md). Ez a szolgáltatás jelenleg csak előzetes verzióban érhető el, és a használatához a szintén előzetes verzióként elérhető [Functions 2.0 futtatókörnyezet](functions-versions.md) szükséges.

Ez az oktatóanyag bemutatja, hogyan helyezheti a függvényalkalmazásokat üzembe egyéni Docker-rendszerképként. Ez a minta akkor hasznos, ha testre kell szabnia a beépített App Service-tároló rendszerképét. Érdemes lehet egyéni rendszerképet használnia, ha a funkciók egy adott nyelvi verziójára vagy egy konkrét függőségre vagy konfigurációra van szükség, amely a beépített rendszerképben nem biztosított.

Ez az oktatóanyag bemutatja, hogyan hozhat létre és küldhet le egyéni rendszerképeket a Docker Hubba az Azure Functions használatával. Ezt a rendszerképet azután üzembehelyezési forrásként használhatja egy, a Linux rendszeren futó függvényalkalmazáshoz. A rendszerképet a Docker használatával készítheti el és küldheti le. Az Azure CLI használatával hozhatja létre a függvényalkalmazást, és helyezheti üzembe a rendszerképet a Docker Hubból. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egyéni rendszerkép készítése a Docker használatával.
> * Egyéni rendszerkép közzététele egy tárolójegyzékben. 
> * Azure Storage-fiók létrehozása. 
> * Linux App Service-csomag létrehozása. 
> * Függvényalkalmazás üzembe helyezése a Docker Hubból.
> * Alkalmazásbeállítások hozzáadása a függvényalkalmazáshoz. 

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépeken követheti.  

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Git](https://git-scm.com/downloads)
* Aktív [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/install/)
* Egy [Docker Hub-fiók](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>A minta letöltése

Egy terminálablakban futtassa a következő parancsot a mintaalkalmazás adattárának a helyi számítógépre történő klónozásához, majd lépjen át a mintakódot tartalmazó könyvtárba.

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>A rendszerkép létrehozása a Docker-fájlból

A Git-adattárban tekintse meg a _Dockerfile_ nevű fájlt. Ez a fájl írja le a függvényalkalmazás a Linux rendszeren való futtatásához szükséges környezetet. 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> Ha egy privát tárolójegyzékben tesz elérhetővé egy rendszerképet, a kapcsolati beállításokat a Dockerfile **ENV** változóival adja a függvényalkalmazáshoz. Mivel ebben az oktatóanyagban nem garantálható, hogy privát adatbázist fog használni, ajánlott biztonsági eljárásként a kapcsolati beállításokat [az üzembe helyezést követően az Azure CLI használatával](#configure-the-function-app) adjuk hozzá.   

### <a name="run-the-build-command"></a>A Build parancs futtatása
A Docker-rendszerkép létrehozásához futtassa a `docker build` parancsot, majd adjon meg egy nevet (`mydockerimage`) és egy címkét (`v1.0.0`). A `<docker-id>` helyére a Docker Hub-fiók azonosítóját írja.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

A parancs a következőhöz hasonló kimenetet eredményez:

```bash
Sending build context to Docker daemon  169.5kB
Step 1/3 : FROM microsoft/azure-functions-runtime:v2.0.0-jessie
v2.0.0-jessie: Pulling from microsoft/azure-functions-runtime
b178b12f7913: Pull complete
2d9ce077a781: Pull complete
4775d4ba55c8: Pull complete
Digest: sha256:073f45fc167b3b5c6642ef4b3c99064430d6b17507095...
Status: Downloaded newer image for microsoft/azure-functions-runtime:v2.0.0-jessie
 ---> 217799efa500
Step 2/3 : ENV AzureWebJobsScriptRoot /home/site/wwwroot
 ---> Running in 528fa2077d17
 ---> 7cc6323b8ae0
Removing intermediate container 528fa2077d17
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5bdac9878423
Successfully built 5bdac9878423
Successfully tagged ggailey777/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>A rendszerkép helyi tesztelése
Ellenőrizze, hogy az elkészült rendszerkép működik-e – ehhez futtassa a Docker-rendszerképet egy helyi tárolóban. Adja ki a [docker run](https://docs.docker.com/engine/reference/commandline/run/) parancsot, és adja meg a rendszerkép nevét és címkéjét. Ügyeljen arra, hogy a `-p` argumentum használatával megadja a portot is.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Amikor az egyéni rendszerkép fut a helyi Docker-tárolóban, a függvényalkalmazás és -tároló megfelelő működésének ellenőrzéséhez keresse fel a böngészőben a <http://localhost:8080> címet.

![Tesztelje helyben a függvényalkalmazást.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Miután ellenőrizte a függvényalkalmazást a tárolóban, állítsa le a végrehajtást. Most leküldheti az egyéni rendszerképet a Docker Hub-fiókjába.

## <a name="push-the-custom-image-to-docker-hub"></a>Az egyéni rendszerkép leküldése a Docker Hubba

A tárolójegyzék egy olyan alkalmazás, amely elérhetővé teszi a rendszerképeket, valamint rendszerkép- és tárolószolgáltatásokat is biztosít. A rendszerkép megosztásához le kell küldenie azt a tárolójegyzékbe. A Docker Hub egy olyan tárolójegyzék a Docker-rendszerképek számára, amely saját nyilvános vagy privát adattárak üzemeltetését teszi lehetővé. 

A rendszerkép leküldése előtt be kell jelentkeznie a Docker Hubra a [docker login](https://docs.docker.com/engine/reference/commandline/login/) paranccsal. A `<docker-id>` helyére a fiók nevét írja, majd adja meg a jelszót a konzolban, amikor a rendszer kéri. Egyéb Docker Hub-jelszóbeállításokért tekintse meg a [docker login parancs dokumentációját](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id> 
```

A „login succeeded” (sikeres bejelentkezés) üzenet jelzi, hogy sikerült bejelentkeznie. Miután bejelentkezett, a rendszerképet a Docker Hubba a [docker push](https://docs.docker.com/engine/reference/commandline/push/) paranccsal küldheti le.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

A parancs kimenetének megvizsgálásával győződjön meg a leküldés sikerességéről.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
A rendszerképet most már használhatja üzembehelyezési forrásként egy új függvényalkalmazáshoz az Azure-ban. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0.21-es vagy újabb verziójára lesz szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Linux App Service-csomag létrehozása

A Functions üzemeltetése Linuxon jelenleg a használatalapú csomagok esetében nem támogatott. Egy Linux App Service-csomagban kell futtatnia. További információk az üzemeltetésről: [Azure Functions szolgáltatási csomagok összehasonlítása](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]


## <a name="create-and-deploy-the-custom-image"></a>Egyéni rendszerkép létrehozása és üzembe helyezése

A függvényalkalmazás a függvények végrehajtásához szükséges gazdaszolgáltatást biztosítja. A függvényalkalmazásokat a Docker Hub-rendszerképekből az [az functionapp create](/cli/azure/functionapp#az-functionapp-create) parancs használatával hozhatja létre. 

A következő parancsban a `<app_name>` helyőrző helyett írjon be egy egyedi függvényalkalmazás-nevet, a `<storage_name>` helyőrző helyett pedig a tárfiók nevét. Az `<app_name>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. Ahogy korábban is, a `<docker-id>` a Docker-fiók neve.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0 
```
A függvény létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

A _deployment-container-image-name_ paraméter jelöli a Docker Hubon tárolt rendszerképet, amelynek használatával a függvényalkalmazást létrehozzuk. 


## <a name="configure-the-function-app"></a>A függvényalkalmazás konfigurálása

A függvénynek a kapcsolati sztringre az alapértelmezett tárfiókhoz való kapcsolódáshoz van szüksége. Ha az egyéni rendszerképet egy privát tárolófiókon teszi közzé, ehelyett ezeket az alkalmazásbeállításokat környezeti változókként kell megadnia a Dockerfile-ban az [ENV utasítás](https://docs.docker.com/engine/reference/builder/#env) vagy egy azzal egyenértékű utasítás használatával. 

Ebben az esetben a `<storage_account>` a létrehozott tárfiók neve. Kérje le a kapcsolati sztringet az [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string) paranccsal. Adja hozzá ezeket az alkalmazásbeállításokat a függvényalkalmazáshoz az [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) paranccsal.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

Most tesztelheti a Linuxon futó Azure-függvényeit.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egyéni rendszerkép készítése a Docker használatával.
> * Egyéni rendszerkép közzététele egy tárolójegyzékben. 
> * Azure Storage-fiók létrehozása. 
> * Linux App Service-csomag létrehozása. 
> * Függvényalkalmazás üzembe helyezése a Docker Hubból.
> * Alkalmazásbeállítások hozzáadása a függvényalkalmazáshoz.

Az alap App Service-platformba beépített folyamatos integrációs funkció engedélyezési módjának elsajátítása. Függvényalkalmazását úgy is konfigurálhatja, hogy a tároló a rendszerkép Docker Hubban történő frissítésekor újra legyen telepítve.

> [!div class="nextstepaction"] 
> [Folyamatos üzembe helyezés a Web App for Containers használatával](../app-service/containers/app-service-linux-ci-cd.md)
