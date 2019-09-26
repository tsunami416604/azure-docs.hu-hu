---
title: Az Azure Storage-szolgáltatásokban tárolt adathozzáférés
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az adattárakat az Azure Storage-szolgáltatások eléréséhez Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: 9de3232bcd7908f775dadff4dc584f2a687b0c68
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299760"
---
# <a name="access-data-in-azure-storage-services"></a>Az Azure Storage-szolgáltatásokban tárolt adathozzáférés

Ebből a cikkből megtudhatja, hogyan érheti el könnyen az Azure Storage-szolgáltatásokban tárolt adatait Azure Machine Learning adattáron keresztül. Az adattárolók a kapcsolódási adatok tárolására szolgálnak, például az előfizetés-AZONOSÍTÓval és a jogkivonat-hitelesítéssel. Az adattárolók használata lehetővé teszi a tárhely elérését anélkül, hogy merevlemez-kapcsolati adatokat kellene létrehoznia a parancsfájlokban.

Ez az útmutató példákat mutat be a következő feladatokra:
* [Adattárolók regisztrálása](#access)
* [Adattárolók beolvasása a munkaterületről](#get)
* [Adatok feltöltése és letöltése adattár használatával](#up-and-down)
* [Az adathozzáférés a betanítás során](#train)

## <a name="prerequisites"></a>Előfeltételek

Adattárolók használatához szüksége lesz egy [munkaterület](concept-workspace.md).

Első lépésként vagy [új munkaterület létrehozása](how-to-manage-workspace.md) vagy egy meglévő beolvasása:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>Adattárolók regisztrálása

Az összes regisztrációs metódus az [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) osztályban található, és a következő formát register_azure_ *.

Az alábbi példák bemutatják, hogyan regisztrálhat egy Azure BLOB-tárolót vagy egy Azure-fájlmegosztást adattárként.

+ **Azure Blob Container adattár**esetén használja a következőt:[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ **Azure fájlmegosztás-adattár**esetén használja [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)a következőt:. Példa: 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>Storage – útmutató

Az Azure Blob Container használatát javasoljuk. Mind a standard, mind a Premium Storage elérhető a Blobok számára. Bár drágábbak, javasoljuk, hogy a prémium szintű tárterületet gyorsabb átviteli sebesség okozza, amely javíthatja a képzések futásának sebességét, különösen akkor, ha egy nagyméretű adathalmazon végez betanítást. Tekintse meg az [Azure díjszabási számológépét](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) a Storage-fiókok költségével kapcsolatos információkért.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Adattárolók beolvasása a munkaterületről

Az aktuális munkaterületen regisztrált adott adattár beszerzéséhez használja a [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) statikus metódust az adattár osztályon:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Egy adott munkaterülethez regisztrált adattárolók listájának lekéréséhez használhatja a [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) (z) tulajdonságot egy munkaterület-objektumon:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Munkaterületek létrehozásakor egy Azure Blob-tároló és egy Azure-fájlmegosztás regisztrálva van a (z `workspaceblobstore` ) `workspacefilestore` és a (z) nevű munkaterületen. A blob-tároló és a munkaterülethez csatolt Storage-fiókban kiépített fájlmegosztás kapcsolódási adatait tárolják. Az `workspaceblobstore` alapértelmezett adattárként van beállítva.

A munkaterület alapértelmezett adattárolója lekérése:

```Python
datastore = ws.get_default_datastore()
```

Ha másik alapértelmezett adattárat szeretne megadni az aktuális munkaterülethez, használja [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) a metódust a munkaterület objektumon:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Adatok feltöltése & letöltéséhez
Az [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) alábbi [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) példákban leírt és metódusok a [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) és a [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) osztályokra vonatkozó, és azokkal azonos módon működnek.

### <a name="upload"></a>Feltöltés

 Töltse fel az adattárhoz, a Python SDK-val vagy a könyvtár, vagy az egyes fájlok.

Egy könyvtár tölthet fel egy adattár `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

A `target_path` paraméter adja meg a feltöltendő fájlmegosztás (vagy blob-tároló) helyét. A rendszer alapértelmezés szerint `None`, ebben az esetben az adatfeltöltés lekérdezi a legfelső szintű. Ha `overwrite=True` a `target_path` rendszer felülírja a meglévőket.

Vagy feltöltheti az egyes fájlok listáját az adattárba az `upload_files()` metódus használatával.

### <a name="download"></a>Letöltés

Ehhez hasonlóan adatokat letölteni a adattárolót a helyi fájlrendszerbe.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

A `target_path` paraméter annak a helyi könyvtárnak a helye, ahová le szeretné tölteni az adatfájlt. Adja meg a mappa elérési útját a letöltéséhez fájlmegosztást (vagy blob-tárolóba), adja meg, hogy az elérési út `prefix`. Ha `prefix` van `None`, a fájlmegosztást (vagy a blob-tároló) teljes tartalmát fogja letöltése.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Az adatai elérése a betanítás során

> [!IMPORTANT]
> A [Azure Machine learning adatkészletek (előzetes verzió)](how-to-create-register-datasets.md) használata az új ajánlott módszer az adathoz való hozzáféréshez a képzésben. Az adatkészletek olyan függvényeket biztosítanak, amelyek a táblázatos adatokat a pandák vagy a Spark DataFrame töltik be, valamint lehetővé teszik bármilyen formátumú fájlok letöltését vagy csatlakoztatását az Azure Blobból, az Azure file-ból, a Azure Data Lake 1., Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL További információ az [adatkészletek betanításáról](how-to-train-with-datasets.md).

A következő táblázat felsorolja azokat a módszereket, amelyekkel a számítási cél az adattárolók futtatás közbeni használatának módját ismerteti. 

Módon|Módszer|Leírás|
----|-----|--------
Csatlakoztatás| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| A használatával csatlakoztathatja az adattárt a számítási célhoz.
Letöltés|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Ezzel a paranccsal töltheti le az adattár tartalmát a által `path_on_compute`megadott helyre. <br><br> Ez a letöltés a Futtatás előtt történik.
Feltöltés|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Ezzel a paranccsal tölthet fel egy fájlt az adattárában `path_on_compute` megadott helyről. <br><br> Ez a feltöltés a Futtatás után következik be.

Az adattár [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) metódusával hivatkozhat egy adott mappára vagy fájlra az adattárban, és elérhetővé teheti azt a számítási célra.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Bármely megadott `datastore` vagy `datastore.path` objektum feloldása egy környezeti `"$AZUREML_DATAREFERENCE_XXXX"`változó neve, amelynek értéke a cél számításhoz tartozó csatlakoztatási/letöltési útvonal. Előfordulhat, hogy az adattár elérési útja nem egyezik meg a betanítási parancsfájl végrehajtási útjával.

### <a name="examples"></a>Példák 

A következő példák [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) a osztályra vonatkoznak az adatok betanítás közbeni eléréséhez. 

`script_params`a egy olyan szótár, amely paramétereket tartalmaz a entry_script. Ezzel a művelettel továbbíthatja az adattárolót, és leírhatja, hogyan történik az adatok elérhetővé tétele a számítási célra. További információt a teljes körű [oktatóanyagban](tutorial-train-models-with-aml.md)olvashat.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Átadhatja az adattárolók listáját is a kalkulátor-konstruktor `inputs` paraméterbe az adattár (ok) adatainak csatlakoztatásához vagy másolásához. Példa erre a kódra:
* A betanítási parancsfájl `datastore1` `train.py` futtatása előtt letölti az összes tartalmat a számítási célhoz
* A Futtatás előtt `'./foo'` `datastore2` `train.py` letölti a mappát a számítási célhoz
* Feltölti a fájlt `'./bar.pkl'` a számítási célhoz `datastore3` a parancsfájl futtatása után.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```

### <a name="compute-and-datastore-matrix"></a>Számítási és adattár-mátrix

Az adattárolók jelenleg támogatják a kapcsolódási adatok tárolását a következő mátrixban felsorolt tárolási szolgáltatásokhoz. Ez a mátrix a különböző számítási célok és adattárolási forgatókönyvek elérhető adathozzáférési funkcióit jeleníti meg. További információ a [Azure Machine learning számítási céljairól](how-to-set-up-training-targets.md#compute-targets-for-training).

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

### <a name="accessing-source-code-during-training"></a>Forráskód elérése a betanítás során

Az Azure Blob Storage nagyobb átviteli sebességgel rendelkezik, mint az Azure-fájlmegosztás, és nagy számú, párhuzamosan elindított feladatra fog méretezni. Ezért javasoljuk, hogy a futtatások konfigurálásával blob Storage-t használjon a forráskód-fájlok átviteléhez.

A következő kódrészlet a futtatási konfigurációt adja meg, amely a blob-adatforgalomhoz használandó blob-adattár.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Az adathozzáférés a pontozás során

A Azure Machine Learning számos módszert kínál a modellek pontozási célra való használatára. A módszerek némelyike nem biztosít hozzáférést az adattárolóhoz. A következő táblázat segítségével megtudhatja, hogy mely módszerek lehetővé teszik az adattárolók elérését a pontozás során:

| Módszer | Adattár-hozzáférés | Leírás |
| ----- | :-----: | ----- |
| [Kötegelt előrejelzés](how-to-run-batch-predictions.md) | ✔ | Előrejelzések készítése aszinkron módon nagy mennyiségű adattal. |
| [Webszolgáltatás](how-to-deploy-and-where.md) | &nbsp; | Modell (ek) üzembe helyezése webszolgáltatásként. |
| [IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Modell (ek) üzembe helyezése IoT Edge eszközökön. |

Olyan helyzetekben, amikor az SDK nem biztosít hozzáférést az adattárolóhoz, létrehozhat egyéni kódokat a megfelelő Azure SDK-val az adateléréshez. A [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python) például egy ügyféloldali kódtár, amely a blobokban vagy fájlokban tárolt adateléréshez használható.


## <a name="next-steps"></a>További lépések

* [A modell tanítása](how-to-train-ml-models.md)

* [Modell üzembe helyezése](how-to-deploy-and-where.md)
