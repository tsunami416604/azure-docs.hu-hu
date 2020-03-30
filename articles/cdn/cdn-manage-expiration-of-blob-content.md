---
title: Az Azure Blob-tárház lejáratának kezelése
titleSuffix: Azure Content Delivery Network
description: Ismerje meg az Azure CDN-gyorsítótárazásban a blobok élő-idő szabályozásának lehetőségeit.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: f28282a802e4b38fadc05c7090fa2a2af154de54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083159"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Az Azure Blob-tárhely lejáratának kezelése az Azure CDN-ben
> [!div class="op_single_selector"]
> * [Azure webes tartalom](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob-tárhely](cdn-manage-expiration-of-blob-content.md)
> 
> 

Az Azure Storage [blobtárolási szolgáltatása](../storage/common/storage-introduction.md#blob-storage) az Azure Content Delivery Network (CDN) szolgáltatással integrált számos Azure-alapú eredet egyike. Bármely nyilvánosan elérhető blobtartalom gyorsítótárazható az Azure CDN-ben, amíg az életidő (TTL) el nem telik. A TTL-t `Cache-Control` az eredeti kiszolgáló HTTP-válaszának fejléce határozza meg. Ez a cikk számos módot, `Cache-Control` amely beállítható a fejléc egy blob az Azure Storage-ban.

A gyorsítótár-beállításokat az Azure Portalon is szabályozhatja a CDN-gyorsítótárazási szabályok beállításával. Ha gyorsítótárazási szabályt hoz létre, és a gyorsítótárazási viselkedését **felülbírálásra** vagy **a gyorsítótár mellőzésére**állítja, a cikkben tárgyalt forrásszerinti gyorsítótárazási beállításokat a rendszer figyelmen kívül hagyja. Az általános gyorsítótárazási fogalmakról a [Gyorsítótárazás működése című](cdn-how-caching-works.md)témakörben talál további információt.

> [!TIP]
> Dönthet úgy, hogy nem állít be TTL-t egy blobon. Ebben az esetben az Azure CDN automatikusan alkalmazza az alapértelmezett Hét napos TTL-t, kivéve, ha gyorsítótárazási szabályokat állított be az Azure Portalon. Ez az alapértelmezett TTL csak az általános webes kézbesítésoptimalizálásra vonatkozik. A nagy fájloptimalizálások esetében az alapértelmezett TTL egy nap, a médiaadatfolyam-optimalizálások esetében pedig egy év.
> 
> Ha többet szeretne tudni arról, hogy az Azure CDN hogyan gyorsítja fel a blobokhoz és más fájlokhoz való hozzáférést, olvassa el az Azure Content Delivery Network áttekintése című [témakört.](cdn-overview.md)
> 
> Az Azure Blob storage-ról további információt a [Blob-tárterület – bevezetés című témakörben talál.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>A Gyorsítótár-vezérlő fejléceinek beállítása CDN-gyorsítótárazási szabályok használatával
A blob fejlécének `Cache-Control` beállításának előnyben részesített módja a gyorsítótárazási szabályok használata az Azure Portalon. A CDN-gyorsítótárazási szabályokról további információt az [Azure CDN-gyorsítótárazási viselkedésének vezérlése gyorsítótárazási szabályokkal](cdn-caching-rules.md)című témakörben talál.

> [!NOTE] 
> A gyorsítótárazási szabályok csak a **Verizon azure CDN Standard és** az **Akamai-profilokból származó Azure CDN Standard esetén** érhetők el. A **Verizon-profilokból származó Azure CDN Premium** esetén az [Azure CDN-szabályok motorját](cdn-rules-engine.md) kell használnia a Portál **kezelése** portálon hasonló funkciókhoz.

**A CDN-gyorsítótárazási szabályok lapjára való navigáláshoz:**

1. Az Azure Portalon válasszon ki egy CDN-profilt, majd válassza ki a blob végpontját.

2. A bal oldali ablaktáblán, a Beállítások alatt válassza a **Gyorsítótárszabályok** lehetőséget.

   ![A CDN gyorsítótárazási szabályai gomb](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Megjelenik a **Gyorsítótárszabályok** lap.

   ![CDN gyorsítótárazási lapja](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Blob storage-szolgáltatás Cache-Control fejléceinek beállítása globális gyorsítótárazási szabályok használatával:**

1. A **Globális gyorsítótárazási szabályok**csoportban állítsa a **lekérdezési karakterláncok figyelési viselkedését** a **Lekérdezési karakterláncok figyelmen kívül hagyása** beállításra, és állítsa a **gyorsítótárazási viselkedést** **felülbírálásra.**
      
2. A **Gyorsítótár lejárati időtartamához**írja be a 3600 értéket a **Másodperc** mezőbe, az 1 értéket az **Órák** mezőbe. 

   ![Példa a CDN globális gyorsítótárazására](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Ez a globális gyorsítótár-szabály egy órás gyorsítótár-időtartamot állít be, és hatással van a végpontra vonatkozó összes kérelemre. Felülír minden `Cache-Control` `Expires` olyan vagy HTTP-fejlécet, amelyet a végpont által megadott forráskiszolgáló küld.   

3. Kattintson a **Mentés** gombra.
 
**Blobfájl Cache-Control fejléceinek beállítása egyéni gyorsítótárazási szabályok használatával:**

1. Az **Egyéni gyorsítótárazási szabályok csoportban**hozzon létre két egyezési feltételt:

     A. Az első egyezési feltételnél állítsa `/blobcontainer1/*` az **Egyezés feltételt** a **Görbe** beállításra, és írja be a Match érték **mezőbe.** Állítsa a **gyorsítótárazás idúra viselkedést** **felülbírálásra,** és írja be a 4 értéket az **Órák** mezőbe.

    B. A második egyezési feltételnél állítsa `/blobcontainer1/blob1.txt` az **Egyezés feltételt** a Görbe **beállításra,** és írja be a Match **value értéket.** Állítsa a **gyorsítótárazás idúra viselkedést** **felülbírálásra,** és írja be a 2 értéket az **Órák** mezőbe.

    ![Példa a CDN egyéni gyorsítótárazására](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Az első egyéni gyorsítótárazási szabály négy órás gyorsítótár-időtartamot állít be a `/blobcontainer1` végpont által megadott forráskiszolgálón lévő mappában lévő blobfájlok esetében. A második szabály csak a `blob1.txt` blobfájl első szabályát bírálja felül, és két órás gyorsítótár-időtartamot állít be.

2. Kattintson a **Mentés** gombra.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>A Cache-Control fejlécek beállítása az Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Az Azure PowerShell](/powershell/azure/overview) az Azure-szolgáltatások felügyeletének egyik leggyorsabb és leghatékonyabb módja. A `Get-AzStorageBlob` parancsmag használatával hivatkozást kaphat a blobra, majd állítsa be a `.ICloudBlob.Properties.CacheControl` tulajdonságot. 

Példa:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> A PowerShell segítségével [is kezelheti a CDN-profilokat és végpontokat.](cdn-manage-powershell.md)
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>A Cache-Control fejlécek beállítása a .NET használatával
Blob fejlécének `Cache-Control` megadásához a .NET-kód használatával, használja az [Azure Storage Client Library for .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [CloudBlob.Properties.CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol) tulajdonság beállításához.

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
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Az [Azure Blob Storage Samples for .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)területen további .NET kódminták érhetők el.
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>A Gyorsítótár-vezérlő fejléceinek beállítása más módszerekkel

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
Az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)segítségével megtekintheti és szerkesztheti a blob tárolási erőforrásait, beleértve például a *CacheControl* tulajdonságot. 

Egy blob *CacheControl-tulajdonságának* frissítése az Azure Storage Explorer böngészővel:
   1. Jelöljön ki egy blobot, majd válassza a helyi menü **Tulajdonságok parancsát.** 
   2. Görgessen le a *CacheControl* tulajdonsághoz.
   3. Írjon be egy értéket, majd válassza a **Mentés lehetőséget.**


![Az Azure Storage Explorer tulajdonságai](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure parancssori felület
Az [Azure parancssori felület](https://docs.microsoft.com/cli/azure) (CLI) segítségével kezelheti az Azure blob erőforrásait a parancssorból. Állítsa be a cache-control fejléc, amikor feltölt egy blobot az Azure `-p` CLI,állítsa be a *cacheControl* tulajdonság a kapcsoló használatával. A következő példa bemutatja, hogyan állíthatja be a TTL-t egy órára (3600 másodperc):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Azure storage services REST API
Az Azure [storage services REST API-val](/rest/api/storageservices/) explicit módon beállíthatja az *x-ms-blob-cache-control* tulajdonságot a következő műveletek használatával egy kérésre:
  
   - [Put Blob](/rest/api/storageservices/Put-Blob)
   - [Blokklista felrakása](/rest/api/storageservices/Put-Block-List)
   - [Blob tulajdonságainak beállítása](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>A Gyorsítótár-vezérlő fejlécének tesztelése
Könnyedén ellenőrizheti a blobok TTL-beállításait. A böngésző [fejlesztői eszközeivel](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)tesztelje, `Cache-Control` hogy a blob tartalmazza-e a válaszfejlécet. A válaszfejlécek vizsgálatához használhat olyan eszközt is, mint [a Wget,](https://www.gnu.org/software/wget/) [a Postman](https://www.getpostman.com/)vagy a [Fiddler.](https://www.telerik.com/fiddler)

## <a name="next-steps"></a>Következő lépések
* [Megtudhatja, hogyan kezelheti a Felhőszolgáltatás-tartalmak lejáratát az Azure CDN-ben](cdn-manage-expiration-of-cloud-service-content.md)
* [További információ a gyorsítótárazási fogalmakról](cdn-how-caching-works.md)

