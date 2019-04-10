---
title: Az adatvezérelt stílusa kifejezés az Azure Maps Web SDK-ban |} A Microsoft Docs
description: Az adatvezérelt stílus kifejezések használata az Azure Maps Web SDK hogyan.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 0f63ed7d00f1ae3e30cdac76606559a4e9f49f04
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288096"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Az adatvezérelt stílusa kifejezés (Web SDK-t)

Kifejezések lehetővé teszik, hogy az üzleti logika alkalmazása, vizsgálja meg a tulajdonságait az adatforrás minden alakzathoz meghatározott stílusának beállítások. Kifejezések is használható egy adatforrás vagy réteg adatok szűréséhez. Kifejezések állhat, feltételes logikát, például ha-utasítások, és a; adatok kezelésére is használható karakterlánc, logikai és matematikai operátorokat. 

Az adatvezérelt stílusok csökkenti a kód szükséges üzleti logikát stílusának körül. A rétegek használatakor kifejezések egy önálló szálon, amely a felhasználói felület szálán üzleti logika kiértékelése képest nagyobb teljesítményt biztosít a renderelési időben értékeli ki.

Az alábbi videó az Azure Maps Web SDK-ban az adatvezérelt stílusának áttekintést nyújt.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Kifejezések JSON-tömbök helyettesítik. A kifejezés a tömb első eleme egy karakterláncérték, amely meghatározza a kifejezés operátor neve. Például "+" vagy "kis". A következő elemek (ha vannak) az argumentumok a kifejezést. Minden argumentum egy konstans értéket (egy karakterlánc, szám, logikai értéket, vagy `null`), vagy egy másik kifejezés tömbben. A következő pseudocode alapszintű struktúrát egy kifejezést határozza meg. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Az Azure Maps Web SDK támogatja a számos különböző típusú kifejezés, amely saját vagy egyéb kifejezések együtt is használhatók.

