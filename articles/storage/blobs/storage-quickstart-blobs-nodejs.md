---
title: 'Gyors útmutató: Azure Blob Storage Library V12 – JavaScript'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure Blob Storage ügyféloldali kódtár 12-es verzióját a JavaScripthez a tárolók és Blobok blob (Object) tárolóban történő létrehozásához. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 90ef1be4366205b1f655a0b93619a897d6d1f25d
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929497"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-javascript"></a>Gyors útmutató: Azure Blob Storage ügyféloldali kódtára a JavaScripthez

Ismerkedjen meg az Azure Blob Storage-beli, a JavaScripthez készült ügyféloldali kódtár szolgáltatással. Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. Kövesse a csomag telepítésének lépéseit, és próbálja ki például az alapszintű feladatokhoz tartozó kódot. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

> [!NOTE]
> A korábbi SDK-verzió használatának megkezdéséhez lásd [: gyors útmutató: Azure Blob Storage ügyféloldali kódtár a javascripthez](storage-quickstart-blobs-nodejs-v10.md).

A JavaScripthez készült Azure Blob Storage ügyféloldali kódtára a következőre használható:

* Tároló létrehozása
* BLOB feltöltése az Azure Storage-ba
* Egy tároló összes blobjának listázása
* A blob letöltése a helyi számítógépre
* Tároló törlése

[API-referenciák dokumentációja](/javascript/api/@azure/storage-blob) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [csomag (node Package Manager)](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | [minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure Storage-fiók – [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Az operációs rendszer aktuális [Node. js](https://nodejs.org/en/download/) -fájlja.

## <a name="setting-up"></a>Beállítás

Ebből a szakaszból megtudhatja, hogyan készít elő egy projektet a JavaScripthez készült Azure Blob Storage ügyféloldali kódtáraval való együttműködésre.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *blob-Gyorsindítás-V12*nevű JavaScript-alkalmazást.

1. A konzol ablakban (például cmd, PowerShell vagy bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Váltson az újonnan létrehozott *blob-Gyorsindítás-V12* könyvtárra.

    ```console
    cd blob-quickstart-v12
    ```

1. Hozzon létre egy *Package. JSON*nevű új szövegfájlt. Ez a fájl határozza meg a Node. js-projektet. Mentse ezt a fájlt a *blob-Gyorsindítás-V12* könyvtárba. A fájl tartalma:

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
    
    Ha szeretné, a `author` mezőben is elhelyezheti a saját nevét.
   
### <a name="install-the-package"></a>A csomag telepítése

Miközben továbbra is a *blob-Gyorsindítás-V12* könyvtárban található, telepítse az Azure Blob Storage ügyféloldali kódtárat a JavaScript-csomaghoz a `npm install` parancs használatával. Ez a parancs beolvassa a *Package. JSON* fájlt, és telepíti az Azure Blob Storage ügyféloldali kódtárat a JavaScript-csomaghoz, és az összes olyan könyvtárat, amelytől függ.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Nyisson meg egy másik új szövegfájlt a Kódszerkesztőben
1. Azure-és Node. js-modulok betöltéséhez `require` hívások hozzáadása
1. A program struktúrájának létrehozása, beleértve a nagyon egyszerű kivételek kezelését

    A kód a következő:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - Javascript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Mentse az új fájlt *blob-Quickstart-v12. js* néven a *blob-Gyorsindítás-V12* könyvtárba.

### <a name="copy-your-credentials-from-the-azure-portal"></a>A hitelesítési adatok másolása az Azure Portalról

Ha a minta alkalmazás az Azure Storage-ba irányuló kérést tesz elérhetővé, akkor azt engedélyezni kell. A kérések engedélyezéséhez adja hozzá a Storage-fiók hitelesítő adatait az alkalmazáshoz kapcsolódási karakterláncként. A tárfiók hitelesítő adatainak megtekintéséhez kövesse az alábbi lépéseket:

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
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

Használja az alábbi JavaScript-osztályokat a következő erőforrásokkal való interakcióhoz:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): a `BlobServiceClient` osztály lehetővé teszi az Azure Storage-erőforrások és a blob-tárolók kezelését.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): a `ContainerClient` osztály lehetővé teszi az Azure Storage-tárolók és a Blobok kezelését.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): a `BlobClient` osztály lehetővé teszi az Azure Storage-Blobok kezelését.

## <a name="code-examples"></a>Példák a kódokra

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következő műveleteket az Azure Blob Storage ügyféloldali kódtára használatával a JavaScripthez:

* [A kapcsolatok karakterláncának beolvasása](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [Tárolóban lévő Blobok listázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód a Storage- [kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból kéri le a Storage-fiókhoz tartozó kapcsolatok karakterláncát.

Adja hozzá ezt a kódot a `main` függvényhez:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód egy UUID értéket fűz hozzá a tároló nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Hozza létre a [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) osztály egy példányát a [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--newpipelineoptions-) metódus meghívásával. Ezután hívja meg a [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) metódust egy tárolóra mutató hivatkozás beszerzéséhez. Végül hívja a [create (létrehozás](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) ) lehetőséget, hogy ténylegesen létrehozza a tárolót a Storage-fiókban.

Adja hozzá ezt a kódot a `main` függvény végéhez:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await new BlobServiceClient.fromConnectionString(CONNECT_STR);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
await containerClient.create();
```

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

A következő kódrészlet:

1. Egy blobba feltöltendő szöveges karakterláncot hoz létre.
1. Beolvas egy [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) objektumra mutató hivatkozást úgy, hogy meghívja a [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) metódust a [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) a [tároló létrehozása](#create-a-container) szakaszban.
1. Feltölti a szöveges karakterláncot a blobba a [feltöltési](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) metódus meghívásával.

Adja hozzá ezt a kódot a `main` függvény végéhez:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure Storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
await blockBlobClient.upload(data, data.length);
```

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A tárolóban lévő Blobok listázása a [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) metódus meghívásával. Ebben az esetben a tárolóhoz csak egy blob lett hozzáadva, így a listázási művelet csak ezt az egy blobot adja vissza.

Adja hozzá ezt a kódot a `main` függvény végéhez:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [letöltési](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) módszer meghívásával. A példában szereplő kód egy `streamToString` nevű segítő függvényt tartalmaz, amely egy Node. js olvasható adatfolyam karakterláncba való olvasására szolgál.

Adja hozzá ezt a kódot a `main` függvény végéhez:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Adja hozzá ezt a segítő funkciót a `main` függvény *után* :

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

A következő kód törli az alkalmazás által létrehozott erőforrásokat, ha eltávolítja a teljes tárolót a [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) metódus használatával. Ha szeretné, törölheti a helyi fájlokat is.

Adja hozzá ezt a kódot a `main` függvény végéhez:

```javascript
console.log('\nDeleting container...');

// Delete container
await containerClient.delete();
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy szöveges karakterláncot, és feltölti a blob Storage-ba. A példa ezután felsorolja a tárolóban lévő blobokat, letölti a blobot, és megjeleníti a letöltött adatfájlokat.

A konzol parancssorában navigáljon a *blob-Quickstart-v12.py* fájlt tartalmazó könyvtárra, majd hajtsa végre a következő `node` parancsot az alkalmazás futtatásához.

```console
node blob-quickstart-v12.js
```

Az alkalmazás kimenete az alábbi példához hasonló:

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

Lépjen be a hibakeresőbe, és vizsgálja meg a Azure Portal a folyamat során. Ellenőrizze, hogy a tároló létrehozása folyamatban van-e. A blobot megnyithatja a tárolón belül, és megtekintheti a tartalmat.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, tölthet le és listázhat blobokat a JavaScript használatával.

A blob Storage-beli minták alkalmazásainak megtekintéséhez folytassa a következőket:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 JavaScript-minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

* További információt a [javascripthez készült Azure SDK](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md)-ban talál.
* Az oktatóanyagok, minták, gyors indítás és egyéb dokumentációk az [Azure SDK for JavaScript dokumentációjában találhatók](/azure/javascript/)meg.
