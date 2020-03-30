---
title: Blob-tároló tulajdonságainak és metaadatainak kezelése a .NET használatával
titleSuffix: Azure Storage
description: Megtudhatja, hogyan állíthatja be és kérheti le a rendszer tulajdonságait, és tárolhatja az egyéni metaadatokat az Azure Storage-fiókblob-tárolókon a .NET ügyfélkódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135921"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Tárolótulajdonságok és metaadatok kezelése a .NET segítségével

A Blob-tárolók támogatják a rendszer tulajdonságait és a felhasználó által definiált metaadatokat, a benne lévő adatokon kívül. Ez a cikk bemutatja, hogyan kezelheti a rendszer tulajdonságait és a felhasználó által definiált metaadatokat az [Azure Storage ügyfélkódtárban .](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="about-properties-and-metadata"></a>Tulajdonságok és metaadatok –

- **Rendszertulajdonságok**: A rendszertulajdonságok minden Blob-tárolási erőforráson léteznek. Néhány uk lehet olvas vagy készlet, rövid idő másikak van olvas- egyetlen. A borítók alatt egyes rendszertulajdonságok megfelelnek bizonyos szabványos HTTP-fejléceknek. Az Azure Storage-ügyfélkódtár a .NET ezeket a tulajdonságokat.

- **Felhasználó által definiált metaadatok:** A felhasználó által definiált metaadatok egy vagy több név-érték párból áll, amelyeket egy Blob-tárolási erőforráshoz megadott. A metaadatok segítségével további értékeket tárolhat az erőforrással. A metaadat-értékek csak a saját céljaira szolgálnak, és nem befolyásolják az erőforrás működését.

A Blob-tárolóerőforrás tulajdonság- és metaadat-értékeinek beolvasása két lépésből áll. Mielőtt elolvasná ezeket az értékeket, explicit módon le kell kérnie őket a **FetchAttributes** vagy **fetchattributesasync** metódus hívásával. Ez alól a szabály alól kivételt képez, hogy az **Exists** és **existsAsync** metódusok meghívják a megfelelő **FetchAttributes** metódust a borítók alatt. Ha meghívja az egyik módszert, nem kell a FetchAttributes metódust is **meghívnia.**

> [!IMPORTANT]
> Ha úgy találja, hogy egy tárolóerőforrás tulajdonság- vagy metaadat-értékei nem lettek feltöltve, ellenőrizze, hogy a kód **fetchattributes** vagy **FetchAttributesAsync metódust hív-e meg.**

A metaadat-név/értékpárok érvényes HTTP-fejlécek, ezért be kell tartaniuk a HTTP-fejlécekre vonatkozó összes korlátozást. A metaadatneveknek érvényes HTTP-fejlécneveknek és érvényes C# azonosítóknak kell lenniük, csak ASCII karaktereket tartalmazhatnak, és a kis- és nagybetűk et nem figyelembe kell venni. A nem ASCII karaktereket tartalmazó metaadat-értékeknek Base64-kódolásúnak vagy URL-kódolásúnak kell lenniük.

## <a name="retrieve-container-properties"></a>Tároló tulajdonságainak beolvasása

A tárolótulajdonságok beolvasásához hívja meg az alábbi módszerek egyikét:

- [FetchAttributes (FetchAttributes)](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

A következő kódpélda lekéri a tároló rendszertulajdonságait, és néhány tulajdonságértéket ír a konzolablakba:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

Metaadatokat egy blobon vagy tárolóerőforráson egy vagy több név-érték párként adhat meg. A metaadatok beállításához adjon név-érték párokat az erőforrás **metaadat-gyűjteményéhez,** majd hívja meg az alábbi módszerek egyikét az értékek írásához:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

A metaadatok nevének meg kell felelnie a C# azonosítók elnevezési konvencióinak. A metaadatnevek megőrzik azt az esetet, amellyel létrehozták őket, de beállításkor vagy olvasáskor nem különböznek a kis- és nagybetűktől. Ha két vagy több azonos nevű metaadat-fejlécet küld el egy erőforráshoz, a Blob storage a 400-as HTTP-hibakódot (hibás kérelem) adja vissza.

A következő kód példa metaadatokat állít be egy tárolón. Az egyik érték a gyűjtemény **Hozzáadás** metódusával van beállítva. A másik érték implicit kulcs/érték szintaxissal van beállítva. Mindkettő érvényes.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

A metaadatok beolvasásához hívja meg a **FetchAttributes** vagy **FetchAttributesAsync** metódust a blobon vagy a tárolón a **metaadat-gyűjtemény** feltöltéséhez, majd olvassa el az értékeket, ahogy az az alábbi példában látható.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
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

- [Tárolótulajdonságaiművelet beszerezése](/rest/api/storageservices/get-container-properties)
- [Tároló metaadat-művelete beállítása](/rest/api/storageservices/set-container-metadata)
- [Tároló metaadat-művelete](/rest/api/storageservices/set-container-metadata)
