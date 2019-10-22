---
title: 'Vizuális kezelőfelület – példa #5: besorolás az elváltozások előrejelzéséhez + szolgáltatóváltást + értékesítés'
titleSuffix: Azure Machine Learning
description: Ez a vizuális felületi mintavételezési folyamat a forgalom bináris osztályozó előrejelzését jeleníti meg, amely az Ügyfélkapcsolat-kezelés (CRM) általános feladata.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 82639779dde08bb1f71fb75dba62038dbf34d1b6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693553"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>5\. példa – besorolás: forgalom előrejelzése, szolgáltatóváltást és értékesítés 

Megtudhatja, hogyan hozhat létre egy összetett gépi tanulási folyamatot anélkül, hogy egyetlen sor kódot kellene írnia a vizualizáció felületének használatával.

Ez a folyamat három, **kétosztályos, kibővített döntési fa besorolási struktúrát** biztosít az Ügyfélkapcsolat-kezelési (CRM) rendszerek általános feladatainak előrejelzéséhez: adatforgalom, szolgáltatóváltást és értékesítés. Az adatértékek és a címkék több adatforráson oszlanak el, és a névtelenség az ügyfelek adataihoz is használhatók, de továbbra is használhatjuk a vizualizációs felületet az adathalmazok egyesítéséhez és a modell betanításához a rejtett értékek használatával.

Mivel megpróbál választ adni a "melyik?" kérdésre? Ez besorolási probléma, de az ebben a mintában látható logikát alkalmazhatja bármilyen gépi tanulási probléma megoldásához, legyen az a regresszió, a besorolás, a fürtözés és így tovább.

Itt látható a folyamathoz tartozó befejezett gráf:

![Folyamat gráf](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza ki a minta 5 folyamat **Megnyitás** gombját.

    ![A folyamat megnyitása](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Adatok

A folyamathoz tartozó adatok a KDD Cup 2009-től származnak. 50 000 sor és 230 szolgáltatás oszlopokat tartalmaz. A feladat az ezeket a szolgáltatásokat használó ügyfelek számára a kivonások, a szolgáltatóváltást és az értékesítések előrejelzése. Az adatokról és a feladatról a [KDD webhelyén](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)talál további információt.

## <a name="pipeline-summary"></a>Folyamat összegzése

Ez a vizuális felületi mintavételezési folyamat a forgalom, a szolgáltatóváltást és az értékesítések bináris osztályozó előrejelzését, az Ügyfélkapcsolat-kezelés (CRM) általános feladatát mutatja be.

Először is, néhány egyszerű adatfeldolgozást.

- A nyers adatkészlet sok hiányzó értékkel rendelkezik. Ha a hiányzó értékeket a 0 értékre szeretné cserélni, használja a **tiszta hiányzó** adatmodult.

    ![Az adatkészlet tisztítása](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- A funkciók és a megfelelő adatforgalom, szolgáltatóváltást és fel-eladási címkék különböző adatkészletekben találhatók. Az **Oszlopok hozzáadása** modul használatával fűzze hozzá a címke oszlopokat a szolgáltatás oszlopaihoz. Az első oszlop, a **Col1**, a felirat oszlop. A többi oszlop, a **Var1**, a **Var2**és más oszlopok a funkció oszlopai.

    ![Oszlop adatkészletének hozzáadása](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- Az adathalmaz felosztásához használja az **Adatfelosztási** modult a vonat-és tesztelési készletekben.

- Ezt követően a megnövelt döntési fa bináris osztályozó és az alapértelmezett paraméterek használatával hozza létre az előrejelzési modelleket. Feladathoz létrehozhat egy modellt, azaz egy modellt, amely az értékesítés, a szolgáltatóváltást és a forgalom előrejelzésére szolgál.

## <a name="results"></a>Eredmények

Jelenítse meg a **modell kiértékelése** modul kimenetét a modell teljesítményének megjelenítéséhez a tesztelési készleten. Az eladási feladatokhoz a ROC-görbe azt mutatja, hogy a modell jobb, mint egy véletlenszerű modell. A görbe alatti terület (AUC) 0,857. A 0,5-as küszöbértéknél a pontosság 0,7, a visszahívás 0,463, az F1 pontszám pedig 0,545.

![Az eredmények kiértékelése](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 Áthelyezheti a **küszöbérték** csúszkát, és megtekintheti a bináris besorolási feladat metrikáinak változását.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ismerje meg a vizuális felületen elérhető egyéb mintákat:

- [1. példa – regresszió: az autó árának előrejelzése](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [2. minta – regresszió: algoritmusok összehasonlítása az autó árának előrejelzéséhez](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [3. példa – besorolás: hitelkockázat előrejelzése](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [4. minta – besorolás: a hitelkockázat előrejelzése (a Cost szenzitív)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [6. példa – besorolás: repülési késések előrejelzése](how-to-ui-sample-classification-predict-flight-delay.md)
- [7. minta – szöveges besorolás: könyvek – felülvizsgálatok](how-to-ui-sample-text-classification.md)
