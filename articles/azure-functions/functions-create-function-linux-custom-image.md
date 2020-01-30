---
title: Azure Functions létrehozása Linuxon egyéni rendszerkép használatával
description: Megismerheti, hogyan hozhat létre egyéni Linux-rendszerképeken futó Azure Functions-függvényeket.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions01
ms.openlocfilehash: edb947f0748c186e146bce5f4dbe9d0b95a2568d
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846482"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Függvény létrehozása Linux rendszeren egyéni tároló használatával

Ebben az oktatóanyagban Python-kódot hoz létre és helyez üzembe Azure Functions egyéni Docker-tárolóként egy Linux-alapú alaprendszerkép használatával. Általában egyéni rendszerképeket használ, ha a függvények egy adott nyelvi verziót igényelnek, vagy olyan függőséget vagy konfigurációt használnak, amelyet a beépített rendszerkép nem biztosít.

Az alapértelmezett Azure App Service tárolót a [Linuxon üzemeltetett első függvény létrehozása](functions-create-first-azure-function-azure-cli-linux.md)című témakörben leírtak szerint is használhatja. A Azure Functions támogatott alaplemezképei a [Azure functions Base images](https://hub.docker.com/_/microsoft-azure-functions-base)tárházban találhatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Function alkalmazást és egy Docker a Azure Functions Core Tools használatával.
> * Egyéni rendszerkép készítése a Docker használatával.
> * Egyéni rendszerkép közzététele egy tárolójegyzékben.
> * Támogatási erőforrások létrehozása az Azure-ban a Function alkalmazáshoz
> * Függvényalkalmazás üzembe helyezése a Docker Hubból.
> * Alkalmazásbeállítások hozzáadása a függvényalkalmazáshoz.
> * Folyamatos üzembe helyezés engedélyezése.
> * Engedélyezze az SSH-kapcsolatokat a tárolóhoz.
> * Adja hozzá a várólista-tároló kimeneti kötését. 

Ezt az oktatóanyagot követheti Windows, Mac OS vagy Linux rendszerű számítógépeken is. Az oktatóanyag elvégzése után az Azure-fiókban néhány USA-dollárért kell fizetnie.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- A [Azure functions Core Tools](./functions-run-local.md#v2) verziója 2.7.1846 vagy újabb
- Az [Azure CLI](/cli/azure/install-azure-cli) verziója 2.0.77 vagy újabb verzió
- A [Azure functions 2. x futtatókörnyezet](functions-versions.md)
- A következő nyelvi futtatókörnyezet-összetevők:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2. x vagy újabb](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3,6-64 bit](https://www.python.org/downloads/release/python-3610/) vagy [Python 3,7-64 bit](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- [Docker-azonosító](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

1. A terminál vagy a parancssorablakban futtassa a `func --version` parancsot annak a vizsgálatához, hogy a Azure Functions Core Tools 2.7.1846 vagy újabb verziójúak-e.
1. `az --version` futtatásával győződjön meg arról, hogy az Azure CLI verziója 2.0.76 vagy újabb.
1. `az login` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.
1. `docker login` futtatása a Docker-ba való bejelentkezéshez. Ez a parancs meghiúsul, ha a Docker nem fut, amely esetben a Docker elindítása után próbálja megismételni a parancsot.

## <a name="create-and-test-the-local-functions-project"></a>A local functions projekt létrehozása és tesztelése

1. Egy terminálon vagy parancssorban hozzon létre egy mappát az oktatóanyaghoz egy megfelelő helyen, majd navigáljon a mappába.

1. A [virtuális környezet létrehozása és aktiválása](functions-create-first-function-python.md#create-and-activate-a-virtual-environment) című témakör útmutatását követve hozzon létre egy virtuális környezetet az oktatóanyaghoz való használatra.

1. Futtassa a következő parancsot a választott nyelvhez egy Function app-projekt létrehozásához egy `LocalFunctionsProject`nevű mappában. Az `--docker` lehetőség létrehoz egy `Dockerfile` a projekthez, amely egy megfelelő egyéni tárolót határoz meg a Azure Functions és a kiválasztott futtatókörnyezet használatával való használatra.

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
    
1. Navigáljon a projekt mappájába:

    ```
    cd LocalFunctionsProject
    ```
    
1. Adjon hozzá egy függvényt a projekthez a következő parancs használatával, ahol a `--name` argumentum a függvény egyedi neve, a `--template` argumentum pedig megadja a függvény triggerét. `func new` hozzon létre egy olyan almappát, amely megfelel a projekt választott nyelvének és a *function. JSON*nevű konfigurációs fájlnak, amely tartalmazza a függvény nevét.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. A függvény helyileg történő teszteléséhez indítsa el a helyi Azure Functions Runtime-gazdagépet a *LocalFunctionsProject* mappában:
   
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

1. Ha megjelenik a `HttpExample` végpont a kimenetben, keresse meg a `http://localhost:7071/api/HttpExample?name=Functions`. A böngészőben olyan üzenetet kell megjelenítenie, mint a "Hello, functions" (a választott programozási nyelvtől függően némileg változó).

1. A gazdagép leállításához használja a CTRL-**C** **billentyűkombinációt** .

## <a name="build-the-container-image-and-test-locally"></a>A tároló rendszerképének létrehozása és helyi tesztelése

1. Választható Vizsgálja meg a * Docker a *LocalFunctionsProj* mappában. A Docker leírja a Function app Linux rendszeren való futtatásához szükséges környezetet: 

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
    > A Azure Functions által támogatott alaplemezképek teljes listája megtalálható a [Azure functions alap lemezképe lapon](https://hub.docker.com/_/microsoft-azure-functions-base).
    
1. A *LocalFunctionsProject* mappában futtassa a [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) parancsot, és adjon meg egy nevet, egy `azurefunctionsimage`és egy címkét `v1.0.0`. A `<docker_id>` helyére a Docker Hub-fiók azonosítóját írja. Ez a parancs létrehozza a tároló Docker-rendszerképét.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    Ha a parancs befejeződik, az új tárolót helyileg is futtathatja.
    
1. A Build teszteléséhez futtassa a rendszerképet egy helyi tárolóban a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) paranccsal, majd cserélje le újra `<docker_id>` a Docker-azonosítójával, és adja hozzá a portok argumentumot, `-p 8080:80`:

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Miután a rendszerkép egy helyi tárolóban fut, nyisson meg egy böngészőt `http://localhost:8080`, amely megjeleníti az alább látható helyőrző képet. A rendszerkép ekkor jelenik meg, mivel a függvény a helyi tárolóban fut, ahogy az az Azure-ban lenne, ami azt jelenti, hogy egy, a *function. JSON* fájlban megadott hozzáférési kulcs védi a `"authLevel": "function"` tulajdonsággal. A tároló még nem lett közzétéve egy Azure-beli Function alkalmazásban, így a kulcs még nem érhető el. Ha helyileg szeretne tesztelni, állítsa le a Docker-t, módosítsa az engedélyezési tulajdonságot `"authLevel": "anonymous"`re, hozza létre újra a rendszerképet, és indítsa újra a Docker-t. Ezután állítsa vissza `"authLevel": "function"` a *function. JSON*fájlban. További információ: [engedélyezési kulcsok](functions-bindings-http-webhook.md#authorization-keys).

    ![Helyőrző képe, amely azt jelzi, hogy a tároló helyileg fut](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Miután ellenőrizte a Function alkalmazást a tárolóban, állítsa le a Docker-t a CTRL+**C** **billentyűkombinációval** .

## <a name="push-the-image-to-docker-hub"></a>A rendszerkép leküldése a Docker hub-ra

A Docker hub egy tároló-beállításjegyzék, amely képeket és lemezképeket és tároló szolgáltatásokat biztosít. Ha meg szeretné osztani a lemezképet, amely magában foglalja az Azure-ba való üzembe helyezést, azt egy beállításjegyzékbe kell leküldeni.

1. Ha még nem jelentkezett be a Docker-be, ezt a [Docker login](https://docs.docker.com/engine/reference/commandline/login/) paranccsal teheti meg, és lecseréli `<docker_id>` a Docker-azonosítójával. Ez a parancs megkéri a felhasználónevet és a jelszót. A "sikeres bejelentkezés" üzenet megerősíti, hogy bejelentkezett.

    ```
    docker login
    ```
    
1. Miután bejelentkezett, küldje le a rendszerképet Docker hub-ra a Docker [push](https://docs.docker.com/engine/reference/commandline/push/) paranccsal, majd ismét cserélje le a `<docker_id>`t a Docker-azonosítójával.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. A hálózati sebességtől függően a rendszerképet úgy kell kitolni, hogy az első alkalommal is eltarthat néhány percig (a későbbi változtatások gyorsabban megadhatók). Várakozás közben folytassa a következő szakasszal, és hozzon létre Azure-erőforrásokat egy másik terminálon.

## <a name="create-supporting-azure-resources-for-your-function"></a>A függvényhez kapcsolódó Azure-erőforrások létrehozása

A függvény kódjának az Azure-ba történő üzembe helyezéséhez három erőforrást kell létrehoznia:

- Egy erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
- Egy Azure Storage-fiók, amely fenntartja az állapotot és a projektekkel kapcsolatos egyéb információkat.
- Egy Azure functions-alkalmazás, amely biztosítja a környezetet a függvény kódjának végrehajtásához. A Function alkalmazás a helyi function projekthez kapcsolódik, és lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.

Ezeket az elemeket az Azure CLI-parancsok használatával hozhatja létre. Az egyes parancsok a JSON-kimenetet biztosítják a befejezés után.

1. Jelentkezzen be az Azure-ba az az [login](/cli/azure/reference-index#az-login) paranccsal:

    ```azurecli
    az login
    ```
    
1. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példában létrehozunk egy `AzureFunctionsContainers-rg` nevű erőforráscsoportot a `westeurope` régióban. (Az erőforráscsoport és az erőforrások általában az Ön közelében lévő régióban hozhatók létre a `az account list-locations` parancsból elérhető régió használatával.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Nem futtathat Linux-és Windows-alkalmazásokat ugyanabban az erőforráscsoporthoz. Ha van egy `AzureFunctionsContainers-rg` nevű meglévő erőforráscsoport egy Windows-függvény alkalmazással vagy webalkalmazással, egy másik erőforráscsoportot kell használnia.
    
1. Hozzon létre egy általános célú Storage-fiókot az erőforráscsoport és a régió területén az az [Storage Account Create](/cli/azure/storage/account#az-storage-account-create) paranccsal. Az alábbi példában cserélje le a `<storage_name>`t a megfelelő globálisan egyedi névre. A névnek három – 24 karakterből kell állnia, és csak kisbetűket tartalmazhat. `Standard_LRS` egy tipikus általános célú fiókot határoz meg.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Ebben az oktatóanyagban a Storage-fióknak csupán néhány USD-es százaléka van.
    
1. A (z) paranccsal hozzon létre egy prémium szintű csomagot Azure Functions nevű `myPremiumPlan` a **rugalmas prémium 1** díjszabási szinten (`--sku EP1`), a Nyugat-európai régióban (`-location westeurope`, vagy használjon egy megfelelő régiót), és egy Linux-tárolóban (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Az egyéni functions-tárolók Linux-üzemeltetése [dedikált (App Service) csomagok](functions-scale.md#app-service-plan) és [prémium csomagok](functions-premium-plan.md#features)esetén támogatott. Az itt található prémium csomagot használjuk, amely igény szerint méretezhető. További információk az üzemeltetésről: [Azure Functions szolgáltatási csomagok összehasonlítása](functions-scale.md). A költségek kiszámításához tekintse meg a [functions díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/functions/).

    A parancs egy kapcsolódó Azure Application Insights-példányt is kiépít ugyanabban az erőforráscsoporthoz, amellyel nyomon követheti a Function alkalmazást, és megtekintheti a naplókat. További információ: [Azure functions figyelése](functions-monitoring.md). A példány nem jár költséggel, amíg be nem aktiválja.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Function-alkalmazás létrehozása és konfigurálása az Azure-ban a rendszerképpel

Az Azure-beli Function-alkalmazás kezeli a függvények végrehajtását a üzemeltetési tervben. Ebben a szakaszban az előző szakasz Azure-erőforrásaival hozzon létre egy Function alkalmazást a Docker hub egyik rendszerképéről, és konfigurálja azt egy Azure Storage-hoz tartozó kapcsolódási karakterlánccal.

1. Hozza létre a functions alkalmazást az az [functionapp Create](/cli/azure/functionapp#az-functionapp-create) parancs használatával. A következő példában cserélje le a `<storage_name>`t az előző szakaszban használt névre a Storage-fiókhoz. Emellett cserélje le a `<app_name>`t egy globálisan egyedi névvel, és `<docker_id>` a Docker-azonosítójával.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Az *üzembe helyezés-Container-rendszerkép-Name* paraméter megadja a Function alkalmazáshoz használandó rendszerképet. Az az [functionapp config Container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) parancs használatával megtekintheti az üzembe helyezéshez használt rendszerképpel kapcsolatos információkat. Egy másik rendszerképből is üzembe helyezheti az az [functionapp config Container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) parancsot.

1. Kérje le a létrehozott Storage-fiókhoz tartozó kapcsolati karakterláncot az az [Storage Account show-kapcsolat-string](/cli/azure/storage/account) paranccsal, és rendelje hozzá egy rendszerhéj-változóhoz `storageConnectionString`:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    
1. Add this setting to the function app by using the [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command. In the following command, replace `<app_name>` with the name of your function app, and replace `<connection_string>` with the connection string from the previous step (a long encoded string that begins with "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. A függvény mostantól használhatja ezt a kapcsolati karakterláncot a Storage-fiók eléréséhez.

> [!TIP]
> A bash-ben egy rendszerhéj-változó használatával rögzítheti a kapcsolódási karakterláncot a vágólap használata helyett. Először a következő parancs használatával hozzon létre egy változót a kapcsolódási karakterlánccal:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Ezután tekintse meg a változót a második parancsban:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Ha egyéni rendszerképet tesz közzé egy privát Container-fiókban, ehelyett környezeti változókat kell használnia a Docker a kapcsolódási karakterlánchoz. További információ: [env utasítás](https://docs.docker.com/engine/reference/builder/#env). Az `DOCKER_REGISTRY_SERVER_USERNAME` és a `DOCKER_REGISTRY_SERVER_PASSWORD`változókat is be kell állítania. Az értékek használatához újra kell építenie a rendszerképet, le kell küldenie a rendszerképet a beállításjegyzékbe, majd újra kell indítania a Function alkalmazást az Azure-ban.

## <a name="verify-your-functions-on-azure"></a>A függvények ellenőrzése az Azure-ban

Az Azure-beli Function alkalmazásban üzembe helyezett képpel a függvényt HTTP-kérelmeken keresztül hívhatja meg. Mivel a *function. JSON* definíciója tartalmazza a `"authLevel": "function"`tulajdonságot, először be kell szereznie a hozzáférési kulcsot (más néven "Function Key"), és a végpontra irányuló KÉRÉSEKBEN URL-ként kell megadnia.

1. A függvény URL-címének lekérése a hozzáférési (Function) kulccsal a Azure Portal használatával vagy az `az rest` paranccsal az Azure CLI használatával.)

    # <a name="portaltabportal"></a>[Portál](#tab/portal)

    1. Jelentkezzen be a Azure Portalba, majd keresse meg a Function alkalmazást a függvény alkalmazás nevének megadásával az oldal tetején található **keresőmezőbe** . Az eredmények között válassza ki a **app Service** erőforrást.

    1. A bal oldali navigációs panel **functions (csak olvasás)** területén válassza ki a függvény nevét.

    1. A részletek panelen válassza a **</> függvény URL-címének beolvasása**lehetőséget:
    
        ![A funkció URL-címének beolvasása parancs a Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. Az előugró ablakban válassza az **alapértelmezett (Function Key)** elemet, majd **másolja a vágólapra**. A kulcs a `?code=`következő karakterből álló karakterlánc.

        ![A függvény URL-címének másolása a Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Mivel a Function alkalmazás tárolóként van telepítve, nem módosíthatja a függvény kódját a portálon. Ehelyett frissítenie kell a projektet a helyi rendszerképben, küldje újra a rendszerképet a beállításjegyzékbe, majd telepítse újra az Azure-ba. A folyamatos üzembe helyezést egy későbbi szakaszban állíthatja be.
    
    # <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)

    1. A következő formátumban szerkesszen egy URL-karakterláncot, és cserélje le `<subscription_id>`, `<resource_group>`és `<app_name>` az Azure-előfizetés azonosítójával, a Function alkalmazás erőforráscsoporthoz, valamint a Function alkalmazás nevét:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Előfordulhat például, hogy az URL-cím a következő címet keresi:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > A kényelem érdekében Ehelyett hozzárendelheti az URL-címet egy környezeti változóhoz, és használhatja azt a `az rest` parancsban.
    
    1. Futtassa az alábbi `az rest` parancsot (az Azure CLI 2.0.77 és újabb verzióiban érhető el), és cserélje le a `<uri>`t az utolsó lépésből származó URI-karakterlánccal, beleértve az idézőjeleket:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. A parancs kimenete a függvény kulcsa. Ezután a teljes függvény URL-címe `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`, lecseréli `<app_name>`, `<function_name>`és `<key>` az adott értékekre.
    
        > [!NOTE]
        > A beolvasott kulcs a functions alkalmazás összes függvényéhez használható *gazdagép* kulcsa. a portálon megjelenített metódus csak az egyetlen függvény kulcsát kérdezi le.

    ---

1. Illessze be a függvény URL-címét a böngésző címsorába, és adja hozzá az URL-cím végéhez `&name=Azure` paramétert. A böngészőben a "Hello Azure" szövegnek kell megjelennie.

    ![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Az engedélyezés teszteléséhez távolítsa el a Code = paramétert az URL-címről, és ellenőrizze, hogy nem kap-e választ a függvénytől.


## <a name="enable-continuous-deployment-to-azure"></a>Folyamatos üzembe helyezés engedélyezése az Azure-ban

Engedélyezheti, hogy a Azure Functions automatikusan frissítse a rendszerkép központi telepítését, amikor frissíti a rendszerképet a beállításjegyzékben.

1. A folyamatos üzembe helyezés engedélyezéséhez használja az [az functionapp Deployment Container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) parancsot, és cserélje le a `<app_name>`t a Function alkalmazás nevére:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Ez a parancs engedélyezi a folyamatos üzembe helyezést, és visszaadja az üzembe helyezési webhook URL-címét. (Ezt az URL-címet bármely későbbi időpontban lekérheti az az [functionapp Deployment Container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) paranccsal.)

1. Másolja az üzembe helyezési webhook URL-címét a vágólapra.

1. Nyissa meg a [Docker hub](https://hub.docker.com/)-t, jelentkezzen be, majd válassza a **tárolók** lehetőséget a navigációs sávon. Keresse meg és válassza ki a képet, válassza a **webhookok** fület, adja meg a **webhook nevét**, illessze be az URL-címet a **webhook URL-címébe**, majd válassza a **Létrehozás**lehetőséget:

    ![Webhook hozzáadása a DockerHub-tárházban](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. A webhook készlettel Azure Functions a lemezképet a Docker hub-ban való frissítésekor újra üzembe helyezi.

## <a name="enable-ssh-connections"></a>SSH-kapcsolatok engedélyezése

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. Ha engedélyezve van az SSH, App Service speciális eszközökkel (kudu) is csatlakozhat a tárolóhoz. Ahhoz, hogy az SSH-val könnyedén csatlakozhasson a tárolóhoz, Azure Functions olyan alaprendszerképet biztosít, amely már engedélyezve van az SSH-val. Csak szerkesztenie kell a Docker, majd újból létre kell hoznia és újra üzembe kell helyeznie a lemezképet. Ezután a speciális eszközökön (kudu) keresztül kapcsolódhat a tárolóhoz

1. A Docker fűzze hozzá a `-appservice` karakterláncot a `FROM` utasításban található alaprendszerképhez:

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

    Az alapképek közötti különbségekről az [app Services-Custom Docker images oktatóanyagban](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections)olvashat.

1. Hozza létre újra a rendszerképet a `docker build` parancs ismételt használatával, és cserélje le a `<docker_id>`t a Docker-azonosítójával:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Küldje le a frissített rendszerképet a Docker hub szolgáltatásba, amely jóval kevesebb időt vesz igénybe, mint az első leküldéses példány, amelyet a rendszerképnek csak a frissített szegmenseit kell feltölteni.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions automatikusan újra üzembe helyezi a rendszerképet a functions alkalmazásban; a folyamat egy percnél rövidebb ideig tart.

1. A böngészőben nyissa meg a `https://<app_name>.scm.azurewebsites.net/`t, és cserélje le a `<app_name>`t az egyedi névre. Ez az URL-cím a Function app-tároló speciális eszközök (kudu) végpontja.

1. Jelentkezzen be az Azure-fiókjába, majd válassza ki az **SSH** -t, és hozzon létre kapcsolatot a tárolóval. A csatlakozás eltarthat néhány másodpercig, ha az Azure még mindig folyamatban van a tároló rendszerképének frissítésében.

1. Miután létrejött a kapcsolatok a tárolóval, futtassa a `top` parancsot az aktuálisan futó folyamatok megtekintéséhez. 

    ![SSH-munkamenetben futó Linux-alapú leggyakoribb parancs](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Írás Azure Storage-várólistába

Azure Functions lehetővé teszi a függvények más Azure-szolgáltatásokhoz és-erőforrásokhoz való összekapcsolását, mivel saját integrációs kódot kell megírnia. Ezek a *kötések*, amelyek a bemeneti és a kimeneti adatokat jelölik, a függvény definíciójában vannak deklarálva. A kötések adatait paraméterként a függvény kapja meg. Az *trigger* egy speciális típusú bemeneti kötés. Bár a függvénynek csak egy triggere van, több bemeneti és kimeneti kötés is lehet. További információ: [Azure functions triggerek és kötések fogalmai](functions-triggers-bindings.md).

Ez a szakasz bemutatja, hogyan integrálhatja a függvényt egy Azure Storage-üzenetsor használatával. Az ehhez a függvényhez hozzáadott kimeneti kötés egy HTTP-kérelemből adatokat ír a várólistában lévő üzenetbe.

## <a name="retrieve-the-azure-storage-connection-string"></a>Az Azure Storage-beli kapcsolatok karakterláncának beolvasása

Korábban létrehozott egy Azure Storage-fiókot, amelyet a Function alkalmazás használ. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ha a beállítást a *Local. Settings. JSON* fájlba tölti le, akkor a függvény helyileg történő futtatásakor ugyanazzal a fiókkal írja be a kapcsolódást a tárolási várólistába. 

1. A projekt gyökerében futtassa a következő parancsot, és cserélje le a `<app_name>`t az előző rövid útmutatóban szereplő Function alkalmazás nevére. Ez a parancs felülírja a fájlban lévő összes meglévő értéket.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Nyissa meg a *Local. Settings. JSON* fájlt, és keresse meg `AzureWebJobsStorage`nevű értéket, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. A jelen cikk más részeiben a `AzureWebJobsStorage` nevet és a kapcsolatok karakterláncát használja.

> [!IMPORTANT]
> Mivel a *Local. Settings. JSON* az Azure-ból letöltött titkos kulcsokat tartalmaz, mindig zárja ki ezt a fájlt a forrás vezérlőelemből. A helyi functions projekttel létrehozott *. gitignore* fájl alapértelmezés szerint kizárja a fájlt.

### <a name="add-an-output-binding-to-functionjson"></a>Kimeneti kötés hozzáadása a function. JSON fájlhoz

Azure Functions minden típusú kötéshez szükség van egy `direction`ra, `type`re és egy egyedi `name`ra, amelyet a *function. JSON* fájlban kell meghatározni. A *function. JSON* már tartalmaz egy bemeneti kötést a "httpTrigger" típushoz és egy kimeneti kötést a HTTP-válaszhoz. Ha hozzá szeretne adni egy kötést egy tárolási sorhoz, módosítsa a fájlt az alábbiak szerint, amely hozzáad egy kimeneti kötést a "várólista" típushoz, ahol az üzenetsor a kódban a `msg`nevű bemeneti argumentumként jelenik meg. A várólista kötéséhez a használni kívánt várólista neve, ebben az esetben a `outqueue`és a kapcsolati karakterláncot tartalmazó beállítások neve is szükséges, ebben az esetben a `AzureWebJobStorage`.

::: zone pivot="programming-language-csharp"

Egy C# eszközosztály projektben a kötések kötési attribútumokként vannak definiálva a Function metódusban. A *function. JSON* fájl ezután automatikusan létrejön ezen attribútumok alapján.

1. A várólista-kötés esetében futtassa a következő [DotNet-csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) parancsot a tárolási bővítmény csomagjának a projekthez való hozzáadásához.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Nyissa meg a *HttpTrigger.cs* fájlt, és adja hozzá a következő `using` utasítást:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Adja hozzá a következő paramétert a `Run` metódus definícióhoz:
    
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

A `msg` paraméter egy `ICollector<T>` típus, amely a függvény befejeződése után kimeneti kötésbe írt üzenetek gyűjteményét jelöli. Ebben az esetben a kimenet egy `outqueue`nevű tárolási várólista. A Storage-fiókhoz tartozó kapcsolatok karakterláncát a `StorageAccountAttribute`állítja be. Ez az attribútum azt a beállítást jelzi, amely a Storage-fiókhoz tartozó kapcsolatok sztringjét tartalmazza, és alkalmazható az osztály, a metódus vagy a paraméter szintjén. Ebben az esetben kihagyhatja a `StorageAccountAttribute`, mert már használja az alapértelmezett Storage-fiókot.

::: zone-end

::: zone pivot="programming-language-javascript"

Módosítsa a *function. JSON* fájlt úgy, hogy az a következőhöz igazodva hozzáadja a várólista-kötést a http-kötés után:

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

Módosítsa a *function. JSON* fájlt úgy, hogy az a következőhöz igazodva hozzáadja a várólista-kötést a http-kötés után:

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

Módosítsa a *function. JSON* fájlt úgy, hogy az a következőhöz igazodva hozzáadja a várólista-kötést a http-kötés után:

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

Módosítsa a *function. JSON* fájlt úgy, hogy az a következőhöz igazodva hozzáadja a várólista-kötést a http-kötés után:

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

A kötés meghatározása után a kötés neve (ebben az esetben `msg`) argumentumként jelenik meg a függvény kódjában (vagy a `context` objektumban JavaScript és írógéppel). Ezt a változót azután használhatja, hogy üzeneteket írjon a várólistába. Bármilyen kódot kell írnia a hitelesítéshez, a várólista-hivatkozás beszerzéséhez vagy az adatíráshoz. Az összes ilyen integrációs feladat kényelmesen kezelhető a Azure Functions futtatókörnyezetben és a várólista kimeneti kötésében.

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

### <a name="update-the-image-in-the-registry"></a>A rendszerkép frissítése a beállításjegyzékben

1. A gyökérkönyvtárban futtassa újra `docker build`, és ezúttal frissítse a címkében szereplő verziót `v1.0.1`. Ahogy korábban is, cserélje le a `<docker_id>`t a Docker hub-fiók azonosítójával:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. A frissített képet visszaküldheti a tárházba `docker push`:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Mivel a folyamatos teljesítést konfigurálta, a beállításjegyzékben lévő rendszerkép frissítése ismét automatikusan frissíti a Function alkalmazást az Azure-ban.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Az üzenet megtekintése az Azure Storage-várólistában

A böngészőben a függvény meghívásához ugyanazt az URL-címet használja. A böngészőnek ugyanazt a választ kell megjelennie, mint korábban, mert nem módosította a függvény kódjának részét. A hozzáadott kód azonban egy üzenetet írt a `name` URL paraméterrel a `outqueue` Storage-várólistához.

Az üzenetsor a [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) vagy a [Microsoft Azure Storage Explorerban](https://storageexplorer.com/)tekinthető meg. Az üzenetsor az Azure CLI-ben is megtekinthető az alábbi lépésekben leírtak szerint:

1. Nyissa meg a Function projekt *Local. Setting. JSON* fájlt, és másolja a kapcsolatok karakterláncának értékét. Egy terminál-vagy parancssori ablakban futtassa a következő parancsot egy `AZURE_STORAGE_CONNECTION_STRING`nevű környezeti változó létrehozásához, és illessze be az adott kapcsolódási karakterláncot a `<connection_string>`helyére. (Ez a környezeti változó azt jelenti, hogy a `--connection-string` argumentum használatával nem kell megadnia a kapcsolódási karakterláncot minden további parancshoz.)

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. Választható A [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) parancs használatával megtekintheti a fiókban található tárolási várólistákat. A parancs kimenetének tartalmaznia kell egy `outqueue`nevű várólistát, amely akkor jött létre, amikor a függvény első üzenetét írta az adott várólistára.
    
    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. A [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) parancs használatával megtekintheti az ebben a várólistában lévő üzeneteket, amelyeknek a függvény korábbi tesztelésekor használt utónévnek kell lennie. A parancs [Base64 kódolásban](functions-bindings-storage-queue.md#encoding)kérdezi le az első üzenetet a várólistában, ezért a szövegként való megjelenítéshez is dekódolnia kell az üzenetet.

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    Mivel az üzenetek gyűjtését és a Base64-ből való dekódolását kell elvégeznie, futtassa a PowerShellt, és használja a PowerShell-parancsot.

    ---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az oktatóanyagban létrehozott erőforrásokkal szeretné tovább használni az Azure-függvényt, akkor az összes erőforrást helyben hagyhatja. Mivel prémium szintű csomagot hozott létre a Azure Functionshoz, a folyamatos költségek napi egy vagy két USD-t foglalnak magukban.

A folyamatos költségek elkerülése érdekében törölje a `AzureFunctionsContainer-rg` erőforráscsoportot az adott csoport összes erőforrásának tisztításához: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Következő lépések

+ [Figyelési függvények](functions-monitoring.md)
+ [Méretezési és üzemeltetési lehetőségek](functions-scale.md)
+ [Kubernetes-alapú kiszolgáló nélküli üzemeltetés](functions-kubernetes-keda.md)
