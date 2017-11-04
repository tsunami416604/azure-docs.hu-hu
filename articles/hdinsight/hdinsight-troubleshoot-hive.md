---
title: "Azure HDInsight Hive hibaelhárításáról |} Microsoft Docs"
description: "Az Apache Hive és a Azure HDInsight kapcsolatos gyakori kérdésekre adott válaszok."
keywords: "Az Azure HDInsight Hive, gyakori kérdések hibaelhárítási útmutatója, gyakori kérdések"
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: 
editor: 
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: dharmeshkakadia
ms.openlocfilehash: 398cc7cd0fa815f12ba5b503a1328688e1159eee
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-hive-by-using-azure-hdinsight"></a>Hive hibaelhárítása az Azure HDInsight használatával

A felső kérdések és azok megoldásait ismerje meg az Apache Ambari az Apache Hive Payload használatakor.


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Hogyan egy Hive metaadattárhoz exportálhatja és importálhatja azokat a fürt egy másik?


### <a name="resolution-steps"></a>Megoldási lépések

1. Csatlakozás egy Secure Shell (SSH) ügyfél segítségével a HDInsight-fürthöz. További információkért lásd: [További olvasnivaló](#additional-reading-end).

2. A következő parancsot a HDInsight-fürtre, amelyből el kívánja exportálni a metaadattárhoz:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

  A parancs létrehoz egy allatables.sql nevű fájlt.

3. A fájl alltables.sql másolja az új HDInsight-fürthöz, és futtassa a következő parancsot:

  ```apache
  hive -f alltables.sql
  ```

A kódot a megoldási lépések feltételezi, hogy az új fürtön adatelérési utak ugyanaz, mint az adatelérési utak, a régi fürtön. Ha az adatelérési utak nem egyezik, a létrehozott alltables.sql fájl módosult manuálisan módosíthatja.

### <a name="additional-reading"></a>További olvasnivaló

- [Csatlakozás egy HDInsight-fürthöz SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Hogyan keresik meg Hive naplók fürt?

### <a name="resolution-steps"></a>Megoldási lépések

1. Kapcsolódás a HDInsight-fürthöz az SSH használatával. További információkért lásd: **További olvasnivaló**.

2. Hive ügyfél naplók megtekintéséhez a következő paranccsal:

  ```apache
  /tmp/<username>/hive.log 
  ```

3. A következő paranccsal metaadattárhoz naplók Hive megtekintéséhez:

  ```apache
  /var/log/hive/hivemetastore.log 
  ```

4. Hiveserver naplók megtekintéséhez a következő paranccsal:

  ```apache
  /var/log/hive/hiveserver2.log 
  ```

### <a name="additional-reading"></a>További olvasnivaló

- [Csatlakozás egy HDInsight-fürthöz SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Hogyan indítsa el a fürt adott konfigurációjú a Hive rendszerhéjat?

### <a name="resolution-steps"></a>Megoldási lépések

1. Adjon meg egy konfigurációs kulcs-érték párt, a Hive rendszerhéjat indításakor. További információkért lásd: [További olvasnivaló](#additional-reading-end).

  ```apache
  hive -hiveconf a=b 
  ```

2. A Hive rendszerhéjat összes hatékony konfiguráció listájában, használja a következő parancsot:

  ```apache
  hive> set;
  ```

  Például az alábbi parancs segítségével indítsa el a Hive rendszerhéjat a hibakeresési naplózás engedélyezve van a konzolon:

  ```apache
  hive -hiveconf hive.root.logger=ALL,console 
  ```

### <a name="additional-reading"></a>További olvasnivaló

- [Hive konfiguráció tulajdonságai](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Hogyan elemezheti a fürt kritikus útvonalra Tez DAG adatokat?


### <a name="resolution-steps"></a>Megoldási lépések
 
1. Elemezze az Apache Tez irányított aciklikus diagramhoz (DAG) egy fürt kritikus grafikonon, csatlakozzon a HDInsight-fürthöz SSH használatával. További információkért lásd: [További olvasnivaló](#additional-reading-end).

2. A parancssorban futtassa a következő parancsot:
   
  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
  ```

3. Más lekérdezések, amelyek segítségével elemezheti a Tez DAG listájában, használja a következő parancsot:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
  ```

  Meg kell adnia egy példa program első argumentumként.

  A program érvényes nevek a következők:
    - **ContainerReuseAnalyzer**: tároló újbóli részleteit egy DAG nyomtatása
    - **CriticalPath**: kritikus elérési útja egy adatbázis-rendelkezésreállási csoportok keresése
    - **LocalityAnalyzer**: egy DAG helység részleteket nyomtatása
    - **ShuffleTimeAnalyzer**: elemzése a egy dag-csoport a véletlen idő – részletek
    - **SkewAnalyzer**: egy DAG eltérésére részleteiről elemzése
    - **SlowNodeAnalyzer**: csomópont részleteit egy DAG nyomtatása
    - **SlowTaskIdentifier**: egy dag-csoport a nyomtatás lassú feladat részletei
    - **SlowestVertexAnalyzer**: egy DAG leglassabb csúcspont részleteket nyomtatása
    - **SpillAnalyzer**: nyomtatás spill részleteit egy dag-csoport
    - **TaskConcurrencyAnalyzer**: egy DAG párhuzamossági részlete nyomtatása
    - **VertexLevelCriticalPathAnalyzer**: a kritikus út csúcspont szinten található egy dag-csoport


### <a name="additional-reading"></a>További olvasnivaló

- [Csatlakozás egy HDInsight-fürthöz SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Hogyan le Tez DAG adatok fürtből?


#### <a name="resolution-steps"></a>Megoldási lépések

A Tez DAG adatok gyűjtéséhez két módja van:

- A parancssorból:
 
    Kapcsolódás a HDInsight-fürthöz az SSH használatával. A parancssorban futtassa a következő parancsot:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Az Ambari Tez nézet használata:
   
  1. Ugrás az Ambari. 
  2. Ugrás a Tez nézetre (alatt a csempék ikonra, a jobb felső sarkában található). 
  3. Válassza ki a megtekinteni kívánt DAG.
  4. Válassza ki **adatletöltéshez**.

### <a name="additional-reading-end"></a>További olvasnivaló

[Csatlakozás egy HDInsight-fürthöz SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>Lásd még:
[Hibaelhárítás az Azure HDInsight segítségével](hdinsight-troubleshoot-guide.md)




