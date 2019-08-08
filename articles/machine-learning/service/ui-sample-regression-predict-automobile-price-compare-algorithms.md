---
title: Regressziós Előrejelzési ár és összehasonlítási algoritmusok
titleSuffix: Azure Machine Learning service
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy összetett gépi tanulási kísérletet anélkül, hogy egyetlen sor kódot kellene írnia a vizualizáció felületének használatával. Ismerje meg, hogyan lehet több regressziós modellt betanítani és összehasonlítani, hogy az autó árát a technikai funkciók alapján Jósolja meg
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 28af7b814a8d214c3529ecb12ffe25ede78b1cb6
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855941"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>2\. minta – regresszió: Előrejelzési ár és összehasonlítási algoritmusok

Megtudhatja, hogyan hozhat létre egy összetett gépi tanulási kísérletet anélkül, hogy egyetlen sor kódot kellene írnia a vizualizáció felületének használatával. Ez a minta a több regressziós modellt is összehasonlítja, hogy az autó árát a technikai jellemzői alapján Jósolja meg. Biztosítjuk az ebben a kísérletben meghozott döntések indoklását, hogy kezelni tudja a gépi tanulási problémákat.

Ha most ismerkedik a gépi tanulással, tekintse meg a kísérlet alapszintű [verzióját](ui-sample-regression-predict-automobile-price-basic.md) , és tekintse meg az alapszintű regressziós kísérletet.

Itt látható a kísérlethez tartozó befejezett gráf:

[![A kísérlet gráfja](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza a minta 2 kísérlet **Megnyitás** gombját:

    ![A kísérlet megnyitása](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Kísérletezés összegzése

A kísérletet a következő lépésekkel hozhatja létre:

1. Szerezze be az adatgyűjtést.
1. Az adatfeldolgozás előtt.
1. A modell betanítása.
1. Tesztelje, értékelje ki és hasonlítsa össze a modelleket.

## <a name="get-the-data"></a>Az adatok lekérése

Ebben a kísérletben az UCI Machine Learning adattárból származó **Automobile Price (nyers)** adatkészletet használjuk. Ez az adatkészlet 26 oszlopot tartalmaz, amelyek az autókkal kapcsolatos információkat tartalmaznak, beleértve a make, a Model, a Price, a jármű funkcióit (például a hengerek számát), az MPG és a biztosítási kockázati pontszámot. A kísérlet célja egy autó árának előrejelzése.

## <a name="pre-process-the-data"></a>Az adatfeldolgozás előkezelése

A fő adat-előkészítési feladatok közé tartozik az adatok tisztítása, az integráció, az átalakítás, a csökkentés és a diszkretizálási vagy a kvantálás. A Visual Interface-ben a bal oldali panel Adatátalakítási csoportjában található modulok segítségével elvégezheti ezeket a műveleteket és egyéb, az adatok előfeldolgozását végző feladatokat.

Ebben a kísérletben az adathalmaz-modul **Select oszlopait** használjuk a sok hiányzó értékkel rendelkező normalizált veszteségek kizárásához. Ezután tiszta, **hiányzó adatok** használatával távolítsa el a hiányzó értékeket tartalmazó sorokat. Ez segít a betanítási adathalmazok tiszta készletének létrehozásában.

![Adatfeldolgozás előtti](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>A modell betanítása

A gépi tanulási problémák változhatnak. Az általános gépi tanulási feladatok közé tartoznak a besorolási, fürtözési, regressziós és ajánlott rendszerek, amelyek mindegyike más algoritmust igényelhet. Az Ön által választott algoritmus gyakran a használati eset követelményeitől függ. Az algoritmus kiválasztása után a paramétereket a pontosabb modell betanításához kell hangolnia. Ezután ki kell értékelnie az összes modellt, például a pontosságot, az érthetőséget és a hatékonyságot mutató mérőszámok alapján.

Mivel a kísérlet célja az autó árának előrejelzése, és mivel a Label (ár) oszlop valós számokat tartalmaz, a regressziós modell jó választás. Figyelembe véve, hogy a szolgáltatások száma viszonylag kicsi (kevesebb, mint 100), és ezek a funkciók nem ritkák, a döntési határ valószínűleg nem lineáris.

A különböző algoritmusok teljesítményének összehasonlításához két nemlineáris algoritmust használunk, a **döntési fa regresszióját** és a döntési **erdő regresszióját**a modellek létrehozásához. Mindkét algoritmushoz meg lehet változtatni a paramétereket, de a kísérlet alapértelmezett értékeit használjuk.

Az **adatok felosztása** modul használatával véletlenszerűen osztjuk meg a bemeneti adatokat, így a betanítási adatkészlet az eredeti adatok 70%-át, a tesztelési adatkészlet pedig az eredeti adatok 30%-át tartalmazza.

## <a name="test-evaluate-and-compare-the-models"></a>Modellek tesztelése, kiértékelése és összehasonlítása

A véletlenszerűen kiválasztott adat két különböző készletét használjuk a betanításhoz, majd tesztelni a modellt az előző szakaszban leírtak szerint. Feldaraboljuk az adatkészletet, és különböző adatkészleteket használunk a modell betanításához és teszteléséhez, hogy a modell kiértékelése nagyobb legyen.

A modell betanítása után a **pontszám modellt** használjuk, és kiértékeljük a **modell** moduljait az előre jelzett eredmények létrehozásához és a modellek kiértékeléséhez. A **score Model** a betanított modell használatával generál előrejelzéseket a teszt adatkészlethez. Ezután továbbítjuk a pontszámokat a **modell** kiértékeléséhez, hogy kiértékeljék az értékelési mérőszámokat.

Ebben a kísérletben két modellt használunk a **modell** kiértékelésére két pár modell összehasonlítására.

Először hasonlítjuk össze két algoritmust a betanítási adatkészleten.
Másodszor, két algoritmust hasonlítunk össze a tesztelési adatkészletből.
Az eredmények a következők:

![Az eredmények összehasonlítása](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Ezek az eredmények azt mutatják, hogy a **megnövelt döntési fa regressziós** modellje alacsonyabb, legfelső szintű, négyzetes hibával rendelkezik, mint a döntési **erdő regressziós**modelljére épülő modell.

Mindkét algoritmus alacsonyabb hibával rendelkezik a betanítási adatkészletnél, mint a láthatatlan tesztelési adatkészleten.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a vizuális felületen elérhető egyéb mintákat:

- [1. példa – regresszió: Autó árának előrejelzése](ui-sample-regression-predict-automobile-price-basic.md)
- [3. példa – besorolás: Hitelkockázat előrejelzése](ui-sample-classification-predict-credit-risk-basic.md)
- [4. minta – besorolás: Hitelkockázat (Cost szenzitív)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5. példa – besorolás: Forgalom előrejelzése](ui-sample-classification-predict-churn.md)
- [6. példa – besorolás: Repülési késések előrejelzése](ui-sample-classification-predict-flight-delay.md)
