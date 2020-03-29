---
title: Az Azure Toolkit for IntelliJ használata a Hortonworks sandboxsegítségével
description: Ismerje meg, hogyan használhatja a HDInsight-eszközöket az Azure Toolkit for IntelliJ és a Hortonworks sandbox használatával.
keywords: hadoop eszközök, hive lekérdezés, intellij, hortonworks homokozó, azúrkék eszközkészlet intellij
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/16/2018
ms.openlocfilehash: 65a15a8506b88e95e14af8c87bcbe33087301519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647832"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>HdInsight-eszközök használata az IntelliJ-hez a Hortonworks sandboxsegítségével

Ismerje meg, hogyan fejlesztheti az Apache Scala-alkalmazásokat a HDInsight Eszközök az IntelliJ-hez, majd tesztelheti az alkalmazásokat a számítógépen futó [Hortonworks sandbox-on.](https://hortonworks.com/products/sandbox/) 

[Az IntelliJ IDEA](https://www.jetbrains.com/idea/) egy Java integrált fejlesztői környezet (IDE) számítógépes szoftverek fejlesztéséhez. Miután kifejleszti és teszteli alkalmazásait a Hortonworks sandboxon, áthelyezheti az alkalmazásokat az [Azure HDInsightba.](apache-hadoop-introduction.md)

## <a name="prerequisites"></a>Előfeltételek

A cikk megkezdése előtt a következő elemekkel kell rendelkeznie:

- Hortonworks Data Platform (HDP) 2.4 a Hortonworks Sandbox fut a helyi számítógépen. A HDP beállításához olvassa [el az Első lépések az Apache Hadoop ökoszisztémában egy Hadoop sandbox segítségével egy virtuális gépen.](apache-hadoop-emulator-get-started.md) 
    > [!NOTE]
    > A HDInsight Tools for IntelliJ-t csak HDP 2.4-es teszttel tesztelték. Ahhoz, hogy HDP 2.4, bontsa **ki Hortonworks Sandbox Archive** a [Hortonworks Sandbox letöltések oldalon](https://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) 1.8-as vagy újabb verzió.](https://aka.ms/azure-jdks) Az Azure Toolkit for IntelliJ jdk-t igényel.

- [IntelliJ IDEA közösségi kiadás](https://www.jetbrains.com/idea/download) a [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) beépülő modullal és az [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) beépülő modullal. A HDInsight-eszközök az IntelliJ-hez az Azure Toolkit for IntelliJ részeként érhető el. 

A beépülő modulok telepítése:

  1. Nyissa meg az IntelliJ IDEA-t.
  2. Az **Üdvözlőlapon** válassza a **Konfigurálás**lehetőséget, majd a **Bővítmények**lehetőséget.
  3. A bal alsó sarokban válassza a **JetBrains telepítése bővítmény t.**
  4. A keresési funkcióval keresse meg a **Scala**elemet, majd válassza a **Telepítés**lehetőséget.
  5. A telepítés befejezéséhez válassza **az Újraindítás Az IntelliJ IDEA lehetőséget.**
  6. Ismételje meg a **4.** További információ: [Install Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Apache Spark Scala alkalmazás létrehozása

Ebben a szakaszban hozzon létre egy minta Scala projekt segítségével IntelliJ IDEA. A következő részben a projekt beküldése előtt összekapcsolhatja az IntelliJ IDEA-t a Hortonworks homokozóval (emulátor).

1. Nyissa meg az IntelliJ IDEA készüléket a számítógépen. Az **Új projekt** párbeszédpanelen hajtsa végre az alábbi lépéseket:

   1. Válassza a **HDInsight** > **Spark lehetőséget a HDInsight (Scala) területen.**
   2. A **Build eszköz** listában válasszon az alábbi esetek közül:

      * **Maven**: A Scala projektkészítő varázsló támogatásához.
      * **SBT**: A függőségek kezelésére és a Scala projekt létrehozásához.

   ![Intellij új scala projekt](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Válassza a **Tovább lehetőséget.**
3. A következő **Új projekt** párbeszédpanelen hajtsa végre az alábbi lépéseket:

   1. A **Projekt neve** mezőbe írjon be egy projektnevet.
   2. A **Projekt helye** mezőbe írja be a projekt helyét.
   3. A **Project SDK** legördülő lista mellett válassza az **Új**lehetőséget, válassza a **JDK**lehetőséget, majd adja meg a Java JDK 1.7-es vagy újabb verziójának mappáját. Válassza a **Java 1.8** a Spark 2.x fürthöz. Válassza a **Java 1.7** a Spark 1.x fürthöz. Az alapértelmezett hely a C:\Program Files\Java\jdk1.8.x_xxx.
   4. A **Spark verzió** legördülő listájában a Scala-projekt létrehozó varázsló integrálja a megfelelő verziót a Spark SDK és a Scala SDK. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa a Spark 1.6.2 (Scala 2.10.5) használatot használja. Győződjön meg arról, hogy a **Scala 2.10.x**jelölésű adattárat használja. Ne használja a Scala 2.11.x jelölésű tárházat.
    
      ![IntelliJ Scala projekttulajdonságok létrehozása](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Válassza a **Finish** (Befejezés) elemet.
5. Ha a **Project** nézet még nincs megnyitva, az **Alt+1** billentyűkombinációval nyissa meg.
6. A **Project Explorer**programban bontsa ki a projektet, és válassza az **src**lehetőséget.
7. Kattintson a jobb gombbal **az src (forrás)** elemre, mutasson az **Új**pontra, majd válassza a **Scala osztály parancsot.**
8. A **Név** mezőben adja meg a megfelelő nevet. A **Szöveg** mezőben válassza az **Objektum**lehetőséget. Ezután válassza **az OK gombot.**

    ![Új Scala osztály létrehozása párbeszédpanel](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. A .scala fájlba illessze be a következő kódot:

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

10. A **Build** menüben válassza a **Projekt létrehozása parancsot.** Győződjön meg arról, hogy a fordítás sikeresen befejeződött.


## <a name="link-to-the-hortonworks-sandbox"></a>Hivatkozás a Hortonworks sandbox

Mielőtt egy Hortonworks sandboxhoz (emulátorhoz) hivatkozna, rendelkeznie kell egy meglévő IntelliJ alkalmazással.

Csatolni egy emulátor:

1. Nyissa meg a projektet az IntelliJ-ben.
2. A **Nézet** menüben válassza az **Eszközök a Windows**menüt, majd az Azure **Explorer**lehetőséget.
3. Bontsa ki az **Azure**csomópontot, kattintson a jobb gombbal a **HDInsight**elemre, és válassza **az Emulátor csatolása parancsot.**
4. Az **Új emulátor csatolása** párbeszédpanelen adja meg a Hortonworks sandbox gyökérfiókjához beállított jelszót. Ezután írja be az alábbi képernyőképen használt értékekhez hasonló értékeket. Ezután válassza **az OK gombot.** 

   ![Új emulátor csatolása párbeszédpanel](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Az emulátor konfigurálásához válassza az **Igen**lehetőséget.

Ha az emulátor sikeresen csatlakozik, az emulátor (Hortonworks sandbox) szerepel a HDInsight-csomóponton.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Küldje el a Spark Scala alkalmazást a Hortonworks sandbox

Miután összekapcsolta az IntelliJ IDEA-t az emulátorral, beküldheti a projektet.

Projekt emulátornak való beküldése:

1. A **Project Explorerben**kattintson a jobb gombbal a projektre, majd válassza **a Spark-alkalmazás küldése a HDInsightba parancsot.**
2. Hajtsa végre a következő lépéseket:

    1. A **Spark-fürt (csak Linux)** legördülő listában válassza ki a helyi Hortonworks sandbox.
    2. A **Főosztály neve** mezőben jelölje ki vagy írja be a főosztály nevét. Ebben a cikkben a neve **GroupByTest**.

3. Válassza a **Küldés** lehetőséget. A feladatbeküldéses naplók a Spark beküldési eszközablakában jelennek meg.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozhat létre Apache Spark-alkalmazásokat egy HDInsight Spark Linux-fürthöz az Azure Toolkit for IntelliJ HDInsight-eszközök használatával.](../spark/apache-spark-intellij-tool-plugin.md)

- Az IntelliJ HDInsight-eszközeiről a HdInsight-eszközök az Apache Spark fejlesztéséhez ( Bevezetés az [IntelliJ-hez eszközkészlet bemutatkozása ) témakörben található.](https://www.youtube.com/watch?v=YTZzYVgut6c)

- Ismerje meg, hogyan [távolról debug Apache Spark alkalmazások egy HDInsight-fürt az Azure Toolkit for IntelliJ keresztül SSH.](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)

- Ismerje meg, hogyan [használhatja a HDInsight-eszközöket az Azure IntelliJ eszközkészletében az Apache Spark-alkalmazások távoli hibakereséséhez egy HDInsight Spark Linux-fürtön.](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

- Ismerje meg, hogyan [hozhat létre Apache Spark-alkalmazásokat az Azure Toolkit for Eclipse HDInsight-eszközeivel.](../spark/apache-spark-eclipse-tool-plugin.md)

