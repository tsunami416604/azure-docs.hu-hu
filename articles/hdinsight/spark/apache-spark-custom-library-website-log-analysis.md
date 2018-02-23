---
title: "A Spark - Azure Python könyvtárak webhelyek naplóinak elemzése |} Microsoft Docs"
description: "A notebook adatelemzés napló segítségével egyéni tárát a Spark on Azure Hdinsighttal mutatja be."
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 8c61c70f-fe7f-4f0f-a4ab-0cccee5668c9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: 544a11f5d9e92f081a8ae494529b7d8ef19551fa
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-spark-cluster-on-hdinsight"></a>Egy egyéni Python kódtár használata a HDInsight Spark-fürt webhelyek naplóinak elemzése

A notebook adatelemzés napló segítségével egyéni tárát a Spark on HDInsight mutatja be. Az egyéni könyvtár használjuk egy Python kódtár nevű **iislogparser.py**.

> [!TIP]
> Ez az oktatóanyag, Ön által létrehozott hdinsight (Linux) Spark-fürtön Jupyter notebook is érhető el. A notebook élmény lehetővé teszi a Python kódtöredékek futtassa a notebook magát. A notebook belül az oktatóprogram elvégzéséhez Spark-fürt létrehozása, indítsa el a Jupyter notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), és futtassa a notebook **naplóinak elemzése a Spark egy egyéni library.ipynb használatával** alatt a **PySpark**  mappa.
>
>

**Előfeltételek:**

Az alábbiakkal kell rendelkeznie:

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Nyers adatok elmentse egy RDD
Ebben a szakaszban használjuk a [Jupyter](https://jupyter.org) notebook társított Apache Spark-fürt a Hdinsightban, amely a nyers mintaadatok feldolgozni, és mentse egy Hive tábla feladatok futtatásához. A mintaadatok egy CSV-fájlt (hvac.csv) elérhető alapértelmezés szerint minden fürtön.

Miután az adatok mentése Hive tábla a következő szakaszban nem fog csatlakozni a Hive tábla a Power BI és a Tableau Üzletiintelligencia-eszközök.

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
5. Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. Ehhez a forgatókönyvhöz szükséges típusok importálása megkezdése. Illessze be a következő kódrészletet a cella üres, és nyomja le az **SHIFT + ENTER**.

        from pyspark.sql import Row
        from pyspark.sql.types import *


1. Hozzon létre egy RDD használatával a napló mintaadatok már elérhető a fürtön. Végezheti el az adatokat a fürthöz tartozó alapértelmezett tárfiókban **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


1. Egy mintanaplót, ellenőrizze, hogy beállítása sikeresen befejeződött az előző lépésben beolvasása.

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

## <a name="analyze-log-data-using-a-custom-python-library"></a>Egy egyéni Python kódtár használatával napló adatok elemzése
1. A fenti kimenetben az első néhány sor tartalmazza a fejléc-információ, és minden fennmaradó sor megfelel az adott fejléc ismertetett séma. Ilyen naplók elemzése bonyolult lehet. Igen, egy egyéni Python kódtár használjuk (**iislogparser.py**), amely lehetővé teszi az ilyen sokkal könnyebben naplók elemzése. Alapértelmezés szerint ezt a szalagtárat megtalálható a következő hdinsight Spark-fürt **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    Nincs a azonban ezt a szalagtárat a `PYTHONPATH` , nem tudjuk használni, például import utasítás segítségével `import iislogparser`. Szeretné használni ezt a szalagtárat, azt kell terjeszteni az összes munkavégző csomópontokhoz. Futtassa a következő kódrészletet.

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


1. `iislogparser` funkció `parse_log_line` , amely visszaadja a `None` Ha egy napló sor a fejlécsor lesz, és egy példányát adja vissza a `LogLine` osztály, ha egy napló sor ütközik. Használja a `LogLine` osztály a napló csak a Sorok kinyerése a RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
2. Néhány kibontott napló sorral annak ellenőrzésére, hogy sikeresen befejeződött. a lépés beolvasása.

       logLines.take(2)

   A kimenet az alábbihoz hasonló lesz:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
3. A `LogLine` osztály, viszont az például néhány hasznos metódusokkal rendelkezik `is_error()`, amely adja vissza, hogy rendelkezik-e a naplóbejegyzés hibakódot. Használja ezt a kibontott napló sorokat fellépett hibák száma számítási, és jelentkezzen be a hibák egy másik fájlba.

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
4. Is **Matplotlib** képi megjelenítés az adatok összeállításához. Például ha el szeretné különíteni a kérelmeket, amelyek hosszú ideig futnak az okait, érdemes található a fájl, amely a legtöbb kiszolgálására átlagos időt vehet igénybe.
   Az alábbi részlet egy kérelem kiszolgálására legtöbb időt vett felső 25 erőforrásokat kéri le.

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
5. Ezt az információt a képernyőn ábra is jelenthet. Első lépésként rajzot létrehozásához, ossza meg velünk először létre kell hoznia egy ideiglenes tábla **AverageTime**. A tábla a naplók megtekintéséhez, hogy voltak-e bármilyen szokatlan késés igényeiben jelentkező adott bármikor idő szerint csoportosítja.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
6. Ezt követően futtathatja a következő SQL-lekérdezést a rekordok lekérése a **AverageTime** tábla.

       %%sql -o averagetime
       SELECT * FROM AverageTime

   A `%%sql` magic követ `-o averagetime` biztosítja, hogy a lekérdezés kimenetét a Jupyter kiszolgálón (általában a fürt headnode) helyileg megőrződjenek. A kimeneti tárolva a [Pandas](http://pandas.pydata.org/) a megadott nevű dataframe **averagetime**.

   A következőhöz hasonló kimenetnek kell megjelennie:

   ![SQL-lekérdezés kimeneti](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL-lekérdezés kimenete")

   További információ a `%%sql` magic, lásd: [támogatott paraméterek a %% sql magic](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).
7. Most már használhatja Matplotlib, a szalagtár segítségével hozza létre az adatok, a képi megjelenítés rajzot létrehozásához. Mert a rajzolási létre kell hozni a helyileg tárolt **averagetime** dataframe, a kódrészletet a következővel kell kezdődnie az `%%local` magic. Ez biztosítja, hogy a kód a Jupyter kiszolgálón helyileg futnak.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   A következőhöz hasonló kimenetnek kell megjelennie:

   ![Matplotlib kimeneti](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib kimeneti")
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
