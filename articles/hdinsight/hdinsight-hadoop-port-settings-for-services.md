---
title: A Hadoop-szolgáltatások által használt portok a HDInsighton – Azure
description: Ez a cikk az Azure HDInsightban futó Apache Hadoop-szolgáltatások által használt portok listáját tartalmazza
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 67cafbb7934381cd4c2936d6e6dfe7fb19d70735
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314691"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Az Apache Hadoop-szolgáltatások által használt portok a HDInsight-on

Ez a dokumentum az Apache Hadoop-szolgáltatások hdinsight-fürtökön futó portjainak listáját tartalmazza. Az SSH használatával a fürthöz való csatlakozáshoz használt portokról is tartalmaz információkat.

## <a name="public-ports-vs-non-public-ports"></a>Nyilvános és nem nyilvános portok

A Linux-alapú HDInsight-fürtök csak három portot fednek fel nyilvánosan az interneten; 22, 23 és 443. Ezek a portok a fürt biztonságos elérésére szolgálnak az SSH és a biztonságos HTTPS protokollon keresztül elérhető szolgáltatások használatával.

Belsőleg HDInsight valósítja meg több Azure virtuális gépek (a csomópont a fürtön) fut egy Azure virtuális hálózaton. A virtuális hálózaton belül az interneten keresztül nem elérhető portokhoz férhet hozzá. Ha például az SSH használatával csatlakozik az egyik főcsomóponthoz, a fő csomópontról közvetlenül elérheti a fürtcsomópontokon futó szolgáltatásokat.

> [!IMPORTANT]  
> Ha nem ad meg egy Azure virtuális hálózatot a HDInsight konfigurációs beállításaként, automatikusan létrejön egy. Azonban nem csatlakozhat más gépekhez (például más Azure virtuális gépekhez vagy az ügyfélfejlesztő géphez) ehhez a virtuális hálózathoz.

További gépek csatlakoztatásához a virtuális hálózathoz először létre kell hoznia a virtuális hálózatot, majd meg kell adnia azt a HDInsight-fürt létrehozásakor. További információ: [A HDInsight virtuális hálózatának megtervezése.](hdinsight-plan-virtual-network-deployment.md)

## <a name="public-ports"></a>Nyilvános portok

Egy HDInsight-fürt összes csomópontja egy Azure virtuális hálózatban található, és nem érhető el közvetlenül az internetről. A nyilvános átjáró internet-hozzáférést biztosít a következő portokhoz, amelyek az összes HDInsight-fürttípuson gyakoriak.

