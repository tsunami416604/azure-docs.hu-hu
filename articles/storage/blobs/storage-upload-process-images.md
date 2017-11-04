---
title: "Töltse fel a felhőben az Azure Storage képadatok |} Microsoft Docs"
description: "Az Azure blob storage használata egy webalkalmazást az alkalmazás adatainak tárolásához"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: a204498016ff837c5247009eaaffbd4f79285d0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>A felhőben az Azure Storage kép adatok feltöltése

Ez az oktatóanyag egy sorozat része. Ez az oktatóanyag bemutatja, hogyan központi telepítése a webes alkalmazás használ az Azure Storage ügyféloldali kódtár képek feltöltése a tárfiókba. Amikor végzett, hogy egy webalkalmazást, tárolására és az Azure storage képek megjelenítése.

![Lemezképeket tároló nézet](media/storage-upload-process-images/figure2.png)

A rész az adatsorozatok megismerheti, hogyan:

> [!div class="checklist"]
> * Create a storage account
> * A tároló létrehozása és az engedélyek beállítása
> * Hívóbetű beolvasása
> * Az Alkalmazásbeállítások konfigurálása
> * Webalkalmazás üzembe helyezése az Azure-bA
> * A webes alkalmazás interakciót

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot 

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.
 
A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot.
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-a-storage-account"></a>Create a storage account
 
