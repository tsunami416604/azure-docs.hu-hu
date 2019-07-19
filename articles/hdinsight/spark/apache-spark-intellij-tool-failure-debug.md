---
title: 'Hiba a Spark-feladatok hibakereséséhez Azure Toolkit for IntelliJ (előzetes verzió) '
description: Részletes útmutató a HDInsight-eszközök használatáról a Azure Toolkit for IntelliJban az alkalmazások távoli HDInsight-fürtökön keresztül SSH-n keresztül történő hibakereséséhez
keywords: távoli IntelliJ hibakeresése, távoli hibakeresés IntelliJ, SSH, IntelliJ, hdinsight, hibakeresési IntelliJ, hibakeresés
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: aff9f0f70377ebc6e741618b22ff82bc06251521
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295911"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Hiba a Spark-feladatok hibakereséséhez Azure Toolkit for IntelliJ (előzetes verzió)

Ez a cikk részletes útmutatást nyújt arról, hogyan használhatja a HDInsight-eszközöket a [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) a **Spark-hibák hibakeresési** alkalmazásainak futtatásához. 

## <a name="prerequisites"></a>Előfeltételek
* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Ez az oktatóanyag a Java-verzió 8.0.202 használja.
  
* IntelliJ IDEA. Ez a cikk [a IntelliJ IDEA Community ver-t használja. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Lásd: [a Azure Toolkit for IntelliJ telepítése](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* Kapcsolódjon a HDInsight-fürthöz. Lásd: [Kapcsolódás a HDInsight-fürthöz](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Storage Explorer. Lásd a [Microsoft Azure Storage Explorer letöltését](https://azure.microsoft.com/features/storage-explorer/)ismertető témakört.

## <a name="create-a-project-with-debugging-template"></a>Projekt létrehozása hibakeresési sablonnal 

Hozzon létre egy Spark 2.3.2-projektet a hibák hibakeresésének folytatásához, végezze el a hiba feladatának hibakeresését a dokumentumban.

1. Nyissa meg az IntelliJ IDEA-t. Nyissa meg az **új projekt** ablakot.

   a. Válassza ki az **Azure Spark/HDInsight** elemet a bal oldali panelen. 

   b. A főablakból válassza a hiba feladatának hibakeresése (előzetes verzió) lehetőséget a **Spark Project szolgáltatásban** .

     ![Hibakeresési projekt létrehozása](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Kattintson a **Tovább** gombra.     
 
2. Az **új projekt** ablakban végezze el a következő lépéseket:

   ![Válassza ki a Spark SDK-t](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Adja meg a projekt nevét és a projekt helyét.

   b. A **Project SDK** legördülő listában válassza ki a következőt: **Java 1,8** for **Spark 2.3.2** -fürt.

   c. A **Spark-verzió** legördülő listában válassza a **Spark 2.3.2 (Scala 2.11.8)** elemet.

   d. Válassza a **Finish** (Befejezés) elemet.

3. Válassza a **src** > **Main** > **Scala** elemet a kód a projektben való megnyitásához. Ez a példa a **AgeMean_Div ()** parancsfájlt használja.

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Spark Scala/Java-alkalmazás futtatása HDInsight-fürtön

Hozzon létre egy Spark Scala/Java-alkalmazást, majd futtassa az alkalmazást egy Spark-fürtön az alábbi lépések végrehajtásával:

1. Kattintson a **konfiguráció hozzáadása** elemre a **futtatási/hibakeresési konfigurációk** ablak megnyitásához.

   ![Konfigurációk szerkesztése](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png) 

2. A **Futtatás/hibakeresés konfiguráció** párbeszédpanelen válassza a pluszjelet ( **+** ). Ezután válassza ki a **Apache Spark a HDInsight** beállításnál.

   ![Új konfiguráció hozzáadása](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Váltson **távoli futtatásra a fürt** lapon. Adja meg a **név**, a **Spark-fürt**és a **fő osztály nevét**. Eszközeink támogatják a **végrehajtókkal**való hibakeresést. A **numExectors**, az alapértelmezett érték 5, és jobb, ha nem nagyobb, mint 3. A futási idő csökkentése érdekében a **Spark. fonál. maxAppAttempts** felveheti a **feladatok konfigurációba** , és az értéket 1-re állíthatja. A konfiguráció mentéséhez kattintson **az OK** gombra.

   ![Hibakeresési konfigurációk futtatása](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. A konfiguráció mostantól a megadott névvel lett mentve. A konfiguráció részleteinek megtekintéséhez válassza ki a konfiguráció nevét. A módosítások elvégzéséhez válassza a **konfigurációk szerkesztése**lehetőséget. 

5. A konfigurációk beállításainak elvégzése után a projektet a távoli fürtön futtathatja.
   
   ![Távoli Futtatás gomb](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Az alkalmazás AZONOSÍTÓját a kimenet ablakban tekintheti meg.
   
   ![Távoli Futtatás gomb](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)   

## <a name="download-failed-job-profile"></a>Sikertelen munkahelyi profil letöltése

Ha a feladatok elküldése meghiúsul, a további hibakereséshez letöltheti a sikertelen feladatot a helyi gépre.

1. Nyissa meg **Microsoft Azure Storage Explorer**, keresse meg a sikertelen feladatokhoz tartozó fürt HDInsight-fiókját, töltse le a sikertelen feladatok erőforrásait a megfelelő helyről: **\hdp\spark2-Events\\.\\ Spark-hibákaz\<alkalmazás-azonosító >** egy helyi mappába. A **tevékenységek** ablakban megjelennek a letöltési folyamat.

   ![letöltési hiba fájlja](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![letöltési hiba fájlja](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)   

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Helyi hibakeresési környezet konfigurálása és sikertelen hibakeresés

1. Nyissa meg az eredeti projektet, vagy hozzon létre egy új projektet, és társítsa az eredeti forráskódhoz. Jelenleg csak a Spark 2.3.2 verziója támogatott a hibák hibakereséséhez.

2. A IntelliJ IDEA-ben hozzon létre egy **Spark-hiba hibakeresési** konfigurációs fájlját, válassza ki az FTD-fájlt a korábban letöltött sikertelen feladatok erőforrásai közül a Spark-feladatokhoz tartozó **környezeti hely** mezőnél.
   
   ![Távoli Futtatás gomb](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

4. Kattintson a helyi Futtatás gombra az eszköztáron, a hiba a Futtatás ablakban jelenik meg.
   
   ![Távoli Futtatás gomb](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![Távoli Futtatás gomb](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

5. Állítsa be a töréspontot a naplóban, majd kattintson a helyi hibakeresés gombra a helyi hibakereséshez ugyanúgy, mint a IntelliJ szokásos Scala/Java-projektjei.

5. A hibakeresés után, ha a projekt sikeresen befejeződik, újra elküldheti a sikertelen feladatot a Spark on HDInsight-fürtön.

## <a name="seealso"></a>Következő lépések
* [Áttekintés Alkalmazások hibakeresése Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Bemutató
* Scala-projekt létrehozása (videó): [Apache Spark Scala-alkalmazások létrehozása](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Távoli hibakeresés (videó): [Apache Spark alkalmazások távoli hibakeresése HDInsight-fürtön Azure Toolkit for IntelliJ használatával](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Forgatókönyvek
* [Apache Spark BI-val: Interaktív adatelemzés a Spark in HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learningkal: A Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatszolgáltatások használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningkal: Az élelmiszer-vizsgálati eredmények előrejelzése a Spark in HDInsight használatával](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](../hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Azure Toolkit for IntelliJ használata Apache Spark-alkalmazások HDInsight-fürthöz való létrehozásához](apache-spark-intellij-tool-plugin.md)
* [Apache Spark-alkalmazások távoli hibakeresése a Azure Toolkit for IntelliJ használatával VPN-en keresztül](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [A IntelliJ HDInsight-eszközeinek használata a Hortonworks-homokozóban](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Apache Spark alkalmazások létrehozásához használja a Azure Toolkit for Eclipse HDInsight-eszközeit](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [Apache Spark a Jupyter notebookhoz elérhető kernelek a HDInsight-fürtön a](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
