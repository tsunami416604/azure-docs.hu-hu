---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/24/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 01ed6d836e5d6bfe139e4a21a0ff6a9708c261d3
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977932"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Szükség van a felhasználónak a hub és az SD-WAN/VPN-eszközök használatára az Azure Virtual WAN használatához?

A Virtual WAN számos funkciót kínál az olyan üvegek számára, mint például a hely/helyek közötti VPN-kapcsolat, a felhasználói/P2S kapcsolatok, a ExpressRoute-kapcsolat, a Virtual Network kapcsolatok, a VPN-ExpressRoute összekapcsolása, a VNET a VNET tranzitív kapcsolat, a központosított útválasztás, az Azure tűzfal és a tűzfal-kezelő biztonság, a monitorozás, a ExpressRoute-titkosítás és számos más képesség A virtuális WAN használatának megkezdéséhez nem szükséges az összes ilyen használati eset. Egyszerűen csak egy használati esettel kezdheti meg az első lépéseket. A virtuális WAN-architektúra egy olyan, a méretezést és a teljesítményt tartalmazó sugaras architektúra, amelyben az ágak (VPN/SD-WAN-eszközök), a felhasználók (Azure VPN-ügyfelek, az openVPN vagy a IKEv2-ügyfelek), a ExpressRoute-áramkörök, a virtuális hálózatok a virtuális központ (ok) nak szólnak. Az összes hub egy szabványos virtuális WAN-kapcsolaton keresztül csatlakozik a teljes hálóhoz, így a felhasználó könnyedén használhatja a Microsoft-gerincet bármilyen (küllős) kapcsolathoz. A hub és az SD-WAN/VPN-eszközök használata esetén a felhasználók manuálisan is beállíthatják az Azure-beli virtuális WAN-portálon, vagy a virtuális WAN-partner CPE (SD-WAN/VPN) használatával állíthatnak be kapcsolatot az Azure-ral. A virtuális WAN-partnerek automatizálást biztosítanak a kapcsolódáshoz, amely lehetővé teszi az eszköz adatainak az Azure-ba való exportálását, az Azure-konfiguráció letöltését és az Azure Virtual WAN hub-kapcsolat létesítését. A pont – hely/felhasználó VPN-kapcsolat esetében az [Azure VPN Client](https://go.microsoft.com/fwlink/?linkid=2117554), az OpenVPN vagy az IKEv2-ügyfél támogatott. 

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Milyen ügyfelet támogat az Azure Virtual WAN felhasználói VPN (pont – hely)?

A Virtual WAN támogatja az [Azure VPN-ügyfelet](https://go.microsoft.com/fwlink/?linkid=2117554), az OpenVPN-ügyfelet vagy bármely IKEv2-ügyfelet. Az Azure AD-hitelesítés támogatott az Azure VPN-ügyfélen. legalább a Windows 10 ügyfél operációs rendszer 17763,0-es vagy újabb verziójára van szükség.  Az OpenVPN-ügyfél képes támogatni a tanúsítványalapú hitelesítést. Miután kiválasztotta a tanúsítvány alapú hitelesítést az átjárón, a. ovpn fájlt fogja látni az eszközre való letöltéshez. A IKEv2 a tanúsítvány és a RADIUS-hitelesítés is támogatott. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Felhasználói VPN (pont – hely) esetén – miért oszlik két útvonalra a P2S-ügyfél készlete?

Minden átjáró két példánnyal rendelkezik, a felosztás úgy történik, hogy az egyes átjáró-példányok egymástól függetlenül lefoglalhatják az ügyfél IP-címeit a csatlakoztatott ügyfelek számára, és a virtuális hálózatról érkező forgalom a megfelelő átjáró-példányra kerül vissza, hogy elkerülje az átjárók közötti példányok ugrását.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Hogyan DNS-kiszolgálókat hozzáadni a P2S-ügyfelekhez?

A P2S-ügyfelekhez két lehetőség van a DNS-kiszolgálók hozzáadására.

1. Nyisson meg egy támogatási jegyet a Microsofttal, és adja hozzá a DNS-kiszolgálókat a hubhoz.
2. Ha a Windows 10 rendszerhez készült Azure VPN-ügyfelet használja, akkor a letöltött profil XML-fájlját is módosíthatja, ** \<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers> ** és az importálás előtt hozzáadhatja a címkéket.

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Felhasználói VPN (pont – hely) esetén – hány ügyfél támogatott?

Minden felhasználói VPN P2S-átjáró két példánnyal rendelkezik, és mindegyik példány támogatja a méretezési egység módosításait. A 1-3-es méretezési egység támogatja a 500-kapcsolatokat, a skálázási egység 4-6 támogatja a 1000-kapcsolatokat, a Scale Unit 7-12 támogatja az 5000-kapcsolatokat és a skálázási egység 13-20 támogatja az akár Tegyük fel például, hogy a felhasználó 1 méretezési egységet választ. Mindegyik skálázási egység egy aktív-aktív átjáró üzembe helyezését jelentené, és a példányok mindegyike (ebben az esetben 2) az 500-os kapcsolatok használatát fogja támogatni. Mivel az 500-es kapcsolatok * 2 átjárón keresztüli elérésére van lehetőség, nem jelenti azt, hogy a 500 helyett a (1000) értéket tervezi a méretezési egységhez, mivel előfordulhat, hogy olyan példányokat kell kiszolgálni, amelyekben a további 500 kapcsolata megszakad, ha túllépi az ajánlott kapcsolatok számát. Ügyeljen arra is, hogy az állásidőt úgy tervezze meg, hogy a méretezési egység vertikális fel-vagy leskálázását választja, vagy a pont – hely konfigurációt a VPN-átjárón módosítsa.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Mi a különbség az Azure Virtual Network Gateway (VPN Gateway) és egy Azure virtuális WAN VPN Gateway között?

A Virtual WAN nagy léptékben biztosít helyek közötti kapcsolatokat, és kifejlesztése során elsősorban az átviteli sebességet, a méretezhetőséget és a könnyű használatot tartották szem előtt. Ha egy helyet egy virtuális WAN VPN-átjáróhoz kapcsolódik, az különbözik a VPN-átjárót használó normál virtuális hálózati átjárótól. Hasonlóképpen, amikor egy ExpressRoute-áramkört csatlakoztat egy virtuális WAN-központhoz, az ExpressRoute-átjáróhoz egy másik erőforrást használ, mint a "ExpressRoute" típusú átjárót használó normál virtuális hálózati átjáró. A virtuális WAN a VPN-és a ExpressRoute esetében akár 20 GB/s összesített átviteli sebességet is támogat. A Virtual WAN a CPE ág-eszközökhöz tartozó partnerekkel való kapcsolat automatizálásával is rendelkezik. A CPE ág eszközei beépített automatizálással rendelkeznek, amely automatikusan kiépíti és csatlakozik az Azure Virtual WAN-hoz. Ezek az eszközök az SD-WAN- és a VPN-partnerek egyre bővülő körétől szerezhetők be. Tekintse meg az [előnyben részesített partnerek listáját](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Miben különbözik a virtuális WAN egy Azure-beli virtuális hálózati átjárótól?

A virtuális hálózati átjáró VPN-je legfeljebb 30 alagútra van korlátozva. Kapcsolatokhoz nagy mennyiségű VPN-forgalmat bonyolító Virtual WAN használata javasolt. Régiónként (Virtual hub) akár 1 000 ág-kapcsolatot is csatlakoztathat, és a központ 20 GB/s-os összesítéssel rendelkezik. A kapcsolatok aktív-aktív alagútnak minősülnek a helyszíni VPN-eszköz és a virtuális központ között. Régiónként egy hubhoz is rendelkezhet, ami azt jelenti, hogy több mint 1 000 ágat tud csatlakoztatni a hubok között.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>A virtuális WAN Gateway méretezési egysége
A méretezési egység egy olyan egység, amely egy átjáró összesített átviteli sebességének kiválasztására van beállítva a virtuális központban. 1 a VPN = 500 Mbps méretezési egysége. 1 skálázási egység, ExpressRoute = 2 GB/s. Például: a VPN 10 skálázási egysége 500 Mbps * 10 = 5 GB/s értéket jelentene.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Mely szolgáltatók (virtuális WAN-partnerek) támogatottak?

Mostanra már számos partner támogatja a teljesen automatizált Virtual WAN-t. További információ: [Virtual WAN-partnerek](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Melyek a Virtual WAN-partnerek automatizálásának lépései?

A partnerek automatizálásának lépéseiért tekintse át [a Virtual WAN-partnerek automatizálásával](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) foglalkozó részt.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Kötelező egy adott partner eszközét használnom?

Nem. Bármely olyan VPN-kompatibilis eszközt használhat, amely megfelel az Azure IKEv2/IKEv1 IPsec-támogatásra vonatkozó követelményeinek.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hogyan csatlakozhatnak a Virtual WAN-partnerek automatikusan az Azure Virtual WAN-hoz?

A szoftveralapú csatlakozási megoldások jellemzően vezérlővel vagy eszközkiépítési központtal kezelik a kompatibilis eszközöket. A vezérlők Azure API-kat is használhatnak az Azure Virtual WAN-hoz való automatikus csatlakozáshoz. Az Automation magában foglalja a fiókirodák feltöltését, az Azure-konfiguráció letöltését, az IPSec-alagutak Azure-beli virtuális hubokba való beállítását, valamint a kapcsolat automatikus beállítását az Azure Virtual WAN-hoz. Ha több száz ága van, a virtuális WAN CPE-partnerekkel való csatlakozás egyszerű, mivel a bevezetési élmény eltartja a nagyméretű IPsec-kapcsolatok beállításának, konfigurálásának és felügyeletének szükségességét. További információ: [Virtual WAN partner Automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).


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

* Ha küllős virtuális hálózatok csatlakozott a központhoz, a küllős virtuális hálózatok továbbra is érvényesek lesznek a kitöltési díjak. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hogyan készülhetnek elő a szolgáltatásra a meglévő partnerek listáján még nem szereplő új partnerek?

Minden virtuális WAN API nyitott API. A technikai megvalósíthatóság értékeléséhez áttekintheti a dokumentációt. Ha bármilyen kérdése van, küldjön egy e-mailt a címre azurevirtualwan@microsoft.com . Ideális esetben a partner olyan eszközzel rendelkezik, amely támogatja az IKEv1 vagy az IKEv2 IPsec-kapcsolatot.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Mi a teendő, ha a virtuális WAN-partnerek listáján nem szerepel a használni kívánt eszköz? Használhatom továbbra is az Azure Virtual WAN VPN-hez való csatlakozáshoz?

Igen, amíg az eszköz támogatja az IPsec-IKEv1 vagy a IKEv2. A virtuális WAN-partnerek automatizálják az eszközről az Azure VPN-végpontokhoz való kapcsolódást. Ez olyan lépéseket tesz szükségessé, mint például az "ág-adatok feltöltése", az "IPsec és a konfiguráció" és a "kapcsolat". Mivel az eszköz nem virtuális WAN-partneri ökoszisztémából származik, az Azure-konfiguráció manuális megkezdése és az eszköz az IPsec-kapcsolat beállításához való frissítése után kell elvégezni a nagy mennyiségű emelést.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Lehetséges Azure Virtual WAN létrehozása Resource Manager-sablon használatával?

Egy virtuális WAN egyszerű konfigurációja egy hubhoz, egy vpnsite pedig egy rövid útmutató [sablon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)használatával hozható létre. A Virtual WAN elsősorban a REST vagy a portálon alapuló szolgáltatás.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Támogatja az Azure Virtual WAN a globális virtuális társhálózatokat? 

A VNet egy másik régióban is kapcsolódhat, mint a virtuális WAN-t.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Megbeszélheti a virtuális hálózatok csatlakoztatott virtuális hubhoz való kommunikációt (V2V-átvitel)?

Igen. A standard szintű virtuális WAN támogatja a vnet, hogy vnet a tranzitív kapcsolatot a virtuális WAN-hubhoz, amelyhez a virtuális hálózatok csatlakozik. A virtuális WAN-terminológiában ezeket az útvonalakat "helyi virtuális WAN VNet Transit"-ként tekintjük át egyetlen régióban lévő virtuális WAN-központhoz csatlakoztatott virtuális hálózatok, és a "globális virtuális WAN VNet Transit" a több virtuális WAN-hubhoz csatlakoztatott virtuális hálózatok több, mint két régióban. A VNet-tranzit a nyilvános előzetes verzióban akár 3 GB-os átviteli sebességet is támogat. Az átviteli sebesség ki lesz bővítve, amikor a globális átvitel megy.

Megjegyzés: jelenleg a V2V Transit előzetes verziójának használatához a VPN GW-t egy virtuális központban kell telepíteni, hogy aktiválja az útválasztási elemeket. Ez a VPN GW nem használatos a V2V átviteli útvonalhoz. Ez egy ismert korlátozás, amely a V2V GA időpontban lesz eltávolítva. A csomópont (ok) VPN Gateway a teljes elindítása után törölhető, mivel nem szükséges a V2V-továbbítási funkciókhoz. 

Bizonyos forgatókönyvek esetében a küllős virtuális hálózatok a helyi vagy globális virtuális WAN VNet-átvitelen kívül közvetlenül [Virtual Network](../articles/virtual-network/virtual-network-peering-overview.md) egymással is összekapcsolódhat. Ebben az esetben a vnet-társítás a virtuális WAN-központon keresztül elsőbbséget élvez a tranzitív kapcsolattal szemben. 

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
A helyszíni eszközökhöz tartozó megoldások forgalmi házirendeket alkalmazhatnak arra, hogy több alagúton keresztül irányítsák a forgalmat az Azure-ba.


### <a name="what-is-global-transit-architecture"></a>Mi az a globális átviteli architektúra?

A globális átviteli architektúrával kapcsolatos információkért lásd: a [globális tranzit hálózati architektúra és a virtuális WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hogyan történik a forgalom átirányítása az Azure gerinchálózatára?

A forgalom a következő mintát követi: ág Device->ISP->Microsoft Network Edge->Microsoft DC (hub VNet) – >Microsoft Network Edge->ISP->ág eszköz

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Ebben a modellben mire van szükség az egyes helyek esetében? Csupán internetkapcsolatra?

Igen. Internetkapcsolat és fizikai eszköz, amely támogatja az IPsec használatát, lehetőleg az integrált [virtuális WAN-partnereinktől](../articles/virtual-wan/virtual-wan-locations-partners.md). Szükség esetén manuálisan is kezelheti a konfigurációt és a kapcsolatot az Azure-hoz a kívánt eszközről.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Hogyan alapértelmezett útvonal (0.0.0.0/0) engedélyezése egy kapcsolatban (VPN, ExpressRoute vagy Virtual Network):

Egy virtuális központ propagálhatja a megtanult alapértelmezett útvonalat egy virtuális hálózat/helyek közötti VPN/ExpressRoute kapcsolatra, ha a jelző "enabled" (engedélyezve) van a kapcsolaton. Ez a jelző akkor látható, ha a felhasználó szerkeszt egy virtuális hálózati kapcsolat, egy VPN-kapcsolat vagy egy ExpressRoute-kapcsolat. Alapértelmezés szerint ez a jelző le van tiltva, ha egy hely vagy egy ExpressRoute áramkör egy hubhoz van csatlakoztatva. Alapértelmezés szerint engedélyezve van, ha egy virtuális hálózati kapcsolat hozzáadásával csatlakozik egy VNet egy virtuális hubhoz. Az alapértelmezett útvonal nem a virtuális WAN-hubhoz származik; a rendszer az alapértelmezett útvonalat propagálja, ha a virtuális WAN-központ már megtanulta a tűzfal központi telepítésének eredményeképpen, vagy ha egy másik csatlakoztatott hely kényszerített bújtatást engedélyez.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Hogyan jelöli ki a virtuális hálózat virtuális központja a különböző hubok útvonalának legjobb elérési útját.

Ha egy virtuális központ több távoli hubhoz is megtudhatja ugyanazt az útvonalat, a következőképpen határozható meg a sorrend.
1. Leghosszabb előtag-egyezés
2. Helyi útvonalak interhub-en keresztül
3. A BGP-en keresztüli statikus útvonalak
4. ExpressRoute (ER) VPN-en keresztül
5. Elérési út hossza

Az ER és az ER közötti átvitel mindig globális elérhetőségen keresztül történik, mert ha a kérelem az egyik központon keresztül érhető el, és a VPN és a távoli elosztóban található, a VPN előnyt élvez a távoli elosztón keresztül, hogy elérje a VPN-en keresztül csatlakoztatott végpontot, vagy a távoli központban


### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Támogatja az IPv6-ot a virtuális WAN-ban?

A Virtual WAN hub és az átjárók nem támogatják az IPv6 protokollt. Ha olyan VNET rendelkezik, amely IPv6-támogatással rendelkezik, és szeretné a VNET a virtuális WAN-hoz csatlakozni, ez a forgatókönyv szintén nem támogatott. 

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Mi a különbség a virtuális WAN-típusok (Basic és standard) között?

Az "alapszintű" WAN-típus lehetővé teszi egy alapszintű központ létrehozását (SKU = Basic). A standard szintű WAN-típus lehetővé teszi A standard hub (SKU = standard) létrehozását. Az alapszintű hubok a helyek közötti VPN-funkciókra korlátozódnak. A standard szintű hubok lehetővé teszik a ExpressRoute, a felhasználói VPN (P2S), a teljes Mesh hub és a VNet-VNet átvitelt a hubokon keresztül. A standard szintű hubok esetében a $0,25/HR alapdíjat kell fizetnie, valamint egy adatfeldolgozási díjat a hubokon keresztüli átvitelhez a VNet-VNet kapcsolat során, valamint a hub és a hub közötti forgalom adatfeldolgozását. További információ: [alapszintű és standard szintű virtuális WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). A díjszabással kapcsolatban tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/virtual-wan/) oldalt.
