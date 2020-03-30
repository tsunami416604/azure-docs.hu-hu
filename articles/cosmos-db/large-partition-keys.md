---
title: Azure Cosmos-tárolók létrehozása nagy partíciós kulccsal
description: Megtudhatja, hogyan hozhat létre egy tárolót az Azure Cosmos DB nagy partíciókulccsal az Azure Portal on és a különböző SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7184a6b85e93c41dfe914813301a4b1a0c88f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887682"
---
# <a name="create-containers-with-large-partition-key"></a>Tárolók létrehozása nagy partíciós kulccsal

Az Azure Cosmos DB kivonatalapú particionálási sémát használ az adatok horizontális méretezése érdekében. 2019. május 3-a előtt létrehozott összes Azure Cosmos-tároló egy kivonatoló függvényt használ, amely a partíciókulcs első 100 bájtja alapján számítja ki a kivonatot. Ha több partíciókulcs azonos első 100 bájttal rendelkezik, akkor ezeket a logikai partíciókat a szolgáltatás ugyanaznak a logikai partíciónak tekinti. Ez azt eredményezheti, hogy a partícióméret-kvóta helytelen problémákat, és egyedi indexeket alkalmaz a partíciókulcsokközött. A probléma megoldásához nagy partíciókulcsok kerülnek bevezetésre. Az Azure Cosmos DB mostantól támogatja a 2 KB-ig növelt értékkel rendelkező nagy partíciókulcsokat.

A nagy partíciókulcsokat a kivonatoló függvény továbbfejlesztett verziójának funkciói támogatják, amely akár 2 KB-os nagy partíciókulcsokból is képes egyedi kivonatot generálni. Ez a kivonatverzió magas partíciókulcs-számosságú forgatókönyvekhez is ajánlott, függetlenül a partíciókulcs méretétől. A partíciókulcs számossága az egyedi logikai partíciók száma, például ~30000 logikai partíciók sorrendjében egy tárolóban. Ez a cikk ismerteti, hogyan hozhat létre egy tárolót egy nagy partíciós kulccsal az Azure Portalon és a különböző SDK-k használatával.

## <a name="create-a-large-partition-key-azure-portal"></a>Nagy partíciókulcs létrehozása (Azure Portal)

Nagy partíciókulcs létrehozásához, amikor új tárolót hoz létre az Azure Portalon, ellenőrizze, hogy a **Partíciókulcs nagyobb, mint 100 bájt.** Törölje a jelölőnégyzet jelölését, ha nincs szüksége nagy partíciókulcsokra, vagy ha az SDK-k 1,18-nál régebbi verzióján futó alkalmazások vannak.

![Nagy partíciókulcsok létrehozása az Azure Portal használatával](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Nagy partíciókulcs létrehozása (PowerShell)

Nagy partíciókulcs-támogatással rendelkező tároló létrehozásához lásd:

* [Hozzon létre egy Azure Cosmos-tárolót nagy partíciókulcs-mérettel](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Nagy partíciókulcs létrehozása (.Net SDK)

Ha nagy partíciós kulccsal szeretne tárolót létrehozni `PartitionKeyDefinitionVersion.V2` a .NET SDK használatával, adja meg a tulajdonságot. The following example shows how to specify the Version property within the PartitionKeyDefinition object and set it to PartitionKeyDefinitionVersion.V2.

### <a name="v3-net-sdk"></a>3.NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>2.NET SDK

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

A nagy partíciókulcsok at az SDK-k alábbi minimális verziói támogatják:

|SDK-típus  | Minimális verzió   |
|---------|---------|
|.Net     |    1.18     |
|Java-szinkronizálás     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | magasabb verzióval, `2017-05-03` `x-ms-version` mint a kérelemfejléc használatával.|
| Resource Manager-sablon | az `partitionKey` objektumon `"version":2` belüli tulajdonság használatával. |

Jelenleg nem használhatja a power BI-ban és az Azure Logic Apps-ben nagy partíciókat tartalmazó tárolókat. Ezekből az alkalmazásokból nagy partíciókulcs nélküli tárolókat is használhat.

## <a name="next-steps"></a>További lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Azure Cosmos-fiók használata](account-overview.md)
