---
title: Blobok tulajdonságainak és metaadatainak kezelése a .NET-Azure Storage használatával
description: Ismerje meg, hogyan állíthatja be és kérheti le a rendszertulajdonságokat, és hogyan tárolhatja az egyéni metaadatokat az Azure Storage-fiókban lévő blobokon a .NET ügyféloldali kódtár használatával
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 395210c582ba7f5e8170a96a46e2816336e33b2d
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624036"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>BLOB-tulajdonságok és-metaadatok kezelése a .NET-tel

A bennük található adatokon kívül a Blobok támogatják a rendszer tulajdonságait és a felhasználó által definiált metaadatokat. Ez a cikk bemutatja, hogyan kezelheti a rendszertulajdonságokat és a felhasználó által definiált metaadatokat az [Azure Storage .net-hez készült ügyféloldali kódtár](/dotnet/api/overview/azure/storage/client)szolgáltatásával.

## <a name="about-properties-and-metadata"></a>A tulajdonságok és a metaadatok

- **Rendszertulajdonságok**: Rendszertulajdonságok találhatók az egyes blob Storage-erőforrásokon. Némelyikük olvasható vagy beállítható, míg mások csak olvashatók. A borítók alatt egyes Rendszertulajdonságok megfelelnek bizonyos szabványos HTTP-fejléceknek. Az Azure Storage .NET-hez készült ügyféloldali kódtára ezeket a tulajdonságokat tárolja.

- **Felhasználó által definiált metaadatok**: a felhasználó által definiált metaadatok egy vagy több, a blob Storage-erőforráshoz megadott név-érték párokból állnak. A metaadatok használatával további értékeket is tárolhat az erőforrással. A metaadatok értéke csak saját célra szolgál, és nem befolyásolja az erőforrás működésének módját.

A blob Storage-erőforrások metaadatainak és tulajdonságértékek beolvasása egy kétlépéses folyamat. Ezeknek az értékeknek a beolvasása előtt explicit módon be kell olvasnia azokat a `FetchAttributes` vagy `FetchAttributesAsync` metódus meghívásával. A szabály alól kivételt képez, hogy a `Exists` és a `ExistsAsync` metódus meghívja a megfelelő `FetchAttributes` módszert a borítók alatt. A módszerek egyikének meghívásakor nem kell meghívnia a `FetchAttributes`t is.

> [!IMPORTANT]
> Ha azt tapasztalja, hogy a tárolási erőforráshoz tartozó tulajdonság vagy metaadatok értéke nem lett feltöltve, ellenőrizze, hogy a kód meghívja-e a `FetchAttributes` vagy `FetchAttributesAsync` metódust.

## <a name="set-and-retrieve-properties"></a>Tulajdonságok beállítása és lekérése

A következő kódrészlet a blobon `ContentType` és `ContentLanguage` rendszertulajdonságot állítja be.

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

A blob tulajdonságainak lekéréséhez hívja meg a blob `FetchAttributes` vagy `FetchAttributesAsync` metódusát a `Properties` tulajdonság feltöltéséhez. A következő kódrészlet a blob rendszertulajdonságait jeleníti meg, és megjeleníti az egyes értékeket:

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

## <a name="set-and-retrieve-metadata"></a>Metaadatok beállítása és lekérése

A metaadatokat egy vagy több név-érték párokkal is megadhatja blob vagy tároló erőforráson. A metaadatok beállításához adja hozzá a név-érték párokat az erőforrás `Metadata` gyűjteményéhez. Ezután hívja meg az alábbi módszerek egyikét az értékek írásához:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

A metaadatok neve/értéke párok érvényes HTTP-fejlécek, és meg kell felelniük a HTTP-fejléceket szabályozó összes korlátozásnak. A metaadatok nevének érvényesnek kell lennie a HTTP C# -fejlécek és az érvényes azonosítók számára, csak ASCII-karaktereket tartalmazhat, és a kis-és nagybetűket nem megkülönböztetőként kell kezelni. [Base64-kódolás](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) vagy [URL-cím –](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) nem ASCII karaktereket tartalmazó metaadat-értékek kódolása.

A metaadatok nevének meg kell felelnie az C# azonosítók elnevezési konvencióinak. A metaadatok nevei megőrzik a létrehozásuk során használt esetet, de a kis-és nagybetűk nem különböznek a beállítás vagy a beolvasás során. Ha legalább két metaadat-fejlécet használ ugyanazzal a névvel, az Azure Blob Storage a 400-as HTTP-hibakódot (hibás kérés) adja vissza.

A következő kódrészlet a Blobok metaadatait állítja be. Egy érték van beállítva a gyűjtemény `Add` metódusának használatával. A másik érték az implicit kulcs/érték szintaxis használatával van beállítva.

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

A metaadatok lekéréséhez hívja meg az `FetchAttributes` vagy `FetchAttributesAsync` metódust a blobon vagy a tárolón a `Metadata` gyűjtemény feltöltéséhez, majd olvassa el az értékeket az alábbi példában látható módon.

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

- [BLOB tulajdonságainak beállítása művelet](/rest/api/storageservices/set-blob-properties)
- [BLOB-tulajdonságok beolvasása művelet](/rest/api/storageservices/get-blob-properties)
- [BLOB metaadat-műveletének beállítása](/rest/api/storageservices/set-blob-metadata)
- [BLOB metaadatainak beolvasása művelet](/rest/api/storageservices/get-blob-metadata)
