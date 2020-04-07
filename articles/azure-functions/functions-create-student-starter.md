---
title: Funkció létrehozása az Azure diákoknak kezdővel
description: Ismerje meg, hogyan hozhat létre Azure-függvényt egy Azure for Student Starter-előfizetésből
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: how-to
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: c7dd88bf0ead558a0c4951baf38543566d805caa
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756472"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Funkció létrehozása az Azure diákoknak kezdővel

Ebben az oktatóanyagban egy "hello world" HTTP-függvényt hozunk létre egy Azure for Students Starter-előfizetésben. Azt is végigvezetjük, hogy mi érhető el az Azure Functions ben ebben az előfizetési típusban.

A Microsoft *Azure diákoknak kezdővel* kezdheti meg az Azure-termékeket, amelyeket a felhőben való fejlesztéshez ingyenesen kell fejlesztenie. [Az ajánlatról itt olvashat bővebben.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Az Azure Functions lehetővé teszi a kód [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) környezetben történő végrehajtását anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást. [A Funkciókról itt olvashat bővebben.](./functions-overview.md)

## <a name="create-a-function"></a>Függvény létrehozása

 Ebben a témakörben megtudhatja, hogyan használhatja a Functions egy HTTP-aktivált "hello world" függvény az Azure Portalon.

![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás lehetővé teszi a függvények logikai egységként történő csoportosítását az erőforrások egyszerűbb kezelése, üzembe helyezése, méretezése és megosztása érdekében.

1. Válassza az **Erőforrás létrehozása** gomb található az Azure Portal bal felső sarkában. Ezután válassza **a Számítási** > **függvény alkalmazást**.

    ![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-student-starter/function-app-create-flow.png)

2. Használja az ábra alatti táblázatban megadott függvényalkalmazás-beállításokat.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
   | **[App Service-csomag/hely](./functions-scale.md)** | Új | Az üzemeltetési terv, amely szabályozza, hogy a függvényalkalmazás milyen régióban van telepítve, és az erőforrások sűrűsége. Az ugyanabban a csomagban telepített több függvényalkalmazás ugyanazt az egyetlen szabad példányt fogja megosztani. Ez a Student Starter terv korlátozása. A teljes tárhely lehetőségek [itt ismertetik.](./functions-scale.md)|
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. |
    |**[Application Insights](./functions-monitoring.md)**| Engedélyezve | Az Application Insights a függvényalkalmazás naplóinak tárolására és elemzésére szolgál. Alapértelmezés szerint engedélyezve van, ha olyan helyet választ, amely támogatja az Application Insights.It is enabled by default if you choose a location that supports Application Insights. Az Application Insights bármely funkcióhoz engedélyezhető, ha manuálisan kiválaszt egy közeli régiót az Application Insights üzembe helyezéséhez. Az Application Insights nélkül csak az élő streamelési naplók at tekintheti meg.

3. Válassza ki a fenti **App Service-csomag/Hely** lehetőséget egy másik hely kiválasztásához

4. Válassza **az Új létrehozása lehetőséget,** majd adjon egyedi nevet a tervnek.

5. Válassza ki az Önhöz legközelebb eső helyet. [Tekintse meg az Azure-régiók teljes térképét itt.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

    ![Új függvényalkalmazás-beállítások megadása](./media/functions-create-student-starter/function-app-create-notification.png)

8. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget.

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>HTTP által aktivált függvény létrehozása

1. Bontsa ki az új **+** függvényalkalmazást, majd kattintson a **Funkciók**gomb, válassza a **Portálon**lehetőséget, és válassza a **Folytatás**lehetőséget.

    ![Függvény rövid útmutatója – platform kiválasztása](./media/functions-create-student-starter/function-app-quickstart-choose-portal.png)

1. Válassza a **WebHook + API**, majd a **Létrehozás** lehetőséget.

    ![A függvények gyors létrehozása az Azure Portalon.](./media/functions-create-student-starter/function-app-quickstart-node-webhook.png)

A rendszer létrehoz egy függvényt egy HTTP által indított függvény nyelvspecifikus sablonjának használatával.

Mostantól egy HTTP-kérelem küldésével futtathatja az új függvényt.

## <a name="test-the-function"></a>A függvény tesztelése

1. Az új függvényben kattintson a **</> Függvény URL-címének beolvasása** elemre a jobb felső sarokban, válassza a **default (Function key)** (alapértelmezett (funkcióbillentyű)) lehetőséget, majd kattintson a **Másolás** gombra. 

    ![A függvény URL-címének másolása az Azure portálról](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

2. Illessze be a függvény URL-címét a böngésző címsorába. Az URL-cím végéhez adja hozzá a `&name=<yourname>` lekérdezési sztring értéket, majd nyomja le az `Enter` billentyűt a billentyűzeten a kérés végrehajtásához. Ekkor a függvény által visszaadott válasz jelenik meg a böngészőben.  

    Az alábbi példa a böngészőben visszaadott választ mutatja:

    ![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-student-starter/function-app-browser-testing.png)

    A kérelem URL-címe alapértelmezés szerint tartalmazza a függvény HTTP protokollon keresztüli eléréséhez szükséges kulcsot.

3. A függvény futásakor a rendszer nyomkövetési adatok ír a naplókba. Az előző végrehajtás nyomkövetési adatainak megtekintéséhez térjen vissza a függvényhez a portálon, és kattintson a képernyő alján található nyílra a **Naplók** elem kibontásához.

   ![A függvények naplómegtekintője az Azure Portalon.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Támogatott funkciók az Azure diákoknak kezdőben

Az Azure diákoknak kezdőként alkalmazásban hozzáférhet az Azure Functions futásidejű funkcióinak többségéhez, az alábbiakban felsorolt számos kulcsfontosságú korlátozással:

* A HTTP-eseményindító az egyetlen támogatott eseményindító típus.
    * Minden bemeneti és kimeneti kötés támogatott! [Lásd a teljes listát itt.](functions-triggers-bindings.md)
* Támogatott nyelvek: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F# (.NET Core 2)
    * [Lásd támogatott nyelvek magasabb tervek itt](supported-languages.md)
* A Windows az egyetlen támogatott operációs rendszer.
* A skálázás naponta legfeljebb 60 percig futó [ingyenes rétegpéldányra](https://azure.microsoft.com/pricing/details/app-service/windows/) korlátozódik. A HTTP-forgalom fogadásakor kiszolgáló nélküli skálázás0-ról 1 példányra lesz méretezve, de nem tovább.
* Csak a Functions futásidejű [2.x-es és újabb](functions-versions.md) verziója támogatott.
* Minden fejlesztői eszköz támogatott a szerkesztési és közzétételi funkciókhoz. Ez magában foglalja a VS-kód, a Visual Studio, az Azure CLI és az Azure Portal. Ha a portálon kívül mást szeretne használni, először létre kell hoznia egy alkalmazást a portálon, majd ki kell választania az alkalmazást központi telepítési célként a kívánt eszközben.

## <a name="next-steps"></a>További lépések

Létrehozott egy függvényalkalmazást egy egyszerű HTTP-aktivált funkcióval! Most már felfedezheti a helyi eszközök, több nyelven, a monitoring, és az integrációk.

 * [Az első függvény létrehozása a Visual Studio használatával](./functions-create-your-first-function-visual-studio.md)
 * [Az első függvény létrehozása a Visual Studio Code használatával](./functions-create-first-function-vs-code.md)
 * [Az Azure Functions JavaScript fejlesztői útmutatója](./functions-reference-node.md)
 * [Azure Functions használata azure SQL-adatbázishoz való csatlakozáshoz](./functions-scenario-database-table-cleanup.md)
 * [További információ az Azure Functions HTTP-kötéseiről.](./functions-bindings-http-webhook.md)
 * [Az Azure-függvények figyelése](./functions-monitoring.md)
