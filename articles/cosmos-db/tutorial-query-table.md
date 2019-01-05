---
title: Hogyan kérdezhetők le táblaadatok az Azure Cosmos DB-ben?
description: Megismerheti, hogyan kérdezhetők le táblaadatok az Azure Cosmos DB-ben
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 11/15/2017
ms.reviewer: sngun
ms.openlocfilehash: 9a47333c8cd8968c007a2d328e81d8ea8585971c
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037990"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-table-api"></a>Oktatóanyag: Azure Cosmos DB lekérdezése a Table API-val

Az Azure Cosmos DB [Tábla API](table-introduction.md) támogatja a kulcs/érték (tábla) adatok OData- és [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service)-lekérdezéseit.  

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Adatok lekérdezése a Tábla API-val

A cikkben szereplő lekérdezések a következő minta `People` táblát használják:

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 | 

A [Táblák és entitások lekérdezése](https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities) című szakaszban talál további részleteket a Tábla API-val való lekérdezés menetéről. 

További információ az Azure Cosmos DB által nyújtott prémium képességekről: [Azure Cosmos DB Tábla API](table-introduction.md) és [Fejlesztés a Tábla API-val .NET-keretrendszerben](tutorial-develop-table-dotnet.md). 

## <a name="prerequisites"></a>Előfeltételek

A lekérdezések működéséhez Azure Cosmos DB-fiókkal kell rendelkeznie, és a tárolóban entitásadatoknak kell lennie. Nem rendelkezik ezekkel? A fiók létrehozásához és az adatbázis feltöltéséhez végezze el az [öt perces gyors útmutatót](create-table-dotnet.md) vagy a [fejlesztői oktatóanyagot](tutorial-develop-table-dotnet.md).

## <a name="query-on-partitionkey-and-rowkey"></a>Lekérdezés PartitionKey és RowKey tulajdonságok esetén
Mivel a PartitionKey és a RowKey tulajdonságok képezik az entitás fő kulcsát, a következő speciális szintaxis segítségével azonosíthatja az entitást: 

**Lekérdezés**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```
**Results**

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

A `$filter` lehetőség részeként is meghatározhatja ezeket a tulajdonságokat, a következő szakaszban látható módon. Vegye figyelembe, hogy a kulcstulajdonság nevei és az állandó értékek megkülönböztetik a kis- és nagybetűket. A PartitionKey és a RowKey tulajdonság típusa egyaránt sztring. 

## <a name="query-by-using-an-odata-filter"></a>Lekérdezés OData-szűrővel
Szűrő sztringjének felépítésekor ne feledje ezeket a szabályokat: 

* Az OData-protokollspecifikáció által definiált logikai operátorokat használja a tulajdonságok és az értékek összehasonlítására. Vegye figyelembe, hogy nem hasonlíthat össze tulajdonságot dinamikus értékkel. A kifejezés egyik oldalának állandónak kell lennie. 
* A tulajdonság nevét, az operátort és az állandó értéket URL-kódolású szóközzel kell elválasztani. A szóköz URL-kódolása `%20`. 
* A szűrési sztring minden része megkülönbözteti a kis- és nagybetűket. 
* Az állandó érték adattípusának meg kell egyeznie a tulajdonságéval ahhoz, hogy a szűrő érvényes eredményeket adjon vissza. További információ a támogatott tulajdonságtípusokról: [A Table szolgáltatás adatmodelljének ismertetése](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). 

A következő példalekérdezés bemutatja, hogyan szűrhet a PartitionKey és az E-mail tulajdonságok alapján OData `$filter` használatával.

**Lekérdezés**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

További információ a szűrőkifejezések felépítéséről különböző adattípusokhoz:[Táblák és entitások lekérdezése](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities).

**Results**

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Ben |Smith | Ben@contoso.com| 425-555-0102 |

## <a name="query-by-using-linq"></a>Lekérdezés a LINQ használatával 
A LINQ használatával is végezhet lekérdezést, amelyet a rendszer a megfelelő OData-lekérdezési kifejezésekre fordít. A következő példa bemutatja, hogyan építhetők fel lekérdezések a .NET SDK-val:

```csharp
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("People");

TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal,"Ben@contoso.com")
    ));

await table.ExecuteQuerySegmentedAsync<CustomerEntity>(query, null);
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Megtudta, hogyan végezhet lekérdezést a Tábla API-val

Továbbléphet a következő oktatóanyagra, amelyben megismerheti, hogyan terjesztheti az adatait globálisan.

> [!div class="nextstepaction"]
> [Globális adatterjesztés](tutorial-global-distribution-table.md)
