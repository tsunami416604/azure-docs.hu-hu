---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: acd601512f2240802de47483a015f03a226da115
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796183"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Mi a különbség az Azure Virtual Network Gateway (VPN Gateway) és egy Azure virtuális WAN VPN Gateway között?

A Virtual WAN nagy léptékben biztosít helyek közötti kapcsolatokat, és kifejlesztése során elsősorban az átviteli sebességet, a méretezhetőséget és a könnyű használatot tartották szem előtt. Ha egy helyet egy virtuális WAN VPN-átjáróhoz kapcsolódik, az különbözik a VPN-átjárót használó normál virtuális hálózati átjárótól. Hasonlóképpen, amikor egy ExpressRoute-áramkört csatlakoztat egy virtuális WAN-központhoz, az ExpressRoute-átjáróhoz egy másik erőforrást használ, mint a "ExpressRoute" típusú átjárót használó normál virtuális hálózati átjáró. A virtuális WAN a VPN-és a ExpressRoute esetében akár 20 GB/s összesített átviteli sebességet is támogat. A Virtual WAN a CPE ág-eszközökhöz tartozó partnerekkel való kapcsolat automatizálásával is rendelkezik. A CPE ág eszközei beépített automatizálással rendelkeznek, amely automatikusan kiépíti és csatlakozik az Azure Virtual WAN-hoz. Ezek az eszközök az SD-WAN- és a VPN-partnerek egyre bővülő körétől szerezhetők be. Tekintse meg az [előnyben részesített partnerek listáját](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Miben különbözik a virtuális WAN egy Azure-beli virtuális hálózati átjárótól?

A virtuális hálózati átjáró VPN-je legfeljebb 30 alagútra van korlátozva. Kapcsolatokhoz nagy mennyiségű VPN-forgalmat bonyolító Virtual WAN használata javasolt. Régiónként (Virtual hub) akár 1 000 ág-kapcsolatot is csatlakoztathat, és a központ 20 GB/s-os összesítéssel rendelkezik. A kapcsolatok aktív-aktív alagútnak minősülnek a helyszíni VPN-eszköz és a virtuális központ között. Régiónként egy hubhoz is rendelkezhet, ami azt jelenti, hogy több mint 1 000 ágat tud csatlakoztatni a hubok között.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Mely szolgáltatók (virtuális WAN-partnerek) támogatottak?

Mostanra már számos partner támogatja a teljesen automatizált Virtual WAN-t. További információ: [Virtual WAN-partnerek](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Melyek a Virtual WAN-partnerek automatizálásának lépései?

A partnerek automatizálásának lépéseiért tekintse át [a Virtual WAN-partnerek automatizálásával](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) foglalkozó részt.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Kötelező egy adott partner eszközét használnom?

Nem. Bármely olyan VPN-kompatibilis eszközt használhat, amely megfelel az Azure IKEv2/IKEv1 IPsec-támogatásra vonatkozó követelményeinek.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hogyan csatlakozhatnak a Virtual WAN-partnerek automatikusan az Azure Virtual WAN-hoz?

A szoftveralapú csatlakozási megoldások jellemzően vezérlővel vagy eszközkiépítési központtal kezelik a kompatibilis eszközöket. A vezérlők Azure API-kat is használhatnak az Azure Virtual WAN-hoz való automatikus csatlakozáshoz. Az Automation magában foglalja a fiókirodák feltöltését, az Azure-konfiguráció letöltését, az IPSec-alagutak Azure-beli virtuális hubokba való beállítását, valamint a kapcsolat automatikus beállítását az Azure Virtual WAN-hoz. Ha több száz ága van, a virtuális WAN CPE-partnerekkel való csatlakozás egyszerű, mivel a bevezetési élmény eltartja a nagyméretű IPsec-kapcsolatok beállításának, konfigurálásának és felügyeletének szükségességét. További információért tekintse meg a [Virtual WAN-partnerek automatizálásával](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) foglalkozó részt.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Kötelező egy adott partner eszközét használnom?

Nem. Bármely olyan VPN-kompatibilis eszközt használhat, amely megfelel az Azure IKEv2/IKEv1 IPsec-támogatásra vonatkozó követelményeinek.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hogyan támogatja a virtuális WAN az SD-WAN-eszközöket?

A virtuális WAN-partnerek automatizálják az IPsec-kapcsolatot az Azure VPN-végpontokkal. Ha a virtuális WAN-partner egy SD-WAN-szolgáltató, akkor azt feltételezi, hogy az SD-WAN-vezérlő az Azure VPN-végpontok automatizálását és IPsec-kapcsolatait kezeli. Ha az SD-WAN-eszköz saját végpontot igényel az Azure VPN helyett a tulajdonosi SD-WAN-funkciókhoz, akkor az SD-WAN végpontot üzembe helyezheti egy Azure-VNet, és az Azure Virtual WAN-ral együtt is létezhet.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Módosítja a Virtual WAN a meglévő kapcsolati funkciókat?

A meglévő Azure kapcsolati funkciók nem változnak.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Elérhetővé válnak új Resource Manager-erőforrások a Virtual WAN-hoz?
  
Igen, a Virtual WAN bevezetésével új Resource Manager-erőforrások válnak elérhetővé. További információ: [Áttekintés](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hány VPN-eszköz tud csatlakozni egyetlen hubhoz?

A virtuális központ legfeljebb 1 000 kapcsolatot támogat. Minden kapcsolat négy kapcsolatból áll, és az egyes kapcsolati kapcsolatok két, aktív-aktív konfigurációban lévő alagutat támogatnak. Az alagutak megszakadnak egy Azure-beli virtuális központ átjáróban.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>A helyszíni VPN-eszköz több központhoz is csatlakoztatható?

Igen. A forgalom a megkezdése után a helyszíni eszközről a legközelebbi Microsoft hálózati szélre, majd a virtuális hubhoz kerül.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Az Azure Virtual WAN-on is üzembe helyezhetem és használhatom kedvenc hálózati virtuális készülékemet (NVA virtuális hálózaton)?

Igen, csatlakoztathatja kedvenc hálózati virtuális berendezése (NVA-ja) virtuális hálózatát az Azure Virtual WAN-hoz. Az első lépésben központ és virtuális hálózat közötti kapcsolattal csatlakoztassa a virtuális hálózati berendezés virtuális hálózatát a központhoz. Ezután hozzon létre egy virtuális hub-útvonalat a virtuális készülékre mutató következő ugrással. Több útvonalat is alkalmazhat a virtuális központ útválasztási táblájára. Az NVA virtuális hálózatához csatlakoztatott összes küllőt a virtuális központhoz is csatlakoztatnia kell. Ezzel biztosítható, hogy a küllők virtuálishálózat-útvonalai a helyszíni rendszerekre legyenek propagálva.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Létrehozhatok hálózati virtuális készüléket a virtuális központban?

Egy hálózati virtuális berendezés (NVA) nem helyezhető üzembe egy virtuális központon belül. Azonban létrehozhat egy olyan küllős VNet, amely a virtuális hubhoz van csatlakoztatva, és lehetővé teszi egy útvonal a központban való közvetlen forgalmát a VNet a NVA IP-címén keresztül.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Lehet küllős VNet virtuális hálózati átjáróval?

Nem. A küllős VNet nem rendelkezhet virtuális hálózati átjáróval, ha a virtuális hubhoz csatlakozik.

### <a name="is-there-support-for-bgp"></a>Van BGP-támogatás?

Igen, a BGP támogatott. VPN-hely létrehozásakor megadhatja a BGP-paramétereket. Ez azt jelenti, hogy az Azure-ban az adott helyhez létrehozott összes kapcsolat engedélyezve lesz a BGP-hez. Továbbá, ha a VNet egy NVA rendelkezik, és ha ez a NVA-VNet egy virtuális WAN-hubhoz lett csatolva, annak érdekében, hogy az NVA-VNet érkező útvonalakat megfelelően hirdesse, a NVA-VNet csatolt küllőknek le kell tiltaniuk a BGP-t. Emellett a küllős virtuális hálózatok a virtuális hubhoz VNet is csatlakoztathatja, hogy a küllős VNet útvonalak a helyszíni rendszerekre legyenek propagálva.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>A virtuális központban irányíthatom UDR-rel a forgalmat?

Igen, a virtuális központ útválasztási táblázatának használatával irányíthatja a forgalmat egy VNet. Ez lehetővé teszi, hogy az Azure-beli cél-virtuális hálózatok útvonalait egy adott IP-cím (általában a NVA NIC) használatával állítsa be.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Milyen licenc- vagy díjszabási információ érhető el a Virtual WAN-ról?

Igen. Lásd a [Díjszabás](https://azure.microsoft.com/pricing/details/virtual-wan/) oldalt.

### <a name="how-do-i-calculate-price-of-a-hub"></a>Hogyan kiszámítja egy központ árát?

* A központ szolgáltatásaiért fizetnie kell. Tegyük fel például, hogy 10 ág vagy olyan helyszíni eszköz van, amely az Azure Virtual WAN-hoz való csatlakozást igényli, ami azt jelenti, hogy csatlakoznia kell a VPN-végpontokhoz a központban. Tegyük fel, hogy ez a VPN 1 skálázási egység = 500 Mbps, ez a díj $0.361/HR. Az egyes kapcsolatok díja 0,05/óra. 10 kapcsolat esetén a teljes szolgáltatás/HR díj $0,361 + $. 5/HR lehet. Az Azure-t elhagyó forgalomra vonatkozó adatforgalmi díj.

* További hub-díj is rendelkezésre áll. Lásd a [Díjszabás](https://azure.microsoft.com/pricing/details/virtual-wan/) oldalt.

* Ha a virtuális hubhoz csatlakozó ExpressRoute-áramkörök miatt ExpressRoute az átjárót, akkor a skálázási egység árát kell fizetnie. Az ABB-ben minden egyes méretezési egység 2 GB/s, az egyes kapcsolati egységek pedig a VPN-kapcsolati egységgel megegyező sebességgel vannak terhelve.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hogyan készülhetnek elő a szolgáltatásra a meglévő partnerek listáján még nem szereplő új partnerek?

Minden virtuális WAN API nyitott API. A technikai megvalósíthatóság értékeléséhez áttekintheti a dokumentációt. Ha bármilyen kérdése van, küldjön egy e-mailt a azurevirtualwan@microsoft.comcímre. Ideális esetben a partner olyan eszközzel rendelkezik, amely támogatja az IKEv1 vagy az IKEv2 IPsec-kapcsolatot.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Mi a teendő, ha a virtuális WAN-partnerek listáján nem szerepel a használni kívánt eszköz? Használhatom továbbra is az Azure Virtual WAN VPN-hez való csatlakozáshoz?

Igen, amíg az eszköz támogatja az IPsec-IKEv1 vagy a IKEv2. A virtuális WAN-partnerek automatizálják az eszközről az Azure VPN-végpontokhoz való kapcsolódást. Ez olyan lépéseket tesz szükségessé, mint például az "ág-adatok feltöltése", az "IPsec és a konfiguráció" és a "kapcsolat". Mivel az eszköz nem virtuális WAN-partneri ökoszisztémából származik, az Azure-konfiguráció manuális megkezdése és az eszköz az IPsec-kapcsolat beállításához való frissítése után kell elvégezni a nagy mennyiségű emelést.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Lehetséges Azure Virtual WAN létrehozása Resource Manager-sablon használatával?

Egy virtuális WAN egyszerű konfigurációja egy hubhoz, egy vpnsite pedig egy rövid útmutató [sablon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)használatával hozható létre. A Virtual WAN elsősorban a REST vagy a portálon alapuló szolgáltatás.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Támogatja az Azure Virtual WAN a globális virtuális társhálózatokat? 

A VNet egy másik régióban is kapcsolódhat, mint a virtuális WAN-t.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Tudnak egymással kommunikálni a virtuális központhoz csatlakoztatott küllő virtuális hálózatok?

Igen. A küllős virtuális hálózatok közvetlenül tud kommunikálni Virtual Network-társon keresztül. További információ: [Társviszony létesítése virtuális hálózatok között](../articles/virtual-network/virtual-network-peering-overview.md). A tranzitívnak-t az elosztón keresztül kommunikáló virtuális hálózatok is támogatjuk.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Mi az az Azure Virtual WAN-hoz kapcsolódó fiókiroda?

Egy ág-eszköz és az Azure Virtual WAN közötti kapcsolat legfeljebb négy hivatkozást támogat. A hivatkozás a fizikai kapcsolati hivatkozás a ág helyén (például: ATT, Verizon stb.). Minden kapcsolati kapcsolat két aktív/aktív IPsec-alagútból áll.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A Virtual WAN-ban engedélyezett az ágak közötti kapcsolat?

Igen, az ágak közötti kapcsolat a Virtual WAN-ban VPN- és VPN–ExpressRoute kapcsolat esetén is elérhető.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Az ág-ág forgalom áthalad az Azure Virtual WAN-on keresztül?

Igen.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Szükséges a virtuális WAN ExpressRoute az egyes helyekről?

Nem, a Virtual WAN nem igényli minden hely esetében az ExpressRoute használatát. Szabványos IPsec helyek közötti kapcsolatot használ az eszközről az Azure-beli virtuális WAN-központba való internetes kapcsolatokon keresztül. Előfordulhat, hogy a helyek ExpressRoute-kapcsolatcsoporttal csatlakoznak a szolgáltatói hálózathoz. A virtuális központ ExpressRoute használatával összekapcsolt helyek esetében a helyek fiókirodával rendelkezhetnek a VPN-és a ExpressRoute között.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Az Azure Virtual WAN használatakor vonatkozik korlátozás a hálózat átviteli sebességére?

Az ágak száma hub/régió esetén 1000 kapcsolatra, a központban pedig összesen 20 GB-ra van korlátozva. Régiónként 1 hub-t is használhat.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Hány VPN-kapcsolat támogatja a virtuális WAN hub-t?

Az Azure Virtual WAN hub akár 1 000 S2S-kapcsolatot, 10 000 P2S-kapcsolatot és 4 ExpressRoute-kapcsolatot képes támogatni egyszerre.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Mi a VPN-alagút és a kapcsolat teljes VPN-átviteli sebessége?

A hub teljes VPN-átviteli sebessége a kiválasztott skálázási egység alapján akár 20 GB-ot is igénybe vesz. Az átviteli sebességet az összes meglévő kapcsolat megosztja. A kapcsolatok mindegyik alagútja akár 1 GB/s-t is képes támogatni.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Nem látom a 20 GB/s beállítást a virtuális hubhoz a portálon. Hogyan konfigurálja?

Navigáljon a VPN-átjáróhoz a portálon belül, és kattintson a skálázási egységre a megfelelő beállításra való váltáshoz.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A virtuális WAN lehetővé teszi, hogy a helyszíni eszköz párhuzamosan több internetszolgáltatót is használjon, vagy mindig egyetlen VPN-alagút?

A virtuális WAN VPN-be érkező kapcsolatok mindig aktív-aktív alagút (az azonos központon vagy régióban lévő rugalmasság esetében) egy, a ágban elérhető hivatkozás használatával. Ez a hivatkozás a helyszíni ág ISP-hivatkozása lehet. A "VPNSite" virtuális WAN lehetővé teszi a kapcsolati adatok hozzáadását a webhelyhez. Ha a fiókirodában több internetszolgáltató található, és mindegyik internetszolgáltató egy hivatkozást tartalmaz, akkor ezek az információk az Azure-beli VPN-hely adataiban állíthatók be. Ugyanakkor a feladatátvétel a fiókirodában lévő internetszolgáltatók közötti kezelése teljesen egy ág-központú útválasztási művelet.

### <a name="what-is-global-transit-architecture"></a>Mi az a globális átviteli architektúra?

A globális átviteli architektúrával kapcsolatos információkért lásd: a [globális tranzit hálózati architektúra és a virtuális WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hogyan történik a forgalom átirányítása az Azure gerinchálózatára?

A forgalom a következő mintát követi: ág Device-> ISP-> Microsoft Network Edge-> Microsoft DC (hub VNet) – > Microsoft Network Edge-> ISP-> ág eszköz

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Ebben a modellben mire van szükség az egyes helyek esetében? Csupán internetkapcsolatra?

Igen. Internetkapcsolat és fizikai eszköz, amely támogatja az IPsec használatát, lehetőleg az integrált [virtuális WAN-partnereinktől](../articles/virtual-wan/virtual-wan-locations-partners.md). Szükség esetén manuálisan is kezelheti a konfigurációt és a kapcsolatot az Azure-hoz a kívánt eszközről.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Hogyan alapértelmezett útvonal (0.0.0.0/0) engedélyezése egy kapcsolatban (VPN, ExpressRoute vagy Virtual Network):

Egy virtuális központ propagálhatja a megtanult alapértelmezett útvonalat egy virtuális hálózat/helyek közötti VPN/ExpressRoute kapcsolatra, ha a jelző "enabled" (engedélyezve) van a kapcsolaton. Ez a jelző akkor látható, ha a felhasználó szerkeszt egy virtuális hálózati kapcsolat, egy VPN-kapcsolat vagy egy ExpressRoute-kapcsolat. Alapértelmezés szerint ez a jelző le van tiltva, ha egy hely vagy egy ExpressRoute áramkör egy hubhoz van csatlakoztatva. Alapértelmezés szerint engedélyezve van, ha egy virtuális hálózati kapcsolat hozzáadásával csatlakozik egy VNet egy virtuális hubhoz. Az alapértelmezett útvonal nem a virtuális WAN-hubhoz származik; a rendszer az alapértelmezett útvonalat propagálja, ha a virtuális WAN-központ már megtanulta a tűzfal központi telepítésének eredményeképpen, vagy ha egy másik csatlakoztatott hely kényszerített bújtatást engedélyez.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Mi a különbség a virtuális WAN-típusok (Basic és standard) között?

Az "alapszintű" WAN-típus lehetővé teszi egy alapszintű központ létrehozását (SKU = Basic). A standard szintű WAN-típus lehetővé teszi A standard hub (SKU = standard) létrehozását. Az alapszintű hubok a helyek közötti VPN-funkciókra korlátozódnak. A standard szintű hubok lehetővé teszik a ExpressRoute, a felhasználói VPN (P2S), a teljes Mesh hub és a VNet-VNet átvitelt a hubokon keresztül. A standard szintű hubok esetében a $0,25/HR alapdíjat kell fizetnie, valamint egy adatfeldolgozási díjat a hubokon keresztüli átvitelhez a VNet-VNet kapcsolat során, valamint a hub és a hub közötti forgalom adatfeldolgozását. További információ: [alapszintű és standard szintű virtuális WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). A díjszabással kapcsolatban tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/virtual-wan/) oldalt.
