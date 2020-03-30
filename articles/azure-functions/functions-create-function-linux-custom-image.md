---
title: Azure-függvények létrehozása Linuxon egyéni lemezkép használatával
description: Megismerheti, hogyan hozhat létre egyéni Linux-rendszerképeken futó Azure Functions-függvényeket.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 8c074c677c645dd03e3cf5288d82aa3e65720e8b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239629"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Függvény létrehozása Linuxon egyéni tároló használatával

Ebben az oktatóanyagban hozzon létre és telepítsen Python-kódot az Azure Functions-be egyéni Docker-tárolóként egy Linux alaprendszerkép használatával. Általában egyéni lemezképet használ, ha a függvények egy adott nyelvi verziót igényelnek, vagy olyan adott függőséget vagy konfigurációt, amelyet a beépített lemezkép nem biztosít.

Az Azure App Service alapértelmezett tárolóját is használhatja [a Linuxon üzemeltetett első függvény létrehozása](functions-create-first-azure-function-azure-cli-linux.md)című részen leírtak szerint. Az Azure Functions támogatott alaplemezképei az [Azure Functions alaplemeztárában](https://hub.docker.com/_/microsoft-azure-functions-base)találhatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy függvényalkalmazást és a Docker-fájlt az Azure Functions Core Tools használatával.
> * Egyéni rendszerkép készítése a Docker használatával.
> * Egyéni rendszerkép közzététele egy tárolójegyzékben.
> * Támogató erőforrások létrehozása az Azure-ban a függvényalkalmazáshoz
> * Függvényalkalmazás üzembe helyezése a Docker Hubból.
> * Alkalmazásbeállítások hozzáadása a függvényalkalmazáshoz.
> * Engedélyezze a folyamatos üzembe helyezést.
> * Engedélyezze az SSH-kapcsolatokat a tárolóhoz.
> * Várólista-tároló kimenetkötés hozzáadása. 

Az oktatóanyag ot bármely Windows, Mac OS vagy Linux rendszert futtató számítógépen követheti. Az oktatóanyag befejezése néhány amerikai dollár költségét fogja kitölteni az Azure-fiókjában.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Az [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846-os vagy újabb verziója
- Az [Azure CLI](/cli/azure/install-azure-cli) 2.0.77-es vagy újabb verziója
- Az [Azure Functions 2.x futásidejű](functions-versions.md)
- A következő nyelvi futásidejű összetevők:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2.x vagy újabb](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [Powershell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3.6 - 64 bites](https://www.python.org/downloads/release/python-3610/) vagy [Python 3.7 - 64 bites](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker között](https://docs.docker.com/install/)
- [Docker-azonosító](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Előfeltétel-ellenőrzés

1. Egy terminál- vagy parancsablakban futtassa `func --version` annak ellenőrzéséhez, hogy az Azure Functions Core Tools 2.7.1846-os vagy újabb verziójú-e.
1. Futtassa `az --version` annak ellenőrzéséhez, hogy az Azure CLI-verzió 2.0.76-os vagy újabb.
1. Futtassa `az login` a bejelentkezést az Azure-ba, és ellenőrizze az aktív előfizetést.
1. Fuss, `docker login` hogy jelentkezzen be a Dockerbe. Ez a parancs sikertelen, ha a Docker nem fut, ebben az esetben indítsa el a docker-t, és próbálkozzon újra a paranccsal.

## <a name="create-and-test-the-local-functions-project"></a>A helyi függvényprojekt létrehozása és tesztelése

1. Terminál- vagy parancssorban hozzon létre egy mappát az oktatóanyag számára egy megfelelő helyen, majd navigáljon a mappába.

1. Kövesse a [virtuális környezet létrehozása és aktiválása](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv) című, az oktatóanyaghoz használható virtuális környezet létrehozásához található utasításokat.

1. Futtassa a következő parancsot a választott nyelvhez, `LocalFunctionsProject`és hozzon létre egy függvényalkalmazás-projektet egy nevű mappában. A `--docker` beállítás létrehoz `Dockerfile` egy a projekt, amely meghatározza a megfelelő egyéni tároló azure functions és a kiválasztott futásidejű használható.

    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionsProject --worker-runtime dotnet --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language javascript --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionsProject --worker-runtime powershell --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionsProject --worker-runtime python --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language typescript --docker
    ```
    ::: zone-end
    
1. Navigálás a projekt mappájába:

    ```
    cd LocalFunctionsProject
    ```
    
1. Adjon hozzá egy függvényt a projekthez `--name` a következő paranccsal, ahol `--template` az argumentum a függvény egyedi neve, és az argumentum megadja a függvény eseményindítóját. `func new`hozzon létre egy olyan almappát, amely megfelel a projekt választott nyelvének megfelelő kódfájlt tartalmazó függvénynévnek, valamint egy *function.json*nevű konfigurációs fájlt.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. A függvény helyi teszteléséhez indítsa el a helyi Azure Functions futásidejű állomást a *LocalFunctionsProject* mappában:
   
    ::: zone pivot="programming-language-csharp"
    ```
    func start --build
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func start
    ```
    ::: zone-end    

    ::: zone pivot="programming-language-typescript"
    ```
    npm install
    ```

    ```
    npm start
    ```
    ::: zone-end

1. Ha megjelenik `HttpExample` a végpont a kimenetben, keresse meg a t. `http://localhost:7071/api/HttpExample?name=Functions` A böngészőnek meg kell jelenítenie egy olyan üzenetet, mint a "Hello, Functions" (a választott programozási nyelvtől függően kissé változatos).

1. A **Ctrl C billentyűvel**-**C** állítsa le az állomást.

## <a name="build-the-container-image-and-test-locally"></a>A tárolórendszerkép létrehozása és helyi tesztelése

1. (Nem kötelező) Vizsgálja meg a *Dockerfile" fájlt a *LocalFunctionsProj* mappában. A Dockerfile ismerteti a függvényalkalmazás Linuxon való futtatásához szükséges környezetet: 

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk AS installer-env

    COPY . /src/dotnet-function-app
    RUN cd /src/dotnet-function-app && \
        mkdir -p /home/site/wwwroot && \
        dotnet publish *.csproj --output /home/site/wwwroot
    
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/dotnet:2.0-appservice 
    FROM mcr.microsoft.com/azure-functions/dotnet:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/powershell:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY requirements.txt /
    RUN pip install -r /requirements.txt
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    > [!NOTE]
    > Az Azure Functions támogatott alaplemezképeinek teljes listája megtalálható az [Azure Functions alaplemezképe oldalon.](https://hub.docker.com/_/microsoft-azure-functions-base)
    
1. A *LocalFunctionsProject* mappában futtassa a [docker](https://docs.docker.com/engine/reference/commandline/build/) build `azurefunctionsimage`parancsot, `v1.0.0`és adjon meg egy nevet, a és a címkét. A `<docker_id>` helyére a Docker Hub-fiók azonosítóját írja. Ez a parancs létrehozza a tároló Docker-rendszerképét.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    Amikor a parancs befejeződik, futtathatja az új tárolót helyileg.
    
1. A build teszteléséhez futtassa a lemezképet egy helyi tárolóban a [docker run](https://docs.docker.com/engine/reference/commandline/run/) paranccsal, cserélje le újra `<docker_id>` a Docker-azonosítót, és adja hozzá a portok argumentumát: `-p 8080:80`

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Miután a kép egy helyi tárolóban `http://localhost:8080`fut, nyisson meg egy böngészőt a számára, amelynek meg kell jelenítenie az alábbi helyőrző képet. A rendszerkép jelenik meg ezen a ponton, mert a függvény fut a helyi tárolóban, ahogy az Azure-ban, ami `"authLevel": "function"` azt jelenti, hogy a *függvény.json* a tulajdonsággal meghatározott hozzáférési kulcs védi. A tároló még nem lett közzétéve egy függvényalkalmazásban az Azure-ban, így a kulcs még nem érhető el. Ha helyileg szeretné tesztelni, állítsa le a `"authLevel": "anonymous"`docker-t, módosítsa az engedélyezési tulajdonságot , építse újra a lemezképet, és indítsa újra a dockert. Ezután `"authLevel": "function"` állítsa vissza a *function.json*. További információt az [engedélyezési kulcsok című témakörben talál.](functions-bindings-http-webhook-trigger.md#authorization-keys)

    ![Helyőrző lemezkép, amely jelzi, hogy a tároló helyileg fut](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Miután ellenőrizte a függvényalkalmazást a tárolóban, állítsa le a docker-t **a Ctrl C-vel.**+**C**

## <a name="push-the-image-to-docker-hub"></a>A lemezkép leküldése a Docker Hubra

A Docker Hub egy tárolóbeállításjegyzék, amely rendszerképeket tárol, és lemezkép- és tárolószolgáltatásokat biztosít. A lemezkép megosztásához, amely magában foglalja az Azure-ba való üzembe helyezést, le kell küldnie egy beállításjegyzékbe.

1. Ha még nem jelentkezett be a Dockerbe, tegye meg a `<docker_id>` [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) paranccsal, és cserélje le a Docker-azonosítót. Ez a parancs a felhasználónevet és a jelszót kéri. A "Bejelentkezés sikeres" üzenet megerősíti, hogy be van jelentkezve.

    ```
    docker login
    ```
    
1. Miután bejelentkezett, leküldéses a rendszerképet a Docker Hub a `<docker_id>` [docker push](https://docs.docker.com/engine/reference/commandline/push/) parancs, ismét lecseréli a Docker-azonosító.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. A hálózati sebességtől függően a kép első lenyomása néhány percet is igénybe vehet (a későbbi módosítások lenyomása sokkal gyorsabb). Várakozás közben folytathatja a következő szakaszt, és létrehozhat Azure-erőforrásokat egy másik terminálon.

## <a name="create-supporting-azure-resources-for-your-function"></a>Támogató Azure-erőforrások létrehozása a funkcióhoz

A függvénykód Azure-ba való üzembe helyezéséhez három erőforrást kell létrehoznia:

- Erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
- Egy Azure Storage-fiók, amely fenntartja a projektek állapot- és egyéb adatait.
- Egy Azure-függvényalkalmazás, amely a függvénykód végrehajtásához biztosít környezetet. A függvényalkalmazás leképezi a helyi függvényprojektet, és lehetővé teszi a függvények logikai egységként történő csoportosítását az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében.

Az Azure CLI-parancsok segítségével hozhatja létre ezeket az elemeket. Minden parancs a teljesítésután biztosítja a JSON-kimenetet.

1. Jelentkezzen be az Azure-ba az [az bejelentkezési](/cli/azure/reference-index#az-login) paranccsal:

    ```azurecli
    az login
    ```
    
1. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példa létrehoz `AzureFunctionsContainers-rg` egy `westeurope` erőforráscsoportot a régióban. (Az erőforráscsoportot és az erőforrásokat általában egy Ön közelében `az account list-locations` lévő régióban hozza létre, a parancsból elérhető terület használatával.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Nem üzemeltethet Linux- és Windows-alkalmazásokat ugyanabban az erőforráscsoportban. Ha egy Windows-függvényalkalmazással vagy webalkalmazással elnevezett `AzureFunctionsContainers-rg` meglévő erőforráscsoporttal rendelkezik, másik erőforráscsoportot kell használnia.
    
1. Hozzon létre egy általános célú tárfiókot az erőforráscsoportban és a régióban az [az storage fiók létrehozása](/cli/azure/storage/account#az-storage-account-create) paranccsal. A következő példában `<storage_name>` cserélje le az Ön számára megfelelő globálisan egyedi nevet. A nevek csak 3–24 karakterből és kisbetűkből állhatnak. `Standard_LRS`egy tipikus általános célú számlát határoz meg.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    A tárfiók csak néhány USD centet von maga után az oktatóanyaghoz.
    
1. A parancs segítségével hozzon létre egy `myPremiumPlan` prémium csomagot az Azure`--sku EP1`Functions nevű elastic Premium`-location westeurope` **1** tarifacsomag ( ), a Nyugat-európai régióban ( , vagy használja a megfelelő régióban az Ön közelében), és egy Linux-tárolóban (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Az egyéni függvénytárolók Linux-tárhelye [támogatott dedikált (App Service) csomagokban](functions-scale.md#app-service-plan) és [prémium csomagokban.](functions-premium-plan.md#features) Itt használjuk a Prémium csomagot, amely szükség szerint skálázható. További információk az üzemeltetésről: [Azure Functions szolgáltatási csomagok összehasonlítása](functions-scale.md). A költségek kiszámításához tekintse meg a [Functions árképzési lapját.](https://azure.microsoft.com/pricing/details/functions/)

    A parancs is rendelkezik egy társított Azure Application Insights-példány ugyanabban az erőforráscsoportban, amellyel figyelheti a függvényalkalmazást, és megtekintheti a naplókat. További információ: [Monitor Azure Functions](functions-monitoring.md). A példány nem jár költségekkel, amíg nem aktiválja.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Függvényalkalmazás létrehozása és konfigurálása az Azure-ban a lemezképpel

Az Azure-beli függvényalkalmazás kezeli a funkciók végrehajtását a tárhelycsomagban. Ebben a szakaszban az Azure-erőforrások az előző szakaszban hozzon létre egy függvényalkalmazást egy képa Docker Hub, és konfigurálja azt egy kapcsolati karakterlánc ot az Azure Storage-ba.

1. Hozza létre a Functions alkalmazást az [az functionapp create](/cli/azure/functionapp#az-functionapp-create) paranccsal. A következő példában `<storage_name>` cserélje le a tárfiók előző szakaszában használt nevet. Is `<app_name>` cserélje ki az Ön számára megfelelő `<docker_id>` globálisan egyedi nevet, és a Docker-azonosító.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    A *központi telepítési-tároló-image-name* paraméter megadja a függvényalkalmazáshoz használandó lemezképet. Az az [functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) parancs segítségével megtekintheti a központi telepítéshez használt lemezkép adatait. Az [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) paranccsal is üzembe helyezheti egy másik lemezképből.

1. Az [az a tárfiók show-connection-string](/cli/azure/storage/account) paranccsal létrehozott tárfiók kapcsolati karakterláncának `storageConnectionString`lekérése egy rendszerhéj-változóhoz rendelve:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Adja hozzá ezt a beállítást a függvényalkalmazáshoz az [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) paranccsal. A következő parancsban `<app_name>` cserélje le a függvényalkalmazás `<connection_string>` nevét, és cserélje le az előző lépésben lévő kapcsolati karakterláncra (egy hosszú kódolású karakterláncra, amely "DefaultEndpointProtocol=" karakterrel kezdődik):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. A függvény most már használhatja ezt a kapcsolati karakterláncot a tárfiók eléréséhez.

> [!TIP]
> A bash, akkor egy shell változó, hogy rögzítse a kapcsolat string használata helyett a vágólapra. Először a következő paranccsal hozzon létre egy változót a kapcsolati karakterlánccal:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Ezután olvassa el a második parancs ban található változót:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Ha közzéteszi az egyéni lemezképet egy privát tárolófiókban, használjon környezeti változókat a Dockerfile-ban a kapcsolati karakterlánchoz. További információt az [ENV utasításban](https://docs.docker.com/engine/reference/builder/#env)talál. A változókat is `DOCKER_REGISTRY_SERVER_USERNAME` `DOCKER_REGISTRY_SERVER_PASSWORD`be kell állítania, és . Az értékek használatához, majd újra kell építenie a lemezképet, leküldése a rendszerképet a beállításjegyzékbe, majd indítsa újra a függvényalkalmazást az Azure-ban.

## <a name="verify-your-functions-on-azure"></a>A funkciók ellenőrzése az Azure-ban

A rendszerkép üzembe helyezése a függvényalkalmazás az Azure-ban, most már http-kérelmeken keresztül meghívhatja a függvényt. Mivel a *function.json* definíció `"authLevel": "function"`tartalmazza a tulajdonságot, először be kell szereznie a hozzáférési kulcsot (más néven a "függvénykulcsot"), és url-paraméterként kell szerepelnie a végpontra irányuló kérelmekben.

1. A függvény URL-címének lekérése a hozzáférési (függvény) kulccsal az `az rest` Azure Portal használatával, vagy az Azure CLI használatával a paranccsal.)

    # <a name="portal"></a>[Portál](#tab/portal)

    1. Jelentkezzen be az Azure Portalon, majd keresse meg a függvényalkalmazást a függvényalkalmazás nevének megadásával a lap tetején lévő **Keresőmezőbe.** Az eredmények ben válassza ki az App Service-erőforrást. **App Service**

    1. A bal oldali navigációs panel **Funkciók (írásvédett)** területén válassza ki a függvény nevét.

    1. A részletek panelen válassza **a</> Funkció URL-címének bekerülése**lehetőséget:
    
        ![A Függvény URL-címének beszerezniparancsa az Azure Portalon](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. Az előugró ablakban válassza az **alapértelmezett (Funkcióbillentyű)** elemet, majd **a Másolás lehetőséget.** A kulcs a következő `?code=`karaktersorozat.

        ![A függvény URL-címének másolása az Azure Portalról](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Mivel a függvényalkalmazás tárolóként van telepítve, nem módosíthatja a függvénykódot a portálon. Ehelyett frissítenie kell a projektet a helyi lemezképen, ismét le kell nyomnia a lemezképet a beállításjegyzékbe, majd újra telepítenie kell az Azure-ba. A folyamatos telepítést egy későbbi szakaszban állíthatja be.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. Az URL-karakterláncot a következő `<subscription_id>` `<resource_group>`formátumban `<app_name>` hozhat létre, a , a , és az Azure-előfizetés-azonosítóját, a függvényalkalmazás erőforráscsoportját, illetve a függvényalkalmazás nevét:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Az URL-cím például a következő címre nézhet ki:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Az egyszerűség kedvéért az URL-címet hozzárendelheti egy `az rest` környezeti változóhoz, és használhatja azt a parancsban.
    
    1. Futtassa `az rest` a következő parancsot (amely az Azure CLI 2.0.77-es és újabb verziójában érhető el), és cserélje le `<uri>` az utolsó lépésURI-karakterláncára, beleértve az árajánlatokat is:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. A parancs kimenete a funkcióbillentyű. Ekkor a teljes `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`függvény `<app_name>`URL-címe a , a , `<function_name>`és `<key>` a meghatározott értékekre kerül.
    
        > [!NOTE]
        > Az itt beolvasott kulcs az a *gazdakulcs,* amely a függvények alkalmazás összes funkciójához működik; a portálon látható módszer csak egy függvény kulcsát olvassa be.

    ---

1. Illessze be a függvény URL-címét a `&name=Azure` böngésző címsorába, és adja hozzá a paramétert az URL végéhez. A "Hello Azure" szövegnek meg kell jelennie a böngészőben.

    ![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Az engedélyezés teszteléséhez távolítsa el a code= paramétert az URL-címből, és ellenőrizze, hogy nem kap-e választ a függvénytől.


## <a name="enable-continuous-deployment-to-azure"></a>Folyamatos üzembe helyezés engedélyezése az Azure-ban

Engedélyezheti, hogy az Azure Functions automatikusan frissítse a lemezkép központi telepítését, amikor frissíti a lemezképet a beállításjegyzékben.

1. Engedélyezze a folyamatos üzembe helyezést az az `<app_name>` [functionapp központi telepítési tároló konfigurációs](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) parancsával, amely a függvényalkalmazás nevére lép:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Ez a parancs lehetővé teszi a folyamatos üzembe helyezést, és visszaadja a központi telepítési webhook URL-címét. (Ezt az URL-címet később az [az functionapp telepítési tároló show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) parancsával bármikor lekérheti.)

1. Másolja a központi telepítési webhook URL-címét a vágólapra.

1. Nyissa meg [a Docker Hubot,](https://hub.docker.com/)jelentkezzen be, és válassza **a Tárolók lehetőséget** a navigációs sávon. Keresse meg és jelölje ki a képet, válassza a **Webhooks** lapot, adja meg a **Webhook nevét,** illessze be az URL-címet a **Webhook URL-címébe,** majd válassza a **Létrehozás**lehetőséget:

    ![A webhook hozzáadása a DockerHub-tárházban](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. A webhook-készlettel az Azure Functions újratelepíti a lemezképet, amikor frissíti a Docker Hubban.

## <a name="enable-ssh-connections"></a>SSH-kapcsolatok engedélyezése

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. Ha az SSH engedélyezve van, az App Service Advanced Tools (Kudu) segítségével csatlakozhat a tárolóhoz. Annak érdekében, hogy az SSH használatával könnyen kapcsolódjon a tárolóhoz, az Azure Functions olyan alaplemezképet biztosít, amelyen már engedélyezve van az SSH. Csak a Docker-fájlt kell szerkeszteni, majd újra kell építenie és újra kell telepítenie a lemezképet. Ezután az Advanced Tools (Kudu) segítségével csatlakozhat a tárolóhoz.

1. A Docker-fájlban fűzze hozzá a `-appservice` `FROM` karakterláncot az alaplemezképhez az utasításban:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    Az alaprendszerképek közötti különbségeket az [App Services – Egyéni docker-rendszerképek oktatóanyag ismerteti.](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections)

1. Építse újra a `docker build` lemezképet a `<docker_id>` parancs ismételt használatával, és cserélje le a Docker-azonosítót:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. A frissített rendszerképet a Docker Hubra kell leadni, amely lényegesen kevesebb időt vesz igénybe, mint az első leküldéses, csak a rendszerkép frissített szegmenseit kell feltölteni.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Az Azure Functions automatikusan újratelepíti a lemezképet a függvények alkalmazásba; a folyamat kevesebb, mint egy perc alatt lezajlik.

1. A böngészőben `https://<app_name>.scm.azurewebsites.net/`nyissa `<app_name>` meg a , az egyedi nevet. Ez az URL-cím a speciális eszközök (Kudu) végpont a függvényalkalmazás-tároló.

1. Jelentkezzen be az Azure-fiókjába, majd válassza ki az **SSH-t** a tárolóval való kapcsolat létrehozásához. A csatlakozás eltarthat néhány percet, ha az Azure még mindig a tárolórendszerkép frissítése folyamatban van.

1. Miután létrejött egy kapcsolat a `top` tárolóval, futtassa a parancsot az éppen futó folyamatok megtekintéséhez. 

    ![Linux felső parancs fut egy SSH munkamenetben](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Írás egy Azure Storage-várólistába

Az Azure Functions lehetővé teszi, hogy a függvényeket más Azure-szolgáltatásokhoz és erőforrásokhoz csatlakoztassa, amelyeknek saját integrációs kódot kell írniuk. Ezek *a kötések*, amelyek mind a bemeneti, mind a kimeneti kötéseket képviselik, a függvénydefinícióban vannak deklarálva. A kötések adatai a függvények számára paraméterekként vannak megadva. Az *eseményindító* a bemeneti kötés egy speciális típusa. Bár egy függvény csak egy eseményindítóval rendelkezik, több bemeneti és kimeneti kötéssel rendelkezhet. További információ: [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md).

Ez a szakasz bemutatja, hogyan integrálhatja a függvényt egy Azure Storage-várólistával. A függvényhez hozzáadott kimeneti kötés http-kérelemből adatokat ír a várólistában lévő üzenetbe.

## <a name="retrieve-the-azure-storage-connection-string"></a>Az Azure Storage-kapcsolati karakterlánc lekérése

Korábban létrehozott egy Azure Storage-fiókot a függvényalkalmazás általi használatra. A fiók kapcsolati karakterlánca biztonságosan tárolódik az Azure-beli alkalmazásbeállításokban. Ha letölti a beállítást a *local.settings.json* fájlba, akkor a helyi futtatásakor használhatja ezt a kapcsolatírást ugyanazon a fiókban lévő Storage várólistára. 

1. A projekt gyökeréből futtassa `<app_name>` a következő parancsot, és cserélje le a függvényalkalmazás nevét az előző rövid útmutatóból. Ez a parancs felülírja a fájlban lévő értékeket.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Nyissa meg a *local.settings.json webhelyet,* és keresse meg a nevű `AzureWebJobsStorage`értéket, amely a Storage-fiók kapcsolati karakterlánca. A cikk `AzureWebJobsStorage` más szakaszaiban a nevet és a kapcsolati karakterláncot használhatja.

> [!IMPORTANT]
> Mivel *a local.settings.json* az Azure-ból letöltött titkokat tartalmaz, mindig zárja ki ezt a fájlt a forrásellenőrzésből. A helyi függvényprojekttel létrehozott *.gitignore* fájl alapértelmezés szerint kizárja a fájlt.

### <a name="add-an-output-binding-to-functionjson"></a>Kimeneti kötés hozzáadása a function.json függvényhez

Az Azure Functions ben minden `direction`típusú `type`kötéshez `name` szükség van egy , és egy egyedi kell definiálni a *function.json* fájlban. A *function.json* már tartalmaz egy bemeneti kötést a "httpTrigger" típushoz, és egy kimeneti kötést a HTTP-válaszhoz. Ha egy tárolóvárólistához szeretne kötést hozzáadni, módosítsa a fájlt a következőképpen, amely kimeneti kötést ad hozzá `msg`a "várólista" típushoz, ahol a várólista bemeneti argumentumként jelenik meg a kódban. A várólista-kötéshez szükség van a használni `outqueue`kívánt várólista nevére, ebben az esetben `AzureWebJobStorage`a kapcsolati karakterláncot tartalmazó beállítások nevére , ebben az esetben .

::: zone pivot="programming-language-csharp"

C# osztálykönyvtár-projektben a kötések a függvénymetódus kötési attribútumaiként vannak definiálva. A *function.json* fájl ezután automatikusan generálódik ezen attribútumok alapján.

1. A várólista-kötés esetén futtassa a következő [dotnet add package](/dotnet/core/tools/dotnet-add-package) parancsot a Storage bővítménycsomag projekthez való hozzáadásához.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Nyissa meg a *HttpTrigger.cs* `using` fájlt, és adja hozzá a következő utasítást:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Adja hozzá a `Run` következő paramétert a metódusdefinícióhoz:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    A `Run` metódus definíciójának most meg kell egyeznie a következő kóddal:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

A `msg` paraméter `ICollector<T>` egy olyan típus, amely a függvény befejezésekor egy kimeneti kötésbe írt üzenetek gyűjteményét jelöli. Ebben az esetben a kimenet egy `outqueue`tárolási várólista nevű. A Storage fiók kapcsolati karakterláncát `StorageAccountAttribute`a. Ez az attribútum azt a beállítást jelzi, amely a Storage-fiók kapcsolati karakterláncát tartalmazza, és az osztály, a metódus vagy a paraméter szintjén alkalmazható. Ebben az esetben kihagyhatja, `StorageAccountAttribute` mert már használja az alapértelmezett tárfiókot.

::: zone-end

::: zone pivot="programming-language-javascript"

Frissítse *a function.json függvényt* a következőknek megfelelően a http-kötés utáni várólista-kötés hozzáadásával:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-powershell"

Frissítse *a function.json függvényt* a következőknek megfelelően a http-kötés utáni várólista-kötés hozzáadásával:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-python"

Frissítse *a function.json függvényt* a következőknek megfelelően a http-kötés utáni várólista-kötés hozzáadásával:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-typescript"

Frissítse *a function.json függvényt* a következőknek megfelelően a http-kötés utáni várólista-kötés hozzáadásával:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

## <a name="add-code-to-use-the-output-binding"></a>Kód hozzáadása a kimeneti kötés használatához

A kötés definiálása után a kötés neve `msg`ebben az esetben argumentumként (vagy `context` javascriptben és TypeScriptben lévő objektumként) jelenik meg a függvénykódban. Ezután ezzel a változóval üzeneteket írhat a várólistába. A hitelesítéshez, a várólista-hivatkozás hoz vagy az adatok írásához bármilyen kódot meg kell írnia. Mindezek az integrációs feladatok kényelmesen kezelik az Azure Functions futásidejű és a várólista kimeneti kötés.

::: zone pivot="programming-language-csharp"
```csharp
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```
::: zone-end

::: zone pivot="programming-language-javascript"
```js
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);

        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```
::: zone-end

::: zone pivot="programming-language-powershell"
```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```
::: zone-end

::: zone pivot="programming-language-python"
```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```
::: zone-end

::: zone pivot="programming-language-typescript"
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);
        
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```
::: zone-end

### <a name="update-the-image-in-the-registry"></a>A lemezkép frissítése a rendszerleíró adatbázisban

1. A gyökérmappában `docker build` futtassa újra, és ezúttal `v1.0.1`frissítse a tag verzióját a számára. A korábhoz `<docker_id>` képest cserélje le a Docker Hub-fiók azonosítóját:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. A frissített kép visszaküldése a `docker push`tárházba a következővel:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Mivel konfigurált a folyamatos kézbesítés, a rendszerkép frissítése a beállításjegyzékben ismét automatikusan frissíti a függvényalkalmazást az Azure-ban.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Az üzenet megtekintése az Azure Storage várólistájában

A böngészőben használja ugyanazt az URL-címet, mint korábban a függvény meghívásához. A böngészőnek ugyanazt a választ kell megjelenítenie, mint korábban, mert nem módosította a függvénykód nak ezt a részét. A hozzáadott kód azonban az URL-paraméter t `outqueue` használja a `name` tárolóvárólistába.

A várólista az [Azure Portalon](../storage/queues/storage-quickstart-queues-portal.md) vagy a [Microsoft Azure Storage Explorerben](https://storageexplorer.com/)tekinthető meg. A várólista az Azure CLI-ben is megtekintheti az alábbi lépésekben leírtak szerint:

1. Nyissa meg a függvényprojekt *local.setting.json* fájlját, és másolja a kapcsolati karakterlánc értékét. Terminál- vagy parancsablakban futtassa a következő `AZURE_STORAGE_CONNECTION_STRING`parancsot a program névre `<connection_string>`szóló környezeti változó létrehozásához, és illessze be az adott kapcsolati karakterláncot a helyére. (Ez a környezeti változó azt jelenti, hogy nem kell megadnia a kapcsolati karakterláncot minden további parancshoz az `--connection-string` argumentum használatával.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Nem kötelező) A [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) parancs segítségével megtekintheti a storage várólistákat a fiókjában. A parancs kimenetének tartalmaznia `outqueue`kell egy várólistát, amely akkor jött létre, amikor a függvény az első üzenetét a várólistára írta.
    
    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. A [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) parancs segítségével megtekintheti a várólistában lévő üzeneteket, amelyek a funkció korábbi tesztelésekén használt első névnek kell lenniük. A parancs lekéri az első üzenetet a várólistában [base64 kódolás](functions-bindings-storage-queue-trigger.md#encoding), így is meg kell dekódolni az üzenetet, hogy megtekinthesse a szöveget.

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    Mivel meg kell dereference az üzenetgyűjtemény és dekódolni base64, futtassa a PowerShellt, és használja a PowerShell parancsot.

    ---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbbra is az Azure-funkcióval szeretne dolgozni az oktatóanyagban létrehozott erőforrások használatával, az összes erőforrást a helyén hagyhatja. Mivel prémium csomagot hozott létre az Azure Functionshez, napi egy vagy két USD-t kell felűröznie a folyamatos költségekben.

A folyamatos költségek elkerülése `AzureFunctionsContainer-rg` érdekében törölje az erőforráscsoportot a csoport összes erőforrásának törléséhez: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>További lépések

+ [Felügyeleti funkciók](functions-monitoring.md)
+ [Méretezési és üzemeltetési lehetőségek](functions-scale.md)
+ [Kubernetes-alapú kiszolgáló nélküli tárhely](functions-kubernetes-keda.md)
