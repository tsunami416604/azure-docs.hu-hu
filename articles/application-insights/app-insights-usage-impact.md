---
title: Az Azure Application Insights használati hatás |} A Microsoft docs
description: Különböző tulajdonságok potenciálisan elemezheti az alkalmazások részeit konvertálási mutatói hatással.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/25/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 9b987ccd17f2060eea125a5d384446cec24fb68a
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969165"
---
# <a name="impact-analysis-with-application-insights"></a>A hatáselemzés az Application insights segítségével

Hatás elemzi, hogyan befolyásolja az gyorsabb betöltési időt és egyéb tulajdonságok konvertálási mutatói az alkalmazás különböző részeit. Pontosabban helyezni, hogy felderíti hogyan **minden dimenzió** , egy **oldalmegtekintés**, **egyéni esemény**, vagy **kérelem** hatással van a használatát egy különböző **oldalmegtekintés** vagy **egyéni esemény**. 

![Hatás eszköz](./media/app-insights-usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Még mindig nem tudja hatás funkciója?

Úgy gondolja, hogy gyakorolt hatás egyik módja, juttatásaival argumentumok egy olyan személlyel, hogyan hatással lenne a webhely egyes aspektusait a lassúsága e felhasználók maradásra csapatát a végső eszközként. Amíg felhasználók lekérés előfordulhat, hogy egy bizonyos mennyiségű lassúsága, hatás biztosítja betekintést optimalizálása és a teljesítmény maximalizálása érdekében a felhasználók átalakítását elosztása érdekében a legjobb módja.

De teljesítményelemzésben hatása a funkciók egy részét. Hiszen hatás támogatja az egyéni eseményeket és dimenziókat, a kérdések megválaszolásával, hogyan felhasználói böngésző választott felel a különböző díjszabás átalakítás, mindössze néhány kattintást igényel.

![Képernyőkép konverziós arányok böngészők](./media/app-insights-usage-impact/0004-browsers.png)

> [!NOTE]
> Az Application Insights-erőforrást kell tartalmaznia, Lapmegtekintések vagy egyéni események, a hatás eszközt használva. [Ismerje meg, hogyan állíthat be az alkalmazás gyűjtése az Application Insights JavaScript SDK automatikusan a lapmegtekintések](../azure-monitor/app/javascript.md). Továbbá ne feledje, hogy meg vannak elemzése korrelációs, mivel a minta mérete számít.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Lapmegtekintés betöltési ideje negatív hatással van hányan konvertálása saját oldalon?

Ha szeretné elkezdeni a hatás eszközzel kérdések megválaszolását, válassza ki egy kezdeti oldal nézet, egyéni eseményt vagy kérelmet.

![Hatás eszköz](./media/app-insights-usage-impact/0002-dropdown.png)

1. Az oldal nézet kiválasztása a **a lapmegtekintés** legördülő listából.
2. Hagyja a **elemzése hogyan annak** az alapértelmezésként beállított elemet a legördülő listából **időtartama** (ebben a környezetben **időtartama** aliasneve **Lapbetöltési idő**.)
3. Az a **hatással van a használatát** legördülő menüben válassza ki a kívánt egyéni esemény. Ez az esemény meg kell egyeznie a 1. lépésben kiválasztott oldal nézet egy felhasználói felületi elemben.

![Az eredmények képernyőképe](./media/app-insights-usage-impact/0003-results.png)

Ebben a példányban, **termékoldalán** betöltési ideje növeli az átváltási árfolyam **kattintott beszerzési termék** leáll. A fenti terjesztési alapján, 3.5-ös másodperc időtartama egy optimális lap betöltése sikerült megcélozni érhet el egy esetleges 55 % konverziós arány. További teljesítménnyel kapcsolatos fejlesztések, 3.5-ös másodpercek alatt betöltési idő csökkentése érdekében jelenleg nem összefügg átalakítás további előnyökkel.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Mi történik, ha lapmegtekintés vagyok nyomon követése, vagy egyéni módon alkalommal betöltése?

Hatás támogatja a standard és egyéni tulajdonságokat és a mértékek. Használjon bármilyen szeretné. Időtartam, helyett az elsődleges és másodlagos események szűrők segítségével pontosabb beolvasása.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Más országokból vagy régiókból származó felhasználók különböző ütemben konvertálásának?

1. Az oldal nézet kiválasztása a **a lapmegtekintés** legördülő listából.
2. Válassza ki a "Ország vagy régió" a **elemzése hogyan annak** legördülő menü
3. Az a **hatással van a használatát** legördülő menüben válassza az 1. lépésben kiválasztott egy felhasználói felületi elemre az oldal nézet a megfelelő, egyéni eseményt.

Ebben az esetben az eredmények már nem fér folyamatos x tengellyel modellbe, akárcsak az első példában. Ehelyett egy szegmentált tölcsérhez hasonlít egy Vizualizáció jelenik meg. Rendezés **használati** megtekintéséhez az egyéni esemény ország alapján átalakítása változata.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Hogyan nem a hatás eszköz számítja ki az átalakítás díjszabása?

Technikai részletek, a hatás eszköz támaszkodik a [Pearson-korrelációs együttható](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Eredmények 1 és jelölő nulla összefüggések keresésére és a egy pozitív korrelációs jelölő 1 -1 és 1 közötti számítja ki.

A hatáselemzés működésének alapszintű áttekintését a következőképpen történik:

Lehetővé teszik _A_ = a fő lapján megtekintése/egyéni esemény/kérelmezési, az első legördülő listában válassza ki. (**a lapmegtekintés**).

Lehetővé teszik _B_ választja, a másodlagos oldal nézet/egyéni esemény = (**hatással van a használatát**).

Hatás megvizsgálja a kijelölt időtartományban található felhasználók egy minta az összes előadáshoz. Minden olyan munkamenethez, minden egyes tűnik _A_.

Munkamenetek majd osztani két különböző, _subsessions_ két feltétel valamelyike alapján:

- A konvertált subsession végződő munkamenet áll egy _B_ esemény és magában foglalja az összes _A_ eseményeket, amelyek előtt _B_.
- Egy konvertálatlan subsession akkor fordul elő, ha az összes _A_parancsot egy terminálban nélkül történik a _B_.

Hogyan végső soron ki a hatását attól függően változik, hogy elemzünk metrika vagy dimenzió-e. Az összes metrikákhoz _A_a egy subsession a átlagolja. Mivel a dimenziók minden értékét _A_ járul hozzá _1/N_ hozzárendelt érték, _B_ ahol _N_ száma_A_céljai a subsession a.

## <a name="next-steps"></a>További lépések

- Használati bővítsék küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [Lapmegtekintések](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha már küldhet egyéni eseményeket vagy lapmegtekintéseket, Fedezze fel az megtudhatja, hogy a felhasználók használni a szolgáltatást a Használatelemzési eszközben.
    - [Tölcsérek](usage-funnels.md)
    - [Megőrzés](app-insights-usage-retention.md)
    - [Felhasználói folyamatok](app-insights-usage-flows.md)
    - [Munkafüzetek](app-insights-usage-workbooks.md)
    - [Adja hozzá a felhasználói környezet](app-insights-usage-send-user-context.md)