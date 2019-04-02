---
title: Adattárolók az adatok elérése / blobok képzéshez
titleSuffix: Azure Machine Learning service
description: Adattárolók használata közben való hozzáféréséhez adatok blobtárolóban képzés az Azure Machine Learning szolgáltatással
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: 85910e2f422ea45b2468f20b4ff9425f64ca3cbe
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793413"
---
# <a name="access-data-from-your-datastores"></a>Az adattárolók érheti el adatait

 Az Azure Machine Learning szolgáltatásban adattárainak a Forráskód módosítása nélkül is számítási helyfüggetlen mechanizmusok tároló elérése érdekében. E képzési paraméterként egy útvonalat kód írása, vagy egy estimator közvetlenül az adattárolót biztosítanak, az Azure Machine Learning-munkafolyamatok győződjön meg arról, az adattár helyeken érhetők el, és elérhetővé téve a számítási környezet.

Ebben az útmutatóban példát a következő feladatok közül:
* [Egy adattároló kiválasztása](#access)
* [Adatok lekérése](#get)
* [Töltse fel és adattárainak adatok letöltése](#up-and-down)
* [Hozzáférés adattárolója betanítás során](#train)

## <a name="prerequisites"></a>Előfeltételek

Adattárolók használatához szüksége lesz egy [munkaterület](concept-azure-machine-learning-architecture.md#workspace).

Első lépésként vagy [új munkaterület létrehozása](setup-create-workspace.md#sdk) vagy egy meglévő beolvasása:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Egy adattároló kiválasztása

Használja az alapértelmezett adattárhoz, vagy használhat sajátot.

### <a name="use-the-default-datastore-in-your-workspace"></a>Az alapértelmezett adattár használata a munkaterületen

 Az egyes munkaterületeken azonnal használható regisztrált, alapértelmezett adattárolót tartalmaz.

A munkaterület alapértelmezett adattárolója lekérése:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>A saját adattárolója regisztrálja a munkaterület

Ha rendelkezik meglévő Azure Storage, regisztrálhatja adattárolót, a munkaterületen.   A rendszer a register-módszerek a [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) osztályt, és rendelkezik az űrlap register_azure_ *. 

Az alábbi példák bemutatják, hogy egy Azure Blob-tároló vagy egy Azure-fájlmegosztás regisztrálásához adattárolót.

+ Az egy **Azure Blob tároló adattár**, használata [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Az egy **Azure fájl megosztási adattárolója**, használjon [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Példa: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           container_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>& Definiálása adattárolók keresése

Az aktuális munkaterületen található regisztrált megadott adattárolót használja [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Egy adott munkaterület minden adattárainak listájának lekéréséhez használja ezt a kódot:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Az aktuális munkaterülethez tartozó más alapértelmezett adattárolót meghatározásához használja [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Töltse fel & adatok letöltése
A [ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) és [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) a következő példákban leírt módszerek jellemző, és az azonos módon működik a [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) és [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) osztályokat.

### <a name="upload"></a>Feltöltés

 Töltse fel az adattárhoz, a Python SDK-val vagy a könyvtár, vagy az egyes fájlok.

Egy könyvtár tölthet fel egy adattár `ds`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```

`target_path` a helyét adja meg a feltöltendő fájlmegosztást (vagy blob-tároló). A rendszer alapértelmezés szerint `None`, ebben az esetben az adatfeltöltés lekérdezi a legfelső szintű. `overwrite=True` felülírja a meglévő adatokat `target_path`.

Vagy töltse fel az egyes fájlok listáját az adattárhoz az adattár keresztül `upload_files()` metódust.

### <a name="download"></a>Letöltés
Ehhez hasonlóan adatokat letölteni a adattárolót a helyi fájlrendszerbe.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path` az a hely, az adatok letöltése a helyi könyvtárban. Adja meg a mappa elérési útját a letöltéséhez fájlmegosztást (vagy blob-tárolóba), adja meg, hogy az elérési út `prefix`. Ha `prefix` van `None`, a fájlmegosztást (vagy a blob-tároló) teljes tartalmát fogja letöltése.

<a name="train"></a>
## <a name="access-datastores-during-training"></a>Hozzáférés adattárainak betanítás során

Miután elérhetővé tette az adattár a számítási célnak, hozzá tud férni a betanítási futtatás (például a képzés és az érvényesítési adatok) során egyszerűen adja át az elérési út azt a tanítási szkriptet paraméterként.

A következő táblázat felsorolja a [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) módszereket, amelyek a számítási célnak az adattár használata futtatások során.

Módja|Módszer|Leírás|
----|-----|--------
Csatlakoztatás| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Az adattár csatlakoztatása a számítási cél használatával.
Letöltés|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Töltse le az adattár tartalmának a megadott helyen `path_on_compute`. <br> A betanítási Futtatás a környezetben a letöltés a Futtatás előtt történik.
Feltöltés|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Tölthet fel egy fájlt a megadott helyen `path_on_compute` , az adattárhoz. <br> Betanítási Futtatás környezetre a feltöltési történik, a Futtatás után.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Hivatkozás egy adott mappa vagy fájl az adattár a, és elérhetővé teheti a számítási célnak, használja az adattárhoz [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) függvény.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Bármely `ds` vagy `ds.path` objektumra mutat egy környezeti változó neve, a formátum `"$AZUREML_DATAREFERENCE_XXXX"` amelynek az értéke az a cél számítási csatlakoztatási/letöltési útja jelöli. Az adattároló-elérési útját a céloldali számítási nem lehet ugyanaz, mint a betanítási szkript végrehajtási útvonalát.

### <a name="compute-context-and-datastore-type-matrix"></a>Számítási környezet és az adattároló típusa mátrix

A következő mátrix jeleníti meg a rendelkezésre álló adatok hozzáférés funkciói a különböző számítási környezetet és az adattárhoz forgatókönyvekhez. Ez a mátrix "folyamat" kifejezés adattárainak használja bemenetként, vagy a kimeneti [Azure Machine Learning-folyamatokat](https://docs.microsoft.com/azure/machine-learning/service/concept-ml-pipelines).

||Helyi számítási|Az Azure Machine Learning Compute|Adatátvitel|Databricks|HDInsight|Azure Batch|Azure DataLake Analytics|Virtuális gépek|
-|--|-----------|----------|---------|-----|--------------|---------|---------|
|AzureBlobDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] <br> Folyamat|Folyamat|Folyamat|[`as_download()`] <br> [`as_upload()`]|Folyamat||[`as_download()`] <br> [`as_upload()`]|
|AzureFileDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] Folyamat |||[`as_download()`] [`as_upload()`]|||[`as_download()`] [`as_upload()`]|
|AzureDataLakeDatastore|||Folyamat|Folyamat|||Folyamat||
|AzureDataLakeGen2Datastore|||Folyamat||||||
|AzureDataPostgresSqlDatastore|||Folyamat||||||
|AzureSqlDatabaseDataDatastore|||Folyamat||||||


> [!NOTE]
> Előfordulhat, hogy forgatókönyvek, amelyben magas iteratív, nagy mennyiségű adat folyamatok futtatása gyorsabb használatával [`as_download()`] helyett [`as_mount()`]; Ez kísérlettel érvényesíthetők.

### <a name="examples"></a>Példák 

A következő hitelesítésikód-példák jellemzőek a [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztály betanítás során az adattár eléréséhez.

Ez a kód létrehoz egy, a képzési szkripttel estimator `train.py`, a megadott forráskönyvtár a definiált paraméterek használatával a `script_params`, mind a megadott számítási célon.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Is átadhat adattárainak listáján a Estimator konstruktor `inputs` csatlakoztatni vagy és- tárolókról a datastore(s) másolása paramétert. Ez a Kódpélda:
* Letölti az adattár összes tartalom `ds1` a számítási célnak, mielőtt a tanítási szkriptet `train.py` futtatása
* Letölti a mappa `'./foo'` az adattárhoz `ds2` előtt a számítási célnak `train.py` futtatása
* Feltölti a fájlt `'./bar.pkl'` a számítási célnak, akár az adattárhoz való `ds3` a szkript futtatása után

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>További lépések

* [A modell tanítása](how-to-train-ml-models.md)

* [Modell üzembe helyezése](how-to-deploy-and-where.md)
