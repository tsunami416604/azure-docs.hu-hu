---
title: Automatizált gépi Tanulási algoritmus kiválasztása és hangolás
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan Azure Machine Learning szolgáltatás automatikusan algoritmus kiválasztása az Ön számára, és hozzon létre egy modellt, hogy időt takaríthat meg a paramétereket, és az Ön által megadott feltételeknek, a legjobb algoritmust határozza meg a modellhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 05/21/2019
ms.custom: seodec18
ms.openlocfilehash: 93eb0fba91ce5064d04a340e8b3e5b984ee73081
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515573"
---
# <a name="what-is-automated-machine-learning"></a>Mi a machine learning automatikus?

Automatizált gépi tanulási, automatizált gépi tanulás, más néven az a folyamat az időigényes, iteratív gépi tanulási modell fejlesztési feladatok automatizálásához. Lehetővé teszi az adatszakértők, az elemzők és a fejlesztők számára a nagy skálázást, a hatékonyság és a termelékenység gépi Tanulási modelleket építhetnek a fenntartási modellminőség mellett.

Hagyományos machine learning-modell fejlesztői erőforrás-igényes, jelentős szakterület ismeretének és létrehozására, és hasonlítsa össze a modellek tucat időt igénylő. Automatizált ML alkalmazni, ha azt szeretné, hogy az Azure Machine Learning betanítására és a egy modell finomhangolása a megadott cél metrika használatával. A szolgáltatás majd végighalad a gépi Tanulási algoritmusokat funkció beállításokat is, ahol minden egyes ismétléskor hoz létre egy képzési pontszám modell megfeleltetni. Minél nagyobb a pontszám, annál jobb a modell minősül "szélességhez" adatait.

Az automatikus machine learning fog gyorsítsa fel az éles használatra kész, gépi Tanulási modelleket első nagyszerű egyszerű és hatékony kezeléséhez szükséges időt.

## <a name="when-to-use-automated-ml"></a>Automatizált gépi tanulás használata

Automatizált ML democratizes a gépi tanulási modell fejlesztési folyamatot, és a felhasználók, függetlenül attól, hogy a data science szaktudásuk egy teljes körű machine learning-folyamatot bármilyen probléma azonosítása.

Adatszakértők, adatelemzők és fejlesztők számára a különböző ágazatok között az automatizált ML használhatja:

+ Gépi tanulási megoldások széles körű programozási ismeretek nélkül megvalósítása
+ Időt és erőforrásokat takaríthat meg
+ Használja ki a data science – gyakorlati tanácsok
+ Adja meg, Agilis problémamegoldó szakembereinkkel

## <a name="how-automated-ml-works"></a>Hogyan automatizált ML működése

Használatával **Azure Machine Learning szolgáltatás**, tervezését és a Machine Learning betanítási automatizált kísérletek futtassa az alábbi lépéseket:

1. **Gépi tanulás probléma azonosítása** kell megoldani: besorolási, -előrejelzés vagy regressziós

1. **Adja meg a forrás- és a címkézett betanítási adatok formátumát**: Numpy tömbök vagy Pandas dataframe

1. **Konfigurálja a számítási célnak modell betanítása**, mint például a [helyi számítógépen, az Azure Machine Learning kiszámítja, távoli virtuális gépek vagy az Azure Databricks](how-to-set-up-training-targets.md).  További információ az automatikus képzési [távoli erőforrás](how-to-auto-train-remote.md).

1. **Konfigurálja az automatikus machine learning-paraméterek** amelyek meghatározzák, hogy hány ismétlések keresztül különböző modell, hiperparaméter beállításait, speciális előfeldolgozása/featurization, és milyen metrikákat, és tekintse meg a legjobb modellt meghatározásakor.  Automatikus betanítási kísérlet beállításait konfigurálhatja [az Azure Portalon](how-to-create-portal-experiments.md) vagy [az SDK-val](how-to-configure-auto-train.md).

1. **Küldje el a betanítási Futtatás.**

  ![Automatizált Machine learning](./media/how-to-automated-ml/automl-concept-diagram2.png)

Betanítás, során az Azure Machine Learning szolgáltatás számos különböző algoritmusok és paraméterek próbálja párhuzamos folyamatok hoz létre. Miután a kilépési feltételeket a kísérletben definiált elér megszűnik.

A naplózott futtatási információk, amely tartalmazza a futtatás során gyűjtött metrikák is ellenőrizheti. A betanítási Futtatás hoz létre egy Python-szerializált objektumot (`.pkl` fájl), amely tartalmazza a modell és az adatok előfeldolgozása.

