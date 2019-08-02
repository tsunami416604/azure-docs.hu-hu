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
ms.date: 05/21/2019
ms.openlocfilehash: 473bf87e1961c3c7687b0867885adef40c14d71f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694330"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Adatkészletek létrehozása és elérése (előzetes verzió) Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészleteket (előzetes verzió), és hogyan érheti el az adatokat helyi vagy távoli kísérletekből.

Azure Machine Learning adatkészletek esetében a következőket teheti: 

* Az adatkészletek által hivatkozott **tárolóban tárolt adatokat egyetlen példányban tárolja**

* **Adatgyűjtés** felderítő adatelemzéssel 

* **Könnyedén hozzáférhet az adatmodell** -képzéshez a kapcsolati sztring vagy az adatelérési út miatti gond nélkül

* **Az adatmegosztás &** más felhasználókkal való együttműködéssel

## <a name="prerequisites"></a>Előfeltételek

Az adatkészletek létrehozásához és működéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy [Azure Machine learning szolgáltatás](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace) munkaterülete

* A [Azure Machine learning SDK for Python telepítve](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), amely tartalmazza a azureml-adatkészletek csomagot.

> [!Note]
> Egyes adatkészlet-osztályok (előzetes verzió) függőségei vannak a [azureml-adatelőkészítés](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomaggal (GA). A Linux-felhasználók esetében ezek az osztályok csak a következő disztribúciókban támogatottak:  Red Hat Enterprise Linux, Ubuntu, Fedora és CentOS.

## <a name="data-formats"></a>Adatformátumok

Azure Machine Learning adatkészletet a következő formátumokból hozhat létre:
+ [tagolt](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none--partition-format-none-)
+ [json](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false--partition-format-none-)
+ [Excel](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true--partition-format-none-)
+ [Parquet](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false--partition-format-none-)
+ [Panda DataFrame](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-pandas-dataframe-dataframe--path-none--in-memory-false-)
+ [SQL-lekérdezés](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [bináris](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)

## <a name="create-datasets"></a>Adatkészletek létrehozása 

Adatkészlet létrehozásával az adatforrás helyére mutató hivatkozást, valamint a hozzá tartozó metaadatok másolatát is létrehozhatja. Az adattárolók a meglévő helyükön maradnak, így nem merülnek fel extra tárolási költségek.

### <a name="create-from-local-files"></a>Létrehozás helyi fájlokból

Töltse le a fájlokat a helyi gépről úgy, hogy megadja a fájl vagy mappa elérési [`auto_read_files()`](/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false--partition-format-none-) útját `Dataset` a osztály metódusával.  Ez a metódus a következő lépéseket hajtja végre anélkül, hogy meg kellene adnia a fájl típusát vagy az elemzési argumentumokat:

* Következtetés és az elválasztó beállítása.
* Üres rekordok kihagyása a fájl tetején.
* Következtetés és a fejlécsor beállítása.
* Az oszlopok adattípusának kikövetkeztetés és átalakítása.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Azt is megteheti, hogy a fájl-specifikus függvények segítségével explicit módon szabályozza a fájl elemzését. 


### <a name="create-from-azure-datastores"></a>Létrehozás Azure-Adattárokból

Adatkészletek létrehozása Azure- [adattárból](how-to-access-data.md):

* Ellenőrizze, hogy `contributor` van `owner` -e hozzáférése a regisztrált Azure-adattárhoz.

* Az adatkészlet létrehozása az adattár egyik útvonalára való hivatkozással 

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
dstore = Datastore.get(workspace, datastore_name)
```

A `from_delimited_files()` metódus használatával tagolt fájlokat olvashat egy [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), és létrehozhat egy nem regisztrált adatkészletet.

```Python
# create an in-memory Dataset on your local machine
dataset = Dataset.from_delimited_files(dstore.path('data/src/crime.csv'))

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Adatkészletek regisztrálása

A létrehozási folyamat befejezéséhez regisztrálja az adatkészleteket a munkaterületen:

Az adatkészletek a munkaterületre való regisztrálásához használja a [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metódust, hogy azok megoszthatók másokkal, és újra felhasználhatók legyenek a különböző kísérletek között.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Ha `exist_ok = False` (alapértelmezett), és egy másikkal megegyező nevű adatkészletet próbál regisztrálni, hiba történik. Állítsa a `True` beállítást a meglévő felülírásához.

## <a name="access-data-in-datasets"></a>Adatkészletekben tárolt adathozzáférés

A regisztrált adatkészletek helyileg és távolról is elérhetők a számítási fürtökön, például a Azure Machine Learning számítási feladatokhoz. A regisztrált adatkészletek kísérletek közötti eléréséhez használja a következő kódot a munkaterület és a regisztrált adatkészlet név szerinti beszerzéséhez.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
print(Dataset.list(workspace))

# Get dataset by name
dataset = Dataset.get(workspace, 'dataset_crime')

# Load data into pandas DataFrame
dataset.to_pandas_dataframe()
```

## <a name="next-steps"></a>További lépések

* [Az adatkészletek megismerése és előkészítése](how-to-explore-prepare-data.md).
* Az adatkészletek használatára vonatkozó példát a [minta notebookok](https://aka.ms/dataset-tutorial)című témakörben talál.
