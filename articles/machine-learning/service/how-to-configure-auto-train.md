---
title: Automatizált ML-kísérletek létrehozása
titleSuffix: Azure Machine Learning service
description: Automatizált gépi tanulási algoritmus választja ki az Ön számára, és készen áll a központi telepítési modell generál. Ismerje meg a beállításokat, amelyek segítségével automatizált machine learning-példakísérleteket konfigurálása.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 5dee966f8664bc14d81004e625ad9632066ffcb2
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742306"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Automatizált ML-kísérletek konfigurálása a Pythonban

Ebből az útmutatóból megtudhatja, hogyan határozhatja meg az automatizált gépi tanulási kísérletek különböző konfigurációs beállításait a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-val. Automatizált machine learning olyan algoritmusai és hiperparaméterek kiválasztja az Ön számára, és készen áll a központi telepítési modell állít elő. Többféle módon automatizált machine learning-példakísérleteket konfigurálásához használhatja.

Az automatizált gépi tanulási kísérletek példáinak megtekintéséhez tekintse [meg az oktatóanyagot: Besorolási modellt is betaníthat automatizált](tutorial-auto-train-models.md) gépi tanulási vagy tanítási modellekkel [a felhőben](how-to-auto-train-remote.md).

Az automatikus machine learningben elérhető konfigurációs lehetőségek:

* Válassza ki a kísérlet típusát: Besorolás, regresszió vagy idősorozat-előrejelzés
* Az adatforrás, formátumokat és adatok újbóli lekérésére
* Válassza a compute-cél: helyi vagy távoli
* Automatizált machine learning-kísérlet beállításai
* Egy automatizált machine learning-kísérlet futtatása
* Modell metrikák böngészése
* Regisztráljon és a modell üzembe helyezése

