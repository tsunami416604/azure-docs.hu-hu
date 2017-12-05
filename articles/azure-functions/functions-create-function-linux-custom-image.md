---
title: "Hozzon létre egy függvényt (előzetes verzió) egyéni lemezkép használatával Linux |} Microsoft Docs"
description: "Útmutató az Azure Functions futó Linux egyéni lemezkép létrehozásához."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 9ba5f45034561f8d897676e8cc4b1a59945403b8
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Függvény létrehozása Linux (előzetes verzió) egyéni lemezkép használatával

Az Azure Functions lehetővé teszi a funkciók Linux saját egyéni tárolóban tárolni. Ez a funkció jelenleg előzetes verzió. Emellett [alapértelmezett Azure App Service tárolóba állomás](functions-create-first-azure-function-azure-cli-linux.md).  

Ebben az oktatóanyagban elsajátíthatja, hogyan egyéni Docker képként függvény alkalmazás telepítése. Ebben a mintában akkor hasznos, ha a beépített App Service-tároló lemezkép testreszabásához szükséges. Érdemes lehet egy egyéni lemezképet használja, ha a funkciók egy adott nyelvhez verziójára van szükség, vagy egy adott függőségekkel vagy az, hogy a beépített lemezképben nincs megadva konfigurációs szükséges.

Ez az oktatóanyag bemutatja, hogyan hozhat létre, és egyéni lemezkép leküldése Docker Hub Azure Functions használatával. Ezt követően az a lemezkép központi telepítési forrásként függvény Linux rendszeren futó alkalmazások. Docker segítségével hozza létre, és a lemezkép leküldéses. Az Azure parancssori felület használatával függvény-alkalmazás létrehozása és a Docker Hub a lemezkép központi telepítését. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy egyéni lemezkép Docker.
> * Egyéni lemezkép közzététele tároló beállításjegyzékbeli. 
> * Hozzon létre egy Azure Storage-fiókot. 
> * Hozzon létre egy Linux App Service-csomagot. 
> * A Docker Hub függvény alkalmazás telepítése.
> * Alkalmazásbeállítások hozzáadása a függvény alkalmazáshoz. 

