---
title: Képadatok feltöltése a felhőbe az Azure Storage segítségével | Microsoft Docs
description: Az Azure Blob storage használata webalkalmazással az alkalmazásadatok tárolására
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.openlocfilehash: e2ee959fb6fc7e8454919c71cfa20e2bb9055dfb
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393849"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Oktatóanyag: Képadatok feltöltése a felhőben az Azure Storage segítségével

Ez az oktatóanyag egy sorozat első része. Ebben az oktatóanyagban megtudhatja, hogyan telepíthet egy webalkalmazást, amely az Azure Blob storage-ügyfélkódtár használatával képeket tölthet fel egy tárfiókba. Ha elkészült, lesz egy webalkalmazás, amely tárolja és megjeleníti az Azure storage-ból származó képeket.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)
![Képátsizer alkalmazás a .NET-ben](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)
![Képresizer alkalmazás a Node.js V10-ben](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Tárfiók létrehozása
> * Tároló létrehozása és az engedélyek beállítása
> * Hozzáférési kulcs lekérése
> * Webalkalmazás üzembe helyezése az Azure-ban
> * Alkalmazásbeállítások konfigurálása
> * A webalkalmazás sal való együttműködése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre. Hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A CLI helyi telepítéséhez és használatához ez az oktatóanyag megköveteli az Azure CLI 2.0.4-es vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.  

A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A minta képeket tölt fel egy Blob-tárolóba egy Azure-tárfiókban. A tárfiók egy egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és hozzáféréséhez. Az [az storage account create](/cli/azure/storage/account) paranccsal hozzon létre egy tárfiókot a létrehozott erőforráscsoportban.

> [!IMPORTANT]
> Az oktatóanyag 2. Győződjön meg arról, hogy hozzon létre a tárfiókot egy Azure-régióban, amely támogatja az Event Grid. A támogatott régiók listáját az [Azure-termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)című témakörben tájékán tájékykad.

A következő parancsban cserélje le a saját globálisan egyedi nevét `<blob_storage_account>` a Blob storage-fiók, ahol a helyőrző látható.

```azurecli-interactive
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>Blob-tárolók létrehozása

Az alkalmazás két tárolót használ a Blob Storage-fiókban. A tárolók hasonlóak a mappákhoz és a tárolóblobokhoz. A *képek* tároló az a hely, ahová az alkalmazás feltölti a teljes felbontású képeket. A sorozat egy későbbi részében egy Azure-függvényalkalmazás tölt fel átméretezett képminiatűröket a *thumbnails* tárolóba.

Szerezze be a tárfiókkulcsot az [az storage account keys list](/cli/azure/storage/account/keys) parancs segítségével. Ezután ezzel a kulccsal hozzon létre két tárolót az [az storage container create](/cli/azure/storage/container) paranccsal.

A *rendszerképek* tárolójának nyilvános `off`hozzáférése a. A *miniatűrök* tárolójának nyilvános `container`hozzáférése a. A `container` nyilvános hozzáférés beállítás lehetővé teszi a felhasználók számára, akik ellátogatnak a weboldalra, hogy megtekintsék a miniatűröket.

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

Jegyezze fel a Blob tárfiók nevét és kulcsát. A mintaalkalmazás ezeket a beállításokat használja a tárfiókhoz való csatlakozáshoz a képek feltöltéséhez. 

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Az alkalmazást tároló webkiszolgálófarm helyét, méretét és funkcióit egy [App Service-csomag](../../app-service/overview-hosting-plans.md) határozza meg.

Hozzon létre egy App Service-csomagot az [az appservice plan create](/cli/azure/appservice/plan) paranccsal.

Az alábbi példa egy `myAppServicePlan` nevű App Service-csomag létrehozását mutatja be az **INGYENES** tarifacsomagban:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A webalkalmazás tárhelyet biztosít a GitHub mintatárból üzembe helyezett mintaalkalmazás-kódszámára. Az [ az webapp create](/cli/azure/webapp) paranccsal hozzon létre egy [webalkalmazást](../../app-service/overview.md) a `myAppServicePlan` App Service-csomagban.  

A következő parancsban `<web_app>` cserélje le egy egyedi névre. Érvényes karakterek: `a-z`, `0-9` és `-`. Ha az `<web_app>` nem egyedi, a következő hibaüzenet jelenik meg: *A megadott `<web_app>` névvel már létezik webhely.* A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Mintaalkalmazás üzembe helyezése a GitHub-adattárból

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével.

A mintaprojekt egy [ASP.NET MVC](https://www.asp.net/mvc) alkalmazást tartalmaz. Az alkalmazás elfogadja a képet, menti azt egy tárfiókba, és képeket jelenít meg egy miniatűr tárolóból. A webalkalmazás az [Azure.Storage](/dotnet/api/azure.storage), [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs)és [Az Azure.Storage.Blobs.Models](/dotnet/api/azure.storage.blobs.models) névtereket használja az Azure Storage szolgáltatással való interakcióhoz.

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)
Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével.

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10
```

---

## <a name="configure-web-app-settings"></a>A webalkalmazás beállításainak konfigurálása

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

A minta webalkalmazás a [.NET Azure Storage API-kat](/dotnet/api/overview/azure/storage) használja a képek feltöltéséhez. A tárfiók hitelesítő adatai a webalkalmazás alkalmazásbeállításaiban vannak beállítva. Adja hozzá az alkalmazásbeállításokat az üzembe helyezett alkalmazáshoz az [az webapp-beállításkészlet](/cli/azure/webapp/config/appsettings) paranccsal.

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

A minta-webalkalmazás az [Azure Storage ügyféloldali kódtár](https://github.com/Azure/azure-storage-js) segítségével kér a képfeltöltéshez használt hozzáférési jogkivonatokat. A storage SDK által használt tárfiók hitelesítő adatai a webalkalmazás alkalmazásbeállításaiban vannak beállítva. Adja hozzá az alkalmazásbeállításokat az üzembe helyezett alkalmazáshoz az [az webapp-beállításkészlet](/cli/azure/webapp/config/appsettings) paranccsal.

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

A webalkalmazás telepítése és konfigurálása után tesztelheti a képfeltöltési funkciót az alkalmazásban.

## <a name="upload-an-image"></a>Rendszerkép feltöltése

A webalkalmazás teszteléséhez nyissa meg a közzétett alkalmazás URL-címét. A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Válassza ki a **Fényképek feltöltése** régiót egy fájl megadásához és feltöltéséhez, vagy húzzon egy fájlt a régióra. Sikeres feltöltés esetén a kép eltűnik. A **Létrehozott miniatűrök** szakasz üres marad, amíg a témakör későbbi részében nem teszteljük.

![Fotók feltöltése a .NET fájlban](media/storage-upload-process-images/figure1.png)

A mintakódban `UploadFileToStorage` a *Storagehelper.cs* fájlban lévő feladat a rendszer a rendszerképek feltöltésére szolgál a tárfiókban lévő *lemezképek* tárolójába az [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) metódus használatával. A következő mintakód tartalmazza a `UploadFileToStorage` műveletet.

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
| [Uri](/dotnet/api/system.uri) | [Uri konstruktor](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [StorageSharedKeyCredential(Karakterlánc, karakterlánc) konstruktor](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

Fájl kijelöléséhez válassza a **Fájl kiválasztása** lehetőséget, majd kattintson a **Kép feltöltése gombra.** A **Létrehozott miniatűrök** szakasz üres marad, amíg a témakör későbbi részében nem teszteljük. 

![Fotók feltöltése a Node.js V10](media/storage-upload-process-images/upload-app-nodejs.png)

A mintakódban a `post` útvonal felelős a képek blobtárolóba való feltöltéséért. Az útvonal modulok használatával segíti a feltöltés feldolgozását:

- [a multer](https://github.com/expressjs/multer) megvalósítja az útvonalkezelő feltöltési stratégiáját.
- [az adatfolyamon át](https://github.com/sindresorhus/into-stream) alakítja a puffert streamelésre a [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html)által megkövetelt korban.

A fájl elküldése az útvonalra, a fájl tartalma a memóriában marad, amíg a fájl feltöltése a blob tárolóba.

> [!IMPORTANT]
> A nagy méretű fájlok memóriába töltése negatív hatással lehet a webalkalmazás teljesítményére. Ha azt várja a felhasználóktól, hogy nagy fájlokat tegyenek közzé, érdemes lehet megfontolni a fájlok nak a webkiszolgáló fájlrendszerén való előkészítését, majd a feltöltések ütemezését a Blob storage-ba. Miután a fájlok a Blob storage,eltávolíthatja őket a kiszolgáló fájlrendszerből.

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

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A bal oldali menüben válassza a **Tárfiókok** lehetőséget, majd válassza ki saját tárfiókja nevét. Válassza **a Tárolók**lehetőséget, majd jelölje ki a **lemezképek** tárolóját.

Ellenőrizze, hogy a kép megjelenik-e a tárolóban.

![Az Azure Portal rendszerképek-tárolójának listája](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Miniatűr megtekintésének tesztelése

A miniatűrök megtekintésének teszteléséhez töltsön fel egy képet a **miniatűrök** tárolóba, hogy ellenőrizze, hogy az alkalmazás képes-e olvasni a **miniatűrök** tárolóját.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A bal oldali menüben válassza a **Tárfiókok** lehetőséget, majd válassza ki saját tárfiókja nevét. Válassza **a Tárolók**lehetőséget, majd jelölje ki a **bélyegképek tárolóját.** Válassza a **Feltöltés** lehetőséget a **Blob feltöltése** panel megnyitásához.

Válasszon ki egy fájlt a fájlválasztóval, és válassza a **Feltöltés**lehetőséget.

Lépjen vissza az alkalmazásba és ellenőrizze, hogy a **thumbnails** tárolóba feltöltött kép látható-e.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)
![.NET image resizer alkalmazás új képpel](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)
![Node.js V10 image resizer alkalmazás új kép jelenik meg](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

A sorozat második részében automatizálhatja a miniatűr képek létrehozását, így nincs szüksége erre a képre. Az Azure Portalon, a **thumbnails** tárolóban válassza ki a feltöltött képet, majd kattintson a **Törlés** elemre a kép törléséhez. 

Engedélyezheti, hogy a Content Delivery Network (CDN) gyorsítótárazhassa a tartalmat az Azure-tárfiókból. A CDN Azure-tárfiókkal való engedélyezéséről az [Azure-tárfiók integrálása az Azure CDN-nel](../../cdn/cdn-create-a-storage-account-with-cdn.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

A sorozat első részében megtanulta, hogyan konfigurálhat egy webalkalmazást a tárterülettel való interakcióra.

Folytassa a sorozat második részével, és ismerje meg, hogy az Event Grid használatával aktiválhat-e egy Azure-függvényt egy lemezkép átméretezéséhez.

> [!div class="nextstepaction"]
> [Egy Azure-függvény Event Grid használatával történő aktiválása egy feltöltött kép átméretezéséhez](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
