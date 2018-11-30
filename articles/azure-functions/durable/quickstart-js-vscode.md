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
ms.openlocfilehash: 114278b2d53cab8dd3302a8a2c2c074b8a5b24fa
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52638580"
---
# <a name="create-your-first-durable-function-in-javascript"></a>A JavaScript tartós függvény létrehozása

*Durable Functions* kiterjesztése [Azure Functions](../functions-overview.md) , amellyel írási állapot-nyilvántartó functions egy kiszolgáló nélküli környezetben. A bővítmény kezeli a állapot, ellenőrzőpontok és újraindul az Ön számára.

Ebből a cikkből elsajátíthatja az Azure Functions Visual Studio Code-bővítmény használata helyi létrehozásához és a egy "hello world" tartós függvény tesztelése.  Ez a függvény összehangolására, és a lánc együttesen hívások egyéb funkciók. Ezután közzéteheti a függvénykódot az Azure-ban.

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

1. Az **Azure: Functions** menüben válassza a függvény létrehozására szolgáló ikont.

    ![Függvény létrehozása](../media/functions-create-first-function-vs-code/create-function.png)

1. Válassza ki a függvényalkalmazás-projektet tartalmazó mappát, majd válassza a **HTTP-trigger** (HTTP-eseményindító) függvénysablont.

    ![A HTTP-eseményindító sablon kiválasztása](../media/functions-create-first-function-vs-code/create-function-choose-template.png)

1. A függvénynek adja a `HttpStart` nevet, nyomja le az Enter billentyűt, majd válassza az **Anonymous** (Névtelen) hitelesítést.

    ![A névtelen hitelesítés kiválasztása](../media/functions-create-first-function-vs-code/create-function-anonymous-auth.png)

    A rendszer létrehoz egy függvényt a választott nyelven a HTTP által indított függvények sablonjának használatával.

    ![A HTTP által indított függvények sablonja a Visual Studio Code-ban](../media/functions-create-first-function-vs-code/new-function-full.png)

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

1. Az **Azure: Functions** menüben válassza a függvény létrehozására szolgáló ikont.

    ![Függvény létrehozása](../media/functions-create-first-function-vs-code/create-function.png)

1. Válassza ki a függvényalkalmazás-projektet tartalmazó mappát, majd válassza a **HTTP-trigger** (HTTP-eseményindító) függvénysablont. Mivel ez a kód fogja cserélni, maga az eseményindító nem számít.

    ![A HTTP-eseményindító sablon kiválasztása](../media/functions-create-first-function-vs-code/create-function-choose-template.png)

1. A függvénynek adja a `OrchestratorFunction` nevet, nyomja le az Enter billentyűt, majd válassza az **Anonymous** (Névtelen) hitelesítést.

    ![A névtelen hitelesítés kiválasztása](../media/functions-create-first-function-vs-code/create-function-anonymous-auth.png)

1. Cserélje le az index.js a JavaScript alatt:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

1. Cserélje le a function.json az alábbi JSON-ban:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Hozzáadtunk egy orchestrator tevékenységfüggvényeket koordinálására. Most adjunk odkazované aktivity függvény.

## <a name="create-an-activity-function"></a>Egy tevékenység függvény létrehozása

1. Az **Azure: Functions** menüben válassza a függvény létrehozására szolgáló ikont.

    ![Függvény létrehozása](../media/functions-create-first-function-vs-code/create-function.png)

1. Válassza ki a függvényalkalmazás-projektet tartalmazó mappát, majd válassza a **HTTP-trigger** (HTTP-eseményindító) függvénysablont. Mivel ez a kód fogja cserélni, maga az eseményindító nem számít.

    ![A HTTP-eseményindító sablon kiválasztása](../media/functions-create-first-function-vs-code/create-function-choose-template.png)

1. A függvénynek adja a `SayHello` nevet, nyomja le az Enter billentyűt, majd válassza az **Anonymous** (Névtelen) hitelesítést.

    ![A névtelen hitelesítés kiválasztása](../media/functions-create-first-function-vs-code/create-function-anonymous-auth.png)

1. Cserélje le az index.js a JavaScript alatt:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

1. Cserélje le a function.json az alábbi JSON-ban:

    [!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

Lehetőségekkel bővült a vezénylési és a lánc együttesen tevékenységfüggvényeket indításához szükséges összes összetevőt.

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