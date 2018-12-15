---
title: Az Azure HDInsight YARN hibaelhárítása
description: Az Apache Hadoop YARN és az Azure HDInsight használatához kapcsolatos gyakori kérdésekre adott válaszok.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: d5582038c35ba3b599be89b7b7939e644d55ea78
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408830"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Az Azure HDInsight az Apache Hadoop YARN hibaelhárítása

A leggyakoribb problémák és azok megoldásait ismerje meg az Apache Ambari az Apache Hadoop YARN hasznos adatot használatakor.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Hogyan hozhatok létre egy új YARN-üzenetsorba egy fürtön?

### <a name="resolution-steps"></a>A megoldás lépései 

Ambari az alábbi lépések segítségével hozzon létre egy új YARN-várólistát, és ezután elosztja a kapacitás lefoglalása valamennyi üzenetsorok között. 

Ebben a példában két meglévő üzenetsorok (**alapértelmezett** és **thriftsvr**) is úgy módosul, 50 %-os kapacitásból 25 %-os kapacitását, amely az új várólista (spark) 50 %-os kapacitást biztosít.
| Várólista | Kapacitás | Maximális kapacitás |
| --- | --- | --- | --- |
| alapértelmezett | 25% | 50% |
| thrftsvr | 25% | 50% |
| A Spark | 50% | 50% |

1. Válassza ki a **Ambari-nézetek** ikonra, és válassza ki a rács minta. Majd **YARN üzenetsor-kezelő**.

    ![Az Ambari-nézetek ikon kiválasztása](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. Válassza ki a **alapértelmezett** várólista.

    ![Az alapértelmezett üzenetsor kiválasztása](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. Az a **alapértelmezett** várólista, módosítsa a **kapacitás** 50 % 25 %-át. Az a **thriftsvr** várólista, módosítsa a **kapacitás** 25 %-át.

    ![A kapacitás módosításához 25 %-át az alapértelmezett és thriftsvr várólisták](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. Új várólista létrehozásához válassza **üzenetsor hozzáadása**.

    ![Válassza ki az üzenetsor hozzáadása](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. Adjon nevet az új üzenetsort.

    ![A Spark várólista neve](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. Hagyja a **kapacitás** értékek 50 %-át, és válassza ki a **műveletek** gombra.

    ![A műveletek gomb kiválasztása](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. Válassza ki **mentse, és frissítse az üzenetsorok**.

    ![Válassza a Mentés és a frissítést](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Ezeket a módosításokat a YARN felhasználói felületén a Scheduler a azonnal láthatók.

### <a name="additional-reading"></a>További olvasnivaló

- [Az Apache Hadoop YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Hogyan tölthetek le YARN-naplókat fürtből?


### <a name="resolution-steps"></a>A megoldás lépései 

1. Csatlakozzon a HDInsight-fürthöz egy Secure Shell (SSH) ügyfél használatával. További információkért lásd: [További olvasnivaló](#additional-reading-2).

2. Az összes alkalmazás azonosítóját az aktuálisan futó YARN-alkalmazások listájában, futtassa a következő parancsot:

    ```apache
    yarn top
    ```
    Az azonosítók láthatók a **APPLICATIONID** oszlop. A naplókat letöltheti a **APPLICATIONID** oszlop.

    ```apache
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

3. Töltse le a YARN-naplóit tároló összes alkalmazás-főkiszolgálóhoz, használja a következő parancsot:
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Ez a parancs létrehoz egy amlogs.txt nevű naplófájlt. 

4. Töltse le a legfrissebb alkalmazás fő YARN-naplóit tároló, a következő paranccsal:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Ez a parancs létrehoz egy latestamlogs.txt nevű naplófájlt. 

4. Az első két alkalmazás főkiszolgálóhoz YARN-naplóit tároló letöltéséhez használja a következő parancsot:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    Ez a parancs létrehoz egy first2amlogs.txt nevű naplófájlt. 

5. Töltse le az összes tároló YARN-naplókat, használja a következő parancsot:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Ez a parancs létrehoz egy logs.txt nevű naplófájlt. 

6. A YARN tárolónapló egy adott tároló letöltéséhez használja a következő parancsot:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    Ez a parancs létrehoz egy containerlogs.txt nevű naplófájlt.

### <a name="additional-reading-2"></a>További olvasnivaló

- [Csatlakozhat a HDInsight (az Apache Hadoop) SSH-val](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Az Apache Hadoop YARN fogalmakat és alkalmazások](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)


### <a name="see-also"></a>Lásd még:
[Hibaelhárítás az Azure HDInsight segítségével](hdinsight-troubleshoot-guide.md)
