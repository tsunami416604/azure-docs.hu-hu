---
title: Webhelyek naplóinak elemzése Python-tárakkal a Sparkban – Azure
description: Ez a jegyzetfüzet azt mutatja be, hogyan elemezheti a naplózási adataikat egyéni kódtár használatával a Spark on Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 6d23e8cfa8d20169d2b63723138b60dafb1069de
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146981"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Webhelyek naplóinak elemzése egyéni Python-kódtár használatával Apache Spark-fürttel a HDInsight-on

Ez a jegyzetfüzet azt mutatja be, hogyan elemezheti a naplózási adataikat egy egyéni kódtár használatával, Apache Spark a HDInsight. Az általunk használt egyéni könyvtár egy **iislogparser.py**nevű Python-könyvtár.

> [!TIP]  
> Ez a cikk Jupyter-jegyzetfüzetként is elérhető a HDInsight-ben létrehozott Spark-(Linux-) fürtökön. A notebook felülete lehetővé teszi a Python-kódrészletek futtatását a jegyzetfüzetből. A cikk jegyzetfüzetből való elvégzéséhez hozzon létre egy Spark-fürtöt, nyisson meg`https://CLUSTERNAME.azurehdinsight.net/jupyter`egy Jupyter notebookot (), majd futtassa a jegyzetfüzeteket a **Sparkban az egyéni kódtár használatával. ipynb** a **PySpark** mappában.

**Előfeltételek:**

