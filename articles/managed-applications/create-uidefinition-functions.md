---
title: "Az Azure által felügyelt alkalmazás, hozzon létre felhasználói felület definition funkciók |} Microsoft Docs"
description: "Az Azure által felügyelt alkalmazások felhasználói felületi definíciók konstrukciója során használandó funkcióit ismerteti"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: dcf570ca4bdc8eacb7e4d7a8ff0011c8e07b7a40
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition funkciók
Ez a szakasz az összes támogatott funkcióit egy CreateUiDefinition aláírásai.

A funkció használatához helyezze a szögletes zárójelek deklaráció. Példa:

```json
"[function()]"
```

Karakterláncok és egyéb funkciók függvény paramétereinek lehet hivatkozni, de karakterláncok kell körül, szimpla idézőjelben. Példa:

```json
"[fn1(fn2(), 'foobar')]"
```

Ahol lehetséges, melyeket referenciaként használhat, a kimenet egy függvény tulajdonságok a pont operátor használatával. Példa:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Hivatkozási funkciók
Ezek a funkciók való hivatkozáshoz kimenetek a Tulajdonságok vagy egy CreateUiDefinition kontextusában használható.

### <a name="basics"></a>alapvető tudnivalók
Az alapok lépésben megadott elem kimeneti értékeit adja vissza.

A következő példa a nevű elem a kimenetet visszaadja `foo` az alapvető lépésben:

```json
"[basics('foo')]"
```

### <a name="steps"></a>lépések
Egy elem az adott lépéssel meghatározott kimeneti értékeit adja vissza. A kimeneti értékek közül az alapvető lépésben, amelyet `basics()` helyette.

A következő példa a nevű elem a kimenetet visszaadja `bar` nevű lépésben `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Az alapok lépés vagy a jelenlegi környezet kijelölt helyét adja meg.

Az alábbi példa visszaadhatja `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Karakterlánc
Ezek a függvények csak JSON karakterláncok használható.

### <a name="concat"></a>Concat
Egy vagy több karakterlánc fűzi össze.

Például ha a kimeneti értéke `element1` Ha `"bar"`, akkor ebben a példában a karakterláncot ad vissza, `"foobar!"`:

```json
"[concat('foo', steps('step1').element1), '!']"
```

### <a name="substring"></a>Substring
A megadott karakterlánc részét adja vissza. A substring megadott indexű elindul, és a megadott időtartam.

A következő példa `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>cserélje le
Egy karakterláncot, amelyben a jelenlegi karakterláncban megadott karakterlánc összes előfordulásának helyére egy másik karakterláncot ad vissza.

A következő példa `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>GUID
Karakterláncot hoz létre globálisan egyedi (GUID).

Az alábbi példa visszaadhatja `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Egy kisbetűssé konvertált karakterláncot ad vissza.

A következő példa `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Nagybetűssé konvertált karakterláncot ad vissza.

A következő példa `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Gyűjtemény-funkciók
Ezek a funkciók gyűjtemények, például a JSON-karakterláncok, tömbök és objektumok használható.

### <a name="contains"></a>tartalmazza
Beolvasása `true` Ha egy karakterláncot tartalmazza a megadott, egy tömb a megadott értéket tartalmaz, vagy az objektum tartalmazza a megadott kulcs.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel `element1` adja vissza `[1, 2, 3]`. A következő példa `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>Hossza
Egy karakterlánc, tömbben értékek száma vagy az objektum kulcsainak száma a karakterek számát adja vissza.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel `element1` adja vissza `[1, 2, 3]`. A következő példa `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>üres
Beolvasása `true` esetén a karakterláncot, a tömb vagy objektum null értékű vagy üres.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel `element1` adja vissza `[1, 2, 3]`. A következő példa `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>4. példa: null, és nincs definiálva
Tegyük fel `element1` van `null` vagy nincs megadva. A következő példa `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>első
Visszaadja a megadott karakterlánc; az első karakter első érték a megadott tömb; vagy az első kulcsot és értéket a megadott objektum.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel `element1` adja vissza `[1, 2, 3]`. A következő példa `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
A következő példa `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>utolsó
A megadott karakterlánc, a legutóbbi értéket a megadott tömb, vagy a legutóbbi kulcs és az értéke a megadott objektum utolsó karaktert adja vissza.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel `element1` adja vissza `[1, 2, 3]`. A következő példa `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>hajtsa végre a megfelelő
Összefüggő karakterből karakterlánc adott számú, megadott számú folytonos értékek elejéről. a tömb vagy összefüggő kulcsok és értékek elejéről. az objektum a megadott számú adja vissza.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel `element1` adja vissza `[1, 2, 3]`. A következő példa `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>Kihagyása
A megadott számú elemet egy gyűjtemény megkerüli, és a fennmaradó elemeket adja majd vissza.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel `element1` adja vissza `[1, 2, 3]`. A következő példa `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
A következő példa `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logikai funkciók
Ezek a funkciók conditionals használható. Egyes funkciókat esetleg nem támogatja az összes JSON-adattípus.

### <a name="equals"></a>egyenlő
Beolvasása `true` Ha mindkét paraméter azonos típusa és értéke. Ez a függvény minden JSON adattípusokat támogat.

A következő példa `true`:

```json
"[equals(0, 0)]"
```

A következő példa `true`:

```json
"[equals('foo', 'foo')]"
```

A következő példa `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>kevesebb
Beolvasása `true` esetén az első paraméter szigorúan kevesebb, mint a második paraméter. Ez a függvény csak a típus számát és a karakterlánc paramétereket támogatja.

A következő példa `true`:

```json
"[less(1, 2)]"
```

