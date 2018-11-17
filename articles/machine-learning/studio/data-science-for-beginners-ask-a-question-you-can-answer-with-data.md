---
title: Kérje meg a kérdés adatok segítségével választ kaphat - adatok adatelemzési kapcsolatos problémák – az Azure Machine Learning |} A Microsoft Docs
description: Megtudhatja, hogyan határozhatja meg az adatelemzés kezdőknek videó 3 éles data science kérdés. Besorolási és regressziós kérdések összehasonlítását tartalmazza.
keywords: Data science problémák, data science kérdése van, állítson össze a kérdéseket, regressziós kérdések, besorolási kérdése van, éles kérdés
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cjgronlund
ms.assetid: 5b9501e3-9964-417a-8ffc-8913103da77b
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: 34d47c284b06a59f738d7a56071b9227d204cc2f
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51821059"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Adatokkal megválaszolható kérdések megfogalmazása
## <a name="video-3-data-science-for-beginners-series"></a>3. Videó: Az adatelemzés kezdőknek sorozat
Megtudhatja, hogyan határozhatja meg a data science probléma, az adatelemzés kezdőknek videó 3 kérdést. Ez a videó besorolási és regressziós algoritmus kérdések összehasonlítását tartalmazza.

A lehető leghatékonyabban a sorozat, tekintse meg az összes. [Nyissa meg a videók listájában](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Ez a sorozat egyéb videók
*Adatelemzés kezdőknek* egy gyors bevezetőt az öt rövid videóban a rendszer.

* 1. Videó: [az 5 kérdés adatelemzés választ ad](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 mp)*
* 2. Videó: [adatelemzési készen áll az adatok?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 perc 56 másodperc)*
* 3. Videó: Tegyen fel kérdést, az adatok a válasz
* 4. Videó: [egy egyszerű modellel válasz előrejelzése](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 másodperc)*
* 5. Videó: [más emberek munkájának lemásolása az adatelemzéshez való másolása](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 másodperc)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>A szövegben: Tegyen fel kérdést, az adatok a válasz
Üdvözli az a sorozat harmadik videó "Adatelemzés kezdőknek."  

Ezt az eszközt a kap néhány tipp a-kérdések megfogalmazása adatokkal kidolgozásában.

Kaphat további ki ezt a videót, ha először tekintse meg az oktatóanyag-sorozatban a két korábbi videók: "az 5 kérdések adatelemző meg tudja válaszolni" és "Az adatelemzés készen áll az adatok?"

## <a name="ask-a-sharp-question"></a>Éles kérdés feltevése
Beszéltünk hogyan adatelemzés során a rendszer egy kérdésre adott válasz előrejelzése (más néven kategóriákat vagy címkéket) nevek és számok használatával. Nem lehet egyszerűen bármelyik kérdés; azonban kell egy *éles kérdést.*

Országról kérdés nem rendelkezik egy vagy több vet fel. Éles kérdést kell.

Tegyük fel, hogy megtalálta, egy-egy genie, akik bármely tegye fel kérdését valósághűen mutathatja be válaszolni fog magic lamp. De mischievous genie, és ő lesz próbál győződjön meg arról, a válasz országról és zavarosnak, ahogy azt a nyelvet a lenti listában kaphat. Szeretne egy kérdést, hogy ő nem segít, de állapítható meg, hogy meg szeretné tudni, hogy így záródó rögzíteni neki.

Ha a kérdés feltevése országról, például a "Mi történik meg a készlet?", a genie előfordulhat, hogy választ, "az árat fog módosítása". Ez egy truthful választ, de az nem nagyon hasznos.

De ha voltak-e éles kérdése, mint például a "Mi a készlet eladási ár lesz jövő héten?", a genie nem, de ez adjon meg egy adott választ, és előre jelezni egy eladási ár.

## <a name="examples-of-your-answer-target-data"></a>A válasz-példákat: célalkalmazás adatok
Után, a kérdés megfogalmazásához, ellenőrizze, hogy rendelkezik-e a válasz-példákat az adatokban.

Ha a kérdést a "Mi a készlet eladási ár lesz a jövő héten?" Ezután van, hogy az adatok a tőzsdei árfolyam előzményeit tartalmazza.

Ha a kérdést a "sikertelen először melyik az a flotta autó fog?" Ezután van, hogy az adatok korábbi hibák adatait tartalmazza.

![A céloznia adatok számára, a válasz-példákat. Állítson össze egy data science kérdést.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Ezekben a példákban válasz a cél nevezzük. A cél, mit szeretne előre jelezni jövőbeli adatpontok kapcsolatos egy kategóriát, vagy egy szám-e.

Ha nem rendelkezik a célként megadott adatokat, akkor be kell szereznie néhány. Azonosra, e nélkül a kérdése megválaszolásában.

## <a name="reformulate-your-question"></a>A kérdés újraszövegezése
Egyes esetekben is megjegyzések átfogalmazása beolvasni egy több hasznos választ kérdésére.

"Az ezen adatok pont A vagy B?" kérdésre előrejelzi, kategória (vagy a neve vagy címkéje) valamit. Fogadja a hívást, hogy használjuk egy *osztályozó algoritmus*.

A kérdés "Mennyi?" vagy a "Hány?" előrejelzi egy adott mennyiség. Fogadja a hívást a használjuk egy *regresszió algoritmus*.

Ha szeretné látni, hogyan tudjuk alakíthatja át ezeket, nézzük meg a kérdést, az "melyik hírek történetet is a legjobban érdekli az olvasó?" Kéri számos lehetőségek – az egyetlen választási előrejelzése más szóval "Az ezen A vagy B vagy C vagy D?" - és a egy osztályozó algoritmus használna.

De lehet, hogy ezt a kérdést könnyebben válaszolniuk, ha azt megjegyzések átfogalmazása "hogyan érdekes, minden egyes történetet ezen a listán, ez az olvasó?" Minden egyes cikk biztosíthat most egy numerikus pontszámot, és ezután könnyedén azonosíthatja a legmagasabb pontozási cikket. Ez az egy regressziós kérdés be a besorolási kérdését átfogalmazni vagy IP-címek fenntartási?

![A kérdés újraszövegezése. Besorolási kérdés és regressziós kérdést.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Hogyan tegye fel kérdése, mely algoritmus egy valami tudhatja meg válaszként.

Látni fogja, hogy az egyes algoritmusok – mint a hírek történetet példában - családok szorosan kapcsolódnak. Felteheti az algoritmus, amely felkínálja a leghasznosabb választ is újraszövegezése.

De, legfontosabb kérje meg, hogy éles kérdést: – a kérdést, hogy meg tudja válaszolni, adatokkal. És ellenőrizze, hogy rendelkezik a megfelelő adatokat, hogy fogadja a hívást.

Beszéltünk alapelveivel kérdéseket tehet fel az adatok a válasz.

Mindenképpen tekintse meg a "Data Science a kezdők" a Microsoft Azure Machine Learning más videókat.

## <a name="next-steps"></a>További lépések
* [Egy első adatelemzési kísérlet a Machine Learning Studio kipróbálása](create-experiment.md)
* [Bevezetés a gépi tanulás a Microsoft Azure](what-is-machine-learning.md)
