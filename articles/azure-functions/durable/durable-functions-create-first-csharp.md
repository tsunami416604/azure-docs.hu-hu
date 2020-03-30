---
title: 'Az első tartós függvény létrehozása az Azure-ban a C használatával #'
description: Hozzon létre és tegyen közzé egy Azure Durable Függvényt a Visual Studio vagy a Visual Studio-kód használatával.
author: jeffhollan
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: azfuncdf
zone_pivot_groups: code-editors-set-one
ms.openlocfilehash: eda3afdf8deb3336cd0c5293c2422e694caa69c8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132760"
---
# <a name="create-your-first-durable-function-in-c"></a>Az első tartós funkció létrehozása C-ben\#

*A Durable Functions* az [Azure Functions bővítménye,](../functions-overview.md) amely lehetővé teszi az állapotalapú függvények kiszolgáló nélküli környezetben történő írását. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

::: zone pivot="code-editor-vscode"

Ebben a cikkben megtudhatja, hogyan használhatja a Visual Studio-kódot egy "hello world" tartós függvény helyi létrehozásához és teszteléséhez.  Ez a függvény vezényli és láncra veri a más függvényeket. Ezután közzéteheti a függvénykódot az Azure-ban. Ezek az eszközök a VS Code [Azure Functions bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)részeként érhetők el.

