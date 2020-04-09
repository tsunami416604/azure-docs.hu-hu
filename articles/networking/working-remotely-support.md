---
title: Az Azure hálózati szolgáltatásainak távoli használata
description: Ez a lap bemutatja, hogyan használhatja az Azure hálózati szolgáltatások, amelyek rendelkezésre állnak, hogy a távoli munka engedélyezéséhez, és hogyan mérsékelheti a forgalmi problémák eredő megnövekedett számú ember dolgozik távolról.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: e0e5806ec59cd2d75111db213d8511488d043eec
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982854"
---
# <a name="working-remotely-using-azure-networking-services"></a>Az Azure hálózati szolgáltatásainak távoli használata

>[!NOTE]
> Ez a cikk bemutatja, hogyan használhatja ki az Azure hálózati szolgáltatásait, a Microsoft-hálózatot és az Azure-partneri ökoszisztémát a távoli munkavégzéshez és a COVID-19-es válság miatt esetleg felmerülő hálózati problémák csökkentéséhez.

Ez a cikk azokat a lehetőségeket ismerteti, amelyek a szervezetek számára elérhetők a felhasználók távelérésének beállításához vagy meglévő megoldásaik további kapacitással való kiegészítéséhez a csúcskihasználtság időszakában. A hálózati építészek a következő kihívásokkal szembesülnek:

- A hálózati kihasználtság növekedésének kezelése.
- Megbízható és biztonságos kapcsolatot biztosítanak vállalatuk és ügyfeleik több alkalmazottjának.
- Biztosítson kapcsolatot a távoli helyekre szerte a világon.

Nem minden hálózat (például a privát WAN és a vállalati alaphálózatok) tapasztal torlódást a távoli munkavégző maximális terhelésétől. A szűk keresztmetszeteket általában csak az otthoni szélessávú hálózatokban és a vállalatok helyszíni hálózatainak VPN-átjáróiban jelentik.

A hálózattervezők segíthetnek enyhíteni a szűk keresztmetszeteket és enyhíteni a hálózati torlódásokat azáltal, hogy szem előtt tartják, hogy a különböző forgalomtípusoknak különböző hálózati kezelési prioritásokra van szükségük, valamint néhány intelligens terhelés-átirányításra/-elosztásra. Például az orvos-beteg interakció valós idejű tele-medecine forgalma nagy jelentőséggel bír, és a késleltetés/vibrezés érzékeny. Mivel a tárolók közötti forgalom replikációja nem késlelteti az érzékeny adatokat. A korábbi forgalmat a legoptimálisabb, jobb minőségű hálózati útvonalon kell irányítani; mivel elfogadható, hogy a későbbi forgalmat az optimálistól elmaradó útvonalon irányítsák.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Bevált gyakorlataink megosztása – Az Azure-hálózat rugalmasságra és magas rendelkezésre állásra lett tervezve

Az Azure-t úgy tervezték, hogy ellenálljon az erőforrások kihasználtságában bekövetkező hirtelen változásoknak, és nagyban segíthet a csúcskihasználtság időszakában. Emellett a Microsoft fenntartja és működteti a világ egyik legnagyobb hálózatát. A Microsoft hálózatát úgy tervezték, hogy magas rendelkezésre állásnak feleljen meg, amely ellenáll a különböző típusú hibáknak: egyetlen hálózati elem meghibásodásától a teljes régió meghibásodásáig.

A Microsoft-hálózatot úgy tervezték, hogy megfeleljen a követelményeknek, és optimális teljesítményt nyújtson a különböző típusú hálózati forgalomhoz, beleértve a Skype és a Teams, a CDN, a valós idejű big data elemzés, az Azure-tárhely, a Bing és az Xbox érzékeny multimédiás forgalmát. Annak érdekében, hogy optimális teljesítményt biztosítson a különböző típusú forgalom számára, a Microsoft hálózat a forgalom eredetéhez a lehető legközelebb vonzza az összes olyan forgalmat, amely az erőforrásokon keresztül kíván áthaladni.

>[!NOTE] 
>Az alábbiakban ismertetett Azure hálózati funkciók használata kihasználja a Microsoft globális hálózatának forgalomvonzatási viselkedését, hogy jobb ügyfélhálózati élményt nyújtson. A Microsoft-hálózat forgalomvonzási viselkedése segít a lehető leghamarabb kikapcsolni a forgalmat az első/utolsó mérföldes hálózatokról, amelyek a csúcskihasználtság időszakában torlódást tapasztalhatnak.
>

## <a name="enable-employees-to-work-remotely"></a>Az alkalmazottak távolról történő munkavégzésének engedélyezése

