---
title: "Azure gyors üzembe helyezés – az Azure Blob storage használatának a Python átviteli objektumok |} Microsoft Docs"
description: "Gyorsan elsajátíthatják átvitele az objektumok Azure Blob storage használatának a Python és a"
services: storage
documentationcenter: storage
author: ruthogunnnaike
manager: cwatson
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/12/2017
ms.author: v-ruogun
ms.openlocfilehash: 4a197af41f5450d84e1c18e15198d1febb02bab1
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-python"></a>Átviteli objektumok az Azure Blob Storage tárolóban pythonos környezetekben
A gyors üzembe helyezés elsajátíthatja Python segítségével töltse fel, töltse le, és az Azure Blob storage tárolója blokk blobok listázása. 

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez: 
* Telepítés [Python](https://www.python.org/downloads/)
* Töltse le és telepítse [Python-hez készült Azure Storage szolgáltatás SDK](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python). 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése
A [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) alapvető Python-alkalmazás a gyors üzembe helyezés használatban van.  

Használjon [git](https://git-scm.com/) letölteni az alkalmazást a fejlesztési környezet egy példányát. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Ez a parancs klónokat a tárházat a helyi git-mappába. Nyissa meg a Python programot, keresse meg a storage-BLOB-python-gyors üzembe helyezés mappát, és example.py fájlt.  

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati karakterlánc konfigurálása
Az alkalmazásban, meg kell adnia a tárfiók nevét és a fiók kulcsának létrehozása egy `BlockBlobService` objektum. Nyissa meg a `example.py` a Megoldáskezelőben a ide a fájlt. Cserélje le a **accountname** és **accountkey** értékeket a fióknevet és kulcsot. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>A minta futtatásához
Ez a minta létrehoz egy tesztelési fájlt a "Dokumentumok" mappában. A minta program teszt feltölti a Blob storage, a tárolóban lévő blobok sorolja fel, és letölti a fájlt új néven. 

Futtassa a mintát. A következő egy példa a kimenet az alkalmazás futtatásakor visszaadott kimenete:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
A folytatáshoz bármely billentyű lenyomásával, a minta program törli a tároló és a fájlok. A folytatás előtt ellenőrizze a "Dokumentumok" mappában a két fájlt. Nyissa meg őket, és tekintse meg a megegyezik azzal.

Például egy eszköz is használhatja a [Azure Tártallózó](http://storageexplorer.com) megtekinthetik a fájlokat a Blob Storage tárolóban. Azure Tártallózó egy ingyenes platformfüggetlen eszköz, amely lehetővé teszi a tárfiók adatait. 

Miután ellenőrizte, hogy a fájlokat, kattintson a bemutató befejeződését, és törölje a tesztfájlok bármelyik billentyűt. Most, hogy ismeri a minta funkciója, nyissa meg a example.py fájlt nézze meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód ismertetése

Ezután azt végezze el a mintakódot, hogy megismerheti, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>A tárolási objektum mutató hivatkozások beolvasása
Az első lépés férhessen hozzá és felügyelhesse a Blob storage használt objektumok hivatkozásainak létrehozásához. Ezek az objektumok egymástól hozza létre, és minden egyes használja a következő egy, a listában.

* Hozható létre a **BlockBlobService** objektum, amely a tárfiókban lévő a Blob szolgáltatás mutat. 

* Hozható létre a **CloudBlobContainer** objektum, a tároló elérésére. Tárolók használatával rendezheti a blobok, például a mappák használ a fájlok rendszerezéséhez a számítógépen.

Miután a felhő Blob tároló, példányosítható a **CloudBlockBlob** objektum, amelyen szeretné, és hajtsa végre a műveletek, például a feltöltés, letöltés, és másolja az adott blobra mutató.

> [!IMPORTANT]
> A tároló nevének kisbetűnek kell lennie. Lásd: [elnevezési és hivatkozó tárolók, Blobok és metaadatok](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) tároló és a blob nevének további információt.

Ebben a szakaszban az objektumok példányosítható, hozzon létre egy új tároló, és majd engedélyek beállítása a tárolón úgy, hogy a blobok nyilvános. A tároló neve **quickstartblobs**. 

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

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Blokkblobok a leggyakrabban használt, és ez a gyors üzembe helyezés használttól.  

Feltölteni a fájlt egy blobba, hozza ki a fájl elérési útját a könyvtár nevét és a fájlnév csatlakozás a helyi meghajtón. A megadott elérési úthoz történő majd feltölteni a fájlt a **létrehozása\_blob\_a\_elérési** metódust. 

Példakód létrehoz egy helyi fájlt a feltöltés és letöltés, mint a feltölteni kívánt fájl tárolására használt **fájl\_elérési\_való\_fájl** és a nevét, a BLOB **helyi\_fájl\_neve**. Az alábbi példa feltölt a fájlt a tároló neve **quickstartblobs**.

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

Többféleképpen feltöltés, a Blob storage használata. Például ha egy memóriafolyam, használhatja a **létrehozása\_blob\_a\_adatfolyam** metódus helyett a **létrehozása\_blob\_a\_elérési**. 

Blokkblobok 4.7 TB nagyságú lehet, és bármilyen, az Excel-táblázatok nagy videó fájlok. Lapblobokat elsősorban az infrastruktúra-szolgáltatási virtuális gépek biztonsági használt VHD-fájlokat. Hozzáfűző blobok használt naplózást, például a kívánt fájlra és majd hozzáadni további információkat. A Blob storage-ban tárolt legtöbb objektum blokkblobokat.

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A tárolót használja a fájlok listáját a **list_blobs** metódust. Ez a módszer egy generátor adja vissza. A következő kódot a bináris objektumok listájának beolvasása, majd végighalad őket, a tárolóban található blobok nevének megjelenítése.  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>A blobok letöltése

Blobok letöltése a helyi lemezek használata a **beolvasása\_blob\_való\_elérési** metódust. A következő kódot az előző részben feltöltött blob tölti le. "_DOWNLOADED" van adva egy utótagot a blob nevének hívjuk fel a helyi lemezen fájlt. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége a blobok a gyors üzembe helyezés a feltöltés, a teljes tárolóhoz használatával törölheti a **törlése\_tároló**. Ha a fájlok már nem szükséges, akkor használhatja a **törlése\_blob** metódus törli a fájlokat.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="next-steps"></a>Következő lépések
 
A gyors üzembe helyezés megtanulta, hogyan viheti át a fájlok helyi lemezre és az Azure blob storage használatának a Python közötti. További információt a blob storage használata, továbbra is a Blob-tároló útmutató.

> [!div class="nextstepaction"]
> [A BLOB Storage műveletek útmutató](./storage-python-how-to-use-blob-storage.md)
 

A Tártallózó alkalmazással és a Blobok kapcsolatos további információkért lásd: [kezelése Azure Blob storage-erőforrások a Tártallózó alkalmazással](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
