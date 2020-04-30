---
title: Azure Functions létrehozása Linuxon egyéni rendszerkép használatával
description: Megismerheti, hogyan hozhat létre egyéni Linux-rendszerképeken futó Azure Functions-függvényeket.
ms.date: 03/30/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: fee4e16bd77664e541eeb36cb807a77d13191899
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82165722"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Függvény létrehozása Linux rendszeren egyéni tároló használatával

Ebben az oktatóanyagban létrehozhatja és üzembe helyezheti a kódot úgy, hogy az egyéni Docker-tárolóként Azure Functions egy Linux-alapú alaprendszerkép használatával. Általában egyéni rendszerképeket használ, ha a függvények egy adott nyelvi verziót igényelnek, vagy olyan függőséget vagy konfigurációt használnak, amelyet a beépített rendszerkép nem biztosít.

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

Ezt az oktatóanyagot követheti Windows, macOS vagy Linux rendszerű számítógépeken is. Az oktatóanyag elvégzése után az Azure-fiókban néhány USA-dollárért kell fizetnie.

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [Docker-azonosító](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Futtassa `docker login` a parancsot a Docker-ba való bejelentkezéshez. Ez a parancs meghiúsul, ha a Docker nem fut, amely esetben a Docker elindítása után próbálja megismételni a parancsot.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>A local functions projekt létrehozása és tesztelése

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Egy terminálon vagy parancssorban futtassa a következő parancsot a választott nyelvhez, és hozzon létre egy Function app-projektet egy `LocalFunctionsProject`nevű mappában.  
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

# <a name="bash"></a>[bash](#tab/bash)
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

A Maven megkéri, hogy a projektnek a telepítéskor való létrehozásának befejezéséhez szükséges értékeket is megkeresse.   
Ha a rendszer kéri, adja meg a következő értékeket:

| Kérdés | Érték | Leírás |
| ------ | ----- | ----------- |
| **csoportazonosító** | `com.fabrikam` | Egy érték, amely egyedileg azonosítja a projektet az összes projektben, a Java [csomag elnevezési szabályait](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) követve. |
| **artifactId** | `fabrikam-functions` | Egy érték, amely a jar neve, verziószám nélkül. |
| **verziója** | `1.0-SNAPSHOT` | Válassza ki az alapértelmezett értéket. |
| **csomag** | `com.fabrikam.functions` | Egy érték, amely a generált függvény kódjához tartozó Java-csomag. Használja az alapértelmezettet. |

A `Y` megerősítéshez írja be vagy nyomja le az ENTER billentyűt.

A Maven létrehoz egy új, _artifactId_nevű mappában található projektfájlt, amely ebben a példában a `fabrikam-functions`. 
::: zone-end
A `--docker` beállítás létrehoz egy `Dockerfile` projektet a projekthez, amely egy megfelelő egyéni tárolót határoz meg a Azure functions és a kiválasztott futtatókörnyezettel való használatra.

Navigáljon a projekt mappájába:
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
Adjon hozzá egy függvényt a projekthez a következő parancs használatával, ahol `--name` az argumentum a függvény egyedi neve, és az `--template` argumentum megadja a függvény triggerét. `func new`hozzon létre egy olyan almappát, amely megfelel a projekt választott nyelvének és a *function. JSON*nevű konfigurációs fájlnak, amely tartalmazza a függvény nevét.

```
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end  
A függvény helyi teszteléséhez indítsa el a helyi Azure Functions futásidejű gazdagépet a projekt mappájának gyökerében: 
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
Ha a `HttpExample` végpont megjelenik a kimenetben, keresse meg a következőt: `http://localhost:7071/api/HttpExample?name=Functions`. A böngészőnek egy "Hello" üzenetet kell megjelenítenie `Functions`, amely visszaismétli a `name` lekérdezési paraméternek megadott értéket.

A gazdagép leállításához használja a CTRL**C** **billentyűt**-.

## <a name="build-the-container-image-and-test-locally"></a>A tároló rendszerképének létrehozása és helyi tesztelése

Választható Vizsgálja meg a * Docker a projekt mappájának gyökerében. A Docker leírja a szükséges környezetet a Function alkalmazás Linux rendszeren való futtatásához.  A Azure Functions által támogatott alaplemezképek teljes listája megtalálható a [Azure functions alap lemezképe lapon](https://hub.docker.com/_/microsoft-azure-functions-base).
    
A legfelső szintű projekt mappában futtassa a [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) parancsot, és adjon meg egy nevet `azurefunctionsimage`, és egy címkét `v1.0.0`. A `<DOCKER_ID>` helyére a Docker Hub-fiók azonosítóját írja. Ez a parancs létrehozza a tároló Docker-rendszerképét.

```
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Ha a parancs befejeződik, az új tárolót helyileg is futtathatja.
    
A Build teszteléséhez futtassa a rendszerképet egy helyi tárolóban a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) paranccsal, és cserélje le `<DOCKER_ID` újra a Docker-azonosítóval, és adja hozzá `-p 8080:80`a portok argumentumot:

```
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Miután a rendszerkép egy helyi tárolóban fut, nyisson meg egy böngészőt `http://localhost:8080`, amely megjeleníti az alább látható helyőrző képet. A rendszerkép ekkor jelenik meg, mivel a függvény a helyi tárolóban fut, ahogy az az Azure-ban lenne, ami azt jelenti, hogy egy, a *function. JSON* fájlban definiált hozzáférési kulccsal védi `"authLevel": "function"` a tulajdonságot. A tároló még nem lett közzétéve egy Azure-beli Function alkalmazásban, így a kulcs még nem érhető el. Ha tesztelni szeretné a helyi tárolót, állítsa le a Docker-t, módosítsa az `"authLevel": "anonymous"`engedélyezési tulajdonságot a értékre, hozza létre újra a rendszerképet, és indítsa újra a Docker-t. Ezután állítsa `"authLevel": "function"` alaphelyzetbe a *function. JSON*fájlt. További információ: [engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Helyőrző képe, amely azt jelzi, hogy a tároló helyileg fut](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Ha a rendszerkép egy helyi tárolóban fut, keresse meg a `http://localhost:8080/api/HttpExample?name=Functions`(z) parancsot, amely a korábban megjelenő "Hello" üzenetet jeleníti meg. Mivel a Maven archetípusa olyan HTTP által aktivált függvényt hoz létre, amely névtelen hitelesítést használ, továbbra is meghívhatja a függvényt annak ellenére, hogy a tárolóban fut. 
::: zone-end  

Miután ellenőrizte a Function alkalmazást a tárolóban, állítsa le a Docker-t a CTRL**C** **billentyűkombinációval**+.

## <a name="push-the-image-to-docker-hub"></a>A rendszerkép leküldése a Docker hub-ra

A Docker hub egy tároló-beállításjegyzék, amely képeket és lemezképeket és tároló szolgáltatásokat biztosít. Ha meg szeretné osztani a lemezképet, amely magában foglalja az Azure-ba való üzembe helyezést, azt egy beállításjegyzékbe kell leküldeni.

1. Ha még nem jelentkezett be a Docker-be, ezt a [Docker-bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) paranccsal teheti `<docker_id>` meg, és cserélje le a Docker-azonosítóra. Ez a parancs megkéri a felhasználónevet és a jelszót. A "sikeres bejelentkezés" üzenet megerősíti, hogy bejelentkezett.

    ```
    docker login
    ```
    
1. Miután bejelentkezett, küldje le a rendszerképet Docker hub-ra a Docker [push](https://docs.docker.com/engine/reference/commandline/push/) paranccsal, majd ismét cserélje `<docker_id>` le a Docker-azonosítóra.

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
    
1. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példában létrehozunk egy nevű `AzureFunctionsContainers-rg` erőforráscsoportot a `westeurope` régióban. (Az erőforráscsoport és az erőforrások általában az Ön közelében lévő régióban hozhatók létre a `az account list-locations` parancsból elérhető régió használatával.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Nem futtathat Linux-és Windows-alkalmazásokat ugyanabban az erőforráscsoporthoz. Ha már van egy nevű `AzureFunctionsContainers-rg` erőforráscsoport egy Windows-függvény alkalmazással vagy webalkalmazással, egy másik erőforráscsoportot kell használnia.
    
1. Hozzon létre egy általános célú Storage-fiókot az erőforráscsoport és a régió területén az az [Storage Account Create](/cli/azure/storage/account#az-storage-account-create) paranccsal. A következő példában cserélje le `<storage_name>` egy globálisan egyedi névre, amely az Ön számára megfelelő. A névnek három – 24 karakterből kell állnia, és csak kisbetűket tartalmazhat. `Standard_LRS`egy tipikus általános célú fiókot határoz meg.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Ebben az oktatóanyagban a Storage-fióknak csupán néhány USD-es százaléka van.
    
1. A (z) paranccsal hozzon létre egy prémium szintű `myPremiumPlan` Azure functions csomagot a **rugalmas prémium 1** díjszabási`--sku EP1`szinten (), a Nyugat-európai`-location westeurope`régióban (vagy az Ön közelében egy megfelelő régió használatával), és egy Linux-`--is-linux`tárolóban ().

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Az egyéni functions-tárolók Linux-üzemeltetése [dedikált (App Service) csomagok](functions-scale.md#app-service-plan) és [prémium csomagok](functions-premium-plan.md#features)esetén támogatott. Az itt található prémium csomagot használjuk, amely igény szerint méretezhető. További információk az üzemeltetésről: [Azure Functions szolgáltatási csomagok összehasonlítása](functions-scale.md). A költségek kiszámításához tekintse meg a [functions díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/functions/).

    A parancs egy kapcsolódó Azure Application Insights-példányt is kiépít ugyanabban az erőforráscsoporthoz, amellyel nyomon követheti a Function alkalmazást, és megtekintheti a naplókat. További információ: [Azure functions figyelése](functions-monitoring.md). A példány nem jár költséggel, amíg be nem aktiválja.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Function-alkalmazás létrehozása és konfigurálása az Azure-ban a rendszerképpel

Az Azure-beli Function-alkalmazás kezeli a függvények végrehajtását a üzemeltetési tervben. Ebben a szakaszban az előző szakasz Azure-erőforrásaival hozzon létre egy Function alkalmazást a Docker hub egyik rendszerképéről, és konfigurálja azt egy Azure Storage-hoz tartozó kapcsolódási karakterlánccal.

1. Hozza létre a functions alkalmazást az az [functionapp Create](/cli/azure/functionapp#az-functionapp-create) parancs használatával. A következő példában cserélje le `<storage_name>` a nevet az előző szakaszban használt névre a Storage-fiókhoz. Cserélje le `<app_name>` egy globálisan egyedi névre is, és `<docker_id>` a Docker-azonosítójával.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Az *üzembe helyezés-Container-rendszerkép-Name* paraméter megadja a Function alkalmazáshoz használandó rendszerképet. Az az [functionapp config Container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) parancs használatával megtekintheti az üzembe helyezéshez használt rendszerképpel kapcsolatos információkat. Egy másik rendszerképből is üzembe helyezheti az az [functionapp config Container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) parancsot.

1. Kérje le a létrehozott Storage-fiókhoz tartozó kapcsolati karakterláncot az az [Storage Account show-kapcsolat-string](/cli/azure/storage/account) paranccsal, és rendelje hozzá egy rendszerhéj `storageConnectionString`-változóhoz:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Adja hozzá ezt a beállítást a Function alkalmazáshoz az az [functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) parancs használatával. A következő parancsban cserélje le `<app_name>` a kifejezést a Function alkalmazás nevére, és cserélje le `<connection_string>` az előző lépéshez tartozó (hosszú kódolású karakterláncot, amely a "DefaultEndpointProtocol =" kifejezéssel kezdődik):
 
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
> Ha egyéni rendszerképet tesz közzé egy privát Container-fiókban, ehelyett környezeti változókat kell használnia a Docker a kapcsolódási karakterlánchoz. További információ: [env utasítás](https://docs.docker.com/engine/reference/builder/#env). A változókat `DOCKER_REGISTRY_SERVER_USERNAME` és `DOCKER_REGISTRY_SERVER_PASSWORD`a értéket is be kell állítania. Az értékek használatához újra kell építenie a rendszerképet, le kell küldenie a rendszerképet a beállításjegyzékbe, majd újra kell indítania a Function alkalmazást az Azure-ban.

## <a name="verify-your-functions-on-azure"></a>A függvények ellenőrzése az Azure-ban

Az Azure-beli Function alkalmazásban üzembe helyezett képpel a függvényt HTTP-kérelmeken keresztül hívhatja meg. Mivel a *function. JSON* definíciója tartalmazza a `"authLevel": "function"`tulajdonságot, először be kell szereznie a hozzáférési kulcsot (más néven "Function Key"), és a végpontra irányuló kérésekben URL-ként kell megadni.

1. A függvény URL-címének lekérése a hozzáférési (Function) kulccsal a Azure Portal használatával vagy az Azure CLI használatával a `az rest` paranccsal.)

    # <a name="portal"></a>[Portál](#tab/portal)

    1. Jelentkezzen be a Azure Portalba, majd keresse meg a Function alkalmazást a függvény alkalmazás nevének megadásával az oldal tetején található **keresőmezőbe** . Az eredmények között válassza ki a **app Service** erőforrást.

    1. A bal oldali navigációs panel **functions (csak olvasás)** területén válassza ki a függvény nevét.

    1. A részletek panelen válassza a **</> függvény URL-címének beolvasása**lehetőséget:
    
        ![A funkció URL-címének beolvasása parancs a Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. Az előugró ablakban válassza az **alapértelmezett (Function Key)** elemet, majd **másolja a vágólapra**. A kulcs a következő `?code=`karakterből álló karakterlánc.

        ![A függvény URL-címének másolása a Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Mivel a Function alkalmazás tárolóként van telepítve, nem módosíthatja a függvény kódját a portálon. Ehelyett frissítenie kell a projektet a helyi rendszerképben, küldje újra a rendszerképet a beállításjegyzékbe, majd telepítse újra az Azure-ba. A folyamatos üzembe helyezést egy későbbi szakaszban állíthatja be.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. A következő formátumban hozza létre az URL-karakterláncot `<subscription_id>`, `<resource_group>`és `<app_name>` cserélje le az Azure-előfizetés azonosítóját, a Function alkalmazás erőforráscsoporthoz, valamint a Function alkalmazás nevét:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Előfordulhat például, hogy az URL-cím a következő címet keresi:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > A kényelem érdekében Ehelyett hozzárendelheti az URL-címet egy környezeti változóhoz, és használhatja azt `az rest` a parancsban.
    
    1. Futtassa az alábbi `az rest` parancsot (az Azure CLI 2.0.77 és újabb verzióiban érhető el), `<uri>` és cserélje le az elemet az utolsó lépés URI-karakterláncára, az idézőjelekkel együtt:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. A parancs kimenete a függvény kulcsa. A teljes függvény URL-címe `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`ekkor a `<app_name>`, `<function_name>`a és `<key>` a értékre van lecserélve az adott értékeknél.
    
        > [!NOTE]
        > A beolvasott kulcs a functions alkalmazás összes függvényéhez használható *gazdagép* kulcsa. a portálon megjelenített metódus csak az egyetlen függvény kulcsát kérdezi le.

    ---

1. Illessze be a függvény URL-címét a böngésző címsorába, és adja hozzá `&name=Azure` a paramétert az URL-cím végéhez. A böngészőben a "Hello Azure" szövegnek kell megjelennie.

    ![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Az engedélyezés teszteléséhez távolítsa el a Code = paramétert az URL-címről, és ellenőrizze, hogy nem kap-e választ a függvénytől.


## <a name="enable-continuous-deployment-to-azure"></a>Folyamatos üzembe helyezés engedélyezése az Azure-ban

Engedélyezheti, hogy a Azure Functions automatikusan frissítse a rendszerkép központi telepítését, amikor frissíti a rendszerképet a beállításjegyzékben.

1. Engedélyezze a folyamatos üzembe helyezést az az [functionapp Deployment Container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) parancs `<app_name>` használatával, a függvény alkalmazásának nevével lecserélve:

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

1. A Docker fűzze hozzá a karakterláncot `-appservice` az `FROM` utasításban található alaprendszerképhez:

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

1. Hozza létre újra a rendszerképet a `docker build` paranccsal, és cserélje `<docker_id>` le a Docker-azonosítóra:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Küldje le a frissített rendszerképet a Docker hub szolgáltatásba, amely jóval kevesebb időt vesz igénybe, mint az első leküldéses példány, amelyet a rendszerképnek csak a frissített szegmenseit kell feltölteni.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions automatikusan újra üzembe helyezi a rendszerképet a functions alkalmazásban; a folyamat egy percnél rövidebb ideig tart.

1. Egy böngészőben nyissa meg `https://<app_name>.scm.azurewebsites.net/`a kifejezést `<app_name>` , és cserélje le az egyedi névre. Ez az URL-cím a Function app-tároló speciális eszközök (kudu) végpontja.

1. Jelentkezzen be az Azure-fiókjába, majd válassza ki az **SSH** -t, és hozzon létre kapcsolatot a tárolóval. A csatlakozás eltarthat néhány másodpercig, ha az Azure továbbra is frissíti a tároló rendszerképét.

1. Miután létrejött a kapcsolatok a tárolóval, futtassa a `top` parancsot az aktuálisan futó folyamatok megtekintéséhez. 

    ![SSH-munkamenetben futó Linux-alapú leggyakoribb parancs](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Írás Azure Storage-várólistába

Azure Functions lehetővé teszi a függvények más Azure-szolgáltatásokhoz és-erőforrásokhoz való összekapcsolását, mivel saját integrációs kódot kell megírnia. Ezek a *kötések*, amelyek a bemeneti és a kimeneti adatokat jelölik, a függvény definíciójában vannak deklarálva. A kötések adatai a függvények számára paraméterekként vannak megadva. Az *trigger* egy speciális típusú bemeneti kötés. Bár a függvénynek csak egy triggere van, több bemeneti és kimeneti kötés is lehet. További információ: [Azure functions triggerek és kötések fogalmai](functions-triggers-bindings.md).

Ez a szakasz bemutatja, hogyan integrálhatja a függvényt egy Azure Storage-üzenetsor használatával. Az ehhez a függvényhez hozzáadott kimeneti kötés egy HTTP-kérelemből adatokat ír a várólistában lévő üzenetbe.

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

Ha a várólista-kötés definiálva van, most frissítheti a függvényt `msg` , hogy megkapja a kimeneti paramétert, és üzeneteket írjon a várólistába.

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

### <a name="update-the-image-in-the-registry"></a>A rendszerkép frissítése a beállításjegyzékben

1. A gyökérkönyvtárban futtassa újra a `docker build` parancsot, és ezúttal frissítse a címkében szereplő verziót `v1.0.1`. Ahogy korábban is, `<docker_id>` cserélje le a Docker hub-fiók azonosítóját:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Küldje vissza a frissített képet a tárházba a `docker push`következővel:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Mivel a folyamatos teljesítést konfigurálta, a beállításjegyzékben lévő rendszerkép frissítése ismét automatikusan frissíti a Function alkalmazást az Azure-ban.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Az üzenet megtekintése az Azure Storage-várólistában

A böngészőben a függvény meghívásához ugyanazt az URL-címet használja. A böngészőnek ugyanazt a választ kell megjelennie, mint korábban, mert nem módosította a függvény kódjának részét. A hozzáadott kód azonban a `name` `outqueue` Storage-üzenetsor URL-címének használatával írt egy üzenetet.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az oktatóanyagban létrehozott erőforrásokkal szeretné tovább használni az Azure-függvényt, akkor az összes erőforrást helyben hagyhatja. Mivel prémium szintű csomagot hozott létre a Azure Functionshoz, a folyamatos költségek napi egy vagy két USD-t foglalnak magukban.

A folyamatos költségek elkerülése érdekében törölje az `AzureFunctionsContainer-rg` erőforráscsoportot a csoport összes erőforrásának tisztításához: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>További lépések

+ [Figyelési függvények](functions-monitoring.md)
+ [Méretezési és üzemeltetési lehetőségek](functions-scale.md)
+ [Kubernetes-alapú kiszolgáló nélküli üzemeltetés](functions-kubernetes-keda.md)
