---
title: 'Rövid útmutató: blobok feltöltése, letöltése és listázása a Node.js használatával – Azure Storage'
description: Létre fog hozni egy tárfiókot és egy tárolót egy objektumtárban (Blobtárban). Majd a Storage Node.js-hez készült ügyféloldali kódtára segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 09/20/2018
ms.author: tamram
ms.openlocfilehash: 267218b4b958251e6748fbae17ca1ac267cf3287
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140587"
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Rövid útmutató: blobok feltöltése, letöltése és listázása a Node.js használatával

A rövid útmutató azt ismerteti, hogyan használható a Node.js blobok feltöltésére, letöltésére és listázására, valamint tárolók kezelésére az Azure Blob Storage-ben.

A rövid útmutató elvégzéséhez szüksége lesz egy [Azure-előfizetésre](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) egy egyszerű Node.js-konzolalkalmazás. Első lépésként klónozza az adattárat a gépre a következő paranccsal:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Az alkalmazás megnyitásához keresse meg a *storage-blobs-node-quickstart* mappát, és nyissa meg kedvenc kódszerkesztő környezetében.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

Az alkalmazás futtatása előtt meg kell adnia a tárfiókjához tartozó kapcsolati sztringet. A mintatadattár tartalmaz egy *.env.example* nevű fájlt. Ezt a fájlt átnevezheti a *.example* bővítmény eltávolításával, amelynek eredményeként létrejön egy *.env* nevű fájl. A *.env* fájlban adja meg a kapcsolati sztring értékét az *AZURE_STORAGE_CONNECTION_STRING* kulcs után.

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

A szkript kimenete az alábbihoz hasonló lesz:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Vegye figyelembe, hogy ha ebben a rövid útmutatóban egy új tárfiókot használ, előfordulhat, hogy nem látszanak a tárolónevek a „*Tárolók*” címke alatt.

## <a name="understanding-the-code"></a>A kód értelmezése
Az első kifejezés értékek betöltésére szolgál a környezeti változókba.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

A *dotenv* modul betölti a környezeti változókat, ha az alkalmazás helyileg fut hibakeresés céljából. Az értékek egy *.env* fájlban vannak meghatározva. A modul innen tölti be őket az aktuális végrehajtási környezetbe. Éles környezetben a kiszolgáló konfigurációja biztosítja ezeket az értékeket, ezért ezt a kód csak akkor lesz futtatva, ha a szkript nem „éles” környezetben fut.

```javascript
const path = require('path');
const storage = require('azure-storage');
```

A modulok rendeltetése a következő: 

