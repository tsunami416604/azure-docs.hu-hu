---
title: Automatizált gépi tanulási kísérletek létrehozása
titleSuffix: Azure Machine Learning
description: Az automatizált gépi tanulás felvesz egy algoritmust az Ön számára, és létrehoz egy modellt, amely készen áll a telepítésre. Ismerje meg azokat a beállításokat, amelyek segítségével konfigurálhatja az automatizált gépi tanulási kísérleteket.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 08/10/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperfq1
ms.openlocfilehash: fe562b8202c508c13f4127d14aeb5f994d15f962
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89649599"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Automatizált gépi tanulási kísérletek konfigurálása Pythonban
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből az útmutatóból megtudhatja, hogyan határozhatja meg az automatizált gépi tanulási kísérletek különböző konfigurációs beállításait a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)-val. Az automatizált gépi tanulás egy algoritmust és hiperparaméterek beállítása hoz létre, és létrehoz egy modellt, amely készen áll a telepítésre. Számos lehetőség közül választhat, amelyek segítségével konfigurálhatja az automatizált gépi tanulási kísérleteket.

Ha szeretné megtekinteni az automatizált gépi tanulási kísérletekre vonatkozó példákat, tekintse meg az [oktatóanyag: besorolási modell betanítása automatizált gépi tanulással](tutorial-auto-train-models.md) vagy [tanítási modelleket a Felhőbeli automatizált gépi tanulással](how-to-auto-train-remote.md).

Az automatizált gépi tanulásban elérhető konfigurációs beállítások:

* Válassza ki a kísérlet típusát: besorolás, regresszió vagy idősorozat-előrejelzés
* Adatforrás, formátumok és beolvasás
* Válassza ki a számítási célt: helyi vagy távoli
* Automatikus gépi tanulási kísérlet beállításai
* Automatizált gépi tanulási kísérlet futtatása
* Modell metrikáinak megismerése
* Modell regisztrálása és üzembe helyezése

