---
title: Regressziós modellek a válaszok előrejelzése
titleSuffix: Azure Machine Learning Studio
description: Hogyan lehet létrehozni egy egyszerű regressziós modell előre jelezni az adatelemzés kezdőknek videó 4 áron. Tartalmaz egy lineáris regressziós cél adatokkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.custom: seodec18
ms.date: 01/03/2018
ms.openlocfilehash: dfac085727ee86be018cfee1e15c3ee51b221763
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819871"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Válasz előrejelzése egy egyszerű modell segítségével
## <a name="video-4-data-science-for-beginners-series"></a>4. Videó: Adatelemzés kezdőknek sorozat
Ismerje meg, hogyan hozhat létre egy egyszerű regressziós modell előre jelezni az adatelemzés kezdőknek videó 4 egy gyémánt árát. Azt fogjuk rajzoljon egy regressziós modellt a célként megadott adatokkal.

A lehető leghatékonyabban a sorozat, tekintse meg az összes. [Nyissa meg a videók listájában](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Ez a sorozat egyéb videók
*Adatelemzés kezdőknek* egy gyors bevezetőt az öt rövid videóban a rendszer.

* 1. Videó: [A 5 kérdés adatelemzés választ ad](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 mp)*
* 2. Videó: [Az adatelemzés készen áll az adatok?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 perc 56 másodperc)*
* 3. Videó: [Tegyen fel kérdést az adatok a válasz](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 másodperc)*
* 4. Videó: Válasz előrejelzése egy egyszerű modell segítségével
* 5. Videó: [Más emberek munkájának lemásolása az adatelemzéshez való másolása](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 másodperc)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>A szövegben: Válasz előrejelzése egy egyszerű modell segítségével
Üdvözli a negyedik videót a "Data Science a kezdők" sorozat. Ezt az eszközt hogy fog egy egyszerű modellt és előrejelzést.

A *modell* egy egyszerűsített történetet az adataival kapcsolatban van. Bemutatom, mi értem.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Megfelelő, a pontos, a csatlakoztatott, elegendő adat összegyűjtése
Tegyük fel, hogy egy gyémánt szeretnék üzemi. Rendelkezem egy kör tartoztak a egy 1.35 karátot gyémánt beállítást a saját vezeték, és szeretném, hogy mennyibe kerül képet kapjon. A Jegyzettömb és a toll tenni az ékszerek tárolóba, és szeretnék írja le a díj az összes az az eset, és azok karát mennyi mérjük a gyémánt. Az első gyémánt - annak 1.01 karát és $7,366 kezdve.

Most halad át, és tegye meg a tárolóban lévő összes többi gyémánt.

![Adatoszlopokat rombuszos](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Figyelje meg, hogy a lista két oszlopot tartalmaz. Minden oszlop rendelkezik egy másik attribútum - súlyozási karát és ár - és minden sor egy egyetlen gyémánt képviselő adatpont.

Valójában létrehoztunk egy kisméretű adatkészlet itt – egy táblát. Figyelje meg, hogy megfelel a feltételeknek a minőségi:

* Az adatok **megfelelő** -súlyozási mindenképp kapcsolatos ár
* Rendelkezik **pontos** – hogy double-checked az árak, amelyek azt írja le
* Rendelkezik **csatlakoztatott** – ne legyenek ezek az oszlopok valamelyikében üres szóközök
* És láthatjuk, ahogy rendelkezik **elegendő** a kérdés megválaszolásában.

## <a name="ask-a-sharp-question"></a>Éles kérdés feltevése
Most már azt fogja jelentenek a a kérdés éles módon: "Ez mennyibe kerül a 1.35 karátot rombusz megvásárlása?"

A lista nem rendelkezik egy 1.35 karátot rombuszos, úgy kell beolvasni a választ a kérdésére, az adatok a rest használata.

## <a name="plot-the-existing-data"></a>A meglévő adatok ábrázolásához.
Az első felveszünk rajzolása vízszintes számát, nevű tengely a diagramhoz végpontkészletben. A súlyok tartománya 0, 2, így azt fogjuk vonal rajzolása, amely lefedi, amely a címtartomány, és csak a minden fél karátot órajel során.

Ezután azt fogja az ár és csatlakoztathatja azt a súly vízszintes tengely függőleges tengely dolgoznak. Ez lesz dollár egységekben. Most már rendelkezünk koordináta tengelyek készletét.

![Súly-és az ár](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Mostantól igénybe ezeket az adatokat, és kapcsolja be fogunk egy *pontdiagram*. Ez remek módja numerikus adathalmaz képi megjelenítése érdekében.

Az első adatpont eyeball azt egy függőleges vonal 1.01 karát címen. Majd hogy eyeball: $7,366 vízszintes vonal. Felel meg, ahol azt rajzoljon egy pont követ. Az első gyémánt Ez jelöli.

Most azt minden egyes gyémánt meg ezen a listán, és végre ugyanezt. Örömmel keresztül, ha ez az amit kapunk: pontokból álló, lemezcsoport, így az egyes gyémánt.

![Pont](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>A modell az adatpontokra rajzolása
A pontok és squint tekinti meg, ha a gyűjtemény néz ki egy fat, intelligens sort. Lehet igénybe vehet a jelölő és rajzoljon egy egyenes vonallal rajta.

A vonal rajzolása által létrehozott egy *modell*. Gondoljon erre úgy, mint véve a való világot és a egy egyszerűsített rajzolt verzió. Most már a rajzolt helytelen – a sor nem fogadja el a összes adatponttal. De, egy hasznos egyszerűsítését.

![Lineáris regressziós egyenes](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Az a tény, hogy minden pont nem pontosan a sor rendben. Az adatszakértők tájékoztatásának, hogy a modell -, amely a sor - és minden pont rendelkezik néhány kimondásával *zaj* vagy *variancia* társítva. Az alapul szolgáló tökéletes kapcsolat áll fenn, és azután ott van a kövecses valós világában, amely hozzáadja a zaj és bizonytalanságot.

Mivel azt a választ a kérdésre próbál *mennyi?* ezt nevezik a *regressziós*. Mivel egy egyenes vonallal használunk, és van egy *lineáris regressziós*.

## <a name="use-the-model-to-find-the-answer"></a>A modell használatával találja a választ
Most már van egy modellt, és megkérjük azt a kérdést: Egy 1.35 karátot gyémánt mennyibe kerül?

A kérdés megválaszolásához azt 1.35 karát szem és a egy függőleges vonal rajzolása. Ha a modell sor azt átlép azt eyeball dollár tengelyhez vízszintes vonal. 10 000, jobb elér. Bumm! Ez a válasz: Egy 1.35 karátot gyémánt 10 000 dollárt kapcsolatos költségek.

![Keresse meg a választ a modell](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Hozzon létre egy megbízhatósági intervallum
Természetes wonder hogyan pontos ez előrejelzésére szolgáló funkció. Érdemes tudni, hogy a 1.35 karátot rombusz lesz nagyon közel 10 000 dollárt, vagy sokkal magasabb vagy alacsonyabb legyen. . Ábra ezt ki, nézzük rajzoljon boríték a regressziós egyenes, amely tartalmazza a legtöbb pont körül. A boríték nevezzük az *megbízhatósági intervallum*: Sajnáljuk, viszonylag abban, hogy árak tartoznak-e a boríték, mert az elmúlt többsége rendelkezik. Két további vízszintes vonal, ahol a 1.35 karátot sor átlép felső és alsó részén borítékot rajzolhat azt.

![Megbízhatósági intervallum](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Most már azt is ismertetés a megbízhatósági intervallum:  Azt is tegyük fel, hogy magabiztosan, hogy egy 1.35 karátot gyémánt díja körülbelül $ 10 000 -, de akár $ 8000-re is lehet, és lehet magas, mint 12 000 $.

## <a name="were-done-with-no-math-or-computers"></a>Már nincs, nincs matematikai vagy számítógépek
Milyen az adatszakértők első fizetős ehhez végrehajtott, és csak a rajzolási tettük:

* A Microsoft feltett egy kérdést, hogy erre a kérdésre sikerült adatokkal
* Úgy alakítottuk ki egy *modell* használatával *lineáris regresszió*
* Végeztünk egy *előrejelzési*, kész, de egy *megbízhatósági intervallum*

És nem használjuk a matematikai vagy számítógépek működtet.

Mostantól Ha kellett volna további információkat, például...

* a kivágási, a rombuszos
* Szín változatok (hogyan zárja be a gyémánt, hogy folyamatban van a fehér)
* az a rombusz befoglalások száma

...helyőrzőkre azt kellett volna több oszlopot. Ebben az esetben a matematikai hasznos lehet. Ha több mint két oszlop van, meglehetősen nehéz pontra rajzolása tanulmány. A matematikai lehetővé teszi, hogy sort, vagy az adatokhoz, hogy adatsík nagyon szépen.

Is helyett mindössze néhány gyémánt, ha két Kaliforniában vagy kétmillió kellett, majd megteheti, hogy sokkal gyorsabban egy számítógépen.

Még ma beszéltünk lineáris regressziós módjáról, és -adatok előrejelzési végeztünk.

Mindenképpen tekintse meg a "Data Science a kezdők" a Microsoft Azure Machine Learning Studio más videókat.

## <a name="next-steps"></a>További lépések
* [Egy első adatelemzési kísérlet a Machine Learning Studio kipróbálása](create-experiment.md)
* [Bevezetés a gépi tanulás a Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
