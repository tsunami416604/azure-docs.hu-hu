---
title: HTTP által aktivált Python-függvény létrehozása az Azure-ban
description: Ismerje meg, hogyan hozhatja létre az első Python-függvényt az Azure-ban a Azure Functions Core Tools és az Azure CLI használatával.
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: 791348088d909785b36934c3b9a2ae00fc0acbb7
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622036"
---
# <a name="create-an-http-triggered-python-function-in-azure"></a>HTTP által aktivált Python-függvény létrehozása az Azure-ban

Ez a cikk bemutatja, hogyan hozhat létre olyan Python-projektet, amely Azure Functionsban fut. Létrehozhat egy HTTP-kérelem által aktivált függvényt is. Végezetül közzé kell tenni a projektet, hogy kiszolgáló nélküli [függvényként](functions-scale.md#consumption-plan) fusson az Azure-ban.

Ez a cikk a Azure Functions első két Python-gyors útmutatója. A rövid útmutató elvégzése után [hozzáadhat egy Azure Storage-üzenetsor kimeneti kötését](functions-add-output-binding-storage-queue-python.md) a függvényhez.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőket kell tennie:

+ Telepítse a [Python-3.6.8](https://www.python.org/downloads/). A Python ezen verzióját a functions ellenőrzi. 3,7 és újabb verziók még nem támogatottak.

+ Telepítse [Azure functions Core Tools](./functions-run-local.md#v2) 2.7.1575 vagy újabb verziót.

+ Telepítse az [Azure CLI](/cli/azure/install-azure-cli) 2. x vagy újabb verzióját.

+ Aktív Azure-előfizetéssel rendelkezik.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Virtuális környezet létrehozása és aktiválása (nem kötelező)

Python-függvények helyi fejlesztéséhez a Python 3.6. x környezetet kell használnia. Futtassa a következő parancsokat egy `.venv`nevű virtuális környezet létrehozásához és aktiválásához.

> [!NOTE]
> Ha a Python nem telepítette a venv-t a Linux-disztribúción, akkor a következő paranccsal telepítheti:
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell vagy Windows-Parancssor:

```powershell
py -m venv .venv
.venv\scripts\activate
```

Most, hogy aktiválta a virtuális környezetet, futtassa a többi parancsot. Ha ki szeretné próbálni a virtuális környezetet, futtassa `deactivate`.

## <a name="create-a-local-functions-project"></a>Helyi functions-projekt létrehozása

A functions-projekt egyenértékű az Azure-beli Function alkalmazásokkal. Több funkcióval is rendelkezhet, amelyek mindegyike ugyanazokat a helyi és üzemeltetési konfigurációkat használja.

1. A virtuális környezetben futtassa a következő parancsot:

    ```console
    func init MyFunctionProj
    ```

1. Válassza a **Python** lehetőséget munkavégző futtatókörnyezetként.

    A parancs létrehoz egy _MyFunctionProj_ mappát. A következő három fájlt tartalmazza:

    * *Local. Settings. JSON*: az Alkalmazásbeállítások és a kapcsolódási karakterláncok helyi futtatásakor való tárolásához használatos. Ez a fájl nem jelenik meg az Azure-ban.
    * *követelmények. txt*: azon csomagok listáját tartalmazza, amelyeket a rendszer az Azure-ba való közzétételre telepít.
    * a *Host. JSON*: olyan globális konfigurációs beállításokat tartalmaz, amelyek a Function alkalmazás összes funkcióját érintik. Ez a fájl közzé van téve az Azure-ban.

1. Nyissa meg az új *MyFunctionProj* mappát:

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>Függvény létrehozása

Adjon hozzá egy függvényt az új projekthez.

1. Ha függvényt szeretne hozzáadni a projekthez, futtassa a következő parancsot:

    ```console
    func new
    ```

1. A **http-trigger** sablon kiválasztásához használja a lefelé mutató nyilat.

1. Ha a rendszer kéri a függvény nevét, írja be a *HttpTrigger* nevet, majd nyomja le az ENTER billentyűt.

Ezek a parancsok létrehoznak egy _HttpTrigger_nevű almappát. A következő fájlokat tartalmazza:

* *function. JSON*: konfigurációs fájl, amely meghatározza a függvényt, az triggert és az egyéb kötéseket. Figyelje meg, hogy ebben a fájlban a `scriptFile` érték a függvényt tartalmazó fájlra mutat, a `bindings` tömb pedig meghatározza a meghívásos triggert és kötéseket.

    Minden kötéshez meg kell adni egy irányt, egy típust és egy egyedi nevet. A HTTP-trigger [`httpTrigger`](functions-bindings-http-webhook.md#trigger) típusú, [`http`](functions-bindings-http-webhook.md#output)típusú kimeneti kötést tartalmaz.

* *\_\_init\_\_.* file: szkriptet, amely a http által aktivált függvény. Figyelje meg, hogy ez a parancsfájl alapértelmezett `main()`. A triggerből származó HTTP-adatok a `binding parameter`nevű `req` használatával jutnak el a függvényhez. A function. JSON fájlban definiált `req`az [Azure. functions. HttpRequest osztály](/python/api/azure-functions/azure.functions.httprequest)egy példánya. 

    A *function. JSON*fájlban `$return`ként definiált visszatérési objektum az [Azure. functions. HttpResponse osztály](/python/api/azure-functions/azure.functions.httpresponse)egy példánya. További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A függvény helyileg fut a Azure Functions Runtime használatával.

1. Ez a parancs elindítja a Function alkalmazást:

    ```console
    func host start
    ```

    A Azure Functions-állomás indításakor a következő kimenethez hasonlóan kell írnia. Itt csonkolt, így jobban áttekinthető:

    ```output
    
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %
    
    ...
    
    Content root path: C:\functions\MyFunctionProj
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    ...
    
    Http Functions:
    
            HttpTrigger: http://localhost:7071/api/HttpTrigger
    
    [8/27/2018 10:38:27 PM] Host started (29486ms)
    [8/27/2018 10:38:27 PM] Job host started
    ```

1. Másolja ki a `HttpTrigger` függvény URL-címét a futtatókörnyezetének kimenetéből, majd illessze be a böngészője címsorába.

1. Az URL-címhez fűzze hozzá a `?name=<yourname>` lekérdezési sztringet, és hajtsa végre a kérelmet. Az alábbi képernyőfelvételen a GET kérelemre adott válasz látható, amelyet a helyi függvény a böngészőnek ad vissza:

    ![Helyi ellenőrzés a böngészőben](./media/functions-create-first-function-python/function-test-local-browser.png)

1. Válassza a CTRL + C billentyűkombinációt a Function alkalmazás leállításához.

Most, hogy már futtatta a függvényt helyben, létrehozhatja az Azure-ban a függvényalkalmazást és az egyéb szükséges erőforrásokat.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Function-alkalmazás létrehozása az Azure-ban

A Function alkalmazás egy környezetet biztosít a függvény kódjának végrehajtásához. Lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.

Futtassa a következő parancsot. Cserélje le a `<APP_NAME>`t egy egyedi Function app-névvel. Cserélje le a `<STORAGE_NAME>`t egy Storage-fiók nevére. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, ezért az Azure összes alkalmazásában csak egyszer használhatja.

> [!NOTE]
> Nem futtathat Linux-és Windows-alkalmazásokat ugyanabban az erőforráscsoporthoz. Ha van egy `myResourceGroup` nevű meglévő erőforráscsoport egy Windows-függvény alkalmazással vagy webalkalmazással, egy másik erőforráscsoportot kell használnia.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Az előző parancs egy társított Azure Application Insights-példányt is kiépít ugyanabban az erőforráscsoporthoz. Ezzel a példánnyal figyelheti a Function alkalmazást, és megtekintheti a naplókat.

Most már készen áll a helyi functions-projekt közzétételére az Azure-beli Function alkalmazásban.

## <a name="deploy-the-function-app-project-to-azure"></a>A függvényalkalmazás projektjének üzembe helyezése az Azure-ban

Miután létrehozta a Function alkalmazást az Azure-ban, használhatja a functions [Azure functionapp publish](functions-run-local.md#project-file-deployment) Core Tools parancsot a projekt kódjának az Azure-ba történő üzembe helyezéséhez. Ebben a példában a `<APP_NAME>` helyére írja be az alkalmazás nevét.

```console
func azure functionapp publish <APP_NAME> --build remote
```

A `--build remote` lehetőség a Python-projektet az Azure-ban hozza létre távolról a központi telepítési csomagban található fájlokból. 

A következő üzenethez hasonló kimenet jelenik meg. Itt csonkolt, így jobban áttekinthető:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Másolhatja a `HttpTrigger` `Invoke url` értékét, és felhasználhatja az Azure-ban található függvény ellenőrzéséhez. Az URL-cím egy `code` lekérdezési karakterlánc értéket tartalmaz, amely a függvény kulcsa, ami megnehezíti mások számára a HTTP-trigger végpontjának meghívását az Azure-ban.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> A közzétett Python-alkalmazások közel valós idejű naplófájljainak megtekintéséhez használja a [Application Insights élő metrikastream](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>További lépések

Létrehozott egy Python functions-projektet egy HTTP által aktivált függvénnyel, futtatta azt a helyi gépen, és üzembe helyezte az Azure-ban. Most terjessze ki a függvényt...

> [!div class="nextstepaction"]
> [Azure Storage-üzenetsor kimeneti kötésének hozzáadása](functions-add-output-binding-storage-queue-python.md)
