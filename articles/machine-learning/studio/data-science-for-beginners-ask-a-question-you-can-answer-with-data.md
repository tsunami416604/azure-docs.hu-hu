---
title: Kérje meg a kérdés adatok választ ad-adatok tudományos problémák - Azure Machine Learning |} Microsoft Docs
description: Megtudhatja, hogyan állítson össze egy éles adatok tudományos kérdést Adattudomány kezdőknek videó 3. Besorolás és regressziós kérdések összehasonlítását tartalmazza.
keywords: adatok tudományos problémák tudományos kérdésekre, állítson össze kérdéseket, a regressziós kérdések, a besorolás kérdéseket, éles kérdés
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.author: hshapiro
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
ms.openlocfilehash: 2078f7e84522a40cf6fcf6dff860d24307084467
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833801"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Adatokkal megválaszolható kérdések megfogalmazása
## <a name="video-3-data-science-for-beginners-series"></a>3. Videó: Adattudomány kezdők sorozat
Megtudhatja, hogyan állítson össze egy adatok tudományos probléma Adattudomány videó 3 kezdőknek fel kérdést az. Ez a videó besorolási és regressziós algoritmus kérdések összehasonlítását tartalmazza.

Ahhoz, hogy minél hatékonyabb működtetését az adatsorozat, tekintse meg azokat. [Ugrás a videók listája](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>A sorozat többi videók
*Adattudomány kezdőknek* van egy gyors Bevezetés a adattudomány az öt rövid videók.

* 1. Videó: [az 5 kérdésekre adatok tudományos válaszok](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 másodperc)*
* 2. Videó: [adattudomány készen áll az adatok?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 perc 56 másodperc)*
* 3. Videó: Tegyen fel kérdést a válasz adatokkal
* 4. Videó: [választ egyszerű modell előrejelzése](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 másodperc)*
* 5. Videó: [mások munkahelyi adattudomány ehhez másolja](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 másodperc)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>A Beszélgetés szövegének: Tegyen fel kérdést a válasz adatokkal
Üdvözöljük a harmadik videó az adatsorozat "Adattudomány kezdőknek."  

Ez egy, a tippek fog kapni olyan vonatkozókat egy kérdést, a válasz adatokkal.

Kaphat további kívül ezt a videót, ha először tekintse meg a sorozat két korábbi videók: "a 5 kérdések adattudomány meg tudja válaszolni" és "adattudomány készen áll-e az adatok?"

## <a name="ask-a-sharp-question"></a>Éles kérdés
Túlmutatnak tárgyalt hogyan adattudomány során a rendszer (más néven a kategóriák vagy címkék) nevek és számok használatával megjósolható választ a kérdésére. Nem lehet egyszerűen bármelyik kérdés; azonban kell lennie egy *éles kérdést.*

Bizonytalan kérdés nem kell egy vagy több válaszolni. Egy éles kérdést kell.

Tegyük fel, a egy genie, akik bármely tegye fel kérdését kételyei vannak válaszolni fog magic lámpa talált. De mischievous genie, és ezután lesz igyekezzen a válasz bizonytalan és zavaró, ezután a számítógépnél kaphat. PIN-kód számára egy kérdést, így záródó, hogy ő nem súgó, de meghatározzák a szeretné megnézni kívánt.

Ha kérdése bizonytalan, például a "Mi zajlik, megtörténjen-e a saját készlet?", a genie előfordulhat, hogy fogadja a hívást, "az ár változik". Ez egy truthful választ, de nincs nagyon hasznos.

De volt-e kérdés éles, például a "Mi a készlet pénztári ár jövő héten?", a genie nem súgó de adjon meg egy adott fogadja a hívást, és előre jelezni eladási árat.

## <a name="examples-of-your-answer-target-data"></a>A válasz példái: célalkalmazás adatok
Amennyiben az a kérdés állítson össze, ellenőrizze, hogy fel kell-e a válasz példák az adatokon.

Ha a kérdés "Mi a készlet pénztári ár lesz jövő héten?" Ezután azt kell ellenőrizze, hogy az adatok a árfolyam előzmények tartalmaz.

Ha a kérdés "mely car a saját járműflotta érintetlen először sikertelen?" Ezután azt kell győződjön meg arról, hogy az adatok előző hibákkal kapcsolatos információkat tartalmaz.

![Cél adatok - példák a válasz. Állítson össze egy adatok tudományos kérdést.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Ezekben a példákban válaszok target nevezzük. Egy célja mi keressük előre jelezni jövőbeli adatpontok kapcsolatos egy kategóriát vagy egy szám.

Ha még nem rendelkezik target adatokat, szüksége lesz néhány eléréséhez. Ön nem fog tudni anélkül, hogy a kérdése megválaszolásában.

## <a name="reformulate-your-question"></a>A kérdés újraszövegezése
Egyes esetekben is megjegyzések átfogalmazása lekérni egy hasznosabb választ a kérdésére.

A kérdéssel "az adatok pont A vagy B?" a kategória (vagy a név vagy címke) valamit előrejelzi. Fogadja a hívást, hogy használjuk a *osztályozó algoritmus*.

A kérdés "Mennyi?" vagy "Hány?" egy meghatározott előrejelzi. Fogadja a hívást a használjuk a *regressziós algoritmus*.

Hogyan azt alakíthatja át ezek megtekintéséhez vizsgáljuk meg a kérdéssel, "mely hírek szövegegység a legtöbb érdekesnek ezzel az olvasóval?" Kéri sok lehetőségek – az egyetlen választási előrejelzése azaz "Nem ezt A vagy B vagy C vagy D?" - és egy osztályozó algoritmus használna.

De előfordulhat, hogy ez a kérdés könnyebben választ, ha azt megjegyzések átfogalmazása "hogyan érdekes van ezen a listán, hogy az olvasó minden szövegegység?" Minden cikk biztosíthat most egy numerikus pontszám, és majd könnyedén azonosíthatja a legmagasabb pontozási cikk. Ez az egy fogalmazza regressziós kérdés azokat a besorolási kérdés vagy mennyi?

![A kérdés újraszövegezése. Besorolási kérdést vagy regressziós kérdést.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Hogyan kérdése az algoritmus a clue elfogadtatni tudhatja meg választ.

Látni fogja, hogy az egyes algoritmusok - mint a hírek szövegegység példánkban - családok szorosan kapcsolódnak. A kérdés, az algoritmus, amely felkínálja a leghasznosabb válasz használatára is újraszövegezése.

De, legfontosabb kérje meg, hogy éles kérdés - adatokkal meg tudja válaszolni a kérdést. És ellenőrizze, hogy rendelkezik a megfelelő adatokat fogadja a hívást.

Néhány alapelvei egy kérdezzen adatokkal meg tudja válaszolni túlmutatnak tárgyalt.

Mindenképpen tekintse meg az "Adatok tudományos a kezdők" a Microsoft Azure Machine Learning más videók.

## <a name="next-steps"></a>További lépések
* [Próbálja meg egy első adatok tudományos kísérletben a Machine Learning Studio](create-experiment.md)
* [A Microsoft Azure Machine Learning bemutatása beolvasása](what-is-machine-learning.md)
