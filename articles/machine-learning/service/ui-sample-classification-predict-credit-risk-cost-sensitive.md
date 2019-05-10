---
title: 'Besorolás: Hitelkockázatot (költség-és nagybetűket)'
titleSuffix: Azure Machine Learning service
description: A vizuális felhasználói felületet mintakísérlet bemutatja, hogyan hajtsa végre a költségérzékeny bináris osztályozási egy egyéni Python-szkript használatával. Hitelkockázat hitelkérelemben megadott információ alapján előrejelzi azt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 433c258f86705f66e0163100407be7996d68bc6b
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65440958"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>4 – besorolási. példa: Hitelkockázatot (költség-és nagybetűket)

A vizuális felhasználói felületet mintakísérlet bemutatja, hogyan hajtsa végre a költségérzékeny bináris osztályozási egy egyéni Python-szkript használatával. A pozitív példák misclassifying költsége ötször mennyibe kerül a misclassifying a negatív minták.

Ez a minta előrejelzi hitelkérelemben, figyelembe véve a téves besorolás költségek ismertetett információk alapján.

Ez a kísérlet a probléma megoldásához modellek létrehozásának két különböző megközelítést összehasonlítva:

- Képzés és az eredeti adathalmazból.
- Az adatkészlet replikált képzést.

