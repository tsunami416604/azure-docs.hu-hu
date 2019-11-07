---
title: 'Gyors útmutató: Azure Blob Storage Library V12 – Python'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure Blob Storage ügyféloldali kódtárat a Pythonhoz a tárolók és Blobok blob (Object) tárolóban történő létrehozásához. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 0851ee9061fcb01a5e007f0cc4989d6a3febc665
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605158"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-python"></a>Gyors útmutató: Azure Blob Storage ügyféloldali kódtára a Pythonhoz

Ismerkedés a Pythonhoz készült Azure Blob Storage ügyféloldali kódtáraval. Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. Kövesse a csomag telepítésének lépéseit, és próbálja ki például az alapszintű feladatokhoz tartozó kódot. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

> [!NOTE]
> A korábbi SDK-verzió használatának megkezdéséhez tekintse meg a gyors útmutató [: Azure Blob Storage ügyféloldali kódtára a Pythonhoz](storage-quickstart-blobs-python-legacy.md)című témakört.

Használja az Azure Blob Storage ügyféloldali kódtárat a következőre:

* Tároló létrehozása
* BLOB feltöltése az Azure Storage-ba
* Egy tároló összes blobjának listázása
* A blob letöltése a helyi számítógépre
* Tároló törlése

[API-referenciák dokumentációja](/python/api/azure-storage-blob) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [csomag (Python-csomag indexe)](https://pypi.org/project/azure-storage-blob/) | [minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure Storage-fiók – [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) az operációs rendszerhez – 2,7, 3,5 vagy újabb

## <a name="setting-up"></a>Beállítás

Ez a szakasz végigvezeti a projekt előkészítésének folyamatán a Pythonhoz készült Azure Blob Storage ügyféloldali kódtáraval való együttműködéshez.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *blob-Gyorsindítás-V12*nevű Python-alkalmazást.

1. A konzol ablakban (például cmd, PowerShell vagy bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Váltson az újonnan létrehozott *blob-Gyorsindítás-V12* könyvtárra.

    ```console
    cd blob-quickstart-v12
    ```

1. A *blob-Gyorsindítás-V12* könyvtárban hozzon létre egy másik könyvtárat, amely az *adat*nevű. A blob-adatfájlokat a rendszer létrehozza és tárolja.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>A csomag telepítése

Miközben még mindig az alkalmazás könyvtárában van, telepítse az Azure Blob Storage ügyféloldali kódtárat a Python-csomaghoz a `pip install` parancs használatával.

```console
pip install azure-storage-blob
```

Ez a parancs telepíti az Azure Blob Storage ügyféloldali kódtárat a Python-csomaghoz, és az összes olyan kódtárat, amelytől függ. Ebben az esetben ez csak a Pythonhoz készült Azure Core Library.

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Új szövegfájl megnyitása a kódszerkesztő programban
1. `import` utasítások hozzáadása
1. A program struktúrájának létrehozása, beleértve a nagyon egyszerű kivételek kezelését

    A kód a következő:

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

1. Mentse az új fájlt *blob-Quickstart-v12.py* néven a *blob-Gyorsindítás-V12* könyvtárba.

### <a name="copy-your-credentials-from-the-azure-portal"></a>A hitelesítési adatok másolása az Azure Portalról

Ha a minta alkalmazás az Azure Storage-ba irányuló kérést tesz elérhetővé, akkor azt engedélyezni kell. A kérések engedélyezéséhez adja hozzá a Storage-fiók hitelesítő adatait az alkalmazáshoz kapcsolódási karakterláncként. A tárfiók hitelesítő adatainak megtekintéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Itt megtekintheti a fiókhoz tartozó hozzáférési kulcsokat, valamint az egyes kulcsokhoz tartozó teljes kapcsolati sztringeket.
4. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához. A kapcsolati sztring értékét hozzáadja egy környezeti változóhoz a következő lépés során.

    ![A kapcsolati sztring az Azure Portalról történő másolását bemutató képernyőkép](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

A kapcsolati sztring másolása után írja azt egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje le a `<yourconnectionstring>`t a tényleges kapcsolatok karakterláncára.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Miután hozzáadta a környezeti változót a Windows rendszerben, el kell indítania a parancssorablak új példányát.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Programok újraindítása

A környezeti változó hozzáadását követően indítsa újra a futó programokat, amelyeknek olvasnia kell a környezeti változót. A folytatás előtt indítsa el például a fejlesztési környezetet vagy a szerkesztőt.

## <a name="object-model"></a>Objektummodell

Az Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem felelnek meg egy adott adatmodellnek vagy definíciónak, például szöveges vagy bináris adatoknak. A blob Storage háromféle típusú erőforrást kínál:

* A Storage-fiók
* Egy tároló a Storage-fiókban
* A tárolóban lévő blob

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![BLOB Storage-architektúra ábrája](./media/storage-blob-introduction/blob1.png)

A következő Python-osztályok használata az alábbi erőforrásokkal való interakcióhoz:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): a `BlobServiceClient` osztály lehetővé teszi az Azure Storage-erőforrások és a blob-tárolók kezelését.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): a `ContainerClient` osztály lehetővé teszi az Azure Storage-tárolók és a Blobok kezelését.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): a `BlobClient` osztály lehetővé teszi az Azure Storage-Blobok kezelését.

