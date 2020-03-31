---
title: Fejlesztés Az Azure-fájlokhoz C++ használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan fejleszthet C++ alkalmazásokat és szolgáltatásokat, amelyek az Azure Files segítségével tárolják a fájladatokat.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 97af40bd1f57acb5b26d3b6216984dfb8e3a5181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699801"
---
# <a name="develop-for-azure-files-with-c"></a>Fejlesztés Az Azure-fájlokhoz C++ nyelven

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése

In this tutorial, you'll learn how to perform basic operations on Azure Files. A C++ nyelven írt mintáksegítségével megtudhatja, hogyan hozhat létre megosztásokat és könyvtárakat, hogyan tölthet fel, listázhat és törölhet fájlokat. Ha most ismeri az Azure Files, a következő szakaszokban található fogalmak átnézése hasznos lesz a minták megértésében.

* Azure-fájlmegosztások létrehozása és törlése
* Könyvtárak létrehozása és törlése
* Fájlok és könyvtárak számbavétele Azure-fájlmegosztásban
* Fájl feltöltése, letöltése és törlése
* Az Azure-fájlmegosztás kvótájának (maximális mérete) beállítása
* Közös hozzáférésű jogosultságkód (SAS-kulcs) létrehozása egy, a megosztásban meghatározott megosztott elérési házirendet használó fájlhoz.

> [!Note]  
> Mivel az Azure Files sMB-n keresztül érhető el, egyszerű alkalmazásokat is írhat, amelyek az Azure-fájlmegosztáshoz a szabványos C++ I/O-osztályok és függvények használatával férnek hozzá. Ez a cikk ismerteti, hogyan írhat alkalmazásokat, amelyek az Azure Storage C++ SDK-t használják, amely a [File REST API-t](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) használja az Azure Files-szal való beszélgetéshez.

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása

A minták létrehozásához telepítenie kell az Azure Storage Client Library 2.4.0 C++.For Samples for C++. Azure storage-fiókot is létre kellett volna hoznia.

Az Azure Storage Client 2.4.0 C++-hoz való telepítéséhez az alábbi módszerek egyikét használhatja:

* **Linux:** Kövesse az Azure [Storage Client Library for C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) lapon megadott utasításokat.
* **Windows:** A Visual Studio alkalmazásban kattintson az **Eszközök &gt; NuGet Csomagkezelő csomagkezelő &gt; konzoljára**. Írja be a következő parancsot a [NuGet Package Manager konzolba,](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) és nyomja **le az ENTER billentyűt.**
  

```powershell
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Az alkalmazás beállítása az Azure Files használatára

Adja hozzá a következő include utasítások a tetején a C ++ forrásfájl, ahol szeretné manipulálni az Azure-fájlok:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure storage-kapcsolati karakterlánc beállítása

A fájltárolás használatához csatlakoznia kell az Azure storage-fiókjához. Az első lépés egy kapcsolati karakterlánc konfigurálása lenne, amelyet a tárfiókhoz való csatlakozáshoz használunk. Definiáljunk egy statikus változót ehhez.

```cpp
// Define the connection-string with your values.
const utility::string_t
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Csatlakozás Azure-tárfiókhoz

A **cloud_storage_account** osztály segítségével képviselheti a tárfiók adatait. A Storage-fiók információit a **parse** metódussal kérheti le a Storage kapcsolati sztringjéből.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Az Azure-fájlmegosztásban lévő összes fájl és könyvtár **egy Megosztás**nevű tárolóban található. A tárfiók annyi megosztással rendelkezhet, amennyit a fiók kapacitása lehetővé tesz. Ahhoz, hogy hozzáférést szerezzen egy megosztáshoz és annak tartalmához, egy Azure Files ügyfelet kell használnia.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();
```

Az Azure Files ügyfél használatával ezután beszerezhet egy megosztásra mutató hivatkozást.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

A megosztás létrehozásához használja a **cloud_file_share** objektum **create_if_not_exists** módszerét.

```cpp
if (share.create_if_not_exists()) {
    std::wcout << U("New share created") << std::endl;
}
```

Ezen a ponton a **megosztás** egy **saját mintamegosztás**nevű megosztásra mutató hivatkozást tartalmaz.

## <a name="delete-an-azure-file-share"></a>Azure-fájlmegosztás törlése

A megosztás törlése úgy történik, hogy a **delete_if_exists** metódust cloud_file_share objektumon. Itt van a minta kód, hogy nem ezt.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

A tárolást úgy rendezheti, hogy fájlokat helyez el az alkönyvtárakban, és nem mindegyik van a gyökérkönyvtárban. Az Azure Files lehetővé teszi, hogy annyi könyvtárat hozzon létre, amennyit a fiókja lehetővé tesz. Az alábbi kód létrehoz egy **saját mintakönyvtár** nevű könyvtárat a gyökérkönyvtár alatt, valamint egy **saját minta-alkönyvtár**nevű alkönyvtárat.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>Könyvtár törlése

A könyvtár törlése egyszerű feladat, bár meg kell jegyezni, hogy nem lehet törölni egy olyan könyvtárat, amely továbbra is tartalmaz fájlokat vagy más könyvtárakat.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory =
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és könyvtárak számbavétele Azure-fájlmegosztásban

A megosztáson belüli fájlok és könyvtárak listájának beszerzése könnyen elvégezhető, ha **cloud_file_directory** hivatkozással **hívja meg list_files_and_directories.** A visszaadott **list_file_and_directory_item**tulajdonságainak és metódusainak gazdag készletének eléréséhez meg kell hívnia a **list_file_and_directory_item.as_file** metódust egy **cloud_file** objektum lehívásához, vagy a **list_file_and_directory_item.as_directory** metódust a **cloud_file_directory** objektum lehívásához.

A következő kód bemutatja, hogyan lehet beolvasni és kiadni a megosztás gyökérkönyvtárában lévő egyes elemek URI-ját.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_directory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }
}
```

