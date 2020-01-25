---
title: Azure Storage-várólista kötésének hozzáadása a Python-függvényhez
description: Egy Azure Storage-várólista integrálása egy Python-függvénnyel egy kimeneti kötés használatával.
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: 14a381d13da052fd67679ed17bbb6b6711f7a0e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715365"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Azure Storage-várólista kötésének hozzáadása a Python-függvényhez

Ebben a cikkben egy Azure Storage-üzenetsor integrálására van szüksége a [http által aktivált Python-függvény létrehozása](functions-create-first-function-python.md)című témakörben létrehozott függvény-és Storage-fiókkal. Ezt az integrációt olyan *kimeneti kötés* használatával érheti el, amely http-kérésből adatokat ír a várólistában lévő üzenetbe. Ennek a cikknek a végrehajtásával az előző rövid útmutató néhány USD százalékán felül nem jár további költségekkel.

## <a name="prerequisites"></a>Előfeltételek

- Fejezze be a gyors üzembe helyezési útmutatót, [hozzon létre egy http által aktivált Python-függvényt](functions-create-first-function-python.md). Ha a cikk végén már megtisztította az erőforrásokat, folytassa újra a functions-alkalmazás Azure-ban való újbóli létrehozásához szükséges lépéseket, de hagyja változatlanul az erőforrásokat.

## <a name="retrieve-the-azure-storage-connection-string"></a>Az Azure Storage-beli kapcsolatok karakterláncának beolvasása

Amikor az előző rövid útmutatóban létrehozott egy Function alkalmazást az Azure-ban, létrehozta a Storage-fiókot is. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ha a beállítást a *Local. Settings. JSON* fájlba tölti le, akkor a függvény helyileg történő futtatásakor ugyanazzal a fiókkal írja be a kapcsolódást a tárolási várólistába. 

1. A projekt gyökerében futtassa a következő parancsot, és cserélje le a `<app_name>`t az előző rövid útmutatóban szereplő Function alkalmazás nevére. Ez a parancs felülírja a fájlban lévő összes meglévő értéket.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Nyissa meg a *Local. Settings. JSON* fájlt, és keresse meg `AzureWebJobsStorage`nevű értéket, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. A jelen cikk más részeiben a `AzureWebJobsStorage` nevet és a kapcsolatok karakterláncát használja.

> [!IMPORTANT]
> Mivel a *Local. Settings. JSON* az Azure-ból letöltött titkos kulcsokat tartalmaz, mindig zárja ki ezt a fájlt a forrás vezérlőelemből. A helyi functions projekttel létrehozott *. gitignore* fájl alapértelmezés szerint kizárja a fájlt.

## <a name="add-an-output-binding-to-functionjson"></a>Kimeneti kötés hozzáadása a function. JSON fájlhoz

Bár a függvények csak egy triggerrel rendelkezhetnek, több bemeneti és kimeneti kötéssel is rendelkezhet, amelyek lehetővé teszik más Azure-szolgáltatásokhoz és-erőforrásokhoz való csatlakozást az egyéni integrációs kód írása nélkül. Ezeket a kötéseket a függvény mappában lévő *function. JSON* fájlban kell deklarálni, a függvényhez használt nyelvnek megfelelően.

Az előző rövid útmutatóban a *function. JSON* fájl a *HttpExample* mappában két kötést tartalmaz a `bindings` gyűjteményben:

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

Minden kötéshez legalább egy típus, egy irány és egy név tartozik. A fenti példában az első kötés `httpTrigger` típusú, `in`irányával. A `in` irányában a `name` megadja egy bemeneti paraméter nevét, amelyet a rendszer az eseményindító által meghívott függvénynek továbbít.

A második kötés `http` típusú, `out`iránya, ebben az esetben a `$return` speciális `name` azt jelzi, hogy ez a kötés a függvény visszatérési értékét használja ahelyett, hogy bemeneti paramétert adjon meg.

Ha a függvényből szeretne írni egy Azure Storage-várólistába, adjon hozzá egy `queue` nevű `out` kötést a (z) `msg`néven az alábbi kódban látható módon:

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

Ebben az esetben `msg` van megadva a függvény kimeneti argumentumként. `queue` típus esetén meg kell adnia a várólista nevét is `queueName`, és meg kell adnia az Azure Storage-kapcsolatok *nevét* (a *Local. Settings. JSON*fájlból) a `connection`ban.

