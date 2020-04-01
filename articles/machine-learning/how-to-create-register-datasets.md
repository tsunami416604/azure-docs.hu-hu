---
title: Azure Machine Learning-adatkészletek létrehozása az adatok eléréséhez
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre Azure Machine Learning-adatkészleteket a gépi tanulási kísérletek hez való hozzáféréshez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: 4025c620aea49dfb26ab203630c121d29d88d9d7
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474537"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning-adatkészletek létrehozása

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan hozhat létre Azure Machine Learning-adatkészleteket a helyi vagy távoli kísérletek adatainak eléréséhez.

Az Azure Machine Learning adatkészleteivel a következőket teheti:

* Az adatok egyetlen példányát az adatkészletek által hivatkozott tárolóban tárolhatja.

* Zökkenőmentesen érheti el az adatokat a modellbetanítás során anélkül, hogy aggódnia kellene a kapcsolati karakterláncok vagy az adatelérési utak miatt.

* Adatok megosztása és együttműködés más felhasználókkal.

## <a name="prerequisites"></a>Előfeltételek
' Adatkészletek létrehozásához és az okkal való munkához a következőkre van szükség:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálja ki az [Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

* Egy [Azure Machine Learning-munkaterület.](how-to-manage-workspace.md)

* Az [Azure Machine Learning SDK python telepítve,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)amely tartalmazza az azureml-adatkészletek csomag.

