---
title: Webes alkalmazás egyéni tűzfalszabály Azure bejárati ajtó
description: Ismerje meg, hogyan használhatja a webes alkalmazás tűzfal (WAF) egyéni szabályok a webes alkalmazások védelme a rosszindulatú támadások ellen.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud;tyao
ms.openlocfilehash: 744c6fb9235c9daa2d5239ef9fd13679db943650
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783967"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Webalkalmazási tűzfal az Azure bejárati ajtajának egyéni szabályok
Azure webalkalmazási tűzfal (WAF) bejárati ajtajának szolgáltatással lehetővé teszi, hogy ki férhet hozzá a webes alkalmazások határoz meg a feltételek alapján. Egyéni WAF szabály áll egy prioritást, szabálytípus, egyezési feltételei és a egy műveletet. Egyéni szabályok két típusa van: szabályok és az utolsó korlát szabályok értékelése. Egy match szabály irányítja a hozzáférést alapján a megfelelő feltételek, amíg az arány korlát szabály irányítja a hozzáférést a megfelelő feltételek és a bejövő kérelmek díjszabás alapján. Előfordulhat, hogy a kiértékelt megakadályozása érdekében egy egyéni szabály letiltása, de a konfiguráció továbbra is folyamatosan. Ez a cikk ismerteti a http-paraméterek alapuló egyezés szabályok.

## <a name="priority-match-conditions-and-action-types"></a>Művelettípusok, prioritás és egyezési feltételei
Szabályozhatja a hozzáférést egy egyéni WAf-szabályt, amely meghatározza egy prioritást, szabálytípus, egyezési feltételei és a egy műveletet. 

- **Prioritás:** egyedi egész szám, amely leírja a WAF-szabálykészlet kiértékelés sorrendjét. Az alacsonyabb szabályok értékeli ki a magasabb értékkel szabályok előtt

- **Művelet:** határozza meg, hogyan irányítsa a kérést, ha egyezik a WAF-szabály. Közül választhat az alábbi műveletek a alkalmazni egy kérelem megegyezik egy egyéni szabályt.

    - *Lehetővé teszi* -WAF továbbítja a quest a háttérkiszolgálóhoz, WAF-naplók és kilépés bejelentkezik egy bejegyzés.
    - *Blokk* -kérelem le van tiltva, a WAF ügyfél választ küld a kérést a háttéralkalmazás nélkül. WAF egy bejegyzést a WAF-naplókban naplózza.
    - *Napló* -WAF-naplókban a WAF-bejegyzés naplózza, és továbbra is a következő szabály kiértékelése.
    - *Átirányítási* -WAF átirányítja a kérést a megadott URI-ra, naplózza a bejegyzés a WAF-naplókban és kilép.

- **Az állapotot meg:** egyezés változó, egy operátorból határozza meg, majd az értékét. Minden szabály több egyezési feltételei is tartalmazhat. Egyeztetési feltételt alapján előfordulhat, hogy az alábbi *felel meg a változók*:
    - RemoteAddr (client IP)
    - requestMethod
    - A lekérdezési karakterlánc
    - PostArgs
    - requestUri
    - RequestHeader
    - RequestBody

- **Üzemeltető:** lista a következőket tartalmazza:
    - Bármely: alapértelmezett műveletet határozza meg, ha nincs szabály sem felel meg vannak gyakran használják. Bármilyen egyezés van all operátort.
    - IPMatch: IP-korlátozást RemoteAddr változó definiálása
    - GeoMatch: földrajzi szűrés RemoteAddr változó definiálása
    - Egyenlő
    - Contains
    - LessThan: méret megkötés
    - GreaterThan: méret megkötés
    - LessThanOrEqual: méret megkötés
    - GreaterThanOrEqual: méret megkötés
    - BeginsWith
     - endsWith

Beállíthat *negálandó* feltétel igaz, ha a feltétel eredménye negated kell lennie.

*Érték egyezik* legpontosabban egyező értékek listáját határozza meg.
A támogatott értékek például HTTP-kérési metódust:
- GET
- POST
- PUT
- HEAD
- DELETE
- ZÁROLÁS
- ZÁROLÁS FELOLDÁSA
- PROFIL
- BEÁLLÍTÁSOK
- PROPFIND
- PROPPATCH
- MKCOL
- MÁSOLÁS
- ÁTHELYEZÉS

## <a name="examples"></a>Példák

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>WAF egyéni szabályok példa http-paraméterek alapján

Íme egy példa, amely két egyezési feltételei az egyéni szabály konfigurációját jeleníti meg. Kérelmek adott helyről történő hivatkozó által meghatározott, és lekérdezési karakterlánc nem tartalmaz "password".

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
Látható egy példa konfiguráció látható "PUT" metódust blokkolásához látható:

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

### <a name="size-constraint"></a>Méret-megkötés

Előfordulhat, hogy létrehozhat egy egyéni szabályt, amely meghatározza egy bejövő kérelem részeként méretének korlátja. Például alábbi szabály blokkolja egy URL-címet, amely 100 karakternél hosszabb.

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
- Ismerje meg [webalkalmazási tűzfal](waf-overview.md)
- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.

