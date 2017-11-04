---
title: "Azure gyors üzembe helyezés – az Azure Blob storage Node.js| használatának átviteli objektumok Microsoft Docs"
description: "Gyorsan elsajátíthatják átvitele az objektumok Azure Blob storage használata Node.js és a"
services: storage
documentationcenter: storage
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/30/2017
ms.author: gwallace
ms.openlocfilehash: 4c3c4ec341a0e5f4f0e7415128479f6448f7db6b
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-nodejs"></a>Az Azure Blob storage használata Node.js átviteli objektumok

A gyors üzembe helyezés elsajátíthatja Node.js segítségével töltse fel, töltse le, és az Azure Blob storage tárolója blokk blobok listázása.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* [Node.js](https://nodejs.org/en/) telepítése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) alapvető Konzolalkalmazás a gyors üzembe helyezés használatban van. 

Használjon [git](https://git-scm.com/) letölteni az alkalmazást a fejlesztési környezet egy példányát.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Ez a parancs klónokat a tárházat a helyi git-mappába. Nyissa meg az alkalmazás keresse meg a storage-BLOB-csomópont – gyors üzembe helyezés mappa, nyissa meg azt, és kattintson duplán arra a index.js.

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati karakterlánc konfigurálása

Az alkalmazás a kapcsolati karakterláncot kell megadnia a tárfiók. Nyissa meg a `index.js` fájlt, keresse meg a `connectionString` változó. Cserélje le az értékét a teljes Azure-portálról mentett azt a kapcsolati karakterlánc értékét. A tárolási kapcsolati karakterlánc a következő hasonlóan kell kinéznie:

```node
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>Telepíti a szükséges csomagokat

Az alkalmazás könyvtárában, futtassa `npm install` telepítése szükséges a fájlban felsorolt csomagok a `package.json` fájlt.

```node
npm install
```

## <a name="run-the-sample"></a>A minta futtatásához

Ez a minta létrehoz egy tesztelési a dokumentumok, feltölti a Blob Storage, a tárolóban lévő blobok sorolja fel, majd letölti a fájlt új néven, a régi és új fájlok összehasonlítás.

A minta futtatásához írja be a `node index.js`. A következő kimenet egy Windows rendszer származik.  A megfelelő elérési utat egy hasonló kimenetet használata Linux várható.

```
Azure Storage Node.js Client Library Blobs Quick Start

1. Creating a container with public access: quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d

2. Creating a file in ~/Documents folder to test the upload and download

   Local File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

3. Uploading BlockBlob: quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

   Uploaded Blob URL: https://mystorageaccount.blob.core.windows.net/quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d/quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

4. Listing blobs in container

   - quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt (type: BlockBlob)


5. Downloading blob

   Downloaded File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d_DOWNLOADED.txt

Sample finished running. When you hit <ENTER> key, the temporary files will be deleted and the sample application will exit.
```

A folytatás előtt ellenőrizze a két fájl MyDocuments. Nyissa meg őket, és tekintse meg a megegyezik azzal.

Például egy eszköz is használhatja a [Azure Tártallózó](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) megtekinthetik a fájlokat a Blob Storage tárolóban. Azure Tártallózó egy ingyenes platformfüggetlen eszköz, amely lehetővé teszi a tárfiók adatait.

Miután ellenőrizte, hogy a fájlokat, kattintson a bemutató befejeződését, és törölje a tesztfájlok bármelyik billentyűt. Most, hogy ismeri a minta funkciója, nyissa meg a index.js fájlt nézze meg a kódot. 

## <a name="get-references-to-the-storage-objects"></a>A tárolási objektum mutató hivatkozások beolvasása

Az első lépés az mutató hivatkozás létrehozása a `BlobService` férhessen hozzá és felügyelhesse a Blob storage használatával. Ezek az objektumok egymástól összeállítása – egyes használja a következő egy, a listában.

* Hozzon létre egy példányát a  **[BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor)**  objektum, amely a tárfiókban lévő a Blob szolgáltatás mutat.

* Hozzon létre egy új tárolót, és ezután az engedélyekkel a tárolóra, a blobok nyilvánosak, és csak egy URL-cím elérhető. A tároló kezdődik-e **quickstartcontainer -**.

Ez a példa [createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) mert szeretnénk hozzon létre egy új tároló minden alkalommal, amikor a minta futtatása. Éles környezetben, ahol az alkalmazás teljes tárolóhoz használ hogy a rendszer jobb eljárás az, hogy csak egyszer hívható meg CreateIfNotExists. Azt is megteheti a tároló időben hozhat létre, így nem kell a kódban létrehozásához.

```node
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

## <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Blokkblobok a leggyakrabban használt. Ideálisak, amelynek az oka alkalmával használták őket a gyors üzembe helyezés szöveg és a bináris adatok tárolására.

Fájl feltöltése a blob, használhatja a [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) metódust. Ez a művelet a blob hoz létre, ha már nem létezik, vagy felülírja, ha már létezik.

A mintakód létrehoz egy helyi fájlt a feltöltés és letöltés, mint a feltölteni kívánt fájl tárolására használt **localPath** és a blob neve **localFileToUpload**. Az alábbi példa feltölti a tárolóhoz kezdődő **quickstartcontainer -**.

```node
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

Többféleképpen feltöltés, a Blob storage használata. Például ha egy memóriafolyam, használhatja a [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream) metódus helyett [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile).

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Ezt követően az alkalmazás a tárolót használja a fájlok listájának lekérése [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). A következő kódot a bináris objektumok listájának beolvasása, majd végighalad őket, az URI-azonosítók található blobok megjelenítő. Másolja az URI a parancsablakot, és illessze be a böngészőt, hogy a fájl.

5000 vagy kevesebb BLOB a tárolóban van, ha az összes a blob nevének egy hívás a beolvasott [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Ha több mint 5000 BLOB a tárolóban, a szolgáltatás lekérdezi az 5000-készletek listájában, amíg a blob nevének mindegyikét lettek beolvasva. Ezért először az API neve, az első 5000 blob nevének, valamint a folytatási kód adja vissza. A második alkalommal megadta a jogkivonatot, és a szolgáltatás lekérdezi a blob nevének a következő készletét, és stb a folytatási kód csak NULL értékű, ami azt jelzi, hogy a blob nevének mindegyikét lettek beolvasva.

```node
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

## <a name="download-blobs"></a>Blobok letöltése

Blobok letöltése a helyi lemezek használata [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile).

A következő kódot a blob feltöltése egy korábbi szakaszában "_DOWNLOADED" utótag hozzáadása a blob nevének, így mindkét fájlt a helyi lemezen tölti le. 

```node
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a blobok a gyors üzembe helyezés feltöltött, törölje a teljes tárolóhoz történő [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) és [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists). Ha már nincs szükség a fájlt is törli. Ez az végrehajtott megvagyunk az alkalmazásban megnyomásakor kilép az alkalmazásból adja meg.

```node
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés megtanulta, hogyan viheti át a fájlokat a helyi lemezek és az Azure Blob storage használata Node.js között. További információt a Blob storage használata, továbbra is a Blob-tároló útmutató.

> [!div class="nextstepaction"]
> [A BLOB Storage műveletek útmutató](storage-nodejs-how-to-use-blob-storage.md)

A Tártallózó alkalmazással és a Blobok kapcsolatos további információkért lásd: [kezelése Azure Blob storage-erőforrások a Tártallózó alkalmazással](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
