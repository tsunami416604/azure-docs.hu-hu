---
title: A C++ - Azure objektum (Blob) storage használata |} Microsoft Docs
description: Strukturálatlan adatok tárolása a felhőben az (objektum) Azure Blob storage szolgáltatással.
services: storage
author: MichaelHauss
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/21/2018
ms.author: michaelhauss
ms.openlocfilehash: d3297ae7bc4a5ac7e2a43d9d44a05365004b685f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-blob-storage-from-c"></a>Blob storage-ának C++ használata

Ez az útmutató bemutatja, hogyan hajthat végre a szolgáltatást az Azure Blob storage szolgáltatást használó általános forgatókönyvhöz. A minták írt C++ és használni a [Azure Storage ügyféloldali kódtára a C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Az ismertetett forgatókönyvek közé tartozik a feltöltése, listázása, letöltése és blobok törlése.  

> [!NOTE]
> Ez az útmutató az Azure Storage ügyféloldali kódtár célozza meg, a C++ 1.0.0 verzió vagy újabb. A Microsoft azt javasolja, hogy a Storage ügyféloldali kódtár legújabb verzióját használja a c++-ban elérhető [NuGet](http://www.nuget.org/packages/wastorage) vagy [GitHub](https://github.com/Azure/azure-storage-cpp).

## <a name="what-is-blob-storage"></a>Mi az a Blob storage?

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>A C++-alkalmazás létrehozása
Ez az útmutató egy C++ alkalmazáson belül futtatható tárolási szolgáltatásokkal fog használni.  

Ehhez az szükséges, akkor telepítse az Azure Storage ügyféloldali kódtára a C++ és az Azure storage-fiók létrehozása az Azure-előfizetése.   

Telepítse az Azure Storage ügyféloldali kódtára a C++, a következő módszereket használhatja:

* **Linux:** megadott kövesse a [Azure Storage ügyféloldali kódtára a C++ információs](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) lap.  
* **Windows:** a Visual Studióban kattintson **eszközök > NuGet-Csomagkezelő > Csomagkezelő konzol**. Írja be a következő parancsot a [NuGet Package Manager console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) nyomja le az ENTER **ENTER**.  
  
     Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Állítsa be az alkalmazását, a Blob storage eléréséhez
Adja hozzá, a következő tartalmazó utasítások, ahol szeretné használni az Azure storage API-kkal blobokhoz való hozzáférést a C++-fájl elejéhez:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Egy Azure storage kapcsolati karakterlánc beállítása
Egy Azure storage-ügyfél egy tárolási kapcsolati karakterlánc végpontok és adatok szolgáltatások eléréséhez szükséges hitelesítő adatok tárolására használ. Ha egy ügyfél-alkalmazás fut, meg kell adnia a tárolási kapcsolati karakterlánc a következő formátumban a tárfiók és a tárelérési kulcs nevét használja a tárfiók szerepel a [Azure Portal](https://portal.azure.com) a a *AccountName* és *AccountKey* értékeket. A storage-fiókok és a hívóbetűk információkért lásd: [kapcsolatos Azure Storage-fiókok](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Ez a példa bemutatja, hogyan deklarálhatnak ahhoz, hogy a kapcsolati karakterlánc statikus mezőben:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Az alkalmazás tesztelése a helyi Windows-számítógép, használhatja a Microsoft Azure [storage emulator](../storage-use-emulator.md) együtt települ, amely a [Azure SDK](https://azure.microsoft.com/downloads/). A storage emulator egy segédprogram, amely a helyi fejlesztési számítógépén elérhető az Azure Blob, Queue és Table szolgáltatások szimulálja. A következő példa bemutatja, hogyan deklarálhatja, hogy tárolni tudja a kapcsolati karakterláncot a helyi storage emulator statikus mezőben:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Az Azure storage emulator elindításához válassza ki a **Start** gombra vagy nyomja le az **Windows** kulcs. Írja be a szöveget **Azure Storage Emulator**, és válassza ki **Microsoft Azure Storage Emulator** az alkalmazások listájából.  

A következő minták azt feltételezik, hogy használt két módszer közül egyik beolvasni a tárolási kapcsolati karakterlánc.  

## <a name="retrieve-your-connection-string"></a>A kapcsolat-karakterlánc beolvasása
Használhatja a **cloud_storage_account** osztályt határoz meg a Tárfiók adatait. A tárfiók adatait a tárolási kapcsolati karakterlánc lekéréséhez használja a **elemezni** metódust.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Ezután a mutató hivatkozás beszerzése egy **cloud_blob_client** osztályt teszi lehetővé, amelyek megfelelnek a tárolók és blobok Blob Storage tárolóban tárolt objektumok beolvasása. Az alábbi kód létrehoz egy **cloud_blob_client** objektumba a tárolási fiók objektum azt lekérése fent:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Útmutató: a tároló létrehozása
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

Alapértelmezés szerint az új tároló privát, és meg kell adnia a tárelérési kulcsát a blobok letöltését az ebben a tárolóban. Ha azt szeretné, hogy elérhetővé tegye a fájlok (BLOB) tárolóban mindenki számára, a tárolót, hogy az alábbi kód segítségével nyilvánossá állíthatja be:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Bárki láthatja a nyilvános tárolókban lévő blobokat, de módosítja vagy törli őket, csak akkor, ha rendelkezik a megfelelő hozzáférési kulccsal.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Hogyan: tölthetők fel blobok egy tárolóba
Az Azure Blob storage támogatja a blobokat és lapblobokat blokkolása. A legtöbb esetben a blokkblobok használata javasolt.  

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, és annak segítségével kérje le a blokkblob hivatkozását. Ha megszerezte a blobhivatkozást, akkor bármilyen streamet feltölthet adatok rá meghívásával a **upload_from_stream** metódust. Ez az eljárás létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen. Az alábbi példák azt mutatják be, hogyan tölthetők fel blobok egy tárolóba, és feltételezik, hogy a tároló már létre lett hozva.  

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

Másik lehetőségként használhatja a **upload_from_file** módszer a fájl blokkblobba való feltöltéséhez.

## <a name="how-to-list-the-blobs-in-a-container"></a>Útmutató: a tárolóban lévő blobok listázása
A tárolóban lévő blobok listázásához először kérje le a tároló hivatkozását. A tároló segítségével **list_blobs** metódusának segítéségével lekérheti a blobokat és/vagy könyvtárakat belül. Számos tulajdonságai és metódusai visszaadásakor eléréséhez **list_blob_item**, meg kell hívnia a **list_blob_item.as_blob** metódus használatával kérje le a **cloud_blob** objektum, vagy a **list_blob.as_directory** metódus egy cloud_blob_directory objektum. A következő kód bemutatja, hogyan kérhető le, és az egyes elemek URI kimeneti a **a minta-tároló** tároló:

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

További műveletek listázásával további információkért lásd: [lista Azure Storage-erőforrások c++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Hogyan: blobok letöltése
Blobok letöltéséhez először kérjen le egy blobhivatkozást, és ezután hívja meg a **download_to_stream** metódust. Az alábbi példában a **download_to_stream** módszerrel kell továbbítania a blob tartalmát egy stream objektumra, majd a helyi fájlba is megmaradnak.  

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

Másik lehetőségként használhatja a **download_to_file** metódus letölteni a blob tartalmát egy fájlba.
Ezenkívül használhatja a **download_text** metódus szöveges karakterláncként BLOB a tartalom letöltéséhez.  

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

## <a name="how-to-delete-blobs"></a>Hogyan: blobok törlése
Blobok törléséhez először kérjen le egy blobhivatkozást, és, majd hívja a **delete_blob** metódust.  

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

* [A C++ Queue Storage használata](../storage-c-plus-plus-how-to-use-queues.md)
* [A C++ Table Storage használata](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [A c++ Azure Storage-erőforrások listája](../storage-c-plus-plus-enumeration.md)
* [A Storage ügyféloldali kódtára a c++ nyelvhez – dokumentáció](http://azure.github.io/azure-storage-cpp)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)
* [Adatátvitel az AzCopy parancssori segédprogrammal](../storage-use-azcopy.md)