Ha nem szeretne programkódot felvenni, akkor [Azure Machine learning Studióban is létrehozhatja az automatizált gépi tanulási kísérleteket](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez szükséges, 
* Egy Azure Machine Learning-munkaterület. A munkaterület létrehozásával kapcsolatban tekintse meg [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md)című témakört.

* A Azure Machine Learning Python SDK telepítve van.
    Az SDK telepítéséhez használhatja a következőt is, 
    * Hozzon létre egy számítási példányt, amely automatikusan telepíti az SDK-t, és előre konfigurálva van a ML-munkafolyamatokhoz. További információ: [What is a Azure Machine learning számítási példány?](concept-compute-instance.md#managing-a-compute-instance) . 

    * [Saját maga is telepítheti az SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)-t. Csak ügyeljen arra, hogy a `automl` továbbiak szerepeljenek. 

## <a name="select-your-experiment-type"></a>A kísérlet típusának kiválasztása

A kísérlet megkezdése előtt meg kell határoznia, hogy milyen típusú gépi tanulási probléma van a megoldásban. Az automatizált gépi tanulás támogatja a, a és a feladattípusait `classification` `regression` `forecasting` . További [információ a feladattípusokról](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

A következő kód a `task` konstruktor paraméterét használja a `AutoMLConfig` kísérlet típusának megadásához `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Adatforrás és formátum

Az automatizált gépi tanulás támogatja a helyi asztalon vagy a felhőben, például az Azure Blob Storageban található adategységeket. Az információk egy **Panda DataFrame** vagy egy **Azure Machine learning TabularDataset**is beolvashatók. [További tudnivalók az adatkészletekről](how-to-create-register-datasets.md).

A betanítási adatgyűjtésre vonatkozó követelmények:
- Az adatokat táblázatos formában kell megadni.
- Az előre jelzett értéknek, a célként megadott oszlopnak szerepelnie kell az adatsorokban.

**Távoli kísérletek**esetén a betanítási adatoknak elérhetőnek kell lenniük a távoli számításból. A AutoML csak akkor fogadja [Azure Machine learning TabularDatasets](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) , ha távoli számítási feladatokra dolgozik. 

Azure Machine Learning adatkészletek a következő funkciókat teszik elérhetővé:

* A statikus fájlokból vagy URL-forrásokból származó adatok egyszerűen átvihetők a munkaterületre.
* Az adatok elérhetővé tétele a Felhőbeli számítási erőforrásokon futó parancsfájlok betanításához. Tekintse meg, [hogyan kell betanítani az adatkészletekkel](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) , hogy miként lehet az `Dataset` osztály használatával csatlakoztatni adatokat a távoli számítási célhoz.

A következő kód egy TabularDataset hoz létre egy webes URL-címről. A [TabularDatasets létrehozásával](how-to-create-register-datasets.md#create-a-tabulardataset) kapcsolatos példákat itt talál: adatkészletek létrehozása más forrásokból, például helyi fájlokból és adattárolóból.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Helyi számítási kísérletek**esetén a Panda dataframes használatát javasoljuk a gyorsabb feldolgozási idő érdekében.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Képzés, érvényesítés és tesztelés

Külön **betanítási és érvényesítési készleteket** is megadhat közvetlenül a `AutoMLConfig` konstruktorban. További információ az [adatfelosztások és a AutoML-kísérletek határokon való ellenőrzésének konfigurálásáról](how-to-configure-cross-validation-data-splits.md) . 

Ha nem ad meg explicit módon a `validation_data` vagy a `n_cross_validation` paramétert, a AutoML az érvényesítési műveletek elvégzésének módjára vonatkozó alapértelmezett technikákat alkalmazza. Ez a meghatározás a paraméterhez rendelt adatkészlet sorainak számától függ `training_data` . 

|Betanítási &nbsp; &nbsp; adatméret| Érvényesítési módszer |
|---|-----|
|**Nagyobb, &nbsp; mint &nbsp; 20 000 &nbsp; sor**| A betanítási/érvényesítési adatfelosztást alkalmazza a rendszer. Az alapértelmezett érték a kezdeti betanítási adatkészlet 10%-át adja meg az érvényesítési készletként. Ezt az ellenőrzési készletet pedig a metrikák számításához használja a rendszer.
|**Kisebb, &nbsp; mint &nbsp; 20 000 &nbsp; sor**| A rendszer több ellenőrzési módszert alkalmaz. A kidobások alapértelmezett száma a sorok számától függ. <br> **Ha az adatkészlet kevesebb mint 1 000 sor, a**rendszer 10 karámot használ. <br> **Ha a sorok 1 000 és 20 000 között vannak**, akkor három hajtogatás van használatban.

Jelenleg a modell kiértékeléséhez meg kell adnia a saját **tesztelési adatait** . Ha például a modell kiértékeléséhez saját tesztelési adatait szeretné bemutatni, tekintse meg a [Jupyter-jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) **tesztelési** szakaszát.

## <a name="compute-to-run-experiment"></a>Számítás a kísérlet futtatásához

A következő határozza meg, hogy a modell hogyan lesz betanítva. Az automatizált Machine learning-betanítási kísérlet a következő számítási lehetőségeken futtatható. Ismerje meg a [helyi és távoli számítási lehetőségek előnyeit és hátrányait](concept-automated-ml.md#local-remote) . 

* **Helyi** számítógép, például helyi asztal vagy laptop – általában akkor, ha kis adatkészlettel rendelkezik, és még mindig a felderítési szakaszban van. Tekintse meg [ezt a jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) helyi számítási példaként. 
 
* Egy **távoli** gép a felhőben – [Azure Machine learning felügyelt számítás](concept-compute-target.md#amlcompute) egy felügyelt szolgáltatás, amely lehetővé teszi a gépi tanulási modellek betanítását az Azure-beli virtuális gépek fürtjén. 

    Tekintse meg [ezt a jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) egy távoli példához Azure Machine learning felügyelt számítás használatával. 

* Egy **Azure Databricks-fürt** az Azure-előfizetésében. További részleteket itt talál [: Azure Databricks-fürt beállítása AUTOMATIZÁLT ml-hez](how-to-configure-environment.md#azure-databricks). Tekintse meg ezt a [GitHub-webhelyet](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) , amely példákat tartalmaz a Azure Databrickst tartalmazó notebookokra.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>A kísérlet beállításainak konfigurálása

Számos lehetőség közül választhat, amelyekkel konfigurálhatja az automatizált gépi tanulási kísérletet. Ezek a paraméterek egy objektum létrehozásával állíthatók be `AutoMLConfig` . A paraméterek teljes listájáért tekintse meg a [AutoMLConfig osztályt](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Néhány példa:

1. A besorolási kísérlet az elsődleges metrika és a kísérlet időkorlátja alapján AUC súlyozottan 30 percre van beállítva, a két kereszt-ellenőrzési hajtogatás pedig 2.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blocked_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
1. Az alábbi példa egy olyan regressziós kísérlet, amely 60 perc után fejeződik be, és öt ellenőrző kereszttel rendelkezik.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      allowed_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```


1. Az előrejelzési feladatokhoz további beállítások szükségesek. további részletekért tekintse meg az [idősorozat-előrejelzési modell automatikus betanítását](how-to-auto-train-forecast.md) ismertető cikket. 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'drop_column_names': ['logQuantity'],
        'forecast_horizon': n_test_periods
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
    
### <a name="supported-models"></a>Támogatott modellek

Az automatizált gépi tanulás különböző modelleket és algoritmusokat próbál az automatizálási és hangolási folyamat során. Felhasználóként nem kell megadnia az algoritmust. 

A három különböző `task` paraméter értéke (a harmadik feladattípus `forecasting` , és egy hasonló, a `regression` feladathoz hasonlóan használt algoritmus) határozza meg az algoritmusok, modellek listáját. Az `allowed_models` vagy a `blocked_models` paraméterrel további módosításokat is végrehajthat az elérhető modellekkel a belefoglaláshoz vagy kizáráshoz. A támogatott modellek listája a [besorolás](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), az [Előrejelzés](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)és a [regresszió](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression) [SupportedModels osztályán](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) található.


### <a name="primary-metric"></a>Elsődleges metrika
A `primary metric` paraméter határozza meg, hogy milyen mérőszámot kell használni az optimalizáláshoz a modell betanításakor. A kiválasztható mérőszámokat a kiválasztott feladattípus határozza meg, az alábbi táblázat pedig az egyes feladattípusok érvényes elsődleges metrikáit tartalmazza.

Ismerje meg a mérőszámok konkrét definícióit az [automatizált gépi tanulás eredményeinek megismeréséhez](how-to-understand-automated-ml.md).

|Osztályozás | Regresszió | Idősoros előrejelzés
|--|--|--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-featurization"></a>Az adatfeaturization

Minden automatizált gépi tanulási kísérlet során az adatok automatikusan méretezhetők és normalizálva vannak, hogy a különböző léptékű funkciókra *érzékeny algoritmusok* segítségével segítsenek. Ezt a skálázást és a normalizálás a featurization néven ismert. További részletekért és példákért tekintse [meg a Featurization a AutoML](how-to-configure-auto-features.md#) című témakört. 

A kísérletek az objektumban való konfigurálásakor `AutoMLConfig` engedélyezheti vagy letilthatja a beállítást `featurization` . A következő táblázat a [AutoMLConfig objektum](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)featurization elfogadott beállításait tartalmazza. 

|Featurization-konfiguráció | Description |
| ------------- | ------------- |
|`"featurization": 'auto'`| Azt jelzi, hogy az előfeldolgozás részeként a rendszer automatikusan végrehajtja az [guardrails és a featurization lépéseket](how-to-configure-auto-features.md#featurization) . **Alapértelmezett beállítás**.|
|`"featurization": 'off'`| Azt jelzi, hogy a featurization lépést nem kell automatikusan elvégezni.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Azt jelzi, hogy a testreszabott featurization lépést kell használni. [Megtudhatja, hogyan szabhatja testre a featurization](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Az automatizált gépi tanulás featurization lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos featurization-lépéseket automatikusan alkalmazza a rendszer a bemeneti adatokra.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Ensemble-konfiguráció

Az Ensemble-modellek alapértelmezés szerint engedélyezve vannak, és a AutoML-futtatások utolsó futtatási ismétlései jelennek meg. Jelenleg a **VotingEnsemble** és a **StackEnsemble** támogatottak. 

A szavazás a súlyozott átlagokat használó, lágy szavazást valósít meg. A halmozási implementáció egy kétrétegű implementációt használ, ahol az első réteg ugyanazokat a modelleket használja, mint a szavazó Ensemble, a második réteg modell pedig az első réteg modelljeinek optimális kombinációját használja. 

Ha ONNX-modelleket használ, **vagy** ha a modell-magyarázat engedélyezve van, a halmozás le van tiltva, és csak a szavazást használja.

Az Ensemble-képzés a `enable_voting_ensemble` és a logikai paraméterek használatával tiltható le `enable_stack_ensemble` .

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

Az alapértelmezett Ensemble-viselkedés módosításához több alapértelmezett argumentum is megadható, mint `kwargs` egy `AutoMLConfig` objektumban.

> [!IMPORTANT]
>  A következő paraméterek nem rendelkeznek explicit paraméterekkel a AutoMLConfig osztályhoz. 

* `ensemble_download_models_timeout_sec`: A **VotingEnsemble** és a **StackEnsemble** modell létrehozásakor a rendszer letölti az előző gyermekekről származó, több felszerelt modelleket. Ha ezt a hibát tapasztalja: `AutoMLEnsembleException: Could not find any models for running ensembling` , akkor előfordulhat, hogy több időt kell megadnia a modellek letöltéséhez. Az alapértelmezett érték 300 másodperc, ha párhuzamosan tölti le ezeket a modelleket, és nincs maximális időkorlát. A paramétert a 300 másodpercnél nagyobb értékre kell beállítani, ha több idő szükséges. 

  > [!NOTE]
  >  Ha elérte az időtúllépést, és a rendszer letölti a modelleket, akkor a ensembling a letöltött számú modellel együtt folytatja. Nem szükséges, hogy az összes modellt le kell tölteni az adott időtúllépésen belüli befejezéshez.

A következő paraméterek csak a **StackEnsemble** -modellekre vonatkoznak: 

* `stack_meta_learner_type`: a meta-Learner egy modell, amely az egyes heterogén modellek kimenetére van kiképezve. Az alapértelmezett meta-tanulók a `LogisticRegression` besorolási feladatokhoz (vagy ha a többszörös `LogisticRegressionCV` ellenőrzés engedélyezve van), valamint `ElasticNet` a regresszió/előrejelzési feladatokhoz (vagy `ElasticNetCV` Ha a kereszt-ellenőrzés engedélyezve van). Ez a paraméter a következő karakterláncok egyike lehet:,,,,, `LogisticRegression` `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` vagy `LinearRegression` .

* `stack_meta_learner_train_percentage`: meghatározza a betanítási készletnek azt az arányát (a betanítási típus kiválasztásakor), amelyet a meta-tanuló képzéséhez kell foglalni. Az alapértelmezett érték: `0.2` . 

* `stack_meta_learner_kwargs`: választható paraméterek, amelyeket át kell adni a meta-Learner inicializálásához. Ezek a paraméterek és paraméterek típusai a megfelelő modell konstruktorában lévő paramétereket és paramétereket tükrözik, és továbbítva lesznek a modell konstruktorának.

Az alábbi kód egy példát mutat be az egyéni Ensemble viselkedésének megadására egy `AutoMLConfig` objektumban.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
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

## <a name="run-experiment"></a>Kísérlet futtatása

Az automatikus ML esetében hozzon létre egy `Experiment` objektumot, amely egy megnevezett objektum, amely a `Workspace` kísérletek futtatására szolgál.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Küldje el a kísérletet futtatásra, és hozzon létre egy modellt. Adja `AutoMLConfig` meg a `submit` metódust a modell létrehozásához.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>A függőségek először egy új gépre települnek.  A kimenet megjelenítése előtt akár 10 percet is igénybe vehet.
>A `show_output` beállítás `True` hatására a rendszer kimenet jelenik meg a konzolon.

 <a name="exit"></a> 

### <a name="exit-criteria"></a>Kilépési feltételek

A kísérlet befejezéséhez több lehetőség is megadható.

|Feltételek| leírás
|----|----
Nincsenek &nbsp; feltételek | Ha nem ad meg kilépési paramétereket, a kísérlet addig folytatódik, amíg az elsődleges metrika nem végez további előrehaladást.
&nbsp; &nbsp; Hosszú &nbsp; &nbsp; idő után| A `experiment_timeout_minutes` beállítások segítségével megadhatja, hogy a kísérlet hány perc elteltével fusson tovább. <br><br> Ha el szeretné kerülni az időtúllépési kísérletek elkerülését, legalább 15 percet vagy 60 percet is igénybe vehet, ha az oszlop mérete meghaladja a 10 000 000-es sorszámot.
&nbsp;Elérte A &nbsp; pontszámot &nbsp; &nbsp;| `experiment_exit_score`A használata a kísérlet befejezése után a megadott elsődleges metrikai pontszám elérésekor.

## <a name="explore-models-and-metrics"></a>Modellek és mérőszámok megismerése

Megtekintheti a betanítási eredményeket egy widgetben vagy beágyazottan, ha egy jegyzetfüzetben van. További részletekért tekintse meg a [modellek követése és értékelése](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs) című témakört.

Lásd: a [gépi tanulási eredmények megismerése](how-to-understand-automated-ml.md) az egyes futtatásokhoz megadott teljesítmény-diagramok és mérőszámok definíciói és példái alapján. 

A featurization összegzéséhez és az adott modellhez hozzáadott funkciók megismeréséhez lásd: [featurization átlátszósága](how-to-configure-auto-features.md#featurization-transparency). 

## <a name="register-and-deploy-models"></a>Modellek regisztrálása és üzembe helyezése

A webszolgáltatások üzembe helyezéséhez szükséges modellek letöltéséről vagy regisztrálásáról további információt a [modell üzembe helyezésének módja és helye](how-to-deploy-and-where.md)című témakörben talál.


<a name="explain"></a>

## <a name="model-interpretability"></a>Modell értelmezhetősége

A modell értelmezése lehetővé teszi, hogy megtudja, miért hoztak létre a modellek előrejelzéseit, és a mögöttes funkciók fontossági értékeit. Az SDK különböző csomagokat tartalmaz, amelyek lehetővé teszik a modell-értelmező funkciók használatát a betanítási és a következtetési időben, a helyi és a központilag telepített modellekhez.

Tekintse meg az [útmutató](how-to-machine-learning-interpretability-automl.md) a kódokhoz című témakört, amely bemutatja, hogyan engedélyezheti a tolmácsolási funkciókat kifejezetten az automatizált gépi tanulási kísérleteken belül.

Általános információk arról, hogy a modell magyarázatait és funkcióinak fontosságát az SDK más területein is engedélyezheti az automatikus gépi tanuláson kívül: a [koncepcióról](how-to-machine-learning-interpretability.md) szóló cikk értelmezése.

> [!NOTE]
> A magyarázat-ügyfél jelenleg nem támogatja a ForecastTCN modellt. Ez a modell nem ad vissza magyarázat-irányítópultot, ha az a legjobb modellként lett visszaadva, és nem támogatja az igény szerinti magyarázatok futtatását.

## <a name="next-steps"></a>Következő lépések

+ További információ a [modellek telepítéséről és helyéről](how-to-deploy-and-where.md).

+ További információ a [regressziós modell automatikus gépi tanulással való betanításáról](tutorial-auto-train-models.md) , illetve a [távoli erőforrásokon való automatikus gépi tanulás használatával történő](how-to-auto-train-remote.md)betanításról.

+ Ismerje meg, hogyan taníthat több modellt a AutoML [számos modell megoldás-gyorsító](https://aka.ms/many-models)használatával.
