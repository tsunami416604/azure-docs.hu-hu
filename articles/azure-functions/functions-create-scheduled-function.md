---
title: "Ütemezés szerint futó függvények létrehozása az Azure-ban | Microsoft Docs"
description: "Megtudhatja, hogyan hozhat olyan függvényt az Azure-ban, amely az Ön által meghatározott ütemezés alapján fut."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 1478a2eedad496d3113fef28920d10859d11b1ce
ms.contentlocale: hu-hu
ms.lasthandoff: 05/12/2017


---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Időzítő által aktivált függvény létrehozása az Azure-ban

Tekintse át, hogyan használhatja az Azure Functions szolgáltatást olyan függvény létrehozására, amely az Ön által meghatározott ütemezés alapján fut.

![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![A függvényalkalmazás elkészült.](./media/functions-create-first-azure-function/function-app-create-success.png)

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Időzítő által aktivált függvény létrehozása

1. Bontsa ki a függvényalkalmazást, kattintson a **Függvények** elem melletti a **+** gombra, és kattintson a kívánt nyelvű **TimerTrigger** sablonra. Ezután használja a táblázatban megadott beállításokat, és kattintson a **Létrehozás** gombra:

| Beállítás | Ajánlott érték | Leírás |
|---|---|---|
| **A függvény neve** | TimerTriggerCSharp1 | Az időzítő által aktivált függvény nevét adja meg. |
| **[Ütemezés](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 \*/1 \* \* \* \* | Hat mezőből álló [CRON-kifejezés](http://en.wikipedia.org/wiki/Cron#CRON_expression), amely úgy ütemezi a függvényt, hogy minden percben fusson. |

Létrejön egy függvény a választott nyelven, amely minden percben futni fog.

1. Ellenőrizze a végrehajtást a naplókban gyűjtött nyomkövetési adatok áttekintésével.

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
