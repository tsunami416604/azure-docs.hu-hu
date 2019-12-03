---
title: A Azure Cosmos DB API-hoz tartozó hírcsatorna módosítása a Cassandra-hoz
description: Megtudhatja, hogyan használhatja az adatváltozásokat a Azure Cosmos DB API-ban a Cassandra paranccsal az adatváltozások beszerzéséhez.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694622"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>A Azure Cosmos DB API-hoz tartozó hírcsatorna módosítása a Cassandra-hoz

A "Cassandra" Azure Cosmos DB API-n keresztüli [adatcsatorna](change-feed.md) -támogatás módosítása a Cassandra Query Language (CQL) lekérdezési predikátumán keresztül érhető el. Ezen predikátum-feltételek használatával lekérdezheti a Change feed API-t. Az alkalmazások az elsődleges kulcs (más néven partíciós kulcs) használatával is beszerezhetik a tábla módosításait, ahogy az a CQL-ben szükséges. Ezután az eredmények alapján további műveleteket hajthat végre. A tábla sorainak módosításait a rendszer a módosítási idő sorrendjében rögzíti, a rendezési sorrendet pedig egy partíciós kulcs garantálja.

Az alábbi példa azt mutatja be, hogyan kérhető le egy módosítási hírcsatorna a Cassandra APIi tábla összes sorában a .NET használatával. Az predikátum COSMOS_CHANGEFEED_START_TIME () közvetlenül a CQL-n belül használható a változási csatornán lévő elemek lekérdezésére a megadott kezdési időpontból (ebben az esetben az aktuális datetime értéknél). A teljes mintát [innen](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/)töltheti le.

Az egyes iterációkban a lekérdezés az utolsó pont módosításainál folytatódik, a lapozási állapot használatával. A tábla új változásainak folyamatos streamjét láthatjuk. Ekkor megjelenik a beszúrt vagy frissített sorok változásai. A Cassandra API változási csatornán keresztüli törlési műveletek figyelése jelenleg nem támogatott. 

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

Ahhoz, hogy az elsődleges kulcs egyetlen sorba módosítsa a módosításokat, felveheti az elsődleges kulcsot a lekérdezésbe. Az alábbi példa bemutatja, hogyan követheti nyomon a sor változásait, ahol "user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Aktuális korlátozások

A következő korlátozások érvényesek a Change feed Cassandra API használatával történő használatakor:

* A lapkák és a frissítések jelenleg támogatottak. A törlési művelet még nem támogatott. Megkerülő megoldásként hozzáadhat egy lágy jelölőt a törölt sorokhoz. Például adjon hozzá egy mezőt a "törölt" nevű sorban, és állítsa "true" (igaz) értékre.
* A legutóbbi frissítés az alapszintű SQL API-ban és az entitás közbenső frissítései nem érhetők el.


## <a name="error-handling"></a>Hibakezelés

A következő hibakódok és üzenetek támogatottak a Cassandra API változási csatornájának használatakor:

* **Http-hibakód 429** – ha a módosítási csatornára korlátozott a ráta, üres lapot ad vissza.

## <a name="next-steps"></a>Következő lépések

* [Azure Cosmos DB Cassandra API erőforrások kezelése Azure Resource Manager sablonok használatával](manage-cassandra-with-resource-manager.md)
