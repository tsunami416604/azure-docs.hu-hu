---
title: Mi az az automatizált ML/AutoML
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhatja ki a Azure Machine Learning automatikusan egy algoritmust, és létrehoz egy modellt, hogy időt takarítson meg az Ön által megadott paraméterek és feltételek használatával, hogy kiválassza a modellhez legmegfelelőbb algoritmust.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: f592a7f5a4af38988bcf433f0adc89d9be7579cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82082009"
---
# <a name="what-is-automated-machine-learning-automl"></a>Mi az a gépi tanulás (AutoML)?

Az automatizált gépi tanulás, más néven automatizált ML vagy AutoML, az időigényes automatizálási folyamat, a gépi tanulási modellek fejlesztésének ismétlődő feladatai. Lehetővé teszi az adatszakértők, elemzők és fejlesztők számára, hogy a modell minőségének fenntartása mellett nagy mennyiségű, hatékonyságú és termelékenységű ML-modellt építsenek. Az automatikus ML a [Microsoft kutatási részlegének](https://arxiv.org/abs/1705.05355)áttörésén alapul.

A hagyományos gépi tanulási modell fejlesztése erőforrás-igényes, és jelentős tartományi ismereteket és időt igényel, hogy több tucat modellt hozzon létre és hasonlítson össze. Az automatizált gépi tanulással felgyorsíthatja az éles használatra kész ML-modellek gyors és hatékony beszerzéséhez szükséges időt.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Mikor kell használni a AutoML: osztályozás, regresszió, & előrejelzés

Alkalmazzon automatikus ML-t, ha azt szeretné, hogy a Azure Machine Learning egy modell betanítását és finomhangolását a megadott cél metrika használatával. A gépi tanulási modell fejlesztési folyamata automatizált ML-ezáltal demokratikussá teszi, és lehetővé teszi a felhasználók számára, hogy az adatelemzési szakértelmük alapján minden probléma esetén azonosíthatók legyenek a teljes gépi tanulási folyamatok.

Az adatszakértők, az elemzők és a fejlesztők az egész iparágban az automatizált ML-ket használhatják:
+ ML-megoldások megvalósítása kiterjedt programozási ismeretek nélkül
+ Időt és erőforrásokat takaríthat meg
+ Az adatelemzési ajánlott eljárások kihasználása
+ Gyors problémamegoldás

### <a name="classification"></a>Osztályozás

A besorolás egy gyakori gépi tanulási feladat. A besorolás olyan felügyelt tanulás típusa, amelyben a modellek bemutatják a betanítási információkat, és ezeket a tanulmányokat az új adatra alkalmazzák. Azure Machine Learning a featurizations kifejezetten ezekhez a feladatokhoz, például a Deep neurális hálózati szöveg featurizers a besoroláshoz. További információ a [featurization beállításairól](how-to-use-automated-ml-for-ml-models.md#featurization). 

A besorolási modellek fő célja, hogy megjósolja, hogy az új adatok milyen kategóriákba esnek a betanítási adatokból származó tanulások alapján. Gyakori besorolási példák például a csalások észlelése, a kézírás-felismerés és az objektumok észlelése.  További információk: az [automatikus gépi tanulásra vonatkozó besorolási](tutorial-train-models-with-aml.md)példa.

Példák a besorolásra és az automatizált gépi tanulásra ezekben a Python-jegyzetfüzetekben: [csalások észlelése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [marketing-előrejelzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)és [hírcsoport-adatbesorolás](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)

### <a name="regression"></a>Regresszió
A besoroláshoz hasonlóan a regressziós feladatok is közösen felügyelt tanulási feladat. Azure Machine Learning [featurizations kifejezetten ezekre a feladatokra](how-to-use-automated-ml-for-ml-models.md#featurization)vonatkozik.

Eltér a besorolástól, ahol az előre jelzett kimeneti értékek kategorikusak, a regressziós modellek előre jelezik a numerikus kimeneti értékeket a független előrejelzők alapján. A regresszió során a cél az, hogy segítsen a független prediktív változók közötti kapcsolat kialakításában azzal, hogy megbecsüli, hogyan befolyásolja a többi változó. Például az autó árát a (z), a gáz kilométer, a biztonsági minősítés stb. jellemzői alapján. További információ: a [regressziós példa a gépi tanulás](tutorial-auto-train-models.md)automatizálására.

A Python-jegyzetfüzetek előrejelzéseit a regressziós és automatizált gépi tanulásra vonatkozó példákban tekintheti meg: [CPU teljesítményének előrejelzése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Idősorozat-előrejelzés

Az előrejelzések az üzleti tevékenység szerves részét képezik, függetlenül attól, hogy bevételi, leltározási, értékesítési vagy vevői igények. Az automatikus ML-vel kombinálhatja a technikákat és a megközelítéseket, és egy ajánlott, magas színvonalú idősorozat-előrejelzést is igénybe vehet. További információ: az [automatikus gépi tanulás az idősorozat-előrejelzéshez](how-to-auto-train-forecast.md). 

Az automatikus idősorozat-kísérletet többváltozós regressziós problémaként kezeli a rendszer. A korábbi idősorozat-értékek "Pivotal", hogy további dimenziókat regressor a többi előrejelzővel együtt. Ez a klasszikus idősorozat-módszerekkel ellentétben az egyik előnye, hogy természetesen több kontextusos változót is magában foglal, és a képzés során egymáshoz fűződő kapcsolataikat. Az automatizált ML egyetlen, de gyakran belsőleg elágazó modellt tanul az adatkészlet összes eleméhez és előrejelzési horizontokhoz. Így több adat érhető el a modell paramétereinek becsléséhez és az általánosításhoz, hogy a láthatatlan adatsorozatok elérhetővé válnak.

A speciális előrejelzési konfiguráció a következőket tartalmazza:
* üdülés észlelése és featurization
* idősorozat-és DNN-tanulók (Auto-ARIMA, próféta, ForecastTCN)
* számos modell támogatja a csoportosítást
* gördülő-eredetű kereszt-ellenőrzés
* konfigurálható késések
* a gördülő ablak összesített funkciói


Tekintse meg a regressziós és automatizált gépi tanulásra vonatkozó példákat a következő Python-jegyzetfüzetekben: [értékesítési előrejelzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), [kereslet-előrejelzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)és [üdítőital-termelési előrejelzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automl-works"></a>A AutoML működése

A betanítás során Azure Machine Learning több folyamatot is létrehoz párhuzamosan, amelyek különböző algoritmusokat és paramétereket próbálnak ki. A szolgáltatás megismétli az ML-algoritmusokat a funkciók kiválasztásával párosítva, ahol minden egyes iteráció egy képzési pontszámmal rendelkező modellt hoz létre. Minél magasabb a pontszám, annál jobb lesz a modellnek az adataihoz igazodni.  Ekkor leáll, ha eléri a kísérletben meghatározott kilépési feltételeket. 

A **Azure Machine learning**használatával a következő lépésekkel megtervezheti és futtathatja az automatizált ml-betanítási kísérleteket:

1. **Azonosítsa a megoldandó problémát** : besorolás, előrejelzés vagy regresszió

1. **Válassza ki, hogy szeretné-e használni a PYTHON SDK-t vagy a Studio webes felületét**: Ismerje meg a [Python SDK és a Studio webes felülete](#parity)közötti paritást.

   * A korlátozott vagy a nem szükséges programkódok kipróbálásához próbálja ki a Azure Machine Learning Studio webes felületét[https://ml.azure.com](https://ml.azure.com/)  
   * Python-fejlesztők számára tekintse meg a [Azure Machine learning PYTHON SDK](how-to-configure-auto-train.md) -t 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. A **címkézett betanítási adatmennyiség forrásának és formátumának meghatározása**: NumPy tömbök vagy pandák dataframe

1. **Konfigurálja a számítási célt a modell betanításához**, például a [helyi számítógép, a Azure Machine learning számítások, a távoli virtuális gépek vagy a Azure Databricks](how-to-set-up-training-targets.md).  Ismerje meg a [távoli erőforrások](how-to-auto-train-remote.md)automatizált képzését.

1. **Konfigurálja az automatikus gépi tanulás paramétereit** , amelyek meghatározzák, hogy a különböző modellek, a hiperparaméter-beállítások, a speciális előfeldolgozási/featurization, valamint a legjobb modell meghatározásakor milyen mérőszámokat kell megvizsgálni.  
1. **A betanítási Futtatás elküldése.**

1. **Az eredmények áttekintése** 

A következő ábra ezt a folyamatot szemlélteti. 
![Automatizált gépi tanulás](./media/concept-automated-ml/automl-concept-diagram2.png)


Megvizsgálhatja a naplózott futtatási adatokat is, amelyek a Futtatás során összegyűjtött [mérőszámokat tartalmazzák](how-to-understand-automated-ml.md) . A betanítási kísérlet egy Python szerializált objektumot`.pkl` (fájlt) hoz létre, amely tartalmazza a modellt és az adatfeldolgozást.

A modell létrehozása automatizálható, és azt is [megtudhatja, milyen fontos vagy releváns funkciók vannak](how-to-configure-auto-train.md#explain) a generált modellekhez.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Előfeldolgozás

Minden automatizált gépi tanulási kísérlet során az adatai az alapértelmezett módszerekkel és opcionálisan a speciális előfeldolgozással vannak feldolgozva.

> [!NOTE]
> Az automatizált gépi tanulás előfeldolgozásának lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos előfeldolgozási lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

### <a name="automatic-preprocessing-standard"></a>Automatikus előfeldolgozás (standard)

Az automatizált gépi tanulási kísérletek során az adatok automatikusan méretezhetők vagy normalizálva vannak, hogy az algoritmusok jól elvégezhetők legyenek.  A modellek betanítása során a rendszer az alábbi skálázási vagy normalizáló technikák egyikét alkalmazza az egyes modellekre.

|&nbsp;&Méretezés&nbsp;normalizálása| Leírás |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Szabványosítási funkciók az átlag és a skálázás egységbeli eltérésének eltávolításával  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Az egyes szolgáltatások méretezésével átalakítja a szolgáltatásokat az adott oszlop minimális és maximális értékével  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Az egyes szolgáltatások méretezése a maximális abszolút értékkel |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |A skálázási funkciók a quantile-tartomány szerint |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineáris dimenzióját-csökkentés az adatértékek számának kibontásával a projekthez egy alacsonyabb dimenziós területre |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ez a transzformátor lineáris dimenzióját-csökkentést végez a csonkolt számú érték lebomlásával (SVD). A PCA-vel ellentétben ez a kalkulátor nem helyezi középpontba az adatmennyiséget, mielőtt feldolgozza az egyes SciPy. a ritka mátrixok hatékonyan működnek. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | A rendszer minden mintát (azaz az Adatmátrix minden sorát) legalább egy nullától eltérő összetevővel együtt átméretezi, hogy a norma (L1 vagy L2) eggyel egyenlő legyen. |

### <a name="advanced-preprocessing--featurization"></a>Speciális előfeldolgozási & featurization

További speciális előfeldolgozási és featurization is elérhetők, például az adatguardrails, a kódolás és az átalakítások. [További információ arról, hogy milyen featurization tartalmaz](how-to-use-automated-ml-for-ml-models.md#featurization). A beállítás engedélyezése a következővel:

+ Azure Machine Learning Studio: engedélyezze az **automatikus featurization** a **további konfiguráció megtekintése** szakaszban [ezekkel a lépésekkel](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ Python SDK: az `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` [ `AutoMLConfig` osztály](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)megadása. 

<a name="parity"></a>

## <a name="the-studio-vs-sdk"></a>A Studio vs SDK

Ismerje meg a Python SDK-ban és a Azure Machine Learningban található stúdióban elérhető, magas szintű automatizált ML-funkciók közötti paritást és különbségeket. 

### <a name="experiment-settings"></a>Kísérletezési beállítások 

A következő beállítások lehetővé teszik az automatikus ML-kísérlet konfigurálását. 

| |A Python SDK|A Studio webes felülete|
----|:----:|:----:
Adat felosztása a betanítási/ellenőrzési készletekbe| ✓|✓
A ML-feladatok támogatása: besorolás, regresszió és előrejelzés| ✓| ✓
Optimalizálás az elsődleges metrika alapján| ✓| ✓
A pénzmosás-számítást számítási célként támogatja | ✓|✓
Az előrejelzési horizont konfigurálása, a cél késleltetése & gördülő ablak|✓|✓
Kilépési feltételek beállítása |✓|✓ 
Egyidejű ismétlések beállítása| ✓|✓
Oszlopok eldobása| ✓|✓
Algoritmusok letiltása|✓|✓
Több érvényesítés |✓|✓
A Azure Databricks-fürtökön betanítást támogatja| ✓|
Megjelenő szolgáltatások nevének megtekintése|✓|
Featurization összegzése| ✓|
Featurization munkaszüneti napokhoz|✓|
Naplófájl részletességi szintjei| ✓|

### <a name="model-settings"></a>Modell beállításai

Ezek a beállítások a legjobb modellre alkalmazhatók az automatikus ML-kísérlet eredményeképpen.

| |A Python SDK|A Studio webes felülete|
|----|:----:|:----:|
|A legjobb modell-regisztráció, üzembe helyezés, magyarázat| ✓|✓|
|Szavazói Ensemble & stack Ensemble-modellek engedélyezése| ✓|✓|
|A legjobb modell megjelenítése a nem elsődleges metrika alapján|✓||
|ONNX-modell kompatibilitásának engedélyezése/letiltása|✓||
|A modell tesztelése | ✓| |

### <a name="run-control-settings"></a>Vezérlési beállítások futtatása

Ezekkel a beállításokkal áttekintheti és szabályozhatja a kísérlet futtatását és a gyermeke futtatását. 

| |A Python SDK|A Studio webes felülete|
|----|:----:|:----:|
|Összegző táblázat futtatása| ✓|✓|
|& gyermekobjektumok futtatásának megszakítása| ✓|✓|
|Guardrails beolvasása| ✓|✓|
|& szüneteltetésének folytatása| ✓| |

## <a name="ensemble-models"></a><a name="ensemble"></a>Ensemble-modellek

Az automatizált gépi tanulás támogatja az Ensemble-modelleket, amelyek alapértelmezés szerint engedélyezve vannak. A Ensemble learning a gépi tanulási eredmények és a prediktív teljesítmény növelésével több modellt kombinálhat egyetlen modell használatával. Az együttes ismétlések a Futtatás utolsó ismétlései jelennek meg. Az automatizált gépi tanulás mind a szavazási, mind a halmozási módszert használja a modellek kombinálásával:

* **Szavazás**: előre jelezhető az előrejelzett osztály valószínűségének súlyozott átlaga (besorolási feladatoknál) vagy előrejelzett regressziós célok alapján (regressziós feladatokhoz).
* **Halmozás**: a halmozás kombinálja a különböző-modelleket, és az egyes modellek kimenete alapján egy meta-modellt is betanít. A jelenlegi alapértelmezett meta-modellek a besorolási feladatokhoz és a ElasticNet a regresszió/előrejelzési feladatokhoz LogisticRegression.

A rendezett Ensemble inicializálásával eldöntheti, hogy mely modelleket kívánja használni az Ensemble-ban, a [Caruana Ensemble kiválasztási algoritmusa](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) . Ez az algoritmus magas szinten inicializálja az összevonást akár öt modellel a legjobb egyéni pontszámokkal, és ellenőrzi, hogy ezek a modellek a legjobb pontszámot követő 5%-os küszöbértéken belül vannak-e a gyenge kezdeti együttesek elkerüléséhez. Ezután minden egyes Ensemble-iterációhoz új modellt adnak hozzá a meglévő együtteshez, az eredményül kapott pontszámot pedig kiszámítjuk. Ha egy új modell javította a meglévő Ensemble-pontszámot, a rendszer frissíti az Ensemble-t, hogy tartalmazza az új modellt.

Lásd: [útmutató](how-to-configure-auto-train.md#ensemble) az alapértelmezett Ensemble beállításainak módosításához az automatikus gépi tanulásban.

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

A Azure Machine Learning használatával a Python-modell létrehozásához és a ONNX-formátumra való átalakításához használhatja az automatikus ML-t. Ha a modellek ONNX formátumúak, különböző platformokon és eszközökön is futtathatók. További információ a [ONNX-mel rendelkező ml-modellek felgyorsításáról](concept-onnx.md).

Tekintse meg a ONNX formátumra való átalakítást [ebben a Jupyter-jegyzetfüzetben példa](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). [A ONNX által támogatott algoritmusok](how-to-configure-auto-train.md#select-your-experiment-type)ismertetése.

A ONNX Runtime támogatja a C#-t is, így a C#-alkalmazásokban automatikusan létrehozott modellt is használhatja az újrakódolás vagy a REST-végpontok által bevezetett hálózati késések nélkül. További információ [a ONNX-modellekről a ONNX Runtime C# API-val](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 



## <a name="next-steps"></a>További lépések

Tekintse át a példákat, és Ismerje meg, hogyan hozhat létre modelleket automatizált gépi tanulás használatával:

+ Kövesse az [oktatóanyagot: regressziós modell automatikus betanítása Azure Machine learning](tutorial-auto-train-models.md)

+ Konfigurálja az automatikus betanítási kísérlet beállításait:
  + A Azure Machine Learning Studióban [kövesse ezeket a lépéseket](how-to-use-automated-ml-for-ml-models.md).
  + A Python SDK használatával hajtsa [végre az alábbi lépéseket](how-to-configure-auto-train.md).

+ Ismerje meg, hogyan [végezheti](how-to-auto-train-forecast.md)el az automatikus betanítást az idősorozat-adatkészletek használatával.

+ Próbálja ki [Jupyter notebook mintákat az automatizált gépi tanuláshoz](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* Az automatikus ML más Microsoft-megoldásokban is elérhető, például:, [ml.net](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-automated) és [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
