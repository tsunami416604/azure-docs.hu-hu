---
title: Kifejezésfüggvények a leképezési adatfolyamban
description: További információ a kifejezésfüggvényekről az adatfolyam leképezésében.
author: kromerm
ms.author: makromer
manager: anandsub
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2019
ms.openlocfilehash: 1b87fa795047070db9a10ceec4b69dd6f7c042a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217193"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Adatátalakítási kifejezések az adatfolyam leképezésében 

## <a name="expression-functions"></a>Kifejezésfüggvények

A Data Factory ban használja a leképezési adatfolyam-szolgáltatás kifejezésnyelvét az adatátalakítások konfigurálásához.
___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy szám abszolút értéke.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Koszinusz inverz értékének számítása* ``acos(1) -> 0.0``
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Karakterláncokat vagy számokat ad hozzá. Dátumot ad hozzá a napok számához. Időtartam hozzáadása egy időbélyeghez. Egy hasonló típusú tömbhozzáfűzése egy másikhoz. Ugyanaz, mint a + operátor* ``add(10, 20) -> 30``
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
Napok hozzáadása dátumhoz vagy időbélyeghez. Megegyezik a dátum + operátorral* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Hónapok hozzáadása dátumhoz vagy időbélyeghez. Tetszés szerint átléphet egy időzónán* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai ÉS operátor. Ugyanaz, mint && * ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Inverz szinázsértéket számít ki* ``asin(0) -> 0.0``
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Inverz érintőérték számítása* ``atan(0) -> 0.0``
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy sík pozitív x tengelye és a koordináták által megadott pont közötti radiánszöget számítja ki.* ``atan2(0, 0) -> 0.0``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Az adatfolyamban név szerint jelöl ki egy oszlopértéket. Második argumentumként megadhatja a nem kötelező adatfolyam-nevet. Ha több találat van, az első találatot visszaadja. Ha nincs egyezés, akkor null értéket ad vissza. A visszaadott értéket a típuskonverziós függvények (TO_DATE, TO_STRING ...) által konvertálva kell konvertálni.  A tervezés időpontjában ismert oszlopneveket csak a nevükkel kell megcímezni. A számított bemenetek nem támogatottak, de paraméterhelyettesítéseket is használhat* ``toString(byName('parent'))``
* ``toLong(byName('income'))``
* ``toBoolean(byName('foster'))``
* ``toLong(byName($debtCol))``
* ``toString(byName('Bogus Column'))``
* ``toString(byName('Bogus Column', 'DeriveStream'))``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Az oszlopértéket az adatfolyamban lévő relatív pozíciója (1 alapú) alapján választja ki. Ha a pozíció kívül esik a határokon, null értéket ad vissza. A visszaadott értéket a típuskonverziós függvények (TO_DATE, TO_STRING ...) által konvertáló típussal kell konvertálni. A számított bemenetek nem támogatottak, de paraméterhelyettesítéseket is használhat* ``toString(byPosition(1))``
* ``toDecimal(byPosition(2), 10, 2)``
* ``toBoolean(byName(4))``
* ``toString(byName($colName))``
* ``toString(byPosition(1234))``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Alapján váltakozó feltételek vonatkozik az egyik vagy a másik. Ha a bemenetek száma páros, a másik értéke NULL az utolsó feltételnél* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Szám kockagyökének kiszámítása* ``cbrt(8) -> 2.0``
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
A legkisebb egész számot adja eredményül, amely nem kisebb a számnál.* ``ceil(-0.1) -> 0``
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Az első nem null értéket adja eredményül egy bemeneti halmazból. Minden bemenetnek azonos típusúnak kell lennie* ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Két azonos típusú értéket hasonlít össze. Negatív egész értéket ad eredményül, ha az érték1 < érték2, 0, ha az érték1 == érték2, pozitív érték, ha 1 > érték2.* ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Változó számú karakterláncösszefűzése. Megegyezik a karakterláncokkal rendelkező + operátorral* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``
* ``isNull('sql' + null) -> true``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Változó számú karakterláncot fűz össze egy elválasztóval együtt. Az első paraméter az elválasztó* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``
___
### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
Igaz értéket ad vissza, ha a megadott tömb bármely eleme igazként értékelhető a megadott predikátumban. A tartalmaz függvény a predikátum függvény egyik elemére való hivatkozást #item* ``contains([1, 2, 3, 4], #item == 3) -> true``
* ``contains([1, 2, 3, 4], #item > 5) -> false``
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Koszinuszérték számítása* ``cos(10) -> -0.8390715290764524``
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Érték koszinusz hiperbolikuszának kiszámítása* ``cosh(0) -> 1.0``
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Kiszámítja a különböző primitív adattípusok oszlopkészletének CRC32 kivonatát bithosszban, amely csak 0(256), 224, 256, 384, 512 értékben lehet. Ezt fel lehet használni, hogy kiszámítja az ujjlenyomat egy sor* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Lejárata az aktuális dátumot, amikor a feladat elindul. Egy opcionális időzónát "GMT", "PST", "UTC", "America/Cayman" formátumban adhat át. A helyi időzóna lesz az alapértelmezett. Lásd a Java SimpleDateFormat a rendelkezésre álló formátumokat. ["https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html)
* ``currentDate() == toDate('2250-12-31') -> false``
* ``currentDate('PST')  == toDate('2250-12-31') -> false``
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Az aktuális időbélyeg lenyelve, amikor a feladat a helyi időzónával kezd futni* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Az aktuális időbélyeg betöltése UTC-ként. Ha azt szeretné, hogy az aktuális időt a fürt időzónájánál eltérő időzónában értelmezze, akkor egy opcionális időzónát adhat át "GMT", "PST", "UTC", "America/Cayman" formátumban. Alapértelmezés szerint az aktuális időzónára van betöltés. Lásd a Java SimpleDateFormat a rendelkezésre álló formátumokat. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). Az UTC-idő konvertálása másik időzónára a UTC()* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Megkapja a hónap napját egy dátummal* ``dayOfMonth(toDate('2018-06-08')) -> 8``
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Megkapja a napot a hét adott dátumot. 1 - vasárnap, 2 - hétfő ..., 7 - szombat* ``dayOfWeek(toDate('2018-06-08')) -> 6``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Megkapja az év napját egy dátummal* ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Időtartam ezredmásodpercben napok számában* ``days(2) -> 172800000L``
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Radiánok fokká alakítása* ``degrees(3.141592653589793) -> 180``
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Elosztja a pár számot. Ugyanaz, mint a / operátor* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc a megadott karakterlánccal végződik-e* ``endsWith('dumbo', 'mbo') -> true``
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Az összehasonlítás egyenlő operátorral. Megegyezik az == operátorral* ``equals(12, 24) -> false``
* ``12 == 24 -> false``
* ``'bad' == 'bad' -> true``
* ``isNull('good' == toString(null)) -> true``
* ``isNull(null == null) -> true``
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Az összehasonlítás egyenlő az eset figyelmen kívül hagyásával foglalkozó operátorral. Megegyezik <=> operátorral* ``'abc'<=>'Abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Szám faktoriálisának kiszámítása* ``factorial(5) -> 120``
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Mindig hamis értéket ad vissza. Használja a függvény szintaxisát(false()), ha van egy "false" nevű oszlop.* ``(10 + 20 > 30) -> false``
* ``(10 + 20 > 30) -> false()``
___
### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
Kiszűri a tömb olyan elemeit, amelyek nem felelnek meg a megadott predikátumnak. A szűrő a predikátumfüggvény egyik elemére #item* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
A legnagyobb egész számot adja eredményül, amely nem nagyobb, mint a szám.* ``floor(-0.1) -> -1``
___
### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
A megadott karakterlánc kódolása a base64-ben* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Átalakítja az utc időbélyeggé. Az időzónát opcionálisan "GMT", "PST", "UTC", "America/Cayman" formában is átadhatja. Ez alapértelmezés szerint az aktuális timezoneUtalja a Java SimpleDateFormat a rendelkezésre álló formátumokat. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítás nagyobb operátor. Megegyezik > operátorral* ``greater(12, 24) -> false``
* ``('dumbo' > 'dum') -> true``
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Az összehasonlítás nagyobb vagy egyenlő operátornál nagyobb. Megegyezik >= operátorral* ``greaterOrEqual(12, 12) -> true``
* ``('dumbo' >= 'dum') -> true``
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
A legnagyobb értéket adja eredményül az értékek között, ha a bemeneti értékeket kihagyom. Null értéket ad vissza, ha az összes bemenet null értékű* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(10, toInteger(null), 20) -> 20``
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Oszlopérték ellenőrzése név szerint az adatfolyamban. Második argumentumként megadhatja a nem kötelező adatfolyam-nevet.  A tervezés időpontjában ismert oszlopneveket csak a nevükkel kell megcímezni. A számított bemenetek nem támogatottak, de paraméterhelyettesítéseket is használhat* ``hasColumn('parent')``
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Egy időbélyeg óraértékét kapja meg. Egy opcionális időzónát "GMT", "PST", "UTC", "America/Cayman" formátumban adhat át. A helyi időzóna lesz az alapértelmezett. Lásd a Java SimpleDateFormat a rendelkezésre álló formátumokat. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Időtartam ezredmásodpercben órák számában* ``hours(2) -> 7200000L``
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Egy feltétel alapján alkalmazza az egyik vagy a másik értéket. Ha a másik nincs megadva, akkor null értékűnek minősül. Mindkét értéknek kompatibilisnek kell lennie(numerikus, karakterlánc...)* ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
Ellenőrzi, hogy az érték NEM NULL, és azt visszaadja, és a másik értéket adja vissza. Az összes bemenetet addig teszteli, amíg meg nem találja az első nem null értéket* ``iifNull(10, 20) -> 10``
* ``iifNull(null, 20, 40) -> 20``
* ``iifNull('bojjus', 'bo', 'dumbo') -> 'dumbo'``
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Annak ellenőrzése, hogy egy elem szerepel-e a tömbben* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Minden szó első betűjét nagybetűssé alakítja. A szavakat szóközsel elválasztva azonosítják* ``initCap('cool iceCREAM') -> 'Cool Icecream'``
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Megkeresi a karakterlánc részkarakterláncának pozícióját(1) egy karakterláncon belül. 0 ad vissza, ha nem található* ``instr('dumbo', 'mbo') -> 3``
* ``instr('microsoft', 'o') -> 5``
* ``instr('good', 'bad') -> 0``
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor törlésre van-e megjelölve. Egynél több bemeneti adatfolyamot vevő átalakítások esetén átadhatja az adatfolyam (1-alapú) indexét. Az adatfolyam-indexnek 1-nek vagy 2-nek, az alapértelmezett értéknek pedig 1-nek kell lennie.* ``isDelete()``
* ``isDelete(1)``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor hibaként van-e megjelölve. Egynél több bemeneti adatfolyamot vevő átalakítások esetén átadhatja az adatfolyam (1-alapú) indexét. Az adatfolyam-indexnek 1-nek vagy 2-nek, az alapértelmezett értéknek pedig 1-nek kell lennie.* ``isError()``
* ``isError(1)``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor figyelmen kívül hagyható-e. Egynél több bemeneti adatfolyamot vevő átalakítások esetén átadhatja az adatfolyam (1-alapú) indexét. Az adatfolyam-indexnek 1-nek vagy 2-nek, az alapértelmezett értéknek pedig 1-nek kell lennie.* ``isIgnore()``
* ``isIgnore(1)``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor beszúrásra van-e megjelölve. Egynél több bemeneti adatfolyamot vevő átalakítások esetén átadhatja az adatfolyam (1-alapú) indexét. Az adatfolyam-indexnek 1-nek vagy 2-nek, az alapértelmezett értéknek pedig 1-nek kell lennie.* ``isInsert()``
* ``isInsert(1)``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor megfelel-e a keresésnek. Egynél több bemeneti adatfolyamot vevő átalakítások esetén átadhatja az adatfolyam (1-alapú) indexét. Az adatfolyam-indexnek 1-nek vagy 2-nek, az alapértelmezett értéknek pedig 1-nek kell lennie.* ``isMatch()``
* ``isMatch(1)``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Annak ellenőrzése, hogy az érték NULL-e* ``isNull(NULL()) -> true``
* ``isNull('') -> false``
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor frissítésre van-e megjelölve. Egynél több bemeneti adatfolyamot vevő átalakítások esetén átadhatja az adatfolyam (1-alapú) indexét. Az adatfolyam-indexnek 1-nek vagy 2-nek, az alapértelmezett értéknek pedig 1-nek kell lennie.* ``isUpdate()``
* ``isUpdate(1)``
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor beszúrásra van-e megjelölve. Egynél több bemeneti adatfolyamot vevő átalakítások esetén átadhatja az adatfolyam (1-alapú) indexét. Az adatfolyam-indexnek 1-nek vagy 2-nek, az alapértelmezett értéknek pedig 1-nek kell lennie.* ``isUpsert()``
* ``isUpsert(1)``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
A hónap utolsó dátumának bekerülése dátummal* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Összehasonlítás kisebb, mint vagy egyenlő operátor. Megegyezik <= operátorral* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Az 1-es indexnél kezdődő részkarakterláncokat kivonata karakterszámmal. Megegyezik a SUBSTRING(str; 1, n)* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
A karakterlánc hosszát adja eredményül.* ``length('dumbo') -> 5``
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítás kevesebb operátor. Megegyezik < operátorral* ``lesser(12, 24) -> true``
* ``('abcd' < 'abc') -> false``
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítás kisebb, mint vagy egyenlő operátor. Megegyezik <= operátorral* ``lesserOrEqual(12, 12) -> true``
* ``('dumbo' <= 'dum') -> false``
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Két húr közötti levenshtein távolságot kap* ``levenshtein('boys', 'girls') -> 4``
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
A minta egy karakterlánc, amely szó szerint illeszkedik. A kivételek a következő speciális szimbólumok: _ a bemenet bármely karakterének megfelel (hasonlóan a hoz. posix reguláris kifejezésekben) % a bemenetnulla vagy több karakterének felel meg (hasonlóan a posix reguláris kifejezések .* karakteréhez).
A menekülési karakter ''. Ha egy escape karakter megelőz egy speciális vagy egy másik escape karaktert, a következő karakter szó szerint illeszkedik. Érvénytelen bármely más karakter elől menekülni.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Megkeresi a karakterlánc részpozíciójának helyét (1-en alapul) egy adott pozíciót kezdő karakterláncon belül. Ha a pozíció nincs megadva, akkor a karakterlánc elejétől kell figyelembe venni. 0 ad vissza, ha nem található* ``locate('mbo', 'dumbo') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Kiszámítja a napló értékét. Az opcionális alap más hol is megadható euler számmal, ha* ``log(100, 10) -> 2``
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
A naplóértékét 10 alap alapján számítja ki* ``log10(100) -> 2``
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Karakterlánc kisbetűs e-két betűje* ``lower('GunChus') -> 'gunchus'``
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Bal párna a húr a mellékelt párnázat, amíg ez egy bizonyos hosszúságú. Ha a karakterlánc egyenlő vagy nagyobb, mint a hossz, * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` 
* ``lpad('dumbo', 4, '-') -> 'dumb'`` 
* akkor a program a "lpad("dumbo", 8, <dumbo'``
___
### <code>'<>') -> '<>ltrim) hosszúságúra vágja</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
A bal oldali levágás a kezdő karakterek ből álló karakterláncot vág le. Ha a második paraméter nincs megadva, akkor a szóközöket vágja le. A második paraméterben megadott bármely karakter tanait trim* ``ltrim('  dumbo  ') -> 'dumbo  '``
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
A tömb minden elemét egy új elemre képezi le a megadott kifejezés használatával. A Map a kifejezés függvény egyik elemére való hivatkozást vár #item* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
A tömb minden elemét egy új elemre képezi le a megadott kifejezés használatával. A Map a kifejezés egyik elemére #item, az elemindexre való hivatkozást #index* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Kiszámítja a különböző primitív adattípusok oszlopkészletének MD5 kivonatát, és egy 32 karakteres hexatokarakterláncot ad vissza. Ezt fel lehet használni, hogy kiszámítja az ujjlenyomat egy sor* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Egy dátum ezredmásodperces értékét kapja. Egy opcionális időzónát "GMT", "PST", "UTC", "America/Cayman" formátumban adhat át. A helyi időzóna lesz az alapértelmezett. Lásd a Java SimpleDateFormat a rendelkezésre álló formátumokat. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Időtartam ezredmásodpercben ezredmásodpercben* ``milliseconds(2) -> 2L``
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Számok kivonása. Vonósítsa ki a napok számát. Időtartam kivonása egy időbélyegből. Két időbélyeg kivonása a különbség ezredmásodpercben való leszámításához. Megegyezik a - operátorral* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Egy időbélyeg percértékét kapja meg. Egy opcionális időzónát "GMT", "PST", "UTC", "America/Cayman" formátumban adhat át. A helyi időzóna lesz az alapértelmezett. Lásd a Java SimpleDateFormat a rendelkezésre álló formátumokat. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Időtartam ezredmásodpercben, percek számában* ``minutes(2) -> 120000L``
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Modulus pár szám. Megegyezik a % operátorral* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Dátum vagy időbélyeg havi értékének bególja* ``month(toDate('2012-8-8')) -> 8``
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
Két dátum közötti hónapok számát kapja. A számítást kerekítheti. Egy opcionális időzónát "GMT", "PST", "UTC", "America/Cayman" formátumban adhat át. A helyi időzóna lesz az alapértelmezett. Lásd a Java SimpleDateFormat a rendelkezésre álló formátumokat. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Szorozza a pár számot. Megegyezik a * operátorral* ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy számot nem ad meg. A pozitív számokat negatívra fordítja, és fordítva* ``negate(13) -> -13``
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
A következő egyedi sorozatot adja eredményül. A szám csak egy partíción belül következik, és a partitionId előrögzíti* ``nextSequence() == 12313112 -> false``
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
A karakterlánc értékének normalizálása különálló ékezetes unicode karakterekre* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai negation operátor* ``not(true) -> false``
* ``not(10 == 20) -> true``
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Az összehasonlítás nem egyenlő operátorral. Megegyezik a != operátorral* ``12 != 24 -> true``
* ``'bojjus' != 'bo' + 'jjus' -> false``
___
### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Annak ellenőrzése, hogy az érték nem NULL-e* ``notNull(NULL()) -> false``
* ``notNull('') -> true``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Null értéket ad eredményül. Használja a függvény szintaxisát(null()), ha van egy "null" nevű oszlop. Minden olyan művelet, amely használ, null* ``isNull('dumbo' + null) -> true``
* ``isNull(10 * null) -> true``
* ``isNull('') -> false``
* ``isNull(10 + 20) -> false``
* ``isNull(10/0) -> true``
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai vagy operátor. Megegyezik a(z) ||* ``or(true, false) -> true``
* ``true || false -> true``
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Pozitív modulus pár szám.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Az aktuális partícióazonosítót adja eredményül, amelyben a bemeneti sor található.* ``partitionId()``
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy számot egy másik erejére emel.* ``power(10, 2) -> 100``
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Elemeket halmoz fel egy tömbben. A csökkentés egy akkumulátorra és az első kifejezés függvény egy elemére való hivatkozást vár #acc és #item néven, és az eredményül kapott értéket a második kifejezésfüggvényben #result ként számít.* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Egy adott regex-minta megfelelő karakterláncának kinyerése. Az utolsó paraméter azonosítja az egyezési csoportot, és ha nincs megadva, az alapértelmezett érték 1 lesz. Használja<regex>a ' '(vissza idézőjel) karaktert menekülés nélkül.* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc megegyezik-e az adott regex mintával. Használja<regex>a ' '(vissza idézőjel) karaktert menekülés nélkül.* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
A regex minta összes előfordulásának lecserélése egy<regex>másik karakterlánc-szintre a megadott karakterláncban A ' '(vissza árajánlat) használata egy karakterlánc hozadékához anélkül, hogy kiszökne.* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Feloszt egy karakterláncot egy határolójel alapján regex alapján, és karakterláncok tömbjét adja vissza.* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
A részkarakterláncok összes előfordulását cserélje le egy másik karakterlánc-szintre a megadott karakterláncban. Ha az utolsó paraméter nincs megadva, akkor az alapértelmezett érték a karakterlánc kiürítése* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie '``
* ``replace('doggie dog', 'dog') -> 'gie '``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Karakterlánc megfordítása* ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Jobb oldali karakterszámmal rendelkező karakterláncrész-karakterláncot olvas be. Megegyezik a SUBSTRING(str; HOSSZ(str) - n, n)* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc megegyezik-e az adott regex mintával* ``rlike('200.50', `(\d+).(\d+)`) -> true``
* ``rlike('bogus', `M[0-9]+.*`) -> false``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Egy számot kerekít egy választható léptékés egy opcionális kerekítési módban. Ha a lépték nincs megadva, akkor az alapértelmezett érték 0 lesz.  Ha a mód nincs megadva, akkor a ROUND_HALF_UP(5) érték. A kerekítés értékei közé tartozik az 1 - ROUND_UP 2 - ROUND_DOWN 3 - ROUND_CEILING 4 - ROUND_FLOOR 5 - ROUND_HALF_UP 6 - ROUND_HALF_DOWN 7 - ROUND_HALF_EVEN 8 - ROUND_UNNECESSARY* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Jobbra párna a húr a mellékelt párnázat, amíg ez egy bizonyos hosszúságú. Ha a karakterlánc egyenlő vagy nagyobb, mint a hossz, * ``rpad('dumbo', 10, '-') -> 'dumbo-----'`` 
* ``rpad('dumbo', 4, '-') -> 'dumb'`` 
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'`` 
___
### <code>rtrim</code>akkor a hossz rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
A jobb oldali karaktersorozat ot szegélyezi. Ha a második paraméter nincs megadva, akkor a szóközöket vágja le. A második paraméterben megadott bármely karakter tanait trim* ``rtrim('  dumbo  ') -> '  dumbo'``
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Egy dátum második értékét kapja. Egy opcionális időzónát "GMT", "PST", "UTC", "America/Cayman" formátumban adhat át. A helyi időzóna lesz az alapértelmezett. Lásd a Java SimpleDateFormat a rendelkezésre álló formátumokat. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Időtartam ezredmásodpercben* ``seconds(2) -> 2000L``
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Kiszámítja a különböző primitív adattípusokoszlop-készletének SHA-1 kivonatát, és egy 40 karakteres hexatokarakterláncot ad vissza. Ezt fel lehet használni, hogy kiszámítja az ujjlenyomat egy sor* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Kiszámítja a különböző primitív adattípusok oszlopkészletének SHA-2 kivonatát egy bithossz alapján, amely csak 0(256), 224, 256, 384, 512 értéklehet. Ezt fel lehet használni, hogy kiszámítja az ujjlenyomat egy sor* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Szinázsértéket számít ki* ``sin(2) -> 0.9092974268256817``
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiperbolikus szinuszértéket számít ki* ``sinh(0) -> 0.0``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Egy tömb részhalmazát nyeri ki egy pozícióból. A pozíció 1 alapul. Ha a hossz nincs megadva, akkor a program alapértelmezés szerint a karakterlánc végét teszi.* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
A tömböt a megadott predikátumfüggvény alapján rendezi. A rendezés a kifejezés függvény két egymást követő elemére #item1 és #item2* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
A karakterlánc soundex kódjának bekapása* ``soundex('genius') -> 'G520'``
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Határolójel alapján feloszt egy karakterláncot, és karakterlánctömböt ad vissza.* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Szám négyzetgyökének kiszámítása* ``sqrt(9) -> 3``
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc a megadott karakterlánccal kezdődik-e* ``startsWith('dumbo', 'du') -> true``
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Hónapok kivonása dátumból vagy időbélyegből. Megegyezik a dátum - operátorral* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Hónapok kivonása dátumból vagy időbélyegből* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Egy bizonyos hosszúságú részkarakterláncot nyer ki egy pozícióból. A pozíció 1 alapul. Ha a hossz nincs megadva, akkor a program alapértelmezés szerint a karakterlánc végét teszi.* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Érintőérték számítása* ``tan(0) -> 0.0``
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiperbolikus érintőértéket számít ki* ``tanh(0) -> 0.0``
___
### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
A megadott karakterlánc kódolása a base64-ben* ``toBase64('bojjus') -> 'Ym9qanVz'``
___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Bármely numerikus/dátum/időbélyegző/karakterlánc bináris ábrázolássá alakítát* ``toBinary(3) -> [0x11]``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
A ('t', "true', "y', "yes", "1") értéket igazés ('f', 'false', 'n', 'no', '0') értékre konvertálja hamisra és NULL-re bármely más értékre.* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``isNull(toBoolean('truthy')) -> true``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
A bemeneti dátumkarakterláncot dátummá alakítja egy választható beviteli dátumformátum használatával. Lásd a Java SimpleDateFormat a rendelkezésre álló formátumokat. Ha a bemeneti dátum formátuma nincs megadva, az alapértelmezett formátum yyyy-[M]M-[d]d. Elfogadott formátumok :[ yyyy, yyyy-[M]M, yyyy-[M]M]M-[d]d, yyyy-[M]M-[d]dT* ]* ``toDate('2012-8-18') -> toDate('2012-08-18')``
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Bármely numerikus vagy karakterláncot decimális értékké alakít át. Ha nincs megadva pontosság és lépték, akkor a (10,2) értékre van alapértelmezett. Az átalakításhoz opcionális Java decimális formátum használható. Egy opcionális területi beállítási formátum BCP47 nyelven, mint például en-US, de, zh-CN* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Bármely numerikus vagy karakterláncot kettős értékké alakít át. Az átalakításhoz opcionális Java decimális formátum használható. Egy opcionális területi beállítási formátum BCP47 nyelven, mint például en-US, de, zh-CN* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Bármely numerikus vagy karakterláncot úszólebegteté alakít át. Az átalakításhoz opcionális Java decimális formátum használható. Bármely dupla csonkolása* ``toFloat(123.45) -> 123.45f``
* ``toFloat('123.45') -> 123.45f``
* ``toFloat('$123.45', '$###.00') -> 123.45f``
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Bármely numerikus vagy karakterláncot egész értékké alakít át. Az átalakításhoz opcionális Java decimális formátum használható. Csonkolja a hosszú, úszó, dupla* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Bármely numerikus vagy karakterláncot hosszú értékké alakít át. Az átalakításhoz opcionális Java decimális formátum használható. Minden úszót csonkol, dupla* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Bármely numerikus vagy karakterláncot rövid értékké alakít át. Az átalakításhoz opcionális Java decimális formátum használható. Minden egész, hosszú, úszó, dupla* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Primitív adattípust karakterlánclá alakít át. Számok és dátumok esetén formátum adható meg. Ha nincs megadva, a rendszer alapértelmezett beállítása történik. A számokhoz java decimális formátumot használ. Az összes lehetséges dátumformátumról a Java SimpleDateFormat hivatkozásra hivatkozik; az alapértelmezett formátum yyyy-MM-dd* ``toString(10) -> '10'``
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
Egy karakterláncot időbélyeggé alakít át, amely választható időbélyeg-formátumot kap. Az összes lehetséges formátumot a Java SimpleDateFormat oldalon tájékasz-e. Ha az időbélyeg nincs megadva, a program az alapértelmezett mintát használja.yyyy-[M]M-[d]d hh:mm:ss[.f...] értéket használja. Egy opcionális időzónát "GMT", "PST", "UTC", "America/Cayman" formátumban adhat át. Az időbélyeg akár ezredmásodperces pontosságot is támogat, 999A Java SimpleDateFormat értéke a rendelkezésre álló formátumokat. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Az időbélyeget UTC-vé alakítja. Egy opcionális időzónát "GMT", "PST", "UTC", "America/Cayman" formátumban adhat át. Ez alapértelmezés szerint az aktuális timezoneUtalja a Java SimpleDateFormat a rendelkezésre álló formátumokat. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Cserélje le az egyik karakterkészletet egy másik karakterkészletre a karakterláncban. A karakterek et 1:1-re cserélik.* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Levágja a kezdő és záró karakterek sorozatát. Ha a második paraméter nincs megadva, akkor a szóközöket vágja le. A második paraméterben megadott bármely karakter tanait trim* ``trim('  dumbo  ') -> 'dumbo'``
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Mindig igaz értéket ad vissza. Használja a függvény szintaxisát(true()), ha van egy "true" nevű oszlop.* ``(10 + 20 == 30) -> true``
* ``(10 + 20 == 30) -> true()``
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Megegyezik az oszlop típusával. Csak a mintakifejezésekben használható.szám megegyezik a rövid, egész, hosszú, dupla, úszó vagy decimális, az integrált egyezések rövid, egész, hosszú, tört megegyezések dupla, úszó, decimális és datetime megegyezések dátum vagy időbélyeg típusa* ``typeMatch(type, 'number')``
* ``typeMatch('date', 'datetime')``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Karakterlánc nagybetűs felnagybetűje* ``upper('bojjus') -> 'BOJJUS'``
___
### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
A generált UUID-t adja eredményül.* ``uuid()``
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Az év hetét kapja meg dátummal* ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Időtartam ezredmásodpercben a hetek számában* ``weeks(2) -> 1209600000L``
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai XOR operátor. Ugyanaz, mint ^ operátor* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Dátum * ``year(toDate('2012-8-8')) -> 2012`` 
## évértékének beszerzése Összesített függvények A következő függvények csak összesítésben, kimutatásban, kimutatásfeloldásban és ablakátalakításokban érhetők el.___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy oszlop értékátlagának lehívása* ``avg(sales)``
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Egy kritérium alapján megkapja egy oszlop értékátlagát* ``avgIf(region == 'West', sales)``
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Az értékek összesített számának leszámítása. Ha a választható oszlop(ok) meg van adva, akkor figyelmen kívül hagyja a NULL értékeket a darabszámban.* ``count(custId)``
* ``count(custId, custName)``
* ``count()``
* ``count(iif(isNull(custId), 1, NULL))``
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Oszlopok különböző értékeinek összesített számának beszámítása* ``countDistinct(custId, custName)``
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Egy feltétel alapján leveszi az értékek összesített számát. Ha a választható oszlop meg van adva, akkor figyelmen kívül hagyja a NULL értékeket a darabszámban* ``countIf(state == 'CA' && commission < 10000, name)``
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Két oszlop közötti kovariancia bekapása* ``covariancePopulation(sales, profit)``
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Egy kritérium alapján két oszlop sokasági kovarianciát kap* ``covariancePopulationIf(region == 'West', sales)``
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Két oszlop mintakovarianciájának bekapása* ``covarianceSample(sales, profit)``
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Egy kritérium alapján két oszlop mintakovarianciáját kapja meg* ``covarianceSampleIf(region == 'West', sales, profit)``
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Egy oszlopcsoport első értékének belátása szerint. Ha a második paraméter figyelmen kívül hagyásaNull nem, akkor hamisnak kell tekinteni* ``first(sales)``
* ``first(sales, false)``
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop kurtózisát kapja* ``kurtosis(sales)``
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy kritérium alapján egy oszlop kurtózisát kapja meg* ``kurtosisIf(region == 'West', sales)``
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Az oszlopcsoport utolsó értékének bekerülése. Ha a második paraméter figyelmen kívül hagyásaNull nem, akkor hamisnak kell tekinteni* ``last(sales)``
* ``last(sales, false)``
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Egy oszlop maximális értékének bekerülése* ``max(sales)``
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Egy feltétel alapján egy oszlop maximális értékét kapja meg* ``maxIf(region == 'West', sales)``
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy oszlop értékeinek átlagát kapja. Megegyezik az AVG-vel* ``mean(sales)``
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Egy feltétel alapján egy oszlop értékeinek átlagát kapja meg. Megegyezik az avgIf* ``meanIf(region == 'West', sales)``
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Egy oszlop minimális értékének bekerülése* ``min(sales)``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Egy feltétel alapján egy oszlop minimális értékét kapja meg* ``minIf(region == 'West', sales)``
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop ferdeségének letérése* ``skewness(sales)``
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Feltételek alapján leteszi egy oszlop ferdeségét* ``skewnessIf(region == 'West', sales)``
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop szórásának bekerülése* ``stdDev(sales)``
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy feltétel alapján egy oszlop szórását kapja meg* ``stddevIf(region == 'West', sales)``
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop sokasági szórásának beszámítása* ``stddevPopulation(sales)``
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy kritérium alapján egy oszlop sokasági szórását kapja* ``stddevPopulationIf(region == 'West', sales)``
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop mintasztinációjának beszámítása* ``stddevSample(sales)``
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy kritérium alapján egy oszlop mintasztinációját kapja meg* ``stddevSampleIf(region == 'West', sales)``
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy numerikus oszlop összesített összegének bekéselése* ``sum(col)``
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy numerikus oszlop különböző értékeinek összesített összegének bekésése* ``sumDistinct(col)``
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
A feltételek alapján egy numerikus oszlop összesített összege kerül le. A feltétel bármely oszlopon alapulhat* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``
* ``sumDistinctIf(true, sales)``
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
A feltételek alapján egy numerikus oszlop összesített összege kerül le. A feltétel bármely oszlopon alapulhat* ``sumIf(state == 'CA' && commission < 10000, sales)``
* ``sumIf(true, sales)``
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop varianciájának leváltása* ``variance(sales)``
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Feltételek alapján egy oszlop varianciáját kapja meg* ``varianceIf(region == 'West', sales)``
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop sokasági varianciájának lemásása* ``variancePopulation(sales)``
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy feltétel alapján egy oszlop sokasági varianciáját kapja meg.* ``variancePopulationIf(region == 'West', sales)``
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop elfogulatlan varianciájának belátása* ``varianceSample(sales)``
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Feltételek alapján egy oszlop * ``varianceSampleIf(region == 'West', sales)`` 
## elfogulatlan varianciáját kapja meg Ablak függvények A következő függvények csak ablakátalakítások esetén érhetők el.___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
A CumeDist függvény kiszámítja egy érték pozícióját a partíció összes értékéhez viszonyítva. Az eredmény a partíció sorrendjében az aktuális sort megelőző vagy azzal egyenlő sorok száma osztva az ablakpartíció sorainak teljes számával. A sorrendben lévő nyakkendőértékek ugyanabba a pozícióba kerülnek.
* ``cumeDist()``
___
### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
Az ablak rendelési sorrendjében megadott értékek csoportjában lévő érték rangját számítja ki. Az eredmény egy plusz a partíció sorrendjében az aktuális sort megelőző vagy azzal egyenlő sorok száma. Az értékek nem hoznak létre hézagokat a sorrendben. A Dense Rank akkor is működik, ha az adatok nincsenek rendezve, és az értékek változását keresi* ``denseRank()``
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Az első kiértékelt paraméter értéke n sorok az aktuális sor előtt. A második paraméter a visszatekintő sorok száma, az alapértelmezett érték pedig 1. Ha nincs annyi sor, null értéket ad vissza, ha csak egy alapértelmezett érték van megadva.* ``lag(amount, 2)``
* ``lag(amount, 2000, 100)``
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Az aktuális sor után kiértékelt első paraméter n sorainak értékét. A második paraméter a várandós sorok száma, az alapértelmezett érték pedig 1. Ha nincs annyi sor, null értéket ad vissza, ha csak egy alapértelmezett érték van megadva.* ``lead(amount, 2)``
* ``lead(amount, 2000, 100)``
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
Az NTile függvény az egyes ablakpartíciók sorait 1-től `n`legfeljebb gyűjtőkig terjedő gyűjtőkre `n` osztja. A gyűjtőértékek legfeljebb 1-vel térnek el egymástól. Ha a partíció sorainak száma nem egyenletesen oszlik el a gyűjtők számára, akkor a fennmaradó értékek egy gyűjtőnként oszlanak el, kezdve az első gyűjtővel. Az NTile függvény hasznos a tertiles, kvartilisek, deciles és egyéb közös összefoglaló statisztikák kiszámításához. A függvény két változót számít ki az inicializálás során: Egy normál gyűjtő mérete egy további sort ad hozzá. Mindkét változó az aktuális partíció méretén alapul. A számítási folyamat során a függvény nyomon követi az aktuális sorszámot, az aktuális gyűjtőszámot és azt a sorszámot, amelynél a gyűjtő módosul (bucketThreshold). Amikor az aktuális sorszám eléri a gyűjtő küszöbértéket, a gyűjtő értéke eggyel nő, és a küszöbérték a gyűjtő méretével nő (plusz egy extra, ha az aktuális gyűjtő párnázott).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
Az ablak rendelési sorrendjében megadott értékek csoportjában lévő érték rangját számítja ki. Az eredmény egy plusz a partíció sorrendjében az aktuális sort megelőző vagy azzal egyenlő sorok száma. Az értékek hézagokat hoznak létre a sorrendben. A rang akkor is működik, ha az adatok nincsenek rendezve, és az értékek változását keresi* ``rank()``
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
1-vel kezdődő ablak soraihoz sorszámozást rendel* ``rowNumber()``

## <a name="next-steps"></a>További lépések

[További információ a Kifejezésszerkesztő használatáról.](concepts-data-flow-expression-builder.md)
