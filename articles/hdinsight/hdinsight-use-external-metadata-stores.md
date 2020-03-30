---
title: Külső metaadat-tárolók használata – Azure HDInsight
description: Külső metaadat-tárolók at használhat az Azure HDInsight-fürtökkel és gyakorlati tanácsokkal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: edb2d256d3e5d98c52dbdff1162e0e030ebe2be3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272161"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Külső metaadattárak használata az Azure HDInsightban

A HDInsight lehetővé teszi az adatok és a metaadatok vezérlését a kulcsfontosságú metaadat-megoldások és felügyeleti adatbázisok külső adattárakba való telepítésével. Ez a funkció jelenleg elérhető [az Apache Hive metastore,](#custom-metastore) [Apache Oozie metastore](#apache-oozie-metastore) és [Apache Ambari adatbázis.](#custom-ambari-db)

A HDInsight Apache Hive metastore-ja az Apache Hadoop architektúra alapvető része. A metatároló a központi sématár, amely más big data-hozzáférési eszközök, például az Apache Spark, az Interactive Query (LLAP), a Presto vagy az Apache Pig által használható központi sématár. A HDInsight egy Azure SQL-adatbázist használ a Hive metastore-ként.

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
Ez az alapértelmezett metatár általában viszonylag egyszerű számítási feladatokhoz használatos, amelyek nem igényelnek több fürtöt, és nem kell a fürt életciklusán túl megőrződött metaadatokat.

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

Létre kell hoznia, vagy egy meglévő Azure SQL-adatbázis beállítása előtt egy egyéni Hive metastore egy HDInsight-fürthöz.  További információ: [Rövid útmutató: Egyetlen adatbázis létrehozása az Azure SQL DB-ben című témakörben.](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

Annak érdekében, hogy a HDInsight-fürt hozzáférhessen a csatlakoztatott Azure SQL Database-adatbázishoz, konfigurálja az Azure SQL Database tűzfalszabályait, hogy az Azure-szolgáltatások és -erőforrások hozzáférhessenek a kiszolgálóhoz.

Ezt a beállítást az Azure Portalon engedélyezheti a **Kiszolgáló tűzfalának beállítása**gombra **kattintva,** majd az **Azure-szolgáltatások és -erőforrások engedélyezése** az Azure-szolgáltatások és -erőforrások alatt az Azure SQL Database-kiszolgáló vagy -adatbázis kiszolgálóeléréséhez. További információt az [IP-tűzfalszabályok létrehozása és kezelése](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) című témakörben talál.

![kiszolgáló tűzfalának beállítása gomb](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![az Azure-szolgáltatások elérésének engedélyezése](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Egyéni metatár kiválasztása a fürt létrehozása során

A fürt egy korábban létrehozott Azure SQL-adatbázisra irányíthatja a fürt létrehozása során, vagy konfigurálhatja az SQL-adatbázist a fürt létrehozása után. Ez a beállítás a **Storage > Metastore-beállításokkal** van megadva, miközben új Hadoop, Spark vagy interaktív Hive-fürtöt hoz létre az Azure Portalról.

![HDInsight Hive Metaadat-tároló Azure-portál](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>A Hive metastore ajánlott gyakorlatai

Íme néhány általános HDInsight Hive metastore gyakorlati tanácsok:

* Amikor csak lehetséges, használjon egyéni metatárolót a számítási erőforrások (a futó fürt) és a metaadatok (a metatárban tárolt) elkülönítéséhez.

* Kezdje egy S2 szinttel, amely 50 DTU és 250 GB tárhelyet biztosít. Ha szűk keresztmetszetet lát, felskálázhatja az adatbázist.

* Ha több HDInsight-fürtet kíván elérni külön adatokhoz, használjon külön adatbázist a metatárhoz az egyes fürtökön. Ha több HDInsight-fürtön is megoszt egy metatárolót, az azt jelenti, hogy a fürtök ugyanazokat a metaadatokat és az alapul szolgáló felhasználói adatfájlokat használják.

* Rendszeresen biztonsági másolatot kell ad az egyéni metaboltról. Az Azure SQL Database automatikusan létrehozza a biztonsági mentéseket, de a biztonsági mentés megőrzési időkerete változó. További információt az [SQL Database automatikus biztonsági mentései című témakörben talál.](../sql-database/sql-database-automated-backups.md)

* Keresse meg a metastore és hdinsight-fürtugyanabban a régióban, a legnagyobb teljesítmény és a legalacsonyabb hálózati kimenő díjak érdekében.

* Az Azure SQL Database Monitoring eszközeivel, például az Azure Portalon vagy az Azure Monitor-naplókban figyelheti a metastore teljesítményét és rendelkezésre állását.

* Ha az Azure HDInsight egy új, magasabb verziója jön létre egy meglévő egyéni metatár-adatbázissal szemben, a rendszer frissíti a metatár sémáját, amely visszafordíthatatlan az adatbázis biztonsági mentésből való visszaállítása nélkül.

* Ha több fürtön osztozik egy metatárolón, győződjön meg arról, hogy az összes fürt ugyanaz a HDInsight-verzió. A különböző Hive-verziók különböző metatár-adatbázissémákat használnak. Például nem oszthat meg egy metatárolót a Hive 2.1 és a Hive 3.1 verziós fürtök között.

* A HDInsight 4.0-s rendszerben a Spark és a Hive független katalógusokat használ a SparkSQL- vagy Hive-táblák eléréséhez. A Spark által létrehozott tábla a Spark-katalógusban található. A Hive által létrehozott tábla a Hive katalógusban található. Ez eltér a HDInsight 3.6-tól, ahol a Hive és a Spark közös katalógust osztott meg. Hive és a Spark-integráció hdinsight 4.0 támaszkodik Hive Warehouse Connector (HWC). A HWC hídként működik a Spark és a Hive között. [További információ a Hive Warehouse Connector ról.](../hdinsight/interactive-query/apache-hive-warehouse-connector.md)

## <a name="apache-oozie-metastore"></a>Apache Oozie metabolt

Az Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli.  Az Oozie támogatja a Hadoop-feladatokat az Apache MapReduce, a Pig, a Hive és mások számára.  Oozie metatárolót használ az aktuális és befejezett munkafolyamatok részleteinek tárolására. Az Oozie használata során a teljesítmény növelése érdekében használhatja az Azure SQL Database-t egyéni metatárolóként. A metatár is hozzáférést biztosít az Oozie-feladat adataihoz a fürt törlése után.

Az Oozie metatároló Azure SQL Database-rel való létrehozásáról az [Apache Oozie használata munkafolyamatokhoz című témakörben talál útmutatást.](hdinsight-use-oozie-linux-mac.md)

## <a name="custom-ambari-db"></a>Egyéni Ambari-adatbázis

Ha saját külső adatbázisát szeretné használni az Apache Ambari segítségével a HDInsight on, olvassa el az [Egyéni Apache Ambari adatbázis](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>További lépések

* [Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával](./hdinsight-hadoop-provision-linux-clusters.md)
