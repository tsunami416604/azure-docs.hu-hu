---
title: "Ütemezés szerint futó függvények létrehozása az Azure-ban | Microsoft Docs"
description: "Megtudhatja, hogyan hozhat olyan függvényt az Azure-ban, amely az Ön által meghatározott ütemezés alapján fut."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/31/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 476e103c7101621e116c5155241f56f1cb9036df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Időzítő által aktivált függvény létrehozása az Azure-ban

Tekintse át, hogyan használhatja az Azure Functions szolgáltatást olyan függvény létrehozására, amely az Ön által meghatározott ütemezés alapján fut.

![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

+ Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![A függvényalkalmazás elkészült.](./media/functions-create-first-azure-function/function-app-create-success.png)

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Időzítő által aktivált függvény létrehozása

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Ha ez az első függvény a függvényalkalmazásban, jelölje ki az **Egyéni függvény** lehetőséget. Ez megjeleníti a függvénysablonok teljes készletét.

    ![Függvények gyors létrehozásának oldala az Azure Portalon](./media/functions-create-scheduled-function/add-first-function.png)

2. Kattintson a kívánt nyelvhez tartozó **TimerTrigger** sablonra. Ezt követően használja a táblázatban megadott beállításokat:

    ![Hozzon létre egy időzítő által aktivált függvényt az Azure Portalon.](./media/functions-create-scheduled-function/functions-create-timer-trigger.png)

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **A függvény neve** | TimerTriggerCSharp1 | Az időzítő által aktivált függvény nevét adja meg. |
    | **[Ütemezés](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 \*/1 \* \* \* \* | Hat mezőből álló [CRON-kifejezés](http://en.wikipedia.org/wiki/Cron#CRON_expression), amely úgy ütemezi a függvényt, hogy minden percben fusson. |

2. Kattintson a **Létrehozás** gombra. Létrejön egy függvény a választott nyelven, amely minden percben futni fog.

3. Ellenőrizze a végrehajtást a naplókban gyűjtött nyomkövetési adatok áttekintésével.

    ![A függvények naplómegtekintője az Azure Portalon.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Most módosíthatja a függvény ütemezését, hogy ritkábban, például óránként egyszer fusson. 

## <a name="update-the-timer-schedule"></a>Az időzítő ütemezésének módosítása

1. Bontsa ki a függvényt, és kattintson az **Integráció** elemre. Itt határozhatja meg a függvény bemeneti és kimeneti kötéseit, és itt állíthatja be az ütemezést is. 

2. Adja meg az új `0 0 */1 * * *` értéket az **Ütemezés** beállításnál, és kattintson a **Mentés** gombra.  

![A függvények időzítési ütemezésének módosítása az Azure Portalon.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

A függvény ezután óránként egyszer fog futni. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Létrehozott egy ütemezés alapján futó függvényt.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

További információ az időzítési eseményindítóktól: [A kódvégrehajtás ütemezése az Azure Functions szolgáltatásban](functions-bindings-timer.md).