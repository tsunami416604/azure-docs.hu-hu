---
title: 'Rövid útmutató: Az Azure Blob storage ügyfélkódtár 2.1-es használata Pythonhoz'
description: Ebben a rövid útmutatóban egy tárfiókot és egy tárolót hoz létre egy objektumtárban (Blob Storage-fiókban). Ezután a Storage Client library v2.1 python-t használja egy blob azure Storage-ba való feltöltéséhez, egy blob letöltéséhez, és a blobok egy tárolóban való listázásához.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019
ms.openlocfilehash: 4b0248604b6e9189d5275177a4960e4c352e8215
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76906442"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Gyorsútmutató: Blobok kezelése a Python v2.1 SDK-val

Ebben a rövid útmutatóban megtudhatja, hogy a Python használatával kezelheti a blobokat. A blobok olyan objektumok, amelyek nagy mennyiségű szöveges vagy bináris adatot képesek tárolni, beleértve a képeket, dokumentumokat, adatfolyam-továbbítási és archiválási adatokat. Blobokat fog feltölteni, letölteni és listázni, és tárolókat hozhat létre és törölhet.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Storage-fiók. [Hozzon létre egy tárfiókot.](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/).
- [Azure Storage SDK pythonhoz](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) egy egyszerű Python-alkalmazás.  

