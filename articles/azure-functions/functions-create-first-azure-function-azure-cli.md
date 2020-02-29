---
title: Olyan függvény létrehozása az Azure-ban, amely válaszol a HTTP-kérelmekre
description: Ismerje meg, hogyan hozhat létre függvényt a parancssorból, majd hogyan teheti közzé a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetéséhez.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c8648a0bdfaeeb8a89ea24b0f49610e5bf5c0491
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190852"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Gyors útmutató: olyan függvény létrehozása az Azure-ban, amely válaszol a HTTP-kérelmekre

Ebben a cikkben parancssori eszközöket használ a HTTP-kérelmekre válaszoló függvények létrehozásához. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe. A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

A cikk [Visual Studio Code-alapú verziója](functions-create-first-function-vs-code.md) is létezik.

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ A [Azure functions Core Tools](./functions-run-local.md#v2) verziója 2.7.1846 vagy újabb 2. x verzió.

+ Az [Azure CLI](/cli/azure/install-azure-cli) verziója 2.0.76 vagy újabb. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node. js](https://nodejs.org/), aktív LTS és karbantartási LTS-verziók (8.11.1 és 10.14.1 ajánlott).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,7](https://www.python.org/downloads/release/python-375/) vagy [Python 3,6](https://www.python.org/downloads/release/python-368/), amelyet a Azure functions támogat. A Python 3,8-es és újabb verziói még nem támogatottak. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell-mag](/powershell/scripting/install/installing-powershell-core-on-windows)

+ A [.net Core SDK 2.2 +](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>A környezet ellenõrzése

+ A terminál vagy a parancssorablakban futtassa `func --version` parancsot, hogy meggyőződjön arról, hogy a Azure Functions Core Tools verziója 2.7.1846 vagy újabb 2. x verziójú.

+ `az --version` futtatásával győződjön meg arról, hogy az Azure CLI verziója 2.0.76 vagy újabb.

+ `az login` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Futtassa `node --version` a Node. js-verziók 8. x vagy 10. x verziójának vizsgálatához.
::: zone-end
::: zone pivot="programming-language-python"
+ Futtassa `python --version` (Linux/MacOS) vagy `py --version` (Windows) rendszert a Python-verzió jelentéseinek 3.7. x vagy 3.6. x használatával való vizsgálatához.

## <a name="create-venv"></a>Virtuális környezet létrehozása és aktiválása

Egy megfelelő mappában futtassa a következő parancsokat egy `.venv`nevű virtuális környezet létrehozásához és aktiválásához. Ügyeljen arra, hogy a Azure Functions által támogatott Python 3,7-vagy 3,6-et használja.


# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Ha a Python nem telepítette a venv csomagot a Linux-disztribúcióban, futtassa a következő parancsot:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

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

Az összes további parancsot futtatja ebben az aktivált virtuális környezetben. (A virtuális környezetből való kilépéshez futtassa `deactivate`.)

::: zone-end

## <a name="create-a-local-function-project"></a>Helyi függvény projekt létrehozása

Azure Functions egy függvény-projekt egy vagy több olyan egyedi függvény tárolója, amely mindegyik reagál egy adott triggerre. Egy projekt összes funkciója ugyanazokat a helyi és üzemeltetési konfigurációkat használja. Ebben a szakaszban egy függvény-projektet hoz létre, amely egyetlen függvényt tartalmaz.

1. A virtuális környezetben futtassa a `func init` parancsot egy functions-projekt létrehozásához egy *LocalFunctionProj* nevű mappában a megadott futtatókörnyezettel:

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


    Ez a mappa a projekt különböző fájljait tartalmazza, beleértve a Configuration Files [Local. Settings. JSON](functions-run-local.md#local-settings-file) és a [Host. JSON](functions-host-json.md)nevű fájlokat. Mivel a *Local. Settings. JSON* az Azure-ból letöltött titkos kulcsokat tartalmazhat, a fájl a *. gitignore* fájlban alapértelmezés szerint ki van zárva a forrás-vezérlőelemből.

1. Navigáljon a projekt mappájába:

    ```
    cd LocalFunctionProj
    ```
    
1. Adjon hozzá egy függvényt a projekthez a következő parancs használatával, ahol a `--name` argumentum a függvény egyedi neve, a `--template` argumentum pedig megadja a függvény triggerét. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new` létrehoz egy HttpExample.cs.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new` létrehoz egy almappát, amely megfelel a projekt választott nyelvének és a *function. JSON*nevű konfigurációs fájlnak, amely tartalmazza a függvény nevét.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>Választható A fájl tartalmának vizsgálata

Ha szeretné, kihagyhatja [a függvény helyi futtatását](#run-the-function-locally) , és később is megvizsgálhatja a fájl tartalmát.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

A *HttpExample.cs* olyan `Run` metódust tartalmaz, amely a `req` változóban fogadja a kérelmeket, amely a **HttpTriggerAttribute**díszített [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) , amely meghatározza az trigger viselkedését. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

A Return objektum egy [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) , amely egy válaszüzenetet ad vissza [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) vagy [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.

*\_\_init\_\_. a. `main()` a. a...* *..*

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

HTTP-trigger esetén a függvény a *function. JSON*fájlban meghatározott `req` változóban fogadja a kérelmeket. `req` az [Azure. functions. HttpRequest osztály](/python/api/azure-functions/azure.functions.httprequest)egy példánya. A *function. JSON*fájlban `$return`ként definiált visszatérési objektum az [Azure. functions. HttpResponse osztály](/python/api/azure-functions/azure.functions.httpresponse)egy példánya. További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index. js

az *index. js* olyan függvényt exportál, amely a *function. JSON*fájlban megadott konfiguráció alapján aktiválódik.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

HTTP-trigger esetén a függvény a *function. JSON*fájlban meghatározott `req` változóban fogadja a kérelmeket. A *függvény. JSON*fájlban `$return`ként definiált visszatérési objektum a válasz. További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index. TS

az *index. TS* olyan függvényt exportál, amely a *function. JSON*fájlban megadott konfiguráció alapján aktiválódik.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

HTTP-trigger esetén a függvény a *function. JSON*fájlban meghatározott **HttpRequest** típusú `req` változóban fogadja a kérelmeket. A *függvény. JSON*fájlban `$return`ként definiált visszatérési objektum a válasz. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>a. ps1 futtatása

a *Run. ps1* definiál egy, a *function. JSON*fájlban megadott konfigurációnak megfelelő függvény-parancsfájlt.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

HTTP-trigger esetén a függvény fogadja a *function. JSON*fájlban definiált `$Request` paraméternek átadott kérelmeket. A *function. JSON*fájlban `Response`ként definiált visszatérési objektum válaszként a `Push-OutputBinding` parancsmagnak lesz átadva. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

a *function. JSON* egy olyan konfigurációs fájl, amely meghatározza a függvény bemeneti és kimeneti `bindings`ét, beleértve az trigger típusát is. 
::: zone-end

::: zone pivot="programming-language-python"
Ha kívánja, megváltoztathatja a `scriptFile`t, ha szükséges, más Python-fájlt is meghívhat.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Minden kötéshez meg kell adni egy irányt, egy típust és egy egyedi nevet. A HTTP-trigger [`httpTrigger`](functions-bindings-http-webhook-trigger.md) típusú, [`http`](functions-bindings-http-webhook-output.md)típusú kimeneti kötést tartalmaz.
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>A függvényhez kapcsolódó Azure-erőforrások létrehozása

Mielőtt üzembe helyezi a függvény kódját az Azure-ban, három erőforrást kell létrehoznia:

- Egy erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
- Egy Storage-fiók, amely fenntartja az állapotot és a projektekkel kapcsolatos egyéb információkat.
- Egy Function alkalmazás, amely biztosítja a környezetet a függvény kódjának végrehajtásához. A Function alkalmazás a helyi function projekthez kapcsolódik, és lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.

Ezen elemek létrehozásához használja az alábbi Azure CLI-parancsokat. Az egyes parancsok a JSON-kimenetet biztosítják a befejezés után.

1. Ha még nem tette meg, jelentkezzen be az Azure-ba az az [login](/cli/azure/reference-index#az-login) paranccsal:

    ```azurecli
    az login
    ```
    
1. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példában létrehozunk egy `AzureFunctionsQuickstart-rg` nevű erőforráscsoportot a `westeurope` régióban. (Az erőforráscsoport és az erőforrások általában az Ön közelében lévő régióban hozhatók létre a `az account list-locations` parancsból elérhető régió használatával.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > Nem futtathat Linux-és Windows-alkalmazásokat ugyanabban az erőforráscsoporthoz. Ha van egy `AzureFunctionsQuickstart-rg` nevű meglévő erőforráscsoport egy Windows-függvény alkalmazással vagy webalkalmazással, egy másik erőforráscsoportot kell használnia.
    ::: zone-end  
    
1. Hozzon létre egy általános célú Storage-fiókot az erőforráscsoport és a régió területén az az [Storage Account Create](/cli/azure/storage/account#az-storage-account-create) paranccsal. Az alábbi példában cserélje le a `<STORAGE_NAME>`t a megfelelő globálisan egyedi névre. A névnek három – 24 karakterből kell állnia, és csak kisbetűket tartalmazhat. `Standard_LRS` a [függvények által támogatott](storage-considerations.md#storage-account-requirements)általános célú fiókot határozza meg.

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    A Storage-fiók ebben a rövid útmutatóban csak néhány cent (USD) értékkel rendelkezik.
    
1. Hozza létre a functions alkalmazást az az [functionapp Create](/cli/azure/functionapp#az-functionapp-create) parancs használatával. Az alábbi példában cserélje le a `<STORAGE_NAME>` az előző lépésben használt fiók nevére, és cserélje le a `<APP_NAME>`t az Önnek megfelelő globálisan egyedi névre. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 

    ::: zone pivot="programming-language-python"  
    Ha a Python 3,6-et használja, akkor a `--runtime-version`t is módosítsa `3.6`re.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Ha a Node. js 8-at használja, akkor a `--runtime-version`t is módosítsa `8`re.

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    Ez a parancs létrehoz egy, a megadott nyelvi futtatókörnyezetben futó Function alkalmazást a [Azure functions használati terv](functions-scale.md#consumption-plan)alatt, amely ingyenesen használható az itt felmerülő felhasználási mennyiséghez. A parancs egy kapcsolódó Azure Application Insights-példányt is kiépít ugyanabban az erőforráscsoporthoz, amellyel nyomon követheti a Function alkalmazást, és megtekintheti a naplókat. További információ: [Azure functions figyelése](functions-monitoring.md). A példány nem jár költséggel, amíg be nem aktiválja.
    
## <a name="deploy-the-function-project-to-azure"></a>A Function projekt üzembe helyezése az Azure-ban

::: zone pivot="programming-language-typescript"  
Mielőtt a Core Tools használatával üzembe helyezi a projektet az Azure-ban, létrehoz egy éles környezetben felépíthető JavaScript-fájlokat a géppel előállított forrásfájlok közül.

A következő parancs előkészíti a géppel készített projektet az üzembe helyezéshez:

```
npm run build:production 
```
::: zone-end  

A szükséges erőforrásokkal most már készen áll a helyi functions-projekt üzembe helyezésére az Azure-beli Function alkalmazásban az functions [Azure functionapp publish](functions-run-local.md#project-file-deployment) parancs használatával. Az alábbi példában cserélje le az `<APP_NAME>` az alkalmazás nevére.

```
func azure functionapp publish <APP_NAME>
```

Ha a következő hibaüzenet jelenik meg: "nem található az alkalmazás neve...", várjon néhány másodpercet, és próbálkozzon újra, mivel az Azure nem tudja teljesen inicializálni az alkalmazást az előző `az functionapp create` parancs után.

A közzétételi parancs a következő kimenethez hasonló eredményeket jelenít meg (egyszerűség kedvéért csonkítva):

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

Mivel a függvény HTTP-eseményindítót használ, meghívja azt úgy, hogy HTTP-kérést küld az URL-címére a böngészőben, vagy egy olyan eszközzel, mint például a curl. Mindkét példányban a `code` URL-cím paraméter az egyedi [funkcióbillentyű](functions-bindings-http-webhook-trigger.md#authorization-keys) , amely engedélyezi a függvény végpontjának meghívását.

# <a name="browser"></a>[Böngésző](#tab/browser)

Másolja a publish (közzététel) parancs kimenetében megjelenő teljes **Meghívási URL-** címet egy böngésző címsorába, és illessze be a lekérdezési paramétert `&name=Functions`. A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor a funkciót helyileg futtatta.

![A függvény kimenete az Azure-ban egy böngészőben fut](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Curl](#tab/curl)

Futtassa [`curl`](https://curl.haxx.se/) a **MEGhívási URL-címmel**, és illessze be a paramétert `&name=Functions`. A parancs kimenetének a "Hello functions" szövegnek kell lennie.

![A függvény kimenete az Azure-on a curl használatával fut](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> A közzétett functions-alkalmazások közel valós idejű naplófájljainak megtekintéséhez használja a [Application Insights élő metrikastream](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatja a következő lépéssel, [vegyen fel egy Azure Storage-üzenetsor kimeneti kötését](functions-add-output-binding-storage-queue-python.md), tartsa meg az összes erőforrását, mivel a már elkészült dolgokra épít.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és az összes benne lévő erőforrást, hogy elkerülje a további költségek felmerülését.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-cli.md)
