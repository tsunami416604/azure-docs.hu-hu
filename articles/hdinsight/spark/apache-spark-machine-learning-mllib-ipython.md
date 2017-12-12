---
title: "Machine learning például a hdinsight - Azure Spark MLlib |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy machine learning-alkalmazást, amely elemzi a DataSet adatkészlet besorolásával keresztül logisztikai regresszió Spark MLlib segítségével."
keywords: "Spark gépi tanulási, spark machine learning – példa"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c0fd4baa-946d-4e03-ad2c-a03491bd90c8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: f98659081b991d403b6477196042c6ff3d40bb12
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="use-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Spark MLlib segítségével a machine learning-alkalmazás létrehozása és elemzése a DataSet adatkészlet

Használata Spark **MLlib** machine learning-alkalmazás egy megnyitott adatkészlethez egyszerű prediktív elemzési létrehozásához. A Spark beépített gépi tanulási szalagtárak, a példában az *besorolás* logisztikai regresszió keresztül. 

> [!TIP]
> Ebben a példában, Ön által létrehozott hdinsight (Linux) Spark-fürtön Jupyter notebook is érhető el. A notebook élmény lehetővé teszi a Python kódtöredékek futtassa a notebook magát. Hajtsa végre az oktatóprogram a notebook belül, Spark-fürt létrehozása, és indítsa el a Jupyter notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`). Ezután futtassa a notebook **Spark Machine Learning - étele ellenőrző adatok MLlib.ipynb a prediktív elemzési** alatt a **Python** mappát.
>
>

MLlib egy Spark Alapkönyvtár, amely számos hasznos segédprogramokat biztosít gépi tanulási feladatok, beleértve a segédprogramok:

* Besorolás
* Regressziós
* Fürtözés
* A témakör modellezési
* Szinguláris érték felbontás ellen (SVD) és egyszerű összetevő elemzés (PEM)
* Alapul, tesztelése és minta számításához

## <a name="what-are-classification-and-logistic-regression"></a>Mik azok a besorolás és logisztikai regresszió?
*Besorolási*népszerű Machine learning-feladat, az a folyamat a bemeneti adatok rendezése kategóriákba. A feladat is található egy osztályozó algoritmus hozzárendelése "címkék" adatokat, hogy a felhasználótól, hogy legyen. Sikerült a például a gépi tanulási algoritmus, amely fogadja bemeneti adatként készlet információkat, és elosztja a készlet két kategóriába sorolhatók véleménye: kell értékesítő készletek és a készletek, amelyek kell tartania.

Logisztikai regresszió a besorolási használt algoritmus. A Spark logisztikai regresszió API akkor hasznos, ha *bináris osztályozási*, vagy egy két bemeneti adatok besorolása. További információ a logisztikai regresszió: [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Összefoglalva, a logisztikai regresszió illesztése egy *logisztikai függvény* , amely használható a valószínűsége annak, hogy egy bemeneti vektort tartozik egy csoport vagy egyéb előre jelezni.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>A prediktív elemzés példa étele ellenőrző adatok
Ebben a példában használható Spark néhány prediktív elemzési étele ellenőrző adatokon (**Food_Inspections1.csv**) keresztül szerzett része, amely a [város a Chicagói adat portált](https://data.cityofchicago.org/). Ez az adatkészlet chicagóban, beleértve az egyes létrehozását, a szabálytalanság található (ha van ilyen) és az ellenőrzés eredményét végezték étele kialakítása ellenőrzésekre információkat tartalmaz. Az adatok CSV-fájl már megtalálható a fürthöz rendelt tárolási fiók **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Az alábbi lépéseket, a most kialakított egy modell megtekintéséhez, mi tart, vagy a étele ellenőrzése sikertelen.

## <a name="start-building-a-spark-mmlib-machine-learning-app"></a>Indítsa el a Spark MMLib machine learning-alkalmazás létrehozása
1. Az [Azure portál](https://portal.azure.com/) kezdőpultján kattintson a Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a következő helyről is megkeresheti: **Browse All (Összes tallózása)** > **HDInsight Clusters** (HDInsight-fürtök).   
1. A Spark-fürt panelén kattintson a **Fürt irányítópultja Dashboard** lehetőségre, majd a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

   > [!NOTE]
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
1. Hozzon létre egy notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.

    ![A Jupyter notebook létrehozása](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-create-jupyter.png "egy új Jupyter notebook létrehozása")
1. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. A felső részen kattintson a notebook nevére, és adjon meg egy könnyen megjegyezhető nevet.

    ![Adjon nevet a notebooknak](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-name-jupyter.png "Adjon nevet a notebooknak")
1. Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. A machine learning alkalmazás ehhez a forgatókönyvhöz szükséges típusok importálása felépítése elindíthatja. Ehhez az szükséges, vigye a kurzort a cella, és nyomja le az **SHIFT + ENTER**.

        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        from pyspark.sql.functions import UserDefinedFunction
        from pyspark.sql.types import *

## <a name="construct-an-input-dataframe"></a>Egy bemeneti dataframe szerkezet
Használhatunk `sqlContext` csatlakoztatva átalakításokat hajthattak végre a strukturált adatok. Az első feladata a mintaadatok betöltése ((**Food_Inspections1.csv**)) egy Spark SQL *dataframe*.

1. Mivel a nyers adatok CSV formátumban, igazolnia kell a Spark-környezet használata való lekérésére a fájl minden sora a memóriába strukturálatlan szövegként; Ezt követően használja a Python a fürt megosztott kötetei szolgáltatás könyvtár minden sort külön-külön elemzése.

        def csvParse(s):
            import csv
            from StringIO import StringIO
            sio = StringIO(s)
            value = csv.reader(sio).next()
            sio.close()
            return value

        inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                        .map(csvParse)
1. Most már tudunk a CSV-fájlt, egy RDD.  Szeretné megtudni, az adatok a séma, nem beolvasni egy sort a RDD.

        inspections.take(1)

    A következőhöz hasonló kimenetnek kell megjelennie:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

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
1. Az előző kimeneti biztosítanak számunkra a bemeneti fájl séma képet. Ez magában foglalja a minden létrehozását, a kialakítása, a címet, és az ellenőrzések, a helyre, többek között az adatok nevét. Néhány oszlopot, amely a prediktív elemzési során hasznosak, és csoportosítsa az eredményeket egy dataframe, amely majd használatával hozzon létre egy ideiglenes táblát, most kiválasztása

        schema = StructType([
        StructField("id", IntegerType(), False),
        StructField("name", StringType(), False),
        StructField("results", StringType(), False),
        StructField("violations", StringType(), True)])

        df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
        df.registerTempTable('CountResults')
1. Most már rendelkezik egy *dataframe*, `df` amelyen most a elemzés elvégezni. Egy ideiglenes tábla hívás is tudunk **CountResults**. Azt a dataframe jelentőséggel négy oszlop szerepel: **azonosító**, **neve**, **eredmények**, és **megsértésének**.

    Folytassuk az adatok mintát:

        df.show(5)

    A következőhöz hasonló kimenetnek kell megjelennie:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +------+--------------------+-------+--------------------+
        |    id|                name|results|          violations|
        +------+--------------------+-------+--------------------+
        |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
        |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
        |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
        |413708|BENCHMARK HOSPITA...|   Pass|                    |
        |413722|           JJ BURGER|   Pass|                    |
        +------+--------------------+-------+--------------------+

## <a name="understand-the-data"></a>Az adatok ismertetése
1. Kezdjük, hogy az az adatkészletet tartalmaz. Például a különböző értékeket Mik a **eredmények** oszlop?

        df.select('results').distinct().show()

    A következőhöz hasonló kimenetnek kell megjelennie:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +--------------------+
        |             results|
        +--------------------+
        |                Fail|
        |Business Not Located|
        |                Pass|
        |  Pass w/ Conditions|
        |     Out of Business|
        +--------------------+
1. Gyors képi megjelenítés segíthet OK információ ezen eredmények a terjesztéséről. Egy ideiglenes tábla az adatok már tudunk **CountResults**. A következő SQL-lekérdezés futtatható jobb megértése, hogyan jutnak el az eredmények eléréséhez a táblázaton.

        %%sql -o countResultsdf
        SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

    A `%%sql` magic követ `-o countResultsdf` biztosítja, hogy a lekérdezés kimenetét a Jupyter kiszolgálón (általában a fürt headnode) helyileg megőrződjenek. A kimeneti tárolva a [Pandas](http://pandas.pydata.org/) a megadott nevű dataframe **countResultsdf**.

    A következőhöz hasonló kimenetnek kell megjelennie:

    ![SQL-lekérdezés kimeneti](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL-lekérdezés kimenete")

    A `%%sql` funkcióval, illetve a PySpark kernellel elérhető egyéb funkciókkal kapcsolatos további információkat [A Spark HDInsight-fürtökkel használt Jupyter notebookokban elérhető kernelek](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic) című részben talál.
1. Matplotlib, a szalagtár segítségével hozza létre az adatok, a képi megjelenítés segítségével rajzot. Mert a rajzolási létre kell hozni a helyileg tárolt **countResultsdf** dataframe, a kódrészletet a következővel kell kezdődnie az `%%local` magic. Ez biztosítja, hogy a kód a Jupyter kiszolgálón helyileg futnak.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt

        labels = countResultsdf['results']
        sizes = countResultsdf['cnt']
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    A következőhöz hasonló kimenetnek kell megjelennie:

    ![Spark gépi tanulási a alkalmazás kimeneti - öt különböző vizsgálat eredményekkel kördiagram](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Spark gépi tanulási a eredmény kimenete")
1. Láthatja, hogy vannak-e 5 különböző eredményeket, amelyeken az ellenőrzés:

   * Nem található üzleti
   * Sikertelen
   * Fázis
   * Feltételek keresztüli rendelkező PSS
   * Üzleti kívül

     Ossza meg velünk fejlesztése modell, amely kitalálni a egy étele ellenőrző eredményeit a megsértésének megadott. Mivel logisztikai regresszió egy bináris osztályozási módszer, így az adatok csoportosításához két kategóriába sorolhatók: **sikertelen** és **átadni**. Egy "átadni keresztüli rendelkező feltételek" még nem állt le a hozzáférési, ha azt a modell betanítását, javasolt a két eredményt egyenértékű. Az eredmények ("Üzleti nem található" vagy "Out üzleti") adatok, hogy távolítsa el azokat a gyakorlókészlethez nem hasznosak. Ez kell lennie rendben, mivel ezek két kategóriába mégis jött létre az eredmények nagyon kis százalékát.
1. Ossza meg velünk lépjen tovább, és alakítsa át a meglévő dataframe (`df`) egy új dataframe, ahol minden egyes ellenőrzés címke-megsértésének párban szerepel-e be. Ebben az esetben a címke `0.0` jelenti. a hiba, a címkék `1.0` sikeres, és a címke jelöli `-1.0` egyes eredményeket e két mellett jelöli. A Microsoft kiszűrhetők az egyéb eredmények az új adatok keret számításakor.

        def labelForResults(s):
            if s == 'Fail':
                return 0.0
            elif s == 'Pass w/ Conditions' or s == 'Pass':
                return 1.0
            else:
                return -1.0
        label = UserDefinedFunction(labelForResults, DoubleType())
        labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')

    Milyen a címkézett adatok tűnik, hogy most egy olyan sor beolvasása.

        labeledData.take(1)

    A következőhöz hasonló kimenetnek kell megjelennie:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>A bemeneti dataframe logisztikai regressziós modell létrehozása
A végső feladata a teljesítményadatokká alakítani a címkézett logisztikai regresszió által elemezhető formátumú. A bemeneti logisztikai regresszió algoritmushoz kell lenniük, amelyek *címke-funkció vektoros párok*, ahol a "szolgáltatás"vektor a bemeneti pontot jelölő számok vektor. Igen igazolnia kell alakítani az "megsértésének", amely félig strukturált és a szabad szöveges, a gép könnyen értelmezhető formára valós szám tömbjének sok megjegyzéseket tartalmaz.

Tanulási megközelítés természetes nyelvű feldolgozása egy szabványos számítógép, hogy minden distinct szó hozzárendelése egy "index", és akkor továbbítja a vektoros a gépi tanulási algoritmus úgy, hogy minden egyes súgóindex-értéket tartalmaz a relatív gyakoriságot karakterláncban szó.

MLlib leegyszerűsíti a művelet elvégzéséhez. Első lépésként "tokenize" minden megsértésének karakterlánc beolvasása az egyes szavak minden karakterlánc. Ezt követően egy `HashingTF` jogkivonatok minden készlete alakítani egy szolgáltatás vektort, majd adhatók át a logisztikai regresszió algoritmus a modell összeállításához. Azt végezze el az összes lépés sorrendben használja a "futószalag".

    tokenizer = Tokenizer(inputCol="violations", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

    model = pipeline.fit(labeledData)

## <a name="evaluate-the-model-on-a-separate-test-dataset"></a>Értékelje ki a modellt a külön tesztelési adatkészletre
A korábban létrehozott modell használhatjuk *előrejelzése* mely új ellenőrzések eredményeinek kell, a megfigyelt szabálysértések alapján. Ebben a modellben az adatkészlettel betanítása azt **Food_Inspections1.csv**. Ossza meg velünk használjon egy második dataset **Food_Inspections2.csv**található *kiértékelése* ebben a modellben az új adatok erősségével. A második készlet (**Food_Inspections2.csv**) már a fürthöz tartozó alapértelmezett tárolót kell megadni.

1. A következő kódrészletet hoz létre egy új dataframe **predictionsDf** , amely tartalmazza a modell által generált előrejelzését. A kódrészletben is létrehoz egy ideiglenes táblát **előrejelzéseket** a dataframe alapján.

        testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                 .map(csvParse) \
                 .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
        testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
        predictionsDf = model.transform(testDf)
        predictionsDf.registerTempTable('Predictions')
        predictionsDf.columns

    A következőhöz hasonló kimenetnek kell megjelennie:

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
1. Tekintse meg az előrejelzés egyikét. Futtassa ezt a kódrészletet:

        predictionsDf.take(1)

   Nincs olyan előrejelzésre lévő teszt az első bejegyzés.
1. A `model.transform()` metódus ugyanazt az átalakítást vonatkozik semmilyen új adatot az azonos sémával, és hogyan adatok előrejelzése érkeznek. Hogyan pontos volt az előrejelzéseket, hogy néhány egyszerű statisztikai tehetünk ennek:

        numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                              (prediction = 1 AND (results = 'Pass' OR
                                                                   results = 'Pass w/ Conditions'))""").count()
        numInspections = predictionsDf.count()

        print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
        print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

    A kimenet a következőképpen néznek:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There were 9315 inspections and there were 8087 successful predictions
        This is a 86.8169618894% success rate

    Logisztikai regresszió használata Spark lehetőséget ad olyan pontos modell megsértésének leírását angol nyelven, és hogy egy adott üzleti volna továbbítja, vagy a étele ellenőrzése sikertelen közötti kapcsolat.

## <a name="create-a-visual-representation-of-the-prediction"></a>Az előrejelzés vizuális ábrázolását létrehozása
A Microsoft hogyan most hozhat létre és segítsen végső képi megjelenítés OK Ez a vizsgálat eredményei.

1. Először a különböző előrejelzéseket és az eredmények kivonja a a **előrejelzéseket** korábban létrehozott ideiglenes tábla. A következő lekérdezések külön a kimeneti adatok *true_positive*, *false_positive*, *true_negative*, és *false_negative*. Az alábbi lekérdezésekben azt kapcsolja ki a képi megjelenítés használatával `-q` , és a kimeneti (használatával `-o`), majd használható a dataframes a `%%local` magic.

        %%sql -q -o true_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

        %%sql -q -o false_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

        %%sql -q -o true_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

        %%sql -q -o false_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
1. Végezetül a következő kódrészletet használja a rajzolási történő létrehozásához **Matplotlib**.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt

        labels = ['True positive', 'False positive', 'True negative', 'False negative']
        sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    A következő kimenetet kell megjelennie:

    ![Spark gépi tanulási alkalmazás kimeneti - Kördiagram százalékos sikertelen étele ellenőrzések. ] (./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark gépi tanulási a eredmény kimenete")

    Ezen a diagramon "pozitív" hivatkozik sikertelen étele ellenőrző negatív eredmény hivatkozik a átadott ellenőrzése közben.

## <a name="shut-down-the-notebook"></a>Állítsa le a notebook
Miután befejezte az alkalmazás fut, állítsa le a notebook az erőforrások kijelölése. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ezzel leállítja és bezárja a notebookot.

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark Streaming: A Spark on HDInsight használata valós idejű streamelési alkalmazások összeállítására](apache-spark-eventhub-streaming.md)
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
