---
title: Automatizált Machine Learning-kísérletek létrehozása
titleSuffix: Azure Machine Learning service
description: Automatizált gépi tanulási algoritmus választja ki az Ön számára, és készen áll a központi telepítési modell generál. Ismerje meg a beállításokat, amelyek segítségével automatizált machine learning-példakísérleteket konfigurálása.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: df05bd984667283b0ccc143ba14fff6b35d69144
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753177"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Automatizált Machine Learning-kísérletek konfigurálása a Pythonban

Ebben az útmutatóban megtudhatja, hogyan határozza meg, az automatikus machine learning-példakísérleteket a különböző konfigurációs beállításait a [Azure Machine Learning-SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatizált machine learning olyan algoritmusai és hiperparaméterek kiválasztja az Ön számára, és készen áll a központi telepítési modell állít elő. Többféle módon automatizált machine learning-példakísérleteket konfigurálásához használhatja.

Egy automatizált machine learning-példakísérleteket Példák megtekintése: [oktatóanyag: Egy automatizált gépi tanulással osztályozási modell betanításához](tutorial-auto-train-models.md) vagy [és a felhőben automatizált machine learning-modellek betanításához](how-to-auto-train-remote.md).

Az automatikus machine learningben elérhető konfigurációs lehetőségek:

* A kísérlet típusának kiválasztása: Besorolás, regressziós vagy Idősorozat-előrejelzés
* Az adatforrás, formátumokat és adatok újbóli lekérésére
* Válassza a compute-cél: helyi vagy távoli
* Automatizált machine learning-kísérlet beállításai
* Egy automatizált machine learning-kísérlet futtatása
* Modell metrikák böngészése
* Regisztráljon és a modell üzembe helyezése

Ha inkább a nem kódolási felületet, is [létrehozása az automatikus machine learning-példakísérleteket az Azure Portalon](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>A kísérlet típusának kiválasztása

Mielőtt elkezdené a kísérlethez, meg kell határoznia a megoldandó, machine learning probléma típusú. Automatizált machine learning támogatja a besorolást, regressziós és előrejelzés tevékenységtípust.

Automatizált machine learning az automatizálás és a hangolási folyamat során a következő algoritmusokat támogatja. Felhasználójaként van, nem szükséges, hogy adja meg az algoritmus. DNN-algoritmusok betanítás során érhetők el, miközben automatizált ML nem építhető fel DNN-modelleket.

Besorolás | Regresszió | Idősor-előrejelzési
|-- |-- |--
[Logisztikai regresszió](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rugalmas Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rugalmas Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Színátmenet kiemelése](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Színátmenet kiemelése](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Színátmenet kiemelése](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)|[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)
[A legközelebbi szomszédok K](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[A legközelebbi szomszédok K](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[A legközelebbi szomszédok K](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineáris SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[Szabadkézi LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Szabadkézi LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-támogatás vektor besorolás (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Sztochasztikus gradiens módszeres (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Sztochasztikus gradiens módszeres (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Rendkívül véletlenszerű fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Rendkívül véletlenszerű fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Rendkívül véletlenszerű fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[A DNN osztályozó](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[A DNN regressor erre](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [A DNN regressor erre](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[A DNN lineáris osztályozó](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineáris regressor erre](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineáris regressor erre](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naiv Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Sztochasztikus gradiens módszeres (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Használja a `task` paramétert a `AutoMLConfig` konstruktor adja meg a kísérletet.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

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
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
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

Kulcs | Típus | Kölcsönösen kizárják egymást az    | Leírás
---|---|---|---
X | Pandas Dataframe vagy Numpy tömbje | data_train, a címkét, az oszlopok |  Megkezdheti az összes funkció
Y | Pandas Dataframe vagy Numpy tömbje |   label   | Megkezdheti az adatok. A besorolás kell lennie az egész számok tömbje.
X_valid | Pandas Dataframe vagy Numpy tömbje   | data_train, felirat | _Nem kötelező_ funkció az érvényesítési set adatok. Ha nincs megadva, az X train között van felosztva, és ellenőrzése
y_valid |   Pandas Dataframe vagy Numpy tömbje | data_train, felirat | _Nem kötelező_ a felirat adatainak ellenőrzése a. Ha nincs megadva, az y train között van felosztva, és ellenőrzése
sample_weight | Pandas Dataframe vagy Numpy tömbje |   data_train, a címkét, az oszlopok| _Nem kötelező_ súlyértéket minden mintához. Akkor használja, ha szeretné rendelni az adatpontokhoz különböző súlya
sample_weight_valid | Pandas Dataframe vagy Numpy tömbje | data_train, a címkét, az oszlopok |    _Nem kötelező_ súlyértéket minden érvényesítési mintához. Ha nincs megadva, a sample_weight train között van felosztva, és ellenőrzés
data_train |    Pandas Dataframe |  X, y, X_valid, y_valid |    Minden adat (funkciók és címke) rendelkező betanítása
label | sztring  | X, y, X_valid, y_valid |  A data_train melyik oszlop felel meg a címke
Oszlopok | karakterláncok tömbje  ||  _Nem kötelező_ oszlopok engedélyezett funkciók használata
cv_splits_indices   | Egész számok tömbje ||  _Nem kötelező_ indexek kell felosztani az adatokat az érvényesítési közötti listája

## <a name="train-and-validation-data"></a>Adatok train és érvényesítése

Külön vonat és érvényesítési beállítása get_data() vagy közvetlenül is megadhat a `AutoMLConfig` metódust.

### <a name="k-folds-cross-validation"></a>K – Modellrész keresztellenőrzés

Használat `n_cross_validations` ellenőrzések közötti számát adja meg a beállítást. A tanítási adathalmazt lesz véletlenszerűen felosztva `n_cross_validations` modellrész egyenlő méretű. Során minden egyes keresztellenőrzési round egyet a kevesebb modellrészt használható ellenőrzés céljából a többi modellrész tanított modell. Ez a folyamat ismétlődik a `n_cross_validations` kerekít mindaddig, amíg minden modellrészek érvényesítési beállított egyszer használatos. Az összes átlagos pontszámok `n_cross_validations` kerekít lesz jelentve, és a megfelelő modellt fog retrained a a egészében betanítási adatok készlete.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo kereszt-ellenőrzés (véletlenszerű ismétlődő alárendelt mintavételi)

Használjon `validation_size` százalékos aránya az érvényesítés, és használja a használandó képzési adatkészlet megadásához `n_cross_validations` közötti ellenőrzések száma. Egyes során keresztellenőrzés round, egy részhalmazát méretű `validation_size` ellenőrzés céljából a többi adat tanított modell a rendszer véletlenszerűen választ. Végül, az átlagos pontszámmodell összes `n_cross_validations` kerekít lesz jelentve, és a megfelelő modellt fog retrained a a egészében betanítási adatok készlete. Monte Carlo idősorozat-előrejelzés nem támogatott.

### <a name="custom-validation-dataset"></a>Egyéni ellenőrzési adatkészlet

Egyéni ellenőrzési adatkészlet, ha véletlenszerű split nem elfogadható, általában idősorozat-adatok vagy imbalanced adatokat használja. A saját érvényesítési adatkészletet is megadhat. A modellben értékelik az érvényesítési élelmiszer-megadott véletlenszerű adatkészlet helyett.

## <a name="compute-to-run-experiment"></a>Számítási kísérlet futtatása

Ezután határozza meg, hol kell betanítani a modellt. Egy automatizált machine learning betanítási kísérlet futtassa a következő számítási lehetőségek:
*   A helyi gépen, például a helyi számítógépen vagy laptopon – általában amikor rendelkezik kisméretű adatkészlet, és továbbra is a feltárás fázisban.
*   Egy távoli gépen a felhőben – [Azure Machine Learning-felügyelt számítási](concept-compute-target.md#amlcompute) egy felügyelt szolgáltatás, amely lehetővé teszi a Azure-beli virtuális gépek fürtjein gépi tanulási modelleket taníthat be.

Tekintse meg a [a GitHub-webhelyről](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) például a helyi és távoli notebookok számítási céljainak.

*   Az Azure Databricks-fürt az Azure-előfizetésében. További információk itt - [ML automatikus beállítása az Azure Databricks-fürtjében](how-to-configure-environment.md#azure-databricks)

Tekintse meg a [a GitHub-webhelyről](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) például az Azure Databricks-jegyzetfüzeteket.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>A kísérlet beállításainak konfigurálása

Többféle módon használhatja az automatikus machine learning-kísérlet konfigurálása. Ezeket a paramétereket állítja be hárítható el egy `AutoMLConfig` objektum. Tekintse meg a [AutoMLConfig osztály](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) paraméterek teljes listája.

Néhány példa:

1.  Besorolási kísérlet az elsődleges metrika és a maximális száma az iteráció és a Befejezés után 50 ismétlési és 2 keresztellenőrzési modellrész kísérletet 12 000 másodperc idő szerint súlyozott AUC használatával.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  Alább egy például 100 közelítő befejezéséhez regressziós kísérlet meg, a minden egyes ismétléskor tartó legfeljebb 600 másodperc az 5-érvényesítési adatbázisközi modellrész.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

A három különböző `task` paraméter határozza meg, hogy a lista a alkalmazni algoritmusok.  Használja a `whitelist` vagy `blacklist` további módosításához az elérhető algoritmusokat vagy kizárja a végrehajtandó iterációk paramétereket. Támogatott modellek listája található [SupportedAlgorithms osztály](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Elsődleges metrika
Az elsődleges metrika; ahogyan az a fenti példák meghatározza, hogy a metrika az optimalizálás használható modell betanítása közben. Úgy dönt, feladattípus kiválaszthatja az elsődleges metrika határozza meg. Az alábbi, az elérhető mérőszámok listája.

|Besorolás | Regresszió | Idősor-előrejelzési
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Adatok előfeldolgozása és featurization

Minden egyes automatizált machine learning-kísérlet, az adatok az [automatikus méretezését és normalized](concept-automated-ml.md#preprocess) jól algoritmusok segítségével.  Azonban is engedélyezheti további előfeldolgozása/featurization, például a hiányzó értékeket imputálási, kódolási és átalakításokat. [További tudnivalók a milyen featurization megtalálható](how-to-create-portal-experiments.md#preprocess).

Ahhoz, hogy ez featurization, adja meg a `"preprocess": True` számára a [ `AutoMLConfig` osztály](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Idősor-előrejelzési
A time series előrejelzési feladattípus van további paraméterek meghatározására.
1. time_column_name – Ez az egyik kötelező paraméter, amely meghatározza az oszlop neve, a tanítási adatokat tartalmazó dátum/idő sorozat.
1. max_horizon – Ez határozza meg a mennyi ideig előre jelezni kívánt meg az ismétlődési gyakoriságára vonatkozóan a betanítási adatok alapján. Például ha napi idő szemek a betanítási adatok, milyen ki az meghatározott azt szeretné, hogy a modell betanításához az nap.
1. grain_column_names – Ez meghatározza a betanítási adatok az egyes idősoros adatokat tartalmazó oszlop nevével. Például egy adott márka áruház értékesítés vannak előrejelzés, tároló és a márka oszlopok a a időfelbontási szint oszlopként, hogy lenne definiálhat.

Példa ezek alatt használt beállításokat, a jegyzetfüzet érhető el minta [Itt](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

## <a name="run-experiment"></a>Kísérlet futtatása

Küldje el a kísérlet futtatásához és a egy modell létrehozása. Adja át a `AutoMLConfig` , a `submit` metódus a modell generálásához.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Függősége először telepítve van egy új gépen.  Mielőtt látható a kimenetre akár 10 percig is eltarthat.
>Beállítás `show_output` való `True` a konzolon látható kimenetet eredményez.

### <a name="exit-criteria"></a>Kilépési feltételek
Van néhány lehetőség meghatározhatja a kísérlet elvégzéséhez.
1. Nincsenek feltételek – Ha nem határoznak meg bármelyik lépjen ki a paramétereket addig, amíg a további előrehaladást nem található az elsődleges metrika továbbra is a kísérletet.
1. Szám ismétlésszámot – meghatározhatja a kísérlet futtatásához az ismétlések száma. Nem kötelező is határozhat meg egy adott időkorláton perc / minden egyes ismétléskor iteration_timeout_minutes hozzáadása.
1. Kilépés után egy bizonyos hosszúságú időszeletet - experiment_timeout_minutes használatával adhatja meg hogy mennyi ideig perc kísérlet továbbra is a Futtatás a között.
1. Kilépés után egy pontszám elérte - dönthet úgy, hogy a kísérlet befejezése után a rendszer elérte az elsődleges mérőszám alapján pontszámot experiment_exit_score használatával.

### <a name="explore-model-metrics"></a>Modell metrikák böngészése

Megtekintheti a betanítási eredmények widget vagy beágyazott, ha Ön történő használatát. Lásd: [nyomon követheti és értékelheti a modellek](how-to-track-experiments.md#view-run-details) további részletekért.

## <a name="understand-automated-ml-models"></a>Automatizált gépi Tanulási modelleket ismertetése

Bármilyen automatikus Machine Learning használatával létrehozott modell tartalmazza-e az alábbi lépéseket:
+ Automatikus funkciófejlesztési (ha előfeldolgozása = True)
+ Skálázás/normalizálási és algoritmus hypermeter értékekkel

Elérhetővé, amelyekkel átlátható ezeket az információkat kérhet automatizált ML fitted_model kimenete.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Automatizált funkciófejlesztési

Előfeldolgozási listájának megtekintéséhez és [funkciófejlesztési automatikus](concept-automated-ml.md#preprocess) , amely akkor fordul elő, amikor előfeldolgozása = True.

Ebben a példában, vegye figyelembe:
+ Nincsenek 4 bemeneti funkciói: Egy (numerikus) (numerikus) B, C (numerikus), D (dátum és idő)
+ Numerikus szolgáltatás C el van dobva, mert a azonosító oszlop minden egyedi értékekkel
+ Numerikus funkciók A és B hiányzó értékeket, és ezért az vannak imputált középérték
+ Dátum és idő D funkció featurized 11 különböző visszafejtett funkciók be

Használja az alábbi 2 illesztett modell az első lépés az API-k segítségével ismerje meg jobban az.  Lásd: [minta Jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ 1\. API: `get_engineered_feature_names()` visszafejtett neveinek listáját adja vissza.

  Használat
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Ez a lista tartalmazza az összes visszafejtett szolgáltatások neveit.

  >[!Note]
  >Feladathoz használni 'timeseriestransformer' = 'előrejelzés", más használata"datatransformer' "regressziós" vagy "osztályozás" feladathoz.

+ 2\. API: `get_featurization_summary()` featurization bemeneti szolgáltatások összegzését adja vissza.

  Használat
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Feladathoz használni 'timeseriestransformer' = 'előrejelzés", más használata"datatransformer' "regressziós" vagy "osztályozás" feladathoz.

  Kimenet:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Az elemek magyarázata:

   |Kimenet|Meghatározás|
   |----|--------|
   |RawFeatureName|A bemeneti funkció vagy az oszlop neve a megadott adatkészletből.|
   |TypeDetected|A bemeneti funkció észlelt adattípusának.|
   |Dropped|Azt jelzi, ha a bemeneti funkció eldobása, vagy használja.|
   |EngineeringFeatureCount|Több automatikus szolgáltatás mérnöki átalakítások során generált funkciót.|
   |Átalakítások|Alkalmazott bemeneti szolgáltatások készítése a visszafejtett funkciók átalakításokat listája.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Skálázás/normalizálási és algoritmus hypermeter értékekkel:

Szeretné megtudni, a folyamat normalizálási/méretezés és algoritmus/hiperparaméter értékei, fitted_model.steps használja. [További tudnivalók a skálázás/normalizálási](concept-automated-ml.md#preprocess). Itt látható egy mintakimenet:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

További részletek, amelyet a segédfüggvény látható [minta Jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint
def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(fitted_model)
```

A következő egy minta kimenet az egy folyamatot egy adott (RobustScalar, ebben az esetben a LogisticRegression) algoritmus használatával.

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>Ismertetik a modell (e)

Automatizált machine learning lehetővé teszi, hogy jobban megismerhesse a szolgáltatás fontosságát.  A betanítási folyamat során a globális szolgáltatás fontosság modell kérheti le.  Besorolási forgatókönyvek esetén is kaphat osztályszintű funkció fontosságát.  Meg kell adnia az érvényesítési adatkészletet (X_valid) funkció fontosság beolvasásához.

Kétféleképpen funkció fontos létrehozni.

*   Egy kísérlet után használhatja `explain_model` ismétlések metódust.

    ```python
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

    ```python
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

    ```python
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

A funkció fontos diagram a munkaterületen, az Azure Portalon jelenítheti meg. A diagram is látható, a Jupyter widget a notebook használatakor. A diagramok bővebben lásd: a [minta Azure Machine Learning szolgáltatás notebookok cikk.](samples-notebooks.md)

```Python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![a szolgáltatás fontosság graph](./media/how-to-configure-auto-train/feature-importance.png)

Hogyan modell magyarázatokat és a szolgáltatás fontosság engedélyezhető az SDK automatizált machine learning-en kívül más területeken további információkért lásd: a [fogalom](machine-learning-interpretability-explainability.md) e című cikket.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hogyan és hol érdemes a modell üzembe helyezése](how-to-deploy-and-where.md).

Tudjon meg többet [hogyan az alkalmazások automatikus machine learning egy regressziós modell betanításához](tutorial-auto-train-models.md) vagy [betanításához használatával hogyan machine learning egy távoli erőforrás az automatizált](how-to-auto-train-remote.md).
