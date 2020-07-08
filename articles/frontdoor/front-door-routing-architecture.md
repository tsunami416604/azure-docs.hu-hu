---
title: Azure bejárati ajtó – útválasztási architektúra | Microsoft Docs
description: Ez a cikk segít megismerni az első ajtó architektúrájának globális nézetének aspektusát.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: a088e52f742f96a13ba61969c2d7a6697c96b145
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80879292"
---
# <a name="routing-architecture-overview"></a>Útválasztási architektúra – áttekintés

Az Azure bejárati ajtaja, amikor megkapja az ügyfelek kérelmeit, megválaszolja őket (ha engedélyezve van a gyorsítótárazás), vagy továbbítja őket a megfelelő alkalmazás-háttérre (fordított proxyként).

</br>Lehetőség van arra, hogy optimalizálja a forgalmat az Azure bejárati ajtóra történő útválasztás, valamint a háttérbe való útválasztás során.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Az előtérben lévő környezet kiválasztása a forgalmi útválasztáshoz

Az Azure bejárati környezetekhez való útválasztás a DNS (Tartománynévrendszer) és a HTTP-(Hypertext Transfer Protocol) [forgalom számára egyaránt](https://en.wikipedia.org/wiki/Anycast) kihasználja a többszintű konfigurációt, így a felhasználói forgalom a legközelebbi környezetbe kerül a hálózati topológia (a legkevesebb ugrás) szempontjából. Ez az architektúra általában a végfelhasználók számára biztosít jobb időigényt (a felosztott TCP előnyeit maximalizálva). A bejárati ajtó a környezeteket elsődleges és tartalék "gyűrűkre" rendezi.  A külső gyűrű olyan környezetekkel rendelkezik, amelyek közelebb vannak a felhasználókhoz, és kevesebb késést kínálnak.  A belső gyűrű olyan környezettel rendelkezik, amely képes kezelni a külső gyűrűs környezet feladatátvételét abban az esetben, ha probléma történik. A külső gyűrű az előnyben részesített cél az összes forgalom számára, de a belső gyűrű szükséges a külső gyűrűből származó forgalom túlcsordulásának kezeléséhez. A virtuális IP-címek (Virtual Internet Protocol Addresss), minden előtér-gazdagép vagy a bejárati ajtó által kiszolgált tartomány egy elsődleges VIP-t kap, amelyet a belső és külső gyűrűben, valamint egy tartalék VIP-ben jelent meg, amelyet csak a belső gyűrűben lévő környezetek jelentettek be. 

</br>Ez az általános stratégia biztosítja, hogy a végfelhasználók kérései mindig elérjék a legközelebb bejárati ajtót, és akkor is, ha az előnyben részesített ajtós környezet állapota nem kifogástalan, a forgalom automatikusan a legközelebbi legközelebbi környezetbe kerül.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Csatlakozás az előtérben lévő környezethez (Split TCP)

A [felosztott TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) egy olyan módszer, amely csökkenti a késéseket és a TCP-problémákat azáltal, hogy egy olyan kapcsolódási ponttal szakítja meg a hozzáférést, amely a nagy adatátviteli idő kisebb  Ha az előtér-környezeteket közelebb helyezi el a végfelhasználók számára, és leállítja a TCP-kapcsolatokat az előtér-környezetben, akkor az alkalmazási háttérrendszer egy nagy, időponthoz (RTT) csatlakozó TCP-kapcsolat két TCP-kapcsolatra oszlik. A végfelhasználó és a bejárati ajtó környezete közötti rövid kapcsolat azt jelenti, hogy a kapcsolat három rövid körúton, a késések megtakarítása és a késleltetés időkerete között jön véget.  Az előtér-környezet és a háttér közötti hosszú kapcsolat előre kialakítható és többször is felhasználható több végfelhasználói hívás között, a TCP-kapcsolat időpontjának mentése után.  Ennek hatására az SSL/TLS (Transport Layer Security) kapcsolat létrehozásakor kerül sor, mivel a kapcsolat biztonságossá tételéhez több kör alakú út szükséges.

## <a name="processing-request-to-match-a-routing-rule"></a>Útválasztási szabálynak megfelelő kérelem feldolgozása
Miután létrehozta a csatlakozást, és TLS-kézfogást hajt végre, amikor egy kérelem egy bejárati ajtós környezetbe kerül, az első lépés az útválasztási szabály egyeztetése. Ez alapvetően megegyezik a bejárati ajtó összes konfigurációjának meghatározásával, amely az adott útválasztási szabálynak felel meg a kérelemnek. További információért olvassa el, hogy a bevezető ajtó hogyan felel meg a [megfelelő útvonalnak](front-door-route-matching.md) .

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Az elérhető háttérrendszer meghatározása a háttér-készletben az útválasztási szabályhoz
Ha a bevezető ajtó megfelel egy útválasztási szabálynak a bejövő kérelem alapján, és ha nincs gyorsítótárazás, a következő lépés az egyeztetett útvonalhoz társított háttér-készlet állapotának lekérése. További információért olvassa el, hogy a bejárati ajtó hogyan [figyeli a háttér](front-door-health-probes.md) -állapotot az állapotfigyelő tesztek használatával.

## <a name="forwarding-the-request-to-your-application-backend"></a>A kérelem továbbítása az alkalmazás-háttérbe
Végül, feltéve, hogy nincs konfigurálva gyorsítótárazás, a felhasználói kérést a rendszer a "legjobb" háttérre továbbítja a rendszer a [bejárati útválasztási módszer](front-door-routing-methods.md) konfigurációja alapján.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
