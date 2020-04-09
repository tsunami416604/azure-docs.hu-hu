---
title: Hozzon létre egy függvényt az Azure-ban, amely válaszol a HTTP-kérésekre
description: Ismerje meg, hogyan hozhat létre egy függvényt a parancssorból, majd tegye közzé a helyi projektet kiszolgáló nélküli üzemeltetésre az Azure Functionsben.
ms.date: 03/30/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c1e1b9912109ae8a7361c9d0d776ca1810a90d5c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886636"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Rövid útmutató: Hozzon létre egy függvényt az Azure-ban, amely válaszol a HTTP-kérésekre

Ebben a cikkben parancssori eszközökkel hozhat létre olyan függvényt, amely válaszol a HTTP-kérésekre. Miután helyileg tesztelte a kódot, üzembe helyezi azt az Azure Functions kiszolgáló nélküli környezetében. A rövid útmutató végrehajtása néhány USD centvagy annál kevesebb költséget jelent az Azure-fiókjában.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
A cikknek van egy [Visual Studio kódalapú változata](functions-create-first-function-vs-code.md) is.
::: zone-end  

::: zone pivot="programming-language-java"  
> [!NOTE]
> Ha Maven nem a preferált fejlesztési eszköz, nézd meg a hasonló útmutatók Java devlopers segítségével [Gradle](/azure/azure-functions/functions-create-first-java-gradle), [IntelliJ IDEA](/azure/java/intellij/azure-toolkit-for-intellij-quickstart-functions) és [VS Kód](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Helyi függvényprojekt létrehozása

Az Azure Functions egy függvényprojekt egy tároló egy vagy több egyedi függvények, amelyek mindegyike reagál egy adott eseményindító. A projekt összes függvénye azonos helyi és üzemeltetési konfigurációval rendelkezik. Ebben a szakaszban olyan függvényprojektet hoz létre, amely egyetlen függvényt tartalmaz.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Futtassa a `func init` parancsot az alábbiak szerint, hogy hozzon létre egy függvényprojektet egy *LocalFunctionProj* nevű mappában a megadott futásidővel:  
::: zone-end  
::: zone pivot="programming-language-python"  
```
func init LocalFunctionProj --python
```
::: zone-end  
::: zone pivot="programming-language-csharp"  
```
func init LocalFunctionProj --dotnet
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```
func init LocalFunctionProj --javascript
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
func init LocalFunctionProj --typescript
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```
func init LocalFunctionProj --powershell
```
::: zone-end    
::: zone pivot="programming-language-java"  
Egy üres mappában futtassa a következő parancsot a Functions-projekt [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)-ból való létrehozásához.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" 
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" 
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
Ez a mappa különböző fájlokat tartalmaz a projekthez, beleértve a [local.settings.json](functions-run-local.md#local-settings-file) és [host.json](functions-host-json.md)nevű konfigurációkat. Mivel *a local.settings.json* tartalmazhat az Azure-ból letöltött titkokat, a fájl alapértelmezés szerint ki van zárva a forrásvezérlésből a *.gitignore* fájlban.

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>(Nem kötelező) A fájl tartalmának vizsgálata

Ha szükséges, ugorjon [a Funkció helyi futtatása](#run-the-function-locally) lehetőségre, és később megvizsgálhatja a fájl tartalmát.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* tartalmaz `Run` egy metódust, amely `req` megkapja a kérelem adatait a változó egy [HttpRequest,](/dotnet/api/microsoft.aspnetcore.http.httprequest) amely díszített a **HttpTriggerAttribute**, amely meghatározza az eseményindító viselkedését. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

A visszatérési objektum egy [ActionResult,](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) amely válaszüzenetet ad vissza [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) vagy [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) értékben. További információ: [Azure Functions HTTP triggerek és kötések](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Függvény.java
*A Function.java* tartalmaz egy `run` metódust, `request` amely a változóban lévő kérelemadatokat fogadja, egy [HttpRequestMessage,](/java/api/com.microsoft.azure.functions.httprequestmessage) amely a [HttpTrigger-kommentárral](/java/api/com.microsoft.azure.functions.annotation.httptrigger) van díszítve, amely meghatározza az eseményindító viselkedését. 

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java":::

A válaszüzenetet a [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API hozza létre.

#### <a name="pomxml"></a>pom.xml

Az alkalmazás üzemeltetéséhez létrehozott Azure-erőforrások beállításai a bővítmény **konfigurációs** elemében vannak `com.microsoft.azure` definiálva, a létrehozott pom.xml fájlban lévő **groupId** azonosítóval. Az alábbi konfigurációs elem például arra utasítja a Maven-alapú központi `westus` telepítést, hogy hozzon létre egy függvényalkalmazást a `java-functions-group` régió erőforráscsoportjában. Maga a függvényalkalmazás a `java-functions-app-service-plan` tervben tárolt Windows rendszeren fut, amely alapértelmezés szerint kiszolgáló nélküli felhasználási csomag.    

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="116-155":::

Ezeket a beállításokat módosíthatja az erőforrások Azure-beli `runtime.os` `windows` létrehozásának szabályozásához, például a kezdeti üzembe helyezés `linux` előtti módosítással. A Maven beépülő modul által támogatott beállítások teljes listáját a [konfiguráció részleteiben találja.](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)

#### <a name="functiontestjava"></a>FunctionTest.java

Az archetípus egységtesztet is generál a funkcióhoz. Ha módosítja a függvényt, hogy kötéseket adjon hozzá, vagy új függvényeket adjon a projekthez, akkor a *FunctionTest.java* fájlban lévő teszteket is módosítania kell.
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_Az\_\_init .py* egy `main()` Python függvényt tartalmaz, amely a *function.json*konfigurációjának megfelelően aktiválódik.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

HTTP-eseményindító esetén a függvény kérésadatokat `req` kap a *function.json*ban meghatározott változóban. `req`az [azure.functions.HttpRequest osztály](/python/api/azure-functions/azure.functions.httprequest)egy példánya. A `$return` *függvény.json*ban definiált visszatérési objektum az [azure.functions.HttpResponse osztály](/python/api/azure-functions/azure.functions.httpresponse)egy példánya. További információ: [Azure Functions HTTP triggerek és kötések](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*Az index.js* olyan függvényt exportál, amely a *function.json*konfigurációjának megfelelően aktiválódik.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

HTTP-eseményindító esetén a függvény kérésadatokat `req` kap a *function.json*ban meghatározott változóban. A `$return` *függvény.json*néven definiált visszatérési objektum a válasz. További információ: [Azure Functions HTTP triggerek és kötések](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*Az index.ts* olyan függvényt exportál, amely a *function.json*konfigurációjának megfelelően aktiválódik.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

HTTP-eseményindító esetén a függvény a `req` *function.json*ban meghatározott **HttpRequest** változóban kapja a kérelemadatokat. A `$return` *függvény.json*néven definiált visszatérési objektum a válasz. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>futtatás.ps1

*A run.ps1* egy függvényparancsfájlt határoz meg, amely a *function.json*konfigurációjának megfelelően aktiválódik.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

HTTP-eseményindító esetén a függvény a `$Request` *function.json*ban definiált param-nak átadott kérelemadatokat kap. A `Response` *függvény.json*néven definiált visszatérési objektum `Push-OutputBinding` válaszként a parancsmagba kerül. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*A function.json* egy konfigurációs fájl, `bindings` amely meghatározza a függvény bemenetét és kimenetét, beleértve az eseményindító típusát is. 
::: zone-end

::: zone pivot="programming-language-python"
Módosíthatja `scriptFile` egy másik Python-fájl meghívásához, ha szükséges.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Minden kötéshez irány, típus és egyedi név szükséges. A HTTP-eseményindító típus- [`httpTrigger`](functions-bindings-http-webhook-trigger.md) és kimeneti [`http`](functions-bindings-http-webhook-output.md)kötésének bemeneti kötése típusú.
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Támogató Azure-erőforrások létrehozása a funkcióhoz

A függvénykód Azure-ba való üzembe helyezése előtt három erőforrást kell létrehoznia:

- Erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
- Egy tárfiók, amely állapot- és egyéb információkat tart fenn a projektekkel kapcsolatban.
- Egy függvényalkalmazás, amely biztosítja a függvénykód végrehajtásának környezetét. A függvényalkalmazás leképezi a helyi függvényprojektet, és lehetővé teszi a függvények logikai egységként történő csoportosítását az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében.

Az alábbi Azure CLI-parancsok használatával hozza létre ezeket az elemeket. Minden parancs a teljesítésután biztosítja a JSON-kimenetet.

Ha még nem tette meg, jelentkezzen be az Azure-ba az [az bejelentkezési](/cli/azure/reference-index#az-login) paranccsal:

    ```azurecli
    az login
    ```
    
Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példa létrehoz `AzureFunctionsQuickstart-rg` egy `westeurope` erőforráscsoportot a régióban. (Az erőforráscsoportot és az erőforrásokat általában egy Ön közelében `az account list-locations` lévő régióban hozza létre, a parancsból elérhető terület használatával.)

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> Nem üzemeltethet Linux- és Windows-alkalmazásokat ugyanabban az erőforráscsoportban. Ha egy Windows-függvényalkalmazással vagy webalkalmazással elnevezett `AzureFunctionsQuickstart-rg` meglévő erőforráscsoporttal rendelkezik, másik erőforráscsoportot kell használnia.
 
    
Hozzon létre egy általános célú tárfiókot az erőforráscsoportban és a régióban az [az storage fiók létrehozása](/cli/azure/storage/account#az-storage-account-create) paranccsal. A következő példában `<STORAGE_NAME>` cserélje le az Ön számára megfelelő globálisan egyedi nevet. A nevek csak 3–24 karakterből és kisbetűkből állhatnak. `Standard_LRS`általános célú fiókot határoz meg, amelyet a [Functions támogat.](storage-considerations.md#storage-account-requirements)

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

A tárfiók csak néhány cent (USD) a rövid útmutató.
    
Hozza létre a függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#az-functionapp-create) paranccsal. A következő példában `<STORAGE_NAME>` cserélje le az előző lépésben használt fiók `<APP_NAME>` nevét, és cserélje le az Ön számára megfelelő globálisan egyedi nevet. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 
::: zone-end  

::: zone pivot="programming-language-python"  
Ha Python 3.8-as `--runtime-version` t `3.8` `--functions_version` használ, váltson a-ra, és a. `3`

Ha Python 3.6-ot `--runtime-version` használ, módosítsa a ikonra. `3.6`

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Ha a Node.js 8-at `--runtime-version` `8`használja, módosítsa a -


```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-csharp"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-powershell"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Ez a parancs létrehoz egy függvényalkalmazást, amely a megadott nyelvi futtatónivalóban fut az [Azure Functions Consumption Plan](functions-scale.md#consumption-plan)alatt, amely ingyenes az itt felmerülő használat mennyisége esetén. A parancs is rendelkezik egy társított Azure Application Insights-példány ugyanabban az erőforráscsoportban, amellyel figyelheti a függvényalkalmazást, és megtekintheti a naplókat. További információ: [Monitor Azure Functions](functions-monitoring.md). A példány nem jár költségekkel, amíg nem aktiválja.
    
## <a name="deploy-the-function-project-to-azure"></a>A függvényprojekt üzembe helyezése az Azure-ban
::: zone-end  

::: zone pivot="programming-language-typescript"  
Mielőtt a Core Tools segítségével telepítené a projektet az Azure-ba, hozzon létre egy éles használatra kész JavaScript-fájlokat a TypeScript forrásfájlokból.

A következő parancs előkészíti a TypeScript-projektet a telepítésre:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
A szükséges erőforrásokkal a helyén, most már készen áll a helyi függvények projekt üzembe helyezésére a függvényalkalmazás az Azure-ban a [func azure functionapp közzétételi](functions-run-local.md#project-file-deployment) parancs használatával. A következő példában `<APP_NAME>` cserélje le az alkalmazás nevét.

```
func azure functionapp publish <APP_NAME>
```

Ha a "Nem található alkalmazás neve ..." hibaüzenet jelenik meg, várjon néhány másodpercet, majd próbálkozzon `az functionapp create` újra, mert előfordulhat, hogy az Azure nem inicializálta teljesen az alkalmazást az előző parancs után.

A közzétételi parancs a következő kimenethez hasonló eredményeket jelenít meg (az egyszerűség kedvéért csonkolva):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
</pre>

::: zone-end  
::: zone pivot="programming-language-java"  
## <a name="deploy-the-function-project-to-azure"></a>A függvényprojekt üzembe helyezése az Azure-ban

Egy függvényalkalmazás és a kapcsolódó erőforrások jönnek létre az Azure-ban, amikor először telepíti a függvények projekt. Az alkalmazás üzemeltetéséhez létrehozott Azure-erőforrások beállításai a [pom.xml fájlban](#pomxml)vannak definiálva. Ebben a cikkben elfogadja az alapértelmezett értékeket.

> [!TIP]
> Ha A pom.xml fájlban a Windows `runtime.os` helyett Linuxon futó `windows` függvényalkalmazást szeretne létrehozni, módosítsa a pom.xml fájl elemét a rendszerre. `linux` Ezekben a [régiókban](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)a Linux futtatása a használati tervben támogatott. Nem lehet linuxos és windowsos alkalmazások at ugyanabban az erőforráscsoportban.

Üzembe helyezés előtt az [Azure](/cli/azure/authenticate-azure-cli) CLI-vel jelentkezzen be az Azure-előfizetésbe. 

```azurecli
az login
```

A következő paranccsal telepítheti a projektet egy új függvényalkalmazásba. 

```
mvn azure-functions:deploy
```

Ez a következő erőforrásokat hozza létre az Azure-ban:

+ Erőforráscsoport. _Java-functions-group_néven kapta a nevét.
+ Tárfiók. A függvények által megkövetelve. A név véletlenszerűen jön létre a tárfiók névkövetelményei alapján.
+ Hosting terv. Kiszolgáló nélküli üzemeltetés a függvényalkalmazáshoz a _Westus_ régióban. A név _java-functions-app-service-plan_.
+ Függvény alkalmazás. A függvényalkalmazás a függvények üzembe helyezésének és végrehajtási egységének a feladatai. A név véletlenszerűen jön létre alapján te vagy a _artifactId,_ hozzáfűzve egy véletlenszerűen generált számot. 

A központi telepítés kicsomagolja a projektfájlokat, és telepíti őket az új függvényalkalmazásba [a zip központi telepítés](functions-deployment-technologies.md#zip-deploy)használatával. A kód az Azure-beli központi telepítési csomagból fut.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>A függvény meghívása az Azure-ban

Mivel a függvény HTTP-eseményindítót használ, azzal hívja meg, hogy http-kérést küld az URL-címére a böngészőben vagy egy olyan eszközzel, mint a curl. Mindkét esetben az `code` URL-paraméter az egyedi [függvénykulcs,](functions-bindings-http-webhook-trigger.md#authorization-keys) amely engedélyezi a függvényvégpont meghívását.

# <a name="browser"></a>[Böngésző](#tab/browser)

Másolja a közzétételi parancs kimenetében látható teljes **Invoke URL-címet** a `&name=Functions`böngésző címsorába, hozzáfűzve a lekérdezési paramétert . A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor a funkciót helyileg futtatta.

![A függvény kimenete az Azure-on fut egy böngészőben](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Curl](#tab/curl)

Futtassa [`curl`](https://curl.haxx.se/) az **Invoke URL-címmel,** hozzáfűzve a paramétert. `&name=Functions` A parancs kimenetének a "Hello Functions" szövegnek kell lennie.

![A függvény kimenete az Azure-on fut a curl használatával](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Egy közzétett függvényalkalmazás közel valós idejű naplóinak megtekintéséhez használja az [Application Insights Live Metrics Stream .To](functions-monitoring.md#streaming-logs)view near real-time logs for a published function app, use the Application Insights Live Metrics Stream .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatja a következő lépést, [adja hozzá az Azure Storage-várólista kimeneti kötését,](functions-add-output-binding-storage-queue-cli.md)tartsa az összes erőforrást a helyén, ahogy épít, amit már megtett.

Ellenkező esetben a következő paranccsal törölje az erőforráscsoportot és az összes tartalmazott erőforrást a további költségek elkerülése érdekében.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-várólistához](functions-add-output-binding-storage-queue-cli.md)
