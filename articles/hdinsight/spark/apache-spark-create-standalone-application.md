---
title: 'Oktatóanyag: Az IntelliJ-vel az Azure HDInsight Spark a Scala Maven-alkalmazás létrehozása'
description: Hozzon létre egy Scalában írt Spark-alkalmazást az Apache Maven buildelési rendszer használatával, valamint egy meglévő Maven archetype-ot a Scala számára az IntelliJ IDEA használatával.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 01/30/2019
ms.openlocfilehash: a969c026d702c423bee4871651c8b4fa26b3d37a
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700944"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Oktatóanyag: A Scala Maven-alkalmazást létrehozni az Apache Spark on HDInsight az IntelliJ-vel

Ebben az oktatóanyagban elsajátíthatja, hogyan hozhat létre egy [Apache Spark](https://spark.apache.org/) írt alkalmazás [Scala](https://www.scala-lang.org/) használatával [Apache Maven](https://maven.apache.org/) integráltuk az IntelliJ IDEA. Ez a cikk az Apache Maven buildelési rendszert használja, és egy, a Scala számára írt, az IntelliJ IDEA által biztosított, meglévő Maven archetype-ot használ kiindulási pontként.  A Scala-alkalmazás IntelliJ IDEA-ban történő létrehozása az alábbi lépésekből áll:

* Buildelési rendszerként a Mavent használja.
* A Spark-modul függőségeinek feloldásához frissítse a Project Object Model- (POM-) fájlt.
* Írja meg az alkalmazást a Scalában.
* Hozzon létre egy olyan JAR-fájlt, amit el lehet küldeni a HDInsight Spark-fürtöknek.
* Futtassa az alkalmazást a Sparkon a Livy használatával.

> [!NOTE]  
> Az alkalmazások létrehozásának és azok Linux rendszerű HDInsight Spark-fürtöknek való elküldésének megkönnyítésére a HDInsight egy IntelliJ IDEA beépülőmodul-eszközt is biztosít. További információkért lásd: [használata HDInsight-eszközei beépülő moduljának létrehozása és elküldése az Apache Spark-alkalmazások az IntelliJ IDEA](apache-spark-intellij-tool-plugin.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az IntelliJ használata Scala Maven-alkalmazások fejlesztéséhez

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* [Oracle Java Development Kitet](https://www.azul.com/downloads/azure-only/zulu/).  Ez az oktatóanyag Java 8.0.202 verziót használja.
* Java IDE. Ez a cikk [IntelliJ IDEA közösségi ver.  2018.3.4](https://www.jetbrains.com/idea/download/).
* IntelliJ-hez készült Azure eszközkészlet.  Lásd: [IntelliJ-hez készült Azure-eszközkészlet telepítése](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

## <a name="install-scala-plugin-for-intellij-idea"></a>Scala beépülő modul az IntelliJ IDEA-hoz
Hajtsa végre a Scala beépülő modul telepítéséhez az alábbi lépéseket:

1. Nyissa meg az IntelliJ IDEA-t.

2. Lépjen az üdvözlőképernyőn **konfigurálása** > **beépülő modulok** megnyitásához a **beépülő modulok** ablak.
   
    ![A Scala beépülő modul engedélyezése](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)

3. Válassza ki **telepítése** Scala beépülő modul, amely megjelent az új ablakban.  
 
    ![A Scala beépülő modul telepítése](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. A beépülő modul sikeres telepítését követően újra kell indítania az IDE-t.


## <a name="use-intellij-to-create-application"></a>Alkalmazás létrehozása az IntelliJ használatával

1. Indítsa el az IntelliJ IDEA, és válassza ki **új projekt létrehozása** megnyitásához a **új projekt** ablak.

2. Válassza ki **Azure Spark és HDInsight** a bal oldali ablaktáblán.

3. Válassza ki **Spark Project (Scala)** a főablakból.

4. Az a **buildelőeszköze** legördülő listában válasszon a következők:
      * **Maven** Scala project-létrehozási varázsló támogatás.
      * **SBT** kezeléséhez a függőségeket, és a Scala-projekt létrehozásához.

   ![A New project (Új projekt) párbeszédablak.](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

5. Kattintson a **Tovább** gombra.

6. Az a **új projekt** ablakban adja meg a következő információkat:  

  	|  Tulajdonság   | Leírás   |  
  	| ----- | ----- |  
  	|Projektnév| Adjon meg egy nevet.|  
  	|Projekt&nbsp;helye| Adja meg a kívánt helyre szeretné menteni a projektet.|
  	|Project SDK| Ez az első használatkor hasznos üres lesz.  Válassza ki **új...**  , és keresse meg a JDK.|
  	|Spark-verzió|A létrehozása varázsló együttműködik a megfelelő verziójú Spark SDK és a Scala SDK-val. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa **Spark 2.3.0-át (Scala 2.11.8)**.|

    ![A Spark SDK kiválasztása](./media/apache-spark-create-standalone-application/hdi-new-project.png)

7. Válassza a **Finish** (Befejezés) elemet.

## <a name="create-a-standalone-scala-project"></a>Önálló Scala-projekt létrehozása

1. Indítsa el az IntelliJ IDEA, és válassza ki **új projekt létrehozása** megnyitásához a **új projekt** ablak.

2. Válassza ki **Maven** a bal oldali ablaktáblán.

3. Adjon meg egy **Projekt SDK**-t. Ha üres, válassza ki a **új...**  , és keresse meg a Java-telepítési könyvtárára.

4. Válassza ki a **archetype létrehozása** jelölőnégyzetet.  

5. Az archetype-ok listájából válassza ki a következőt: **org.scala-tools.archetypes:scala-archetype-simple**. Ez archetype a megfelelő könyvtárstruktúrát hozza létre, és letölti a szükséges alapértelmezett függőségek Scala program írása.

    ![Maven-projekt létrehozása](./media/apache-spark-create-standalone-application/create-maven-project.png)

6. Kattintson a **Tovább** gombra.

7. Adja meg a **GroupId** (Csoportazonosító), az **ArtifactId** (Összetevő-azonosító) és a **Version** (Verzió) mezők értékeit. Ebben az oktatóanyagban a következő értékeket használjuk:

    - **GroupId:** com.microsoft.spark.example
    - **ArtifactId:** SparkSimpleApp

8. Kattintson a **Tovább** gombra.

9. Ellenőrizze a beállításokat, majd válassza a **Next** (Tovább) lehetőséget.

10. Ellenőrizze a projekt nevét és helyét, majd válassza a **Finish** (Befejezés) lehetőséget.  A projekt importálása néhány percet vesz igénybe.

11. Miután importálta a projektet, a bal oldali ablaktáblában lépjen **SparkSimpleApp** > **src** > **tesztelése**  >  **scala** > **com** > **microsoft** > **spark**  >  **példa**.  Kattintson a jobb gombbal **MySpec**, majd válassza ki **törlése...** . Nincs szüksége erre a fájlra az alkalmazáshoz.  Válassza ki **OK** a párbeszédpanelen.
  
12. A következő lépésekben, frissítse a **pom.xml** meghatározásához a Spark Scala-alkalmazás függőségeit. A rendszer által automatikusan letöltött és feloldott függőségek esetében a Mavent ennek megfelelően kell konfigurálnia.

13. Az a **fájl** menüjében válassza **beállítások** megnyitásához a **beállítások** ablak.

14. Az a **beállítások** ablakban navigáljon a **hozhat létre, a Futtatás üzembe helyezési** > **Build Tools** > **Maven**  >  **Importálása**.

15. Válassza ki a **Import Maven-projektek automatikus** jelölőnégyzetet.

16. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.  Majd visszatér a projekt ablak.
   
    ![A Maven konfigurálása automatikus letöltésekhez](./media/apache-spark-create-standalone-application/configure-maven.png)
   

17. A bal oldali panelen lépjen **src** > **fő** > **scala** > **com.microsoft.spark.example**, majd kattintson duplán **alkalmazás** App.scala megnyitásához.

18. Cserélje le a meglévő mintakódot a következő kódra, majd mentse a módosításokat. Ez a kód a (minden HDInsight Spark-fürtön megtalálható) HVAC.csv fájlból olvassa be az adatokat, lekéri azokat a sorokat, amelyek hatodik oszlopában csak egy számjegy szerepel, a kimenetet pedig a fürt alapértelmezett Storage-tárolójának **/HVACOut** mappájába írja.

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
19. A bal oldali panelen kattintson duplán a **pom.xml** fájlra.  
   
20. A `<project>\<properties>` részben adja hozzá a következő szegmenseket:
      
          <scala.version>2.11.8</scala.version>
          <scala.compat.version>2.11.8</scala.compat.version>
          <scala.binary.version>2.11</scala.binary.version>

21. A `<project>\<dependencies>` részben adja hozzá a következő szegmenseket:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>2.3.0</version>
           </dependency>
      
    Mentse a pom.xml fájl módosításait.

22. Hozza létre a .jar kiterjesztésű fájlt. Az IntelliJ IDEA lehetővé teszi a JAR-fájlok projektösszetevőként való létrehozását. Hajtsa végre a következő lépéseket.
    
    1. Az a **fájl** menüjében válassza **Projektstruktúra...** .

    2. Az a **Projektstruktúra** ablakában navigáljon a **összetevők** > **a plusz +** > **JAR**  >  **a függőségekkel rendelkező modulok...** .
       
        ![JAR-fájl létrehozása](./media/apache-spark-create-standalone-application/create-jar-1.png)

    3. Az a **az modulok létrehozása JAR** ablakban válassza ki a mappa ikont a **Main osztály** szövegmező.

    4. Az a **Main osztály kiválasztása** ablakban válassza ki az osztály, amely alapértelmezés szerint megjelenik majd **OK**.
       
        ![JAR-fájl létrehozása](./media/apache-spark-create-standalone-application/create-jar-2.png)

    5. Az a **az modulok létrehozása JAR** ablakban győződjön meg arról a **bontsa ki a cél JAR** kiválasztott, és adja meg a beállítás akkor **OK**.  Ez a beállítás egyetlen JAR-fájlt hoz létre az összes függőséggel együtt.
       
        ![JAR-fájl létrehozása](./media/apache-spark-create-standalone-application/create-jar-3.png)

    6. A **kimeneti elrendezés** lap felsorolja az összes a JAR-fájlok kivételével, amelyek tartalmazzák a Maven-projektet. Itt kijelölheti és törölheti azokat, amelyek nincsenek közvetlen függőségi viszonyban a Scala-alkalmazással. Az itt létrehozott alkalmazás esetében az utolsó (**SparkSimpleApp compile output**) kivételével az összes fájlt törölheti. Válassza ki a JAR-fájlok kivételével törli, és jelölje ki a negatív szimbólum **-**.
       
        ![JAR-fájl létrehozása](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Ellenőrizze, hogy a **közé tartozik a project build** jelölőnégyzet be van jelölve, ami biztosítja, hogy a jar jön létre minden alkalommal, amikor a projekt létrehozott vagy frissített. Válassza ki **alkalmaz** , majd **OK**.

    7. A jar létrehozásához lépjen a **hozhat létre** > **összetevőket hozhat létre** > **hozhat létre**. A projekt lefordítása fog körülbelül 30 másodperc múlva.  A kimeneti JAR-fájl az **\out\artifacts** mappában jön létre.
       
        ![JAR-fájl létrehozása](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Futtassa az alkalmazást az Apache Spark-fürt
Az alkalmazás fürtön történő futtatásához a következő módszereket használhatja:

* **Másolja át az alkalmazás JAR-fájlját abba az Azure Storage-blobba**, amely a fürthöz van társítva. Ehhez az [**AzCopy**](../../storage/common/storage-use-azcopy.md) parancssori segédprogramot használhatja. Az adatok feltöltésére számos egyéb kliens is rendelkezésre áll. További információk a csatlakoztatásukról annak [Upload data for HDInsight az Apache Hadoop-feladatok](../hdinsight-upload-data.md).

* **Az Apache Livy használatával távolról egy alkalmazás feladat elküldése** a Spark-fürthöz. A HDInsight-alapú Spark-fürtök tartalmazzák a Livyt, amely elvégzi a REST-végpontok közzétételét, így lehetővé teszi a Spark-feladatok távoli elküldését. További információkért lásd: [távolról segítségével Apache Livy Spark-fürtök HDInsight az Apache Spark elküldése feladatok](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Következő lépés

Ebben a cikkben megtanulta, hogyan hozhat létre egy Apache Spark scala-alkalmazás. Folytassa a következő cikkel, amelyben megtudhatja, hogyan futtathatja ezt az alkalmazást egy HDInsight Spark-fürtön, a Livy használatával.

> [!div class="nextstepaction"]
>[Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](./apache-spark-livy-rest-interface.md)
