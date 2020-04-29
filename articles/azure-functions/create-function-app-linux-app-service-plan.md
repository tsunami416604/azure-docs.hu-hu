---
title: Function-alkalmazás létrehozása Linux rendszeren a Azure Portal
description: Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure Portalon.
ms.topic: how-to
ms.date: 02/28/2019
ms.openlocfilehash: b2de36faf07ad661ff8817adc48b726f54990ceb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80754117"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Function-alkalmazás létrehozása Linux rendszeren egy Azure App Service tervben

Az Azure Functions lehetővé teszi, hogy a függvényeit Linux rendszerben egy alapértelmezett Azure App Service-tárolóban üzemeltesse. Ez a cikk bemutatja, hogyan használható a [Azure Portal](https://portal.azure.com) egy olyan Linux-alapú Function-alkalmazás létrehozásához, amely egy [app Service](functions-scale.md#app-service-plan)-csomagban fut. [Használhatja saját egyéni tárolóját](functions-create-function-linux-custom-image.md) is.

![Függvényalkalmazás létrehozása az Azure Portalon](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények Linux rendszerben való végrehajtásának biztosításához. A függvényalkalmazás szolgáltat környezetet a függvénykód végrehajtásához. Lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése, skálázása és megosztása érdekében. Ebben a cikkben egy App Service tervet hoz létre a Function-alkalmazás létrehozásakor.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra, majd válassza a **Számítás** > **Függvényalkalmazás** lehetőséget.

    ![Függvényalkalmazás létrehozása az Azure Portalon](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Használja az ábra alatti táblázatban megadott függvényalkalmazás-beállításokat.

    ![Új függvényalkalmazás-beállítások megadása](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. | 
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
    | **OS** | Linux | A Function alkalmazás Linux rendszeren fut. |
    | **Közzététel** | Kód | A **futásidejű verem** alapértelmezett Linux-tárolóját használja a rendszer. Csak a Function app-projekt kódját kell megadnia. Egy másik lehetőség egy egyéni [Docker-rendszerkép](functions-create-function-linux-custom-image.md)közzététele. |
    | **[Üzemeltetési csomag](functions-scale.md)** | App Service-csomag | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Ha App Service csomagot futtat, akkor szabályozhatja a [Function alkalmazás skálázását](functions-scale.md).  |
    | **App Service csomag/hely** | Csomag létrehozása | Válassza az **új létrehozása** elemet, és adjon meg egy **app Service-csomag** nevét. Válasszon egy **helyet** az Ön közelében lévő [régióban](https://azure.microsoft.com/regions/) vagy a funkciókhoz hozzáférő egyéb szolgáltatások közelében. Válassza ki a kívánt **[árképzési szintet](https://azure.microsoft.com/pricing/details/app-service/linux/)**. <br/>A Linux és a Windows rendszerű függvények alkalmazásai nem futtathatók ugyanabban a App Service tervben. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. |
    | **[Storage](../storage/common/storage-account-create.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amennyiben az megfelel a [tárfiókokra vonatkozó követelményeknek](storage-considerations.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Engedélyezve | A Application Insights alapértelmezés szerint le van tiltva. Javasoljuk, hogy most engedélyezze a Application Insights integrációt, és válasszon egy üzemeltetési helyet a App Service-csomag helyének közelében. Ha ezt később szeretné elvégezni, tekintse meg a [Azure functions figyelése](functions-monitoring.md)című témakört.  |

3. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

4. Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

    ![Új függvényalkalmazás-beállítások megadása](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget.

Ezután létrehozhat egy függvényt az új függvényalkalmazásban. Még a Function app elérhetővé tétele után is eltarthat néhány percig, hogy teljesen inicializálható legyen.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>HTTP által aktivált függvény létrehozása

Ebből a szakaszból megtudhatja, hogyan hozhat létre függvényt az új Function alkalmazásban a portálon.

> [!NOTE]
> A portál fejlesztői felülete hasznos lehet a Azure Functions kipróbálásához. A legtöbb esetben érdemes lehet helyileg fejleszteni a függvényeket, és a [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) vagy a [Azure functions Core Tools](functions-run-local.md#create-a-local-functions-project)használatával közzétenni a projektet a Function alkalmazásban.  

1. Az új függvény alkalmazásban válassza az **Áttekintés** fület, majd a teljes kiválasztást követően válassza az **+ új függvény**lehetőséget.

    ![Új függvény létrehozása az Áttekintés lapról](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. A rövid **útmutató lapon válassza** **a portálon**, majd a **Folytatás**lehetőséget.

    ![Válassza ki a Function Development platformot.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. Válassza a **WebHook + API**, majd a **Létrehozás** lehetőséget.

    ![A függvények gyors létrehozása az Azure Portalon.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

A rendszer létrehoz egy függvényt egy HTTP által indított függvény nyelvspecifikus sablonjának használatával.

Mostantól egy HTTP-kérelem küldésével futtathatja az új függvényt.

## <a name="test-the-function"></a>A függvény tesztelése

1. Az új függvényben kattintson a **</> Függvény URL-címének beolvasása** elemre a jobb felső sarokban, válassza a **default (Function key)** (alapértelmezett (funkcióbillentyű)) lehetőséget, majd kattintson a **Másolás** gombra. 

    ![A függvény URL-címének másolása az Azure portálról](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

2. Illessze be a függvény URL-címét a böngésző címsorába. Az URL-cím végéhez adja hozzá a `&name=<yourname>` lekérdezési sztring értéket, majd nyomja le az `Enter` billentyűt a billentyűzeten a kérés végrehajtásához. Ekkor a függvény által visszaadott válasz jelenik meg a böngészőben.  

    Az alábbi példa a böngészőben visszaadott választ mutatja:

    ![A függvény által visszaadott válasz a böngészőben.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    A kérelem URL-címe alapértelmezés szerint tartalmazza a függvény HTTP protokollon keresztüli eléréséhez szükséges kulcsot.

3. A függvény futásakor a rendszer nyomkövetési adatok ír a naplókba. Az előző végrehajtás nyomkövetési adatainak megtekintéséhez térjen vissza a függvényhez a portálon, és kattintson a képernyő alján található nyílra a **Naplók** elem kibontásához.

   ![A függvények naplómegtekintője az Azure Portalon.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Létrehozott egy függvényalkalmazást és egy HTTP által aktivált egyszerű függvényt.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

További információt az [Azure Functions HTTP-kötéseit](functions-bindings-http-webhook.md) ismertető cikkben talál.
