---
title: FELHASZNÁLÓIFELÜLET-definíciós függvények létrehozása
description: A Azure Managed Applications felhasználói felületi definícióinak összeállításakor használandó függvények ismertetése
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a93f4ff2ddc0737692de9e5619cf7a7521936224
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82980813"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition függvények
Ez a szakasz a CreateUiDefinition összes támogatott funkciójának aláírásait tartalmazza.

A függvények használatához a szögletes zárójelekkel meghívást kell használni. Például:

```json
"[function()]"
```

A karakterláncok és egyéb függvények a függvények paramétereinek is szerepelhetnek, de a sztringeket aposztrófok között kell megadni. Például:

```json
"[fn1(fn2(), 'foobar')]"
```

Adott esetben a függvények kimenetének tulajdonságaira hivatkozhat a pont operátor használatával. Például:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Hivatkozó függvények
Ezekkel a függvényekkel a CreateUiDefinition tulajdonságainak vagy környezetének kimeneteit lehet használni.

### <a name="basics"></a>alapjai
Az alapvető beállítások lépésben meghatározott elem kimeneti értékeit adja vissza.

A következő példa az alapismeretek lépésben megnevezett elem kimenetét adja vissza `foo` :

```json
"[basics('foo')]"
```

### <a name="steps"></a>lépések
Egy elem kimeneti értékeit adja vissza, amely a megadott lépésben van meghatározva. Az alapvető lépések lépésben szereplő elemek kimeneti értékeinek lekéréséhez használja `basics()` helyette a parancsot.

Az alábbi példa a nevű elem kimenetét adja vissza a következő `bar` lépésben `foo` :

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Az alapismeretek lépésben vagy az aktuális környezetben kiválasztott helyet adja vissza.

A következő példa visszatérhet `"westus"` :

```json
"[location()]"
```

## <a name="string-functions"></a>Sztringfüggvények
Ezek a függvények csak JSON-karakterláncokkal használhatók.

### <a name="concat"></a>concat
Egy vagy több karakterlánc összefűzése.

Ha például a kimenet értéke `element1` IF `"bar"` , akkor ez a példa a következő karakterláncot adja vissza `"foobar!"` :

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>substring
A megadott karakterlánc alsztringjét adja vissza. Az alkarakterlánc a megadott indexnél kezdődik, és a megadott hosszúságú.

Az alábbi példa a következőt adja vissza `"ftw"` :

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>csere
Egy olyan karakterláncot ad vissza, amelyben az aktuális karakterláncban szereplő megadott sztring összes előfordulása egy másik karakterlánccal van lecserélve.

Az alábbi példa a következőt adja vissza `"Everything is awesome!"` :

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
Globálisan egyedi karakterláncot (GUID) hoz létre.

A következő példa visszatérhet `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` :

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Egy kisbetűsre konvertált karakterláncot ad vissza.

Az alábbi példa a következőt adja vissza `"foobar"` :

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Egy nagybetűre konvertált karakterláncot ad vissza.

Az alábbi példa a következőt adja vissza `"FOOBAR"` :

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Gyűjtési függvények
Ezek a függvények gyűjtemények, például JSON-karakterláncok, tömbök és objektumok használatával használhatók.

### <a name="contains"></a>tartalmazza
Azt adja vissza `true` , hogy egy karakterlánc tartalmazza-e a megadott alkarakterláncot, egy tömb tartalmazza a megadott értéket, vagy egy objektum tartalmazza a megadott kulcsot.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a következőt adja vissza `true` :

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `false` :

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Visszaadott `element1` érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>hossz
Egy sztringben szereplő karakterek számát, egy tömbben lévő értékek számát, illetve egy objektum kulcsainak számát adja vissza.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a következőt adja vissza `6` :

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `3` :

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Visszaadott `element1` érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Az alábbi példa a következőt adja vissza `2` :

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>üres
Azt adja vissza, `true` hogy a karakterlánc, tömb vagy objektum null értékű vagy üres-e.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a következőt adja vissza `true` :

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `false` :

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Visszaadott `element1` érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Az alábbi példa a következőt adja vissza `false` :

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>4. példa: null és nem definiált
`element1`A feltételezés `null` vagy nincs meghatározva. Az alábbi példa a következőt adja vissza `true` :

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>első
A megadott karakterlánc első karakterét adja vissza. a megadott tömb első értéke; vagy a megadott objektum első kulcsa és értéke.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a következőt adja vissza `"f"` :

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `1` :

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Visszaadott `element1` érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Az alábbi példa a következőt adja vissza `{"key1": "foobar"}` :

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>utolsó
A megadott karakterlánc utolsó karakterét adja vissza, a megadott tömb utolsó értékét, vagy a megadott objektum utolsó kulcsát és értékét.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a következőt adja vissza `"r"` :

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `2` :

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Visszaadott `element1` érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Az alábbi példa a következőt adja vissza `{"key2": "raboof"}` :

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>eltarthat
Megadott számú folytonos karaktert ad vissza a karakterlánc elejétől, a tömb elejétől megadott számú folytonos értékből, vagy az objektum elejétől megadott számú folytonos kulcsból és értékből.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a következőt adja vissza `"foo"` :

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `[1, 2]` :

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Visszaadott `element1` érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Az alábbi példa a következőt adja vissza `{"key1": "foobar"}` :

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>kihagyása
Megkerüli a gyűjtemény megadott számú elemét, majd visszaadja a többi elemet.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a következőt adja vissza `"bar"` :

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `[3]` :

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Visszaadott `element1` érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Az alábbi példa a következőt adja vissza `{"key2": "raboof"}` :

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logikai függvények
Ezek a függvények feltételes használatban is használhatók. Előfordulhat, hogy egyes függvények nem támogatják az összes JSON-adattípust.

