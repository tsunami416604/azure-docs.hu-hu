---
title: A felhasználói folyamatok az Azure Application insights szolgáltatásban felhasználói navigációs mintáinak elemzése |} A Microsoft docs
description: Elemezheti, hogy a felhasználók hogyan navigálnak a lapjait és szolgáltatásait a webalkalmazás között.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 05853cb494ef6cc615bea7eecb1cfac288b46f3b
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721952"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>A felhasználói folyamatok az Application insights szolgáltatásban felhasználói navigációs mintáinak elemzése

![Application Insights felhasználói folyamatok eszköz](./media/app-insights-usage-flows/00001-flows.png)

A felhasználói folyamatok eszköz megjeleníti, hogy a felhasználók hogyan navigálnak a lapjait és szolgáltatásait a hely között. Itt a remek hasonló kérdések megválaszolásához:

* Tegye a felhasználók hogyan navigálnak a webhelyen lévő másik lapra?
* Milyen Igen felhasználók kattintson egy oldalon, a webhelyen?
* Hol találhatók azok a helyek, felhasználók churn leginkább a helyről?
* Vannak-e helyeken, ahol a felhasználók ismétlődő ugyanaz a művelet újra és újra?

A felhasználói folyamatok eszköz elindul egy kezdeti oldalmegtekintést, egyéni esemény vagy megadott kivétel. Adja meg a kezdeti esemény, előtt, és ezt követően a felhasználói munkamenet során történt eseményeket felhasználói folyamatok jeleníti meg. Hány alkalommal mindegyik elérési út lett követni a felhasználók különböző vastagságú vonalak megjelenítése Speciális **munkamenet kezdete** csomópontok megjelenítése, ahol a többi csomópont megkezdte a munkamenet. **Munkamenet állapotig** csomópontok megjelenítése, hogy hány felhasználó küldött nincs Lapmegtekintések vagy egyéni események után az előző csomópont kiemelése, ahol felhasználók valószínűleg left webhelyét.