| Szolgáltatás | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- |
| Sshd |22 |SSH |Az elsődleges headnode-on az sshd-hez csatlakoztatja az ügyfeleket. További információ: [SSH használata a HDInsight segítségével.](hdinsight-hadoop-linux-use-ssh-unix.md) |
| Sshd |22 |SSH |Az ügyfeleket a peremhálózati csomópontsshd-hoz kapcsolja össze. További információ: [SSH használata a HDInsight segítségével.](hdinsight-hadoop-linux-use-ssh-unix.md) |
| Sshd |23 |SSH |Az ügyfeleket a másodlagos csomópontsul lévő sshd-hez csatlakoztatja. További információ: [SSH használata a HDInsight segítségével.](hdinsight-hadoop-linux-use-ssh-unix.md) |
| Ambari |443 |HTTPS |Ambari webes felhasználói felület. Lásd: [HDInsight kezelése az Apache Ambari Web felhasználói felületén](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. Lásd: [HDInsight kezelése az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Lásd: [MapReduce használata curl-el](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |A Hive-hoz az ODBC használatával csatlakozik. Lásd: [Az Excel csatlakoztatása a HDInsighthoz a Microsoft ODBC illesztőprogrammal.](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) |
| HiveServer2 |443 |JDBC |Az ApacheHive-hoz csatlakozik a JDBC használatával. Lásd: [Csatlakozás az Apache Hive-hoz hdinsight-on a Hive JDBC illesztőprogram használatával](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Az alábbi fürttípusokhoz érhetők el:

| Szolgáltatás | Port | Protocol (Protokoll) | Fürt típusa | Leírás |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBase REST API. Lásd: [Első lépések az Apache HBase használatával](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark REST API. Lásd: [Apache Spark-feladatok küldése távolról az Apache Livy használatával](spark/apache-spark-livy-rest-interface.md) |
| Spark Takarékosság szerver |443 |HTTPS |Spark |Spark Takarékosság kiszolgáló hive-lekérdezések küldésére használt. Lásd: [Beeline használata az Apache Hive-val a HDInsight-on](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Storm web felhasználói felület. Lásd: [Apache Storm-topológiák telepítése és kezelése a HDInsightban](storm/apache-storm-deploy-monitor-topology-linux.md) |
| Kafka Rest proxy |443 |HTTPS |Kafka |Kafka REST API. Lásd: [Apache Kafka-fürtök használata az Azure HDInsightban REST-proxy használatával](kafka/rest-proxy.md) |

### <a name="authentication"></a>Hitelesítés

Az interneten nyilvánosan elérhető valamennyi szolgáltatást hitelesíteni kell:

| Port | Hitelesítő adatok |
| --- | --- |
| 22 vagy 23 |A fürt létrehozása során megadott SSH-felhasználói hitelesítő adatok |
| 443 |A fürt létrehozása során beállított bejelentkezési név (alapértelmezett: rendszergazda) és jelszó |

## <a name="non-public-ports"></a>Nem nyilvános portok

> [!NOTE]  
> Egyes szolgáltatások csak bizonyos fürttípusokon érhetők el. A HBase például csak HBase fürttípusokon érhető el.

> [!IMPORTANT]  
> Egyes szolgáltatások egyszerre csak egy csomóponton futnak. Ha megpróbál csatlakozni a szolgáltatáshoz az elsődleges csomóponton, és hibaüzenetet kap, próbálkozzon újra a másodlagos csomópont használatával.

### <a name="ambari"></a>Ambari

| Szolgáltatás | Csomópontok | Port | URL elérési útja | Protocol (Protokoll) |
| --- | --- | --- | --- | --- |
| Ambari webes felhasználói felület | Fő csomópontok | 8080 | / | HTTP |
| Ambari REST API | Fő csomópontok | 8080 | /api/v1 | HTTP |

Példák:

* Ambari REST API:`curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS-portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| NameNode webes felhasználói felület |Fő csomópontok |30070 |HTTPS |Webes felhasználói felület az állapot megtekintéséhez |
| NameNode metaadat-szolgáltatás |főcsomópontok |8020 |Ipc |Fájlrendszer metaadatai |
| DataNode között |Minden munkavégző csomópont |30075 |HTTPS |Webes felhasználói felület az állapot, a naplók stb. |
| DataNode között |Minden munkavégző csomópont |30010 |&nbsp; |Adatátvitel |
| DataNode között |Minden munkavégző csomópont |30020 |Ipc |Metaadat-műveletek |
| Másodlagos névcsomópont |Fő csomópontok |50090 |HTTP |A NameNode metaadatainak ellenőrzőpontja |

### <a name="yarn-ports"></a>YARN portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| Erőforrás-kezelő webes felhasználói felülete |Fő csomópontok |8088 |HTTP |Az Erőforrás-kezelő webes felhasználói felülete |
| Erőforrás-kezelő webes felhasználói felülete |Fő csomópontok |8090 |HTTPS |Az Erőforrás-kezelő webes felhasználói felülete |
| Erőforrás-kezelő rendszergazdai felülete |főcsomópontok |8141 |Ipc |Az alkalmazások beküldése (Hive, Hive szerver, Pig, stb) |
| Erőforrás-kezelő ütemezője |főcsomópontok |8030 |HTTP |Felügyeleti felület |
| Erőforrás-kezelő alkalmazásfelülete |főcsomópontok |8050 |HTTP |Az alkalmazáskezelő felületének címe |
| NodeManager |Minden munkavégző csomópont |30050 |&nbsp; |A tárolókezelő címe |
| NodeManager webes felhasználói felület |Minden munkavégző csomópont |30060 |HTTP |Erőforrás-kezelő felülete |
| Idővonal címe |Fő csomópontok |10200 |RPC |Az Idővonal szolgáltatás RPC szolgáltatása. |
| Idővonal webes felhasználói felülete |Fő csomópontok |8188 |HTTP |Az idővonal szolgáltatás webes felhasználói felülete |

### <a name="hive-ports"></a>Hive-portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| HiveServer2 |Fő csomópontok |10001 |Takarékosság |A Hive-hoz való csatlakozás szolgáltatása (Thrift/JDBC) |
| Hive-metaadattár |Fő csomópontok |9083 |Takarékosság |Hive metaadatokhoz való csatlakozás szolgáltatása (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| WebHCat kiszolgáló |Fő csomópontok |30111 |HTTP |Webes API a HCatalog és más Hadoop-szolgáltatások mellett |

### <a name="mapreduce-ports"></a>MapReduce portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| JobHistory (Feladattörténete) |Fő csomópontok |19888 |HTTP |MapReduce JobHistory webes felhasználói felület |
| JobHistory (Feladattörténete) |Fő csomópontok |10020 |&nbsp; |MapReduce Feladatelőzmények kiszolgáló |
| ShuffleHandler (Véletlenkezelő) |&nbsp; |13562 |&nbsp; |Köztes térképkimenetek átvitele a kérelmező szűkítőknek |

### <a name="oozie"></a>Oozie

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| Oozie kiszolgáló |Fő csomópontok |11000 |HTTP |Az Oozie szolgáltatás URL-címe |
| Oozie kiszolgáló |Fő csomópontok |11001 |HTTP |Port Oozie admin |

### <a name="ambari-metrics"></a>Ambari-metrikák

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| TimeLine (alkalmazáselőzmények) |Fő csomópontok |6188 |HTTP |A TimeLine szolgáltatás webes felhasználói felülete |
| TimeLine (alkalmazáselőzmények) |Fő csomópontok |30200 |RPC |A TimeLine szolgáltatás webes felhasználói felülete |

### <a name="hbase-ports"></a>HBase portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| HMaster (HMester) |Fő csomópontok |16000 |&nbsp; |&nbsp; |
| HMaster info web felhasználói felület |Fő csomópontok |16010 |HTTP |A HBase főkiszolgáló webfelhasználói felületének portja |
| Régiókiszolgáló |Minden munkavégző csomópont |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Az a port, amelyet az ügyfelek a ZooKeeper-hez való csatlakozáshoz használnak |

### <a name="kafka-ports"></a>Kafka portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| Bróker |Dolgozó csomópontok |9092 |[Kafka drót protokoll](https://kafka.apache.org/protocol.html) |Ügyfélkommunikációhoz használatos |
| &nbsp; |Zookeeper csomópontok |2181 |&nbsp; |Az a port, amelyet az ügyfelek a Zookeeper-hez való csatlakozáshoz használnak |
| REST-proxy | Kafka felügyeleti csomópontok |9400 |HTTPS |[Kafka REST specifikáció](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Spark-portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | URL elérési útja | Leírás |
| --- | --- | --- | --- | --- | --- |
| Spark Takarékosság szerverek |Fő csomópontok |10002 |Takarékosság | &nbsp; | A Spark SQL-hez való csatlakozás szolgáltatása (Thrift/JDBC) |
| Livy szerver | Fő csomópontok | 8998 | HTTP | &nbsp; | Szolgáltatás a futó utasításokhoz, feladatokhoz és alkalmazásokhoz |
| Jupyter notebook | Fő csomópontok | 8001 | HTTP | &nbsp; | Jupyter notebook honlapja |

Példák:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. Ebben a `10.0.0.11` példában a Livy szolgáltatást tartalmazó csomópont IP-címe.
