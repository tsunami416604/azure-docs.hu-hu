---
title: Azure rövid útmutató – Blobok feltöltése, letöltése és listázása az Azure Storage-ban a Ruby használatával | Microsoft Docs
description: Ebben a rövid útmutatóban egy tárfiókot és egy tárolót hoz létre. Majd a Storage Rubyhoz készült ügyféloldali kódtára segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: seguler
ms.openlocfilehash: 493da815b670c15bb892fa0be750c684cab70cb9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-ruby"></a>Rövid útmutató: blobok feltöltése, letöltése és listázása a Ruby használatával

A rövid útmutató azt ismerteti, hogyan használható a Ruby blokkblobok feltöltésére, letöltésére és listázására egy, az Azure Blob Storage-ban található tárolóban. 

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez: 
* Telepítse a [Rubyt](https://www.ruby-lang.org/en/downloads/)
* Telepítse a [Rubyhoz készült Azure Storage-kódtárat](https://docs.microsoft.com/azure/storage/blobs/storage-ruby-how-to-use-blob-storage#configure-your-application-to-access-storage) a rubygem csomag használatával. 

```
gem install azure-storage-blob
```

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése
A rövid útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) egy egyszerű Ruby-alkalmazás.  

A [git](https://git-scm.com/) használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

Ez a parancs a helyi git mappába klónozza az adattárat. A Ruby mintaalkalmazás megnyitásához keresse meg a storage-blobs-ruby-quickstart mappát, és nyissa meg az example.rb fájlt.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati karakterlánc konfigurálása
Az alkalmazásban meg kell adnia a tárfiók nevét és a fiókkulcsot, hogy létrehozhasson egy `BlobService` példányt az alkalmazásból. Nyissa meg az `example.rb` fájlt az IDE Megoldáskezelőjéből. Az **accountname** és az **accountkey** értékeket cserélje le a fiók nevére, illetve kulcsára. 

```ruby 
blob_client = Azure::Storage::Blob::BlobService.create(
            storage_account_name: account_name,
            storage_access_key: account_key
          )
```

## <a name="run-the-sample"></a>Minta futtatása
Ez a minta egy tesztfájlt hoz létre a „Dokumentumok” mappában. A mintaprogram feltölti a tesztfájlt a Blob-tárolóba, listázza a tárolóban található blobokat, majd letölti a fájlt egy új néven. 

Futtassa a mintát. Az alábbi kimenet példa az alkalmazás futtatásakor kapott kimenetre:
  
```
Creating a container: quickstartblobs7b278be3-a0dd-438b-b9cc-473401f0c0e8

Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Amikor lenyom egy billentyűt a folytatáshoz, a mintaprogram törli a Storage-tárolót és a fájlokat. Mielőtt továbblépne, ellenőrizze a két fájlt a „Dokumentumok” mappában. Ha megnyitja őket, láthatja, hogy megegyeznek.

Az [Azure Storage Explorert](http://storageexplorer.com) vagy egy ahhoz hasonló eszközt is használhat a fájl megtekintéséhez a Blob-tárolóban. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat. 

A fájlok ellenőrzése után nyomja le bármelyik billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez. Most, hogy tisztában van a minta működésével, nyissa meg az example.rb fájlt, és tekintse meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz
Az első teendő a referenciák létrehozása a Blob-tárolóhoz való hozzáféréshez és kezeléséhez használt objektumokhoz. Ezek az objektumok egymásra épülnek, és mindegyiket a listában utánuk következő használja.

* Hozzon létre egy példányt az Azure Storage **BlobService** objektumából a kapcsolati hitelesítő adatok beállításához. 
* Hozza létre a **Container** objektumot. Ez azt a tárolót képviseli, amelyhez Ön hozzáfér. A tárolók a blobok csoportosítására használhatók, hasonlóan ahhoz, ahogyan a számítógépen a mappákkal rendszerezi a fájlokat.

A felhőbeli blobtároló létrehozása után létrehozhatja a **Block** blobobjektumot, amely pontosan arra a blobra mutat, amelyre kíváncsi, és elvégezheti a feltöltési, letöltési, másolási vagy egyéb műveleteket.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos részletekért lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

Ebben a szakaszban létre fogja hozni az Azure Storage-kliens és a blobszolgáltatás objektumának egy példányát, valamint egy új tárolót, majd beállítja annak engedélyeit úgy, hogy a blobok nyilvánosak legyenek. A tároló neve **quickstartblobs**. 

```ruby 
# Create a BlobService object
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs' + SecureRandom.uuid
container = blob_client.create_container(container_name)   

# Set the permission so the blobs are public.
blob_client.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A leggyakrabban használt elemek a blokkblobok, és ez a rövid útmutató is ezeket használja.  

Ha szeretne feltölteni egy fájlt a blobba, szüksége lesz a fájl teljes útvonalára, amelyet a helyi meghajtón található könyvtárnév és fájlnév összefűzésével kap meg. Ezt követően feltöltheti a fájlt a megadott útvonalra a **create\_block\_blob()** metódus használatával. 

A mintakód létrehoz egy, a fel- és letöltéshez használatos helyi fájlt, és úgy tárolja el, hogy az a **file\_path\_to\_file** névvel és a **local\_file\_name** blobnévvel legyen feltöltve. A következő példa feltölti a fájlt a **quickstartblobs** nevű tárolóba.

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_client.create_block_blob(container.name, local_file_name, full_path_to_file)
```

Egy blokkblob tartalmának részleges frissítéséhez használja a **create\_block\_list()** metódust. A blokkblobok legfeljebb 4,7 TB méretűek lehetnek, és az Excel-munkafüzetektől kezdve a nagyméretű videofájlokig bármit tartalmazhatnak. A lapblobok elsősorban az IaaS virtuális gépek biztonsági mentéséhez szükséges VHD-fájlokhoz használatosak. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A hozzáfűző blobokat egyetlen írót tartalmazó modellek esetében érdemes használni. A blobtárolókban tárolt objektumok a legtöbb esetben blokkblobok.

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A tárolóban található fájlok listáját a **list\_blobs()** metódus használatával kérheti le. A következő kód lekéri a blobok listáját, majd végighalad rajtuk, és megjeleníti a tárolóban talált blobok nevét.  

```ruby
# List the blobs in the container
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-the-blobs"></a>A blobok letöltése

Blobokat a **get\_blob()** metódussal tölthet le a helyi lemezre. A következő kód letölti az útmutató korábbi fejezetében feltöltött blobot. A blob nevéhez hozzáadja a „_DOWNLOADED” (Letöltve) utótagot, így mindkét fájlt láthatja majd a helyi lemezen. 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_client.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az ebben a rövid útmutatóban feltöltött blobokra, a teljes tárolót törölheti a **delete\_container()** metódussal. Ha már nincs szüksége a létrehozott fájlokra, a **delete\_blob()** metódus használatával törölheti őket.

```ruby
# Clean up resources. This includes the container and the temp files
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```
## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Blobokkal rendelkező Ruby-alkalmazások fejlesztéséhez használható forrásanyagok

Blob Storage-tárolókat alkalmazó Ruby-alkalmazások fejlesztéséhez tekintse át az alábbi további forrásanyagokat is:

- Az Azure Storage-hoz készült [Ruby ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-ruby) a GitHubon tekintheti meg és töltheti le.
- Tekintse át a Ruby ügyféloldali kódtár használatával írt [Blob Storage-mintákat](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=ruby&term=blob).

## <a name="next-steps"></a>További lépések
 
Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a Ruby használatával. Ha bővebb információra van szüksége a Blob Storage használatával kapcsolatban, lépjen tovább a Blob Storage használati útmutatójára.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](./storage-ruby-how-to-use-blob-storage.md)


További információk a Storage Explorerről és a blobokról: [Azure Blob Storage-erőforrások kezelése a Storage Explorer használatával](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
