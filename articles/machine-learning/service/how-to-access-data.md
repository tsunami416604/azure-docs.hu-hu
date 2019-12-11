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
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 94cdf683bc8524786e1f32607ef18f976990ba07
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979121"
---
# <a name="access-data-in-azure-storage-services"></a>Az Azure Storage-szolgáltatásokban tárolt adathozzáférés
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan érheti el könnyen az Azure Storage-szolgáltatásokban tárolt adatait Azure Machine Learning adattáron keresztül. Az adattárolók a kapcsolódási adatok tárolására szolgálnak, például az előfizetés-AZONOSÍTÓval és a jogkivonat-hitelesítéssel. Az adattárolók használata lehetővé teszi a tárhely elérését anélkül, hogy merevlemez-kapcsolati adatokat kellene létrehoznia a parancsfájlokban. Ezekből az [Azure Storage-megoldásokból is létrehozhat adattárakat](#matrix). A nem támogatott tárolási megoldások esetében, valamint az adatforgalom megtakarítása a gépi tanulási kísérletek során azt javasoljuk, hogy helyezze át az adatait a támogatott Azure Storage-megoldásokra. [Megtudhatja, hogyan helyezheti át adatait](#move). 

Ez az útmutató példákat mutat be a következő feladatokra:
* Adattárolók regisztrálása
* Adattárolók beolvasása a munkaterületről
* Adatok feltöltése és letöltése adattár használatával
* Az adathozzáférés a betanítás során
* Az Azure Storage szolgáltatásba való adatáthelyezés

## <a name="prerequisites"></a>Előfeltételek
A következőkre lesz szüksége
- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

- Azure Storage-fiók [Azure Blob-tárolóval](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) vagy [Azure-fájlmegosztás](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), vagy a [Azure Machine learning studióhoz](https://ml.azure.com/)való hozzáférés.

- Egy Azure Machine Learning-munkaterület. 
    - [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md) , vagy használjon egy meglévőt a Python SDK használatával.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Adattárolók létrehozása és regisztrálása

Ha az Azure Storage-megoldást adattárként regisztrálja, automatikusan létrehozza az adattárat egy adott munkaterületen. Adattárolókat a Python SDK vagy a Azure Machine Learning Studio használatával hozhat létre és regisztrálhat egy munkaterületre.

### <a name="using-the-python-sdk"></a>A Python SDK használata

Az összes regisztrálási módszer a [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) osztályban található, és a Form register_azure_ *.

A Register () metódus feltöltéséhez szükséges információk a [Azure Machine learning Studióval](https://ml.azure.com) és a következő lépésekkel tölthetők le

1. Válassza ki a **Storage-fiókok** elemet a bal oldali ablaktáblán, és válassza ki a regisztrálni kívánt Storage-fiókot. 
2. Az **áttekintő** oldal olyan információkat tartalmaz, mint például a fiók neve, a tároló vagy a fájlmegosztás neve. 
3. A hitelesítési adatokhoz (például a fiók kulcsa vagy az SAS-tokenhez) a bal oldali **Beállítások** ablaktáblában navigáljon a **fiók kulcsaihoz** . 

>FONTOS Ha a Storage-fiókja egy VNET van, akkor csak az Azure Blob-adattár létrehozása támogatott. Állítsa be a paramétert, `grant_workspace_access` `True`, hogy a munkaterület hozzáférjen a Storage-fiókhoz.

Az alábbi példák bemutatják, hogyan regisztrálhat egy Azure BLOB-tárolót vagy egy Azure-fájlmegosztást adattárként.

+ **Azure Blob Container adattár**esetén használja a [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

    A következő kód létrehozza és regisztrálja az adattárat, `my_datastore`a munkaterületre, `ws`. Ez az adattár fér hozzá az Azure Blob-tárolóhoz, `my_blob_container`az Azure Storage-fiókhoz, `my_storage_account` a megadott fiók kulcsát használva.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```

+ **Azure fájlmegosztás-adattár**esetén használja a [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

    A következő kód létrehozza és regisztrálja az adattárat, `my_datastore`a munkaterületre, `ws`. Ez az adattár fér hozzá az Azure-fájlmegosztás `my_file_share`éhez az Azure Storage-fiókban, `my_storage_account` a megadott fiók kulcsa alapján.

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

### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning Studio használata 

Hozzon létre egy új adattárt a Azure Machine Learning Studio néhány lépésében.

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/).
1. A **kezelés**alatt kattintson a bal oldali ablaktábla adattárolók **elemére** .
1. Válassza az **+ új adattár**lehetőséget.
1. Fejezze be az új adattár űrlapot. Az űrlap intelligens frissítése az Azure Storage-típus és a hitelesítés típusa beállítás alapján történik.
  
Az űrlap feltöltéséhez szükséges adatok [Azure Portalon](https://portal.azure.com)keresztül találhatók. Válassza ki a **Storage-fiókok** elemet a bal oldali ablaktáblán, és válassza ki a regisztrálni kívánt Storage-fiókot. Az **áttekintő** oldal olyan információkat tartalmaz, mint például a fiók neve, a tároló vagy a fájlmegosztás neve. A hitelesítési elemek, például a fiók kulcsa vagy az SAS-token esetében a bal oldali **Beállítások** ablaktáblában navigáljon a **fiók kulcsaihoz** .

Az alábbi példa azt mutatja be, hogy az űrlap hogyan néz ki az Azure Blob-adattár létrehozásához. 
    
 ![Új adattár](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Adattárolók beolvasása a munkaterületről

Az aktuális munkaterületen regisztrált adott adattár beszerzéséhez használja a [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) statikus metódust az adattár osztályban:

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

Munkaterületek létrehozásakor a rendszer automatikusan regisztrálja az Azure BLOB-tárolót és egy Azure-fájlmegosztást a `workspaceblobstore` nevű munkaterülethez, és `workspacefilestore`. Ezek tárolja a blob-tároló és a munkaterülethez csatolt Storage-fiókban kiépített fájlmegosztás kapcsolódási adatait. A `workspaceblobstore` alapértelmezett adattárként van beállítva.

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

A `target_path` paraméter adja meg a feltöltendő fájlmegosztás (vagy blob-tároló) helyét. Az alapértelmezett érték `None`, amely esetben az adatfeltöltés a gyökérbe történik. Ellenkező esetben, ha `overwrite=True`, hogy a rendszer felülírja-e az összes meglévő adat`target_path`t.

Vagy az `upload_files()` metódussal feltöltheti az egyes fájlok listáját az adattárba.

### <a name="download"></a>Letöltés

Hasonlóképpen töltse le az adatok egy adattárból a helyi fájlrendszerbe.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

A `target_path` paraméter annak a helyi könyvtárnak a helye, ahová le szeretné tölteni az adatfájlt. A letölteni kívánt fájlmegosztás (vagy blob-tároló) mappájának elérési útjának megadásához adja meg a `prefix`elérési útját. Ha `prefix` `None`, a rendszer a fájlmegosztás (vagy blob-tároló) összes tartalmát letölti.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Az adatai elérése a betanítás során

> [!IMPORTANT]
> A [Azure Machine learning adatkészletek](how-to-create-register-datasets.md) használata az új ajánlott módszer az adathoz való hozzáféréshez a képzésben. Az adatkészletek olyan függvényeket biztosítanak, amelyek a táblázatos adatokat a pandák vagy a Spark DataFrame töltik be, valamint lehetővé teszik bármilyen formátumú fájlok letöltését vagy csatlakoztatását az Azure Blobból, az Azure file-ból, a Azure Data Lake 1., Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL További információ az [adatkészletek betanításáról](how-to-train-with-datasets.md).

A következő táblázat felsorolja azokat a módszereket, amelyekkel a számítási cél az adattárolók futtatás közbeni használatának módját ismerteti. 

Módon|Módszer|Leírás|
----|-----|--------
Csatlakoztatás| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| A használatával csatlakoztathatja az adattárt a számítási célhoz. Ha csatlakoztatva van, a rendszer az adattár összes fájlját elérhetővé teszi a számítási cél számára.
Letöltés|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Ezzel a paranccsal töltheti le az adattár tartalmát `path_on_compute`által megadott helyre. <br><br> Ez a letöltés a Futtatás előtt történik.
Feltöltés|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Ezzel a paranccsal tölthet fel egy fájlt a `path_on_compute` által megadott helyről az adattárba. <br><br> Ez a feltöltés a Futtatás után következik be.

Ha egy adott mappát vagy fájlt szeretne hivatkozni az adattárában, és elérhetővé teszi azt a számítási célra, használja az adattár [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) metódusát.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Minden megadott `datastore` vagy `datastore.path` objektum a formátum `"$AZUREML_DATAREFERENCE_XXXX"`egy környezeti változó nevével oldható fel, amelynek értéke a cél számításhoz tartozó csatlakoztatási/letöltési útvonal. Előfordulhat, hogy az adattár elérési útja nem egyezik meg a betanítási parancsfájl végrehajtási útjával.

### <a name="examples"></a>Példák 

Javasoljuk, hogy a [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztály használatával hozzáférjen az adatokhoz a betanítás során. 

A `script_params` változó a entry_script paramétereket tartalmazó szótár. Ezzel a művelettel továbbíthatja az adattárolót, és leírhatja, hogyan történik az adatok elérhetővé tétele a számítási célra. További információt a teljes körű [oktatóanyagban](tutorial-train-models-with-aml.md)olvashat.

```Python
from azureml.train.estimator import Estimator

# notice '/' is in front, this indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Átadhatja az adattárolók listáját is a kalkulátor konstruktorának `inputs` paraméterre az adattár (ok) adatainak csatlakoztatásához vagy másolásához. Példa erre a kódra:
* A betanítási parancsfájl `train.py` futtatása előtt letölti a `datastore1` összes tartalmát a számítási célhoz.
* A `train.py` futtatása előtt letölti a `datastore2` `'./foo'` mappát a számítási célhoz
* A parancsfájl futtatása után feltölti a `'./bar.pkl'` fájlt a számítási célhelyről a `datastore3`ra.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
Ha RunConfig-objektumot szeretne használni a betanításhoz, létre kell hoznia egy [DataReference](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) objektumot. 

A következő kód bemutatja, hogyan használható egy DataReference objektum egy becslési folyamatban. A teljes példa: Ez a [Jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb).

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

   output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Számítási és adattár-mátrix

Az adattárolók jelenleg támogatják a kapcsolódási adatok tárolását a következő mátrixban felsorolt tárolási szolgáltatásokhoz. Ez a mátrix a különböző számítási célok és adattárolási forgatókönyvek elérhető adathozzáférési funkcióit jeleníti meg. További információ a [Azure Machine learning számítási céljairól](how-to-set-up-training-targets.md#compute-targets-for-training).

|Számítási szolgáltatások|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Helyi|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|–         |–                                                                         |
| Azure Machine Learning Compute |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml&nbsp;folyamatok](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml&nbsp;folyamatok](concept-ml-pipelines.md)|–         |–                                                                         |
| Virtual machines (Virtuális gépek)               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |–         |–                                                                         |
| HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |–         |–                                                                         |
| Adatátvitel                  |[ML&nbsp;folyamatok](concept-ml-pipelines.md)                                               |–                                           |[ML&nbsp;folyamatok](concept-ml-pipelines.md)            |[ML&nbsp;folyamatok](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;folyamatok](concept-ml-pipelines.md)                                              |–                                           |[ML&nbsp;folyamatok](concept-ml-pipelines.md)             |–                                                                         |
| Azure Batch                    |[ML&nbsp;folyamatok](concept-ml-pipelines.md)                                               |–                                           |–         |–                                                                         |
| Azure DataLake-elemzés       |–                                           |–                                           |[ML&nbsp;folyamatok](concept-ml-pipelines.md)             |–                                                                         |

> [!NOTE]
> Előfordulhatnak olyan forgatókönyvek, amelyekben a nagy mértékben ismétlődő, nagyméretű adatfolyamatok gyorsabban futnak `as_download()` a `as_mount()`helyett. ezt a kísérlettel lehet ellenőrizni.

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

Azure Machine Learning támogatja az Azure Blob, az Azure file, a Azure Data Lake Gen 1, a Azure Data Lake Gen 2, az Azure SQL, az Azure PostgreSQL és az Azure-beli adatok elérését. A nem támogatott tárterület esetében az adatforgalomnak a gépi tanulási kísérletek során történő megtakarítása érdekében javasoljuk, hogy az adatait Azure Data Factory használatával helyezze át a támogatott Azure Storage-megoldásokra. Azure Data Factory a hatékony és rugalmas adatátvitelt több mint 80 előre összeépített összekötővel biztosítja – többek között az Azure-adatszolgáltatások, a helyszíni adatforrások, az Amazon S3 és a vöröseltolódás, valamint a Google BigQuery – díjmentesen. [A részletes útmutató lépéseit követve áthelyezheti az adatait Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Következő lépések

* [Modell betanítása](how-to-train-ml-models.md).

* [Modell üzembe helyezése](how-to-deploy-and-where.md).
