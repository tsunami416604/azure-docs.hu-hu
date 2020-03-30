---
title: Az Apache Spark – problémamegoldás az Azure HDInsightban
description: Válaszok az Apache Spark és az Azure HDInsight használatával kapcsolatos gyakori kérdésekre.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 80bca2dab1d07d9b99e75e283068bff99335fa18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271940"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Az Apache Spark hibaelhárítása az Azure HDInsighttal

Ismerje meg a legfontosabb problémákat és azok megoldásait, amikor az Apache Spark hasznos terhelésével dolgozik az [Apache Ambari-ban.](https://ambari.apache.org/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Hogyan konfigurálható egy Apache Spark-alkalmazás az Apache Ambari fürtökön történő használatával?

A Spark konfigurációs értékei hangolhatók, `OutofMemoryError` így elkerülhető az Apache Spark-alkalmazáskivétel. A következő lépések az Azure HDInsight alapértelmezett Spark-konfigurációs értékeit mutatják be:

1. Jelentkezzen be az Ambari-ba `https://CLUSTERNAME.azurehdidnsight.net` a fürt hitelesítő adataival. A kezdeti képernyő egy áttekintő irányítópultot jelenít meg. A HDInsight 3.6 és a 4.0 között kisebb kozmetikai különbségek vannak.

1. Keresse meg a **Spark2** > **Configs**.

    ![A Configs lap kijelölése](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. A konfigurációk listájában válassza ki és bontsa ki az **Egyéni szikra2-alapértelmezett beállításokat.**

1. Keresse meg a módosítani kívánt értékbeállítást, például **a spark.executor.memory**fájlt. Ebben az esetben a **9728m** értéke túl magas.

    ![Egyéni szikra-alapértelmezések kiválasztása](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Állítsa az értéket az ajánlott értékre. Ehhez a beállításhoz a **2048 m** érték ajánlott.

1. Mentse az értéket, majd mentse a konfigurációt. Kattintson a **Mentés** gombra.

    ![Érték módosítása 2048 m-re](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Írjon egy megjegyzést a konfigurációs változásokról, majd válassza a **Mentés gombot.**

    ![Megjegyzés megadása a végrehajtott módosításokról](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Értesítést kap, ha bármilyen konfigurációk figyelmet igényel. Jegyezze fel az elemeket, majd válassza **a Folytatás egyáltalán**lehetőséget.

    ![Válaszd a Folytatás tiszaszerint](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. A konfiguráció mentésekor a rendszer kéri a szolgáltatás újraindítását. Válassza **az Újraindítás**lehetőséget.

    ![Újraindítás kiválasztása](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Erősítse meg az újraindítást.

    ![Válassza az Újraindítás megerősítése az összeset lehetőséget](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Megtekintheti a futó folyamatokat.

    ![Futó folyamatok áttekintése](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Hozzáadhat konfigurációkat. A konfigurációk listájában válassza az **Egyéni szikra2-alapértelmezett lehetőséget,** majd a **Tulajdonság hozzáadása**lehetőséget.

    ![Tulajdonság hozzáadása kiválasztása](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Új tulajdonság definiálása. Egyetlen tulajdonságot definiálhat egy párbeszédpanel használatával bizonyos beállításokhoz, például az adattípushoz. Vagy több tulajdonságot is definiálhat soronként egy definíció használatával.

    Ebben a példában a **spark.driver.memory** tulajdonság **4g**értékben van definiálva.

    ![Új tulajdonság definiálása](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Mentse a konfigurációt, majd indítsa újra a szolgáltatást a 6.

Ezek a módosítások fürtszintűek, de felülbírálhatók a Spark-feladat elküldésekor.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Hogyan konfigurálható egy Apache Spark-alkalmazás egy Jupyter Notebook-jegyzetfüzet fürtökön történő használatával?

A Jupyter-jegyzetfüzet első cellájában a **%%configure direktíva** után adja meg a Spark-konfigurációkat érvényes JSON formátumban. Szükség szerint módosítsa a tényleges értékeket:

![Konfiguráció hozzáadása](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Hogyan konfigurálható egy Apache Spark-alkalmazás az Apache Livy fürtökön történő használatával?

Küldje el a Spark-alkalmazást livy egy REST-ügyfél, például cURL használatával. Az alábbihoz hasonló parancsot használjon. Szükség szerint módosítsa a tényleges értékeket:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Hogyan konfigurálható egy Apache Spark-alkalmazás a spark-submit fürtökön történő használatával?

Indítsa el a szikrahéjat az alábbihoz hasonló paranccsal. Szükség szerint módosítsa a konfigurációk tényleges értékét:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>További olvasás

[Apache Spark-feladatküldés HDInsight-fürtökön](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* [Spark memóriakezelés – áttekintés](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [A Spark-alkalmazás hibakeresése HDInsight-fürtökön.](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
