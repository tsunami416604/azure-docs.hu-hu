---
title: 'Visual Interface példa #3: regresszió az árakhoz és az algoritmusok összehasonlítása'
titleSuffix: Azure Machine Learning
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy összetett gépi tanulási folyamatot anélkül, hogy egyetlen sort kellene írnia a vizualizáció felületének használatával. Ismerje meg, hogyan lehet több regressziós modellt betanítani és összehasonlítani, hogy az autó árát a technikai funkciók alapján Jósolja meg
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 651644eaae910792aac2144531d09afc4cde7153
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692792"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>2\. minta – regresszió: előrejelzési ár és összehasonlítási algoritmusok

Megtudhatja, hogyan hozhat létre egy összetett gépi tanulási folyamatot anélkül, hogy egyetlen sor kódot kellene írnia a vizualizáció felületének használatával. Ez a minta a több regressziós modellt is összehasonlítja, hogy az autó árát a technikai jellemzői alapján Jósolja meg. Biztosítjuk az ebben a folyamatban meghozott döntések indoklását, így kezelheti a gépi tanulási problémák megoldását.

Ha most ismerkedik a gépi tanulással, tekintse meg a folyamat [alapszintű verzióját](how-to-ui-sample-regression-predict-automobile-price-basic.md) .

Itt látható a folyamathoz tartozó befejezett gráf:

[a folyamat ![Graph](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza a minta 2 folyamat **Megnyitás** gombját:

    ![A folyamat megnyitása](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="pipeline-summary"></a>Folyamat összegzése

A Machine learning-folyamat létrehozásához kövesse az alábbi lépéseket:

1. Szerezze be az adatgyűjtést.
1. Az adatfeldolgozás előtt.
1. A modell betanítása.
1. Tesztelje, értékelje ki és hasonlítsa össze a modelleket.

## <a name="get-the-data"></a>Az adatok lekérése

Ez a példa az **Automobile Price (nyers)** adatkészletet használja, amely az UCI Machine learning adattárból származik. Ez az adatkészlet 26 oszlopot tartalmaz, amelyek az autókkal kapcsolatos információkat tartalmaznak, beleértve a make, a Model, a Price, a jármű funkcióit (például a hengerek számát), az MPG és a biztosítási kockázati pontszámot.

## <a name="pre-process-the-data"></a>Az adatfeldolgozás előkezelése

A fő adat-előkészítési feladatok közé tartozik az adatok tisztítása, az integráció, az átalakítás, a csökkentés és a diszkretizálási vagy a kvantálás. A Visual Interface-ben a bal oldali panel **Adatátalakítási** csoportjában található modulok segítségével elvégezheti ezeket a műveleteket és egyéb, az adatok előfeldolgozását végző feladatokat.

A sok hiányzó értékkel rendelkező normalizált veszteségek kizárásához használja az **adatkészlethez tartozó oszlopok kiválasztása** modult. Ezután tiszta, **hiányzó adatok** használatával távolítsa el a hiányzó értékeket tartalmazó sorokat. Ez segít a betanítási adathalmazok tiszta készletének létrehozásában.

![Adatfeldolgozás előtti](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>A modell tanítása

A gépi tanulási problémák változhatnak. Az általános gépi tanulási feladatok közé tartoznak a besorolási, fürtözési, regressziós és ajánlott rendszerek, amelyek mindegyike más algoritmust igényelhet. Az Ön által választott algoritmus gyakran a használati eset követelményeitől függ. Az algoritmus kiválasztása után a paramétereket a pontosabb modell betanításához kell hangolnia. Ezután ki kell értékelnie az összes modellt, például a pontosságot, az érthetőséget és a hatékonyságot mutató mérőszámok alapján.

Mivel ennek a folyamatnak a célja az autó árának előrejelzése, és mivel a Label (ár) oszlop valós számokat tartalmaz, a regressziós modell jó választás. Figyelembe véve, hogy a szolgáltatások száma viszonylag kicsi (kevesebb, mint 100), és ezek a funkciók nem ritkák, a döntési határ valószínűleg nem lineáris.

A különböző algoritmusok teljesítményének összehasonlításához két nemlineáris algoritmust használunk, a **döntési fa regresszióját** és a **döntési erdő regresszióját**a modellek létrehozásához. Mindkét algoritmushoz meg lehet változtatni a paramétereket, de ez a minta a folyamat alapértelmezett értékeit használja.

Az **adatok felosztása** modul használatával véletlenszerűen oszthatja meg a bemeneti adatokat, így a betanítási adatkészlet az eredeti adatok 70%-át, a tesztelési adatkészlet pedig az eredeti adatok 30%-át tartalmazza.

## <a name="test-evaluate-and-compare-the-models"></a>Modellek tesztelése, kiértékelése és összehasonlítása

A véletlenszerűen kiválasztott adat két különböző készletét használja a betanításhoz, majd tesztelje a modellt az előző szakaszban leírtak szerint. Ossza fel az adatkészletet, és használjon különböző adatkészleteket a modell betanításához és teszteléséhez, hogy a modell kiértékelése még nagyobb legyen.

A modell betanítása után használja a **pontszám modellt** , és **értékelje** ki a modell moduljait az előre jelzett eredmények létrehozásához és a modellek kiértékeléséhez. A **score Model** a betanított modell használatával generál előrejelzéseket a teszt adatkészlethez. Ezután adja át a pontszámokat a modell kiértékeléséhez, hogy **kiértékelje** az értékelési metrikákat.

Ebben a folyamatban a **modell kiértékelésének** két példányát használja két pár modell összehasonlítására.

Először hasonlítsa össze a betanítási adatkészlet két algoritmusát.
Másodszor, hasonlítsa össze a tesztelési adatkészlet két algoritmusát.

Az eredmények a következők:

![Az eredmények összehasonlítása](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Ezek az eredmények azt mutatják, hogy a **megnövelt döntési fa regressziós** modellje alacsonyabb, legfelső szintű, négyzetes hibával rendelkezik, mint a **döntési erdő regressziós**modelljére épülő modell.

Mindkét algoritmus alacsonyabb hibával rendelkezik a betanítási adatkészletnél, mint a láthatatlan tesztelési adatkészleten.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ismerje meg a vizuális felületen elérhető egyéb mintákat:

- [1. példa – regresszió: az autó árának előrejelzése](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [3. példa – besorolás: hitelkockázat előrejelzése](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [4. minta – besorolás: a hitelkockázat előrejelzése (a Cost szenzitív)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5. példa – besorolás: forgalom előrejelzése](how-to-ui-sample-classification-predict-churn.md)
- [6. példa – besorolás: repülési késések előrejelzése](how-to-ui-sample-classification-predict-flight-delay.md)
- [7. minta – szöveges besorolás: könyvek – felülvizsgálatok](how-to-ui-sample-text-classification.md)
