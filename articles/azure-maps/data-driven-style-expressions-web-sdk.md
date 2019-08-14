---
title: Adatvezérelt stílusú kifejezések a Azure Maps web SDK-ban | Microsoft Docs
description: Adatvezérelt stílusú kifejezések használata a Azure Maps web SDK-ban.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 507af54b8b4c2e7c67538a1a25a040c7ee5fdfd5
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976315"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Adatvezérelt stílusú kifejezések (web SDK)

A kifejezések lehetővé teszik, hogy üzleti logikát alkalmazzon az adatforrásban lévő egyes alakzatokban definiált tulajdonságokat betekintő formázási lehetőségekre. A kifejezések az adatforrásban vagy a rétegben lévő adatszűréshez is használhatók. A kifejezések tartalmazhatnak feltételes logikát, például if-utasítást, és az adatkezeléshez is használhatók; karakterlánc-, logikai és matematikai operátorok. 

Az adatvezérelt stílusok csökkenthetik a szükséges kód mennyiségét, hogy az üzleti logikát a stílus alapján lehessen megvalósítani. A rétegekkel való használat esetén a kifejezések kiértékelése egy külön szálon történik, amely nagyobb teljesítményt biztosít a felhasználói felületi szál üzleti logikájának kiértékeléséhez képest.

Az alábbi videó áttekintést nyújt az adatvezérelt stílusról a Azure Maps web SDK-ban.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

A kifejezések JSON-tömbökként jelennek meg. A tömb egyik kifejezésének első eleme egy olyan karakterlánc, amely megadja a kifejezés operátor nevét. Például: "+" vagy "Case". A következő elemek (ha vannak ilyenek) a kifejezés argumentumai. Minden argumentum vagy egy literális érték (karakterlánc, szám, logikai vagy `null`), vagy egy másik Expression tömb. A következő pseudocode határozzák meg a kifejezés alapszerkezetét. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

A Azure Maps web SDK számos olyan típust támogat, amelyek a saját vagy más kifejezésekkel együtt használhatók.

