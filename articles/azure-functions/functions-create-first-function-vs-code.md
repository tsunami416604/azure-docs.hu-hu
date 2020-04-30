---
title: Az első függvény létrehozása az Azure-ban a Visual Studio Code használatával
description: Hozzon létre és tegyen közzé az Azure-on egy egyszerű, HTTP-eseményindítót használó függvényt az Azure Functions-bővítmény használatával a Visual Studio Code-ban.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 08575164334db965a9e5592b761a06205208de9e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81732774"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Gyors útmutató: Azure Functions projekt létrehozása a Visual Studio Code használatával

Ebben a cikkben a Visual Studio Code használatával hoz létre egy olyan függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe. A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár. 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Ennek a cikknek a [CLI-alapú verziója](functions-create-first-azure-function-azure-cli.md) is létezik.
::: zone-end  

::: zone pivot="programming-language-java"  
> [!NOTE]
> Ha a VS Code nem az Ön által előnyben részesített fejlesztői eszköz, tekintse meg a Java-fejlesztőknek készült hasonló oktatóanyagokat a [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java), a [Gradle](/azure/azure-functions/functions-create-first-java-gradle) és a [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions)használatával.
::: zone-end  

## <a name="configure-your-environment"></a>A környezet konfigurálása

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő követelményekkel:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"  
+ [Node. js](https://nodejs.org/), a Windows for NPM esetében kötelező. Csak [az aktív LTS-és karbantartási LTS-verziók](https://nodejs.org/about/releases/). A `node --version` parancs használatával vizsgálja meg a verziót.
    A macOS és Linux rendszereken a helyi fejlesztéshez nem szükséges.   
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
+ [Node. js](https://nodejs.org/), aktív LTS és karbantartási LTS-verziók (10.14.1 ajánlott). A `node --version` parancs használatával vizsgálja meg a verziót.
::: zone-end 
::: zone pivot="programming-language-python"
+ [Python 3,8](https://www.python.org/downloads/release/python-381/), [Python 3,7](https://www.python.org/downloads/release/python-375/), [Python 3,6](https://www.python.org/downloads/release/python-368/) Azure functions (x64) támogatja.
::: zone-end   
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ A [.net Core SDK 2.2 +](https://www.microsoft.com/net/download)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ A [Java fejlesztői csomag](https://aka.ms/azure-jdks)8-as verziója.

+ [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió.
::: zone-end  
+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  
::: zone pivot="programming-language-csharp"  
+ A Visual Studio Code-hoz készült [C#-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .  
::: zone-end  
::: zone pivot="programming-language-python"
+ A [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a Visual Studio Code-hoz.  
::: zone-end  
::: zone pivot="programming-language-powershell"
+ A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)-hoz készült PowerShell-bővítmény. 
::: zone-end  
::: zone pivot="programming-language-java"  
+ A [Java-bővítmény csomagja](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
::: zone-end  

+ A Visual Studio Code [Azure functions bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) . 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása 

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

    ::: zone pivot="programming-language-java"  
    + **Válasszon nyelvet a függvény projekthez**: válassza a `Java`lehetőséget.

    + **Adja meg a csoport azonosítóját**: válassza a lehetőséget `com.function`.

    + **Adja meg az összetevő azonosítóját**: válassza a lehetőséget `myFunction`.

    + **Adja meg a verziószámot**: válassza a lehetőséget `1.0-SNAPSHOT`.

    + **Adja meg a csomag nevét**: `com.function`válassza a lehetőséget.

    + **Adja meg az alkalmazás nevét**: `myFunction-12345`válassza a lehetőséget.
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Válasszon sablont a projekt első függvényéhez**: válassza a lehetőséget `HTTP trigger`.
    
    + **Adja meg a függvény nevét**: `HttpExample`Type.
    ::: zone-end  
    ::: zone pivot="programming-language-csharp"
    + **Adja meg a névteret**: Type `My.Functions`. 
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Engedélyezési szint**: válassza `Anonymous`a lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját. Az engedélyezési szint megismeréséhez tekintse meg az [engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys)című témakört.
    ::: zone-end  
    + **Válassza ki, hogyan szeretné megnyitni a projektet**: válassza a `Add to workspace`lehetőséget.

1. Ezen információk használatával a Visual Studio Code egy Azure Functions projektet hoz létre egy HTTP-triggerrel. A helyi projektfájlok a Explorerben tekinthetők meg. További információ a létrehozott fájlokról: [generált projektfájlok](functions-develop-vs-code.md#generated-project-files). 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python,programming-language-java"

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

1. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába, adja hozzá a `name` lekérdezési karakterláncot `?name=Functions` az URL-cím végéhez, majd hajtsa végre a kérelmet. A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

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
