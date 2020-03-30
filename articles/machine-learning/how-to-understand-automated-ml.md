---
title: Az automatikus ml-eredmények ismertetése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan tekintheti meg és értheti meg az egyes automatizált gépi tanulási futtatások diagramjait és mutatóit.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: c5f12da3606361b504d4581916d9645fa3cd24f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283458"
---
# <a name="understand-automated-machine-learning-results"></a>Az automatizált gépi tanulási eredmények értelmezése
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan tekintheti meg és ismerheti meg az egyes automatikus gépi tanulási futtatások diagramjait és metrikákat. 

További információk:
+ [Metrikák, diagramok és görbék besorolási modellekhez](#classification)
+ [Metrikák, diagramok és grafikonok regressziós modellekhez](#regression)
+ [A modell értelmezhetősége és a jellemzőfontosság](#explain-model)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

* Hozzon létre egy kísérletet az automatikus gépi tanulási futtatáshoz, akár az SDK-val, akár az Azure Machine Learning stúdióban.

    * Az SDK használatával [besorolási modellt](how-to-auto-train-remote.md) vagy [regressziós modellt](tutorial-auto-train-models.md) hozhat létre
    * Az [Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md) használatával a megfelelő adatok feltöltésével besorolási vagy regressziós modellt hozhat létre.

## <a name="view-the-run"></a>A futtatás megtekintése

Egy automatizált gépi tanulási kísérlet futtatása után a futtatások előzményei megtalálhatók a gépi tanulási munkaterületen. 

1. Lépjen a munkaterülethez.

1. A munkaterület bal oldali paneljén válassza a **Kísérletek**lehetőséget.

   ![A kísérlet menüjének képernyőképe](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. A kísérletek listájában válassza ki a vizsgálni kívánt kísérletet.

   [![Kísérleti lista](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Az alsó táblázatban válassza a **Futtatás lehetőséget.**

   Kísérlet futtatása ) [ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. A Modellek, válassza ki az **algoritmus nevét** a modell, amely tovább szeretne vizsgálni.

   [![Kísérleti modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Ugyanezeket az eredményeket is láthatja `RunDetails`a futás során, amikor a [Jupyter widgetet](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)használja.

## <a name="classification-results"></a><a name="classification"></a>Osztályozási eredmények

A következő metrikák és diagramok minden olyan besorolási modellhez elérhetők, amelyet az Azure Machine Learning automatizált gépi tanulási képességeinek használatával hoz létre

+ [Mutatókat](#classification-metrics)
+ [Zavartsági mátrix](#confusion-matrix)
+ [Precíziós visszahívási diagram](#precision-recall-chart)
+ [A vevő működési jellemzői (vagy ROC)](#roc)
+ [Emelési görbe](#lift-curve)
+ [Nyereséggörbe](#gains-curve)
+ [Kalibrációs görbét](#calibration-plot)

### <a name="classification-metrics"></a>Besorolási mutatók

A következő metrikák kerülnek mentésre minden futtatási iteráció egy besorolási feladathoz.

Metrika|Leírás|Számítás|További paraméterek
--|--|--|--
AUC_Macro| Az AUC a vevő működési jellemző görbéje alatti terület. A makró az AUC számtani középközépe az egyes osztályok esetében.  | [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlag="makró"|
AUC_Micro| Az AUC a vevő működési jellemző görbéje alatti terület. A mikrot globálisan számítják ki az egyes osztályok valódi pozitív és hamis pozitívjainak kombinálásával.| [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlag="mikro"|
AUC_Weighted  | Az AUC a vevő működési jellemző görbéje alatti terület. A súlyozott az egyes osztályok pontszámának számtani középértéke, amelyet az egyes osztályokban lévő valódi példányok számával súlyoznak.| [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|átlag ="súlyozott"
accuracy|A pontosság az előre jelzett címkék százalékos értéke, amelyek pontosan megfelelnek a valódi címkéknek. |[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|Az átlagos pontosság a pontosság-visszahívási görbét az egyes küszöbértékeknél elért pontosság súlyozott középértékének tekinti, az előző küszöbértékhez képest a visszahívás növekedését pedig súlyként használjuk. A makró az egyes osztályok átlagos pontossági pontszámának számtani középértéke.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlag="makró"|
average_precision_score_micro|Az átlagos pontosság a pontosság-visszahívási görbét az egyes küszöbértékeknél elért pontosság súlyozott középértékének tekinti, az előző küszöbértékhez képest a visszahívás növekedését pedig súlyként használjuk. A mikrot globálisan számítják ki a valódi pozitív és hamis pozitív értékek kombinálásával minden egyes levágáskor.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlag="mikro"|
average_precision_score_weighted|Az átlagos pontosság a pontosság-visszahívási görbét az egyes küszöbértékeknél elért pontosság súlyozott középértékének tekinti, az előző küszöbértékhez képest a visszahívás növekedését pedig súlyként használjuk. A súlyozott az egyes osztályok átlagos pontossági pontszámának számtani középértéke, az egyes osztályokban lévő valódi példányok számával súlyozva.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlag ="súlyozott"|
balanced_accuracy|A kiegyensúlyozott pontosság az egyes osztályok visszahívásának számtani átlaga.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag="makró"|
f1_score_macro|Az F1 pontszám a pontosság és a visszahívás harmonikus középformája. A makró az F1 pontszám számtani átlaga az egyes osztályok esetében.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlag="makró"|
f1_score_micro|Az F1 pontszám a pontosság és a visszahívás harmonikus középformája. A mikroértéket globálisan számítják ki az összes valódi pozitív, hamis negatív és hamis pozitív összeg megszámlálásával.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlag="mikro"|
f1_score_weighted|Az F1 pontszám a pontosság és a visszahívás harmonikus középformája. Az F1 pontszám osztálygyakorisága szerinti súlyozott átlag minden egyes osztályra|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlag ="súlyozott"|
log_loss|Ez a veszteség függvény használt (multinomial) logisztikai regresszió és kiterjesztések, mint például a neurális hálózatok, meghatározása szerint a negatív log-valószínűsége a valódi címkék adott valószínűségű valószínűségegy valószínűségi osztályozó jóslatok. Egyetlen minta esetében, amelynek {0,1} valódi címkéje yt és becsült valószínűsége yp yt = 1, a log loss a -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp)).|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|Normalizált makró visszahívás makró visszahívás normalizált, hogy a véletlenszerű teljesítmény pontszám 0 és a tökéletes teljesítmény egy tucat 1. Ezt a norm_macro_recall := (recall_score_macro - R)/(1 - R) értékkel érik el, ahol R a recall_score_macro véletlenszerű előrejelzések (azaz R=0,5 a bináris osztályozás és az R=(1/C) várható értéke a C-osztály osztályozási problémák esetén).|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag = "makró" |
precision_score_macro|A pontosság a megfelelően címkézett pozitív előre jelzett elemek százalékos százaléka. A makró az egyes osztályok pontosságának számtani középformája.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlag="makró"|
precision_score_micro|A pontosság a megfelelően címkézett pozitív előre jelzett elemek százalékos százaléka. A mikrot globálisan számítják ki a teljes valódi pozitív és hamis pozitív értékek megszámlálásával.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlag="mikro"|
precision_score_weighted|A pontosság a megfelelően címkézett pozitív előre jelzett elemek százalékos százaléka. A súlyozott az egyes osztályok pontosságának számtani középértéke, az egyes osztályok valódi példányainak számával súlyozva.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlag ="súlyozott"|
recall_score_macro|A visszahívás egy adott osztály helyesen címkézett elemeinek százalékos százaléka. A makró az egyes osztályok visszahívásának számtani középformája.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag="makró"|
recall_score_micro|A visszahívás egy adott osztály helyesen címkézett elemeinek százalékos százaléka. A Micro számítása világszerte az összes valódi pozitív, hamis negatív és hamis pozitív érték megszámlálásával történik.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag="mikro"|
recall_score_weighted|A visszahívás egy adott osztály helyesen címkézett elemeinek százalékos százaléka. A súlyozott a visszahívás számtani középértéke minden osztályban, az egyes osztályok valódi példányainak számával súlyozva.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlag ="súlyozott"|
weighted_accuracy|A súlyozott pontosság olyan pontosság, ahol az egyes példáknak adott súly megegyezik az adott példa valódi osztályában lévő valódi példányok arányával.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight olyan vektor, amely megegyezik az adott osztály nak a célelem egyes elemeire vonatkozó arányával.|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Zavartsági mátrix

#### <a name="what-is-a-confusion-matrix"></a>Mi az a zűrzavaros mátrix?
A besorolási modell teljesítményének leírására tévesztési mátrix ot alkalmaz. Minden sor megjeleníti az adatkészlet igaz vagy tényleges osztályának példányait, és minden oszlop a modell által előre jelzett osztály példányait jelöli. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Mit csinál az automatizált ml a zűrzavaros mátrixcal?
Besorolási problémák esetén az Azure Machine Learning automatikusan biztosít egy zavart mátrix minden modell, amely épül. Minden egyes zavartmátrix esetében az automatikus ml megmutatja az egyes előre jelzett címkék (oszlopok) gyakoriságát a valódi címkével (sor) összehasonlítva. Minél sötétebb a szín, annál nagyobb a szám a mátrix adott részében. 

#### <a name="what-does-a-good-model-look-like"></a>Hogy néz ki egy jó modell?
Összehasonlítjuk az adatkészlet tényleges értékét a modell által megadott előre jelzett értékekkel. Emiatt a gépi tanulási modellek nagyobb pontosságú, ha a modell a legtöbb értékeit az átlós mentén, ami azt jelenti, a modell megjósolta a helyes értéket. Ha egy modell osztály egyensúlytalanság, a zavartmátrix segít az elfogult modell észlelésében.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>1. példa: Gyenge pontosságú osztályozási modell
![Besorolási modell gyenge pontossággal](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>2. példa: Nagy pontosságú osztályozási modell 
![Nagy pontosságú osztályozási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>3. példa: Nagy pontosságú és nagy torzítású osztályozási modell a modell-előrejelzésekben
![Nagy pontosságú és nagy torzítású osztályozási modell a modell-előrejelzésekben](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Precíziós visszahívási diagram
#### <a name="what-is-a-precision-recall-chart"></a>Mi az a precíziós visszahívási diagram?
A pontosság-visszahívási görbe a pontosság és a modell visszahívása közötti kapcsolatot mutatja. A precizitás kifejezés azt jelzi, hogy a modell képes az összes példány megfelelő en címkézni. A Visszahívás azt jelzi, hogy az osztályozó képes-e megtalálni egy adott címke összes példányát.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Mit csinál az automatizált ml a precíziós visszahívási diagrammal?

Ezzel a diagrammal összehasonlíthatja az egyes modellek pontosság-visszahívási görbéit, hogy meghatározza, melyik modellnek van elfogadható kapcsolata az adott üzleti probléma pontossága és visszahívása között. Ez a diagram a makróátlag-pontos visszahívást, a mikroátlagos pontosság-visszahívást és a modell összes osztályához tartozó pontosság-visszahívást mutatja. 

A makroátlag az egyes osztályoktól függetlenül számítja ki a metrikát, majd az átlagot veszi fel, és az összes osztályt egyenlően kezeli. A mikroátlag azonban összesíti az összes osztály hozzájárulását az átlag kiszámításához. A mikroátlag akkor előnyösebb, ha az adatkészletben osztálykiegyensúlyozatlanság van.

#### <a name="what-does-a-good-model-look-like"></a>Hogy néz ki egy jó modell?
Az üzleti probléma céljától függően az ideális pontosság-visszahívási görbe eltérhet. Néhány példa az alábbiakban

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>1. példa: Alacsony pontosságú és alacsony visszahívású osztályozási modell
![Alacsony pontosságú és alacsony visszahívású osztályozási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>2. példa: ~100%-os pontosságú és ~100%-os visszahívású osztályozási modell 
![Nagy pontosságú és visszahívási osztályozási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC diagram

#### <a name="what-is-a-roc-chart"></a>Mi az a ROC diagram?
A vevő működési jellemzője (vagy ROC) a helyesen osztályozott címkék és az adott modell helytelenül osztályozott címkéinek mintája. A ROC-görbe kevésbé informatív lehet, ha nagy torzítású adatkészleteken lévő modelleket tanít be, mivel nem jeleníti meg a hamis pozitív címkéket.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Mit csinál az automatizált ML a ROC-diagrammal?
Az automatizált ml generálmakró átlagpontosság-visszahívás, micro average precision-recall, és a pontosság-visszahívás társított összes osztály egy modell. 

A makroátlag az egyes osztályoktól függetlenül számítja ki a metrikát, majd az átlagot veszi fel, és az összes osztályt egyenlően kezeli. A mikroátlag azonban összesíti az összes osztály hozzájárulását az átlag kiszámításához. A mikroátlag akkor előnyösebb, ha az adatkészletben osztálykiegyensúlyozatlanság van.

#### <a name="what-does-a-good-model-look-like"></a>Hogy néz ki egy jó modell?
Ideális esetben a modell lesz közelebb 100%-os valódi pozitív arány, és közelebb 0%-os hamis pozitív arány. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>1. példa: Besorolási modell alacsony igaz címkékkel és magas hamis címkékkel
![Osztályozási modell alacsony igaz címkékkel és magas hamis címkékkel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>2. példa: Besorolási modell magas valódi címkékkel és alacsony hamis címkékkel
![osztályozási modell magas valódi címkékkel és alacsony hamis címkékkel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Lift diagram
#### <a name="what-is-a-lift-chart"></a>Mi az a liftdiagram?
A növekedési diagramok egy osztályozási modell teljesítményének értékelésére szolgálnak. Ez azt mutatja, hogy mennyivel jobb an-term, hogy nem a generált modell képest anélkül, hogy a modell pontossága.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Mit csinál az automatikus ml a liftdiagrammal?
Összehasonlíthatja az Azure Machine Learning és az alapvonal segítségével automatikusan kialakított modell felemelkedését az adott modell értéknyereségének megtekintéséhez.
#### <a name="what-does-a-good-model-look-like"></a>Hogy néz ki egy jó modell?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>1. példa: Olyan besorolási modell, amely rosszabb, mint egy véletlenszerű kiválasztási modell
![Olyan osztályozási modell, amely rosszabb, mint egy véletlenszerű kiválasztási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>2. példa: Olyan besorolási modell, amely jobban teljesít, mint egy véletlenszerű kijelölési modell
![Jobban teljesítő besorolási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Nyereség diagram
#### <a name="what-is-a-gains-chart"></a>Mi az a nyereség diagram?

A nyereségdiagram a besorolási modell teljesítményét az adatok egyes részei szerint értékeli ki. Azt mutatja, minden percentilis az adatkészlet, mennyivel jobban lehet várni, hogy végre képest egy véletlenszerű kiválasztási modell.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Mit csinál az automatizált ML a nyereség diagram?
A halmozott nyereség diagram segítségével kiválaszthatja a besorolási cutoff egy százalék, amely megfelel a kívánt nyereséget a modellből. Ez az információ egy másik módja a kísérő liftdiagram eredményeinek vizsgálatának.

#### <a name="what-does-a-good-model-look-like"></a>Hogy néz ki egy jó modell?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>1. példa: Besorolási modell minimális nyereséggel
![besorolási modell minimális nyereséggel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>2. példa: Jelentős nyereséggel rendelkező osztályozási modell
![Jelentős nyereséggel rendelkező osztályozási modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Kalibrációs diagram

#### <a name="what-is-a-calibration-chart"></a>Mi az a kalibrációs diagram?
A kalibrációs görbét a prediktív modell megbízhatóságának megjelenítésére használják. Ezt úgy éri el, hogy megmutatja az előre jelzett valószínűség és a tényleges valószínűség közötti kapcsolatot, ahol a "valószínűség" annak valószínűségét jelenti, hogy egy adott példány valamilyen címke alá tartozik.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Mit csinál az automatizált ml a kalibrációs diagrammal?
Az összes besorolási probléma esetén áttekintheti a mikroátlag, a makroátlag és az egyes osztályok kalibrációs vonalát egy adott prediktív modellben.

A makroátlag az egyes osztályoktól függetlenül számítja ki a metrikát, majd az átlagot veszi fel, és az összes osztályt egyenlően kezeli. A mikroátlag azonban összesíti az összes osztály hozzájárulását az átlag kiszámításához. 
#### <a name="what-does-a-good-model-look-like"></a>Hogy néz ki egy jó modell?
 A jól kalibrált modell igazodik az y=x vonalhoz, ahol ésszerűen biztos az előrejelzésekben. A túlmagabiztos modell igazodik az y=0 vonalhoz, ahol az előre jelzett valószínűség jelen van, de nincs tényleges valószínűség. 


##### <a name="example-1-a-well-calibrated-model"></a>1. példa: Jól kalibrált modell
![ jobban kalibrált modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>2. példa: Túlmagabiztos modell
![Egy túlmagabiztos modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>Regressziós eredmények

A következő metrikák és diagramok minden olyan regressziós modellhez elérhetők, amelyet az Azure Machine Learning automatizált gépi tanulási képességeivel hoz létre

+ [Mutatókat](#reg-metrics)
+ [Előre jelzett vs. Igaz](#pvt)
+ [A maradékok hisztogramja](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>Regressziós mutatók

A következő metrikák at minden futtatási ismétlés egy regressziós vagy előrejelzési feladat.

|Metrika|Leírás|Számítás|További paraméterek
--|--|--|--|
explained_variance|A magyarázott variancia az az arány, amelyhez egy matematikai modell egy adott adatkészlet variációját figyelembe véve figyelembe adja. Ez az eredeti adatok varianciájának százalékos csökkenése a hibák varianciájához. Ha a hibák átlaga 0, akkor az megegyezik a magyarázott varianciával.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score|R2 a meghatározási együttható vagy a négyzetes hibák százalékos csökkenése az átlagot kimenetelt kivezető alapmodellhez képest. |[Számítás](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|A spearman korreláció a két adatkészlet közötti kapcsolat monotoniitásának nem parametrikus mértéke. A Pearson-korrelációval ellentétben a Spearman korreláció nem feltételezi, hogy mindkét adatkészlet általában elosztott. Más korrelációs együtthatókhoz hasonlóan ez is -1 és +1 között változik, és a 0 nem jelent korrelációt. A -1 vagy +1 korrelációk pontos monoton kapcsolatot jelentenek. A pozitív korrelációk azt jelentik, hogy x növekedésével y is. A negatív korrelációk azt jelentik, hogy az x növekedésével y csökken.|[Számítás](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|Az átlagos abszolút hiba a cél és az előrejelzés közötti abszolút érték várható értéke.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error|A normalizált átlagos abszolút hiba az abszolút hibát és az adatok tartományát hányta.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Osztás az adatok tartománya szerint|
median_absolute_error|Az abszolút medián hiba a cél és az előrejelzés közötti összes abszolút különbség mediánja. Ez a veszteség robusztus a kiugró értékekhez képest.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|Normalizált abszolút medián hiba a medián abszolút hiba osztva az adatok tartományával|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Osztás az adatok tartománya szerint|
root_mean_squared_error|A négyzetes átlagos négyzetes hiba a cél és az előrejelzés közötti várható négyzetkülönbség négyzetgyöke|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error|Normalizált gyökér átlagos négyzetes hiba a gyinoki átlagú, az adatok tartományával elosztva.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Osztás az adatok tartománya szerint|
root_mean_squared_log_error|A négyzetes négyzetes naplóhiba a várt négyzetes logaritmikus hiba négyzetgyöke|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error|Normalizált gyökér átlagos négyzetes naplóhiba a négyzetes négyzetes naplóhiba és az adatok tartományának hányadosa|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Osztás az adatok tartománya szerint|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>Előre jelzett és igaz diagram
#### <a name="what-is-a-predicted-vs-true-chart"></a>Mi az előre jelzett és az igaz diagram?
Az előre jelzett és az igaz érték egy előre jelzett érték és a regressziós probléma valódi értéke közötti kapcsolatot mutatja. Ez a grafikon egy modell teljesítményének mérésére használható, mivel minél közelebb van az y=x vonalhoz az előre jelzett értékek, annál jobb a prediktív modell pontossága.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Mit tesz az automatikus ml a Előre jelzett és az igaz diagrammal?
Minden futtatás után láthatja az egyes regressziós modellek előre jelzett és igaz grafikonjait. Az adatok adatainak védelme érdekében az értékek et együtt ürülik meg, és az egyes tárolók mérete sávdiagramként jelenik meg a diagramterület alsó részén. Összehasonlíthatja a prediktív modellt a világosabb árnyékterülettel, amely hibahatárral mutatja a hibát, és összehasonlíthatja a modell ideális értékét.

#### <a name="what-does-a-good-model-look-like"></a>Hogy néz ki egy jó modell?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>1. példa: Kis pontosságú osztályozási modell
![Gyenge pontosságú regressziós modell az előrejelzésekben](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>2. példa: Nagy pontosságú regressziós modell 
[![Regressziós modell nagy pontossággal az előrejelzésekben](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>A maradékok kórlapjának hisztogramja
#### <a name="what-is-a-residuals-chart"></a>Mi az a maradék diagram?
A maradvány egy megfigyelt y-t jelöl – az előre jelzett y-t. Az alacsony torzítású hibahatár megjelenítéséhez a maradékok hisztogramját haranggörbeként kell forznunk, 0 körül. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Mit csinál az automatizált ML a maradékdiagrammal?
Az automatikus ml automatikusan biztosít egy maradékdiagramot az előrejelzésekben előforduló hibák eloszlásának megjelenítéséhez.
#### <a name="what-does-a-good-model-look-like"></a>Hogy néz ki egy jó modell?
Egy jó modell általában egy harang görbe vagy hibák körül nulla.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>1. példa: Regressziós modell, amelynek hibái torzítása
![SA regressziós modell a hibák elfogultságával](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>2. példa: Regressziós modell a hibák egyenletesebb eloszlásával
![Regressziós modell a hibák egyenletesebb eloszlásával](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>A modell értelmezhetősége és a jellemzőfontosság
Az automatikus ml gépi tanulási értelmezhetőségi irányítópultot biztosít a futtatásokhoz.
Az értelmezhetőségi funkciók engedélyezéséről további információt [az](how-to-machine-learning-interpretability-automl.md) automatizált gépelési kísérletek értelmezhetőségének engedélyezéséről talál.

## <a name="next-steps"></a>További lépések

+ További információ az [automatikus ml-ről](concept-automated-ml.md) az Azure Machine Learningben.
+ Próbálja ki az [automatikus gépi tanulási modell magyarázata](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) minta notebookok.
