---
title: Expression függvények a leképezési adatfolyamban
description: Tudnivalók a Expression functions szolgáltatásról a leképezési adatforgalomban.
author: kromerm
ms.author: makromer
manager: anandsub
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2019
ms.openlocfilehash: 8e607a4447448ee0e822dbee721b701d677d4f63
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197453"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Adatátalakítási kifejezések a leképezési adatfolyamban 

## <a name="expression-functions"></a>Expression függvények

A Data Factoryban az adatátalakítások konfigurálásához használja a leképezési adatfolyam funkciójának kifejezés nyelvét.
___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy szám abszolút értéke.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a koszinusz inverz értékét * ``acos(1) -> 0.0``
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Karakterláncok vagy számok párokat adja meg. A dátum és a napok számát adja hozzá. Egy időtartamot rendel egy időbélyeghez. Egy hasonló típusú tömb hozzáfűzése egy másikhoz. Ugyanaz, mint a + operátor * ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Napok hozzáadása dátumhoz vagy időbélyeghez. Ugyanaz, mint a + operátor a Date * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Hónapok hozzáadása dátumhoz vagy időbélyeghez. Igény szerint átadhat egy időzónát * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai és operátor. Ugyanaz, mint a & & * ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy inverz szinuszos értéket számít ki * ``asin(0) -> 0.0``
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy inverz tangens értéket számít ki * ``atan(0) -> 0.0``
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Azt a szöget adja vissza radiánban, amely a sík pozitív x tengelye és a koordináták * ``atan2(0, 0) -> 0.0``
___
### <code>byName</code>által megadott pont között van. 
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Oszlop értékének kiválasztása a streamben név szerint. A második argumentumként átadhat egy opcionális stream-nevet. Ha több egyezés van, a rendszer az első egyezést adja vissza. Ha nincs egyezés, NULL értéket ad vissza. A visszaadott értéket a Type konverziós függvények egyikének kell átalakítania (TO_DATE, TO_STRING...).  A tervezési időszakban ismert oszlopnevek csak a nevük alapján kezelhetők. A számított bemenetek nem támogatottak, de paraméterekkel való helyettesítést használhat * ``toString(byName('parent'))``
* ``toLong(byName('income'))``
* ``toBoolean(byName('foster'))``
* ``toLong(byName($debtCol))``
* ``toString(byName('Bogus Column'))``
* ``toString(byName('Bogus Column', 'DeriveStream'))``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Oszlop értékének kiválasztása az adatfolyamban lévő relatív pozíció (1 alapú) alapján. Ha a pozíció kívül esik a határértékeken, NULL értéket ad vissza. A visszaadott értéket a Type konverziós függvények egyikének kell átalakítania (TO_DATE, TO_STRING...) A számított bemenetek nem támogatottak, de paraméterekkel való helyettesítést használhat * ``toString(byPosition(1))``
* ``toDecimal(byPosition(2), 10, 2)``
* ``toBoolean(byName(4))``
* ``toString(byName($colName))``
* ``toString(byPosition(1234))``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
A váltakozó feltételek alapján egy vagy több érték is érvényes. Ha a bemenetek száma páros, a másik alapértelmezett értéke NULL az utolsó feltétel * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
* ``cbrt(8) -> 2.0``
___
### <code>ceil</code>szám adatkockájának gyökerét számítja ki 
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
A legkisebb olyan egész számot adja vissza, amely nem kisebb, mint a * ``ceil(-0.1) -> 0``
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Az első nem null értéket adja vissza bemenetek készletében. Minden bemenetnek ugyanolyan típusúnak kell lennie, * ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Összehasonlítja az azonos típusú két értéket. Negatív egész számot ad vissza, ha érték1 < érték2, 0, ha érték1 = = érték2, pozitív érték, ha érték1 > érték2 * ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Változó számú karakterlánc összefűzése egymással. Ugyanaz, mint a + operátor a sztringekkel * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``
* ``isNull('sql' + null) -> true``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Változó számú karakterláncot fűz össze egy elválasztóval. Az első paraméter az elválasztó * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``
___
### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
Igaz értéket ad vissza, ha a megadott tömb bármely eleme igaz értékre értékeli a megadott predikátumban. A tartalmaz egy hivatkozást a predikátum függvény egyik elemére #item * ``contains([1, 2, 3, 4], #item == 3) -> true``
* ``contains([1, 2, 3, 4], #item > 5) -> false``
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a koszinusz értékét * ``cos(10) -> -0.8390715290764524``
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy érték koszinusz hiperbolikusát számítja ki * ``cosh(0) -> 1.0``
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Kiszámítja a különböző primitív adattípusok oszlopának CRC32 kivonatát egy kis hosszúságú értékkel, amely csak 0 (256), 224, 256, 384, 512 értékű lehet. Felhasználható egy sor ujjlenyomatának kiszámításához * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Lekérdezi az aktuális dátumot, amikor a feladatnak futnia kell. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. (https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html) * ``currentDate() == toDate('2250-12-31') -> false``
* ``currentDate('PST')  == toDate('2250-12-31') -> false``
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Lekérdezi az aktuális időbélyeget, amikor a * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``
___
### <code>currentUTC</code>helyi időzónával fut 
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Az aktuális időbélyeg beolvasása UTC-ként. Ha azt szeretné, hogy a jelenlegi idő a fürt időzónája szerint egy másik időzónában legyen értelmezve, a választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában lehet átadni. Alapértelmezés szerint az aktuális időzóna. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). Ha az UTC-időt egy másik időzónára szeretné átalakítani, használja a fromUTC () * ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
A hónap napjának beolvasása * ``dayOfMonth(toDate('2018-06-08')) -> 8``
___
### <code>dayOfWeek</code>dátummal 
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
A hét napját adja meg egy dátummal. 1 – vasárnap, 2 – hétfő..., 7 – szombat * ``dayOfWeek(toDate('2018-06-08')) -> 6``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Az év napjának beolvasása * ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>days</code>dátummal 
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
A * ``days(2) -> 172800000L``
___
### <code>degrees</code>napok számának időtartama (ezredmásodpercben) 
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
A radián értéket * ``degrees(3.141592653589793) -> 180``
___
### <code>divide</code>fok értékre konvertálja 
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Egy pár számot oszt szét. Ugyanaz, mint a/operátor * ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc a megadott karakterlánccal végződik-e * ``endsWith('dumbo', 'mbo') -> true``
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítás egyenlő operátor. Ugyanaz, mint = = operátor * ``equals(12, 24) -> false``
* ``12 == 24 -> false``
* ``'bad' == 'bad' -> true``
* ``isNull('good' == toString(null)) -> true``
* ``isNull(null == null) -> true``
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Az összehasonlítási művelet figyelmen kívül hagyja a kis-és nagybetűket. Ugyanaz, mint a < = > operátor * ``'abc'<=>'Abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
* ``factorial(5) -> 120``
___
### <code>false</code>szám faktoriálisát kiszámítása 
<code><b>false() => boolean</b></code><br/><br/>
Mindig hamis értéket ad vissza. Ha "false" (hamis) nevű oszlop van, akkor használja a függvény szintaxisát (false ()), * ``(10 + 20 > 30) -> false``
* ``(10 + 20 > 30) -> false()``
___
### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
Kiszűri a tömb azon elemeit, amelyek nem felelnek meg a megadott predikátumnak. A szűrő a predikátum függvény egy elemére mutató hivatkozást vár #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
A legnagyobb egész számot adja vissza, amely nem nagyobb, mint a * ``floor(-0.1) -> -1``
___
### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
A megadott karakterlánc kódolása Base64 * ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Az UTC értékre konvertálja az időbélyeget. Igény szerint átadhatja az időzónát "GMT", "PST", "UTC", "Amerika/Kajmán" formátumban. Alapértelmezés szerint az aktuális timezoneRefer Java SimpleDateFormat elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítási nagyobb operátor. Ugyanaz, mint > operátor * ``greater(12, 24) -> false``
* ``('dumbo' > 'dum') -> true``
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítás nagyobb vagy egyenlő operátorral. Ugyanaz, mint a > = operátor * ``greaterOrEqual(12, 12) -> true``
* ``('dumbo' >= 'dum') -> true``
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
A legnagyobb értéket adja vissza az értékek listájában, mivel a bemenet a null értékek kihagyása. Null értéket ad vissza, ha minden bemenet null értékű * ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(10, toInteger(null), 20) -> 20``
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Az oszlop értékének ellenőrzése a streamben név szerint. A második argumentumként átadhat egy opcionális stream-nevet.  A tervezési időszakban ismert oszlopnevek csak a nevük alapján kezelhetők. A számított bemenetek nem támogatottak, de a * ``hasColumn('parent')``
___
### <code>hour</code>paraméteres helyettesítéseket is használhat 
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Egy időbélyeg óra értékének beolvasása. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Az órák számának időtartama (ezredmásodpercben) * ``hours(2) -> 7200000L``
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Egy feltétel alapján egy vagy több érték is érvényes. Ha más nincs meghatározva, akkor NULL értékűnek számít. Mindkét értéknek kompatibilisnek kell lennie (numerikus, string...) * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
Ellenőrzi, hogy az érték nem NULL, és visszaadja-e a másodlagos értéket. Teszteli az összes bemenetet, amíg meg nem találja az első nem null értéket, * ``iifNull(10, 20) -> 10``
* ``iifNull(null, 20, 40) -> 20``
* ``iifNull('bojjus', 'bo', 'dumbo') -> 'dumbo'``
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Ellenőrzi, hogy egy elem szerepel-e a tömbben * ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Minden szó első betűjét nagybetűssé alakítja. A szavakat szóközzel elválasztva azonosítjuk * ``initCap('cool iceCREAM') -> 'Cool Icecream'``
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Megkeresi a karakterláncban található alsztring pozícióját (1 alapján). a 0 értéket adja vissza, ha nem található * ``instr('dumbo', 'mbo') -> 3``
* ``instr('microsoft', 'o') -> 5``
* ``instr('good', 'bad') -> 0``
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor törlésre van-e megjelölve. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek kell lennie, az alapértelmezett érték pedig 1 * ``isDelete()``
* ``isDelete(1)``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor hibásként van-e megjelölve. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek kell lennie, az alapértelmezett érték pedig 1 * ``isError()``
* ``isError(1)``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a rendszer figyelmen kívül hagyja-e a sort. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek kell lennie, az alapértelmezett érték pedig 1 * ``isIgnore()``
* ``isIgnore(1)``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor be van-e jelölve a beszúráshoz. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek kell lennie, az alapértelmezett érték pedig 1 * ``isInsert()``
* ``isInsert(1)``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor egyezik-e a kereséssel. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek kell lennie, az alapértelmezett érték pedig 1 * ``isMatch()``
* ``isMatch(1)``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Ellenőrzi, hogy az érték NULL * ``isNull(NULL()) -> true``
* ``isNull('') -> false``
___
### <code>isUpdate</code>-e 
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor frissítésre van-e megjelölve. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek kell lennie, az alapértelmezett érték pedig 1 * ``isUpdate()``
* ``isUpdate(1)``
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor be van-e jelölve a beszúráshoz. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek kell lennie, az alapértelmezett érték pedig 1 * ``isUpsert()``
* ``isUpsert(1)``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
A hónap utolsó napjának beolvasása * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``
___
### <code>least</code>dátummal 
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Összehasonlítás kisebb vagy egyenlő operátorral. Ugyanaz, mint a < = operátor * ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Egy alsztring kibontása az 1. indexnél a karakterek számával. Ugyanaz, mint az alsztring (Str, 1, n) * ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
* ``length('dumbo') -> 5``
___
### <code>lesser</code>sztring hosszát adja vissza 
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítás nélküli operátor. Ugyanaz, mint < operátor * ``lesser(12, 24) -> true``
* ``('abcd' < 'abc') -> false``
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítás kisebb vagy egyenlő operátorral. Ugyanaz, mint a < = operátor * ``lesserOrEqual(12, 12) -> true``
* ``('dumbo' <= 'dum') -> false``
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Lekéri a levenshtein távolságot két karakterlánc között * ``levenshtein('boys', 'girls') -> 4``
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
A minta egy, a szó szoros egyezésű karakterlánc. A kivételek a következő speciális szimbólumok: _ a bemenet bármelyik karakterének felel meg (ehhez hasonlóan). a POSIX reguláris kifejezésekben a (z)% a bemenet nulla vagy több karakterének felel meg (a következőhöz hasonlóan: POSIX reguláris kifejezés).
Az escape-karakter a következő: "". Ha egy escape-karakter egy speciális szimbólum vagy egy másik escape-karakter előtt következik be, a következő karakter egyeztetése szó szerint történik. A többi karakter Escape-értéke érvénytelen.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Megkeresi az alsztring pozícióját (1) egy adott pozíciót indító karakterláncon belül. Ha a pozíció kimarad, a karakterlánc elejétől számít. a 0 értéket adja vissza, ha nem található * ``locate('mbo', 'dumbo') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Kiszámítja a napló értékét. Egy opcionális alap nem adható meg, ha * ``log(100, 10) -> 2``
___
### <code>log10</code>használatban van egy Euler-szám 
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a log értéket 10 alap * ``log10(100) -> 2``
___
### <code>lower</code>alapján 
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Kisbetűs karakterlánc * ``lower('GunChus') -> 'gunchus'``
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
A bal oldali Kitöltés után a karakterláncot a megadott kitöltéssel kell ellátni, amíg az adott hossz nem lesz. Ha a karakterlánc egyenlő vagy nagyobb, mint a hossz, akkor a * ``lpad('dumbo', 10, '-') -> '-----dumbo'``
* ``lpad('dumbo', 4, '-') -> 'dumb'``
* "" lpad ("Dumbo", 8, "< >"), > < ><dumbo'``
___
### <code></code>LTrim 
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
A bal oldali levágja a kezdő karakterek sztringjét. Ha a második paraméter nem lett megadva, a szóközöket vágja le. Máskülönben a második paraméterben megadott bármely karaktert felvágja * ``ltrim('  dumbo  ') -> 'dumbo  '``
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
A tömb minden elemét egy új elemre képezi le a megadott kifejezés használatával. A Térkép a kifejezés függvény egy elemére mutató hivatkozást vár #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
A tömb minden elemét egy új elemre képezi le a megadott kifejezés használatával. A Térkép a kifejezés függvény egy elemére mutató hivatkozást vár #itemként, és az elem indexére mutató hivatkozást #index * ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Kiszámítja a változó primitív adattípusú oszlopok MD5-kivonatát, és egy 32 karakteres hexadecimális karakterláncot ad vissza. Felhasználható egy sor ujjlenyomatának kiszámításához * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Lekéri egy dátum ezredmásodperc értékét. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Időtartam ezredmásodpercben * ``milliseconds(2) -> 2L``
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Számok kivonása. Kivonás a napok számától számítva. Kivonni időtartama egy időbélyegből. A kivonni két időbélyeget kap, hogy a különbség ezredmásodpercben legyen. Ugyanaz, mint a-Operator * ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Egy időbélyeg perces értékének beolvasása. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
A 
___
### <code>mod</code>* ``minutes(2) -> 120000L``percek közötti időtartam ezredmásodpercben 
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Pár számjegyből álló modulus. Ugyanaz, mint a (z)% operátor * ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Beolvassa a dátum vagy időbélyeg * ``month(toDate('2012-8-8')) -> 8``
___
### <code>monthsBetween</code>a hónap értékét 
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
A két dátum közötti hónapok számának beolvasása. Kikapcsolhatja a számítást. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
A számok összeszorzása. Ugyanaz, mint a * operátor * ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy számot tagad. A pozitív számok negatívra váltása, és fordítva * ``negate(13) -> -13``
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
A következő egyedi sorozatot adja vissza. A szám egymást követő csak egy partíción belül van, és a partitionId * ``nextSequence() == 12313112 -> false``
___
### <code>normalize</code>előre rögzíti 
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
A karakterlánc értékének normalizálása az ékezetes Unicode-karakterek elkülönítéséhez * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai tagadás operátor * ``not(true) -> false``
* ``not(10 == 20) -> true``
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Az összehasonlítás nem egyenlő az operátorral. Ugyanaz, mint a! = operátor * ``12 != 24 -> true``
* ``'bojjus' != 'bo' + 'jjus' -> false``
___
### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Ellenőrzi, hogy az érték nem NULL * ``notNull(NULL()) -> false``
* ``notNull('') -> true``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
NULL értéket ad vissza. Ha van "NULL" nevű oszlop, használja a Function szintaxist (null ()). A által használt összes művelet NULL * ``isNull('dumbo' + null) -> true``
* ``isNull(10 * null) -> true``
* ``isNull('') -> false``
* ``isNull(10 + 20) -> false``
* ``isNull(10/0) -> true``
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai VAGY operátor. Ugyanaz, mint | | * ``or(true, false) -> true``
* ``true || false -> true``
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Pár szám pozitív modulusa.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Az aktuális partíció-azonosítót adja vissza, amely a bemeneti sor * ``partitionId()``
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy számot vet fel egy másik * ``power(10, 2) -> 100``
___
### <code>reduce</code>ra 
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Egy tömb elemeinek felhalmozódása. A csökkentés egy gyűjtőre és egy elemre mutató hivatkozást vár az első kifejezésben #acc és #itemként, és az eredményül kapott értéket a második Expression függvényben használt #resultként * ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Egyező alsztring kinyerése egy adott regex-mintához. Az utolsó paraméter azonosítja az egyeztetési csoportot, és alapértelmezés szerint 1, ha nincs megadva. A (z) "<regex>" (vissza idézőjel) használatával egy sztringnek kell megegyeznie, és nem * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc megfelel-e a megadott regex-mintának. A (z) "<regex>" (vissza idézőjel) használatával egy sztringnek kell megegyeznie, és nem * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Egy regex-minta összes előfordulásának cseréje egy másik, a megadott karakterláncban található alkarakterlánccal, a "<regex>" (vissza idézőjel) értékkel egyező karakterláncot használ, és nem kell megegyeznie * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Egy sztringet a regexen alapuló határolójel alapján feldarabol, és a karakterláncok tömbjét adja vissza * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
Egy alsztring összes előfordulását cserélje le egy másik, az adott karakterláncban szereplő alsztringre. Ha az utolsó paraméter ki van hagyva, a rendszer alapértelmezés szerint üres karakterláncot * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie '``
* ``replace('doggie dog', 'dog') -> 'gie '``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Visszafordít egy karakterláncot * ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Kibont egy alsztringet a jobb oldali karakterek számával. Ugyanaz, mint az alsztring (Str, LENGTH (Str)-n, n) * ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc megfelel-e a megadott regex-mintának * ``rlike('200.50', `(\d+).(\d+)`) -> true``
* ``rlike('bogus', `M[0-9]+.*`) -> false``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Egy számot kerekít egy opcionális skálán és egy opcionális kerekítési módra. Ha a skála nincs megadva, a rendszer alapértelmezés szerint 0 értéket ad meg.  Ha a mód nincs megadva, az alapértelmezett érték ROUND_HALF_UP (5). A kerekítési értékek közé tartozik az 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
A jobb gombbal a karakterláncot a megadott kitöltés alapján kell kiadni, amíg az adott hosszúságú nem lesz. Ha a karakterlánc egyenlő vagy nagyobb, mint a hossz, akkor a * ``rpad('dumbo', 10, '-') -> 'dumbo-----'``
* ``rpad('dumbo', 4, '-') -> 'dumb'``
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``
___
### <code>rtrim</code>RTrim</code>re van kimetszve 
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
A jobb oldali levágja a kezdő karakterek egy sztringjét. Ha a második paraméter nem lett megadva, a szóközöket vágja le. Máskülönben a második paraméterben megadott bármely karaktert felvágja * ``rtrim('  dumbo  ') -> '  dumbo'``
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Egy dátum második értékét kéri le. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
A 
___
### <code>sha1</code>* ``seconds(2) -> 2000L``másodpercben megadott időtartam ezredmásodpercben 
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Kiszámítja a változó primitív adattípusok oszlopának SHA-1 kivonatát, és egy 40 karakteres hexadecimális karakterláncot ad vissza. Felhasználható egy sor ujjlenyomatának kiszámításához * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Kiszámítja a változó primitív adattípusú oszlop SHA-2 kivonatát, amely egy kis hosszúságú, ami csak 0 (256), 224, 256, 384, 512 érték lehet. Felhasználható egy sor ujjlenyomatának kiszámításához * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a szinusz értéket * ``sin(2) -> 0.9092974268256817``
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a szinusz hiperbolikus értéket * ``sinh(0) -> 0.0``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Egy tömb részhalmazának kibontása egy pozícióból. A pozíció 1 alapú. Ha a hossz nincs megadva, a rendszer alapértelmezés szerint a karakterlánc végére * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
Rendezi a tömböt a megadott predikátum függvény használatával. A rendezés két egymást követő elemre mutató hivatkozást vár a kifejezés függvényben #item1 és #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Lekéri a * ``soundex('genius') -> 'G520'``
___
### <code>split</code>sztring Soundex kódját 
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Feldarabol egy karakterláncot egy határolójel alapján, és karakterláncokat ad vissza * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja egy szám négyzet gyökerét * ``sqrt(9) -> 3``
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc a megadott karakterlánccal kezdődik-e * ``startsWith('dumbo', 'du') -> true``
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Hónapok kivonása egy dátumból vagy időbélyegből. Ugyanaz, mint a-operátor a Date * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Hónapok kivonása egy dátumból vagy időbélyegből * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Egy adott hosszúságú karakterlánc kibontása egy adott pozícióból. A pozíció 1 alapú. Ha a hossz nincs megadva, az alapértelmezett érték a karakterlánc végére * ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a tangens értékét * ``tan(0) -> 0.0``
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a hiperbolikus tangens értékét * ``tanh(0) -> 0.0``
___
### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
A megadott karakterlánc kódolása Base64 * ``toBase64('bojjus') -> 'Ym9qanVz'``
___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Bármilyen numerikus/dátum/timestamp/sztring konvertálása bináris ábrázolásra * ``toBinary(3) -> [0x11]``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
A ("", "true", "y", "yes", "1") értéket True ("f", "false", "n", "No", "0") értékre konvertálja hamis és NULL értékre, * ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``isNull(toBoolean('truthy')) -> true``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
A bemeneti dátum karakterláncának dátumra konvertálása egy opcionális bemeneti dátumformátum használatával. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. Ha a bemeneti dátum formátuma nincs megadva, az alapértelmezett formátum: ÉÉÉÉ-[M] M-[d] d. Az elfogadott formátumok a következők: [éééé, éééé-[M] M, éééé-[M] M-[d] d, éééé-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')``
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Bármilyen numerikus vagy sztringet decimális értékké alakít. Ha a pontosság és a skála nincs megadva, a rendszer alapértelmezés szerint a következőt adja meg: (10, 2). Az átalakításhoz választható Java decimális formátumot is használhat. Egy opcionális területi beállítás formátuma BCP47 nyelven, például en-US, de, zh-CN * ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Bármely numerikus vagy sztringet dupla értékre konvertál. Az átalakításhoz választható Java decimális formátumot is használhat. Egy opcionális területi beállítás formátuma BCP47 nyelven, például en-US, de, zh-CN * ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Bármely numerikus vagy sztringet lebegőpontos értékké alakít. Az átalakításhoz választható Java decimális formátumot is használhat. Levágja a dupla * ``toFloat(123.45) -> 123.45f``
* ``toFloat('123.45') -> 123.45f``
* ``toFloat('$123.45', '$###.00') -> 123.45f``
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Bármilyen numerikus vagy sztringet egész értékre konvertál. Az átalakításhoz választható Java decimális formátumot is használhat. Levágja a hosszú, lebegőpontos, dupla * ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Tetszőleges numerikus vagy sztringet alakít át hosszú értékre. Az átalakításhoz választható Java decimális formátumot is használhat. Bármilyen lebegőpontos, dupla * ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``kivágása 
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Bármilyen numerikus vagy sztringet alakít át egy rövid értékre. Az átalakításhoz választható Java decimális formátumot is használhat. Egy egész szám, hosszú, lebegőpontos, dupla * ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code> 
___
### <code>toString</code>csonkítása.<br/><br/>
Egy primitív adattípust karakterlánccá alakít át. A számok és a dátum formátuma megadható. Ha nincs megadva, a rendszer alapértelmezés szerint ki van választva. A Java decimális formátum a számok esetében használatos. Tekintse meg a Java SimpleDateFormat az összes lehetséges dátumformátumot; az alapértelmezett formátum éééé-hh-nn * ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``
* ``toString(4 == 20) -> 'false'``
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Egy karakterláncot egy időbélyeg-formátumra konvertál, és nem kötelező időbélyeg-formátumot. Tekintse meg a Java SimpleDateFormat az összes lehetséges formátumot. Ha az időbélyeg ki van hagyva az alapértelmezett mintának. éééé-[M] M-[d] d óó: PP: SS [. f...] használatos. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. Az időbélyeg legfeljebb ezredmásodperces pontosságot támogat a 999Refer Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Az időbélyeget UTC értékre alakítja. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. Alapértelmezés szerint az aktuális timezoneRefer Java SimpleDateFormat elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Egy karakterkészletet cseréljen fel egy másik karakterkészletre a karakterláncban. A karakterek 1 és 1 közötti helyettesítő * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Egy kezdő és záró karakterből álló karakterláncot metsz. Ha a második paraméter nem lett megadva, a szóközöket vágja le. Máskülönben a második paraméterben megadott bármely karaktert felvágja * ``trim('  dumbo  ') -> 'dumbo'``
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Mindig igaz értéket ad vissza. Ha van "true" nevű oszlop, akkor a függvény szintaxisa (true ()), * ``(10 + 20 == 30) -> true``
* ``(10 + 20 == 30) -> true()``
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Az oszlop típusának felel meg. Csak minta kifejezésekben használható. a szám a rövid, az egész, a hosszú, a dupla, az float vagy a decimális, az integrált egyezések rövid, egész, hosszú, töredékes egyezések dupla, lebegőpontos, decimális és datetime értékkel egyezik * ``typeMatch(type, 'number')``
* ``typeMatch('date', 'datetime')``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Nagybetűs karakterlánc * ``upper('bojjus') -> 'BOJJUS'``
___
### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
A generált UUID * ``uuid()``
___
### <code>weekOfYear</code>t adja vissza 
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Az év hetének beolvasása * ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>weeks</code>dátummal 
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
* ``weeks(2) -> 1209600000L``
___
### <code>xor</code>hetek számának időtartama ezredmásodpercben 
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai XOR operátor. Ugyanaz, mint a ^ operátor * ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Az év értékének beolvasása * ``year(toDate('2012-8-8')) -> 2012``
## összesítési függvényekben a következő függvények csak összesített, pivot, unpivot és Window átalakításokban érhetők el ___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy oszlop értékeinek átlagát * ``avg(sales)``
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
A feltételek alapján a * ``avgIf(region == 'West', sales)``
___
### <code>count</code>egy oszlop értékeinek átlagát kapja 
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Az értékek összesített számának beolvasása. Ha az opcionális oszlop (ok) meg van adva, a rendszer figyelmen kívül hagyja a NULL értékeket a Count * ``count(custId)``
* ``count(custId, custName)``
* ``count()``
* ``count(iif(isNull(custId), 1, NULL))``
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Beolvassa az oszlopok egy csoportjának különböző értékeinek összesített számát * ``countDistinct(custId, custName)``
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
A feltételek alapján lekéri az értékek összesített számát. Ha a választható oszlop meg van adva, a rendszer figyelmen kívül hagyja a NULL értékeket a Count * ``countIf(state == 'CA' && commission < 10000, name)``
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Lekérdezi a sokaság közötti eltérést két oszlop között * ``covariancePopulation(sales, profit)``
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján beolvassa a két oszlop populációs eltérését * ``covariancePopulationIf(region == 'West', sales)``
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Beolvassa a két oszlop minta-eltérését * ``covarianceSample(sales, profit)``
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján beolvassa a két oszlop minta-eltérését * ``covarianceSampleIf(region == 'West', sales, profit)``
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Egy oszlopcsoport első értékének beolvasása. Ha a második paraméter ignoreNulls ki van hagyva, a rendszer hamis értéket feltételez * ``first(sales)``
* ``first(sales, false)``
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Lekéri egy oszlop csúcsosságát * ``kurtosis(sales)``
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop csúcsosságát * ``kurtosisIf(region == 'West', sales)``
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Egy oszlopcsoport utolsó értékének beolvasása. Ha a második paraméter ignoreNulls ki van hagyva, a rendszer hamis értéket feltételez * ``last(sales)``
* ``last(sales, false)``
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Lekéri egy oszlop maximális értékét, * ``max(sales)``
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
A feltételek alapján a * ``maxIf(region == 'West', sales)``
___
### <code>mean</code>egy oszlop maximális értékét kapja meg 
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy oszlop értékeinek középértékét kapja meg. Ugyanaz, mint az AVG * ``mean(sales)``
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
A feltételek alapján egy oszlop értékeinek középértékét kapja meg. Ugyanaz, mint a avgIf * ``meanIf(region == 'West', sales)``
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Lekéri egy oszlop minimális értékét * ``min(sales)``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop minimális értékét, * ``minIf(region == 'West', sales)``
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Lekérdezi egy oszlop torzítását * ``skewness(sales)``
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop torzítását * ``skewnessIf(region == 'West', sales)``
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop szórásának beolvasása * ``stdDev(sales)``
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop szórását * ``stddevIf(region == 'West', sales)``
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Lekérdezi a * ``stddevPopulation(sales)``
___
### <code>stddevPopulationIf</code>egy oszlop populációs szórását 
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján a * ``stddevPopulationIf(region == 'West', sales)``
___
### <code>stddevSample</code>egy oszlopának szórását kapja meg 
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beolvassa a * ``stddevSample(sales)``
___
### <code>stddevSampleIf</code>oszlop szórását 
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján a * ``stddevSampleIf(region == 'West', sales)``
___
### <code>sum</code>oszlopok szórását kapja meg 
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy numerikus oszlop összesített összegének beolvasása * ``sum(col)``
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Lekérdezi egy numerikus oszlop különböző értékeinek összesített összegét * ``sumDistinct(col)``
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
A feltételek alapján lekéri egy numerikus oszlop összesített összegét. A feltétel bármely olyan oszlopon alapulhat, * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``
* ``sumDistinctIf(true, sales)``
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
A feltételek alapján lekéri egy numerikus oszlop összesített összegét. A feltétel bármely olyan oszlopon alapulhat, * ``sumIf(state == 'CA' && commission < 10000, sales)``
* ``sumIf(true, sales)``
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Lekérdezi egy oszlop variancia-* ``variance(sales)``
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján beolvassa egy oszlop * ``varianceIf(region == 'West', sales)``
___
### <code>variancePopulation</code>i eltérését 
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Lekéri egy oszlop populációjának eltérését * ``variancePopulation(sales)``
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop populációjának eltérését * ``variancePopulationIf(region == 'West', sales)``
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Lekérdezi a * ``varianceSample(sales)``
___
### <code>varianceSampleIf</code>egy oszlop elfogulatlan eltérését 
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy adott feltétel alapján beolvassa egy oszlop torzítás nélküli eltérését * ``varianceSampleIf(region == 'West', sales)``
## ablak függvényében a következő függvények csak az ablak átalakításokban érhetők el ___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
A CumeDist függvény kiszámítja egy érték pozícióját a partícióban lévő összes értékhez viszonyítva. Ennek eredményeképpen a partíció rendezésének aktuális sorával megegyező vagy azzal egyenlő sorok száma elosztva a Windows-partíció sorainak teljes számával. A rendezésben szereplő összes döntetlen érték ugyanarra a pozícióra lesz kiértékelve.
* ``cumeDist()``
___
### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
Kiszámítja egy érték rangsorát egy ablak Order by záradékában megadott értékek csoportjából. Az eredmény az a sor, amely a partíció sorrendje szerint az aktuális sorral megegyező vagy annál korábbi sorok számát jelöli. Az értékek nem hoznak létre hézagokat a sorozatban. A sűrű rangsor akkor is működik, ha az adatok nem rendezettek, és a * ``denseRank()``
___
### <code>lag</code>értékekben keresi a változást 
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Az első paraméter értékének beolvasása n sor az aktuális sor előtt. A második paraméter a visszakeresett sorok száma, az alapértelmezett érték pedig 1. Ha nincs annyi sor, a rendszer null értéket ad vissza, hacsak nem ad meg alapértelmezett értéket * ``lag(amount, 2)``
* ``lag(amount, 2000, 100)``
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Az első paraméter értékének beolvasása az aktuális sor után n sorban. A második paraméter a megtekinteni kívánt sorok száma, az alapértelmezett érték pedig 1. Ha nincs annyi sor, a rendszer null értéket ad vissza, hacsak nem ad meg alapértelmezett értéket * ``lead(amount, 2)``
* ``lead(amount, 2000, 100)``
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
A NTile függvény az egyes Windows-partíciók sorait felosztja `n` gyűjtők számára, 1-től egészen a legtöbb `n`ig. A gyűjtő értéke legfeljebb 1 lesz. Ha a partícióban lévő sorok száma nem egyenletesen oszlik meg a gyűjtők számával, akkor a fennmaradó értékeket a rendszer egy gyűjtőre osztja szét, az első gyűjtőtől kezdve. A NTile függvény a tertiles, a quartiles, a deciles és más gyakori összesítő statisztikák kiszámításához hasznos. A függvény két változót számít ki az inicializálás során: a normál gyűjtő méretének egy további sora lesz hozzáadva. Mindkét változó az aktuális partíció méretétől függ. A számítási folyamat során a függvény nyomon követi az aktuális sorszámot, az aktuális gyűjtő számát, valamint azt a sorszámot, amelynél a gyűjtő módosul (bucketThreshold). Ha az aktuális sor száma eléri a gyűjtő küszöbértékét, a rendszer eggyel növeli a gyűjtő értékét, a küszöbértéket pedig a gyűjtő mérete növeli (plusz egy extra, ha az aktuális gyűjtő betömött).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
Kiszámítja egy érték rangsorát egy ablak Order by záradékában megadott értékek csoportjából. Az eredmény az a sor, amely a partíció sorrendje szerint az aktuális sorral megegyező vagy annál korábbi sorok számát jelöli. Az értékek a sorozatban mutatkozó hézagokat eredményezik. A Rank akkor is működik, ha az adatok nem rendezettek, és a * ``rank()``
___
### <code>rowNumber</code>értékek változását keresi 
<code><b>rowNumber() => integer</b></code><br/><br/>
Egymást követő sorszámozást rendel egy ablakban lévő sorokhoz, 1-től kezdődően * ``rowNumber()``

## <a name="next-steps"></a>További lépések

[Ismerje meg a Expression Builder használatát](concepts-data-flow-expression-builder.md).
