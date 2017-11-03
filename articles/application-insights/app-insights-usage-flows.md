---
title: "Felhasználói navigációs minták a felhasználó Azure Application insightsban zajló kommunikációról elemzése |} Microsoft docs"
description: "Elemezze a felhasználók hogyan keresse meg a lapokat és a szolgáltatások webalkalmazás között."
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 08/02/2017
ms.author: cfreeman
ms.openlocfilehash: d17ed3dff08f00a1d6a2108608e42b29f95fbd84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>A felhasználó zajlik az Application Insightsban felhasználói navigációs minták elemzése

![Application Insights felhasználói Forgalomáramlás eszköz](./media/app-insights-usage-flows/flows.png)

A felhasználó Forgalomáramlás eszköz visualizes felhasználók hogyan keresse meg a lapokat és a webhely szolgáltatások között. Fontos nagy hasonló kérdések megválaszolásához:
* Hogyan tegye felhasználók elhagyni a lapot a webhely?
* Mi tegye felhasználók kattintson egy oldalon, a webhely?
* Hol találhatók a helyek, felhasználók kavarog leginkább a helyről?
* Vannak-e helyen, ahol a felhasználók ismétlése ugyanaz a művelet újra és újra?

A felhasználó zajlik az eszköz elindítja a kezdeti vagy egy megadott esemény. Ez a nézet vagy egyéni esemény megadott, felhasználói Forgalomáramlás látható, Lapmegtekintések és egyéni események, amelyet a felhasználóknak küldött azonnal ezt követően egy munkamenetben két lépések azt követően, és így tovább. Különböző vastagsága sornyi hányszor mindegyik elérési út volt követ felhasználók megjelenítése. Különleges "Munkamenet véget ért" csomópontok megjelenítése hány felhasználó küldött nincs Lapmegtekintések vagy egyéni események az előző csomópont után kiemelve, ahol felhasználók valószínűleg korábban a helyen.



> [!NOTE]
> Az Application Insights-erőforrás Lapmegtekintések vagy egyéni események a felhasználó Forgalomáramlás eszközzel kell tartalmaznia. [Ismerje meg, hogyan állíthat be az alkalmazás automatikusan az Application Insights JavaScript SDK a lapmegtekintések gyűjtéséhez](app-insights-javascript.md).
> 
> 

## <a name="start-by-choosing-an-initial-page-view-or-custom-event"></a>Válasszon egy kezdeti nézet vagy egyéni esemény indítása

![A felhasználó Forgalomáramlás kezdeti esemény kiválasztása](./media/app-insights-usage-flows/flows-initial-event.png)

A kezdéshez a felhasználó Forgalomáramlás eszközzel kérdések megválaszolásával kezdeti nézet vagy a képi megjelenítéshez tartozó kiindulási pontként kiszolgálására egyéni esemény választ:
1. Kattintson a hivatkozásra a "nincs mit felhasználók után...?" title, vagy kattintson a Szerkesztés gombra. 
2. A "Kezdeti event" legördülő listából válassza ki a lap nézet vagy egyéni esemény.
3. Kattintson a "Create graph".

A képi megjelenítés "1. lépés" oszlopa jeleníti meg, mi felhasználók nyerte leggyakrabban csak a rendezett felső az a legkisebb – részletek a kezdeti esemény után. A "2. lépés" és a későbbi oszlopok bemutatják, mi felhasználók az, ami azt követően, az összes a módon felhasználók lépjen a webhely keresztül kép létrehozása.

Alapértelmezés szerint a felhasználói Forgalomáramlás eszköz véletlenszerűen minták csak az elmúlt 24 órában Lapmegtekintések és egyéni esemény a webhelyről. Növelje az időtartományt, és módosítsa a teljesítmény és a pontosság elosztás véletlenszerű mintavételi a Szerkesztés menü.

Ha egyes Lapmegtekintések és egyéni események nem releváns, kattintson az "X" a csomópontokon, elrejteni kívánt. Után az elrejteni kívánt csomópontokat, a "Create graph" gombra a képi megjelenítés alatt. Tekintse meg az összes elrejtett csomópontot, kattintson a Szerkesztés gombra, majd tekintse meg a "Kizárt események" szakasz.

Lapmegtekintések vagy egyéni események hiányoznak, ha a várt a képi megjelenítés meg:
* Ellenőrizze a "Kizárt események" szakaszban kattintson a Szerkesztés menü.
* Használja a "Részletességi szintje" vezérlő a Szerkesztés menü események kevésbé gyakran használják fel a képi megjelenítés.
* Ha a nézet vagy a várt egyéni esemény ritkán által küldött felhasználók, próbálja növelni a képi megjelenítés a Szerkesztés menü időtartománya.
* Győződjön meg arról, hogy a lap megtekintéséhez, vagy egyéni várt esemény be van állítva az Application Insights SDK a webhely a forráskód lehet összegyűjteni. [További tudnivalók az egyéni események gyűjtése.](app-insights-api-custom-events-metrics.md)

