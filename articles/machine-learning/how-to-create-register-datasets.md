---
title: Azure Machine Learning adatkészletek létrehozása az adateléréshez
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészleteket a gépi tanulási kísérletekhez szükséges adateléréshez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/22/2020
ms.openlocfilehash: 5f58698de289efc0b74550260c2229f2a08d798d
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461374"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning adatkészletek létrehozása

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészleteket a helyi vagy távoli kísérletek adataihoz való hozzáféréshez. Annak megismeréséhez, hogy az adatkészletek hogyan illeszkednek Azure Machine Learning összesített adatelérési munkafolyamataihoz, tekintse meg a [biztonságos hozzáférés adatai](concept-data.md#data-workflow) című cikket.

Azure Machine Learning adatkészletek esetében a következőket teheti:

* Az adatkészletek által hivatkozott adatokat egyetlen példányban tárolja a tárolóban.

* Zökkenőmentesen férhet hozzá az adatmodell-képzéshez a kapcsolati karakterláncok vagy az adatelérési utak miatt

* Az adatmegosztás és együttműködés más felhasználókkal.

[További információ az adatkészletek betanításáról](how-to-train-with-datasets.md).

## <a name="prerequisites"></a>Előfeltételek

Az adatkészletek létrehozásához és működéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md).

* A [Azure Machine learning SDK for Python telepítve](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), amely tartalmazza a azureml-adatkészletek csomagot.

> [!NOTE]
> Egyes adatkészlet-osztályok függőségekkel rendelkeznek a [azureml-adatelőkészítés](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomagon, amely csak a 64 bites Python rendszerrel kompatibilis. A Linux-felhasználók esetében ezek az osztályok csak a következő disztribúciókban támogatottak: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) és CentOS (7).

## <a name="compute-size-guidance"></a>Számítási méretre vonatkozó útmutató

Adatkészlet létrehozásakor tekintse át a számítási feldolgozási teljesítményt és az adatok méretét a memóriában. A tárolóban tárolt adatai mérete nem egyezik meg a dataframe lévő adatmérettel. Például a CSV-fájlokban lévő adat akár 10x-re is kiterjeszthető egy dataframe, így egy 1 GB-os CSV-fájl 10 GB-nyi lehet egy dataframe. 

Ha tömöríti az adatait, tovább bővíthető. a tömörített parketta-formátumban tárolt 20 GB-os viszonylag ritka adat a memóriában is kiterjeszthető ~ 800 GB-ra. Mivel a Parquet-fájlok oszlopos formátumban tárolják az adattárolást, ha csak az oszlopok felét kell megadnia, akkor a memóriában csak a ~ 400 GB-ot kell betölteni.
 
Ha a Pandat használja, nincs ok arra, hogy több mint 1 vCPU, mivel ez minden, amit használni fog. A Modin és a Dask/Ray használatával egyszerűen integrálással számos vCPU egyetlen Azure Machine Learning számítási példányon/csomóponton keresztül, és szükség esetén nagy méretű fürtre is kibővíthető, ha egyszerűen `import pandas as pd` változik `import modin.pandas as pd` . 
 
[További információ az adatfeldolgozás optimalizálásáról Azure Machine Learning](concept-optimize-data-processing.md)

## <a name="dataset-types"></a>Adathalmaz-típusok

Két adatkészlet-típus létezik, attól függően, hogy a felhasználók hogyan használják őket a képzésben:

* A [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) osztály egy vagy több fájlt hivatkozik az adattárolókban vagy a nyilvános URL-címeken. Ezzel a módszerrel letöltheti vagy csatlakoztathatja a fájlokat a számítási feladatokhoz FileDataset objektumként. A fájlok bármilyen formátumúak lehetnek, ami lehetővé teszi a gépi tanulási forgatókönyvek szélesebb körének használatát, beleértve a mély tanulást is. 

