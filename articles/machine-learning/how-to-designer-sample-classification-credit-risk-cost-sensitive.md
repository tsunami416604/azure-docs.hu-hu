---
title: 'Tervező: hitelkockázat előrejelzése – példa'
titleSuffix: Azure Machine Learning
description: Osztályozó készítése és egyéni Python-parancsfájlok használata a hitelkockázat előrejelzéséhez Azure Machine Learning Designer használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 54ca0df005dccceacc88044a51f31ad784b7071b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763402"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>Osztályozó & létrehozása Python-parancsfájlok használatával a hitelkockázat előrejelzéséhez Azure Machine Learning Designer használatával

**Designer (előzetes verzió) 4. minta**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ez a cikk bemutatja, hogyan hozhat létre egy összetett gépi tanulási folyamatot a Designer (előzetes verzió) használatával. Megismerheti, hogyan hozhat létre egyéni logikát Python-szkriptek használatával, és hogyan hasonlíthat össze több modellt a legjobb lehetőség kiválasztásához.

Ez a példa egy osztályozó beosztásával Jósolja meg a hitelkockázat-használati adatokat, például a kreditek előzményeit, az életkort és a hitelkártyák számát. A cikkben szereplő fogalmakat azonban a saját gépi tanulási problémák megoldására is alkalmazhatja.

Itt látható a folyamathoz tartozó befejezett gráf:

[a folyamat ![gráfja](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. A megnyitásához kattintson a 4. minta elemre.

## <a name="data"></a>Adatok

Ez a példa a németországi hitelkártya-adatkészletet használja az UC Irvine adattárból. 1 000 mintát tartalmaz 20 funkcióval és egy címkével. Mindegyik minta egy személyt jelöl. A 20 funkció számszerű és kategorikus funkciókat tartalmaz. Az adatkészletről az [UCI webhelyén](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)talál további információt. Az utolsó oszlop a címkéje, amely a hitelkockázat kialakulását jelöli, és csak két lehetséges értékkel rendelkezik: magas hitelkockázati kockázat = 2, és alacsony hitelkockázat = 1.

## <a name="pipeline-summary"></a>Folyamat összegzése

Ebben a folyamatban a probléma megoldásához két különböző megközelítést hasonlít össze a modellek létrehozásához:

- Képzés az eredeti adatkészlettel.
- Betanítás replikált adatkészlettel.

Mindkét megközelítéssel kiértékeli a modelleket úgy, hogy a tesztelési adatkészletet használja a replikációval, így biztosítva, hogy az eredmények összhangban legyenek a Cost függvénnyel. Tesztelje a két osztályt mindkét módszerrel: **kétosztályos támogatású vektoros gép** és **kétosztályos, megnövelt döntési fa**.

Az alacsony kockázatú példa magas szintű beosztályozásának díja 1, a magas kockázatú példa pedig alacsony érték esetén pedig az 5. Ezt a helytelen besorolási költségeket a **Python-szkriptek** futtatására szolgáló modul segítségével vesszük figyelembe.

Itt látható a folyamat gráfja:

[a folyamat ![gráfja](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Adatfeldolgozás

A **metaadatok szerkesztő** moduljának használatával megkezdheti az oszlopnevek hozzáadását az alapértelmezett oszlopnevek és az UCI-beli adatkészlet leírásában beszerzett, pontosabb nevek helyett. Adja meg az új oszlopnevek vesszővel elválasztott értékként a **metaadat-szerkesztő** **új oszlop** neve mezőjében.

Ezután hozza létre a kockázati előrejelzési modell fejlesztéséhez használt képzési és tesztelési csoportokat. Az **Adatfelosztási** modul használatával Ossza szét az eredeti adatkészletet a betanítási és tesztelési készletekbe. Az egyenlő méretű készletek létrehozásához állítsa a **sorok töredékét az első kimeneti adatkészletben** a 0,7 értékre.

### <a name="generate-the-new-dataset"></a>Az új adatkészlet előállítása

Mivel a kockázatok kiszámításának díja magas, a következőhöz hasonló módon állíthatja be a téves besorolás költségeit:

- Magas kockázatú esetekben az alacsony kockázatnak minősülő esetek: 5
- Alacsony kockázatú esetekben a nagy kockázatú esetek: 1

Ahhoz, hogy tükrözze ezt a Cost függvényt, egy új adatkészletet állítson elő. Az új adatkészletben az egyes magas kockázatú példák öt alkalommal replikálódnak, de az alacsony kockázatú példák száma nem változik. A replikáció előtt Ossza szét az adatokat képzésre és tesztelési adatkészletekre, hogy ne kelljen mindkét készletben azonos sort kialakítani.

A magas kockázatú adatforrások replikálásához helyezze ezt a Python-kódot egy **Execute Python parancsfájl** -modulba:

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

A **kétosztályos támogatás vektoros gépi** algoritmusához Normalizált érték szükséges. Ezért a **normalizálás** adatmodul használatával normalizálja az összes numerikus funkció tartományát egy `tanh` átalakítással. A `tanh` átalakítás az összes numerikus funkciót egy 0 és 1 tartományba eső értékre konvertálja, miközben az értékek teljes eloszlását megőrzi.

A **kétosztályos támogatású vektoros gépi** modul kezeli a karakterlánc-funkciókat, átalakítja azokat a kategorikus funkciókra, majd a bináris funkciókra, amelyek értéke nulla vagy egy. Így nem szükséges a funkciók normalizálása.

## <a name="models"></a>Modellek

Mivel két besorolást alkalmazott, a **kétosztályos támogató vektoros gép** (SVM) és a **kétosztályos kibővített döntési fa**, valamint két adatkészlet összesen négy modellt hoz majd ki:

- A SVM az eredeti adattal van kiképezve.
- A replikált SVM betanítva.
- Megnövelt döntési fa, amely az eredeti adattal lett kiképezve.
- A megerősített döntési fa replikált adattal van kiképezve.

Ez a példa a szabványos adatelemzési munkafolyamatot használja a modellek létrehozásához, betanításához és teszteléséhez:

1. Inicializálja a tanulási algoritmusokat a **kétosztályos támogatású vektoros gép** és a **kétosztályos kibővített döntési fa**használatával.
1. A **Train Model** használatával alkalmazza az algoritmust az adatokra, és hozza létre a tényleges modellt.
1. A **pontszám modell** használatával pontszámokat hozhat létre a tesztelési példák alapján.

Az alábbi ábrán a folyamat egy része látható, amelyben az eredeti és a replikált betanítási készletek két különböző SVM-modell betanítására szolgálnak. A betanítási **modell** a betanítási készlethez csatlakozik, és a **pontszám modell** a tesztelési készlethez van csatlakoztatva.

![Folyamat gráf](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/score-part.png)

A folyamat kiértékelési szakaszában a négy modell pontosságát számítja ki. Ehhez a folyamathoz a **kiértékelési modell** használatával hasonlítsa össze azokat a példákat, amelyeknek azonos a téves besorolási díja.

A **modell kiértékelése** modul a teljesítmény mérőszámait akár két gólt is kiszámíthatja. Így a **modell kiértékelésének** egyik példányával kiértékelheti a két SVM modellt és a **modell** egy másik példányát, hogy kiértékelje a két Kiemelt döntési fa modelljét.

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

## <a name="results"></a>Eredmények

A folyamat eredményének megtekintéséhez kattintson a jobb gombbal az adatkészlet modul utolsó **Select oszlopának** vizualizáció kimenetére.

![Kimenet megjelenítése](media/how-to-designer-sample-classification-credit-risk-cost-sensitive/sample4-lastselect-1225.png)

Az első oszlop felsorolja a modell létrehozásához használt gépi tanulási algoritmust.

A második oszlop a betanítási készlet típusát jelöli.

A harmadik oszlop a költséghatékony pontossági értéket tartalmazza.

Ezekből az eredményekből láthatja, hogy a legjobb pontosságot a **kétosztályos támogatású vektoros géppel** létrehozott modell adta meg, és a replikált betanítási adatkészletre van kiképezve.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ismerje meg a tervező számára elérhető egyéb mintákat:

- [1. példa – regresszió: az autó árának előrejelzése](how-to-designer-sample-regression-automobile-price-basic.md)
- [2. minta – regresszió: algoritmusok összehasonlítása az autó árának előrejelzéséhez](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [3. minta – besorolás a szolgáltatás kiválasztásával: bevétel előrejelzése](how-to-designer-sample-classification-predict-income.md)
- [5. példa – besorolás: forgalom előrejelzése](how-to-designer-sample-classification-churn.md)
- [6. példa – besorolás: repülési késések előrejelzése](how-to-designer-sample-classification-flight-delay.md)
- [7. minta – szöveges besorolás: wikipedia SP 500 adatkészlet](how-to-designer-sample-text-classification.md)
