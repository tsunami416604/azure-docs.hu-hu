---
title: Adattárolók/Blobok hozzáférése a betanításhoz
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja az adattárakat a blob-adattároláshoz a Azure Machine Learning szolgáltatással való betanítás során
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 97a4bc20394553b97211763cedaa76c3711306f2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319329"
---
# <a name="access-data-from-your-datastores"></a>Adatok elérése az adattárból

 Azure Machine Learning szolgáltatásban az adattárolók a forráskód módosítása nélkül férnek hozzá a tárterülethez. Azt határozza meg, hogy a betanítási kódot paraméterként kell-e megadnia, vagy közvetlenül egy kalkulátorhoz adja meg az adattárat, Azure Machine Learning munkafolyamatok biztosítják, hogy az adattár helyei elérhetők legyenek, és elérhetővé válnak a számítási környezet számára.

Ez az útmutató példákat mutat be a következő feladatokra:
* [Adattár kiválasztása](#access)
* [Adatok lekérése](#get)
* [Adatok feltöltése és letöltése adattárba](#up-and-down)
* [Az adattár elérése a betanítás során](#train)

## <a name="prerequisites"></a>Előfeltételek

Adattárolók használatához szüksége lesz egy [munkaterület](concept-workspace.md).

Első lépésként vagy [új munkaterület létrehozása](setup-create-workspace.md#sdk) vagy egy meglévő beolvasása:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Adattár kiválasztása

Használhatja az alapértelmezett adattárat, vagy sajátot is használhat.

### <a name="use-the-default-datastore-in-your-workspace"></a>Az alapértelmezett adattár használata a munkaterületen

 Minden munkaterülethez tartozik egy regisztrált, alapértelmezett adattár, amelyet azonnal használhat.

A munkaterület alapértelmezett adattárolója lekérése:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Saját adattár regisztrálása a munkaterületen

Ha rendelkezik meglévő Azure Storage, regisztrálhatja adattárolót, a munkaterületen. 

<a name="store"></a>

####  <a name="storage-guidance"></a>Storage – útmutató

A blob Storage és a blob-adattárolók használatát javasoljuk. Mind a standard, mind a Premium Storage elérhető a Blobok számára. Bár drágábbak, javasoljuk, hogy a prémium szintű tárterületet gyorsabb átviteli sebesség okozza, amely javíthatja a képzések futásának sebességét, különösen akkor, ha egy nagyméretű adathalmazon végez betanítást. Tekintse meg az [Azure díjszabási számológépét](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) a Storage-fiókok költségével kapcsolatos információkért.

>[!NOTE]
> Azure Machine Learning a szolgáltatás más típusú adattárolókat is támogat, amelyek bizonyos esetekben hasznosak lehetnek. Ha például egy adatbázisban tárolt adataival kell betanítania, használhatja a AzureSQLDatabaseDatastore vagy a AzurePostgreSqlDatastore. Tekintse meg [ezt a táblázatot](#matrix) az elérhető adattár-típusokhoz.

#### <a name="register-your-datastore"></a>Az adattár regisztrálása
Az összes regisztrációs metódus az [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) osztályban található, és a következő formát register_azure_ *.

Az alábbi példák bemutatják, hogyan regisztrálhat egy Azure BLOB-tárolót vagy egy Azure-fájlmegosztást adattárként.

+ **Azure Blob Container adattár**esetén használja a következőt:[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ **Azure fájlmegosztás-adattár**esetén használja [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)a következőt:. Példa: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Adattárolók meghatározása &

Az aktuális munkaterületen regisztrált megadott adattár beszerzéséhez használja [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) a következőt:

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Az adott munkaterületen található összes adattár listájának lekéréséhez használja a következő kódot:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Az aktuális munkaterület eltérő alapértelmezett adattárának definiálásához használja [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-)a következőt:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Adatok feltöltése & letöltéséhez
Az [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) alábbi [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) példákban leírt és metódusok a [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) és a [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) osztályokra vonatkozó, és azokkal azonos módon működnek.

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
## <a name="access-datastores-during-training"></a>Adattárolók elérése a betanítás során

Miután elérhetővé tette az adattárat a betanítási számítási célra, elérheti azt a betanítási futtatás során (például képzés vagy érvényesítési adatok), ha egyszerűen átadja az elérési utat a betanítási szkript paraméterének.

A következő táblázat felsorolja azokat [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) a metódusokat, amelyekkel a számítási cél a Futtatás során az adattár használatát ismerteti.

Módon|Módszer|Leírás|
----|-----|--------
Csatlakoztatás| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| A használatával csatlakoztathatja az adattárt a számítási célhoz.
Letöltés|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Ezzel a paranccsal töltheti le az adattár tartalmát a által `path_on_compute`megadott helyre. <br> A betanítási környezethez ez a letöltés a Futtatás előtt történik.
Feltöltés|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Ezzel a paranccsal tölthet fel egy fájlt az adattárában `path_on_compute` megadott helyről. <br> A betanítási környezethez ez a feltöltés a Futtatás után történik.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Ha egy adott mappára vagy fájlra szeretne hivatkozni az adattárában, és elérhetővé teszi azt a számítási célra, használja [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) az adattár függvényét.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Bármely `ds` `"$AZUREML_DATAREFERENCE_XXXX"` vagy `ds.path` objektum feloldása egy környezeti változó neve, amelynek az értéke a cél számításának csatlakoztatási/letöltési útvonalát jelöli. Előfordulhat, hogy az adattár elérési útja nem egyezik meg a betanítási parancsfájl végrehajtási útjával.

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>Számítási és adattár-mátrix betanítása

A következő mátrix megjeleníti az elérhető adathozzáférési funkciókat a különböző képzési számítási célokhoz és adattárolási forgatókönyvekhez. További információ a [Azure Machine learning képzési számítási céljairól](how-to-set-up-training-targets.md#compute-targets-for-training).

|Compute|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Helyi:|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|–         |–                                                                         |
| Az Azure Machine Learning Compute |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml&nbsp;-folyamatok](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml&nbsp;-folyamatok](concept-ml-pipelines.md)|–         |–                                                                         |
| Virtual machines (Virtuális gépek)               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |–         |–                                                                         |
| HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |–         |–                                                                         |
| Adatátvitel                  |[ML&nbsp;-folyamatok](concept-ml-pipelines.md)                                               |–                                           |[ML&nbsp;-folyamatok](concept-ml-pipelines.md)            |[ML&nbsp;-folyamatok](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;-folyamatok](concept-ml-pipelines.md)                                              |–                                           |[ML&nbsp;-folyamatok](concept-ml-pipelines.md)             |–                                                                         |
| Azure Batch                    |[ML&nbsp;-folyamatok](concept-ml-pipelines.md)                                               |–                                           |N/A         |–                                                                         |
| Azure DataLake Analytics       |–                                           |–                                           |[ML&nbsp;-folyamatok](concept-ml-pipelines.md)             |–                                                                         |

> [!NOTE]
> Előfordulhatnak olyan forgatókönyvek, amelyekben a nagy mértékben ismétlődő, nagyméretű adatfolyamatok `as_download()` gyorsabban futnak `as_mount()`a helyett; ez a kísérlettel ellenőrizhető.

### <a name="examples"></a>Példák 

Az alábbi példák a [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályra vonatkoznak, amely az adattár elérését mutatja be a betanítás során.

Ez a kód egy kalkulátort hoz létre a betanítási parancsfájl `train.py`használatával, a jelzett forrás könyvtárból a ben `script_params`definiált paraméterek használatával, a meghatározott képzési számítási célra.

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

Átadhatja az adattárolók listáját is a kalkulátor konstruktor `inputs` paramétereként az adattár (ok) ra való csatlakoztatáshoz vagy másoláshoz. Példa erre a kódra:
* A betanítási parancsfájl `train.py` futtatása `ds1` előtt letölti az adattár összes tartalmát a számítási célhoz
* A Futtatás előtt `'./foo'` `ds2` letöltiamappátazadattárból`train.py` a számítási célhoz
* A parancsfájl futtatása után `'./bar.pkl'` feltölti a fájlt a számítási célra az adattárba `ds3`

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="access-datastores-during-for-scoring"></a>Adattárolók elérése a pontozás során

A Azure Machine Learning szolgáltatás számos lehetőséget kínál a modellek pontozásra való használatára. A módszerek némelyike nem biztosít hozzáférést az adattárolóhoz. A következő táblázat segítségével megtudhatja, hogy mely módszerek lehetővé teszik az adattárolók elérését a pontozás során:

| Módszer | Adattár-hozzáférés | Leírás |
| ----- | :-----: | ----- |
| [Kötegelt előrejelzés](how-to-run-batch-predictions.md) | ✔ | Előrejelzések készítése aszinkron módon nagy mennyiségű adattal. |
| [Webszolgáltatás](how-to-deploy-and-where.md) | &nbsp; | Modell (ek) üzembe helyezése webszolgáltatásként. |
| [IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Modell (ek) üzembe helyezése IoT Edge eszközökön. |

Olyan helyzetekben, amikor az SDK nem biztosít hozzáférést az adattárolóhoz, létrehozhat egyéni kódokat a megfelelő Azure SDK-val az adateléréshez. Például a Pythonhoz készült [Azure Storage SDK](https://github.com/Azure/azure-storage-python) használatával férhet hozzá a blobokban tárolt adataihoz.

## <a name="next-steps"></a>További lépések

* [A modell tanítása](how-to-train-ml-models.md)

* [Modell üzembe helyezése](how-to-deploy-and-where.md)
