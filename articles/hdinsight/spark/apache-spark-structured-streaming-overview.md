---
title: Spark strukturált adatfolyamként való továbbítás az Azure HDInsight |} Microsoft Docs
description: Spark strukturált adatfolyam-alkalmazások használata a HDInsight Spark-fürtök.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: maxluk
ms.openlocfilehash: f3eccfb63b127f3cbb8f9d95ec2fa802861d8c9d
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="overview-of-spark-structured-streaming"></a>A strukturált Spark Streaming áttekintése

Spark strukturált Streaming lehetővé teszi az adatok adatstreamek feldolgozására méretezhető, nagy átviteli, hibatűrő alkalmazások végrehajtásához. Strukturált Streaming a Spark SQL-kezelő épül, és javítja a szerkezet származó Spark SQL adatkeretek és adatkészleteket, megírhatja streaming lekérdezi azonos módon, akkor kötegelt lekérdezéseket írhat.  

Strukturált Streaming-alkalmazások HDInsight Spark-fürtjei futtatnak, és csatlakozni a streamelési adatok Kafka, TCP szoftvercsatornát (hibakereséshez), Azure Storage, vagy az Azure Data Lake Store. Az utóbbi, külső tárolószolgáltatások használ, két lehetőség engedélyezi a tárolási fel új fájlokat beállításjegyzékek, és azok tartalmát feldolgozását, mintha továbbítva lettek. 

Strukturált Streaming időintervalluma műveletek alkalmazása a bemeneti adatokat, például kijelölés, leképezés, összesítési, Ablakozó, és a folyamatos átviteli DataFrame hivatkozással DataFrames csatlakoztatása hosszan futó lekérdezést hoz létre. A következő, az eredményeket a file storage (Azure Storage Blobsba vagy Data Lake Store), vagy bármely adattárral (például az SQL-adatbázis vagy a Power bi-ban) egyéni kód használatával. Strukturált Streaming emellett kimeneti helyileg hibakereséshez a konzolon, és egy memórián belüli tábla hívjuk fel a létrehozott hdinsight hibakeresési adatok. 

![A HDInsight és strukturált Spark Streaming streamfeldolgozó ](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]
> Spark strukturált Streaming primitívnek Spark Streaming (DStreams). Továbbítja, strukturált Streaming kap fejlesztése és karbantartási, DStreams lesz csak a karbantartási módban. Strukturált Streaming jelenleg nem, a szolgáltatás-töltse ki a DStreams az adatforrások és fogadók esetében nem támogatja, így kiértékelheti a vonatkozó követelményeket válassza ki a megfelelő Spark adatfolyam a feldolgozási beállítás. 

## <a name="streams-as-tables"></a>Táblák adatfolyamok

Az adatok adatfolyam Spark strukturált Streaming jelöli, amely részletesen unbounded táblázatként, ez azt jelenti, hogy a tábla továbbra is nő, ahogy új adat érkezik. Ez *bemeneti tábla* folyamatosan dolgozza fel a hosszan futó lekérdezés, és az eredmények küldött egy *eredménytábla*:

![A koncepció Streaming szerkezete](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

Strukturált adatfolyam adatok megérkezik a rendszer, és van egy bemeneti táblára történő azonnal okozhatnak. (A DataFrame és adatkészlet API-k használatával) lekérdezéseket, szemben a bemeneti táblázat műveleteket írhat. A lekérdezés kimenet egy másik tábla a *eredmények táblázatában*. Az eredmények táblázatában tartalmazza a lekérdezést, ahol egy külső datastore, ilyen a relációs adatok megrajzolásához eredményeit. Ha adatok feldolgozása a bemeneti táblázat időzítése vezérli a *eseményindító időköz*. Alapértelmezés szerint az eseményindító időköz strukturált Streaming megpróbálja feldolgozni az adatokat, amint megérkeznek, értéke nulla. A gyakorlatban ez azt jelenti, hogy, amint strukturált Streaming végezhető el az előző lekérdezés futtatásakor feldolgozása, újonnan érkezett adatokat futtatni egy másik feldolgozási kezdődik. Az eseményindító futtatását, hogy, beállíthatja, hogy a streamelési adatok feldolgozása időalapú kötegekben. 

A táblák tartalmazhat, csak a legutóbbi új adatokat idő a lekérdezési eredmények adatok fel lettek dolgozva (*hozzáfűzéssel*), vagy a tábla teljesen frissíteni lehet, minden alkalommal, amikor nincs új adatokat, így a tábla tartalmazza az összes kimeneti adatokat az adatfolyam-továbbítási lekérdezés kezdete óta (*teljes mód*).

### <a name="append-mode"></a>Append mode

A hozzáfűzéssel, csak a sor utolsó lekérdezés futtatása az eredmények táblázatában szerepelnek az eredmények táblázatában hozzáadott és a külső tárhelyen. Például a legegyszerűbb lekérdezés csak minden adatot a bemeneti tábla másolja az eredmények táblázatában változatlan. Minden alkalommal, amikor egy eseményindító időtartam, az új adatok feldolgozási, és az eredmények táblázatában a sorait, úgy, hogy új adatok jelennek meg. 

Fontolja meg egy olyan forgatókönyvet, ahol feldolgozás alatt az hőmérséklet-érzékelők, például egy termosztát telemetriai adatokat. Tegyük fel, az első eseményindító ideje 00:01, az eszköz 1 hőmérséklet-érték 95 fokban egy esemény feldolgozása. A lekérdezés az első eseményindító csak a sor ideje 00:01. az eredmények táblázatában jelenik meg. Ideje 00:02 egy másik eseményt fogadásakor, a csak az új sor nem rendelkező ideje 00:02 sor, és így a az eredmények táblázatában csak adott egy sort tartalmaznia.

![Strukturált Streaming hozzáfűzéssel](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Ha használatával hozzáfűzéssel, a lekérdezés volna kell alkalmazása leképezések (ügyel az oszlopok kijelölése), szűrés (kiadási csak bizonyos feltételeknek megfelelő sorok) vagy a csatlakozás (bővítheti az adatokat a statikus keresési tábla adatait). Hozzáfűzendő egyszerűen csak a megfelelő új adatai rámutat, külső tárolási mód elérhetővé válnak.

### <a name="complete-mode"></a>Teljes mód

Tételezzük azonos, ezúttal teljes mód használatával. Teljes módban teljes kimeneti frissítik a táblázatot minden eseményindító, a tábla nem csak a legutóbbi futtatás eseményindító, azonban az összes futtatása adatokat tartalmazza. Teljes mód segítségével másolja az adatokat, az eredmények táblázatában számára a bemeneti táblázat változatlan. Az új sor minden kiváltott futtatáskor a korábbi sorok együtt jelennek meg. A kimeneti eredmények táblázatában kat, összegyűjteni, mert a lekérdezés megkezdte, és végül kevés a memória kell futtatni az adatok tárolásához. Teljes módban készült, a bejövő adatokat valamilyen módon összesített lekérdezésekkel, és így tovább minden eseményindító az eredmények táblázatában frissül az új összefoglaló. 

Tegyük fel, amennyiben a feldolgozása már öt másodpercenként értékelésével, és az adatok feldolgozása a hatodik második ideje. A bemeneti tábla események rendelkezik ideje 00:01 és ideje 00:03. A példában a lekérdezés célja, hogy az eszköz az átlaghőmérséklet öt másodpercenként. Ez a lekérdezés végrehajtása, amely összes minden 5 másodperces ablak tartoznak, átlagának kiszámítása a hőmérséklet és adott időszakban az átlaghőmérséklet egy sort eredményez értéket összesítő vonatkozik. Az első 5 másodperces ablak végén nincsenek két rekordnak: (00:01, 1, 95) és (00:03, 1, 98). Igen, a az ablak 00:00 – 00:05 az összesítés az átlaghőmérséklet 96.5 fokban rendelkező rekordot hoz létre. A következő 5 másodperces ablak nincs jelenleg csak egy-egy ideje 00:06, így az eredményül kapott átlaghőmérséklet 98 fokban megadva. Ideje 00:10, teljes módban, az eredmények táblázatában rendelkezik a sorok mindkét windows 00:00 – 00:05 és 00:05-00:10 a lekérdezés kimenete az összes összesített sort, mert nem csupán a újakat. Ezért az eredmények táblázatában továbbra is nő, ahogy új windows kerülnek.    

![Strukturált adatfolyam teljes módban](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nem minden lekérdezések teljes móddal, akkor a táblázat határainak nélkül.  Fontolja meg az előző példában, ahelyett hogy átlagolási által időkerete hőmérséklet, ehelyett átlagosan által eszközazonosító. Eredménytáblájában rögzített számú sort (szövegmezőnként egy eszköz) az eszköz az átlaghőmérséklet rendelkező összes, az adott eszközről fogadott adatok pontok között tartalmazza. Új hőmérsékletek érkeznek, az eredmények táblázatában frissül, hogy a tábla átlagok mindig naprakészek. 

## <a name="components-of-a-spark-structured-streaming-application"></a>A Spark strukturált Streaming alkalmazások összetevői

Egy egyszerű példa lekérdezést is összefoglalója hour-long Windows hőmérséklet szivattyútelepek érzékelőinek adatai. Ebben az esetben az adatok tárolását a JSON-fájlokat az Azure Storage (az alapértelmezett tárolóként a HDInsight-fürthöz kapcsolódó):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

A JSON-fájlok tárolják a `temps` almappa alatt tárolót a HDInsight-fürthöz. 

### <a name="define-the-input-source"></a>A bemeneti forrás megadása

Először konfiguráljon egy DataFrame, amely ismerteti az adatok és bármely, hogy az adatforrás által igényelt beállításokat. Ebben a példában a JSON-fájlok az Azure Storage megrajzolja és érvényes séma őket olvasási időpontban.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>A lekérdezés alkalmazása

A következő alkalmazni a kívánt műveleteket a Streaming DataFrame tartalmazó lekérdezést. Összesítést ebben az esetben az összes sor csoportosítja 1 órás windows, és majd kiszámítja a minimális átlagos és maximális hőmérsékletek adott 1 órás ablakban.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Adja meg a kimeneti fogadóját

Ezt követően adja meg a sorok, amelyekre az eredmények táblázatában minden eseményindító intervallumon belül kerülnek célját. Ebben a példában csak kimenete egy memórián belüli tábla összes sorát `temps` , hogy később lekérheti a sparksql-hez. Teljes kimeneti mód biztosítja, hogy minden sor minden windows kimeneti minden alkalommal.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>A lekérdezés indítása

Indítsa el a streamelési lekérdezés, és fut, amíg egy lezáró jel érkezik. 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Az eredmények megtekintése

Az azonos SparkSession, a lekérdezés futtatása közben a sparksql-hez lekérdezése futtathatja a `temps` a lekérdezési eredmények tároló tábla. 

    select * from temps

Ez a lekérdezés eredményeit az alábbihoz hasonló eredményez:


| ablak |  Min(Temp) | AVG(Temp) | Max(Temp) |
| --- | --- | --- | --- |
|{u'start': ' 2016 u-07-26T02:00:00.000Z ", u'end"... |    95 |    95.231579 | 99 |
|{u'start': ' 2016 u-07-26T03:00:00.000Z ", u'end"...  |95 |   96.023048 | 99 |
|{u'start': ' 2016 u-07-26T04:00:00.000Z ", u'end"...  |95 |   96.797133 | 99 |
|{u'start': ' 2016 u-07-26T05:00:00.000Z ", u'end"...  |95 |   96.984639 | 99 |
|{u'start': ' 2016 u-07-26T06:00:00.000Z ", u'end"...  |95 |   97.014749 | 99 |
|{u'start': ' 2016 u-07-26T07:00:00.000Z ", u'end"...  |95 |   96.980971 | 99 |
|{u'start': ' 2016 u-07-26T08:00:00.000Z ", u'end"...  |95 |   96.965997 | 99 |  

További részletek a Spark strukturált adatfolyam API a bemeneti adatokkal együtt források, műveletek és a kimeneti fogadók esetében akkor támogatja, lásd: [Spark strukturált Streaming programozásával foglalkozó útmutatójában](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Ellenőrzőpontok és írási keresési naplókat

Képes biztosítani a rugalmasság és a hibatűrés, strukturált Streaming támaszkodik *ellenőrzőpontok* annak érdekében, hogy az adatfolyam feldolgozása még csomópont hibákkal megszakításmentes folytatásához. A Hdinsightban Spark tartós Storage Azure Storage vagy a Data Lake Store ellenőrzőpontot hoz létre. Ezeket az ellenőrzőpontokat a folyamatos átviteli lekérdezési folyamat adatainak tárolására. Emellett strukturált Streaming használ egy *írási előre napló* (WAL). A WAL beérkezett, de még nem feldolgozott egy lekérdezés által feldolgozott adatokat rögzíti. Ha hiba lép fel, és a WAL feldolgozási újraindul, a forrás érkező eseményeket nem vesznek el.

## <a name="deploying-spark-streaming-applications"></a>Spark Streaming-alkalmazások telepítése

Általában létre olyan Spark Streaming-alkalmazás helyileg történő JAR-fájlra, és ezután telepítse a Spark on HDInsight úgy, hogy a HDInsight-fürtjét csatlakoztatott alapértelmezett tároló a JAR-fájlra. Megkezdheti az alkalmazás a LIVY REST API-khoz elérhető a fürtből, POST művelettel. A POST törzsét tartalmaz, amely a útvonalat biztosít a JAR, amelynek fő metódus határozza meg, és futtatja az adatfolyam-továbbítási alkalmazást, és szükség esetén a feladat (például végrehajtója, memória és -magok száma) erőforrás-követelményei az osztály nevét a JSON-dokumentum , és minden olyan konfigurációs beállítást az alkalmazás kódjában igényel.

![Spark Streaming alkalmazás telepítése](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Az összes alkalmazás állapotát ellenőrizni lehessen a LIVY végpont egy GET kérelmet a. A DELETE kérelmet a LIVY végpont kiállításával végül is állítsa le a futó alkalmazások. További részletek a LIVY API: [LIVY távoli feladatok](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>További lépések

* [Apache Spark-fürt létrehozása a Hdinsightban](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Spark Streaming programozásával foglalkozó útmutatójában szerkezete](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Indítsa el a távoli LIVY Spark feladatokhoz](apache-spark-livy-rest-interface.md)
