---
title: Az Azure Data Factory-folyamat kifejezés Adatfüggvény leképezése
description: Az Azure Data Factory-folyamat kifejezés Adatfüggvény leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 74b22f194e7af000dbd8b53ec95dbbff2c5c588a
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59010532"
---
# <a name="mapping-data-flow-data-transformation-expressions"></a>Data flow adatok átalakítása kifejezések leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>Kifejezés funkciók

Data Factory-leképezés adatfolyamok rendelkezik egy kifejezésnyelv adatátalakítások konfigurálásához használható.

<code>abs</code>
==============================
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
A Modulus pozitív számok pár.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
*********************************
<code>acos</code>
==============================
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja egy consine inverz értéket
* ``acos(1) -> 0.0``
*********************************
<code>add</code>
==============================
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Hozzáad egy pár karakterlánc vagy szám. Néhány napot ad hozzá egy dátumot. Egy tömb hasonló típusú hozzáfűzi a másikra. Ugyanaz, mint a + operátor
* ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``
*********************************
<code>addDays</code>
==============================
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Nap hozzáadása egy dátum- vagy időbélyegző. Ugyanaz, mint a + operátor dátum
* ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``
*********************************
<code>addMonths</code>
==============================
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Egy dátum- vagy időbélyegző hónap hozzáadása
* ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
*********************************
<code>and</code>
==============================
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
ÉS logikai operátor. Ugyanaz, mint a & &
* ``and(true, false) -> false``
* ``true && false -> false``
*********************************
<code>asin</code>
==============================
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy szám inverz szinuszát értéket számít
* ``asin(0) -> 0.0``
*********************************
<code>atan</code>
==============================
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy szám inverz érintő értéket számít
* ``atan(0) -> 0.0``
*********************************
<code>atan2</code>
==============================
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Azt a szöget adja vissza radiánban sík pozitív x tengely, és adja meg a koordináták a pont között
* ``atan2(0, 0) -> 0.0``
*********************************
<code>avg</code>
==============================
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Lekérdezi egy oszlop értékek átlaga
* ``avg(sales) -> 7523420.234``
*********************************
<code>avgIf</code>
==============================
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
A feltételek lekérdezi az oszlopok értékek átlaga alapján
* ``avgIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>case</code>
==============================
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Alapján több értéket vagy egyéb feltételek váltakozó vonatkozik. Ha a bemenetek száma akkor is, a másik má hodnotu NULL utolsó feltétel
* ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``
*********************************
<code>cbrt</code>
==============================
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
A kocka legfelső szintű egy szám kiszámítása
* ``cbrt(8) -> 2.0``
*********************************
<code>ceil</code>
==============================
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
A legkisebb egész szám nem kisebb, mint a számot adja vissza
* ``ceil(-0.1) -> 0``
*********************************
<code>concat</code>
==============================
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Összefűzi a karakterláncok változó számú együtt. Ugyanaz, mint a + operátort karakterláncokkal
* ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
*********************************
<code>concatWS</code>
==============================
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Az elválasztó együtt karakterláncok változó számú fűz össze. Az első paraméter az elválasztó
* ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
*********************************
<code>cos</code>
==============================
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Cosine értéket adja eredményül
* ``cos(10) -> -0.83907152907``
*********************************
<code>cosh</code>
==============================
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja egy koszinusz
* ``cosh(0) -> 1.0``
*********************************
<code>count</code>
==============================
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Lekérdezi az összesített értékek száma. Ha a választható oszlopoknak van megadva, figyelmen kívül hagyja a száma a NULL értékek
* ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
*********************************
<code>countDistinct</code>
==============================
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Lekérdezi a különböző értékeket oszlopkészleteket összesített száma
* ``countDistinct(custId, custName) -> 60``
*********************************
<code>countIf</code>
==============================
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Alapján egy feltétel lekérdezi az összesített értékek száma. Ha a választható oszlop van megadva, figyelmen kívül hagyja a száma a NULL értékek
* ``countIf(state == 'CA' && commission < 10000, name) -> 100``
*********************************
<code>covariancePopulation</code>
==============================
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Lekérdezi a lakosság kovariancia két oszlop között
* ``covariancePopulation(sales, profit) -> 122.12``
*********************************
<code>covariancePopulationIf</code>
==============================
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Egy feltétel alapján, a két oszlop population kovarianciáját beolvasása
* ``covariancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>covarianceSample</code>
==============================
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Két oszlopra sample kovarianciáját beolvasása
* ``covarianceSample(sales, profit) -> 122.12``
*********************************
<code>covarianceSampleIf</code>
==============================
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Egy feltétel alapján, két oszlopra sample kovarianciáját beolvasása
* ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
*********************************
<code>crc32</code>
==============================
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Kiszámítja a CRC32 kivonatot oszlopa egy olyan bithosszt, amely csak az értékek 0(256), megadott primitív adattípusokat különböző készlete 224, 256, 384-et, 512. Sor ujjlenyomattal kiszámításához használható
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
*********************************
<code>cumeDist</code>
==============================
<code><b>cumeDist() => integer</b></code><br/><br/>
A CumeDist függvény kiszámítja az összes értéket viszonyított érték pozíciója a partíción. Ez előző vagy a sorrendjének befolyásolása érdekében a partíció ablak a partíción sorok száma osztva az aktuális sor egyenlő sorok számát. Az eredménykészlet tie értékeket kiértékelik az ugyanazon a helyen.
* ``cumeDist() -> 1``
*********************************
<code>currentDate</code>
==============================
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Lekérdezi az aktuális dátumot, amikor a feladat futni kezd. Megadhat egy választható időzóna "GMT", "PST", 'UTC', "Amerika/Kajmán" formájában. A helyi időzónában alapértelmezett értékként szolgál.
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``
*********************************
<code>currentTimestamp</code>
==============================
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Lekérdezi az aktuális időbélyeget, amikor a feladat elindul a helyi időzóna
* ``currentTimestamp() -> 12-12-2030T12:12:12``
*********************************
<code>currentUTC</code>
==============================
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Lekéri az aktuális az időbélyeg (UTC) szerint. Megadhat egy választható időzóna "GMT", "PST", 'UTC', "Amerika/Kajmán" formájában. Ez alapértelmezés szerint az aktuális időzóna
* ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>dayOfMonth</code>
==============================
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Lekérdezi a megadott date hónap napja
* ``dayOfMonth(toDate('2018-06-08')) -> 08``
*********************************
<code>dayOfWeek</code>
==============================
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Lekérdezi a megadott date hét napja. 1 – vasárnap, 2 -... hétfő, 7 – szombat
* ``dayOfWeek(toDate('2018-06-08')) -> 7``
*********************************
<code>dayOfYear</code>
==============================
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Lekérdezi a megadott date év napja
* ``dayOfYear(toDate('2016-04-09')) -> 100``
*********************************
<code>degrees</code>
==============================
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Radiánt fokká alakít át
* ``degrees(3.141592653589793) -> 180``
*********************************
<code>denseRank</code>
==============================
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Kiszámítja egy értéket egy csoportot az értékek rangsorát. Előző vagy annál az aktuális sor sorrendjének befolyásolása érdekében a partíció sorok számát és egy jön létre. Az értékek nem hoznak létre a feladatütemezési hiányosságok. Sűrű rang is működik, ha adatokat nincs rendezve, és az értékeket módosítsa keres
* ``denseRank(salesQtr, salesAmt) -> 1``
*********************************
<code>divide</code>
==============================
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
A számok pár osztja fel. Ugyanaz, mint a / operátor
* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
*********************************
<code>endsWith</code>
==============================
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy ha a karakterlánc végződik-e a megadott karakterlánc
* ``endsWith('great', 'eat') -> true``
*********************************
<code>equals</code>
==============================
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlító operátor eredménye. Ugyanaz, mint a operátor ==
* ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'abc'=='abc' -> true``
*********************************
<code>equalsIgnoreCase</code>
==============================
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Összehasonlító operátor nagybetűktől egyenlő. Ugyanaz, mint a <> = operátor
* ``'abc'=='abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
*********************************
<code>factorial</code>
==============================
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Egy szám faktoriálisát kiszámítása
* ``factorial(5) -> 120``
*********************************
<code>false</code>
==============================
<code><b>false() => boolean</b></code><br/><br/>
Minden esetben hamis értéket ad vissza. A függvény syntax(false()) használhatja, ha a "false" nevű oszlop neve
* ``isDiscounted == false()``
* ``isDiscounted() == false``
*********************************
<code>first</code>
==============================
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Lekérdezi egy oszlopcsoport első értéke. Ha a második paraméter ignoreNulls hiányzik, feltételezzük false (hamis)
* ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
*********************************
<code>floor</code>
==============================
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
A legnagyobb egész szám nem nagyobb, mint a számot adja vissza
* ``floor(-0.1) -> -1``
*********************************
<code>greater</code>
==============================
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlító nagyobb operátor. Ugyanaz, mint a > operátor
* ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
*********************************
<code>greaterOrEqual</code>
==============================
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlító nagyobb vagy egyenlő operátor szerinti szűrése. Ugyanaz, mint a > = operátor
* ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
*********************************
<code>greatest</code>
==============================
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Bemeneti értékek listája között legnagyobb értékét adja vissza. Ha az összes bemenetei null értékűek null értéket ad vissza
* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
*********************************
<code>hour</code>
==============================
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Lekérdezi az időbélyeg az óra értékét. Megadhat egy választható időzóna "GMT", "PST", 'UTC', "Amerika/Kajmán" formájában. A helyi időzónában alapértelmezett értékként szolgál.
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``
*********************************
<code>iif</code>
==============================
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Alapján feltétel több értéket vagy másik vonatkozik. Ha más není zadán tekintendő null értékű. Mindkettő az érték kompatibilisnek kell lenniük (szám, karakterlánc...) * ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
*********************************
<code>in</code>
==============================
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Ellenőrzi, hogy egy elem a tömbben található
* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
*********************************
<code>initCap</code>
==============================
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Minden szó első betűjének nagybetűssé alakít át. Szavak a rendszer megállapítja, elválasztó karakter választja el
* ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
*********************************
<code>instr</code>
==============================
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Megkeresi a pozíció (1-alapú) a karakterláncrész egy karakterláncból. 0 visszaadott Ha nem található
* ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
*********************************
<code>isDelete</code>
==============================
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy ha a sor törlése van megjelölve. Átalakítások egynél több bemeneti stream véve az (1-alapú) index, az adatfolyam adhat át. A stream index alapértelmezett értéke: 1
* ``isDelete() -> true``
* ``isDelete(1) -> false``
*********************************
<code>isError</code>
==============================
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy ha a sor hiba van megjelölve. Átalakítások egynél több bemeneti stream véve az (1-alapú) index, az adatfolyam adhat át. A stream index alapértelmezett értéke: 1
* ``isError() -> true``
* ``isError(1) -> false``
*********************************
<code>isIgnore</code>
==============================
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy ha a sor figyelmen kívül van megjelölve. Átalakítások egynél több bemeneti stream véve az (1-alapú) index, az adatfolyam adhat át. A stream index alapértelmezett értéke: 1
* ``isIgnore() -> true``
* ``isIgnore(1) -> false``
*********************************
<code>isInsert</code>
==============================
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy ha a sor beszúrása van megjelölve. Átalakítások egynél több bemeneti stream véve az (1-alapú) index, az adatfolyam adhat át. A stream index alapértelmezett értéke: 1
* ``isInsert() -> true``
* ``isInsert(1) -> false``
*********************************
<code>isMatch</code>
==============================
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy ha a sor a keresési egyezik. Átalakítások egynél több bemeneti stream véve az (1-alapú) index, az adatfolyam adhat át. A stream index alapértelmezett értéke: 1
* ``isMatch() -> true``
* ``isMatch(1) -> false``
*********************************
<code>isNull</code>
==============================
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Ellenőrzi, hogy az érték NULL
* ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
*********************************
<code>isUpdate</code>
==============================
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy ha a sor frissítés van megjelölve. Átalakítások egynél több bemeneti stream véve az (1-alapú) index, az adatfolyam adhat át. A stream index alapértelmezett értéke: 1
* ``isUpdate() -> true``
* ``isUpdate(1) -> false``
*********************************
<code>kurtosis</code>
==============================
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Az értékek oszlop beolvasása
* ``kurtosis(sales) -> 122.12``
*********************************
<code>kurtosisIf</code>
==============================
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy feltétel alapján, az értékek oszlop beolvasása
* ``kurtosisIf(region == 'West', sales) -> 122.12``
*********************************
<code>lag</code>
==============================
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Lekérdezi az első paraméter kiértékelése n sorok előtt az aktuális sor értékét. A második paraméter visszakeresnünk sorok számát és az alapértelmezett érték az 1. Ha nem null értéket ad vissza, ha az alapértelmezett érték van megadva tetszőleges számú sorok
* ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
*********************************
<code>last</code>
==============================
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Lekérdezi egy oszlopcsoport utolsó értékét. Ha a második paraméter ignoreNulls hiányzik, feltételezzük false (hamis)
* ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
*********************************
<code>lastDayOfMonth</code>
==============================
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Lekérdezi a megadott date hónap utolsó napját
* ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
*********************************
<code>lead</code>
==============================
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Az első n értékeli ki a paraméter-sorok értékét lekérdezi az aktuális sor után. A második paraméter várjuk sorok száma és az alapértelmezett érték az 1. Ha nem null értéket ad vissza, ha az alapértelmezett érték van megadva tetszőleges számú sorok
* ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
*********************************
<code>least</code>
==============================
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Összehasonlító kisebb vagy egyenlő operátor. Ugyanaz, mint a < = operátor
* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
*********************************
<code>left</code>
==============================
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Kinyeri egy karakterláncrészletet kezdő található karakterek számát az 1. Ugyanaz, mint a SUBSTRING (str, 1, n)
* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
*********************************
<code>length</code>
==============================
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
A karakterlánc hosszát adja vissza
* ``length('kiddo') -> 5``
*********************************
<code>lesser</code>
==============================
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlító operátor kevésbé. Ugyanaz, mint a < operátor
* ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
*********************************
<code>lesserOrEqual</code>
==============================
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlító kisebb vagy egyenlő operátor. Ugyanaz, mint a < = operátor
* ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
*********************************
<code>levenshtein</code>
==============================
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
A két karakterláncot levenshtein távolsága beolvasása
* ``levenshtein('boys', 'girls') -> 4``
*********************************
<code>like</code>
==============================
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
A minta: szó szerint egyező karakterlánc. A kivételek közé tartoznak a következő speciális szimbólumokat: _ (hasonló a bemeneti adatok több karaktert helyettesít. posix reguláris kifejezések) % tartalmazza a bemeneti adatok nulla vagy több karaktert (hasonló. * a posix reguláris kifejezések).
Az escape-karakter: "%. Ha escape-karakter szerepel egy speciális szimbólum, vagy egy másik escape-karakter, a következő karaktert szó szerint megfeleltetni. Érvénytelen escape-bármilyen más karakter.
* ``like('icecream', 'ice%') -> true``
*********************************
<code>locate</code>
==============================
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Megkeresi a pozíció (1-alapú) a karakterláncrész egy bizonyos kezdőpozíciója egy karakterláncból. Ha kihagyja a pozíció a karakterlánc elején található minősül. 0 visszaadott Ha nem található
* ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
*********************************
<code>log</code>
==============================
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Napló értéket számít. Egy nem kötelező alapja lehet más euler számnak megadott, ha a használt
* ``log(100, 10) -> 2``
*********************************
<code>log10</code>
==============================
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Alap 10-alapú log értéket számít
* ``log10(100) -> 2``
*********************************
<code>lower</code>
==============================
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Egy karakterlánc lowercases
* ``lower('GunChus') -> 'gunchus'``
*********************************
<code>lpad</code>
==============================
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Balra a karakterlánc a megadott kitöltési, egészen addig, amíg egy bizonyos hosszúságú bevezető nullákkal tölti fel. Ha a karakterlánc hossza nagyobb vagy azzal egyenlőnek, akkor tekintendő műveletvégzés * ``lpad('great', 10, '-') -> '-----great'``
* ``lpad('great', 4, '-') -> 'great'``
* : "% lpad ("nagy"8" <> ") ->" <><great'``
*********************************
<code>ltrim</code>
==============================
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Balra kivág egy szöveges karakterlánc vezető. Ha a második paraméter nincs megadva, azt levágja a szóközöket. Ellenkező esetben azt levágja a második paraméterben megadott bármely karakter
* ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
*********************************
<code>max</code>
==============================
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Lekérdezi egy olyan oszlop maximális értéke
* ``MAX(sales) -> 12312131.12``
*********************************
<code>maxIf</code>
==============================
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Egy feltétel alapján, lekérdezi egy olyan oszlop maximális értéke
* ``maxIf(region == 'West', sales) -> 99999.56``
*********************************
<code>md5</code>
==============================
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Kiszámítja az MD5-kivonat oszlopa primitív adattípusokat különböző készlete, és hexadecimális 32 karakter hosszúságú karakterláncot ad vissza. Sor ujjlenyomattal kiszámításához használható
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``
*********************************
<code>mean</code>
==============================
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Lekérdezi egy oszlop értékeinek középértéke. Ugyanaz, mint az átlagos
* ``mean(sales) -> 7523420.234``
*********************************
<code>meanIf</code>
==============================
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Alapján egy feltétel lekérdezi egy oszlop értékeinek középértéke. Ugyanaz, mint a avgIf
* ``meanIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>min</code>
==============================
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Egy oszlop legkisebb értékének beolvasása
* ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
*********************************
<code>minIf</code>
==============================
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Egy feltétel alapján, egy oszlop legkisebb értékének beolvasása
* ``minIf(region == 'West', sales) -> 00.01``
*********************************
<code>minus</code>
==============================
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Kivonja a számokat. Kivonandó dátum néhány napot. Ugyanaz, mint a - operátor
* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
*********************************
<code>minute</code>
==============================
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
A perc értéke az időbélyeg beolvasása. Megadhat egy választható időzóna "GMT", "PST", 'UTC', "Amerika/Kajmán" formájában. A helyi időzónában alapértelmezett értékként szolgál.
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``
*********************************
<code>mod</code>
==============================
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
A számok pár modulus. Ugyanaz, mint a % operátor
* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
*********************************
<code>month</code>
==============================
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
A hónap értékét egy dátum- vagy időbélyegző beolvasása
* ``month(toDate('2012-8-8')) -> 8``
*********************************
<code>monthsBetween</code>
==============================
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
Lekérdezi a között két datesYou hónapok száma adhat át egy nem kötelező időzóna "GMT", "PST", 'UTC', "Amerika/Kajmán" formájában. A helyi időzónában alapértelmezett értékként szolgál.
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
*********************************
<code>multiply</code>
==============================
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Összeszoroz két számot. Ugyanaz, mint a * operátor
* ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
*********************************
<code>nTile</code>
==============================
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
Az NTile függvény a sorok be minden ablakot partíció osztja fel `n` legfeljebb 1-ről terjedő gyűjtők `n`. Legfeljebb 1 gyűjtőbe értékek eltérőek lehetnek. Ha a partíción sorok száma nem egyenlő arányban oszthatja azon gyűjtők számát, majd a hátralévő értékek gyűjtőbe, akkor az első gyűjtőbe kezdve minden elosztott egyet. Az NTile függvény különösen hasznos tertiles, quartiles, deciles és egyéb gyakori összefoglaló statisztikák kiszámításához. A függvény kiszámolja a két változót az inicializálás során: Egy normál gyűjtő mérete hozzáadja egy extra sor lesz. Mindkét változót a jelenlegi partíció méretét alapulnak. A számítás során a függvény nyomon követi, hogy az aktuális sor száma, az aktuális gyűjtőbe száma és a sor számát, amelyen a gyűjtő (bucketThreshold) változik. Az aktuális sor száma eléri a küszöbértéket gyűjtőbe, amikor a gyűjtő érték eggyel nő, és a küszöbérték növekszik a gyűjtő mérete (és a egy extra, ha a jelenlegi gyűjtőhöz számokhoz).
* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``
*********************************
<code>negate</code>
==============================
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy szám ellentettjét adja. Ennek a pozitív szám negatív, és ez fordítva is igaz
* ``negate(13) -> -13``
*********************************
<code>nextSequence</code>
==============================
<code><b>nextSequence() => long</b></code><br/><br/>
A folyamat következő egyedi adja vissza. Hány egymást követő csak egy partíción belül, és a következő előtaggal, a partitionId segítségével
* ``nextSequence() -> 12313112``
*********************************
<code>not</code>
==============================
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai negálás operátor
* ``not(true) -> false``
* ``not(premium)``
*********************************
<code>notEquals</code>
==============================
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlító operátor nem egyenlő. Ugyanaz, mint a! = operátor
* ``12!=24 -> true``
* ``'abc'!='abc' -> false``
*********************************
<code>null</code>
==============================
<code><b>null() => null</b></code><br/><br/>
NULL értéket ad vissza. A függvény syntax(null()) használhatja, ha a "null" nevű oszlop neve. Minden művelet, amely használja azt eredményezi egy NULL értékű
* ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``
*********************************
<code>or</code>
==============================
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai vagy operátor. Ugyanaz, mint a ||
* ``or(true, false) -> true``
* ``true || false -> true``
*********************************
<code>pMod</code>
==============================
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
A Modulus pozitív számok pár.
* ``pmod(-20, 8) -> 4``
*********************************
<code>power</code>
==============================
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy szám egy másik hatványra emel
* ``power(10, 2) -> 100``
*********************************
<code>rank</code>
==============================
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Kiszámítja egy értéket egy csoportot az értékek rangsorát. Előző vagy annál az aktuális sor sorrendjének befolyásolása érdekében a partíció sorok számát és egy jön létre. Az értékeket a feladatütemezési hiányosságok állítja elő. Rang működik, még ha adatok nincs rendezve, és az értékeket módosítsa keres
* ``rank(salesQtr, salesAmt) -> 1``
*********************************
<code>regexExtract</code>
==============================
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Bontsa ki a megadott regex minta egy egyező karakterláncrészletet. Utolsó paramétere helyén az egyezés csoportot határozza meg, és az alapértelmezett értéke 1, ha nincs megadva. Használja a(z)<regex>'(back quote) karakterláncnak megfelelő escape-karaktersorozat nélkül
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
*********************************
<code>regexMatch</code>
==============================
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy ha a karakterlánc megfelel-e a megadott regex minta. Használja a(z)<regex>'(back quote) karakterláncnak megfelelő escape-karaktersorozat nélkül
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
*********************************
<code>regexReplace</code>
==============================
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Cserélje le a megadott karakterlánc használata egy másik karakterláncrészlet egy Reguláriskifejezés-minta összes előfordulását a(z)<regex>'(back quote) karakterláncnak megfelelő escape-karaktersorozat nélkül
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
*********************************
<code>regexSplit</code>
==============================
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Egy karakterláncot egy reguláris kifejezés alapján elválasztó alapján bontja és karakterláncok tömbjét adja vissza
* ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
*********************************
<code>replace</code>
==============================
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Cserélje le a karakterláncrészlet összes előfordulását a megadott karakterlánc egy másik karakterláncrészlet
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
*********************************
<code>reverse</code>
==============================
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Megfordítja a karakterlánc
* ``reverse('gunchus') -> 'suhcnug'``
*********************************
<code>right</code>
==============================
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Karakterek száma a karakterláncrész kigyűjti a jobb oldalon. Ugyanaz, mint a SUBSTRING (str, LENGTH(str) - n, n)
* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
*********************************
<code>rlike</code>
==============================
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy ha a karakterlánc megfelel-e a megadott regex minta
* ``rlike('200.50', '(\d+).(\d+)') -> true``
*********************************
<code>round</code>
==============================
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Lefelé kerekít egy számot, egy nem kötelező méretezési csoport és a egy nem kötelező kerekítési mód. A méretezési csoport elhagyása esetén 0 alapértelmezés szerint.  Ha kihagyja a módot a ROUND_HALF_UP(5) alapértelmezés szerint. Az értékek kerekítési: 1 - 2 ROUND_UP - ROUND_DOWN 3 - 4. ROUND_CEILING – ROUND_FLOOR 5 - ROUND_HALF_UP 6 - ROUND_HALF_DOWN 7 - ROUND_HALF_EVEN 8 - ROUND_UNNECESSARY
* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
*********************************
<code>rowNumber</code>
==============================
<code><b>rowNumber() => integer</b></code><br/><br/>
Hozzárendel egy 1-től induló ablakban sorok számozása egymást követő sor
* ``rowNumber() -> 1``
*********************************
<code>rpad</code>
==============================
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Jobb a karakterlánc a megadott kitöltési, egészen addig, amíg egy bizonyos hosszúságú bevezető nullákkal tölti fel. Ha a karakterlánc hossza nagyobb vagy azzal egyenlőnek, akkor tekintendő műveletvégzés * ``rpad('great', 10, '-') -> 'great-----'``
* ``rpad('great', 4, '-') -> 'great'``
* ``rpad('great', 8, '<>') -> 'great<><'``
*********************************
<code>rtrimrtrim</code>
==============================
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Jobb kivág egy szöveges karakterlánc vezető. Ha a második paraméter nincs megadva, azt levágja a szóközöket. Ellenkező esetben azt levágja a második paraméterben megadott bármely karakter
* ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
*********************************
<code>second</code>
==============================
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
A dátum a második érték beolvasása. Megadhat egy választható időzóna "GMT", "PST", 'UTC', "Amerika/Kajmán" formájában. A helyi időzónában alapértelmezett értékként szolgál.
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``
*********************************
<code>sha1</code>
==============================
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Az SHA-1 kivonatoló oszlopa primitív adattípusokat különböző készlete kiszámolja, és egy 40 karakterből álló hexadecimális karakterláncot ad vissza. Sor ujjlenyomattal kiszámításához használható
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``
*********************************
<code>sha2</code>
==============================
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Kiszámítja az SHA-2 kivonatoló oszlopa egy olyan bithosszt, amely csak az értékek 0(256), megadott primitív adattípusokat különböző készlete 224, 256, 384-et, 512. Sor ujjlenyomattal kiszámításához használható
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``
*********************************
<code>sin</code>
==============================
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja egy szinusz értéket
* ``sin(2) -> 0.90929742682``
*********************************
<code>sinh</code>
==============================
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja egy szinusz értéket
* ``sinh(0) -> 0.0``
*********************************
<code>skewness</code>
==============================
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Lekérdezi az eltéréseket az oszlopok
* ``skewness(sales) -> 122.12``
*********************************
<code>skewnessIf</code>
==============================
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy feltétel alapján, az eltéréseket az oszlopok beolvasása
* ``skewnessIf(region == 'West', sales) -> 122.12``
*********************************
<code>slice</code>
==============================
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Kiolvassa a tömböt egy részét a helyzetben. Pozice je alapú 1. Ha hossza hiányzik, akkor alapértelmezés szerint a karakterlánc végére
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
*********************************
<code>soundex</code>
==============================
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
A karakterlánc a soundex kód beolvasása
* ``soundex('genius') -> 'G520'``
*********************************
<code>split</code>
==============================
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Egy karakterláncot egy elválasztó alapján bontja és karakterláncok tömbjét adja vissza
* ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
*********************************
<code>sqrt</code>
==============================
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja egy szám négyzetgyökét
* ``sqrt(9) -> 3``
*********************************
<code>startsWith</code>
==============================
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc kezdődik-e a megadott karakterlánc
* ``startsWith('great', 'gr') -> true``
*********************************
<code>stddev</code>
==============================
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Lekérdezi egy olyan oszlop szórása
* ``stdDev(sales) -> 122.12``
*********************************
<code>stddevIf</code>
==============================
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy feltétel alapján, lekérdezi a oszlop szórása
* ``stddevIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevPopulation</code>
==============================
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Az oszlop statisztikai sokaságra számított szórását beolvasása
* ``stddevPopulation(sales) -> 122.12``
*********************************
<code>stddevPopulationIf</code>
==============================
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy feltétel alapján, az oszlop statisztikai sokaságra számított szórását beolvasása
* ``stddevPopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevSample</code>
==============================
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Lekérdezi a minta egy olyan oszlop szórása
* ``stddevSample(sales) -> 122.12``
*********************************
<code>stddevSampleIf</code>
==============================
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy feltétel alapján, lekérdezi a minta egy olyan oszlop szórása
* ``stddevSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>subDays</code>
==============================
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Egy hónapon kivonása. Ugyanaz, mint a - operátort dátuma
* ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
*********************************
<code>subMonths</code>
==============================
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Egy dátum- vagy időbélyegző hónapig kivonása
* ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
*********************************
<code>substring</code>
==============================
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Egy bizonyos hosszúságú részkarakterlánc kigyűjti a helyzetben. Pozice je alapú 1. Ha hossza hiányzik, akkor alapértelmezés szerint a karakterlánc végére
* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
*********************************
<code>sum</code>
==============================
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Lekérdezi egy numerikus oszlop összesített összege
* ``sum(col) -> value``
*********************************
<code>sumDistinct</code>
==============================
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Lekérdezi egy numerikus oszlopot különböző értékeket összesített összege
* ``sumDistinct(col) -> value``
*********************************
<code>sumDistinctIf</code>
==============================
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Alapuló feltételek lekérdezi egy numerikus oszlop összesített összege. A feltétel alapulhat bármely oszlop
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
*********************************
<code>sumIf</code>
==============================
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Alapuló feltételek lekérdezi egy numerikus oszlop összesített összege. A feltétel alapulhat bármely oszlop
* ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
*********************************
<code>tan</code>
==============================
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja az érintő érték
* ``tan(0) -> 0.0``
*********************************
<code>tanh</code>
==============================
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy hiperbolikus az érintő értéket számít
* ``tanh(0) -> 0.0``
*********************************
<code>toBoolean</code>
==============================
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Alakít egy értéket, (sikerült ","true","y","Igen","1") igaz értékre és ("f","false", n", "nem", "0") a False (hamis), és semmilyen más érték NULL
* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
*********************************
<code>toDate</code>
==============================
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Egy karakterláncot alakít egy nem kötelező dátumformátum adott dátumot. Tekintse meg az összes lehetséges formátumok Java SimpleDateFormat. Ha a dátumformátum, a rendszer a következő kombinációit fogadja. [éééé, éééé-[M] M, éééé-[M] M-[d] d, yyyy-[M] M-[d] d, yyyy-[M] M-[d] d, yyyy-[M] M-[d] dT *]
* ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``
*********************************
<code>toDecimal</code>
==============================
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : integral], [<i>&lt;value3&gt;</i> : integral], [<i>&lt;value4&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Bármely numerikus vagy karakterlánc decimális értékké alakítja. Ha a pontosság és skála nincs megadva, a (10,2) alapértelmezés szerint. Nem kötelező Java decimális formátum az átalakításhoz is használható.
* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
*********************************
<code>toDouble</code>
==============================
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => double</b></code><br/><br/>
Egy dupla értéket bármely numerikus vagy karakterlánc alakítja. Nem kötelező Java decimális formátum az átalakításhoz is használható.
* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
*********************************
<code>toFloat</code>
==============================
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => float</b></code><br/><br/>
Bármely numerikus vagy karakterlánc lebegőpontos értékké alakítja. Nem kötelező Java decimális formátum az átalakításhoz is használható. Minden olyan dupla csonkolja
* ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``
*********************************
<code>toInteger</code>
==============================
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => integer</b></code><br/><br/>
Bármely numerikus vagy karakterlánc alakít egy egész számot. Nem kötelező Java decimális formátum az átalakításhoz is használható. Minden olyan hosszú, lebegőpontos double csonkolja
* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
*********************************
<code>toLong</code>
==============================
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => long</b></code><br/><br/>
Bármely numerikus vagy karakterlánc hosszú értékké alakítja. Nem kötelező Java decimális formátum az átalakításhoz is használható. Bármely lebegőpontos double csonkolja
* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
*********************************
<code>toShort</code>
==============================
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => short</b></code><br/><br/>
Bármely numerikus vagy karakterlánc rövid értékké alakítja. Nem kötelező Java decimális formátum az átalakításhoz is használható. Minden olyan egész számot, hosszú, lebegőpontos, dupla csonkolja
* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
*********************************
<code>toString</code>
==============================
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Egy karakterláncot egy egyszerű datatype alakítja át. A számok és dátum formátumban adható meg. Ha nincs megadva, a rendszer alapértelmezett kivétele történik. Számok Java decimális formátum használható. Tekintse meg a Java SimpleDateFormat minden lehetséges dátumformátum; az alapértelmezett formátuma éééé-hh-nn
* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``
*********************************
<code>toTimestamp</code>
==============================
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Egy karakterláncot alakít egy nem kötelező időbélyeg formátuma megadott dátumot. Tekintse meg az összes lehetséges formátumok Java SimpleDateFormat. Ha nincs megadva a történő küldés időbélyegzője legyen az alapértelmezett minta éééé-[M] M-[d] d óó [. f...] szolgál
* ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``
*********************************
<code>toUTC</code>
==============================
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Konvertálja az időbélyeg (UTC). Megadhat egy választható időzóna "GMT", "PST", 'UTC', "Amerika/Kajmán" formájában. Ez alapértelmezés szerint az aktuális időzóna
* ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>translate</code>
==============================
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Cserélje le egy másik készlet karakter a karakterláncban szereplő karakterek egy készletét. Karakterek 1 és 1 közötti csere rendelkezik
* ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
*********************************
<code>trim</code>
==============================
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Kezdő és záró karakterlánc levágja. Ha a második paraméter nincs megadva, azt levágja a szóközöket. Ellenkező esetben azt levágja a második paraméterben megadott bármely karakter
* ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``
*********************************
<code>true</code>
==============================
<code><b>true() => boolean</b></code><br/><br/>
Mindig igaz értéket ad vissza. A függvény syntax(true()) használhatja, ha egy "true" nevű oszlop neve
* ``isDiscounted == true()``
* ``isDiscounted() == true``
*********************************
<code>typeMatch</code>
==============================
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Az oszlop típusa megegyezik. Csak akkor használható, a minta expressions.number megtalálható rövid, egész szám, long, double, lebegőpontos vagy decimális szerves felel meg, egész szám, dupla hosszú, a tört egyezés, lebegőpontos decimálisra, és a dátum és idő egyezések dátum- vagy időbélyegző-típus
* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``
*********************************
<code>upper</code>
==============================
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Egy karakterlánc uppercases
* ``upper('bojjus') -> 'BOJJUS'``
*********************************
<code>variance</code>
==============================
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Lekérdezi a varianciát egy oszlop
* ``variance(sales) -> 122.12``
*********************************
<code>varianceIf</code>
==============================
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy feltétel alapján, a varianciát egy oszlop beolvasása
* ``varianceIf(region == 'West', sales) -> 122.12``
*********************************
<code>variancePopulation</code>
==============================
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop statisztikai sokaságra számított varianciáját beolvasása
* ``variancePopulation(sales) -> 122.12``
*********************************
<code>variancePopulationIf</code>
==============================
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy feltétel alapján, az oszlop statisztikai sokaságra számított varianciáját beolvasása
* ``variancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>varianceSample</code>
==============================
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop torzítatlan populáció varianciáját beolvasása
* ``varianceSample(sales) -> 122.12``
*********************************
<code>varianceSampleIf</code>
==============================
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy feltétel alapján, torzítatlan populáció varianciáját oszlop beolvasása
* ``varianceSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>weekOfYear</code>
==============================
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Lekérdezi a megadott date év hete
* ``weekOfYear(toDate('2008-02-20')) -> 8``
*********************************
<code>xor</code>
==============================
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai kizáró vagy műveletet. Ugyanaz, mint a ^ operátorral
* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
*********************************
<code>year</code>
==============================
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
A dátum az év értéke beolvasása
* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan használható a Kifejezésszerkesztő](concepts-data-flow-expression-builder.md)
