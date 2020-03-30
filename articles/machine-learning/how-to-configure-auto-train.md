---
title: Automatikus ml-kísérletek létrehozása
titleSuffix: Azure Machine Learning
description: Az automatikus gépi tanulás kiválaszt egy algoritmust, és létrehoz egy, üzembe helyezésre kész modellt. Ismerje meg az automatikus gépi tanulási kísérletek konfigurálásához használható lehetőségeket.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 4cf940e38a84ea2eeb1896c8f7c628c8d5734374
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247129"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Automatizált gépi tanulási kísérletek konfigurálása Pythonban
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből az útmutatóból megtudhatja, hogyan határozhatja meg az automatikus gépi tanulási kísérletek különböző konfigurációs beállításait az [Azure Machine Learning SDK-val.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) Az automatikus gépi tanulás kiválaszt egy algoritmust és hiperparamétereket, és létrehoz egy, üzembe helyezésre kész modellt. Számos lehetőség közül választhat, amelyek segítségével konfigurálhatja az automatikus gépi tanulási kísérletek.

Az automatizált gépi tanulási kísérletek példáinak megtekintéséhez tekintse meg [az Oktatóanyag: Besorolási modell betanítása automatizált gépi tanulással](tutorial-auto-train-models.md) vagy [A modellek betanítása automatizált gépi tanulással a felhőben című témakört.](how-to-auto-train-remote.md)

Az automatizált gépi tanulásban elérhető konfigurációs lehetőségek:

* Válassza ki a kísérlet típusát: Besorolás, Regresszió vagy Idősorozat-előrejelzés
* Adatforrás, formátumok és adatok beolvasása
* Válassza ki a számítási célt: helyi vagy távoli
* Automatikus gépi tanulási kísérlet beállításai
* Automatizált gépi tanulási kísérlet futtatása
* Modellmutatók felfedezése
* Modell regisztrálása és üzembe helyezése

Ha a kód nélküli élményt részesíti előnyben, az Azure Machine Learning stúdióban is [létrehozhat automatikus gépi tanulási kísérleteket.](how-to-use-automated-ml-for-ml-models.md)

## <a name="select-your-experiment-type"></a>A kísérlet típusának kiválasztása

A kísérlet megkezdése előtt meg kell határoznia, hogy milyen típusú gépi tanulási problémát old meg. Az automatikus gépi tanulás támogatja a besorolás, a regresszió és az előrejelzés feladattípusait. További információ a [feladattípusokról.](how-to-define-task-type.md)

Az automatizált gépi tanulás a következő algoritmusokat támogatja az automatizálási és hangolási folyamat során. Felhasználóként nincs szükség az algoritmus megadására.

Osztályozás | Regresszió | Idősoros előrejelzés
|-- |-- |--
[Logisztikai regresszió](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rugalmas háló](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rugalmas háló](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Fény GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Fény GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Fény GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Színátmenet kiemelése](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Színátmenet kiemelése](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Színátmenet kiemelése](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)|[Döntési fa](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Legközelebbi Szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Legközelebbi Szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Legközelebbi Szomszédok](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineáris SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS-lasszó](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS-lasszó](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Támogatás vektor besorolás (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Sztochastikus gradiens (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Sztochastikus gradiens (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Véletlenszerű erdő](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Rendkívül randomizált fák](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN osztályozó](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN regresszió](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN regresszió](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN lineáris osztályozó](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineáris regresszió](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineáris regresszió](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naiv Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Gyors lineáris regresszió](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Sztochastikus gradiens (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Online gradiens eslesztő](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Próféta](https://facebook.github.io/prophet/docs/quick_start.html)
|[Átlagos perceptron osztályozó](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ElőrejelzésTCN
|[Lineáris SVM osztályozó](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

A `task` `AutoMLConfig` konstruktor ban lévő paraméter segítségével adja meg a kísérlet típusát.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Adatforrás és formátum

Az automatikus gépi tanulás támogatja a helyi asztalon vagy a felhőben, például az Azure Blob Storage-ban található adatokat. Az adatok beolvashatók egy **Pandas DataFrame** vagy egy **Azure Machine Learning tabularDataset.**  [További információ az adatkészletekről.](how-to-create-register-datasets.md)

A képzési adatokra vonatkozó követelmények:
- Az adatoknak táblázatos formában kell lenniük.
- Az előre jelezni a céloszlopnak az adatokban kell lennie.

Az alábbi kódpéldák bemutatják, hogyan tárolhatja az adatokat ezekben a formátumokban.

* Táblázatos adatkészlet

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Pandák adatkerete

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Adatok beolvasása távoli számítású kísérlethez

Távoli végrehajtások esetén a betanítási adatoknak elérhetőnek kell lenniük a távoli számításból. Az [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) SDK osztálya a következő funkciókat teszi elérhetővé:

* egyszerűen átviheti az adatokat statikus fájlokból vagy URL-forrásokból a munkaterületre
* az adatok elérhetővé teszik a betanítási parancsfájlok számára, ha felhőalapú számítási erőforrásokon futnak

Tekintse meg az [útmutatót](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) egy `Dataset` példa az osztály segítségével az adatok csatlakoztatása a számítási célhoz.

## <a name="train-and-validation-data"></a>Betanítási és érvényesítési adatok

Külön vonat- és érvényesítési `AutoMLConfig` készleteket adhat meg közvetlenül a kivitelezőben.

### <a name="k-folds-cross-validation"></a>K-Folds keresztérvényesítés

A `n_cross_validations` beállítással megadhatja a keresztérvényesítések számát. A betanítási adatkészlet véletlenszerűen `n_cross_validations` azonos méretű redőkre lesz felosztva. Minden kereszt-ellenőrzési fordulóban az egyik redőlesz használva a fennmaradó hajtásokon betanított modell hitelesítésére. Ez a folyamat `n_cross_validations` ismétlődik a körökben, amíg minden hajtás egyszer nem használható érvényesítési készletként. Az összes `n_cross_validations` forduló átlagos pontszámait jelenti a rendszer, és a megfelelő modellt a teljes betanítási adatkészletre képezi át.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo keresztérvényesítés (ismételt véletlenszerű almintavétel)

A `validation_size` betanítási adatkészlet érvényességi arányának megadására, valamint `n_cross_validations` a keresztellenőrzések számának megadására használható. Minden egyes kereszt-ellenőrzési fordulóban `validation_size` egy részhalmaza mérete véletlenszerűen kiválasztva a fennmaradó adatokon betanított modell érvényesítéséhez. Végül az összes `n_cross_validations` fordulóban az átlagos pontszámok at jelentiak, és a megfelelő modellt a teljes betanítási adatkészletre képezzük át. Monte Carlo nem támogatott az idősorozat-előrejelzéshez.

### <a name="custom-validation-dataset"></a>Egyéni érvényesítési adatkészlet

Használjon egyéni érvényesítési adatkészletet, ha a véletlenszerű felosztás nem elfogadható, általában az idősorozat-adatok vagy a kiegyensúlyozatlan adatok. Megadhatja saját érvényesítési adatkészletét. A modell kiértékelése a véletlenszerű adatkészlet helyett megadott érvényesítési adatkészlet alapján kerül kiértékelésre.

## <a name="compute-to-run-experiment"></a>Számítás a kísérlet futtatásához

Ezután határozza meg, ahol a modell lesz betanítva. Egy automatizált gépi tanulási képzési kísérlet a következő számítási lehetőségeken futtatható:
* A helyi gép, például egy helyi asztali vagy laptop – Általában, ha kis adatkészlet, és még mindig a feltárási szakaszban.
* Egy távoli gép a felhőben – [az Azure Machine Learning managed compute](concept-compute-target.md#amlcompute) egy felügyelt szolgáltatás, amely lehetővé teszi a gépi tanulási modellek betanítását az Azure virtuális gépek fürtjein.

  Tekintse meg ezt a [GitHub-webhelyet,](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) ahol példákat talál a helyi és távoli számítási célokkal rendelkező jegyzetfüzetekre.

* Egy Azure Databricks-fürt az Azure-előfizetésben. További részleteket itt talál - [Az Azure Databricks-fürt beállítása az automatizált ml-hez](how-to-configure-environment.md#azure-databricks)

  Tekintse meg ezt a [GitHub-webhelyet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) példákat az Azure Databricks notebookok.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>A kísérlet beállításainak konfigurálása

Számos lehetőség közül választhat, hogy konfigurálja az automatikus gépi tanulási kísérlet. Ezeket a paramétereket egy `AutoMLConfig` objektum példányosításával lehet beállítani. A paraméterek teljes listáját az [AutoMLConfig osztályban](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) láthatja.

Néhány példa:

1. Besorolási kísérlet az AUC súlyozott elsődleges metrikaként a kísérlet időout perc 30 perc és 2 kereszt-érvényesítési redők.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. Az alábbiakban egy példa egy regressziós kísérlet, amely 60 perc után ér véget öt érvényesítési keresztredők.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['kNN regressor'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

A három `task` különböző paraméterérték (a `forecasting`harmadik feladattípus , és `regression` hasonló algoritmuskészletet használ feladatként) határozza meg az alkalmazandó modellek listáját. A `whitelist` vagy `blacklist` paraméterek segítségével tovább módosíthatja az iterációkat a rendelkezésre álló modellekkel, hogy tartalmazza vagy kizárja. A támogatott modellek listája megtalálható a [SupportedModels osztályban](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) ([Besorolás](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [Előrejelzés](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)és [Regresszió](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

A kísérlet időtúllépési hibáinak elkerülése érdekében az `experiment_timeout_minutes` automatikus hibaelhárító szolgáltatás nak legalább 15 percre, vagy 60 percre kell beállítania, ha a sor oszlopméret jelege meghaladja a 10 milliót.

### <a name="primary-metric"></a>Elsődleges metrika
Az elsődleges metrika határozza meg a modell optimalizálási betanítása során használandó metrikát. A választható metrikákat a kiválasztott feladattípus határozza meg, és az alábbi táblázat az egyes feladattípusok érvényes elsődleges mutatóit jeleníti meg.

|Osztályozás | Regresszió | Idősoros előrejelzés
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Ismerje meg ezeknek a metrikáknak a konkrét definícióit [az Automatikus gépi tanulási eredmények megértése című](how-to-understand-automated-ml.md)részben.

### <a name="data-featurization"></a>Adatok featurization

Minden automatizált gépi tanulási kísérletben az adatok [automatikusan méretezve és normalizálva](concept-automated-ml.md#preprocess) lesznek, hogy *segítsenek bizonyos* algoritmusoknak, amelyek érzékenyek a különböző skálákon lévő funkciókra.  Azonban további featurization is engedélyezhet, például a hiányzó értékek imputálása, kódolása és átalakítása. [További információ arról, hogy mi a csomag.](how-to-use-automated-ml-for-ml-models.md#featurization)

A kísérletek konfigurálásakor engedélyezheti a `featurization`speciális beállítást. Az alábbi táblázat az [ `AutoMLConfig` osztályban](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)elfogadott megjelenítési beállításokat mutatja be.

|Jellemző konfiguráció | Leírás |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Azt jelzi, hogy a testreszabott jellemzőlépésnek kell használnia. [További információ a jellemző testreszabásáról.](how-to-configure-auto-train.md#customize-feature-engineering)|
|`"featurization": 'off'`| Azt jelzi, hogy a featurization lépés nem végezhető el automatikusan.|
|`"featurization": 'auto'`| Azt jelzi, hogy az előfeldolgozás részeként az [adatkorlátok és a jellemzőlépések](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) automatikusan végrehajtásra kerülnek.|

> [!NOTE]
> Az automatizált gépi tanulás, amely a mögöttes modell részévé válik(funkciónormalizálás, hiányzó adatok kezelése, szöveg átalakítása numerikussá stb.). Ha a modell t használja az előrejelzések, ugyanazokat a jellemző lépések alkalmazása a betanítás során automatikusan alkalmazza a bemeneti adatokat.

### <a name="time-series-forecasting"></a>Idősoros előrejelzés
Az idősorozat-feladat `forecasting` további paramétereket igényel a konfigurációs objektumban:

1. `time_column_name`: Kötelező paraméter, amely meghatározza az oszlop nevét a betanítási adatok at tartalmazó érvényes idősorozatok.
1. `max_horizon`: A betanítási adatok gyakorisága alapján határozza meg, hogy mennyi időt szeretne előre jelezni. Ha például napi időgabonával rendelkezik betanítási adatokkal, megadhatja, hogy a modell milyen messzire legyen napokon belül.
1. `grain_column_names`: A betanítási adatokban egyedi idősorozat-adatokat tartalmazó oszlopok nevét határozza meg. Ha például egy adott márka értékesítését tárolja, akkor az üzlet- és márkaoszlopokat kell meghatároznia gabonaoszlopokként. Külön idősorok és előrejelzések jönnek létre az egyes szemcsémek/csoportosítások. 

Az alábbi beállításokra vonatkozó példákat a [mintajegyzetfüzetben találja.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)

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

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Ensemble konfiguráció

Az ensemble modellek alapértelmezés szerint engedélyezve vannak, és egy automatizált gépi tanulási futtatás végső futtatási ismétléseként jelennek meg. Jelenleg támogatott együttes módszerek szavazás és egymásra. A szavazás súlyozott átlagok használatával puha szavazásként történik, és a halmozási megvalósítás kétrétegű implementációt használ, ahol az első réteg ugyanazokat a modelleket használja, mint a szavazási együttes, és a második rétegmodellt használják a a modellek az első réteg. Ha ONNX modelleket használ, **vagy** engedélyezve van a modell-magyarázhatóság, a halmozás le lesz tiltva, és csak a szavazás lesz használva.

Több alapértelmezett argumentum is megadható, `kwargs` `AutoMLConfig` mint egy objektumban az alapértelmezett veremegyüttes viselkedésének megváltoztatására.

* `stack_meta_learner_type`: a metatanuló az egyes heterogén modellek teljesítményére kiképzett modell. Az alapértelmezett metatanulók `LogisticRegression` osztályozási feladatokra (vagy `LogisticRegressionCV` ha a `ElasticNet` keresztérvényesítés engedélyezve van) `ElasticNetCV` és a regressziós/előrejelzési feladatokra (vagy ha a keresztérvényesítés engedélyezve van). Ez a paraméter a következő karakterláncok `LightGBMClassifier`egyike `ElasticNet` `ElasticNetCV`lehet: `LogisticRegression`, `LogisticRegressionCV`, , , , `LightGBMRegressor`, vagy `LinearRegression`.
* `stack_meta_learner_train_percentage`: meghatározza a képzési készlet azon hányadát (a képzés kiválasztásakor és a képzés validálási típusának kiválasztásakor), amelyet a metatanuló képzésére kell fenntartani. Az alapértelmezett `0.2`érték a .
* `stack_meta_learner_kwargs`: választható paraméterek, amelyeket át kell adni a metatanuló inicializálójának. Ezek a paraméterek és paramétertípusok tükrözik a megfelelő modellkonstruktor paramétereit és paramétertípusait, és továbbítják őket a modellkonstruktornak.

A következő kód egy példát mutat be `AutoMLConfig` egy objektum egyéni együttesviselkedésének megadására.

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

Az együttes betanítása alapértelmezés szerint engedélyezve `enable_voting_ensemble` van, de a logikai `enable_stack_ensemble` paraméterek használatával letiltható.

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

Az automatikus hibaelhárító `Experiment` hoz létre egy objektumot, amely egy elnevezett objektum egy `Workspace` kísérletek futtatásához használt objektumban.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Küldje el a kísérletet futtatásra, és hozzon létre egy modellt. Adja `AutoMLConfig` át `submit` a módszert a modell létrehozásához.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>A függőségek először egy új gépre vannak telepítve.  A kimenet bemutatása akár 10 percet is igénybe vehet.
>Ha `show_output` `True` a beállítás azt eredményezi, hogy a kimenet megjelenik a konzolon.

### <a name="exit-criteria"></a>Kilépési feltételek
Van néhány lehetőség, amelyet megadhat a kísérlet befejezéséhez.
1. Nincs feltétel: Ha nem határoz meg kilépési paramétereket, a kísérlet addig folytatódik, amíg nem történik további előrelépés az elsődleges metrika.
1. Kilépés egy hosszú idő `experiment_timeout_minutes` után: A beállítások használatával megadhatja, hogy mennyi ideig fusson a kísérlet.
1. Kilépés a pontszám elérése után: A használat `experiment_exit_score` befejezi a kísérletet az elsődleges metrikapontszám elérése után.

### <a name="explore-model-metrics"></a>Modellmutatók felfedezése

Megtekintheti a betanítási eredményeket egy widgetben vagy a szövegközi, ha egy jegyzetfüzetben van. További részletekért lásd [a Modellek nyomon követése és kiértékelése](how-to-track-experiments.md#view-run-details) című témakört.

## <a name="understand-automated-ml-models"></a>Az automatizált ml-modellek ismertetése

Az automatizált ml-rel előállított modellek a következő lépéseket tartalmazzák:
+ Automatizált szolgáltatástervezés `"featurization": 'auto'`(ha)
+ Méretezés/Normalizálás és algoritmus hiperparaméterértékekkel

Átláthatóvá tesszük, hogy ezeket az információkat az fitted_model az automatizált ml kimenetéből.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Automatizált szolgáltatástervezés

Tekintse meg az előfeldolgozás és az `"featurization": 'auto'` [automatizált szolgáltatástervezés](concept-automated-ml.md#preprocess) listáját, amely a esetén történik.

Tekintsük ezt a példát:
+ Négy bemeneti funkció létezik: A (Numerikus), B (Numerikus), C (Numerikus), D (DateTime)
+ A Numerikus C szolgáltatás elesik, mert ez egy azonosító oszlop, amely minden egyedi értéket tartalmaz
+ Az A és B numerikus jellemzők nek hiányzó értékei vannak, ezért az átlag
+ DateTime funkció D van featurized-ba 11 különböző mesterséges funkciók

Használja ezt a 2 API-t a felszerelt modell első lépésében, hogy jobban megértse.  Lásd [ezt a mintajegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` a módosított szolgáltatásnevek listáját adja vissza.

  Használat
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Ez a lista tartalmazza az összes mesterséges szolgáltatásnevet.

  >[!Note]
  >Használja a "timeseriestransformer" a task='forecasting', else use 'datatransformer' for 'regression' or 'classification' task.

+ API 2: `get_featurization_summary()` az összes bemeneti funkció jellemző összegzését adja vissza.

  Használat
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Használja a "timeseriestransformer" a task='forecasting', else use 'datatransformer' for 'regression' or 'classification' task.

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
   |RawFeatureName (RawFeatureName)|Beviteli szolgáltatás/oszlop név a megadott adatkészletből.|
   |TípusÉszlelt|A bemeneti szolgáltatás adattípusát észlelte.|
   |Csökkent|Azt jelzi, hogy a beviteli szolgáltatás ellett-e vagy használatban van-e.|
   |EngineeringFeatureCount (Műszakifunkciószáma)|Az automatizált szolgáltatástervezési átalakítások által létrehozott funkciók száma.|
   |Átalakítások|A mesterséges funkciók létrehozásához a bemeneti funkciókra alkalmazott átalakítások listája.|
   
### <a name="customize-feature-engineering"></a>Szolgáltatástervezés testreszabása
A szolgáltatástervezés testreszabásához adja meg a megadását. `"featurization": FeaturizationConfig`

A támogatott testreszabás a következőket tartalmazza:

|Testreszabás|Meghatározás|
|--|--|
|Oszlop cél frissítése|A megadott oszlop jellemzőtípusának felülbírálása.|
|Transzformátor paraméterének frissítése |A megadott transzformátor paramétereinek frissítése. Jelenleg támogatja az Imputer (átlagos, leggyakoribb & medián) és a HashOneHotEncoder.|
|Oszlopok eldobása |Oszlopok csepp attól, hogy featurized.|
|Blokk transzformátorok| Blokk transzformátorok kell használni a featurization folyamat.|

Hozza létre a FeaturizationConfig objektumot API-hívásokkal:
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

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Méretezés/Normalizálás és algoritmus hiperparaméterértékekkel:

A folyamat méretezési/normalizálási és algoritmus-/hiperparaméterértékeinek megértéséhez kövesse a fitted_model.steps. [További információ a méretezésről/normalizálásról.](concept-automated-ml.md#preprocess) Itt látható egy mintakimenet:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

További részletekért használja ezt a [mintajegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)látható segítő funkciót.

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

A következő minta kimenet egy adott algoritmust használó folyamathoz (logisticRegression robustScalar, ebben az esetben).

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

### <a name="predict-class-probability"></a>Osztály valószínűségének előrejelzése

Az automatizált ml-rel előállított modellek mindegyike burkoló objektumokkal rendelkezik, amelyek tükrözik a nyílt forráskódú eredetosztályuk funkcióit. Az automatikus ML által visszaadott legtöbb `predict_proba()` besorolási modell burkolóobjektuma valósítja meg a funkciót, amely elfogadja a jellemzők tömbszerű vagy ritka mátrixadat-mintáját (X értékek), és az egyes minták n-dimenziós tömbjét és annak adott osztályvalószínűségét adja vissza.

Feltételezve, hogy a legjobb futási és felszerelt modellt ugyanazokat `predict_proba()` a hívásokat használja felülről, közvetlenül a felszerelt modellből hívhat, és a modell típusától függően a megfelelő formátumú `X_test` mintát adhat meg.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Ha az alapul szolgáló `predict_proba()` modell nem támogatja a funkciót, vagy a formátum helytelen, egy modellosztály-specifikus kivétel jelenik meg. Tekintse meg a [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) és [az XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) referencia-dokumentumok példákat, hogyan ez a függvény valósul meg a különböző modelltípusok.

<a name="explain"></a>

## <a name="model-interpretability"></a>Modell értelmezhetősége

A modell értelmezhetősége lehetővé teszi annak megértését, hogy a modellek miért készítettek előrejelzéseket, és miért az alapul szolgáló jellemzőfontossági értékek. Az SDK különböző csomagokat tartalmaz a modell értelmezhetőségi funkcióinak engedélyezéséhez, mind a betanítási, mind a következtetési időben, a helyi és az üzembe helyezett modellek hez.

Tekintse meg a [kódminták útmutatóját](how-to-machine-learning-interpretability-automl.md) arról, hogyan engedélyezheti az értelmezhetőségi funkciókat kifejezetten az automatizált gépi tanulási kísérleteken belül.

Általános tájékoztatást arról, hogyan modell magyarázatok és jellemző fontosságát lehet engedélyezni más területein az SDK kívül automatizált gépi tanulás, lásd a [koncepció](how-to-machine-learning-interpretability.md) cikk értelmezhetőség.

## <a name="next-steps"></a>További lépések

További információ a [modell üzembe helyezésének módjáról és helyéről.](how-to-deploy-and-where.md)

További információ [aregressziós modell automatikus gépi tanulással történő betanításáról,](tutorial-auto-train-models.md) illetve [az automatikus gépi tanulás távoli erőforráson történő betanításáról.](how-to-auto-train-remote.md)
