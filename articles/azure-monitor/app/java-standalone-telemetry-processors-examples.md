---
title: Telemetria processors példák – Azure Monitor Application Insights Javához
description: Példák telemetria-processzorok bemutatása Azure Monitor Application Insights Javához
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 9b29c9611359c97c4097ad0b90ee2673bb28f37c
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696312"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>Telemetria processors példák – Azure Monitor Application Insights Javához

## <a name="includeexclude-samples"></a>Minták belefoglalása/kizárása

### <a name="1-include-spans"></a>1. belefoglalási felölelés

A következő mutatja be, hogy az attribútumok processzora milyen felöleli. A processzor nem dolgozza fel az összes olyan átnyúlt, amely nem felel meg a tulajdonságoknak.

A következő feltételeknek kell teljesülniük a egyezéshez:
* A span névnek a következőnek kell lennie: "spana" vagy "spanB" 

Az alábbiak a belefoglalási tulajdonságokat és a processzor-műveleteket is megegyeznek.
* Span1 neve: "spana" attribútumok: {env: dev, test_request: 123, credit_card: 1234}
* Span2 neve: "spanB" attribútumok: {env: dev, test_request: false}
* Span3 neve: "spana" attribútumok: {env: 1, test_request: dev, credit_card: 1234}

A következő span nem egyezik a belefoglalási tulajdonságokkal, és a processzor műveletei nem lesznek alkalmazva.
* Span4 neve: "spanC" attribútumok: {env: dev, test_request: false}

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
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="2-exclude-spans"></a>2. kihagyás kizárása

Az alábbi részekben látható, hogy az attribútumok processzora nem terjed ki. A processzor nem dolgozza fel az összes olyan átnyúlt, amely megfelel a tulajdonságoknak.

A következő feltételeknek kell teljesülniük a egyezéshez:
* A span névnek a következőnek kell lennie: "spana" vagy "spanB" 

A következők olyan átnyúlnak, amelyek megfelelnek a kizárási tulajdonságoknak, és a processzor műveletei nem alkalmazhatók.
* Span1 neve: "spana" attribútumok: {env: dev, test_request: 123, credit_card: 1234}
* Span2 neve: "spanB" attribútumok: {env: dev, test_request: false}
* Span3 neve: "spana" attribútumok: {env: 1, test_request: dev, credit_card: 1234}

A következő tartomány nem felel meg a kizárási tulajdonságokkal és a processzor műveleteivel.
* Span4 neve: "spanC" attribútumok: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="3-excludemulti-spans"></a>3. a ExcludeMulti átível

Az alábbi részekben látható, hogy az attribútumok processzora nem terjed ki. A processzor nem dolgozza fel az összes olyan átnyúlt, amely megfelel a tulajdonságoknak.

A következő feltételeknek kell teljesülniük a egyezéshez:
* Egy attribútumnak ("env", "dev") léteznie kell egy egyezés tartományában.
* Ha van egy "test_request" kulccsal rendelkező attribútum a span-ben, akkor egyezés van.

A következők olyan átnyúlnak, amelyek megfelelnek a kizárási tulajdonságoknak, és a processzor műveletei nem alkalmazhatók.
* Span1 neve: "spanB" attribútumok: {env: dev, test_request: 123, credit_card: 1234}
* Span2 neve: "spana" attribútumok: {env: dev, test_request: false}

A következő tartomány nem felel meg a kizárási tulajdonságokkal és a processzor műveleteivel.
* Span3 neve: "spanB" attribútumok: {env: 1, test_request: dev, credit_card: 1234}
* Span4 neve: "spanC" attribútumok: {env: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="4-selective-processing"></a>4. szelektív feldolgozás

Az alábbi cikk azt mutatja be, hogy a rendszer hogyan alkalmazza a processzorra kiterjedő span tulajdonságokat. A `include` Tulajdonságok azt mondják, hogy melyeket kell belefoglalni, és a `exclude` Tulajdonságok további kiszűrési felskálázásokat tartalmaznak, amelyek nem lesznek feldolgozva.

Az alábbi konfigurációval az alábbi, a tulajdonságok és a processzor műveletekkel egyező találatok vannak alkalmazva:

* Span1 neve: "spanB" attribútumok: {env: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Span2 neve: "spana" attribútumok: {env: előkészítés, test_request: false, redact_trace: true}

A következő találatok nem egyeznek meg a tulajdonságok belefoglalása és a processzor műveleteivel:
* Span3 neve: "spanB" attribútumok: {env: Production, test_request: true, credit_card: 1234, redact_trace: false}
* Span4 neve: "spanC" attribútumok: {env: dev, test_request: false}

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
  }
}
```
## <a name="attribute-processor-samples"></a>Attribútum-feldolgozó minták

### <a name="insert"></a>Beszúrás

A következő új attribútumot szúr be {"attribute1": "attributeValue1"}, hogy a "attribute1" kulcs nem létezik.

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
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>Beszúrás másik kulcsból

Az alábbi érték a "anotherkey" attribútum értékét használja a (z) {"newKey": "anotherkey" attribútum értékének a megtalálása érdekében, hogy a "newKey" kulcs nem létezik. Ha a "anotherkey" attribútum nem létezik, a rendszer nem szúr be új attribútumot a felölelő értékekre.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>Frissítés

A következő frissíti az attribútumot {"db. Secret": "kitakart"} értékre, és frissíti a "Boo" attribútumot a "foo" attribútum értéke alapján. A "Boo" attribútum nélküli Átívelés nem változik.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>Törlés

Az alábbi "credit_card" kulccsal rendelkező attribútum törlését mutatja be.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>Kivonat

Az alábbi táblázat a kivonatoló létező attribútumok értékeit mutatja be.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
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

### <a name="extract"></a>Kinyerés

Az alábbi példa bemutatja, hogyan lehet a regex használatával új attribútumokat létrehozni egy másik attribútum értéke alapján.
Például: http. URL = ' http://example.com/path?queryParam1=value1 , queryParam2 = érték2 ', a következő attribútumok lesznek beszúrva:
* httpProtocol: http
* httpDomain: example.com
* httpPath: elérési út
* httpQueryParams: queryParam1 = érték1, queryParam2 = érték2
* a http. URL-cím értéke nem változik.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
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


## <a name="span-processor-samples"></a>Kiterjedésű processzor mintái

### <a name="name-a-span"></a>Egy span neve

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

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>Attribútumok kinyerése a span neve és a Belefoglalás és kizárás

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