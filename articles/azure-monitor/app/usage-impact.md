---
title: Az Azure Application Insights használati hatása | Microsoft-dokumentumok
description: Elemezze, hogy a különböző tulajdonságok milyen hatással lehetnek az alkalmazások egyes részeinek konverziós arányára.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 787221c4df3f06029d19ee779a28bb763723f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671035"
---
# <a name="impact-analysis-with-application-insights"></a>Hatáselemzés az Application Insights segítségével

Az Impact azt elemzi, hogy a betöltési idők és más tulajdonságok hogyan befolyásolják az alkalmazás különböző részeinek konverziós arányát. Pontosabban fogalmazva felderíti, hogy az **oldalnézet**, **az egyéni események**vagy **kérelmek** bármely **dimenziója** hogyan befolyásolja egy másik **oldalnézet** vagy **egyéni esemény**használatát. 

![Érintőeszköz](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Még mindig nem biztos benne, mit csinál az Impact?

Az impact egyik módja az, hogy a végső eszköz az érvek rendezésére a csapategy tagja, hogy milyen lassúság bizonyos szempontból a webhely befolyásolja, hogy a felhasználók kibír körül. Bár a felhasználók tolerálhatnak egy bizonyos mértékű lassúságot, az Impact betekintést nyújt abba, hogyan lehet a legjobban egyensúlyba hozni az optimalizálást és a teljesítményt a felhasználói konverzió maximalizálása érdekében.

De a teljesítmény elemzése csak az Impact képességeinek egy része. Mivel az Impact támogatja az egyéni eseményeket és dimenziókat, az olyan kérdések megválaszolása, mint például, hogy a felhasználói böngésző választása hogyan korrelál a különböző konverziós arányokkal, csak néhány kattintásnyira van.

![Képernyőkép-átalakítás böngészők által](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> Az Application Insights-erőforrásnak oldalmegtekintéseket vagy egyéni eseményeket kell tartalmaznia az Impact eszköz használatához. [Megtudhatja, hogy miként állíthatja be az alkalmazást úgy, hogy automatikusan összegyűjtse az oldalmegtekintéseket az Application Insights JavaScript SDK segítségével.](../../azure-monitor/app/javascript.md) Azt is vegye szem előtt, hogy mivel korrelációt elemez, a mintaméret számít.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Hatással van az oldalbetöltési idő arra, hogy hány anenerlátot konvertálaz oldalamon?

Ha az Impact eszközzel szeretné megválaszolni a kérdéseket, válasszon egy kezdeti oldalmegtekintést, egyéni eseményt vagy kérést.

![Érintőeszköz](./media/usage-impact/0002-dropdown.png)

1. Válasszon egy oldalnézetet **a Lapnézet ide gördülő** legördülő menüből.
2. Hagyja meg az **elemzést, hogy a** legördülő lista az **Időtartam** alapértelmezett kiválasztásánál (Ebben a környezetben az **Időtartam** az **Oldalbetöltési idő aliasa.)**
3. A legördülő legördülő **használatának hatásaihoz** válasszon ki egy egyéni eseményt. Ennek az eseménynek meg kell felelnie az 1.

![Az eredmények képernyőképe](./media/usage-impact/0003-results.png)

Ebben az esetben a **termékoldal** betöltési ideje növeli a konverziós arány **tanévenként a Termék vásárlása kattintással.** A fenti eloszlás alapján az optimális oldalterhelés idotartama 3,5 másodperc lehet a potenciális 55%-os konverziós arány elérése érdekében. A 3,5 másodperc alatti betöltési idő csökkentése érdekében a további teljesítménybeli fejlesztések jelenleg nem kapcsolódnak további konverziós előnyökhöz.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Mi a teendő, ha egyéni módon követem az oldalmegtekintéseket vagy a betöltési időket?

Az Impact támogatja mind a szabványos, mind az egyéni tulajdonságokat és méréseket. Azt használsz, amit akarsz. Időtartam helyett használjon szűrőket az elsődleges és másodlagos események pontosabb.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>A különböző országokból vagy régiókból származó felhasználók eltérő arányban konvertálnak?

1. Válasszon egy oldalnézetet **a Lapnézet ide gördülő** legördülő menüből.
2. Válassza az "Ország vagy régió" lehetőséget **a** legördülő menü
3. A legördülő menü **használatának hatásaihoz** válasszon egy olyan egyéni eseményt, amely megfelel az 1.

Ebben az esetben az eredmények már nem illeszkednek egy folyamatos x-tengelyes modellbe, mint az első példában. Ehelyett egy szegmentált tölcsérhez hasonló vizualizáció jelenik meg. Rendezés **használat** szerint az egyéni eseményre való konverzió ország/régió alapján történő módosításának megtekintéséhez.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Hogyan számítja ki az Impact eszköz ezeket a konverziós arányokat?

A motorháztető alatt az Impact eszköz a [Pearson korrelációs együtthatóra](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)támaszkodik. Az eredményeket -1 és 1 között számítják ki, a -1 negatív lineáris korrelációt, az 1 pedig pozitív lineáris korrelációt.

Az impact analízis működésének alapvető bontása a következő:

Hagyja, hogy _A_ = az első legördülő menüben kiválasztott főoldalnézet/egyéni esemény/kérés legyen. (**Az oldalnézethez).**

Hagyja, _hogy B_ = a kiválasztott másodlagos oldalnézet/egyéni esemény **(hatással van a használatára).**

Az Impact a kiválasztott időtartományban lévő felhasználók összes munkamenetének mintáját vizsgálja. Minden egyes munkamenethez az _A_.

A munkamenetek ezután két különböző _típusú almunkamenetre oszlanak_ a következő két feltétel egyike alapján:

- Az átalakított almunkamenet egy B-edkedéssel végződő munkamenetből áll, és magában foglalja a _B._ előtti összes _A_ eseményt. _B_
- Nem konvertált almunkamenet akkor következik be, amikor az _"A"_ terminál nélkül fordul elő _._

Az Impact végső kiszámításának módja attól függ, hogy metrikus vagy dimenzió szerint elemezzük-e. Metrikák esetén az almunkamenetben az összes _A_'s átlagos. Míg a dimenziók esetében az _Egyes A_ értékek _1/N-rel_ járulnak hozzá a _B-hez_ rendelt értékhez, ahol _N_ az almunkamenetben _lévő A's_száma.

## <a name="next-steps"></a>További lépések

- A használati élmény engedélyezéséhez kezdje el elküldeni [az egyéni eseményeket](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [oldalnézeteket.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Ha már küld egyéni eseményeket vagy oldalnézeteket, fedezze fel a Használati eszközöket, hogy megtudja, hogyan használják a felhasználók a szolgáltatást.
    - [Tölcsérek](usage-funnels.md)
    - [Megőrzés](usage-retention.md)
    - [Felhasználói folyamatok](usage-flows.md)
    - [Munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
    - [Felhasználói környezet hozzáadása](usage-send-user-context.md)
