---
title: Az Azure HDInsight YARN hibaelhárítása
description: Az Apache Hadoop YARN és az Azure HDInsight használatához kapcsolatos gyakori kérdésekre adott válaszok.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: f0c7b966b9fa7580809d2df0f4d05a7146ca0fd1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272200"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Az Azure HDInsight használatával Apache Hadoop-FONALak hibáinak megoldása

A leggyakoribb problémák és azok megoldásait ismerje meg az Apache Ambari az Apache Hadoop YARN hasznos adatot használatakor.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Hogyan hozhatok létre egy új YARN-üzenetsorba egy fürtön?

### <a name="resolution-steps"></a>A megoldás lépései

Ambari az alábbi lépések segítségével hozzon létre egy új YARN-várólistát, és ezután elosztja a kapacitás lefoglalása valamennyi üzenetsorok között.

Ebben a példában két meglévő várólista (**alapértelmezett** és **thriftsvr**) is módosul 50%-os kapacitásról 25%-ra, ami az új üzenetsor (Spark) 50%-os kapacitását adja meg.

| Várólista | Kapacitás | Maximális kapacitás |
| --- | --- | --- |
| alapértelmezett | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. Válassza a **Ambari nézetek** ikont, majd válassza ki a rács mintát. Ezután válassza a **fonál üzenetsor-kezelő**elemet.

    ![Apache Ambari irányítópult-szál üzenetsor-kezelője](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Válassza ki az **alapértelmezett** várólistát.

    ![Apache Ambari-fonal – alapértelmezett üzenetsor kiválasztása](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. Az **alapértelmezett** várólista esetében módosítsa a **kapacitást** 50%-ról 25%-ra. A **thriftsvr** -várólista esetében módosítsa a **kapacitást** 25%-ra.

    ![A kapacitás módosításához 25 %-át az alapértelmezett és thriftsvr várólisták](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Új várólista létrehozásához válassza a **várólista hozzáadása**lehetőséget.

    ![Apache Ambari-FONÁL irányítópult-várólista hozzáadása](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Adjon nevet az új üzenetsort.

    ![Apache Ambari-szál irányítópultjának neve üzenetsor](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Hagyja meg a **kapacitás** értékeit 50%-ban, majd kattintson a **műveletek** gombra.

    ![Apache Ambari-fonal kiválasztása művelet](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Válassza **a Mentés és frissítés várólisták**lehetőséget.

    ![Válassza a Mentés és a frissítést](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Ezeket a módosításokat a YARN felhasználói felületén a Scheduler a azonnal láthatók.

### <a name="additional-reading"></a>További olvasnivaló

- [Apache Hadoop fonal CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Hogyan tölthetek le YARN-naplókat fürtből?

### <a name="resolution-steps"></a>A megoldás lépései

1. Csatlakozzon a HDInsight-fürthöz egy Secure Shell (SSH) ügyfél használatával. További információ: [további olvasás](#additional-reading-2).

1. Az összes alkalmazás azonosítóját az aktuálisan futó YARN-alkalmazások listájában, futtassa a következő parancsot:

    ```apache
    yarn top
    ```

    Az azonosítók a **APPLICATIONID** oszlopban jelennek meg. A naplók a **APPLICATIONID** oszlopból tölthetők le.

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

1. Töltse le a YARN-naplóit tároló összes alkalmazás-főkiszolgálóhoz, használja a következő parancsot:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Ez a parancs létrehoz egy amlogs.txt nevű naplófájlt.

1. Töltse le a legfrissebb alkalmazás fő YARN-naplóit tároló, a következő paranccsal:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Ez a parancs létrehoz egy latestamlogs.txt nevű naplófájlt.

1. Az első két alkalmazás főkiszolgálóhoz YARN-naplóit tároló letöltéséhez használja a következő parancsot:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Ez a parancs létrehoz egy first2amlogs.txt nevű naplófájlt.

1. Töltse le az összes tároló YARN-naplókat, használja a következő parancsot:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Ez a parancs létrehoz egy logs.txt nevű naplófájlt.

1. A YARN tárolónapló egy adott tároló letöltéséhez használja a következő parancsot:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Ez a parancs létrehoz egy containerlogs.txt nevű naplófájlt.

### <a name="additional-reading-2"></a>További olvasnivaló

- [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [A fonal-fogalmak és-alkalmazások Apache Hadoop](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

- Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
