---
title: Webalkalmazás tűzfalának egyéni szabálya az Azure Bejárati ajtajához
description: Megtudhatja, hogy miként használhatja a webalkalmazásokat a rosszindulatú támadásokkal szembenvédő egyéni szabályokat.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475824"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Egyéni szabályok az Azure Bejárati ajtóval rendelkező webalkalmazás-tűzfalhoz

Az Azure Web Application Firewall (WAF) frontdoor nal lehetővé teszi a webes alkalmazásokhoz való hozzáférés szabályozását a megadott feltételek alapján. Az egyéni WAF-szabály prioritási számból, szabálytípusból, egyezési feltételekből és egy műveletből áll. Az egyéni szabályoknak két típusa van: egyezési szabályok és sebességkorlátozási szabályok. Az egyezési szabály az egyező feltételek alapján szabályozza a hozzáférést, míg a díjkorlát-szabály az egyező feltételek és a bejövő kérelmek díjai alapján szabályozza a hozzáférést. Letilthatja az egyéni szabályt, hogy megakadályozza annak kiértékelését, de továbbra is megtartja a konfigurációt. 

## <a name="priority-match-conditions-and-action-types"></a>Prioritás, egyezési feltételek és művelettípusok

A hozzáférést egy egyéni WAf-szabállyal szabályozhatja, amely meghatározza a prioritási számot, a szabálytípusát, az egyezési feltételek tömbjét és egy műveletet. 

- **Prioritás:** egy egyedi egész szám, amely leírja a WAF-szabályok kiértékelésének sorrendjét. Az alacsonyabb prioritású szabályok kiértékelése a magasabb értékekkel rendelkező szabályok előtt történik. A prioritási számoknak egyedinek kell lenniük az összes egyéni szabály között.

- **Művelet:** meghatározza, hogyan kell egy kérelmet továbbirányítani, ha egy WAF-szabály egyezik. Az alábbi műveletek közül választhat, amelyeket akkor kell alkalmazni, ha egy kérelem megfelel egy egyéni szabálynak.

    - *Allow* - WAF továbbítja a küldetést a back-end, bejelentkezik egy bejegyzést WAF naplók és kilép.
    - *Blokk* – A kérelem le van tiltva, a WAF válaszküldése az ügyfélnek anélkül, hogy a kérést a háttérrendszerbe továbbítana. A WAF naplót vesz egy bejegyzést a WAF-naplókban.
    - *Napló* – a WAF naplózza a WAF-naplókban lévő bejegyzést, és folytatja a következő szabály kiértékelését.
    - *Átirányítás* – A WAF átirányítja a kérelmet egy megadott URI-ra, naplóz egy bejegyzést a WAF-naplókban, és kilép.

- **Egyezési feltétel:** egyegyezési változót, operátort és egyezési értéket határoz meg. Minden szabály több egyezési feltételt is tartalmazhat. Az egyezési feltétel alapulhat földrajzi hely, ügyfél IP-címek (CIDR), méret vagy string egyezés. A karakterlánc-egyezés ellentétes lehet az egyezési változók listájával.
  - **Változó egyeztetése:**
    - Kérésmód
    - QueryString
    - PostArgs között
    - RequestUri (Kérés)
    - RequestHeader fejléc
    - RequestBody (Kérés)
    - Cookie-k
  - **Üzemeltető:**
    - Bármely: gyakran használják az alapértelmezett művelet definiálására, ha nincs enek szabályok egyeztetése. Minden egyezik az összes operátorral.
    - Egyenlő
    - Contains
    - LessThan: méretmegkötés
    - GreaterThan: méretmegkötés
    - LessThanOrEqual: méretmegkötés
    - GreaterThanOrEqual: méretmegkötés
    - BeginsWith (Kezdete)
    - EndsWith
    - Regex
  
  - **A Regex** nem támogatja a következő műveleteket: 
    - Visszahivatkozások és alkifejezések rögzítése
    - Tetszőleges nulla szélességű állítások
    - Szubrutin-hivatkozások és rekurzív minták
    - Feltételes minták
    - Visszakövetésvezérlő műveletek
    - A \C egybájtos direktíva
    - A \R újvonalas egyezési direktíva
    - A \K egyezés visszaállítási irányelve
    - Kihívések és beágyazott kód
    - Atomi csoportosítás és birtokos katantifierek

  - **Negate [nem kötelező]:** Beállíthatja, hogy a *negate* feltétel igaz legyen, ha egy feltétel eredményét meg kell cáfolni.
      
  - **Átalakítás [nem kötelező]:** Az egyeztetés idote előtt elvégezandó átalakítások neveit tartalmazó karakterláncok listája. Ezek a következő átalakítások lehetnek:
     - Nagybetűs 
     - Kisbetűs
     - Trim
     - EltávolításNull
     - UrlDekód
     - UrlEncode
     
   - **Egyezési érték:** A http-kérelem metódusának támogatott értékei a következők:
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - Zár
     - Kinyit
     - PROFIL
     - Lehetőségek
     - PROPFIND
     - PROPPATCH
     - MKCOL
     - Másol
     - Mozgatni

## <a name="examples"></a>Példák

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Waf egyéni szabályok példa http paraméterek alapján

Íme egy példa, amely egy egyéni szabály konfigurációját mutatja be két egyezési feltételsel. A kérelmek egy megadott helyről származnak, a hogy a hivatkozó konkretinó konkretin, a lekérdezési karakterlánc pedig nem tartalmaz "jelszót".

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
A "PUT" módszer blokkolására szolgáló példa konfigurációja az alábbiak szerint látható:

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

### <a name="size-constraint"></a>Méretmegkötés

Hozhat létre egy egyéni szabályt, amely meghatározza a bejövő kérelem egy részének méretkorlátozását. A szabály alatti szabályok például letiltjanak egy 100 karakternél hosszabb URL-t.

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
- [Webalkalmazás-tűzfal házirend konfigurálása az Azure PowerShell használatával](waf-front-door-custom-rules-powershell.md) 
- További információ a [webes alkalmazástűzfalról a bejárati ajtóval](afds-overview.md)
- Útmutató a [Front Door létrehozásához](../../frontdoor/quickstart-create-front-door.md).

