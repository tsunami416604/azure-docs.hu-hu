---
title: Telemetria processzorok (előzetes verzió) – Azure Monitor Application Insights Javához
description: Telemetria-processzorok konfigurálása a Azure Monitor Application Insights Javához
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: ba4e6b8b5e9db494ab4c0c372c2086087a2d58cb
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133174"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Telemetria processzorok (előzetes verzió) – Azure Monitor Application Insights Javához

> [!NOTE]
> Ez a funkció még mindig előzetes verzióban érhető el.

A Application Insights Java 3,0-ügynöke most már rendelkezik a telemetria-alapú adatfeldolgozáshoz szükséges képességekkel az exportálás előtt.

A telemetria processzorok néhány felhasználási esete a következő:
 * Bizalmas adatok maszkolása
 * Egyéni dimenziók feltételes hozzáadása
 * Az összesítéshez és a megjelenítéshez használt telemetria-név frissítése
 * A betöltési díjak szabályozására szolgáló kihúzási vagy szűrési tartomány attribútumai

## <a name="terminology"></a>Terminológia

Mielőtt beugorjunk a telemetria-processzorokra, fontos megérteni, hogy mi az a nyomkövetés és a felölelés.

### <a name="traces"></a>Hívásláncok

A nyomok nyomon követik egy, a-nek nevezett kérelem előrehaladását, `trace` mivel azt egy alkalmazást alkotó szolgáltatások kezelik. A kérést egy felhasználó vagy egy alkalmazás kezdeményezheti. Az a alkalmazásban az egyes munkaegységeket `trace` nevezzük `span` `trace` . a az a csomópont. A `trace` az egyetlen legfelső szintű tartományból és az összes gyermekből álló számból áll.

### <a name="span"></a>Span

A felöleli azokat az objektumokat, amelyek a kérelemben érintett egyes szolgáltatásoknak vagy összetevőknek a rendszeren keresztüli átfolyása során végzett munkát jelölik. A a `span` tartalmaz egy olyan `span context` globálisan egyedi azonosítót, amely az egyes spanok részét képező egyedi kérést jelképezi. 

Átnyúló beágyazás:

