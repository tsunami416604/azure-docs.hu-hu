---
title: Azure Data Lake Storage Gen2 Python SDK fájlokhoz & ACL-khez
description: Használja a Python-címtan kezelheti a könyvtárakat, valamint a fájl- és címtárhozzáférés-vezérlési listákat (ACL) olyan tárfiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
author: normesta
ms.service: storage
ms.date: 04/10/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a79f3110206a01b9b974952f0ec0d299644be11f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262349"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>A Python használatával kezelheti a könyvtárakat, fájlokat és ACL-okat az Azure Data Lake Storage Gen2-ben

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet a Python könyvtárakat, fájlokat és engedélyeket olyan tárfiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS). 

[Csomag (Python-csomagindex)](https://pypi.org/project/azure-storage-file-datalake/) | [minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API-referencia](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html) | [Gen1 a Gen2 leképezés](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | visszajelzést[ad](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan tárfiók, amelynek hierarchikus névtere (HNS) engedélyezve van. Az [alábbi](data-lake-storage-quickstart-create-account.md) utasításokat követve hozzon létre egyet.

## <a name="set-up-your-project"></a>A projekt beállítása

Telepítse az Azure Data Lake Storage ügyfélkönyvtárat a Pythonhoz [a pip](https://pypi.org/project/pip/)használatával.

```
pip install azure-storage-file-datalake
```

Adja hozzá ezeket az importálási kimutatásokat a kódfájl tetejéhez.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Csatlakozás a fiókhoz

A jelenések ebben a cikkben, létre kell hoznia egy **DataLakeServiceClient** példányt, amely a tárfiókot jelöli. 

### <a name="connect-by-using-an-account-key"></a>Csatlakozás fiókkulcs használatával

Ez a legegyszerűbb módja annak, hogy csatlakozzon egy fiókhoz. 

Ez a példa egy **DataLakeServiceClient-példányt** hoz létre egy fiókkulcs használatával.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Cserélje `storage_account_name` le a helyőrző értéket a tárfiók nevére.

- Cserélje `storage_account_key` le a helyőrző értéket a tárfiók hozzáférési kulcsára.

### <a name="connect-by-using-azure-active-directory-ad"></a>Csatlakozás az Azure Active Directory (AD) használatával

Használhatja az [Azure identity ügyfélkódtár python](https://pypi.org/project/azure-identity/) az alkalmazás hitelesítéséhez az Azure AD.You can use the Azure identity client library for Python to authenticate your application with Azure AD.

Ez a példa egy **DataLakeServiceClient-példányt** hoz létre egy ügyfélazonosító, egy ügyféltitok és egy bérlői azonosító használatával.  Ezeknek az értékeknek a [beszerzése, olvassa el a jogkivonat beszerzése az Azure AD-től az ügyfélalkalmazásból érkező kérelmek engedélyezéséhez](../common/storage-auth-aad-app.md)című témakört.

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> További példák: az [Azure identity ügyfélkódtár python dokumentáció.](https://pypi.org/project/azure-identity/)

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer a fájlok tárolójaként működik. Létrehozhat egyet a **FileSystemDataServiceServiceClient.create_file_system** metódus hívásával.

Ez a példa létrehoz `my-file-system`egy fájlrendszert.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Könyvtár létrehozása

Directory-hivatkozás létrehozása a **FileSystemClient.create_directory** metódus hívásával.

Ez a példa `my-directory` egy fájlrendszerhez nevezett könyvtárat ad hozzá. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Könyvtár átnevezése vagy áthelyezése

Könyvtár átnevezése vagy áthelyezése a **DataLakeDirectoryClient.rename_directory** metódus hívásával. Adja át a kívánt könyvtár elérési útját egy paraméternek. 

Ez a példa átnevezi az `my-subdirectory-renamed`alkönyvtárat a nevére.

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Könyvtár törlése

Könyvtár törlése a **DataLakeDirectoryClient.delete_directory** metódus hívásával.

Ez a példa törli `my-directory`a .  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Címtárengedélyek kezelése

A **DataLakeDirectoryClient.get_access_control** metódus hívásával és az ACL beállításával a **DataLakeDirectoryClient.set_access_control** metódus hívásával.

> [!NOTE]
> Ha az alkalmazás engedélyezi a hozzáférést az Azure Active Directory (Azure AD) használatával, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá van rendelve a [Storage Blob Data Owner szerepkörhöz.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Ha többet szeretne tudni az ACL-engedélyek alkalmazásáról és módosításuk hatásairól, olvassa el [a Hozzáférés-vezérlés az Azure Data Lake Storage Gen2 alkalmazásban című témakört.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

Ez a példa beveszi és beállítja a névvel ellátott könyvtár ACL-t. `my-directory` A `rwxr-xrw-` karakterlánc olvasási, írási és végrehajtási engedélyeket ad a tulajdonában lévő felhasználónak, csak olvasási és végrehajtási engedélyeket ad a birtokló csoportnak, és az összes többi olvasási és írási engedélyt ad.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba 

Először hozzon létre egy fájlhivatkozást a célkönyvtárban a **DataLakeFileClient** osztály egy példányának létrehozásával. Töltsön fel egy fájlt a **DataLakeFileClient.append_data** metódus hívásával. Győződjön meg arról, hogy a feltöltést a **DataLakeFileClient.flush_data** metódus hívásával fejezi be.

Ez a példa egy szövegfájlt `my-directory`tölt fel a nevesített könyvtárba.   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> Ha a fájlmérete nagy, a kódnak több hívást kell kezdeményeznie a **DataLakeFileClient.append_data** metódushoz. Fontolja meg a **DataLakeFileClient.upload_data** metódus használatát. Így a teljes fájlt egyetlen hívással feltöltheti. 

## <a name="upload-a-large-file-to-a-directory"></a>Nagyfájl feltöltése könyvtárba

A **DataLakeFileClient.upload_data** metódussal nagy fájlokat tölthet fel anélkül, hogy több hívást kellene kezdeményeznie a **DataLakeFileClient.append_data** metódusba.

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Fájlengedélyek kezelése

A **DataLakeFileClient.get_access_control** metódus hívásával és az ACL beállításával a **DataLakeFileClient.set_access_control** metódus hívásával.

> [!NOTE]
> Ha az alkalmazás engedélyezi a hozzáférést az Azure Active Directory (Azure AD) használatával, akkor győződjön meg arról, hogy az alkalmazás által a hozzáférés engedélyezéséhez használt rendszerbiztonsági tag hozzá van rendelve a [Storage Blob Data Owner szerepkörhöz.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Ha többet szeretne tudni az ACL-engedélyek alkalmazásáról és módosításuk hatásairól, olvassa el [a Hozzáférés-vezérlés az Azure Data Lake Storage Gen2 alkalmazásban című témakört.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

Ez a példa beveszi és beállítja egy nevű fájl ACL-ét. `my-file.txt` A `rwxr-xrw-` karakterlánc olvasási, írási és végrehajtási engedélyeket ad a tulajdonában lévő felhasználónak, csak olvasási és végrehajtási engedélyeket ad a birtokló csoportnak, és az összes többi olvasási és írási engedélyt ad.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>Letöltés könyvtárból 

Nyisson meg egy helyi fájlt írásra. Ezután hozzon létre egy **DataLakeFileClient** példányt, amely a letölteni kívánt fájlt jelöli. Hívja meg a **DataLakeFileClient.read_file** fájlból bájtokat olvasson, majd írja ezeket a bájtokat a helyi fájlba. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

A könyvtár tartalmának listázása a **FileSystemClient.get_paths** metódus hívásával, majd az eredmények között történő számbavétel.

Ebben a példában a program kinyomtatja az egyes alkönyvtárak és -fájlok elérési útját, amelyek egy könyvtárban `my-directory`találhatók.

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>Lásd még

* [API-referenciadokumentáció](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Csomag (Python-csomagindex)](https://pypi.org/project/azure-storage-file-datalake/)
* [Példák](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 –Gen2 leképezés](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-python/issues)
