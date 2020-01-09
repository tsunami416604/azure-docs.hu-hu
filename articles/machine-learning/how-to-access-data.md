---
title: Az Azure Storage-szolgáltatásokban tárolt adathozzáférés
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az adattárakat az Azure Storage-szolgáltatásokhoz való biztonságos kapcsolódásra Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ylxiong
author: YLXiong1125
ms.reviewer: nibaccam
ms.date: 12/10/2019
ms.custom: seodec18
ms.openlocfilehash: ac6ef6341013ca13d5a9f27be8897365c1c2155d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540944"
---
# <a name="access-data-in-azure-storage-services"></a>Az Azure Storage-szolgáltatásokban tárolt adathozzáférés
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan érheti el könnyen az Azure Storage-szolgáltatásokban tárolt adatait Azure Machine Learning adattáron keresztül. Az adattárolók a kapcsolódási adatok tárolására szolgálnak, például az előfizetés-AZONOSÍTÓval és a jogkivonat-hitelesítéssel. Az adattárolók használatakor anélkül férhet hozzá a tárhelyhez, hogy a parancsfájlokban rögzített kapcsolati adatokat kellene létrehoznia. 

[Ezekből az Azure Storage-megoldásokból is létrehozhat adattárakat](#matrix). A nem támogatott tárolási megoldások esetében, valamint az adatforgalom megtakarítása a gépi tanulási kísérletek során azt javasoljuk, hogy [Helyezze át az adatait](#move) a támogatott Azure Storage-megoldásokra. 

## <a name="prerequisites"></a>Előfeltételek
A következők szükségesek:
- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

- Azure Storage-fiók [Azure Blob-tárolóval](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) vagy [Azure-fájlmegosztás](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), vagy a [Azure Machine learning studióhoz](https://ml.azure.com/)való hozzáférés.

- Egy Azure Machine Learning-munkaterület.
  
  [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md) , vagy használjon egy meglévőt a Python SDK-n keresztül:

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Adattárolók létrehozása és regisztrálása

Ha az Azure Storage-megoldást adattárként regisztrálja, automatikusan létrehozza az adattárat egy adott munkaterületen. Adattárolókat a Python SDK vagy a Azure Machine Learning Studio használatával hozhat létre és regisztrálhat egy munkaterületre.

### <a name="python-sdk"></a>Python SDK

Az összes regisztrálási módszer a [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) osztályban található, és az űrlap `register_azure_*`.

A `register()` módszernek a [Azure Portal](https://portal.azure.com)használatával történő feltöltéséhez szükséges információkat a következő módon találja:

1. Válassza ki a **Storage-fiókok** elemet a bal oldali ablaktáblán, és válassza ki a regisztrálni kívánt Storage-fiókot. 
2. A fiók neve, a tároló és a fájlmegosztás neve például az **Áttekintés oldalon olvasható** . A hitelesítési adatokhoz (például a fiók kulcsa vagy az SAS-tokenhez) lépjen a **Beállítások** ablaktábla **hozzáférési kulcsok** elemére. 

> [!IMPORTANT]
> Ha a Storage-fiókja egy virtuális hálózaton van, csak az Azure Blob-adattár létrehozása támogatott. Ha meg szeretné adni a munkaterület hozzáférését a Storage-fiókhoz, állítsa a `grant_workspace_access` paramétert `True`re.

Az alábbi példák bemutatják, hogyan regisztrálhat egy Azure BLOB-tárolót, egy Azure-fájlmegosztást és egy Azure SQL-adattárat adattárként.

#### <a name="blob-container"></a>Blobtároló

Az Azure Blob-tárolók adattárként való regisztrálásához használja a [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

A következő kód létrehozza és regisztrálja a `blob_datastore_name` adattárt a `ws` munkaterületen. Ez az adattár fér hozzá a `my-container-name` blob-tárolóhoz a `my-account-name` Storage-fiókban a megadott fiók kulcsának használatával.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Fájlmegosztás

Az Azure-fájlmegosztás adattárként való regisztrálásához használja a [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

A következő kód létrehozza és regisztrálja a `file_datastore_name` adattárt a `ws` munkaterületen. Ez az adattár fér hozzá a `my-fileshare-name` fájlmegosztást a `my-account-name` Storage-fiókban a megadott fiók kulcsának használatával.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                 datastore_name=file_datastore_name, 
                                                 file_share_name=file_share_name, 
                                                 account_name=account_name,
                                                 account_key=account_key)
```

#### <a name="sql-data"></a>SQL-adatszolgáltatások

Az Azure SQL-adattárolók esetében [register_azure_sql_database ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none-) használatával regisztrálja az Azure SQL Database-adatbázishoz csatlakozó hitelesítő adatokat az SQL-hitelesítéssel vagy a szolgáltatás egyszerű engedélyeivel. 

Regisztrálás az SQL-hitelesítésen keresztül:

```python
sql_datastore_name="azsqlsdksql"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the Azure SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the Azure SQL database
username=os.getenv("SQL_USER_NAME", "<my-sql-user-name>") # Username of the database user to access the database
password=os.getenv("SQL_USER_PASSWORD", "<my-sql-user-password>") # Password of the database user to access the database

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                  datastore_name=sql_datastore_name,
                                                  server_name=server_name,
                                                  database_name=database_name,
                                                  username=username,
                                                  password=password)

```

Egy egyszerű szolgáltatáson keresztüli regisztrálás:

```python 
sql_datastore_name="azsqlsdksp"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the SQL database
client_id=os.getenv("SQL_CLIENTNAME", "<my-client-id>") # Client ID of the service principal with permissions to access the database
client_secret=os.getenv("SQL_CLIENTSECRET", "<my-client-secret>") # Secret of the service principal
tenant_id=os.getenv("SQL_TENANTID", "<my-tenant-id>") # Tenant ID of the service principal

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                      datastore_name=sql_datastore_name,
                                                      server_name=server_name,
                                                      database_name=database_name,
                                                      client_id=client_id,
                                                      client_secret=client_secret,
                                                      tenant_id=tenant_id)
```

#### <a name="storage-guidance"></a>Storage – útmutató

Javasoljuk, hogy egy Azure BLOB-tárolót. Mind a standard, mind a Premium Storage elérhető a Blobok számára. Habár a Premium Storage drágább, a gyorsabb átviteli sebesség növelheti a képzések sebességét, különösen akkor, ha egy nagyméretű adathalmazra van betanítva. További információ a Storage-fiókok költségéről: az [Azure díjszabási kalkulátora](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio 

Hozzon létre egy új adattárt néhány lépésben a Azure Machine Learning Studióban:

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/).
1. A **kezelés** **alatt kattintson a** bal oldali ablaktábla adattárolók elemére.
1. Válassza az **+ új adattár**lehetőséget.
1. Töltse ki az űrlapot egy új adattárhoz. Az űrlap intelligens módon frissül saját maga, az Azure Storage-típus és a hitelesítési típus választása alapján.
  
Itt megtalálhatja azokat az adatokat, amelyekre az űrlapot fel kell töltenie a [Azure Portal](https://portal.azure.com). Válassza ki a **Storage-fiókok** elemet a bal oldali ablaktáblán, és válassza ki a regisztrálni kívánt Storage-fiókot. Az **áttekintő** oldal olyan információkat tartalmaz, mint például a fióknév, a tároló és a fájlmegosztás neve. A hitelesítési elemek, például a fiók kulcsa vagy az SAS-token esetében a **Beállítások** ablaktáblán lépjen a **fiókok kulcsai** elemre.

Az alábbi példa bemutatja, hogyan néz ki az űrlap az Azure Blob-adattár létrehozásakor: 
    
![Űrlap új adattárhoz](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Adattárolók beolvasása a munkaterületről

Az aktuális munkaterületen regisztrált adott adattár beszerzéséhez használja a [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) statikus metódust a `Datastore` osztályban:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Egy adott munkaterülethez regisztrált adattárolók listájának lekéréséhez használhatja a [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) tulajdonságot egy munkaterület-objektumon:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Munkaterületek létrehozásakor a rendszer automatikusan regisztrálja az Azure BLOB-tárolót és egy Azure-fájlmegosztást a munkaterületen. Ezek neve `workspaceblobstore` és `workspacefilestore`, illetve. A blob-tároló és a munkaterülethez csatolt Storage-fiókban kiépített fájlmegosztás kapcsolódási adatait tárolják. Az `workspaceblobstore` tároló alapértelmezett adattárként van beállítva.

A munkaterület alapértelmezett adattárának beszerzéséhez használja ezt a sort:

```Python
datastore = ws.get_default_datastore()
```

Ha másik alapértelmezett adattárat szeretne megadni az aktuális munkaterülethez, használja a [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) metódust a munkaterület objektumon:

```Python
# Define the default datastore for the current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Adatok feltöltése és letöltése

Az alábbi példákban ismertetett [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) és [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metódusok a [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) és a [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) osztályokra vonatkozó, és azokkal azonos módon működnek.

### <a name="upload"></a>Feltöltés

Töltsön fel egy könyvtárat vagy egyedi fájlt az adattárba a Python SDK használatával:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

A `target_path` paraméter adja meg a feltöltendő fájlmegosztás (vagy blob-tároló) helyét. Alapértelmezés szerint `None`, így az adatfeltöltés a gyökérbe történik. Ha `overwrite=True`, a rendszer felülírja a `target_path` összes meglévő szolgáltatását.

A `upload_files()` metódussal feltöltheti az egyes fájlok listáját is az adattárba.

### <a name="download"></a>Letöltés

Adatok letöltése egy adattárból a helyi fájlrendszerbe:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

A `target_path` paraméter annak a helyi könyvtárnak a helye, ahová le szeretné tölteni az adatfájlt. A letölteni kívánt fájlmegosztás (vagy blob-tároló) mappájának elérési útjának megadásához adja meg a `prefix`elérési útját. Ha `prefix` `None`, a rendszer a fájlmegosztás (vagy blob-tároló) összes tartalmát letölti.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Az adatai elérése a betanítás során

> [!IMPORTANT]
> Azt javasoljuk, hogy [Azure Machine learning adatkészletek](how-to-create-register-datasets.md) használatával hozzáférjen az adataihoz a képzésben. Az adatkészletek olyan függvényeket biztosítanak, amelyek táblázatos adatokat töltenek be egy Panda vagy Spark DataFrame. Az adatkészletek lehetővé teszik az Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database és Azure Database for PostgreSQL bármilyen formátumú fájljának letöltését vagy csatlakoztatását is. [További információ az adatkészletek betanításáról](how-to-train-with-datasets.md).

A következő táblázat felsorolja azokat a metódusokat, amelyek alapján a számítási cél az adattárolók használatának módja a Futtatás során: 

Módon|Módszer|Leírás|
----|-----|--------
Csatlakoztatás| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| A használatával csatlakoztathatja az adattárt a számítási célhoz. Az adattár csatlakoztatásakor a rendszer az adattár összes fájlját elérhetővé teszi a számítási cél számára.
Letöltés|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Ezzel a paranccsal töltheti le az adattár tartalmát `path_on_compute`által megadott helyre. <br><br> Ez a letöltés a Futtatás előtt történik.
Feltöltés|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Ezzel a paranccsal tölthet fel egy fájlt a `path_on_compute` által megadott helyről az adattárba. <br><br> Ez a feltöltés a Futtatás után következik be.

Ha egy adott mappát vagy fájlt szeretne hivatkozni az adattárában, és elérhetővé teszi azt a számítási célra, használja az adattár [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) metódusát:

```Python
# To mount the full contents in your storage to the compute target
datastore.as_mount()

# To download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Minden megadott `datastore` vagy `datastore.path` objektum a formátum `"$AZUREML_DATAREFERENCE_XXXX"`egy környezeti változó nevéhez van feloldva. A név értéke a számítási cél csatlakoztatási/letöltési útvonala. Előfordulhat, hogy a számítási cél adattárának elérési útja nem egyezik meg a betanítási parancsfájl végrehajtási útjával.

### <a name="examples"></a>Példák 

Javasoljuk, hogy a [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) osztály használatával hozzáférjen az adatokhoz a betanítás során. 

A `script_params` változó egy olyan szótár, amely `entry_script`paramétereket tartalmaz. Ezzel a művelettel továbbíthatja az adattárolót, és leírhatja, hogyan történik az adatok elérhetővé tétele a számítási célra. További információt a teljes [körű oktatóanyagban](tutorial-train-models-with-aml.md)olvashat.

```Python
from azureml.train.estimator import Estimator

# Notice that '/' is in front, which indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Átadhatja az adattárolók listáját is a `Estimator` konstruktor `inputs` paraméteréhez az adattárba/adatokba való csatlakoztatáshoz vagy másoláshoz. Példa erre a kódra:
* A `train.py` tanítási parancsfájl futtatása előtt letölti a `datastore1` összes tartalmát a számítási célhoz.
* A `train.py` futtatása előtt letölti `datastore2` `'./foo'` mappát a számítási célhoz.
* Feltölti a `'./bar.pkl'` fájlt a számítási célhelyről `datastore3` a parancsfájl futtatása után.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
Ha `RunConfig` objektumot szeretne betanításra használni, be kell állítania egy [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) objektumot. 

A következő kód bemutatja, hogyan használható egy `DataReference` objektum egy becslési folyamatban. A teljes példa: Ez a [Jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb).

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

Az adattárolók jelenleg támogatják a kapcsolódási adatok tárolását a következő mátrixban felsorolt tárolási szolgáltatásokhoz. Ez a mátrix a különböző számítási célokhoz és adattárolási forgatókönyvekhez elérhető adathozzáférési funkciókat jeleníti meg. [További információ a Azure Machine learning számítási céljairól](how-to-set-up-training-targets.md#compute-targets-for-training).

|Számítási szolgáltatások|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Helyi|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|–         |–                                                                         |
| Azure Machine Learning számítás |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [Machine learning folyamatok](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [Machine learning folyamatok](concept-ml-pipelines.md)|–         |–                                                                         |
| Virtuális gépek               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |–         |–                                                                         |
| Azure HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |–         |–                                                                         |
| Adatátvitel                  |[Machine Learning folyamatok](concept-ml-pipelines.md)                                               |–                                           |[Machine Learning folyamatok](concept-ml-pipelines.md)            |[Machine Learning folyamatok](concept-ml-pipelines.md)                                                                            |
| Azure Databricks                     |[Machine Learning folyamatok](concept-ml-pipelines.md)                                              |–                                           |[Machine Learning folyamatok](concept-ml-pipelines.md)             |–                                                                         |
| Azure Batch                    |[Machine Learning folyamatok](concept-ml-pipelines.md)                                               |–                                           |–         |–                                                                         |
| Azure Data Lake Analytics       |–                                           |–                                           |[Machine Learning folyamatok](concept-ml-pipelines.md)             |–                                                                         |

> [!NOTE]
> Előfordulhatnak olyan helyzetek, amikor a nagy mértékben ismétlődő, nagyméretű adatfolyamatok gyorsabban futnak `as_download()` a `as_mount()`helyett. Ezt a kísérletet ellenőrizheti.

### <a name="accessing-source-code-during-training"></a>Forráskód elérése a betanítás során

Az Azure Blob Storage nagyobb átviteli sebességgel rendelkezik, mint egy Azure-fájlmegosztás, és nagy mennyiségű, párhuzamosan elindított feladatra fog méretezni. Ezért javasoljuk, hogy a futtatások konfigurálásával blob Storage-t használjon a forráskód-fájlok átviteléhez.

A következő mintakód a futtatási konfigurációban adja meg, hogy melyik blob-adattár használható a forráskód-átvitelhez:

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Az adathozzáférés a pontozás során

A Azure Machine Learning számos módszert kínál a modellek pontozási célra való használatára. A módszerek némelyike nem biztosít hozzáférést az adattárolóhoz. A következő táblázat segítségével megtudhatja, hogy mely módszerek lehetővé teszik az adattárolók elérését a pontozás során:

| Módszer | Adattár-hozzáférés | Leírás |
| ----- | :-----: | ----- |
| [Kötegelt előrejelzés](how-to-run-batch-predictions.md) | ✔ | Előrejelzések készítése aszinkron módon nagy mennyiségű adattal. |
| [Webszolgáltatás](how-to-deploy-and-where.md) | &nbsp; | Modellek üzembe helyezése webszolgáltatásként. |
| [Azure IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Modellek üzembe helyezése IoT Edge eszközökön. |

Olyan helyzetekben, amikor az SDK nem biztosít hozzáférést az adattárolók számára, létrehozhat egyéni kódokat a megfelelő Azure SDK használatával az adateléréshez. A [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python) például egy ügyféloldali kódtár, amely a blobokban vagy fájlokban tárolt adateléréshez használható.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Az adatáthelyezés támogatott Azure Storage-megoldásokra

Azure Machine Learning támogatja az Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database és Azure Database for PostgreSQL adatainak elérését. Ha nem támogatott tárterületet használ, javasoljuk, hogy Azure Data Factory használatával helyezze át az adatait a támogatott Azure Storage-megoldásokra. Az adatforgalom a támogatott tárolóba való áthelyezésével megtakaríthatja a kimenő adatforgalom költségeit a gépi tanulási kísérletek során. 

Azure Data Factory a hatékony és rugalmas adatátvitelt több mint 80 előre összeépített összekötővel biztosítja, felár nélkül. Ezek az összekötők közé tartoznak az Azure adatszolgáltatások, a helyszíni adatforrások, az Amazon S3 és a vöröseltolódás, valamint a Google BigQuery. A [részletes útmutatót követve áthelyezheti adatait Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Következő lépések

* [Modell betanítása](how-to-train-ml-models.md)
* [Modell üzembe helyezése](how-to-deploy-and-where.md)
