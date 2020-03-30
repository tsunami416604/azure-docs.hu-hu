---
title: Ismerje meg az Apache Spark-kódfogalmakat az Azure Data Lake Analytics U-SQL fejlesztői számára.
description: Ez a cikk ismerteti az Apache Spark fogalmait, amelyek segítségével az U-SQL-fejlesztők megérthetik a Spark-kódfogalmakat.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74424003"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Az Apache Spark kódjának ismertetése az U-SQL-fejlesztők számára

Ez a szakasz magas szintű útmutatást nyújt az U-SQL-parancsfájlok Apache Sparkká történő átalakításához.

- A két [nyelv feldolgozási paradigmájának összehasonlításával](#understand-the-u-sql-and-spark-language-and-processing-paradigms) kezdődik
- Tippek a következőkhöz:
   - [Parancsfájlok átalakítása,](#transform-u-sql-scripts) beleértve az U-SQL [sorhalmaz-kifejezéseit](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions)
   - [.NET-kód](#transform-net-code)
   - [Adattípusok](#transform-typed-values)
   - [Katalógusobjektumok](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Ismerje meg az U-SQL és a Spark nyelvi és feldolgozási paradigmáit

Mielőtt elkezdené átváltani az Azure Data Lake Analytics U-SQL-parancsfájljait a Sparkba, hasznos megérteni a két rendszer általános nyelvét és feldolgozási filozófiáit.

Az U-SQL egy SQL-szerű deklaratív lekérdezési nyelv, amely adatfolyam-paradigmát használ, és lehetővé teszi a .NET (például C#), Python és R nyelven írt felhasználói kód egyszerű beágyazását és horizontális kitöltését. A felhasználó-bővítmények egyszerű kifejezéseket vagy felhasználó által definiált függvényeket valósíthatnak meg, de lehetővé tehetik a felhasználó számára az úgynevezett felhasználó által definiált operátorok megvalósítását is, amelyek egyéni operátorokat valósítanak meg sorhalmazszintű átalakítások, kibontások és kimenetírás végrehajtására.

A Spark egy horizontális felskálázási keretrendszer, amely számos nyelvi kötést kínál a Scala, Java, Python, .NET stb. majd egy LINQ-szerű tartomány-specifikus nyelv (DSL) átalakítására őket. Azt is előírja, sparkSQL deklaratív alnyelvként az adatkeret és az adatkészlet absztrakciók. A DSL a műveletek, átalakítások és műveletek két kategóriáját biztosítja. Az adatabsztrakciókra történő átalakítások alkalmazása nem hajtja végre az átalakítást, hanem a kiértékelésre benyújtandó végrehajtási tervet (például az eredmény ideiglenes táblába vagy fájlba írása vagy nyomtatása eredmény).

Így amikor egy U-SQL-parancsfájlt spark-programba fordít, el kell döntenie, hogy melyik nyelvet szeretné használni, hogy legalább létrehozza az adatkeret-absztrakciót (amely jelenleg a leggyakrabban használt adataktrakció), és hogy meg szeretné-e írni a deklaratív adatfolyam-átalakítások a DSL vagy a SparkSQL használatával. Néhány összetettebb esetben előfordulhat, hogy fel kell osztania az U-SQL-parancsfájlt a Spark és az Azure Batch vagy az Azure Functions által megvalósított egyéb lépések sorozatára.

Továbbá az Azure Data Lake Analytics az U-SQL szolgáltatást kiszolgáló nélküli feladatszolgáltatási környezetben kínálja, míg mind az Azure Databricks, mind az Azure HDInsight fürtszolgáltatás formájában kínálja a Sparkot. Az alkalmazás átalakításakor figyelembe kell vennie a fürtök létrehozásának, méretezésének, méretezésének és leszerelésének következményeit.

## <a name="transform-u-sql-scripts"></a>U-SQL parancsfájlok átalakítása

Az U-SQL parancsfájlok a következő feldolgozási mintát követik:

1. Az adatok olvasásra kerül vagy `EXTRACT` strukturálatlan fájlokból, az utasítás, a hely- vagy fájlkészlet specifikációja, valamint a beépített vagy felhasználó által definiált vonóésszelő és a kívánt séma, vagy u-SQL táblákból (felügyelt vagy külső táblák) történő olvasásra. Ez egy sorhalmazként jelenik meg.
2. A sorhalmazok több U-SQL utasításban lesznek átalakítva, amelyek U-SQL kifejezéseket alkalmaznak a sorhalmazokra, és új sorkészleteket hoznak létre.
3. Végül az eredményül kapott sorkészletek a `OUTPUT` hely(ek)et és a beépített vagy felhasználó által definiált kimenetet tartalmazó utasítást használó fájlokba, illetve egy U-SQL-táblába kerülnek.

A parancsfájl kiértékelése lustán történik, ami azt jelenti, hogy minden egyes kinyerési és átalakítási lépés egy kifejezésfából és globálisan kiértékelt (az adatfolyam) áll.

A Spark-programok hasonlóak, mivel a Spark-összekötők segítségével olvassa be az adatokat, és hozza létre az adatkereteket, majd alkalmazza az átalakításokat az adatkereteken a LINQ-szerű DSL vagy a SparkSQL használatával, majd írja be az eredményt fájlokba, ideiglenes Spark-táblákba, bizonyos programozási nyelvtípusokat, vagy a konzolt.

## <a name="transform-net-code"></a>.NET kód átalakítása

Az U-SQL kifejezésnyelve A C# , és számos lehetőséget kínál az egyéni .NET-kódok horizontális felskálázására.

Mivel a Spark jelenleg nem támogatja a .NET-kód megvalósítását, vagy át kell írnia a kifejezéseket egy egyenértékű Spark, Scala, Java vagy Python kifejezésbe, vagy meg kell találnia a módját, hogy behívja a .NET-kódot. Ha a parancsfájl .NET tárakat használ, a következő lehetőségek közül választhat:

- Fordítsa le a .NET kódot Scala vagy Python.
- Az U-SQL-parancsfájl felosztása több lépésre, ahol az Azure Batch-folyamatok segítségével alkalmazhatja a .NET átalakításokat (ha elfogadható skálát kaphat)
- A Moebius nevű nyílt forráskódú .NET nyelvi kötést használja. Ez a projekt nincs támogatott állapotban.

Mindenesetre, ha az U-SQL-parancsfájlokban nagy mennyiségű .NET logika található, további útmutatásért lépjen kapcsolatba velünk a Microsoft-fiók képviselőjén keresztül.

A következő részletek a .NET és a C# használat különböző eseteire vonatkozóan találhatók az U-SQL parancsfájlokban.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Skaláris inline U-SQL C# kifejezések átalakítása

Az U-SQL kifejezési nyelve C#. A skaláris inline U-SQL kifejezések nagy része natív módon valósítható meg a jobb teljesítmény érdekében, míg a bonyolultabb kifejezések a .

A Spark saját skaláris kifejezési nyelvvel rendelkezik (akár a DSL részeként, akár a SparkSQL-ben), és lehetővé teszi a felhasználó által definiált függvények hívását a gazdagép nyelvén.

Ha skaláris kifejezésekkel rendelkezik az U-SQL-ben, először keresse meg a legmegfelelőbb natív módon értelmezett Spark skaláris kifejezést a legtöbb teljesítmény érdekében, majd rendelje hozzá a többi kifejezést a Spark-üzemeltetési nyelv felhasználó által definiált függvényébe.

Ne feledje, hogy a .NET és a C# típusszemantikája eltérő, mint a Spark-üzemeltetési nyelvek és a Spark DSL.Ne feledje, hogy a .NET és a C# különböző típusú szemantika, mint a Spark üzemeltetési nyelvek és a Spark DSL. Az [alábbiakban](#transform-typed-values) további részleteket talál a rendszertípus-különbségekről.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Felhasználó által definiált skaláris .NET függvények és felhasználó által definiált összesítők átalakítása

Az U-SQL segítségével tetszőleges skaláris .NET függvényeket hívhat meg, és a .NET-ben írt, felhasználó által definiált összesítőkhívását is lehetővé teszi.

A Spark támogatja a felhasználó által definiált függvényeket és a legtöbb üzemeltetési nyelvén írt felhasználó által definiált összesítőt, amelyek a Spark DSL-jéből és a SparkSQL-ből hívhatók.

### <a name="transform-user-defined-operators-udos"></a>Felhasználó által definiált operátorok (ISO-k) átalakítása

Az U-SQL a felhasználó által definiált operátorok (ISO-k) több kategóriáját biztosítja, például a kivonókat, outputtereket, szűkítőket, processzorokat, applier-eket és kombinálókat, amelyek .NET-ben (és – bizonyos mértékig – Pythonban és R-ben) írhatók.

A Spark nem ugyanazt a bővíthetőségi modellt kínálja az operátorok számára, de egyesek számára egyenértékű képességekkel rendelkezik.

A Spark-egyenértékű elszívók és outputters spark-összekötők. Sok U-SQL-kicsomagolók, előfordulhat, hogy talál egy egyenértékű összekötőt a Spark-közösségben. Mások számára egyéni összekötőt kell írnia. Ha az U-SQL-kicsomagoló összetett, és több .NET-kódtárat használ, célszerű lehet olyan összekötőt építeni a Scala rendszerben, amely interop használatával hívja be a .NET-könyvtárba, amely az adatok tényleges feldolgozását végzi. Ebben az esetben telepítenie kell a .NET Core futásidőt a Spark-fürtre, és meg kell győződnie arról, hogy a hivatkozott .NET-kódtárak .NET Standard 2.0-nak megfelelőek.

A többi típusú U-SQL ISO kell átírni a felhasználó által definiált függvények és összesítők és a szemantikailag megfelelő Spark DLS vagy SparkSQL kifejezés használatával. Például egy processzor leképezhető egy SELECT-hez, amely különböző UDF-meghívásokat foglal le, olyan függvényként csomagolva, amely egy adatkeretet argumentumként vesz fel, és adatkeretet ad vissza.

### <a name="transform-u-sqls-optional-libraries"></a>Az U-SQL választható kódtárainak átalakítása

Az U-SQL opcionális és bemutató kódtárakat kínál, amelyek [Python,](data-lake-analytics-u-sql-python-extensions.md) [R,](data-lake-analytics-u-sql-r-extensions.md) [JSON, XML, AVRO támogatást](https://github.com/Azure/usql/tree/master/Examples/DataFormats)és néhány [kognitív szolgáltatási funkciót kínálnak.](data-lake-analytics-u-sql-cognitive.md)

A Spark saját Python- és R-integrációt, pySpark- és SparkR-integrációt kínál, és összekötőket biztosít a JSON, AZ XML és az AVRO olvasásához és írásához.

Ha át kell alakítania egy, a kognitív szolgáltatások könyvtáraira hivatkozó parancsfájlt, javasoljuk, hogy lépjen kapcsolatba velünk a Microsoft-fiók képviselőjén keresztül.

## <a name="transform-typed-values"></a>Beírt értékek átalakítása

Mivel az U-SQL típusrendszere a .NET típusú rendszeren alapul, és a Spark saját típusú rendszerrel rendelkezik, amelyet a gazdagépnyelvi kötés befolyásol, meg kell győződnie arról, hogy a használt típusok közel vannak, és bizonyos típusok esetében a típustartományok, a pontosság és/vagy a méretezés kissé eltérőek lehetnek. Továbbá az U-SQL és `null` a Spark eltérően kezeli az értékeket.

### <a name="data-types"></a>Adattípusok

Az alábbi táblázat a Spark, a Scala és a PySpark megfelelő típusait adja meg a megadott U-SQL-típusokhoz.

| U-SQL | Spark |  Scala | PySpark között |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

További információkért lásd:

- [org.apache.spark.sql.types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Spark SQL- és DataFrame-típusok](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Scala-értéktípusok](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>A NULL kezelése

A Sparkban az alapértelmezett típusok null értékeket engedélyeznek, míg az U-SQL-ben explicit módon nullázhatóként jelöli meg a skaláris, nem objektumot. Bár a Spark lehetővé teszi, hogy egy oszlopot nem nullázhatóként definiáljon, nem kényszeríti a megkötést, és [rossz eredményt eredményezhet.](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836)

A Sparkban a NULL azt jelzi, hogy az érték ismeretlen. A Spark NULL érték eltér bármely érték, beleértve magát is. Két Spark NULL érték, vagy egy NULL érték és bármely más érték összehasonlítása ismeretlen értéket ad vissza, mert az egyes NULL értékek értéke ismeretlen.  

Ez a viselkedés eltér az U-SQL-től, amely `null` a C# szemantikát követi, ahol eltér bármely értéktől, de önmagával egyenlő.  

Így a `SELECT` SparkSQL `WHERE column_name = NULL` utasítás, amely nulla sort ad vissza, még akkor is, ha null értékek vannak a rendszerben, `column_name`míg az U-SQL-ben a sorokat adja vissza, ahol `column_name` a `null`értéke. Hasonlóképpen a `SELECT` Spark-utasítás, amely nulla sort ad `WHERE column_name != NULL` vissza, még akkor is, ha nem null értékek vannak a rendszerben, `column_name`míg az U-SQL-ben a nem null értékű sorokat adja vissza. Így ha azt szeretné, hogy az U-SQL null-check szemantika, akkor használja [isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) és [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) volt (vagy a DSL-megfelelője).

## <a name="transform-u-sql-catalog-objects"></a>U-SQL katalógusobjektumok átalakítása

Az egyik fő különbség az, hogy az U-SQL-parancsfájlok használhatják a katalógus objektumok, amelyek közül sok nem rendelkezik közvetlen Spark-egyenértékű.

A Spark támogatja a Hive Meta-tároló fogalmait, főként az adatbázisokat és a táblákat, így leképezheti az U-SQL-adatbázisokat és sémákat a Hive-adatbázisokhoz, valamint az U-SQL táblákat a Spark-táblákhoz (lásd: [U-SQL-táblákban tárolt adatok áthelyezése),](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)de nem támogatja a nézeteket, a tábla értékű függvényeket (TVF-ek), a tárolt eljárásokat, az U-SQL-szerelvényeket, a külső adatforrásokat stb.

Az U-SQL kódobjektumok, például a nézetek, tvf-ek, tárolt eljárások és szerelvények a Spark kódfüggvényei és könyvtárai segítségével modellezhetők, és a gazdanyelv funkciójának és eljárási absztrakciós mechanizmusainak használatával hivatkozhatók (például importálással Python-modulok vagy scala-függvényekre való hivatkozás).

Ha az U-SQL katalógust adatok és kódobjektumok projektek és csoportok közötti megosztására használták, akkor a megosztáshoz egyenértékű mechanizmusokat kell használni (például a Maven-t kódobjektumok megosztására).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>U-SQL sorkészlet-kifejezések és SQL-alapú skaláris kifejezések átalakítása

Az U-SQL alapvető nyelve a sorkészletek átalakítása, és az SQL-en alapul. Az alábbiakban az U-SQL-ben a leggyakoribb sorhalmaz-kifejezések nem teljes listája látható:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+Aggregátumok+`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN` kifejezések
- `CROSS`/`OUTER``APPLY` kifejezések
- `PIVOT`/`UNPIVOT`Kifejezések
- `VALUES`sorhalmaz konstruktor

- Kifejezések beállítása`UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Emellett az U-SQL számos SQL-alapú skaláris kifejezést biztosít, például

- `OVER`ablakos kifejezések
- különböző beépített aggregátorok és rangsorolási`SUM`funkciók `FIRST` ( , stb.)
- A legismertebb SQL `CASE`skaláris kifejezések: `LIKE`,`NOT` `IN`, `AND` `OR` , ) , stb.

A Spark a legtöbb ilyen kifejezéshez egyenértékű kifejezéseket kínál dsl-, mind sparkSQL formájában. A Sparkban natívan nem támogatott kifejezések egy részét újra kell írni a natív Spark-kifejezések és a szemantikailag egyenértékű minták kombinációjával. Például `OUTER UNION` le kell fordítani az előrejelzések és a szakszervezetek egyenértékű kombinációjára.

A NULL értékek eltérő kezelése miatt az U-SQL illesztés mindig egyezik egy sortal, ha mindkét összehasonlított oszlop NULL értéket tartalmaz, míg a Spark illesztése nem egyezik meg az ilyen oszlopokkal, kivéve, ha explicit null ellenőrzések et adnak hozzá.

## <a name="transform-other-u-sql-concepts"></a>Más U-SQL fogalmak átalakítása

Az U-SQL számos egyéb szolgáltatást és fogalmat is kínál, például az SQL Server-adatbázisok összevont lekérdezéseit, a paramétereket, a skalár- és lambda-kifejezési változókat, a rendszerváltozókat, `OPTION` a tippeket.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>SQL Server-adatbázisok/külső táblák összevont lekérdezései

Az U-SQL adatforrás- és külső táblákat, valamint közvetlen lekérdezéseket biztosít az Azure SQL Database-hez. Bár a Spark nem kínálja ugyanazokat az objektum-absztrakciókat, [az Azure SQL Database számára spark-összekötőt](../sql-database/sql-database-spark-connector.md) biztosít, amely SQL-adatbázisok lekérdezésére használható.

### <a name="u-sql-parameters-and-variables"></a>U-SQL paraméterek és változók

A paraméterek és a felhasználói változók egyenértékű fogalmakkal rendelkeznek a Sparkban és a tárhelynyelvükben.

A Scala alkalmazásban például megadhat `var` egy változót a következő kulcsszónal:

```
var x = 2 * 3;
println(x)
```

Az U-SQL rendszerváltozói (a `@@`változók kezdve ) két kategóriába sorolhatók:

- Beállítási rendszerváltozók, amelyek meghatározott értékekre állíthatók be, hogy befolyásolják a parancsfájlok viselkedését
- Információs rendszer változók, amelyek érdeklődnek a rendszer- és feladatszintű információk

A legtöbb beállítható rendszerváltozónak nincs közvetlen megfelelője a Sparkban. Az információs rendszer változóinak egy része modellezhető úgy, hogy az információkat argumentumként adja át a feladat végrehajtása során, mások nak is lehet egy ezzel egyenértékű funkciója a Spark üzemeltetési nyelvén.

### <a name="u-sql-hints"></a>U-SQL tanácsok

Az U-SQL számos szintaktikai módot kínál a lekérdezésoptimalizáló és a végrehajtási motorra vonatkozó tippek megadására:  

- U-SQL rendszerváltozó beállítása
- a `OPTION` sorhalmaz kifejezéshez társított záradék, amely adatokat vagy tervcélzást ad meg
- illesztési emlékeztető az illesztési kifejezés szintaxisában (például `BROADCASTLEFT`)

A Spark költségalapú lekérdezés-optimalizálója saját képességekkel rendelkezik a tippek biztosításához és a lekérdezésteljesítmény ének hangolására. Kérjük, olvassa el a megfelelő dokumentációt.

## <a name="next-steps"></a>További lépések

- [A Spark-adatformátumok ismertetése u-SQL-fejlesztők számára](understand-spark-data-formats.md)
- [.NET apache sparkhoz](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Frissítse big data-elemzési megoldásait az Azure Data Lake Storage Gen1-ről az Azure Data Lake Storage Gen2-re](../storage/blobs/data-lake-storage-upgrade.md)
- [Adatok átalakítása az Azure Data Factory Spark-tevékenységével](../data-factory/transform-data-using-spark.md)
- [Adatok átalakítása a Hadoop-struktúra használatával az Azure Data Factoryban](../data-factory/transform-data-using-hadoop-hive.md)
- [Mi az az Azure HDInsight-alapú Apache Spark?](../hdinsight/spark/apache-spark-overview.md)
