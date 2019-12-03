---
title: Mi az az automatizált ML/automl
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhatja ki a Azure Machine Learning automatikusan egy algoritmust, és létrehoz egy modellt, hogy időt takarítson meg az Ön által megadott paraméterek és feltételek használatával, hogy kiválassza a modellhez legmegfelelőbb algoritmust.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 11/04/2019
ms.openlocfilehash: 1320448b88fa3851196a3dfcb3107921721d364d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707682"
---
# <a name="what-is-automated-machine-learning"></a>Mi az az automatizált gépi tanulás?

Az automatizált gépi tanulás, más néven automatizált ML, az időigényes automatizálási folyamat, a gépi tanulási modellek fejlesztésének ismétlődő feladatai. Lehetővé teszi az adatszakértők, elemzők és fejlesztők számára, hogy a modell minőségének fenntartása mellett nagy mennyiségű, hatékonyságú és termelékenységű ML-modellt építsenek. Az automatikus ML a [Microsoft kutatási részlegének](https://arxiv.org/abs/1705.05355)áttörésén alapul.

A hagyományos gépi tanulási modell fejlesztése erőforrás-igényes, és jelentős tartományi ismereteket és időt igényel, hogy több tucat modellt hozzon létre és hasonlítson össze. Alkalmazzon automatikus ML-t, ha azt szeretné, hogy a Azure Machine Learning egy modell betanítását és finomhangolását a megadott cél metrika használatával. A szolgáltatás ezután megismétli az ML-algoritmusokat a funkciók kiválasztásával párosítva, ahol minden egyes iteráció egy modellt hoz létre egy képzési pontszámmal. Minél magasabb a pontszám, annál jobb lesz a modellnek az adataihoz igazodni.

Az automatizált gépi tanulással felgyorsíthatja az éles használatra kész ML-modellek gyors és hatékony beszerzéséhez szükséges időt.

## <a name="when-to-use-automated-ml"></a>Mikor kell használni az automatikus ML-t

A gépi tanulási modell fejlesztési folyamata automatizált ML-ezáltal demokratikussá teszi, és lehetővé teszi a felhasználók számára, hogy az adatelemzési szakértelmük alapján minden probléma esetén azonosíthatók legyenek a teljes gépi tanulási folyamatok.

Az adatszakértők, az elemzők és a fejlesztők az egész iparágban az automatizált ML-ket használhatják:

+ Gépi tanulási megoldások megvalósítása kiterjedt programozási ismeretek nélkül
+ Időt és erőforrásokat takaríthat meg
+ Az adatelemzési ajánlott eljárások kihasználása
+ Gyors problémamegoldás

Az alábbi táblázat a gyakori automatikus ML-használati eseteket sorolja fel. 

Osztályozás| Regresszió | Idősorozat-előrejelzés
---|---|---
[Csalások észlelése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[CPU-teljesítmény előrejelzése](https://github.com/Azure/MachineLearningNotebooks/blob/37541b10714f9337dbbae721bea494272dc7d151/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb) |[Kereslet-előrejelzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
[Marketing-előrejelzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Anyagok tartósságának előrejelzése](https://github.com/Azure/MachineLearningNotebooks/blob/37541b10714f9337dbbae721bea494272dc7d151/how-to-use-azureml/automated-machine-learning/regression-concrete-strength/auto-ml-regression-concrete-strength.ipynb)|[Értékesítések előrejelzése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)

## <a name="how-automated-ml-works"></a>Az automatizált ML működése

A **Azure Machine learning**használatával a következő lépésekkel megtervezheti és futtathatja az automatizált ml-betanítási kísérleteket:

1. **Azonosítsa a megoldandó problémát** : besorolás, előrejelzés vagy regresszió

1. A **címkézett betanítási adatmennyiség forrásának és formátumának meghatározása**: NumPy tömbök vagy pandák dataframe

1. **Konfigurálja a számítási célt a modell betanításához**, például a [helyi számítógép, a Azure Machine learning számítások, a távoli virtuális gépek vagy a Azure Databricks](how-to-set-up-training-targets.md).  Ismerje meg a [távoli erőforrások](how-to-auto-train-remote.md)automatizált képzését.

1. **Konfigurálja az automatikus gépi tanulás paramétereit** , amelyek meghatározzák, hogy a különböző modellek, a hiperparaméter-beállítások, a speciális előfeldolgozási/featurization, valamint a legjobb modell meghatározásakor milyen mérőszámokat kell megvizsgálni.  Megadhatja az automatikus betanítási kísérlet beállításait [Azure Machine learning Studióban](https://ml.azure.com)vagy [az SDK-val](how-to-configure-auto-train.md). 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **A betanítási Futtatás elküldése.**

  ![Automatizált gépi tanulás](./media/how-to-automated-ml/automl-concept-diagram2.png)

A betanítás során Azure Machine Learning több párhuzamos folyamatot hoz létre, amelyek különböző algoritmusokat és paramétereket próbálnak ki. Ekkor leáll, ha eléri a kísérletben meghatározott kilépési feltételeket.

Megvizsgálhatja a naplózott futtatási adatokat is, amelyek a Futtatás során összegyűjtött [mérőszámokat tartalmazzák](how-to-understand-automated-ml.md) . A betanítási kísérlet egy Python szerializált objektumot (`.pkl` fájlt) hoz létre, amely tartalmazza a modellt és az adatfeldolgozást.

A modell létrehozása automatizálható, és azt is [megtudhatja, milyen fontos vagy releváns funkciók vannak](how-to-configure-auto-train.md#explain) a generált modellekhez.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Előfeldolgozás

Minden automatizált gépi tanulási kísérlet során az adatai az alapértelmezett módszerekkel és opcionálisan a speciális előfeldolgozással vannak feldolgozva.

> [!NOTE]
> Az automatizált gépi tanulás előfeldolgozásának lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos előfeldolgozási lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

### <a name="automatic-preprocessing-standard"></a>Automatikus előfeldolgozás (standard)

Az automatizált gépi tanulási kísérletek során az adatok automatikusan méretezhetők vagy normalizálva vannak, hogy az algoritmusok jól elvégezhetők legyenek.  A modellek betanítása során a rendszer az alábbi skálázási vagy normalizáló technikák egyikét alkalmazza az egyes modellekre.

|Méretezés&nbsp;&&nbsp;normalizálás| Leírás |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Szabványosítási funkciók az átlag és a skálázás egységbeli eltérésének eltávolításával  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Az egyes szolgáltatások méretezésével átalakítja a szolgáltatásokat az adott oszlop minimális és maximális értékével  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Az egyes szolgáltatások méretezése a maximális abszolút értékkel |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |A skálázási funkciók a quantile-tartomány szerint |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineáris dimenzióját-csökkentés az adatértékek számának kibontásával a projekthez egy alacsonyabb dimenziós területre |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ez a transzformátor lineáris dimenzióját-csökkentést végez a csonkolt számú érték lebomlásával (SVD). A PCA-vel ellentétben ez a kalkulátor nem teszi lehetővé az adatközpontok megfogalmazását az egyes számokból származó érték kibontása előtt. Ez azt jelenti, hogy a SciPy. a ritka mátrixok hatékonyan használhatók |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | A rendszer minden mintát (azaz az Adatmátrix minden sorát) legalább egy nullától eltérő összetevővel együtt átméretezi a többi mintától függetlenül, így annak normája (L1 vagy L2) eggyel egyenlő |

### <a name="advanced-preprocessing-optional-featurization"></a>Speciális előfeldolgozás: opcionális featurization

További speciális előfeldolgozási és featurization is elérhetők, például a hiányzó értékek: imputálási, kódolás és átalakítások. [További információ arról, hogy milyen featurization tartalmaz](how-to-create-portal-experiments.md#preprocess). A beállítás engedélyezése a következővel:

+ Azure Machine Learning Studio: az [alábbi lépésekkel](how-to-create-portal-experiments.md)kiválaszthatja a **featurization beállításainak megtekintése** a **konfigurációs Futtatás** szakaszban.

+ Python SDK: `"feauturization": auto' / 'off' / FeaturizationConfig` megadása a [`AutoMLConfig` osztályhoz](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py).


## <a name="time-series-forecasting"></a>Idősoros előrejelzés
Az előrejelzések az üzleti tevékenység szerves részét képezik, függetlenül attól, hogy bevételi, leltározási, értékesítési vagy vevői igények. Az automatikus ML-vel kombinálhatja a technikákat és a megközelítéseket, és egy ajánlott, magas színvonalú idősorozat-előrejelzést is igénybe vehet.

Az automatikus idősorozat-kísérletet többváltozós regressziós problémaként kezeli a rendszer. A korábbi idősorozat-értékek "Pivotal", hogy további dimenziókat regressor a többi előrejelzővel együtt. Ez a klasszikus idősorozat-módszerekkel ellentétben az egyik előnye, hogy természetesen több kontextusos változót is magában foglal, és a képzés során egymáshoz fűződő kapcsolataikat. Az automatizált ML egyetlen, de gyakran belsőleg elágazó modellt tanul az adatkészlet összes eleméhez és előrejelzési horizontokhoz. Így több adat érhető el a modell paramétereinek becsléséhez és az általánosításhoz, hogy a láthatatlan adatsorozatok elérhetővé válnak.

További információ: az [automatikus gépi tanulásra vonatkozó példa az idősorozat-előrejelzéshez](how-to-auto-train-forecast.md). Vagy tekintse meg az [energia igényét bemutató jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) részletes kód példákkal a speciális előrejelzési konfigurációhoz, beleértve a következőket:

* üdülés észlelése és featurization
* idősorozat-és DNN-tanulók (Auto-ARIMA, próféta, ForecastTCN)
* számos modell-támogatás csoportosításon keresztül
* gördülő-eredetű kereszt-ellenőrzés
* konfigurálható késések
* a gördülő ablak összesített funkciói

## <a name="ensemble"></a>Ensemble-modellek

Az automatizált gépi tanulás támogatja az Ensemble-modelleket, amelyek alapértelmezés szerint engedélyezve vannak. A Ensemble learning a gépi tanulási eredmények és a prediktív teljesítmény növelésével több modellt kombinálhat egyetlen modell használatával. Az együttes ismétlések a Futtatás utolsó ismétlései jelennek meg. Az automatizált gépi tanulás mind a szavazási, mind a halmozási módszert használja a modellek kombinálásával:

* **Szavazás**: előre jelezhető az előrejelzett osztály valószínűségének súlyozott átlaga (besorolási feladatoknál) vagy előrejelzett regressziós célok alapján (regressziós feladatokhoz).
* **Halmozás**: a halmozás kombinálja a különböző-modelleket, és az egyes modellek kimenete alapján egy meta-modellt is betanít. A jelenlegi alapértelmezett meta-modellek a besorolási feladatokhoz és a ElasticNet a regresszió/előrejelzési feladatokhoz LogisticRegression.

A rendezett Ensemble inicializálásával eldöntheti, hogy mely modelleket kívánja használni az Ensemble-ban, a [Caruana Ensemble kiválasztási algoritmusa](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) . Ez az algoritmus magas szinten inicializálja az összevonást akár 5 modellel a legjobb egyéni pontszámokkal, és ellenőrzi, hogy ezek a modellek a legjobb pontszámot követő 5%-os küszöbértéken belül vannak-e a gyenge kezdeti együttesek elkerüléséhez. Ezután minden egyes Ensemble-iterációhoz új modellt adnak hozzá a meglévő együtteshez, az eredményül kapott pontszámot pedig kiszámítjuk. Ha egy új modell javította a meglévő Ensemble-pontszámot, a rendszer frissíti az Ensemble-t, hogy tartalmazza az új modellt.

Lásd: [útmutató](how-to-configure-auto-train.md#ensemble) az alapértelmezett Ensemble beállításainak módosításához az automatikus gépi tanulásban.

## <a name="imbalance"></a>Kiegyensúlyozatlan adathalmazok

A rendszer a gépi tanulási besorolási forgatókönyvek esetében általában az adatokon alapuló, nem kiegyensúlyozott adatokra hivatkozik, és olyan adatokra utal, amelyek az egyes osztályokba tartozó megfigyelések aránytalan arányát tartalmazzák. Ez az egyensúlyhiány a modell pontosságának hamisan érzékelt pozitív hatását eredményezheti, mivel a bemeneti adatok az egyik osztályhoz képest elfogultak, ami azt eredményezi, hogy a betanított modell a torzítást utánozza. 

A Machine learning-munkafolyamatok egyszerűsítésének céljaként az automatikus ML beépített képességekkel rendelkezik, amelyek segítenek a kiegyensúlyozatlan adatmennyiségek, például a 

- Egy **súlyozási oszlop**: az automatikus ml a súlyozott oszlopot bemenetként támogatja, ami az adatokban lévő sorok súlyozását eredményezi, ami akár több vagy kevesebb "fontos" osztályt is tehet. Példa a [jegyzetfüzetre](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/sample-weight/auto-ml-sample-weight.ipynb) 

- Az automatikus ML által használt algoritmusok megfelelően kezelhetik az akár 20:1-es egyensúlyhiányt, ami azt jelenti, hogy a leggyakoribb osztály 20 alkalommal több sort tartalmaz az adatmennyiségnél, mint a legkisebb közös osztály.

### <a name="identify-models-with-imbalanced-data"></a>A kiegyensúlyozatlan adattal rendelkező modellek azonosítása

Mivel a besorolási algoritmusokat általában pontossággal értékelik ki, a modell pontossági pontszámának ellenőrzése jó módszer annak azonosítására, hogy az érintett adatok nem egyensúlyban vannak-e. Valóban nagy pontossággal vagy nagyon alacsony pontossággal rendelkezett bizonyos osztályok esetében?

Emellett az automatikus ML-futtatások automatikusan létrehozzák a következő diagramokat, amelyek segítségével megismerheti a modell besorolásának helyességét, és azonosíthatja a kiegyensúlyozatlan adatok által potenciálisan érintett modelleket.

Diagram| Leírás
---|---
[Zavart mátrix](how-to-understand-automated-ml.md#confusion-matrix)| Kiértékeli a helyesen kategorizált címkéket az adatok tényleges címkéjén. 
[Pontosság – visszahívás](how-to-understand-automated-ml.md#precision-recall-chart)| Kiértékeli a helyes feliratok arányát az adatokban található címkézett példányok arányával. 
[ROC-görbék](how-to-understand-automated-ml.md#roc)| Kiértékeli a helyes feliratok arányát a hamis pozitív feliratok arányával.

### <a name="handle-imbalanced-data"></a>Kiegyensúlyozatlan adatmennyiség kezelése 

Az alábbi módszerek további lehetőségeket biztosítanak az automatikus ML-n kívüli, kiegyensúlyozatlan adatmennyiség kezelésére. 

- Újramintavételezés még az osztályra is, akár a kisebb osztályok mintavételezésével vagy a nagyobb osztályok mintavételezésével. Ezek a módszerek szaktudást igényelnek a feldolgozáshoz és az elemzéshez.

- Használjon olyan teljesítmény-mérőszámot, amely jobban bánik a kiegyensúlyozatlan adatokkal. Az F1 pontszám például a precizitás és a visszahívás súlyozott átlaga. A pontosság mértéke az osztályozó pontossága – az alacsony pontosság azt jelzi, hogy a téves pozitív érték nagy számú hamis pozitív--,, míg a visszahívási mérték egy osztályozó teljessége – az alacsony visszahívás nagy számú hamis negatív értéket jelez. 

## <a name="use-with-onnx-in-c-apps"></a>Használat a ONNX C# alkalmazásokban

A Azure Machine Learning használatával a Python-modell létrehozásához és a ONNX-formátumra való átalakításához használhatja az automatikus ML-t. Az ONNX Runtime támogatja C#, így az C# alkalmazásokban automatikusan létrehozott modell újrakódolás vagy a REST-végpontok által bevezethető hálózati késések nélkül is használható. Próbálja ki ezt a folyamatot ebben a [Jupyter-jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatizált ML a Microsofton belül

Az automatikus ML más Microsoft-megoldásokban is elérhető, például:

|Integráció|Leírás|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Automatikus modell kiválasztása és betanítása .NET-alkalmazásokban a Visual Studio és a Visual Studio Code ML.NET automatizált ML (előzetes verzió) használatával.|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|A HDInsight-fürtökön párhuzamosan végezheti el az automatizált ML-betanítási feladatok felskálázását a Sparkban.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Gépi tanulási modellek meghívása közvetlenül Power BI (előzetes verzió).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Új gépi tanulási modelleket hozhat létre SQL Server 2019 big data-fürtök adatain keresztül.|

## <a name="next-steps"></a>Következő lépések

Tekintse át a példákat, és Ismerje meg, hogyan hozhat létre modelleket automatizált gépi tanulás használatával:

+ Kövesse az [oktatóanyagot: regressziós modell automatikus betanítása az Azure automatizált Machine learning](tutorial-auto-train-models.md)

+ Konfigurálja az automatikus betanítási kísérlet beállításait:
  + A Azure Machine Learning Studióban [kövesse ezeket a lépéseket](how-to-create-portal-experiments.md).
  + A Python SDK használatával hajtsa [végre az alábbi lépéseket](how-to-configure-auto-train.md).

+ Ismerje meg, hogyan [végezheti](how-to-auto-train-forecast.md)el az automatikus betanítást az idősorozat-adatkészletek használatával.

+ Próbálja ki [Jupyter notebook mintákat az automatizált gépi tanuláshoz](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
