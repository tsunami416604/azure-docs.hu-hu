---
title: 'Besorolás: Hitelkockázat előrejelzése'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan hozhat létre a machine learning-osztályozó a vizuális felhasználói felületének használatával egyetlen kódsor megírása nélkül.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 6ec91ca83d7aa1bc5e6c290d35b573a60cc0ed19
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605773"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Mintául szolgáló 3 - besorolás: Hitelkockázat előrejelzése

Ismerje meg, hogyan hozhat létre a machine learning-osztályozó a vizuális felhasználói felületének használatával egyetlen kódsor megírása nélkül. Ez a minta betanítja a **kétosztályos gyorsított döntési fa** előre jelezni a kredit (nagyon vagy kevésbé gyakori) kockázati kredit alkalmazással kapcsolatos adatok, például a kreditelőzmények életkor és a hitelkártya száma alapján.

Mivel tudjuk visszaszerezni a választ a kérdésre "Melyiket?" Ezt hívják besorolás probléma. Az ugyanazon alapvető folyamat, amelynek célja a machine learning probléma bármilyen típusú, legyen szó regressziós, besorolás, fürtözés, és így tovább is alkalmazhat.

A befejezett graph ehhez a kísérlethez itt látható:

![A kísérlet diagram](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza ki a **nyílt** gomb a mintául szolgáló 3. kísérlet:

    ![Nyissa meg a kísérlet](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Kapcsolódó minta

[4 – besorolási. példa: Hitelkockázat-becslés (költség-és nagybetűket)](ui-sample-classification-predict-credit-risk-cost-sensitive.md) speciális kísérletet, amelyek ezzel a kísérlettel azonos problémával megoldja biztosít. Azt mutatja, hogyan hajthat végre _költség-és nagybetűket_ besorolási használatával egy **Python-szkript végrehajtására** modul, és hasonlítsa össze a két bináris osztályozási algoritmusok teljesítményének. Tekintse meg, ha többet szeretne megtudni a besorolási kísérletek létrehozását.

## <a name="data"></a>Data

A Németországi hitelkártya adatkészlet a UC Irvine adattárból használjuk.
Az adatkészlet 20 funkciók és 1 címkével 1000 mintákat tartalmazza. Minden minta egy személyt jelöl. Szolgáltatásai közé tartozik a numerikus és a kategorikus funkciókat. Tekintse meg a [UCI webhely](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) kategorikus funkcióinak jelentését. Az utolsó oszlop, a címke, amely azt jelzi, hogy a hitelkockázat kiszámításához, és csak két lehetséges értékek: magas hitelkockázat = 2, és alacsony hitelkockázat = 1.

## <a name="experiment-summary"></a>Kísérlet összegzése

Hogy kövesse az alábbi lépéseket a megoldás létrehozásához:

1. A Németországi UCI adatok adatkészlet modul húzza a kísérletvászonra.
1. Adjon hozzá egy **metaadatainak szerkesztése** modult, hogy az egyes oszlopok adjon kifejező nevet.
1. Adjon hozzá egy **Split Data** modul a tanítási és tesztelési készletek létrehozásához. Az első kimeneti adatkészletnél a 0,7 azoknak a soroknak az óradíj arányos részét állítja be. A beállítással megadható, hogy a kimenet a modul bal oldali portjával és a többi pedig a megfelelő portot 70 %-a az adatok lesz. A bal oldali adatkészletet használunk tanítási és tesztelési a megfelelőt.
1. Adjon hozzá egy **két osztályú súlyozott döntési fa** modul inicializálása egy gyorsított döntési fa osztályozó által igénybe vett.
1. Adjon hozzá egy **tanítási modell** modul. Az előző lépésben az osztályozó által igénybe vett bal oldali bemeneti portjával csatlakozni a **tanítási modell**. Adja hozzá a gyakorlókészlethez (a bal oldali kimeneti portját a **Split Data**) jobb oldali bemeneti portjával a **tanítási modell**. A **Train Model** lesz az osztályozó által igénybe vett betanításához.
1. Adjon hozzá egy **Score Model** modul, és csatlakozzon a **tanítási modell** modul rá. Majd adja hozzá a teszt csoportot (a jobb oldali portját a **Split Data**), a **Score Model**. A **Score Model** fog előrejelzéseket. A kimeneti portra, az előrejelzéseket és a pozitív osztály valószínűségek választhat.
1. Adjon hozzá egy **Evaluate Model** modul és a bal oldali bemeneti portját a pontozott adatkészlet csatlakozik. A kiértékelés eredményeinek megtekintéséhez válassza ki a kimeneti portjára, a **Evaluate Model** modul, és válassza ki **Visualize**.

Itt látható a teljes kísérlet diagram:

![A kísérlet diagram](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>Results (Eredmények)

![Eredmények értékelése](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

A kiértékelési eredmények között láthatja, hogy a modell a AUC 0.776. 0\.5-ös küszöbértékkel a pontosság 0.621, a visszaírási 0,456, pedig a F1 pontszám 0.526.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a vizuális felületen érhető el a más minták:

- [1 – regressziós. példa: Egy autó árát előrejelzése](ui-sample-regression-predict-automobile-price-basic.md)
- [2 – regressziós. példa: Hasonlítsa össze az autó árának előrejelzése algoritmusok](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [4 – besorolási. példa: Hitelkockázatot (költség-és nagybetűket)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5 – besorolási. példa: Forgalom előrejelzése](ui-sample-classification-predict-churn.md)
- [6 – besorolási. példa: A járatok késésének előrejelzése](ui-sample-classification-predict-flight-delay.md)