Ha nem szeretne programkódot felvenni, akkor [a Azure Portal is létrehozhatja az automatizált gépi tanulási kísérleteket](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>A kísérlet típusának kiválasztása

Mielőtt elkezdené a kísérlethez, meg kell határoznia a megoldandó, machine learning probléma típusú. Automatizált machine learning támogatja a besorolást, regressziós és előrejelzés tevékenységtípust.

Automatizált machine learning az automatizálás és a hangolási folyamat során a következő algoritmusokat támogatja. Felhasználójaként van, nem szükséges, hogy adja meg az algoritmus.

Besorolás | Regresszió | Idősorozat-előrejelzés
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
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN osztályozó](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN lineáris osztályozó](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineáris Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineáris Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naiv Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Sztochasztikus gradiens módszeres (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

A kísérlet típusának megadásához használja a `AutoMLConfig` konstruktor paraméterét.`task`

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

Távoli végrehajtáshoz a távoli számításból elérhetővé kell tennie az adatok elérését. Ezt az adattárolóba való feltöltéssel teheti meg.

Íme egy példa a használatára `datastore`:

```python
    import pandas as pd
    from sklearn import datasets

    data_train = datasets.load_digits()

    pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
    pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)
```

### <a name="define-dprep-references"></a>Dprep-hivatkozások definiálása

Az X és az y meghatározása dprep-hivatkozásként, amelyet az alábbihoz hasonló `AutoMLConfig` automatizált Machine learning-objektumnak továbbít a rendszer:

```python

    X = dprep.auto_read_file(path=ds.path('digitsdata/X_train.csv'))
    y = dprep.auto_read_file(path=ds.path('digitsdata/y_train.csv'))


    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 path = project_folder,
                                 run_configuration=conda_run_config,
                                 X = X,
                                 y = y,
                                 **automl_settings
                                )
```

## <a name="train-and-validation-data"></a>Adatok train és érvényesítése

A metódusban közvetlenül is megadhatja a `AutoMLConfig` külön betanítási és érvényesítési készletet.

### <a name="k-folds-cross-validation"></a>K – Modellrész keresztellenőrzés

Használat `n_cross_validations` ellenőrzések közötti számát adja meg a beállítást. A tanítási adathalmazt lesz véletlenszerűen felosztva `n_cross_validations` modellrész egyenlő méretű. Során minden egyes keresztellenőrzési round egyet a kevesebb modellrészt használható ellenőrzés céljából a többi modellrész tanított modell. Ez a folyamat ismétlődik a `n_cross_validations` kerekít mindaddig, amíg minden modellrészek érvényesítési beállított egyszer használatos. Az összes átlagos pontszámok `n_cross_validations` kerekít lesz jelentve, és a megfelelő modellt fog retrained a a egészében betanítási adatok készlete.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo Cross Validation (ismétlődő véletlenszerű almintavételezés)

Használjon `validation_size` százalékos aránya az érvényesítés, és használja a használandó képzési adatkészlet megadásához `n_cross_validations` közötti ellenőrzések száma. Egyes során keresztellenőrzés round, egy részhalmazát méretű `validation_size` ellenőrzés céljából a többi adat tanított modell a rendszer véletlenszerűen választ. Végül, az átlagos pontszámmodell összes `n_cross_validations` kerekít lesz jelentve, és a megfelelő modellt fog retrained a a egészében betanítási adatok készlete. A Monte Carlo nem támogatott az idősorozat-előrejelzéshez.

### <a name="custom-validation-dataset"></a>Egyéni ellenőrzési adatkészlet

Használjon egyéni ellenőrzési adatkészletet, ha a véletlenszerű felosztás nem fogadható el, általában idősorozat-vagy kiegyensúlyozatlan adat. A saját érvényesítési adatkészletet is megadhat. A modellben értékelik az érvényesítési élelmiszer-megadott véletlenszerű adatkészlet helyett.

## <a name="compute-to-run-experiment"></a>Számítási kísérlet futtatása

Ezután határozza meg, hol kell betanítani a modellt. Egy automatizált machine learning betanítási kísérlet futtassa a következő számítási lehetőségek:
*   A helyi gépen, például a helyi számítógépen vagy laptopon – általában amikor rendelkezik kisméretű adatkészlet, és továbbra is a feltárás fázisban.
*   Egy távoli gépen a felhőben – [Azure Machine Learning-felügyelt számítási](concept-compute-target.md#amlcompute) egy felügyelt szolgáltatás, amely lehetővé teszi a Azure-beli virtuális gépek fürtjein gépi tanulási modelleket taníthat be.

Tekintse meg a [GitHub-webhelyet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) , például a helyi és távoli számítási célokat szolgáló jegyzetfüzeteket.

*   Egy Azure Databricks-fürt az Azure-előfizetésében. További részleteket itt talál [: Azure Databricks-fürt beállítása AUTOMATIZÁLT ml-hez](how-to-configure-environment.md#azure-databricks)

Tekintse meg a [GitHub-webhelyet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) , például jegyzetfüzeteket Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>A kísérlet beállításainak konfigurálása

Többféle módon használhatja az automatikus machine learning-kísérlet konfigurálása. Ezeket a paramétereket állítja be hárítható el egy `AutoMLConfig` objektum. A paraméterek teljes listájáért tekintse meg a [AutoMLConfig osztályt](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) .

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

A három különböző `task` paraméter értéke határozza meg az alkalmazandó algoritmusok listáját.  A vagy `whitelist` `blacklist` a paraméterrel további módosításokat is módosíthat az elérhető algoritmusok használatával a belefoglaláshoz vagy kizáráshoz. A támogatott modellek listája a [SupportedAlgorithms osztályban](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py)található.

### <a name="primary-metric"></a>Elsődleges metrika
Az elsődleges metrika; ahogy az a fenti példákban is látható, az optimalizálásra szolgáló modell képzése során használandó mérőszámot határozza meg. A kiválasztható elsődleges metrikát a választott feladattípus határozza meg. Alább látható az elérhető metrikák listája.

|Besorolás | Regresszió | Idősorozat-előrejelzés
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Adatfeldolgozási & featurization

Az automatizált gépi tanulási kísérletek során az adatok [automatikusan méretezhetők és normalizálva](concept-automated-ml.md#preprocess) vannak, hogy az algoritmusok jól elvégezhetők legyenek.  Ugyanakkor további előfeldolgozási/featurization is engedélyezheti, például hiányzó értékeket imputálási, kódolást és átalakításokat. [További információ arról, hogy milyen featurization tartalmaz](how-to-create-portal-experiments.md#preprocess).

A featurization engedélyezéséhez határozza meg `"preprocess": True` az [ `AutoMLConfig` osztályt](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Idősorozat-előrejelzés
Az idősorozat-előrejelzési feladattípushoz további paramétereket kell megadni.
1. time_column_name – ez egy kötelező paraméter, amely meghatározza a dátum/idő adatsorozatot tartalmazó betanítási adatok oszlopának nevét.
1. max_horizon – ez határozza meg, hogy mennyi idő elteltével kívánja előre jelezni a betanítási adatmennyiséget. Ha például napi időkeretekkel rendelkező betanítási információkkal rendelkezik, meghatározhatja, hogy a modell milyen mértékben legyen betanítva.
1. grain_column_names – ez határozza meg az oszlopok nevét, amelyekben a betanítási adataiban az egyes idősorozatok adatai szerepelnek. Ha például egy adott márka értékesítési adatait az áruházban szeretné megtekinteni, a tároló és a márka oszlopokat a gabona oszlopaiban definiálhatja.

Tekintse meg az alább használt beállítások példáját, a jegyzetfüzet példája [itt](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)érhető el.

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

### <a name="ensemble"></a>Ensemble-konfiguráció

Az Ensemble-modellek alapértelmezés szerint engedélyezve vannak, és az automatikus gépi tanulás futtatásakor a végső futtatási ismétlésként jelennek meg. A jelenleg támogatott Ensemble-módszerek a szavazás és a halmozás. A szavazás a súlyozott átlagokat használó, Soft-szavazóként van megvalósítva, és a halmozási implementáció két rétegbeli implementációt használ, ahol az első réteg ugyanazokkal a modellekkel rendelkezik, mint a szavazói együttes, és a második réteg modellje a következő optimális kombinációját használja: az első réteg modelljei. Ha ONNX-modelleket használ, **vagy** ha a modell-magyarázat engedélyezve van, a halmozás le lesz tiltva, és csak a szavazást fogja használni.

Több alapértelmezett argumentum is megadható, mint `kwargs` egy `AutoMLConfig` olyan objektumban, amely megváltoztatja az alapértelmezett stack Ensemble viselkedését.

* `stack_meta_learner_type`: a meta-Learner egy modell, amely az egyes különböző-modellek kimenetére van kiképezve. Az alapértelmezett meta-tanulók `LogisticRegression` a besorolási feladatokhoz (vagy `LogisticRegressionCV` ha a többszörös ellenőrzés engedélyezve `ElasticNet` van), valamint a regresszió/előrejelzési feladatokhoz (vagy `ElasticNetCV` ha a kereszt-ellenőrzés engedélyezve van). Ez a paraméter a következő karakterláncok egyike lehet `LogisticRegression`: `LogisticRegressionCV` `LightGBMClassifier`, `LightGBMRegressor` `ElasticNet` `ElasticNetCV`,,,, vagy `LinearRegression`.
* `stack_meta_learner_train_percentage`: meghatározza a betanítási készletnek azt az arányát (a betanítási típus kiválasztásakor), amelyet a meta-tanuló képzéséhez kell foglalni. Az `0.2`alapértelmezett érték:.
* `stack_meta_learner_kwargs`: választható paraméterek, amelyeket át kell adni a meta-Learner inicializálásához. Ezek a paraméterek és paraméterek típusú tükrözések a megfelelő modell konstruktorával, és a modell konstruktorának továbbítva lesznek.

Az alábbi kód egy példát mutat be az egyéni Ensemble viselkedésének megadására egy `AutoMLConfig` objektumban.

```python
ensemble_settings = {
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Az Ensemble-képzés alapértelmezés szerint engedélyezve van, de a és `enable_voting_ensemble` `enable_stack_ensemble` a logikai paraméterek használatával letiltható.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Kísérlet futtatása

Az automatikus ml `Workspace` -ben létrehoz `Experiment` egy objektumot, amely egy megnevezett objektum, amely a kísérletek futtatására szolgál.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Küldje el a kísérlet futtatásához és a egy modell létrehozása. Adja át a `AutoMLConfig` , a `submit` metódus a modell generálásához.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Függősége először telepítve van egy új gépen.  Mielőtt látható a kimenetre akár 10 percig is eltarthat.
>Beállítás `show_output` való `True` a konzolon látható kimenetet eredményez.

### <a name="exit-criteria"></a>Kilépési feltételek
A kísérlet befejezéséhez több lehetőség is megadható.
1. Nincs feltétel – ha nem ad meg kilépési paramétereket, a kísérlet addig folytatódik, amíg az elsődleges metrika nem végez további előrehaladást.
1. Ismétlések száma – megadhatja a kísérlet futtatásához szükséges iterációk számát. A iteration_timeout_minutes hozzáadása opcionálisan percek alatt meghatározható az egyes iterációk időkorlátja.
1. Kilépés hosszú idő elteltével – a beállításokban megadott experiment_timeout_minutes használatával meghatározhatja, hogy a rendszer hány perc elteltével folytassa a kísérlet futtatását.
1. Kilépés egy pontszám elérésekor – a experiment_exit_score használatával elvégezheti a kísérlet befejezését, miután az elsődleges metrika alapján elérte a pontszám értékét.

### <a name="explore-model-metrics"></a>Modell metrikák böngészése

Megtekintheti a betanítási eredményeket egy widgetben vagy beágyazottan, ha egy jegyzetfüzetben van. Lásd: [nyomon követheti és értékelheti a modellek](how-to-track-experiments.md#view-run-details) további részletekért.

## <a name="understand-automated-ml-models"></a>Az automatizált ML-modellek ismertetése

Az automatikus ML használatával előállított modellek a következő lépéseket tartalmazzák:
+ Automatizált funkciók tervezése (ha az előfeldolgozás = true)
+ Skálázás/normalizálás és algoritmus hypermeter-értékekkel

Ezt az információt a fitted_model-kimenetről az automatikus ML-ből átláthatóvá tesszük.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Automatizált funkciók tervezése

Tekintse meg az előfeldolgozás és az [automatizált funkcióinak](concept-automated-ml.md#preprocess) listáját, amely akkor történik meg, amikor az előfeldolgozás = True.

Megfontolandó példa:
+ 4 bemeneti funkció létezik: A (numerikus), B (numerikus), C (numerikus), D (DateTime)
+ A C numerikus funkció el lett dobva, mert egy azonosító oszlop, amely minden egyedi értéket tartalmaz.
+ Az A és B számú numerikus funkció hiányzó értékeket tartalmaz, ezért az A Mean
+ A D dátum/idő funkció featurized 11 különböző mérnöki funkcióval rendelkezik

A beszerelt modell első lépéseként használja ezt a 2 API-t, hogy jobban megértsen.  Tekintse meg [ezt a minta notebookot](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ 1\. API `get_engineered_feature_names()` : a meglevő szolgáltatások neveinek listáját adja vissza.

  Használat:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Ez a lista tartalmazza az összes megtervezett szolgáltatás nevét.

  >[!Note]
  >Használja a "timeseriestransformer" műveletet a Task = "forecasting" művelethez, máskülönben használja a "datatransformer" műveletet a "regresszió" vagy a "besorolás" feladathoz.

+ 2\. API `get_featurization_summary()` : a bemeneti funkciók featurization összegzését adja vissza.

  Használat:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Használja a "timeseriestransformer" műveletet a Task = "forecasting" művelethez, máskülönben használja a "datatransformer" műveletet a "regresszió" vagy a "besorolás" feladathoz.

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

   |Output|Meghatározás|
   |----|--------|
   |RawFeatureName|A megadott adatkészlet bemeneti funkciójának vagy oszlopának neve.|
   |TypeDetected|A bemeneti funkció észlelt adattípusa.|
   |Csökkent|Jelzi, hogy a bemeneti szolgáltatás el lett-e dobva vagy használatban van-e.|
   |EngineeringFeatureCount|Az automatizált funkciók mérnöki átalakításán keresztül generált szolgáltatások száma.|
   |Átalakítások|A bemenő funkciókra alkalmazott átalakítások listája a mérnöki funkciók létrehozásához.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Skálázás/normalizálás és algoritmus hypermeter-értékekkel:

Egy folyamat skálázási/normalizáló és algoritmus/hiperparaméter értékeinek megismeréséhez használja a fitted_model. Steps. [További információ a méretezéssel/normalizálás](concept-automated-ml.md#preprocess). Itt látható egy mintakimenet:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

További részletekért használja az ebben a [példában](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)szereplő jegyzetfüzetben látható segítő funkciót.

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

A következő példa egy folyamat kimenetét adja meg egy adott algoritmus használatával (ebben az esetben a LogisticRegression a RobustScalar-mel).

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

## <a name="explain-the-model-interpretability"></a>A modell (értelmező) ismertetése

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

A funkció fontos diagram a munkaterületen, az Azure Portalon jelenítheti meg. Az URL-cím megjelenítése a Run objektum használatával:

```
automl_run.get_portal_url()
```

A funkció fontos diagram a munkaterületen, az Azure Portalon jelenítheti meg. A diagram akkor is megjelenik, ha a `RunDetails` [Jupyter widgetet](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) használja egy jegyzetfüzetben. Ha többet szeretne megtudni a diagramokról, tekintse át az [automatizált gépi tanulás eredményeit](how-to-understand-automated-ml.md)ismertető témakört.

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![a szolgáltatás fontosság graph](./media/how-to-configure-auto-train/feature-importance.png)

További információk arról, hogy a modell magyarázatait és funkcióit hogyan lehet engedélyezni az SDK más területein az automatikus gépi tanuláson kívül, lásd [](machine-learning-interpretability-explainability.md) az értelmezési cikk fogalmát.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hogyan és hol érdemes a modell üzembe helyezése](how-to-deploy-and-where.md).

További információ a [regressziós modell automatikus gépi tanulással](tutorial-auto-train-models.md) való betanításáról, illetve a [távoli erőforrásokon való automatikus gépi tanulás használatával történő](how-to-auto-train-remote.md)betanításról.
