---
title: Telemetria processzorok (előzetes verzió) – Azure Monitor Application Insights Java
description: Telemetria processzorok Azure Monitor Application Insights Javához
ms.topic: conceptual
ms.date: 10/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 316243e130345ce1b009e065cf9d8250c501ee84
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601033"
---
# <a name="telemetry-processors-preview-for-azure-monitor-application-insights-java"></a>Telemetria processzorok (előzetes verzió) Azure Monitor Application Insights Javához

> [!NOTE]
> Ez a funkció még mindig előzetes verzióban érhető el.

A Application Insights Java 3,0-ügynöke most már rendelkezik a telemetria-alapú adatfeldolgozáshoz szükséges képességekkel az exportálás előtt.

### <a name="some-use-cases"></a>Néhány felhasználási eset:
 * Bizalmas adatok maszkolása
 * Egyéni dimenziók feltételes hozzáadása
 * Az összesítéshez és a megjelenítéshez használt telemetria-név frissítése

### <a name="supported-processors"></a>Támogatott processzorok:
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

Az attribútum processzora és a tartományon kívüli processzor lehetővé teszi, hogy a megfelelő tartományhoz tartozó tulajdonságok készletét adja meg, hogy meghatározza, hogy a rendszer belefoglalja-e a span-t, vagy kizárják a processzorból. A beállítás konfigurálásához a `include` és/vagy a (z) és/vagy legalább egy, illetve az `exclude` `matchType` egyik `spanNames` `attributes` szükséges. A belefoglalási/kizárási konfiguráció több megadott feltételt is támogat. Az összes megadott feltételnek igaz értékűnek kell lennie, ha egyezés történik. 

**Kötelező mező**: 
* `matchType` meghatározza, hogy a `spanNames` rendszer hogyan értelmezze az elemeket és a `attributes` tömböket. A lehetséges értékek: `regexp` és `strict`. 

Nem **kötelező mezők**: 
* `spanNames` legalább egy elemnek meg kell egyeznie. 
* `attributes` meghatározza az egyeztetni kívánt attribútumok listáját. Ezeknek az attribútumoknak pontosan egyezniük kell egymással.

> [!NOTE]
> Ha mindkettő `include` és `exclude` a meg van adva, a tulajdonságok a `include` Tulajdonságok előtt lesznek bejelölve `exclude` .

#### <a name="sample-usage"></a>Minta használata

