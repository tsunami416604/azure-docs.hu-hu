---
title: HTTP által aktivált Python-függvény létrehozása az Azure-ban
description: Útmutató az első Python-függvény létrehozása az Azure-ban az Azure Functions Core Tools és az Azure CLI használatával.
ms.date: 11/07/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 18ae1ed000ffe61ce1ea9ff5c18aae98a0ffae65
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227190"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Gyors útmutató: HTTP által aktivált Python-függvény létrehozása az Azure-ban

Ez a cikk bemutatja, hogyan hozhat létre olyan Python-projektet, amely Azure Functionsban fut. Létrehozhat egy HTTP-kérelem által aktivált függvényt is. A helyileg történő futtatás után közzé kell tennie a projektet, hogy [kiszolgáló nélküli függvényként](functions-scale.md#consumption-plan) fusson az Azure-ban. 

Ez a cikk a Azure Functions első két Python-gyors útmutatója. A rövid útmutató elvégzése után [hozzáadhat egy Azure Storage-üzenetsor kimeneti kötését](functions-add-output-binding-storage-queue-python.md) a függvényhez.

A cikk [Visual Studio Code-alapú verziója](/azure/python/tutorial-vs-code-serverless-python-01) is létezik.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőket kell tennie:

+ Telepítse a [Python-3.7.4](https://www.python.org/downloads/). A Python ezen verzióját a functions ellenőrzi. A Python 3,8-es és újabb verziói még nem támogatottak.

+ Telepítse [Azure functions Core Tools](./functions-run-local.md#v2) 2.7.1846 vagy újabb verziót.

+ Telepítse az [Azure CLI](/cli/azure/install-azure-cli) verzióját 2.0.76 vagy újabb verzióra.

+ Aktív Azure-előfizetéssel rendelkezik.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Hozzon létre, és aktiválja a virtuális környezet

Python 3,7-környezetet kell használnia a Python-függvények helyi fejlesztéséhez. Futtassa a következő parancsokat egy `.venv`nevű virtuális környezet létrehozásához és aktiválásához.

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

A functions projekt több funkcióval is rendelkezhet, amelyek mindegyike ugyanazokat a helyi és üzemeltetési konfigurációkat használja.

A virtuális környezetben futtassa a következő parancsokat:

```console
func init MyFunctionProj --python
cd MyFunctionProj
```

A `func init` parancs létrehoz egy _MyFunctionProj_ mappát. A mappában található Python-projekthez még nincs funkció. Ezután adja hozzá őket.

## <a name="create-a-function"></a>Függvény létrehozása

Ha függvényt szeretne hozzáadni a projekthez, futtassa a következő parancsot:

```console
func new --name HttpTrigger --template "HTTP trigger"
```

Ez a parancs létrehoz egy _HttpTrigger_nevű almappát, amely a következő fájlokat tartalmazza:

* *function. JSON*: konfigurációs fájl, amely meghatározza a függvényt, az triggert és az egyéb kötéseket. Figyelje meg, hogy ebben a fájlban a `scriptFile` érték a függvényt tartalmazó fájlra mutat, a `bindings` tömb pedig meghatározza a meghívásos triggert és kötéseket.

    Minden kötéshez meg kell adni egy irányt, egy típust és egy egyedi nevet. A HTTP-trigger [`httpTrigger`](functions-bindings-http-webhook.md#trigger) típusú, [`http`](functions-bindings-http-webhook.md#output)típusú kimeneti kötést tartalmaz.

* *\_\_init\_\_.* file: szkriptet, amely a http által aktivált függvény. Figyelje meg, hogy ez a parancsfájl alapértelmezett `main()`. A triggerből származó HTTP-adatok a `binding parameter`nevű `req` használatával jutnak el a függvényhez. A function. JSON fájlban definiált `req`az [Azure. functions. HttpRequest osztály](/python/api/azure-functions/azure.functions.httprequest)egy példánya. 

    A *function. JSON*fájlban `$return`ként definiált visszatérési objektum az [Azure. functions. HttpResponse osztály](/python/api/azure-functions/azure.functions.httpresponse)egy példánya. További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](functions-bindings-http-webhook.md).

Most már futtathatja az új függvényt a helyi számítógépen.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Ez a parancs elindítja a Function alkalmazást a Azure Functions futtatókörnyezet (functions. exe) használatával:

```console
func host start
```

A kimenetre a következő információkat kell megadnia:

```output
Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger    
```

Másolja a `HttpTrigger` függvény URL-címét ebből a kimenetből, és illessze be a böngésző címsorába. Az URL-címhez fűzze hozzá a `?name=<yourname>` lekérdezési sztringet, és hajtsa végre a kérelmet. Az alábbi képernyőfelvételen a GET kérelemre adott válasz látható, amelyet a helyi függvény a böngészőnek ad vissza:

![Helyi ellenőrzés a böngészőben](./media/functions-create-first-function-python/function-test-local-browser.png)

A CTRL + C billentyűkombinációval állítsa le a Function app-végrehajtást.

Most, hogy helyileg futtatta a függvényt, üzembe helyezheti a függvény kódját az Azure-ban.  
Az alkalmazás üzembe helyezése előtt létre kell hoznia néhány Azure-erőforrást.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Function-alkalmazás létrehozása az Azure-ban

A Function alkalmazás egy környezetet biztosít a függvény kódjának végrehajtásához. Lehetővé teszi, hogy egyszerűbb felügyelete, üzembe helyezéséhez és erőforrás-megosztás logikai egységbe csoportosítsa a függvényeket. 

Futtassa a következő parancsot. Cserélje le a `<APP_NAME>`t egy egyedi Function app-névvel. Cserélje le a `<STORAGE_NAME>`t egy Storage-fiók nevére. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, ezért az Azure összes alkalmazásában csak egyszer használhatja.

> [!NOTE]
> Nem futtathat Linux-és Windows-alkalmazásokat ugyanabban az erőforráscsoporthoz. Ha van egy `myResourceGroup` nevű meglévő erőforráscsoport egy Windows-függvény alkalmazással vagy webalkalmazással, egy másik erőforráscsoportot kell használnia.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python --runtime-version 3.7 \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Az előző parancs egy Python-3.7.4 futtató Function alkalmazást hoz létre. Emellett egy társított Azure Application Insights-példányt is kiépít ugyanabban az erőforráscsoporthoz. Ezzel a példánnyal figyelheti a Function alkalmazást, és megtekintheti a naplókat. 

Most már készen áll a helyi functions-projekt közzétételére az Azure-beli Function alkalmazásban.

## <a name="deploy-the-function-app-project-to-azure"></a>A függvényalkalmazás projektjének üzembe helyezése az Azure-ban

Miután létrehozta a Function alkalmazást az Azure-ban, használhatja a functions [Azure functionapp publish](functions-run-local.md#project-file-deployment) Core Tools parancsot a projekt kódjának az Azure-ba történő üzembe helyezéséhez. Ebben a példában a `<APP_NAME>` helyére írja be az alkalmazás nevét.

```console
func azure functionapp publish <APP_NAME> --build remote
```

A `--build remote` lehetőség a Python-projektet az Azure-ban hozza létre távolról a központi telepítési csomagban található fájlokból, ami ajánlott. 

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

## <a name="next-steps"></a>Következő lépések

Létrehozott egy Python functions-projektet egy HTTP által aktivált függvénnyel, futtatta azt a helyi gépen, és üzembe helyezte az Azure-ban. Most terjessze ki a függvényt...

> [!div class="nextstepaction"]
> [Azure Storage-üzenetsor kimeneti kötésének hozzáadása](functions-add-output-binding-storage-queue-python.md)
