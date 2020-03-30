---
title: Fejlesztés Az Azure-fájlok pythonnal | Microsoft dokumentumok
description: Ismerje meg, hogyan fejleszthet Python-alkalmazásokat és -szolgáltatásokat, amelyek az Azure Files segítségével tárolják a fájladatokat.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 139e3009722761172b7bbd57805a7f5b07e55fc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699396"
---
# <a name="develop-for-azure-files-with-python"></a>Fejlesztés az Azure Files szolgáltatáshoz Pythonnal
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Ez az oktatóanyag bemutatja a Python használatával olyan alkalmazások vagy szolgáltatások fejlesztésének alapjait, amelyek az Azure Files-t használják a fájladatok tárolására. Ebben az oktatóanyagban egy egyszerű konzolalkalmazást hozunk létre, és bemutatjuk, hogyan hajthatja végre az alapvető műveleteket a Python és az Azure Files használatával:

* Azure-fájlmegosztások létrehozása
* Könyvtárak létrehozása
* Fájlok és könyvtárak számbavétele Azure-fájlmegosztásban
* Fájl feltöltése, letöltése és törlése

> [!Note]  
> Mivel az Azure Files sMB-n keresztül érhető el, egyszerű alkalmazásokat is írhat, amelyek az Azure-fájlmegosztáshoz a szabványos Python I/O-osztályok és -függvények használatával férnek hozzá. Ez a cikk ismerteti, hogyan írhat alkalmazásokat, amelyek az Azure Storage Python SDK, amely az [Azure Files REST API-t](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) használja az Azure Files használatával az Azure Files használatával.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Az Azure Storage SDK python-hoz szolgáltatásletöltése és telepítése

Az [Azure Storage SDK pythonhoz](https://github.com/azure/azure-storage-python) python 2.7, 3.3, 3.4, 3.5 vagy 3.6 igényel.
 
## <a name="install-via-pypi"></a>Telepítés PyPi-n keresztül

A Python package indexen (PyPI) keresztül történő telepítéshez írja be a következőt:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Ha az Azure Storage SDK-ról frissít a Python 0.36-os vagy korábbi `pip uninstall azure-storage` verziójához, távolítsa el a régebbi SDK-t a legújabb csomag telepítése előtt.

Alternatív telepítési módszerekért keresse fel az [Azure Storage SDK python-t a GitHubon.](https://github.com/Azure/azure-storage-python/)

## <a name="view-the-sample-application"></a>A mintaalkalmazás megtekintése
f A Python azure-fájlokkal való használatát bemutató mintaalkalmazás megtekintéséhez és futtatásához lásd: Azure Storage: Első lépések az [Azure Files pythonban.](https://github.com/Azure-Samples/storage-file-python-getting-started) 

A mintaalkalmazás futtatásához győződjön meg `azure-storage-file` `azure-storage-common` arról, hogy telepítette a és a csomagokat is.

## <a name="set-up-your-application-to-use-azure-files"></a>Az alkalmazás beállítása az Azure Files használatára
Adja hozzá a következőket bármely Python-forrásfájl tetején, amelyben programozott módon szeretné elérni az Azure Storage-t.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Kapcsolat beállítása az Azure Files-hoz 
Az `FileService` objektum lehetővé teszi, hogy megosztásokkal, könyvtárakkal és fájlokkal dolgozzon. A következő kód `FileService` létrehoz egy objektumot a tárfiók nevével és a fiókkulcsával. A `<myaccount>` és a `<mykey>` értéket cserélje le a fiók nevére és kulcsára.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
A következő kódpéldában egy `FileService` objektum segítségével létrehozhatja a megosztást, ha az nem létezik.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Könyvtár létrehozása
A tárolást úgy is rendszerezheti, hogy a fájlokat alkönyvtárakba helyezi, és nem mindegyik van a gyökérkönyvtárban. Az Azure Files lehetővé teszi, hogy annyi könyvtárat hozzon létre, amennyit a fiókja lehetővé tesz. Az alábbi kód létrehoz egy **mintakönyvtár** nevű alkönyvtárat a gyökérkönyvtár alatt.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és könyvtárak számbavétele Azure-fájlmegosztásban
A fájlok és könyvtárak megosztásban való listázásához használja a **listakönyvtárak\_\_és\_fájlok** módszerét. A metódus egy generátort ad vissza. A következő kód egy megosztásban lévő egyes fájlok és könyvtárak **nevét** adja ki a konzolra.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Fájl feltöltése 
Az Azure fájlmegosztás legalább egy gyökérkönyvtárat tartalmaz, ahol a fájlok találhatók. Ebből a szakaszból megtudhatja, hogyan tölthet fel egy fájlt a helyi tárolóból egy megosztás gyökérkönyvtárába.

Fájl létrehozásához és adatok feltöltéséhez `create_file_from_bytes` `create_file_from_text` használja a `create_file_from_path` `create_file_from_stream`, vagy metódust. Ezek olyan magas szintű módszerek, amelyek végrehajtják a szükséges adattömböt, ha az adatok mérete meghaladja a 64 MB-ot.

`create_file_from_path`feltölti a fájl tartalmát a megadott elérési `create_file_from_stream` útról, és feltölti a tartalmat egy már megnyitott fájlból/adatfolyamból. `create_file_from_bytes`feltölt egy sor bájtot, `create_file_from_text` és feltölti a megadott szöveges értéket a megadott kódolással (alapértelmezés szerint UTF-8).

A következő példa feltölti a **sunset.png** fájl tartalmát a **myfile** fájlba.

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
Ha adatokat szeretne letölteni `get_file_to_path` `get_file_to_stream`egy `get_file_to_bytes`fájlból, használja a , , vagy `get_file_to_text`a . Ezek olyan magas szintű módszerek, amelyek végrehajtják a szükséges adattömböt, ha az adatok mérete meghaladja a 64 MB-ot.

A következő példa `get_file_to_path` bemutatja a **myfile** fájl tartalmának letöltését és az **out-sunset.png** fájlban való tárolását.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Fájl törlése
Végül egy fájl törléséhez `delete_file`hívja meg a hívást.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Megosztáspillanatkép létrehozása
A teljes fájlmegosztásról időpontban másolatot készíthet.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Megosztáspillanatkép létrehozása metaadatokkal**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Listamegosztások és pillanatképek 
Egy adott megosztás összes pillanatképét felsorolhatja.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Megosztás pillanatképének tallózása
Az egyes megosztási pillanatképek tartalma között böngészve lekérheti a fájlokat és a könyvtárakat attól az időponttól kezdve.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Fájl beszerezni a megosztás pillanatfelvételéből
A visszaállítási forgatókönyvhez letöltheti a fájlt egy megosztási pillanatképből.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Egyetlen megosztási pillanatkép törlése  
Egyetlen megosztási pillanatképet törölhet.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Megosztási pillanatképek esetén a megosztástörlések törlése
Pillanatképeket tartalmazó megosztás csak akkor törölhető, ha először az összes pillanatkép törlődik.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>További lépések
Most, hogy megtanulta az Azure-fájlok pythonnal való kezelésének módját, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg.

* [Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK python-hoz](https://github.com/Azure/azure-storage-python)
