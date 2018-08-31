---
title: A c++ segítségével – Azure objektumtárat (Blobtárat) storage használata |} A Microsoft Docs
description: Strukturálatlan adatok Store (objektum) az Azure Blob storage-tárolókat a felhőben.
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: michaelhauss
ms.component: blobs
ms.openlocfilehash: d0b0f8ef2fcc4307482b4ccffcb46410eaba33d5
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306204"
---
# <a name="how-to-use-blob-storage-from-c"></a>A c++ segítségével Blob storage használata

Ez az útmutató bemutatja, hogyan hajthat végre az Azure Blob storage szolgáltatást használó általános forgatókönyvhöz. A kódminták C++ nyelven íródtak, és az [Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát](http://github.com/Azure/azure-storage-cpp/blob/master/README.md) használják. Az ismertetett forgatókönyvek között megtalálható a feltöltése, listázása, letöltése és a blobok törlése.  

> [!NOTE]
> Ez az útmutató az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárának 1.0.0-s és újabb verziójára vonatkozik. A Microsoft javasolja, hogy a Storage ügyféloldali kódtár legújabb verziójának használata C++-n keresztül elérhető [NuGet](http://www.nuget.org/packages/wastorage) vagy [GitHub](https://github.com/Azure/azure-storage-cpp).

## <a name="what-is-blob-storage"></a>Mi a Blob storage?

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása
Ebben az útmutatóban tárolási szolgáltatásokkal, amely egy C++-alkalmazásból futtathat fogja használni.  

Ehhez telepítenie kell az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát, és létre kell hoznia egy Azure Storage-fiókot az Azure-előfizetésben.   

Az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát az alábbi módszerekkel telepítheti:

* **Linux:** kövesse az utasításokat adott a [Azure Storage ügyféloldali kódtára a C++ információs](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) lapot.  
* **Windows:** A Visual Studióban válassza a **Tools (Eszközök) > NuGet Package Manager (NuGet-csomagkezelő) > Package Manager Console (Csomagkezelő konzol)** elemet. Írja be a következő parancsot a [NuGet Package Manager console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) nyomja le az ENTER **ENTER**.  
  
     Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Hozzáférés a Blob storage-alkalmazás konfigurálása
Adja hozzá a következő tartalmaznak utasításokat a kívánja blobok elérése az Azure storage API-k használatával C++ fájl elejéhez:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Egy Azure storage kapcsolati karakterlánc beállítása
Az Azure Storage-ügyfél egy tárolási kapcsolati sztringet használ az adatkezelési szolgáltatások elérésére szolgáló végpontok és hitelesítő adatok tárolásához. Ha egy ügyfélalkalmazás fut, meg kell adnia a tárolási kapcsolati karakterlánc a következő formátumban szerepel a tárfiók nevét a tárfiók és a tárelérési kulcs használatával a [az Azure Portal](https://portal.azure.com) számára a *AccountName* és *AccountKey* értékeket. Információk a storage-fiókok és a hozzáférési kulcsok: [kapcsolatos Azure-Tárfiókok](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Az alkalmazás tesztelése a helyi Windows-számítógép, használhatja a Microsoft Azure [storage emulator](../storage-use-emulator.md) együtt települ, amely a [Azure SDK](https://azure.microsoft.com/downloads/). A storage emulator egy segédprogram, amely szimulálja a Blob, Queue és Table szolgáltatások, a helyi fejlesztői gépen elérhető az Azure-ban. A következő példa bemutatja, hogyan deklarálhat statikus mezőt a helyi Storage Emulatorhoz használható kapcsolati sztring tárolására:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Az Azure storage emulator indításához válassza ki a **Start** gombra vagy nyomja le az **Windows** kulcs. Kezdje el beírni **Azure Storage Emulator**, és válassza ki **a Microsoft Azure Storage Emulator** az alkalmazások listájából.  

Az alábbi minták azt feltételezik, hogy az ezen két módszer egyikével kérte le a Storage kapcsolati sztringjét.  

## <a name="retrieve-your-connection-string"></a>A kapcsolati sztring lekérése
Használhatja a **cloud_storage_account** osztály, amely a Storage-fiók adatait jelöli. A Storage-fiók információit a **parse** metódussal kérheti le a Storage kapcsolati sztringjéből.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Ezután kérdezze le a hivatkozás egy **cloud_blob_client** teszi lehetővé, amelyek a tárolók és blobok, Blob storage-ben tárolt objektumokat beolvasni osztályt. Az alábbi kód létrehoz egy **cloud_blob_client** objektum, a storage-fiók objektummal, azt a fenti lekért:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Hogyan: tároló létrehozása
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

A példa bemutatja, hogyan hozhat létre tárolót, ha még nem rendelkezik vele:  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Alapértelmezés szerint az új tároló privát, és meg kell adnia a tárelérési kulcs blobok letöltése a tárolóból. Ha azt szeretné, hogy a fájlok (bináris objektumok) a tárolóban elérhető mindenki számára elérhető, állíthatja be a következő kód segítségével nyilvánossá teheti a tárolót:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Bárki hozzáférhet az interneten egy nyilvános tárolóban lévő blobok látható, de módosíthatja vagy törölheti őket, csak akkor, ha rendelkezik a megfelelő hozzáférési kulccsal.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Hogyan: feltölt egy blobot egy tárolóba
Az Azure Blob storage támogatja a blobok és lapblobok letiltása. A legtöbb esetben a blokkblobok használata javasolt.  

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, és annak segítségével kérje le a blokkblob hivatkozását. Ha megszerezte a blobhivatkozást, feltölthet bármilyen streamet, meghívásával a **upload_from_stream** metódust. Ez az eljárás létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen. Az alábbi példák azt mutatják be, hogyan tölthetők fel blobok egy tárolóba, és feltételezik, hogy a tároló már létre lett hozva.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Másik lehetőségként használhatja a **upload_from_file** metódus feltölthet egy fájlt egy blokkblobba.

## <a name="how-to-list-the-blobs-in-a-container"></a>Útmutató: a tárolóban lévő blobok listázása
A tárolóban lévő blobok listázásához először kérje le a tároló hivatkozását. Ezután használhatja a tároló **list_blobs** metódusának segítéségével lekérheti a blobok és/vagy a benne található könyvtárak. Számos tulajdonság és metódus visszaadásakor eléréséhez **list_blob_item**, meg kell hívni a **list_blob_item.as_blob** metódus lekéréséhez egy **cloud_blob** objektumot, vagy a **list_blob.as_directory** metódus cloud_blob_directory-objektum. A következő kód bemutatja, hogyan kérhető le és küldhető az egyes elemek URI azonosítója a **my-sample-container** tároló:

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

A műveletek listázása. További részletekért lásd: [lista Azure Storage-erőforrások c++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Útmutató: blobok letöltése
Blobok letöltéséhez először kérjen le egy blobhivatkozást, és ezután hívja meg a **download_to_stream** metódust. Az alábbi példában a **download_to_stream** módszerrel kell továbbítania a blob tartalmát egy stream objektumra, majd megőrizheti a helyi fájlba.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Másik lehetőségként használhatja a **download_to_file** metódus letölteni egy blob tartalmát egy fájlba.
Emellett használhatja a **download_text** metódus egy BLOB egy szöveges karakterlánc, a tartalom letöltéséhez.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Útmutató: blobok törlése
Blobok törléséhez először kérjen le egy blobhivatkozást, majd hívja a **delete_blob** metódust.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a blob storage alapjait, az alábbi hivatkozásokból tudhat meg többet az Azure Storage.  

* [A c++ segítségével a Queue Storage használata](../storage-c-plus-plus-how-to-use-queues.md)
* [Hogyan használható a Table Storage, a c++ segítségével](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [A C++ Azure Storage-erőforrások listája](../storage-c-plus-plus-enumeration.md)
* [A Storage ügyféloldali kódtára a c++ nyelvhez – dokumentáció](http://azure.github.io/azure-storage-cpp)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)
* [Adatátvitel az AzCopy parancssori segédprogrammal](../storage-use-azcopy.md)

