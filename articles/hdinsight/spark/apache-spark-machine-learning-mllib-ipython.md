---
title: Gépi tanulási példa a Spark MLlib a HDInsight-ben – Azure
description: Ismerje meg, hogy a Spark MLlib használatával hogyan hozhat létre egy olyan gépi tanulási alkalmazást, amely az osztályozást a logisztikai regresszió használatával elemzi.
keywords: Spark Machine learning, Spark Machine learning – példa
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: c8ead7abc454df387db31b2ce65d2ba714b0067d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494085"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Gépi tanulási alkalmazások készítése és adatkészletek elemzése Apache Spark MLlib használatával

Megtudhatja, hogyan hozhat létre Apache Spark [MLlib](https://spark.apache.org/mllib/) egy gépi tanulási alkalmazás létrehozásához egy nyitott adatkészlet egyszerű prediktív elemzéséhez. A Spark beépített gépi tanulási könyvtáraiból a példa a logisztikai regresszión keresztüli *besorolást* használja. 

A MLlib egy alapszintű Spark-könyvtár, amely számos segédprogramot biztosít a gépi tanulási feladatokhoz, beleértve a következőkre alkalmas segédprogramokat:

* Osztályozás
* Regressziós
* Fürtszolgáltatás
* Témakör modellezése
* Egyrészes értékek elbomlása (SVD) és a fő összetevők elemzése (PEM)
* A hipotézis tesztelése és a minta statisztikáinak kiszámítása

## <a name="understand-classification-and-logistic-regression"></a>A besorolás és a logisztikai regresszió ismertetése
Az *osztályozás*, amely egy népszerű gépi tanulási feladat, a bemeneti adatok kategóriákba rendezésének folyamata. A besorolási algoritmus feladata, hogy kiderítse, hogyan rendeljen hozzá "címkéket" a megadott adatokhoz. Tegyük fel például, hogy egy gépi tanulási algoritmus, amely adatokat fogad el bemenetként, és két kategóriába osztja el az állományt: a készleteket, amelyeket érdemes értékesíteni és készleteket tárolni.

A logisztikai regresszió a besoroláshoz használt algoritmus. A Spark logisztikai regressziós API-ját *bináris besoroláshoz*vagy a bemeneti adatok két csoportba való besorolásához lehet hasznos. További információ a logisztikai regressziókkal kapcsolatban: [wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Összefoglalva, a logisztikai regressziós folyamat egy *logisztikai függvényt* hoz létre, amellyel előre megjósolható, hogy egy bemeneti vektor egy csoportba vagy a másikba tartozik-e.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Prediktív elemzési példa az élelmiszer-ellenőrzési adataira
Ebben a példában a Spark segítségével elvégezheti a [Chicago adatportálon](https://data.cityofchicago.org/)keresztül beszerzett élelmiszer-ellenőrzési adatok (**Food_Inspections1. csv**) prediktív elemzését. Ez az adatkészlet a Chicago-ban végrehajtott élelmiszer-létesítési vizsgálatokról tartalmaz információkat, beleértve az egyes létesítményekkel kapcsolatos információkat, a megtalált szabálysértéseket (ha vannak ilyenek) és a vizsgálat eredményeit. A CSV-adatfájl már elérhető a fürthöz a **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**-ben társított Storage-fiókban.

Az alábbi lépésekben egy modellt fejleszt ki, amelyből megtudhatja, mit kell tennie az élelmiszer-ellenőrzés elvégzéséhez vagy elutasításához.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Apache Spark MLlib Machine learning-alkalmazás létrehozása

1. Hozzon létre egy Jupyter notebookot a PySpark-kernellel. Az utasításokért lásd: [Jupyter notebook létrehozása](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importálja az alkalmazáshoz szükséges típusokat. Másolja és illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER**billentyűkombinációt.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    A PySpark kernel miatt nem kell explicit módon létrehoznia a környezeteket. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. 

## <a name="construct-the-input-dataframe"></a>A bemeneti dataframe felépítése

Mivel a nyers adat CSV formátumú, a Spark kontextus használatával a fájl a memóriában strukturálatlan szövegként hívható le, majd a Python CSV-könyvtára segítségével elemezheti az egyes vonalakat.

1. A következő sorok futtatásával hozzon létre egy rugalmasan elosztott adatkészletet (RDD) a bemeneti adatok importálásával és elemzésével.

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

2. Futtassa a következő kódot egy sor RDD való lekéréséhez, így az adatsémát is megtekintheti:

    ```PySpark
    inspections.take(1)
    ```

    A kimenet a következőket eredményezi:

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

    A kimenet a bemeneti fájl sémájának egy ötletét adja meg. Magában foglalja az összes létesítmény nevét, a létesítmény típusát, a címeit, az ellenőrzések és a hely helyét, egyebek között. 

3. A következő kód futtatásával hozzon létre egy dataframe (*DF*) és egy ideiglenes táblát (*CountResults*), és néhány oszlopot, amelyek hasznosak a prediktív elemzéshez. a `sqlContext` a strukturált adatok átalakításának elvégzésére szolgál. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    A dataframe szereplő négy oszlop az **azonosító**, a **név**, az **eredmények**és a **szabálysértés**.

4. A következő kód futtatásával szerezzen be egy kis mintát az adathoz:

    ```PySpark
    df.show(5)
    ```

    A kimenet a következőket eredményezi:

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

## <a name="understand-the-data"></a>Az adatgyűjtés ismertetése

Kezdjük azzal, hogy az adatkészlet mit tartalmaz. 

1. A következő kód futtatásával jelenítse meg az **eredmények** oszlopban szereplő különböző értékeket:

    ```PySpark
    df.select('results').distinct().show()
    ```

    A kimenet a következőket eredményezi:

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

2. A következő kód futtatásával jelenítheti meg az eredmények eloszlását:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    A `%%sql` Magic ezt követően `-o countResultsdf` biztosítja, hogy a lekérdezés kimenete helyileg megmaradjon a Jupyter-kiszolgálón (általában a fürt átjárócsomóponthoz). A kimenet a megadott nevű **CountResultsdf** [pandák](https://pandas.pydata.org/) dataframe marad. További információ a `%%sql` magicról és a PySpark kernelhez elérhető egyéb varázslatokról: a [Jupyter notebookokon elérhető kernelek Apache Spark HDInsight-fürtökkel](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    A kimenet a következőket eredményezi:

    ![SQL-lekérdezés kimenete](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL-lekérdezés kimenete")


3. A [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib)-t, az adatvizualizációk létrehozásához használt könyvtárat is használhatja a mintaterület létrehozásához. Mivel a parcellát a helyileg megőrzött **countResultsdf** -dataframe kell létrehozni, a kódrészletnek a `%%local` varázslattal kell kezdődnie. Ez biztosítja, hogy a kód helyileg fusson a Jupyter-kiszolgálón.

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

    A kimenet a következőket eredményezi:

    ![Spark Machine learning-alkalmazás kimenete – tortadiagram öt különböző vizsgálati eredménnyel](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "A Spark Machine learning eredményének kimenete")

    Az élelmiszer-ellenőrzés eredményeinek előrejelzéséhez ki kell dolgoznia egy modellt a szabálysértések alapján. Mivel a logisztikai regresszió bináris besorolású módszer, érdemes a következő két kategóriába csoportosítani az eredményeket: **Fail** és **pass**:

   - Pass
       - Pass
       - H/feltételek továbbítása
   - Sikertelen
       - Sikertelen
   - Elveti
       - Üzleti nem található
       - Üzleti tevékenység

     A többi eredmény ("üzleti nem található" vagy "üzleti tevékenység") nem hasznos, és az eredmények nagyon kis hányadát is elvégzik.

4. A következő kód futtatásával alakítsa át a meglévő dataframe (`df`) egy olyan új dataframe, amelyben az egyes ellenőrzések címke-megsértési párokként jelennek meg. Ebben az esetben a `0.0` egy címkéje hibát jelez, `1.0` a címkéje sikeresnek jelöli, és a `-1.0` címkéje a kettő mellett néhány eredményt jelöl. 

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

5. A következő kód futtatásával jelenítse meg a címkézett adatfeliratok egy sorát:

    ```PySpark
    labeledData.take(1)
    ```

    A kimenet a következőket eredményezi:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Logisztikai regressziós modell létrehozása a bemeneti dataframe

A végső feladat a címkézett adatok átalakítása olyan formátumra, amelyet a logisztikai regresszió alapján lehet elemezni. A logisztikai regressziós algoritmus bemenetének a *címke-szolgáltatás vektoros párok*készletének kell lennie, ahol a "szolgáltatás vektora" a bemeneti pontot jelképező számok vektora. Ezért át kell alakítania a "szabálysértések" oszlopot, amely félig strukturált, és számos, szabad szöveggel rendelkező megjegyzést tartalmaz a valós számok tömbje számára, amelyeket a gépek könnyedén megértettek.

A természetes nyelv feldolgozásának egyik szabványos gépi tanulási módszere az egyes különálló szavak "index", majd egy vektor átadása a gépi tanulási algoritmusba, hogy az egyes indexek értéke tartalmazza a szó relatív gyakoriságát a szöveges karakterláncban.

A MLlib egyszerű módszert kínál a művelet elvégzésére. Először is "tokenize" minden megsértési karakterláncot, hogy minden egyes karakterláncban beolvassa az egyes szavakat. Ezt követően egy `HashingTF` használatával alakítsa át a tokenek egyes készleteit egy szolgáltatás-vektorba, amely ezután átadható a logisztikai regressziós algoritmusnak a modell létrehozásához. Ezeket a lépéseket a "folyamat" használatával végezheti el.

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Modell kiértékelése egy másik adatkészlet használatával

A korábban létrehozott modellt használva *megjósolhatja* , hogy az új ellenőrzések eredményei milyenek lesznek a megfigyelt szabálysértések alapján. Ezt a modellt a **Food_Inspections1. csv**adatkészlet alapján tanította. A modell erősségének *kiértékeléséhez* használhatja a **Food_Inspections2. csv**második adatkészletet az új adatokat. Ez a második adatkészlet (**Food_Inspections2. csv**) a fürthöz társított alapértelmezett tárolóban található.

1. A következő kód futtatásával hozzon létre egy új dataframe, amely a modell által generált **predictionsDf** tartalmazza. A kódrészlet létrehoz egy, az **előrejelzések** alapján létrehozott ideiglenes táblát is a dataframe alapján.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

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

1. Tekintse meg az egyik előrejelzést. A kódrészlet futtatása:

    ```PySpark
    predictionsDf.take(1)
    ```

   A tesztelési adatkészlet első bejegyzésének előrejelzése.
1. A `model.transform()` módszer ugyanazt az átalakítást alkalmazza minden olyan új adatváltozásra, amely ugyanazzal a sémával rendelkezik, és megérkezik az adatgyűjtés módjára. Néhány egyszerű statisztikával megtudhatja, milyen pontosak voltak a jóslatok:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    A kimenet a következőhöz hasonlóan néz ki:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Ha logisztikai regressziót használ a Sparktal, pontos modellt biztosít a szabálysértési leírások angol nyelven való kapcsolatáról, illetve arról, hogy egy adott vállalkozás átadja-e az élelmiszer-ellenőrzést vagy sem.

## <a name="create-a-visual-representation-of-the-prediction"></a>Az előrejelzés vizuális ábrázolásának létrehozása
Most létrehozhat egy végső vizualizációt, amely segít a teszt eredményeinek indoklásában.

1. Első lépésként Kinyeri a korábban létrehozott **előrejelzési** ideiglenes tábla különböző előrejelzéseit és eredményeit. A következő lekérdezések elkülönítik a kimenetet a *true_positive*, a *false_positive*, a *true_negative*és a *false_negative*. Az alábbi lekérdezésekben kikapcsolhatja a vizualizációt `-q` használatával, és a kimenetet (`-o`használatával) is mentheti a dataframes, amelyet aztán használhat a `%%local` Magic segítségével.

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

1. Végül használja az alábbi kódrészletet a mintaterület **Matplotlib**használatával történő létrehozásához.

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

    ![Spark Machine learning-alkalmazás kimenete – kördiagram százaléka a sikertelen élelmiszer-ellenőrzésekhez.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "A Spark Machine learning eredményének kimenete")

    Ebben a diagramban a "pozitív" eredmény a sikertelen élelmiszer-ellenőrzésre utal, míg a negatív eredmény egy átadott vizsgálatra utal.

## <a name="shut-down-the-notebook"></a>A jegyzetfüzet leállítása
Miután befejezte az alkalmazás futtatását, állítsa le a notebookot az erőforrások felszabadításához. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ezzel leáll, és bezárja a jegyzetfüzetet.

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Alkalmazási helyzetek
* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek Apache Spark-fürtben HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
