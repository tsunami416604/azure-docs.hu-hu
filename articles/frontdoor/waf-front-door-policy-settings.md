---
title: Webalkalmazási tűzfal az Azure bejárati ajtó a házirend-beállítások
description: Ismerje meg a webalkalmazási tűzfal (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 4c2f070e9b3c972f063008df8880b196ddb069cc
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362999"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Webalkalmazási tűzfal az Azure bejárati ajtó a házirend-beállítások

A webalkalmazási tűzfal (WAF) házirend lehetővé teszi a webes alkalmazásokhoz való hozzáférés szabályozásához, egyéni és felügyelt szabályai szerint. A WAF-szabályzat nevének egyedinek kell lennie. Érvényesítési hiba fog kapni, ha egy meglévő nevét használja. Ebben a cikkben leírtak szerint a házirendhez tartozó meghatározott összes szabály vonatkozó több szintű házirend-beállításokat a rendszer.

## <a name="waf-state"></a>WAF-állapot

A WAF házirendet bejárati ajtajának a következő két állapotok egyike lehet:
- **Engedélyezve:** Egy szabályzat engedélyezésekor a WAF aktívan van vizsgálatához a bejövő kéréseket, és megfelelően szabálydefiníciók megfelelő műveleteket hajtja végre
- **Letiltva:** – Ha a házirend le van tiltva, WAF vizsgálat szüneteltetve van. Bejövő kérelmek WAF fog kerülni, és háttéralkalmazások bejárati ajtajának útválasztás alapján küldött.

## <a name="waf-mode"></a>WAF-mód

WAF házirend beállítható úgy, hogy futtassa a következő két módban:

- **Észlelés üzemmód** észlelési módban fusson, amikor nem-e bármilyen műveletet eltérő figyelő WAF, és jelentkezzen a kérelmet, és az egyező WAF szabálya WAF-naplókban. Kapcsolja be a diagnosztikai naplózás bejárati ajtajának (portál használata esetén ez érhető el a a **diagnosztikai** szakaszban az Azure Portalon).

- **Megelőzés üzemmód** megelőzési módban történő futtatására konfigurálásakor WAF a megadott művelet tart, ha a kérelem megfelel a szabálynak. Egyeztetett kérések is naplózza a WAF-naplókban.

## <a name="waf-response-for-blocked-requests"></a>A blokkolt kérelmek WAF-válasza

Alapértelmezés szerint ha WAF blokkol egy kérelem egy egyező szabály miatt, adja vissza az - 403-as állapotkódot **a kérelem le van tiltva** üzenet. A referencia-karakterlánc is naplózási adja vissza.

A kérelem le van tiltva a WAF által során meghatározható a egy egyéni válasz állapotkódja és válaszüzenet. A következő egyéni állapotkódok támogatottak:

- 200    OK
- 403    Forbidden
- 405 Metoda není povolena
- 406 nem megfelelő
- 429 túl sok kérelem

Egyéni válasz állapota és a válaszidő üzenetet szintű házirend-beállítás kötelező. Ha konfigurálva van, minden a blokkolt kérelmek beolvasása az ugyanazon egyéni válasz állapota és a válaszüzenet.

## <a name="uri-for-redirect-action"></a>Művelet átirányítási URI

Egy URI-t, hogy átirányítsa a kéréseket, ha meg kell a **ÁTIRÁNYÍTÁSI** művelet van kiválasztva a szabály a WAF-házirend található. Az átirányítási URI-t kell lennie egy érvényes HTTP (S) helyet, és beállítások konfigurálása után "ÁTIRÁNYÍTÁSI" művelettel egyező szabályok irányítja át a megadott hely összes kérelem.


## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan adhat meg a WAF [egyéni válaszok](waf-front-door-configure-custom-response-code.md)
