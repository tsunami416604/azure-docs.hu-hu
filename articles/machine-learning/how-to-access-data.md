---
title: Kapcsolódás az Azure Storage-szolgáltatásokhoz
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az adattárakat az Azure Storage-szolgáltatásokhoz való biztonságos kapcsolódásra Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 904738d73aaa0580773a085c70cd74f4240fc4b7
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773934"
---
# <a name="connect-to-azure-storage-services"></a>Kapcsolódás az Azure Storage-szolgáltatásokhoz
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan csatlakozhat az Azure Storage-szolgáltatásokhoz Azure Machine Learning adattáron keresztül. Az adattár tárolja a kapcsolati adatokat, például az előfizetési azonosítót és a jogkivonat-engedélyezést a munkaterülethez társított [Key Vaultban](https://azure.microsoft.com/services/key-vault/) , így biztonságosan hozzáférhet a tárolóhoz anélkül, hogy a parancsfájlokban rögzített kódokat kellene megtennie. Ha szeretné megismerni, hogy az adattárolók hogyan illeszkednek Azure Machine Learning összesített adatelérési munkafolyamataihoz, tekintse meg a [biztonságos hozzáférésről](concept-data.md#data-workflow) szóló cikket.

[Ezekből az Azure Storage-megoldásokból is létrehozhat adattárakat](#matrix). A nem támogatott tárolási megoldások esetében, valamint az adatforgalom megtakarítása a gépi tanulási kísérletek során azt javasoljuk, hogy [Helyezze át az adatait](#move) a támogatott Azure Storage-megoldásokra. 

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:
- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

- Azure Storage-fiók [Azure Blob-tárolóval](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) vagy [Azure-fájlmegosztás](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), vagy a [Azure Machine learning studióhoz](https://ml.azure.com/)való hozzáférés.

- Egy Azure Machine Learning-munkaterület.
  
  [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md) , vagy használjon egy meglévőt a Python SDK-n keresztül. Importálja a `Workspace` és az `Datastore` osztályt, és töltse be az előfizetési adatokat a fájlból `config.json` a függvény használatával `from_config()` . Ez alapértelmezés szerint az aktuális könyvtárban keresi a JSON-fájlt, de megadhat egy elérésiút-paramétert is, amely a fájlra mutat `from_config(path="your/file/path")` .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Támogatott adattárolási szolgáltatások típusai

Az adattárolók jelenleg támogatják a kapcsolódási adatok tárolását a következő mátrixban felsorolt tárolási szolgáltatásokhoz.

| Tárolási &nbsp; típus | Hitelesítés &nbsp; típusa | [Azure &nbsp; Machine &nbsp; learning Studio](https://ml.azure.com/) | [Az Azure &nbsp; Machine &nbsp; learning &nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure &nbsp; Machine &nbsp; learning parancssori felület](reference-azure-machine-learning-cli.md) | [Azure &nbsp; Machine &nbsp; learning – &nbsp; REST API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure &nbsp; blob &nbsp; Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Fiókkulcs <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure- &nbsp; fájlmegosztás &nbsp;](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Fiókkulcs <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure &nbsp; Data Lake &nbsp; Storage, &nbsp; 1. generációs](https://docs.microsoft.com/azure/data-lake-store/)| Szolgáltatásnév| ✓ | ✓ | ✓ |✓
[Azure &nbsp; Data Lake &nbsp; Storage, &nbsp; 2. generációs](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Szolgáltatásnév| ✓ | ✓ | ✓ |✓
[Azure &nbsp; SQL &nbsp; Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL-hitelesítés <br>Szolgáltatásnév| ✓ | ✓ | ✓ |✓
[Azure &nbsp; PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | SQL-hitelesítés| ✓ | ✓ | ✓ |✓
[MySQL-hez készült Azure- &nbsp; adatbázis &nbsp; &nbsp;](https://docs.microsoft.com/azure/mysql/overview) | SQL-hitelesítés|  | ✓* | ✓* |✓*
[Databricks &nbsp; fájlrendszer &nbsp;](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Nincs hitelesítés | | ✓** | ✓ ** |✓** 

* A MySQL-t csak a folyamat [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)támogatja. <br>
* * A Databricks csak a folyamat [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) esetében támogatott

### <a name="storage-guidance"></a>Storage – útmutató

Javasoljuk, hogy hozzon létre egy adattárt egy [Azure Blob-tárolóhoz](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Mind a standard, mind a Premium Storage elérhető a Blobok számára. Habár a Premium Storage drágább, a gyorsabb átviteli sebesség növelheti a képzések sebességét, különösen akkor, ha egy nagyméretű adathalmazra van betanítva. További információ a Storage-fiókok költségéről: az [Azure díjszabási kalkulátora](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) az Azure Blob Storage-ra épül, és nagyvállalati Big Data elemzésre lett tervezve. Data Lake Storage Gen2 alapvető része egy [hierarchikus névtér](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) hozzáadása a blob Storage-hoz. A hierarchikus névtér objektumokat vagy fájlokat rendez a címtárak hierarchiájában a hatékony adateléréshez.

Munkaterületek létrehozásakor a rendszer automatikusan regisztrálja az Azure BLOB-tárolót és egy Azure-fájlmegosztást a munkaterületen. Ezek neve `workspaceblobstore` és `workspacefilestore` , illetve. `workspaceblobstore`a a munkaterület-összetevők és a Machine learning-kísérletek naplófájljainak tárolására szolgál. `workspacefilestore`a a [számítási példányon](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)keresztül engedélyezve lévő jegyzetfüzetek és R-parancsfájlok tárolására szolgál. A `workspaceblobstore` tároló alapértelmezett adattárként van beállítva.

> [!IMPORTANT]
> A Azure Machine Learning Designer (előzetes verzió) automatikusan létrehoz egy **azureml_globaldatasets** nevű adattárat, amikor megnyit egy mintát a tervező kezdőlapján. Ez az adattár csak minta adatkészleteket tartalmaz. A bizalmas adatokhoz való hozzáféréshez **ne** használja ezt az adattárt.
> ![Automatikusan létrehozott adattár Designer minta adatkészletekhez](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Adattárolók létrehozása és regisztrálása

Ha az Azure Storage-megoldást adattárként regisztrálja, automatikusan létrehozza és regisztrálja az adattárt egy adott munkaterületen. Adattárolókat a [PYTHON SDK](#python-sdk) vagy a [Azure Machine learning Studio](#azure-machine-learning-studio)használatával hozhat létre és regisztrálhat egy munkaterületre.

>[!IMPORTANT]
> A kezdeti adattár létrehozása és regisztrálása során Azure Machine Learning ellenőrzi, hogy a mögöttes tárolási szolgáltatás létezik-e, és hogy a felhasználó által megadott rendszerbiztonsági tag (Felhasználónév, szolgáltatásnév vagy SAS-jogkivonat) hozzáfér-e a tárolóhoz. Az 1. és a 2. Azure Data Lake Storage adattárolók esetében azonban ez az ellenőrzés később is megtörténik, amikor az adatelérési módszerek, például [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) a vagy a neve. 
<br><br>
Az adattár létrehozása után ezt az ellenőrzést csak olyan metódusok hajtják végre, amelyek hozzáférést igényelnek a mögöttes tárolóhoz, **nem** minden alkalommal, amikor az adattár-objektumokat lekérik. Például az ellenőrzés akkor történik meg, ha le szeretné tölteni a fájlokat az adattárból. Ha azonban csak módosítani szeretné az alapértelmezett adattárat, akkor az érvényesítés nem történik meg.

### <a name="python-sdk"></a>Python SDK

Az összes regisztrációs metódus az osztályban van [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) , és rendelkezik az űrlappal `register_azure_*` .
> [!IMPORTANT]
> Ha a Storage-fiókja egy virtuális hálózatban található, akkor csak **az SDK-n keresztüli** adattárolók létrehozását támogatja a rendszer.

Itt megtalálhatja a `register_azure_*()` metódusnak a [Azure Portalon](https://portal.azure.com)való feltöltéséhez szükséges adatokat.

* Ha a hitelesítéshez fiókot vagy SAS-tokent szeretne használni, válassza ki a **Storage-fiókok** lehetőséget a bal oldali ablaktáblán, és válassza ki a regisztrálni kívánt Storage-fiókot. 
  * Az **áttekintő** oldal olyan információkat tartalmaz, mint például a fióknév, a tároló és a fájlmegosztás neve. 
      1. A fiókok kulcsai lapon lépjen a **hozzáférési kulcsok** elemre a **Beállítások** ablaktáblán. 
      1. SAS-tokenek esetén a beállítások ablaktáblán válassza a **közös hozzáférésű aláírások** **lehetőséget** .

* Ha a hitelesítéshez a szolgáltatás elvét szeretné használni, lépjen a **Alkalmazásregisztrációk** , és válassza ki a használni kívánt alkalmazást. 
    * A megfelelő **áttekintő** oldal tartalmazni fogja a szükséges információkat, például a bérlő azonosítóját és az ügyfél-azonosítót.

Az alábbi példák bemutatják, hogyan regisztrálhat egy Azure BLOB-tárolót, egy Azure-fájlmegosztást és Azure Data Lake Storage 2. generációját adattárként. A példákban szereplő paraméterek az adattár létrehozásához és regisztrálásához **szükséges paraméterek** . 

Ha más tárolási szolgáltatások adattárolóit szeretné létrehozni, és az ezekhez a módszerekhez nem kötelező paramétereket szeretne használni, tekintse meg a [megfelelő `register_azure_*` módszerek dokumentációját](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Blobtároló

Az Azure Blob-tárolók adattárként való regisztrálásához használja a következőt: [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

A következő kód létrehozza és regisztrálja az `blob_datastore_name` adattárt a `ws` munkaterületen. Ez az adattár hozzáfér a `my-container-name` blob-tárolóhoz a `my-account-name` Storage-fiókban a megadott fiók-hozzáférési kulcs használatával.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```
Ha a blob-tároló virtuális hálózatban található, a paramétert is adja `skip_validation=True` meg a [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) metódusban. 

#### <a name="file-share"></a>Fájlmegosztás

Azure-fájlmegosztás adattárként való regisztrálásához használja a következőt: [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) . 

A következő kód létrehozza és regisztrálja az `file_datastore_name` adattárt a `ws` munkaterületen. Ez az adattár hozzáfér a `my-fileshare-name` fájlmegosztást a `my-account-name` Storage-fiókban a megadott fiók-hozzáférési kulcs használatával.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```
Ha a fájlmegosztás virtuális hálózatban található, a paramétert is adja `skip_validation=True` meg a [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) metódusban. 

#### <a name="azure-data-lake-storage-generation-2"></a>2. generációs Azure Data Lake Storage

Egy Azure Data Lake Storage 2. generációs (ADLS Gen 2) adattár esetében a [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) használatával regisztrálja az Azure DataLake Gen 2 tárolóhoz csatlakoztatott hitelesítő adatokat az [egyszerű szolgáltatás engedélyeivel](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Az egyszerű szolgáltatás használatához [regisztrálnia](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) kell az alkalmazást, és biztosítania kell az egyszerű szolgáltatásnév számára a *Storage blob adatolvasói* hozzáférését. További információ a [2. generációs ADLS-hez beállított hozzáférés-vezérlésről](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Az egyszerű szolgáltatás használatához [regisztrálnia](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) kell az alkalmazást, és a megfelelő adateléréssel kell megadnia az egyszerű szolgáltatásnevet. További információ a [2. generációs ADLS-hez beállított hozzáférés-vezérlésről](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

A következő kód létrehozza és regisztrálja az `adlsgen2_datastore_name` adattárt a `ws` munkaterületen. Ez az adattár hozzáfér a fájlrendszerhez a `test` `account_name` Storage-fiókban a megadott egyszerű szolgáltatás hitelesítő adataival.

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

> [!IMPORTANT]
> Ha a Storage-fiókja egy virtuális hálózatban található, akkor csak [az SDK-n keresztüli](#python-sdk) adattárolók létrehozását támogatja a rendszer. 

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/).
1. A **kezelés** **alatt kattintson a** bal oldali ablaktábla adattárolók elemére.
1. Válassza az **+ új adattár**lehetőséget.
1. Töltse ki az űrlapot egy új adattárhoz. Az űrlap intelligens módon frissül saját maga, az Azure Storage-típus és a hitelesítési típus választása alapján.
  
Itt megtalálhatja azokat az adatokat, amelyekre az űrlapot fel kell töltenie a [Azure Portal](https://portal.azure.com). Válassza ki a **Storage-fiókok** elemet a bal oldali ablaktáblán, és válassza ki a regisztrálni kívánt Storage-fiókot. Az **áttekintő** oldal olyan információkat tartalmaz, mint például a fióknév, a tároló és a fájlmegosztás neve. 

* A hitelesítési elemek, például a fiók kulcsa vagy az SAS-token esetében lépjen a **Beállítások** ablaktábla **hozzáférési kulcsok** elemére. 

* Az egyszerű szolgáltatások, például a bérlői azonosító és az ügyfél-azonosító esetében lépjen a **Alkalmazásregisztrációk** , és válassza ki a használni kívánt alkalmazást. A kapcsolódó **áttekintő** lap ezeket az elemeket fogja tartalmazni. 

Az alábbi példa bemutatja, hogyan néz ki az űrlap az Azure Blob-adattár létrehozásakor: 
    
![Űrlap új adattárhoz](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Adattárolók beolvasása a munkaterületről

Az aktuális munkaterületen regisztrált adott adattár beszerzéséhez használja a [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) statikus metódust az `Datastore` osztályban:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Egy adott munkaterülethez regisztrált adattárolók listájának lekéréséhez használhatja a (z) [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) tulajdonságot egy munkaterület-objektumon:

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
Az alapértelmezett adattár a következő kóddal is módosítható. Ez a képesség csak az SDK-n keresztül támogatott. 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Adatok feltöltése és letöltése

Az [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) alábbi példákban ismertetett és metódusok a és a [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) és a [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) osztályokra vonatkozó, azonos módon működnek.

> [!NOTE]
> Jelenleg nem támogatott a AzureDataLakeGen2-adattárolók feltöltése.

### <a name="upload"></a>Feltöltés

Töltsön fel egy könyvtárat vagy egyedi fájlt az adattárba a Python SDK használatával:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

A `target_path` paraméter adja meg a feltöltendő fájlmegosztás (vagy blob-tároló) helyét. Alapértelmezés szerint `None` az, hogy a rendszer a gyökérbe töltse fel az adatfeldolgozást. Ha a rendszer felülírja a `overwrite=True` meglévő összes beérkező adatelemet `target_path` .

A metódussal feltöltheti az egyes fájlok listáját is az adattárba `upload_files()` .

### <a name="download"></a>Letöltés

Adatok letöltése egy adattárból a helyi fájlrendszerbe:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

A `target_path` paraméter annak a helyi könyvtárnak a helye, ahová le szeretné tölteni az adatfájlt. A letölteni kívánt fájlmegosztás (vagy blob-tároló) mappájának elérési útjának megadásához adja meg a következő elérési utat: `prefix` . Ha `prefix` Ez a érték, a rendszer a `None` fájlmegosztás (vagy blob-tároló) összes tartalmát letölti.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Az adatai elérése a betanítás során

Az adattárolókban lévő adatokkal való kommunikációhoz, illetve az adatoknak a gépi tanulási feladatok, például a képzés, a [Azure Machine learning adatkészletek létrehozásához](how-to-create-register-datasets.md)használatos objektumba való becsomagolásához. Az adatkészletek olyan függvényeket biztosítanak, amelyek táblázatos adatokat töltenek be egy Panda vagy Spark DataFrame. Az adatkészletek lehetővé teszik az Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database és Azure Database for PostgreSQL bármilyen formátumú fájljának letöltését vagy csatlakoztatását is. [További információ az adatkészletek betanításáról](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Forráskód elérése a betanítás során

Az Azure Blob Storage nagyobb átviteli sebességgel rendelkezik, mint egy Azure-fájlmegosztás, és nagy mennyiségű, párhuzamosan elindított feladatra fog méretezni. Ezért javasoljuk, hogy a futtatások konfigurálásával blob Storage-t használjon a forráskód-fájlok átviteléhez.

A következő kódrészlet a futtatási konfigurációt adja meg, amely a blob-adatforgalomhoz használandó blob-adattár.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Az adathozzáférés a pontozás során

A Azure Machine Learning számos módszert kínál a modellek pontozási célra való használatára. A módszerek némelyike nem biztosít hozzáférést az adattárolóhoz. A következő táblázat segítségével megtudhatja, hogy mely módszerek lehetővé teszik az adattárolók elérését a pontozás során:

| Metódus | Adattár-hozzáférés | Leírás |
| ----- | :-----: | ----- |
| [Kötegelt előrejelzés](how-to-use-parallel-run-step.md) | ✔ | Előrejelzések készítése aszinkron módon nagy mennyiségű adattal. |
| [Webszolgáltatás](how-to-deploy-and-where.md) | &nbsp; | Modellek üzembe helyezése webszolgáltatásként. |
| [Azure IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Modellek üzembe helyezése IoT Edge eszközökön. |

Olyan helyzetekben, amikor az SDK nem biztosít hozzáférést az adattárolók számára, létrehozhat egyéni kódokat a megfelelő Azure SDK használatával az adateléréshez. A [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python) például egy ügyféloldali kódtár, amely a blobokban vagy fájlokban tárolt adateléréshez használható.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Az adatáthelyezés támogatott Azure Storage-megoldásokra

Azure Machine Learning támogatja az Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database és Azure Database for PostgreSQL adatainak elérését. Ha nem támogatott tárterületet használ, javasoljuk, hogy [Azure Data Factory és az alábbi lépések](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)segítségével helyezze át az adatait a támogatott Azure Storage-megoldásokra. Az adatforgalom a támogatott tárolóba való áthelyezésével megtakaríthatja a kimenő adatforgalom költségeit a gépi tanulási kísérletek során. 

Azure Data Factory a hatékony és rugalmas adatátvitelt több mint 80 előre összeépített összekötővel biztosítja, felár nélkül. Ezek az összekötők közé tartoznak az Azure adatszolgáltatások, a helyszíni adatforrások, az Amazon S3 és a vöröseltolódás, valamint a Google BigQuery.

## <a name="next-steps"></a>További lépések

* [Azure Machine learning-adatkészlet létrehozása](how-to-create-register-datasets.md)
* [Modell betanítása](how-to-train-ml-models.md)
* [Modell üzembe helyezése](how-to-deploy-and-where.md)
