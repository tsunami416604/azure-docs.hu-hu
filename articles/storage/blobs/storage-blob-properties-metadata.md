---
title: Blob tulajdonságainak és metaadatainak kezelése a .NET használatával – Azure Storage
description: Megtudhatja, hogyan állíthatja be és kérheti le a rendszertulajdonságait, és hogyan tárolhatja az egyéni metaadatokat az Azure Storage-fiókblobokon a .NET ügyfélkódtár használatával.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137662"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Blob-tulajdonságok és metaadatok kezelése a .NET segítségével

A benne lévő adatok mellett a blobok támogatják a rendszer tulajdonságait és a felhasználó által definiált metaadatokat. Ez a cikk bemutatja, hogyan kezelheti a rendszer tulajdonságait és a felhasználó által definiált metaadatokat az [Azure Storage ügyfélkódtárban .](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="about-properties-and-metadata"></a>Tulajdonságok és metaadatok –

- **Rendszertulajdonságok**: A rendszertulajdonságok minden Blob-tárolási erőforráson léteznek. Néhány uk lehet olvas vagy készlet, rövid idő másikak van olvas- egyetlen. A borítók alatt egyes rendszertulajdonságok megfelelnek bizonyos szabványos HTTP-fejléceknek. Az Azure Storage-ügyfélkódtár a .NET ezeket a tulajdonságokat.

- **Felhasználó által definiált metaadatok:** A felhasználó által definiált metaadatok egy vagy több név-érték párból áll, amelyeket egy Blob-tárolási erőforráshoz megadott. A metaadatok segítségével további értékeket tárolhat az erőforrással. A metaadat-értékek csak a saját céljaira szolgálnak, és nem befolyásolják az erőforrás működését.

A Blob-tárolóerőforrások metaadat- és tulajdonságértékeinek beolvasása két lépésből áll. Mielőtt elolvasná ezeket az értékeket, explicit `FetchAttributes` `FetchAttributesAsync` módon le kell kérnie őket a vagy metódus hívásával. Ez alól a szabály `Exists` alól `ExistsAsync` kivételt `FetchAttributes` képez, hogy a módszerek a megfelelő módszert hívják a takaró alatt. Ha meghívja az egyik módszert, nem kell `FetchAttributes`a .

> [!IMPORTANT]
> Ha úgy találja, hogy egy tárolóerőforrás tulajdonság- vagy metaadat-értékei `FetchAttributes` `FetchAttributesAsync` nem lettek feltöltve, ellenőrizze, hogy a kód meghívja-e a vagy metódust.

## <a name="set-and-retrieve-properties"></a>Tulajdonságok beállítása és beolvasása

A következő kód `ContentType` példa `ContentLanguage` beállítja a és a rendszer tulajdonságait egy blob.

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Blob-tulajdonságok beolvasásához `FetchAttributes` `FetchAttributesAsync` hívja meg a blobon `Properties` lévő metódust a tulajdonság feltöltéséhez. A következő kódpélda leveszi a blob rendszertulajdonságait, és megjeleníti az értékek egy részét:

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

## <a name="set-and-retrieve-metadata"></a>Metaadatok beállítása és beolvasása

Metaadatokat egy blobon vagy tárolóerőforráson egy vagy több név-érték párként adhat meg. A metaadatok beállításához adjon név-érték `Metadata` párokat az erőforrás gyűjteményéhez. Ezután hívja meg az alábbi módszerek egyikét az értékek írásához:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

A metaadat-név/értékpárok érvényes HTTP-fejlécek, és be kell tartaniuk a HTTP-fejlécekre vonatkozó összes korlátozást. A metaadatneveknek érvényes HTTP-fejlécneveknek és érvényes C# azonosítóknak kell lenniük, csak ASCII karaktereket tartalmazhatnak, és a kis- és nagybetűk et nem figyelembe kell venni. [Base64-enkódolja](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) vagy [URL-enkódolja](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) a nem ASCII karaktereket tartalmazó metaadat-értékeket.

A metaadatok nevének meg kell felelnie a C# azonosítók elnevezési konvencióinak. A metaadatnevek fenntartják a létrehozásukkor használt kis- és nagybetűket, de beállításkor vagy olvasáskor nem különböznek a kis- és nagybetűktől. Ha két vagy több, azonos nevű metaadat-fejlécet küld el egy erőforráshoz, az Azure Blob storage a 400-as HTTP-hibakódot (hibás kérelem) adja vissza.

A következő kód példa metaadatokat állít be egy blobon. Egy érték van beállítva `Add` a gyűjtemény metódusával. A másik érték implicit kulcs/érték szintaxissal van beállítva.

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

A metaadatok beolvasásához hívja meg a `FetchAttributes` blobon vagy a tárolón lévő metódust `FetchAttributesAsync` a `Metadata` gyűjtemény feltöltéséhez, majd olvassa el az értékeket, ahogy az az alábbi példában látható.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Lásd még

- [Blob tulajdonságainak beállítása művelet](/rest/api/storageservices/set-blob-properties)
- [Blob tulajdonságainak beszereznie művelet](/rest/api/storageservices/get-blob-properties)
- [Blob metaadat-műveletbeállítása](/rest/api/storageservices/set-blob-metadata)
- [Blob metaadat-műveletbeka](/rest/api/storageservices/get-blob-metadata)
