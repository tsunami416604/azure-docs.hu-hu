---
title: A Cassandra Azure Cosmos DB API-jának módosítása
description: Ismerje meg, hogyan használhatja a módosítási hírcsatornát az Azure Cosmos DB API-ban cassandra az adatok módosításait.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74694622"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>A Cassandra Azure Cosmos DB API-jának módosítása

Módosítsa a [gettetési](change-feed.md) támogatás az Azure Cosmos DB API Cassandra érhető el a lekérdezési predikátumok a Cassandra lekérdezési nyelv (CQL) érhető el. Ezekkel az alapfeltételekkel lekérdezheti a változáscsatorna API-t. Az alkalmazások a CQL-ben szükséges elsődleges kulcs (más néven partíciókulcs) használatával kaphatják meg a táblán végrehajtott módosításokat. Ezután további műveleteket is végrehajthat az eredmények alapján. A tábla sorainak módosításai a módosítási idő sorrendjében kerülnek rögzítésre, és a rendezési sorrend partíciókulcsonként garantált.

A következő példa bemutatja, hogyan kaphat be módosítási hírcsatornát egy Cassandra API Keyspace tábla összes sorára a .NET használatával. A predikátum COSMOS_CHANGEFEED_START_TIME() közvetlenül a CQL-en belül használatos a változáscsatorna elemeinek lekérdezésére egy megadott kezdési időpontból (ebben az esetben az aktuális dátumidő). Letöltheti a teljes mintát [itt](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/).

Minden iteráció, a lekérdezés folytatódik az utolsó pont változások at olvasták, a lapozási állapot használatával. Láthatjuk a folyamatos patak az új változások a táblázat ban a Keyspace. Látni fogjuk a beszúrt vagy frissített sorok módosításait. A Cassandra API-ban a módosítási hírcsatorna módosítási hírcsatornájával végzett törlési műveletek figyelése jelenleg nem támogatott. 

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

Annak érdekében, hogy a módosításokat egy sor elsődleges kulcs, felveheti az elsődleges kulcsot a lekérdezésben. A következő példa bemutatja, hogyan követhető a "user_id = 1" sorban lévő változások nyomon követése

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Aktuális korlátozások

A cassandra API-val történő módosítási hírcsatorna használatakor a következő korlátozások érvényesek:

* A beszúrások és a frissítések jelenleg támogatottak. A törlési művelet még nem támogatott. Kerülő megoldásként lágy jelölőt adhat a törölt sorokhoz. Például vegyen fel egy "törölt" nevű mezőt a sorba, és állítsa "igaz" értékre.
* Az utolsó frissítés megmarad, mivel az alapvető SQL API-ban és az entitás köztes frissítései nem érhetők el.


## <a name="error-handling"></a>Hibakezelés

A Cassandra API módosítási hírcsatornájának használatakor a következő hibakódok és üzenetek támogatottak:

* **HTTP-hibakód 429** - Ha a módosítási hírcsatorna sebessége korlátozott, üres oldalt ad vissza.

## <a name="next-steps"></a>További lépések

* [Az Azure Cosmos DB Cassandra API-erőforrások kezelése az Azure Resource Manager-sablonokkal](manage-cassandra-with-resource-manager.md)
