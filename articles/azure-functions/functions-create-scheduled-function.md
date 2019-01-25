---
title: Ütemezés szerint futó függvények létrehozása az Azure-ban | Microsoft Docs
description: Megtudhatja, hogyan hozhat olyan függvényt az Azure-ban, amely az Ön által meghatározott ütemezés alapján fut.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: a6b1e4e1571e6ce3cee1658907efd35e9c73ca1a
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903395"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Időzítő által aktivált függvény létrehozása az Azure-ban

Tekintse át, hogyan használhatja az Azure Functions szolgáltatást olyan [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) függvény létrehozására, amely az Ön által meghatározott ütemezés alapján fut.

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

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Ha ez az első függvény a függvényalkalmazásban, válassza a **Portálba épített**, majd a **Folytatás** lehetőséget. Ha nem ez az első, folytassa a harmadik lépéssel.

   ![Függvények gyors létrehozásának oldala az Azure Portalon](./media/functions-create-scheduled-function/function-app-quickstart-choose-portal.png)

2. Válassza ki a **További sablonok**, majd a **Befejezés és sablonok megtekintése** lehetőséget.

    ![Függvények rövid útmutatója - további sablonok kiválasztása](./media/functions-create-scheduled-function/add-first-function.png)

3. A keresőmezőbe írja be a `timer` és az új eseményindítót konfigurálja az ábra alatti táblázatban megadott beállításokkal.

    ![Hozzon létre egy időzítő által aktivált függvényt az Azure Portalon.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Name (Név)** | Alapértelmezett | Az időzítő által aktivált függvény nevét adja meg. |
    | **Ütemezés** | 0 \*/1 \* \* \* \* | Hat mezőből álló [CRON-kifejezés](functions-bindings-timer.md#cron-expressions), amely úgy ütemezi a függvényt, hogy minden percben fusson. |

4. Kattintson a **Create** (Létrehozás) gombra. Létrejön egy függvény a választott nyelven, amely minden percben futni fog.

5. Ellenőrizze a végrehajtást a naplókban gyűjtött nyomkövetési adatok áttekintésével.

    ![A függvények naplómegtekintője az Azure Portalon.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Most módosíthatja a függvény ütemezését, hogy ne percenként, hanem óránként egyszer fusson.

## <a name="update-the-timer-schedule"></a>Az időzítő ütemezésének módosítása

1. Bontsa ki a függvényt, és kattintson az **Integráció** elemre. Itt határozhatja meg a függvény bemeneti és kimeneti kötéseit, és itt állíthatja be az ütemezést is. 

2. Adja meg az új `0 0 */1 * * *` értéket az óránkénti **Ütemezés** beállításnál, és kattintson a **Mentés** elemre.  

![A függvények időzítési ütemezésének módosítása az Azure Portalon.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

A függvény ezután óránként egyszer fog futni. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Létrehozott egy ütemezés alapján futó függvényt. Az időzítési eseményindítóktól kapcsolatos további információkért lásd: [kódvégrehajtás ütemezése az Azure Functions](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
