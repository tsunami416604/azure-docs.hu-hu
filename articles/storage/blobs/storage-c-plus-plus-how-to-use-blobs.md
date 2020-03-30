---
title: Az objektum (Blob) tárolóhasználata C++ - Azure | Microsoft dokumentumok
description: Strukturálatlan adatokat tárolanak a felhőben az Azure Blob (objektum) tárolóval.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/21/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 0a9015e33f5456efeac7f7c887995ac4a69f0259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941812"
---
# <a name="how-to-use-blob-storage-from-c"></a>A Blob storage használata C++ oldalról

Ez az útmutató bemutatja, hogyan hajthatja végre a gyakori forgatókönyvek az Azure Blob storage használatával. A példák bemutatják, hogyan tölthet fel, listázheti, töltheti le és törölheti a blobokat. A kódminták C++ nyelven íródtak, és az [Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) használják.   

A Blob storage bemutatása című témakörben olvashat [bővebben.](storage-blobs-introduction.md)

> [!NOTE]
> Ez az útmutató az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárának 1.0.0-s és újabb verziójára vonatkozik. A Microsoft a C++ tár legújabb verzióját javasolja, amely a [NuGet](https://www.nuget.org/packages/wastorage) vagy a [GitHub](https://github.com/Azure/azure-storage-cpp)segítségével érhető el.

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása
Ebben az útmutatóban olyan tárolási funkciókat fog használni, amelyek egy C++ alkalmazáson belül futtathatók.  

Ehhez telepítenie kell az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát, és létre kell hoznia egy Azure Storage-fiókot az Azure-előfizetésben.   

Az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát az alábbi módszerekkel telepítheti:

* **Linux:** Kövesse az Azure [Storage Ügyféltár C++ README: Első lépések linuxos](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) lapján megadott utasításokat.
* **Windows:** Windows rendszerben használja a [vcpkg](https://github.com/microsoft/vcpkg) függőségkezelőt. Kövesse a [gyorsindítást](https://github.com/microsoft/vcpkg#quick-start) a vcpkg inicializálásához. Ezután a következő paranccsal telepítse a tárat:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

A README-fájlban útmutatót talál a forráskód létrehozásához és a [NuGet-be](https://github.com/Azure/azure-storage-cpp#download--install) való exportálásához.

## <a name="configure-your-application-to-access-blob-storage"></a>Az alkalmazás konfigurálása a Blob storage eléréséhez
Adja hozzá a következő include utasítások a tetején a C++ fájl, ahol szeretné használni az Azure storage API-k at a blobok eléréséhez:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Azure storage-kapcsolati karakterlánc beállítása
Az Azure Storage-ügyfél egy tárolási kapcsolati sztringet használ az adatkezelési szolgáltatások elérésére szolgáló végpontok és hitelesítő adatok tárolásához. Ügyfélalkalmazásban való futtatáskor meg kell adnia a tárolási kapcsolat karakterláncát a következő formátumban, a tárfiók nevével és az Azure Portalon az *AccountName* és *accountKey* értékekhez az [Azure Portalon](https://portal.azure.com) felsorolt tárfiók tárolási hozzáférési kulcsával. A tárfiókokról és a hozzáférési kulcsokról az [Azure Storage-fiókok – további](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)információt talál. Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Az alkalmazás helyi Windows-számítógépen való teszteléséhez használhatja az [Azure SDK-val](https://azure.microsoft.com/downloads/)telepített Microsoft [Azure-tárterület-emulátort.](../storage-use-emulator.md) A storage-emulátor egy olyan segédprogram, amely szimulálja a blob, várólista és table szolgáltatások elérhető az Azure-ban a helyi fejlesztői gépen. A következő példa bemutatja, hogyan deklarálhat statikus mezőt a helyi Storage Emulatorhoz használható kapcsolati sztring tárolására:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Az Azure storage emulátor ának elindításához válassza a **Start** gombot, vagy nyomja le a **Windows** billentyűt. Kezdje el beírni az **Azure Storage Emulator ,** és válassza ki a Microsoft Azure **Storage-emulátor** az alkalmazások listájából.  

Az alábbi minták azt feltételezik, hogy az ezen két módszer egyikével kérte le a Storage kapcsolati sztringjét.  

## <a name="retrieve-your-storage-account"></a>A tárfiók lekérése
A **cloud_storage_account** osztály segítségével képviselheti a tárfiók adatait. A Storage-fiók információit a **parse** metódussal kérheti le a Storage kapcsolati sztringjéből.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Ezután egy **cloud_blob_client** osztályra mutató hivatkozást kaphat, mivel lehetővé teszi a Blob storage-ban tárolt tárolókat és blobokat képviselő objektumok lekérését. A következő kód létrehoz egy **cloud_blob_client** objektumot a fent lekért tárfiók-objektum használatával:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Útmutató: Tároló létrehozása
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

Alapértelmezés szerint az új tároló magánjellegű, és meg kell adnia a tárolási hozzáférési kulcsot a blobok letöltéséhez ebből a tárolóból. Ha azt szeretné, hogy a tárolón belüli fájlok (blobok) mindenki számára elérhetőlegyen, a tárolót a következő kód használatával állíthatja be nyilvánosnak:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Az interneten bárki láthatja a blobokat egy nyilvános tárolóban, de csak akkor módosíthatja vagy törölheti őket, ha rendelkezik a megfelelő hozzáférési kulccsal.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Útmutató: Blob feltöltése egy tárolóba
Az Azure Blob Storage támogatja a blokkblobokat és a lapblobokat. A legtöbb esetben a blokkblobok használata javasolt.  

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, és annak segítségével kérje le a blokkblob hivatkozását. Miután rendelkezik egy blob hivatkozás, feltöltheti az adatok adatfolyamot, hívja meg a **upload_from_stream** metódust. Ez az eljárás létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen. Az alábbi példák azt mutatják be, hogyan tölthetők fel blobok egy tárolóba, és feltételezik, hogy a tároló már létre lett hozva.  

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

Másik lehetőségként a **upload_from_file** módszerrel tölthet fel egy fájlt egy blokkblobba.

## <a name="how-to-list-the-blobs-in-a-container"></a>Útmutató: A blobok listázása egy tárolóban
A tárolóban lévő blobok listázásához először kérje le a tároló hivatkozását. Ezután használhatja a tároló **list_blobs** módszer a blobok és/vagy könyvtárak benne. A visszaadott **list_blob_item**tulajdonságainak és metódusainak gazdag készletének eléréséhez meg kell hívnia a **list_blob_item.as_blob** metódust egy **cloud_blob** objektum lehívásához, vagy a **list_blob.as_directory** metódust a cloud_blob_directory objektum lehívásához. A következő kód bemutatja, hogyan lehet beolvasni és kiadni az egyes elemek **URI-ját a saját mintatárolótárolóban:**

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

A listázási műveletekről az [Azure Storage Resources listázása C++ nyelven (List, C++ nyelven)](../storage-c-plus-plus-enumeration.md)további részletekért olvassa el a listázási műveleteket.

## <a name="how-to-download-blobs"></a>Útmutató: Blobok letöltése
Blobok letöltéséhez először olvassa be a blobhivatkozást, majd hívja meg a **download_to_stream** metódust. A következő példa a **download_to_stream** metódust használja a blob tartalmának átvitelére egy adatfolyam-objektumba, amelyet ezután egy helyi fájlban megtarthat.  

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

Azt is megteheti, hogy a **download_to_file** módszerrel letölti a blob tartalmát egy fájlba.
Emellett a **download_text** módszerrel is letöltheti a blob tartalmát szöveges karakterláncként.  

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

## <a name="how-to-delete-blobs"></a>Útmutató: Blobok törlése
Blob törléséhez először kap egy blob referencia, majd hívja meg a **delete_blob** metódusrajta.  

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
Most, hogy megtanulta a blob storage alapjait, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg az Azure Storage-ról.  

* [A Queue Storage használata C++-szal](../storage-c-plus-plus-how-to-use-queues.md)
* [A C++ táblatároló használata](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Azure Storage-erőforrások listázása C++-ban](../storage-c-plus-plus-enumeration.md)
* [C++ referenciatároló ügyféltár](https://azure.github.io/azure-storage-cpp)
* [Azure Storage-dokumentáció](https://azure.microsoft.com/documentation/services/storage/)
* [Transfer data with the AzCopy Command-Line Utility (Adatátvitel az AzCopy parancssori segédprogrammal)](../storage-use-azcopy.md)

