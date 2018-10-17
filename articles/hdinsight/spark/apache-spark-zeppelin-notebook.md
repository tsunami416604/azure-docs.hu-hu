---
title: Zeppelin notebookok használata Azure HDInsight az Apache Spark-fürt
description: Zeppelin notebookok használata Azure HDInsight az Apache Spark-fürtök lépésenkénti útmutatót.
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.openlocfilehash: 8e32939f3e253bfdd6f8d989f616f30e1b9f27eb
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364863"
---
# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Zeppelin notebookok használata Azure HDInsight az Apache Spark-fürt

HDInsight Spark-fürtök tartalmazzák, amelyek segítségével a Spark-feladatok futtatása a Zeppelin notebookok. Ebből a cikkből elsajátíthatja a Zeppelin notebook használata egy HDInsight-fürtön.

**Előfeltételek:**

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Indítsa el a Zeppelin-jegyzetfüzet
1. A Spark-fürt panelén kattintson **fürt irányítópultja**, és kattintson a **Zeppelin-Jegyzetfüzet**. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.
   
   > [!NOTE]
   > A Zeppelin Notebook használhat saját fürtjében, a böngészőben a következő URL-cím megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`
   > 
   > 
1. Hozzon létre új notebookot. Kattintson a fejléc ablak **Notebook**, és kattintson a **új megjegyzés létrehozása**.
   
    ![Hozzon létre egy új Zeppelin-Jegyzetfüzet](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "új Zeppelin notebook létrehozása")
   
    Adja meg a notebook elnevezése, és kattintson a **Megjegyzés létrehozása**.
1. Ügyeljen arra, hogy a notebook fejléc egy csatlakoztatott állapotát jeleníti meg. A jobb felső sarokban lévő zöld pontot helyén.
   
    ![Zeppelin notebook állapot](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin notebook állapota")
1. Töltse be a mintaadatokat egy ideiglenes táblába. Spark-fürt létrehozásakor, a HDInsight, a mintaadatfájl **hvac.csv**, másolja a társított storage-fiók alatt **\HdiSamples\SensorSampleData\hvac**.
   
    Az üres bekezdés, amely az Új jegyzetfüzet alapértelmezés szerint létrejön illessze be az alábbi kódrészletet.
   
        %livy2.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter
   
        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
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
   
    Nyomja meg **SHIFT + ENTER** , vagy kattintson a **lejátszása** a bekezdés a kódrészlet futtatása gombra. A bekezdés a jobb felső sarkának állapota Kész, függő, BEFEJEZETT, FUTÓ, érdemes halad. A kimenetben megjelenik-e stejném paragraph alján. A képernyőfelvételen a következőhöz hasonlóan néz ki:
   
    ![Hozzon létre egy ideiglenes táblát a nyers adatoktól](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "hozzon létre egy ideiglenes táblát nyers adatokból")
   
    Minden bekezdéséhez címet is megadhatja. A jobb oldali sarokban, kattintson a **beállítások** ikonra, majd kattintson **Zobrazit název**.

> [!NOTE]
> a(z) % spark2 interpret nem támogatott a Zeppelin-jegyzetfüzetek minden HDInsight-verziók és % sh interpret nem támogatja a HDInsight 4.0 és újabb verziók esetében.
>

1. Spark SQL-utasítások mostantól futtathatja a **hvac** tábla. Ha új bekezdést illessze be a következő lekérdezést. A lekérdezés lekéri a az épület-Azonosítót és a különbség a cél és minden egyes létrehozásához egy adott időpontban aktuális hőmérséklet. Nyomja meg **SHIFT + ENTER**.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    A **% sql** elején utasítás arra utasítja a notebook használatához a Livy Scala értelmezője számára készült.
   
    Az alábbi képernyőfelvételen a kimenetet.
   
    ![A notebook használatával Spark SQL-utasítás futtatásával](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "a notebook használatával Spark SQL-utasítás futtatása")
   
     Az azonos kimenethez tartozó különböző reprezentációinak közötti váltáshoz kattintson (téglalap színnel) megjelenítési beállításait. Kattintson a **beállítások** kiválasztása milyen consitutes a kulcs és az értékeket a kimenetben. Használja a fenti képernyőfelvételen **buildingID** a kulcs és az átlagosan **temp_diff** értékeként.
1. Változók használata a lekérdezés Spark SQL-utasítások is futtathatja. A következő kódrészletet bemutatja, hogyan adhat meg egy változót **Temp**, a lehetséges értékek a lekérdezni kívánt lekérdezés. A lekérdezés első futtatásakor automatikusan megjelenik egy legördülő változóhoz megadott értékekkel.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    Illessze be a kódrészletet egy új bekezdés, és nyomja le az **SHIFT + ENTER**. Az alábbi képernyőfelvételen a kimenetet.
   
    ![A notebook használatával Spark SQL-utasítás futtatásával](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "a notebook használatával Spark SQL-utasítás futtatása")
   
    Az ezt követő lekérdezéseket válasszon ki egy új értéket a legördülő listából, és futtassa újra a lekérdezést. Kattintson a **beállítások** kiválasztása milyen consitutes a kulcs és az értékeket a kimenetben. Használja a fenti képernyőfelvételen **buildingID** átlagát kulcsként **temp_diff** értéket, és **targettemp** csoportként.
1. Indítsa újra a Livy értelmezője számára készült kilép belőle. Ehhez nyissa meg a interpret beállítások elemre kattintva a bejelentkezett felhasználó neve, a jobb felső sarokban, és kattintson a **Interpret**.
   
    ![Indítsa el a interpret](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-kimenet")
1. Görgessen a Livy interpret beállításait, és kattintson a **indítsa újra a**.
   
    ![Indítsa újra a Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "indítsa újra a Zeppelin intepreter")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Hogyan külső csomagok használata a notebook?
A Zeppelin notebook konfigurálhatja az Apache Spark-fürt a HDInsight (Linux), amelyek nem tartalmaz-a-beépített a fürt külső, a Közösség által biztosított csomagok használata. Kereshet a [Maven tárházból](http://search.maven.org/) a teljes listát az elérhető csomagokat. Elérhető csomagok listáját a más forrásokból is beszerezheti. Például Közösség által biztosított csomagok teljes listája megtalálható [Spark csomagok](http://spark-packages.org/).

Ebben a cikkben bemutatjuk, hogyan használata a [spark-fürt megosztott kötetei szolgáltatás](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) a Jupyter notebookkal csomagot.

1. Interpret beállításainak megnyitásához. A jobb felső sarokban, kattintson a bejelentkezett felhasználó nevében, és kattintson **Interpret**.
   
    ![Indítsa el a interpret](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-kimenet")
1. Görgessen a Livy interpret beállításait, és kattintson a **szerkesztése**.
   
    ![Interpret beállításainak módosítása](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "interpret beállításainak módosítása")
1. Adjon hozzá egy új kulcsot nevű **livy.spark.jars.packages** majd az értékét állítsa a következő formátumban `group:id:version`. Ha használni szeretné a [spark-fürt megosztott kötetei szolgáltatás](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) csomag, be kell a kulcs értékét `com.databricks:spark-csv_2.10:1.4.0`.
   
    ![Interpret beállításainak módosítása](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "interpret beállításainak módosítása")
   
    Kattintson a **mentése** , majd indítsa újra a Livy értelmezője számára készült.
1. **Tipp**: Ha szeretné megtudni, hogyan kell érkeznie a kulcsnak az értéke a fent megadott, a következő módját.
   
    a. Keresse meg a csomagot a Maven tárházból. Ebben az oktatóanyagban használt [spark-fürt megosztott kötetei szolgáltatás](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Gyűjtse össze a tárházban, a tartozó értékeket **GroupId**, **ArtifactId**, és **verzió**.
   
    ![Külső csomagok használata Jupyter notebook](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "külső csomagok használata Jupyter notebook")
   
    c. Fűz össze adatokat a három, egymástól kettősponttal (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>A Zeppelin notebookok tároló?
A Zeppelin notebookok kerülnek a fürt átjárócsomópontjaihoz. Így ha törli a fürtöt, a notebookok is törlődik. Ha meg szeretné őrizni a notebookok más fürtökön későbbi használatra, exportálnia kell őket a feladatok futtatását követően. A notebook exportálásához kattintson a **exportálása** ikonra az alábbi képen látható módon.

![Töltse le a notebookot](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "töltse le a notebookot")

Ez a jegyzetfüzet egy JSON-fájlt a letöltési helyre menti.

## <a name="livy-session-management"></a>Livy munkamenet-kezelés
A Zeppelin-jegyzetfüzet futtatásakor a kód első bekezdésben új Livy-munkamenetet a HDInsight Spark-fürt jön létre. Ez a munkamenet közösen használja az összes Zeppelin-jegyzetfüzetek ezt követően hozzon létre. Ha valamilyen okból a Livy-munkamenet le állni a (fürt újraindítás, stb.), nem lesz képes feladatok futtatása a Zeppelin-jegyzetfüzet.

Ebben az esetben a következő lépéseket kell elvégeznie, feladatok futtatása a Zeppelin-jegyzetfüzet megkezdése előtt. 

1. Indítsa újra a Zeppelin-jegyzetfüzet a Livy értelmezője számára készült. Ehhez nyissa meg a interpret beállítások elemre kattintva a bejelentkezett felhasználó neve, a jobb felső sarokban, és kattintson a **Interpret**.
   
    ![Indítsa el a interpret](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-kimenet")
1. Görgessen a Livy interpret beállításait, és kattintson a **indítsa újra a**.
   
    ![Indítsa újra a Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "indítsa újra a Zeppelin intepreter")
1. A kódcella futtatásához a meglévő Zeppelin-jegyzetfüzeteket. Ez létrehoz egy új Livy-munkamenetet a HDInsight-fürt.

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 







