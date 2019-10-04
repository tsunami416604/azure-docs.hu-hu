---
title: A webalkalmazási tűzfal házirend-beállításai az Azure bejárati ajtaján
description: A webalkalmazási tűzfal (WAF) megismerése.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 8f51cb6944221416b098a9b953db417053155f1e
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849108"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>A webalkalmazási tűzfal házirend-beállításai az Azure bejárati ajtaján

A webalkalmazási tűzfal (WAF) házirendje lehetővé teszi, hogy egyéni és felügyelt szabályok alapján vezérelje a webalkalmazásokhoz való hozzáférést. A WAF-szabályzat nevének egyedinek kell lennie. Ha meglévő nevet próbál használni, a rendszer érvényesítési hibát fog kapni. Az ebben a cikkben leírtak szerint több házirendi szintű beállítás is érvényes az adott szabályzathoz megadott összes szabályra.

## <a name="waf-state"></a>WAF állapota

A WAF házirendje a következő két állapot egyike lehet:
- **Engedélyezve** Ha egy házirend engedélyezve van, a WAF aktívan ellenőrzi a bejövő kérelmeket, és a szabályok definíciói szerint végrehajtja a megfelelő műveleteket.
- **Letiltva:** – ha egy házirend le van tiltva, a WAF-ellenőrzés szünetel. A bejövő kérések megkerülik a WAF, és a rendszer az előtér-útválasztás alapján küldi el a háttér-végpontoknak.

## <a name="waf-mode"></a>WAF mód

A WAF házirend a következő két módban való futtatásra konfigurálható:

- **Észlelési mód** Az észlelési módban való futtatáskor a WAF nem hajt végre semmilyen műveletet a figyeléstől, és naplózza a kérést és a megegyező WAF szabályt a WAF-naplókhoz. A naplózási diagnosztika bekapcsolása a bejárati ajtóhoz (a portál használata esetén ez a **diagnosztika** szakaszban érhető el a Azure Portal).

- **Megelőzési mód** Ha a beállítás a megelőzési módban való futtatásra van konfigurálva, a WAF végrehajtja a megadott műveletet, ha egy kérelem megfelel egy szabálynak. A rendszer a WAF-naplókban is naplózza a megfeleltetett kérelmeket.

## <a name="waf-response-for-blocked-requests"></a>WAF válasz a blokkolt kérelmekhez

Alapértelmezés szerint, ha a WAF egy egyeztetett szabály miatt blokkol egy kérést, a egy 403-as állapotkódot ad vissza – **a kérés blokkolva** üzenet. A rendszer a naplózáshoz is visszaadja a hivatkozási karakterláncot.

Megadhat egy egyéni válasz állapotkódot és válaszüzenetet, ha a WAF letiltotta a kérelmet. A következő egyéni állapotkódok támogatottak:

- 200 OK
- 403 Tiltott
- 405 metódus nem engedélyezett
- 406 nem fogadható el
- 429 túl sok kérés

Az egyéni válasz állapotkód és a válaszüzenet egy házirendi szintű beállítás. Miután konfigurálta, az összes letiltott kérelem ugyanazt az egyéni választ kapja és válaszüzenetet kap.

## <a name="uri-for-redirect-action"></a>Átirányítási művelet URI-ja

Meg kell határoznia egy URI-t, hogy átirányítsa a kérelmeket, ha az **átirányítás** művelet be van JELÖLVE a WAF szabályzatban foglalt szabályok bármelyikéhez. Az átirányítási URI-nak érvényes HTTP (S) helynek kell lennie, és a konfigurálást követően az "átirányítás" művelettel egyező szabályokat tartalmazó kérelmeket a rendszer átirányítja a megadott helyre.


## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan határozhatja meg a WAF [Egyéni válaszait](waf-front-door-configure-custom-response-code.md)
