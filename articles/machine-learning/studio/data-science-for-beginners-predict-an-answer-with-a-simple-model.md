---
title: "Egyszerű regressziós modell - Azure Machine Learning választ előrejelzése |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy egyszerű regressziós modell Adattudomány videó 4 kezdőknek ár előrejelzése céljából. Egy lineáris regressziós cél adatokat tartalmazza."
keywords: "a modell, egyszerű modell, árának előrejelzése, egyszerű regressziós modell létrehozása"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: a28f1fab-e2d8-4663-aa7d-ca3530c8b525
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: cgronlun
ms.openlocfilehash: 79c66fe29c8d51a8cd1db24a024974f943e89e74
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="predict-an-answer-with-a-simple-model"></a>Válasz előrejelzése egy egyszerű modell segítségével
## <a name="video-4-data-science-for-beginners-series"></a>4. Videó: Adattudomány kezdők sorozat
Útmutató a videó 4 kezdőknek Adattudomány egy rombusz árára egyszerű regressziós modell. Azt fogja megrajzolásához egy regressziós modell cél adatokkal.

Ahhoz, hogy minél hatékonyabb működtetését az adatsorozat, tekintse meg azokat. [Ugrás a videók listája](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>A sorozat többi videók
*Adattudomány kezdőknek* van egy gyors Bevezetés a adattudomány az öt rövid videók.

* 1. Videó: [az 5 kérdésekre adatok tudományos válaszok](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 másodperc)*
* 2. Videó: [adattudomány készen áll az adatok?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 perc 56 másodperc)*
* 3. Videó: [tegyen fel kérdést a válasz adatokkal](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 másodperc)*
* 4. Videó: Egyszerű modell választ előrejelzése
* 5. Videó: [mások munkahelyi adattudomány ehhez másolja](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 másodperc)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>A Beszélgetés szövegének: Egyszerű modell választ előrejelzése
Üdvözli a negyedik videó az "adatok tudományos a kezdők" az adatsorozat. Ezt a telepítést azt bemutatjuk egy egyszerű modell létrehozása, ellenőrizze az előrejelzéshez.

A *modell* egy egyszerűsített szövegegység az adatok körül forog. I fogjuk I jelenti.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Megfelelő, a pontos, csatlakoztatott, elég adatok gyűjtése
Tegyük fel például, egy rombusz szeretnék üzemi. Amely egy 1.35 beszúrási rombusz a beállítással a NO@LOC tartoztak gyűrű van, és a kívánt képet kapjon a rendszer mennyibe. A Jegyzettömb és toll tenni a ékszereket tárolóba, és szeretnék írja le az összes, a rombusz – az eset, és azok karát mennyi mérjük az ár. Az első rombusz - fennállt 1.01 karát és $7,366 kezdve.

Most keresztül halad, és tegye meg ezt az összes a más rombusz – a tároló.

![Oszlopok rombusz adatok](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Figyelje meg, hogy a kiválasztott két oszlopot tartalmaz. Mindegyik oszlopnak van egy másik attribútum - a súlyozás karát és ár - és minden egyes sorára egyetlen rombusz jelölő egyetlen adatpont.

Ténylegesen létrehoztunk Önnek egy kis adatkészlet ide - tábla. Figyelje meg, hogy megfelel-e a minőségi feltételek:

* Az adatok **vonatkozó** -súlyozási mindenképpen kapcsolódó ár
* Rendelkezik **pontos** -azt double-checked az árak, amelyek azt írja le
* Rendelkezik **csatlakoztatott** -nincsenek ezeket az oszlopokat az üres szóközök nélkül
* És mivel megtanulhatja, rendelkezik **elég** a kérdése megválaszolásában

## <a name="ask-a-sharp-question"></a>Éles kérdés
Most azt fogja jelent a kérdés éles úgy: "fog mennyibe vásárolható meg a 1.35 beszúrási rombusz?"

A lista nem rendelkezik egy 1.35 beszúrási rombusz, úgy kell használni az adatok a többi beolvasni a választ a kérdésére.

## <a name="plot-the-existing-data"></a>A meglévő adatok ábrázolása
Elsőként azt kell végeznie az rajzolása vízszintes számot, egy tengely, a diagram a súlyok hívása. A súlyok tartománya 0-2, azt fogja rajzolása, amely hozzá van rendelve, amely a tartomány és az egyes fele beszúrási ticks helyezze.

Ezután azt fogja az ár rögzítése, és csatlakoztassa a súlyozás vízszintes tengely függőleges tengely megrajzolásához. Ezzel egység dolláros lesz. Most van koordináta tengelyek készlete.

![Súly és az ár](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Most állapotba ezeket az adatokat, és kapcsolja be az oktatóanyagban módosítjuk egy *pont rajzot*. Ez remek módja numerikus adatkészletek megjelenítéséhez.

Az első adatpont azt eyeball 1.01 karát mérve. Ezt követően: $7,366 vízszintes vonal eyeball azt. Megfelelnek-e, ha azt egy pont megrajzolásához. Az első rombusz jelképez.

Most azt halad át minden rombusz ezen a listán, és tegye meg ugyanezt. A rendszer keresztül, ha ez az azt lekérése: pontokból álló, lemezcsoport, minden egyes rombusz egyet.

![Pontdiagram rajzot](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>A modell a adatpontokra megrajzolásához
Most pontok és squint tekinti meg, ha a gyűjtemény néz fat, intelligens sor. Azt a jelölő igénybe, és egy egyenes vonalat rajta.

Egy sor rajzolási, által létrehozott egy *modell*. Használjon a valós életben véve, és azt simplistic Rajzfilmes verziójának. Most a Rajzfilmes megfelelő – a sor nem halad át az adatpont. Ez azonban hasznos egyszerűsítését.

![Lineáris regressziós egyenes](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Az, hogy a névben nem végighaladnia pontosan a sor az OK gombra. Adatszakértőkön azt ismertetik, ennek kimondásával, hogy a modell -, amely a sor - és minden pont van néhány *zaj* vagy *variancia* társítva. Az alapul szolgáló tökéletes kapcsolat áll fenn, és a zaj és bizonytalanság hozzáadó kövecses, valós világ nincs majd.

Mivel az a kérdés megválaszolásához próbált *mennyi?* ezt nevezik a *regressziós*. Mivel használunk egyenes, és van egy *lineáris regressziós*.

## <a name="use-the-model-to-find-the-answer"></a>Itt megtalálja a választ a modell segítségével
A modell van, és megkérjük, a kérdés: mennyi lesz a 1.35 beszúrási rombusz költség?

A kérdés megválaszolásához azt szem 1.35 karát és egy függőleges vonalat. Ha több érint a modell sor azt eyeball vízszintes vonal dollár a tengelyre. Találatok száma a jobb: 10 000-re. Elterjedése! Ez a válasz: egy 1.35 beszúrási rombusz körülbelül 10 000 $ költségek.

![Itt megtalálja a választ a modell](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Hozzon létre egy megbízhatósági intervallum
Természetes való wonder hogyan pontos ezt nevezzük. Akkor érdemes tisztában lenni, hogy a 1.35 beszúrási rombusz lesz nagyon közel $ 10 000, vagy sokkal magasabb vagy alacsonyabb. Mi a teendő a kimenő, most megrajzolásához borítékot a regressziós egyenes, amely tartalmazza a legtöbb pont körül. A boríték nevezik az *megbízhatósági*: a rendszer közérthető bizonyos abban, hogy az árak esik-e a boríték, mert az elmúlt legtöbbjük rendelkezhet. Ha a 1.35 beszúrási sor keverve használ a felső és alsó részén borítékot azt is tárolt két vízszintesen több sort.

![Megbízhatósági intervallum](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Most azt is ismertetés az vetett bizalmat időköz: magabiztosan azt mondani, hogy egy 1.35 beszúrási rombusz ára készül $ 10 000 -, de lehet akár csupán $8000 és lehet, mint $ 12 000.

## <a name="were-done-with-no-math-or-computers"></a>A Microsoft nem használja, nem matematikai vagy számítógépek
Azt adta milyen adatszakértőkön beolvasása fizetős ehhez, és azt adta azt csak fel:

* Azt kéri, a kérdés, hogy azt az választ sikerült adatokkal
* Azt a beépített egy *modell* használatával *lineáris regressziós*
* A Microsoft tett egy *előrejelzés*, kész, de egy *megbízhatósági intervallum*

És nem használjuk matematikai vagy számítógépek ne.

Ha további információt a Microsoft volt, mostantól például...

* a kivágási, a rombusz
* szín változata (milyen közel a rombusz, hogy éppen fehér)
* az a rombusz befoglalások száma

...helyőrzőkre azt kellett volna több oszlopot. Ebben az esetben a matematikai lesz hasznos. Ha több mint két oszlop, pontokból megrajzolásához papíron rögzített. A matematikai lehetővé teszi, hogy a sor vagy az, hogy az adatok vezérlősík nagyon szépen oszlopnál.

Is ha helyett csak néhány olyan gyémántok, le kellett két példányban vagy két millió, majd munka teheti sokkal gyorsabb, a számítógép.

Napjainkban túlmutatnak tárgyalt lineáris regressziós módjáról, és azt tett adatok előrejelzéshez.

Mindenképpen tekintse meg az "Adatok tudományos a kezdők" a Microsoft Azure Machine Learning más videók.

## <a name="next-steps"></a>További lépések
* [Próbálja meg egy első adatok tudományos kísérletben a Machine Learning Studio](create-experiment.md)
* [A Microsoft Azure Machine Learning bemutatása beolvasása](what-is-machine-learning.md)
