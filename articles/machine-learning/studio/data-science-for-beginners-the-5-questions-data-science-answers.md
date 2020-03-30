---
title: Adatelemzés kezdőknek
titleSuffix: ML Studio (classic) - Azure
description: Data Science kezdőknek tanítja az alapvető fogalmak at 5 rövid videók, kezdve az 5 kérdések Adattudomány válaszok. Az Azure Machine Learningből.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: af0611e121228a587e159bd4e6529c807132d16d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204443"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Adatelemzés kezdőknek – 1. videó: 5 kérdés, amelyre az adatelemzés választ ad
Az *adattudomány kezdőknek* című rövid videóban, öt rövid adattudóstól kaphat be rövid bevezetőt az adatelemzésbe. Ezek a videók alapvető, de hasznos, hogy érdekli az adattudomány, vagy dolgozik adatszakértőkkel.

Ez az első videó arról szól, hogy milyen kérdésekre, hogy az adattudomány tud válaszolni. Ahhoz, hogy a legtöbbet hozza ki a sorozat, nézd meg őket. [Ugrás a videók listájára](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Egyéb videók ebben a sorozatban
*Data Science kezdőknek* egy gyors bevezetés az adattudomány körülbelül 25 perc összesen. Nézze meg mind az öt videót:

* 1. videó: Az 5 kérdés adatelemzési válaszok
* 2. videó: [Készen áll az adatok az adatelemzésre?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 perc 56 mp)*
* 3. videó: [Olyan kérdés feltehet ő: adatokkal válaszolhat](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 mp)*
* 4. videó: [Válasz előrejelzése egy egyszerű modellel](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 mp)*
* 5. videó: [Mások munkájának másolása adatelemzéshez](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 mp)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Átirat: Az 5 kérdés adatelemzési válaszok
Hello! Üdvözöljük a videó sorozat *Data Science kezdőknek*.

Adattudomány lehet megfélemlítő, úgyhogy be az alapokat itt nélkül egyenletek vagy számítógépes programozási zsargon.

Ebben az első videóban az "5 kérdés adattudományi válaszáról" fogunk beszélni.

Az adatelemzés számokat és neveket (más néven kategóriákat vagy címkéket) használ a kérdésekre adott válaszok előrejelzéséhez.

Lehet, hogy meglepi, de *már csak öt kérdés, hogy az adatok tudomány választ:*

* Ez a "A" vagy a B?
* Nem furcsa ez?
* Mennyi - vagy - mennyi?
* Hogy szervezik ezt meg?
* Mi a teendő ezután?

Ezek a kérdések mindegyike választ egy külön család gépi tanulási módszerek, az úgynevezett algoritmusok.

Hasznos lehet egy algoritmust receptként, az adatokat pedig összetevőként gondolni. Egy algoritmus megmondja, hogyan kell kombinálni és keverni az adatokat annak érdekében, hogy választ kapjon. A számítógépek olyanok, mint egy turmixgép. Ők a legtöbb kemény munka az algoritmus az Ön számára, és ők elég gyorsan.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>1. kérdés: Ez A vagy B? osztályozási algoritmusokat használ
Kezdjük azzal a kérdéssel: Ez a következő A vagy B?

![Besorolási algoritmusok: Ez Az A vagy B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Ezt az algoritmuscsaládot kétosztályos besorolásnak nevezzük.

Ez hasznos minden olyan kérdésre, amely csak két lehetséges választ.

Példa:

* Vajon ez a gumiabroncs nem a következő 1000 mérföld: Igen vagy nem?
* Amely több ügyfelet hoz: egy $5 kupont vagy egy 25% kedvezményt?

Ez a kérdés is lehet átfogalmazni, hogy több mint két lehetőség: Ez az A vagy B vagy C vagy D, stb?  Ezt többosztályos besorolásnak nevezzük, és akkor hasznos, ha több – vagy több ezer – lehetséges válasza van. A többosztályos besorolás a legvalószínűbbet választja.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>2. kérdés: Ez furcsa? anomáliadetektálási algoritmusokat használ
A következő kérdés adattudomány tud válaszolni: Van ez furcsa? Erre a kérdésre az anomáliadetektálásnak nevezett algoritmuscsalád válaszol.

![Anomália detektálás algoritmusok: Ez furcsa?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Ha van hitelkártyája, már részesült az anomáliaészlelés előnyeiből. A hitelkártya-kibocsátó elemzi a vásárlási mintákat, hogy figyelmeztesse Önt az esetleges csalásokra. A "furcsa" költségek lehetnek olyan üzletekben történő vásárlás, ahol általában nem vásárolsz vagy vásárolsz szokatlanul drága terméket.

Ez a kérdés sok szempontból hasznos lehet. Ilyenek például a következők:

* Ha van egy autó nyomásmérők, érdemes tudni: Ez a nyomásmérő normális?
* Ha az internetet figyeli, tudni szeretné: Ez az üzenet az internetről tipikus?

Az anomáliadetektálás váratlan vagy szokatlan eseményeket vagy viselkedéseket jelöl. Ez ad nyomokat, hogy hol kell keresni a problémákat.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>3. kérdés: Mennyibe kerül? vagy hány? regressziós algoritmusokat használ
Gépi tanulás is megjósolni a választ, hogy mennyi? vagy hány? A kérdést megválaszoló algoritmuscsaládot regressziónak nevezzük.

![Regressziós algoritmusok: Mennyi? vagy hány?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

A regressziós algoritmusok numerikus előrejelzéseket tesznek, például:

* Mennyi lesz a hőmérséklet jövő kedden?  
* Mi lesz a negyedik negyedéves eladásaim?

Segítenek megválaszolni minden olyan kérdést, amely egy számot kér.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>4. kérdés: Hogyan szervezik ezt? fürtözési algoritmusokat használ
Most az utolsó két kérdés egy kicsit fejlettebb.

Néha szeretné megérteni az adatkészlet szerkezetét - Hogyan rendszerezhető ez? Ehhez a kérdéshez nincsenek olyan példák, amelyekről már ismeri az eredményeket.

Sok módja van, hogy kötekedik ki az adatok szerkezetét. Az egyik megközelítés a fürtözés. Az adatokat természetes "csomókra" bontja, a könnyebb értelmezés érdekében. A klaszterek, nincs egy helyes válasz.

![Fürtözési algoritmusok: Hogyan rendszerezhető ez?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Gyakori példák a fürtözési kérdésekre:

* Melyik néző szereti az azonos típusú filmeket?
* Mely nyomtatómodellek nem felelnek meg ugyanúgy?

Az adatok rendszerezésének megértésével jobban megértheti – és előre jelezheti – a viselkedéseket és az eseményeket.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>5. kérdés: Most mit tegyek? megerősítési tanulási algoritmusokat használ
Az utolsó kérdés - Mit tegyek most? – használ egy család algoritmusok úgynevezett megerősítése tanulás.

Megerősítése tanulás ihlette, hogy az agy a patkányok és az emberek reagálnak a büntetés és a jutalom. Ezek az algoritmusok tanulnak az eredményekből, és döntenek a következő műveletről.

Jellemzően, megerősíttanulás van egy jó roham részére gyorsbüfé rendszereket amit kell -hoz csinál egy halom kicsi döntés nélkül emberi irányítás.

![Megerősítés tanulási algoritmusok: Mi a következő teendő?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

A válaszok mindig arról szólnak, hogy milyen lépéseket kell tenni - általában egy gép vagy egy robot által. Példák:

* Ha én vagyok a hőmérséklet-szabályozó rendszer egy ház: Állítsa be a hőmérsékletet, vagy hagyja, hogy hol van?  
* Ha én vagyok az önvezető autó: A sárga fény, fék vagy felgyorsítása?  
* Robotporszívó esetén: Porszívózás közben, vagy menjen vissza a töltőállomásra?

A megerősítési tanulási algoritmusok menet közben gyűjtenek adatokat, tanulva a próbaverzióból és a hibákból.

Tehát ennyi - Az 5 kérdés adattudomány tud válaszolni.

## <a name="next-steps"></a>További lépések
* [Próbálja ki az első adatelemzési kísérletet a Machine Learning Studio-val (klasszikus)](create-experiment.md)
* [Bevezetés a Microsoft Azure-alapú gépi tanulásba](/azure/machine-learning/preview/overview-what-is-azure-ml)
