---
title: Kísérletek futtatása az Azure automatizált ML-vel
description: Gépi tanulási kísérletek futtatása a Apache Spark és az Azure automatizált ML használatával
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: 57830ae14c5d6653f6a2225924160fcb75646c12
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590655"
---
# <a name="run-experiments-using-azure-automated-ml-and-apache-spark"></a>Kísérletek futtatása az Azure automatizált ML-vel és Apache Spark

A Azure Machine Learning egy felhőalapú környezet, amely lehetővé teszi a gépi tanulási modellek betanítását, üzembe helyezését, automatizálását, kezelését és nyomon követését. 

Ebben az oktatóanyagban a Azure Machine Learning [automatizált gépi tanulást](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) használ egy regressziós modell létrehozásához a New York-i taxi díjszabásának előrejelzéséhez. Ez a folyamat fogadja az oktatási és konfigurációs beállításokat, és automatikusan megismétli a különböző funkciók normalizálása/szabványosítási módszereinek, modelljeinek és hiperparaméter beállításainak kombinációját, hogy a legjobb modellt lehessen megérkezni.

Ez az oktatóanyag a következő feladatokat ismerteti:
- Az adat letöltése Apache Spark és Azure Open-adatkészletek használatával
- Adatátalakítás és-tisztítás az Apache Spark dataframes használatával
- Automatizált gépi tanulási regressziós modell betanítása
- Modell pontosságának kiszámítása

### <a name="before-you-begin"></a>Előkészületek
- Hozzon létre egy Apache Spark készletet az [Apache Spark készlet létrehozása oktatóanyag](../quickstart-create-apache-spark-pool-studio.md)követésével.
- Ha nem rendelkezik meglévő Azure Machine Learning munkaterülettel, fejezze be a [Azure Machine learning munkaterület beállítási oktatóanyagát](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup) . 

### <a name="understand-regression-models"></a>A regressziós modellek ismertetése
A *regressziós modellek* előre jelezik a numerikus kimeneti értékeket a független előrejelzők alapján. A regresszió során a cél az, hogy segítsen a független prediktív változók közötti kapcsolat kialakításában azzal, hogy megbecsüli, hogyan befolyásolja a többi változó.  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>Regressziós elemzési példa a New York-i taxik adataihoz
Ebben a példában a Spark használatával elvégezheti a New York-i taxi Trip-adatok elemzését. Az adatokat az [Azure Open-adatkészletek](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)segítségével érheti el. Az adatkészlet ezen részhalmaza a Yellow taxi-utakról tartalmaz információkat, beleértve az egyes utazásokkal, a kezdési és befejezési időponttal és a hellyel, a díjszabással és egyéb érdekes attribútumokkal kapcsolatos információkat.

> [!IMPORTANT]
> 
> Az adatok tárolási helyétől való kihúzása további költségekkel járhat. A következő lépésekben a New York-i taxi viteldíjának előrejelzésére szolgáló modellt fog kialakítani. 
> 

##  <a name="download-and-prepare-the-data"></a>Az adatgyűjtés letöltése és előkészítése

1. Hozzon létre egy jegyzetfüzetet a PySpark kernel használatával. Útmutatásért lásd: [Jegyzetfüzet létrehozása](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.)
   
   > [!Note]
   > 
   > A PySpark kernel miatt nem kell explicit módon létrehoznia a környezeteket. A Spark-környezet automatikusan létrejön az első kód cellájának futtatásakor.
   >

