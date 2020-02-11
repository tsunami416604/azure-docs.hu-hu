---
title: HTTP által aktivált Python-függvény létrehozása az Azure-ban
description: Kiszolgáló nélküli Python-kód létrehozása és üzembe helyezése a felhőben Azure Functions használatával.
ms.date: 01/15/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 529a992178fae5566c8e315956388c4cd4b80257
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116217"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Gyors útmutató: HTTP által aktivált Python-függvény létrehozása az Azure-ban

Ebben a cikkben parancssori eszközöket használ egy olyan Python-függvény létrehozásához, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe. A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

A cikk [Visual Studio Code-alapú verziója](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-python) is létezik.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- A [Azure functions Core Tools](./functions-run-local.md#v2) verziója 2.7.1846 vagy újabb.
- Az [Azure CLI](/cli/azure/install-azure-cli) verziója 2.0.76 vagy újabb. 
- [Python 3.7.4 – 64 bit](https://www.python.org/downloads/release/python-374/). (A Python 3.7.4 ellenőrzése Azure Functions; A Python 3,8-es és újabb verziói még nem támogatottak.)

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

1. A terminál vagy a parancssorablakban futtassa a `func --version` parancsot annak a vizsgálatához, hogy a Azure Functions Core Tools 2.7.1846 vagy újabb verziójúak-e.
1. `az --version` futtatásával győződjön meg arról, hogy az Azure CLI verziója 2.0.76 vagy újabb.
1. `az login` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.
1. Futtasson `python --version` (Linux/MacOS) vagy `py --version` (Windows) a Python-verzió jelentéseinek 3.7. x-ben való vizsgálatához.

## <a name="create-and-activate-a-virtual-environment"></a>Hozzon létre, és aktiválja a virtuális környezet

Egy megfelelő mappában futtassa a következő parancsokat egy `.venv`nevű virtuális környezet létrehozásához és aktiválásához. Ügyeljen arra, hogy a Azure Functions által támogatott Python 3,7-et használja.


# <a name="bashtabbash"></a>[bash](#tab/bash)

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

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Az összes további parancsot futtatja ebben az aktivált virtuális környezetben. (A virtuális környezetből való kilépéshez futtassa `deactivate`.)

## <a name="create-a-local-function-project"></a>Helyi függvény projekt létrehozása

Azure Functions egy függvény-projekt egy vagy több olyan egyedi függvény tárolója, amely mindegyik reagál egy adott triggerre. Egy projekt összes funkciója ugyanazokat a helyi és üzemeltetési konfigurációkat használja. Ebben a szakaszban egy függvény-projektet hoz létre, amely egyetlen függvényt tartalmaz.

1. A virtuális környezetben futtassa a `func init` parancsot egy functions-projekt létrehozásához egy *LocalFunctionProj* nevű mappában a megadott futtatókörnyezettel:

    ```
    func init LocalFunctionProj --python
    ```
    
    Ez a mappa a projekt különböző fájljait tartalmazza, beleértve a Configuration Files [Local. Settings. JSON](functions-run-local.md#local-settings-file) és a [Host. JSON](functions-host-json.md)nevű fájlokat. Mivel a *Local. Settings. JSON* az Azure-ból letöltött titkos kulcsokat tartalmazhat, a fájl a *. gitignore* fájlban alapértelmezés szerint ki van zárva a forrás-vezérlőelemből.

    > [!TIP]
    > Mivel a Function projekt egy adott futtatókörnyezethez van kötve, a projekt összes funkcióját ugyanazzal a nyelvvel kell írni.

1. Navigáljon a projekt mappájába:

    ```
    cd LocalFunctionProj
    ```
    
1. Adjon hozzá egy függvényt a projekthez a következő parancs használatával, ahol a `--name` argumentum a függvény egyedi neve, a `--template` argumentum pedig megadja a függvény triggerét. `func new` hozzon létre egy olyan almappát, amely megfelel a projekt választott nyelvének és a *function. JSON*nevű konfigurációs fájlnak, amely tartalmazza a függvény nevét.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>Választható A fájl tartalmának vizsgálata

Ha szeretné, kihagyhatja [a függvény helyi futtatását](#run-the-function-locally) , és később is megvizsgálhatja a fájl tartalmát.

#### <a name="__init__py"></a>\_\_init\_\_.

*\_\_init\_\_. a. `main()` a. a...* *..*

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

HTTP-trigger esetén a függvény a *function. JSON*fájlban meghatározott `req` változóban fogadja a kérelmeket. `req` az [Azure. functions. HttpRequest osztály](/python/api/azure-functions/azure.functions.httprequest)egy példánya. A *function. JSON*fájlban `$return`ként definiált visszatérési objektum az [Azure. functions. HttpResponse osztály](/python/api/azure-functions/azure.functions.httpresponse)egy példánya. További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](functions-bindings-http-webhook.md).

#### <a name="functionjson"></a>function.json

a *function. JSON* egy olyan konfigurációs fájl, amely meghatározza a függvény bemeneti és kimeneti `bindings`ét, beleértve az trigger típusát is. Ha kívánja, megváltoztathatja a `scriptFile`t, ha szükséges, más Python-fájlt is meghívhat.

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
    }
  ]
}
```

Minden kötéshez meg kell adni egy irányt, egy típust és egy egyedi nevet. A HTTP-trigger [`httpTrigger`](functions-bindings-http-webhook.md#trigger) típusú, [`http`](functions-bindings-http-webhook.md#output)típusú kimeneti kötést tartalmaz.


## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Indítsa el a függvényt úgy, hogy elindítja a helyi Azure Functions Runtime-gazdagépet a *LocalFunctionProj* mappában:

```
func start
```

A következő kimenetnek kell megjelennie. (Ha a HttpExample nem jelenik meg az alább látható módon, valószínűleg elindította a gazdagépet a *HttpExample* mappából. Ebben az esetben a **Ctrl**+**C** billentyűkombinációval állítsa le a gazdagépet, navigáljon a szülő *LocalFunctionProj* mappára, majd futtassa újra `func start`.)

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

Másolja a `HttpExample` függvény URL-címét ebből a kimenetből egy böngészőbe, és fűzze hozzá a lekérdezési karakterláncot `?name=<your-name>`, így a teljes URL-címet, például `http://localhost:7071/api/HttpExample?name=Functions`. A böngészőben a következőhöz hasonló üzenetnek kell megjelennie `Hello Functions`:

