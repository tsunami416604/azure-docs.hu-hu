---
title: Készen állnak adatai az elemzésre? Az adatok kiértékelése - Azure Machine Learning |} Microsoft Docs
description: Négy feltételek adatait meg kell felelniük való adattudomány készen áll. Ez a videó rendelkezik konkrét, meghatározott példák segítséget az alapszintű adatok kiértékelése.
keywords: a vonatkozó adatok adatok kiértékelése, adatok, adatok feltételek, készen áll a adatok előkészítése
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cjgronlund
ms.assetid: d502062c-da70-4b21-9054-0bfd9902612e
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: 5ab3c7716485053432240cb74be8ebc60c9ad274
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834617"
---
# <a name="is-your-data-ready-for-data-science"></a>Készen állnak adatai az elemzésre?
## <a name="video-2-data-science-for-beginners-series"></a>2. Videó: Adattudomány kezdők sorozat
Útmutató az adatokat, hogy ellenőrizze, hogy megfelel-e egyszerű feltételt, készen áll a adattudomány kiértékeléséhez.

Ahhoz, hogy minél hatékonyabb működtetését az adatsorozat, tekintse meg azokat. [Ugrás a videók listája](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>A sorozat többi videók
*Adattudomány kezdőknek* van egy gyors Bevezetés a adattudomány az öt rövid videók.

* 1. Videó: [az 5 kérdésekre adatok tudományos válaszok](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 másodperc)*
* 2. Videó: Az adatok készen áll a adattudomány?
* 3. Videó: [tegyen fel kérdést a válasz adatokkal](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 másodperc)*
* 4. Videó: [választ egyszerű modell előrejelzése](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 másodperc)*
* 5. Videó: [mások munkahelyi adattudomány ehhez másolja](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 másodperc)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>A Beszélgetés szövegének: Az adatok készen áll a adattudomány?
Üdvözli a "Az adatok készen áll a adattudomány?" a második videó az adatsorozat *Adattudomány kezdőknek*.  

Előtt adattudomány adhat meg a kívánt válaszokat, akkor adjon neki néhány kiváló minőségű raw anyagok történő együttműködésre. Csakúgy, mint egy pizzaszósz, annál jobb a kiindulási pont, annál jobb a végső termék összetevők elvégzése. 

## <a name="criteria-for-data"></a>Adatok feltételeit
Adattudomány, az egyes összetevők, például együtt kell húzni van:

* Megfelelő
* Csatlakozva
* A pontos
* Elegendő a használata

## <a name="is-your-data-relevant"></a>Fontos az adatokat?
Így az első összetevő - kell vonatkozó adatokat.

![A vonatkozó adatokat, és irreleváns adatok - adatok kiértékelése](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

A bal oldali a tábla egy Boston sáv, az utolsó játékban piros Sox batting átlagos és a legközelebbi kényelmi tárolóban tej ára kívül tesztelt hét személyek vér alkohol szintjét mutatja be.

Ezek az összes tökéletesen jogos adatai. Az egyetlen hiba, nincs jelentősége. Ezek a számok között nem nyilvánvaló kapcsolat áll fenn. Ha valaki akitől tej és a piros Sox batting átlagos aktuális ára, nincs semmilyen módon nem lehetett kitalálni a vér alkohol tartalmuk.

Most tekintse meg a tábla a jobb oldalon. Minden személy törzs ezúttal háttértár mérték továbbá azok volna italok száma.  Minden egyes sorára szereplő számok most kapcsolódik egymáshoz. Ha I akitől a szervezet háttértár és a már korábban Margaritas számát, akkor teheti a saját vér cikkekből alkohol tartalom.

## <a name="do-you-have-connected-data"></a>Tegye csatlakozott adatokat?
A következő összetevő az csatlakoztatott adatai.

![Csatlakoztatott vagy leválasztott adat - adatok feltételek, készen áll az adatok](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Íme néhány hamburgers minőségének vonatkozó adatokat: hőmérséklet patty súly és minősítési újság helyi étele a rács. De figyelje meg, a bal oldali tábla hiányosságait.

A legtöbb adatkészletek hiányzik néhány érték. Gyakori megoldás, hogy rendelkezik az ehhez hasonló lyuk és módon kerülő őket. De ha túl sok hiányzik, az adatok elkezdi Svájc közötti sajt tűnik.

A tábla a bal oldali tekinti meg, ha nincs, sok hiányzó adatot, hogy nehezen rács hőmérséklet és patty súly közötti kapcsolat bármilyen kapja meg. Ebben a példában a leválasztott adatainak megjelenítése.

A jobb oldali tábla azonban megtelt, és kész - csatlakoztatott adatok példát.

## <a name="is-your-data-accurate"></a>Az adatok pontos van?
A következő összetevője pontosságát. Az alábbiakban négy célok találati.

![Hibás adatokat - adatok feltételek és pontos adatok](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Tekintse meg a cél a jobb felső részén. Nincs jogosultság a bikák szem körül szoros csoportosítása. Természetesen ez pontos. Furcsa viselkedése adattudomány nyelvén, alatta a cél jobb teljesítményt is figyelembe veszi a pontos.

Ha ezek nyilak közepére kimenő képezte le, mutatunk be, hogy a rendszer nagyon közel a bikák követheti. A nyilak helyezkednek el minden, a cél körül, nem pontos most minősül, de azok még része a bikák szem, pontos most számít.

Most tekintse meg a bal felső cél. Itt a nyilak találati nagyon közel együtt szoros csoportosítása. Pontos fontosságúak, de mivel a központ ki a bikák szem módon pontatlan fontosságúak. A nyilak a bal alsó célzott pontos és nem pontos is. Ez archer további eljárás szükséges.

## <a name="do-you-have-enough-data-to-work-with"></a>Elegendő adatokra van?
Végezetül #4 összetevője elegendő adatot.

![Elegendő vonatkozó adatok elemzési célú van? Az adatok kiértékelése](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Gondolja, hogy egy ecset stroke elem található a kifestési a tábla minden egyes adatpont. Ha csak néhány őket, lehet, hogy a kifestési intelligens - nehéz mondja el, mi.

Néhány további ecset vonások való hozzáadásakor a kifestési elindítja az beszerzése kissé tisztább biztosít.

Ha alig elég vonások, csak akkor jelenik meg elegendő néhány széleskörű vonatkozó döntések meghozatalában. Ennyi az egész valahol előfordulhat, hogy kívánt látogasson el? A jelek világos, amely a következőképpen néz tiszta vízjel – Igen, ahol fogom szabadságon van.

További adatok hozzáadása a kép tisztább válik, és döntéseket lehet részletesebb. Most vessen egy pillantást a bal oldali bank a három szállodák. Az az előtérben egy architekturális funkcióit is láthatja. Dönthet hagyja el a harmadik emelet a miatt a nézet is.

A megfelelő, csatlakoztatott, pontos adatokat, és ahhoz, hogy rendelkezik az összetevők szükséges néhány kiváló minőségű adattudomány.

Ügyeljen arra, hogy tekintse meg a többi négy videók *Adattudomány kezdőknek* a Microsoft Azure Machine Learning.

## <a name="next-steps"></a>További lépések
* [Próbálja meg egy első adatok tudományos kísérletben a Machine Learning Studio](create-experiment.md)
* [A Microsoft Azure Machine Learning bemutatása beolvasása](what-is-machine-learning.md)
