---
title: "Azure Blob Storage tárolóban az Azure Content Delivery Network lejáratának kezelése |} Microsoft Docs"
description: "További tudnivalók a beállításait élő idő a blobok az Azure CDN gyorsítótárazását."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: 6fa563e102569064d045fbb60ebd5d1df52e3e73
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>Azure Blob Storage tárolóban az Azure Content Delivery Network lejáratának kezelése
> [!div class="op_single_selector"]
> * [Az Azure webes tartalom](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

A [Blob storage szolgáltatás](../storage/common/storage-introduction.md#blob-storage) az Azure Storage számos Azure-alapú források egyik integrálva van az Azure Content Delivery Network (CDN). Bármely nyilvánosan elérhető blobtartalom Azure CDN mindaddig, amíg az idő-live (TTL) lejárta gyorsítótárazható. Az élettartam határozza meg a `Cache-Control` fejléc a következő a HTTP-válasz a forráskiszolgálóról. Ez a cikk ismerteti, amely lehet többféle módon a `Cache-Control` az Azure Storage blob fejlécet.

> [!TIP]
> Ha szeretné, nincs TTL be blob. Ebben az esetben Azure CDN automatikusan érvényes alapértelmezett élettartam 7 nap. Ez az alapértelmezett élettartam csak azokra az általános webes kézbesítési optimalizálás. A nagy méretű fájlok optimalizálást az alapértelmezett élettartam egy nap, az pedig médiaadatfolyam-továbbítást optimalizálást, az alapértelmezett élettartam egy év.
> 
> Azure CDN gyorsabb hozzáférés blobok és egyéb fájlok működésével kapcsolatos további információkért lásd: [az Azure Content Delivery Network áttekintése](cdn-overview.md).
> 
> Azure Blob Storage tárolóban kapcsolatos további információkért lásd: [Blob storage bemutatása](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>A Cache-Control fejlécek beállítása az Azure PowerShell használatával
[Az Azure PowerShell](/powershell/azure/overview) egyik a leggyorsabb és leghatékonyabb módja az Azure-szolgáltatások felügyeletéhez. Használja a `Get-AzureStorageBlob` parancsmagot, hogy megkapja a blobra mutató hivatkozást, majd állítsa be a `.ICloudBlob.Properties.CacheControl` tulajdonság. 

Példa:

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Használhatja a PowerShell [a CDN-profil és a végpontok kezelése](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>A beállítás a Cache-Control fejlécek .NET használatával
Egy blob beállítása `Cache-Control` fejlécet a .NET-kódot, használja a [Azure Storage ügyféloldali kódtára a .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) beállítása a [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) tulajdonság.

Példa:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Nincsenek elérhető további .NET-Kódminták [Azure Blob Storage-példák .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>A beállítás a Cache-Control fejlécek más módszerrel

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
A [Azure Tártallózó](https://azure.microsoft.com/en-us/features/storage-explorer/), megtekintheti és szerkesztheti a blob storage-erőforrások, többek között a tulajdonságokat, mint a *CacheControl* tulajdonság. 

Frissítése az *CacheControl* tulajdonság a Tártallózó alkalmazással Azure BLOB:
   1. Jelöljön ki egy blobot, majd **tulajdonságok** a helyi menüből. 
   2. Görgessen le a *CacheControl* tulajdonság.
   3. Adjon meg egy értéket, majd kattintson a **mentése**.


![Az Azure Tártallózó tulajdonságai](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure parancssori felület
Az a [Azure parancssori felület](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) (CLI) kezelheti az Azure blob-erőforrások a parancssorból. Állítsa be a cache-control fejléc, ha feltölt egy blobot a az Azure parancssori felület, állítsa be a *cacheControl* tulajdonság használatával a `-p` váltani. A következő példa bemutatja, hogyan állítsa be a TTL-t egy óra (3600 másodperc):
  
"" Az azure CLI azure storage-blob feltöltése a -c <connectionstring> - p cacheControl = "max-kor 3600" .\test.txt myContainer teszt.txt =
```

### Azure storage services REST API
You can use the [Azure storage services REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) to explicitly set the *x-ms-blob-cache-control* property by using the following operations on a request:
  
   - [Put Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx)
   - [Put Block List](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)
   - [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## Testing the Cache-Control header
You can easily verify the TTL settings of your blobs. With your browser's [developer tools](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test that your blob includes the `Cache-Control` response header. You can also use a tool such as [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/), or [Fiddler](http://www.telerik.com/fiddler) to examine the response headers.

## Next Steps
* [Learn how to manage expiration of Cloud Service content in Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)

