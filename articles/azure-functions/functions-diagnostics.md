---
title: Az Azure Functions diagnosztika áttekintése
description: Ismerje meg, hogyan háríthatja el a függvényalkalmazással kapcsolatos problémákat az Azure Functions diagnosztikával.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834047"
---
# <a name="azure-functions-diagnostics-overview"></a>Az Azure Functions diagnosztika áttekintése

Amikor egy függvényalkalmazást futtat, fel szeretne készülni az esetlegesen felmerülő problémákra, a 4xx hibákból a hibák kiváltásához. Az Azure Functions diagnosztika egy intelligens és interaktív élmény, amely segít a függvényalkalmazás konfiguráció és többletköltség nélküli hibaelhárításában. Amikor problémákba ütközik a függvényalkalmazással, az Azure Functions diagnosztikája rámutat arra, hogy mi a baj, hogy a megfelelő információkhoz vezessen, hogy könnyebben és gyorsabban elháríthassa és megoldhassa a problémát. Ez a cikk bemutatja az Azure Functions diagnosztika használatával a gyakori függvényalkalmazás-problémák gyorsabb diagnosztizálásához és megoldásához szükséges alapokat.

## <a name="start-azure-functions-diagnostics"></a>Az Azure Functions diagnosztikájának indítása

Az Azure Functions diagnosztikájának elérése:

1. Nyissa meg a függvényalkalmazást az [Azure Portalon.](https://portal.azure.com)
2. Válassza a **Platform funkciói** lapot.
3. Válassza **a Problémák diagnosztizálása és megoldása** lehetőséget az **Erőforrás-kezelés**csoportban, amely megnyitja az Azure Functions diagnosztikát.
4. Válasszon olyan kategóriát, amely a legjobban leírja a függvényalkalmazás kérdését a kezdőlap csempéjének kulcsszavaival. Olyan kulcsszót is beírhat, amely a legjobban leírja a problémát a keresősávban. Beírhatja `execution` például a függvényalkalmazás végrehajtásához kapcsolódó diagnosztikai jelentések listáját, és közvetlenül a kezdőlapról nyithatja meg őket.

![Kezdőlap](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Az interaktív felület használata

Miután kiválasztotta azt a kezdőlapkategóriát, amely a legjobban illeszkedik a függvényalkalmazás problémájához, az Azure Functions diagnosztika interaktív felülete, a Dzsinn végigvezeti önt az alkalmazás problémájának diagnosztizálásán és megoldásán. A Genie által biztosított csempeparancsikonok segítségével megtekintheti az Önt érdeklő problémakategória teljes diagnosztikai jelentését. A csempe parancsikonok közvetlen hozzáférést biztosít a diagnosztikai metrikák.

![Genie](./media/functions-diagnostics/genie.png)

A csempék kiválasztása után megtekintheti a csempén leírt problémával kapcsolatos témakörök listáját. Ezek a témakörök a teljes jelentésből származó jelentős információk töredékeit tartalmazza. A problémák további kivizsgálásához a témakörök bármelyikét kiválaszthatja. A **Teljes jelentés megtekintése** lehetőséget választva az összes témakört egy oldalon találhatja meg.

![A diagnosztikai jelentés előnézete](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Diagnosztikai jelentés megtekintése

Miután kiválasztott egy témakört, megtekintheti a függvényalkalmazásra jellemző diagnosztikai jelentést. A diagnosztikai jelentések állapotikonok segítségével jelzik, hogy vannak-e konkrét problémák az alkalmazással kapcsolatban. A probléma részletes leírása, az ajánlott műveletek, a kapcsolódó mutatók és a hasznos dokumentumok. A testreszabott diagnosztikai jelentések a függvényalkalmazásban futó ellenőrzések sorozatából jönnek létre. A diagnosztikai jelentések hasznos eszközt képezhetnek a funkcióalkalmazás problémáinak azonosításához és a probléma megoldásához való elvezető útmutatóhoz.

## <a name="find-the-problem-code"></a>A problémakód megkeresése

A parancsfájl-alapú függvények, a **függvény végrehajtása és a hibák** a Függvény alkalmazás le vagy **jelentési hibák,** hogy szűkítse le a kódsort okozó kivételek vagy hibák. Ez a funkció hasznos eszköz lehet a kiváltó ok megszerzéséhez és a problémák megoldásához egy adott kódsorból. Ez a beállítás nem érhető el előre lefordított C# és Java függvények esetén.

![Diagnosztikai jelentés a függvényvégrehajtási hibákról](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Függvény kivétel](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>További lépések

Kérdéseket tehet fel, vagy visszajelzést adhat az Azure Functions diagnosztikájáról a [UserVoice](https://feedback.azure.com/forums/355860-azure-functions)webhelyen. Kérjük, olvassa el `[Diag]` a címben a visszajelzést.

> [!div class="nextstepaction"]
> [A funkcióalkalmazások figyelése](functions-monitoring.md)
