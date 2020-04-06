---
title: Az adatok áttelepítése Cassandra API-fiókba az Azure Cosmos DB-oktatóanyagban
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a CQL-másolás parancsot & Sparkhasználatával adatok másolása az Apache Cassandra-ból egy Cassandra API-fiókba az Azure Cosmos DB-ben
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
Customer intent: As a developer, I want to migrate my existing Cassandra workloads to Azure Cosmos DB so that the overhead to manage resources, clusters, and garbage collection is automatically handled by Azure Cosmos DB.
ms.openlocfilehash: d94ad56508d5e5f1e28a24e82460a68ffce5592f
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666882"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Oktatóanyag: Az adatok áttelepítése Cassandra API-fiókba az Azure Cosmos DB-ben

Fejlesztőként előfordulhat, hogy meglévő Cassandra-számítási feladatokkal rendelkezik, amelyek a helyszínen vagy a felhőben futnak, és érdemes lehet áttelepíteni őket az Azure-ba. Az ilyen számítási feladatok áttelepítheti a Cassandra API-fiók az Azure Cosmos DB.You can migrate such workloads to a Cassandra API-fiók in Azure Cosmos DB. Ez az oktatóanyag az Apache Cassandra-adatok Nak az Azure Cosmos DB Cassandra API-fiókjába való áttelepítéséhez elérhető különböző lehetőségekről tartalmaz utasításokat.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Az áttelepítés megtervezése
> * A migrálás előfeltételei
> * Adatok migrálása a cqlsh COPY paranccsal
> * Adatok migrálása a Spark használatával

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites-for-migration"></a>A migrálás előfeltételei

* **Az átviteli igények becslése:** Mielőtt áttelepíti az adatokat a Cassandra API-fiók az Azure Cosmos DB,meg kell becsülnie a számítási feladatok átviteli igényeinek. Általában ajánlott a CRUD-műveletekhez szükséges átlagos átviteli sebességgel kezdeni, majd ezt kiegészíteni a kinyerési, átalakítási és betöltési (ETL-) vagy a csúcsidejű műveletekhez szükséges további sebességgel. A migrálás megtervezéséhez a következő adatokra lesz szükség: 

  * **Meglévő vagy becsült adatmennyiség:** A minimálisan szükséges adatbázisméretet és átviteli sebességet határozza meg. Ha egy új alkalmazás adatmennyiségét becsüli fel, feltételezheti, hogy az adatok egyenletesen oszlanak majd el a sorok között, és az értéket az adatok méretét felszorozva kaphatja meg. 

  * **Szükséges átviteli:** Hozzávetőleges olvasási (lekérdezés/leolvasás) és írási (frissítési/törlési/beszúrási) átviteli sebesség. Erre az értékre a szükséges kérelemegység-mennyiség állandó adatmennyiség melletti kiszámításához van szükség.  

  * **A séma:** Csatlakozzon a meglévő Cassandra-fürthöz a cqlsh segítségével, és exportálja a sémát a Cassandrából: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Miután azonosította a meglévő számítási feladatok követelményeit, létre kell hoznia egy Azure Cosmos-fiókot, adatbázist és tárolókat az összegyűjtött átviteli követelményeknek megfelelően.  

  * **Határozza meg egy művelet vtv-díját:** A Randra API által támogatott SDK-k bármelyikének használatával határozhatja meg a Rosztúc-api-kat. Ez a példa a kérelemegység-díjak .NET-tel való beszerzését mutatja be.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **A szükséges átviteli sebesség lefoglalása:** Az Azure Cosmos DB-vel automatikusan, a növekvő követelményeknek megfelelően skálázható a tárolás és az átviteli sebesség. A szükséges átviteli sebességet az [Azure Cosmos DB kérelemegység-kalkulátorával](https://www.documentdb.com/capacityplanner) számíthatja ki. 

* **Táblák létrehozása a Cassandra API-fiókban:** Az adatok áttelepítése előtt hozza létre előre az összes táblát az Azure Portalról vagy a cqlsh-ból. Ha egy azure Cosmos-fiók, amely adatbázisszintű átviteli, győződjön meg arról, hogy egy partíciókulcsot az Azure Cosmos-tárolók létrehozásakor.

* **Átviteli sebesség növelése:** Az adatok migrálásának időtartama az Azure Cosmos DB-táblákhoz lefoglalt átviteli sebességtől függ. A migrálás idejére növelje meg az átviteli sebességet. Nagyobb átviteli sebesség beállításakor nincs szükség a sebesség korlátozására, és gyorsabban végezhet a migrálással. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. Azt is javasoljuk, hogy az Azure Cosmos-fiók ugyanabban a régióban, mint a forrás-adatbázis. 

* **TLS engedélyezése:** Az Azure Cosmos DB szigorú biztonsági követelményekkel és szabványokkal rendelkezik. Ügyeljen arra, hogy engedélyezze a TLS-t, amikor kapcsolatba lép a fiókjával. Ha a CQL-t SSH-val használja, lehetősége van A TLS-adatok megadására.

## <a name="options-to-migrate-data"></a>Adatmigrálási lehetőségek

A meglévő Cassandra-számításifeladatokból a következő lehetőségekkel migrálhatja az adatokat az Azure Cosmos DB-be:

* [A cqlsh COPY parancsával](#migrate-data-using-cqlsh-copy-command)  
* [A Spark használatával](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Adatok migrálása a cqlsh COPY paranccsal

A [CQL COPY parancs](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) helyi adatok másolására szolgál a Cassandra API-fiók az Azure Cosmos DB-ben. Az adatok másolásához hajtsa végre a következő lépéseket:

1. A Cassandra API-fiók kapcsolatisztring-adatainak lekérése:

   * Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és keresse meg az Azure Cosmos-fiókját.

   * Nyissa meg a **Kapcsolati sztring** panelt, amely a Cassandra API-fiókhoz a cqlsh-ból való kapcsolódáshoz szükséges összes információt tartalmazza.

2. A portál kapcsolati adatait használva jelentkezzen be a cqlsh-ba.

3. Használja a CQL COPY parancsot a helyi adatok a Cassandra API-fiókba való másolásához.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Adatok migrálása a Spark használatával 

Az alábbi lépésekkel adatokat telepítheti át a Cassandra API-fiókba a Sparkkal:

- Azure [Databricks-fürt](cassandra-spark-databricks.md) vagy [HDInsight-fürt](cassandra-spark-hdinsight.md) kiépítése 

- Adatok áthelyezése a cél Cassandra API-végpontra a [táblamásolási művelet](cassandra-spark-table-copy-ops.md) használatával 

Az adatok áttelepítése a Spark-feladatok használatával ajánlott lehetőség, ha az azure-beli virtuális gépekben vagy bármely más felhőben lévő adatok egy meglévő fürtben vannak. Ez a beállítás megköveteli, hogy a Spark egy alkalommal vagy rendszeres letöltési közvetítőként legyen beállítva. A helyszíni és az Azure közötti Azure ExpressRoute-kapcsolat használatával felgyorsíthatja ezt az áttelepítést. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, az Azure Cosmos-fiókot és az összes kapcsolódó erőforrást. Ehhez jelölje ki a virtuális gép erőforráscsoportját, válassza a **Törlés**lehetőséget, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan telepítheti át az adatokat cassandra API-fiókba az Azure Cosmos DB-ben. Most a következő cikket, hogy többet tudjon meg az Azure Cosmos DB fogalmak:

> [!div class="nextstepaction"]
> [Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](../cosmos-db/consistency-levels.md)