Modell létrehozása automatikus, miközben is [megtudhatja, hogyan fontos és releváns szolgáltatások vannak](how-to-configure-auto-train.md#explain) a létrehozott modellek.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Előfeldolgozása

Minden automatikus machine learning-kísérlet, az alapértelmezett a módszerekkel, és igény szerint – speciális előfeldolgozása az adatok előre feldolgozott.

### <a name="automatic-preprocessing-standard"></a>Automatikus előfeldolgozása (szokásos)

Az adatok minden automatikus machine learning-kísérlet, az automatikusan horizontálisan vagy normalized jól algoritmusok segítségével.  Során modell betanítása és a egy, a következő skálázás vagy a normalizálási módszerek lépnek érvénybe, mindegyik modellt.

|Skálázás&nbsp;&&nbsp;normalizálási| Leírás |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Szolgáltatások szabványosíthatja eltávolításával az átlag, és méretezési lehetőségek érhetők el egység variancia  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Szolgáltatások alakítja át az egyes szolgáltatások által az adott oszlop minimális és maximális méretezése  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Az abszolút maximális értékét minden egyes szolgáltatás méretezése |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |A Scaler funkciók azok ki osztóérték tartomány szerint |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |A projekt azt egy alacsonyabb dimenzionális terület, az adatokat egyetlen érték felbontása segítségével lineáris dimenziócsökkentést |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ez átalakító lineáris dimenziócsökkentést jedné csonkolt érték idősorfelbontási (SVD) segítségével hajtja végre. Ellentétesen PEM ez estimator nem center az adatokat az egyes számú értéket idősorfelbontási számítástechnika előtt. Ez azt jelenti, hogy együtt tudjon működni az scipy.sparse mátrixok hatékonyan |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Mindegyik minta (vagyis az adatok mátrixban minden egyes sorára) legalább egy nem nulla értékű összetevővel újra méretezett más minták függetlenül, hogy az előírt (l1 vagy l2) eredménye egy |

### <a name="advanced-preprocessing-optional-featurization"></a>Speciális előfeldolgozása: nem kötelező featurization

További speciális előfeldolgozása és featurization is elérhetők, például a hiányzó értékeket imputálási, kódolási és átalakításokat. [További tudnivalók a milyen featurization megtalálható](how-to-create-portal-experiments.md#preprocess). Ez a beállítás engedélyezése:

+ Az Azure Portalon: Válassza a **Preprocess** a jelölőnégyzet a **speciális beállítások** [ezek a lépések](how-to-create-portal-experiments.md).

+ Python SDK: Adjon meg `"preprocess": True` számára a [ `AutoMLConfig` osztály](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Ensemble modellek

Ensemble modellek az automatizált machine learning segítségével betaníthatja az [Caruana ensemble kijelölés algoritmust rendezett Ensemble inicializálási](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Ensemble learning machine learning eredményeket és prediktív teljesítménye növelhető a weboldalak százmilliárdjainak biztosítják a szolgáltatás egyetlen modellek használatával számos modellt. A Futtatás utolsó ismétlése ensemble ismétléseinek jelenik meg.

## <a name="training-metric-output"></a>Metrika kimeneti képzés

Többféle módon futtatási törzsének képzési pontossága metrikákat tekinthet meg.

* A Jupyter widget használja.
* Használja a `get_metrics()` bármely függvény `Run` objektum.
* A metrikák megtekintése az Azure Portalon a kísérlet során.

### <a name="classification-metrics"></a>Besorolási metrikák

A következő metrikák besorolási feladat futtatási törzsének lesznek mentve.

|Metrika|Leírás|Számítás|További paraméterek
--|--|--|--|
AUC_Macro| AUC az a terület, a fogadó működő jellemző görbe alatt. Makró minden egyes osztály a AUC számtani középértékét.  | [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlagos = "makra."|
AUC_Micro| AUC az a terület, a fogadó működő jellemző görbe alatt. Micro globálisan számított a valódi pozitívok és az egyes osztályok vakriasztások kombinálásával| [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlagos = "micro"|
AUC_Weighted  | AUC az a terület, a fogadó működő jellemző görbe alatt. Az eredmény az egyes osztályok az egyes osztályok igaz példányok száma szerint számtani súlyozott van.| [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|átlagos = "súlyozott"
accuracy|Pontosság pontosan egyezik a valódi címkéket előre jelzett címkékhez százaléka. |[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|Átlagos pontosság pontosság-visszahívási görbe elért minden egyes küszöbértéket, a korábbi küszöbértéket, a súly használt fogyasztóktól növekedés az szükséges, a súlyozott átlag foglalja össze. Makró az egyes osztályok átlagos pontosság pontszám középértéke|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlagos = "makra."|
average_precision_score_micro|Átlagos pontosság pontosság-visszahívási görbe elért minden egyes küszöbértéket, a korábbi küszöbértéket, a súly használt fogyasztóktól növekedés az szükséges, a súlyozott átlag foglalja össze. Micro globálisan számított weboldalak százmilliárdjainak biztosítják a valódi pozitívok és a téves, minden egyes megszakítási szerint|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlagos = "micro"|
average_precision_score_weighted|Átlagos pontosság pontosság-visszahívási görbe elért minden egyes küszöbértéket, a korábbi küszöbértéket, a súly használt fogyasztóktól növekedés az szükséges, a súlyozott átlag foglalja össze. Súlyozott van számtani átlagos pontosság az eredmény az egyes osztályok az egyes osztályok igaz példányok száma szerint|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlagos = "súlyozott"|
balanced_accuracy|Elosztott terhelésű pontosságát az egyes osztályok visszaírási számtani középértékét.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "makra."|
f1_score_macro|F1 pontszám: közepének pontosság és a visszahívás. Makró az egyes osztályok F1 pontszám középértéke|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlagos = "makra."|
f1_score_micro|F1 pontszám: közepének pontosság és a visszahívás. Micro globálisan számított alapján a teljes valódi pozitívok, a téves negatív és a hamis pozitív jelzések elkerülése|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlagos = "micro"|
f1_score_weighted|F1 pontszám: közepének pontosság és a visszahívás. Súlyozott átlag által az egyes osztályok F1 pontszám osztály gyakorisága|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlagos = "súlyozott"|
log_loss|Ez az a veszteség függvény a logisztikai regressziós (multinomial) és -bővítmények, például a Neurális hálózatokat definiálva a valószínűségi besorolás előrejelzéseket megadott igaz címkék negatív log valószínűségét, használt. Egyetlen minta igaz a címke a yt {0,1} és becsült yp valószínűsége, hogy yt = 1, a napló adatvesztés a - P jelentkezzen (yt&#124;yp) =-(yt log(yp) + (1 – yt) log (1 – yp))|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|Normalizált makró visszahívása makró ne felejtse el, hogy a véletlenszerű teljesítmény 0 pontszámot pedig tökéletes teljesítmény 1 pontszámot normalized. Ez úgy érhető el, norm_macro_recall: (recall_score_macro - R) = /(1-R), ahol az R véletlenszerű ismeretekkel (azaz a R = 0,5 bináris osztályozási) és a C osztályú osztályozási problémák R=(1/C) recall_score_macro várt értéke|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "makró", majd (recall_score_macro - R) /(1-R), ahol az R véletlenszerű ismeretekkel (azaz a R = 0,5 bináris osztályozási) és a C osztályú osztályozási problémák R=(1/C) recall_score_macro várt értéke|
precision_score_macro|Pontosság a százalékos aránya a következő címkét: egy adott osztály elemeit tartalmazza ténylegesen az adott osztály. Makró az egyes osztályok pontosság számtani középértékét|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "makra."|
precision_score_micro|Pontosság a százalékos aránya a következő címkét: egy adott osztály elemeit tartalmazza ténylegesen az adott osztály. Micro globálisan számított alapján a teljes valódi pozitívok és a hamis pozitív jelzések elkerülése|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "micro"|
precision_score_weighted|Pontosság a százalékos aránya a következő címkét: egy adott osztály elemeit tartalmazza ténylegesen az adott osztály. Minden osztály az egyes osztályok igaz példányok száma szerint a pontossági számtani súlyozott van.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "súlyozott"|
recall_score_macro|Visszaírási valójában egy bizonyos osztály elemeinek megfelelően van-e jelölve, hogy százaléka. Makró az egyes osztályok visszaírási középértéke|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "makra."|
recall_score_micro|Visszaírási valójában egy bizonyos osztály elemeinek megfelelően van-e jelölve, hogy százaléka. Micro globálisan számított a teljes valódi pozitívok, a téves negatív leltár szerint|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "micro"|
recall_score_weighted|Visszaírási valójában egy bizonyos osztály elemeinek megfelelően van-e jelölve, hogy százaléka. Minden osztály az egyes osztályok igaz példányok száma szerint visszaírási számtani középértékét súlyozott van.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "súlyozott"|
weighted_accuracy|Ahol a súlyt kapnak valamennyi példa megegyezik az időarány, amíg igaz példányok adott példa igaz osztályban súlyozott pontossága pontossága|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight egy adott osztály az egyes elemekhez az időarány, amíg a célzott egyenlő vektor|

### <a name="regression-and-forecasting-metrics"></a>Regresszió és előrejelzési mérőszámok

A következő metrikák regressziós vagy előrejelzési feladat futtatási törzsének lesznek mentve.

|Metrika|Leírás|Számítás|További paraméterek
--|--|--|--|
explained_variance|MAGYARÁZAT eltérés az időarány, amíg, amelyhez a modell matematikai fiókok számára egy adott adatkészlet változata. A százalékos varianciát a varianciát a hibák az eredeti adatok csökkenése. Az átlag, a hibák értéke 0, esetén egyenlő magyarázat eltérés.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score|R2 meghatározása vagy a képest egy alapkonfiguráció modellt, amely a mean squared hibák százalékos csökkenését a relatív. Az átlag, a hibák értéke 0, esetén egyenlő magyarázat eltérés.|[Számítás](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|Valamennyi korrelációs a két adatkészlet között fennálló monotonicity nonparametric mértékegysége. A Pearson-korrelációs eltérően a valamennyi korrelációs nem feltételezi azt, hogy mindkét olyan adatkészlettel általában oszlanak meg. Más korrelációs együttható, például az egyik platformjától függően -1 és + 1 nincs korreláció úgy 0-val. -1 és + 1 összefüggéseket egy pontos monoton kapcsolatot jelenti. Pozitív összefüggéseket jelenti azt, hogy x növekszik, hogy mit y. Negatív összefüggéseket jelenti azt, hogy növeli az x y csökken.|[Számítás](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|Jelenti azt, hogy abszolút hiba a várt értékkel, az abszolút értékét a cél- és az előrejelzési közötti különbség|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error|Normalizált átlagos abszolút hiba osztva az adatok tartományán mean Absolute Error|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Az adatok számos nullával|
median_absolute_error|Közepes abszolút hiba a cél- és az előrejelzési közötti összes abszolút eltérések középértékének. Ezen adatveszteség robusztus a kiugró értékeket.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|Normalizált medián abszolút hiba osztva az adatok tartományán medián abszolút hiba|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Az adatok számos nullával|
root_mean_squared_error|Root mean squared hiba a cél- és az előrejelzési várt eltéréseinek négyzetgyökét|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error|Normalizált legfelső szintű mean squared hiba root mean squared hiba osztva az adatok tartományán:|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Az adatok számos nullával|
root_mean_squared_log_error|Legfelső szintű mean squared log hiba a várt squared logaritmikus hiba négyzetgyökét|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error|Root mean squared log normalizált hiba elosztja az adatokat számos root mean squared log hiba|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Az adatok számos nullával|


## <a name="use-with-onnx-in-c-apps"></a>Használja az ONNX- C# alkalmazások

Az Azure Machine Learning segítségével automatizált gépi Tanulási modell létrehozása a Python, és annak az ONNX-formátumra alakítja át. Az ONNX-modul támogatja C#, így használhatja a az automatikusan létrehozott modell az C# alkalmazások anélkül, hogy nem kell újraprogramozás, vagy a hálózati késések, amelyek REST-végpontokat. Próbálja ki ezt a folyamatot például [a Jupyter notebookot az](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>A Microsoft közötti automatikus gépi tanulás

Automatizált ML is érhető el a többi Microsoft-megoldások például:

|Integráció|Leírás|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Automatikus modell kiválasztása és képzések a Visual Studio és a Visual Studio Code használatával ML.NET a .NET-alkalmazások automatizált a Machine Learning (előzetes verzió).|
|[HDIsnight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Horizontális felskálázás az automatizált Machine Learning betanítási feladatokat a Spark on HDInsight-fürtök párhuzamosan.|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Hívja meg a machine learning-modellek közvetlenül a Power BI (előzetes verzió).|

## <a name="next-steps"></a>További lépések

Példák, és ismerje meg, hogyan hozhat létre automatizált Machine Learning használatával:

+ Kövesse a [oktatóanyag: Az Azure automatikus Machine Learning egy osztályozási modell automatikusan betanítása](tutorial-auto-train-models.md)

+ Adja meg az automatikus betanítási kísérlet beállításait:
  + Az Azure portal felületén [ezekkel a lépésekkel](how-to-create-portal-experiments.md).
  + A Python SDK-val [ezekkel a lépésekkel](how-to-configure-auto-train.md).

+ Ismerje meg, hogyan train, idősorozat-adatok használatával automatikus [ezekkel a lépésekkel](how-to-auto-train-forecast.md).

+ Próbálja ki [Jupyter Notebook minták](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
