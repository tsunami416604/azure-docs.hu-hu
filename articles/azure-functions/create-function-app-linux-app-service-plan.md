---
title: Függvényalkalmazás létrehozása Linux rendszeren az Azure Portalról |} A Microsoft Docs
description: Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure Portalon.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: glenga
ms.custom: ''
ms.openlocfilehash: cc99bc4345c388f22e72957590f3917a85e214e0
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339808"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Egy függvényalkalmazás létrehozása a linuxon futó Azure App Service-csomag

Az Azure Functions lehetővé teszi, hogy a függvényeit Linux rendszerben egy alapértelmezett Azure App Service-tárolóban üzemeltesse. Ez a cikk bemutatja, hogyan használata a [az Azure portal](https://portal.azure.com) egy futó Linux-ban üzemeltetett függvényalkalmazás létrehozásához egy [App Service-csomag](functions-scale.md#app-service-plan). [Használhatja saját egyéni tárolóját](functions-create-function-linux-custom-image.md) is.

![Függvényalkalmazás létrehozása az Azure Portalon](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények Linux rendszerben való végrehajtásának biztosításához. A függvényalkalmazás szolgáltat környezetet a függvénykód végrehajtásához. Lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében. Ebben a cikkben létrehozott App Service-csomag a függvényalkalmazás létrehozásakor.

1. Válassza ki a **erőforrás létrehozása** gomb megtalálható az Azure portal bal felső sarkában majd válassza ki **számítási** > **Függvényalkalmazás**.

    ![Függvényalkalmazás létrehozása az Azure Portalon](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Használja az ábra alatti táblázatban megadott függvényalkalmazás-beállításokat.

    ![Új függvényalkalmazás-beállítások megadása](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. | 
    | **[Erőforráscsoport](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
    | **OS** | Linux | A függvényalkalmazás a Linux rendszeren futó. |
    | **Közzététel** | Kód | Az alapértelmezett Linux-tárolót a **futtatókörnyezeti verem** szolgál. Meg kell adnia az összes, a függvény projekt kódját. Egy másik lehetőség az, hogy közzététele egy egyéni [Docker-rendszerkép](functions-create-function-linux-custom-image.md). |
    | **[Szolgáltatási csomag](functions-scale.md)** | App Service-csomag | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Az App Service-csomag futtatásakor szabályozhatja a [méretezése a függvényalkalmazás](functions-scale.md).  |
    | **Az App Service-csomag/hely** | Csomag létrehozása | Válasszon **új létrehozása** , és adja meg egy **App Service-csomag** nevét. Válasszon egy **hely** a egy [régió](https://azure.microsoft.com/regions/) Önhöz vagy más szolgáltatások közelében a függvények által elért. Válassza ki a kívánt  **[tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/linux/)**. <br/>Az App Service-csomag Linux- és Windows függvényalkalmazások nem futtatható. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. [Podpora Pythonu](functions-reference-python.md) jelenleg előzetes verzióban érhető el. |
    | **[Tárolás](../storage/common/storage-quickstart-create-account.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amennyiben az megfelel a [tárfiókokra vonatkozó követelményeknek](functions-scale.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Engedélyezve | Az Application Insights alapértelmezés szerint le van tiltva. Javasoljuk, hogy most már az Application Insights-integráció engedélyezése és az App Service-csomag hely közelében üzemeltetési hely kiválasztása. Ha később szeretné, tekintse meg [figyelése az Azure Functions](functions-monitoring.md).  |

3. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

4. Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

    ![Új függvényalkalmazás-beállítások megadása](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget.

> [!TIP]
> Ha nem találja a portálon a függvényalkalmazásokat, próbáljon [Függvényalkalmazásokat felvenni a kedvencek közé az Azure Portalon](functions-how-to-use-azure-function-app-settings.md#favorite).

Ezután létrehozhat egy függvényt az új függvényalkalmazásban. Érhető el a függvényalkalmazást, után is igénybe vehet néhány percet a teljes inicializálására.

## <a name="create-function"></a>HTTP által aktivált függvény létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre olyan függvényt a portálon az új függvényalkalmazásban.

> [!NOTE]
> A portál fejlesztési környezetet biztosít az Azure Functions kipróbálásához hasznos lehet. A legtöbb esetben érdemes lehet a függvények helyi fejlesztés és a projekt közzététele a függvényalkalmazáshoz segítségével [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) vagy a [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. Az új függvényalkalmazást, válassza a **áttekintése** lapra, és teljesen betölti után válassza **+ új funkció**.

    ![Új függvény létrehozása az Áttekintés lap](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. Az a **rövid** lapra, majd **a portálon**, és válassza ki **Folytatás**.

    ![Válassza ki a függvény fejlesztési platform.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

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
