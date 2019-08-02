---
title: Webalkalmazási tűzfal egyéni Azure-szabálya
description: Ismerje meg, hogy a webalkalmazási tűzfal (WAF) egyéni szabályai hogyan védik a webalkalmazásokat rosszindulatú támadásokkal szemben.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 02b335de7f105d768168d5f798ec9109136d7430
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846266"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Egyéni szabályok webalkalmazási tűzfalhoz az Azure bejárati ajtaján
Az Azure webalkalmazási tűzfal (WAF) és a bejárati ajtó szolgáltatás lehetővé teszi a webalkalmazásokhoz való hozzáférés szabályozását az Ön által meghatározott feltételek alapján. Az egyéni WAF-szabályok prioritási számból, szabály típusból, egyeztetési feltételekből és műveletből állnak. Az egyéni szabályoknak két típusa van: a szabályok és a díjszabási szabályok egyeztetése. Az egyeztetési szabály a megfelelő feltételek alapján szabályozza a hozzáférést, míg a díjszabási szabály a megfelelő feltételek és a beérkező kérések díjszabása alapján szabályozza a hozzáférést. Előfordulhat, hogy letilt egy egyéni szabályt, hogy megakadályozza annak kiértékelését, de továbbra is megtartja a konfigurációt. Ez a cikk a http-paramétereken alapuló egyezési szabályokat tárgyalja.

## <a name="priority-match-conditions-and-action-types"></a>Prioritás, egyeztetési feltételek és műveleti típusok
A hozzáférést egy olyan egyéni WAf-szabállyal szabályozhatja, amely meghatározza a prioritási számot, a szabályt, az egyeztetési feltételeket és a műveletet. 

- **Prioritás:** egy egyedi egész szám, amely LEÍRJA a WAF-szabályok kiértékelésének sorrendjét. Az alacsonyabb értékkel rendelkező szabályok kiértékelése a magasabb értékekkel rendelkező szabályok előtt történik

- **Művelet:** azt határozza meg, hogyan történjen a kérések továbbítása egy WAF-szabály egyeztetése esetén. Az alábbi műveletek egyikét választhatja, ha egy kérelem megfelel egy egyéni szabálynak.

    - Az *Allow* -WAF továbbítja a küldetést a háttérbe, naplóz egy BEJEGYZÉST a WAF-naplókban, és kilép.
    - ** A letiltási kérelem le van tiltva, a WAF a kérésnek a háttér felé való továbbítása nélkül küld választ az ügyfélnek. A WAF naplóz egy bejegyzést a WAF-naplókban.
    - A *log* -WAF naplózza a WAF-naplókban lévő bejegyzést, és folytatja a következő szabály kiértékelését.
    - *Átirányítás* – a WAF átirányítja a kérést egy megadott URI-ra, naplóz egy BEJEGYZÉST a WAF-naplókban, és kilép.

- **Egyeztetési feltétel:** meghatározza a Match változót, az operátort és a megfeleltetési értéket. Az egyes szabályok több egyezési feltételt is tartalmazhatnak. Az egyeztetési feltételt az alábbi *egyezési változók*alapján lehet megadni:
    - RemoteAddr (ügyfél IP-címe)
    - RequestMethod
    - A lekérdezési karakterlánc
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **Operátor:** a lista a következőket tartalmazza:
    - Bármelyik: általában az alapértelmezett művelet definiálására használatos, ha nem egyeznek a szabályok. Minden az összes operátornak felel meg.
    - IPMatch: IP-korlátozás definiálása a RemoteAddr változóhoz
    - GeoMatch: a RemoteAddr változó földrajzi szűrésének meghatározása
    - Egyenlő
    - tartalmaz
    - LessThan: méret korlátozás
    - GreaterThan: méret korlátozás
    - LessThanOrEqual: méret korlátozás
    - GreaterThanOrEqual: méret korlátozás
    - BeginsWith
     - endsWith

Megadhatja, hogy a *tagadás* feltétel igaz legyen, ha egy feltétel eredményét meg kell tagadni.

Az *egyeztetési érték* határozza meg a lehetséges egyezési értékek listáját.
A HTTP-kérések támogatott metódusának értékei a következők:
- GET
- POST
- PUT
- HEAD
- DELETE
- LOCK
- ZÁROLÁSÁNAK FELOLDÁSÁHOZ
- PROFIL
- BEÁLLÍTÁSOK
- PROPFIND
- PROPPATCH
- MKCOL
- MÁSOLJA
- ÁTHELYEZÉSE

## <a name="examples"></a>Példák

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>WAF – példa http-paraméterek alapján

Az alábbi példa egy olyan egyéni szabály konfigurációját mutatja be, amely két egyeztetési feltétellel rendelkezik. A kérések egy adott helyről érkeznek, a referrer által meghatározott módon, és a lekérdezési karakterlánc nem tartalmazza a "password" karaktert.

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
A "PUT" metódust blokkoló konfiguráció például az alábbi módon jelenik meg:

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Méret korlátozás

Létrehozhat egy egyéni szabályt, amely a beérkező kérések egy részében meghatározza a méret megkötését. Az alábbi szabály például egy 100 karakternél hosszabb URL-címet blokkol.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>További lépések
- Tudnivalók a [](waf-overview.md) webalkalmazási tűzfalról
- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.

