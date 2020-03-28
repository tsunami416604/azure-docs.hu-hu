---
title: Biztonságos hozzáférés az alkalmazásadatokhoz
titleSuffix: Azure Storage
description: Alkalmazásainak adatait SAS-jogkivonatok, titkosítás és HTTPS protokoll segítségével tudhatja biztonságban a felhőben.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: 13a2a0bcc362a13b0c42650509d356f613527cfc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061329"
---
# <a name="secure-access-to-application-data"></a>Biztonságos hozzáférés az alkalmazásadatokhoz

Ez az oktatóanyag egy sorozat harmadik része. Megtudhatja, hogyan teheti biztonságossá a tárfiókhoz való hozzáférést. 

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A miniatűr képekhez a SAS-tokenek segítségével férhet hozzá
> * Kiszolgálóoldali titkosítás bekapcsolása
> * Csak HTTPS-protokollal történő átvitel engedélyezése

Az [Azure Blob Storage](../common/storage-introduction.md#blob-storage) stabil szolgáltatást biztosít az alkalmazások fájljainak tárolásához. Az oktatóanyag kibővíti [az előző témakört][previous-tutorial], hogy bemutassa, hogyan teheti biztonságossá a tárfiókhoz való hozzáférést egy webalkalmazásból. Az oktatóanyag elvégzése után a rendszer titkosítja a képeket, és a webalkalmazás biztonságos SAS-jogkivonatok segítségével fér hozzá a miniatűr képekhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez el kell végeznie az előző tárolási oktatóanyagot: [Feltöltött képek átméretezésének automatizálása az Event Grid használatával][previous-tutorial].

## <a name="set-container-public-access"></a>A tároló nyilvános hozzáférésének beállítása

Az oktatóanyag-sorozat ezen részében SAS-jogkivonatokat használunk a miniatűr képekhez való hozzáféréshez. Ebben a lépésben a *thumbnails* tároló nyilvános hozzáférését `off` értékre állítja be.

```azurecli-interactive 
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>SAS-jogkivonatok konfigurálása miniatűr képekhez

Az oktatóanyag-sorozat első részében a webalkalmazás egy nyilvános tárolóból származó képeket jelenített meg. A sorozat ezen részében megosztott hozzáférésű aláírások (SAS) jogkivonatokat használ a miniatűr képek lekéréséhez. A SAS-jogkivonatok segítségével korlátozott hozzáférést biztosíthat egy tárolóhoz vagy blobhoz IP-cím, protokoll, időintervallum vagy engedélyezett jogosultságok alapján. A SAS-ről további információt az [Azure Storage-erőforrásokhoz megosztott hozzáférésű aláírások (SAS) használatával való korlátozott hozzáférés megadása](../common/storage-sas-overview.md)című témakörben talál.

A példában a forráskód adattára a `sasTokens` ágat használja, amely egy frissített kódmintát tartalmaz. Törölje a meglévő GitHub-telepítést az [az webapp deployment source delete](/cli/azure/webapp/deployment/source) parancs segítségével. Azután konfigurálja a GitHub-telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével.

Az alábbi parancsban a `<web-app>` a webalkalmazás neve.

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

Az adattár `sasTokens` ága frissíti a `StorageHelper.cs` fájlt. A `GetThumbNailUrls` feladatot az alábbi példakóddal cseréli ki. A frissített feladat lekéri a miniatűr URL-címeket egy [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) használatával a SAS-jogkivonat kezdési, lejárati idejének és engedélyeinek megadásához. Telepítést követően a webalkalmazás mostantól SAS-jogkivonatot használó URL-címmel kéri le a miniatűr képeket. A frissített feladat az alábbi példában látható:

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

Az előző feladatban használt osztályok, tulajdonságok és metódusok a következők:

| Osztály | Tulajdonságok | Metódusok |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [Uri](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Létezik](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs (GetBlobs)](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem elem](/dotnet/api/azure.storage.blobs.models.blobitem) | [Név](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder között](/dotnet/api/system.uribuilder) | [Lekérdezés](/dotnet/api/system.uribuilder.query) |  |
|[Lista](/dotnet/api/system.collections.generic.list-1) | | [Hozzáadás](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="server-side-encryption"></a>Kiszolgálóoldali titkosítás

Az [Azure Storage Service Encryption (SSE)](../common/storage-service-encryption.md) segít az adatok biztonságos megőrzésében. Az SSE titkosítja az inaktív adatokat, valamint titkosítási, visszafejtési és kulcskezelési feladatokat lát el. Minden adat titkosítása 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) történik, amely a jelenleg elérhető egyik legerősebb blokktitkosító.

Az SSE automatikusan titkosítja minden teljesítményszint (Standard és Prémium), minden üzembehelyezési modell (Azure Resource Manager és klasszikus) és minden Azure Storage-szolgáltatás (Blob, Queue, Table és File) adatait. 

## <a name="enable-https-only"></a>Csak HTTPS engedélyezése

A tárfiókba érkező és az onnan kimenő adatkérések biztonsága érdekében korlátozhatja a kéréseket a HTTPS-protokoll használatára. Frissítse a tárfiók által kért protokollt az [az storage account update](/cli/azure/storage/account) parancs segítségével.

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
