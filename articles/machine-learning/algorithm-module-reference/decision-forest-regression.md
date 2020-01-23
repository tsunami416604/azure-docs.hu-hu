---
title: 'Döntési erdő regressziója: modul leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használható a döntési erdő regressziós modulja Azure Machine Learning egy regressziós modell létrehozásához a döntési fák együttese alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 2c1b61d43fde00c435b83071015246bf990e873e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546673"
---
# <a name="decision-forest-regression-module"></a>Döntési erdő regressziós modulja

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal egy regressziós modellt hozhat létre a döntési fák együttese alapján.

A modell konfigurálása után a modellt a címkével ellátott adatkészlet és a [vonat modell](./train-model.md) modul használatával kell betanítania. Ezt követően a betanított modell segítségével előrejelzéseket készíthet. 

## <a name="how-it-works"></a>Működési elv

A döntési fák olyan nem parametrikus modellek, amelyek az egyes példányok esetében egyszerű teszteket hajtanak végre, a bináris faszerkezetek adatstruktúráját, amíg el nem éri a levél csomópontot (döntés).

A döntési fák a következő előnyöket biztosítják:

- Hatékonyak mind a számítások, mind a memóriahasználat esetében a képzés és az előrejelzés során.

- Nem lineáris döntési határokat is jelenthetnek.

- Integrált funkciók kiválasztását és besorolását végzik, és rugalmasan működnek a zajos funkciók jelenlétében.

Ez a regressziós modell a döntési fák együttesét tartalmazza. A regressziós döntési erdőben minden fa a Gauss-eloszlást előrejelzésként jeleníti meg. A fák összevonása során a rendszer összesítést végez a modellben található összes fa összevont eloszlásához legközelebb eső Gauss-eloszlás megtalálásához.

Az algoritmus elméleti keretrendszerével és annak megvalósításával kapcsolatos további információkért tekintse meg a következő cikket: [döntési erdők: az osztályozás, a regresszió, a sűrűség becslése, a sokrétű tanulás és a részben felügyelt tanulás egységes keretrendszere](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Döntési erdő regressziós modelljének konfigurálása

1. Adja hozzá a **döntési erdő regressziós** modulját a folyamathoz. A modult a tervezőben a **Machine learning**, a **modell inicializálása**és a **regresszió**lehetőség alatt találja.

2. Nyissa meg a modul tulajdonságait, és az **újramintavételezési módszernél**válassza ki az egyes fák létrehozásához használt módszert.  A **csomagok** és a **replikálás**lehetőség közül választhat.

    - **Poggyász**: a poggyászt rendszerindítási *összesítésnek*is nevezik. A regressziós döntési erdőben található fák mindegyike egy Gauss-eloszlást ad vissza előrejelzés útján. Az Összesítés egy olyan Gauss megkeresése, amelynek első két pillanata megegyezik az egyes fák által visszaadott összes eloszlás kombinálásával a Gauss-eloszlások keverékének pillanataival.

         További információ: a rendszerindítási [összesítések](https://wikipedia.org/wiki/Bootstrap_aggregating)Wikipedia-bejegyzése.

    - **Replikálás**: a replikáció során minden fát pontosan ugyanazok a bemeneti adatok képeznek. Az egyes facsomópontok esetében a kiosztott predikátumok véletlenszerűek maradnak, és a fák sokrétűek lesznek.

         További információ a betanítási folyamatról a **replikálási** lehetőséggel kapcsolatban: [Computer Vision és az orvosi képek elemzését szolgáló döntési erdők. Criminisi és J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/).

3. Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.

    - **Egyetlen paraméter**

      Ha tudja, hogyan szeretné konfigurálni a modellt, megadhatja az értékek egy adott halmazát argumentumként. Lehetséges, hogy kísérletezéssel megismerte ezeket az értékeket, vagy útmutatásként kapta őket.



4. A **döntési fák száma mezőben**adja meg az Ensemble-ben létrehozandó döntési fák teljes számát. További döntési fák létrehozásával lehetőség van jobb lefedettségre, de a képzési idő növekedni fog.

    > [!TIP]
    > Ez az érték a betanított modell megjelenítésekor megjelenő fák számát is szabályozza. Ha egyetlen fát szeretne megtekinteni vagy kinyomtatni, az értéket 1-re állíthatja. azonban ez azt jelenti, hogy csak egy fa lesz létrehozva (a kezdeti paraméterekkel rendelkező fa), és további iterációk nem lesznek végrehajtva.

5. **A döntési fák maximális mélységéhez**adjon meg egy számot, amely korlátozza a döntési fa maximális mélységét. A fa mélységének növelésével növelheti a pontosságot, és megnövelheti a beilleszkedő és a megnövekedett betanítási időt is.

6. A **véletlenszerű felosztások száma a csomóponton**mezőben adja meg a fa egyes csomópontjainak kiépítésekor használandó felosztások számát. A *felosztás* azt jelenti, hogy a fa (csomópont) egyes szintjeinek funkciói véletlenszerűen vannak osztva.

7. A **minták minimális száma a levél csomópontjainál**adja meg az esetek minimális számát, amely szükséges ahhoz, hogy bármely terminál csomópont (levél) a fában legyen létrehozva.

     Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább öt olyan esetet kellene tartalmaznia, amelyek megfelelnek ugyanazoknak a feltételeknek.


9. Egy címkézett adatkészlet csatlakoztatása, válasszon ki egy egycímkés oszlopot, amely legfeljebb két eredményből áll, és csatlakozzon a [betanítási modellhez](./train-model.md).

    - Ha az **oktatói mód létrehozása** beállítást **egyetlen paraméterre**állítja be, a modell betanítása a [Train Model](./train-model.md) modul használatával végezhető el.

   

10. A folyamat futtatása.

### <a name="results"></a>Eredmények

A betanítás befejezése után:

+ A betanított modell pillanatképének mentéséhez válassza ki a betanítási modult, majd váltson a **kimenetek** lapra a jobb oldali panelen. Kattintson az ikonra a **modell regisztrálása**elemre.  A mentett modellt megtalálhatja modulként a modul fájában. 

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 