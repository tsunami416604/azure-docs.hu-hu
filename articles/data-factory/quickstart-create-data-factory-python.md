---
title: "Azure-beli adat-előállító létrehozása a Python használatával | Microsoft Docs"
description: "Létrehozhat egy Azure-beli adat-előállítót az adatok egy Azure Blob Storage egyik helyéről egy másik helyére történő másolásához."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9c6f7c8e052aed2ab5aba2ee54065bf40e81a022
ms.contentlocale: hu-hu
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-data-factory-and-pipeline-using-python"></a>Adat-előállító és folyamat létrehozása a Python használatával
Az Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás. Lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek alkalmasak az adatok átvitelének és átalakításának irányítására és automatizálására. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok, feldolgozhatók és átalakíthatók az adatok különböző számítási szolgáltatások használatával (pl.: Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning), és a kimeneti adatok közzétehetők olyan adattárakban, mint az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket. 

Ez a rövid útmutató bemutatja, hogyan használható a Python egy Azure-beli adat-előállító létrehozásához. Az adat-előállító folyamata adatokat másol az Azure Blob Storage egyik mappájából egy másik mappába.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

* **Azure Storage-fiók** A blobtároló **forrás-** és **fogadó**adattárként lesz használatban. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. 
* **Egy alkalmazás létrehozása az Azure Active Directoryban** [ennek az útmutatónak](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) a lépéseit követve. Jegyezze fel a következő értékeket, amelyeket a későbbi lépésekben fog használni: **alkalmazásazonosító**, **hitelesítési kulcs** és **bérlőazonosító**. Rendelje hozzá az alkalmazást a **Közreműködő** szerepkörhöz az ugyanebben a cikkben található utasításokat követve. 

### <a name="create-and-upload-an-input-file"></a>Bemeneti fájl létrehozása és feltöltése

1. Indítsa el a Jegyzettömböt. Másolja be az alábbi szöveget, és mentse egy **input.txt** nevű fájlként a lemezen.
    
    ```
    John|Doe
    Jane|Doe
    ```
