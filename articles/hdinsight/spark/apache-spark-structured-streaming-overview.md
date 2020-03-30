---
title: Strukturált streamelés az Azure HDInsightban
description: A Spark strukturált streamelési alkalmazások használata HDInsight Spark-fürtökön.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 19cfd5d8ed4100048c270fb41e5e54a920c61516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75548836"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Az Apache Spark strukturált streamelésének áttekintése

[Apache szikra](https://spark.apache.org/) A strukturált adatfolyam-adatfolyamok lehetővé teszik méretezhető, nagy átviteli sebességű, hibatűrő alkalmazások megvalósítását az adatfolyamok feldolgozásához. Strukturált streamelési épül a Spark SQL-motor, és javítja a konstrukciók a Spark SQL-adatkeretek és adatkészletek, így streamelési lekérdezéseket ugyanúgy írhat, mint a kötegelt lekérdezések írása.  

A strukturált streamelési alkalmazások HDInsight Spark-fürtökön futnak, és az [Apache Kafka](https://kafka.apache.org/)-ból , egy TCP-szoftvercsatornából (hibakeresési célokra), az Azure Storage-ból vagy az Azure Data Lake Storage-ból származó streamelési adatokhoz csatlakoznak. Az utóbbi két lehetőség, amely a külső tárolási szolgáltatásokra támaszkodik, lehetővé teszi a tárolóba felvett új fájlok figyelését, és a tartalmuk feldolgozását úgy, mintha streamelték volna őket.

Strukturált streamelési létrehoz egy hosszú ideig futó lekérdezést, amelynek során műveleteket alkalmaz a bemeneti adatok, például a kiválasztás, vetítés, összesítés, ablakolás, és a streaming DataFrame referencia DataFrames. Ezután az eredményeket a fájltárolóba (Azure Storage Blobs vagy Data Lake Storage) vagy bármely adattárba adja ki egyéni kód (például SQL Database vagy Power BI) használatával. Strukturált streamelés is biztosít kimenetet a konzol on-the-hibakeresés helyileg, és egy memóriában lévő tábla, így láthatja a hibakereséshez létrehozott adatok hdinsight.

![Streamfeldolgozás HDInsight-mal és Spark strukturált streameléssel](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> A Spark strukturált streamelése felváltja a Spark Streaming (DStreams) szolgáltatást. A jövőben a strukturált streamelés fejlesztéseket és karbantartást kap, míg a DStreams csak karbantartási módban lesz. Strukturált streamelés jelenleg nem olyan funkciókkal kiegészített, mint a DStreams a források és a fogadók, hogy támogatja a dobozból, ezért értékelje ki a követelmények kiválasztásához a megfelelő Spark-stream feldolgozási beállítást.

## <a name="streams-as-tables"></a>Adatfolyamok táblaként

A Spark strukturált adatfolyama egy adatfolyamot jelent, amely nincs részletesen határtalan, azaz a tábla az új adatok megérkezésével tovább növekszik. Ezt a *bemeneti táblát* egy hosszú ideig futó lekérdezés folyamatosan feldolgozza, és a *kimeneti táblába*küldött eredményeket:

![Strukturált streamelési koncepció](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

Strukturált streamelési, adatok érkeznek a rendszerbe, és azonnal egy bemeneti táblába. Olyan lekérdezéseket írhat (a DataFrame és az Adatkészlet API-k használatával), amelyek műveleteket hajtanak végre ezen a bemeneti táblán. A lekérdezés kimenete egy másik táblát, az *eredménytáblát eredményez.* Az eredménytábla tartalmazza a lekérdezés eredményeit, amelyből adatokat rajzol egy külső adattárhoz, egy relációs adatbázishoz. A bemeneti táblából történő adatfeldolgozás időpontjának időzítését az *eseményindító-intervallum*szabályozza . Alapértelmezés szerint az eseményindító-időköz nulla, így a strukturált streamelés megpróbálja feldolgozni az adatokat, amint megérkezik. A gyakorlatban ez azt jelenti, hogy amint strukturált streamelés befejeződött az előző lekérdezés futtatásának feldolgozása, elindítja egy másik feldolgozási futtatás ellen az újonnan fogadott adatok. Beállíthatja, hogy az eseményindító időközönként fusson, így a streamelési adatok időalapú kötegekben lesznek feldolgozva.

Az eredménytáblákban szereplő adatok csak a lekérdezés legutóbbi feldolgozása óta *(hozzáfűző mód)* óta új adatokat tartalmazhatnak, vagy a tábla minden alkalommal frissíthető, amikor új adatok jelennek meg, így a tábla az adatfolyam-lekérdezés kezdete óta az összes kimeneti adatot tartalmazza (*teljes mód*).

### <a name="append-mode"></a>Hozzáfűzési mód

Hozzáfűzési módban csak az eredmények táblához az utolsó lekérdezés futtatása óta hozzáadott sorok jelen vannak az eredménytáblában, és külső tárolóba vannak írva. A legegyszerűbb lekérdezés például csak a bemeneti táblából származó összes adatot változatlanul másolja az eredménytáblába. Minden alkalommal, amikor egy eseményindító időköz lejár, a rendszer feldolgozza az új adatokat, és az új adatokat képviselő sorok megjelennek az eredménytáblában.

Fontolja meg egy olyan forgatókönyv, amelyben a hőmérséklet-érzékelők, például egy termosztát telemetriai adatokat dolgoz fel. Tegyük fel, hogy az első eseményindító egy eseményt dolgoz fel 00:01-kor az 1-es eszközhöz, 95 fokos hőmérséklet-értékkel. A lekérdezés első eseményindítójában csak a 00:01-es idővel rendelkező sor jelenik meg az eredménytáblában. 00:02 időpontban, amikor egy másik esemény érkezik, az egyetlen új sor a sor idő00:02, így az eredménytábla csak azt az egy sort tartalmazza.

![Strukturált streamelési hozzáfűző mód](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Hozzáfűző mód használatakor a lekérdezés vetületeket alkalmaz (kiválasztja a számára fontos oszlopokat), szűri (csak bizonyos feltételeknek megfelelő sorokat választ) vagy csatlakozik (az adatok hozzátöltése statikus keresőtáblából származó adatokkal). A hozzáfűző mód megkönnyíti, hogy csak a megfelelő új adatpontokat tolja ki a külső tárolóba.

### <a name="complete-mode"></a>Teljes mód

Fontolja meg ugyanazt a forgatókönyvet, ezúttal a teljes módban. Teljes módban a teljes kimeneti tábla minden eseményindítón frissül, így a tábla nem csak a legutóbbi eseményindító-futtatásból, hanem az összes futtatásból származó adatokat is tartalmaz. A teljes mód segítségével a bemeneti táblából változatlanul másolhatja az adatokat az eredménytáblába. Minden aktivált futtatáskor az új eredménysorok az összes előző sorral együtt jelennek meg. A kimeneti eredmények tábla a végén tárolja az összes összegyűjtött adatokat, mivel a lekérdezés kezdődött, és végül elfogy a memória. A teljes mód olyan összesített lekérdezésekkel való használatra szolgál, amelyek valamilyen módon összegezik a bejövő adatokat, így minden eseményindítón az eredménytábla új összegzéssel frissül.

Tegyük fel, hogy eddig öt másodpercnyi adat már feldolgozva van, és itt az ideje, hogy feldolgozzuk az adatokat a hatodik másodpercre. A bemeneti tábla 00:01 és 00:03 időeseményeket is beírt. A példa lekérdezés célja, hogy az eszköz átlagos hőmérsékletét öt másodpercenként adja meg. A lekérdezés megvalósítása olyan összesítést alkalmaz, amely az 5 másodperces időszakon belül reked, átlaga a hőmérséklet, és létrehoz egy sort az adott intervallum átlaghőmérsékletéhez. Az első 5 másodperces ablak végén két tuple starész van: (00:01, 1, 95) és (00:03, 1, 98). Tehát az ablak00:00-00:05 az aggregáció termel egy törzs az átlagos hőmérséklet 96,5 fok. A következő 5 másodperces ablakban csak egy adatpont van 00:06-kor, így az eredményül kapott átlaghőmérséklet 98 fok. A 00:10 időpontban a teljes módban az eredménytábla a windows 00:00-00:05 és a 00:05-00:10 rendszer sorait is tartalmaz, mivel a lekérdezés az összes összesített sort, nem csak az újakat adja ki. Ezért az eredménytábla az új ablakok hozzáadásával tovább növekszik.

![Strukturált streamelésteljes mód](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nem minden teljes módot használó lekérdezés hatására a tábla határok nélkül növekszik.  Vegye figyelembe az előző példában, hogy ahelyett, hogy átlagolna a hőmérsékletet az időablak, akkor átlagoló helyett eszközazonosító. Az eredménytábla rögzített számú sort tartalmaz (eszközönként egyet), és az eszköz átlagos hőmérsékletét tartalmazza az adott eszköztől kapott összes adatponton. Az új hőmérsékletek beérkezésekor az eredménytábla frissül, így a táblázatátlagok mindig aktuálisak lesznek.

## <a name="components-of-a-spark-structured-streaming-application"></a>A Spark strukturált streamelési alkalmazás összetevői

Egy egyszerű példa lekérdezés összegezheti a hőmérséklet-értékek egyórás ablakok. Ebben az esetben az adatok jsonfájlokban tárolódnak az Azure Storage-ban (a HDInsight-fürt alapértelmezett tárolójaként csatolva):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Ezek a JSON-fájlok `temps` a HDInsight-fürt tárolója alatti almappában tárolódnak.

### <a name="define-the-input-source"></a>A bemeneti forrás meghatározása

Először konfiguráljon egy DataFrame-et, amely leírja az adatok forrását és a forrás által igényelt beállításokat. Ez a példa az Azure Storage JSON-fájljaiból merít, és olvasási időben sémát alkalmaz rájuk.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>A lekérdezés alkalmazása

Ezután alkalmazzon egy lekérdezést, amely tartalmazza a kívánt műveleteket a streamelési DataFrame ellen. Ebben az esetben az összesítés az összes sorokat 1 órás ablakokba csoportosítja, majd kiszámítja a minimális, átlagos és maximális hőmérsékletet az adott 1 órás ablakban.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>A kimeneti fogadó meghatározása

Ezután adja meg az egyes eseményindító-időközökön belül az eredménytáblához hozzáadott sorok célját. Ebben a példában csak az összes `temps` sort egy memórián belüli táblába adja ki, amelyet később lekérdezhet a SparkSQL-rel. A teljes kimeneti mód biztosítja, hogy az összes ablak összes sora minden alkalommal kilegyen kapcsolva.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>A lekérdezés indítása

Indítsa el a streamelési lekérdezést, és futtassa, amíg a végződtetési jel nem érkezik.

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Eredmények megtekintése

Miközben a lekérdezés fut, ugyanabban a SparkSession, futtathat `temps` egy SparkSQL-lekérdezést a tábla, ahol a lekérdezés eredményeit tárolja.

    select * from temps

Ez a lekérdezés az alábbihoz hasonló eredményt ad:

| Ablak |  min(temp) | átlag(ideiglenes) | max(ideiglenes) |
| --- | --- | --- | --- |
|{u'start': u'2016-07-26T02:00:00.000Z', u'end'... |    95 |    95.231579 | 99 |
|{u'start': u'2016-07-26T03:00:00.000Z', u'end'...  |95 |   96.023048 | 99 |
|{u'start': u'2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': u'2016-07-26T05:00:00.000Z', u'end'...  |95 |   96.984639 | 99 |
|{u'start': u'2016-07-26T06:00:00.000Z', u'end'...  |95 |   97.014749 | 99 |
|{u'start': u'2016-07-26T07:00:00.000Z', u'end'...  |95 |   96.980971 | 99 |
|{u'start': u'2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

A Spark Structured Stream API-val kapcsolatos részletekért, valamint az általa támogatott bemeneti adatforrások, műveletek és kimeneti fogadók az [Apache Spark strukturált streamelési programozási útmutatójában](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)talál.

## <a name="checkpointing-and-write-ahead-logs"></a>Ellenőrzőpontok és előre írt naplók

A rugalmasság és a hibatűrés biztosítása érdekében a strukturált *streamelés ellenőrzőpontokra* támaszkodik annak érdekében, hogy az adatfolyam-feldolgozás megszakítás nélkül folytatódjon, még csomóponthibák esetén is. A HDInsightban a Spark ellenőrzőpontokat hoz létre a tartós tároláshoz, akár az Azure Storage, akár a Data Lake Storage számára. Ezek az ellenőrzőpontok a streamelési lekérdezés folyamatadatait tárolják. Emellett a strukturált *adatfolyam-továbbítás egy írási előre naplót* (WAL) használ. A WAL rögzíti a fogadott, de lekérdezéssel még fel nem dolgozott adatokat. Ha hiba történik, és a feldolgozás torkig van a WAL-val, a forrásból érkező események nem vesznek el.

## <a name="deploying-spark-streaming-applications"></a>Spark Streaming-alkalmazások telepítése

Általában egy Spark Streaming-alkalmazást helyileg hoz létre egy JAR-fájlba, majd telepíti a Spark hdinsight-alapú üzembe helyezéséhez a JAR-fájl HDInsight-fürthöz csatlakoztatott alapértelmezett tárolóra másolásával. Az alkalmazást a fürtből elérhető [Apache Livy](https://livy.incubator.apache.org/) REST API-kkal indíthatja el egy POST-művelet használatával. A POST törzse tartalmaz egy JSON-dokumentumot, amely biztosítja a JAR elérési útját, annak az osztálynak a nevét, amelynek fő metódusa meghatározza és futtatja a streamelési alkalmazást, valamint opcionálisan a feladat erőforrás-követelményeit (például a végrehajtók, a memória és a magok számát) , valamint az alkalmazáskód által igényelt konfigurációs beállításokat.

![Spark Streaming-alkalmazás telepítése](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Az összes alkalmazás állapota is ellenőrizhető egy GET kéréssel egy LIVY-végpontmal szemben. Végül egy futó alkalmazást leállíthat egy DELETE-kérelem kiadásával a LIVY-végponton. A LIVY API-val kapcsolatos részletekért tekintse [meg az Apache LIVY távoli feladatait](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>További lépések

* [Apache Spark-fürt létrehozása a HDInsightban](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark strukturált streamelési programozási útmutató](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Az Apache LIVY segítségével távolról indíthatapache Spark-feladatokat](apache-spark-livy-rest-interface.md)