### <a name="equals"></a>egyenlő
Azt adja vissza `true` , hogy mindkét paraméter ugyanazzal a típussal és értékkel rendelkezik-e. Ez a függvény az összes JSON-adattípust támogatja.

Az alábbi példa a következőt adja vissza `true` :

```json
"[equals(0, 0)]"
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[equals('foo', 'foo')]"
```

Az alábbi példa a következőt adja vissza `false` :

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Azt adja vissza `true` , hogy az első paraméter szigorúan kisebb-e a második paraméternél. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `true` :

```json
"[less(1, 2)]"
```

Az alábbi példa a következőt adja vissza `false` :

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Visszaadja `true` , ha az első paraméter kisebb vagy egyenlő, mint a második paraméter. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `true` :

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Azt adja vissza `true` , hogy az első paraméter szigorúan nagyobb-e, mint a második paraméter. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `false` :

```json
"[greater(1, 2)]"
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Visszaadja `true` , ha az első paraméter nagyobb vagy egyenlő, mint a második paraméter. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `true` :

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>és
A értéket adja vissza `true` , ha az összes paraméter ki van értékelve `true` . Ez a függvény két vagy több paramétert támogat, amelyek csak logikai típusúak.

Az alábbi példa a következőt adja vissza `true` :

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Az alábbi példa a következőt adja vissza `false` :

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>vagy
Azt adja vissza `true` , hogy a paraméterek közül legalább az egyik a következőre van-e kiértékelve: `true` . Ez a függvény két vagy több paramétert támogat, amelyek csak logikai típusúak.

Az alábbi példa a következőt adja vissza `true` :

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Visszaadja `true` , ha a paraméter kiértékelése a következőre történik: `false` . Ez a függvény csak logikai típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `true` :

```json
"[not(false)]"
```

Az alábbi példa a következőt adja vissza `false` :

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>összefonódik
Az első nem null paraméter értékét adja vissza. Ez a függvény az összes JSON-adattípust támogatja.

A feltételezések `element1` és `element2` nincsenek meghatározva. Az alábbi példa a következőt adja vissza `"foobar"` :

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

Ez a függvény különösen olyan választható hívás kontextusában hasznos, amely az oldal betöltése után felhasználói beavatkozás miatt történik. Ilyen például, ha a felhasználói felületen egy mezőre helyezett megkötések egy másik, **kezdetben nem látható** mező aktuálisan kijelölt értékével függenek. Ebben az esetben `coalesce()` felhasználható arra, hogy a függvényt az oldal betöltési ideje alatt szintaktikai módon érvényes legyen, miközben a felhasználó a mezővel folytatott kommunikációhoz szükséges hatást gyakorolja.

Vegye figyelembe `DropDown` , hogy ez lehetővé teszi, hogy a felhasználó több különböző típusú adatbázis közül válasszon:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Ha egy másik mező műveletét szeretné felvenni a mező aktuálisan kiválasztott értékére, használja a parancsot az `coalesce()` itt látható módon:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Erre azért van szükség, mert az `databaseType` kezdetben nem látható, ezért nem rendelkezik értékkel. Ennek hatására a teljes kifejezés kiértékelése nem megfelelő.

## <a name="conversion-functions"></a>Átalakítási függvények
Ezek a függvények a JSON-adattípusok és a kódolások közötti értékek átalakítására használhatók.

### <a name="int"></a>int
Egy egész számra konvertálja a paramétert. Ez a függvény a Number és a String típusú paramétereket támogatja.

Az alábbi példa a következőt adja vissza `1` :

```json
"[int('1')]"
```

Az alábbi példa a következőt adja vissza `2` :

```json
"[int(2.9)]"
```

### <a name="float"></a>lebegőpontos
A paramétert egy lebegőpontos pontra konvertálja. Ez a függvény a Number és a String típusú paramétereket támogatja.

Az alábbi példa a következőt adja vissza `1.0` :

```json
"[float('1.0')]"
```

Az alábbi példa a következőt adja vissza `2.9` :

```json
"[float(2.9)]"
```

### <a name="string"></a>sztring
A paramétert karakterlánccá alakítja. Ez a függvény az összes JSON-adattípus paramétereit támogatja.

Az alábbi példa a következőt adja vissza `"1"` :

```json
"[string(1)]"
```

Az alábbi példa a következőt adja vissza `"2.9"` :

```json
"[string(2.9)]"
```

Az alábbi példa a következőt adja vissza `"[1,2,3]"` :

```json
"[string([1,2,3])]"
```

Az alábbi példa a következőt adja vissza `"{"foo":"bar"}"` :

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>logikai
A paramétert logikai értékre alakítja. Ez a függvény a következő típusú paramétereket támogatja: number, string és Boolean. A JavaScript logikai értékeihez hasonlóan bármilyen érték, kivéve `0` vagy `'false'` visszaadja `true` .

Az alábbi példa a következőt adja vissza `true` :

```json
"[bool(1)]"
```

Az alábbi példa a következőt adja vissza `false` :

```json
"[bool(0)]"
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[bool(true)]"
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
A paramétert natív típusra konvertálja. Más szóval ez a függvény a inverze `string()` . Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `1` :

