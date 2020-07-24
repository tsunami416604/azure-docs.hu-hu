---
title: Az automatizált ML-eredmények ismertetése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan tekintheti meg és értelmezheti a diagramokat és mérőszámokat az egyes automatizált gépi tanulási futtatásokhoz.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 12/05/2019
ms.openlocfilehash: 18addfc6b7a0002aba26b668481d6bedb612fffc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090348"
---
# <a name="understand-automated-machine-learning-results"></a>Az automatizált gépi tanulási eredmények értelmezése
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan tekintheti meg és értelmezheti az egyes automatizált gépi tanulási folyamatokhoz tartozó diagramokat és mérőszámokat. 

További információk:
+ [Mérőszámok és diagramok besorolási modellekhez](#classification)
+ [Metrikák és diagramok a regressziós modellekhez](#regression)
+ [Modell-értelmező és-funkció fontossága](#explain-model)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot a feladatok megkezdése előtt. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Hozzon létre egy kísérletet az automatizált gépi tanuláshoz, vagy az SDK-val vagy a Azure Machine Learning Studióban.

    * Az SDK használata [besorolási modell](how-to-auto-train-remote.md) vagy [regressziós modell](tutorial-auto-train-models.md) létrehozásához
    * A megfelelő adatfeltöltés használatával hozzon létre egy besorolási vagy regressziós modellt a [Azure Machine learning Studióval](how-to-use-automated-ml-for-ml-models.md) .

## <a name="view-the-run"></a>A Futtatás megtekintése

Az automatizált gépi tanulási kísérlet futtatása után a futtatások előzményei a Machine learning-munkaterületen találhatók. 

1. Lépjen a munkaterülethez.

1. A munkaterület bal oldali paneljén válassza a **kísérletek**lehetőséget.

   ![A kísérlet menü képernyőképe](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. A kísérletek listájában válassza ki a felderíteni kívánt elemet.

   [![Kísérletek listája](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Az alsó táblázatban válassza a **Futtatás**lehetőséget.

   [ ![ Kísérlet futtatása](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. A modellek területen válassza ki a modellhez használni kívánt **algoritmus nevét** .

   [![Kísérleti modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Ugyanezeket az eredményeket is láthatja a Futtatás során, amikor a `RunDetails` [Jupyter widgetet](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)használja.

## <a name="classification-results"></a><a name="classification"></a>Besorolási eredmények

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
AUC_macro| A AUC a fogadó operációs karakterisztika görbe alatti terület. A makró az egyes osztályok AUC számtani középértéke.  | [Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlag = "makró"|
AUC_micro| A AUC a fogadó operációs karakterisztika görbe alatti terület. A Micro kiszámításának alapja az egyes osztályok valódi pozitív és hamis pozitív kombinációja.| [Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlag = "Micro"|
AUC_weighted  | A AUC a fogadó operációs karakterisztika görbe alatti terület. A súlyozott érték az egyes osztályok pontszámának számtani középértéke, amelyet az egyes osztályokban lévő igaz példányok száma alapján kell súlyozni.| [Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|átlag = "súlyozott"
accuracy|A pontosság azon előre jelzett címkék százaléka, amelyek pontosan megfelelnek az igaz feliratoknak. |[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nincs|
average_precision_score_macro|Az átlagos pontosság egy precíziós visszahívás görbét összegzi, amely az egyes küszöbértékekben elért pontosságok súlyozott középértékét jelenti, és a rendszer visszahívja az előző küszöbértéket, amelyet a súlyozáshoz használtak. A makró az egyes osztályok átlagos pontossági pontszámának számtani középértéke.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlag = "makró"|
average_precision_score_micro|Az átlagos pontosság egy precíziós visszahívás görbét összegzi, amely az egyes küszöbértékekben elért pontosságok súlyozott középértékét jelenti, és a rendszer visszahívja az előző küszöbértéket, amelyet a súlyozáshoz használtak. A Micro kiszámításának alapja a valódi pozitív és a hamis pozitív érték összevonása minden egyes levágáskor.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlag = "Micro"|
average_precision_score_weighted|Az átlagos pontosság egy precíziós visszahívás görbét összegzi, amely az egyes küszöbértékekben elért pontosságok súlyozott középértékét jelenti, és a rendszer visszahívja az előző küszöbértéket, amelyet a súlyozáshoz használtak. A súlyozott érték az egyes osztályok átlagos pontossági pontszámának számtani középértéke, amelyet az egyes osztályokban lévő igaz példányok száma alapján kell súlyozni.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlag = "súlyozott"|
balanced_accuracy|A kiegyensúlyozott pontosság az egyes osztályok visszahívásának számtani középértéke.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "makró"|
f1_score_macro|Az F1 pontszám a pontosság és a visszahívás harmonikus középértéke. A makró az F1 pontszám számtani középértéke az egyes osztályokhoz.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlag = "makró"|
f1_score_micro|Az F1 pontszám a pontosság és a visszahívás harmonikus középértéke. A Micro kiszámításának alapja a teljes valódi pozitív, a hamis negatív és a téves pozitív érték számítása.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlag = "Micro"|
f1_score_weighted|Az F1 pontszám a pontosság és a visszahívás harmonikus középértéke. Súlyozott középértékek az egyes osztályokhoz tartozó F1-pontszámok osztályának gyakorisága alapján|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlag = "súlyozott"|
log_loss|Ez a (MULTINOMIAL) logisztikai regresszió és bővítmények (például neurális hálózatok) által használt veszteséges függvény, amely negatív naplózási valószínűséggel van meghatározva az igaz címkék valószínűsége alapján, az osztályozó jóslatai. Egyetlen olyan minta esetében, amely a következővel rendelkezik, {0,1} és becsült valószínűsége: a YT = 1, a log P (yt&#124;YP) =-(YT-napló (YP) + (1-YT) naplója (1-YP)).|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nincs|
norm_macro_recall|A normalizált makrók felidézése a makró-visszahívás normalizált, így a véletlenszerű teljesítmény pontszáma 0, a tökéletes teljesítmény pedig 1. Ezt a norm_macro_recall: = (recall_score_macro-R)/(1-R) értékekkel érheti el, ahol az R az recall_score_macro várt értéke a véletlenszerű előrejelzések esetében (pl. R = 0,5 a bináris besoroláshoz és R = (1/C) a C osztályú besorolási problémákhoz).|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "makró" |
precision_score_macro|A pontosság a megfelelő címkével ellátott, pozitívan megjósolt elemek százaléka. A makró az egyes osztályok pontosságának számtani középértéke.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlag = "makró"|
precision_score_micro|A pontosság a megfelelő címkével ellátott, pozitívan megjósolt elemek százaléka. A Micro kiszámításának alapja a teljes valódi pozitív és a hamis pozitív eredmény.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlag = "Micro"|
precision_score_weighted|A pontosság a megfelelő címkével ellátott, pozitívan megjósolt elemek százaléka. A súlyozott érték az egyes osztályok pontosságának számtani középértéke, az egyes osztályokban lévő igaz példányok számával súlyozottan.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlag = "súlyozott"|
recall_score_macro|A visszahívás egy adott osztály megfelelően címkézett elemeinek százaléka. A makró az egyes osztályok visszahívásának számtani középértéke.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "makró"|
recall_score_micro|A visszahívás egy adott osztály megfelelően címkézett elemeinek százaléka. A Micro kiszámításának alapja a teljes valódi pozitív, a hamis negatív és a téves pozitív érték megszámlálása|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "Micro"|
recall_score_weighted|A visszahívás egy adott osztály megfelelően címkézett elemeinek százaléka. A súlyozott érték az egyes osztályok visszahívásának számtani középértéke, az egyes osztályokban lévő igaz példányok számával súlyozottan.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "súlyozott"|
weighted_accuracy|A súlyozott pontosság az a pontosság, amelyben az egyes példákban megadott súlyozás egyenlő az adott példában szereplő igaz osztályokba tartozó valódi példányok arányával.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|a sample_weight egy vektor, amely az adott osztálynak a cél egyes elemeinek arányával egyenlő.|

### <a name="binary-vs-multiclass-metrics"></a>Bináris és többosztályos metrikák

A AutoML nem tesz különbséget a bináris és a többosztályos metrikák között. Ugyanazokat az érvényesítési metrikákat kell jelenteni, hogy az adatkészlet két osztályból vagy kettőből áll-e. Néhány metrika azonban többosztályos besorolásra szolgál. Bináris adatkészletre alkalmazva ezek a mérőszámok nem kezelik osztályként az osztályt `true` , ahogy az várható. A többosztályos használatra szánt metrikák a, a vagy a utótaggal vannak ellátva `micro` `macro` `weighted` . Ilyenek például a következők:,,, `average_precision_score` `f1_score` `precision_score` `recall_score` és `AUC` .

Egy konkrét példa ezt a különbségtételt teszi lehetővé: a felidézés helyett `tp / (tp + fn)` a többosztályos átlagot ( `micro` , `macro` , vagy `weighted` ) átlagot a bináris besorolási adatkészlet mindkét osztályán. Ez egyenértékű az osztály és az osztály visszahívásának kiszámításával `true` `false` , majd a kettő átlagának megtételével.

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Zavart mátrix

#### <a name="what-is-a-confusion-matrix"></a>Mi az a zűrzavaros mátrix?
A rendszer a besorolási modell teljesítményének leírására használja a zűrzavaros mátrixot. Minden sor megjeleníti az adott adatkészlet igaz vagy tényleges osztályának példányait, és minden oszlop az osztály azon példányaira vonatkozik, amelyeket a modell előre jelzett. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Mit tesz az automatikus ML a zavartsági mátrixmal?
Besorolási problémák esetén Azure Machine Learning automatikusan egy összekeverhető mátrixot biztosít minden létrehozott modellhez. Az automatikus ML minden egyes zavart mátrix esetében megjeleníti az összes előre jelzett címke (oszlop) gyakoriságát, összehasonlítva az igaz címkével (sor). Minél sötétebb a szín, annál nagyobb számnak kell lennie a mátrix adott részében. 

#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
Összehasonlítjuk az adatkészlet tényleges értékét a modell által megadott előre jelzett értékekkel. Emiatt a gépi tanulási modellek nagyobb pontossággal rendelkeznek, ha a modellben a legtöbb érték az átló mentén van, ami azt jelenti, hogy a modell a helyes értéket jelezte. Ha a modellnek van osztály-egyensúlyhiánya, a zavart mátrix segít felderíteni egy elfogult modellt.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>1. példa: gyenge pontosságú besorolási modell
![Gyenge pontosságú besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>2. példa: nagy pontosságú besorolási modell 
![Nagy pontosságú besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>3. példa: egy nagy pontosságú és nagy mértékű torzulást biztosító besorolási modell a modell-előrejelzésekben
![Nagy pontosságú és nagy mértékű torzulást biztosító besorolási modell a modell-előrejelzésekben](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Precíziós visszahívási diagram
#### <a name="what-is-a-precision-recall-chart"></a>Mi az a precíziós visszahívás diagram?
A precíziós visszahívás görbe a modellből származó pontosság és visszahívás közötti kapcsolatot mutatja. A pontosság kifejezés azt jelenti, hogy a modell képes az összes példány megfelelő címkézésére. A visszahívás azt jelöli, hogy egy osztályozó képes-e egy adott címke összes példányának megkeresésére.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Mit tesz az automatizált ML a precíziós visszahívás diagrammal?

Ezzel a diagrammal összevetheti az egyes modellek pontossági felidézési görbéit, és meghatározhatja, hogy melyik modell elfogadható kapcsolata legyen a pontosság és az adott üzleti probléma felidézése között. Ebben a diagramban a makrók pontossága – visszahívás, a mikro-átlag pontossága – visszahívás, valamint a modell összes osztályához társított pontossági visszahívás látható. 

A Macro-átlag minden osztálytól függetlenül kiszámítja a metrikát, majd az átlagot, és az összes osztályt egyformán kezeli. A mikro-átlag azonban összesíti az összes osztály hozzájárulását az átlag számításához. A mikro-átlag előnyben részesített, ha az adatkészletben van osztálybeli egyensúlyhiány.

#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
Az üzleti probléma céljától függően az ideális precíziós visszahívás görbe eltérő lehet. Néhány példát alább találhat

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>1. példa: alacsony pontosságú és alacsony visszahívású besorolási modell
![Alacsony precizitású és alacsony visszahívású besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>2. példa: besorolási modell ~ 100% pontossággal és ~ 100% visszahívás 
![Besorolási modell nagy pontossággal és visszahívás](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC-diagram

#### <a name="what-is-a-roc-chart"></a>Mi az a ROC-diagram?
A fogadó működési jellemzője (vagy a ROC) a helyesen besorolt címkék és egy adott modell helytelen besorolású címkéi. A ROC-görbe kevésbé informatív lehet, ha magas osztályú egyensúlyhiánysal rendelkező adatkészleteken tanít modelleket, mivel a többségi osztály kiszoríthatja a kisebbségi osztályokból származó hozzájárulásokat.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Mit tesz az automatizált ML a ROC-diagrammal?
A ROC-diagram alatti terület a helyesen besorolt minták arányában jeleníthető meg. A ROC-diagram haladó felhasználója a görbe alatti terület fölé kerülhet, és a besorolási küszöb vagy a döntési határ függvényében a valódi pozitív és a hamis pozitív díjakra vonatkozó intuíciót kaphat.

#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
Egy ROC-görbe, amely a bal felső sarokban, 100%-os True pozitív arányban és 0%-os hamis pozitív arányban közelíti meg a legjobb modellt. Egy véletlenszerű modell a bal felső sarokban látható egyenes vonalként fog megjelenni. Rosszabb, mint az y = x vonal alatt Beúszás.

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>1. példa: alacsony igaz címkékkel és magas hamis címkékkel rendelkező besorolási modell
![Besorolási modell alacsony igaz címkékkel és magas hamis címkékkel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>2. példa: magas igaz címkéket és alacsony hamis címkéket tartalmazó besorolási modell
![magas igaz címkéket és alacsony hamis címkéket tartalmazó besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Diagram emelése
#### <a name="what-is-a-lift-chart"></a>Mi az a lift-diagram?
A kiemelési diagramok a besorolási modellek teljesítményének kiértékelésére szolgálnak. A felvonó diagram azt mutatja, hogy a modellek hányszor jobbak egy véletlenszerű modellhez képest. Ez viszonylagos teljesítményt nyújt, amely figyelembe veszi, hogy a besorolás nehezebben növekszik az osztályok számának növelésével. A véletlenszerű modell nem fogja előre jelezni a minták nagyobb hányadát egy olyan adatkészletből, amelynek tíz osztálya a két osztályt tartalmazó adatkészlethez hasonlít.

#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Mit jelent az automatizált ML a felvonó diagrammal?
Az adott modell értékének megszerzéséhez összehasonlíthatja a modell azon felvonóját, amelyet a rendszer automatikusan Azure Machine Learning az alaptervhez.
#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>1. példa: egy véletlenszerű kiválasztási modellnél rosszabb besorolási modell
![Egy véletlenszerű kiválasztási modellnél rosszabb besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>2. példa: egy véletlenszerű kiválasztási modellnél jobb teljesítményű besorolási modell
![Jobb teljesítményű besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="cumulative-gains-chart"></a>Halmozott nyereségek diagramja
#### <a name="what-is-a-cumulative-gains-chart"></a>Mi az összesítő nyereségű diagram?

Az összesítő nyereség diagram a besorolási modell teljesítményét értékeli ki az egyes adatrészeken. Az adathalmaz minden egyes százalékában a diagramon látható, hogy hány minta lett pontosan besorolva.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Mit tesz az automatizált ML a nyereség diagrammal?
A halmozott nyereség diagram segítségével kiválaszthatja a besorolást a modell kívánt nyereségének megfelelő százalékos arány használatával. Ez az információ egy másik módszert biztosít a kapcsolódó felvonó diagram eredményeinek megtekintésére.

#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>1. példa: osztályozási modell minimális nyereséggel
![besorolási modell minimális nyereséggel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>2. példa: jelentős nyereségű besorolási modell
![Jelentős nyereségű besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Kalibrációs diagram

#### <a name="what-is-a-calibration-chart"></a>Mi az a kalibrációs diagram?
Egy kalibrációs terület a prediktív modell megbízhatóságának megjelenítésére szolgál. Ez az előre jelzett valószínűség és a tényleges valószínűség közötti kapcsolat bemutatásával történik, ahol a "valószínűség" azt jelzi, hogy egy adott példány egy adott címkéhez tartozik-e.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Mit tesz a kalibrációs diagramon az automatikus ML?
Az összes besorolási probléma esetében áttekintheti a kalibrációs vonalat a mikro-átlag, a makro-átlag és az egyes osztályok számára egy adott prediktív modellben.

A Macro-átlag minden osztálytól függetlenül kiszámítja a metrikát, majd az átlagot, és az összes osztályt egyformán kezeli. A mikro-átlag azonban összesíti az összes osztály hozzájárulását az átlag számításához. 
#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
A jól kalibrált modell az y = x sorral összhangban van, ahol helyesen Jósolja meg, hogy a minták az egyes osztályokhoz tartoznak-e. A túlzottan nagy valószínűséggel megjósolható, hogy az egyes minták osztályának értéke nem lehet nulla és egy.


##### <a name="example-1-a-well-calibrated-model"></a>1. példa: egy jól kalibrált modell
![ több jól kalibrált modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>2. példa: egy magabiztos modell
![Egy több mint magabiztos modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>Regressziós eredmények

A következő mérőszámok és diagramok érhetők el minden olyan regressziós modellhez, amelyet a Azure Machine Learning automatizált gépi tanulási képességeivel épít fel.

+ [Metrikák](#reg-metrics)
+ [Előre jelzett vagy igaz](#pvt)
+ [Maradványok hisztogramja](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>Regressziós metrikák

A rendszer a következő metrikákat menti a regressziós vagy előrejelzési feladatokhoz minden futtatási iteráció során.

|Metrika|Leírás|Számítás|További paraméterek
--|--|--|--|
explained_variance|Az elmagyarázott variancia azt az arányt adja meg, amelynek a matematikai modellje egy adott adathalmaz variációját mutatja. Ez az eredeti és a hibák eltérésének százalékos csökkenése. Ha a hibák középértéke 0, akkor egyenlő a magyarázattal.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nincs|
r2_score|R2 a meghatározási együttható vagy a négyzetes hibák százalékos csökkenése a középértéket eredményező alapmodellhez képest. |[Kiszámítása](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nincs|
spearman_correlation|A lándzsás korreláció a két adathalmaz közötti kapcsolat monotonicity nem számszerű mérőszáma. A Pearson korrelációtól eltérően a lándzsás korreláció nem feltételezi, hogy mindkét adathalmazt általában kiosztják. Más korrelációs tényezőhöz hasonlóan ez az 1 és + 1 között változik, és a 0 érték nem jelent korrelációt. Az-1 vagy + 1 korrelációk pontos monoton kapcsolatot jelentenek. A pozitív korrelációk azt sugallják, hogy az x növekszik, így az y. A negatív korrelációk azt sugallják, hogy az x nő, az y pedig csökken.|[Kiszámítása](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nincs|
mean_absolute_error|Abszolút érték: a cél és az előrejelzés közötti különbség abszolút értéke a várt érték.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nincs|
normalized_mean_absolute_error|Normalizált Mean abszolút hiba: abszolút hiba, amelyet az adattartomány feloszt|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Osztás az adattartomány szerint|
median_absolute_error|A medián abszolút hiba a cél és az előrejelzés közötti összes abszolút különbség középértéke. Ez a veszteség robusztus a kiugró értékeknél.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nincs|
normalized_median_absolute_error|Normalizált medián abszolút hiba: az adattartományon belül az abszolút érték középértékének hányadosa.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Osztás az adattartomány szerint|
root_mean_squared_error|A legfelső szintű, négyzetes hiba a cél és az előrejelzés között a várt négyzetes különbség négyzetes gyökere.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nincs|
normalized_root_mean_squared_error|Normalizált legfelső szintű, négyzetes hiba: a legfelső szintű, négyzetes hiba, amely az adattartomány szerint oszlik el|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Osztás az adattartomány szerint|
root_mean_squared_log_error|A legfelső szintű négyzetes naplózási hiba a várt szögletes logaritmikus hiba négyzetének gyökere.|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nincs|
normalized_root_mean_squared_log_error|Normalizált legfelső szintű, négyzetes naplózási hiba: legfelső szintű négyzetes naplózási hiba az adattartomány szerint elosztva|[Kiszámítása](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Osztás az adattartomány szerint|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>Előre jelzett és igaz diagram
#### <a name="what-is-a-predicted-vs-true-chart"></a>Mi az az előre jelzett vagy igaz diagram?
Az előre jelzett és az igaz érték azt mutatja, hogy egy regressziós probléma esetén az előre jelzett érték és a korrelációs valódi értéke közötti kapcsolat látható. Ez a gráf felhasználható egy modell teljesítményének mérésére, ahogy az y = x vonal közelebb van az előre jelzett értékekhez, annál jobb a prediktív modell pontossága.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Mit tesz az automatikus ML az előre jelzett és az igaz diagrammal?
Az egyes futtatások után megtekintheti az egyes regressziós modellekhez tartozó, előre jelzett és igaz gráfot is. Az adatok védelme érdekében az értékek együtt dobozolni, és az egyes raktárhelyek mérete a diagramterület alsó részén oszlopdiagramként jelenik meg. A prediktív modellt összehasonlíthatja a világosabb árnyalattal, amely a hibák margóit mutatja, így a modellnek ideális értékkel kell rendelkeznie.

#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>1. példa: kis pontosságú besorolási modell
![Regressziós modell alacsony pontossággal a jóslatokban](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>2. példa: nagy pontosságú regressziós modell 
[![Regressziós modell nagy pontossággal az előrejelzések szerint](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>Maradványok diagramjának hisztogramja
#### <a name="what-is-a-residuals-chart"></a>Mi az a fennmaradó diagram?
A maradék az előrejelzés és a tényleges érték () közötti különbség `y_pred - y_true` . Ha alacsony torzítású hibát szeretne megjeleníteni, a maradékok hisztogramját harang alakú görbévé kell alakítani, a 0 körüli középpontba. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Mit jelent az automatizált ML a fennmaradó diagrammal?
Az automatikus ML automatikusan megjelenít egy fennmaradó diagramot, amely megjeleníti a hibák eloszlását a jóslatokban.
#### <a name="what-does-a-good-model-look-like"></a>Mi a jó modell?
A jó modell általában a nullához közel álló maradványokat tartalmaz.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>1. példa: egy regressziós modell, amely torzulást okoz a hibáknál
![SA regressziós modell a hibák torzításával](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>2. példa: egy regressziós modell a hibák egyenletes eloszlásával
![Egy regressziós modell, amely a hibák egyenletes eloszlásával rendelkezik](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>Modell-értelmező és-funkció fontossága
Az automatikus ML gépi tanulásra vonatkozó értelmező irányítópultot biztosít a futtatásához.
A tolmácsolási funkciók engedélyezésével kapcsolatos további információkért tekintse [meg az AUTOMATIZÁLT](how-to-machine-learning-interpretability-automl.md) ml-kísérletek értelmezésének engedélyezése című témakört.

> [!NOTE]
> A magyarázat-ügyfél jelenleg nem támogatja a ForecastTCN modellt. Ez a modell nem ad vissza magyarázat-irányítópultot, ha az a legjobb modellként lett visszaadva, és nem támogatja az igény szerinti magyarázatok futtatását.

## <a name="next-steps"></a>További lépések

+ További információ a Azure Machine Learning [automatizált ml](concept-automated-ml.md) -ről.
+ Próbálja ki az [automatizált Machine learning Model magyarázat](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) minta jegyzetfüzeteket.
