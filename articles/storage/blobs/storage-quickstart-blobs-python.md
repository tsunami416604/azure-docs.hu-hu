---
title: Azure rövid útmutató – Blob létrehozása objektumtárban Python használatával | Microsoft Docs
description: Ebben a rövid útmutatóban egy tárfiókot és egy tárolót hoz létre egy objektumtárban (Blob Storage-fiókban). Majd a Storage Pythonhoz készült ügyféloldali kódtára segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: tamram
ms.openlocfilehash: 95b960245e00a353bb165e1f1d38db56bce62a8f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996724"
---
# <a name="quickstart-upload-download-and-list-blobs-with-python"></a>Gyors útmutató: Feltöltése, letöltése és a blobok listázása a Python használatával

A rövid útmutató azt ismerteti, hogyan használható a Python blokkblobok feltöltésére, letöltésére és listázására egy, az Azure Blob Storage-ban található tárolóban. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Győződjön meg arról, hogy telepítve van a következő további Előfeltételek:

* [Python](https://www.python.org/downloads/)
* [Azure Storage SDK for Python](https://github.com/Azure/azure-sdk-for-python)

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése
A rövid útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) egy egyszerű Python-alkalmazás.  

A [git](https://git-scm.com/) használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Ez a parancs a helyi git mappába klónozza az *Azure-Samples/storage-blobs-python-quickstart* adattárat. A Python-program futtatásához nyissa meg az *example.py* fájlt az adattár gyökérkönyvtárából.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása
Az alkalmazásban adja meg a tárfiók nevét és a fiókkulcsot, hogy létrehozhasson egy `BlockBlobService` objektumot. Nyissa meg az *example.py* fájlt az IDE Megoldáskezelőjéből. Az `accountname` és az `accountkey` értéket cserélje le a fiók nevére, illetve kulcsára. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Minta futtatása
Ez a minta egy tesztfájlt hoz létre a *Dokumentumok* mappában. A mintaprogram feltölti a tesztfájlt a Blob-tárolóba, listázza a tárolóban található blobokat, majd letölti a fájlt egy új néven. 

Első lépésként telepítse a függőségeket a `pip install` futtatásával:

    pip install azure-storage-blob

Ezután futtassa a mintákat. A következő kimenethez hasonló üzenetek jelennek meg:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Mielőtt továbblépne, ellenőrizze a két fájlt a *Dokumentumok* mappában. Ha megnyitja őket, láthatja, hogy megegyeznek.

Az [Azure Storage Explorert](https://storageexplorer.com) vagy egy ahhoz hasonló eszközt is használhat, ha szeretné a fájlt megtekinteni a blobtárolóban. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat. 

A fájlok ellenőrzése után nyomja le bármelyik billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez. Most, hogy tisztában van a minta működésével, nyissa meg az *example.py* fájlt, és tekintse meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

Tekintsük át a mintakódot, hogy értelmezzük annak működését.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz
Először létre kell hozni a referenciákat a Blob Storage eléréséhez és kezeléséhez használt objektumokhoz. Ezek az objektumok egymásra épülnek, és mindegyiket a listában utánuk következő használja.

* Hozza létre a **BlockBlobService** objektum példányát, amely a tárfiók Blob szolgáltatására mutat. 

* Hozza létre a **CloudBlobContainer** objektum példányát. Ez azt a tárolót jelöli, amelyhez Ön hozzáfér. A tárolók a blobok csoportosítására használhatók, hasonlóan ahhoz, ahogyan a számítógépen a mappákkal rendszerezi a fájlokat.

A felhőbeli blobtároló létrehozása után hozza létre a **CloudBlockBlob** objektum példányát, amely pontosan arra a blobra mutat, amelyre kíváncsi. Ezután elvégezheti a feltöltési, letöltési, másolási vagy egyéb műveleteket.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és a blobok elnevezésével kapcsolatos további információért tekintse meg a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával foglalkozó cikket](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Ebben a szakaszban létre fogja hozni az objektumok példányát és egy új tárolót, majd beállítja annak engedélyeit úgy, hogy a blobok nyilvánosak legyenek. A tároló neve **quickstartblobs**. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A leggyakrabban használt elemek a blokkblobok, és ez a gyorsútmutató is ezeket használja.  

Ha szeretne feltölteni egy fájlt a blobba, szüksége lesz a fájl teljes útvonalára, amelyet a helyi meghajtón található könyvtárnév és fájlnév összefűzésével kap meg. Ezt követően feltöltheti a fájlt a megadott útvonalra a `create\_blob\_from\_path` metódus használatával. 

A mintakód létrehoz egy, a fel- és letöltéshez használatos helyi fájlt, a `file\_path\_to\_file` jelöli a feltöltendő fájl elérési útját, a `local\_file\_name` pedig a blob nevét. A következő példa feltölti a fájlt a **quickstartblobs** nevű tárolóba.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

A Blob-tárolóval többféle feltöltési módszer használható. Ha például memóriastreamje van, használhatja a `create\_blob\_from\_stream` metódust a `create\_blob\_from\_path` helyett. 

A blokkblobok legfeljebb 4,7 TB méretűek lehetnek, és az Excel-munkafüzetektől kezdve a nagyméretű videofájlokig bármit tartalmazhatnak. A lapblobok elsősorban az IaaS virtuális gépek biztonsági mentéséhez szükséges VHD-fájlokhoz használhatók. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A blobtárolókban tárolt objektumok a legtöbb esetben blokkblobok.

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Szerezze be a tárolóban található fájlok listáját a `list_blobs` metódussal. A metódus egy generátort ad vissza. A következő kód lekéri a blobok listáját, &mdash;majd végighalad rajtuk&mdash;, és megjeleníti a tárolóban talált blobok nevét.  

```python
# List the blobs in the container
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>A blobok letöltése

Blobokat a helyi lemezre a `the get\_blob\_to\_path` metódussal tölthet le. A következő kód letölti az útmutató korábbi fejezetében feltöltött blobot. A blob nevéhez hozzáadja a *_DOWNLOADED* (letöltve) utótagot, így mindkét fájlt láthatja majd a helyi lemezen. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az ebben a rövid útmutatóban feltöltött blobokra, a teljes tárolót törölheti a `delete\_container` metódussal. Az egyes fájlok törléséhez használja a `delete\_blob` metódust.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Blobokkal rendelkező Python-alkalmazások fejlesztéséhez használható forrásanyagok

Blob Storage-tárolókat alkalmazó Python-alkalmazások fejlesztéséhez tekintse át az alábbi további forrásanyagokat is:

### <a name="binaries-and-source-code"></a>Bináris fájlok és forráskód

- Az Azure Storage-hoz tartozó [Python ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-python) megtekintheti, letöltheti és telepítheti a GitHubról.

### <a name="client-library-reference-and-samples"></a>Ügyféloldali kódtár – referencia és minták

- A Python ügyféloldali kódtárral kapcsolatos további információért lásd [a Python API-referenciáját](https://docs.microsoft.com/python/api/overview/azure/storage).
- Tekintse át a Python ügyféloldali kódtár használatával írt [Blob Storage-mintákat](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob).

## <a name="next-steps"></a>További lépések
 
Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a Python használatával. Ha bővebb információra van szüksége a Blob Storage használatával kapcsolatban, lépjen tovább a Blob Storage használati útmutatójára.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](./storage-python-how-to-use-blob-storage.md)
 
További információk a Storage Explorerről és a blobokról: [Azure Blob Storage-erőforrások kezelése a Storage Explorer használatával](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
