---
title: 'Rövid útmutató: Azure Blob storage-tár v12 - Python'
description: Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Azure Blob storage-beli ügyfélkód12-es python-verziójával egy tárolót és egy blobot a Blob (objektum) tárolójában. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 8daf7380e859cd2f9b5890c716f7b7d95e6c3fe4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061340"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Gyorsútmutató: Blobok kezelése python v12 SDK-val

Ebben a rövid útmutatóban megtudhatja, hogy a Python használatával kezelheti a blobokat. A blobok olyan objektumok, amelyek nagy mennyiségű szöveges vagy bináris adatot képesek tárolni, beleértve a képeket, dokumentumokat, adatfolyam-továbbítási és archiválási adatokat. Blobokat fog feltölteni, letölteni és listázni, és tárolókat hozhat létre és törölhet.

[API-referenciadokumentáció–](/python/api/azure-storage-blob) | [könyvtár forráskódjának](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [csomagja (Python-csomagindex)](https://pypi.org/project/azure-storage-blob/) | [mintái](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Storage-fiók. [Hozzon létre egy tárfiókot.](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) 2.7, 3.5 vagy újabb.

> [!NOTE]
> Az előző SDK-verzió ismerkedéséhez olvassa el a [Gyorsútmutató: Blobok kezelése python-v2.1-es SDK-val](storage-quickstart-blobs-python-legacy.md)című témakört.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti a projekt előkészítése az Azure Blob storage-ügyfélkódtár v12 python-hoz való használata.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *Python-alkalmazás nevű blob-quickstart-v12.*

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Váltson át az újonnan létrehozott *blob-quickstart-v12* könyvtárra.

    ```console
    cd blob-quickstart-v12
    ```

1. A blob-quickstart-v12 könyvtár oldalán hozzon létre egy másik *könyvtárat,* az úgynevezett data .In side the *blob-quickstart-v12* directory, create another directory called data . Ez az, ahol a blob adatfájlok jönnek létre és tárolják.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>A csomag telepítése

Miközben továbbra is az alkalmazás könyvtárában, telepítse az Azure `pip install` Blob storage ügyfélrendszertár python-csomag a parancs használatával.

```console
pip install azure-storage-blob
```

Ez a parancs telepíti az Azure Blob storage ügyfélkódtár python csomag és az összes olyan kódtárak, amelyektől függ. Ebben az esetben ez csak az Azure core library for Python.

### <a name="set-up-the-app-framework"></a>Az alkalmazáskeretrendszer beállítása

A projekt könyvtárából:

1. Új szövegfájl megnyitása a kódszerkesztőben
1. Utasítások `import` hozzáadása
1. A program struktúrájának létrehozása, beleértve az alapvető kivételkezelést

    Itt a kód:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Mentse az új fájlt *blob-quickstart-v12.py* a *blob-quickstart-v12* könyvtárba.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Blob storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy definíciót, például szöveges vagy bináris adatok. A Blob storage háromféle erőforrást kínál:

* A tárfiók
* Tároló a tárfiókban
* Blob a tárolóban

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Blob-tárolási architektúra diagramja](./media/storage-blobs-introduction/blob1.png)

A következő Python-osztályok segítségével kezelheti ezeket az erőforrásokat:

* [BlobServiceClient:](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) `BlobServiceClient` Az osztály lehetővé teszi az Azure Storage-erőforrások és blobtárolók kezelésére.
* [ContainerClient:](/python/api/azure-storage-blob/azure.storage.blob.containerclient) `ContainerClient` Az osztály lehetővé teszi az Azure Storage-tárolók és azok blobjainak kezelésére.
* [BlobClient:](/python/api/azure-storage-blob/azure.storage.blob.blobclient) `BlobClient` Az osztály lehetővé teszi az Azure Storage-blobok kezelésére.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek bemutatják, hogyan hajthatja végre a következőket az Azure Blob storage-ügyfélrendszertár a Pythonhoz:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [A tárolóban lévő blobok listázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód lekéri a tárfiók kapcsolati karakterláncát a [tárolási kapcsolat karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból.

Adja hozzá ezt `try` a kódot a blokkon belül:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód hozzáfűz egy UUID értéket a tároló nevéhez annak érdekében, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Hozzon létre egy példányt a [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) osztály hívja meg a [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) metódust. Ezután hívja meg a [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) metódust a tároló tényleges létrehozásához a tárfiókban.

Adja hozzá ezt a `try` kódot a blokk végéhez:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

A következő kódrészlet:

1. Szövegfájlt hoz létre a helyi könyvtárban.
1. Egy [BlobClient-objektumra](/python/api/azure-storage-blob/azure.storage.blob.blobclient) mutató hivatkozást kér le a [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) metódus hívásával a [BlobServiceClient-en](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) a [Tároló létrehozása](#create-a-container) szakaszból.
1. Feltölti a helyi szöveges fájlt a blobba a [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) metódus hívásával.

Adja hozzá ezt a `try` kódot a blokk végéhez:

```python
# Create a file in local data directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Sorolja fel a blobok a tárolóban hívja meg a [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) metódust. Ebben az esetben csak egy blob lett hozzáadva a tárolóhoz, így a listaelem-művelet csak azt az egy blobot adja vissza.

Adja hozzá ezt a `try` kódot a blokk végéhez:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) metódus hívásával. A példakód hozzáadja a "DOWNLOAD" utótagot a fájlnévhez, így mindkét fájlt láthatja a helyi fájlrendszerben.

Adja hozzá ezt a `try` kódot a blokk végéhez:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Tároló törlése

A következő kód törli az erőforrásokat az alkalmazás által létrehozott eltávolításával a teljes tároló a [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) metódussal. A helyi fájlokat is törölheti, ha szeretné.

Az alkalmazás szünetela felhasználói `input()` bevitel a hívás, mielőtt törli a blob, tároló és a helyi fájlokat. Ez jó alkalom annak ellenőrzésére, hogy az erőforrások megfelelően jöttek-e létre, mielőtt törölné őket.

Adja hozzá ezt a `try` kódot a blokk végéhez:

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy tesztfájlt a helyi mappában, és feltölti azt a Blob storage-ba. A példa ezután felsorolja a blobok a tárolóban, és letölti a fájlt egy új nevet, így össze lehet hasonlítani a régi és az új fájlokat.

Nyissa meg a *blob-quickstart-v12.py* fájlt tartalmazó könyvtárat, majd hajtsa végre a következő `python` parancsot az alkalmazás futtatásához.

```console
python blob-quickstart-v12.py
```

Az alkalmazás kimenete hasonló a következő példához:

```output
Azure Blob storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

A tisztítási folyamat megkezdése előtt ellenőrizze, hogy az *adatmappában* van-e a két fájl. Ha megnyitja őket, láthatja, hogy megegyeznek.

Miután ellenőrizte a fájlokat, nyomja le az **Enter** billentyűt a tesztfájlok törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, tölthet le és listázhatja a blobokat a Python használatával.

A Blob storage mintaalkalmazásának megtekintéséhez folytassa a következőket:

> [!div class="nextstepaction"]
> [Azure Blob storage SDK v12 Python-minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* További információ: Az [Azure SDK pythonhoz.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md)
* Oktatóanyagok, minták, rövid útmutatók és egyéb dokumentáció, keresse fel [az Azure python fejlesztőknek.](/azure/python/)
