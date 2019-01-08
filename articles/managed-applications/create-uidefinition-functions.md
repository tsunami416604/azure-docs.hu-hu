---
title: Az Azure által felügyelt alkalmazás létrehozása a felhasználói Felületet definiáló függvények |} A Microsoft Docs
description: A funkciók használata az Azure Managed Applications Felhasználóifelület-definíciók kapcsolatot létesítő ismerteti
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 80fd593eecf189d516a8c9d7ef2a94ec9f23fc39
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063794"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition funkciók
Ez a szakasz tartalmazza az aláírások, a CreateUiDefinition támogatott függvények.

A funkció használatához tegyük a nyilatkozat szögletes zárójelek. Példa:

```json
"[function()]"
```

Karakterláncok és más függvényekkel függvény paraméterek lehet hivatkozni, de karakterláncokat kell körül, szimpla idézőjelben. Példa:

```json
"[fn1(fn2(), 'foobar')]"
```

Ahol lehetséges, a függvény kimenetének tulajdonságait a pont operátor használatával hivatkozhat. Példa:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Hivatkozási függvények
Ezek a függvények segítségével kimenetek hivatkozáshoz a Tulajdonságok vagy egy CreateUiDefinition kontextusában.

### <a name="basics"></a>Alapvető tudnivalók
Az alapvető beállítások lépésben meghatározott elem a kimeneti értékeket adja vissza.

Az alábbi példa kimenete nevű elem `foo` az alapokat lépésben:

```json
"[basics('foo')]"
```

### <a name="steps"></a>lépések
A megadott lépésben meghatározott elem a kimeneti értékeket adja vissza. A kimeneti értékeket elemek az alapokat lépésben, amelyet `basics()` helyette.

Az alábbi példa kimenete nevű elem `bar` a nevű lépésben `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Az alapvető beállítások lépés vagy az aktuális környezet kiválasztott helyét adja vissza.

Az alábbi példa vissza `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Sztringfüggvények
Ezek a függvények csak a JSON-sztringek használható.

### <a name="concat"></a>Concat
Egy vagy több karakterláncokat fűz össze.

Például ha a kimeneti értékét `element1` Ha `"bar"`, akkor ebben a példában a karakterláncot ad vissza, `"foobar!"`:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>karakterláncrészlet
A megadott karakterlánc a karakterláncrészt adja vissza. A substring a megadott indexpozíciónál elindul, és a megadott karakterből áll.

A következő példa eredménye `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>cserélje le
Adja vissza, amelyben a megadott karakterlánc, a jelenlegi karakterlánc összes előfordulásának egy másik karakterláncra cseréli.

A következő példa eredménye `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>GUID azonosítója
Létrehoz egy globálisan egyedi karakterlánc (GUID).

Az alábbi példa vissza `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Egy kisbetűssé konvertált karakterláncot ad vissza.

A következő példa eredménye `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Nagybetűssé konvertált karakterláncot ad vissza.

A következő példa eredménye `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Gyűjtemény-funkciók
Ezek a függvények gyűjtemények, például a JSON-sztringek, tömbök, és objektumokat is használható.

### <a name="contains"></a>tartalmazza a következőt:
Értéket ad vissza `true` Ha egy karakterlánc tartalmaz a megadott karakterláncrészlet, egy tömböt tartalmaz a megadott érték, vagy az objektum tartalmazza a megadott kulcs.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa eredménye `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel, `element1` adja vissza `[1, 2, 3]`. A következő példa eredménye `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel, `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa eredménye `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>Hossza
Egy karakterlánc, a tömbben értékek száma vagy egy objektumot a kulcsok száma a karakterek számát adja vissza.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa eredménye `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel, `element1` adja vissza `[1, 2, 3]`. A következő példa eredménye `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel, `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa eredménye `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>üres
Értéket ad vissza `true` , ha a karakterlánc, tömb vagy objektum null értékű vagy üres.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa eredménye `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel, `element1` adja vissza `[1, 2, 3]`. A következő példa eredménye `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel, `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa eredménye `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>4. példa: null, és nincs definiálva
Tegyük fel, `element1` van `null` vagy nincs definiálva. A következő példa eredménye `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>első
A megadott karakterlánc; az első karaktert adja vissza a megadott tömb; az első érték vagy az első kulcs-érték a megadott objektum.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa eredménye `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel, `element1` adja vissza `[1, 2, 3]`. A következő példa eredménye `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel, `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
A következő példa eredménye `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>utolsó
Az utolsó karakter a megadott karakterlánc, az utolsó a megadott tömbben, vagy a legutóbbi kulcs értékét és a megadott objektum értékét adja vissza.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa eredménye `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel, `element1` adja vissza `[1, 2, 3]`. A következő példa eredménye `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel, `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa eredménye `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>hajtsa végre a megfelelő
A megadott számú karakterlánc összefüggő karakterből, a kezdetektől a tömb folytonos értékek megadott számú vagy összefüggő kulcsok és értékek kezdete és az objektum megadott számú adja vissza.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa eredménye `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel, `element1` adja vissza `[1, 2, 3]`. A következő példa eredménye `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel, `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

A következő példa eredménye `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>kihagyás
A megadott számú elem a gyűjteményben lévő megkerüli, és ezután a többi elemeit adja vissza.