```json
"[parse('1')]"
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[parse('true')]"
```

Az alábbi példa a következőt adja vissza `[1,2,3]` :

```json
"[parse('[1,2,3]')]"
```

Az alábbi példa a következőt adja vissza `{"foo":"bar"}` :

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
A paramétert egy Base-64 kódolású karakterláncba kódolja. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `"Zm9vYmFy"` :

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
A paraméter dekódolása egy Base-64 kódolású karakterláncból. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `"foobar"` :

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Kódolja a paramétert egy URL-kódolású karakterláncba. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `"https%3A%2F%2Fportal.azure.com%2F"` :

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
A paraméter dekódolása egy URL-kódolású karakterláncból. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `"https://portal.azure.com/"` :

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematikai függvények
### <a name="add"></a>add
Két számot ad vissza, és visszaadja az eredményt.

Az alábbi példa a következőt adja vissza `3` :

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Sub
Kivonja a második számot az első számból, és visszaadja az eredményt.

Az alábbi példa a következőt adja vissza `1` :

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Összeszoroz két számot, és visszaadja az eredményt.

Az alábbi példa a következőt adja vissza `6` :

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Az első számot felosztja a második számra, és visszaadja az eredményt. Az eredmény mindig egész szám.

Az alábbi példa a következőt adja vissza `2` :

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Az első szám felosztása a második szám alapján, és a maradékot adja vissza.

Az alábbi példa a következőt adja vissza `0` :

```json
"[mod(6, 3)]"
```

Az alábbi példa a következőt adja vissza `2` :

```json
"[mod(6, 4)]"
```

### <a name="min"></a>p
A két szám kis részét adja vissza.

Az alábbi példa a következőt adja vissza `1` :

```json
"[min(1, 2)]"
```

### <a name="max"></a>Max
A két szám nagyobb számát adja vissza.

Az alábbi példa a következőt adja vissza `2` :

```json
"[max(1, 2)]"
```

### <a name="range"></a>tartomány
A megadott tartományon belüli, egész számokból álló sorozatot hoz létre.

Az alábbi példa a következőt adja vissza `[1,2,3]` :

```json
"[range(1, 3)]"
```

### <a name="rand"></a>Rand
Egy véletlenszerű, a megadott tartományon belüli egész számot ad vissza. Ez a függvény nem állít elő kriptográfiailag biztonságos véletlenszerű számokat.

A következő példa visszatérhet `42` :

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>padló
A legnagyobb olyan egész számot adja vissza, amely kisebb vagy egyenlő, mint a megadott szám.

Az alábbi példa a következőt adja vissza `3` :

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
A megadott számnál nagyobb vagy azzal egyenlő legnagyobb egész számot adja vissza.

Az alábbi példa a következőt adja vissza `4` :

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Dátumfüggvények
### <a name="utcnow"></a>utcNow
A helyi számítógépen az aktuális dátum és idő ISO 8601 formátumú karakterláncát adja vissza.

A következő példa visszatérhet `"1990-12-31T23:59:59.000Z"` :

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Egy egész számú másodpercet ad hozzá a megadott időbélyeghez.

Az alábbi példa a következőt adja vissza `"1991-01-01T00:00:00.000Z"` :

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Egy egész számú percet ad hozzá a megadott időbélyeghez.

Az alábbi példa a következőt adja vissza `"1991-01-01T00:00:59.000Z"` :

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
A megadott időbélyeghez tartozó egész számú órát ad hozzá.

Az alábbi példa a következőt adja vissza `"1991-01-01T00:59:59.000Z"` :

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>További lépések
* A Azure Resource Manager bevezetését lásd: [Azure Resource Manager áttekintése](../management/overview.md).

