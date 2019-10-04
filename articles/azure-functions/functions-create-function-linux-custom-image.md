---
title: Azure Functions létrehozása Linuxon egyéni rendszerkép használatával
description: Megismerheti, hogyan hozhat létre egyéni Linux-rendszerképeken futó Azure Functions-függvényeket.
author: ggailey777
ms.author: glenga
ms.date: 09/27/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: b4fd931142f8a3daf11b0540241323453e25ea4f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839061"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Függvény létrehozása Linux rendszerben egyéni rendszerkép használatával

Az Azure Functions lehetővé teszi, hogy a függvényeit Linux rendszerben egy saját egyéni tárolóban üzemeltesse. Emellett [egy alapértelmezett Azure App Service-tárolóban is üzemeltetheti](functions-create-first-azure-function-azure-cli-linux.md). Ehhez a funkcióhoz [a functions 2. x futtatókörnyezete](functions-versions.md)szükséges.

Ez az oktatóanyag bemutatja, hogyan helyezhetők üzembe a függvények az Azure-ban egyéni Docker-rendszerképként. Ez a minta akkor hasznos, ha testre kell szabnia a beépített tároló rendszerképét. Érdemes lehet egyéni rendszerképet használnia, ha a funkciók egy adott nyelvi verziójára vagy egy konkrét függőségre vagy konfigurációra van szükség, amely a beépített rendszerképben nem biztosított. A Azure Functions támogatott alaplemezképei a [Azure functions Base images](https://hub.docker.com/_/microsoft-azure-functions-base)tárházban találhatók. 

Ez az oktatóanyag bemutatja, hogyan hozhat létre függvényeket egyéni Linux-rendszerképekben az Azure Functions Core Tools használatával. A rendszerképet egy, az Azure CLI-vel létrehozott függvényalkalmazásban teszi közzé az Azure-ban. Később frissíti a függvényt az Azure üzenetsor-tárolóhoz való kapcsolódáshoz. Azt is engedélyezi.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Függvényalkalmazás és a Docker-fájl létrehozása a Core Tools használatával.
> * Egyéni rendszerkép készítése a Docker használatával.
> * Egyéni rendszerkép közzététele egy tárolójegyzékben.
> * Azure Storage-fiók létrehozása.
> * Hozzon létre egy prémium szintű üzemeltetési csomagot.
> * Függvényalkalmazás üzembe helyezése a Docker Hubból.
> * Alkalmazásbeállítások hozzáadása a függvényalkalmazáshoz.
> * Folyamatos üzembe helyezés engedélyezése.
> * Engedélyezze az SSH-kapcsolatokat a tárolóhoz.
> * Adja hozzá a várólista-tároló kimeneti kötését. 
> * Application Insights figyelés hozzáadása.

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató számítógépeken követheti. 

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához az alábbiakkal kell rendelkeznie:

* Telepítse az [Azure Core Tools 2.x verzióját](functions-run-local.md#v2).

* Telepítse az [Azure CLI-t]( /cli/azure/install-azure-cli). Ehhez a cikkhez az Azure CLI 2.0-ás vagy újabb verziója szükséges. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`.  
Használhatja az [Azure Cloud Shellt](https://shell.azure.com/bash) is.

* Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>A helyi projekt létrehozása

Futtassa a következő parancsot a parancssorból, hogy létrehozzon az aktuális helyi könyvtár `MyFunctionProj` mappájába egy függvényalkalmazás-projektet. Python-projektekhez [virtuális környezetben kell futnia](functions-create-first-function-python.md#create-and-activate-a-virtual-environment-optional).

```bash
func init MyFunctionProj --docker
```

A `--docker` paraméter hozzáadása esetén létrejön egy Docker-fájl a projekthez. Ezzel a fájllal hozható létre az egyéni tároló a projekt futtatásához. A használt alap rendszerkép a feldolgozói futtatókörnyezet választott nyelvétől függ.  

Amikor a rendszer kéri, válasszon ki egy feldolgozói futtatókörnyezetet az alábbi nyelvek közül:

* `dotnet`: létrehoz egy .NET Core Class Library-projektet (. csproj).
* `node`: létrehoz egy JavaScript-projektet.
* `python`: létrehoz egy Python-projektet.  

Az alábbi paranccsal léphet az új `MyFunctionProj` projektmappára.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Build a Docker-fájlból

Tekintse meg a _Docker-fájlt_ a projekt gyökérmappájában. Ez a fájl írja le a függvényalkalmazás a Linux rendszeren való futtatásához szükséges környezetet. Az alábbi példa egy Docker-fájl, amely egy függvényalkalmazást a JavaScript (Node.js) feldolgozói futtatókörnyezetben futtató tárolót hoz létre: 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> A Azure Functions által támogatott alaplemezképek teljes listája megtalálható a [Azure functions alap lemezképe lapon](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>A `build` parancs futtatása

A gyökérmappában futtassa a [docker build](https://docs.docker.com/engine/reference/commandline/build/) parancsot, és adjon meg egy nevet (`mydockerimage`) és egy címkét (`v1.0.0`). A `<docker-id>` helyére a Docker Hub-fiók azonosítóját írja. Ez a parancs létrehozza a tároló Docker-rendszerképét.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Ha a parancs befejeződik, az új tárolót helyileg is futtathatja.

### <a name="run-the-image-locally"></a>A rendszerkép helyi futtatása
Ellenőrizze, hogy a létrehozott rendszerkép működik-e – ehhez futtassa a Docker-rendszerképet egy helyi tárolóban. Adja ki a [docker run](https://docs.docker.com/engine/reference/commandline/run/) parancsot, és adja meg a rendszerkép nevét és címkéjét. Ügyeljen arra, hogy a `-p` argumentum használatával megadja a portot is.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Amikor az egyéni rendszerkép fut a helyi Docker-tárolóban, a függvényalkalmazás és -tároló megfelelő működésének ellenőrzéséhez keresse fel a böngészőben a <http://localhost:8080> címet.

![Futtassa helyileg a Function alkalmazást.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> Ezen a ponton, amikor megpróbálja meghívni az adott HTTP-függvényt, HTTP 401-es hibaüzenetet kap. Ennek az az oka, hogy a függvény a helyi tárolóban fut, ahogyan az az Azure-ban lenne, ami azt jelenti, hogy a függvény kulcsának megadása kötelező. Mivel a tároló még nem lett közzétéve egy Function alkalmazásban, nincs elérhető funkcióbillentyű. Később láthatja, hogy amikor a fő eszközöket használja a tároló közzétételéhez, a funkciógombok megjelennek Önnek. Ha a helyi tárolóban futó függvényt szeretné tesztelni, az [engedélyezési kulcsot](functions-bindings-http-webhook.md#authorization-keys) `anonymous` értékre módosíthatja. 

Miután ellenőrizte a függvényalkalmazást a tárolóban, állítsa le a végrehajtást. Most leküldheti az egyéni rendszerképet a Docker Hub-fiókjába.

## <a name="push-to-docker-hub"></a>Leküldés a Docker hub-ra

A tárolójegyzék egy olyan alkalmazás, amely elérhetővé teszi a rendszerképeket, valamint rendszerkép- és tárolószolgáltatásokat is biztosít. A rendszerkép megosztásához le kell küldenie azt a tárolójegyzékbe. A Docker Hub egy olyan tárolójegyzék a Docker-rendszerképek számára, amely saját nyilvános vagy privát adattárak üzemeltetését teszi lehetővé.

A rendszerkép leküldése előtt be kell jelentkeznie a Docker Hubra a [docker login](https://docs.docker.com/engine/reference/commandline/login/) paranccsal. A `<docker-id>` helyére a fiók nevét írja, majd adja meg a jelszót a konzolban, amikor a rendszer kéri. Egyéb Docker Hub-jelszóbeállításokért tekintse meg a [docker login parancs dokumentációját](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

A "sikeres bejelentkezés" üzenet megerősíti, hogy bejelentkezett. Miután bejelentkezett, a rendszerképet a Docker Hubba a [docker push](https://docs.docker.com/engine/reference/commandline/push/) paranccsal küldheti le.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

A leküldéses művelet sikeres elvégzése után a rendszerképet használhatja egy új Function alkalmazás üzembe helyezési forrásaként az Azure-ban.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Prémium csomag létrehozása

A [dedikált (App Service) csomagok](functions-scale.md#app-service-plan) és [prémium csomagok](functions-premium-plan.md#features)által támogatott egyéni functions-tárolók Linux-üzemeltetése. Ez az oktatóanyag egy prémium szintű csomagot használ, amely igény szerint méretezhető. További információk az üzemeltetésről: [Azure Functions szolgáltatási csomagok összehasonlítása](functions-scale.md).

Az alábbi példa egy `myPremiumPlan` nevű prémium csomagot hoz létre az **1** . szintű prémium szintű 1. csomag (`--sku EP1`), az USA nyugati régiójában (`-location WestUS`), valamint egy Linux-tárolóban (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Alkalmazás létrehozása a rendszerképből

A Function alkalmazás kezeli a függvények végrehajtását a üzemeltetési tervben. A függvényalkalmazásokat a Docker Hub-rendszerképekből az [az functionapp create](/cli/azure/functionapp#az-functionapp-create) parancs használatával hozhatja létre.

A következő parancsban a `<app_name>` helyőrző helyett írjon be egy egyedi függvényalkalmazás-nevet, a `<storage_name>` helyőrző helyett pedig a tárfiók nevét. Az `<app_name>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. Ahogy korábban is, a `<docker-id>` a Docker-fiók neve.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

A _deployment-container-image-name_ paraméter jelöli a Docker Hubon tárolt rendszerképet, amelynek használatával a függvényalkalmazást létrehozzuk. Az az [functionapp config Container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) paranccsal tekintheti meg az üzembe helyezéshez használt rendszerképpel kapcsolatos információkat. Használja az az [functionapp config Container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) parancsot egy másik rendszerképből való üzembe helyezéshez.

## <a name="configure-the-function-app"></a>A függvényalkalmazás konfigurálása

A függvénynek a kapcsolati sztringre az alapértelmezett tárfiókhoz való kapcsolódáshoz van szüksége. Amikor egyéni rendszerképet tesz közzé egy privát Container-fiókban, ehelyett környezeti változókként kell beállítania ezeket az Alkalmazásbeállítások a Docker az [env utasítás](https://docs.docker.com/engine/reference/builder/#env)használatával, vagy valami hasonló.

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
> Ha a tároló privát, akkor a következő Alkalmazásbeállítások is be kell állítania  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Ezen értékek kiválasztásához le kell állítania, majd el kell indítania a Function alkalmazást

## <a name="verify-your-functions"></a>A függvények ellenőrzése

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

A létrehozott HTTP-trigger függvényhez egy [funkcióbillentyű](functions-bindings-http-webhook.md#authorization-keys) szükséges a végpont meghívásakor. Jelenleg a legegyszerűbb módszer a függvény URL-címének lekérésére, a kulcsot is beleértve, a [Azure Portal]. 

> [!TIP]
> A funkcióbillentyűk a [Kulcskezelő API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)-k segítségével is beszerezhetők, amelyekhez [tulajdonosi jogkivonatot](/cli/azure/account#az-account-get-access-token)kell bemutatnia a hitelesítéshez.

Keresse meg az új Function alkalmazást a [Azure Portal] írja be a Function alkalmazás nevét a lap tetején található **keresőmezőbe** , és válassza ki a **app Service** erőforrást.

Válassza ki a **MyHttpTrigger** függvényt, válassza a **</> a függvény URL-címének beolvasása** > **alapértelmezett (Function Key)**  > **Másolás**elemet.

![A függvény URL-címének másolása az Azure portálról](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

Ebben az URL-ben a függvény kulcsa a `code` lekérdezési paraméter. 

> [!NOTE]  
> Mivel a Function alkalmazás tárolóként van telepítve, nem módosíthatja a függvény kódját a portálon. Ehelyett frissítenie kell a projektet a helyi tárolóban, és újra közzé kell tennie az Azure-ban.

Illessze be a függvény URL-címét a böngésző címsorába. Az URL-cím végéhez adja hozzá a `&name=<yourname>` lekérdezési sztring értéket, majd nyomja le az `Enter` billentyűt a billentyűzeten a kérés végrehajtásához. Ekkor a függvény által visszaadott válasz jelenik meg a böngészőben.

Az alábbi példa a böngészőben visszaadott választ mutatja:

![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

A kérelem URL-címe alapértelmezés szerint tartalmazza a függvény HTTP protokollon keresztüli eléréséhez szükséges kulcsot. 

## <a name="enable-continuous-deployment"></a>Folyamatos üzembe helyezés engedélyezése

A tárolók használatának egyik előnye, hogy támogatja a folyamatos üzembe helyezést. A functions lehetővé teszi a frissítések automatikus központi telepítését, amikor a tároló frissül a beállításjegyzékben. Engedélyezze a folyamatos üzembe helyezést az az [functionapp Deployment Container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) paranccsal.

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Ez a parancs visszaadja a üzembe helyezési webhook URL-címét a folyamatos üzembe helyezés engedélyezése után. Az URL-cím visszaküldéséhez használhatja az az [functionapp Deployment Container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) parancsot is. 

Másolja a telepítési URL-címet, és keresse meg a DockerHub-tárházat, válassza a **webhookok** fület, írja be a webhook **nevét** , illessze be az URL-címet a **webhook URL-címébe**, majd válassza a pluszjelet ( **+** ).

![Webhook hozzáadása a DockerHub-tárházban](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

A webhook készlettel a DockerHub csatolt rendszerképének minden frissítése a Function alkalmazás letöltésével és a legújabb rendszerkép telepítésével jár.

## <a name="enable-ssh-connections"></a>SSH-kapcsolatok engedélyezése

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. Ha engedélyezve van az SSH, App Service speciális eszközökkel (kudu) is csatlakozhat a tárolóhoz. Ahhoz, hogy az SSH használatával könnyedén csatlakozhasson a tárolóhoz, a functions olyan alaprendszerképet biztosít, amely már engedélyezve van az SSH-val. 

### <a name="change-the-base-image"></a>Az alaprendszerkép módosítása

A Docker fűzze hozzá a `-appservice` karakterláncot a `FROM` utasításban található alaprendszerképhez, amely egy JavaScript-projekthez hasonlóan néz ki.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

A két alaprendszerkép különbségei lehetővé teszik az SSH-kapcsolatokat a tárolóba. Ezek a különbségek részletesen [jelennek meg a app Services oktatóanyagban](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

### <a name="rebuild-and-redeploy-the-image"></a>A rendszerkép újraépítése és újbóli üzembe helyezése

A gyökérkönyvtárban futtassa újra a [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) parancsot az előzőekben lecserélve a Docker hub-fiók azonosítójának `<docker-id>` helyére. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Küldje vissza a frissített képet a Docker hub-ra.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

A rendszer újratelepíti a frissített lemezképet a Function alkalmazásba.

### <a name="connect-to-your-container-in-azure"></a>Kapcsolódás a tárolóhoz az Azure-ban

A böngészőben nyissa meg az alábbi speciális eszközöket (kudu) `scm.` végpontot a Function app-tárolóhoz, és cserélje le a `<app_name>` értéket a Function alkalmazás nevével.

```
https://<app_name>.scm.azurewebsites.net/
```

Jelentkezzen be az Azure-fiókjába, majd válassza az **SSH** fület SSH-kapcsolatok létrehozásához a tárolóban.

A kapcsolódás után futtassa a `top` parancsot az aktuálisan futó folyamatok megtekintéséhez. 

![Egy SSH-munkamenetben futó Linux-alapú legfelső szintű parancs.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Írás a várólista-tárolóba

A functions lehetővé teszi az Azure-szolgáltatások és egyéb erőforrások a funkciókhoz való összekapcsolását anélkül, hogy saját integrációs kódot kellene írnia. Ezek a *kötések*, amelyek a bemeneti és a kimeneti adatokat jelölik, a függvény definíciójában vannak deklarálva. A kötések adatait paraméterként a függvény kapja meg. Az *trigger* egy speciális típusú bemeneti kötés. Bár a függvénynek csak egy triggere van, több bemeneti és kimeneti kötés is lehet. További információ: [Azure functions triggerek és kötések fogalmai](functions-triggers-bindings.md).

Ez a szakasz bemutatja, hogyan integrálhatja a függvényt egy Azure Storage-üzenetsor használatával. Az ehhez a függvényhez hozzáadott kimeneti kötés egy HTTP-kérelemből adatokat ír a várólistában lévő üzenetbe.

### <a name="download-the-function-app-settings"></a>A függvény alkalmazás beállításainak letöltése

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Bővítmény-csomagok engedélyezése

Mivel a várólista-tároló kimeneti kötését használja, a projekt futtatása előtt telepítenie kell a Storage-kötések bővítményt. 

#### <a name="javascriptpython"></a>JavaScript/Python

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

#### <a name="c-class-library"></a>C#osztály könyvtára

A HTTP-és időzítő-eseményindítók kivételével a kötések kiterjesztési csomagként vannak implementálva. Futtassa a következő [DotNet-csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) parancsot a terminál ablakban a tárolási bővítmény csomagjának a projekthez való hozzáadásához.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> A Visual Studio használata esetén a NuGet csomagkezelő használatával is hozzáadhatja ezt a csomagot.

Most hozzáadhat egy tárolási kimeneti kötést a projekthez.

### <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A functions esetében minden típusú kötéshez `direction`, `type` és egy egyedi `name` szükséges, amely a function. JSON fájlban adható meg. Az attribútumok definiálásának módja a Function alkalmazás nyelvétől függ.

####  <a name="javascriptpython"></a>JavaScript/Python

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

#### <a name="c-class-library"></a>C#osztály könyvtára

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

### <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

A kötés meghatározása után a kötés `name` értékkel férhet hozzá a függvény aláírása attribútumként. Kimeneti kötés használatával nem szükséges az Azure Storage SDK-kód használata hitelesítéshez, üzenetsor-hivatkozás beszerzése vagy az adatírás. A functions futtatókörnyezet és a várólista kimeneti kötése elvégzi ezeket a feladatokat.

#### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

#### <a name="python"></a>Python

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

#### <a name="c-class-library"></a>C#osztály könyvtára

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

### <a name="update-the-hosted-container"></a>Az üzemeltetett tároló frissítése

A gyökérkönyvtárban futtassa újra a [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) parancsot, és ezúttal frissítse a címkén lévő verziót `v1.0.2` értékre. Ahogy korábban is, cserélje le a `<docker-id>` értéket a Docker hub-fiók azonosítójával. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Küldje vissza a frissített képet a tárházba.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>A frissítések ellenőrzése az Azure-ban

A függvény elindításához ugyanazt az URL-címet használja, mint a böngészőben. Ugyanezt a választ kell látnia. Ezúttal azonban a `name` paraméternek átadott karakterlánc a `outqueue` tárolási várólistába íródik.

### <a name="set-the-storage-account-connection"></a>A Storage-fiók kapcsolatainak beállítása

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>A tárolási várólista lekérdezése

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen telepítette az egyéni tárolót egy Azure-beli Function-alkalmazásba, érdemes megfontolnia a következő témaköröket:

+ [Figyelési függvények](functions-monitoring.md)
+ [Méretezési és üzemeltetési lehetőségek](functions-scale.md)
+ [Kubernetes-alapú kiszolgáló nélküli üzemeltetés](functions-kubernetes-keda.md)

[Azure Portal]: https://portal.azure.com