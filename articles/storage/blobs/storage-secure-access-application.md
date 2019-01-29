---
title: Biztonságos hozzáférés egy alkalmazás adataihoz a felhőben az Azure Storage segítségével | Microsoft Docs
description: Alkalmazásainak adatait SAS-jogkivonatok, titkosítás és HTTPS protokoll segítségével tudhatja biztonságban a felhőben.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 05/30/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: ce44d4c066183ff796c8efa1dceb20dff73868ee
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192392"
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Biztonságos hozzáférés egy alkalmazás adataihoz a felhőben

Ez az oktatóanyag egy sorozat harmadik része. Megtudhatja, hogyan teheti biztonságossá a tárfiókhoz való hozzáférést. 

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A miniatűr képekhez a SAS-tokenek segítségével férhet hozzá
> * Kiszolgálóoldali titkosítás bekapcsolása
> * Csak HTTPS-protokollal történő átvitel engedélyezése

Az [Azure Blob Storage](../common/storage-introduction.md#blob-storage) stabil szolgáltatást biztosít az alkalmazások fájljainak tárolásához. Az oktatóanyag kibővíti [az előző témakört][previous-tutorial], hogy bemutassa, hogyan teheti biztonságossá a tárfiókhoz való hozzáférést egy webalkalmazásból. Az oktatóanyag elvégzése után a rendszer titkosítja a képeket, és a webalkalmazás biztonságos SAS-jogkivonatok segítségével fér hozzá a miniatűr képekhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez el kell végeznie az előző tárolási oktatóanyagot: [Event Grid használatával feltöltött képek átméretezésének automatizálása][previous-tutorial]. 

## <a name="set-container-public-access"></a>A tároló nyilvános hozzáférésének beállítása

Az oktatóanyag-sorozat ezen részében SAS-jogkivonatokat használunk a miniatűr képekhez való hozzáféréshez. Ebben a lépésben a _thumbnails_ tároló nyilvános hozzáférését `off` értékre állítja be.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbnails  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>SAS-jogkivonatok konfigurálása miniatűr képekhez

Az oktatóanyag-sorozat első részében a webalkalmazás egy nyilvános tárolóból származó képeket jelenített meg. A sorozat ezen részében a [közös hozzáférésű jogosultságkód (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) jogkivonatai segítségével kérjük le a miniatűr képeket. A SAS-jogkivonatok segítségével korlátozott hozzáférést biztosíthat egy tárolóhoz vagy blobhoz IP-cím, protokoll, időintervallum vagy engedélyezett jogosultságok alapján.

A példában a forráskód adattára a `sasTokens` ágat használja, amely egy frissített kódmintát tartalmaz. Törölje a meglévő GitHub-telepítést az [az webapp deployment source delete](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_delete) parancs segítségével. Azután konfigurálja a GitHub-telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével.  

Az alábbi parancsban a `<web-app>` a webalkalmazás neve.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

Az adattár `sasTokens` ága frissíti a `StorageHelper.cs` fájlt. A `GetThumbNailUrls` feladatot az alábbi példakóddal cseréli ki. A frissített feladat lekéri a miniatűrök URL-címeit úgy, hogy egy [SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet) szabályzat segítségével kezdési időpontot, lejárati időpontot és engedélyeket ad meg a SAS-jogkivonat számára. Telepítést követően a webalkalmazás mostantól SAS-jogkivonatot használó URL-címmel kéri le a miniatűr képeket. A frissített feladat az alábbi példában látható:
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

Az előző feladatban használt osztályok, tulajdonságok és metódusok a következők:

|Osztály  |Tulajdonságok| Metódusok  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     | |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.windowsazure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment?view=azure-dotnet)    | [Results](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[Engedélyek](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>Kiszolgálóoldali titkosítás

Az [Azure Storage Service Encryption (SSE)](../common/storage-service-encryption.md) segít az adatok biztonságos megőrzésében. Az SSE titkosítja az inaktív adatokat, valamint titkosítási, visszafejtési és kulcskezelési feladatokat lát el. Minden adat titkosítása 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) történik, amely a jelenleg elérhető egyik legerősebb blokktitkosító.

Az SSE automatikusan titkosítja minden teljesítményszint (Standard és Prémium), minden üzembehelyezési modell (Azure Resource Manager és klasszikus) és minden Azure Storage-szolgáltatás (Blob, Queue, Table és File) adatait. 

## <a name="enable-https-only"></a>Csak HTTPS engedélyezése

A tárfiókba érkező és az onnan kimenő adatkérések biztonsága érdekében korlátozhatja a kéréseket a HTTPS-protokoll használatára. Frissítse a tárfiók által kért protokollt az [az storage account update](/cli/azure/storage/account#az_storage_account_update) parancs segítségével.

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Tesztelje a kapcsolatot a `HTTP` használatával a `curl` protokollal.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Most, hogy biztonságos átvitel szükséges, az alábbi üzenetet kapja:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>További lépések

A sorozat harmadik részében megtanulta, hogy miként teheti biztonságossá a tárfiókhoz való hozzáférést, többek között:

> [!div class="checklist"]
> * A miniatűr képekhez a SAS-tokenek segítségével férhet hozzá
> * Kiszolgálóoldali titkosítás bekapcsolása
> * Csak HTTPS-protokollal történő átvitel engedélyezése

A sorozat negyedik részében a felhőalapú tárolási alkalmazások monitorozásának és hibaelhárításának módjaival ismerkedhet meg.

> [!div class="nextstepaction"]
> [Monitorozás és hibaelhárítás alkalmazás felhőalkalmazás-tárolójában](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