A következőkkel kell rendelkeznie:

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Nyers adatbázis mentése RDD
Ebben a szakaszban a HDInsight lévő Apache Spark-fürthöz társított [Jupyter](https://jupyter.org) -jegyzetfüzetet használjuk a nyers mintaadatok feldolgozását végző feladatok futtatásához és egy struktúra-táblázatként való mentéséhez. A mintaadatok egy. csv-fájl (HVAC. csv), amely alapértelmezés szerint minden fürtön elérhető.

Miután az adatait Apache Hive táblázatként mentette, a következő szakaszban a BI-eszközök, például a Power BI és a tabló használatával fogunk csatlakozni a kaptár táblához.

1. Az [Azure portál](https://portal.azure.com/) kezdőpultján kattintson a Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a következő helyről is megkeresheti: **Browse All (Összes tallózása)**  > **HDInsight Clusters** (HDInsight-fürtök).

2. A Spark-fürt panelén kattintson a **Fürt irányítópultja Dashboard** lehetőségre, majd a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

   > [!NOTE]
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Hozzon létre új notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.

    ![Új Apache Jupyter-jegyzetfüzet létrehozása](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Új Jupyter-jegyzetfüzet létrehozása")

4. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. A felső részen kattintson a notebook nevére, és adjon meg egy könnyen megjegyezhető nevet.

    ![Adjon nevet a notebooknak](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Adjon nevet a notebooknak")
5. Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. Először importálja a forgatókönyvhöz szükséges típusokat. Illessze be a következő kódrészletet egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt.

        from pyspark.sql import Row
        from pyspark.sql.types import *

6. Hozzon létre egy RDD a fürtön már elérhető mintaadatok használatával. A fürthöz tartozó alapértelmezett Storage-fiókban lévő, a **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**címen található adatelérési helyhez férhet hozzá.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')

7. Egy mintául szolgáló naplóbejegyzés lekérésével ellenőrizheti, hogy az előző lépés sikeresen befejeződött-e.

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

## <a name="analyze-log-data-using-a-custom-python-library"></a>Naplófájlok elemzése egyéni Python-kódtár használatával

1. A fenti kimenetben az első pár sor tartalmazza a fejléc adatait, és minden fennmaradó sor megegyezik a fejlécben leírt sémával. Az ilyen naplók elemzése bonyolult lehet. Tehát egy egyéni Python-függvénytárat (**iislogparser.py**) használunk, amely sokkal egyszerűbbé teszi az ilyen naplók elemzését. Alapértelmezés szerint ez a tár a Spark-fürt része a HDInsight-on a **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**címen.

    Ez a tár azonban nem a `PYTHONPATH` következő, ezért nem használható olyan importálási utasítás használatával, mint például `import iislogparser`:. Ennek a könyvtárnak a használatához terjesztenie kell az összes munkavégző csomópontra. Futtassa az alábbi kódrészletet.

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')

1. `iislogparser`egy olyan függvényt `parse_log_line` ad `None` vissza, amely azt adja meg, hogy a naplósor fejléc-e, és `LogLine` az osztály egy példányát adja vissza, ha egy naplófájlt tartalmaz. `LogLine` A osztály használatával csak a naplósorokat lehet kibontani a RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()

1. Néhány kibontott naplózási sor beolvasása annak ellenőrzéséhez, hogy a lépés sikeresen befejeződött-e.

       logLines.take(2)

   A kimenet az alábbihoz hasonló lesz:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]

1. A `LogLine` (z) osztálynak vannak olyan hasznos módszerei, mint `is_error()`például a, amely azt adja vissza, hogy a naplóbejegyzés tartalmaz-e hibakódot. Ezzel kiszámíthatja a hibák számát a kibontott naplófájlokban, majd egy másik fájlba naplózhatja az összes hibát.

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
1. Az **Matplotlib** használatával az adatvizualizációt is létrehozhatja. Ha például el szeretné különíteni a hosszú ideig futó kérelmek okát, érdemes lehet megkeresni azokat a fájlokat, amelyek a legtöbb időt igénybe vehetik.
   Az alábbi kódrészlet lekérdezi az első 25 erőforrást, amely a legtöbb időt a kérelem kiszolgálására vette.

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

1. Ezeket az információkat a mintaterület formájában is megjelenítheti. Első lépésként hozzon létre egy ideiglenes tábla **AverageTime**. A tábla ideje szerint csoportosítja a naplókat, hogy az adott időpontban szokatlan késéssel jár-e.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

1. Ezután futtathatja a következő SQL-lekérdezést a **AverageTime** tábla összes rekordjának lekéréséhez.

       %%sql -o averagetime
       SELECT * FROM AverageTime

   A `%%sql` Magic`-o averagetime` ezt követően biztosítja, hogy a lekérdezés kimenete helyileg megmaradjon a Jupyter-kiszolgálón (általában a fürt átjárócsomóponthoz). A kimenet a megadott nevű **Averagetime** [pandák](https://pandas.pydata.org/) dataframe marad.

   A következőhöz hasonló kimenetnek kell megjelennie:

   ![hdinsight jupyter SQL-qyery kimenete](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL-lekérdezés kimenete")

   A `%%sql` Magic szolgáltatással kapcsolatos további információkért lásd: [%% SQL Magic által támogatott paraméterek](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Most már használhatja a Matplotlib, az adatvizualizációk létrehozásához használt könyvtárat, amely létrehoz egy rajzolt. Mivel a parcellát a helyileg megőrzött **averagetime** dataframe kell létrehozni, a kódrészletnek a `%%local` mágia kell kezdődnie. Ez biztosítja, hogy a kód helyileg fusson a Jupyter-kiszolgálón.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   A következőhöz hasonló kimenetnek kell megjelennie:

   az ![Apache Spark webes naplójának elemzési ábrázolása](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib kimenete")

1. Az alkalmazás futtatását követően állítsa le a notebookot az erőforrások felszabadítása érdekében. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ezzel leállítja és bezárja a notebookot.

## <a name="seealso"></a>Lásd még:
* [Áttekintés Apache Spark az Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Apache Spark BI-val: Interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learningkal: A Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningkal: Az élelmiszer-vizsgálati eredmények előrejelzése a Spark in HDInsight használatával](apache-spark-machine-learning-mllib-ipython.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [A IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Apache Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek Apache Spark-fürtben HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
