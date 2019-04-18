---
title: Az Azure Functions létrehozása Linux rendszerben egyéni rendszerkép használatával
description: Megismerheti, hogyan hozhat létre egyéni Linux-rendszerképeken futó Azure Functions-függvényeket.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 02/25/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 03e1ec58b0ef3ad50a04f82ced7d20119ab3ef5b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59491457"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Függvény létrehozása Linux rendszerben egyéni rendszerkép használatával

Az Azure Functions lehetővé teszi, hogy a függvényeit Linux rendszerben egy saját egyéni tárolóban üzemeltesse. Emellett [egy alapértelmezett Azure App Service-tárolóban is üzemeltetheti](functions-create-first-azure-function-azure-cli-linux.md). Ez a szolgáltatás csak [a Functions 2.x verziójú futtatókörnyezet](functions-versions.md).

Ez az oktatóanyag bemutatja, hogyan helyezhetők üzembe a függvények az Azure-ban egyéni Docker-rendszerképként. Ez a minta akkor hasznos, ha testre kell szabnia a beépített App Service-tároló rendszerképét. Érdemes lehet egyéni rendszerképet használnia, ha a funkciók egy adott nyelvi verziójára vagy egy konkrét függőségre vagy konfigurációra van szükség, amely a beépített rendszerképben nem biztosított. Alaplemezképek támogatott Azure-funkciók találhatók a [Azure Functions alapjául lemezképek tárház](https://hub.docker.com/_/microsoft-azure-functions-base). [Podpora Pythonu](functions-reference-python.md) jelenleg előzetes verzióban érhető el.

Ez az oktatóanyag bemutatja, hogyan hozhat létre függvényeket egyéni Linux-rendszerképekben az Azure Functions Core Tools használatával. A rendszerképet egy, az Azure CLI-vel létrehozott függvényalkalmazásban teszi közzé az Azure-ban.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Függvényalkalmazás és a Docker-fájl létrehozása a Core Tools használatával.
> * Egyéni rendszerkép készítése a Docker használatával.
> * Egyéni rendszerkép közzététele egy tárolójegyzékben.
> * Azure Storage-fiók létrehozása.
> * Linux App Service-csomag létrehozása.
> * Függvényalkalmazás üzembe helyezése a Docker Hubból.
> * Alkalmazásbeállítások hozzáadása a függvényalkalmazáshoz.
> * Folyamatos üzembe helyezés engedélyezése

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépeken követheti.  

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához az alábbiakkal kell rendelkeznie:

* Telepítse az [Azure Core Tools 2.x verzióját](functions-run-local.md#v2).

* Telepítse az [Azure CLI-t]( /cli/azure/install-azure-cli). Ehhez a cikkhez az Azure CLI 2.0-ás vagy újabb verziója szükséges. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`.  
Használhatja az [Azure Cloud Shellt](https://shell.azure.com/bash) is.

* Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>A helyi függvényalkalmazás-projekt létrehozása

Futtassa a következő parancsot a parancssorból, hogy létrehozzon az aktuális helyi könyvtár `MyFunctionProj` mappájába egy függvényalkalmazás-projektet.

```bash
func init MyFunctionProj --docker
```

A `--docker` paraméter hozzáadása esetén létrejön egy Docker-fájl a projekthez. Ezzel a fájllal hozható létre az egyéni tároló a projekt futtatásához. A használt alap rendszerkép a feldolgozói futtatókörnyezet választott nyelvétől függ.  

Amikor a rendszer kéri, válasszon ki egy feldolgozói futtatókörnyezetet az alábbi nyelvek közül:

* `dotnet`: létrehoz egy .NET osztálytárprojektet (.csproj).
* `node`: létrehoz egy JavaScript-projektet.
* `python`: egy Python-projektet hoz létre.

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

A parancs végrehajtásakor a következő kimenethez hasonlót fog látni:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

Az alábbi paranccsal léphet az új `MyFunctionProj` projektmappára.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>A rendszerkép létrehozása a Docker-fájlból

Tekintse meg a _Docker-fájlt_ a projekt gyökérmappájában. Ez a fájl írja le a függvényalkalmazás a Linux rendszeren való futtatásához szükséges környezetet. Az alábbi példa egy Docker-fájl, amely egy függvényalkalmazást a JavaScript (Node.js) feldolgozói futtatókörnyezetben futtató tárolót hoz létre: 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Az Azure Functions támogatott alaplemezképek teljes listája megtalálható a [Azure Functions alaplemezkép lapján](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>A `build` parancs futtatása
A gyökérmappában futtassa a [docker build](https://docs.docker.com/engine/reference/commandline/build/) parancsot, és adjon meg egy nevet (`mydockerimage`) és egy címkét (`v1.0.0`). A `<docker-id>` helyére a Docker Hub-fiók azonosítóját írja. Ez a parancs létrehozza a tároló Docker-rendszerképét.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

A parancs végrehajtásakor az alábbihoz hasonló kimenet látható, ami jelen esetben egy JavaScript feldolgozói futtatókörnyezethez kapcsolódik:

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>A rendszerkép helyi tesztelése
Ellenőrizze, hogy a létrehozott rendszerkép működik-e – ehhez futtassa a Docker-rendszerképet egy helyi tárolóban. Adja ki a [docker run](https://docs.docker.com/engine/reference/commandline/run/) parancsot, és adja meg a rendszerkép nevét és címkéjét. Ügyeljen arra, hogy a `-p` argumentum használatával megadja a portot is.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Amikor az egyéni rendszerkép fut a helyi Docker-tárolóban, a függvényalkalmazás és -tároló megfelelő működésének ellenőrzéséhez keresse fel a böngészőben a <http://localhost:8080> címet.

![Tesztelje helyben a függvényalkalmazást.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Ha kívánja, ismét tesztelheti a függvényt, ezúttal a helyi tárolóban a következő URL-cím használatával:

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

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
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

A rendszerképet most már használhatja üzembehelyezési forrásként egy új függvényalkalmazáshoz az Azure-ban.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Linux App Service-csomag létrehozása

A Functions üzemeltetése Linuxon jelenleg a használatalapú csomagok esetében nem támogatott. A Linux rendszerű tárolóalkalmazásokat egy Linux App Service-csomagban kell üzemeltetnie. További információk az üzemeltetésről: [Azure Functions szolgáltatási csomagok összehasonlítása](functions-scale.md).

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

A _deployment-container-image-name_ paraméter jelöli a Docker Hubon tárolt rendszerképet, amelynek használatával a függvényalkalmazást létrehozzuk. Használja a [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) parancsot az üzembe helyezéshez használt rendszerkép kapcsolatos információk megtekintéséhez. Használja a [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) parancsot egy másik lemezkép alapján üzembe helyezéséhez.

## <a name="configure-the-function-app"></a>A függvényalkalmazás konfigurálása

A függvénynek a kapcsolati sztringre az alapértelmezett tárfiókhoz való kapcsolódáshoz van szüksége. Ha az egyéni rendszerképet egy privát tárolófiókon teszi közzé, ehelyett ezeket az alkalmazásbeállításokat környezeti változókként kell megadnia a Docker-fájlban az [ENV utasítás](https://docs.docker.com/engine/reference/builder/#env) vagy egy hasonló utasítás használatával.

Ebben az esetben a `<storage_name>` a létrehozott tárfiók neve. Kérje le a kapcsolati sztringet az [az storage account show-connection-string](/cli/azure/storage/account) paranccsal. Adja hozzá ezeket az alkalmazásbeállításokat a függvényalkalmazáshoz az [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) paranccsal.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Ha a tároló privát, lenne, valamint a következő alkalmazás beállításainak megadása  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Állítsa le és indítsa el a függvényalkalmazás ezekre az értékekre, visszajusson az kell

Most tesztelheti a Linuxon futó Azure-függvényeit.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Az ajánlott úgy a függvények végrehajtásának figyelése, hogy a függvényalkalmazás integrálása az Azure Application Insights. Ha függvényalkalmazást hoz létre az Azure Portalon, az integráció, készen áll alapértelmezés szerint. Azonban a függvényalkalmazás létrehozásakor az Azure CLI használatával az integráció az Azure-ban a függvényalkalmazás nem fejeződött.

Az Application Insights engedélyezése a függvényalkalmazás számára:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

További tudnivalókért lásd: [figyelése az Azure Functions](functions-monitoring.md).

## <a name="enable-continuous-deployment"></a>Folyamatos üzembe helyezés engedélyezése

Tárolók használatával járó előnyöket egyik folyamatban van, képes a frissítések automatikus telepítése a tárolók a beállításjegyzékben frissítésekor. A folyamatos üzembe helyezés engedélyezése a [az functionapp deployment adattároló konfigurációját](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) parancsot.

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Ez a parancs visszaadja az üzembe helyezés a webhook URL-cím engedélyezve van a folyamatos üzembe helyezés után. Is használhatja a [az functionapp deployment tároló show-cd-URL-címe](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) parancsba is, az URL-címet. 

Másolja az üzembe helyezési URL-címet, és tallózással keresse meg a DockerHub-tárházban, válassza a **Webhookok** fülre, írja be egy **Webhook neve** webhook, illessze be az URL-címet **Webhook URL-CÍMÉT**, és válassza a a plusz jelre (**+**).

![Hozzáadja a webhookot a DockerHub tárházban](media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

A webhook értéke a csatolt kép DockerHub a frissítéseket a függvényalkalmazás, letöltik és telepítik a legfrissebb képet eredményez.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Függvényalkalmazás és a Docker-fájl létrehozása a Core Tools használatával.
> * Egyéni rendszerkép készítése a Docker használatával.
> * Egyéni rendszerkép közzététele egy tárolójegyzékben.
> * Azure Storage-fiók létrehozása.
> * Linux App Service-csomag létrehozása.
> * Függvényalkalmazás üzembe helyezése a Docker Hubból.
> * Alkalmazásbeállítások hozzáadása a függvényalkalmazáshoz.

Az alap App Service-platformba beépített folyamatos integrációs funkció engedélyezési módjának elsajátítása. Függvényalkalmazását úgy is konfigurálhatja, hogy a tároló a rendszerkép Docker Hubban történő frissítésekor újra legyen telepítve.

> [!div class="nextstepaction"] 
> [Folyamatos üzembe helyezés a Web App for Containers használatával](../app-service/containers/app-service-linux-ci-cd.md)
