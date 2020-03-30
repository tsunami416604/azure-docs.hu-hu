---
title: Yarn – hiba elhárítása az Azure HDInsightban
description: Válaszok az Apache Hadoop YARN és az Azure HDInsight használatával kapcsolatos gyakori kérdésekre.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: f0c7b966b9fa7580809d2df0f4d05a7146ca0fd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272200"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Az Apache Hadoop YARN hibaelhárítása az Azure HDInsighttal

Ismerje meg a legfontosabb problémákat és azok megoldásait, amikor az Apache Hadoop YARN rakományával dolgozik az Apache Ambari-ban.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Hogyan hozhatok létre új YARN-várólistát egy fürtön?

### <a name="resolution-steps"></a>A megoldás lépései

Az Ambari következő lépéseivel hozzon létre egy új YARN várólistát, majd egyensúlyozza ki a kapacitásallokációt az összes várólista között.

Ebben a példában két meglévő várólista **(alapértelmezett** és **thriftsvr)** egyaránt 50%-os kapacitásról 25%-os kapacitásra változik, ami az új várólista (szikra) 50%-os kapacitást biztosít.

| Várólista | Kapacitás | Maximális kapacitás |
| --- | --- | --- |
| alapértelmezett | 25% | 50% |
| thrftsvr között | 25% | 50% |
| spark | 50% | 50% |

1. Jelölje ki az **Ambari nézetek** ikont, majd a rácsmintát. Ezután válassza a **YARN várólista-kezelő t**.

    ![Apache Ambari műszerfal YARN várólista-kezelő](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Válassza ki az **alapértelmezett** várólistát.

    ![Az Apache Ambari YARN alapértelmezett várólistát választ](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. Az **alapértelmezett** várólista esetében módosítsa a **kapacitást** 50%-ról 25%-ra. A **thriftsvr-várólistához** módosítsa a **kapacitást** 25%-ra.

    ![Módosítsa a kapacitást 25%-ra az alapértelmezett és a takarékossági vr várólisták esetében](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Új várólista létrehozásához válassza **a Várólista hozzáadása**lehetőséget.

    ![Apache Ambari YARN irányítópult várólistának hozzáadása](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Nevezze el az új várólistát.

    ![Apache Ambari YARN irányítópult neve Várólista](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Hagyja a **kapacitásértékeket** 50%-on, majd kattintson a **Műveletek** gombra.

    ![Apache Ambari YARN kijelölési művelet](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Válassza **a Várólisták mentése és frissítése**lehetőséget.

    ![Várólisták mentése és frissítése jelölőnégyzetet](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Ezek a változások azonnal láthatók a YARN scheduler felhasználói felületén.

### <a name="additional-reading"></a>További olvasás

- [Apache Hadoop YARN kapacitásütemező](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Hogyan tölthetek le YARN naplókat egy fürtből?

### <a name="resolution-steps"></a>A megoldás lépései

1. Csatlakozzon a HDInsight-fürthöz egy Biztonságos rendszerhéj (SSH) ügyfél használatával. További információt a További olvasás című témakörben [talál.](#additional-reading-2)

1. A yarn alkalmazások összes futó alkalmazásazonosítójának listázásához futtassa a következő parancsot:

    ```apache
    yarn top
    ```

    Az azonosítók az **APPLICATIONID** oszlopban jelennek meg. A naplókat az **APPLICATIONID** oszlopból töltheti le.

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

1. A YARN tárolónaplók letöltéséhez használja a következő parancsot:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Ez a parancs létrehoz egy amlogs.txt nevű naplófájlt.

1. Ha csak a legújabb alkalmazáskezelő törzsszámára szeretné letölteni a YARN tárolónaplókat, használja a következő parancsot:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Ez a parancs egy latestamlogs.txt nevű naplófájlt hoz létre.

1. Az első két alkalmazásminta YARN tárolónaplójának letöltéséhez használja a következő parancsot:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Ez a parancs létrehoz egy first2amlogs.txt nevű naplófájlt.

1. Az összes YARN tárolónapló letöltéséhez használja a következő parancsot:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Ez a parancs egy logs.txt nevű naplófájlt hoz létre.

1. Egy adott tároló YARN tárolónaplójának letöltéséhez használja a következő parancsot:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Ez a parancs egy containerlogs.txt nevű naplófájlt hoz létre.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>További olvasás

- [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Az Apache Hadoop YARN koncepciói és alkalmazásai](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

- Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

- Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
