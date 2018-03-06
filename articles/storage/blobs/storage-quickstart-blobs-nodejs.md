---
title: "Azure rövid útmutató – Blobok feltöltése, letöltése és listázása az Azure Storage-ban a Node.js használatával | Microsoft Docs"
description: "Ebben a rövid útmutatóban egy tárfiókot és egy tárolót hoz létre. Majd a Storage Node.js-hez készült ügyféloldali kódtára segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat."
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: cshoe
ms.openlocfilehash: ad0d4a2242aef99e0307f732175e0c50010580ce
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Rövid útmutató: blobok feltöltése, letöltése és listázása a Node.js használatával

A rövid útmutató azt ismerteti, hogyan használható a Node.js blokkblobok feltöltésére, letöltésére és listázására egy, az Azure Blob Storage-ben található tárolóban.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* [Node.js](https://nodejs.org/en/) telepítése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A gyors útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) egy egyszerű konzolalkalmazás. 

A [git](https://git-scm.com/) használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Ez a parancs a helyi git mappába klónozza az adattárat. Az alkalmazás megnyitásához keresse meg a storage-blobs-node-quickstart mappát, és kattintson duplán az index.js fájlra.

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati karakterlánc konfigurálása

Az alkalmazásban meg kell adnia a tárfiókjához tartozó kapcsolati karakterláncot. Nyissa meg az `index.js` fájlt és keresse meg a `connectionString` változót. Cserélje le a kapcsolati karakterlánc teljes értékét arra, amelyet az Azure Portalról elmentett. A tárfiók kapcsolati karakterláncának a következőképpen kell kinéznie:

```javascript
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>Szükséges csomagok telepítése

Az alkalmazás könyvtárában futtassa az `npm install` parancsot a `package.json` fájlban felsorolt szükséges fájlok telepítéséhez.

```javascript
npm install
```

## <a name="run-the-sample"></a>Minta futtatása

Ez a minta létrehoz egy tesztfájlt a Dokumentumok mappában, feltölti a Blob-tárolóba, listázza a tárolóban található blobokat, majd letölti a fájlt új néven, hogy össze lehessen hasonlítani a régebbi fájllal.

Futtassa a mintát a következő szöveg beírásával: `node index.js`. A következő kimenet Windows rendszerből származik.  Linux használata esetén hasonló kimenet várható a megfelelő fájlelérési utakkal.

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

A folytatás előtt ellenőrizze a két fájlt a Dokumentumok mappában. Ha megnyitja őket, láthatja, hogy megegyeznek.

Az [Azure Storage Explorert](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy egy ahhoz hasonló eszközt is használhat, ha szeretné a fájlt megtekinteni a blobtárolóban. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat.

A fájlok ellenőrzése után nyomja le bármelyik billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez. Most, hogy tudja, hogy működik a minta, nyissa meg az index.js fájlt, és tekintse meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz

Az első lépésben létre kell hozni a referenciát a **BlobService** objektumhoz, amelyet a blobtároló eléréséhez és kezeléséhez használ. Ezek az objektumok egymásra épülnek – mindegyiket a listában utánuk következő használja.

* Hozza létre a [BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor) objektum példányát, amely a tárfiókja Blob szolgáltatására mutat.

* Hozzon létre egy új tárolót, majd állítsa be annak engedélyeit úgy, hogy a blobok nyilvánosak és mindössze egy URL-címmel elérhetőek legyenek. A tároló neve a **quickstartcontainer-** szöveggel kezdődik.

Ez a példa a [createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) parancsot használja, mert a minta futtatásakor mindig új tárolót szeretnénk létrehozni. Éles környezetben, ahol ugyanazt a tárolót használja egy alkalmazásban, jobb eljárás a CreateIfNotExists egyszeri hívása. Azt is megteheti, hogy előre létrehozza a tárolót, hogy ne a kódban kelljen létrehoznia.

```javascript
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

### <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A leggyakrabban használt elemek a blokkblobok. Ezek ideálisak a szövegek és a bináris adatok tárolására, és ezért használja őket ez a gyors útmutató is.

Fájl egy blobba való feltöltéséhez használja a [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) metódust. Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen.

A mintakód létrehoz egy, a fel- és letöltéshez használatos helyi fájlt, és úgy tárolja el, hogy az a **localPath** néven és a blob nevével elnevezve legyen feltöltve a **localFileToUpload** helyre. A következő példa feltölti a fájlt az Ön tárolójára, amelynek neve a **quickstartcontainer-** szöveggel kezdődik.

```javascript
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

A Blob-tárolóval többféle feltöltési módszer használható. Ha például memóriastreamje van, használhatja a [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream) metódust a [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) helyett.

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Ezután az alkalmazás lekéri a tároló fájljainak listáját a [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented) utasítással. A következő kód lekéri a blobok listáját, majd végighalad rajtuk, és megjeleníti a talált blobok URI-ját. Az URI-t kimásolhatja a parancsablakból, és a fájl megtekintéséhez beillesztheti egy böngészőbe.

Ha 5000 vagy kevesebb blob található a tárolóban, az összes blobnevet lekérheti a [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented) egyetlen hívásával. Ha több mint 5000 blob található a tárolóban, a szolgáltatás 5000-es csomagokban kéri le a blobnevek listáját, amíg a végükre nem ér. Ennek megfelelően az API az első hívásakor az első 5000 blobnevet és egy folytatási kódot ad vissza. A második alkalommal meg kell adnia ezt a kódot, és a szolgáltatás lekéri a blobnevek következő készletét, és így tovább, amíg a folytatási kód értéke nulla nem lesz – ez jelzi, hogy a rendszer az összes blobnevet lekérte.

```javascript
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

### <a name="download-blobs"></a>Blobok letöltése

Blobokat a helyi lemezre a [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile) utasítással tölthet le.

A következő kód letölti az útmutató korábbi fejezetében feltöltött blobot, és hozzáadja a „_DOWNLOADED” (Letöltve) utótagot a nevéhez, így mindkét fájlt láthatja majd a helyi lemezen. 

```javascript
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben a rövid útmutatóban feltöltött blobokra, a teljes tárolót törölheti a [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) és a [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists) paranccsal. A létrehozott fájlokat is törölje, ha már nincs szüksége rájuk. Ezt az alkalmazás elvégzi, amikor az Enter billentyűvel kilép belőle.

```javascript
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a Node.js használatával. Ha bővebb információra van szüksége a Blob Storage használatával kapcsolatban, lépjen tovább a Blob Storage használati útmutatójára.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](storage-nodejs-how-to-use-blob-storage.md)

Az Azure Storage Node.js-referenciájáért lásd: [azure-storage csomag](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest).