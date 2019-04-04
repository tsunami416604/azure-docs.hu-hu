---
title: Az Azure webalkalmazás-tűzfal – gyakori kérdések
description: Ez az oldal nyújt Azure bejárati ajtajának szolgáltatással kapcsolatos gyakori kérdésekre adott válaszok
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: ef021bc9a4f3cb8b49ab3b455e00dfa8dc27dc76
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58910000"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Gyakori kérdések az Azure webalkalmazás-tűzfal

Ez a cikk gyakori kérdésekre ad választ az Azure web application firewall (WAF) szolgáltatásairól és funkcióiról. 

## <a name="what-is-azure-waf"></a>Mi az Azure WAF?

Azure WAF, amely segít megvédeni a webes alkalmazások gyakori fenyegetések, például SQL injektálási, webhelyek közötti parancsfájl-kezelési és más internetes támadásokkal szemben a webalkalmazási tűzfal. A webes alkalmazásokhoz való hozzáférés szabályozásához egyéni és felügyelt szabályok kombinációját álló WAF házirend határozhatja meg.

Application Gateway- vagy Azure bejárati ajtajának szolgáltatások üzemeltetett webalkalmazásokhoz az Azure WAF házirend is alkalmazható.

## <a name="what-is-waf-for-azure-front-door-service"></a>Mi az Azure bejárati ajtajának szolgáltatás WAF? 

Az Azure bejárati ajtajának egy nagy mértékben skálázható, globálisan elosztott alkalmazások és a tartalomkézbesítési hálózat. Azure WAF, ha a bejárati ajtó, integrálva szolgáltatásmegtagadásos – leállítja, célzott támadásokat alkalmazás az Azure-hálózat peremén, megközelíti a támadás forrásokból, mielőtt belép a virtuális hálózat védelmet nyújt teljesítmény feláldozása nélkül.

## <a name="how-will-i-be-charged-for-azure-waf-for-front-door"></a>Hogyan kell fizetnem az Azure WAF for bejárati ajtajának?
A nyilvános előzetes verzióban ingyenesen bejárati ajtajának WAF használatát. Vegye figyelembe, hogy bejárati ajtajának díj extra. Bejárati ajtajának szolgáltatás díjszabása [Itt](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="does-azure-waf-support-https"></a>Azure WAF támogatja a HTTPS?

Bejárati ajtajának szolgáltatást kínál, SSL-kiürítés. WAF natív módon integrálva van bejárati ajtajának és után, a rendszer visszafejti a kérelem ellenőrizheti.

## <a name="does-azure-waf-support-ipv6"></a>Támogatja az IPv6 Azure WAF?

Igen. Konfigurálhatja az IPv4 és IPv6-alapú IP-korlátozást.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Hogy mennyire naprakész a felügyelt szabálykészletek vannak?

Fenyegetések világának módosítása tartani a legkedvezőbb végzünk. Ha egy új szabályt frissül, a rendszer hozzáadja az alapértelmezett szabálykészletet egy új verziószámmal.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Ha egy végrehajtott saját WAF házirend Mi a propagálás idő?

A WAF-házirend telepítése globálisan általában a körülbelül 5 percet vesz igénybe, és gyakran elvégzi hamarabb.

## <a name="can-waf-policies-be-different-for-different-regions"></a>WAF-szabályzatok esetén eltérők lehetnek különböző régiókban?

Integrált bejárati ajtajának szolgáltatással, WAF esetén egy globális erőforrást. Ugyanazt a konfigurációt az összes bejárati ajtajának helyek közötti vonatkozik.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Hogyan korlátozza a hozzáférést a háttérbeli bejárati ajtajának csak lennie ahhoz, hogy?

Előfordulhat, hogy konfigurálnia a IP hozzáférés-vezérlési listát csak bejárati ajtajának kimenő IP-címtartományokhoz engedélyezéséhez és megtagadásához közvetlen hozzáféréssel internetről a háttérszolgáltatáshoz. Szolgáltatáscímkék támogatottak a virtuális hálózaton használható. Ezenkívül ellenőrizheti, hogy a X továbbított fogadó HTTP-fejléc mező érvénytelen, a webalkalmazás.




## <a name="which-azure-waf-options-should-i-choose"></a>Melyik Azure WAF lehetőségek válasszam?

Két lehetőség van az Azure-ban WAF-szabályzatok alkalmazása esetén. Az Azure bejárati ajtajának WAF egy olyan globálisan elosztott, edge biztonsági megoldás. Az Application Gateway WAF egy olyan regionális, dedikált megoldás. Azt javasoljuk, hogy az általános teljesítmény- és biztonsági követelmények alapján megoldás választása. További információkért lásd: [terheléselosztás és az Azure application kézbesítési](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Támogatják a WAF ugyanazokat a szolgáltatásokat az összes integrált platform?

Jelenleg ModSec CRS 2.2.9 és CRS 3.0-s szabályok csak az Application gatewayben WAF támogatott. A sebességhatárolt, a földrajzi szűrés és az Azure felügyelt alapértelmezett szabálykészletet szabályok csak WAF, Azure bejárati ajtajának használata támogatott.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Integrálva van a DDoS protection bejárati ajtajának? 

Globálisan elosztott Azure-beli hálózati élei mentén, Azure bejárati ajtajának is számára, és nagy támadások földrajzilag elkülönítése. Létrehozhat egyéni WAF-házirend számára, hogy automatikusan blokk- és forgalmi korlát http (s) támadásokkal szemben, amelyek ismert aláírások. További több, engedélyezheti a DDoS Protection standard szintű, a háttéralkalmazások telepítve vannak a hálózaton. Az Azure DDoS Protection Standard ügyfelei további előnyökkel költségek védelmi, SLA-garanciára és szakértői való hozzáférés DDoS Rapid Response Team azonnali segítséget többek között a támadások során részesülnek. 

## <a name="next-steps"></a>További lépések

- Ismerje meg [ Azure webalkalmazási tűzfal](waf-overview.md).
- Tudjon meg többet [Azure bejárati ajtajának](front-door-overview.md).
