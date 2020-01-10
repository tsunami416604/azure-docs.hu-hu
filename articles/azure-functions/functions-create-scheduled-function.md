---
title: Azure-beli ütemterven futó függvény létrehozása
description: Megtudhatja, hogyan hozhat olyan függvényt az Azure-ban, amely az Ön által meghatározott ütemezés alapján fut.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: quickstart
ms.date: 03/28/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 808f0f81f937da688a8873e5f6ee959976e9d6aa
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769285"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Időzítő által aktivált függvény létrehozása az Azure-ban

Megtudhatja, hogyan használhatja a Azure Functionst olyan [kiszolgáló](https://azure.microsoft.com/solutions/serverless/) nélküli függvény létrehozásához, amely az Ön által meghatározott ütemterv alapján fut.

![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

+ Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![A függvényalkalmazás elkészült.](./media/functions-create-first-azure-function/function-app-create-success.png)

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Időzítő által aktivált függvény létrehozása

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Ha ez az első függvény a függvényalkalmazásban, válassza a **Portálba épített**, majd a **Folytatás** lehetőséget. Ellenkező esetben folytassa a 3. lépéssel.

   ![Függvények gyors létrehozásának oldala az Azure Portalon](./media/functions-create-scheduled-function/function-app-quickstart-choose-portal.png)

2. Válassza ki a **További sablonok**, majd a **Befejezés és sablonok megtekintése** lehetőséget.

    ![Függvények rövid útmutatója - további sablonok kiválasztása](./media/functions-create-scheduled-function/add-first-function.png)

3. A keresőmezőbe írja be a `timer`t, és konfigurálja az új triggert a rendszerkép alatti táblázatban megadott beállításokkal.

    ![Hozzon létre egy időzítő által aktivált függvényt az Azure Portalon.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Name (Név)** | Alapértelmezett | Az időzítő által aktivált függvény nevét adja meg. |
    | **Ütemezés** | 0 \*/1 \* \* \* \* | Hat mezőből álló [CRON-kifejezés](functions-bindings-timer.md#ncrontab-expressions), amely úgy ütemezi a függvényt, hogy minden percben fusson. |

4. Kattintson a **Create** (Létrehozás) gombra. A függvény a választott nyelven jön létre, amely percenként, a percben fut.

5. Ellenőrizze a végrehajtást a naplókban gyűjtött nyomkövetési adatok áttekintésével.

    ![A függvények naplómegtekintője az Azure Portalon.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Most módosíthatja a függvény ütemezését, hogy ne percenként, hanem óránként egyszer fusson.

## <a name="update-the-timer-schedule"></a>Az időzítő ütemezésének módosítása

1. Bontsa ki a függvényt, és kattintson az **Integráció** elemre. Itt határozhatja meg a függvény bemeneti és kimeneti kötéseit, és itt állíthatja be az ütemezést is. 

2. Adja meg az új `0 0 */1 * * *` értéket az óránkénti **Ütemezés** beállításnál, és kattintson a **Mentés** elemre.  

![A függvények időzítési ütemezésének módosítása az Azure Portalon.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Mostantól óránként egyszer fut a függvény.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Létrehozott egy ütemezés alapján futó függvényt. Az időzítő eseményindítókkal kapcsolatos további információkért lásd: [kód végrehajtásának elvégzése Azure Functionssal](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
