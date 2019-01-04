---
title: HDInsight – Azure Hadoop-szolgáltatások által használt portok
description: HDInsight futó Hadoop-szolgáltatások által használt portok listája.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: be264be41b198e95dae64730ef31f431ec06a2e7
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715460"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>HDInsight az Apache Hadoop-szolgáltatások által használt portok

Ez a dokumentum a Linux-alapú HDInsight-fürtökön futó Apache Hadoop-szolgáltatások által használt portok listáját tartalmazza. A fürthöz SSH használatával való kapcsolódáshoz használt portokon információkat is biztosít.

## <a name="public-ports-vs-non-public-ports"></a>Nyilvános port és a nem nyilvános port

Linux-alapú HDInsight-fürtök csak nyilvánosan az interneten; három portokat tesz elérhetővé 22-es, 23, és a 443-as porton. Ezeket a portokot hozzá biztonságosan a fürthöz az SSH és a biztonságos HTTPS protokollon keresztül elérhetővé tett szolgáltatásokat használja.

Belső használatra a HDInsight van megvalósítva több Azure Virtual Machines szolgáltatás által (a fürtben lévő csomópontok) rendszert futtató Azure virtuális hálózatban. Az a virtuális hálózaton belül hozzáférhet portok nem teszi közzé az interneten keresztül. Például csatlakozni a átjárócsomópontjaihoz SSH egyikét, ha a fő csomópontot, majd közvetlenül hozzáférhet a fürtcsomópontokon futó szolgáltatásokat.

> [!IMPORTANT]  
> Ha nem ad meg az Azure Virtual Network konfigurációs beállítás, a HDInsight, az egyik automatikusan létrejön. Más gépeken (például más Azure virtuális gépek vagy az ügyfél fejlesztői gépen) azonban nem tud csatlakozni a virtuális hálózaton.