## <a name="code-examples"></a>Példák a kódokra

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült Azure Blob Storage ügyféloldali kódtára használatával:

* [A kapcsolatok karakterláncának beolvasása](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [Tárolóban lévő Blobok listázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód a Storage- [kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból kéri le a Storage-fiókhoz tartozó kapcsolatok karakterláncát.

Adja hozzá ezt a kódot a `try` blokkon belül:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called CONNECT_STR. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('CONNECT_STR')
```

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód egy UUID értéket fűz hozzá a tároló nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Hozza létre a [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) osztály egy példányát a [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) metódus meghívásával. Ezután hívja meg a [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) metódust, hogy ténylegesen létrehozza a tárolót a Storage-fiókban.

Adja hozzá ezt a kódot a `try` blokk végéhez:

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

1. Létrehoz egy szövegfájlt a helyi könyvtárban.
1. Beolvas egy [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) objektumra mutató hivatkozást úgy, hogy meghívja a [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) metódust a [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) a [tároló létrehozása](#create-a-container) szakaszban.
1. A [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) metódus meghívásával feltölti a helyi szövegfájlt a blobba.

Adja hozzá ezt a kódot a `try` blokk végéhez:

```python
# Create a file in local Documents directory to upload and download
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

A tárolóban lévő Blobok listázása a [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) metódus meghívásával. Ebben az esetben a tárolóhoz csak egy blob lett hozzáadva, így a listázási művelet csak ezt az egy blobot adja vissza.

Adja hozzá ezt a kódot a `try` blokk végéhez:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) metódus meghívásával. A példában szereplő kód a "Letöltés" utótagot adja hozzá a fájl nevéhez, hogy mindkét fájl látható legyen a helyi fájlrendszerben.

Adja hozzá ezt a kódot a `try` blokk végéhez:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in Documents
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Tároló törlése

A következő kód megtisztítja az alkalmazás által létrehozott erőforrásokat, ha eltávolítja a teljes tárolót a [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) metódussal. Ha szeretné, törölheti a helyi fájlokat is.

Az alkalmazás a `input()` meghívásával szünetelteti a felhasználói bevitelt, mielőtt törli a blobot, a tárolót és a helyi fájlokat. Ez jó eséllyel ellenőrizhető, hogy az erőforrások valóban helyesen lettek-e létrehozva a Törlésük előtt.

Adja hozzá ezt a kódot a `try` blokk végéhez:

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

Ez az alkalmazás létrehoz egy tesztoldalt a helyi mappában, és feltölti azt a blob Storage-ba. A példa ezután felsorolja a tárolóban lévő blobokat, és letölti a fájlt egy új névvel, hogy össze lehessen hasonlítani a régi és az új fájlokat.

Keresse meg a *blob-Quickstart-v12.py* fájlt tartalmazó könyvtárat, majd hajtsa végre a következő `python` parancsot az alkalmazás futtatásához.

```console
python blob-quickstart-v12.py
```

Az alkalmazás kimenete az alábbi példához hasonló:

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

A tisztítási folyamat megkezdése előtt tekintse meg a két fájl *dokumentumok* mappáját. Ha megnyitja őket, láthatja, hogy megegyeznek.

A fájlok ellenőrzése után nyomja le az **ENTER** billentyűt a tesztoldal törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, tölthet le és listázhat blobokat a Python használatával.

A blob Storage-beli minták alkalmazásainak megtekintéséhez folytassa a következőket:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 Python-minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* További információért lásd a [Pythonhoz készült Azure SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md)-t.
* Az oktatóanyagok, a minták, a gyors üzembe helyezés és az egyéb dokumentációk az Azure-ban, [Python-fejlesztőknek](/azure/python/)című oldalon találhatók.
