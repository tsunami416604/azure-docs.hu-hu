---
title: Az első függvény létrehozása az Azure-ban a Visual Studio Code használatával
description: Hozzon létre és tegyen közzé az Azure-on egy egyszerű, HTTP-eseményindítót használó függvényt az Azure Functions-bővítmény használatával a Visual Studio Code-ban.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, függvények, eseményfeldolgozás, számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.topic: quickstart
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 52f682f7c7f06056be122b33d27592a55a01be94
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70744068"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Az első függvény létrehozása a Visual Studio Code használatával

Az Azure Functions lehetővé teszi a kód [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) környezetben történő végrehajtását anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást.

Ebben a cikkben megtudhatja, hogy a [Azure Functions-bővítmény a Visual Studio Code-hoz] használatával miként hozhat létre és tesztelhet a helyi számítógépén egy „Helló világ!”-függvényt a Microsoft Visual Studio Code használatával. Ezután közzéteheti a függvénykódot az Azure-ban a Visual Studio Code-ból.

![Azure-függvénykód Visual Studio-projektben](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

A bővítmény jelenleg a C#JavaScript, a Java és a Python függvényeket támogatja. A cikkben ismertetett lépések és az azt követő cikk csak a JavaScriptet C# és a függvényeket támogatja. Ha szeretné megtudni, hogyan hozhat létre és tehet közzé Python-függvényeket a Visual Studio Code használatával, tekintse meg a következőt: a [Python telepítése Azure functions](https://code.visualstudio.com/docs/python/tutorial-azure-functions). Ha szeretné megtudni, hogyan hozhat létre és tehet közzé PowerShell-funkciókat a Visual Studio Code használatával, tekintse meg [az első PowerShell-függvény létrehozása az Azure-ban](functions-create-first-function-powershell.md)című témakört. 

A bővítmény jelenleg előzetes verzióként érhető el. További tudnivalókért tekintse meg az [Azure Functions-bővítmény a Visual Studio Code-hoz] bővítmény oldalát.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* Telepítse a [Visual Studio Code-ot](https://code.visualstudio.com/) a [támogatott platformok](https://code.visualstudio.com/docs/supporting/requirements#_platforms) egyikén.

* Telepítse a [Azure functions Core Tools](functions-run-local.md#v2)2. x verzióját.

* Telepítse az Ön által választott nyelvhez tartozó követelményeket:

    | Nyelv | Követelmény |
    | -------- | --------- |
    | **C#** | [C#kiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Aktív LTS-és karbantartási LTS-verziók (8.11.1 és 10.14.1 ajánlott).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>A függvény futtatása az Azure-ban

1. Másolja a vágólapra a HTTP-eseményindító URL-címét az **Output** (Kimenet) panelről. Ez az URL-cím tartalmazza a függvény kulcsát, amelyet `code` a rendszer a lekérdezési paraméternek továbbít. Ahogyan korábban, most is az URL-cím végéhez adja hozzá a `?name=<yourname>` lekérdezési sztringet, és hajtsa végre a kérelmet.

    A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Az alábbiakban látható a böngészőben a távoli GET kérelemre a függvény által visszaadott válasz: 

    ![A függvény által visszaadott válasz a böngészőben](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>További lépések

A Visual Studio Code segítéségével létrehozott egy egyszerű, HTTP-eseményindítóval aktivált függvényt tartalmazó függvényalkalmazást. A következő cikkben kibonthatja ezt a függvényt egy kimeneti kötés hozzáadásával. Ez a kötés a HTTP-kérelemből írja be a karakterláncot egy Azure Queue Storage-várólistában lévő üzenetbe. A következő cikk azt is bemutatja, hogyan távolíthatja el ezeket az új Azure-erőforrásokat a létrehozott erőforráscsoport eltávolításával.

> [!div class="nextstepaction"]
> [Azure Storage-várólista kötésének hozzáadása a függvényhez](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions-bővítmény a Visual Studio Code-hoz]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
