---
title: Adatalapú stíluskifejezések az Azure Maps Web SDK-ban | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan használhatja az adatközpontú stíluskifejezéseket a Microsoft Azure Maps Web SDK-ban.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d6009a655adcc26ebef31588eff2332a05f3a001
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804724"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Adatalapú stíluskifejezések (webes SDK)

A kifejezések lehetővé teszik, hogy üzleti logikát alkalmazzon azokra a formázási beállításokra, amelyek az adatforrás egyes alakzataiban meghatározott tulajdonságokat figyelik. A kifejezések szűrhetik az adatforrásban vagy rétegben lévő adatokat. A kifejezések állhatnak feltételes logikából, például if-utasításokból. És ezek segítségével adatokat lehet manipulálni a következő használatával: karakterlánc-operátorok, logikai operátorok és matematikai operátorok.

Az adatvezérelt stílusok csökkentik a stílussal kapcsolatos üzleti logika megvalósításához szükséges kód mennyiségét. Ha rétegekkel használja, a kifejezések kiértékelése rendereléskor történik egy külön szálon. Ez a funkció nagyobb teljesítményt nyújt, mint a felhasználói felület szálának üzleti logikájának kiértékelése.

Ez a videó áttekintést nyújt az Azure Maps Web SDK adatalapú stílusáról.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

A kifejezések JSON-tömbökként jelennek meg. A tömb ben szereplő kifejezés első eleme egy karakterlánc, amely megadja a kifejezés operátorának nevét. Például "+" vagy "kis- és nagybetű". A következő elemek (ha vannak) a kifejezés argumentumai. Minden argumentum vagy egy literális érték (karakterlánc, `null`szám, logikai vagy ) vagy egy másik kifejezéstömb. A következő pszeudokód határozza meg egy kifejezés alapvető szerkezetét. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Az Azure Maps Web SDK számos típusú kifejezést támogat. A kifejezések önmagukban vagy más kifejezésekkel kombinálva is használhatók.