Az Azure VPN-átjáró támogatja mind a point-to-site (P2S) és a helyek közötti (S2S) VPN-kapcsolatok. Az Azure VPN-átjáró használatával skálázhatja az alkalmazott kapcsolatait, hogy biztonságosan hozzáférhessen az Azure üzembe helyezett erőforrásaihoz és a helyszíni erőforrásokhoz. További információt a [Felhasználók távoli munkavégzésének engedélyezése](../vpn-gateway/work-remotely-support.md)című témakörben talál. 

Ha SSTP protokollt használ, az egyidejű kapcsolatok száma 128-ra korlátozódik. Ahhoz, hogy nagyobb számú kapcsolatot kapjon, javasoljuk, hogy váltson openvpn vagy IKEv2 kapcsolatra. További információ: [Áttérés openVPN protokollra vagy IKEv2 az SSTP protokollból című témakörben.](../vpn-gateway/ikev2-openvpn-from-sstp.md
)

Az Azure-ban üzembe helyezett erőforrások eléréséhez a távoli fejlesztők a VPN-kapcsolat helyett azure-bastion-megoldást használhatnak a biztonságos shell access (RDP vagy SSH) eléréséhez anélkül, hogy nyilvános IP-címeket kellene elérni ük a virtuális gépeken. További információ: [Munka távolról az Azure Bastion használatával](../bastion/work-remotely-support.md)című témakörben.

A nagyméretű VPN-kapcsolatok összesítéséhez, a különböző helyszíni globális helyeken, különböző regionális csomópontokban és küllővirtuális hálózatokban lévő erőforrások közötti kapcsolatok támogatásához, valamint több otthoni szélessávú hálózat kihasználásának optimalizálásához használhatja az Azure Virtual WAN-t. További információ: [Küzd, hogy ellát-hoz dolgozik otthonról kell igényelni? Itt tud segíteni az Azure Virtual WAN.](../virtual-wan/work-remotely-support.md)

A távoli munkaerő támogatásának másik módja az Azure virtuális hálózatában üzemeltetett virtuális asztali infrastruktúra (VDI) üzembe helyezése, amelyet egy Azure-tűzfal biztosít. A Windows virtuális asztal (WVD) például egy asztali és alkalmazásvirtualizációs szolgáltatás, amely az Azure-ban fut. A Windows Virtual Desktop segítségével méretezhető és rugalmas környezetet állíthat be az Azure-előfizetésében anélkül, hogy további átjárókiszolgálókat kellene futtatnia. Csak a virtuális hálózat WVD virtuális gépeiért felelős. További információ: [Azure Firewall remote work support](../firewall/remote-work-support.md). 

Az Azure-nak számos környezetvédelmi rendszerpartnere is van. Partnereink hálózati virtuális készülékek az Azure-ban is segíthet a VPN-kapcsolat méretezése. További információ: [Network Virtual Appliance (NVA) a távoli munkával kapcsolatos szempontok.](../vpn-gateway/nva-work-remotely-support.md)

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Az alkalmazottak kapcsolatának kiterjesztése a globálisan elosztott erőforrások eléréséhez

A következő Azure-szolgáltatások segítségével az alkalmazottak hozzáférhetnek a globálisan elosztott erőforrásokhoz. Az erőforrások bármelyik Azure-régióban, a helyszíni hálózatokban, vagy akár más nyilvános vagy privát felhőkben lehetnek. 

- **Azure virtuális hálózati társviszony-létesítés:** Ha az erőforrásokat több Azure-régióban telepíti, és/vagy ha több virtuális hálózatot használó, távolról dolgozó alkalmazottak kapcsolatát összesíti, virtuális hálózati társviszony-létesítéssel kapcsolatot létesíthet a több Azure virtuális hálózat között. További információt a [Virtuális hálózati társviszony-létesítés][VNet-peer]című témakörben talál.

- **Azure VPN-alapú megoldás:** A P2S-en vagy S2S-VPN-en keresztül az Azure-hoz csatlakoztatott távoli alkalmazottak számára engedélyezheti a helyszíni hálózatokhoz való hozzáférést az S2S VPN helyszíni hálózatok és az Azure VPN-átjáró közötti konfigurálásával. További információt a Helyek közötti kapcsolat létrehozása című témakörben [talál.][S2S]

- **ExpressRoute:** Az ExpressRoute privát társviszony-létesítéshasználatával engedélyezheti a privát kapcsolatot az Azure-telepítések és a helyszíni infrastruktúra vagy az infrastruktúra egy helymegosztási létesítményben. ExpressRoute, a Microsoft társviszony-létesítés, is lehetővé teszi a nyilvános végpontok eléréséhez a Microsoft a helyszíni hálózaton. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Biztonságos kapcsolatot, megbízhatóságot, nagyobb átviteli teljesítményt kínálnak, alacsonyabb és konzisztens késést, mint az interneten keresztüli tipikus kapcsolatok. További információkat az [ExpressRoute áttekintésében][ExR] találhat. Az [ExpressRoute-partneri ökoszisztémánk][ExR-eco] részét már része a meglévő hálózati szolgáltató kihasználása segíthet csökkenteni a Microsofttal való nagy sávszélességű kapcsolatok megszerzéséhez szükséges időt.  Az [ExpressRoute Direct][ExR-D] használatával közvetlenül csatlakoztathatja a helyszíni hálózatot a Microsoft gerinchálózatához. ExpressRoute Direct kínál két különböző vonal-díj lehetőségek kettős 10 Gb/s vagy 100 Gbps. 

