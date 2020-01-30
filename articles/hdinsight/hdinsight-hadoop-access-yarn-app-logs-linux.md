---
title: Hozzáférési Apache Hadoop a FONALas alkalmazások naplóihoz – Azure HDInsight
description: Megtudhatja, hogyan érheti el a fonal-alkalmazási naplókat egy Linux-alapú HDInsight-(Apache Hadoop-) fürtön a parancssori felület és a webböngésző használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76764381"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Hozzáférési Apache Hadoop a FONALas alkalmazások naplóihoz a Linux-alapú HDInsight

Megtudhatja, hogyan érheti el a naplókat [Apache HADOOP fonalak](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (még egy erőforrás-egyeztető) alkalmazásai számára egy [Apache Hadoop](https://hadoop.apache.org/) -fürtön az Azure HDInsight.

## <a name="what-is-apache-yarn"></a>Mi az Apache-fonal?

A FONALak több programozási modellt támogatnak ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) is) az erőforrás-kezelés leválasztásával az alkalmazás ütemezése/monitorozása során. A fonal globális *erőforráskezelő* (RM), munkavégző-csomópontos *csomópontkezelők* (alrendszerek) és felhasználónkénti *ApplicationMasters* (AMs) használ. Az per-Application AM egyezteti az erőforrásokat (processzor, memória, lemez, hálózat) az alkalmazásnak az RM-vel való futtatásához. Az RM úgy működik, hogy az ilyen erőforrásokat *tárolóként*adja meg. Az AM feladata az RM által hozzárendelt tárolók állapotának nyomon követése. Egy alkalmazás az alkalmazás természetétől függően számos tárolót igényelhet.

Minden alkalmazás több *alkalmazási kísérletből*állhat. Ha egy alkalmazás meghibásodik, előfordulhat, hogy a rendszer újrapróbálkozik új kísérletként. Minden kísérlet egy tárolóban fut. Bizonyos értelemben a tárolók a FONALas alkalmazások alapszintű munkaegységének környezetét biztosítják. A tároló környezetében elvégzett összes munkát az egyetlen feldolgozó csomóponton hajtja végre, amelyen a tároló le lett foglalva. További információért lásd a [Hadoop: fonal-alkalmazások írása](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)vagy a [Apache Hadoop fonalak](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) .

Ha a fürtöt a nagyobb feldolgozási sebesség támogatásához kívánja méretezni, a [fürtöket manuálisan, néhány különböző nyelven](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters) [is használhatja.](hdinsight-autoscale-clusters.md)

## <a name="yarn-timeline-server"></a>FONAL idővonal-kiszolgálója

A [Apache HADOOP fonál idővonal-kiszolgálója](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) általános információkat biztosít a befejezett alkalmazásokról

A fonal idővonal-kiszolgálója a következő típusú adattípusokat tartalmazza:

* Az alkalmazás azonosítója, az alkalmazás egyedi azonosítója
* Az alkalmazást elindító felhasználó
* Információk az alkalmazás befejezésére tett kísérletekről
* Az adott alkalmazás által megkísérelt tárolók

## <a name="yarn-applications-and-logs"></a>FONALas alkalmazások és naplók

A FONALak több programozási modellt támogatnak ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) is) az erőforrás-kezelés leválasztásával az alkalmazás ütemezése/monitorozása során. A fonal globális *erőforráskezelő* (RM), munkavégző-csomópontos *csomópontkezelők* (alrendszerek) és felhasználónkénti *ApplicationMasters* (AMs) használ. Az per-Application AM egyezteti az erőforrásokat (processzor, memória, lemez, hálózat) az alkalmazásnak az RM-vel való futtatásához. Az RM úgy működik, hogy az ilyen erőforrásokat *tárolóként*adja meg. Az AM feladata az RM által hozzárendelt tárolók állapotának nyomon követése. Egy alkalmazás az alkalmazás természetétől függően számos tárolót igényelhet.

Minden alkalmazás több *alkalmazási kísérletből*állhat. Ha egy alkalmazás meghibásodik, előfordulhat, hogy a rendszer újrapróbálkozik új kísérletként. Minden kísérlet egy tárolóban fut. Bizonyos értelemben a tárolók a FONALas alkalmazások alapszintű munkaegységének környezetét biztosítják. A tároló környezetében elvégzett összes munkát az egyetlen feldolgozó csomóponton hajtja végre, amelyen a tároló le lett foglalva. További információért lásd: [Apache Hadoop a fonalat ismertető fogalmakat](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) .