Mindkét módszer azt a modellek használatával tesztelési adathalmazon replikációs győződjön meg arról, hogy eredmények összhangban legyenek a költségek függvény kiértékelése. Mindkét módszerénél a két osztályozó teszteljük: **Kétosztályos támogató Vektorgép** és **Kétosztályos gyorsított döntési fa**.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza ki a **nyílt** gomb a minta 4 kísérlet:

    ![Nyissa meg a kísérlet](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="related-sample"></a>Kapcsolódó minta

Lásd: [minta 3 - besorolás: Hitelkockázat-becslés (alapszintű)](ui-sample-classification-predict-churn.md) téves besorolás költségek nélkül beállítása, amely ehhez a kísérlethez azonos problémával megoldja alapszintű kísérlethez.

## <a name="data"></a>Adatok

A Németországi hitelkártya adatkészlet a UC Irvine adattárból használjuk. Ez az adatkészlet 20 funkciók és 1 címkével 1000 mintákat tartalmazza. Minden minta egy személyt jelöl. 20 szolgáltatásai közé tartozik a numerikus és a kategorikus funkciókat. Tekintse meg a [UCI webhely](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) az adatkészlet további információt. Az utolsó oszlop, a címke, amely azt jelzi, hogy a hitelkockázat kiszámításához, és csak két lehetséges értékek: magas hitelkockázat = 2, és alacsony hitelkockázat = 1.

## <a name="experiment-summary"></a>Kísérlet összegzése

Egy magas, alacsony kockázatú példa misclassifying költségének értéke 1, és egy, alacsony kockázatú példa misclassifying költsége 5. Használjuk egy **Python-szkript végrehajtására** fiókra a helytelen osztályozásra költsége a modult.

A grafikon a kísérlet a következő:

[![A kísérlet diagram](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Adatfeldolgozás

Módszertan használatával a **metaadat-szerkesztő** modul hozzáadása oszlopnevek el az alapértelmezett oszlopnevek kifejezőbb nevekkel, az adatkészlet leírása a UCI webhelyen szerzett. Az új oszlopnevek kínálunk, vesszővel tagolt értékek a **új oszlop** nevét tartalmazó mező a **metaadat-szerkesztő**.

Ezután azt létrehozni a képzés, és tesztelje a kockázati előrejelzési modell fejlesztéséhez használt. Osztottuk fel, az eredeti adathalmazból az azonos méretű betanítási és vizsgálati csoportokba használatával a **Split Data** modul. Egyenlő méretű csoportok létrehozásához, hogy állítsa a **az első kimeneti adatkészletnél a sorok** 0,5 lehetőséget.

### <a name="generate-the-new-dataset"></a>Az új adatkészlet létrehozása

Mivel a magas kockázati alábecslésének költségét, ehhez hasonló téves besorolás költsége beállított:

- Alacsony kockázatot misclassified magas kockázatú esetek: 5
- Magas kockázatot misclassified alacsony kockázatú esetek: 1.

Költség függvény megfelelően, hogy egy új adatkészlet hozzon létre. Az új adatkészletre a rendszer replikálja az egyes magas kockázatú példa ötször, de alacsony kockázatú példák száma nem változik. Hogy az adatok felosztása tanítási és tesztelési adatkészleteket, így megakadályozhatja a ugyanabban a sorban a mindkét replikáció előtt.

A magas kockázatú az adatok replikálása, tárgyaljuk, a Python-kód be egy **Python-szkript végrehajtására** modul:

```
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

A **Python-szkript végrehajtására** modul replikálja a képzés és a teszt adatkészletek.

### <a name="feature-engineering"></a>Jellemzőkiemelés

A **Kétosztályos támogató Vektorgép** algoritmus normalizált adatokra van szüksége. Ezért használjuk a **normalizálása adatok** modul optimalizálására a tartományokat az összes numerikus funkcióit egy `tanh` átalakításában. A `tanh` átalakítási összes numerikus szolgáltatás alakít számos 0 és 1 között szereplő értékek általános elosztásának megőrzése.

A **Kétosztályos támogató Vektorgép** modul karakterlánc funkciók, a konvertálása 0 vagy 1 értékű kategorikus szolgáltatása és majd a bináris kezeli. Így nem szükséges optimalizálására ezeket a funkciókat.

## <a name="models"></a>Modellek

Mivel két osztályozó alkalmazunk **Kétosztályos támogató Vektorgép** (SVM) és **két osztályú súlyozott döntési fa**, és két adatkészletet is használhatja, hogy összesen négy modell létrehozásához:

- Eredeti adatokhoz képzett SVM.
- Képzett SVM replikált adatokat.
- Gyorsított döntési fa eredeti adatokhoz képzett.
- Gyorsított döntési fa képzett replikált adatokat.

A standard szintű kísérleti munkafolyamat létrehozását, betanítását és a modell teszteléséhez használjuk:

1. A tanulási algoritmusokat használ inicializálása **Kétosztályos támogató Vektorgép** és **két osztályú súlyozott döntési fa**.
1. Használat **tanítási modell** a alkalmazni az algoritmus az adatokat, és a tényleges modell létrehozása.
3. Használat **Score Model** pontszámok előállításához a tesztelési példák segítségével.

Az alábbi ábrán látható egy részét, amelyben az eredeti és replikált képzési csoportok segítségével két különböző SVM modelleket taníthat be ehhez a kísérlethez. **Modell betanításához** a gyakorlókészlethez csatlakozik és **Score Model** csatlakoztatva van a vizsgálat beállítása.

![Kísérlet diagram](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)


A kiértékelés fázisban a kísérlet azt a számítási négy a modell pontosságát. Ehhez a kísérlethez használjuk **Evaluate Model** költség összehasonlítására példa, amelyek az ugyanazon téves besorolás rendelkezik.

A **Evaluate Model** modul képes a számítási teljesítmény-mérőszámait akár két pontozott modellek esetén. Ezért használjuk egy példánya **Evaluate Model** értékelheti ki a két SVM modell és a egy másik példánya **Evaluate Model** a két súlyozott döntési fa modelleket szeretne értékelni.

Figyelje meg, hogy a replikált tesztadatkészlet használja-e a bemeneti **Score Model**. Más szóval a végső pontossága pontszámok kapcsolódnak a nem megfelelő címkéket magukba.

## <a name="combine-multiple-results"></a>Több eredmények összevonása

A **Evaluate Model** modul egyetlen sor, amely tartalmazza a különböző mérőszámokat tartalmazó táblát hoz létre. Az eredmények pontosságának egyetlen készlet létrehozásához, először használjuk **sorok hozzáadása** úgy, hogy az eredmények ezeket egyetlen táblában. Használjuk majd a következő Python-szkriptet a **Python-szkript végrehajtására** az eredmények táblázatát adja meg a modell neve és a képzési megközelítés minden egyes sorára modult:

```
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```


## <a name="results"></a>Results (Eredmények)

A kísérlet az eredmények megtekintéséhez, jobb gombbal az utolsó Visualize kimenete **Select Columns in Dataset** modul.

![Kimenet vizualizációja](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

Az első oszlop a gépi tanulási algoritmus a modell generálásához használt sorolja fel.
A második oszlop a gyakorlókészlethez típusát jelzi.
A harmadik oszlop a költségérzékeny pontossága értéket tartalmaz.

Ezekkel az eredményekkel, láthatja, hogy a modell, amellyel létrehozták a legpontosabb adott **Kétosztályos támogató Vektorgép** és a replikált betanítási adatkészletet a betanított.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a vizuális felületen érhető el a más minták:

- [1 – regressziós. példa: Egy autó árát előrejelzése](ui-sample-regression-predict-automobile-price-basic.md)
- [2 – regressziós. példa: Hasonlítsa össze az autó árának előrejelzése algoritmusok](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Mintául szolgáló 3 - besorolás: Hitelkockázat előrejelzése](ui-sample-classification-predict-credit-risk-basic.md)
- [5 – besorolási. példa: Forgalom előrejelzése](ui-sample-classification-predict-churn.md)