| A kifejezések típusa | Leírás |
|---------------------|-------------|
| [Logikai kifejezések](#boolean-expressions) | Logikai kifejezésen logikai operátorokkal kifejezések kiértékelése logikai összehasonlítások tárházát biztosítja. |
| [Színe kifejezés](#color-expressions) | Szín kifejezés könnyebben hozhat létre és kezelheti a színértékek. |
| [Feltételes kifejezések](#conditional-expressions) | A feltételes kifejezések biztosít logic, ha-utasítások hasonlóak. |
| [Adatok kifejezések](#data-expressions) | Egy szolgáltatás tulajdonság adataihoz hozzáférést biztosít. |
| [Interpolálja és kifejezések. lépés:](#interpolate-and-step-expressions) | Interpolálja és lépés kifejezések mentén interpolált görbe vagy lépés függvény értékek kiszámításához használható. |
| [Réteg megadott kifejezések](#layer-specific-expressions) | Speciális kifejezések, amelyek csak egy rétegben alkalmazható. |
| [Matematikai kifejezések](#math-expressions) | Kifejezés keretében adatokon alapuló számítások végrehajtásához matematikai operátorokat tartalmaz. |
| [Karakterlánc operátor kifejezése](#string-operator-expressions) | Karakterlánc operátor kifejezések átalakítási műveletek végrehajtása a karakterláncok például kiszámításával és átalakítás az eset. |
| [Típus kifejezés](#type-expressions) | Típus kifejezés biztosítanak eszközöket teszteléshez és a különböző adattípusok – például a karakterláncokat, számok és logikai értékek konvertálása. |
| [Változókötés kifejezések](#variable-binding-expressions) | Változókötés kifejezések lehetővé teszik a számítások eredményét egy változóban tárolni, és a máshol kifejezésben hivatkozott egy többször számítsa ki újra a tárolt érték nélkül. |
| [Nagyítás kifejezése](#zoom-expression) | Renderelési időben kérdezi le a térkép jelenlegi nagyítási szintjét. |

Ebben a dokumentumban szereplő összes példák a következő szolgáltatás segítségével különböző módszereket mutatnak be, hogy a különböző típusú kifejezéseket is használható. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {     
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Adatok kifejezések

Adatok kifejezések funkció tulajdonság adataihoz hozzáférést biztosítanak. 

| Kifejezés | Visszatérési típusa | Leírás |
|------------|-------------|-------------|
| `['at', number, array]` | objektum | Lekérdezi egy elemet egy tömb. |
| `['geometry-type']` | sztring | Lekérdezi a funkció geometriai típusa: Pont, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | érték | A tulajdonság értéke az aktuális szolgáltatás tulajdonságainak beolvasása. Ha hiányzik a kért tulajdonsága null értéket ad vissza. |
| `['get', string, object]` | érték | A tulajdonság értéke a megadott objektum tulajdonságait olvassa be. Ha hiányzik a kért tulajdonsága null értéket ad vissza. |
| `['has', string]` | logikai | Meghatározza, hogy egy szolgáltatás tulajdonságainak van-e a megadott tulajdonságot. |
| `['has', string, object]` | logikai | Meghatározza, hogy az objektum tulajdonságainak van-e a megadott tulajdonságot. |
| `['id']` | érték | A Funkcióazonosító lekérdezi, ha rendelkezik ilyennel. |
| `['length', string | array]` | szám | A tömb vagy karakterlánc hosszának beolvasása. |

**Példák**

Egy szolgáltatás tulajdonságainak elérhetők közvetlenül egy kifejezés használatával egy `get` kifejezés. Az alábbi példa egy buborék réteg színe tulajdonság megadására a szolgáltatás "zoneColor" értékét használja. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

A fenti példában megfelelően fog működni, ha minden pont szolgáltatás rendelkezik a `zoneColor` tulajdonságot, ha nem, de a szín lesz valószínűleg térhet vissza "fekete". A tartalék szín módosítása egy `case` kifejezés használható együtt a `has` kifejezés, ellenőrizze, hogy a tulajdonság létezik, és ha ehelyett, nem visszaadja egy tartalék színt.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Buborékdiagram és szimbólum rétegek alapértelmezés szerint egy adatforrásban alakzatokhoz koordinátáit jelennek meg. Ezt megteheti egy sokszög- vagy vonaldiagramon az csúcspont kiemeléséhez. A `filter` a réteg is a beállítással korlátozhatja a funkciókat a vártak geometry típusú egy `['geometry-type']` kifejezés belül egy logikai kifejezés. Az alábbi példa korlátozza, hogy csak egy buborék réteg `Point` funkciók vannak leképezve.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

Az alábbi példában is lehetővé teszi `Point` és `MultiPoint` funkciókat a megjelenítésre. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Ehhez hasonlóan a Vázlat sokszögek sor rétegekben jelenik meg. Tiltsa le ezt a viselkedést egy vonalréteg, adjon hozzá egy szűrőt, amely csak lehetővé teszi, hogy `LineString` és `MultiLineString` funkciókat.  

## <a name="math-expressions"></a>Matematikai kifejezések

Matematikai kifejezések matematikai operátorokat kifejezés keretében adatokon alapuló számítások végrehajtásához adja meg.

| Kifejezés | Visszatérési típusa | Leírás |
|------------|-------------|-------------|
| `['+', number, number, …]` | szám | A megadott számok összegét számítja ki. |
| `['-', number]` | szám | Kivonja a megadott szám szerint 0. |
| `['-', number, number]` | szám | A második szám szerint az első számok időpontból. |
| `['*', number, number, …]` | szám | A megadott számok szorozza meg együtt. |
| `['/', number, number]` | szám | Az első szám, a második szám alapján osztja fel. |
| `['%', number, number]` | szám | Kiszámítja a fennmaradó, a második szám szerint az első szám való osztás esetén. |
| `['^', number, number]` | szám | Az első olyan értéket, a teljesítmény, a második szám megadott hatványra emelt értékét számítja ki. |
| `['abs', number]` | szám | A megadott szám abszolút értékét számítja ki. |
| `['acos', number]` | szám | A megadott szám arkusz koszinuszát számítja ki. |
| `['asin', number]` | szám | A megadott szám arkusz számítja ki. |
| `['atan', number]` | szám | A megadott szám arkusz tangensét számítja ki. |
| `['ceil', number]` | szám | A szám a következő egész egész számra kerekít. |
| `['cos', number]` | szám | A megadott szám cos számítja ki. |
| `['e']` | szám | Visszaadja a matematikai állandó `e`. |
| `['floor', number]` | szám | A szám egész az előző egész számra kerekít. |
| `['ln', number]` | szám | A megadott szám természetes alapú logaritmusát számítja ki. |
| `['ln2']` | szám | Visszaadja a matematikai állandó `ln(2)`. |
| `['log10', number]` | szám | A megadott szám logaritmusát base-tíz számítja ki. |
| `['log2', number]` | szám | A megadott szám logaritmusát alapja a második kiszámítja. |
| `['max', number, number, …]` | szám | Kiszámítja a szám a megadott készletben a maximális számát. |
| `['min', number, number, …]` | szám | Kiszámítja a megadott készlet számok minimális száma. |
| `['pi']` | szám | Visszaadja a matematikai állandó `PI`. |
| `['round', number]` | szám | A számot a legközelebbi egész számra kerekít. Felénél értékek távolodó kerekíti. Ha például `['round', -1.5]` -2 értékelődik ki. |
| `['sin', number]` | szám | A megadott szám szinuszát számítja ki. |
| `['sqrt', number]` | szám | Kiszámítja a megadott szám négyzetgyökét. |
| `['tan', number]` | szám | A megadott szám tangensét számítja ki. |
## <a name="boolean-expressions"></a>Logikai kifejezések

Logikai kifejezésen logikai operátorokkal kifejezések kiértékelése logikai összehasonlítások tárházát biztosítja.

Értékek összehasonlítása, az összehasonlítás szigorúan írta be. Különböző típusú értékek mindig minősülnek nem egyenlő. Esetekben, ahol a típusok ismert parse időpontban eltérő lehet érvénytelen tekinti, és a egy elemzési hiba állítja elő. 

| Kifejezés | Visszatérési típusa | Leírás |
|------------|-------------|-------------|
| `['! ', boolean]` | logikai | Logikai negálás. Értéket ad vissza `true` Ha a bemenet `false`, és `false` Ha a bemenet `true`. |
| `['!= ', value, value]` | logikai | Értéket ad vissza `true` Ha bemeneti érték nem egyenlő, `false` más módon. |
| `['<', value, value]` | logikai | Értéket ad vissza `true` esetén az első bemeneti szigorúan kevesebb, mint a második `false` más módon. Az argumentumok kell lennie, vagy a karakterláncok vagy mindkét számot. |
| `['<=', value, value]` | logikai | Értéket ad vissza `true` kisebb vagy egyenlő, mint a második, az első bemenet esetén `false` más módon. Az argumentumok kell lennie, vagy a karakterláncok vagy mindkét számot. |
| `['==', value, value]` | logikai | Értéket ad vissza `true` esetén, ha a bemeneti értékek egyeznek, `false` más módon. Az argumentumok kell lennie, vagy a karakterláncok vagy mindkét számot. |
| `['>', value, value]` | logikai | Értéket ad vissza `true` Ha az első bemeneti szigorúan nagyobb, mint a második `false` más módon. Az argumentumok kell lennie, vagy a karakterláncok vagy mindkét számot. |
| `['>=' value, value]` | logikai | Értéket ad vissza `true` első bemeneti esetén nagyobb vagy egyenlő a második `false` más módon. Az argumentumok kell lennie, vagy a karakterláncok vagy mindkét számot. |
| `['all', boolean, boolean, …]` | logikai | Értéket ad vissza `true` ha összes bemeneti `true`, `false` más módon. |
| `['any', boolean, boolean, …]` | logikai | Értéket ad vissza `true` Ha bármelyik bemenetei `true`, `false` más módon. |

## <a name="conditional-expressions"></a>Feltételes kifejezések

A feltételes kifejezések biztosít logic, ha-utasítások hasonlóak.

Az alábbi kifejezések feltételes logikát műveletek végrehajtása a bemeneti adatait. Ha például a `case` kifejezés "if/majd/else" logikai közben biztosít a `match` kifejezés olyan, mintha egy "switch utasítás". 

### <a name="case-expression"></a>Case kifejezés

A `case` kifejezést is egy feltételt, amely if utasítás például logikai elemeket (Ha/majd/else) biztosít. Az ilyen típusú kifejezés végigvezeti a logikai feltételek listáját, és az első logikai feltétel "igaz" kimeneti értékét adja vissza.

A következő pseudocode struktúráját határozza meg a `case` kifejezés. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Példa**

Az alábbi példa végigvezeti különböző logikai feltételeket, amíg nem talál egy visszaadó `true`, és majd a kapcsolódó érték visszaadása. Ha nincs logikai feltétel `true`, egy tartalék értékét adja vissza. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Keresési kifejezést

A `match` kifejezés egy olyan feltételt, például a logikai kapcsoló utasítás biztosító típusú. A bemeneti mint minden kifejezés is lehet `['get', 'entityType']` , amely a szám vagy egy karakterláncot ad vissza. Minden címke egy konstans érték vagy egy olyan értéktömböt literal, amelynek értékeit kell lennie az összes karakterláncokat vagy csak számokat kell lennie. A bemeneti illeszkedik, ha a tömb találat az értékeket. Minden címkének egyedinek kell lennie. Ha a bemeneti típus nem felel meg a feliratok a típusát, az eredmény lesz tartalék értékét.

A következő pseudocode struktúráját határozza meg a `match` kifejezés. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Példák**

Az alábbi példa ellenőrzi a `entityType` egy buborék réteg pont szolgáltatása tulajdonsága egyezést keres. Ha egyezést talál, a megadott értéket ad vissza, vagy a tartalék értéket adja vissza.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

Az alábbi példában egy tömb listában címkék, amelyek az összes visszaküldje-e ugyanaz az érték egy készletét. Ez a sokkal hatékonyabb, mint minden címke listázása külön-külön. Ebben az esetben ha a `entityType` tulajdonság értéke "éttermi" vagy "grocery_store", a "red" színt adja vissza.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>Coalesce kifejezés

A `coalesce` kifejezés kifejezések készletével lépések, amíg az első nem üres érték kapunk, és ezt az értéket adja vissza. 

A következő pseudocode struktúráját határozza meg a ` coalesce` kifejezés. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Példa**

Az alábbi példában egy `coalesce` kifejezés beállítása a `textField` szimbólum réteg lehetőséget. Ha a `title` tulajdonság hiányzik a funkció vagy a set `null`, a kifejezés eztuán megpróbálja keres a `subtitle` tulajdonságot, ha a hiányzó vagy `null`, azt fogja majd visszatér egy üres karakterlánc. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

## <a name="type-expressions"></a>Típus kifejezés

Típus kifejezés biztosítanak eszközöket teszteléshez és a különböző adattípusok – például a karakterláncokat, számok és logikai értékek konvertálása.

| Kifejezés | Visszatérési típusa | Leírás |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | tömb \| objektum | Szövegkonstansérték tömböt vagy objektumot ad vissza. Ez a kifejezés használatával megakadályozza, hogy egy tömböt vagy objektumot, egy kifejezés kiértékelése megtörténik. Erre akkor szükség, amikor egy tömböt vagy objektumot kell egy kifejezés által visszaadott. |
| `['to-boolean', value]` | logikai | A bemeneti érték konvertálása logikai érték beolvasása. Az eredmény `false` amikor a bemeneti érték egy üres karakterlánc, `0`, `false`, `null`, vagy `NaN`; ellenkező esetben a `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | szín | A bemeneti érték konvertálása egy színt. Ha több érték van megadva, egyenként értékeli ahhoz, amíg a az első sikeres átalakítás. A bemenetek egyike sem alakítható, ha a kifejezés nem megfelelő. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | szám | Ha lehetséges alakítja a bemeneti érték egy szám. Ha a bemenet `null` vagy `false`, az eredmény: 0. Ha a bemenet `true`, az eredmény: 1. Ha a bemeneti karakterláncot, akkor egy számot a alakítja át a [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) karakterláncot az ECMAScript nyelvi specifikáció függvény. Ha több érték van megadva, egyenként értékeli ahhoz, amíg a az első sikeres átalakítás. A bemenetek egyike sem alakítható, ha a kifejezés nem megfelelő. |
| `['to-string', value]` | sztring | A bemeneti érték konvertálása egy karakterláncot. Ha a bemenet `null`, az eredmény `""`. Ha a bemenet egy logikai érték, az eredmény van `"true"` vagy `"false"`. Ha a bemenet több, azt egy karakterlánc használatával alakítja át a [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) az ECMAScript nyelvi specifikáció függvény számot. Ha a bemeneti színt, azt konvertálja CSS RGBA-szín karakterláncnak `"rgba(r,g,b,a)"`. Ellenkező esetben a bemeneti alakítja át egy karakterlánc használata a [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) a ECMAScript nyelvi specifikáció funkcióját. |
| `['typeof', value]` | sztring | Egy karakterlánc, amely a megadott érték típusát adja vissza. |

> [!TIP]
> Ha egy ehhez hasonló hibaüzenetet `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` jelenik meg a böngészőbeli konzolon ez azt jelenti, hogy nincs-e valahol a kódban, amely rendelkezik egy tömb, amely nem rendelkezik az első értékét egy karakterláncot egy kifejezés. Ha azt szeretné, hogy a kifejezés egy tömböt adnak vissza, a tömb a sortörés a `literal` kifejezés. Az alábbi példa beállítja az ikon `offset` szimbólum réteg, amely kell lennie a két számot tartalmazó tömb beállítás egy `match` két eltolási értéket választhat kifejezés értéke alapján a `entityType` pont tulajdonság a szolgáltatás.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If there is no title, try getting the subtitle. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Színe kifejezés

Szín kifejezés könnyebben hozhat létre és kezelheti a színértékek.

| Kifejezés | Visszatérési típusa | Leírás |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | szín | Létrehoz egy színértéket származó *piros*, *zöld*, és *kék* összetevők közötti tartományban kell `0` és `255`, és a egy azalfaösszetevő`1`. Ha valamelyik összetevő az engedélyezett tartományon kívül esik, a kifejezés nem megfelelő. |
| `['rgba', number, number, number, number]` | szín | Létrehoz egy színértéket származó *piros*, *zöld*, *kék* összetevők közötti tartományban kell `0` és `255`, és a egy tartományon belül az alfa összetevő `0` és `1`. Ha valamelyik összetevő az engedélyezett tartományon kívül esik, a kifejezés nem megfelelő. |
| `['to-rgba']` | \[number, number, number, number\] | Egy négy-elem tömböt tartalmazó a bemeneti színt ad vissza, *piros*, *zöld*, *kék*, és *alpha* összetevők, ebben a sorrendben. |

**Példa**

A következő példában létrehozunk és szín RGB, amely rendelkezik egy *piros* értékét `255`, és *zöld* és *kék* értékek szorzata `2.5` értékével a `temperature` tulajdonság. A hőmérséklet-változás különböző árnyalatait változik a színt, *piros*.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Karakterlánc operátor kifejezése

Karakterlánc operátor kifejezések átalakítási műveletek végrehajtása a karakterláncok például kiszámításával és átalakítás az eset. 

| Kifejezés | Visszatérési típusa | Leírás |
|------------|-------------|-------------|
| `['concat', string, string, …]` | sztring | Több karakterláncokat fűz össze a együtt. Minden egyes értéknek karakterláncnak kell lennie. Használja a `to-string` kifejezés egyéb értéktípusok alakítandó karakterlánc, szükség esetén írja be. |
| `['downcase', string]` | sztring | A megadott karakterlánc kisbetűvé alakítja. |
| `['upcase', string]` | sztring | A megadott karakterlánc nagybetűssé alakít át. |

**Példa**

Az alábbi példa alakítja át a `temperature` pont tulajdonság egy karakterláncban funkciót, és majd a "° F" a végéhez fűzi össze.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

A fenti kifejezést egy PIN-kód a következő szöveggel: "64° F" felett az alábbi képen látható módon átfedésben a térképen megjelenítve.

<center>

![Operátor példában a karakterlánc-kifejezés](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Interpolálja és kifejezések. lépés:

Interpolálja és lépés kifejezések mentén interpolált görbe vagy lépés függvény értékek kiszámításához használható. Ezek a kifejezések vesz egy kifejezés, amely egy numerikus értéket adja vissza a bemeneti például `['get',  'temperature']`. A bemeneti érték párok, bemeneti és kimeneti értékek, az úgynevezett "nem", az érték, amely legjobban megfelel az interpolált görbe vagy lépés függvény meghatározásához képest értékeli ki. Minden egyes állítsa le a bemeneti érték egy szám lehet, és növekvő sorrendben kell. A kimeneti értékeket kell lennie, egy szám és számból álló tömböt, vagy egy színt.

### <a name="interpolate-expression"></a>Interpolálja kifejezés

Egy `interpolate` stop érték közötti közbeszúrva alapján számítja ki az értékek egy halmazát folyamatos, zökkenőmentes kifejezés használható. Egy `interpolate` színértékek visszaadó hoz létre a kiválasztott értékek melyik eredményben szín átmenetekhez.

Három típusú interpolációs módszerek használhatók egy `interpolate` kifejezés:
 
* `['linear']` -Fedettségét a költségráfordításokkal egyenes arányban leállítja a két között.
* `['exponential', base]` -Fedettségét a exponenciálisan között a leáll. A `base` érték azt határozza meg, ahol a kimeneti növeli a sebességét. A magasabb értékek győződjön meg a kimeneti növeli a tartomány magas vége felé. A `base` 1-hez közeli érték, amely több költségráfordításokkal egyenes arányban növekszik kimenet eredményez.
* `['cubic-bezier', x1, y1, x2, y2]` -Fedettségét a használatával egy [m3 Bezier-görbe](https://developer.mozilla.org/docs/Web/CSS/timing-function) határozzák meg a megadott ellenőrző pontok.

Íme egy példa a különböző típusú interpolations kinézni. 

| Lineáris  | Exponenciális | Kijelző Bezier |
|---------|-------------|--------------|
| ![Lineáris interpolációs graph](media/how-to-expressions/linear-interpolation.png) | ![Exponenciális interpolációs graph](media/how-to-expressions/exponential-interpolation.png) | ![Bezier-interpolációs m3 graph](media/how-to-expressions/bezier-curve-interpolation.png) |

A következő pseudocode struktúráját határozza meg a `interpolate` kifejezés. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Példa**

Az alábbi példában egy `linear interpolate` kifejezés beállítása a `color` tulajdonság egy buborék réteg alapján a `temperature` pont szolgáltatásának tulajdonság. Ha a `temperature` értéke kisebb, mint 60, "blue" vissza kell adni, ha a 60-as és kisebb, mint 70, közötti sárga vissza kell adni, ha 70 és 80, között "narancs" vissza kell adni, ha a 80-s vagy újabb, "red" vissza kell adni.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Az alábbi képen láthatja, hogyan a színeket a fenti kifejezés közül választ.
 
<center>

![Interpolálja példában a kifejezés](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Lépés kifejezés

A `step` kifejezés kiértékelése diszkrét, lépcsőzetes eredmény érték kiszámításához használható egy [piecewise állandó függvény](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) határozzák meg leáll. 

A következő pseudocode struktúráját határozza meg a `step` kifejezés. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Lépés kifejezések a leállítás előtt a bemeneti érték, vagy ha a bemenet első bemeneti érték kisebb, mint az első lépésként kimeneti értékét adja vissza. 

**Példa**

Az alábbi példában egy `step` kifejezés beállítása a `color` tulajdonság egy buborék réteg alapján a `temperature` pont szolgáltatásának tulajdonság. Ha a `temperature` értéke kisebb, mint 60, "blue" vissza kell adni, ha a 60-as és kisebb, mint 70, között "sárga" vissza kell adni, ha 70 és 80, között "narancs" vissza kell adni, ha a 80-s vagy újabb, "red" vissza kell adni.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Az alábbi képen láthatja, hogyan a színeket a fenti kifejezés közül választ.
 
<center>

![Lépés példában a kifejezés](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Réteg megadott kifejezések

Speciális kifejezések, amelyek csak az adott réteg vonatkoznak.

### <a name="heat-map-density-expression"></a>HEAT map sűrűségű kifejezés

Egy heat map sűrűségű kifejezés minden egyes képpont heat map rétegben heat map sűrűségű értékét kérdezi le, és típusúként van definiálva `['heatmap-density']`. Közötti szám értéke `0` és `1` együtt használatos, és egy `interpolation` vagy `step` meghatározásához a szín színátmenetes hőtérkép színezése használt kifejezés. Ez a kifejezés csak akkor használható a a [beállítás szín](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) a heat map réteg.

> [!TIP]
> A 0-interpolációs kifejezés indexnél vagy egy lépés szín alapértelmezett színét határozza meg a terület színe, ha nem szerepel megjeleníthető adat és a háttérszínt egy használható. Számos igény szerint állítsa be ezt az értéket átlátható és a egy félig átlátszók fekete. 

**Példa**

Ebben a példában egy vonalhajózási interpolációs kifejezés használatával hozzon létre egy zökkenőmentes szín színátmenetes hőtérkép renderelési. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Mellett egy zökkenőmentes átmenet használatával egy hőtérkép színezése, színek adható meg a tartományok belül használatával egy `step` kifejezés. Használatával egy `step` hőtérkép színkódolása kifejezése működésképtelenné válik be, amely így több hasonlít a körvonal vagy a mérlegeli stílus térképet sűrűségű vizuálisan azokat a tartományokat.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

További információkért lásd: a [adjon hozzá egy megadott hőtérképrészlet térképréteg](map-add-heat-map-layer.md) dokumentációját.

### <a name="line-progress-expression"></a>Vonal folyamatban kifejezés

Egy sor folyamatban kifejezés kérdezi le a folyamat egy vonalréteg egy színátmenetes vonal mentén, és típusúként van definiálva `['line-progress']`. Ez az érték 0 és 1 közötti szám, és együtt kell használni egy `interpolation` vagy `step` kifejezés. Ez a kifejezés csak használható a [strokeGradient beállítás]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) a sor réteg. 

> [!NOTE]
> A `strokeGradient` a sor réteg utóbbi lehetőség megköveteli a `lineMetrics` értékűre kell beállítani a beállítás az adatforrás `true`.

**Példa**

Az alábbi példában a `['line-progress']` kifejezés egy vonal a körvonal színe színátmenet alkalmazandó.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Tekintse meg élő példát](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Szöveges mező formátum kifejezése

A szöveges formátum Mezőkifejezés használható a `textField` lehetőség a szimbólum rétegek `textOptions` tulajdonság vegyes szöveg formázása. Ez a kifejezés lehetővé teszi, hogy a bemeneti karakterláncok és a formázási beállítások megadását. Az alábbi lehetőségek a kifejezésben szereplő minden egyes bemeneti karakterlánc adható meg.

 * `'font-scale'` -Adja meg a betűméret a méretezési tényezőt. Ha meg van adva, ez az érték felülbírálja a `size` tulajdonságát a `textOptions` az egyedi karakterlánc.
 * `'text-font'` -Adja meg ezt a karakterláncot kell használni egy vagy több betűcsaládok. Ha meg van adva, ez az érték felülbírálja a `font` tulajdonságát a `textOptions` az egyedi karakterlánc.

A következő pseudocode a szöveges formátum Mezőkifejezés struktúráját határozza meg. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    …
]
```

**Példa**

Az alábbi példa a szövegmező formázza a félkövér betűtípus hozzáadásával és a betűmérete vertikális felskálázása az `title` tulajdonság a funkció. Ebben a példában is hozzáad a `subtitle` tulajdonság a egy új sor, és a méretezett funkciójának betűméret le.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 'font-scale': 0.75 }
        ]
    }
});
```

Ez a réteg a pont funkció az alábbi képen látható módon jelenik meg:
 
<center>

![Formázott szöveges mezőt pont funkció képe](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Szám formátum kifejezése

A `number-format` kifejezés csak használható együtt a `textField` szimbólum réteg lehetőséget. Ez a kifejezés olyan formázott karakterláncot alakítja át a megadott számot. Ez a kifejezés burkolja JavaScript [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) funkciót, és támogatja a következő beállításokat.

 * `locale` -Adja meg ezt a beállítást, az olyan módon, amely igazodik a megadott nyelvre a karakterláncok számok átalakításához. Adja át a [BCP-47 nyelvcímkét](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) be ezt a beállítást.
 * `currency` – Ha a szám átalakítása egy karakterlánc, amely egy pénznemet. Lehetséges értékek a következők a [ISO 4217 Pénznemkód](https://en.wikipedia.org/wiki/ISO_4217), például a "EUR" euro, vagy a kínai RMB "CNY", "USD" az Amerikai dollár.
 * `'min-fraction-digits'` – A tizedesjegyek számát, a karakterlánc verzióban minimális számát adja meg.
 * `'max-fraction-digits'` – A tizedesjegyek számát, a karakterlánc verzióban maximális számának megadása.

A következő pseudocode a szöveges formátum Mezőkifejezés struktúráját határozza meg. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Példa**

Az alábbi példában egy `number-format` kifejezés módosítása a `revenue` tulajdonság pont szolgáltatásának a jelenik meg a `textField` lehetőség egy szimbólum réteg úgy, hogy az Amerikai dollár értékű megjelenik.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Ez a réteg a pont funkció az alábbi képen látható módon jelenik meg:

<center>

![Szám formátum kifejezés példa](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Nagyítás kifejezése

A `zoom` kifejezés olvashatók be a térkép jelenlegi nagyítási szintjét a renderelési időben, és típusúként van definiálva `['zoom']`. Ez a kifejezés a térkép minimális és maximális nagyítási szint tartományán közötti számot ad vissza. Ez a kifejezés használata lehetővé teszi a stílusok módosul, a térkép nagyítási szintjét, dinamikusan módosíthatók. A `zoom` kifejezés csak akkor használható a `interpolate` és `step` kifejezéseket.

**Példa**

Alapértelmezés szerint a megadott hőtérképrészlet térképréteg jelennek meg az adatpontok sugarait rendelkeznek egy rögzített képpontos sugarat rendel minden nagyítási szint. Mivel a térképet, mert már teljes együttesen az adatok összesítések és a megadott hőtérképrészlet térképréteg megjelenése. A `zoom` kifejezés segítségével méretezheti a sugarat rendel minden egyes nagyítási szintjét úgy, hogy az egyes vonatkozik a leképezés egy fizikai területen. Ez fogja elérhetővé tenni keresse meg a megadott hőtérképrészlet térképréteg statikus, és konzisztens. Minden egyes a térkép nagyítási szintjét rendelkezik kétszer annyi képpont vertikális és horizontális skálázást végezhet a korábbi nagyítási szintjét. A radius skálázás úgy, hogy az egyes nagyítási szintjét, megduplázódik hoz létre egy, a következőhöz konzisztens az összes nagyítási szint hőtérkép. A használatával lehet elvégezni a `zoom` kifejezés egy `base 2 exponential interpolation` kifejezés alább látható módon. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[Tekintse meg élő példát](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Változókötés kifejezések

Változókötés kifejezések, hogy azt lehet rá hivatkozni máshol kifejezésben többször azt újraszámítása nélkül tárolható egy változóban számítás eredménye. Ez a kifejezések, amelyek sok számítást magában foglalja a hasznos optimalizálása

| Kifejezés | Visszatérési típusa | Leírás |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"let"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Név1: karakterlánc,<br/>&nbsp;&nbsp;&nbsp;&nbsp;érték1: bármely,<br/>&nbsp;&nbsp;&nbsp;&nbsp;Név2: karakterlánc,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: bármely,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Egy vagy több értéket által használható változók tárolja a `var` kifejezés a gyermek kifejezés, amely visszaadja az eredményt. |
| `['var', name: string]` | bármilyen | Hivatkozik, amely használatával hoztuk létre a `let` kifejezés. |

**Példa**

Ebben a példában egy kifejezés, amely kiszámítja a bevétel viszonyított hőmérséklet arány, majd használja a `case` ennek az értéknek logikai különböző műveleteket kiértékelendő kifejezés. A `let` kifejezés a bevétel viszonyított aránya hőmérséklet tárolására, úgy, hogy, csak egyszer lehet kiszámítani kell szolgál, és a `var` kifejezés gyakran nélkül újraszámítja, igény szerint hivatkozik a változót.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>További lépések

A kifejezések megvalósító további Kódminták a következő cikkekben talál:

> [!div class="nextstepaction"] 
> [Szimbólumréteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Buborékréteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [Alakzatok hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Hőtérképréteg hozzáadása](map-add-heat-map-layer.md)

További információ a réteg beállításait, amelyek támogatják a kifejezések:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