| Kifejezések típusa | Leírás |
|---------------------|-------------|
| [Összesítő kifejezés](#aggregate-expression) | Olyan kifejezés, amely egy adathalmazon keresztül feldolgozott számítást határoz `clusterProperties` meg, `DataSource`és amely a lehetőséggel használható. |
| [Logikai kifejezések](#boolean-expressions) | A logikai kifejezések logikai operátorok kifejezéseit biztosítják a logikai összehasonlítások kiértékeléséhez. |
| [Színkifejezések](#color-expressions) | A színkifejezések megkönnyítik a színértékek létrehozását és manipulálását. |
| [Feltételes kifejezések](#conditional-expressions) | A feltételes kifejezések olyan logikai műveleteket biztosítanak, amelyek hasonlóak az if-utasításokhoz. |
| [Adatkifejezések](#data-expressions) | Hozzáférést biztosít a tulajdonságadatokhoz egy szolgáltatásban. |
| [Interpoláció és lépéskifejezések](#interpolate-and-step-expressions) | Az Interpoláció és a lépéskifejezések segítségével interpolált görbe vagy lépésfüggvény mentén lehet értékeket számítani. |
| [Rétegspecifikus kifejezések](#layer-specific-expressions) | Olyan speciális kifejezések, amelyek csak egy rétegre vonatkoznak. |
| [Matematikai kifejezések](#math-expressions) | Matematikai operátorokat biztosít az adatvezérelt számítások elvégzéséhez a kifejezéskeretrendszeren belül. |
| [Karakterlánc-operátor-kifejezések](#string-operator-expressions) | A karakterlánc-operátor-kifejezések konverziós műveleteket hajtanak végre karakterláncokon, például összefűzik és konvertálják a kis- és nagybetűket. |
| [Szövegkifejezések](#type-expressions) | A típuskifejezések különböző adattípusok, például karakterláncok, számok és logikai értékek tesztelésére és konvertálására szolgáló eszközöket biztosítanak. |
| [Változókötési kifejezések](#variable-binding-expressions) | A változókötési kifejezések egy számítás eredményét változóban tárolják, és többször hivatkoznak egy kifejezésre egy kifejezésben anélkül, hogy újra kellene számítani a tárolt értéket. |
| [Nagyítási kifejezés](#zoom-expression) | Lekéri a térkép aktuális nagyítási szintjét renderelési időpontban. |

A dokumentumban szereplő összes példa a következő funkcióval mutatja be a különböző típusú kifejezések használatának különböző módjait. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Adatkifejezések

Az adatkifejezések hozzáférést biztosítanak a tulajdonságadatokhoz egy szolgáltatásban. 

| Kifejezés | Visszatérés típusa | Leírás |
|------------|-------------|-------------|
| `['at', number, array]` | objektum | Elem beolvasása tömbből. |
| `['geometry-type']` | sztring | A szolgáltatás geometriai típusának beírása: Pont, MultiPoint, LineString, MultiLineString, Sokszög, MultiPoligon. |
| `['get', string]` | érték | Leveszi a tulajdonság értékét az aktuális szolgáltatás tulajdonságaiból. Null értéket ad vissza, ha a kért tulajdonság hiányzik. |
| `['get', string, object]` | érték | Leveszi a tulajdonság értékét a megadott objektum tulajdonságaiból. Null értéket ad vissza, ha a kért tulajdonság hiányzik. |
| `['has', string]` | logikai | Azt határozza meg, hogy egy szolgáltatás tulajdonságai rendelkeznek-e a megadott tulajdonsággal. |
| `['has', string, object]` | logikai | Azt határozza meg, hogy az objektum tulajdonságai rendelkeznek-e a megadott tulajdonsággal. |
| `['id']` | érték | Leveszi a funkció azonosítóját, ha rendelkezik ilyenekkel. |
| `['length', string | array]` | szám | Karakterlánc vagy tömb hosszát kapja. |
| `['in', boolean | string | number, array]` | logikai | Azt határozza meg, hogy létezik-e elem egy tömbben |
| `['in', substring, string]` | logikai | Azt határozza meg, hogy létezik-e karakterláncrész-e karakterláncban |

**Példák**

A szolgáltatások tulajdonságai kifejezés használatával közvetlenül érhetők `get` el egy kifejezésben. Ez a példa a szolgáltatás "zoneColor" értékét használja a buborékréteg színtulajdonságának megadásához. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

A fenti példa jól fog működni, ha `zoneColor` az összes pont jellemző rendelkezik a tulajdonsággal. Ha nem, a szín valószínűleg visszaesik a "fekete". A tartalék szín módosításához `case` használjon kifejezést `has` a kifejezéssel együtt annak ellenőrzésére, hogy a tulajdonság létezik-e. Ha a tulajdonság nem létezik, adja vissza a tartalék színt.

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

A buborék- és szimbólumrétegek alapértelmezés szerint az adatforrás összes alakzatának koordinátáit jelenítik meg. Ez a viselkedés kiemelheti a sokszög vagy vonal csúcsait. A `filter` réteg beállításával korlátozhatja a renderelt jellemzők geometriatípusát egy `['geometry-type']` logikai kifejezésen belüli kifejezés használatával. A következő példa egy buborékréteget korlátoz, így csak `Point` a funkciók jelennek meg.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

A következő példa `Point` `MultiPoint` lehetővé teszi a funkciók megjelenítését. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Hasonlóképpen a sokszögek körvonala is vonalrétegekben jelenik meg. Ha le szeretné tiltani ezt a viselkedést egy `LineString` `MultiLineString` vonalrétegben, adjon hozzá egy szűrőt, amely csak a szolgáltatásokat engedélyezi és tartalmazza.  

## <a name="math-expressions"></a>Matematikai kifejezések

A matematikai kifejezések matematikai operátorokat biztosítanak az adatalapú számítások elvégzéséhez a kifejezéskeretrendszeren belül.

| Kifejezés | Visszatérés típusa | Leírás |
|------------|-------------|-------------|
| `['+', number, number, …]` | szám | Kiszámítja a megadott számok összegét. |
| `['-', number]` | szám | A 0-t kivonja a megadott számmal. |
| `['-', number, number]` | szám | Az első számokat a második számmal vonja ki. |
| `['*', number, number, …]` | szám | A megadott számok összeszorzása. |
| `['/', number, number]` | szám | Az első számot elosztja a második számmal. |
| `['%', number, number]` | szám | Kiszámítja a maradékot, amikor az első számot elosztja a második számmal. |
| `['^', number, number]` | szám | Kiszámítja a második szám hatásához emelt első érték értékét. |
| `['abs', number]` | szám | Kiszámítja a megadott szám abszolút értékét. |
| `['acos', number]` | szám | Kiszámítja a megadott szám arkkoszinuszát. |
| `['asin', number]` | szám | Kiszámítja a megadott szám arkuszszinját. |
| `['atan', number]` | szám | Kiszámítja a megadott szám arkérintő-ját. |
| `['ceil', number]` | szám | A szám felfelé kerekítése a következő egész egész számra. |
| `['cos', number]` | szám | Kiszámítja a megadott szám okait. |
| `['e']` | szám | A matematikai `e`állandót adja eredményül. |
| `['floor', number]` | szám | A számot lefelé kerekíti az előző egész egész számra. |
| `['ln', number]` | szám | Kiszámítja a megadott szám természetes logaritmusát. |
| `['ln2']` | szám | A matematikai `ln(2)`állandót adja eredményül. |
| `['log10', number]` | szám | Kiszámítja a megadott szám alaptíz logaritmusát. |
| `['log2', number]` | szám | Kiszámítja a megadott szám két alapú logaritmusát. |
| `['max', number, number, …]` | szám | Kiszámítja a megadott számhalmaz maximális számát. |
| `['min', number, number, …]` | szám | Kiszámítja a megadott számhalmaz minimális számát. |
| `['pi']` | szám | A matematikai `PI`állandót adja eredményül. |
| `['round', number]` | szám | A számot a legközelebbi egész számra kerekíti. A félidős értékeket a függvény nulláról kerekíti. Például `['round', -1.5]` a kiértékelés -2. |
| `['sin', number]` | szám | Kiszámítja a megadott szám szinuszát. |
| `['sqrt', number]` | szám | Kiszámítja a megadott szám négyzetgyökét. |
| `['tan', number]` | szám | Kiszámítja a megadott szám érintőit. |

## <a name="aggregate-expression"></a>Összesítő kifejezés

Az összesített kifejezés olyan számítást határoz meg, amelyet egy adathalmazon keresztül dolgoznak fel, és amely a `clusterProperties` lehetőséggel `DataSource`használható. Ezeknek a kifejezéseknek a kimenetének számnak vagy logikai értéknek kell lennie. 

Az összesített kifejezés három értéket vesz fel: egy operátorértéket és egy kezdeti értéket, valamint egy olyan kifejezést, amely lekéri a tulajdonságot az adatok minden jellemzőjéből az összesített művelet alkalmazásához. Ez a kifejezés formátuma a következő:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operátor: Olyan kifejezésfüggvény, amelyet ezután a fürt `mapExpression` egyes pontjai által kiszámított összes értékre alkalmazanak. Támogatott operátorok: 
    - A számok `+` `*`esetében: , , `max`,`min`
    - Logikai okok `all`esetében: ,`any`
- initialValue: Olyan kezdeti érték, amelyhez az első számított érték összesítésre kerül.
- mapExpression: Az adatkészlet minden pontjára alkalmazott kifejezés.

**Példák**

Ha egy adatkészlet összes szolgáltatása rendelkezik `revenue` tulajdonsággal, amely egy szám. Ezután a fürt összes, az adatkészletből létrehozott pontjainak teljes bevétele kiszámítható. Ez a számítás a következő összesítő kifejezéssel történik:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Logikai kifejezések

A logikai kifejezések logikai operátorok kifejezéseit biztosítják a logikai összehasonlítások kiértékeléséhez.

Az értékek összehasonlításakor az összehasonlítás szigorúan be van gépelve. A különböző típusú értékek mindig egyenlőtlennek minősülnek. Azok az esetek, amelyekben a típusok elemzéskor eltérőek, érvénytelennek minősülnek, és elemzési hibát eredményeznek. 

| Kifejezés | Visszatérés típusa | Leírás |
|------------|-------------|-------------|
| `['! ', boolean]` | logikai | Logikus kitagonzálás. Akkor `true` adja vissza, `false` ha a `true`bemenet , `false`és ha a bemenet . |
| `['!= ', value, value]` | logikai | Akkor `true` adja vissza, ha `false` a bemeneti értékek nem egyenlőek, ellenkező esetben. |
| `['<', value, value]` | logikai | Akkor `true` adja vissza, ha az első `false` bemenet szigorúan kisebb, mint a második, ellenkező esetben. Az argumentumok nak mindkét karakterláncnak vagy mindkét számnak kell lenniük. |
| `['<=', value, value]` | logikai | Akkor `true` adja vissza, ha az első bemenet `false` kisebb vagy egyenlő a másodikkal, ellenkező esetben. Az argumentumok nak mindkét karakterláncnak vagy mindkét számnak kell lenniük. |
| `['==', value, value]` | logikai | Akkor `true` adja vissza, ha `false` a bemeneti értékek egyenlőek, ellenkező esetben. Az argumentumok nak mindkét karakterláncnak vagy mindkét számnak kell lenniük. |
| `['>', value, value]` | logikai | Akkor `true` adja vissza, ha az első `false` bemenet szigorúan nagyobb, mint a második, ellenkező esetben. Az argumentumok nak mindkét karakterláncnak vagy mindkét számnak kell lenniük. |
| `['>=' value, value]` | logikai | Akkor `true` adja vissza, ha az első bemenet `false` nagyobb vagy egyenlő a másodikkal, ellenkező esetben. Az argumentumok nak mindkét karakterláncnak vagy mindkét számnak kell lenniük. |
| `['all', boolean, boolean, …]` | logikai | Akkor `true` adja vissza a `true` `false` függvényt, ha az összes bemenet egyébként. |
| `['any', boolean, boolean, …]` | logikai | Akkor `true` adja vissza, ha `true` `false` a bemenetek bármelyike egyébként. |

## <a name="conditional-expressions"></a>Feltételes kifejezések

A feltételes kifejezések olyan logikai műveleteket biztosítanak, amelyek hasonlóak az if-utasításokhoz.

A következő kifejezések feltételes logikai műveleteket hajtanak végre a bemeneti adatokon. A `case` kifejezés például "if/then/else" logikát biztosít, míg a `match` kifejezés olyan, mint egy "switch-statement". 

### <a name="case-expression"></a>Esetkifejezés

A `case` kifejezés olyan feltételes kifejezéstípus, amely "if/then/else" logikát biztosít. Az ilyen típusú kifejezés a logikai feltételek listáját lépteti át. Az első logikai feltétel kimeneti értékét adja vissza, amely igaz értéket ad ki.

A következő pszeudokód `case` határozza meg a kifejezés szerkezetét. 

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

A következő példa végiglép a különböző logikai feltételeken, amíg meg nem találja az egyik, hogy kiértékeli a `true`, majd visszaadja a társított értéket. Ha nem számít ki `true`logikai feltételt, a függvény tartalék értéket ad vissza. 

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

### <a name="match-expression"></a>Kifejezés egyeztetése

A `match` kifejezés olyan feltételes kifejezéstípus, amely kapcsolóutasítás-szerű logikát biztosít. A bemenet bármilyen kifejezés `['get', 'entityType']` lehet, például karakterláncot vagy számot ad vissza. Minden címkének vagy egyetlen literális értéknek vagy literális értékek tömbjének kell lennie, amelyek értékei nek minden karakterláncnak vagy számnak kell lenniük. A bemenet megegyezik, ha a tömb bármelyik értéke egyezik. Minden címkének egyedinek kell lennie. Ha a bemeneti típus nem egyezik meg a címkék típusával, az eredmény a tartalék érték lesz.

A következő pszeudokód `match` határozza meg a kifejezés szerkezetét. 

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

A következő példa `entityType` egy pontjellemző tulajdonságát vizsgálja egy buborékrétegben, amely egyezést keres. Ha egyezést talál, a megadott értéket adja vissza, vagy visszaadja a tartalék értéket.

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

A következő példa egy tömb segítségével sorolja fel azokat a címkéket, amelyek mindegyike ugyanazt az értéket adja vissza. Ez a megközelítés sokkal hatékonyabb, mint az egyes címkék egyenként történő listázása. Ebben az esetben, `entityType` ha a szálláshely "étterem" vagy "grocery_store", a "piros" színt visszaadják.

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

A következő példa egyezési kifejezést használ egy "tömbben" vagy "tömbtartalmazza" típusú szűrő végrehajtásához. Ebben az esetben a kifejezés szűri azokat az adatokat, amelyek azonosítóértéke szerepel az engedélyezett azonosítók listájában. Kifejezések szűrőkkel való használataesetén az eredménynek logikai értéknek kell lennie.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>Egyesítő kifejezés

A `coalesce` kifejezés végiglépked a kifejezések egy készletén, amíg az első nem null érték meg nem lesz késedelmébe, és vissza nem adja ezt az értéket. 

A következő pszeudokód `coalesce` határozza meg a kifejezés szerkezetét. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Példa**

A következő példa `coalesce` egy kifejezés `textField` segítségével állítja be a szimbólumréteg beállítását. Ha `title` a tulajdonság hiányzik a `null`szolgáltatásból, vagy a beállítás `subtitle` a , a `null`kifejezés megpróbálja megkeresni a tulajdonságot, ha hiányzik, vagy , akkor visszaesik egy üres karakterláncra. 

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

A következő példa `coalesce` egy kifejezést használ a térképsprite-ban elérhető első elérhető képikon beolvasásához a megadott képnevek listájából.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Szövegkifejezések

A típuskifejezések különböző adattípusok, például karakterláncok, számok és logikai értékek tesztelésére és konvertálására szolgáló eszközöket biztosítanak.

| Kifejezés | Visszatérés típusa | Leírás |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | tömbobjektum \| | Konstans tömb- vagy objektumértéket ad eredményül. Ezzel a kifejezéssel megakadályozhatja, hogy egy tömb vagy objektum kifejezésként kiértékelhető legyen. Erre akkor van szükség, ha egy tömböt vagy objektumot egy kifejezésnek vissza kell adnia. |
| `['image', string]` | sztring | Ellenőrzi, hogy egy adott képazonosító be van-e töltve a térképek képsprite-ba. Ha ez az, az azonosítót adja vissza, ellenkező esetben null ad vissza. |
| `['to-boolean', value]` | logikai | A bemeneti értéket logikai értékké alakítja. Az eredmény `false` akkor következik be, `0` `false`amikor `null`a `NaN`bemenet üres karakterlánc, , , , vagy ; egyébként `true`a . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | szín | A bemeneti értéket színné alakítja. Ha több érték van megadva, mindegyik kiértékelése sorrendben történik, amíg az első sikeres konverzió be nem kerül. Ha egyik bemenet sem konvertálható, a kifejezés hiba. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | szám | Lehetőség szerint számmá alakítja a bemeneti értéket. Ha a `null` bemenet `false`vagy a , az eredmény 0. Ha a `true`bemeneti bemenet, az eredmény 1. Ha a bemenet karakterlánc, akkor az ECMAScript nyelvi specifikáció [Szám](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) karakterlánc függvényének használatával számmá alakul át. Ha több érték van megadva, mindegyik kiértékelése sorrendben történik, amíg az első sikeres konverzió be nem kerül. Ha egyik bemenet sem konvertálható, a kifejezés hiba. |
| `['to-string', value]` | sztring | A bemeneti értéket karakterláncsá alakítja. Ha a `null`bemeneti a `""`. Ha a bemenet logikai érték, `"true"` `"false"`az eredmény vagy a . Ha a bemenet szám, akkor az ECMAScript nyelvi specifikáció [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) szám függvényének használatával karakterláncba konvertálódik. Ha a bemenet színe, akkor css RGBA színkarakterláncra konvertálódik. `"rgba(r,g,b,a)"` Ellenkező esetben a program a bemenetet karakterlánccé alakítja az ECMAScript nyelvi specifikáció [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) függvényével. |
| `['typeof', value]` | sztring | A megadott érték típusát leíró karakterláncot ad eredményül. |

> [!TIP]
> Ha a böngészőkonzolon `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` hasonló hibaüzenet jelenik meg, az azt jelenti, hogy valahol van egy kifejezés a kódban, amely rendelkezik egy olyan tömbbel, amely nem rendelkezik karakterlánccal az első értékhez. Ha azt szeretné, hogy a kifejezés tömböt `literal` ad vissza, csomagolja a tömböt a kifejezéssel. A következő példa `offset` egy szimbólumréteg ikonbeállítását állítja be, amelynek két `match` számot tartalmazó tömbnek kell lennie, `entityType` kifejezéssel két eltolási érték kiválasztásához a pontjellemző tulajdonságának értéke alapján.
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
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Színkifejezések

A színkifejezések megkönnyítik a színértékek létrehozását és manipulálását.

| Kifejezés | Visszatérés típusa | Leírás |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | szín | Színértéket hoz létre *a piros,* *a zöld*és a *kék* összetevőkből, amelyeknek a és `0` `255`a között kell lennie, valamint a . `1` Ha bármelyik összetevő kívül esik a tartományon, a kifejezés hiba. |
| `['rgba', number, number, number, number]` | szín | Színértéket hoz létre *a piros,* *zöld*, `0` *kék* összetevőkből, amelyeknek a `1`között és `255`a között kell lennie, és egy alfa-összetevőt a tartományon `0` belül. Ha bármelyik összetevő kívül esik a tartományon, a kifejezés hiba. |
| `['to-rgba']` | \[szám, szám, szám, szám\] | A bemeneti szín *piros,* *zöld,* *kék*és *alfa* összetevőit tartalmazó négyelemes tömböt ad eredményül ebben a sorrendben. |

**Példa**

A következő példa egy RGB színértéket hoz `255`létre, *amelynek piros* értéke , valamint *zöld* `temperature` és *kék* értékek, amelyeket a tulajdonság értékével való szorztással `2.5` számítanak ki. Ahogy a hőmérséklet változik, a szín a *vörös*különböző árnyalataira változik .

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

## <a name="string-operator-expressions"></a>Karakterlánc-operátor-kifejezések

A karakterlánc-operátor-kifejezések konverziós műveleteket hajtanak végre karakterláncokon, például összefűzik és konvertálják a kis- és nagybetűket. 

| Kifejezés | Visszatérés típusa | Leírás |
|------------|-------------|-------------|
| `['concat', string, string, …]` | sztring | Több karakterlánc összefűzése. Minden értéknek karakterláncnak kell lennie. A `to-string` típuskifejezéssel szükség esetén más értéktípusokat karakterláncba konvertálni. |
| `['downcase', string]` | sztring | A megadott karakterláncot kisbetűssé alakítja. |
| `['upcase', string]` | sztring | A megadott karakterláncot nagybetűssé alakítja. |

**Példa**

A következő példa `temperature` a pontjellemző tulajdonságát karakterlánccá alakítja, majd a "°F" szót a végére fűzi.

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

A fenti kifejezés egy tűt jelenít meg a térképen, amelynek tetején a "64°F" szöveg látható, ahogy az az alábbi képen látható.

<center>

![Példa karakterlánc-operátori kifejezésre](media/how-to-expressions/string-operator-expression.png)</center>

## <a name="interpolate-and-step-expressions"></a>Interpoláció és lépéskifejezések

Az Interpoláció és a lépéskifejezések segítségével interpolált görbe vagy lépésfüggvény mentén lehet értékeket számítani. Ezek a kifejezések olyan kifejezést vesznek fel, amely például `['get',  'temperature']`numerikus értéket ad vissza bemenetként. A bemeneti érték kiértékelése a bemeneti és kimeneti értékek párjai alapján történik, így meghatározható az interpolált görbe vagy lépésfüggvény számára legjobban illeszkedő érték. A kimeneti értékeket "leáll"-nak nevezzük. Az egyes számlálók bemeneti értékeinek számnak kell lenniük, és növekvő sorrendben kell lenniük. A kimeneti értékeknek számnak és számtömbnek vagy színnek kell lenniük.

### <a name="interpolate-expression"></a>Kifejezés interpolálása

Egy `interpolate` kifejezés segítségével folyamatos, sima értékhalmazt lehet kiszámítani a stopértékek közötti interpolációval. A `interpolate` színértékeket visszaadó kifejezés színátmenetet eredményez, amelyből az eredményértékek ki vannak jelölve.

Egy `interpolate` kifejezésben háromféle interpolációs módszer használható:
 
* `['linear']`- Lineárisan interpolál a két megálló között.
* `['exponential', base]`- Exponenciálisan interpolál a megállók között. Az `base` érték határozza meg a kimenet növekedésének sebességét. A magasabb értékek miatt a kimenet nagyobb mértékben növekszik a tartomány felső vége felé. Az `base` 1-hez közeli érték lineárisan növekvő kimenetet eredményez.
* `['cubic-bezier', x1, y1, x2, y2]`- Interpolál egy [köbös Bezier-görbét](https://developer.mozilla.org/docs/Web/CSS/timing-function) használva, amelyet az adott ellenőrzési pontok határoznak meg.

Íme egy példa arra, hogy hogyan néznek ki ezek a különböző interpolációk. 

| Lineáris  | Exponenciális | Köbös Bezier |
|---------|-------------|--------------|
| ![Lineáris interpolációs grafikon](media/how-to-expressions/linear-interpolation.png) | ![Exponenciális interpolációs grafikon](media/how-to-expressions/exponential-interpolation.png) | ![Köbös Bezier interpolációs grafikon](media/how-to-expressions/bezier-curve-interpolation.png) |

A következő pszeudokód `interpolate` határozza meg a kifejezés szerkezetét. 

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

A következő példa `linear interpolate` egy kifejezés `color` segítségével állítja be `temperature` a buborékréteg tulajdonságát a pontjellemző tulajdonsága alapján. Ha `temperature` az érték 60-nál kisebb, a "kék" értéket adja vissza. Ha 60 és 70 év között van, a sárga visszakerül. Ha 70 és kevesebb, mint 80, a "narancs" visszakerül. Ha 80 vagy nagyobb, a "piros" visszakerül.

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

Az alábbi képen látható, hogyan választják ki a színeket a fenti kifejezéshez.
 
<center>

![Példa a kifejezés](media/how-to-expressions/interpolate-expression-example.png) interpolálására</center>

### <a name="step-expression"></a>Lépéskifejezés

A `step` kifejezés segítségével diszkrét, lépcsős eredményértékeket számíthat ki a megállók által [meghatározott, darabonként állandó függvény](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) kiértékelésével. 

A következő pszeudokód `step` határozza meg a kifejezés szerkezetét. 

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

A lépéskifejezések a leállítás kimeneti értékét adják vissza közvetlenül a bemeneti érték előtt, vagy az első bemeneti értéket, ha a bemenet kisebb, mint az első állomás. 

**Példa**

A következő példa `step` egy kifejezés `color` segítségével állítja be `temperature` a buborékréteg tulajdonságát a pontjellemző tulajdonsága alapján. Ha `temperature` az érték 60-nál kisebb, a "kék" értéket adja vissza. Ha 60 és kevesebb, mint 70, a "sárga" visszakerül. Ha 70 és kevesebb, mint 80, a "narancs" visszakerül. Ha 80 vagy nagyobb, a "piros" visszakerül.

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

Az alábbi képen látható, hogyan választják ki a színeket a fenti kifejezéshez.
 
<center>

![Példa lépéskifejezésre](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Rétegspecifikus kifejezések

Speciális kifejezések, amelyek csak adott rétegekre vonatkoznak.

### <a name="heat-map-density-expression"></a>Hőtérkép sűrűségének kifejezése

A hőtérkép sűrűségének kifejezése lekéri a hőtérkép sűrűségének értékét a `['heatmap-density']`hőtérkép-réteg minden egyes képpontjához, és a meghatározása a . Ez az érték `0` a `1`és a között szám. Egy vagy `interpolation` `step` kifejezéssel kombinálva definiálja a hőtérkép színezéséhez használt színátmenetet. Ez a kifejezés csak a hőtérkép-réteg [színbeállításában](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) használható.

> [!TIP]
> A 0. A 0-as index színével lehet meghatározni a háttérszínt. Sokan inkább ezt az értéket átláthatóvagy félig átlátszó fekete.

**Példa**

Ez a példa vonalhajózási interpolációs kifejezést használ a hőtérkép simított színátmenetének létrehozásához. 

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

A mellett, hogy egy sima színátmenetet használ a hőtérkép színezésére, a `step` színek egy kifejezés használatával is megadhatók egy tartományon belül. A `step` hőtérkép színezéséhez használt kifejezés vizuálisan olyan tartományokra bontja a sűrűséget, amelyek hasonlítanak egy kontúr- vagy radarstílus-térképre.  

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

További információt a [Hőtérkép-réteg hozzáadása](map-add-heat-map-layer.md) dokumentációban talál.

### <a name="line-progress-expression"></a>Sorfolyamat-kifejezés

A vonal végrehajtási kifejezése a vonalréteg színátmenetvonala mentén `['line-progress']`halad, és a definíciója a . Ez az érték 0 és 1 közötti szám. Ezt használják együtt egy `interpolation` vagy `step` kifejezés. Ez a kifejezés csak a vonalréteg [körvonalGradient beállításával]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) használható. 

> [!NOTE]
> A `strokeGradient` vonalréteg beállításához `lineMetrics` az adatforrás beállítását a `true`parancsra kell állítani.

**Példa**

Ez a `['line-progress']` példa a kifejezés segítségével színátmenetet alkalmaz egy vonal körvonalára.

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

[Élő példa megtekintése](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Szövegmezőformátum kifejezése

A szövegmező formátumkifejezésa a `textField` szimbólumrétegek `textOptions` tulajdonság beállításával használható vegyes szövegformázást biztosít. Ez a kifejezés lehetővé teszi a bemeneti karakterláncok és formázási beállítások készletének megadhatóvá nyilvánítási karakterláncok és formázási beállítások megírását. Ebben a kifejezésben a következő beállítások adhatók meg a kifejezés minden bemeneti karakterláncához.

 * `'font-scale'`- Megadja a betűméret méretezési tényezőjét. Ha meg van adva, ez `size` az `textOptions` érték felülírja az egyes karakterláncok tulajdonságát.
 * `'text-font'`- Egy vagy több betűtípuscsaládot ad meg, amelyet ehhez a karakterlánchoz kell használni. Ha meg van adva, ez `font` az `textOptions` érték felülírja az egyes karakterláncok tulajdonságát.
 * `'text-color'`- Megadja azt a színt, amelyet a szövegre alkalmazni kell a rendereléskor. 

A következő pszeudokód határozza meg a szövegmező formátumkifejezésének szerkezetét. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**Példa**

A következő példa félkövér betűtípus hozzáadásával és a szolgáltatás `title` tulajdonságának betűméretének növelése mellett formázza a szövegmezőt. Ez a példa `subtitle` a szolgáltatás tulajdonságát is hozzáadja egy új vonalhoz, kicsinyített betűmérettel és piros színnel.

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
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

Ez a réteg a pontfunkciót az alábbi képen látható módon jeleníti meg:
 
<center>

![A Pont szolgáltatás képe formázott szövegmezővel](media/how-to-expressions/text-field-format-expression.png)</center>

### <a name="number-format-expression"></a>Számformátum-kifejezés

A `number-format` kifejezés csak szimbólumréteg `textField` beállításával használható. Ez a kifejezés formázott karakterláncdá alakítja a megadott számot. Ez a kifejezés körbefolyatja a JavaScript [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) függvényét, és a következő beállításokat támogatja.

 * `locale`- Adja meg ezt a beállítást a számok karakterláncokká alakításához a megadott nyelvhez igazodó módon. Adja át a [BCP 47 nyelvi címkét](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) ennek a beállításnak.
 * `currency`- A szám átváltása pénznemet képviselő karakterláncra. A lehetséges értékek az [ISO 4217 pénznemkódok,](https://en.wikipedia.org/wiki/ISO_4217)mint például az "USD" az USA-dollár, az "EUR" az euró, vagy a "CNY" a kínai RMB esetében.
 * `'min-fraction-digits'`- Megadja a szám karakterláncverziójába felvenni a tizedesjegyek minimális számát.
 * `'max-fraction-digits'`- Megadja a szám karakterláncverziójába felvenni rekedő tizedesjegyek maximális számát.

A következő pszeudokód határozza meg a szövegmező formátumkifejezésének szerkezetét. 

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

A következő példa `number-format` egy kifejezés `revenue` segítségével módosítja a pontjellemző `textField` tulajdonságának megjelenítését a szimbólumréteg beállításában úgy, hogy az USA-dollár értéknek tűnjön.

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

Ez a réteg a pontfunkciót az alábbi képen látható módon jeleníti meg:

<center>

![Példa a](media/how-to-expressions/number-format-expression.png) Számformátum kifejezésre</center>

### <a name="image-expression"></a>Képkifejezés

A képkifejezés a szimbólumréteg `textField` és a `image` sokszögréteg `fillPattern` beállításaival, valamint lehetőséggel használható. Ez a kifejezés ellenőrzi, hogy a kért kép létezik-e a stílusban, és a feloldott képnevet vagy `null`a , attól függően, hogy a kép jelenleg a stílusban van-e. Ez az ellenőrzési folyamat szinkron, és megköveteli, hogy a lemezképet hozzáadja a stílushoz, mielőtt a lemezkép argumentumban kérné.

**Példa**

A következő példa `image` egy kifejezés segítségével egy ikont ad a szimbólumrétegben lévő szöveggel. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Ez a réteg a szimbólumréteg szövegmezőjét jeleníti meg az alábbi képen látható módon:

<center>

![Példa képkifejezésre](media/how-to-expressions/image-expression.png)</center>

## <a name="zoom-expression"></a>Nagyítási kifejezés

A `zoom` program a térkép aktuális nagyítási szintjének lekéréséhez `['zoom']`használja a leképezési időt, és a definíciója a . Ez a kifejezés a térkép minimális és maximális nagyítási szinttartománya közötti számot adja vissza. Az Azure Maps interaktív térképvezérlők webes és Android-alapú támogatja a 25 nagyítási szint, számozott 0-24. A `zoom` kifejezés használata lehetővé teszi a stílusok dinamikus módosítását a térkép nagyítási szintjének módosításával. A `zoom` kifejezés csak kifejezésekkel `step` és kifejezésekkel `interpolate` használható.

**Példa**

Alapértelmezés szerint a hőtérkép-rétegben megjelenített adatpontok sugara rögzített képpontsugarat mutat az összes nagyítási szinthez. A térkép nagyításakor az adatok összesítése és a hőtérkép-réteg máshogy néz ki. Egy `zoom` kifejezés segítségével skálázhatja az egyes nagyítási szintsugarat úgy, hogy minden adatpont a térkép ugyanazon fizikai felületét fedje le. Ez teszi a hőtérkép réteg meg statikus és következetes. A térkép minden egyes nagyítási szintje kétszer annyi képponttal rendelkezik függőlegesen és vízszintesen, mint az előző nagyítási szint. A sugár méretezése, úgy, hogy minden egyes nagyítási szinttel megduplázódik, olyan hőtérképet hoz létre, amely minden nagyítási szinten egységesnek tűnik. Ez a `zoom` kifejezés kifejezéssel egy `base 2 exponential interpolation` kifejezéssel valósítható meg, a minimális nagyítási szinthez beállított képpontsugárral és a maximális nagyítási szinthez szükséges méretezett sugárral, az alábbiak szerint kiszámítva. `2 * Math.pow(2, minZoom - maxZoom)`

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[Élő példa megtekintése](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Változókötési kifejezések

A változókötési kifejezések egy számítás eredményét változóban tárolják. Így a számítási eredmények többször is hivatkozhatók egy kifejezésben máshol. Ez egy hasznos optimalizálás a kifejezések, amelyek magukban foglalják a sok számítást.

| Kifejezés | Visszatérés típusa | Leírás |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"legyen",<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: karakterlánc,<br/>&nbsp;&nbsp;&nbsp;&nbsp;érték1: bármely,<br/>&nbsp;&nbsp;&nbsp;&nbsp;név2: karakterlánc,<br/>&nbsp;&nbsp;&nbsp;&nbsp;érték2: bármely,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression kifejezés<br/>\] | | Egy vagy több értéket változóként tárol `var` az eredményt visszaadó gyermekkifejezésben lévő kifejezés számára. |
| `['var', name: string]` | bármely | A kifejezés sel létrehozott változóra `let` hivatkozik. |

**Példa**

Ez a példa egy olyan kifejezést használ, amely kiszámítja a bevételt a hőmérséklet arányhoz viszonyítva, majd egy `case` kifejezést használ az érték különböző logikai műveleteinek kiértékelésére. A `let` kifejezés a bevétel és a hőmérséklet arány ának tárolására szolgál, így csak egyszer kell kiszámítani. A `var` kifejezés a változóra szükség szerint annyit hivatkozik, amilyen gyakran újra kell számítania.

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

A kifejezéseket megvalósító további kódmintákat az alábbi cikkekben a következő cikkekben lehet látni:

> [!div class="nextstepaction"] 
> [Szimbólumréteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Buborékréteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Hőtérképréteg hozzáadása](map-add-heat-map-layer.md)

További információ a kifejezéseket támogató rétegbeállításokról:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions (Buborékréteg opciók)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOpciók](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [Sokszögréteg beállításai](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
