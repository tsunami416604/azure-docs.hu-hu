---
title: Azure bejárati ajtó | Microsoft Docs
description: Ez a cikk az Azure Front Doorról nyújt áttekintést.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2020
ms.author: duau
ms.openlocfilehash: 039e5b94bbd9d3b6c3edcb92eff88e7a9931205d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448750"
---
# <a name="what-is-azure-front-door"></a>Mi az az Azure Front Door?

Az Azure bejárati ajtó egy globális, méretezhető belépési pont, amely a Microsoft globális peremhálózati hálózatát használja gyors, biztonságos és széles körben méretezhető webalkalmazások létrehozásához. A bejárati ajtóval a globális fogyasztói és nagyvállalati alkalmazásokat robusztus, nagy teljesítményű, személyre szabott modern alkalmazásokkal alakíthatja át, amelyek az Azure-on keresztül globális közönséget érnek el.

:::image type="content" source="media/front-door-overview/front-door-visual-diagram.png#center" alt-text="Bejárati ajtó architektúrája":::

A bejárati ajtó a 7. réteg (HTTP/HTTPS-réteg) használatával működik, és a Split TCP-t és a Microsoft globális hálózatát használja a globális kapcsolatok javítása érdekében. Az útválasztási módszer alapján gondoskodhat arról, hogy a bevezető ajtó átirányítsa az ügyfelek kérelmeit a leggyorsabb és leginkább elérhető alkalmazás-háttérbe. Az alkalmazás-háttérrendszer egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás. A bejárati ajtó számos [forgalom-útválasztási módszert](front-door-routing-methods.md) és [háttérbeli állapot-figyelési lehetőséget](front-door-health-probes.md) biztosít különböző alkalmazási igényeknek és automatikus feladatátvételi forgatókönyveknek megfelelően. A [Traffic Managerhoz](../traffic-manager/traffic-manager-overview.md)hasonlóan a bejárati ajtó is rugalmasan működik a hibáknál, beleértve a teljes Azure-régió meghibásodásait is.

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. 
> * Ha DNS-alapú globális útválasztást szeretne végezni, és **nem** rendelkezik a TRANSPORT Layer Security (TLS) protokoll leállítására ("SSL-kiszervezés"), a HTTP/HTTPS-kérelemre vagy az alkalmazás-réteg feldolgozására vonatkozó követelményekkel, tekintse át a [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Ha terheléselosztást szeretne betölteni a kiszolgálók között az alkalmazási réteg egyik régiójában, tekintse át [Application Gateway](../application-gateway/application-gateway-introduction.md)
> * A hálózati réteg terheléselosztásának elvégzéséhez tekintse át [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> A végpontok közötti forgatókönyvek igény szerint összekapcsolják ezeket a megoldásokat.
> Az Azure terheléselosztási lehetőségeinek összehasonlítását lásd: [Az Azure terheléselosztási lehetőségeinek áttekintése](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="why-use-azure-front-door"></a>Miért érdemes az Azure bejárati ajtót használni?

A bejárati ajtóval a dinamikus webalkalmazások és a statikus tartalmak kiépítését, üzemeltetését és méretezését végezheti el. A bevezető ajtó lehetővé teszi a webes forgalom globális útválasztásának meghatározását, kezelését és figyelését azáltal, hogy a gyors globális feladatátvételsel optimalizálja a legfelső szintű végfelhasználói teljesítményt és megbízhatóságot.

A bejárati ajtóhoz tartozó főbb funkciók:

* Gyorsított alkalmazások teljesítménye a **[Split TCP](front-door-routing-architecture.md#splittcp)**-alapú csomópontos **[protokoll](front-door-routing-architecture.md#anycast)** használatával.

* A háttérbeli erőforrások intelligens **[állapotának](front-door-health-probes.md)** monitorozása.

*  **[URL-elérésiút-alapú](front-door-route-matching.md)** útválasztás a kérelmekhez.

* Lehetővé teszi több webhely üzemeltetését a hatékony alkalmazás-infrastruktúrához. 

* Cookie-alapú **[munkamenet-affinitás](front-door-routing-methods.md#affinity)**.

* **[SSL-kiürítés](front-door-custom-domain-https.md)** és tanúsítványkezelő.

* Saját **[egyéni tartomány](front-door-custom-domain.md)** definiálása. 

* Az alkalmazás biztonsága egyéni **[webalkalmazási tűzfal (WAF)](../web-application-firewall/overview.md)** szabályokkal és **[Azure DDoS Protectionokkal](../virtual-network/ddos-protection-overview.md)**.

* HTTP-forgalom átirányítása HTTPS **[-re URL-átirányítás](front-door-url-redirect.md)** használatával.

* Egyéni továbbítási útvonal **[URL-re való újraírásával](front-door-url-rewrite.md)**.

* A végpontok közötti IPv6-kapcsolat és a **[http/2 protokoll](front-door-http2.md)** natív támogatása.

## <a name="pricing"></a>Díjszabás

Díjszabási információkért tekintse meg [A Front Door díjszabása](https://azure.microsoft.com/pricing/details/frontdoor/) című cikket. Lásd: [SLA az Azure bejárati ajtóhoz](https://azure.microsoft.com/en-us/support/legal/sla/frontdoor/v1_0/).

## <a name="whats-new"></a>Újdonságok

Fizessen elő az RSS-hírcsatornára, és tekintse meg az Azure-beli első ajtó funkciójának frissítéseit az [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) oldalon.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