* A [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) táblázatos formátumban jeleníti meg az adatokat a megadott fájl vagy fájlok listájának elemzésével. Ez lehetővé teszi, hogy a rendszer egy Panda vagy Spark DataFrame is megvalósuljon az információ. Létrehozhat egy `TabularDataset` objektumot. csv,. TSV,. Parque,. JSON fájlokból és SQL-lekérdezések eredményeiből. A teljes listát lásd: [TabularDatasetFactory osztály](https://aka.ms/tabulardataset-api-reference).

## <a name="create-datasets-via-the-sdk"></a>Adatkészletek létrehozása az SDK-n keresztül

Az adathalmazok létrehozásával az adatforrások helyére mutató hivatkozásokat és a metaadatok másolatait is létrehozza. Mivel az adatok a meglévő helyükön maradnak, nincsenek további tárolási költségek. Ahhoz, hogy az adatok elérhetők legyenek Azure Machine Learning számára, az adatkészleteket az [Azure](how-to-access-data.md) -adattárolók vagy nyilvános webes URL-címek elérési útjaiból kell létrehozni. 

Adatkészletek létrehozása Azure- [adattárból](how-to-access-data.md) a Python SDK használatával:

1. Ellenőrizze, hogy rendelkezik `contributor` -e `owner` a regisztrált Azure adattárral.

2. Hozza létre az adatkészletet az adattár elérési útjaira hivatkozó hivatkozásokkal.

> [!Note]
> Több adattárból is létrehozhat adatkészletet több adattárolóban. Az adatkészletek létrehozásához használt fájlok vagy adatméretek száma nem korlátozott. Az egyes adatelérési útvonalak esetében azonban a rendszer néhány kérelmet küld a tárolási szolgáltatásnak, hogy meggyőződjön róla, hogy egy fájlra vagy mappára mutat. Ez a terhelés csökkentheti a teljesítményt vagy a hibát. Egy 1000-fájlon belüli, egy mappára hivatkozó adatkészlet egy adatútvonalra hivatkozik. Javasoljuk, hogy az optimális teljesítmény érdekében az adattárolók kevesebb mint 100 útvonalára hivatkozó adatkészletet hozzon létre.

#### <a name="create-a-filedataset"></a>FileDataset létrehozása

A [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) osztály metódusának használatával `FileDatasetFactory` bármilyen formátumban betöltheti a fájlokat, és nem regisztrált FileDataset hozhat létre. Ha a tárterület virtuális hálózat vagy tűzfal mögött van, állítsa a paramétert a `validate=False` `from_files()` metódusban. Ezzel megkerüli a kezdeti ellenőrzési lépést, és gondoskodik arról, hogy az adatkészletet ezekből a biztonságos fájlokból lehessen létrehozni.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="create-a-tabulardataset"></a>TabularDataset létrehozása

Az osztályban lévő metódus használatával olvassa be a [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) `TabularDatasetFactory` . csv vagy a. TSV formátumú fájlokat, és hozzon létre egy nem regisztrált TabularDataset. Ha több fájlt olvas be, az eredmények egy táblázatos ábrázolásba lesznek összesítve. 

A következő kód beolvassa a munkaterület meglévő munkaterületét és a kívánt adattárat név alapján. Ezután átadja az adattár és a fájl helyét a `path` paraméternek egy új TabularDataset létrehozásához `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Alapértelmezés szerint a TabularDataset létrehozásakor a rendszer automatikusan kikövetkezteti az oszlop adattípusait. Ha a késleltetett típusok nem felelnek meg az elvárásainak, az alábbi kód használatával megadhatja az oszlopok típusait. A paraméter `infer_column_type` csak tagolt fájlokból létrehozott adatkészletek esetében alkalmazható. [További információ a támogatott adattípusokról](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)is.

> [!IMPORTANT] 
> Ha a tárterület virtuális hálózat vagy tűzfal mögött van, akkor csak az SDK-n keresztüli adatkészlet létrehozását támogatja a rendszer. Az adatkészlet létrehozásához ügyeljen rá, hogy a paramétereket `validate=False` és a `infer_column_types=False` `from_delimited_files()` metódust is tartalmazza. Ezzel megkerüli a kezdeti ellenőrzési ellenőrzést, és gondoskodik arról, hogy az adatkészletet ezekből a biztonságos fájlokból hozza létre. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Index|PassengerId|Túlélte|Pclass|Name|szex|Kor|SibSp|Parch|Ticket|Legnagyobb légitársasága|Kabin|Megkezdte
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Hamis|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7,2500||S
1|2|Igaz|1|Cumings, Mrs. John Bradley (Florence Briggs th...|female|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Igaz|3|Heikkinen, Miss. Laina 's|female|26,0|0|0|STON/O2. 3101282|7,9250||S

Ha adatkészletet szeretne létrehozni egy memóriából származó pandák dataframe, írja az adatokat egy helyi fájlba, például egy CSV-fájlba, és hozza létre az adatkészletet a fájlból. A következő kód mutatja be ezt a munkafolyamatot.

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')
# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

A [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) osztály metódusának használatával `TabularDatasetFactory` olvassa el a Azure SQL Database:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore. Take note of double parenthesis.
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

A TabularDatasets megadhat egy időbélyegzőt az adatok egyik oszlopában, vagy bárhonnan, ahol a rendszer az elérésiút-minta adatait tárolja egy idősorozat-tulajdonság engedélyezéséhez. Ez az előírás lehetővé teszi az egyszerű és hatékony szűrést az idő alapján.

A [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) osztály metódusának használatával `TabularDataset` adja meg az időbélyegző oszlopot, és engedélyezze az idő szerinti szűrést. További információ: [táblázatos idősorozattal kapcsolatos API-bemutató a NOAA időjárási adataival](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```
### <a name="register-datasets"></a>Adatkészletek regisztrálása

A létrehozási folyamat befejezéséhez regisztrálja az adatkészleteket egy munkaterületen. Az [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) adatkészletek munkaterülethez való regisztrálásához használja a metódust, hogy megossza őket másokkal, és újra felhasználja őket a munkaterületen lévő kísérletek között:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-in-the-studio"></a>Adatkészletek létrehozása a Studióban

Az alábbi lépések és animációk bemutatják, hogyan hozhat létre adatkészletet [Azure Machine learning Studióban](https://ml.azure.com).

> [!Note]
> A Azure Machine Learning Studióval létrehozott adatkészletek automatikusan regisztrálva lesznek a munkaterületen.

![Adatkészlet létrehozása a felhasználói felületen](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Adatkészlet létrehozása a Studióban:
1. Jelentkezzen be a következő címen: https://ml.azure.com .
1. Válassza az **adatkészletek** lehetőséget a bal oldali ablaktábla **eszközök** részén. 
1. Válassza az **adatkészlet létrehozása** lehetőséget az adatkészlet forrásának kiválasztásához. Ez a forrás lehet helyi fájl, adattár vagy nyilvános URL-cím.
1. Válassza a **táblázatos** vagy a **fájl** lehetőséget az adatkészlet típusához.
1. Kattintson a **tovább** gombra az **adattár és a fájl kiválasztása** űrlap megnyitásához. Ezen az űrlapon kiválaszthatja, hogy hol szeretné megőrizni az adatkészletet a létrehozás után, valamint kiválaszthatja az adatkészlethez használni kívánt adatfájlokat. 
    1. Az érvényesítés kihagyásának engedélyezése, ha az adatai virtuális hálózaton vannak. További információ a [virtuális hálózatok elkülönítéséről és adatvédelméről](how-to-enable-virtual-network.md#machine-learning-studio).
1. Válassza a **tovább** lehetőséget a **beállítások és az előnézet** és a **séma** űrlapok feltöltéséhez; ezeket a fájltípusok alapján intelligens módon töltik fel, és az űrlapokon való létrehozás előtt további beállításokkal konfigurálhatja az adatkészletet. 
1. Kattintson a **tovább** gombra a **részletek megerősítése** űrlap áttekintéséhez. Válassza ki a beállításokat, és hozzon létre egy opcionális adatprofilt az adatkészlethez. További információ az [adatprofilkészítésről](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Válassza a **Létrehozás** lehetőséget az adatkészlet létrehozásának befejezéséhez.

## <a name="create-datasets-with-azure-open-datasets"></a>Adatkészletek létrehozása az Azure Open-adatkészletekkel

Az [Azure Open-adatkészletek](https://azure.microsoft.com/services/open-datasets/) olyan beszerzett nyilvános adatkészletek, amelyekkel pontosabb modelleket adhat hozzá a gépi tanulási megoldásokhoz. Az adatkészletek olyan nyilvános tartományi adatokat foglalnak magukban, mint az időjárás, a népszámlálás, az ünnepnapok, a közbiztonság és a gépi tanulási modellek betanítását és a prediktív megoldások bővítését segítő hely. A nyílt adatkészletek a felhőben vannak Microsoft Azure, és az SDK és a munkaterület felhasználói felületén is szerepelnek.

### <a name="use-the-sdk"></a>Az SDK használata

Ha az SDK-ból Azure Open-adatkészleteket tartalmazó adatkészleteket szeretne létrehozni, győződjön meg arról, hogy telepítette a csomagot a következővel: `pip install azureml-opendatasets` . Minden különálló adatkészletet a saját osztálya képvisel az SDK-ban, és bizonyos osztályok akár a,, `TabularDataset` akár `FileDataset` mindkettőként is elérhetők. Az osztályok teljes listáját a [dokumentációban](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) találja.

Bizonyos osztályokat lekérhet a `TabularDataset` vagy rendszerbe `FileDataset` , amely lehetővé teszi a fájlok közvetlen kezelését és/vagy letöltését. Más osztályok **csak** az egyik vagy függvény használatával kaphatnak adatkészletet `get_tabular_dataset()` `get_file_dataset()` . Az alábbi mintakód néhány példát mutat be az ilyen típusú osztályokra.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Amikor egy megnyitott adatkészletből létrehozott adatkészletet regisztrál, az adatok letöltése nem történik meg azonnal, de az adatok a kéréskor (például a betanítás során) egy központi tárolóhelyről lesznek elérhetők.

### <a name="use-the-ui"></a>A felhasználói felület használata

A felhasználói felületen keresztül is létrehozhat adatkészleteket a nyitott adatkészletek osztályaiból. A munkaterületen válassza az **adatkészletek** lapot az **eszközök**területen. Az **adatkészlet létrehozása** legördülő menüben válassza a **megnyitott adatkészletek**lehetőséget.

![Adatkészlet megnyitása a felhasználói felületen](./media/how-to-create-register-datasets/open-datasets-1.png)

Válasszon ki egy adatkészletet a csempe kiválasztásával. (A keresősáv használatával szűrheti a szűrőt.) Válassza a **tovább**lehetőséget.

![Adatkészlet kiválasztása](./media/how-to-create-register-datasets/open-datasets-2.png)

Válassza ki azt a nevet, amelyben regisztrálni kívánja az adatkészletet, és opcionálisan szűrheti az adatokat az elérhető szűrők használatával. Ebben az esetben a munkaszüneti adatkészletek esetében az időszakot egy évig, az országkód pedig csak az Egyesült Államokban szűri. Válassza a **Létrehozás** lehetőséget.

![Adatkészlet-paraméterek beállítása és adatkészlet létrehozása](./media/how-to-create-register-datasets/open-datasets-3.png)

Az adatkészlet mostantól elérhető a munkaterületen az **adatkészletek**területen. Azt ugyanúgy használhatja, mint a létrehozott többi adatkészletet.

## <a name="version-datasets"></a>Verziók adatkészletei

Egy új verzió létrehozásával regisztrálhat egy új adatkészletet ugyanazon a néven. Az adatkészlet verziója lehetővé teszi az adathalmazok könyvjelzővel való megjelölését, hogy az adatkészlet egy adott verzióját a kísérletezéshez vagy a későbbi sokszorosításhoz lehessen alkalmazni. További információ az [adatkészletek verzióiról](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>Adatkészletek elérése a parancsfájlban

A regisztrált adatkészletek helyileg és távolról is elérhetők a számítási fürtökön, például az Azure Machine Learning számítási feladatokhoz. A regisztrált adatkészlet kísérletek közötti eléréséhez használja a következő kódot a munkaterület és a regisztrált adatkészlet név szerinti eléréséhez. Alapértelmezés szerint az [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) osztályban lévő metódus a `Dataset` munkaterülethez regisztrált adatkészlet legújabb verzióját adja vissza.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-datasets-in-a-virtual-network"></a>Adatkészletek elérése egy virtuális hálózaton

Ha a munkaterület virtuális hálózatban található, az érvényesítés kihagyása érdekében konfigurálnia kell az adatkészletet. További információ az adattárolók és adatkészletek virtuális hálózatban való használatáról: [hálózati elkülönítés a betanítás során & a privát virtuális hálózatokkal való következtetés](how-to-enable-virtual-network.md#use-datastores-and-datasets).

## <a name="next-steps"></a>További lépések

* Ismerje meg, [Hogyan lehet betanítani az adatkészleteket](how-to-train-with-datasets.md).
* Az automatizált gépi tanulást a TabularDatasets-mel való [betanításhoz](https://aka.ms/automl-dataset)használhatja.
* További példákat az adatkészlet betanításával kapcsolatban a [minta notebookok](https://aka.ms/dataset-tutorial)című témakörben talál.
