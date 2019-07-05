---
title: Az azureml-adatkészletek adatokhoz való hozzáférést-adatkészletek létrehozása
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan különböző forrásokból származó adatkészletek létrehozása, és regisztrálja a munkaterület adatkészletek
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: a879fa17244977277dab3e2e66c5888a44759764
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444030"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Hozzon létre, és az Azure Machine Learning (előzetes verzió) adatkészletek elérése

Ebből a cikkből megismerheti, hogyan hozhat létre az Azure Machine Learning-adatkészletek (előzetes verzió) és az adatok elérése a helyi és távoli kísérletek.

A felügyelt adatkészletek segítségével: 
* **Könnyen hozzáférhet az adatokhoz a modell betanítása közben** alapul szolgáló adattárak újracsatlakozás nélkül

* **Győződjön meg arról, az adatkonzisztencia és megismételhetőség** kísérletek között ugyanazon mutató használatával: notebookok, automatizált ml, a folyamatok, vizuális felhasználói felületet

* **Adatok megosztása és együttműködés** más felhasználókkal

* **Adatok feltárása** & adatok pillanatképeinek és verziói életciklusának kezelése

* **Hasonlítsa össze az adatokat** éles képzés


## <a name="prerequisites"></a>Előfeltételek

Hozhat létre és adatkészletekkel működnek együtt lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy [Azure Machine Learning szolgáltatás munkaterület](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* A [Azure Machine Learning SDK telepítve van a Pythonhoz készült](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), amely tartalmazza az azureml-adatkészletek csomagot.

> [!Note]
> Néhány adatkészlet osztályok (előzetes verzió) függőségekkel rendelkezik a [azureml-adatelőkészítés](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomag (elérhetővé tétel GA). A Linux-felhasználók ezeket az osztályokat csak a következő disztribúciók a támogatják:  Red Hat Enterprise Linux, Ubuntu, Fedora, and CentOS.

## <a name="data-formats"></a>Adatformátumok a célnyelven

Az Azure Machine Learning-adatkészlet hozhat létre a következő adatokat:
+ [delimited](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none-)
+ [Bináris](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet eszközökben](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [Azure SQL Database](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Azure Data Lake gen. 1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>Adatkészletek létrehozása 

Az azureml-adatkészletek csomaggal, az adatkészletek kezelheti a [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) és kifejezetten [a `Dataset` osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py).

### <a name="create-from-local-files"></a>A helyi fájlok létrehozása

A helyi gépen a fájl vagy mappa elérési út megadásával betölteni a [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) módszerrel a `Dataset` osztály.  Ez a módszer akkor adja meg a fájl típusa és elemzési argumentumok nélkül hajtja végre az alábbi lépéseket:

* Adatcsatornához és a kivonni kívánt beállítást.
* Átugorja a felső részén a fájl üres rekordokat.
* Adatcsatornához és a fejlécsor beállítást.
* Adatcsatornához és oszlop adattípus konvertálásakor.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Másik lehetőségként a fájl-specifikus függvények használatával explicit módon vezérelheti a fájl elemzése. 


### <a name="create-from-azure-datastores"></a>Az Azure adattárainak létrehozása

Adatkészletek létrehozása egy Azure-adattárhoz:

* Győződjön meg arról, hogy `contributor` vagy `owner` a regisztrált Azure adattárolója való hozzáférést.

* Importálás a [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) és [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) és `Dataset` az SDK-csomagot.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 A `get()` metódus lekér egy meglévő adattárhoz a munkaterületen.

```
dstore = Datastore.get(workspace, datastore_name)
```

Használja a `from_delimited_files()` módszer, olvassa el a tagolt fájlokat, és a egy nem regisztrált adatkészlet létrehozásához.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Az adatkészletek regisztrálása

A létrehozási folyamat befejezéséhez regisztrálja az adatkészletekből munkaterület:

Használja a [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) módszer regisztrálásához adatkészletek a munkaterülethez, így azok másokkal, és újra felhasználható különböző kísérletek között.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Ha `exist_ok = False` (alapértelmezett), és a hiba akkor fordul elő, mint egy másik, azonos nevű adatkészlet regisztrálását kísérli. Állítsa be `True` felülírja a meglévő.

## <a name="access-data-in-datasets"></a>Hozzáférés az adatokhoz a adatkészletek

Helyileg, távolról és például az Azure Machine Learning compute számítási fürtök a regisztrált adatkészletek elérhető és értelmezhető. A regisztrált adatkészlet újbóli kísérletek között, és a számítási környezetek, az alábbi kód használatával név szerint a munkaterület és a regisztrált adatkészlet lekérése.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>További lépések

* [Ismerje meg, és adatkészletek előkészítése](how-to-explore-prepare-data.md).
* Példa adatkészleteket használó, tekintse meg a [notebookok minta](https://aka.ms/dataset-tutorial).
