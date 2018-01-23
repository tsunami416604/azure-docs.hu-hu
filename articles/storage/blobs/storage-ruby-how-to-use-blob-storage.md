---
title: "Blob storage (object storage) Ruby az használata |} Microsoft Docs"
description: "Store unstructured data in the cloud with Azure Blob storage (object storage) (Strukturálatlan adatok tárolása a felhőben Azure Blob Storage-fiókkal (objektumtároló))."
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: e2fe4c45-27b0-4d15-b3fb-e7eb574db717
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 01/18/2018
ms.author: tamram
ms.openlocfilehash: c4c6d47511acdae7afaf4a535c24c6fcc7e389b1
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="how-to-use-blob-storage-from-ruby"></a>How to use Blob storage from Ruby (A Blob Storage használata Rubyval)
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Áttekintés
Az Azure Blob Storage egy olyan szolgáltatás, amely a strukturálatlan adatokat objektumként/blobként tárolja a felhőben. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható.

Ez az útmutató bemutatja, hogyan hajthat végre a Blob storage használatával gyakori forgatókönyvek. A minták íródtak, a Ruby API használatával. Az ismertetett forgatókönyvek **feltöltése, listázása, letöltése,** és **törlése** blobokat.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby-alkalmazás létrehozása
Ruby-alkalmazás létrehozása. Útmutatásért lásd: [Ruby-alkalmazás létrehozása az App Service Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).


## <a name="configure-your-application-to-access-storage"></a>Állítsa be az alkalmazását tároló elérésére
Azure Storage használatához szüksége töltse le és használja a Ruby azure csomagot, amely tartalmaz egy kényelmi szalagtár szerepel, amely a többi tárolási szolgáltatásokkal kommunikálni.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems használja a csomag beszerzése
1. Használjon például egy parancssori felületet **PowerShell** (Windows), **Terminálszolgáltatások** (Mac), vagy **Bash** (Unix).
2. Írja be a "gem telepítése azure-storage-blob" gem és függőségeinek telepítéséhez a parancsablakban.

### <a name="import-the-package"></a>A csomag importálása
Kedvenc szövegszerkesztőjével használ, a Ruby, hol szeretne használni a tárolási fájl elejéhez adja hozzá a következő:

```ruby
require "azure/storage/blob"
```

## <a name="set-up-an-azure-storage-connection"></a>Egy Azure Storage-kapcsolat beállítása
Az azure-moduljának a környezeti változókat olvassák **AZURE\_tárolási\_fiók** és **AZURE\_tárolási\_ACCESS_KEY** az Azure storage-fiókhoz való kapcsolódáshoz szükséges információkat. Ha ezek a környezeti változók nem, meg kell adnia a fiók az adatokat a **Azure::Blob::BlobService:: létrehozása** az alábbi kódra:

```ruby
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )
```

Ezek az értékek beolvasása klasszikus vagy erőforrás-kezelő storage-fiókot az Azure-portálon:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Nyissa meg a használni kívánt tárfiókot.
3. Kattintson a beállítások panelen kattintson a jobb **hívóbetűk**.
4. A elérési kulcsok paneljén megjelenő láthatja, a hozzáférési kulcs 1 és 2 elérési kulcsot. Ezek bármelyikét használhatja.
5. A Másolás ikonra a kulcs másolása a vágólapra.

## <a name="create-a-container"></a>Tároló létrehozása
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

A **Azure::Storage::Blob::BlobService** objektum lehetővé teszi, hogy a tárolók és blobok. Egy tároló létrehozásához használja a **létrehozása\_container()** metódust.

Az alábbi példakód létrehoz egy tárolót vagy kiírja a hiba, ha van ilyen.

```ruby
azure_blob_service = Azure::Storage::Blob::BlobService.create_from_env
begin
    container = azure_blob_service.create_container("test-container")
rescue
    puts $!
end
```

Ha azt szeretné, hogy a fájlok a tároló nyilvános, beállíthatja a tároló engedélyeit.

Csak módosíthatja a <strong>létrehozása\_container()</strong> hívásakor adja át a **: nyilvános\_hozzáférés\_szint** lehetőséget:

```ruby
container = azure_blob_service.create_container("test-container",
    :public_access_level => "<public access level>")
```

Az érvényes értékek a **: nyilvános\_hozzáférés\_szint** lehetőség van:

* **BLOB:** nyilvános olvasási hozzáférés a blobok határozza meg. Blobadatok található olvasható névtelen kérelem keresztül, de adatai nem érhető el. Ügyfelek névtelen kérelem keresztül a tárolóban található blobok nem tudja felsorolni.
* **tároló:** határozza meg a tároló és a blob adatait a teljes nyilvános olvasási hozzáférés. Ügyfelek névtelen kérelem keresztül a tárolóban található blobok enumerálása, de nem tudja felsorolni a tárolók a tárfiókon belül.

Azt is megteheti, módosíthatja a nyilvános hozzáférési szint, a tároló használatával **beállítása\_tároló\_acl()** metódus nyilvános hozzáférés szintjének beállításához.

Az alábbi példakód módosítja a nyilvános hozzáférés szintet **tároló**:

```ruby
azure_blob_service.set_container_acl('test-container', "container")
```

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba
Tartalom feltöltése a blob, használja a **létrehozása\_blokk\_blob()** metódussal a blob, hozzon létre egy fájl vagy karakterlánc használják a blob tartalmát.

Az alábbi kód feltölti **test.png** , egy új blob neve "lemezkép-blob" tárolóban.

```ruby
content = File.open("test.png", "rb") { |file| file.read }
blob = azure_blob_service.create_block_blob(container.name,
    "image-blob", content)
puts blob.name
```

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása
A tárolók kilistázhatja **list_containers()** metódust.
A tárolóban lévő blobok listázása, használja a **lista\_blobs()** metódust. Ahhoz, hogy minden, a tárolóban lévő blobok listázásához hajtsa végre a szolgáltatás által visszaadott folytatási és továbbra is fut a list_blobs a tokenhez. Tekintse meg a [lista Blobok REST API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) részleteiről.

Az alábbi kód kiírja az összes blobjának egy tárolóban.

```ruby
nextMarker = nil
loop do
    blobs = azure_blob_service.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

## <a name="download-blobs"></a>Blobok letöltése
Blobok letöltéséhez használjon a **beolvasása\_blob()** metódusának segítéségével lekérheti a tartalmát.

Az alábbi példakód mutatja be, használatával **beolvasása\_blob()** töltse le a "lemezkép-blob" tartalmát és egy helyi fájlba írja.

```ruby
blob, content = azure_blob_service.get_blob(container.name,"image-blob")
File.open("download.png","wb") {|f| f.write(content)}
```

## <a name="delete-a-blob"></a>Blobok törléséhez
Végezetül blob törléséhez használja a **törlése\_blob()** metódust. Az alábbi példakód bemutatja, hogyan blobok törléséhez.

```ruby
azure_blob_service.delete_blob(container.name, "image-blob")
```

## <a name="next-steps"></a>További lépések
Tájékozódhat az összetettebb tárolási feladatok elvégzéséről, az alábbi hivatkozásokat követve:

* [Az Azure Storage csapat blogja](http://blogs.msdn.com/b/windowsazurestorage/)
* [Az Azure Storage szolgáltatás SDK a Rubyhoz](https://github.com/azure/azure-storage-ruby) GitHub tárházából
* [Adatátvitel az AzCopy parancssori segédprogrammal](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

