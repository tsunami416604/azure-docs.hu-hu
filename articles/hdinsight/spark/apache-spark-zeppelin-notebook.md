---
title: "Zeppelin notebookok használata Azure HDInsight az Apache Spark-fürt |} Microsoft Docs"
description: "Részletes útmutatók Apache Spark-fürtök on Azure HDInsight Zeppelin notebookok használata."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: df489d70-7788-4efa-a089-e5e5006421e2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 9546a0d99647b0debfbac9d6a5f3e2dcf34d0fa0
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Zeppelin notebookok használata Azure HDInsight az Apache Spark-fürt

HDInsight Spark-fürtjei Zeppelin notebookok használó feladatok futtatása Spark tartalmazza. Ebből a cikkből megismerheti a Zeppelin notebook használata a HDInsight-fürtöt.

> [!NOTE]
> Zeppelin notebookok csak 1.6.3 Spark on HDInsight 3.5-ös és 2.1.0 Spark on HDInsight 3.6 érhetők el.
>

**Előfeltételek:**

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Indítsa el a Zeppelin notebook
1. A Spark-fürt panelén kattintson **fürt irányítópult**, és kattintson a **Zeppelin Notebook**. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.
   
   > [!NOTE]
   > Előfordulhat, hogy is eléri a Zeppelin Notebook a fürt a böngészőben nyissa meg a következő URL-címet. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`
   > 
   > 
2. Hozzon létre új notebookot. A fejléc ablaktáblában kattintson **Notebook**, és kattintson a **hozzon létre új megjegyzés**.
   
    ![Létrehozhat egy újat Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "egy új Zeppelin notebook létrehozása")
   
    Adja meg a notebook nevét, és kattintson **létrehozása Megjegyzés**.
3. Ellenőrizze azt is, a notebook fejléc egy csatlakoztatott állapotát jeleníti meg. A jobb felső sarokban, zöld pontot helyén.
   
    ![Zeppelin notebook állapot](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin notebook állapota")
4. Töltse be a mintaadatokat egy ideiglenes táblába. Spark-fürt hdinsightban a mintaadatfájlokat létrehozásakor **hvac.csv**, másolja a kapcsolódó tárfiók alatt **\HdiSamples\SensorSampleData\hvac**.
   
    Az üres bekezdés, amely az új notebook alapértelmezés szerint létrejön illessze be a következő kódrészletet.
   
        %livy.spark
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
   
    Nyomja le az **SHIFT + ENTER** , vagy kattintson a **lejátszása** gomb a bekezdés, a részlet futtatásához. A bekezdés jobb-sarkában állapotának a kész, függőben lévő, FUTÓ befejezett kell előrehaladás. Egyazon paragraph alján megjelenik a kimenetet. A képernyőfelvételen a következőképpen néznek:
   
    ![Hozzon létre egy ideiglenes táblából a nyers adatoktól](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "a nyers adatoktól ideiglenes tábla létrehozása")
   
    Minden bekezdéséhez címet is megadhatja. Kattintson a jobb oldali sarokban a **beállítások** ikonra, végül **cím megjelenítése a**.
5. Most futtathatja a Spark SQL-utasítások a **hvac** tábla. Illessze be a következő lekérdezést egy új bekezdésben szereplő esetekben. A lekérdezés lekéri az épület-Azonosítót és a különbség a cél és a tényleges hőmérsékletek minden készítéséhez egy adott időpontban. Nyomja le az **SHIFT + ENTER**.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    A **% sql** nyilatkozat elején közli a notebook használatához a Livy Scala értelmező.
   
    Az alábbi képernyőfelvételen látható a kimenetet.
   
    ![A notebook használata Spark SQL-utasítás futtatása](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "a notebook használata Spark SQL-utasítás futtatása")
   
     Kattintson a megjelenítési beállítások (kiemelt téglalap) az azonos kimenethez tartozó különböző felelősséget közötti váltáshoz. Kattintson a **beállítások** választhat, milyen consitutes a kulcs értékeit a kimenetben. A fenti képernyőfelvételen használ **buildingID** átlaga és a kulcs, **temp_diff** értékeként.
6. A lekérdezési változók használata Spark SQL-utasítások is futtathatja. A következő kódrészletet bemutatja, hogyan adhat meg egy változót **Temp**, a lehetséges értékek a le szeretné kérdezni a lekérdezésben. A lekérdezés első futtatásakor egy legördülő lista automatikusan töltődik változó megadott értékekre.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    Ezt a kódrészletet illessze be egy új bekezdés, és nyomja le az **SHIFT + ENTER**. Az alábbi képernyőfelvételen látható a kimenetet.
   
    ![A notebook használata Spark SQL-utasítás futtatása](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "a notebook használata Spark SQL-utasítás futtatása")
   
    A lekérdezések egy új értéket a legördülő listán válasszon, és futtassa újból a lekérdezést. Kattintson a **beállítások** választhat, milyen consitutes a kulcs értékeit a kimenetben. A fenti képernyőfelvételen használ **buildingID** átlaga kulcsként **temp_diff** értékeként, és **targettemp** csoportot.
7. Indítsa újra a Livy értelmező kilép az alkalmazásból. Ehhez az szükséges, parancsértelmező beállításai megnyitásához a bejelentkezett felhasználó nevében a jobb felső sarokban a, és kattintson a **parancsértelmező**.
   
    ![Indítsa el a parancsértelmező](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "kimeneti struktúra")
8. Görgessen a Livy parancsértelmező beállításait, és kattintson a **indítsa újra a**.
   
    ![Indítsa újra a Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "indítsa újra a Zeppelin intepreter")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Hogyan külső csomagok használata a notebook?
Konfigurálhatja a Zeppelin notebook az Apache Spark-fürt a HDInsight (Linux), amelyek nem szerepel az a-kész a fürt külső, közösségi hozzájárult csomagok használata. Kereshet az [Maven-tárház](http://search.maven.org/) csomagok rendelkezésre álló teljes listáját. Más forrásból is megkapható elérhető csomagok listáját. Például közösségi hozzájárult csomagok teljes listája megtalálható [Spark csomagok](http://spark-packages.org/).

Ebből a cikkből látni fogja a használata a [spark-fürt megosztott kötetei szolgáltatás](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) a Jupyter notebookkal csomag.

1. Nyissa meg a parancsértelmező beállításait. A jobb felső sarokban, kattintson a bejelentkezett felhasználó nevében, majd kattintson **parancsértelmező**.
   
    ![Indítsa el a parancsértelmező](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "kimeneti struktúra")
2. Görgessen a Livy parancsértelmező beállításait, és kattintson a **szerkesztése**.
   
    ![Parancsértelmező beállításainak módosítása](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "parancsértelmező beállításainak módosítása")
3. Adja hozzá egy új kulcsot, úgynevezett **livy.spark.jars.packages** és az értékét állítsa a formátumban `group:id:version`. Ha szeretné használni a [spark-fürt megosztott kötetei szolgáltatás](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) csomag, meg kell adni a kulcs értékének `com.databricks:spark-csv_2.10:1.4.0`.
   
    ![Parancsértelmező beállításainak módosítása](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "parancsértelmező beállításainak módosítása")
   
    Kattintson a **mentése** , majd indítsa újra a Livy értelmező.
4. **Tipp**: Ha azt szeretné, hogy hogyan megérkeznek a kulcsnak az értéke a fent megadott, hogyan.
   
    a. Keresse meg a csomagot a Maven-tárházat. Ebben az oktatóanyagban használtuk [spark-fürt megosztott kötetei szolgáltatás](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. A tárház gyűjtse össze a értékeinek **GroupId**, **artifactid szakaszát**, és **verzió**.
   
    ![Külső csomagok használata Jupyter notebook](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "külső csomagok használata Jupyter notebook")
   
    c. A következő három érték, egymástól kettősponttal összefűzésére (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>A Zeppelin notebookok tároló?
A fürt headnodes a Zeppelin notebookok kerülnek. Ezért, ha törli a fürtöt, a notebookok is törlődik. Ha meg szeretné őrizni későbbi használatra más fürtökön jegyzetfüzetek, exportálnia kell őket futó a feladatok befejezése után. A notebook exportálásához kattintson a **exportálása** ikon az alábbi ábrán látható módon.

![Töltse le a notebook](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "töltse le a notebook")

Ez a notebook egy JSON-fájl a letöltési helyre menti.

## <a name="livy-session-management"></a>Livy munkamenet-kezelés
A kód első bekezdésben szereplő esetekben a Zeppelin jegyzetfüzet futtatásakor a új Livy munkamenet létrehozása a HDInsight Spark-fürtön. A munkamenet által megosztott minden Zeppelin notebookok ezt követően hozzon létre. Ha valamilyen okból a munkamenet Livy leállítása (fürt újraindítás, stb.), nem kell a Zeppelin notebook származó feladatok futtathatók.

Ilyen esetben a következő lépéseket kell elvégezni, a Zeppelin jegyzetfüzet futó feladatok megkezdése előtt. 

1. Indítsa újra a Livy értelmező a Zeppelin notebook a. Ehhez az szükséges, parancsértelmező beállításai megnyitásához a bejelentkezett felhasználó nevében a jobb felső sarokban a, és kattintson a **parancsértelmező**.
   
    ![Indítsa el a parancsértelmező](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "kimeneti struktúra")
2. Görgessen a Livy parancsértelmező beállításait, és kattintson a **indítsa újra a**.
   
    ![Indítsa újra a Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "indítsa újra a Zeppelin intepreter")
3. Futtassa a kódcella meglévő Zeppelin jegyzetfüzet. Ez létrehoz egy új Livy munkamenetet a HDInsight-fürt.

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







