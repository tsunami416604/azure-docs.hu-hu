---
title: Az alkalmazásadatok biztonságos elérése
titleSuffix: Azure Storage
description: Alkalmazásainak adatait SAS-jogkivonatok, titkosítás és HTTPS protokoll segítségével tudhatja biztonságban a felhőben.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: 654efd8f5fbe31131ae03a8e794bc2113df2d29f
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912188"
---
# <a name="secure-access-to-application-data"></a>Az alkalmazásadatok biztonságos elérése

Ez az oktatóanyag egy sorozat harmadik része. Megtudhatja, hogyan teheti biztonságossá a tárfiókhoz való hozzáférést. 

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A miniatűr képekhez a SAS-tokenek segítségével férhet hozzá
> * Kiszolgálóoldali titkosítás bekapcsolása
> * Csak HTTPS-protokollal történő átvitel engedélyezése

Az [Azure Blob Storage](../common/storage-introduction.md#blob-storage) stabil szolgáltatást biztosít az alkalmazások fájljainak tárolásához. Ez az oktatóanyag kibővíti [az előző témakört][previous-tutorial] , amely azt mutatja be, hogyan lehet biztonságossá tenni a Storage-fiókjához való hozzáférést egy webalkalmazásból. Az oktatóanyag elvégzése után a rendszer titkosítja a képeket, és a webalkalmazás biztonságos SAS-jogkivonatok segítségével fér hozzá a miniatűr képekhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez el kell végeznie az előző tárolási oktatóanyagot: a [feltöltött képek átméretezésének automatizálása Event Grid használatával][previous-tutorial].

## <a name="set-container-public-access"></a>A tároló nyilvános hozzáférésének beállítása

Az oktatóanyag-sorozat ezen részében SAS-jogkivonatokat használunk a miniatűr képekhez való hozzáféréshez. Ebben a lépésben a _thumbnails_ tároló nyilvános hozzáférését `off` értékre állítja be.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ 
--account-name $blobStorageAccount --account-key $blobStorageAccountKey \ 
--name thumbnails --public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>SAS-jogkivonatok konfigurálása miniatűr képekhez

Az oktatóanyag-sorozat első részében a webalkalmazás egy nyilvános tárolóból származó képeket jelenített meg. A sorozat ezen részében a közös hozzáférésű aláírások (SAS) jogkivonatok segítségével kérheti le a miniatűr lemezképeket. A SAS-jogkivonatok segítségével korlátozott hozzáférést biztosíthat egy tárolóhoz vagy blobhoz IP-cím, protokoll, időintervallum vagy engedélyezett jogosultságok alapján. Az SAS-vel kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférési aláírások (SAS) használatával](../common/storage-sas-overview.md).

A példában a forráskód adattára a `sasTokens` ágat használja, amely egy frissített kódmintát tartalmaz. Törölje a meglévő GitHub-telepítést az [az webapp deployment source delete](/cli/azure/webapp/deployment/source) parancs segítségével. Azután konfigurálja a GitHub-telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével.  

Az alábbi parancsban a `<web-app>` a webalkalmazás neve.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

Az adattár `sasTokens` ága frissíti a `StorageHelper.cs` fájlt. A `GetThumbNailUrls` feladatot az alábbi példakóddal cseréli ki. A frissített feladat lekéri a miniatűrök URL-címeit úgy, hogy egy [SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy) szabályzat segítségével kezdési időpontot, lejárati időpontot és engedélyeket ad meg a SAS-jogkivonat számára. Telepítést követően a webalkalmazás mostantól SAS-jogkivonatot használó URL-címmel kéri le a miniatűr képeket. A frissített feladat az alábbi példában látható:
    
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
|[StorageCredentials](/dotnet/api/microsoft.azure.cosmos.table.storagecredentials)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)        |
|[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)     | |[GetContainerReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getcontainerreference)         |
|[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer)     | |[SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)       |
|[BlobContinuationToken](/dotnet/api/microsoft.azure.storage.blob.blobcontinuationtoken)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment)    | [Results](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment.results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy)     | [SharedAccessStartTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime)<br>[Engedélyek](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.permissions) |        |

## <a name="server-side-encryption"></a>Kiszolgálóoldali titkosítás

Az [Azure Storage Service Encryption (SSE)](../common/storage-service-encryption.md) segít az adatok biztonságos megőrzésében. Az SSE titkosítja az inaktív adatokat, valamint titkosítási, visszafejtési és kulcskezelési feladatokat lát el. Minden adat titkosítása 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) történik, amely a jelenleg elérhető egyik legerősebb blokktitkosító.

Az SSE automatikusan titkosítja minden teljesítményszint (Standard és Prémium), minden üzembehelyezési modell (Azure Resource Manager és klasszikus) és minden Azure Storage-szolgáltatás (Blob, Queue, Table és File) adatait. 

## <a name="enable-https-only"></a>Csak HTTPS engedélyezése

A tárfiókba érkező és az onnan kimenő adatkérések biztonsága érdekében korlátozhatja a kéréseket a HTTPS-protokoll használatára. Frissítse a tárfiók által kért protokollt az [az storage account update](/cli/azure/storage/account) parancs segítségével.

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Tesztelje a kapcsolatot a `curl` használatával a `HTTP` protokollal.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Most, hogy biztonságos átvitel szükséges, az alábbi üzenetet kapja:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Következő lépések

A sorozat harmadik részében megtanulta, hogy miként teheti biztonságossá a tárfiókhoz való hozzáférést, többek között:

> [!div class="checklist"]
> * A miniatűr képekhez a SAS-tokenek segítségével férhet hozzá
> * Kiszolgálóoldali titkosítás bekapcsolása
> * Csak HTTPS-protokollal történő átvitel engedélyezése

A sorozat negyedik részében a felhőalapú tárolási alkalmazások monitorozásának és hibaelhárításának módjaival ismerkedhet meg.

> [!div class="nextstepaction"]
> [Monitorozás és hibaelhárítás alkalmazás felhőalkalmazás-tárolójában](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
