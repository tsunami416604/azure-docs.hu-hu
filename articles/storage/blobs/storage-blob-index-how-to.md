---
title: BLOB-index kihasználása az Azure Blob Storage-ban tárolt adatkezeléshez és-kereséshez
description: Tekintse át, hogyan használhatja a blob-indexelési címkéket a blob-objektumok felderítéséhez, kezeléséhez és lekérdezéséhez.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: hux
ms.openlocfilehash: c8c33797fa35a64a2572026d5cb52d42c57b9b83
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466255"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Az Azure Blob Storage-ban lévő blob-indexek (előzetes verzió) használatával kezelheti és keresheti meg az adatkeresést

A blob index címkék kategorizálják az adatait a Storage-fiókban a kulcs-érték címke attribútumainak felhasználásával. Ezeket a címkéket a rendszer automatikusan indexeli, és lekérdezhető többdimenziós indexként teszi elérhetővé az adatkereséshez. Ebből a cikkből megtudhatja, hogyan állíthat be, kérhet le és kereshet meg a blob-indexek címkéi használatával.

További információ a blob indexről: az [Azure Blob Storage adatainak kezelése és keresése a blob indexével (előzetes verzió)](storage-manage-find-blobs.md).

> [!NOTE]
> A blob index nyilvános előzetes verzióban érhető el, és a **franciaországi Közép** -és **dél-franciaországi** régiókban is elérhető. Ha többet szeretne megtudni erről a szolgáltatásról, valamint az ismert problémákról és a korlátozásokról, tekintse meg [Az Azure Blob Storage a blob index (előzetes verzió) használatával történő kezelésével és keresésével](storage-manage-find-blobs.md)kapcsolatos információkat.

## <a name="prerequisites"></a>Előfeltételek
# <a name="portal"></a>[Portál](#tab/azure-portal)
- A blob index előzetes verziójához való hozzáféréshez regisztrált és jóváhagyott előfizetés
- Hozzáférés [Azure Portal](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)
Mivel a blob index nyilvános előzetes verzióban érhető el, a .NET Storage-csomag megjelenik az előzetes verziójú NuGet-hírcsatornában. Ennek a könyvtárnak a változása a mai és a hivatalos beváltási időpontra változhat. 

1. A Visual Studióban adja hozzá a `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` NuGet-csomag forrásaihoz tartozó URL-címet. 

   További információ: [Package sources](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. A NuGet csomagkezelő eszközben keresse meg az **Azure. Storage. Blobs** csomagot, és telepítse a **12.5.0-dev. 20200422.2** verziót a projektbe. A parancsot futtathatja is```Install-Package Azure.Storage.Blobs -Version 12.5.0-dev.20200422.2```

   További információt a [csomagok keresése és telepítése](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)című témakörben talál.

3. Adja hozzá a következő using utasításokat a fájl elejéhez.
```csharp
using Azure;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
```
---

## <a name="upload-a-new-blob-with-index-tags"></a>Új blob feltöltése tárgymutató-címkékkel
# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Storage-fiókját. 

2. Navigáljon a containers ( **tárolók** ) lehetőségre a **blob Service**alatt, válassza ki a tárolót

3. Kattintson a **feltöltés** gombra a feltöltés panel megnyitásához, és tallózással keresse meg a helyi fájlrendszert, és keresse meg a blokkoló blobként feltölteni kívánt fájlt.

4. Bontsa ki a **speciális** legördülő menüt, és nyissa meg a **blob-index címkék** szakaszt.

5. Adja meg az adatokra alkalmazni kívánt kulcs/érték blob-index címkéit

6. A blob feltöltéséhez kattintson a **feltöltés** gombra.

![Adatok feltöltése blob-index címkékkel](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)
Az alábbi példa bemutatja, hogyan hozhat létre egy hozzáfűzési blobot a létrehozás során létrehozott címkékkel.
```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");
         
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>BLOB-index címkék beolvasása, beállítása és frissítése
# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Storage-fiókját. 

2. Navigáljon a containers ( **tárolók** ) lehetőségre a **blob Service**alatt, válassza ki a tárolót

3. Válassza ki a kívánt blobot a Blobok listájából a kijelölt tárolóban

4. A blob áttekintése lapon megjelenik a blob tulajdonságai, beleértve a **blob-index címkéit** is

5. A blobhoz tartozó kulcs/érték indexek címkéit lekérheti, beállíthatja, módosíthatja vagy törölheti.

6. Kattintson a **Save (Mentés** ) gombra a blob frissítéseinek megerősítéséhez

![BLOB index-címkék beolvasása, beállítása, frissítése és törlése az objektumokon](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or Update Blob Index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get Blob Index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List Blobs with all options returned including Blob Index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing Blob Index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Az Adatszűrés és-keresés a blob-index címkékkel

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portalon belül a blob-index címkék szűrő automatikusan alkalmazza a `@container` paramétert a kiválasztott tároló hatókörére. Ha a teljes Storage-fiókban szeretné szűrni és megtalálni a címkézett adatait, használja a REST API, SDK-kat vagy eszközöket.

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Storage-fiókját. 

2. Navigáljon a containers ( **tárolók** ) lehetőségre a **blob Service**alatt, válassza ki a tárolót

3. Válassza a **blob index címkék szűrő** gombot a kiválasztott tárolón belüli szűréshez

4. Adja meg a blob index címke kulcsát és a címke értékét

5. A **blob index címkék szűrő** gombjának kiválasztásával további címke-szűrőket adhat hozzá (legfeljebb 10)

![Címkézett objektumok szűrése és keresése a blob-index címkékkel](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob Index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<FilterBlobItem> blobs = new List<FilterBlobItem>();
          await foreach (FilterBlobItem filterBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(filterBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Életciklus-kezelés blob-index címkékkel

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Storage-fiókját. 

2. Navigáljon az **életciklus-kezelési** lehetőségre a **blob Service** alatt

3. Válassza a *szabály hozzáadása* lehetőséget, majd töltse ki a műveleti készlet űrlap mezőket.

4. Válassza a szűrő beállítása lehetőséget a választható szűrő hozzáadásához az előtag egyeztetéséhez és a blob-index egyeztetéséhez ![ blob-index címkézése az életciklus-felügyelethez](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. Válassza a **felülvizsgálat + Hozzáadás** elemet, és tekintse át a szabály beállításai ![ életciklus-kezelési szabályt a blob index címkék szűrővel példa](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. Válassza a **Hozzáadás** lehetőséget az új szabály életciklus-kezelési házirendre való alkalmazásához

# <a name="net"></a>[.NET](#tab/net)
Az [életciklus-kezelési](storage-lifecycle-management-concepts.md) szabályzatok minden egyes Storage-fiókra vonatkoznak a vezérlési sík szintjén. A .NET esetében telepítse a [Microsoft Azure felügyeleti tár függvénytárának 16.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) vagy újabb verzióját, hogy kihasználja a blob-index egyeztetési szűrőjét egy életciklus-kezelési szabályon belül.

---

## <a name="next-steps"></a>Következő lépések

További információ a blob-indexről. Lásd: az [Azure Blob Storage-beli adatkezelés és-keresés a blob-indextel (előzetes verzió)](storage-manage-find-blobs.md )

További információ az életciklus-kezelésről. Lásd: [Az Azure Blob Storage életciklusának kezelése](storage-lifecycle-management-concepts.md)
