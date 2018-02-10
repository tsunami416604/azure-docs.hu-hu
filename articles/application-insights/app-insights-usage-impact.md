---
title: "Az Azure Application Insights használati hatás |} Microsoft docs"
description: "Hogyan más tulajdonságokkal potenciálisan elemzése hatással lehet az alkalmazások részei átalakítás sebességet."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/25/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: d76db02647ce878343f60fc84cf063c5b7833438
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="impact-analysis-with-application-insights"></a>A hatáselemzés az Application insights szolgáltatással

Hatás elemzi, hogyan betöltési idők és egyéb tulajdonságok hassanak átváltása a az alkalmazás különböző részeit. Pontosabban helyezése, felderíti hogyan **bármely olyan dimenzió** , egy **nézet lapon**, **egyéni esemény**, vagy **kérelem** hatással van az használatát, a különböző **nézet lapon** vagy **egyéni esemény**. 

![Hatás eszköz](./media/app-insights-usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Még nem biztos hatás funkciója?

Gondolja, hogy hatással lehet az egyik módja van, mint a végső eszköz kiegyenlítéséhez argumentumok személlyel kapcsolatban, hogyan érinti lassúsága néhány szempontja, hogy a hely a felhasználókat a maradásra e csoportja. Felhasználók előfordulhat, hogy bizonyos mennyiségű lassúsága működését, amíg hatás lehetővé teszi az optimalizálás, és a teljesítmény maximalizálásához felhasználói átalakítás elosztása érdekében a legjobb módja betekintést.

De teljesítményének elemzése hatása a képességek egy részét. Mivel hatás támogatja az egyéni események és dimenziókat, például hogyan felhasználói böngésző kiválasztása felel az átalakítás különböző sebességű megválaszolásával számítógépnél néhány kattintással.

![Képernyőkép átalakítás böngésző](./media/app-insights-usage-impact/0004-browsers.png)

> [!NOTE]
> Az Application Insights-erőforrás Lapmegtekintések vagy egyéni események a hatás eszközzel kell tartalmaznia. [Ismerje meg, hogyan állíthat be az alkalmazás automatikusan az Application Insights JavaScript SDK a lapmegtekintések gyűjtéséhez](app-insights-javascript.md). Azt is vegye figyelembe, hogy azóta meg vannak elemzése korrelációs, minta mérete kérdések.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Van lapbetöltési idő érintő, hányan alakítsa át a lapon?

A hatás eszközzel kérdések megválaszolásával megkezdéséhez válasszon egy kezdeti nézetben, az egyéni esemény vagy a kérelem.

![Hatás eszköz](./media/app-insights-usage-impact/0002-dropdown.png)

1. Válassza ki a lap nézetet a **a nézet** legördülő menüből.
2. Hagyja a **elemzése hogyan annak** az alapértelmezett lehetőséget a legördülő lista **időtartam** (ebben a környezetben **időtartam** aliasneve **Lapbetöltési idő**.)
3. Az a **hatással van az használatát** legördülő menüben válassza ki az egyéni esemény. Ez az esemény meg kell egyeznie a felhasználói felületi elem az 1. lépésben kiválasztott nézet.

![Az eredmények képernyőképe](./media/app-insights-usage-impact/0003-results.png)

Ebben az esetben **Termékoldalára** betöltési ideje növeli az átváltási árfolyam **kattintott beszerzési termék** leáll. A fenti terjesztési alapján, az optimális lap betöltési időtartama 3.5-ös másodperc volt megcélozni a potenciális 55 % átváltási árfolyam eléréséhez. Lapbetöltési idő, 3.5-ös másodperc alatt csökkentése érdekében további teljesítménynövekedést jelenleg függ további átalakítás előnyeit.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Mi történik, ha Lapmegtekintések vagyok nyomon követése, vagy egyéni módon alkalommal betölteni?

Hatása a szabványos és az egyéni tulajdonságok és mérések támogatja. Használni kívánt függetlenül. Időtartamban az elsődleges és másodlagos események szűrők segítségével pontosabb beolvasása.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Más országokból vagy régiókból felhasználók alakít át különböző ütemben?

1. Válassza ki a lap nézetet a **a nézet** legördülő menüből.
2. Válassza ki az "Ország vagy régió" a **elemzése hogyan annak** legördülő lista
3. Az a **hatással van az használatát** legördülő menüben válassza a nézet egy felhasználói felületi elem megfelelő, egyéni esemény döntött, hogy az 1. lépésben.

Ebben az esetben az eredmények már nem felelnek meg a folyamatos x tengely modellnek, akárcsak az első. Ehelyett egy szegmentált tölcsér hasonló képi megjelenítés jelenik meg. Rendezze **használati** az egyéni esemény ország alapján átalakítása változata megtekintéséhez.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Hogyan a hatás eszköz átalakítás sebesség kiszámítása?

A technikai részletek alatt a hatás eszköz támaszkodik a [Pearson korrelációs együttható] (https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Eredmények arra az esetre vonatkoznak -1 és 1 -1, nulla korrelációs és jelölő pozitív korrelációs 1 jelölő között.

Az alapvető információkat hatáselemzés működése a következőképpen történik:

Így _A_ = a fő megtekintése/egyéni esemény/lapkérelem az első legördülő lista lehetőséget választja. (**a lapmegtekintés**).

Lehetővé teszik _B_ a másodlagos megtekintése/egyéni esemény választja = (**hatással van az használatát**).

A kijelölt időtartományban a felhasználóktól a munkamenetek mintát hatás megvizsgálja. Minden egyes előfordulásakor jelek minden munkamenethez _A_.

Munkamenetek majd lebontva két különböző a _subsessions_ két feltételek valamelyike alapján:

- A konvertált subsession végződő munkamenet áll egy _B_ esemény és magában foglalja az összes _A_ előtti események _B_.
- Egy konvertálatlan subsession akkor fordul elő, ha az összes _A_egy terminált nélkül történik a _B_.

Hogyan végeredményben kiszámítása a hatása attól függően változik, hogy azt vannak elemzése metrika vagy dimenzió. Minden metrikáihoz _A_végpontjára egy subsession a program átlagolja. Mivel a dimensions érték _A_ hozzájárul _1/N_ rendelt érték _B_ ahol _N_ száma_A_által a a subsession.

## <a name="next-steps"></a>További lépések

- Ahhoz, hogy a használati tapasztalatok, küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [lapmegtekintés](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha egyéni események vagy Lapmegtekintések már küld, megismerkedhet a használati eszközök további, a szolgáltatás használatát a felhasználók.
    - [Tölcsérek](usage-funnels.md)
    - [Megőrzés](app-insights-usage-retention.md)
    - [Felhasználói folyamatok](app-insights-usage-flows.md)
    - [Munkafüzetek](app-insights-usage-workbooks.md)
    - [Felhasználói környezet hozzáadása](app-insights-usage-send-user-context.md)