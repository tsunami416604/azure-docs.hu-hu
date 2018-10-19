---
title: Adatok migrálása az Azure Cosmos DB Cassandra API-fiókba
description: Ismerje meg, hogyan másolhat Apache Cassandra-adatokat az Azure Cosmos DB Cassandra API-ba a CQL másolási parancs és a Spark használatával.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.author: govindk
ms.topic: tutorial
ms.date: 09/24/2018
ms.reviewer: sngun
ms.openlocfilehash: f73a201a25bb2f975e8a261a6c21aa7b066c3a7c
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247850"
---
# <a name="migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>Adatok migrálása az Azure Cosmos DB Cassandra API-fiókba

Ez az oktatóanyag bemutatja, hogyan migrálhat Apache Cassandra-adatokat az Azure Cosmos DB Cassandra API-ba. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Az áttelepítés megtervezése
> * A migrálás előfeltételei
> * Adatok migrálása a cqlsh COPY paranccsal
> * Adatok migrálása a Spark használatával 

## <a name="plan-for-migration"></a>Az áttelepítés megtervezése

Az adatok az Azure Cosmos DB Cassandra API-ba való migrálása előtt fel kell becsülnie a számítási feladat által igényelt átviteli sebességet. Általában ajánlott a CRUD-műveletekhez szükséges átlagos átviteli sebességgel kezdeni, majd ezt kiegészíteni a kinyerési, átalakítási és betöltési (ETL-) vagy a csúcsidejű műveletekhez szükséges további sebességgel. A migrálás megtervezéséhez a következő adatokra lesz szükség: 

* **Meglévő vagy becsült adatmennyiség:** A minimálisan szükséges adatbázisméretet és átviteli sebességet határozza meg. Ha egy új alkalmazás adatmennyiségét becsüli fel, feltételezheti, hogy az adatok egyenletesen oszlanak majd el a sorok között, és az értéket az adatok méretét felszorozva kaphatja meg. 

* **Szükséges átviteli sebesség:** A hozzávetőleges olvasási (lekérdezési/lekérési) és írási (frissítési/törlési/beszúrási) átviteli sebesség. Erre az értékre a szükséges kérelemegység-mennyiség állandó adatmennyiség melletti kiszámításához van szükség.  

* **A séma beolvasása:** Csatlakozzon a meglévő Cassandra-fürthöz cqlsh használatával, és exportálja a sémát a Cassandráról: 

  ```bash
  cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
  ```

Miután felmérte a meglévő számítási feladathoz szükséges kapacitásokat, hozzon létre egy Azure Cosmos DB-fiókot, egy adatbázist és tárolókat az összegyűjtött teljesítménykövetelmények alapján.  

* **A műveletek kérelemegységenkénti díjának megállapítása:** A kérelemegységeket bármely tetszőleges Azure Cosmos DB Cassandra API SDK-val meghatározhatja. Ez a példa a kérelemegység-díjak .NET-tel való beszerzését mutatja be.

  ```csharp
  var tableInsertStatement = table.Insert(sampleEntity);
  var insertResult = await tableInsertStatement.ExecuteAsync();

  foreach (string key in insertResult.Info.IncomingPayload)
    {
       byte[] valueInBytes = customPayload[key];
       string value = Encoding.UTF8.GetString(valueInBytes);
       Console.WriteLine($"CustomPayload:  {key}: {value}");
    }
  ```

* **A szükséges átviteli sebesség lefoglalása:** Az Azure Cosmos DB-vel automatikusan, a növekvő követelményeknek megfelelően skálázható a tárolás és az átviteli sebesség. A szükséges átviteli sebességet az [Azure Cosmos DB kérelemegység-kalkulátorával](https://www.documentdb.com/capacityplanner) számíthatja ki. 

## <a name="prerequisites-for-migration"></a>A migrálás előfeltételei

* **Táblák létrehozása az Azure Cosmos DB Cassandra API-fiókban:** Az adatok migrálásának indítása előtt hozza létre előre az összes táblát az Azure Portalról vagy a cqlsh-val. Ha egy olyan Azure Cosmos DB-fiókba migrál, amely adatbázisszintű átviteli sebességgel rendelkezik, az Azure Cosmos DB-tárolók létrehozásakor mindenképpen adjon meg egy partíciókulcsot.

* **Átviteli sebesség növelése:** Az adatok migrálásának időtartama az Azure Cosmos DB-táblákhoz lefoglalt átviteli sebességtől függ. A migrálás idejére növelje meg az átviteli sebességet. Nagyobb átviteli sebesség beállításakor nincs szükség a sebesség korlátozására, és gyorsabban végezhet a migrálással. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. További információ az átviteli sebesség növeléséről az Azure Cosmos DB-tárolók [átviteli sebességének beállítását](set-throughput.md) ismertető szakaszban található. Emellett javasolt az Azure Cosmos DB-fiókot és a forrásadatbázist azonos régióban használni. 

* **Az SSL engedélyezése:** Az Azure Cosmos DB szigorú biztonsági feltételekkel és szabványokkal rendelkezik. A fiókja használatakor mindig engedélyezze az SSL-t. Ha az SSH-val használja a CQL-t, akkor megadhat SSL-információkat.

## <a name="options-to-migrate-data"></a>Adatmigrálási lehetőségek

A meglévő Cassandra-számításifeladatokból a következő lehetőségekkel migrálhatja az adatokat az Azure Cosmos DB-be:

* [A cqlsh COPY parancsával](#using-cqlsh-copy-command)  
* [A Spark használatával](#using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Adatok migrálása a cqlsh COPY paranccsal

A [CQL COPY paranccsal](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) helyi adatok másolhatók az Azure Cosmos DB Cassandra API-fiókba. Az adatok másolásához hajtsa végre a következő lépéseket:

1. A Cassandra API-fiók kapcsolatisztring-adatainak lekérése:

   * Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és lépjen az Azure Cosmos DB-fiókhoz.

   * Nyissa meg a **Kapcsolati sztring** panelt, amely a Cassandra API-fiókhoz a cqlsh-ból való kapcsolódáshoz szükséges összes információt tartalmazza.

2. A portál kapcsolati adatait használva jelentkezzen be a cqlsh-ba.

3. Használja a CQL COPY parancsot a helyi adatok a Cassandra API-fiókba való másolásához.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Adatok migrálása a Spark használatával 

A következő lépések végrehajtásával migrálhatja az adatokat a Sparkkal az Azure Cosmos DB Cassandra API-ba:

- Foglaljon le egy [Azure Databricks-](cassandra-spark-databricks.md) vagy egy [HDInsight-fürtöt](cassandra-spark-hdinsight.md). 

- Helyezze át az adatokat a Cassandra API-célvégpontra a [táblamásolási művelet](cassandra-spark-table-copy-ops.md) végrehajtásával. 

Az adatok a Spark-feladatokkal való migrálása a javasolt megoldás, ha az adatok egy meglévő fürtben találhatók egy Azure-beli virtuális gépen vagy valamely más felhőben. Ehhez a Sparkot közvetítőként kell beállítani egyszeri vagy rendszeres betöltéshez. A migrálás felgyorsítható, ha a helyszíni és az Azure-beli rendszerek között ExpressRoute-kapcsolatot használ. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan migrálhatja adatait az Azure Cosmos DB Cassandra API-fiókba. Az Azure Cosmos DB-vel kapcsolatos további információért folytassa az Alapfogalmak szakasz áttekintésével. 

> [!div class="nextstepaction"]
> [Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](../cosmos-db/consistency-levels.md)


