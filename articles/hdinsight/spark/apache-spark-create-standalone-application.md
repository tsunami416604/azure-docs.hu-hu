---
title: "A Spark-fürtök - Azure HDInsight futtathatnak Scala-alkalmazás létrehozása |} Microsoft Docs"
description: "Az Apache Maven scalában írt a buildelési rendszer és egy meglévő Maven archetype az IntelliJ IDEA által biztosított Scala Spark-alkalmazás létrehozása."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: nitinme
ms.openlocfilehash: bf1b127c4a8e05517d7bd877b463a04bcc0990a0
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="create-a-scala-maven-application-to-run-on-apache-spark-cluster-on-hdinsight"></a>Az Apache Spark-fürttel hdinsighton futó Scala Maven-alkalmazás létrehozása

Megtudhatja, hogyan hozhat létre az IntelliJ IDEA Maven használatával scalában írt Spark alkalmazást. A cikk Apache Maven build rendszert használ, és az IntelliJ IDEA által biztosított Scala egy meglévő Maven archetype kezdődik.  Az IntelliJ IDEA a Scala-alkalmazások létrehozása a következő lépésekből áll:

* Maven használata a buildelési rendszer.
* Frissítse a projekt Object Model (POM) fájlját, hogy a modul Spark-függőségek feloldása.
* Írja be az alkalmazás Scala.
* Egy HDInsight Spark-fürtjei küldheti el jar-fájlt létrehozni.
* Futtassa az alkalmazást a Spark-fürt Livy használatával.

