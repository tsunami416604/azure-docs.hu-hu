---
title: Az Azure bejárati ajtajának Service - alkalmazás réteg biztonsági |} A Microsoft Docs
description: Ez a cikk segít megérteni, hogyan Azure bejárati ajtajának szolgáltatás lehetővé teszi a háttérkomponensei védelmében
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e5714f60b7fdd790f3af8e31250c41038110fd08
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047543"
---
# <a name="application-layer-security-with-front-door"></a>A bejárati ajtajának alkalmazásbiztonsági réteg
Azure bejárati ajtajának Service lehetővé teszi a webes alkalmazás védelmi funkció révén a webes alkalmazások és a hálózati támadások és SQL-injektálás vagy az idegen hely parancsfájl-kezelési (XSS) gyakori webes biztonsági rések támadások ellen. Engedélyezett http (s) az előtér-kiszolgálókon, bejárati ajtajának application layer biztonsági globálisan elosztott és mindig, leállításával rosszindulatú támadások, az Azure hálózati peremhálózati, milyen távolságra származó a háttérrendszerekre. A fokozott biztonság és a teljesítmény optimalizálása bejárati ajtajának gyors biztosít, és biztonságos webes élmény a végfelhasználók számára.

## <a name="application-protection"></a>Alkalmazásvédelem
Alkalmazásvédelem bejárati ajtajának edge környezeteket világszerte, alkalmazások, megfelelően van konfigurálva, és automatikusan letiltja a nem – HTTP (s) forgalmat a webes alkalmazások elérése. A több-bérlős elosztott architektúra lehetővé teszi, hogy nagy mennyiségű globális védelmi teljesítmény feláldozása nélkül. Http (s) számítási feladatok esetén bejárati ajtajának webszolgáltatás alkalmazás védelmet biztosít egy gazdag szabálymotorral egyéni szabályok, előre konfigurált szabálykészletben gyakori támadások ellen, és részletes naplózás minden kérelemhez, amely megfelel egy szabályt. Többek között rugalmas műveletek lehetővé teszik, letiltása, vagy a napló csak használata támogatott.

## <a name="custom-access-control-rules"></a>Egyéni hozzáférés-vezérlési szabályok
- **IP-Tiltólistákkal és engedélyezési listákkal:** konfigurálhatja a egyéni szabályok a webalkalmazások ügyfél IP-címek listája alapján való hozzáférés szabályozásához. IP v4 és IP v6 egyaránt támogatottak
- **Földrajzi alapú hozzáférés-vezérlés:** konfigurálásával szabályozható a webes alkalmazások országkód: az ügyfél IP-cím alapján egyéni szabályok
- **Szűrés a HTTP-paraméterek:** előfordulhat, hogy a megfelelő http (s) kérelem paramétereit, beleértve a fejléceket, URL-CÍMÉT, alapuló egyéni hozzáférési szabályok konfigurálása és lekérdezési karakterláncok

## <a name="azure-managed-rules"></a>Azure által felügyelt szabályok
- Alapértelmezés szerint engedélyezve van a közös felső OWASP biztonsági rések elleni egy előre konfigurált szabálykészletet. Előzetes verzióban a szabálykészletet sqli és xss kérések ellenőrzése magában foglalja. További szabályok fog bővülni. Dönthet úgy, hogy kezdje a napló egyetlen művelet, előre konfigurált szabályai munkahelyi érvényesítéséhez, az alkalmazások az elvárt módon 

## <a name="rate-limiting"></a>Arány korlátozása
- A sebesség-vezérlési szabály, hogy minden ügyfél IP-rendellenes nagy forgalmat.  Előfordulhat, hogy lát egy küszöbértéket egy egy perces időszakra ügyfél IP-cím által engedélyezett webes kérelmek száma.

## <a name="centralized-protection-policy"></a>Központosított alkalmazásvédelmi szabályzat
- Előfordulhat, hogy több védelmi szabályok megadása, és hozzáadhatja őket egy szabályzatot a prioritásuk szerinti sorrendben történik. Egyéni szabályok a magasabb prioritású, mint a kivételek engedélyezése a felügyelt szabálykészletben rendelkezik. Egyetlen szabályzatban a webes alkalmazás társítva.  Webes alkalmazás azonos védelmi szabályzat replikálódik minden helyen lévő összes peremhálózati kiszolgálókról, győződjön meg, hogy következetes biztonsági házirend, minden régióban

## <a name="configuration"></a>Konfiguráció
- Az előzetes verzióban használatával REST API-k, PowerShell vagy parancssori felület bejárati ajtajának alkalmazás védelmi szabályok és a szabályzatok létrehozása és telepítése. Mielőtt az általános támogatni fogja az portál elérésére. 


## <a name="monitoring"></a>Figyelés
Bejárati ajtajának lehetővé teszi a webes alkalmazások, amelyek integrálhatók az Azure monitorral nyomon követheti a riasztásokat, és könnyedén figyelheti a trendeket, valós idejű metrikák használatával támadások elleni figyelése.

## <a name="pricing"></a>Díjszabás
Az előzetes verzióban bejárati ajtajának application layer biztonsági díjmentes.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozzon létre egy bejárati ajtajának](quickstart-create-front-door.md).
- Ismerje meg, [bejárati ajtajának működése](front-door-routing-architecture.md).
