---
title: Csatlakozás Azure Storage-szolgáltatásokhoz
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az adattárakat az Azure Storage-szolgáltatásokhoz való biztonságos kapcsolódásra Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 07/22/2020
ms.custom: how-to, contperfq1, devx-track-python
ms.openlocfilehash: 08685a6ebfcbfced91c3685635c40ff48030fe38
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89669577"
---
# <a name="connect-to-azure-storage-services"></a>Csatlakozás Azure Storage-szolgáltatásokhoz
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan **csatlakozhat az Azure Storage-szolgáltatásokhoz Azure Machine learning adattáron keresztül**. Az adattárolók biztonságosan csatlakoznak az Azure Storage szolgáltatáshoz a hitelesítő adatok és az eredeti adatforrások integritásának veszélyeztetése nélkül. A kapcsolati adatokat, például az előfizetési AZONOSÍTÓját és a jogkivonat-engedélyezést a munkaterülethez társított [Key Vault](https://azure.microsoft.com/services/key-vault/) tárolják, így biztonságosan hozzáférhet a tárolóhoz anélkül, hogy a parancsfájlokban rögzített kódokat kellene megtennie. Az adattárolók létrehozásához és regisztrálásához használhatja a [Azure Machine learning PYTHON SDK](#python) -t vagy a [Azure Machine learning Studio alkalmazást](#studio) .

Ha az adattárolókat a Azure Machine Learning VS Code bővítménnyel szeretné létrehozni és kezelni, További tudnivalókért tekintse meg a [vs Code erőforrás-kezelési útmutatóját](how-to-manage-resources-vscode.md#datastores) .

[Ezekből az Azure Storage-megoldásokból is létrehozhat adattárakat](#matrix). A nem **támogatott tárolási megoldások esetében**, valamint az adatforgalomnak a ml-kísérletek során történő megtakarítása érdekében [Helyezze át az adatait](#move) egy támogatott Azure Storage-megoldásba.  

Ha szeretné megismerni, hogy az adattárolók hogyan illeszkednek Azure Machine Learning összesített adatelérési munkafolyamataihoz, tekintse meg a [biztonságos hozzáférésről](concept-data.md#data-workflow) szóló cikket.

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:
- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

- Egy [támogatott tárolási típusú](#matrix)Azure Storage-fiók.

- A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true), vagy a [Azure Machine learning studióhoz](https://ml.azure.com/)való hozzáférés.

- Egy Azure Machine Learning-munkaterület.
  
  [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md) , vagy használjon egy meglévőt a Python SDK-n keresztül. 

    Importálja a `Workspace` és az `Datastore` osztályt, és töltse be az előfizetési adatokat a fájlból `config.json` a függvény használatával `from_config()` . Ez alapértelmezés szerint az aktuális könyvtárban keresi a JSON-fájlt, de megadhat egy elérésiút-paramétert is, amely a fájlra mutat `from_config(path="your/file/path")` .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    Amikor létrehoz egy munkaterületet, az Azure Blob-tároló és az Azure-fájlmegosztás automatikusan adattárként lesz regisztrálva a munkaterületen. Ezek neve `workspaceblobstore` és `workspacefilestore` , illetve. A a `workspaceblobstore` munkaterület-összetevők és a Machine learning-kísérletek naplófájljainak tárolására szolgál. Az **alapértelmezett adattárként** is be van állítva, és nem törölhető a munkaterületről. A a `workspacefilestore` [számítási példányon](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)keresztül engedélyezve lévő jegyzetfüzetek és R-parancsfájlok tárolására szolgál.
    
    > [!NOTE]
    > A Azure Machine Learning Designer (előzetes verzió) automatikusan létrehoz egy **azureml_globaldatasets** nevű adattárat, amikor megnyit egy mintát a tervező kezdőlapján. Ez az adattár csak minta adatkészleteket tartalmaz. A bizalmas adatokhoz való hozzáféréshez **ne** használja ezt az adattárt.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Támogatott adattárolási szolgáltatások típusai

Az adattárolók jelenleg támogatják a kapcsolódási adatok tárolását a következő mátrixban felsorolt tárolási szolgáltatásokhoz.

| Tárolási &nbsp; típus | Hitelesítés &nbsp; típusa | [Azure &nbsp; Machine &nbsp; learning Studio](https://ml.azure.com/) | [Az Azure &nbsp; Machine &nbsp; learning &nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) |  [Azure &nbsp; Machine &nbsp; learning parancssori felület](reference-azure-machine-learning-cli.md) | [Azure &nbsp; Machine &nbsp; learning – &nbsp; REST API](https://docs.microsoft.com/rest/api/azureml/) | VS Code
---|---|---|---|---|---|---
[Azure &nbsp; blob &nbsp; Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Fiókkulcs <br> SAS-jogkivonat | ✓ | ✓ | ✓ |✓ |✓
[Azure- &nbsp; fájlmegosztás &nbsp;](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Fiókkulcs <br> SAS-jogkivonat | ✓ | ✓ | ✓ |✓|✓
[Azure &nbsp; Data Lake &nbsp; Storage, &nbsp; 1. generációs](https://docs.microsoft.com/azure/data-lake-store/)| Szolgáltatásnév| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; Data Lake &nbsp; Storage, &nbsp; 2. generációs](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Szolgáltatásnév| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; SQL &nbsp; Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL-hitelesítés <br>Szolgáltatásnév| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | SQL-hitelesítés| ✓ | ✓ | ✓ |✓|
[MySQL-hez készült Azure- &nbsp; adatbázis &nbsp; &nbsp;](https://docs.microsoft.com/azure/mysql/overview) | SQL-hitelesítés|  | ✓* | ✓* |✓*|
[Databricks &nbsp; fájlrendszer &nbsp;](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Nincs hitelesítés | | ✓** | ✓ ** |✓** |

\*A MySQL-t csak a folyamat [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py&preserve-view=true) támogatja<br />
\*\*A Databricks csak a folyamat [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py&preserve-view=true) támogatott


### <a name="storage-guidance"></a>Storage – útmutató

Javasoljuk, hogy hozzon létre egy adattárt egy [Azure Blob-tárolóhoz](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Mind a standard, mind a Premium Storage elérhető a Blobok számára. Habár a Premium Storage drágább, a gyorsabb átviteli sebesség növelheti a képzések sebességét, különösen akkor, ha egy nagyméretű adathalmazra van betanítva. További információ a Storage-fiókok költségéről: az [Azure díjszabási kalkulátora](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) az Azure Blob Storage-ra épül, és nagyvállalati Big Data elemzésre lett tervezve. Data Lake Storage Gen2 alapvető része egy [hierarchikus névtér](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) hozzáadása a blob Storage-hoz. A hierarchikus névtér objektumokat vagy fájlokat rendez a címtárak hierarchiájában a hatékony adateléréshez.

## <a name="storage-access-and-permissions"></a>Tárterület-hozzáférés és engedélyek

Az Azure Storage szolgáltatáshoz való biztonságos kapcsolódás biztosításához Azure Machine Learning megköveteli, hogy rendelkezzen a megfelelő adattároló-tároló eléréséhez szükséges engedéllyel. Ez a hozzáférés az adattár regisztrálásához használt hitelesítési hitelesítő adatoktól függ. 

### <a name="virtual-network"></a>Virtuális hálózat 

Ha az adattároló-fiók egy **virtuális hálózaton**található, további konfigurációs lépések szükségesek ahhoz, hogy Azure Machine learning hozzáférjen az adataihoz. Tekintse meg a [Azure Machine learning Studio használata Azure-beli virtuális hálózatban](how-to-enable-studio-virtual-network.md) című témakört, amely biztosítja, hogy a megfelelő konfigurációs lépések érvényesüljenek az adattár létrehozásakor és regisztrálása során.  

### <a name="access-validation"></a>Hozzáférés ellenőrzése

**A kezdeti adattár létrehozása és regisztrálása során**Azure Machine learning automatikusan ellenőrzi, hogy a mögöttes tárolási szolgáltatás létezik-e, és hogy a felhasználó által megadott rendszerbiztonsági tag (Felhasználónév, szolgáltatásnév vagy sas-jogkivonat) hozzáfér-e a megadott tárolóhoz.

Az **adattár létrehozása után**ezt az ellenőrzést csak olyan metódusok hajtják végre, amelyek hozzáférést igényelnek a mögöttes tárolóhoz, **nem** minden alkalommal, amikor az adattár-objektumokat lekérik. Például az ellenőrzés akkor történik meg, ha le szeretné tölteni a fájlokat az adattárból. Ha azonban csak módosítani szeretné az alapértelmezett adattárat, akkor az érvényesítés nem történik meg.

A mögöttes tárolási szolgáltatáshoz való hozzáférésének hitelesítéséhez megadhatja a fiók kulcsát, a közös hozzáférési aláírások (SAS) jogkivonatait vagy a szolgáltatásnevet a `register_azure_*()` létrehozni kívánt adattár-típus megfelelő metódusában. A [tárolási típus mátrixa](#matrix) felsorolja az egyes adattár-típusoknak megfelelő támogatott hitelesítési típusokat.

Megtalálhatja a fiók kulcsát, az SAS-tokent és az egyszerű szolgáltatásnév adatait a [Azure Portal](https://portal.azure.com).

* Ha a hitelesítéshez fiókot vagy SAS-tokent szeretne használni, válassza ki a **Storage-fiókok** lehetőséget a bal oldali ablaktáblán, és válassza ki a regisztrálni kívánt Storage-fiókot. 
  * Az **áttekintő** oldal olyan információkat tartalmaz, mint például a fióknév, a tároló és a fájlmegosztás neve. 
      1. A fiókok kulcsai lapon lépjen a **hozzáférési kulcsok** elemre a **Beállítások** ablaktáblán. 
      1. SAS-tokenek esetén a beállítások ablaktáblán válassza a **közös hozzáférésű aláírások** **lehetőséget** .

* Ha egy egyszerű szolgáltatásnév használatát tervezi hitelesítésre, lépjen a **Alkalmazásregisztrációk** , és válassza ki a használni kívánt alkalmazást. 
    * A megfelelő **áttekintő** oldal tartalmazni fogja a szükséges információkat, például a bérlő azonosítóját és az ügyfél-azonosítót.

> [!IMPORTANT]
> Biztonsági okokból előfordulhat, hogy módosítania kell az Azure Storage-fiókhoz tartozó hozzáférési kulcsokat (a fiók kulcsát vagy SAS-tokenjét). Ha ezt teszi, ügyeljen arra, hogy szinkronizálja az új hitelesítő adatokat a munkaterülettel és a hozzá csatlakoztatott adattárolókkal. Útmutató [a frissített hitelesítő adatok szinkronizálásához](how-to-change-storage-access-key.md). 

### <a name="permissions"></a>Engedélyek

Az Azure Blob Container és Azure Data Lake 2. generációs tárolók esetében ellenőrizze, hogy a hitelesítési hitelesítő adatai rendelkeznek-e **Storage blob-Adatolvasóval** . További információ a [Storage blob-Adatolvasóról](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader). 

<a name="python"></a>

## <a name="create-and-register-datastores-via-the-sdk"></a>Adattárolók létrehozása és regisztrálása az SDK-n keresztül

Ha az Azure Storage-megoldást adattárként regisztrálja, automatikusan létrehozza és regisztrálja az adattárt egy adott munkaterületen. Tekintse át a [tárolási hozzáférési & engedélyek](#storage-access-and-permissions) szakaszt a virtuális hálózati forgatókönyvekkel kapcsolatos útmutatásért és a szükséges hitelesítő adatok megkereséséhez. 

Ebben a szakaszban példákat talál egy adattár létrehozására és regisztrálására a Python SDK-val a következő tárolási típusoknál. A példákban szereplő paraméterek az adattár létrehozásához és regisztrálásához **szükséges paraméterek** .

* [Azure Blob-tároló](#azure-blob-container)
* [Azure-fájlmegosztás](#azure-file-share)
* [2. generációs Azure Data Lake Storage](#azure-data-lake-storage-generation-2)

 Más támogatott tárolási szolgáltatásokhoz tartozó adattárolók létrehozásához tekintse meg a [megfelelő `register_azure_*` módszerek dokumentációját](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#&preserve-view=truemethods).

Ha alacsony kódú élményt szeretne, tekintse meg az adattárolók [létrehozása Azure Machine learning Studióban](#studio)című témakört.

> [!NOTE]
> Az adattár neve csak kisbetűkből, számokból és aláhúzásokból állhat. 

### <a name="azure-blob-container"></a>Azure Blob-tároló

Az Azure Blob-tárolók adattárként való regisztrálásához használja a következőt: [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

A következő kód létrehozza és regisztrálja az `blob_datastore_name` adattárt a `ws` munkaterületen. Ez az adattár hozzáfér a `my-container-name` blob-tárolóhoz a `my-account-name` Storage-fiókban a megadott fiók-hozzáférési kulcs használatával. Tekintse át a [tárolási hozzáférési & engedélyek](#storage-access-and-permissions) szakaszt a virtuális hálózati forgatókönyvekkel kapcsolatos útmutatásért és a szükséges hitelesítő adatok megkereséséhez. 

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

### <a name="azure-file-share"></a>Azure-fájlmegosztás

Azure-fájlmegosztás adattárként való regisztrálásához használja a következőt: [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#&preserve-view=trueregister-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) . 

A következő kód létrehozza és regisztrálja az `file_datastore_name` adattárt a `ws` munkaterületen. Ez az adattár hozzáfér a `my-fileshare-name` fájlmegosztást a `my-account-name` Storage-fiókban a megadott fiók-hozzáférési kulcs használatával. Tekintse át a [tárolási hozzáférési & engedélyek](#storage-access-and-permissions) szakaszt a virtuális hálózati forgatókönyvekkel kapcsolatos útmutatásért és a szükséges hitelesítő adatok megkereséséhez. 

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

### <a name="azure-data-lake-storage-generation-2"></a>2. generációs Azure Data Lake Storage

Egy Azure Data Lake Storage 2. generációs (ADLS Gen 2) adattár esetében a [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) használatával regisztrálja az Azure DataLake Gen 2 tárolóhoz csatlakoztatott hitelesítő adatokat az [egyszerű szolgáltatás engedélyeivel](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).  

Az egyszerű szolgáltatás használatához [regisztrálnia](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) kell az alkalmazást, és biztosítania kell az egyszerű szolgáltatásnév számára a **Storage blob adatolvasói** hozzáférését. További információ a [2. generációs ADLS-hez beállított hozzáférés-vezérlésről](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

A következő kód létrehozza és regisztrálja az `adlsgen2_datastore_name` adattárt a `ws` munkaterületen. Ez az adattár hozzáfér a fájlrendszerhez a `test` `account_name` Storage-fiókban a megadott egyszerű szolgáltatás hitelesítő adataival. Tekintse át a [tárolási hozzáférési & engedélyek](#storage-access-and-permissions) szakaszt a virtuális hálózati forgatókönyvekkel kapcsolatos útmutatásért és a szükséges hitelesítő adatok megkereséséhez. 

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

<a name="studio"></a>


## <a name="create-datastores-in-the-studio"></a>Adattárolók létrehozása a Studióban 

Hozzon létre egy új adattárt néhány lépésben a Azure Machine Learning Studióval.

> [!IMPORTANT]
> Ha az adattároló-fiók egy virtuális hálózaton található, további konfigurációs lépések szükségesek ahhoz, hogy a Studio hozzáférhessen az adataihoz. A megfelelő konfigurációs lépések megtételéhez tekintse meg a [Azure Machine learning Studio használata Azure-beli virtuális hálózatban](how-to-enable-studio-virtual-network.md) című témakört. 

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/).
1. A **kezelés** **alatt kattintson a** bal oldali ablaktábla adattárolók elemére.
1. Válassza az **+ új adattár**lehetőséget.
1. Töltse ki az űrlapot egy új adattárhoz. Az űrlap intelligens módon frissül saját maga, az Azure Storage-típus és a hitelesítési típus választása alapján. Az űrlap feltöltéséhez szükséges hitelesítő adatok megkereséséhez tekintse meg a [tárolási hozzáférés és engedélyek című szakaszt](#access-validation) .

Az alábbi példa bemutatja, hogyan néz ki az űrlap az **Azure Blob-adattár**létrehozásakor: 
    
![Űrlap új adattárhoz](media/how-to-access-data/new-datastore-form.png)

<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Adattárolók használata

Az adattár létrehozása után [hozzon létre egy Azure Machine learning adatkészletet](how-to-create-register-datasets.md) az adataival való interakcióhoz. Az adatkészletek a gépi tanulási feladatokhoz, például a képzéshez egy lustán kiértékelt adatforrásba csomagolják az adatokat. Emellett lehetővé teszik az Azure Storage-szolgáltatásokból, például az Azure Blob Storage-ból és a ADLS-ből származó bármilyen formátumú fájlok [letöltését vagy csatlakoztatását](how-to-train-with-datasets.md#mount-vs-download) . A táblázatos adatokat egy Panda vagy Spark DataFrame is betöltheti.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Adattárolók beolvasása a munkaterületről

Az aktuális munkaterületen regisztrált adott adattár beszerzéséhez használja a [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#&preserve-view=trueget-workspace--datastore-name-) statikus metódust az `Datastore` osztályban:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Egy adott munkaterülethez regisztrált adattárolók listájának lekéréséhez használhatja a (z) [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#&preserve-view=truedatastores) tulajdonságot egy munkaterület-objektumon:

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

## <a name="access-data-during-scoring"></a>Az adathozzáférés a pontozás során

A Azure Machine Learning számos módszert kínál a modellek pontozási célra való használatára. A módszerek némelyike nem biztosít hozzáférést az adattárolóhoz. A következő táblázat segítségével megtudhatja, hogy mely módszerek lehetővé teszik az adattárolók elérését a pontozás során:

| Metódus | Adattár-hozzáférés | Description |
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
