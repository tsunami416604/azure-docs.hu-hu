---
title: A C++ az Azure Files kialakított |} Microsoft Docs
description: Ismerje meg, hogyan fejleszthet C++ alkalmazások és adatok tárolásához Azure-fájlokat használó szolgáltatásokat.
services: storage
documentationcenter: .net
author: renashahmsft
manager: aungoo
editor: tamram
ms.assetid: a1e8c99e-47a6-43a9-9541-c9262eb00b38
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: renashahmsft
ms.openlocfilehash: e0b5974780813eb4f3d67c42781db4d95829814d
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737571"
---
# <a name="develop-for-azure-files-with-c"></a>A C++-Azure fájlok fejlesztése
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ebben az oktatóanyagban, megtudhatja, hogyan Azure fájlokon alapvető műveletek végrehajtása. Írt C++ minták segítségével megtudhatja, hogyan megosztások létrehozása és könyvtárak, feltöltése, listázása és fájlok törlése. Ha most ismerkedik az Azure Fileshoz, a minták hasznos információkat a következő szakaszok a jellemzői áthaladás lesz.

* Hozzon létre vagy töröljön az Azure fájlmegosztások
* Hozzon létre vagy töröljön a könyvtárak
* Fájlok és könyvtárak az Azure fájlmegosztások számbavétele
* Töltse fel, töltse le és törölje a fájlt
* Egy Azure fájlmegosztás kvótájának (maximális méret) beállítása
* Közös hozzáférésű jogosultságkód (SAS-kulcs) létrehozása egy, a megosztásban meghatározott megosztott elérési házirendet használó fájlhoz.

> [!Note]  
> Azure fájlok érhető SMB-n keresztül, mert akkor lehet egyszerű alkalmazások írását, amelyek a szabványos C++ i/o-osztályok és függvény használata Azure fájlmegosztás eléréséhez. Ez a cikk azt ismerteti, hogyan alkalmazások írását, amelyek az Azure Storage C++ SDK-val, használja a [fájl REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) ügyfélcsatornája az Azure Fileshoz.

## <a name="create-a-c-application"></a>A C++-alkalmazás létrehozása
A minták létrehozásához szüksége lesz az Azure Storage ügyféloldali kódtár 2.4.0 for C++ telepítése. Meg is létrehozott egy Azure storage-fiókot.

Az Azure Storage ügyfél 2.4.0 telepítésében C++, a következő módszerek egyikét használhatja:

* **Linux:** megadott kövesse a [Azure Storage ügyféloldali kódtára a C++ információs](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) lap.
* **Windows:** a Visual Studióban kattintson **eszközök &gt; NuGet-Csomagkezelő &gt; Csomagkezelő konzol**. Írja be a következő parancsot a [NuGet Package Manager console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) nyomja le az ENTER **ENTER**.
  
```
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Állítsa be az alkalmazás Azure-fájlok használata
Adja hozzá az alábbi utasításokat a felső részén a C++ forrásfájl Azure fájlkezelő helyét tartalmazza:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Egy Azure storage kapcsolati karakterlánc beállítása
A File storage használatához szüksége az Azure storage-fiókjához. Az első lépés is konfigurálhat egy kapcsolati karakterláncot, amely csatlakozni a tárfiókhoz fogjuk használni. Nézzük meg egy statikus változót ehhez.

```cpp
// Define the connection-string with your values.
const utility::string_t 
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Az Azure storage-fiókhoz való csatlakozást
Használhatja a **cloud_storage_account** osztályt határoz meg a Tárfiók adatait. A tárfiók adatait a tárolási kapcsolati karakterlánc lekéréséhez használja a **elemezni** metódust.

```cpp
// Retrieve storage account from connection string.    
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
A fájlok és könyvtárak az Azure fájlmegosztások a találhatók nevű tároló egy **megosztása**. A tárfiók lehet a kapacitásával lehetővé teszi tetszőleges számú megosztások. A megosztást és tartalmát hozzáférést szerezni kell használnia egy Azure fájlok ügyfél.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();
```

Az Azure Fileshoz-ügyfélprogrammal, majd szerezhet be egy hivatkozást egy megosztásra.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

A megosztás létrehozásához használja a **create_if_not_exists** metódusában a **cloud_file_share** objektum.

