---
title: Az Azure Application Insights felhasználói folyamatai elemzik a navigációs folyamatokat
description: Elemezze, hogy a felhasználók hogyan navigálnak a webalkalmazás lapjai és szolgáltatásai között.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8622ede9e6f7fba2fde2e0b2e90eb31520a23d04
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892444"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Felhasználói navigációs minták elemzése felhasználói folyamatok segítségével az Application Insightsban

![Az Application Insights felhasználói folyamatok eszköze](./media/usage-flows/00001-flows.png)

A Felhasználói folyamatok eszköz megjeleníti, hogy a felhasználók hogyan navigálnak a webhely lapjai és szolgáltatásai között. Ez nagyszerű a kérdések megválaszolására, mint például:

* Hogyan navigálhatnak el a felhasználók a webhely egy lapjáról?
* Mire kattintanak a felhasználók a webhely egy oldalára?
* Hol vannak azok a helyek, ahol a felhasználók a legtöbbet kavarognak a webhelyéről?
* Vannak olyan helyek, ahol a felhasználók ugyanazt a műveletet ismételgetik újra és újra?

A Felhasználói folyamatok eszköz a megadott kezdeti oldalnézetből, egyéni eseményből vagy kivételből indul ki. A kezdeti esemény miatt a Felhasználói folyamatok a felhasználói munkamenetek előtt és után történt eseményeket jeleníti meg. A különböző vastagságú vonalak azt mutatják, hogy az egyes görbék hány alkalommal követték a felhasználók. A Speciális **munkamenet elindított** csomópontjai azt mutatják, hogy a következő csomópontok hol kezdték a munkamenetet. **A Munkamenet-végződésű** csomópontok azt mutatják, hogy az előző csomópont után hány felhasználó nem küldött oldalmegtekintést vagy egyéni eseményt, kiemelve, hogy a felhasználók valószínűleg hol hagyták el a webhelyet.

> [!NOTE]
> Az Application Insights-erőforrásnak oldalmegtekintéseket vagy egyéni eseményeket kell tartalmaznia a Felhasználói folyamatok eszköz használatához. [Megtudhatja, hogy miként állíthatja be az alkalmazást úgy, hogy automatikusan összegyűjtse az oldalmegtekintéseket az Application Insights JavaScript SDK segítségével.](../../azure-monitor/app/javascript.md)
>
>

## <a name="start-by-choosing-an-initial-event"></a>Először válasszon ki egy kezdeti eseményt

![Kezdeti esemény kiválasztása a felhasználói folyamatokhoz](./media/usage-flows/00002-flows-initial-event.png)

A kérdések megválaszolásának megkezdéséhez a Felhasználói folyamatok eszközzel válasszon egy kezdeti oldalnézetet, egyéni eseményt vagy kivételt, amely a vizualizáció kiindulópontjaként szolgál:

1. Kattintson a Hivatkozásra a Mit tegyenek a **felhasználók a...?** cím után, vagy kattintson a **Szerkesztés** gombra.
2. Válasszon oldalnézetet, egyéni eseményt vagy kivételt a **Kezdeti esemény** legördülő menüből.
3. Kattintson **a Diagram létrehozása gombra.**

A vizualizáció "1. A "2.

Alapértelmezés szerint a Felhasználói folyamatok eszköz véletlenszerűen csak az utolsó 24 óra oldalmegtekintést és egyéni eseményt mintáteszi a webhelyről. Növelheti az időtartományt, és módosíthatja a teljesítmény és a pontosság egyensúlyát a véletlenszerű mintavételezéshez a Szerkesztés menüben.

Ha az oldalmegtekintések, egyéni események és kivételek egy része nem releváns az Ön számára, kattintson az **Elrejteni** kívánt csomópontok x-ére. Miután kiválasztotta az elrejteni kívánt csomópontokat, kattintson a **képi megjelenítés** alatti Diagram létrehozása gombra. Az összes elrejtett csomópont megtekintéséhez kattintson a **Szerkesztés** gombra, majd tekintse meg a **Kizárt események szakaszt.**

Ha hiányoznak azok az oldalmegtekintések vagy egyéni események, amelyeket a vizualizációban látni szeretne:

* Ellenőrizze a Szerkesztés **menü** **Kizárt események** szakaszát.
* A **Mások** csomópontok plusz gombjaival kevésbé gyakori eseményeket vehet fel a vizualizációba.
* Ha a várt oldalnézetet vagy egyéni eseményt a felhasználók ritkán küldik el, próbálja meg növelni a megjelenítés **időtartományát** a Szerkesztés menüben.
* Győződjön meg arról, hogy a várhatóan várt oldalnézet, egyéni esemény vagy kivétel úgy van beállítva, hogy az Application Insights SDK a webhely forráskódjában gyűjtse össze. [További információ az egyéni események gyűjtéséről.](../../azure-monitor/app/api-custom-events-metrics.md)

