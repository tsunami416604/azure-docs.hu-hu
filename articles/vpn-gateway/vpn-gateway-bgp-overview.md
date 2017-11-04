---
title: "Az Azure VPN Gatewayek BGP áttekintése |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan használható a BGP az Azure VPN Gateway megoldással együtt."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: f8c3985c-c128-4f34-835c-0e88742bf36e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/12/2017
ms.author: yushwang
ms.openlocfilehash: 13a17eb3d78e70a09864bf218f1027d6e98486a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>A BGP és az Azure VPN Gateway együttműködésének áttekintése
Ez a cikk ismerteti az Azure VPN Gateway megoldásban a BGP (Border Gateway Protocol) támogatását.

A BGP az interneten gyakran használt szabványos útválasztási protokoll az útválasztási és elérhetőségi információcserére két vagy több hálózat között. Azure Virtual Network-környezetben a BGP teszi lehetővé az Azure VPN Gateway átjárók és a helyszíni VPN-eszközök (más néven BGP-társak vagy -szomszédok) számára az információcserét az „útvonalakat” illetően. Ezáltal mindkét átjáró ismerni fogja az érintett átjárókon és útválasztókon áthaladó előtagok rendelkezésre állási és elérhetőségi információit. A BGP lehetővé teszi a több hálózat közötti tranzit útválasztást azon útvonalak propagálásával az összes többi BGP-társ számára, amelyeket a BGP-átjáró az egyik BGP-társtól vesz át. 

## <a name="why"></a>Miért érdemes használni a BGP?
A BGP egy olyan opcionális szolgáltatás, amely az Azure útvonalalapú VPN-átjáróival együtt használható. A szolgáltatás engedélyezése előtt arról is meg kell győződni, hogy a helyszíni VPN-eszközök támogatják a BGP használatát. Az Azure VPN-átjáróit és a helyszíni VPN-eszközöket a BGP alkalmazása nélkül is tovább használhatja. Mindez egyenértékű a statikus (BGP nélküli) útvonalak dinamikus (BGP-vel történő) útválasztással *szembeni* alkalmazásával a hálózatok és az Azure között.

A BGP használata számos előnyt és új képességet biztosít:

### <a name="prefix"></a>Támogatja az automatikus és rugalmas előtag frissítések
A BGP használatakor kizárólag egy minimális előtag megadására van szükség egy adott BGP-társ számára, az IPsec S2S VPN-alagúton keresztül. Lehet ez olyan kicsi is, mint a helyszíni VPN-eszközhöz tartozó BGP-társ IP-címének gazdaelőtagja (/32-es előtag). Szabályozhatja az Azure-on meghirdetett helyszíni hálózati előtagok körét az Azure Virtual Network hozzáférésének biztosításához.

Is előfordulhat, hogy tartalmazza a VNet-címelőtagjait, például a nagy privát IP-címterület (például: 10.0.0.0/8) némelyike nagyobb előtagok hirdethető meg. Vegye figyelembe, ha az előtagok nem lehet azonos a VNet-előtagok egyike. A rendszer elutasítja a virtuális hálózat előtagjaival azonos útvonalakat.

### <a name="multitunnel"></a>Egy VNet és egy helyszíni hely közötti több alagutat támogatja a BGP-alapú automatikus feladatátvételi
Ugyanazon a helyen több kapcsolat is létesíthető az Azure virtuális hálózat és a helyszíni VPN-eszközök között. Ezen képesség révén több alagút (elérési út) létesíthető a két hálózat között, aktív-aktív konfiguráció formájában. Ha az alagutat egyik le van választva, visszavonja a megfelelő útvonalak BGP keresztül, és automatikusan lesz a forgalom a fennmaradó alagutak.

A következő ábra erre a magas rendelkezésre állású beállításra mutat egyszerű példát:

![Több aktív elérési út](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="transitrouting"></a>Támogatja a tranzit útválasztást a helyszíni hálózatokhoz és a több Azure Vnet között
A BGP több átjáró számára biztosítja a különböző hálózatokról származó előtagok megtanulásának és propagálásának lehetőségét, és hogy azok közvetlen vagy közvetett módon csatlakoznak-e. Ezzel engedélyezhető az Azure VPN-átjárók használatával történő tranzit útválasztás a helyszínek között vagy több Azure virtuális hálózaton.

A következő ábra egy olyan, több elérési úttal rendelkező, többugrásos topológiára mutat példát, amely két helyszíni hálózat közötti tranzit adatátvitelre képes, Azure VPN-átjárókon keresztül, a Microsoft Networks keretein belül:

![Többugrásos átvitel](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="faq"></a>A BGP – GYAKORI KÉRDÉSEK
[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="next-steps"></a>Következő lépések
A BGP helyek és virtuális hálózatok közötti kapcsolathoz történő konfigurálásának lépéseit [A BGP használatának első lépései Azure VPN-átjárókon](vpn-gateway-bgp-resource-manager-ps.md) című részben tekintheti meg.

