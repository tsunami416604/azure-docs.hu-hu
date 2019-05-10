---
title: Az első PowerShell-függvény létrehozása az Azure Functions használatával
description: Útmutató az első PowerShell-függvény létrehozása az Azure-ban a Visual Studio Code-ot.
services: functions
keywords: ''
author: joeyaiello
manager: jeconnoc
ms.author: jaiello, glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.devlang: powershell
ms.openlocfilehash: 24640c9013f7a5b81cd5b1b6f45de49c5baad0e6
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473274"
---
# <a name="create-your-first-powershell-function-in-azure-preview"></a>Az első PowerShell-függvény létrehozása az Azure-ban (előzetes verzió)

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Ez a rövid útmutató a cikk bemutatja, hogyan hozhat létre az első [kiszolgáló nélküli](https://azure.com/serverless) PowerShell függvény használatával a Visual Studio Code-ot.

![Az Azure Functions-kódhoz a Visual Studio Code-projektben](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

Használja a [Azure Functions-bővítmény a Visual Studio Code-hoz] helyileg egy PowerShell-függvény létrehozása és majd üzembe helyezte azt egy új függvényalkalmazáshoz az Azure-ban. A bővítmény jelenleg előzetes verzióként érhető el. További tudnivalókért tekintse meg az [Azure Functions-bővítmény a Visual Studio Code-hoz] bővítmény oldalát.

> [!NOTE]  
> PowerShell-támogatás a [Azure Functions bővítmény][azure functions-bővítmény a visual studio code-hoz] jelenleg le van tiltva alapértelmezés szerint. PowerShell-támogatás engedélyezésével egyike, ez a cikk lépéseit.

Az alábbi lépéseket a macOS, Windows és Linux-alapú operációs rendszerek támogatottak.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* Telepítés [a PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core)

* Telepítse a [Visual Studio Code-ot](https://code.visualstudio.com/) a [támogatott platformok](https://code.visualstudio.com/docs/supporting/requirements#_platforms) egyikén. 

* Telepítés [PowerShell-bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Telepítés [.NET Core SDK 2.2 +](https://www.microsoft.com/net/download) (Azure Functions Core Tools szükséges és elérhető az összes támogatott platformon).

* Verzió telepítése 2.x verzióját a [Azure Functions Core Tools](functions-run-local.md#v2).

* Emellett egy aktív Azure-előfizetéssel.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Függvényalkalmazás-projekt létrehozása

A Visual Studio Code Azure Functions projektsablonja egy olyan projektet hoz létre, amely közzétehető egy Azure-függvényalkalmazásban. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások felügyelete, üzembe helyezése és megosztása érdekében. 

1. A Visual Studio Code, válassza ki az Azure embléma megjelenítése a **Azure: Függvények** területre, és válassza az új projekt létrehozása ikonra.

    ![Függvényalkalmazás-projekt létrehozása](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Válasszon egy helyet, a Functions-projekt munkaterület, és válassza a **kiválasztása**.

    > [!NOTE]
    > Ez a cikk munkaterületen kívüli teljesítésre lett tervezve. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Válassza ki a **Powershell (előzetes verzió)** , a függvényalkalmazás projektjét a nyelvet, majd **Azure Functions v2**.

1. Válassza a **HTTP-eseményindító** az első függvény sablonként használni `HTTPTrigger` , a függvény neve, és válassza ki egy engedélyezési szintet **függvény**.

    > [!NOTE]
    > A **függvény** jogosultsági szint szükséges egy [függvénykulcs](functions-bindings-http-webhook.md#authorization-keys) értéke, ha a függvény-végpontot hív-e az Azure-ban. Ez megnehezíti az a függvény hívása csak mindenki számára.

1. Amikor a program kéri, válassza az **Add to workspace** (Hozzáadás munkaterülethez) lehetőséget.

A Visual Studio Code egy új munkaterületet hoz létre a PowerShell függvényalkalmazás projektjét. Ez a projekt tartalmazza a [host.json](functions-host-json.md) és [local.settings.json](functions-run-local.md#local-settings-file) konfigurációs fájlokat, amelyek minden függvény a projekt a alkalmazni. Ez [PowerShell projekt](functions-reference-powershell.md#folder-structure) ugyanaz, mint egy Azure-ban futó függvényalkalmazást.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Az Azure Functions Core Tools lehetővé teszi, hogy futtatását és az Azure Functions-projekt helyi hibakeresését a Visual Studio Code integrálható.  

1. Hibakeresése a függvényt, helyezze be a hívást a [ `Wait-Debugger` ] előtt csatolja a hibakeresőt a függvénykódot a parancsmag nyomja le az F5 indítsa el a függvényalkalmazás projektjét, és társítsa a hibakeresőt. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. A lekérdezési karakterlánc hozzáfűzése `?name=<yourname>` URL-címet, és a majd `Invoke-RestMethod` a kérelem végrehajtása a következő:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Egy böngészőből is a GET-kérés hajthat végre.

    A HttpTrigger végpont nélkül megadásának hívható egy `name` lekérdezési paramétert, vagy a törzsben paraméter, a függvény visszaadja az 500-as hiba. Amikor a kód run.ps1, láthatja, hogy ez a hiba a kialakítás miatt jelentkezik.

1. A hibakeresés leállításához nyomja le a Shift + F5 billentyűkombinációt.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

> [!NOTE]
> Ne felejtse el eltávolítani a beléptetés-visszavonásokat `Wait-Debugger` mielőtt közzéteszi a függvények az Azure-bA. 

> [!NOTE]
> Függvényalkalmazás létrehozása az Azure-ban csak akkor kéri a Függvényalkalmazás neve. AzureFunctions.advancedCreation kérni az összes többi értéket Igaz értékre van állítva.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>A függvény futtatása az Azure-ban

Győződjön meg arról, hogy fut-e a közzétett függvényhez az Azure-ban, hajtsa végre a következő PowerShell-parancsot, és cserélje le a `Uri` az előző lépésben a HTTPTrigger függvény URL-paraméter. Mint korábban, fűzze hozzá a lekérdezési karakterlánc `&name=<yourname>` az URL-címhez, akkor az alábbi példában látható módon:

```powershell
PS > Invoke-WebRequest -Method Get -Uri "https://glengatest-vscode-powershell.azurewebsites.net/api/HttpTrigger?code=nrY05eZutfPqLo0som...&name=PowerShell"

StatusCode        : 200
StatusDescription : OK
Content           : Hello PowerShell
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 16
                    Content-Type: text/plain; charset=utf-8
                    Date: Thu, 25 Apr 2019 16:01:22 GMT

                    Hello PowerShell
Forms             : {}
Headers           : {[Content-Length, 16], [Content-Type, text/plain; charset=utf-8], [Date, Thu, 25 Apr 2019 16:01:22 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 16
```

## <a name="next-steps"></a>További lépések

A Visual Studio Code segítségével hozzon létre egy PowerShell-függvényalkalmazást egy egyszerű HTTP-eseményindítóval aktivált függvényt. További információk is érdemes [egy PowerShell-függvény helyi hibakeresése](functions-debug-powershell-local.md) az Azure Functions Core Tools használatával. Tekintse meg a [fejlesztői útmutató az Azure Functions PowerShell](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Az Application Insights-integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions-bővítmény a Visual Studio Code-hoz]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[`Wait-Debugger`]: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