Ha további lépéseket szeretne látni a vizualizációban, használja az **Előző és** a **Következő lépések** legördülő menüt a vizualizáció felett.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Egy oldal vagy funkció fellátogatása után hová mennek a felhasználók, és mire kattintanak?

![A Felhasználói folyamatok használata annak megértéséhez, hogy a felhasználók hol kattintanak](./media/usage-flows/00003-flows-one-step.png)

Ha a kezdeti esemény oldalnézet, a vizualizáció első oszlopa ("1. lépés") gyorsan megértheti, hogy a felhasználók mit tettek közvetlenül az oldal fellátogatása után. Próbálja meg megnyitni a webhelyet a Felhasználói folyamatok vizualizáció melletti ablakban. Hasonlítsa össze az "1. Gyakran előfordul, hogy az oldalon található, a csapat számára jelentéktelennek tűnő felhasználói felületi elem az oldal leggyakrabban használt elemei közé tartozhat. Ez lehet egy nagyszerű kiindulási pont a tervezési fejlesztések a webhelyen.

Ha a kezdeti esemény egyéni esemény, az első oszlopban látható, hogy a felhasználók mit tettek közvetlenül a művelet végrehajtása után. Az oldalmegtekintésekhez ugyanúgy érdemes megfontolni, hogy a felhasználók megfigyelt viselkedése megfelel-e a csapat céljainak és elvárásainak. Ha a kiválasztott kezdeti esemény például a "Hozzáadott elem a bevásárlókosárba" lesz, ne ellenőrizze, hogy röviddel ezután megjelenik-e a "Go to Checkout" és a "Befejezett vásárlás" a vizualizációban. Ha a felhasználói viselkedés eltér az elvárásaitól, a vizualizáció segítségével megtudhatja, hogy a felhasználók hogyan "szorulnak csapdába" a webhely jelenlegi kialakítása miatt.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Hol vannak azok a helyek, ahol a felhasználók a legtöbbet kavarognak a webhelyéről?

Figyelje a **munkamenet-végződt** csomópontokat, amelyek magasan jelennek meg a vizualizáció egyik oszlopában, különösen a folyamat korai szakaszában. Ez azt jelenti, hogy sok felhasználó valószínűleg churned a webhely után az előző elérési utat az oldalak és a felhasználói felület kölcsönhatások. Néha lemorzsolódás várható - miután befejezte a vásárlást egy e-kereskedelmi webhelyen, például -, de általában a lemorzsolódás a tervezési problémák, a gyenge teljesítmény vagy más, javítható problémák jele.

Ne feledje, hogy **a munkamenet-végződt** csomópontok csak az Application Insights-erőforrás által gyűjtött telemetriai adatokon alapulnak. Ha az Application Insights nem kap telemetriai adatokat bizonyos felhasználói interakciók, a felhasználók továbbra is interakcióba léphettek a webhely ilyen módon, miután a Felhasználói folyamatok eszköz azt mondja, a munkamenet véget ért.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Vannak olyan helyek, ahol a felhasználók ugyanazt a műveletet ismételgetik újra és újra?

Keressen olyan oldalmegtekintést vagy egyéni eseményt, amelyet sok felhasználó megismétel a vizualizáció későbbi lépései során. Ez általában azt jelenti, hogy a felhasználók ismétlődő műveleteket hajtanak végre a webhelyen. Ha ismétlést talál, gondolkodjon el a webhely tervének megváltoztatásán vagy új funkciók hozzáadásán az ismétlések csökkentése érdekében. Ha például tömeges szerkesztési funkciókat ad hozzá, ha a felhasználók ismétlődő műveleteket hajtanak végre a táblázatelemek minden során.

## <a name="common-questions"></a>Gyakori kérdések

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>A kezdeti esemény az első alkalommal jelenik meg egy munkamenetben, vagy bármikor, amikor megjelenik egy munkamenetben?

A vizualizáció első eseménye csak azt jelzi, amikor egy felhasználó először küldte el az oldalmegtekintést vagy egyéni eseményt egy munkamenet során. Ha a felhasználók egy munkamenetben többször is elküldhetik a kezdeti eseményt, akkor *first* az "1.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>A vizualizációban lévő csomópontok némelyike túl magas szintű. Például egy csomópont, amely csak azt mondja: "Button Clicked." Hogyan bonthatom le részletesebb csomópontokra?

Használja a Szerkesztés **menü** Felosztás a beállítások **szerint** parancsát:

1. Válassza ki a lebontani kívánt eseményt az **Esemény** menüben.
2. Válasszon egy dimenziót a **Dimenzió** menüben. Ha például van egy "Gombkattintással" nevű esemény, próbálkozzon a "Gomb neve" nevű egyéni tulajdonsággal.

## <a name="next-steps"></a>További lépések

* [Használat – áttekintés](usage-overview.md)
* [Felhasználók, munkamenetek és események](usage-segmentation.md)
* [Megőrzés](usage-retention.md)
* [Egyéni események hozzáadása az alkalmazáshoz](../../azure-monitor/app/api-custom-events-metrics.md)
