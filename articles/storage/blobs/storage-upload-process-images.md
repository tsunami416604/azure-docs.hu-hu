---
title: Képadatok feltöltése a felhőbe az Azure Storage segítségével | Microsoft Docs
description: Az Azure Blob Storage használata webalkalmazással az alkalmazásadatok tárolásához
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: mhopkins
ms.reviewer: dineshm
ms.openlocfilehash: afa4672d2a8f65b61d634b95b695d1c9814bf991
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053448"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Oktatóanyag: képadatok feltöltése a felhőbe az Azure Storage szolgáltatással

Ez az oktatóanyag egy sorozat első része. Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy webalkalmazást, amely az Azure Storage ügyféloldali kódtárat használja a lemezképek Storage-fiókba való feltöltéséhez. Ha elkészült, egy olyan webalkalmazás fog rendelkezni, amely az Azure Storage-ban tárolja és megjeleníti a lemezképeket.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![Rendszerkép-alkalmazás a .NET-ben](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node. js v2 SDK](#tab/nodejs)
![A Node. js v2-ben elérhető rendszerkép-alkalmazás](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node. js v10 SDK](#tab/nodejsv10)
![Képresizer alkalmazás Node. js v10-ben](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Create a storage account
> * Tároló létrehozása és az engedélyek beállítása
> * Hozzáférési kulcs lekérése
> * Webalkalmazás üzembe helyezése az Azure-ban
> * Alkalmazásbeállítások konfigurálása
> * Interakció a webalkalmazással

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre. Mielőtt elkezdené, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A parancssori felület helyi telepítéséhez és használatához az oktatóanyaghoz az Azure CLI 2.0.4 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása 

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.  

A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia 
```

## <a name="create-a-storage-account"></a>Create a storage account

A minta képeket tölt fel egy Azure Storage-fiókban lévő blobtárolóba. A tárfiók egy egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és hozzáféréséhez. Az [az storage account create](/cli/azure/storage/account) paranccsal hozzon létre egy tárfiókot a létrehozott erőforráscsoportban.

> [!IMPORTANT]
> Az oktatóanyag 2. részében a blob Storage-Azure Event Gridt használja. Győződjön meg arról, hogy a Storage-fiókot egy olyan Azure-régióban hozza létre, amely támogatja a Event Grid. A támogatott régiók listáját lásd: Azure- [termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

A következő parancsban cserélje le a saját globálisan egyedi nevét arra a blob Storage-fiókra, amelyben a `<blob_storage_account>` helyőrző látható.  

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

az storage account create --name $blobStorageAccount \
--location southeastasia --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>BLOB Storage-tárolók létrehozása

Az alkalmazás két tárolót használ a Blob Storage-fiókban. A tárolók hasonlók a mappákhoz, és tárolják a blobokat. A *képek* tároló az a hely, ahová az alkalmazás feltölti a teljes felbontású képeket. A sorozat egy későbbi részében egy Azure-függvényalkalmazás tölt fel átméretezett képminiatűröket a *thumbnails* tárolóba.

Szerezze be a tárfiókkulcsot az [az storage account keys list](/cli/azure/storage/account/keys) parancs segítségével. Ezután ezt a kulcsot használva hozzon létre két tárolót az az [Storage Container Create](/cli/azure/storage/container) paranccsal.  

A *rendszerkép* -tároló nyilvános hozzáférése `off`értékre van állítva. A *miniatűrök* tároló nyilvános hozzáférése `container`értékre van állítva. A `container` nyilvános hozzáférés beállítás lehetővé teszi, hogy a weblapon megtekintő felhasználók megtekinthessék a miniatűrt.

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey
```

Jegyezze fel a blob Storage-fiók nevét és kulcsát. A minta alkalmazás ezeket a beállításokat használva csatlakozik a Storage-fiókhoz a lemezképek feltöltéséhez. 

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Az alkalmazást tároló webkiszolgálófarm helyét, méretét és funkcióit egy [App Service-csomag](../../app-service/overview-hosting-plans.md) határozza meg.

Hozzon létre egy App Service-csomagot az [az appservice plan create](/cli/azure/appservice/plan) paranccsal.

Az alábbi példa egy `myAppServicePlan` nevű App Service-csomag létrehozását mutatja be az **INGYENES** tarifacsomagban:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A webalkalmazás üzemeltetési területet biztosít a GitHub-minta tárházban üzembe helyezett minta alkalmazás kódjához. Az [ az webapp create](/cli/azure/webapp) paranccsal hozzon létre egy [webalkalmazást](../../app-service/overview.md) a `myAppServicePlan` App Service-csomagban.  

A következő parancsban cserélje le a `<web_app>`t egyedi névre. Érvényes karakterek: `a-z`, `0-9` és `-`. Ha az `<web_app>` nem egyedi, a következő hibaüzenet jelenik meg: _A megadott `<web_app>` névvel már létezik webhely._ A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp=<web_app>

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Mintaalkalmazás üzembe helyezése a GitHub-adattárból

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével.

A minta projekt egy [ASP.net MVC](https://www.asp.net/mvc) -alkalmazást tartalmaz. Az alkalmazás elfogadja a lemezképet, menti azt egy Storage-fiókba, és megjeleníti a képeket egy miniatűr tárolóból. A webalkalmazás a [Microsoft. Azure. Storage](/dotnet/api/overview/azure/storage), a [Microsoft. Azure. Storage. blob](/dotnet/api/microsoft.azure.storage.blob)és a Microsoft. Azure. Storage. auth névtereket használja az Azure Storage ügyféloldali könyvtárából az Azure Storage szolgáltatással való kommunikációhoz.

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node. js v2 SDK](#tab/nodejs)
Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével. 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node. js v10 SDK](#tab/nodejsv10)
Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével. 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10
```

---

## <a name="configure-web-app-settings"></a>A webalkalmazás beállításainak konfigurálása

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

A minta-webalkalmazás az [Azure Storage ügyféloldali kódtár](/dotnet/api/overview/azure/storage) segítségével kér a képfeltöltéshez használt hozzáférési jogkivonatokat. A Storage SDK által használt Storage-fiók hitelesítő adatai a webalkalmazás alkalmazás-beállításaiban vannak beállítva. Adja hozzá az alkalmazás beállításait az üzembe helyezett alkalmazáshoz az az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings) paranccsal.

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=$blobStorageAccountKey  
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node. js v2 SDK](#tab/nodejs)

A minta-webalkalmazás az [Azure Storage ügyféloldali kódtár](https://docs.microsoft.com/javascript/api/azure-storage) segítségével kér a képfeltöltéshez használt hozzáférési jogkivonatokat. A Storage SDK által használt Storage-fiók hitelesítő adatai a webalkalmazás alkalmazás-beállításaiban vannak beállítva. Adja hozzá az alkalmazás beállításait az üzembe helyezett alkalmazáshoz az az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings) paranccsal.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__AccountKey=$blobStorageAccountKey \
AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node. js v10 SDK](#tab/nodejsv10)

A minta-webalkalmazás az [Azure Storage ügyféloldali kódtár](https://github.com/Azure/azure-storage-js) segítségével kér a képfeltöltéshez használt hozzáférési jogkivonatokat. A Storage SDK által használt Storage-fiók hitelesítő adatai a webalkalmazás alkalmazás-beállításaiban vannak beállítva. Adja hozzá az alkalmazás beállításait az üzembe helyezett alkalmazáshoz az az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings) paranccsal.

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

A webalkalmazás üzembe helyezése és konfigurálása után tesztelheti a rendszerkép feltöltési funkcióját az alkalmazásban.

## <a name="upload-an-image"></a>Lemezkép feltöltése

A webalkalmazás teszteléséhez nyissa meg a közzétett alkalmazás URL-címét. A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Válassza a **fényképek feltöltése** régiót egy fájl kiválasztásához és feltöltéséhez, vagy húzzon egy fájlt a régióra. Sikeres feltöltés esetén a kép eltűnik. A **létrehozott miniatűrök** szakasz üres marad, amíg a témakör későbbi szakaszában nem teszteljük.

![Fényképek feltöltése a .NET-ben](media/storage-upload-process-images/figure1.png)

A mintakód a *Storagehelper.cs* fájl `UploadFiletoStorage` feladatát használja *a lemezképek tárolójába* való feltöltéséhez a Storage-fiókon belül a [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) metódus használatával. A következő mintakód tartalmazza a `UploadFiletoStorage` műveletet.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

Az előző feladatban használt osztályok és módszerek a következők:

|Osztály  |Módszer  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.azure.cosmos.table.storagecredentials)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)       |
|[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)     |[GetContainerReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getcontainerreference)         |
|[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer)    | [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference)        |
|[CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob)     | [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.file.cloudfile.uploadfromstreamasync)        |

# <a name="nodejs-v2-sdktabnodejs"></a>[Node. js v2 SDK](#tab/nodejs)

Válassza a **fájl** kiválasztása lehetőséget egy fájl kiválasztásához, majd kattintson a **rendszerkép feltöltése**elemre. A **létrehozott miniatűrök** szakasz üres marad, amíg a témakör későbbi szakaszában nem teszteljük. 

![Fényképek feltöltése a Node. js v2-ben](media/storage-upload-process-images/upload-app-nodejs.png)

A mintakódban a `post` útvonal felelős a képek blobtárolóba való feltöltéséért. Az útvonal modulok használatával segíti a feltöltés feldolgozását:

- a [multer](https://github.com/expressjs/multer) megvalósítja a feltöltési stratégiát az útválasztási kezelőhöz.
- a [into-stream](https://github.com/sindresorhus/into-stream) a [createBlockBlobFromStream] által megkövetelt módon átalakítja a puffert egy adatfolyamba. (https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html)

Ahogy a fájl el lesz küldve az útvonalra, a fájl tartalma a memóriában marad mindaddig, amíg a fájl fel nem töltődik a blob-tárolóba.

> [!IMPORTANT]
> A nagyméretű fájlok memóriába való betöltése negatív hatással lehet a webalkalmazás teljesítményére. Ha azt szeretné, hogy a felhasználók nagy fájlokat tegyenek közzé, érdemes megfontolnia a webkiszolgáló fájlrendszerének átmeneti fájljait, majd a feltöltéseket a blob Storage-ba ütemezve. Miután a fájlok a blob Storage-ban találhatók, eltávolíthatja őket a kiszolgálói fájlrendszerből.

```javascript
const
      express = require('express')
    , router = express.Router()

    , multer = require('multer')
    , inMemoryStorage = multer.memoryStorage()
    , uploadStrategy = multer({ storage: inMemoryStorage }).single('image')

    , azureStorage = require('azure-storage')
    , blobService = azureStorage.createBlobService()

    , getStream = require('into-stream')
    , containerName = 'images'
;

const handleError = (err, res) => {
    res.status(500);
    res.render('error', { error: err });
};

const getBlobName = originalName => {
    const identifier = Math.random().toString().replace(/0\./, ''); // remove "0." from start of string
    return `${originalName}-${identifier}`;
};

router.post('/', uploadStrategy, (req, res) => {

    const
          blobName = getBlobName(req.file.originalname)
        , stream = getStream(req.file.buffer)
        , streamLength = req.file.buffer.length
    ;

    blobService.createBlockBlobFromStream(containerName, blobName, stream, streamLength, err => {

        if(err) {
            handleError(err);
            return;
        }

        res.render('success', { 
            message: 'File uploaded to Azure Blob storage.' 
        });
    });
});
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node. js v10 SDK](#tab/nodejsv10)

Válassza a **fájl** kiválasztása lehetőséget egy fájl kiválasztásához, majd kattintson a **rendszerkép feltöltése**elemre. A **létrehozott miniatűrök** szakasz üres marad, amíg a témakör későbbi szakaszában nem teszteljük. 

![Fényképek feltöltése a Node. js v10-ben](media/storage-upload-process-images/upload-app-nodejs.png)

A mintakódban a `post` útvonal felelős a képek blobtárolóba való feltöltéséért. Az útvonal modulok használatával segíti a feltöltés feldolgozását:

- a [multer](https://github.com/expressjs/multer) megvalósítja a feltöltési stratégiát az útválasztási kezelőhöz.
- a [into-stream](https://github.com/sindresorhus/into-stream) a [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html)által előírt módon átalakítja a puffert egy adatfolyamba.

Ahogy a fájl el lesz küldve az útvonalra, a fájl tartalma a memóriában marad mindaddig, amíg a fájl fel nem töltődik a blob-tárolóba.

> [!IMPORTANT]
> A nagyméretű fájlok memóriába való betöltése negatív hatással lehet a webalkalmazás teljesítményére. Ha azt szeretné, hogy a felhasználók nagy fájlokat tegyenek közzé, érdemes megfontolnia a webkiszolgáló fájlrendszerének átmeneti fájljait, majd a feltöltéseket a blob Storage-ba ütemezve. Miután a fájlok a blob Storage-ban találhatók, eltávolíthatja őket a kiszolgálói fájlrendszerből.

```javascript
const {
  Aborter,
  BlobURL,
  BlockBlobURL,
  ContainerURL,
  ServiceURL,
  StorageURL,
  SharedKeyCredential,
  uploadStreamToBlockBlob
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };
const ONE_MINUTE = 60 * 1000;
const aborter = Aborter.timeout(30 * ONE_MINUTE);

const sharedKeyCredential = new SharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(sharedKeyCredential);
const serviceURL = new ServiceURL(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, ''); 
  return `${identifier}-${originalName}`;
};

router.post('/', uploadStrategy, async (req, res) => {

    const blobName = getBlobName(req.file.originalname);
    const stream = getStream(req.file.buffer);
    const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
    const blobURL = BlobURL.fromContainerURL(containerURL, blobName);
    const blockBlobURL = BlockBlobURL.fromBlobURL(blobURL);

    try {
      
      await uploadStreamToBlockBlob(aborter, stream,
        blockBlobURL, uploadOptions.bufferSize, uploadOptions.maxBuffers);

      res.render('success', { message: 'File uploaded to Azure Blob storage.' });   

    } catch (err) {

      res.render('error', { message: 'Something went wrong.' });

    }
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Ellenőrizze, hogy a kép megjelenik-e a tárfiókban

Jelentkezzen be az [Azure portálra](https://portal.azure.com). A bal oldali menüben válassza a **Tárfiókok** lehetőséget, majd válassza ki saját tárfiókja nevét. A **blob szolgáltatás**alatt válassza a **Blobok**, majd a **lemezképek** tároló elemet.

Ellenőrizze, hogy a kép megjelenik-e a tárolóban.

![Képek tárolójának Azure Portal-listája](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Miniatűr megtekintésének tesztelése

A miniatűr megtekintésének teszteléséhez fel kell töltenie egy rendszerképet a **miniatűrök** tárolóba annak ellenőrzéséhez, hogy az alkalmazás képes-e olvasni a **miniatűrök** tárolót.

Jelentkezzen be az [Azure portálra](https://portal.azure.com). A bal oldali menüben válassza a **Tárfiókok** lehetőséget, majd válassza ki saját tárfiókja nevét. A **blob szolgáltatás**alatt válassza a **Blobok**lehetőséget, majd válassza ki a **miniatűrök** tárolót. Válassza a **Feltöltés** lehetőséget a **Blob feltöltése** panel megnyitásához.

Válasszon ki egy fájlt a fájl választóval, és válassza a **feltöltés**lehetőséget.

Lépjen vissza az alkalmazásba és ellenőrizze, hogy a **thumbnails** tárolóba feltöltött kép látható-e.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![.NET-rendszerkép Resizer-alkalmazás új képpel megjelenítve](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node. js v2 SDK](#tab/nodejs)
![Node. js v2 rendszerkép-alkalmazás új képpel megjelenítve](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node. js v10 SDK](#tab/nodejsv10)
![Node. js v10 rendszerkép-alkalmazás új képpel megjelenítve](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

A sorozat második részében automatizálja a miniatűr képek létrehozását, így nincs szüksége erre a képre. Az Azure Portalon, a **thumbnails** tárolóban válassza ki a feltöltött képet, majd kattintson a **Törlés** elemre a kép törléséhez. 

Engedélyezheti a CDN-t az Azure Storage-fiók tartalmának gyorsítótárazásához. A CDN Azure Storage-fiókkal való engedélyezésével kapcsolatos további információkért lásd: [Azure Storage-fiók integrálása Azure CDNokkal](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Következő lépések

A sorozat első részében megtanulta, hogyan konfigurálhat egy webalkalmazást a tárterülettel való interakcióhoz.

A sorozat második részében megismerheti, hogyan lehet a Event Grid használatával elindítani egy Azure-függvényt egy rendszerkép átméretezéséhez.

> [!div class="nextstepaction"]
> [Egy Azure-függvény Event Grid használatával történő aktiválása egy feltöltött kép átméretezéséhez](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
