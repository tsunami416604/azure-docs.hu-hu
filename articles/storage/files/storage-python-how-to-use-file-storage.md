---
title: Fejlesztés az Azure Files a Pythonnal |} A Microsoft Docs
description: Ismerje meg, hogyan fejleszthet Python-alkalmazásokat és szolgáltatásokat, amelyek az Azure Files használatának a fájladatok tárolásához.
services: storage
author: wmgries
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: tamram
ms.component: files
ms.openlocfilehash: f1ebc059c66615ecf21054e85472b2a6d86dfce6
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743187"
---
# <a name="develop-for-azure-files-with-python"></a>Fejlesztés az Azure Files Python használatával
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Ez az oktatóanyag bemutatja annak alapjait, a Python használatával való alkalmazásokat és szolgáltatásokat, amelyek az Azure Files használatának a fájladatok tárolásához. Ebben az oktatóanyagban a rendszer létrehoz egy egyszerű konzolalkalmazást és bemutatják, hogyan hajthat végre alapszintű műveleteket a Python és az Azure Files:

* Azure-fájlmegosztások létrehozása
* Könyvtárak létrehozása
* Fájlok és mappák az Azure-fájlmegosztások számbavétele
* Feltöltésére, letöltésére és fájl törlése

> [!Note]  
> Az Azure Files SMB-n keresztül lehet elérni, mivel, lehetséges, hogy a szabványos Python i/o-osztályokat és funkciók használatával az Azure-fájlmegosztás eléréséhez egyszerű alkalmazások írására. Ez a cikk azt ismerteti, hogyan írhat alkalmazásokat, amelyek használják az Azure Storage Python SDK-t használ a [Azure Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) felvenni a kapcsolatot az Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Töltse le és telepítse az Azure Storage SDK a Pythonhoz

A [Pythonhoz készült Azure Storage SDK](https://github.com/azure/azure-storage-python) van szükség a Python 2.7-es, 3.3-as, 3.4-es, 3.5-ös és 3.6-os.
 
## <a name="install-via-pypi"></a>Keresztül PyPi telepítése

Telepítse a Python-Csomagindexet (PyPI) keresztül, írja be:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Ha 0.36 vagy korábbi verzióját a Pythonhoz készült Azure Storage SDK frissíti, távolítsa el a régebbi SDK használatával `pip uninstall azure-storage` a legújabb csomag telepítése előtt.

Alternatív telepítési módszert, látogasson el a [a Githubon Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>A mintaalkalmazás megtekintése
f megtekintéséhez és futtathat egy mintaalkalmazást, amely bemutatja, hogyan használhatja a Pythont az Azure Files, lásd: [Azure Storage: Ismerkedés az Azure Files pythonban](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Futtassa a mintaalkalmazást, győződjön meg arról, hogy mindkettő telepítése a `azure-storage-file` és `azure-storage-common` csomagokat.

## <a name="set-up-your-application-to-use-azure-files"></a>Állítsa be alkalmazását az Azure Files használata
Adja hozzá a következő, bármely Python-forrás, amelyben programozott módon érheti el az Azure Storage kívánt fájl elején.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Az Azure Files kapcsolat beállítása 
A `FileService` objektum lehetővé teszi, hogy használatával a megosztások, könyvtárak és fájlok. Az alábbi kód létrehoz egy `FileService` objektumba a tárfiók nevét és kulcsát. A `<myaccount>` és a `<mykey>` értéket cserélje le a fiók nevére és kulcsára.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Az alábbi példakód a használható egy `FileService` objektumot a megosztás létrehozásához. Ha még nem létezik.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Könyvtár létrehozása
Tároló található alkönyvtárat nem ezek mindegyike a gyökérmappában lévő fájlokat azáltal is rendszerezheti. Az Azure Files lehetővé teszi, hogy hozzon létre annyi könyvtárakon, a fiók lehetővé teszi. Az alábbi kódot hoz létre a pillanatképmappa nevű **sampledir** a gyökérkönyvtárban.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és mappák az Azure-fájlmegosztások számbavétele
A fájlok és a egy megosztást a címtárak listájában, használja a **lista\_könyvtárak\_és\_fájlok** metódust. A metódus egy generátort ad vissza. A következő kód a kimenetek a **neve** , minden egyes fájl- és oszthatnak meg a konzolon.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Fájl feltöltése 
Azure-fájlmegosztás tartalmaz legalább az, egy gyökérkönyvtár fájlokat tároló is. Ebben a szakaszban megismerheti, hogyan feltölthet egy fájlt a helyi tárolóból gyökérkönyvtárában található egy megosztást az alakzatot kell.

Hozzon létre egy fájlt, és töltse fel az adatokat, használja a `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` vagy `create_file_from_text` módszereket. Hajtsa végre a szükséges darabolás, ha az adatok mérete meghaladja a 64 MB-os magas szintű módszerek.

`create_file_from_path` a megadott elérési út és a egy fájl tartalmát, feltölti és `create_file_from_stream` feltölt egy már megnyitott fájl/stream tartalmát. `create_file_from_bytes` tömbjeként, feltölti és `create_file_from_text` tölt fel a megadott szöveges értéket a megadott kódolás (alapértelmezett értéke UTF-8) használatával.

A következő példa feltölti a tartalmát a **sunset.png** fájlt a **myfile** fájlt.

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
Adatok fájlból való letöltéséhez használja `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes`, vagy `get_file_to_text`. Hajtsa végre a szükséges darabolás, ha az adatok mérete meghaladja a 64 MB-os magas szintű módszerek.

A következő példa bemutatja, hogy használatával `get_file_to_path` tartalmának letöltése a **myfile** fájlt, és tárolja a a **out-sunset.png** fájl.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Fájl törlése
Végül, a fájl törléséhez hívja `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot-preview"></a>Hozzon létre a megosztási pillanatképek (előzetes verzió)
A teljes fájlmegosztás idő másolatát egy pontot hozhat létre.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Metaadatokat tartalmazó megosztási pillanatkép létrehozása**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Lista-megosztásokat és -pillanatképek 
Listázhatja az összes egy adott megosztás pillanatképeit.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Keresse meg a megosztási pillanatképek
Minden egyes megosztási pillanatképek beolvasni a fájlok és könyvtárak ettől a ponttól időben tartalmát megnyithatja.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Megosztás pillanatképéből-fájljának beolvasása
A helyreállítási forgatókönyvben letölthető egy fájl megosztási pillanatképből.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Egyetlen megosztási pillanatkép törlése  
Egyetlen megosztási pillanatkép törlése

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Megosztás törlése, ha a megosztási pillanatképek léteznek
A pillanatképeket tartalmazó megosztásra nem lehet törölni, kivéve, ha minden pillanatkép először el kell hagyni.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>További lépések
Most, hogy bemutattuk, hogyan lehet a Python használatával Azure Files kezelésére, hajtsa végre az alábbi hivatkozások további.

* [Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355)
* [A Microsoft Azure Storage-hoz készült Python SDK](https://github.com/Azure/azure-storage-python)
