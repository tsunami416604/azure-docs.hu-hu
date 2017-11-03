---
title: "Az Azure Files Python kialakított |} Microsoft Docs"
description: "Ismerje meg, hogyan fejleszthet Python-alkalmazások és adatok tárolásához Azure-fájlokat használó szolgáltatásokat."
services: storage
documentationcenter: python
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/19/2017
ms.author: robinsh
ms.openlocfilehash: 17fdbbe2ea1d22c6f0ac287c4afbe17a44563f41
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="develop-for-azure-files-with-python"></a>Az Azure Files Python kialakított
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Ez az oktatóanyag a Python segítségével alkalmazásokat és szolgáltatásokat fájl adatok tárolásához Azure-fájlokat használó alapjait mutatni. Ebben az oktatóanyagban a rendszer egyszerű Konzolalkalmazás létrehozása és bemutatják, hogyan Azure fájlokat és Python alapszintű műveletek végrehajtása:

* Az Azure fájlmegosztások létrehozása
* Könyvtárak létrehozása
* Fájlok és könyvtárak egy Azure fájlmegosztás számbavétele
* Töltse fel, töltse le és törölje a fájlt

> [!Note]  
> Azure fájlok érhető SMB-n keresztül, mert akkor lehet egyszerű alkalmazások írását, amelyek a szabványos Python i/o-osztályok és függvény használata Azure fájlmegosztás eléréséhez. Ez a cikk azt ismerteti, hogyan alkalmazások írását, amelyek az Azure Storage Python SDK-val, használja a [Azure fájlok REST API](https://docs.microsoft.com/en-us/rest/api/storageservices/fileservices/file-service-rest-api) ügyfélcsatornája az Azure Fileshoz.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Töltse le és telepítse az Azure Storage SDK Python-hez

Python-hez készült Azure Storage szolgáltatás SDK Python 2.7, 3.3-as, 3.4, 3.5-ös vagy 3.6 igényel, és 4 csomagokban származnak: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` és `azure-storage-queue`. Ebben az oktatóanyagban fogjuk használni `azure-storage-file` csomag.
 
## <a name="install-via-pypi"></a>PyPi keresztül

A Python-Csomagindexet (PyPI) keresztül telepítéséhez írja be:

```bash
pip install azure-storage-file
```


> [!NOTE]
> Ha az Azure Storage SDK-ból a Python 0.36 vagy korábbi verzióját frissíti, akkor először eltávolításához használja `pip uninstall azure-storage` , már nem szeptemberben a Storage szolgáltatás SDK Python csomagban.
> 
> 

Alternatív módszerek, látogasson el a [Azure Storage szolgáltatás SDK a Githubon Python](https://github.com/Azure/azure-storage-python/).

## <a name="set-up-your-application-to-use-azure-files"></a>Állítsa be az alkalmazás Azure-fájlok használata
Adja hozzá a következő tetejénél található bármely Python forrásfájl, amelyben programon keresztüli eléréséhez az Azure Storage kívánja.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Az Azure Fileshoz kapcsolat beállítása 
A `FileService` objektum lehetővé teszi, hogy a megosztások, könyvtárak és fájlok. Az alábbi kód létrehoz egy `FileService` objektumba a tárfiók nevét és a fiók kulcsot. Cserélje le `<myaccount>` és `<mykey>` a fióknevet és kulcsot.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Az alábbi példakód használhat egy `FileService` objektumot a megosztás létrehozásához, ha még nem létezik.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Könyvtár létrehozása
Tárolási tegyen alkönyvtárat így ahelyett hogy ezek a gyökérkönyvtárban található fájlok is rendezhetők. Az Azure Files hozhat létre a fiókját engedélyezi a könyvtárat. Az alábbi kódot hoz létre a nevű alkönyvtárát **sampledir** a gyökérkönyvtárban.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és könyvtárak egy Azure fájlmegosztás számbavétele
Kilistázhatja a fájlok és könyvtárak olyan megosztáson található, a **lista\_könyvtárak\_és\_fájlok** metódust. Ez a módszer egy generátor adja vissza. A következő kimenetek kódot a **neve** minden fájl és a könyvtár egy megosztáson található, a konzolhoz.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Fájl feltöltése 
A megosztás tartalmaz legalább az Azure File, egy gyökérkönyvtár fájlokat tároló is. Ebben a szakaszban megtudhatja, hogyan feltölteni a fájlt a helyi tároló megosztás gyökérkönyvtárában alakzatot.

Hozzon létre egy fájlt, és feltölteni az adatokat, használja a `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` vagy `create_file_from_text` módszerek. Hajtsa végre a szükséges adattömbösítő, ha az adatok mérete meghaladja a 64 MB magas szintű módszerek.

`create_file_from_path`feltölt egy fájlt a megadott elérési és `create_file_from_stream` feltölt egy már megnyitott fájl vagy adatfolyam tartalmát. `create_file_from_bytes`Bájttömb, feltölti és `create_file_from_text` feltölti az adott szöveges értéket a megadott kódolás (alapértelmezett értéke UTF-8) használatával.

Az alábbi példa feltölti a tartalmát a **sunset.png** fájlt a **saját_fájl** fájlt.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Fájl letöltése
Adatok fájlból való letöltéséhez használjon `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes`, vagy `get_file_to_text`. Hajtsa végre a szükséges adattömbösítő, ha az adatok mérete meghaladja a 64 MB magas szintű módszerek.

A következő példa bemutatja, hogy használatával `get_file_to_path` tartalmának letöltése a **saját_fájl** fájlt, és tárolja el azt, hogy a **out-sunset.png** fájlt.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Fájl törlése
Végezetül, a fájl törléséhez hívja meg a `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot-preview"></a>Megosztás pillanatkép (előzetes verzió) létrehozása
Létrehozhat egy pont kötött másolata, amelyet a teljes fájlmegosztást.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**A metaadatok megosztás pillanatkép létrehozása**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Lista-megosztásokat és -pillanatképek 
Egy adott megosztáshoz a pillanatképek felsorolása

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Keresse meg a megosztás pillanatkép
Böngészhet a tartalom minden megosztás pillanatkép lekérdezni a fájlok és könyvtárak külön időben.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Megosztás pillanatképből fájl beolvasása
A fájl letöltheti megosztás pillanatkép, az adott helyreállítási körülményekhez.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Egyetlen megosztása pillanatkép törlése  
Egyetlen megosztása pillanatkép törlése.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Törölje a megosztást, amikor megosztás pillanatképek létezik
Nem lehet törölni a pillanatképeket tartalmazó megosztásra, kivéve, ha a pillanatképek először el kell hagyni.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte how Azure Python konfigurál, ezeket hivatkozásokat követve tudhat meg többet.

* [Python fejlesztői központ](/develop/python/)
* [Az Azure Storage-szolgáltatások REST API-ja](http://msdn.microsoft.com/library/azure/dd179355)
* [A Microsoft Azure Storage Python SDK](https://github.com/Azure/azure-storage-python)