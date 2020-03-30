---
title: Az első tartós függvény létrehozása az Azure-ban JavaScript használatával
description: Hozzon létre és tegyen közzé egy Azure durable függvényt a Visual Studio-kód használatával.
author: anthonychu
ms.topic: quickstart
ms.date: 03/24/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: 55098daa69d3e878140b20095b0a3e08811269e1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80257648"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Az első tartós függvény létrehozása a JavaScriptben

*A Durable Functions* az [Azure Functions bővítménye,](../functions-overview.md) amely lehetővé teszi az állapotalapú függvények kiszolgáló nélküli környezetben történő írását. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

Ebben a cikkben megtudhatja, hogyan használhatja a Visual Studio Code Azure Functions bővítményt egy "hello world" tartós függvény helyi létrehozásához és teszteléséhez.  Ez a függvény vezényli és összeláncolja a más függvények hívásait. Ezután közzéteheti a függvénykódot az Azure-ban.

![Tartós függvény futtatása az Azure-ban](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse [a Visual Studio-kódot](https://code.visualstudio.com/download).

* Az [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) VS Code bővítmény telepítése

* Győződjön meg arról, hogy az Azure Functions Core Tools legújabb [verziójával rendelkezik.](../functions-run-local.md)

* A tartós függvények hez Azure-tárfiók szükséges. Szüksége van egy Azure-előfizetésre.

* Győződjön meg arról, hogy a [Node.js](https://nodejs.org/) 10.x vagy 12.x verziója telepítve van.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása 

Ebben a szakaszban a Visual Studio-kód használatával hozzon létre egy helyi Azure Functions projektet. 

1. A Visual Studio-kódban nyomja le az F1 (vagy a Ctrl/Cmd+Shift+P) billentyűkombinációt a parancspaletta megnyitásához. A parancspalettán keresse `Azure Functions: Create New Project...`meg és válassza a lehetőséget.

    ![Függvény létrehozása](media/quickstart-js-vscode/functions-create-project.png)

1. Válasszon egy üres mappahelyet a projekthez, és válassza a **Kijelölés lehetőséget.**

1. Az utasításokat követve adja meg a következő információkat:

    | Kérdés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Nyelv kiválasztása a függvényalkalmazás-projekthez | JavaScript | Hozzon létre egy helyi Node.js Functions projektet. |
    | Válassza ki a verziót | Azure Functions v3 | Ez a lehetőség csak akkor jelenik meg, ha a Core Tools még nincs telepítve. Ebben az esetben a Core Tools az alkalmazás első futtatásakor települ. |
    | Sablon kiválasztása a projekt első függvényéhez | Ugrás egyelőre | |
    | Válassza ki, hogyan szeretné megnyitni a projektet | Megnyitás az aktuális ablakban | A VS-kód újbóli megnyitása a kijelölt mappában. |

A Visual Studio Code szükség esetén telepíti az Azure Functions alapvető eszközeit. Emellett létrehoz egy függvényalkalmazás-projektet egy mappában. Ez a projekt tartalmazza a [host.json](../functions-host-json.md) és [a local.settings.json konfigurációs](../functions-run-local.md#local-settings-file) fájlokat.

A gyökérmappában egy package.json fájl is létrejön.

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Az Azure Functions V2 kompatibilitási módjának engedélyezése

Jelenleg a JavaScript tartós függvények engedélyezni kell az Azure Functions V2 kompatibilitási mód engedélyezését.

1. Nyissa meg a *local.settings.json webhelyet* az alkalmazás helyi futtatásakor használt beállítások szerkesztéséhez.

1. Adjon hozzá `FUNCTIONS_V2_COMPATIBILITY_MODE` egy nevű `true`beállítást.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureWebJobsStorage": "",
            "FUNCTIONS_WORKER_RUNTIME": "node",
            "FUNCTIONS_V2_COMPATIBILITY_MODE": "true"
        }
    }
    ```

## <a name="install-the-durable-functions-npm-package"></a>A Durable Functions npm csomag telepítése

Ha egy Node.js függvényalkalmazásban szeretne tartós függvényekkel `durable-functions`dolgozni, használja a nevű tárat.

1. A *Nézet* menüben vagy a Ctrl+Shift+' billentyűkombinációval új terminált nyithat meg a VS-kódban.

1. Telepítse `durable-functions` az npm `npm install durable-functions` csomagot a függvényalkalmazás gyökérkönyvtárában való futtatással.

## <a name="creating-your-functions"></a>A függvények létrehozása

A legalapvetőbb Durable Functions alkalmazás három funkciót tartalmaz:

* *Orchestrator függvény* – egy olyan munkafolyamatot ír le, amely más függvényeket vezényl.
* *Tevékenység függvény* – az orchestrator függvény által megnevezett, munkát végez, és opcionálisan egy értéket ad vissza.
* *Ügyfélfüggvény* – egy rendszeres Azure-függvény, amely elindítja az orchestrator függvényt. Ez a példa http-aktivált függvényt használ.

### <a name="orchestrator-function"></a>Orchestrator függvény

Sablon segítségével hozhatja létre a projekt tartós függvénykódját.

1. A parancspalettán keresse `Azure Functions: Create Function...`meg és válassza a lehetőséget.

1. Az utasításokat követve adja meg a következő információkat:

    | Kérdés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Sablon kiválasztása a funkcióhoz | Tartós függvények orchestrator | Tartós függvények vezénylési hangszerelése |
    | Függvénynév megadására | HelloOrchestrator | A tartós függvény neve |

Hozzáadtál egy orchestratort a tevékenységfüggvények koordinálásához. Nyissa meg *a HelloOrchestrator/index.js-t* az orchestrator függvény megtekintéséhez. Minden hívás `context.df.callActivity` meghívja a `Hello`nevű tevékenységfüggvényt.

Ezután adja hozzá a `Hello` hivatkozott tevékenység funkciót.

### <a name="activity-function"></a>Tevékenység függvény

1. A parancspalettán keresse `Azure Functions: Create Function...`meg és válassza a lehetőséget.

1. Az utasításokat követve adja meg a következő információkat:

    | Kérdés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Sablon kiválasztása a funkcióhoz | Tartós függvények tevékenység | Tevékenységfüggvény létrehozása |
    | Függvénynév megadására | helló | A tevékenységfüggvény neve |

Az orchestrator `Hello` által meghívott tevékenységfüggvényt adta hozzá. Nyissa meg *a Hello/index.js-t,* és tekintse meg, hogy egy nevet vesz fel bemenetként, és visszaadja az üdvözlést. Egy tevékenységfüggvény, ahol műveleteket hajthat végre, például adatbázis-hívást kezdeményezni vagy számításokat végrehajtani.

Végül hozzá fog adni egy HTTP-aktivált függvényt, amely elindítja a vezénylést.

### <a name="client-function-http-starter"></a>Ügyfélfunkció (HTTP starter)

1. A parancspalettán keresse `Azure Functions: Create Function...`meg és válassza a lehetőséget.

1. Az utasításokat követve adja meg a következő információkat:

    | Kérdés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Sablon kiválasztása a funkcióhoz | Tartós funkciók HTTP starter | HTTP-kezdőfunkció létrehozása |
    | Függvénynév megadására | DurableFunctionshttpStart | A tevékenységfüggvény neve |
    | Authorization level (Engedélyszint) | Névtelen | A bemutató céljából engedélyezze a függvény hitelesítés nélküli megnevezését |

Hozzáadott egy HTTP-aktivált függvényt, amely elindítja a vezénylést. Nyissa meg *a DurableFunctionsHttpStart/index.js metódust,* és tekintse meg, hogy egy új vezénylési indítást használ.Open DurableFunctionsHttpStart/index.js to see that it uses `client.startNew` to start a new orchestration. Ezután `client.createCheckStatusResponse` egy HTTP-választ ad vissza, amely tartalmazza az új vezénylésfigyelésfigyeléséhez és kezeléséhez használható URL-címeket.

Most már rendelkezik egy Durable Functions alkalmazással, amely helyileg futtatható és telepíthető az Azure-ba.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studio Code-ból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

1. A funkció teszteléséhez állítson be `Hello` egy töréspontot a tevékenységfüggvény-kódban (*Hello/index.js*). Nyomja le az `Debug: Start Debugging` F5 billentyűt, vagy válasszon a parancspalettából a függvényalkalmazás-projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

    > [!NOTE]
    > A hibakeresésről a [Tartós függvények diagnosztikája](durable-functions-diagnostics.md#debugging) című dokumentumban talál további információt.

1. A tartós funkciók futtatásához Azure Storage-fiók szükséges. Amikor a VS Code tárfiók kiválasztását kéri, válassza **a Tárfiók kiválasztása lehetőséget.**

    ![Storage-fiók létrehozása](media/quickstart-js-vscode/functions-select-storage.png)

1. A következő utasításokat követve adja meg a következő információkat egy új tárfiók létrehozásához az Azure-ban.

    | Kérdés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Előfizetés kiválasztása | *az előfizetés neve* | Válassza ki az Azure-előfizetését |
    | Tárfiók kiválasztása | Új tárfiók létrehozása |  |
    | Adja meg az új tárfiók nevét | *egyedi név* | A létrehozandó tárfiók neve |
    | Erőforráscsoport kiválasztása | *egyedi név* | A létrehozandó erőforráscsoport neve |
    | Hely kiválasztása | *régió* | Válasszon ki egy Önhöz közeli területet |

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](media/quickstart-js-vscode/functions-f5.png)

1. Egy eszköz, például [postás](https://www.getpostman.com/) vagy [cURL](https://curl.haxx.se/)használatával http postakérést küldhet az URL-végpontnak. Cserélje le az utolsó szegmenst az orchestrator függvény ( .`HelloOrchestrator` Az URL-címnek `http://localhost:7071/api/orchestrators/HelloOrchestrator`a hoz hasonlónak kell lennie.

   A válasz a HTTP-függvény kezdeti eredménye, amely tudatja önvel, hogy a tartós vezényléssikeresen elindult. Ez még nem a vezénylés végeredménye. A válasz tartalmaz néhány hasznos URL-t. Most kérdezzük le a vezénylés állapotát.

1. Másolja a program `statusQueryGetUri` url-értékét, és illessze be a böngésző címsorába, és hajtsa végre a kérést. Azt is megteheti, hogy továbbra is használja postás, hogy kiadja a GET kérelmet.

   A kérelem lekérdezi a vezénylési példány az állapot. Meg kell kapnia egy esetleges választ, amely megmutatja nekünk a példány befejeződött, és tartalmazza a kimenetek vagy a tartós függvény eredményeit. Úgy néz ki, mint: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. A hibakeresés leállításához nyomja le **a Shift + F5** billentyűkombinációt a VS-kódban.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Az Azure Functions V2 kompatibilitási módjának engedélyezése

Ugyanazt az Azure Functions V2-kompatibilitást, amelyet helyileg engedélyezett, engedélyezni kell az azure-beli alkalmazásban.

1. A parancspaletta használatával `Azure Functions: Edit Setting...`keresse meg és válassza a lehetőséget.

1. Kövesse a utasításokat, hogy keresse meg a függvényalkalmazást az Azure-előfizetésben.

1. Válassza a(z) `Create new App Setting...` lehetőséget.

1. Adjon meg egy `FUNCTIONS_V2_COMPATIBILITY_MODE`új beállítási kulcsot a.

1. Adja meg a `true`beállításértékét.

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a vágólapra a HTTP-eseményindító URL-címét az **Output** (Kimenet) panelről. A HTTP által aktivált függvényt meghívja url-címnek a következő formátumban kell lennie:`http://<functionappname>.azurewebsites.net/orchestrators/HelloOrchestrator`

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. A közzétett alkalmazás használatakor ugyanazt az állapotválaszt kell kapnia, mint korábban.

## <a name="next-steps"></a>További lépések

A Visual Studio-kód segítségével javascript-alapú tartós függvényalkalmazást hozhat létre és tesz közzé.

> [!div class="nextstepaction"]
> [További információ a tartós függvények gyakori mintáiról](durable-functions-overview.md#application-patterns)