Ha meg szeretné tekinteni a további lépéseket a képi megjelenítés, használja a "Több lépésből áll" csúszkát a Szerkesztés menü.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Egy lapon vagy a szolgáltatás megtekintése, után tegye felhasználók hová, és mi tegye kattintanak?

![Felhasználói Forgalomáramlás segítségével azonosíthatja, ahol a felhasználók kattintanak](./media/app-insights-usage-flows/flows-one-step.png)

A kezdeti esemény nézet egy, az első oszlop ("1. lépés") képi megjelenítés akkor gyorsan megérteni, hogy mi felhasználók közvetlenül a meglátogatása után volt. Próbálja meg megnyitni a webhely egy ablakban, a felhasználó Forgalomáramlás képi megjelenítés mellett. Hasonlítsa össze a felhasználók hogyan használják a lapot az "1. lépés" oszlopban szereplő események elvárásainak. Úgy tűnik, a csapat jelentéktelen lap egy felhasználói felületi elem gyakran, a leggyakrabban használt lapon között lehet. Lehet, hogy a Tervező javításai és a hely számára kiváló kiindulási pontot.

A kezdeti esemény az egyéni esemény, ha az első oszlopban látható, mi felhasználókat, hogy a művelet végrehajtása után volt. Csakúgy, mint a lapmegtekintések, fontolja meg, ha a felhasználók a megfigyelt viselkedés megfelel-e a csapata célok és az elvárásokról. Ha a kijelölt kezdeti esemény "Hozzáadott elemet a vásárlás bevásárlókocsiból", például nézzük meg, ha a képi megjelenítés röviddel azután "Nyissa meg a kivétel" és "A vásárlás befejezése" jelenik meg. Ha a felhasználói viselkedés nem sokkal elvárásainak, használja a képi megjelenítés megértése, hogyan felhasználók kihozhatják "találkozott" a hely aktuális terv.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Hol találhatók a helyek, felhasználók kavarog leginkább a helyről?

Figyelés "Munkamenet véget ért" csomópontok nagy felfelé megjelenő a képi megjelenítés, különösen akkor korai szakaszában a folyamat egy oszlopban. Ez azt jelenti, hogy a lapok és a felhasználói felület kölcsönhatások a fenti elérési út végrehajtása után a hely valószínűleg churned sok felhasználó. Egyes esetekben várhatóan - végeztével a beszerzési egy kereskedelmi webhelyen például - forgalom, de általában forgalom bejelentkezési tervezési hibát, gyenge teljesítményt, vagy más növelhető a hellyel.

Ne feledje, hogy "munkamenet véget ért" csomópont csak az Application Insights-erőforrás által gyűjtött telemetriáját alapulnak. Az Application Insights telemetria az egyes felhasználói kommunikációt nem kap, ha felhasználók sikerült továbbra is kell kezelni a hely ezen a módon után a felhasználó Forgalomáramlás eszköz szerint a munkamenet véget ért.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Vannak-e helyen, ahol a felhasználók ismétlése ugyanaz a művelet újra és újra?

Keresse meg a nézet vagy egyéni esemény, sok felhasználó által a képi megjelenítés következő lépései közötti ismétlődő. Ez általában azt jelenti, hogy felhasználók ismétlődő műveletek működnek a webhelyen. Ha talál ismétlődő, gondolja át a webhely kialakításának módosítása vagy hozzáadása az új funkció ismétlődés csökkentése érdekében. Tömeges szerkesztés funkció például hozzáadása, ha talál ismétlődő műveletek végrehajtása minden egyes sorára egy elemet a felhasználók.

## <a name="common-questions"></a>Gyakori kérdések

### <a name="why-do-steps-appear-disconnected"></a>Miért lépéseket jelenik meg a leválasztott?

![Leválasztott lépéssel felhasználói adatfolyamok](./media/app-insights-usage-flows/flows-disconnected.png)

Ha lépéseket (oszlop) a felhasználó Forgalomáramlás képi megjelenítés nem kapcsolódik, az azt jelenti, a lépések között felhasználók által végrehajtott elérési útvonalak egyike volt elég gyakran megjelenítendő. Futtatásával jelenítheti meg a képi megjelenítés ritkábban eseményeket, ezért a lépések jelennek meg a csatlakoztatott, a csúszka "Részletességi szintje" a Szerkesztés menü.

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Nem a kezdeti esemény jelentik az esemény egy munkamenet jelenik meg először, vagy amikor az megjelenik egy munkamenet?

A képi megjelenítés a kezdeti esemény csak az első alkalommal a felhasználó adott lapmegtekintés vagy egyéni esemény egy munkamenet során küldött jelöli. Ha is el lehet küldeni a kezdeti esemény többször a munkamenetben, majd az "1. lépés" oszlopban csak látható felhasználók működése után a *első* kezdeti esemény példányát, nem minden esetben.

## <a name="next-steps"></a>Következő lépések

* [Használat – áttekintés](app-insights-usage-overview.md)
* [Felhasználók, a munkamenetek és az események](app-insights-usage-segmentation.md)
* [Megőrzés](app-insights-usage-retention.md)
* [Egyéni események hozzáadása az alkalmazáshoz](app-insights-api-custom-events-metrics.md)