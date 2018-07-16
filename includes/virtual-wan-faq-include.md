---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/10/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5cb1360731eeabe4963330210ba6fe090f0e088a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008824"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Mi a különbség egy Azure-beli virtuális hálózati átjáró (VPN Gateway) és egy Azure Virtual WAN-beli VPNGateway között?

A Virtual WAN nagy léptékű helyek közötti kapcsolatot biztosít, és kifejlesztése során elsősorban az átviteli sebességet, a méretezhetőséget és a könnyű használatot tartották szem előtt. A CPE-ágeszközök üzembe helyezése Azure Virtual WAN-hoz való csatlakozása automatikusan történik. Ezek az eszközök az SD-WAN- és a VPN-partnerek egyre bővülő körétől szerezhetők be.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>A program indításakor mely eszközszolgáltatók (Virtual WAN-partnerek) támogatottak? 

A teljesen automatizált Virtual WAN-t jelenleg a Citrix és a Riverbed támogatja. A következő hónapokban további partnerekkel (például Nokia Nuage, Palo Alto, Checkpoint) bővül ez a kör. További információ: [Virtual WAN-partnerek](https://aka.ms/virtualwan).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Kötelező egy adott partner eszközét használnom?

Nem. Bármely olyan VPN-kompatibilis eszközt használhat, amely megfelel az IKEv2 IPsec-támogatás előzetes verziójú követelményeinek.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hogyan csatlakozhatnak a Virtual WAN-partnerek automatikusan az Azure Virtual WAN-hoz?

A szoftveralapú csatlakozási megoldások jellemzően vezérlővel vagy eszközkiépítési központtal kezelik a kompatibilis eszközöket. A vezérlők Azure API-kat is használhatnak az Azure Virtual WAN-hoz való automatikus csatlakozáshoz. Ennek működéséről a [Virtual WAN-partnerek automatizálásával foglalkozó részben](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) talál további információt.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Módosítja a Virtual WAN a meglévő kapcsolati funkciókat?   

A meglévő Azure kapcsolati funkciók nem változnak.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Elérhetővé válnak új Resource Manager-erőforrások a Virtual WAN-hoz?
  
Igen, a Virtual WAN bevezetésével új Resource Manager-erőforrások válnak elérhetővé. További információ: [Áttekintés](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>Van bármilyen különleges követelménye az előzetes verzióhoz való csatlakozásnak? 

Az Azure Virtual WAN konfigurálása előtt regisztrálnia kell az előfizetését az előzetes verzióban. Ehhez küldjön egy e-mailt a következő címre az előfizetés azonosítójával: <azurevirtualwan@microsoft.com>. Az előfizetés regisztrációja után egy e-mailt fog kapni. Amíg a regisztráció megerősítéséről nem kap értesítést, nem használhatja a Virtual WAN-t a portálon.

Szempontok:

* Az Előzetes verzió csak a nyilvános Azure-régiókban érhető el.
* Virtuális központonként legfeljebb 100 kapcsolat támogatott. Mindegyik kapcsolathoz 2-2 aktív-aktív konfigurációjú alagút tartozik. Az alagutak végpontja egy Azure Virtual Hub VPN Gateway.
* A következő esetekben fontolja meg az előzetes verzió használatát:
  * Virtuális központonként 1 Gb/s-nél kisebb összesített sávszélességet kíván beállítani.
  * Olyan VPN-eszközzel rendelkezik, amely támogatja az útvonalalapú konfigurálást és az IKEv2 IPsec-kapcsolatot is.
  * Ki szeretné próbálni az SD-WAN használatát és a program indításakor meglévő partnerek (Riverbed, Citrix) kompatibilis eszközeit.<br>vagy<br>Olyan ágak közötti és ág–Azure kapcsolatokat kíván beállítani, amelyek magukban foglalják a helyszíni eszköz konfigurációkezelését is. (Önálló konfigurálás)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Támogatja az Azure Virtual WAN a globális virtuális társhálózatokat? 

 Nem.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Tudnak egymással kommunikálni a virtuális központhoz csatlakoztatott küllő virtuális hálózatok?

Igen. A virtuális központhoz csatlakoztatott küllők között is létesíthető közvetlen virtuális társhálózat. További információ: [Társviszony létesítése virtuális hálózatok között](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Az Azure Virtual WAN-on is üzembe helyezhetem és használhatom kedvenc hálózati virtuális készülékemet (NVA virtuális hálózaton)?

Igen, csatlakoztathatja kedvenc hálózati virtuális készüléke (NVA) virtuális hálózatát az Azure Virtual WAN-hoz, de ehhez a központnak rendelkeznie kell útválasztási képességgel (ez a funkció általánosan elérhető lesz). Az NVA virtuális hálózathoz csatlakoztatott összes küllőt a virtuális központhoz is csatlakoztatnia kell. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Rendelkezhet az NVA virtuális hálózat virtuális hálózati átjáróval?

Nem. Az NVA virtuális hálózatnak nem lehet virtuális hálózati átjárója, ha a virtuális központhoz van csatlakoztatva. 

### <a name="is-there-support-for-bgp"></a>Van BGP-támogatás?

Igen, a BGP támogatott. Az NVA virtuális hálózat útvonalainak megfelelő meghirdetése érdekében a küllőknek le kell tiltaniuk a BGP-t, ha olyan NVA virtuális hálózathoz csatlakoznak, amely egy virtuális központhoz csatlakozik. Ezenkívül csatlakoztassa a küllő virtuális hálózatokat a virtuális központhoz.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>A virtuális központban irányíthatom UDR-rel a forgalmat?

Az UDR és az útválasztási funkció általánosan elérhető lesz.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Milyen licenc- vagy díjszabási információ érhető el a Virtual WAN-ról?
 
A szolgáltatás felár nélkül használható. Az előzetes verzióban a jelenlegi [Azure VPN- és kimenő forgalmi díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway/) marad érvényben.