---
title: Nem particionált Azure Cosmos-tárolók áttelepíthetők particionált tárolók számára
description: Megtudhatja, hogyan telepítheti át az összes meglévő nem particionált tárolót particionált tárolóba.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 1afca920a8146ce5501900bcc9e36bdebcccca09
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706074"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Nem particionált tárolók áttelepíthetők particionált tárolók számára

Azure Cosmos DB támogatja a tárolók partíciós kulcs nélküli létrehozását. Jelenleg nem particionált tárolókat hozhat létre az Azure CLI és a Azure Cosmos DB SDK-k (.net, Java, NodeJs) használatával, amelyek verziója kisebb vagy egyenlő, mint 2. x. Nem particionált tárolók nem hozhatók létre a Azure Portal használatával. Azonban az ilyen nem particionált tárolók nem rugalmasak, és a rögzített tárolókapacitás 10 GB, a maximális átviteli sebesség pedig 10K RU/s.

A nem particionált tárolók örököltek, és a tárolók és az átviteli sebesség méretezéséhez át kell telepítenie a meglévő nem particionált tárolókat a particionált tárolók között. A Azure Cosmos DB rendszer által meghatározott mechanizmust biztosít a nem particionált tárolók particionált tárolóba való átadásához. Ez a dokumentum azt ismerteti, hogy a meglévő, nem particionált tárolók hogyan lesznek automatikusan áttelepítve a particionált tárolókban. Az automatikus áttelepítési funkciót csak akkor használhatja ki, ha az SDK-k v3-as verzióját használja az összes nyelven.

> [!NOTE]
> Jelenleg nem telepíthet át Azure Cosmos DB MongoDB és Gremlin API-fiókokat a jelen dokumentumban ismertetett lépések segítségével.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Tároló átmigrálása a rendszer által definiált partíciós kulccsal

Az áttelepítés támogatásához a Azure Cosmos DB egy `/_partitionkey` nevű rendszerpartíciós kulcsot biztosít az összes olyan tárolón, amely nem rendelkezik partíciós kulccsal. A partíciós kulcs definíciója a tárolók migrálása után nem módosítható. Egy particionált tárolóba áttelepített tároló definíciója például a következő lesz:

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

A tároló migrálása után létrehozhat dokumentumokat úgy, hogy kitölti a `_partitionKey` tulajdonságot a dokumentum többi tulajdonságával együtt. A `_partitionKey` tulajdonság a dokumentumok partíciós kulcsát jelöli.

A megfelelő partíciós kulcs kiválasztása fontos a kiépített átviteli sebesség optimális kihasználásához. További információ: [How to Choo an Partition Key](partitioning-overview.md) article.

> [!NOTE]
> Csak akkor használhatja a rendszerszintű partíciós kulcs előnyeit, ha az SDK-k legújabb vagy v3-as verzióját használja az összes nyelven.

Az alábbi példában egy mintakód látható egy olyan dokumentum létrehozásához, amely a rendszer által definiált partíciós kulccsal rendelkezik, és elolvasta a következő dokumentumot:

**A dokumentum JSON-ábrázolása**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

A teljes minta esetében tekintse meg a [.net-minták][1] GitHub-tárházát.
                      
## <a name="migrate-the-documents"></a>A dokumentumok migrálása

Amíg a tároló definíciója ki van bővítve egy Partition Key tulajdonsággal, a tárolóban található dokumentumok nem lesznek automatikusan áttelepítve. Ez azt jelenti, hogy a rendszerpartíció Key tulajdonságának `/_partitionKey` elérési útja nem adódik hozzá automatikusan a meglévő dokumentumokhoz. A meglévő dokumentumokat úgy kell újraparticionálni, hogy beolvassa azokat a dokumentumokat, amelyeket nem partíciós kulcs nélkül hoztak létre, és visszaírják őket a dokumentumok `_partitionKey` tulajdonságával.

## <a name="access-documents-that-dont-have-a-partition-key"></a>A partíciós kulccsal nem rendelkező dokumentumok elérése

Az alkalmazások a "PartitionKey. None" nevű speciális rendszertulajdonságot használva érhetik el a meglévő dokumentumokat, amelyek nem rendelkeznek partíciós kulccsal, ez a nem áttelepített dokumentumok értéke. Ezt a tulajdonságot használhatja az összes szifilisz-és lekérdezési műveletben. Az alábbi példa egy olyan mintát mutat be, amely egyetlen dokumentumot olvas be a NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

A dokumentumok újraparticionálásával kapcsolatos teljes minta a [.net-minták][1] GitHub-tárházában található. 

## <a name="compatibility-with-sdks"></a>Az SDK-k kompatibilitása

Azure Cosmos DB SDK-k (például v2. x. x és v1. x. x) régebbi verziója nem támogatja a rendszer által definiált partíciós kulcs tulajdonságot. Tehát ha egy régebbi SDK-ból olvassa be a tároló definícióját, nem tartalmaz partíciós kulcs definícióját, és ezek a tárolók ugyanúgy működnek, mint korábban. Az SDK-k régebbi verziójával létrehozott alkalmazások továbbra is a nem particionált módon működnek, mivel nem módosulnak. 

Ha egy áttelepített tárolót az SDK legújabb/v3 verziója használ, és megkezdi a rendszer által definiált partíciós kulcs feltöltését az új dokumentumokon belül, akkor a régebbi SDK-k által már nem férhet hozzá (olvasási, frissítési, törlési, lekérdezési) ilyen dokumentumokat.

## <a name="next-steps"></a>Következő lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Azure Cosmos-fiók használata](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration