---
title: Felhasználói navigációs minták a felhasználó Azure Application insightsban zajló kommunikációról elemzése |} Microsoft docs
description: Elemezze a felhasználók hogyan keresse meg a lapokat és a szolgáltatások webalkalmazás között.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/24/2018
ms.author: mbullwin; daviste
ms.openlocfilehash: 5913039d3ab288c2fc8366073b340538989512ee
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>A felhasználó zajlik az Application Insightsban felhasználói navigációs minták elemzése

![Application Insights felhasználói Forgalomáramlás eszköz](./media/app-insights-usage-flows/00001-flows.png)

A felhasználó Forgalomáramlás eszköz visualizes felhasználók hogyan keresse meg a lapokat és a webhely szolgáltatások között. Fontos nagy hasonló kérdések megválaszolásához:

* Hogyan tegye felhasználók elhagyni a lapot a webhely?
* Mi tegye felhasználók kattintson egy oldalon, a webhely?
* Hol találhatók a helyek, felhasználók kavarog leginkább a helyről?
* Vannak-e helyen, ahol a felhasználók ismétlése ugyanaz a művelet újra és újra?

A felhasználó zajlik az eszköz elindítja egy kezdeti nézet, az egyéni esemény vagy a megadott kivétel. Az első esemény miatt, felhasználói Forgalomáramlás előtt, és ezt követően a felhasználói munkamenet során történt eseményeket jeleníti meg. Különböző vastagsága sornyi hányszor mindegyik elérési út volt követ felhasználók megjelenítése. Különleges **munkamenet kezdete** csomópontok megjelenítése, amelyen a többi csomópont megkezdte a munkamenet. **Munkamenet befejezve** csomópontok megjelenítése, hogy hány felhasználó küldött nincs Lapmegtekintések vagy egyéni események az előző csomópont után kiemelve, ahol felhasználók valószínűleg korábban a helyen.