## <a name="upload-a-file"></a>Fájl feltöltése

Legalább egy Azure-fájlmegosztás tartalmaz egy gyökérkönyvtárat, ahol a fájlok találhatók. Ebből a szakaszból megtudhatja, hogyan tölthet fel egy fájlt a helyi tárolóból egy megosztás gyökérkönyvtárába.

A fájl feltöltésének első lépése az, hogy hivatkozást szerezzen arra a könyvtárra, ahol tartózkodnia kell. Ehhez hívja meg a **megosztásobjektum get_root_directory_reference** metódusát.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Most, hogy már rendelkezik hivatkozással a megosztás gyökérkönyvtárára, feltölthet rá egy fájlt. Ez a példa fájlból, szövegből és adatfolyamból töltődik fel.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream =
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));
```

## <a name="download-a-file"></a>Fájl letöltése

Fájlok letöltéséhez először olvassa be a fájlhivatkozást, majd hívja meg a **download_to_stream** metódust a fájl tartalmának egy adatfolyam-objektumba való átviteléhez, amelyet aztán megőrizhet egy helyi fájlban. Másik lehetőségként a **download_to_file** módszerrel letöltheti a fájl tartalmát egy helyi fájlba. A **download_text** módszerrel szöveges karakterláncként töltheti le a fájl tartalmát.

A következő példa a **download_to_stream** és **download_text** módszereket használja az előző szakaszokban létrehozott fájlok letöltésének bemutatására.

```cpp
// Download as text
azure::storage::cloud_file text_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>Fájl törlése

Egy másik gyakori Azure Files művelet a fájl törlése. A következő kód törli a gyökérkönyvtárban tárolt my-sample-file-3 nevű fájlt.

```cpp
// Get a reference to the root directory for the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

azure::storage::cloud_file file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Az Azure-fájlmegosztás kvótájának (maximális mérete) beállítása

A fájlmegosztáskvótát (vagy maximális méretet) gigabájtban állíthatja be. Azt is ellenőrizheti, hogy aktuálisan mennyi adatot tárol a fájlmegosztás.

Ha beállít egy kvótát egy megosztáshoz, korlátozhatja a megosztáson tárolt fájlok összesített méretét. Ha a megosztásban található fájlok teljes mérete meghaladja a megosztáshoz beállított kvótát, az ügyfelek nem növelhetik tovább a meglévő fájlok méretét, és csak olyan új fájlokat hozhatnak létre, amelyek üresek.

Az alábbi példa bemutatja, hogyan ellenőrizheti egy megosztás aktuális kihasználását, és hogyan adhat meg hozzá kvótát.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Közös hozzáférésű jogosultságkód létrehozása egy fájlhoz vagy fájlmegosztáshoz

Létrehozhat egy fájlmegosztáshoz vagy egy fájlhoz közös hozzáférésű aláírást (SAS). Létrehozhat egy megosztott elérési házirendet is egy fájlmegosztáshoz, hogy kezelni tudja a közös hozzáférésű jogosultságkódokat. Azért érdemes létrehozni megosztott elérési házirendet, mert annak az eszközeivel vissza lehet hívni az SAS-t, amennyiben sérülne a biztonsága.

Az alábbi példa létrehoz egy megosztott elérési házirendet egy megosztáson, majd felhasználja a házirendet egy, a megosztásban található fájlhoz tartozó SAS korlátozására.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy =
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() +
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir =
        share.get_root_directory_reference();
    azure::storage::cloud_file file =
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share
    //  and associate this access policy with it.
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");
    file_with_sas.upload_text(text);

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();
    ucout << downloaded_text << std::endl;
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```

## <a name="next-steps"></a>További lépések

Az alábbi forrásokból többet is megtudhat az Azure Storage-ról:

* [A Storage ügyféloldali kódtára a C++ programnyelvhez](https://github.com/Azure/azure-storage-cpp)
* [Az Azure Storage-fájlszolgáltatás mintái C++ nyelven](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Azure Storage-dokumentáció](https://azure.microsoft.com/documentation/services/storage/)