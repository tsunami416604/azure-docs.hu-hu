---
title: Adatelemzés kezdőknek
titleSuffix: ML Studio (classic) - Azure
description: Adatelemzés kezdőknek van megtanítja 5 rövid videót, az alapvető fogalmait kezdve az 5 kérdések adatelemzés választ ad. Az Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: af0611e121228a587e159bd4e6529c807132d16d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204443"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Adatelemzés kezdőknek – 1. videó: 5 kérdés, amelyre az adatelemzés választ ad
Gyors bevezetést nyerhet az adatelemzésből az adatelemzésből a *kezdők számára* öt rövid videóban egy legfontosabb adattudóstól. Ezeket a videókat egyszerű, de hasznos,-e Önt érdeklő adatelemzésre vagy dolgozik, az adatszakértők.

Ez a videó első tárgya bármilyen típusú kérdéseket, amelyeket az adatelemzés segítségével választ kaphat. A lehető leghatékonyabban a sorozat, tekintse meg az összes. [Ugrás a videók listájára](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Ez a sorozat egyéb videók
Az *adatelemzés kezdőknek* gyors bevezetést biztosítanak az adatelemzésből, így összesen 25 percet vesz igénybe. Tekintse meg az összes öt videó:

* 1\. Videó: A 5 kérdés adatelemzés választ ad
* 2\. videó: [készen áll](data-science-for-beginners-is-your-data-ready-for-data-science.md) az adatelemzésre? *(4 perc 56 mp)*
* 3\. videó: [Kérdezzen rá az adatválaszra](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 mp)*
* 4\. videó: [Válasz Megjósolása egy egyszerű modellel](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 mp)*
* 5\. videó: [a többi személy munkájának másolása az adatelemzéshez](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 mp)*

## <a name="transcript-the-5-questions-data-science-answers"></a>A szövegben: Az 5 kérdés adatelemzés választ ad
szia! Köszönti a *kezdőknek szóló*Video Series-adatelemzés.

Adatelemzési átköltöztetését, lehet, így e fogja bevezetni az alapokat itt egyenletek és programozási szakkifejezéseivel számítógép nélkül.

Az első videó tárgyaljuk "adatelemzés választ ad a 5 kérdés,."

Adatelemzés a kérdésekre adott válaszok előrejelzése számok és nevének (más néven kategóriákat vagy címkéket) használja.

Lehet, hogy meglepő, de az *adattudományi válaszok közül csak öt kérdés*:

* Az ebben A vagy B?
* Az Ez furcsának?
* Mennyi – vagy – hány?
* Hogyan ez vannak rendezve?
* Mit tegyek mellett?

Ezek a kérdések mindegyike külön összessége, machine learning módszerek, úgynevezett algoritmusokat használnak a válaszolt.

Hasznos lehet a recept, az algoritmus és az adatokat, mint az összetevők állításoknak. Egy algoritmus bemutatja, hogyan lehet egyesíteni, és kombinálhatók az adatok annak érdekében, hogy a válasz. A blender hasonlóak a számítógépeken. A nehezét, az algoritmus a legtöbb ők az Ön számára, és tesznek, lefut.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Kérdés: 1: Az ebben A vagy B? besorolási algoritmusokat használ
Kezdjük a kérdést: Ez A vagy B van?

![Besorolási algoritmusok: Ez A vagy B van?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Az operációsrendszer-algoritmusok két osztályú osztályozási nevezzük.

Ez hasznos, amely csak két lehetséges válaszok rendelkezik minden kérdést.

Például:

* Ez kulcsszava sikertelen lesz a következő 1000 mérföld: Igen vagy nem?
* Amely elérhetővé teszi a további ügyfelek: egy 5 USD kuponkód vagy 25 %-os kedvezményt?

Ezt a kérdést is képes rephrased tartalmazza a több mint két lehetőség közül választhat: van ez A vagy B vagy C vagy D, stb.?  Ezt nevezzük többosztályos osztályozási és a hozzá tartozó hasznos, ha több – vagy több ezer – lehetséges válaszokat. Többosztályos osztályozási úgy dönt, hogy a legnagyobb valószínűséggel egy.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>2\. kérdés: Van furcsának? anomáliadetektálási észlelési algoritmusokat használ
A következő kérdésben, az adatelemzés választ is van: a szokatlan megfogalmazást van? A kérdésre összessége, anomáliadetektálás úgynevezett algoritmusokat.

![Anomáliadetektálási észlelési algoritmusait: Ez furcsa van?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Ha rendelkezik bankkártyával, már előnyben részesült a anomáliák észlelése során. Hitelkártya cég elemzi a vásárlási mintákat, úgy, hogy azok riasztja Önt, a lehetséges csalásról. Díjakat, amelyek "furcsának" lehet, ahol nem rendszerint vásárol egy tároló vagy szokatlanul régen elemeire vásárlási vásárlás.

Ezt a kérdést a sokféleképp hasznos lehet. Például:

* Ha egy autó-nyomás mérőműszer, érdemes tudni, hogy: a normál olvasása nyomásmérő?
* Ha az internetet figyeli, érdemes tudnia, hogy ez az üzenet jellemző az internetről?

Anomáliadetektálás jelzők váratlan vagy szokatlan események vagy viselkedéseket kezdeményezhet. A keresőmotorok biztosít hova kell néznie a problémákat.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Kérdés: 3: Mennyi? vagy hogy sok? regressziós algoritmust használja.
Sokkal gépi tanulási is tudja jelezni a hogyan választ? vagy hogy sok? Az olyan ezt a kérdést algoritmuscsaládot regressziós nevezzük.

![Regresszió algoritmus: mennyi? vagy hogy sok?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regresszió algoritmus előrejelzéseket numerikus, például:

* Mi a hőmérséklet lesz a következő kedd?  
* Mi lesz a negyedik negyedév értékesítési?

Segítségükkel könnyebben bármilyen, amely kéri a szám a kérdésére választ kaphat.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>4 kérdés: Hogyan ez vannak rendezve? Fürtszolgáltatás algoritmusokat használ
Most már az utolsó két kérdések olyan speciális kicsit.

Néha szeretné tudni, hogy egy adatkészlet - struktúrája hogyan ez vannak rendezve? Ebben a kérdésben nem rendelkezik olyan példákkal, amelyekkel már ismeri a eredményeit.

Nincsenek nagy mennyiségű adatok struktúráját megtalálása módjai. Egyik lehetőség van a fürtszolgáltatás. Adatok, elkülöníti természetes "halmozza,", a könnyebb értelmezése. Fürtözési, nincs egyetlen helyes válasz nem.

![Fürtszolgáltatás algoritmusok: hogyan ez vannak rendezve?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Fürtözési kérdések gyakori alkalmazási helyzetek:

* Melyik megtekintők, például filmek egyező típusú?
* Melyik nyomtatótípusra ugyanúgy sikertelen?

Megismerése, hogyan vannak rendszerezve, hogy jobban átláthassák - és előrejelzése - viselkedések és események.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>5\. kérdés: Mit tegyek most? használja a megerősítő tanulási algoritmus
Az utolsó kérdés – Mi a teendő most? – használja a megerősítő tanulást nevű algoritmusok összessége.

Hogyan agyából RAT és az emberek választ büntetés és a felhőtechnológia által inspirációt volt a megerősítő tanulást. Ezek az algoritmusok ismerje meg az eredményeket, és döntse el, a következő művelet.

Megerősítő tanulást általában jó megoldás lehet, hogy el kell végeznie a sok kis döntések emberi útmutatás nélkül automatizált rendszerekhez.

![Megerősítő tanulási algoritmus: Mi a teendő ezután?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Kapcsolatos milyen műveletet kell meghoznia – általában egy gép vagy a robot a kérdéseket, megválaszolja mindig állnak. Példák:

* Ha a hőmérséklet egy adott vezérlő rendszer vagyok: állítsa be a hőmérséklet, vagy hagyja, ahol a szolgáltatás?  
* Ha egy önkiszolgáló autó vagyok: egy sárga világos féknek vagy gyorsabb?  
* A robot vákuumot: megtarthatja a vacuuming, vagy lépjen vissza a díjszabási állomás?

Megerősítő tanulási algoritmusokat, próbálkozást, go, képzés adatgyűjtés.

Így – ez az 5 kérdések adatelemzési válaszolhat.

## <a name="next-steps"></a>További lépések
* [Próbálja ki az első adatelemzési kísérletet Machine Learning Studio (klasszikus)](create-experiment.md)
* [Bevezetés a Machine Learning on Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
