---
title: 'Visual Interface példa #3: besorolás a hitelkockázat előrejelzéséhez'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre egy gépi tanulási osztályozó anélkül, hogy egyetlen sor kódot kellene írnia a vizualizáció felületének használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 4649303b8ee643130b8e254f01bfffbe8ad9eb2b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693499"
---
# <a name="sample-3---classification-predict-credit-risk"></a>3\. példa – besorolás: hitelkockázat előrejelzése

Megtudhatja, hogyan hozhat létre egy gépi tanulási osztályozó anélkül, hogy egyetlen sor kódot kellene írnia a vizualizáció felületének használatával. Ez a példa egy **kétosztályos, kibővített döntési fát** mutat be a hitelkockázat (magas vagy alacsony) alapján, például a hitelminősítés, az életkor és a hitelkártyák száma alapján.

Mivel a kérdés a következőt válaszolja: "melyik?" ezt besorolási problémanak nevezzük. Ugyanakkor ugyanezen alapvető eljárást alkalmazhatja bármilyen gépi tanulási probléma megoldásához, legyen szó a regresszióról, a besorolásról, a fürtözésről és így tovább.

A minta utolsó folyamat gráfja a következő:

![A folyamat gráfja](media/how-to-ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza a 3. minta folyamat **Megnyitás** gombját:

    ![A folyamat megnyitása](media/how-to-ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Kapcsolódó minta

[4. példa – besorolás: a hitelkockázat-előrejelzés (a Cost szenzitív)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md) olyan speciális folyamatot biztosít, amely a mintával megegyező problémát oldja meg. Ez azt mutatja be, hogyan végezhető el a *költséges* besorolás a **végrehajtási Python parancsfájl** -modul használatával és két bináris besorolási algoritmus teljesítményének összevetése. Ha szeretne többet megtudni a besorolási folyamatok létrehozásáról, tekintse meg a következőt:.


## <a name="data"></a>Adatok

A minta a németországi hitelkártya-adatkészletet használja az UC Irvine adattárból. 1 000 mintát tartalmaz 20 funkcióval és 1 címkével. Mindegyik minta egy személyt jelöl. A funkciók közé tartoznak a numerikus és a kategorikus funkciók. A kategorikus funkciók jelentését az [UCI webhelyén](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) tekintheti meg. Az utolsó oszlop a címkéje, amely a hitelkockázat kialakulását jelöli, és csak két lehetséges értékkel rendelkezik: magas hitelkockázati kockázat = 2, és alacsony hitelkockázat = 1.

## <a name="pipeline-summary"></a>Folyamat összegzése

A folyamat létrehozásához kövesse az alábbi lépéseket:

1. Húzza a németországi hitelkártya UCI adathalmaz modult a folyamat vászonra.
1. Vegyen fel egy **metaadatok szerkesztése** modult, hogy minden oszlophoz hozzá lehessen adni az értelmes neveket.
1. A képzési és tesztelési készletek létrehozásához vegyen fel egy **felosztott adat** -modult. Állítsa az első kimeneti adatkészletben lévő sorok töredékét 0,7-re. Ezzel a beállítással megadható, hogy az adatok 70%-a a modul bal oldali portjára, a többi pedig a megfelelő portra kerüljön. A bal oldali adatkészletet a képzéshez és a megfelelő teszteléshez használjuk.
1. Vegyen fel egy **kétosztályos, növelt döntési faszerkezetet** a megnövelt döntési fa besorolásának inicializálásához.
1. Vegyen fel egy **Train Model** -modult. Kapcsolja össze az osztályozó az előző lépésből a **vonat modell**bal oldali bemeneti portjába. Adja hozzá a betanítási készletet (a **felosztott adatok**bal oldali kimeneti portját) a **vonat modell**megfelelő bemeneti portjához. A **betanítási modell** az osztályozó tanítását fogja képezni.
1. Vegyen fel egy **pontszám-modell** modult, és kapcsolódjon hozzá a **Train Model** modulhoz. Ezután adja hozzá a tesztelési készletet (a **felosztott**adatmennyiség megfelelő portját) a **pontszám modellhez**. A **pontszám modell** az előrejelzéseket fogja végezni. Kiválaszthatja a kimeneti portját a jóslatok és a pozitív osztály valószínűségének megtekintéséhez.
1. Adja hozzá a **modell kiértékelése** modult, és kapcsolja össze a mutatós adatkészletet a bal oldali bemeneti portjával. A kiértékelés eredményeinek megtekintéséhez válassza ki a **modell kiértékelése** modul kimeneti portját, és válassza a **Megjelenítés**lehetőséget.

## <a name="results"></a>Eredmények

![Az eredmények kiértékelése](media/how-to-ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

A kiértékelés eredményeiben láthatja, hogy a modell AUC 0,776. A 0,5-as küszöbértéknél a pontosság 0,621, a visszahívás 0,456, az F1 pontszám pedig 0,526.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ismerje meg a vizuális felületen elérhető egyéb mintákat:

- [1. példa – regresszió: az autó árának előrejelzése](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [2. minta – regresszió: algoritmusok összehasonlítása az autó árának előrejelzéséhez](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [4. minta – besorolás: a hitelkockázat előrejelzése (a Cost szenzitív)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5. példa – besorolás: forgalom előrejelzése](how-to-ui-sample-classification-predict-churn.md)
- [6. példa – besorolás: repülési késések előrejelzése](how-to-ui-sample-classification-predict-flight-delay.md)
- [7. minta – szöveges besorolás: könyvek – felülvizsgálatok](how-to-ui-sample-text-classification.md)