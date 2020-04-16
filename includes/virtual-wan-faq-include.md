---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/24/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a2d79391832bbb5424c6d4096eb5c1a597623367
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81421726"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>A felhasználónak rendelkeznie kell hubés beszélt SD-WAN/VPN-eszközök kel az Azure Virtual WAN használatához?

A Virtual WAN számos, egyetlen üvegtáblába épített funkciót kínál, például a hely/webhely közötti VPN-kapcsolatot, a felhasználói/p2S-kapcsolatot, az ExpressRoute-kapcsolatot, a virtuális hálózati kapcsolatot, a VPN ExpressRoute interconnectivity-ot, a vnet-től a vnet-hez tranzitív kapcsolatot, a központi útválasztást, az Azure tűzfal at és a tűzfalkezelő biztonságát, a figyelést, az ExpressRoute-titkosítást és sok más képességet. A Virtual WAN használatának megkezdéséhez nem kell rendelkeznie az összes ilyen használati esetsel. Egyszerűen csak egy használati tokkal kezdheti el. A Virtual WAN architektúra egy hub és küllős architektúra, amely nek méretezése és teljesítménye beépített, ahol ágak (VPN/SD-WAN eszközök), a felhasználók (Azure VPN-ügyfelek, openVPN vagy IKEv2 ügyfelek), ExpressRoute-áramkörök, virtuális hálózatok szolgálnak küllők a virtuális hub (kulcák). Az összes hub teljes hálóban csatlakozik egy szabványos virtuális WAN-ban, így a felhasználó könnyen használhatja a Microsoft gerinchálózatát bármely (bármilyen küllős) kapcsolathoz. Az SD-WAN/VPN-eszközökkel való hub és beszélt eszközök esetében a felhasználók manuálisan beállíthatják az Azure Virtual WAN portálon, vagy a Virtual WAN Partner CPE (SD-WAN/VPN) segítségével állíthatnak be kapcsolatot az Azure-ral. A Virtuális WAN-partnerek automatizálást biztosítanak a kapcsolathoz, amely lehetővé teszi az eszközadatok exportálását az Azure-ba, az Azure-konfiguráció letöltését és az Azure Virtual WAN hubhoz való kapcsolódást. A pont-hely/felhasználó VPN-kapcsolat esetén az [Azure VPN-ügyfelet,](https://go.microsoft.com/fwlink/?linkid=2117554)az OpenVPN- vagy IKEv2 ügyfelet támogatjuk. 

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Milyen ügyfelet támogat az Azure Virtual WAN-felhasználó VPN-je (pontról helyre) ?

A Virtual WAN támogatja az [Azure VPN-ügyfelet,](https://go.microsoft.com/fwlink/?linkid=2117554)az OpenVPN-ügyfelet vagy bármely IKEv2 ügyfelet. Az Azure AD-hitelesítés t an Azure VPN-ügyfél.A Windows 10 ügyfél operációs rendszer 17763.0-s vagy újabb verziójának legalább egy része szükséges.  Az OpenVPN-ügyfél(ök) támogathatják a tanúsítványalapú hitelesítést. Miután a tanúsítványalapú hitelesítés t választotta ki az átjárón, látni fogja a .ovpn fájlt, amelyet le kell töltenie az eszközre. Az IKEv2 a tanúsítványt és a RADIUS-hitelesítést is támogatja. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Felhasználói VPN (Point-to-site)- Miért a P2S ügyfélkészlet két útvonalra van osztva?

Minden átjáró két példányt, a felosztás történik, hogy minden átjárópéldány önállóan lefoglalni az ügyfél IP-k a csatlakoztatott ügyfelek és a virtuális hálózatról a forgalom a megfelelő átjárópéldány, hogy elkerüljék az átjárók közötti példány ugrás.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Hogyan vehetek fel DNS-kiszolgálókat a P2S-ügyfelekhez?

A P2S-ügyfelek DNS-kiszolgálóinak hozzáadására két lehetőség van.

1. Nyisson meg egy támogatási jegyet a Microsofttal, és vegye rá őket, hogy adják hozzá dns-kiszolgálóikat a központhoz
2. Ha a Windows 10-hez való Azure VPN-ügyfelet használja, módosíthatja a letöltött profil XML-fájlját, és ** \<hozzáadhatja a dns-kiszolgálókat>a dnsserver> \< \</dnsserver>\</dnsservers>** címkékimportálása előtt.

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

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Felhasználói VPN (point-to-site)- hány ügyfél támogatott?

Minden egyes felhasználói VPN P2S átjáró két példányt, és minden példány támogatja upto bizonyos felhasználók, mint a méretezési egység változik. Az 1-3-as méretezési egység támogatja az 500 csatlakozást, a Scale unit 4-6 támogatja az 1000 csatlakozást, a Scale unit 7-10 támogatja az 5000 csatlakozást, a 11+-os méretezési egység pedig akár 10 000 kapcsolatot is támogat. Például tegyük fel, hogy a felhasználó 1 méretezési egységet választ. Minden méretezési egység azt jelentené, hogy egy aktív-aktív átjáró üzembe helyezett, és minden példány (ebben az esetben 2) támogatná akár 500 kapcsolatok. Mivel átjárónként 500 kapcsolatot * 2 kaphat, ez nem jelenti azt, hogy az 500 helyett 1000-et tervez az 500-hoz, mivel előfordulhat, hogy ki kell szolgálni, amelynek során az extra 500 kapcsolat megszakadhat, ha meghaladja az ajánlott kapcsolatszámot.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Mi a különbség az Azure virtuális hálózati átjáró (VPN-átjáró) és az Azure Virtual WAN VPN-átjáró között?

A Virtual WAN nagy léptékben biztosít helyek közötti kapcsolatokat, és kifejlesztése során elsősorban az átviteli sebességet, a méretezhetőséget és a könnyű használatot tartották szem előtt. Amikor egy helyet egy Virtuális WAN VPN-átjáróhoz csatlakoztat, az különbözik a hagyományos virtuális hálózati átjárótól, amely "VPN" átjárótípust használ. Hasonlóképpen, amikor egy ExpressRoute-csoportot egy virtuális WAN-elosztóhoz csatlakoztat, az az ExpressRoute-átjáróhoz más erőforrást használ, mint az "ExpressRoute" átjárótípust használó normál virtuális hálózati átjáró. A Virtual WAN legfeljebb 20 Gb/s összesített átviteli sebességet támogat mind a VPN, mind az ExpressRoute esetében. A Virtual WAN a CPE-fiókeszköz-partnerek ökoszisztémájával való kapcsolódásautomatizálással is rendelkezik. A CPE-elágazó eszközök beépített automatizálással rendelkeznek, amely automatikusan rendelkezik az Azure Virtual WAN-hoz. Ezek az eszközök az SD-WAN- és a VPN-partnerek egyre bővülő körétől szerezhetők be. Lásd az [előnyben részesített partnerek listáját](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Miben különbözik a Virtual WAN az Azure virtuális hálózati átjárójától?

A virtuális hálózati átjáró VPN-je 30 alagútra korlátozódik. Kapcsolatokhoz nagy mennyiségű VPN-forgalmat bonyolító Virtual WAN használata javasolt. Régiónként (virtuális központonként) legfeljebb 1000 ági kapcsolatot csatlakoztathat hubonként 20 Gb/s összesítéssel. A kapcsolatok aktív-aktív alagútnak minősülnek a helyszíni VPN-eszköz és a virtuális központ között. Régiónként egy hubot használhat, ami azt jelenti, hogy több mint 1000 ágat csatlakoztathat a hubok között.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Mi az a Virtual WAN gateway scale egység
A méretezési egység egy olyan egység, amely a virtuális hub ban lévő átjáró összesített átviteli valószínűségének kiválasztására van definiálva. 1 skála egység VPN = 500 Mbps . Az ExpressRoute 1 méretezési egysége = 2 Gb/s. Példa: 10 vpn-skálaegység 500 Mbps * 10 = 5 Gb/s-ot jelentene

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Mely eszközszolgáltatók (Virtual WAN-partnerek) támogatottak?

Mostanra már számos partner támogatja a teljesen automatizált Virtual WAN-t. További információ: [Virtual WAN-partnerek](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Melyek a Virtual WAN-partnerek automatizálásának lépései?

A partnerek automatizálásának lépéseiért tekintse át [a Virtual WAN-partnerek automatizálásával](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) foglalkozó részt.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Kötelező egy adott partner eszközét használnom?

Nem. Bármely olyan VPN-kompatibilis eszközt használhat, amely megfelel az Azure IKEv2/IKEv1 IPsec-támogatásra vonatkozó követelményeinek.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hogyan csatlakozhatnak a Virtual WAN-partnerek automatikusan az Azure Virtual WAN-hoz?

A szoftveralapú csatlakozási megoldások jellemzően vezérlővel vagy eszközkiépítési központtal kezelik a kompatibilis eszközöket. A vezérlők Azure API-kat is használhatnak az Azure Virtual WAN-hoz való automatikus csatlakozáshoz. Az automatizálás magában foglalja az ágadatok feltöltését, az Azure-konfiguráció letöltését, az IPSec-alagutak azure virtuális elosztókba való beállítását, valamint a fiókeszköz azure virtual WAN-ra való automatikus beállítását. Ha több száz ága van, a Virtual WAN CPE-partnerek használatával való csatlakozás egyszerű, mert a bevezetési élmény elveszi a nagyméretű IPsec-kapcsolat beállításának, konfigurálásának és kezelésének szükségességét. További információ: [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).


### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hogyan támogatja a Virtual WAN az SD-WAN eszközöket?

A virtuális WAN-partnerek automatizálják az IPsec-kapcsolatot az Azure VPN-végpontokkal. Ha a Virtual WAN-partner egy SD-WAN-szolgáltató, akkor azt kell, hogy az SD-WAN vezérlő kezeli az automatizálásés az IPsec-kapcsolat az Azure VPN végpontok. Ha az SD-WAN-eszköz az Azure VPN helyett saját végpontot igényel bármely saját SD-WAN funkcióhoz, telepítheti az SD-WAN végpontot egy Azure virtuális hálózatban, és együtt létezhet az Azure Virtual WAN-nal.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Módosítja a Virtual WAN a meglévő kapcsolati funkciókat?

A meglévő Azure kapcsolati funkciók nem változnak.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Elérhetővé válnak új Resource Manager-erőforrások a Virtual WAN-hoz?
  
Igen, a Virtual WAN bevezetésével új Resource Manager-erőforrások válnak elérhetővé. További információ: [Áttekintés](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hány VPN-eszköz tud csatlakozni egy hubhoz?

Virtuális központonként akár 1000 kapcsolat is támogatott. Minden kapcsolat négy kapcsolatból áll, és minden kapcsolat két, aktív konfigurációban lévő alagutat támogat. Az alagutak egy Azure virtuális központi vpngateway-ben fejeződnek be.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>A helyszíni VPN-eszköz több központhoz is csatlakoztatható?

Igen. A forgalom a helyszíni eszköztől a legközelebbi Microsoft hálózati peremhálózatig, majd a virtuális központig történik.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Az Azure Virtual WAN-on is üzembe helyezhetem és használhatom kedvenc hálózati virtuális készülékemet (NVA virtuális hálózaton)?

Igen, csatlakoztathatja kedvenc hálózati virtuális berendezése (NVA-ja) virtuális hálózatát az Azure Virtual WAN-hoz. Az első lépésben központ és virtuális hálózat közötti kapcsolattal csatlakoztassa a virtuális hálózati berendezés virtuális hálózatát a központhoz. Ezután hozzon létre egy virtuális központi útvonalat egy következő ugrással a virtuális berendezésre mutatva. A virtuális hub-útvonaltáblára több útvonalat is alkalmazhat. Az NVA virtuális hálózatához csatlakoztatott összes küllőt a virtuális központhoz is csatlakoztatnia kell. Ezzel biztosítható, hogy a küllők virtuálishálózat-útvonalai a helyszíni rendszerekre legyenek propagálva.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Létrehozhatok hálózati virtuális berendezést a virtuális központban?

Hálózati virtuális berendezés (NVA) nem telepíthető virtuális elosztón belül. Azonban létrehozhatja azt egy küllős virtuális hálózat, amely csatlakozik a virtuális hubhoz, és lehetővé teszi, hogy a hub egy útvonalat a cél virtuális hálózat irányítására az NVA IP-cím (a hálózati adapter).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Lehet egy küllővirtuális hálózat rendelkezik egy virtuális hálózati átjáró?

Nem. A küllővirtuális hálózat nem rendelkezhet virtuális hálózati átjáróval, ha az a virtuális hubhoz csatlakozik.

### <a name="is-there-support-for-bgp"></a>Van BGP-támogatás?

Igen, a BGP támogatott. Vpn-hely létrehozásakor megadhatja benne a BGP-paramétereket. Ez azt jelenti, hogy az Azure-ban az adott helyhez létrehozott kapcsolatok engedélyezve lesznek a BGP számára. Továbbá ha nva-val rendelkező virtuális hálózattal rendelkezik, és ha ez az NVA virtuális hálózat egy virtuális WAN-hubhoz volt csatlakoztatva, annak érdekében, hogy az NVA virtuális hálózatról érkező útvonalak megfelelően legyenek meghirdetve, az NVA virtuális hálózathoz csatlakoztatott küllőknek le kell tiltaniuk a BGP-t. Emellett csatlakoztassa ezeket a küllővirtuális hálózatokat a virtuális hub virtuális hálózatához, hogy a küllős virtuális hálózat útvonalai a helyszíni rendszerekre legyenek propagálva.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>A virtuális központban irányíthatom UDR-rel a forgalmat?

Igen, a virtuális csomópont útvonaltáblája segítségével irányíthatja a forgalmat egy virtuális csomópont-útvonaltáblával. Ez lehetővé teszi, hogy az Azure-ban a célvirtuális hálózatok útvonalait egy adott IP-címen keresztül állítsa be (általában az NVA NIC-en keresztül).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Milyen licenc- vagy díjszabási információ érhető el a Virtual WAN-ról?

Igen. Lásd a [Díjszabás](https://azure.microsoft.com/pricing/details/virtual-wan/) oldalt.

### <a name="how-do-i-calculate-price-of-a-hub"></a>Hogyan számíthatom ki egy hub árát?

* Ön fizetne a szolgáltatásokért a központban. Tegyük fel például, hogy 10 ággal vagy helyszíni eszközzel rendelkezik, amelyek az Azure Virtual WAN-hoz való csatlakozáshoz való csatlakozást igényelnek, ami a hub VPN-végpontjaihoz való csatlakozást jelentené. Mondjuk, hogy ez 1 skálaegység VPN = 500 Mbps, ez $0.361/hr. Minden kapcsolat díja $0.05/óra. 10 kapcsolat esetén a szolgáltatás/óra teljes díja $0.361 + $.5/óra lenne. Az Azure-t elhagyó forgalom érti az adatforgalmi díjakat.

* További központi töltés van. Lásd a [Díjszabás](https://azure.microsoft.com/pricing/details/virtual-wan/) oldalt.

* Ha expressroute-átjáró miatt ExpressRoute-áramkörök egy virtuális hubhoz csatlakozik, majd meg kell fizetnie a méretezési egységár. Az ER minden méretezési egysége 2 Gb/s, és minden egyes csatlakozási egységet a VPN-kapcsolati egységgel azonos sebességgel számítunk fel.

* Ha a küllők VNET-ek csatlakoztak a hubhoz, a küllők vnet-jein lévő társviszony-létesítési díjak továbbra is érvényesek. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hogyan készülhetnek elő a szolgáltatásra a meglévő partnerek listáján még nem szereplő új partnerek?

Minden virtuális WAN API nyitott API. A dokumentációt átveheti a műszaki megvalósíthatóság értékeléséhez. Ha bármilyen kérdése van, küldjön egy e-mailt a . azurevirtualwan@microsoft.com Ideális esetben a partner olyan eszközzel rendelkezik, amely támogatja az IKEv1 vagy az IKEv2 IPsec-kapcsolatot.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Mi a teendő, ha egy általam használt eszköz nem szerepel a Virtuális WAN-partnerek listáján? Továbbra is használhatom az Azure Virtual WAN VPN-hez való csatlakozáshoz?

Igen, amíg az eszköz támogatja az IPsec IKEv1 vagy IKEv2.Yes as as the device supports IPsec IKEv1 or IKEv2. A virtuális WAN-partnerek automatizálják a kapcsolatot az eszközről az Azure VPN-végpontokra. Ez olyan lépések automatizálását jelenti, mint a "fiókadatok feltöltése", az "IPsec és konfiguráció" és a "kapcsolat". Mivel az eszköz nem egy Virtuális WAN-partner-ökoszisztémából származik, az Azure-konfiguráció manuális átvételének és az iPsec-kapcsolat beállításához az eszköz frissítésének nehéz elvégezése szükséges.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Lehetséges Azure Virtual WAN létrehozása Resource Manager-sablon használatával?

Egy virtuális WAN egyszerű konfigurációja egy hubbal és egy vpnsite-nal egy [gyorsindítási sablon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)használatával hozható létre. A Virtual WAN elsősorban REST vagy portal alapú szolgáltatás.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Támogatja az Azure Virtual WAN a globális virtuális társhálózatokat? 

A virtuális hálózat a virtuális WAN-tól eltérő régióban is csatlakoztatható.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>A virtuális hubhoz csatlakoztatott küllővirtuális hálózatok kommunikálhatnak egymással (V2V-átvitel)?

Igen. Standard Virtual WAN támogatja a virtuális hálózat és a virtuális hálózat tranzitív kapcsolat a virtuális WAN hub, amely a virtuális hálózatok csatlakoznak. A Virtual WAN terminológiában ezeket az elérési utakat "helyi virtuális WAN virtuális hálózat-átvitelnek" nevezzük egy virtuális wan-elosztóhoz egyetlen régión belül, és "globális virtuális WAN virtuális hálózati átvitelt" a virtuális hálózatokhoz, amelyek több virtuális WAN-csomóponton keresztül, két vagy több régióban keresztül csatlakoznak. A virtuális hálózatok átvitele legfeljebb 3 Gb/s átviteli sebességet támogat a nyilvános előzetes verzióban. Átviteli lesz kibővült, ha a globális átvitel megy GA.

MEGJEGYZÉS: Jelenleg a V2V-átvitel előzetes verziójához egy VPN GW-t kell telepíteni egy virtuális központban az útválasztási elemek elindításához. Ez a VPN GW nem használható a V2V tranzitútvonalhoz. Ez egy ismert korlátozás, és el kell távolítani idején V2V GA. A VPN-átjáró a hub(ok)on keresztül teljes indításután törölhető, mivel a V2V-átvitel funkcióhoz nincs szükség. 

Bizonyos esetekben a küllős virtuális hálózatok is közvetlenül társviszonyviszonyt létesítési egymással [a virtuális hálózati társviszony-létesítés](../articles/virtual-network/virtual-network-peering-overview.md) mellett a helyi vagy globális virtual WAN virtuális hálózat átvitel. Ebben az esetben a virtuális hálózati társviszony-létesítés elsőbbséget élvez a virtual WAN hubon keresztül i tranzitív kapcsolattal szemben. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Mi az Azure Virtual WAN fiókkapcsolata?

Egy ágeszközről az Azure Virtual WAN-ba való kapcsolat legfeljebb négy kapcsolatot támogat. A kapcsolat a fizikai kapcsolat kapcsolat a fióktelep helyén (például: ATT, Verizon stb.). Minden kapcsolatkapcsolat két aktív/aktív IPsec-alagútból áll.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A Virtual WAN-ban engedélyezett az ágak közötti kapcsolat?

Igen, az ágak közötti kapcsolat a Virtual WAN-ban VPN- és VPN–ExpressRoute kapcsolat esetén is elérhető.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Az ág-ág forgalom áthalad az Azure Virtual WAN-on?

Igen.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>A Virtual WAN minden helyről expressroute-ot igényel?

Nem, a Virtual WAN nem igényli minden hely esetében az ExpressRoute használatát. Szabványos IPsec-alapú hely-hely kapcsolatot használ az eszközről az Azure Virtual WAN hubra mutató internetes kapcsolatokon keresztül. Előfordulhat, hogy a helyek ExpressRoute-kapcsolatcsoporttal csatlakoznak a szolgáltatói hálózathoz. Az ExpressRoute használatával egy virtuális hubon csatlakoztatott helyek esetében a VPN és az ExpressRoute közötti ágforgalom-forgalom ága is lehet.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Az Azure Virtual WAN használatakor vonatkozik korlátozás a hálózat átviteli sebességére?

Az ágak száma hubonként/régiónként 1000 kapcsolatra és összesen 20 Gb/s-os fiókra korlátozódik a hubonként. Régiónként 1 hub lehet.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Hány VPN-kapcsolatot támogat egy virtuális WAN-központ?

Egy Azure Virtual WAN hub egyszerre legfeljebb 1000 S2S-kapcsolatot, 10 000 P2S-kapcsolatot és 4 ExpressRoute-kapcsolatot támogathat.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Mi a VPN-alagút és a kapcsolat teljes VPN-átviteli-eredménye?

Egy hub teljes VPN-átviteli sebessége legfeljebb 20 Gb/s a kiválasztott méretezési egység alapján. Az átviteli átaputét az összes meglévő kapcsolat megosztja. A kapcsolat minden alagútja legfeljebb 1 Gb/s-ot támogathat.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Nem látom a 20 Gbps-os beállítást a portálon a virtuális hub. Hogyan tudom beállítani, hogy?

Keresse meg a VPN-átjárót a portál egyik hubján belül, és kattintson a méretezési egységre a megfelelő beállítás módosításához.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A Virtual WAN lehetővé teszi, hogy a helyszíni eszköz több internetszolgáltatót használjon párhuzamosan, vagy mindig egyetlen VPN-alagútról van szó?

A virtuális WAN VPN-be érkező kapcsolat mindig aktív-aktív alagút (az ugyanazon hubon/régión belüli rugalmassághoz) az ágban elérhető hivatkozás használatával. Ez a kapcsolat lehet egy isp-kapcsolat a helyszíni ágban. Virtual WAN "VPNSite" lehetővé teszi, hogy adjunk link információkat a helyszínen. Ha több internetszolgáltatóval rendelkezik az ágban, és mindegyik internetszolgáltató megadott egy hivatkozást, akkor ez az információ beállítható az Azure-beli VPN-helyadatokban. Az ág internetszolgáltatói közötti feladatátvétel kezelése azonban teljesen elágazás-központú útválasztási művelet.

### <a name="what-is-global-transit-architecture"></a>Mi a globális tranzitarchitektúra?

A globális tranzitarchitektúráról a [Globális tranzithálózati architektúra és](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)a Virtual WAN című témakörben talál további információt.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hogyan történik a forgalom átirányítása az Azure gerinchálózatára?

A forgalom a mintát követi: ágeszköz ->ISP->Microsoft hálózati él>Microsoft DC (hub VNet)->Microsoft hálózati él->ISP->ágeszköz

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Ebben a modellben mire van szükség az egyes helyek esetében? Csupán internetkapcsolatra?

Igen. Internetkapcsolat és fizikai eszköz, amely támogatja az IPsec-et, lehetőleg integrált [Virtual WAN partnereinktől.](../articles/virtual-wan/virtual-wan-locations-partners.md) Szükség esetén manuálisan kezelheti a konfigurációt és az Azure-hoz való kapcsolódást az előnyben részesített eszközről.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Hogyan engedélyezhetem az alapértelmezett útvonalat (0.0.0.0/0) egy kapcsolatban (VPN, ExpressRoute vagy Virtuális hálózat):

A virtuális elosztók a megtanult alapértelmezett útvonalat virtuális hálózatra/helyekről helyekre VPN/ExpressRoute-kapcsolatra terjeszthetik, ha a jelző "Engedélyezve" a kapcsolaton. Ez a jelző akkor látható, ha a felhasználó virtuális hálózati kapcsolatot, VPN-kapcsolatot vagy ExpressRoute-kapcsolatot jelöl. Alapértelmezés szerint ez a jelző le van tiltva, ha egy hely vagy egy ExpressRoute-kapcsolat hubhoz csatlakozik. Alapértelmezés szerint engedélyezve van, ha virtuális hálózati kapcsolatot ad hozzá egy virtuális hálózat virtuális elosztóhoz való csatlakoztatásához. Az alapértelmezett útvonal nem a Virtual WAN hubból származik; az alapértelmezett útvonal at propagálja a rendszer, ha azt a Virtual WAN hub már megtanulta a tűzfal központi telepítésének eredményeként, vagy ha egy másik csatlakoztatott hely kényszerített bújtatása engedélyezve van.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Hogyan választja ki a virtuális WAN virtuális központja a legjobb útvonalat egy útvonalhoz több hubról?

Ha egy virtuális központ ugyanazt az útvonalat több távoli hubról tanulja meg, a rendezési sorrend a következő:
1) Route Origin a) Hálózati útvonalak – A Virtuális központ átjárói által közvetlenül megtanult VNET-előtagok b) BGP c) Hub RouteTable (statikusan konfigurált útvonalak) d) InterHub-útvonalak
2)  Útvonal-metrika: A Virtual WAN az ExpressRoute-ot részesíti előnyben a VPN-nel szemben. Az ExpressRoute-társ nagyobb súlyú, mint a VPN-társ
3)  AS görbe hossza

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Mi a különbség a Virtual WAN típusok (Alap- és Standard)között?

Az "Alapszintű" WAN-típus lehetővé teszi egy alapvető hub (SKU = Basic) létrehozását. A "Standard" WAN típus lehetővé teszi a szabványos hub létrehozását (SKU = Standard). Az alapvető elosztók a helyek közötti VPN-funkciókra korlátozódnak. A szabványos elosztók lehetővé teszik az ExpressRoute, a Felhasználói VPN (P2S), a teljes hálóhub és a virtuális hálózat között a csomópontokon keresztül történő továbbítást. A szabványos csomópontokért 0,25 USD alapdíjat kell fizetnie, és adatfeldolgozási díjat kell fizetnie a csomópontokon való átvitelért a virtuális hálózat és a virtuális hálózat közötti kapcsolat során, valamint adatfeldolgozást a hub és a hub közötti forgalom számára. További információ: [Basic and Standard virtual WANs](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Az árakról az [Árképzés](https://azure.microsoft.com/pricing/details/virtual-wan/) oldalon találja.
