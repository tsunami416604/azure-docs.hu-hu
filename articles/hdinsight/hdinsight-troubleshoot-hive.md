---
title: Hive – problémamegoldás az Azure HDInsight használatával
description: Válaszok az Apache Hive és az Azure HDInsight használatával kapcsolatos gyakori kérdésekre.
keywords: Azure HDInsight, Hive, GYAKORI KÉRDÉSEK, hibaelhárítási útmutató, gyakori kérdések
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 02247adb9852a72b386feb2ef0924b0f1b3d6277
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895234"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Az Apache Hive hibaelhárítása az Azure HDInsighttal

Ismerje meg a legfontosabb kérdéseket és azok megoldásait, amikor apache Ambari Apache Hive-rakományokkal dolgozik.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Hogyan exportálhatok egy Hive metatárolót, és hogyan importálhatom egy másik fürtre?

### <a name="resolution-steps"></a>A megoldás lépései

1. Csatlakozzon a HDInsight-fürthöz egy Biztonságos rendszerhéj (SSH) ügyfél használatával. További információt a További olvasás című témakörben [talál.](#additional-reading-end)

2. Futtassa a következő parancsot azon a HDInsight-fürtön, amelyből exportálni szeretné a metatárolót:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Ez a parancs egy allatables.sql nevű fájlt hoz létre.

3. Másolja az alltables.sql fájlt az új HDInsight-fürtbe, majd futtassa a következő parancsot:

    ```apache
    hive -f alltables.sql
    ```

A megoldási lépésekben lévő kód feltételezi, hogy az új fürt adatelérési útjai megegyeznek a régi fürt adatelérési útvonalával. Ha az adatelérési utak eltérőek, manuálisan `alltables.sql` szerkesztheti a létrehozott fájlt, hogy tükrözze a módosításokat.

### <a name="additional-reading"></a>További olvasás

- [Csatlakozás HDInsight-fürthöz az SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Hogyan találhatom meg a Kaptárnaplókat egy fürtön?

### <a name="resolution-steps"></a>A megoldás lépései

1. Csatlakozzon a HDInsight-fürthöz az SSH használatával. További információt a További olvasás című témakörben **talál.**

2. A Hive-ügyfélnaplók megtekintéséhez használja a következő parancsot:

   ```apache
   /tmp/<username>/hive.log
   ```

3. A Hive metastore-naplók megtekintéséhez használja a következő parancsot:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. A Hive-kiszolgáló naplóinak megtekintéséhez használja a következő parancsot:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>További olvasás

- [Csatlakozás HDInsight-fürthöz az SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Hogyan indíthatom el a Hive-rendszerhéjat adott konfigurációkkal a fürtön?

### <a name="resolution-steps"></a>A megoldás lépései

1. Adja meg a konfigurációs kulcs-érték pár, amikor elindítja a Hive rendszerhéj. További információt a További olvasás című témakörben [talál.](#additional-reading-end)

   ```apache
   hive -hiveconf a=b
   ```

2. A Hive-rendszerhéj összes hatékony konfigurációjának listázásához használja a következő parancsot:

   ```apache
   hive> set;
   ```

   A következő paranccsal például indítsa el a Hive-rendszerhéjat úgy, hogy a hibakeresési naplózás engedélyezve van a konzolon:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>További olvasás

- [Hive konfigurációs tulajdonságai](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Hogyan elemezhetem az Apache Tez DAG adatokat egy fürtkritikus útvonalon?

### <a name="resolution-steps"></a>A megoldás lépései

1. Az Apache Tez irányított aciklikus gráf (DAG) egy fürtkritikus grafikonon, ssh használatával csatlakozhat a HDInsight-fürthöz. További információt a További olvasás című témakörben [talál.](#additional-reading-end)

2. A parancssorban futtassa a következő parancsot:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. A Tez DAG elemzésére használható egyéb elemzők listázásához használja a következő parancsot:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Első argumentumként meg kell adnia egy példaprogramot.

   Az érvényes programnevek a következők:
    - **ContainerReuseAnalyzer**: A tároló újrafelhasználása a DAG-ben
    - **CriticalPath**: Keresse meg a kritikus utat a DAG
    - **LocalityAnalyzer**: A helyadatok nyomtatása dag-ben
    - **ShuffleTimeAnalyzer:** Elemezze a shuffle idő részleteit egy DAG
    - **SkewAnalyzer**: Elemezze a döntés részleteit egy DAG
    - **SlowNodeAnalyzer**: Csomópont részleteinek nyomtatása dag-ban
    - **SlowTaskIdentifier**: Lassú feladatrészletek nyomtatása dag-ban
    - **SlowestVertexAnalyzer**: A leglassabb csúcspont részleteinek nyomtatása dag-ban
    - **SpillAnalyzer:** Print spill részletek egy DAG
    - **TaskConcurrencyAnalyzer**: A feladat egyidejűségi részleteinek nyomtatása dag-ben
    - **VertexLevelCriticalPathAnalyzer**: Keresse meg a kritikus utat csúcspont szinten egy DAG

### <a name="additional-reading"></a>További olvasás

- [Csatlakozás HDInsight-fürthöz az SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Hogyan tölthetem le a Tez DAG adatokat egy fürtből?

#### <a name="resolution-steps"></a>A megoldás lépései

A Tez DAG adatok kétféleképpen gyűjthetők:

- A parancssorból:

    Csatlakozzon a HDInsight-fürthöz az SSH használatával. A parancssorban futtassa a következő parancsot:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Használja az Ambari Tez nézetet:

  1. Menj Ambarihoz.
  2. Nyissa meg a Tez nézetet (a jobb felső sarokban lévő csempék ikonja alatt).
  3. Válassza ki a megtekinteni kívánt dag-ot.
  4. Válassza **az Adatok letöltése**lehetőséget.

### <a name="additional-reading"></a><a name="additional-reading-end"></a>További olvasás

[Csatlakozás HDInsight-fürthöz az SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

- Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

- Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
