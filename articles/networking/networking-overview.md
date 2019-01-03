---
title: Az Azure-hálózatok |} A Microsoft Docs
description: Ismerje meg az Azure hálózati szolgáltatásokat és képességeket.
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: f21d92dabfcfbe51cf8135388a1ab489c20593a4
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537545"
---
# <a name="azure-networking"></a>Az Azure-hálózatok

Az Azure biztosít a különböző hálózati funkciói, amelyek együtt vagy külön-külön is használhatók. Kattintson bármelyik további információkat olvashat a következő főbb képességei:
- [Azure-erőforrások közötti kapcsolat](#connectivity): Csatlakozás Azure-erőforrások együtt, a felhőben egy biztonságos, privát virtuális hálózatot.
- [Internetkapcsolat](#internet-connectivity): Az Azure-erőforrások az interneten keresztül kommunikálnak.
- [Helyszíni kapcsolatok](#on-premises-connectivity): A helyszíni hálózat csatlakoztatása az Azure-erőforrások virtuális magánhálózat (VPN) az interneten keresztül, vagy az Azure-bA egy dedikált kapcsolaton keresztül.
- [Terheléselosztás és az adatforgalom irányának betöltése](#load-balancing): Bejövő forgalmának terheléselosztása kiszolgálók a ugyanarra a helyre és a forgalom különböző helyeken lévő kiszolgálókra.
- [Biztonsági](#security): Hálózati alhálózatokhoz vagy az egyes virtuális gépek (VM) közötti hálózati forgalom szűrése.
- [Útválasztás](#routing): Használja az alapértelmezett útválasztását, vagy a teljes körűen szabályozhatja az Azure és helyszíni erőforrások között.
- [Kezelhetőségi](#manageability): Figyelheti, és az Azure hálózati erőforrások kezeléséhez.
- [Telepítési és konfigurációs eszközök](#tools): Egy olyan webes portál vagy a többplatformos parancssori eszközök segítségével telepítheti és konfigurálhatja a hálózati erőforrásokhoz.

## <a name="Connectivity"></a>Azure-erőforrások közötti kapcsolat

Például a Virtual Machines, Cloud Services, Virtual Machines Scale Sets és az Azure App Service Environment-környezetek Azure-erőforrások is privát módon kommunikáljanak egymással egy Azure virtuális hálózaton (VNet) keresztül. Virtuális hálózat egy logikai elkülönítés az Azure felhő számára kijelölt a [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Minden egyes Azure-előfizetésen belül több virtuális hálózat és az Azure valósíthat meg [régió](https://azure.microsoft.com/regions). Minden egyes virtuális hálózat el különítve a többi virtuális hálózatok. Az egyes virtuális hálózatok a következőket teheti:

- Megadhat egy egyéni magánhálózati IP-címteret nyilvános és magánhálózati (RFC 1918) címek használatával. Hozzárendelése az Azure-erőforrások csatlakozott a virtuális hálózathoz magánhálózati IP-cím hozzárendelése a címtér.
- A virtuális hálózat szegmentálása egy vagy több alhálózatra, és foglaljon le egy minden egyes alhálózathoz a virtuális hálózat címtere része.
- Használja az Azure által biztosított névfeloldást, vagy adja meg a saját DNS-kiszolgáló használatra egy virtuális hálózathoz csatlakozó erőforrás.

Az Azure Virtual Network szolgáltatással kapcsolatos további tudnivalókért olvassa el a [virtuális hálózatok áttekintése](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk. Kapcsolódás virtuális hálózatok egymáshoz, erőforrások kommunikálhassanak egymással virtuális hálózatokat vagy a vneten engedélyezése. Használhatja az alábbi beállítások egyikében vagy egymáshoz virtuális hálózatok csatlakoztatása:

- **Társviszony-létesítés:** Lehetővé teszi a különböző Azure virtuális hálózat azonos Azure-régióban kommunikálnak egymással a kapcsolódó erőforrásokhoz. A sávszélességet és a virtuális hálózatok közötti késés megegyezik, mintha az erőforrásokat ugyanabban a Vnetben csatlakoztatva. Társviszony-létesítés kapcsolatos további információkért olvassa el a [virtuális hálózati társviszony-létesítési áttekintése](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **VPN-átjáró:** Lehetővé teszi az erőforrások különböző Azure vnetekhez csatlakoztatni az Azure régiókon belül kommunikálni egymással. Virtuális hálózatok közötti forgalom halad keresztül az Azure VPN Gateway. A sávszélesség, az átjáró virtuális hálózatok közötti sávszélesség korlátozódik. Virtuális hálózatok csatlakoztatása VPN-átjáróval kapcsolatos további információkért olvassa el a [VNet – VNet kapcsolat konfigurálása a régiók közötti](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

## <a name="internet-connectivity"></a>Internetkapcsolat

Egy virtuális hálózathoz csatlakozó összes Azure-erőforrások alapértelmezés szerint az internetre irányuló kimenő kapcsolattal rendelkezik. A magánhálózati IP-cím az erőforrás az adatforrás hálózati cím lefordított (SNAT) által az Azure-infrastruktúra nyilvános IP-címre. Kimenő internetkapcsolattal kapcsolatos további információkért olvassa el a [az Azure kimenő kapcsolatainak ismertetése](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

Bejövő kommunikációt az Azure-erőforrásokhoz az internetről, vagy kimenő kommunikációját az SNAT az interneten, kell egy erőforráshoz hozzárendelt nyilvános IP-cím. Nyilvános IP-címek kapcsolatos további információkért olvassa el a [nyilvános IP-címek](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

## <a name="on-premises-connectivity"></a>Helyszíni kapcsolatok

Erőforrások a virtuális hálózatban található biztonságos kapcsolaton keresztül érheti el a VPN-kapcsolatot, vagy egy közvetlen, privát kapcsolat. Az Azure virtuális hálózat és a helyszíni hálózat közötti hálózati adatforgalom elküldésére, létre kell hoznia egy virtuális hálózati átjárót. Az átjáró, a típusú kapcsolat létrehozásához használni kívánt VPN- vagy ExpressRoute beállításokat konfigurál.

A helyszíni hálózat csatlakozhat egy Vnethez a következő lehetőségek közül:

**Pont – hely (az SSTP VPN)**

Az alábbi képen látható külön, mutasson a hely kapcsolatok több számítógép és a egy virtuális hálózat között:

![Pont–hely kapcsolat](./media/networking-overview/point-to-site.png)

Ez a kapcsolat egy egyedi számítógép és a egy virtuális hálózat között jön létre. Ez a kapcsolattípus remek választás, ha csak most ismerkedik az Azure szolgáltatással, illetve a fejlesztők számára, mert a meglévő hálózatot csak kis mértékben vagy egyáltalán nem kell módosítani. Egyben kényelmes, amikor csatlakozik egy távoli helyen, például egy konferencia- vagy otthoni. Pont – hely kapcsolatok gyakran összekapcsolt hely – hely kapcsolattal a ugyanazon a virtuális hálózati átjárón keresztül. A kapcsolat az SSTP protokollt használja, titkosított kommunikációt biztosít a számítógép és a virtuális hálózat között az interneten keresztül. A késés, a pont – hely VPN-hez már előre nem látható, mert az a forgalom az interneten halad át.

**Site-to-site (IPsec/IKE VPN-alagút)**

![Helyek közötti kapcsolat](./media/networking-overview/site-to-site.png)

Ezt a kapcsolatot a helyszíni VPN-eszköz és a egy Azure VPN Gateway átjáró között. Ez a kapcsolattípus lehetővé teszi, hogy bármilyen helyszíni erőforráshoz, hogy elérjék a virtuális hálózat eléréséhez. A kapcsolat egy IPSec/IKE VPN, amely titkosított kommunikációt biztosít a helyszíni eszközök és az Azure VPN gateway között az interneten. Több helyszíni helyhez csatlakozhatnak a VPN-átjáróhoz. Egyes helyeken a helyszíni VPN-eszköznek rendelkeznie kell egy kifelé irányuló nyilvános IP-címet, amely nem NAT mögött. A késést, helyek közötti kapcsolat nem határozható meg előre, mivel az a forgalom az interneten halad át.

**ExpressRoute (dedikált privát kapcsolat)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Ez a kapcsolattípus jön létre a hálózat és az Azure között egy ExpressRoute-partneren keresztül. Ez a kapcsolat nem nyilvános. Forgalom sem halad át az interneten. A késés, az ExpressRoute-kapcsolatok az már előre jelezhető, mert a forgalom nem haladnak át az interneten. Az ExpressRoute kombinálható egy helyek közötti kapcsolat.

Az előző kapcsolat lehetőségekkel kapcsolatos további tudnivalókért olvassa el a [kapcsolati topológia-diagramok](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

## <a name="load-balancing"></a>Terheléselosztás és az adatforgalom irányának betöltése

A Microsoft Azure több szolgáltatásokat kínál a hálózati forgalom hogyan ossza kezelése és elosztott terhelésű. A következő lehetőségek bármelyikét használhatja önállóan vagy együtt:

**DNS terheléselosztás**

Az Azure Traffic Manager szolgáltatás a globális DNS terheléselosztást biztosít. A TRAFFIC Manager-ügyfelek IP-címe megfelelően működő végpontot, az alábbi útválasztási módszerek egyike alapján válaszol:
- **Földrajzi:** Az ügyfelek irányítja, meghatározott végpontokhoz (Azure, külső vagy beágyazott) alapú mely földrajzi helye határozza meg a DNS-lekérdezés származik. Ez a módszer lehetővé teszi az olyan forgatókönyvekben, ahol, hogy az ügyfél földrajzi régióban, és azok alapján, Útválasztás fontos. Ilyenek például az adatok szuverenitását megbízás tartalom és a felhasználói felület honosítása megfelel, és a különféle régiókból származó forgalom mérése.
- **Teljesítmény:** Az ügyfélnek visszaadott IP-címet a "legközelebb" az ügyfél. A "legközelebbi" végpont nem feltétlenül legközelebbi mért földrajzi távolság. Ehelyett ez a módszer alapján lehetet nyilvántartani hálózati késés a legközelebb eső végpont határozza meg. A TRAFFIC Manager egy internetes késés tábla nyomon követéséhez az üzenetváltási idő közötti IP-címtartományok és minden egyes Azure-adatközpont tárolja.
- **Prioritás:** Az elsődleges végpont (legnagyobb prioritás) átirányítja a forgalmat. Ha az elsődleges végpont nem érhető el, a Traffic Manager a második végpontra irányítja a forgalmat. Ha az elsődleges és másodlagos végpontok nem érhetők el, áramlik a forgalom, a harmadik, és így tovább. A végpont rendelkezésre állását a konfigurált állapota (engedélyezve vagy letiltva), és a folyamatban lévő végpont-monitorozás alapul.
- **Súlyozott ciklikus időszeletelés:** Az egyes kérések a Traffic Manager véletlenszerűen választ elérhető végpontot. A valószínűsége annak, a végpont kiválasztása a hozzárendelt összes elérhető végpontok súlyok alapul. Az azonos súly használatával-még akkor is, az adatforgalom eloszlása eredményez az összes végpontok között. Ezekre a végpontokra, a DNS-válaszok ritkább vagy gyakoribb visszaadandó magasabb vagy alacsonyabb súlyok használata a meghatározott végpontokhoz okoz.

Az alábbi képen egy webalkalmazás, egy webalkalmazás végpont felé irányuló kérelmet. Végpontok az más Azure-szolgáltatások például a virtuális gépek és Felhőszolgáltatások is.

![Traffic Manager](./media/networking-overview/traffic-manager.png)

Az ügyfél közvetlenül csatlakozik az, hogy a végpont. Az Azure Traffic Manager észleli, ha a végpont nem kifogástalan, és ezután átirányítja a felhasználókat az ügyfelek egy másik, megfelelő végpontra. Traffic Managerrel kapcsolatos további tudnivalókért olvassa el a [Azure Traffic Manager áttekintése](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

**Alkalmazásterhelés-elosztás**

Az Azure Application Gateway szolgáltatás biztosítja az alkalmazáskézbesítési vezérlőt (ADC) szolgáltatást. Az Application Gateway az Ön alkalmazásait, beleértve a webalkalmazási tűzfal webes alkalmazásai védelmét a biztonsági rések és az azokat kihasználó támadások ellen különböző 7. rétegbeli (HTTP/HTTPS) terheléselosztás lehetőségeket kínál. Az Application Gateway lehetővé teszi optimalizálhatják a webfarmok termelékenységét a processzorigényes SSL-lezárások az Alkalmazásátjáró felé történő kiszervezésével. 

Egyéb 7. rétegbeli útválasztási lehetőségeket tartalmazza, Ciklikus időszeleteléses elosztását bejövő forgalom, a cookie-alapú munkamenet-affinitást, a URL-cím-alapú útválasztás és a egy application gateway mögött több webhelyet is üzemeltethet. Az Application Gateway konfigurálható egy internetre irányuló átjáró, egy belső átjáró vagy mindkettőt. Application Gateway-példány az Azure teljes körűen felügyelt, méretezhető és magas rendelkezésre állású. Diagnosztikai és naplózási képességek széles skáláját biztosítja a jobb kezelhetőség érdekében. Az Application Gateway kapcsolatos további információkért olvassa el a [Application Gateway áttekintése](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

Az alábbi képen látható URL-cím-alapú útválasztás az Application Gateway szolgáltatással:

![Application Gateway](./media/networking-overview/application-gateway.png)

**Hálózati terheléselosztás**

Az Azure Load Balancerrel biztosít a nagy teljesítményű, kis késleltetésű 4. szintű terheléselosztó minden UDP és TCP protokollra. Kezeli a bejövő és kimenő kapcsolatokat. Beállíthatja, hogy a nyilvános és belső terheléselosztásos végpontjait. Meghatározhat szabályokat a bejövő kapcsolatok leképezése háttérbeli készletet destinations TCP- és HTTP-állapotellenőrzéséhez beállítások használatával kezelheti a szolgáltatás rendelkezésre állása. Load Balancer kapcsolatos további információkért olvassa el a [Load Balancer áttekintése](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

Az alábbi képen egy internetkapcsolattal rendelkező többrétegű alkalmazást, amely mindkét külső és belső terheléselosztók használja:

![Terheléselosztó](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Biztonsági

Az alábbi beállítások segítségével Azure-erőforrásoktól érkező forgalmat szűrheti:

- **Hálózat:** Azure-beli hálózati biztonsági csoportok (NSG) az Azure-erőforrások bejövő és kimenő forgalom szűrése valósítható meg. Minden NSG tartalmaz egy vagy több bejövő és kimenő szabályok. Minden szabály azt határozza meg, a forrás IP-címek, cél IP-címek, port és protokoll, amely az forgalom szűrve van. NSG-ket az egyes alhálózatokat és az egyes virtuális gépekhez is alkalmazható. NSG-kkel kapcsolatos további információkért olvassa el a [hálózati biztonsági csoportok áttekintése](../virtual-network/security-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Alkalmazás:** Webalkalmazási tűzfallal rendelkező Application Gateway használatával védheti a webalkalmazásokat a biztonsági rések és az azokat kihasználó támadások ellen. Gyakori Példák SQL injektálási támadások, webhelyek közötti szkriptelést és hibás formátumú fejlécek. Az Application gateway szűri ki ezt a forgalmat, és leállítja a webkiszolgálók elérése. Ön engedélyezni kívánt szabályokat konfigurálhat. Az SSL-egyeztetési szabályzatokat konfigurálása biztosítja, hogy bizonyos szabályzatokat, le kell tiltani. A webalkalmazási tűzfallal kapcsolatos további információkért olvassa el a [webalkalmazási tűzfal](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

Ha Azure-ban nem adja meg, vagy használhatja a helyszíni hálózati alkalmazások használandó hálózati képességet, a termékek bevezetése a virtuális gépek, és csatlakoztassa őket a virtuális hálózat. A [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) előfordulhat, hogy a jelenleg használt hálózati alkalmazások előre konfigurált számos különböző virtuális gépeket tartalmaz. Ezek a előre konfigurált virtuális gépek általában hálózati virtuális berendezések (NVA) nevezik. Az nva-k az alkalmazások, például tűzfal- és WAN-optimalizálás érhetők el.

## <a name="routing"></a>Útválasztás

Az Azure létrehozza alapértelmezett útválasztási táblázatokat, amelyek lehetővé teszik az erőforrások egymással kommunikálni bármely virtuális hálózat egyetlen alhálózatának sem kapcsolódik. A következő típusú útvonalakat az Azure-hoz létre alapértelmezett útvonalak felülírásához egyikében vagy valósíthatja meg:
- **Felhasználó által definiált:** Létrehozhat egyéni útválasztási táblázatokat az útvonalakat a vezérlőelemre, hova érkezzen a forgalom az egyes alhálózatokon. A felhasználó által megadott útválasztással kapcsolatos további információkért olvassa el a [felhasználó által megadott útvonalakat](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) ismertető cikket.
- **Border gateway protocol (BGP):** Ha a helyszíni hálózathoz egy VPN-átjáró vagy ExpressRoute-kapcsolat használatával csatlakoztatja a virtuális hálózat, a virtuális hálózatok propagálható BGP-útvonalak. A BGP az interneten gyakran használt szabványos útválasztási protokoll az útválasztási és elérhetőségi információcserére két vagy több hálózat között. Ha Azure-beli virtuális hálózatok összefüggésben használja, a BGP teszi lehetővé, az Azure VPN Gateway és a helyszíni VPN-eszközök, BGP-társak vagy -szomszédok, az "útvonalakat", amely tájékoztatja a rendelkezésre állási és az előtagok útválasztókon mindkét átjáró nevű az érintett átjárókon keresztül. Tranzit útválasztás több hálózat közötti egy BGP-társ BGP-átjáró tanul, más BGP-társak útvonalak propagálásával BGP is engedélyezheti. A BGP kapcsolatos további információkért tekintse meg a [BGP és az Azure VPN Gateway áttekintése](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

## <a name="manageability"></a>Kezelhetőségi

Az Azure a következő felügyelhető és kezelhető a hálózati eszközöket kínál:
- **Tevékenységnaplók:** Az összes Azure-erőforrások tevékenységeket tartalmazó naplók, amely a műveletek végrehajtása műveletek állapotának információt biztosítanak, és a művelet kezdeményezője rendelkezik. A Tevékenységnaplók kapcsolatos további információkért olvassa el a [Tevékenységnaplók áttekintése](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Diagnosztikai naplók:** Rendszeres és spontán események hálózati erőforrások által létrehozott és bejelentkezett az Azure storage-fiókok, az Azure-Eseményközpontba küldi, vagy az Azure Log Analytics felé küldött. Erőforrás állapotának betekintést nyújtanak a diagnosztikai naplók. Diagnosztikai naplók Load Balancer (internetes elérésű), a hálózati biztonsági csoportok, útvonalak és az Application Gateway-okat. Diagnosztikai naplók kapcsolatos további információkért olvassa el a [diagnosztikai naplók áttekintése](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Metrikák:** Mérőszám játszik TELJESÍTMÉNYMÉRÉSEK és erőforrások olyan időszakban gyűjtött teljesítményszámlálók. Mérőszámok segítségével riasztás küszöbértékek alapján. Jelenleg metrikák érhetők el az Application Gatewayen. További információk a metrikákról, olvassa el a [metrikáinak áttekintésében](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Hibaelhárítás:** Hibaelhárítási információk közvetlenül az Azure Portalon érhető el. Az információk segítik az ExpressRoute, VPN-átjárót, az Application Gateway, hálózati biztonsági naplók, útvonalak, DNS, terheléselosztó és a Traffic Manager szolgáltatással kapcsolatos gyakori problémák diagnosztizálásához.
- **Szerepköralapú hozzáférés-vezérlés (RBAC):** Szabályozza, ki is létrehozhatja és kezelheti a szerepköralapú hozzáférés-vezérlés (RBAC) a hálózati erőforrásokat. További információ az RBAC olvassa el a [RBAC – első lépések](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk. 
- **Csomagrögzítés:** Az Azure Network Watcher szolgáltatás lehetővé teszi a csomagrögzítés futtatása virtuális gépen belül a virtuális gép egy bővítményével. Ez a funkció a Linux és Windows virtuális gépek érhető el. A csomagrögzítés kapcsolatos további információkért olvassa el a [Csomagrögzítés áttekintése](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **IP-forgalmának ellenőrzése:** A Network Watcher lehetővé teszi, hogy egy Azure virtuális Gépen, és a egy távoli erőforrás határozza meg, hogy a csomag engedélyezett vagy tiltott közötti IP-forgalmának ellenőrzése. Ez a funkció teszi lehetővé a rendszergazdák gyorsan diagnosztizálhatja a kapcsolódási problémák. IP-forgalmának ellenőrzése kapcsolatos további tudnivalókért olvassa el a [IP-folyamat ellenőrzése – áttekintés](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **VPN-kapcsolatának hibaelhárítása:** A Network Watcher VPN hibaelhárító képességét lehetővé teszi a lekérdezés egy kapcsolattal vagy átjáró és az erőforrások állapotának ellenőrzése. VPN-kapcsolatok hibaelhárításával kapcsolatos további tudnivalókért olvassa el a [VPN-kapcsolat hibaelhárítása – áttekintés](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Hálózati topológia megtekintése:** Virtuális hálózat és a Network Watcher tekintse meg a hálózati erőforrások grafikus ábrázolását. Hálózati topológia megtekintése kapcsolatos további információkért olvassa el a [topológiájának áttekintése](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

## <a name="tools"></a>Telepítési és konfigurációs eszközök

Telepítheti és konfigurálhatja az Azure hálózati erőforrásaival a következő eszközök bármelyikével:

- **Az Azure Portalon:** Egy grafikus felhasználói felület, amelyen a böngészőben. Nyissa meg az [Azure Portalt](http://portal.azure.com).
- **Azure PowerShell-lel:** Windows-számítógépek kezelése az Azure parancssori eszközök. További információ az Azure PowerShell olvassa el a [Azure PowerShell áttekintése](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Az Azure parancssori felület (CLI):** Parancssori eszközök kezelése az Azure Linux, macOS vagy Windows-számítógépekről. További információ az Azure CLI olvassa el a [Azure CLI áttekintése](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Az Azure Resource Manager-sablonok:** Egy fájl (JSON formátumban), amely meghatározza az infrastruktúra és a egy Azure-megoldás konfigurációját. A sablonok segítségével a megoldás a teljes életciklusa során ismételten üzembe helyezhető, és az erőforrások üzembe helyezése biztosan konzisztens lesz. Sablonok készítése kapcsolatos további információkért olvassa el a [gyakorlati tanácsok a sablonok létrehozásához](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk. Sablonok is üzembe helyezhetők az Azure Portalon, parancssori felület vagy PowerShell. Első lépésként sablonokkal azonnal üzembe helyezése a sok előre konfigurált sablonok egyikét a [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?term=network) könyvtár. 

## <a name="pricing"></a>Díjszabás

Díj, az Azure hálózati szolgáltatások rendelkezik, míg mások ingyenes. Nézet a [virtuális hálózati](https://azure.microsoft.com/pricing/details/virtual-network), [VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway), [Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/), [terheléselosztó](https://azure.microsoft.com/pricing/details/load-balancer), [Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) és [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) díjszabási lapjait további információt.

## <a name="next-steps"></a>További lépések

- Az első virtuális hálózat létrehozása, és csatlakozzon néhány virtuális gépet, a lépéseket követve a [az első virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- A számítógép csatlakoztatása virtuális hálózathoz a lépéseket követve a [pont – hely kapcsolat konfigurálása](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- Terheléselosztását internetes forgalom nyilvános kiszolgálókon található lépéseket követve a [hozható létre internetkapcsolattal rendelkező terheléselosztó](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
