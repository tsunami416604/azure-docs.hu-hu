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
ms.openlocfilehash: eade9f4e2a956a6542b69e93b0102169ddd32ccf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281233"
---
# <a name="create-your-first-durable-function-in-javascript"></a>A JavaScript tartós függvény létrehozása

*Durable Functions* kiterjesztése [Azure Functions](../functions-overview.md) , amellyel írási állapot-nyilvántartó functions egy kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

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

## <a name="install-the-durable-functions-npm-package"></a>Durable Functions npm-csomag telepítése

1. Telepítse a `durable-functions` npm-csomag futtatásával `npm install durable-functions` függvényalkalmazás gyökérkönyvtárában.

## <a name="create-a-starter-function"></a>Alapszintű függvény létrehozása

Először hozzon létre egy HTTP által aktivált függvényt, amely egy tartós függvény vezénylési elindítja.

1. A **Azure: Függvények**, kattintson a Create Function ikonra.

    ![Függvény létrehozása](./media/quickstart-js-vscode/create-function.png)

2. Válassza ki a függvényalkalmazás-projektet tartalmazó mappát, majd válassza a **HTTP-trigger** (HTTP-eseményindító) függvénysablont.

    ![A HTTP-eseményindító sablon kiválasztása](./media/quickstart-js-vscode/create-function-choose-template.png)

3. A függvénynek adja a `HttpStart` nevet, nyomja le az Enter billentyűt, majd válassza az **Anonymous** (Névtelen) hitelesítést.

    ![A névtelen hitelesítés kiválasztása](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

    A rendszer létrehoz egy függvényt a választott nyelven a HTTP által indított függvények sablonjának használatával.

4. Cserélje le az index.js a JavaScript alatt:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

5. Cserélje le a function.json az alábbi JSON-ban:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Ezzel létrehoztunk egy belépési pont a tartós függvény. Adjunk hozzá egy orchestrator.

## <a name="create-an-orchestrator-function"></a>Az Orchestrator-függvény létrehozása

Ezután hozzon létre egy másik függvényt az orchestrator lennie. A HTTP-eseményindító sablonjához függvényt használjuk kényelmi célokat szolgál. Magát a függvénykódot az orchestrator kódot váltja fel.

1. Ismételje meg az előző szakaszban hozhat létre egy másik függvény használatával a HTTP-eseményindító sablonjához. Most adja a függvénynek `OrchestratorFunction`.

2. Nyissa meg az új függvény az index.js fájlt, és cserélje ki annak tartalmát az alábbira:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

3. Nyissa meg a function.json fájlt, és cserélje le a következő JSON-ra:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Hozzáadtunk egy orchestrator tevékenységfüggvényeket koordinálására. Most adjunk odkazované aktivity függvény.

## <a name="create-an-activity-function"></a>Egy tevékenység függvény létrehozása

1. Ismételje meg a használatával a HTTP-eseményindító sablonjához harmadik függvény létrehozásához az előző szakaszokban. De ezúttal adja a függvénynek `E1_SayHello`.

2. Nyissa meg az új függvény az index.js fájlt, és cserélje ki annak tartalmát az alábbira:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

3. Cserélje le a function.json az alábbi JSON-ban:

    [!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

Hozzáadtunk egy vezénylési és lánc együttesen tevékenységfüggvényeket indulhat szükséges összes összetevő most.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studio Code-ból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.  

1. A Windows-számítógépen indítsa el az Azure Storage Emulator, és ellenőrizze, hogy a **AzureWebJobsStorage** local.settings.json tulajdonságának értéke `UseDevelopmentStorage=true`. 

    A Storage Emulator 5.8 győződjön meg arról, hogy a **AzureWebJobsSecretStorageType** local.settings.json tulajdonságának értéke `files`. A Mac vagy Linux rendszerű számítógépen kell állítani a **AzureWebJobsStorage** tulajdonságát egy meglévő Azure-tárfiók kapcsolati sztringjét. A cikk későbbi részében egy storage-fiókot hoz létre.

2. A függvény teszteléséhez állítson be egy töréspontot a függvény kódjában, majd nyomja le az F5 billentyűt a függvényalkalmazás-projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg. Ha ez az első alkalommal használja a Durable Functions, a Durable Functions bővítmény telepítve van, és a build néhány másodpercig is eltarthat.

    > [!NOTE]
    > JavaScript-Durable Functions-verzió megkövetelése **1.7.0-ás** vagy nagyobb, mint a **Microsoft.Azure.WebJobs.Extensions.DurableTask** bővítmény. Ellenőrizze a Durable Functions bővítmény verzióját a `extensions.csproj` fájl megfelel a követelményeknek. Ha nem jelenik meg, állítsa le a függvényalkalmazás, módosítsa a verzióját, és nyomja le az F5 billentyűt, indítsa újra a függvényalkalmazás.

3. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Cserélje le a `{functionName}` elemet a `OrchestratorFunction` kérdésre.

5. Hasonló eszköz használatával [Postman](https://www.getpostman.com/) vagy [cURL](https://curl.haxx.se/), egy HTTP POST kérést küldhet az URL-végpontot.

   A válasz, a HTTP-függvény, arról a tartós vezénylési kezdeti eredmény sikeresen elindult. Ez még nem áll a vezénylési végeredményét. A válasz néhány hasznos URL-címeket tartalmazza. Most tegyük a vezénylési állapotának lekérdezése.

6. Másolja az URL-cím értéke `statusQueryGetUri` , és illessze be a böngésző címsorában, és hajtsa végre a kérést. Másik megoldásként is folytathatja a GET kérés kiadása a Postman használatával.

   A kérelem lekérdezi az orchestration-példány állapota. Végleges választ, amely megmutatja a példány befejeződött, és tartalmazza a kimenetek vagy a tartós függvény eredményét szerezheti be. Hasonlóan néz ki: 

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

7. Hibakeresés leállításához nyomja le az ENTER **Shift + F5** a VS Code-ban.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a vágólapra a HTTP-eseményindító URL-címét az **Output** (Kimenet) panelről. A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Az azonos állapotot Válasz való előtt szerezheti be a közzétett alkalmazás használatakor.

## <a name="next-steps"></a>További lépések

A Visual Studio Code használt létrehozása és közzététele egy JavaScript tartós függvényalkalmazást.

> [!div class="nextstepaction"]
> [Tartós függvény előforduló általános minták megismeréséhez](durable-functions-concepts.md)