Az alkalmazás naplói (és a kapcsolódó tárolók naplói) kritikus fontosságúak a problematikus Hadoop-alkalmazások hibakereséséhez. A fonal szép keretrendszert biztosít az alkalmazások naplóinak a [naplózási összesítési](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) funkcióval való összegyűjtéséhez, összesítéséhez és tárolásához. A naplózási összesítés funkció lehetővé teszi, hogy az alkalmazások naplói több determinisztikus. Összesíti a munkavégző csomópont összes tárolójában lévő naplókat, és a munkavégző csomóponton egy összesített naplófájlként tárolja őket. Az alkalmazás befejeződése után a rendszer az alapértelmezett fájlrendszerben tárolja a naplót. Az alkalmazás több száz vagy akár több ezer tárolót is használhat, de egyetlen feldolgozó csomóponton futtatott összes tároló naplóit mindig egyetlen fájlba összesíti a rendszer. Így az alkalmazás csak 1 log/feldolgozó csomópontot használ. A naplózási összesítés alapértelmezés szerint engedélyezve van a 3,0-es és újabb verziójú HDInsight-fürtökön. Az összesített naplók a fürt alapértelmezett tárolójában találhatók. A következő elérési út a naplók HDFS elérési útja:

```
/app-logs/<user>/logs/<applicationId>
```

Az elérési útban `user` az alkalmazást elindító felhasználó neve. A `applicationId` a fonal RM-alkalmazáshoz rendelt egyedi azonosító.

Az összesített naplók nem olvashatók közvetlenül, ahogy egy [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), a tároló által indexelt [bináris formátumban](https://issues.apache.org/jira/browse/HADOOP-3315) vannak írva. A erőforráskezelő-naplók vagy a CLI-eszközök segítségével egyszerű szövegként tekintheti meg ezeket a naplókat alkalmazások vagy tárolók esetében.

## <a name="yarn-logs-in-an-esp-cluster"></a>Egy ESP-fürtön lévő fonal-naplók

Két konfigurációt kell hozzáadni a Ambari egyéni `mapred-site`ához.

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net`, ahol a `CLUSTERNAME` a fürt neve.

1. A Ambari felhasználói felületén navigáljon a **MapReduce2** > **configs** > **speciális** > **Egyéni mapred-helyhez**.

1. Adja hozzá a következő tulajdonságok *egyikét* :

    **1. beállítás**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **2. beállítás**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Mentse a módosításokat, és indítsa újra az összes érintett szolgáltatást.

## <a name="yarn-cli-tools"></a>FONAL CLI-eszközök

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](./hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Sorolja fel a jelenleg futó fonal-alkalmazások összes alkalmazás-azonosítóját a következő paranccsal:

    ```bash
    yarn top
    ```

    Jegyezze fel az alkalmazás azonosítóját abban a `APPLICATIONID` oszlopban, amelynek naplóit le szeretné tölteni.

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Ezeket a naplókat egyszerű szövegként tekintheti meg a következő parancsok egyikének futtatásával:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    A parancsok futtatásakor határozza meg a &lt;applicationId >, &lt;a felhasználó által elindított >, &lt;containerId > és &lt;feldolgozó-csomópont-címek > információit.

### <a name="other-sample-commands"></a>Egyéb minta parancsok

1. Töltse le a fonalas tárolók naplóit az összes alkalmazás-főkiszolgálóhoz az alábbi paranccsal. Ekkor létrejön a `amlogs.txt` nevű naplófájl szöveges formátumban.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Töltse le a fonal-tároló naplóit csak a legújabb alkalmazás-főkiszolgáló számára a következő paranccsal:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Töltse le a fonal-tároló naplóit az első két alkalmazás főkiszolgálói számára a következő paranccsal:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Töltse le az összes fonalas tároló naplóit a következő paranccsal:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Töltsön le egy adott tárolóhoz tartozó fonal-tároló naplót a következő paranccsal:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>FONAL erőforráskezelő felhasználói felülete

A fonal erőforráskezelő felhasználói felülete a fürt átjárócsomóponthoz fut. Ez a Ambari webes felhasználói felületén keresztül érhető el. A következő lépések végrehajtásával tekintheti meg a FONALak naplóit:

1. A böngészőben nyissa meg a `https://CLUSTERNAME.azurehdinsight.net`. Cserélje le a CLUSTERNAME-t a HDInsight-fürt nevére.

2. A bal oldali szolgáltatások listájából válassza a **fonal**lehetőséget.

    ![Apache Ambari fonal-szolgáltatás kiválasztva](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. A **rövid hivatkozások** legördülő listából válassza ki az egyik fürtcsomópont-csomópontot, majd válassza a **erőforráskezelő-napló**elemet.

    ![Apache Ambari-fonal – gyors hivatkozások](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Megjelenik a FONALak naplóira mutató hivatkozások listája.
