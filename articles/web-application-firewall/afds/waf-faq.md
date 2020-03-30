---
title: Azure webalkalmazás-tűzfal – gyakori kérdések
description: Ez a cikk választ ad az Azure Bejárati ajtaján található webalkalmazás-tűzfallal kapcsolatos gyakori kérdésekre
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: c40210f3a6f0fb10be1f20deef87b2acfa076a4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460636"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Gyakori kérdések az Azure Web Application Firewall szolgáltatással kapcsolatban az Azure Bejárati ajtó szolgáltatáson

Ez a cikk az Azure webalkalmazás-tűzfal (WAF) szolgáltatásaival és funkcióival kapcsolatos gyakori kérdésekre ad választ. 

## <a name="what-is-azure-waf"></a>Mi az Azure WAF?

Az Azure WAF egy webes alkalmazás tűzfal, amely segít megvédeni a webes alkalmazásokat a gyakori fenyegetések, például az SQL-injektálás, a webhelyek közötti parancsfájlok és egyéb webes biztonsági rések. A webalkalmazásokhoz való hozzáférés szabályozásához egyéni és felügyelt szabályok kombinációjából álló WAF-házirendet határozhat meg.

Az Azure WAF-szabályzat alkalmazható az Application Gateway vagy az Azure Front Doors üzemeltetett webalkalmazásokra.

## <a name="what-is-waf-on-azure-front-door"></a>Mi a WAF az Azure bejárati ajtaján? 

Az Azure Front Door egy jól méretezhető, globálisan terjeszthető alkalmazás- és tartalomkézbesítési hálózat. Az Azure WAF, a Bejárati ajtóval integrálva leállítja a szolgáltatásmegtagadást és a célzott alkalmazástámadásokat az Azure-hálózat peremén, közel a támadási forrásokhoz, mielőtt belépnének a virtuális hálózatba, védelmet nyújt a teljesítmény feláldozása nélkül.

## <a name="does-azure-waf-support-https"></a>Támogatja az Azure WAF a HTTPS protokollt?

Bejárati ajtó kínál SSL kiszervezés. A WAF natívmódon integrálva van a Bejárati ajtóval, és a visszafejtés után megvizsgálhatja a kérelmeket.

## <a name="does-azure-waf-support-ipv6"></a>Támogatja az Azure WAF az IPv6 protokollt?

Igen. Az IPv4 és az IPv6 ip-korlátozását beállíthatja.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Mennyire naprakészek a kezelt szabálykészletek?

Mindent megteszünk, hogy lépést tartsunk a változó fenyegetési környezettel. Új szabály frissítése után az új verziószámmal hozzáadódik az alapértelmezett szabálykészlethez.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Mi a terjedési idő, ha módosítja a WAF-házirendet?

A WAF-házirend globális telepítése általában körülbelül 5 percet vesz igénybe, és gyakran hamarabb fejeződik be.

## <a name="can-waf-policies-be-different-for-different-regions"></a>A WAF-házirendek eltérőek lehetnek a különböző régiókban?

A Bejárati ajtóval integrálva a WAF globális erőforrás. Ugyanaz a konfiguráció vonatkozik az összes bejárati ajtó helyekre.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Hogyan korlátozhatom a hozzáférést a háttérprogramomhoz, hogy csak a Bejárati ajtótól legyen?

Az IP-hozzáférés-vezérlési listát a háttérben úgy konfigurálhatja, hogy csak a bejárati ajtó kimenő IP-címtartományai engedélyezze, és megtagadja a közvetlen hozzáférést az internetről. A szolgáltatáscímkék használata a virtuális hálózaton támogatott. Ezenkívül ellenőrizheti, hogy az X-Forwarded-Host HTTP fejlécmező érvényes-e a webalkalmazásra.

## <a name="which-azure-waf-options-should-i-choose"></a>Milyen Azure WAF-beállításokat válasszak?

Két lehetőség van a WAF-szabályzatok azure-beli alkalmazásakor. A WAF és az Azure Front Door egy globálisan elosztott, peremhálózati biztonsági megoldás. A WAF alkalmazásátjáróval egy regionális, dedikált megoldás. Javasoljuk, hogy az általános teljesítmény és a biztonsági követelmények alapján válasszon megoldást. További információ: [Terheléselosztás az Azure alkalmazáskézbesítési csomagjával.](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Támogatja ugyanazokat a WAF funkciókat minden integrált platformon?

Jelenleg a ModSec CRS 2.2.9, CRS 3.0 és CRS 3.1 szabályok csak az Application Gateway WAF-jével támogatottak. A sebességkorlátozás, a földrajzi szűrés és az Azure által felügyelt alapértelmezett szabálykészlet-szabályok csak az Azure Bejárati ajtaján lévő WAF-szabályok támogatottak.

## <a name="is-ddos-protection-integrated-with-front-door"></a>A DDoS védelem integrálva van a Bejárati ajtóval? 

Az Azure Front Door globálisan elosztott, az Azure Front Door képes elnyelni és földrajzilag elkülöníteni a nagy mennyiségű támadásokat. Egyéni WAF-házirendet hozhat létre, amely automatikusan blokkolja és korlátozza az ismert aláírásokkal rendelkező http(ek)es támadásokat. További, engedélyezheti a DDoS Protection Standard a virtuális hálózaton, ahol a háttér-rendszerek üzembe helyezése. Az Azure DDoS Protection Standard ügyfelei további előnyöket kapnak, beleértve a költségvédelmet, az SLA-garanciát és a DDoS gyorsreagálású csapat szakértőinek hozzáférését a támadás során.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Miért kerül át a díjkorlát-szabályhoz beállított küszöbérték feletti további kérések a háttérkiszolgálóra?

A sebességkorlátozási szabály korlátozhatja bármely ügyfél IP-címéből származó rendellenesen nagy forgalmat. Az ügyfél IP-címéről egy vagy öt perces időtartam alatt engedélyezett webes kérelmek számát küszöbértéket konfigurálhat. A részletes sebességszabályozáshoz a sebességkorlátozás kombinálható további egyezési feltételekkel, például a HTTP(S) paraméteregyeztetéssel. 

Az azonos ügyféltől érkező kérések gyakran ugyanarra a bejárati ajtókiszolgálóra érkeznek. Ebben az esetben a küszöbérték feletti további kérelmek azonnal letiltásra kerülnek. 

Előfordulhat azonban, hogy az ugyanattól az ügyféltől érkező kérések egy másik bejárati ajtókiszolgálóra érkeznek, amely még nem frissítette a sebességkorlát-számlálót. Például az ügyfél megnyithat egy új kapcsolatot minden egyes kérelemhez, és a küszöbérték alacsony. Ebben az esetben az új bejárati ajtó kiszolgálóra irányuló első kérés megfelelne a sebességkorlát-ellenőrzésnek. A sebességkorlát küszöbértéke általában magas, hogy megvédje a szolgáltatásmegtagadási támadások bármely ügyfél IP-cím. Egy nagyon alacsony küszöbértéket, előfordulhat, hogy további kérelmek et a küszöbérték felett átjutni.

## <a name="next-steps"></a>További lépések

- További információ az [Azure webalkalmazás-tűzfalról](../overview.md).
- További információ az [Azure Bejárati Ajtajáról.](../../frontdoor/front-door-overview.md)
