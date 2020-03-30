---
title: Felhasználói felületdefiníciós függvények létrehozása
description: Az Azure felügyelt alkalmazások felhasználói felületi definícióinak összeállításakor használandó függvények ismertetése
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6e56c5e528a17d42a75da54158f00857a917645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248449"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition függvények
Ez a szakasz a CreateUiDefinition összes támogatott függvényének aláírásait tartalmazza.

Függvény használatához vegye körül a deklarációt szögletes zárójelekkel. Példa:

```json
"[function()]"
```

A karakterláncokra és egyéb függvényekre egy függvény paramétereiként lehet hivatkozni, de a karakterláncokat idézőjelek közé kell venni. Példa:

```json
"[fn1(fn2(), 'foobar')]"
```

Adott esetben a függvény kimenetének tulajdonságaira hivatkozhat a pontoperátor használatával. Példa:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Hivatkozásfüggvények
Ezek a függvények a CreateUiDefinition tulajdonságaiból vagy környezetéből származó kimenetekre hivatkozhatnak.

### <a name="basics"></a>Alapjai
Az Alapok lépésben definiált elem kimeneti értékeit adja eredményül.

A következő példa az Alapok `foo` lépésben elnevezett elem kimenetét adja vissza:

```json
"[basics('foo')]"
```

### <a name="steps"></a>lépések
A megadott lépésben definiált elem kimeneti értékeit adja eredményül. Az Alapok lépés elemeinek kimeneti értékeit `basics()` használja helyette.

A következő példa a nevű `bar` lépésben elnevezett elem kimenetét adja `foo`vissza:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Az Alapok lépésben vagy az aktuális környezetben kijelölt helyet adja eredményül.

A következő példa `"westus"`a következő térhet vissza:

```json
"[location()]"
```

## <a name="string-functions"></a>Sztringfüggvények
Ezek a függvények csak JSON karakterláncokkal használhatók.

### <a name="concat"></a>Concat
Egy vagy több karakterlánc összefűzése.

Ha például az if `element1` `"bar"`kimeneti értéke, akkor `"foobar!"`ez a példa a karakterláncot adja vissza:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>Substring
A megadott karakterlánc részkarakterláncát adja eredményül. A karakterláncrész a megadott indexnél kezdődik, és a megadott hosszúságú.

A következő `"ftw"`példa a dedikál:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>Helyettesít
Olyan karakterláncot ad vissza, amelyben az aktuális karakterláncban megadott karakterlánc összes előfordulását egy másik karakterlánc váltja fel.

A következő `"Everything is awesome!"`példa a dedikál:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>Guid
Globálisan egyedi karakterláncot (GUID) hoz létre.

A következő példa `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`a következő térhet vissza:

```json
"[guid()]"
```

### <a name="tolower"></a>lassabbra
Kisbetűssé konvertált karakterláncot ad eredményül.

