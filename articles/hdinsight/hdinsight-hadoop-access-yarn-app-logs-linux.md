---
title: Az Apache Hadoop YARN alkalmazásnaplók elérése – Azure HDInsight
description: Ismerje meg, hogyan érheti el a YARN alkalmazásnaplókat egy Linux-alapú HDInsight (Apache Hadoop) fürtön a parancssori és a webböngésző használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764381"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Az Apache Hadoop YARN alkalmazásnaplók elérése Linux-alapú HDInsight-alapú

Ismerje meg, hogyan érheti el az [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Még egy másik erőforrás-tárgyaló) alkalmazások naplóit az Azure HDInsight [Apache Hadoop-fürtjein.](https://hadoop.apache.org/)

## <a name="what-is-apache-yarn"></a>Mi az Apache YARN?

A YARN több programozási modellt is támogat[(az Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) az egyik) az erőforrás-kezelés alkalmazásütemezéstől/figyeléstől való leválasztásával. A YARN globális *ResourceManager* (RM), munkavégző csomópontonkénti csomópontnodeManagers (NM- ek) és alkalmazásonkénti ApplicationMasters (AM) (AM) (AM) (AM) (egy dolgozó-csomópont *nodeManagers)* és alkalmazásonkénti *ApplicationMasters* (AMs) használ. Az alkalmazásonkénti AM egyezteti az erőforrás-működtető vel való alkalmazás futtatásához szükséges erőforrásokat (PROCESSZOR, memória, lemez, hálózat). Az RM együttműködik az új gyártókkal, hogy megadja ezeket az erőforrásokat, amelyek *tárolóként*vannak megadva. Az am felelős az rm által hozzárendelt tárolók előrehaladásának nyomon követéséért. Egy alkalmazás az alkalmazás jellegétől függően sok tárolót igényelhet.

Minden alkalmazás több *alkalmazáskísérletből*állhat. Ha egy alkalmazás meghibásodik, a rendszer újra próbálkozhat új kísérletként. Minden kísérlet egy tárolóban fut. Bizonyos értelemben a tároló biztosítja a YARN-alkalmazás által végzett munka alapvető egységének környezetét. Minden olyan munka, amely egy tároló környezetében történik, azon az egyfeldolgozó-csomóponton történik, amelyen a tárolót lefoglalták. További hivatkozás: [Hadoop: Yarn applications írása](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)vagy [Apache Hadoop YARN.](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)

A fürt nagyobb feldolgozási átviteli arányának méretezéséhez használhatja [az automatikus skálázást,](hdinsight-autoscale-clusters.md) vagy [manuálisan skálázhatja a fürtöket néhány különböző nyelven.](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters)

## <a name="yarn-timeline-server"></a>YARN idővonal-kiszolgáló

Az [Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) általános információkat nyújt a befejezett alkalmazásokról

A YARN Timeline Server a következő típusú adatokat tartalmazza:

* Az alkalmazásazonosító, egy alkalmazás egyedi azonosítója
* Az alkalmazást elkérő felhasználó
* A kérelem kitöltésére tett kísérletekre vonatkozó információk
* Az adott alkalmazási kísérlet által használt tárolók

## <a name="yarn-applications-and-logs"></a>YARN alkalmazások és naplók

A YARN több programozási modellt is támogat[(az Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) az egyik) az erőforrás-kezelés alkalmazásütemezéstől/figyeléstől való leválasztásával. A YARN globális *ResourceManager* (RM), munkavégző csomópontonkénti csomópontnodeManagers (NM- ek) és alkalmazásonkénti ApplicationMasters (AM) (AM) (AM) (AM) (egy dolgozó-csomópont *nodeManagers)* és alkalmazásonkénti *ApplicationMasters* (AMs) használ. Az alkalmazásonkénti AM egyezteti az erőforrás-működtető vel való alkalmazás futtatásához szükséges erőforrásokat (PROCESSZOR, memória, lemez, hálózat). Az RM együttműködik az új gyártókkal, hogy megadja ezeket az erőforrásokat, amelyek *tárolóként*vannak megadva. Az am felelős az rm által hozzárendelt tárolók előrehaladásának nyomon követéséért. Egy alkalmazás az alkalmazás jellegétől függően sok tárolót igényelhet.

Minden alkalmazás több *alkalmazáskísérletből*állhat. Ha egy alkalmazás meghibásodik, a rendszer újra próbálkozhat új kísérletként. Minden kísérlet egy tárolóban fut. Bizonyos értelemben a tároló biztosítja a YARN-alkalmazás által végzett munka alapvető egységének környezetét. Minden olyan munka, amely egy tároló környezetében történik, azon az egyfeldolgozó-csomóponton történik, amelyen a tárolót lefoglalták. További hivatkozásaz [Apache Hadoop YARN Concepts című témakörben.](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)

Az alkalmazásnaplók (és a hozzájuk tartozó tárolónaplók) kritikus fontosságúak a problémás Hadoop-alkalmazások hibakeresésében. A YARN egy szép keretet biztosít az alkalmazásnaplók gyűjtéséhez, összesítéséhez és tárolásához a [Naplóösszesítés](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) funkcióval. A Naplóösszesítés szolgáltatás az alkalmazásnaplók elérését determinisztikusabbá teszi. Összesíti a naplókat a munkavégző csomópont összes tárolója között, és munkavégző csomópontonként egy összesített naplófájlként tárolja őket. A napló az alkalmazás befejezése után az alapértelmezett fájlrendszerben tárolódik. Az alkalmazás több száz vagy több ezer tárolót használhat, de az egyetlen munkavégző csomóponton futó összes tároló naplói mindig egyetlen fájlba vannak összesítve. Így az alkalmazás által használt feldolgozócsomópontonként csak 1 napló van. A log összesítés alapértelmezés szerint engedélyezve van a HDInsight-fürtök 3.0-s vagy újabb verzióiban. Az összesített naplók a fürt alapértelmezett tárolójában találhatók. A következő elérési út a naplók HDFS elérési útja:

```
/app-logs/<user>/logs/<applicationId>
```

Az elérési `user` úton annak a felhasználónak a neve, aki elindította az alkalmazást. Ez `applicationId` a YARN RM által egy alkalmazáshoz rendelt egyedi azonosító.

Az összesített naplók közvetlenül nem olvashatók, mivel [tfile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf)- [bináris formátumban](https://issues.apache.org/jira/browse/HADOOP-3315) bináris tárolóáltal indexelt. A YARN ResourceManager naplók vagy CLI-eszközök segítségével tekintse meg ezeket a naplókat egyszerű szövegként alkalmazások vagy fontos tárolók.

## <a name="yarn-logs-in-an-esp-cluster"></a>Fonalnaplók ESP-fürtben

Két konfigurációt kell hozzáadni `mapred-site` az ambari egyéni beállításához.

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net`meg `CLUSTERNAME` a , ahol a fürt neve.

1. Az Ambari felhasználói felületén keresse meg a **MapReduce2** > **Configs** > **Advanced** > Custom**mapred-site webhelyet.**

1. Adja hozzá az alábbi tulajdonságkészletek *egyikét:*

    **1. beállítás**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **2. készlet**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Mentse a módosításokat, és indítsa újra az összes érintett szolgáltatást.

## <a name="yarn-cli-tools"></a>YARN CLI eszközök

1. Az [ssh paranccsal](./hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Sorolja fel az aktuálisan futó Fonalalkalmazások összes alkalmazásazonosítóját a következő paranccsal:

    ```bash
    yarn top
    ```

    Jegyezze fel az `APPLICATIONID` alkalmazásazonosítót abból az oszlopból, amelynek naplóit le kell tölteni.

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

1. Ezeket a naplókat egyszerű szövegként tekintheti meg az alábbi parancsok egyikének futtatásával:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Adja &lt;meg az applicationId>, &lt;a user-who-started-the-application>, &lt;a containerId> és &lt;a worker-node-address> adatokat a parancsok futtatásakor.

### <a name="other-sample-commands"></a>Egyéb mintaparancsok

1. Töltse le a Fonaltárolók naplóit az összes alkalmazásmintához az alábbi paranccsal. Ezzel szöveges formátumban `amlogs.txt` elnevezett naplófájlt hoz létre.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. A Fonaltároló-naplók letöltése csak a legújabb alkalmazáskezelő főkiszolgálóra a következő paranccsal:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Töltse le a YARN tárolónaplókat az első két alkalmazásmintarendszerhez a következő paranccsal:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Töltse le az összes fonaltároló-naplót a következő paranccsal:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Töltsön le fonaltárolónaplót egy adott tárolóhoz a következő paranccsal:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager felhasználói felülete

A YARN ResourceManager felhasználói felülete a fürt csomópontján fut. Az Ambari webes felhasználói felületén keresztül érhető el. A YARN naplók megtekintéséhez kövesse az alábbi lépéseket:

1. A böngészőben keresse `https://CLUSTERNAME.azurehdinsight.net`meg a ( vagy a ) Cserélje le a CLUSTERNAME kifejezést a HDInsight-fürt nevére.

2. A bal oldali szolgáltatások listájában válassza a **YARN**lehetőséget.

    ![Apache Ambari Yarn szolgáltatás kiválasztva](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. A **Gyorshivatkozások** legördülő menüben jelölje ki a fürtfőcsomópontok egyikét, majd válassza a **ResourceManager-napló**lehetőséget.

    ![Apache Ambari Fonal gyors linkek](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Ön bemutatott egy listát a linkeket YARN naplók.