További információ a kötések részleteiről: [Azure functions eseményindítók és kötések fogalmak](functions-triggers-bindings.md) és [üzenetsor kimenetének konfigurálása](functions-bindings-storage-queue.md#output---configuration).

## <a name="add-code-to-use-the-output-binding"></a>Kód hozzáadása a kimeneti kötés használatához

A *function. JSON*fájlban megadott üzenetsor-kötéssel frissítheti a függvényt, hogy megkapja a `msg` kimeneti paramétert, és üzeneteket írjon a várólistába.

Frissítse a *HttpExample\\\_\_init\_\_.* a következő kódot, és adja hozzá a `msg` paramétert a Function definícióhoz, és `msg.set(name)` a `if name:` utasítás alatt.

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

A `msg` paraméter a [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)egy példánya. A `set` metódus egy karakterlánc-üzenetet ír a várólistába, ebben az esetben az URL-lekérdezési karakterlánc függvénynek átadott nevet.

Figyelje meg, hogy *nem* kell kódot írnia a hitelesítéshez, a várólista-hivatkozás beszerzéséhez vagy az adatíráshoz. Az összes ilyen integrációs feladat kényelmesen kezelhető a Azure Functions futtatókörnyezetben és a várólista kimeneti kötésében.

## <a name="run-and-test-the-function-locally"></a>A függvény helyi futtatása és tesztelése

1. Egy terminálon vagy parancssorban navigáljon a Function Project mappára, a *LocalFunctionProj*.

1. Indítsa el a Azure Functions Runtime-gazdagépet a következő parancs használatával.

    ```
    func host start
    ```

1. Ha az indítás befejeződött, és megjelenik az `HttpExample` végpont URL-címe, másolja az URL-címet egy böngészőbe, és fűzze hozzá a lekérdezési karakterláncot `?name=<your-name>`, így a teljes URL-cím, például a `http://localhost:7071/api/HttpExample?name=Guido`. A böngészőben a következőhöz hasonló üzenetnek kell megjelennie, mint az előző cikkben `Hello Guido`.

    Ha nem látja a `HttpExample` végpontot, állítsa le a gazdagépet a CTRL+**C** **billentyűkombinációval** , és ellenőrizze a hibák kimenetét. A gazdagép például nem aktiválja a végpontot, ha a *function. JSON*fájlban hiba van. Győződjön meg arról is, hogy `func host start`t futtat a functions projekt mappából, és nem a *HttpExample* mappából.

1. Ha elkészült, állítsa le a gazdagépet a CTRL+**C** **billentyűkombinációval** .

> [!TIP]
> Az indítás során a gazdagép letölti és telepíti a [Storage kötési bővítményt](functions-bindings-storage-blob.md#packages---functions-2x-and-higher) és a Microsoft egyéb kötési bővítményeit. Ez a telepítés azért történik, mert a kötési bővítmények alapértelmezés szerint engedélyezve vannak a *Host. JSON* fájlban a következő tulajdonságokkal:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Ha a kötési bővítményekkel kapcsolatos hibákat észlel, ellenőrizze, hogy a fenti tulajdonságok szerepelnek-e a *Host. JSON*fájlban.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Az üzenet megtekintése az Azure Storage-várólistában

Ha a függvény HTTP-választ hoz létre a webböngészőhöz, a `msg.set(name)`is meghívja, amely egy `outqueue`nevű Azure Storage-várólistára ír egy üzenetet, a várólista-kötésben megadott módon. Az üzenetsor a [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) vagy a [Microsoft Azure Storage Explorerban](https://storageexplorer.com/)tekinthető meg. Az üzenetsor az Azure CLI-ben is megtekinthető az alábbi lépésekben leírtak szerint:

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
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
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
    
## <a name="redeploy-the-project-to-azure"></a>A projekt újbóli üzembe helyezése az Azure-ban

Most, hogy a funkciót helyileg tesztelte, és ellenőrizte, hogy üzenetet írt az Azure Storage-üzenetsor számára, a projekt újbóli üzembe helyezésével frissítheti az Azure-on futó végpontot.

1. A *LocalFunctionsProj* mappában a [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) parancs használatával telepítse újra a projektet, és cserélje le a`<app_name>`t az alkalmazás nevére.

    ```
    func azure functionapp publish <app_name>
    ```
    
1. Ahogy az előző rövid útmutatóban is, az újratelepített függvény teszteléséhez használjon böngészőt vagy CURL-t.

    # <a name="browsertabbrowser"></a>[Böngésző](#tab/browser)
    
    Másolja a publish (közzététel) parancs kimenetében megjelenő teljes **Meghívási URL-** címet egy böngésző címsorába, és illessze be a lekérdezési paramétert `&name=Azure`. A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor a funkciót helyileg futtatta.

    ![A függvény kimenete az Azure-ban egy böngészőben fut](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curltabcurl"></a>[Curl](#tab/curl)
    
    Futtassa a [curlot](https://curl.haxx.se/) a **Meghívási URL-lel**, és adja hozzá a `&name=Azure`paramétert. A parancs kimenetének a "Hello Azure" szövegnek kell lennie.
    
    ![A függvény kimenete az Azure-on a curl használatával fut](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. Az előző szakaszban leírtak szerint vizsgálja meg újra a tárolási várólistát annak ellenőrzéséhez, hogy az tartalmazza-e a várólistára írt új üzenetet.

    Ha az Azure CLI-t használja a várólista vizsgálatához, akkor a `az storage message peek` parancs csak az üzenetsor első üzenetét jeleníti meg. Az üzenetek feldolgozásának szimulálása érdekében a `az storage message get` helyett használja az összes azonos argumentumot. Az `get` parancs visszaadja az üzenetet, és eltávolítja azt a várólistából. Ezután megismételheti ugyanazt a parancsot, amíg a várólista üres (és a parancs hibát jelez).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatja a következő lépéssel, [engedélyezze a Application Insights integrációt](functions-monitoring.md#manually-connect-an-app-insights-resource), tartsa meg az összes erőforrását, mivel a már elkészült dolgokra épít.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és az összes benne lévő erőforrást, hogy elkerülje a további költségek felmerülését.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Application Insights integráció engedélyezése Azure Functions alkalmazással](functions-monitoring.md#manually-connect-an-app-insights-resource)

Egyéb erőforrások:

- [Példák a Pythonban elérhető teljes körű függvények projektjeire](/samples/browse/?products=azure-functions&languages=python).
- [Azure Functions Python fejlesztői útmutató](functions-reference-python.md)
- [Azure functions eseményindítók és kötések](functions-triggers-bindings.md).
- [Functions – díjszabási oldal](https://azure.microsoft.com/pricing/details/functions/)
- A [fogyasztási terv költségeinek becslése](functions-consumption-costs.md) .
