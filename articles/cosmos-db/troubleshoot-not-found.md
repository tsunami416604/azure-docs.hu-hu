---
title: A Azure Cosmos DB nem található kivételek megoldása
description: A nem található kivétel diagnosztizálása és javítása
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294387"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>Nem található Azure Cosmos DB diagnosztizálása és megoldása
Az 404-es HTTP-állapotkód azt jelenti, hogy az erőforrás már nem létezik.

## <a name="expected-behavior"></a>Várt viselkedés
Sok olyan érvényes forgatókönyv létezik, amelyben egy alkalmazás 404-re vár, és megfelelően kezeli a forgatókönyvet.

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>A rendszer nem talált visszaadott értéket a létező vagy létező elemek esetében
Az alábbiakban látható a 404-as állapotkód visszaadásának lehetséges oka, ha az adott elemmel vagy kilép.

### <a name="1-race-condition"></a>1. versenyhelyzet
Több SDK-ügyfél példánya van, és az írás előtt történt az olvasás.

#### <a name="solution"></a>Megoldás:
1. Cosmos DB esetén az alapértelmezett fiók konzisztenciája a munkamenet konzisztenciája. Egy elem létrehozásakor vagy frissítésekor a válasz egy munkamenet-tokent ad vissza, amely az SDK-példányok között átadható, így garantálható, hogy az olvasási kérelem egy adott módosítással rendelkező replikáról olvas.
2. A [konzisztencia szintjének](consistency-levels-choosing.md) módosítása [erősebb szintre](consistency-levels-tradeoffs.md)

### <a name="2-invalid-partition-key-and-id-combination"></a>2. érvénytelen a partíciós kulcs és az azonosító kombinációja
A partíciós kulcs és az azonosító kombinációja érvénytelen.

#### <a name="solution"></a>Megoldás:
Javítsa ki a helytelen kombinációt okozó alkalmazás-logikát. 

### <a name="3-invalid-character-in-item-id"></a>3. Érvénytelen karakter az AZONOSÍTÓban
A rendszer beszúr egy elemeket Cosmos DBba, és az AZONOSÍTÓban [érvénytelen karakter](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) szerepel.

#### <a name="solution"></a>Megoldás:
Javasoljuk, hogy a felhasználók az azonosítót egy másik értékre módosítsa, amely nem tartalmazza a speciális karaktereket. Ha megváltoztatja az azonosítót, az azonosító Base64 kódolásával elkerülheti a speciális karaktereket.

A tárolóba már beszúrt elemek a név alapú hivatkozások helyett a RID-értékek használatával cserélhetők le.
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4. élettartam (TTL) kiürítése
Az objektum élettartama [(TTL)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) tulajdonsága be lett állítva. Az objektum törölve lett, mert az élettartama lejárt.

#### <a name="solution"></a>Megoldás:
Módosítsa az élettartamot, hogy megakadályozza az elem kiürítését.

### <a name="5-lazy-indexing"></a>5. lusta indexelés
Nem történt meg a [lusta indexelés](index-policy.md#indexing-mode) .

#### <a name="solution"></a>Megoldás:
Várjon, amíg az indexelés felveszi vagy megváltoztatja az indexelési házirendet

### <a name="6-parent-resource-deleted"></a>6. szülő erőforrás törölve
A rendszer törölte az adatbázist és/vagy tárolót, amelyben az eleme található.

#### <a name="solution"></a>Megoldás:
1. [Állítsa vissza](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) a fölérendelt erőforrást, vagy hozza létre újra az erőforrásokat.
2. Új erőforrás létrehozása a törölt erőforrás lecseréléséhez

## <a name="next-steps"></a>További lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md)
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md) teljesítményével kapcsolatos irányelvek ismertetése