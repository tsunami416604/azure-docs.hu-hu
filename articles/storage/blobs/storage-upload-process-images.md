---
title: Képadatok feltöltése a felhőbe az Azure Storage segítségével | Microsoft Docs
description: Azure Blob storage használata egy olyan webalkalmazással az alkalmazásadatok tárolására szolgáló
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 01404c89665ebfea62e7bda0e7566289bb15f2ae
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300958"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Oktatóanyag: Képadatok feltöltése a felhőbe az Azure Storage segítségével

Ez az oktatóanyag egy sorozat első része. Ebben az oktatóanyagban megtudhatja, hogyan helyezhet üzembe egy webalkalmazást, amely az Azure Storage ügyféloldali kódtárat használja tölthet fel képeket a storage-fiók. Ha elkészült, rendelkezni fog a egy webalkalmazást, amely tárolja, és megjeleníti a rendszerképeket az Azure storage-ból.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![Képek tároló nézete](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
![Képek tároló nézete](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
![Képek tároló nézete](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Tárfiók létrehozása
> * Tároló létrehozása és az engedélyek beállítása
> * Hozzáférési kulcs lekérése
> * Webalkalmazás üzembe helyezése az Azure-bA
> * Alkalmazásbeállítások konfigurálása
> * A webalkalmazás kezelése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre. Hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Helyi telepítése és használata a parancssori felület, ebben az oktatóanyagban, hogy, futtatnia kell az Azure CLI 2.0.4-es vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot 

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.  

A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia 
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A minta képeket tölt fel egy Azure Storage-fiókban lévő blobtárolóba. A tárfiók egy egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és hozzáféréséhez. Az [az storage account create](/cli/azure/storage/account) paranccsal hozzon létre egy tárfiókot a létrehozott erőforráscsoportban.

> [!IMPORTANT]
> Az oktatóanyag 2 része az Azure Event Grid használhatja a Blob storage használatával. Ellenőrizze, hogy a tárfiók létrehozása az Azure-régióban, amely támogatja az Event Griddel. Támogatott régiók listáját lásd: [az Azure-termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

A következő parancsban cserélje le a saját globálisan egyedi tárfióknevét Blob storage-fiók nevét a `<blob_storage_account>` helyőrző.  

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

az storage account create --name $blobStorageAccount \
--location southeastasia --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>A Blob storage-tárolók létrehozása

Az alkalmazás két tárolót használ a Blob Storage-fiókban. A tárolók hasonlók a mappákhoz, és a blobok tárolására. A *képek* tároló az a hely, ahová az alkalmazás feltölti a teljes felbontású képeket. A sorozat egy későbbi részében egy Azure-függvényalkalmazás tölt fel átméretezett képminiatűröket a *thumbnails* tárolóba.

Szerezze be a tárfiókkulcsot az [az storage account keys list](/cli/azure/storage/account/keys) parancs segítségével. Ezután használja ezt a kulcsot a két tárolót hozhat létre a [az a tároló létrehozása](/cli/azure/storage/container) parancsot.  

A *lemezképek* tároló nyilvános hozzáférése erre van beállítva `off`. A *miniatűrök* tároló nyilvános hozzáférése erre van beállítva `container`. A `container` nyilvános hozzáférési beállítás lehetővé teszi a felhasználók, akik a weblapon, a miniatűrök megtekintéséhez.

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

Jegyezze fel a Blob storage-fiók neve és kulcsa. A mintaalkalmazás csatlakozhat a tárfiókhoz a képek alkalmazza ezeket a beállításokat. 

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Az alkalmazást tároló webkiszolgálófarm helyét, méretét és funkcióit egy [App Service-csomag](../../app-service/overview-hosting-plans.md) határozza meg.

Hozzon létre egy App Service-csomagot az [az appservice plan create](/cli/azure/appservice/plan) paranccsal.

Az alábbi példa egy `myAppServicePlan` nevű App Service-csomag létrehozását mutatja be az **INGYENES** tarifacsomagban:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A webalkalmazás üzemeltetési tárterületet biztosít a GitHub-mintaadattárból az üzembe helyezett app mintakódot. Az [ az webapp create](/cli/azure/webapp) paranccsal hozzon létre egy [webalkalmazást](../../app-service/overview.md) a `myAppServicePlan` App Service-csomagban.  

A következő parancsban cserélje le a `<web_app>` , egyedi névvel. Érvényes karakterek: `a-z`, `0-9` és `-`. Ha `<web_app>` van a hibaüzenet kap nem egyedi: _Ezzel a névvel webhely `<web_app>` már létezik._ A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp=<web_app>

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Mintaalkalmazás üzembe helyezése a GitHub-adattárból

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével.

A mintaprojekt tartalmaz egy [ASP.NET MVC](https://www.asp.net/mvc) alkalmazást. Az alkalmazás fogad, a storage-tárfiókba menti és képeket miniatűrtárolóból jeleníti meg. A webes alkalmazás használja a [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet), és a [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) névtereket az Azure storage ügyféloldali kódtár használatával kommunikálhat az Azure storage.

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével. 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével. 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10
```

---

## <a name="configure-web-app-settings"></a>A webalkalmazás beállításainak konfigurálása

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

A minta-webalkalmazás az [Azure Storage ügyféloldali kódtár](/dotnet/api/overview/azure/storage?view=azure-dotnet) segítségével kér a képfeltöltéshez használt hozzáférési jogkivonatokat. A tárfiók hitelesítő adatainak a Storage SDK-t használja a web app alkalmazás beállításai vannak beállítva. Alkalmazásbeállítások hozzáadása a telepített alkalmazás lecserélhető a [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) parancsot.

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=$blobStorageAccountKey  
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

A minta-webalkalmazás az [Azure Storage ügyféloldali kódtár](https://docs.microsoft.com/javascript/api/azure-storage) segítségével kér a képfeltöltéshez használt hozzáférési jogkivonatokat. A tárfiók hitelesítő adatainak a Storage SDK-t használja a web app alkalmazás beállításai vannak beállítva. Alkalmazásbeállítások hozzáadása a telepített alkalmazás lecserélhető a [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) parancsot.

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

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

A minta-webalkalmazás az [Azure Storage ügyféloldali kódtár](https://github.com/Azure/azure-storage-js) segítségével kér a képfeltöltéshez használt hozzáférési jogkivonatokat. A tárfiók hitelesítő adatainak a Storage SDK-t használja a web app alkalmazás beállításai vannak beállítva. Alkalmazásbeállítások hozzáadása a telepített alkalmazás lecserélhető a [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) parancsot.

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Miután telepíti és konfigurálja a web app, tesztelheti a képek feltöltésének működését az alkalmazásban.

## <a name="upload-an-image"></a>Rendszerkép feltöltése

A webalkalmazás teszteléséhez nyissa meg a közzétett alkalmazás URL-címét. A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Válassza ki a **fényképek feltöltése** régiót válassza ki, és töltsön fel egy fájlt, vagy egy fájl húzza a régió. Sikeres feltöltés esetén a kép eltűnik. A **létrehozott miniatűrök** szakasz üres marad, amíg ez a témakör későbbi részében teszteljük.

![ImageResizer alkalmazás](media/storage-upload-process-images/figure1.png)

A mintakód a `UploadFiletoStorage` a feladat a *Storagehelper.cs* fájllal a képek a *lemezképek* tárolón belül a tárfiók tárfiókkulcsait a [ UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) metódust. A következő mintakód tartalmazza a `UploadFiletoStorage` műveletet.

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
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

Válassza ki **fájl kiválasztása** jelöljön ki egy fájlt, majd kattintson a **kép feltöltése**. A **létrehozott miniatűrök** szakasz üres marad, amíg ez a témakör későbbi részében teszteljük. 

![Képfeltöltő alkalmazás](media/storage-upload-process-images/upload-app-nodejs.png)

A mintakódban a `post` útvonal felelős a képek blobtárolóba való feltöltéséért. Az útvonal modulok használatával segíti a feltöltés feldolgozását:

- [multer](https://github.com/expressjs/multer) az útvonal-kezelő a feltöltés stratégiát valósít meg.
- [az adatfolyam](https://github.com/sindresorhus/into-stream) alakítja át a puffer [createBlockBlobFromStream] által kért stream. ()http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html)

A fájlt küld az útvonalat, mivel a fájl tartalmát a memóriában marad, amíg nem a feltölti a blobtárolóba.

> [!IMPORTANT]
> Nagy fájlok betöltése a memóriába, előfordulhat, hogy a webalkalmazás teljesítményére negatív hatással. Ha azt felhasználók közzététele a nagy méretű fájlok, érdemes, vegye figyelembe a web server fájlrendszer a fájlok átmeneti, és ezután ütemezés feltöltése a Blob storage-bA. Miután a fájlok Blob storage-ban, a kiszolgáló fájlrendszerből eltávolíthatja őket.

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

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

Válassza ki **fájl kiválasztása** jelöljön ki egy fájlt, majd kattintson a **kép feltöltése**. A **létrehozott miniatűrök** szakasz üres marad, amíg ez a témakör későbbi részében teszteljük. 

![Képfeltöltő alkalmazás](media/storage-upload-process-images/upload-app-nodejs.png)

A mintakódban a `post` útvonal felelős a képek blobtárolóba való feltöltéséért. Az útvonal modulok használatával segíti a feltöltés feldolgozását:

- [multer](https://github.com/expressjs/multer) az útvonal-kezelő a feltöltés stratégiát valósít meg.
- [az adatfolyam](https://github.com/sindresorhus/into-stream) a puffer alakít át egy stream által kért [createBlockBlobFromStream](http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html).

A fájlt küld az útvonalat, mivel a fájl tartalmát a memóriában marad, amíg nem a feltölti a blobtárolóba.

> [!IMPORTANT]
> Nagy fájlok betöltése a memóriába, előfordulhat, hogy a webalkalmazás teljesítményére negatív hatással. Ha azt felhasználók közzététele a nagy méretű fájlok, érdemes, vegye figyelembe a web server fájlrendszer a fájlok átmeneti, és ezután ütemezés feltöltése a Blob storage-bA. Miután a fájlok Blob storage-ban, a kiszolgáló fájlrendszerből eltávolíthatja őket.

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

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A bal oldali menüben válassza a **Tárfiókok** lehetőséget, majd válassza ki saját tárfiókja nevét. Alatt **Blob Service**válassza **Blobok**, majd válassza ki a **lemezképek** tároló.

Ellenőrizze, hogy a kép megjelenik-e a tárolóban.

![Képek tároló nézete](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Miniatűr megtekintésének tesztelése

A miniatűrök megtekintésének teszteléséhez, feltölthet egy képet, a **miniatűrök** tárolót, hogy ellenőrizze, hogy az alkalmazás tudja olvasni a **miniatűrök** tároló.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A bal oldali menüben válassza a **Tárfiókok** lehetőséget, majd válassza ki saját tárfiókja nevét. A **Blob Service**válassza **Blobok**, majd válassza a **miniatűrök** tároló. Válassza a **Feltöltés** lehetőséget a **Blob feltöltése** panel megnyitásához.

A Fájlkereső a válasszon ki egy fájlt, és válassza ki **feltöltése**.

Lépjen vissza az alkalmazásba és ellenőrizze, hogy a **thumbnails** tárolóba feltöltött kép látható-e.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![Képek tároló nézete](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
![Képek tároló nézete](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
![Képek tároló nézete](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

A sorozat második részében, automatizálhatja a miniatűr kép létrehozása így nem szükséges ezt a képet. Az Azure Portalon, a **thumbnails** tárolóban válassza ki a feltöltött képet, majd kattintson a **Törlés** elemre a kép törléséhez. 

A tartalmak gyorsítótárazására CDN engedélyezheti az Azure storage-fiókból. CDN engedélyezése az Azure storage-fiók kapcsolatos további információkért lásd: [az Azure storage-fiók integrálása az Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>További lépések

A sorozat első részében, az útmutatóból megtudhatta, hogyan kommunikál a storage-WebApp konfigurálásához.

Lépjen be a két sorozat része további információ az Event Grid használatával aktiválhat egy Azure-függvényt egy kép átméretezéséhez.

> [!div class="nextstepaction"]
> [Egy Azure-függvény Event Grid használatával történő aktiválása egy feltöltött kép átméretezéséhez](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
