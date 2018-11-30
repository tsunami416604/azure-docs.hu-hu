---
title: Az első tartós függvény létrehozása az Azure-banC#
description: Hozzon létre, és a egy Visual Studio használatával tartós Azure-függvény közzététele.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: azure functions, függvények, eseményfeldolgozás, számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: azfuncdf
ms.openlocfilehash: c7153823ade64b086eb38046ed6d7cdb0e0a1381
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52638632"
---
# <a name="create-your-first-durable-function-in-c"></a>A tartós függvény létrehozásaC#

*Durable Functions* kiterjesztése [Azure Functions](../functions-overview.md) , amellyel írási állapot-nyilvántartó functions egy kiszolgáló nélküli környezetben. A bővítmény kezeli a állapot, ellenőrzőpontok és újraindul az Ön számára.

Ebből a cikkből elsajátíthatja, hogyan használja a Visual Studio 2017-eszközök az Azure Functions helyi létrehozásához és a egy "hello world" tartós függvény tesztelése.  Ez a függvény összehangolására, és a lánc együttesen hívások egyéb funkciók. Ezután közzéteheti a függvénykódot az Azure-ban. Ezek az eszközök a Visual Studio 2017 Azure-fejlesztési számítási feladatának részeként érhetők el.

![Tartós függvény futtatása a felhőben](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse a [Visual Studio 2017](https://azure.microsoft.com/downloads/)-et, és győződjön meg arról, hogy az **Azure-fejlesztési** számítási feladat is telepítve van.

* Győződjön meg arról, hogy rendelkezik a [legújabb Azure Functions-eszközökkel](../functions-develop-vs.md#check-your-tools-version).

* Győződjön meg arról, hogy a [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) telepíteni és futtatni.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Függvényalkalmazás-projekt létrehozása

A Visual Studio Azure Functions projektsablonja egy olyan projektet hoz létre, amely közzétehető egy Azure-függvényalkalmazásban. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások felügyelete, üzembe helyezése és megosztása érdekében.

1. A Visual Studio **Fájl** menüjében válassza az **Új** > **Projekt** lehetőséget.

2. Az **Új projekt** párbeszédpanelen válassza a **Telepítve** elemet, bontsa ki a **Visual C#** > **Felhő** csomópontot, válassza az **Azure Functions** lehetőséget, írja be a projekt **Nevét**, majd kattintson az **OK** gombra. A függvényalkalmazás nevének egy C#-névtérként is érvényesnek kell lennie, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy más nem alfanumerikus karaktereket.

    ![Új projekt párbeszédpanel a Visual Studióban egy függvény létrehozásához](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

3. Használja a kép alatti táblázatban megadott beállításokat.

    ![A Visual Studio Új függvény párbeszédpanelje](./media/durable-functions-create-first-csharp/functions-vs-new-function.png) 

    | Beállítás      | Ajánlott érték  | Leírás                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verzió** | Azure Functions 2.x <br />(.NET Core) | A verzió 2.x verziójú futtatókörnyezet, az Azure Functions, amely támogatja a .NET Core használó Functions-projektet hoz létre. Az Azure Functions 1.x támogatja a .NET-keretrendszert. További információ: [Azure Functions futtatókörnyezet-verzió megcélzása](../functions-versions.md).   |
    | **Sablon** | Üres | Ez létrehoz egy üres függvényalkalmazást. |
    | **Storage-fiók**  | Storage Emulator | Storage-fiók megadása kötelező a tartós függvény állapot-felügyeleti. |

4. Kattintson a **OK** üres Functions-projekt létrehozása.

## <a name="add-functions-to-the-app"></a>Funkciók hozzáadása az alkalmazáshoz

A Visual Studio létrehoz egy üres függvényalkalmazás projektjét.  Az alkalmazáshoz szükséges alapvető konfigurációs fájlokat tartalmaz, de még nem tartalmaz minden.  Adja hozzá a projekthez egy tartós függvénysablon kell.

1. Kattintson a jobb gombbal a projektre a Visual Studióban, és válassza ki **Hozzáadás** > **új Azure-függvény**.

    ![Új függvény hozzáadása](./media/durable-functions-create-first-csharp/functions-vs-add-new-function.png)

2. Győződjön meg arról **Azure-függvény** van kiválasztva a Hozzáadás menüből, és adja meg a C# fájl nevét.  Nyomja meg az **Add** (Hozzáadás) gombot.

3. Válassza ki a **Durable Functions Vezénylési** sablonnal, majd kattintson **Ok**

    ![Tartós sablon kiválasztása](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)  

Egy új tartós függvény megjelenik az alkalmazáshoz.  Nyissa meg az új fájl a tartalom megtekintéséhez.  A tartós funkciója láncolás – példa egy egyszerű függvényt.  

* A `RunOrchestrator` módszer társítva az orchestrator-funkció.  Ez a függvény elindítja, hozzon létre egy listát, és adja hozzá a lista három függvény meghívásával eredményét.  A három függvényhívások befejezése után a listát ad vissza.  A függvény azt hívja a `SayHello` metódus (alapértelmezett fogja meghívni "<NameOfFile>_Hello").
* A `SayHello` a függvény egy hello adja vissza.
* A `HttpStart` módszer bemutatja a függvényt, amely megkezdi a vezénylési példánya.  Társítva van egy [HTTP-eseményindító](../functions-bindings-http-webhook.md) , amely elindítja az orchestrator egy új példányát, és visszaadja a jelölőnégyzet állapotának választ.

Most, hogy létrehozta a Functions-projektet és a egy tartós függvényt, tesztelheti a helyi számítógépen.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studióból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

1. A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez. Lehet, hogy egy tűzfalkivételt is engedélyeznie kell, hogy az eszközök kezelhessék a HTTP-kéréseket.

2. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Az Azure helyi futtatókörnyezete](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Illessze be a HTTP-kérelem URL-CÍMÉT a böngésző címsorában, és hajtsa végre a kérést. Az alábbiakban látható a böngészőben a helyi GET kérelemre a függvény által visszaadott válasz: 

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    A válasz, a HTTP-függvény, arról a tartós vezénylési kezdeti eredmény sikeresen elindult.  Ez még nem áll a vezénylési végeredményét.  A válasz néhány hasznos URL-címeket tartalmazza.  Most tegyük a vezénylési állapotának lekérdezése.

4. Másolja az URL-cím értéke `statusQueryGetUri` és illessze be azt a címet a böngésző sávot, és hajtsa végre a kérést.

    A kérelem lekérdezi az orchestration-példány állapota. Az alábbihoz hasonló végleges választ kell kapnia.  Ez megmutatja a példány befejeződött, és tartalmazza a kimenetek vagy a tartós függvény eredményét.

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
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

5. A hibakeresés leállításához nyomja le a **Shift + F5** billentyűkombinációt.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A projekt közzétételéhez rendelkeznie kell egy függvényalkalmazással.az Azure-előfizetéséhez. Közvetlenül a Visual Studióból is létrehozhat függvényalkalmazást.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a függvényalkalmazás alap URL-címét a Publish (Közzététel) profiloldalról. Cserélje ki a függvény helyi tesztelésekor használt `localhost:port` URL-címrészt az új alap URL-címmel.

    Az URL-cím, amely meghívja a tartós függvény HTTP-eseményindító a következő formátumban kell megadni:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Az azonos állapotot Válasz való előtt szerezheti be a közzétett alkalmazás használatakor.

## <a name="next-steps"></a>További lépések

A Visual Studio segítségével létrehozása és közzététele egy C# tartós függvényalkalmazást.

> [!div class="nextstepaction"]
> [Ismerje meg a gyakori tartós függvény minták.](durable-functions-overview.md)
