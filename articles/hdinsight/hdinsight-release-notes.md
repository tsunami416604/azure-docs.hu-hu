---
title: Az Azure HDInsight kibocsátási megjegyzései
description: Legújabb kibocsátási megjegyzések az Azure HDInsight. Hadoop, Spark, az R Server, Hive és további fejlesztési tippek és részletek szerezze be.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 6ba9e77faeb297f9862b39384d397b478dc7cf36
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617733"
---
# <a name="release-notes-for-azure-hdinsight"></a>Az Azure HDInsight kibocsátási megjegyzései

Ez a cikk a kapcsolatos információkat nyújt a **legutóbbi** Azure HDInsight kibocsátási frissítéseket. A korábbi operációs információkért lásd: [HDInsight kibocsátási megjegyzései archív](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight versioning cikk](hdinsight-component-versioning.md).

## <a name="summary"></a>Összegzés

Az Azure HDInsight a legnépszerűbb szolgáltatások nagyvállalati ügyfelek körében a nyílt forráskódú Apache Spark és Apache Hadoop analytics az Azure-ban egyike.

## <a name="new-features"></a>Új funkciók

További információ a HDInsight 4.0-s fontos változásokat., lásd: [HDI 4.0 újdonságai?](../hdinsight/hdinsight-version-release.md#whats-new-in-hdi-40).

## <a name="component-versions"></a>Összetevő-verziók

Az összes HDInsight 4.0 összetevők hivatalos Apache verzióját az alábbi táblázatban szerepelnek. A felsorolt összetevői kiadásaiban elérhető legújabb stabil verziója.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Az Apache HBase 2.0.0-s
- Az Apache Hive 3.1.0
- Apache Kafka 1.1.1
- Az Apache Mahout 0.9.0+
- Az Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Az Apache Ranger 0.7.0
- Az Apache csúszka 0.92.0
- Apache Spark 2.3.1
- Apache Sqoop 1.4.7
- Az Apache TEZ 0.9.1
- Az Apache Zeppelin 0.8.0
- Az Apache ZooKeeper 3.4.6

Apache összetevők későbbi verziói néha mellett a fent felsorolt verziói HDP terjesztési vannak csoportosítva. Ebben az esetben ezen újabb verziói a Technical Preview kiadások táblázatban, és az Apache összetevő verziója, az éles környezetben a fenti lista nem helyettesítse.

## <a name="apache-patch-information"></a>Az Apache javítás információk

Javítások a HDInsight 4.0 elérhető további információkért tekintse meg a javítás, az alábbi táblázat az egyes termékekhez tartozó listázása.

| Terméknév | Információ a javítás |
|---|---|
| Ambari | [Az Ambari javítás információk](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Információ a Hadoop-javítás](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [Információ a HBase-javítás](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Ebben a kiadásban a további Apache javítások 3.1.0 Hive biztosít.  |
| Kafka | Ebben a kiadásban 1.1.1 további Apache javítások a Kafka. |
| Oozie | [Információ az Oozie-javítások](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Információ a Phoenix-javítás](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Információ a Pig-javítás](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Információ a ranger-javítás](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Információ a Spark-javítás](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Ebben a kiadásban a Sqoop, a további Apache javítások 1.4.7. |
| Tez | Ebben a kiadásban további Apache javítások a 0.9.1 Tez biztosít. |
| Zeppelin | Ebben a kiadásban a további Apache javítások 0.8.0 Zeppelin biztosít. |
| Zookeeper | [Információ a zookeeper-javítás](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Kijavítva a Common Vulnerabilities and Exposures

További információ a biztonsági problémák megoldása a jelen kiadás, tekintse meg a Hortonworks [rögzített Common Vulnerabilities and Exposures a HDP 3.0.1-es](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Ismert problémák

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>Replikációs megszakad a biztonságos hbase-hez az alapértelmezett telepítés

HDInsight 4.0-s tegye a következőket:

1. A fürt közötti kommunikáció engedélyezése.
1. Jelentkezzen be az aktív átjárócsomópontjával.
1. Töltse le egy parancsfájlt, hogy engedélyezze a replikációt a következő paranccsal:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Írja be a parancsot `sudo kinit <domainuser>`.
1. Írja be a parancsfájl futtatásához az alábbi parancsot:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
A HDInsight 3.6-os tegye a következőket:

1. Jelentkezzen be aktív HMaster ZK.
1. Töltse le egy parancsfájlt, hogy engedélyezze a replikációt a következő paranccsal:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Írja be a parancsot `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Írja be a következő parancsot:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>A Phoenix az Sqlline való migrálása után leáll a 4.0-s HDInsight HBase-fürt

Kövesse az alábbi lépéseket:

1. Dobja el a következő Phoenix táblák:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Ha a tábla nem törölhető, indítsa újra a HBase törölni fennálló kapcsolatokat a táblák.
1. Futtassa ismét az `sqlline.py` parancsot. Phoenix újra létrehozza az összes táblát, amely az 1. lépésben törölve lett.
1. A Phoenix-táblák és nézetek, a HBase-adatok újbóli létrehozása.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>A Phoenix az Sqlline HBase Phoenix metaadatok replikálása a HDInsight 3.6-os 4.0-s után leáll

Kövesse az alábbi lépéseket:

1. Mielőtt végrehajtaná a replikálást, nyissa meg a célfürt 4.0-s verzióját, és hajtsa végre `sqlline.py`. Ez a parancs létrehozza a Phoenix táblákkal, mint `SYSTEM.MUTEX` és `SYSTEM.LOG` , amely csak a 4.0-s létezik.
1. Dobja el az alábbi táblázatokban:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. A HBase-replikáció

## <a name="deprecation"></a>Elavulás

Apache Storm és a Machine Learning-szolgáltatások nem érhetők el a HDInsight 4.0-s verzióját.
