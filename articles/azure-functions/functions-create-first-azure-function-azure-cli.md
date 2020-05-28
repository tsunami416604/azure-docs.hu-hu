---
title: Olyan függvény létrehozása az Azure-ban, amely válaszol a HTTP-kérelmekre
description: Ismerje meg, hogyan hozhat létre függvényt a parancssorból, majd hogyan teheti közzé a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetéséhez.
ms.date: 03/30/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: bfd956a4423031db370eb3a8ad94c59dd0f5931c
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996524"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Gyors útmutató: olyan függvény létrehozása az Azure-ban, amely válaszol a HTTP-kérelmekre

::: zone pivot="programming-language-csharp"  
Ebben a cikkben parancssori eszközökkel hozhat létre egy C# szintű függvénytár-alapú függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe. 
::: zone-end  
::: zone pivot="programming-language-javascript"
Ebben a cikkben parancssori eszközöket használ a HTTP-kérelmekre válaszoló JavaScript-függvények létrehozásához. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe. 
::: zone-end
::: zone pivot="programming-language-typescript"
Ebben a cikkben parancssori eszközöket használ a HTTP-kérelmekre válaszoló írógéppel-függvények létrehozásához. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe. 
::: zone-end   
::: zone pivot="programming-language-powershell"
Ebben a cikkben parancssori eszközöket használ egy olyan PowerShell-függvény létrehozásához, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe. 
::: zone-end  
::: zone pivot="programming-language-python" 
Ebben a cikkben parancssori eszközöket használ egy olyan Python-függvény létrehozásához, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe. 
::: zone-end  
::: zone pivot="programming-language-java" 
Ebben a cikkben parancssori eszközöket használ egy olyan Java-függvény létrehozásához, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe. 
::: zone-end

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
A cikk [Visual Studio Code-alapú verziója](functions-create-first-function-vs-code.md) is létezik.
::: zone-end  
::: zone pivot="programming-language-java"  
> [!NOTE]
> Ha a Maven nem az Ön által előnyben részesített fejlesztői eszköz, tekintse meg a Java-fejlesztőknek szóló hasonló oktatóanyagokat a [Gradle](/azure/azure-functions/functions-create-first-java-gradle), a [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) és a [Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java)használatával.
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Helyi függvény projekt létrehozása

Azure Functions egy függvény-projekt egy vagy több olyan egyedi függvény tárolója, amely mindegyik reagál egy adott triggerre. Egy projekt összes funkciója ugyanazokat a helyi és üzemeltetési konfigurációkat használja. Ebben a szakaszban egy függvény-projektet hoz létre, amely egyetlen függvényt tartalmaz.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Futtassa a `func init` parancsot az alábbiak szerint, hogy hozzon létre egy functions-projektet egy *LocalFunctionProj* nevű mappában a megadott futtatókörnyezettel:  
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

# <a name="bash"></a>[bash](#tab/bash)
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

A Maven megkéri, hogy a projektnek a telepítéskor való létrehozásának befejezéséhez szükséges értékeket is megkeresse.   
Ha a rendszer kéri, adja meg a következő értékeket:

| Kérdés | Érték | Leírás |
| ------ | ----- | ----------- |
| **csoportazonosító** | `com.fabrikam` | Egy érték, amely egyedileg azonosítja a projektet az összes projektben, a Java [csomag elnevezési szabályait](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) követve. |
| **artifactId** | `fabrikam-functions` | Egy érték, amely a jar neve, verziószám nélkül. |
| **verziója** | `1.0-SNAPSHOT` | Válassza ki az alapértelmezett értéket. |
| **csomag** | `com.fabrikam` | Egy érték, amely a generált függvény kódjához tartozó Java-csomag. Használja az alapértelmezettet. |

`Y`A megerősítéshez írja be vagy nyomja le az ENTER billentyűt.

