---
title: Az Azure Bejárati ajtóval rendelkező webalkalmazás-tűzfal házirend-beállításai
description: Ismerje meg a webalkalmazás-tűzfalat (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932619"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Az Azure Bejárati ajtaján lévő webalkalmazás-tűzfal házirend-beállításai

A webalkalmazás-tűzfal (WAF) házirend lehetővé teszi a webalkalmazásokhoz való hozzáférés egyéni és felügyelt szabályok készletével történő szabályozását. A WAF-házirend nevének egyedinek kell lennie. Ha meglévő nevet próbál használni, érvényesítési hiba lép fel. Több házirendszintű beállítás van, amelyek az adott házirendhez megadott összes szabályra vonatkoznak a jelen cikkben leírtak szerint.

## <a name="waf-state"></a>WAF állapot

A bejárati ajtó WAF-házirendje a következő két állapot egyikében lehet:
- **Engedélyezve:** Ha egy házirend engedélyezve van, a WAF aktívan ellenőrzi a bejövő kérelmeket, és a szabálydefinícióknak megfelelően megteszi a megfelelő műveleteket.
- **Letiltva:** - Ha egy házirend le van tiltva, a WAF-ellenőrzés szünetel. A bejövő kérelmek megkerülik a WAF-ot, és a bejárati ajtó útválasztása alapján háttérkiszolgálókra kerülnek.

## <a name="waf-mode"></a>WAF mód

A WAF-házirend beállítható úgy, hogy a következő két módban fusson:

- **Észlelési mód** Észlelési módban a WAF nem végez más műveleteket, mint a kérelem és az egyező WAF-szabály figyelése és naplózása a WAF-naplókba. Kapcsolja be a naplózási diagnosztika a bejárati ajtaját (portal használata kor, ez érhető el az Azure Portal **Diagnosztika** szakaszának megy).

- **Megelőzési mód** Ha megelőzési módban van konfigurálva, a WAF végrehajtja a megadott műveletet, ha egy kérelem megfelel egy szabálynak. Az egyező kérelmek et is naplózza a WAF-naplók.

## <a name="waf-response-for-blocked-requests"></a>WAF-válasz a blokkolt kérelmekre

Alapértelmezés szerint, ha a WAF letilt egy kérelmet egy egyező szabály miatt, 403-as állapotkódot ad vissza a - **A kérelem blokkolva van.** A rendszer egy hivatkozási karakterláncot is visszaad a naplózáshoz.

Egyéni válaszállapotkódot és válaszüzenetet definiálhat, ha a WAF blokkolja a kérést. A következő egyéni állapotkódok támogatottak:

- 200 OK
- 403 Tiltott
- 405 Módszer nem engedélyezett
- 406 Nem fogadható el
- 429 Túl sok kérés

Az egyéni válasz állapotkódja és a válaszüzenet egy házirendszintű beállítás. Konfigurálása után az összes blokkolt kérelem ugyanazt az egyéni válaszállapotot és válaszüzenetet kapja.

## <a name="uri-for-redirect-action"></a>Uri átirányítási művelethez

Meg kell határoznia egy URI-t, amelyhez át irányíthatja a kérelmeket, ha az **ÁTIRÁNYÍTÁS** művelet a WAF-házirendben szereplő szabályok bármelyikéhez van kiválasztva. Ennek az átirányítási URI-nak érvényes HTTP(S) helynek kell lennie, és a konfigurálás után az "ÁTIRÁNYÍTÁS" művelettel rendelkező összes kérelem megfeleltetési szabályát a rendszer a megadott helyre irányítja át.


## <a name="next-steps"></a>További lépések
- További információ a WAF [egyéni válaszok meghatározásáról](waf-front-door-configure-custom-response-code.md)