2.  Az [Azure Storage Explorer](http://storageexplorer.com/) vagy egy hasonló eszköz használatával hozza létre az **adfv2tutorial** tárolót, és töltse fel az input.txt fájlt a tárolóba. 

## <a name="install-the-python-package"></a>Python-csomag telepítése
1. Nyisson meg egy terminált vagy parancssort rendszergazdai jogosultságokkal.  
2. A Python-csomag a Data Factoryhoz történő telepítéséhez futtassa az alábbi parancsot:

    ```
    pip install azure-mgmt-datafactory
    ```

    A [Python SDK for Data Factory](https://github.com/Azure/azure-sdk-for-python) a Python 2.7-es, 3.3-as, 3.4-es, 3.5-ös és 3.6-os verziójának használatát támogatja.
## <a name="create-a-data-factory-client"></a>Adat-előállító ügyfél létrehozása

1. Hozzon létre egy **datafactory.py** nevű fájlt. Adja hozzá az alábbi utasításokat, hogy a névterekre mutató hivatkozásokat tudjon felvenni.
    
    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from msrestazure.azure_cloud import Cloud, CloudEndpoints, CloudSuffixes
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    import json
    import time    
    ```
2. Adja hozzá az alábbi függvényeket az adatok megjelenítéséhez. 

    ```python
    def print_item(group):
      """Print an Azure object instance."""
      print("\tName: {}".format(group.name))
      print("\tId: {}".format(group.id))
      if hasattr(group, 'location'):
          print("\tLocation: {}".format(group.location))
      if hasattr(group, 'tags'):
          print("\tTags: {}".format(group.tags))
      if hasattr(group, 'properties'):
          print_properties(group.properties)
    
    def print_properties(props):
      """Print a ResourceGroup properties instance."""
      if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
          print("\tProperties:")
          print("\t\tProvisioning State: {}".format(props.provisioning_state))
      print("\n\n")    
    ```
3. Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy DataPipelineManagementClient osztályú példányt. Ezzel az objektummal adat-előállítót, társított szolgáltatást, adatkészleteket és folyamatot hozhat létre. Ezenfelül ez az objektum a folyamat futása részleteinek monitorozására is használható.

    ```python   
    subscription_id = '<your subscription ID where the factory resides>'
    credentials = ServicePrincipalCredentials(
            client_id='<yourClientId>',
            secret='<YourPassword>',
            tenant='<YourTenandId>'
    )
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)
    
    rg_params = {'location':'eastus'}
    df_params = {'location':'eastus'}
    rg_name = '<Your Resource Group Name>'
    df_name = '<Your Data Factory Name>'
    ```

## <a name="create-a-data-factory"></a>Data factory létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza az **adat-előállítót**. 

```python
#Create Resource Group
resource_client.resource_groups.create_or_update(rg_name, rg_params)

#Create Data Factory
df_resource = Factory(location='eastus')
df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
print_item(df)
while df.provisioning_state != 'Succeeded':
    df = adf_client.factories.get(rg_name, df_name)
    time.sleep(1)
print_item(adf_client.factories.get(rg_name, df_name))
```

## <a name="create-a-linked-service"></a>Társított szolgáltatás létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **Azure Storage-beli társított szolgáltatást**.

Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a rövid útmutatóban csak egy Azure Storage-beli társított szolgáltatást kell létrehoznia a másolás forrásaként és fogadó adattárként. A példában ennek a neve: AzureStorageLinkedService.

```python
#Create Storage Linked Service
ls_name = 'storageLinkedService'

#Replace Storage String with your credentials
storage_string = SecureString('DefaultEndpointsProtocol=https;AccountName=<replace>;AccountKey=<replace>')

ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
print_item(ls)
```
## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a részben két adatkészletet hoz létre: egyet a forráshoz és egyet a fogadóhoz.

### <a name="create-a-dataset-for-source-azure-blob"></a>Adatkészlet létrehozása a forrás Azure Blobhoz
Adja hozzá a következő kódot a Main metódushoz, amely létrehoz egy Azure Blob-adatkészletet. Az Azure Blob-adatkészlet tulajdonságaival kapcsolatos információkért tekintse meg az [Azure Blob-összekötőt](connector-azure-blob-storage.md#dataset-properties) ismertető cikket. 

Meghatároz egy adatkészletet, amely a forrásadatokat jelöli az Azure Blobban. Ez a blob-adatkészlet az előző lépésben létrehozott Azure Storage-beli társított szolgáltatásra vonatkozik.

```python
#Create Dataset Input
ds_name = 'ds_in'
ds_ls = LinkedServiceReference(ls_name)
blob_path= 'adfv2tutorial/'
blob_filename = 'input.txt'
ds_azure_blob= AzureBlobDataset(ds_ls, folder_path=blob_path, file_name = blob_filename)
ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Adatkészlet létrehozása a fogadó Azure Blobhoz
Adja hozzá a következő kódot a Main metódushoz, amely létrehoz egy Azure Blob-adatkészletet. Az Azure Blob-adatkészlet tulajdonságaival kapcsolatos információkért tekintse meg az [Azure Blob-összekötőt](connector-azure-blob-storage.md#dataset-properties) ismertető cikket. 

Meghatároz egy adatkészletet, amely a forrásadatokat jelöli az Azure Blobban. Ez a blob-adatkészlet az előző lépésben létrehozott Azure Storage-beli társított szolgáltatásra vonatkozik.

```python
#Create Dataset Output
dsOut_name = 'ds_out'
output_blobpath = 'output/'
dsOut_azure_blob = AzureBlobDataset(ds_ls, folder_path=output_blobpath)
dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza a **másolási tevékenységet tartalmazó folyamatot**.

```python
#Create 1st activity: Copy Activity
act_name =  'copyBlobtoBlob'
blob_source = BlobSource()
blob_sink = BlobSink()
dsin_ref = DatasetReference(ds_name)
dsOut_ref = DatasetReference(dsOut_name)
copy_activity = CopyActivity(act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

#Create Pipeline
p_name =  'copyPipeline'
params_for_pipeline = {}
p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
print_item(p)
```


## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely **elindítja a folyamat futását**.

```python
#Create Pipeline Run
print(adf_client.pipelines.create_run(rg_name, df_name, p_name,
    {
    }
))
```

## <a name="run-the-code"></a>A kód futtatása
Állítsa össze és indítsa el az alkalmazást, majd ellenőrizze a folyamat-végrehajtást.

A konzol megjeleníti az adat-előállító, a társított szolgáltatás, az adatkészletek, a folyamat, valamint a folyamat futása létrehozási állapotát. Várjon, amíg megjelennek a másolási tevékenység futásának részletei, beleértve az olvasott és írt adatok méretét. Ezután például az [Azure Storage Explorerhez](https://azure.microsoft.com/features/storage-explorer/) hasonló eszközök használatával ellenőrizheti, hogy a blobok a változókban megadottak szerint át lettek-e másolva az outputBlobPath helyre az inputBlobPath helyről.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az adat-előállító programkódból történő törléséhez adja hozzá az alábbi kódsorokat a programhoz: 

```csharp
adf_client.data_factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md). 
