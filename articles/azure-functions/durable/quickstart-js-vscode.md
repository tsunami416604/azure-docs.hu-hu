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
ms.openlocfilehash: 91b61e88d876f481e74b8f2295b6fffced3f7902
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065515"
---
# <a name="create-your-first-durable-function-in-javascript"></a>A JavaScript tartós függvény létrehozása

*Durable Functions* kiterjesztése [Azure Functions](../functions-overview.md) , amellyel írási állapot-nyilvántartó functions egy kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

Ebből a cikkből elsajátíthatja az Azure Functions Visual Studio Code-bővítmény használata helyi létrehozásához és a egy "hello world" tartós függvény tesztelése.  Ez a függvény összehangolására, és a lánc együttesen hívások egyéb funkciók. Ezután közzéteheti a függvénykódot az Azure-ban.

![Azure-ban futó tartós függvény](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítés [Visual Studio Code](https://code.visualstudio.com/download).

* Ellenőrizze, hogy a legújabb verzióját a [Azure Functions Core Tools](../functions-run-local.md).

* A Windows-számítógépen ellenőrizze, hogy rendelkezik a [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) telepíteni és futtatni. A Mac vagy Linux rendszerű számítógépen egy tényleges Azure storage-fiókot kell használnia.

* Győződjön meg arról, hogy 8.0-s verzió vagy újabb verziója, [Node.js](https://nodejs.org/) telepítve.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Durable Functions npm-csomag telepítése

1. Telepítse a `durable-functions` npm-csomag futtatásával `npm install durable-functions` függvényalkalmazás gyökérkönyvtárában.

## <a name="creating-your-functions"></a>A függvények létrehozása

Most létrehozunk kell Durable Functions használatának első lépései a három funkciót: egy HTTP-alapkészlet, a vezénylő és a egy tevékenység függvényt. A HTTP-alapkészlet kezdeményez a teljes megoldást, és az orchestrator program feladatütemezőnk különböző tevékenységfüggvényeket.

### <a name="http-starter"></a>HTTP-alapkészlet

Először hozzon létre egy HTTP által aktivált függvényt, amely egy tartós függvény vezénylési elindítja.

1. A *Azure: Függvények*, válassza ki a **Create Function** ikonra.

    ![Függvény létrehozása](./media/quickstart-js-vscode/create-function.png)

2. Válassza ki a mappát a függvényalkalmazás projektjét, és válassza ki a **Durable Functions – HTTP-alapkészlet** függvénysablon.

    ![Az alapszintű HTTP-sablon kiválasztása](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Hagyja bejelölve az alapértelmezett nevet, `DurableFunctionsHttpStart` , és nyomja le az x Enter **, majd válassza ki **névtelen** hitelesítés.

    ![A névtelen hitelesítés kiválasztása](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Ezzel létrehoztunk egy belépési pont a tartós függvény. Adjunk hozzá egy orchestrator.

### <a name="orchestrator"></a>Az orchestrator

Most hozunk létre az orchestrator tevékenységfüggvényeket koordinálására.

1. A *Azure: Függvények*, válassza ki a **Create Function** ikonra.

    ![Függvény létrehozása](./media/quickstart-js-vscode/create-function.png)

2. Válassza ki a mappát a függvényalkalmazás projektjét, és válassza ki a **Durable Functions-vezénylő** függvénysablon. A név hagyja az alapértelmezett "DurableFunctionsOrchestrator"

    ![Az orchestrator-sablon kiválasztása](./media/quickstart-js-vscode/create-function-choose-template.png)

Hozzáadtunk egy orchestrator tevékenységfüggvényeket koordinálására. Most adjunk odkazované aktivity függvény.

### <a name="activity"></a>Tevékenység

Most ténylegesen végezze el a munkát a megoldás egy tevékenység-függvény hozunk létre.

1. A *Azure: Függvények*, válassza ki a **Create Function** ikonra.

    ![Függvény létrehozása](./media/quickstart-js-vscode/create-function.png)

2. Válassza ki a mappát a függvényalkalmazás projektjét, és válassza ki a **Durable Functions-tevékenység** függvénysablon. Hagyja meg a nevet az alapértelmezett "Hello".

    ![A tevékenység sablonjának kiválasztása](./media/quickstart-js-vscode/create-function-choose-template.png)

Hozzáadtunk egy vezénylési és lánc együttesen tevékenységfüggvényeket indulhat szükséges összes összetevő most.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studio Code-ból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

1. A Windows-számítógépen indítsa el az Azure Storage Emulator, és ellenőrizze, hogy a **AzureWebJobsStorage** tulajdonsága *local.settings.json* értékre van állítva `UseDevelopmentStorage=true`.

    A Storage Emulator 5.8 győződjön meg arról, hogy a **AzureWebJobsSecretStorageType** local.settings.json tulajdonságának értéke `files`. A Mac vagy Linux rendszerű számítógépen kell állítani a **AzureWebJobsStorage** tulajdonságát egy meglévő Azure-tárfiók kapcsolati sztringjét. A cikk későbbi részében egy storage-fiókot hoz létre.

2. A függvény teszteléséhez állítson be egy töréspontot a függvény kódjában, majd nyomja le az F5 billentyűt a függvényalkalmazás-projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg. Ha ez az első alkalommal használja a Durable Functions, a Durable Functions bővítmény telepítve van, és a build néhány másodpercig is eltarthat.

    > [!NOTE]
    > JavaScript-Durable Functions-verzió megkövetelése **1.7.0-ás** vagy nagyobb, mint a **Microsoft.Azure.WebJobs.Extensions.DurableTask** bővítmény. Futtassa a következő parancsot a abból a gyökérmappából, az Azure Functions-alkalmazás a Durable Functions bővítmény telepítése `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Cserélje le a `{functionName}` elemet a `DurableFunctionsOrchestrator` kérdésre.

5. Hasonló eszköz használatával [Postman](https://www.getpostman.com/) vagy [cURL](https://curl.haxx.se/), HTTP POST-kérelmet küldeni az URL-végpontot.

   A válasz, a HTTP-függvény, arról a tartós vezénylési kezdeti eredmény sikeresen elindult. Ez még nem áll a vezénylési végeredményét. A válasz néhány hasznos URL-címeket tartalmazza. Most tegyük a vezénylési állapotának lekérdezése.

6. Másolja az URL-cím értéke `statusQueryGetUri` , és illessze be a böngésző címsorában, és hajtsa végre a kérést. Másik megoldásként is folytathatja a GET kérés kiadása a Postman használatával.

   A kérelem lekérdezi az orchestration-példány állapota. A végleges választ, amely megmutatja a példány befejeződött, és tartalmazza a kimenetek vagy a tartós függvény eredményét szerezheti be. Hasonlóan néz ki: 

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
