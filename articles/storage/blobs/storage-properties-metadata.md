---
title: Objektum tulajdonságai és az Azure Storage-metaadatok beállítása és lekérése |} A Microsoft Docs
description: Egyéni metaadatok Store az Azure Storage, az objektumokon, majd állítsa be, és Rendszertulajdonságok lekéréséhez.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.openlocfilehash: 86bb7e736754cbc6a93bba5fff5d8d1877b1e3b4
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916580"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Tulajdonságok és metaadatok beállítása és lekérése

Azure Storage támogatási Rendszertulajdonságok és a felhasználó által definiált metaadatok mellett az adatokat tartalmazó objektumot. Ez a cikk ismerteti a kezelését Rendszertulajdonságok és a felhasználó által definiált metaadatok a [Azure Storage ügyféloldali kódtára a .NET-hez](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **A Rendszertulajdonságok**: Rendszertulajdonságok minden egyes tárolási erőforrás található. Némelyike olvasása vagy beállítása, míg mások csak olvasható. A háttérben néhány Rendszertulajdonságok bizonyos szabványos HTTP-fejlécek felelnek meg. Az Azure Storage ügyfélkódtáraival ezeket a tulajdonságokat fenntartani az Ön számára.

* **Felhasználó által definiált metaadatok**: Egy vagy több név-érték párok, amely egy Azure Storage-erőforrást a megadott felhasználó által definiált metaadatok áll. Metaadatok segítségével tárolja az erőforrás további értékeket. Metaadatértékeket csak a saját célokat szolgálnak, és nincsenek hatással az erőforrások működését.

Tulajdonság és a metaadatok értékek tárolási erőforrás beolvasásakor két lépésből áll. Mielőtt el tudja olvasni ezeket az értékeket, meg kell explicit módon beolvassa őket meghívásával a **FetchAttributes** vagy **FetchAttributesAsync** metódust. Kivételt jelent, ha a hívás a **Exists** vagy **ExistsAsync** erőforrás metódust. Ha felhívja a következők egyikét, az Azure Storage meghívja a megfelelő **FetchAttributes** valójában metódus hívása részeként a **Exists** metódus.

> [!IMPORTANT]
> Ha azt tapasztalja, hogy vlastnost nebo metaadatok értékek tárolási erőforrás nem lett feltöltve, ellenőrizze, hogy a kód meghívja a **FetchAttributes** vagy **FetchAttributesAsync** metódust.
>
> Metaadatok név/érték párok érvényes HTTP-fejléceket, és ezért meg kell felelnie, HTTP-fejlécek vonatkozó összes korlátozás. Metaadatneveknek egyedieknek kell lenniük a HTTP-fejlécet az érvényes nevek, csak ASCII-karaktereket tartalmazhat, és kell-e kezelni a kis-és. Nem ASCII-karaktereket tartalmazó metaadatértékeket Base64-kódolású vagy URL-kódolású kell lennie.

## <a name="setting-and-retrieving-properties"></a>Beállítási és lekérési tulajdonságai
Tulajdonságértékek lekéréséhez hívja meg a **FetchAttributesAsync** metódust a blob vagy a tároló feltöltéséhez a tulajdonságait, majd olvassa el az értékeket.

Állítsa be az objektum tulajdonságait, adja meg a tulajdonság értékét, majd hívja a **SetProperties metódus** metódust.

Az alábbi példakód létrehoz egy tárolót, majd kiírja a tulajdonságértékek némelyike a konzolablakban.

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
Megadhatja egy vagy több név-érték párok blobon vagy tárolón erőforrásra vonatkozó metaadatok. Metaadatok, adja hozzá a név-érték párokat a **metaadatok** gyűjtemény az erőforráson, majd hívja meg a **SetMetadata** vagy **SetMetadataAsync** mentse az értékeket a metódust a a szolgáltatás.

> [!NOTE]
> A metaadatok nevét meg kell felelnie az elnevezési konvencióinak C#-azonosítók.
>
>

Az alábbi példakód metaadatokat tároló beállítása. Egy érték van beállítva, a gyűjtemény használatával **Hozzáadás** metódust. A többi beállítás értéke implicit kulcs/érték-szintaxis használatával. Egyaránt érvényesek.

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

Metaadatok lekérése céljából, hívja a **FetchAttributes** vagy **FetchAttributesAsync** metódust a blob vagy a tároló feltöltéséhez a **metaadatok** gyűjteményt, majd olvassa el a értékek, az alábbi példában látható módon.

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

## <a name="next-steps"></a>További lépések
* [Az Azure Storage ügyféloldali kódtára a .NET-referencia](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Az Azure Storage ügyféloldali kódtára a .NET NuGet-csomag](https://www.nuget.org/packages/WindowsAzure.Storage/)
