---
title: "Azure Storage blobs szolgáltatásban az Azure CDN lejáratának kezelése |} Microsoft Docs"
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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d4741921806e443d92c385a04b781cec296c2ae8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-expiration-of-azure-storage-blobs-in-azure-cdn"></a>Azure Storage blobs szolgáltatásban az Azure CDN lejáratának kezelése
> [!div class="op_single_selector"]
> * [Az Azure Web Apps/Felhőszolgáltatások, ASP.NET, vagy az IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Storage-blob szolgáltatás](cdn-manage-expiration-of-blob-content.md)
> 
> 

A [blob szolgáltatás](../storage/common/storage-introduction.md#blob-storage) a [Azure Storage](../storage/common/storage-introduction.md) integrálva van a több Azure-alapú források egyik Azure CDN szolgáltatás használata.  Bármely nyilvánosan elérhető blobtartalom Azure CDN mindaddig, amíg az idő-live (TTL) lejárta gyorsítótárazható.  Az élettartam határozza meg a [ *Cache-Control* fejléc](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) az Azure Storage-ból a HTTP-válasz.

> [!TIP]
> Választhatja a blob nem TTL beállítása.  Ebben az esetben Azure CDN automatikusan érvényes alapértelmezett élettartam 7 nap.
> 
> Azure CDN gyorsabb hozzáférés blobok és egyéb fájlok működésével kapcsolatos további információkért lásd: a [Azure CDN áttekintése](cdn-overview.md).
> 
> Az Azure Storage-blob szolgáltatásban további részletekért lásd: [Blob szolgáltatással kapcsolatos fogalmak](https://msdn.microsoft.com/library/dd179376.aspx). 
> 
> 

Ez az oktatóanyag bemutatja a TTL-t állíthat be az Azure Storage blob számos módja.  

## <a name="azure-powershell"></a>Azure PowerShell
[Az Azure PowerShell](/powershell/azure/overview) egyik a leggyorsabb, a leghatékonyabb módszer az Azure-szolgáltatások felügyeletéhez.  Használja a `Get-AzureStorageBlob` parancsmagot, hogy megkapja a blobra mutató hivatkozást, majd állítsa be a `.ICloudBlob.Properties.CacheControl` tulajdonság. 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Használhatja a PowerShell [a CDN-profil és a végpontok kezelése](cdn-manage-powershell.md).
> 
> 

## <a name="azure-storage-client-library-for-net"></a>Az Azure Storage .NET-hez készült ügyféloldali kódtára
Egy blob TTL-t használó .NET beállításához használja a [Azure Storage ügyféloldali kódtára a .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) beállítása a [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) tulajdonság.

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
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Nincsenek elérhető számos további .NET Kódminták a [Azure Blob Storage-példák .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 
> 

## <a name="other-methods"></a>Más módszerrel
* [Azure parancssori felület](../cli-install-nodejs.md)
  
    Amikor feltöltése a blob, állítsa be a *cacheControl* tulajdonság használatával a `-p` váltani.  Ebben a példában az élettartam beállítja egy óra (3600 másodperc).
  
    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
  
    Explicit módon beállítva a *x-ms-blob-cache-control* tulajdonságának egy [Put Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Put tiltólista](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx), vagy [Blob tulajdonságai](https://msdn.microsoft.com/library/azure/ee691966.aspx) kérelmet.
* Külső tárolófelügyeleti eszközök
  
    Egyes külső Azure Tárolófelügyeleti eszközök lehetővé teszik a *CacheControl* blobok tulajdonságát. 

## <a name="testing-the-cache-control-header"></a>Tesztelés a *Cache-Control* fejléc
Az élettartam a blobok könnyen ellenőrizheti.  A böngésző használatával [fejlesztői eszközök](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), tesztelje, hogy a blob közé tartoznak a következők a *Cache-Control* válaszfejlécet.  Egy eszköz, például is használható **wget**, [Postman](https://www.getpostman.com/), vagy [Fiddler](http://www.telerik.com/fiddler) megvizsgálhatja a response fejlécekkel együtt.

## <a name="next-steps"></a>Következő lépések
* [Olvassa el a *Cache-Control* fejléc](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* [Útmutató: Azure CDN felhő Felhőszolgáltatásbeli tartalom lejáratának kezelése](cdn-manage-expiration-of-cloud-service-content.md)

