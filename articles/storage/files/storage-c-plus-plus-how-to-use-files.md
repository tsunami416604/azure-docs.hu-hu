---
title: Fejlesztés az Azure Files c++ |} A Microsoft Docs
description: Megtudhatja, hogyan fejleszthet c++ nyelven írt alkalmazások és szolgáltatások, amelyek az Azure Files használatának a fájladatok tárolásához.
services: storage
author: renashahmsft
ms.service: storage
ms.topic: article
ms.date: 09/19/2017
ms.author: renashahmsft
ms.component: files
ms.openlocfilehash: f2b6a03ce4e2c03f148fda67cfad1c4d51636b7a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234969"
---
# <a name="develop-for-azure-files-with-c"></a>Fejlesztés az Azure Files c++
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ebben az oktatóanyagban megismerheti, hogyan alapszintű műveletek végrehajtása az Azure Files lesz. C++ nyelven írt mintát, keresztül, megtudhatja, hogyan-megosztásokat és -könyvtárak létrehozása, feltöltése, listázása és törölje a fájlokat. Ha most ismerkedik az Azure Files, a minták hasznos információkat az alábbi szakaszok a fogalmak keresztül lesz.

* Hozzon létre vagy töröljön az Azure-fájlmegosztások
* Hozzon létre, és törölje a címtárakat
* Fájlok és mappák az Azure-fájlmegosztások számbavétele
* Feltöltésére, letöltésére és fájl törlése
* Állítsa be az Azure-fájlmegosztás kvótájának (maximális méret)
* Közös hozzáférésű jogosultságkód (SAS-kulcs) létrehozása egy, a megosztásban meghatározott megosztott elérési házirendet használó fájlhoz.

> [!Note]  
> Az Azure Files SMB-n keresztül lehet elérni, mivel, lehetséges, hogy a standard C++ i/o-osztályokat és funkciók használatával az Azure-fájlmegosztás eléréséhez egyszerű alkalmazások írására. Ez a cikk azt ismerteti, hogyan írhat alkalmazásokat, amelyek használják az Azure Storage C++ SDK-t használ a [fájl REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) felvenni a kapcsolatot az Azure Files.

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása
A minták összeállításához, szüksége lesz az Azure Storage ügyféloldali kódtár 2.4.0 telepítheti a C++. Meg is létrehozott egy Azure storage-fiókot.

Telepítse az Azure Storage-kliens 2.4.0 c++, a következő módszerek egyikét használhatja:

* **Linux:** kövesse az utasításokat adott a [Azure Storage ügyféloldali kódtára a C++ információs](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) lapot.
* **Windows:** a Visual Studióban kattintson **eszközök &gt; NuGet-Csomagkezelő &gt; Package Manager Console**. Írja be a következő parancsot a [NuGet Package Manager console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) nyomja le az ENTER **ENTER**.
  
```
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Állítsa be alkalmazását az Azure Files használata
Adja hozzá a következő utasításokat a felső részén a C++ forrásfájlt, ahol szeretné az Azure Files kezelése tartalmazza:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Egy Azure storage kapcsolati karakterlánc beállítása
A File storage használatával kell csatlakoznia az Azure storage-fiókba. Az első lépés egy kapcsolati karakterláncot, amely segítségével a tárfiókhoz való kapcsolódás konfigurálása lenne. Határozzon meg egy statikus változó valósítható meg.

```cpp
// Define the connection-string with your values.
const utility::string_t 
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Az Azure storage-fiók csatlakoztatása
Használhatja a **cloud_storage_account** osztály, amely a Storage-fiók adatait jelöli. A Storage-fiók információit a **parse** metódussal kérheti le a Storage kapcsolati sztringjéből.

```cpp
// Retrieve storage account from connection string.    
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
A fájlok és könyvtárak az Azure-fájlmegosztások nevű tárolóban található egy **megosztása**. A storage-fiókot, a fiók kapacitás lehetővé teszi, hogy annyi megosztások rendelkezhet. Szerezzen be egy megosztást és tartalmát a hozzáférést, az Azure Files-ügyfél használata kell.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();
```

Az Azure Files-ügyfelet használ, majd szerezhet be egy hivatkozást a megosztáshoz.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

A megosztás létrehozásához használja a **create_if_not_exists** módszere a **cloud_file_share** objektum.

