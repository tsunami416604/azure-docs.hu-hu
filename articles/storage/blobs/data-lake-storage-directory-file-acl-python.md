---
title: A Python használata fájlok & ACL-ekkel Azure Data Lake Storage Gen2 (előzetes verzió)
description: A Python segítségével kezelheti a könyvtárakat és a fájl-és címtár-hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér.
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a65e8b2c5fbcdd4f6df0f8f39b24fa97b8deeca3
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534432"
---
# <a name="use-python-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>A Python használata fájlok & ACL-ekkel Azure Data Lake Storage Gen2 (előzetes verzió)

Ez a cikk bemutatja, hogyan lehet a Python használatával könyvtárakat, fájlokat és engedélyeket létrehozni és kezelni olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS). 

> [!IMPORTANT]
> A Pythonhoz készült Azure Data Lake Storage ügyféloldali kódtár jelenleg nyilvános előzetes verzióban érhető el.

[Csomag (Python-csomag indexe)](https://pypi.org/project/azure-storage-file-datalake/) | [minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API-referenciák](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html) | [Gen1 a Gen2 leképezéséhez](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [visszajelzés küldése](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.

## <a name="set-up-your-project"></a>A projekt beállítása

Telepítse a Pythonhoz készült Azure Data Lake Storage ügyféloldali kódtárat a [pip](https://pypi.org/project/pip/)használatával.

```
pip install azure-storage-file-datalake --pre
```

Adja hozzá ezeket az importálási utasításokat a fájl elejéhez.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

A cikkben szereplő kódrészletek használatához létre kell hoznia egy **DataLakeServiceClient** -példányt, amely a Storage-fiókot jelképezi. A legegyszerűbb módja, ha egy fiókot használ. 

Ez a példa egy fiókot használ a Storage-fiókot képviselő **DataLakeServiceClient** -példány létrehozásához. 

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Cserélje le a `storage_account_name` helyőrző értékét a Storage-fiók nevére.

- Cserélje le a `storage-account-key` helyőrző értékét a Storage-fiók elérési kulcsára.

## <a name="create-a-file-system"></a>Fájlrendszer létrehozása

A fájlrendszer tárolóként működik a fájlok számára. Létrehozhat egyet a **FileSystemDataLakeServiceClient. create_file_system** metódus meghívásával.

Ez a példa egy `my-file-system`nevű fájlrendszert hoz létre.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-referenciát a **FileSystemClient. create_directory** metódus meghívásával.

Ez a példa egy `my-directory` nevű könyvtárat helyez el egy fájlrendszerhez. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a **DataLakeDirectoryClient. rename_directory** metódus meghívásával. Adja meg a kívánt könyvtár elérési útját (a paramétert). 

Ez a példa egy alkönyvtárat nevez át a `my-subdirectory-renamed`névre.

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

A **DataLakeDirectoryClient. delete_directory** metódus meghívásával törölhet egy könyvtárat.

Ez a példa töröl egy `my-directory`nevű könyvtárat.  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Címtár engedélyeinek kezelése

A címtár hozzáférés-vezérlési listájának (ACL) beszerzéséhez hívja meg a **DataLakeDirectoryClient. get_access_control** metódust, és állítsa be az ACL-t úgy, hogy meghívja a **DataLakeDirectoryClient. set_access_control** metódust.

Ez a példa lekérdezi és beállítja egy `my-directory`nevű könyvtár ACL-listáját. Az olvasási, írási és végrehajtási jogosultságokat tartalmazó karakterlánc `rwxr-xrw-` megadja a tulajdonosi csoport számára az olvasási és végrehajtási engedélyeket, és minden más olvasási és írási engedélyt ad.

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

Először hozzon létre egy fájlt a célhelyen a **DataLakeFileClient** osztály egy példányának létrehozásával. Töltsön fel egy fájlt a **DataLakeFileClient. append_data** metódus meghívásával. Ügyeljen arra, hogy a feltöltést a **DataLakeFileClient. flush_data** metódus meghívásával végezze el.

Ez a példa egy szövegfájlt tölt fel egy `my-directory`nevű könyvtárba.   

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

## <a name="manage-file-permissions"></a>Fájlengedélyek kezelése

Egy fájl hozzáférés-vezérlési listájának (ACL) beszerzéséhez hívja meg a **DataLakeFileClient. get_access_control** metódust, és állítsa be az ACL-t úgy, hogy meghívja a **DataLakeFileClient. set_access_control** metódust.

Ez a példa lekérdezi és beállítja a `my-file.txt`nevű fájl hozzáférés-vezérlési listáját. Az olvasási, írási és végrehajtási jogosultságokat tartalmazó karakterlánc `rwxr-xrw-` megadja a tulajdonosi csoport számára az olvasási és végrehajtási engedélyeket, és minden más olvasási és írási engedélyt ad.

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

## <a name="download-from-a-directory"></a>Letöltés egy címtárból 

Nyisson meg egy helyi fájlt írásra. Ezután hozzon létre egy **DataLakeFileClient** -példányt, amely a letölteni kívánt fájlt jelöli. Hívja meg a **DataLakeFileClient. read_file** fájlt a fájl bájtjainak olvasásához, majd írja be a bájtokat a helyi fájlba. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

A könyvtár tartalmának listázásához hívja meg a **FileSystemClient. get_paths** metódust, majd az eredmények alapján sorolja fel azokat.

Ez a példa kinyomtatja az egyes alkönyvtárak és fájlok elérési útját, amely egy `my-directory`nevű könyvtárban található.

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

## <a name="see-also"></a>Lásd még:

* [API-referenciák dokumentációja](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Csomag (Python-csomag indexe)](https://pypi.org/project/azure-storage-file-datalake/)
* [Minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-python/issues)
