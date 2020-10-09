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
ms.date: 10/06/2020
ms.openlocfilehash: ab7ed95ba4b9a4fa6f0bafb78ea80a2d4acac71d
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827879"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Adatátalakítási kifejezések a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="expression-functions"></a>Kifejezésfüggvények

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
Kiszámítja a koszinusz inverz értékét.  
* ``acos(1) -> 0.0``  
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Karakterláncok vagy számok párokat adja meg. A dátum és a napok számát adja hozzá. Egy időtartamot rendel egy időbélyeghez. Egy hasonló típusú tömb hozzáfűzése egy másikhoz. Ugyanaz, mint a + operátor.  
* ``add(10, 20) -> 30``  
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
Napok hozzáadása dátumhoz vagy időbélyeghez. Ugyanaz, mint a + operátor a dátumhoz.  
* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``  
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Hónapok hozzáadása dátumhoz vagy időbélyeghez. Igény szerint átadhat egy időzónát.  
* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``  
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``  
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logikai és operátor. Ugyanaz, mint a &&.  
* ``and(true, false) -> false``  
* ``true && false -> false``  
___
### <code>array</code>
<code><b>array([<i>&lt;value1&gt;</i> : any], ...) => array</b></code><br/><br/>
Elemek tömbjét hozza létre. Minden elemnek azonos típusúnak kell lennie. Ha nincs megadva elem, az alapértelmezett karakterlánc-tömb. Ugyanaz, mint a [] létrehozási operátor.  
* ``array('Seattle', 'Washington')``
* ``['Seattle', 'Washington']``
* ``['Seattle', 'Washington'][1]``
* ``'Washington'``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy inverz szinusz értékét számítja ki.  
* ``asin(0) -> 0.0``  
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy inverz tangens értékét számítja ki.  
* ``atan(0) -> 0.0``  
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
A sík pozitív x tengelye és a koordináták által megadott pont közötti szöget adja vissza radiánban.  
* ``atan2(0, 0) -> 0.0``  
___
### <code>byOrigin</code>
<code><b>byOrigin(<i>&lt;column name&gt;</i> : string, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
Oszlop értékének kiválasztása a forrás adatfolyamban név alapján. A második argumentum a forrás-adatfolyam neve. Ha több egyezés van, a rendszer az első egyezést adja vissza. Ha nincs egyezés, NULL értéket ad vissza. A visszaadott értéket a Type konverziós függvények egyikének kell átalakítania (TO_DATE, TO_STRING...). A tervezési időszakban ismert oszlopnevek csak a nevük alapján kezelhetők. A számított bemenetek nem támogatottak, de használhat paraméterekkel való helyettesítést is.  
* ``toString(byOrigin('ancestor', 'ancestorStream'))``
___
### <code>byOrigins</code>
<code><b>byOrigins(<i>&lt;column names&gt;</i> : array, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
Kijelöli az oszlopok tömbjét név szerint az adatfolyamban. A második argumentum az a stream, ahonnan származik. Ha több egyezés van, a rendszer az első egyezést adja vissza. Ha nincs egyezés, NULL értéket ad vissza. A visszaadott értéket a Type konverziós függvények egyikének kell átalakítania (TO_DATE, TO_STRING...) A tervezési időszakban ismert oszlopnevek csak a nevük alapján kezelhetők. A számított bemenetek nem támogatottak, de használhat paraméterekkel való helyettesítést is.
* ``toString(byOrigins(['ancestor1', 'ancestor2'], 'ancestorStream'))``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Oszlop értékének kiválasztása a streamben név szerint. A második argumentumként átadhat egy opcionális stream-nevet. Ha több egyezés van, a rendszer az első egyezést adja vissza. Ha nincs egyezés, NULL értéket ad vissza. A visszaadott értéket a Type konverziós függvények egyikének kell átalakítania (TO_DATE, TO_STRING...).  A tervezési időszakban ismert oszlopnevek csak a nevük alapján kezelhetők. A számított bemenetek nem támogatottak, de használhat paraméterekkel való helyettesítést is.  
* ``toString(byName('parent'))``  
* ``toLong(byName('income'))``  
* ``toBoolean(byName('foster'))``  
* ``toLong(byName($debtCol))``  
* ``toString(byName('Bogus Column'))``  
* ``toString(byName('Bogus Column', 'DeriveStream'))``  
___
### <code>byNames</code>
<code><b>byNames(<i>&lt;column names&gt;</i> : array, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Válassza ki az oszlopok tömbjét név szerint az adatfolyamban. A második argumentumként átadhat egy opcionális stream-nevet. Ha több egyezés van, a rendszer az első egyezést adja vissza. Ha egy oszlophoz nem tartoznak egyezések, a teljes kimenet NULL értékű. A visszaadott értéknek egy Type Conversion functions (toDate, toString,...) típusúnak kell lennie.  A tervezési időszakban ismert oszlopnevek csak a nevük alapján kezelhetők. A számított bemenetek nem támogatottak, de használhat paraméterekkel való helyettesítést is.
* ``toString(byNames(['parent', 'child']))``
* ``byNames(['parent']) ? string``
* ``toLong(byNames(['income']))``
* ``byNames(['income']) ? long``
* ``toBoolean(byNames(['foster']))``
* ``toLong(byNames($debtCols))``
* ``toString(byNames(['a Column']))``
* ``toString(byNames(['a Column'], 'DeriveStream'))``
* ``byNames(['orderItem']) ? (itemName as string, itemQty as integer)``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Oszlop értékének kiválasztása az adatfolyamban lévő relatív pozíció (1 alapú) alapján. Ha a pozíció kívül esik a határértékeken, NULL értéket ad vissza. A visszaadott értéket a Type konverziós függvények egyikének kell átalakítania (TO_DATE, TO_STRING...) A számított bemenetek nem támogatottak, de használhat paraméterekkel való helyettesítést is.  
* ``toString(byPosition(1))``  
* ``toDecimal(byPosition(2), 10, 2)``  
* ``toBoolean(byName(4))``  
* ``toString(byName($colName))``  
* ``toString(byPosition(1234))``  
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
A váltakozó feltételek alapján egy vagy több érték is érvényes. Ha a bemenetek száma páros, a másik alapértelmezett értéke NULL az utolsó feltételnél.  
* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``  
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``  
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``  
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy szám adatkockájának gyökerét számítja ki.  
* ``cbrt(8) -> 2.0``  
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
A legkisebb egész számot adja vissza, amely nem kisebb, mint a szám.  
* ``ceil(-0.1) -> 0``  
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Az első nem null értéket adja vissza bemenetek készletében. Minden bemenetnek azonos típusúnak kell lennie.  
* ``coalesce(10, 20) -> 10``  
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``  
___
### <code>collect</code>
<code><b>collect(<i>&lt;value1&gt;</i> : any) => array</b></code><br/><br/>
Az összesített csoportban lévő kifejezés összes értékét egy tömbbe gyűjti. A szerkezetek összegyűjthetők és átalakíthatók más struktúrákba a folyamat során. Az elemek száma egyenlő lesz a csoportban lévő sorok számával, és tartalmazhat null értékeket is. Az összegyűjtött elemek számának kicsinek kell lennie.  
* ``collect(salesPerson)``
* ``collect(firstName + lastName))``
* ``collect(@(name = salesPerson, sales = salesAmount) )``
___
### <code>columnNames</code>
<code><b>columnNames(<i>&lt;value1&gt;</i> : string) => array</b></code><br/><br/>
Egy adatfolyam összes kimeneti oszlopának beolvasása. A második argumentumként átadhat egy opcionális stream-nevet.  
* ``columnNames()``
* ``columnNames('DeriveStream')``

___
### <code>columns</code>
<code><b>columns([<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Egy adatfolyam összes kimeneti oszlopának beolvasása. A második argumentumként átadhat egy opcionális stream-nevet.   
* ``columns()``
* ``columns('DeriveStream')``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Összehasonlítja az azonos típusú két értéket. Negatív egész számot ad vissza, ha érték1 < érték2, 0, ha érték1 = = érték2, pozitív érték, ha érték1 > érték2.  
* ``(compare(12, 24) < 1) -> true``  
* ``(compare('dumbo', 'dum') > 0) -> true``  
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Változó számú karakterlánc összefűzése egymással. Ugyanaz, mint a + operátor karakterláncokkal.  
* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``  
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``  
* ``isNull('sql' + null) -> true``  
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Változó számú karakterláncot fűz össze egy elválasztóval. Az első paraméter az elválasztó.  
* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``  
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``  
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``  
___
### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
Igaz értéket ad vissza, ha a megadott tömb bármely eleme igaz értékre értékeli a megadott predikátumban. A tartalmaz egy hivatkozást a predikátum függvény egyik elemére #itemként.  
* ``contains([1, 2, 3, 4], #item == 3) -> true``  
* ``contains([1, 2, 3, 4], #item > 5) -> false``  
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kiszámítja a koszinusz értékét.  
* ``cos(10) -> -0.8390715290764524``  
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Egy érték koszinusz hiperbolikusát számítja ki.  
* ``cosh(0) -> 1.0``  
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Kiszámítja a különböző primitív adattípusok oszlopának CRC32 kivonatát egy kis hosszúságú értékkel, amely csak 0 (256), 224, 256, 384, 512 értékű lehet. Felhasználható egy sor ujjlenyomatának kiszámítására.  
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``  
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Lekérdezi az aktuális dátumot, amikor a feladatnak futnia kell. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzónát használja a rendszer alapértelmezettként. Tekintse át `SimpleDateFormat` a Java osztályát a rendelkezésre álló formátumokhoz. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). 
* ``currentDate() == toDate('2250-12-31') -> false``  
* ``currentDate('PST')  == toDate('2250-12-31') -> false``  
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``  
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Lekérdezi az aktuális időbélyeget, amikor a feladatot a helyi időzónával kezdi futtatni.  
* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``  
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Az aktuális időbélyeg beolvasása UTC-ként. Ha azt szeretné, hogy a jelenlegi idő a fürt időzónája szerint egy másik időzónában legyen értelmezve, a választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában lehet átadni. Alapértelmezés szerint az aktuális időzóna. Tekintse át `SimpleDateFormat` a Java osztályát a rendelkezésre álló formátumokhoz. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). Az UTC-idő más időzónára való konvertálása `fromUTC()` .  
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``  
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
A hónap napját adja meg.  
* ``dayOfMonth(toDate('2018-06-08')) -> 8``  
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
A hét napját adja meg egy dátummal. 1 – vasárnap, 2 – hétfő..., 7 – szombat.  
* ``dayOfWeek(toDate('2018-06-08')) -> 6``  
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Az év napját adja meg.  
* ``dayOfYear(toDate('2016-04-09')) -> 100``  
___
### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
A napok számának időtartama (ezredmásodpercben).  
* ``days(2) -> 172800000L``  
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
A radián értéket fok értékre alakítja.  
* ``degrees(3.141592653589793) -> 180``  
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Egy pár számot oszt szét. Ugyanaz, mint a `/` kezelő.  
* ``divide(20, 10) -> 2``  
* ``20 / 10 -> 2``  
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Ellenőrzi, hogy a karakterlánc a megadott karakterlánccal végződik-e.  
* ``endsWith('dumbo', 'mbo') -> true``  
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Összehasonlítás egyenlő operátor. Ugyanaz, mint = = operátor.  
* ``equals(12, 24) -> false``  
* ``12 == 24 -> false``  
* ``'bad' == 'bad' -> true``  
* ``isNull('good' == toString(null)) -> true``  
* ``isNull(null == null) -> true``  
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Az összehasonlítási művelet figyelmen kívül hagyja a kis-és nagybetűket. Ugyanaz, mint a <=> operátor.  
* ``'abc'<=>'Abc' -> true``  
* ``equalsIgnoreCase('abc', 'Abc') -> true``  
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Kiszámítja egy szám faktoriálisát.  
* ``factorial(5) -> 120``  
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Mindig hamis értéket ad vissza. Használja a függvényt `syntax(false())` , ha van "false" nevű oszlop.  
* ``(10 + 20 > 30) -> false``  
* ``(10 + 20 > 30) -> false()``
___
### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
Kiszűri a tömb azon elemeit, amelyek nem felelnek meg a megadott predikátumnak. A szűrő a predikátum függvény egy elemére mutató hivatkozást vár #itemként.  
* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``  
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``  
___
### <code>find</code>
<code><b>find(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Keresse meg az első elemet egy olyan tömbből, amely megfelel a feltételnek. Egy szűrő függvényt vesz igénybe, ahol a tömbben lévő elem #itemként kezelhető. Mélyen beágyazott térképekhez a #item_n (#item_1, #item_2...) jelöléssel hivatkozhat a szülő térképekre.  
* ``find([10, 20, 30], #item > 10) -> 20``
* ``find(['azure', 'data', 'factory'], length(#item) > 4) -> 'azure'``
* ``find([
      @(
         name = 'Daniel',
         types = [
                   @(mood = 'jovial', behavior = 'terrific'),
                   @(mood = 'grumpy', behavior = 'bad')
                 ]
        ),
      @(
         name = 'Mark',
         types = [
                   @(mood = 'happy', behavior = 'awesome'),
                   @(mood = 'calm', behavior = 'reclusive')
                 ]
        )
      ],
      contains(#item.types, #item.mood=='happy')  /*Filter out the happy kid*/
    )``
* ``
     @(
           name = 'Mark',
           types = [
                     @(mood = 'happy', behavior = 'awesome'),
                     @(mood = 'calm', behavior = 'reclusive')
                   ]
      )
    ``  
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></a><br/><br/> kód a legnagyobb egész számot adja vissza, amely nem nagyobb than the number.  
* ``floor(-0.1) -> -1``  
___
### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></Code><br/><br/> kódolja a megadott string in base64.  
* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``  
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></a><br/><br/> kód az UTC időbélyegre konvertálható. Igény szerint átadhatja az időzónát "GMT", "PST", "UTC", "Amerika/Kajmán" formátumban. Alapértelmezés szerint az aktuális timezo ne. Refer Java's `S i mpleDateFormat osztálya elérhető formátumok számára. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></Code><br/><br/> összehasonlítás nagyobb operátor. Same as > operator.  
* ``greater(12, 24) -> false``  
* ``('dumbo' > 'dum') -> true``  
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></kód><br/><br/> összehasonlítás nagyobb vagy egyenlő operátorral. Azonos as >= operator.  
* ``greaterOrEqual(12, 12) -> true``  
* ``('dumbo' >= 'dum') -> true``  
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></a><br/><br/> kód a legnagyobb értéket adja vissza az értékek listájában, mivel a bemenet a null értékek kihagyása mellett szerepel. Null értéket ad vissza, ha az összes inputs are null.  
* ``greatest(10, 30,15, 20) -> 30``  
* ``greatest(10, toInteger(null), 20) -> 20``  
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``  
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></Code><br/><br/> az oszlop értékének ellenőrzése a streamben név szerint. A második argumentumként átadhat egy opcionális stream-nevet. A tervezési időszakban ismert oszlopnevek csak a nevük alapján kezelhetők. A számított bemenetek nem támogatottak, de használhatja a parameter substitutions.  
* ``hasColumn('parent')``  
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></Code><br/><br/> lekérdezi egy időbélyeg óra értékét. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzóna az lt. Refer Java's `S i elérhető formátumok defau mpleDateFormat osztálya. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``  
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``  
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></kód><br/><br/> időtartam ezredmásodpercben number of hours.  
* ``hours(2) -> 7200000L``  
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></a következő kód><br/><br/> egy feltétel alapján egy értéket vagy a másikat alkalmazza. Ha más nincs meghatározva, akkor NULL értékűnek számít. Mindkét értéknek kompatibilisnek kell lennie (numeric, string...).  
* ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``  
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``  
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></Code><br/><br/> ellenőrzi, hogy az első paraméter null értékű-e. Ha nem null értékű, a rendszer az első paramétert adja vissza. Null érték esetén a rendszer a második paramétert adja vissza. Ha három paraméter van megadva, a viselkedés ugyanaz, mint az IIf (isNull (érték1), érték2, érték3), és a harmadik paramétert adja vissza, ha az első VA lue is not null.  
* ``iifNull (10, 20) -> 10``  
* ``iifNull(null,20, 40) -> 20``  
* ``iifNull('azure', 'data', 'factory') -> 'factory'``  
* ``iifNull(null, 'data', 'factory') -> 'data'``  
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></kód><br/><br/> ellenőrzi, hogy egy adott elemmel is in the array.  
* ``in([10, 20, 30], 10) -> true``  
* ``in(['good', 'kid'], 'bad') -> false``  
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></Code><br/><br/> minden szó első betűjét nagybetűssé alakítja. A szavakat a rendszer különállóként azonosítjad by whitespace.  
* ``initCap('cool iceCREAM') -> 'Cool Icecream'``  
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></a><br/><br/> kód megkeresi a karakterláncban található alsztring pozícióját (1 alapján). 0 visszaadvaed if not found.  
* ``instr('dumbo', 'mbo') -> 3``  
* ``instr('microsoft', 'o') -> 5``  
* ``instr('good', 'bad') -> 0``  
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></Code><br/><br/> ellenőrzi, hogy a sor törlésre van-e megjelölve. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek és a default value is 1.  
* ``isDelete()``  
* ``isDelete(1)``  
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></Code><br/><br/> ellenőrzi, hogy a sor hibásként van-e megjelölve. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek és a default value is 1.  
* ``isError()``  
* ``isError(1)``  
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></Code><br/><br/> ellenőrzi, hogy a sor jelölése figyelmen kívül hagyható-e. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek és a default value is 1.  
* ``isIgnore()``  
* ``isIgnore(1)``  
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></Code><br/><br/> ellenőrzi, hogy a sor be van-e jelölve a beszúráshoz. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek és a default value is 1.  
* ``isInsert()``  
* ``isInsert(1)``  
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></Code><br/><br/> ellenőrzi, hogy a sor egyezik-e a kereséssel. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek és a default value is 1.  
* ``isMatch()``  
* ``isMatch(1)``  
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></Code><br/><br/> ellenőrzi, hogy a the value is NULL.  
* ``isNull(NULL()) -> true``  
* ``isNull('') -> false``  
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></Code><br/><br/> ellenőrzi, hogy a sor frissítésre van-e megjelölve. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek és a default value is 1.  
* ``isUpdate()``  
* ``isUpdate(1)``  
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></Code><br/><br/> ellenőrzi, hogy a sor be van-e jelölve a beszúráshoz. Az egynél több bemeneti adatfolyamot használó átalakítások esetében az adatfolyam (1-alapú) indexe adható át. Az adatfolyam-indexnek 1 vagy 2 értékűnek és a default value is 1.  
* ``isUpsert()``  
* ``isUpsert(1)``  
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></Code><br/><br/> lekéri a Mon utolsó napjátth given a date.  
* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``  
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></Code><br/><br/> összehasonlítás kisebb vagy egyenlő operátor. Azonos as <= operator.  
* ``least(10, 30,15, 20) -> 10``  
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``  
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></a Code><br/><br/> Kinyer egy alsztringet az 1. indexnél a karakterek számával. Ugyanaz, mint a SUBStRING(str, 1, n).  
* ``left('bojjus', 2) -> 'bo'``  
* ``left('bojjus', 20) -> 'bojjus'``  
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></Code><br/><br/> visszaadja a hosszah of the string.  
* ``length('dumbo') -> 5``  
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></Code><br/><br/> összehasonlítás kevesebb operátor. Same as < operator.  
* ``lesser(12, 24) -> true``  
* ``('abcd' < 'abc') -> false``  
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></Code><br/><br/> összehasonlítás kisebb vagy egyenlő operátor. Azonos as <= operator.  
* ``lesserOrEqual(12, 12) -> true``  
* ``('dumbo' <= 'dum') -> false``  
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></Code><br/><br/> lekéri a levenshtein távolságot between two strings.  
* ``levenshtein('boys', 'girls') -> 4``  
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></Code><br/><br/> a minta egy olyan karakterlánc, amely szó szerint egyezik. A kivételek a következő speciális szimbólumok: _ a bemenet bármelyik karakterének felel meg (ehhez hasonlóan). a POSIX REGU- l ar kifejezések esetében a (z)% a bemenetben lévő nulla vagy több karakternek felel meg (a következőhöz hasonlóan: POSIX regul a r Expressions).
Az escape-karakter a következő: "". Ha egy escape-karakter egy speciális szimbólum vagy egy másik escape-karakter előtt következik be, a következő karakter egyeztetése szó szerint történik. Érvénytelen a menekülési other character.  
* ``like('icecream', 'ice%') -> true``  
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></a következő kód><br/><br/> megkeresi a karakterláncban található alsztring pozícióját (1 alapján) egy adott pozíciótól kezdődően. Ha a pozíció kimarad, a karakterlánc elejétől számít. 0 visszaadva ed if not found.  
* ``locate('mbo','dumbo') -> 3``  
* ``locate('o', 'microsoft', 6) -> 7``  
* ``locate('bad', 'good') -> 0``  
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></Code><br/><br/> kiszámítja a log értéket. Egy opcionális alap nem adható meg egy Euler number if used.  
* ``log(100, 10) -> 2``  
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> kiszámítja a b log értéketased on 10 base.  
* ``log10(100) -> 2``  
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></Code><br/><br/> Lowercases a string.  
* ``lower('GunChus') -> 'gunchus'``  
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></kód><br/><br/> bal a megadott kitöltés alapján kitölti a karakterláncot, amíg az adott hosszúságú nem lesz. Ha a karakterlánc egyenlő vagy nagyobb, mint a hossz, akkor a d to the length.  
* ``lpad('dumbo', 10, '-') - > '-----dumbo'``  
* ``lpad('dumbo', 4,  '-') > "dumb" "*" lpad ("Dumbo"). 8, '<>') -> '<><dumbo'``  ___
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></kód><br/><br/> balra levágja a kezdő karakterek sztringjét. Ha a második paraméter nem lett megadva, a szóközöket vágja le. Egyéb esetben az s-ben megadott bármilyen karaktert felvágjaecond parameter.  
* ``ltrim('  dumbo  ') -> 'dumbo  '``  
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``  
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></Code><br/><br/> leképezi a tömb minden elemét egy új elemre a megadott kifejezés használatával. A Térkép a Fu kifejezés egyik elemére mutató hivatkozást várnction as #item.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></Code><br/><br/> leképezi a tömb minden elemét egy új elemre a megadott kifejezés használatával. A Térkép a kifejezés függvény egy elemére mutató hivatkozást vár #itemként és az elemre mutató hivatkozást. index as #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></a Code><br/><br/> kiszámítja a változó primitív adattípusú oszlop MD5-kivonatát, és egy 32 karakterből álló hexadecimális karakterláncot ad vissza. Az ujj kiszámításához használhatóprint for a row.  
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``  
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></a><br/><br/> kód egy dátum ezredmásodperc értékét kapja meg. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzóna az lt. Refer Java's `S i elérhető formátumok defau mpleDateFormat osztálya. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></kód><br/><br/> időtartam ezredmásodpercben a számhoz of milliseconds.  
* ``milliseconds(2) -> 2L``  
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></Code><br/><br/> kivonja a számokat. Napok számának kivonása egy dátumból. Időtartam kivonása egy időbélyegből. Két időbélyeg kivonása, hogy a különbség ezredmásodpercben legyen. Ugyanaz, mint the - operator.  
* ``minus(20, 10) -> 10``  
* ``20 - 10 -> 10``  
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``  
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``  
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></a Code><br/><br/> egy időbélyeg perces értékét kapja meg. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzóna az lt. Refer Java's `S i elérhető formátumok defau mpleDateFormat osztálya. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``  
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``  
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></kód><br/><br/> időtartama ezredmásodpercben a Nu esetébenmber of minutes.  
* ``minutes(2) -> 120000L``  
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></kód><br/><br/> Számsorozatok modulusa. Ugyanaz, mint the % operator.  
* ``mod(20, 8) -> 4``  
* ``20 % 8 -> 4``  
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></Code><br/><br/> lekéri a hónap értékét a date or timestamp.  
* ``month(toDate('2012-8-8')) -> 8``  
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></a Code><br/><br/> lekéri a két dátum közötti hónapok számát. Kikapcsolhatja a számítást. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzóna az lt. Refer Java's `S i elérhető formátumok defau mpleDateFormat osztálya. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``  
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></kód><br/><br/> szorozza meg a számokat. Ugyanaz, mint the * operator.  
* ``multiply(20, 10) -> 200``  
* ``20 * 10 -> 200``  
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></a Code><br/><br/> megtagadja a számot. A pozitív számok negatívra váltása and vice versa.  
* ``negate(13) -> -13``  
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></a><br/><br/> kód a következő egyedi sorozatot adja vissza. A szám egymást követő, csak egy partíción belül, és a következő előtaggal van ellátva: the partitionId.  
* ``nextSequence() == 12313112 -> false``  
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></Code><br/><br/> normalizálja a karakterlánc értékét az ékezetes UNI elkülönítéséhezcode characters.  
* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``  
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></Code><br/><br/> logikai negation operator.  
* ``not(true) -> false``  
* ``not(10 == 20) -> true``  
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></Code><br/><br/> összehasonlítás nem egyenlő a kezelővel. Azonos as != operator.  
* ``12!= 24 -> true``  
* ``'bojjus' != 'bo' + 'jjus' -> false``  
___
### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></Code><br/><br/> ellenőrzi, hogy a VAlue is not NULL.  
* ``notNull(NULL()) -> false``  
* ``notNull('') -> true``  
___
### <code>null</code>
<code><b>null() => null</b></a><br/><br/> kód NULL értéket ad vissza. Use the function `szintaxis (null ()) "ha van" NULL "nevű oszlop. Bármely, az r esult in a NULL.  
* ``isNull('dumbo' + null) -> true``  
* ``isNull(10 * -t használó művelet null) -> true``  
* ``isNull('') -> false``  
* ``isNull(10 + 20) -> false``  
* ``isNull(10/0) -> true``  
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></Code><br/><br/> logikai vagy Operator. Same as ||.  
* ``or(true, false) -> true``  
* ``true || false -> true``  
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></Code><br/><br/> pozitív modulusa pair of numbers.  
* ``pmod(-20, 8) -> 4``  
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></Code><br/><br/> az aktuális partíció azonosítóját adja vissza. input row is in.  
* ``partitionId()``  
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></Code><br/><br/> egy számot vet fel a p-reower of another.  
* ``power(10, 2) -> 100``  
___
### <code>random</code>
<code><b>random(<i>&lt;value1&gt;</i> : integral) => long</b></Code><br/><br/> egy véletlenszerűen kiválasztott magot ad vissza egy partíción belül. A magot rögzített értéknek kell lennie, és a partitionId és a produ együttes használatace random values  
* ``random(1) == 1 -> false``
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></a Code><br/><br/> egy tömb elemeit összesíti. A csökkentés egy gyűjtőre és egy elemre mutató hivatkozást vár az első kifejezésben #acc és #itemként, és az eredményül kapott értéket a második kifejezésben használt #resultnak számítja ki.ession function.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></Code><br/><br/> Kinyer egy egyező alsztringet egy adott regex mintához. Az utolsó paraméter azonosítja az egyeztetési csoportot, és alapértelmezett értéke 1 if omittEd. A "<regex>" (vissza idézőjel) használata a következő sztringnek megfelelően:ithout escaping.  
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``  
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``  
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></Code><br/><br/> ellenőrzi, hogy a karakterlánc megfelel-e a megadott Rege-nekx patteRN. A "<regex>" (vissza idézőjel) használata a következő sztringnek megfelelően:ithout escaping.  
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``  
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``  
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></Code><br/><br/> egy regex-minta összes előfordulását lecseréli egy másik alsztringre a GI-benven stra "<regex>" (vissza idézőjel) használata a következő sztringnek megfelelően:ithout escaping.  
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``  
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``  
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></Code><br/><br/> feldarabol egy karakterláncot a regexen alapuló határolójel alapján, és egy a értéket adja visszarray of strings.  
* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``  
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``  
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``  
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``  
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></Code><br/><br/> egy alsztring összes előfordulását lecseréli egy másik, a megadott karakterláncban szereplő alkarakterlánccal. Ha az utolsó paraméter ki van hagyva, alapértelmezés szerint to empty string.  
* ``replace('doggie dog', 'dog', 'cat') - > 'catgie cat'``  
* ``replace('doggie dog', 'dog','') -> 'gie '``  
* ``replace('doggie dog', 'dog') -> 'gie '``  
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></Code><br/><br/> reverses a string.  
* ``reverse('gunchus') -> 'suhcnug'``  
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></Code><br/><br/> Kinyer egy alsztringet a jobb oldali karakterek számával. Ugyanaz, mint az alsztring (Str, LENGTH(str) - n, n).  
* ``right('bojjus', 2) -> 'us'``  
* ``right('bojjus', 20) -> 'bojjus'``  
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></Code><br/><br/> ellenőrzi, hogy a karakterlánc megfelel-e a megadott értéknekn regex pattern.  
* ``rlike('200.50', `(\d+).(\d+)`) -> true``  
* ``rlike('bogus', `M[0-9]+.*`) -> false``  
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></a br/><br/> kód><a választható méretezést és a választható kerekítési módot adja meg. Ha a skála nincs megadva, a rendszer alapértelmezés szerint 0 értéket ad meg. Ha a mód nincs megadva, az alapértelmezett érték ROUND_HALF_UP (5). A ounding értékei, r beleértve az u 1 – ROUND_U P 2 – ROUND_DOWN 3-ROUND_CEILING 4 – ROUND_FLOOR 5 – ROUND_HALF_UP 6 – ROUND_HALF_DOWN 7 – ROUND_HALF_EVEN 8 – roUND_UNNECESSARY.  
* ``round(100.123) -> 100.0``  
* ``round(2.5, 0) -> 3.0``  
* ``round(5.3999999999999995, 2, 7) -> 5.40``  
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></kód><br/><br/> Right pad a karakterláncot a megadott kitöltés alapján, amíg az adott hosszúságú nem lesz. Ha a karakterlánc egyenlő vagy nagyobb, mint a hossz, akkor a rendszer metszid to the length.  
* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``  
* ``rpad('dumbo', 4, '-') -> 'dumb'``  
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``  ___
### <code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></a Code><br/><br/> jobbra metszi a kezdő karakterek sztringjét. Ha a második paraméter nem lett megadva, a szóközöket vágja le. Egyéb esetben az s-ben megadott bármilyen karaktert felvágjaecond parameter.  
* ``rtrim('  dumbo  ') -> '  dumbo'``  
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``  
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></a Code><br/><br/> egy dátum második értékét kapja meg. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. A helyi időzóna az lt. Refer Java's `S i elérhető formátumok defau mpleDateFormat osztálya. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``  
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></kód><br/><br/> időtartama ezredmásodpercben a Nu esetébenmber of seconds.  
* ``seconds(2) -> 2000L``  
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></Code><br/><br/> kiszámítja az SHA-1 kivonatot a különböző primitív adattípusú oszlopokból, és egy 40 karakterből álló hexadecimális karakterláncot ad vissza. Az ujj kiszámításához használhatóprint for a row.  
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``  
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></a Code><br/><br/> kiszámítja az SHA-2 kivonatot, amely változó primitív adattípusú oszlopokat használ egy kis hosszban, ami csak 0 (256), 224, 256, 384, 512 érték lehet. Az ujj kiszámításához használhatóprint for a row.  
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``  
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> Calculates a sine value.  
* ``sin(2) -> 0.9092974268256817``  
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> kiszámítja a hyperbolic sine value.  
* ``sinh(0) -> 0.0``  
___
### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></Code><br/><br/> megkeresi az a méretétrray or map type  
* ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></Code><br/><br/> kibontja egy tömb egy részhalmazát egy pozícióból. A pozíció 1 alapú. Ha a hossz nincs megadva, a rendszer alapértelmezés szerint az EN értéket adja megd of the string.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></Code><br/><br/> rendezi a tömböt a megadott predikátum függvény használatával. A rendezés két egymást követő elemre mutató hivatkozást vár a kifejezés függvényben #item1 and #item2.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``  
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></Code><br/><br/> lekéri a Soundex kódot for the string.  
* ``soundex('genius') -> 'G520'``  
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></Code><br/><br/> a karakterláncot egy határolójel alapján feldarabolja, és egy értéket ad vissza.rray of strings.  
* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``  
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``  
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> kiszámítja az r négyzetetoot of a number.  
* ``sqrt(9) -> 3``  
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></Code><br/><br/> ellenőrzi, hogy a karakterlánc a supplied string.  
* ``startsWith('dumbo', 'du') -> true``  
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></kód><br/><br/> kivonási napok egy dátumból vagy időbélyegből. Ugyanaz, mint az-operator for date.  
* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``  
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></kód><br/><br/> kivonási hónapok egy da-bólte or timestamp.  
* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``  
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></Code><br/><br/> Kinyer egy adott hosszúságú karakterláncot a pozícióból. A pozíció 1 alapú. Ha a hossz nincs megadva, a rendszer alapértelmezés szerint az EN d of the string.  
* ``substring('Cat in the hat', értéket adja meg 5, 2) -> 'in'``  
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``  
* ``substring('Cat in the hat', 5) -> 'in the hat'``  
* ``substring('Cat in the hat', 100, 100) -> ''``  
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></kód><br/><br/> számítások a tangent value.  
* ``tan(0) -> 0.0``  
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> kiszámítja a hyperbolic tangent value.  
* ``tanh(0) -> 0.0``  
___
### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></Code><br/><br/> kódolja a megadott string in base64.  
* ``toBase64('bojjus') -> 'Ym9qanVz'``  
___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></kód><br/><br/> a numerikus/dátum/időbélyeg/sztring konvertálása binárisra representation.  
* ``toBinary(3) -> [0x11]``  
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></a (z)><br/><br/> kód értéke ("", "true", "y", "yes", "1") igaz és ("f", "false", "n", "No", "0") értékre vált a False és a NULL értékre. any other value.  
* ``toBoolean('true') -> true``  
* ``toBoolean('n') -> false``  
* ``isNull(toBoolean('truthy')) -> true``  
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></a Code><br/><br/> a bemeneti dátum karakterláncát dátumra alakítja egy opcionális input Date Form at. Refer Java's `S i mpleDateFormat ' osztály használatával a rendelkezésre álló formátumokhoz. Ha a bemeneti dátum formátuma nincs megadva, az alapértelmezett formátum: ÉÉÉÉ-[M] M-[d] d. Az elfogadott formátumok a következők: [éééé, éééé-[M] M, éééé-[M] M-[d] d, YYYy-[M]M-[d]dT* ].  
* ``toDate('2012-8-18') -> toDate('2012-08-18')``  
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``  
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></a br/><br/> kód><a numerikus vagy sztringet decimális értékké alakítja. Ha a pontosság és a skála nincs megadva, a rendszer alapértelmezés szerint a következőt adja meg: (10, 2). Az átalakításhoz választható Java decimális formátumot is használhat. Egy opcionális területi beállítás formátuma BCP47, például en-US, de, zh-CN.  
* ``toDecimal(123.45) -> 123.45``  
* ``toDecimal('123.45', 8, 4) -> 123.4500``  
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``  
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``  
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></a br/><br/> kód><a numerikus vagy sztringet egy dupla értékre konvertálja. Az átalakításhoz választható Java decimális formátumot is használhat. Egy opcionális területi beállítás formátuma BCP47, például en-US, de, zh-CN.  
* ``toDouble(123.45) -> 123.45``  
* ``toDouble('123.45') -> 123.45``  
* ``toDouble('$123.45', '$###.00') -> 123.45``  
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``  
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></a br/><br/> kód><a numerikus vagy sztringet lebegőpontos értékké alakítja. Az átalakításhoz választható Java decimális formátumot is használhat. TRUNCates any double.  
* ``toFloat(123.45) -> 123.45f``  
* ``toFloat('123.45') -> 123.45f``  
* ``toFloat('$123.45', '$###.00') -> 123.45f``  
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></a><br/><br/> kód a numerikus vagy a sztringet egész értékre konvertálja. Az átalakításhoz választható Java decimális formátumot is használhat. Bármilyen hosszúságú csonkítás, float, double.  
* ``toInteger(123) -> 123``  
* ``toInteger('123') -> 123``  
* ``toInteger('$123', '$###') -> 123``  
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></a br/><br/> kód><a numerikus vagy sztringet hosszú értékre alakítja át. Az átalakításhoz választható Java decimális formátumot is használhat. Csonkolt egyy float, double.  
* ``toLong(123) -> 123``  
* ``toLong('123') -> 123``  
* ``toLong('$123', '$###') -> 123``  
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></a br/><br/> kód><a numerikus vagy sztringet egy rövid értékre alakítja át. Az átalakításhoz választható Java decimális formátumot is használhat. Bármely egész szám, hosszú érték csonkítása, float, double.  
* ``toShort(123) -> 123``  
* ``toShort('123') -> 123``  
* ``toShort('$123', '$###') -> 123``  
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></Code><br/><br/> egy primitív adattípust konvertál karakterlánccá. A számok és a dátum formátuma megadható. Ha nincs megadva, a rendszer alapértelmezés szerint ki van választva. A Java decimális formátum a számok esetében használatos. Tekintse meg a Java SimpleDateFormat az összes lehetséges dátumformátumot; az alapértelmezett t is yyyy-MM-dd.  
* ``toStri ng(10) -> '10'``  
* ``toString('engineer') -> 'engineer'``  
* ``toString(123456.789, '##,###.##') - > '123,456.79'``  
* ``toString(123.78, '000000.000') - forma > '000123.780'``  
* ``toString(12345, '##0.#####E0')-> '12.345E3'``  
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``  
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``  
* ``toString(4 == 20) -> 'false'``  
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></a><br/><br/> kód nem kötelező időbélyeg-formátumra konvertál egy karakterláncot egy időbélyegre. Ha az időbélyeg ki van hagyva, az alapértelmezett mint éééé-[M] M-[d] d óó: PP: SS [. f...] használatos. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. Az időbélyeg legfeljebb ezredmásodperces pontosságot támogat, amely 9 99. Refer Java's `S i mpleDateFormat osztályt biztosít az elérhető formátumokhoz. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``  
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></Code><br/><br/> átalakítja az időbélyeget UTC értékre. A választható időzónát "GMT", "PST", "UTC", "America/Cayman" formában adhatja át. Alapértelmezés szerint az aktuális timezo ne. Refer Java's `S i mpleDateFormat osztálya elérhető formátumok számára. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></Code><br/><br/> lecserél egy karakterkészletet egy másik karakterkészlettel a karakterláncban. A karakterek száma 1 t o 1 replacement.  
* ``translate('(bojjus)', '()', '[]')-> '[bojjus]'``  
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``  
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></a><br/><br/> kód egy kezdő és záró karakterből álló karakterláncot metsz. Ha a második paraméter nem lett megadva, a szóközöket vágja le. Egyéb esetben az s-ben megadott bármilyen karaktert felvágjaecond parameter.  
* ``trim('  dumbo  ') -> 'dumbo'``  
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``  
___
### <code>true</code>
<code><b>true() => boolean</b></a><br/><br/> kód mindig igaz értéket ad vissza. Use the function `szintaxis (true ()), ha van oszlopokmn named 'true'.  
* ``(10 + 20 == 30) -> true``  
* ``(10 + 20 == 30) -> true()``  
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></a><br/><br/> kód megegyezik az oszlop típusával. Csak minta kifejezésekben használható. a szám a rövid, az egész, a hosszú, a dupla, az úszó vagy a decimális, az integrált egyezések rövid, egész, hosszú, töredékes egyezések dupla, float, decimális és DateTime egyezések dátum vagy timestamp type.  
* ``typeMatch(type, 'number')``  
* ``typeMatch('date', 'datetime')``  
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></Code><br/><br/> Uppercases a string.  
* ``upper('bojjus') -> 'BOJJUS'``  
___
### <code>uuid</code>
<code><b>uuid() => string</b></Code><br/><br/> visszaadja a generated UUID.  
* ``uuid()``  
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></Code><br/><br/> beolvassa a ti hetetar given a date.  
* ``weekOfYear(toDate('2008-02-20')) -> 8``  
___
### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></kód><br/><br/> időtartam ezredmásodpercben number of weeks.  
* ``weeks(2) -> 1209600000L``  
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></Code><br/><br/> logikai XOR operátor. Same as ^ operator.  
* ``xor(true, false) -> true``  
* ``xor(true, true) -> false``  
* ``true ^ false -> true``  
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></Code><br/><br/> value of a date.  
* ``year(toDate('2012- 8-8')) beolvassa az év > 2012 ' ' # # Agg r Egate functions a következő függvények csak összesítés, kimutatás, kivonás és ablak esetén érhetők el.transformations.
___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></Code><br/><br/> lekéri a val átlagátues of a column.  
* ``avg(sales)``  
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></a Code><br/><br/> feltételek alapján lekéri a val átlagátues of a column.  
* ``avgIf(region == 'West', sales)``  
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></Code><br/><br/> lekéri az értékek összesített számát. Ha az opcionális oszlop (ok) meg van adva, a rendszer figyelmen kívül hagyja a NULL értéket es in the count.  
* ` `count(custId)``  
* ``count(cus tId, custName)`` .* ``count()``  
* ``count(iif(isNull(custId), 1, NULL))``  
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></Code><br/><br/> beolvassa az a különböző értékeinek összesített számát set of columns.  
* ``countDistinct(custId, custName)``  
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></a><br/><br/> a feltételek alapján lekéri az értékek összesített számát. Ha a választható oszlop meg van adva, a rendszer figyelmen kívül hagyja a NULL értéket.es in the count.  
* ``countIf(state == 'CA' && commission < 10000, name)``  
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></Code><br/><br/> lekéri a populációs szórás between two columns.  
* ``covariancePopulation(sales, profit)``  
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></kód><br/><br/> feltételek alapján, lekéri a sokaság szórását of two columns.  
* ``covariancePopulationIf(region == 'West', sales)``  
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></Code><br/><br/> beolvassa a minta szórását of two columns.  
* ``covarianceSample(sales, profit)``  
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></kód><br/><br/> feltételek alapján, beolvassa a minta-szórást of two columns.  
* ``covarianceSampleIf(region == 'West', sales, profit)``  
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></Code><br/><br/> beolvassa az oszlopcsoport első értékét. Ha a második paraméter ignoreNulls ki van hagyva, azts assumed false.  
* ``first(sales)``  
* ``first(sales, false)``  
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> lekéri a kurtosis of a column.  
* ``kurtosis(sales)``  
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></Code><br/><br/> feltételek alapján lekéri a kurtosis of a column.  
* ``kurtosisIf(region == 'West', sales)``  
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></a><br/><br/> kód beolvassa az oszlopcsoportok utolsó értékét. Ha a második paraméter ignoreNulls ki s assumed false.  
* van hagyva, azt ``last(sales)``  
* ``last(sales, false)``  
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></Code><br/><br/> lekéri a maximális VAlue of a column.  
* ``max(sales)``  
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></kód><br/><br/> feltételek alapján, a maximális VA értéket kapjalue of a column.  
* ``maxIf(region == 'West', sales)``  
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></Code><br/><br/> egy oszlopok értékeinek középértékét kapja megmn. Same as AVG.  
* ``mean(sales)``  
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></kód><br/><br/> feltételek alapján egy oszlop értékeinek középértékét kapja meg.. Same as avgIf.  
* ``meanIf(region == 'West', sales)``  
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></Code><br/><br/> lekéri a VA minimálislue of a column.  
* ``min(sales)``  
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></Code><br/><br/> feltételek alapján lekéri a VA minimálislue of a column.  
* ``minIf(region == 'West', sales)``  
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> lekéri a skewness of a column.  
* ``skewness(sales)``  
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></Code><br/><br/> feltételek alapján lekéri a skewness of a column.  
* ``skewnessIf(region == 'West', sales)``  
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> lekéri a standard Deviation of a column.  
* ``stdDev(sales)``  
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></Code><br/><br/> feltételek alapján, beolvassa a standard Deviation of a column.  
* ``stddevIf(region == 'West', sales)``  
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> lekéri a populáció standard Deviation of a column.  
* ``stddevPopulation(sales)``  
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></Code><br/><br/> feltételek alapján lekéri a standard szintű Deviation of a column.  
* ``stddevPopulationIf(region == 'West', sales)``  
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> lekéri a minta szabványos Deviation of a column.  
* ``stddevSample(sales)``  
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></Code><br/><br/> feltételek alapján lekéri a minta szabványos Deviation of a column.  
* ``stddevSampleIf(region == 'West', sales)``  
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></Code><br/><br/> lekérdezi az összesített összegét numeric column.  
* ``sum(col)``  
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></Code><br/><br/> lekérdezi az a különböző értékeinek összesített összegét numeric column.  
* ``sumDistinct(col)``  
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></a kód><br/><br/> a feltételek alapján lekéri egy numerikus oszlop összesített összegét. A feltétel alap d on any column.  
* ``sumDistinctIf(state == 'CA' && commission < lehet 10000, sales)``  
* ``sumDistinctIf(true, sales)``  
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></a kód><br/><br/> a feltételek alapján lekéri egy numerikus oszlop összesített összegét. A feltétel alap d on any column.  
* ``sumIf(state == 'CA' && commission < lehet 10000, sales)``  
* ``sumIf(true, sales)``  
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> lekéri a Variance of a column.  
* ``variance(sales)``  
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></Code><br/><br/> feltételek alapján lekéri a Variance of a column.  
* ``varianceIf(region == 'West', sales)``  
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> lekéri a populációs Variance of a column.  
* ``variancePopulation(sales)``  
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></Code><br/><br/> feltételek alapján lekéri a populáció Variance of a column.  
* ``variancePopulationIf(region == 'West', sales)``  
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></Code><br/><br/> beolvassa az elfogulatlan Variance of a column.  
* ``varianceSample(sales)``  
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></Code><br/><br/> feltételek alapján, beolvassa az elfogulatlan Varia nce of a column.  
* ``varianceSampleIf(region ==  'West' , Sales) ' ' # # W indow függvények a következő függvények csak az ablakban érhetők eltransformations.
___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></Code><br/><br/> a CumeDist függvény kiszámítja egy érték pozícióját a partícióban lévő összes értékhez viszonyítva. Ennek eredményeképpen a partíció rendezésének aktuális sorával megegyező vagy azzal egyenlő sorok száma elosztva a Windows-partíció sorainak teljes számával. A rendezésben szereplő összes döntetlen érték a következőre lesz kiértékelve:e same position.  
* ``cumeDist()``  
___
### <code>denseRank</code>
<code><b>denseRank() => integer</b></Code><br/><br/> kiszámítja egy érték rangsorát egy ablak Order by záradékában megadott értékek csoportjába. Az eredmény az a sor, amely a partíció sorrendje szerint az aktuális sorral megegyező vagy annál korábbi sorok számát jelöli. Az értékek nem hoznak létre hézagokat a sorozatban. A sűrű rangsor akkor is működik, ha az Adatrendezés nem történik meg, és a c-t keresihange in values.  
* ``denseRank()``  
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></Code><br/><br/> lekérdezi az első paraméter értékét, amely az aktuális sor előtt n sor lett kiértékelve. A második paraméter a visszakeresett sorok száma, az alapértelmezett érték pedig 1. Ha nincs annyi sor, a null értéket adja vissza, kivéve, ha egy alapértelmezett value is specified.  
* ``lag(amount, 2)``  
* ``lag(amount, 2000, 100)``  
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></Code><br/><br/> lekérdezi az első paraméter értékét, amely az aktuális sor után n sor lett kiértékelve. A második paraméter a megtekinteni kívánt sorok száma, az alapértelmezett érték pedig 1. Ha nincs annyi sor, a null értéket adja vissza, kivéve, ha egy alapértelmezett value is specified.  
* ``lead(amount, 2)``  
* ``lead(amount, 2000, 100)``  
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></Code><br/><br/> a NTile függvény az egyes Windo sorait w pa r "n" gyűjtőre osztja el az f rom 1 értéktől a legtöbb "n"-ig. A gyűjtő értéke legfeljebb 1 lesz. Ha a partícióban lévő sorok száma nem egyenletesen oszlik meg a gyűjtők számával, akkor a fennmaradó értékeket a rendszer egy gyűjtőre osztja szét, az első gyűjtőtől kezdve. A NTile függvény a tertiles, a quartiles, a deciles és más gyakori összesítő statisztikák kiszámításához hasznos. A függvény két változót számít ki az inicializálás során: a normál gyűjtő méretének egy további sora lesz hozzáadva. Mindkét változó az aktuális partíció méretétől függ. A számítási folyamat során a függvény nyomon követi az aktuális sorszámot, az aktuális gyűjtő számát, valamint azt a sorszámot, amelynél a gyűjtő módosul (bucketThreshold). Ha az aktuális sor száma eléri a gyűjtő küszöbértékét, a rendszer eggyel növeli a gyűjtő értékét, a küszöbértéket pedig a gyűjtő mérete növeli (plusz egy extra, ha az aktuális Bu cket is padded).* ``nTile()``  
* ``nTile(numOfBuckets)``  
___
### <code>rank</code>
<code><b>rank() => integer</b></Code><br/><br/> kiszámítja egy érték rangsorát egy ablak Order by záradékában megadott értékek csoportjába. Az eredmény az a sor, amely a partíció sorrendje szerint az aktuális sorral megegyező vagy annál korábbi sorok számát jelöli. Az értékek a sorozatban mutatkozó hézagokat eredményezik. A rang akkor is működik, ha az Adatrendezés nem történik meg, és nem keresi a c-thange in values.  
* ``rank()``  
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></Code><br/><br/> hozzárendeli egy szekvenciális sorszámot a sorokhoz egy ablakban starting with 1.  
* ``rowNumber()``* ``
 @(
       name = 'Mark',
       types = [
                 @(mood = 'happy', behavior = 'awesome'),
                 @(mood = 'calm', behavior = 'reclusive')
               ]
  )
``  
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Returns the largest integer not greater than the number.  
* ``floor(-0.1) -> -1``  
___
### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Encodes the given string in base64.  
* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``  
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Converts to the timestamp from UTC. You can optionally pass the timezone in the form of 'GMT', 'PST', 'UTC', 'America/Cayman'. It is defaulted to the current timezone. Refer Java's `SimpleDateFormat` class for available formats. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Comparison greater operator. Same as > operator.  
* ``greater(12, 24) -> false``  
* ``('dumbo' > 'dum') -> true``  
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Comparison greater than or equal operator. Same as >= operator.  
* ``greaterOrEqual(12, 12) -> true``  
* ``('dumbo' >= 'dum') -> true``  
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Returns the greatest value among the list of values as input skipping null values. Returns null if all inputs are null.  
* ``greatest(10, 30, 15, 20) -> 30``  
* ``greatest(10, toInteger(null), 20) -> 20``  
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``  
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Checks for a column value by name in the stream. You can pass a optional stream name as the second argument. Column names known at design time should be addressed just by their name. Computed inputs are not supported but you can use parameter substitutions.  
* ``hasColumn('parent')``  
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Gets the hour value of a timestamp. You can pass an optional timezone in the form of 'GMT', 'PST', 'UTC', 'America/Cayman'. The local timezone is used as the default. Refer Java's `SimpleDateFormat` class for available formats. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``  
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``  
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duration in milliseconds for number of hours.  
* ``hours(2) -> 7200000L``  
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Based on a condition applies one value or the other. If other is unspecified it is considered NULL. Both the values must be compatible(numeric, string...).  
* ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``  
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``  
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
Checks if the first parameter is null. If not null, the first parameter is returned. If null, the second parameter is returned. If three parameters are specified, the behavior is the same as iif(isNull(value1), value2, value3) and the third parameter is returned if the first value is not null.  
* ``iifNull(10, 20) -> 10``  
* ``iifNull(null, 20, 40) -> 20``  
* ``iifNull('azure', 'data', 'factory') -> 'factory'``  
* ``iifNull(null, 'data', 'factory') -> 'data'``  
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Checks if an item is in the array.  
* ``in([10, 20, 30], 10) -> true``  
* ``in(['good', 'kid'], 'bad') -> false``  
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Converts the first letter of every word to uppercase. Words are identified as separated by whitespace.  
* ``initCap('cool iceCREAM') -> 'Cool Icecream'``  
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Finds the position(1 based) of the substring within a string. 0 is returned if not found.  
* ``instr('dumbo', 'mbo') -> 3``  
* ``instr('microsoft', 'o') -> 5``  
* ``instr('good', 'bad') -> 0``  
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Checks if the row is marked for delete. For transformations taking more than one input stream you can pass the (1-based) index of the stream. The stream index should be either 1 or 2 and the default value is 1.  
* ``isDelete()``  
* ``isDelete(1)``  
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Checks if the row is marked as error. For transformations taking more than one input stream you can pass the (1-based) index of the stream. The stream index should be either 1 or 2 and the default value is 1.  
* ``isError()``  
* ``isError(1)``  
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Checks if the row is marked to be ignored. For transformations taking more than one input stream you can pass the (1-based) index of the stream. The stream index should be either 1 or 2 and the default value is 1.  
* ``isIgnore()``  
* ``isIgnore(1)``  
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Checks if the row is marked for insert. For transformations taking more than one input stream you can pass the (1-based) index of the stream. The stream index should be either 1 or 2 and the default value is 1.  
* ``isInsert()``  
* ``isInsert(1)``  
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Checks if the row is matched at lookup. For transformations taking more than one input stream you can pass the (1-based) index of the stream. The stream index should be either 1 or 2 and the default value is 1.  
* ``isMatch()``  
* ``isMatch(1)``  
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Checks if the value is NULL.  
* ``isNull(NULL()) -> true``  
* ``isNull('') -> false``  
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Checks if the row is marked for update. For transformations taking more than one input stream you can pass the (1-based) index of the stream. The stream index should be either 1 or 2 and the default value is 1.  
* ``isUpdate()``  
* ``isUpdate(1)``  
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Checks if the row is marked for insert. For transformations taking more than one input stream you can pass the (1-based) index of the stream. The stream index should be either 1 or 2 and the default value is 1.  
* ``isUpsert()``  
* ``isUpsert(1)``  
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Gets the last date of the month given a date.  
* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``  
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Comparison lesser than or equal operator. Same as <= operator.  
* ``least(10, 30, 15, 20) -> 10``  
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``  
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extracts a substring start at index 1 with number of characters. Same as SUBSTRING(str, 1, n).  
* ``left('bojjus', 2) -> 'bo'``  
* ``left('bojjus', 20) -> 'bojjus'``  
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Returns the length of the string.  
* ``length('dumbo') -> 5``  
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Comparison less operator. Same as < operator.  
* ``lesser(12, 24) -> true``  
* ``('abcd' < 'abc') -> false``  
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Comparison lesser than or equal operator. Same as <= operator.  
* ``lesserOrEqual(12, 12) -> true``  
* ``('dumbo' <= 'dum') -> false``  
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Gets the levenshtein distance between two strings.  
* ``levenshtein('boys', 'girls') -> 4``  
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
The pattern is a string that is matched literally. The exceptions are the following special symbols:  _ matches any one character in the input (similar to . in posix regular expressions)
  % matches zero or more characters in the input (similar to .* in posix regular expressions).
  The escape character is ''. If an escape character precedes a special symbol or another escape character, the following character is matched literally. It is invalid to escape any other character.  
* ``like('icecream', 'ice%') -> true``  
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Finds the position(1 based) of the substring within a string starting a certain position. If the position is omitted it is considered from the beginning of the string. 0 is returned if not found.  
* ``locate('mbo', 'dumbo') -> 3``  
* ``locate('o', 'microsoft', 6) -> 7``  
* ``locate('bad', 'good') -> 0``  
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Calculates log value. An optional base can be supplied else a Euler number if used.  
* ``log(100, 10) -> 2``  
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calculates log value based on 10 base.  
* ``log10(100) -> 2``  
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Lowercases a string.  
* ``lower('GunChus') -> 'gunchus'``  
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Left pads the string by the supplied padding until it is of a certain length. If the string is equal to or greater than the length, then it is trimmed to the length.  
* ``lpad('dumbo', 10, '-') -> '-----dumbo'``  
* ``lpad('dumbo', 4, '-') -> 'dumb'``  
* ``lpad('dumbo', 8, '<>') -> '<><dumbo'``  
___
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Left trims a string of leading characters. If second parameter is unspecified, it trims whitespace. Else it trims any character specified in the second parameter.  
* ``ltrim('  dumbo  ') -> 'dumbo  '``  
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``  
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Maps each element of the array to a new element using the provided expression. Map expects a reference to one element in the expression function as #item.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
Maps each element of the array to a new element using the provided expression. Map expects a reference to one element in the expression function as #item and a reference to the element index as #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calculates the MD5 digest of set of column of varying primitive datatypes and returns a 32 character hex string. It can be used to calculate a fingerprint for a row.  
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``  
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Gets the millisecond value of a date. You can pass an optional timezone in the form of 'GMT', 'PST', 'UTC', 'America/Cayman'. The local timezone is used as the default. Refer Java's `SimpleDateFormat` class for available formats. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duration in milliseconds for number of milliseconds.  
* ``milliseconds(2) -> 2L``  
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Subtracts numbers. Subtract number of days from a date. Subtract duration from a timestamp. Subtract two timestamps to get difference in milliseconds. Same as the - operator.  
* ``minus(20, 10) -> 10``  
* ``20 - 10 -> 10``  
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``  
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``  
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Gets the minute value of a timestamp. You can pass an optional timezone in the form of 'GMT', 'PST', 'UTC', 'America/Cayman'. The local timezone is used as the default. Refer Java's `SimpleDateFormat` class for available formats. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``  
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``  
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duration in milliseconds for number of minutes.  
* ``minutes(2) -> 120000L``  
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Modulus of pair of numbers. Same as the % operator.  
* ``mod(20, 8) -> 4``  
* ``20 % 8 -> 4``  
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Gets the month value of a date or timestamp.  
* ``month(toDate('2012-8-8')) -> 8``  
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
Gets the number of months between two dates. You can round off the calculation.You can pass an optional timezone in the form of 'GMT', 'PST', 'UTC', 'America/Cayman'. The local timezone is used as the default. Refer Java's `SimpleDateFormat` class for available formats. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``  
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Multiplies pair of numbers. Same as the * operator.  
* ``multiply(20, 10) -> 200``  
* ``20 * 10 -> 200``  
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Negates a number. Turns positive numbers to negative and vice versa.  
* ``negate(13) -> -13``  
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Returns the next unique sequence. The number is consecutive only within a partition and is prefixed by the partitionId.  
* ``nextSequence() == 12313112 -> false``  
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalizes the string value to separate accented unicode characters.  
* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``  
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logical negation operator.  
* ``not(true) -> false``  
* ``not(10 == 20) -> true``  
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Comparison not equals operator. Same as != operator.  
* ``12 != 24 -> true``  
* ``'bojjus' != 'bo' + 'jjus' -> false``  
___
### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Checks if the value is not NULL.  
* ``notNull(NULL()) -> false``  
* ``notNull('') -> true``  
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Returns a NULL value. Use the function `syntax(null())` if there is a column named 'null'. Any operation that uses will result in a NULL.  
* ``isNull('dumbo' + null) -> true``  
* ``isNull(10 * null) -> true``  
* ``isNull('') -> false``  
* ``isNull(10 + 20) -> false``  
* ``isNull(10/0) -> true``  
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logical OR operator. Same as ||.  
* ``or(true, false) -> true``  
* ``true || false -> true``  
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Positive Modulus of pair of numbers.  
* ``pmod(-20, 8) -> 4``  
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Returns the current partition id the input row is in.  
* ``partitionId()``  
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Raises one number to the power of another.  
* ``power(10, 2) -> 100``  
___
### <code>random</code>
<code><b>random(<i>&lt;value1&gt;</i> : integral) => long</b></code><br/><br/>
Returns a random number given an optional seed within a partition. The seed should be a fixed value and is used in conjunction with the partitionId to produce random values  
* ``random(1) == 1 -> false``
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Accumulates elements in an array. Reduce expects a reference to an accumulator and one element in the first expression function as #acc and #item and it expects the resulting value as #result to be used in the second expression function.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Extract a matching substring for a given regex pattern. The last parameter identifies the match group and is defaulted to 1 if omitted. Use `<regex>`(back quote) to match a string without escaping.  
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``  
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``  
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Checks if the string matches the given regex pattern. Use `<regex>`(back quote) to match a string without escaping.  
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``  
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``  
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Replace all occurrences of a regex pattern with another substring in the given string Use `<regex>`(back quote) to match a string without escaping.  
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``  
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``  
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Splits a string based on a delimiter based on regex and returns an array of strings.  
* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``  
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``  
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``  
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``  
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
Replace all occurrences of a substring with another substring in the given string. If the last parameter is omitted, it is default to empty string.  
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``  
* ``replace('doggie dog', 'dog', '') -> 'gie '``  
* ``replace('doggie dog', 'dog') -> 'gie '``  
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Reverses a string.  
* ``reverse('gunchus') -> 'suhcnug'``  
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extracts a substring with number of characters from the right. Same as SUBSTRING(str, LENGTH(str) - n, n).  
* ``right('bojjus', 2) -> 'us'``  
* ``right('bojjus', 20) -> 'bojjus'``  
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Checks if the string matches the given regex pattern.  
* ``rlike('200.50', `(\d+).(\d+)`) -> true``  
* ``rlike('bogus', `M[0-9]+.*`) -> false``  
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Rounds a number given an optional scale and an optional rounding mode. If the scale is omitted, it is defaulted to 0. If the mode is omitted, it is defaulted to ROUND_HALF_UP(5). The values for rounding include
1 - ROUND_UP
2 - ROUND_DOWN
3 - ROUND_CEILING
4 - ROUND_FLOOR
5 - ROUND_HALF_UP
6 - ROUND_HALF_DOWN
7 - ROUND_HALF_EVEN
8 - ROUND_UNNECESSARY.  
* ``round(100.123) -> 100.0``  
* ``round(2.5, 0) -> 3.0``  
* ``round(5.3999999999999995, 2, 7) -> 5.40``  
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Right pads the string by the supplied padding until it is of a certain length. If the string is equal to or greater than the length, then it is trimmed to the length.  
* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``  
* ``rpad('dumbo', 4, '-') -> 'dumb'``  
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``  
___
### <code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Right trims a string of leading characters. If second parameter is unspecified, it trims whitespace. Else it trims any character specified in the second parameter.  
* ``rtrim('  dumbo  ') -> '  dumbo'``  
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``  
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Gets the second value of a date. You can pass an optional timezone in the form of 'GMT', 'PST', 'UTC', 'America/Cayman'. The local timezone is used as the default. Refer Java's `SimpleDateFormat` class for available formats. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``  
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duration in milliseconds for number of seconds.  
* ``seconds(2) -> 2000L``  
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calculates the SHA-1 digest of set of column of varying primitive datatypes and returns a 40 character hex string. It can be used to calculate a fingerprint for a row.  
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``  
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Calculates the SHA-2 digest of set of column of varying primitive datatypes given a bit length which can only be of values 0(256), 224, 256, 384, 512. It can be used to calculate a fingerprint for a row.  
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``  
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calculates a sine value.  
* ``sin(2) -> 0.9092974268256817``  
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calculates a hyperbolic sine value.  
* ``sinh(0) -> 0.0``  
___
### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></code><br/><br/>
Finds the size of an array or map type  
* ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extracts a subset of an array from a position. Position is 1 based. If the length is omitted, it is defaulted to end of the string.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
Sorts the array using the provided predicate function. Sort expects a reference to two consecutive elements in the expression function as #item1 and #item2.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``  
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Gets the soundex code for the string.  
* ``soundex('genius') -> 'G520'``  
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Splits a string based on a delimiter and returns an array of strings.  
* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``  
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``  
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calculates the square root of a number.  
* ``sqrt(9) -> 3``  
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Checks if the string starts with the supplied string.  
* ``startsWith('dumbo', 'du') -> true``  
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Subtract days from a date or timestamp. Same as the - operator for date.  
* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``  
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Subtract months from a date or timestamp.  
* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``  
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extracts a substring of a certain length from a position. Position is 1 based. If the length is omitted, it is defaulted to end of the string.  
* ``substring('Cat in the hat', 5, 2) -> 'in'``  
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``  
* ``substring('Cat in the hat', 5) -> 'in the hat'``  
* ``substring('Cat in the hat', 100, 100) -> ''``  
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calculates a tangent value.  
* ``tan(0) -> 0.0``  
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calculates a hyperbolic tangent value.  
* ``tanh(0) -> 0.0``  
___
### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Encodes the given string in base64.  
* ``toBase64('bojjus') -> 'Ym9qanVz'``  
___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Converts any numeric/date/timestamp/string to binary representation.  
* ``toBinary(3) -> [0x11]``  
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Converts a value of ('t', 'true', 'y', 'yes', '1') to true and ('f', 'false', 'n', 'no', '0') to false and NULL for any other value.  
* ``toBoolean('true') -> true``  
* ``toBoolean('n') -> false``  
* ``isNull(toBoolean('truthy')) -> true``  
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Converts input date string to date using an optional input date format. Refer Java's `SimpleDateFormat` class for available formats. If the input date format is omitted, default format is yyyy-[M]M-[d]d. Accepted formats are :[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ].  
* ``toDate('2012-8-18') -> toDate('2012-08-18')``  
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``  
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Converts any numeric or string to a decimal value. If precision and scale are not specified, it is defaulted to (10,2).An optional Java decimal format can be used for the conversion. An optional locale format in the form of BCP47 language like en-US, de, zh-CN.  
* ``toDecimal(123.45) -> 123.45``  
* ``toDecimal('123.45', 8, 4) -> 123.4500``  
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``  
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``  
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Converts any numeric or string to a double value. An optional Java decimal format can be used for the conversion. An optional locale format in the form of BCP47 language like en-US, de, zh-CN.  
* ``toDouble(123.45) -> 123.45``  
* ``toDouble('123.45') -> 123.45``  
* ``toDouble('$123.45', '$###.00') -> 123.45``  
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``  
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Converts any numeric or string to a float value. An optional Java decimal format can be used for the conversion. Truncates any double.  
* ``toFloat(123.45) -> 123.45f``  
* ``toFloat('123.45') -> 123.45f``  
* ``toFloat('$123.45', '$###.00') -> 123.45f``  
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Converts any numeric or string to an integer value. An optional Java decimal format can be used for the conversion. Truncates any long, float, double.  
* ``toInteger(123) -> 123``  
* ``toInteger('123') -> 123``  
* ``toInteger('$123', '$###') -> 123``  
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Converts any numeric or string to a long value. An optional Java decimal format can be used for the conversion. Truncates any float, double.  
* ``toLong(123) -> 123``  
* ``toLong('123') -> 123``  
* ``toLong('$123', '$###') -> 123``  
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Converts any numeric or string to a short value. An optional Java decimal format can be used for the conversion. Truncates any integer, long, float, double.  
* ``toShort(123) -> 123``  
* ``toShort('123') -> 123``  
* ``toShort('$123', '$###') -> 123``  
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Converts a primitive datatype to a string. For numbers and date a format can be specified. If unspecified the system default is picked.Java decimal format is used for numbers. Refer to Java SimpleDateFormat for all possible date formats; the default format is yyyy-MM-dd.  
* ``toString(10) -> '10'``  
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
Converts a string to a timestamp given an optional timestamp format. If the timestamp is omitted the default pattern yyyy-[M]M-[d]d hh:mm:ss[.f...] is used. You can pass an optional timezone in the form of 'GMT', 'PST', 'UTC', 'America/Cayman'. Timestamp supports up to millisecond accuracy with value of 999. Refer Java's `SimpleDateFormat` class for available formats. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``  
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Converts the timestamp to UTC. You can pass an optional timezone in the form of 'GMT', 'PST', 'UTC', 'America/Cayman'. It is defaulted to the current timezone. Refer Java's `SimpleDateFormat` class for available formats. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Replace one set of characters by another set of characters in the string. Characters have 1 to 1 replacement.  
* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``  
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``  
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Trims a string of leading and trailing characters. If second parameter is unspecified, it trims whitespace. Else it trims any character specified in the second parameter.  
* ``trim('  dumbo  ') -> 'dumbo'``  
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``  
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Always returns a true value. Use the function `syntax(true())` if there is a column named 'true'.  
* ``(10 + 20 == 30) -> true``  
* ``(10 + 20 == 30) -> true()``  
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Matches the type of the column. Can only be used in pattern expressions.number matches short, integer, long, double, float or decimal, integral matches short, integer, long, fractional matches double, float, decimal and datetime matches date or timestamp type.  
* ``typeMatch(type, 'number')``  
* ``typeMatch('date', 'datetime')``  
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Uppercases a string.  
* ``upper('bojjus') -> 'BOJJUS'``  
___
### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
Returns the generated UUID.  
* ``uuid()``  
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Gets the week of the year given a date.  
* ``weekOfYear(toDate('2008-02-20')) -> 8``  
___
### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duration in milliseconds for number of weeks.  
* ``weeks(2) -> 1209600000L``  
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logical XOR operator. Same as ^ operator.  
* ``xor(true, false) -> true``  
* ``xor(true, true) -> false``  
* ``true ^ false -> true``  
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Gets the year value of a date.  
* ``year(toDate('2012-8-8')) -> 2012``  
## Aggregate functions
The following functions are only available in aggregate, pivot, unpivot, and window transformations.
___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Gets the average of values of a column.  
* ``avg(sales)``  
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Based on a criteria gets the average of values of a column.  
* ``avgIf(region == 'West', sales)``  
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Gets the aggregate count of values. If the optional column(s) is specified, it ignores NULL values in the count.  
* ``count(custId)``  
* ``count(custId, custName)``  
* ``count()``  
* ``count(iif(isNull(custId), 1, NULL))``  
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Gets the aggregate count of distinct values of a set of columns.  
* ``countDistinct(custId, custName)``  
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Based on a criteria gets the aggregate count of values. If the optional column is specified, it ignores NULL values in the count.  
* ``countIf(state == 'CA' && commission < 10000, name)``  
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Gets the population covariance between two columns.  
* ``covariancePopulation(sales, profit)``  
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Based on a criteria, gets the population covariance of two columns.  
* ``covariancePopulationIf(region == 'West', sales)``  
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Gets the sample covariance of two columns.  
* ``covarianceSample(sales, profit)``  
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Based on a criteria, gets the sample covariance of two columns.  
* ``covarianceSampleIf(region == 'West', sales, profit)``  
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Gets the first value of a column group. If the second parameter ignoreNulls is omitted, it is assumed false.  
* ``first(sales)``  
* ``first(sales, false)``  
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Gets the kurtosis of a column.  
* ``kurtosis(sales)``  
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Based on a criteria, gets the kurtosis of a column.  
* ``kurtosisIf(region == 'West', sales)``  
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Gets the last value of a column group. If the second parameter ignoreNulls is omitted, it is assumed false.  
* ``last(sales)``  
* ``last(sales, false)``  
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Gets the maximum value of a column.  
* ``max(sales)``  
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Based on a criteria, gets the maximum value of a column.  
* ``maxIf(region == 'West', sales)``  
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Gets the mean of values of a column. Same as AVG.  
* ``mean(sales)``  
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Based on a criteria gets the mean of values of a column. Same as avgIf.  
* ``meanIf(region == 'West', sales)``  
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Gets the minimum value of a column.  
* ``min(sales)``  
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Based on a criteria, gets the minimum value of a column.  
* ``minIf(region == 'West', sales)``  
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Gets the skewness of a column.  
* ``skewness(sales)``  
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Based on a criteria, gets the skewness of a column.  
* ``skewnessIf(region == 'West', sales)``  
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Gets the standard deviation of a column.  
* ``stdDev(sales)``  
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Based on a criteria, gets the standard deviation of a column.  
* ``stddevIf(region == 'West', sales)``  
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Gets the population standard deviation of a column.  
* ``stddevPopulation(sales)``  
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Based on a criteria, gets the population standard deviation of a column.  
* ``stddevPopulationIf(region == 'West', sales)``  
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Gets the sample standard deviation of a column.  
* ``stddevSample(sales)``  
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Based on a criteria, gets the sample standard deviation of a column.  
* ``stddevSampleIf(region == 'West', sales)``  
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Gets the aggregate sum of a numeric column.  
* ``sum(col)``  
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Gets the aggregate sum of distinct values of a numeric column.  
* ``sumDistinct(col)``  
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Based on criteria gets the aggregate sum of a numeric column. The condition can be based on any column.  
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``  
* ``sumDistinctIf(true, sales)``  
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Based on criteria gets the aggregate sum of a numeric column. The condition can be based on any column.  
* ``sumIf(state == 'CA' && commission < 10000, sales)``  
* ``sumIf(true, sales)``  
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Gets the variance of a column.  
* ``variance(sales)``  
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Based on a criteria, gets the variance of a column.  
* ``varianceIf(region == 'West', sales)``  
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Gets the population variance of a column.  
* ``variancePopulation(sales)``  
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Based on a criteria, gets the population variance of a column.  
* ``variancePopulationIf(region == 'West', sales)``  
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Gets the unbiased variance of a column.  
* ``varianceSample(sales)``  
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Based on a criteria, gets the unbiased variance of a column.  
* ``varianceSampleIf(region == 'West', sales)``  
## Window functions
The following functions are only available in window transformations.
___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
The CumeDist function computes the position of a value relative to all values in the partition. The result is the number of rows preceding or equal to the current row in the ordering of the partition divided by the total number of rows in the window partition. Any tie values in the  ordering will evaluate to the same position.  
* ``cumeDist()``  
___
### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
Computes the rank of a value in a group of values specified in a window's order by clause. The result is one plus the number of rows preceding or equal to the current row in the ordering of the partition. The values will not produce gaps in the sequence. Dense Rank works even when data is not sorted and looks for change in values.  
* ``denseRank()``  
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Gets the value of the first parameter evaluated n rows before the current row. The second parameter is the number of rows to look back and the default value is 1. If there are not as many rows a value of null is returned unless a default value is specified.  
* ``lag(amount, 2)``  
* ``lag(amount, 2000, 100)``  
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Gets the value of the first parameter evaluated n rows after the current row. The second parameter is the number of rows to look forward and the default value is 1. If there are not as many rows a value of null is returned unless a default value is specified.  
* ``lead(amount, 2)``  
* ``lead(amount, 2000, 100)``  
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
The NTile function divides the rows for each window partition into `n` buckets ranging from 1 to at most `n`. Bucket values will differ by at most 1. If the number of rows in the partition does not divide evenly into the number of buckets, then the remainder values are distributed one per bucket, starting with the first bucket. The NTile function is useful for the calculation of tertiles, quartiles, deciles, and other common summary statistics. The function calculates two variables during initialization: The size of a regular bucket will have one extra row added to it. Both variables are based on the size of the current partition. During the calculation process the function keeps track of the current row number, the current bucket number, and the row number at which the bucket will change (bucketThreshold). When the current row number reaches bucket threshold, the bucket value is increased by one and the threshold is increased by the bucket size (plus one extra if the current bucket is padded).  
* ``nTile()``  
* ``nTile(numOfBuckets)``  
___
### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
Computes the rank of a value in a group of values specified in a window's order by clause. The result is one plus the number of rows preceding or equal to the current row in the ordering of the partition. The values will produce gaps in the sequence. Rank works even when data is not sorted and looks for change in values.  
* ``rank()``  
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Assigns a sequential row numbering for rows in a window starting with 1.  
* ``rowNumber()``  

## <a name="next-steps"></a>Következő lépések

[Ismerje meg a Expression Builder használatát](concepts-data-flow-expression-builder.md).
