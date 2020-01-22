---
title: A Hadoop Services által a HDInsight-ben használt portok – Azure
description: Ez a cikk az Azure HDInsight futó Apache Hadoop-szolgáltatások által használt portok listáját tartalmazza
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 67cafbb7934381cd4c2936d6e6dfe7fb19d70735
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314691"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>A HDInsight Apache Hadoop Services által használt portok

Ez a dokumentum a HDInsight-fürtökön futó Apache Hadoop-szolgáltatások által használt portok listáját tartalmazza. Emellett információkat nyújt a fürt SSH-kapcsolaton keresztüli csatlakozásához használt portokról is.

## <a name="public-ports-vs-non-public-ports"></a>Nyilvános portok és nem nyilvános portok

A Linux-alapú HDInsight-fürtök csak három portot tesznek elérhetővé nyilvánosan az interneten. 22, 23 és 443. Ezek a portok a fürt biztonságos elérésére szolgálnak SSH-n keresztül és a biztonságos HTTPS protokollon keresztül elérhető szolgáltatásokkal.

Belsőleg a HDInsight-t több Azure-Virtual Machines (a fürtben lévő csomópontok) implementálja egy Azure-Virtual Network. A virtuális hálózaton belülről elérheti az interneten keresztül nem elérhető portokat. Ha például az egyik fő csomóponthoz csatlakozik az SSH használatával, akkor a fő csomópontból közvetlenül hozzáférhet a fürtcsomópontokon futó szolgáltatásokhoz.

> [!IMPORTANT]  
> Ha nem ad meg Azure-Virtual Network a HDInsight konfigurációs beállításaként, az egyik automatikusan létrejön. Ehhez a virtuális hálózathoz azonban nem csatlakozhat más gépekhez (például az Azure Virtual Machines vagy az ügyféloldali fejlesztői géphez).

