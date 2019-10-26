---
title: Adatkészletek létrehozása az azureml-adatkészletekkel való hozzáféréshez
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre adatkészleteket különböző forrásokból, és hogyan regisztrálhat adatkészleteket a munkaterületén
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 10/10/2019
ms.openlocfilehash: a558658d7c853560f0939c99dc5dce739d985944
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900711"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Adatkészletek létrehozása és elérése (előzetes verzió) Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészleteket (előzetes verzió), és hogyan érheti el az adatokat helyi vagy távoli kísérletekből.

Azure Machine Learning adatkészletek esetében a következőket teheti:

* Az adatkészletek által hivatkozott **tárolóban lévő adatokat egyetlen példányban tárolja** .

* **Egyszerűen férhet hozzá az adatmodell-képzésekhez** a kapcsolati karakterláncok vagy az adatelérési utak miatti gond nélkül.

* Az **Adatmegosztással & dolgozhat** más felhasználókkal.

## <a name="prerequisites"></a>Előfeltételek

Az adatkészletek létrehozásához és működéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* [Azure Machine learning munkaterület](how-to-manage-workspace.md)

* A [Azure Machine learning SDK for Python telepítve](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), amely tartalmazza a azureml-adatkészletek csomagot.

> [!Note]
> Egyes adatkészlet-osztályok (előzetes verzió) függőségei vannak a [azureml-adatelőkészítés](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomagon. A Linux-felhasználók esetében ezek az osztályok csak a következő disztribúciókban támogatottak: Red Hat Enterprise Linux, Ubuntu, Fedora és CentOS.

## <a name="dataset-types"></a>Adathalmazok típusai

Az adatkészletek két típusba vannak kategorizálva aszerint, hogy a felhasználók hogyan használják őket a képzésben.

* A [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) táblázatos formátumban jeleníti meg az adatokat a megadott fájl vagy fájlok listájának elemzésével. Ez lehetővé teszi, hogy a rendszer egy Panda vagy Spark DataFrame is megvalósuljon az információ. Egy `TabularDataset` objektum hozható létre a CSV-ből, a TSV-ből, a parketta-fájlokból, az SQL-lekérdezés eredményeiből stb. A teljes listát a [dokumentációban találhatja](https://aka.ms/tabulardataset-api-reference)meg.

* A [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) egy vagy több fájlt hivatkozik az adattárolókban vagy a nyilvános URL-címeken. Ezzel lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számítási feladatokhoz. A fájlok bármilyen formátummal rendelkezhetnek, ami lehetővé teszi a gépi tanulási forgatókönyvek széles körét, beleértve a mély tanulást is.

További információ a közelgő API-változásokról: [itt](https://aka.ms/tabular-dataset)található.

## <a name="create-datasets"></a>Adatkészletek létrehozása

Adatkészlet létrehozásával az adatforrás helyére mutató hivatkozást, valamint a hozzá tartozó metaadatok másolatát is létrehozhatja. Az adattárolók a meglévő helyükön maradnak, így nem merülnek fel extra tárolási költségek. A TabularDatasets és a FileDatasets is létrehozható a Python SDK vagy a munkaterület kezdőlapja (előzetes verzió) használatával. 

Ahhoz, hogy az adatok elérhetők legyenek Azure Machine Learning számára, az adatkészleteket az [Azure](how-to-access-data.md) -adattárolók vagy nyilvános webes URL-címek elérési útjaiból kell létrehozni.

### <a name="using-the-sdk"></a>Az SDK használata

Adatkészletek létrehozása Azure- [adattárból](how-to-access-data.md) a Python SDK használatával:

* Ellenőrizze, hogy van-e `contributor` vagy `owner` hozzáférése a regisztrált Azure adattárhoz.

* Hozza létre az adatkészletet az adattár egyik útvonalára való hivatkozással.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```
#### <a name="create-tabulardatasets"></a>TabularDatasets létrehozása

A `TabularDatasetFactory` osztály [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) metódusával olvassa be a CSV-vagy TSV-formátumú fájlokat, és hozzon létre egy nem regisztrált TabularDataset. Ha több fájlból olvas be adatokat, a rendszer összesíti az eredményeket egy táblázatos ábrázolásba.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Túlélte|Pclass|Név|szex|Kor|SibSp|Parch|Jegy|Legnagyobb légitársasága|Kabin|Megkezdte
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7,2500||S
1|2|1|1|Cumings, Mrs. John Bradley (Florence Briggs th...|female|38,0|1|0|PC 17599|71,2833|C85|C#
2|3|1|3|Heikkinen, Miss. Laina 's|female|26,0|0|0|STON/O2. 3101282|7,9250||S

A Azure SQL Databaseból való olvasáshoz használja a `TabularDatasetFactory` osztály [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) metódusát.

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

A TabularDatasets az adatok egy oszlopában adhatók meg, vagy az elérési út mintájának adatait egy idősorozat-tulajdonság engedélyezésével lehet megadni, amely lehetővé teszi az egyszerű és hatékony szűrést az idő alapján.

Használja a [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) metódust `TabularDataset` osztályban az időbélyegző oszlop megadásához és az idő szerinti szűrés engedélyezéséhez. További példákat és részleteket [itt](https://aka.ms/azureml-tsd-notebook)találhat.

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

#### <a name="create-filedatasets"></a>FileDatasets létrehozása

A fájlok bármilyen formátumban való betöltéséhez használja a `FileDatasetFactory` osztály [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) metódusát, és hozzon létre egy nem regisztrált FileDataset.

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]
mnist_ds = Dataset.File.from_files(path=web_paths)
```

### <a name="using-the-workspace-landing-page"></a>A munkaterület kezdőlapjának használata

Jelentkezzen be a [munkaterület](https://ml.azure.com) kezdőlapján, és hozzon létre egy adatkészletet a webes felületen keresztül. A munkaterület kezdőlapja támogatja mind a TabularDatasets, mind a FileDatasets létrehozását.

A következő animáció bemutatja, hogyan hozhat létre adatkészletet a munkaterület kezdőlapján.

Először válassza az **adatkészletek** lehetőséget a bal oldali ablaktábla **eszközök** részén. Ezután válassza az **+ adatkészlet létrehozása** lehetőséget az adatkészlet forrásának kiválasztásához; Ez lehet helyi fájlokból, adattárból vagy nyilvános webes URL-címekből is. Válassza ki az **adatkészlet típusát**: * táblázatos vagy fájl. A **beállítások és az előnézet** és a **séma** űrlapja intelligens módon van feltöltve a fájl típusa alapján. A **tovább** gombra kattintva megtekintheti őket, vagy az adatkészlet további konfigurálását a létrehozás előtt. Az adatkészlet létrehozásának befejezéséhez kattintson a **kész** gombra.

![Adatkészlet létrehozása a felhasználói felületen](media/how-to-create-register-datasets/create-dataset-ui.gif)

## <a name="register-datasets"></a>Adatkészletek regisztrálása

A létrehozási folyamat befejezéséhez regisztrálja az adatkészleteket egy munkaterületen.

A [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metódussal regisztrálhatja az adatkészleteket a munkaterülettel, hogy másokkal is megoszthatók legyenek, és újra felhasználhatók legyenek a különböző kísérletek között.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

>[!Note]
> A munkaterület kezdőlapján létrehozott adatkészletek automatikusan regisztrálva lesznek a munkaterületen.

## <a name="version-datasets"></a>Verziók adatkészletei

Egy új verzió létrehozásával regisztrálhat egy új adatkészletet ugyanazon a néven. Az adatkészlet verziója lehetővé teszi az adathalmazok könyvjelzővel való megjelölését, így a kísérletezéshez vagy a későbbi sokszorosításhoz az adatkészlet adott verzióját is alkalmazhatja. További információ az [adatkészletek verzióiról](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-datasets-in-your-script"></a>Adatkészletek elérése a parancsfájlban

A regisztrált adatkészletek helyileg és távolról is elérhetők a számítási fürtökön, például a Azure Machine Learning számítási feladatokhoz. A regisztrált adatkészletek kísérletek közötti eléréséhez használja a következő kódot a munkaterület és a regisztrált adatkészlet név szerinti beszerzéséhez. A `Dataset` osztály [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) metódusa alapértelmezés szerint a munkaterületen regisztrált adatkészlet legújabb verzióját adja vissza.

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

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, [Hogyan lehet betanítani az adatkészleteket](how-to-train-with-datasets.md).
* Az automatizált gépi tanulást a TabularDatasets-mel való [betanításhoz](https://aka.ms/automl-dataset)használhatja.
* Az adatkészletek betanításával kapcsolatos további példákért tekintse meg a [minta jegyzetfüzeteket](https://aka.ms/dataset-tutorial).
