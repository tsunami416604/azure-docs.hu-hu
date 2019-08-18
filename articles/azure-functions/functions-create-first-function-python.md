---
title: HTTP által aktivált függvény létrehozása az Azure-ban
description: Útmutató az első Python-függvény létrehozása az Azure-ban az Azure Functions Core Tools és az Azure CLI használatával.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 47de61db96b0f8f9b338f135d4f32eecc4a64efe
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562947"
---
# <a name="create-an-http-triggered-function-in-azure"></a>HTTP által aktivált függvény létrehozása az Azure-ban

Ez a cikk bemutatja, hogyan hozhat létre olyan Python-projektet, amely Azure Functionsban fut. A létrehozott függvényt HTTP-kérések aktiválják. Végezetül közzé kell tenni a projektet, hogy kiszolgáló nélküli [függvényként](functions-scale.md#consumption-plan) fusson az Azure-ban.

Ez a cikk a Azure Functions első két rövid útmutatója. A cikk elvégzése után [hozzáadhat egy Azure Storage-üzenetsor kimeneti kötését](functions-add-output-binding-storage-queue-python.md) a függvényhez.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőkkel kell rendelkeznie:

+ Telepítse a [Python 3,6](https://www.python.org/downloads/)-es frissítést.

+ Telepítse [Azure functions Core Tools](./functions-run-local.md#v2) 2.6.1071 vagy újabb verziót.

+ Telepítse az [Azure CLI](/cli/azure/install-azure-cli) 2. x vagy újabb verzióját.

+ Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Virtuális környezet létrehozása és aktiválása (nem kötelező)

Python-függvények helyi fejlesztéséhez és teszteléséhez javasolt Python 3,6-környezet használata. Futtassa a következő parancsokat, létrehozása és aktiválása nevű virtuális környezetet `.venv`.

### <a name="bash"></a>Bash

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell vagy Windows-Parancssor:

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

A többi parancs a virtuális környezeten belül fut.

## <a name="create-a-local-functions-project"></a>Egy helyi Functions-projekt létrehozása

A functions-projekt egyenértékű az Azure-beli Function alkalmazásokkal. Több funkcióval is rendelkezhet, amelyek mindegyike ugyanazokat a helyi és üzemeltetési konfigurációkat használja.

A virtuális környezetben futtassa a következő parancsot, és válassza a **Python** lehetőséget munkavégző futtatókörnyezetként.

```console
func init MyFunctionProj
```

Létrejön egy _MyFunctionProj_ nevű mappa, amely a következő három fájlt tartalmazza:

* `local.settings.json`az alkalmazás beállításainak és a kapcsolódási karakterláncoknak a helyi futtatásakor történő tárolására szolgál. Ez a fájl nem jelenik meg az Azure-ban.
* `requirements.txt`Az Azure-ba való közzétételre telepítendő csomagok listáját tartalmazza.
* `host.json`olyan globális konfigurációs beállításokat tartalmaz, amelyek a Function alkalmazás összes funkcióját érintik. Ez a fájl közzé van téve az Azure-ban.

Navigáljon az új MyFunctionProj mappára:

```console
cd MyFunctionProj
```

Ezután frissítse a Host. JSON fájlt a bővítmény-csomagok engedélyezéséhez.  

## <a name="create-a-function"></a>Függvény létrehozása

Ha függvényt szeretne hozzáadni a projekthez, futtassa a következő parancsot:

```console
func new
```

Válassza ki a **http-trigger** sablonját, írja be `HttpTrigger` a függvény nevét, majd nyomja le az ENTER billentyűt.

A rendszer létrehoz egy _HttpTrigger_ nevű almappát, amely a következő fájlokat tartalmazza:

* **function. JSON**: konfigurációs fájl, amely meghatározza a függvényt, az triggert és az egyéb kötéseket. Tekintse át ezt a fájlt, és tekintse meg, hogy a függvényt tartalmazó fájlra mutató `scriptFile` pontok értéke, míg a meghívásos trigger és kötések definiálva vannak a `bindings` tömbben.

  Minden kötéshez meg kell adni egy irányt, egy típust és egy egyedi nevet. A http [`httpTrigger`](functions-bindings-http-webhook.md#trigger) -trigger típusa és kimeneti kötése típusú [`http`](functions-bindings-http-webhook.md#output)bemeneti kötést tartalmaz.

* **init.file\_: a http által aktivált függvényt tartalmazó parancsfájl.\_ \_ \_** Tekintse át ezt a parancsfájlt, és ellenőrizze `main()`, hogy az tartalmaz-e alapértelmezett értéket. Az triggerből származó http-adatok átadása ehhez a `req` függvényhez a nevesített kötési paraméter használatával történik. A function. JSON `req` fájlban definiált érték az [Azure. functions. HttpRequest osztály](/python/api/azure-functions/azure.functions.httprequest)egy példánya. 

    A function. JSON fájlban megadott `$return` visszatérési objektum az [Azure. functions. HttpResponse osztály](/python/api/azure-functions/azure.functions.httpresponse)egy példánya. További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A következő parancs elindítja a Function alkalmazást, amely az Azure-ban használt Azure Functions futtatókörnyezettel helyileg fut.

```bash
func host start
```

A functions gazdagép indításakor a következő kimenetnek kell megadnia, amelyet az olvashatóság érdekében csonkolt:

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

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Másolja ki a `HttpTrigger` függvény URL-címét a futtatókörnyezetének kimenetéből, majd illessze be a böngészője címsorába. Az URL-címhez fűzze hozzá a `?name=<yourname>` lekérdezési sztringet, és hajtsa végre a kérelmet. Az alábbiakban látható a böngészőben a GET kérelemre a helyi függvény által visszaadott válasz:

![Helyi tesztelés a böngészőben](./media/functions-create-first-function-python/function-test-local-browser.png)

Most, hogy már futtatta a függvényt helyben, létrehozhatja az Azure-ban a függvényalkalmazást és az egyéb szükséges erőforrásokat.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Function-alkalmazás létrehozása az Azure-ban

A Function alkalmazás egy környezetet biztosít a függvény kódjának végrehajtásához. Lehetővé teszi, hogy egyszerűbb felügyelete, üzembe helyezéséhez és erőforrás-megosztás logikai egységbe csoportosítsa a függvényeket.

Futtassa a következő parancsot egy egyedi függvényalkalmazás-nevet helyén használatával a `<APP_NAME>` helyőrző és a tárfiók nevét `<STORAGE_NAME>`. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, ezért az Azure összes alkalmazásában csak egyszer használhatja.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```
> [!NOTE]
> A Linux-és Windows-alkalmazások nem futhatnak ugyanabban az erőforráscsoporthoz. Ha már van egy nevű `myResourceGroup` erőforráscsoport egy Windows-függvény alkalmazással vagy webalkalmazással, egy másik erőforráscsoportot kell használnia.

Ezzel a paranccsal egy társított Azure Application Insights-példány is kiépíthető ugyanabban az erőforráscsoporthoz, amely a naplók figyelésére és megtekintésére használható.

Most már készen áll a helyi functions-projekt közzétételére az Azure-beli Function alkalmazásban.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> A közzétett Python-alkalmazások közel valós idejű naplófájljainak megtekintéséhez javasoljuk, hogy használja a [Application Insights élő metrikastream](functions-monitoring.md#streaming-logs)

## <a name="next-steps"></a>További lépések

Létrehozott egy Python functions-projektet egy HTTP által aktivált függvénnyel, futtatta azt a helyi gépen, és üzembe helyezte az Azure-ban. Most terjessze ki a függvényt...

> [!div class="nextstepaction"]
> [Azure Storage-üzenetsor kimeneti kötésének hozzáadása](functions-add-output-binding-storage-queue-python.md)
