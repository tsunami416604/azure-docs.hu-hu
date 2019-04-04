---
title: Az Azure Blob storage-ban az Azure Content Delivery Network lejáratának kezelése |} A Microsoft Docs
description: Az Azure CDN gyorsítótárazási lévő blobok esetében szabályozni a time-to-live lehetőségek ismertetése.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: f7fc11af8cd2574271b26f7dec62072692685672
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916801"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Az Azure Blob storage-ban az Azure CDN lejáratának kezelése
> [!div class="op_single_selector"]
> * [Azure webes tartalom](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

A [tároló Blobszolgáltatás](../storage/common/storage-introduction.md#blob-storage) az Azure Storage számos Azure-alapú források közül integrálva van az Azure Content Delivery Network (CDN). Bármely nyilvánosan hozzáférhető blob tartalma Azure CDN a gyorsítótárazható, mindaddig, amíg az idő-az-élettartam (TTL) lejárta. Az élettartam határozza meg a `Cache-Control` fejlécet a HTTP-válasz a forráskiszolgálóról. Ez a cikk azt ismerteti, több módszert is, amely lehet a `Cache-Control` egy blobot az Azure Storage-fejléce.

Gyorsítótár beállításai az Azure Portalról beállítás CDN gyorsítótárazási szabályok is szabályozhatja. Ha létrehoz egy gyorsítótárszabály és beállítása a gyorsítótárazási viselkedés **felülbírálása** vagy **gyorsítótár megkerülése**, a forrás által biztosított gyorsítótárazási beállításait a cikkben leírtak szerint a rendszer figyelmen kívül hagyja. Általános gyorsítótárazási fogalmak kapcsolatos információkért lásd: [gyorsítótárazás működése](cdn-how-caching-works.md).

> [!TIP]
> Választhat, nincs élettartam beállítani egy blobot. Ebben az esetben az Azure CDN automatikusan alkalmazza a egy alapértelmezett élettartam hét nap, kivéve, ha meg van adva a gyorsítótár-szabályokkal, az Azure Portalon. Ez az alapértelmezett TTL csak azokra az általános webes kézbesítés optimalizálása. A nagyméretű fájlok optimalizálása az alapértelmezett élettartam egy nap, és az online médialejátszás optimalizálása, az alapértelmezett élettartam egy év.
> 
> Azure CDN gyorsíthatja fel a hozzáférés a blobokhoz és egyéb fájlok működésével kapcsolatos további információkért lásd: [áttekintése az Azure Content Delivery Network](cdn-overview.md).
> 
> Azure Blob storage szolgáltatással kapcsolatos további információkért lásd: [Blob storage bemutatása](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>CDN gyorsítótárazási szabályok használatával Cache-Control fejléc beállítása
Az előnyben részesített módja egy blob beállítás `Cache-Control` fejléc gyorsítótárazási szabályok használata az Azure Portalon. CDN gyorsítótárazási szabályok kapcsolatos további információkért lásd: [vezérlő Azure CDN gyorsítótárazási viselkedésének gyorsítótár-szabályokkal](cdn-caching-rules.md).

> [!NOTE] 
> Gyorsítótárazási szabályok csak esetén érhetők el **Azure CDN Standard verizon** és **Azure CDN Akamai Standard** profilok. A **verizon Azure CDN Premium** profilok kell használnia a [Azure CDN szabálymotorral](cdn-rules-engine.md) a a **kezelés** portál a hasonló funkciókat.

**Nyissa meg a CDN gyorsítótárazási szabályok lapot a**:

1. Az Azure Portalon válassza ki a CDN-profil, majd válassza ki a blob végpontja.

2. A bal oldali ablaktáblán, a Beállítások alatt válassza a **Gyorsítótárszabályok** lehetőséget.

   ![CDN gyorsítótárazási szabályok gomb](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Megjelenik a **Gyorsítótárszabályok** lap.

   ![CDN gyorsítótárazási lap](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**A Blob storage szolgáltatás Cache-Control fejléc beállítása a globális gyorsítótárazási szabályokat használatával:**

1. Alatt **globális gyorsítótárszabályok**állítsa be **lekérdezési karakterláncok gyorsítótárazásának működése** való **lekérdezési karakterláncok kihagyása** és **gyorsítótárazási viselkedésének** , **Felülbírálás**.
      
2. A **gyorsítótár elévülési ideje**, adja meg a 3600 a **másodperc** mezőbe, vagy az 1 a **óra** mezőbe. 

   ![CDN globális gyorsítótárazási szabályokat példa](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   A globális gyorsítótárazási szabály beállítja egy egy órás gyorsítótárazás időtartama, és hatással van az összes kérelem a végponthoz. Ez a beállítás felülbírálja bármely `Cache-Control` vagy `Expires` a végpont által megadott a forráskiszolgáló által küldött HTTP-fejléceket.   

3. Kattintson a **Mentés** gombra.
 
**Beállítása egy blob fájl Cache-Control fejléceket egyéni gyorsítótárazási szabályok használatával:**

1. A **egyéni gyorsítótárszabályok**, hozzon létre két egyezési feltételei:

     A. Az első egyezés feltétel beállítása **feltételnek megfelelő** való **elérési útja** , és adja meg `/blobcontainer1/*` a **érték egyezik**. Állítsa be **gyorsítótárazási viselkedésének** való **felülbírálása** , és adja meg a 4 a **óra** mezőbe.

    B. Állítsa be a második feltétel egyezik **feltételnek megfelelő** való **elérési** , és adja meg `/blobcontainer1/blob1.txt` a **érték egyezik**. Állítsa be **gyorsítótárazási viselkedésének** való **felülbírálása** , és adja meg a 2 a **óra** mezőbe.

    ![CDN-t egyéni gyorsítótárazási szabályok példa](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Az első egyéni gyorsítótárszabály beállítja a gyorsítótárazás időtartama négyórás bármely blob fájlokat a `/blobcontainer1` a forráskiszolgálón, a végpont által megadott mappába. A második szabály felülírja az első szabály az `blob1.txt` csak blob fájl és a egy két órás, a gyorsítótárazás idejének beállítása.

2. Kattintson a **Mentés** gombra.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>A Cache-Control fejléc beállítása az Azure PowerShell-lel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Az Azure PowerShell](/powershell/azure/overview) egyik felügyelheti az Azure-szolgáltatások a leggyorsabb és leghatékonyabb módja. Használja a `Get-AzStorageBlob` -parancsmaggal beolvasható egy hivatkozást a blob, majd állítsa be a `.ICloudBlob.Properties.CacheControl` tulajdonság. 

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
> Is használhatja a PowerShell-lel [a CDN-profilok és a végpontok kezeléséhez](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>A Cache-Control fejléc beállítása a .NET-keretrendszerrel
Egy blob megadása `Cache-Control` fejléc használatával a .NET-kód, használja a [Azure Storage ügyféloldali kódtára a .NET-hez](../storage/blobs/storage-dotnet-how-to-use-blobs.md) beállítása a [CloudBlob.Properties.CacheControl](/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol#Microsoft_WindowsAzure_Storage_Blob_BlobProperties_CacheControl) tulajdonság.

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
> Nincsenek elérhető további .NET platformra írt kódmintái [Azure Blob Storage-mintákat a .NET-hez](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>A beállítás Cache-Control fejléceket egyéb módszerek használatával

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
A [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), megtekintheti és szerkesztheti a blob storage-erőforrások, többek között például a tulajdonságok a *CacheControl* tulajdonság. 

Frissítése az *CacheControl* egy blobot az Azure Storage Explorerrel tulajdonságát:
   1. Jelöljön ki egy blobot, majd **tulajdonságok** a helyi menüből. 
   2. Görgessen le a *CacheControl* tulajdonság.
   3. Adjon meg egy értéket, majd válassza ki **mentése**.


![Az Azure Storage Explorer tulajdonságai](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure parancssori felület
Az a [Azure parancssori felület](https://docs.microsoft.com/cli/azure) (CLI), az Azure blob-erőforrások is kezelhetők a parancssorból. A cache-control fejléc beállítása, ha feltölt egy blobot az Azure CLI-vel, állítsa be a *cacheControl* tulajdonság használatával a `-p` váltani. Az alábbi példa bemutatja, hogyan állítható be az élettartam egy óra (3600 másodperc):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Az Azure storage szolgáltatásaihoz való REST API-val
Használhatja a [Azure storage szolgáltatásaihoz való REST API-val](/rest/api/storageservices/) beállításának a *x-ms-blob-cache-control* tulajdonság használatával a kérelem a következő műveleteket:
  
   - [Put Blob](/rest/api/storageservices/Put-Blob)
   - [PUT tiltólista](/rest/api/storageservices/Put-Block-List)
   - [A Blob tulajdonságainak megadása](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>A Cache-Control fejléc tesztelése
Élettartam beállításait, a blobokat egyszerűen ellenőrizheti. A böngésző [fejlesztői eszközök](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), tesztelje, hogy a blob magában foglalja a `Cache-Control` válaszfejléc. Például egy eszköz is használható [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/), vagy [Fiddler](https://www.telerik.com/fiddler) a válaszfejlécek vizsgálatához.

## <a name="next-steps"></a>További lépések
* [Útmutató: Azure CDN-ben a Felhőszolgáltatásbeli tartalom lejáratának kezelése](cdn-manage-expiration-of-cloud-service-content.md)
* [Gyorsítótárazás fogalmak ismertetése](cdn-how-caching-works.md)

