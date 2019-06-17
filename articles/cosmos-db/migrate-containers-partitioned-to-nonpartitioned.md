---
title: Az Azure Cosmos DB-tárolók nem particionált particionált tárolók áttelepítése
description: Ismerje meg, hogyan telepítheti át az összes meglévő nem particionált tárolót particionált tárolókba.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 8ba9489496a8f9e3703702e344684b4028a002cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241924"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>A particionált tárolók nem particionált tárolók áttelepítése

Az Azure Cosmos DB támogatja a nem tartozik partíciós kulcs létrehozása tárolókat. Nem particionált tárolók jelenleg Azure CLI-vel és az Azure Cosmos DB SDK-k (.Net, Java, NodeJs) verziójú kisebb vagy egyenlő, mint a 2.x használatával hozhat létre. Nem hozható létre nem particionált tárolók az Azure portal használatával. Az ilyen nem particionált tárolók azonban nem rugalmas, és javította a storage kapacitása 10 GB-os és az átviteli sebesség legfeljebb 10 ezer Kérelemegység/s.

A nem particionált tárolók örökölt és a meglévő nem particionált tárolók át kell telepítenie a particionált tárolók méretezési dokumentumtárolási és adattovábbítási kapacitással. Az Azure Cosmos DB lehetővé teszi a rendszer által meghatározott a nem particionált tárolók áttelepítéséhez a particionált tárolók. Ez a dokumentum azt ismerteti, hogyan az összes meglévő nem particionált tárolót automatikus telepítse át a particionált tárolók. Csak akkor, ha az SDK-k v3-as verzióját az összes nyelven használ, a automatikus áttelepítési funkciót is igénybe vehet.

> [!NOTE] 
> Azure Cosmos DB mongodb-hez és a Gremlin API-fiókok jelenleg nem tudja áttelepíteni a jelen dokumentumban ismertetett lépésekkel. 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>A rendszer által meghatározott partíciókulccsal tároló áttelepítése

Támogatja az áttelepítést, az Azure Cosmos DB nevű rendszer által definiált partíciókulcs meghatározása `/_partitionkey` meg az összes tárolót, amelyek nem rendelkeznek egy partíciókulcsot. A partíciós kulcs definíciójában nem módosítható, miután a tároló áttelepítése. Például egy tároló, amely egy particionált tárolóba való áttelepítése meghatározása a következők: 

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
 
Miután a tároló áttelepítése, létrehozhat dokumentumok feltöltése a `_partitionKey` tulajdonsággal együtt a dokumentumot más egyéb tulajdonságait. A `_partitionKey` tulajdonság jelöli a partíciókulcs a dokumentumok. 

A megfelelő partíciókulcs kiválasztása fontos a kiosztott átviteli sebesség optimális használatához. További információkért lásd: [partíciókulcs kiválasztása](partitioning-overview.md) cikk. 

> [!NOTE]
> Használja ki a rendszer meghatározott partíciókulcs is igénybe vehet, csak akkor, ha az SDK-k legújabb/v3-as verzióját az összes nyelven használják.

Az alábbi példa bemutatja a rendszer a partíciókulccsal rendelkező dokumentum létrehozásához, és olvassa el a dokumentumot egy mintakód:

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

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

A teljes minta: a [.Net-minták](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) GitHub-adattárban. 
                      
## <a name="migrate-the-documents"></a>A dokumentumok migrálása

A tároló-definíció partíciós kulcs tulajdonsággal bővült, míg a tárolóban lévő dokumentumokon nem automatikus át. Ami azt jelenti, hogy a rendszer partíciós kulcs tulajdonságát `/_partitionKey` elérési út nem automatikusan adja hozzá a meglévő dokumentumok. A meglévő dokumentumok újraparticionálni tudnivalóinak, amelyek egy partíciókulcsot nélkül lettek létrehozva és újraírási azokat vissza a szükséges `_partitionKey` tulajdonság a-dokumentumokban. 

## <a name="access-documents-that-dont-have-a-partition-key"></a>A partíciós kulcs nem rendelkező dokumentumokhoz

Ez az érték a nem áttelepített dokumentumok, alkalmazások érheti el a meglévő dokumentumok, amelyek nem rendelkeznek a partíciós kulcs "CosmosContainerSettings.NonePartitionKeyValue" nevű speciális rendszer tulajdonság használatával. A CRUD-MŰVELETEKKEL és lekérdezési műveleteket is használhatja ezt a tulajdonságot. Az alábbi példa megmutatja a NonePartitionKey egyetlen dokumentum olvasni. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

A dokumentumok újraparticionálása a teljes minta: a [.Net-minták](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) GitHub-adattárban. 

## <a name="compatibility-with-sdks"></a>SDK-kkal való kompatibilitás érdekében

Azure Cosmos DB SDK például V2.x.x és V1.x.x régebbi verzióját nem támogatja a rendszer által meghatározott partíciós kulcs tulajdonságát. Tehát a tároló-definíció egy régebbi SDK-t, olvassa el, ha bármely partíciós kulcs definíciójában nem tartalmaz, és ezek a tárolók fog változni pontosan. Az SDK-k régebbi verziójával készített alkalmazások továbbra is működni a nem particionált, módosítás nélkül. 

Egy áttelepített tárolót használja fel az SDK legújabb/v3-as verzióját, és indítása a rendszer által definiált partíciókulcs belül az új dokumentumok feltöltése, nem lehet hozzáférni (olvasás, update, delete, lekérdezés) ezek a dokumentumok a régebbi SDK többé.

## <a name="next-steps"></a>További lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB-kérésegységeiről](request-units.md)
* [A tárolók és adatbázisok kiépítése átviteli](set-throughput.md)
* [Az Azure Cosmos-fiók használata](account-overview.md)