---
title: Automatizált ML-kísérletek létrehozása
titleSuffix: Azure Machine Learning
description: Az automatizált gépi tanulás felvesz egy algoritmust az Ön számára, és létrehoz egy modellt, amely készen áll a telepítésre. Ismerje meg azokat a beállításokat, amelyek segítségével konfigurálhatja az automatizált gépi tanulási kísérleteket.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: b3192e4bf25763e870cc618e5e45f16384607b7f
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277993"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Automatizált ML-kísérletek konfigurálása a Pythonban
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből az útmutatóból megtudhatja, hogyan határozhatja meg az automatizált gépi tanulási kísérletek különböző konfigurációs beállításait a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-val. Az automatizált gépi tanulás egy algoritmust és hiperparaméterek beállítása hoz létre, és létrehoz egy modellt, amely készen áll a telepítésre. Számos lehetőség közül választhat, amelyek segítségével konfigurálhatja az automatizált gépi tanulási kísérleteket.

Egy automatizált gépi tanulási kísérletre vonatkozó példák megtekintéséhez tekintse meg az [oktatóanyag: besorolási modell betanítása automatizált gépi tanulási](tutorial-auto-train-models.md) vagy [tanítási modellekkel a Felhőbeli automatizált gépi tanulással](how-to-auto-train-remote.md)című témakört.

Az automatizált gépi tanulásban elérhető konfigurációs beállítások:

* Válassza ki a kísérlet típusát: besorolás, regresszió vagy idősorozat-előrejelzés
* Adatforrás, formátumok és beolvasás
* Válassza ki a számítási célt: helyi vagy távoli
* Automatikus gépi tanulási kísérlet beállításai
* Automatizált gépi tanulási kísérlet futtatása
* Modell metrikáinak megismerése
* Modell regisztrálása és üzembe helyezése

Ha nem szeretne programkódot felvenni, akkor [Azure Machine learning Studióban is létrehozhatja az automatizált gépi tanulási kísérleteket](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Válassza ki a kísérlet típusát

A kísérlet megkezdése előtt meg kell határoznia, hogy milyen típusú gépi tanulási probléma van a megoldásban. Az automatizált gépi tanulás támogatja a besorolási, a regressziós és az előrejelzési feladatok típusát. További [információ a feladattípusokról](how-to-define-task-type.md).

Az automatizált gépi tanulás a következő algoritmusokat támogatja az automatizálási és hangolási folyamat során. Felhasználóként nem kell megadnia az algoritmust.

Osztályozás | Regresszió | Idősorozat-előrejelzés
|-- |-- |--
[Logisztikai regresszió](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rugalmas háló](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rugalmas háló](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Világos GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradiens fokozása](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradiens fokozása](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradiens fokozása](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)|[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)
[Legközelebbi szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Legközelebbi szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Legközelebbi szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineáris SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS lasszó](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS lasszó](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[A vektor besorolásának támogatása (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Sztochasztikus gradiens leereszkedés (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Sztochasztikus gradiens leereszkedés (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN osztályozó](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN lineáris osztályozó](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineáris Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineáris Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naiv Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Gyors lineáris Regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Automatikus ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Sztochasztikus gradiens leereszkedés (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Online színátmenet-leereszkedés Regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Próféta](https://facebook.github.io/prophet/docs/quick_start.html)
|[Átlagos Perceptron osztályozó](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[Lineáris SVM osztályozó](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

A kísérlet típusának megadásához használja a `AutoMLConfig` konstruktor `task` paraméterét.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Adatforrás és formátum

Az automatizált gépi tanulás támogatja a helyi asztalon vagy a felhőben, például az Azure Blob Storageban található adategységeket. Az információk egy **Panda DataFrame** vagy egy **Azure Machine learning TabularDataset**is beolvashatók.  [További tudnivalók az adatkészletekről](how-to-create-register-datasets.md).

A betanítási adatgyűjtésre vonatkozó követelmények:
- Az adatokat táblázatos formában kell megadni.
- Az előre jelzett értéknek, a célként megadott oszlopnak szerepelnie kell az adatsorokban.

Az alábbi példák bemutatják, hogyan tárolhatja az ilyen formátumú adattárakat.

* TabularDataset
  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Panda dataframe

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split

    df = pd.read_csv("your-local-file.csv")
    train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
    label = "label-col-name"
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Adatok lekérése a kísérlet futtatásához távoli számításkor

Távoli végrehajtás esetén a betanítási adatoknak elérhetőnek kell lenniük a távoli számításból. Az SDK-ban [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) osztály a következő funkciókat teszi elérhetővé:

* adatok egyszerű átvitele statikus fájlokból vagy URL-forrásokból a munkaterületre
* az adatok elérhetővé tétele a Felhőbeli számítási erőforrásokon futó parancsfájlok betanításához

A következő témakörben talál példát arra, [Hogyan](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) csatlakoztathatók az adatok a számítási célra a `Dataset` osztály használatával.

## <a name="train-and-validation-data"></a>Betanítási és érvényesítési adatkészletek

A `AutoMLConfig` konstruktorban közvetlenül is megadhatja a különböző vonat-és ellenőrzési készleteket.

### <a name="k-folds-cross-validation"></a>K-összecsukható kereszt-ellenőrzés

A `n_cross_validations` beállítással adhatja meg a több érvényesítést. A betanítási adatkészletet véletlenszerűen `n_cross_validations`, egyenlő méretűre bontja. Az egyes átellenőrzési körökben a rendszer az egyik hajtogatást használja a fennmaradó hajtogatási modell érvényesítéséhez. Ez a folyamat megismétli a `n_cross_validations`i köröket, amíg az összes összekapcsolást be nem használja az érvényesítési csoportba. A rendszer az összes `n_cross_validations` kör átlagos pontszámát fogja jelenteni, és a rendszer a megfelelő modellt a teljes betanítási adatkészletre átképezi.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo Cross Validation (ismétlődő véletlenszerű almintavételezés)

A `validation_size` segítségével megadhatja az érvényesítéshez használandó betanítási adatkészlet százalékos arányát, és a `n_cross_validations` használatával megadhatja a többhöz tartozó érvényességi értéket. Az egyes átellenőrzési körökben a méret `validation_size` egy részhalmaza véletlenszerűen lesz kiválasztva a fennmaradó adattípusra képzett modell érvényesítéséhez. Végezetül az összes `n_cross_validations`-kör átlagos pontszámát fogjuk jelenteni, és a rendszer a megfelelő modellt a teljes betanítási adatkészletre újra betanítja. A Monte Carlo nem támogatott az idősorozat-előrejelzéshez.

### <a name="custom-validation-dataset"></a>Egyéni ellenőrzési adatkészlet

Használjon egyéni ellenőrzési adatkészletet, ha a véletlenszerű felosztás nem fogadható el, általában idősorozat-vagy kiegyensúlyozatlan adat. Saját ellenőrzési adatkészletet is megadhat. A modell kiértékelése a véletlenszerű adatkészlet helyett megadott ellenőrzési adatkészlet alapján történik.

## <a name="compute-to-run-experiment"></a>Számítás a kísérlet futtatásához

A következő határozza meg, hogy a modell hogyan lesz betanítva. Az automatizált Machine learning-betanítási kísérlet a következő számítási lehetőségeken futtatható:
*   Helyi számítógép, például helyi asztal vagy laptop – általában kis adatkészletek esetén, és még mindig a felderítési szakaszban van.
*   Egy távoli gép a felhőben – [Azure Machine learning felügyelt számítás](concept-compute-target.md#amlcompute) egy felügyelt szolgáltatás, amely lehetővé teszi a gépi tanulási modellek betanítását az Azure-beli virtuális gépek fürtjén.

    Tekintse meg ezt a [GitHub-webhelyet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) a helyi és távoli számítási célokkal rendelkező jegyzetfüzetek példáinak megtekintéséhez.

*   Egy Azure Databricks-fürt az Azure-előfizetésében. További részleteket itt talál [: Azure Databricks-fürt beállítása AUTOMATIZÁLT ml-hez](how-to-configure-environment.md#azure-databricks)

    Tekintse meg ezt a [GitHub-webhelyet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) , amely példákat tartalmaz a Azure Databrickst tartalmazó notebookokra.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>A kísérlet beállításainak konfigurálása

Számos lehetőség közül választhat, amelyekkel konfigurálhatja az automatizált gépi tanulási kísérletet. Ezeket a paramétereket egy `AutoMLConfig` objektum létrehozásával állítja be a rendszer. A paraméterek teljes listájáért tekintse meg a [AutoMLConfig osztályt](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Néhány példa:

1.  A besorolási kísérlet az elsődleges metrika és a kísérlet időkorlátja alapján AUC súlyozottan 30 percre van beállítva, a két kereszt-ellenőrzési hajtogatás pedig 2.

    ```python
    automl_classifier=AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        blacklist_models='XGBoostClassifier',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=2)
    ```
2.  Az alábbi példa egy olyan regressziós kísérletre van beállítva, amely 60 perc után fejeződik be, és öt ellenőrző kereszttel rendelkezik.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        experiment_timeout_minutes=60,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5)
    ```

A három különböző `task` paraméter értéke (a harmadik feladattípus `forecasting`, és egy hasonló algoritmus-készletet használ `regression` feladatokhoz) határozza meg az alkalmazandó modellek listáját. A `whitelist` vagy `blacklist` paraméterekkel további módosításokat is módosíthat az elérhető modellekkel a belefoglaláshoz vagy kizáráshoz. A támogatott modellek listája a [SupportedModels osztályban](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels)található.

### <a name="primary-metric"></a>Elsődleges metrika
Az elsődleges metrika határozza meg, hogy milyen mérőszámot kell használni az optimalizáláshoz a modell betanításakor. A kiválasztható mérőszámokat a kiválasztott feladattípus határozza meg, az alábbi táblázat pedig az egyes feladattípusok érvényes elsődleges metrikáit tartalmazza.

|Osztályozás | Regresszió | Idősorozat-előrejelzés
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Ismerje meg a mérőszámok konkrét definícióit az [automatizált gépi tanulás eredményeinek megismeréséhez](how-to-understand-automated-ml.md).

### <a name="data-preprocessing--featurization"></a>Adatfeldolgozási & featurization

Minden automatizált gépi tanulási kísérlet során az adatok [automatikusan méretezhetők és normalizálva](concept-automated-ml.md#preprocess) vannak, hogy a különböző léptékű funkciókra *érzékeny algoritmusok* segítségével segítsenek.  Ugyanakkor további előfeldolgozási/featurization is engedélyezheti, például hiányzó értékeket imputálási, kódolást és átalakításokat. [További információ arról, hogy milyen featurization tartalmaz](how-to-create-portal-experiments.md#preprocess).

A featurization engedélyezéséhez a [`AutoMLConfig` osztályhoz](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)`"preprocess": True`t kell megadni.

> [!NOTE]
> Az automatizált gépi tanulás előfeldolgozásának lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos előfeldolgozási lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

### <a name="time-series-forecasting"></a>Idősorozat-előrejelzés
Az idősorozat `forecasting` feladat további paramétereket igényel a konfigurációs objektumban:

1. `time_column_name`: kötelező paraméter, amely meghatározza a betanítási adataiban érvényes idősorozatot tartalmazó oszlop nevét.
1. `max_horizon`: azt határozza meg, hogy mennyi idő elteltével kívánja előre jelezni a betanítási adatmennyiséget. Ha például napi időkeretekkel rendelkező betanítási információkkal rendelkezik, meghatározhatja, hogy a modell milyen mértékben legyen betanítva.
1. `grain_column_names`: meghatározza a betanítási adataiban az egyes idősorozat-adataikat tartalmazó oszlopok nevét. Ha például egy adott márka értékesítési adatait az áruházban szeretné megtekinteni, a tároló és a márka oszlopokat a gabona oszlopaiban definiálhatja. Minden egyes gabona/csoportosítás esetében külön idősorozatok és előrejelzések jönnek létre. 

Az alább használt beállításokra vonatkozó példákért tekintse meg a [minta notebookot](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

automl_config = AutoMLConfig(task = 'forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=20,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>Ensemble-konfiguráció

Az Ensemble-modellek alapértelmezés szerint engedélyezve vannak, és az automatikus gépi tanulás futtatásakor a végső futtatási ismétlésként jelennek meg. A jelenleg támogatott Ensemble-módszerek a szavazás és a halmozás. A szavazás a súlyozott átlagokat használó, Soft-szavazóként van megvalósítva, és a halmozási implementáció két rétegbeli implementációt használ, ahol az első réteg ugyanazokkal a modellekkel rendelkezik, mint a szavazói együttes, és a második réteg modellje a következő optimális kombinációjának megkeresésére szolgál: az első réteg modelljei. Ha ONNX-modelleket használ, **vagy** ha a modell-magyarázat engedélyezve van, a halmozás le lesz tiltva, és csak a szavazást fogja használni.

Több alapértelmezett argumentum is megadható `kwargs`ként egy `AutoMLConfig` objektumban, hogy megváltoztassa az alapértelmezett stack Ensemble viselkedését.

* `stack_meta_learner_type`: a meta-Learner egy modell, amely az egyes különböző-modellek kimenetére van kiképezve. Az alapértelmezett meta-tanulók `LogisticRegression` a besorolási feladatokhoz (vagy `LogisticRegressionCV`, ha a kereszt-ellenőrzés engedélyezve van), és `ElasticNet` a regresszió/előrejelzési feladatokhoz (vagy `ElasticNetCV` ha a kereszt-ellenőrzés engedélyezve van). Ez a paraméter a következő karakterláncok egyike lehet: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor`vagy `LinearRegression`.
* `stack_meta_learner_train_percentage`: a betanítási készlet (a betanítási típus kiválasztásakor) a meta-tanuló betanítása számára fenntartott arányát határozza meg. Az alapértelmezett érték `0.2`.
* `stack_meta_learner_kwargs`: nem kötelező paramétereket adni a meta-learning inicializáló. Ezek a paraméterek és paraméterek típusai a megfelelő modell konstruktorában lévő paramétereket és paramétereket tükrözik, és továbbítva lesznek a modell konstruktorának.

A következő kód példát mutat be az egyéni Ensemble viselkedésének megadására egy `AutoMLConfig` objektumban.

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
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Az Ensemble-képzés alapértelmezés szerint engedélyezve van, de a `enable_voting_ensemble` és az `enable_stack_ensemble` logikai paraméterek használatával le is tilthatja.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Kísérlet futtatása

Az automatikus ML esetében egy `Experiment` objektumot hoz létre, amely egy elnevezett objektum a kísérletek futtatásához használt `Workspace`.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Elküldheti a kísérletet egy modell futtatásához és létrehozásához. Adja át a `AutoMLConfig`t a `submit` metódusnak a modell létrehozásához.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>A függőségek először egy új gépre települnek.  A kimenet megjelenítése előtt akár 10 percet is igénybe vehet.
>A `show_output` beállítása a konzolon megjelenített kimenet `True` eredményeire.

### <a name="exit-criteria"></a>Kilépési feltételek
A kísérlet befejezéséhez több lehetőség is megadható.
1. Nincs feltétel: Ha nem ad meg kilépési paramétereket, a kísérlet addig folytatódik, amíg az elsődleges metrika nem végez további előrehaladást.
1. Kilépés hosszú idő után: `experiment_timeout_minutes` használata a beállításokban megadhatja, hogy a rendszer hány perc elteltével folytassa a kísérlet futtatását.
1. Kilépés egy pontszám elérésekor: a `experiment_exit_score` használatával a rendszer elvégzi a kísérletet, miután elérte az elsődleges metrikai pontszám értékét.

### <a name="explore-model-metrics"></a>Modell metrikáinak megismerése

Megtekintheti a betanítási eredményeket egy widgetben vagy beágyazottan, ha egy jegyzetfüzetben van. További részletekért tekintse meg a [modellek követése és értékelése](how-to-track-experiments.md#view-run-details) című témakört.

## <a name="understand-automated-ml-models"></a>Az automatizált ML-modellek ismertetése

Az automatikus ML használatával előállított modellek a következő lépéseket tartalmazzák:
+ Automatizált funkciók tervezése (ha az előfeldolgozás = true)
+ Skálázás/normalizálás és algoritmus hiperparaméter-értékekkel

Ennek az információnak a beolvasása az automatizált ML-ből származó fitted_model kimenetből átlátható.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Automatizált funkciók tervezése

Tekintse meg az előfeldolgozás és az [automatizált funkciók](concept-automated-ml.md#preprocess) listáját, amely akkor történik meg, amikor a feauturization = Auto.

Megfontolandó példa:
+ Négy bemeneti funkció létezik: A (numerikus), B (numerikus), C (numerikus), D (DateTime)
+ A C numerikus funkció el lett dobva, mert egy azonosító oszlop, amely minden egyedi értéket tartalmaz.
+ Az A és B számú numerikus funkció hiányzó értékeket tartalmaz, ezért a Mean által imputált
+ A D dátum/idő funkció featurized 11 különböző mérnöki funkcióval rendelkezik

A beszerelt modell első lépéseként használja ezt a 2 API-t, hogy jobban megértsen.  Tekintse meg [ezt a minta notebookot](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ 1\. API: `get_engineered_feature_names()` a meglevő funkciók neveinek listáját adja vissza.

  Használat
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Ez a lista tartalmazza az összes megtervezett szolgáltatás nevét.

  >[!Note]
  >Használja a "timeseriestransformer" műveletet a Task = "forecasting" művelethez, máskülönben használja a "datatransformer" műveletet a "regresszió" vagy a "besorolás" feladathoz.

+ 2\. API: a `get_featurization_summary()` az összes bemeneti szolgáltatás featurization-összegzését adja vissza.

  Használat
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

   Helyszín:

   |Kimenet|Meghatározás|
   |----|--------|
   |RawFeatureName|A megadott adatkészlet bemeneti funkciójának vagy oszlopának neve.|
   |TypeDetected|A bemeneti funkció észlelt adattípusa.|
   |Csökkent|Jelzi, hogy a bemeneti szolgáltatás el lett-e dobva vagy használatban van-e.|
   |EngineeringFeatureCount|Az automatizált funkciók mérnöki átalakításán keresztül generált szolgáltatások száma.|
   |Átalakítások|A bemenő funkciókra alkalmazott átalakítások listája a mérnöki funkciók létrehozásához.|
   
### <a name="customize-feature-engineering"></a>A funkciók mérnöki testreszabása
A szolgáltatások mérnöki testreszabásához válassza a `"feauturization":FeaturizationConfig`lehetőséget.

A támogatott Testreszabás az alábbiakat tartalmazza:

|Testreszabás|Meghatározás|
|--|--|
|Oszlop céljának frissítése|Felülbírálja a szolgáltatás típusát a megadott oszlophoz.|
|A transzformátor paraméterének frissítése |A megadott átalakító paramétereinek frissítése. Jelenleg támogatja az imputált (mean, leggyakoribb & medián) és a HashOneHotEncoder.|
|Oszlopok eldobása |Az eldobni kívánt oszlopok featurized.|
|Transzformátorok letiltása| A featurization-folyamathoz használandó transzformátorok letiltása.|

Hozza létre a FeaturizationConfig objektumot az API-hívások használatával:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Skálázás/normalizálás és algoritmus hiperparaméter-értékekkel:

A folyamat skálázási/normalizáló és algoritmus/hiperparaméter értékeinek megismeréséhez használja a fitted_model. Steps. [További információ a méretezéssel/normalizálás](concept-automated-ml.md#preprocess). Itt látható egy mintakimenet:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

További részletekért használja az ebben a [példában szereplő jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)látható segítő funkciót.

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

A következő minta kimenet egy adott algoritmust használó folyamatra vonatkozik (ebben az esetben a LogisticRegression a RobustScalar-mel).

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

### <a name="predict-class-probability"></a>Előrejelzési osztály valószínűsége

Az automatikus ML-vel előállított modellek mindegyike rendelkezik olyan burkoló objektumokkal, amelyek a nyílt forráskódú Origin osztályból származó funkciókat tükröznek. Az automatizált ML által visszaadott legtöbb besorolási modell a `predict_proba()` függvényt implementálja, amely a funkciók (X értékek) tömbhöz hasonló vagy ritka mátrix-adatmintáját fogadja el, és az egyes minták n-dimenziós tömbjét és a hozzá tartozó osztály valószínűségét adja vissza.

Feltételezve, hogy a fenti hívások közül a legjobb futtatást és a beillesztett modellt is lekérte, közvetlenül a beszerelt modellből hívhat `predict_proba()`t, és a modell típusától függően a megfelelő formátumban adja meg a `X_test` mintát.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Ha az alapul szolgáló modell nem támogatja az `predict_proba()` függvényt, vagy helytelen a formátuma, a modell osztályra jellemző kivételt fog dobni. Tekintse meg a [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) és a [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) dokumentációját, amely példákat tartalmaz arra, hogy a függvény hogyan legyen implementálva különböző típusú modellekhez.

<a name="explain"></a>

## <a name="model-interpretability"></a>Modell értelmezhetősége

A modell értelmezése lehetővé teszi, hogy megtudja, miért hoztak létre a modellek előrejelzéseit, és a mögöttes funkciók fontossági értékeit. Az SDK különböző csomagokat tartalmaz, amelyek lehetővé teszik a modell-értelmező funkciók használatát a betanítási és a következtetési időben, a helyi és a központilag telepített modellekhez.

Tekintse meg az [útmutató](how-to-machine-learning-interpretability-automl.md) a kódokhoz című témakört, amely bemutatja, hogyan engedélyezheti a tolmácsolási funkciókat kifejezetten az automatizált gépi tanulási kísérleteken belül.

Általános információk arról, hogy a modell magyarázatait és funkcióinak fontosságát az SDK más területein is engedélyezheti az automatikus gépi tanuláson kívül: a [koncepcióról](how-to-machine-learning-interpretability.md) szóló cikk értelmezése.

## <a name="next-steps"></a>Következő lépések

További információ a [modellek telepítéséről és helyéről](how-to-deploy-and-where.md).

További információ a [regressziós modell automatikus gépi tanulással való betanításáról](tutorial-auto-train-models.md) , illetve a [távoli erőforrásokon való automatikus gépi tanulás használatával történő](how-to-auto-train-remote.md)betanításról.
