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
ms.openlocfilehash: 194902cfa2992e4370b68bf140ec3a5e03f364ca
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597683"
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
X_valid | Pandas Dataframe vagy Numpy tömbje   | data_train, felirat | _Nem kötelező_ érvényesítése az összes szolgáltatás. Ha nincs megadva, az X train között van felosztva, és ellenőrzése
y_valid |   Pandas Dataframe vagy Numpy tömbje | data_train, felirat | _Nem kötelező_ a felirat adatainak ellenőrzése a. Ha nincs megadva, az y train között van felosztva, és ellenőrzése
sample_weight | Pandas Dataframe vagy Numpy tömbje |   data_train, a címkét, az oszlopok| _Nem kötelező_ súlyértéket minden mintához. Akkor használja, ha szeretné rendelni az adatpontokhoz különböző súlya
sample_weight_valid | Pandas Dataframe vagy Numpy tömbje | data_train, a címkét, az oszlopok |    _Nem kötelező_ súlyértéket minden érvényesítési mintához. Ha nincs megadva, a sample_weight train között van felosztva, és ellenőrzés
data_train |    Pandas Dataframe |  X, y, X_valid, y_valid |    Minden adat (funkciók és címke) rendelkező betanítása
label | sztring  | X, y, X_valid, y_valid |  A data_train melyik oszlop felel meg a címke
Oszlopok | karakterláncok tömbje  ||  _Nem kötelező_ oszlopok engedélyezett funkciók használata
cv_splits_indices   | Egész számok tömbje ||  _Nem kötelező_ indexek kell felosztani az adatokat az érvényesítési közötti listája

### <a name="load-and-prepare-data-using-data-prep-sdk"></a>Betölteni és adatelőkészítéshez SDK használata az adatok előkészítése
Automatizált machine learning-példakísérleteket támogatja az adatok betöltése és alakítja át a az adatelőkészítés SDK használatával. Az SDK-val lehetővé teszi a

>* Elemzés paraméter következtetésekhez (kódolás, elválasztó, a fejlécek) rendelkező több fájltípus betölthető
>* Típus-átalakítás következtetésekhez használatával fájl betöltése közben
>* MS SQL Server és az Azure Data Lake Storage kapcsolat támogatása
>* Az autorefresh tulajdonság oszlop hozzáadása
>* Hiányzó imputálására
>* Oszlopok származtatása példa alapján
>* Szűrés
>* Egyéni Python-átalakítások

További információ az adatok előkészítési sdk tekintse meg a [hogyan készíti elő az adatok modellezését, ahol a cikk](how-to-load-data.md).
Alább látható egy példa, adat-előkészítési sdk használata az adatok betöltése.
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

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo kereszt-ellenőrzés (véletlenszerű ismétlődő alárendelt mintavételi)

Használjon `validation_size` százalékos aránya az érvényesítés, és használja a használandó képzési adatkészlet megadásához `n_cross_validations` közötti ellenőrzések száma. Egyes során keresztellenőrzés round, egy részhalmazát méretű `validation_size` ellenőrzés céljából a többi adat tanított modell a rendszer véletlenszerűen választ. Végül, az átlagos pontszámmodell összes `n_cross_validations` kerekít lesz jelentve, és a megfelelő modellt fog retrained a a egészében betanítási adatok készlete. Monte Carlo idősorozat-előrejelzés nem támogatott.

### <a name="custom-validation-dataset"></a>Egyéni ellenőrzési adatkészlet

Egyéni ellenőrzési adatkészlet, ha véletlenszerű split nem elfogadható, általában idősorozat-adatok vagy imbalanced adatokat használja. A saját érvényesítési adatkészletet is megadhat. A modellben értékelik az érvényesítési élelmiszer-megadott véletlenszerű adatkészlet helyett.

## <a name="compute-to-run-experiment"></a>Számítási kísérlet futtatása

Ezután határozza meg, hol kell betanítani a modellt. Egy automatizált machine learning betanítási kísérlet futtassa a következő számítási lehetőségek:
*   A helyi gépen, például a helyi számítógépen vagy laptopon – általában amikor rendelkezik kisméretű adatkészlet, és továbbra is a feltárás fázisban.
*   Egy távoli gépen a felhőben – [Azure Machine Learning-felügyelt számítási](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) egy felügyelt szolgáltatás, amely lehetővé teszi a Azure-beli virtuális gépek fürtjein gépi tanulási modelleket taníthat be.

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

## <a name="primary-metric"></a>Elsődleges metrika
Az elsődleges metrika; ahogyan az a fenti példák meghatározza, hogy a metrika az optimalizálás használható modell betanítása közben. Úgy dönt, feladattípus kiválaszthatja az elsődleges metrika határozza meg. Az alábbi, az elérhető mérőszámok listája.

|Besorolás | Regresszió | Idősor-előrejelzési
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

## <a name="data-preprocessing--featurization"></a>Adatok előfeldolgozása és featurization

