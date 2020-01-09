---
title: Azure Toolkit for IntelliJ használata a Hortonworks-homokozóban
description: Megtudhatja, hogyan használhatja a Azure Toolkit for IntelliJ HDInsight-eszközeit a Hortonworks-homokozóban.
keywords: Hadoop-eszközök, kaptár-lekérdezés, IntelliJ, hortonworks-homokozó, Azure Toolkit for IntelliJ
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/16/2018
ms.openlocfilehash: 65a15a8506b88e95e14af8c87bcbe33087301519
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647832"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>A IntelliJ HDInsight-eszközeinek használata a Hortonworks-homokozóban

Ismerje meg, hogy miként használhatók az HDInsight Tools for IntelliJ az Apache Scala-alkalmazások fejlesztéséhez, majd tesztelheti az alkalmazásokat a számítógépen futó [Hortonworks-homokozóban](https://hortonworks.com/products/sandbox/) . 

A [INTELLIJ Idea](https://www.jetbrains.com/idea/) egy Java integrált fejlesztési környezet (ide) a számítógépes szoftverek fejlesztéséhez. Miután fejleszti és teszteli az alkalmazásait a Hortonworks homokozóban, áthelyezheti az alkalmazásokat az [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdése előtt a következő elemeket kell megadnia:

- Hortonworks adatplatform (HDP) 2,4 a helyi számítógépen futó Hortonworks-munkaterületen. A HDP beállításával kapcsolatban lásd: [Bevezetés a Apache Hadoop ökoszisztémába egy virtuális gépen futó Hadoop-homokozóval](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > A IntelliJ HDInsight-eszközei csak a HDP 2,4-mel lettek tesztelve. A HDP 2,4 lekéréséhez bontsa ki a **Hortonworks sandbox Archive** elemet a [Hortonworks sandbox letöltési webhelyén](https://hortonworks.com/downloads/#sandbox).

- A [Java Developer Kit (JDK) 1,8-es vagy újabb verziója](https://aka.ms/azure-jdks). Azure Toolkit for IntelliJ a JDK-t igényli.

- A [INTELLIJ Idea Community Edition](https://www.jetbrains.com/idea/download) a [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) beépülő modullal és a [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) beépülő modullal. A IntelliJ HDInsight eszközei a Azure Toolkit for IntelliJ részeként érhetők el. 

A beépülő modulok telepítése:

  1. Nyissa meg az IntelliJ IDEA-t.
  2. Az **Üdvözöljük** lapon válassza a **Konfigurálás**lehetőséget, majd válassza a **plugins**lehetőséget.
  3. A bal alsó sarokban válassza a **JetBrains beépülő modul telepítése**lehetőséget.
  4. Használja a Search függvényt a **Scala**megkereséséhez, majd válassza a **telepítés**lehetőséget.
  5. A telepítés befejezéséhez válassza a **INTELLIJ ötlet újraindítása**lehetőséget.
  6. **Azure Toolkit for IntelliJ**telepítéséhez ismételje meg a 4. és az 5. lépést. További információ: [Install Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Apache Spark Scala-alkalmazás létrehozása

Ebben a szakaszban egy, a IntelliJ IDEA használatával létrehozott példa Scala-projektet hoz létre. A következő szakaszban a projekt elküldése előtt összekapcsolja a IntelliJ IDEA-t a Hortonworks-homokozóval (Emulator).

1. Nyissa meg a IntelliJ ÖTLETET a számítógépén. Az **új projekt** párbeszédpanelen hajtsa végre a következő lépéseket:

   1. Válassza ki a **HDInsight** > **Spark on HDInsight (Scala)** lehetőséget.
   2. A **Build-eszköz** listában válassza a következők egyikét a forgatókönyv alapján:

      * **Maven**: a Scala projekt-létrehozási varázsló támogatásához.
      * **SBT**: függőségek kezeléséhez és a Scala projekt létrehozásához.

   ![IntelliJ új Scala-projekt létrehozása](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Kattintson a **Tovább** gombra.
3. A következő **új projekt** párbeszédpanelen hajtsa végre a következő lépéseket:

   1. A **projekt neve** mezőben adja meg a projekt nevét.
   2. A **projekt helye** mezőben adja meg a projekt helyét.
   3. A **Project SDK** legördülő lista mellett válassza az **új**lehetőséget, válassza a **JDK**lehetőséget, majd adja meg a Java jdk 1,7-es vagy újabb verziójának mappáját. Válassza a **Java 1,8** lehetőséget a Spark 2. x fürthöz. Válassza a **Java 1,7** lehetőséget a Spark 1. x fürthöz. Az alapértelmezett hely a C:\Program Files\Java\jdk1.8. x_xxx.
   4. A **Spark-verzió** legördülő listában a Scala projekt létrehozása varázsló a Spark SDK és a Scala SDK megfelelő verzióját integrálja. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa a Spark 1.6.2 (Scala 2.10.5) szolgáltatást használja. Győződjön meg arról, hogy az adattárat a **Scala 2.10. x**jelöléssel jelölte meg. Ne használja az adattárat, amely a Scala 2.11. x-et jelölte.
    
      ![IntelliJ Scala-projekt tulajdonságainak létrehozása](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Válassza a **Finish** (Befejezés) elemet.
5. Ha a **projekt** nézet még nincs megnyitva, nyomja le az **Alt + 1** billentyűkombinációt a megnyitásához.
6. A **Project Explorerben**bontsa ki a projektet, majd válassza az **src**elemet.
7. Kattintson a jobb gombbal az **src**elemre, mutasson az **új**elemre, majd válassza a **Scala osztály**elemet.
8. A **Név** mezőben adja meg a megfelelő nevet. A **típus** mezőben válassza az **objektum**lehetőséget. Ezután válassza az **OK** lehetőséget.

    ![Az új Scala-osztály létrehozása párbeszédpanel](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. A. Scala fájlban illessze be a következő kódot:

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

10. A **Build** menüben válassza a **projekt létrehozása**lehetőséget. Győződjön meg arról, hogy a fordítás sikeresen befejeződik.


## <a name="link-to-the-hortonworks-sandbox"></a>Hivatkozás a Hortonworks-Sandboxra

Ahhoz, hogy egy Hortonworks-Homokozóhoz (emulátorhoz) lehessen kapcsolni, rendelkeznie kell egy meglévő IntelliJ-alkalmazással.

Hivatkozás egy Emulátorra:

1. Nyissa meg a projektet a IntelliJ-ben.
2. A **nézet** menüben válassza az **eszközök Windows**, majd az **Azure Explorer**lehetőséget.
3. Bontsa ki az **Azure**-t, kattintson a jobb gombbal a **HDInsight**elemre, majd válassza **az emulátor csatolása**lehetőséget.
4. Az **új emulátor csatolása** párbeszédpanelen adja meg a Hortonworks-homokozó gyökeréhez beállított jelszót. Ezután adja meg az alábbi képernyőképen használt értékeket. Ezután válassza az **OK** lehetőséget. 

   ![Az új emulátor csatolása párbeszédpanel](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Az emulátor konfigurálásához válassza az **Igen**lehetőséget.

Az emulátor sikeres csatlakoztatása után az emulátor (Hortonworks sandbox) megjelenik a HDInsight csomóponton.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>A Spark Scala alkalmazás beküldése a Hortonworks-homokozóba

Miután összekapcsolta a IntelliJ GONDOLATát az emulátorral, elküldheti a projektjét.

Projekt elküldése emulátorba:

1. A **Project Explorerben**kattintson a jobb gombbal a projektre, majd válassza a **Spark-alkalmazás elküldése a HDInsight**lehetőséget.
2. Hajtsa végre a következő lépéseket:

    1. A **Spark-fürtben (csak Linux)** legördülő listában válassza ki a helyi Hortonworks-homokozót.
    2. A **fő osztály neve** mezőben válassza ki vagy adja meg a fő osztály nevét. Ebben a cikkben a név **GroupByTest**.

3. Válassza a **Küldés** lehetőséget. A beküldési naplók a Spark beküldési eszköz ablakában jelennek meg.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hozhat létre Apache Spark-alkalmazásokat egy HDInsight Spark Linux-fürthöz a Azure Toolkit for IntelliJ HDInsight eszközeinek használatával](../spark/apache-spark-intellij-tool-plugin.md).

- A IntelliJ készült HDInsight-eszközökről a következő témakörben talál további információt: [HDInsight-eszközök bevezetése a IntelliJ-hez a Apache Spark fejlesztéséhez](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Megtudhatja, hogyan végezhet [távolról hibakeresést Apache Spark-alkalmazásokat egy HDInsight-fürtön a Azure TOOLKIT for INTELLIJ SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)-n keresztül.

- Ismerje meg, hogyan [használhatja a Azure Toolkit for IntelliJ HDInsight-eszközeit a Apache Spark alkalmazások távoli hibakereséséhez egy HDInsight Spark Linux-fürtön](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Megtudhatja, hogyan [hozhat létre Apache Spark-alkalmazásokat a Azure Toolkit for Eclipse HDInsight eszközeinek használatával](../spark/apache-spark-eclipse-tool-plugin.md).

