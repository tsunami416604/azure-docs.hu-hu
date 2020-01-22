---
title: 'Tervező: osztályozás, prediktív jövedelem – példa'
titleSuffix: Azure Machine Learning
description: Ezt a példát követve hozzon létre egy kód nélküli besorolást a bevétel előrejelzéséhez Azure Machine Learning Designer használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: bfae0d8eed80a88475c447a141097022fed9adff
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311134"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>Osztályozó & felépítése a szolgáltatás kiválasztásával előre jelezheti a bevételt Azure Machine Learning Designer használatával

**3. Designer minta**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Megtudhatja, hogyan hozhat létre egy gépi tanulási osztályozó anélkül, hogy egyetlen sor kódot írna a Designer használatával. Ez a példa egy **kétosztályos, megnövelt döntési fát** mutat be a felnőtt népszámlálások jövedelmének előrejelzéséhez (> = 50k vagy < = 50k).

Mivel a kérdés a következőt válaszolja: "melyik?" ezt besorolási problémanak nevezzük. Ugyanakkor ugyanezen alapvető eljárást alkalmazhatja bármilyen gépi tanulási probléma – regresszió, besorolás, fürtözés stb. – kezelésére.

A minta utolsó folyamat gráfja a következő:

![A folyamat gráfja](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Kattintson a 3. mintát a megnyitásához.



## <a name="data"></a>Adatok

Az adatkészlet 14 funkciót és egy felirat oszlopot tartalmaz. Többféle típusú funkció létezik, például a numerikus és a kategorikus. Az alábbi ábrán egy részlet látható az adatkészletből: ![adatok](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>Folyamat összegzése

A folyamat létrehozásához kövesse az alábbi lépéseket:

1. Húzza a felnőtt népszámlálás-bevétel bináris adatkészletének modulját a folyamat vászonra.
1. A képzési és tesztelési készletek létrehozásához vegyen fel egy **felosztott adat** -modult. Állítsa az első kimeneti adatkészletben lévő sorok töredékét 0,7-re. Ezzel a beállítással megadható, hogy az adatok 70%-a a modul bal oldali portjára, a többi pedig a megfelelő portra kerüljön. A bal oldali adatkészletet a képzéshez és a megfelelő teszteléshez használjuk.
1. Adja hozzá a **Filter-alapú funkciók kiválasztási** modulját az 5 funkció PearsonCorreclation való kiválasztásához. 
1. Vegyen fel egy **kétosztályos, növelt döntési faszerkezetet** a megnövelt döntési fa besorolásának inicializálásához.
1. Vegyen fel egy **Train Model** -modult. Kapcsolja össze az osztályozó az előző lépésből a **vonat modell**bal oldali bemeneti portjába. Kapcsolja össze a szűrt adatkészletet a Filter-alapú szolgáltatás kiválasztási moduljából a betanítási adatkészletként.  A **betanítási modell** az osztályozó tanítását fogja képezni.
1. Az adatkészletek teszteléséhez adja hozzá az Oszlopok kiválasztása átalakítás és az átalakítási modul alkalmazása elemet, hogy ugyanazt az átalakítást (szűrt funkció kiválasztása) alkalmazza.
![alkalmazása – átalakítás](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. Adja hozzá a **pontszám modell** modult, és kapcsolja össze a **Train Model** modult. Ezután adja hozzá a tesztelési készletet (az alkalmazás átalakítási moduljának kimenete, amely a beállítás kijelölését alkalmazza a set is értékre) a **pontszám modellbe**. A **pontszám modell** az előrejelzéseket fogja végezni. Kiválaszthatja a kimeneti portját a jóslatok és a pozitív osztály valószínűségének megtekintéséhez.


    Ennek a folyamatnak két pontszám-modulja van, a jobb oldalon az előrejelzés előtt ki van zárva a Label (címke) oszlop. Ez készen áll a valós idejű végpont üzembe helyezésére, mert a webszolgáltatás bemenete csak a nem címkével rendelkező funkciókat fogja várni. 

1. Adja hozzá a **modell kiértékelése** modult, és kapcsolja össze a mutatós adatkészletet a bal oldali bemeneti portjával. A kiértékelés eredményeinek megtekintéséhez válassza ki a **modell kiértékelése** modul kimeneti portját, és válassza a **Megjelenítés**lehetőséget.

## <a name="results"></a>Eredmények

![Az eredmények kiértékelése](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

A kiértékelés eredményeiben láthatja, hogy a görbék, például a ROC, a Precision-Recall és a zavartság mérőszámok. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ismerje meg a tervező számára elérhető egyéb mintákat:

- [1. példa – regresszió: az autó árának előrejelzése](how-to-designer-sample-regression-automobile-price-basic.md)
- [2. minta – regresszió: algoritmusok összehasonlítása az autó árának előrejelzéséhez](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [4. minta – besorolás: a hitelkockázat előrejelzése (a Cost szenzitív)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [5. példa – besorolás: forgalom előrejelzése](how-to-designer-sample-classification-churn.md)
- [6. példa – besorolás: repülési késések előrejelzése](how-to-designer-sample-classification-flight-delay.md)
- [7. minta – szöveges besorolás: wikipedia SP 500 adatkészlet](how-to-designer-sample-text-classification.md)