Minden egyes automatizált machine learning-kísérlet, az adatok az [automatikus méretezését és normalized](concept-automated-ml.md#preprocess) jól algoritmusok segítségével.  Azonban is engedélyezheti további előfeldolgozása/featurization, például a hiányzó értékeket imputálási, kódolási és átalakításokat. [További tudnivalók a milyen featurization megtalálható](how-to-create-portal-experiments.md#preprocess). 

Ahhoz, hogy ez featurization, adja meg a `"preprocess": True` számára a [ `AutoMLConfig` osztály](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="time-series-forecasting"></a>Idősor-előrejelzési
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

## <a name="exit-criteria"></a>Kilépési feltételek 
Van néhány lehetőség meghatározhatja a kísérlet elvégzéséhez.
1. Nincsenek feltételek – Ha nem határoznak meg bármelyik lépjen ki a paramétereket addig, amíg a további előrehaladást nem található az elsődleges metrika továbbra is a kísérletet. 
1. Szám ismétlésszámot – meghatározhatja a kísérlet futtatásához az ismétlések száma. Nem kötelező is határozhat meg egy adott időkorláton perc / minden egyes ismétléskor iteration_timeout_minutes hozzáadása.
1. Kilépés után egy bizonyos hosszúságú időszeletet - experiment_timeout_minutes használatával adhatja meg hogy mennyi ideig perc kísérlet továbbra is a Futtatás a között.
1. Kilépés után egy pontszám elérte - dönthet úgy, hogy a kísérlet befejezése után a rendszer elérte az elsődleges mérőszám alapján pontszámot experiment_exit_score használatával.

## <a name="explore-model-metrics"></a>Modell metrikák böngészése
Megtekintheti az eredményeket a widget vagy beágyazott, ha Ön történő használatát. Lásd: [nyomon követheti és értékelheti a modellek](how-to-track-experiments.md#view-run-details) további részletekért.


### <a name="classification-metrics"></a>Besorolási metrikák
A következő metrikák besorolási tevékenység minden egyes ismétléskor lesznek mentve.

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

### <a name="regression-and-time-series-forecasting-metrics"></a>Regresszió és idő series előrejelzési mérőszámok
A következő metrikák regressziós vagy előrejelzési feladat minden egyes ismétléskor lesznek mentve.

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

+ 1. API: `get_engineered_feature_names()` visszafejtett neveinek listáját adja vissza.

  Használat: 
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Ez a lista tartalmazza az összes visszafejtett szolgáltatások neveit.

  >[!Note]
  >Feladathoz használni 'timeseriestransformer' = 'előrejelzés", más használata"datatransformer' "regressziós" vagy "osztályozás" feladathoz. 

+ 2. API: `get_featurization_summary()` featurization bemeneti szolgáltatások összegzését adja vissza.

  Használat: 
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

Íme egy minta kimenet:

+ A folyamat egy adott (ebben az esetben RobustScalar a LogisticRegression) algoritmus használatával:

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
  
+ A folyamat ensemble megközelítéssel: Ebben az esetben egy közelmúltra 2 különböző folyamatok

  ```
  prefittedsoftvotingclassifier
  {'estimators': ['1', '18'],
  'weights': [0.6666666666666667,
              0.3333333333333333]}

  1 - RobustScaler
  {'copy': True,
   'quantile_range': [25, 75],
   'with_centering': True,
   'with_scaling': False}
  
  1 - LightGBMClassifier
  {'boosting_type': 'gbdt',
   'class_weight': None,
   'colsample_bytree': 0.2977777777777778,
   'importance_type': 'split',
   'learning_rate': 0.1,
   'max_bin': 30,
   'max_depth': 5,
   'min_child_samples': 6,
   'min_child_weight': 5,
   'min_split_gain': 0.05263157894736842,
   'n_estimators': 200,
   'n_jobs': 1,
   'num_leaves': 176,
   'objective': None,
   'random_state': None,
   'reg_alpha': 0.2631578947368421,
   'reg_lambda': 0,
   'silent': True,
   'subsample': 0.8415789473684211,
   'subsample_for_bin': 200000,
   'subsample_freq': 0,
   'verbose': -10}
  
  18 - StandardScalerWrapper
  {'class_name': 'StandardScaler',
   'copy': True,
   'module_name': 'sklearn.preprocessing.data',
   'with_mean': True,
   'with_std': True}
  
  18 - LightGBMClassifier
  {'boosting_type': 'goss',
   'class_weight': None,
   'colsample_bytree': 0.2977777777777778,
   'importance_type': 'split',
   'learning_rate': 0.07894947368421053,
   'max_bin': 30,
   'max_depth': 6,
   'min_child_samples': 47,
   'min_child_weight': 0,
   'min_split_gain': 0.2631578947368421,
   'n_estimators': 400,
   'n_jobs': 1,
   'num_leaves': 14,
   'objective': None,
   'random_state': None,
   'reg_alpha': 0.5789473684210527,
   'reg_lambda': 0.7894736842105263,
   'silent': True,
   'subsample': 1,
   'subsample_for_bin': 200000,
   'subsample_freq': 0,
   'verbose': -10}
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

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hogyan és hol érdemes a modell üzembe helyezése](how-to-deploy-and-where.md).

Tudjon meg többet [hogyan az alkalmazások automatikus machine learning egy regressziós modell betanításához](tutorial-auto-train-models.md) vagy [betanításához használatával hogyan machine learning egy távoli erőforrás az automatizált](how-to-auto-train-remote.md).
