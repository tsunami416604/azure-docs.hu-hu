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
ms.openlocfilehash: 882a43a75fa720b13d931740818e5ee6e893bcab
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753336"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Oktatóanyag: Képadatok feltöltése a felhőbe az Azure Storage segítségével

Ez az oktatóanyag egy sorozat első része. Ebben az oktatóanyagban megtudhatja, hogyan helyezhet üzembe egy webalkalmazást, amely az Azure Storage ügyféloldali kódtárat használja tölthet fel képeket a storage-fiók. Ha elkészült, rendelkezni fog a egy webalkalmazást, amely tárolja, és megjeleníti a rendszerképeket az Azure storage-ból.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![Képek tároló nézete](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
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

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.  

A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia 
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A minta képeket tölt fel egy Azure Storage-fiókban lévő blobtárolóba. A tárfiók egy egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és hozzáféréséhez. Az [az storage account create](/cli/azure/storage/account#az_storage_account_create) paranccsal hozzon létre egy tárfiókot a létrehozott erőforráscsoportban.

> [!IMPORTANT]
> Az oktatóanyag 2 része az Azure Event Grid használhatja a Blob storage használatával. Ellenőrizze, hogy a tárfiók létrehozása az Azure-régióban, amely támogatja az Event Griddel. Támogatott régiók listáját lásd: [az Azure-termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

A következő parancsban cserélje le a saját globálisan egyedi tárfióknevét Blob storage-fiók nevét a `<blob_storage_account>` helyőrző.  

```azurecli-interactive
az storage account create --name <blob_storage_account> \
--location southeastasia --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>A Blob storage-tárolók létrehozása

Az alkalmazás két tárolót használ a Blob Storage-fiókban. A tárolók hasonlók a mappákhoz, és a blobok tárolására. A *képek* tároló az a hely, ahová az alkalmazás feltölti a teljes felbontású képeket. A sorozat egy későbbi részében egy Azure-függvényalkalmazás tölt fel átméretezett képminiatűröket a *thumbnails* tárolóba.

Szerezze be a tárfiókkulcsot az [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) parancs segítségével. Ezután használja ezt a kulcsot a két tárolót hozhat létre a [az a tároló létrehozása](/cli/azure/storage/container#az_storage_container_create) parancsot.  

Ebben az esetben `<blob_storage_account>` a létrehozott Blob Storage-fiók neve. A *lemezképek* tároló nyilvános hozzáférése erre van beállítva `off`. A *miniatűrök* tároló nyilvános hozzáférése erre van beállítva `container`. A `container` nyilvános hozzáférési beállítás lehetővé teszi a felhasználók, akik a weblapon, a miniatűrök megtekintéséhez.

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

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

Hozzon létre egy App Service-csomagot az [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) paranccsal.

Az alábbi példa egy `myAppServicePlan` nevű App Service-csomag létrehozását mutatja be az **INGYENES** tarifacsomagban:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A webalkalmazás üzemeltetési tárterületet biztosít a GitHub-mintaadattárból az üzembe helyezett app mintakódot. Az [ az webapp create](/cli/azure/webapp#az_webapp_create) paranccsal hozzon létre egy [webalkalmazást](../../app-service/overview.md) a `myAppServicePlan` App Service-csomagban.  

A következő parancsban cserélje le a `<web_app>` , egyedi névvel. Érvényes karakterek: `a-z`, `0-9` és `-`. Ha `<web_app>` van a hibaüzenet kap nem egyedi: _Ezzel a névvel webhely `<web_app>` már létezik._ A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Mintaalkalmazás üzembe helyezése a GitHub-adattárból

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) parancs segítségével. Cserélje le a `<web_app>` elemet az előző lépésben létrehozott webalkalmazás nevére.

A mintaprojekt tartalmaz egy [ASP.NET MVC](https://www.asp.net/mvc) alkalmazást. Az alkalmazás fogad, a storage-tárfiókba menti és képeket miniatűrtárolóból jeleníti meg. A webes alkalmazás használja a [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet), és a [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) névtereket az Azure storage ügyféloldali kódtár használatával kommunikálhat az Azure storage.

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) parancs segítségével. Cserélje le a `<web_app>` elemet az előző lépésben létrehozott webalkalmazás nevére.

---

```azurecli-interactive
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

## <a name="configure-web-app-settings"></a>A webalkalmazás beállításainak konfigurálása

A minta-webalkalmazás az [Azure Storage ügyféloldali kódtár](/dotnet/api/overview/azure/storage?view=azure-dotnet) segítségével kér a képfeltöltéshez használt hozzáférési jogkivonatokat. A tárfiók hitelesítő adatainak a Storage SDK-t használja a web app alkalmazás beállításai vannak beállítva. Alkalmazásbeállítások hozzáadása a telepített alkalmazás lecserélhető a [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) parancsot.

A következő parancsban cserélje le a `<blob_storage_account>` a Blob storage-fiók nevére és `<blob_storage_key>` és a hozzá tartozó kulcsot. Cserélje le a `<web_app>` elemet az előző lépésben létrehozott webalkalmazás nevére.

```azurecli-interactive
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
```

Miután telepíti és konfigurálja a web app, tesztelheti a képek feltöltésének működését az alkalmazásban.

## <a name="upload-an-image"></a>Rendszerkép feltöltése

A webalkalmazás teszteléséhez nyissa meg a közzétett alkalmazás URL-címét. A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.
Válassza ki a **fényképek feltöltése** régiót válassza ki, és töltsön fel egy fájlt, vagy egy fájl húzza a régió. Sikeres feltöltés esetén a kép eltűnik.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

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
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

![Képfeltöltő alkalmazás](media/storage-upload-process-images/upload-app-nodejs.png)

A mintakódban a `post` útvonal felelős a képek blobtárolóba való feltöltéséért. Az útvonal modulok használatával segíti a feltöltés feldolgozását:

- [multer](https://github.com/expressjs/multer) az útvonal-kezelő a feltöltés stratégiát valósít meg.
- [az adatfolyam](https://github.com/sindresorhus/into-stream) alakítja át a puffer [createBlockBlobFromStream] által kért stream. ()http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html#createBlockBlobFromStream)

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

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Képek tároló nézete](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

A sorozat második részében, automatizálhatja a miniatűr kép létrehozása így nem szükséges ezt a képet. Az Azure Portalon, a **thumbnails** tárolóban válassza ki a feltöltött képet, majd kattintson a **Törlés** elemre a kép törléséhez. 

A tartalmak gyorsítótárazására CDN engedélyezheti az Azure storage-fiókból. CDN engedélyezése az Azure storage-fiók kapcsolatos további információkért lásd: [az Azure storage-fiók integrálása az Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>További lépések

A sorozat első részében, az útmutatóból megtudhatta, hogyan kommunikál a storage-WebApp konfigurálásához.

Lépjen be a két sorozat része további információ az Event Grid használatával aktiválhat egy Azure-függvényt egy kép átméretezéséhez.

> [!div class="nextstepaction"]
> [Egy Azure-függvény Event Grid használatával történő aktiválása egy feltöltött kép átméretezéséhez](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
