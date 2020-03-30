---
title: Válaszok előrejelzése regressziós modellekkel
titleSuffix: ML Studio (classic) - Azure
description: Hogyan hozzon létre egy egyszerű regressziós modellt az adattudomány kezdőknek 4. Lineáris regressziót tartalmaz a céladatokkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 6ad9c7912eee6c3f5ec55b9cd7ab340bc79c9db7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837777"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Válasz előrejelzése egy egyszerű modell segítségével
## <a name="video-4-data-science-for-beginners-series"></a>4. videó: Adattudomány kezdőknek sorozat
Ismerje meg, hogyan hozhat létre egy egyszerű regressziós modellt egy gyémánt árának előrejelzéséhez a kezdők számára videó 4. Rajzolunk egy regressziós modellt céladatokkal.

Ahhoz, hogy a legtöbbet hozza ki a sorozat, nézd meg őket. [Ugrás a videók listájára](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Egyéb videók ebben a sorozatban
*Data Science kezdőknek* egy gyors bevezetés az adattudomány öt rövid videók.

* 1. videó: [Az 5 kérdés adatelemzési válaszok](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 mp)*
* 2. videó: [Készen áll az adatok az adatelemzésre?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 perc 56 mp)*
* 3. videó: [Olyan kérdés feltehet ő: adatokkal válaszolhat](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 mp)*
* 4. videó: Válasz előrejelzése egy egyszerű modellel
* 5. videó: [Mások munkájának másolása adatelemzéshez](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 mp)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Átirat: Megjósolni a választ egy egyszerű modell
Üdvözöljük a negyedik videó a "Data Science kezdőknek" sorozat. Ebben egy egyszerű modellt építünk, és előrejelzést készítünk.

A *modell* egy egyszerűsített történet az adatainkról. Megmutatom, mire gondolok.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Releváns, pontos, összekapcsolt, elegendő adat gyűjtése
Mondd, hogy gyémántot akarok venni. Van egy gyűrűm, ami a nagymamámé volt, egy 1,35 karátos gyémánttal, és szeretném, ha lenne egy ötletem, mennyibe fog kerülni. Beviszek egy jegyzettömböt és egy tollat az ékszerboltba, és leírom az összes gyémánt árát a táskában, és hogy mennyit nyomnak karátban. Kezdve az első gyémánt - ez 1,01 karátos és 7366 $.

Most átmegyek, és megteszem a többi gyémántért a boltban.

![Rombusz alakú adatok oszlopai](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Figyelje meg, hogy a listánkkét oszlopból áll. Minden oszlopnak más az attribútuma – a karátban és az árban megadott súly –, és minden sor egyetlen adatpont, amely egyetlen rombuszt jelöl.

Valójában létrehoztunk itt egy kis adatkészletet - egy táblázatot. Figyelje meg, hogy megfelel a minőségi kritériumoknak:

* Az adatok **relevánsak** - a súly egyértelműen az árhoz kapcsolódik
* Ez **pontos** - kétszer is ellenőriztük az árakat, amiket leírunk
* Ez **csatlakoztatva** van - nincsenek üres helyek egyik oszlopban sem
* És amint látni fogjuk, **elég adat,** hogy válaszoljon a kérdésünkre.

## <a name="ask-a-sharp-question"></a>Éles kérdés feltehet fel
Most élesen feltesszük a kérdést: "Mennyibe fog kerülni egy 1,35 karátos gyémánt vásárlása?"

A listánkon nincs 1,35 karátos gyémánt, ezért a többi adatot fel kell használnunk, hogy választ kapjunk a kérdésre.

## <a name="plot-the-existing-data"></a>A meglévő adatok nyomtatása
Az első dolog, amit teszünk, hogy rajzolunk egy vízszintes számvonalat, az úgynevezett tengelyt, hogy feltérképezzük a súlyokat. A súlyok tartománya 0-tól 2-ig terjed, ezért rajzolunk egy vonalat, amely lefedi ezt a tartományt, és minden fél karátosra kullancsot helyezünk.

Ezután rajzolunk egy függőleges tengelyt az ár rögzítéséhez és a vízszintes súlytengelyhez való csatlakoztatásához. Ez dollárban fog elbenne mezed. Most már van egy sor koordináta tengelyünk.

![Súly- és ártengelyek](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Fogjuk ezeket az adatokat most, és egy *szétszórt cselekménysé*alakítjuk. Ez egy nagyszerű módja annak, hogy vizualizálja numerikus adatkészletek.

Az első adatpont, mi szemgolyó egy függőleges vonal 1,01 karátos. Aztán egy vízszintes vonalat bámulunk 7366 dollárért. Ahol találkoznak, rajzolunk egy pont. Ez az első gyémántunk.

Most végigmegyünk minden gyémántezen a listán, és nem ugyanaz a dolog. Ha végeztünk, ezt kapjuk: egy csomó pötty, egy-egy gyémántért.

![Szórási telek](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>A modell rajzolása az adatpontokon keresztül
Most, ha megnézed a poklokat és a kancsalságot, a gyűjtemény úgy néz ki, mint egy kövér, bolyhos vonal. Fogjuk a filctollunkat, és egyenes vonalat húzunk rajta.

Egy vonal megrajzolásával létrehoztunk egy *modellt.* Gondolj erre úgy, mint vesz a valós világban, és hogy egy egyszerű rajzfilm változata is. Most a rajzfilm rossz - a vonal nem megy át az összes adatpontot. De ez egy hasznos egyszerűsítés.

![Lineáris regressziós vonal](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Az a tény, hogy az összes pont nem megy át pontosan a vonalon, rendben van. Az adatszakértők ezt azzal magyarázzák, hogy ott van a modell - ez a vonal -, és akkor minden pontnak van némi *zaja* vagy *varianciája.* Ott van a mögöttes tökéletes kapcsolat, és ott van a kavicsos, valós világ, amely növeli a zajés a bizonytalanság.

Mert próbálunk válaszolni a *kérdésre, hogy mennyi?* Ez az úgynevezett *regresszió*. És mivel egyenes vonalat használunk, ez egy *lineáris regresszió.*

## <a name="use-the-model-to-find-the-answer"></a>Használja a modellt, hogy megtalálja a választ
Most van egy modell, és feltesszük a kérdést: Mennyibe kerül egy 1,35 karátos gyémánt költsége?

Hogy válaszoljunk a kérdésünkre, 1,35 karátos szemünkkel és függőleges vonalat húzunk. Ahol átlépi a modellvonalat, vízszintes vonalat húzunk a dollár tengelyhez. 10,000-nél ér. Boom! Ez a válasz: A 1,35 karátos gyémánt költségek körülbelül 10.000 $.

![A válasz megkeresése a modellen](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Megbízhatósági intervallum létrehozása
Természetes, hogy csoda, hogy pontos ez a jóslat. Ez hasznos tudni, hogy az 1,35 karátos gyémánt lesz nagyon közel 10.000 $, vagy sokkal magasabb vagy alacsonyabb. Hogy ezt kitaláljuk, rajzoljunk egy borítékot a regressziós vonal köré, amely tartalmazza a legtöbb pont. Ezt a borítékot *konfidenciaintervallumnak*nevezzük: Biztosak vagyunk benne, hogy az árak ebbe a borítékba esnek, mert a múltban a legtöbbjük. Rajzolhatunk még két vízszintes vonalat, ahonnan az 1,35 karátos vonal keresztezi a boríték tetejét és alját.

![Megbízhatósági intervallum](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Most már lehet mondani valamit a mi bizalom intervallum: Azt mondhatjuk, magabiztosan, hogy az ár egy 1,35 karátos gyémánt körülbelül $ 10,000 - de lehet, hogy olyan alacsony, mint $ 8,000, és lehet, hogy olyan magas, mint $ 12,000.

## <a name="were-done-with-no-math-or-computers"></a>Végeztünk, matek és számítógépek nélkül.
Azt tettük, amiért az adatszakértőket fizetik, és csak úgy tettük, hogy lerajzoltuk:

* Olyan kérdést tettünk fel, amire adatokkal tudtunk válaszolni
* Építettünk egy *modellt* *lineáris regresszió*
* Tettünk egy *előrejelzést,* kiegészítve egy *kondináty-intervallummal*

És nem használtunk matekot vagy számítógépet.

Ha több információnk lett volna, mint...

* a gyémánt vágása
* színvariációk (milyen közel van a gyémánt, hogy fehér)
* száma zárványok a gyémánt

... akkor több oszlopunk lett volna. Ebben az esetben a matek hasznossá válik. Ha kettőnél több oszlopa van, nehéz a dolokat papírra rajzolni. A matek lehetővé teszi, hogy illeszkedjen, hogy a vonal, vagy hogy a sík az adatok nagyon szépen.

Továbbá, ha ahelyett, hogy csak egy maroknyi gyémánt, mi volt a kétezer-két millió, akkor meg tudod csinálni, hogy a munka sokkal gyorsabb a számítógéppel.

Ma már beszéltünk arról, hogyan kell csinálni a lineáris regresszió, és tettünk egy előrejelzést adatok felhasználásával.

Győződjön meg róla, hogy nézd meg a többi videó a "Data Science kezdőknek" a Microsoft Azure Machine Learning Studio (klasszikus).

## <a name="next-steps"></a>További lépések
* [Próbálja ki az első adatelemzési kísérletet a Machine Learning Studio-val (klasszikus)](create-experiment.md)
* [Bevezetés a Microsoft Azure-alapú gépi tanulásba](/azure/machine-learning/preview/overview-what-is-azure-ml)
