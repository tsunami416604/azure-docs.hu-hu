---
title: 'Regresszió: Ár előrejelzése és algoritmusok összehasonlítása'
titleSuffix: Azure Machine Learning service
description: Ez a cikk bemutatja, hogyan hozhat létre egy összetett gépi tanulási kísérletet a vizuális felhasználói felületének használatával egyetlen kódsor megírása nélkül. Ismerje meg, hogyan betanítása és a egy autó árát, műszaki jellemzők alapján előre több regressziós modell összehasonlítására
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: aa0a1fc2acdc9687030040c23cdb1781e9529169
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605691"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>2 – regressziós. példa: Ár előrejelzése és algoritmusok összehasonlítása

Ismerje meg, hogyan hozhat létre egy összetett gépi tanulási kísérletet a vizuális felhasználói felületének használatával egyetlen kódsor megírása nélkül. Ez a példa betanítja és hasonlítja össze több regressziós modell előre jelezni egy autó árát annak műszaki jellemzők alapján. A közösségértékek biztosítunk a kiválasztott lehetőségeket ehhez a kísérlethez, így Ön is saját machine learning problémák kiszolgálókon.

Most csak ismerkedik a machine learninggel, ha akkor is vessen egy pillantást a [Alapverzió](ui-sample-regression-predict-automobile-price-basic.md) ezzel a kísérlettel egy alapszintű regressziós kísérlet megtekintéséhez.

A befejezett graph ehhez a kísérlethez itt látható:

[![A kísérlet diagram](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza ki a **nyílt** gomb a minta 2. kísérlet:

    ![Nyissa meg a kísérlet](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Kísérlet összegzése

Ezeket a lépéseket a kísérletet használjuk:

1. Az adatok lekérése.
1. Előzetesen feldolgozni az adatokat.
1. A modell betanításához.
1. Tesztelheti, kiértékelheti és a modell összehasonlítására.

## <a name="get-the-data"></a>Az adatok lekérése

Ehhez a kísérlethez használjuk a **Automobile price data (Raw)** adatkészletet, amely a UCI Machine Learning tárházból. Ez az adatkészlet 26 autók, beleértve a márkája, típusa, ár, jármű-funkciók (például a hengerszám), MPG, és egy biztosítási kockázati pontszám információt tartalmazó oszlopokat tartalmaz. Ez a kísérlet célja előrejelzésére egy autó árát.

## <a name="pre-process-the-data"></a>Az adatok előzetes feldolgozása

A fő adat-előkészítési tevékenységeket adattisztító, integráció, átalakítási, csökkentését, és a diszkretizálási vagy mennyiségmeghatározási tartalmazza. Vizuális felületen, a modul végre ezeket a műveleteket és más adatok előzetes feldolgozás feladatokat találja a **adatátalakítás** csoportot a bal oldali panelen.

Ehhez a kísérlethez használjuk a **Select Columns in Dataset** normalized-losses oszlop kizárása számos hiányzó értéket tartalmazó modult. Használjuk majd **Clean Missing Data** hiányzó értéket tartalmazó sorok eltávolításához. Ez segít egy tiszta betanítási adatkészletet létrehozása.

![Üzem előtti adatfeldolgozás](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>A modell betanítása

Machine learning problémák eltérőek lehetnek. Általános gépi tanulási feladatok közé tartozik a besorolás, fürtözés, regressziós és ajánló rendszerek, amelyek mindegyike egy teljesen más algoritmust is szükség lehet. A választott algoritmust gyakran a használati eset követelményeinek függ. Miután egy algoritmus kiválasztásához kell hangolása paramétereket egy pontosabb modell betanításához. Kell például a pontosságot, a képminőséget és a hatékonyság teljesítménymetrikák alapján minden modelleket szeretne értékelni.

Mivel ez a kísérlet célja az, hogy autó árának előrejelzése, és a egy regressziós modell akkor hasznos, mert a címke (ár) oszlopot tartalmaz valós számmá. Figyelembe véve, hogy a szolgáltatások száma viszonylag kicsi (kevesebb mint 100), és ezeket a funkciókat nem ritka, a döntési határ várhatóan nem lineáris.

A különféle algoritmusok teljesítményének összehasonlítására, a két nemlineáris algoritmusok, használjuk **súlyozott regressziós döntési fa** és **döntési erdő regressziós**, modelleket hozhat létre. Mindkét algoritmusok kell paramétereket, módosíthatja, de ehhez a kísérlethez használjuk az alapértelmezett értékeket.

Használjuk a **Split Data** modul véletlenszerűen osztani a bemeneti adatokat, így a betanítási adatkészletet tartalmaz az eredeti adatok 70 %-át és a tesztelési adatkészletet tartalmaz az eredeti adatok 30 %-át.

## <a name="test-evaluate-and-compare-the-models"></a>Tesztelheti, kiértékelheti és a modell összehasonlítására

Használjuk véletlenszerűen kiválasztott adatokat két különböző eljáráscsoport betanítására és tesztelésére a modell az előző szakaszban leírtak szerint. Hogy az adatkészlet fel különböző adatkészleteket taníthat vagy tesztelhet a modellt, hogy a modell értékelése több cél.

Miután a modell tanítása, használjuk a **Score Model** és **Evaluate Model** hozhat létre prediktív eredményeket, és a modell kiértékelése modul. **Pontszám modell** állít elő, a tesztelési adatkészletnél előrejelzések a betanított modell használatával. Azt, akkor továbbítja a pontszámokat **Evaluate Model** értékelési mérőszámok létrehozásához.

Az ehhez a kísérlethez két példányát használjuk **Evaluate Model** két párok modell összehasonlítására.

Először a betanítási adatkészletet a két algoritmus összehasonlítva.
A második összehasonlítva a tesztelési adatkészletet a két algoritmust.
Az eredmények a következők:

![Az eredmények összehasonlítása](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Ezekkel az eredményekkel megjelenítése, hogy a létrehozott modell **súlyozott regressziós döntési fa** rendelkezik egy alacsonyabb root mean squared hiba történt a modell épül, mint **döntési erdő regressziós**.

Mindkét algoritmusokat a betanítási adatkészletet a látott tesztelési adatkészleten, mint az alacsonyabb hiba van.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a vizuális felületen érhető el a más minták:

- [1 – regressziós. példa: Egy autó árát előrejelzése](ui-sample-regression-predict-automobile-price-basic.md)
- [Mintául szolgáló 3 - besorolás: Hitelkockázat előrejelzése](ui-sample-classification-predict-credit-risk-basic.md)
- [4 – besorolási. példa: Hitelkockázatot (költség-és nagybetűket)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5 – besorolási. példa: Forgalom előrejelzése](ui-sample-classification-predict-churn.md)
- [6 – besorolási. példa: A járatok késésének előrejelzése](ui-sample-classification-predict-flight-delay.md)
