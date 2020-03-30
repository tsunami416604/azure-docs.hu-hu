---
title: Nem particionált Azure Cosmos-tárolók áttelepítése particionált tárolókba
description: Ismerje meg, hogyan telepítheti át az összes meglévő nem particionált tárolók particionált tárolókba.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 742ef62895f3ef64e8fa22ab21d2947bee57776b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623350"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Nem particionált tárolók áttelepítése particionált tárolókba

Az Azure Cosmos DB támogatja a partíciókulcs nélküli tárolók létrehozását. Jelenleg nem particionált tárolókat hozhat létre az Azure CLI és az Azure Cosmos DB SDK-k (.Net, Java, NodeJs) használatával, amelyek verziója 2.x-nél kisebb vagy azzal egyenlő. Nem particionált tárolókat nem hozhat létre az Azure Portal használatával. Az ilyen nem particionált tárolók azonban nem rugalmasak, és 20 GB rögzített tárolókapacitással és 10 K RU/s átviteli korláttal rendelkeznek.

A nem particionált tárolók örökölt, és a meglévő, nem particionált tárolók particionált tárolók a tároló és az átviteli kapacitás méretezése érdekében. Az Azure Cosmos DB egy rendszer által definiált mechanizmust biztosít a nem particionált tárolók particionált tárolókba való áttelepítéséhez. Ez a dokumentum bemutatja, hogy az összes meglévő nem particionált tárolók automatikusáttelepítése particionált tárolókba. Az automatikus áttelepítési szolgáltatás csak akkor használható, ha az SDK-k V3-as verzióját használja az összes nyelven.

> [!NOTE]
> Jelenleg nem telepítheti át az Azure Cosmos DB MongoDB és a Gremlin API-fiókokat a jelen dokumentumban leírt lépések használatával.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Tároló áttelepítése a rendszer által definiált partíciós kulccsal

Az áttelepítés támogatása érdekében az Azure Cosmos DB `/_partitionkey` egy rendszer által definiált partíciókulcsot biztosít, amely minden olyan tárolón megjelenik, amely nem rendelkezik partíciókat. A tárolók áttelepítése után nem módosíthatja a partíciókulcs-definíciót. Például egy particionált tárolóba áttelepített tároló definíciója a következő lesz:

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

A tároló áttelepítése után dokumentumokat hozhat létre a `_partitionKey` tulajdonság és a dokumentum egyéb tulajdonságainak feltöltésével. A `_partitionKey` tulajdonság a dokumentumok partíciókulcsát jelöli.

A megfelelő partíciókulcs kiválasztása fontos a kiosztott átviteli teljesítmény optimális kihasználásához. További információ: [hogyan választhat ja a partíciókulcs-cikket.](partitioning-overview.md)

> [!NOTE]
> A rendszer által definiált partíciókulcs csak akkor használható, ha az SDK-k legújabb/V3-as verzióját használja az összes nyelven.

A következő példa egy mintakódot mutat be, amely a rendszer által definiált partíciókulcssal rendelkező dokumentumot hoz létre, és olvassa el a dokumentumot:

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

A teljes minta: [a .Net minták][1] GitHub-tárház.
                      
## <a name="migrate-the-documents"></a>Dokumentumok áttelepítése

Míg a tároló definíciója egy partíciókulcs tulajdonsággal van javítva, a tárolón belüli dokumentumok nem lesznek automatikusan áttelepítve. Ami azt jelenti, `/_partitionKey` hogy a rendszerpartíció kulcstulajdonságának elérési útja nem kerül automatikusan a meglévő dokumentumokhoz. Újra kell particionálnia a meglévő dokumentumokat a partíciókulcs nélkül létrehozott `_partitionKey` dokumentumok olvasásával, és újra kell írnia őket a dokumentumokban lévő tulajdonsággal.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Partíciókulccsal nem rendelkező dokumentumok elérése

Az alkalmazások hozzáférhetnek a meglévő dokumentumokhoz, amelyek nem rendelkeznek partíciókulccsal a "PartitionKey.None" nevű speciális rendszertulajdonság használatával, ez a nem áttelepített dokumentumok értéke. Ezt a tulajdonságot az összes CRUD és lekérdezési műveletben használhatja. A következő példa egy minta beolvasásához egyetlen dokumentumot a NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

A dokumentumok újraparticionálására vonatkozó teljes minta a [.Net minták][1] GitHub-tárházban található. 

## <a name="compatibility-with-sdks"></a>Kompatibilitás az SDK-kkal

Az Azure Cosmos DB SDK-k, például a V2.x.x és a V1.x.x régebbi verziója nem támogatja a rendszer által definiált partíciókulcs-tulajdonságot. Így amikor egy régebbi SDK-ból olvassa be a tárolódefiníciót, nem tartalmaz partíciókulcs-definíciót, és ezek a tárolók pontosan a korábban fognak úgy viselkedni, mint korábban. Az SDK-k régebbi verziójával készült alkalmazások továbbra is nem particionált, mint a módosítások nélkül. 

Ha az sdk legújabb/V3-as verziója felhasználja az áttelepített tárolót, és az új dokumentumokon belül elkezdi felnépesíteni a rendszer által definiált partíciókulcsot, többé nem férhet hozzá (olvasni, frissíteni, törölni, lekérdezni) ezeket a dokumentumokat a régebbi SDK-kból.

## <a name="known-issues"></a>Ismert problémák

**A partíciókulcs nélkül v3 SDK használatával beszúrt elemek számának lekérdezése nagyobb átviteli felhasználást vonhat maga után**

Ha a V3 SDK-ból lekérdezi a V2 SDK használatával beszúrt elemeket, vagy a V3 SDK paraméterrel `PartitionKey.None` beszúrt elemeket, a count lekérdezés több RU/s-t vehet igénybe, ha a `PartitionKey.None` paraméter a FeedOptions-ben van megadva. Azt javasoljuk, hogy ne `PartitionKey.None` adja meg a paramétert, ha nincs más elem partíciókulccsal beszúrva.

Ha új elemeket szúr be a partíciókulcs különböző értékeivel, az ilyen `FeedOptions` elemek számának lekérdezése a megfelelő kulcs átadásával nem lesz probléma. Miután új dokumentumokat szúrt be partíciós kulccsal, ha csak a dokumentumszámot kell lekérdeznie a partíciókulcs értéke nélkül, a lekérdezés ismét magasabb RU/s-ot okozhat, mint a normál particionált gyűjtemények.

## <a name="next-steps"></a>További lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Azure Cosmos-fiók használata](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration