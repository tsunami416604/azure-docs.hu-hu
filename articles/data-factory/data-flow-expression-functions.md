---
title: Expression functions a Azure Data Factory leképezés adatáramlási funkciójában
description: Tudnivalók a Expression functions szolgáltatásról a leképezési adatforgalomban.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 72c516f0a6e377cc16205917967482a29b4fdfbd
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "69036220"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Adatátalakítási kifejezések a leképezési adatfolyamban 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>Expression függvények

A Data Factoryban az adatátalakítások konfigurálásához használja a leképezési adatfolyam funkciójának kifejezés nyelvét.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Pár szám pozitív modulusa.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a koszinusz inverz értékét.* ``acos(1) -> 0.0``
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Karakterláncok vagy számok párokat adja meg. A dátum és a napok számát adja hozzá. Egy hasonló típusú tömb hozzáfűzése egy másikhoz. Ugyanaz, mint a + operátor* ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Napok hozzáadása dátumhoz vagy időbélyeghez. Ugyanaz, mint a + operátor a dátumhoz* ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Hónapok hozzáadása dátumhoz vagy időbélyeghez* ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai és operátor. Ugyanaz, mint a & &* ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Inverz szinuszos értéket számít ki.* ``asin(0) -> 0.0``
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy inverz tangens értékét számítja ki.* ``atan(0) -> 0.0``
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A sík pozitív x tengelye és a koordináták által megadott pont közötti szöget adja vissza radiánban.* ``atan2(0, 0) -> 0.0``
___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy oszlop értékeinek átlagát kéri le.* ``avg(sales) -> 7523420.234``
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
A feltételek alapján egy oszlop értékeinek átlagát kapja meg.* ``avgIf(region == 'West', sales) -> 7523420.234``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Oszlop értékének kiválasztása a streamben név szerint. Ha több egyezés van, a rendszer az első egyezést adja vissza. Ha nincs egyezés, NULL értéket ad vissza. A visszaadott értéket a Type konverziós függvények (TO_DATE, TO_STRING...) egyikének kell átalakítania.  A tervezési időszakban ismert oszlopnevek csak a nevük alapján kezelhetők. A számított bemenetek nem támogatottak, de használhat paraméterekkel való helyettesítést is.* ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Oszlop értékének kiválasztása az adatfolyamban lévő relatív pozíció (1 alapú) alapján. Ha a pozíció kívül esik a határértékeken, NULL értéket ad vissza. A visszaadott értéket a Type konverziós függvények egyikének kell átalakítania (TO_DATE, TO_STRING...). A számított bemenetek nem támogatottak, de használhat paraméterekkel való helyettesítést is.* ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
A váltakozó feltételek alapján egy vagy több érték is érvényes. Ha a bemenetek száma páros, a másik NULL értékű az utolsó feltételnél.* ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy szám adatkockájának gyökerét számítja ki.* ``cbrt(8) -> 2.0``
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
A legkisebb olyan egész számot adja vissza, amely nem kisebb, mint a szám* ``ceil(-0.1) -> 0``
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Az első nem null értéket adja vissza bemenetek készletében. Minden bemenetnek azonos típusúnak kell lennie* ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Összehasonlítja az azonos típusú két értéket. Negatív egész számot ad vissza, ha érték1 < érték2, 0, ha érték1 = = érték2, pozitív érték, ha érték1 > érték2* ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Változó számú karakterlánc összefűzése egymással. Ugyanaz, mint a + operátor sztringekkel* ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Változó számú karakterláncot fűz össze egy elválasztóval. Az első paraméter az elválasztó* ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a koszinusz értékét* ``cos(10) -> -0.83907152907``
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy érték koszinusz hiperbolikusát számítja ki.* ``cosh(0) -> 1.0``
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Az értékek összesített számának beolvasása. Ha a választható oszlop (ok) meg van adva, a rendszer figyelmen kívül hagyja a NULL értékeket a darabszámban.* ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Egy adott oszlop különböző értékeinek összesített számát jeleníti meg.* ``countDistinct(custId, custName) -> 60``
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
A feltételek alapján lekéri az értékek összesített számát. Ha a választható oszlop meg van adva, a rendszer figyelmen kívül hagyja a NULL értékeket a darabszámban.* ``countIf(state == 'CA' && commission < 10000, name) -> 100``
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A két oszlop közötti sokasági eltérés beolvasása* ``covariancePopulation(sales, profit) -> 122.12``
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján beolvassa a két oszlop sokasági eltérését* ``covariancePopulationIf(region == 'West', sales) -> 122.12``
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Két oszlop minta-összetételének beolvasása* ``covarianceSample(sales, profit) -> 122.12``
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján beolvassa két oszlop minta-eltérését* ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Kiszámítja a különböző primitív adattípusok oszlopának CRC32 kivonatát egy kis hosszúságú értékkel, amely csak 0 (256), 224, 256, 384, 512 értékű lehet. Egy sor ujjlenyomatának kiszámításához használható.* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
A CumeDist függvény kiszámítja egy érték pozícióját a partícióban lévő összes értékhez viszonyítva. Ennek eredményeképpen a partíció rendezésének aktuális sorával megegyező vagy azzal egyenlő sorok száma elosztva a Windows-partíció sorainak teljes számával. A rendezésben szereplő összes döntetlen érték ugyanarra a pozícióra lesz kiértékelve.
* ``cumeDist() -> 1``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Lekérdezi az aktuális dátumot, amikor a feladatnak futnia kell. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként.
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Az aktuális időbélyeg beolvasása, amikor a rendszer a feladatot a helyi időzónával kezdi futtatni* ``currentTimestamp() -> 12-12-2030T12:12:12``
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Beolvassa az aktuális időbélyeget UTC-ként. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. Alapértelmezés szerint az aktuális időzóna* ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
A hónap napját adja meg egy dátummal* ``dayOfMonth(toDate('2018-06-08')) -> 08``
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
A hét napját adja meg egy dátummal. 1 – vasárnap, 2 – hétfő..., 7 – szombat* ``dayOfWeek(toDate('2018-06-08')) -> 7``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Az év napját adja meg* ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
A radián konvertálása fok-ra* ``degrees(3.141592653589793) -> 180``
___
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Kiszámítja egy érték rangsorát az értékek egy csoportjában. Az eredmény az a sor, amely a partíció sorrendje szerint az aktuális sorral megegyező vagy annál korábbi sorok számát jelöli. Az értékek nem hoznak létre hézagokat a sorozatban. A sűrű rangsor akkor is működik, ha az adatok nem rendezettek, és az értékek változását keresi* ``denseRank(salesQtr, salesAmt) -> 1``
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Egy pár számot oszt szét. Ugyanaz, mint a/operátor* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc a megadott karakterlánccal végződik-e* ``endsWith('great', 'eat') -> true``
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítás egyenlő operátor. Ugyanaz, mint = = operátor* ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Az összehasonlítási művelet figyelmen kívül hagyja a kis-és nagybetűket. Ugyanaz, mint a < = > operátor* ``'abc'<==>'abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Egy szám faktoriálisát kiszámítása* ``factorial(5) -> 120``
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Mindig hamis értéket ad vissza. Ha van "false" nevű oszlop, használja a függvény szintaxisát (false ()).* ``isDiscounted == false()``
* ``isDiscounted() == false``
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Egy oszlopcsoport első értékének beolvasása. Ha a második paraméter ignoreNulls ki van hagyva, a rendszer hamis értéket feltételez.* ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
A legnagyobb egész számot adja vissza, amely nem nagyobb, mint a szám* ``floor(-0.1) -> -1``
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Az UTC értékre konvertálja az időbélyeget. Igény szerint átadhatja az időzónát "GMT", "PST", "UTC", "Amerika/Kajmán" formátumban. Alapértelmezés szerint az aktuális időzóna* ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítási nagyobb operátor. Ugyanaz, mint > operátor* ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítás nagyobb vagy egyenlő operátorral. Ugyanaz, mint a > = operátor* ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
A legnagyobb értéket adja vissza az értékek listájában, bemenetként. Null értéket ad vissza, ha az összes bemenet null értékű* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Egy időbélyeg óra értékének beolvasása. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként.
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Egy feltétel alapján egy vagy több érték is érvényes. Ha más nincs meghatározva, akkor NULL értékűnek számít. Mindkét értéknek kompatibilisnek kell lennie (numerikus, karakterlánc...)* ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Ellenőrzi, hogy egy elem szerepel-e a tömbben* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Minden szó első betűjét nagybetűssé alakítja. A szavak szóközzel elválasztva vannak azonosítva* ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Megkeresi a karakterláncban található alsztring pozícióját (1 alapján). a 0 értéket adja vissza, ha nem található* ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor törlésre van-e megjelölve. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1* ``isDelete() -> true``
* ``isDelete(1) -> false``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor hibásként van-e megjelölve. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1* ``isError() -> true``
* ``isError(1) -> false``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a rendszer figyelmen kívül hagyja-e a sort. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1* ``isIgnore() -> true``
* ``isIgnore(1) -> false``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor be van-e jelölve a beszúráshoz. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1* ``isInsert() -> true``
* ``isInsert(1) -> false``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor egyezik-e a kereséssel. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1* ``isMatch() -> true``
* ``isMatch(1) -> false``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Annak ellenőrzése, hogy az érték NULL-e* ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a sor frissítésre van-e megjelölve. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1* ``isUpdate() -> true``
* ``isUpdate(1) -> false``
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop csúcsosságát beolvasása* ``kurtosis(sales) -> 122.12``
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop csúcsosságát.* ``kurtosisIf(region == 'West', sales) -> 122.12``
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Az első paraméter értékének beolvasása n sor az aktuális sor előtt. A második paraméter a visszakeresett sorok száma, az alapértelmezett érték pedig 1. Ha nincs annyi sor, a rendszer null értéket ad vissza, kivéve, ha meg van adva egy alapértelmezett érték.* ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Egy oszlopcsoport utolsó értékének beolvasása. Ha a második paraméter ignoreNulls ki van hagyva, a rendszer hamis értéket feltételez.* ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
A hónap utolsó napját adja meg egy dátummal.* ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Az első paraméter értékének beolvasása az aktuális sor után n sorban. A második paraméter a megtekinteni kívánt sorok száma, az alapértelmezett érték pedig 1. Ha nincs annyi sor, a rendszer null értéket ad vissza, kivéve, ha meg van adva egy alapértelmezett érték.* ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Összehasonlítás kisebb vagy egyenlő operátorral. Ugyanaz, mint a < = operátor* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Egy alsztring kibontása az 1. indexnél a karakterek számával. Ugyanaz, mint az alsztring (Str, 1, n)* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
A karakterlánc hosszát adja vissza.* ``length('kiddo') -> 5``
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítás nélküli operátor. Ugyanaz, mint < operátor* ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítás kisebb vagy egyenlő operátorral. Ugyanaz, mint a < = operátor* ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Lekéri a levenshtein távolságot két karakterlánc között* ``levenshtein('boys', 'girls') -> 4``
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
A minta egy, a szó szoros egyezésű karakterlánc. A kivételek a következő speciális szimbólumok: _ a bemenet bármelyik karakterének felel meg (ehhez hasonlóan). a POSIX reguláris kifejezésekben a (z)% a bemenet nulla vagy több karakterének felel meg (a következőhöz hasonlóan: POSIX reguláris kifejezés).
Az escape-karakter a következő: "". Ha egy escape-karakter egy speciális szimbólum vagy egy másik escape-karakter előtt következik be, a következő karakter egyeztetése szó szerint történik. A többi karakter Escape-értéke érvénytelen.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Megkeresi az alsztring pozícióját (1) egy adott pozíciót indító karakterláncon belül. Ha a pozíció kimarad, a karakterlánc elejétől számít. a 0 értéket adja vissza, ha nem található* ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Kiszámítja a napló értékét. Egy opcionális alap is megadható, ha használatban van egy Euler-szám* ``log(100, 10) -> 2``
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a log értéket 10 alap alapján* ``log10(100) -> 2``
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Kisbetűs karakterlánc* ``lower('GunChus') -> 'gunchus'``
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
A bal oldali Kitöltés után a karakterláncot a megadott kitöltéssel kell ellátni, amíg az adott hossz nem lesz. Ha a karakterlánc egyenlő vagy nagyobb, mint a hossz, akkor * ``lpad('great', 10, '-') -> '___--great'`` a rendszer nem op 
* ``lpad('great', 4, '-') -> 'great'`` 
* "lpad (" nagy ", 8," < > ")->" < ><great'``
___
### <code>LTrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
A bal oldali levágja a kezdő karakterek sztringjét. Ha a második paraméter nem lett megadva, a szóközöket vágja le. Egyéb esetben a második paraméterben megadott bármely karaktert felvágja.* ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Egy oszlop maximális értékének beolvasása* ``MAX(sales) -> 12312131.12``
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop maximális értékét.* ``maxIf(region == 'West', sales) -> 99999.56``
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Kiszámítja a változó primitív adattípusú oszlopok MD5-kivonatát, és egy 32 karakteres hexadecimális karakterláncot ad vissza. Egy sor ujjlenyomatának kiszámításához használható.* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy oszlop értékeinek középértékét kapja meg. Ugyanaz, mint az AVG* ``mean(sales) -> 7523420.234``
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
A feltételek alapján egy oszlop értékeinek középértékét kapja meg. Ugyanaz, mint a avgIf* ``meanIf(region == 'West', sales) -> 7523420.234``
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Lekéri egy dátum ezredmásodperc értékét. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként.
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Egy oszlop minimális értékének beolvasása* ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop minimális értékét.* ``minIf(region == 'West', sales) -> 00.01``
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Számok kivonása. Kivonás a napok számától számítva. Ugyanaz, mint a-operátor* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Egy időbélyeg perces értékének beolvasása. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként.
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Pár számjegyből álló modulus. Ugyanaz, mint a (z)% operátor* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Egy dátum vagy időbélyeg hónapjának értékének beolvasása* ``month(toDate('2012-8-8')) -> 8``
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
Lekérdezi a két datesYou közötti hónapok számát "GMT", "PST", "UTC", "Amerika/Cayman" formában. A helyi időzónát használja a rendszer alapértelmezettként.
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
A számok összeszorzása. Ugyanaz, mint az * operátor* ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
A NTile függvény az egyes ablakos partíciók `n` sorait az 1 és `n`a közötti érték között osztja szét. A gyűjtő értéke legfeljebb 1 lesz. Ha a partícióban lévő sorok száma nem egyenletesen oszlik meg a gyűjtők számával, akkor a fennmaradó értékeket a rendszer egy gyűjtőre osztja szét, az első gyűjtőtől kezdve. A NTile függvény a tertiles, a quartiles, a deciles és más gyakori összesítő statisztikák kiszámításához hasznos. A függvény két változót számít ki az inicializálás során: A normál gyűjtők mérete egy további sorral bővül. Mindkét változó az aktuális partíció méretétől függ. A számítási folyamat során a függvény nyomon követi az aktuális sorszámot, az aktuális gyűjtő számát, valamint azt a sorszámot, amelynél a gyűjtő módosul (bucketThreshold). Ha az aktuális sor száma eléri a gyűjtő küszöbértékét, a rendszer eggyel növeli a gyűjtő értékét, a küszöbértéket pedig a gyűjtő mérete növeli (plusz egy extra, ha az aktuális gyűjtő betömött).
* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy számot tagad. A pozitív számok negatívra váltása és fordítva* ``negate(13) -> -13``
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
A következő egyedi sorozatot adja vissza. A szám egymást követő, csak egy partíción belül, és a partitionId előtaggal van ellátva.* ``nextSequence() -> 12313112``
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
A karakterlánc értékének normalizálása az ékezetes Unicode-karakterek elkülönítéséhez* ``normalize('boys') -> 'boys'``
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai tagadás operátor* ``not(true) -> false``
* ``not(premium)``
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Az összehasonlítás nem egyenlő az operátorral. Ugyanaz, mint a! = operátor* ``12!=24 -> true``
* ``'abc'!='abc' -> false``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
NULL értéket ad vissza. Ha van "NULL" nevű oszlop, használja a Function szintaxist (null ()). A által használt összes művelet NULL értéket fog eredményezni.* ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai vagy operátor. Ugyanaz, mint | |* ``or(true, false) -> true``
* ``true || false -> true``
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Pár szám pozitív modulusa.
* ``pmod(-20, 8) -> 4``
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy számot vet fel egy másik hatványra* ``power(10, 2) -> 100``
___
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Kiszámítja egy érték rangsorát az értékek egy csoportjában. Az eredmény az a sor, amely a partíció sorrendje szerint az aktuális sorral megegyező vagy annál korábbi sorok számát jelöli. Az értékek a sorozatban mutatkozó hézagokat eredményezik. A Rank akkor is működik, ha az adatok nem rendezettek, és az értékek változását keresi* ``rank(salesQtr, salesAmt) -> 1``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Egyező alsztring kinyerése egy adott regex-mintához. Az utolsó paraméter azonosítja az egyeztetési csoportot, és alapértelmezés szerint 1, ha nincs megadva. A (<regex>z) "" (vissza idézőjel) használatával megegyező karakterláncot kell megadnia a szökés nélkül* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc megfelel-e a megadott regex-mintának. A (<regex>z) "" (vissza idézőjel) használatával megegyező karakterláncot kell megadnia a szökés nélkül* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Egy regex-minta összes előfordulásának lecserélése egy másik, a megadott karakterláncban található alsztringre a (z) "<regex>" (vissza idézőjel) kifejezés használatával* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Karakterlánc felosztása egy határolójel alapján, és a karakterláncok tömbjét adja vissza.* ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Egy alsztring összes előfordulásának cseréje egy másik, az adott karakterláncban szereplő alkarakterlánccal* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Karakterlánc megfordítása* ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Kibont egy alsztringet a jobb oldali karakterek számával. Ugyanaz, mint az alsztring (Str, LENGTH (Str)-n, n)* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc megfelel-e a megadott regex-mintának* ``rlike('200.50', '(\d+).(\d+)') -> true``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Egy számot kerekít egy opcionális skálán és egy opcionális kerekítési módra. Ha a skála nincs megadva, a rendszer alapértelmezés szerint 0 értéket ad meg.  Ha a mód nincs megadva, a rendszer alapértelmezés szerint a ROUND_HALF_UP (5) értéket adja meg. A kerekítési értékek közé tartozik az 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Szekvenciális sorszámozást rendel egy ablak soraihoz, 1-től kezdődően.* ``rowNumber() -> 1``
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
A jobb gombbal a karakterláncot a megadott kitöltés alapján kell kiadni, amíg az adott hosszúságú nem lesz. Ha a karakterlánc egyenlő vagy nagyobb, mint a hossz * ``rpad('great', 10, '-') -> 'great___--'``, akkor nem op 
* ``rpad('great', 4, '-') -> 'great'`` 
* ``rpad('great', 8, '<>') -> 'great<><'`` 
___
### <code>rtrim</code>-RTrim minősül.</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
A jobb oldali levágja a kezdő karakterek egy sztringjét. Ha a második paraméter nem lett megadva, a szóközöket vágja le. Egyéb esetben a második paraméterben megadott bármely karaktert felvágja.* ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Egy dátum második értékét kéri le. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként.
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Kiszámítja a változó primitív adattípusok oszlopának SHA-1 kivonatát, és egy 40 karakteres hexadecimális karakterláncot ad vissza. Egy sor ujjlenyomatának kiszámításához használható.* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Kiszámítja a változó primitív adattípusú oszlop SHA-2 kivonatát, amely egy kis hosszúságú, ami csak 0 (256), 224, 256, 384, 512 érték lehet. Egy sor ujjlenyomatának kiszámításához használható.* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Szinuszos értéket számít ki* ``sin(2) -> 0.90929742682``
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a szinusz hiperbolikus értékét.* ``sinh(0) -> 0.0``
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop torzításának beolvasása* ``skewness(sales) -> 122.12``
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy adott feltétel alapján lekéri egy oszlop döntését* ``skewnessIf(region == 'West', sales) -> 122.12``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Egy tömb részhalmazának kibontása egy pozícióból. A pozíció 1 alapú. Ha a hossz nincs megadva, a rendszer alapértelmezés szerint a karakterlánc végét adja meg.* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
A karakterlánc Soundex-kódjának beolvasása* ``soundex('genius') -> 'G520'``
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Karakterlánc felosztása elválasztó alapján, karakterláncok tömbjét adja vissza.* ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja egy szám négyzetének gyökerét.* ``sqrt(9) -> 3``
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc a megadott karakterlánccal kezdődik-e* ``startsWith('great', 'gr') -> true``
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop szórásának beolvasása* ``stdDev(sales) -> 122.12``
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Egy adott feltétel alapján lekéri egy oszlop szórását.* ``stddevIf(region == 'West', sales) -> 122.12``
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop sokasági szórásának beolvasása* ``stddevPopulation(sales) -> 122.12``
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop sokasági szórását* ``stddevPopulationIf(region == 'West', sales) -> 122.12``
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop minta szórásának beolvasása* ``stddevSample(sales) -> 122.12``
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop szórását.* ``stddevSampleIf(region == 'West', sales) -> 122.12``
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Hónapok kivonása egy dátumból. Ugyanaz, mint a-operátor a dátumhoz* ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Hónapok kivonása dátumból vagy időbélyegből* ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Egy adott hosszúságú karakterlánc kibontása egy adott pozícióból. A pozíció 1 alapú. Ha a hossz nincs megadva, a rendszer alapértelmezés szerint a karakterlánc végét adja meg.* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy numerikus oszlop összesített összegének beolvasása* ``sum(col) -> value``
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy numerikus oszlop különböző értékeinek összesített összegét kéri le.* ``sumDistinct(col) -> value``
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
A feltételek alapján lekéri egy numerikus oszlop összesített összegét. A feltétel bármely oszlopon alapulhat* ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
A feltételek alapján lekéri egy numerikus oszlop összesített összegét. A feltétel bármely oszlopon alapulhat* ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a tangens értékét* ``tan(0) -> 0.0``
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy hiperbolikus tangens értékét számítja ki.* ``tanh(0) -> 0.0``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
A ("", "true", "y", "yes", "1") értéket True értékre ("f", "false", "n", "No", "0") konvertálja hamis értékre, és NULL értéket bármilyen más értékhez.* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Egy karakterláncot egy dátumra konvertál, amely nem kötelező dátumformátum. Tekintse meg a Java SimpleDateFormat az összes lehetséges formátumot. Ha a dátumformátum nincs megadva, a rendszer az alábbi kombinációkat fogadja el. [éééé, éééé-[M] M, éééé-[M] M-[d] d, éééé-[M] M-[d] d, éééé-[M] M-[d] d, éééé-[M] M-[d] dT *]* ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Bármilyen numerikus vagy sztringet decimális értékké alakít. Ha a pontosság és a skála nincs megadva, a rendszer alapértelmezés szerint a következőt adja meg: (10, 2). Az átalakításhoz választható Java decimális formátumot is használhat. Egy opcionális területi beállítás formátuma BCP47 nyelven, például en-US, de, zh-CN* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Bármely numerikus vagy sztringet dupla értékre konvertál. Az átalakításhoz választható Java decimális formátumot is használhat. Egy opcionális területi beállítás formátuma BCP47 nyelven, például en-US, de, zh-CN* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Bármely numerikus vagy sztringet lebegőpontos értékké alakít. Az átalakításhoz választható Java decimális formátumot is használhat. Bármilyen dupla csonkítás* ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Bármilyen numerikus vagy sztringet egész értékre konvertál. Az átalakításhoz választható Java decimális formátumot is használhat. Levágja a hosszú, lebegőpontos, dupla* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Tetszőleges numerikus vagy sztringet alakít át hosszú értékre. Az átalakításhoz választható Java decimális formátumot is használhat. Bármilyen lebegőpontos, dupla érték csonkítása* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Bármilyen numerikus vagy sztringet alakít át egy rövid értékre. Az átalakításhoz választható Java decimális formátumot is használhat. Egész szám, hosszú, lebegőpontos, dupla érték csonkítása* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Egy primitív adattípust karakterlánccá alakít át. A számok és a dátum formátuma megadható. Ha nincs megadva, a rendszer alapértelmezés szerint ki van választva. A Java decimális formátum a számok esetében használatos. Tekintse meg a Java SimpleDateFormat az összes lehetséges dátumformátumot; az alapértelmezett formátum: éééé-hh-nn* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Karakterláncot alakít át egy dátumra, és nem kötelező időbélyeg-formátumot kap. Tekintse meg a Java SimpleDateFormat az összes lehetséges formátumot. Ha az időbélyeg ki van hagyva, az alapértelmezett mint éééé-[M] M-[d] d óó: PP: SS [. f...] van használatban* ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Az időbélyeget UTC értékre alakítja. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. Alapértelmezés szerint az aktuális időzóna* ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Egy karakterkészletet cseréljen fel egy másik karakterkészletre a karakterláncban. A karakterek 1 és 1 közötti helyettesítéssel rendelkeznek* ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Egy kezdő és záró karakterből álló karakterláncot metsz. Ha a második paraméter nem lett megadva, a szóközöket vágja le. Egyéb esetben a második paraméterben megadott bármely karaktert felvágja.* ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Mindig igaz értéket ad vissza. Ha van "true" nevű oszlop, használja a Function szintaxist (true ()).* ``isDiscounted == true()``
* ``isDiscounted() == true``
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Az oszlop típusának felel meg. Csak minta kifejezésekben használható. a szám a rövid, az egész, a hosszú, a dupla, az float vagy a decimális, az integrált egyezések rövid, egész, hosszú, töredékes egyezések dupla, lebegőpontos, decimális és datetime értékkel egyezik a dátummal vagy az időbélyeg típusával* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Karakterláncok nagybetűi* ``upper('bojjus') -> 'BOJJUS'``
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop eltérésének beolvasása* ``variance(sales) -> 122.12``
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop varianciaát.* ``varianceIf(region == 'West', sales) -> 122.12``
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop populációbeli eltérésének beolvasása* ``variancePopulation(sales) -> 122.12``
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop populációjának eltérését* ``variancePopulationIf(region == 'West', sales) -> 122.12``
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy oszlop elfogulatlan eltérésének beolvasása* ``varianceSample(sales) -> 122.12``
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A feltételek alapján lekéri egy oszlop elfogulatlan eltérését.* ``varianceSampleIf(region == 'West', sales) -> 122.12``
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Az év hetet adja meg egy dátummal* ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai XOR operátor. Ugyanaz, mint a ^ operátor* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Egy dátum év értékének beolvasása* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>További lépések

[Ismerje meg a Expression Builder használatát](concepts-data-flow-expression-builder.md).