- **Azure Virtual WAN:** Az Azure Virtual WAN zökkenőmentes interoperabilitást tesz lehetővé a VPN-kapcsolatok és az ExpressRoute-áramkörök között. Ahogy korábban említettük, az Azure Virtual WAN támogatja a különböző globális helyeken, különböző regionális csomópontokban és küllős virtuális hálózatokban lévő erőforrások közötti kapcsolatokat is.

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Ügyfélkapcsolat méretezése előtér-erőforrásokhoz

Azokban az időkben, amikor több ember megy online, sok vállalati weboldalak tapasztalat fokozott ügyfélforgalom. Az Azure Application Gateway segíthet kezelni ezt a megnövekedett előtér-számítási feladatot. További információ: [Application Gateway high traffic support](../application-gateway/high-traffic-support.md).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Microsoft-támogatás a felhőalapú forgalomhoz

A más nyilvános felhőkben történő központi telepítések esetén a Microsoft globális kapcsolatot biztosíthat. Az Azure Virtual WAN, VPN vagy ExpressRoute segíthet ebben a tekintetben. Az Azure-ból más felhőkre való kapcsolat kiterjesztéséhez konfigurálhatja az S2S VPN-t a két felhő között. Az ExpressRoute használatával kapcsolatot is létesíthet az Azure-ból más nyilvános felhőkre. Az Oracle-felhő az ExpressRoute-partneri ökoszisztéma része. Közvetlen [kapcsolatot állíthat be az Azure és az Oracle Cloud Infrastructure között.][Az-OCI] Az ExpressRoute-partneri ökoszisztéma részét betöltő legtöbb szolgáltató más nyilvános felhőkhöz is privát kapcsolatot kínál. Kihasználva ezeket a szolgáltatókat, létrehozhat privát kapcsolatot az Azure-ban és más felhők között az ExpressRoute-on keresztül.

## <a name="next-steps"></a>További lépések

Az alábbi cikkek ismertetik, hogy a különböző Azure hálózati funkciók hogyan használhatók a felhasználók távoli munkára való méretezésére:

| **Cikk** | **Leírás** |
| --- | --- |
| [A felhasználók távolról történő munkavégzésének engedélyezése](../vpn-gateway/work-remotely-support.md) | Tekintse át a rendelkezésre álló lehetőségeket a felhasználók távelérésének beállításához, vagy meglévő megoldásaik további kapacitással való kiegészítéséhez a szervezet számára.|
| [Küzd, hogy ellát a munka otthonról kell? Itt segíthet az Azure Virtual WAN](../virtual-wan/work-remotely-support.md) | Használja az Azure Virtual WAN-t a szervezet távoli kapcsolódási igényeinek kielégítéséhez.|
| [Nagy mennyiségű forgalom támogatása az Application Gatewayben](../application-gateway/high-traffic-support.md) | Az Application Gateway webalkalmazás-tűzfallal (WAF) skálázható és biztonságos módon kezelheti a webalkalmazások forgalmát. |
| [A hálózati virtuális berendezés (NVA) szempontjai a távoli munkához](../vpn-gateway/nva-work-remotely-support.md)|Tekintse át az Azure-beli nva-k használatával kapcsolatos útmutatást a távelérési megoldások biztosításához. |
| [Áttérés openVPN protokollra vagy IKEv2-re az SSTP-ből](https://go.microsoft.com/fwlink/?linkid=2124112) | Az OpenVPN protokollra vagy az IKEv2 protokollra való áttéréssel az SSTP 128 egyidejű kapcsolati korlátjának leküzdése.|
| [Az Azure Bastion távoli használata](../bastion/work-remotely-support.md) | Biztonságos és zökkenőmentes RDP/SSH-kapcsolatot biztosíthat az Azure virtuális hálózaton belüli virtuális gépekhez, közvetlenül az Azure Portalon, nyilvános IP-cím használata nélkül. |
| [Hibrid kapcsolat létrehozása az Azure ExpressRoute használatával a távoli felhasználók támogatása érdekében](../expressroute/work-remotely-support.md) | Az ExpressRoute használatával hibrid kapcsolattal engedélyezheti a szervezet felhasználóiszámára a távoli munkavégzést.|
| [Az Azure Firewall távoli munkahelyi támogatása](../firewall/remote-work-support.md)|Az Azure tűzfal használatával megvédheti az Azure virtuális hálózati erőforrásait. |

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
