---
title: Külső metaadat-tárolók használata – Azure HDInsight
description: Külső metaadat-tárolók használata az Azure HDInsight-fürtökkel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/30/2020
ms.openlocfilehash: d956a9c93280ac22c4707f22c0769853f0f36c83
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84015148"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Külső metaadattárak használata az Azure HDInsightban

A HDInsight lehetővé teszi az adatok és a metaadatok külső adattárral való felügyeletét. Ez a funkció [Apache Hive metaadattár](#custom-metastore), [Apache Oozie Metaadattár](#apache-oozie-metastore)és [Apache Ambari adatbázishoz](#custom-ambari-db)érhető el.

A HDInsight Apache Hive metaadattár a Apache Hadoop architektúra alapvető részét képezi. A metaadattár a központi séma tárháza. A metaadattár más big data hozzáférési eszközök, például a Apache Spark, az interaktív lekérdezés (LLAP), a Presto vagy az Apache Pig használják. A HDInsight Azure SQL Database használ Hive-metaadattárként.

![HDInsight struktúra metaadat-tárolójának architektúrája](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

A HDInsight-fürtökhöz kétféleképpen állíthat be metaadattár:

* [Alapértelmezett metaadattár](#default-metastore)
* [Egyéni metaadattár](#custom-metastore)

## <a name="default-metastore"></a>Alapértelmezett metaadattár

Alapértelmezés szerint a HDInsight létrehoz egy metaadattár minden fürt típussal. Ehelyett egyéni metaadattár is megadhat. Az alapértelmezett metaadattár a következő szempontokat tartalmazza:

* Nincs további díj. A HDInsight minden metaadattár létrehoz egy, a további díjak nélkül.

* Az alapértelmezett metaadattár a fürt életciklusának részét képezik. Amikor töröl egy fürtöt, a rendszer a megfelelő metaadattár és metaadatokat is törli.

* Az alapértelmezett metaadattár nem oszthatók meg más fürtökkel.

* Az alapértelmezett metaadattár az alapszintű Azure SQL Database, amely öt DTU (adatbázis-tranzakciós egység) korlátot használ.
Ez az alapértelmezett metaadattár jellemzően viszonylag egyszerű számítási feladatokhoz használatos. Olyan munkaterhelések, amelyek nem igényelnek több fürtöt, és nem kell megőrizniük a fürt életciklusán túli metaadatokat.

* Éles számítási feladatokhoz ajánlott áttelepíteni egy külső metaadattár. További részletekért tekintse meg az alábbi szakaszt.

## <a name="custom-metastore"></a>Egyéni metaadattár

A HDInsight az éles fürtökhöz ajánlott egyéni metaadattárak is támogatja:

* Saját Azure SQL Database kell megadnia a metaadattár.

* A metaadattár életciklusa nem kötődik a fürtök életciklusához, így a metaadatok elvesztése nélkül hozhat létre és törölhet fürtöket. Az olyan metaadatok, mint például a struktúra sémái a HDInsight-fürt törlését és újbóli létrehozását követően is megmaradnak.

* Az egyéni metaadattár több fürtöt és fürtöt is csatlakoztathat ehhez a metaadattár. Például egyetlen metaadattár oszthatók meg az interaktív lekérdezés, a kaptár és a Spark-fürtök között a HDInsight-ben.

* Egy metaadattár (Azure SQL Database) költségeiért kell fizetnie a választott teljesítményszint alapján.

* Igény szerint méretezheti a metaadattár.

* A fürtöt és a külső metaadattár ugyanabban a régióban kell üzemeltetni.

![HDInsight-struktúra metaadatainak tárolójának használati esete](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Az egyéni metaadattár létrehozási és konfigurációs Azure SQL Database

Hozzon létre vagy rendelkezzen meglévő Azure SQL Database a HDInsight-fürthöz tartozó egyéni Hive-metaadattár beállítása előtt.  További információ: gyors útmutató [: önálló adatbázis létrehozása Azure SQL Databaseban](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

A fürt létrehozása során a HDInsight szolgáltatásnak csatlakoznia kell a külső metaadattár, és ellenőriznie kell a hitelesítő adatait. Azure SQL Database tűzfalszabályok konfigurálásával engedélyezheti az Azure-szolgáltatások és-erőforrások számára a kiszolgáló elérését. Engedélyezze ezt a beállítást a Azure Portal a **kiszolgáló tűzfalának beállítása**elem kiválasztásával. Ezután válassza a **nincs** a **nyilvános hálózati hozzáférés megtagadása**alatt lehetőséget, és **Igen** , az **Azure-szolgáltatások és-erőforrások engedélyezése alatt a kiszolgálóhoz való hozzáféréshez** Azure SQL Database. További információt az [IP-Tűzfalszabályok létrehozásával és kezelésével](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) foglalkozó témakörben talál.

Az SQL-áruházakhoz tartozó magánhálózati végpontok nem támogatottak.

![kiszolgáló tűzfalának beállítása gomb](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![Azure-szolgáltatások hozzáférésének engedélyezése](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Egyéni metaadattár kiválasztása a fürt létrehozása során

A fürtöt egy korábban létrehozott Azure SQL Databasere irányíthatja bármikor. A portálon keresztüli fürt létrehozásához a beállítás a **Storage > metaadattár beállításainál**adható meg.

![HDInsight struktúra metaadatainak tárolója Azure Portal](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Hive-metaadattár irányelvek

* Ha lehetséges, használjon egyéni metaadattár, hogy elkülönítse a számítási erőforrásokat (a futó fürtöt) és a metaadatokat (a metaadattár tárolva).

* Első lépésként egy S2 szintű, amely 50 DTU és 250 GB tárterületet biztosít. Ha szűk keresztmetszet jelenik meg, az adatbázis felskálázása felfelé is elvégezhető.

* Ha több HDInsight-fürthöz is szeretne hozzáférni, használjon külön adatbázist az egyes fürtök metaadattár. Ha több HDInsight-fürtön keresztül oszt meg egy metaadattár, az azt jelenti, hogy a fürtök ugyanazokat a metaadatokat és a mögöttes felhasználói adatfájlokat használják.

* Rendszeresen készítse elő az egyéni metaadattár biztonsági mentését. Azure SQL Database automatikusan készít biztonsági másolatokat, de a biztonsági mentés megőrzési időkerete változó. További információ: az [automatikus SQL Database biztonsági mentések](../azure-sql/database/automated-backups-overview.md)ismertetése.

* Keresse meg a metaadattár és a HDInsight-fürtöt ugyanabban a régióban. Ez a konfiguráció biztosítja a legmagasabb teljesítmény és a legalacsonyabb hálózati kimenő forgalom díját.

* A metaadattár a teljesítmény és a rendelkezésre állás figyeléséhez Azure SQL Database monitorozási eszközök vagy Azure Monitor naplók használatával.

* Amikor új, újabb Azure HDInsight-verziót hoz létre egy meglévő egyéni metaadattár-adatbázison, a rendszer frissíti a metaadattár sémáját. A frissítés visszafordíthatatlan az adatbázis biztonsági másolatból való visszaállítása nélkül.

* Ha több fürtön keresztül oszt meg egy metaadattár, győződjön meg arról, hogy az összes fürt ugyanazt a HDInsight-verziót használja. A különböző kaptár-verziók eltérő metaadattár-adatbázis-sémákat használnak. Például nem oszthat meg metaadattár a kaptár 2,1 és a kaptár 3,1 verziójú fürtök között.

* A HDInsight 4,0-ben a Spark és a kaptár független katalógusokat használ a SparkSQL-vagy kaptár-táblák eléréséhez. A Spark-katalógusban a Spark által létrehozott tábla él. A kaptárak által létrehozott tábla a kaptár-katalógusban él. Ez a viselkedés eltér a HDInsight 3,6-nél, ahol a kaptár és a Spark közös katalógusa található. A HDInsight 4,0 struktúra és Spark integrációja a kaptár Warehouse-összekötőre (ÜZEMELTETHETŐ WEBMAG) támaszkodik. A ÜZEMELTETHETŐ WEBMAG a Spark és a kaptár közötti hídként működik. Tudnivalók [a kaptár-tárház összekötőről](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

* A HDInsight 4,0-ben, ha meg szeretné osztani a kaptár és a Spark közötti metaadattár, ehhez módosítsa a metaadattár. Catalog. default tulajdonságot a Spark-fürtben található struktúrára. Ezt a tulajdonságot a Ambari Advanced spark2-kaptár-site-felülbírálásban találja. Fontos tisztában lenni azzal, hogy a metaadattár megosztása csak a külső struktúra táblái esetében működik, ez nem fog működni, ha belső/felügyelt struktúra-táblákat vagy savas táblákat tartalmaz.  

## <a name="apache-oozie-metastore"></a>Apache Oozie metaadattár

Az Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli. A Oozie támogatja az Apache MapReduce, a Pig, a kaptár és mások Hadoop-feladatait.  A Oozie metaadattár használ a munkafolyamatok részleteinek tárolására. A Oozie használata esetén a teljesítmény növeléséhez használhatja a Azure SQL Databaset egyéni metaadattár. A metaadattár a fürt törlése után hozzáférést biztosít a Oozie-feladatok adataihoz.

A Oozie-metaadattár Azure SQL Database használatával történő létrehozásával kapcsolatos utasításokért lásd: [az Apache Oozie használata a munkafolyamatokhoz](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Egyéni Ambari-adatbázis

Ha saját külső adatbázist szeretne használni az Apache Ambari a HDInsight-on, tekintse meg az [Egyéni Apache Ambari-adatbázist](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>További lépések

* [Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával](./hdinsight-hadoop-provision-linux-clusters.md)
