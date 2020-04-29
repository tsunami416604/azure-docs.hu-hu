---
title: FELHASZNÁLÓIFELÜLET-definíciós függvények létrehozása
description: A Azure Managed Applications felhasználói felületi definícióinak összeállításakor használandó függvények ismertetése
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6e56c5e528a17d42a75da54158f00857a917645c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79248449"
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

A következő példa az alapismeretek lépésben megnevezett `foo` elem kimenetét adja vissza:

```json
"[basics('foo')]"
```

### <a name="steps"></a>lépések
Egy elem kimeneti értékeit adja vissza, amely a megadott lépésben van meghatározva. Az alapvető lépések lépésben szereplő elemek kimeneti értékeinek lekéréséhez használja `basics()` helyette a parancsot.

Az alábbi példa a nevű elem `bar` kimenetét adja vissza a következő lépésben: `foo`

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

Ha például a kimenet értéke `element1` if `"bar"`, akkor ez a példa a következő karakterláncot `"foobar!"`adja vissza:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>substring
A megadott karakterlánc alsztringjét adja vissza. Az alkarakterlánc a megadott indexnél kezdődik, és a megadott hosszúságú.

Az alábbi példa a `"ftw"`következőt adja vissza:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>csere
Egy olyan karakterláncot ad vissza, amelyben az aktuális karakterláncban szereplő megadott sztring összes előfordulása egy másik karakterlánccal van lecserélve.

Az alábbi példa a `"Everything is awesome!"`következőt adja vissza:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
Globálisan egyedi karakterláncot (GUID) hoz létre.

