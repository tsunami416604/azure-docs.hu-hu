---
title: Azure eszközkészlet használata a Hortonworks védőfal IntelliJ |} Microsoft Docs
description: Megtudhatja, hogyan használhat HDInsight eszközöket az Azure-eszközkészlet a Hortonworks védőfal az intellij-t.
keywords: hadoop-eszközök hive lekérdezés, az intellij, hortonworks védőfal, intellij azure eszköztára
services: HDInsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 59251287f7597955fe22700cc96527b9476753c1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Használja a HDInsight Tools for IntelliJ a Hortonworks védőfal

Ismerje meg, hogyan használható a HDInsight Tools for IntelliJ Apache Scala-alkalmazások fejlesztéséhez és tesztelje az alkalmazásokat a [Hortonworks védőfal](http://hortonworks.com/products/sandbox/) fut a számítógépen. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) fejlesztési számítógép szoftver a Java integrált fejlesztési környezeti (IDE) van. Miután Ön által fejlesztett és tesztelje az alkalmazásokat a Hortonworks védőfal, áthelyezheti az alkalmazásokat a [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

- Hortonworks Data Platform (HDP) 2.4 a helyi számítógépen futó Hortonworks a(z). HDP beállításához tekintse meg a [Ismerkedés a Hadoop ökoszisztémájának a virtuális gépen Hadoop védőfalat](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > A HDInsight Tools for IntelliJ csak HDP 2.4 tesztelték. Ahhoz, hogy HDP 2.4, bontsa ki a **Hortonworks védőfal archív** a a [Hortonworks védőfal tölti le a hely](http://hortonworks.com/downloads/#sandbox).

- [Java fejlesztői készlet (JDK) 1,8 vagy újabb verzió](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Az IntelliJ Azure eszköztára JDK igényel.

- [IntelliJ IDEA community edition](https://www.jetbrains.com/idea/download) rendelkező a [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) beépülő modul és a [IntelliJ Azure eszköztára](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) beépülő modult. A HDInsight Tools for IntelliJ érhető el az intellij-t Azure eszköztára részeként. 

A beépülő modulok telepítése:

  1. Nyissa meg az IntelliJ IDEA-t.
  2. Az a **üdvözlő** lapon jelölje be **konfigurálása**, majd válassza ki **beépülő modulok**.
  3. Válassza ki a bal alsó sarkában **JetBrains telepítése beépülő modul**.
  4. A keresési funkció segítségével kereshet **Scala**, majd válassza ki **telepítése**.
  5. A telepítés befejezéséhez válassza ki a **indítsa újra az IntelliJ IDEA**.
  6. Ismételje meg a 4. és 5 telepítéséhez **IntelliJ Azure eszköztára**. További információkért lásd: [Azure eszközkészlet telepítése az IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-a-spark-scala-application"></a>Spark Scala-alkalmazás létrehozása

Ebben a szakaszban IntelliJ IDEA használatával hoz létre egy minta Scala-projektet. A következő szakaszban kapcsolhat IntelliJ IDEA a Hortonworks védőfal (emulátor) a projekt mentése előtt.

1. Nyissa meg az IntelliJ IDEA a számítógépen. Az a **új projekt** párbeszédpanelen töltse ki az alábbi lépéseket:

   1. Válassza ki **HDInsight** > **a Spark on HDInsight (Scala)**.
   2. Az a **Build eszköz** listában válasszon egyet az alábbi, az adott esetben alapján:

    * **Maven**: A Scala projekt-létrehozási varázsló támogatása.
    * **SBT**: függőségek kezelése és a Scala projekt felépítéséhez.

   ![Az új projekt párbeszédpanel](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Kattintson a **Tovább** gombra.
3. A következő **új projekt** párbeszédpanelen töltse ki az alábbi lépéseket:

    1. Az a **projektnevet** mezőbe írja be a projekt nevét.
    2. Az a **projekt** mezőbe írja be a projekt helyére.
    3. Mellett a **projekt SDK** legördülő listában válassza **új**, jelölje be **JDK**, majd adja meg a mappát a Java JDK 1.7 vagy újabb verziója. Válassza ki **Java 1.8** a Spark 2.x fürthöz. Válassza ki **Java 1.7** a Spark 1.x fürthöz. Az alapértelmezett hely: C:\Program Files\Java\jdk1.8.x_xxx.
    4. Az a **Spark verzió** legördülő listából válassza ki, a Scala-projekt létrehozása varázsló integrálja a megfelelő verzióját a Spark SDK és a Scala SDK. Ha a Spark-fürt verziója korábbi, mint 2,0, válassza ki a **Spark 1.x**. Máskülönben válassza **Spark2.x**. A példa Spark 1.6.2 (Scala 2.10.5). Győződjön meg arról, hogy a megjelölt tárház használunk **Scala 2.10.x**. Ne használja a tárház Scala megjelölve 2.11.x.
    
    ![Az IntelliJ Scala projekt Tulajdonságok létrehozása](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Válassza a **Finish** (Befejezés) elemet.
5. Ha a **projekt** nézet még nincs megnyitva, nyomja le az ENTER **Alt + 1** való megnyitásához.
6. A **Project Explorer**, bontsa ki a projektet, és válassza **src**.
7. Kattintson a jobb gombbal **src**, mutasson a **új**, majd válassza ki **Scala osztály**.
8. Az a **neve** adjon meg egy nevet. Az a **jellegű** mezőben válassza **objektum**. Ezután válassza az **OK** lehetőséget.

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

10. Az a **Build** menüjében válassza **Build projekt**. Győződjön meg arról, hogy a fordítás futtatása sikeresen befejeződött.


## <a name="link-to-the-hortonworks-sandbox"></a>A Hortonworks védőfal mutató hivatkozás

Mielőtt Hortonworks védőfalat (emulátor) is kapcsolhat, egy meglévő IntelliJ alkalmazást kell rendelkeznie.

Az emulátor összekapcsolása:

1. Nyissa meg a projektet az intellij-t.
2. Az a **nézet** menüjében válassza **eszközök Windows**, majd válassza ki **Azure Explorer**.
3. Bontsa ki a **Azure**, kattintson a jobb gombbal **HDInsight**, majd válassza ki **hivatkozásra az emulátor**.
4. Az a **hivatkozásra egy új emulátor** párbeszédpanelen adja meg a jelszavát, amelyet a Hortonworks védőfal rendszergazdafiók beállította. Ezután adja meg az alábbi képernyőfelvételen a hasonló értékek. Ezután válassza az **OK** lehetőséget. 

   ![A hivatkozás egy új emulátor párbeszédpanel](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Az emulátor konfigurálásához jelölje ki **Igen**.

Ha az emulátor sikeresen csatlakozik, az emulátor (Hortonworks védőfal) megjelenik a HDInsight-csomóponton.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>A Spark Scala-kérelmet a Hortonworks védőfal felé

Miután a IntelliJ IDEA emulátorának, elküldheti a a projekthez.

A projekt egy emulátorának küldeni:

1. A **Project Explorer**, kattintson jobb gombbal a projektre, majd válassza ki **küldje el a Spark-alkalmazást, amely**.
2. Hajtsa végre a következő lépéseket:

    1. Az a **Spark-fürt (csak Linux)** legördülő listára, válassza ki a helyi Hortonworks védőfal.
    2. Az a **fő osztálynév** mezőben válassza ki vagy adja meg a fő osztály nevét. Ebben az oktatóanyagban a értéke **GroupByTest**.

3. Válassza ki **nyújt**. A feladat elküldése naplók a Spark küldésének eszköz ablakban jelennek meg.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [Azure eszköztára IntelliJ HDInsight Tools használatával Spark-alkalmazások a HDInsight Spark Linux-fürtök létrehozása](../spark/apache-spark-intellij-tool-plugin.md).

- A HDInsight Tools for IntelliJ kapcsolatos videót: [a HDInsight Tools bevezetni az IntelliJ Spark fejlesztési](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Megtudhatja, hogyan [távolról az IntelliJ SSH-n keresztül a Azure eszközkészlet a HDInsight-fürtök a Spark-alkalmazások hibakeresését](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Megtudhatja, hogyan [Azure eszköztára IntelliJ HDInsight Tools használatával távolról egy HDInsight Spark Linux fürtön Spark-alkalmazások](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Megtudhatja, hogyan [az Eclipse Spark-alkalmazások létrehozása az Azure eszközkészlet használata a HDInsight Tools](../spark/apache-spark-eclipse-tool-plugin.md).

- A HDInsight Tools for Eclipse kapcsolatos videót: [használata a HDInsight Tools for Spark-alkalmazások létrehozása az eclipse-ben](https://mix.office.com/watch/1rau2mopb6fha).
