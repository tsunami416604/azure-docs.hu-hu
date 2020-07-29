---
title: Blobok tulajdonságainak és metaadatainak kezelése a .NET-Azure Storage használatával
description: Ismerje meg, hogyan állíthatja be és kérheti le a rendszertulajdonságokat, és hogyan tárolhatja az egyéni metaadatokat az Azure Storage-fiókban lévő blobokon a .NET ügyféloldali kódtár használatával
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 3d86b6e39d6199d2f0268070cfa5456e512daa49
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465881"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>BLOB-tulajdonságok és-metaadatok kezelése a .NET-tel

A bennük található adatokon kívül a Blobok támogatják a rendszer tulajdonságait és a felhasználó által definiált metaadatokat. Ez a cikk bemutatja, hogyan kezelheti a rendszertulajdonságokat és a felhasználó által definiált metaadatokat az [Azure Storage .net-hez készült ügyféloldali kódtár](/dotnet/api/overview/azure/storage?view=azure-dotnet)szolgáltatásával.

## <a name="about-properties-and-metadata"></a>A tulajdonságok és a metaadatok

- **Rendszertulajdonságok**: Rendszertulajdonságok találhatók az egyes blob Storage-erőforrásokon. Némelyikük olvasható vagy beállítható, míg mások csak olvashatók. A borítók alatt egyes Rendszertulajdonságok megfelelnek bizonyos szabványos HTTP-fejléceknek. Az Azure Storage .NET-hez készült ügyféloldali kódtára ezeket a tulajdonságokat tárolja.

- **Felhasználó által definiált metaadatok**: a felhasználó által definiált metaadatok egy vagy több, a blob Storage-erőforráshoz megadott név-érték párokból állnak. A metaadatok használatával további értékeket is tárolhat az erőforrással. A metaadatok értéke csak saját célra szolgál, és nem befolyásolja az erőforrás működésének módját.

> [!NOTE]
> A blob index címkéi lehetővé teszik a felhasználó által definiált kulcs/érték attribútumoknak a blob Storage-erőforrással együtt történő tárolását is. A metaadatokhoz hasonlóan csak a blob-indexek indexelése automatikusan történik, és a natív blob szolgáltatás lekérdezi őket. A metaadatokat nem lehet natív módon indexelni és lekérdezni, hacsak nem használ külön szolgáltatást, például Azure Search.
>
> A szolgáltatással kapcsolatos további információkért lásd: az [Azure Blob Storage adatainak kezelése és keresése a blob-indextel (előzetes verzió)](storage-manage-find-blobs.md).

A blob Storage-erőforrások metaadatainak és tulajdonságértékek beolvasása egy kétlépéses folyamat. Ezeknek az értékeknek a beolvasása előtt explicit módon be kell olvasnia azokat a vagy metódus meghívásával `FetchAttributes` `FetchAttributesAsync` . A szabály alól kivételt képez, hogy a `Exists` és a `ExistsAsync` metódusok a megfelelő módszert hívják meg `FetchAttributes` a borítók alatt. Ha meghívja az egyik módszert, nem kell meghívnia a következőt: `FetchAttributes` .

> [!IMPORTANT]
> Ha azt tapasztalja, hogy a tárolási erőforráshoz tartozó tulajdonság vagy metaadatok értéke nem lett feltöltve, ellenőrizze, hogy a kód meghívja-e a `FetchAttributes` vagy a `FetchAttributesAsync` metódust.

## <a name="set-and-retrieve-properties"></a>Tulajdonságok beállítása és lekérése

A következő kódrészlet a és a `ContentType` `ContentLanguage` rendszer tulajdonságait állítja be egy blobon.

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

A blob tulajdonságainak lekéréséhez hívja meg a (z) `FetchAttributes` vagy `FetchAttributesAsync` metódust a blobon a tulajdonság feltöltéséhez `Properties` . A következő kódrészlet a blob rendszertulajdonságait jeleníti meg, és megjeleníti az egyes értékeket:

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

A metaadatokat egy vagy több név-érték párokkal is megadhatja blob vagy tároló erőforráson. A metaadatok beállításához adja hozzá a név-érték párokat az `Metadata` erőforrás gyűjteményéhez. Ezután hívja meg az alábbi módszerek egyikét az értékek írásához:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

A metaadatok neve/értéke párok érvényes HTTP-fejlécek, és meg kell felelniük a HTTP-fejléceket szabályozó összes korlátozásnak. A metaadatok nevének érvényes HTTP-fejléc-névnek és érvényes C#-azonosítónak kell lennie, csak ASCII-karaktereket tartalmazhat, és a kis-és nagybetűket nem megkülönböztetőként kell kezelni. [Base64-kódolás](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) vagy [URL-cím –](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) nem ASCII karaktereket tartalmazó metaadat-értékek kódolása.

A metaadatok nevének meg kell felelnie a C# azonosítók elnevezési konvencióinak. A metaadatok nevei megőrzik a létrehozásuk során használt esetet, de a kis-és nagybetűk nem különböznek a beállítás vagy a beolvasás során. Ha legalább két metaadat-fejlécet használ ugyanazzal a névvel, az Azure Blob Storage a 400-as HTTP-hibakódot (hibás kérés) adja vissza.

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

A metaadatok lekéréséhez hívja `FetchAttributes` meg a vagy a `FetchAttributesAsync` metódust a blobon vagy a tárolón a gyűjtemény feltöltéséhez `Metadata` , majd olvassa el az értékeket az alábbi példában látható módon.

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
