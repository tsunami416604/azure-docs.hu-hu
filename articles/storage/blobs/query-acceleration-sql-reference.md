---
title: A lekérdezés gyorsításának SQL nyelvi referenciája (előzetes verzió)
titleSuffix: Azure Storage
description: Ismerje meg, hogyan használható a lekérdezési gyorsítás SQL-szintaxisa.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81772118"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>A lekérdezés gyorsításának SQL nyelvi referenciája (előzetes verzió)

A lekérdezési gyorsítás egy ANSI SQL-szerű nyelvet támogat a blob-tartalmakon keresztüli lekérdezések kifejezésére.  A lekérdezés-gyorsítási SQL-dialektus az ANSI SQL egy részhalmaza, amely korlátozott számú támogatott adattípust, operátort stb., de az ANSI SQL-ben is kibővül, hogy támogassa a többtényezős, félig strukturált adatformátumokat, például a JSON-t. 

> [!NOTE]
> A lekérdezési gyorsítási funkció nyilvános előzetes verzióban érhető el, és a közép-Kanada középső régiójában és Közép-Franciaországban található. A korlátozások áttekintéséhez tekintse meg az [ismert problémákkal foglalkozó](data-lake-storage-known-issues.md) cikket. Az előzetes verzióra való regisztráláshoz tekintse meg [ezt az űrlapot](https://aka.ms/adls/qa-preview-signup). 

## <a name="select-syntax"></a>Szintaxis kiválasztása

A lekérdezési gyorsítás által támogatott egyetlen SQL-utasítás a SELECT utasítás. Ez a példa minden olyan sort ad vissza, amelynél a kifejezés igaz értéket ad vissza.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

CSV formátumú adat esetén a *táblának* a következőnek kell lennie `BlobStorage`:.  Ez azt jelenti, hogy a lekérdezés a REST-hívásban megadott blobon fog futni.
JSON formátumú adat esetén a *tábla* a "táblázat leírója".   Lásd a jelen cikk [táblázatos descripters](#table-descriptors) című szakaszát.

Az alábbi példában minden olyan sorra, amelynél a WHERE *kifejezés* igaz értéket ad vissza, ez az utasítás egy új sort fog visszaadni, amely az egyes leképezési kifejezések kiértékelésével készül.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

Az alábbi példa egy összesített számítást ad vissza (például egy adott oszlop átlagos értékét) az egyes sorokon, amelyek esetében a *kifejezés* igaz értéket ad vissza. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

A következő példa egy CSV-formátumú blob felosztására alkalmas eltolásokat ad vissza.  Tekintse meg a jelen cikk [sys. Split](#sys-split) című szakaszát.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>Adattípusok

|Adattípus|Leírás|
|---------|-------------------------------------------|
|INT      |64 bites előjeles egész szám.                     |
|FLOAT    |64 bites ("dupla pontosságú") lebegőpontos pont.|
|KARAKTERLÁNC   |Változó hosszúságú Unicode-karakterlánc.            |
|IDŐBÉLYEG|Egy adott időpontban.                           |
|LOGIKAI  |TRUE (igaz) vagy FALSE (hamis).                             |

A CSV formátumú adatokból származó értékek olvasásakor az összes érték karakterláncként van beolvasva.  A karakterlánc-értékek a CAST kifejezéseket használó más típusokra is átalakíthatók.  Az értékek implicit módon más típusokra is felhelyezhetők a környezettől függően. További információ: [adattípus-prioritás (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

## <a name="expressions"></a>Kifejezések

### <a name="referencing-fields"></a>Viszonyítási mezők

A JSON-formátumú adat vagy a CSV formátumú, fejléctel formázott adat esetén a mezőket név alapján lehet hivatkozni.  A mezőnevek idézőjelek vagy nem idézőjelek lehetnek. Az idézőjeles mezők neve idézőjelek közé (") van lefoglalva, tartalmazhat szóközt, és megkülönbözteti a kis-és nagybetűket.  A nem jegyzett mezők neve kis-és nagybetűk megkülönböztetése, és nem tartalmazhat speciális karaktereket.

CSV-formázott adat esetén a mezőket a sorszám alapján is hivatkozhatjuk, aláhúzás (_) karakterrel.  Előfordulhat például, hogy az első mező _1-ként hivatkozik, vagy a tizenegyedik mező _11 néven hivatkozik rá.  A mezők sorszám szerinti hivatkozási értéke olyan CSV-formázott adatok esetében hasznos, amelyek nem tartalmaznak fejlécsort, ebben az esetben az egyetlen lehetőség, hogy egy adott mezőre hivatkozzon a sorszám.

### <a name="operators"></a>Operátorok

A következő szabványos SQL-operátorok támogatottak:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Ha az operátor bal és jobb oldalán lévő adattípusok eltérnek, akkor az automatikus konverzió az itt megadott szabályoknak megfelelően lesz elvégezve: [adattípus-prioritás (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

A lekérdezés gyorsításának SQL-nyelve csak a cikkben tárgyalt adattípusok nagyon kis részhalmazát támogatja.  Tekintse meg a jelen cikk [adattípusok](#data-types) című szakaszát.

### <a name="casts"></a>Típuskonverziók

A lekérdezés gyorsításának SQL-nyelve támogatja a CAST operátort a szabályoknak megfelelően: [adattípus-átalakítás (adatbázismotor)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

A lekérdezés gyorsításának SQL-nyelve csak a cikkben tárgyalt adattípusok kis részhalmazát támogatja.  Tekintse meg a jelen cikk [adattípusok](#data-types) című szakaszát.

### <a name="string-functions"></a>Sztringfüggvények

A lekérdezés gyorsításának SQL-nyelve a következő szabványos SQL-karakterlánc-függvényeket támogatja:

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

Íme néhány példa:

|Függvény|Példa|Eredmény|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

A [Like](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) függvény segítségével megkeresheti a mintázatot. Íme néhány példa, amely a [Like](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) függvényt használja az adatkarakterlánc ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``kereséséhez.

|Lekérdezés|Példa|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>Dátumfüggvények

A következő szabványos SQL Date függvények támogatottak:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Jelenleg a [szabványos IS08601 összes dátumformátum-formátumát](https://www.w3.org/TR/NOTE-datetime)konvertáljuk. 

#### <a name="date_add-function"></a>DATE_ADD függvény

A lekérdezés gyorsításának SQL nyelve az ``DATE_ADD`` év, hónap, nap, óra, perc, másodperc a függvényhez való használatát támogatja.

Példák:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF függvény

A lekérdezés gyorsításának SQL nyelve az ``DATE_DIFF`` év, hónap, nap, óra, perc, másodperc a függvényhez való használatát támogatja.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>Kinyerési függvény

A ``DATE_ADD`` függvény által támogatott, a Date résztől eltérő Kibontás esetén a lekérdezési gyorsítás SQL nyelve a timezone_hour és az timezone_minutet is támogatja.

Példák:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING függvény

Példák:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

Ez a táblázat azokat a karakterláncokat ismerteti, amelyeket a ``TO_STRING`` függvény kimeneti formátumának megadásához használhat.

|Formázó sztring    |Kimenet                               |
|-----------------|-------------------------------------|
|éé               |Év 2 számjegyű formátumban – 1999 as "99"|
|é                |Év 4 számjegyű formátumban               |
|éééé             |Év 4 számjegyű formátumban               |
|M                |Év hónapja – 1                    |
|MM               |Nulla párnázott hónap – 01               |
|MMM              |Abbr. Év hónapja – JAN            |
|MMMM             |Teljes hónap – május                      |
|n                |Hónap napja (1-31)                  |
|nn               |Nulla (nap) – hónap (01-31)     |
|a                |AM vagy du                             |
|ó                |Nap órája (1-12)                   |
|óó               |Nulla párnázott óra od nap (01-12)     |
|H                |Nap órája (0-23)                   |
|HH               |Nulla párnázott óra (00-23)      |
|m                |Perc/óra (0-59)                |
|hh               |Nulla párnázott perc (00-59)           |
|s                |Percek másodpercben (0-59)             |
|mm               |Nulla párnázott másodperc (00-59)          |
|S                |Másodpercek hányada (0,1-0.9)        |
|SS               |Másodpercek hányada (0.01-0,99)      |
|ÉER              |Másodpercek hányada (0,001-0.999)    |
|X                |Eltolás órában                      |
|XX vagy XXXX       |Eltolás óra és percben (+ 0430)  |
|XXX vagy XXXXX     |Eltolás óra és percben (-07:00) |
|x                |Eltolás órában (7)                  |
|XX vagy XXXX       |Eltolás óra és perc között (+ 0530)    |
|XXX vagy XXXXX     |Eltolás óra és perc között (+ 05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP függvény

Csak IS08601-formátumok támogatottak.

Példák:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> A rendszeridőt a ``UTCNOW`` függvény használatával is lekérheti.


## <a name="aggregate-expressions"></a>Összesítő kifejezések

A SELECT utasítás tartalmazhat egy vagy több leképezési kifejezést, vagy egyetlen összesítő kifejezést is.  A következő összesített kifejezések támogatottak:

|Kifejezés|Leírás|
|--|--|
|[DARABSZÁM (\*)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |A predikátum kifejezésének megfelelő rekordok számát adja vissza.|
|[DARABSZÁM (kifejezés)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Azon rekordok számát adja vissza, amelyek esetében a kifejezés nem null értékű.|
|[ÁTLAG (kifejezés)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |A kifejezés null értéktől eltérő értékeinek átlagát adja vissza.|
|[MIN (kifejezés)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |A kifejezés minimális nem null értékű értékét adja vissza.|
|[Max (kifejezés](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15))    |A kifejezés nem null értékű maximális értékét adja vissza.|
|[SUM (kifejezés)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |A kifejezés összes nem null értékének összegét adja vissza.|

### <a name="missing"></a>HIÁNYZÓ

Az ``IS MISSING`` operátor az egyetlen nem szabványos, amelyet a lekérdezés GYORSÍTÁSának SQL-nyelve támogat.  JSON-adatok esetén, ha egy mező hiányzik egy adott bemeneti rekordból, a kifejezés mező ``IS MISSING`` az igaz logikai értéket fogja kiértékelni.

<a id="table-descriptors" />

## <a name="table-descriptors"></a>Tábla leírói

CSV-adatként a tábla neve mindig `BlobStorage`.  Például:

```sql
SELECT * FROM BlobStorage
```

A JSON-információk esetében további lehetőségek érhetők el:

```sql
SELECT * FROM BlobStorage[*].path
```

Ez lehetővé teszi, hogy a lekérdezések a JSON-adat alkészletei felett legyenek.

A JSON-lekérdezések esetében a FROM záradék részét képező elérési utat is megemlítheti. Ezek az elérési utak segítenek a JSON-adathalmazok elemzésében. Ezek az elérési utak a JSON-tömbre és az objektumok értékére is hivatkozhatnak.

Vegyük például ezt részletesebben.

Ez a mintaadatok:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

Lehet, hogy csak a fenti adatokból származó `warehouses` JSON-objektum érdekli. Az `warehouses` objektum egy JSON-tömb típusa, ezért ezt a FROM záradékban lehet megemlíteni. A mintául szolgáló lekérdezés valahogy így néz ki.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

A lekérdezés lekérdezi az összes mezőt, de csak a földrajzi szélességet választja.

Ha csak a `dimensions` JSON-objektum értékének elérését szeretné elérni, a lekérdezésben használhatja az adott objektumra vonatkozó hivatkozásokat. Például:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Ez korlátozza a hozzáférést az `dimensions` objektum tagjaihoz is. Ha a JSON-mezők más tagjait és a JSON-objektumok belső értékeit is el szeretné érni, akkor olyan lekérdezéseket használhat, mint például az alábbi példában látható:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> A BlobStorage és a\*BlobStorage [] mindkettő a teljes objektumra vonatkozik. Ha azonban a FROM záradékban van egy elérési út, akkor a következőt kell használnia:\*BlobStorage []. Path

<a id="sys-split" />

## <a name="syssplit"></a>Sys. Split

Ez a SELECT utasítás speciális formája, amely csak a CSV-formátumú adatokhoz érhető el.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Használja ezt az utasítást azokban az esetekben, amikor le szeretné tölteni, majd feldolgozza a CSV-adatrekordokat a kötegekben. Így párhuzamosan feldolgozhatja a rekordokat ahelyett, hogy egyszerre le kellene töltenie az összes rekordot. Ez az utasítás nem ad vissza rekordokat a CSV-fájlból. Ehelyett a Batch-méretek gyűjteményét adja vissza. Ezután az egyes batch-méretekkel lekérheti az adatrekordok kötegét. 

A *split_size* paraméterrel adhatja meg, hogy hány bájtot szeretne használni a kötegek. Ha például egyszerre csak 10 MB-nyi adat feldolgozását szeretné feldolgozni, akkor a következőhöz hasonló utasítást kell kinéznie `SELECT sys.split(10485760)FROM BlobStorage` : mivel a 10 mb értéke 10 485 760 bájt. Minden köteg annyi rekordot fog tartalmazni, amelyek elférnek a 10 MB-ban. 

A legtöbb esetben az egyes kötegek mérete valamivel nagyobb lesz, mint a megadott szám. Ennek oka, hogy egy köteg nem tartalmazhat részleges rekordot. Ha egy köteg utolsó rekordja a küszöb vége előtt indul el, a köteg nagyobb lesz, hogy a teljes rekordot is tartalmazza. Az utolsó köteg mérete valószínűleg kisebb lesz, mint a megadott méret.

>[!NOTE]
> A split_sizenak legalább 10 MB (10485760) méretűnek kell lennie.

## <a name="see-also"></a>Lásd még

- [Azure Data Lake Storage lekérdezési gyorsítás (előzetes verzió)](data-lake-storage-query-acceleration.md)
- [Az Adatszűrés Azure Data Lake Storage lekérdezési gyorsítással (előzetes verzió)](data-lake-storage-query-acceleration-how-to.md)