#### <a name="example-1-string"></a>1. példa: karakterlánc
A következő példa eredménye `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>2. példa: tömb
Tegyük fel, `element1` adja vissza `[1, 2, 3]`. A következő példa eredménye `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>3. példa: objektum
Tegyük fel, `element1` adja vissza:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
A következő példa eredménye `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logikai függvények
Ezek a függvények elágaztatását is használható. Néhány funkció nem támogatja a JSON-adatok minden típus.

### <a name="equals"></a>egyenlő
Értéket ad vissza `true` Ha mindkét paraméter típusa és értéke azonos. Ez a függvény minden JSON-adattípusokat támogat.

A következő példa eredménye `true`:

```json
"[equals(0, 0)]"
```

A következő példa eredménye `true`:

```json
"[equals('foo', 'foo')]"
```

A következő példa eredménye `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>kevesebb
Értéket ad vissza `true` esetén az első paraméterként szigorúan kevesebb, mint a második paraméter. Ez a funkció csak a típus szám és karakterlánc paramétereket támogatja.

A következő példa eredménye `true`:

```json
"[less(1, 2)]"
```

A következő példa eredménye `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Értéket ad vissza `true` kisebb vagy egyenlő, mint a második paraméterként az első paraméter esetén. Ez a funkció csak a típus szám és karakterlánc paramétereket támogatja.

A következő példa eredménye `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>nagyobb
Értéket ad vissza `true` szigorúan nagyobb, mint a második paraméterként az első paraméter esetén. Ez a funkció csak a típus szám és karakterlánc paramétereket támogatja.

A következő példa eredménye `false`:

```json
"[greater(1, 2)]"
```

A következő példa eredménye `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Értéket ad vissza `true` Ha nagyobb vagy egyenlő, második paraméterként az első paraméterként. Ez a funkció csak a típus szám és karakterlánc paramétereket támogatja.

A következő példa eredménye `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>és
Értéket ad vissza `true` , ha a paraméterek kiértékelése `true`. Ez a funkció kizárólag logikai típusú két vagy több paraméter támogatja.

A következő példa eredménye `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

A következő példa eredménye `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>vagy
Értéket ad vissza `true` ha legalább egy paraméter `true`. Ez a funkció kizárólag logikai típusú két vagy több paraméter támogatja.

A következő példa eredménye `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

A következő példa eredménye `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>nem
Értéket ad vissza `true` Ha a paraméter `false`. Ez a funkció kizárólag logikai típusú paramétereket támogatja.

A következő példa eredménye `true`:

```json
"[not(false)]"
```

A következő példa eredménye `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>Coalesce
Az első nem üres paraméter értékét adja vissza. Ez a függvény minden JSON-adattípusokat támogat.

Tegyük fel, `element1` és `element2` nincs definiálva. A következő példa eredménye `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Konverziós függvények
Ezek a függvények segítségével konvertálja a JSON-adattípusok és kódolásokat közötti értékeket.

### <a name="int"></a>int
A paraméter alakít egy egész számot. Ez a függvény és karakterlánc típusú paramétereket támogatja.

A következő példa eredménye `1`:

```json
"[int('1')]"
```

