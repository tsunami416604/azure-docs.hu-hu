---
title: Az Azure HDInsight kibocsátási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 7c31520efd881e8e0b5ed309f62d273bac59c0e3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945029"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információ: [HDInsight verziószámozási cikk](hdinsight-component-versioning.md).

## <a name="summary"></a>Összegzés

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében a nyílt forráskódú Apache Hadoop és az Azure-beli Apache Spark-elemzések terén.

## <a name="new-features"></a>Új funkciók

A HDInsight 4,0-es verzióval kapcsolatos fontos változásokkal kapcsolatos további információkért lásd: a [HDI 4,0 újdonságai?](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Összetevő-verziók

Az összes HDInsight 4,0-összetevő hivatalos Apache-verziója alább található. A felsorolt összetevők a legújabb stabil verziók kiadásait tartalmazzák.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1, 2.1.0
- Apache Mahout 0.9.0 +
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1, 2.4.0
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Az Apache-összetevők újabb verziói néha a HDP eloszlásba vannak csomagolva, a fent felsorolt verziók mellett. Ebben az esetben ezek a későbbi verziók a Technical Previews táblában vannak felsorolva, és nem helyettesíthetik a fenti lista Apache összetevő-verzióit éles környezetben.

## <a name="apache-patch-information"></a>Apache-javítási információk

A HDInsight 4,0-ben elérhető javításokról további információt az alábbi táblázatban talál az egyes termékekhez tartozó patch-lista.

| Terméknév | Javítási információk |
|---|---|
| Ambari | [Ambari-javítási információk](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Hadoop-javítási információk](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [HBase-javítási információk](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Ez a kiadás további Apache-javítások nélkül biztosít kaptár-3.1.0.  |
| Kafka | Ebben a kiadásban a Kafka 1.1.1-es verziója további Apache-javítások nélkül elérhető. |
| Oozie | [Oozie-javítási információk](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [A Phoenix javításával kapcsolatos információk](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Pig-javítási információk](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Ranger-javítási információk](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Spark-javítási információk](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Ez a kiadás további Apache-javítások nélkül biztosít Sqoop 1.4.7. |
| Tez | Ez a kiadás további Apache-javítások nélkül biztosít TEZ 0.9.1. |
| Zeppelin | Ez a kiadás a Zeppelin 0.8.0 további Apache-javítások nélkül nyújtja. |
| Zookeeper | [Zookeeper-javítási információk](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Rögzített gyakori sebezhetőségek és kitettségek

További információ az ebben a kiadásban megoldott biztonsági problémákról: Hortonworks ' a [HDP 3.0.1 rögzített gyakori biztonsági rései és a kitettségek](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Ismert problémák

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>A biztonságos HBase a replikáció alapértelmezett telepítéssel megszakadt

A HDInsight 4,0 esetében hajtsa végre a következő lépéseket:

1. Fürt közötti kommunikáció engedélyezése.
1. Jelentkezzen be az aktív átjárócsomóponthoz.
1. Töltsön le egy parancsfájlt a replikáció engedélyezéséhez a következő paranccsal:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Írja be a `sudo kinit <domainuser>`parancsot.
1. A parancsfájl futtatásához írja be a következő parancsot:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
A HDInsight 3,6 esetében tegye a következőket:

1. Jelentkezzen be az aktív HMaster ZK.
1. Töltsön le egy parancsfájlt a replikáció engedélyezéséhez a következő paranccsal:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Írja be a `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`parancsot.
1. Írja be a következő parancsot:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>A Phoenix az sqlline használata a HBase-fürt HDInsight 4,0-re való áttelepítése után leáll

Hajtsa végre a következő lépéseket:

1. Dobja el a következő Phoenix-táblákat:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Ha nem tudja törölni a táblák bármelyikét, indítsa újra a HBase a táblák kapcsolatainak törléséhez.
1. Futtassa ismét az `sqlline.py` parancsot. A Phoenix újra létrehozza az 1. lépésben törölt összes táblát.
1. A HBase-adataihoz tartozó Phoenix-táblák és-nézetek újbóli előállítása.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>A Phoenix az sqlline használata a HBase Phoenix metaadatainak replikálását követően leáll a HDInsight 3,6 – 4,0

Hajtsa végre a következő lépéseket:

1. A replikáció előtt lépjen a cél 4,0 fürtre, és hajtsa `sqlline.py`végre a műveletet. Ez a parancs olyan Phoenix- `SYSTEM.MUTEX` táblákat `SYSTEM.LOG` hoz majd, amelyek csak a 4,0-es verzióban léteznek.
1. Dobja el a következő táblázatokat:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. A HBase-replikáció elindítása

## <a name="deprecation"></a>Elavulás

A Apache Storm és a ML szolgáltatás nem érhető el a HDInsight 4,0-ben.
