---
title: Python-kódtárakat a Spark - az Azure webhelyek naplóinak elemzése
description: Ez a jegyzetfüzet bemutatja, hogyan elemezheti a naplófájlok adatait a Spark on Azure HDInsight egy egyéni kódtár használatával.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: jasonh
ms.openlocfilehash: a22e1f90c01b6b0e871516816815286a4f6671a2
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045716"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-spark-cluster-on-hdinsight"></a>Egy egyéni Python-kódtár használata a HDInsight Spark-fürt webhelynaplók elemzése

Ez a jegyzetfüzet bemutatja, hogyan elemezheti a naplófájlok adatait egy egyéni kódtár a Spark on HDInsight használatával. Az egyéni tárát használja használjuk az nevű Python-kódtár **iislogparser.py**.

> [!TIP]
> Ebben az oktatóanyagban egyben egy Jupyter notebookot HDInsight szolgáltatásban létrehozott (Linux) Spark-fürtön elérhető. A jegyzetfüzet-megoldás lehetővé teszi a notebookból magát a Python-kódrészletek futtatását. Egy jegyzetfüzetet belül, az oktatóanyag elvégzéséhez hozzon létre egy Spark-fürtöt, indítsa el a Jupyter notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), majd futtassa a notebook **-naplók elemzése a Spark használatával egyéni library.ipynb** alatt a **PySpark**  mappát.
>
>

**Előfeltételek:**