A Maven létrehoz egy új, _artifactId_nevű mappában található projektfájlt, amely ebben a példában a `fabrikam-functions` . 
::: zone-end  
Navigáljon a projekt mappájába:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionProj
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
Ez a mappa a projekt különböző fájljait tartalmazza, beleértve a Configuration Files [Local. Settings. JSON](functions-run-local.md#local-settings-file) és a [Host. JSON](functions-host-json.md)nevű fájlokat. Mivel a *Local. Settings. JSON* az Azure-ból letöltött titkos kulcsokat tartalmazhat, a fájl a *. gitignore* fájlban alapértelmezés szerint ki van zárva a forrás-vezérlőelemből.

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>Választható A fájl tartalmának vizsgálata

Ha szeretné, kihagyhatja [a függvény helyi futtatását](#run-the-function-locally) , és később is megvizsgálhatja a fájl tartalmát.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

A *HttpExample.cs* olyan `Run` metódust tartalmaz, amely a változóban fogadja a `req` [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) , amely a **HttpTriggerAttribute**díszített, amely meghatározza az trigger viselkedését. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

A Return objektum egy [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) , amely egy válaszüzenetet ad vissza [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) vagy [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Function. Java
*Function. Java* olyan `run` metódust tartalmaz, amely fogadja a kérelmeket a `request` változóban a [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) jegyzettel díszített [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) , amely meghatározza az trigger viselkedését. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

A válaszüzenetet a [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API hozza létre.

#### <a name="pomxml"></a>Pom. XML

Az alkalmazás üzemeltetéséhez létrehozott Azure-erőforrások beállításai a beépülő modul **konfigurációs** elemében vannak meghatározva a **groupId** `com.microsoft.azure` generált Pom. xml fájl GroupID. Az alábbi konfigurációs elem például arra utasítja a Maven-alapú telepítést, hogy hozzon létre egy Function alkalmazást a `java-functions-group` régióban található erőforráscsoporthoz `westus` . A Function alkalmazás maga fut a csomagban lévő Windows rendszeren `java-functions-app-service-plan` , amely alapértelmezés szerint kiszolgáló nélküli fogyasztási csomag.    

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-102":::

Ezen beállítások módosításával szabályozhatja, hogy az erőforrások hogyan jöjjenek létre az Azure-ban, például a verzióról a verzióra való váltással a `runtime.os` `windows` `linux` kezdeti üzembe helyezés előtt. A Maven beépülő modul által támogatott beállítások teljes listájáért tekintse meg a [konfiguráció részleteit](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunctionTest. Java

Az archetípus is létrehoz egy egység tesztet a függvényhez. Ha módosítja a függvényt kötések hozzáadására vagy új függvények hozzáadására a projekthez, a *FunctionTest. Java* fájlban is módosítania kell a teszteket.
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init \_ \_ . a

az * \_ \_ init \_ \_ .* a a `main()` *function. JSON*fájlban megadott konfiguráció alapján aktivált Python-függvényt tartalmaz.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

HTTP-trigger esetén a függvény a `req` *function. JSON*fájlban meghatározott változóban fogadja a kérelmeket. `req`az [Azure. functions. HttpRequest osztály](/python/api/azure-functions/azure.functions.httprequest)egy példánya. A `$return` *function. JSON*fájlban megadott visszatérési objektum az [Azure. functions. HttpResponse osztály](/python/api/azure-functions/azure.functions.httpresponse)egy példánya. További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index. js

az *index. js* olyan függvényt exportál, amely a *function. JSON*fájlban megadott konfiguráció alapján aktiválódik.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

HTTP-trigger esetén a függvény a `req` *function. JSON*fájlban meghatározott változóban fogadja a kérelmeket. A `$return` Válasz a *function. JSON*fájlban megadott visszatérési objektum. További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index. TS

az *index. TS* olyan függvényt exportál, amely a *function. JSON*fájlban megadott konfiguráció alapján aktiválódik.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

HTTP-trigger esetén a függvény a `req` *function. JSON*fájlban meghatározott **HttpRequest** típusú változóban fogadja a kérelmeket. A `$return` Válasz a *function. JSON*fájlban megadott visszatérési objektum. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>a. ps1 futtatása

a *Run. ps1* definiál egy, a *function. JSON*fájlban megadott konfigurációnak megfelelő függvény-parancsfájlt.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

HTTP-trigger esetén a függvény fogadja a `$Request` *function. JSON*fájlban meghatározott paraméternek átadott kérelmeket. A `Response` válaszként a parancsmagnak a *function. JSON*fájlban megadott visszatérési objektumot adja át a rendszer `Push-OutputBinding` . 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

a *function. JSON* egy olyan konfigurációs fájl, amely meghatározza a függvény bemenetét és kimenetét `bindings` , beleértve az trigger típusát is. 
::: zone-end

::: zone pivot="programming-language-python"
Ha kívánja `scriptFile` , másik Python-fájlt is meghívhat.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Minden kötéshez meg kell adni egy irányt, egy típust és egy egyedi nevet. A HTTP-trigger típusa [`httpTrigger`](functions-bindings-http-webhook-trigger.md) és kimeneti kötése típusú bemeneti kötést tartalmaz [`http`](functions-bindings-http-webhook-output.md) .
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>A függvényhez kapcsolódó Azure-erőforrások létrehozása

Mielőtt üzembe helyezi a függvény kódját az Azure-ban, három erőforrást kell létrehoznia:

- Egy erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
- Egy Storage-fiók, amely fenntartja az állapotot és a projektekkel kapcsolatos egyéb információkat.
- Egy Function alkalmazás, amely biztosítja a környezetet a függvény kódjának végrehajtásához. A Function alkalmazás a helyi function projekthez kapcsolódik, és lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.

Ezen elemek létrehozásához használja az alábbi Azure CLI-parancsokat. Az egyes parancsok a JSON-kimenetet biztosítják a befejezés után.

Ha még nem tette meg, jelentkezzen be az Azure-ba az az [login](/cli/azure/reference-index#az-login) paranccsal:

```azurecli
az login
```
    
Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példában létrehozunk egy nevű erőforráscsoportot `AzureFunctionsQuickstart-rg` a `westeurope` régióban. (Az erőforráscsoport és az erőforrások általában az Ön közelében lévő régióban hozhatók létre a parancsból elérhető régió használatával `az account list-locations` .)

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> Nem futtathat Linux-és Windows-alkalmazásokat ugyanabban az erőforráscsoporthoz. Ha már van egy nevű erőforráscsoport `AzureFunctionsQuickstart-rg` egy Windows-függvény alkalmazással vagy webalkalmazással, egy másik erőforráscsoportot kell használnia.
 
    
Hozzon létre egy általános célú Storage-fiókot az erőforráscsoport és a régió területén az az [Storage Account Create](/cli/azure/storage/account#az-storage-account-create) paranccsal. A következő példában cserélje le `<STORAGE_NAME>` egy globálisan egyedi névre, amely az Ön számára megfelelő. A névnek három – 24 karakterből kell állnia, és csak kisbetűket tartalmazhat. `Standard_LRS`a [függvények által támogatott](storage-considerations.md#storage-account-requirements)általános célú fiókot határozza meg.

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

A Storage-fiók ebben a rövid útmutatóban csak néhány cent (USD) értékkel rendelkezik.
    
Hozza létre a Function alkalmazást az az [functionapp Create](/cli/azure/functionapp#az-functionapp-create) parancs használatával. Az alábbi példában cserélje le az `<STORAGE_NAME>` t az előző lépésben használt fiók nevére, és cserélje le az értékét a `<APP_NAME>` megfelelő globálisan egyedi névre. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 
::: zone-end  

::: zone pivot="programming-language-python"  
Ha a Python 3,8-et használja, váltson `--runtime-version` a `3.8` és a rendszerre `--functions_version` `3` .

Ha a Python 3,6-et használja, váltson a következőre: `--runtime-version` `3.6` .

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Ha a Node. js 8-at használja, a következőre is váltson: `--runtime-version` `8` .


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
Ez a parancs létrehoz egy, a megadott nyelvi futtatókörnyezetben futó Function alkalmazást a [Azure functions használati terv](functions-scale.md#consumption-plan)alatt, amely ingyenesen használható az itt felmerülő felhasználási mennyiséghez. A parancs egy kapcsolódó Azure Application Insights-példányt is kiépít ugyanabban az erőforráscsoporthoz, amellyel nyomon követheti a Function alkalmazást, és megtekintheti a naplókat. További információ: [Azure functions figyelése](functions-monitoring.md). A példány nem jár költséggel, amíg be nem aktiválja.
    
## <a name="deploy-the-function-project-to-azure"></a>A Function projekt üzembe helyezése az Azure-ban
::: zone-end  

::: zone pivot="programming-language-typescript"  
Mielőtt a Core Tools használatával üzembe helyezi a projektet az Azure-ban, létrehoz egy éles környezetben felépíthető JavaScript-fájlokat a géppel előállított forrásfájlok közül.

A következő parancs előkészíti a géppel készített projektet az üzembe helyezéshez:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
A szükséges erőforrásokkal most már készen áll a helyi functions-projekt üzembe helyezésére az Azure-beli Function alkalmazásban az functions [Azure functionapp publish](functions-run-local.md#project-file-deployment) parancs használatával. A következő példában cserélje le az `<APP_NAME>` alkalmazást az alkalmazás nevére.

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

::: zone-end  
::: zone pivot="programming-language-java"  
## <a name="deploy-the-function-project-to-azure"></a>A Function projekt üzembe helyezése az Azure-ban

A functions-alkalmazás és a kapcsolódó erőforrások az Azure-ban jönnek létre, amikor először telepíti a functions-projektet. Az alkalmazás üzemeltetéséhez létrehozott Azure-erőforrások beállításai a [Pom. xml fájlban](#pomxml)vannak meghatározva. Ebben a cikkben fogadja el az alapértelmezett értékeket.

> [!TIP]
> Ha a Windows helyett Linux rendszeren futó Function alkalmazást szeretne létrehozni, módosítsa a `runtime.os` Pom. xml fájl elemét a verzióról a következőre: `windows` `linux` . [Ezekben a régiókban](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)támogatott a Linux futtatása a használati tervekben. Nem rendelkezhet olyan alkalmazásokkal, amelyek Linux rendszeren futnak, és ugyanazon az erőforráscsoporthoz futtatják a Windowson futó alkalmazásokat.

Az üzembe helyezés előtt az az [login](/cli/azure/authenticate-azure-cli) Azure CLI-paranccsal jelentkezzen be az Azure-előfizetésbe. 

```azurecli
az login
```

A következő parancs használatával telepítheti a projektet egy új Function alkalmazásba. 

```
mvn azure-functions:deploy
```

Ez a következő erőforrásokat hozza létre az Azure-ban:

+ Erőforráscsoport. Neve: _Java-functions-Group_.
+ Storage-fiók. A függvények igénylik. A név véletlenszerűen jön létre a Storage-fióknév követelményei alapján.
+ Üzemeltetési csomag. A _westus_ régióban lévő Function App kiszolgáló nélküli üzemeltetése. A név a _Java-functions-app-Service-Plan_.
+ Function alkalmazás. A functions alkalmazás a függvények üzembe helyezési és végrehajtási egysége. A név véletlenszerűen jön létre a _artifactId_alapján, véletlenszerűen generált számmal hozzáfűzve. 

A központi telepítés a Project fájljait csomagolja és telepíti az új Function alkalmazásba a [zip-telepítés](functions-deployment-technologies.md#zip-deploy)használatával. A kód a központi telepítési csomagból fut az Azure-ban.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>A függvény meghívása az Azure-ban

Mivel a függvény HTTP-eseményindítót használ, meghívja azt úgy, hogy HTTP-kérést küld az URL-címére a böngészőben, vagy egy olyan eszközzel, mint például a curl. Mindkét példányban az `code` URL paraméter az egyedi [funkcióbillentyű](functions-bindings-http-webhook-trigger.md#authorization-keys) , amely engedélyezi a függvény végpontjának meghívását.

# <a name="browser"></a>[Böngésző](#tab/browser)

Másolja a publish (közzététel) parancs kimenetében megjelenő teljes **Meghívási URL-** címet egy böngésző címsorába, és illessze be a lekérdezési paramétert `&name=Functions` . A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor a funkciót helyileg futtatta.

![A függvény kimenete az Azure-ban egy böngészőben fut](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Curl](#tab/curl)

Futtassa a parancsot [`curl`](https://curl.haxx.se/) a **Meghívási URL-címmel**, és illessze be a paramétert `&name=Functions` . A parancs kimenetének a "Hello functions" szövegnek kell lennie.

![A függvény kimenete az Azure-on a curl használatával fut](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> A közzétett functions-alkalmazások közel valós idejű naplófájljainak megtekintéséhez használja a [Application Insights élő metrikastream](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha folytatja a következő lépéssel, [vegyen fel egy Azure Storage-üzenetsor kimeneti kötését](functions-add-output-binding-storage-queue-cli.md), tartsa meg az összes erőforrását, mivel a már elkészült dolgokra épít.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és az összes benne lévő erőforrást, hogy elkerülje a további költségek felmerülését.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end
::: zone pivot="programming-language-java"
```azurecli
az group delete --name java-functions-group
```
::: zone-end

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-cli.md)
