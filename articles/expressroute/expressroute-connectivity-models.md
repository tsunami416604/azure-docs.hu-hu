---
title: "ExpressRoute kapcsolati modellek: Csatlakozás a Microsoft Azure-hoz hálózati szolgáltatókon, adatcserélőkön és Ethernet-szolgáltatókon keresztül | Microsoft Docs"
description: "Ez a cikk a különböző típusú kapcsolatokat ismerteti az ügyfél hálózata és a Microsoft Azure, az Office 365 és a Dynamics 365 szolgáltatás között. Az ügyfelek használhatnak MPLS-szolgáltatókat, felhőbeli adatcserélőket vagy Ethernet-szolgáltatókat."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 6401a2a29c5fd8dfa40bebd331621784da5958be
ms.openlocfilehash: 00f97da2189491103c461b49ac82feb92d8f4b9b


---
# <a name="expressroute-connectivity-models"></a>ExpressRoute kapcsolati modellek
A helyszíni hálózata és a Microsoft-felhő között háromféleképpen hozhat létre kapcsolatot: [Cloud Exchange közös elhelyezés](#CloudExchange), [pontok közötti Ethernet-kapcsolat](#Ethernet) és [bármely elemek közötti (IPVPN) kapcsolat](#IPVPN). A kapcsolatszolgáltatók egy vagy több kapcsolódási modellt kínálhatnak. Kapcsolatszolgáltatójával együttműködve választhatja ki az Ön számára optimális modellt.
<br><br>

![Az ExpressRoute kapcsolati modellek diagramja](./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png)

## <a name="a-namecloudexchangeaco-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>Közös elhelyezés felhőalapú adatcsere keretében
Ha közösen van elhelyezve egy felhőalapú adatcserével rendelkező létesítményben, virtuális keresztkapcsolatokat rendelhet a Microsoft-felhőhöz a közös környezet szolgáltatójának Ethernet-adatcserélőjén. A közöskörnyezet-szolgáltatók 2. rétegbeli keresztkapcsolatokat vagy felügyelt, 3. rétegbeli keresztkapcsolatokat kínálnak a közös elhelyezési létesítményben lévő infrastruktúra és a Microsoft-felhő között.

## <a name="a-nameethernetapoint-to-point-ethernet-connections"></a><a name="Ethernet"></a>Pontok közti Ethernet-kapcsolatok
Helyszíni adatközpontjait/irodáit pontok közötti Ethernet-hivatkozásokon keresztül is csatlakoztathatja a Microsoft Cloudhoz. A pontok közötti Ethernet-szolgáltatók vagy 2. rétegbeli kapcsolatokat vagy felügyelt 3. rétegbeli kapcsolatokat kínálnak a hely és a Microsoft Cloud között.

## <a name="a-nameipvpnaany-to-any-ipvpn-networks"></a><a name="IPVPN"></a>Bármely elemek közötti (IPVPN-) hálózat
WAN hálózatát integrálhatja a Microsoft Clouddal. Az IPVPN-szolgáltatók (jellemzően MPLS VPN) bármilyen elemek közötti kapcsolódást kínálnak a fiókirodák és az adatközpontok közti kapcsolatokhoz. A Microsoft Cloud összekapcsolható a WAN hálózatával, így ugyanúgy jelenik meg, mint bármely másik fiókiroda. A WAN-szolgáltatók jellemzően felügyelt 3. rétegbeli kapcsolatokat kínálnak. Az ExpressRoute képességi és szolgáltatásai a fenti kapcsolódási modellek mindegyikében megegyeznek. 

## <a name="next-steps"></a>Következő lépések
* Ismerje meg az ExpressRoute-kapcsolatokat és útválasztási tartományokat. Lásd: [ExpressRoute-kapcsolatcsoportok és útválasztási tartományok](expressroute-circuit-peerings.md).
* Tudjon meg többet az ExpressRoute funkcióiról. Lásd: [Az ExpressRoute technikai áttekintése](expressroute-introduction.md)
* Találjon egy szolgáltatót. Lásd: [ExpressRoute-partnerek és társviszony-létesítési helyszínek](expressroute-locations.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
* Tekintse meg az [Útválasztás](expressroute-routing.md), a [NAT](expressroute-nat.md) és a [QoS](expressroute-qos.md) követelményeit.
* Az ExpressRoute-kapcsolat konfigurálása.
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-portal-resource-manager.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-portal-resource-manager.md)


<!--HONumber=Feb17_HO2-->


