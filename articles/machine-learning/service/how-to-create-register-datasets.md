---
title: Hozzon létre és regisztrálja a munkaterület adatkészletek
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
ms.date: 05/02/19
ms.openlocfilehash: 65a861c647c2dc92e416fa356075821aa5060042
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205043"
---
# <a name="create-and-register-azure-machine-learning-datasets-preview"></a>Hozzon létre és regisztrálja az Azure Machine Learning-adatkészletek (előzetes verzió)

Ez a cikk bemutatja az Azure Machine Learning a munkafolyamatok létrehozását és regisztrálását az adatkészleteket, és hogyan érheti el őket újból a helyi és távoli kísérletek között.

Az Azure Machine Learning-adatkészletek (előzetes verzió) egyszerűbbé eléréséhez, és dolgozhat az adatokkal. Az adatkészletek különböző alkalmazási helyzetekben, mint például a modellek tanítása és folyamat létrehozása adatok kezelésére. Használatával a [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), is adatok népszerű formátumok használata, alapul szolgáló tároló eléréséhez, ismerje meg, és előkészíti az adatokat, különböző adatkészlet-definíciókban életciklusának kezelése és hasonlítsa össze a használt adatkészletek között képzés és éles környezetben.

## <a name="prerequisites"></a>Előfeltételek

Létrehozására és regisztrálására adatkészletek lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Az Azure Machine Learning szolgáltatás munkaterületén. Lásd: [hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Az Azure Machine Learning SDK Pythonhoz készült. Telepíteni, vagy frissítsen az SDK legújabb verzióját, lásd: [telepítése vagy frissítése az SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

## <a name="create-datasets-from-local-files"></a>A helyi fájlokból-adatkészletek létrehozása

A helyi gépen a fájl vagy mappa elérési út megadásával betölteni a [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) módszerrel a `Dataset` osztály.  Ez a módszer akkor adja meg a fájl típusa és elemzési argumentumok nélkül hajtja végre az alábbi lépéseket:

* Adatcsatornához és a kivonni kívánt beállítást.
* Átugorja a felső részén a fájl üres rekordokat.
* Adatcsatornához és a fejlécsor beállítást.
* Adatcsatornához és oszlop adattípus konvertálásakor.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Másik lehetőségként a fájl-specifikus függvények használatával explicit módon vezérelheti a fájl elemzése. Jelenleg az adatkészletek SDK támogatja a [tagolt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-), [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-), [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-), [bináris](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-), és [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-) fájlformátum.

## <a name="create-datasets-from-azure-datastores"></a>Az Azure adattárainak-adatkészletek létrehozása

Egy Azure adattárolója adatkészleteket létrehozni, hogy ne felejtse el:

* Ellenőrizze, hogy rendelkezik, közreműködő vagy tulajdonos, a regisztrált Azure adattárolója való hozzáférést.

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

||ID (Azonosító)|Eset száma|Dátum|Letiltás|IUCR|Elsődleges típusa|Leírás|Leírás helye|Letartóztatás|Hazai|...|Ward|Közösségi terület|Az FBI kód|X koordinátáját|Y koordinátája|Év|Frissítés dátuma|Szélesség|Hosszúság|Hely|
|--|--|---|---|---|---|----|------|-------|------|-----|---|----|----|-----|-----|------|----|-----|----|----|-----
|0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|MEGTÉVESZTŐ ELJÁRÁS|PÉNZÜGYI ADATOKKAL VALÓ VISSZAÉLÉS KERESZTÜL 300 USD|EGYÉB|FALSE|FALSE|...|9|50|11|1183356|1831503|2016|5/11/2016 15:48|41.69283384|-87.60431945|(41.692833841, -87.60431945)|
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD ELENTÉS MENTÉSE|890|LOPÁS| FROM BUILDING|RESIDENCE|FALSE|FALSE|...|21|71|6|1166776|1850053|2016|5/12/2016 15:48|41.74410697|-87.66449429|(41.744106973, -87.664494285)
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTÓI ELENTÉS MENTÉSE|1154|MEGTÉVESZTŐ ELJÁRÁS|PÉNZÜGYI ADATOKKAL VALÓ VISSZAÉLÉS 300 USD MAJD A|RESIDENCE|FALSE|FALSE|...|19|74|11|||2016|5/12/2016 15:50
3|10519591|HZ261534|4/15/2016 9:00|113XX S VALÓ MENTÉSE|1120|MEGTÉVESZTŐ ELJÁRÁS|FORGERY|RESIDENCE|FALSE|FALSE|...|9|49|10|||2016|5/13/2016 15:51
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE ELENTÉS MENTÉSE|890|LOPÁS|FROM BUILDING|SCHOOL, PUBLIC, BUILDING|FALSE|FALSE|...|40|13|6|||2016|5/25/2016 15:59|

## <a name="register-your-datasets-with-workspace"></a>Az adatkészletek regisztrálás munkaterület

Használja a [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metódus adatkészletek regisztrálásához megosztása a munkaterülethez, és újra felhasználhatja a szervezeten belül, és különböző kísérletek között.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',

                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Az alapértelmezett paraméter beállítása `register()` van `exist_ok = False`. Ha próbál meg regisztrálni egy adatkészlet ugyanazzal a névvel, ez a beállítás módosítása nélkül egy hibát eredményez.

A `register()` metódus visszaadja a már regisztrált adatkészletek a paraméter beállítása `exist_ok = True`.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
```

Használat `list()` látható az összes, a regisztrált adatkészletek a munkaterületén.

```Python
Dataset.list(workspace_name)
```

A fenti kód a következőket eredményezi:

```Python
[Dataset(Name: dataset_crime,
         Workspace: workspace_name)]
```

## <a name="access-datasets-in-workspace"></a>A munkaterület adatkészletek elérése

Helyileg, távolról és például az Azure Machine Learning compute számítási fürtök a regisztrált adatkészletek elérhető és értelmezhető. A regisztrált adatkészlet újbóli kísérletek között, és a számítási környezetek, az alábbi kód használatával név szerint a munkaterület és a regisztrált adatkészlet lekérése.

```Python
workspace = Workspace.from_config()

dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>További lépések

* [Ismerje meg, és adatkészletek előkészítése](how-to-explore-prepare-data.md).
* [Az adatkészlet-definíciók életciklusának kezelése](how-to-manage-dataset-definitions.md).
* Példa adatkészleteket használó, tekintse meg a [notebookok minta](https://aka.ms/dataset-tutorial).
