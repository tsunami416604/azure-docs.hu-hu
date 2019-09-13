---
title: Az első tartós funkció létrehozása az Azure-ban JavaScript használatával
description: Azure tartós függvény létrehozása és közzététele a Visual Studio Code használatával.
services: functions
documentationcenter: na
author: ColbyTresness
manager: jeconnoc
keywords: azure functions, függvények, eseményfeldolgozás, számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: glenga
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: b47e828f3b8d760594cb04ba40ceaa7248050c52
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933470"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Az első tartós függvény létrehozása a JavaScriptben

A *Durable Functions* [Azure functions](../functions-overview.md) , amely lehetővé teszi állapot-nyilvántartó függvények írására kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studio Code Azure Functions bővítményt a "Hello World" tartós funkciójának helyi létrehozására és tesztelésére.  Ez a függvény összehangolja és összekapcsolja a hívásokat más funkciókkal. Ezután közzéteheti a függvénykódot az Azure-ban.

![Tartós funkció futtatása az Azure-ban](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse a [Visual Studio Code](https://code.visualstudio.com/download)-ot.

* Győződjön meg arról, hogy rendelkezik a [Azure functions Core Tools](../functions-run-local.md)legújabb verziójával.

* Windows rendszerű számítógépen ellenőrizze, hogy az [Azure Storage-emulátor](../../storage/common/storage-use-emulator.md) telepítve van-e és fut-e. Mac vagy Linux rendszerű számítógépen a tényleges Azure Storage-fiókot kell használnia.

* Győződjön meg arról, hogy a 8,0-es vagy újabb verziójú [Node. js](https://nodejs.org/) -verzió van telepítve.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>A Durable Functions NPM-csomag telepítése

1. Telepítse a `durable-functions` NPM csomagot `npm install durable-functions` a Function alkalmazás gyökérkönyvtárában.

## <a name="creating-your-functions"></a>A függvények létrehozása

Most létrehozjuk a három olyan függvényt, amely a Durable Functions megkezdéséhez szükséges: egy HTTP Starter, egy Orchestrator és egy Activity függvény. A HTTP-kezdő elindítja a teljes megoldást, és a Orchestrator különböző tevékenységi funkciókra küldi a munkát.

### <a name="http-starter"></a>HTTP-kezdő

Először hozzon létre egy HTTP által aktivált függvényt, amely tartós függvény-előkészítést indít el.

1. Az *Azure-ból: Függvények*területen válassza a **create Function (függvény létrehozása** ) ikont.

    ![Függvény létrehozása](./media/quickstart-js-vscode/create-function.png)

2. Válassza ki a Function app Project nevű mappát, és válassza ki a **DURABLE FUNCTIONS http Starter** Function sablont.

    ![A HTTP Starter-sablon kiválasztása](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Hagyja meg az alapértelmezett nevet `DurableFunctionsHttpStart` , és nyomja meg a * * * * ENTER * * gombot, majd válassza a **Névtelen** hitelesítés lehetőséget.

    ![A névtelen hitelesítés kiválasztása](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Most létrehoztunk egy belépési pontot a tartós függvénynek. Vegyünk fel egy Orchestrator.

### <a name="orchestrator"></a>Orchestrator

Most létrehozunk egy Orchestrator a tevékenység-függvények koordinálásához.

1. Az *Azure-ból: Függvények*területen válassza a **create Function (függvény létrehozása** ) ikont.

    ![Függvény létrehozása](./media/quickstart-js-vscode/create-function.png)

2. Válassza ki a Function app Project nevű mappát, és válassza ki a **Durable functions Orchestrator** . Hagyja meg a nevet alapértelmezett "DurableFunctionsOrchestrator" néven.

    ![Orchestrator-sablon kiválasztása](./media/quickstart-js-vscode/create-function-choose-template.png)

Felvettünk egy Orchestrator a tevékenységi funkciók koordinálására. Most adjuk hozzá a hivatkozott tevékenység funkciót.

### <a name="activity"></a>Tevékenység

Most létrehozunk egy tevékenység-függvényt, amely ténylegesen elvégzi a megoldás munkáját.

1. Az *Azure-ból: Függvények*területen válassza a **create Function (függvény létrehozása** ) ikont.

    ![Függvény létrehozása](./media/quickstart-js-vscode/create-function.png)

2. Válassza ki a Function app Project nevű mappát, és válassza a **Durable functions Activity** függvény sablont. Hagyja meg a nevet alapértelmezett "Hello"-ként.

    ![A tevékenység sablonjának kiválasztása](./media/quickstart-js-vscode/create-function-choose-template.png)

Most már hozzáadta az összes olyan összetevőt, amely egy előkészítés és a lánc összevonása tevékenység funkcióinak elindításához szükséges.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studio Code-ból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

1. A Windows rendszerű számítógépen indítsa el az Azure Storage Emulatort, és győződjön meg arról, hogy a *Local. Settings. JSON* `UseDevelopmentStorage=true` **AzureWebJobsStorage** tulajdonsága a következőre van beállítva:.

    A Storage Emulator 5,8 esetén győződjön meg arról, hogy a local. Settings. JSON **AzureWebJobsSecretStorageType** tulajdonsága a következőre van beállítva `files`:. Mac vagy Linux rendszerű számítógépen a **AzureWebJobsStorage** tulajdonságot egy meglévő Azure Storage-fiókhoz tartozó kapcsolódási sztringre kell beállítania. Ebben a cikkben később létrehoz egy Storage-fiókot.

2. A függvény teszteléséhez állítson be egy töréspontot a függvény kódjában, majd nyomja le az F5 billentyűt a függvényalkalmazás-projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg. Ha első alkalommal használja a Durable Functions-t, a Durable Functions bővítmény telepítve lesz, és a Build eltarthat néhány másodpercig.

    > [!NOTE]
    > A JavaScript Durable Functions a **Microsoft. Azure. webjobs. Extensions. DurableTask** kiterjesztés **1.7.0** vagy újabb verzióját igényli. Futtassa a következő parancsot a Azure Functions alkalmazás gyökérkönyvtárában a Durable Functions bővítmény telepítéséhez`func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Cserélje le a `{functionName}` elemet a `DurableFunctionsOrchestrator` kérdésre.

5. Ha például a [Poster](https://www.getpostman.com/) vagy a [curl](https://curl.haxx.se/)eszközt használja, küldjön egy HTTP POST-kérelmet az URL-végpontnak.

   A válasz a HTTP-függvény kezdeti eredménye, amely közli, hogy a tartós összehangolás sikeresen elindult. Még nem az előkészítés végeredménye. A válasz több hasznos URL-címet is tartalmaz. Most pedig lekérdezjük a folyamat állapotát.

6. Másolja ki az URL- `statusQueryGetUri` címet, és illessze be a böngésző címsorába, majd hajtsa végre a kérelmet. Azt is megteheti, hogy továbbra is a Poster használatával adja ki a GET kérelmet.

   A kérelem lekérdezi az állapotot az előkészítési példányon. Egy végleges választ kap, amely megmutatja, hogy a példány befejeződik, és tartalmazza a tartós funkció kimeneteit vagy eredményét. A következőképpen néz ki: 

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

7. A hibakeresés leállításához nyomja le a **SHIFT + F5** billentyűkombinációt a vs Code-ban.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a vágólapra a HTTP-eseményindító URL-címét az **Output** (Kimenet) panelről. A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Ugyanezt az állapot-választ kell megadnia, mint korábban a közzétett alkalmazás használatakor.

## <a name="next-steps"></a>További lépések

A Visual Studio Code használatával JavaScript tartós Function-alkalmazást hozhat létre és tehet közzé.

> [!div class="nextstepaction"]
> [További tudnivalók a tartós függvények gyakori mintái](durable-functions-overview.md#application-patterns)