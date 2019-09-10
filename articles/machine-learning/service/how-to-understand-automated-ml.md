---
title: Az automatizált ML-eredmények ismertetése
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan tekintheti meg és értelmezheti a diagramokat és mérőszámokat az egyes automatizált gépi tanulási futtatásokhoz.
services: machine-learning
author: nilesha
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: 60ecd71419bbf65cceab257cb97a96929d24ec08
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860555"
---
# <a name="understand-automated-machine-learning-results"></a>Az automatizált gépi tanulás eredményeinek megismerése

Ebből a cikkből megtudhatja, hogyan tekintheti meg és értelmezheti az egyes automatizált gépi tanulási folyamatokhoz tartozó diagramokat és mérőszámokat. 

További információk az alábbiakról:
+ [Mérőszámok, diagramok és görbék a besorolási modellekhez](#classification)
+ [Mérőszámok, diagramok és grafikonok regressziós modellekhez](#regression)
+ [Modell-értelmező és-funkció fontossága](#explain-model)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Hozzon létre egy automatizált gépi tanulási kísérletet az SDK-val, a Azure Portal vagy a munkaterület kezdőlapján (előzetes verzió).

    * Az SDK használata [besorolási modell](how-to-auto-train-remote.md) vagy regressziós [modell](tutorial-auto-train-models.md) létrehozásához
    * A megfelelő adatfeltöltés használatával hozzon létre egy besorolási vagy regressziós modellt a [Azure Portal vagy a munkaterület kezdőlapján (előzetes verzió)](how-to-create-portal-experiments.md) .

## <a name="view-the-run"></a>A Futtatás megtekintése

Az automatizált gépi tanulási kísérlet futtatása után a futtatások előzményei a Machine learning szolgáltatás munkaterületén találhatók. 

1. Lépjen a munkaterülethez.

1. A munkaterület bal oldali paneljén válassza a **kísérletek**lehetőséget.

   ![Kísérlet menü képernyőképe](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. A kísérletek listájában válassza ki a felderíteni kívánt elemet.

   [![Kísérletek listája](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Az alsó táblában válassza ki a **futtatási számot**.

   [ Kísérletfuttatása![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. Az ismétlések táblázatban válassza ki a modell azon **iterációjának számát** , amelyet szeretne felderíteni.

   [![Kísérleti modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Ugyanezeket az eredményeket is láthatja a Futtatás során, amikor a `RunDetails` [Jupyter widgetet](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)használja.

## <a name="classification"></a>Besorolási eredmények

A következő mérőszámokat és diagramokat minden olyan besorolási modell esetében elérhetővé teszi, amelyet a Azure Machine Learning gépi tanulási képességeinek felhasználásával épít fel.

+ [Metrikák](#classification-metrics)
+ [Keveredési mátrix](#confusion-matrix)
+ [Pontosság-visszahívási diagram](#precision-recall-chart)
+ [Fogadó működési jellemzői (vagy ROC)](#roc)
+ [Átemelés görbévé](#lift-curve)
+ [Nyereség görbévé](#gains-curve)
+ [Hitelesítési diagram](#calibration-plot)

### <a name="classification-metrics"></a>Besorolási metrikák

A következő metrikákat menti a rendszer minden egyes futtatási iterációban egy besorolási feladathoz.

|Metrika|Leírás|Számítás|További paraméterek
--|--|--|--|
AUC_Macro| AUC az a terület, a fogadó működő jellemző görbe alatt. Makró minden egyes osztály a AUC számtani középértékét.  | [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlagos = "makra."|
AUC_Micro| AUC az a terület, a fogadó működő jellemző görbe alatt. A Micro kiszámításának alapja az egyes osztályok valódi pozitív és hamis pozitív kombinációja.| [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlagos = "micro"|
AUC_Weighted  | AUC az a terület, a fogadó működő jellemző görbe alatt. A súlyozott érték az egyes osztályok pontszámának számtani középértéke, amelyet az egyes osztályokban lévő igaz példányok száma alapján kell súlyozni.| [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|átlagos = "súlyozott"
accuracy|Pontosság pontosan egyezik a valódi címkéket előre jelzett címkékhez százaléka. |[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|Átlagos pontosság pontosság-visszahívási görbe elért minden egyes küszöbértéket, a korábbi küszöbértéket, a súly használt fogyasztóktól növekedés az szükséges, a súlyozott átlag foglalja össze. A makró az egyes osztályok átlagos pontossági pontszámának számtani középértéke.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlagos = "makra."|
average_precision_score_micro|Átlagos pontosság pontosság-visszahívási görbe elért minden egyes küszöbértéket, a korábbi küszöbértéket, a súly használt fogyasztóktól növekedés az szükséges, a súlyozott átlag foglalja össze. A Micro kiszámításának alapja a valódi pozitív és a hamis pozitív érték összevonása minden egyes levágáskor.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlagos = "micro"|
average_precision_score_weighted|Átlagos pontosság pontosság-visszahívási görbe elért minden egyes küszöbértéket, a korábbi küszöbértéket, a súly használt fogyasztóktól növekedés az szükséges, a súlyozott átlag foglalja össze. A súlyozott érték az egyes osztályok átlagos pontossági pontszámának számtani középértéke, amelyet az egyes osztályokban lévő igaz példányok száma alapján kell súlyozni.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlagos = "súlyozott"|
balanced_accuracy|Elosztott terhelésű pontosságát az egyes osztályok visszaírási számtani középértékét.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "makra."|
f1_score_macro|F1 pontszám: közepének pontosság és a visszahívás. A makró az F1 pontszám számtani középértéke az egyes osztályokhoz.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlagos = "makra."|
f1_score_micro|F1 pontszám: közepének pontosság és a visszahívás. A Micro kiszámításának alapja a teljes valódi pozitív, a hamis negatív és a téves pozitív érték számítása.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlagos = "micro"|
f1_score_weighted|F1 pontszám: közepének pontosság és a visszahívás. Súlyozott átlag által az egyes osztályok F1 pontszám osztály gyakorisága|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlagos = "súlyozott"|
log_loss|Ez az a veszteség függvény a logisztikai regressziós (multinomial) és -bővítmények, például a Neurális hálózatokat definiálva a valószínűségi besorolás előrejelzéseket megadott igaz címkék negatív log valószínűségét, használt. Egyetlen olyan minta esetében, amely az "YT {0,1} ", a "YT" és az "a" becsült valószínűsége a következő: YT&#124;= 1, a napló elvesztése – log P (YT YP) =-(YT-napló (YP) + (1-YT) napló (1-YP)).|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|Normalizált makró visszahívása makró ne felejtse el, hogy a véletlenszerű teljesítmény 0 pontszámot pedig tökéletes teljesítmény 1 pontszámot normalized. Ez a norm_macro_recall: = (recall_score_macro-R)/(1-R) által érhető el, ahol az R a recall_score_macro várt értéke a véletlenszerű előrejelzések esetében (pl. R = 0,5 a bináris besoroláshoz és R = (1/C) a C osztályú besorolási problémákhoz).|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "makró" |
precision_score_macro|Pontosság a százalékos aránya a következő címkét: egy adott osztály elemeit tartalmazza ténylegesen az adott osztály. A makró az egyes osztályok pontosságának számtani középértéke.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "makra."|
precision_score_micro|Pontosság a százalékos aránya a következő címkét: egy adott osztály elemeit tartalmazza ténylegesen az adott osztály. A Micro kiszámításának alapja a teljes valódi pozitív és a hamis pozitív eredmény.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "micro"|
precision_score_weighted|Pontosság a százalékos aránya a következő címkét: egy adott osztály elemeit tartalmazza ténylegesen az adott osztály. A súlyozott érték az egyes osztályok pontosságának számtani középértéke, az egyes osztályokban lévő igaz példányok számával súlyozottan.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "súlyozott"|
recall_score_macro|Visszaírási valójában egy bizonyos osztály elemeinek megfelelően van-e jelölve, hogy százaléka. A makró az egyes osztályok visszahívásának számtani középértéke.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "makra."|
recall_score_micro|Visszaírási valójában egy bizonyos osztály elemeinek megfelelően van-e jelölve, hogy százaléka. A Micro kiszámításának alapja a teljes valódi pozitív, a hamis negatív és a téves pozitív érték megszámlálása|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "micro"|
recall_score_weighted|Visszaírási valójában egy bizonyos osztály elemeinek megfelelően van-e jelölve, hogy százaléka. A súlyozott érték az egyes osztályok visszahívásának számtani középértéke, az egyes osztályokban lévő igaz példányok számával súlyozottan.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "súlyozott"|
weighted_accuracy|A súlyozott pontosság az a pontosság, amelyben az egyes példákban megadott súlyozás egyenlő az adott példában szereplő igaz osztályokba tartozó valódi példányok arányával.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight egy adott osztály az egyes elemekhez az időarány, amíg a célzott egyenlő vektor|

### <a name="confusion-matrix"></a>Keveredési mátrix

Egy keveredési mátrixot egy osztályozási modell teljesítményét leírására szolgál. Minden egyes sor jeleníti meg az IGAZ osztály példányait, és minden oszlop felel meg a példányok, az előrejelzett osztály. A keveredési mátrix megfelelően osztályozott címkéket és a egy adott modell hibásan besorolt címkéit jeleníti meg.

Az Azure Machine Learning osztályozási problémák, automatikusan biztosít mindegyik modellt, amely egy keveredési mátrixot. Az automatikus ML minden egyes összekeveredési mátrix esetében megjeleníti az egyes előre jelzett címkék gyakoriságát és az egyes igaz címkék metszetét. Minél sötétebb a szín, annál nagyobb számnak kell lennie a mátrix adott részében. Ideális esetben a legsötétebb színek a mátrix átlója. 

1\. példa: Gyenge pontosságú besorolási modell ![gyenge pontossággal](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

2\. példa Nagy pontossággal (ideális) ![rendelkező besorolási modell nagy pontossággal](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Pontosság-visszahívási diagram

Az ezen a diagramon hasonlítsa össze a pontosság-visszahívási görbék egyes modellekre meghatározni, mely a modellnek van egy elfogadható pontosság és a visszaírási az adott üzleti probléma közötti kapcsolat. A diagram bemutatja a makró átlagos pontosság-visszahívási, a Micro átlagos pontosság-visszahívási és a egy modell minden osztály társított pontosság-visszahívási.

A pontosság jelöli, hogy arra, hogy az összes példány megfelelően címkézése besorolás kifejezés. Visszaírási arra, hogy az összes példányát egy adott címkét az osztályozó által igénybe vett jelöli. A pontosság-visszahívási görbe két ezek a fogalmak közötti kapcsolatot mutatja. Ideális esetben a modell kellene pontosság 100 %-os és 100 %-os pontossággal.

1\. példa: Alacsony pontosságú és ![alacsony szintű visszahívásos besorolási modellt tartalmazó besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

2\. példa Besorolási modell ~ 100% pontossággal és ~ 100% visszahívás (ideális) ![a besorolási modell nagy pontossággal és visszahívás](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

Jellemző (vagy ROC) működő fogadó egy diagram, a megfelelő osztályozott címkék és a egy adott modell hibásan besorolt címkéit. ROC-görbe kevesebb adatot tartalmazó lehet, amikor nagy eltérés, mivel az adatkészletek képzési modellek nem jelenik meg a hamis pozitív címkéket.

1\. példa: Alacsony ![igaz címkéket és magas hamis címkéket tartalmazó besorolási modell alacsony igaz címkékkel és magas hamis címkékkel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

2\. példa Egy magas igaz címkével és alacsony hamis ![címkékkel rendelkező besorolási modell magas igaz címkékkel és alacsony hamis címkékkel rendelkező besorolási modellel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Átemelés görbévé

Összehasonlíthatja a felvonó, a létrehozott modell automatikusan az Azure Machine Learning az alaptervhez annak érdekében, hogy az érték nyer, hogy az adott modell megtekintéséhez.

Lift diagramok segítségével egy osztályozási modell teljesítményének értékeléséhez. Megmutatja, hogyan sokkal jobban várható, hogy a modell egy modell nélkül képest. 

1\. példa: A modell rosszabb, mint egy véletlenszerű kiválasztási ![modell, amely rosszabb, mint egy véletlenszerű kiválasztási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

2\. példa A modell jobb, mint egy véletlenszerű kiválasztási ![modell, amely jobb teljesítményű besorolási modellt végez](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Nyereség görbévé

Az adatok minden egyes része egy osztályozási modell teljesítményét értékeli ki a nyereséget diagram. Az egyes PERCENTILIS mennyi jobban várható végrehajtásához egy véletlenszerű kijelölés modell összehasonlítja az adatkészlet jeleníti meg.

A halmozott nyereség diagram segítségével válassza ki a besorolást megszakítási használatával, amely megfelel a modellből a kívánt nyereség százalékban. Ezt az információt biztosít egy másik módszer a megnézzük a kísérő növekedési diagramon az eredményeket.

1\. példa: Egy besorolási modell minimális ![nyereséggel rendelkező besorolási modellel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

2\. példa Besorolási modell ![jelentős nyereséggel rendelkező besorolási modellel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Hitelesítési diagram

Az összes besorolási kapcsolatos problémák esetén a hitelesítési sor micro – átlag, a makró-átlagos és a egy adott prediktív modellt az egyes osztályok tekintheti meg. 

Hitelesítési rajzot a prediktív modellek magabiztosan megjelenítésére szolgál. Ezt nem: Megjeleníti az előre jelzett valószínűség és a tényleges valószínűsége közötti kapcsolat, ahol "valószínűség" jelenti a valószínűsége, hogy az adott példány néhány címke alatt tartozik. Egy jól hitelesített modell igazítja az y = x sor, ahol a szolgáltatás ésszerűen abban az előrejelzéseket. Egy túlzott confident modell igazítja az y = 0 sort, amelyben az előre jelzett valószínűség megtalálható, de nem tényleges valószínűsége.

1\. példa: Egy jól kalibrált modell ![ jobban kalibrált modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

2\. példa Egy több mint ![magabiztos modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Regressziós eredmények

A következő mérőszámok és diagramok érhetők el minden olyan regressziós modellhez, amelyet a Azure Machine Learning automatizált gépi tanulási képességeivel épít fel.

+ [Metrikák](#reg-metrics)
+ [Előre jelzett vs. Igaz](#pvt)
+ [Például hisztogramja](#histo)


### <a name="reg-metrics"></a>Regressziós metrikák

A rendszer a következő metrikákat menti a regressziós vagy előrejelzési feladatokhoz minden futtatási iteráció során.

|Metrika|Leírás|Számítás|További paraméterek
--|--|--|--|
explained_variance|MAGYARÁZAT eltérés az időarány, amíg, amelyhez a modell matematikai fiókok számára egy adott adatkészlet változata. A százalékos varianciát a varianciát a hibák az eredeti adatok csökkenése. Az átlag, a hibák értéke 0, esetén egyenlő magyarázat eltérés.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score|R2 meghatározása vagy a képest egy alapkonfiguráció modellt, amely a mean squared hibák százalékos csökkenését a relatív. |[Számítás](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|Valamennyi korrelációs a két adatkészlet között fennálló monotonicity nonparametric mértékegysége. A Pearson-korrelációs eltérően a valamennyi korrelációs nem feltételezi azt, hogy mindkét olyan adatkészlettel általában oszlanak meg. Más korrelációs együttható, például az egyik platformjától függően -1 és + 1 nincs korreláció úgy 0-val. -1 és + 1 összefüggéseket egy pontos monoton kapcsolatot jelenti. Pozitív összefüggéseket jelenti azt, hogy x növekszik, hogy mit y. Negatív összefüggéseket jelenti azt, hogy növeli az x y csökken.|[Számítás](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|Jelenti azt, hogy abszolút hiba a várt értékkel, az abszolút értékét a cél- és az előrejelzési közötti különbség|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error|Normalizált átlagos abszolút hiba osztva az adatok tartományán mean Absolute Error|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Az adatok számos nullával|
median_absolute_error|Közepes abszolút hiba a cél- és az előrejelzési közötti összes abszolút eltérések középértékének. Ezen adatveszteség robusztus a kiugró értékeket.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|Normalizált medián abszolút hiba osztva az adatok tartományán medián abszolút hiba|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Az adatok számos nullával|
root_mean_squared_error|Root mean squared hiba a cél- és az előrejelzési várt eltéréseinek négyzetgyökét|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error|Normalizált legfelső szintű mean squared hiba root mean squared hiba osztva az adatok tartományán:|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Az adatok számos nullával|
root_mean_squared_log_error|Legfelső szintű mean squared log hiba a várt squared logaritmikus hiba négyzetgyökét|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error|Normalizált legfelső szintű, négyzetes naplózási hiba: legfelső szintű négyzetes naplózási hiba az adattartomány szerint elosztva|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Az adatok számos nullával|

### <a name="pvt"></a>Előre jelzett vs. True

Előre jelzett vs. Igaz előre jelzett érték és a egy regressziós probléma a correlating IGAZ érték közötti kapcsolatot mutatja. Ez a diagram segítségével mérheti a modell teljesítményét, minél közelebb az y = x sor az előre jelzett értékek a következők, annál jobb a prediktív modell pontosságát.

Minden egyes futás után megjelenik egy előre jelzett és minden egyes regressziós modell igaz grafikon. Adatok védelme érdekében értékek együtt vannak binned, és a egy oszlopdiagram a diagramterület alsó részén jelenik meg minden doboz méretét. Hiba történt a margók, ahol a modell lehet ideális megoldás értékkel megjelenítő világosabb terület árnyékolása a össze lehessen hasonlítani a prediktív modellben.

1\. példa: Alacsony pontosságú regressziós modell előrejelzése ![egy regressziós modellt, amely alacsony pontossággal rendelkezik a jóslatokban](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

2\. példa Egy regressziós modell, amely nagy pontossággal [ ![van ellátva az előrejelzések szerint a regressziós modellben nagy pontossággal](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Maradványok hisztogramja

A fennmaradó megfigyelt y – az előre jelzett y jelöli. A hibahatáron az alacsony eltérés megjelenítéséhez, például a hisztogram kell alakúak lehetnek harang görbe 0 Eszközkezelőre. 

1\. példa: Egy regressziós modell, amely torzítást ![tartalmaz a hibákhoz tartozó SA regressziós modellben](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

2\. példa Egy regressziós modell, amely még nagyobb mennyiségű hibát ![tartalmaz, egy regressziós modell a hibák még összetettebb eloszlásával](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Modell-értelmező és-funkció fontossága

A funkció fontossága révén megtekintheti, hogy az egyes szolgáltatások milyen értékesek a modell kialakításában. Ez a számítás alapértelmezés szerint ki van kapcsolva, mivel jelentősen növelheti a futási időt.   Az összes modellhez engedélyezheti a modell magyarázatát, vagy csak a legjobban illeszkedő modellt magyarázza el.

A funkció fontos pontszám modell átfogó, valamint egy prediktív modellt az osztály egy tekintheti meg. Szolgáltatás / láthatja, hogyan viszonyul fontosságát, minden egyes osztály ellen, és teljes.

![A szolgáltatás magyarázat képessége](./media/how-to-understand-automated-ml/feature-importance.gif)

A tolmácsolási funkciók engedélyezésével kapcsolatos további információkért lásd: [AUTOMATIZÁLT ml-kísérletek konfigurálása a Pythonban](how-to-configure-auto-train.md#explain-the-model-interpretability).  A legjobb modellt bemutató példát a [legjobb modell magyarázata](how-to-auto-train-remote.md#explain)című témakörben talál.

## <a name="next-steps"></a>További lépések

+ További információ a Azure Machine Learning [automatizált ml](concept-automated-ml.md) -ről.
+ Próbálja ki az [automatizált Machine learning Model magyarázat](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) minta notebookját.