> [!NOTE]
> Az Application Insights-erőforrás Lapmegtekintések vagy egyéni események a felhasználó Forgalomáramlás eszközzel kell tartalmaznia. [Ismerje meg, hogyan állíthat be az alkalmazás automatikusan az Application Insights JavaScript SDK a lapmegtekintések gyűjtéséhez](app-insights-javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Válassza ki a kezdeti esemény indítása

![A felhasználó Forgalomáramlás kezdeti esemény kiválasztása](./media/app-insights-usage-flows/00002-flows-initial-event.png)

A felhasználó Forgalomáramlás eszközzel kérdések megválaszolásával megkezdéséhez válassza ki a kezdeti nézet, egyéni esemény, vagy a képi megjelenítéshez tartozó kiindulási pontként kiszolgálására kivétel:

1. Kattintson a hivatkozásra a **tegye felhasználók mit után...?**  title, vagy kattintson a **szerkesztése** gombra.
2. Jelöljön ki egy nézet, egyéni esemény vagy kivételnek felelnek meg a **kezdeti esemény** legördülő menüből.
3. Kattintson a **létrehozás graph**.

A képi megjelenítés "1. lépés" oszlopa jeleníti meg, mi felhasználók nyerte leggyakrabban csak a rendezett felülről lefelé a leginkább a legalább gyakori a kezdeti esemény után. A "2. lépés" és a későbbi oszlopok bemutatják, mi felhasználók az, ami azt követően, az összes a módon felhasználók lépjen a webhely keresztül kép létrehozása.

Alapértelmezés szerint a felhasználói Forgalomáramlás eszköz véletlenszerűen minták csak az elmúlt 24 órában Lapmegtekintések és egyéni esemény a webhelyről. Növelje az időtartományt, és módosítsa a teljesítmény és a pontosság elosztás véletlenszerű mintavételi a Szerkesztés menü.

Ha, a lapmegtekintések, egyéni események és kivételek nem releváns, kattintson a **X** el szeretné rejteni a csomóponton. A csomópontok elrejtése, kattintson a kívánt kiválasztása után a **létrehozás graph** a képi megjelenítés gombra. Minden csomópont elrejtett megtekintéséhez kattintson a **szerkesztése** gombra, majd tekintse meg a **események kizárt** szakasz.

Lapmegtekintések vagy egyéni események hiányoznak, ha a várt a képi megjelenítés meg:

* Ellenőrizze a **események kizárt** szakasz a **szerkesztése** menü.
* Gombjaival plusz **mások** csomópontok események kevésbé gyakran használják a képi megjelenítés felvenni.
* Ha a nézet vagy a várt egyéni esemény ritkán által küldött felhasználók, próbálja növelni a képi megjelenítés időtartománya a **szerkesztése** menü.
* Egyéni esemény vagy kivétel várt be van állítva az Application Insights SDK a webhely a forráskód lehet összegyűjteni, győződjön meg arról, hogy a lap megjelenítése. [További tudnivalók az egyéni események gyűjtése.](app-insights-api-custom-events-metrics.md)

Ha meg szeretné tekinteni a további lépéseket a képi megjelenítés, használja a **előző lépéseket** és **további lépések** legördülő listák megnyílásának a képi megjelenítés fent.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Egy lapon vagy a szolgáltatás megtekintése, után tegye felhasználók hová, és mi tegye kattintanak?

![Felhasználói Forgalomáramlás segítségével azonosíthatja, ahol a felhasználók kattintanak](./media/app-insights-usage-flows/00003-flows-one-step.png)

A kezdeti esemény nézet egy, az első oszlop ("1. lépés") képi megjelenítés akkor gyorsan megérteni, hogy mi felhasználók közvetlenül a meglátogatása után volt. Próbálja meg megnyitni a webhely egy ablakban, a felhasználó Forgalomáramlás képi megjelenítés mellett. Hasonlítsa össze a felhasználók hogyan használják a lapot az "1. lépés" oszlopban szereplő események elvárásainak. Úgy tűnik, a csapat jelentéktelen lap egy felhasználói felületi elem gyakran, a leggyakrabban használt lapon között lehet. Lehet, hogy a Tervező javításai és a hely számára kiváló kiindulási pontot.

A kezdeti esemény az egyéni esemény, ha az első oszlopban látható, mi felhasználókat, hogy a művelet végrehajtása után volt. Csakúgy, mint a lapmegtekintések, fontolja meg, ha a felhasználók a megfigyelt viselkedés megfelel-e a csapata célok és az elvárásokról. Ha a kijelölt kezdeti esemény "Hozzáadott elemet a vásárlás bevásárlókocsiból", például nézzük meg, ha a képi megjelenítés röviddel azután "Nyissa meg a kivétel" és "A vásárlás befejezése" jelenik meg. Ha a felhasználói viselkedés eltér az igényeinek, segítségével a képi megjelenítés megérteni, hogyan felhasználók kihozhatják "találkozott" a hely aktuális terv.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Hol találhatók a helyek, felhasználók kavarog leginkább a helyről?

Figyelendő **munkamenet véget ért** csomópontok nagy felfelé megjelenő a képi megjelenítés, különösen akkor korai szakaszában a folyamat egy oszlopban. Ez azt jelenti, hogy a lapok és a felhasználói felület kölcsönhatások a fenti elérési út végrehajtása után a hely valószínűleg churned sok felhasználó. Egyes esetekben várhatóan - végeztével a beszerzési egy kereskedelmi webhelyen például - forgalom, de általában forgalom bejelentkezési tervezési hibát, gyenge teljesítményt, vagy más növelhető a hellyel.

Ne felejtse el, amely **munkamenet véget ért** csomópontok csak az Application Insights-erőforrás által gyűjtött telemetriáját alapulnak. Az Application Insights telemetria az egyes felhasználói kommunikációt nem kap, ha felhasználók sikerült továbbra is kell kezelni a hely ezen a módon után a felhasználó Forgalomáramlás eszköz szerint a munkamenet véget ért.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Vannak-e helyen, ahol a felhasználók ismétlése ugyanaz a művelet újra és újra?

Keresse meg a nézet vagy egyéni esemény, sok felhasználó által a képi megjelenítés következő lépései közötti ismétlődő. Ez általában azt jelenti, hogy felhasználók ismétlődő műveletek működnek a webhelyen. Ha talál ismétlődő, gondolja át a webhely kialakításának módosítása vagy hozzáadása az új funkció ismétlődés csökkentése érdekében. Tömeges szerkesztés funkció például hozzáadása, ha talál ismétlődő műveletek végrehajtása minden egyes sorára egy elemet a felhasználók.

## <a name="common-questions"></a>Gyakori kérdések

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Nem a kezdeti esemény jelentik az esemény egy munkamenet jelenik meg először, vagy amikor az megjelenik egy munkamenet?

A képi megjelenítés a kezdeti esemény csak az első alkalommal a felhasználó adott lapmegtekintés vagy egyéni esemény egy munkamenet során küldött jelöli. Ha is el lehet küldeni a kezdeti esemény többször a munkamenetben, majd az "1. lépés" oszlopban csak látható felhasználók működése után a *első* kezdeti esemény példányát, nem minden esetben.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>A képi megjelenítés csomópontján túl magas szintű. Például, ha egy csomópont, amely szerint csak a "Gomb Clicked." Hogyan lehet I felosztása azt részletesebb csomópontok?

Használja a **szerint** beállításai a **szerkesztése** menüben:

1. Válassza ki az esemény bontani kívánja a **esemény** menü.
2. Válassza ki a dimenzió a **dimenzió** menü. Például ha "Gombra kattint" nevű esemény, próbálja meg egyéni tulajdonságot, "Gomb neve."

## <a name="next-steps"></a>További lépések

* [Használat – áttekintés](app-insights-usage-overview.md)
* [Felhasználók, a munkamenetek és az események](app-insights-usage-segmentation.md)
* [Megőrzés](app-insights-usage-retention.md)
* [Egyéni események hozzáadása az alkalmazáshoz](app-insights-api-custom-events-metrics.md)