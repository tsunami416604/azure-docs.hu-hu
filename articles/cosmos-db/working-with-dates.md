---
title: Dátumok kalkulálása az Azure Cosmos DB-ben
description: A DataTime-objektumok tárolása, indexelése és lekérdezése az Azure Cosmos DB-ben
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 92fa35fbe8e5eef4dbdc8b6c47a9055affd449a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273187"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Dátumok kal kukaca az Azure Cosmos DB-ben

Az Azure Cosmos DB sémarugalmasságot és gazdag indexelést biztosít egy natív [JSON-adatmodellen](https://www.json.org) keresztül. Az Azure Cosmos DB összes erőforrása, beleértve az adatbázisokat, tárolókat, dokumentumokat és tárolt eljárásokat, json-dokumentumokként van modellezve és tárolva. A hordozhatóság követelménye ként a JSON (és az Azure Cosmos DB) csak az alapvető típusok egy kis készletét támogatja: karakterlánc, szám, logikai, tömb, objektum és null. A JSON azonban rugalmas, és lehetővé teszi a fejlesztők és a keretrendszerek számára, hogy összetettebb típusokat képviseljenek ezekkel a primitívekkel, és objektumokként vagy tömbökként komponálják őket.

Az alapvető típusok mellett számos alkalmazásnak szüksége van a DateTime típusra a dátumok és az időbélyegek ábrázolásához. Ez a cikk azt ismerteti, hogy a fejlesztők hogyan tárolhatják, kérhetik le és kérdezhetik le a dátumokat az Azure Cosmos DB-ben a .NET SDK használatával.

## <a name="storing-datetimes"></a>DateTimes tárolása

Az Azure Cosmos DB támogatja a JSON-típusokat, például - karakterlánc, szám, logikai, null, tömb, objektum. Közvetlenül nem támogatja a DateTime típust. Jelenleg az Azure Cosmos DB nem támogatja a dátumok honosítását. Tehát a DateTimes-ot karakterláncként kell tárolnia. Az Azure Cosmos DB DateTime karakterláncai ajánlott formátuma az `YYYY-MM-DDThh:mm:ss.sssZ` ISO 8601 UTC szabványt követi. Javasoljuk, hogy az Azure Cosmos DB összes dátumát UTC-ként tárolja. A dátumkarakterláncok formátumba konvertálása lexikografikus módon teszi lehetővé a dátumok rendezését. Ha nem UTC-dátumokat tárol, a logikát az ügyféloldalon kell kezelni. A helyi DateTime UTC-vé történő konvertálásához az eltolást a JSON-ban tulajdonságként kell ismernie/tárolnia, és az ügyfél az eltolás segítségével kiszámíthatja az UTC DateTime értéket.

A legtöbb alkalmazás a DateTime alapértelmezett karakterlánc-ábrázolását a következő okok miatt használhatja:

* A karakterláncok összehasonlíthatók, és a DateTime értékek relatív sorrendje megmarad, amikor karakterláncokká alakulnak át.
* Ez a megközelítés nem igényel egyéni kódot vagy attribútumokat a JSON-konvertáláshoz.
* A JSON-ban tárolt dátumok emberileg olvashatók.
* Ez a megközelítés kihasználhatja az Azure Cosmos DB indexét a gyors lekérdezési teljesítmény érdekében.

A következő kódrészlet például `Order` két DateTime tulajdonságot `ShipDate` tartalmazó `OrderDate` objektumot tárol, és a .NET SDK-t használó dokumentumként:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });
```

Ez a dokumentum az Azure Cosmos DB-ben az alábbiak szerint tárolódik:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Másik lehetőségként a DateTimes-t Unix időbélyegként is tárolhatja, azaz az 1970. Az Azure Cosmos DB belső`_ts`időbélyeg ( ) tulajdonsága ezt a megközelítést követi. A [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) osztály használatával szerializálhatja a DateTimes-t számként.

## <a name="querying-datetimes-in-linq"></a>DateTimes lekérdezése a LINQ-ban

Az SQL .NET SDK automatikusan támogatja az Azure Cosmos DB-ben a LINQ-n keresztül tárolt adatok lekérdezését. A következő kódrészlet például egy LINQ-lekérdezést jelenít meg, amely az elmúlt három napban szállított rendeléseket szűri:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Lefordítva a következő SQL utasításra, és az Azure Cosmos DB-n hajtva végre:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")
```

Az Azure Cosmos DB SQL-lekérdezési nyelvéről és a LINQ-szolgáltatóról a [LINQ-ban található Querying Cosmos DB-ről olvashat bővebben.](sql-query-linq-to-sql.md)

## <a name="indexing-datetimes-for-range-queries"></a>Dátumidők indexelése tartománylekérdezésekhez

A lekérdezések gyakoriak a DateTime-értékekkel. A lekérdezések hatékony végrehajtásához a lekérdezés szűrőjének bármely tulajdonságán meg kell határozni egy indexet.

Az indexelési szabályzatok konfigurálásáról az [Azure Cosmos DB indexelési szabályzataiban](index-policy.md)olvashat bővebben. 

## <a name="next-steps"></a>Következő lépések

* A Kódminták letöltése és [futtatása a GitHubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* További információ az [SQL-lekérdezésekről](sql-query-getting-started.md)
* További információ az [Azure Cosmos DB indexelési szabályzatairól](index-policy.md)
