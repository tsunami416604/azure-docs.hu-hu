---
title: Blobok feltöltése, letöltése, listázása és törlése a JavaScripthez készült Azure Storage SDK 10-es verziójának (előzetes verzió) használatával
description: Blobok és tárolók létrehozása, feltöltése és törlése a Node.js-ben az Azure Storage használatával
services: storage
author: craigshoemaker
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 09/19/2018
ms.author: cshoe
ms.openlocfilehash: a325029ded60a1cd8274743a88f7a4d410466dea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987577"
---
# <a name="quickstart-upload-download-list-and-delete-blobs-using-azure-storage-v10-sdk-for-javascript-preview"></a>Rövid útmutató: Blobok feltöltése, letöltése, listázása és törlése a JavaScripthez készült Azure Storage SDK 10-es verziójának (előzetes verzió) használatával

Ebből a rövid útmutatóból megtudhatja, hogyan lehet a [JavaScripthez készült Azure Storage SDK 10-es verziójának](https://github.com/Azure/azure-storage-js) használatával a Node.js-ben a blobokat fel- és letölteni, listázni és törölni, valamint a tárolókat kezelni.

A rövid útmutató elvégzéséhez szüksége lesz egy [Azure-előfizetésre](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git) egy egyszerű Node.js-konzolalkalmazás. Első lépésként klónozza az adattárat a gépre a következő paranccsal:

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

Ezután módosítsa az alkalmazás mappáit:

```bash
cd azure-storage-js-v10-quickstart
```

Most nyissa meg a mappát kedvenc kódszerkesztő környezetében.

## <a name="configure-your-storage-credentials"></a>A tároló hitelesítő adatainak konfigurálása

Az alkalmazás futtatása előtt meg kell adnia a tárfiókhoz tartozó hitelesítő adatokat. A mintatadattár tartalmaz egy *.env.example* nevű fájlt. Nevezze át a fájlt a *.example* bővítmény eltávolításával, amelynek eredményeként egy *.env* nevű fájl jön létre. A *.env* fájlban adja meg a fióknév és a hozzáférési kulcs értékét az *AZURE_STORAGE_ACCOUNT_NAME* és az *AZURE_STORAGE_ACCOUNT_ACCESS_KEY* kulcs után.

## <a name="install-required-packages"></a>Szükséges csomagok telepítése

Az alkalmazás könyvtárában futtassa az *npm install* parancsot az alkalmazáshoz szükséges csomagok telepítéséhez.

```bash
npm install
```

## <a name="run-the-sample"></a>Minta futtatása
A függőségek telepítését követően a következő paranccsal futtathatja a mintát:

```bash
npm start
```

Az alkalmazás kimenete az alábbi példához hasonló lesz:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```
Ha ehhez a rövid útmutatóhoz egy új tárfiókot használ, előfordulhat, hogy nem látszanak tárolónevek a „*Tárolók*” címke alatt.

## <a name="understanding-the-code"></a>A kód értelmezése
A minta első része osztályokat és függvényeket importál az Azure Blob Storage-névtérből. Az egyes importált elemek a mintában való használatukkor részletesen is be lesznek mutatva.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

A hitelesítő adatokat a rendszer környezeti változókból olvassa be a vonatkozó környezetnek megfelelően.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

A *dotenv* modul tölti be a környezeti változókat, amikor az alkalmazást hibakeresési céllal helyileg futtatja. Az értékek egy *.env* nevű fájlban vannak meghatározva. A modul innen tölti be őket az aktuális végrehajtási környezetbe. Éles környezetben a kiszolgáló konfigurációja biztosítja ezeket az értékeket, ezért ez a kód csak akkor fut, amikor a szkript *nem* „éles” környezetben fut.

A következő néhány blokkot a fájlrendszerhez való jobb illeszkedés érdekében importáljuk.

```javascript
const fs = require('fs');
const path = require('path');
```

A modulok rendeltetése a következő: 

- Az *fs* a Node.js natív modulja a fájlrendszer használatához

- A *path* a fájl abszolút elérési útjának meghatározásához szükséges, amelyet a fájl a Blob Storage-be való feltöltése során használatos

Ezután a környezeti változók értékeinek beolvasása és konstans értékekben való tárolása történik.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
A következő konstansok segítenek feltárni a fájlméretszámítás célját a feltöltési műveletek során.
```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```
Beállítható, hogy az API-kérelmek egy adott időtartam után időtúllépésbe kerüljenek. Az *Aborter* osztály feladata a kérelmek időtúllépésének kezelése, és a mintában az időtúllépések meghatározása a következő állandó alkalmazásával történik.
```javascript
const ONE_MINUTE = 60 * 1000;
```
### <a name="calling-code"></a>Hívó kód

A JavaScript *async/await* szintaxisának támogatása érdekében a teljes hívókód egy *execute* nevű függvénybe van csomagolva. Tehát a rendszer az *execute* függvényt fogja hívni, amelyet egy ígéretként kezel.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
A teljes alábbi kód az execute függvényen belül fut, amelybe a `// commands...` megjegyzés kerül.

Első lépésként a releváns változókat deklaráljuk a nevek és mintatartalmak hozzárendeléséhez, valamint a Blob Storage-be feltölteni kívánt helyi fájl kijelöléséhez.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

A fiók hitelesítő adataival egy folyamatot hozunk létre, amelynek a feladata lesz azt kezelni, hogy a kérések hogyan lesznek a REST API-ra küldve. A folyamatok szálbiztosak, és az újrapróbálkozási szabályzatok, a naplózás, a HTTP-válaszdeszerializálási szabályok és egyebek logikáját határozzák meg.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```
Ebben a kódblokkban a következő osztályokat használjuk:

- A *SharedKeyCredential* osztály a tárfiókok hitelesítő adatainak beburkolását végzi a kérelemfolyamatoknak való átadás érdekében.

- A *StorageURL* osztály az új folyamatok létrehozására szolgál.

- A *ServiceURL* osztály egy, a REST API-ban használt URL modellezését végzi. Az osztály példányaival olyan műveletek hajthatók végre, mint például a tárolók listázása és a környezeti információk megadása a tárolók URL-címeinek létrehozásához.

A *ServiceURL*-példányt a *ContainerURL*- és *BlockBlobURL*-példányokkal együtt használva kezelhetők a tárfiókban lévő tárolók és blobok.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```
A *containerURL* és a *blockBlobURL* változók végig használatban maradnak a mintában a tárfiókon végrehajtott műveletekben. 

Ezen a ponton a tároló nem létezik a tárfiókban. A *ContainerURL*-példány jelöli azt az URL-címet, amelyet használhat. A példány használatával hozhatja létre és törölheti a tárolót. A tároló helye megfelel egy, a következőhöz hasonló helynek felel meg:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

A *blockBlobURL* az egyes blobok kezelésére szolgál, és a blobok tartalmának feltöltéséhez, letöltéséhez és törléséhez használható. Az itt bemutatott URL a következő helyhez hasonló:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```
Ahogy a tároló, úgy még a blokkblob sem létezik. A *blockBlobURL* változót később használjuk majd a blob tartalmak feltöltésével való létrehozásához.

### <a name="using-the-aborter-class"></a>Az Aborter osztály használata

Beállítható, hogy az API-kérelmek egy adott időtartam után időtúllépésbe kerüljenek. Az *Aborter* osztály feladata a kérelmek időtúllépésének kezelése. Az alábbi kód egy környezetet hoz létre, amelyben kérelmek egy adott csoportjának 30 perce van a lefutásra.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```
Az Aborterek (megszakítók) lehetővé teszik a kérelmek szabályzását, mivel a segítségükkel:

- kijelölhető egy adott kérelemköteg számára rendelkezésre álló idő,
- kijelölhető, hogy egy adott egyéni kérelem milyen hosszan futhat a kötegben,
- visszavonhatók a kérelmek,
- az *Aborter.None* statikus tag használatával megakadályozható, hogy a kérelmek egyáltalán időtúllépésbe kerüljenek

### <a name="show-container-names"></a>Tárolónevek megjelenítése
A fiókok rengeteg tárolót tartalmazhatnak. A következő kód bemutatja, hogyan lehet a tárolókat szegmentált módon listázni, aminek segítségével nagy számú tárolót tekinthet át. A *showContainerNames* függvénynek *ServiceURL*- és *Aborter*-példányokat adunk át.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```
A *showContainerNames* függvény a *listContainersSegment* metódus használatával tárolónevek kötegeit kéri le a tárfiókról.
```javascript
async function showContainerNames(aborter, serviceURL) {

    let response;
    let marker;

    do {
        response = await serviceURL.listContainersSegment(aborter, marker);
        marker = response.marker;
        for(let container of response.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```
A válasz visszaérkezésekor a *containerItems* többszöri végrehajtásával adható át a név a konzolnak. 

### <a name="create-a-container"></a>Tároló létrehozása

A tárolók létrehozásához a *ContainerURL* osztály *create* metódusa használható.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```
Mivel a tároló neve a *ContainerURL.fromServiceURL(serviceURL, containerName)* meghívásakor definiálva lesz, a tároló létrehozásához elegendő a *create* metódust meghívni.

### <a name="upload-text"></a>Szöveg feltöltése
A szövegek feltöltéséhez a blobba használja az *upload* metódust.
```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```
Itt a szöveget és annak hosszát adjuk át a metódusnak.
### <a name="upload-a-local-file"></a>Helyi fájl feltöltése
A helyi fájlok a tárolóba való feltöltéséhez a tároló URL-címére és a fájl elérési útjára van szükség.
```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```
Az *uploadLocalFile* függvény meghívja az *uploadFileToBlockBlob* függvényt, amely a fájl elérési útját és a blokkblob céljának egy példányát veszi fel argumentumként.
```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```
### <a name="upload-a-stream"></a>Stream feltöltése
A streamek feltöltése is támogatott. A minta egy helyi fájlt streamként megnyit és átad az upload metódusnak.
```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```
Az *uploadStream* függvény az *uploadStreamToBlockBlob* meghívásával tölti fel a streamet a tárolóba.
```javascript
async function uploadStream(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```
A feltöltés során az *uploadStreamToBlockBlob* pufferek lefoglalásával biztosítja a stream adatainak gyorsítótárazását, amennyiben újra kellene próbálkozni. A *maxBuffers* érték jelzi, hogy legfeljebb hány puffer használható, mivel mindegyik puffer külön fájlfeltöltési kérelmet hoz létre. Ideális esetben a több puffer nagyobb sebességet eredményez, azonban így nő a memóriahasználat. A pufferek kellően magas száma esetén a feltöltési sebesség elér egy korlátot, és már a hálózat vagy a meghajtó jelenti a szűk keresztmetszetet az ügyfél helyett.

### <a name="show-blob-names"></a>Blobnevek megjelenítése
Ahogy az egyes fiókok több tárolót is tartalmazhatnak, az egyes tárolók maguk is potenciálisan hatalmas mennyiségű blobot tartalmazhatnak. A tárolóban lévő egyes blobok a *ContainerURL* osztály egy példányán keresztül érhetők el. 
```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```
A *showBlobNames* függvény a *listBlobFlatSegment* meghívásával kéri le a blobkötegeket a tárolókból.
```javascript
async function showBlobNames(aborter, containerURL) {

    let response;
    let marker;

    do {
        response = await containerURL.listBlobFlatSegment(aborter);
        marker = response.marker;
        for(let blob of response.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```
### <a name="download-a-blob"></a>Blob letöltése
A létrehozását követően a blob tartalma a *download* metódussal tölthető le.
```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = downloadResponse.readableStreamBody.read(content.length).toString();
console.log(`Downloaded blob content: "${downloadedContent}"`);
```
A rendszer a választ streamként adja vissza. Ebben a példában a streamet egy sztringgé alakítjuk a konzolba való megadáshoz.
### <a name="delete-a-blob"></a>Blob törlése
A blobok a *BlockBlobURL*-példányok *delete* metódusával törölhetők a tárolóból.
```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```
### <a name="delete-a-container"></a>Tároló törlése
A tárolók a *ContainerURL*-példányok *delete* metódusával törölhetők a tárfiókból.
```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A kódminta végén a tárfiókba írt összes adat automatikusan törölve lesz. 

## <a name="next-steps"></a>További lépések

Ez a rövid útmutató azt mutatja be, hogyan kezelhetők a blobok és tárolók az Azure Blob Storage-tárfiókokban a Node.js használatával. Az SDK használatával kapcsolatos bővebb információkért lásd a GitHub-adattárat.

> [!div class="nextstepaction"]
> [A JavaScripthez készült Azure Storage SDK 10-es verziójának adattára](https://github.com/Azure/azure-storage-js)