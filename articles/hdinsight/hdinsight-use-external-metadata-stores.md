---
title: Külső metaadat-tárolók használata – Azure HDInsight
description: Külső metaadat-tárolókat használhat az Azure HDInsight-fürtökkel és az ajánlott eljárásokkal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: a876269b2746a1065cee2639cfc5804aff0b3446
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027706"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Külső metaadat-tárolók használata az Azure HDInsight

A HDInsight Apache Hive metaadattár a Apache Hadoop architektúra alapvető részét képezi. A metaadattár a központi séma tárháza, amelyet más big data olyan hozzáférési eszközök is használhatnak, mint például a Apache Spark, az interaktív lekérdezés (LLAP), a Presto vagy az Apache Pig. A HDInsight Azure SQL Database használ Hive-metaadattárként.

![HDInsight struktúra metaadat-tárolójának architektúrája](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

A HDInsight-fürtökhöz kétféleképpen állíthat be metaadattár:

* [Alapértelmezett metaadattár](#default-metastore)
* [Egyéni metaadattár](#custom-metastore)

## <a name="default-metastore"></a>Alapértelmezett metaadattár

Alapértelmezés szerint a HDInsight létrehoz egy metaadattár minden fürt típussal. Ehelyett egyéni metaadattár is megadhat. Az alapértelmezett metaadattár a következő szempontokat tartalmazza:

* Nincs további díj. A HDInsight minden metaadattár létrehoz egy, a további díjak nélkül.

* Az alapértelmezett metaadattár a fürt életciklusának részét képezik. Amikor töröl egy fürtöt, a rendszer a megfelelő metaadattár és metaadatokat is törli.

* Az alapértelmezett metaadattár nem oszthatók meg más fürtökkel.

* Az alapértelmezett metaadattár az alapszintű Azure SQL DB-t használja, amely öt DTU (adatbázis-tranzakciós egység) korlátot tartalmaz.
Ez az alapértelmezett metaadattár jellemzően olyan viszonylag egyszerű munkaterhelésekhez használatos, amelyek nem igényelnek több fürtöt, és nincs szükségük a fürt életciklusán kívül megőrizni kívánt metaadatokra.

## <a name="custom-metastore"></a>Egyéni metaadattár

A HDInsight az éles fürtökhöz ajánlott egyéni metaadattárak is támogatja:

* Saját Azure SQL Database kell megadnia a metaadattár.

* A metaadattár életciklusa nem kötődik a fürtök életciklusához, így a metaadatok elvesztése nélkül hozhat létre és törölhet fürtöket. Az olyan metaadatok, mint például a struktúra sémái a HDInsight-fürt törlését és újbóli létrehozását követően is megmaradnak.

* Az egyéni metaadattár több fürtöt és fürtöt is csatlakoztathat ehhez a metaadattár. Például egyetlen metaadattár oszthatók meg az interaktív lekérdezés, a kaptár és a Spark-fürtök között a HDInsight-ben.

* Az Ön által választott teljesítményszint alapján kell fizetnie a metaadattár (Azure SQL DB) költségeiért.

* Igény szerint méretezheti a metaadattár.

![HDInsight-struktúra metaadatainak tárolójának használati esete](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Egyéni metaadattár kiválasztása a fürt létrehozása során

A fürt létrehozása során egy korábban létrehozott Azure SQL Databasere irányíthatja a fürtöt, vagy beállíthatja a SQL Databaset a fürt létrehozása után. Ez a beállítás a **Storage > metaadattár beállításaival** van megadva, miközben új Hadoop, Spark vagy interaktív kaptár-fürtöt hoz létre a Azure Portal.

![HDInsight struktúra metaadatainak tárolója Azure Portal](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

Hozzáadhat további fürtöket Azure Portal vagy Ambari konfigurációkból származó egyéni metaadattár is (a kaptár > Advanced)

![HDInsight-struktúra metaadat-tárolójának Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Hive-metaadattár ajánlott eljárások

Az alábbi általános HDInsight Hive-metaadattár ajánlott eljárások:

* Ha lehetséges, használjon egyéni metaadattár, hogy elkülönítse a számítási erőforrásokat (a futó fürtöt) és a metaadatokat (a metaadattár tárolva).

* Első lépésként egy S2 szintű, amely 50 DTU és 250 GB tárterületet biztosít. Ha szűk keresztmetszet jelenik meg, az adatbázis felskálázása felfelé is elvégezhető.

* Ha több HDInsight-fürthöz is szeretne hozzáférni, használjon külön adatbázist az egyes fürtök metaadattár. Ha több HDInsight-fürtön keresztül oszt meg egy metaadattár, az azt jelenti, hogy a fürtök ugyanazokat a metaadatokat és a mögöttes felhasználói adatfájlokat használják.

* Rendszeresen készítse elő az egyéni metaadattár biztonsági mentését. Azure SQL Database automatikusan készít biztonsági másolatokat, de a biztonsági mentés megőrzési időkerete változó. További információ: az [automatikus SQL Database biztonsági mentések](../sql-database/sql-database-automated-backups.md)ismertetése.

* Keresse meg a metaadattár és a HDInsight-fürtöt ugyanabban a régióban, a legnagyobb teljesítmény és a legalacsonyabb hálózati forgalom díja mellett.

* A metaadattár a teljesítmény és a rendelkezésre állás figyelésére Azure SQL Database monitorozási eszközök, például a Azure Portal vagy a Azure Monitor naplók használatával.

* Ha új, újabb Azure HDInsight-verziót hoz létre egy meglévő egyéni metaadattár-adatbázison, a rendszer frissíti a metaadattár sémáját, amely az adatbázis biztonsági másolatból való visszaállítása nélkül visszafordíthatatlan.

* Ha több fürtön keresztül oszt meg egy metaadattár, győződjön meg arról, hogy az összes fürt ugyanazt a HDInsight-verziót használja. A különböző kaptár-verziók eltérő metaadattár-adatbázis-sémákat használnak. Például nem oszthat meg metaadattár a kaptár 2,1 és a kaptár 3,1 verziójú fürtök között.

* A HDInsight 4,0-ben a Spark és a kaptár független katalógusokat használ a SparkSQL-vagy kaptár-táblák eléréséhez. A Spark katalógusában a Spark által létrehozott tábla található. Egy struktúra által létrehozott tábla a kaptár-katalógusban található. Ez eltér a HDInsight 3,6-nél, ahol a kaptár és a Spark közös közös katalógusa. A HDInsight 4,0 struktúra és Spark integrációja a kaptár Warehouse-összekötőre (ÜZEMELTETHETŐ WEBMAG) támaszkodik. A ÜZEMELTETHETŐ WEBMAG a Spark és a kaptár közötti hídként működik. Tudnivalók [a kaptár-tárház összekötőről](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie metaadattár

Az Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli.  A Oozie támogatja az Apache MapReduce, a Pig, a kaptár és mások Hadoop-feladatait.  A Oozie metaadattár használ az aktuális és befejezett munkafolyamatok adatainak tárolására. A Oozie használata esetén a teljesítmény növeléséhez használhatja a Azure SQL Databaset egyéni metaadattár. A metaadattár a fürt törlése után is biztosíthat hozzáférést a Oozie-feladatok adataihoz.

A Oozie-metaadattár Azure SQL Database használatával történő létrehozásával kapcsolatos utasításokért lásd: [az Apache Oozie használata a munkafolyamatokhoz](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Következő lépések

* [Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával](./hdinsight-hadoop-provision-linux-clusters.md)
