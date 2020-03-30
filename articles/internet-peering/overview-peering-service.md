---
title: Internetes társviszony-létesítés i és társviszony-létesítési szolgáltatás
titleSuffix: Azure
description: Internetes társviszony-létesítés i és társviszony-létesítési szolgáltatás
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 6c1205fcacd3d7228f1aecf1e603b66d9e1fcee5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75775704"
---
# <a name="internet-peering-vs-peering-service"></a>Internetes társviszony-létesítés i és társviszony-létesítési szolgáltatás

Az internetes társviszony-létesítés a Microsoft globális hálózata (AS8075) és a Fuvarozók vagy szolgáltatók hálózata közötti bármilyen összekapcsolására utal. A Szolgáltató társviszony-létesítési szolgáltatáspartnerré válhat, ha megvalósítja az alábbiakban ismertetett társviszony-létesítési partnerségi követelményeket, hogy megbízható és jól teljesítő nyilvános kapcsolatot biztosítson az ügyfél és a Microsoft-hálózat közötti optimális útválasztással.

## <a name="about-peering-service"></a>A Peering Service ismertetése
A társviszony-létesítési szolgáltatás egy partnerségi program a legfontosabb szolgáltatókkal, hogy kategóriájában a legjobb nyilvános internetkapcsolatot biztosítsanak a vállalati felhasználók számára. A program részét vevő partnerek közvetlen, magas rendelkezésre állású, georedundáns kapcsolatokkal és optimalizált útválasztással rendelkeznek a Microsoft számára. A társviszony-létesítési szolgáltatás a Microsoft kapcsolódási portfóliójának kiegészítése:
*   ExpressRoute az IaaS- vagy PaaS-erőforrásokhoz való privát kapcsolathoz (privát IP-terület támogatása)
    *   Partneralapú kapcsolat
    *   Közvetlen 100G-kapcsolat a Microsofttal
*   IPSEC az interneten keresztül a VPN-kapcsolat a felhő
*   SD-WAN-kapcsolat az Azure-ral a Virtual WAN-on keresztül

A társviszony-létesítési szolgáltatás célszegmense a SaaS-kapcsolat, az SD-WAN ügyfelek, akik hajlandóak internetes kitörést végrehajtani a telephelyen, valamint a kettős stratégiával rendelkező Ügyfelek MPLS és nagyvállalati szintű internet.

A Microsoft Cloud szolgáltatáshoz való csatlakozáskor az elsődleges cél a késés minimalizálása kell, hogy legyen azáltal, hogy csökkenti az oda-vissza út idejét (RTT) egy felhasználói webhelyről a Microsoft Global Network hálózatra, amely a Microsoft nyilvános hálózati gerince, amely összeköti a Microsoft összes adatközpontját és több felhőalkalmazás belépési pont. Lásd: [A legjobb kapcsolat és teljesítmény az Office 365-ben.](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694)

> [!div class="mx-imgBorder"]
> ![Az elosztott hozzáférés képe](./media/distributed-access.png)

A fenti ábrán a globális vállalat minden fiókirodája a partner hálózatán keresztül a lehető legközelebbi Microsoft peremhálózati helyhez csatlakozik.

**Társviszony-létesítési szolgáltatás ügyfélelőnyei:**
* A legjobb nyilvános útválasztás az interneten keresztül a Microsoft Cloud Services-be az optimális teljesítmény és megbízhatóság érdekében.
* Lehetőség a Microsoft Cloud hoz való csatlakozáshoz előnyben részesített SP kiválasztására.
* Forgalmi elemzések, például a késés jelentéskészítés, és előtag figyelése.
* Optimális hálózati ugrások (AS-ugrások) a Microsoft Cloud-ból.
* Útvonalelemzés és statisztika – Események a Border Gateway Protocol[(BGP)](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)útvonal anomáliák (szivárgás/eltérítés észlelése) és az optimálistól elmaradó útválasztás.

## <a name="peering-service-partnership-requirements"></a>Társviszony-létesítési szolgáltatás partnerségi követelményei
* Kapcsolódás a Microsoft Cloud szolgáltatással az ügyfélhez legközelebb eső helyen. A partnerszolgáltató a felhasználói forgalmat a felhasználóhoz legközelebb eső Microsoft edge-re irányítja. Hasonlóképpen, a felhasználó felé irányuló forgalom esetén a Microsoft a forgalmat (a BGP-címke használatával) a felhasználóhoz legközelebb eső peremhelyre irányítja, és az SP továbbítja a forgalmat a felhasználónak.
* A Partner magas rendelkezésre álló, nagy átviteli sebességű és georedundáns kapcsolatot tart fenn a Microsoft Global Network hálózattal.
* A partner használhatja a meglévő társviszony-létesítési szolgáltatását, ha az megfelel a követelménynek

## <a name="faq"></a>GYIK
A gyakori kérdésekről a [Társviszony-létesítési szolgáltatás – gyakori kérdések](service-faqs.md).

## <a name="next-steps"></a>További lépések

* További információ az ügyfélelőnyökről [a Társviszony-létesítési szolgáltatás segítségével.](https://docs.microsoft.com/azure/peering-service/)
* Ismerje meg a társviszony-létesítési szolgáltatás közvetlen társviszony-létesítésének engedélyezésének lépéseit az [társviszony-létesítési szolgáltatás partnerének forgatókönyvében.](walkthrough-peering-service-all.md)
