---
title: Az első tartós funkció létrehozása az Azure-ban a használatávalC#
description: Azure tartós függvény létrehozása és közzététele a Visual Studióval.
author: jeffhollan
ms.topic: quickstart
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 12e79df4af2dab097a41cf8482d5a344080890cf
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769701"
---
# <a name="create-your-first-durable-function-in-c"></a>Hozza létre első tartós funkcióját C\#

A *Durable Functions* [Azure functions](../functions-overview.md) , amely lehetővé teszi állapot-nyilvántartó függvények írására kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studio 2019-et a "Hello World" tartós funkciójának helyi létrehozására és tesztelésére.  Ez a függvény összehangolja és láncokba rendezi a más függvények hívásait. Ezután közzéteheti a függvénykódot az Azure-ban. Ezek az eszközök az Azure-fejlesztési számítási feladatok részeként érhetők el a Visual Studio 2019-ben.

![Tartós funkció futtatása az Azure-ban](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse a [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)alkalmazást. Győződjön meg arról, hogy az **Azure-fejlesztési** munkaterhelés is telepítve van. A Visual Studio 2017 támogatja a Durable Functions fejlesztést is, de a felhasználói felület és a lépések eltérnek egymástól.

* Ellenőrizze, hogy az [Azure Storage-emulátor](../../storage/common/storage-use-emulator.md) telepítve van-e és fut-e.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Függvényalkalmazás-projekt létrehozása

A Azure Functions sablon létrehoz egy projektet, amely közzétehető egy Azure-beli Function alkalmazásban. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése, skálázása és megosztása érdekében.

1. A Visual Studio **Fájl** menüjében válassza az **Új** > **Projekt** lehetőséget.

1. Az **új projekt hozzáadása** párbeszédpanelen keresse meg a `functions`, válassza ki a **Azure functions** sablont, és kattintson a **tovább**gombra. 

    ![Új projekt párbeszédpanel a Visual Studióban egy függvény létrehozásához](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Írjon be egy **projekt nevét** a projekthez, majd kattintson **az OK gombra**. A projekt nevének érvényesnek kell lennie C# névtérként, ezért ne használjon aláhúzást, kötőjelet vagy más nem alfanumerikus karaktereket.

1. Az **új Azure functions alkalmazás létrehozása**területen használja a képet követő táblázatban megadott beállításokat.

    ![Új Azure Functions alkalmazás párbeszédpanel létrehozása a Visual Studióban](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Beállítás      | Ajánlott érték  | Leírás                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verzió** | Azure Functions 2,0 <br />(.NET Core) | Egy olyan Function projektet hoz létre, amely a .NET Core-ot támogató Azure Functions 2,0-es verzióját használja. A Azure Functions 1,0 támogatja a .NET-keretrendszert. További információ: [Azure Functions futtatókörnyezet-verzió megcélzása](../functions-versions.md).   |
    | **Sablon** | Üres | Létrehoz egy üres Function alkalmazást. |
    | **Storage-fiók**  | Storage Emulator | A tartós működés állapotának kezeléséhez Storage-fiókra van szükség. |

4. Válassza a **Létrehozás** lehetőséget egy üres Function projekt létrehozásához. Ez a projekt a függvények futtatásához szükséges alapszintű konfigurációs fájlokat tartalmaz.

## <a name="add-functions-to-the-app"></a>Függvények hozzáadása az alkalmazáshoz

A következő lépésekkel hozhatja létre a projektben a tartós függvény kódját.

1. Kattintson a jobb gombbal a projektre a Visual Studióban, majd válassza a **hozzáadás** > **új Azure-függvény**lehetőséget.

    ![Új függvény hozzáadása](./media/durable-functions-create-first-csharp/functions-vs-add-new-function.png)

1. Ellenőrizze, hogy az **Azure Function** be van-e jelölve a Hozzáadás menüben, C# írja be a fájl nevét, majd válassza a **Hozzáadás**lehetőséget.

1. Válassza ki a **Durable functions a hangszerelési** sablont, majd kattintson **az OK gombra** .

    ![Tartós sablon kiválasztása](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)  

> [!NOTE]
> Ez a sablon jelenleg egy tartós függvényt hoz létre a bővítmény régebbi 1. x verziójának használatával. Tekintse meg a [Durable functions verziókról](durable-functions-versions.md) szóló cikket, amely arról nyújt tájékoztatást, hogyan frissíthet a Durable functions újabb 2. x verziójára.

A rendszer új tartós függvényt ad hozzá az alkalmazáshoz.  A tartalom megtekintéséhez nyissa meg az új. cs fájlt. Ez a tartós függvény egy egyszerű függvények láncolására szolgáló példa a következő módszerekkel:  

| Módszer | Függvénynév | Leírás |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Kezeli a tartós összeszerelést. Ebben az esetben a folyamat elindul, létrehoz egy listát, és hozzáadja a listához a három függvény hívásának eredményét.  Ha a három függvény meghívása befejeződött, a rendszer visszaadja a listát. |
| **`SayHello`** | `<file-name>_Hello` | A függvény egy Hello értéket ad vissza. Ez az a függvény, amely az összehangolt üzleti logikát tartalmazza. |
| **`HttpStart`** | `<file-name>_HttpStart` | [Http-triggert használó függvény](../functions-bindings-http-webhook.md) , amely elindítja a folyamat egy példányát, és visszaadja az állapot-ellenőrzési választ. |

Most, hogy létrehozta a függvény projektjét és egy tartós függvényt, tesztelheti a helyi számítógépen.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studióból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

1. A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez. Lehet, hogy egy tűzfalkivételt is engedélyeznie kell, hogy az eszközök kezelhessék a HTTP-kéréseket.

2. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Az Azure helyi futtatókörnyezete](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába, és hajtsa végre a kérelmet. Az alábbiakban látható a böngészőben a helyi GET kérelemre a függvény által visszaadott válasz:

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    A válasz a HTTP-függvény kezdeti eredménye, amely közli, hogy a tartós összehangolás sikeresen elindult.  Még nem az előkészítés végeredménye.  A válasz több hasznos URL-címet is tartalmaz.  Most pedig lekérdezjük a folyamat állapotát.

4. Másolja `statusQueryGetUri` és illessze be az URL-címet a böngésző címsorába, majd hajtsa végre a kérelmet.

    A kérelem lekérdezi az állapotot az előkészítési példányon. A következőhöz hasonló, végleges választ kaphat.  Ez a kimenet mutatja, hogy a példány befejeződött, és tartalmazza a tartós függvény kimeneteit vagy eredményét.

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

5. A hibakeresés leállításához nyomja le a **Shift + F5** billentyűkombinációt.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A projekt közzétételéhez rendelkeznie kell egy függvényalkalmazással.az Azure-előfizetéséhez. Közvetlenül a Visual Studióból is létrehozhat függvényalkalmazást.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a függvényalkalmazás alap URL-címét a Publish (Közzététel) profiloldalról. Cserélje ki a függvény helyi tesztelésekor használt `localhost:port` URL-címrészt az új alap URL-címmel.

    A tartós függvény HTTP-triggerét meghívó URL-címnek a következő formátumúnak kell lennie:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Ugyanezt az állapot-választ kell megadnia, mint korábban a közzétett alkalmazás használatakor.

## <a name="next-steps"></a>Következő lépések

A C# Visual Studio használatával tartós Function-alkalmazást hozhat létre és tehet közzé.

> [!div class="nextstepaction"]
> [További tudnivalók a tartós függvények gyakori mintái](durable-functions-overview.md#application-patterns)