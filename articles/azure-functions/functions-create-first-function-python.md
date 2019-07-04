---
title: Egy HTTP által aktivált függvény létrehozása az Azure-ban
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
ms.openlocfilehash: 5ef30fbf647492f79c64508d8306868aa1f6b278
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444580"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Egy HTTP által aktivált függvény létrehozása az Azure-ban

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Ez a cikk bemutatja, hogyan hozzon létre egy Python-projektet, amely futtatja az Azure Functions parancssori eszközök használatával. A létrehozott függvény HTTP-kérelmekre váltja ki. Végül a projekthez, hogy a futtató közzéteszi egy [kiszolgáló nélküli függvény](functions-scale.md#consumption-plan) az Azure-ban.

Ez a cikk az Azure Functions két útmutatóink első. Ez a cikk befejezése után, [hozzáadása egy Azure Storage üzenetsor kimeneti kötésének](functions-add-output-binding-storage-queue-python.md) a függvényt.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, az alábbiakkal kell rendelkeznie:

+ Telepítés [Python 3.6-os](https://www.python.org/downloads/).

+ Telepítés [Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.1071 vagy újabb verzióját.

+ Telepítse a [Azure CLI-vel](/cli/azure/install-azure-cli) verzió 2.x-es vagy újabb verziója.

+ Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Hozzon létre, és aktiválja a virtuális környezet

Helyi fejlesztés és Python-funkciók teszteléséhez a Python 3.6-os környezetben kell működniük. Futtassa a következő parancsokat, létrehozása és aktiválása nevű virtuális környezetet `.env`.

### <a name="bash"></a>Bash:

```bash
python3.6 -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell vagy egy Windows parancssorban:

```powershell
py -3.6 -m venv .venv
.venv\scripts\activate
```

A fennmaradó parancsok futtatása a virtuális környezeten belül.

## <a name="create-a-local-functions-project"></a>Egy helyi Functions-projekt létrehozása

A Functions-projekt megegyezik egy függvényalkalmazást az Azure-ban. Több függvényt, hogy az összes megosztás ugyanezzel a helyi és üzemeltetési konfigurációval rendelkezhet.

A virtuális környezetben, futtassa a következő parancsot, kiválasztása **python** , a worker futásidejű.

```console
func init MyFunctionProj
```

Nevű mappa _MyFunctionProj_ jön létre, amely tartalmazza a következő három fájlokat:

* `local.settings.json` helyi futtatás során Alkalmazásbeállítások és a kapcsolati karakterláncok tárolásához használatos. Ez a fájl nem közzéteheti az Azure-bA.
* `requirements.txt` a közzététel az Azure-bA telepítendő csomagok listáját tartalmazza.
* `host.json` a függvényalkalmazás a függvények érintő globális konfigurációs beállításait tartalmazza. Ez a fájl közzéteheti az Azure-bA.

Keresse meg az új MyFunctionProj mappa:

```console
cd MyFunctionProj
```

Ezután frissítse a host.json fájl kiterjesztése csomagok engedélyezéséhez.  

## <a name="create-a-function"></a>Függvény létrehozása

Függvény hozzáadása a projekthez, futtassa a következő parancsot:

```console
func new
```

Válassza ki a **HTTP-eseményindító** írja be a sablon `HttpTrigger` , a függvény nevét, majd nyomja le az Enter.

Egy almappát _HttpTrigger_ jön létre, amely a következő fájlokat tartalmazza:

* **Function.JSON**: konfigurációs fájlt, amely meghatározza a függvény, eseményindító és más kötéseit. Ezt a fájlt, és megnézheti, hogy az érték `scriptFile` a fájlt, amely tartalmazza a függvény, amíg a meghívási triggerét és kötéseit meghatározott mutat a `bindings` tömb.

  Minden egyes kötés szükséges iránya, típusa és a egy egyedi nevet. A HTTP-eseményindítóval rendelkezik típusú bemeneti kötést [ `httpTrigger` ](functions-bindings-http-webhook.md#trigger) és a kimeneti kötés típusú [ `http` ](functions-bindings-http-webhook.md#output).

* **\_\_Init\_\_.py**: parancsfájlt, amely a HTTP által aktivált függvény. Ezt a szkriptet, és megnézheti, hogy tartalmaz egy alapértelmezett `main()`. Ez a funkció használatával HTTP-adatok a trigger által átadott a `req` kötési paraméter neve. Function.json, meghatározott `req` példánya a [azure.functions.HttpRequest osztály](/python/api/azure-functions/azure.functions.httprequest). 

    A visszaadott objektum definiálva `$return` function.json, a rendszer egy példánya [azure.functions.HttpResponse osztály](/python/api/azure-functions/azure.functions.httpresponse). További tudnivalókért lásd: [Azure Functions – HTTP-eseményindítók és kötések](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A következő parancsot helyileg használja az ugyanazon az Azure Functions runtime az Azure-függvényalkalmazás elindul.

```bash
func host start
```

A Functions gazdagép indításakor ír a következő kimenetet, amely az olvashatóság érdekében csonkolva lett hasonló:

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

## <a name="create-a-function-app-in-azure"></a>Függvényalkalmazás létrehozása az Azure-ban

Egy függvényalkalmazás szolgáltat környezetet a függvénykód végrehajtása. Lehetővé teszi, hogy egyszerűbb felügyelete, üzembe helyezéséhez és erőforrás-megosztás logikai egységbe csoportosítsa a függvényeket.

Futtassa a következő parancsot egy egyedi függvényalkalmazás-nevet helyén használatával a `<APP_NAME>` helyőrző és a tárfiók nevét `<STORAGE_NAME>`. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, ezért az Azure összes alkalmazásában csak egyszer használhatja.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

> [!NOTE]
> Az Azure Functions Linux rendszeren a Használatalapú csomag jelenleg előzetes verzióban és csak akkor érhető el a következő régiókban: USA nyugati RÉGIÓJA, USA keleti RÉGIÓJA, Nyugat-Európa, Kelet-Ázsia. Továbbá Linux és Windows-alkalmazások nem futhat az ugyanabban az erőforráscsoportban. Ha rendelkezik egy meglévő erőforráscsoportot `myResourceGroup` Windows függvényalkalmazás vagy webalkalmazás, egy másik erőforráscsoportot kell használnia.

Most már készen áll a helyi functions-projekt közzététele a függvényalkalmazáshoz az Azure-ban.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="next-steps"></a>További lépések

Egy Python functions-projekt létrehozta egy HTTP által aktivált függvény, futtassa a helyi gépén, és üzembe helyezte azt az Azure-bA. Most már kiterjesztheti a függvény által...

> [!div class="nextstepaction"]
> [Azure Storage-tárolók hozzáadása üzenetsor kimeneti kötése](functions-add-output-binding-storage-queue-python.md)
