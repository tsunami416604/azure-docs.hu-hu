---
title: Olyan kérdés feladása, amelyet az adatok megválaszolhatnak
titleSuffix: ML Studio (classic) - Azure
description: Ismerje meg, hogyan fogalmazhat meg egy éles adatelemzési kérdést az adattudomány kezdőknek videó 3 című videóban. Tartalmazza az osztályozási és regressziós kérdések összehasonlítását.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 26837337b49d79a26404fd6709b036f6907720f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838841"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Adatokkal megválaszolható kérdések megfogalmazása
## <a name="video-3-data-science-for-beginners-series"></a>3. videó: Adattudomány kezdőknek sorozat
Ismerje meg, hogyan fogalmazhat meg egy adatelemzési problémát egy kérdésben a Kezdők nek készült adattudomány 3. Ez a videó a besorolási és regressziós algoritmusok kérdéseinek összehasonlítását tartalmazza.

Ahhoz, hogy a legtöbbet hozza ki a sorozat, nézd meg őket. [Ugrás a videók listájára](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Egyéb videók ebben a sorozatban
*Data Science kezdőknek* egy gyors bevezetés az adattudomány öt rövid videók.

* 1. videó: [Az 5 kérdés adatelemzési válaszok](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 mp)*
* 2. videó: [Készen áll az adatok az adatelemzésre?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 perc 56 mp)*
* 3. videó: Olyan kérdés feltehet és válaszolhat adatokkal
* 4. videó: [Válasz előrejelzése egy egyszerű modellel](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 mp)*
* 5. videó: [Mások munkájának másolása adatelemzéshez](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 mp)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Átirat: Olyan kérdés feltehet ő, amire adatokkal megválaszolható
Üdvözöljük a harmadik videó a sorozat "Data Science kezdőknek."  

Ebben néhány tippet kap az adatokkal megválaszolható kérdés megfogalmazásához.

Lehet, hogy többet ki ebből a videóból, ha először megnézi a sorozat két korábbi videóját: "Az 5 kérdés adattudománya megválaszolhatja" és "Készen áll-e az adatai az adatelemzésre?"

## <a name="ask-a-sharp-question"></a>Éles kérdés feltehet fel
Már beszéltünk arról, hogy az adattudomány a nevek (más néven kategóriák vagy címkék) és számok használatával történő használatával a kérdésre adott válasz előrejelzéséhez. De ez nem lehet akármilyen kérdés; *éles kérdésnek* kell lennie.

Egy homályos kérdésre nem kell nevet vagy számot adni. Egy éles kérdés kell.

Képzeld el, hogy találtál egy varázslámpát egy dzsinnnel, aki őszintén válaszol minden kérdésedre. De ez egy csintalan dzsinn, aki megpróbálja, hogy a válasz olyan homályos és zavaros, mint tudnak megúszni. Egy olyan kérdéssel akarod leszögezni őket, ami annyira légmentes, hogy nem tudnak segíteni, de elmondják, amit tudni akarnak.

Ha felteszel egy homályos kérdést, például: "Mi fog történni a részvényeimmel?", a dzsinn azt válaszolhatná, hogy "Az ár meg fog változni". Ez egy őszinte válasz, de nem sokat segít.

De ha úgy döntesz, hogy kérje egy éles kérdés, mint a "Mi lesz a készlet eladási ár a jövő héten?", a dzsinn nem tud segíteni, de kapsz egy konkrét választ, és megjósolni az eladási ár.

## <a name="examples-of-your-answer-target-data"></a>Példák a válaszra: Céladatok
Miután megfogalmazta a kérdést, ellenőrizze, hogy van-e példa a válaszra az adataiban.

Ha a kérdés ünk: "Mi lesz a készlet emgedineladási ára a jövő héten?" akkor meg kell bizonyosonunk arról, hogy az adataink tartalmazzák a részvényárfolyam előzményeit.

Ha a kérdésünk az, hogy "Melyik autó fog először meghibásodni a flottámban?" akkor meg kell bizonyosonunk arról, hogy adataink tartalmazzák a korábbi hibákra vonatkozó információkat.

![Céladatok - példák a válaszra. Fogalmazzon meg egy adatelemzési kérdést.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Ezeket a válaszokat célpontnak nevezzük. A cél az, amit próbálunk megjósolni a jövőbeli adatpontokról, legyen az kategória vagy szám.

Ha nem rendelkezik céladatokkal, be kell szereznie néhányat. Nélküle nem tudsz válaszolni a kérdésedre.

## <a name="reformulate-your-question"></a>Fogalmazza át a kérdését
Néha átfogalmazhatja a kérdését, hogy hasznosabb választ kapjon.

A kérdés: "Ez az adatpont A vagy B?" megjósolja valaminek a kategóriáját (vagy nevét vagy címkéjét). A válaszhoz *osztályozási algoritmust*használunk.

A kérdés: "Mennyi?" vagy "Hány?" megjósol egy összeget. A válaszhoz *regressziós algoritmust*használunk.

Hogy lássuk, hogyan tudjuk átalakítani ezeket, nézzük meg a kérdést: "Melyik hír a legérdekesebb, hogy ez az olvasó?" Azt kéri, hogy a jóslat egyetlen választás számos lehetőség - más szóval "Ez az A vagy B vagy C vagy D?" - és osztályozási algoritmust használna.

De ez a kérdés könnyebb lehet válaszolni, ha átfogalmazni, hogy "Mennyire érdekes az egyes történetek ezen a listán, hogy ez az olvasó?" Most minden cikknek numerikus pontszámot adhat, majd könnyen azonosíthatja a legmagasabb pontszámot. Ez egy átfogalmazása a besorolási kérdés egy regressziós kérdés, vagy mennyi?

![Fogalmazza át a kérdését. Besorolási kérdés vs. regressziós kérdés.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Hogyan tesz fel egy kérdést egy nyom, amely algoritmus adhat választ.

Azt találjuk, hogy bizonyos családok algoritmusok - mint azok a mi hír példa - szorosan kapcsolódnak. Újrafogalmazhatja a kérdést, hogy a leghasznosabb választ hozó algoritmust használja.

De ami a legfontosabb, tegye fel ezt az éles kérdést - azt a kérdést, amelyet adatokkal tud megválaszolni. És győződjön meg róla, hogy a megfelelő adatokat válaszolni.

Beszéltünk néhány alapelvről, amelyek egy olyan kérdés feltevéséhez, amire adatokkal válaszolhat.

Győződjön meg róla, hogy nézd meg a többi videó a "Data Science kezdőknek" a Microsoft Azure Machine Learning Studio (klasszikus).

## <a name="next-steps"></a>További lépések
* [Próbálja ki az első adatelemzési kísérletet a Machine Learning Studio-val (klasszikus)](create-experiment.md)
* [Bevezetés a Microsoft Azure-alapú gépi tanulásba](/azure/machine-learning/preview/overview-what-is-azure-ml)
