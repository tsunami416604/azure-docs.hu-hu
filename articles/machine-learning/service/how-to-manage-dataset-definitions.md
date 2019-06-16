---
title: Verzió és az adatkészlet-definíciók az azureml-adatkészletek kezelése
titleSuffix: Azure Machine Learning service
description: Az adatkészlet-definíciók frissítése és a definíciók életciklusának kezelése
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 4476bdd902f054683fa544dc44b548689f3a1881
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241884"
---
# <a name="version-and-manage-your-dataset-definitions"></a>Verzió és az adatkészlet-definíciókban kezelése

Ismerje meg, hogyan frissítse és kezelése az Azure Machine Learning-adatkészletek (előzetes verzió) az adatkészlet-definíciók.

Adatkészlet-definíciókat a módszereket, az adatkészletek, többek között a forrásadatok és az Adatátalakítási lépéseket végre mindkét a hivatkozás előkészítéséhez használható. Egy adatkészlet lehet számos definíciókat, és a saját életciklusának minden egyes definíciójában.

A következő esetben egy adatkészlet-definíciókban használatának példája:

1. Létrehozhat egy machine learning-folyamatot, amely az aktuális az adatkészlet-definíció használ fel.
1. Az alapul szolgáló adatok változásának; új attribútumok lettek hozzáadva.
1. A definíció, amely hozzáadja az új attribútumok kezeléséhez további átalakítások új verzióját hozza létre.

Ebben a példában a meglévő folyamat továbbra is az eredeti definíciójának verzió felhasználását. Új forgatókönyvek használhatók, például egy modell, vagy létrehoz egy folyamatot az új definíciós verzióval használható.

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz egy Azure-előfizetés és a egy munkaterületet, regisztrálja az adatkészlet ahhoz, hogy az adatkészlet-definíciók életciklusának kezeléséhez.

A mintafájl, amelyet az ebben a dokumentumban szereplő példák érhető el: [ https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv ](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv).

## <a name="create-and-update-definitions"></a>Hozzon létre és -leírások frissítése

Rendszer először létrehoz és regisztrálja a munkaterület egy adatkészletet.

```python
from azureml.core import Workspace, Datastore, Dataset

# change the configuration for workspace and Datastore
subscription_id = 'your subscription id'
resource_group = 'your resource group name'
workspace_name = 'your workspace name'
datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace(subscription_id, resource_group, workspace_name)

# get a Datastore that has already been created in the workspace
dstore = Datastore.get(workspace, datastore_name)

# create an in-memory Dataset from Datastore
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# register the Dataset with the workspace. if a Dataset with the same name already exists, retrieve it by turning `exist_ok = True`
dataset_name = 'crime dataset'
dataset = dataset.register(workspace = workspace, 
                 name = dataset_name, 
                 description = 'crime dataset for demo', 
                 tags = {'year':'2019', 'month':'Apr'},
                 exist_ok = True)
```

Az első adatkészlet-definícióban (`version_id` = 1) amikor létrejön az adatkészlet jön létre. Ezután minden alkalommal, amikor frissíti az adatkészlet-definícióban, egy új version_id rendeli hozzá a legújabb definíció.