| Kifejezések típusa | Leírás |
|---------------------|-------------|
| [Összesítő kifejezés](#aggregate-expression) | Egy olyan kifejezés, amely meghatározza egy adathalmazon feldolgozott számítást, és használható az `clusterProperties` a kapcsolóval. `DataSource` |
| [Logikai kifejezések](#boolean-expressions) | A logikai kifejezések logikai operátorok egy készletét biztosítják a logikai összehasonlítások kiértékeléséhez. |
| [Színkifejezések](#color-expressions) | A színkifejezések egyszerűbbé teszik a színértékek létrehozását és kezelését. |
| [Feltételes kifejezések](#conditional-expressions) | A feltételes kifejezések olyan logikai műveleteket biztosítanak, amelyek például if-utasítások. |
| [Adatkifejezések](#data-expressions) | Hozzáférést biztosít a szolgáltatásban található tulajdonság-információhoz. |
| [Interpolációs és Step kifejezések](#interpolate-and-step-expressions) | Az interpolációs és a Step kifejezésekkel az értékek kiszámíthatók az interpolált görbe vagy a Step függvény használatával. |
| [Réteg-specifikus kifejezések](#layer-specific-expressions) | Olyan speciális kifejezések, amelyek csak egyetlen rétegre érvényesek. |
| [Matematikai kifejezések](#math-expressions) | Matematikai operátorokat biztosít az adatvezérelt számítások végrehajtásához a kifejezés-keretrendszeren belül. |
| [Karakterlánc-operátor kifejezései](#string-operator-expressions) | A karakterlánc-operátor kifejezései olyan karakterlánc-átalakítási műveleteket hajtanak végre, mint például az Összefűzés és az átalakítás. |
| [Típus kifejezések](#type-expressions) | A Type kifejezések a különböző adattípusok (például karakterláncok, számok és logikai értékek) tesztelésére és átalakítására szolgáló eszközöket biztosítanak. |
| [Változó kötési kifejezések](#variable-binding-expressions) | A változó kötési kifejezések lehetővé teszik, hogy a számítás eredményei egy változóban legyenek tárolva, és többször is hivatkoznak egy kifejezésben, anélkül, hogy újra kellene számítani a tárolt értéket. |
| [Nagyítás kifejezése](#zoom-expression) | Lekéri a Térkép jelenlegi nagyítási szintjét renderelési időben. |

A jelen dokumentumban szereplő összes példa a következő funkcióval mutatja be a különböző típusú kifejezések használatának különböző módjait. 

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

Az adatkifejezések hozzáférést biztosítanak a szolgáltatásban található tulajdonság-értékekhez. 

| Kifejezés | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['at', number, array]` | object | Egy elem lekérése egy tömbből. |
| `['geometry-type']` | Karakterlánc | A szolgáltatás geometriai típusának beolvasása: Pont, multipoint, LineString, MultiLineString, sokszög, többsokszögű. |
| `['get', string]` | value | A tulajdonság értékének beolvasása az aktuális funkció tulajdonságaiból. Null értéket ad vissza, ha a kért tulajdonság hiányzik. |
| `['get', string, object]` | value | A tulajdonság értékének beolvasása a megadott objektum tulajdonságaiból. Null értéket ad vissza, ha a kért tulajdonság hiányzik. |
| `['has', string]` | boolean | Meghatározza, hogy a szolgáltatás tulajdonságai rendelkeznek-e a megadott tulajdonsággal. |
| `['has', string, object]` | boolean | Meghatározza, hogy az objektum tulajdonságai rendelkeznek-e a megadott tulajdonsággal. |
| `['id']` | value | A szolgáltatás AZONOSÍTÓjának beolvasása, ha rendelkezik ilyennel. |
| `['length', string | array]` | szám | Egy karakterlánc vagy tömb hosszának beolvasása. |

**Példák**

Egy szolgáltatás tulajdonságai közvetlenül egy kifejezés használatával `get` érhetők el egy kifejezésben. A következő példa a szolgáltatás "zoneColor" értékét használja a buborékdiagram szín tulajdonságának megadásához. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

A fenti példa akkor működik, ha az összes funkció rendelkezik a `zoneColor` tulajdonsággal, de ha nem, akkor a szín valószínűleg visszakerül a "fekete" értékre. A tartalék szín módosításához egy `case` kifejezés `has` használható a kifejezéssel együtt annak ellenőrzéséhez, hogy a tulajdonság létezik-e, és ha nem ad vissza egy tartalék színt.

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

A buborék és a szimbólum rétegek alapértelmezés szerint az adatforrásban lévő összes alakzat koordinátáit fogják megjeleníteni. Ezt megteheti egy sokszög vagy vonal csúcspontjának kiemeléséhez. A réteg `['geometry-type']` beállításával korlátozhatja az általa megjelenített szolgáltatások geometriájának típusát egy logikai kifejezésen belüli kifejezés használatával. `filter` Az alábbi példa egy buborék réteget korlátozza, hogy csak `Point` a funkciók legyenek megjelenítve.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

A következő példa lehetővé teszi `Point` a és `MultiPoint` a szolgáltatások megjelenítését. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Hasonlóképpen a sokszögek körvonalai is megjelennek a sorokban. Ha le szeretné tiltani ezt a viselkedést egy vonal rétegben, adjon hozzá `LineString` egy `MultiLineString` olyan szűrőt, amely csak a és a funkciókat engedélyezi.  

## <a name="math-expressions"></a>Matematikai kifejezések

A matematikai kifejezések matematikai operátorokat biztosítanak az adatvezérelt számítások végrehajtásához a kifejezés keretrendszerében.

| Kifejezés | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['+', number, number, …]` | szám | Kiszámítja a megadott számok összegét. |
| `['-', number]` | szám | A megadott szám szerinti 0 kivonása. |
| `['-', number, number]` | szám | Kivonja az első számokat a második szám alapján. |
| `['*', number, number, …]` | szám | A megadott számok összeszorzása. |
| `['/', number, number]` | szám | Az első szám felosztása a második szám alapján. |
| `['%', number, number]` | szám | Kiszámítja a maradékot az első szám második számmal való osztásakor. |
| `['^', number, number]` | szám | Kiszámítja a második szám hatványára emelt első érték értékét. |
| `['abs', number]` | szám | Kiszámítja a megadott szám abszolút értékét. |
| `['acos', number]` | szám | Kiszámítja a megadott szám szám arkusz koszinuszát. |
| `['asin', number]` | szám | Kiszámítja a megadott szám arkusz szinuszát. |
| `['atan', number]` | szám | Kiszámítja a megadott szám arkusz tangensét. |
| `['ceil', number]` | szám | Felfelé kerekíti a számot a következő egész számra. |
| `['cos', number]` | szám | Kiszámítja a megadott szám cos-számát. |
| `['e']` | szám | A matematikai állandót `e`adja vissza. |
| `['floor', number]` | szám | Lefelé kerekíti a számot az előző egész egész számra. |
| `['ln', number]` | szám | A megadott szám természetes alapú logaritmusát számítja ki. |
| `['ln2']` | szám | A matematikai állandót `ln(2)`adja vissza. |
| `['log10', number]` | szám | Kiszámítja a megadott szám 10-es alapú logaritmusát. |
| `['log2', number]` | szám | Kiszámítja a megadott szám alap-két logaritmusát. |
| `['max', number, number, …]` | szám | Kiszámítja a megadott Számsorozatok maximális számát. |
| `['min', number, number, …]` | szám | Kiszámítja a minimális számot a megadott számú készletben. |
| `['pi']` | szám | A matematikai állandót `PI`adja vissza. |
| `['round', number]` | szám | A számot a legközelebbi egész számra kerekíti. A félúton lévő értékek a nullától távolabbi távolságra vannak kerekítve. Például `['round', -1.5]` a-2 értékre. |
| `['sin', number]` | szám | Kiszámítja a megadott szám szinuszát. |
| `['sqrt', number]` | szám | Kiszámítja a megadott szám négyzet gyökerét. |
| `['tan', number]` | szám | Kiszámítja a megadott szám tangensét. |

## <a name="aggregate-expression"></a>Összesítő kifejezés

Az összesítő kifejezés egy adathalmazon feldolgozott számítást határoz meg, amely a használatával használható `clusterProperties`. `DataSource` A kifejezések kimenetének számnak vagy logikai értéknek kell lennie. 

Az összesítő kifejezés három értéket vesz igénybe; egy operátor értéke és a kezdeti érték, valamint egy kifejezés, amely egy tulajdonságot kér le egy olyan adatokból, amely az összesített műveletet alkalmazza a alkalmazásban. A kifejezés formátuma a következő:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- üzemeltető Egy kifejezési függvény, amely a fürt minden pontja által `mapExpression` kiszámított összes értékre vonatkozik. Támogatott operátorok; 
    - Számok: `+` `*` ,,,`max``min`
    - Logikai értékek esetén: `all`,`any`
- initialValue: Egy kezdeti érték, amelyben az első számított érték összesítve lesz.
- mapExpression: Az adathalmaz minden pontjára alkalmazott kifejezés.

**Példák**

Ha az adathalmaz összes funkciója rendelkezik egy `revenue` számmal rendelkező tulajdonsággal. Az adatkészletből létrehozott fürt összes pontjának teljes bevételét a következő összesítő kifejezéssel számíthatja ki:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Logikai kifejezések

A logikai kifejezések logikai operátorok egy készletét biztosítják a logikai összehasonlítások kiértékeléséhez.

Az értékek összehasonlításakor az összehasonlítás szigorúan be van írva. A különböző típusú értékek mindig egyenlőtlennek számítanak. Azok az esetek, amelyekben a típusok ismertek, hogy az elemzési idő eltérő, és elemzési hibát eredményez. 

| Kifejezés | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Logikai tagadás. Visszaadja `true` `false`, ha a bemenet, `false` és ha a bemenet `true`szerepel. |
| `['!= ', value, value]` | boolean | Visszaadja `true` , ha a bemeneti értékek nem egyenlőek, `false` ellenkező esetben. |
| `['<', value, value]` | boolean | Akkor `true` adja vissza, ha az első bemenet szigorúan kisebb a másodiknál, `false` ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['<=', value, value]` | boolean | Azt `true` adja vissza, hogy az első bemenet kisebb-e vagy egyenlő- `false` e a másodikval, ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['==', value, value]` | boolean | Visszaadja `true` , ha a bemeneti értékek egyenlőek, `false` ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['>', value, value]` | boolean | Azt `true` adja vissza, hogy az első bemenet szigorúan nagyobb-e `false` , mint a második, ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['>=' value, value]` | boolean | Akkor `true` adja vissza, ha az első bemenet nagyobb vagy egyenlő, mint a `false` második, ellenkező esetben. Az argumentumoknak karakterláncoknak vagy mindkét számnak kell lenniük. |
| `['all', boolean, boolean, …]` | boolean | Visszaadja `true` `true`, ha az összes bemenet `false` , ellenkező esetben. |
| `['any', boolean, boolean, …]` | boolean | Visszaadja `true` `true`, ha a bemenetek bármelyike, `false` ellenkező esetben. |

## <a name="conditional-expressions"></a>Feltételes kifejezések

A feltételes kifejezések olyan logikai műveleteket biztosítanak, amelyek például if-utasítások.

A következő kifejezések feltételes logikai műveleteket hajtanak végre a bemeneti adatokon. A `case` kifejezés például "If/then/Else" logikát biztosít, miközben a `match` kifejezés olyan, mint a "Switch-utasítás". 

### <a name="case-expression"></a>Case kifejezés

A `case` kifejezés olyan feltételes kifejezés, amely megadja a IF-utasításhoz hasonló logikát (ha/ha/ha van ilyen). Ez a típusú kifejezés a logikai feltételek listáján keresztül történik, és az első logikai feltétel kimeneti értékét adja vissza igaz értékként.

A következő pseudocode határozzák meg a `case` kifejezés szerkezetét. 

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

A következő példa különböző logikai feltételeken halad végig `true`, amíg meg nem találja a kiértékelését, majd visszaadja a hozzá tartozó értéket. Ha egyetlen logikai feltétel sincs kiértékelve `true`, a rendszer visszaadja a tartalék értéket. 

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

### <a name="match-expression"></a>Egyezés kifejezése

A `match` kifejezés olyan feltételes kifejezés, amely switch-utasítást, például logikát biztosít. A bemenet bármely kifejezés lehet, `['get', 'entityType']` például egy sztringet vagy egy számot ad vissza. Minden címkének egy literális vagy literális értékből álló tömbnek kell lennie, amelynek értékének minden sztringnek vagy számnak kell lennie. A bemenet megegyezik, ha a tömb bármelyik értéke megegyezik. Minden címkének egyedinek kell lennie. Ha a bemeneti típus nem egyezik a címkék típusával, az eredmény a tartalék érték lesz.

A következő pseudocode határozzák meg a `match` kifejezés szerkezetét. 

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

A következő példa `entityType` egy buborék rétegben lévő pont funkció tulajdonságát keresi meg. Ha egyezést talál, a rendszer a megadott értéket adja vissza, vagy visszaadja a tartalék értéket.

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

Az alábbi példa egy tömböt használ a címkék egy halmazának listázásához, amelynek minden esetben ugyanazt az értéket kell visszaadnia. Ez sokkal hatékonyabb, mint a címkék egyenkénti listázása. Ebben az esetben, ha a `entityType` tulajdonság "Restaurant" vagy "grocery_store", a "Red" színt adja vissza.

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

A következő példa egy egyezési kifejezést használ egy "in Array" vagy "Array" típusú szűrő elvégzéséhez, ebben az esetben olyan adatszűrési értékeket tartalmaz, amely az engedélyezett azonosítók listáján szereplő azonosító értékkel rendelkezik. Ha szűrőket használó kifejezéseket használ, az eredménynek logikai értéknek kell lennie.

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

Egy `coalesce` kifejezés a kifejezések egy halmazán halad át, amíg az első nem null értéket nem szerzi be, és az értéket adja vissza. 

A következő pseudocode határozzák meg a `coalesce` kifejezés szerkezetét. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Példa**

Az alábbi példa egy `coalesce` kifejezést használ egy szimbólum réteg `textField` beállításának beállításához. Ha a `title` tulajdonság hiányzik a szolgáltatásból `null`, vagy a értékre van állítva, a kifejezés ezután megpróbálja megkeresni a `subtitle` tulajdonságot, `null`ha a hiányzó, vagy pedig visszaesik egy üres karakterláncra. 

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

## <a name="type-expressions"></a>Típus kifejezések

A Type kifejezések a különböző adattípusok (például karakterláncok, számok és logikai értékek) tesztelésére és átalakítására szolgáló eszközöket biztosítanak.

| Kifejezés | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | tömb \| objektum | Egy konstans tömb vagy objektum értékét adja vissza. Ezzel a kifejezéssel megakadályozható, hogy egy tömb vagy objektum kifejezésként legyen kiértékelve. Erre akkor van szükség, ha egy tömböt vagy objektumot egy kifejezésnek kell visszaadnia. |
| `['to-boolean', value]` | boolean | A bemeneti értéket logikai értékre alakítja. Az eredmény `false` az, ha a bemenet egy üres karakterlánc `false`, `0` `null`,, vagy `NaN`;, ellenkező esetben `true`a. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | szín | Átalakítja a bemeneti értéket egy színre. Ha több érték van megadva, a rendszer mindegyiket kiértékeli, amíg meg nem történik az első sikeres konverzió. Ha a bemenetek egyike sem alakítható át, akkor a kifejezés hibát jelez. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | szám | Ha lehetséges, átalakítja a bemeneti értéket egy számra. Ha a bemenet `null` vagy `false`a, az eredmény 0. Ha a bemenet értéke `true`, az eredmény 1. Ha a bemenet egy karakterlánc, akkor a rendszer a ECMAScript nyelvi specifikáció [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) string függvényével egy számra konvertálja. Ha több érték van megadva, a rendszer mindegyiket kiértékeli, amíg meg nem történik az első sikeres konverzió. Ha a bemenetek egyike sem alakítható át, akkor a kifejezés hibát jelez. |
| `['to-string', value]` | Karakterlánc | A bemeneti értéket karakterlánccá alakítja. Ha a bemenet értéke `null`, az `""`eredmény:. Ha a bemenet logikai érték, az eredmény `"true"` a vagy `"false"`a. Ha a bemenet egy szám, a rendszer a ECMAScript nyelv specifikációjának [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) Number függvényét használva karakterlánccá alakítja át. Ha a bemenet szín, a rendszer átalakítja a CSS RGBA színkarakterlánccá `"rgba(r,g,b,a)"`. Ellenkező esetben a rendszer a bemenetet egy karakterlánccá alakítja át a ECMAScript nyelvi specifikációjának [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) funkciója segítségével. |
| `['typeof', value]` | Karakterlánc | A megadott érték típusát leíró karakterláncot ad vissza. |

> [!TIP]
> Ha egy hasonló `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` hibaüzenet jelenik meg a böngésző konzolján, akkor az azt jelenti, hogy a kódban van egy olyan kifejezés, amely egy olyan tömbvel rendelkezik, amely nem rendelkezik sztringtel az első értékhez. Ha azt szeretné, hogy a kifejezés egy tömböt ad vissza, zárja be `literal` a tömböt a kifejezéssel. Az alábbi példa egy szimbólum réteg `offset` ikonját állítja be, amelynek két számot tartalmazó tömbnek kell lennie, egy `match` kifejezéssel, amely két eltolási érték közötti választást alkalmaz a pont `entityType` tulajdonságának értéke alapján. vonás.
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

## <a name="color-expressions"></a>Színkifejezések

A színkifejezések egyszerűbbé teszik a színértékek létrehozását és kezelését.

| Kifejezés | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | szín | Egy színértéket hoz létre a *vörös*, *zöld*és *kék* összetevőkből a és `0` `255`a között, és egy alfa- `1`összetevőt. Ha bármelyik összetevő tartományon kívül esik, a kifejezés hibát jelez. |
| `['rgba', number, number, number, number]` | szín | Egy színértéket hoz létre a *vörös*, *zöld*és *kék* összetevők között `255`, `0` amelyeknek a és a közötti tartományba kell esnie `1`, valamint egy alfa `0` -összetevőt a és a tartományban. Ha bármelyik összetevő tartományon kívül esik, a kifejezés hibát jelez. |
| `['to-rgba']` | \[szám, szám, szám, szám\] | Egy négy elemből álló tömböt ad vissza, amely a bemeneti szín *vörös*, *zöld*, *kék*és *alfa* összetevőit tartalmazza ebben a sorrendben. |

**Példa**

A következő példa létrehoz és RGB színértéket `255`tartalmaz, amely *vörös* értékkel rendelkezik, a *zöld* és a `2.5` `temperature` kék értékeket pedig a tulajdonság értékének szorzatával számítjuk ki. Ahogy a hőmérséklet megváltozik, a szín különböző árnyalatú *vörös*színűre változik.

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

## <a name="string-operator-expressions"></a>Karakterlánc-operátor kifejezései

A karakterlánc-operátor kifejezései olyan karakterlánc-átalakítási műveleteket hajtanak végre, mint például az Összefűzés és az átalakítás. 

| Kifejezés | Visszatérési típus | Leírás |
|------------|-------------|-------------|
| `['concat', string, string, …]` | Karakterlánc | Több karakterlánc összefűzése egymással. Minden értéknek sztringnek kell lennie. Ha szükséges `to-string` , használja a Type kifejezést más típusú értékek karakterlánccá alakításához. |
| `['downcase', string]` | Karakterlánc | A megadott karakterláncot kisbetűsre alakítja. |
| `['upcase', string]` | Karakterlánc | A megadott karakterláncot nagybetűssé alakítja. |

**Példa**

Az alábbi példa átalakítja `temperature` a pont funkció tulajdonságát egy sztringre, majd a végéhez összefűzi a "°f" karakterláncot.

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

A fenti kifejezés egy PIN-kódot jelenít meg a térképen a "64 °F" szöveggel, az alábbi képen látható módon.

<center>

![Karakterlánc-operátor kifejezése – példa](media/how-to-expressions/string-operator-expression.png)</center>

## <a name="interpolate-and-step-expressions"></a>Interpolációs és Step kifejezések

Az interpolációs és a Step kifejezésekkel az értékek kiszámíthatók az interpolált görbe vagy a Step függvény használatával. Ezek a kifejezések olyan kifejezéssel rendelkeznek, amely egy numerikus értéket ad vissza bemenetként, `['get',  'temperature']`például:. A bemeneti érték kiértékelése a "leállítások" nevű bemeneti és kimeneti értékekkel, az interpolált görbe vagy a Step függvényhez legjobban illeszkedő érték meghatározásával történik. Az egyes leállítás bemeneti értékének számnak kell lennie, és növekvő sorrendben kell lennie. A kimeneti értékeknek számnak, számok tömbének vagy színnek kell lenniük.

### <a name="interpolate-expression"></a>Interpolációs kifejezés

Egy `interpolate` kifejezéssel folytonos és simított értékeket lehet kiszámítani a leállítási értékek közötti interpolációval. A színértékeket visszaadó kifejezés olyan színátmenetet hoz létre, amelyben a rendszer kijelöli az eredmények értékét. `interpolate`

A `interpolate` kifejezésekben három típusú interpolációs módszer használható:
 
* `['linear']`– A leállások párosítása között lineárisan interpolált.
* `['exponential', base]`– A leállás exponenciálisan megszakítható. Az `base` érték határozza meg, hogy a kimenet milyen sebességgel növekszik. A nagyobb értékek miatt a kimenet nagyobb mértékben növekszik a tartomány magas végén. Az `base` 1 értékhez közeledő értékek olyan kimenetet eredményeznek, amely lineárisan növekszik.
* `['cubic-bezier', x1, y1, x2, y2]`– A megadott vezérlési pontok által meghatározott [köbméter Bezier-görbe](https://developer.mozilla.org/docs/Web/CSS/timing-function) használatával Interpolációk.

Íme egy példa arra, hogy a különböző típusú Interpolációk hogyan néznek ki. 

| Lineáris  | Exponenciális | Köbméter Bezier |
|---------|-------------|--------------|
| ![Lineáris interpolációs gráf](media/how-to-expressions/linear-interpolation.png) | ![Exponenciális interpolációs gráf](media/how-to-expressions/exponential-interpolation.png) | ![Köbméter Bezier-interpolációs gráf](media/how-to-expressions/bezier-curve-interpolation.png) |

A következő pseudocode határozzák meg a `interpolate` kifejezés szerkezetét. 

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

Az alábbi példa egy `linear interpolate` kifejezést használ egy `temperature` buborékdiagram `color` tulajdonságának beállítására a pont funkció tulajdonsága alapján. Ha az `temperature` érték kisebb, mint 60, akkor a rendszer a "Blue" értéket adja vissza, ha 60 és kevesebb, mint 70, a sárga értéket adja vissza, ha a 70 és kisebb, mint 80, a "narancssárga" értéket adja vissza a rendszer, ha az 80 vagy nagyobb értéke "Red".

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

![Példa](media/how-to-expressions/interpolate-expression-example.png) interpolált kifejezésre</center>

### <a name="step-expression"></a>Lépés kifejezése

Egy `step` kifejezéssel kiszámíthatja a különálló, lépcsőzetes eredmények értékét a leállások által definiált [piecewise függvény](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) kiértékelésével. 

A következő pseudocode határozzák meg a `step` kifejezés szerkezetét. 

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

A Step kifejezésekkel közvetlenül a bemeneti érték előtt, vagy az első leállítási értéknél kisebb értéket kell megadni a Leállítás eredményében. 

**Példa**

Az alábbi példa egy `step` kifejezést használ egy `temperature` buborékdiagram `color` tulajdonságának beállítására a pont funkció tulajdonsága alapján. Ha az `temperature` érték kisebb, mint 60, akkor a "Blue" értéket adja vissza, ha 60 és kevesebb, mint 70, a "sárga" karakterláncot adja vissza, ha a 70 és kisebb, mint 80, a "narancssárga" értéket adja vissza, ha az 80 vagy a nagyobb, a "Red" értéket adja vissza a rendszer.

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

![Példa a Step kifejezésre](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Réteg-specifikus kifejezések

Speciális kifejezések, amelyek csak bizonyos rétegekre érvényesek.

### <a name="heat-map-density-expression"></a>Hő-Térkép sűrűségének kifejezése

A Heat Map sűrűség kifejezés lekérdezi a Heat Térkép sűrűségének értékét egy hő-Térkép réteg minden egyes képpontjához `['heatmap-density']`, és definiálva van. Ez `0` az érték a és `1` a közötti szám, amely egy `interpolation` vagy `step` kifejezéssel együtt használható a hő-Térkép színezéséhez használt színátmenet definiálásához. Ez a kifejezés csak a Heat Map réteg [szín beállításában](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) használható.

> [!TIP]
> A 0 indexű szín egy interpolációs kifejezésben vagy egy lépés színének alapértelmezett színe határozza meg annak a területnek a színét, amelynél nincs olyan érték, amely a háttér színének meghatározására használható. Számos előnyben részesítette ezt az értéket áttetszőre vagy félig átlátszó feketére állítani. 

**Példa**

Ez a példa egy vonalhajózási interpolációs kifejezést használ a hő-Térkép megjelenítésére szolgáló sima színátmenet létrehozásához. 

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

Amellett, hogy a zökkenőmentes átmenetet egy hő-Térkép színezésére használja, a színeket `step` kifejezés használatával is megadhatja egy tartományon belül. A hő-Térkép színezésére szolgáló kifejezéshasználatávalasűrűségvizuálisanolyantartományokrakerül,amelyeknagyobbmértékbenhasonlítanakegykontúrosvagyradarstílusútérképre.`step`  

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

További információkért lásd a [Heat Map-réteg hozzáadása](map-add-heat-map-layer.md) című dokumentumot.

### <a name="line-progress-expression"></a>Vonal állapota kifejezés

Egy vonal-folyamatjelző kifejezés lekérdezi az előrehaladást egy vonal rétegében lévő átmenetes vonal mentén `['line-progress']`, és a következőképpen van definiálva:. Ez az érték 0 és 1 közötti szám, és egy `interpolation` vagy `step` kifejezéssel együtt használatos. Ez a kifejezés csak a vonal rétegének [strokeGradient kapcsolóval]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) használható. 

> [!NOTE]
> A vonal rétegének `true` `lineMetrics` `strokeGradient` beállításához meg kell adni az adatforrás beállítását.

**Példa**

A következő példa a `['line-progress']` kifejezés használatával egy színátmenetet alkalmaz egy vonal körvonalára.

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

[Lásd az élő példát](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Szöveg mező formázása kifejezés

A Text mező formázása kifejezés a Symbol Layers `textField` `textOptions` tulajdonsággal együtt használható vegyes szövegformázás biztosításához. Ez a kifejezés lehetővé teszi a bemeneti karakterláncok és a formázási beállítások megadását. Ebben a kifejezésben a következő beállítások adhatók meg minden bemeneti karakterlánchoz.

 * `'font-scale'`-Megadja a betűméret méretezési tényezőjét. Ha `textOptions` meg van adva, ez az érték `size` felülbírálja az egyéni sztringhez tartozó tulajdonságot.
 * `'text-font'`-Egy vagy több olyan betűkészlet-családot határoz meg, amelyet ehhez a karakterlánchoz kell használni. Ha `textOptions` meg van adva, ez az érték `font` felülbírálja az egyéni sztringhez tartozó tulajdonságot.

A következő pseudocode határozzák meg a szöveg mező formázása kifejezés szerkezetét. 

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

A következő példa egy félkövér betűkészlet hozzáadásával formázza a szövegmezőt, és a szolgáltatás `title` tulajdonságának betűméretét. Ez a példa a funkció `subtitle` tulajdonságát egy sortörésen is hozzáadja, és a méretezési betűméretet.

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

Ez a réteg az alábbi képen látható módon fogja megjeleníteni a pont funkciót:
 
<center>

![A pont funkció képe formázott szöveg mezővel](media/how-to-expressions/text-field-format-expression.png)</center>

### <a name="number-format-expression"></a>Számformátum kifejezése

A `number-format` kifejezés csak szimbólum réteg `textField` beállításával használható. Ez a kifejezés egy formázott karakterlánccá alakítja át a megadott számot. Ez a kifejezés a JavaScript [Number. toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) függvényt csomagolja be, és a következő beállításokat támogatja.

 * `locale`-Adja meg ezt a lehetőséget, ha a számokat karakterlánccá szeretné konvertálni úgy, hogy az a megadott nyelven legyen igazítva. Adja át a [BCP 47 Language címkét](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) ebbe a beállításba.
 * `currency`– A szám konvertálása pénznemet jelölő sztringre. A lehetséges értékek az [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217)-es pénznem-kódok, például az USA-dollár "USD", az "EUR" az euro esetében, vagy a kínai RMB "CNY".
 * `'min-fraction-digits'`-A szám karakterlánc-verziójában szerepeltetni kívánt tizedesjegyek minimális számát határozza meg.
 * `'max-fraction-digits'`-A szám karakterlánc-verziójában szerepeltetni kívánt tizedesjegyek maximális számát adja meg.

A következő pseudocode határozzák meg a szöveg mező formázása kifejezés szerkezetét. 

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

Az alábbi példa egy kifejezést `number-format` használ egy kifejezés használatával, `revenue` amely azt módosítja, hogyan jelenik meg a pont funkció `textField` tulajdonsága egy szimbólum rétegben, hogy megjelenjen az amerikai dollár értéke.

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

Ez a réteg az alábbi képen látható módon fogja megjeleníteni a pont funkciót:

<center>

![Példa](media/how-to-expressions/number-format-expression.png) a számformátum kifejezésére</center>

## <a name="zoom-expression"></a>Nagyítás kifejezése

A kifejezés használatával lekérdezhető a Térkép jelenlegi nagyítási szintje a renderelési időben, és a következőként van `['zoom']`definiálva:. `zoom` Ez a kifejezés a Térkép minimális és maximális nagyítási szintje közötti számot adja vissza. A kifejezés használata lehetővé teszi, hogy a stílusok dinamikusan legyenek módosítva, mivel módosul a Térkép nagyítási szintje. A `zoom` kifejezés csak `interpolate` és `step` kifejezésekkel használható.

**Példa**

Alapértelmezés szerint a Heat Map rétegben megjelenített adatpontok sugara rögzített képpont-sugárral rendelkezik az összes nagyítási szinthez. Mivel a Térkép kinagyítja az adatösszesítéseket, és a Heat Map-réteg eltérőnek tűnik. Egy `zoom` kifejezés használható a sugár méretezésére az egyes nagyítási szintekhez úgy, hogy az egyes adatpontok a Térkép fizikai területére is kiterjednek. Így a Heat Térkép rétegének statikus és konzisztensnek kell lennie. A Térkép minden nagyítási szintje kétszer annyi képpontot tartalmaz függőlegesen és vízszintesen, mint az előző nagyítási szint. A sugár méretezése úgy, hogy az minden nagyítási szinten megduplázódik, egy olyan hő-térképet hoz létre, amely minden nagyítási szinten konzisztensnek tűnik. Ez a kifejezésnek az `zoom` alább látható `base 2 exponential interpolation` kifejezéssel való használatával valósítható meg. 

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

[Lásd az élő példát](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Változó kötési kifejezések

A változó kötési kifejezések egy változóban tárolják a számítás eredményét, így a kifejezésben máshol is hivatkozhatók, anélkül, hogy újra kellene számítani. Ez egy hasznos optimalizálás olyan kifejezésekhez, amelyek sok számítást tartalmaznak

| Kifejezés | Visszatérési típus | Leírás |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Let",<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: karakterlánc,<br/>&nbsp;&nbsp;&nbsp;&nbsp;érték1: bármely,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: karakterlánc,<br/>&nbsp;&nbsp;&nbsp;&nbsp;érték2: bármely,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Egy vagy több értéket tárol változóként az eredményt visszaadó `var` gyermek kifejezésben szereplő kifejezés használatával. |
| `['var', name: string]` | bármilyen | A `let` kifejezés használatával létrehozott változóra hivatkozik. |

**Példa**

Ez a példa egy olyan kifejezést használ, amely a bevételt a hőmérsékleti arányhoz viszonyítva kiszámítja, majd egy `case` kifejezés használatával kiértékeli a különböző logikai műveleteket ezen az értéken. A `let` kifejezés a bevétel hőmérsékleti arányhoz viszonyított tárolására szolgál, így azt csak egyszer kell kiszámítani, és `var` a kifejezés a szükségesnél gyakrabban hivatkozik erre a változóra anélkül, hogy újra kellene számítania.

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

Az alábbi cikkekben további kódokat talál a kifejezések megvalósításához:

> [!div class="nextstepaction"] 
> [Szimbólum réteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Buborék réteg hozzáadása](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Vonal rétegének hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszög réteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Heat Map-réteg hozzáadása](map-add-heat-map-layer.md)

További információ a kifejezéseket támogató rétegbeli lehetőségekről:

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
