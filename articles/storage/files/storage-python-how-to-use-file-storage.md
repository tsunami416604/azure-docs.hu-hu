---
title: Fejlesztés a Azure Files Pythonban | Microsoft Docs
description: Megtudhatja, hogyan fejleszthet olyan Python-alkalmazásokat és-szolgáltatásokat, amelyek a fájlok tárolásához Azure Files használnak.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 139e3009722761172b7bbd57805a7f5b07e55fc0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699396"
---
# <a name="develop-for-azure-files-with-python"></a>Fejlesztés Azure Files Python-val
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Ez az oktatóanyag bemutatja a Python használatának alapjait olyan alkalmazások vagy szolgáltatások fejlesztéséhez, amelyek a fájlok tárolására Azure Files használnak. Ebben az oktatóanyagban egy egyszerű konzolos alkalmazást hozunk létre, és bemutatjuk, hogyan hajthat végre alapszintű műveleteket a Python és a Azure Files használatával:

* Azure-fájlmegosztás létrehozása
* Könyvtárak létrehozása
* Fájlok és könyvtárak enumerálása Azure-fájlmegosztás esetén
* Fájl feltöltése, letöltése és törlése

> [!Note]  
> Mivel Azure Files az SMB-kapcsolaton keresztül érhető el, az Azure-fájlmegosztás szabványos Python I/O-osztályaival és-funkcióival való használatával egyszerű alkalmazásokat is írhat. Ez a cikk leírja, hogyan írhat olyan alkalmazásokat, amelyek az Azure Storage Python SDK-t használják, amely a [Azure Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) használatával kommunikál Azure files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Töltse le és telepítse a Pythonhoz készült Azure Storage SDK-t

A [Pythonhoz készült Azure Storage SDK](https://github.com/azure/azure-storage-python) használatához Python 2,7, 3,3, 3,4, 3,5 vagy 3,6 szükséges.
 
## <a name="install-via-pypi"></a>Telepítés a PyPi-on keresztül

A Python Package index (PyPI) használatával történő telepítéshez írja be a következőt:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Ha a Python 0,36-es vagy korábbi verziójú Azure Storage SDK-ból frissít, távolítsa el a régebbi `pip uninstall azure-storage` SDK-t a legújabb csomag telepítése előtt.

Alternatív telepítési módszerekért keresse fel a [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python/)-t a githubon.

## <a name="view-the-sample-application"></a>A minta alkalmazás megtekintése
f a Python és a Azure Files [használatának módját bemutató minta alkalmazás megtekintése és futtatása: Azure Storage: Első lépések a Python](https://github.com/Azure-Samples/storage-file-python-getting-started)Azure Filesával. 

A minta alkalmazás futtatásához győződjön meg arról, hogy a és `azure-storage-file` `azure-storage-common` a csomagokat is telepítette.

## <a name="set-up-your-application-to-use-azure-files"></a>Az alkalmazás beállítása Azure Files használatára
Adja hozzá a következőt az összes olyan Python-forrásfájl tetejéhez, amelyben programozottan szeretné elérni az Azure Storage-t.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Azure Fileshoz való kapcsolódás beállítása 
Az `FileService` objektum lehetővé teszi a megosztások, könyvtárak és fájlok munkavégzését. A következő kód létrehoz egy `FileService` objektumot a Storage-fiók neve és a fiók kulcsa alapján. A `<myaccount>` és a `<mykey>` értéket cserélje le a fiók nevére és kulcsára.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
A következő példában egy `FileService` objektum segítségével hozhatja létre a megosztást, ha az nem létezik.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Könyvtár létrehozása
A tárolót úgy is rendszerezheti, hogy az alkönyvtárakon belül helyez el fájlokat, ahelyett, hogy mindegyiket a gyökérkönyvtárba helyezné. Azure Files lehetővé teszi, hogy annyi könyvtárat hozzon létre, amennyit a fiókja engedélyezni fog. Az alábbi kód létrehoz egy **sampledir** nevű alkönyvtárat a gyökérkönyvtár alatt.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és könyvtárak enumerálása Azure-fájlmegosztás esetén
A megosztásban található fájlok és könyvtárak listázásához használja a **könyvtárak\_és\_\_fájlok listázása** módszert. A metódus egy generátort ad vissza. A következő kód egy megosztásban lévő egyes fájlok és könyvtárak **nevét** adja eredményül a konzolon.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Fájl feltöltése 
Az Azure-fájlmegosztás legalább egy gyökérkönyvtárat tartalmaz, ahol a fájlok találhatók. Ebből a szakaszból megtudhatja, hogyan tölthet fel egy fájlt a helyi tárolóból egy megosztás gyökérkönyvtárára.

Fájl létrehozásához és az adatok feltöltéséhez használja a `create_file_from_path` `create_file_from_bytes` , `create_file_from_stream`vagy `create_file_from_text` a metódust. Ezek magas szintű módszerek, amelyek elvégzik a szükséges adatdarabolást, ha az adatok mérete meghaladja az 64 MB-ot.

`create_file_from_path`feltölti a fájl tartalmát a megadott elérési útról, és `create_file_from_stream` feltölti a tartalmat egy már megnyitott fájlból/streamből. `create_file_from_bytes`feltölt egy bájtos tömböt, és `create_file_from_text` feltölti a megadott szöveges értéket a megadott kódolással (alapértelmezett érték: UTF-8).

A következő példa feltölti a **Sunset. png** fájl tartalmát a **sajat** -fájlba.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Fájl letöltése
Adatok fájlból való letöltéséhez használja `get_file_to_path` `get_file_to_stream` `get_file_to_bytes`a következőt:, `get_file_to_text`, vagy. Ezek magas szintű módszerek, amelyek elvégzik a szükséges adatdarabolást, ha az adatok mérete meghaladja az 64 MB-ot.

Az alábbi példa bemutatja `get_file_to_path` , hogyan töltheti le a **sajat** -fájl tartalmát, és hogyan tárolhatja azt a **out-Sunset. png** fájlba.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Fájl törlése
Végül a fájl törléséhez hívja `delete_file`meg a következőt:.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Megosztási pillanatkép létrehozása
A teljes fájlmegosztás időpontját is létrehozhatja.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Megosztási pillanatkép létrehozása metaadatokkal**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Megosztások és Pillanatképek listázása 
Egy adott megosztás összes pillanatképét listázhatja.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Megosztási pillanatkép tallózása
Az egyes megosztási Pillanatképek tartalmának tallózásával lekérheti a fájlokat és a címtárakat az adott időpontban.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Fájl beolvasása a megosztási pillanatképből
A visszaállítási forgatókönyvhöz letöltheti a megosztási pillanatképből származó fájlokat.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Egyetlen megosztási pillanatkép törlése  
Egyetlen megosztási pillanatképet is törölhet.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Megosztás törlése, ha a megosztási Pillanatképek léteznek
Pillanatképeket tartalmazó megosztás csak akkor törölhető, ha az összes pillanatképet először törlik.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte, hogyan kezelheti Azure Files a Python segítségével, az alábbi hivatkozásokat követve további információkat tudhat meg.

* [Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355)
* [A Pythonhoz készült Microsoft Azure Storage SDK](https://github.com/Azure/azure-storage-python)
