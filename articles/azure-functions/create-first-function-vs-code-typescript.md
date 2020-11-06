---
title: Írógéppel függvény létrehozása a Visual Studio Code-Azure Functions használatával
description: Ismerje meg, hogyan hozhat létre egy géppel használható függvényt, majd hogyan teheti közzé a helyi Node.js projektet a Azure Functions kiszolgáló nélküli üzemeltetésében a Visual Studio Code-ban lévő Azure Functions bővítménnyel.
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: 48295aab80b8a15a313ce7fa7acf94606357930b
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425116"
---
# <a name="quickstart-create-a-function-in-azure-with-typescript-using-visual-studio-code"></a>Gyors útmutató: függvény létrehozása az Azure-ban a Visual Studio Code használatával

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Ebben a cikkben a Visual Studio Code használatával hozzon létre egy olyan írógéppel függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár. 

Ennek a cikknek a [CLI-alapú verziója](create-first-function-cli-typescript.md) is létezik.

## <a name="configure-your-environment"></a>A környezet konfigurálása

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő követelményekkel:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js](https://nodejs.org/), aktív LTS-és karbantartási LTS-verziók (10.14.1 ajánlott). A `node --version` parancs használatával vizsgálja meg a verziót.  

+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A Visual Studio Code [Azure functions bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) .

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása

Ebben a szakaszban a Visual Studio Code használatával hozzon létre egy helyi Azure Functions projektet írógéppel. A cikk későbbi részében a függvény kódját közzé fogja tenni az Azure-ban. 

1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **új projekt létrehozása...** ikont.

    ![Válassza az új projekt létrehozása lehetőséget.](media/functions-create-first-function-vs-code/create-new-project.png)

1. Válasszon egy címtárbeli helyet a projekt munkaterülethez, és válassza a **kiválasztás** lehetőséget.

    > [!NOTE]
    > Ezeket a lépéseket úgy tervezték, hogy a munkaterületen kívül is el lehessen végezni. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Adja meg a következő információkat a kérdésekben:

    + **Válasszon nyelvet a függvény projekthez** : válassza a lehetőséget `TypeScript` .

    + **Válasszon sablont a projekt első függvényéhez** : válassza a lehetőséget `HTTP trigger` .

    + **Adja meg a függvény nevét** : Type `HttpExample` .

    + **Engedélyezési szint** : válassza `Anonymous` a lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját. Az engedélyezési szint megismeréséhez tekintse meg az [engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys)című témakört.

    + **Válassza ki, hogyan szeretné megnyitni a projektet** : válassza a lehetőséget `Add to workspace` .

1. Ezen információk használatával a Visual Studio Code egy Azure Functions projektet hoz létre egy HTTP-triggerrel. A helyi projektfájlok a Explorerben tekinthetők meg. További információ a létrehozott fájlokról: [generált projektfájlok](functions-develop-vs-code.md#generated-project-files). 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Miután meggyőződött róla, hogy a függvény megfelelően fut a helyi számítógépen, ideje, hogy a Visual Studio Code használatával tegye közzé a projektet közvetlenül az Azure-ban.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

Ebben a szakaszban egy Function-alkalmazást és egy kapcsolódó erőforrást hoz létre az Azure-előfizetésében, majd üzembe helyezi a kódot.

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban. 

1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **üzembe helyezés az alkalmazásban** ... gombot.

    ![A projekt közzététele az Azure-ban](media/functions-create-first-function-vs-code/function-app-publish-project.png)

1. Adja meg a következő információkat a kérdésekben:

    + **Mappa kiválasztása** : válasszon egy mappát a munkaterületről, vagy tallózással keresse meg a Function alkalmazást tartalmazó mappát. Ez nem jelenik meg, ha már nyitva van egy érvényes Function alkalmazás.

    + **Előfizetés kiválasztása** : válassza ki a használni kívánt előfizetést. Ez nem jelenik meg, ha csak egy előfizetéssel rendelkezik.

    + **Függvényalkalmazás kiválasztása az Azure-ban** : válassza a lehetőséget `+ Create new Function App` . (Ne válassza a `Advanced` lehetőséget, amely nem szerepel ebben a cikkben.)

    + **Adja meg a Function alkalmazás globálisan egyedi nevét** : írjon be egy URL-útvonalon érvényes nevet. A rendszer érvényesíti a beírt nevet, hogy a Azure Functions egyedi legyen. 

    + **Válasszon egy futtatókörnyezetet** : válassza ki a helyileg futtatott Node.js verzióját. A `node --version` parancs segítségével megtekintheti a verziót.

    + **Válasszon egy helyet az új erőforrásokhoz** : a jobb teljesítmény érdekében válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) .

1. Ha elkészült, a következő Azure-erőforrások jönnek létre az előfizetésben, és neveket kell használni a Function app neve alapján:

    + Egy erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
    + Szabványos Azure Storage-fiók, amely fenntartja az állapotot és a projektekkel kapcsolatos egyéb információkat.
    + Egy használati terv, amely a kiszolgáló nélküli Function alkalmazás mögöttes gazdagépét határozza meg. 
    + Egy Function alkalmazás, amely biztosítja a környezetet a függvény kódjának végrehajtásához. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az azonos üzemeltetési csomagban található erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.
    + A Function alkalmazáshoz csatlakoztatott Application Insights-példány, amely a kiszolgáló nélküli függvény használatát követi nyomon.

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. 

1. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat is. Ha kihagyja az értesítést, válassza a jobb alsó sarokban található harang ikont az újbóli megjelenítéshez.

    ![Teljes értesítés létrehozása](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>További lépések

A Visual Studio Code segítéségével létrehozott egy egyszerű, HTTP-eseményindítóval aktivált függvényt tartalmazó függvényalkalmazást. A következő cikkben kibonthatja ezt a függvényt egy kimeneti kötés hozzáadásával. Ez a kötés a HTTP-kérelemből írja be a karakterláncot egy Azure Queue Storage-várólistában lévő üzenetbe. 

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-typescript)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
