---
title: Függvény létrehozása az Azure diákoknak kezdőknek
description: Ismerje meg, hogyan hozhat létre Azure-függvényt egy Azure-beli Student Starter-előfizetés keretében
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: how-to
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: c7dd88bf0ead558a0c4951baf38543566d805caa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756472"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Függvény létrehozása az Azure diákoknak kezdőknek

Ebben az oktatóanyagban létrehozunk egy "Hello World" HTTP-függvényt egy Azure diákoknak kezdő előfizetéshez. Az előfizetéshez tartozó Azure Functions elérhetővé tételeit is megismerheti.

A Microsoft *Azure diákoknak kezdő* csomaggal a felhőben való fejlesztéshez szükséges Azure-termékeket ingyenesen elsajátíthatja. [Ebben az ajánlatban itt találhat további információt.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Az Azure Functions lehetővé teszi a kód [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) környezetben történő végrehajtását anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást. [További információ a functions szolgáltatásról.](./functions-overview.md)

## <a name="create-a-function"></a>Függvény létrehozása

 Ebből a témakörből megtudhatja, hogyan használhatók a függvények a HTTP által aktivált "Hello World" függvény létrehozásához a Azure Portal.

![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése, skálázása és megosztása érdekében.

1. Válassza a Azure Portal bal felső sarkában található **erőforrás létrehozása** gombot. Ezután válassza a **számítási** > **függvényalkalmazás**elemet.

    ![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-student-starter/function-app-create-flow.png)

2. Használja az ábra alatti táblázatban megadott függvényalkalmazás-beállításokat.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
   | **[App Service csomag/hely](./functions-scale.md)** | Új | A üzemeltetési terv, amely azt szabályozza, hogy a Function alkalmazás mely régiót helyezi üzembe, és az erőforrások sűrűségét. Az ugyanarra a csomagra telepített több Function-alkalmazás mindegyike ugyanazzal az ingyenes példánnyal fog osztozni. Ez a Student Starter-terv korlátozása. [Itt részletesen ismertetjük](./functions-scale.md) a teljes üzemeltetési lehetőségeket.|
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. |
    |**[Application Insights](./functions-monitoring.md)**| Engedélyezve | Application Insights a függvény alkalmazás naplófájljainak tárolására és elemzésére szolgál. Alapértelmezés szerint engedélyezve van, ha olyan helyet választ, amely támogatja a Application Insights. Application Insights bármely függvényhez engedélyezhető, ha manuálisan kiválasztja a közeli régiót Application Insights üzembe helyezéséhez. Application Insights nélkül csak az élő adatfolyam-naplókat fogja tudni megtekinteni.

3. Válassza a fenti **app Service terv/hely** lehetőséget egy másik hely kiválasztásához

4. Válassza az **új létrehozása** lehetőséget, majd adjon meg egy egyedi nevet a tervnek.

5. Válassza ki az Önhöz legközelebb eső helyet. [Itt megtekintheti az Azure-régiók teljes térképét.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

    ![Új függvényalkalmazás-beállítások megadása](./media/functions-create-student-starter/function-app-create-notification.png)

8. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget.

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>HTTP által aktivált függvény létrehozása

1. Bontsa ki az új Function alkalmazást, majd **+** kattintson a **függvények**elem melletti gombra, válassza a **portálon**, majd a **Folytatás**lehetőséget.

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

## <a name="supported-features-in-azure-for-students-starter"></a>Az Azure diákoknak kezdőknek támogatott szolgáltatásai

Az Azure diákoknak kezdőknek hozzáférése van a Azure Functions futtatókörnyezet legtöbb funkciójának nagy részéhez, és több, az alábbiakban felsorolt kulcsfontosságú korlátozással rendelkezik:

* A HTTP-trigger az egyetlen támogatott trigger-típus.
    * Minden bemenet és minden kimeneti kötés támogatott! [Itt tekintheti meg a teljes listát.](functions-triggers-bindings.md)
* Támogatott nyelvek: 
    * C# (.NET Core 2)
    * JavaScript (node. js 8 & 10)
    * F # (.NET Core 2)
    * [A magasabb csomagokban támogatott nyelveket itt tekintheti meg](supported-languages.md)
* A Windows az egyetlen támogatott operációs rendszer.
* A skála egy ingyenes, legfeljebb 60 percet futtató, napi szinten üzemelő [példányra](https://azure.microsoft.com/pricing/details/app-service/windows/) korlátozódik. A rendszer automatikusan 0 – 1 példányra serverlessly a HTTP-forgalom fogadása után, de még nem.
* Csak a functions futtatókörnyezet [2. x vagy újabb verziója](functions-versions.md) támogatott.
* Az összes fejlesztői szerszámozás támogatott szerkesztési és közzétételi függvények esetén. Ide tartozik a VS Code, a Visual Studio, az Azure CLI és a Azure Portal. Ha a portálon kívül bármilyen más eszközt szeretne használni, először létre kell hoznia egy alkalmazást a portálon, majd az alkalmazást központi telepítési célként kell kiválasztania az előnyben részesített eszközön.

## <a name="next-steps"></a>További lépések

Létrehozta a Function alkalmazást egy egyszerű HTTP-triggerrel rendelkező függvénnyel. Most már megismerheti a helyi eszközöket, a több nyelvet, a figyelést és az integrációt.

 * [Az első függvény létrehozása a Visual Studio használatával](./functions-create-your-first-function-visual-studio.md)
 * [Az első függvény létrehozása a Visual Studio Code használatával](./functions-create-first-function-vs-code.md)
 * [Azure Functions JavaScript fejlesztői útmutató](./functions-reference-node.md)
 * [Azure Functions használata Azure SQL Databasehoz való kapcsolódáshoz](./functions-scenario-database-table-cleanup.md)
 * [További információ a Azure FUNCTIONS http-kötésekről](./functions-bindings-http-webhook.md).
 * [A Azure Functions figyelése](./functions-monitoring.md)
