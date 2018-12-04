---
title: Az első tartós függvény létrehozása az Azure-ban a JavaScript használatával
description: Hozzon létre, és a egy Visual Studio Code használatával tartós Azure-függvény közzététele.
services: functions
documentationcenter: na
author: ColbyTresness
manager: jeconnoc
keywords: azure functions, függvények, eseményfeldolgozás, számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: azfuncdf, cotresne, glenga
ms.openlocfilehash: 7dceed4d81f1e1767cbf91804573043d1204beee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838905"
---
# <a name="create-your-first-durable-function-in-javascript"></a>A JavaScript tartós függvény létrehozása

*Durable Functions* kiterjesztése [Azure Functions](../functions-overview.md) , amellyel írási állapot-nyilvántartó functions egy kiszolgáló nélküli környezetben. A bővítmény kezeli a állapot, ellenőrzőpontok és újraindul az Ön számára.

Ebből a cikkből elsajátíthatja az Azure Functions Visual Studio Code-bővítmény használata helyi létrehozásához és a egy "hello world" tartós függvény tesztelése.  Ez a függvény összehangolására, és a lánc együttesen hívások egyéb funkciók. Ezután közzéteheti a függvénykódot az Azure-ban.

![Azure-ban futó tartós függvény](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítés [Visual Studio Code](https://code.visualstudio.com/download).

* Győződjön meg arról, hogy rendelkezik a [legújabb Azure Functions-eszközökkel](../functions-develop-vs.md#check-your-tools-version).

* A Windows-számítógépen ellenőrizze, hogy rendelkezik a [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) telepíteni és futtatni. A Mac vagy Linux rendszerű számítógépen egy tényleges Azure storage-fiókot kell használnia.

* Győződjön meg arról, hogy 8.0-s verzió vagy újabb verziója, [Node.js](https://nodejs.org/) telepítve.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="create-a-starter-function"></a>Alapszintű függvény létrehozása

Először hozzon létre egy HTTP által aktivált függvényt, amely egy tartós függvény vezénylési elindítja.

1. Az **Azure: Functions** menüben válassza a függvény létrehozására szolgáló ikont.

    ![Függvény létrehozása](./media/quickstart-js-vscode/create-function.png)

1. Válassza ki a függvényalkalmazás-projektet tartalmazó mappát, majd válassza a **HTTP-trigger** (HTTP-eseményindító) függvénysablont.

    ![A HTTP-eseményindító sablon kiválasztása](./media/quickstart-js-vscode/create-function-choose-template.png)

1. A függvénynek adja a `HttpStart` nevet, nyomja le az Enter billentyűt, majd válassza az **Anonymous** (Névtelen) hitelesítést.

    ![A névtelen hitelesítés kiválasztása](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

    A rendszer létrehoz egy függvényt a választott nyelven a HTTP által indított függvények sablonjának használatával.

1. Cserélje le az index.js a JavaScript alatt:

    ```javascript
    const df = require("durable-functions");
    
    module.exports = async function (context, req) {
        const client = df.getClient(context);
        const instanceId = await client.startNew(req.params.functionName, undefined, req.body);
    
        context.log(`Started orchestration with ID = '${instanceId}'.`);
    
        return client.createCheckStatusResponse(context.bindingData.req, instanceId);
    };
    ```

1. Cserélje le a function.json az alábbi JSON-ban:

    ```JSON
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "name": "req",
          "type": "httpTrigger",
          "direction": "in",
          "route": "orchestrators/{functionName}",
          "methods": ["post"]
        },
        {
          "name": "$return",
          "type": "http",
          "direction": "out"
        },
        {
          "name": "starter",
          "type": "orchestrationClient",
          "direction": "in"
        }
      ],
      "disabled": false
    }
    ```

Ezzel létrehoztunk egy belépési pont a tartós függvény. Adjunk hozzá egy orchestrator.

## <a name="create-an-orchestrator-function"></a>Az Orchestrator-függvény létrehozása

Ezután hozzon létre egy másik függvényt az orchestrator lennie. A HTTP-eseményindító sablonjához függvényt használjuk kényelmi célokat szolgál. Magát a függvénykódot az orchestrator kódot váltja fel.

1. Ismételje meg az előző szakaszban hozhat létre egy másik függvény használatával a HTTP-eseményindító sablonjához. Most adja a függvénynek `OrchestratorFunction`.

1. Nyissa meg az új függvény az index.js fájlt, és cserélje ki annak tartalmát az alábbira:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

1. Nyissa meg a function.json fájlt, és cserélje le a következő JSON-ra:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Hozzáadtunk egy orchestrator tevékenységfüggvényeket koordinálására. Most adjunk odkazované aktivity függvény.

## <a name="create-an-activity-function"></a>Egy tevékenység függvény létrehozása

1. Ismételje meg a használatával a HTTP-eseményindító sablonjához harmadik függvény létrehozásához az előző szakaszokban. De ezúttal adja a függvénynek `SayHello`.

1. Nyissa meg az új függvény az index.js fájlt, és cserélje ki annak tartalmát az alábbira:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

1. Cserélje le a function.json az alábbi JSON-ban:

    [!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

Hozzáadtunk egy vezénylési és lánc együttesen tevékenységfüggvényeket indulhat szükséges összes összetevő most.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studio Code-ból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.  

1. Telepítse az npm-durable-functions csomag futtatásával `npm install durable-functions` függvényalkalmazás gyökérkönyvtárában.

1. A Windows-számítógépen indítsa el az Azure Storage Emulator, és ellenőrizze, hogy a **AzureWebJobsStorage** local.settings.json tulajdonságának értéke `UseDevelopmentStorage=true`. A Mac vagy Linux rendszerű számítógépen kell állítani a **AzureWebJobsStorage** tulajdonságát egy meglévő Azure-tárfiók kapcsolati sztringjét. A cikk későbbi részében egy storage-fiókot hoz létre.

1. A függvény teszteléséhez állítson be egy töréspontot a függvény kódjában, majd nyomja le az F5 billentyűt a függvényalkalmazás-projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg. Ha ez az első alkalommal használja a Durable Functions, a Durable Functions bővítmény telepítve van, és a build néhány másodpercig is eltarthat.

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Illessze be a HTTP-kérelem URL-CÍMÉT a böngésző címsorában, és a vezénylési állapotának megtekintéséhez.

1. A hibakeresés leállításához nyomja le a Shift + F1 billentyűkombinációt.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a vágólapra a HTTP-eseményindító URL-címét az **Output** (Kimenet) panelről. A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

        http://<functionappname>.azurewebsites.net/api/<functionname>

1. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Az azonos állapotot Válasz való előtt szerezheti be a közzétett alkalmazás használatakor.

## <a name="next-steps"></a>További lépések

A Visual Studio Code használt létrehozása és közzététele egy JavaScript tartós függvényalkalmazást.

> [!div class="nextstepaction"]
> [Tartós függvény előforduló általános minták megismeréséhez](durable-functions-overview.md)