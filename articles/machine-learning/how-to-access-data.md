---
title: Az Azure Storage-szolgáltatásokban tárolt adathozzáférés
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az adattárakat az Azure Storage-szolgáltatásokhoz való biztonságos kapcsolódásra Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: 6d68599af644e5bb03fc850a880b07c6a4d262a9
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370484"
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
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Támogatott adattárolási szolgáltatások típusai

Az adattárolók jelenleg támogatják a kapcsolódási adatok tárolását a következő mátrixban felsorolt tárolási szolgáltatásokhoz. Az Azure-adattárház jelenleg nem támogatott. 

| Storage&nbsp;típusa | Hitelesítés&nbsp;típusa | [Azure&nbsp;Machine&nbsp;learning Studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;learning&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;Machine,&nbsp;learning parancssori felület](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;learning&nbsp; REST API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure&nbsp;blob&nbsp;Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Fiókkulcs <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure&nbsp;fájl&nbsp;megosztás](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Fiókkulcs <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| Egyszerű szolgáltatásnév| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Egyszerű szolgáltatásnév| ✓ | ✓ | ✓ |✓
Azure&nbsp;SQL&nbsp;-adatbázis| SQL-hitelesítés <br>Egyszerű szolgáltatásnév| ✓ | ✓ | ✓ |✓
Azure&nbsp;PostgreSQL | SQL-hitelesítés| ✓ | ✓ | ✓ |✓
Azure-&nbsp;adatbázis-&nbsp;&nbsp;MySQL-hez | SQL-hitelesítés|  | ✓ | ✓ |✓
Databricks&nbsp;fájl&nbsp;System| Nincs hitelesítés | | ✓ | ✓ |✓ 

\* csak helyi számítási célok esetén támogatott

### <a name="storage-guidance"></a>Storage – útmutató

Javasoljuk, hogy hozzon létre egy adattárt egy Azure Blob-tárolóhoz.  
Mind a standard, mind a Premium Storage elérhető a Blobok számára. Habár a Premium Storage drágább, a gyorsabb átviteli sebesség növelheti a képzések sebességét, különösen akkor, ha egy nagyméretű adathalmazra van betanítva. További információ a Storage-fiókok költségéről: az [Azure díjszabási kalkulátora](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

Munkaterületek létrehozásakor a rendszer automatikusan regisztrálja az Azure BLOB-tárolót és egy Azure-fájlmegosztást a munkaterületen. Ezek neve `workspaceblobstore` és `workspacefilestore`, illetve. A blob-tároló és a munkaterülethez csatolt Storage-fiókban kiépített fájlmegosztás kapcsolódási adatait tárolják. Az `workspaceblobstore` tároló alapértelmezett adattárként van beállítva.

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Adattárolók létrehozása és regisztrálása

Ha az Azure Storage-megoldást adattárként regisztrálja, automatikusan létrehozza és regisztrálja az adattárt egy adott munkaterületen. Adattárolókat a Python SDK vagy a Azure Machine Learning Studio használatával hozhat létre és regisztrálhat egy munkaterületre.

>[!IMPORTANT]
> Az aktuális adattár létrehozása és regisztrálása során Azure Machine Learning ellenőrzi, hogy a felhasználó által megadott rendszerbiztonsági tag (Felhasználónév, szolgáltatásnév vagy SAS-jogkivonat) hozzáfér-e a mögöttes tárolási szolgáltatáshoz. 
<br>
Az 1. és a 2. Azure Data Lake Storage adattárolók esetében azonban ez az ellenőrzés később is megtörténik, amikor az adatelérési módszerek, például a [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) vagy a [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) neve. 

### <a name="python-sdk"></a>Python SDK

Az összes regisztrálási módszer a [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) osztályban található, és az űrlap `register_azure_*`.

A `register()` módszernek a [Azure Portal](https://portal.azure.com)használatával történő feltöltéséhez szükséges információkat a következő módon találja:

1. Válassza ki a **Storage-fiókok** elemet a bal oldali ablaktáblán, és válassza ki a regisztrálni kívánt Storage-fiókot. 
2. A fiók neve, a tároló és a fájlmegosztás neve például az **Áttekintés oldalon olvasható** . A hitelesítési adatokhoz (például a fiók kulcsa vagy az SAS-tokenhez) lépjen a **Beállítások** ablaktábla **hozzáférési kulcsok** elemére. 

> [!IMPORTANT]
> Ha a Storage-fiókja egy virtuális hálózaton van, csak az Azure Blob-adattár létrehozása támogatott. Ha meg szeretné adni a munkaterület hozzáférését a Storage-fiókhoz, állítsa a `grant_workspace_access` paramétert `True`re.

Az alábbi példák bemutatják, hogyan regisztrálhat egy Azure BLOB-tárolót, egy Azure-fájlmegosztást és Azure Data Lake Storage 2. generációját adattárként. Más tárolási szolgáltatások esetében tekintse meg a [`register_azure_*` metódusok dokumentációját](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

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

#### <a name="azure-data-lake-storage-generation-2"></a>2\. generációs Azure Data Lake Storage

Egy Azure Data Lake Storage 2. generációs (ADLS Gen 2) adattár esetében a [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) használatával regisztrálja az Azure DataLake Gen 2 tárolóhoz csatlakoztatott hitelesítő adatokat az [egyszerű szolgáltatás engedélyeivel](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). További információ a [2. generációs ADLS-hez beállított hozzáférés-vezérlésről](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

A következő kód létrehozza és regisztrálja a `adlsgen2_datastore_name` adattárt a `ws` munkaterületen. Ez az adattár fér hozzá a fájlrendszer `test` a `account_name` Storage-fiókban a megadott egyszerű szolgáltatás hitelesítő adataival.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

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
## <a name="upload-and-download-data"></a>Adatok le- és feltöltése

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

Az adattárolókban lévő adatokkal való kommunikációhoz, illetve az adatoknak a gépi tanulási feladatok, például a képzés, a [Azure Machine learning adatkészletek létrehozásához](how-to-create-register-datasets.md)használatos objektumba való becsomagolásához. Az adatkészletek olyan függvényeket biztosítanak, amelyek táblázatos adatokat töltenek be egy Panda vagy Spark DataFrame. Az adatkészletek lehetővé teszik az Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database és Azure Database for PostgreSQL bármilyen formátumú fájljának letöltését vagy csatlakoztatását is. [További információ az adatkészletek betanításáról](how-to-train-with-datasets.md).

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
| [Kötegelt előrejelzés](how-to-use-parallel-run-step.md) | ✔ | Előrejelzések készítése aszinkron módon nagy mennyiségű adattal. |
| [Webszolgáltatás](how-to-deploy-and-where.md) | &nbsp; | Modellek üzembe helyezése webszolgáltatásként. |
| [Azure IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Modellek üzembe helyezése IoT Edge eszközökön. |

Olyan helyzetekben, amikor az SDK nem biztosít hozzáférést az adattárolók számára, létrehozhat egyéni kódokat a megfelelő Azure SDK használatával az adateléréshez. A [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python) például egy ügyféloldali kódtár, amely a blobokban vagy fájlokban tárolt adateléréshez használható.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Az adatáthelyezés támogatott Azure Storage-megoldásokra

Azure Machine Learning támogatja az Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database és Azure Database for PostgreSQL adatainak elérését. Ha nem támogatott tárterületet használ, javasoljuk, hogy [Azure Data Factory és az alábbi lépések](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)segítségével helyezze át az adatait a támogatott Azure Storage-megoldásokra. Az adatforgalom a támogatott tárolóba való áthelyezésével megtakaríthatja a kimenő adatforgalom költségeit a gépi tanulási kísérletek során. 

Azure Data Factory a hatékony és rugalmas adatátvitelt több mint 80 előre összeépített összekötővel biztosítja, felár nélkül. Ezek az összekötők közé tartoznak az Azure adatszolgáltatások, a helyszíni adatforrások, az Amazon S3 és a vöröseltolódás, valamint a Google BigQuery.

## <a name="next-steps"></a>Következő lépések

* [Azure Machine learning-adatkészlet létrehozása](how-to-create-register-datasets.md)
* [Modell betanítása](how-to-train-ml-models.md)
* [Modell üzembe helyezése](how-to-deploy-and-where.md)
