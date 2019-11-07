---
title: Hozzon létre nagyméretű partíciós kulccsal rendelkező Azure Cosmos-tárolókat a Azure Portal és a különböző SDK-k használatával.
description: Megtudhatja, hogyan hozhat létre tárolót Azure Cosmos DB nagyméretű partíciós kulccsal Azure Portal és különböző SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: e2ea934140322a13f07a90f4246bacd3f9dbe6c9
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721099"
---
# <a name="create-containers-with-large-partition-key"></a>Nagyméretű partíciós kulccsal rendelkező tárolók létrehozása

A Azure Cosmos DB kivonat-alapú particionálási sémát használ az adathorizontális skálázás eléréséhez. Az 3 2019. május előtt létrehozott összes Azure Cosmos-tároló kivonatoló függvényt használ, amely a partíciós kulcs első 100 bájtja alapján számítja ki a kivonatot. Ha az első 100 bájtnál több partíciós kulcs is van, akkor ezek a logikai partíciók a szolgáltatással azonos logikai partíciónak tekintendők. Ez olyan problémákhoz vezethet, mint például a partíciók méretére vonatkozó kvóta helytelen, és a partíciós kulcsok között egyedi indexeket kell alkalmazni. A probléma megoldásához nagy méretű partíciós kulcsok vannak bevezetve. A Azure Cosmos DB mostantól legfeljebb 2 KB-os értékkel támogatja a nagyméretű partíciós kulcsokat.

A nagyméretű partíciós kulcsokat a kivonatoló függvény továbbfejlesztett verziójának funkciói támogatják, amelyek egyedi kivonatot hozhatnak létre a nagyméretű partíciós kulcsokból akár 2 KB-ra. Ezt a kivonatoló verziót ajánlott olyan forgatókönyvek esetén is használni, amelyek a partíciós kulcs méretétől függetlenül nagy méretűek. A particionálási kulcs számos egyedi logikai partícióként van definiálva, például egy tárolóban lévő ~ 30000 logikai partíciók sorrendjében. Ez a cikk azt ismerteti, hogyan hozhat létre nagyméretű partíciós kulccsal rendelkező tárolót a Azure Portal és a különböző SDK-k használatával.

## <a name="create-a-large-partition-key-azure-portal"></a>Nagy partíciós kulcs létrehozása (Azure Portal)

Ha nagy méretű partíciót szeretne létrehozni, amikor a Azure Portal használatával hoz létre egy új tárolót, győződjön meg arról, hogy a **saját partíciós kulcs nagyobb, mint 100 bájtos** beállítás. Törölje a jelölőnégyzet jelölését, ha nincs szüksége nagyméretű partíciós kulcsokra, vagy ha olyan alkalmazásokkal rendelkezik, amelyek 1,18-nál régebbi SDK-verzión futnak.

![Nagyméretű partíciós kulcsok létrehozása Azure Portal használatával](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Nagy partíciós kulcs létrehozása (PowerShell)

Nagyméretű partíciós kulcsot támogató tároló létrehozásához lásd:

* [Azure Cosmos-tároló létrehozása nagyméretű partíciós kulcs méretével](manage-with-powershell.md##create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Nagyméretű partíciós kulcs (.net SDK) létrehozása

Ha nagyméretű partíciós kulccsal rendelkező tárolót szeretne létrehozni a .NET SDK használatával, akkor a `PartitionKeyDefinitionVersion.V2` tulajdonságot kell megadnia. Az alábbi példa bemutatja, hogyan adhatja meg a Version tulajdonságot a PartitionKeyDefinition objektumon belül, és beállíthatja a PartitionKeyDefinitionVersion. v2 értékre.

### <a name="v3-net-sdk"></a>v3 .NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>v2 .NET SDK

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="supported-sdk-versions"></a>Támogatott SDK-verziók

A nagyméretű partíciós kulcsokat a következő SDK-k minimális verziói támogatják:

|SDK típusa  | Minimális verzió   |
|---------|---------|
|.NET     |    1,18     |
|Java-szinkronizálás     |   2.4.0      |
|Java aszinkron   |  2.5.0        |
| REST API | a `2017-05-03` verziószáma nagyobb, mint a `x-ms-version` kérelem fejlécének használatával.|
| Resource Manager-sablon | a 2. verzió a `partitionKey` objektumon belüli `"version":2` tulajdonság használatával. |

Jelenleg nem használhat nagyméretű partíciós kulccsal rendelkező tárolókat Power BI és Azure Logic Apps. Ezekből az alkalmazásokból nagyméretű partíciós kulcs nélkül is használhat tárolókat.

## <a name="next-steps"></a>További lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
* [Adatforgalom kiépítése a tárolók és adatbázisok számára](set-throughput.md)
* [Azure Cosmos-fiók használata](account-overview.md)
