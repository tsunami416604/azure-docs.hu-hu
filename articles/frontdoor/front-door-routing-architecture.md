---
title: Az Azure bejárati ajtajának Service - útválasztó architektúrája |} A Microsoft Docs
description: Ez a cikk segít megérteni a bejárati ajtajának architektúra globális nézet aspektusa.
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
ms.openlocfilehash: 42a9c4f9983a68459351fc98fb8b757759e585bf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964891"
---
# <a name="routing-architecture-overview"></a>Útválasztási architektúrájának áttekintése

Az Azure-bejárati ajtajának Service, amikor az ügyfél megkapja kéri majd, vagy válaszokat őket (Ha a gyorsítótár engedélyezve van), és továbbítja őket a megfelelő alkalmazás háttérrendszere (fordított proxyként).

</br>Nincsenek optimalizálhatja a forgalom Azure bejárati ajtajának útválasztás, valamint ha háttérkomponenseinek útválasztási lehetőségek.

## <a name = "anycast"></a>Forgalom-útválasztási (Anycast) számára a bejárati ajtajának környezet kiválasztása

Az Azure kezdettől fogva útválasztást használ az környezetek [csomópontválasztásos](https://en.wikipedia.org/wiki/Anycast) DNS (tartománynévrendszer), mind a HTTP (Hypertext Transfer Protocol)-forgalmat, ezért felhasználói forgalom halad át a legközelebbi környezet hálózati topológia (legkevesebb tekintetében útválasztók ugrásainak). Ez az architektúra általában kínál üzenetváltási idő jobban a végfelhasználók számára (Split TCP előnyeinek lehető legnagyobb). Bejárati ajtajának rendszerezi a környezetek az elsődleges és tartalék "körök".  A külső gyűrű környezeteket, amelyek a felhasználók számára, kisebb a késésük ajánlat közelebb van.  A belső gyűrű rendelkezik környezeteket, amelyek a feladatátvétel, a külső gyűrű környezet képes kezelni, abban az esetben, ha a probléma akkor fordul elő. A külső gyűrű a preferált céljaként minden forgalmat, de a belső gyűrű a külső gyűrű érkező forgalom túlcsordulás kezeléséhez szükséges. VIP-címek (virtuális IP-címek), szempontjából minden előtér-gazdagép vagy -tartományban bejárati ajtajának által kiszolgált hozzá van rendelve elsődleges virtuális IP-címhez, amely szerint a belső és külső gyűrű a környezetek jelentettük be, valamint a tartalék virtuális IP-címhez, amely csak a környezetek bejelentéséig az a belső gyűrű. 

</br>Ez a teljes stratégia biztosítja, hogy az Ön végfelhasználóitól érkező kérelmek mindig elérje a legközelebbi bejárati ajtajának környezet és, hogy akkor is, ha az előnyben részesített bejárati ajtajának környezet nem megfelelő állapotú majd forgalom automatikusan áthelyezi a következő legközelebbi környezetbe.

## <a name = "splittcp"></a>Kapcsolódás a bejárati ajtajának környezet (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) technika, késések és TCP problémák csökkentése használhatatlanná tévő egy kapcsolatot, amely egy nagy üzenetváltási idő járna kisebb tömbökre.  Helyezi el a bejárati ajtajának környezetek közelebb a végfelhasználók számára, és leállítja a TCP-kapcsolatok a bejárati ajtajának környezeten belül, egy TCP-kapcsolatot a egy nagy üzenetváltási időt (RTT) alkalmazás háttérrendszere felosztása két TCP-kapcsolatokat. Rövid és a közötti kapcsolat a végfelhasználó számára a bejárati ajtajának környezet azt jelenti, hogy létre lekérdezi a kapcsolat, több mint három rövid adatváltások három hosszú adatváltások, késés mentése helyett.  A hosszú kapcsolat a bejárati ajtajának környezet és a háttérrendszer között lehetnek előre meghatározott és azt több végfelhasználói hívást, újra mentése a TCP-kapcsolati időt.  A hatást meg kell szorozni az SSL/TLS (Transport Layer Security)-kapcsolat létrehozásakor, mert nincsenek további adatváltások a kapcsolat biztonságossá tétele érdekében.

## <a name="processing-request-to-match-a-routing-rule"></a>Kérelem-útválasztási szabály megfelelően feldolgozása
Után a kapcsolat létrehozása és a egy SSL végzett kézfogás, amikor egy kérelem hajtanak végre a bejárati ajtajának környezetben, útválasztási szabályainak megfelelő első lépése. A megfelelés alapvetően meghatározása az összes konfiguráció elöl ajtó, mely adott útválasztási szabály megfelelően a kérést. Olvassa el elöl ajtó honnan [útvonal egyező](front-door-route-matching.md) további.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Az útválasztási szabály háttérkészlet elérhető háttérrendszereket azonosítása
Miután bejárati ajtajának rendelkezik olyan útválasztási szabály a bejövő kérelem alapján, és ha nincs gyorsítótárazás, majd a következő lépésben lekérni az állapot-mintavételi ellenőrzése az egyező útvonalhoz tartozó háttérkészlete számára. Olvassa el hogyan bejárati ajtajának figyeli a háttérrendszer állapota használatával [állapotadat-mintavételek](front-door-health-probes.md) további.

## <a name="forwarding-the-request-to-your-application-backend"></a>A kérést az alkalmazás háttérrendszerével
Végül, ha nincs gyorsítótárazás konfigurálva van, a felhasználói kérelem továbbíthatja a rendszer a "ajánlott" háttérrendszer alapján a [bejárati ajtajának esetén használt útválasztási módszer](front-door-routing-methods.md) konfigurációja.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozzon létre egy bejárati ajtajának](quickstart-create-front-door.md).