![Tartós függvény futtatása az Azure-ban](./media/durable-functions-create-first-csharp/functions-vscode-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse [a Visual Studio-kódot](https://code.visualstudio.com/download).

* Telepítse a következő VS Code bővítményeket:
    - [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
    - [C #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

* Győződjön meg arról, hogy az Azure Functions Core Tools legújabb [verziójával rendelkezik.](../functions-run-local.md)

* A tartós függvények hez Azure-tárfiók szükséges. Szüksége van egy Azure-előfizetésre.

* Győződjön meg arról, hogy a [.NET Core SDK](https://dotnet.microsoft.com/download) 3.1-es vagy újabb verziója van telepítve.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása 

Ebben a szakaszban a Visual Studio-kód használatával hozzon létre egy helyi Azure Functions projektet. 

1. A Visual Studio-kódban nyomja le az F1 (vagy a Ctrl/Cmd+Shift+P) billentyűkombinációt a parancspaletta megnyitásához. A parancspalettán keresse `Azure Functions: Create New Project...`meg és válassza a lehetőséget.

    ![Függvényprojekt létrehozása](media/durable-functions-create-first-csharp/functions-vscode-create-project.png)

1. Válasszon egy üres mappahelyet a projekthez, és válassza a **Kijelölés lehetőséget.**

1. Az utasításokat követve adja meg a következő információkat:

    | Kérdés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Nyelv kiválasztása a függvényalkalmazás-projekthez | C# | Hozzon létre egy helyi C# Functions projektet. |
    | Válassza ki a verziót | Azure Functions v3 | Ez a lehetőség csak akkor jelenik meg, ha a Core Tools még nincs telepítve. Ebben az esetben a Core Tools az alkalmazás első futtatásakor települ. |
    | Sablon kiválasztása a projekt első függvényéhez | Ugrás egyelőre | |
    | Válassza ki, hogyan szeretné megnyitni a projektet | Megnyitás az aktuális ablakban | A VS-kód újbóli megnyitása a kijelölt mappában. |

A Visual Studio Code szükség esetén telepíti az Azure Functions alapvető eszközeit. Emellett létrehoz egy függvényalkalmazás-projektet egy mappában. Ez a projekt tartalmazza a [host.json](../functions-host-json.md) és [a local.settings.json konfigurációs](../functions-run-local.md#local-settings-file) fájlokat.

## <a name="add-functions-to-the-app"></a>Függvények hozzáadása az alkalmazáshoz

A következő lépések egy sablon t használja a projekt tartós függvénykódjának létrehozásához.

1. A parancspalettán keresse `Azure Functions: Create Function...`meg és válassza a lehetőséget.

1. Az utasításokat követve adja meg a következő információkat:

    | Kérdés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Sablon kiválasztása a funkcióhoz | DurableFunctionsOrchestration (DurableFunctionsOrchestration) | Tartós függvények vezénylési hangszerelése |
    | Függvénynév megadására | HelloOrchestration | Annak az osztálynak a neve, amelyben a függvények jönnek létre |
    | Névtér biztosítása | Vállalat.Függvény | A létrehozott osztály névtere |

1. Amikor a VS Code tárfiók kiválasztását kéri, válassza **a Tárfiók kiválasztása lehetőséget.** A következő utasításokat követve adja meg a következő információkat egy új tárfiók létrehozásához az Azure-ban.

    | Kérdés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Előfizetés kiválasztása | *az előfizetés neve* | Válassza ki az Azure-előfizetését |
    | Tárfiók kiválasztása | Új tárfiók létrehozása |  |
    | Adja meg az új tárfiók nevét | *egyedi név* | A létrehozandó tárfiók neve |
    | Erőforráscsoport kiválasztása | *egyedi név* | A létrehozandó erőforráscsoport neve |
    | Hely kiválasztása | *régió* | Válasszon ki egy Önhöz közeli területet |

Az új függvényeket tartalmazó osztály hozzáadódik a projekthez. A VS Code hozzáadja a tárfiók kapcsolati karakterláncát a [`Microsoft.Azure.WebJobs.Extensions.DurableTask`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) *local.settings.json fájlhoz,* és egy hivatkozást a NuGet csomagra a *.csproj* projektfájlhoz.

Nyissa meg az új *HelloOrchestration.cs* fájlt a tartalom megtekintéséhez. Ez a tartós funkció egy egyszerű függvény láncolva példa a következő módszerekkel:  

| Módszer | Függvényneve | Leírás |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `HelloOrchestration` | Kezeli a tartós vezénylési. Ebben az esetben a vezénylési kezdődik, létrehoz egy listát, és hozzáadja az eredményt három függvény hívások a listához.  Amikor a három függvényhívás befejeződött, visszaadja a listát. |
| **`SayHello`** | `HelloOrchestration_Hello` | A függvény hello-t ad vissza. Ez az a függvény, amely tartalmazza az üzleti logika, amely jelenleg hangszerelt. |
| **`HttpStart`** | `HelloOrchestration_HttpStart` | Egy [HTTP-aktivált függvény,](../functions-bindings-http-webhook.md) amely elindítja a vezénylési példányt, és egy ellenőrző állapotválaszt ad vissza. |

Most, hogy létrehozta a függvényprojektet és egy tartós funkciót, tesztelheti azt a helyi számítógépen.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studio Code-ból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

1. A függvény teszteléséhez állítson be `SayHello` egy töréspontot a tevékenységfüggvény-kódban, és nyomja le az F5 billentyűt a függvényalkalmazás-projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

    > [!NOTE]
    > A hibakeresésről a [Tartós függvények diagnosztikája](durable-functions-diagnostics.md#debugging) című dokumentumban talál további információt.

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](media/durable-functions-create-first-csharp/functions-vscode-f5.png)

1. Egy eszköz, például [postás](https://www.getpostman.com/) vagy [cURL](https://curl.haxx.se/)használatával http postakérést küldhet az URL-végpontnak.

   A válasz a HTTP-függvény kezdeti eredménye, amely tudatja velünk, hogy a tartós vezénylés sikeresen elindult. Ez még nem a vezénylés végeredménye. A válasz tartalmaz néhány hasznos URL-t. Most kérdezzük le a vezénylés állapotát.

1. Másolja a program `statusQueryGetUri` url-értékét, és illessze be a böngésző címsorába, és hajtsa végre a kérést. Azt is megteheti, hogy továbbra is használja postás, hogy kiadja a GET kérelmet.

   A kérelem lekérdezi a vezénylési példány az állapot. Meg kell kapnia egy esetleges választ, amely megmutatja nekünk a példány befejeződött, és tartalmazza a kimenetek vagy a tartós függvény eredményeit. Úgy néz ki, mint: 

    ```json
    {
        "name": "HelloOrchestration",
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

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a vágólapra a HTTP-eseményindító URL-címét az **Output** (Kimenet) panelről. A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

        https://<functionappname>.azurewebsites.net/api/HelloOrchestration_HttpStart

1. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. A közzétett alkalmazás használatakor ugyanazt az állapotválaszt kell kapnia, mint korábban.

## <a name="next-steps"></a>További lépések

A Visual Studio-kód segítségével létrehozott és közzétehet egy C# tartós függvényalkalmazást.

> [!div class="nextstepaction"]
> [További információ a tartós függvények gyakori mintáiról](durable-functions-overview.md#application-patterns)

::: zone-end

::: zone pivot="code-editor-visualstudio"

Ebben a cikkben megtudhatja, hogyan hozhat létre és tesztelheti a Visual Studio 2019-et egy "hello world" tartós funkció helyi létrehozásához és teszteléséhez.  Ez a függvény vezényli és láncra veri a más függvényeket. Ezután közzéteheti a függvénykódot az Azure-ban. Ezek az eszközök a Visual Studio 2019 Azure fejlesztési munkaterhelésének részeként érhetők el.

![Tartós függvény futtatása az Azure-ban](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse a [Visual Studio 2019-et.](https://visualstudio.microsoft.com/vs/) Győződjön meg arról, hogy az **Azure fejlesztési** számítási feladatok is telepítve van. A Visual Studio 2017 támogatja a Durable Functions fejlesztését is, de a felhasználói felület és a lépések eltérőek.

* Ellenőrizze, hogy telepítette és futtatta-e az [Azure Storage-emulátort.](../../storage/common/storage-use-emulator.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Függvényalkalmazás-projekt létrehozása

Az Azure Functions sablon létrehoz egy projektet, amely közzétehető egy függvényalkalmazás az Azure-ban. A függvényalkalmazás lehetővé teszi a függvények logikai egységként történő csoportosítását az erőforrások egyszerűbb kezelése, üzembe helyezése, méretezése és megosztása érdekében.

1. A Visual Studióban válassza a Fájl menü **Új** > **projekt** **parancsát.**

1. Az **Új projekt létrehozása** párbeszédpanelen `functions`keresse meg a ( Keresés ) című mezőt, válassza az **Azure Functions** sablont, és válassza a **Tovább**gombot. 

    ![Új projekt párbeszédpanel a Visual Studióban egy függvény létrehozásához](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Írja be a **projekt nevét,** és válassza az **OK gombot.** A projekt nevének C# névtérként érvényesnek kell lennie, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy más nem alfanumerikus karaktereket.

1. Az **új Azure Functions alkalmazás létrehozása**párbeszédpanelen használja a lemezképet követő táblázatban megadott beállításokat.

    ![Új Azure Functions alkalmazás párbeszédpanel létrehozása a Visual Studióban](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Beállítás      | Ajánlott érték  | Leírás                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verzió** | Azure Functions 3.0 <br />(.NET Core) | Létrehoz egy függvényprojektet, amely az Azure Functions 3.0-s verzióját használja, amely támogatja a .NET Core 3.1-et. További információ: [Azure Functions futtatókörnyezet-verzió megcélzása](../functions-versions.md).   |
    | **Sablon** | Üres | Üres függvényalkalmazást hoz létre. |
    | **Tárfiók**  | Storage Emulator | A tartós függvényállapot-kezeléshez tárfiók szükséges. |

4. Üres függvényprojekt létrehozásához válassza a **Létrehozás** lehetőséget. Ez a projekt a funkciók futtatásához szükséges alapvető konfigurációs fájlokkal rendelkezik.

## <a name="add-functions-to-the-app"></a>Függvények hozzáadása az alkalmazáshoz

A következő lépések egy sablon t használja a projekt tartós függvénykódjának létrehozásához.

1. Kattintson a jobb gombbal a projektre a Visual Studióban, és válassza az**Új Azure-függvény** **hozzáadása** > parancsot.

    ![Új függvény hozzáadása](./media/durable-functions-create-first-csharp/functions-vs-add-function.png)

1. Ellenőrizze, hogy az **Azure Függvény** ki van-e jelölve a hozzáadás menüből, írja be a C# fájl nevét, és válassza a **Hozzáadás lehetőséget.**

1. Válassza a **Tartós függvények vénulyálása** sablont, majd az **Ok**

    ![Tartós sablon kiválasztása](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)

A függvény egy új tartós függvényt ad hozzá az alkalmazáshoz.  Nyissa meg az új .cs fájlt a tartalom megtekintéséhez. Ez a tartós funkció egy egyszerű függvény láncolva példa a következő módszerekkel:  

| Módszer | Függvényneve | Leírás |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Kezeli a tartós vezénylési. Ebben az esetben a vezénylési kezdődik, létrehoz egy listát, és hozzáadja az eredményt három függvény hívások a listához.  Amikor a három függvényhívás befejeződött, visszaadja a listát. |
| **`SayHello`** | `<file-name>_Hello` | A függvény hello-t ad vissza. Ez az a függvény, amely tartalmazza az üzleti logika, amely jelenleg hangszerelt. |
| **`HttpStart`** | `<file-name>_HttpStart` | Egy [HTTP-aktivált függvény,](../functions-bindings-http-webhook.md) amely elindítja a vezénylési példányt, és egy ellenőrző állapotválaszt ad vissza. |

Most, hogy létrehozta a függvényprojektet és egy tartós funkciót, tesztelheti azt a helyi számítógépen.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studióból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

1. A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez. Lehet, hogy egy tűzfalkivételt is engedélyeznie kell, hogy az eszközök kezelhessék a HTTP-kéréseket.

2. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Az Azure helyi futtatókörnyezete](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába, és hajtsa végre a kérést. Az alábbiakban látható a böngészőben a helyi GET kérelemre a függvény által visszaadott válasz:

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    A válasz a HTTP-függvény kezdeti eredménye, amely tudatja velünk, hogy a tartós vezénylés sikeresen elindult.  Ez még nem a vezénylés végeredménye.  A válasz tartalmaz néhány hasznos URL-t.  Most kérdezzük le a vezénylés állapotát.

4. Másolja a böngésző `statusQueryGetUri` címsorába az URL-értéket, és illessze be azt a böngésző címsorába, és hajtsa végre a kérést.

    A kérelem lekérdezi a vezénylési példány az állapot. A következőre reagáló végleges választ kell kapnia.  Ez a kimenet megmutatja, hogy a példány befejeződött, és tartalmazza a kimenetek vagy a tartós függvény eredményeit.

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
        "createdTime": "2019-11-02T07:07:40Z",
        "lastUpdatedTime": "2019-11-02T07:07:52Z"
    }
    ```

5. A hibakeresés leállításához nyomja le a **Shift + F5 billentyűkombinációt.**

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A projekt közzétételéhez rendelkeznie kell egy függvényalkalmazással.az Azure-előfizetéséhez. Közvetlenül a Visual Studióból is létrehozhat függvényalkalmazást.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a függvényalkalmazás alap URL-címét a Publish (Közzététel) profiloldalról. Cserélje ki a függvény helyi tesztelésekor használt `localhost:port` URL-címrészt az új alap URL-címmel.

    A tartós függvény HTTP-eseményindítóját megindító URL-címnek a következő formátumúnak kell lennie:

        https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. A közzétett alkalmazás használatakor ugyanazt az állapotválaszt kell kapnia, mint korábban.

## <a name="next-steps"></a>További lépések

A Visual Studio segítségével létrehozott és közzétett egy C# tartós függvényalkalmazást.

> [!div class="nextstepaction"]
> [További információ a tartós függvények gyakori mintáiról](durable-functions-overview.md#application-patterns)

::: zone-end
