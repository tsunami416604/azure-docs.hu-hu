---
title: "A 5 adattudomány kérdésekre - Adattudomány kezdőknek - Azure Machine Learning |} Microsoft Docs"
description: "Adattudomány kezdők érték útmutatást ad az alapvető fogalmait a 5 rövid videók, az 5 kérdések adatok tudományos válaszok kezdve. Az Azure gépi tanulás."
keywords: "Ennek során adattudomány, adatok tudományos kezdők, adattudomány kezdők, adatok tudományos alapjai, tudományos kérdésekre, adatok tudományos videó, adatok tudományos bemutatása"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: a01f93ee-01eb-4afe-abbd-cfa035c119b0
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2017
ms.author: cgronlun
ms.openlocfilehash: 47257bdb054b069c864ebf545b0c00bdfb05a9ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Adatelemzés kezdőknek – 1. videó: 5 kérdés, amelyre az adatelemzés választ ad
A gyors Bevezetés a adattudomány az beszerzése *Adattudomány kezdőknek* a egy felső adatok tudósok az öt rövid videók. Videók olyan alapvető, de hasznos, hogy érdekli, ennek során adattudomány vagy adatszakértőkön használata.

Ez a videó első kérdésekhez, amely meg tudja válaszolni, adattudomány típusú tárgya. Ahhoz, hogy minél hatékonyabb működtetését az adatsorozat, tekintse meg azokat. [Ugrás a videók listája](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>A sorozat többi videók
*Adattudomány kezdőknek* van egy gyors Bevezetés az adatok tudományos véve körülbelül 25 percig teljes. Tekintse meg az összes öt videók:

* 1. Videó: Az 5 kérdésekre adatok tudományos válaszok
* 2. Videó: [adattudomány készen áll az adatok?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 perc 56 másodperc)*
* 3. Videó: [tegyen fel kérdést a válasz adatokkal](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 másodperc)*
* 4. Videó: [választ egyszerű modell előrejelzése](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 másodperc)*
* 5. Videó: [mások munkahelyi adattudomány ehhez másolja](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 másodperc)*

## <a name="transcript-the-5-questions-data-science-answers"></a>A Beszélgetés szövegének: A 5 kérdésekre adatok tudományos válaszok
szia! Üdvözli a videósorozat *Adattudomány kezdőknek*.

Adattudomány ijesztő lehet, ezért I lesz szükség az alapok itt egyenleteket vagy a számítógép egy zsargon programozási nélkül.

A első videóban azt szolgáltatással kapcsolatban az "a 5 kérdésekre adatok tudományos válaszokat."

Adattudomány előre jelezni a kérdésekre adott válaszok számok és nevének (más néven a kategóriák vagy címkék) használja.

Akkor lehet, hogy lepje meg, de *adott adatok tudományos válaszok csak öt kérdések nincsenek*:

* Ez A vagy B van?
* Az Ez furcsának?
* Mennyi – vagy – hány?
* Hogyan rendszerezett Ez?
* Mit tegyek mellett?

Ezeket a kérdéseket a machine learning módszerek algoritmusok nevű külön termékcsalád melléket.

Akkor célszerű gondolja át, egy receptet algoritmust és az adatok a összetevőként. Egy algoritmus alapján kombinálhatja, és az adatok kombinálhatók ahhoz, hogy a választ. Számítógépek vannak, például egy keverőgép. Az algoritmus a rögzített munka nagyobb része tehetik meg, és így tesznek, lefut.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>1. kérdés: Az ebben A vagy B? besorolási algoritmusokat használ
Kezdjük a kérdés: Ez A vagy B?

![Besorolási algoritmusok: Ez A vagy B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

A család két osztályú osztályozás nevezik.

Célszerű minden kérdés, amelynek csak két lehetséges válaszok esetében.

Példa:

* Ez kulcsszava sikertelen lesz a következő 1000 miles: Igen vagy nem?
* Ami további ügyfelek elérését: $5-ráta vagy 25 %-os kedvezményes?

Ez a kérdés is lehet rephrased felvenni több mint két lehetőség közül választhat: Ez A vagy B vagy C vagy D, stb.?  Ez a lehetőség multiclass besorolási és annak hasznos, ha több – vagy több ezer volt – lehetséges válaszokat. Multiclass besorolási úgy dönt, hogy a legnagyobb valószínűséggel egy.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>2. kérdés: Ez furcsának van? az anomáliadetektálási észlelési algoritmusokat használ
A következő kérdésre választ ad adattudomány van: Ez furcsa van? Egy anomáliadetektálás nevű család megválaszolja ezt a kérdést.

![Az anomáliadetektálási észlelési algoritmusokat: Ez furcsa van?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Ha hitelkártyával, hogy már korábban anomáliadetektálás élvezte. A hitelkártya vállalati elemzi a beszerzési mintáinak, úgy, hogy azok figyelmeztető lehetséges csalásról. Költségek, amelyek "furcsának" lehet, ahol nem általában felületeknél áruházbeli vagy a vételi szokatlanul pricey elem beszerzési.

Ez a kérdés nagy a módon hasznos lehet. Például:

* Ha egy autó rendelkező alávetni, érdemes tudnia: a normál olvasása érte?
* Az internet figyelés kívánt tudnia: Ez az üzenet az internetről tipikus van?

Anomáliadetektálás jelzők váratlan vagy szokatlan események vagy viselkedés. Hol problémák biztosít keresik.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>3. kérdés: Mennyi? vagy hogyan sok? regressziós algoritmusokat használ
Gépi tanulás képes is becsülhető, hogyan válasz sokkal? vagy hogyan sok? Az, hogy felveszi ezt a kérdést algoritmuscsaládot regressziós nevezik.

![Regressziós algoritmus: mennyi? vagy hogyan sok?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regressziós algoritmus előrejelzésekhez numerikus, például:

* Mi a hőmérséklet lesz a következő kedd?  
* Mi lesz a negyedik negyedév értékesítési?

Annak elősegítése, válaszolja meg a kérdést, amely rákérdez a számára.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>4. kérdés: Hogyan ez rendszerezett? Fürtszolgáltatás algoritmusokat használ
Az utolsó két kérdések most egy fejlettebb bit.

Néha szeretné tudni, hogy a struktúra egy adatkészlet - hogyan ez rendszerezett? Ez a kérdés nincs példák, amelyek már ismeri a eredményekkel.

Nincsenek nagy mennyiségű adatok szerkezete kimenő tease módjai. Egyik módszer van a fürtszolgáltatás. Az elválasztja az adatok természetes "halmozza," a könnyebb értelmezéséhez. Fürtözési, nincs egy megfelelő válasz.

![Fürtszolgáltatás algoritmusok: hogyan ez rendszerezett?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Fürtszolgáltatás kérdések gyakori példák:

* Mely megjelenítők filmek azonos típusú hasonló?
* Nyomtató modellek ugyanúgy sikertelen?

Megismerni, adatok, akkor is jobban megismerni - és előrejelzése - viselkedéshez és események.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>5. kérdés: Mit tegyek most? használja a tanulási algoritmus megerősítése
Az utolsó kérdés – Mi a teendő most? – családba tartozó nevű megerősítése tanulási algoritmust használ.

Megerősítése tanulás által hogyan patkányok és emberek agyából válaszol büntetés és előnyök lett ösztönző. Ezek az algoritmusok eredményekkel megtudjuk, és adja meg a következő műveletet.

Megerősítése tanulás általában remekül beválik, ha automatikus rendszerek, el kell végeznie a sok kisméretű kérdés nélkül emberi útmutatást.

![Tanulási algoritmus megerősítése: Mit tegyek mellett?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Kérdéseket válaszolja meg a rendszer mindig kapcsolatos mit kell meghoznia – általában egy gép vagy egy robot. Például a következők:

* Ha egy házat hőmérséklet ellenőrzési rendszerének vagyok: a hőmérséklet beállítása, vagy hagyja, ahol van?  
* Ha egy önálló vezetői autó vagyok: egy sárga világos féknek vagy felgyorsítani?  
* A robot vákuumot: vacuuming hagyja, vagy lépjen vissza a díjszabási állomás?

Megerősítése tanulási algoritmusok, akkor lépjen, tanulási próbálkozást az adatgyűjtést.

Hogy az – az 5 kérdések adattudomány választ ad.

## <a name="next-steps"></a>Következő lépések
* [Próbálja meg egy első adatok tudományos kísérletben a Machine Learning Studio](create-experiment.md)
* [A Microsoft Azure Machine Learning bemutatása beolvasása](what-is-machine-learning.md)
