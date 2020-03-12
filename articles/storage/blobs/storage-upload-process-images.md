---
title: Képadatok feltöltése a felhőbe az Azure Storage segítségével | Microsoft Docs
description: Azure Blob storage használata egy olyan webalkalmazással az alkalmazásadatok tárolására szolgáló
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.openlocfilehash: 49078d2f374203a9fab4fe0f5e3881f6b1b22959
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130329"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Oktatóanyag: A felhőben az Azure Storage képadatok feltöltése

Ez az oktatóanyag egy sorozat első része. Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy olyan webalkalmazást, amely az Azure Blob Storage ügyféloldali kódtárat használja a lemezképek Storage-fiókba való feltöltéséhez. Ha elkészült, rendelkezni fog a egy webalkalmazást, amely tárolja, és megjeleníti a rendszerképeket az Azure storage-ból.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)
![Rendszerkép-alkalmazás a .NET-ben](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[Node. js v10 SDK](#tab/nodejsv10)
![Képresizer alkalmazás Node. js v10-ben](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

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

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre. Mielőtt elkezdené, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Helyi telepítése és használata a parancssori felület, ebben az oktatóanyagban, hogy, futtatnia kell az Azure CLI 2.0.4-es vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.  

A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A minta feltölti a képeket egy Azure Storage-fiókban található blob-tárolóba. A tárfiók egy egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és hozzáféréséhez. Az [az storage account create](/cli/azure/storage/account) paranccsal hozzon létre egy tárfiókot a létrehozott erőforráscsoportban.

> [!IMPORTANT]
> Az oktatóanyag 2 része az Azure Event Grid használhatja a Blob storage használatával. Ellenőrizze, hogy a tárfiók létrehozása az Azure-régióban, amely támogatja az Event Griddel. A támogatott régiók listáját lásd: Azure- [termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

A következő parancsban cserélje le a saját globálisan egyedi nevét arra a blob Storage-fiókra, amelyben a `<blob_storage_account>` helyőrző látható.

```azurecli-interactive
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>A Blob storage-tárolók létrehozása

Az alkalmazás két tárolót használ a Blob Storage-fiókban. A tárolók hasonlók a mappákhoz, és a blobok tárolására. A *képek* tároló az a hely, ahová az alkalmazás feltölti a teljes felbontású képeket. A sorozat egy későbbi részében egy Azure-függvényalkalmazás tölt fel átméretezett képminiatűröket a *thumbnails* tárolóba.

Szerezze be a tárfiókkulcsot az [az storage account keys list](/cli/azure/storage/account/keys) parancs segítségével. Ezután ezt a kulcsot használva hozzon létre két tárolót az az [Storage Container Create](/cli/azure/storage/container) paranccsal.

A *rendszerkép* -tároló nyilvános hozzáférése `off`értékre van állítva. A *miniatűrök* tároló nyilvános hozzáférése `container`értékre van állítva. A `container` nyilvános hozzáférés beállítás lehetővé teszi, hogy a weblapon megtekintő felhasználók megtekinthessék a miniatűrt.

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

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

A webalkalmazás üzemeltetési tárterületet biztosít a GitHub-mintaadattárból az üzembe helyezett app mintakódot. Az [ az webapp create](../../app-service/overview.md) paranccsal hozzon létre egy `myAppServicePlan`webalkalmazást[ a ](/cli/azure/webapp) App Service-csomagban.  

A következő parancsban cserélje le a `<web_app>`t egyedi névre. Érvényes karakterek: `a-z`, `0-9` és `-`. Ha az `<web_app>` nem egyedi, a következő hibaüzenet jelenik meg: *A megadott `<web_app>` névvel már létezik webhely.* A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Mintaalkalmazás üzembe helyezése a GitHub-adattárból

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével.

A minta projekt egy [ASP.net MVC](https://www.asp.net/mvc) -alkalmazást tartalmaz. Az alkalmazás fogad, a storage-tárfiókba menti és képeket miniatűrtárolóból jeleníti meg. A webalkalmazás az [Azure. Storage](/dotnet/api/azure.storage), az [Azure. Storage. Blobs](/dotnet/api/azure.storage.blobs)és az [Azure. Storage. Blobs. models](/dotnet/api/azure.storage.blobs.models) névtereket használja az Azure Storage szolgáltatással való kommunikációhoz.

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v10-sdk"></a>[Node. js v10 SDK](#tab/nodejsv10)
Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével.

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node
```

---

## <a name="configure-web-app-settings"></a>A webalkalmazás beállításainak konfigurálása

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

A minta webalkalmazás a [.net-hez készült Azure Storage API-kat](/dotnet/api/overview/azure/storage) használja a Képek feltöltéséhez. A Storage-fiók hitelesítő adatai a webalkalmazás alkalmazás-beállításainál vannak megadva. Adja hozzá az alkalmazás beállításait az üzembe helyezett alkalmazáshoz az az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings) paranccsal.

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="nodejs-v10-sdk"></a>[Node. js v10 SDK](#tab/nodejsv10)

A minta-webalkalmazás az [Azure Storage ügyféloldali kódtár](https://github.com/Azure/azure-storage-js) segítségével kér a képfeltöltéshez használt hozzáférési jogkivonatokat. A tárfiók hitelesítő adatainak a Storage SDK-t használja a web app alkalmazás beállításai vannak beállítva. Adja hozzá az alkalmazás beállításait az üzembe helyezett alkalmazáshoz az az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings) paranccsal.

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Miután telepíti és konfigurálja a web app, tesztelheti a képek feltöltésének működését az alkalmazásban.

## <a name="upload-an-image"></a>Rendszerkép feltöltése

A webalkalmazás teszteléséhez nyissa meg a közzétett alkalmazás URL-címét. A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

Válassza a **fényképek feltöltése** régiót egy fájl megadásához és feltöltéséhez, vagy húzzon egy fájlt a régióra. Sikeres feltöltés esetén a kép eltűnik. A **létrehozott miniatűrök** szakasz üres marad, amíg a témakör későbbi szakaszában nem teszteljük.

![Fényképek feltöltése a .NET-ben](media/storage-upload-process-images/figure1.png)

A mintakód a *Storagehelper.cs* fájl `UploadFileToStorage` feladatát használja *a lemezképek tárolójába* való feltöltéséhez a Storage-fiókon belül a [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) metódus használatával. A következő mintakód tartalmazza a `UploadFileToStorage` műveletet.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

Az előző feladatban használt osztályok és módszerek a következők:

| Osztály    | Módszer   |
|----------|----------|
| [URI](/dotnet/api/system.uri) | [URI-konstruktor](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [StorageSharedKeyCredential (karakterlánc, karakterlánc) konstruktor](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="nodejs-v10-sdk"></a>[Node. js v10 SDK](#tab/nodejsv10)

Válassza a **fájl** kiválasztása lehetőséget egy fájl kiválasztásához, majd kattintson a **rendszerkép feltöltése**elemre. A **létrehozott miniatűrök** szakasz üres marad, amíg a témakör későbbi szakaszában nem teszteljük. 

![Fényképek feltöltése a Node. js v10-ben](media/storage-upload-process-images/upload-app-nodejs.png)

A mintakódban a `post` útvonal felelős a képek blobtárolóba való feltöltéséért. Az útvonal modulok használatával segíti a feltöltés feldolgozását:

- a [multer](https://github.com/expressjs/multer) megvalósítja a feltöltési stratégiát az útválasztási kezelőhöz.
- a [into-stream](https://github.com/sindresorhus/into-stream) a [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html)által előírt módon átalakítja a puffert egy adatfolyamba.

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

Jelentkezzen be az [Azure Portal](https://portal.azure.com). A bal oldali menüben válassza a **Tárfiókok** lehetőséget, majd válassza ki saját tárfiókja nevét. Válassza a **tárolók**, majd a **lemezképek** tároló elemet.

Ellenőrizze, hogy a kép megjelenik-e a tárolóban.

![Képek tárolójának Azure Portal-listája](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Miniatűr megtekintésének tesztelése

A miniatűr megtekintésének teszteléséhez fel kell töltenie egy rendszerképet a **miniatűrök** tárolóba annak ellenőrzéséhez, hogy az alkalmazás képes-e olvasni a **miniatűrök** tárolót.

Jelentkezzen be az [Azure Portal](https://portal.azure.com). A bal oldali menüben válassza a **Tárfiókok** lehetőséget, majd válassza ki saját tárfiókja nevét. Válassza a **tárolók**lehetőséget, majd válassza ki a **miniatűrök** tárolót. Válassza a **Feltöltés** lehetőséget a **Blob feltöltése** panel megnyitásához.

Válasszon ki egy fájlt a fájl választóval, és válassza a **feltöltés**lehetőséget.

Lépjen vissza az alkalmazásba és ellenőrizze, hogy a **thumbnails** tárolóba feltöltött kép látható-e.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)
![.NET-rendszerkép Resizer-alkalmazás új képpel megjelenítve](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[Node. js v10 SDK](#tab/nodejsv10)
![Node. js v10 rendszerkép-alkalmazás új képpel megjelenítve](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

A sorozat második részében, automatizálhatja a miniatűr kép létrehozása így nem szükséges ezt a képet. Az Azure Portalon, a **thumbnails** tárolóban válassza ki a feltöltött képet, majd kattintson a **Törlés** elemre a kép törléséhez. 

Engedélyezheti Content Delivery Network (CDN) számára a tartalom gyorsítótárazását az Azure Storage-fiókból. A CDN Azure Storage-fiókkal való engedélyezésével kapcsolatos további információkért lásd: [Azure Storage-fiók integrálása Azure CDNokkal](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Következő lépések

A sorozat első részében, az útmutatóból megtudhatta, hogyan kommunikál a storage-WebApp konfigurálásához.

Lépjen be a két sorozat része további információ az Event Grid használatával aktiválhat egy Azure-függvényt egy kép átméretezéséhez.

> [!div class="nextstepaction"]
> [Egy Azure-függvény Event Grid használatával történő aktiválása egy feltöltött kép átméretezéséhez](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
