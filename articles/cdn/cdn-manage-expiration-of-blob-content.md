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
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: f5609f98de7ce6967dd1ff502e88d798741384df
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>Azure Blob Storage tárolóban az Azure Content Delivery Network lejáratának kezelése
> [!div class="op_single_selector"]
> * [Azure webes tartalom](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

A [Blob storage szolgáltatás](../storage/common/storage-introduction.md#blob-storage) az Azure Storage számos Azure-alapú források egyik integrálva van az Azure Content Delivery Network (CDN). Bármely nyilvánosan elérhető blobtartalom Azure CDN mindaddig, amíg az idő-live (TTL) lejárta gyorsítótárazható. Az élettartam határozza meg a `Cache-Control` fejléc a következő a HTTP-válasz a forráskiszolgálóról. Ez a cikk ismerteti, amely lehet többféle módon a `Cache-Control` az Azure Storage blob fejlécet.

Azt is meghatározhatja, Azure-portálról gyorsítótár beállításait úgy, hogy [szabályok gyorsítótárazás CDN](#setting-cache-control-headers-by-using-caching-rules). Ha Ön gyorsítótárazási szabály létrehozása, és állítsa a gyorsítótár-viselkedést **felülbírálása** vagy **gyorsítótár megkerülése**, a megadott forrás gyorsítótárazási beállítások vannak megadva ebben a cikkben ismertetett figyelmen kívül lesznek hagyva. Általános gyorsítótárazási fogalmak kapcsolatos információkért lásd: [gyorsítótárazás működése](cdn-how-caching-works.md).

> [!TIP]
> Ha szeretné, nincs TTL be blob. Ebben az esetben Azure CDN automatikusan érvényes alapértelmezett élettartam 7 nap során, kivéve, ha meg van adva az Azure portálon szabályok gyorsítótárazás. Ez az alapértelmezett élettartam csak azokra az általános webes kézbesítési optimalizálás. A nagy méretű fájlok optimalizálást az alapértelmezett élettartam egy nap, az pedig médiaadatfolyam-továbbítást optimalizálást, az alapértelmezett élettartam egy év.
> 
> Azure CDN gyorsabb hozzáférés blobok és egyéb fájlok működésével kapcsolatos további információkért lásd: [az Azure Content Delivery Network áttekintése](cdn-overview.md).
> 
> Azure Blob Storage tárolóban kapcsolatos további információkért lásd: [Blob storage bemutatása](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Gyorsítótárazás szabályok CDN használatával Cache-Control fejlécek beállítása
A beállítás egy blob előnyben részesített módszere `Cache-Control` fejléc, hogy gyorsítótárazási szabályokat használja az Azure portálon. Gyorsítótárazás szabályok CDN kapcsolatos további információkért lásd: [vezérlő Azure CDN szolgáltatás használata a szabályok gyorsítótárazással gyorsítótárazásának](cdn-caching-rules.md).

> [!NOTE] 
> Gyorsítótárazás szabályok csak esetén érhetők el **Verizon Standard Azure CDN** és **Akamai Standard Azure CDN** profilok. A **Verizon Premium Azure CDN** -profilok kell használnia a [Azure CDN szabálymotor](cdn-rules-engine.md) a a **kezelése** hasonló funkciókat a portál.

**Nyissa meg a CDN gyorsítótárazási szabályok lapot a**:

1. Az Azure portálon válassza ki a CDN-profil, majd válassza ki a blob végpontja.

2. A beállítások a bal oldali ablaktáblában jelölje ki a **szabályok gyorsítótárazás**.

   ![CDN-gyorsítótárazási szabályok gomb](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   A **szabályok gyorsítótárazás** lap jelenik meg.

   ![CDN-gyorsítótárazási lap](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**A Blob storage szolgáltatás Cache-Control fejlécek beállítása globális gyorsítótárazási szabályok használatával:**

1. A **szabályok gyorsítótárazás globális**, beállíthatja **lekérdezési karakterláncok gyorsítótárazásának működése** való **lekérdezési karakterláncok figyelmen kívül** , és **gyorsítótárazásának** való **Bírálja felül**.
      
2. A **gyorsítótár lejárati időtartam**, adja meg a 3600 a **másodperc** gépen, illetve az 1 a **óra** mezőbe. 

   ![CDN globális gyorsítótárazási szabályok – példa](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   A globális gyorsítótárszabályt beállítja a gyorsítótárazás időtartama egy óra, és hatással van a végpont minden kérelemhez. Felülbírálja a `Cache-Control` vagy `Expires` az eredeti kiszolgálóra a végpont által megadott által küldött HTTP-fejléceket.   

3. Kattintson a **Mentés** gombra.
 
**Beállítása blob fájl Cache-Control fejlécek gyorsítótárazási egyéni szabályok használatával:**

1. A **egyéni szabályok gyorsítótárazás**, hozzon létre két egyező feltételek:

     A. Az első egyező feltétel beállítása **feltételének** való **elérési** , és írja be `/blobcontainer1/*` a **más értékkel egyezik**. Állítsa be **gyorsítótárazásának** való **felülbírálása** , és adja meg a 4 a **óra** mezőbe.

    B. A második egyezés feltétel beállítása **feltételének** való **elérési** , és írja be `/blobcontainer1/blob1.txt` a **más értékkel egyezik**. Állítsa be **gyorsítótárazásának** való **felülbírálása** , és adja meg a 2 a **óra** mezőbe.

    ![CDN egyéni gyorsítótárazási szabályok – példa](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Az első egyéni gyorsítótárszabályt beállítja a gyorsítótárazás időtartama négyórás bármely blob fájlokat a `/blobcontainer1` a forrás kiszolgálón, a végpont által megadott mappában. A második szabály felülbírálja az első szabály a `blob1.txt` csak fájlját és beállítása a gyorsítótárazás időtartama két órában.

2. Kattintson a **Mentés** gombra.


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
Adjon meg egy blobba való `Cache-Control` fejlécet a .NET-kódot, használja a [Azure Storage ügyféloldali kódtára a .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) beállítása a [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) tulajdonság.

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
> Nincsenek elérhető további .NET-Kódminták [Azure Blob Storage-példák .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>A beállítás a Cache-Control fejlécek más módszerrel

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
A [Azure Tártallózó](https://azure.microsoft.com/en-us/features/storage-explorer/), megtekintheti és szerkesztheti a blob storage-erőforrások, többek között a tulajdonságokat, mint a *CacheControl* tulajdonság. 

Frissítése az *CacheControl* tulajdonság a Tártallózó alkalmazással Azure BLOB:
   1. Jelöljön ki egy blobot, majd **tulajdonságok** a helyi menüből. 
   2. Görgessen le a *CacheControl* tulajdonság.
   3. Adjon meg egy értéket, majd válasszon **mentése**.


![Az Azure Tártallózó tulajdonságai](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure parancssori felület
Az a [Azure parancssori felület](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) (CLI) kezelheti az Azure blob-erőforrások a parancssorból. Állítsa be a cache-control fejléc, ha feltölt egy blobot a az Azure parancssori felület, állítsa be a *cacheControl* tulajdonság használatával a `-p` váltani. A következő példa bemutatja, hogyan állítsa be a TTL-t egy óra (3600 másodperc):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Az Azure storage szolgáltatások REST API-n
Használhatja a [az Azure storage szolgáltatások REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) explicit módon meghatározhatja a *x-ms-blob-cache-control* tulajdonság használatával a kérelem a következő műveleteket:
  
   - [Put Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx)
   - [PUT tiltólista](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)
   - [A Blob tulajdonságainak beállítása](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## <a name="testing-the-cache-control-header"></a>A Cache-Control fejléc tesztelése
A blobok TTL beállításainak könnyen ellenőrizheti. A böngésző [fejlesztői eszközök](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), tesztelje, hogy a blob magában foglalja a `Cache-Control` válaszfejlécet. Például egy eszköz is használható [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/), vagy [Fiddler](http://www.telerik.com/fiddler) megvizsgálhatja a response fejlécekkel együtt.

## <a name="next-steps"></a>További lépések
* [Útmutató: Azure CDN felhő Felhőszolgáltatásbeli tartalom lejáratának kezelése](cdn-manage-expiration-of-cloud-service-content.md)
* [Gyorsítótárazás fogalmak megismerése](cdn-how-caching-works.md)

