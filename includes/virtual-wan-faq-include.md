---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/18/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d059cab5668eef8d4dafc1442ca9749a7dcf8c9d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162514"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Mi a különbség egy Azure-beli virtuális hálózati átjáró (VPN Gateway) és egy Azure Virtual WAN-beli VPNGateway között?

A Virtual WAN nagy léptékű helyek közötti kapcsolatot biztosít, és kifejlesztése során elsősorban az átviteli sebességet, a méretezhetőséget és a könnyű használatot tartották szem előtt. A CPE-ágeszközök üzembe helyezése Azure Virtual WAN-hoz való csatlakozása automatikusan történik. Ezek az eszközök az SD-WAN- és a VPN-partnerek egyre bővülő körétől szerezhetők be.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>A program indításakor mely eszközszolgáltatók (Virtual WAN-partnerek) támogatottak? 

A teljesen automatizált Virtual WAN-t jelenleg a Citrix és a Riverbed támogatja. További információ: [Virtual WAN-partnerek](https://aka.ms/virtualwan).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Kötelező egy adott partner eszközét használnom?

Nem. Bármely olyan VPN-kompatibilis eszközt használhat, amely megfelel az IKEv2 IPsec-támogatás előzetes verziójú követelményeinek.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hogyan csatlakozhatnak a Virtual WAN-partnerek automatikusan az Azure Virtual WAN-hoz?

A szoftveralapú csatlakozási megoldások jellemzően vezérlővel vagy eszközkiépítési központtal kezelik a kompatibilis eszközöket. A vezérlők Azure API-kat is használhatnak az Azure Virtual WAN-hoz való automatikus csatlakozáshoz. További információért tekintse meg a [Virtual WAN-partnerek automatizálásával](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) foglalkozó részt.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Módosítja a Virtual WAN a meglévő kapcsolati funkciókat?   

A meglévő Azure kapcsolati funkciók nem változnak.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Elérhetővé válnak új Resource Manager-erőforrások a Virtual WAN-hoz?
  
Igen, a Virtual WAN bevezetésével új Resource Manager-erőforrások válnak elérhetővé. További információ: [Áttekintés](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>Van bármilyen különleges követelménye az előzetes verzióhoz való csatlakozásnak? 

Az Azure Virtual WAN konfigurálása előtt regisztrálnia kell az előfizetését az előzetes verzióban. Ehhez küldjön egy e-mailt a következő címre az előfizetés azonosítójával: <azurevirtualwan@microsoft.com>. Az előfizetés regisztrációja után egy e-mailt fog kapni. Amíg a regisztráció megerősítéséről nem kap értesítést, nem használhatja a Virtual WAN-t a portálon.

Szempontok:

* Az Előzetes verzió csak a nyilvános Azure-régiókban érhető el.
* Virtuális központonként legfeljebb 100 kapcsolat támogatott. Mindegyik kapcsolathoz kettő aktív–aktív konfigurációjú alagút tartozik. Az alagutak végpontja egy Azure Virtual Hub VPN Gateway.
* A következő esetekben fontolja meg az előzetes verzió használatát:
  * Virtuális központonként 1 Gb/s-nél kisebb összesített sávszélességet kíván beállítani.
  * Olyan VPN-eszközzel rendelkezik, amely támogatja az útvonalalapú konfigurálást és az IKEv2 IPsec-kapcsolatot is.
  * Ki szeretné próbálni az SD-WAN használatát és a program indításakor meglévő partnerek (Riverbed, Citrix) kompatibilis eszközeit.<br>vagy<br>Olyan ágak közötti és ág–Azure kapcsolatokat kíván beállítani, amelyek magukban foglalják a helyszíni eszköz konfigurációkezelését is. (Önálló konfigurálás)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Támogatja az Azure Virtual WAN a globális virtuális társhálózatokat? 

 Nem.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Tudnak egymással kommunikálni a virtuális központhoz csatlakoztatott küllő virtuális hálózatok?

Igen. A virtuális központhoz csatlakoztatott küllők között is létesíthető közvetlen virtuális társhálózat. További információ: [Társviszony létesítése virtuális hálózatok között](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Az Azure Virtual WAN-on is üzembe helyezhetem és használhatom kedvenc hálózati virtuális készülékemet (NVA virtuális hálózaton)?

Igen, csatlakoztathatja kedvenc hálózati virtuális készüléke (NVA) virtuális hálózatát az Azure Virtual WAN-hoz, de ehhez a központnak rendelkeznie kell útválasztási képességgel (ennek megvalósítása terveink között szerepel). Az NVA virtuális hálózathoz csatlakoztatott összes küllőt a virtuális központhoz is csatlakoztatnia kell. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Rendelkezhet az NVA virtuális hálózat virtuális hálózati átjáróval?

Nem. Az NVA virtuális hálózatnak nem lehet virtuális hálózati átjárója, ha a virtuális központhoz van csatlakoztatva. 

### <a name="is-there-support-for-bgp"></a>Van BGP-támogatás?

Igen, a BGP támogatott. Az NVA virtuális hálózat útvonalainak megfelelő meghirdetése érdekében a küllőknek le kell tiltaniuk a BGP-t, ha olyan NVA virtuális hálózathoz csatlakoznak, amely egy virtuális központhoz csatlakozik. Ezenkívül csatlakoztassa a küllő virtuális hálózatokat a virtuális központhoz.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>A virtuális központban irányíthatom UDR-rel a forgalmat?

Ennek megvalósítása szerepel a terveink között. Kövessen bennünket!

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Milyen licenc- vagy díjszabási információ érhető el a Virtual WAN-ról?
 
A szolgáltatás felár nélkül használható az előzetes verzióban. Az előzetes verzióban a jelenlegi [Azure VPN- és kimenő forgalmi díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway/) marad érvényben.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hogyan készülhetnek elő a szolgáltatásra a meglévő partnerek listáján még nem szereplő új partnerek?

Küldjön egy e-mailt a következő címre: azurevirtualwan@microsoft.com. Ideális esetben a partner olyan eszközzel rendelkezik, amely támogatja az IKEv2 IPsec-kapcsolatot.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Lehetséges Azure Virtual WAN létrehozása Resource Manager-sablon használatával?

Ennek megoldásán jelenleg még dolgozunk. A szolgáltatás működtetését jelenleg a REST és a Portal használata teszi lehetővé.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A Virtual WAN-ban engedélyezett az ágak közötti kapcsolat?

Igen, az ágak közötti kapcsolat elérhető a Virtual WAN-ban.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Az Azure Virtual WAN lehetővé teszi az ágak közötti adatforgalom bonyolítását?

Igen.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Miben különbözik a Virtual WAN szolgáltatás a már létező Azure virtuális hálózati átjárótól?

A virtuális hálózati (VPN-) átjáró legfeljebb 30 alagutat támogat. Kapcsolatokhoz nagy mennyiségű VPN-forgalmat bonyolító Virtual WAN használata javasolt. A nyilvános előzetes verzió legfeljebb 100 ágak közötti kapcsolatot támogat, és 1 Gb/s sávszélességet biztosít központonként.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>A Virtual WAN minden egyes hely esetében igényli az ExpressRoute használatát?

Nem, a Virtual WAN nem igényli minden hely esetében az ExpressRoute használatát. Internetes hivatkozásokon alapuló, szabványos IPsec helyek közötti kapcsolatot használ az eszköz és az Azure Virtual WAN-központ között.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Az Azure Virtual WAN használatakor vonatkozik korlátozás a hálózat átviteli sebességére?

A nyilvános előzetes verzióban az ágak száma központok/régió közötti kapcsolatonként legfeljebb 100 lehet, illetve 1 G sávszélesség érhető el központonként.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A Virtual WAN szolgáltatás lehetővé teszi a helyszíni eszközök számára több internetszolgáltató párhuzamos használatát, vagy egyszerre csak egy VPN-alagutat támogat?

Igen, aktív–aktív alagutakat is használhat (2 alagút = 1 Azure Virtual WAN-kapcsolat) egy önálló ágból kiindulva az ágeszköztől függően.

### <a name="how-is-traffic-is-routed-on-the-azure-backbone"></a>Hogyan történik a forgalom átirányítása az Azure gerinchálózatára?

A forgalom a következő mintát követi: ágeszköz ->internetszolgáltató->Microsoft Edge->Microsoft DC->Microsoft Edge->internetszolgáltató->ágeszköz.

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Ebben a modellben mire van szükség az egyes helyek esetében? Csupán internetkapcsolatra?

Igen. Lehetőleg olyan internetkapcsolatra és fizikai eszközre, amely integrált partnereink kínálatából származik. Kivéve, ha az Azure és a választott eszköz közötti konfiguráció és kapcsolat kézi kezelése mellett dönt.