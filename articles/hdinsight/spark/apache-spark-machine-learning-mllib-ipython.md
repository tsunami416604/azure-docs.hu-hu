---
title: A Spark MLlib a HDInsight - Azure Machine learning példa
description: Megtudhatja, hogyan hozhat létre egy machine learning-alkalmazást, amely elemzi a logisztikai regressziós keresztül a fájlbesorolás segítségével adatkészlet a Spark MLlib segítségével.
keywords: a Spark a machine learning, a spark machine learning-példa
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: jasonh
ms.openlocfilehash: 068d5ee7200f9597da11c0a2850ef4941f4ea900
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619532"
---
# <a name="use-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Spark MLlib segítségével hozhat létre a machine learning-alkalmazás, és a egy adatkészlet elemzése

Ismerje meg, hogyan használható a Spark [MLlib](https://spark.apache.org/mllib/) machine learning-alkalmazás egyszerű prediktív elemzéseket végezhet egy megnyitott adatkészlet létrehozásához. A Spark a beépített gépi tanulás kódtárakat, ez a példa *besorolási* logisztikai regressziós keresztül. 

> [!TIP]
> Ebben a példában is egy Jupyter notebookot HDInsight szolgáltatásban létrehozott (Linux) Spark-fürtön elérhető. A jegyzetfüzet-megoldás lehetővé teszi a notebookból magát a Python-kódrészletek futtatását. Kövesse az oktatóanyag a jegyzetfüzet belül, hozzon létre egy Spark-fürtöt, majd indítsa el a Jupyter notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`). Ezután futtassa a notebook **Spark Machine Learning - prediktív elemzés a MLlib.ipynb használata az élelmiszervizsgálati ellenőrző adatok** alatt a **Python** mappát.
>
>

MLlib egy Spark Alapkönyvtár, amely számos hasznos segédprogramokat biztosít a machine learning feladatokat, beleértve a megfelelő segédprogramok:

* Besorolás
* Regresszió
* Fürtözés
* A témakör modellezés
* Egyetlen érték idősorfelbontási (SVD) és egyszerű összetevő elemzés (PEM)
* Tesztelés és minta statisztikák kiszámításához elmélet

## <a name="understand-classification-and-logistic-regression"></a>Besorolás és a logisztikai regressziós ismertetése
*Besorolási*közkedvelt Machine learning-feladat kategóriákba bemeneti Adatrendezés során a rendszer. A feladat egy osztályozó algoritmus, döntse el, a bemeneti adatok Ön által megadott "címkék" hozzárendelése a. Ha például sikerült gondol, egy gépi tanulási algoritmus, amely fogadja bemeneti adatként tőzsdei információkat, és elosztja a készlet két kategóriába sorolhatók: kell árusító készletek és a készletek, amelyek kell tartania.

Logisztikai regressziós a besorolási használt algoritmus. A Spark a logisztikai regressziós API akkor hasznos, ha *bináris osztályozási*, vagy egy két csoporthoz bemeneti adatok besorolása. További információ a logisztikai regressziót: [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Összefoglalva, a logisztikai regressziós illesztése egy *logisztikai függvény* , amely előre jelezni a valószínűsége annak, hogy egy bemeneti vektor tartozik egy csoport vagy a másik használható.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Prediktív elemzés példa az élelmiszer-ellenőrzési adatok
Ebben a példában a prediktív elemzést élelmiszer-ellenőrzési adatokon végrehajtandó használhatja a Spark (**Food_Inspections1.csv**), amely keresztül szerezték be a [város, Chicagói adatportálon](https://data.cityofchicago.org/). Ez az adatkészlet food létesítmény ellenőrzések, Chicago, többek között az egyes létrehozásáról, a szabálysértések található (ha van ilyen) és az ellenőrzés eredményét a végezték kapcsolatos információt tartalmazza. A CSV adatfájl már elérhető a fürthöz társított tárfiókban **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Az alábbi lépéseket a megtekintéséhez, hogy mire van szüksége, vagy sikertelen egy élelmiszer-vizsgálati modell fejlesztése.

## <a name="create-a-spark-mllib-machine-learning-app"></a>Spark MLlib machine learning-alkalmazás létrehozása

1. Hozzon létre egy Jupyter notebookot a PySpark-kernellel. Az utasításokért lásd: [Jupyter notebook létrehozása](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Ehhez az alkalmazáshoz szükséges típusok importálása. Másolja és illessze be a következő kódot egy üres cellába, majd nyomja le **SHIRT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    A PySpark kernel miatt nem kell explicit módon semmilyen tartalmat létrehozásához. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. 

## <a name="construct-the-input-dataframe"></a>A bemeneti dataframe szerkezet

Mivel a nyers adatok CSV formátumban, a Spark környezet használatával kérje le a fájlt a memóriába, a strukturálatlan szöveg, és a Python a CSV-kódtár használatával minden egyes sor az adatok elemzése.

1. Futtassa a következő sorokat hozzon létre egy rugalmas elosztott adatkészlet (RDD) importálása és a bemeneti adatok elemzésekor.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value
    
    inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Futtassa a következő kódot egy sor lekérése az RDD, így egy pillantást az adatok séma:

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

    A kimenet a séma a bemeneti fájl ötlet biztosítja. Ez magában foglalja a nevét, minden létesítmény létrehozásáról, a címet, és az ellenőrzések a helyre, többek között az adatok típusát. 

3. Futtassa a következő kódot, hogy hozzon létre dataframe-(*df*) és a egy ideiglenes táblát (*CountResults*) néhány oszlopokat, amelyek hasznosak a prediktív elemzés céljából. `sqlContext` strukturált adatok átalakításokat szolgál. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Az adathalmaz jelentőséggel négy oszlop **azonosító**, **neve**, **eredmények**, és **szabálysértések**.

4. Futtassa a mintát az adatok beolvasása a következő kódot:

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

Kezdjük a megtapasztalhatja, az adatkészlet tartalmaz. 

1. Futtassa a következő kódot az eltérő értékeket megjelenítéséhez a **eredmények** oszlopban:

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

2. Futtassa a következő kódot az eredményeket a terjesztési megjelenítése:

    ```PySpark
    %%sql -o countResultsdf
    SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results
    ```

    A `%%sql` Magic Quadrant követ `-o countResultsdf` biztosítja, hogy a lekérdezés kimenete a Jupyter-kiszolgálón (általában a fürt átjárócsomópontjával) helyileg tárolja. A kimenet a megőrzés pedig egy [Pandas](http://pandas.pydata.org/) a megadott nevű adathalmaz **countResultsdf**. A `%%sql` funkcióval, illetve a PySpark kernellel elérhető egyéb funkciókkal kapcsolatos további információkat [A Spark HDInsight-fürtökkel használt Jupyter notebookokban elérhető kernelek](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic) című részben talál.

    A kimenet a következő:

    ![SQL-lekérdezés kimenete](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL-lekérdezés kimenete")


3. Is [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), egy kódtár segítségével hozza létre a vizualizációt az adatok, létrehozhat egy rajzot. Az ábrázolást kell létrehozni, mert a megőrzött helyileg **countResultsdf** adathalmaz, a kódtöredék a következővel kell kezdődnie az `%%local` Magic Quadrant. Ez biztosítja, hogy a kód a Jupyter-kiszolgálón helyben futtatja.

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

    A kimenet a következő:

    ![A Spark machine learning-alkalmazás kimenete – a tortadiagram az öt különböző eredmények](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Spark machine learning-eredmény kimeneti")

    Nincsenek 5 különböző eredmények, amelyeken az ellenőrzés:

    - Nem találhatók üzleti
    - Sikertelen
    - Sikeres
    - Feltételek használatával adja át
    - Üzleti kívül

    Előre jelezni egy food ellenőrzési serkenti az eredményt, a szabálysértések alapuló modell fejlesztéshez szükséges. Mivel a logisztikai regressziós egy bináris osztályozási metódust, logikus csoportosítása az eredményadatok két kategóriába sorolhatók: **sikertelen** és **átadni**:

    - Sikeres
        - Sikeres
        - Feltételek használatával adja át
    - Sikertelen
        - Sikertelen
    - Elvetés
        - Nem találhatók üzleti
        - Üzleti kívül

    Az eredmények ("Üzleti nem található" vagy "kívüli üzleti") az adatok nem hasznos, és ezek alkotják az eredmények nagyon kis százalékát ennek ellenére.

4. Futtassa a következő kódot a meglévő dataframe átalakítása (`df`) egy új, ahol minden egyes ellenőrzés jelenik meg egy címkét-szabálysértések pár dataframe-be. Az ebben az esetben címkét, `0.0` hiba, a címke jelöli `1.0` sikeres és a címkét, `-1.0` egyes eredményeket e két mellett jelöli. 

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

5. Futtassa a következő kódot a megcímkézett egy adatsornak megjelenítése:

    ```PySpark
    labeledData.take(1)
    ```

    A kimenet a következő:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>A bemeneti adathalmazból lépéseit egy logisztikai regressziós modell létrehozása

A végső feladat, hogy olyan formátumra, amely a logisztikai regressziós elemezhetők a címkézett adatokkal konvertálni. A bemenet egy logisztikai regressziós algoritmusával kell lenniük, amelyek *-funkció vektor párok*, ahol a "szolgáltatás"vektor számokat a szövegbeviteli pontot jelölő vektor. Tehát konvertálnia kell a "szabálysértések" oszlop, amely részben strukturált, és a szabad szöveges, tömbként lesz valós számmá, amely egy gépen sikerült egyszerűen átláthatja, hogy sok megjegyzéseket tartalmaz.

Egy standard szintű gépi megközelítés természetes nyelvi feldolgozás céljából, hogy minden distinct szó egy "index" hozzárendelni, és a egy vektoros majd át a gépi tanulási algoritmus úgy, hogy minden egyes indexértéket tartalmaz relatív gyakoriságát, hogy a Word, a szöveges karakterláncban.

MLlib végrehajtani a műveletet egy egyszerűbb megoldást kínál. Első lépésként "tokenize" az egyes szavak beolvasni az egyes karakterláncokban minden szabálysértések karakterláncot. Ezután használja a `HashingTF` jogkivonatok minden készlete alakítható át egy funkció vektor, amely majd adható át a logisztikai regressziós algoritmus a modell létrehozásához. Ezen lépések mindegyike használatával "folyamat" sorrendben végez.

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Egy másik adatkészlet használatakor a modell értékelése

Használhatja a korábban létrehozott modell *előrejelzése* mi új ellenőrzések eredményét lesz, a megsértések, amelyek a megfigyelt alapján. Ez a modell az adatkészlet betanított **Food_Inspections1.csv**. Használhat egy második adatkészlet **Food_Inspections2.csv**, az *kiértékelése* az új adatokat az ebben a modellben erőssége. A második készlet (**Food_Inspections2.csv**) szerepel a fürthöz társított alapértelmezett tároló.

1. Futtassa a következő kódot egy új adathalmaz létrehozásához **predictionsDf** , amely tartalmazza az előrejelzést, a modell által generált. A kódrészlet is létrehoz egy ideiglenes táblát nevű **előrejelzéseket** az adathalmaz alapján.

    ```PySpark
    testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ```
    # -----------------
    # THIS IS AN OUTPUT
    # -----------------

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

1. Tekintse meg az előrejelzés egyikét. Ez a kódrészlet futtatása:

    ```PySpark
    predictionsDf.take(1)
    ```

   Az első bejegyzés a test-adatkészlet előrejelzési van.
1. A `model.transform()` módszer ugyanazt az átalakítást érvényes ugyanazzal a sémával rendelkező új adatokat és hogyan lehet az adatok besorolását előrejelzési érkeznek. Néhány egyszerű statisztikai, megtapasztalhatja, milyen pontos az előrejelzés is teheti:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    A kimenet az alábbihoz hasonló:

    ```
    # -----------------
    # THIS IS AN OUTPUT
    # -----------------

    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    A Spark használatával a logisztikai regressziós biztosít angol nyelven szabálysértések leírásokat és a egy adott üzleti lenne át akár egy élelmiszer-ellenőrzés sikertelen közötti kapcsolat egy pontos modell.

## <a name="create-a-visual-representation-of-the-prediction"></a>Hozzon létre egy vizuális megjelenítését, az előrejelzési
Most már összeállíthatja a végleges Vizualizáció segítségével, az okból a teszt eredményei.

1. Indítsa el a különböző előrejelzéseket és az eredmények a a **előrejelzéseket** korábban létrehozott ideiglenes táblát. A következő lekérdezéseket külön a kimenetben: *true_positive*, *false_positive*, *true_negative*, és *false_negative*. Az alábbi lekérdezésekhez kikapcsolása képi megjelenítés használatával `-q` , és a kimenet (használatával `-o`), majd használható a dataframes a `%%local` Magic Quadrant.

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

1. Végezetül a következő kódrészlet használatával létrehozhat egy a diagram használatával **Matplotlib**.

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

    ![A Spark machine learning-alkalmazás kimenete – diagram százalékos sikertelen élelmiszer-ellenőrzések. ] (./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark machine learning-eredmény kimeneti")

    Ezen a diagramon "pozitív" eredmény hivatkozik a sikertelen élelmiszer-forgalmat, amíg a negatív eredmény hivatkozik egy sikeres ellenőrzés.

## <a name="shut-down-the-notebook"></a>Állítsa le a notebookot
Miután befejezte az alkalmazás fut, állítsa le a notebookot az erőforrások felszabadítása érdekében. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ezzel leállítja és bezárja a notebookot.

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
