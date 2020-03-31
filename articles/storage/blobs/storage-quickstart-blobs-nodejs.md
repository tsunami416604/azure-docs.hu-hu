---
title: 'Rövid útmutató: Azure Blob storage-tár v12 – JavaScript'
description: Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Azure Blob storage-beli ügyfélkód12-es JavaScript-verzióját egy tároló és egy blob blob (objektum) tárolóban. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: e68e91d90846ab77b994b53be7a84a9dd8bc5a25
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241037"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Gyorsútmutató: Blobok kezelése JavaScript v12 SDK-val a Node.js-ben

Ebben a rövid útmutatóban megtudhatja, hogy a blobok segítségével Node.js használatával. A blobok olyan objektumok, amelyek nagy mennyiségű szöveges vagy bináris adatot képesek tárolni, beleértve a képeket, dokumentumokat, adatfolyam-továbbítási és archiválási adatokat. Blobokat fog feltölteni, letölteni és listázni, és tárolókat hozhat létre és törölhet.

[API-referenciadokumentáció–](/javascript/api/@azure/storage-blob) | [forráskód](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [csomag (node package manager)](https://www.npmjs.com/package/@azure/storage-blob) | [minták](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Storage-fiók. [Hozzon létre egy tárfiókot.](../common/storage-account-create.md)
- [Node.js](https://nodejs.org/en/download/).

> [!NOTE]
> Az előző SDK-verzió ismerkedéséhez olvassa el a [Gyorsútmutató: Blobok kezelése JavaScript v10 SDK-val a Node.js-ben című témakört.](storage-quickstart-blobs-nodejs-legacy.md)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti a projekt előkészítése az Azure Blob storage-ügyfélkódtár v12 JavaScript.

### <a name="create-the-project"></a>A projekt létrehozása

*Blob-quickstart-v12*nevű JavaScript-alkalmazás létrehozása.

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Váltson át az újonnan létrehozott *blob-quickstart-v12* könyvtárra.

    ```console
    cd blob-quickstart-v12
    ```

1. Hozzon létre egy *package.json*nevű új szövegfájlt. Ez a fájl határozza meg a Node.js projektet. Mentse ezt a fájlt a *blob-quickstart-v12* könyvtárba. Itt van a fájl tartalma:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    Ha szeretné, beírhatja `author` a saját nevét a mezőbe.
   
### <a name="install-the-package"></a>A csomag telepítése

Miközben továbbra is a *blob-quickstart-v12* könyvtárban, telepítse az Azure Blob `npm install` storage ügyfélrendszertár JavaScript-csomag a parancs használatával. Ez a parancs beolvassa a *package.json* fájlt, és telepíti az Azure Blob storage ügyfélkódtár v12 JavaScript-csomag és az összes olyan kódtár, amelytől függ.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Az alkalmazáskeretrendszer beállítása

A projekt könyvtárából:

1. Másik új szövegfájl megnyitása a kódszerkesztőben
1. Az `require` Azure és a Node.js modulok betöltéséhez kezdeményezett hívások hozzáadása
1. A program struktúrájának létrehozása, beleértve az alapvető kivételkezelést

    Itt a kód:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Mentse az új fájlt *blob-quickstart-v12.js* néven a *blob-quickstart-v12* könyvtárba.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Blob storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy definíciót, például szöveges vagy bináris adatok. A Blob storage háromféle erőforrást kínál:

* A tárfiók
* Tároló a tárfiókban
* Blob a tárolóban

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Blob-tárolási architektúra diagramja](./media/storage-blobs-introduction/blob1.png)

Az alábbi JavaScript-osztályok segítségével kezelheti ezeket az erőforrásokat:

* [BlobServiceClient:](/javascript/api/@azure/storage-blob/blobserviceclient) `BlobServiceClient` Az osztály lehetővé teszi az Azure Storage-erőforrások és blobtárolók kezelésére.
* [ContainerClient:](/javascript/api/@azure/storage-blob/containerclient) `ContainerClient` Az osztály lehetővé teszi az Azure Storage-tárolók és azok blobjainak kezelésére.
* [BlobClient:](/javascript/api/@azure/storage-blob/blobclient) `BlobClient` Az osztály lehetővé teszi az Azure Storage-blobok kezelésére.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek bemutatják, hogyan hajthatja végre a következőket az Azure Blob storage-ügyfélkódtár javascripthez:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [A tárolóban lévő blobok listázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód lekéri a tárfiók kapcsolati karakterláncát a [tárolási kapcsolat karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból.

Adja hozzá ezt `main` a kódot a függvényhez:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód hozzáfűz egy UUID értéket a tároló nevéhez annak érdekében, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Hozzon létre egy példányt a [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) osztály hívásával a [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) metódust. Ezután hívja meg a [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) metódust egy tárolóra való hivatkozás lekérni. Végül hívja meg a [create-t](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) a tároló tényleges létrehozásához a tárfiókban.

Adja hozzá ezt a `main` kódot a függvény végéhez:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

A következő kódrészlet:

1. Létrehoz egy blobba feltöltendő szöveges karakterláncot.
1. Egy [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) objektumra mutató hivatkozást kér le a [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) metódus nak a [ContainerClient metóduson](/javascript/api/@azure/storage-blob/containerclient) történő [meghívásával](#create-a-container) a Tároló létrehozása szakaszból.
1. Feltölti a szöveges karakterlánc adatait a blobba a [feltöltési](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) módszer hívásával.

Adja hozzá ezt a `main` kódot a függvény végéhez:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Sorolja fel a blobok a tárolóban hívja meg a [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) metódus. Ebben az esetben csak egy blob lett hozzáadva a tárolóhoz, így a listaelem-művelet csak azt az egy blobot adja vissza.

Adja hozzá ezt a `main` kódot a függvény végéhez:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [letöltési](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) módszer hívásával. A példakód tartalmaz egy `streamToString`segítő függvényt, amely a Node.js olvasható adatfolyamot egy karakterláncba olvassa.

Adja hozzá ezt a `main` kódot a függvény végéhez:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Adja hozzá ezt a `main` segítő funkciót a függvény *után:*

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Tároló törlése

A következő kód törli az erőforrásokat az alkalmazás által létrehozott eltávolításával a teljes tároló [ti a törlési](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) módszerrel. A helyi fájlokat is törölheti, ha szeretné.

Adja hozzá ezt a `main` kódot a függvény végéhez:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy szöveges karakterláncot, és feltölti azt a Blob storage.This app creates a text string and uploads it to Blob storage. A példa ezután felsorolja a blob(ok) a tárolóban, letölti a blobot, és megjeleníti a letöltött adatokat.

A konzolparancsból keresse meg a *blob-quickstart-v12.py* fájlt tartalmazó könyvtárat, majd hajtsa végre a következő `node` parancsot az alkalmazás futtatásához.

```console
node blob-quickstart-v12.js
```

Az alkalmazás kimenete hasonló a következő példához:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Lépjen végig a hibakeresőben lévő kódon, és ellenőrizze az [Azure Portalt](https://portal.azure.com) a folyamat során. Ellenőrizze, hogy a tároló létrehozása folyamatban van-e. Megnyithatja a blobot a tárolóban, és megtekintheti a tartalmát.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, tölthet le és listázhatja a blobokat JavaScript használatával.

Oktatóanyagok, minták, rövid útmutatók és egyéb dokumentációk:

> [!div class="nextstepaction"]
> [Az Azure for JavaScript dokumentációja](/azure/javascript/)

* További információ: [Az Azure Blob storage ügyfélkódtár javascripthez.](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob)
* A Blob storage mintaalkalmazásai megtekintéséhez folytassa az [Azure Blob storage-ügyféltár v12-es JavaScript-mintáival.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)