* A span neve
* Egy nem módosítható `SpanContext` , amely egyedileg azonosítja a span-t
* Szülő-span `Span` ,-vagy Null-formátumú `SpanContext`
* Egy `SpanKind` műveletet
* Kezdő időbélyeg
* Záró időbélyeg
* [`Attributes`](#attributes)
* Az időbélyeggel ellátott események listája
* A `Status` .

Általánosságban a span életciklusa a következőhöz hasonló:

* Egy szolgáltatás kérést kap. Ha létezik, a rendszer kinyeri a span-környezetet a kérések fejlécében.
* Egy új span jön létre a kinyert tartomány környezetének gyermeke; Ha nincs ilyen, létrejön egy új gyökérszintű tartomány.
* A szolgáltatás kezeli a kérelmet. A rendszer további attribútumokat és eseményeket ad hozzá a tartományhoz, amelyek hasznosak lehetnek a kérelem kontextusának megismeréséhez, például a kérést kezelő gép állomásneve, vagy az ügyfél-azonosítók.
* A szolgáltatás alösszetevői által végzett munka megjelenítéséhez új felölelő hozható létre.
* Ha a szolgáltatás távoli hívást kezdeményez egy másik szolgáltatásba, az aktuális tartomány-környezet szerializálva lesz, és a következő szolgáltatásnak továbbítja a span-környezetnek a fejlécek vagy az üzenet borítékba való beírásával.
* A szolgáltatás által végzett munka sikeresen befejeződött, vagy nem. A span állapot megfelelően van beállítva, és a span jelölése kész.

### <a name="attributes"></a>Attribútumok

`Attributes` az a-ben beágyazott nulla vagy több kulcs-érték párok listája `span` . Az attribútumnak a következő tulajdonságokkal kell rendelkeznie:

Az attribútum kulcsa, amelynek nem null és nem üres karakterláncnak kell lennie.
Az attribútum értéke, amely a következők egyike:
* Egyszerű típus: karakterlánc, logikai, dupla pontosságú lebegőpontos (IEEE 754-1985) vagy aláírt 64 bites egész szám.
* Egyszerű típusú értékek tömbje. A tömbnek homogénnek kell lennie, azaz nem tartalmazhat különböző típusú értékeket. Olyan protokollok esetében, amelyek nem támogatják natív módon a tömbök értékét, ezeket az értékeket JSON-karakterláncként kell megjeleníteni.

## <a name="supported-processors"></a>Támogatott processzorok:
 * Attribútum processzora
 * Span processzor

## <a name="to-get-started"></a>Első lépések

Hozzon létre egy nevű konfigurációs fájlt `applicationinsights.json` , és helyezze ugyanabba a könyvtárba `applicationinsights-agent-***.jar` , a következő sablonnal.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>Belefoglalási/kizárási felölelés

Az attribútum-feldolgozó és a span processzor elérhetővé teszi a beállítást, amely meghatározza, hogy a rendszer milyen tulajdonságokat adjon meg a megfelelő tartományhoz, hogy megállapítsa, van-e belefoglalva vagy kizárva a telemetria processzorból. A beállítás konfigurálásához a `include` és/vagy a (z) és/vagy legalább egy, illetve az `exclude` `matchType` egyik `spanNames` `attributes` szükséges. A belefoglalási/kizárási konfiguráció több megadott feltételt is támogat. Az összes megadott feltételnek igaz értékűnek kell lennie, ha egyezés történik. 

**Kötelező mező**: 
* `matchType` meghatározza, hogy a `spanNames` rendszer hogyan értelmezze az elemeket és a `attributes` tömböket. A lehetséges értékek: `regexp` és `strict`. 

Nem **kötelező mezők**: 
* `spanNames` legalább egy elemnek meg kell egyeznie. 
* `attributes` meghatározza az egyeztetni kívánt attribútumok listáját. Ezeknek az attribútumoknak pontosan egyezniük kell egymással.

> [!NOTE]
> Ha mindkettő `include` és `exclude` a meg van adva, a tulajdonságok a `include` Tulajdonságok előtt lesznek bejelölve `exclude` .

#### <a name="sample-usage"></a>Példa a használatra

```json

"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "redact_trace",
          "value": "false"
        }
      ]
    },
    "actions": [
      {
        "key": "credit_card",
        "action": "delete"
      },
      {
        "key": "duplicate_key",
        "action": "delete"
      }
    ]
  }
]
```
További tudnivalókat a [telemetria Processor példák](./java-standalone-telemetry-processors-examples.md) dokumentációjában talál.

## <a name="attribute-processor"></a>Attribútum processzora 

Az attribútumok feldolgozó a span attribútumait módosítja. Lehetőség van arra is, hogy belefoglalja vagy kizárja a felölelő képességeket. A konfigurációs fájlban megadott sorrendben végrehajtott műveletek listáját veszi figyelembe. A támogatott műveletek a következők:

### `insert`

Egy olyan új attribútum beszúrása, amelyben a kulcs még nem létezik.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      },
    ]
  }
]
```
A `insert` művelethez a következők szükségesek
  * `key`
  * az egyik `value` vagy `fromAttribute`
  * `action`:`insert`

### `update`

Egy olyan attribútum frissítése, amelyben a kulcs létezik

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      },
    ]
  }
]
```
A `update` művelethez a következők szükségesek
  * `key`
  * az egyik `value` vagy `fromAttribute`
  * `action`:`update`


### `delete` 

Attribútum törlése egy tartományból

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      },
    ]
  }
]
```
A `delete` művelethez a következők szükségesek
  * `key`
  * `action`: `delete`

### `hash`

Kivonatok (SHA1) meglévő attribútumérték

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      },
    ]
  }
]
```
A `hash` művelethez a következők szükségesek
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Ez a funkció csak az 3.0.1-es és újabb verzióiban érhető el

