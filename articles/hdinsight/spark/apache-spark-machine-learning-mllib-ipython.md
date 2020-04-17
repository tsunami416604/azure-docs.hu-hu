---
title: Példa gépi tanulási példáka Spark MLlib használatára a HDInsighton – Azure
description: Ismerje meg, hogyan használhatja a Spark MLlib egy gépi tanulási alkalmazást, amely logisztikai regressziós osztályozással elemzi az adatkészletet.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: 26695df299ba5d0f50c8f271b5da99284a8d6764
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531133"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Gépi tanulási alkalmazások létrehozása és adatkészlet elemzése az Apache Spark MLlib használatával

Ismerje meg, hogyan hozhat létre gépi tanulási alkalmazást az Apache Spark [MLlib](https://spark.apache.org/mllib/) használatával. Az alkalmazás prediktív elemzést végez egy nyitott adatkészleten. A Spark beépített gépi tanulási kódtáraiból ez a példa logisztikai regresszión keresztüli *besorolást* használ.

Az MLlib egy alapvető Spark-könyvtár, amely számos olyan segédprogramot biztosít, amelyek hasznosak a gépi tanulási feladatokhoz, például:

* Osztályozás
* Regresszió
* Fürtözés
* Modellezés
* Egyedi érték-bomlás (SVD) és fő komponenselemzés (PCA)
* Hipotézistesztelés és mintastatisztikák számítása

## <a name="understand-classification-and-logistic-regression"></a>Az osztályozás és a logisztikai regresszió ismertetése

*Besorolás*, egy népszerű gépi tanulási feladat, a folyamat a bemeneti adatok kategóriákba rendezésének folyamata. Ez a feladata a besorolási algoritmus, hogy kitaláljuk, hogyan kell hozzárendelni a "címkék" a megadott bemeneti adatokat. Például azt gondolhatja, egy gépi tanulási algoritmus, amely elfogadja a készletadatok bemenetként. Ezután osztja az állomány két kategóriába sorolhatók: készletek, hogy el kell adni, és a készletek, hogy meg kell tartani.

A logisztikai regresszió a besoroláshoz használt algoritmus. A Spark logisztikai regressziós API-ja hasznos *a bináris osztályozáshoz,* vagy a bemeneti adatok két csoport egyikébe történő besorolásához. A logisztikai regressziókról a [Wikipédia című témakörben](https://en.wikipedia.org/wiki/Logistic_regression)talál további információt.

Összefoglalva, a logisztikai regressziós folyamat *logisztikai függvényt*hoz létre. A függvény segítségével előre jelezheti annak valószínűségét, hogy egy bemeneti vektor az egyik vagy a másik csoportba tartozik.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Prediktív elemzés példa az élelmiszer-vizsgálati adatokra

Ebben a példában a Spark segítségével prediktív elemzést végezhet az élelmiszer-vizsgálati adatokon (**Food_Inspections1.csv**). A [Chicagói Város adatportálján](https://data.cityofchicago.org/)keresztül szerzett adatok. Ez az adatkészlet a Chicagóban végzett élelmiszer-ipari létesítmények vizsgálatairól tartalmaz információkat. Beleértve az egyes létesítményekre vonatkozó információkat, a feltárt jogsértéseket (ha vannak ilyenek), valamint az ellenőrzés eredményeit. A CSV-adatfájl már elérhető a fürthöz társított tárfiókban a **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**helyen.

Az alábbi lépésekben kifejleszt egy modellt, hogy mi kell ahhoz, hogy átmenjen egy élelmiszer-ellenőrzésen, vagy megbukjon.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Apache Spark MLlib gépi tanulási alkalmazás létrehozása

1. Hozzon létre egy Jupyter notebookot a PySpark-kernellel. Az utasításokért lásd: [Jupyter notebook létrehozása](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importálja az alkalmazáshoz szükséges típusokat. Másolja a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER billentyűkombinációt.**

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```

    A PySpark kernel miatt nem kell explicit környezeteket létrehoznia. A Spark és a Hive-környezetek automatikusan létrejönnek az első kódcella futtatásakor.

## <a name="construct-the-input-dataframe"></a>A bemeneti adatkeret kialakítása

A Spark környezetben a nyers CSV-adatok at a memóriába strukturálatlan szövegként húzza le. Ezután a Python CSV-könyvtárával elemezheti az adatok egyes sorait.

1. A bemeneti adatok importálásával és elemzésével futtassa a következő sorokat egy rugalmas elosztott adatkészlet (RDD) létrehozásához.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value

    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Futtassa a következő kódot, hogy beolvasson egy sort az RDD-ből, hogy megnézhesse az adatsémát:

    ```PySpark
    inspections.take(1)
    ```

    A kimenet a következő:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    A kimenet a bemeneti fájl sémájának ötletét adja. Ez magában foglalja a nevét minden létesítmény, és a létesítmény típusát. Is, a cím, az adatok az ellenőrzések, és a helyét, többek között.

3. Futtassa a következő kódot egy adatkeret (*df*) és egy ideiglenes tábla (*CountResults*) létrehozásához néhány oszlopot, amelyek hasznosak a prediktív elemzéshez. `sqlContext`strukturált adatok átalakítására szolgál.

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])

    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Az adatkeret négy érdekes oszlopa az **azonosító**, **név**, **eredmények**és **szabálysértések**.

4. Futtassa a következő kódot, hogy egy kis mintát kapjon az adatokból:

    ```PySpark
    df.show(5)
    ```

    A kimenet a következő:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Az adatok megismerése

Kezdjük meg, hogy milyen az adatkészlet tartalmazza. 

1. Futtassa a következő kódot az **eredmények** oszlopában lévő különböző értékek megjelenítéséhez:

    ```PySpark
    df.select('results').distinct().show()
    ```

    A kimenet a következő:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Az eredmények eloszlásának megjelenítéséhez futtassa a következő kódot:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    A `%%sql` varázslat, `-o countResultsdf` amelyet követ, biztosítja, hogy a lekérdezés kimenete helyileg megmarad a Jupyter-kiszolgálón (általában a fürt fejnokrészén). A kimenet [pandas](https://pandas.pydata.org/) adatkeretként marad meg a megadott **countResultsdf**névvel. A PySpark `%%sql` kernelben elérhető varázslatokról és egyéb varázslatokról a [Jupyter-jegyzetfüzetekben elérhető Kernelek Apache Spark HDInsight-fürtökkel című](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)témakörben talál további információt.

    A kimenet a következő:

    ![SQL-lekérdezés kimenete](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL-lekérdezés kimenete")

3. A [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib)könyvtárat az adatok megjelenítéséhez használt társegítségével is létrehozhatja a nyomtatás. Mivel a nyomtatást a helyileg megőrzött **countResultsdf** adatkeretből kell létrehozni, `%%local` a kódrészletnek a varázslattal kell kezdődnie. Ez a művelet biztosítja, hogy a kód helyileg fusson a Jupyter-kiszolgálón.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Az élelmiszer-ellenőrzési eredmény előrejelzéséhez ki kell dolgoznia egy modellt a jogsértések alapján. Mivel a logisztikai regresszió bináris osztályozási módszer, célszerű az eredményadatokat két kategóriába sorolni: **Sikertelen** és **Átlépés**:

   - Át
       - Át
       - Pass w/ feltételek
   - Sikertelen
       - Sikertelen
   - Elvetés
       - Az üzleti vállalkozás nem található
       - Üzleten kívüli

     A többi eredménnyel ("Üzleti helyen nem található" vagy "Üzleten kívül") rendelkező adatok nem hasznosak, és az eredmények kis százalékát teszik ki.

4. Futtassa a következő kódot a`df`meglévő dataframe( ) új adatkeretté alakításához, ahol minden egyes ellenőrzés címke-megsértése párként jelenik meg. Ebben az esetben a `0.0` címke egy hiba, `1.0` a címke jelent a siker, és a `-1.0` címke képviseli néhány eredményt a két eredmény mellett.

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Futtassa a következő kódot a címkézett adatok egy sorának megjelenítéséhez:

    ```PySpark
    labeledData.take(1)
    ```

    A kimenet a következő:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Logisztikai regressziós modell létrehozása a bemeneti adatkeretből

A végső feladat a címkézett adatok konvertálása. Az adatokat olyan formátumgá alakíthatja, amely logisztikai regresszióval elemezhető. A logisztikai regressziós algoritmus bemenetéhez *címke-jellemző vektorpárokra*van szükség. Ahol a "jellemzővektor" a bemeneti pontot reprezentatot képviselő számok vektora. Tehát meg kell konvertálnia a "szabálysértések" oszlopot, amely félig strukturált, és sok megjegyzést tartalmaz a szabad szövegben. Alakítsa át az oszlopot valós számok olyan tömbjéhez, amelyeket egy gép könnyen megérthet.

A természetes nyelv feldolgozásának egyik szabványos gépi tanulási megközelítése, hogy minden egyes különböző szóhoz "indexet" rendel. Ezután adja át a vektor a gépi tanulási algoritmus. Úgy, hogy minden index értéke tartalmazza az adott szó relatív gyakoriságát a szöveges karakterláncban.

Az MLlib egyszerű módot kínál a művelet megcsinálására. Először is, "tokenize" minden megsértése string,hogy az egyes szavakat minden string. Ezután az `HashingTF` a segítségével konvertálja a tokenek minden egyes készletét egy szolgáltatásvektor, amely ezután átadható a logisztikai regressziós algoritmus egy modell létrehozásához. Ezeket a lépéseket egy "csővezeték" használatával egymás után hajthatja végre.

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>A modell kiértékelése egy másik adatkészlet használatával

A korábban létrehozott modell segítségével *előre jelezheti,* hogy milyen eredményeket fog hozni az új ellenőrzések. Az előrejelzések a megfigyelt jogsértéseken alapulnak. Ezt a modellt az **Food_Inspections1.csv**adatkészleten tanította be. A modell erősségének *kiértékeléséhez használhatja* a modell erősségét az **Food_Inspections2.csv**Food_Inspections2. Ez a második adatkészlet (**Food_Inspections2.csv**) a fürthöz társított alapértelmezett tárolótárolóban található.

1. Futtassa a következő kódot egy új dataframe, **előrejelzésekDf,** amely tartalmazza a modell által generált előrejelzést. A kódrészlet is létrehoz egy ideiglenes tábla nevű **előrejelzések** az adatkeret alapján.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    A következő hez hasonló kimenetnek kell lennie:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Nézd meg az egyik jóslatot. Futtassa ezt a kódrészletet:

    ```PySpark
    predictionsDf.take(1)
    ```

   Van egy előrejelzés a tesztadatkészlet első bejegyzésére.

1. A `model.transform()` módszer ugyanazt az átalakítást alkalmazza az azonos sémával rendelkező új adatokra, és előrejelzést ad az adatok besorolásának módjáról. Meg tudod csinálni néhány statisztikát, hogy egy értelemben, hogy az előrejelzések voltak:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    A kimenet a következő szövegnek tűnik:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Logisztikai regresszió használata a Spark segítségével modellként szolgál a szabálysértések angol nyelvű leírások közötti kapcsolat. És hogy egy adott vállalkozás átmegy-e vagy megbukik-e egy élelmiszer-ellenőrzésen.

## <a name="create-a-visual-representation-of-the-prediction"></a>Az előrejelzés vizuális ábrázolásának létrehozása

Most már létrehozhat egy végső vizualizációt, hogy segítsen a teszt eredményeinek megismerésében.

1. Először a különböző előrejelzések és az eredmények az **előrejelzések** ideiglenes tábla korábban létrehozott. A következő lekérdezések *true_positive*, *false_positive*, *true_negative*és false_negative a kimenetet *választják el.* Az alábbi lekérdezésekben kikapcsolhatja a `-q` vizualizációt a kimenet `-o`használatával (a használatával) adatkeretként, amelyet aztán a `%%local` mágiával lehet használni.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Végül a következő kódrészlettel hozza létre a telket a **Matplotlib**használatával.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    A következő kimenetnek kell megjelennie:

    ![Spark gépi tanulási alkalmazás kimenete – a sikertelen élelmiszer-ellenőrzések kördiagram-százalékos aránya.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark gépi tanulási eredmény kimenet")

    Ebben a táblázatban a "pozitív" eredmény a sikertelen élelmiszer-vizsgálatra utal, míg a negatív eredmény egy átadott vizsgálatra utal.

## <a name="shut-down-the-notebook"></a>A jegyzetfüzet leállítása

Miután befejezte az alkalmazás futtatását, állítsa le a jegyzetfüzetet az erőforrások felszabadításához. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ez a művelet leállítja és bezárja a notebookot.

## <a name="next-steps"></a>További lépések

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark bi-val: Interaktív adatelemzés a Spark használatával a HDInsightban üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
* [Apache Spark gépi tanulással: Használja a Sparkot a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight eszközök beépülő moduljával távolról debugelje az Apache Spark alkalmazásokat](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsighton](apache-spark-zeppelin-notebook.md)
* [A Jupyter notebookhoz elérhető kernelek az Apache Spark-fürtHDInsighthoz](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
