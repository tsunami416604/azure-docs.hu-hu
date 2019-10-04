---
title: Azure hálózati biztonság | Microsoft Docs
description: Ismerkedjen meg a felhőalapú számítástechnikai szolgáltatásokkal, amelyek számos számítási példányt tartalmaznak, & olyan szolgáltatásokat, amelyek automatikusan fel-és leskálázást tesznek lehetővé az alkalmazás vagy a vállalat igényeinek megfelelően.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 3c4bdb3d46e4630257c2567cf2003ebede00b71e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934772"
---
# <a name="azure-network-security"></a>Azure hálózati biztonság

Tudjuk, hogy a biztonság az egyik a felhőben, és mennyire fontos, hogy pontos és kellő időben tájékozódjon az Azure biztonságáról. Az Azure alkalmazásokhoz és szolgáltatásokhoz való használatának egyik legjobb oka az, hogy kihasználja az Azure biztonsági eszközeinek és képességeinek széles körét. Ezek az eszközök és képességek segítenek a biztonságos megoldások létrehozásában az Azure platformon.

Microsoft Azure biztosítja az ügyféladatok titkosságát, integritását és rendelkezésre állását, ugyanakkor lehetővé teszi az átlátható elszámoltathatóságot is. Annak érdekében, hogy jobban megértse az ügyfél szemszögéből Microsoft Azure belül megvalósított hálózati biztonsági vezérlők gyűjteményét, ez a cikk az "Azure hálózati biztonság" című cikkben található, amely átfogó képet nyújt a hálózati biztonsági ellenőrzésekről elérhető Microsoft Azure.

A tanulmány célja, hogy tájékoztassa az Azure-ban üzembe helyezett megoldások biztonságának javításához konfigurálható hálózati vezérlők széles köréről. Ha azt szeretné, hogy a Microsoft miként gondoskodik az Azure platform hálózati hálójának védelméről, tekintse meg a [Microsoft adatvédelmi](https://microsoft.com/en-us/trustcenter/cloudservices/azure)központjának Azure Security című szakaszát.

## <a name="azure-platform"></a>Azure-platform

Az Azure egy nyilvános felhőalapú szolgáltatási platform, amely az operációs rendszerek, a programozási nyelvek, a keretrendszerek, az eszközök, az adatbázisok és az eszközök széles választékát támogatja.  Linux-tárolókat is futtathat a Docker-integrációval; alkalmazások készítése JavaScript, Python, .NET, PHP, Java és Node. js használatával az iOS-, Android-és Windows-eszközökön is létrehozhatók háttérrendszer. Az Azure Cloud Services több millió fejlesztőt és informatikai szakembereket is támogat.

Ha a-t vagy a-t egy nyilvános felhőalapú szolgáltatóra építi, vagy áttelepíti az informatikai eszközöket, akkor az adott szervezet képességei a szolgáltatások és az adatok védelmére támaszkodnak a felhőalapú eszközök biztonságának kezeléséhez.

Az Azure infrastruktúráját úgy alakították ki, hogy a létesítményből egyszerre több millió ügyfelet üzemeltető alkalmazások számára biztosítson egy megbízható alapot, amely alapján a vállalatok megfelelnek a biztonsági követelményeknek. Emellett az Azure a konfigurálható biztonsági beállítások széles skáláját biztosítja, és lehetővé teszi azok szabályozását, így testre szabhatja a biztonságot, hogy megfeleljenek a szervezete üzembe helyezésének egyedi követelményeinek.

## <a name="abstract"></a>Absztrakt

A Microsoft nyilvános felhőalapú szolgáltatásai Hyper-Scale szolgáltatásokat és infrastruktúrát, nagyvállalati szintű képességeket és számos lehetőséget biztosítanak a hibrid kapcsolatokhoz. Ezeket a szolgáltatásokat az interneten vagy az Azure ExpressRoute keresztül érheti el, amely magánhálózati kapcsolatot biztosít. A Microsoft Azure platformmal zökkenőmentesen kiterjesztheti infrastruktúráját a felhőbe, és többrétegű architektúrákat építhet ki. Emellett a harmadik felek a biztonsági szolgáltatásokat és a virtuális berendezéseket is lehetővé teszik a fejlett funkciók engedélyezését.

Az Azure hálózati szolgáltatásai a tervezéssel maximalizálják a rugalmasságot, a rendelkezésre állást, a rugalmasságot, a biztonságot és az integritást. Ez a tanulmány részletesen ismerteti az Azure hálózati funkcióit, valamint azt, hogy az ügyfelek hogyan használhatják az Azure natív biztonsági funkcióit az információs eszközök védelmére.

A tanulmány célközönsége a következőkre terjed ki:

- Technikai vezetők, hálózati rendszergazdák és fejlesztők, akik az Azure-ban elérhető és támogatott biztonsági megoldásokat keresnek.

-   A KKV-k és az üzleti folyamatok vezetői magas szintű áttekintést kívánnak kapni az Azure-beli nyilvános felhőben a hálózati biztonsággal kapcsolatos megbeszélésekhez kapcsolódó Azure hálózati technológiákról és szolgáltatásokról.

## <a name="azure-networking-big-picture"></a>Azure hálózatkezelés – nagyméretű kép
Microsoft Azure tartalmaz egy robusztus hálózati infrastruktúrát, amely támogatja az alkalmazások és szolgáltatások csatlakozási követelményeit. A hálózati kapcsolat az Azure-ban, a helyszíni és az Azure által üzemeltetett erőforrások, valamint az internetről és az Azure-ból származó erőforrások között lehetséges.

![Azure hálózatkezelés – nagyméretű kép](./media/network-security/azure-network-security-fig-1.png)

Az [Azure hálózati infrastruktúra](../../virtual-machines/windows/infrastructure-example.md) lehetővé teszi az Azure-erőforrások biztonságos összekapcsolását a Virtual Networks (virtuális hálózatok) szolgáltatással. A VNet a saját hálózatának ábrázolása a felhőben. A VNet az előfizetéséhez dedikált Azure Cloud Network logikai elkülönítése. A virtuális hálózatok a helyszíni hálózatokhoz is csatlakoztathatók.

Az Azure támogatja a dedikált WAN-kapcsolatot a helyszíni hálózattal és egy Azure-Virtual Network a [ExpressRoute](../../expressroute/expressroute-introduction.md). Az Azure és a webhely közötti kapcsolat olyan dedikált kapcsolatot használ, amely nem a nyilvános interneten keresztül érhető el. Ha az Azure-alkalmazás több adatközpontban fut, az [azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) segítségével a felhasználóktól érkező kéréseket intelligens módon irányíthatja át az alkalmazás példányai között. Az Azure-ban nem futó szolgáltatásokra is átirányíthatja a forgalmat, ha azok elérhetők az internetről.

## <a name="enterprise-view-of-azure-networking-components"></a>Az Azure hálózati összetevőinek nagyvállalati nézete
Az Azure számos hálózati összetevővel rendelkezik, amelyek a hálózati biztonsági megbeszélésekhez kapcsolódnak. leírjuk ezeket a hálózati összetevőket, és a velük kapcsolatos biztonsági problémákra koncentrálunk.

> [!Note]
> Az Azure hálózatkezelésének nem minden aspektusa van leírva – csak azokat a szempontokat tárgyaljuk, amelyek az Azure-ban üzembe helyezett szolgáltatások és alkalmazások körébe tartozó biztonságos hálózati infrastruktúra megtervezésében és kialakításában is megtekinthetők.

Ebben a dokumentumban a következő Azure Networking Enterprise-képességekre fog vonatkozni:

-   Alapszintű hálózati kapcsolat

-   Hibrid kapcsolat

-   Biztonsági vezérlők

-   Hálózati ellenőrzés

### <a name="basic-network-connectivity"></a>Alapszintű hálózati kapcsolat

Az [azure Virtual Network](../../virtual-network/virtual-networks-overview.md) szolgáltatás lehetővé teszi az Azure-erőforrások biztonságos összekapcsolását a Virtual Networks (VNet) használatával. A VNet a saját hálózatának ábrázolása a felhőben. A VNet az előfizetéséhez dedikált Azure hálózati infrastruktúra logikai elkülönítése. A helyek közötti VPN-eket és a dedikált [WAN-kapcsolatokat](../../expressroute/expressroute-introduction.md)használva virtuális hálózatok is csatlakozhat egymáshoz és a helyszíni hálózatokhoz.

![Alapszintű hálózati kapcsolat](./media/network-security/azure-network-security-fig-2.png)

Azzal a feltétellel, hogy virtuális gépeket használ az Azure-beli kiszolgálók üzemeltetéséhez, a kérdés az, hogy ezek a virtuális gépek hogyan csatlakoznak a hálózathoz. A válasz az, hogy a virtuális gépek csatlakoznak egy [Azure](../../virtual-network/virtual-networks-overview.md)-Virtual Networkhoz.

Az Azure-beli virtuális hálózatok olyanok, mint a helyszíni virtuális hálózatok saját virtualizációs platform-megoldásokkal, például a Microsoft Hyper-V vagy a VMware használatával.

#### <a name="intra-vnet-connectivity"></a>VNet-kapcsolat

Összekapcsolhatja a virtuális hálózatok egymással, és engedélyezheti, hogy a VNet kapcsolódó erőforrások a virtuális hálózatok-on keresztül kommunikáljanak egymással. A virtuális hálózatok egymáshoz való összekapcsolásához a következő lehetőségek egyikét vagy mindkettőt használhatja:

- **Társviszony** Lehetővé teszi, hogy a különböző Azure-virtuális hálózatok kapcsolódó erőforrások ugyanazon az Azure-helyen kommunikáljanak egymással. A sávszélesség és a késés a VNet között ugyanaz, mint ha az erőforrások ugyanahhoz a VNet csatlakoztak. Ha többet szeretne megtudni a peering szolgáltatással kapcsolatban, olvassa el a [virtuális hálózat](../../virtual-network/virtual-network-peering-overview.md)társítása című témakört.

  ![Társviszony-létesítés](./media/network-security/azure-network-security-fig-3.png)

- **VNet – VNet kapcsolatok:** Lehetővé teszi, hogy a különböző Azure-VNet kapcsolódó erőforrások ugyanazon vagy különböző Azure-helyszíneken legyenek elérhetők. A közvetítéstől eltérően a sávszélesség korlátozott a virtuális hálózatok között, mert az adatforgalomnak egy Azure-VPN Gatewayon kell átesnie.

![VNet és VNet közötti kapcsolatok](./media/network-security/azure-network-security-fig-4.png)


Ha többet szeretne megtudni a virtuális hálózatok VNet-VNet kapcsolattal való csatlakoztatásáról, olvassa el a [VNet-kapcsolat konfigurálása című cikket](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Azure-beli virtuális hálózati képességek:

Amint láthatja, az Azure Virtual Network virtuális gépeket biztosít a hálózathoz való kapcsolódáshoz, hogy biztonságos módon csatlakozhassanak más hálózati erőforrásokhoz. Az alapszintű kapcsolat azonban csak a kezdet. Az Azure Virtual Network szolgáltatás következő képességei az Azure Virtual Network biztonsági jellemzőit teszik elérhetővé:

-   Elkülönítés

-   Internetkapcsolat

-   Azure-erőforrás kapcsolata

-   VNet-kapcsolat

-   Helyszíni kapcsolatok

-   Forgalomszűrés

-   Útválasztás

**Elkülönítés**

A virtuális hálózatok [](../../virtual-network/virtual-networks-overview.md) egymástól el vannak különítve. Létrehozhat külön virtuális hálózatok a fejlesztési, tesztelési és éles környezetekhez, amelyek ugyanazt a [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) -címtartományt használják. Ezzel együtt több virtuális hálózatok is létrehozhat, amelyek különböző CIDR használnak, és összekapcsolják a hálózatokat. A VNet több alhálózatra is szegmentálhatja.

Az Azure belső névfeloldást biztosít a virtuális gépekhez és [Cloud Services](https://azure.microsoft.com/services/cloud-services/) VNet csatlakoztatott szerepkör-példányokhoz. Opcionálisan konfigurálhat egy VNet a saját DNS-kiszolgálók használatára, az Azure belső névfeloldásának használata helyett.

Az Azure-előfizetések és az [](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure- [régiók](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json)között több virtuális hálózatok is megvalósítható. Minden egyes virtuális hálózat el különítve a többi virtuális hálózatok. Az egyes VNet a következőket teheti:

-   Megadhat egy egyéni magánhálózati IP-címteret nyilvános és magánhálózati (RFC 1918) címek használatával. Az Azure a VNet csatlakoztatott erőforrásokat rendeli hozzá egy magánhálózati IP-címhez a címtartomány alapján.

-   Csoportosítsa a VNet egy vagy több alhálózatra, és rendelje hozzá a VNet-címtartomány egy részét az egyes alhálózatokhoz.

-   Használja az Azure által biztosított névfeloldást, vagy adja meg saját DNS-kiszolgálóját a VNet csatlakoztatott erőforrások számára. Ha többet szeretne megtudni a névfeloldásról a virtuális hálózatok-ben, olvassa el a [virtuális gépek és a Cloud Services](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)névfeloldását ismertető témakört.

**Internetkapcsolat**

A VNet csatlakoztatott összes [Azure Virtual Machines (VM)](../../virtual-machines/windows/index.yml) és Cloud Services szerepkör-példány alapértelmezés szerint elérhető az internethez. Igény szerint adott erőforrásokhoz is engedélyezheti a bejövő hozzáférést. A (VM) és Cloud Services VNet csatlakoztatott szerepkör-példányok alapértelmezés szerint az internethez férnek hozzá. Igény szerint adott erőforrásokhoz is engedélyezheti a bejövő hozzáférést.

A VNet csatlakozó összes erőforrás alapértelmezés szerint kimenő kapcsolattal rendelkezik az internethez. Az erőforrás magánhálózati IP-címe az Azure-infrastruktúra által a nyilvános IP-címhez lefordított forrásoldali hálózati cím (SNAT). Az alapértelmezett kapcsolatot az egyéni Útválasztás és forgalom szűrésével módosíthatja. A kimenő internetkapcsolattal kapcsolatos további tudnivalókért olvassa el a [Kimenő kapcsolatok ismertetése az Azure-ban](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört.

Ha az internetről bejövő Azure-erőforrásokat szeretne továbbítani, vagy ha SNAT nélkül szeretne kommunikálni az interneten, az erőforrásnak nyilvános IP-címet kell rendelnie. Ha többet szeretne megtudni a nyilvános IP-címekről, olvassa el a [nyilvános IP-címeket](../../virtual-network/virtual-network-public-ip-address.md).

**Azure-erőforrás kapcsolata**

Az [Azure-erőforrások](../../virtual-network/virtual-networks-overview.md) , például a Cloud Services és a virtuális gépek is csatlakoztathatók ugyanahhoz a VNet. Az erőforrások privát IP-címekkel csatlakozhatnak egymáshoz, még akkor is, ha különböző alhálózatokon vannak. Az Azure alapértelmezett útválasztást biztosít az alhálózatok, a virtuális hálózatok és a helyszíni hálózatok között, így nem kell konfigurálnia és kezelnie az útvonalakat.

Több Azure-erőforrást is összekapcsolhat egy VNet, például Virtual Machines (VM), Cloud Services, App Service környezettel és Virtual Machine Scale Setsokkal. A virtuális gépek egy hálózati adapteren (NIC) keresztül csatlakoznak a VNet lévő alhálózathoz. Ha többet szeretne megtudni a hálózati adapterekről, olvassa el a [hálózati adaptereket](../../virtual-network/virtual-network-network-interface.md).

**VNet-kapcsolat**

A [virtuális hálózatok](../../virtual-network/virtual-networks-overview.md) csatlakozhatnak egymáshoz, és lehetővé teszik, hogy bármely VNet kapcsolódó erőforrások bármilyen más VNet bármilyen erőforrással kommunikáljanak.

Összekapcsolhatja a virtuális hálózatok egymással, és engedélyezheti, hogy a VNet kapcsolódó erőforrások a virtuális hálózatok-on keresztül kommunikáljanak egymással. A virtuális hálózatok egymáshoz való összekapcsolásához a következő lehetőségek egyikét vagy mindkettőt használhatja:

- **Társviszony** Lehetővé teszi, hogy a különböző Azure-virtuális hálózatok kapcsolódó erőforrások ugyanazon az Azure-helyen kommunikáljanak egymással. A sávszélesség és a késés a virtuális hálózatok között ugyanaz, mint ha az erőforrások ugyanahhoz a VNet.To csatlakoznak, további információ a társításról: a [virtuális hálózat](../../virtual-network/virtual-network-peering-overview.md)társításának olvasása.

- **VNet – VNet kapcsolatok:** Lehetővé teszi, hogy a különböző Azure-VNet kapcsolódó erőforrások ugyanazon vagy különböző Azure-helyszíneken legyenek elérhetők. A közvetítéstől eltérően a sávszélesség korlátozott a virtuális hálózatok között, mert az adatforgalomnak egy Azure-VPN Gatewayon kell átesnie. További információ a virtuális hálózatok VNet-VNet kapcsolattal való csatlakoztatásáról. További információért olvassa el a [VNet-VNet közötti kapcsolatok konfigurálása](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) című témakört.

**Helyszíni kapcsolatok**

A virtuális hálózatok a hálózat és az Azure közötti magánhálózati kapcsolaton keresztül, illetve az interneten keresztül helyek közötti VPN-kapcsolaton keresztül csatlakozhatnak [a](../../virtual-network/virtual-networks-overview.md) helyszíni hálózatokhoz.

A helyszíni hálózatot a következő lehetőségek bármely kombinációja segítségével kapcsolhatja össze egy VNet:

- **Pont – hely típusú virtuális magánhálózat (VPN):** A hálózatához és a VNet csatlakoztatott egyetlen számítógép között jön. Ez a kapcsolattípus remek választás, ha csak most ismerkedik az Azure szolgáltatással, illetve a fejlesztők számára, mert a meglévő hálózatot csak kis mértékben vagy egyáltalán nem kell módosítani. A kapcsolat az SSTP protokollt használja az interneten keresztüli titkosított kommunikáció biztosítására a számítógép és a VNet között. Egy pont – hely típusú VPN késése előre nem látható, mert a forgalom az interneten halad át.

- **Helyek közötti VPN:** A VPN-eszköz és egy Azure-VPN Gateway között létrejött. Ez a kapcsolattípus minden olyan helyszíni erőforrást engedélyez, amelyet Ön engedélyez a VNet való hozzáféréshez. A kapcsolat egy IPsec/IKE VPN, amely titkosított kommunikációt biztosít az interneten keresztül a helyszíni eszköz és az Azure VPN Gateway között. A helyek közötti kapcsolat késése előre nem látható, mert a forgalom az interneten halad át.

- **Azure ExpressRoute:** A hálózat és az Azure között egy ExpressRoute-partneren keresztül létesítettük. Ez a kapcsolat nem nyilvános. A forgalom nem haladja meg az internetet. A ExpressRoute-kapcsolatok késése előre jelezhető, mert a forgalom nem halad át az interneten. Ha többet szeretne megtudni az összes korábbi kapcsolódási lehetőségről, olvassa el a [kapcsolódási topológia diagramjait](../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Forgalomszűrés**

A virtuális gép és a Cloud Services szerepkör példányai a [hálózati forgalom](../../virtual-network/virtual-networks-overview.md) a forrás IP-címe és portja, a cél IP-címe és portja, valamint a protokoll alapján szűrhető a bejövő és a kimenő.

A hálózati forgalom alhálózatok közötti szűrése az alábbi lehetőségek egyikével vagy akár mindkettővel elvégezhető:

- **Hálózati biztonsági csoportok (NSG):** Mindegyik NSG több bejövő és kimenő biztonsági szabályt is tartalmazhat, amelyek lehetővé teszik a forgalom szűrését a forrás és a cél IP-címe, a port és a protokoll alapján. NSG alkalmazhat egy virtuális gép minden hálózati adapterén. NSG is alkalmazhat egy hálózati adapter vagy más Azure-erőforrás alhálózatához, amelyhez csatlakozik. Ha többet szeretne megtudni a NSG, olvassa el a [hálózati biztonsági csoportokat](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

- **Virtual Network berendezések:** A virtuális hálózati készülékek olyan virtuális gépek, amelyek hálózati funkciót (például tűzfalat) végrehajtó szoftvert futtatnak. Tekintse meg az elérhető NVA listáját az Azure Marketplace-en. A NVA olyanok is elérhetők, amelyek WAN-optimalizálást és más hálózati adatforgalmi funkciókat biztosítanak. A NVA általában felhasználó által definiált vagy BGP-útvonalakkal használják. NVA is használhat a virtuális hálózatok közötti adatforgalom szűrésére.

**Útválasztás**

Opcionálisan felülbírálhatja az Azure alapértelmezett útválasztását a saját útvonalak konfigurálásával vagy a BGP-útvonalak hálózati átjárón keresztül történő használatával.

Az Azure útválasztási táblákat hoz létre, amelyek lehetővé teszik, hogy bármely VNet bármely alhálózatához csatlakozó erőforrások alapértelmezés szerint kommunikálhassanak egymással. Az Azure által létrehozott alapértelmezett útvonalak felülírásához valósítsa meg az alábbi lehetőségek egyikét, vagy akár mindkettőt:

- **Felhasználó által megadott útvonalak:** Létrehozhat egyéni útválasztási táblákat olyan útvonalakkal, amelyekkel szabályozható, hogy az egyes alhálózatok hová irányítsák a forgalmat. Ha többet szeretne megtudni a felhasználó által megadott útvonalakról, olvassa el a [felhasználó által megadott útvonalakat](../../virtual-network/virtual-networks-udr-overview.md).

- **BGP-útvonalak:** Ha a VNet egy Azure VPN Gateway vagy ExpressRoute-kapcsolat használatával csatlakoztatja a helyszíni hálózathoz, a BGP-útvonalakat propagálhatja a virtuális hálózatok.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Hibrid internetkapcsolat: Kapcsolódás helyszíni hálózathoz
A helyszíni hálózatot a következő lehetőségek bármely kombinációja segítségével kapcsolhatja össze egy VNet:

-   Internetkapcsolat

-   Pont – hely típusú VPN (P2S VPN)

-   Helyek közötti VPN (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Internetkapcsolat

Ahogy a neve is sugallja, az internetkapcsolat lehetővé teszi a számítási feladatok internetről való elérését azáltal, hogy a virtuális hálózaton belül élő munkaterhelésekhez különböző nyilvános végpontokat tesz elérhetővé. Ezek a számítási feladatok az internetre irányuló [Load Balancer](../../load-balancer/load-balancer-overview.md#publicloadbalancer) használatával vagy egyszerűen egy nyilvános IP-cím kiosztásával tehetők elérhetővé a virtuális géphez. Így bármilyen módon elérhetővé válik az interneten, hogy el tudja érni a virtuális gépet, ha a gazdagép tűzfala, a [hálózati biztonsági csoportok (NSG-EK)](../../virtual-network/virtual-network-vnet-plan-design-arm.md)és a [felhasználó által megadott útvonalak](../../virtual-network/virtual-networks-udr-overview.md) lehetővé teszik, hogy megtörténjen.

Ebben a forgatókönyvben egy olyan alkalmazást tehet közzé, amelynek nyilvánosnak kell lennie az internethez, és képesnek kell lennie arra, hogy bárhonnan vagy adott helyekről csatlakozhasson a számítási feladatok konfigurációjától függően.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Pont – hely típusú VPN vagy helyek közötti VPN
Ez a kettő ugyanabba a kategóriába tartozik. Mindkettőnek szüksége van a VNet, hogy legyen egy VPN Gateway, és csatlakozni tud hozzá a munkaállomás VPN-ügyfelének használatával a [pont – hely konfiguráció](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) részeként, vagy beállíthatja a helyszíni [VPN-eszközt](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) , hogy képes legyen a helyek közötti kapcsolat megszakítására. VPN-. Így a helyszíni eszközök kapcsolódhatnak a VNet lévő erőforrásokhoz.

A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat egy SSTP (Secure Socket Tunneling Protocol) használatával működő VPN-kapcsolat.

![Pont – hely VPN](./media/network-security/azure-network-security-fig-5.png)

A pont – hely kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a VNet, például otthonról vagy konferencia-központról, vagy ha csak néhány ügyfélnek kell csatlakoznia egy virtuális hálózathoz.

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. A VPN-kapcsolatot az ügyfélszámítógépről hozhatja létre. Ezért a P2S nem ajánlott az Azure-hoz csatlakozni, ha az Azure-hálózathoz számos helyszíni eszköz és számítógép állandó kapcsolata szükséges.

![Site-to-Site VPN](./media/network-security/azure-network-security-fig-6.png)

> [!Note]
> A pont – hely kapcsolatokkal kapcsolatos további információkért lásd a [pont – hely fa v Q-t](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül.

Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. Ez a kapcsolat az interneten keresztül zajlik, és lehetővé teszi, hogy a hálózat és az Azure közötti titkosított kapcsolaton belül "alagút" információkat helyezzen el. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. Az alagút titkosítása [IPsec-alagút mód](https://technet.microsoft.com/library/cc786385.aspx)használatával történik.

Míg a helyek közötti VPN megbízható, megbízható és kialakított technológia, az alagúton belüli forgalom az interneten halad át. Emellett a sávszélesség viszonylag korlátozott, de legfeljebb 200 MB/s.

Ha a létesítmények közötti kapcsolatok esetében kivételes szintű biztonsági vagy teljesítményre van szüksége, javasoljuk, hogy az Azure ExpressRoute-t használja a létesítmények közötti kapcsolathoz. A ExpressRoute egy dedikált WAN-kapcsolat a helyszíni hely vagy egy Exchange-szolgáltató között. Mivel ez egy távközlési kapcsolat, az adatai nem jutnak el az interneten keresztül, ezért nem teszik elérhetővé az internetes kommunikációban rejlő lehetséges kockázatokat.

> [!Note]
> További információ a VPN-átjárókkal kapcsolatban: tudnivalók a [VPN Gateway-](../../vpn-gateway/vpn-gateway-about-vpngateways.md)ről.

#### <a name="dedicated-wan-link"></a>Dedikált WAN-kapcsolat
Microsoft Azure ExpressRoute lehetővé teszi a helyszíni hálózatok kibővítését az Azure-ba egy dedikált privát kapcsolaton keresztül, amely egy kapcsolati szolgáltató által könnyíti meg.

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.

![ Dedikált WAN-kapcsolat](./media/network-security/azure-network-security-fig-7.png)

> [!Note]
> További információ a hálózatnak a Microsofthoz való csatlakoztatásáról a ExpressRoute használatával: [ExpressRoute kapcsolati modellek](../../vpn-gateway/vpn-gateway-about-vpngateways.md) és [ExpressRoute technikai áttekintés](../../expressroute/expressroute-introduction.md).

A helyek közötti VPN-beállításokhoz hasonlóan a ExpressRoute is lehetővé teszi, hogy olyan erőforrásokhoz kapcsolódjon, amelyek nem feltétlenül csak egy VNet vannak. Valójában az SKU-tól függően 10 virtuális hálózatok tud csatlakozni. Ha [prémium szintű bővítménye](../../expressroute/expressroute-faqs.md)van, a sávszélességtől függően akár 100 virtuális hálózatok-kapcsolat is lehetséges. Ha többet szeretne megtudni arról, hogy az ilyen típusú kapcsolatok hogyan néznek ki, olvassa el a [kapcsolati topológiai diagramok](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört.

### <a name="security-controls"></a>Biztonsági vezérlők
Az Azure Virtual Network biztosít egy biztonságos, logikai hálózatot, amely más virtuális hálózatoktól el van különítve, és számos, a helyszíni hálózatokon használt biztonsági vezérlőt támogat. Az ügyfelek saját struktúrát hozhatnak létre a következő használatával: alhálózatok – saját magánhálózati IP-címtartományt használnak, útválasztási táblákat, hálózati biztonsági csoportokat, hozzáférés-vezérlési listákat (ACL-eket), átjárókat és virtuális berendezéseket használhatnak a Felhőbeli számítási feladatok futtatásához.

Az Azure-beli virtuális hálózatokon a következő biztonsági vezérlők használhatók:

-   Hálózati hozzáférés-vezérlés

-   Felhasználó által megadott útvonalak

-   Hálózati biztonsági berendezés

-   Application Gateway

-   Azure webalkalmazási tűzfal

-   Hálózat rendelkezésre állásának vezérlése

#### <a name="network-access-controls"></a>Hálózati hozzáférés-vezérlés
Míg az Azure Virtual Network (VNet) az Azure hálózati modell sarokköve, és az elkülönítést és védelmet biztosít, a hálózati [biztonsági csoportok (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) a hálózati forgalomra vonatkozó szabályok betartatásához és szabályozásához használt fő eszköz.

![ Hálózati hozzáférés-vezérlés](./media/network-security/azure-network-security-fig-8.png)


A hozzáférés szabályozása a virtuális hálózaton belüli munkaterhelések közötti kommunikáció engedélyezésével vagy megtagadásával, az ügyfél hálózatán lévő rendszerekkel a létesítmények közötti kapcsolaton vagy közvetlen internetes kommunikáción keresztül.

A diagramon mind a virtuális hálózatok, mind a NSG az Azure összesített biztonsági veremének egy adott rétegében található, ahol a NSG, a UDR és a hálózati virtuális berendezések biztonsági határokat hozhatnak létre a védett hálózatban található alkalmazások központi telepítésének védelme érdekében.

A NSG 5 rekord használatával értékeli a forgalmat (és a NSG konfigurált szabályokban szerepel):

-   [Forrás és cél IP-címe](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Forrás-és célport](https://technet.microsoft.com/library/dd197515)

-   Protokoll: [Transmission Control Protocol (TCP)](https://technet.microsoft.com/library/cc940037.aspx) vagy [User Datagram Protocol (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Ez azt jelenti, hogy az egyetlen virtuális gép és a virtuális gépek csoportja, illetve egy virtuális gép és egy másik virtuális gép, illetve a teljes alhálózatok közötti hozzáférés szabályozására van lehetőség. Ne feledje, hogy ez egyszerű, állapot-nyilvántartó csomagszűrés, nem teljes csomagok ellenőrzése. A hálózati biztonsági csoportban nincs protokoll-ellenőrzési vagy hálózati szintű azonosító vagy IP-cím képesség.

A NSG olyan beépített szabályokkal is rendelkezik, amelyekkel tisztában kell lennie. Ezek a következők:

-   **Egy adott virtuális hálózaton belüli összes forgalom engedélyezése:** Az azonos Azure-Virtual Network lévő összes virtuális gép képes kommunikálni egymással.

-   **Az Azure terheléselosztás bejövőként való engedélyezése:**  ez a szabály engedélyezi bármely forráscím forgalmát az Azure Load Balancer bármely célcím számára.

-   **Az összes bejövő utasítás tiltása:**  ez a szabály blokkolja az internetről származó összes forgalmat, amelyet explicit módon engedélyezett.

-   **Az internethez kimenő összes forgalom engedélyezése:** Ez a szabály lehetővé teszi, hogy a virtuális gépek kapcsolatot kezdeményezzenek az internettel. Ha nem szeretné, hogy ezek a kapcsolatok inicializálva legyenek, létre kell hoznia egy szabályt a kapcsolatok blokkolásához vagy a kényszerített bújtatás kényszerítéséhez.

#### <a name="system-routes-and-user-defined-routes"></a>Rendszerútvonalak és felhasználó által megadott útvonalak

Ha virtuális gépeket (VM-ket) ad hozzá egy virtuális hálózathoz (VNet) az Azure-ban, megfigyelheti, hogy a virtuális gépek képesek kommunikálni egymással a hálózaton keresztül, automatikusan. Nem kell megadni átjárót, akkor sem, ha a virtuális gépek külön alhálózatokon vannak.

Ugyanez vonatkozik a virtuális gépek és a nyilvános internet közötti kommunikációra, és akár a helyszíni hálózatra is, ha jelen van egy hibrid kapcsolat az Azure és a saját adatközpont között.

![Rendszerútvonalak](./media/network-security/azure-network-security-fig-9.png)

A kommunikáció ilyen típusú áramlása azért lehetséges, mert az Azure rendszerútvonalak sorát használja az IP-cím adatforgalmának meghatározására. A rendszerútvonalak az alábbi helyzetekben irányítják a kommunikáció áramlását:

-   Ha ugyanazon az alhálózaton belül történik.

-   Két alhálózat között történik egy virtuális hálózaton belül.

-   Virtuális gépek és az internet között történik.

-   Két VNet között történik egy VPN-átjárón keresztül.

-   Egy VNet egy másik VNet a VNet-közvetítésen keresztül (a[szolgáltatás láncolása](../../virtual-network/virtual-network-peering-overview.md)).

-   Egy VNet és a helyszíni hálózat között történik egy VPN-átjárón keresztül.

Számos vállalat rendelkezik szigorú biztonsági és megfelelőségi követelményekkel, amelyek megkövetelik az összes hálózati csomag helyszíni vizsgálatát az egyes házirendek betartatása érdekében. Az Azure egy kényszerített [bújtatás](../../vpn-gateway/vpn-gateway-about-forced-tunneling.md) nevű mechanizmust biztosít, amely a virtuális gépekről a helyszíni forgalomra irányítja az egyéni útvonal vagy [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) hirdetmények létrehozásával a ExpressRoute vagy a VPN használatával.

Kényszerített bújtatás az Azure-beli virtuális hálózati felhasználó által megadott útvonalak (UDR) keresztül van konfigurálva. Egy alapértelmezett útvonalat az Azure VPN gateway kifejezése egy helyszíni helyre irányítja a forgalmat.

A következő szakaszban azok az Azure Virtual Network az útválasztási tábla-és útvonalak a jelenlegi korlátozás:

- Minden egyes virtuális hálózat alhálózatához rendelkezik egy beépített, rendszer-útválasztási táblázatához. A rendszer útválasztási tábla az útvonalak a következő három csoport rendelkezik:

  -  **Helyi VNet útvonalak:** Közvetlenül a cél virtuális gépekhez ugyanazon a virtuális hálózaton

  - **Helyszíni útvonalak:** Az Azure VPN Gateway-hez

  -  **Alapértelmezett útvonal:** Közvetlenül az internethez. Az előző két útvonal által nem jelzett magánhálózati IP-címekre irányuló csomagokat a rendszer elveti.

- A felhasználó által megadott útvonalak kiadásával létrehozhat egy útválasztási táblázatot egy alapértelmezett útvonal hozzáadásához, majd társíthatja az útválasztási táblázatot a VNet-alhálózathoz, hogy engedélyezze a kényszerített bújtatást ezeken az alhálózatokon.

- Meg kell állítania egy "alapértelmezett webhely" a létesítmények közötti helyek között a virtuális hálózathoz csatlakozik.

- Kényszerített bújtatás kell rendelni egy virtuális hálózattal, amely dinamikus útválasztási VPN-átjáró (nem a statikus átjárók) rendelkezik.

- Kényszerített bújtatás ExpressRoute nincs konfigurálva ez a mechanizmus keresztül, de ehelyett szerint engedélyezve van a társviszony-létesítési ExpressRoute BGP-munkamenetek használatával egy alapértelmezett útvonalat hirdet.

> [!Note]
> További információkért tekintse meg a [ExpressRoute dokumentációját](https://azure.microsoft.com/documentation/services/expressroute/) .

#### <a name="network-security-appliances"></a>Hálózati biztonsági berendezések
Míg a hálózati biztonsági csoportok és a felhasználó által megadott útvonalak bizonyos mértékű hálózati biztonságot biztosíthatnak az [OSI-modell](https://en.wikipedia.org/wiki/OSI_model)hálózati és szállítási rétegeiben, olyan helyzetek lesznek, amikor szeretné, hogy a biztonság magasabb szinten legyen hálózati verem. Ilyen helyzetekben ajánlott az Azure-partnerek által biztosított virtuális hálózati biztonsági berendezések üzembe helyezése.

![Hálózati biztonsági berendezések](./media/network-security/azure-network-security-fig-10.png)

Az Azure hálózati biztonsági berendezések javítják a VNet biztonsági és hálózati funkcióit, és az [Azure Marketplace](https://azuremarketplace.microsoft.com)-en keresztül számos gyártótól elérhetők. Ezek a virtuális biztonsági berendezések az alábbiak biztosítására használhatók:

-   Magasan elérhető tűzfalak

-   Behatolás-megelőzés

-   Behatolás észlelése

-   Webalkalmazási tűzfalak (WAF)

-   WAN-optimalizálás

-   Útválasztás

-   Terheléselosztás

-   VPN

-   Tanúsítványok kezelése

-   Active Directory

-   Többtényezős hitelesítés

#### <a name="application-gateway"></a>Alkalmazásátjáró

A [Microsoft Azure Application Gateway](../../application-gateway/overview.md) egy dedikált virtuális berendezés, amely egy alkalmazás-továbbítási vezérlőt (ADC) biztosít szolgáltatásként.

 ![Application Gateway](./media/network-security/azure-network-security-fig-11.png)

A Application Gateway lehetővé teszi a webfarm teljesítményének és rendelkezésre állásának optimalizálását azáltal, hogy kiszervezi a CPU-igényes SSL-lezárást az Application Gateway felé (SSL-kiürítés). Emellett további 7. rétegbeli útválasztási lehetőségeket is biztosít, beleértve a következőket:

-   A bejövő forgalom ciklikus időszeleteléses eloszlása

-   Cookie-alapú munkamenet-affinitás

-   URL-elérésiút-alapú útválasztás

-   Több webhely üzemeltetése egyetlen Application Gateway


A [webalkalmazási tűzfal (WAF)](../../application-gateway/waf-overview.md) az Application Gateway részeként is elérhető. Ez védelmet nyújt a webes alkalmazásoknak a gyakori webes sebezhetőségek és a biztonsági rések ellen. Application Gateway konfigurálható internetkapcsolattal rendelkező átjáróként, csak belső átjáróként vagy mindkettő kombinációjával.

Application Gateway WAF az észlelési vagy megelőzési módban is futtatható. Gyakori használati eset a rendszergazdák számára, hogy észlelési módban fussanak a rosszindulatú minták forgalmának megfigyeléséhez. Ha a rendszer a potenciális biztonsági réseket észleli, a megelőzési mód bekapcsolásával blokkolja a gyanús bejövő forgalmat.

 ![Application Gateway](./media/network-security/azure-network-security-fig-12.png)

Emellett a Application Gateway WAF segítségével figyelheti a webalkalmazásokat a támadások ellen olyan valós idejű WAF-napló használatával, amely integrálva van [Azure monitor](../../azure-monitor/overview.md) és [Azure Security Center](https://azure.microsoft.com/services/security-center/) a WAF-riasztások nyomon követésére és a trendek egyszerű figyelésére.

A JSON formátumú napló közvetlenül az ügyfél Storage-fiókjába kerül. A naplók teljes körű vezérléssel rendelkeznek, és a saját adatmegőrzési szabályzatait is alkalmazhatják.

Ezeket a naplókat a saját elemzési rendszerébe is betöltheti [Azure log Integration](https://aka.ms/AzLog)használatával. A WAF-naplók integrálva vannak [Azure monitor naplókkal](/azure/log-analytics/log-analytics-overview) is, így Azure monitor naplók használatával kifinomult, részletes lekérdezéseket végezhetnek el.

#### <a name="azure-web-application-firewall-waf"></a>Azure webalkalmazási tűzfal (WAF)

A webalkalmazások egyre gyakrabban célozzák meg a kártékony támadásokat, amelyek a gyakori ismert biztonsági réseket, például az SQL-injektálást, a helyközi parancsfájlok támadásait és az [OWASP](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)-ben megjelenő más támadásokat használják. Az alkalmazásban az ilyen sebezhetőségek megelőzése az alkalmazás topológiájának több rétegében szigorú karbantartást, javítást és figyelést igényel.

 ![Azure webalkalmazási tűzfal (WAF)](./media/network-security/azure-network-security-fig-13.png)

A központosított [webalkalmazási tűzfal (WAF)](../../application-gateway/waf-overview.md) védelmet nyújt a webes támadások ellen, és egyszerűbbé teszi a biztonsági felügyeletet az alkalmazások módosítása nélkül.

Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

#### <a name="network-availability-controls"></a>Hálózati rendelkezésre állási vezérlők

A Microsoft Azure-ral többféleképpen lehet elosztani a hálózati forgalmat. Ezek a lehetőségek különböző működési elveken alapulnak, különböző funkciókészlettel rendelkeznek és különböző forgatókönyveket támogatnak. Elkülönítve vagy egymással kombinálva egyaránt használhatók.

A hálózati rendelkezésre állási vezérlők a következők:

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Azure Load Balancer**

Magas rendelkezésre állást és hálózati teljesítményt biztosít alkalmazásai számára. Ez egy 4. rétegbeli (TCP, UDP) terheléselosztó, amely a bejövő forgalmat egy elosztott terhelésű készletben definiált szolgáltatások kifogástalan példányai között osztja szét.

 ![Azure Load Balancer](./media/network-security/azure-network-security-fig-14.png)


A Azure Load Balancer a következőre konfigurálható:

-   A bejövő internetes forgalom terheléselosztása a virtuális gépekre. Ezt a konfigurációt internetre irányuló [terheléselosztásnak](../../load-balancer/load-balancer-overview.md#publicloadbalancer)nevezzük.

-   A virtuális hálózatban lévő virtuális gépek, a Cloud Services-beli virtuális gépek, illetve a helyszíni számítógépek és a virtuális gépek közötti adatforgalom terheléselosztása egy telephelyi virtuális hálózaton. Ezt a konfigurációt [belső terheléselosztásnak](../../load-balancer/load-balancer-overview.md#internalloadbalancer)nevezzük.

-   Külső forgalom továbbítása egy adott virtuális géphez.

A felhőben lévő összes erőforrásnak nyilvános IP-címmel kell rendelkeznie ahhoz, hogy elérhető legyen az internetről. Az Azure-beli felhőalapú infrastruktúra nem irányítható IP-címeket használ az erőforrásaihoz. Az Azure hálózati címfordítást (NAT) használ nyilvános IP-címekkel az internethez való kommunikációhoz.

 **Application Gateway**

 A Application Gateway az alkalmazás rétegében működik (7. réteg az OSI hálózati hivatkozási veremben). Fordított proxyszolgáltatásként működik, az ügyfélkapcsolatok leállítását és a kérelmek háttérvégpontokhoz való továbbítását végzi.

 **Traffic Manager**

Microsoft Azure Traffic Manager lehetővé teszi a szolgáltatás-végpontok felhasználói forgalmának a különböző adatközpontokban való elosztásának szabályozását. A Traffic Manager által támogatott szolgáltatási végpontok közé tartoznak az Azure-beli virtuális gépek, a Web Apps és a Cloud Services. A Traffic Manager külső, nem Azure-végpontokkal együtt is használható.

Traffic Manager a tartománynévrendszer (DNS) használatával irányítja az ügyfelek kérelmeit a legmegfelelőbb végpontra a [forgalmi útválasztási módszer](../../traffic-manager/traffic-manager-routing-methods.md) és a végpontok állapota alapján. Traffic Manager számos forgalom-útválasztási módszert kínál a különböző alkalmazási igények kielégítésére, a végpont [](../../traffic-manager/traffic-manager-monitoring.md)állapotának figyelésére és az automatikus feladatátvételre. A Traffic Manager ellenáll a meghibásodásoknak, beleértve akár egy egész Azure-régió meghibásodását is.

Az Azure Traffic Manager segítségével szabályozhatja az alkalmazás-végpontok forgalmának eloszlását. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás.

Traffic Manager két fő előnyt biztosít:

-   A forgalom eloszlása a különböző [forgalom-útválasztási módszerek](../../traffic-manager/traffic-manager-routing-methods.md)egyike szerint.

-   [A végpont állapotának](../../traffic-manager/traffic-manager-monitoring.md) és automatikus feladatátvételének folyamatos figyelése, ha a végpontok meghiúsulnak.

Amikor egy ügyfél megpróbál csatlakozni egy szolgáltatáshoz, először fel kell oldania a szolgáltatás DNS-nevét egy IP-címhez. Az ügyfél ezután csatlakozik ehhez az IP-címhez a szolgáltatás eléréséhez. Traffic Manager a DNS használatával irányítja az ügyfeleket bizonyos szolgáltatási végpontokra a forgalom-útválasztási módszer szabályai alapján. Az ügyfelek közvetlenül csatlakoznak a kiválasztott végponthoz. Traffic Manager nem proxy vagy átjáró. Traffic Manager nem látja az ügyfél és a szolgáltatás közötti adatforgalmat.

### <a name="azure-network-validation"></a>Azure-hálózat ellenőrzése

Az Azure-hálózat ellenőrzése annak biztosítása, hogy az Azure-hálózat megfelelően legyen konfigurálva, és az érvényesítés a hálózat figyeléséhez elérhető szolgáltatásokkal és szolgáltatásokkal végezhető el. Az Azure Network Watcher segítségével számos naplózási és diagnosztikai képességet elérhet, amelyek segítségével megismerheti a hálózati teljesítményt és állapotot. Ezek a képességek a portál, a Power shell, a CLI, a REST API és az SDK használatával érhetők el.

Az Azure Operational Security a felhasználók számára elérhető szolgáltatásokat, vezérlőket és szolgáltatásokat jelenti a Microsoft Azureban tárolt adatok, alkalmazások és egyéb eszközök védelmére. Az Azure Operational Security egy olyan keretrendszerre épül, amely magában foglalja a Microsoft számára egyedi, a Microsoft biztonságos fejlesztési életciklusát (SDL) és a Microsoft Security Response Center programját , valamint a Cyber biztonsági fenyegetések tájképének részletes ismerete.

-   [Azure Security Center](../../security-center/security-center-intro.md)

-   [Azure Monitor](../../azure-monitor/overview.md)

-   [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)

-   [Azure Storage-elemzés](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Azure Resource Manager

A Microsoft Azuret üzemeltető személyek és folyamatok talán a platform legfontosabb biztonsági funkciója. Ez a szakasz a Microsoft globális adatközpont-infrastruktúrájának azon funkcióit ismerteti, amelyek segítik a biztonságot, a folytonosságot és az adatvédelmet.

Az alkalmazás infrastruktúrája általában számos összetevőből áll – például egy virtuális gépből, tárfiókból és virtuális hálózatból, vagy egy webalkalmazásból, adatbázisból, adatbázis-kiszolgálóból és harmadik féltől származó szolgáltatásokból. Ezeket az összetevőket nem külön entitásokként látja, hanem egyetlen entitás kapcsolódó és egymással összefüggő részeiként. Csoportként érdemes telepíteni, kezelni és megfigyelni őket. Az Azure Resource Manager lehetővé teszi, hogy a megoldásában az erőforrásokkal egy csoportként dolgozzon.

A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. A telepítéshez egy sablon használatos, amely különböző, például tesztelési, átmeneti és üzemi környezetben is képes működni. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően.

**A Resource Manager használatának előnyei**

A Resource Manager számos előnyt kínál:

-   A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

-   A megoldást ismételten telepítheti a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

-   Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.

-   Megadhatja az erőforrások közötti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

-   Hozzáférés-vezérlést alkalmazhat az összes szolgáltatásra az erőforráscsoportban, mivel a szerepköralapú hozzáférés-vezérlés (RBAC) natív módon integrálva van a felügyeleti platformba.

-   Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.

-   Megtekintheti a szervezete számlázását úgy, hogy megtekinti az erőforrások megosztására szolgáló kódelemek egy csoportjának költségeit.

> [!Note]
> A Resource Manager egy új módot kínál a megoldások telepítésére és kezelésére. Ha a korábbi telepítési modellt használta, és további információkat kíván megtudni a változásokról, tekintse meg [A Resource Manager telepítés és a hagyományos telepítés ismertetése](../../azure-resource-manager/resource-manager-deployment-model.md) című cikket.

## <a name="azure-network-logging-and-monitoring"></a>Azure hálózati naplózás és figyelés

Az Azure számos eszközt kínál a hálózati biztonsági események figyelésére, megelőzésére, észlelésére és reagálására. Az Ön számára elérhető leghatékonyabb eszközök közé tartoznak a következők:

-   Network Watcher

-   Hálózati erőforrások szintjének figyelése

-   Azure Monitor-naplók

### <a name="network-watcher"></a>Hálózati figyelő

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) – forgatókönyv-alapú figyelés a Network Watcher szolgáltatásaival. Ez a szolgáltatás magában foglalja a csomagok rögzítését, a következő ugrást, az IP-forgalom ellenőrzését, a biztonsági csoport nézetét, a NSG folyamat naplóit. A forgatókönyvek szintjének figyelése lehetővé teszi a hálózati erőforrások teljes körű megtekintését az egyes hálózati erőforrások figyelésével szemben.

 ![Network Watcher](./media/network-security/azure-network-security-fig-15.png)

Network Watcher egy regionális szolgáltatás, amely lehetővé teszi az Azure-ban, a-ben és az-ban lévő hálózati forgatókönyvek szintjének figyelését és diagnosztizálását. A Network Watcher elérhető hálózati diagnosztikai és vizualizációs eszközök segítségével megismerheti, diagnosztizálhatja és elemezheti a hálózatát az Azure-ban.

A Network Watcher jelenleg a következő képességekkel rendelkezik:

#### <a name="topology"></a>Topológia

A [topológia](../../network-watcher/view-network-topology.md) egy virtuális hálózat hálózati erőforrásainak gráfját adja vissza. A gráf ábrázolja az erőforrások közötti kapcsolatot, amely a végpontok közötti hálózati kapcsolatot jelöli. A portálon a topológia a virtuális hálózat alapján adja vissza az erőforrás-objektumokat. A kapcsolatokat a Network Watcher régión kívüli erőforrások közötti vonalak mutatják, még akkor is, ha az erőforráscsoport nem fog megjelenni. A portál nézetben visszaadott erőforrások a hálózati összetevők egy részét képezik. A hálózati erőforrások teljes listáját a [PowerShell](../../network-watcher/view-network-topology.md) vagy a [Rest](../../network-watcher/view-network-topology.md)segítségével tekintheti meg.

Az erőforrások visszaadásakor a rendszer két kapcsolat között modellezi a kapcsolatot.

- A **Container** -Virtual Network tartalmaz egy alhálózatot, amely egy hálózati adaptert tartalmaz.

- **Társított** – a hálózati adapter egy virtuális géphez van társítva.

#### <a name="variable-packet-capture"></a>Változó csomagrögzítés

Network Watcher [változó csomagok rögzítése](../../network-watcher/network-watcher-packet-capture-overview.md) lehetővé teszi a csomagok rögzítési munkameneteinek létrehozását a virtuális gépekre irányuló és onnan érkező forgalom nyomon követéséhez. A csomagok rögzítése segít a hálózati rendellenességek reaktív és proaktív módon történő diagnosztizálásában. A többi felhasználás magában foglalja a hálózati statisztikák összegyűjtését, a hálózati behatolásokkal kapcsolatos információk megszerzését, az ügyfél és a kiszolgáló közötti kommunikáció hibakeresését.

A csomagok rögzítése olyan virtuálisgép-bővítmény, amely Network Watcheron keresztül távolról elindult. Ez a funkció megkönnyíti a csomagok manuális futtatásának terhét a kívánt virtuális gépen, amely értékes időt takarít meg. A csomagok rögzítése a portál, a PowerShell, a CLI vagy a REST API segítségével indítható el. A csomagok rögzítésének elindításának egyik példája a virtuális gépekkel kapcsolatos riasztások.

#### <a name="ip-flow-verify"></a>IP-folyamat ellenőrzése

Az [IP-folyamatok ellenőrzik](../../network-watcher/network-watcher-ip-flow-verify-overview.md) , hogy egy csomag engedélyezett-e vagy le van-e tiltva egy virtuális gépről 5 rekordos információ alapján. Ezek az információk irányból, protokollból, helyi IP-ről, távoli IP-ről, helyi portról és távoli portról állnak. Ha a csomagot egy biztonsági csoport megtagadja, a rendszer a csomagot megtagadó szabály nevét adja vissza. Bármilyen forrás vagy cél IP-cím választható, ez a szolgáltatás segít a rendszergazdáknak gyorsan diagnosztizálni a kapcsolati problémákat az internetről, illetve a vagy a helyszíni környezetből.

Az IP-folyamatok ellenőrzik a virtuális gép hálózati adapterét. Ezt követően a rendszer ellenőrzi, hogy a forgalmi folyamat a megadott hálózati adapterre vagy onnan érkező beállítások alapján van-e ellenőrizve. Ez a funkció akkor hasznos, ha a hálózati biztonsági csoport egyik szabálya blokkolja a virtuális gépek bejövő vagy kimenő forgalmát.

#### <a name="next-hop"></a>Következő ugrás

Meghatározza az Azure hálózati hálóban továbbított csomagok [következő ugrását](../../network-watcher/network-watcher-next-hop-overview.md) , amely lehetővé teszi a helytelenül konfigurált, felhasználó által megadott útvonalak diagnosztizálását. A virtuális gépekről érkező forgalmat a rendszer a hálózati adapterhez társított érvényes útvonalak alapján küldi el a célhelyre. A következő ugrás egy adott virtuális gépről és hálózati adapterről származó csomag következő ugrási típusát és IP-címét kérdezi le. Ez segít megállapítani, hogy a csomag a célhelyre van-e irányítva, vagy a forgalom fekete lyukban van-e.

A következő ugrás a következő ugráshoz társított útválasztási táblázatot is visszaadja. Amikor egy következő ugrást kérdez le, ha az útvonal felhasználó által definiált útvonalként van definiálva, akkor a rendszer az útvonalat adja vissza. Ellenkező esetben a következő ugrás a "rendszerútvonal" értéket adja vissza.

#### <a name="security-group-view"></a>Biztonságicsoport-nézet

A virtuális gépen alkalmazott hatályos és alkalmazott biztonsági szabályok beolvasása. A hálózati biztonsági csoportok alhálózatok vagy hálózati ADAPTERek szintjén vannak társítva. Ha az alhálózat szintjén van társítva, az az alhálózat összes virtuálisgép-példányára vonatkozik. A hálózati [biztonsági csoport nézet](../../network-watcher/network-watcher-security-group-view-overview.md) az összes olyan konfigurált NSG és szabályt visszaadja, amely a virtuális gép hálózati adapteréhez és alhálózatának szintjén van társítva, és betekintést nyújt a konfigurációba. Emellett az érvényes biztonsági szabályok a virtuális gépek egyes hálózati adapterei esetében is visszakerülnek. A hálózati biztonsági csoport nézet használatával olyan hálózati biztonsági réseket is felhasználhat, mint a nyitott portok. Azt is ellenőrizheti, hogy a hálózati biztonsági csoport a várt módon működik [-e a konfigurált és a hatályos biztonsági szabályok összehasonlítása](../../network-watcher/network-watcher-nsg-auditing-powershell.md)alapján.

#### <a name="nsg-flow-logging"></a>NSG folyamat naplózása

 A hálózati biztonsági csoportok folyamatábrája lehetővé teszi a csoport biztonsági szabályai által engedélyezett vagy megtagadott forgalomhoz kapcsolódó naplók rögzítését. A folyamatot 5 rekordos információ határozza meg – forrás IP-cím, célként megadott IP-cím, forrásport, célport és protokoll.

A [hálózati biztonsági csoport folyamatábrái](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) a Network Watcher szolgáltatása, amely lehetővé teszi, hogy a hálózati biztonsági csoporton keresztül megtekintse a bejövő és kimenő IP-forgalomra vonatkozó információkat.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Virtuális hálózati átjáró és a kapcsolatok hibaelhárítása

A Network Watcher számos képességgel rendelkezik, amelyek az Azure hálózati erőforrásainak megismeréséhez kapcsolódnak. Ezen képességek egyike az erőforrás-hibaelhárítás. Az [erőforrás](../../network-watcher/network-watcher-troubleshoot-manage-rest.md) -hibaelhárítást a PowerShell, a CLI vagy a REST API hívhatja. A híváskor Network Watcher megvizsgálja egy Virtual Network átjáró vagy egy kapcsolat állapotát, és visszaadja az eredményeit.

Ez a szakasz végigvezeti az erőforrás-hibaelhárításhoz jelenleg elérhető különböző felügyeleti feladatokon.

-   [Virtual Network átjáró hibáinak megoldása](../../network-watcher/network-watcher-troubleshoot-manage-rest.md)

-   [Kapcsolatok hibáinak megoldása](../../network-watcher/network-watcher-troubleshoot-manage-rest.md)

#### <a name="network-subscription-limits"></a>Hálózati előfizetési korlátok

A [hálózati előfizetések korlátai](../../network-watcher/network-watcher-monitoring-overview.md) a régióban lévő előfizetésben lévő egyes hálózati erőforrások használatának részleteit tartalmazzák a rendelkezésre álló erőforrások maximális száma ellenében.

#### <a name="configuring-diagnostics-log"></a>Diagnosztikai napló konfigurálása

A Network Watcher [diagnosztikai naplókat](../../network-watcher/network-watcher-monitoring-overview.md) jelenít meg. Ez a nézet a diagnosztikai naplózást támogató összes hálózati erőforrást tartalmazza. Ebből a nézetből kényelmesen és gyorsan engedélyezheti és tilthatja le a hálózati erőforrásokat.

### <a name="network-resource-level-monitoring"></a>Hálózati erőforrások szintjének figyelése

Az erőforrás szintű figyeléshez a következő funkciók érhetők el:

#### <a name="audit-log"></a>Napló

A rendszer naplózza a hálózatok konfigurációjának részeként végrehajtott műveleteket. Ezek a naplók elengedhetetlenek a különböző megfelelőségi követelmények kialakításához. Ezek a naplók a Azure Portalban tekinthetők meg, vagy a Microsoft-eszközök, például Power BI vagy külső gyártótól származó eszközök használatával kérhetők le. A naplók a portálon, a PowerShellen, a CLI-n és a REST API-n keresztül érhetők el.

> [!Note]
> A naplókkal kapcsolatos további információkért lásd: [műveletek naplózása a Resource Managerrel](../../azure-resource-manager/resource-group-audit.md).
A naplók az összes hálózati erőforráson végzett műveletekhez érhetők el.


#### <a name="metrics"></a>Mérőszámok

A metrikák egy adott időszakban mért teljesítmény-és számlálók. A metrikák jelenleg Application Gateway számára érhetők el. A metrikák a küszöbértékeken alapuló riasztások elindítására használhatók. Az Azure Application Gateway alapértelmezés szerint a háttér-készlet összes erőforrásának állapotát figyeli, és automatikusan eltávolítja a készletből a nem megfelelőnek ítélt erőforrásokat. Application Gateway folytatja a nem kifogástalan állapotú példányok figyelését, és visszaadja azokat az egészséges háttér-készlethez, amint elérhetővé válnak, és reagálnak az állapotra. Az Application Gateway az állapot-mintavételt ugyanazzal a porttal küldi el, mint amely a háttérbeli HTTP-beállításokban van meghatározva. Ez a konfiguráció biztosítja, hogy a mintavétel ugyanazt a portot vizsgálja, amelyet az ügyfelek a háttérhez való csatlakozáshoz használnak.

> [!Note]
> A riasztások létrehozásával kapcsolatos mérőszámok megtekintéséhez tekintse meg [Application Gateway diagnosztikát](../../application-gateway/application-gateway-probe-overview.md) .

#### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Az időszakos és spontán eseményeket a hálózati erőforrások és a bejelentkezett Storage-fiókok, az Event hub vagy a Azure Monitor naplók hozzák létre. Ezek a naplók betekintést nyújtanak egy erőforrás állapotára. Ezek a naplók olyan eszközökön tekinthetők meg, mint a Power BI és a Azure Monitor naplók. A diagnosztikai naplók megtekintésének megismeréséhez keresse fel [Azure monitor naplókat](../../azure-monitor/insights/azure-networking-analytics.md).

A diagnosztikai naplók a [Load Balancer](../../load-balancer/load-balancer-monitor-log.md), a [hálózati biztonsági csoportok](../../virtual-network/virtual-network-nsg-manage-log.md), az útvonalak és a [Application Gateway](../../application-gateway/application-gateway-diagnostics.md)számára érhetők el.

A Network Watcher diagnosztikai naplókat jelenít meg. Ez a nézet a diagnosztikai naplózást támogató összes hálózati erőforrást tartalmazza. Ebből a nézetből kényelmesen és gyorsan engedélyezheti és tilthatja le a hálózati erőforrásokat.

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

A [Azure monitor logs](../../log-analytics/log-analytics-queries.md) szolgáltatás az Azure-ban, amely figyeli a Felhőbeli és a helyszíni környezeteket a rendelkezésre állásuk és a teljesítményük fenntartása érdekében. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson.

Azure Monitor naplók a következő megoldásokat kínálja a hálózatok figyeléséhez:

-   Network Performance Monitor (NPM)

-   Azure Application Gateway Analitika

-   Azure hálózati biztonsági csoport elemzése

#### <a name="network-performance-monitor-npm"></a>Hálózati Teljesítményfigyelő (NPM)
A [Network Performance monitor](../../azure-monitor/insights/network-performance-monitor.md) felügyeleti megoldás egy hálózati figyelési megoldás, amely figyeli a hálózatok állapotát, rendelkezésre állását és elérhetőségét.

A következők közötti kapcsolat figyelésére használható:

-   nyilvános felhő és helyszíni

-   adatközpontok és felhasználói helyszínek (fiókirodák)

-   többrétegű alkalmazások különböző szintjeinek üzemeltetésére szolgáló alhálózatok.


#### <a name="azure-application-gateway-analytics-in-azure-monitor-logs"></a>Azure Application Gateway-elemzések Azure Monitor-naplókban

Az Application Gateway átjárók esetében az alábbi naplók támogatottak:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Az Application Gateway a következő metrikákat támogatja:

-   5 perces átviteli sebesség

#### <a name="azure-network-security-group-analytics-in-azure-monitor-logs"></a>Azure hálózati biztonsági csoport elemzése Azure Monitor naplókban

[Hálózati biztonsági csoportok](../../virtual-network/virtual-network-nsg-manage-log.md)esetén a következő naplók támogatottak:

- **NetworkSecurityGroupEvent:** Azokat a bejegyzéseket tartalmazza, amelyek NSG-szabályait a rendszer MAC-címen alapuló virtuális gépekre és példányokra alkalmazza. A szabályok állapotát 60 másodpercenként gyűjti a rendszer.

- **NetworkSecurityGroupRuleCounter:** Azokat a bejegyzéseket tartalmazza, amelyekkel az egyes NSG-szabályok a forgalom megtagadására vagy engedélyezésére vonatkoznak.

## <a name="next-steps"></a>További lépések
A biztonságról további információt a részletes biztonsági témakörökben talál:

-   [Hálózati biztonsági csoportok naplóinak Azure Monitor (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md)

-   [A Felhőbeli fennakadást okozó hálózatkezelési fejlesztések](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC A Microsoft globális Felhőjét felruházó hálózati kapcsoló szoftver](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Hogyan épít a Microsoft a gyors és megbízható globális hálózatot](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [A hálózati innováció megvilágítása](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