2. Mivel a nyers adatmennyiség parkettás formátumú, a Spark-környezettel közvetlenül is lehívhatja a fájlt a memóriába dataframe. Hozzon létre egy Spark-dataframe az adatoknak a nyílt adatkészletek API-n keresztüli beolvasásával. Itt a Spark dataframe *sémát* használjuk az olvasási tulajdonságoknál az adattípusok és a séma kikövetkeztetni. 
   
   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "nyctlc"
   blob_relative_path = "yellow"
   blob_sas_token = r""

   # Allow Spark to read from Blob remotely
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

   # Spark read parquet, note that it won't load any data yet by now
   df = spark.read.parquet(wasbs_path)
   ```

3. A Spark-készlet (előzetes verzió) méretétől függően előfordulhat, hogy a nyers adatmennyiség túl nagy, vagy túl sok időt vesz igénybe. Ezeket az adatmennyiségeket leszűkítheti a ```start_date``` és a szűrők használatával ```end_date``` . Ez egy olyan szűrőt alkalmaz, amely egy hónapot ad vissza. Miután megtörtént a szűrt dataframe, a függvényt is futtatjuk ```describe()``` az új dataframe az egyes mezők összegző statisztikáinak megtekintéséhez. 

   Az összegző statisztika alapján láthatjuk, hogy az adatokban nincsenek szabálytalanságok és kiugró értékek. A statisztikák például azt mutatják, hogy a minimális utazási távolság nullánál kisebb. Ezeket a szabálytalan adatpontokat ki kell szűrni.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Mostantól az adatkészlet funkcióit az oszlopok egy halmazának kiválasztásával és különböző időalapú funkciók létrehozásával hozhatja létre a felvételi dátum és idő mezőből. A korábbi lépésekben azonosított kiugró értékeket is kiszűrjük, majd eltávolítjuk az utolsó néhány oszlopot, amely nem szükséges a betanításhoz.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster and less expensive down sample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                , column('puMonth').alias('month_num') \
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                & (sampled_taxi_df.rateCodeId <= 5)\
                                & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
Amint láthatja, ez egy új dataframe hoz létre, amely további oszlopokkal fog szolgálni a hónap napjához, a pickup óra, a hétköznap és a teljes menetidő. 

![A taxi dataframe képe.](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>Tesztelési és érvényesítési adatkészletek előállítása

Ha elkészült a végső adatkészlettel, az adatokat kioszthatja a betanítási és tesztelési készletekben a Spark ```random_ split ``` függvény használatával. A megadott súlyok használatával a függvény véletlenszerűen felosztja az adatokat a modell betanítása és a teszteléshez használt ellenőrzési adatkészlet betanítási adatkészletbe.

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

Ez a lépés biztosítja, hogy az adatpontok tesztelik a modell betanításához nem használt befejezett modellt. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Kapcsolódás Azure Machine Learning-munkaterülethoz
Azure Machine Learning a  **munkaterület** egy olyan osztály, amely elfogadja az Azure-előfizetést és az erőforrás-információkat. Létrehoz egy felhőalapú erőforrást is a modell futtatásának figyelésére és nyomon követésére. Ebben a lépésben egy munkaterület-objektumot hozunk létre a meglévő Azure Machine Learning munkaterületről.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Dataframe átalakítása Azure Machine Learning adatkészletbe
Távoli kísérlet elküldéséhez át kell alakítania az adatkészletet egy Azure Machine Learningba ```TabularDatset``` . A [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) táblázatos formátumban jeleníti meg az adatokat a megadott fájlok elemzésével.

A következő kód lekéri a meglévő munkaterületet és az alapértelmezett Azure Machine Learning alapértelmezett adattárat. Ezután átadja az adattár és a fájl helyét a Path paraméternek egy új létrehozásához ```TabularDataset``` . 

```python
import pandas 
from azureml.core import Dataset

# Get the AML Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into AML Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```

![A feltöltött adatkészlet képe.](./media/apache-spark-machine-learning-aml-notebook/upload-dataset.png)

## <a name="submit-an-auto-ml-experiment"></a>Automatikus ML-kísérlet küldése

#### <a name="define-training-settings"></a>Képzési beállítások megadása

1. A kísérlet elküldéséhez meg kell határoznia a kísérlet paramétert és a modell beállításait a betanításhoz. A beállítások teljes listáját [itt](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)tekintheti meg.

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

2. Most átadjuk a definiált képzési beállításokat \* \* kwargs paraméterként egy AutoMLConfig objektumra. Mivel a Sparkban vagyunk betanításban, át kell adni a Spark-környezetet is, amelyet a változó automatikusan elérhetővé tesz ```sc``` . Emellett megadhatja a betanítási és a modell típusát is, amely ebben az esetben regressziós.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data = dataset_training,
                             spark_context = sc,
                             model_explainability = False, 
                             label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
> Az automatizált gépi tanulás előfeldolgozásának lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos előfeldolgozási lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

#### <a name="train-the-automatic-regression-model"></a>Az automatikus regressziós modell betanítása 
Most létrehozunk egy kísérlet objektumot a Azure Machine Learning munkaterületen. Egy kísérlet tárolóként működik az egyes futtatásokhoz. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

A kísérlet befejezését követően a kimenet a befejezett iterációk részleteit adja vissza. Minden egyes iterációnál megjelenik a modell típusa, a Futtatás időtartama és a képzés pontossága. A mező LEGJOBBAN a mérőszám típusa alapján a legjobb futó tanítási pontszámot követi nyomon.

