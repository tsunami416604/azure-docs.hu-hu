---
title: Lekérdezésgyorsítás SQL nyelvi hivatkozás (előzetes verzió)
titleSuffix: Azure Storage
description: További információ a lekérdezésgyorsítás sql szintaxisának használatáról.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772118"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>Lekérdezésgyorsítás SQL nyelvi hivatkozás (előzetes verzió)

A lekérdezésgyorsítás támogatja az ANSI SQL-szerű nyelvet a blobtartalmakkal kapcsolatos lekérdezések kifejezéséhez.  A lekérdezés gyorsítása SQL dialektus az ANSI SQL egy részhalmaza, amely a támogatott adattípusok, operátorok stb. 

> [!NOTE]
> A lekérdezésgyorsítási szolgáltatás nyilvános előzetes verzióban érhető el, és a Kanadai Közép- és Franciaország-középrégiókban érhető el. A korlátozások áttekintéséhez olvassa el az [Ismert problémák](data-lake-storage-known-issues.md) című cikket. Az előnézetbe való regisztráláshoz tekintse meg [ezt az űrlapot.](https://aka.ms/adls/qa-preview-signup) 

## <a name="select-syntax"></a>SELECT szintaxis

A lekérdezésgyorsítás által támogatott egyetlen SQL utasítás a SELECT utasítás. Ez a példa minden olyan sort ad vissza, amelynek a kifejezésigaz értéket ad vissza.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

CsV formátumú adatok esetén a `BlobStorage` *táblázatnak* a legyen .  Ez azt jelenti, hogy a lekérdezés a REST-hívásban megadott blobon fut.
A JSON-formátumú adatok esetében a *tábla* egy "táblaleíró".   Lásd a [cikk Táblázatleírók](#table-descriptors) című részét.

A következő példában minden olyan soresetében, amelyre a WHERE *kifejezés* igaz értéket ad vissza, ez az utasítás egy új sort ad vissza, amely az egyes vetítési kifejezések kiértékeléséből áll.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

A következő példa egy összesített számítást ad vissza (például egy adott oszlop átlagértéke) minden olyan sorban, amelyre a *kifejezés* igaz értéket ad vissza. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

A következő példa megfelelő eltolásokat ad vissza a CSV-formátumú blob felosztásához.  Tekintse meg a [cikk Sys.Split](#sys-split) című részét.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>Adattípusok

|Adattípus|Leírás|
|---------|-------------------------------------------|
|INT      |64 bites aláírt egész szám.                     |
|Úszó    |64 bites ("dupla pontosságú") lebegőpontos.|
|Karakterlánc   |Változó hosszúságú Unicode karakterlánc.            |
|Időbélyeg|Egy pont az időben.                           |
|Logikai  |TRUE (igaz) vagy FALSE (hamis).                             |

A CSV-formátumú adatok értékeinek olvasásakor az összes érték karakterláncként lesz olvasható.  A karakterlánc-értékek cast kifejezésekkel más típusokká is konvertálhatók.  Az értékek implicit módon a környezettől függően más típusokra is leadhatók. További információ: [Adattípus-elsőbbség (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

## <a name="expressions"></a>Kifejezések

### <a name="referencing-fields"></a>Mezőkre való hivatkozás

A JSON-formátumú adatok, illetve a fejlécsorral rendelkező CSV-formátumú adatok esetében a mezők név szerint is hivatkozhatók.  A mezőnevek et lehet idézni vagy nem idézni. Az idézett mezőnevek idézőjelek közé vannak bezárva ("), szóközöket tartalmazhatnak, és a kis- és nagybetűket is érzékenyen befolyásolják.  A nem idézett mezőnevek nem különböznek a kis- és nagybetűktől, és nem tartalmazhatnak speciális karaktereket.

A CSV-formátumú adatokban a mezőkre sorszámmal is lehet hivatkozni, aláhúzásjellel (_) előtaggal.  Az első mezőre például _1, a tizenegyedik mezőre (_1) lehet hivatkozni.  A mezők sorszámonkénti hivatkozása olyan CSV-formátumú adatok esetében hasznos, amelyek nem tartalmaznak fejlécsort, ebben az esetben egy adott mezőre csak sorszámmal lehet hivatkozni.

### <a name="operators"></a>Operátorok

A következő szabványos SQL-operátorok támogatottak:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Ha az operátor bal és jobb oldalán lévő adattípusok eltérőek, akkor az automatikus konvertálás az itt megadott szabályok szerint történik: [Adattípus-elsőbbség (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

A lekérdezés gyorsítása SQL nyelv támogatja csak egy nagyon kis részét az adattípusok tárgyalt a cikkben.  Lásd a cikk [Adattípusok](#data-types) című részét.

### <a name="casts"></a>Vet

A lekérdezés gyorsítása SQL nyelv támogatja a CAST operátor, a szabályok szerint itt: [Adattípus átalakítása (Database Engine)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

A lekérdezés gyorsítása SQL nyelv támogatja csak egy kis része az adattípusok tárgyalt a cikkben.  Lásd a cikk [Adattípusok](#data-types) című részét.

### <a name="string-functions"></a>Sztringfüggvények

A lekérdezésgyorsítás SQL-nyelv e következő szabványos SQL-karakterlánc-függvényeket támogatja:

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

A [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) funkció segít a minta keresésében. Íme néhány példa, hogy használja a [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) funkciót ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``keresni az adatkarakterlánc .

|Lekérdezés|Példa|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>Dátumfüggvények

A következő szabványos SQL dátumfüggvények támogatottak:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Jelenleg konvertálni az összes [dátum formátumok szabványos IS08601](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>DATE_ADD függvény

A lekérdezés gyorsítása SQL nyelv támogatja év, hónap, ``DATE_ADD`` nap, óra, perc, második a függvény.

Példák:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF függvény

A lekérdezés gyorsítása SQL nyelv támogatja év, hónap, ``DATE_DIFF`` nap, óra, perc, második a függvény.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>KIVONÁS függvény

A ``DATE_ADD`` függvényhez támogatott dátumrésztől eltérő KIBONTÁS esetén a lekérdezésgyorsítás SQL-nyelve támogatja a timezone_hour és a timezone_minute dátumrészként.

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

Ez a táblázat azokat a karakterláncokat ismerteti, ``TO_STRING`` amelyek segítségével megadhatja a függvény kimeneti formátumát.

|Karakterlánc formázása    |Kimenet                               |
|-----------------|-------------------------------------|
|éé               |Év 2 számjegyű formátumban – 1999 mint "99"|
|é                |Év 4 számjegyű formátumban               |
|éééé             |Év 4 számjegyű formátumban               |
|M                |Az év hónapja – 1                    |
|MM               |Nulla párnázott hónap – 01               |
|MMM              |Abbr. év hónapja -JAN            |
|MMMM             |Teljes hónap – május                      |
|n                |A hónap napja (1-31)                  |
|nn               |A hónap nulla párnázott napja (01-31)     |
|a                |AM vagy PM                             |
|ó                |A nap órája (1-12)                   |
|óó               |Nulla párnázott óra od nap (01-12)     |
|H                |A nap órája (0-23)                   |
|HH               |Nulla párnázott napóra (00-23)      |
|m                |Óraperc (0-59)                |
|hh               |Nulla párnázott perc (00-59)           |
|s                |Perc második (0-59)             |
|mm               |Nulla párnázott másodperc (00-59)          |
|S                |Másodperctört (0,1-0,9)        |
|SS               |Másodperctört (0,01-0,99)      |
|Éer              |Másodperctört (0.001-0.999)    |
|X                |Eltolás órában                      |
|XX vagy XXXX       |Eltolás órában és percben (+0430)  |
|XXX vagy XXXXX     |Eltolás órában és percben (-07:00) |
|x                |Eltolás órában (7)                  |
|xx vagy xxxx       |Eltolás órában és percben (+0530)    |
|Xxx vagy xxxxx     |Eltolás órában és percben (+05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP függvény

Csak IS08601 formátumok támogatottak.

Példák:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> A ``UTCNOW`` funkció segítségével is lehetővé teheti a rendszeridőt.


## <a name="aggregate-expressions"></a>Összesítő kifejezések

A SELECT utasítás egy vagy több vetítési kifejezést vagy egyetlen összesítő kifejezést tartalmazhat.  A következő összesítő kifejezések támogatottak:

|Kifejezés|Leírás|
|--|--|
|[DARAB(\*)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |A predikátumkifejezésnek megfelelő rekordok számát adja eredményül.|
|[DARAB(kifejezés)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Azon rekordok számát adja eredményül, amelyek kifejezése nem null értékű.|
|[ÁTLAG(kifejezés)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |A kifejezés nem null értékű értékeinek átlagát adja eredményül.|
|[MIN(kifejezés)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |A kifejezés minimális nem null értékét adja eredményül.|
|[MAX(kifejezés)](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15)    |A kifejezés maximális nem null értékét adja eredményül.|
|[SZUM(kifejezés)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |A kifejezés összes nem null értékének összegét adja eredményül.|

### <a name="missing"></a>Hiányzó

Az ``IS MISSING`` operátor az egyetlen nem szabványos, amelyet a lekérdezésgyorsítás SQL-nyelv támogat.  JSON-adatok esetén, ha egy mező hiányzik egy ``IS MISSING`` adott bemeneti rekordból, a kifejezésmező értéke igaz.

<a id="table-descriptors" />

## <a name="table-descriptors"></a>Táblaleírók

CsV-adatok esetén a tábla `BlobStorage`neve mindig .  Például:

```sql
SELECT * FROM BlobStorage
```

A JSON-adatok kal kapcsolatban további lehetőségek állnak rendelkezésre:

```sql
SELECT * FROM BlobStorage[*].path
```

Ez lehetővé teszi a JSON-adatok részhalmazai lekérdezéseit.

JSON-lekérdezések esetén az elérési utat a FROM záradék egy részében is megemlítheti. Ezek az elérési utak segítenek a JSON-adatok részhalmazának elemzésében. Ezek az elérési utak jsontömb- és objektumértékekre hivatkozhatnak.

Vegyünk egy példát, hogy megértsük ezt részletesebben.

Ezek a mintaadataink:

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

Lehet, hogy csak `warehouses` a fenti adatokból származó JSON-objektum érdekli. Az `warehouses` objektum JSON-tömbtípus, így ezt a FROM záradékban is megemlítheti. A mintalekérdezés valahogy így nézhet ki.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

A lekérdezés beszerzi az összes mezőt, de csak a szélességi fokot választja ki.

Ha csak a `dimensions` JSON-objektum értékét szeretné elérni, akkor a lekérdezésben hivatkozhat az objektumra. Például:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Ez korlátozza az `dimensions` objektum tagjaihoz való hozzáférést is. Ha a JSON-mezők más tagjait és a JSON-objektumok belső értékeit szeretné elérni, akkor a következő példában látható lekérdezéseket is használhatja:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> A BlobStorage és\*a BlobStorage[ ] egyaránt a teljes objektumra hivatkozik. Ha azonban van egy elérési út a FROM záradékban, akkor\*a BlobStorage[ ].path

<a id="sys-split" />

## <a name="syssplit"></a>Sys.Split

Ez a SELECT utasítás speciális formája, amely csak CSV-formátumú adatok esetén érhető el.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Használja ezt az utasítást azokban az esetekben, amikor le szeretné tölteni, majd kötegekben fel szeretné dolgozni a CSV-adatrekordokat. Így a rekordokat párhuzamosan dolgozhatja fel ahelyett, hogy egyszerre kellene letöltenie az összes rekordot. Ez az utasítás nem ad vissza rekordokat a CSV-fájlból. Ehelyett kötegméretek gyűjteményét adja vissza. Ezután az egyes kötegek mérete segítségével lekérheti az adatrekordok kötegét. 

A *split_size* paraméterrel megadhatja, hogy az egyes kötegek hány bájtot tartalmazzanak. Ha például egyszerre csak 10 MB adatot szeretne feldolgozni, akkor az utasítás `SELECT sys.split(10485760)FROM BlobStorage` így fog kinézni: mivel a 10 MB 10 485 760 bájtnak felel meg. Minden köteg annyi rekordot tartalmaz, amennyi elfér a 10 MB-ba. 

A legtöbb esetben az egyes kötegek mérete valamivel nagyobb lesz, mint a megadott szám. Ennek az az oka, hogy egy köteg nem tartalmazhat részleges rekordot. Ha egy köteg utolsó rekordja a küszöbérték vége előtt kezdődik, a köteg nagyobb lesz, így a teljes rekordot is tartalmazhatja. Az utolsó köteg mérete valószínűleg kisebb lesz, mint a megadott méret.

>[!NOTE]
> A split_size legalább 10 MB-nak (10485760) kell lennie.

## <a name="see-also"></a>Lásd még

- [Azure Data Lake Storage lekérdezésgyorsítás (előzetes verzió)](data-lake-storage-query-acceleration.md)
- [Adatok szűrése az Azure Data Lake Storage lekérdezésgyorsításával (előzetes verzió)](data-lake-storage-query-acceleration-how-to.md)

