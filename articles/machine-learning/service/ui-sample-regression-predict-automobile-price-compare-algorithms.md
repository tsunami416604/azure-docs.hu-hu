---
title: 'Regresszió: Ár előrejelzése és algoritmusok összehasonlítása'
titleSuffix: Azure Machine Learning service
description: A vizuális felhasználói felületet mintakísérlet mutatja be két előre jelezni egy autó árát regressziós modellek teljesítményének összehasonlítását. A folyamat magában foglalja a képzés, a tesztelés és a Automobile price data (Raw) adatkészlet a modell kiértékelése.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 9748e9706e1140f200458ba77f8b2fc424be3b14
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029100"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>2 – regressziós. példa: Ár előrejelzése és algoritmusok összehasonlítása

A vizuális felhasználói felületet mintakísérlet mutatja be két előre jelezni egy autó árát regressziós modellek teljesítményének összehasonlítását. A folyamat magában foglalja a képzés, a tesztelés és a modell értékelése használatával a **Automobile price data (Raw)** adatkészlet.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza ki a **nyílt** gomb a minta 2. kísérlet:

    ![Nyissa meg a kísérlet](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="related-sample"></a>Kapcsolódó minta

[1 – regressziós. példa: Autó árának előrejelzése (alapszintű)](ui-sample-regression-predict-automobile-price-basic.md) biztosít egy egyszerűbb kísérletet, amely ehhez a kísérlethez azonos problémával megoldja, de csak egy regressziós modellt használja. Tekintse meg, ha alapszintű mintái regressziós keres.

## <a name="experiment-summary"></a>Kísérlet összegzése

Ezeket a lépéseket a kísérletet használjuk:

1. Az adatok lekérése.
1. Előzetesen feldolgozni az adatokat.
1. A modell betanításához.
1. Tesztelheti, kiértékelheti és a modell összehasonlítására.

Itt látható a teljes grafikon a kísérlet:

[ ![A kísérlet diagram](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png#lightbox)


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