Az alábbi lépéseket a Mac, a Windows vagy Linux számítógép támogatottak.  

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Git](https://git-scm.com/downloads)
* Az aktív [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/get-started/#setup)
* A [Docker Hub fiók](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>A minta letöltése

Egy terminálablakot futtassa a következő parancsot klónozza a sample app tárházat a helyi számítógépen, majd nyissa meg azt a könyvtárat a mintakódot tartalmazó.

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>A lemezkép a Docker-fájlból

A Git-tárházban, vessen egy pillantást a _Dockerfile_. Ez a fájl ismerteti a környezetben, amely a függvény app Linux rendszeren futtatásához szükséges. 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> Esetén a személyes tárolót beállításjegyzék képet, adjon a kapcsolat beállításait az függvény alkalmazás használatával **ENV** a Dockerfile változókat. Ez az oktatóanyag nem garantálja, hogy használja-e személyes beállításjegyzékbeli, mert a kapcsolat beállításai vannak [adhatók hozzá a telepítést követően az Azure parancssori felület](#configure-the-function-app) ajánlott biztonsági eljárásként.   

### <a name="run-the-build-command"></a>A Build parancs futtatása
A Docker-lemezkép létrehozásához futtassa a `docker build` parancsot, és adjon meg egy nevet, `mydockerimage`, és címkében `v1.0.0`. Cserélje le `<docker-id>` a Docker hubbal fiók azonosítóját.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

A parancs a következőhöz hasonló kimenetet hoz létre:

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

### <a name="test-the-image-locally"></a>A lemezkép helyi tesztelése
Győződjön meg arról, hogy működik-e a beépített lemezképet egy helyi tárolóban a Docker lemezkép futtatásával. A probléma a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancsot, és átadni nevét és a lemezkép címke. Adja meg a portot a `-p` argumentum.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Az egyéni lemezképet futtató helyi Docker-tároló, ellenőrizze a függvény alkalmazás és a tároló vannak jól működő meghajtói tallózással <8080>.

![A függvény alkalmazás helyi tesztelése.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Miután hav ellenőrizni a függvény alkalmazást a tárolóban, akkor állítsa le a végrehajtása. Most tolható ki az egyéni lemezképet a Docker központ fiókjához.

## <a name="push-the-custom-image-to-docker-hub"></a>Az egyéni rendszerképet leküldése Docker Hub

A beállításjegyzék olyan alkalmazás, amely üzemelteti a képek és szolgáltatások kép és a tároló szolgáltatásokat biztosít. Ahhoz, hogy megoszthatja a képet, meg kell leküldéses a beállításjegyzékhez. Docker, amely lehetővé teszi a saját adattárak nyilvános vagy titkos beállításjegyzékbeli Docker lemezképek is. 

Mielőtt tolható ki a lemezkép, be kell jelentkeznie Docker Hub használata a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) parancsot. Cserélje le `<docker-id>` a fiók nevét és jelszavát a konzolba a parancssorba írja be. Egyéb Docker Hub jelszóbeállítások, tekintse meg a [docker bejelentkezési parancs dokumentáció](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id> 
```

"Sikeres bejelentkezés" üzenet jelzi, hogy van bejelentkezve. Miután bejelentkezett, akkor leküldése a kép Docker központ használatával a [docker leküldéses](https://docs.docker.com/engine/reference/commandline/push/) parancsot.

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Győződjön meg arról, hogy az sikeres volt. a parancs megvizsgálásával leküldéses kimenetét.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
Most használhatja a lemezképet a központi telepítési forrásként egy új funkció alkalmazás az Azure-ban. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez a témakör igényel-e az Azure parancssori felület 2.0.21 verzió vagy újabb. Futtatás `az --version` található verzió található. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>A Linux App Service-csomag létrehozása

Linux futtató függvények jelenleg nem támogatott fogyasztás csomagok. A Linux App Service-csomagot kell használni. Üzemeltetési kapcsolatos további információkért lásd: [összehasonlítása az Azure Functions üzemeltetési tervek](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]


## <a name="create-and-deploy-the-custom-image"></a>Hozzon létre, és az egyéni lemezkép

A függvényalkalmazás biztosítja a funkciók végrehajtását. Függvény-alkalmazás létrehozása a Docker Hub-lemezkép használatával a [az functionapp létrehozása](/cli/azure/functionapp#create) parancsot. 

Az alábbi parancs helyettesítse be egy egyedi alkalmazás függvénynév válaszoknál láthatja a `<app_name>` helyőrző és a tárfiók neve `<storage_name>`. Az `<app_name>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. Mint korábban `<docker-id>` a Docker-fiók neve.

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

A _telepítési-tároló-lemezképnév_ paraméter jelzi a Docker Hub használata a függvénynek app létrehozásához tárolt lemezképet. 


## <a name="configure-the-function-app"></a>A függvény alkalmazás konfigurálása

A függvény a kapcsolódási karakterlánc az alapértelmezett tárfiók való kapcsolódáshoz szükséges. Ha az egyéni lemezképet közzéteszi a magánfelhő tároló-fiókra, kell helyette állítja be ezen alkalmazás beállításai a környezeti változók a Dockerfile használatával a [ENV utasítás](https://docs.docker.com/engine/reference/builder/#env), vagy azzal egyenértékű. 

Ebben az esetben `<storage_account>` létrehozott tárfiók neve. A kapcsolati karakterlánc beolvasása a [az storage-fiók megjelenítése-kapcsolat-karakterlánc](/cli/azure/storage/account#show-connection-string) parancsot. Ezek a beállítások alkalmazás hozzáadása a függvény alkalmazás a [az functionapp appsettings konfiguráció](/cli/azure/functionapp/config/appsettings#set) parancsot.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

A Linux az Azure-ban futó függvények most tesztelheti.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre egy egyéni lemezkép Docker.
> * Egyéni lemezkép közzététele tároló beállításjegyzékbeli. 
> * Hozzon létre egy Azure Storage-fiókot. 
> * Hozzon létre egy Linux App Service-csomagot. 
> * A Docker Hub függvény alkalmazás telepítése.
> * Alkalmazásbeállítások hozzáadása a függvény alkalmazáshoz.

További tudnivalók az Azure Functions helyileg használata az Azure Functions Core eszközök fejlesztése.

> [!div class="nextstepaction"] 
> [Kód és az Azure Functions helyi tesztelése](functions-run-local.md)
