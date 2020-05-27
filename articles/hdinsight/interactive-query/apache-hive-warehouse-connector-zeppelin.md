---
title: Kas Warehouse-összekötő – Apache Zeppelin a Livy használatával – Azure HDInsight
description: Ismerje meg, hogyan integrálhatja a méhkas Warehouse-összekötőt az Apache Zeppelin használatával az Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 1f9d2d9bd2a58fa4c6f14db8ffd067bb39fc1553
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853812"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Az Apache Zeppelin integrálása a kaptár Warehouse-összekötővel az Azure HDInsight

A HDInsight Spark-fürtök különböző tolmácsokkal rendelkező Apache Zeppelin-jegyzetfüzeteket tartalmaznak. Ebben a cikkben csak a Livy-tolmácsra koncentrálunk, hogy a Spark-táblákat a méhkas Warehouse-összekötő használatával férhessenek hozzá.

## <a name="prerequisite"></a>Előfeltétel

Fejezze be a [kaptár Warehouse-összekötő telepítési](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) lépéseit.

## <a name="getting-started"></a>Első lépések

1. A Apache Spark-fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az SSH-munkamenetből hajtsa végre a következő parancsot a és a verziójának megjegyzéséhez `hive-warehouse-connector-assembly` `pyspark_hwc` :

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    Mentse a kimenetet későbbi használatra az Apache Zeppelin konfigurálásakor.

## <a name="configure-livy"></a>Livy konfigurálása

A következő konfigurációk szükségesek ahhoz, hogy a Zeppelin-táblákat a Livy értelmező használatával férhessenek hozzá.

### <a name="interactive-query-cluster"></a>Interaktív lekérdezési fürt

1. Egy webböngészőből navigáljon `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs` oda, ahol a LLAPCLUSTERNAME az interaktív lekérdezési fürt neve.

1. Navigáljon a **speciális**  >  **Egyéni Core-site**elemre. Válassza a **tulajdonság hozzáadása...** lehetőséget a következő konfigurációk hozzáadásához:

    | Konfiguráció                 | Érték |
    | ----------------------------- |-------|
    | Hadoop. proxyuser. Livy. groups  | *     |
    | Hadoop. proxyuser. Livy. hosts   | *     |

1. Mentse a módosításokat, és indítsa újra az összes érintett összetevőt.

### <a name="spark-cluster"></a>Spark-fürt

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` oda, ahol a CLUSTERNAME a Apache Spark-fürt neve.

1. Bontsa ki az **Egyéni livy2-conf**elemet. Válassza a **tulajdonság hozzáadása...** lehetőséget a következő konfiguráció hozzáadásához:

    | Konfiguráció                 | Érték                                      |
    | ----------------------------- |------------------------------------------  |
    | Livy. file. local-dir-engedélyezési lista | /usr/HDP/current/hive_warehouse_connector/ |

1. Mentse a módosításokat, és indítsa újra az összes érintett összetevőt.

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Livy-értelmező konfigurálása Zeppelin felhasználói felületen (Spark-fürt)

1. Egy böngészőben nyissa meg a (z `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter` ) elemet, ahol a a `CLUSTERNAME` Apache Spark-fürt neve.

1. Navigáljon a **livy2**.

1. Adja hozzá a következő konfigurációkat:

    | Konfiguráció                 | Érték                                      |
    | ----------------------------- |:------------------------------------------:|
    | Livy. Spark. Hadoop. kaptár. llap. Daemon. Service. hosts | @llap0 |
    | Livy. Spark. Security. hitelesítő adatok. hiveserver2. enabled | igaz |
    | Livy. Spark. SQL. kaptár. llap | igaz |
    | Livy. Spark. fonál. Security. hitelesítő adatok. hiveserver2. enabled | igaz |
    | Livy. főfelhasználók | Livy, Zeppelin |
    | Livy. Spark. tégelyek | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>Cserélje le a verziót az [első lépésekből](#getting-started)beszerzett értékekre. |
    | Livy. Spark. submit. pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>Cserélje le a verziót az [első lépésekből](#getting-started)beszerzett értékekre. |
    | Livy. Spark. SQL. Kas. hiveserver2. JDBC. URL | Állítsa be az interaktív lekérdezési fürt HiveServer2 interaktív JDBC URL-címére. |
    | Spark. Security. hitelesítő adatok. hiveserver2. enabled | igaz |

1. Csak ESP-fürtök esetén adja hozzá a következő konfigurációt:

    | Konfiguráció| Érték|
    |---|---|
    | Livy. Spark. SQL. Kas. hiveserver2. JDBC. URL. Principal | `hive/<headnode-FQDN>@<AAD-Domain>` |

    Cserélje le az értékét az `<headnode-FQDN>` interaktív lekérdezési fürt fő csomópontjának teljes tartománynevére.
    Cserélje le a helyére annak a `<AAD-DOMAIN>` Azure Active Directorynak (HRE) a nevét, amelyhez a fürt csatlakozik. Használjon nagybetűs karakterláncot az `<AAD-DOMAIN>` értékhez, ellenkező esetben a hitelesítő adat nem található. `/etc/krb5.conf`Szükség esetén ellenőrizze a tartománynevek nevét.

1. Mentse a módosításokat, és indítsa újra a Livy-tolmácsot.

Ha a Livy-értelmező nem érhető el, módosítsa a `shiro.ini` Zeppelin-összetevőn belüli fájlt a Ambari-ben. További információ: az [Apache Zeppelin biztonságának konfigurálása](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html).  


## <a name="running-queries-in-zeppelin"></a>Lekérdezések futtatása a Zeppelin-ben 

Zeppelin-jegyzetfüzet elindítása a Livy-értelmező használatával és a következők végrehajtása

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>További lépések

* [ÜZEMELTETHETŐ WEBMAG és Apache Spark műveletek](./apache-hive-warehouse-connector-operations.md)
* [ÜZEMELTETHETŐ WEBMAG-integráció Apache Spark és Apache Hive](./apache-hive-warehouse-connector.md)
* [Az interaktív lekérdezés használata HDInsighttal](./apache-interactive-query-get-started.md)