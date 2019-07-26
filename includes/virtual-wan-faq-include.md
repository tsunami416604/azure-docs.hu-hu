---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c46c11ead645b93d7710d1e11636037e4dcaf8e7
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444564"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Mi a különbség egy Azure-beli virtuális hálózati átjáró (VPN Gateway) és egy Azure Virtual WAN-beli VPNGateway között?

A Virtual WAN nagy léptékben biztosít helyek közötti kapcsolatokat, és kifejlesztése során elsősorban az átviteli sebességet, a méretezhetőséget és a könnyű használatot tartották szem előtt. A virtuális WAN-kapcsolat ExpressRoute jelenleg előzetes verzióban érhető el. A CPE ág eszközeinek kiépítése és az Azure Virtual WAN csatlakoztatása. Ezek az eszközök az SD-WAN- és a VPN-partnerek egyre bővülő körétől szerezhetők be. Tekintse meg az [előnyben részesített partnerek listáját](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Mi az az Azure Virtual WAN-hoz kapcsolódó fiókiroda?

Kapcsolat egy ág-eszközről az Azure Virtual WAN-ba, amely két aktív/aktív IPsec-alagútból áll.

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

A virtuális központ legfeljebb 1 000 kapcsolatot támogat. Mindegyik kapcsolathoz kettő aktív–aktív konfigurációjú alagút tartozik. Az alagutak végpontja egy Azure Virtual Hub VPN Gateway.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>A helyszíni VPN-eszköz több központhoz is csatlakoztatható?

Igen. Az adatforgalom a kezdéskor a helyszíni eszközről a legközelebbi Microsoft-peremhálózatra, majd onnan a virtuális központ felé irányul.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Támogatja az Azure Virtual WAN a globális virtuális társhálózatokat? 

 Nem.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Tudnak egymással kommunikálni a virtuális központhoz csatlakoztatott küllő virtuális hálózatok?

Igen. A küllős virtuális hálózatok közvetlenül tud kommunikálni Virtual Network-társon keresztül. Nem támogatjuk azonban a virtuális hálózatok a tranzitívnak keresztüli kommunikációt az elosztón keresztül. További információ: [Társviszony létesítése virtuális hálózatok között](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Az Azure Virtual WAN-on is üzembe helyezhetem és használhatom kedvenc hálózati virtuális készülékemet (NVA virtuális hálózaton)?

Igen, csatlakoztathatja kedvenc hálózati virtuális berendezése (NVA-ja) virtuális hálózatát az Azure Virtual WAN-hoz. Az első lépésben központ és virtuális hálózat közötti kapcsolattal csatlakoztassa a virtuális hálózati berendezés virtuális hálózatát a központhoz. Ezt követően hozzon létre egy virtuálisközpont-útvonalat, amelynek a következő ugrása a virtuális berendezésre mutat. A virtuális központ útválasztási táblázatán több útvonalat is alkalmazhat. Az NVA virtuális hálózatához csatlakoztatott összes küllőt a virtuális központhoz is csatlakoztatnia kell. Ezzel biztosítható, hogy a küllők virtuálishálózat-útvonalai a helyszíni rendszerekre legyenek propagálva.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Rendelkezhet az NVA virtuális hálózat virtuális hálózati átjáróval?

Nem. Az NVA virtuális hálózatnak nem lehet virtuális hálózati átjárója, ha a virtuális központhoz van csatlakoztatva. 

### <a name="is-there-support-for-bgp"></a>Van BGP-támogatás?

Igen, a BGP támogatott. VPN-hely létrehozásakor megadhatja a BGP-paramétereket. Ez azt jelenti, hogy az Azure-ban az adott helyhez létrehozott összes kapcsolat engedélyezve lesz a BGP-hez. Továbbá, ha a VNet egy NVA rendelkezik, és ha ez a NVA-VNet egy virtuális WAN-hubhoz lett csatolva, annak érdekében, hogy az NVA-VNet érkező útvonalakat megfelelően hirdesse, a NVA-VNet csatolt küllőknek le kell tiltaniuk a BGP-t. Emellett a küllős virtuális hálózatok a virtuális hubhoz VNet is csatlakoztathatja, hogy a küllős VNet útvonalak a helyszíni rendszerekre legyenek propagálva.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>A virtuális központban irányíthatom UDR-rel a forgalmat?

Igen, a virtuális központ útválasztási táblázatával átirányíthatja az adatforgalmat egy virtuális hálózatra.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Milyen licenc- vagy díjszabási információ érhető el a Virtual WAN-ról?
 
Igen. Lásd a [Díjszabás](https://azure.microsoft.com/pricing/details/virtual-wan/) oldalt.

### <a name="how-do-i-calculate-price-of-a-hub"></a>Hogyan kiszámítja egy központ árát?
 
A szolgáltatásért a központban kell fizetnie. Az Azure Virtual WAN-hoz való csatlakozást igénylő 10 ág vagy helyszíni eszköz például a hub VPN-végpontokhoz való csatlakozást feltételezi. Tegyük fel, hogy ez a VPN 1 skálázási egység = 500 Mbps, ez a díj $0.361/HR. Az egyes kapcsolatok díja a $0,08/HR. 10 kapcsolat esetén a teljes szolgáltatás/HR díj $0,361 + $. 8/ HR. Az Azure-t elhagyó forgalomra vonatkozó adatforgalmi díj. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hogyan készülhetnek elő a szolgáltatásra a meglévő partnerek listáján még nem szereplő új partnerek?

Küldjön egy e-mailt a következő címre: azurevirtualwan@microsoft.com. Ideális esetben a partner olyan eszközzel rendelkezik, amely támogatja az IKEv1 vagy az IKEv2 IPsec-kapcsolatot.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Mi a teendő, ha a virtuális WAN-partnerek listáján nem szerepel a használni kívánt eszköz? Használhatom továbbra is az Azure Virtual WAN VPN-hez való csatlakozáshoz?

Igen, amíg az eszköz támogatja az IPsec-IKEv1 vagy a IKEv2. A virtuális WAN-partnerek automatizálják az eszközről az Azure VPN-végpontokhoz való kapcsolódást. Ez olyan lépéseket tesz szükségessé, mint például az "ág-adatok feltöltése", az "IPsec és a konfiguráció" és a "kapcsolat". Mivel az eszköz nem virtuális WAN-partneri ökoszisztémából származik, az Azure-konfiguráció manuális megkezdése és az eszköz az IPsec-kapcsolat beállításához való frissítése után kell elvégezni a nagy mennyiségű emelést. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Lehetséges Azure Virtual WAN létrehozása Resource Manager-sablon használatával?

Egy virtuális WAN egyetlen hubhoz és egy vpnsite való egyszerű konfigurációja egy Azure rövid útmutató [sablon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)használatával hozható létre. A Virtual WAN-t elsősorban a REST-tel vagy a portálon lehet üzemeltetni.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A Virtual WAN-ban engedélyezett az ágak közötti kapcsolat?

Igen, az ágak közötti kapcsolat a Virtual WAN-ban VPN- és VPN–ExpressRoute kapcsolat esetén is elérhető. Habár a VPN-helyek közötti kapcsolat a GA, a ExpressRoute jelenleg előzetes verzióban érhető el.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Az Azure Virtual WAN lehetővé teszi az ágak közötti adatforgalom bonyolítását?

Igen.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Miben különbözik a Virtual WAN szolgáltatás a már létező Azure virtuális hálózati átjárótól?

A virtuális hálózati (VPN-) átjáró legfeljebb 30 alagutat támogat. Kapcsolatokhoz nagy mennyiségű VPN-forgalmat bonyolító Virtual WAN használata javasolt. Legfeljebb 1 000 ág kapcsolatot tud csatlakoztatni 20 GB/s-vel a központban minden régióban. A kapcsolatok aktív-aktív alagútnak minősülnek a helyszíni VPN-eszköz és a virtuális központ között. Régiónként egy hubhoz is rendelkezhet, ami azt jelenti, hogy több mint 1 000 ágat tud csatlakoztatni a hubok között.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hogyan támogatja a virtuális WAN az SD-WAN-eszközöket?

A virtuális WAN-partnerek automatizálják az IPsec-kapcsolatot az Azure VPN-végpontokkal. Ha a virtuális WAN-partner egy SD-WAN-szolgáltató, akkor azt feltételezi, hogy az SD-WAN-vezérlő az Azure VPN-végpontok automatizálását és IPsec-kapcsolatait kezeli. Ha az SD-WAN-eszköz saját végpontot igényel az Azure VPN helyett a tulajdonosi SD-WAN-funkciókhoz, akkor az SD-WAN végpontot üzembe helyezheti egy Azure-VNet, és az Azure Virtual WAN-ral együtt is létezhet.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>A Virtual WAN minden egyes hely esetében igényli az ExpressRoute használatát?

Nem, a Virtual WAN nem igényli minden hely esetében az ExpressRoute használatát. Internetes hivatkozásokon alapuló, szabványos IPsec helyek közötti kapcsolatot használ az eszköz és az Azure Virtual WAN-központ között. Előfordulhat, hogy a helyek ExpressRoute-kapcsolatcsoporttal csatlakoznak a szolgáltatói hálózathoz. Azon helyek esetében, amelyek ExpressRoute-kapcsolattal vannak csatlakoztatva egy virtuális központhoz (ez a megoldás előzetes verzióban érhető el), a helyek ágak közötti adatforgalmat bonyolíthatnak a VPN és az ExpressRoute között. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Az Azure Virtual WAN használatakor vonatkozik korlátozás a hálózat átviteli sebességére?

Az ágak száma hub/régió esetén 1000 kapcsolatra, a központban pedig összesen 20 GB-ra van korlátozva.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Hány VPN-kapcsolat támogatja a virtuális WAN hub-t?

Egy Azure-beli virtuális WAN-Központ akár 1 000 S2S-kapcsolatot és 10 000 P2S-kapcsolatot képes támogatni egyszerre.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Mi a VPN-alagút és a kapcsolat teljes VPN-átviteli sebessége?

A hub teljes VPN-átviteli sebessége a kiválasztott skálázási egység alapján akár 20 GB-ot is igénybe vesz. Az átviteli sebességet az összes meglévő kapcsolat megosztja.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A Virtual WAN szolgáltatás lehetővé teszi a helyszíni eszközök számára több internetszolgáltató párhuzamos használatát, vagy egyszerre csak egy VPN-alagutat támogat?

A virtuális WAN VPN-be érkező kapcsolatok mindig aktív-aktív alagút (az azonos központon vagy régióban lévő rugalmasság esetében) egy, a ágban elérhető hivatkozás használatával. Ez a hivatkozás a helyszíni ág ISP-hivatkozása lehet. A virtuális WAN nem biztosít olyan speciális logikát, amely párhuzamosan több INTERNETSZOLGÁLTATÓt állít be. az INTERNETSZOLGÁLTATÓn belüli feladatátvétel kezelése az ág teljes mértékben egy ág-központú hálózati művelet. A kedvenc SD-WAN-megoldásával elvégezheti az elérési út kijelölését a fiókirodában.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hogyan történik a forgalom átirányítása az Azure gerinchálózatára?

A forgalom a következő mintát követi: ág Device-> ISP-> Microsoft Edge-> Microsoft DC (hub VNet) – > Microsoft Edge-> ISP-> ág eszköz

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Ebben a modellben mire van szükség az egyes helyek esetében? Csupán internetkapcsolatra?

Igen. Internetkapcsolat és fizikai eszköz, amely támogatja az IPsec használatát, lehetőleg az integrált [partnereinktől](https://go.microsoft.com/fwlink/p/?linkid=2019615). Ha szeretné, azt is megteheti, hogy manuálisan kezeli az Azure és a választott eszköz közötti konfigurációt és kapcsolatot.
