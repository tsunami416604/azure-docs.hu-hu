---
title: Az első függvény létrehozása az Azure-ban a Visual Studio Code használatával
description: Hozzon létre és tegyen közzé az Azure-on egy egyszerű, HTTP-eseményindítót használó függvényt az Azure Functions-bővítmény használatával a Visual Studio Code-ban.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 6f1c211a8110d95adb5e6802313c5b7deafe3864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276461"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Rövid útmutató: Azure Functions-projekt létrehozása a Visual Studio-kód használatával

Ebben a cikkben a Visual Studio-kód használatával hozzon létre egy függvényt, amely válaszol a HTTP-kérésekre. Miután helyileg tesztelte a kódot, üzembe helyezi azt az Azure Functions kiszolgáló nélküli környezetében. A rövid útmutató végrehajtása néhány USD centvagy annál kevesebb költséget jelent az Azure-fiókjában. 

Van is egy [CLI-alapú változata](functions-create-first-azure-function-azure-cli.md) ezt a cikket.

## <a name="configure-your-environment"></a>A környezet konfigurálása

Mielőtt elkezdene, győződjön meg arról, hogy a következő követelmények vannak érvényben:

+ Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"  
+ [Node.js](https://nodejs.org/), a Windows által megkövetelt npm. Csak [az aktív LTS- és karbantartási LTS-verziók. ](https://nodejs.org/about/releases/) Használja `npm --version` a parancsot, hogy ellenőrizze a verziót.
    Nem szükséges a helyi fejlesztés MacOS és Linux.   
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
+ [Node.js](https://nodejs.org/), Aktív LTS- és karbantartási LTS-verziók (10.14.1 ajánlott). Használja `npm --version` a parancsot, hogy ellenőrizze a verziót.
::: zone-end 
::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-381/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/) az Azure Functions (x64) támogatja.
::: zone-end   
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ A [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)  
::: zone-end  
+ [Visual Studio-kód](https://code.visualstudio.com/) a [támogatott platformok egyikén.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)  
::: zone pivot="programming-language-csharp"  
+ A Visual Studio-kód [C# kiterjesztése.](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)  
::: zone-end  
::: zone pivot="programming-language-python"
+ A Visual Studio-kód [Python-bővítménye.](https://marketplace.visualstudio.com/items?itemName=ms-python.python)  
::: zone-end  
::: zone pivot="programming-language-powershell"
+ A [Visual Studio-kód PowerShell-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  
::: zone-end  

+ A Visual Studio-kód [Azure Functions bővítménye.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása 

Ebben a szakaszban a Visual Studio-kód használatával hozhat létre egy helyi Azure Functions-projektet a kiválasztott nyelven. A cikk későbbi részében közzé fogja tenni a függvénykódot az Azure-ban. 

1. Válassza az Azure ikont a tevékenységsávon, majd az **Azure: Functions** területen válassza az **Új projekt létrehozása...** ikont.

    ![Válassza az Új projekt létrehozása lehetőséget](media/functions-create-first-function-vs-code/create-new-project.png)

1. Válassza ki a projektmunkaterület könyvtárhelyét, és válassza a **Kijelölés gombot.**

    > [!NOTE]
    > Ezeket a lépéseket úgy tervezték, hogy a munkaterületen kívül hajtsák végre őket. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Adja meg a következő információkat a következő utasításokat:

    ::: zone pivot="programming-language-csharp"
    + **Válasszon nyelvet a függvényprojekthez:** Válassza a lehetőséget. `C#`
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    + **Válasszon nyelvet a függvényprojekthez:** Válassza a lehetőséget. `JavaScript`
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    + **Válasszon nyelvet a függvényprojekthez:** Válassza a lehetőséget. `TypeScript`
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    + **Válasszon nyelvet a függvényprojekthez:** Válassza a lehetőséget. `PowerShell`
    ::: zone-end
    ::: zone pivot="programming-language-python"
    + **Válasszon nyelvet a függvényprojekthez:** Válassza a lehetőséget. `Python`

    + **Válasszon ki egy Python-aliast virtuális környezet létrehozásához:** Válassza ki a Python-értelmező helyét. Ha a hely nem jelenik meg, írja be a python bináris elérési útját.  
    ::: zone-end

    + **Válasszon sablont a projekt első függvényéhez:** Válassza a lehetőséget. `HTTP trigger`
    
    + **Adjon meg egy függvénynevet:** Írja be a típust. `HttpExample`
    
    ::: zone pivot="programming-language-csharp"
    + **Adjon meg egy névteret:** Írja be a típust. `My.Functions` 
    ::: zone-end

    + **Engedélyezési szint** `Anonymous`: Válassza a lehetőséget, amely lehetővé teszi, hogy bárki hívja a függvényvégpontot. Az engedélyezési szintről az [Engedélyezési kulcsok (Engedélyezési kulcsok) témakörben](functions-bindings-http-webhook-trigger.md#authorization-keys)olvashat.

    + **Válassza ki, hogyan szeretné megnyitni a projektet:** Válassza a lehetőséget. `Add to workspace`

1. Ezen információk használatával a Visual Studio-kód létrehoz egy Azure Functions projektet HTTP-eseményindítóval. A helyi projektfájlokat az Intézőben tekintheti meg. A létrehozott fájlokról a Létrehozott projektfájlok ról további információért olvassa el a [Létrehozott projektfájlok című témakört.](functions-develop-vs-code.md#generated-project-files) 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Miután meggyőződött arról, hogy a függvény megfelelően fut a helyi számítógépen, itt az ideje, hogy a Visual Studio Code használatával tegye közzé a projektet közvetlenül az Azure-ban. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>A funkció futtatása az Azure-ban

1. Vissza az **Azure: Funkciók** terület az oldalsávon, bontsa ki az új függvényalkalmazás az előfizetés alatt. A **Functions (Windows)** vagy a Ctrl + click (MacOS) elemre kattintva a **Jobb**gombbal (Windows) elemre kattintva válassza a **Függvény URL-címének másolása parancsot.**

    ![Az új HTTP-eseményindító függvény URL-címének másolása](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Illessze be ezt az URL-címet a HTTP-kérelemhez a böngésző címsorába, adja hozzá a `name` lekérdezési karakterláncot az URL-cím `?name=Functions` végéhez, majd hajtsa végre a kérést. A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions 
        
    A következő példa a böngészőben a függvény által visszaadott távoli GET-kérésre adott választ mutatja be: 

    ![A függvény által visszaadott válasz a böngészőben](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatja a következő lépést, [adjon hozzá egy Azure Storage-várólista-kötést a függvényhez,](functions-add-output-binding-storage-queue-vs-code.md)az összes erőforrást a helyén kell tartania ahhoz, hogy a már elvégzett funkciókra építve építkezhet.

Ellenkező esetben a következő lépésekkel törölheti a függvényalkalmazást és a hozzá kapcsolódó erőforrásokat, hogy elkerülje a további költségeket.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

A Függvényköltségekről a [Felhasználási terv költségeinek becslése (Becslés)](functions-consumption-costs.md)témakörben olvashat bővebben.

## <a name="next-steps"></a>További lépések

A Visual Studio Code segítéségével létrehozott egy egyszerű, HTTP-eseményindítóval aktivált függvényt tartalmazó függvényalkalmazást. A következő cikkben ezt a függvényt kimeneti kötés hozzáadásával bonthatja ki. Ez a kötés írja a karakterláncot a HTTP-kérelem egy üzenetet egy Azure Queue Storage várólistában. 

> [!div class="nextstepaction"]
> [Azure Storage-várólista-kötés hozzáadása a függvényhez](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
