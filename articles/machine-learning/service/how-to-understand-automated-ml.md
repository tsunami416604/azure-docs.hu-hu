---
title: Az automatizált ML-eredmények ismertetése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan tekintheti meg és értelmezheti a diagramokat és mérőszámokat az egyes automatizált gépi tanulási futtatásokhoz.
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 93695e0bbcb81a570519a6f74cfdeab4ef85f076
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489397"
---
# <a name="understand-automated-machine-learning-results"></a>Az automatizált gépi tanulás eredményeinek megismerése
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan tekintheti meg és értelmezheti az egyes automatizált gépi tanulási folyamatokhoz tartozó diagramokat és mérőszámokat. 

További információk:
+ [Mérőszámok, diagramok és görbék a besorolási modellekhez](#classification)
+ [Mérőszámok, diagramok és grafikonok regressziós modellekhez](#regression)
+ [Modell-értelmező és-funkció fontossága](#explain-model)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Hozzon létre egy kísérletet az automatizált gépi tanuláshoz, vagy az SDK-val vagy a Azure Machine Learning Studióban.

    * Az SDK használata [besorolási modell](how-to-auto-train-remote.md) vagy [regressziós modell](tutorial-auto-train-models.md) létrehozásához
    * A megfelelő adatfeltöltés használatával hozzon létre egy besorolási vagy regressziós modellt a [Azure Machine learning Studióval](how-to-create-portal-experiments.md) .

## <a name="view-the-run"></a>A Futtatás megtekintése

Az automatizált gépi tanulási kísérlet futtatása után a futtatások előzményei a Machine learning-munkaterületen találhatók. 

1. Lépjen a munkaterülethez.

1. A munkaterület bal oldali paneljén válassza a **kísérletek**lehetőséget.

   ![A kísérlet menü képernyőképe](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. A kísérletek listájában válassza ki a felderíteni kívánt elemet.

   [![-kísérletek listája](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Az alsó táblában válassza ki a **futtatási számot**.

   [![kísérlet futtatása](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. Az ismétlések táblázatban válassza ki a modell azon **iterációjának számát** , amelyet szeretne felderíteni.

   [![kísérleti modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Ugyanezeket az eredményeket a Futtatás során is láthatja, amikor a `RunDetails`[Jupyter widgetet](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)használja.

## <a name="classification"></a>Besorolási eredmények

A következő mérőszámokat és diagramokat minden olyan besorolási modell esetében elérhetővé teszi, amelyet a Azure Machine Learning gépi tanulási képességeinek felhasználásával épít fel.

+ [Metrikák](#classification-metrics)
+ [Zavart mátrix](#confusion-matrix)
+ [Precíziós visszahívási diagram](#precision-recall-chart)
+ [Fogadó működési jellemzői (vagy ROC)](#roc)
+ [Emelő görbe](#lift-curve)
+ [Nyereségi görbe](#gains-curve)
+ [Kalibrálási ábra](#calibration-plot)

### <a name="classification-metrics"></a>Besorolási metrikák

A következő metrikákat menti a rendszer minden egyes futtatási iterációban egy besorolási feladathoz.

|Metrika|Leírás|Számítás|További paraméterek
--|--|--|--|
AUC_Macro| A AUC a fogadó operációs karakterisztika görbe alatti terület. A makró az egyes osztályok AUC számtani középértéke.  | [Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlag = "makró"|
AUC_Micro| A AUC a fogadó operációs karakterisztika görbe alatti terület. A Micro kiszámításának alapja az egyes osztályok valódi pozitív és hamis pozitív kombinációja.| [Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlag = "Micro"|
AUC_Weighted  | A AUC a fogadó operációs karakterisztika görbe alatti terület. A súlyozott érték az egyes osztályok pontszámának számtani középértéke, amelyet az egyes osztályokban lévő igaz példányok száma alapján kell súlyozni.| [Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|átlag = "súlyozott"
accuracy|A pontosság azon előre jelzett címkék százaléka, amelyek pontosan megfelelnek az igaz feliratoknak. |[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|Az átlagos pontosság egy precíziós visszahívás görbét összegzi, amely az egyes küszöbértékekben elért pontosságok súlyozott középértékét jelenti, és a rendszer visszahívja az előző küszöbértéket, amelyet a súlyozáshoz használtak. A makró az egyes osztályok átlagos pontossági pontszámának számtani középértéke.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlag = "makró"|
average_precision_score_micro|Az átlagos pontosság egy precíziós visszahívás görbét összegzi, amely az egyes küszöbértékekben elért pontosságok súlyozott középértékét jelenti, és a rendszer visszahívja az előző küszöbértéket, amelyet a súlyozáshoz használtak. A Micro kiszámításának alapja a valódi pozitív és a hamis pozitív érték összevonása minden egyes levágáskor.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlag = "Micro"|
average_precision_score_weighted|Az átlagos pontosság egy precíziós visszahívás görbét összegzi, amely az egyes küszöbértékekben elért pontosságok súlyozott középértékét jelenti, és a rendszer visszahívja az előző küszöbértéket, amelyet a súlyozáshoz használtak. A súlyozott érték az egyes osztályok átlagos pontossági pontszámának számtani középértéke, amelyet az egyes osztályokban lévő igaz példányok száma alapján kell súlyozni.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlag = "súlyozott"|
balanced_accuracy|A kiegyensúlyozott pontosság az egyes osztályok visszahívásának számtani középértéke.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "makró"|
f1_score_macro|Az F1 pontszám a pontosság és a visszahívás harmonikus középértéke. A makró az F1 pontszám számtani középértéke az egyes osztályokhoz.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlag = "makró"|
f1_score_micro|Az F1 pontszám a pontosság és a visszahívás harmonikus középértéke. A Micro kiszámításának alapja a teljes valódi pozitív, a hamis negatív és a téves pozitív érték számítása.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlag = "Micro"|
f1_score_weighted|Az F1 pontszám a pontosság és a visszahívás harmonikus középértéke. Súlyozott középértékek az egyes osztályokhoz tartozó F1-pontszámok osztályának gyakorisága alapján|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlag = "súlyozott"|
log_loss|Ez a (MULTINOMIAL) logisztikai regresszió és bővítmények (például neurális hálózatok) által használt veszteséges függvény, amely negatív naplózási valószínűséggel van meghatározva az igaz címkék valószínűsége alapján, az osztályozó jóslatai. Egyetlen olyan minta esetében, amelynél az {0,1} és a következő a valószínűsége: a YT = 1, a log P (YT&#124;YP) =-(YT-napló (YP) + (1-YT) napló (1-YP)|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|A normalizált makrók felidézése a makró-visszahívás normalizált, így a véletlenszerű teljesítmény pontszáma 0, a tökéletes teljesítmény pedig 1. Ez a norm_macro_recall: = (recall_score_macro-R)/(1-R) által érhető el, ahol az R a recall_score_macro várt értéke a véletlenszerű előrejelzések esetében (pl. R = 0,5 a bináris besoroláshoz és R = (1/C) a C osztályú besorolási problémákhoz).|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "makró" |
precision_score_macro|A pontosság a megadott osztályba tartozó elemek százaléka, amely valójában ebben az osztályban található. A makró az egyes osztályok pontosságának számtani középértéke.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlag = "makró"|
precision_score_micro|A pontosság a megadott osztályba tartozó elemek százaléka, amely valójában ebben az osztályban található. A Micro kiszámításának alapja a teljes valódi pozitív és a hamis pozitív eredmény.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlag = "Micro"|
precision_score_weighted|A pontosság a megadott osztályba tartozó elemek százaléka, amely valójában ebben az osztályban található. A súlyozott érték az egyes osztályok pontosságának számtani középértéke, az egyes osztályokban lévő igaz példányok számával súlyozottan.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlag = "súlyozott"|
recall_score_macro|A visszahívás az adott osztályhoz tartozó, megfelelően címkézett elemek százalékos arányát jelöli. A makró az egyes osztályok visszahívásának számtani középértéke.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "makró"|
recall_score_micro|A visszahívás az adott osztályhoz tartozó, megfelelően címkézett elemek százalékos arányát jelöli. A Micro kiszámításának alapja a teljes valódi pozitív, a hamis negatív és a téves pozitív érték megszámlálása|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "Micro"|
recall_score_weighted|A visszahívás az adott osztályhoz tartozó, megfelelően címkézett elemek százalékos arányát jelöli. A súlyozott érték az egyes osztályok visszahívásának számtani középértéke, az egyes osztályokban lévő igaz példányok számával súlyozottan.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "súlyozott"|
weighted_accuracy|A súlyozott pontosság az a pontosság, amelyben az egyes példákban megadott súlyozás egyenlő az adott példában szereplő igaz osztályokba tartozó valódi példányok arányával.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|a sample_weight olyan vektor, amely a cél egyes elemeihez tartozó osztály arányának felel meg|

### <a name="confusion-matrix"></a>Zavart mátrix

A rendszer a besorolási modell teljesítményének leírására használja a zűrzavaros mátrixot. Minden sor megjeleníti a True osztály példányait, és minden oszlop az előrejelzett osztály példányait jelöli. A zavart mátrix a megfelelő besorolású címkéket és az adott modell helytelen besorolású címkéit jeleníti meg.

Besorolási problémák esetén Azure Machine Learning automatikusan egy összekeverhető mátrixot biztosít minden létrehozott modellhez. Az automatikus ML minden egyes összekeveredési mátrix esetében megjeleníti az egyes előre jelzett címkék gyakoriságát és az egyes igaz címkék metszetét. Minél sötétebb a szín, annál nagyobb számnak kell lennie a mátrix adott részében. Ideális esetben a legsötétebb színek a mátrix átlója. 

1\. példa: gyenge pontosságú besorolási modell ![A gyenge pontosságú besorolási modellel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

2\. példa: nagy pontossággal (ideális) ![egy nagy pontosságú besorolási modellt](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Precíziós visszahívási diagram

Ezzel a diagrammal összevetheti az egyes modellek pontossági felidézési görbéit, és meghatározhatja, hogy melyik modell elfogadható kapcsolata legyen a pontosság és az adott üzleti probléma felidézése között. Ebben a diagramban a makrók pontossága – visszahívás, a mikro-átlag pontossága – visszahívás, valamint a modell összes osztályához társított pontossági visszahívás látható.

A pontosság kifejezés azt jelenti, hogy az osztályozó képes az összes példány megfelelő címkézésére. A visszahívás azt jelöli, hogy egy osztályozó képes-e egy adott címke összes példányának megkeresésére. A precíziós visszahívás görbe a két fogalom közötti kapcsolatot mutatja. Ideális esetben a modell 100%-os pontossággal és 100%-os pontossággal rendelkezhet.

1\. példa: egy kis precizitású és kis visszahívású besorolási modell ![egy kis precizitású és alacsony visszahívás](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

2\. példa: egy ~ 100% Precision és ~ 100% Recall (ideális) besorolási modell, ![A besorolási modell nagy pontossággal és visszahívás](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

A fogadó működési jellemzője (vagy a ROC) a helyesen besorolt címkék és egy adott modell helytelen besorolású címkéi. A ROC-görbe kevésbé informatív lehet, ha nagy mértékű torzítással rendelkező adatkészleteken tanít modelleket, mivel nem jeleníti meg a hamis pozitív címkéket.

1\. példa: alacsony igaz címkéket és magas hamis címkéket ![besorolási modell alacsony igaz címkékkel és magas hamis címkékkel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

2\. példa: A magas igaz címkéket és az alacsony hamis címkéket ![besorolási modell magas igaz címkékkel és alacsony téves címkékkel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Emelő görbe

Az adott modell értékének megszerzéséhez összehasonlíthatja a modell azon felvonóját, amelyet a rendszer automatikusan Azure Machine Learning az alaptervhez.

A kiemelési diagramok a besorolási modell teljesítményének kiértékelésére szolgálnak. Azt mutatja, hogy mennyit lehet a modellhez képest egy modellel elvárni. 

1\. példa: a modell egy véletlenszerű kiválasztási modellnél rosszabbra ![egy olyan besorolási modellt, amely rosszabb, mint egy véletlenszerű kiválasztási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

2\. példa: a modell jobbat végez, mint egy véletlenszerű kiválasztási modell ![egy besorolási modellt, amely jobb](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Nyereségi görbe

A nyereségi diagram kiértékeli a besorolási modell teljesítményét az adat egyes részei alapján. Ez az adathalmaz minden egyes százalékos arányát mutatja, ami sokkal jobb lehet, ha a véletlenszerű kiválasztási modellhez képest jobban elvárható.

A halmozott nyereség diagram segítségével kiválaszthatja a besorolást a modell kívánt nyereségének megfelelő százalékos arány használatával. Ez az információ egy másik módszert biztosít a kapcsolódó felvonó diagram eredményeinek megtekintésére.

1\. példa: besorolási modell minimális nyereséggel ![egy minimális nyereségű besorolási modellel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

2\. példa: egy jelentős nyereséggel ![besorolási modell jelentős nyereséggel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Kalibrálási ábra

Az összes besorolási probléma esetében áttekintheti a kalibrációs vonalat a mikro-átlag, a makro-átlag és az egyes osztályok számára egy adott prediktív modellben. 

Egy kalibrációs terület a prediktív modell megbízhatóságának megjelenítésére szolgál. Ez az előre jelzett valószínűség és a tényleges valószínűség közötti kapcsolat bemutatásával történik, ahol a "valószínűség" azt jelzi, hogy egy adott példány egy adott címkéhez tartozik-e. A jól kalibrált modell az y = x vonallal van igazítva, ahol az előrejelzésekben ésszerűen magabiztos. A túlzottan magabiztos modell az y = 0 sorral van igazítva, ahol az előre jelzett valószínűség látható, de nincs tényleges valószínűség.

1\. példa: egy jól kalibrált modell ![ jól kalibrált modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

2\. példa: egy több mint magabiztos modell ![egy magabiztos modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Regressziós eredmények

A következő mérőszámok és diagramok érhetők el minden olyan regressziós modellhez, amelyet a Azure Machine Learning automatizált gépi tanulási képességeivel épít fel.

+ [Metrikák](#reg-metrics)
+ [Előre jelzett vagy igaz](#pvt)
+ [Maradványok hisztogramja](#histo)


### <a name="reg-metrics"></a>Regressziós metrikák

A rendszer a következő metrikákat menti a regressziós vagy előrejelzési feladatokhoz minden futtatási iteráció során.

|Metrika|Leírás|Számítás|További paraméterek
--|--|--|--|
explained_variance|Az elmagyarázott variancia azt az arányt adja meg, amelynek a matematikai modellje egy adott adathalmaz variációját mutatja. Ez az eredeti és a hibák eltérésének százalékos csökkenése. Ha a hibák középértéke 0, akkor egyenlő a magyarázattal.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score|R2 a meghatározási együttható vagy a négyzetes hibák százalékos csökkenése a középértéket eredményező alapmodellhez képest. |[Kiszámítása](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|A lándzsás korreláció a két adathalmaz közötti kapcsolat monotonicity nem számszerű mérőszáma. A Pearson korrelációtól eltérően a lándzsás korreláció nem feltételezi, hogy mindkét adathalmazt általában kiosztják. Más korrelációs tényezőhöz hasonlóan ez az 1 és + 1 között változik, és a 0 érték nem jelent korrelációt. Az-1 vagy + 1 korrelációk pontos monoton kapcsolatot jelentenek. A pozitív korrelációk azt sugallják, hogy az x növekszik, így az y. A negatív korrelációk azt sugallják, hogy az x nő, az y pedig csökken.|[Kiszámítása](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|Abszolút érték: a cél és az előrejelzés közötti különbség abszolút értéke a várt érték.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error|Normalizált Mean abszolút hiba: abszolút hiba, amelyet az adattartomány feloszt|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Osztás az adattartomány szerint|
median_absolute_error|A medián abszolút hiba a cél és az előrejelzés közötti összes abszolút különbség középértéke. Ez a veszteség robusztus a kiugró értékeknél.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|Normalizált medián abszolút hiba: az adattartományon belül az abszolút érték középértékének hányadosa.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Osztás az adattartomány szerint|
root_mean_squared_error|A legfelső szintű, négyzetes hiba a cél és az előrejelzés között a várt négyzetes különbség négyzetes gyökere.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error|Normalizált legfelső szintű, négyzetes hiba: a legfelső szintű, négyzetes hiba, amely az adattartomány szerint oszlik el|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Osztás az adattartomány szerint|
root_mean_squared_log_error|A legfelső szintű négyzetes naplózási hiba a várt szögletes logaritmikus hiba négyzetének gyökere.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error|Normalizált legfelső szintű, négyzetes naplózási hiba: legfelső szintű négyzetes naplózási hiba az adattartomány szerint elosztva|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Osztás az adattartomány szerint|

### <a name="pvt"></a>Előre jelzett vagy igaz

Az előre jelzett és az igaz érték azt mutatja, hogy egy regressziós probléma esetén az előre jelzett érték és a korrelációs valódi értéke közötti kapcsolat látható. Ez a gráf felhasználható egy modell teljesítményének mérésére, ahogy az y = x vonal közelebb van az előre jelzett értékekhez, annál jobb a prediktív modell pontossága.

Az egyes futtatások után megtekintheti az egyes regressziós modellekhez tartozó, előre jelzett és igaz gráfot is. Az adatok védelme érdekében az értékek együtt dobozolni, és az egyes raktárhelyek mérete a diagramterület alsó részén oszlopdiagramként jelenik meg. A prediktív modellt összehasonlíthatja a világosabb árnyalattal, amely a hibák margóit mutatja, így a modellnek ideális értékkel kell rendelkeznie.

1\. példa: az előrejelzések kis pontosságú regressziós modellje ![egy regressziós modellt, amely alacsony pontossággal rendelkezik az előrejelzésekben](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

2\. példa: az előrejelzésekben nagy pontosságú regressziós modell [![egy regressziós modellt, amelynek előrejelzései nagy pontossággal rendelkeznek](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Maradványok hisztogramja

A maradék egy megfigyelt y – az előre jelzett y karaktert jelöli. Ha alacsony torzítású hibát szeretne megjeleníteni, a maradékok hisztogramját harang alakú görbévé kell alakítani, a 0 körüli középpontba. 

1\. példa: egy regressziós modell, amelynek a hibája ![SA regressziós modellje a hibák torzításával](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

2\. példa: egy regressziós modell, amely többek között a hibák egyenletes eloszlásával ![egy regressziós modellt a hibák egyenletes eloszlásával](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Modell-értelmező és-funkció fontossága

A funkció fontossága révén megtekintheti, hogy az egyes szolgáltatások milyen értékesek a modell kialakításában. Ez a számítás alapértelmezés szerint ki van kapcsolva, mivel jelentősen növelheti a futási időt.   Az összes modellhez engedélyezheti a modell magyarázatát, vagy csak a legjobban illeszkedő modellt magyarázza el.

Tekintse át a funkció fontossági pontszámát a modell általános, valamint a prediktív modell osztályán. Az egyes funkciókhoz viszonyítva az egyes osztályok és a szolgáltatások fontossága is látható.

![Funkciók magyarázatának képessége](./media/how-to-understand-automated-ml/feature-importance.gif)

A tolmácsolási funkciók engedélyezésével kapcsolatos további információkért tekintse [meg az AUTOMATIZÁLT](how-to-machine-learning-interpretability-automl.md) ml-kísérletek értelmezésének engedélyezése című témakört.

## <a name="next-steps"></a>További lépések

+ További információ a Azure Machine Learning [automatizált ml](concept-automated-ml.md) -ről.
+ Próbálja ki az [automatizált Machine learning Model magyarázat](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) minta notebookját.