A következő [git](https://git-scm.com/) paranccsal töltse le az alkalmazást a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

A Python-program áttekintéséhez nyissa meg a *example.py* fájlt a tárház gyökerében.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

Az alkalmazásban adja meg a tárfiók nevét és a fiókkulcsot, hogy létrehozhasson egy `BlockBlobService` objektumot.

1. Nyissa meg az *example.py* fájlt az IDE Megoldáskezelőjéből.

1. Cserélje `accountname` le `accountkey` a és az értékeket a tárfiók nevére és kulcsára:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Mentse és zárja be a fájlt.

## <a name="run-the-sample"></a>Minta futtatása

A mintaprogram létrehoz egy tesztfájlt a *Dokumentumok* mappában, feltölti a fájlt a Blob storage-ba, felsorolja a fájlokban lévő blobokat, és új néven tölti le a fájlt.

1. Telepítse a függőségeket:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Nyissa meg a mintaalkalmazást:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Minta futtatása:

    ```console
    python example.py
    ```

    A következő kimenethez hasonló üzenetek jelennek meg:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. A folytatás előtt nyissa meg a *Dokumentumok* mappát, és ellenőrizze a két fájlt.

    * *QuickStart_\<univerzálisan egyedi azonosító\>*
    * *QuickStart_\<univerzálisan egyedi\>azonosító _DOWNLOADED*

1. Ha megnyitja őket, láthatja, hogy megegyeznek.

    Olyan eszközt is használhat, mint az [Azure Storage Explorer.](https://storageexplorer.com) A Blob storage-ban lévő fájlok megtekintésére alkalmas. Az Azure Storage Explorer egy ingyenes platformfüggetlen eszköz, amely lehetővé teszi a tárfiók adatainak elérését. 

1. Miután megvizsgálta a fájlokat, nyomja meg bármelyik billentyűt a minta befejezéséhez, és törölje a tesztfájlokat.

## <a name="learn-about-the-sample-code"></a>További információ a mintakódról

Most, hogy már tudja, mit csinál a minta, nyissa meg a *example.py* fájlt a kód megnézetéhez.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz

Ebben a szakaszban létre fogja hozni az objektumok példányát és egy új tárolót, majd beállítja annak engedélyeit úgy, hogy a blobok nyilvánosak legyenek. Hívd a konténert. `quickstartblobs` 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

Először létre kell hozni a referenciákat a Blob Storage eléréséhez és kezeléséhez használt objektumokhoz. Ezek az objektumok egymásra épülnek, és mindegyiket a listában utánuk következő használja.

* Hozza létre a **BlockBlobService** objektum példányát, amely a tárfiók Blob szolgáltatására mutat. 

* Hozza létre a **CloudBlobContainer** objektum példányát. Ez azt a tárolót jelöli, amelyhez Ön hozzáfér. A rendszer tárolók segítségével rendszerezi a blobokat, ahogy a számítógépen lévő mappákat használja a fájlok rendszerezéséhez.

A felhőbeli blobtároló létrehozása után hozza létre a **CloudBlockBlob** objektum példányát, amely pontosan arra a blobra mutat, amelyre kíváncsi. Ezután elvégezheti a feltöltési, letöltési, másolási vagy egyéb műveleteket.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tároló- és blobnevekről további információt a [Tárolók, blobok és metaadatok elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)című témakörben talál.

### <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A blokkblobok legfeljebb 4,7 TB méretűek lehetnek, és az Excel-munkafüzetektől kezdve a nagyméretű videofájlokig bármit tartalmazhatnak. A hozzáfűző blobok használatával naplózhatja, ha egy fájlba szeretne írni, majd további információkat szeretne hozzáadni. A lapblobokat elsősorban a virtuális merevlemez (VHD) fájljaihasználják, amelyek szolgáltatás virtuális gépként (IaaS virtuális gépek) támasztják vissza az infrastruktúrát. A leggyakrabban használt elemek a blokkblobok. Ez a rövid útmutató blokkblobokat használ.

Ha szeretne feltölteni egy fájlt a blobba, szüksége lesz a fájl teljes útvonalára, amelyet a helyi meghajtón található könyvtárnév és fájlnév összefűzésével kap meg. Ezt követően feltöltheti a fájlt a megadott útvonalra a `create_blob_from_path` metódus használatával. 

A mintakód létrehoz egy helyi fájlt, amelyet a rendszer a feltöltéshez és a letöltéshez használ, és tárolja a rendszer által feltöltött fájlt *full_path_to_file* és a blob nevét *local_file_name*. Ez a példa feltölti a `quickstartblobs`fájlt a tárolónevű:

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

A Blob-tárolóval többféle feltöltési módszer használható. Ha például memóriastreamje van, használhatja a `create_blob_from_stream` metódust a `create_blob_from_path` helyett. 

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A következő kód `generator` létrehoz `list_blobs` egy a metódushoz. A kód végighalad a tárolóban lévő blobok listáján, és kinyomtatja a nevüket a konzolra.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>A blobok letöltése


Blobok letöltése a helyi `get_blob_to_path` lemezre a módszerrel.
A következő kód letölti a korábban feltöltött blobot. A rendszer *hozzáfűzi _DOWNLOADED* a blob nevét, így mindkét fájlt láthatja a helyi lemezen.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az ebben a rövid útmutatóban feltöltött blobokra, a teljes tárolót törölheti a `delete_container` metódussal. Az egyes fájlok törléséhez használja a `delete_blob` metódust.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Blobokkal rendelkező Python-alkalmazások fejlesztéséhez használható forrásanyagok

A Python-fejlesztés a Blob storage szolgáltatással kapcsolatos további információkért tekintse meg az alábbi további erőforrásokat:

### <a name="binaries-and-source-code"></a>Bináris fájlok és forráskód

- Az Azure Storage-hoz tartozó [Python ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-python) megtekintheti, letöltheti és telepítheti a GitHubról.

### <a name="client-library-reference-and-samples"></a>Ügyféloldali kódtár – referencia és minták

- A Python-ügyfélkódtárról az [Azure Storage-kódtárak pythonhoz](https://docs.microsoft.com/python/api/overview/azure/storage)című témakörben található.
- Tekintse át a Python ügyféloldali kódtár használatával írt [Blob Storage-mintákat](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob).

## <a name="next-steps"></a>További lépések
 
Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a Python használatával. 

A Storage Explorer ről és a Blobs ról az [Azure Blob storage-erőforrások kezelése a Storage Explorer segítségével](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)című témakörben tud többet.