Kinyeri az értékeket a szabályban megadott, a bemeneti kulcstól a reguláris kifejezési szabály használatával. Ha a célként megadott kulcs már létezik, a rendszer felülbírálja. Ehhez hasonlóan viselkedik a [](#extract-attributes-from-span-name) `toAttributes` meglévő attribútummal rendelkező span Processor beállításhoz a forrásként.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      },
    ]
  }
]
```
A `extract` művelethez a következők szükségesek
* `key`
* `pattern`
* `action` : `extract`

További tudnivalókat a [telemetria Processor példák](./java-standalone-telemetry-processors-examples.md) dokumentációjában talál.

## <a name="span-processors"></a>Span processzorok

A span processzor a span neve alapján módosítja a span tartomány nevét vagy attribútumait. Lehetőség van arra is, hogy belefoglalja vagy kizárja a felölelő képességeket.

### <a name="name-a-span"></a>Egy span neve

A név szakasz részeként a következő beállítás szükséges:

* `fromAttributes`: A kulcsok attribútumának értéke a konfigurációban megadott sorrendben új név létrehozására szolgál. Az összes attribútum kulcsát meg kell adni abban a tartományon belül, amelynek át kell neveznie a processzort.

A következő beállítás opcionálisan konfigurálható:

* `separator`: A rendszer a megadott karakterláncot használja az értékek felosztásához.
> [!NOTE]
> Ha az Átnevezés attól függ, hogy az attribútumok processzora milyen attribútumokat módosít, győződjön meg arról, hogy a csővezeték-specifikációban az attribútumok processzora után a span processzor van megadva.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-span-name"></a>Attribútumok kinyerése a tartomány nevéből

Lefoglalja a reguláris kifejezések listáját, hogy egyezzen a tartomány nevével, és kinyerje az attribútumokat az alkifejezések alapján. A szakaszban meg kell adni `toAttributes` .

A következő beállítások szükségesek:

`rules` : Azoknak a szabályoknak a listája, amelyekkel kinyerheti az attribútumok értékeit a tartomány neve alapján. A span Name értékeit a kinyert attribútumok neve váltja fel. A lista minden szabálya regex minta sztring. A span nevet a rendszer a regexben ellenőrzi. Ha a regex egyezik, a regex összes megnevezett alkifejezése attribútumként lesz kibontva, és hozzá lesz adva a tartományhoz. Az egyes alkifejezések neve az attribútumérték és az alkifejezések egyezési részévé válik. Az span Name egyező részét a kinyert attribútum neve váltja fel. Ha az attribútumok már léteznek a span-ban, a rendszer felülírja őket. A folyamat a megadott sorrendben minden szabálynál megismétlődik. Minden további szabály az előző szabály feldolgozását követően kimenetként működik.

```json

"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="list-of-attributes"></a>Attribútumok listája

Az alábbiakban felsorolunk néhány gyakori, a telemetria processzorokban használható span-attribútumot.

### <a name="http-spans"></a>HTTP-Átívelés

| Attribútum  | Típus | Description | 
|---|---|---|
| `http.method` | sztring | HTTP-kérelem módszere.|
| `http.url` | sztring | Teljes HTTP-kérelem URL-címe az űrlapon `scheme://host[:port]/path?query[#fragment]` . Általában a töredék nem kerül át HTTP-n keresztül, de ha ismert, mégis szerepelnie kell bennük.|
| `http.status_code` | szám | [Http-válasz állapotának kódja](https://tools.ietf.org/html/rfc7231#section-6)|
| `http.flavor` | sztring | A használt HTTP-protokoll típusa |
| `http.user_agent` | sztring | Az ügyfél által eljuttatott [http User-Agent](https://tools.ietf.org/html/rfc7231#section-5.5.3) fejléc értéke. |

### <a name="jdbc-spans"></a>JDBC-átívelő

| Attribútum  | Típus | Description  |
|---|---|---|
| `db.system` | sztring | Az adatbázis-kezelő rendszer (adatbázisok kezelése) által használt termék azonosítója. |
| `db.connection_string` | sztring | Az adatbázishoz való kapcsolódáshoz használt kapcsolati karakterlánc. Javasoljuk, hogy távolítsa el a beágyazott hitelesítő adatokat.|
| `db.user` | sztring | Az adatbázis eléréséhez használt Felhasználónév. |
| `db.name` | sztring | Ez az attribútum az elérni kívánt adatbázis nevének jelentésére szolgál. Az adatbázist átváltó parancsok esetén ezt a célként megadott adatbázisra kell beállítani (még akkor is, ha a parancs sikertelen).|
| `db.statement` | sztring | Az adatbázis-utasítás végrehajtása folyamatban van.|