---
title: Azure-függvények létrehozása Linuxon egyéni lemezkép használatával
description: Megismerheti, hogyan hozhat létre egyéni Linux-rendszerképeken futó Azure Functions-függvényeket.
ms.date: 03/30/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 44ca8f721967b90be283f867f8656344ec3f1906
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673414"
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

Az oktatóanyag ot bármely Windows, macOS vagy Linux rendszert futtató számítógépen követheti. Az oktatóanyag befejezése néhány amerikai dollár költségét fogja kitölteni az Azure-fiókjában.

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker között](https://docs.docker.com/install/)  

+ [Docker-azonosító](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Fuss, `docker login` hogy jelentkezzen be a Dockerbe. Ez a parancs sikertelen, ha a Docker nem fut, ebben az esetben indítsa el a docker-t, és próbálkozzon újra a paranccsal.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>A helyi függvényprojekt létrehozása és tesztelése

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Terminál- vagy parancssorban futtassa a következő parancsot a kiválasztott nyelvhez, és hozzon létre egy függvényalkalmazás-projektet a nevű `LocalFunctionsProject`mappában.  
::: zone-end  
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
::: zone pivot="programming-language-java"  
Egy üres mappában futtassa a következő parancsot a Functions-projekt [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)-ból való létrehozásához.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
---

A Maven olyan értékeket kér, amelyek a projekt üzembe helyezéséhez szükségesek.   
Amikor a rendszer kéri, adja meg a következő értékeket:

| Kérdés | Érték | Leírás |
| ------ | ----- | ----------- |
| **csoportazonosító** | `com.fabrikam` | Olyan érték, amely egyedileg azonosítja a projektet az összes projektben, a Java [csomagelnevezési szabályainak](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) megfelelően. |
| **artifactId** | `fabrikam-functions` | Az edény neve verziószám nélküli érték. |
| **Változat** | `1.0-SNAPSHOT` | Válassza ki az alapértelmezett értéket. |
| **Csomag** | `com.fabrikam.functions` | Olyan érték, amely a létrehozott függvénykód Java-csomagja. Használja az alapértelmezettet. |

A `Y` megerősítéshez írja be vagy nyomja le az Enter billentyűt.

A Maven egy új mappában hozza létre a projektfájlokat, `fabrikam-functions`amelynek neve _artifactId_, amely ebben a példában a. 
::: zone-end
A `--docker` beállítás létrehoz `Dockerfile` egy a projekt, amely meghatározza a megfelelő egyéni tároló azure functions és a kiválasztott futásidejű használható.

Navigálás a projekt mappájába:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Adjon hozzá egy függvényt a projekthez `--name` a következő paranccsal, ahol `--template` az argumentum a függvény egyedi neve, és az argumentum megadja a függvény eseményindítóját. `func new`hozzon létre egy olyan almappát, amely megfelel a projekt választott nyelvének megfelelő kódfájlt tartalmazó függvénynévnek, valamint egy *function.json*nevű konfigurációs fájlt.

```
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end  
A függvény helyi teszteléséhez indítsa el a helyi Azure Functions futásidejű állomást a projektmappa gyökerében: 
::: zone pivot="programming-language-csharp"  
```
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
mvn clean package  
mvn azure-functions:run
```
::: zone-end
Ha megjelenik `HttpExample` a végpont a kimenetben, keresse meg a t. [`http://localhost:7071/api/HttpExample?name=Functions`](http://localhost:7071/api/HttpExample?name=Functions) A böngészőnek meg kell jelennie egy `Functions`"hello" üzenetet, amely visszhangzik vissza , a `name` lekérdezési paraméterhez megadott érték.

A **Ctrl C billentyűvel**-**C** állítsa le az állomást.

## <a name="build-the-container-image-and-test-locally"></a>A tárolórendszerkép létrehozása és helyi tesztelése

(Nem kötelező) Vizsgálja meg a *Dockerfile" fájlt a projektmappa gyökerében. A Dockerfile ismerteti a szükséges környezetet a függvényalkalmazás linuxos futtatásához.  Az Azure Functions támogatott alaplemezképeinek teljes listája megtalálható az [Azure Functions alaplemezképe oldalon.](https://hub.docker.com/_/microsoft-azure-functions-base)
    
A gyökérprojekt mappájában futtassa a [docker](https://docs.docker.com/engine/reference/commandline/build/) build `azurefunctionsimage`parancsot, `v1.0.0`és adjon meg egy nevet , és címkézze meg a . A `<DOCKER_ID>` helyére a Docker Hub-fiók azonosítóját írja. Ez a parancs létrehozza a tároló Docker-rendszerképét.

```
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Amikor a parancs befejeződik, futtathatja az új tárolót helyileg.
    
A build teszteléséhez futtassa a lemezképet egy helyi tárolóban a [docker run](https://docs.docker.com/engine/reference/commandline/run/) paranccsal, cserélje le újra `<DOCKER_ID` a Docker-azonosítót, és adja hozzá a portok argumentumát: `-p 8080:80`

```
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Miután a kép egy helyi tárolóban `http://localhost:8080`fut, nyisson meg egy böngészőt a számára, amelynek meg kell jelenítenie az alábbi helyőrző képet. A rendszerkép jelenik meg ezen a ponton, mert a függvény fut a helyi tárolóban, ahogy az Azure-ban, ami `"authLevel": "function"` azt jelenti, hogy a *függvény.json* a tulajdonsággal meghatározott hozzáférési kulcs védi. A tároló még nem lett közzétéve egy függvényalkalmazásban az Azure-ban, így a kulcs még nem érhető el. Ha a helyi tárolóval szeretné tesztelni, állítsa le `"authLevel": "anonymous"`a docker-t, módosítsa az engedélyezési tulajdonságot , építse újra a lemezképet, és indítsa újra a dockert. Ezután `"authLevel": "function"` állítsa vissza a *function.json*. További információt az [engedélyezési kulcsok című témakörben talál.](functions-bindings-http-webhook-trigger.md#authorization-keys)

![Helyőrző lemezkép, amely jelzi, hogy a tároló helyileg fut](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Miután a rendszerkép egy helyi [`http://localhost:8080/api/HttpExample?name=Functions`](http://localhost:8080/api/HttpExample?name=Functions)tárolóban fut, keresse meg a tallózással, amelynek ugyanazt a "hello" üzenetet kell megjelenítenie, mint korábban. Mivel a Maven archetípus létrehoz egy HTTP-aktivált függvényt, amely névtelen engedélyezést használ, akkor is meghívhatja a függvényt, még akkor is, ha az a tárolóban fut. 
::: zone-end  

Miután ellenőrizte a függvényalkalmazást a tárolóban, állítsa le a docker-t **a Ctrl C-vel.**+**C**

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

1. Jelentkezzen be az Azure-fiókjába, majd válassza ki az **SSH-t** a tárolóval való kapcsolat létrehozásához. A csatlakozás eltarthat néhány pillanatig, ha az Azure még mindig frissíti a tárolórendszerképet.

1. Miután létrejött egy kapcsolat a `top` tárolóval, futtassa a parancsot az éppen futó folyamatok megtekintéséhez. 

    ![Linux felső parancs fut egy SSH munkamenetben](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Írás egy Azure Storage-várólistába

Az Azure Functions lehetővé teszi, hogy a függvényeket más Azure-szolgáltatásokhoz és erőforrásokhoz csatlakoztassa, amelyeknek saját integrációs kódot kell írniuk. Ezek *a kötések*, amelyek mind a bemeneti, mind a kimeneti kötéseket képviselik, a függvénydefinícióban vannak deklarálva. A kötések adatai a függvények számára paraméterekként vannak megadva. Az *eseményindító* a bemeneti kötés egy speciális típusa. Bár egy függvény csak egy eseményindítóval rendelkezik, több bemeneti és kimeneti kötéssel rendelkezhet. További információ: [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md).

Ez a szakasz bemutatja, hogyan integrálhatja a függvényt egy Azure Storage-várólistával. A függvényhez hozzáadott kimeneti kötés http-kérelemből adatokat ír a várólistában lévő üzenetbe.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

## <a name="add-code-to-use-the-output-binding"></a>Kód hozzáadása a kimeneti kötés használatához

A várólista-kötés definiálva most frissítheti `msg` a függvényt, hogy megkapja a kimeneti paramétert, és üzeneteket írjon a várólistába.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
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

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

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