> [!NOTE]
> Az Application Insights-erőforrást kell tartalmaznia, Lapmegtekintések vagy egyéni események, a felhasználói folyamatok eszköz használata. [Ismerje meg, hogyan állíthat be az alkalmazás gyűjtése az Application Insights JavaScript SDK automatikusan a lapmegtekintések](app-insights-javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Kezdésképpen a kezdeti esemény

![Kezdeti esemény, választhatja a felhasználói folyamatok](./media/app-insights-usage-flows/00002-flows-initial-event.png)

Ha szeretné elkezdeni a felhasználói folyamatok eszköz kérdések megválaszolását, válassza ki egy kezdeti oldalmegtekintést, egyéni esemény vagy kivétel a Vizualizáció a kiindulási pontként szolgál:

1. Kattintson a hivatkozásra a **nincs felhasználói tevékenység után...?**  cím, vagy kattintson a **szerkesztése** gombra.
2. Válasszon egy oldalmegtekintést, egyéni eseményt vagy kivételt a **kezdeti esemény** legördülő listából.
3. Kattintson a **létrehozás graph**.

Az "1. lépés" oszlopot a Vizualizáció látható, milyen felhasználók tudta leggyakrabban csak a rendezett felülről lefelé a legtöbb legritkább, a kezdeti esemény után. A "2. lépés" és a következő oszlopok megjelenítése, mi felhasználók tette azt követően, az összes módon felhasználók van nyitva a webhelyen keresztül kép létrehozásához.

Alapértelmezés szerint a felhasználói folyamatok eszköz véletlenszerűen minták csak az elmúlt 24 órában lapmegtekintés és egyéni esemény az webhelyről. Növelje az időtartományt, és módosítsa az egyenleg a teljesítmény és a pontosság véletlenszerű mintavételi a Szerkesztés menü.

Ha az Ön számára nem bizonyos az oldalmegtekintéseket, az egyéni eseményeket és a kivételeket, kattintson a **X** el szeretné rejteni a csomópontokon. Miután kiválasztotta a kívánt csomópontokat is elrejtése, kattintson a **létrehozás graph** gomb alatti a vizualizációt. Összes elrejtett csomópont megtekintéséhez kattintson a **szerkesztése** gombra, majd tekintse meg a **kizárt események** szakaszban.

Ha a lapmegtekintések vagy egyéni események, amelyek esetében hiányzik a várt a Vizualizáció:

* Ellenőrizze a **kizárt események** című rész a **szerkesztése** menü.
* Plusz gombok **mások** többek között a kevésbé gyakran használják események a Vizualizáció-csomópontokat.
* Ha az oldal nézet vagy egyéni esemény várható ritkán küld a felhasználók, próbálkozzon az időtartomány, a Vizualizációk növelésével a **szerkesztése** menü.
* Ellenőrizze, hogy a lap megtekintéséhez, egyéni eseményt vagy kivétel várt beállításáról gyűjti össze az Application Insights SDK a webhely a forráskódban. [További információ az egyéni események gyűjtése.](app-insights-api-custom-events-metrics.md)

Ha meg szeretné tekinteni a további lépéseket a vizualizációban, használja a **előző lépések** és **további lépések** legördülő menük, a Vizualizáció fölött.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Weblap vagy szolgáltatás megtekintése, után tegye felhasználók hová, és mi tegye kattintanak?

![Ismerje meg, ahol a felhasználók kattintanak a felhasználói folyamatok használatával](./media/app-insights-usage-flows/00003-flows-one-step.png)

Ha a kezdeti esemény egy lapmegtekintés, az első oszlop ("1" lépés), a Vizualizáció gyorsan megismerheti, mit felhasználók közvetlenül az oldalra látogat után volt. Próbálja meg megnyitni a hely a felhasználói folyamatok képi megjelenítés mellett ablakban. Hasonlítsa össze az elvárásainak, a felhasználók hogyan használják az oldal az események az "1. lépés" oszlopban. Gyakran előfordul azon az oldalon, úgy tűnik, nem jelentős a csapata számára olyan felhasználói felületi elemek között a leggyakrabban használt a lapon lehet. Arculati fejlesztések a helyhez a remek kiindulási pont lehet.

A kezdeti esemény az egyéni esemény, ha az első oszlop mutatja, milyen felhasználók csak az adott művelet végrehajtása után volt. Lapmegtekintések, mint a fontolja meg, ha a felhasználók viselkedését a megfigyelt megfelel-e a csapat céljait és elvárásainak. Ha a kijelölt kezdeti esemény "Hozzáadott elem a vásárlás bevásárlókocsihoz", például nézzük meg, ha a Vizualizáció Röviddel ezután "Ugrás a Checkout" és "Beszerzési befejeződött" jelenik meg. Ha a felhasználói viselkedés eltér az elvárásainak, használja a Vizualizáció megérteni, hogyan felhasználók kihozhatják a hely jelenlegi kialakítás "találkozott".

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Hol találhatók azok a helyek, felhasználók churn leginkább a helyről?

Tekintse meg a **munkamenet véget ért** csomópontok nagy mentési jelennek meg, a Vizualizáció egy folyamatban korai különösen oszlopban. Ez azt jelenti, hogy hány felhasználó után a következő lapokat és a felhasználói felület kölcsönhatások a fenti elérési út a webhelyek valószínűleg előforduló. Néha adatváltozás - elvégezte a beszerzés, egy e-kereskedelmi webhelyen például - várt, de általában a forgalom a tervezési problémákat, gyenge teljesítményt vagy egyéb problémák a hellyel, amely javítani lehet a jele.

Vegye figyelembe, hogy **munkamenet véget ért** csomópontok csak az Application Insights-erőforrás által gyűjtött telemetria alapulnak. Az Application Insights telemetria bizonyos felhasználói interakció érdekében nem kap, ha felhasználók sikerült továbbra is rendelkezik kezelhetők a hely ezen a módon után a felhasználói folyamatok eszköz szerint a munkamenet véget ért.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Vannak-e helyeken, ahol a felhasználók ismétlődő ugyanaz a művelet újra és újra?

Keressen egy oldal nézet vagy egyéni esemény, amelyek sok felhasználó között a vizualizációt a következő lépéseinek ismétlődik. Ez általában azt jelenti, hogy felhasználók ismétlődő műveleteket végez a webhelyen. Ha azt tapasztalja, ismétlődés, gondolja át a webhely kialakításának módosítása vagy hozzáadása ismétlési csökkentése érdekében új funkciókat. Tömeges szerkesztés funkció például hozzáadása, ha a felhasználók ismétlődő műveleteket végez egy elemet minden egyes sorára.

## <a name="common-questions"></a>Gyakori kérdések

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>A kezdeti esemény jelölik az esemény egy munkamenet jelenik meg először nem, vagy minden alkalommal megjelenik egy munkamenetben?

A Vizualizáció a kezdeti esemény csak az első alkalommal egy felhasználó adott oldal nézet vagy egyéni esemény egy munkamenet során küldött jelöli. Ha a felhasználók elküldhetik a kezdeti esemény többször is feldolgozza a munkamenet, majd az "1. lépés" oszlop csak bemutatja, hogyan viselkednek a felhasználók után a *első* kezdeti esemény példányát, nem minden példány.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>A csomópontok a Vizualizáció némelyike túl magas szintű. Ha például arról, hogy a csak "Gomb gombos. kattintással elérhető" csomópont Hogyan tudok oszthatja azt részletesebb csomópontok be?

Használja a **felosztás** lehetőségei a a **szerkesztése** menüben:

1. Válassza ki a kívánt eseményt felosztania az **esemény** menü.
2. Válasszon egy adott dimenzió a **dimenzió** menü. Ha egy esemény "Gombra kattint" nevű, próbáljon meg például a "Gomb neve." nevű egyéni tulajdonságot

## <a name="next-steps"></a>További lépések

* [Használat – áttekintés](app-insights-usage-overview.md)
* [Felhasználók, munkamenetek és események](app-insights-usage-segmentation.md)
* [Megőrzés](app-insights-usage-retention.md)
* [Egyéni események hozzáadása az alkalmazáshoz](app-insights-api-custom-events-metrics.md)