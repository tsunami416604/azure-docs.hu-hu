---
title: Azure webalkalmazási tűzfal – gyakori kérdések
description: Ez a lap az Azure bejárati szolgáltatásával kapcsolatos gyakori kérdésekre ad választ.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: c993e465bc439ff52cba3241dbff64b7655d1f12
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849177"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Gyakori kérdések az Azure webalkalmazási tűzfallal kapcsolatban

Ez a cikk az Azure webalkalmazási tűzfal (WAF) funkcióit és funkcióit érintő gyakori kérdésekre ad választ. 

## <a name="what-is-azure-waf"></a>Mi az Azure WAF?

Az Azure WAF egy webalkalmazási tűzfal, amely segít megvédeni webalkalmazásait az olyan gyakori fenyegetésektől, mint az SQL-injektálás, a helyek közötti parancsfájlok és más webes támadások. A webalkalmazásokhoz való hozzáférés szabályozásához egyéni és felügyelt szabályok kombinációjából álló WAF-szabályzatot is meghatározhat.

Az Azure-WAF szabályzatok alkalmazhatók Application Gateway vagy Azure bejárati szolgáltatásokban üzemeltetett webalkalmazásokra is.

## <a name="what-is-waf-for-azure-front-door-service"></a>Mi a WAF az Azure bejárati ajtó szolgáltatásához? 

Az Azure bejárati ajtó egy rugalmasan méretezhető, globálisan elosztott alkalmazás-és Content Delivery Network. Az Azure WAF a bejárati ajtóval integráltan leállítja az Azure-hálózat peremén a szolgáltatásmegtagadási és a megcélzó alkalmazások támadásait, és a virtuális hálózat megkezdése előtt a támadási forrásokhoz közeledik, és a teljesítmény feláldozása nélkül nyújt védelmet.

## <a name="does-azure-waf-support-https"></a>Támogatja az Azure WAF a HTTPS-t?

A bejárati ajtó szolgáltatás SSL-alapú kiszervezést biztosít. A WAF natív módon integrálva van a bejárati ajtóval, és a visszafejtés után megvizsgálhatja a kérést.

## <a name="does-azure-waf-support-ipv6"></a>Támogatja az Azure WAF az IPv6-ot?

Igen. Az IPv4-és IPv6-alapú IP-korlátozást is konfigurálhatja.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Hogyan lehet naprakész a felügyelt szabálykészlet?

Mi a legjobb megoldás a fenyegetés tájképének megváltoztatására. Új szabály frissítésekor a rendszer hozzáadja az alapértelmezett szabálykészlet új verziószámmal.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Mi a terjesztési idő, ha módosítást végezek a WAF-szabályzatban?

A WAF-szabályzatok globális üzembe helyezése általában körülbelül 5 percet vesz igénybe, és gyakran hamarabb elvégezhető.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Eltérőek lehetnek a különböző régiók WAF házirendjei?

A bejárati ajtó szolgáltatással integrált WAF globális erőforrás. Ugyanaz a konfiguráció az összes bejárati hely esetében érvényes.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Hogyan a háttérbeli hozzáférés korlátozása csak a bejárati ajtóról?

A háttérbeli IP-Access Control listát úgy is konfigurálhatja, hogy csak a kimenő IP-címtartományok számára engedélyezze a közvetlen hozzáférést az internetről. A szolgáltatás címkéi a virtuális hálózaton való használatra támogatottak. Emellett ellenőrizheti, hogy az X által továbbított-Host HTTP-fejléc mező érvényes-e a webalkalmazáshoz.




## <a name="which-azure-waf-options-should-i-choose"></a>Mely Azure WAF-beállításokat válasszam?

A WAF-szabályzatok az Azure-ban való alkalmazása két lehetőséget kínál. A WAF az Azure bejárati ajtó egy globálisan elosztott, peremhálózati biztonsági megoldás. A Application Gateway WAF egy regionális, dedikált megoldás. Javasoljuk, hogy válasszon egy megoldást a teljes teljesítmény-és biztonsági követelmények alapján. További információ: terheléselosztás [Az Azure Application Delivery Suite](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)szolgáltatással.


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Minden integrált platformon ugyanazokat az WAF funkciókat támogatja?

Jelenleg a ModSec CRS 2.2.9 és a CRS 3,0 szabályai csak a WAF esetében támogatottak a következő címen: Application Gateway. A díjszabás korlátozása, a Geo-szűrés és az Azure által felügyelt alapértelmezett szabálykészlet-szabályok csak az Azure-beli WAF támogatottak.

## <a name="is-ddos-protection-integrated-with-front-door"></a>A DDoS Protection integrálva van a bejárati ajtóval? 

Az Azure-beli hálózati éleken globálisan elosztott Azure bejárati ajtó képes felvenni és földrajzilag elkülöníteni a nagy mennyiségű támadásokat. Létrehozhat egyéni WAF szabályzatot, amellyel automatikusan blokkolhatja és korlátozhatja az ismert aláírásokkal rendelkező http (s) támadásokat. További részletekként engedélyezheti DDoS Protection szabványt azon a VNet, amelyen a háttérrendszer üzembe lett helyezve. Azure DDoS Protection standard ügyfeleink további kedvezményeket kapnak, például a költséghatékonyságot, az SLA-garanciát és a DDoS gyors reagálású csapat szakembereinek hozzáférését a támadás során felmerülő azonnali segítségért. 

## <a name="next-steps"></a>További lépések

- Ismerje meg az [Azure](waf-overview.md)webalkalmazási tűzfalat.
- További információ az [Azure](front-door-overview.md)bejáratáról.