> [!NOTE]
> Egyes adatkészletosztályok függőséget az [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomagtól függenek. A Linux-felhasználók számára ezek az osztályok csak a következő disztribúciókon támogatottak: Red Hat Enterprise Linux, Ubuntu, Fedora és CentOS.

## <a name="compute-size-guidance"></a>Számítási méret útmutató

Adatkészlet létrehozásakor tekintse át a számítási feldolgozási teljesítményt és a memóriában lévő adatok méretét. A tárolóban lévő adatok mérete nem egyezik meg az adatkeretben lévő adatok méretével. Például a CSV-fájlok adatai akár 10x-re is kibővülhetnek egy adatkeretben, így egy 1 GB-os CSV-fájl 10 GB-ra válhat egy adatkeretben. 

A fő tényező az, hogy mekkora az adatkészlet a memóriában, azaz adatkeretként. Javasoljuk, hogy a számítási méret és a feldolgozási teljesítmény a RAM méretének 2-szeresét tartalmazza. Tehát, ha a dataframe 10 GB, azt szeretné, hogy a számítási cél 20 + GB RAM-mal, hogy az adatkeret kényelmesen elférjen a memóriában, és feldolgozzák. Ha az adatok tömörítve vannak, tovább bővülhet; A tömörített parkettaformátumban tárolt 20 GB viszonylag ritka adat ~800 GB-ra bővülhet a memóriában. Mivel a Parquet fájlok oszlopos formátumban tárolják az adatokat, ha csak az oszlopok felére van szüksége, akkor csak ~400 GB memóriát kell betöltenie.
 
Ha Pandákat használ, nincs oka 1-nél több vCPU-nak, mivel ez minden, amit használni fog. Könnyedén párhuzamosítható számos vCPU-val egyetlen Azure Machine Learning-számítási példány/csomópont modin és Dask/Ray segítségével, és szükség esetén `import pandas as pd` `import modin.pandas as pd`egy nagy fürtre skálázható, egyszerűen átválthat a rendszerre. 
 
Ha nem tud elég nagy virtuális az adatok, akkor két lehetőség közül választhat: használja a keretrendszer, mint a Spark vagy dask a feldolgozás végrehajtásához az adatok "elfogyott a memória", azaz a dataframe betöltődik RAM partíciópartícióés feldolgozott, a végeredmény a végén gyűjtött. Ha ez túl lassú, a Spark vagy a Dask lehetővé teszi, hogy egy fürtre skálázható, amely továbbra is használható interaktívan. 

## <a name="dataset-types"></a>Adathalmaz-típusok

Két adatkészlet-típus létezik, attól függően, hogy a felhasználók hogyan használják fel őket a betanítássorán:

* [A TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) táblázatos formátumú adatokat jelöl a megadott fájl vagy fájllista elemzésével. Ez lehetővé teszi, hogy az adatok at pandákká vagy Spark DataFrame-be materializáld. Objektumot `TabularDataset` .csv, .tsv, .parquet, .jsonl fájlokból és SQL-lekérdezési eredményekből hozhat létre. A teljes listát lásd: [TabularDatasetFactory osztály](https://aka.ms/tabulardataset-api-reference).

* A [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) osztály egy vagy több fájlra hivatkozik az adattárakban vagy a nyilvános URL-címekben. Ezzel a módszerrel letöltheti vagy csatlakoztathatja a fájlokat a compute-hoz FileDataset objektumként. A fájlok bármilyen formátumban lehetnek, ami lehetővé teszi a gépi tanulási forgatókönyvek szélesebb körét, beleértve a mély tanulást is.

Ha többet szeretne megtudni a közelgő API-változásokról, olvassa el [a Dataset API módosítási értesítése](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Adatkészletek létrehozása

Adatkészlet létrehozásával hivatkozást hozhat létre az adatforrás helyére, valamint a metaadatok másolatát. Mivel az adatok a meglévő helyen maradnak, nem kell többlettárolási költséget fizetnie. A Python `TabularDataset` SDK vagy a használatával mind `FileDataset` https://ml.azure.comés adatkészleteket létrehozhat.

Ahhoz, hogy az adatok elérhetők legyenek az Azure Machine Learning által, az adatkészleteket [az Azure-adattárakban](how-to-access-data.md) vagy nyilvános webes URL-címeken lévő elérési utakból kell létrehozni. 

### <a name="use-the-sdk"></a>Az SDK használata

Adatkészletek létrehozása egy [Azure-adattárból](how-to-access-data.md) a Python SDK használatával:

1. Ellenőrizze, hogy `contributor` `owner` rendelkezik-e a regisztrált Azure-adattárral, vagy hozzáfér-e.

2. Hozza létre az adatkészletet az adattár elérési útjaira hivatkozva.
> [!Note]
> Több adattárban több elérési útból is létrehozhat adatkészletet. Nincs szigorú korlát a fájlok száma vagy az adatok mérete, amely hozhat létre egy adatkészletet. Azonban minden egyes adatelérési út esetében a rendszer néhány kérést küld a tárolószolgáltatásnak, hogy ellenőrizze, hogy fájlra vagy mappára mutat-e. Ez a többletterhelés teljesítménycsökkenhetvagy meghibásodáshoz vezethet. Egy 1000 fájllal rendelkező mappára hivatkozó adatkészlet egy adatelérési útra hivatkozik. Azt javasoljuk, hogy az optimális teljesítmény érdekében 100-nál kevesebb elérési utat tartalmazó adatkészletet hozzon létre az adattárakban.

#### <a name="create-a-tabulardataset"></a>Táblázatos adatkészlet létrehozása

Az [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-) `TabularDatasetFactory` osztály metódusával .csv vagy .tsv formátumban olvashat fájlokat, és nem regisztrált táblázatos adathalmazt hozhat létre. Ha több fájlból olvas, az eredmények egyetlen táblázatos ábrázolásba lesznek összesítve. 

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Alapértelmezés szerint a táblázatos adathalmaz létrehozásakor az oszlopadattípusok automatikusan kikövetkeztenek. Ha a kikövetkeztetett típusok nem felelnek meg az elvárásoknak, oszloptípusokat adhat meg a következő kód használatával. A `infer_column_type` paraméter csak a tagolt fájlokból létrehozott adatkészletek esetében alkalmazható. [A támogatott adattípusokról is többet tudhat meg.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)

> [!IMPORTANT] 
> Ha a tárhely egy virtuális hálózat vagy tűzfal mögött van, csak az SDK-n keresztül létrehozott adatkészlet támogatott. Az adatkészlet létrehozásához mindenképpen adja meg `validate=False` `infer_column_types=False` a `from_delimited_files()` paramétereket és a metódusban. Ez megkerüli a kezdeti érvényesítési ellenőrzést, és biztosítja, hogy az adatkészletet ezekből a biztonságos fájlokból hozhassa létre. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Túlélte|Posztály|Név|szex|Kor|SibSp között|Parch|Ticket|Viteldíj|Kabin|Beszállt
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False (Hamis)|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True (Igaz)|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85 között|C#
2|3|True (Igaz)|3|Heikkinen, kisasszony. Laina között|female|26.0|0|0|STON/O2. 3101282|7.9250||S


Ha egy memóriapanda dataframe-ből szeretne adatkészletet létrehozni, írja az adatokat egy helyi fájlba, például egy csv-fájlba, és hozza létre az adatkészletet abból a fájlból. A következő kód bemutatja ezt a munkafolyamatot.

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

Használja [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) az osztály `TabularDatasetFactory` metódusát az Azure SQL Database-ből való olvasáshoz:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

A TabularDatasets-ben megadhatja az időbélyegzőt az adatok egyik oszlopából vagy attól a területről, ahol az útvonalminta-adatok at tárolják az idősorozat-vonás engedélyezéséhez. Ez a specifikáció lehetővé teszi az egyszerű és hatékony szűrést az idő szerint.

Az [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) osztály metódusával megadhatja az időbélyegző oszlopát, és engedélyezheti az`TabularDataset` idő szerinti szűrést. További információ: [Táblázatos idősorozatokkal kapcsolatos API-bemutató a NOAA időjárási adatokkal.](https://aka.ms/azureml-tsd-notebook)

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

#### <a name="create-a-filedataset"></a>Fájladathalmaz létrehozása

Az [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) `FileDatasetFactory` osztály metódusával bármilyen formátumban betölthet fájlokat, és nem regisztrált FileDataset-et hozhat létre. Ha a tárhely egy virtuális hálózat vagy tűzfal `validate =False` mögött `from_files()` van, állítsa be a paramétert a metódusban. Ez megkerüli a kezdeti ellenőrzési lépést, és biztosítja, hogy az adatkészletet ezekből a biztonságos fájlokból hozhassa létre.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>Az interneten 
Az alábbi lépések és animációk bemutatják, hogyan hozhat https://ml.azure.comlétre adatkészletet az Azure Machine Learning stúdióban.

![Adatkészlet létrehozása a felhasználói felülettel](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Adatkészlet létrehozása a stúdióban:
1. Jelentkezzen be https://ml.azure.coma helyen.
1. Válassza **az adatkészletek lehetőséget** a bal oldali ablaktábla **Eszközök** szakaszában. 
1. **Válassza az Adatkészlet létrehozása** lehetőséget az adatkészlet forrásának kiválasztásához. Ez a forrás lehet helyi fájl, adattár vagy nyilvános URL.This source can be local files, a datastore, or public URLs.
1. Válassza **a Táblázatos** vagy **a Fájl** az adatkészlet típusához lehetőséget.
1. Válassza a **Tovább** gombot az Adattár és a **fájlkijelölési** űrlap megnyitásához. Ezen a képernyőn kiválaszthatja, hogy hol szeretné tartani az adatkészletet a létrehozás után, valamint kiválaszthatja, hogy milyen adatfájlokat használjon az adatkészlethez. 
1. Válassza a **Tovább** gombra a **Beállítások, az előnézeti** és **sémaűrlapok feltöltéséhez** lehetőséget; a fájltípus alapján intelligensen vannak feltöltve, és az adatkészletet az ilyen űrlapoklétrehozása előtt tovább konfigurálhatja. 
1. Válassza a **Tovább** gombot a **Részletek megerősítése** űrlap áttekintéséhez. Ellenőrizze a beállításokat, és hozzon létre egy opcionális adatprofilt az adatkészlethez. További információ az [adatprofilozásról.](how-to-use-automated-ml-for-ml-models.md#profile) 
1. Az adatkészlet létrehozásának befejezéséhez válassza a **Létrehozás** gombot.

## <a name="register-datasets"></a>Adatkészletek regisztrálása

A létrehozási folyamat befejezéséhez regisztrálja az adatkészleteket egy munkaterülettel. A [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) módszerrel regisztrálhatja az adatkészleteket a munkaterülettel, hogy megoszthassa őket másokkal, és újra felhasználhassa őket különböző kísérletekben:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Az Azure Machine Learning studión keresztül létrehozott adatkészletek automatikusan regisztrálva vannak a munkaterületre.

## <a name="create-datasets-with-azure-open-datasets"></a>Adatkészletek létrehozása Azure Open adatkészletekkel

[Az Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) olyan nyilvános adatkészletek, amelyek segítségével forgatókönyv-specifikus funkciókat adhat a gépi tanulási megoldásokhoz a pontosabb modellek érdekében. Az adatkészletek közé tartoznak az időjárásra, a népszámlálásra, az ünnepekre, a közbiztonságra és a helyre vonatkozó nyilvános adatok, amelyek segítenek a gépi tanulási modellek betanításában és a prediktív megoldások gazdagításában. A nyílt adatkészletek a Microsoft Azure felhőjében találhatók, és mind az SDK, mind a munkaterület felhasználói felületén megtalálhatók.

### <a name="use-the-sdk"></a>Az SDK használata

Ha az SDK-ból az Azure Open adatkészletekkel szeretne adatkészleteket `pip install azureml-opendatasets`létrehozni, győződjön meg arról, hogy telepítette a csomagot. Minden különálló adatkészletet saját osztály képvisel az SDK-ban, és bizonyos osztályok `TabularDataset` `FileDataset`a , vagy mindkettőként érhetők el. Az osztályok teljes listáját a [referenciadokumentációban](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) találja.

Bizonyos osztályokat lekérhet `TabularDataset` vagy `FileDataset`a vagy , amely lehetővé teszi a fájlok közvetlen manipulálását és/ vagy letöltését. Más osztályok **csak** az egyik `get_tabular_dataset()` vagy `get_file_dataset()` függvény használatával kaphatnak adatkészletet. A következő kódminta néhány példát mutat be az ilyen típusú osztályokra.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Amikor nyílt adatkészletekből létrehozott adatkészletet regisztrál, a rendszer nem töltődik le azonnal, de az adatok később érik el őket, amikor azt kérik (például a betanítás során) egy központi tárolóhelyről.

### <a name="use-the-ui"></a>A felhasználói felület használata

Az Open Datasets osztályokból is létrehozhat adatkészleteket a felhasználói felületen keresztül. A munkaterületen válassza az **Adatkészletek** lapot az **Eszközök**csoportban. Az **Adatkészlet létrehozása** legördülő menüben válassza **a Nyitott adatkészletek közül**lehetőséget.

![Nyitott adatkészlet a felhasználói felülettel](./media/how-to-create-register-datasets/open-datasets-1.png)

Jelöljön ki egy adatkészletet a csempéjének kijelölésével. (A keresősáv használatával szűrheti a programot.) Válassza a **Tovább lehetőséget.**

![Adatkészlet kiválasztása](./media/how-to-create-register-datasets/open-datasets-2.png)

Válassza ki azt a nevet, amely alatt regisztrálni szeretné az adatkészletet, és szükség esetén szűrje az adatokat a rendelkezésre álló szűrők használatával. Ebben az esetben a munkaszüneti napok adatkészlet, szűrheti az időszak egy évre, és az országkód csak az Egyesült Államokban. Kattintson a **Létrehozás** gombra.

![Adatkészlet-params beállítása és adatkészlet létrehozása](./media/how-to-create-register-datasets/open-datasets-3.png)

Az adatkészlet most már elérhető a munkaterületen **az Adatkészletek**területen. Ugyanúgy használhatja, mint a többi létrehozott adatkészletet.

## <a name="version-datasets"></a>Verzióadatkészletek

Új adatkészletet regisztrálhat ugyanazzal a névvel egy új verzió létrehozásával. Az adatkészlet verziója az adatok állapotának könyvjelzővel történő megjelölésével teszi learatható, így az adatkészlet egy adott verzióját alkalmazhatja kísérletezésre vagy későbbi sokszorosításra. További információ az [adatkészlet-verziókról.](how-to-version-track-datasets.md)
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

## <a name="access-datasets-in-your-script"></a>A parancsfájl adatkészleteielérése

A regisztrált adatkészletek helyileg és távolról is elérhetők az Azure Machine Learning-alapú számítási fürtökön. A regisztrált adatkészlet kísérletek során történő eléréséhez használja a következő kódot a munkaterület és a regisztrált adatkészlet név szerinti eléréséhez. Alapértelmezés szerint [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) az osztály `Dataset` metódusa a munkaterülethez regisztrált adatkészlet legújabb verzióját adja vissza.

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

## <a name="next-steps"></a>További lépések

* További információ [az adatkészletek betanításáról.](how-to-train-with-datasets.md)
* A [Táblázatos adatkészletekkel automatikus](https://aka.ms/automl-dataset)gépi tanulás segítségével tanítható be.
* További adatkészlet-betanítási példákat a [mintajegyzetfüzetekben](https://aka.ms/dataset-tutorial)talál.
