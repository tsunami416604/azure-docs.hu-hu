---
title: Az Azure Cosmos DB dátumok használata
description: Ismerje meg az Azure Cosmos DB dátumok használata.
services: cosmos-db
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/25/2017
ms.openlocfilehash: 48879eb3ae4fc3a54d50a4e896b4a1eb94acabf9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53073805"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Az Azure Cosmos DB dátumok használata
Az Azure Cosmos DB séma rugalmasságát és a gazdag indexelési natív lehetőségeket kínál [JSON](https://www.json.org) adatmodellt. Az összes Azure Cosmos DB-erőforrásokat, például adatbázisok, tárolók, dokumentumok és tárolt eljárások modellezése és tárolása JSON-dokumentumok formájában. A hordozható visszatérhetnek követelmény, JSON-t (és az Azure Cosmos DB) támogatja a alapvető típusok csak egy kis készletét: karakterlánc, szám, logikai érték, tömböt, objektum és Null. JSON azonban rendkívül rugalmas, és lehetővé teszi a fejlesztők és a keretrendszereket, amelyek ezeket a primitívek használatával, és objektumokat vagy tömbök összeállítása azokat összetettebb típusokra. 

Az alapvető típusok, mellett számos alkalmazás kell a [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) dátumok és időbélyegek képviselő típus. Ez a cikk bemutatja, hogyan fejlesztők is tárolása, lekérése és lekérdezése az Azure Cosmos DB .NET SDK használatával dátumok.

## <a name="storing-datetimes"></a>Időpontok tárolására
Alapértelmezés szerint a [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) szerializálja a dátum/idő értékek [ISO 8601](https://www.iso.org/iso/catalogue_detail?csnumber=40874) karakterláncokat. A legtöbb alkalmazás a következő okok miatt alapértelmezett karakteres használható dátum és idő:

* Karakterláncok összehasonlíthatók, és a dátum/idő értékek relatív sorrendjének esetén is megőrződik karakterláncok való átalakításából származnak. 
* Ez a megközelítés a JSON-átalakítás bármilyen egyéni kód vagy attribútumok nem igényel.
* A JSON-fájlban tárolt dátumok emberi olvasható.
* Ez a megközelítés kihasználhatják az Azure Cosmos DB index a gyors lekérdezési teljesítményre.

Például az alábbi kódrészlet tárolja egy `Order` objektumot tartalmazó két dátum/idő tulajdonság - `ShipDate` és `OrderDate` -dokumentumként a .NET SDK használatával:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Ez a dokumentum következő tárolja az Azure Cosmos DB:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Másik lehetőségként tárolhatja időpontok Unix időbélyegeket, mint azt jelenti, egy számot jelölő 1970. január 1. óta eltelt másodpercek számát. Az Azure Cosmos DB belső Timestamp (`_ts`) tulajdonságát a következő ezt a megközelítést. Használhatja a [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) osztály as-számokat időpontok szerializálásához. 

## <a name="indexing-datetimes-for-range-queries"></a>Tartomány lekérdezéseket időpontok indexelése
A dátum/idő értékek gyakoriak a lekérdezések. Például a tegnap óta létrehozott összes rendelést, vagy keresse meg az összes, az elmúlt öt percben feladott megrendelések van szüksége, ha szüksége tartomány lekérdezések végrehajtásához. Hatékony hajtsa végre ezeket a lekérdezéseket, konfigurálnia kell a tartomány az indexelés a karakterláncok gyűjtemény.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Az indexelő a házirendek konfigurálásával kapcsolatos többet is megtudhat [Azure Cosmos DB-indexelő házirendek](index-policy.md).

## <a name="querying-datetimes-in-linq"></a>A LINQ időpontok lekérdezése
Az SQL .NET SDK-val automatikusan támogatja a LINQ-n keresztül az Azure Cosmos DB-ben tárolt adatok lekérdezésére. Például a következő kódrészlet azt mutatja be egy LINQ-lekérdezést, hogy az elmúlt három napban teljesített szűrők megrendelések.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

További információ az Azure Cosmos DB SQL lekérdezési nyelvet és a LINQ szolgáltatójának [lekérdezése a Cosmos DB](how-to-sql-query.md).

Ebben a cikkben azt keresi, hogyan lehet tárolni, Tárgymutató és időpontok az Azure Cosmos DB lekérdezése.

## <a name="next-steps"></a>További lépések
* Töltse le és futtassa a [Kódminták a Githubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Tudjon meg többet [SQL-lekérdezések](how-to-sql-query.md)
* Tudjon meg többet [Azure Cosmos DB-indexelő házirendek](index-policy.md)
