---
title: "Cassandra adatok importálása az Azure Cosmos DB |} Microsoft Docs"
description: "Útmutató: Azure Cosmos DB Cassandra adatait átmásolja a CQL Másolás parancs segítségével."
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: 
ms.assetid: eced5f6a-3f56-417a-b544-18cf000af33a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: bbfcbadcb0010d50e572b335d4cac78b33e974c8
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Az Azure Cosmos DB: Import Cassandra adatok

Ez az oktatóanyag útmutatás Azure Cosmos DB Cassandra adatok importálása a Cassandra Query Language (CQL) másolás parancs használatával. 

Ez az oktatóanyag ismerteti a következő feladatokat:

> [!div class="checklist"]
> * A kapcsolati karakterlánc beolvasása
> * Adatok importálása a cqlsh Másolás parancs használatával
> * A Spark-összekötővel importálása 

# <a name="prerequisites"></a>Előfeltételek

* Telepítés [Apache Cassandra](http://cassandra.apache.org/download/) és biztosítják *cqlsh* található.
* Átviteli sebesség növelése: az adatok áttelepítése időtartamát határozza meg a táblák létesített átviteli. Győződjön meg arról, növelheti a teljesítményt nagyobb adatok áttelepítésre. Az áttelepítés befejezése után csökkenti az átviteli sebesség költségek csökkentése érdekében. További információ az átviteli sebesség növelése a [Azure-portálon](https://portal.azure.com), lásd: [Set átviteli az Azure Cosmos DB tárolókat](set-throughput.md).
* SSL engedélyezése: Azure Cosmos-adatbázis a szigorú biztonsági követelmények és szabványok tartoznak. Ne aktiválja az SSL-fiókja folytatott kommunikáció során. Ha SSH CQL használ, lehetősége van SSL információk megadására. 

## <a name="find-your-connection-string"></a>Keresse meg a kapcsolati karakterláncot

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a bal, a **Azure Cosmos DB**.

2. Az a **előfizetések** ablaktáblán válassza ki a fiók nevét.

3. Kattintson a **kapcsolati karakterlánc**. A jobb oldali ablaktáblán a fiók sikeresen csatlakozni kell az összes információt tartalmazza.

    ![Csatlakozási karakterlánc oldal](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>Másolás cqlsh használata

Adatok importálása Cassandra adatok Azure Cosmos DB Cassandra API-val használható, használja a következő útmutatást:

1. Jelentkezzen be a portálról kapcsolati információk használatával cqhsh.
2. Használja a [CQL Másolás parancs](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) helyi adatok másolása az Apache Cassandra API-végpontot. Biztosítsa, hogy a forrás és cél ugyanabban az adatközpontban a késési problémák minimalizálása érdekében.

### <a name="guide-for-moving-data-with-cqlsh"></a>Útmutató a cqlsh az adatok áthelyezése

1. Előre létrehozni, illetve a tábla skálázni:
    * Alapértelmezés szerint az Azure Cosmos DB rendelkezések egy új Cassandra API 1000 kérelemegység (RU/mp) (CQL-alapú létrehozása ki van építve 400 RU/mp) másodpercenként a tábla. Az áttelepítés előtt cqlsh használatával, előre létrehozni az összes tábla a [Azure-portálon](https://portal.azure.com) vagy cqlsh. 

    * Az a [Azure-portálon](https://portal.azure.com), növelhető a táblák az alapértelmezett átviteli sebesség (1000 vagy 400 RU/mp) a 10000 RU/mp az áttelepítés időtartama. A nagyobb átviteli sebességgel elkerülheti a sávszélesség-szabályozás, és kevesebb idő alatt át. Óránkénti számlázási Azure Cosmos DB, csökkentheti az átviteli sebesség közvetlenül a költségek csökkentése érdekében az áttelepítés után.

2. Határozza meg a művelet a RU kell fizetni. Ehhez az Azure Cosmos DB Cassandra API SDK a kiválasztott használatával. Ez a példa első RU díjak .NET verzióját jeleníti meg. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. Határozza meg, hogy a gép az Azure Cosmos DB szolgáltatás várakozási ideje. Ha az Azure adatközponton belül, a késés alacsony egyetlen számjegy ezredmásodperces számnak kell lennie. Ha az Azure Adatközponton kívülről, majd használhatja a psping vagy azurespeed.com hozzávetőleges késését lekérése a tartózkodási helyét.   

4. Paraméterek (NUMPROCESS, INGESTRATE, MAXBATCHSIZE vagy MINBATCHSIZE), amely elegendő teljesítményt nyújtanak a megfelelő értékek kiszámítása. 

5. A végső áttelepítési parancsot. A parancs futtatása azt feltételezi, hogy a kapcsolati karakterlánc adatainak felhasználásával cqlsh indította.

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>Adatok importálása a Spark segítségével

Egy meglévő fürthöz az Azure virtuális gépeken tárolt adatok, a rendszer adatok importálása Spark használatával is megoldható. Ehhez a Spark egy alkalommal vagy rendszeres adatfeldolgozást a pénznemként beállítása. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban már megismerte a következő feladatok elvégzéséhez:

> [!div class="checklist"]
> * A kapcsolat-karakterlánc beolvasása
> * Adatokat importálhat cql Másolás parancs használatával
> * A Spark-összekötővel importálása 

Most már folytathatja a fogalmakat részt Azure Cosmos DB további információt. 

> [!div class="nextstepaction"]
>[Az Azure Cosmos Adatbázisba hangolható konzisztencia szintek](../cosmos-db/consistency-levels.md)
