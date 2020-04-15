---
title: Csatlakozás az Azure-tárolási szolgáltatásokhoz
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogy miként használhatja az adattárakat az Azure Machine Learninggel való betanítás során az Azure Storage-szolgáltatásokhoz való biztonságos csatlakozáshoz
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: ca892b5f360f523ee2b5ff875dfb0707136a5ab5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383445"
---
# <a name="connect-to-azure-storage-services"></a>Csatlakozás az Azure-tárolási szolgáltatásokhoz
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan csatlakozhat az Azure Machine Learning-adattárakon keresztül az Azure Storage-szolgáltatásokhoz. Az adattárak kapcsolati adatokat tárolnak, [Key Vault](https://azure.microsoft.com/services/key-vault/) például az előfizetés-azonosítót és a kulcstárolóban a munkaterülethez társított jogkivonat-engedélyezést, így biztonságosan hozzáférhet a tárhelyhez anélkül, hogy a parancsfájlokban szigorú kódot kellene kötnie.

Ezeket az [Azure-tárolási megoldásokat ezekből](#matrix)az adattárakból hozhatja létre. A nem támogatott tárolási megoldások, valamint az adatforgalom költsége mentése a gépi tanulási kísérletek során, azt javasoljuk, hogy [helyezze át az adatokat](#move) a támogatott Azure tárolási megoldások. 

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:
- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki az [Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

- Azure-tárfiók [Azure blobtárolóval](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) vagy [Azure-fájlmegosztással.](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)

- Az [Azure Machine Learning SDK pythonhoz](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), vagy az Azure Machine Learning [stúdióhoz](https://ml.azure.com/)való hozzáférés.

- Egy Azure Machine Learning-munkaterület.
  
  [Hozzon létre egy Azure Machine Learning-munkaterületet,](how-to-manage-workspace.md) vagy használjon egy meglévőt a Python SDK-n keresztül. Importálja `Workspace` `Datastore` az osztályt és az osztályt, és töltse be az előfizetésadatait a fájlból `config.json` a funkció használatával. `from_config()` Ez alapértelmezés szerint az aktuális könyvtárban keresi a JSON-fájlt, de megadhat `from_config(path="your/file/path")`egy elérési utat is, amely a használatával a fájlra mutat.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Támogatott adattárolási szolgáltatástípusok

Az adattárak jelenleg támogatják a kapcsolatadatok tárolását a következő mátrixban felsorolt tárolási szolgáltatásokhoz.

| Tároló&nbsp;típusa | Hitelesítés&nbsp;típusa | [Azure&nbsp;&nbsp;Machine Learning stúdió](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;Machine&nbsp;Learning CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Rest API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure&nbsp;Blob&nbsp;Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Fiókkulcs <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure&nbsp;&nbsp;fájlmegosztás](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Fiókkulcs <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data&nbsp;Lake&nbsp;Storage Gen 1](https://docs.microsoft.com/azure/data-lake-store/)| Szolgáltatásnév| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data&nbsp;Lake&nbsp;storage gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Szolgáltatásnév| ✓ | ✓ | ✓ |✓
[Azure&nbsp;&nbsp;SQL-adatbázis](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL-hitelesítés <br>Szolgáltatásnév| ✓ | ✓ | ✓ |✓
[Az&nbsp;Azure PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | SQL-hitelesítés| ✓ | ✓ | ✓ |✓
[Azure-adatbázis&nbsp;&nbsp;a MySQL-hez&nbsp;](https://docs.microsoft.com/azure/mysql/overview) | SQL-hitelesítés|  | ✓* | ✓* |✓*
[Databricks&nbsp;&nbsp;fájlrendszer](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Nincs hitelesítés | | ✓** | ✓ ** |✓** 

*A MySQL csak a [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)csővezetékhez támogatott. <br>
**A Databricks csak a [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) csővezetéken támogatott

### <a name="storage-guidance"></a>Storage – útmutató

Javasoljuk, hogy hozzon létre egy adattár egy [Azure Blob-tároló.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) A normál és a prémium szintű tárhely is elérhető a blobok. Bár a prémium szintű tárolás drágább, a gyorsabb átviteli sebesség javíthatja a betanítási futtatások sebességét, különösen akkor, ha nagy adatkészleten keresztül edz. A tárfiókok költségeiről az [Azure díjszabási kalkulátora](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)szolgál.

[Az Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) az Azure Blob storage-ra épül, és nagyvállalati big data-elemzésre lett tervezve. A Data Lake Storage Gen2 alapvető része egy [hierarchikus névtér](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) hozzáadása a Blob storage-hoz. A hierarchikus névtér az objektumokat/fájlokat könyvtárak hierarchiájába rendezi a hatékony adathozzáférés érdekében.

Amikor létrehoz egy munkaterületet, egy Azure blob-tároló és egy Azure-fájlmegosztás automatikusan regisztrálva lesz a munkaterületre. A neve, `workspaceblobstore` `workspacefilestore`illetve a neve. `workspaceblobstore`a munkaterületi összetevők és a gépi tanulási kísérletnaplói tárolására szolgál. `workspacefilestore`[a számítási példányon](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)keresztül engedélyezett jegyzetfüzetek és R-parancsfájlok tárolására szolgál. A `workspaceblobstore` tároló alapértelmezett adattárként van beállítva.

> [!IMPORTANT]
> Az Azure Machine Learning tervezője (előzetes verzió) létrehoz egy **azureml_globaldatasets** nevű adattalapot, amikor megnyit egy mintát a tervező kezdőlapján. Ez az adattár csak mintaadatkészleteket tartalmaz. **Kérjük, ne** használja ezt az adattáratot bizalmas adathozzáférésre.
> ![Automatikusan létrehozott adattár tervezői mintaadatkészletekhez](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Adattárak létrehozása és regisztrálása

Amikor egy Azure-tárolási megoldást regisztrál adattárként, automatikusan létrehozza és regisztrálja az adattatárolót egy adott munkaterületre. A Python SDK vagy az Azure Machine Learning studio használatával hozhat létre és regisztrálhat adattárakat egy munkaterületre.

>[!IMPORTANT]
> A kezdeti adattár létrehozása és regisztrálása folyamat részeként az Azure Machine Learning ellenőrzi, hogy az alapul szolgáló tárolási szolgáltatás létezik-e, és hogy a felhasználó által megadott egyszerű (felhasználónév, egyszerű szolgáltatás vagy SAS-jogkivonat) rendelkezik-e hozzáféréssel az adott tárterülethez. Az Azure Data Lake Storage Gen 1 és 2 adattárak, azonban [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) ez az érvényesítés később történik, amikor az adatelérési módszerek, mint vagy hívják. 
<br><br>
Az adattár létrehozása után ez az ellenőrzés csak olyan metódusok esetében történik, amelyek hozzáférést igényelnek az alapul szolgáló tárolótárolóhoz, **nem** minden alkalommal, amikor az adattár-objektumok lekérése történik. Érvényesítés történik például, ha fájlokat szeretne letölteni az adattárból; de ha csak meg szeretné változtatni az alapértelmezett adattár, akkor érvényesítés nem történik meg.

### <a name="python-sdk"></a>Python SDK

Az összes regisztermódszer az [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) `register_azure_*`osztályon található, és a formát kell megformálni.

A metódus feltöltéséhez szükséges információkat az `register()` Azure [Portalon](https://portal.azure.com)találja.
Válassza **a storage fiókok** a bal oldali ablaktáblában, és válassza ki a regisztrálni kívánt tárfiókot. Az **Áttekintés** lap olyan információkat tartalmaz, mint például a fiók neve, a tároló és a fájlmegosztás neve. 

* Hitelesítési elemek, például fiókkulcs vagy SAS-jogkivonat esetén nyissa meg az **Access-kulcsok lapot** a **Beállítások** ablaktáblán. 

* Az egyszerű szolgáltatáscikkek, például a bérlői azonosító és az ügyfélazonosító, nyissa meg az **alkalmazásregisztrációkat,** és válassza ki, hogy melyik alkalmazást szeretné használni. A megfelelő áttekintés oldal tartalmazza ezeket **az** elemeket.

> [!IMPORTANT]
> Ha a tárfiók egy virtuális hálózatban van, csak a Blob, a Fájlmegosztás, az ADLS Gen 1 és az ADLS Gen 2 adattárak létrehozása támogatott **az SDK-n keresztül.** Ha hozzáférést szeretne adni a munkaterületnek a `grant_workspace_access` `True`tárfiókhoz, állítsa a paramétert a beállításra.

Az alábbi példák bemutatják, hogyan regisztrálhat egy Azure blob-tárolót, egy Azure-fájlmegosztást és az Azure Data Lake Storage Generation 2 adattárként. Egyéb tárolási szolgáltatások esetében olvassa el [a `register_azure_*` vonatkozó módszerek referenciadokumentációját.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)

#### <a name="blob-container"></a>Blobtároló

Ha egy Azure blob-tárolót szeretne [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)adattárként regisztrálni, használja a használatát.

A következő kód létrehozza `blob_datastore_name` és regisztrálja `ws` az adattatárolót a munkaterületre. Ez az adattár `my-container-name` a megadott `my-account-name` fiók hozzáférési kulcs használatával éri el a blob-tárolót a tárfiókban.

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

#### <a name="file-share"></a>Fájlmegosztás

Ha egy Azure-fájlmegosztást adattárként szeretne regisztrálni, használja a használatát. [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) 

A következő kód létrehozza `file_datastore_name` és regisztrálja `ws` az adattatárolót a munkaterületre. Ez az `my-fileshare-name` adattár a megadott `my-account-name` fiókhozzáférési kulcs használatával fér hozzá a tárfiók fájlmegosztásához.

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

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake storage 2.

Az Azure Data Lake Storage Generation 2 (ADLS Gen 2) adattár ban [a register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) használatával regisztrálhat egy azure DataLake Gen 2-tárolóhoz csatlakoztatott hitelesítő adattárszolgáltatást, [amely alapvető engedélyekkel rendelkezik.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) Annak érdekében, hogy kihasználja a szolgáltatás egyszerű regisztrálnia kell [az alkalmazást,](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) és megadja a szolgáltatásnév a megfelelő adathozzáférés. További információ az [ADLS Gen 2-hez beállított hozzáférés-vezérlésről.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) 

A következő kód létrehozza `adlsgen2_datastore_name` és regisztrálja `ws` az adattatárolót a munkaterületre. Ez az adattár a `test` tárfiók ban lévő `account_name` fájlrendszerhez fér hozzá a megadott egyszerű szolgáltatáshitelesítő adatok használatával.

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

Hozzon létre egy új adattaboltot néhány lépésben az Azure Machine Learning stúdióban:

> [!IMPORTANT]
> Ha a tárfiók egy virtuális hálózatban van, csak [az sdk-n keresztüli](#python-sdk) adattárak létrehozása támogatott. 

1. Jelentkezzen be az [Azure Machine Learning stúdióba.](https://ml.azure.com/)
1. Válassza az **Adattárak** elemet a bal oldali ablaktáblában a **Kezelés csoportban.**
1. Válassza **a + Új adattár lehetőséget.**
1. Töltse ki az új adattár űrlapát. Az űrlap intelligensmódon frissíti magát az Azure-tárhely típusának és hitelesítési típusának kiválasztása alapján.
  
Az űrlap feltöltéséhez szükséges információkat az Azure [Portalon](https://portal.azure.com)találja. Válassza **a storage fiókok** a bal oldali ablaktáblában, és válassza ki a regisztrálni kívánt tárfiókot. Az **Áttekintés** lap olyan információkat tartalmaz, mint például a fiók neve, a tároló és a fájlmegosztás neve. 

* Hitelesítési elemek, például fiókkulcs vagy SAS-jogkivonat esetén nyissa meg az **Access-kulcsok lapot** a **Beállítások** ablaktáblán. 

* Az egyszerű szolgáltatáscikkek, például a bérlői azonosító és az ügyfélazonosító, nyissa meg az **alkalmazásregisztrációkat,** és válassza ki, hogy melyik alkalmazást szeretné használni. A megfelelő áttekintés oldal tartalmazza ezeket **az** elemeket. 

A következő példa bemutatja, hogyan néz ki az űrlap, amikor létrehoz egy Azure blob adattára: 
    
![Új adattár űrlapja](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Adattárak beszereznie a munkaterületről

Ha egy adott adattamozót szeretne [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) regisztrálni az `Datastore` aktuális munkaterületen, használja a statikus metódust az osztályon:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Egy adott munkaterülethez regisztrált adattárak listájának leéséhez használja a [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) tulajdonságot egy munkaterület-objektumon:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

A munkaterület alapértelmezett adattárának leéséhez használja ezt a sort:

```Python
datastore = ws.get_default_datastore()
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Adatok feltöltése és letöltése

Az [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) alábbi példákban ismertetett módszerek az [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) és az [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) osztályokra jellemzőek, és azonos módon működnek.

### <a name="upload"></a>Feltöltés

Töltsön fel egy könyvtárat vagy az egyes fájlokat az adattárba a Python SDK használatával:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

A `target_path` paraméter megadja a feltöltendő fájlmegosztás (vagy blobtároló) helyét. Alapértelmezés szerint `None`a , így az adatok feltöltése a gyökérbe. Ha `overwrite=True`a rendszer `target_path` felülírja a meglévő adatokat.

Az egyes fájlok listáját is feltöltheti az `upload_files()` adattárba a módszersegítségével.

### <a name="download"></a>Letöltés

Adatok letöltése adattárból a helyi fájlrendszerbe:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

A `target_path` paraméter annak a helyi könyvtárnak a helye, amelybe az adatokat le szeretné tölteni. Ha meg szeretné adni a fájlmegosztásban (vagy blobtárolóban) lévő `prefix`mappáelérési utat, adja meg a kívánt elérési utat. Ha `prefix` `None`a , a fájlmegosztás (vagy blob tároló) teljes tartalma le töltődik.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Az adatok elérése a betanítás során

Az adattárakban lévő adatok kal való interakcióhoz vagy az adatok gépi tanulási feladatokhoz , például betanításhoz egy fogyóeszköz-objektumba való [csomagolásához hozzon létre egy Azure Machine Learning-adatkészletet.](how-to-create-register-datasets.md) Az adatkészletek olyan funkciókat biztosítanak, amelyek táblázatos adatokat töltenek be pandákba vagy Spark DataFrame-be. Az adatkészletek lehetővé teszik bármilyen formátumú fájlok letöltését vagy csatlakoztatását az Azure Blob storage, az Azure Files, az Azure Data Lake Storage Gen1, az Azure Data Lake Storage Gen2, az Azure SQL Database és az Azure Database for PostgreSQL szolgáltatásból. [További információ az adatkészletek betanításáról.](how-to-train-with-datasets.md)

### <a name="accessing-source-code-during-training"></a>A forráskód elérése a betanítás során

Az Azure Blob storage nagyobb átviteli sebesség, mint egy Azure-fájlmegosztás, és a párhuzamosan elindított feladatok nagy számára méretezhető. Ezért azt javasoljuk, hogy konfigurálja a fut a Blob storage forráskód fájlok átviteléhez.

A következő kódpélda határozza meg a futtatási konfiguráció, amely blob adattár ban a forráskód átvitele.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Hozzáférés az adatokhoz a pontozás során

Az Azure Machine Learning számos lehetőséget kínál a modellek pontozási használatára. Néhány ilyen módszer nem biztosít hozzáférést az adattárakhoz. Az alábbi táblázat segítségével megismerheti, hogy mely módszerek teszik lehetővé az adattárak elérését a pontozás során:

| Módszer | Adattár-hozzáférés | Leírás |
| ----- | :-----: | ----- |
| [Kötegelt előrejelzés](how-to-use-parallel-run-step.md) | ✔ | Előrejelzéseket készíthet nagy mennyiségű adatról aszinkron módon. |
| [Webszolgáltatás](how-to-deploy-and-where.md) | &nbsp; | Modellek telepítése webszolgáltatásként. |
| [Azure IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Modellek üzembe helyezése IoT Edge-eszközökre. |

Olyan helyzetekben, amikor az SDK nem biztosít hozzáférést az adattárakhoz, előfordulhat, hogy egyéni kódot hozhat létre a megfelelő Azure SDK használatával az adatok eléréséhez. Például az [Azure Storage SDK python](https://github.com/Azure/azure-storage-python) egy ügyfélkódtár, amely segítségével hozzáférhet a blobokban vagy fájlokban tárolt adatok.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Adatok áthelyezése támogatott Azure-tárolási megoldásokba

Az Azure Machine Learning támogatja az Azure Blob storage, az Azure Files, az Azure Data Lake Storage Gen1, az Azure Data Lake Storage Gen2, az Azure SQL Database és az Azure Database for PostgreSQL adatainak elérését. Ha nem támogatott tárhelyet használ, azt javasoljuk, hogy az Azure [Data Factory és ezek](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)a lépések használatával helyezze át az adatokat a támogatott Azure storage-megoldásokra. Az adatok támogatott tárolóba való áthelyezésével a gépi tanulási kísérletek során megtakaríthatja az adatforgalom költségeit. 

Az Azure Data Factory hatékony és rugalmas adatátvitelt biztosít több mint 80 előre összeállított összekötővel, további költségek nélkül. Ezek az összekötők közé tartozik az Azure-adatszolgáltatások, a helyszíni adatforrások, az Amazon S3 és a Redshift, valamint a Google BigQuery.

## <a name="next-steps"></a>További lépések

* [Azure-beli gépi tanulási adatkészlet létrehozása](how-to-create-register-datasets.md)
* [Modell betanítása](how-to-train-ml-models.md)
* [Modell üzembe helyezése](how-to-deploy-and-where.md)
