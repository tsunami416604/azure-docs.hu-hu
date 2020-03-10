---
title: FELHASZNÁLÓIFELÜLET-definíciós függvények létrehozása
description: A Azure Managed Applications felhasználói felületi definícióinak összeállításakor használandó függvények ismertetése
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6e56c5e528a17d42a75da54158f00857a917645c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380858"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition függvények
Ez a szakasz a CreateUiDefinition összes támogatott funkciójának aláírásait tartalmazza.

A függvények használatához szögletes zárójelek közé kell belefoglalni a deklarációt. Például:

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

Az alábbi példa a `foo` nevű elem kimenetét adja vissza az alapvető lépések lépésben:

```json
"[basics('foo')]"
```

### <a name="steps"></a>lépések
Egy elem kimeneti értékeit adja vissza, amely a megadott lépésben van meghatározva. Az alapvető lépések lépésben szereplő elemek kimeneti értékeinek lekéréséhez használja a `basics()` helyet.

A következő példa a `bar` nevű elem kimenetét adja vissza a `foo`nevű lépésben:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Az alapismeretek lépésben vagy az aktuális környezetben kiválasztott helyet adja vissza.

A következő példa visszatérhet `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Sztringfüggvények
Ezek a függvények csak JSON-karakterláncokkal használhatók.

### <a name="concat"></a>concat
Egy vagy több karakterlánc összefűzése.

Ha például a `element1` kimeneti értéke `"bar"`, akkor a példa a következő karakterláncot adja vissza `"foobar!"`:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>substring
A megadott karakterlánc alsztringjét adja vissza. Az alkarakterlánc a megadott indexnél kezdődik, és a megadott hosszúságú.

A következő példa a `"ftw"`t adja vissza:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>csere
Egy olyan karakterláncot ad vissza, amelyben az aktuális karakterláncban szereplő megadott sztring összes előfordulása egy másik karakterlánccal van lecserélve.

A következő példa a `"Everything is awesome!"`t adja vissza:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>GUID
Globálisan egyedi karakterláncot (GUID) hoz létre.

A következő példa visszatérhet `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Egy kisbetűsre konvertált karakterláncot ad vissza.

A következő példa a `"foobar"`t adja vissza:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Egy nagybetűre konvertált karakterláncot ad vissza.

A következő példa a `"FOOBAR"`t adja vissza:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Gyűjtési függvények
Ezek a függvények gyűjtemények, például JSON-karakterláncok, tömbök és objektumok használatával használhatók.

### <a name="contains"></a>tartalmaz
`true` ad vissza, ha egy karakterlánc tartalmazza a megadott alkarakterláncot, egy tömb tartalmazza a megadott értéket, vagy egy objektum tartalmazza a megadott kulcsot.

#### <a name="example-1-string"></a>1\. példa: karakterlánc
A következő példa a `true`t adja vissza:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>2\. példa: tömb
Tegyük fel, hogy `element1` visszaadja `[1, 2, 3]`. A következő példa a `false`t adja vissza:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>3\. példa: objektum
`element1` visszaadott érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa a `true`t adja vissza:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Egy sztringben szereplő karakterek számát, egy tömbben lévő értékek számát, illetve egy objektum kulcsainak számát adja vissza.

#### <a name="example-1-string"></a>1\. példa: karakterlánc
A következő példa a `6`t adja vissza:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>2\. példa: tömb
Tegyük fel, hogy `element1` visszaadja `[1, 2, 3]`. A következő példa a `3`t adja vissza:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3\. példa: objektum
`element1` visszaadott érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa a `2`t adja vissza:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>empty
`true` visszaadása, ha a sztring, tömb vagy objektum null értékű vagy üres.

#### <a name="example-1-string"></a>1\. példa: karakterlánc
A következő példa a `true`t adja vissza:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>2\. példa: tömb
Tegyük fel, hogy `element1` visszaadja `[1, 2, 3]`. A következő példa a `false`t adja vissza:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3\. példa: objektum
`element1` visszaadott érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa a `false`t adja vissza:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>4\. példa: null és nem definiált
Tegyük fel, hogy `element1` `null` vagy nincs meghatározva. A következő példa a `true`t adja vissza:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>első
A megadott karakterlánc első karakterét adja vissza. a megadott tömb első értéke; vagy a megadott objektum első kulcsa és értéke.

#### <a name="example-1-string"></a>1\. példa: karakterlánc
A következő példa a `"f"`t adja vissza:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>2\. példa: tömb
Tegyük fel, hogy `element1` visszaadja `[1, 2, 3]`. A következő példa a `1`t adja vissza:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3\. példa: objektum
`element1` visszaadott érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
A következő példa a `{"key1": "foobar"}`t adja vissza:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>utolsó
A megadott karakterlánc utolsó karakterét adja vissza, a megadott tömb utolsó értékét, vagy a megadott objektum utolsó kulcsát és értékét.

#### <a name="example-1-string"></a>1\. példa: karakterlánc
A következő példa a `"r"`t adja vissza:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>2\. példa: tömb
Tegyük fel, hogy `element1` visszaadja `[1, 2, 3]`. A következő példa a `2`t adja vissza:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3\. példa: objektum
`element1` visszaadott érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa a `{"key2": "raboof"}`t adja vissza:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>take
Megadott számú folytonos karaktert ad vissza a karakterlánc elejétől, a tömb elejétől megadott számú folytonos értékből, vagy az objektum elejétől megadott számú folytonos kulcsból és értékből.

#### <a name="example-1-string"></a>1\. példa: karakterlánc
A következő példa a `"foo"`t adja vissza:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>2\. példa: tömb
Tegyük fel, hogy `element1` visszaadja `[1, 2, 3]`. A következő példa a `[1, 2]`t adja vissza:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>3\. példa: objektum
`element1` visszaadott érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa a `{"key1": "foobar"}`t adja vissza:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>kihagyása
Megkerüli a gyűjtemény megadott számú elemét, majd visszaadja a többi elemet.

#### <a name="example-1-string"></a>1\. példa: karakterlánc
A következő példa a `"bar"`t adja vissza:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>2\. példa: tömb
Tegyük fel, hogy `element1` visszaadja `[1, 2, 3]`. A következő példa a `[3]`t adja vissza:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>3\. példa: objektum
`element1` visszaadott érték:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
A következő példa a `{"key2": "raboof"}`t adja vissza:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logikai függvények
Ezek a függvények feltételes használatban is használhatók. Előfordulhat, hogy egyes függvények nem támogatják az összes JSON-adattípust.

### <a name="equals"></a>egyenlő
`true` értéket ad vissza, ha mindkét paraméter ugyanolyan típusú és értékű. Ez a függvény az összes JSON-adattípust támogatja.

A következő példa a `true`t adja vissza:

```json
"[equals(0, 0)]"
```

A következő példa a `true`t adja vissza:

```json
"[equals('foo', 'foo')]"
```

A következő példa a `false`t adja vissza:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>kisebb
`true` visszaadása, ha az első paraméter szigorúan kisebb a második paraméternél. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

A következő példa a `true`t adja vissza:

```json
"[less(1, 2)]"
```

A következő példa a `false`t adja vissza:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
`true` visszaadása, ha az első paraméter kisebb vagy egyenlő, mint a második paraméter. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

A következő példa a `true`t adja vissza:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>nagyobb
`true` visszaadása, ha az első paraméter szigorúan nagyobb a második paraméternél. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

A következő példa a `false`t adja vissza:

```json
"[greater(1, 2)]"
```

A következő példa a `true`t adja vissza:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
`true` visszaadása, ha az első paraméter nagyobb vagy egyenlő, mint a második paraméter. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

A következő példa a `true`t adja vissza:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>és
`true` visszaadása, ha az összes paraméter kiértékelése `true`. Ez a függvény két vagy több paramétert támogat, amelyek csak logikai típusúak.

A következő példa a `true`t adja vissza:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

A következő példa a `false`t adja vissza:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>vagy
`true` értéket ad vissza, ha a paraméterek közül legalább az egyik kiértékelése `true`. Ez a függvény két vagy több paramétert támogat, amelyek csak logikai típusúak.

A következő példa a `true`t adja vissza:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

A következő példa a `true`t adja vissza:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>nem
`true` visszaadása, ha a paraméter kiértékelése `false`. Ez a függvény csak logikai típusú paramétereket támogat.

A következő példa a `true`t adja vissza:

```json
"[not(false)]"
```

A következő példa a `false`t adja vissza:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>összefonódik
Az első nem null paraméter értékét adja vissza. Ez a függvény az összes JSON-adattípust támogatja.

Tegyük fel, hogy `element1` és `element2` nincsenek meghatározva. A következő példa a `"foobar"`t adja vissza:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Átalakítási függvények
Ezek a függvények a JSON-adattípusok és a kódolások közötti értékek átalakítására használhatók.

### <a name="int"></a>int
Egy egész számra konvertálja a paramétert. Ez a függvény a Number és a String típusú paramétereket támogatja.

A következő példa a `1`t adja vissza:

```json
"[int('1')]"
```

A következő példa a `2`t adja vissza:

```json
"[int(2.9)]"
```

### <a name="float"></a>lebegőpontos
A paramétert egy lebegőpontos pontra konvertálja. Ez a függvény a Number és a String típusú paramétereket támogatja.

A következő példa a `1.0`t adja vissza:

```json
"[float('1.0')]"
```

A következő példa a `2.9`t adja vissza:

```json
"[float(2.9)]"
```

### <a name="string"></a>sztring
A paramétert karakterlánccá alakítja. Ez a függvény az összes JSON-adattípus paramétereit támogatja.

