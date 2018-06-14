---
title: Az objektum tulajdonságai és az Azure Storage metaadatok beolvasása és beállítása |} Microsoft Docs
description: Egyéni metaadat tárolásához az Azure Storage-objektumokhoz, és állítsa be, és Rendszertulajdonságok lekéréséhez.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: a3eb598b2dabd4986c72b8814926eb0944707050
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873029"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Tulajdonságok és metaadatok beállítása és lekérése

Az objektumok Azure Storage támogatási rendszer tulajdonságok felhasználó által definiált metaadatok, hogy milyen adatokat tartalmaznak mellett. Ez a cikk ismerteti, amelyek kezelése rendszer tulajdonságai és a felhasználó által definiált metaadatok a [Azure Storage ügyféloldali kódtára a .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **A Rendszertulajdonságok**: Rendszertulajdonságok egyes tárolási erőforrások léteznek. Némelyikük olvasása vagy beállítása, míg mások csak olvasható. A színfalak néhány Rendszertulajdonságok megfelelnek bizonyos szabványos HTTP-fejlécek. Az Azure storage ügyféloldali kódtár megőrzi ezeket Önnek.

* **Felhasználó által definiált metaadatok**: felhasználói metaadatai metaadatokat, amelyek egy adott erőforráshoz név-érték pár formájában ad meg. Metaadatok segítségével tárolja a tároló egyik erőforrásához további értékeket. Ezek további metaadatokat az értékek csak a saját célokra, és nincsenek hatással az erőforrás működését.

A tároló egyik erőforrásához tulajdonság és metaadatok értékeinek beolvasásakor két lépésből áll. Olvasása előtt hasznos lehet ezeket az értékeket, akkor kell explicit módon fetch őket meghívásával a **FetchAttributesAsync** metódust.

> [!IMPORTANT]
> A tároló egyik erőforrásához tulajdonság és a metaadat értéke nincs feltöltve, kivéve, ha meghívja a egyikét a **FetchAttributesAsync** módszerek.
>
> Kapni fog egy `400 Bad Request` , ha a név/érték párok nem ASCII-karaktereket tartalmaznak. Metaadatok név/érték párok érvényes HTTP-fejlécek, és ezért meg kell felelnie minden HTTP-fejlécek vonatkozó korlátozások. Ezért ajánlott URL-kódolást vagy a Base64 kódolás neveit és értékeit tartalmazó, nem ASCII-karaktereket használja.
>

## <a name="setting-and-retrieving-properties"></a>És tulajdonságainak beolvasása
Tulajdonságértékei olvashatók, hívja meg a **FetchAttributesAsync** módszer a blob vagy a tároló a tulajdonságok, olvassa el az értékeket.

Az objektum tulajdonságainak beállításához adja meg a tulajdonság értékét, majd hívja a **SetProperties metódus** metódust.

Az alábbi példakód létrehoz egy tárolót, majd a konzolablakban a tulajdonságértékek némelyike ír.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>És metaadatok beolvasása
Egy vagy több név-érték párként blob vagy tároló erőforrásra vonatkozó metaadatok is megadhat. Metaadatok beállításához vegye fel a név-érték párok a **metaadatok** az erőforrás-, gyűjtemény, majd hívja a **SetMetadata** metódus értékek mentéséhez a szolgáltatást.

> [!NOTE]
> A metaadatok nevét meg kell felelnie a C# azonosítók elnevezési szabályai.
>
>

Az alábbi példakód egy tároló metaadatainak beállítása. Egy érték van beállítva, a gyűjtemény használata **Hozzáadás** metódust. A többi beállítás értéke implicit kulcs/érték-szintaxis használatával. Egyaránt érvényesek.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Metaadatok lekérése céljából hívható meg a **FetchAttributes** módszer a blob vagy a tároló feltöltése a **metaadatok** gyűjtemény, majd olvassa el az értékeket az alábbi példában látható módon.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
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
```

## <a name="next-steps"></a>Következő lépések
* [Az Azure Storage ügyféloldali kódtára a .NET-referencia](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Az Azure Storage ügyféloldali kódtára a .NET NuGet-csomag](https://www.nuget.org/packages/WindowsAzure.Storage/)