> [!NOTE]
> A HDInsight emellett az IntelliJ IDEA beépülő modul eszköz létrehozása és alkalmazások egy HDInsight Spark-fürt Linux rendszeren való elküldése folyamatának megkönnyítése érdekében. További információkért lásd: [használata a HDInsight-eszközei beépülő IntelliJ Idea létrehozásához és elküldéséhez a Spark-alkalmazások](apache-spark-intellij-tool-plugin.md).
> 
> 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).
* Oracle Java fejlesztői készlet. A későbbiekben telepítheti az [Itt](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* A Java IDE. Ebben a cikkben az IntelliJ IDEA 15.0.1. A későbbiekben telepítheti az [Itt](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Az IntelliJ Idea Scala beépülő modul telepítése
IntelliJ IDEA telepítési volt nem kéri a felhasználót a Scala beépülő modul engedélyezése, ha indítsa el az IntelliJ IDEA, és hajtsa végre a következő lépésekkel telepíti a beépülő modul:

1. Indítsa el az IntelliJ IDEA és az üdvözlőképernyőn kattintson **konfigurálása** majd **beépülő modulok**.
   
    ![Scala beépülő modul engedélyezése](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
2. A következő képernyőn kattintson **JetBrains telepítése beépülő modul** a bal alsó sarkában. Az a **Tallózás JetBrains beépülő modulok** párbeszédpanel megnyílik, keresse meg a Scala, és kattintson **telepítése**.
   
    ![Scala beépülő modul telepítése](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
3. A beépülő modul sikeres telepítése után kattintson a **indítsa újra az IntelliJ IDEA gomb** az IDE újraindítására.

## <a name="create-a-standalone-scala-project"></a>Önálló Scala-projekt létrehozása
1. Indítsa el az IntelliJ IDEA, és hozzon létre egy új projektet. Az új projekt párbeszédpanel a következők közül választhat, és kattintson a **következő**.
   
    ![Maven-projekt létrehozása](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Válassza ki **Maven** a projekt típusként.
   * Adjon meg egy **SDK projekt**. Az új gombra, és keresse meg a Java telepítési könyvtárát, általában `C:\Program Files\Java\jdk1.8.0_66`.
   * Válassza ki a **létrehozása a archetype** lehetőséget.
   * Archetypes listában jelölje ki **org.scala tools.archetypes:scala-archetype-egyszerű**. Ez hozza létre a megfelelő könyvtárstruktúrát, és töltse le a szükséges alapértelmezett függőségek Scala program írni.
2. Adja meg a megfelelő értékeket **GroupId**, **artifactid szakaszát**, és **verzió**. Kattintson a **Tovább** gombra.
3. A következő párbeszédpanelen, amelyben meg kell határoznia Maven kezdőkönyvtár és egyéb felhasználói beállításokat, fogadja el az alapértelmezett beállításokat, majd kattintson **következő**.
4. A legutóbbi párbeszédpanelen adja meg a projekt nevét és helyét, és kattintson a **Befejezés**.
5. Törölje a **MySpec.Scala** a(z) **src\test\scala\com\microsoft\spark\example**. Nem kell ezt az alkalmazáshoz.
6. Ha szükséges, nevezze át az alapértelmezett forrás- és a vizsgált fájlok. Lépjen a bal oldali ablaktáblán az IntelliJ IDEA, **src\main\scala\com.microsoft.spark.example**. Kattintson a jobb gombbal **App.scala**, kattintson a **Refactor**, kattintson a fájl. Nevezze át, és a párbeszédpanelen adja meg az új nevet az alkalmazásnak, majd **Refactor**.
   
    ![Fájl átnevezése](./media/apache-spark-create-standalone-application/rename-scala-files.png)  
7. A későbbi lépésekben akkor frissíti a pom.xml megadhatók a Spark Scala alkalmazás függőségeit. Ezeket a függőségeket letölthető, és automatikusan megoldani ennek megfelelően Maven kell konfigurálnia.
   
    ![Automatikus letöltés Maven konfigurálása](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. Az a **fájl** menüben kattintson a **beállítások**.
   2. Az a **beállítások** párbeszédpanel navigáljon a **Build, a végrehajtási, a központi telepítési** > **Build Tools** > **Maven** > **importálása**.
   3. Jelölje be a **Import Maven projektek automatikusan**.
   4. Kattintson a **alkalmaz**, és kattintson a **OK**.
8. Frissítse a Scala forrásfájl tartalmazza az alkalmazás kódjában. Nyissa meg, és cserélje le a meglévő mintakód a következő kódra, és menti a módosításokat. Ez a kód beolvassa az adatokat (az összes HDInsight Spark-fürtjei elérhető) HVAC.csv, lekéri a sor csak egy számot a hatodik oszlopban és ír a kimeneti **/HVACOut** alatt az alapértelmezett tároló, a fürt számára.
   
        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
9. Frissítse a pom.xml.
   
   1. Belül `<project>\<properties>` adja hozzá a következő:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. Belül `<project>\<dependencies>` adja hozzá a következő:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Pom.xml módosításainak mentéséhez.
10. Hozza létre a .jar fájlt. IntelliJ IDEA lehetővé teszi a JAR létrehozását, a projekt összetevő. A következő lépésekkel.
    
    1. Az a **fájl** menüben kattintson a **szerkezetének**.
    2. Az a **szerkezetének** párbeszédpanel, kattintson a **összetevők** és kattintson a plusz jelre. Az előugró párbeszédpanelen kattintson **JAR**, és kattintson a **a függőségekkel rendelkező modulok**.
       
        ![Hozzon létre JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. Az a **modulokban létrehozása JAR** párbeszédpanel párbeszédpanelen kattintson a három pont (![három pont](./media/apache-spark-create-standalone-application/ellipsis.png) ) szemben a **fő osztály**.
    4. Az a **fő osztály kiválasztása** párbeszédpanel mezőben, válassza ki az osztályt, amely alapértelmezés szerint jelenik meg, és kattintson a **OK**.
       
        ![Hozzon létre JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. Az a **modulokban létrehozása JAR** párbeszédpanelen győződjön meg arról, hogy lehetőség **bontsa ki a cél JAR** van kiválasztva, és kattintson **OK**. Ez minden függőség egyetlen JAR hoz létre.
       
        ![Hozzon létre JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. A kimeneti elrendezés lap felsorolja az összes a JAR-fájlok kivételével, amelyek tartalmazzák a Maven project a. Válassza ki, és törölheti azokat, amelyeken a Scala alkalmazásnak nincs közvetlen függőség. Itt létrehozzuk az alkalmazás is távolítja el a legutóbb (**SparkSimpleApp fordítási kimeneti**). A JAR-fájlok kivételével törli, majd válassza ki a **törlése** ikonra.
       
        ![Hozzon létre JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Győződjön meg arról, hogy **győződjön Build** be van jelölve, amely biztosítja, hogy a jar minden alkalommal létrejön a projekt beépített vagy frissíteni. Kattintson a **alkalmazása** , majd **OK**.
    7. A menüsávban kattintson **Build**, és kattintson a **ellenőrizze projekt**. Is **Build összetevők** a jar létrehozásához. A kimeneti jar alatt létrejön **\out\artifacts**.
       
        ![Hozzon létre JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Az alkalmazás futtatása Spark-fürt
Az alkalmazás futtatásához a fürtön, tegye a következőket:

* **Az alkalmazás jar másolása az Azure storage-blob** a fürthöz rendelt. Használhat [ **AzCopy**](../../storage/common/storage-use-azcopy.md), parancssori segédprogram, ehhez. Nincsenek feltölteni az adatokat használó más ügyfelek is számos. A rájuk vonatkozó további található [feltölteni az adatokat a HDInsight Hadoop-feladatok](../hdinsight-upload-data.md).
* **Egy alkalmazás feladat távolról küldhetnek a Livy használatával** a Spark-fürt számára. A HDInsight Spark-fürtök REST-végpontok távolról a Spark feladatok küldéséhez elérhetővé tévő Livy tartalmazza. További információkért lásd: [Livy távolról használata Spark-fürtök a HDInsight Spark küldje el feladatok](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Következő lépés

Ebben a cikkben megtanulta, hogyan hozhat létre egy Spark scala alkalmazást. Előzetes hogyan futtathatják az alkalmazást egy HDInsight Spark-fürt Livy használatával a következő cikket.

> [!div class="nextstepaction"]
>[Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

