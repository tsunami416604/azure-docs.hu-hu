---
title: Az Azure Cosmos DB dátumok használata
description: Útmutató dátumból/időből-objektumok tárolásához, indexeléséhez és lekérdezéséhez Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 92fa35fbe8e5eef4dbdc8b6c47a9055affd449a5
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273187"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Az Azure Cosmos DB dátumok használata

A Azure Cosmos DB a séma rugalmasságát és a gazdag indexelést egy natív [JSON](https://www.json.org) -adatmodellen keresztül biztosítja. Az összes Azure Cosmos DB-erőforrásokat, például adatbázisok, tárolók, dokumentumok és tárolt eljárások modellezése és tárolása JSON-dokumentumok formájában. A hordozható visszatérhetnek követelmény, JSON-t (és az Azure Cosmos DB) támogatja a alapvető típusok csak egy kis készletét: karakterlánc, szám, logikai érték, tömböt, objektum és Null. JSON azonban rendkívül rugalmas, és lehetővé teszi a fejlesztők és a keretrendszereket, amelyek ezeket a primitívek használatával, és objektumokat vagy tömbök összeállítása azokat összetettebb típusokra.

Az alaptípusokon kívül számos alkalmazásnak szüksége van a DateTime típusra a dátumok és időbélyegek megjelenítéséhez. Ez a cikk bemutatja, hogyan fejlesztők is tárolása, lekérése és lekérdezése az Azure Cosmos DB .NET SDK használatával dátumok.

## <a name="storing-datetimes"></a>Időpontok tárolására

Azure Cosmos DB támogatja a JSON-típusokat, például a-string, a number, a Boolean, a null, a Array, az Object. Nem támogatja közvetlenül a DateTime típust. A Azure Cosmos DB jelenleg nem támogatja a dátumok honosítását. Ezért a DateTimes karakterláncként kell tárolnia. A DateTime karakterláncok ajánlott formátuma Azure Cosmos DBban `YYYY-MM-DDThh:mm:ss.sssZ`, amely az ISO 8601 UTC szabványt követi. Azt javasoljuk, hogy a Azure Cosmos DB összes dátumát UTC-ként tárolja. A dátum sztringek erre a formátumra való konvertálása lehetővé teszi a rendezési dátumok lexicographically. Ha a nem UTC formátumú dátumok vannak tárolva, a logikát az ügyféloldali helyen kell kezelni. Ha a helyi DateTime értéket UTC-re szeretné átalakítani, az eltolásnak ismertnek kell lennie, és a JSON-tulajdonságként kell tárolnia, és az ügyfél az eltolást a számítási UTC DateTime értékre használhatja.

A legtöbb alkalmazás a következő okok miatt alapértelmezett karakteres használható dátum és idő:

* Karakterláncok összehasonlíthatók, és a dátum/idő értékek relatív sorrendjének esetén is megőrződik karakterláncok való átalakításából származnak.
* Ez a megközelítés a JSON-átalakítás bármilyen egyéni kód vagy attribútumok nem igényel.
* A JSON-fájlban tárolt dátumok emberi olvasható.
* Ez a megközelítés kihasználhatják az Azure Cosmos DB index a gyors lekérdezési teljesítményre.

Az alábbi kódrészlet például egy `Order` objektumot tárol, amely két DateTime tulajdonságot tartalmaz – `ShipDate` és `OrderDate` a .NET SDK-t használó dokumentumként:

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

Ez a dokumentum következő tárolja az Azure Cosmos DB:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Másik lehetőségként tárolhatja időpontok Unix időbélyegeket, mint azt jelenti, egy számot jelölő 1970. január 1. óta eltelt másodpercek számát. A Azure Cosmos DB belső timestamp (`_ts`) tulajdonsága ezt a megközelítést követi. A [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) osztály használatával számként szerializálhatja a dátum és idő értéket.

## <a name="querying-datetimes-in-linq"></a>A LINQ időpontok lekérdezése

Az SQL .NET SDK-val automatikusan támogatja a LINQ-n keresztül az Azure Cosmos DB-ben tárolt adatok lekérdezésére. Az alábbi kódrészlet például egy LINQ-lekérdezést mutat be, amely az elmúlt három napban szállított rendeléseket szűri:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Lefordítva a következő SQL-utasításra, és a Azure Cosmos DBon hajtja végre:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")
```

A Azure Cosmos DB SQL-lekérdezési nyelvét és a LINQ-szolgáltatót a [LINQ-Cosmos db lekérdezése](sql-query-linq-to-sql.md)című témakörben olvashatja.

## <a name="indexing-datetimes-for-range-queries"></a>Tartomány lekérdezéseket időpontok indexelése

A lekérdezések gyakran DateTime értékekkel vannak meghatározva. A lekérdezések hatékony végrehajtásához a lekérdezés szűrője bármely tulajdonságában definiálni kell egy indexet.

További információ az indexelési házirendek konfigurálásáról [Azure Cosmos db indexelési házirendekben](index-policy.md). 

## <a name="next-steps"></a>További lépések

* A [kód mintáinak letöltése és futtatása a githubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* További információ az [SQL-lekérdezésekről](sql-query-getting-started.md)
* További információ az [Azure Cosmos db indexelési házirendekről](index-policy.md)