A következő példa visszatérhet `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Egy kisbetűsre konvertált karakterláncot ad vissza.

Az alábbi példa a `"foobar"`következőt adja vissza:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Egy nagybetűre konvertált karakterláncot ad vissza.

Az alábbi példa a `"FOOBAR"`következőt adja vissza:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Gyűjtési függvények
Ezek a függvények gyűjtemények, például JSON-karakterláncok, tömbök és objektumok használatával használhatók.

### <a name="contains"></a>tartalmazza a következőt:
Azt `true` adja vissza, hogy egy karakterlánc tartalmazza-e a megadott alkarakterláncot, egy tömb tartalmazza a megadott értéket, vagy egy objektum tartalmazza a megadott kulcsot.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a `true`következőt adja vissza:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
`element1` Visszaadott `[1, 2, 3]`érték. Az alábbi példa a `false`következőt adja vissza:

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

Az alábbi példa a `true`következőt adja vissza:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>hossz
Egy sztringben szereplő karakterek számát, egy tömbben lévő értékek számát, illetve egy objektum kulcsainak számát adja vissza.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a `6`következőt adja vissza:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
`element1` Visszaadott `[1, 2, 3]`érték. Az alábbi példa a `3`következőt adja vissza:

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

Az alábbi példa a `2`következőt adja vissza:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>üres
Azt `true` adja vissza, hogy a karakterlánc, tömb vagy objektum null értékű vagy üres-e.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a `true`következőt adja vissza:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
`element1` Visszaadott `[1, 2, 3]`érték. Az alábbi példa a `false`következőt adja vissza:

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

Az alábbi példa a `false`következőt adja vissza:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>4. példa: null és nem definiált
A `element1` feltételezés `null` vagy nincs meghatározva. Az alábbi példa a `true`következőt adja vissza:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>első
A megadott karakterlánc első karakterét adja vissza. a megadott tömb első értéke; vagy a megadott objektum első kulcsa és értéke.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a `"f"`következőt adja vissza:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
`element1` Visszaadott `[1, 2, 3]`érték. Az alábbi példa a `1`következőt adja vissza:

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
Az alábbi példa a `{"key1": "foobar"}`következőt adja vissza:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>utolsó
A megadott karakterlánc utolsó karakterét adja vissza, a megadott tömb utolsó értékét, vagy a megadott objektum utolsó kulcsát és értékét.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a `"r"`következőt adja vissza:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
`element1` Visszaadott `[1, 2, 3]`érték. Az alábbi példa a `2`következőt adja vissza:

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

Az alábbi példa a `{"key2": "raboof"}`következőt adja vissza:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>eltarthat
Megadott számú folytonos karaktert ad vissza a karakterlánc elejétől, a tömb elejétől megadott számú folytonos értékből, vagy az objektum elejétől megadott számú folytonos kulcsból és értékből.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a `"foo"`következőt adja vissza:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>2. példa: tömb
`element1` Visszaadott `[1, 2, 3]`érték. Az alábbi példa a `[1, 2]`következőt adja vissza:

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

Az alábbi példa a `{"key1": "foobar"}`következőt adja vissza:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>kihagyása
Megkerüli a gyűjtemény megadott számú elemét, majd visszaadja a többi elemet.

#### <a name="example-1-string"></a>1. példa: karakterlánc
Az alábbi példa a `"bar"`következőt adja vissza:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>2. példa: tömb
`element1` Visszaadott `[1, 2, 3]`érték. Az alábbi példa a `[3]`következőt adja vissza:

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
Az alábbi példa a `{"key2": "raboof"}`következőt adja vissza:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logikai függvények
Ezek a függvények feltételes használatban is használhatók. Előfordulhat, hogy egyes függvények nem támogatják az összes JSON-adattípust.

### <a name="equals"></a>egyenlő
Azt `true` adja vissza, hogy mindkét paraméter ugyanazzal a típussal és értékkel rendelkezik-e. Ez a függvény az összes JSON-adattípust támogatja.

Az alábbi példa a `true`következőt adja vissza:

```json
"[equals(0, 0)]"
```

Az alábbi példa a `true`következőt adja vissza:

```json
"[equals('foo', 'foo')]"
```

Az alábbi példa a `false`következőt adja vissza:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Azt `true` adja vissza, hogy az első paraméter szigorúan kisebb-e a második paraméternél. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

Az alábbi példa a `true`következőt adja vissza:

```json
"[less(1, 2)]"
```

Az alábbi példa a `false`következőt adja vissza:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Visszaadja `true` , ha az első paraméter kisebb vagy egyenlő, mint a második paraméter. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

Az alábbi példa a `true`következőt adja vissza:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Azt `true` adja vissza, hogy az első paraméter szigorúan nagyobb-e, mint a második paraméter. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

Az alábbi példa a `false`következőt adja vissza:

```json
"[greater(1, 2)]"
```

Az alábbi példa a `true`következőt adja vissza:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Visszaadja `true` , ha az első paraméter nagyobb vagy egyenlő, mint a második paraméter. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

Az alábbi példa a `true`következőt adja vissza:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>és
A `true` értéket adja vissza, ha az `true`összes paraméter ki van értékelve. Ez a függvény két vagy több paramétert támogat, amelyek csak logikai típusúak.

Az alábbi példa a `true`következőt adja vissza:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Az alábbi példa a `false`következőt adja vissza:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>vagy
Azt `true` adja vissza, hogy a paraméterek közül legalább az egyik `true`a következőre van-e kiértékelve:. Ez a függvény két vagy több paramétert támogat, amelyek csak logikai típusúak.

Az alábbi példa a `true`következőt adja vissza:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Az alábbi példa a `true`következőt adja vissza:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Visszaadja `true` , `false`ha a paraméter kiértékelése a következőre történik:. Ez a függvény csak logikai típusú paramétereket támogat.

Az alábbi példa a `true`következőt adja vissza:

```json
"[not(false)]"
```

Az alábbi példa a `false`következőt adja vissza:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>összefonódik
Az első nem null paraméter értékét adja vissza. Ez a függvény az összes JSON-adattípust támogatja.

A `element1` feltételezések és `element2` nincsenek meghatározva. Az alábbi példa a `"foobar"`következőt adja vissza:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Átalakítási függvények
Ezek a függvények a JSON-adattípusok és a kódolások közötti értékek átalakítására használhatók.

### <a name="int"></a>int
Egy egész számra konvertálja a paramétert. Ez a függvény a Number és a String típusú paramétereket támogatja.

Az alábbi példa a `1`következőt adja vissza:

```json
"[int('1')]"
```

Az alábbi példa a `2`következőt adja vissza:

```json
"[int(2.9)]"
```

### <a name="float"></a>lebegőpontos
A paramétert egy lebegőpontos pontra konvertálja. Ez a függvény a Number és a String típusú paramétereket támogatja.

Az alábbi példa a `1.0`következőt adja vissza:

```json
"[float('1.0')]"
```

Az alábbi példa a `2.9`következőt adja vissza:

```json
"[float(2.9)]"
```

### <a name="string"></a>sztring
A paramétert karakterlánccá alakítja. Ez a függvény az összes JSON-adattípus paramétereit támogatja.

Az alábbi példa a `"1"`következőt adja vissza:

```json
"[string(1)]"
```

Az alábbi példa a `"2.9"`következőt adja vissza:

```json
"[string(2.9)]"
```

Az alábbi példa a `"[1,2,3]"`következőt adja vissza:

```json
"[string([1,2,3])]"
```

Az alábbi példa a `"{"foo":"bar"}"`következőt adja vissza:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>logikai
A paramétert logikai értékre alakítja. Ez a függvény a következő típusú paramétereket támogatja: number, string és Boolean. A JavaScript logikai értékeihez hasonlóan bármilyen érték, kivéve `0` vagy `'false'` visszaadja `true`.

Az alábbi példa a `true`következőt adja vissza:

```json
"[bool(1)]"
```

Az alábbi példa a `false`következőt adja vissza:

```json
"[bool(0)]"
```

Az alábbi példa a `true`következőt adja vissza:

```json
"[bool(true)]"
```

Az alábbi példa a `true`következőt adja vissza:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
A paramétert natív típusra konvertálja. Más szóval ez a függvény a inverze `string()`. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a `1`következőt adja vissza:

```json
"[parse('1')]"
```

Az alábbi példa a `true`következőt adja vissza:

```json
"[parse('true')]"
```

Az alábbi példa a `[1,2,3]`következőt adja vissza:

```json
"[parse('[1,2,3]')]"
```

Az alábbi példa a `{"foo":"bar"}`következőt adja vissza:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
A paramétert egy Base-64 kódolású karakterláncba kódolja. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a `"Zm9vYmFy"`következőt adja vissza:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
A paraméter dekódolása egy Base-64 kódolású karakterláncból. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a `"foobar"`következőt adja vissza:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Kódolja a paramétert egy URL-kódolású karakterláncba. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a `"https%3A%2F%2Fportal.azure.com%2F"`következőt adja vissza:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
A paraméter dekódolása egy URL-kódolású karakterláncból. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a `"https://portal.azure.com/"`következőt adja vissza:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematikai függvények
### <a name="add"></a>add
Két számot ad vissza, és visszaadja az eredményt.

Az alábbi példa a `3`következőt adja vissza:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Sub
Kivonja a második számot az első számból, és visszaadja az eredményt.

Az alábbi példa a `1`következőt adja vissza:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Összeszoroz két számot, és visszaadja az eredményt.

Az alábbi példa a `6`következőt adja vissza:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Az első számot felosztja a második számra, és visszaadja az eredményt. Az eredmény mindig egész szám.

Az alábbi példa a `2`következőt adja vissza:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Az első szám felosztása a második szám alapján, és a maradékot adja vissza.

Az alábbi példa a `0`következőt adja vissza:

```json
"[mod(6, 3)]"
```

Az alábbi példa a `2`következőt adja vissza:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>p
A két szám kis részét adja vissza.

Az alábbi példa a `1`következőt adja vissza:

```json
"[min(1, 2)]"
```

### <a name="max"></a>Max
A két szám nagyobb számát adja vissza.

Az alábbi példa a `2`következőt adja vissza:

```json
"[max(1, 2)]"
```

### <a name="range"></a>tartomány
A megadott tartományon belüli, egész számokból álló sorozatot hoz létre.

Az alábbi példa a `[1,2,3]`következőt adja vissza:

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

Az alábbi példa a `3`következőt adja vissza:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
A megadott számnál nagyobb vagy azzal egyenlő legnagyobb egész számot adja vissza.

Az alábbi példa a `4`következőt adja vissza:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Dátumfüggvények
### <a name="utcnow"></a>utcNow
A helyi számítógépen az aktuális dátum és idő ISO 8601 formátumú karakterláncát adja vissza.

A következő példa visszatérhet `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Egy egész számú másodpercet ad hozzá a megadott időbélyeghez.

Az alábbi példa a `"1991-01-01T00:00:00.000Z"`következőt adja vissza:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Egy egész számú percet ad hozzá a megadott időbélyeghez.

Az alábbi példa a `"1991-01-01T00:00:59.000Z"`következőt adja vissza:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
A megadott időbélyeghez tartozó egész számú órát ad hozzá.

Az alábbi példa a `"1991-01-01T00:59:59.000Z"`következőt adja vissza:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>További lépések
* A Azure Resource Manager bevezetését lásd: [Azure Resource Manager áttekintése](../management/overview.md).

