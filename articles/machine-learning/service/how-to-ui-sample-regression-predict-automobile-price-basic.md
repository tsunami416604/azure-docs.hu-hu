---
title: 'A Visual Interface példa #1: regresszió az ár előrejelzéséhez'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre gépi tanulási modellt az autó árának előrejelzéséhez egyetlen sor kód írása nélkül.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: a91f1446d8aab3db36499a9b5707d48d387b6081
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131557"
---
# <a name="sample-1---regression-predict-price"></a>1\. példa – regresszió: Ár előrejelzése

Megtudhatja, hogyan hozhat létre egy gépi tanulási regressziós modellt anélkül, hogy egyetlen sor kódot kellene írnia a vizualizáció felületének használatával.

Ez a kísérlet egy **döntési erdő regressor** , amely az autó díjszabását a gyártmány, a modell, a lóerő és a méret technikai jellemzői alapján Jósolja meg. Mivel a "mennyit?" kérdésre próbál válaszolni? ezt regressziós problémának nevezzük. Ebben a példában azonban ugyanazok az alapvető lépések alkalmazhatók, hogy bármilyen típusú gépi tanulási probléma feloldható legyen a regresszió, a besorolás, a fürtözés és így tovább.

A képzési gépi tanulási modell alapvető lépései a következők:

1. Az adatok lekérése
1. Az adatfeldolgozás előkezelése
1. A modell betanítása
1. A modell értékelése

Itt látható a kísérlet utolsó, befejezett gráfja. Biztosítjuk az összes modul indoklását, így a hasonló döntéseket saját maga is megteheti.

![A kísérlet gráfja](media/how-to-ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza az 1. minta kísérlet **Megnyitás** gombját:

    ![A kísérlet megnyitása](media/how-to-ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Az adatok lekérése

Ez a példa az **Automobile Price (nyers)** adatkészletet használja, amely az UCI Machine learning adattárból származik. Az adatkészlet 26 oszlopot tartalmaz, amely az autókkal kapcsolatos információkat tartalmaz, beleértve a make, a Model, a Price, a jármű funkcióit (például a hengerek számát), az MPG és a biztosítási kockázati pontszámot. A kísérlet célja az autó árának előrejelzése.

## <a name="pre-process-the-data"></a>Az adatfeldolgozás előkezelése

A fő adat-előkészítési feladatok közé tartozik az adatok tisztítása, az integráció, az átalakítás, a csökkentés és a diszkretizálási vagy a kvantálás. A vizualizációs felületen a bal oldali panelen található **Adatátalakítási** csoportban található modulok segítségével elvégezheti ezeket a műveleteket, valamint az egyéb adatfeldolgozás előtti feladatokat.

A sok hiányzó értékkel rendelkező normalizált veszteségek kizárásához használja az **adatkészlethez tartozó oszlopok kiválasztása** modult. Ezután a hiányzó **adatok** törlésével távolítsa el a hiányzó értékeket tartalmazó sorokat. Ez segít a betanítási adathalmazok tiszta készletének létrehozásában.

![Adatfeldolgozás előtti](./media/how-to-ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>A modell betanítása

A gépi tanulási problémák változhatnak. Az általános gépi tanulási feladatok közé tartoznak a besorolási, fürtözési, regressziós és ajánlott rendszerek, amelyek mindegyike más algoritmust igényelhet. Az Ön által választott algoritmus gyakran a használati eset követelményeitől függ. Az algoritmus kiválasztása után a paramétereket a pontosabb modell betanításához kell hangolnia. Ezután ki kell értékelnie az összes modellt, például a pontosságot, az érthetőséget és a hatékonyságot mutató mérőszámok alapján.

Mivel a kísérlet célja az autó árának előrejelzése, és mivel a Label (ár) oszlop valós számokat tartalmaz, a regressziós modell jó választás. Figyelembe véve, hogy a szolgáltatások száma viszonylag kicsi (kevesebb, mint 100), és ezek a funkciók nem ritkák, a döntési határ valószínűleg nem lineáris. Ezért a kísérlethez a **döntési erdő regresszióját** használjuk.

Az **adatok felosztása** modul használatával véletlenszerűen oszthatja meg a bemeneti adatokat, így a betanítási adatkészlet az eredeti adatok 70%-át, a tesztelési adatkészlet pedig az eredeti adatok 30%-át tartalmazza.

## <a name="test-evaluate-and-compare"></a>Tesztelés, kiértékelés és összehasonlítás

Ossza fel az adatkészletet, és használjon különböző adatkészleteket a modell betanításához és teszteléséhez, hogy a modell kiértékelése még nagyobb legyen.

A modell betanítása után használhatja a **pontszám modellt** , és **kiértékelheti a modell** moduljait az előre jelzett eredmények létrehozásához és a modellek kiértékeléséhez.

A **score Model** a betanított modell használatával generál előrejelzéseket a teszt adatkészlethez. Az eredmény ellenőrzéséhez válassza ki a **pontszám modell** kimeneti portját, majd válassza a **Megjelenítés**lehetőséget.

![Pontszám eredménye](./media/how-to-ui-sample-regression-predict-automobile-price-basic/score-result.png)

Értékelje ki a pontszámokat a **modell kiértékelése** modulban értékelési mérőszámok létrehozásához. Az eredmény ellenőrzéséhez válassza ki a **kiértékelési modell** kimeneti portját, majd válassza a **Megjelenítés**lehetőséget.

![Eredmény kiértékelése](./media/how-to-ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a vizuális felületen elérhető egyéb mintákat:

- [2. minta – regresszió: Algoritmusok összehasonlítása az autó árának előrejelzéséhez](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [3. példa – besorolás: Hitelkockázat előrejelzése](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [4. minta – besorolás: Hitelkockázat (Cost szenzitív)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5. példa – besorolás: Forgalom előrejelzése](how-to-ui-sample-classification-predict-churn.md)
- [6. példa – besorolás: Repülési késések előrejelzése](how-to-ui-sample-classification-predict-flight-delay.md)
