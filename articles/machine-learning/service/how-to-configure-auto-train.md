---
title: Automatizált Machine Learning-kísérletek létrehozása
titleSuffix: Azure Machine Learning service
description: Automatizált gépi tanulási algoritmus választja ki az Ön számára, és készen áll a központi telepítési modell generál. Ismerje meg a beállításokat, amelyek segítségével automatizált machine learning-példakísérleteket konfigurálása.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 67995b4cc9c212f1798a37b54873bd349ac36576
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384921"
---
# <a name="configure-automated-machine-learning-experiments"></a>Automatizált machine learning-példakísérleteket konfigurálása

Automatizált machine learning olyan algoritmusai és hiperparaméterek kiválasztja az Ön számára, és készen áll a központi telepítési modell állít elő. Többféle módon automatizált machine learning-példakísérleteket konfigurálásához használhatja. Ebben az útmutatóban megtudhatja, hogyan különböző konfigurációs beállításainak megadásához.

Egy automatizált machine learning-példakísérleteket Példák megtekintése: [oktatóanyag: Egy automatizált gépi tanulással osztályozási modell betanításához](tutorial-auto-train-models.md) vagy [és a felhőben automatizált machine learning-modellek betanításához](how-to-auto-train-remote.md).

Az automatikus machine learningben elérhető konfigurációs lehetőségek:

* A kísérlet típusának kiválasztása: Besorolás, regressziós és előrejelzés
* Az adatforrás, formátumokat és adatok újbóli lekérésére
* Válassza a compute-cél: helyi vagy távoli
* Automatizált machine learning-kísérlet beállításai
* Egy automatizált machine learning-kísérlet futtatása
* Modell metrikák böngészése
* Regisztráljon és a modell üzembe helyezése

## <a name="select-your-experiment-type"></a>A kísérlet típusának kiválasztása
Mielőtt elkezdené a kísérlethez, meg kell határoznia a megoldandó, machine learning probléma típusú. Automatizált machine learning támogatja a besorolást, regressziós és előrejelzés tevékenységtípust. 

Automatizált gépi tanulási funkciók általánosan elérhetők, amíg **előrejelzés jelenleg még nyilvános előzetes verzióban.**

Automatizált machine learning az automatizálás és a hangolási folyamat során a következő algoritmusokat támogatja. Felhasználójaként van, nem szükséges, hogy adja meg az algoritmus.

