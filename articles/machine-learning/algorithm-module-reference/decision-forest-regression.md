---
title: 'Döntési erdő regressziója: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használható a döntési erdő regressziós modulja Azure Machine Learning szolgáltatásban egy regressziós modell létrehozásához a döntési fák együttese alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b8bb3285aecb6aff399606e6263f014027a86581
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128886"
---
# <a name="decision-forest-regression-module"></a>Döntési erdő regressziós modulja

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal egy regressziós modellt hozhat létre a döntési fák együttese alapján.

A modell konfigurálása után a modellt a címkével ellátott adatkészlet és a [vonat modell](./train-model.md) modul használatával kell betanítania.  Ezt követően a betanított modell segítségével előrejelzéseket készíthet. 

## <a name="how-it-works"></a>Működés

A döntési fák olyan nem parametrikus modellek, amelyek az egyes példányok esetében egyszerű teszteket hajtanak végre, a bináris faszerkezetek adatstruktúráját, amíg el nem éri a levél csomópontot (döntés).

A döntési fák a következő előnyöket biztosítják:

- Hatékonyak mind a számítások, mind a memóriahasználat esetében a képzés és az előrejelzés során.

- Nem lineáris döntési határokat is jelenthetnek.

- Integrált funkciók kiválasztását és besorolását végzik, és rugalmasan működnek a zajos funkciók jelenlétében.

Ez a regressziós modell a döntési fák együttesét tartalmazza. A regressziós döntési erdőben minden fa a Gauss-eloszlást előrejelzésként jeleníti meg. A fák összevonása során a rendszer összesítést végez a modellben található összes fa összevont eloszlásához legközelebb eső Gauss-eloszlás megtalálásához.

Az algoritmus elméleti keretrendszerével és annak megvalósításával kapcsolatos további információkért tekintse meg ezt a cikket: [Döntési erdők: Egységes keretrendszer a besoroláshoz, a Regresszióhoz, a sűrűség értékeléséhez, a sokrétű tanuláshoz és a részben felügyelt tanuláshoz](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Döntési erdő regressziós modelljének konfigurálása

1. Adja hozzá a **döntési erdő regressziós** modulját a kísérlethez. A modult a **Machine learning**, a **modell inicializálása**és a **regresszió**alatt található felületen érheti el.

2. Nyissa meg a modul tulajdonságait,és az újramintavételezési módszernél válassza ki az egyes fák létrehozásához használt módszert.  A **csomagok** és a **replikálás**lehetőség közül választhat.

    - **Poggyász**: A poggyászt rendszerindítási *összesítésnek*is nevezik. A regressziós döntési erdőben található fák mindegyike egy Gauss-eloszlást ad vissza előrejelzés útján. Az Összesítés egy olyan Gauss megkeresése, amelynek első két pillanata megegyezik az egyes fák által visszaadott összes Gauss összevonásával.

         További információ: a rendszerindítási összesítések [](https://wikipedia.org/wiki/Bootstrap_aggregating)Wikipedia-bejegyzése.

    - **Replikálás**: A replikáció során minden fát pontosan ugyanazok a bemeneti adatok képeznek. Az egyes facsomópontok esetében a kiosztott predikátumok véletlenszerűek maradnak, és a fák sokrétűek lesznek.

         További információ a betanítási folyamatról a **replikálási** lehetőséggel kapcsolatban [: Computer Vision és az orvosi képek elemzését szolgáló döntési erdők. Criminisi és J. Shotton. Springer 2013. ](https://research.microsoft.com/projects/decisionforests/).

3. Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.

    - **Egyetlen paraméter**

      Ha tudja, hogyan szeretné konfigurálni a modellt, megadhatja az értékek egy adott halmazát argumentumként. Lehetséges, hogy kísérletezéssel megismerte ezeket az értékeket, vagy útmutatásként kapta őket.



4. A **döntési fák száma mezőben**adja meg az Ensemble-ben létrehozandó döntési fák teljes számát. További döntési fák létrehozásával lehetőség van jobb lefedettségre, de a képzési idő növekedni fog.

    > [!TIP]
    > Ez az érték a betanított modell megjelenítésekor megjelenő fák számát is szabályozza. Ha egyetlen fát szeretne megtekinteni vagy kinyomtatni, az értéket 1-re állíthatja. azonban ez azt jelenti, hogy csak egy fa lesz létrehozva (a kezdeti paraméterekkel rendelkező fa), és további iterációk nem lesznek végrehajtva.

5. **A döntési fák maximális mélységéhez**adjon meg egy számot, amely korlátozza a döntési fa maximális mélységét. A fa mélységének növelésével növelheti a pontosságot, és megnövelheti a beilleszkedő és a megnövekedett betanítási időt is.

6. A **véletlenszerű felosztások száma**a csomóponton mezőben adja meg a fa egyes csomópontjainak kiépítésekor használandó felosztások számát. A *felosztás* azt jelenti, hogy a fa (csomópont) egyes szintjeinek funkciói véletlenszerűen vannak osztva.

7. A **minták minimális**száma a levél csomópontjainál adja meg az esetek minimális számát, amely szükséges ahhoz, hogy bármely terminál csomópont (levél) a fában legyen létrehozva.

     Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább öt olyan esetet kellene tartalmaznia, amelyek megfelelnek ugyanazoknak a feltételeknek.


9. Egy címkézett adatkészlet csatlakoztatása, válasszon ki egy egycímkés oszlopot, amely legfeljebb két eredményből áll, és [](./train-model.md)csatlakozzon a betanítási modellhez.

    - Ha az **oktatói mód létrehozása** beállítást **egyetlen paraméterre**állítja be, a modell betanítása a [Train Model](./train-model.md) modul használatával végezhető el.

   

10. Futtassa a kísérletet.

### <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ Ha meg szeretné tekinteni az egyes iterációkban létrehozott fát, kattintson a jobb gombbal a betanítási modul kimenetére, majd válassza a **Megjelenítés**lehetőséget.

+ Ha meg szeretné tekinteni az egyes csomópontok szabályait, kattintson az egyes faszerkezetekre, és bontsa ki a felosztást.

+ A betanított modell pillanatképének mentéséhez kattintson a jobb gombbal az oktatási modul kimenetére, és válassza a **Mentés**betanítva modellként lehetőséget. A modell ezen példánya nem frissül a kísérlet egymást követő futtatásakor. 

## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 