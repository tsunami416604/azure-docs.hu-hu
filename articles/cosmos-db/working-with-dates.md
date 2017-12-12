---
title: "Az Azure Cosmos Adatbázisba dátumok használata |} Microsoft Docs"
description: "További tudnivalók az Azure Cosmos Adatbázisba dátumok használata."
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: e587772f-ce9f-498c-a017-a51e7265bb23
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: arramac
ms.openlocfilehash: 0f26aea674eb2317c976af0cb8e81f619a8d64ae
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Az Azure Cosmos DB dátumok használata
Azure Cosmos DB biztosítja a sémák rugalmasságát és a gazdag indexelési keresztül natív [JSON](http://www.json.org) adatmodell. Minden Azure Cosmos DB-erőforrások, például az adatbázisok, gyűjtemények, dokumentumok és tárolt eljárások modellezése és tárolása JSON-dokumentumok is. Hordozható lesznek követelményként JSON (és az Azure Cosmos DB) támogatja a típus csak egy kis készletét: karakterlánc, szám, logikai érték, a tömb, objektum és Null. Azonban JSON rugalmas, és engedélyezze a fejlesztők és keretrendszerek képviselő összetett típusok ezek primitívek használatával, és objektumokat vagy tömbök összeállítása őket. 

Az egyszerű típusok, mellett számos alkalmazás kell a [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) dátumok és időbélyegeket képviselő típus. Ez a cikk ismerteti, hogyan fejlesztők is tárolhatja, beolvasása és a .NET SDK használatával Azure Cosmos DB dátumok lekérdezése.

## <a name="storing-datetimes"></a>Időpontok tárolása
Alapértelmezés szerint a [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) rendezi sorba dátum/idő értékek [ISO 8601](http://www.iso.org/iso/catalogue_detail?csnumber=40874) karakterláncok. A legtöbb alkalmazás a következő okok miatt használhatják a DateTime típusú érték az alapértelmezett karakterlánc-ábrázolása:

* Karakterláncok hasonlíthatók össze, és a dátum/idő értékek relatív sorrendjének esetén is megőrződik karakterláncok való átalakításából származnak. 
* Ez a megközelítés nem igényel egyéni kód vagy attribútumok JSON átalakításhoz.
* A dátum a JSON-ban tárolt emberi olvasható.
* Ez a megközelítés előnyeit élvezheti Azure Cosmos DB indexe gyors teljesítmény-küszöbérték.

Például az alábbi kódrészletben tárolja egy `Order` tartalmazó a két dátum/idő tulajdonság - objektum `ShipDate` és `OrderDate` -dokumentumként a .NET SDK használatával:

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

Ez a dokumentum következő Azure Cosmos DB tárolja:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Alternatív megoldásként tárolhatja időpontok Unix időbélyegeket, mint ez azt jelenti, hogy egy számot jelölő 1970. január 1. óta eltelt másodpercek számát. Belső időbélyeg Azure Cosmos-adatbázis (`_ts`) tulajdonság követi ezt a módszert használja. Használhatja a [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) osztály számként időpontok szerializálni. 

## <a name="indexing-datetimes-for-range-queries"></a>A lekérdezések időpontok indexelő
A dátum/idő értékek megegyeznek a lekérdezések. Például a tegnap óta létrehozott összes rendelések található, vagy megkeresheti az elmúlt öt percben szállított összes rendeléseket kell, ha szüksége lekérdezések végrehajtásához. Sikeres végrehajtásához ezeket a lekérdezéseket, konfigurálnia kell a gyűjteményt a karakterláncokhoz indexelő tartományon.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Az indexelő a házirendek konfigurálásával kapcsolatos részletesebb [Azure Cosmos DB indexelő házirendek](indexing-policies.md).

## <a name="querying-datetimes-in-linq"></a>A LINQ időpontok lekérdezése
Az SQL .NET SDK-t automatikusan támogatja az Azure Cosmos DB keresztül LINQ tárolt adatok lekérdezésére. Például az alábbi kódrészletben láthatja a LINQ lekérdezés, hogy az elmúlt három napban teljesített szűrők megrendelések.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

További Azure Cosmos DB SQL lekérdező nyelve és a LINQ szolgáltatónál, [lekérdezése Cosmos DB](documentdb-sql-query.md).

Ez a cikk azt venni, hogyan tárolhatja, index és az Azure Cosmos Adatbázisba időpontok lekérdezése.

## <a name="next-steps"></a>Következő lépések
* Töltse le és futtassa a [minták a Githubon Code](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* További információ [SQL-lekérdezések](documentdb-sql-query.md)
* További információ [Azure Cosmos DB indexelő házirendek](indexing-policies.md)
