---
title: Webhelynaplók elemzése Python-kódtárakkal a Sparkban – Azure
description: Ez a jegyzetfüzet bemutatja, hogyan elemezheti a naplóadatokat egy egyéni kódtár használatával az Azure HDInsight Spark használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: c6bf26d8f3a73db6ee69b2aa0de73872911893bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552712"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Webhelynaplók elemzése egyéni Python-könyvtár használatával az Apache Spark-fürttel a HDInsighton

Ez a jegyzetfüzet bemutatja, hogyan elemezheti a naplóadatokat egy egyéni könyvtár használatával az Apache Spark segítségével a HDInsighton. Az általunk használt egyéni könyvtár egy **Python-könyvtár,** a iislogparser.py.

## <a name="prerequisites"></a>Előfeltételek

Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Nyers adatok mentése rdd-ként

Ebben a szakaszban a HDInsightban egy Apache Spark-fürthöz társított [Jupyter-jegyzetfüzetet](https://jupyter.org) használjuk a nyers mintaadatokat feldolgozó és Hive-táblaként mentést tartalmazó feladatok futtatásához. A mintaadatok alapértelmezés szerint az összes fürtön elérhető .csv fájl (hvac.csv).

Miután az adatokat apache Hive-táblaként mentette, a következő szakaszban bi-eszközökkel, például a Power BI-val és a Tableau-val fogunk csatlakozni a Hive-táblához.

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net/jupyter`meg `CLUSTERNAME` a , ahol a fürt neve.

1. Hozzon létre új notebookot. Válassza az **Új**lehetőséget, majd a **PySpark**lehetőséget.

    ![Új Apache Jupyter notebook létrehozása](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Új Jupyter notebook létrehozása")

1. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. Jelölje ki a jegyzetfüzet nevét a tetején, és adjon meg egy rövid nevet.

    ![A notebook elnevezése](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "A notebook elnevezése")

1. Mivel a PySpark kernel használatával létrehozott egy jegyzetfüzetet, nem kell explicit környezeteket létrehoznia. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. Először importálja az ehhez a forgatókönyvhöz szükséges típusokat. Illessze be a következő kódrészletet egy üres cellába, majd nyomja le a **Shift + Enter billentyűkombinációt.**

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. RdD létrehozása a fürtön már elérhető mintanapló-adatok használatával. A fürthöz társított alapértelmezett tárfiókban lévő `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log`adatokat a rendszer a ban érheti el. Hajtsa végre a következő kódot:

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. A mintanapló készlet beolvasása annak ellenőrzéséhez, hogy az előző lépés sikeresen befejeződött-e.

    ```pyspark
    logs.take(5)
    ```

    A következő höz hasonló kimenetnek kell lennie:

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>Naplóadatok elemzése egyéni Python-tár használatával

1. A fenti kimenetben az első pársorok tartalmazzák a fejlécadatokat, és minden fennmaradó sor megegyezik a fejlécben leírt sémával. Az ilyen naplók elemzése bonyolult lehet. Tehát egy egyéni Python könyvtárat **(iislogparser.py)** használunk, amely sokkal könnyebbé teszi az ilyen naplók elemzését. Alapértelmezés szerint ez a tár tartalmazza a Spark-fürt hdinsight a. `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py`

    Ez a könyvtár azonban nem `PYTHONPATH` található, így nem használhatjuk egy `import iislogparser`importálási utasítás használatával, például a . A tár használatához szét kell osztanunk az összes munkavégző csomópontnak. Futtassa a következő részletet.

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. `iislogparser`olyan függvényt `parse_log_line` `None` ad vissza, amely akkor ad vissza, `LogLine` ha a naplósor fejlécsor, és az osztály egy példányát adja vissza, ha naplósorba ütközik. Az `LogLine` osztály segítségével csak a naplósorokat bontsa ki az RDD-ből:

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. Néhány kibontott naplósor beolvasása a lépés sikeres befejezésének ellenőrzéséhez.

    ```pyspark
    logLines.take(2)
    ```

   A kimenetnek a következő szöveghez hasonlónak kell lennie:

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. Az `LogLine` osztály viszont rendelkezik néhány hasznos `is_error()`módszerrel, például a , amely azt adja vissza, hogy a naplóbejegyzésnek van-e hibakódja. Ezzel az osztállyal kiszámíthatja a kibontott naplósorokban előforduló hibák számát, majd naplózja az összes hibát egy másik fájlba.

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    A kimenetnek `There are 30 errors and 646 log entries`meg kell jelennie.

1. A **Matplotlib** segítségével is létrehozhatja az adatok vizualizációját. Ha például el szeretné különíteni a hosszú ideig futó kérelmek okait, érdemes lehet megkeresni azokat a fájlokat, amelyek átlagosan a legtöbb időt veszik igénybe. Az alábbi kódrészlet lekéri a 25 legfontosabb erőforrást, amely a legtöbb időt vett igénybe a kérelem kiszolgálásához.

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   A következő hez hasonló kimenetnek kell lennie:

    ```output
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
    ```

1. Ezt az információt nyomtatás formájában is megmutathatja. A telek létrehozásának első lépéseként hozzunk létre először egy ideiglenes táblázatot **AverageTime**. A tábla csoportosítja a naplókat az idő, hogy lássa, voltak-e szokatlan késési csúcsok egy adott időpontban.

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. Ezután a következő SQL-lekérdezés futtatásával lejuthat az **AverageTime** tábla összes rekordjának lehívására.

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   A `%%sql` varázslat, `-o averagetime` amelyet követ, biztosítja, hogy a lekérdezés kimenete helyileg megmarad a Jupyter-kiszolgálón (általában a fürt fejnokrészén). A kimenet [pandas](https://pandas.pydata.org/) adatkeretként marad meg a megadott nevű **átlagidővel.**

   A következőhez hasonló kimenetnek kell lennie:

   ![hdinsight jupyter sql lekérdezés kimenete](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL-lekérdezés kimenete")

   A `%%sql` varázslattal kapcsolatos további információkért [lásd: A (z) %%sql magic által támogatott paraméterek](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Most már használhatja Matplotlib, a könyvtár létrehozásához használt vizualizációs adatok, hozzon létre egy telek. Mivel a nyomtatást a helyileg megőrzött **átlagos időadatkeretből** kell létrehozni, `%%local` a kódrészletnek a varázslattal kell kezdődnie. Ez biztosítja, hogy a kód helyileg fusson a Jupyter kiszolgálón.

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   A következőhez hasonló kimenetnek kell lennie:

   ![Apache Spark webnapló elemzés telek](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib kimenet")

1. Miután befejezte az alkalmazás futtatását, állítsa le a jegyzetfüzetet az erőforrások felszabadításához. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ez a művelet leállítja és bezárja a jegyzetfüzetet.

## <a name="next-steps"></a>További lépések

Fedezze fel a következő cikkeket:

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)