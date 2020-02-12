---
title: 'Tervező: az autós árak előrejelzése (alapszintű) példa'
titleSuffix: Azure Machine Learning
description: Hozzon létre egy ML regressziós modellt az autó árának előrejelzéséhez anélkül, hogy egyetlen sor kódot kellene írnia Azure Machine Learning Designer használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 02/11/2020
ms.openlocfilehash: 58adbc7607b0b32e79123b701c37f55ce7cc1d2e
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138129"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>A regresszió használata az autók árának előrejelzéséhez Azure Machine Learning Designer használatával

**Designer (előzetes verzió) 1. minta**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Megtudhatja, hogyan hozhat létre egy gépi tanulási regressziós modellt anélkül, hogy egyetlen sor kódot kellene írnia a tervező (előzetes verzió) használatával.

Ez a folyamat **lineáris regressor** biztosít az autó árának előrejelzésére olyan technikai funkciók alapján, mint például a gyártmány, a modell, a lóerő és a méret. Mivel a "mennyit?" kérdésre próbál válaszolni? ezt regressziós problémának nevezzük. Ebben a példában azonban ugyanazok az alapvető lépések alkalmazhatók, hogy bármilyen típusú gépi tanulási probléma feloldható legyen a regresszió, a besorolás, a fürtözés és így tovább.

A képzési gépi tanulási modell alapvető lépései a következők:

1. Az adatok lekérése
1. Az adatfeldolgozás előkezelése
1. A modell betanítása
1. A modell kiértékelése

Itt látható a folyamat utolsó, befejezett gráfja. Ez a cikk az összes modul indoklását tartalmazza, így a hasonló döntéseket saját maga is megteheti.

![A folyamat gráfja](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Kattintson az 1. mintára a 。 megnyitásához


## <a name="get-the-data"></a>Az adatok lekérése

Ez a példa az **Automobile Price (nyers)** adatkészletet használja, amely az UCI Machine learning adattárból származik. Az adatkészlet 26 oszlopot tartalmaz, amely az autókkal kapcsolatos információkat tartalmaz, beleértve a make, a Model, a Price, a jármű funkcióit (például a hengerek számát), az MPG és a biztosítási kockázati pontszámot. A minta célja az autó árának előrejelzése.

## <a name="pre-process-the-data"></a>Az adatfeldolgozás előkezelése

A fő adat-előkészítési feladatok közé tartozik az adatok tisztítása, az integráció, az átalakítás, a csökkentés és a diszkretizálási vagy a kvantálás. A tervezőben megtalálhatja azokat a modulokat, amelyek végrehajtják ezeket a műveleteket, valamint az egyéb adat-előkészítő feladatokat a bal oldali panelen található **Adatátalakítási** csoportban.

A sok hiányzó értékkel rendelkező normalizált veszteségek kizárásához használja az **adatkészlethez tartozó oszlopok kiválasztása** modult. Ezután a hiányzó **adatok** törlésével távolítsa el a hiányzó értékeket tartalmazó sorokat. Ez segít a betanítási adathalmazok tiszta készletének létrehozásában.

![Adatfeldolgozás előtti](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>A modell betanítása

A gépi tanulási problémák változhatnak. Az általános gépi tanulási feladatok közé tartoznak a besorolási, fürtözési, regressziós és ajánlott rendszerek, amelyek mindegyike más algoritmust igényelhet. Az Ön által választott algoritmus gyakran a használati eset követelményeitől függ. Az algoritmus kiválasztása után a paramétereket a pontosabb modell betanításához kell hangolnia. Ezután ki kell értékelnie az összes modellt, például a pontosságot, az érthetőséget és a hatékonyságot mutató mérőszámok alapján.

Mivel a minta célja, hogy előre megjósolja az autó árát, és mivel a Label (ár) oszlop folyamatos adat, a regressziós modell jó választás lehet. Ehhez a folyamathoz **lineáris regressziót** használunk.

Az **adatok felosztása** modul használatával véletlenszerűen oszthatja meg a bemeneti adatokat, így a betanítási adatkészlet az eredeti adatok 70%-át, a tesztelési adatkészlet pedig az eredeti adatok 30%-át tartalmazza.

## <a name="test-evaluate-and-compare"></a>Tesztelés, kiértékelés és összehasonlítás

Ossza fel az adatkészletet, és használjon különböző adatkészleteket a modell betanításához és teszteléséhez, hogy a modell kiértékelése még nagyobb legyen.

A modell betanítása után használhatja a **pontszám modellt** , és **kiértékelheti a modell** moduljait az előre jelzett eredmények létrehozásához és a modellek kiértékeléséhez.

A **score Model** a betanított modell használatával generál előrejelzéseket a teszt adatkészlethez. Az eredmény ellenőrzéséhez válassza ki a **pontszám modell** kimeneti portját, majd válassza a **Megjelenítés**lehetőséget.

![Pontszám eredménye](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

Értékelje ki a pontszámokat a **modell kiértékelése** modulban értékelési mérőszámok létrehozásához. Az eredmény ellenőrzéséhez válassza ki a **kiértékelési modell** kimeneti portját, majd válassza a **Megjelenítés**lehetőséget.

![Eredmény kiértékelése](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ismerje meg a tervező számára elérhető egyéb mintákat:

- [2. minta – regresszió: algoritmusok összehasonlítása az autó árának előrejelzéséhez](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [3. minta – besorolás a szolgáltatás kiválasztásával: bevétel előrejelzése](how-to-designer-sample-classification-predict-income.md)
- [4. minta – besorolás: a hitelkockázat előrejelzése (a Cost szenzitív)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [5. példa – besorolás: forgalom előrejelzése](how-to-designer-sample-classification-churn.md)
- [6. példa – besorolás: repülési késések előrejelzése](how-to-designer-sample-classification-flight-delay.md)
- [7. minta – szöveges besorolás: wikipedia SP 500 adatkészlet](how-to-designer-sample-text-classification.md)