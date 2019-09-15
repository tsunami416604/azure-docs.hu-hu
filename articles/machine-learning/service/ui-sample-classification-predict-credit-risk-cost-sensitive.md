---
title: 'Visual Interface példa #4: Besorolás a hitelkockázat előrejelzéséhez (a Cost szenzitív)'
titleSuffix: Azure Machine Learning
description: Ez a cikk bemutatja, hogyan hozhat létre egy összetett gépi tanulási kísérletet a vizualizációs felületen. Megtudhatja, hogyan implementálhat egyéni Python-parancsfájlokat, és hogyan hasonlíthat össze több modellt a legjobb lehetőség kiválasztásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 977ff101b0f697a48b3e5595834c98fef0f1119a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997028"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>4\. minta – besorolás: Hitelkockázat (Cost szenzitív)

Ez a cikk bemutatja, hogyan hozhat létre egy összetett gépi tanulási kísérletet a vizualizációs felületen. Megismerheti, hogyan hozhat létre egyéni logikát Python-szkriptek használatával, és hogyan hasonlíthat össze több modellt a legjobb lehetőség kiválasztásához.

Ez a példa egy osztályozó beosztásával Jósolja meg a hitelkockázat-használati adatokat, például a kreditek előzményeit, az életkort és a hitelkártyák számát. A cikkben szereplő fogalmakat azonban a saját gépi tanulási problémák megoldására is alkalmazhatja.

Ha most ismerkedik a gépi tanulással, tekintse meg az [alapszintű osztályozó mintát](ui-sample-classification-predict-credit-risk-basic.md) .

Itt látható a kísérlethez tartozó befejezett gráf:

[![A kísérlet gráfja](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza a minta 4 kísérlet **Megnyitás** gombját:

    ![A kísérlet megnyitása](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Data

A németországi hitelkártya-adatkészletet az UC Irvine adattárból használjuk. Ez az adatkészlet 20 funkcióval és 1 címkével rendelkező 1 000-mintákat tartalmaz. Mindegyik minta egy személyt jelöl. A 20 funkció számszerű és kategorikus funkciókat tartalmaz. Az adatkészletről az [UCI webhelyén](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) talál további információt. Az utolsó oszlop a címkéje, amely a hitelkockázat kialakulását jelöli, és csak két lehetséges értékkel rendelkezik: magas hitelkockázati kockázat = 2, és alacsony hitelkockázat = 1.

## <a name="experiment-summary"></a>Kísérletezés összegzése

Ebben a kísérletben két különböző megközelítést hasonlítunk össze a modellek létrehozásához a probléma megoldásához:

- Képzés az eredeti adatkészlettel.
- Betanítás replikált adatkészlettel.

Mindkét megközelítéssel kiértékeljük a modelleket úgy, hogy a tesztelési adatkészletet használja a replikálással, így biztosítva, hogy az eredmények összhangban legyenek a Cost függvénnyel. Két, mindkét megközelítéssel rendelkező minősítőt tesztelünk: **Kétosztályos támogatású vektoros gép** és **kétosztályos kibővített döntési fa**.

Az alacsony kockázatú példa magas szintű beosztályozásának díja 1, a magas kockázatú példa pedig alacsony érték esetén pedig az 5. Ezt a helytelen besorolási költségeket a **Python-szkriptek** futtatására szolgáló modul segítségével vesszük figyelembe.

Itt látható a kísérlet gráfja:

[![A kísérlet gráfja](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Adatfeldolgozás

Először a **metaadat-szerkesztő** modullal kell megadnia az oszlopnevek hozzáadását, hogy az alapértelmezett OSZLOPNEVEK az UCI-helyen található adatkészlet leírásában szereplő, pontosabb nevekkel legyenek lecserélve. Az új oszlopnevek vesszővel tagolt értékként jelennek meg a **metaadat-szerkesztő** **új oszlop** neve mezőjében.

Ezután létrehozjuk a kockázati előrejelzési modell fejlesztéséhez használt képzési és tesztelési készleteket. Az eredeti adatkészletet az **Adatfelosztási** modul használatával az azonos méretű betanítási és tesztelési csoportokra bontottuk. Az egyenlő méretű készletek létrehozásához az **első kimeneti adatkészletben a sorok töredékét** állítsa 0,5-ra.

### <a name="generate-the-new-dataset"></a>Az új adatkészlet előállítása

Mivel a kockázatok kiszámításának díja magas, a következőhöz hasonló módon állíthatjuk be a téves besorolás költségeit:

- Magas kockázatú esetekben az alacsony kockázatnak minősülő esetek: 5
- Az alacsony kockázatú esetekben a nagy kockázatú esetek nem tartoznak ide: 1

Ennek a Cost-funkciónak a tükrözéséhez új adatkészletet hozunkunk. Az új adatkészletben az egyes magas kockázatú példák öt alkalommal replikálódnak, de az alacsony kockázatú példák száma nem változik. A replikáció előtt az adatokat kiosztjuk a betanítási és tesztelési adatkészletekkel, hogy a sorok ne legyenek mindkét készletben.

A magas kockázatú adatforrások replikálásához a Python-kódot egy **Execute Python parancsfájl** -modulba tesszük:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

A **Python-szkript végrehajtása** modul a betanítási és tesztelési adatkészleteket is replikálja.

### <a name="feature-engineering"></a>Jellemzőkiemelés

A **kétosztályos támogatás vektoros gépi** algoritmusához Normalizált érték szükséges. Ezért a **normalizálás** adatmodul használatával normalizáljuk a numerikus funkciók tartományait egy `tanh` átalakítással. Az `tanh` átalakítások az összes numerikus funkciót egy 0 és 1 tartományon belül értékre alakítják, miközben megőrzik az értékek teljes eloszlását.

A **kétosztályos támogatású vektoros gépi** modul a karakterlánc-funkciókat kezeli, és a kategorikus funkciókra, majd a 0 vagy az 1 értékű bináris funkciókra konvertálja. Ezért nem kell normalizálni ezeket a funkciókat.

## <a name="models"></a>Modellek

Mivel két osztályozást alkalmazunk, a **kétosztályos támogató vektoros gépet** (SVM) és a **kétosztályos kibővített döntési fát**, és két adatkészletet is használhatunk, összesen négy modellt hozunk elő:

- A SVM az eredeti adattal van kiképezve.
- A replikált SVM betanítva.
- Megnövelt döntési fa, amely az eredeti adattal lett kiképezve.
- A megerősített döntési fa replikált adattal van kiképezve.

A modellek létrehozásához, betanításához és teszteléséhez a szabványos kísérleti munkafolyamatot használjuk:

1. Inicializálja a tanulási algoritmusokat a **kétosztályos támogatású vektoros gép** és a **kétosztályos kibővített döntési fa**használatával.
1. A **Train Model** használatával alkalmazza az algoritmust az adatokra, és hozza létre a tényleges modellt.
1. A **pontszám modell** használatával pontszámokat hozhat létre a tesztelési példák alapján.

Az alábbi ábrán a kísérlet egy része látható, amelyben az eredeti és a replikált betanítási készletek két különböző SVM-modell betanítására szolgálnak. A betanítási **modell** a betanítási készlethez csatlakozik, és a **pontszám modell** a tesztelési készlethez van csatlakoztatva.

![Kísérleti gráf](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

A kísérlet kiértékelési szakaszában a négy modell pontosságát számítjuk ki. Ebben a kísérletben a **kiértékelési modell** segítségével hasonlítjuk össze azokat a példákat, amelyeknek azonos a téves besorolási díja.

A **modell kiértékelése** modul a teljesítmény mérőszámait akár két gólt is kiszámíthatja. Ezért a **modell kiértékelésének** egyik példányát használjuk a két SVM-modell kiértékelésére és a **modell** egy másik példányára, hogy kiértékelje a két Kiemelt döntési fa modelljét.

Figyelje meg, hogy a rendszer a replikált tesztelési adatkészletet használja a **pontszám modell**bemenetként. Más szóval a végső pontossági pontszámok tartalmazzák a címkék hibás beolvasásának költségeit.

## <a name="combine-multiple-results"></a>Több eredmény egyesítése

A **modell kiértékelése** modul olyan táblázatot hoz létre, amely egy sor különböző metrikákat tartalmaz. A pontossági eredmények egyetlen készletének létrehozásához először a **sorok hozzáadása** lehetőséget használjuk az eredmények egyetlen táblába való összevonásához. Ezután az alábbi Python-szkriptet használjuk a **Python szkript végrehajtása** modulban a modell nevének és a betanítási módszernek a táblázat minden egyes sorához való hozzáadásához:

```Python
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

A kísérlet eredményeinek megtekintéséhez kattintson a jobb gombbal az adatkészlet modul utolsó **Select oszlopának** vizualizáció kimenetére.

![Kimenet megjelenítése](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

Az első oszlop felsorolja a modell létrehozásához használt gépi tanulási algoritmust.
A második oszlop a betanítási készlet típusát jelöli.
A harmadik oszlop a költséghatékony pontossági értéket tartalmazza.

Ezekből az eredményekből láthatja, hogy a legjobb pontosságot a **kétosztályos támogatású vektoros géppel** létrehozott modell adta meg, és a replikált betanítási adatkészletre van kiképezve.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a vizuális felületen elérhető egyéb mintákat:

- [1. példa – regresszió: Autó árának előrejelzése](ui-sample-regression-predict-automobile-price-basic.md)
- [2. minta – regresszió: Algoritmusok összehasonlítása az autó árának előrejelzéséhez](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [3. példa – besorolás: Hitelkockázat előrejelzése](ui-sample-classification-predict-credit-risk-basic.md)
- [5. példa – besorolás: Forgalom előrejelzése](ui-sample-classification-predict-churn.md)
- [6. példa – besorolás: Repülési késések előrejelzése](ui-sample-classification-predict-flight-delay.md)
