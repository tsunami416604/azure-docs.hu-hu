---
title: Zeppelin notebookok & Apache Spark cluster – Azure HDInsight
description: Részletes útmutató a Apache Spark-fürtökkel rendelkező Zeppelin notebookok Azure HDInsight való használatához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: 75811382867b93c778641ece42971018eff39949
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664613"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Apache Zeppelin notebookok használata Apache Spark-fürttel az Azure HDInsight

A HDInsight Spark-fürtök közé tartoznak az [Apache Zeppelin](https://zeppelin.apache.org/) notebookok, amelyek [Apache Spark](https://spark.apache.org/) feladatok futtatására használhatók. Ebből a cikkből megtudhatja, hogyan használhatja a Zeppelin notebookot egy HDInsight-fürtön.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* A fürtök elsődleges tárolójának URI-sémája. Ez `wasb://` az Azure Blob Storage, `abfs://` Azure Data Lake Storage Gen2 vagy `adl://` számára Azure Data Lake Storage Gen1. Ha a biztonságos átvitel engedélyezve van a Blob Storage számára, akkor az URI `wasbs://`.  További információ: [biztonságos átvitel megkövetelése az Azure Storage-ban](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Apache Zeppelin-jegyzetfüzet elindítása

1. A Spark-fürt **áttekintése**lapon válassza ki a **Zeppelin jegyzetfüzet** elemet a **fürt irányítópultján**. Adja meg a fürt rendszergazdai hitelesítő adatait.  

   > [!NOTE]  
   > A következő URL-címet a böngészőben is elérheti, ha megnyithatja a Zeppelin jegyzetfüzetet a fürthöz. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Hozzon létre új notebookot. A fejléc panelen navigáljon a **jegyzetfüzet** > **új Megjegyzés létrehozása**elemre.

    ![Új Zeppelin-jegyzetfüzet létrehozása](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Új Zeppelin-jegyzetfüzet létrehozása")

    Adja meg a jegyzetfüzet nevét, majd válassza a **Megjegyzés létrehozása**lehetőséget.

3. Győződjön meg arról, hogy a jegyzetfüzet fejléce csatlakoztatott állapotot mutat. Ezt a jobb felső sarokban található zöld pont jelöli.

    ![Zeppelin notebook állapota](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin notebook állapota")

4. Töltse be a mintaadatokat egy ideiglenes táblába. Amikor létrehoz egy Spark-fürtöt a HDInsight-ben, a rendszer átmásolja a minta adatfájlt (`hvac.csv`) a `\HdiSamples\SensorSampleData\hvac`alatt található társított Storage-fiókba.

    Illessze be a következő kódrészletet az új jegyzetfüzetben alapértelmezés szerint létrehozott üres bekezdésbe.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Nyomja le a **SHIFT + ENTER** billentyűkombinációt, vagy kattintson a bekezdés **Lejátszás** gombjára a kódrészlet futtatásához. A bekezdés jobb oldali sarkában lévő állapotnak a kész, FÜGGŐben lévő, befejezett értékűre kell mutatnia. A kimenet az azonos bekezdés alján jelenik meg. A képernyőkép a következőhöz hasonlóan néz ki:

    ![Ideiglenes tábla létrehozása nyers adatokból](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Ideiglenes tábla létrehozása nyers adatokból")

    Az egyes bekezdésekhez címet is megadhat. A bekezdés jobb oldali sarkában válassza a **Beállítások** ikont (lánckerék), majd válassza a **title (cím megjelenítése**) lehetőséget.  

    > [!NOTE]  
    > % spark2-értelmező nem támogatott a Zeppelin jegyzetfüzetekben az összes HDInsight-verzióban, és a (z)% sh tolmács nem támogatott a HDInsight 4,0-től kezdődően.

5. Most már futtathatja a Spark SQL-utasításokat a `hvac` táblán. Illessze be a következő lekérdezést egy új bekezdésbe. A lekérdezés lekérdezi az építési azonosítót, valamint a cél és a tényleges hőmérséklet közötti különbséget egy adott dátumon. Nyomja le a **SHIFT + ENTER**billentyűkombinációt.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    Az elején található **% SQL** -utasítás azt jelzi, hogy a jegyzetfüzet a Livy Scala tolmácsot használja.

6. Válassza a **oszlopdiagram** ikont a Megjelenítés módosításához.  a **menüsáv**kiválasztása után megjelenő **Beállítások**lehetővé teszik a **kulcsok**és **értékek**kiválasztását.  Az alábbi képernyőfelvételen a kimenet látható.

    ![Spark SQL-utasítás futtatása a notebook1 használatával](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Spark SQL-utasítás futtatása a notebook1 használatával")

7. A Spark SQL-utasításokat a lekérdezésben szereplő változók használatával is futtathatja. A következő kódrészletből megtudhatja, hogyan definiálhat egy változót a lekérdezésben a lekérdezésben szereplő lehetséges értékekkel `Temp`. A lekérdezés első futtatásakor a legördülő menü automatikusan kitöltődik a változóhoz megadott értékekkel.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Illessze be ezt a kódrészletet egy új bekezdésbe, majd nyomja le a **SHIFT + ENTER**billentyűkombinációt. Ezután válassza a **65** elemet a **temp** legördülő listából.

8. Válassza a **oszlopdiagram** ikont a Megjelenítés módosításához.  Ezután válassza a **Beállítások** lehetőséget, és végezze el a következő módosításokat:

   * **Csoportok:**  **Targettemp**hozzáadása.  
   * **Értékek:** 1. Törlés **dátuma**.  2. **Temp_diff**hozzáadása.  3.  Módosítsa a gyűjtőt az **összegből** az **AVG**értékre.  

     Az alábbi képernyőfelvételen a kimenet látható.

     ![Spark SQL-utasítás futtatása a notebook2 használatával](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Spark SQL-utasítás futtatása a notebook2 használatával")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>A Hogyan külső csomagokat használ a jegyzetfüzettel?

A HDInsight Apache Spark-fürtben lévő Zeppelin notebookot úgy is konfigurálhatja, hogy olyan külső, Közösség által biztosított csomagokat használjon, amelyek nem szerepelnek a fürtben. A [Maven-tárházban](https://search.maven.org/) megkeresheti a rendelkezésre álló csomagok teljes listáját. Lekérheti az egyéb forrásokból származó elérhető csomagok listáját is. A közösségi hozzájárulású csomagok teljes listája például a [Spark-csomagokban](https://spark-packages.org/)érhető el.

Ebből a cikkből megtudhatja, hogyan használhatja a [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) csomagot a Jupyter notebook használatával.

1. Nyissa meg a tolmács beállításait. A jobb felső sarokban válassza ki a bejelentkezett felhasználó nevét, majd válassza a **tolmács**lehetőséget.

    ![Tolmács elindítása](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Struktúra kimenete")

2. Görgessen a **livy2**, majd válassza a **Szerkesztés**lehetőséget.

    ![Tolmács beállításai1 módosítása](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Tolmács beállításai1 módosítása")

3. Navigáljon a Key `livy.spark.jars.packages`elemre, és állítsa be az értékét a következő formátumban: `group:id:version`. Ha tehát a [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) csomagot szeretné használni, a kulcs értékét `com.databricks:spark-csv_2.10:1.4.0`értékre kell állítania.

    ![Tolmács settings2 módosítása](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Tolmács settings2 módosítása")

    Válassza a **Mentés** lehetőséget, majd kattintson az **OK gombra** a Livy-értelmező újraindításához.

4. Ha szeretné megismerni, hogyan lehet megérkezni a fent megadott kulcs értékeként, itt találja a következőt:.

    a. Keresse meg a csomagot a Maven-tárházban. Ehhez a cikkhez a [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)fájlt használtuk.

    b. A tárházból Gyűjtse össze a **GroupID**, a **ArtifactId**és a **verzió**értékeit.

    ![Külső csomagok használata Jupyter notebooktal](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Külső csomagok használata Jupyter notebooktal")

    c. Összefűzi a három értéket kettősponttal elválasztva ( **:** ).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Hol lesznek mentve a Zeppelin jegyzetfüzetek?

A Zeppelin jegyzetfüzetek a fürt átjárócsomópontokkal lesznek mentve. Tehát ha törli a fürtöt, a jegyzetfüzetek is törlődnek. Ha szeretné megőrizni a jegyzetfüzeteket a későbbi használat érdekében más fürtökön, exportálnia kell azokat a feladatok futtatása után. Jegyzetfüzet exportálásához válassza ki az **Exportálás** ikont az alábbi képen látható módon.

![Jegyzetfüzet letöltése](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "A jegyzetfüzet letöltése")

Ekkor a rendszer a jegyzetfüzetet JSON-fájlként menti a letöltési helyen.

## <a name="livy-session-management"></a>Livy munkamenet-kezelés

Ha a Zeppelin-jegyzetfüzet első kód bekezdését futtatja, a rendszer egy új Livy-munkamenetet hoz létre a HDInsight Spark-fürtben. Ez a munkamenet az összes később létrehozott Zeppelin-jegyzetfüzetben meg van osztva. Ha valamilyen oknál fogva a Livy-munkamenetet elveszik (a fürt újraindítása stb.), nem fog tudni feladatokat futtatni a Zeppelin jegyzetfüzetből.

Ilyen esetben az alábbi lépéseket kell elvégeznie, mielőtt elkezdené a feladatok futtatását egy Zeppelin-jegyzetfüzetből.  

1. Indítsa újra a Livy-tolmácsot a Zeppelin jegyzetfüzetből. Ehhez nyissa meg a tolmács beállításait úgy, hogy kiválasztja a bejelentkezett felhasználó nevét a jobb felső sarokban, majd kiválasztja a **tolmács**elemet.

    ![Tolmács elindítása](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Struktúra kimenete")

2. Görgessen a **livy2**, majd válassza az **Újraindítás**lehetőséget.

    ![A Livy-értelmező újraindítása](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "A Zeppelin-tolmács újraindítása")

3. Kód cellájának futtatása meglévő Zeppelin-jegyzetfüzetből. Ez létrehoz egy új Livy-munkamenetet a HDInsight-fürtben.

## <a name="seealso"></a>Lásd még:

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [A IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Apache Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Jupyter notebookokhoz elérhető kernelek Apache Spark-fürtben HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
