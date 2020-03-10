---
title: Az automatizált ML-eredmények ismertetése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan tekintheti meg és értelmezheti a diagramokat és mérőszámokat az egyes automatizált gépi tanulási futtatásokhoz.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 69cf79f8258f85f2fb5e787f91aa843837d0a3a1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393351"
---
# <a name="understand-automated-machine-learning-results"></a>Az automatizált gépi tanulás eredményeinek megismerése
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

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

   ![Kísérlet menü képernyőképe](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. A kísérletek listájában válassza ki a felderíteni kívánt elemet.

   [![-kísérletek listája](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Az alsó táblázatban válassza a **Futtatás**lehetőséget.

   [![kísérlet futtatása](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. A modellek területen válassza ki a modellhez használni kívánt **algoritmus nevét** .

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

Metrika|Leírás|Számítás|További paraméterek
--|--|--|--
AUC_Macro| AUC az a terület, a fogadó működő jellemző görbe alatt. Makró minden egyes osztály a AUC számtani középértékét.  | [Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlagos = "makra."|
AUC_Micro| AUC az a terület, a fogadó működő jellemző görbe alatt. A Micro kiszámításának alapja az egyes osztályok valódi pozitív és hamis pozitív kombinációja.| [Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlagos = "micro"|
AUC_Weighted  | AUC az a terület, a fogadó működő jellemző görbe alatt. A súlyozott érték az egyes osztályok pontszámának számtani középértéke, amelyet az egyes osztályokban lévő igaz példányok száma alapján kell súlyozni.| [Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|átlagos = "súlyozott"
accuracy|Pontosság pontosan egyezik a valódi címkéket előre jelzett címkékhez százaléka. |[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nincs|
average_precision_score_macro|Átlagos pontosság pontosság-visszahívási görbe elért minden egyes küszöbértéket, a korábbi küszöbértéket, a súly használt fogyasztóktól növekedés az szükséges, a súlyozott átlag foglalja össze. A makró az egyes osztályok átlagos pontossági pontszámának számtani középértéke.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlagos = "makra."|
average_precision_score_micro|Átlagos pontosság pontosság-visszahívási görbe elért minden egyes küszöbértéket, a korábbi küszöbértéket, a súly használt fogyasztóktól növekedés az szükséges, a súlyozott átlag foglalja össze. A Micro kiszámításának alapja a valódi pozitív és a hamis pozitív érték összevonása minden egyes levágáskor.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlagos = "micro"|
average_precision_score_weighted|Átlagos pontosság pontosság-visszahívási görbe elért minden egyes küszöbértéket, a korábbi küszöbértéket, a súly használt fogyasztóktól növekedés az szükséges, a súlyozott átlag foglalja össze. A súlyozott érték az egyes osztályok átlagos pontossági pontszámának számtani középértéke, amelyet az egyes osztályokban lévő igaz példányok száma alapján kell súlyozni.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlagos = "súlyozott"|
balanced_accuracy|Elosztott terhelésű pontosságát az egyes osztályok visszaírási számtani középértékét.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "makra."|
f1_score_macro|F1 pontszám: közepének pontosság és a visszahívás. A makró az F1 pontszám számtani középértéke az egyes osztályokhoz.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlagos = "makra."|
f1_score_micro|F1 pontszám: közepének pontosság és a visszahívás. A Micro kiszámításának alapja a teljes valódi pozitív, a hamis negatív és a téves pozitív érték számítása.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlagos = "micro"|
f1_score_weighted|F1 pontszám: közepének pontosság és a visszahívás. Súlyozott átlag által az egyes osztályok F1 pontszám osztály gyakorisága|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlagos = "súlyozott"|
log_loss|Ez az a veszteség függvény a logisztikai regressziós (multinomial) és -bővítmények, például a Neurális hálózatokat definiálva a valószínűségi besorolás előrejelzéseket megadott igaz címkék negatív log valószínűségét, használt. Egyetlen olyan minta esetében, amelynél az {0,1} és a következő a valószínűsége: a YT = 1, a log P (YT&#124;YP) =-(YT-napló (YP) + (1-YT) napló (1-YP)|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nincs|
norm_macro_recall|Normalizált makró visszahívása makró ne felejtse el, hogy a véletlenszerű teljesítmény 0 pontszámot pedig tökéletes teljesítmény 1 pontszámot normalized. Ezt a norm_macro_recall: = (recall_score_macro-R)/(1-R) értékekkel érheti el, ahol az R az recall_score_macro várt értéke a véletlenszerű előrejelzések esetében (pl. R = 0,5 a bináris besoroláshoz és R = (1/C) a C osztályú besorolási problémákhoz).|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "makró" |
precision_score_macro|A pontosság a megfelelő címkével ellátott, pozitívan megjósolt elemek százaléka. A makró az egyes osztályok pontosságának számtani középértéke.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "makra."|
precision_score_micro|A pontosság a megfelelő címkével ellátott, pozitívan megjósolt elemek százaléka. A Micro kiszámításának alapja a teljes valódi pozitív és a hamis pozitív eredmény.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "micro"|
precision_score_weighted|A pontosság a megfelelő címkével ellátott, pozitívan megjósolt elemek százaléka. A súlyozott érték az egyes osztályok pontosságának számtani középértéke, az egyes osztályokban lévő igaz példányok számával súlyozottan.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "súlyozott"|
recall_score_macro|A visszahívás egy adott osztály megfelelően címkézett elemeinek százaléka. A makró az egyes osztályok visszahívásának számtani középértéke.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "makra."|
recall_score_micro|A visszahívás egy adott osztály megfelelően címkézett elemeinek százaléka. A Micro kiszámításának alapja a teljes valódi pozitív, a hamis negatív és a téves pozitív érték megszámlálása|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "micro"|
recall_score_weighted|A visszahívás egy adott osztály megfelelően címkézett elemeinek százaléka. A súlyozott érték az egyes osztályok visszahívásának számtani középértéke, az egyes osztályokban lévő igaz példányok számával súlyozottan.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "súlyozott"|
weighted_accuracy|A súlyozott pontosság az a pontosság, amelyben az egyes példákban megadott súlyozás egyenlő az adott példában szereplő igaz osztályokba tartozó valódi példányok arányával.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight egy adott osztály az egyes elemekhez az időarány, amíg a célzott egyenlő vektor|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Keveredési mátrix

#### <a name="what-is-a-confusion-matrix"></a>Mi az a zűrzavaros mátrix?
Egy keveredési mátrixot egy osztályozási modell teljesítményét leírására szolgál. Minden sor megjeleníti az adott adatkészlet igaz vagy tényleges osztályának példányait, és minden oszlop az osztály azon példányaira vonatkozik, amelyeket a modell előre jelzett. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Mit tesz az automatikus ML a zavartsági mátrixmal?
Az Azure Machine Learning osztályozási problémák, automatikusan biztosít mindegyik modellt, amely egy keveredési mátrixot. Az automatikus ML minden egyes zavart mátrix esetében megjeleníti az összes előre jelzett címke (oszlop) gyakoriságát, összehasonlítva az igaz címkével (sor). Minél sötétebb a szín, annál nagyobb számnak kell lennie a mátrix adott részében. 

#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
Összehasonlítjuk az adatkészlet tényleges értékét a modell által megadott előre jelzett értékekkel. Emiatt a gépi tanulási modellek nagyobb pontossággal rendelkeznek, ha a modellben a legtöbb érték az átló mentén van, ami azt jelenti, hogy a modell a helyes értéket jelezte. Ha a modellnek van osztály-egyensúlyhiánya, a zavart mátrix segít felderíteni egy elfogult modellt.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>1\. példa: gyenge pontosságú besorolási modell
![Gyenge pontosságú besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>2\. példa: nagy pontosságú besorolási modell 
![Nagy pontosságú besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>3\. példa: egy nagy pontosságú és nagy mértékű torzulást biztosító besorolási modell a modell-előrejelzésekben
![Nagy pontosságú és nagy mértékű torzulást biztosító besorolási modell a modell-előrejelzésekben](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Pontosság-visszahívási diagram
#### <a name="what-is-a-precision-recall-chart"></a>Mi az a precíziós visszahívás diagram?
A precíziós visszahívás görbe a modellből származó pontosság és visszahívás közötti kapcsolatot mutatja. A pontosság kifejezés azt jelenti, hogy a modell képes az összes példány megfelelő címkézésére. Visszaírási arra, hogy az összes példányát egy adott címkét az osztályozó által igénybe vett jelöli.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Mit tesz az automatizált ML a precíziós visszahívás diagrammal?

Az ezen a diagramon hasonlítsa össze a pontosság-visszahívási görbék egyes modellekre meghatározni, mely a modellnek van egy elfogadható pontosság és a visszaírási az adott üzleti probléma közötti kapcsolat. A diagram bemutatja a makró átlagos pontosság-visszahívási, a Micro átlagos pontosság-visszahívási és a egy modell minden osztály társított pontosság-visszahívási. 

A Macro-átlag minden osztálytól függetlenül kiszámítja a metrikát, majd az átlagot, és az összes osztályt egyformán kezeli. A mikro-átlag azonban összesíti az összes osztály hozzájárulását az átlag számításához. A mikro-átlag előnyben részesített, ha az adatkészletben van osztálybeli egyensúlyhiány.

#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
Az üzleti probléma céljától függően az ideális precíziós visszahívás görbe eltérő lehet. Néhány példát alább találhat

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>1\. példa: alacsony pontosságú és alacsony visszahívású besorolási modell
![Alacsony precizitású és alacsony visszahívású besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>2\. példa: besorolási modell ~ 100% pontossággal és ~ 100% visszahívás 
![A besorolási modellt nagy pontossággal és visszahívás](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC-diagram

#### <a name="what-is-a-roc-chart"></a>Mi az a ROC-diagram?
Jellemző (vagy ROC) működő fogadó egy diagram, a megfelelő osztályozott címkék és a egy adott modell hibásan besorolt címkéit. ROC-görbe kevesebb adatot tartalmazó lehet, amikor nagy eltérés, mivel az adatkészletek képzési modellek nem jelenik meg a hamis pozitív címkéket.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Mit tesz az automatizált ML a ROC-diagrammal?
Az automatizált ML a makrók átlagos pontosságát – visszahívás, a mikro-átlag pontossága – visszahívás, valamint a modell összes osztályához társított pontossági visszahívás. 

A Macro-átlag minden osztálytól függetlenül kiszámítja a metrikát, majd az átlagot, és az összes osztályt egyformán kezeli. A mikro-átlag azonban összesíti az összes osztály hozzájárulását az átlag számításához. A mikro-átlag előnyben részesített, ha az adatkészletben van osztálybeli egyensúlyhiány.

#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
Ideális esetben a modell a 100%-os valódi pozitív arányt és a 0%-os hamis pozitív arányt közelíti meg. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>1\. példa: alacsony igaz címkékkel és magas hamis címkékkel rendelkező besorolási modell
![Besorolási modell alacsony igaz címkékkel és magas hamis címkékkel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>2\. példa: magas igaz címkéket és alacsony hamis címkéket tartalmazó besorolási modell
magas igaz címkéket és alacsony hamis címkéket tartalmazó besorolási modellt ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Diagram emelése
#### <a name="what-is-a-lift-chart"></a>Mi az a lift-diagram?
Lift diagramok segítségével egy osztályozási modell teljesítményének értékeléséhez. Ez azt mutatja, hogy mennyit érdemes a generált modellhez képest a modell pontossága nélkül.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Mit jelent az automatizált ML a felvonó diagrammal?
Összehasonlíthatja a felvonó, a létrehozott modell automatikusan az Azure Machine Learning az alaptervhez annak érdekében, hogy az érték nyer, hogy az adott modell megtekintéséhez.
#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>1\. példa: egy véletlenszerű kiválasztási modellnél rosszabb besorolási modell
![Egy véletlenszerű kiválasztási modellnél rosszabb besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>2\. példa: egy véletlenszerű kiválasztási modellnél jobb teljesítményű besorolási modell
![A jobb](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
t végző besorolási modellt<a name="gains-curve"></a>
### <a name="gains-chart"></a>Adatnyereség diagramja
#### <a name="what-is-a-gains-chart"></a>Mi az a nyereség diagram?

Az adatok minden egyes része egy osztályozási modell teljesítményét értékeli ki a nyereséget diagram. Az egyes PERCENTILIS mennyi jobban várható végrehajtásához egy véletlenszerű kijelölés modell összehasonlítja az adatkészlet jeleníti meg.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Mit tesz az automatizált ML a nyereség diagrammal?
A halmozott nyereség diagram segítségével válassza ki a besorolást megszakítási használatával, amely megfelel a modellből a kívánt nyereség százalékban. Ezt az információt biztosít egy másik módszer a megnézzük a kísérő növekedési diagramon az eredményeket.

#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>1\. példa: osztályozási modell minimális nyereséggel
![besorolási modell minimális nyereséggel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>2\. példa: jelentős nyereségű besorolási modell
jelentős nyereségű besorolási modellt ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Kalibrációs diagram

#### <a name="what-is-a-calibration-chart"></a>Mi az a kalibrációs diagram?
Hitelesítési rajzot a prediktív modellek magabiztosan megjelenítésére szolgál. Ezt nem: Megjeleníti az előre jelzett valószínűség és a tényleges valószínűsége közötti kapcsolat, ahol "valószínűség" jelenti a valószínűsége, hogy az adott példány néhány címke alatt tartozik.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Mit tesz a kalibrációs diagramon az automatikus ML?
Az összes besorolási kapcsolatos problémák esetén a hitelesítési sor micro – átlag, a makró-átlagos és a egy adott prediktív modellt az egyes osztályok tekintheti meg.

A Macro-átlag minden osztálytól függetlenül kiszámítja a metrikát, majd az átlagot, és az összes osztályt egyformán kezeli. A mikro-átlag azonban összesíti az összes osztály hozzájárulását az átlag számításához. 
#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
 A jól kalibrált modell az y = x sorral összhangban van, ahol az előrejelzések szerint ésszerűen magabiztos. Egy túlzott confident modell igazítja az y = 0 sort, amelyben az előre jelzett valószínűség megtalálható, de nem tényleges valószínűsége. 


##### <a name="example-1-a-well-calibrated-model"></a>1\. példa: egy jól kalibrált modell
![ több jól kalibrált modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>2\. példa: egy magabiztos modell
![Egy több mint magabiztos modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Regressziós eredmények

A következő mérőszámok és diagramok érhetők el minden olyan regressziós modellhez, amelyet a Azure Machine Learning automatizált gépi tanulási képességeivel épít fel.

+ [Metrikák](#reg-metrics)
+ [Előre jelzett vagy igaz](#pvt)
+ [Maradványok hisztogramja](#histo)


### <a name="reg-metrics"></a>Regressziós metrikák

A rendszer a következő metrikákat menti a regressziós vagy előrejelzési feladatokhoz minden futtatási iteráció során.

|Metrika|Leírás|Számítás|További paraméterek
--|--|--|--|
explained_variance|MAGYARÁZAT eltérés az időarány, amíg, amelyhez a modell matematikai fiókok számára egy adott adatkészlet változata. A százalékos varianciát a varianciát a hibák az eredeti adatok csökkenése. Az átlag, a hibák értéke 0, esetén egyenlő magyarázat eltérés.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nincs|
r2_score|R2 meghatározása vagy a képest egy alapkonfiguráció modellt, amely a mean squared hibák százalékos csökkenését a relatív. |[Kiszámítása](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nincs|
spearman_correlation|Valamennyi korrelációs a két adatkészlet között fennálló monotonicity nonparametric mértékegysége. A Pearson-korrelációs eltérően a valamennyi korrelációs nem feltételezi azt, hogy mindkét olyan adatkészlettel általában oszlanak meg. Más korrelációs együttható, például az egyik platformjától függően -1 és + 1 nincs korreláció úgy 0-val. -1 és + 1 összefüggéseket egy pontos monoton kapcsolatot jelenti. Pozitív összefüggéseket jelenti azt, hogy x növekszik, hogy mit y. Negatív összefüggéseket jelenti azt, hogy növeli az x y csökken.|[Kiszámítása](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nincs|
mean_absolute_error|Jelenti azt, hogy abszolút hiba a várt értékkel, az abszolút értékét a cél- és az előrejelzési közötti különbség|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nincs|
normalized_mean_absolute_error|Normalizált átlagos abszolút hiba osztva az adatok tartományán mean Absolute Error|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Az adatok számos nullával|
median_absolute_error|Közepes abszolút hiba a cél- és az előrejelzési közötti összes abszolút eltérések középértékének. Ezen adatveszteség robusztus a kiugró értékeket.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nincs|
normalized_median_absolute_error|Normalizált medián abszolút hiba osztva az adatok tartományán medián abszolút hiba|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Az adatok számos nullával|
root_mean_squared_error|Root mean squared hiba a cél- és az előrejelzési várt eltéréseinek négyzetgyökét|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nincs|
normalized_root_mean_squared_error|Normalizált legfelső szintű mean squared hiba root mean squared hiba osztva az adatok tartományán:|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Az adatok számos nullával|
root_mean_squared_log_error|Legfelső szintű mean squared log hiba a várt squared logaritmikus hiba négyzetgyökét|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nincs|
normalized_root_mean_squared_log_error|Normalizált legfelső szintű, négyzetes naplózási hiba: legfelső szintű négyzetes naplózási hiba az adattartomány szerint elosztva|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Az adatok számos nullával|

### <a name="pvt"></a>Előre jelzett és igaz diagram
#### <a name="what-is-a-predicted-vs-true-chart"></a>Mi az az előre jelzett vagy igaz diagram?
Az előre jelzett és az igaz érték azt mutatja, hogy egy regressziós probléma esetén az előre jelzett érték és a korrelációs valódi értéke közötti kapcsolat látható. Ez a diagram segítségével mérheti a modell teljesítményét, minél közelebb az y = x sor az előre jelzett értékek a következők, annál jobb a prediktív modell pontosságát.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Mit tesz az automatikus ML az előre jelzett és az igaz diagrammal?
Minden egyes futás után megjelenik egy előre jelzett és minden egyes regressziós modell igaz grafikon. Adatok védelme érdekében értékek együtt vannak binned, és a egy oszlopdiagram a diagramterület alsó részén jelenik meg minden doboz méretét. Hiba történt a margók, ahol a modell lehet ideális megoldás értékkel megjelenítő világosabb terület árnyékolása a össze lehessen hasonlítani a prediktív modellben.

#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>1\. példa: kis pontosságú besorolási modell
![Regressziós modell alacsony pontossággal a jóslatokban](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>2\. példa: nagy pontosságú regressziós modell 
[az előrejelzések szerint nagy pontossággal ![regressziós modellt](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Maradványok diagramjának hisztogramja
#### <a name="what-is-a-residuals-chart"></a>Mi az a fennmaradó diagram?
A fennmaradó megfigyelt y – az előre jelzett y jelöli. A hibahatáron az alacsony eltérés megjelenítéséhez, például a hisztogram kell alakúak lehetnek harang görbe 0 Eszközkezelőre. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Mit jelent az automatizált ML a fennmaradó diagrammal?
Az automatikus ML automatikusan megjelenít egy fennmaradó diagramot, amely megjeleníti a hibák eloszlását a jóslatokban.
#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
A jó modell általában egy harang-görbét vagy egy nulla körüli hibát tartalmaz.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>1\. példa: egy regressziós modell, amely torzulást okoz a hibáknál
![SA regressziós modell a hibák torzításával](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>2\. példa: egy regressziós modell a hibák egyenletes eloszlásával
![Egy regressziós modell, amely a hibák egyenletes eloszlásával rendelkezik](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Modell-értelmező és-funkció fontossága
Az automatikus ML gépi tanulásra vonatkozó értelmező irányítópultot biztosít a futtatásához.
A tolmácsolási funkciók engedélyezésével kapcsolatos további információkért tekintse [meg az AUTOMATIZÁLT](how-to-machine-learning-interpretability-automl.md) ml-kísérletek értelmezésének engedélyezése című témakört.

## <a name="next-steps"></a>Következő lépések

+ További információ a Azure Machine Learning [automatizált ml](concept-automated-ml.md) -ről.
+ Próbálja ki az [automatizált Machine learning Model magyarázat](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) minta jegyzetfüzeteket.