A következő példa eredménye `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>lebegőpontos
A paraméter egy lebegőpontos alakítja át. Ez a függvény és karakterlánc típusú paramétereket támogatja.

A következő példa eredménye `1.0`:

```json
"[float('1.0')]"
```

A következő példa eredménye `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>sztring
Egy karakterlánc alakítja át a paramétert. Ez a függvény minden JSON-adattípusú paramétereket támogatja.

A következő példa eredménye `"1"`:

```json
"[string(1)]"
```

A következő példa eredménye `"2.9"`:

```json
"[string(2.9)]"
```

A következő példa eredménye `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

A következő példa eredménye `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>Logikai
A paraméter alakítja át egy logikai érték. Ez a függvény típusa szám, karakterlánc és logikai paramétereket támogatja. Hasonló a logikai értékek a JavaScript, bármelyik értéke kivételével `0` vagy `'false'` adja vissza `true`.

A következő példa eredménye `true`:

```json
"[bool(1)]"
```

A következő példa eredménye `false`:

```json
"[bool(0)]"
```

A következő példa eredménye `true`:

```json
"[bool(true)]"
```

A következő példa eredménye `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>elemzése
Nativní typ alakítja át a paramétert. Más szóval ez a funkció csak a inverzét `string()`. Ez a funkció csak a karakterlánc típusú paramétereket támogatja.

A következő példa eredménye `1`:

```json
"[parse('1')]"
```

A következő példa eredménye `true`:

```json
"[parse('true')]"
```

A következő példa eredménye `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

A következő példa eredménye `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
A paraméter egy base-64 kódolású karakterláncot kódol. Ez a funkció csak a karakterlánc típusú paramétereket támogatja.

A következő példa eredménye `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
A paraméter a base-64 kódolású karakterlánc dekódolása. Ez a funkció csak a karakterlánc típusú paramétereket támogatja.

A következő példa eredménye `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Kódol egy URL-kódolású karakterlánc paraméter. Ez a funkció csak a karakterlánc típusú paramétereket támogatja.

A következő példa eredménye `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
A paraméter egy URL-kódolású karakterlánc dekódolása. Ez a funkció csak a karakterlánc típusú paramétereket támogatja.

A következő példa eredménye `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematikai függvények
### <a name="add"></a>add
Két számot ad össze, és visszaadja az eredményt.

A következő példa eredménye `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Sub
A második szám az első szám az levonja, és az eredményt adja vissza.

A következő példa eredménye `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>MUL számú
Összeszoroz két számot, és visszaadja az eredményt.

A következő példa eredménye `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>DIV
A második szám szerint az első szám hányadosát adja vissza az eredményt. Az eredmény, mindig egy egész számot.

A következő példa eredménye `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>MOD
A második szám szerint az első szám hányadosát adja vissza a maradékot.

A következő példa eredménye `0`:

```json
"[mod(6, 3)]"
```

A következő példa eredménye `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>perc
A két szám a kis adja vissza.

A következő példa eredménye `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max.
A két szám nagyobb értéket ad vissza.

A következő példa eredménye `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>Címtartomány
A megadott tartományban egész számok sorozata állít elő.

A következő példa eredménye `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
A megadott tartományban szerves véletlenszerű számot ad vissza. Ez a függvény nem ad titkosítási szempontból biztonságos véletlenszerű számból.

Az alábbi példa vissza `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>Emelet
Visszaadja a legnagyobb egész szám kisebb vagy egyenlő a megadott szám.

A következő példa eredménye `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
A legnagyobb egész számot ad vissza, nagyobb vagy egyenlő a megadott szám.

A következő példa eredménye `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Dátumfüggvények
### <a name="utcnow"></a>utcNow
ISO 8601 formátumban az aktuális dátum és idő karakterláncot ad vissza a helyi számítógépen.

Az alábbi példa vissza `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>masodpercekHozzaadasa
A megadott időbélyeg ad hozzá egy szerves másodpercek száma.

A következő példa eredménye `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
A megadott időbélyeg ad hozzá egy szerves percek száma.

A következő példa eredménye `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
A megadott időbélyeg ad hozzá egy szerves órák száma.

A következő példa eredménye `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>További lépések
* Bevezetés az Azure Resource Managerhez, lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).