További gépeket csatlakoztatni a virtuális hálózat, először hozza létre a virtuális hálózathoz, és adja meg azt a HDInsight-fürt létrehozása során. További információkért lásd: [HDInsight kiterjesztése képességek az Azure Virtual Network használatával](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Nyilvános port

Egy HDInsight-fürt összes csomópontja egy Azure virtuális hálózatban találhatók, és nem tudnak közvetlenül hozzáférni az internetről. Egy nyilvános átjáró a következő portokat, amelyek közösek az összes HDInsight-fürt típusa internet-hozzáférést biztosít.

| Szolgáltatás | Port | Protokoll | Leírás |
| --- | --- | --- | --- |
| sshd |22 |SSH |Az ügyfelek az elsődleges átjárócsomóponton sshd kapcsolódik. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Az ügyfelek csatlakozik, az élcsomóponton sshd. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Ügyfelek sshd meg a másodlagos átjárócsomóponthoz csatlakozik. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Az Ambari webes felhasználói felületen. Lásd: [kezelése HDInsight az Apache Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Az Ambari REST API-t. Lásd: [kezelése HDInsight az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md) |
| Webhcat használatával |443 |HTTPS |HCatalog REST API-t. Lásd: [Apache Hive használata a curl használatával](hadoop/apache-hadoop-use-pig-curl.md), [Apache Pig használata a Curl](hadoop/apache-hadoop-use-pig-curl.md), [MapReduce használata a curl használatával](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Hive ODBC segítségével csatlakozik. Lásd: [Excel csatlakoztatása a HDInsight a Microsoft ODBC-illesztőprogram](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |JDBC segítségével ApacheHive csatlakozik. Lásd: [csatlakozás az Apache Hive a HDInsight, a Hive JDBC-illesztővel](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Az alábbi funkciók érhetők el az adott fürttípusokat:

| Szolgáltatás | Port | Protokoll | Fürttípus | Leírás |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBase REST API-t. Lásd: [Apache HBase használatának első lépései](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark REST API-t. Lásd: [távolról az Apache Livy használatával nyújt az Apache Spark-feladatok](spark/apache-spark-livy-rest-interface.md) |
| A Spark Thrift-kiszolgáló |443 |HTTPS |Spark |Hive-lekérdezések elküldéséhez használt Spark Thrift-kiszolgáló. Lásd: [a Beeline használata a HDInsight Apache Hive-val](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |A Storm webes felhasználói felületen. Lásd: [üzembe helyezése és kezelése a HDInsight Apache Storm-topológiák](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Hitelesítés

Az interneten nyilvánosan elérhető az összes szolgáltatás hitelesíteni kell:

| Port | Hitelesítő adatok |
| --- | --- |
| 22-es vagy 23 |A fürt létrehozásakor megadott SSH felhasználói hitelesítő adatok |
| 443 |A bejelentkezési nevet (alapértelmezett: rendszergazdai) és a fürt létrehozásakor beállított jelszó |

## <a name="non-public-ports"></a>Nem nyilvános port

> [!NOTE]  
> Néhány szolgáltatás csak az adott fürttípusokat a érhetők el. Például HBase csak akkor használható, a HBase-fürt típusok.

> [!IMPORTANT]  
> Néhány szolgáltatás csak az egyik átjárócsomópontjával egyszerre futni. Megkísérli a szolgáltatást az elsődleges átjárócsomóponthoz csatlakozik, és hibaüzenetet kap, ha újra a másodlagos átjárócsomóponthoz használatával.

### <a name="ambari"></a>Ambari

| Szolgáltatás | Csomópontok | Port | URL-cím | Protokoll | 
| --- | --- | --- | --- | --- |
| Az Ambari webes felhasználói felületen | Átjárócsomópontok | 8080 | / | HTTP |
| Az Ambari REST API | Átjárócsomópontok | 8080 | / api/v1 | HTTP |

Példák:

* Az Ambari REST API-val: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Leírás |
| --- | --- | --- | --- | --- |
| NameNode webes felhasználói felületen |Átjárócsomópontok |30070 |HTTPS |Webes felhasználói felületen állapotának megtekintése |
| NameNode metadata szolgáltatás |Fő csomópontok |8020 |IPC |A fájl-metaadatok |
| DataNode |Összes munkavégző csomóponton |30075 |HTTPS |Webes felhasználói felületen állapot megtekintése, naplók, stb. |
| DataNode |Összes munkavégző csomóponton |30010 |&nbsp; |Adatátvitel |
| DataNode |Összes munkavégző csomóponton |30020 |IPC |Metaadat-műveletek |
| Másodlagos NameNode |Átjárócsomópontok |50090 |HTTP |Ellenőrzőpont NameNode-metaadatok |

### <a name="yarn-ports"></a>YARN-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Leírás |
| --- | --- | --- | --- | --- |
| Resource Manager webes felhasználói felületen |Átjárócsomópontok |8088 |HTTP |Webes felhasználói felületen a Resource Managerhez |
| Resource Manager webes felhasználói felületen |Átjárócsomópontok |8090 |HTTPS |Webes felhasználói felületen a Resource Managerhez |
| Erőforrás-kezelő felügyeleti felülete |Fő csomópontok |8141 |IPC |Az alkalmazás a jelentkezés (Hive, Pig, Hive server stb.) |
| Erőforrás-kezelő scheduler |Fő csomópontok |8030 |HTTP |Rendszergazdai felület |
| Erőforrás-kezelő alkalmazás felületen |Fő csomópontok |8050 |HTTP |Az alkalmazások manager adapter címére |
| NodeManager |Összes munkavégző csomóponton |30050 |&nbsp; |A tároló-kezelő címe |
| NodeManager webes felhasználói felületen |Összes munkavégző csomóponton |30060 |HTTP |Resource manager felületéről |
| Idősor-cím |Átjárócsomópontok |10200 |RPC |Az ütemterv RPC szolgáltatás. |
| Idősor webes felhasználói felületen |Átjárócsomópontok |8181 |HTTP |Az ütemterv szolgáltatás webes felhasználói felületen |

### <a name="hive-ports"></a>Hive-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Leírás |
| --- | --- | --- | --- | --- |
| HiveServer2 |Átjárócsomópontok |10001 |Thrift |Kapcsolódás a Hive-(Thrift/JDBC) szolgáltatás |
| Hive-Metaadattár |Átjárócsomópontok |9083 |Thrift |Kapcsolódás a Hive-metaadatokat (Thrift/JDBC) szolgáltatás |

### <a name="webhcat-ports"></a>WebHCat-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Leírás |
| --- | --- | --- | --- | --- |
| WebHCat-kiszolgáló |Átjárócsomópontok |30111 |HTTP |Webes API-k fölött HCatalog és más Hadoop-szolgáltatásokhoz |

### <a name="mapreduce-ports"></a>A MapReduce-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Leírás |
| --- | --- | --- | --- | --- |
| JobHistory |Átjárócsomópontok |19888 |HTTP |A MapReduce JobHistory webes felhasználói felületen |
| JobHistory |Átjárócsomópontok |10020 |&nbsp; |A MapReduce JobHistory kiszolgáló |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Adatátvitel köztes térkép azt kérő csökkentő kimenete |

### <a name="oozie"></a>Oozie

| Szolgáltatás | Csomópontok | Port | Protokoll | Leírás |
| --- | --- | --- | --- | --- |
| Az Oozie-kiszolgáló |Átjárócsomópontok |11000 |HTTP |Az Oozie-szolgáltatás URL-címe |
| Az Oozie-kiszolgáló |Átjárócsomópontok |11001 |HTTP |Az Oozie-felügyeleti port |

### <a name="ambari-metrics"></a>Ambari-metrikák

| Szolgáltatás | Csomópontok | Port | Protokoll | Leírás |
| --- | --- | --- | --- | --- |
| Idővonal (alkalmazás Előzmények) |Átjárócsomópontok |6188 |HTTP |Az ütemterv szolgáltatás webes felhasználói felületen |
| Idővonal (alkalmazás Előzmények) |Átjárócsomópontok |30200 |RPC |Az ütemterv szolgáltatás webes felhasználói felületen |

### <a name="hbase-ports"></a>HBase-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Leírás |
| --- | --- | --- | --- | --- |
| HMaster |Átjárócsomópontok |16000 |&nbsp; |&nbsp; |
| HMaster info webes felhasználói felület |Átjárócsomópontok |16010 |HTTP |A port, a HBase főkiszolgáló webes felhasználói felület |
| Régióbeli kiszolgálók |Összes munkavégző csomóponton |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |A port, amelyet az ügyfelek használatával kapcsolódhat a ZooKeeper |

### <a name="kafka-ports"></a>A Kafka-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | Leírás |
| --- | --- | --- | --- | --- |
| Munkamenet-átvitelszervező |Munkavégző csomópontok |9092 |[A Kafka átviteli protokoll](https://kafka.apache.org/protocol.html) |Az ügyfél-kommunikációhoz használt |
| &nbsp; |Zookeeper-csomópontok |2181 |&nbsp; |A port, amelyet az ügyfelek használatával kapcsolódhat a Zookeeper |

### <a name="spark-ports"></a>Spark-portok

| Szolgáltatás | Csomópontok | Port | Protokoll | URL-cím | Leírás |
| --- | --- | --- | --- | --- | --- |
| A Spark Thrift-kiszolgáló |Átjárócsomópontok |10002 |Thrift | &nbsp; | Spark SQL (Thrift/JDBC) való kapcsolódáshoz szolgáltatás |
| Livy-kiszolgáló | Átjárócsomópontok | 8998 | HTTP | &nbsp; | Szolgáltatás utasítások, feladatok és alkalmazások futtatásához |
| Jupyter Notebook | Átjárócsomópontok | 8001 | HTTP | &nbsp; | Jupyter notebook webhely |

Példák:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. Ebben a példában `10.0.0.11` az átjárócsomóponthoz, a Livy szolgáltatást üzemeltető IP-címe.
