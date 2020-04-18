---
title: 'Oktatóanyag: Scala Maven alkalmazás a Spark & IntelliJ - Azure HDInsight'
description: Oktatóanyag - Hozzon létre egy Scala-ban írt Spark-alkalmazást az Apache Maven-nel, mint buildrendszerrel. És egy meglévő Maven archetípus a Scala számára, amelyet az IntelliJ IDEA biztosít.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 04/17/2020
ms.openlocfilehash: bc6c1049fc3a8f4f4d54e405f61801c916ecab61
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640594"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Oktatóanyag: Hozzon létre egy Scala Maven alkalmazást az Apache Spark hoz a HDInsightban az IntelliJ használatával

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy Scala-ban írt Apache Spark-alkalmazást az Apache Maven és az IntelliJ IDEA használatával. A cikk az Apache Maven-t használja buildrendszerként. És kezdődik egy meglévő Maven archetípus Scala által nyújtott IntelliJ IDEA.  A Scala-alkalmazás IntelliJ IDEA-ban történő létrehozása az alábbi lépésekből áll:

* Buildelési rendszerként a Mavent használja.
* A Spark-modul függőségeinek feloldásához frissítse a Project Object Model- (POM-) fájlt.
* Írja meg az alkalmazást a Scalában.
* Hozzon létre egy olyan JAR-fájlt, amit el lehet küldeni a HDInsight Spark-fürtöknek.
* Futtassa az alkalmazást a Sparkon a Livy használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Scala beépülő modul az IntelliJ IDEA-hoz
> * Az IntelliJ használata Scala Maven-alkalmazások fejlesztéséhez
> * Önálló Scala-projekt létrehozása

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* [Oracle Java Fejlesztési készlet](https://www.azul.com/downloads/azure-only/zulu/).  Ez az oktatóanyag a Java 8.0.202-es verzióját használja.

* Java IDE. Ez a cikk használ [IntelliJ IDEA Közösségi ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Lásd: [Az Azure Toolkit telepítése az IntelliJ-hez című témakört.](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in)

## <a name="install-scala-plugin-for-intellij-idea"></a>Scala beépülő modul az IntelliJ IDEA-hoz

A Scala bővítmény telepítéséhez tegye a következő lépéseket:

1. Nyissa meg az IntelliJ IDEA-t.

2. Az üdvözlőképernyőn válassza a Bővítmények **konfigurálása** > **Plugins** lehetőséget a Bővítmények ablak **megnyitásához.**

    !["IntelliJ IDEA engedélyezése scala plugin"](./media/apache-spark-create-standalone-application/enable-scala-plugin1.png)

3. Válassza a **Telepítés** lehetőséget az új ablakban szereplő Scala bővítményhez.  

    !["IntelliJ IDEA telepíteni scala plugin"](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. A beépülő modul sikeres telepítését követően újra kell indítania az IDE-t.

## <a name="use-intellij-to-create-application"></a>Alkalmazás létrehozása az IntelliJ használatával

1. Indítsa el az IntelliJ IDEA programot, és válassza az **Új projekt létrehozása lehetőséget** az Új **projekt** ablak megnyitásához.

2. Válassza az **Apache Spark/HDInsight** lehetőséget a bal oldali ablaktáblában.

3. Válassza ki a **Spark Project (Scala)** a főablakban.

4. A **Build eszköz** legördülő listájában válasszon az alábbi értékek közül:
      * **Maven** a Scala projektkészítő varázsló támogatásához.
      * **SBT** a függőségek kezeléséhez és a Scala projekt létrehozásához.

   ![IntelliJ Az Új projekt párbeszédpanel](./media/apache-spark-create-standalone-application/intellij-project-apache-spark.png)

5. Kattintson a **Tovább** gombra.

6. Az **Új projekt** ablakban adja meg a következő információkat:  

  	|  Tulajdonság   | Leírás   |  
  	| ----- | ----- |  
  	|Projektnév| Írjon be egy nevet.|  
  	|Projekt&nbsp;helye| Adja meg a projekt mentési helyét.|
  	|Projekt SDK| Ez a mező üres lesz az IDEA első használatakor.  Válassza az **Új lehetőséget...** és keresse meg a JDK-t.|
  	|Spark-verzió|A létrehozási varázsló integrálja a Spark SDK és a Scala SDK megfelelő verzióját. Ha a Spark-fürt verziója 2.0-nál korábbi, válassza a **Spark 1.x** lehetőséget. Máskülönben válassza a **Spark2.x** lehetőséget. Ez a példa **a Spark 2.3.0 (Scala 2.11.8) .**|

    ![IntelliJ IDEA A Spark SDK kiválasztása](./media/apache-spark-create-standalone-application/hdi-scala-new-project.png)

7. Válassza a **Finish** (Befejezés) elemet.

## <a name="create-a-standalone-scala-project"></a>Önálló Scala-projekt létrehozása

1. Indítsa el az IntelliJ IDEA programot, és válassza az **Új projekt létrehozása lehetőséget** az Új **projekt** ablak megnyitásához.

2. Válassza a **Maven** elemet a bal oldali ablaktáblából.

3. Adjon meg egy **Projekt SDK**-t. Ha üres, válassza az **Új lehetőséget,** és keresse meg a Java telepítési könyvtárat.

4. Jelölje be a **Létrehozás archetípusból** jelölőnégyzetet.  

5. Az archetípusok listájában **`org.scala-tools.archetypes:scala-archetype-simple`** válassza a lehetőséget. Ez az archetípus létrehozza a megfelelő könyvtárstruktúrát, és letölti a Scala program írásához szükséges alapértelmezett függőségeket.

    !["IntelliJ IDEA létre Maven projekt"](./media/apache-spark-create-standalone-application/intellij-project-create-maven.png)

6. Kattintson a **Tovább** gombra.

7. A **műtermék koordinátáinak kibontása.** Adja meg a **GroupId**és az **ArtifactId megfelelő értékeit.** **A név**és **a hely** automatikus feltöltésre kerül. Ebben az oktatóanyagban a következő értékeket használjuk:

    - **GroupId:** com.microsoft.spark.example
    - **ArtifactId:** SparkSimpleApp

    !["IntelliJ IDEA létre Maven projekt"](./media/apache-spark-create-standalone-application/intellij-artifact-coordinates.png)

8. Kattintson a **Tovább** gombra.

9. Ellenőrizze a beállításokat, majd válassza a **Next** (Tovább) lehetőséget.

10. Ellenőrizze a projekt nevét és helyét, majd válassza a **Finish** (Befejezés) lehetőséget.  A projekt importálása néhány percet vesz igénybe.

11. Miután a projekt importált, a bal oldali ablaktáblából keresse meg a **SparkSimpleApp** > **src** > **teszt** > **scala** > **com** > **microsoft** > **spark** > **példa**.  Kattintson a jobb gombbal a **MySpec**elemre, majd válassza **a Törlés parancsot. .**. Nincs szüksége erre a fájlra az alkalmazáshoz.  A párbeszédpanelen válassza az **OK** gombot.
  
12. A későbbi lépésekben frissítse a **pom.xml** a Spark Scala alkalmazás függőségeinek meghatározásához. Ahhoz, hogy ezek a függőségek automatikusan letöltődjenek és feloldódjanak, konfigurálnia kell a Maven-t.

13. A **Fájl** **menüBeállítások parancsára** kattintson a **Beállítások** ablak megnyitásához.

14. A **Beállítások** ablakban keresse meg a **Build, Execution, Deployment** > **Build Tools** > **Maven** > **Importing parancsot.**

15. Jelölje be a **Maven-projektek automatikus importálása** jelölőnégyzetet.

16. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.  Ezután visszakerül a projektablakba.

    ![A Maven konfigurálása automatikus letöltésekhez](./media/apache-spark-create-standalone-application/configure-maven-download.png)

17. A bal oldali ablaktáblában keresse meg az **scala** > **fő** > **scala** > **com.microsoft.spark.example**, majd kattintson duplán **App** megnyitásához App.scala.

18. Cserélje le a meglévő mintakódot a következő kódra, majd mentse a módosításokat. Ez a kód beolvassa az adatokat a HVAC.csv (elérhető az összes HDInsight Spark-fürtök). Azokat a sorokat olvassa be, amelyek nek csak egy számjegye van a hatodik oszlopban. És írja a kimenetet a **/HVACOut** a fürt alapértelmezett tárolótárolója alatt.

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

22. Hozza létre a .jar kiterjesztésű fájlt. Az IntelliJ IDEA lehetővé teszi a JAR-fájlok projektösszetevőként való létrehozását. Tegye a következő lépéseket.

    1. A **Fájl** menüben válassza a **Projektszerkezet...**.

    2. A **Projektszerkezet ablakban** keresse meg a **Plusz** > **szimbólum ot +** > **JAR** > a**függőségekkel rendelkező modulokból lehetőséget...**.

        !["IntelliJ IDEA projekt szerkezete add jar"](./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png)

    3. A **JAR létrehozása modulokból** ablakban jelölje ki a mappa ikonját a **Főosztály** szövegmezőben.

    4. A **Főosztály kiválasztása** ablakban jelölje ki az alapértelmezés szerint megjelenő osztályt, majd kattintson az **OK gombra.**

        !["IntelliJ IDEA projektstruktúra kiválasztása osztály"](./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png)

    5. A **Jar létrehozása modulokból** ablakban győződjön meg arról, **hogy a cél JAR-érték kinyerése** be van jelölve, majd válassza az **OK**gombot.  Ez a beállítás egyetlen JAR-fájlt hoz létre az összes függőséggel együtt.

        ![IntelliJ IDEA projekt szerkezet jar modulból](./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png)

    6. A **Kimeneti elrendezés** lap felsorolja a Maven-projekt részét képező összes edényt. Itt kijelölheti és törölheti azokat, amelyek nincsenek közvetlen függőségi viszonyban a Scala-alkalmazással. Az alkalmazás hoz létre itt, akkor távolítsa el az összes, de az utolsó **(SparkSimpleApp fordításkimenet**). Jelölje ki a törölni kívánt üvegeket, majd jelölje ki a negatív szimbólumot **-**.

        !["IntelliJ IDEA projektstruktúra törlése kimenet"](./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png)

        Győződjön meg arról, hogy a **Felvétel a projektbuildbe** jelölőnégyzet be van jelölve. Ez a beállítás biztosítja, hogy a jar jön létre minden alkalommal, amikor a projekt épül vagy frissül. Válassza **az Alkalmaz,** majd **az OK gombot.**

    7. Az edény létrehozásához keresse meg a Build Build Artifacts Build **(Build Artifacts** > Build build) (Build Artifacts Build build) (Build Artifacts Build build) **(Build** > Artifacts**Build Build) (** A projekt körülbelül 30 másodperc múlva összeállít.  A kimeneti JAR-fájl az **\out\artifacts** mappában jön létre.

        ![IntelliJ IDEA projekt műtárgy kimenet](./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Az alkalmazás futtatása az Apache Spark-fürtön

Az alkalmazás fürtön történő futtatásához a következő módszereket használhatja:

* **Másolja az alkalmazásjar a** fürthöz társított Azure Storage blob. Ehhez az **AzCopy** parancssori segédprogramot használhatja. Az adatok feltöltésére számos egyéb kliens is rendelkezésre áll. Ezekről többet a [HDInsight-ban található Apache Hadoop-feladatok adatainak feltöltése területen](../hdinsight-upload-data.md)talál.

* **Az Apache Livy használatával távolról küldhet el egy alkalmazásfeladatot** a Spark-fürtbe. A HDInsight-alapú Spark-fürtök tartalmazzák a Livyt, amely elvégzi a REST-végpontok közzétételét, így lehetővé teszi a Spark-feladatok távoli elküldését. További információ: [Küldés Apache Spark-feladatok távolról az Apache Livy a Spark-fürtök hdinsight.](apache-spark-livy-rest-interface.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje a következő lépésekkel létrehozott fürtöt:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A felső **keresőmezőbe** írja be a **HDInsight kifejezést.**

1. Válassza a **HDInsight-fürtök et a** **Szolgáltatások csoportban.**

1. A megjelenő HDInsight-fürtök listájában válassza ki az oktatóanyaghoz létrehozott fürt melletti **...** elemet.

1. Válassza a **Törlés** elemet. Válassza az **Igen** lehetőséget.

!["HDInsight azure portal delete cluster"](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

## <a name="next-step"></a>Következő lépés

Ebben a cikkben megtanulta, hogyan hozhat létre egy Apache Spark scala alkalmazást. Folytassa a következő cikkel, amelyben megtudhatja, hogyan futtathatja ezt az alkalmazást egy HDInsight Spark-fürtön, a Livy használatával.

> [!div class="nextstepaction"]
>[Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](./apache-spark-livy-rest-interface.md)
