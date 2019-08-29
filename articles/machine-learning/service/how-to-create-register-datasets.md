---
title: Adatkészletek létrehozása az azureml-adatkészletekkel való hozzáféréshez
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan hozhat létre adatkészleteket különböző forrásokból, és hogyan regisztrálhat adatkészleteket a munkaterületén
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/22/2019
ms.openlocfilehash: e5d5d36e82914f1d6d03299db0ed1427ac5a389a
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147591"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Adatkészletek létrehozása és elérése (előzetes verzió) Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészleteket (előzetes verzió), és hogyan érheti el az adatokat helyi vagy távoli kísérletekből.

Azure Machine Learning adatkészletek esetében a következőket teheti: 

* Az adatkészletek által hivatkozott **tárolóban lévő adatokat egyetlen példányban tárolja** . 

* **Egyszerűen férhet hozzá az adatmodell** -képzésekhez a kapcsolati karakterláncok vagy az adatelérési utak miatti gond nélkül.

* Az adatmegosztással **& dolgozhat** más felhasználókkal.

## <a name="prerequisites"></a>Előfeltételek

Az adatkészletek létrehozásához és működéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy [Azure Machine learning szolgáltatás](how-to-manage-workspace.md) munkaterülete

* A [Azure Machine learning SDK for Python telepítve](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), amely tartalmazza a azureml-adatkészletek csomagot.

> [!Note]
> Egyes adatkészlet-osztályok (előzetes verzió) függőségei vannak a [azureml-adatelőkészítés](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomagon. A Linux-felhasználók esetében ezek az osztályok csak a következő disztribúciókban támogatottak:  Red Hat Enterprise Linux, Ubuntu, Fedora és CentOS.

## <a name="dataset-types"></a>Adathalmazok típusai

Az adatkészletek különböző típusokra vannak kategorizálva, attól függően, hogy a felhasználók hogyan használják őket a képzésben. Adatkészlet-típusok listája:
* A [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) táblázatos formátumban jeleníti meg az adatokat a megadott fájl vagy fájlok listájának elemzésével. Ez lehetővé teszi, hogy egy Panda DataFrame megvalósuljon az információ. Létrehozhat `TabularDataset` egy objektumot a CSV, a TSV, a Parquet Files, az SQL-lekérdezési eredmények stb. alapján. A teljes listát a dokumentációban találhatja [](https://aka.ms/tabulardataset-api-reference)meg.
* A FileDataset egy vagy több fájlt hivatkozik az adattárolókban vagy a nyilvános URL-címeken. Ezzel lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számítási feladatokhoz. A fájlok bármilyen formátummal rendelkezhetnek, ami lehetővé teszi a gépi tanulási forgatókönyvek széles körét, beleértve a mély tanulást is.

További információ a közelgő API-változásokról: [itt](https://aka.ms/tabular-dataset)található.

## <a name="create-datasets"></a>Adatkészletek létrehozása 

Adatkészlet létrehozásával az adatforrás helyére mutató hivatkozást, valamint a hozzá tartozó metaadatok másolatát is létrehozhatja. Az adattárolók a meglévő helyükön maradnak, így nem merülnek fel extra tárolási költségek.

Ahhoz, hogy az adatok elérhetők legyenek Azure Machine Learning szolgáltatás számára, az adatkészleteket az [Azure](how-to-access-data.md) -adattárolók vagy nyilvános webes URL-címek elérési útjaiból kell létrehozni.

Adatkészletek létrehozása Azure- [adattárból](how-to-access-data.md):

* Ellenőrizze, hogy `contributor` van `owner` -e hozzáférése a regisztrált Azure-adattárhoz.

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
### <a name="create-tabulardatasets"></a>TabularDatasets létrehozása

Az `from_delimited_files()` `TabularDatasetFactory` osztályban található metódus használatával olvassa be a CSV-vagy TSV-formátumú fájlokat, és hozzon létre egy nem regisztrált TabularDataset. Ha több fájlból olvas be adatokat, a rendszer összesíti az eredményeket egy táblázatos ábrázolásba.

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

| |PassengerId|Túlélte|Pclass|Name (Név)|szex|Kor|SibSp|Parch|Jegy|Legnagyobb légitársasága|Kabin|Megkezdte
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7,2500||S
1|2|1|1|Cumings, Mrs. John Bradley (Florence Briggs th...|female|38,0|1|0|PC 17599|71,2833|C85|C
2|3|1|3|Heikkinen, Miss. Laina 's|female|26,0|0|0|STON/O2. 3101282|7,9250||S

### <a name="create-filedatasets"></a>FileDatasets létrehozása
Az `FileDatasetFactory` osztályban `from_files()` lévő metódus használatával bármilyen formátumban betöltheti a fájlokat, és létrehozhat egy nem regisztrált FileDataset.

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
## <a name="register-datasets"></a>Adatkészletek regisztrálása

A létrehozási folyamat befejezéséhez regisztrálja az adatkészleteket a munkaterületen:

Az adatkészletek a munkaterületre való regisztrálásához használja a `register()` metódust, hogy azok megoszthatók másokkal, és újra felhasználhatók legyenek a különböző kísérletek között.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

## <a name="version-datasets"></a>Verziók adatkészletei

Egy új verzió létrehozásával regisztrálhat egy új adatkészletet ugyanazon a néven. Az adatkészlet verziója lehetővé teszi az adathalmazok könyvjelzővel való megjelölését, így a kísérletezéshez vagy a későbbi sokszorosításhoz az adatkészlet adott verzióját is alkalmazhatja. A verziószámozás megfontolásának tipikus forgatókönyvei: 
* Ha új adatgyűjtési lehetőség áll rendelkezésre.
* Ha különböző adatelőkészítési vagy szolgáltatás-mérnöki módszereket alkalmaz.

```Python
# create a TabularDataset from new Titanic training data
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


## <a name="access-your-data-during-training"></a>Az adatai elérése a betanítás során

A regisztrált adatkészletek helyileg és távolról is elérhetők a számítási fürtökön, például a Azure Machine Learning számítási feladatokhoz. A regisztrált adatkészletek kísérletek közötti eléréséhez használja a következő kódot a munkaterület és a regisztrált adatkészlet név szerinti beszerzéséhez. Az [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) `Dataset` osztály metódusa alapértelmezés szerint a munkaterületen regisztrált adatkészlet legújabb verzióját adja vissza.

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

* Az automatizált gépi tanulást a [TabularDatasets](https://aka.ms/automl-dataset)-mel való betanításhoz használhatja.
* Az adatkészletek betanításával kapcsolatos további példákért tekintse meg a [minta jegyzetfüzeteket](https://aka.ms/dataset-tutorial).