az *.env* fájl betöltése az aktuális végrehajtási környezetbe
- a *path* a blobtárolóba feltölteni kívánt fájl abszolút elérési útjának meghatározásához szükséges
- az *azure-storage* a Node.js [Azure Storage SDK](https://docs.microsoft.com/javascript/api/azure-storage) modulja

Ezután a **blobService** változó az Azure Blob service egy új példányaként lesz inicializálva.

```javascript
const blobService = storage.createBlobService();
```

A következő implementációban a *blobService* függvények mindegyike egy *Promise* ígéret részét képezi, amely hozzáférést biztosít a JavaScript *async* függvényéhez és *await* operátorához, ezáltal zökkenőmentessé teszi az [Azure Storage API](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) visszahívásainak működését. Ha minden függvényre sikeres válasz érkezik, az ígéret a kapcsolódó adatokkal és a műveletre vonatkozó üzenettel oldódik fel.

### <a name="list-containers"></a>Tárolók listázása

A *listContainers* függvény meghívja a [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listcontainerssegmented) függvényt, amely csoportokba rendezett tárológyűjteményeket ad vissza.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

A csoportok mérete a [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest) függvénnyel konfigurálható. A *listContainersSegmented* függvény [ContainerResult](/nodejs/api/azure-storage/blobresult) példányok tömbjeként adja vissza a blobmetaadatokat. Az eredményeket a rendszer 5000 növekményes kötegekben (szegmensekben) adja vissza. Ha egy tároló több, mint 5000 blobot tartalmaz, akkor az eredmények egy *continuationToken* értéket is tartalmaznak. A blobtárolóban szereplő további szegmensek listázásához továbbíthatja a folytatási jogkivonatot a *listContainersSegment* függvénybe második argumentumként.

### <a name="create-a-container"></a>Tároló létrehozása

A *createContainer* függvény meghívja a [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createcontainerifnotexists) függvényt, és beállítja a megfelelő hozzáférési szintet a blobhoz.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

A **createContainerIfNotExists** függvény második paramétere (*options*) egy [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createcontainerifnotexists) értéket fogad el. A *publicAccessLevel* *blob* értéke határozza meg bizonyos blobadatok nyilvánossá tételét. Ez a beállítás a *container* szintű hozzáféréssel ellentétben nem a tároló tartalmának listázását teszi lehetővé.

A **createContainerIfNotExists** függvény engedélyezi az alkalmazás számára a *createContainer* parancs többszöri használatát anélkül, hogy hibát adna vissza, ha a tároló már létezik. Éles környezetben gyakran csak egyszer hívja meg a **createContainerIfNotExists** függvényt, ha mindig ugyanazt a tárolót használja az alkalmazásban. Ilyen esetekben előre is létrehozhatja a tárolót a portálon vagy az Azure CLI-n keresztül.

### <a name="upload-text"></a>Szöveg feltöltése

Az *uploadString* függvény meghívja a [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromtext) metódust, hogy írjon (vagy felülírjon) egy tetszőleges sztringet a blobtárolóban.

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>Helyi fájl feltöltése

Az *uploadLocalFile* függvény a [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) metódust használja a fájlrendszer egy fájljának a blobtárolóba való feltöltésére és írására (vagy felülírására). 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
A tartalmak blobokba való feltöltéséhez egyéb megközelítések is elérhetők, például a [text](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) és a [streams](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream) használata. Ha ellenőrizni szeretné, hogy a fájl feltöltődött-e a blobtárolóba, használhatja az [Azure Storage Explorert](https://azure.microsoft.com/features/storage-explorer/) a fiókban szereplő adatok megtekintéséhez.

### <a name="list-the-blobs"></a>A blobok listázása

A *listBlobs* függvény meghívja a [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) metódust, amely egy tároló blobmetaadatainak listáját adja vissza. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

A *listBlobsSegmented* metódus [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest) példányok tömbjeként adja vissza a blobmetaadatokat. Az eredményeket a rendszer 5000 növekményes kötegekben (szegmensekben) adja vissza. Ha egy tároló több, mint 5000 blobot tartalmaz, akkor az eredmények egy **continuationToken** értéket is tartalmaznak. A blobtárolóban szereplő további szegmensek listázásához a folytatási jogkivonatot továbbíthatja a **listBlobSegmented** metódusba második argumentumként.

### <a name="download-a-blob"></a>Blob letöltése

A *downloadBlob* függvény a [getBlobToText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#getblobtotext) utasítást használja a blob tartalmának letöltéséhez a megadott abszolút fájlelérési útvonalra.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
Az itt látható implementáció módosítja a forrást, és a blob tartalmát sztringként adja vissza. Egy blobot továbbá [adatfolyamként](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#getblobtostream), illetve közvetlenül egy [helyi fájlba](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#getblobtolocalfile) is letölthet.

### <a name="delete-a-blob"></a>Blob törlése

A *deleteBlob* függvény meghívja a [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) függvényt. Ahogy a neve is mutatja, ez a függvény nem ad vissza hibát, ha a blob már törölve van.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>Tároló törlése

Tárolókat úgy törölhet, ha meghívja a *deleteContainer* metódust a blobszolgáltatásból, majd átadja a szolgáltatásnak a tároló nevét.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>Hívó kód

A JavaScript *async/await* szintaxisának támogatása érdekében minden hívókód egy *execute* nevű függvénybe van csomagolva. Tehát a rendszer az execute függvényt fogja hívni, amelyet egy ígéretként kezel.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Az alábbi összes kód az execute függvényen belül fut, ahová a `// commands` megjegyzés kerül.

Első lépésként a releváns változók úgy vannak deklarálva, hogy neveket és mintatartalmakat rendeljenek hozzá, valamint, hogy a Blobtárolóba feltölteni kívánt helyi fájlra mutassanak.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

A tárfiókban található tárolók listázásához a listContainers függvényt kell meghívni, és a visszaadott tárolólista fel lesz jegyezve a kimeneti ablakban.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

Amint elérhető a tárolók listája, a tömbös *findIndex* metódus használatával ellenőrizheti, hogy a létrehozni kívánt tároló létezik-e már. Ha a tároló nem létezik, a rendszer létrehozza.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
Következő lépésként egy sztring és egy helyi fájl lesz feltöltve a Blobtárolóba.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
A blobok listázási folyamata ugyanaz, mint a tárolók esetében. A *listBlobs* meghívása egy, a tárolóban található blobtömböt ad vissza, amelyet a rendszer naplóz a kimeneti ablakban.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

Egy blob letöltéséhez a válasz rögzítve lesz, majd ennek használatával fog a rendszer hozzáférni a blob értékéhez. A válaszból a readableStreamBody érték karakterlánccá alakul, és naplózva lesz a kimeneti ablakban.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

Végül a rendszer törli a blobot és a tárolót a tárfiókból.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A kódminta végén a tárfiókba írt összes adat automatikusan törölve lesz. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Blobokkal rendelkező Node.js-alkalmazások fejlesztéséhez használható forrásanyagok

Blob Storage-tárolókat alkalmazó Node.js-alkalmazások fejlesztéséhez tekintse át az alábbi további forrásanyagokat is:

### <a name="binaries-and-source-code"></a>Bináris fájlok és forráskód

- Az Azure Storage-hoz készült [Node.js ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-node) megtekintheti és telepítheti a GitHubról.

### <a name="client-library-reference-and-samples"></a>Ügyféloldali kódtár – referencia és minták

- A Node.js ügyféloldali kódtárral kapcsolatos további információért lásd [a Node.js API-referenciáját](https://docs.microsoft.com/javascript/api/overview/azure/storage).
- Tekintse át a Node.js ügyféloldali kódtár használatával írt [Blob Storage-mintákat](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob).

## <a name="next-steps"></a>További lépések

Ez a rövid útmutató azt mutatja be, hogyan tölthetők fel fájlok egy helyi lemez és az Azure Blob Storage között a Node.js használatával. Ha bővebb információra van szüksége a Blobtárolók használatával kapcsolatban, lépjen tovább a GitHub adattárára.

> [!div class="nextstepaction"]
> [Azure Storage SDK JavaScript-adattárhoz](https://github.com/Azure/azure-storage-node)