![Képernyőkép a modell kimenetéről.](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> A AutoML kísérlet elküldését követően különböző iterációkat és modell-típusokat fog futtatni. Ez a Futtatás általában 1 – 1,5 órát vesz igénybe. 

#### <a name="retrieve-the-best-model"></a>A legjobb modell lekérése
Az iterációk közül a legjobb modell kiválasztásához a függvényt fogjuk használni ```get_output``` a legjobb Futtatás és a felszerelt modell visszaadásához. Az alábbi kód beolvassa az összes naplózott metrika vagy egy adott iteráció legjobb futtatási és beszerelt modelljét.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>Modell pontosságának tesztelése

1. A modell pontosságának teszteléséhez a legjobb modellt fogjuk használni a tesztelési adatkészleten a taxi viteldíj-előrejelzések futtatásához. A ```predict``` függvény a legjobb modellt használja, és előre jelzi az y (viteldíj mennyisége) értékét az ellenőrzési adatkészletből. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2. A legfelső szintű Mean-Square hiba (GYÖKÁTLAGOS) a modell által előre jelzett és a megfigyelt értékek közötti különbségeket gyakran használt mérték. Az eredmények legfelső szintű négyzetes hibáját az y_test dataframe és a modell által előre jelzett értékek összehasonlításával számítjuk ki. 

   A függvény ```mean_squared_error``` két tömböt vesz igénybe, és kiszámítja a közöttük lévő átlagos négyzetes hibát. Ezután az eredmény négyzet gyökerét vesszük. Ez a metrika nagyjából azt jelzi, hogy a taxi viteldíjának előrejelzései a tényleges viteldíjak értékeiből származnak.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate Root Mean Square Error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   
   A legfelső szintű Mean-Square típusú hiba jól szemlélteti, hogy a modell pontosan milyen mértékben Jósolja meg a választ. Az eredményekből láthatja, hogy a modell meglehetősen jó, ha az adatkészlet funkciói alapján előre megjósolja a taxi viteldíjait, jellemzően a +-$2,00

3. Futtassa a következő kódot a középérték abszolút százalékának kiszámításához (MAPE). Ez a metrika a hiba százalékában kifejezi a pontosságot. Ezt úgy hajtja végre, hogy kiszámítja az összes előre jelzett és tényleges érték közötti abszolút különbséget, majd összesíti az összes különbséget. Ezt követően a tényleges értékek összegének százalékában fejezi ki az összeget.

   ```python
   # Calculate MAPE and Model Accuracy 
   sum_actuals = sum_errors = 0

   for actual_val, predict_val in zip(y_actual, y_predict):
       abs_error = actual_val - predict_val
       if abs_error < 0:
           abs_error = abs_error * -1

       sum_errors = sum_errors + abs_error
       sum_actuals = sum_actuals + actual_val

   mean_abs_percent_error = sum_errors / sum_actuals

   print("Model MAPE:")
   print(mean_abs_percent_error)
   print()
   print("Model Accuracy:")
   print(1 - mean_abs_percent_error)
   ```

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   A két előrejelzés pontossági metrikájában láthatja, hogy a modell elég jó, ha az adatkészlet funkciói alapján előre megjósolja a taxi viteldíjait. 

4. A lineáris regressziós modell rögzítése után most meg kell határozni, hogy milyen jól illeszkedik a modell az adattípushoz. Ehhez a tényleges viteldíj-értékeket kell kirajzolni az előre jelzett kimenetre. Emellett az R-négyzetes mértéket is kiszámítjuk, hogy megértsük, hogyan zárjuk be az adatok a beépített regressziós vonalba.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the Actual vs Predicted Fare Amount Values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
   plt.show()
   ```
   
   ![A regressziós mintaterület képernyőképe.](./media/apache-spark-machine-learning-aml-notebook/aml-fare-amount.png)

   Az eredményekből láthatjuk, hogy az R-négyzetes mérték a variancia 95%-ában van elkönyvelve. Ezt a tényleges, megfigyelt versek is ellenőrzik. Minél több eltérés van, amelyet a regressziós modell figyelembe vesz, annál közelebb esik az adatpontok a beépített regressziós vonalhoz.  

## <a name="register-model-to-azure-machine-learning"></a>Modell regisztrálása a Azure Machine Learningba
A legjobb modell ellenőrzése után regisztrálhatjuk a modellt Azure Machine Learningra. A modell regisztrálása után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```

```Output
NYCGreenTaxiModel 1
```

## <a name="view-results-in-azure-machine-learning"></a>Eredmények megtekintése Azure Machine Learning
Végül az iterációk eredményeinek eléréséhez navigáljon a Azure Machine Learning-munkaterületban található kísérlethez. Itt további részleteket tudhat meg a Futtatás, a megkísérelt modellek és a modell egyéb mérőszámai állapotáról. 

![A pénzmosás-munkaterület képernyőképe.](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>További lépések
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark MLlib-oktatóanyag](./apache-spark-machine-learning-mllib-notebook.md)
