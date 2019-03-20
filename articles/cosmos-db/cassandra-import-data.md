---
title: 'Oktatóanyag: Az adatok áttelepítése az Azure Cosmos DB Cassandra API-fiók'
description: Ebből az oktatóanyagból megtudhatja, hogyan másolhat adatokat egy Apache Cassandra-ból az Azure Cosmos DB Cassandra API-fiók a CQL Másolás parancs és a Spark használatával.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
Customer intent: As a developer, I want to migrate my existing Cassandra workloads to Azure Cosmos DB so that the overhead to manage resources, clusters, and garbage collection is automatically handled by Azure Cosmos DB.
ms.openlocfilehash: c9f7ec5009c9299e317d9b10f857e326d25fa005
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58120108"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Oktatóanyag: Az adatok áttelepítése az Azure Cosmos DB Cassandra API-fiók

A fejlesztők, lehetséges, hogy a helyszínen futtató meglévő Cassandra-feladatokat vagy a felhőben, és előfordulhat, hogy szeretne az Azure-bA migrálásához. Ilyen számítási feladatok áttelepítheti az Azure Cosmos DB Cassandra API-fiók. Ebben az oktatóanyagban útmutatás különböző lehetőség legyen elérhető a Apache Cassandra-adatok áttelepítése az Azure Cosmos DB Cassandra API-fiókba.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Az áttelepítés megtervezése
> * A migrálás előfeltételei
> * Adatok migrálása a cqlsh COPY paranccsal
> * Adatok migrálása a Spark használatával

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites-for-migration"></a>A migrálás előfeltételei

* **Becsülni az átviteli sebességet van szüksége:** Mielőtt adatokat telepít át a Cassandra API-fiók az Azure Cosmos DB, a számítási feladatok átviteli igényeit kell becslései szerint. Általában ajánlott a CRUD-műveletekhez szükséges átlagos átviteli sebességgel kezdeni, majd ezt kiegészíteni a kinyerési, átalakítási és betöltési (ETL-) vagy a csúcsidejű műveletekhez szükséges további sebességgel. A migrálás megtervezéséhez a következő adatokra lesz szükség: 

  * **Meglévő adatok mérete vagy adatok becsült mérete:** Határozza meg a minimálisan szükséges adatbázis mérete és az átviteli sebesség követelménynek. Ha egy új alkalmazás adatmennyiségét becsüli fel, feltételezheti, hogy az adatok egyenletesen oszlanak majd el a sorok között, és az értéket az adatok méretét felszorozva kaphatja meg. 

  * **Átviteli sebességgel:** (Lekérdezés/get) becsült olvasási és írási (update/delete/insert) átviteli sebességet. Erre az értékre a szükséges kérelemegység-mennyiség állandó adatmennyiség melletti kiszámításához van szükség.  

  * **A séma:** A meglévő Cassandra-fürtöt cqlsh keresztül csatlakozik, és exportálja a sémát a Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Miután azonosította a meglévő számítási feladatok követelményeit, hozzunk létre egy Azure Cosmos-fiók, adatbázis és tárolók az összegyűjtött átviteli követelményeinek megfelelően.  

  * **Határozza meg egy művelet RU ingyenesen:** Megadhatja, hogy a kérelemegység a Cassandra API által támogatott SDK-k egyikével. Ez a példa a kérelemegység-díjak .NET-tel való beszerzését mutatja be.

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

* **Társítsa az átviteli sebességgel:** Az Azure Cosmos DB is automatikusan skálázható a tárolás és átviteli sebesség a követelmények növekedésével. A szükséges átviteli sebességet az [Azure Cosmos DB kérelemegység-kalkulátorával](https://www.documentdb.com/capacityplanner) számíthatja ki. 

* **Táblázatok létrehozása a Cassandra API-fiók:** Mielőtt elkezdené az adatok áttelepítését végzi, előre létrehozni minden a táblák az Azure Portalról vagy a cqlsh. Ha az áttelepítés egy Azure Cosmos-fiókhoz, amelyen adatbázis-szintű átviteli sebessége, ügyeljen arra, hogy az Azure Cosmos-tároló létrehozásakor adja meg a partíciókulcsot.

* **Átviteli sebesség növelése:** Az adatok migrálása idejére a táblák az Azure Cosmos DB-ben kiépített átviteli függ. A migrálás idejére növelje meg az átviteli sebességet. Nagyobb átviteli sebesség beállításakor nincs szükség a sebesség korlátozására, és gyorsabban végezhet a migrálással. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. Az Azure Cosmos-fiók rendelkezik a forrásadatbázis ugyanabban a régióban is ajánlott. 

* **SSL engedélyezése:** Az Azure Cosmos DB szigorú biztonsági követelményekkel és szabványokkal rendelkezik. A fiókja használatakor mindig engedélyezze az SSL-t. Ha az SSH-val használja a CQL-t, akkor megadhat SSL-információkat.

## <a name="options-to-migrate-data"></a>Adatmigrálási lehetőségek

A meglévő Cassandra-számításifeladatokból a következő lehetőségekkel migrálhatja az adatokat az Azure Cosmos DB-be:

* [A cqlsh COPY parancsával](#migrate-data-using-cqlsh-copy-command)  
* [A Spark használatával](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Adatok migrálása a cqlsh COPY paranccsal

A [CQL Másolás parancs](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) a Cassandra API-fiókot az Azure Cosmos DB helyi adatok másolása szolgál. Az adatok másolásához hajtsa végre a következő lépéseket:

1. A Cassandra API-fiók kapcsolatisztring-adatainak lekérése:

   * Jelentkezzen be a [az Azure portal](https://portal.azure.com), és lépjen az Azure Cosmos-fiókra.

   * Nyissa meg a **Kapcsolati sztring** panelt, amely a Cassandra API-fiókhoz a cqlsh-ból való kapcsolódáshoz szükséges összes információt tartalmazza.

2. A portál kapcsolati adatait használva jelentkezzen be a cqlsh-ba.

3. Használja a CQL COPY parancsot a helyi adatok a Cassandra API-fiókba való másolásához.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Adatok migrálása a Spark használatával 

A következő lépések segítségével telepítse át az adatokat a Cassandra API-fiókot, a Spark segítségével:

- Üzembe helyezése egy [Azure Databricks-fürt](cassandra-spark-databricks.md) vagy egy [HDInsight-fürt](cassandra-spark-hdinsight.md) 

- Helyezze át adatokat a rendeltetési Cassandra API-végpont használatával a [tábla másolási művelet](cassandra-spark-table-copy-ops.md) 

Áttelepítés a Spark-feladatok adatok a beállítás ajánlott Ha egy meglévő fürthöz az Azure-beli virtuális gépek vagy bármely más felhőalapú szereplő adatokat. Ehhez a beállításhoz az Apache Spark a szokásos betöltési vagy egy ideig a köztes beállítása. Az áttelepítés gyorsítható fel az Azure ExpressRoute-kapcsolat a helyszíni és az Azure között. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor szükség van rájuk már nem, törölheti az erőforráscsoportot, az Azure Cosmos-fiók és minden kapcsolódó erőforrás. Ehhez válassza ki a virtuális gép, jelölje be az erőforráscsoport **törlése**, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban bemutattuk az adatok áttelepítése az Azure Cosmos DB Cassandra API-fiókot. Most már továbbléphet a következő cikkben más az Azure Cosmos DB kapcsolatos fogalmak megismeréséhez:

> [!div class="nextstepaction"]
> [Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](../cosmos-db/consistency-levels.md)