A következő példa a `"1"`t adja vissza:

```json
"[string(1)]"
```

A következő példa a `"2.9"`t adja vissza:

```json
"[string(2.9)]"
```

A következő példa a `"[1,2,3]"`t adja vissza:

```json
"[string([1,2,3])]"
```

A következő példa a `"{"foo":"bar"}"`t adja vissza:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
A paramétert logikai értékre alakítja. Ez a függvény a következő típusú paramétereket támogatja: number, string és Boolean. A JavaScript logikai értékeihez hasonlóan a `0` és a `'false'` kivételével bármely érték `true`ad vissza.

A következő példa a `true`t adja vissza:

```json
"[bool(1)]"
```

A következő példa a `false`t adja vissza:

```json
"[bool(0)]"
```

A következő példa a `true`t adja vissza:

```json
"[bool(true)]"
```

A következő példa a `true`t adja vissza:

```json
"[bool('true')]"
```

### <a name="parse"></a>elemezni
A paramétert natív típusra konvertálja. Más szóval ez a függvény a `string()`inverze. Ez a függvény csak karakterlánc típusú paramétereket támogat.

A következő példa a `1`t adja vissza:

```json
"[parse('1')]"
```

A következő példa a `true`t adja vissza:

```json
"[parse('true')]"
```

A következő példa a `[1,2,3]`t adja vissza:

```json
"[parse('[1,2,3]')]"
```

A következő példa a `{"foo":"bar"}`t adja vissza:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
A paramétert egy Base-64 kódolású karakterláncba kódolja. Ez a függvény csak karakterlánc típusú paramétereket támogat.

A következő példa a `"Zm9vYmFy"`t adja vissza:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
A paraméter dekódolása egy Base-64 kódolású karakterláncból. Ez a függvény csak karakterlánc típusú paramétereket támogat.

A következő példa a `"foobar"`t adja vissza:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Kódolja a paramétert egy URL-kódolású karakterláncba. Ez a függvény csak karakterlánc típusú paramétereket támogat.

A következő példa a `"https%3A%2F%2Fportal.azure.com%2F"`t adja vissza:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
A paraméter dekódolása egy URL-kódolású karakterláncból. Ez a függvény csak karakterlánc típusú paramétereket támogat.

A következő példa a `"https://portal.azure.com/"`t adja vissza:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematikai függvények
### <a name="add"></a>add
Két számot ad vissza, és visszaadja az eredményt.

A következő példa a `3`t adja vissza:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Kivonja a második számot az első számból, és visszaadja az eredményt.

A következő példa a `1`t adja vissza:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Összeszoroz két számot, és visszaadja az eredményt.

A következő példa a `6`t adja vissza:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Az első számot felosztja a második számra, és visszaadja az eredményt. Az eredmény mindig egész szám.

A következő példa a `2`t adja vissza:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Az első szám felosztása a második szám alapján, és a maradékot adja vissza.

A következő példa a `0`t adja vissza:

```json
"[mod(6, 3)]"
```

A következő példa a `2`t adja vissza:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min.
A két szám kis részét adja vissza.

A következő példa a `1`t adja vissza:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max.
A két szám nagyobb számát adja vissza.

A következő példa a `2`t adja vissza:

```json
"[max(1, 2)]"
```

### <a name="range"></a>tartomány
A megadott tartományon belüli, egész számokból álló sorozatot hoz létre.

A következő példa a `[1,2,3]`t adja vissza:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>Rand
Egy véletlenszerű, a megadott tartományon belüli egész számot ad vissza. Ez a függvény nem állít elő kriptográfiailag biztonságos véletlenszerű számokat.

A következő példa visszatérhet `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>padló
A legnagyobb olyan egész számot adja vissza, amely kisebb vagy egyenlő, mint a megadott szám.

A következő példa a `3`t adja vissza:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
A megadott számnál nagyobb vagy azzal egyenlő legnagyobb egész számot adja vissza.

A következő példa a `4`t adja vissza:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Dátum függvények
### <a name="utcnow"></a>utcNow
A helyi számítógépen az aktuális dátum és idő ISO 8601 formátumú karakterláncát adja vissza.

A következő példa visszatérhet `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Egy egész számú másodpercet ad hozzá a megadott időbélyeghez.

A következő példa a `"1991-01-01T00:00:00.000Z"`t adja vissza:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Egy egész számú percet ad hozzá a megadott időbélyeghez.

A következő példa a `"1991-01-01T00:00:59.000Z"`t adja vissza:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
A megadott időbélyeghez tartozó egész számú órát ad hozzá.

A következő példa a `"1991-01-01T00:59:59.000Z"`t adja vissza:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Következő lépések
* A Azure Resource Manager bevezetését lásd: [Azure Resource Manager áttekintése](../management/overview.md).

