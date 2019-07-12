---
title: 'Regresszió: Ár előrejelzése'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan hozhat létre egy gépi tanulási modellt az autó árát előrejelzésére egy egyetlen sor kód írása nélkül.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: ddc7ae10581075127f72dd020c59cf28bbfc9ae2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606137"
---
# <a name="sample-1---regression-predict-price"></a>1 – regressziós. példa: Ár előrejelzése

Ismerje meg, hogyan hozhat létre egy gépi tanulási regressziós modellt a vizuális felhasználói felületének használatával egyetlen kódsor megírása nélkül.

Ez vonatok kísérletezhet egy **döntési erdő regressor erre** előrejelzésére egy autó az ár műszaki szolgáltatások, például márkája, típusa, lóerő és méretét. Mivel tudjuk "Mennyi?" kérdésre próbál Ezt nevezzük regressziós probléma. Azonban ez a kísérlet, amelynek célja a machine learning probléma bármilyen típusú, legyen szó regressziós, besorolás, fürtözés, és így tovább az alapvető lépéseket alkalmazhatja.

Az egy képzési gépi tanulási modellt az alapvető lépések a következők:

1. Az adatok lekérése
1. Az adatok előzetes feldolgozása
1. A modell betanítása
1. A modell értékelése

A végső, befejezett grafikon a kísérlet azt fogjuk projekten dolgozik, az itt látható. Biztosítunk a közösségértékek modulhoz tartozó összes, így is döntéseket hasonló saját maga.

![A kísérlet diagram](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza ki a **nyílt** gomb a minta 1 kísérlet:

    ![Nyissa meg a kísérlet](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Az adatok lekérése

Ehhez a kísérlethez használjuk a **Automobile price data (Raw)** adatkészletet, amely a UCI Machine Learning tárházból. Az adatkészlet 26 autók, beleértve a márkája, típusa, ár, jármű-funkciók (például a hengerszám), MPG, és egy biztosítási kockázati pontszám információt tartalmazó oszlopokat tartalmaz. Az e kísérlet célja, hogy az autó árának előrejelzése.

## <a name="pre-process-the-data"></a>Az adatok előzetes feldolgozása

A fő adat-előkészítési tevékenységeket adattisztító, integráció, átalakítási, csökkentését, és a diszkretizálási vagy mennyiségmeghatározási tartalmazza. A vizuális felületen található modulok ezeket a műveleteket és más adatok előzetes feldolgozás feladatokat végez a **adatátalakítás** csoportot a bal oldali panelen.

Használjuk a **Select Columns in Dataset** normalized-losses oszlop kizárása számos hiányzó értéket tartalmazó modult. Használjuk majd **Clean Missing Data** hiányzó értéket tartalmazó sorok eltávolításához. Ez segít egy tiszta betanítási adatkészletet létrehozása.

![Üzem előtti adatfeldolgozás](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>A modell betanítása

Machine learning problémák eltérőek lehetnek. Általános gépi tanulási feladatok közé tartozik a besorolás, fürtözés, regressziós és ajánló rendszerek, amelyek mindegyike egy teljesen más algoritmust is szükség lehet. A választott algoritmust gyakran a használati eset követelményeinek függ. Miután egy algoritmus kiválasztásához kell hangolása paramétereket egy pontosabb modell betanításához. Kell például a pontosságot, a képminőséget és a hatékonyság teljesítménymetrikák alapján minden modelleket szeretne értékelni.

Mivel ez a kísérlet célja az, hogy autó árának előrejelzése, és a egy regressziós modell akkor hasznos, mert a címke (ár) oszlopot tartalmaz valós számmá. Figyelembe véve, hogy a szolgáltatások száma viszonylag kicsi (kevesebb mint 100), és ezeket a funkciókat nem ritka, a döntési határ várhatóan nem lineáris. Ezért használjuk a **döntési erdő regressziós** ehhez a kísérlethez.

Használjuk a **Split Data** modul véletlenszerűen osztani a bemeneti adatokat, így a betanítási adatkészletet tartalmaz az eredeti adatok 70 %-át és a tesztelési adatkészletet tartalmaz az eredeti adatok 30 %-át.

## <a name="test-evaluate-and-compare"></a>Tesztelheti, kiértékelheti és összehasonlítása

 Hogy az adatkészlet fel különböző adatkészleteket taníthat vagy tesztelhet a modellt, hogy a modell értékelése több cél.

Miután a modell tanítása, használjuk a **Score Model** és **Evaluate Model** hozhat létre prediktív eredményeket, és a modell kiértékelése modul.

**Pontszám modell** állít elő, a tesztelési adatkészletnél előrejelzések a betanított modell használatával. Ellenőrizze az eredményt, válassza ki a kimeneti portjára, **Score Model** majd **Visualize**.

![Pontszám eredménye](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Azt, akkor továbbítja a pontszámokat az **Evaluate Model** modul értékelési mérőszámok létrehozásához. Ellenőrizze az eredményt, válassza ki a kimeneti portjára, a **Evaluate Model** majd **Visualize**.

![Eredmények kiértékelése](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a vizuális felületen érhető el a más minták:

- [2 – regressziós. példa: Hasonlítsa össze az autó árának előrejelzése algoritmusok](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Mintául szolgáló 3 - besorolás: Hitelkockázat előrejelzése](ui-sample-classification-predict-credit-risk-basic.md)
- [4 – besorolási. példa: Hitelkockázatot (költség-és nagybetűket)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5 – besorolási. példa: Forgalom előrejelzése](ui-sample-classification-predict-churn.md)
- [6 – besorolási. példa: A járatok késésének előrejelzése](ui-sample-classification-predict-flight-delay.md)