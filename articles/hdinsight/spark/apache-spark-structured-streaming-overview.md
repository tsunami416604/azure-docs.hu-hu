---
title: A Spark strukturált Stream az Azure HDInsight
description: Alkalmazások a Spark strukturált Stream használata a HDInsight Spark-fürtön.
services: hdinsight
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: maxluk
ms.openlocfilehash: 7470783ba3ebac652c83c397ba2bbe683023c657
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041585"
---
# <a name="overview-of-spark-structured-streaming"></a>A Spark strukturált Stream áttekintése

A Spark strukturált Stream lehetővé teszi a data-adatfolyamok feldolgozására, skálázható, nagy átviteli sebességű és hibatűrő alkalmazások megvalósításához. Strukturált Streamelés a Spark SQL-motor épül, és javítja a származó Spark SQL adatkeretek szerkezeteket és adatkészletek, streamelési lekérdezése ugyanúgy, írhat, kötegelt / volna írni.  

Structured Streaming-alkalmazások HDInsight Spark-fürtökön futó, és a streamelési adatok a Kafka, a TCP szoftvercsatorna (a hibakereséshez), az Azure Storage vagy Azure Data Lake Store csatlakozni. Az utóbbi két lehetőség, külső tárolási szolgáltatások támaszkodik, amely lehetővé teszi, tekintse meg a storage-bA hozzáadott új fájlok és feldolgozása azok tartalmát, mintha továbbítva lettek. 

Strukturált Streamelés óraszáma műveleteket alkalmaz a bemeneti adatok, például a kijelölés, leképezése, összesítési, ablakkezelési és a streamelési DataFrame hivatkozással DataFrames csatlakoztatása hosszan futó lekérdezést hoz létre. Következő lépésként, az eredményeket a file storage (Azure Storage Blobsba vagy a Data Lake Store) vagy minden adattárhoz egyéni kódot (például SQL-adatbázis vagy a Power BI) használatával. Strukturált Streamelés a helyi hibakeresési konzolt, és egy memórián belüli tábla kimeneti is biztosít, így láthatja a létrehozott HDInsight a hibakeresési adatokat. 

![A Stream feldolgozása a HDInsight és a Spark strukturált Stream ](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]
> A Spark strukturált Stream lecseréli a Spark Streaming (DStreams). Továbbítja, strukturált Stream kap megjelent fejlesztéseit és a karbantartás, amíg DStreams lesz csak a karbantartási módban. Strukturált Streamelés jelenleg nem, a szolgáltatás teljes körű DStreams, az adatforrásokhoz, és hogy támogatja a beépített fogadók, ezért a választani a megfelelő Spark stream feldolgozási beállítás kapcsolatos követelmények kiértékeléséhez. 

## <a name="streams-as-tables"></a>Streamek táblákként

Egy adatfolyam Spark strukturált Stream jelöli, amely korlátlan streameken működő mélyebben táblaként, a tábla, amely azt jelenti, hogy továbbra is nő, ahogy az új adatok érkeznek. Ez *bemeneti tábla* folyamatosan dolgoz fel egy hosszan futó lekérdezést, és elküldi az eredményeket egy *eredménytábla*:

![Strukturált Streamelés a koncepció](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

Strukturált Streamelés adatok szolgáltatáshoz érkezik, a rendszer, és azonnal egy bemeneti táblát be lett töltve. (Az adathalmaz és az adatkészlet API-k használatával), amelyek a bemeneti tábla kapcsolatos művelet-végrehajtási hajtanak végre lekérdezéseket írhat. A lekérdezés kimenet egy másik tábla a *eredmények táblázatában*. Az eredmények tábla, amelyből rajzoljon egy külső adattár, például egy relációs adatbázis adatait, a lekérdezés eredményeit tartalmazza. Amikor az adatok feldolgozása a bemeneti táblázat időzítése vezérli a *eseményindító időköz*. Alapértelmezés szerint a trigger időköz nulla, így strukturált Stream megpróbálja feldolgozni az adatokat, amint megérkeznek. A gyakorlatban ez azt jelenti, hogy, amint strukturált Stream történik elindítja a Futtatás az előző lekérdezés feldolgozása, egy másik feldolgozó futtatása az újonnan érkezett adatokon. Konfigurálhatja az eseményindító, futtatását, hogy, úgy, hogy a streamadatok feldolgozása időalapú kötegekben. 

Az adatok a táblák tartalmazhat, csak az utolsó óta új adatok idő a lekérdezés eredményében fel lettek dolgozva (*hozzáfűzéssel*), vagy a tábla minden alkalommal, amikor nincs új adat, így a tábla tartalmazza az összes kimeneti adatokat lehet teljesen frissítve a streamelési lekérdezés kezdete óta (*teljes mód*).

### <a name="append-mode"></a>Hozzáfűzési mód

A hozzáfűző módban csak a sor a legutóbbi lekérdezés futtatása az eredmények táblázatában találhatók az eredmények táblázatában hozzáadott és a külső tárhelyen. Például a legegyszerűbb lekérdezés csak az összes adatokat másol a bemeneti tábla változatlan eredmények táblázatában. Minden alkalommal, amikor egy trigger időtartam, az új adatok feldolgozása, és az új adatokat képviselő sora megjelenjen eredmények táblázatában. 

Példaként vegyünk egy forgatókönyvet, ahol olyan feldolgozási hőmérséklet-érzékelő, például egy thermostat származó telemetriai adatok. Tegyük fel, az első eseményindító egy esemény feldolgozása idő 00:01 1 eszköz és a egy hőmérséklet-érték 95 fokban. A lekérdezés az első eseményindító csak az idő 00:01-sor jelenik meg az eredmények táblázatában. Idő 00:02-es időpontnál egy másik eseményt érkezésekor a csak az új sor a sor az idő 00:02, és így a eredmények táblázatában csak, egy sort tartalmaznia.

![Strukturált Streamelés hozzáfűzéssel](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Amikor használatával hozzáfűzéssel, a lekérdezés kellene lennie alkalmazása leképezések (azt ügyel oszlopok kiválasztásával), szűrés (kiadási csak bizonyos feltételek egyező sorok) vagy a csatlakozás (az adatok egy statikus keresési táblázat származó adatokkal kiegészítve). Könnyen a releváns új adatok leküldése mutat külső tárolási mód teszi hozzáfűzése.

### <a name="complete-mode"></a>Teljes mód

Fontolja meg ugyanezt a forgatókönyvet, ezúttal teljes mód használatával. A teljes kimeneti tábla teljes módban frissül az minden eseményindító, a tábla nem csak a legutóbbi eseményindító-futtatás, azonban minden Futtatás adatait tartalmazza. Másolja az adatokat a bemeneti táblázat az eredmények táblázatában változatlan formában használhatja teljes mód. Az összes aktivált futtatását az új sorok együtt a korábbi sorok jelennek meg. A kimeneti eredmények tábla lesz végül összegyűjteni, mert a lekérdezés kezdete, és végül futtatná nincs elég memória a adatok tárolásához. Teljes módban az összesítő lekérdezés, amely valamilyen módon a bejövő adatok összegzése használatra szánt, és így tovább minden trigger eredmények táblázatában frissül az új összegzését. 

Feltételezik, amennyiben vannak a öt másodperc alatt az adatok feldolgozása már, az adatok feldolgozása a hatodik második ideje. A bemeneti tábla események idő 00:01 és idő 00:03 rendelkezik. Ez a példa lekérdezés célja, hogy az eszköz az átlaghőmérséklet öt másodpercenként. Ez a lekérdezés végrehajtásának aggregátum, amely az értékei, minden egyes 5 másodperces időtartamon belül esik, akkor számítja ki a hőmérséklet és hoz létre egy sort az átlaghőmérséklet adott időszakra vonatkozik. Az első 5 másodperces ablak végén nincsenek két: (00:01, 1, 95) és (00:03, 1, 98). Tehát az ablak 00:00 – 00:05 az összesítés 96.5 fok átlagos hőmérsékletét a rekordot hoz létre. A következő 5 másodperces ablak nincs csak egyetlen adatpont: 00:06 / idő, így az eredményül kapott átlaghőmérséklet 98 fok. Idő 00:10, teljes körű mód használata esetén az eredmények található táblázat soraiban a tartozó mindkét windows 00:00 – 00:05 és 00:05 – 00:10, mert a lekérdezés megjeleníti az összes összesített sor, nem csak az újakat. Ezért az eredmények táblázatában továbbra is nő, ahogy új windows kerülnek.    

![Strukturált Streamelés kész mód](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nem minden lekérdezések teljes mód használata miatt korlátok nélkül növelheti a tábla.  Érdemes lehet az előző példában, amely helyett átlagolási időtartam által a hőmérséklet, ehelyett átlagolt által eszközazonosító. Az eredményként kapott tábla rögzített számú sort (egy eszközönként) az átlaghőmérséklet az eszköz az adott eszközről fogadott összes adatpont között tartalmazza. Új hőmérsékletek érkezik, az eredmények táblázatában frissül, hogy a táblázatban az átlagokat mindig naprakészek. 

## <a name="components-of-a-spark-structured-streaming-application"></a>A Spark strukturált Stream alkalmazások összetevői

Egy egyszerű példa lekérdezés is összesíteni a hőmérséklet olvasmányok hour-long Windows. Ebben az esetben az adatok JSON-fájlok (a HDInsight-fürt alapértelmezett tárolóként csatolt) Azure Storage-ban tárolja:

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Ezek a JSON-fájlok tárolják a `temps` alatt a HDInsight-fürt tároló almappába. 

### <a name="define-the-input-source"></a>A bemeneti forrás megadása

Először állítsa be, amely leírja az adatokat, és a beállításokat, amelyeket az adatforrás szükséges forrását DataFrame. Ebben a példában megrajzolja a JSON-fájlokból az Azure Storage-ban, és alkalmazza a séma hozzájuk olvasási időpontban.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>A lekérdezés a alkalmazni

Ezután a alkalmazni egy lekérdezést, amely tartalmazza a kívánt műveleteket az adatfolyam-adathalmaz ellen. Ebben az esetben egy összesítést a csoportok összes sorát, 1 óra Windows, és majd kiszámítja, hogy 1 óra ablakban a minimális átlagos és maximális hőmérsékleteket.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>A kimeneti fogadó megadása

Ezt követően adja meg a cél a sorok, az eredmények táblázatában hozzáadott minden trigger intervallumon belül. Ebben a példában csak egy memórián belüli tábla minden sort adja vissza `temps` , hogy később lekérdezheti a sparksql-hez. Teljes kimenő mód biztosítja, hogy az összes sort az összes Windows kimeneti minden alkalommal.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Indítsa el a lekérdezést

Indítsa el a streamelési lekérdezést, és futtassa addig, amíg egy lezáró jel érkezik. 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Az eredmények megtekintése

A lekérdezés fut, az azonos SparkSession, míg a sparksql-hez lekérdezést futtathatja a `temps` tábla a lekérdezés eredményeinek tárolására. 

    select * from temps

Ez a lekérdezés poskytne eredmények a következőhöz hasonló:


| ablak |  Min(Temp) | AVG(Temp) | Max(Temp) |
| --- | --- | --- | --- |
|{u'start ": u" 2016-07-26T02:00:00.000Z ", u'end"... |    95 |    95.231579 | 99 |
|{u'start ": u" 2016-07-26T03:00:00.000Z ", u'end"...  |95 |   96.023048 | 99 |
|{u'start ": u" 2016-07-26T04:00:00.000Z ", u'end"...  |95 |   96.797133 | 99 |
|{u'start ": u" 2016-07-26T05:00:00.000Z ", u'end"...  |95 |   96.984639 | 99 |
|{u'start ": u" 2016-07-26T06:00:00.000Z ", u'end"...  |95 |   97.014749 | 99 |
|{u'start ": u" 2016-07-26T07:00:00.000Z ", u'end"...  |95 |   96.980971 | 99 |
|{u'start ": u" 2016-07-26T08:00:00.000Z ", u'end"...  |95 |   96.965997 | 99 |  

A Spark strukturált Stream API részleteiért, a bemeneti adatokkal együtt forrásokból, a műveletek és a kimeneti fogadók azt támogatja, lásd: [Spark strukturált Streamelés programozási útmutatója](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Ellenőrzőpontok létrehozása és előre írási naplók

Rugalmasság és a hibatűrés biztosításához, strukturált Stream támaszkodik *ellenőrzőpontok* , győződjön meg arról, hogy a stream feldolgozása még a csomóponthibák megszakításmentes folytatásához. A HDInsight Spark tartós tárolási, az Azure Storage vagy a Data Lake Store ellenőrzőpontok hoz létre. Ezek az ellenőrzőpontok a streamelési lekérdezés folyamatban adatainak tárolására. Emellett strukturált Stream használ egy *írási előre log* (WAL). A WAL beérkezett, de még nem feldolgozott lekérdezések által betöltött adatokat rögzíti. Ha hiba lép fel, és a WAL feldolgozási újraindul, bármilyen forrásból fogadott események nem vesznek el.

## <a name="deploying-spark-streaming-applications"></a>Spark Streaming-alkalmazások üzembe helyezéséhez

Általában a helyileg egy JAR-fájlba a Spark Streaming-alkalmazás létrehozása, és ezután telepítheti az a Spark on HDInsight a JAR-fájlt másolja a csatlakoztatott és a HDInsight-fürt alapértelmezett tárolója. Az alkalmazás a fürtről, a POST művelettel indítható LIVY REST API-kat. A bejegyzés törzse tartalmazza a JSON-dokumentumot, az elérési utat biztosít a JAR, amelynek fő metódus határozza meg, és az adatfolyam-továbbítási alkalmazást, és szükség esetén az erőforrás-igényű (például végrehajtóval, a memória és magok száma) a feladat fut az osztály neve , és minden olyan konfigurációs beállítást az alkalmazás kódjában igényel.

![A Spark Streaming-alkalmazás üzembe helyezése](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Az összes alkalmazás állapotát egy GET kérelmet a LIVY-végpont az is ellenőrizhető. Végül a futó alkalmazás leállíthatja a LIVY végponthoz törlési kérelem elküldése. További információ a LIVY API: [távoli feladatok a LIVYVEL](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>További lépések

* [A HDInsight egy Apache Spark-fürt létrehozása](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [A Spark strukturált Streamelés programozási útmutatója](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Indítsa el a Spark-feladatok a LIVYVEL távolról](apache-spark-livy-rest-interface.md)
