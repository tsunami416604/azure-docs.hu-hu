---
title: Azure Bejárati ajtó - Alkalmazásréteg biztonsága | Microsoft dokumentumok
description: Ez a cikk segít megérteni, hogy az Azure Front Door hogyan teszi lehetővé az alkalmazás háttérrendszerek védelmét és védelmét
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
ms.openlocfilehash: e458926930c1b95d48886559551878fc6c9d0673
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471795"
---
# <a name="application-layer-security-with-front-door"></a>Alkalmazási réteg biztonsága a bejárati ajtóval
Az Azure Front Door webalkalmazás-védelmi képességgel védi a webes alkalmazásokat a hálózati támadások tól és a gyakori webes biztonsági rések kihasználásától, például az SQL Injection vagy a Cross Site Scripting (XSS) ellen. A http(ek) előtér-kiszolgálókhoz engedélyezett Front Door alkalmazásréteg-biztonsága globálisan elosztott és mindig bekapcsolt, az Azure hálózati peremhálózatának szélén, a háttérrendszerektől távol történő rosszindulatú támadások leállítása. A nagyobb biztonság és teljesítményoptimalizálás nak köszönhetően a Front Door gyors és biztonságos webes élményt nyújt a végfelhasználóknak.

## <a name="application-protection"></a>Alkalmazásvédelem
A Front Door alkalmazásvédelme a világ minden peremhálózati környezetében konfigurálva van, az alkalmazásokkal összhangban, és automatikusan blokkolja a nem http(ek) forgalmát a webes alkalmazások elérésében. Több-bérlős elosztott architektúránk a teljesítmény feláldozása nélkül teszi lehetővé a globális védelmet. Http(ek) számítási feladatok esetén a Bejárati ajtó webalkalmazás-védelmi szolgáltatása gazdag szabálymotort biztosít az egyéni szabályokhoz, előre konfigurált szabálykészletet a gyakori támadások ellen, és részletes naplózást biztosít minden olyan kérelemhez, amely megfelel egy szabálynak. A rugalmas műveletek, beleértve az engedélyezést, a tiltást vagy a naplót, támogatottak.

## <a name="custom-access-control-rules"></a>Egyéni hozzáférés-vezérlési szabályok
- **IP-engedélyezési lista és tiltólista:** Egyéni szabályokat állíthat be úgy, hogy az ügyfél IP-címeinek listája alapján szabályozzák a webalkalmazásokhoz való hozzáférést. Mind az IP v4, mind az IP v6 támogatott
- **Földrajzi alapú hozzáférés-vezérlés:** Egyéni szabályokat állíthat be a webalkalmazásokhoz való hozzáférés szabályozására az országkód alapján, amelyből az ügyfél IP
- **HTTP-paraméterek szűrése:** Egyéni hozzáférési szabályokat konfigurálhat a http(ek) kérési paraméterek alapján, beleértve a fejléceket, az URL-címeket és a lekérdezési karakterláncokat

## <a name="azure-managed-rules"></a>Az Azure által felügyelt szabályok
- A gyakori oWASP-biztonsági rések elleni előre konfigurált szabálykészlet alapértelmezés szerint engedélyezve van. Az előzetes verzióban a szabályok tartalmazzák az sqli és xss kérelmek ellenőrzését. További szabályok kerülnek hozzáadásra. Dönthet úgy, hogy csak a naplóművelettel kezdi az előre konfigurált szabályok működésének ellenőrzéséhez az alkalmazások esetében elvárt módon. 

## <a name="rate-limiting"></a>Sebességkorlátozás
- A díjszabályozási szabály az, hogy korlátozza a rendellenes nagy forgalmat bármely ügyfél IP-cím.  Az ügyfél IP-címe által egy perces időtartam alatt engedélyezett webes kérelmek számát küszöbértéket állíthat be.

## <a name="centralized-protection-policy"></a>Központosított védelmi házirend
- Több védelmi szabályt is definiálhat, és prioritási sorrendben hozzáadhatja őket egy házirendhez. Az egyéni szabályok prioritása magasabb, mint a felügyelt szabálykészlet a kivételek engedélyezéséhez. Egyetlen szabályzat van társítva a webalkalmazáshoz.  Ugyanaz a webalkalmazás-védelmi házirend replikálódik az összes peremhálózati kiszolgálóra minden helyen, biztosítva a konzisztens biztonsági házirendet minden régióban

## <a name="configuration"></a>Konfiguráció
- Az előzetes verzió során rest API-kat, PowerShellt vagy CLI-t használhat a Bejárati ajtó alkalmazásvédelmi szabályainak és szabályzatainak létrehozásához és üzembe helyezéséhez. A portálhozzáférés támogatott lesz, mielőtt a szolgáltatás általánosan elérhetővé válik. 


## <a name="monitoring"></a>Figyelés
A Bejárati ajtó lehetővé teszi a webes alkalmazások támadások elleni figyelését az Azure Monitorba integrált valós idejű mérőszámokkal a riasztások nyomon követésére és a trendek egyszerű figyelésére.

## <a name="pricing"></a>Díjszabás
A Front Door alkalmazásréteg-biztonsága ingyenes az előzetes verzió ban.


## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
