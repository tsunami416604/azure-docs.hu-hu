---
title: PowerShell-függvény létrehozása a Visual Studio Code-Azure Functions használatával
description: Ismerje meg, hogyan hozhat létre PowerShell-függvényeket, és hogyan teheti közzé a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetésére a Visual Studio Code Azure Functions bővítménnyel.
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: 585339f87c89b0fa5a0f50dbef155b0dcffda68e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425034"
---
# <a name="quickstart-create-a-powershell-function-in-azure-using-visual-studio-code"></a>Rövid útmutató: PowerShell-függvény létrehozása az Azure-ban a Visual Studio Code használatával

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Ebben a cikkben a Visual Studio Code használatával hozzon létre egy PowerShell-függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

Ennek a cikknek a [CLI-alapú verziója](create-first-function-cli-powershell.md) is létezik.

## <a name="configure-your-environment"></a>A környezet konfigurálása

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő követelményekkel:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js](https://nodejs.org/), amelyet a Windows a NPM igényel. Csak [az aktív LTS-és karbantartási LTS-verziók](https://nodejs.org/about/releases/). A `node --version` parancs használatával vizsgálja meg a verziót.
    A macOS és Linux rendszereken a helyi fejlesztéshez nem szükséges.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows)

+ Mind a [.net core 3,1](https://www.microsoft.com/net/download) , mind a [.net Core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.2)  

+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).    

+ A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)-hoz készült PowerShell-bővítmény.  

+ A Visual Studio Code [Azure functions bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) . 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása

Ebben a szakaszban a Visual Studio Code használatával hozzon létre egy helyi Azure Functions projektet a PowerShellben. A cikk későbbi részében a függvény kódját közzé fogja tenni az Azure-ban.

1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **új projekt létrehozása...** ikont.

    ![Válassza az új projekt létrehozása lehetőséget.](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Válasszon egy címtárbeli helyet a projekt munkaterülethez, és válassza a **kiválasztás** lehetőséget.

    > [!NOTE]
    > Ezeket a lépéseket úgy tervezték, hogy a munkaterületen kívül is el lehessen végezni. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Adja meg a következő információkat a kérdésekben:

    + **Válasszon nyelvet a függvény projekthez** : válassza a lehetőséget `PowerShell` .

    + **Válasszon sablont a projekt első függvényéhez** : válassza a lehetőséget `HTTP trigger` .

    + **Adja meg a függvény nevét** : Type `HttpExample` .

    + **Engedélyezési szint** : válassza `Anonymous` a lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját. Az engedélyezési szint megismeréséhez tekintse meg az [engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys)című témakört.

    + **Válassza ki, hogyan szeretné megnyitni a projektet** : válassza a lehetőséget `Add to workspace` .

1. Ezen információk használatával a Visual Studio Code egy Azure Functions projektet hoz létre egy HTTP-triggerrel. A helyi projektfájlok a Explorerben tekinthetők meg. További információ a létrehozott fájlokról: [generált projektfájlok](functions-develop-vs-code.md#generated-project-files). 

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

Miután meggyőződött róla, hogy a függvény megfelelően fut a helyi számítógépen, ideje, hogy a Visual Studio Code használatával tegye közzé a projektet közvetlenül az Azure-ban. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>További lépések

A Visual Studio Code segítéségével létrehozott egy egyszerű, HTTP-eseményindítóval aktivált függvényt tartalmazó függvényalkalmazást. A következő cikkben kibonthatja ezt a függvényt egy kimeneti kötés hozzáadásával. Ez a kötés a HTTP-kérelemből írja be a karakterláncot egy Azure Queue Storage-várólistában lévő üzenetbe. 

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-powershell)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
