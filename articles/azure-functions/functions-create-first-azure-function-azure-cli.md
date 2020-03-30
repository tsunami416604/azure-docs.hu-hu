---
title: Hozzon létre egy függvényt az Azure-ban, amely válaszol a HTTP-kérésekre
description: Ismerje meg, hogyan hozhat létre egy függvényt a parancssorból, majd tegye közzé a helyi projektet kiszolgáló nélküli üzemeltetésre az Azure Functionsben.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 89b6a9f31414cbaa9cc92c1a0d881a1354180990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282732"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Rövid útmutató: Hozzon létre egy függvényt az Azure-ban, amely válaszol a HTTP-kérésekre

Ebben a cikkben parancssori eszközökkel hozhat létre olyan függvényt, amely válaszol a HTTP-kérésekre. Miután helyileg tesztelte a kódot, üzembe helyezi azt az Azure Functions kiszolgáló nélküli környezetében. A rövid útmutató végrehajtása néhány USD centvagy annál kevesebb költséget jelent az Azure-fiókjában.

A cikknek van egy [Visual Studio kódalapú változata](functions-create-first-function-vs-code.md) is.

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt elkezdené, rendelkeznie kell a következőkkel:

+ Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ Az [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846-os vagy egy későbbi 2.x-es verziója.
::: zone-end  
::: zone pivot="programming-language-python"
+ A Python 3.6-os és 3.7-es verziójához az [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846-os vagy egy későbbi 2.x-es verziója szükséges. A Python 3.8 használatához a Core Tools [3.x verziója](./functions-run-local.md#v2) szükséges.
::: zone-end

+ Az [Azure CLI](/cli/azure/install-azure-cli) 2.0.76-os vagy újabb verziója. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Aktív LTS és karbantartási LTS verziók (8.11.1 és 10.14.1 ajánlott).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-382/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/), amelyek et az Azure Functions (x64) támogat.
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ A [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>A környezet ellenőrzése

+ Egy terminál- vagy parancsablakban futtassa `func --version` annak ellenőrzéséhez, hogy az Azure Functions Core Tools 2.7.1846-os vagy egy későbbi 2.x-es verziót tartalmaz-e.

+ Futtassa `az --version` annak ellenőrzéséhez, hogy az Azure CLI-verzió 2.0.76-os vagy újabb.

+ Futtassa `az login` a bejelentkezést az Azure-ba, és ellenőrizze az aktív előfizetést.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Futtassa `node --version` a Node.js 8.x vagy 10.x verziójelentések ellenőrzéséhez.
::: zone-end
::: zone pivot="programming-language-python"
+ Futtassa `python --version` (Linux/MacOS) vagy `py --version` (Windows) a Python 3.8.x, 3.7.x vagy 3.6.x verziójelentések ellenőrzéséhez.

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Virtuális környezet létrehozása és aktiválása

A megfelelő mappában futtassa a következő parancsokat `.venv`a nevű virtuális környezet létrehozásához és aktiválásához. Ügyeljen arra, hogy a Python 3.8, 3.7 vagy 3.6, amelyek az Azure Functions által támogatott.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Ha a Python nem telepítette a venv csomagot a Linux disztribúcióra, futtassa a következő parancsot:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Az összes további parancsot ebben az aktivált virtuális környezetben futtatja. (A virtuális környezetből `deactivate`való kilépéshez futtassa a .)

::: zone-end

## <a name="create-a-local-function-project"></a>Helyi függvényprojekt létrehozása

Az Azure Functions egy függvényprojekt egy tároló egy vagy több egyedi függvények, amelyek mindegyike reagál egy adott eseményindító. A projekt összes függvénye azonos helyi és üzemeltetési konfigurációval rendelkezik. Ebben a szakaszban olyan függvényprojektet hoz létre, amely egyetlen függvényt tartalmaz.

1. A virtuális környezetben `func init` futtassa a parancsot, hogy hozzon létre egy függvényprojektet egy *LocalFunctionProj* nevű mappában a megadott futásidővel:

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


    Ez a mappa különböző fájlokat tartalmaz a projekthez, beleértve a [local.settings.json](functions-run-local.md#local-settings-file) és [host.json](functions-host-json.md)nevű konfigurációkat. Mivel *a local.settings.json* tartalmazhat az Azure-ból letöltött titkokat, a fájl alapértelmezés szerint ki van zárva a forrásvezérlésből a *.gitignore* fájlban.

1. Navigálás a projekt mappájába:

    ```
    cd LocalFunctionProj
    ```
    
1. Adjon hozzá egy függvényt a projekthez `--name` a következő paranccsal, ahol `--template` az argumentum a függvény egyedi neve, és az argumentum megadja a függvény eseményindítóját. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new`HttpExample.cs kódfájlt hoz létre.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new`létrehoz egy, a függvény nevének megfelelő almappát, amely a projekt választott nyelvének megfelelő kódfájlt és *function.json*nevű konfigurációs fájlt tartalmaz.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>(Nem kötelező) A fájl tartalmának vizsgálata

Ha szükséges, ugorjon [a Funkció helyi futtatása](#run-the-function-locally) lehetőségre, és később megvizsgálhatja a fájl tartalmát.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* tartalmaz `Run` egy metódust, amely `req` megkapja a kérelem adatait a változó egy [HttpRequest,](/dotnet/api/microsoft.aspnetcore.http.httprequest) amely díszített a **HttpTriggerAttribute**, amely meghatározza az eseményindító viselkedését. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

A visszatérési objektum egy [ActionResult,](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) amely válaszüzenetet ad vissza [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) vagy [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) értékben. További információ: [Azure Functions HTTP triggerek és kötések](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
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

## <a name="create-supporting-azure-resources-for-your-function"></a>Támogató Azure-erőforrások létrehozása a funkcióhoz

A függvénykód Azure-ba való üzembe helyezése előtt három erőforrást kell létrehoznia:

- Erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
- Egy tárfiók, amely állapot- és egyéb információkat tart fenn a projektekkel kapcsolatban.
- Egy függvényalkalmazás, amely biztosítja a függvénykód végrehajtásának környezetét. A függvényalkalmazás leképezi a helyi függvényprojektet, és lehetővé teszi a függvények logikai egységként történő csoportosítását az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében.

Az alábbi Azure CLI-parancsok használatával hozza létre ezeket az elemeket. Minden parancs a teljesítésután biztosítja a JSON-kimenetet.

1. Ha még nem tette meg, jelentkezzen be az Azure-ba az [az bejelentkezési](/cli/azure/reference-index#az-login) paranccsal:

    ```azurecli
    az login
    ```
    
1. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példa létrehoz `AzureFunctionsQuickstart-rg` egy `westeurope` erőforráscsoportot a régióban. (Az erőforráscsoportot és az erőforrásokat általában egy Ön közelében `az account list-locations` lévő régióban hozza létre, a parancsból elérhető terület használatával.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > Nem üzemeltethet Linux- és Windows-alkalmazásokat ugyanabban az erőforráscsoportban. Ha egy Windows-függvényalkalmazással vagy webalkalmazással elnevezett `AzureFunctionsQuickstart-rg` meglévő erőforráscsoporttal rendelkezik, másik erőforráscsoportot kell használnia.
    ::: zone-end  
    
1. Hozzon létre egy általános célú tárfiókot az erőforráscsoportban és a régióban az [az storage fiók létrehozása](/cli/azure/storage/account#az-storage-account-create) paranccsal. A következő példában `<STORAGE_NAME>` cserélje le az Ön számára megfelelő globálisan egyedi nevet. A nevek csak 3–24 karakterből és kisbetűkből állhatnak. `Standard_LRS`általános célú fiókot határoz meg, amelyet a [Functions támogat.](storage-considerations.md#storage-account-requirements)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    A tárfiók csak néhány cent (USD) a rövid útmutató.
    
1. Hozza létre a függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#az-functionapp-create) paranccsal. A következő példában `<STORAGE_NAME>` cserélje le az előző lépésben használt fiók `<APP_NAME>` nevét, és cserélje le az Ön számára megfelelő globálisan egyedi nevet. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 

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

    Ez a parancs létrehoz egy függvényalkalmazást, amely a megadott nyelvi futtatónivalóban fut az [Azure Functions Consumption Plan](functions-scale.md#consumption-plan)alatt, amely ingyenes az itt felmerülő használat mennyisége esetén. A parancs is rendelkezik egy társított Azure Application Insights-példány ugyanabban az erőforráscsoportban, amellyel figyelheti a függvényalkalmazást, és megtekintheti a naplókat. További információ: [Monitor Azure Functions](functions-monitoring.md). A példány nem jár költségekkel, amíg nem aktiválja.
    
## <a name="deploy-the-function-project-to-azure"></a>A függvényprojekt üzembe helyezése az Azure-ban

::: zone pivot="programming-language-typescript"  
Mielőtt a Core Tools segítségével telepítené a projektet az Azure-ba, hozzon létre egy éles használatra kész JavaScript-fájlokat a TypeScript forrásfájlokból.

A következő parancs előkészíti a TypeScript-projektet a telepítésre:

```
npm run build:production 
```
::: zone-end  

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
