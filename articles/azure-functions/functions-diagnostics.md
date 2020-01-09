---
title: Azure Functions diagnosztika áttekintése
description: Ismerje meg, hogy miként lehet elhárítani a Function alkalmazással kapcsolatos problémákat Azure Functions diagnosztikát.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 29f90508bf0bdd77f02d78cbcba8fdfe7009f0d2
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562051"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure Functions diagnosztika áttekintése

Ha egy Function alkalmazást futtat, elő kell készítenie az esetlegesen felmerülő problémákra a hibák 4xx. A Azure Functions Diagnostics egy intelligens és interaktív megoldás, amellyel a Function-alkalmazás nem konfigurálható és nem használható fel külön díj nélkül. Ha a Function alkalmazással kapcsolatos problémákba ütközik, Azure Functions diagnosztika kimutatja, mi a baj, hogy a megfelelő információkhoz vezessen, hogy könnyebben és gyorsan hárítsa el a problémát, és hárítsa el azokat. Ebből a cikkből megtudhatja, hogyan használhatja a Azure Functions diagnosztikát a gyakori Function app-problémák gyors diagnosztizálásához és megoldásához.

## <a name="start-azure-functions-diagnostics"></a>Azure Functions diagnosztika elindítása

Azure Functions diagnosztika elérése:

1. Navigáljon a Function alkalmazáshoz a [Azure Portal](https://portal.azure.com).
2. Válassza ki a **platform szolgáltatásai** lapot.
3. Válassza a **diagnosztizálás és problémák megoldása** az **Erőforrás-kezelés**alatt lehetőséget, amely megnyitja Azure functions diagnosztikát.
4. Válassza ki azt a kategóriát, amely a legjobban leírja a függvény alkalmazásának hibáját a Kezdőlap csempén található kulcsszavak használatával. Olyan kulcsszót is beírhat, amely a legjobban leírja a problémát a keresősávban. Például beírhatja `execution` a Function app-végrehajtáshoz kapcsolódó diagnosztikai jelentések listájának megjelenítéséhez, és közvetlenül a kezdőlapról nyithatja meg őket.

![Kezdőlap](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Az interaktív felület használata

Miután kiválasztott egy olyan Kezdőlap kategóriát, amely a legjobban megfelel a Function app problémájának, Azure Functions Diagnostics interaktív felülete, a Genie, végigvezeti Önt az alkalmazás problémáinak diagnosztizálásán és megoldásán. A Genie által biztosított csempe-parancsikonok segítségével megtekintheti a problémás kategória teljes diagnosztikai jelentését, amelyre kíváncsi. A csempék parancsikonjai közvetlen módot biztosítanak a diagnosztikai metrikák elérésére.

![Genie](./media/functions-diagnostics/genie.png)

A csempék kiválasztása után megtekintheti a csempén bemutatott problémával kapcsolatos témakörök listáját. Ezek a témakörök a teljes jelentésből származó jelentős információk részleteit tartalmazzák. A témakörök bármelyikét kiválaszthatja a további problémák kivizsgálásához. Azt is megteheti, hogy a **teljes jelentés megtekintése** lehetőségre kattintva egyetlen oldalon tekintheti meg az összes témakört.

![Diagnosztikai jelentés előnézete](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Diagnosztikai jelentés megtekintése

Miután kiválasztott egy témakört, megtekintheti a Function alkalmazásra jellemző diagnosztikai jelentést. A diagnosztikai jelentések állapotjelző ikonokkal jelzik, hogy van-e konkrét probléma az alkalmazással kapcsolatban. Megtekintheti a probléma részletes leírását, az ajánlott műveleteket, a kapcsolódó mérőszámokat és a hasznos dokumentumokat. A rendszer a testreszabott diagnosztikai jelentéseket a Function alkalmazásban futó ellenőrzések sorozatából hozza létre. A diagnosztikai jelentések hasznos eszköz lehet a Function-alkalmazásban felmerülő problémák azonosításához és a probléma megoldásához.

## <a name="find-the-problem-code"></a>A probléma kódjának megkeresése 

A parancsfájl-alapú függvények esetében a **függvény végrehajtása** és a **hibák** **függvényalkalmazás legördülő menüben, illetve a hibák bejelentése** a kódban a kivételeket vagy hibákat okozó sorokra szűkíthető. Ez a funkció hasznos eszköz lehet a kiváltó ok beszerzéséhez és a hibák adott kódból való kijavításához. Ez a beállítás nem érhető el előre C# lefordított és Java-függvényekhez.

![Diagnosztikai jelentés a függvények végrehajtásával kapcsolatos hibákról](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Függvény kivétele](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Következő lépések

Kérdéseket tehet fel, és visszajelzést küldhet Azure Functions diagnosztikát a következő címen: [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Kérjük, vegye fel `[Diag]` a visszajelzés címébe.

> [!div class="nextstepaction"]
> [A Function apps figyelése](functions-monitoring.md)