Besorolás | Regresszió | Előrejelzések
|-- |-- |--
[Logisztikai regresszió](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rugalmas Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rugalmas Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Sztochasztikus gradiens módszeres (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Naiv Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Színátmenet kiemelése](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Színátmenet kiemelése](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[C-támogatás vektor besorolás (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)|[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)
[Lineáris SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[A legközelebbi szomszédok K](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[A legközelebbi szomszédok K](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[A legközelebbi szomszédok K](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors)|[Szabadkézi LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Szabadkézi LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Sztochasztikus gradiens módszeres (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Sztochasztikus gradiens módszeres (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Rendkívül véletlenszerű fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Rendkívül véletlenszerű fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Rendkívül véletlenszerű fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Színátmenet kiemelése](https://scikit-learn.org/stable/modules/ensemble.html#classification)|
[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|


## <a name="data-source-and-format"></a>Adatforrás és a formátum
Automatizált machine learning található adatok támogatja, a helyi számítógépére vagy a felhőben, például az Azure Blob Storage. Az adatok olvashatók be a scikit-ismerje meg a támogatott formátumok. Áttekintheti az adatokat:
* Numpy tömbök X (szolgáltatások) és az y (célváltozó vagy más néven címke)
* Pandas dataframe 

Példák:

*   Numpy tömbök

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

*   Pandas dataframe

    ```python
    import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Kísérlet futtatása távoli számítási adatlehívást

Ha egy távoli számítási használatával futtathatja a kísérletet, az adatok betöltése közben kell csomagolni, külön python-szkriptet a `get_data()`. Ez a szkript távoli számítási van futtassa, ahol az automatizált machine learning-kísérlet futtatása. `get_data` szükségtelenné teszi az adatok lehívása törzsének a hálózaton keresztül. Nélkül `get_data`, a kísérlet sikertelen lesz, amikor távoli számítási futtatja.

Íme egy példa `get_data`:

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection import train_test_split 
from sklearn.preprocessing import LabelEncoder 
def get_data(): # Burning man 2016 data 
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42) 
    return { "X" : df, "y" : y }
```

Az a `AutoMLConfig` objektumot, akkor adja meg a `data_script` paraméter, és adja meg az elérési útját a `get_data` hasonló, az alábbi parancsfájlt:

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

`get_data` parancsfájl adhat vissza:

Kulcs | Típus |    Kölcsönösen kizárják egymást az | Leírás
---|---|---|---
X | Pandas Dataframe vagy Numpy tömbje | data_train, a címkét, az oszlopok |  Megkezdheti az összes funkció
Y | Pandas Dataframe vagy Numpy tömbje |   label   | Megkezdheti az adatok. A besorolás kell lennie az egész számok tömbje.
X_valid | Pandas Dataframe vagy Numpy tömbje   | data_train, felirat | _Nem kötelező_ érvényesítése az összes szolgáltatás. Ha nincs megadva, az X train között van felosztva, és ellenőrzése
y_valid |   Pandas Dataframe vagy Numpy tömbje | data_train, felirat | _Nem kötelező_ a felirat adatainak ellenőrzése a. Ha nincs megadva, az y train között van felosztva, és ellenőrzése
sample_weight | Pandas Dataframe vagy Numpy tömbje |   data_train, a címkét, az oszlopok| _Nem kötelező_ súlyértéket minden mintához. Akkor használja, ha szeretné rendelni az adatpontokhoz különböző súlya 
sample_weight_valid | Pandas Dataframe vagy Numpy tömbje | data_train, a címkét, az oszlopok |    _Nem kötelező_ súlyértéket minden érvényesítési mintához. Ha nincs megadva, a sample_weight train között van felosztva, és ellenőrzés
data_train |    Pandas Dataframe |  X, y, X_valid, y_valid |    Minden adat (funkciók és címke) rendelkező betanítása
label | sztring  | X, y, X_valid, y_valid |  A data_train melyik oszlop felel meg a címke
Oszlopok | karakterláncok tömbje  ||  _Nem kötelező_ oszlopok engedélyezett funkciók használata
cv_splits_indices   | Egész számok tömbje ||  _Nem kötelező_ indexek kell felosztani az adatokat az érvényesítési közötti listája

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>Betölteni, és előkészíti az adatokat az Adatelőkészítés-SDK-val
Automatizált machine learning-példakísérleteket támogatja az adatok betöltése és alakítja át a az adatelőkészítés SDK használatával. Az SDK-val lehetővé teszi a

>* Elemzés paraméter következtetésekhez (kódolás, elválasztó, a fejlécek) rendelkező több fájltípus betölthető
>* Típus-átalakítás következtetésekhez használatával fájl betöltése közben
>* MS SQL Server és az Azure Data Lake Storage kapcsolat támogatása
>* Az autorefresh tulajdonság oszlop hozzáadása
>* Hiányzó imputálására
>* Oszlopok származtatása példa alapján
>* Szűrés
>* Egyéni Python-átalakítások

További információ az adatok előkészítési sdk tekintse meg a [hogyan készíti elő az adatok modellezését, ahol a cikk](how-to-load-data.md). Alább látható egy példa, adat-előkészítési sdk használata az adatok betöltése. 
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>Adatok train és érvényesítése

Külön vonat és érvényesítési beállítása get_data() vagy közvetlenül is megadhat a `AutoMLConfig` metódust.

## <a name="cross-validation-split-options"></a>Keresztellenőrzési vágási lehetőségek

### <a name="k-folds-cross-validation"></a>K – Modellrész keresztellenőrzés

Használat `n_cross_validations` ellenőrzések közötti számát adja meg a beállítást. A tanítási adathalmazt lesz véletlenszerűen felosztva `n_cross_validations` modellrész egyenlő méretű. Során minden egyes keresztellenőrzési round egyet a kevesebb modellrészt használható ellenőrzés céljából a többi modellrész tanított modell. Ez a folyamat ismétlődik a `n_cross_validations` kerekít mindaddig, amíg minden modellrészek érvényesítési beállított egyszer használatos. Az összes átlagos pontszámok `n_cross_validations` kerekít lesz jelentve, és a megfelelő modellt fog retrained a a egészében betanítási adatok készlete.

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Monte Carlo keresztellenőrzés (más néven) Ismétlődő véletlenszerű alárendelt mintavételi)

Használjon `validation_size` százalékos aránya az érvényesítés, és használja a használandó képzési adatkészlet megadásához `n_cross_validations` közötti ellenőrzések száma. Egyes során keresztellenőrzés round, egy részhalmazát méretű `validation_size` ellenőrzés céljából a többi adat tanított modell a rendszer véletlenszerűen választ. Végül, az átlagos pontszámmodell összes `n_cross_validations` kerekít lesz jelentve, és a megfelelő modellt fog retrained a a egészében betanítási adatok készlete.

### <a name="custom-validation-dataset"></a>Egyéni ellenőrzési adatkészlet

Egyéni ellenőrzési adatkészletet használja, ha véletlenszerű split nem fogadható el (általában time series és imbalanced adatokat). A saját érvényesítési adatkészletet is megadhat. A modellben értékelik az érvényesítési élelmiszer-megadott véletlenszerű adatkészlet helyett.

## <a name="compute-to-run-experiment"></a>Számítási kísérlet futtatása

Ezután határozza meg, hol kell betanítani a modellt. Egy automatizált machine learning betanítási kísérlet futtassa a következő számítási lehetőségek:
*   A helyi gépen, például a helyi számítógépen vagy laptopon – általában amikor rendelkezik kisméretű adatkészlet, és továbbra is a feltárás fázisban.
*   Egy távoli gépen a felhőben – [Azure Machine Learning-felügyelt számítási](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) egy felügyelt szolgáltatás, amely lehetővé teszi a Azure-beli virtuális gépek fürtjein gépi tanulási modelleket taníthat be.

Tekintse meg a [a Github-webhelyről](https://github.com/Azure/MachineLearningNotebooks/tree/master/automl) például a helyi és távoli notebookok számítási céljainak.

<a name='configure-experiment'/>

## <a name="configure-your-experiment-settings"></a>A kísérlet beállításainak konfigurálása

Többféle módon használhatja az automatikus machine learning-kísérlet konfigurálása. Ezeket a paramétereket állítja be hárítható el egy `AutoMLConfig` objektum.

Néhány példa:

1.  Besorolási kísérlet az elsődleges metrika és a maximális száma az iteráció és a Befejezés után 50 ismétlési és 2 keresztellenőrzési modellrész kísérletet 12 000 másodperc idő szerint súlyozott AUC használatával.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X, 
        y=y,
        n_cross_validations=2)
    ```
2.  Alább egy például 100 közelítő befejezéséhez regressziós kísérlet meg, a minden egyes ismétléskor tartó legfeljebb 600 másodperc az 5-érvényesítési adatbázisközi modellrész.

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

Ez a táblázat felsorolja a paraméterek beállításai is futtathatja a kísérletet és alapértelmezett értékeik érhető el.

Tulajdonság |  Leírás | Alapértelmezett érték
--|--|--
`task`  |Adja meg a machine learning probléma típusát. Megengedett értékek: <li>Besorolás</li><li>Regresszió</li><li>Előrejelzések</li>    | None |
`primary_metric` |Ez a metrika szeretné optimalizálni a modell épületben. Például a primary_metric pontossága ad meg, ha automatikus egy modell található a legnagyobb pontosságú gépi tanulási keresi. Csak egy primary_metric kísérletenként adható meg. Megengedett értékek: <br/>**Besorolási**:<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**Regresszió**: <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score  </li> | Besorolási: pontossága <br/>A regresszió: spearman_correlation <br/> |
`experiment_exit_score` |   A primary_metric célérték adhatja meg. Miután egy modell található, amely megfelel a primary_metric cél, automatizált gépi tanulási le fog állni, léptetés, és az a kísérlet befejeződik. Ha ez az érték nincs beállítva (alapértelmezett), automatikus machine learning-kísérlet ismétléseinek megadott tevékenységsort futtatásához továbbra is. Egy dupla értéket vesz fel. Ha soha nem eléri a cél, majd automatikus machine learning továbbra is addig ismétlések megadott ismétlések száma.| None
`iterations` |Az ismétlések maximális számát. Minden egyes ismétléskor megegyezik a betanítási feladat, amely egy folyamat eredményez. Folyamat az adatok előfeldolgozása és a modell. A magas színvonalú modell, amelyet 250 vagy több    | 100
`max_concurrent_iterations`|    Párhuzamos az ismétlések maximális száma. Ez a beállítás csak távoli számítási működik.|   1
`max_cores_per_iteration`   | Azt jelzi, hogy hány magunk a számítási célnak a használni kívánt betanításához egy folyamatot. Ha az algoritmus kihasználhatják a több mag, majd ez növeli a Többmagos gépen a teljesítményt. Beállíthatja a gépen elérhető összes mag használandó 1 értéket ad.|  1
`iteration_timeout_minutes` |   Korlátozza egy adott iterációhoz szükséges idő (perc). Ha egy iterációját meghaladja a megadott mennyiséget, a iteráció beolvasása megszakítva. Ha nincs megadva, a rendszer az iteráció továbbra is fut, amíg befejeződik. |   None
`n_cross_validations`   |A keresztellenőrzés felosztásainak száma| None
`validation_size`   |Állítsa be az összes képzési minta százalékában érvényesítési mérete.|  None
`preprocess` | Igaz/hamis <br/>Igaz lehetővé teszi, hogy a bemeneti adatok az előfeldolgozási végrehajtásához kísérletezhet. Következő része a előfeldolgozása<li>Hiányzó adatok: Biztosítják a hiányzó adatokat-numerikus az átlag, a legtöbb előfordulásával szöveg </li><li>Kategorikus értékek: Adattípus esetén a numerikus és egyedi értékek száma a kevesebb mint 5 %-os, az egyik gyakori kódolási alakíthatók át egymásba </li><li>Stb. Ellenőrizze a teljes listát [a GitHub-adattár](https://aka.ms/aml-notebooks)</li><br/>Megjegyzés: Ha az adatok ritka nem használhat előfeldolgozása = true |  False (Hamis) | 
`blacklist_models`  | Automatizált machine learning-kísérlet megkísérli számos különböző algoritmus rendelkezik. Konfigurálja az egyes algoritmusok kizárása a kísérlet. Akkor hasznos, ha vegye figyelembe, hogy aránytól nem működnek jól az adatkészlet. Kivéve a algoritmusok is takaríthat meg számítási erőforrásokat és képzési időt.<br/>Besorolási megengedett értékek<br/><li>LogisticRegression</li><li>SGD</li><li>MultinomialNaiveBayes</li><li>BernoulliNaiveBayes</li><li>SVM</li><li>LinearSVM</li><li>KNN</li><li>DecisionTree</li><li>RandomForest</li><li>ExtremeRandomTrees</li><li>LightGBM</li><li>GradientBoosting</li><li>TensorFlowDNN</li><li>TensorFlowLinearClassifier</li><br/>Engedélyezett értékek regresszió<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li><br/>Megengedett értékek az előrejelzés<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li>|   None
`whitelist_models`  | Automatizált machine learning-kísérlet megkísérli számos különböző algoritmus rendelkezik. Konfigurálja úgy, hogy bizonyos algoritmust a kísérlethez tartalmazza. Akkor hasznos, ha vegye figyelembe, hogy aránytól esetén működik megfelelően az adatkészlet. <br/>Besorolási megengedett értékek<br/><li>LogisticRegression</li><li>SGD</li><li>MultinomialNaiveBayes</li><li>BernoulliNaiveBayes</li><li>SVM</li><li>LinearSVM</li><li>KNN</li><li>DecisionTree</li><li>RandomForest</li><li>ExtremeRandomTrees</li><li>LightGBM</li><li>GradientBoosting</li><li>TensorFlowDNN</li><li>TensorFlowLinearClassifier</li><br/>Engedélyezett értékek regresszió<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li><br/>Megengedett értékek az előrejelzés<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li>|  None
`verbosity` |Folyamatban van a részletes és a kritikus folyamatban információval azt szabályozza, a naplózási szint a legkevésbé. Részletességi szint ugyanazokat az értékeket vesz igénybe, a python-naplózás csomagban meghatározott módon. Engedélyezett értékek a következők:<br/><li>logging.INFO</li><li>a naplózás. FIGYELMEZTETÉS</li><li>a naplózás. HIBA TÖRTÉNT</li><li>a naplózás. KRITIKUS</li>  | logging.INFO</li> 
`X` | Megkezdheti az összes funkció |  None
`y` |   Megkezdheti az adatok. A besorolás kell lennie az egész számok tömbje.|  None
`X_valid`|_Nem kötelező_ érvényesítése az összes szolgáltatás. Ha nincs megadva, az X train között van felosztva, és ellenőrzése |   None
`y_valid`   |_Nem kötelező_ a felirat adatainak ellenőrzése a. Ha nincs megadva, az y train között van felosztva, és ellenőrzése    | None
`sample_weight` |   _Nem kötelező_ súlyértéket minden mintához. Akkor használja, ha szeretné rendelni az adatpontokhoz különböző súlya |   None
`sample_weight_valid`   |   _Nem kötelező_ súlyértéket minden érvényesítési mintához. Ha nincs megadva, a sample_weight train között van felosztva, és ellenőrzés   | None
`run_configuration` |   RunConfiguration objektum.  Távoli Futtatás használja. |None
`data_script`  |    A get_data metódus tartalmazó fájl elérési útja.  Távoli Futtatás szükséges.   |None
`model_explainability` | _Nem kötelező_ igaz/hamis <br/>  Igaz lehetővé teszi, hogy minden egyes ismétléskor végrehajtani a funkció fontos kísérletezhet. Használhatja explain_model() módszert is egy adott iteráció engedélyezése funkció fontosságát, igény szerinti, az iteráció kísérlet befejezése után. | False (Hamis)
`enable_ensembling`|Ez a jelző azt ensembling iteráció engedélyezése az összes többi ismétlésének befejezését követően.| True (Igaz) 
`ensemble_iterations`|Során, ami lehetőséget választjuk, a végső ensemble részét illesztett folyamat ismétlések száma.| 15
`experiment_timeout_minutes`| Korlátozza a (minues), hogy mennyi ideig tarthat a teljes kísérlet futtatása | None

## <a name="data-pre-processing-and-featurization"></a>Üzem előtti adatfeldolgozás és a featurization

Ha `preprocess=True`, a következő adatok előfeldolgozása lépések végrehajtása automatikusan történik meg:
1.  Nagy számosságú dobja el vagy nem eltérés szolgáltatások
    * Képzés és érvényesítési csoportok elvetni a funkciókat nem hasznos információkat. Ezek többek között az összes érték hiányzik, ugyanazt az értéket az összes sor vagy a rendkívül nagy számosságú (például kivonatokat, azonosítók vagy GUID-azonosítói).
1.  Imputálási. értéke hiányzik.
    *   Numerikus funkciók imputálására a hiányzó értékek az oszlopban szereplő értékek átlaga.
    *   Kategorikus funkciók imputálására leggyakoribb értéket a hiányzó értékeket.
1.  További funkciók létrehozása
    * A dátum és idő jellemzői: Év, hónap, nap, hét napja, nap, év, negyedév, az év, óra, perc, másodperc.
    * Az SMS jellemzők: Kifejezés gyakorisága word unigram, bi-gramokat és tri-gram, Count vectorizer alapján.
1.  Átalakítások és kódolásokat
    * Numerikus nagyon kevés egyedi értékkel kategorikus funkciók alakítja át a funkciókat.
    * Függően Számosság kategorikus funkcióját hajtsa végre a kódolás vagy (ujjlenyomat) egy gyakori kódolási címke.

## <a name="run-experiment"></a>Kísérlet futtatása

Küldje el a kísérlet futtatásához és a egy modell létrehozása. Adja át a `AutoMLConfig` , a `submit` metódus a modell generálásához.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Függősége először telepítve van egy új gépen.  Mielőtt látható a kimenetre akár 10 percig is eltarthat.
>Beállítás `show_output` való `True` a konzolon látható kimenetet eredményez.


## <a name="explore-model-metrics"></a>Modell metrikák böngészése
Megtekintheti az eredményeket a widget vagy beágyazott, ha Ön történő használatát. Lásd: [nyomon követheti és értékelheti a modellek](how-to-track-experiments.md#view-run-details) további részletekért.


### <a name="classification-metrics"></a>Besorolási metrikák
A következő metrikák besorolási tevékenység minden egyes ismétléskor lesznek mentve.

|Elsődleges metrika|Leírás|Számítás|További paraméterek
--|--|--|--|--|
AUC_Macro| AUC az a terület, a fogadó működő jellemző görbe alatt. Makró minden egyes osztály a AUC számtani középértékét.  | [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlagos = "makra."|
AUC_Micro| AUC az a terület, a fogadó működő jellemző görbe alatt. Micro globably számított a valódi pozitívok és az egyes osztályok vakriasztások kombinálásával| [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlagos = "micro"|
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
norm_macro_recall|Normalizált makró visszahívása makró ne felejtse el, hogy a véletlenszerű teljesítmény 0 pontszámot pedig tökéletes teljesítmény 1 pontszámot normalized. Ez úgy érhető el, norm_macro_recall: (recall_score_macro - R) = /(1-R), ahol az R véletlenszerű ismeretekkel (azaz a R = 0,5 bináris osztályozási) és a C osztályú osztályozási problémák R=(1/C) recall_score_macro várt értéke|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "makró", majd (recall_score_macro - R) /(1-R), ahol az R véletlenszerű ismeretekkel (azaz a R = 0,5 bináris osztályozási) és a C osztályú osztályozási problémák R=(1/C) recall_score_macro várt értéke|
precision_score_macro|Pontosság a százalékos aránya a következő címkét: egy adott osztály elemeit tartalmazza ténylegesen az adott osztály. Makró az egyes osztályok pontosság számtani középértékét|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "makra."|
precision_score_micro|Pontosság a százalékos aránya a következő címkét: egy adott osztály elemeit tartalmazza ténylegesen az adott osztály. Micro globálisan számított alapján a teljes valódi pozitívok és a hamis pozitív jelzések elkerülése|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "micro"|
precision_score_weighted|Pontosság a százalékos aránya a következő címkét: egy adott osztály elemeit tartalmazza ténylegesen az adott osztály. Minden osztály az egyes osztályok igaz példányok száma szerint a pontossági számtani súlyozott van.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "súlyozott"|
recall_score_macro|Visszaírási valójában egy bizonyos osztály elemeinek megfelelően van-e jelölve, hogy százaléka. Makró az egyes osztályok visszaírási középértéke|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "makra."|
recall_score_micro|Visszaírási valójában egy bizonyos osztály elemeinek megfelelően van-e jelölve, hogy százaléka. Micro globálisan számított a teljes valódi pozitívok, a téves negatív leltár szerint|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "micro"|
recall_score_weighted|Visszaírási valójában egy bizonyos osztály elemeinek megfelelően van-e jelölve, hogy százaléka. Minden osztály az egyes osztályok igaz példányok száma szerint visszaírási számtani középértékét súlyozott van.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "súlyozott"|
weighted_accuracy|Ahol a súlyt kapnak valamennyi példa megegyezik az időarány, amíg igaz példányok adott példa igaz osztályban súlyozott pontossága pontossága|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight egy adott osztály az egyes elemekhez az időarány, amíg a célzott egyenlő vektor|

### <a name="regression-and-forecasting-metrics"></a>Regresszió és előrejelzési mérőszámok
A következő metrikák regressziós vagy előrejelzési feladat minden egyes ismétléskor lesznek mentve.

|Elsődleges metrika|Leírás|Számítás|További paraméterek
--|--|--|--|--|
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
normalized_root_mean_squared_log_error|Noramlized Root mean squared log hiba a root mean squared log hiba osztva az adatok tartományán|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Az adatok számos nullával|

## <a name="explain-the-model"></a>A modell ismertetik.

Automatizált gépi tanulási funkciók általánosan elérhetők, amíg **a modell explainability funkció nyilvános előzetes verzióként.**

Automatizált machine learning lehetővé teszi, hogy jobban megismerhesse a szolgáltatás fontosságát.  A betanítási folyamat során a globális szolgáltatás fontosság modell kérheti le.  Besorolási forgatókönyvek esetén is kaphat osztályszintű funkció fontosságát.  Meg kell adnia az érvényesítési adatkészletet (X_valid) funkció fontosság beolvasásához.

Kétféleképpen funkció fontos létrehozni.

*   Egy kísérlet után használhatja `explain_model` ismétlések metódust.

    ```
    from azureml.train.automl.automlexplainer import explain_model
    
    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)
    
    #Overall feature importance
    print(overall_imp)
    print(overall_summary) 
    
    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary) 
    ```

*   Funkció fontos összes ismétlésének megtekintéséhez állítsa `model_explainability` jelzőt `True` AutoMLConfig a.  

    ```
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train, 
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    Ha végzett, lekérni egy adott iteráció funkció fontos retrieve_model_explanation metódus használatával.

    ```
    from azureml.train.automl.automlexplainer import retrieve_model_explanation
    
    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)
    
    #Overall feature importance
    print(overall_imp)
    print(overall_summary) 
    
    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary) 
    ```

A funkció fontos diagram a munkaterületen, az Azure Portalon jelenítheti meg. A diagram is látható, a Jupyter widget a notebook használatakor. A diagramok bővebben lásd: a [minta Azure ML notebookok cikk.](samples-notebooks.md)

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![a szolgáltatás fontosság graph](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hogyan és hol érdemes a modell üzembe helyezése](how-to-deploy-and-where.md).

Tudjon meg többet [hogyan az alkalmazások automatikus machine learning egy besorolási modell betanításához](tutorial-auto-train-models.md) vagy [betanításához használatával hogyan machine learning egy távoli erőforrás az automatizált](how-to-auto-train-remote.md). 
