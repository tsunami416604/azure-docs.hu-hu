---
title: "Hibaelhárítás YARN Azure HDInsight segítségével |} Microsoft Docs"
description: "Az Apache Hadoop yarn rendszerre és az Azure HDInsight kapcsolatos gyakori kérdésekre adott válaszok."
keywords: "Az Azure HDInsight, YARN, gyakori kérdések hibaelhárítási útmutatója, gyakori kérdések"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: a30d7a8e3f2e11a0c5ed05c5b68d75d32b138fb9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-yarn-by-using-azure-hdinsight"></a>Hibaelhárítás YARN Azure HDInsight segítségével

A legfőbb problémákat és azok megoldásait ismerje meg az Apache Ambari az Apache Hadoop YARN Payload van jelen használatakor.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Hogyan hozzon létre egy új YARN várólistát egy fürtön?


### <a name="resolution-steps"></a>Megoldási lépések 

Ambari az alábbi lépések segítségével hozzon létre egy új YARN várólistát, majd válassza az között az összes várólistán kapacitás lefoglalása egyenleg. 

Ebben a példában két meglévő várólisták (**alapértelmezett** és **thriftsvr**) is megváltoznak 50 % kapacitásából 25 % kapacitás, amely az új várólista (külső) 50 % kapacitást biztosít.
| Várólista | Kapacitás | Maximális kapacitás |
| --- | --- | --- | --- |
| Alapértelmezett | 25% | 50% |
| thrftsvr | 25% | 50% |
| Spark | 50% | 50% |

1. Válassza ki a **Ambari nézetek** ikonra, és válassza a rács mintát. Válassza ki, **YARN várólista-kezelő**.

    ![Válassza ki az Ambari nézetek ikon](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. Válassza ki a **alapértelmezett** várólista.

    ![Válassza ki az alapértelmezett várólista](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. Az a **alapértelmezett** várólista, módosítsa a **kapacitás** 50 % 25 %-át. Az a **thriftsvr** várólista, módosítsa a **kapacitás** 25 %-át.

    ![A kapacitás módosítsa az alapértelmezett és thriftsvr várólisták 25 %-át](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. Hozzon létre egy új várólistát, jelölje be **hozzáadása várólista**.

    ![Válassza ki a várólista hozzáadása](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. Az új várólista neve.

    ![A várólista Spark neve](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. Hagyja a **kapacitás** érték 50 %-át, és válassza ki azt a **műveletek** gombra.

    ![A műveletek gomb kiválasztása](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. Válassza ki **mentse és frissítse a várólisták**.

    ![Válassza ki, mentse, és a frissítést](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

A módosítások azonnal a YARN Feladatütemező felhasználói felület az láthatók.

### <a name="additional-reading"></a>További olvasnivaló

- [YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Hogyan le a YARN naplóit fürtből?


### <a name="resolution-steps"></a>Megoldási lépések 

1. Csatlakozás egy Secure Shell (SSH) ügyfél segítségével a HDInsight-fürthöz. További információkért lásd: [További olvasnivaló](#additional-reading-2).

2. A YARN alkalmazások éppen futó összes alkalmazás azonosítót felsorolásához futtassa a következő parancsot:

    ```apache
    yarn top
    ```
    Az azonosítók a a **APPLICATIONID** oszlop. Letöltheti a naplói a **APPLICATIONID** oszlop.

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

3. Minden alkalmazás főkiszolgálók YARN tároló naplók letöltéséhez a következő paranccsal:
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Ez a parancs egy amlogs.txt nevű naplófájlt hoz létre. 

4. Csak a legfrissebb alkalmazás főkiszolgáló YARN tároló naplók letöltéséhez a következő paranccsal:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Ez a parancs egy latestamlogs.txt nevű naplófájlt hoz létre. 

4. Az első két alkalmazás főkiszolgálók YARN tároló naplók letöltéséhez a következő paranccsal:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    Ez a parancs egy first2amlogs.txt nevű naplófájlt hoz létre. 

5. Minden YARN tároló naplók letöltéséhez a következő paranccsal:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Ez a parancs egy logs.txt nevű naplófájlt hoz létre. 

6. A YARN tároló egy adott tárolóhoz tartozó naplók letöltéséhez a következő paranccsal:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    Ez a parancs egy containerlogs.txt nevű naplófájlt hoz létre.

### <a name="additional-reading-2"></a>További olvasnivaló

- [Csatlakozás HDInsight (Hadoop) SSH használatával](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN fogalmakat és alkalmazások](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)


### <a name="see-also"></a>Lásd még:
[Hibaelhárítás az Azure HDInsight segítségével](hdinsight-troubleshoot-guide.md)







