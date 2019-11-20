---
title: 'Tervező: példa a forgalom előrejelzésére'
titleSuffix: Azure Machine Learning
description: Ezt a besorolási példát követve előre megjósolhatja a Azure Machine Learning Designer & megnövelt döntési fákat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 11/04/2019
ms.openlocfilehash: 5cf61d3446f960b65eb85538be9ea020671cced2
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196038"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>A megnövelt döntési fa használata a forgalom előrejelzésére Azure Machine Learning Designer használatával

**Designer (előzetes verzió) 5. minta**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Megtudhatja, hogyan hozhat létre egy összetett gépi tanulási folyamatot anélkül, hogy a Designer (előzetes verzió) használatával egyetlen sor kódot kellene írnia.

Ez a folyamat 2 **kétosztályos kibővített döntési** faminősítést biztosít az Ügyfélkapcsolat-kezelési (CRM) rendszerek általános feladatainak előrejelzéséhez – ügyfél-adatforgalom. Az adatértékek és a címkék több adatforráson oszlanak el, és névtelenség az ügyfelek adataival, azonban továbbra is használhatjuk a tervezőt az adathalmazok egyesítéséhez és a modell betanításához a rejtett értékek használatával.

Mivel megpróbál választ adni a "melyik?" kérdésre? Ez besorolási probléma, de az ebben a mintában látható logikát alkalmazhatja bármilyen gépi tanulási probléma megoldásához, legyen az a regresszió, a besorolás, a fürtözés és így tovább.

Itt látható a folyamathoz tartozó befejezett gráf:

![Folyamat gráf](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Kattintson az 5. minta lehetőségre a megnyitásához. 

## <a name="data"></a>Adatok

A folyamathoz tartozó adatok a KDD Cup 2009-től származnak. 50 000 sor és 230 szolgáltatás oszlopokat tartalmaz. A feladat az ezeket a szolgáltatásokat használó ügyfelek számára a kivonások, a szolgáltatóváltást és az értékesítések előrejelzése. Az adatokról és a feladatról a [KDD webhelyén](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)talál további információt.

## <a name="pipeline-summary"></a>Folyamat összegzése

A tervezőben ez a példában szereplő folyamat a forgalom, a szolgáltatóváltást és a fel-eladás bináris osztályozó előrejelzését mutatja be, amely az Ügyfélkapcsolat-kezelés (CRM) általános feladata.

Először is, néhány egyszerű adatfeldolgozást.

- A nyers adatkészlet sok hiányzó értékkel rendelkezik. Ha a hiányzó értékeket a 0 értékre szeretné cserélni, használja a **tiszta hiányzó** adatmodult.

    ![Az adatkészlet tisztítása](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- A funkciók és a megfelelő adatforgalom különböző adatkészletekben találhatók. Az **Oszlopok hozzáadása** modul használatával fűzze hozzá a címke oszlopokat a szolgáltatás oszlopaihoz. Az első oszlop, a **Col1**, a felirat oszlop. A vizualizáció eredményében láthatjuk, hogy az adatkészlet kiegyensúlyozatlan. Több negatív (-1) példa van, mint a pozitív példák (+ 1). A **arcul ütötte** modul használatával később is növelheti a kimutatott eseteket.

    ![Oszlop adatkészletének hozzáadása](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)



- Az adathalmaz felosztásához használja az **Adatfelosztási** modult a vonat-és tesztelési készletekben.

- Ezt követően a megnövelt döntési fa bináris osztályozó és az alapértelmezett paraméterek használatával hozza létre az előrejelzési modelleket. Feladathoz létrehozhat egy modellt, azaz egy modellt, amely az értékesítés, a szolgáltatóváltást és a forgalom előrejelzésére szolgál.

- A folyamat jobb oldalán a **arcul ütötte** modul használatával növeljük a pozitív példák százalékos arányát. A ARCUL ütötte százalékos értéke 100-re van állítva a pozitív példák megduplázása érdekében. További információ arról, hogyan működik a ARCUL ütötte modul a [arcul ütötte modul reference0](../././algorithm-module-reference/SMOTE.md).

## <a name="results"></a>Results (Eredmények)

Jelenítse meg a **modell kiértékelése** modul kimenetét a modell teljesítményének megjelenítéséhez a tesztelési készleten. 

![Az eredmények kiértékelése](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 Áthelyezheti a **küszöbérték** csúszkát, és megtekintheti a bináris besorolási feladat metrikáinak változását. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ismerje meg a tervező számára elérhető egyéb mintákat:

- [1. példa – regresszió: az autó árának előrejelzése](how-to-designer-sample-regression-automobile-price-basic.md)
- [2. minta – regresszió: algoritmusok összehasonlítása az autó árának előrejelzéséhez](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [3. minta – besorolás a szolgáltatás kiválasztásával: bevétel előrejelzése](how-to-designer-sample-classification-predict-income.md)
- [4. minta – besorolás: a hitelkockázat előrejelzése (a Cost szenzitív)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [6. példa – besorolás: repülési késések előrejelzése](how-to-designer-sample-classification-flight-delay.md)
- [7. minta – szöveges besorolás: wikipedia SP 500 adatkészlet](how-to-designer-sample-text-classification.md)
