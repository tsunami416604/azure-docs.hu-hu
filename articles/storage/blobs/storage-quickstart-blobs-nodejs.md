---
title: Azure rövid útmutató – Blobok feltöltése, letöltése és listázása az Azure Storage-ban a Node.js használatával | Microsoft Docs
description: Ebben a rövid útmutatóban egy tárfiókot és egy tárolót hoz létre. Majd a Storage Node.js-hez készült ügyféloldali kódtára segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: cshoe
ms.openlocfilehash: 28f9936c297b6f641810e0c7783f4d84be108286
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Rövid útmutató: blobok feltöltése, letöltése és listázása a Node.js használatával

A rövid útmutató azt ismerteti, hogyan használható a Node.js blokkblobok feltöltésére, letöltésére és listázására egy, az Azure Blob Storage-ben található tárolóban.

A rövid útmutató elvégzéséhez szüksége lesz egy [Azure-előfizetésre](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) egy egyszerű Node.js-konzolalkalmazás. Első lépésként klónozza az adattárat a gépre a következő paranccsal:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Az alkalmazás megnyitásához keresse meg a *storage-blobs-node-quickstart* mappát, és nyissa meg kedvenc kódszerkesztő környezetében.

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati karakterlánc konfigurálása

Az alkalmazás futtatása előtt meg kell adnia a tárfiókjához tartozó kapcsolati karakterláncot. A mintatadattár tartalmaz egy *.env.example* nevű fájlt. Ezt a fájlt átnevezheti a *.example* bővítmény eltávolításával, amelynek eredményeként létrejön egy *.env* nevű fájl. A *.env* fájlban adja meg a kapcsolati karakterlánc értékét az *AZURE_STORAGE_CONNECTION_STRING* kulcs után.

## <a name="install-required-packages"></a>Szükséges csomagok telepítése

Az alkalmazás könyvtárában futtassa az *npm install* parancsot az alkalmazáshoz szükséges csomagok telepítéséhez.

```bash
npm install
```

## <a name="run-the-sample"></a>Minta futtatása
A függőségek telepítését követően a szkriptbe parancsokat továbbítva futtathatja a mintát. Például futtassa a következő parancsot egy blobtároló létrehozásához:

```bash
node index.js --command createContainer
```

Az elérhető parancsok a következők:

| Parancs | Leírás |
|---------|---------|
|*createContainer* | Létrehoz egy *test-container* nevű tárolót (akkor is sikeres lesz, ha a tároló már létezik) |
|*upload*          | Feltölti az *example.txt* fájlt a *test-container* tárolóba |
|*download*        | Letölti az *example* blob tartalmát az *example.downloaded.txt* nevű fájlba |
|*törlés*          | Törli az *example* blobot |
|*list*            | Listázza a *test-container* tároló tartalmát a konzolban |


## <a name="understanding-the-sample-code"></a>A mintakód értelmezése
Ez a kódminta néhány modult használ a fájlrendszerhez és a parancssorhoz való csatlakozáshoz. 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

A modulok rendeltetése a következő: 

- a *dotenv* betölti a *.env* nevű fájlban meghatározott környezeti változókat az aktuális végrehajtási környezetbe
- a *path* a blobtárolóba feltölteni kívánt fájl abszolút elérési útjának meghatározásához szükséges
- a *yargs* egy egyszerű felületet kínál a parancssori argumentumok eléréséhez
- az *azure-storage* a Node.js [Azure Storage SDK](/nodejs/api/azure-storage) modulja

Ez után a változók egy sorának inicializálása következik:

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

A változók a következő értékekre vannak beállítva:

- a *blobService* változó az Azure Blob szolgáltatás egy új példányára van beállítva
- a *containerName* változó a tároló nevére van beállítva
- a *sourceFilePath* változó a feltölteni kívánt fájl abszolút elérési útvonalára van beállítva
- a *blobName* változó a fáj neve lesz kiterjesztés nélkül

A következő implementációban a *blobService* függvények mindegyike egy *Promise* ígéret részét képezi, amely hozzáférést biztosít a JavaScript *async* függvényéhez és *await* operátorához, ezáltal zökkenőmentessé teszi az [Azure Storage API](/nodejs/api/azure-storage/blobservice) visszahívásainak működését. Ha minden függvényre sikeres válasz érkezik, az ígéret a kapcsolódó adatokkal és a műveletre vonatkozó üzenettel oldódik fel.

### <a name="create-a-blob-container"></a>Blobtároló létrehozása

A *createContainer* függvény meghívja a [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) függvényt, és beállítja a megfelelő hozzáférési szintet a blobhoz.

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

A **createContainerIfNotExists** függvény második paramétere (*options*) egy [publicAccessLevel](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) értéket fogad el. A *publicAccessLevel* *blob* értéke határozza meg bizonyos blobadatok nyilvánossá tételét. Ez a beállítás a *container* szintű hozzáféréssel ellentétben nem a tároló tartalmának listázását teszi lehetővé.

A **createContainerIfNotExists** függvény engedélyezi az alkalmazás számára a *createContainer* parancs többszöri használatát anélkül, hogy hibát adna vissza, ha a tároló már létezik. Éles környezetben gyakran csak egyszer hívja meg a **createContainerIfNotExists** függvényt, ha mindig ugyanazt a tárolót használja az alkalmazásban. Ilyen esetekben előre is létrehozhatja a tárolót a portálon vagy az Azure CLI-n keresztül.

