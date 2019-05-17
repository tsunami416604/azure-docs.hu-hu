---
title: 'Besorolás: Előrejelezheti a forgalom szolgáltatóváltást és felfelé értékesítése '
titleSuffix: Azure Machine Learning service
description: Ez a mintakísérlet vizuális felhasználói felületet jeleníti meg a bináris osztályozó előrejelzési adatforgalom, az Ügyfélkapcsolat-kezelés (CRM) gyakori feladat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 42724f5fcb3101015cef0d218a3d548f349646be
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785818"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>5 – besorolási. példa: Előrejelezheti a forgalom szolgáltatóváltást és felfelé értékesítése 

Ismerje meg, hogyan hozhat létre egy összetett gépi tanulási kísérletet a vizuális felhasználói felületének használatával egyetlen kódsor megírása nélkül.

Ezzel a kísérlettel betanítja három, **kétosztályos gyorsított döntési fa** előre jelezni az Ügyfélkapcsolat-kezelő (CRM) rendszerben kapcsolódó gyakori feladatok deklarációkkal: lemorzsolódási szolgáltatóváltást és felfelé értékesítése. Az adatok értékeit és címkék elosztja a több adatforrást és ügyfél-információkat anonimizálási betűtípus, azonban továbbra is használhatjuk a vizuális felhasználói felületet adatkészletek kombinálhatja, és a titkosított értékeket használó modell betanításához.

Mivel tudjuk visszaszerezni a választ a kérdésre "Melyiket?" Ezt hívják besorolás probléma. Azonban ez a kísérlet, amelynek célja a machine learning probléma bármilyen típusú, legyen szó regressziós, besorolás, fürtözés, és így tovább ugyanazokat a lépéseket alkalmazhatja.

A befejezett graph ehhez a kísérlethez itt látható:

![Kísérlet diagram](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza ki a **nyílt** a minta 5 kísérlet gombra.

    ![Nyissa meg a kísérlet](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Adatok

Az adatok, ehhez a kísérlethez használjuk a évi Versenysorozatából Cup 2009. Az adatkészlet 50 000 sort és oszlopot. a szolgáltatás 230. rendelkezik. A feladat, hogy előrejelzése lemorzsolódási szolgáltatóváltást és fel értékesítési ezeket a funkciókat használó ügyfelek számára. Az adatok és a feladat kapcsolatos további információkért lásd: a [évi Versenysorozatából webhely](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="experiment-summary"></a>Kísérlet összegzése

A vizuális felhasználói felületet mintakísérlet lemorzsolódási szolgáltatóváltást és felfelé – értékesítés, az Ügyfélkapcsolat-kezelés (CRM) gyakori feladat bináris osztályozó előrejelzését mutatja.

Első lépésként végzünk néhány egyszerű adatfeldolgozás.

- A nyers adatkészletet tartalmaz sok érték hiányzik. Használjuk a **Clean Missing Data** modul cserélje le a hiányzó értékek 0-val.

    ![Az Adatkészlet törlése](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- Az funkciók és a megfelelő adatváltozás, szolgáltatóváltást, és fel értékesítési címkék különböző adatkészleteket. Használjuk a **oszlopok hozzáadása** modul a címke oszlopok hozzáfűzése a szolgáltatás oszlopokat. Az első oszlop **Col1**, a címke oszlop. A többi oszlop, **Var1**, **Var2**, és így tovább, a szolgáltatás oszlop.

    ![Az oszlop adatkészlet hozzáadása](./media/ui-sample-classification-predict-churn/added-column1.png)

- Használjuk a **Split Data** modul az adatkészlet felosztása tanítási és tesztelje a csoportok.

    Ezt követően segítségével a súlyozott döntési fa bináris osztályozó az alapértelmezett paraméterek a prediktív modellek létrehozása. Feladatonként, hogy egy modell előre jelezni az fel értékesítési szolgáltatóváltást és adatváltozás egyes egy modell készítünk.

## <a name="results"></a>Results (Eredmények)

Megjelenítheti a kimenetét a **Evaluate Model** modult, tekintse meg a modell teljesítményét a vizsgálat beállítása. Az fel értékesítési feladat ROC-görbe mutatja be, hogy a modell jobban, mint egy véletlenszerű modell does. A görbe (AUC) alatti terület 0,857. 0.5-ös küszöbértékkel a pontosság 0,7, a visszaírási 0.463, pedig a F1 pontszám 0.545.

![Eredmények értékelése](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Áthelyezheti a **küszöbérték** csúszka, és tekintse meg a mérőszámok módosításához a bináris osztályozási feladat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a vizuális felületen érhető el a más minták:

- [1 – regressziós. példa: Egy autó árát előrejelzése](ui-sample-regression-predict-automobile-price-basic.md)
- [2 – regressziós. példa: Hasonlítsa össze az autó árának előrejelzése algoritmusok](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Mintául szolgáló 3 - besorolás: Hitelkockázat előrejelzése](ui-sample-classification-predict-credit-risk-basic.md)
- [4 – besorolási. példa: Hitelkockázatot (költség-és nagybetűket)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
