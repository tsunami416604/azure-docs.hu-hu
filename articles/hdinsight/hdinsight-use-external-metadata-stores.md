---
title: Külső metaadat-tárolók használata – Azure HDInsight
description: Külső metaadat-tárolók használata az Azure HDInsight-fürtökkel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/03/2020
ms.openlocfilehash: e53164d1e25f8a8d0a14d21c0544d95cf912fe9f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313962"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Külső metaadattárak használata az Azure HDInsightban

A HDInsight lehetővé teszi az adatok és a metaadatok külső adattárakkal való vezérlését. Ez a funkció elérhető az [Apache Hive metastore,](#custom-metastore) [Apache Oozie metastore](#apache-oozie-metastore)és [Apache Ambari adatbázis.](#custom-ambari-db)

A HDInsight Apache Hive metastore-ja az Apache Hadoop architektúra alapvető része. A metatároló a központi sématár. A metatárolót más big data-hozzáférési eszközök, például az Apache Spark, az Interactive Query (LLAP), a Presto vagy az Apache Pig használják. A HDInsight egy Azure SQL-adatbázist használ a Hive metastore-ként.

![HDInsight Hive metaadattár-architektúra](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

A HDInsight-fürtök metatárolóját kétféleképpen állíthatja be:

* [Alapértelmezett metabolt](#default-metastore)
* [Egyéni metabolt](#custom-metastore)

## <a name="default-metastore"></a>Alapértelmezett metabolt

Alapértelmezés szerint a HDInsight létrehoz egy metatárolót minden fürttípussal. Ehelyett megadhat egy egyéni metatárolót. Az alapértelmezett metatároló a következő szempontokat tartalmazza:

* Nincs további költség. A HDInsight minden fürttípussal létrehoz egy metatárolót, anélkül, hogy önnek további költségeket jelentene.

* Minden alapértelmezett metatároló a fürt életciklusának része. Fürt törlésekor a megfelelő metatár és metaadatok is törlődnek.

* Az alapértelmezett metatároló nem osztható meg más fürtökkel.

* Az alapértelmezett metastore az alapvető Azure SQL DB-t használja, amely öt DTU (adatbázis-tranzakciós egység) korláttal rendelkezik.
Ez az alapértelmezett metastore általában viszonylag egyszerű számítási feladatokhoz használatos. Olyan számítási feladatok, amelyek nem igényelnek több fürtöt, és nem igényelnek metaadatokat a fürt életciklusán túl.

## <a name="custom-metastore"></a>Egyéni metabolt

A HDInsight támogatja az egyéni metaboltokat is, amelyek éles fürtökhöz ajánlottak:

* Megadhatja a saját Azure SQL-adatbázis metastore.

* A metatár életciklusa nem kötődik a fürtök életciklusához, így a metaadatok elvesztése nélkül hozhat létre és törölhet fürtöket. Metaadatok, például a Hive-sémák megmaradnak, még a HDInsight-fürt törlése után is.

* Az egyéni metatároló lehetővé teszi, hogy több fürtöt és fürttípust csatoljon az adott metatárhoz. Például egy metatároló megosztható az interaktív lekérdezés, a Hive és a Spark-fürtök között a HDInsightban.

* A metastore (Azure SQL DB) költségét a választott teljesítményszintnek megfelelően kell fizetnie.

* Szükség szerint felskálázhatja a metatárolót.

* A fürtnek és a külső metatárolónak ugyanabban a régióban kell üzemeltetnie.

![HDInsight-struktúra metaadat-tároló használati esete](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Létrehozása és config Azure SQL-adatbázis az egyéni metastore

Hozzon létre vagy hozzon létre egy meglévő Azure SQL-adatbázist, mielőtt egy HDInsight-fürthöz egyéni Hive metatárolót állítana be.  További információ: [Rövid útmutató: Egyetlen adatbázis létrehozása az Azure SQL DB-ben című témakörben.](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

A fürt létrehozása során a HDInsight-szolgáltatásnak csatlakoznia kell a külső metatárhoz, és ellenőriznie kell a hitelesítő adatait. Konfigurálja az Azure SQL Database tűzfalszabályait, hogy az Azure-szolgáltatások és -erőforrások hozzáférhessenek a kiszolgálóhoz. Engedélyezze ezt a beállítást az Azure Portalon a **Kiszolgálótűzfal beállítása**lehetőséget választva. Ezután válassza a **Nem** lehetőséget a **Nyilvános hálózati hozzáférés megtagadása**csoportban, és az **Igen** lehetőséget az **Azure-szolgáltatások és -erőforrások az** Azure SQL Database-kiszolgáló vagy -adatbázis kiszolgálójához való hozzáférés engedélyezése alatt. További információt az [IP-tűzfalszabályok létrehozása és kezelése](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) című témakörben talál.

![kiszolgáló tűzfalának beállítása gomb](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![az Azure-szolgáltatások elérésének engedélyezése](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Egyéni metatár kiválasztása a fürt létrehozása során

A fürt bármikor rámutathat egy korábban létrehozott Azure SQL-adatbázisra. A portálon keresztüli fürtlétrehozásához a beállítás a **Storage > Metastore beállításaiban**adható meg.

![HDInsight Hive Metaadat-tároló Azure-portál](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>A Hive metastore irányelvei

* Amikor csak lehetséges, használjon egyéni metatárolót a számítási erőforrások (a futó fürt) és a metaadatok (a metatárban tárolt) elkülönítéséhez.

* Kezdje egy S2 szinttel, amely 50 DTU és 250 GB tárhelyet biztosít. Ha szűk keresztmetszetet lát, felskálázhatja az adatbázist.

* Ha több HDInsight-fürtet kíván elérni külön adatokhoz, használjon külön adatbázist a metatárhoz az egyes fürtökön. Ha több HDInsight-fürtön is megoszt egy metatárolót, az azt jelenti, hogy a fürtök ugyanazokat a metaadatokat és az alapul szolgáló felhasználói adatfájlokat használják.

* Rendszeresen biztonsági másolatot kell ad az egyéni metaboltról. Az Azure SQL Database automatikusan létrehozza a biztonsági mentéseket, de a biztonsági mentés megőrzési időkerete változó. További információt az [SQL Database automatikus biztonsági mentései című témakörben talál.](../sql-database/sql-database-automated-backups.md)

* Keresse meg a metastore és a HDInsight-fürt ugyanabban a régióban. Ez a konfiguráció biztosítja a legnagyobb teljesítményt és a legalacsonyabb hálózati kimenő díjakat.

* Az Azure SQL Database Monitoring eszközeivel vagy az Azure Monitor naplóival figyelheti a metatároló teljesítményét és rendelkezésre állását.

* Ha az Azure HDInsight egy új, magasabb verziója jön létre egy meglévő egyéni metatár-adatbázissal szemben, a rendszer frissíti a metatár sémáját. A frissítés visszafordíthatatlan anélkül, hogy visszaállítaná az adatbázist a biztonsági másolatból.

* Ha több fürtön osztozik egy metatárolón, győződjön meg arról, hogy az összes fürt ugyanaz a HDInsight-verzió. A különböző Hive-verziók különböző metatár-adatbázissémákat használnak. Például nem oszthat meg egy metatárolót a Hive 2.1 és a Hive 3.1 verziós fürtök között.

* A HDInsight 4.0-s rendszerben a Spark és a Hive független katalógusokat használ a SparkSQL- vagy Hive-táblák eléréséhez. A Spark által létrehozott tábla a Spark-katalógusban él. A Hive által létrehozott tábla a Hive katalógusban él. Ez a viselkedés eltér a HDInsight 3.6-tól, ahol a Hive és a Spark közös katalógust osztott meg. Hive és a Spark-integráció hdinsight 4.0 támaszkodik Hive Warehouse Connector (HWC). A HWC hídként működik a Spark és a Hive között. [További információ a Hive Warehouse Connector ról.](../hdinsight/interactive-query/apache-hive-warehouse-connector.md)

## <a name="apache-oozie-metastore"></a>Apache Oozie metabolt

Az Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Az Oozie támogatja a Hadoop-feladatokat az Apache MapReduce, a Pig, a Hive és mások számára.  Oozie metatárolót használ a munkafolyamatok részleteinek tárolására. Az Oozie használata során a teljesítmény növelése érdekében használhatja az Azure SQL Database-t egyéni metatárolóként. A metatároló hozzáférést biztosít az Oozie-feladat adataihoz a fürt törlése után.

Az Oozie metatároló Azure SQL Database-rel való létrehozásáról az [Apache Oozie használata munkafolyamatokhoz című témakörben talál útmutatást.](hdinsight-use-oozie-linux-mac.md)

## <a name="custom-ambari-db"></a>Egyéni Ambari-adatbázis

Ha saját külső adatbázisát szeretné használni az Apache Ambari segítségével a HDInsight on, olvassa el az [Egyéni Apache Ambari adatbázis](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>További lépések

* [Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával](./hdinsight-hadoop-provision-linux-clusters.md)