### <a name="upload-a-blob-to-the-container"></a>A blob feltöltése a tárolóba

Az *upload* függvény a [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) függvényt használja a fájlrendszer egy fájljának a blobtárolóba való feltöltésére és írására vagy felülírására. 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
A mintaalkalmazás környezetén belül az *example.txt* nevű fájl lesz feltöltve az *example* nevű blobba a *test-contaier* nevű tárolóban. A tartalmak blobokba való feltöltéséhez egyéb megközelítések is elérhetők, például a [text](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) és a [streams](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream) használata.

Ha ellenőrizni szeretné, hogy a fájl feltöltődött-e a blobtárolóba, használhatja az [Azure Storage Explorert](https://azure.microsoft.com/en-us/features/storage-explorer/) a fiókban szereplő adatok megtekintéséhez.

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A *list* függvény meghívja a [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) metódust, amely egy tároló blobmetaadatainak listáját adja vissza. 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

A *listBlobsSegmented* metódus [BlobResult](/nodejs/api/azure-storage/blobresult) példányok tömbjeként adja vissza a blobmetaadatokat. Az eredményeket a rendszer 5000 növekményes kötegekben (szegmensekben) adja vissza. Ha egy tároló több, mint 5000 blobot tartalmaz, akkor az eredmények egy **continuationToken** értéket is tartalmaznak. A blobtárolóban szereplő további szegmensek listázásához a folytatási jogkivonatot továbbíthatja a **listBlobSegmented** metódusba második argumentumként.

### <a name="download-a-blob-from-the-container"></a>Blob letöltése a tárolóból

A *download* függvény a [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile) utasítást használja a blob tartalmának a megadott abszolút fájlelérési útvonalra való letöltéséhez.

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
Az itt látható megvalósítás megváltoztatja a forrásfájl útvonalát, és hozzáfűzi a *.downloaded.txt* kifejezést a fájl nevéhez. Valós környezetben a letöltés céljának kiválasztásakor módosíthatja a helyet, valamint a fájl nevét.

### <a name="delete-blobs-in-the-container"></a>Blobok törlése a tárolóban

A *deleteBlock* függvény (a *delete* konzolparancs aliasnévvel) meghívja a [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) függvényt. Ahogy a neve is mutatja, ez a függvény nem ad vissza hibát, ha blob már törölve van.

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>Feltöltés és listázás

Az ígéretek használatának egyik előnye, hogy több parancsot is össze lehet fűzni. Az **uploadAndList** függvény példájából látható, hogy mennyire egyszerűen listázható egy blob tartalma közvetlenül a fájl feltöltése után.

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>Hívó kód

A parancssorba implementált függvények közzétételéhez minden egyes funkciót egy objektumliterálhoz kell rendelni.

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

Most, hogy a *_module* kifejezés a megfelelő helyre került, a parancsok mindegyike elérhető a paracssorból.

```javascript
const commandExists = () => exists = !!_module[args.command];
```

Ha egy adott parancs nem létezik, a *_module* tulajdonságai súgószövegként jelennek meg a felhasználó számára a konzolban. 

Az *executeCommand* függvény olyan *async* függvény, amely az *await* operátor segítségével meghívja az adott parancsot, és a konzolban naplózza az adatokhoz küldött üzeneteket.

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

Végezetül a végrehajtó kód először meghívja a *commandExists* változót egy ismert parancs a szkriptbe való továbbításának ellenőrzéséhez. Ha ki van választva egy meglévő parancs, akkor a rendszer futtatja a parancsot, és a felmerülő hibákat naplózza a konzolban.

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha nem tervezi a cikkben létrehozott adatok vagy fiókok használatát, a nem kívánt számlázás elkerülése érdekében érdemes törölnie azokat. A blobok és tárolók törléséhez használhatja a [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteBlobIfExists_container__blob__options__callback_) és a [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteContainerIfExists_container__options__callback_) metódusokat. A tárfiókot [a portálon keresztül](../common/storage-create-storage-account.md) is törölheti.

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Blobokkal rendelkező Node.js-alkalmazások fejlesztéséhez használható forrásanyagok

Blob Storage-tárolókat alkalmazó Node.js-alkalmazások fejlesztéséhez tekintse át az alábbi további forrásanyagokat is:

### <a name="binaries-and-source-code"></a>Bináris fájlok és forráskód

- Az Azure Storage-hoz készült [Node.js ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-node) megtekintheti és telepítheti a GitHubról.

### <a name="client-library-reference-and-samples"></a>Ügyféloldali kódtár – referencia és minták

- A Node.js ügyféloldali kódtárral kapcsolatos további információért lásd [a Node.js API-referenciáját](https://docs.microsoft.com/en-us/javascript/api/overview/azure/storage).
- Tekintse át a Node.js ügyféloldali kódtár használatával írt [Blob Storage-mintákat](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob).

## <a name="next-steps"></a>További lépések

Ez a rövid útmutató azt mutatja be, hogyan tölthetők fel fájlok egy helyi lemez és az Azure Blob Storage között a Node.js használatával. Ha bővebb információra van szüksége a Blob Storage használatával kapcsolatban, lépjen tovább a Blob Storage használati útmutatójára.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](storage-nodejs-how-to-use-blob-storage.md)

Az Azure Storage Node.js-referenciájáért lásd: [azure-storage csomag](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest).