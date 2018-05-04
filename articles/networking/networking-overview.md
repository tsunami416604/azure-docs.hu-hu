---
title: Az Azure hálózatkezelés |} Microsoft Docs
description: További tudnivalók az Azure hálózati szolgáltatásokat és képességeket.
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
ms.openlocfilehash: 47ee22df081b71e7bafa40210a9c4cac0a844825
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-networking"></a>Az Azure hálózatkezelés

Azure biztosít a különböző hálózati képességekkel, amelyek együtt vagy külön-külön is használható. Kattintson valamelyik az alábbi főbb képességeket tudhat meg többet őket:
- [Azure-erőforrások közötti kapcsolat](#connectivity): csatlakozás Azure-erőforrások együtt, a felhőben biztonságos, titkos virtuális hálózaton.
- [Internetkapcsolat](#internet-connectivity): Azure-erőforrások érkező vagy oda irányuló kommunikálnak az interneten keresztül.
- [A helyi kapcsolat](#on-premises-connectivity): egy a helyszíni hálózathoz csatlakozni az Azure-erőforrások virtuális magánhálózat (VPN) az interneten keresztül, vagy az Azure-bA egy dedikált kapcsolaton keresztül.
- [Terheléselosztás és a forgalom iránya betöltése](#load-balancing): terhelés oszthatja el a forgalmat az ugyanazon a helyen, és a kiszolgálók, különböző helyeken közvetlen forgalma kiszolgálójára.
- [Biztonsági](#security): alhálózatok vagy egyedi virtuális gépeket (VM) közötti hálózati forgalom szűrésére.
- [Útválasztás](#routing): használja az alapértelmezett útválasztási vagy teljes irányítása az Azure és a helyszíni erőforrások között.
- [Kezelhetőségi](#manageability): a figyelő és az Azure hálózati erőforrások kezeléséhez.
- [Üzembe helyezési és konfigurációs eszközök](#tools): egy webes portál vagy a platformfüggetlen parancssori eszközök telepítését és konfigurálását a hálózati erőforrásokhoz.

## <a name="Connectivity"></a>Azure-erőforrások közötti kapcsolat

Azure-erőforrások, például a virtuális gépek, a Cloud Services, a virtuális gépek méretezési csoportok és az Azure App Service Environment-környezetek is közvetlenül egymással kommunikálnak az Azure Virtual Network (VNet) keresztül. A virtuális hálózat kijelölve a Azure-felhő logikai elkülönítése a [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Megvalósíthat több Vnetek belül minden Azure-előfizetés és Azure [régió](https://azure.microsoft.com/regions). Minden egyes virtuális hálózat el különítve a más Vnetekről. Minden egyes vnet a következő műveletek végezhetők el:

- Megadhat egy egyéni magánhálózati IP-címteret nyilvános és magánhálózati (RFC 1918) címek használatával. Azure rendel erőforrások a VNet egy magánhálózati IP-cím keresztül kapcsolódik a címterület lehet kijelölni.
- A VNet szegmentálni be egy vagy több alhálózatból, és foglaljon le a VNet-címterek minden alhálózat egy része.
- Azure által biztosított névfeloldás használata, vagy adjon meg egy Vnetet csatlakoztatott a saját DNS-kiszolgáló számára.

Az Azure Virtual Network szolgáltatás kapcsolatos további tudnivalókért olvassa el a [virtuális hálózat áttekintése](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk. Kapcsolódás Vnetek egymással, vagy VNet kommunikálhassanak egymással Vnetek csatlakoztatott engedélyezése. Valamelyike vagy mindegyike a következő beállítások segítségével Vnetek csatlakozni egymáshoz:

- **Társviszony-létesítés:** lehetővé teszi az erőforrások egymással kommunikálni az Azure-régión belül különböző Azure Vnetekhez csatlakoztatni. A sávszélesség és a késleltetés között a Vnetek megegyezik, mintha az erőforrások csatlakoztatva ugyanazt a virtuális hálózatot. Társviszony-létesítés kapcsolatos további tudnivalókért olvassa el a [virtuális hálózati társviszony-létesítési áttekintése](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **VPN-átjáró:** lehetővé teszi az erőforrások egymással kommunikálnak a különböző Azure-régiók belül különböző Azure Vnetekhez csatlakoztatni. Vnetek közötti forgalmat egy Azure VPN-átjárón keresztül. Az átjáró sávszélesség közötti Vnetek sávszélesség korlátozódik. Vnetek csatlakozás VPN-átjáróval kapcsolatos további tudnivalókért olvassa el a [konfigurálja a VNet – VNet-kapcsolatot régiók](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

## <a name="internet-connectivity"></a>Internetkapcsolat

Egy virtuális hálózat csatlakozik az összes Azure-erőforrások kimenő képes kapcsolódni az internetre alapértelmezés szerint rendelkezik. A magánhálózati IP-cím erőforrás hálózati forráscím lefordított (SNAT) által az Azure-infrastruktúra egy nyilvános IP-cím. Kimenő internetkapcsolattal kapcsolatos további tudnivalókért olvassa el a [ismertetése az Azure-ban kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

Való kommunikációra bejövő Azure-erőforrások az internetről, vagy az internethez nélkül SNAT kimenő kommunikációra, egy erőforrást egy nyilvános IP-címet kell hozzárendelve. Nyilvános IP-címek kapcsolatos további tudnivalókért olvassa el a [nyilvános IP-címek](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

## <a name="on-premises-connectivity"></a>A helyi kapcsolat

Keresztül elérhető erőforrásokhoz a virtuális hálózat biztonságos VPN-kapcsolatot, és a közvetlen kapcsolatot. Az Azure virtuális hálózat és a helyszíni hálózat között a hálózati forgalom elküldése, létre kell hoznia a virtuális hálózati átjáró. A kapcsolat típusát, amelyet, VPN vagy ExpressRoute létrehozásához az átjáró beállításainak konfigurálása

A helyszíni hálózat csatlakozhatnak egy virtuális hálózat használatával tetszőleges kombinációját az alábbiak közül:

**Pont-pont (keresztül az SSTP VPN)**

Az alábbi képen látható több számítógép és egy virtuális hálózat közötti kapcsolatok külön pont:

![Pont–hely kapcsolat](./media/networking-overview/point-to-site.png)

Ez a kapcsolat létrejön egy egyedi számítógép és egy virtuális hálózat között. Ez a kapcsolattípus remek választás, ha csak most ismerkedik az Azure szolgáltatással, illetve a fejlesztők számára, mert a meglévő hálózatot csak kis mértékben vagy egyáltalán nem kell módosítani. Célszerű is kényelmesen használható, ha csatlakozik egy távoli helyre, például egy konferenciaterem vagy otthoni. Pont – hely kapcsolatok gyakran összekapcsolt webhelyek kapcsolaton keresztül az azonos virtuális hálózati átjárón keresztül. A kapcsolat az SSTP protokoll segítségével, titkosított kommunikációt biztosít a számítógép és a virtuális hálózat között az interneten keresztül. A pont-pont típusú VPN várakozási is előre nem látható, mivel a forgalom halad át az interneten.

**Pont-pont (IPsec/IKE VPN-alagút)**

![Helyek közötti kapcsolat](./media/networking-overview/site-to-site.png)

Ezt a kapcsolatot a helyszíni VPN-eszköz és az Azure VPN-átjáró között. A kapcsolat típusa lehetővé teszi, hogy az összes helyi erőforrást is engedélyezni szeretné a virtuális hálózat eléréséhez. A kapcsolat az IPSec/IKE VPN, amely titkosított kommunikációt biztosít az interneten, a helyszíni eszközök és az Azure VPN gateway között. Az azonos VPN-átjáró több helyszíni hely csatlakozhat. Az egyes helyeken a helyszíni VPN-eszköz kívülről elérhető nyilvános IP-címnek, amely nincs a NAT mögött kell rendelkeznie. A pont-pont kapcsolat a késési is előre nem látható, mivel a forgalom halad át az interneten.

**Az ExpressRoute (dedikált magánhálózati kapcsolat)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Ilyen típusú kapcsolat hoznak létre a hálózat és az Azure-ban egy ExpressRoute-partner keresztül. Ez a kapcsolat nem nyilvános. Forgalom nem bejárják az interneten. Az ExpressRoute-kapcsolat a késési is előre jelezhető, mivel a forgalom nem haladnak át az interneten. ExpressRoute össze lehet kombinálni webhelyek kapcsolatot.

Az előző kapcsolódási beállítás kapcsolatos további tudnivalókért olvassa el a [kapcsolat topológiai diagramot](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

## <a name="load-balancing"></a>Terheléselosztás és a forgalom iránya betöltése

A Microsoft Azure több kezelése a hálózati adatforgalom elosztása milyen szolgáltatásokat és elosztott terhelésű biztosít. A következő lehetőségek bármelyikét használhatja önállóan vagy együtt:

**DNS terheléselosztás**

Az Azure Traffic Manager szolgáltatás a globális DNS terheléselosztás biztosít. A TRAFFIC Manager-ügyfelek, amelyeken a kifogástalan állapotú végpontok, az alábbi útválasztási módszerek egyike alapján IP-címe válaszol:
- **Földrajzi:** ügyfelek van irányítva meghatározott végpontokhoz (Azure, külső vagy beágyazott) alapú mely földrajzi helyen található, a DNS-lekérdezés származik. Ez a módszer lehetővé teszi, hogy a forgatókönyvek, ahol egy ügyfél a földrajzi régióban ismerete, és a velük alapján, fontos. Például megfelel az adatok közös joghatóság alá megbízás honosítása tartalom & felhasználói élményt, és a különböző régiókban érkező forgalmat méri.
- **Teljesítmény:** az IP-cím, az ügyfél számára a "legközelebb" az ügyfél. A "legközelebbi" végpont nincs feltétlenül legközelebbi földrajzi távolság mérve. Ehelyett ez a módszer hálózati késés mérésével a legközelebbi végpontot határozza meg. A TRAFFIC Manager IP-címtartományok és minden Azure-adatközpontban közötti üzenetváltások idejének nyomon követésére egy Internet késés tábla tárolja.
- **Prioritás:** forgalom elsődleges végpont (legnagyobb prioritás) van átirányítva. Az elsődleges végpont nem érhető el, ha a Traffic Manager irányítja a forgalmat a második végpontnak. Az elsődleges és másodlagos végpontok nem érhetők el, ha a forgalom halad, a külső, és így tovább. A végpont rendelkezésre állását a konfigurált állapota (engedélyezve vagy letiltva), és a folyamatban lévő végpontmonitoring kijelző alapul.
- **Súlyozott ciklikus multiplexelés:** az egyes kérelmek Traffic Manager véletlenszerűen választ egy elérhető végpontot. A valószínűségi végpont kiválasztása a súlyok rendelt összes elérhető végpontok alapul. Az azonos súlyozással használatával egy még akkor is, a forgalom eloszlása eredményez az összes végpontok között. A meghatározott végpontokhoz magasabb vagy alacsonyabb súlyok használatával azt eredményezi, ezekre a végpontokra ritkább vagy gyakoribb visszahelyezi a DNS-válaszok.

Az alábbi képen egy webalkalmazást, a webes alkalmazás végpont felé irányuló kérelmet. Végpontok más Azure-szolgáltatásokkal például a virtuális gépek és Felhőszolgáltatások is lehet.

![Traffic Manager](./media/networking-overview/traffic-manager.png)

Az ügyfél közvetlenül kapcsolódik az adott végpontra. Az Azure Traffic Manager észleli, ha a végpont nem kifogástalan, és ügyfelek majd átirányítja egy másik, a megfelelő végpont. További információ a Traffic Manager, olvassa el a [Azure Traffic Manager – áttekintés](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

**Alkalmazásterhelés-elosztás**

Az Azure Application Gateway szolgáltatás alkalmazás kézbesítési vezérlő (LÉPETT) biztosít szolgáltatásként. Alkalmazásátjáró az alkalmazások, beleértve a webalkalmazások biztonsági réseket és biztonsági rések elleni védelméhez webalkalmazási tűzfal különböző réteg 7 (HTTP/HTTPS) terheléselosztás képességeket biztosít. Alkalmazásátjáró is lehetővé teszi a webkiszolgáló farm termelékenység optimalizálása kiürítésével a CPU-intenzív SSL-lezárást az Alkalmazásátjáró. 

Más réteg 7 útválasztási lehetőségek közé tartozik a ciklikus multiplexelés, bejövő forgalmat, a munkamenet cookie-alapú kapcsolat, a URL-cím elérési út-alapú útválasztási és a több webhely mögött egyetlen Alkalmazásátjáró lehetőséget. Alkalmazásátjáró konfigurálhat egy internetes átjárót, egy csak belső-átjáró vagy mindkettőt. Alkalmazásátjáró teljesen Azure felügyelt, méretezhető és magas rendelkezésre állású. Diagnosztikai és naplózási képességek széles skáláját biztosítja a jobb kezelhetőség érdekében. Alkalmazásátjáró kapcsolatos további tudnivalókért olvassa el a [Alkalmazásátjáró áttekintése](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

Az alábbi képen látható URL-cím elérési út-alapú alkalmazás átjáróval útválasztás:

![Application Gateway](./media/networking-overview/application-gateway.png)

**Hálózati terheléselosztás**

Az Azure terheléselosztó biztosít a nagy teljesítményű, alacsony késésű réteg 4 terheléselosztás összes UDP- és TCP protokollt. Bejövő és kimenő kapcsolatok kezeli. Nyilvános és belső elosztott terhelésű végpont konfigurálhatja. Bejövő kapcsolatok hozzárendelését háttér-készlet célhelyekre TCP- és HTTP állapot-ellenőrzés beállítások segítségével kezelheti a szolgáltatás rendelkezésre állása szabályokat adhat meg. Terheléselosztó kapcsolatos további tudnivalókért olvassa el a [Load Balancer áttekintése](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

Az alábbi képen egy internetre irányuló többrétegű alkalmazást, amely mindkét külső és belső terheléselosztók használja:

![Terheléselosztó](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Biztonsági

Az alábbi beállítások segítségével Azure-erőforrások forgalmat szűrheti:

- **Hálózati:** Azure hálózati biztonsági csoportokkal (NSG-k) szűrésére Azure-erőforrások bejövő és kimenő forgalmat is létrehozható. Minden NSG tartalmaz egy vagy több bejövő és kimenő szabályokat. Minden egyes szabály azt határozza meg, a forrás IP-címek, a cél IP-címek, a port és a forgalom szűrt protokoll. Az NSG-k különálló alhálózatokat és az egyes virtuális gépeken is alkalmazható. Ha többet szeretne megtudni az NSG-k, olvassa el a [hálózati biztonsági csoportok – áttekintés](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Alkalmazás:** webalkalmazási tűzfal Alkalmazásátjáró segítségével megvédheti a webalkalmazások biztonsági réseket és biztonsági réseket. Közös többek között az SQL kihasználó közötti helyközi és hibás fejléceket. Alkalmazásátjáró kiszűri a forgalmat, és leállítja a webkiszolgálók elérése. Tudunk milyen engedélyezni kívánt szabályok konfigurálása. Az SSL-egyeztetést szabályzatok konfigurálásával biztosítja engedélyezi bizonyos szabályzatokat, le kell tiltani. A webalkalmazási tűzfal kapcsolatos további tudnivalókért olvassa el a [webalkalmazási tűzfal](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

Ha módosítania kell az Azure nem adja meg, vagy használhatja a helyszíni hálózati alkalmazások használni kívánt hálózati funkció, a termékek megvalósítását a virtuális gépeket, és csatlakoztassa őket a virtuális hálózat. A [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) előre konfigurálva van, előfordulhat, hogy a jelenleg használt hálózati alkalmazások számos különböző virtuális gépek tartalmazza. Ezek előre konfigurált virtuális gépek általában hálózati virtuális készülékek (NVA) nevezik. Az alkalmazások, például a tűzfal és a WAN-optimalizálást NVAs érhetők el.

## <a name="routing"></a>Útválasztás

Az Azure létrehoz alapértelmezett útvonaltábláit, amelyek lehetővé teszik az erőforrások csatlakoznak bármely egymással kommunikálnak a virtuális hálózat egyetlen alhálózatának sem. Valamelyike vagy mindegyike útvonalak felülbírálhatja az alapértelmezett útvonalak Azure-hoz létre a következő típusú valósíthatja meg:
- **Felhasználó által definiált:** hozhat létre egyéni útvonaltáblák útvonalak adott vezérlőn, ahol továbbítódik a az egyes alhálózatokon. A felhasználó által megadott útválasztással kapcsolatos további információkért olvassa el a [felhasználó által megadott útvonalakat](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) ismertető cikket.
- **A border gateway protocol (BGP):** Ha a virtuális hálózat csatlakozni a helyszíni hálózat az Azure VPN-átjáró vagy ExpressRoute kapcsolattal, akkor kerülhet BGP-útvonalakat a Vnetek. A BGP az interneten gyakran használt szabványos útválasztási protokoll az útválasztási és elérhetőségi információcserére két vagy több hálózat között. Ha az Azure virtuális hálózatok környezetben használják, BGP lehetővé teszi, hogy az Azure VPN-átjáróként és BGP-társakat vagy szomszédok, az exchange-"irányítja a", amely a rendelkezésre állási és ezek előtagok nyissa meg az elérhetőség mindkét átjárók tájékoztatja a helyszíni VPN-eszközök az átjárók és az útválasztók használatával kapcsolatban. Tranzit útválasztás több hálózat között propagálására útvonalakat a BGP-partner minden más BGP-társhoz való Tanulja meg a BGP-átjáró BGP is engedélyezheti. A BGP kapcsolatos további tudnivalókért tekintse meg a [BGP Azure VPN Gatewayek – áttekintés](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

## <a name="manageability"></a>Kezelhetőség

Azure biztosít felügyeletéhez és kezeléséhez hálózatkezelés az alábbi eszközöket:
- **Tevékenységi naplóit:** összes Azure-erőforrások rendelkezik tevékenységi naplóit, amelyek információval szolgálnak a műveletek végrehajtása műveletek állapotának és hogy ki kezdeményezte a műveletet. Tevékenységi naplóit kapcsolatos további tudnivalókért olvassa el a [tevékenység naplózza áttekintése](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Diagnosztikai naplók:** időszakos és önkéntes események hálózati erőforrások által létrehozott és bejelentkezett az Azure storage-fiókok, egy Azure Eseményközponthoz küldött, vagy Azure Naplóelemzés küldött. Diagnosztikai naplók betekintést nyújtanak a erőforrás állapotát. Diagnosztikai naplók terheléselosztó (internetről hozzáférhető), a hálózati biztonsági csoportok, a útvonalak és az Application Gateway-okat. Diagnosztikai naplók kapcsolatos további tudnivalókért olvassa el a [diagnosztikai naplók áttekintése](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Metrikák:** adatok gyűjtése le TELJESÍTMÉNYMÉRÉSEK és az erőforrás egy meghatározott időtartamra vonatkozóan gyűjtött adatait. Mérőszámok segítségével aktiváltak riasztásokat küszöbértékek alapján. Jelenleg metrikák érhetők el az alkalmazás-átjárón. Metrikák kapcsolatos további tudnivalókért olvassa el a [metrikák áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Hibaelhárítás:** hibaelhárítási információkat érhető el közvetlenül az Azure-portálon. Az információk segítséget nyújtanak a ExpressRoute, VPN-átjáró, Alkalmazásátjáró, hálózati biztonsági naplókat, útvonalakat, DNS, Load Balancer és Traffic Manager kapcsolatos gyakori problémák diagnosztizálásához.
- **Szerepköralapú hozzáférés-vezérlést (RBAC):** személyek hozhat létre és kezelhet a szerepköralapú hozzáférés-vezérlést (RBAC) hálózati erőforrásokhoz. További tudnivalók az RBAC olvasásával a [Ismerkedés az RBAC](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk. 
- **Csomagrögzítéssel:** az Azure hálózati figyelőt szolgáltatás lehetővé teszi a virtuális gép egy csomagrögzítéssel átszervezhető egy bővítményt a virtuális Gépen belül. Ez a funkció a Linux és a Windows virtuális gépek érhető el. Csomagrögzítéssel kapcsolatos további tudnivalókért olvassa el a [csomag rögzítési áttekintése](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Ellenőrizze az IP-adatfolyamok:** hálózati figyelőt lehetővé teszi, hogy ellenőrizze az IP-adatfolyamok között annak meghatározásához, hogy a csomag engedélyezett vagy megtagadott egy Azure virtuális gép és a távoli erőforrás. Ez a funkció teszi lehetővé a rendszergazdák gyorsan a problémák diagnosztizálásához. IP-adatfolyamok ellenőrzésével kapcsolatos további tudnivalókért olvassa el a [IP folyamata – áttekintés ellenőrzése](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **VPN-kapcsolatok:** hálózati figyelőt a VPN hibaelhárító képessége lehetővé teszi a lekérdezési kapcsolat vagy átjáró és az erőforrások állapotának ellenőrzése. VPN-kapcsolatok hibaelhárítással kapcsolatos további tudnivalókért olvassa el a [VPN-kapcsolat hibaelhárítása – áttekintés](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Hálózati topológia megtekintése:** tekintse meg a hálózati erőforrások grafikus ábrázolása egy virtuális hálózatot a hálózati figyelőt. Hálózati topológia megjelenítésével kapcsolatos további tudnivalókért olvassa el a [topológiájának áttekintése](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.

## <a name="tools"></a>Üzembe helyezési és konfigurációs eszközök

Telepítheti és konfigurálhatja az Azure-hálózati erőforrások a következő eszközök bármelyikével:

- **Azure-portálon:** egy grafikus felhasználói felület, amelyen a böngészőben. Nyissa meg az [Azure portált](http://portal.azure.com).
- **Az Azure PowerShell:** Azure a Windows rendszerű számítógépek felügyeletére szolgáló parancssori eszközök. További tudnivalók az Azure PowerShell ehhez beolvassa a [Azure PowerShell áttekintése](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Azure parancssori felület (CLI):** Azure Linux, macOS vagy Windows-számítógépek felügyeletére szolgáló parancssori eszközök. További tudnivalók az Azure parancssori felület ehhez beolvassa a [Azure CLI áttekintése](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- **Az Azure Resource Manager-sablonok:** (JSON formátumú fájlba), amely meghatározza az infrastruktúra és egy Azure megoldás konfigurációját. A sablonok segítségével a megoldás a teljes életciklusa során ismételten üzembe helyezhető, és az erőforrások üzembe helyezése biztosan konzisztens lesz. Sablonok készítése kapcsolatos további tudnivalókért olvassa el a [gyakorlati tanácsok sablonok létrehozásához](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk. Sablonok az Azure-portálon, a parancssori felületen vagy a PowerShell használatával is telepíthető. A sablonokkal rögtön használatba, telepítenie kell a számos előre konfigurált sablonok egyikét a [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/resources/templates/?term=network) könyvtár. 

## <a name="pricing"></a>Díjszabás

Az Azure hálózati szolgáltatások rendelkeznek járnak, míg mások szabad. Nézet a [virtuális hálózati](https://azure.microsoft.com/pricing/details/virtual-network), [VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway), [Alkalmazásátjáró](https://azure.microsoft.com/pricing/details/application-gateway/), [terheléselosztó](https://azure.microsoft.com/pricing/details/load-balancer), [hálózatifigyelőt](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) és [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) árképzési lapok további információt.

## <a name="next-steps"></a>További lépések

- Az első virtuális hálózat létrehozása, és csatlakozik néhány virtuális géppel, Ehhez hajtsa végre a lépéseket a [az első virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- A lépések végrehajtásával csatlakoztassa a számítógépet egy Vnetet az [egy pont – hely kapcsolat beállítása](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
- Terhelésének elosztása internetes forgalom nyilvános kiszolgálókon található lépések végrehajtásával a [hozzon létre egy internetre irányuló terheléselosztót](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) cikk.
