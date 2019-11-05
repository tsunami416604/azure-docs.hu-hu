---
title: 'Tervezői példa #3: besorolás a hitelkockázat előrejelzéséhez'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre egy gépi tanulási osztályozó anélkül, hogy egyetlen sor kódot kellene írnia a Designer (előzetes verzió) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 2709c2e0da154a8530175144070ad560c8921b85
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515537"
---
# <a name="sample-3---classification-with-feature-selection-income-prediction"></a>3\. minta – besorolás a szolgáltatás kiválasztásával: bevétel előrejelzése
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Megtudhatja, hogyan hozhat létre egy gépi tanulási osztályozó anélkül, hogy egyetlen sor kódot kellene írnia a Designer (előzetes verzió) használatával. Ez a példa egy **kétosztályos, megnövelt döntési fát** mutat be a felnőtt népszámlálások jövedelmének előrejelzéséhez (> = 50k vagy < = 50k).

Mivel a kérdés a következőt válaszolja: "melyik?" ezt besorolási problémanak nevezzük. Ugyanakkor ugyanezen alapvető eljárást alkalmazhatja bármilyen gépi tanulási probléma megoldásához, legyen szó a regresszióról, a besorolásról, a fürtözésről és így tovább.

A minta utolsó folyamat gráfja a következő:

![A folyamat gráfja](media/how-to-ui-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Kattintson a 3. mintát a megnyitásához.



## <a name="data"></a>Adatok

Az adatkészlet 14 funkciót és egy felirat oszlopot tartalmaz. Többféle típusú funkció létezik, például a numerikus és a kategorikus. Az alábbi ábrán egy részlet látható az adatkészletből: ![adatok](media/how-to-ui-sample-classification-predict-income/data.png)



## <a name="pipeline-summary"></a>Folyamat összegzése

A folyamat létrehozásához kövesse az alábbi lépéseket:

1. Húzza a felnőtt népszámlálás-bevétel bináris adatkészletének modulját a folyamat vászonra.
1. A képzési és tesztelési készletek létrehozásához vegyen fel egy **felosztott adat** -modult. Állítsa az első kimeneti adatkészletben lévő sorok töredékét 0,7-re. Ezzel a beállítással megadható, hogy az adatok 70%-a a modul bal oldali portjára, a többi pedig a megfelelő portra kerüljön. A bal oldali adatkészletet a képzéshez és a megfelelő teszteléshez használjuk.
1. Adja hozzá a **Filter-alapú funkciók kiválasztási** modulját az 5 funkció PearsonCorreclation való kiválasztásához. 
1. Vegyen fel egy **kétosztályos, növelt döntési faszerkezetet** a megnövelt döntési fa besorolásának inicializálásához.
1. Vegyen fel egy **Train Model** -modult. Kapcsolja össze az osztályozó az előző lépésből a **vonat modell**bal oldali bemeneti portjába. Kapcsolja össze a szűrt adatkészletet a Filter-alapú szolgáltatás kiválasztási moduljából a betanítási adatkészletként.  A **betanítási modell** az osztályozó tanítását fogja képezni.
1. Az adatkészletek teszteléséhez adja hozzá az Oszlopok kiválasztása átalakítás és az átalakítási modul alkalmazása elemet, hogy ugyanazt az átalakítást (szűrt funkció kiválasztása) alkalmazza.
![alkalmazása – átalakítás](media/how-to-ui-sample-classification-predict-income/transformation.png)
1. Adja hozzá a **pontszám modell** modult, és kapcsolja össze a **Train Model** modult. Ezután adja hozzá a tesztelési készletet (az alkalmazás átalakítási moduljának kimenete, amely a beállítás kijelölését alkalmazza a set is értékre) a **pontszám modellbe**. A **pontszám modell** az előrejelzéseket fogja végezni. Kiválaszthatja a kimeneti portját a jóslatok és a pozitív osztály valószínűségének megtekintéséhez.


    Ennek a folyamatnak két pontszám-modulja van, a jobb oldalon az előrejelzés előtt ki van zárva a Label (címke) oszlop. Ez készen áll a valós idejű végpont üzembe helyezésére, mert a webszolgáltatás bemenete csak a nem címkével rendelkező funkciókat fogja várni. 

1. Adja hozzá a **modell kiértékelése** modult, és kapcsolja össze a mutatós adatkészletet a bal oldali bemeneti portjával. A kiértékelés eredményeinek megtekintéséhez válassza ki a **modell kiértékelése** modul kimeneti portját, és válassza a **Megjelenítés**lehetőséget.

## <a name="results"></a>Results (Eredmények)

![Az eredmények kiértékelése](media/how-to-ui-sample-classification-predict-income/evaluate-result.png)

A kiértékelés eredményeiben láthatja, hogy a görbék, például a ROC, a Precision-Recall és a zavartság mérőszámok. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a tervező számára elérhető egyéb mintákat:

- [1. példa – regresszió: az autó árának előrejelzése](how-to-designer-sample-regression-automobile-price-basic.md)
- [2. minta – regresszió: algoritmusok összehasonlítása az autó árának előrejelzéséhez](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [4. minta – besorolás: a hitelkockázat előrejelzése (a Cost szenzitív)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [5. példa – besorolás: forgalom előrejelzése](how-to-designer-sample-classification-churn.md)
- [6. példa – besorolás: repülési késések előrejelzése](how-to-designer-sample-classification-flight-delay.md)
- [7. minta – szöveges besorolás: wikipedia SP 500 adatkészlet](how-to-designer-sample-text-classification.md)