```cpp
if (share.create_if_not_exists()) {    
    std::wcout << U("New share created") << std::endl;    
}
```

Ezen a ponton **megosztása** egy hivatkozást a megosztásban található **saját minta megosztás**.

## <a name="delete-an-azure-file-share"></a>Azure-fájlmegosztás törlése
Egy megosztás törlésekor történik meghívásával a **delete_if_exists** metódus cloud_file_share objektumon. Itt látható, amely, amely mintakódot.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Könyvtár létrehozása
Tároló található alkönyvtárai nem ezek mindegyike a gyökérmappában lévő fájlokat azáltal rendezhetők. Az Azure Files lehetővé teszi, hogy hozzon létre annyi könyvtárakon, a fiók lehetővé teszi. Az alábbi kódot nevű könyvtárat hoz létre **my-sample-directory** alatt a gyökérkönyvtár, valamint nevű alkönyvtárban **saját minta alkönyvtár**.

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
Egy egyszerű feladat, egy könyvtár törlése folyamatban, bár Megjegyzendő, hogy nem törölhető a fájlok továbbra is tartalmazó könyvtárba, vagy más címtárakban.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és mappák az Azure-fájlmegosztások számbavétele
Fájlok és könyvtárak a megosztáson belüli beszerzése könnyedén elvégezhető meghívásával **list_files_and_directories** a egy **cloud_file_directory** hivatkozást. Számos tulajdonság és metódus visszaadásakor eléréséhez **list_file_and_directory_item**, meg kell hívni a **list_file_and_directory_item.as_file** metódus lekéréséhez egy **cloud_file**  objektumot, vagy a **list_file_and_directory_item.as_directory** metódus lekéréséhez egy **cloud_file_directory** objektum.

A következő kód bemutatja, hogyan kérhető le és küldhető a megosztás a gyökérmappában lévő egyes elemek URI azonosítója.

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
Legalább az az Azure-fájlmegosztások fájlokat tároló is gyökérkönyvtár tartalmazza. Ebben a szakaszban megismerheti, hogyan feltölthet egy fájlt a helyi tárolóból gyökérkönyvtárában található egy megosztást az alakzatot kell.

Az első lépés egy fájl feltöltése az szerezzen be egy hivatkozást a könyvtárban, ahol kell tárolni. Meghívásával ehhez a **get_root_directory_reference** metódus a megosztott objektum.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Most, hogy a megosztás gyökérkönyvtárában egy hivatkozást, azt egy fájlt tölthet fel. Ebben a példában egy fájlból, szöveg és streamből tölt fel.

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
Fájlok letöltéséhez először kérjen le egy hivatkozást, és ezután hívja meg a **download_to_stream** módszerrel kell továbbítania a fájl tartalmát egy stream objektumra, amely megőrizhet egy helyi fájlba. Másik lehetőségként használhatja a **download_to_file** metódus letölteni egy fájl tartalmát egy helyi fájlba. Használhatja a **download_text** metódus egy szöveges karakterlánc egy fájl tartalmának letöltéséhez.

Az alábbi példában a **download_to_stream** és **download_text** metódusok használatával mutatja be, letölti a fájlokat, a korábbi szakaszokban létrehozott.

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
Egy másik gyakori Azure Files-művelet a fájl törlése. Az alábbi kód egy fájlt a saját-minta-fájl – 3 tárolja a gyökérkönyvtárban törli.

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

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Állítsa be az Azure-fájlmegosztás kvótájának (maximális méret)
Beállíthatja a kvótát (vagy maximális méretet) egy fájlmegosztáshoz, GB-ban. Azt is ellenőrizheti, hogy aktuálisan mennyi adatot tárol a fájlmegosztás.

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
Létrehozhat egy közös hozzáférésű jogosultságkód (SAS) egy fájlmegosztáshoz vagy egyetlen fájlra. Létrehozhat egy megosztott elérési házirendet is egy fájlmegosztáshoz, hogy kezelni tudja a közös hozzáférésű jogosultságkódokat. Azért érdemes létrehozni megosztott elérési házirendet, mert annak az eszközeivel vissza lehet hívni az SAS-t, amennyiben sérülne a biztonsága.

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
* [Szolgáltatás fájlminták az Azure Storage c++](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)