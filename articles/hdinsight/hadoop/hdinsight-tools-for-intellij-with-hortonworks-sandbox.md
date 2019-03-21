---
title: With hortonworks – tesztkörnyezet IntelliJ-hez készült Azure-eszközkészlet használata
description: Ismerje meg a HDInsight Tools with hortonworks – tesztkörnyezet IntelliJ-hez készült Azure-eszközkészlet használata.
keywords: hadoop tools,hive query,intellij,hortonworks sandbox,azure toolkit for intellij
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 98f22f531ca15bf88cd7d0a9add2851651e2eec7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118323"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>HDInsight Tools for IntelliJ with hortonworks – tesztkörnyezet használata

Ismerje meg, hogyan Apache Scala-alkalmazások fejlesztése, és tesztelje az alkalmazásokat a HDInsight Tools for IntelliJ használatával [hortonworks – tesztkörnyezet](https://hortonworks.com/products/sandbox/) fut a számítógépen. 

[Az IntelliJ IDEA](https://www.jetbrains.com/idea/) van egy Java integrált fejlesztőkörnyezet (IDE) számítógép szoftver fejlesztéséhez. Miután fejlesztése és tesztelik az alkalmazásokat a Hortonworks Sandbox, továbbléphet az alkalmazásokat a [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

- Hortonworks Data Platform (HDP) 2.4 a Hortonworks Sandbox fut a helyi számítógépen. HDP beállításával kapcsolatban lásd: [Ismerkedjen meg a Hadoop-tesztkörnyezet, a virtuális gépen az Apache Hadoop-ökoszisztéma](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > IntelliJ-hez készült HDInsight-eszközök csak a HDP 2.4 tesztelték. Első HDP 2.4, bontsa ki a **Hortonworks Sandbox archív** a a [hortonworks – tesztkörnyezet tölti le a hely](https://hortonworks.com/downloads/#sandbox).

- [Java fejlesztői készlet (JDK) 1.8-as vagy újabb verzió](https://aka.ms/azure-jdks). IntelliJ-hez készült Azure-eszközkészlet JDK van szükség.

- [Az IntelliJ IDEA community Edition kiadását](https://www.jetbrains.com/idea/download) az a [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) beépülő modul és a [IntelliJ-hez készült Azure-eszközkészlet](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) beépülő modult. HDInsight Tools for IntelliJ érhető el az IntelliJ-hez készült Azure-eszközkészlet részeként. 

A beépülő modulok telepítése:

  1. Nyissa meg az IntelliJ IDEA-t.
  2. Az a **üdvözlő** lapon jelölje be **konfigurálása**, majd válassza ki **beépülő modulok**.
  3. A bal alsó sarokban válassza **eszközt telepítése beépülő modul**.
  4. Keresse meg a keresési funkció használatával **Scala**, majd válassza ki **telepítése**.
  5. A telepítés végrehajtásához válassza **indítsa újra az IntelliJ IDEA**.
  6. Ismételje meg a 4. és 5 telepítéséhez **IntelliJ-hez készült Azure-eszközkészlet**. További információkért lásd: [IntelliJ-hez készült Azure eszközkészlet telepítése](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Hozzon létre egy Apache Spark Scala-alkalmazások

Ebben a szakaszban az IntelliJ IDEA használatával hoz létre egy mintaprojektet Scala. A következő szakaszban társítja az IntelliJ IDEA a Hortonworks Sandbox (emulátor) a projekt mentése előtt.

1. Nyissa meg az IntelliJ IDEA a számítógépen. Az a **új projekt** párbeszédpanelen töltse ki a következő lépéseket:

   1. Válassza ki a **HDInsight** > **Spark on HDInsight (Scala)** lehetőséget.
   2. Az a **buildelőeszköze** listában válasszon a következő, a forgatókönyv alapján:

      * **Maven**: Scala project-létrehozási varázsló támogatás.
      * **SBT**: A függőségek kezelése, és a Scala-projekt létrehozásához.

   ![A New project (Új projekt) párbeszédablak.](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Kattintson a **Tovább** gombra.
3. A következő **új projekt** párbeszédpanelen töltse ki az alábbi lépéseket:

   1. Az a **projektnév** adja meg a projekt nevét.
   2. Az a **projekt helyét** adja meg a projekt helyére.
   3. Mellett a **projekt SDK** legördülő listában válassza **új**válassza **JDK**, majd adja meg a mappa a Java JDK 1.7 vagy újabb verziója. Válassza ki **Java 1.8-as** 2.x Spark-fürt. Válassza ki **Java 1.7-es** 1.x Spark-fürt. The default location is C:\Program Files\Java\jdk1.8.x_xxx.
   4. Az a **Spark-verzió** legördülő listából válassza ki, a Scala-projekt létrehozása varázsló együttműködik a megfelelő verzió az SDK a Spark és a Scala SDK. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa a Spark 1.6.2-es verzióján (Scala 2.10.5) használja. Győződjön meg arról, hogy használ-e megjelölve a tárház **Scala 2.10.x**. Ne használja a tárház Scala megjelölve 2.11.x.
    
      ![Create IntelliJ Scala project properties](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Válassza a **Finish** (Befejezés) elemet.
5. Ha a **projekt** nézet még nincs megnyitva, nyomja meg az **Alt + 1** való megnyitásához.
6. A **Project Explorer**, bontsa ki a projektet, és válassza **src**.
7. Kattintson a jobb gombbal **src**, mutasson a **új**, majd válassza ki **Scala osztály**.
8. Az a **neve** adjon meg egy nevet. Az a **Kind** jelölje ki **objektum**. Ezután válassza az **OK** lehetőséget.

    ![Az új Scala osztály létrehozása párbeszédpanel](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. A .scala fájlban illessze be a következő kódot:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. Az a **összeállítása** menüjében válassza **Build project**. Győződjön meg arról, hogy a fordítás sikeresen befejeződik.


## <a name="link-to-the-hortonworks-sandbox"></a>A Hortonworks Sandbox mutató hivatkozás

A Hortonworks Sandbox (emulátor) kapcsolat, mielőtt egy meglévő IntelliJ alkalmazást kell rendelkeznie.

Az emulator összekapcsolása:

1. Nyissa meg a projektet az intellij-ben.
2. Az a **nézet** menüjében válassza **eszközök Windows**, majd válassza ki **Azure Explorer**.
3. Bontsa ki a **Azure**, kattintson a jobb gombbal **HDInsight**, majd válassza ki **hivatkozás egy emulátoron**.
4. Az a **hivatkozás egy új emulátor** párbeszédpanel mezőbe írja be a rendszergazdafiók, a Hortonworks Sandbox beállított jelszót. Ezután adja meg az értékeket használja az alábbi képernyőképen hasonlóak. Ezután válassza az **OK** lehetőséget. 

   ![A hivatkozás egy új emulátor párbeszédpanel](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Az emulátor konfigurálásához válasszon **Igen**.

Az emulator sikeresen csatlakoztatva van, amikor az emulátor (hortonworks – tesztkörnyezet) szerepel a HDInsight-csomópont.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Küldje el a Spark Scala alkalmazást a hortonworks – tesztkörnyezet

Után az IntelliJ IDEA van csatolva az emulátorban, elküldheti a projekthez.

Az emulátor egy projekt elküldése:

1. A **Project Explorer**, és kattintson a jobb gombbal a projektre, majd válassza ki **HDInsight Spark elküldése alkalmazás**.
2. Hajtsa végre a következő lépéseket:

    1. Az a **Spark-fürt (csak Linux)** legördülő listára, válassza ki a helyi hortonworks – tesztkörnyezet.
    2. Az a **Main osztály neve** mezőben válassza ki vagy adja meg a fő osztály nevét. Ebben az oktatóanyagban a név **GroupByTest**.

3. Válassza ki **elküldése**. A feladat beküldése naplókat a Spark küldésének eszköz ablakban láthatók.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [IntelliJ-hez készült Azure-eszközkészlet HDInsight Tools használatával hozzon létre egy HDInsight Spark Linux-fürt az Apache Spark-alkalmazások](../spark/apache-spark-intellij-tool-plugin.md).

- A HDInsight Tools for intellij-vel kapcsolatos videót [Apache Spark fejlesztési IntelliJ-hez készült HDInsight eszközök bevezetni](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Ismerje meg, hogyan [ssh-n keresztül az IntelliJ-hez egy HDInsight-fürtön az Azure-eszközkészlet az Apache Spark-alkalmazások távoli hibakeresése](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Ismerje meg, hogyan [az IntelliJ-hez készült Azure-eszközkészlet HDInsight Tools használata Apache Spark-alkalmazások távolról a HDInsight Spark Linux-fürt](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Ismerje meg, hogyan [HDInsight-eszközök használata az Apache Spark-alkalmazások létrehozása az Eclipse-hez készült Azure-eszközkészlet](../spark/apache-spark-eclipse-tool-plugin.md).

- Eclipse-hez készült HDInsight-eszközökkel kapcsolatos egy videót, [HDInsight Tools használata Spark-alkalmazások létrehozása az Eclipse-hez](https://mix.office.com/watch/1rau2mopb6fha).
