---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 761b68ca99df8ae5b4d379b95e7d2a300f7e6238
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874036"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Mi a különbség egy Azure-beli virtuális hálózati átjáró (VPN Gateway) és egy Azure Virtual WAN-beli VPNGateway között?

A Virtual WAN nagy léptékben biztosít helyek közötti kapcsolatokat, és kifejlesztése során elsősorban az átviteli sebességet, a méretezhetőséget és a könnyű használatot tartották szem előtt. Az ExpressRoute és a pont-hely kapcsolati funkciók jelenleg csak előzetes verzióban érhetők el. A CPE-ágeszközök üzembe helyezése Azure Virtual WAN-hoz való csatlakozása automatikusan történik. Ezek az eszközök az SD-WAN- és a VPN-partnerek egyre bővülő körétől szerezhetők be. Lásd az [előnyben részesített partnerek listáját](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>A program indításakor mely eszközszolgáltatók (Virtual WAN-partnerek) támogatottak? 

Mostanra már számos partner támogatja a teljesen automatizált Virtual WAN-t. További információ: [Virtual WAN-partnerek](https://go.microsoft.com/fwlink/p/?linkid=2019615). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Melyek a Virtual WAN-partnerek automatizálásának lépései?

A partnerek automatizálásának lépéseiért tekintse át [a Virtual WAN-partnerek automatizálásával](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) foglalkozó részt.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Kötelező egy adott partner eszközét használnom?

Nem. Bármely olyan VPN-kompatibilis eszközt használhat, amely megfelel az Azure IKEv2/IKEv1 IPsec-támogatásra vonatkozó követelményeinek.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hogyan csatlakozhatnak a Virtual WAN-partnerek automatikusan az Azure Virtual WAN-hoz?

A szoftveralapú csatlakozási megoldások jellemzően vezérlővel vagy eszközkiépítési központtal kezelik a kompatibilis eszközöket. A vezérlők Azure API-kat is használhatnak az Azure Virtual WAN-hoz való automatikus csatlakozáshoz. További információért tekintse meg a Virtual WAN-partnerek automatizálásával foglalkozó részt.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Módosítja a Virtual WAN a meglévő kapcsolati funkciókat?   

A meglévő Azure kapcsolati funkciók nem változnak.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Elérhetővé válnak új Resource Manager-erőforrások a Virtual WAN-hoz?
  
Igen, a Virtual WAN bevezetésével új Resource Manager-erőforrások válnak elérhetővé. További információ: [Áttekintés](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hány VPN-eszköz csatlakoztatható egyetlen központhoz?

Virtuális központonként legfeljebb 1000 kapcsolat támogatott. Mindegyik kapcsolathoz kettő aktív–aktív konfigurációjú alagút tartozik. Az alagutak végpontja egy Azure Virtual Hub VPN Gateway.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>A helyszíni VPN-eszköz több központhoz is csatlakoztatható?

Igen. Az adatforgalom a kezdéskor a helyszíni eszközről a legközelebbi Microsoft-peremhálózatra, majd onnan a virtuális központ felé irányul.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Támogatja az Azure Virtual WAN a globális virtuális társhálózatokat? 

 Nem.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Tudnak egymással kommunikálni a virtuális központhoz csatlakoztatott küllő virtuális hálózatok?

Igen. A virtuális központhoz csatlakoztatott küllők között is létesíthető közvetlen virtuális társhálózat. További információ: [Társviszony létesítése virtuális hálózatok között](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Az Azure Virtual WAN-on is üzembe helyezhetem és használhatom kedvenc hálózati virtuális készülékemet (NVA virtuális hálózaton)?

Igen, csatlakoztathatja kedvenc hálózati virtuális berendezése (NVA-ja) virtuális hálózatát az Azure Virtual WAN-hoz. Az első lépésben központ és virtuális hálózat közötti kapcsolattal csatlakoztassa a virtuális hálózati berendezés virtuális hálózatát a központhoz. Ezt követően hozzon létre egy virtuálisközpont-útvonalat, amelynek a következő ugrása a virtuális berendezésre mutat. A virtuális központ útválasztási táblázatán több útvonalat is alkalmazhat. Az NVA virtuális hálózatához csatlakoztatott összes küllőt a virtuális központhoz is csatlakoztatnia kell. Ezzel biztosítható, hogy a küllők virtuálishálózat-útvonalai a helyszíni rendszerekre legyenek propagálva.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Rendelkezhet az NVA virtuális hálózat virtuális hálózati átjáróval?

Nem. Az NVA virtuális hálózatnak nem lehet virtuális hálózati átjárója, ha a virtuális központhoz van csatlakoztatva. 

### <a name="is-there-support-for-bgp"></a>Van BGP-támogatás?

Igen, a BGP támogatott. Az NVA virtuális hálózat útvonalainak megfelelő meghirdetése érdekében a küllőknek le kell tiltaniuk a BGP-t, ha olyan NVA virtuális hálózathoz csatlakoznak, amely egy virtuális központhoz csatlakozik. A küllő virtuális hálózatokat mindenképpen csatlakoztassa a virtuális központhoz. Ezzel biztosítható, hogy a küllők virtuálishálózat-útvonalai a helyszíni rendszerekre legyenek propagálva.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>A virtuális központban irányíthatom UDR-rel a forgalmat?

Igen, a virtuális központ útválasztási táblázatával átirányíthatja az adatforgalmat egy virtuális hálózatra.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Milyen licenc- vagy díjszabási információ érhető el a Virtual WAN-ról?
 
Igen. Lásd a [Díjszabás](https://azure.microsoft.com/pricing/details/virtual-wan/) oldalt.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hogyan készülhetnek elő a szolgáltatásra a meglévő partnerek listáján még nem szereplő új partnerek?

Küldjön egy e-mailt a következő címre: azurevirtualwan@microsoft.com. Ideális esetben a partner olyan eszközzel rendelkezik, amely támogatja az IKEv1 vagy az IKEv2 IPsec-kapcsolatot.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Lehetséges Azure Virtual WAN létrehozása Resource Manager-sablon használatával?

Az [Azure rövid útmutatók sablonjai](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network) segítségével létrehozhat mintakonfigurációt egy virtuális WAN-nal, egy központtal és egy VPN-hellyel. A Virtual WAN-t elsősorban a REST-tel vagy a portálon lehet üzemeltetni.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A Virtual WAN-ban engedélyezett az ágak közötti kapcsolat?

Igen, az ágak közötti kapcsolat a Virtual WAN-ban VPN- és VPN–ExpressRoute kapcsolat esetén is elérhető. A helyek közötti VPN-kapcsolat általánosan elérhető, az ExpressRoute- és a pont-hely kapcsolat azonban jelenleg még csak előzetes verzióban érhető el.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Az Azure Virtual WAN lehetővé teszi az ágak közötti adatforgalom bonyolítását?

Igen.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Miben különbözik a Virtual WAN szolgáltatás a már létező Azure virtuális hálózati átjárótól?

A virtuális hálózati (VPN-) átjáró legfeljebb 30 alagutat támogat. Kapcsolatokhoz nagy mennyiségű VPN-forgalmat bonyolító Virtual WAN használata javasolt. A rendszer legfeljebb 1000 ágak közötti kapcsolatot támogat, és központonként 2 Gb/s sebességet biztosít a nyugati középső kivételével minden régióban. A nyugati középső régióban 20 Gb/s sebesség érhető el. A többi régióban a jövőben tervezzük bevezetni a 20 Gb/s-ot. A kapcsolatok aktív-aktív alagútnak minősülnek a helyszíni VPN-eszköz és a virtuális központ között. Régiónként egy központtal rendelkezhet, ami azt jelenti, hogy 1000-nél is több ágat csatlakoztathat a központok között.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>A Virtual WAN minden egyes hely esetében igényli az ExpressRoute használatát?

Nem, a Virtual WAN nem igényli minden hely esetében az ExpressRoute használatát. Internetes hivatkozásokon alapuló, szabványos IPsec helyek közötti kapcsolatot használ az eszköz és az Azure Virtual WAN-központ között. Előfordulhat, hogy a helyek ExpressRoute-kapcsolatcsoporttal csatlakoznak a szolgáltatói hálózathoz. Azon helyek esetében, amelyek ExpressRoute-kapcsolattal vannak csatlakoztatva egy virtuális központhoz (ez a megoldás előzetes verzióban érhető el), a helyek ágak közötti adatforgalmat bonyolíthatnak a VPN és az ExpressRoute között. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Az Azure Virtual WAN használatakor vonatkozik korlátozás a hálózat átviteli sebességére?

Az ágak száma központonként/régiónként legfeljebb 1000 kapcsolat lehet, és a központban 2 Gb/s sebesség érhető el. Kivétel az USA nyugati középső régiója, ahol a 20 Gb/s is elérhető. A többi régióban a jövőben tervezzük bevezetni a 20 Gb/s-ot.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A Virtual WAN szolgáltatás lehetővé teszi a helyszíni eszközök számára több internetszolgáltató párhuzamos használatát, vagy egyszerre csak egy VPN-alagutat támogat?

Igen, aktív–aktív alagutakat is használhat (2 alagút = 1 Azure Virtual WAN-kapcsolat) egy önálló ágból kiindulva az ágeszköztől függően.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hogyan történik a forgalom átirányítása az Azure gerinchálózatára?

A forgalom a következő mintát követi: ágeszköz ->internetszolgáltató->Microsoft Edge->Microsoft DC->Microsoft Edge->internetszolgáltató->ágeszköz

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Ebben a modellben mire van szükség az egyes helyek esetében? Csupán internetkapcsolatra?

Igen. Lehetőleg olyan internetkapcsolatra és fizikai eszközre, amely integrált [partnereink](https://go.microsoft.com/fwlink/p/?linkid=2019615) kínálatából származik. Ha szeretné, azt is megteheti, hogy manuálisan kezeli az Azure és a választott eszköz közötti konfigurációt és kapcsolatot.
