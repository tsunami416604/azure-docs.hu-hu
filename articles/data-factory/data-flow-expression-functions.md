---
title: Expression functions a Azure Data Factory leképezés adatáramlási funkciójában
description: Tudnivalók a Expression functions szolgáltatásról a leképezési adatforgalomban.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 27d968aa5202fbeb38be9a2416514d2185c1d8b9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436742"
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
Kiszámítja a koszinusz inverzének értékét * ``acos(1) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Karakterláncok vagy számok párokat adja meg. A dátum és a napok számát adja hozzá. Egy időtartamot rendel egy időbélyeghez. Egy hasonló típusú tömb hozzáfűzése egy másikhoz. Ugyanaz, mint a + operátor * ``add(10, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Napok hozzáadása dátumhoz vagy időbélyeghez. Ugyanaz, mint a + operátor a Date * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')`` @ no__t-1 @ no__t-2<br/><br/>
Hónapok hozzáadása dátumhoz vagy időbélyeghez. Igény szerint átadhat egy időzónát * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Logikai és operátor. Ugyanaz, mint a & & * ``and(true, false) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Inverz szinuszos értéket számít ki * ``asin(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Egy inverz tangens értékét számítja ki * ``atan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Azt a szöget adja vissza radiánban, amely a sík pozitív x tengelye és a koordináták által megadott pont között * ``atan2(0, 0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Egy oszlop értékeinek átlagát kéri * ``avg(sales)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján lekéri egy oszlop értékeinek átlagát * ``avgIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Oszlop értékének kiválasztása a streamben név szerint. A második argumentumként átadhat egy opcionális stream-nevet. Ha több egyezés van, a rendszer az első egyezést adja vissza. Ha nincs egyezés, NULL értéket ad vissza. A visszaadott értéket a Type konverziós függvények (TO_DATE, TO_STRING...) egyikének kell átalakítania.  A tervezési időszakban ismert oszlopnevek csak a nevük alapján kezelhetők. A számított bemenetek nem támogatottak, de használhat paraméteres helyettesítéseket * ``toString(byName('parent'))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Oszlop értékének kiválasztása az adatfolyamban lévő relatív pozíció (1 alapú) alapján. Ha a pozíció kívül esik a határértékeken, NULL értéket ad vissza. A visszaadott értéket a Type konverziós függvények egyikének kell átalakítania (TO_DATE, TO_STRING...). A számított bemenetek nem támogatottak, de használhat paraméteres helyettesítéseket * ``toString(byPosition(1))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
A váltakozó feltételek alapján egy vagy több érték is érvényes. Ha a bemenetek száma páros, a másik alapértelmezett értéke NULL az utolsó feltétel esetében * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
@No__t-0 @ no__t-1 @ no__t-2 számú kocka gyökerének kiszámítása<br/><br/>
A legkisebb olyan egész számot adja vissza, amely nem kisebb, mint a szám * ``ceil(-0.1) -> 0`` @ no__t-1 @ no__t-2<br/><br/>
Az első nem null értéket adja vissza bemenetek készletében. Minden bemenetnek azonos típusúnak kell lennie * ``coalesce(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Összehasonlítja az azonos típusú két értéket. Negatív egész számot ad vissza, ha érték1 < érték2, 0, ha érték1 = = érték2, pozitív érték, ha érték1 > érték2 * ``(compare(12, 24) < 1) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Változó számú karakterlánc összefűzése egymással. Ugyanaz, mint a + operátor a sztringekkel * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Változó számú karakterláncot fűz össze egy elválasztóval. Az első paraméter az elválasztó * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Igaz értéket ad vissza, ha a megadott tömb bármely eleme igaz értékre értékeli a megadott predikátumban. A tartalmaz egy hivatkozást a predikátum függvény egyik elemére #item * ``contains([1, 2, 3, 4], #item == 3) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kiszámítja a koszinusz értékét * ``cos(10) -> -0.8390715290764524`` @ no__t-1 @ no__t-2<br/><br/>
Egy érték koszinusz hiperbolikusát számítja ki * ``cosh(0) -> 1.0`` @ no__t-1 @ no__t-2<br/><br/>
Az értékek összesített számának beolvasása. Ha a nem kötelező oszlop (ok) meg van adva, a rendszer figyelmen kívül hagyja a NULL értékeket a Count * ``count(custId)`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Lekérdezi az oszlopok egy halmazának különböző értékeinek összesített számát * ``countDistinct(custId, custName)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján lekéri az értékek összesített számát. Ha a választható oszlop meg van adva, a rendszer figyelmen kívül hagyja a NULL értékeket a Count * ``countIf(state == 'CA' && commission < 10000, name)`` @ no__t-1 @ no__t-2<br/><br/>
Lekérdezi a sokaság közötti eltérést két oszlop között * ``covariancePopulation(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján beolvassa a két oszlop sokasági eltérését * ``covariancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Beolvassa a két oszlop minta-eltérését * ``covarianceSample(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján beolvassa két oszlop minta-szórását * ``covarianceSampleIf(region == 'West', sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Kiszámítja a különböző primitív adattípusok oszlopának CRC32 kivonatát egy kis hosszúságú értékkel, amely csak 0 (256), 224, 256, 384, 512 értékű lehet. Felhasználható egy sor ujjlenyomatának kiszámítására * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L`` @ no__t-1 @ no__t-2<br/><br/>
A CumeDist függvény kiszámítja egy érték pozícióját a partícióban lévő összes értékhez viszonyítva. Ennek eredményeképpen a partíció rendezésének aktuális sorával megegyező vagy azzal egyenlő sorok száma elosztva a Windows-partíció sorainak teljes számával. A rendezésben szereplő összes döntetlen érték ugyanarra a pozícióra lesz kiértékelve.
* ``cumeDist()``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Lekérdezi az aktuális dátumot, amikor a feladatnak futnia kell. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Lekérdezi az aktuális időbélyeget, amikor a feladatok a helyi időzónával kezdődnek * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false`` @ no__t-1 @ no__t-2<br/><br/>
Az aktuális időbélyeg beolvasása UTC-ként. Ha azt szeretné, hogy a jelenlegi idő a fürt időzónája szerint egy másik időzónában legyen értelmezve, a választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában lehet átadni. Alapértelmezés szerint az aktuális időzóna. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. A [SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html) használatával alakítsa át az UTC-időt egy másik időzónára a fromUTC () használatával.
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Beolvassa a hónap napját a dátum * ``dayOfMonth(toDate('2018-06-08')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
A hét napját adja meg egy dátummal. 1 – vasárnap, 2 – hétfő..., 7 – szombat * ``dayOfWeek(toDate('2018-06-08')) -> 6`` @ no__t-1 @ no__t-2<br/><br/>
Az év napjának beolvasása * ``dayOfYear(toDate('2016-04-09')) -> 100`` @ no__t-1 @ no__t-2 dátummal<br/><br/>
A napok számának időtartama (ezredmásodpercben) * ``days(2) -> 172800000L`` @ no__t-1 @ no__t-2<br/><br/>
A radián konvertálása fok * ``degrees(3.141592653589793) -> 180`` @ no__t-1 @ no__t-2<br/><br/>
Kiszámítja egy érték rangsorát az értékek egy csoportjában. Az eredmény az a sor, amely a partíció sorrendje szerint az aktuális sorral megegyező vagy annál korábbi sorok számát jelöli. Az értékek nem hoznak létre hézagokat a sorozatban. A sűrű rangsor akkor is működik, ha az adatok nem rendezettek, és az értékek változását keresi * ``denseRank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Egy pár számot oszt szét. Ugyanaz, mint a/operátor * ``divide(20, 10) -> 2`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ellenőrzi, hogy a karakterlánc a megadott karakterlánccal végződik-e * ``endsWith('dumbo', 'mbo') -> true`` @ no__t-1 @ no__t-2<br/><br/>
Összehasonlítás egyenlő operátor. Ugyanaz, mint = = operátor * ``equals(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Az összehasonlítási művelet figyelmen kívül hagyja a kis-és nagybetűket. Ugyanaz, mint a < = > operátor * ``'abc'<=>'Abc' -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Egy szám faktoriálisát kiszámítása * ``factorial(5) -> 120`` @ no__t-1 @ no__t-2<br/><br/>
Mindig hamis értéket ad vissza. Ha van "false" nevű oszlop, használja a függvény szintaxisát (false ()), * ``(10 + 20 > 30) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kiszűri a tömb azon elemeit, amelyek nem felelnek meg a megadott predikátumnak. A szűrő a predikátum függvény egy elemére mutató hivatkozást vár #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Egy oszlopcsoport első értékének beolvasása. Ha a második paraméter ignoreNulls ki van hagyva, a rendszer hamis * ``first(sales)`` @ no__t-1 @ no__t-2 @ no__t-3 értéket feltételez.<br/><br/>
A legnagyobb egész számot adja vissza, amely nem nagyobb, mint a szám * ``floor(-0.1) -> -1`` @ no__t-1 @ no__t-2<br/><br/>
Az UTC értékre konvertálja az időbélyeget. Igény szerint átadhatja az időzónát "GMT", "PST", "UTC", "Amerika/Kajmán" formátumban. Alapértelmezés szerint az aktuális timezoneRefer Java SimpleDateFormat elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Összehasonlítási nagyobb operátor. Ugyanaz, mint > operátor * ``greater(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Összehasonlítás nagyobb vagy egyenlő operátorral. Ugyanaz, mint a > = operátor * ``greaterOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
A legnagyobb értéket adja vissza az értékek listájában, mivel a bemenet a null értékek kihagyása. Null értéket ad vissza, ha minden bemenet null * ``greatest(10, 30, 15, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Az oszlop értékének ellenőrzése a streamben név szerint. A második argumentumként átadhat egy opcionális stream-nevet.  A tervezési időszakban ismert oszlopnevek csak a nevük alapján kezelhetők. A számított bemenetek nem támogatottak, de használhat paraméteres helyettesítéseket * ``hasColumn('parent')`` @ no__t-1 @ no__t-2<br/><br/>
Egy időbélyeg óra értékének beolvasása. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Az órák számának időtartama (ezredmásodpercben) * ``hours(2) -> 7200000L`` @ no__t-1 @ no__t-2<br/><br/>
A tömb minden elemét egy új elemre képezi le a megadott kifejezés használatával. A Térkép a kifejezés függvény egy elemére mutató hivatkozást vár #itemként, és az elem indexére mutató hivatkozást #index * ``iMap([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]`` @ no__t-1 @ no__t-2<br/><br/>
Egy feltétel alapján egy vagy több érték is érvényes. Ha más nincs meghatározva, akkor NULL értékűnek számít. Mindkét értéknek kompatibilisnek kell lennie (numerikus, karakterlánc...) * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Ellenőrzi, hogy az érték nem NULL, és visszaadja-e a másodlagos értéket. Teszteli az összes bemenetet, amíg meg nem találja az első nem null értéket * ``iifNull(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Ellenőrzi, hogy egy elem szerepel-e a tömbben * ``in([10, 20, 30], 10) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Minden szó első betűjét nagybetűssé alakítja. A szavakat szóközzel elválasztva azonosítjuk * ``initCap('cool iceCREAM') -> 'Cool Icecream'`` @ no__t-1 @ no__t-2<br/><br/>
Megkeresi a karakterláncban található alsztring pozícióját (1 alapján). a 0 értéket adja vissza, ha nem található * ``instr('dumbo', 'mbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Ellenőrzi, hogy a sor törlésre van-e megjelölve. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1 * ``isDelete()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ellenőrzi, hogy a sor hibásként van-e megjelölve. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1 * ``isError()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ellenőrzi, hogy a rendszer figyelmen kívül hagyja-e a sort. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1 * ``isIgnore()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ellenőrzi, hogy a sor be van-e jelölve a beszúráshoz. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1 * ``isInsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ellenőrzi, hogy a sor egyezik-e a kereséssel. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1 * ``isMatch()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ellenőrzi, hogy az érték NULL * ``isNull(NULL()) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ellenőrzi, hogy a sor frissítésre van-e megjelölve. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1 * ``isUpdate()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ellenőrzi, hogy a sor be van-e jelölve a beszúráshoz. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-index alapértelmezett értéke 1 * ``isUpsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Egy oszlop csúcsosságát beolvasása * ``kurtosis(sales)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján lekéri egy oszlop csúcsosságát * ``kurtosisIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Az első paraméter értékének beolvasása n sor az aktuális sor előtt. A második paraméter a visszakeresett sorok száma, az alapértelmezett érték pedig 1. Ha nincs annyi sor, a rendszer null értéket ad vissza, kivéve, ha meg van adva egy alapértelmezett érték * ``lag(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Egy oszlopcsoport utolsó értékének beolvasása. Ha a második paraméter ignoreNulls ki van hagyva, a rendszer hamis * ``last(sales)`` @ no__t-1 @ no__t-2 @ no__t-3 értéket feltételez.<br/><br/>
A hónap utolsó napját kérdezi le * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')`` @ no__t-1 @ no__t-2<br/><br/>
Az első paraméter értékének beolvasása az aktuális sor után n sorban. A második paraméter a megtekinteni kívánt sorok száma, az alapértelmezett érték pedig 1. Ha nincs annyi sor, a rendszer null értéket ad vissza, kivéve, ha meg van adva egy alapértelmezett érték * ``lead(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Összehasonlítás kisebb vagy egyenlő operátorral. Ugyanaz, mint a < = operátor * ``least(10, 30, 15, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Egy alsztring kibontása az 1. indexnél a karakterek számával. Ugyanaz, mint az alsztring (Str, 1, n) * ``left('bojjus', 2) -> 'bo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
A karakterlánc hosszát adja vissza * ``length('dumbo') -> 5`` @ no__t-1 @ no__t-2<br/><br/>
Összehasonlítás nélküli operátor. Ugyanaz, mint < operátor * ``lesser(12, 24) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Összehasonlítás kisebb vagy egyenlő operátorral. Ugyanaz, mint a < = operátor * ``lesserOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Lekéri a levenshtein távolságot két karakterlánc között * ``levenshtein('boys', 'girls') -> 4`` @ no__t-1 @ no__t-2<br/><br/>
A minta egy, a szó szoros egyezésű karakterlánc. A kivételek a következő speciális szimbólumok: _ a bemenet bármelyik karakterének felel meg (ehhez hasonlóan). a POSIX reguláris kifejezésekben a (z)% a bemenet nulla vagy több karakterének felel meg (a következőhöz hasonlóan: POSIX reguláris kifejezés).
Az escape-karakter a következő: "". Ha egy escape-karakter egy speciális szimbólum vagy egy másik escape-karakter előtt következik be, a következő karakter egyeztetése szó szerint történik. A többi karakter Escape-értéke érvénytelen.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Megkeresi az alsztring pozícióját (1) egy adott pozíciót indító karakterláncon belül. Ha a pozíció kimarad, a karakterlánc elejétől számít. a 0 értéket adja vissza, ha nem található * ``locate('mbo', 'dumbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Kiszámítja a napló értékét. Egy opcionális alap nem adható meg egy Euler-szám, ha * ``log(100, 10) -> 2`` @ no__t-1 @ no__t-2<br/><br/>
Kiszámítja a log értéket 10 alap * ``log10(100) -> 2`` @ no__t-1 @ no__t-2 alapján<br/><br/>
Kisbetűs karakterlánc * ``lower('GunChus') -> 'gunchus'`` @ no__t-1 @ no__t-2<br/><br/>
A bal oldali Kitöltés után a karakterláncot a megadott kitöltéssel kell ellátni, amíg az adott hossz nem lesz. Ha a karakterlánc egyenlő vagy nagyobb, mint a hossz, akkor a hossz * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` @ no__t-1 @ no__t-2 "" lpad ("Dumbo", 8, "< >")-> "< > <dumbo'``
___
### <code>ltrim @ no__t-4 @ no__t-5<br/><br/>
A bal oldali levágja a kezdő karakterek sztringjét. Ha a második paraméter nem lett megadva, a szóközöket vágja le. Más esetben a második paraméterben megadott karaktereket vágja * ``ltrim('  dumbo  ') -> 'dumbo  '`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
A tömb minden elemét egy új elemre képezi le a megadott kifejezés használatával. A Térkép a kifejezés függvény egy elemére mutató hivatkozást vár #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Egy oszlop maximális értékének beolvasása * ``max(sales)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján egy oszlop maximális értékét kapja meg * ``maxIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Kiszámítja a változó primitív adattípusú oszlopok MD5-kivonatát, és egy 32 karakteres hexadecimális karakterláncot ad vissza. Felhasználható egy sor ujjlenyomatának kiszámítására * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'`` @ no__t-1 @ no__t-2<br/><br/>
Egy oszlop értékeinek középértékét kapja meg. Ugyanaz, mint az AVG * ``mean(sales)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján egy oszlop értékeinek középértékét kapja meg. Ugyanaz, mint a avgIf * ``meanIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Lekéri egy dátum ezredmásodperc értékét. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871`` @ no__t-2 @ no__t-3<br/><br/>
Időtartam ezredmásodpercben * ``seconds(2) -> 2L`` @ no__t-1 @ no__t-2<br/><br/>
Lekéri egy oszlop minimális értékét * ``min(sales)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján lekéri egy oszlop minimális értékét * ``minIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Számok kivonása. Kivonás a napok számától számítva. Kivonni időtartama egy időbélyegből. A kivonni két időbélyeget kap, hogy a különbség ezredmásodpercben legyen. Ugyanaz, mint a-Operator * ``minus(20, 10) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Egy időbélyeg perces értékének beolvasása. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
A percek számának időtartama (ezredmásodpercben) * ``minutes(2) -> 120000L`` @ no__t-1 @ no__t-2<br/><br/>
Pár számjegyből álló modulus. Ugyanaz, mint a (z)% operátor * ``mod(20, 8) -> 4`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Lekéri egy dátum vagy időbélyeg hónapjának értékét * ``month(toDate('2012-8-8')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
A két dátum közötti hónapok számának beolvasása. Kikapcsolhatja a számítást. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677`` @ no__t-2 @ no__t-3<br/><br/>
A számok összeszorzása. Ugyanaz, mint a * operátor * ``multiply(20, 10) -> 200`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
A NTile függvény az egyes ablakos partíciók sorait a `n` gyűjtőre osztja, 1-től egészen a legtöbb `n` értékig. A gyűjtő értéke legfeljebb 1 lesz. Ha a partícióban lévő sorok száma nem egyenletesen oszlik meg a gyűjtők számával, akkor a fennmaradó értékeket a rendszer egy gyűjtőre osztja szét, az első gyűjtőtől kezdve. A NTile függvény a tertiles, a quartiles, a deciles és más gyakori összesítő statisztikák kiszámításához hasznos. A függvény két változót számít ki az inicializálás során: a normál gyűjtő méretének egy további sora lesz hozzáadva. Mindkét változó az aktuális partíció méretétől függ. A számítási folyamat során a függvény nyomon követi az aktuális sorszámot, az aktuális gyűjtő számát, valamint azt a sorszámot, amelynél a gyűjtő módosul (bucketThreshold). Ha az aktuális sor száma eléri a gyűjtő küszöbértékét, a rendszer eggyel növeli a gyűjtő értékét, a küszöbértéket pedig a gyűjtő mérete növeli (plusz egy extra, ha az aktuális gyűjtő betömött).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Egy számot tagad. A pozitív számok negatívra váltása, és fordítva * ``negate(13) -> -13`` @ no__t-1 @ no__t-2<br/><br/>
A következő egyedi sorozatot adja vissza. A szám egymást követő, csak egy partíción belül van, és a partitionId előtaggal van ellátva * ``nextSequence() == 12313112 -> false`` @ no__t-1 @ no__t-2<br/><br/>
A karakterlánc értékének normalizálása az ékezetes Unicode-karakterek elkülönítéséhez * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'`` @ no__t-1 @ no__t-2<br/><br/>
Logikai tagadási operátor * ``not(true) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Az összehasonlítás nem egyenlő az operátorral. Ugyanaz, mint! = operátor * ``12 != 24 -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
NULL értéket ad vissza. Ha van "NULL" nevű oszlop, használja a Function szintaxist (null ()). A által használt összes művelet NULL * ``isNull('dumbo' + null) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 értéket eredményez.<br/><br/>
Logikai vagy operátor. Ugyanaz, mint | | * ``or(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Pár szám pozitív modulusa.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
A bemeneti sor aktuális partíciójának azonosítóját adja vissza * ``partitionId()`` @ no__t-1 @ no__t-2<br/><br/>
Egy szám kiemelése egy másik * ``power(10, 2) -> 100`` @ no__t-1 @ no__t-2<br/><br/>
Kiszámítja egy érték rangsorát az értékek egy csoportjában. Az eredmény az a sor, amely a partíció sorrendje szerint az aktuális sorral megegyező vagy annál korábbi sorok számát jelöli. Az értékek a sorozatban mutatkozó hézagokat eredményezik. A Rank akkor is működik, ha az adatok nem rendezettek, és az értékek változását keresi * ``rank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Egy tömb elemeinek felhalmozódása. A csökkentés egy gyűjtőre és egy elemre mutató hivatkozást vár az első kifejezésben #acc és #itemként, és az eredményül kapott értéket a második kifejezés függvényében használandó #resultként * ``reduce([1, 2, 3, 4], 0, #acc + #item, #result) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Egyező alsztring kinyerése egy adott regex-mintához. Az utolsó paraméter azonosítja az egyeztetési csoportot, és alapértelmezés szerint 1, ha nincs megadva. A "<regex>" (vissza idézőjel) használatával egy karakterláncot kell megegyeznie a * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'`` @ no__t-2 @ no__t-3 @ no__t-4 használata nélkül.<br/><br/>
Ellenőrzi, hogy a karakterlánc megfelel-e a megadott regex-mintának. A "<regex>" (vissza idézőjel) használatával egy karakterláncot kell megegyeznie a * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true`` @ no__t-2 @ no__t-3 @ no__t-4 használata nélkül.<br/><br/>
Egy regex-minta összes előfordulásának lecserélése az adott karakterláncban található másik alkarakterlánccal a "<regex>" (vissza idézőjel) értékkel egyező karakterláncot használ, és nem kell megegyeznie * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Egy karakterlánc felosztása a regexen alapuló határolójel alapján, és a karakterláncok tömbjét adja vissza * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Egy alsztring összes előfordulását cserélje le egy másik, az adott karakterláncban szereplő alsztringre. Ha az utolsó paraméter ki van hagyva, az alapértelmezett érték üres karakterlánc * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Karakterlánc megfordítása * ``reverse('gunchus') -> 'suhcnug'`` @ no__t-1 @ no__t-2<br/><br/>
Kibont egy alsztringet a jobb oldali karakterek számával. Ugyanaz, mint az alsztring (Str, LENGTH (Str)-n, n) * ``right('bojjus', 2) -> 'us'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ellenőrzi, hogy a karakterlánc megegyezik-e a megadott regex-mintával * ``rlike('200.50', `(\d+).(\d+)`) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Egy számot kerekít egy opcionális skálán és egy opcionális kerekítési módra. Ha a skála nincs megadva, a rendszer alapértelmezés szerint 0 értéket ad meg.  Ha a mód nincs megadva, a rendszer alapértelmezés szerint a ROUND_HALF_UP (5) értéket adja meg. A kerekítési értékek közé tartozik az 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Szekvenciális sorszámozást rendel egy ablakban lévő sorokhoz 1 * ``rowNumber()`` @ no__t-1 @ no__t-2<br/><br/>
A jobb gombbal a karakterláncot a megadott kitöltés alapján kell kiadni, amíg az adott hosszúságú nem lesz. Ha a karakterlánc egyenlő vagy nagyobb, mint a hossz, akkor azt a hossz * ``rpad('dumbo', 10, '-') -> 'dumbo-----'`` @ no__t-1 @ no__t-2 @ no__t-3rtrim @ no__t-4 @ no__t-5 értékre vágja.<br/><br/>
A jobb oldali levágja a kezdő karakterek egy sztringjét. Ha a második paraméter nem lett megadva, a szóközöket vágja le. Más esetben a második paraméterben megadott karaktereket vágja * ``rtrim('  dumbo  ') -> '  dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Egy dátum második értékét kéri le. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59`` @ no__t-2 @ no__t-3<br/><br/>
Időtartam ezredmásodpercben a másodpercek száma * ``seconds(2) -> 2000L`` @ no__t-1 @ no__t-2<br/><br/>
Kiszámítja a változó primitív adattípusok oszlopának SHA-1 kivonatát, és egy 40 karakteres hexadecimális karakterláncot ad vissza. Felhasználható egy sor ujjlenyomatának kiszámítására * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'`` @ no__t-1 @ no__t-2<br/><br/>
Kiszámítja a változó primitív adattípusú oszlop SHA-2 kivonatát, amely egy kis hosszúságú, ami csak 0 (256), 224, 256, 384, 512 érték lehet. Felhasználható egy sor ujjlenyomatának kiszámítására * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'`` @ no__t-1 @ no__t-2<br/><br/>
Szinuszos értéket számít ki * ``sin(2) -> 0.9092974268256817`` @ no__t-1 @ no__t-2<br/><br/>
Kiszámítja a szinusz hiperbolikus értékét * ``sinh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Egy oszlop torzításának beolvasása * ``skewness(sales)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján lekéri egy oszlop torzítását * ``skewnessIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Egy tömb részhalmazának kibontása egy pozícióból. A pozíció 1 alapú. Ha a hossz nincs megadva, az alapértelmezett érték a karakterlánc végére * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Rendezi a tömböt a megadott predikátum függvény használatával. A rendezés két egymást követő elemre mutató hivatkozást vár a kifejezés függvényben #item1 és #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Lekéri a karakterlánc Soundex kódját * ``soundex('genius') -> 'G520'`` @ no__t-1 @ no__t-2<br/><br/>
A sztringet egy határolójel alapján osztja el, és a karakterláncok tömbjét adja vissza * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8<br/><br/>
Kiszámítja egy szám négyzetének gyökerét * ``sqrt(9) -> 3`` @ no__t-1 @ no__t-2<br/><br/>
Ellenőrzi, hogy a karakterlánc a megadott karakterlánccal kezdődik-e * ``startsWith('dumbo', 'du') -> true`` @ no__t-1 @ no__t-2<br/><br/>
Egy oszlop szórásának beolvasása * ``stdDev(sales)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján lekéri egy oszlop szórását * ``stddevIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Egy oszlop populációs szórásának beolvasása * ``stddevPopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján lekéri egy oszlop sokasági szórását * ``stddevPopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Egy oszlop minta szórásának beolvasása * ``stddevSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján a * ``stddevSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2 oszlop mintájának szórását kapja meg.<br/><br/>
Hónapok kivonása egy dátumból vagy időbélyegből. Ugyanaz, mint az-operátor a Date * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')`` @ no__t-1 @ no__t-2<br/><br/>
Hónapok kivonása egy dátumból vagy időbélyegből * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')`` @ no__t-1 @ no__t-2<br/><br/>
Egy adott hosszúságú karakterlánc kibontása egy adott pozícióból. A pozíció 1 alapú. Ha a hossz nincs megadva, az alapértelmezett érték a karakterlánc végére * ``substring('Cat in the hat', 5, 2) -> 'in'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Egy numerikus oszlop összesített összegének beolvasása * ``sum(col)`` @ no__t-1 @ no__t-2<br/><br/>
Lekérdezi egy numerikus oszlop különböző értékeinek összesített összegét * ``sumDistinct(col)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján lekéri egy numerikus oszlop összesített összegét. A feltétel bármely oszlopon alapulhat * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
A feltételek alapján lekéri egy numerikus oszlop összesített összegét. A feltétel bármely oszlopon alapulhat * ``sumIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kiszámítja a tangens értékét * ``tan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Egy hiperbolikus tangens értékét számítja ki * ``tanh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Bármilyen numerikus/dátum/timestamp/sztring konvertálása bináris ábrázolásra * ``toBinary(3) -> [0x11]`` @ no__t-1 @ no__t-2<br/><br/>
A ("", "true", "y", "yes", "1") értéket True értékre ("f", "false", "n", "No", "0") konvertálja hamis értékre, és NULL értéket minden más értékhez * ``toBoolean('true') -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
A bemeneti dátum karakterláncának dátumra konvertálása egy opcionális bemeneti dátumformátum használatával. Tekintse át a Java SimpleDateFormat az elérhető formátumokhoz. Ha a bemeneti dátum formátuma nincs megadva, az alapértelmezett formátum: ÉÉÉÉ-[M] M-[d] d. Az elfogadott formátumok a következők: [éééé, éééé-[M] M, éééé-[M] M-[d] d, éééé-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Bármilyen numerikus vagy sztringet decimális értékké alakít. Ha a pontosság és a skála nincs megadva, a rendszer alapértelmezés szerint a következőt adja meg: (10, 2). Az átalakításhoz választható Java decimális formátumot is használhat. Egy opcionális területi beállítás formátuma BCP47 nyelven, például en-US, de, zh-CN * ``toDecimal(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Bármely numerikus vagy sztringet dupla értékre konvertál. Az átalakításhoz választható Java decimális formátumot is használhat. Egy opcionális területi beállítás formátuma BCP47 nyelven, például en-US, de, zh-CN * ``toDouble(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Bármely numerikus vagy sztringet lebegőpontos értékké alakít. Az átalakításhoz választható Java decimális formátumot is használhat. Bármely Double @no__t csonkítása – 0 @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Bármilyen numerikus vagy sztringet egész értékre konvertál. Az átalakításhoz választható Java decimális formátumot is használhat. Levágja a hosszú, lebegőpontos, dupla * ``toInteger(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Tetszőleges numerikus vagy sztringet alakít át hosszú értékre. Az átalakításhoz választható Java decimális formátumot is használhat. Bármilyen lebegőpontos, dupla * ``toLong(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Bármilyen numerikus vagy sztringet alakít át egy rövid értékre. Az átalakításhoz választható Java decimális formátumot is használhat. Bármilyen egész szám, hosszú, float, Double * ``toShort(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Egy primitív adattípust karakterlánccá alakít át. A számok és a dátum formátuma megadható. Ha nincs megadva, a rendszer alapértelmezés szerint ki van választva. A Java decimális formátum a számok esetében használatos. Tekintse meg a Java SimpleDateFormat az összes lehetséges dátumformátumot; az alapértelmezett formátum: éééé-hh-dd * ``toString(10) -> '10'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Egy karakterláncot egy időbélyeg-formátumra konvertál, és nem kötelező időbélyeg-formátumot. Tekintse meg a Java SimpleDateFormat az összes lehetséges formátumot. Ha az időbélyeg ki van hagyva az alapértelmezett mintának. éééé-[M] M-[d] d óó: PP: SS [. f...] használatos. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. Az időbélyeg támogatja a legfeljebb ezredmásodperces pontosságot a 999Refer Java SimpleDateFormat az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Az időbélyeget UTC értékre alakítja. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. Alapértelmezés szerint az aktuális timezoneRefer Java SimpleDateFormat elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Egy karakterkészletet cseréljen fel egy másik karakterkészletre a karakterláncban. A karakterek 1 és 1 közötti helyettesítéssel rendelkeznek * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Egy kezdő és záró karakterből álló karakterláncot metsz. Ha a második paraméter nem lett megadva, a szóközöket vágja le. Más esetben a második paraméterben megadott karaktereket vágja * ``trim('  dumbo  ') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Mindig igaz értéket ad vissza. Használja a Function szintaxist (true ()), ha van egy "true" nevű oszlop, * ``(10 + 20 == 30) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Az oszlop típusának felel meg. Csak minta kifejezésekben használható. a szám megfelel a rövid, az egész, a hosszú, a dupla, az float vagy a decimális, az integrált egyezések rövid, egész, hosszú, töredékes egyezések dupla, lebegőpontos, decimális és DateTime egyezési dátum vagy időbélyeg típusa * ``typeMatch(type, 'number')`` @ no__t-1 @ no__t-2 @ no__ t-3<br/><br/>
Nagybetűs karakterlánc * ``upper('bojjus') -> 'BOJJUS'`` @ no__t-1 @ no__t-2<br/><br/>
A generált UUID * ``uuid()`` @ no__t-1 @ no__t-2 értéket adja vissza.<br/><br/>
Egy oszlop eltérésének beolvasása * ``variance(sales)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján lekéri egy oszlop eltérését * ``varianceIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Lekéri egy oszlop populációjának eltérését * ``variancePopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján lekéri egy oszlop populációjának eltérését * ``variancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Egy oszlop elfogulatlan variancia beolvasása * ``varianceSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
A feltételek alapján beolvassa egy oszlop elfogulatlan eltérését * ``varianceSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Az év hetének beolvasása dátummal * ``weekOfYear(toDate('2008-02-20')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
A hetek számának időtartama (ezredmásodpercben) * ``weeks(2) -> 1209600000L`` @ no__t-1 @ no__t-2<br/><br/>
Logikai XOR operátor. Ugyanaz, mint a ^ operátor * ``xor(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
A dátum év értékének beolvasása @no__t – 0

## <a name="next-steps"></a>Következő lépések

[Ismerje meg a Expression Builder használatát](concepts-data-flow-expression-builder.md).