A minta egy blob-tárolóba, az Azure Storage-fiók feltölti a lemezképeket. A storage-fiók egy egyedi névteret tárolhatja és érheti el az Azure storage-adatobjektumok biztosít. Az [az storage account create](/cli/azure/storage/account#create) paranccsal hozzon létre egy tárfiókot a létrehozott erőforráscsoportban. 

> [!IMPORTANT] 
> Az oktatóanyag 2 részén esemény-előfizetések blob Storage használata. Esemény-előfizetések jelenleg csak a támogatott Blob storage-fiókok a nyugati középső Régiójában és USA nyugati régiója 2. Ez a korlátozás miatt tárolják a képeket és miniatűrök mintaalkalmazás által használt Blob storage-fiók kell létrehoznia.   

Az alábbi parancs helyettesítse a saját globálisan egyedi nevet a Blob storage-fiók, ahol megjelenik a `<blob_storage_account>` helyőrző.  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>A blob storage tárolók létrehozása
 
Az alkalmazás két tárolót használja a Blob storage-fiók. Tárolók mappák hasonló, és a blobok tárolására szolgálnak. A _képek_ tárolóban, ahol az alkalmazás feltöltését teljes felbontású képek. A sorozat későbbi részében, az Azure-függvény alkalmazások átméretezett kép miniatűrök feltölti a _OK_ tároló. 

A tárfiók kulcsa segítségével könnyebben nyerhet a [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#list) parancsot. Ezután a kulcs létrehozására használhatja két tárolók használata a [az tároló létrehozása](/cli/azure/storage/container#create) parancsot.  
 
Ebben az esetben `<blob_storage_account>` létrehozott Blob storage-fiók neve. A _képek_ tárolók nyilvános hozzáférés van beállítva `off`, a _OK_ tárolók nyilvános hozzáférés van beállítva `container`. A `container` nyilvános hozzáférés beállítás lehetővé teszi, hogy a miniatűrök is meg szeretné jeleníteni, személyek, amely a Microsoft webhelyén talál.
 
```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbs --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

Jegyezze fel a blob storage-fiók nevét és a kulcs. A mintaalkalmazás ezeket a beállításokat használja a tárfiók, hogy a képek feltöltése való kapcsolódáshoz. 

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása 

Az alkalmazást tároló webkiszolgálófarm helyét, méretét és funkcióit egy [App Service-csomag](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) határozza meg. 

Hozzon létre egy App Service-csomagot az [az appservice plan create](/cli/azure/appservice/plan#create) paranccsal. 

Az alábbi példa egy `myAppServicePlan` nevű App Service-csomag létrehozását mutatja be az **INGYENES** tarifacsomagban: 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása 

A webalkalmazás üzemeltetési helyet biztosít a minta-mintaalkalmazás kódját a GitHub minta tárházból telepített. Az [ az webapp create](/cli/azure/webapp#create) paranccsal hozzon létre egy [webalkalmazást](../../app-service/app-service-web-overview.md) a `myAppServicePlan` App Service-csomagban.  
 
A következő parancsban cserélje le `<web_app>` egyedi névvel (érvényes karakterek: `a-z`, `0-9`, és `-`). Ha `<web_app>` van nem egyedi a következő üzenet jelenik: _webhely a megadott nevű `<web_app>` már létezik._ A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>A GitHub-tárházban a mintaalkalmazás telepítése 

App Service Telepítse központilag a tartalmakat egy webalkalmazás több módot is támogatja. Ebben az oktatóanyagban egy nyilvános GitHub minta tárházból a webes alkalmazás központi telepítése: [https://github.com/Azure-Samples/storage-blob-upload-from-webapp](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). A webes alkalmazás a GitHub-KözpontiTelepítés konfigurálása a [az webalkalmazás központi telepítési forrás konfigurációs](/cli/azure/webapp/deployment/source#config) parancsot. Cserélje le `<web_app>` az előző lépésben létrehozott webalkalmazás nevét.

A minta-projekt tartalmazza az [ASP.NET MVC](https://www.asp.net/mvc) alkalmazást, amely elfogadja a képen menti ezeket az adatokat egy tárfiókot, és tárolókból miniatűr képeket jeleníti meg. A webalkalmazás a [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet), és a [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) az az Azure storage ügyféloldali kódtár segítségével kommunikál az Azure storage számot tartó névtereket. 

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>A webalkalmazás-beállítások konfigurálása 

A minta webes alkalmazás használatát a [Azure Storage ügyféloldali kódtár](/dotnet/api/overview/azure/storage?view=azure-dotnet) szükséges hozzáférési jogkivonatok, hogy a képek feltöltése használt. A tárfiók hitelesítő adatainak a Storage szolgáltatás SDK által használt a webalkalmazás számára az alkalmazás beállításainak megadása Alkalmazásbeállítások hozzáadása a telepített alkalmazás lecserélhető a [az webapp appsettings konfiguráció](/cli/azure/webapp/config/appsettings#set) parancsot. 

Az alábbi parancs `<blob_storage_account>` a Blob storage-fiók neve és `<blob_storage_key>` a társított kulcs. Cserélje le `<web_app>` az előző lépésben létrehozott webalkalmazás nevét.     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbs \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

A webes alkalmazás telepített és konfigurált, után tesztelheti a lemezkép feltöltése funkció az alkalmazásban.   

## <a name="upload-an-image"></a>Rendszerkép feltöltése 

A webes alkalmazás teszteléséhez tallózással keresse meg a közzétett alkalmazás URL-CÍMÉT. A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`. Válassza ki a **fénykép feltöltése** a régióban legyen, jelölje ki és -fájl feltöltése vagy áthúzása egy fájl a régióhoz. A kép eltűnik, ha sikeresen feltöltve.

![ImageResizer alkalmazás](media/storage-upload-process-images/figure1.png)

Példakód a `UploadFiletoStorage` a feladat a `Storagehelper.cs` fájllal a képek feltöltése a `images` tároló részére a tárolási fiók használata a [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) metódust. A következő példakód tartalmazza a `UploadFiletoStorage` feladat. 

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

A következő osztályok és módszerek használják az előző feladatban:

|Osztály  |Módszer  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Ellenőrizze a kép megjelenik-e a tárfiókban lévő

Jelentkezzen be https://portal.azure.com. A bal oldali menüben válassza ki a **tárfiókok**, majd válassza ki a tárfiók nevét. A **áttekintése**, jelölje be a **képek** tároló.

Ellenőrizze, hogy a kép megjelenik-e a tárolóban.

![Lemezképeket tároló nézet](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Teszt miniatűr megtekintése

Miniatűr megtekintésre teszteléséhez lesz feltöltött lemezkép a miniatűr tároló az alkalmazás tudja olvasni a miniatűr tároló biztosítása érdekében.

Jelentkezzen be https://portal.azure.com. A bal oldali menüben válassza ki a **tárfiókok**, majd válassza ki a tárfiók nevét. Válassza ki **tárolók** alatt **Blob szolgáltatás** válassza ki a **OK** tároló. Válassza ki **feltöltése** megnyitásához a **feltöltése a blob** ablaktáblán.

Válassza a fájlválasztó egy fájlhoz, majd **feltöltése**.

Lépjen vissza az alkalmazásnak, hogy ellenőrizze, hogy a lemezképet fel van töltve a **OK** tároló látható.

![Lemezképeket tároló nézet](media/storage-upload-process-images/figure2.png)

Az a **OK** tároló az Azure portálon, válassza ki a lemezképet, feltöltött, és válassza ki **törlése** törli a lemezképet. Az adatsorozat két részén automatizálta a miniatűr képek létrehozása, nincs szükség a lemezképet.

CDN-t az Azure storage-fiók tartalmak gyorsítótárazására való engedélyezhető. Amíg nem ez az oktatóanyag ismerteti, hogyan CDN engedélyezése az Azure storage-fiók, ellátogathat: [Azure-tárfiók integrálása az Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Következő lépések

Az első rész adatsorozat megtanulta, hogyan például való interakció webes alkalmazás konfigurálásával kapcsolatos:

> [!div class="checklist"]
> * Create a storage account
> * A tároló létrehozása és az engedélyek beállítása
> * Hívóbetű beolvasása
> * Az Alkalmazásbeállítások konfigurálása
> * Webalkalmazás üzembe helyezése az Azure-bA
> * A webes alkalmazás interakciót

Előzetes a sorozat esemény rács használata az Azure funkció kép átméretezése – áttekintés két részét.

> [!div class="nextstepaction"]
> [Egy Azure-funkció feltöltött kép átméretezése esemény rács segítségével](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
