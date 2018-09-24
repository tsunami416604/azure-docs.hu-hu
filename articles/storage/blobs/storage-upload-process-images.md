---
title: Képadatok feltöltése a felhőbe az Azure Storage segítségével | Microsoft Docs
description: Az Azure blobtároló használata webalkalmazással az alkalmazásadatok tárolásához
services: storage
documentationcenter: ''
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: aefc9ae15918a1269614fed41d76d75396684e64
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987288"
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>Képadatok feltöltése a felhőbe az Azure Storage segítségével

Ez az oktatóanyag egy sorozat első része. Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe egy webalkalmazást, amely az Azure Storage ügyféloldali kódtárat használja képek feltöltéséhez egy tárfiókba. Ha elkészült, rendelkezni fog egy webalkalmazással, amely képeket tárol az Azure Storage tárterületen, és megjeleníti onnan őket.

# <a name="nettabnet"></a>[\.NET](#tab/net)
![Képek tároló nézete](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Képek tároló nézete](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Tárfiók létrehozása
> * Tároló létrehozása és az engedélyek beállítása
> * Hozzáférési kulcs lekérése
> * Alkalmazásbeállítások konfigurálása
> * Webalkalmazás üzembe helyezése az Azure-ban
> * A webalkalmazás használata

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot 

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.
 
A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot.
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
 
A minta képeket tölt fel egy Azure Storage-fiókban lévő blobtárolóba. A tárfiók egy egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és hozzáféréséhez. Az [az storage account create](/cli/azure/storage/account#az_storage_account_create) paranccsal hozzon létre egy tárfiókot a létrehozott erőforráscsoportban. 

> [!IMPORTANT] 
> Az oktatóanyag második része a blobtárolókhoz tartozó esemény-előfizetések használatát mutatja be. Esemény-előfizetések csak az alábbi régiókban található Blob Storage-fiókokhoz támogatottak: Délkelet-Ázsia, Kelet-Ázsia, Kelet-Ausztrália, Délkelet-Ausztrália, USA középső régiója, USA keleti régiója, USA 2. keleti régiója, Nyugat-Európa, Észak-Európa, Kelet-Japán, Nyugat-Japán, USA nyugati középső régiója, USA nyugati régiója, USA 2. nyugati régiója. A korlátozás miatt a képek és miniatűrök tárolásához egy, a mintaalkalmazás által használt Blob Storage-fiókot kell létrehoznia.   

A következő parancsban a `<blob_storage_account>` helyőrző helyett írja be a saját globálisan egyedi Blob Storage-fióknevét.  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>Blob Storage-tárolók létrehozása

Az alkalmazás két tárolót használ a Blob Storage-fiókban. A tárolók hasonlók a mappákhoz, és a blobok tárolására szolgálnak. A _képek_ tároló az a hely, ahová az alkalmazás feltölti a teljes felbontású képeket. A sorozat egy későbbi részében egy Azure-függvényalkalmazás tölt fel átméretezett képminiatűröket a _thumbnails_ tárolóba. 

Szerezze be a tárfiókkulcsot az [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) parancs segítségével. Ezután a kulcs használatával két tárolót hozhat létre az [az storage container create](/cli/azure/storage/container#az_storage_container_create) paranccsal.  
 
Ebben az esetben `<blob_storage_account>` a létrehozott Blob Storage-fiók neve. Az _images_ tároló nyilvános hozzáférése `off` értékű, a _thumbnails_ tároló nyilvános hozzáférése pedig `container` értékű. A `container` nyilvános hozzáférési beállítás lehetővé teszi, hogy a miniatűrök megtekinthetők legyenek a webhely látogatói számára.
 
```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

Jegyezze fel a Blob Storage-fiók nevét és kulcsát. A mintaalkalmazás ezekkel a beállításokkal csatlakozik a tárfiókhoz a képek feltöltéséhez. 

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása 

Az alkalmazást tároló webkiszolgálófarm helyét, méretét és funkcióit egy [App Service-csomag](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) határozza meg. 

Hozzon létre egy App Service-csomagot az [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) paranccsal. 

Az alábbi példa egy `myAppServicePlan` nevű App Service-csomag létrehozását mutatja be az **INGYENES** tarifacsomagban: 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása 

A webalkalmazás üzemeltetési tárterületet biztosít a mintaalkalmazás GitHub-mintaadattárból települő kódjához. Az [ az webapp create](/cli/azure/webapp#az_webapp_create) paranccsal hozzon létre egy [webalkalmazást](../../app-service/app-service-web-overview.md) a `myAppServicePlan` App Service-csomagban.  
 
Az alábbi parancsban cserélje ki az `<web_app>` elemet egy egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`). Ha az `<web_app>` nem egyedi, a következő hibaüzenet jelenik meg: _A megadott `<web_app>` névvel már létezik webhely._ A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Mintaalkalmazás üzembe helyezése a GitHub-adattárból

# <a name="nettabnet"></a>[\.NET](#tab/net)

Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) parancs segítségével. Cserélje le a `<web_app>` elemet az előző lépésben létrehozott webalkalmazás nevére.

A mintaprojekt tartalmaz egy [ASP.NET MVC](https://www.asp.net/mvc) alkalmazást, amely képeket fogad, ment a tárfiókba, illetve jelenít meg a miniatűrtárolóból. A webalkalmazás a [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) és a [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) névtereket használja az Azure Storage ügyféloldali kódtárból az Azure Storage-tárterülettel való kommunikációhoz. 

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) parancs segítségével. Cserélje le a `<web_app>` elemet az előző lépésben létrehozott webalkalmazás nevére.

---

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>A webalkalmazás beállításainak konfigurálása 

A minta-webalkalmazás az [Azure Storage ügyféloldali kódtár](/dotnet/api/overview/azure/storage?view=azure-dotnet) segítségével kér a képfeltöltéshez használt hozzáférési jogkivonatokat. A Storage SDK által használt tárfiókhitelesítő-adatok a webalkalmazás alkalmazásbeállításaiban állíthatók be. Adjon hozzá alkalmazásbeállításokat az üzembe helyezett alkalmazáshoz az [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) parancs segítségével. 

Az alábbi parancsban a Blob Storage-fiók neve `<blob_storage_account>`, a hozzá tartozó kulcs pedig `<blob_storage_key>`. Cserélje le a `<web_app>` elemet az előző lépésben létrehozott webalkalmazás nevére.     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

A webalkalmazás üzembe helyezése és konfigurálása után tesztelheti a képek feltöltésének működését az alkalmazásban.   

## <a name="upload-an-image"></a>Rendszerkép feltöltése 

A webalkalmazás teszteléséhez nyissa meg a közzétett alkalmazás URL-címét. A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`. Válassza a **Fényképek feltöltése** területet egy fájl kiválasztásához és feltöltéséhez, vagy húzza a fájlt a régióra. Sikeres feltöltés esetén a kép eltűnik.

# <a name="nettabnet"></a>[\.NET](#tab/net)

![ImageResizer alkalmazás](media/storage-upload-process-images/figure1.png)

A mintakódban a képek felöltése a tárfiókban lévő `images` tárolóba a `Storagehelper.cs` fájlban található `UploadFiletoStorage` művelettel, az [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) módszer segítségével történik. A következő mintakód tartalmazza a `UploadFiletoStorage` műveletet. 

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

- a [multer](https://github.com/expressjs/multer) implementálja az útvonalkezelő feltöltési stratégiáját
- az [into-stream](https://github.com/sindresorhus/into-stream) streammé konvertálja a puffert a [createBlockBlobFromStream](http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html#createBlockBlobFromStream) utasításnak megfelelően

A fájl útvonalhoz való küldése során a fájl tartalma a memóriában marad, amíg a fájl fel nincs töltve a blobtárolóba.

> [!IMPORTANT]
> A nagyméretű fájlok betöltése a memóriába negatív hatással lehet a webalkalmazás teljesítményére. Ha a felhasználók várhatóan nagyméretű fájlokat tesznek közzé, érdemes lehet a fájlokat a webkiszolgáló fájlrendszerében előkészíteni, majd ezután ütemezni a blobtárolóba való feltöltést. Ha a fájlok a blobtárolóba kerültek, eltávolíthatja őket a kiszolgáló fájlrendszeréből.

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

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A bal oldali menüben válassza a **Tárfiókok** lehetőséget, majd válassza ki saját tárfiókja nevét. Az **Áttekintés** menüpontban válassza ki a **képek** tárolót.

Ellenőrizze, hogy a kép megjelenik-e a tárolóban.

![Képek tároló nézete](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Miniatűr megtekintésének tesztelése

A miniatűrök megtekintésének teszteléséhez feltölthet egy képet a miniatűrtárolóba, hogy meggyőződjön arról, olvassa-e az alkalmazás a miniatűrtárolót.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A bal oldali menüben válassza a **Tárfiókok** lehetőséget, majd válassza ki saját tárfiókja nevét. Válassza a **Tárolók** lehetőséget a **Blob szolgáltatás** menüpont alatt, majd válassza a **thumbnails** tárolót. Válassza a **Feltöltés** lehetőséget a **Blob feltöltése** panel megnyitásához.

A fájlkereső segítségével válasszon ki egy fájlt, és kattintson a **Feltöltés** elemre.

Lépjen vissza az alkalmazásba és ellenőrizze, hogy a **thumbnails** tárolóba feltöltött kép látható-e.

# <a name="nettabnet"></a>[\.NET](#tab/net)
![Képek tároló nézete](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Képek tároló nézete](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Az Azure Portalon, a **thumbnails** tárolóban válassza ki a feltöltött képet, majd kattintson a **Törlés** elemre a kép törléséhez. A sorozat második részében a miniatűrképek létrehozását automatizálhatja, ezért erre a tesztképre nincs szükség.

Engedélyezheti, hogy a CDN gyorsítótárazza az Azure Storage-fiók tartalmát. A CDN engedélyezése az Azure Storage-fiókhoz nem része ennek az oktatóanyagnak, de ha többet szeretne megtudni a folyamatról, látogasson el ide: [Azure Storage-fiók integrálása az Azure CDN-nel](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>További lépések

A sorozat első részében megismerkedett a tárterülettel kommunikáló webalkalmazás konfigurációjának lépéseivel:

> [!div class="checklist"]
> * Tárfiók létrehozása
> * Tároló létrehozása és az engedélyek beállítása
> * Hozzáférési kulcs lekérése
> * Alkalmazásbeállítások konfigurálása
> * Webalkalmazás üzembe helyezése az Azure-ban
> * A webalkalmazás használata

Hogy többet tudjon meg egy Azure-függvény Event Griddel történő aktiválásáról egy kép átméretezéséhez, lépjen tovább a sorozat második részére.

> [!div class="nextstepaction"]
> [Egy Azure-függvény Event Grid használatával történő aktiválása egy feltöltött kép átméretezéséhez](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