```python
# get the Dataset from the workspace by name
dataset = workspace.datasets['dataset_name']

# get the latest Dataset definition
ds_def = dataset.get_definition()

# update the Dataset definition to select only the columns I'm interested in
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])

# with this update, the latest definition for dataset now has `version_id = 2`
dataset = dataset.update_definition(ds_def, 'select useful column')

dataset = workspace.datasets['dataset_name']
dataset.head(5)
```
<div>
<style scoped> .dataframe pedig tbody tr th: csak a-típusú {függőleges igazítás: középre;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>azonosító</th>
      <th>Letartóztatás</th>
      <th>Szélesség</th>
      <th>Hosszúság</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>FALSE</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>FALSE</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

A definíciófrissítés nem törli az előző adatkészlet-definíciókban. Továbbra is elérheti, és az előző definíciók felhasználását.

```python
# specify `version_id` to get a previous definition 
ds_def_old = dataset.get_definition(version_id = 1)
ds_def_old.head(5)
```
<div>
<style scoped> .dataframe pedig tbody tr th: csak a-típusú {függőleges igazítás: középre;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>azonosító</th>
      <th>Eset száma</th>
      <th>Dátum</th>
      <th>Letiltás</th>
      <th>IUCR</th>
      <th>Elsődleges típusa</th>
      <th>Leírás</th>
      <th>Leírás helye</th>
      <th>Letartóztatás</th>
      <th>Hazai</th>
      <th>...</th>
      <th>Ward</th>
      <th>Közösségi terület</th>
      <th>Az FBI kód</th>
      <th>X koordinátáját</th>
      <th>Y koordinátája</th>
      <th>Év</th>
      <th>Frissítés dátuma</th>
      <th>Szélesség</th>
      <th>Hosszúság</th>
      <th>Hely</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>HZ239907</td>
      <td>4/4/2016 23:56</td>
      <td>007XX E 111TH ST</td>
      <td>1153</td>
      <td>MEGTÉVESZTŐ ELJÁRÁS</td>
      <td>PÉNZÜGYI ADATOKKAL VALÓ VISSZAÉLÉS KERESZTÜL 300 USD</td>
      <td>EGYÉB</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>50</td>
      <td>11</td>
      <td>1183356</td>
      <td>1831503</td>
      <td>2016</td>
      <td>5/11/2016 15:48</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
      <td>(41.692833841, -87.60431945)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>HZ258664</td>
      <td>4/15/2016 17:00</td>
      <td>082XX S MARSHFIELD ELENTÉS MENTÉSE</td>
      <td>890</td>
      <td>LOPÁS</td>
      <td>FROM BUILDING</td>
      <td>RESIDENCE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>21</td>
      <td>71</td>
      <td>6</td>
      <td>1166776</td>
      <td>1850053</td>
      <td>2016</td>
      <td>5/12/2016 15:48</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
      <td>(41.744106973, -87.664494285)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>HZ261252</td>
      <td>4/15/2016 10:00</td>
      <td>104XX S SACRAMENTÓI ELENTÉS MENTÉSE</td>
      <td>1154</td>
      <td>MEGTÉVESZTŐ ELJÁRÁS</td>
      <td>PÉNZÜGYI ADATOKKAL VALÓ VISSZAÉLÉS 300 USD MAJD A</td>
      <td>RESIDENCE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>19</td>
      <td>74</td>
      <td>11</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/12/2016 15:50</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>HZ261534</td>
      <td>4/15/2016 9:00</td>
      <td>113XX S VALÓ MENTÉSE</td>
      <td>1120</td>
      <td>MEGTÉVESZTŐ ELJÁRÁS</td>
      <td>FORGERY</td>
      <td>RESIDENCE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>49</td>
      <td>10</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/13/2016 15:51</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>HZ277630</td>
      <td>4/15/2016 10:00</td>
      <td>055XX N KEDZIE ELENTÉS MENTÉSE</td>
      <td>890</td>
      <td>LOPÁS</td>
      <td>FROM BUILDING</td>
      <td>SCHOOL, PUBLIC, BUILDING</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>40</td>
      <td>13</td>
      <td>6</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/25/2016 15:59</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Ahogy láthatjuk, az eredmény, az adatkészlet-definícióban első verziója továbbra is biztosítja, hogy az összes oszlop függetlenül újabb frissítés bűnözés adatokból. Például ha rendelkezik egy gépi tanulási modell jelenleg használ az adatkészlet első verziója, és használatával `Date` oszlop egyik funkciója, az adatkészletből, akkor nem tapasztalnak csak tartjaalegújabbdefiníciófrissítésénekköszönhetőenbármilyenhiba`ID`, `Arrest`, `Latitude`, `Longitude` oszlopok bűnözés adataiból.

## <a name="how-to-access-dataset-definitions"></a>Az adatkészlet-definíciók elérése

Az összes definíciók listájának lekéréséhez használja `get_definitions()`. A definíció egy adott verzióját használja `get_definition()`. A következő példa bemutatja az összes definíciók listájának beolvasása, és ezután beolvasása az 1. verzió:

```python
# list all definitions for the dataset
dataset.get_definitions()
# get version ID 1
dataset.get_definition(version_id=1)
```

A kimenet a `get_definitions()` az alábbi példához hasonló:

```text
{'2': VersionID: 2, State: active, Created: 2019-04-19 16:43:52.439890+00:00, Modified: 2019-04-19 16:43:52.439890+00:00, Notes: select useful column,
 '1': VersionID: 1, State: active, Created: 2019-04-19 16:39:14.112046+00:00, Modified: 2019-04-19 16:44:12.912663+00:00, Notes: None}
```

A lekérdezés definícióját, ha a gépi tanulási modellek vagy a machine learning folyamat használhatja azt.

## <a name="manage-lifecycle-of-dataset-definitions"></a>Az adatkészlet-definíciók életciklusának kezelése

Mindegyik adatkészlet-definícióban életciklusának függetlenül is felügyelhetők. Az életciklus három szakaszból áll: aktív, elavult, vagy archivált.

### <a name="active"></a>Aktív

Létrejön egy új adatkészlet-definícióban, esetén alapértelmezés szerint aktív. 

### <a name="deprecate"></a>Kivezetjük

Az adatkészlet-definíciók is elavulttá válik, amikor a használati már nem ajánlott, és a egy helyettesítő érhető el. Elavult adatkészlet-definícióban a machine learning-folyamatokat használata esetén a figyelmeztető üzenet beolvasása vissza, de a végrehajtás nem lesznek letiltva.

Amikor egy adatkészlet-definícióban elavulttá, adja meg az adatkészlet Azonosítóját és az adatkészlet definíciós verzió Azonosítóját a csere definíciójában. Ezeket az információkat tárolja, és figyelmeztető üzenetet használt fel egy elavult adatkészlet-definícióban meg.

```python
# get the definition that you want to deprecate
ds_def = dataset.get_definition(version_id = 1)

# deprecate it by providing the information for the replacement definition, which is recommended to be used in machine learning scenarios
ds_def.deprecate(deprecate_by_dataset_id=dataset.id, deprecated_by_definition_version=2)
```

### <a name="archive"></a>Archívum

Az adatkészlet-definíciók archiválhatók, ha a definíciók nem kellene használni (például a mögöttes adatok már nem érhető el) bármilyen okból. Archivált adatkészlet-definícióban machine learning-folyamatokat használja, ha végrehajtási hiba miatt blokkolva lesz.

```python
# archive the definition with `version_id = 1`
ds_def = dataset.get_definition(version_id = 1)
ds_def.archive()
```

### <a name="reactivate"></a>Újraaktiválás

Könnyedén újraaktiválhatja bármely elavult vagy archív adatkészlet-definícióban.

```python
# reactivate Dataset definition with `version_id =1` which was archived in the previous step
ds_def = dataset.get_definition(version_id = 1)
ds_def.reactivate()
```

## <a name="next-steps"></a>További lépések

Adatkészletek való használatáról további információkért lásd: [létrehozása és regisztrálása az Azure Machine Learning adatkészletek](how-to-create-register-datasets.md).

Példa adatkészleteket használó, tekintse meg a [notebookok minta](https://aka.ms/dataset-tutorial).
