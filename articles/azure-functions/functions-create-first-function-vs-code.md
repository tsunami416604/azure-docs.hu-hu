---
title: Az első függvény létrehozása az Azure-ban a Visual Studio Code használatával
description: Hozzon létre és tegyen közzé az Azure-on egy egyszerű, HTTP-eseményindítót használó függvényt az Azure Functions-bővítmény használatával a Visual Studio Code-ban.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 42888060f206c89a597a1a18783070d0a805dfb9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241303"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Gyors útmutató: Azure Functions projekt létrehozása a Visual Studio Code használatával

Ebben a cikkben a Visual Studio Code használatával hoz létre egy olyan függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe. A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár. 

A cikk [CLI-alapú verziója](functions-create-first-azure-function-azure-cli.md) is létezik.

## <a name="configure-your-environment"></a>A környezet konfigurálása

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő követelményekkel:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"  
+ [Node. js](https://nodejs.org/), a Windows for NPM esetében kötelező. Csak [az aktív LTS-és karbantartási LTS-verziók ](https://nodejs.org/about/releases/). Használja a `npm --version` parancsot a verziójának vizsgálatához.
    A MacOS és Linux rendszereken a helyi fejlesztéshez nem szükséges.   
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
+ [Node. js](https://nodejs.org/), aktív LTS és karbantartási LTS-verziók (10.14.1 ajánlott). Használja a `npm --version` parancsot a verziójának vizsgálatához.
::: zone-end 
::: zone pivot="programming-language-python"
+ A [python 3,8](https://www.python.org/downloads/release/python-381/), a [Python 3,7](https://www.python.org/downloads/release/python-375/), a [Python 3,6](https://www.python.org/downloads/release/python-368/) Azure functions támogatja.
::: zone-end   
::: zone pivot="programming-language-powershell"
+ [PowerShell-mag](/powershell/scripting/install/installing-powershell-core-on-windows)

+ A [.net Core SDK 2.2 +](https://www.microsoft.com/net/download)  
::: zone-end  
+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  
::: zone pivot="programming-language-csharp"  
+ A Visual Studio Code [ C# bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .  
::: zone-end  
::: zone pivot="programming-language-python"
+ A [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a Visual Studio Code-hoz.  
::: zone-end  
::: zone pivot="programming-language-powershell"
+ A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)-hoz készült PowerShell-bővítmény.  
::: zone-end  

+ A Visual Studio Code [Azure functions bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) . 

## <a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása 

Ebben a szakaszban a Visual Studio Code használatával hozzon létre egy helyi Azure Functions projektet a választott nyelven. A cikk későbbi részében a függvény kódját közzé fogja tenni az Azure-ban. 

1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **új projekt létrehozása...** ikont.

    ![Válassza az új projekt létrehozása lehetőséget.](media/functions-create-first-function-vs-code/create-new-project.png)

1. Válasszon egy címtárbeli helyet a projekt munkaterülethez, és válassza a **kiválasztás**lehetőséget.

    > [!NOTE]
    > Ezeket a lépéseket úgy tervezték, hogy a munkaterületen kívül is el lehessen végezni. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Adja meg a következő információkat a kérdésekben:

    ::: zone pivot="programming-language-csharp"
    + **Válasszon nyelvet a függvény projekthez**: válassza a `C#`lehetőséget.
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    + **Válasszon nyelvet a függvény projekthez**: válassza a `JavaScript`lehetőséget.
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    + **Válasszon nyelvet a függvény projekthez**: válassza a `TypeScript`lehetőséget.
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    + **Válasszon nyelvet a függvény projekthez**: válassza a `PowerShell`lehetőséget.
    ::: zone-end
    ::: zone pivot="programming-language-python"
    + **Válasszon nyelvet a függvény projekthez**: válassza a `Python`lehetőséget.

    + **Válasszon ki egy Python-aliast a virtuális környezet létrehozásához**: válassza ki a Python-tolmács helyét. Ha a hely nem jelenik meg, írja be a Python bináris fájl teljes elérési útját.  
    ::: zone-end

    + **Válasszon sablont a projekt első függvényéhez**: válassza a `HTTP trigger`lehetőséget.
    
    + **Adja meg a függvény nevét**: Type `HttpExample`.
    
    ::: zone pivot="programming-language-csharp"
    + **Adja meg a névteret**: írja be a `My.Functions`. 
    ::: zone-end

    + **Engedélyezési szint**: válassza a `Anonymous`lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját. Az engedélyezési szint megismeréséhez tekintse meg az [engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys)című témakört.

    + **Válassza ki, hogyan szeretné megnyitni a projektet**: válassza a `Add to workspace`lehetőséget.

1. Ezen információk használatával a Visual Studio Code egy Azure Functions projektet hoz létre egy HTTP-triggerrel. A helyi projektfájlok a Explorerben tekinthetők meg. További információ a létrehozott fájlokról: [generált projektfájlok](functions-develop-vs-code.md#generated-project-files). 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Miután meggyőződött róla, hogy a függvény megfelelően fut a helyi számítógépen, ideje, hogy a Visual Studio Code használatával tegye közzé a projektet közvetlenül az Azure-ban. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>A függvény futtatása az Azure-ban

1. Vissza az **Azure: functions** területen az oldal sávján bontsa ki az új Function alkalmazást az előfizetés alatt. Bontsa ki a **függvények**elemet, kattintson a jobb gombbal (Windows) vagy a CTRL + kattintás (MacOS) elemre a **HttpExample**, majd válassza a **függvény URL-címének másolása**lehetőséget.

    ![A függvény URL-címének másolása az új HTTP-triggerhez](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába, adja hozzá a `name` lekérdezési karakterláncot az URL-cím végéhez `?name=Functions`, majd hajtsa végre a kérelmet. A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions 
        
    Az alábbi példa a böngészőben a függvény által visszaadott távoli GET kérelemre adott választ mutatja be: 

    ![A függvény által visszaadott válasz a böngészőben](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatja a következő lépéssel, [vegyen fel egy Azure Storage-várólista-kötést a függvényhez](functions-add-output-binding-storage-queue-vs-code.md), az összes erőforrást meg kell őriznie, hogy az Ön által már elvégzett feladatra épít.

Ellenkező esetben a következő lépésekkel törölheti a Function alkalmazást és annak kapcsolódó erőforrásait, hogy elkerülje a további költségek felmerülését.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

A functions-költségekkel kapcsolatos további tudnivalókért lásd: a [felhasználási terv költségeinek becslése](functions-consumption-costs.md).

## <a name="next-steps"></a>További lépések

A Visual Studio Code segítéségével létrehozott egy egyszerű, HTTP-eseményindítóval aktivált függvényt tartalmazó függvényalkalmazást. A következő cikkben kibonthatja ezt a függvényt egy kimeneti kötés hozzáadásával. Ez a kötés a HTTP-kérelemből írja be a karakterláncot egy Azure Queue Storage-várólistában lévő üzenetbe. 

> [!div class="nextstepaction"]
> [Azure Storage-várólista kötésének hozzáadása a függvényhez](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
