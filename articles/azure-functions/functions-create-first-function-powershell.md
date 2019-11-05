---
title: Az első PowerShell-függvény létrehozása az Azure-ban
description: Ismerje meg, hogyan hozhatja létre az első PowerShell-függvényt az Azure-ban a Visual Studio Code használatával.
author: joeyaiello
manager: gwallace
ms.author: jaiello
ms.reviewer: glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.openlocfilehash: 1d6d641e141862b12fed40b800589aad70af2789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469405"
---
# <a name="create-your-first-powershell-function-in-azure"></a>Az első PowerShell-függvény létrehozása az Azure-ban

Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja létre az első [kiszolgáló](https://azure.com/serverless) nélküli PowerShell-függvényt a Visual Studio Code használatával.

![Kód Azure Functions egy Visual Studio Code-projektben](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

A [Azure Functions-bővítmény a Visual Studio Code-hoz] -hoz készült Azure functions-bővítmény használatával helyileg hozhat létre egy PowerShell-függvényt, majd üzembe helyezheti azt egy új Azure-beli Function alkalmazásban. A bővítmény jelenleg előzetes verzióként érhető el. További tudnivalókért tekintse meg az [Azure Functions-bővítmény a Visual Studio Code-hoz] bővítmény oldalát.

> [!NOTE]  
> Alapértelmezés szerint a PowerShell-támogatás a Visual Studio Code [Azure functions-bővítményhez][azure functions-bővítmény a visual studio code-hoz] jelenleg le van tiltva. A PowerShell-támogatás engedélyezése a cikkben ismertetett lépések egyike.

A következő lépések támogatottak a macOS, a Windows és a Linux rendszerű operációs rendszereken.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* A [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows) telepítése

* Telepítse a [Visual Studio Code-ot](https://code.visualstudio.com/) a [támogatott platformok](https://code.visualstudio.com/docs/supporting/requirements#_platforms) egyikén. 

* Telepítse [a PowerShell-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)-hoz.

* Telepítse a [.net Core SDK 2.2 +](https://www.microsoft.com/net/download) (a Azure functions Core Tools által megkövetelt és az összes támogatott platformon elérhető).

* Telepítse a [Azure functions Core Tools](functions-run-local.md#v2)2. x verzióját.

* Aktív Azure-előfizetésre is szüksége van.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Függvényalkalmazás-projekt létrehozása

A Visual Studio Code Azure Functions projektsablonja egy olyan projektet hoz létre, amely közzétehető egy Azure-függvényalkalmazásban. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások felügyelete, üzembe helyezése és megosztása érdekében. 

1. A Visual Studio Code-ban, válassza az Azure logót az **Azure: Functions** terület megjelenítéséhez, majd válassza a Create New Project (Új projekt létrehozása) ikont.

    ![Függvényalkalmazás-projekt létrehozása](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Válasszon egy helyet a functions projekt munkaterülethez, és válassza a **kiválasztás**lehetőséget.

    > [!NOTE]
    > Ez a cikk munkaterületen kívüli teljesítésre lett tervezve. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Válassza ki a **PowerShellt** a Function app-projekt nyelve, majd **Azure functions v2**.

1. Válassza ki a **http-triggert** az első függvény sablonja, használja a `HTTPTrigger` nevet, és válassza ki a **függvény**engedélyezési szintjét.

    > [!NOTE]
    > A **függvény engedélyezési szintjének** függvény [kulcsának](functions-bindings-http-webhook.md#authorization-keys) értékét kell megadnia a függvény végpontjának az Azure-ban való meghívásakor. Ez megnehezíti a függvény meghívását.

1. Amikor a program kéri, válassza az **Add to workspace** (Hozzáadás munkaterülethez) lehetőséget.

A Visual Studio Code létrehozza a PowerShell-függvény alkalmazás projektjét egy új munkaterületen. Ez a projekt tartalmazza a [Host. JSON](functions-host-json.md) és a [Local. Settings. JSON](functions-run-local.md#local-settings-file) konfigurációs fájlokat, amelyek a projekt összes függvényére érvényesek. Ez a [PowerShell-projekt](functions-reference-powershell.md#folder-structure) ugyanaz, mint az Azure-ban futó Function alkalmazás.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A Azure Functions Core Tools a Visual Studio Code-ban integrálva lehetővé teszi egy Azure Functions-projekt helyi futtatását és hibakeresését.  

1. A függvény hibakereséséhez helyezzen be egy hívást az [`Wait-Debugger`] parancsmaghoz a függvény kódjában, mielőtt csatolni szeretné a hibakeresőt, majd nyomja le az F5 billentyűt a Function app projekt elindításához, és csatolja a hibakeresőt. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. Fűzze hozzá a lekérdezési karakterláncot `?name=<yourname>` ehhez az URL-címhez, majd a `Invoke-RestMethod` használatával hajtsa végre a kérést, a következőképpen:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    A GET kérést egy böngészőből is végrehajthatja.

    Ha a HttpTrigger-végpontot úgy hívja meg, hogy `name` paramétert nem egy lekérdezési paraméterként vagy a törzsben adja át, a függvény egy [HttpStatusCode]:: BadRequest hibát ad vissza. Amikor a Run. ps1 kódban áttekinti a kódot, láthatja, hogy ez a hiba a tervezés szerint történik.

1. A hibakeresés leállításához nyomja le a Shift + F5 billentyűkombinációt.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

> [!NOTE]
> A függvények Azure-ba való közzététele előtt ne felejtse el eltávolítani a `Wait-Debugger` hívásokat. 
>
> A Function alkalmazás Azure-beli létrehozása csak a Function alkalmazás nevének megadását kéri. Más értékek is meg vannak határozva.
> Állítsa be `azureFunctions.advancedCreation`, hogy `true`, hogy a rendszer az összes többi értéket kéri.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>A függvény futtatása az Azure-ban

Annak ellenőrzéséhez, hogy a közzétett függvény fut-e az Azure-ban, hajtsa végre a következő PowerShell-parancsot, és cserélje le a `Uri` paramétert az előző lépésben a HTTPTrigger függvény URL-címére. Ahogy korábban is, fűzze hozzá a lekérdezési karakterláncot `&name=<yourname>` az URL-címhez, ahogy az alábbi példában is látható:

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

A Visual Studio Code használatával egy egyszerű HTTP-triggerrel aktivált függvény használatával hozhat létre egy PowerShell-függvény alkalmazást. Érdemes lehet többet megtudni a [PowerShell-függvények helyi hibakereséséről](functions-debug-powershell-local.md) a Azure functions Core Tools használatával. Tekintse meg a [Azure functions PowerShell fejlesztői útmutatóját](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Application Insights-integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions-bővítmény a Visual Studio Code-hoz]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
["WAIT-Debugger"]: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