A következő példa `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Beolvasása `true` kisebb vagy egyenlő, mint a második paraméter az első paraméter esetén. Ez a függvény csak a típus számát és a karakterlánc paramétereket támogatja.

A következő példa `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>nagyobb
Beolvasása `true` szigorúan nagyobb, mint a második paraméter az első paraméter esetén. Ez a függvény csak a típus számát és a karakterlánc paramétereket támogatja.

A következő példa `false`:

```json
"[greater(1, 2)]"
```

A következő példa `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Beolvasása `true` az első paraméter nagyobb vagy egyenlő a második paraméter esetén. Ez a függvény csak a típus számát és a karakterlánc paramétereket támogatja.

A következő példa `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>és
Beolvasása `true` Ha a paraméterek értékelhetők `true`. Ez a függvény csak logikai típusú két vagy több paramétereket támogatja.

A következő példa `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

A következő példa `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>vagy
Beolvasása `true` Ha a paraméterek közül legalább egy értékelődik ki `true`. Ez a függvény csak logikai típusú két vagy több paramétereket támogatja.

A következő példa `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

A következő példa `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>nem
Beolvasása `true` Ha a paraméter értéke `false`. Ez a függvény csak logikai típusú paramétereket támogatja.

A következő példa `true`:

```json
"[not(false)]"
```

A következő példa `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>Egyesítés
Az első nem üres paraméter értékét adja vissza. Ez a függvény minden JSON adattípusokat támogat.

Tegyük fel `element1` és `element2` nincs definiálva. A következő példa `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Átalakítás funkciók
Ezek a funkciók alakítható át JSON-adattípusok és kódolások használható.

### <a name="int"></a>int
A paraméter konvertál egy egész számot. Ez a funkció támogatja a paraméterek száma és a karakterlánc.

A következő példa `1`:

```json
"[int('1')]"
```

A következő példa `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>Lebegőpontos
A paraméter egy lebegőpontos alakítja át. Ez a funkció támogatja a paraméterek száma és a karakterlánc.

A következő példa `1.0`:

```json
"[float('1.0')]"
```

A következő példa `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>Karakterlánc
Konvertálja a paraméter karakterlánccá. Ez a függvény minden JSON adattípusú paramétereket támogatja.

A következő példa `"1"`:

```json
"[string(1)]"
```

A következő példa `"2.9"`:

```json
"[string(2.9)]"
```

A következő példa `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

A következő példa `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>logikai érték
A paraméter konvertálása logikai érték. Ez a funkció támogatja a paraméterek száma, a karakterlánc és a logikai. A JavaScript logikai hasonló, bármelyik értéke kivéve `0` vagy `'false'` adja vissza `true`.

A következő példa `true`:

```json
"[bool(1)]"
```

A következő példa `false`:

```json
"[bool(0)]"
```

A következő példa `true`:

```json
"[bool(true)]"
```

A következő példa `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>elemzése
Konvertálja a paraméter natív típusa. Más szóval ez a függvény megkülönbözteti a inverzét `string()`. Ez a függvény csak a karakterlánc típusú paramétereket támogatja.

A következő példa `1`:

```json
"[parse('1')]"
```

A következő példa `true`:

```json
"[parse('true')]"
```

A következő példa `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

A következő példa `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
A paraméter base-64 kódolású karakterlánc kódolja. Ez a függvény csak a karakterlánc típusú paramétereket támogatja.

A következő példa `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
A paraméter egy base-64 kódolású karakterlánc dekódol. Ez a függvény csak a karakterlánc típusú paramétereket támogatja.

A következő példa `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
A paraméter egy URL-kódolású karakterlánc kódolja. Ez a függvény csak a karakterlánc típusú paramétereket támogatja.

A következő példa `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
A paraméter egy URL-kódolású karakterlánc dekódol. Ez a függvény csak a karakterlánc típusú paramétereket támogatja.

A következő példa `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematikai függvények
### <a name="add"></a>Hozzáadása
Két számot ad, és visszaadja az eredményt.

A következő példa `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Sub
A második szám az első szám levonja, és az eredményt adja vissza.

A következő példa `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>MUL számú
Két szám szorozza meg, és visszaadja az eredményt.

A következő példa `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>DIV
A második szám szerint az első szám osztja, és visszaadja az eredményt. Eredménye mindig egy egész számot.

A következő példa `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>MOD
A második szám szerint az első szám osztja, és a maradékot adja vissza.

A következő példa `0`:

```json
"[mod(6, 3)]"
```

A következő példa `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>perc
A kis a két számot adja vissza.

A következő példa `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>maximális
A két szám nagyobb értéket ad vissza.

A következő példa `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>tartomány
A megadott tartományban egész számok sorozata állít elő.

A következő példa `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>VÉL
A megadott tartományban szerves véletlenszerű számot ad vissza. Ez a funkció nem hoz létre olyan titkosítással biztonságos véletlenszerű számból.

Az alábbi példa visszaadhatja `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>Emelet
A legnagyobb egész számot ad vissza kisebb vagy egyenlő, mint a megadott szám.

A következő példa `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
A legnagyobb egész számot ad vissza, nagyobb vagy egyenlő a megadott számára korlátozva.

A következő példa `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Dátum-funkciók
### <a name="utcnow"></a>utcNow
A helyi számítógépen a jelenlegi dátum és idő ISO 8601 formátumú karakterláncot ad vissza.

Az alábbi példa visszaadhatja `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>masodpercekHozzaadasa
Hozzáadja a megadott időbélyeg egész számú másodperc.

A következő példa `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Hozzáadja a megadott időbélyeg egész számú percben.

A következő példa `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
A megadott időbélyeg ad hozzá az órát egész szám.

A következő példa `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Következő lépések
* Lásd a Bevezetés az Azure Resource Manager [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).

