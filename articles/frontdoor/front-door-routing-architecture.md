---
title: Azure Bejárati ajtó - útválasztási architektúra | Microsoft dokumentumok
description: Ez a cikk segít megérteni a Front Door architektúrájának globális nézetaspektusát.
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
ms.openlocfilehash: fd1f06bcb92ea97e0e9e9a6eefeac957031575a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471557"
---
# <a name="routing-architecture-overview"></a>Útválasztási architektúra – áttekintés

Az Azure bejárati ajtaját, amikor megkapja az ügyfélkérelmeket, majd vagy válaszol rájuk (ha a gyorsítótárazás engedélyezve van), vagy továbbítja őket a megfelelő alkalmazás-háttérrendszer (fordított proxyként).

</br>Lehetőség van a forgalom optimalizálására az Azure bejárati ajtajának útválasztásakor, valamint a háttérrendszerekre történő útválasztás során.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>A bejárati ajtó környezet ének kiválasztása forgalomútválasztáshoz (Anycast)

Útválasztás az Azure Bejárati ajtó környezetben használja [Anycast](https://en.wikipedia.org/wiki/Anycast) mind a DNS (Domain Name System) és a HTTP (Hypertext Transfer Protocol) forgalmat, így a felhasználói forgalom megy a legközelebbi környezetben a hálózati topológia (legkevesebb ugrás). Ez az architektúra általában jobb oda-vissza időket kínál a végfelhasználók számára (maximalizálva a Split TCP előnyeit). Front Door szervezi a környezetet az elsődleges és tartalék "gyűrűk".  A külső gyűrű környezetben, amelyek közelebb állnak a felhasználók, mely alacsonyabb késések.  A belső gyűrű olyan környezetekkel rendelkezik, amelyek kezelni tudják a külső gyűrűkörnyezet feladatátvételét, ha probléma történik. A külső gyűrű az előnyben részesített cél minden forgalom, de a belső gyűrű szükséges kezelni a forgalom túlcsordulás a külső gyűrű. Ami a VIP (Virtual Internet Protocol címek), minden előtér-állomás, vagy a domain által kiszolgált Bejárati ajtó van rendelve egy elsődleges VIP, amely által bejelentett környezetben mind a belső és a külső gyűrű, valamint a tartalék VIP, amely csak a környezetben bejelentette a belső gyűrűben. 

</br>Ez az átfogó stratégia biztosítja, hogy a végfelhasználók tól érkező kérések mindig a legközelebbi bejárati ajtó környezetben, és hogy még akkor is, ha az előnyben részesített bejárati ajtajának környezet nem megfelelő, majd a forgalom automatikusan a legközelebbi környezetbe kerül.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Csatlakozás bejárati ajtókörnyezethez (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) egy olyan technika, hogy csökkentsék a késések és a TCP problémák megtörésével egy kapcsolat, amely felmerülő nagy oda-vissza idő kisebb darabokra.  Azáltal, hogy a bejárati ajtó környezeteket közelebb helyezi a végfelhasználókhoz, és a Bejárati ajtó környezetben megszünteti a TCP-kapcsolatokat, egy TCP-kapcsolat nagy oda-vissza idővel (RTT) az alkalmazás háttérrendszerével két TCP-kapcsolatra oszlik. A végfelhasználó és a Bejárati ajtó környezet közötti rövid kapcsolat azt jelenti, hogy a kapcsolat három hosszú oda-vissza út helyett három rövid oda-vissza út felett jön létre, így a késés megtakarítása megtakarítja a kapcsolatot.  A bejárati ajtó környezet és a háttérrendszer közötti hosszú kapcsolat előre létrehozható és újrafelhasználható több végfelhasználói hívásban, így a TCP-kapcsolat idáig megtakarítható.  A hatás megsokszorozódik SSL/TLS (Transport Layer Security) kapcsolat létrehozásakor, mivel több oda-vissza út van a kapcsolat biztosítására.

## <a name="processing-request-to-match-a-routing-rule"></a>Útválasztási szabálynak megfelelő kérelem feldolgozása
A kapcsolat létrehozása és az SSL-kézfogás elvégzése után, amikor egy kérelem bejárati ajtajához tartozó környezetben érkezik, az útválasztási szabály egyeztetése az első lépés. Ez az egyezés alapvetően határozza meg az összes konfigurációk bejárati ajtaját, amely adott útválasztási szabály, hogy megfeleljen a kérelmet. További információ arról, hogy a Bejárati ajtó hogyan [egyezik](front-door-route-matching.md) az útvonalegyeztetésével.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>A rendelkezésre álló háttérrendszerek azonosítása a háttérkészletben az útválasztási szabályhoz
Ha a Bejárati ajtó rendelkezik egy egyezést egy útválasztási szabály alapján a bejövő kérelmet, és ha nincs gyorsítótárazás, majd a következő lépés az állapotmintavétel állapotát a háttér-készlet társított egyező útvonal. További információ arról, hogy a Bejárati ajtó hogyan figyeli a háttér-állapotot [az állapotpróbák](front-door-health-probes.md) használatával.

## <a name="forwarding-the-request-to-your-application-backend"></a>A kérelem továbbítása az alkalmazás háttérkiszolgálójára
Végül, feltételezve, hogy nincs gyorsítótárazás konfigurálva, a felhasználói kérelem továbbításra kerül a "legjobb" háttérrendszerre a [bejárati ajtó útválasztási módszer konfigurációja](front-door-routing-methods.md) alapján.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