![A függvény helyi futtatásának eredménye a böngészőben](./media/functions-create-first-function-python/function-test-local-browser.png)

A `func start` futtatott terminál a kérések során a naplózási kimenetet is megjeleníti.

Ha elkészült, nyomja le a **Ctrl**+**C** billentyűkombinációt a functions gazdagép leállításához.

## <a name="create-supporting-azure-resources-for-your-function"></a>A függvényhez kapcsolódó Azure-erőforrások létrehozása

Mielőtt üzembe helyezi a függvény kódját az Azure-ban, három erőforrást kell létrehoznia:

- Egy erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
- Egy Azure Storage-fiók, amely fenntartja az állapotot és a projektekkel kapcsolatos egyéb információkat.
- Egy Azure functions-alkalmazás, amely biztosítja a környezetet a függvény kódjának végrehajtásához. A Function alkalmazás a helyi function projekthez kapcsolódik, és lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.

Ezeket az elemeket az Azure CLI-parancsok használatával hozhatja létre. Az egyes parancsok a JSON-kimenetet biztosítják a befejezés után.

1. Ha még nem tette meg, jelentkezzen be az Azure-ba az az [login](/cli/azure/reference-index#az-login) paranccsal:

    ```azurecli
    az login
    ```
    
1. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példában létrehozunk egy `AzureFunctionsQuickstart-rg` nevű erőforráscsoportot a `westeurope` régióban. (Az erőforráscsoport és az erőforrások általában az Ön közelében lévő régióban hozhatók létre a `az account list-locations` parancsból elérhető régió használatával.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > Nem futtathat Linux-és Windows-alkalmazásokat ugyanabban az erőforráscsoporthoz. Ha van egy `AzureFunctionsQuickstart-rg` nevű meglévő erőforráscsoport egy Windows-függvény alkalmazással vagy webalkalmazással, egy másik erőforráscsoportot kell használnia.
    
1. Hozzon létre egy általános célú Storage-fiókot az erőforráscsoport és a régió területén az az [Storage Account Create](/cli/azure/storage/account#az-storage-account-create) paranccsal. Az alábbi példában cserélje le a `<storage_name>`t a megfelelő globálisan egyedi névre. A névnek három – 24 karakterből kell állnia, és csak kisbetűket tartalmazhat. `Standard_LRS` egy tipikus általános célú fiókot határoz meg.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Ebben a rövid útmutatóban a Storage-fiók mindössze néhány USD-t vesz fel.
    
1. Hozza létre a functions alkalmazást az az [functionapp Create](/cli/azure/functionapp#az-functionapp-create) parancs használatával. Az alábbi példában cserélje le a `<storage_name>` az előző lépésben használt fiók nevére, és cserélje le a `<app_name>`t az Önnek megfelelő globálisan egyedi névre. Az `<app_name>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál,

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    Ez a parancs létrehoz egy, a megadott nyelvi futtatókörnyezetet futtató Function alkalmazást a [Azure functions használati terv](functions-scale.md#consumption-plan)alatt, amely ingyenesen használható az itt felmerülő használathoz. A parancs egy kapcsolódó Azure Application Insights-példányt is kiépít ugyanabban az erőforráscsoporthoz, amellyel nyomon követheti a Function alkalmazást, és megtekintheti a naplókat. További információ: [Azure functions figyelése](functions-monitoring.md). A példány nem jár költséggel, amíg be nem aktiválja.
    
## <a name="deploy-the-function-project-to-azure"></a>A Function projekt üzembe helyezése az Azure-ban

A szükséges erőforrásokkal most már készen áll a helyi functions-projekt üzembe helyezésére az Azure-beli Function alkalmazásban az functions [Azure functionapp publish](functions-run-local.md#project-file-deployment) parancs használatával. Az alábbi példában cserélje le az `<app_name>` az alkalmazás nevére.

```
func azure functionapp publish <app_name>
```

Ha a következő hibaüzenet jelenik meg: "nem található az alkalmazás neve...", várjon néhány másodpercet, és próbálkozzon újra, mivel az Azure nem tudja teljesen inicializálni az alkalmazást az előző `az functionapp create` parancs után.

A közzétételi parancs a következő kimenethez hasonló eredményeket jelenít meg (egyszerűség kedvéért csonkítva):

```output
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
```

## <a name="invoke-the-function-on-azure"></a>A függvény meghívása az Azure-ban

Mivel a függvény HTTP-eseményindítót használ, meghívja azt úgy, hogy HTTP-kérést küld az URL-címére a böngészőben, vagy egy olyan eszközzel, mint például a curl. Mindkét példányban a `code` URL-cím paraméter az egyedi funkcióbillentyű, amely engedélyezi a hívást a függvény végpontján.

# <a name="browsertabbrowser"></a>[Böngésző](#tab/browser)

Másolja a publish (közzététel) parancs kimenetében megjelenő teljes **Meghívási URL-** címet egy böngésző címsorába, és illessze be a lekérdezési paramétert `&name=Azure`. A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor a funkciót helyileg futtatta.

![A függvény kimenete az Azure-ban egy böngészőben fut](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curltabcurl"></a>[Curl](#tab/curl)

Futtassa a [curlot](https://curl.haxx.se/) a **Meghívási URL-lel**, és adja hozzá a `&name=Azure`paramétert. A parancs kimenetének a "Hello Azure" szövegnek kell lennie.

![A függvény kimenete az Azure-on a curl használatával fut](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> A közzétett Python-alkalmazások közel valós idejű naplófájljainak megtekintéséhez használja a [Application Insights élő metrikastream](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatja a következő lépéssel, [vegyen fel egy Azure Storage-üzenetsor kimeneti kötését](functions-add-output-binding-storage-queue-python.md), tartsa meg az összes erőforrását, mivel a már elkészült dolgokra épít.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és az összes benne lévő erőforrást, hogy elkerülje a további költségek felmerülését.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Storage-üzenetsor kimeneti kötésének hozzáadása](functions-add-output-binding-storage-queue-python.md)
