---
title: Besorolási Forgalom előrejelzése + szolgáltatóváltást + értékesítés
titleSuffix: Azure Machine Learning service
description: Ez a Visual Interface-példa a változás bináris osztályozó előrejelzését jeleníti meg, amely az Ügyfélkapcsolat-kezelés (CRM) általános feladata.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: a0b3e3de604e7c59ea6a06bd59007964c98e0057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845990"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>5\. példa – besorolás: A forgalom előrejelzése, a szolgáltatóváltást és az értékesítés 

Megtudhatja, hogyan hozhat létre egy összetett gépi tanulási kísérletet anélkül, hogy egyetlen sor kódot kellene írnia a vizualizáció felületének használatával.

Ez a kísérlet három, kétosztályos kibővített döntési faosztályozási struktúrát biztosít az Ügyfélkapcsolat-kezelési (CRM) rendszerek általános feladatainak előrejelzéséhez: adatforgalom, szolgáltatóváltást és értékesítés. Az adatértékek és a címkék több adatforráson oszlanak el, és a névtelenség az ügyfelek adataival vannak összekeverve, de továbbra is használhatjuk a vizualizációs felületet az adatkészletek összevonásához és a modell betanításához a kódolt értékek használatával.

Mivel megpróbáljuk választ adni a "melyik?" kérdésre? ezt besorolási problémanak nevezzük. Ebben a kísérletben azonban ugyanezeket a lépéseket alkalmazhatja a gépi tanulási problémák bármilyen típusának kezelésére, akár a regresszió, a besorolás, a fürtözés és így tovább.

Itt látható a kísérlethez tartozó befejezett gráf:

![Kísérleti gráf](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza a **Megnyitás** gombot a minta 5 kísérlethez.

    ![A kísérlet megnyitása](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

A kísérlethez használt adatok a KDD Cup 2009-ból származnak. Az adatkészlet 50 000 sor és 230 szolgáltatás oszlopot tartalmaz. A feladat az ezeket a szolgáltatásokat használó ügyfelek számára a kivonások, a szolgáltatóváltást és az értékesítések előrejelzése. Az adatokról és a feladatról a [KDD webhelyén](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)talál további információt.

## <a name="experiment-summary"></a>Kísérletezés összegzése

Ez a Visual Interface-minta-kísérlet a forgalom, a szolgáltatóváltást és a fel-eladás bináris osztályozó előrejelzését mutatja be, amely az Ügyfélkapcsolat-kezelés (CRM) általános feladata.

Először is végezzünk el néhány egyszerű adatfeldolgozást.

- A nyers adatkészlet sok hiányzó értéket tartalmaz. A **tiszta hiányzó adatok** modult használjuk a hiányzó értékek 0 értékre való lecseréléséhez.

    ![Az adatkészlet tisztítása](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- A funkciók és a megfelelő adatforgalom, szolgáltatóváltást és fel-eladási címkék különböző adatkészletekben találhatók. Az **Oszlopok hozzáadása** modul használatával fűzze hozzá a címke oszlopokat a szolgáltatás oszlopaihoz. Az első oszlop, a **Col1**, a felirat oszlop. A többi oszlop, a **Var1**, a **Var2**és más oszlopok a funkció oszlopai.

    ![Oszlop adatkészletének hozzáadása](./media/ui-sample-classification-predict-churn/added-column1.png)

- Az adathalmazt a kiosztási és a tesztelési csoportokra bontva használjuk az adatfelosztási modul használatával.

    Ezután a megnövelt döntési fa bináris besorolást használjuk az alapértelmezett paraméterekkel az előrejelzési modellek létrehozásához. Feladathoz egy modellt hozunk létre, azaz egy modellt, amely az értékesítés, a szolgáltatóváltást és a forgalom előrejelzésére szolgál.

## <a name="results"></a>Results (Eredmények)

Jelenítse meg a **modell** kiértékelése modul kimenetét a modell teljesítményének megjelenítéséhez a tesztelési készleten. Az eladási feladatokhoz a ROC-görbe azt mutatja, hogy a modell jobb, mint egy véletlenszerű modell. A görbe alatti terület (AUC) 0,857. A 0,5-as küszöbértéknél a pontosság 0,7, a visszahívás 0,463, az F1 pontszám pedig 0,545.

![Az eredmények kiértékelése](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Áthelyezheti a **küszöbérték** csúszkát, és megtekintheti a bináris besorolási feladat metrikáinak változását.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a vizuális felületen elérhető egyéb mintákat:

- [1. példa – regresszió: Autó árának előrejelzése](ui-sample-regression-predict-automobile-price-basic.md)
- [2. minta – regresszió: Algoritmusok összehasonlítása az autó árának előrejelzéséhez](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [3. példa – besorolás: Hitelkockázat előrejelzése](ui-sample-classification-predict-credit-risk-basic.md)
- [4. minta – besorolás: Hitelkockázat (Cost szenzitív)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [6. példa – besorolás: Repülési késések előrejelzése](ui-sample-classification-predict-flight-delay.md)