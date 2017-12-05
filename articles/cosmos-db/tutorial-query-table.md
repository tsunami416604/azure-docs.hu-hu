---
title: "Hogyan lehet az Azure Cosmos Adatbázisba tábla adatait? | Microsoft Docs"
description: "Ismerje meg, hogyan tábla adatait az Azure Cosmos-Adatbázisba"
services: cosmos-db
documentationcenter: 
author: kanshiG
manager: jhubbard
editor: 
tags: 
ms.assetid: 14bcb94e-583c-46f7-9ea8-db010eb2ab43
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 80fed91c45ae19193f6b8dfcaef747f8c4253dee
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="azure-cosmos-db-how-to-query-table-data-by-using-the-table-api"></a>Azure Cosmos DB: Hogyan tábla adatait kéri a tábla API használatával

Az Azure Cosmos DB [tábla API](table-introduction.md) támogatja az OData és [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) Lekérdezések kulcs/érték (tábla) adatok alapján.  

Ez a cikk ismerteti a következő feladatokat: 

> [!div class="checklist"]
> * Tábla API-val adatok lekérdezése

Ebben a cikkben a lekérdezések használja az alábbi minta `People` tábla:

| PartitionKey | RowKey | E-mail cím | Telefonszám |
| --- | --- | --- | --- |
| Grönlandi | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 | 

Lásd: [lekérdezése táblákat és entitásokat](https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities) lekérdezés a tábla API használatával kapcsolatos részletekért. 

Prémium szintű képességeit, amely Azure Cosmos DB nyújt további információkért lásd: [Azure Cosmos DB tábla API](table-introduction.md) és [Develop a tábla API-t a .NET](tutorial-develop-table-dotnet.md). 

## <a name="prerequisites"></a>Előfeltételek

A lekérdezések működjön rendelkezik Azure Cosmos DB fiókkal és a tárolóban lévő adatok entitás rendelkezik. Nem rendelkezik egyetlen, az? Fejezze be a [ötperces gyors üzembe helyezés](create-table-dotnet.md) vagy a [fejlesztői útmutató](tutorial-develop-table-dotnet.md) hozzon létre egy fiókot, és töltse fel az adatbázist.

## <a name="query-on-partitionkey-and-rowkey"></a>Lekérdezés PartitionKey és RowKey
A PartitionKey és RowKey tulajdonság egy entitás elsődleges kulcsának alkotnak, mert a következő szintaxist segítségével azonosítja az entitást: 

**Lekérdezés**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```
**Eredmények**

| PartitionKey | RowKey | E-mail cím | Telefonszám |
| --- | --- | --- | --- |
| Grönlandi | Walter | Walter@contoso.com| 425-555-0104 |

Másik megoldásként megadhatja ezeket a tulajdonságokat részeként a `$filter` lehetőség, mert az alábbi részben található. Vegye figyelembe, hogy a kulcstulajdonság és állandó értékek kis-és nagybetűket. A PartitionKey és a RowKey tulajdonságainak vannak karakterlánc típusú. 

## <a name="query-by-using-an-odata-filter"></a>Egy OData-szűrő segítségével lekérdezése
Egy szűrési karakterláncot térített közben tartsa szem előtt ezeket a szabályokat: 

* Az OData protokoll specifikációja által definiált logikai operátorok segítségével összehasonlíthatja a tulajdonság értéke. Vegye figyelembe, hogy a dinamikus érték a tulajdonság nem hasonlíthatók össze. A kifejezés egy oldalán állandónak kell lennie. 
* A tulajdonság nevét, a operátor és a konstans URL-kódolású szóközt kell elválasztani. Egy hely, mint URL-kódolású `%20`. 
* Minden a szűrési karakterláncot részei kis-és nagybetűket. 
* Ugyanannál az adattípusnál a tulajdonság ahhoz, hogy a szűrő érvényes találatot, az állandó értéknek kell lennie. További információ a támogatott tulajdonságtípus: [ismertetése a Table szolgáltatás adatmodell](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). 

Íme egy példa bemutatja, hogyan szűrése a PartitionKey és e-mailek tulajdonság egy OData lekérdezést `$filter`.

**Lekérdezés**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Hogyan készítse elő különféle adatok szűrőkifejezéseket további információkért lásd: [lekérdezése táblákat és entitásokat](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities).

**Eredmények**

| PartitionKey | RowKey | E-mail cím | Telefonszám |
| --- | --- | --- | --- |
| Ben |Smith | Ben@contoso.com| 425-555-0102 |

## <a name="query-by-using-linq"></a>A LINQ lekérdezés 
LINQ, amely a megfelelő OData-lekérdezési kifejezések használatával is lekérheti. Íme egy példa bemutatja, hogyan hozhat létre lekérdezéseket a .NET SDK használatával:

```csharp
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("people");

TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition(PartitionKey, QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition(Email, QueryComparisons.Equal,"Ben@contoso.com")
    ));

await table.ExecuteQuerySegmentedAsync<CustomerEntity>(query, null);
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Megtudta, a tábla API-jával lekérdezése

Most már folytathatja a következő oktatóanyag megtudhatja, miként ossza el az adatokat globális.

> [!div class="nextstepaction"]
> [Az adatok globálisan terjesztése](tutorial-global-distribution-table.md)