Ha további gépeket szeretne csatlakoztatni a virtuális hálózathoz, először létre kell hoznia a virtuális hálózatot, majd meg kell adnia a HDInsight-fürt létrehozásakor. További információ: [virtuális hálózat megtervezése a HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Nyilvános portok

Egy HDInsight-fürt összes csomópontja egy Azure-Virtual Network található, és nem lehet közvetlenül hozzáférni az internetről. A nyilvános átjárók a következő portokhoz biztosítanak internet-hozzáférést, amelyek az összes HDInsight esetében közösek.

| Szolgáltatás | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- |
| sshd |22 |SSH |Összekapcsolja az ügyfeleket az sshd-vel az elsődleges átjárócsomóponthoz. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Összekapcsolja az ügyfeleket az sshd-vel a peremhálózati csomóponton. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Összekapcsolja az ügyfeleket az sshd-vel a másodlagos átjárócsomóponthoz. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Ambari webes felhasználói felület. Lásd: [a HDInsight kezelése az Apache Ambari webes felületén](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. Lásd: [a HDInsight kezelése az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Lásd: [a MapReduce használata a curl használatával](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Csatlakozás a Kaptárhoz ODBC használatával. Lásd: [az Excel és a HDInsight összekötése a Microsoft ODBC-illesztővel](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Csatlakozás a ApacheHive a JDBC használatával. Lásd: [kapcsolódás Apache Hive a HDInsight a kaptár JDBC-illesztőprogram használatával](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

A következő típusok érhetők el adott fürtökhöz:

| Szolgáltatás | Port | Protocol (Protokoll) | Fürttípus | Leírás |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBase REST API. Lásd: [az Apache HBase használatának első lépései](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark REST API. Lásd: [Apache Spark feladatok távoli elküldése az Apache Livy használatával](spark/apache-spark-livy-rest-interface.md) |
| Spark-takarékos kiszolgáló |443 |HTTPS |Spark |A Spark-takarékossági kiszolgáló a kaptár-lekérdezések elküldésére szolgál. Lásd: [a beeline Apache Hive használata a HDInsight-on](hadoop/apache-hadoop-use-hive-beeline.md) |
| A Storm |443 |HTTPS |A Storm |Storm webes felhasználói felület. Lásd: [Apache Storm-topológiák üzembe helyezése és kezelése a HDInsight-](storm/apache-storm-deploy-monitor-topology-linux.md) ben |
| Kafka Rest-proxy |443 |HTTPS |Kafka |Kafka-REST API. További információ: [Apache Kafka-fürtök használata az Azure HDINSIGHT Rest-proxy használatával](kafka/rest-proxy.md) |

### <a name="authentication"></a>Hitelesítés

Az interneten nyilvánosan elérhető összes szolgáltatást hitelesíteni kell:

| Port | Hitelesítő adatok |
| --- | --- |
| 22 vagy 23 |A fürt létrehozásakor megadott SSH-felhasználói hitelesítő adatok |
| 443 |A fürt létrehozása során beállított bejelentkezési név (alapértelmezett: rendszergazda) és jelszó |

## <a name="non-public-ports"></a>Nem nyilvános portok

> [!NOTE]  
> Egyes szolgáltatások csak bizonyos típusú fürtökön érhetők el. Például a HBase csak HBase-fürtökön érhető el.

> [!IMPORTANT]  
> Egyes szolgáltatások egyszerre csak egy átjárócsomóponthoz futnak. Ha az elsődleges átjárócsomóponthoz próbál csatlakozni a szolgáltatáshoz, és hibaüzenetet kap, próbálkozzon újra a másodlagos átjárócsomóponthoz használatával.

### <a name="ambari"></a>Ambari

| Szolgáltatás | Csomópontok | Port | URL-cím elérési útja | Protocol (Protokoll) |
| --- | --- | --- | --- | --- |
| Ambari webes felhasználói felület | Átjárócsomópontok | 8080 | / | HTTP |
| Ambari REST API | Átjárócsomópontok | 8080 | /api/v1 | HTTP |

Példák:

* Ambari REST API: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS-portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| NameNode webes felhasználói felület |Átjárócsomópontok |30070 |HTTPS |Az állapot megtekintésére szolgáló webes KEZELŐFELÜLET |
| NameNode metaadat-szolgáltatás |fő csomópontok |8020 |IPC |Fájlrendszer metaadatainak |
| DataNode |Minden munkavégző csomópont |30075 |HTTPS |Webes felhasználói felület az állapotok, naplók stb. megtekintéséhez |
| DataNode |Minden munkavégző csomópont |30010 |&nbsp; |Adatátvitel |
| DataNode |Minden munkavégző csomópont |30020 |IPC |Metaadat-műveletek |
| Másodlagos NameNode |Átjárócsomópontok |50090 |HTTP |Ellenőrzőpont a NameNode-metaadatokhoz |

### <a name="yarn-ports"></a>FONALas portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| Resource Manager webes felhasználói felület |Átjárócsomópontok |8088 |HTTP |A Resource Manager webes felhasználói felülete |
| Resource Manager webes felhasználói felület |Átjárócsomópontok |8090 |HTTPS |A Resource Manager webes felhasználói felülete |
| Resource Manager felügyeleti felület |fő csomópontok |8141 |IPC |Alkalmazás-beadványok esetén (struktúra, kaptár-kiszolgáló, Pig stb.) |
| Resource Manager-ütemező |fő csomópontok |8030 |HTTP |Felügyeleti felület |
| Resource Manager-alkalmazás felülete |fő csomópontok |8050 |HTTP |Az Applications Manager felületének címe |
| NodeManager |Minden munkavégző csomópont |30050 |&nbsp; |A Container Manager címe |
| NodeManager webes felhasználói felület |Minden munkavégző csomópont |30060 |HTTP |Resource Manager-felület |
| Idősor címe |Átjárócsomópontok |10200 |RPC |Az ütemterv szolgáltatás RPC szolgáltatása. |
| Ütemterv webes felhasználói felülete |Átjárócsomópontok |8188 |HTTP |Az idővonal-szolgáltatás webes felhasználói felülete |

### <a name="hive-ports"></a>Struktúra portjai

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| HiveServer2 |Átjárócsomópontok |10001 |Takarékosság |Szolgáltatás a Kaptárhoz való csatlakozáshoz (takarékosság/JDBC) |
| Hive-metaadattár |Átjárócsomópontok |9083 |Takarékosság |Szolgáltatás a kaptár-metaadatokhoz való csatlakozáshoz (takarékosság/JDBC) |

### <a name="webhcat-ports"></a>Webhcaten-portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| Webhcaten-kiszolgáló |Átjárócsomópontok |30111 |HTTP |Webes API a HCatalog és más Hadoop-szolgáltatásokhoz |

### <a name="mapreduce-ports"></a>MapReduce-portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| JobHistory |Átjárócsomópontok |19888 |HTTP |MapReduce JobHistory webes felhasználói felület |
| JobHistory |Átjárócsomópontok |10020 |&nbsp; |MapReduce JobHistory-kiszolgáló |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Közbenső leképezési kimenetek továbbítása a szűkítők igényléséhez |

### <a name="oozie"></a>Oozie

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| Oozie-kiszolgáló |Átjárócsomópontok |11000 |HTTP |Oozie-szolgáltatás URL-címe |
| Oozie-kiszolgáló |Átjárócsomópontok |11001 |HTTP |Oozie-rendszergazda portja |

### <a name="ambari-metrics"></a>Ambari-metrikák

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| Idősor (alkalmazás előzményei) |Átjárócsomópontok |6188 |HTTP |Az idővonal-szolgáltatás webes felhasználói felülete |
| Idősor (alkalmazás előzményei) |Átjárócsomópontok |30200 |RPC |Az idővonal-szolgáltatás webes felhasználói felülete |

### <a name="hbase-ports"></a>HBase-portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| HMaster |Átjárócsomópontok |16000 |&nbsp; |&nbsp; |
| HMaster-információ webes felhasználói felülete |Átjárócsomópontok |16010 |HTTP |A HBase Master webes felhasználói felületének portja |
| Régió-kiszolgáló |Minden munkavégző csomópont |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Az ügyfelek által a ZooKeeper való csatlakozáshoz használt port |

### <a name="kafka-ports"></a>Kafka-portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | Leírás |
| --- | --- | --- | --- | --- |
| Bróker |Munkavégző-csomópontok |9092 |[Kafka-huzal protokoll](https://kafka.apache.org/protocol.html) |Ügyfél-kommunikációhoz használatos |
| &nbsp; |Zookeeper-csomópontok |2181 |&nbsp; |Az ügyfelek által a Zookeeper való csatlakozáshoz használt port |
| REST-proxy | Kafka felügyeleti csomópontok |9400 |HTTPS |[Kafka REST-specifikáció](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Spark-portok

| Szolgáltatás | Csomópontok | Port | Protocol (Protokoll) | URL-cím elérési útja | Leírás |
| --- | --- | --- | --- | --- | --- |
| Spark-kiszolgálók |Átjárócsomópontok |10002 |Takarékosság | &nbsp; | Szolgáltatás a Spark SQL-hez való csatlakozáshoz (takarékosság/JDBC) |
| Livy-kiszolgáló | Átjárócsomópontok | 8998 | HTTP | &nbsp; | Az utasítások, feladatok és alkalmazások futtatására szolgáló szolgáltatás |
| Jupyter notebook | Átjárócsomópontok | 8001 | HTTP | &nbsp; | Jupyter notebook webhelye |

Példák:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. Ebben a példában a `10.0.0.11` a Livy szolgáltatást futtató átjárócsomóponthoz IP-címe.