Az alábbi cikk azt mutatja be, hogy a rendszer hogyan alkalmazza a processzorra kiterjedő span tulajdonságokat. A `include` Tulajdonságok azt mondják, hogy melyeket kell belefoglalni, és a `exclude` Tulajdonságok további kiszűrési felskálázásokat tartalmaznak, amelyek nem lesznek feldolgozva.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "svcA",
            "svcB"
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
  }
}
```

A fenti konfigurációval a következő találatok érvényesek a tulajdonságok és a processzor műveleteire:

* Span1 neve: "svcB" attribútumok: {env: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}

* Span2 neve: "svcA" attribútumok: {env: előkészítés, test_request: false, redact_trace: true}

A következő találatok nem egyeznek meg a tulajdonságok belefoglalása és a processzor műveleteivel:

* Span3 neve: "svcB" attribútumok: {env: Production, test_request: true, credit_card: 1234, redact_trace: false}

* Span4 neve: "svcC" attribútumok: {env: dev, test_request: false}

## <a name="attribute-processor"></a>Attribútum processzora 

Az attribútumok feldolgozó a span attribútumait módosítja. Lehetőség van arra is, hogy belefoglalja vagy kizárja a felölelő képességeket.
A konfigurációs fájlban megadott sorrendben végrehajtott műveletek listáját veszi figyelembe. A támogatott műveletek a következők:

* `insert` : Beszúr egy új attribútumot azokon a felöleli, ahol a kulcs még nem létezik
* `update` : Frissíti egy attribútumot a felölelő helyen, ahol a kulcs létezik
* `delete` : Attribútum törlése egy tartományból
* `hash`   : Kivonatok (SHA1) meglévő attribútumérték

A műveletek `insert` és `update`
* `key` kötelező
* `value`vagy az egyik `fromAttribute` szükséges
* A `action` használata kötelező.

A `delete` művelethez
* `key` kötelező
* `action`: `delete` kötelező.

A `hash` művelethez
* `key` kötelező
* `action` : `hash` kötelező.

A műveletek listája összeállítható olyan gazdag forgatókönyvek létrehozásához, mint például a visszatöltési attribútum, az értékek új kulcsra másolása, a bizalmas információk kivonása.

#### <a name="sample-usage"></a>Minta használata

Az alábbi példa bemutatja a kulcsok/értékek átadását a következőre:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

Az alábbi példa azt mutatja be, hogy a processzor úgy van konfigurálva, hogy csak a létező kulcsokat frissítse egy attribútumban:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

Az alábbi példa bemutatja, hogyan lehet feldolgozni a regexp-mintázatnak megfelelő span névvel rendelkező átnyúló folyamatokat.
Ez a processzor eltávolítja a "token" attribútumot, és a "password" attribútumot eltorzítja az elterjedések között, ahol a span neve megegyezik az "Auth" értékkel \* és ha a span neve nem egyezik a "login" értékkel \* .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

## <a name="span-processors"></a>Span processzorok

A span processzor a span neve alapján módosítja a span tartomány nevét vagy attribútumait. Lehetőség van arra is, hogy belefoglalja vagy kizárja a felölelő képességeket.

### <a name="name-a-span"></a>Egy span neve

A név szakasz részeként a következő beállítás szükséges:

* `fromAttributes`: A kulcsok attribútumának értéke a konfigurációban megadott sorrendben új név létrehozására szolgál. Az összes attribútum kulcsát meg kell adni abban a tartományon belül, amelynek át kell neveznie a processzort.

A következő beállítás opcionálisan konfigurálható:

* `separator`: A rendszer a megadott karakterláncot használja az értékek felosztásához.
> [!NOTE]
> Ha az Átnevezés attól függ, hogy az attribútumok processzora milyen attribútumokat módosít, győződjön meg arról, hogy a csővezeték-specifikációban az attribútumok processzora után a span processzor van megadva.

#### <a name="sample-usage"></a>Minta használata

A következő példa a "db. SVC", a "művelet" és az "id" attribútum értékeit adja meg a span új nevét (ebben a sorrendben), a "::" értékkel elválasztva.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>Attribútumok kinyerése a tartomány nevéből

Lefoglalja a reguláris kifejezések listáját, hogy egyezzen a tartomány nevével, és kinyerje az attribútumokat az alkifejezések alapján. A szakaszban meg kell adni `toAttributes` .

A következő beállítások szükségesek:

`rules` : Azoknak a szabályoknak a listája, amelyekkel kinyerheti az attribútumok értékeit a tartomány neve alapján. A span Name értékeit a kinyert attribútumok neve váltja fel. A lista minden szabálya regex minta sztring. A span nevet a rendszer a regexben ellenőrzi. Ha a regex egyezik, a regex összes megnevezett alkifejezése attribútumként lesz kibontva, és hozzá lesz adva a tartományhoz. Az egyes alkifejezések neve az attribútumérték és az alkifejezések egyezési részévé válik. Az span Name egyező részét a kinyert attribútum neve váltja fel. Ha az attribútumok már léteznek a span-ban, a rendszer felülírja őket. A folyamat a megadott sorrendben minden szabálynál megismétlődik. Minden további szabály az előző szabály feldolgozását követően kimenetként működik.

#### <a name="sample-usage"></a>Minta használata

Tegyük fel, hogy a bemeneti span neve/API/v1/Document/12345678/Update. A következő eredmények a kimeneti span névben való alkalmazása a/api/v1/document/{documentId}/update új "documentId" = "12345678" attribútumot ad hozzá a tartományhoz.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

A következő mutatja be, hogy átnevezi a span nevet a (z) {operation_website} értékre, és hozzáadja a (z) {Key: operation_website, Value: oldSpanName} attribútumot, ha a span a következő tulajdonságokkal rendelkezik:
- A span neve "/" karakterláncot tartalmaz a sztringben.
- A span neve nem "adományozó/változás".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```