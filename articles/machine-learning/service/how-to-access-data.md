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
ms.openlocfilehash: 3576f7cc0297ff1e9b10373ccc27b09e1a0ae8ae
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436696"
---
# <a name="access-data-in-azure-storage-services"></a>Az Azure Storage-szolgáltatásokban tárolt adathozzáférés

Ebből a cikkből megtudhatja, hogyan érheti el könnyen az Azure Storage-szolgáltatásokban tárolt adatait Azure Machine Learning adattáron keresztül. Az adattárolók a kapcsolódási adatok tárolására szolgálnak, például az előfizetés-AZONOSÍTÓval és a jogkivonat-hitelesítéssel. Az adattárolók használata lehetővé teszi a tárhely elérését anélkül, hogy merevlemez-kapcsolati adatokat kellene létrehoznia a parancsfájlokban. Ezekből az [Azure Storage-megoldásokból is létrehozhat adattárakat](#matrix). A nem támogatott tárolási megoldások esetében az adatforgalomnak a gépi tanulási kísérletek során történő megtakarítása érdekében javasoljuk, hogy az adatait a támogatott Azure Storage-megoldásokba helyezze át. [Megtudhatja, hogyan helyezheti át adatait](#move). 

Ez az útmutató példákat mutat be a következő feladatokra:
* [Adattárolók regisztrálása](#access)
* [Adattárolók beolvasása a munkaterületről](#get)
* [Adatok feltöltése és letöltése adattár használatával](#up-and-down)
* [Az adathozzáférés a betanítás során](#train)
* [Az Azure-ba irányuló adatáthelyezés](#move)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

- Azure Storage-fiók [Azure Blob-tárolóval](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) vagy [Azure-fájlmegosztás](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), vagy a munkaterület kezdőlapjának [(előzetes verzió)](https://ml.azure.com/)elérésére.

- Egy Azure Machine Learning-munkaterület. 
    - [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md) , vagy használjon egy meglévőt a Python SDK használatával.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Adattárolók létrehozása és regisztrálása

Ha az Azure Storage-megoldást adattárként regisztrálja, automatikusan létrehozza az adattárat egy adott munkaterületen. Adattárolókat a Python SDK vagy a munkaterület kezdőlapján hozhat létre és regisztrálhat a munkaterületre.

### <a name="using-the-python-sdk"></a>A Python SDK használata

Az összes regisztrálási módszer a [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) osztályban található, és a következő formát register_azure_ *.

A Register () metódus feltöltéséhez szükséges információk a [Azure Portalon](https://ms.portal.azure.com)keresztül találhatók. Válassza ki a **Storage-fiókok** elemet a bal oldali ablaktáblán, és válassza ki a regisztrálni kívánt Storage-fiókot. Az **áttekintő** oldal olyan információkat tartalmaz, mint például a fiók neve, a tároló vagy a fájlmegosztás neve. A hitelesítési adatokhoz (például a fiók kulcsa vagy az SAS-tokenhez) a bal oldali **Beállítások** ablaktáblában navigáljon a **fiók kulcsaihoz** . 

Az alábbi példák bemutatják, hogyan regisztrálhat egy Azure BLOB-tárolót vagy egy Azure-fájlmegosztást adattárként.

+ **Azure Blob Container adattár**esetén használja a következőt: [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

    A következő kód létrehozza és regisztrálja az adattárat (@no__t – 0) a munkaterületre, `ws`. Ez az adattár fér hozzá az Azure Blob-tárolóhoz (@no__t – 0) az Azure Storage-fiókban, `my_storage_account` a megadott fiók kulcsa alapján.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```

+ **Azure fájlmegosztás-adattár**esetén használja a következőt: [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

    A következő kód létrehozza és regisztrálja az adattárat (@no__t – 0) a munkaterületre, `ws`. Ez az adattár fér hozzá az Azure-fájlmegosztás @no__t – 0, az Azure Storage-fiókhoz, `my_storage_account` a megadott fiók kulcsa alapján.

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>Storage – útmutató

Az Azure Blob Container használatát javasoljuk. Mind a standard, mind a Premium Storage elérhető a Blobok számára. Bár drágábbak, javasoljuk, hogy a prémium szintű tárterületet gyorsabb átviteli sebesség okozza, amely javíthatja a képzések futásának sebességét, különösen akkor, ha egy nagyméretű adathalmazon végez betanítást. Tekintse meg az [Azure díjszabási számológépét](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) a Storage-fiókok költségével kapcsolatos információkért.

### <a name="using-the-workspace-landing-page"></a>A munkaterület kezdőlapjának használata 

Hozzon létre egy új adattárt néhány lépésben a munkaterület kezdőlapján.

1. Jelentkezzen be a [munkaterület](https://ml.azure.com/)kezdőlapján.
1. A **kezelés**alatt kattintson a bal oldali ablaktábla adattárolók **elemére** .
1. Válassza az **+ új adattár**lehetőséget.
1. Fejezze be az új adattár űrlapot. Az űrlap intelligens frissítése az Azure Storage-típus és a hitelesítés típusa beállítás alapján történik.
  
Az űrlap feltöltéséhez szükséges információk a [Azure Portalon](https://ms.portal.azure.com)keresztül találhatók. Válassza ki a **Storage-fiókok** elemet a bal oldali ablaktáblán, és válassza ki a regisztrálni kívánt Storage-fiókot. Az **áttekintő** oldal olyan információkat tartalmaz, mint például a fiók neve, a tároló vagy a fájlmegosztás neve. A hitelesítési elemek, például a fiók kulcsa vagy az SAS-token esetében a bal oldali **Beállítások** ablaktáblában navigáljon a **fiók kulcsaihoz** .

Az alábbi példa azt mutatja be, hogy az űrlap hogyan fog kinézni egy Azure Blob-adattár létrehozásához. 
    
 ![Új adattár](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Adattárolók beolvasása a munkaterületről

Az aktuális munkaterületen regisztrált adott adattár beszerzéséhez használja az [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) statikus metódust az adattár osztályban:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Egy adott munkaterülethez regisztrált adattárolók listájának lekéréséhez használhatja a [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) tulajdonságot egy munkaterület-objektumon:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Amikor létrehoz egy munkaterületet, egy Azure BLOB-tárolót és egy Azure-fájlmegosztást regisztrál a `workspaceblobstore` nevű munkaterületre és `workspacefilestore`-re. A blob-tároló és a munkaterülethez csatolt Storage-fiókban kiépített fájlmegosztás kapcsolódási adatait tárolják. A `workspaceblobstore` alapértelmezett adattárként van beállítva.

A munkaterület alapértelmezett adattárának beolvasása:

```Python
datastore = ws.get_default_datastore()
```

Ha másik alapértelmezett adattárat szeretne megadni az aktuális munkaterülethez, használja a [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) metódust a munkaterület objektumon:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Adatok feltöltése & letöltéséhez
Az alábbi példákban ismertetett [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) és [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metódusok a [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) és a [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) osztályokra vonatkozó, és azokkal azonos módon működnek.

### <a name="upload"></a>Feltöltés

 Töltsön fel egy könyvtárat vagy egyedi fájlt az adattárba a Python SDK használatával.

Könyvtár feltöltése adattárba `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

A `target_path` paraméter adja meg a feltöltendő fájlmegosztás (vagy blob-tároló) helyét. Alapértelmezett értéke @no__t – 0, amely esetben az adatfeltöltés a gyökérbe történik. Ha @no__t – 0 `target_path` meglévő adatértékét felülírja a rendszer.

Vagy töltse fel az egyes fájlok listáját az adattárba a `upload_files()` metódus használatával.

### <a name="download"></a>Letöltés

Hasonlóképpen töltse le az adatok egy adattárból a helyi fájlrendszerbe.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

A `target_path` paraméter annak a helyi könyvtárnak a helye, ahová le szeretné tölteni az adatfájlt. A letölteni kívánt fájlmegosztás (vagy blob-tároló) mappájának elérési útjának megadásához adja meg a `prefix` elérési utat. Ha `prefix` `None`, a fájlmegosztás (vagy blob-tároló) teljes tartalma le lesz töltve.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Az adatai elérése a betanítás során

> [!IMPORTANT]
> A [Azure Machine learning adatkészletek (előzetes verzió)](how-to-create-register-datasets.md) használata az új ajánlott módszer az adathoz való hozzáféréshez a képzésben. Az adatkészletek olyan függvényeket biztosítanak, amelyek a táblázatos adatokat a pandák vagy a Spark DataFrame töltik be, valamint lehetővé teszik bármilyen formátumú fájlok letöltését vagy csatlakoztatását az Azure Blobból, az Azure file-ból, a Azure Data Lake 1., Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL További információ az [adatkészletek betanításáról](how-to-train-with-datasets.md).

A következő táblázat felsorolja azokat a módszereket, amelyekkel a számítási cél az adattárolók futtatás közbeni használatának módját ismerteti. 

Módon|Módszer|Leírás|
----|-----|--------
Csatlakoztatás| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| A használatával csatlakoztathatja az adattárt a számítási célhoz.
Letöltés|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|A használatával letöltheti az adattár tartalmát a `path_on_compute` által megadott helyre. <br><br> Ez a letöltés a Futtatás előtt történik.
Feltöltés|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Ezzel a paranccsal tölthet fel egy fájlt a `path_on_compute` által megadott helyről az adattárba. <br><br> Ez a feltöltés a Futtatás után következik be.

Ha egy adott mappát vagy fájlt szeretne hivatkozni az adattárában, és elérhetővé szeretné tenni a számítási célra, használja az adattár [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) metódusát.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Bármely megadott `datastore` vagy `datastore.path` objektum a (z) `"$AZUREML_DATAREFERENCE_XXXX"` formátum környezeti változójának egyikét oldja fel, amelynek értéke a cél számításhoz tartozó csatlakoztatási/letöltési útvonal. Előfordulhat, hogy az adattár elérési útja nem egyezik meg a betanítási parancsfájl végrehajtási útjával.

### <a name="examples"></a>Példák 

A következő példák a [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztályra vonatkoznak az adatok betanítás közbeni eléréséhez. 

a `script_params` egy olyan szótár, amely paramétereket tartalmaz a entry_script. Ezzel a művelettel továbbíthatja az adattárolót, és leírhatja, hogyan történik az adatok elérhetővé tétele a számítási célra. További információt a teljes körű [oktatóanyagban](tutorial-train-models-with-aml.md)olvashat.

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

Átadhatja az adattárolók listáját is a kalkulátor konstruktorának `inputs` paraméterrel az adattár (ok) ba történő csatlakoztatásához vagy másolásához. Példa erre a kódra:
* A `datastore1` összes tartalmának letöltése a számítási célhoz, mielőtt a betanítási parancsfájl `train.py` fut
* A `train.py` futtatása előtt letölti a `'./foo'` mappát `datastore2` értékről a számítási célhoz.
* A parancsfájl futtatása után feltölti a `'./bar.pkl'` fájlt a számítási célhelyről a `datastore3` értékre.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Számítási és adattár-mátrix

Az adattárolók jelenleg támogatják a kapcsolódási adatok tárolását a következő mátrixban felsorolt tárolási szolgáltatásokhoz. Ez a mátrix a különböző számítási célok és adattárolási forgatókönyvek elérhető adathozzáférési funkcióit jeleníti meg. További információ a [Azure Machine learning számítási céljairól](how-to-set-up-training-targets.md#compute-targets-for-training).

|Számítási szolgáltatások|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Helyi|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|–         |–                                                                         |
| Azure Machine Learning számítás |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml @ no__t-4pipelines](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml @ no__t-4pipelines](concept-ml-pipelines.md)|–         |–                                                                         |
| Virtual machines (Virtuális gépek)               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |–         |–                                                                         |
| HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |–         |–                                                                         |
| Adatátvitel                  |[ML @ no__t – 1pipelines](concept-ml-pipelines.md)                                               |–                                           |[ML @ no__t – 1pipelines](concept-ml-pipelines.md)            |[ML @ no__t – 1pipelines](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML @ no__t – 1pipelines](concept-ml-pipelines.md)                                              |–                                           |[ML @ no__t – 1pipelines](concept-ml-pipelines.md)             |–                                                                         |
| Azure Batch                    |[ML @ no__t – 1pipelines](concept-ml-pipelines.md)                                               |–                                           |–         |–                                                                         |
| Azure DataLake-elemzés       |–                                           |–                                           |[ML @ no__t – 1pipelines](concept-ml-pipelines.md)             |–                                                                         |

> [!NOTE]
> Előfordulhatnak olyan forgatókönyvek, amelyekben a nagy mértékben ismétlődő, nagyméretű adatfolyamatok gyorsabban futnak @no__t – 0 helyett `as_mount()`; ezt a kísérlettel lehet ellenőrizni.

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

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Az adatáthelyezés támogatott Azure Storage-megoldásokra

Az Azure Machine learning szolgáltatás támogatja az Azure Blob, az Azure file, a Azure Data Lake Gen 1, a Azure Data Lake Gen 2, az Azure SQL és az Azure PostgreSQL adatainak elérését. A nem támogatott tárterület esetében az adatforgalomnak a gépi tanulási kísérletek során történő megtakarítása érdekében javasoljuk, hogy az adatait Azure Data Factory használatával helyezze át a támogatott Azure Storage-megoldásokra. Azure Data Factory a hatékony és rugalmas adatátvitelt több mint 80 előre összeépített összekötővel biztosítja – többek között az Azure-adatszolgáltatások, a helyszíni adatforrások, az Amazon S3 és a vöröseltolódás, valamint a Google BigQuery – díjmentesen. [A részletes útmutató lépéseit követve áthelyezheti az adatait Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Következő lépések

* [Modell betanítása](how-to-train-ml-models.md)

* [Modell üzembe helyezése](how-to-deploy-and-where.md)