A következő `"foobar"`példa a dedikál:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper (felső
Nagybetűssé konvertált karakterláncot ad eredményül.

A következő `"FOOBAR"`példa a dedikál:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Gyűjtési függvények
Ezek a függvények gyűjtemények, például JSON-karakterláncok, tömbök és objektumok számára használhatók.

### <a name="contains"></a>tartalmazza a következőt:
Akkor `true` a dúl vissza, ha egy karakterlánc a megadott részkarakterláncot tartalmazza, egy tömb a megadott értéket tartalmazza, vagy egy objektum tartalmazza a megadott kulcsot.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő `true`példa a dedikál:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tételezzük fel `element1` a visszaküldést. `[1, 2, 3]` A következő `false`példa a dedikál:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tétel: Visszáru: `element1`

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő `true`példa a dedikál:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>hossz
Egy karakterlánc ban lévő karakterek számát, a tömbben lévő értékek számát vagy az objektum billentyűinek számát adja eredményül.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő `6`példa a dedikál:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tételezzük fel `element1` a visszaküldést. `[1, 2, 3]` A következő `3`példa a dedikál:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tétel: Visszáru: `element1`

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő `2`példa a dedikál:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>üres
Akkor `true` adja vissza, ha a karakterlánc, tömb vagy objektum null értékű vagy üres.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő `true`példa a dedikál:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tételezzük fel `element1` a visszaküldést. `[1, 2, 3]` A következő `false`példa a dedikál:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tétel: Visszáru: `element1`

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő `false`példa a dedikál:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>4. példa: null és nem definiált
Tegyük fel, `element1` hogy nem `null` definiált. A következő `true`példa a dedikál:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>Első
A megadott karakterlánc első karakterét adja eredményül; a megadott tömb első értéke; vagy a megadott objektum első kulcsát és értékét.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő `"f"`példa a dedikál:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tételezzük fel `element1` a visszaküldést. `[1, 2, 3]` A következő `1`példa a dedikál:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tétel: Visszáru: `element1`

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
A következő `{"key1": "foobar"}`példa a dedikál:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>Utolsó
A megadott karakterlánc utolsó karakterét, a megadott tömb utolsó értékét vagy a megadott objektum utolsó kulcsát és értékét adja eredményül.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő `"r"`példa a dedikál:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tételezzük fel `element1` a visszaküldést. `[1, 2, 3]` A következő `2`példa a dedikál:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tétel: Visszáru: `element1`

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő `{"key2": "raboof"}`példa a dedikál:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>venni
A karakterlánc kezdetétől megadott számú összefüggő karaktert, a tömb kezdetétől megadott számú összefüggő értéket, vagy az objektum elejétől megadott számú összefüggő kulcsot és értéket ad eredményül.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő `"foo"`példa a dedikál:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tételezzük fel `element1` a visszaküldést. `[1, 2, 3]` A következő `[1, 2]`példa a dedikál:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tétel: Visszáru: `element1`

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő `{"key1": "foobar"}`példa a dedikál:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>Ugrál
Megkerüli a gyűjtemény megadott számú elemét, majd visszaadja a többi elemet.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő `"bar"`példa a dedikál:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tételezzük fel `element1` a visszaküldést. `[1, 2, 3]` A következő `[3]`példa a dedikál:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tétel: Visszáru: `element1`

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
A következő `{"key2": "raboof"}`példa a dedikál:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logikai függvények
Ezek a függvények feltételes körülmények között használhatók. Előfordulhat, hogy egyes függvények nem támogatják az összes JSON-adattípust.

### <a name="equals"></a>egyenlő
Akkor `true` a dúl vissza, ha mindkét paraméter azonos típusú és értékű. Ez a függvény az összes JSON-adattípust támogatja.

A következő `true`példa a dedikál:

```json
"[equals(0, 0)]"
```

A következő `true`példa a dedikál:

```json
"[equals('foo', 'foo')]"
```

A következő `false`példa a dedikál:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Akkor `true` adja vissza, ha az első paraméter szigorúan kisebb, mint a második paraméter. Ez a függvény csak a típusszám és a karakterlánc paramétereit támogatja.

A következő `true`példa a dedikál:

```json
"[less(1, 2)]"
```

A következő `false`példa a dedikál:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Akkor `true` adja vissza, ha az első paraméter kisebb vagy egyenlő a második paraméterrel. Ez a függvény csak a típusszám és a karakterlánc paramétereit támogatja.

A következő `true`példa a dedikál:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Akkor `true` adja vissza, ha az első paraméter szigorúan nagyobb, mint a második paraméter. Ez a függvény csak a típusszám és a karakterlánc paramétereit támogatja.

A következő `false`példa a dedikál:

```json
"[greater(1, 2)]"
```

A következő `true`példa a dedikál:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Akkor `true` adja vissza, ha az első paraméter nagyobb vagy egyenlő a második paraméterrel. Ez a függvény csak a típusszám és a karakterlánc paramétereit támogatja.

A következő `true`példa a dedikál:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>és
Akkor `true` adja vissza, ha `true`az összes paraméter kiértékelt . Ez a függvény csak logikai típusú két vagy több paramétert támogat.

A következő `true`példa a dedikál:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

A következő `false`példa a dedikál:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>vagy
Akkor `true` adja vissza, ha a paraméterek `true`közül legalább az egyik kiértékelés a hoz. Ez a függvény csak logikai típusú két vagy több paramétert támogat.

A következő `true`példa a dedikál:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

A következő `true`példa a dedikál:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Akkor `true` adja vissza, `false`ha a paraméter eredménye . Ez a függvény csak logikai típusú paramétereket támogat.

A következő `true`példa a dedikál:

```json
"[not(false)]"
```

A következő `false`példa a dedikál:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>összeolvad
Az első nem null paraméter értékét adja eredményül. Ez a függvény az összes JSON-adattípust támogatja.

Tegyük fel, `element1` és `element2` nem definiált. A következő `"foobar"`példa a dedikál:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Konverziós függvények
Ezek a függvények a JSON-adattípusok és kódolások közötti értékek konvertálására használhatók.

### <a name="int"></a>int
A paramétert egész számmá alakítja. Ez a függvény támogatja a típusszám és a karakterlánc paramétereit.

A következő `1`példa a dedikál:

```json
"[int('1')]"
```

A következő `2`példa a dedikál:

```json
"[int(2.9)]"
```

### <a name="float"></a>lebegőpontos
A paramétert lebegőpontossá alakítja. Ez a függvény támogatja a típusszám és a karakterlánc paramétereit.

A következő `1.0`példa a dedikál:

```json
"[float('1.0')]"
```

A következő `2.9`példa a dedikál:

```json
"[float(2.9)]"
```

### <a name="string"></a>sztring
A paramétert karakterláncgá alakítja. Ez a függvény az összes JSON-adattípus paramétereit támogatja.

A következő `"1"`példa a dedikál:

```json
"[string(1)]"
```

A következő `"2.9"`példa a dedikál:

```json
"[string(2.9)]"
```

A következő `"[1,2,3]"`példa a dedikál:

```json
"[string([1,2,3])]"
```

A következő `"{"foo":"bar"}"`példa a dedikál:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>Bool
A paramétert logikai értékké alakítja. Ez a függvény támogatja a típusszám, a karakterlánc és a logikai paraméter paramétereit. A JavaScript-beli logikai értékekhez `0` `'false'` hasonlóan `true`minden érték, kivéve vagy adja vissza.

A következő `true`példa a dedikál:

```json
"[bool(1)]"
```

A következő `false`példa a dedikál:

```json
"[bool(0)]"
```

A következő `true`példa a dedikál:

```json
"[bool(true)]"
```

A következő `true`példa a dedikál:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
A paramétert natív típussá alakítja. Más szóval ez a függvény `string()`a. Ez a függvény csak karakterlánctípusú paramétereket támogat.

A következő `1`példa a dedikál:

```json
"[parse('1')]"
```

A következő `true`példa a dedikál:

```json
"[parse('true')]"
```

A következő `[1,2,3]`példa a dedikál:

```json
"[parse('[1,2,3]')]"
```

A következő `{"foo":"bar"}`példa a dedikál:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>kódbase64
A paramétert egy 64-es alapkódolású karakterláncba kódolja. Ez a függvény csak karakterlánctípusú paramétereket támogat.

A következő `"Zm9vYmFy"`példa a dedikál:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>dekódbase64
Dekódolja a paramétert egy 64-es alapú kódolású karakterláncból. Ez a függvény csak karakterlánctípusú paramétereket támogat.

A következő `"foobar"`példa a dedikál:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>kódolásuriösszetevő
A paramétert URL-kódolású karakterláncba kódolja. Ez a függvény csak karakterlánctípusú paramétereket támogat.

A következő `"https%3A%2F%2Fportal.azure.com%2F"`példa a dedikál:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>dekódurikomponens
A paramétert URL-kódolású karakterláncból dekódolja. Ez a függvény csak karakterlánctípusú paramétereket támogat.

A következő `"https://portal.azure.com/"`példa a dedikál:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematikai függvények
### <a name="add"></a>add
Két számot ad hozzá, és az eredményt adja vissza.

A következő `3`példa a dedikál:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Al
Kivonja a második számot az első számból, és visszaadja az eredményt.

A következő `1`példa a dedikál:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>Mul
Két szám szorzata, és az eredményt adja eredményül.

A következő `6`példa a dedikál:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Az első számot elosztja a második számmal, és visszaadja az eredményt. Az eredmény mindig egész szám.

A következő `2`példa a dedikál:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>Mod
Az első számot elosztja a második számmal, és a maradékot adja eredményül.

A következő `0`példa a dedikál:

```json
"[mod(6, 3)]"
```

A következő `2`példa a dedikál:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>p
A két szám kis részét adja eredményül.

A következő `1`példa a dedikál:

```json
"[min(1, 2)]"
```

### <a name="max"></a>Max
A két szám közül a nagyobbat adja eredményül.

A következő `2`példa a dedikál:

```json
"[max(1, 2)]"
```

### <a name="range"></a>Tartomány
Integrálszámok sorozatát hozza létre a megadott tartományon belül.

A következő `[1,2,3]`példa a dedikál:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>Rand
A megadott tartományon belüli véletlen integrált ad eredményül. Ez a függvény nem hoz létre kriptográfiailag biztonságos véletlen számokat.

A következő példa `42`a következő térhet vissza:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>Padló
A megadott számnál kisebb vagy azzal egyenlő legnagyobb egész számot adja eredményül.

A következő `3`példa a dedikál:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil között
A megadott számnál nagyobb vagy azzal egyenlő legnagyobb egész számot adja eredményül.

A következő `4`példa a dedikál:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Dátumfüggvények
### <a name="utcnow"></a>utcNow között
A helyi számítógépen az aktuális dátum és idő ISO 8601 formátumú karakterláncát adja vissza.

A következő példa `"1990-12-31T23:59:59.000Z"`a következő térhet vissza:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
A megadott időbélyeghez teljes számú másodpercet ad hozzá.

A következő `"1991-01-01T00:00:00.000Z"`példa a dedikál:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
A megadott időbélyeghez teljes számú percet ad hozzá.

A következő `"1991-01-01T00:00:59.000Z"`példa a dedikál:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours (Órák)
Az órák teljes számát adja hozzá a megadott időbélyeghez.

A következő `"1991-01-01T00:59:59.000Z"`példa a dedikál:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>További lépések
* Az Azure Resource Manager bemutatása az [Azure Resource Manager áttekintése című témakörben található.](../management/overview.md)