```cpp
if (share.create_if_not_exists()) {    
    std::wcout << U("New share created") << std::endl;    
}
```

Ezen a ponton **megosztása** nevű megosztáshoz egy hivatkozást tartalmaz **a minta-megosztás**.

## <a name="delete-an-azure-file-share"></a>Az Azure fájlmegosztások törlése
Egy megosztás törlése meghívásával történik a **delete_if_exists** cloud_file_share objektum metódus. Itt látható mintakód, amelyet, amely.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Könyvtár létrehozása
Tároló üzembe alkönyvtárak így ahelyett hogy ezek a gyökérkönyvtárban található fájlok rendezhetők. Az Azure Files hozhat létre a fiókját engedélyezi a könyvtárat. Az alábbi kód létrehoz egy nevű könyvtár **saját minta directory** alatt a gyökérkönyvtár, valamint a nevű **saját minta alkönyvtár**.

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
A könyvtár törlése mely egyszerű feladat, érdemes megjegyezni, hogy nem törölhető a fájlok továbbra is tartalmazó könyvtár vagy más címtárakban.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és könyvtárak az Azure fájlmegosztások számbavétele
Fájlok és könyvtárak egy megosztáson belüli listájának beszerzésével könnyen történik meghívásával **list_files_and_directories** a egy **cloud_file_directory** hivatkozás. Számos tulajdonságai és metódusai visszaadásakor eléréséhez **list_file_and_directory_item**, meg kell hívnia a **list_file_and_directory_item.as_file** metódus használatával kérje le a **cloud_file**  objektum, vagy a **list_file_and_directory_item.as_directory** metódus használatával kérje le a **cloud_file_directory** objektum.

A következő kód bemutatja, hogyan kérhető le és küldhető a megosztás a gyökérmappában lévő egyes elemek URI.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_diretory_result_iterator end_of_results;

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
Legalább az Azure fájlmegosztások egy gyökérkönyvtár fájlokat tároló is tartalmazza. Ebben a szakaszban megtudhatja, hogyan feltölteni a fájlt a helyi tároló megosztás gyökérkönyvtárában alakzatot.

Az első lépés egy feltöltés az beszerzése hivatkozni kell a könyvtárban, ahol kell tárolni. Ehhez hívja a **get_root_directory_reference** a megosztás metódusa.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Most, hogy a megosztás gyökérkönyvtárában hivatkozik, akkor fájlt tölthet fel rá. Ebben a példában feltölt egy fájlt, szöveg és a folyam.

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
Fájlok letöltéséhez először kérjen le egy hivatkozást, és, majd hívja a **download_to_stream** módszerrel kell továbbítania a tartalmát egy stream objektumra, amely meg tudja majd továbbra is fennáll egy helyi fájlba. Másik lehetőségként használhatja a **download_to_file** metódus egy fájl tartalmát egy helyi fájl letöltéséhez. Használhatja a **download_text** metódus egy szöveges karakterlánc egy fájlhoz, a tartalom letöltéséhez.

Az alábbi példában a **download_to_stream** és **download_text** módszerek letölti a fájlokat, amelyek a korábbi szakaszokban létrehozott bemutatásához.

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
Egy másik közös Azure fájlok művelet a fájl törlése. Az alábbi kód töröl egy fájlt, a-minta-fájl – 3 nevű tárolja a gyökérkönyvtárban.

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

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Egy Azure fájlmegosztás kvótájának (maximális méret) beállítása
Beállíthatja a kvótát (vagy maximális méretet) egy fájlmegosztáshoz GB-ban. Azt is ellenőrizheti, hogy aktuálisan mennyi adatot tárol a fájlmegosztás.

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
Létrehozhat egy közös hozzáférésű jogosultságkód (SAS) egy fájlmegosztáshoz vagy fájlhoz. Létrehozhat egy megosztott elérési házirendet is egy fájlmegosztáshoz, hogy kezelni tudja a közös hozzáférésű jogosultságkódokat. Azért érdemes létrehozni megosztott elérési házirendet, mert annak az eszközeivel vissza lehet hívni az SAS-t, amennyiben sérülne a biztonsága.

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
* [Az azure Storage szolgáltatás fájlminták c++] (https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Azure Storage Explorer](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)