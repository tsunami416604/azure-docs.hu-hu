---
title: "Biztonságos hozzáférés a felhőben az Azure Storage alkalmazásadatokról a |} Microsoft Docs"
description: "Az alkalmazás felhőben tárolt adatainak biztonságos jogkivonatokat, a titkosítás és a HTTPS SAS használatával"
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
ms.openlocfilehash: c43165e230a00b6a4408637fd2290a21800d07b9
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Biztonságos hozzáférés a felhőben tárolt adatainak egy alkalmazás

Ez az oktatóanyag három egy sorozat része. Megismerheti, hogyan biztosíthat biztonságos hozzáférést a tárfiókhoz. 

A sorozat három része a megismerheti, hogyan:

> [!div class="checklist"]
> * A miniatűrök eléréséhez használja az SAS-tokenje
> * Kiszolgálóoldali titkosítás bekapcsolása
> * Csak HTTPS átviteli engedélyezése

[Az Azure blob storage](../common/storage-introduction.md#blob-storage) alkalmazások fájljainak tárolásához egy robusztus szolgáltatást biztosít. Ez az oktatóanyag bővíti [az előző témakörben] [ previous-tutorial] bemutatja a biztonságos hozzáférés a tárfiók a webalkalmazásból. Ha végzett a képek lesznek titkosítva, és a webalkalmazás a miniatűr képeket elérésére használja a biztonságos SAS-tokenje.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez végrehajtotta az előző tárolási oktatóanyag: [automatizálás átméretezése feltöltött lemezképeket rácshoz esemény][previous-tutorial]. 

## <a name="set-container-public-access"></a>Tároló nyilvános hozzáférés beállítása

Ez az oktatóanyag sorozat része SAS-tokenje használ a miniatűrök eléréséhez. Ebben a lépésben beállíthatja a nyilvános elérhetőségét a _OK_ tárolót, hogy `off`.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbs  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Miniatűrök SAS-tokenje konfigurálása

Az oktatóanyag adatsorozat egyik részében a webes alkalmazás lemezképet rögzíthet a nyilvános tárolók lett megjelenítő. Ez a sorozat része, használhatja az [biztonságos hozzáférési Jogosultságkód (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) -jogkivonat beolvasása a miniatűr képét. SAS-tokenje lehetővé teszik a korlátozott hozzáférést biztosít egy tárolót vagy a blob IP, protokoll, időtartam alapján, illetve jogokat engedélyezett.

Ebben a példában a forráskódraktárban használja a `sasTokens` fiókirodai, amelynek egy frissített kódminta. Törölje a meglévő GitHub-telepítéssel a [az webalkalmazás központi telepítési forrás törlése](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_delete). Ezután konfigurálja a Githubon központi telepítést, hogy a webes alkalmazás a [az webalkalmazás központi telepítési forrás konfigurációs](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) parancsot.  

Az alábbi parancs `<web-app>` a webes alkalmazás neve.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

A `sasTokens` a tárház frissítések fiókirodai a `StorageHelper.cs` fájlt. Ez a felváltja a `GetThumbNailUrls` az alábbi példakód a feladatot. A frissített feladat kéri le a Miniatűr URL-címeket úgy, hogy egy [SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet) a kezdési ideje, a lejárati idő és a SAS-jogkivonat engedélyeinek megadásához. Amennyiben a webes alkalmazás telepített most lekéri a miniatűrök egy SAS-jogkivonat használatával URL-címet. A frissített feladatot az alábbi példában látható:
    
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

    // Set the permission of the container to public
    await container.SetPermissionsAsync(new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

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

A következő osztályokat, tulajdonságokat és módszerek használják az előző feladatban:

|Osztály  |Tulajdonságok| Metódusok  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     | |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.windowsazure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment?view=azure-dotnet)    | [Eredmények](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[Engedélyek](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>Kiszolgálóoldali titkosítás

[Az Azure Storage szolgáltatás titkosítási (SSE)](../common/storage-service-encryption.md) segítségével hatékonyan védheti, valamint az adatok védelme. SSE titkosítja az adatokat nyugalmi, titkosítási, visszafejtési és kulcskezelés kezelése. Összes adat titkosítva van, 256 bites [AES titkosítási](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), a legerősebb blokk egyik Rejtjelek érhető el.

A következő példa engedélyezi a titkosítást a blobok. Titkosítás engedélyezése előtt létrehozott meglévő blobok nem titkosítottak. A `x-ms-server-encrypted` blob a kérelem fejlécében a blob titkosítási állapotát jeleníti meg.

```azurecli-interactive
az storage account update --encryption-services blob --name <storage-account-name> --resource-group myResourceGroup
```

Most, hogy engedélyezve van, töltse fel az új képet a webalkalmazás.

Használatával `curl` kapcsolóját `-I` csak a fejlécek lekéréséhez helyettesítse a saját értékeit `<storage-account-name>`, `<container>`, és `<blob-name>`.  

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<blob-name>?$sasToken -I
```

A válaszban, vegye figyelembe a `x-ms-server-encrypted` fejléc megjelenítése `true`. Ezt a fejlécet azonosítja, hogy az SSE most titkosítja az adatokat.

```
HTTP/1.1 200 OK
Content-Length: 209489
Content-Type: image/png
Last-Modified: Mon, 11 Sep 2017 19:27:42 GMT
Accept-Ranges: bytes
ETag: "0x8D4F94B2BE76D45"
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 57047db3-001e-0050-3e34-2ba769000000
x-ms-version: 2017-04-17
x-ms-lease-status: unlocked
x-ms-lease-state: available
x-ms-blob-type: BlockBlob
x-ms-server-encrypted: true
Date: Mon, 11 Sep 2017 19:27:46 GMT
```

## <a name="enable-https-only"></a>Csak a HTTPS engedélyezéséhez

Győződjön meg arról, hogy a tárfiók érkező vagy oda irányuló kérelmek adatok biztonságos, hogy csak HTTPS kérelmek korlátozhatja. Frissítse a tárolási fiók szükséges protokollt használatával a [az tárolási fiók frissítés](/cli/azure/storage/account#az_storage_account_update) parancsot.

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Tesztelje a kapcsolatot használja `curl` használatával a `HTTP` protokoll.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Most, hogy biztonságos átvitele szükség, a következő hibaüzenet jelenik meg:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>További lépések

A sorozat három része megtanulta, hogyan biztosíthat biztonságos hozzáférést a tárfiókhoz, például hogy miként:

> [!div class="checklist"]
> * A miniatűrök eléréséhez használja az SAS-tokenje
> * Kiszolgálóoldali titkosítás bekapcsolása
> * Csak HTTPS átviteli engedélyezése

Előzetes áttekintésével megismerheti, hogyan figyelheti és a felhőalapú tároló-alkalmazás az adatsorozat négy részét.

> [!div class="nextstepaction"]
> [Figyelésére és hibaelhárítására alkalmazás felhő kiszolgálóalkalmazás-tároló](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json