Az alábbiakkal kell rendelkeznie:

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Nyers adatok mentése egy RDD
Ebben a szakaszban fogjuk használni a [Jupyter](https://jupyter.org) notebook társított Apache Spark-fürt a HDInsight, amely a nyers mintaadatok feldolgozni, és mentse egy Hive-táblába feladatok futtatásához. A mintaadatokat egy .csv-fájlba (hvac.csv) elérhető alapértelmezés szerint minden fürtön.

Miután az adatokat, egy Hive-táblába menti, a következő szakaszban nem fog csatlakozni a Hive-tábla használatával, BI eszközök, például a Power BI és a Tableau.

1. Az [Azure portál](https://portal.azure.com/) kezdőpultján kattintson a Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a következő helyről is megkeresheti: **Browse All (Összes tallózása)** > **HDInsight Clusters** (HDInsight-fürtök).   
2. A Spark-fürt panelén kattintson a **Fürt irányítópultja Dashboard** lehetőségre, majd a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

   > [!NOTE]
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Hozzon létre új notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.

    ![Új Jupyter notebook létrehozása](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Új Jupyter notebook létrehozása")
4. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. A felső részen kattintson a notebook nevére, és adjon meg egy könnyen megjegyezhető nevet.

    ![Adjon nevet a notebooknak](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Adjon nevet a notebooknak")
5. Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. Ehhez a forgatókönyvhöz szükséges típusok importálása elindíthatja. Illessze be a következő kódrészletet egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt.

        from pyspark.sql import Row
        from pyspark.sql.types import *


1. Hozzon létre egy RDD mintaadatokkal a napló már elérhető a fürtön. Férhet hozzá az adataihoz a fürthöz társított alapértelmezett tárfiókban **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


1. Beolvasni egy mintanaplót ellenőrizheti, hogy állítsa be az előző lépésben sikeresen befejeződött.

        logs.take(5)

    A következőhöz hasonló kimenetnek kell megjelennie:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Elemezheti a naplófájlok adatait egy egyéni Python-kódtár használatával
1. A fenti kimenetben az első néhány sor a fejléc-információkat tartalmaznak, és minden fennmaradó sor megfelel-e a séma a fejléc ismertetett. Ilyen naplók elemzése bonyolult lehet. Ezért használjuk egy egyéni Python-kódtár (**iislogparser.py**), amely lehetővé teszi az ilyen sokkal könnyebben naplók elemzése. Alapértelmezés szerint ez a kódtár részét képezi a HDInsight, Spark-fürthöz **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    Azonban ebben a könyvtárban nem szerepel a következőben a `PYTHONPATH` így nem lehet használni, például importálás utasítás segítségével `import iislogparser`. A tár használata, hogy kell terjesztenie az összes munkavégző csomópontja. Futtassa az alábbi kódrészletet.

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


1. `iislogparser` biztosít egy függvény `parse_log_line` , amely visszaadja a `None` Ha egy napló sor a fejlécsor lesz, és a egy példányát adja vissza a `LogLine` osztályhoz, ha egy napló sor tapasztal. Használja a `LogLine` osztály a napló csak a Sorok kinyerése a RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
2. Néhány kinyert log sorral ellenőrizheti, hogy sikeresen befejeződött. a lépés beolvasni.

       logLines.take(2)

   A kimenet az alábbihoz hasonló lesz:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
3. A `LogLine` osztály, viszont az például néhány hasznos metódussal rendelkezik `is_error()`, hogy rendelkezik-e egy naplóbejegyzés hibakódot ad vissza. Ezzel a számítási hibák a kinyert log sorok száma, és jelentkezzen be a hibákat az egy másik fájlba.

       errors = logLines.filter(lambda p: p.is_error())
       numLines = logLines.count()
       numErrors = errors.count()
       print 'There are', numErrors, 'errors and', numLines, 'log entries'
       errors.map(lambda p: str(p)).saveAsTextFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

   A következőhöz hasonló kimenetnek kell megjelennie:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       There are 30 errors and 646 log entries
4. Is **Matplotlib** az adatok a Vizualizáció létrehozásához. Például ha el szeretné különíteni a hosszú ideig futó kérelmek okának, érdemes keresse meg a fájlokat, az átlagos kiszolgálása érdekében a legtöbb időt vesz igénybe.
   Az alábbi kódrészlet lekérdezi a felső 25 erőforrást, amely a kérés kiszolgálása érdekében a legtöbb időt vett igénybe.

       def avgTimeTakenByKey(rdd):
           return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                   lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                   lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                     .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

       avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

   A következőhöz hasonló kimenetnek kell megjelennie:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [(u'/blogposts/mvc4/step13.png', 197.5),
        (u'/blogposts/mvc2/step10.jpg', 179.5),
        (u'/blogposts/extractusercontrol/step5.png', 170.0),
        (u'/blogposts/mvc4/step8.png', 159.0),
        (u'/blogposts/mvcrouting/step22.jpg', 155.0),
        (u'/blogposts/mvcrouting/step3.jpg', 152.0),
        (u'/blogposts/linqsproc1/step16.jpg', 138.75),
        (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
        (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
        (u'/blogposts/nested/step2.jpg', 126.0),
        (u'/blogposts/adminpack/step1.png', 124.0),
        (u'/BlogPosts/datalistpaging/step2.png', 118.0),
        (u'/blogposts/mvc4/step35.png', 117.0),
        (u'/blogposts/mvcrouting/step2.jpg', 116.5),
        (u'/blogposts/aboutme/basketball.jpg', 109.0),
        (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
        (u'/blogposts/mvc4/step12.png', 106.0),
        (u'/blogposts/linq8/step0.jpg', 105.5),
        (u'/blogposts/mvc2/step18.jpg', 104.0),
        (u'/blogposts/mvc2/step11.jpg', 104.0),
        (u'/blogposts/mvcrouting/step1.jpg', 104.0),
        (u'/blogposts/extractusercontrol/step1.png', 103.0),
        (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
        (u'/blogposts/mvcrouting/step21.jpg', 101.0),
        (u'/blogposts/mvc4/step1.png', 98.0)]
5. Ez az információ diagram formájában is bemutathatja. Első lépésként létrehozhat egy rajzot, ossza meg velünk először hozzon létre egy ideiglenes táblát **AverageTime**. A tábla a naplók megtekintéséhez, hogy voltak-e bármilyen szokatlan késés adatforgalmi csúcsokhoz bármely adott időpontban idő szerint csoportosítja.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
6. Ezután futtathatja a következő SQL-lekérdezést összes rekord lekérése a **AverageTime** tábla.

       %%sql -o averagetime
       SELECT * FROM AverageTime

   A `%%sql` Magic Quadrant követ `-o averagetime` biztosítja, hogy a lekérdezés kimenete a Jupyter-kiszolgálón (általában a fürt átjárócsomópontjával) helyileg tárolja. A kimenet a megőrzés pedig egy [Pandas](http://pandas.pydata.org/) a megadott nevű adathalmaz **averagetime**.

   A következőhöz hasonló kimenetnek kell megjelennie:

   ![SQL-lekérdezés kimenete](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL-lekérdezés kimenete")

   További információ a `%%sql` magic, lásd: [támogatott paraméterek a %% sql Magic Quadrant](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).
7. Mostantól használhatja a Matplotlib, a kódtár segítségével hozza létre a vizualizációt az adatokat, létrehozhat egy rajzot. Az ábrázolást kell létrehozni, mert a megőrzött helyileg **averagetime** adathalmaz, a kódtöredék a következővel kell kezdődnie az `%%local` Magic Quadrant. Ez biztosítja, hogy a kód a Jupyter-kiszolgálón helyben futtatja.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   A következőhöz hasonló kimenetnek kell megjelennie:

   ![Matplotlib kimeneti](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib kimenet")
8. Az alkalmazás futtatását követően állítsa le a notebookot az erőforrások felszabadítása érdekében. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ezzel leállítja és bezárja a notebookot.

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
