---
title: 'Visual Interface példa #5: Besorolás az elváltozások előrejelzéséhez + szolgáltatóváltást + értékesítés'
titleSuffix: Azure Machine Learning
description: Ez a Visual Interface-példa a változás bináris osztályozó előrejelzését jeleníti meg, amely az Ügyfélkapcsolat-kezelés (CRM) általános feladata.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 260d94ddf2572979e819ee89dfcbd315ef3c4769
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131934"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>5\. példa – besorolás: A forgalom előrejelzése, a szolgáltatóváltást és az értékesítés 

Megtudhatja, hogyan hozhat létre egy összetett gépi tanulási kísérletet anélkül, hogy egyetlen sor kódot kellene írnia a vizualizáció felületének használatával.

Ez a kísérlet három, **kétosztályos kibővített döntési faosztályozási struktúrát** biztosít az Ügyfélkapcsolat-kezelési (CRM) rendszerek általános feladatainak előrejelzéséhez: adatforgalom, szolgáltatóváltást és értékesítés. Az adatértékek és a címkék több adatforráson oszlanak el, és a névtelenség az ügyfelek adataival vannak összekeverve, de továbbra is használhatjuk a vizualizációs felületet az adatkészletek összevonásához és a modell betanításához a kódolt értékek használatával.

Mivel megpróbál választ adni a "melyik?" kérdésre? Ez a besorolási probléma, de ugyanezt a logikát alkalmazhatja ebben a projektben bármilyen gépi tanulási probléma megoldásához, legyen az a regresszió, a besorolás, a fürtözés és így tovább.

Itt látható a kísérlethez tartozó befejezett gráf:

![Kísérleti gráf](./media/how-to-ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza a **Megnyitás** gombot a minta 5 kísérlethez.

    ![A kísérlet megnyitása](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

A kísérlethez tartozó adatok a 2009-es KDD Cup-ról származnak. 50 000 sor és 230 szolgáltatás oszlopokat tartalmaz. A feladat az ezeket a szolgáltatásokat használó ügyfelek számára a kivonások, a szolgáltatóváltást és az értékesítések előrejelzése. Az adatokról és a feladatról a [KDD webhelyén](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)talál további információt.

## <a name="experiment-summary"></a>Kísérletezés összegzése

Ez a Visual Interface-minta-kísérlet a forgalom, a szolgáltatóváltást és a fel-eladás bináris osztályozó előrejelzését mutatja be, amely az Ügyfélkapcsolat-kezelés (CRM) általános feladata.

Először végezze el az egyszerű adatfeldolgozást.

- A nyers adatkészlet sok hiányzó értéket tartalmaz. Ha a hiányzó értékeket a 0 értékre szeretné cserélni, használja a **tiszta hiányzó** adatmodult.

    ![Az adatkészlet tisztítása](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- A funkciók és a megfelelő adatforgalom, szolgáltatóváltást és fel-eladási címkék különböző adatkészletekben találhatók. Az **Oszlopok hozzáadása** modul használatával fűzze hozzá a címke oszlopokat a szolgáltatás oszlopaihoz. Az első oszlop, a **Col1**, a felirat oszlop. A többi oszlop, a **Var1**, a **Var2**és más oszlopok a funkció oszlopai.

    ![Oszlop adatkészletének hozzáadása](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- Az adathalmaz felosztásához használja az **Adatfelosztási** modult a vonat-és tesztelési készletekben.

    Ezt követően a megnövelt döntési fa bináris osztályozó és az alapértelmezett paraméterek használatával hozza létre az előrejelzési modelleket. Feladathoz létrehozhat egy modellt, azaz egy modellt, amely az értékesítés, a szolgáltatóváltást és a forgalom előrejelzésére szolgál.

## <a name="results"></a>Results (Eredmények)

Jelenítse meg a **modell kiértékelése** modul kimenetét a modell teljesítményének megjelenítéséhez a tesztelési készleten. Az eladási feladatokhoz a ROC-görbe azt mutatja, hogy a modell jobb, mint egy véletlenszerű modell. A görbe alatti terület (AUC) 0,857. A 0,5-as küszöbértéknél a pontosság 0,7, a visszahívás 0,463, az F1 pontszám pedig 0,545.

![Az eredmények kiértékelése](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 Áthelyezheti a **küszöbérték** csúszkát, és megtekintheti a bináris besorolási feladat metrikáinak változását.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a vizuális felületen elérhető egyéb mintákat:

- [1. példa – regresszió: Autó árának előrejelzése](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [2. minta – regresszió: Algoritmusok összehasonlítása az autó árának előrejelzéséhez](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [3. példa – besorolás: Hitelkockázat előrejelzése](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [4. minta – besorolás: Hitelkockázat (Cost szenzitív)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [6. példa – besorolás: Repülési késések előrejelzése](how-to-ui-sample-classification-predict-flight-delay.md)
