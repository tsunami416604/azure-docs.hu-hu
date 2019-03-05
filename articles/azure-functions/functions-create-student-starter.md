---
title: Az Azure diákoknak szóló ajánlatához függvény létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan Starter hallgató előfizetés az Azure-ban egy Azure-függvény létrehozása
Customer intent: As a student, I want to be able to create a HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
services: functions
documentationcenter: na
author: alexkarcher-msft
manager: ggailey777
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: 5fd9f51185b8461269d498b254d9e9d9f4118270
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344606"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Az Azure diákoknak szóló ajánlatához függvény létrehozása

Ebben az oktatóanyagban létre fogunk hozni egy hello world HTTP függvényt az Azure diákoknak kezdő csomag előfizetését. Is végigvezetjük érhető el az Azure Functions az ezt az előfizetéstípust.

A Microsoft *Azure diákoknak szóló ajánlatához* megkezdheti az ingyenes felhőbeli fejlesztéshez szükséges Azure-termékekkel. [További információ itt ezt az ajánlatot.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Az Azure Functions lehetővé teszi a kód [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) környezetben történő végrehajtását anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást. [További információ a Functionsről itt.](./functions-overview.md)

## <a name="create-a-function"></a>Függvény létrehozása

 Ebben a témakörben megtudhatja, hogyan használhatja a Functions egy "Hello world" HTTP által aktivált függvény létrehozása az Azure Portalon.

![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, telepítése és megosztása érdekében. 

1. Válassza az Azure Portal bal felső sarkában található **Új** gombot, majd válassza a **Számítás** > **Függvényalkalmazás** lehetőséget.

    ![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-student-starter/function-app-create-flow.png)

2. Használja az ábra alatti táblázatban megadott függvényalkalmazás-beállításokat.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. | 
    | **[Erőforráscsoport](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
   | **[Az App Service-csomag/hely](./functions-scale.md)** | Új | A csomag, hogy szabályozza a függvényalkalmazás üzembe régióját és az erőforrások effektus sűrűségét. Több Függvényalkalmazás ugyanabban a csomagban telepített összes oszt egyetlen ingyenes ugyanezen példányában. Ez a korlátozás a tanulók terv. A teljes üzemeltetési lehetőségek a következők [itt ismertetése.](./functions-scale.md)|
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. |
    |**[Application Insights](./functions-monitoring.md)**| Engedélyezve | Az Application Insights segítségével tárolhatja és elemezheti a függvényalkalmazás naplókat. Ez alapértelmezés szerint engedélyezve van, ha úgy dönt, hogy olyan helyre, amely támogatja az Application Insights. Az Application Insights esetén is engedélyezhető függvényeket egy közeli régiót manuálisan kiválasztásával az Application Insights üzembe helyezéséhez. Az Application Insights, anélkül csak lesz élő streamelési naplók megtekintéséhez.

3. Válassza ki **App Service-csomag/hely** válasszon másik helyet a fenti

4. Válassza ki **új létrehozása** majd adni a csomag egy egyedi nevet.

5. Válassza ki az Önhöz legközelebbi helyet. [Tekintse meg a teljes térképet, az Azure-régiók itt.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

    ![Új függvényalkalmazás-beállítások megadása](./media/functions-create-student-starter/function-app-create-notification.png)

8. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget.

> [!TIP]
> Ha nem találja a portálon a függvényalkalmazásokat, próbáljon [Függvényalkalmazásokat felvenni a kedvencek közé az Azure Portalon](./functions-how-to-use-azure-function-app-settings.md#favorite).
Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

## <a name="create-function"></a>HTTP által aktivált függvény létrehozása

1. Bontsa ki az új függvényalkalmazást, majd kattintson a **Függvények** elem melletti **+** gombra, és válassza a **Portálba épített**, majd a **Folytatás** lehetőséget.

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

## <a name="supported-features-in-azure-for-students-starter"></a>Támogatott szolgáltatások az Azure diákoknak kezdő csomag

Az Azure tanulók számára hozzáférés leggyakoribb funkciói az Azure Functions-futtatókörnyezet, az alábbiakban néhány kulcsfontosságú korlátozásokkal rendelkezik:

* A HTTP-eseményindítóval jelenleg az egyetlen eseményindító támogatott.
    * Az összes bemeneti, és az összes kimeneti kötések támogatottak! [Tekintse meg a teljes listát.](functions-triggers-bindings.md)
* Támogatott nyelvek: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F# (.NET Core 2)
    * [Tekintse meg az ide-magasabb szintű csomagokban támogatott nyelvek](supported-languages.md)
* Windows az egyetlen támogatott operációs rendszer.
* Méretezési csoport korlátozódik [egy ingyenes szintű példány](https://azure.microsoft.com/pricing/details/app-service/windows/) akár 60 perc minden nap futtatással. Ön serverlessly skálázza 0, 1 példány automatikusan HTTP-adatforgalom érkezik, de további.
* Csak [a 2.x verziójú futtatókörnyezet](functions-versions.md) használata támogatott.
* Az összes Fejlesztőeszközök függvények közzétételéhez és a Szerkesztés támogatott. Ez magában foglalja a VS Code, Visual Studio, az Azure CLI és az Azure Portalon. Ha szeretné használni a portál csakis, szüksége lesz, először hozzon létre egy alkalmazást a portálon, és a kívánt eszközben központi telepítés céljaként válassza, amelyet az alkalmazás.

## <a name="next-steps"></a>További lépések

Egy függvényalkalmazást egy egyszerű HTTP-triggert használó függvényt hozott létre! Most már megvizsgálhatja helyi hibakeresését, további nyelveket, figyelés és integrációk.

 * [Az első függvény létrehozása a Visual Studio használatával](./functions-create-your-first-function-visual-studio.md)
 * [A Visual Studio Code az első függvény létrehozása](./functions-create-first-function-vs-code.md)
 * [Az Azure Functions JavaScript-fejlesztői útmutató](./functions-reference-node.md)
 * [Csatlakozás az Azure SQL Database az Azure Functions használatával](./functions-scenario-database-table-cleanup.md)
 * [További információ az Azure Functions – HTTP-kötések](./functions-bindings-http-webhook.md).
 * [Az Azure Functions monitorozása](./functions-monitoring.md)
