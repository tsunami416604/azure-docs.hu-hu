---
title: Az Azure HDInsight Apache Spark hibáinak megoldása
description: Az Apache Spark és az Azure HDInsight használatához kapcsolatos gyakori kérdésekre adott válaszok.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 8931f9b09836d30f95e25cee245932475c3cf64c
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018430"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Hibaelhárítás az Apache Spark az Azure HDInsight segítségével

Ismerje meg a leggyakoribb problémákat és azok megoldásait, amikor Apache Spark hasznos adatokkal dolgozik az [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Egy Apache Spark-alkalmazás konfigurálása az Apache Ambari-fürtökön

A Spark konfigurációs értékei a Apache Spark alkalmazás `OutofMemoryError` kivételének elkerülése érdekében állíthatók be. Az alábbi lépések az alapértelmezett Spark-konfigurációs értékeket mutatják be az Azure HDInsight:

1. Jelentkezzen be a Ambari `https://CLUSTERNAME.azurehdidnsight.net` -ba a fürt hitelesítő adataival. A kezdeti képernyő egy áttekintő irányítópultot jelenít meg. A HDInsight 3,6 és 4,0 közötti enyhe kozmetikai különbségek vannak.

1. Navigáljon a **Spark2** > -**konfigurációkhoz**.

    ![Válassza ki a Configs lap](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. A konfigurációk listájában válassza ki és bontsa ki az **Egyéni-spark2 – alapértelmezett értékeket**.

1. Keresse meg a beállítás, amely kell beállítani, mint például **spark.executor.memory**. Ebben az esetben a **9728m** értéke túl magas.

    ![Válassza ki az egyéni – a spark-alapértelmezései](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Állítsa az értékét az ajánlott beállítás. Az érték **2048m** állítja ezt a beállítást javasoljuk.

1. Mentse az értéket, és mentse a konfigurációt. Kattintson a **Mentés** gombra.

    ![Módosítsa az értéket a 2048m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Írjon megjegyzést a konfigurációs változásokról, és válassza ki **mentése**.

    ![Adja meg a végzett módosítások kapcsolatos megjegyzés](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Ha a figyelmet igénylő konfigurációk értesítést kap. Jegyezze fel az elemeket, és válassza ki **folytatja ennek ellenére**.

    ![Válassza ki folytatja ennek ellenére](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Amikor egy konfigurációs mentette, a rendszer kéri, indítsa újra a szolgáltatást. Válassza ki **indítsa újra a**.

    ![Válassza az újraindítás](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Erősítse meg az újraindítást.

    ![Indítsa újra az összes jóváhagyás kijelölése](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    A futó folyamatok tekintheti meg.

    ![Tekintse át a futó folyamatok](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Konfigurációkat adhat hozzá. Konfigurációk listájából válassza ki **egyéni-spark2 – alapértelmezett**, majd válassza ki **tulajdonság hozzáadása**.

    ![Válassza ki a tulajdonság hozzáadása](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Adjon meg egy új tulajdonság. Egyetlen tulajdonság meghatározhatja az egyes beállítások, például az adattípus párbeszédpanel használatával. Vagy több tulajdonságok adhatók soronként egy definíció használatával.

    Ebben a példában a **spark.driver.memory** tulajdonság értékkel van definiálva **4g**.

    ![Adja meg az új tulajdonság](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. A konfiguráció mentéséhez, és indítsa újra a 6 és 7 lépésben ismertetett módon.

Ezek a változások fürtre kiterjedő, de a Spark-feladat elküldésekor felülbírálható.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Egy Apache Spark-alkalmazás konfigurálása a fürtök Jupyter notebook használatával

Az első olyan cellára, a Jupyter notebookot az után a **%% konfigurálása** irányelv, érvényes JSON formátumban adja meg a Spark-konfigurációkat. A tényleges értékek módosítása szükséges:

![Konfiguráció hozzáadása](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Egy Apache Spark-alkalmazás konfigurálása fürtökön Apache Livy használatával

Küldje el a Livy-, Spark-alkalmazás REST-ügyfél, például a cURL használatával. Használjon az alábbihoz hasonló parancsot. A tényleges értékek módosítása szükséges:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Hogyan konfigurálhatom egy Apache Spark, az alkalmazás a spark-submit fürtökön?

Indítsa el a spark-shell az alábbihoz hasonló parancs használatával. Szükség szerint módosítsa a konfiguráció a tényleges érték:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>További olvasnivaló

[A HDInsight-fürtökön az Apache Spark-feladat küldése](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* A [Spark memória-kezelési áttekintése](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [A Spark-alkalmazás hibakeresése a HDInsight-fürtökön](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
