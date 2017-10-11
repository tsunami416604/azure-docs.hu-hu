---
title: "Az Azure hálózati biztonság |} Microsoft Docs"
description: "További információk a felhőalapú számítási szolgáltatás, amely tartalmazza a számítási példányokért széles kijelölt & szolgáltatások, amely is fel-le automatikusan az alkalmazás vagy a vállalat igényeinek."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2017
ms.author: TomSh
ms.openlocfilehash: 6ab59dd02391287a1effc0b51502bb7eb90db319
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="azure-network-security"></a>Az Azure hálózati biztonság

Tudjuk, hogy biztonsági-e a feladat a felhőben, és hogy mennyire fontos, hogy található-e az Azure biztonsági pontos és időben információt. A legjobb okok miatt az alkalmazások és szolgáltatások Azure használandó egyik széles köréről Azure a biztonsági eszközök és képességek előnyeit. Ezekkel az eszközökkel és képességek segítségével történő biztonságos megoldások létrehozása az Azure platformon.

A Microsoft Azure a titkosítás, integritás és rendelkezésre állás ügyféladatok, biztosítása átlátszó elszámolási kötelezettségéről szóló biztosít. Segítséget jobb megértése érdekében a hálózati biztonsági vezérlők megvalósítva a Microsoft Azure-ban, az ügyfél szempontjából gyűjteménye, ez, "Azure hálózati biztonság", a cikk biztosításához egy átfogó nézze meg a hálózati biztonsági vezérlő érhető el a Microsoft Azure-ban.

A célja, hogy azonosítható, hogy a hálózati vezérlő kell továbbfejleszteni a biztonságot a megoldások Azure központi telepítését konfigurálhatja széles körét. Ha érdekli a Microsoft nem biztonságos a hálózati háló az Azure platform magát, az Azure biztonsági szakaszában talál a [Microsoft Trust Center](https://www.microsoft.com/trustcenter/security/azure-security).

## <a name="azure-platform"></a>Azure-platform

Azure egy nyilvános felhőszolgáltatási platform, amely támogatja az operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök széles körű kijelölés.  Linux-tárolók futtatható a Docker integrációja; alkalmazások, JavaScript, Python, .NET, PHP, Java és Node.js; build vissza-végpontok iOS, Android és Windows eszközökhöz. Azure felhőszolgáltatások támogatja a fejlesztők milliói technológiát és informatikai szakemberek számára már alapulnak, és megbízható.

Létrehozása vagy áttelepítése informatikai eszközök, a nyilvános felhőbeli szolgáltatás szolgáltatója, akkor vannak támaszkodva az alkalmazások és a szolgáltatások és a biztonsági a felhő alapú eszközök kezelésére adathordozóira vezérlők adatok védelme érdekében, hogy szervezete képességek.

Azure infrastruktúrája alkalmazások felhasználók millióit egyidejűleg üzemeltetéséhez a létesítmény a készült, és biztosít egy megbízható foundation, amelyre vállalatok megfelel a biztonsági követelményeknek. Emellett Azure biztosít konfigurálható biztonsági beállítások és képes azokat, hogy testre szabhatja a szervezet központi telepítések egyedi követelményeinek biztonsági széles körű gyűjteménye.

## <a name="abstract"></a>Absztrakt

Nyilvános felhőalapú Microsoft-szolgáltatás biztosításához kapacitású szolgáltatások és az infrastruktúra, a vállalati szintű képességet és a számos választhat való hibrid kapcsolathoz. Választhat, ezeket a szolgáltatásokat, vagy az interneten keresztül, vagy Azure ExpressRoute, amely magánhálózati kapcsolatot biztosít az elérésére. A Microsoft Azure platform zökkenőmentesen bővítheti az infrastruktúrát kiterjeszti a felhőbe, és hozhat létre többrétegű architektúrák számára teszi lehetővé. Emellett harmadik felek engedélyezéséhez bővített szolgáltatásokat nyújtó biztonsági szolgáltatásait és a virtuális készülékek.

Azure hálózati szolgáltatások maximalizálása érdekében a rugalmasságot, a rendelkezésre állási, a rugalmasság, a biztonságának és integritásának úgy lett kialakítva. Ez a dokumentum az Azure és az ügyfelek használatát Azure natív biztonsági funkcióival információs védelme érdekében a hálózati funkciók részleteit.

E tanulmány a megfelelő célközönség a következők:

- Műszaki kezelők, a hálózati rendszergazdák és fejlesztők számára elérhető és támogatott az Azure biztonsági megoldásokat keres.

-   Kis-és középvállalkozások vagy üzleti folyamat vezető szeretné, hogy a magas szintű áttekintését, az Azure hálózati technológiákat és szolgáltatásokat, amelyek kapcsolódnak az Azure nyilvános felhőjében a hálózati biztonsági körül beszélgetéseket.

## <a name="azure-networking-big-picture"></a>Azure-hálózat nagy vonalakban tekinti
A Microsoft Azure tartalmazza az alkalmazás és szolgáltatás hálózati kapcsolati követelményeinek támogatására robusztus hálózati infrastruktúrát. Helyszíni között, az Azure-ban lévő erőforrások közötti hálózati kapcsolatot, és Azure üzemeltetett erőforrásokhoz, és hogy, és az internetről és az Azure.

![Azure-hálózat nagy vonalakban tekinti](media/azure-network-security/azure-network-security-fig-1.png)

A [Azure hálózati infrastruktúra](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) lehetővé teszi, hogy biztonságosan kapcsolódjanak a Azure-erőforrások egymástól a virtuális hálózatokon (Vnetek). A virtuális hálózat a felhőben saját hálózati ábrázolása. A virtuális hálózat a hálózat az előfizetésre kijelölt Azure-felhőbe hálózati logikai elkülönítése. A helyszíni hálózatokhoz Vnetek csatlakozhatnak.

Az Azure által támogatott dedikált WAN-kapcsolaton kapcsolat a helyszíni hálózat és az Azure virtuális hálózat [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). A hely és az Azure közötti kapcsolat, amely nem halad át a nyilvános internethez dedikált kapcsolatot használ. Ha több adatközpontot az Azure alkalmazás fut, akkor használhatja [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) a felhasználók ezután több példányát kérelmek. Is irányíthatja a forgalmat a szolgáltatás nem fut az Azure-ban, ha azok elérhetők az internetről.

## <a name="enterprise-view-of-azure-networking-components"></a>Vállalati nézet Azure hálózati összetevők
Azure rendelkezik, amely kapcsolódik a hálózati biztonsági beszélgetéseket számos hálózati összetevők. azt írja le a hálózati összetevők pedig a őket kapcsolatos biztonsági problémák.

> [!Note]
> Nem minden szempontját Azure hálózatkezelés ismerteti – arról lesz szó csak azok tervezését és a biztonságos hálózati infrastruktúra a szolgáltatások és alkalmazások központi telepítése az Azure-ban kialakítását döntő számít.

A dokumentum tartalma a következő Azure lesz hálózati vállalati lehetőségeket:

-   Alapvető hálózati kapcsolat

-   A hibrid kapcsolat

-   Biztonsági vezérlőket

-   Hálózat érvényességének ellenőrzése

### <a name="basic-network-connectivity"></a>Alapvető hálózati kapcsolat

A [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) szolgáltatás lehetővé teszi, hogy biztonságosan kapcsolódjanak a Azure-erőforrások egymástól a virtuális hálózatot (VNet). A virtuális hálózat a felhőben saját hálózati ábrázolása. Egy Vnetet az Azure hálózati infrastruktúra előfizetéshez dedikált logikai elkülönítése. Elérhetők Vnetek csatlakoztatják egymáshoz és a pont-pont virtuális magánhálózatok használata a helyszíni hálózatokhoz és a dedikált [WAN-kapcsolatok](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Alapvető hálózati kapcsolat](media/azure-network-security/azure-network-security-fig-2.png)

A feltétellel, hogy használja a virtuális gépek az Azure-kiszolgálók, a kérdés esetén hogyan virtuális gépek csatlakoznak-e a hálózathoz. A válasz, virtuális gépek csatlakozni egy [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Egy Azure virtuális hálózatot például a virtuális hálózatok vannak a helyszíni használni a saját virtualizálási platform megoldások, például a Microsoft Hyper-V vagy VMware.

#### <a name="intra-vnet-connectivity"></a>Helyen belüli-VNet-kapcsolatot

Kapcsolódás Vnetek egymással, vagy VNet kommunikálhassanak egymással Vnetek csatlakoztatott engedélyezése. Valamelyike vagy mindegyike a következő beállítások segítségével Vnetek csatlakozni egymáshoz:

- **Társviszony-létesítés:** lehetővé teszi az erőforrások egymással kommunikálni az Azure ugyanazon a helyen belül különböző Azure Vnetekhez csatlakoztatni. A sávszélesség és a virtuális hálózat között késleltetés megegyezik, mintha az erőforrások csatlakoztatva ugyanazt a virtuális hálózatot. Társviszony-létesítés kapcsolatos további tudnivalókért olvassa el [virtuális hálózati társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

 ![Társviszony-létesítés](media/azure-network-security/azure-network-security-fig-3.png)

- **VNet – VNet-kapcsolatot:** lehetővé teszi, hogy az ugyanazon vagy másik Azure helyek belül különböző Azure VNet kapcsolódó erőforrásokhoz. Ellentétben a társviszony-létesítést, sávszélesség oka Vnetek közötti forgalmat kell áramlása az Azure VPN Gateway.

![VNet – VNet-kapcsolatot](media/azure-network-security/azure-network-security-fig-4.png)


VNet – VNet-kapcsolatot Vneteket összekötő kapcsolatos további tudnivalókért olvassa el a [konfigurálja a VNet – VNet-kapcsolatot cikk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Azure-beli virtuális hálózat képességek:

Ahogy látja, akkor az Azure virtuális hálózat biztosít a virtuális gépek, hogy más hálózati erőforrásokhoz, biztonságos módon csatlakozhatnak a hálózathoz való kapcsolódáshoz. Hálózati kapcsolat, azonban ez csak a kezdet. Az Azure Virtual Network szolgáltatás az alábbi képességeket teszi közzé az Azure Virtual Network security jellemzői:

-   Elkülönítés

-   Internetkapcsolat

-   Az Azure erőforrás-kapcsolat

-   VNet-kapcsolatot

-   Helyszíni kapcsolatok

-   Forgalomszűrést végez

-   Útválasztás

**Elkülönítés**

Vnetek [elkülönített](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) egymástól. Létrehozhat, amelyek ugyanazon fejlesztési, tesztelési és éles külön Vnetek [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) blokkok cím. Ezzel szemben, amelyek különböző CIDR címblokkokat és összekapcsolhatja az hálózatok több Vnetek is létrehozhat. Egy VNet érdekében több alhálózatra is szegmentálhatja.

Azure belső névfeloldást biztosít a virtuális gépek és [Felhőszolgáltatások](https://azure.microsoft.com/services/cloud-services/) szerepkörpéldányokat csatlakozik egy Vnetet. Konfigurálhatja egy VNet és a saját DNS-kiszolgálók használata az Azure belső névfeloldást használata helyett.

Minden Azure belül több Vnetek Megvalósíthat [előfizetés](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) és az Azure [régió](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json). Minden egyes virtuális hálózat el különítve a más Vnetekről. Minden egyes vnet a következő műveletek végezhetők el:

-   Adjon meg egy egyéni privát IP-címtér nyilvános és titkos (az RFC 1918) címeket használnak. Erőforrások Azure rendel a virtuális hálózat egy magánhálózati IP-cím keresztül kapcsolódik a címterület, rendeli.

-   A VNet szegmentálni be egy vagy több alhálózatból, és foglaljon le a VNet-címterek minden alhálózat egy része.

-   Azure által biztosított névfeloldás használata, vagy adjon meg egy Vnetet csatlakoztatott a saját DNS-kiszolgáló számára. Névfeloldás a Vnetek kapcsolatos további tudnivalókért olvassa el a [névfeloldását virtuális gépek és Felhőszolgáltatások](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Internetkapcsolat**

Minden [Azure virtuális gépek (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) , és csatlakozik egy Vnetet Felhőszolgáltatások szerepkörpéldányok Internet,-hozzáférés alapértelmezés szerint. Bejövő hozzáférés adható konkrét erőforrásokhoz, engedélyezheti igény szerint is. (VM), és csatlakozik egy Vnetet Felhőszolgáltatások szerepkörpéldányok Internet,-hozzáférés alapértelmezés szerint. Bejövő hozzáférés adható konkrét erőforrásokhoz, engedélyezheti igény szerint is.

Egy Vnetet kapcsolódó összes erőforrásokhoz alapértelmezés szerint kimenő képes kapcsolódni az internetre rendelkeznek. A magánhálózati IP-cím erőforrás hálózati forráscím lefordított (SNAT) által az Azure-infrastruktúra egy nyilvános IP-cím. Egyéni Útválasztás és a forgalom szűrés alkalmazásával módosíthatja az alapértelmezett működését. Kimenő internetkapcsolattal kapcsolatos további tudnivalókért olvassa el a [ismertetése az Azure-ban kimenő kapcsolatok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Való kommunikációra bejövő Azure-erőforrások az internetről, vagy az internethez nélkül SNAT kimenő kommunikációra, egy erőforrást egy nyilvános IP-címet kell hozzárendelve. Nyilvános IP-címek kapcsolatos további tudnivalókért olvassa el a [nyilvános IP-címek](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Az Azure erőforrás-kapcsolat**

[Azure-erőforrások](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) például a Felhőszolgáltatások és virtuális gépek is csatlakoztathatók ugyanazt a virtuális hálózatot. Az erőforrások egymást magánhálózati IP-címek is csatlakozhat, ha külön alhálózatokon vannak. Azure biztosít alapértelmezett között alhálózatok, a Vnetek és a helyszíni hálózatokhoz, így nem kell konfigurálnia és felügyelnie az útvonalakat.

Több Azure-erőforrások csatlakozhat a virtuális hálózat, például a virtuális gépek (VM), a Cloud Services, az App Service Environment-környezetek és a virtuálisgép-méretezési készlet. Virtuális gépek csatlakozni egy alhálózatot a hálózati adaptert (NIC) keresztül egy Vneten belül. Hálózati adapter kapcsolatos további tudnivalókért olvassa el a [hálózati illesztőt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**VNet-kapcsolatot**

[Vnetek](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) csatlakozhatnak egymáshoz, csatlakoznak bármely virtuális hálózat bármely más virtuális hálózaton erőforrás kommunikálni erőforrások engedélyezése.

Kapcsolódás Vnetek egymással, vagy VNet kommunikálhassanak egymással Vnetek csatlakoztatott engedélyezése. Valamelyike vagy mindegyike a következő beállítások segítségével Vnetek csatlakozni egymáshoz:

- **Társviszony-létesítés:** lehetővé teszi az erőforrások egymással kommunikálni az Azure ugyanazon a helyen belül különböző Azure Vnetekhez csatlakoztatni. A sávszélesség és a késleltetés között a Vnetek: ugyanaz, mintha csak az azonos VNet.To csatlakoztatva az erőforrások tudhat meg többet társviszony-létesítést, olvassa el a [virtuális hálózati társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **VNet – VNet-kapcsolatot:** lehetővé teszi, hogy az ugyanazon vagy másik Azure helyek belül különböző Azure VNet kapcsolódó erőforrásokhoz. Ellentétben a társviszony-létesítést, sávszélesség oka Vnetek közötti forgalmat kell áramlása az Azure VPN Gateway. További információt a Vneteket összekötő VNet – VNet-kapcsolatot. További tudnivalókért olvassa el a [VNet – VNet-kapcsolatot konfiguráló](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Helyszíni kapcsolatok**

A vnetek való [helyszíni](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) hálózatok magánhálózati kapcsolatok a hálózati és az Azure között, vagy az interneten keresztül a pont-pont VPN-kapcsolaton keresztül.

A helyszíni hálózat csatlakozhatnak egy virtuális hálózat használatával tetszőleges kombinációját az alábbiak közül:

- **Pont-pont virtuális magánhálózati (VPN):** a egyetlen számítógép csatlakozik a hálózathoz és a virtuális hálózat között. A kapcsolat típusa nem nagyszerű, ha Ön most csak az első lépések az Azure-ral, vagy a fejlesztők számára, mert azt a meglévő hálózati kevéssé vagy egyáltalán ne módosítását igényli. A kapcsolat az SSTP protokoll segítségével, titkosított kommunikációt biztosít a számítógép és a virtuális hálózat között az interneten keresztül. A pont-pont típusú VPN várakozási is előre nem látható, mivel a forgalom halad át az interneten.

- **Telephelyek közötti VPN:** a VPN-eszköz és az Azure VPN-átjáró között. A kapcsolat típusa lehetővé teszi, hogy az összes helyszíni erőforrás is engedélyezni szeretné a virtuális hálózat eléréséhez. A kapcsolat az IPsec/IKE VPN, amely titkosított kommunikációt biztosít az interneten, a helyszíni eszközök és az Azure VPN gateway között. A pont-pont kapcsolat a késési is előre nem látható, mivel a forgalom halad át az interneten.

- **Az Azure ExpressRoute:** hoznak létre egy ExpressRoute-partner keresztül a hálózat és az Azure-ban. Ezt a kapcsolatot a sajátja. Forgalom nem bejárják az interneten. Az ExpressRoute-kapcsolat a késési is előre jelezhető, mivel a forgalom nem haladnak át az interneten. Az előző kapcsolódási beállítás kapcsolatos további tudnivalókért olvassa el a [kapcsolat topológiai diagramot](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Forgalomszűrés**

Virtuális gép és a Felhőszolgáltatások szerepkörpéldányokat [hálózati forgalom](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) bejövő és kimenő alapján szűrhetők forrás IP-cím és port, cél IP-cím és port és protokoll.

Szűrheti a hálózati forgalom valamelyike vagy mindegyike a következő beállítások segítségével alhálózatok között:

- **Hálózati biztonsági csoportok (NSG):** minden NSG tartalmazhat több bejövő és kimenő biztonsági szabály lehetővé teszi, hogy a forrás és cél IP-cím, port és protokoll forgalmának szűrésére. Akkor is alkalmazzon NSG-t egyes hálózati adapterek virtuális gépen. Az NSG-t is alkalmazhat az alhálózat egy hálózati Adaptert, vagy más Azure-erőforrás, csatlakozik-e. Ha többet szeretne megtudni az NSG-k, olvassa el a [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Virtuális hálózati berendezések:** egy virtuális hálózati berendezések egy hálózati funkciót, például egy tűzfal végző szoftvert futtató virtuális gép. Az Azure piactéren elérhető NVAs listájának megtekintése. NVAs is elérhetők, hogy WAN-optimalizálást és az egyéb hálózati forgalom funkciókat biztosít. NVAs általában használt felhasználói vagy a BGP-útvonalakat. NVA Vnetek közötti forgalom szűrésére is használhatja.

**Útválasztás**

Azure alapértelmezett útválasztás konfigurálása a saját útvonalakat, vagy a hálózati átjáró BGP-útvonalakat segítségével igény szerint felülbírálható.

Az útvonaltáblák, amelyek lehetővé teszik az erőforrások csatlakoznak bármely kommunikálhatnak egymással, alapértelmezés szerint a virtuális hálózat egyetlen alhálózatának sem az Azure létrehoz. Megvalósíthat valamelyike vagy mindegyike felülbírálhatja az alapértelmezett útvonalak Azure-hoz létre a következő beállításokat:

- **Felhasználó által definiált útvonalak:** hozhat létre egyéni útvonaltáblák útvonalak adott vezérlőn, ahol továbbítódik a az egyes alhálózatokon. Felhasználó által definiált útvonalak kapcsolatos további tudnivalókért olvassa el a [felhasználó által definiált útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **BGP-útvonalakat:** Ha a virtuális hálózat csatlakozni a helyszíni hálózat az Azure VPN-átjáró vagy ExpressRoute kapcsolattal, akkor kerülhet BGP-útvonalakat a Vnetek.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Hibrid internetkapcsolat: egy a helyszíni hálózat
A helyszíni hálózat csatlakozhatnak egy virtuális hálózat használatával tetszőleges kombinációját az alábbiak közül:

-   Internetkapcsolat

-   Pont – hely típusú VPN (P2S VPN)

-   Telephelyek közötti VPN (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Internetkapcsolat

A neve alapján, mint internetkapcsolat elérhetővé válnak a munkaterhelések az internetről, azzal, hogy Ön teszi közzé a különböző nyilvános végpontok, amely a virtuális hálózaton belüli élő. Az ilyen terhelések sikerült elérhetővé tehető használatával [internetre terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) vagy egyszerűen csak egy nyilvános IP-cím hozzárendelése a virtuális Gépet. Ezzel a módszerrel válik lehetővé semmit sem kell tudni érnie a virtuális gép által megadott állomás tűzfal, az Internet [hálózati biztonsági csoportok (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg), és [felhaszn útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) lehetővé teszi, hogy megtörténjen-e.

Ebben a forgatókönyvben egy alkalmazás, amelyet a nyilvános az internethez, és kapcsolódhatnak ahhoz bárhol, vagy a munkaterhelések konfigurációjától függően meghatározott helyeiről származó teheti ki.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Pont – hely típusú VPN vagy a telephelyek közötti VPN
Ez a két azonos a kategóriába tartozik. Mindkét van szükségük a virtuális hálózat VPN-átjáró, és kapcsolódhat az segítségével a VPN-ügyfél a munkaállomás részeként a [pont-hely konfigurációs](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) vagy konfigurálhatja a helyszíni [VPN-eszköz](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) tenni a telephelyek közötti VPN leáll. Ezzel a módszerrel a helyszíni eszközök a Vneten belül erőforrások csatlakozhat.

A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat egy SSTP (Secure Socket Tunneling Protocol) használatával működő VPN-kapcsolat.

![Pont – hely típusú VPN](media/azure-network-security/azure-network-security-fig-5.png)

Pont – hely kapcsolatok hasznosak, ha szeretne csatlakozni a virtuális hálózat egy távoli helyről, például home vagy egy konferencia center, vagy ha csak néhány ügyfelek számára, amelyek egy virtuális hálózathoz való kapcsolódáshoz.

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. A VPN-kapcsolatot az ügyfélszámítógépről hozhatja létre. P2S, ezért nem javasolt módon csatlakoztathatják az Azure-bA abban az esetben, ha az Azure-hálózat számos helyszíni eszközök és számítógépek egy állandó kapcsolatot kell.

![Telephelyek közötti VPN](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Pont – hely kapcsolatok kapcsolatos további információkért tekintse meg a [pont-pont be v Q](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül.

Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi a "tunnel" információ belül a hálózati és az Azure közötti egy titkosított kapcsolat. Telephelyek közötti VPN egy biztonságos, érett technológia, amely különböző méretű vállalatok által évtizedeken van telepítve. Bújtatás titkosítás használatával történik [IPsec-bújtatásos mód](https://technet.microsoft.com/library/cc786385.aspx).

A megbízható, megbízható és a meghatározott technológiája pedig a telephelyek közötti VPN a belül a alagút forgalmi haladnak át az interneten. Ezenkívül sávszélesség viszonylag korlátozódik legfeljebb körülbelül 200 MB/s.

A létesítmények közötti kapcsolatokat. szükséges egy rendkívüli szintű biztonsági vagy a teljesítmény, azt javasoljuk, hogy a létesítmények közötti kapcsolatot használjon Azure ExpressRoute. ExpressRoute dedikált WAN a helyszíni hely vagy egy Exchange-szolgáltató közötti kapcsolat. Mivel ez egy telco kapcsolat, a az adatokat az interneten keresztül nem utazik, és ezért nem érhető el az internetes adatátvitel járó esetleges kockázatokat.

> [!Note]
> További információ a VPN-átjárók tudnivalók [VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Dedikált WAN-kapcsolat
A Microsoft Azure ExpressRoute lehetővé teszi a helyszíni hálózatokhoz kiterjeszti az Azure a kapcsolat szolgáltatójánál könnyíteni dedikált titkos kapcsolaton keresztül.

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.

![ Dedikált WAN-kapcsolat](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> A Microsoft ExpressRoute segítségével a hálózati csatlakozás információkért lásd: [ExpressRoute modellek](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) és [ExpressRoute műszaki áttekintés](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Mint az a pont-pont VPN-beállításokat, ExpressRoute is lehetővé teszi, amelyek nem feltétlenül csak egy virtuális erőforrások eléréséhez. Valójában attól függően, hogy a Termékváltozat csatlakozhat 10 Vnetek. Ha rendelkezik a [prémium szintű bővítmény](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), akár 100 Vnetek kapcsolatokat is előfordulhatnak, attól függően, hogy a sávszélesség. Az ilyen típusú kapcsolatokat keresse meg például kapcsolatos további tudnivalókért olvassa el [kapcsolat topológiai diagramot](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Biztonsági vezérlőket
Egy Azure virtuális hálózatra biztosít egy biztonságos, logikai hálózathoz el különítve a többi virtuális hálózatok, amely támogatja több biztonsági vezérlő, amelyet a helyszíni hálózatokhoz. Az ügyfelek létre saját struktúra használatával: alhálózatok – azok a saját privát IP-címtartományt használja, az útvonaltáblák konfigurálása, a hálózati biztonsági csoportok, hozzáférés-vezérlési listák (ACL), átjárók, és a felhőben való futtatásra a munkaterheléseket a virtuális készülékek.

Az Azure virtuális hálózaton használható biztonsági vezérlők a következők:

-   Hálózati hozzáférés-vezérlést

-   Felhasználó által definiált útvonalak

-   Hálózati biztonsági készülékek

-   Application Gateway

-   Az Azure webalkalmazási tűzfal

-   Hálózati rendelkezésre állás vezérlő

#### <a name="network-access-controls"></a>Hálózati hozzáférés-vezérlést
Amíg az Azure Virtual Network (VNet) Azure hálózatmodell legfontosabb feladatai közé tartoznak, és biztosítja a leválasztásra és a védelemre, a [hálózati biztonsági csoportok (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) kényszerítése, és szabályozhatja a hálózati forgalomra vonatkozó szabályok hálózati szintű fő eszközei.

![ Hálózati hozzáférés-vezérlést](media/azure-network-security/azure-network-security-fig-8.png)


Hozzáférést lehetővé tevő vagy elutasítja a futó alkalmazások és szolgáltatások a virtuális hálózaton, az ügyfél hálózatokon keresztül közötti kapcsolatot nyújthassanak, rendszerek közötti kommunikációt, vagy közvetlen Internet-kommunikációt.

A diagramon Vnetek és NSG-ket is találhatók, az Azure teljes biztonsági verem, ahol az NSG-k, UDR és virtuális hálózati berendezések segítségével az alkalmazások központi telepítéseit a védett hálózat védelme érdekében biztonsági határokat hozzon létre egy adott rétegben.

NSG-ket egy 5 rekordos segítségével értékelje ki a forgalom (és az konfigurál az NSG-szabályok használnak):

-   [Forrás és cél IP-cím](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Forrás és cél port](https://technet.microsoft.com/library/dd197515)

-   Protokoll: [Transmission Control Protocol (TCP)](https://technet.microsoft.com/library/cc940037.aspx) vagy [User Datagram Protocol (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Ez azt jelenti, szabályozhatja a hozzáférést egy virtuális és a virtuális gépeket, vagy egy másik egyetlen virtuális gép egyetlen virtuális Gépet egy csoportját, vagy teljes alhálózatok között. Ebben az esetben ne feledje, hogy ez a szűrés, egyszerű csomag Csomagvizsgálat nem teljes. Nincs protokoll érvényesítési vagy hálózati szintek Azonosítói vagy IP-CÍMEK a hálózati biztonsági csoport képességét.

Az NSG-t, meg kell ismernie a beépített szabályokat tartalmaz. Ezek a következők:

-   **Egy adott virtuális hálózaton belüli összes forgalmat engedélyezi:** minden virtuális gép ugyanazon az Azure Virtual Network kommunikálhatnak egymással.

-   **Engedélyezi a bejövő Azure terheléselosztási:** Ez a szabály lehetővé teszi bármely célcím bármely forráscím-forgalom az Azure load balancer.

-   **Az összes bejövő megtagadása:** Ez a szabály blokkolja az összes forgalom az internetről, amely explicit módon engedélyezi hogy forrás.

-   **Az összes forgalom az internethez kimenő forgalom engedélyezése:** Ez a szabály lehetővé teszi, hogy a virtuális gépek Internet kapcsolatokhoz. Ha nem szeretné, hogy ezek a kapcsolatok kezdeményezett, hozzon létre egy szabályt, amely ezeket a kapcsolatokat letiltása vagy kényszerítése a kényszerített bújtatás szeretné.

#### <a name="system-routes-and-user-defined-routes"></a>Rendszerútvonalak és a felhasználó által definiált útvonalak

Virtuális gépek (VM) ad hozzá egy virtuális hálózatot (VNet), az Azure-ban, ha azt észleli, hogy a virtuális gépek képesek kommunikálni egymással a hálózaton keresztül, automatikusan. Nem kell megadni átjárót, akkor sem, ha a virtuális gépek külön alhálózatokon vannak.

Ugyanez vonatkozik a virtuális gépek és a nyilvános internet közötti kommunikációra, és akár a helyszíni hálózatra is, ha jelen van egy hibrid kapcsolat az Azure és a saját adatközpont között.

![Rendszerútvonalak](media/azure-network-security/azure-network-security-fig-9.png)

A kommunikáció ilyen típusú áramlása azért lehetséges, mert az Azure rendszerútvonalak sorát használja az IP-cím adatforgalmának meghatározására. A rendszerútvonalak az alábbi helyzetekben irányítják a kommunikáció áramlását:

-   Ha ugyanazon az alhálózaton belül történik.

-   Két alhálózat között történik egy virtuális hálózaton belül.

-   Virtuális gépek és az internet között történik.

-   Két VNet között történik egy VPN-átjárón keresztül.

-   Egy vnet és egy másik virtuális hálózaton keresztül a Vnetben társviszony-létesítés ([szolgáltatás láncolás](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Egy VNet és a helyszíni hálózat között történik egy VPN-átjárón keresztül.

Sok vállalat rendelkezik szigorú biztonsági, és a helyszíni ellenőrzés az összes hálózati csomag kényszerítéséhez adott igénylő megfelelőségi követelmények házirendeket. Azure nevű mechanizmust biztosít [kényszerített bújtatás](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) , amely irányítja a forgalmat a virtuális gépek helyszíni egyéni útvonal létrehozása vagy által [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) hirdetmények expressroute-on vagy VPN használatával.

Az Azure-ban kényszerített bújtatás konfigurálása virtuális hálózati felhasználó által definiált útvonalak (UDR) keresztül. Egy helyszíni hely irányít át forgalmat az Azure VPN gatewayhez alapértelmezett útvonalat kifejezve.

A következő szakaszban azok az aktuális útválasztási táblázat és korlátozása útvonalak egy Azure virtuális hálózat:

-   Minden egyes virtuális hálózati alhálózat van egy beépített, rendszer-útválasztási táblázatához. A rendszer útválasztási táblázatban az útvonalak a következő három csoport rendelkezik:

 -  **Helyi VNet útvonalak:** közvetlenül és a cél virtuális gépek ugyanabban a virtuális hálózatban

 - **A helyi útvonalak:** számára az Azure VPN gateway

 -  **Alapértelmezett útvonal:** közvetlenül kell az internethez. A privát IP-címek nem fedi le az előző két útvonalakat a csomagok megszakadnak.

-   Felhasználó által definiált útvonalak kiadása létrehoz egy útválasztási táblázatot egy alapértelmezett útvonal hozzáadása, és majd társítsa a virtuális hálózat alhálózathoz ezek alhálózatok kényszerített bújtatás engedélyezése az útválasztási táblázatban.

-   Egy "alapértelmezett"nevű hely a létesítmények közötti helyi helyek között a virtuális hálózathoz be kell.

-   A kényszerített bújtatás kell rendelni egy Vnetet, amely egy dinamikus útválasztási VPN-átjáró (nem a statikus átjárók) rendelkezik.

- A kényszerített bújtatás ExpressRoute a mechanizmus révén nincs konfigurálva, de ehelyett szerint engedélyezve van egy alapértelmezett útvonalat hirdet a ExpressRoute BGP társviszony-létesítési munkameneteket keresztül.

> [!Note]
> További információkért lásd: a [ExpressRoute dokumentációja](https://azure.microsoft.com/documentation/services/expressroute/) további információt.

#### <a name="network-security-appliances"></a>Hálózati biztonsági készülékek
Amíg a hálózati biztonsági csoportok és felhasználói útvonalakat biztosít egy bizonyos szintű hálózati biztonság, a hálózati és a szállítási réteg a [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), létezhetnek helyzetek, ahol azt szeretné, vagy a hálózati vermet magasabb szintű biztonsági engedélyezni kell. Ilyen helyzetekben azt javasoljuk, hogy telepít virtuális hálózati biztonsági készülékek Azure partnerei által biztosított.

![Hálózati biztonsági készülékek](./media/azure-network-security/azure-network-security-fig-10.png)

Az Azure hálózati biztonsági készülékek VNet biztonság növelésével és hálózati funkciók, és számos gyártó keresztül elérhető a [Azure piactér](https://azuremarketplace.microsoft.com). Adja meg e virtuális biztonsági készülékek telepíthetők:

-   Magas rendelkezésre állású tűzfalak

-   Behatolás megelőzése

-   Behatolásérzékelési

-   Webalkalmazási tűzfalak (WAFs)

-   WAN-optimalizálást

-   Útválasztás

-   Terheléselosztás

-   VPN

-   Tanúsítványkezelés

-   Active Directory

-   Többtényezős hitelesítés

#### <a name="application-gateway"></a>Alkalmazásátjáró

[A Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) van egy dedikált virtuális készülék, amely az alkalmazás kézbesítési vezérlőhöz (LÉPETT) szolgáltatás.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Alkalmazásátjáró lehetővé teszi a webkiszolgáló farm teljesítmény és rendelkezésre állás optimalizálása kiürítésével a CPU intenzív SSL-lezárást az Alkalmazásátjáró (SSL-feladatkiszervezést). Emellett biztosítja az egyéb réteg 7 útválasztási képességeket telepít, például:

-   Ciklikus multiplexelés bejövő forgalom

-   A munkamenet cookie-alapú kapcsolat

-   URL-cím elérési út-alapú útválasztási

-   Csak egyetlen alkalmazás átjáró mögötti több webhely képes


A [webalkalmazási tűzfal (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) az Alkalmazásátjáró részeként is biztosítja. Ez biztosít védelmet webalkalmazások számára közös webes biztonsági rések és biztonsági réseket. Alkalmazásátjáró konfigurálhat Internet felé néző átjáró, a belső átjárót, vagy mindkettőt.

Alkalmazás átjáró WAF észlelési vagy megelőzési módban futtatható. Gyakori használati eset a rendszergazdák megfigyelni a kártevő minták forgalom észlelési módban való futásra van. Amennyiben lehetséges észlel, szolgáltatják megelőzésére módra blokkolja a gyanús bejövő forgalmat.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

Ezenkívül alkalmazás átjáró WAF segít figyelni a webalkalmazásokat integrálva van a valós idejű WAF-naplót használó támadások ellen [Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) és [az Azure Security Center](https://azure.microsoft.com/services/security-center/) nyomon követhető a WAF riasztásokat, és könnyedén figyelheti a trendeket.

A JSON-formázott napló közvetlenül az ügyfél tárfiókjával kerül. Ezek a naplók teljes hozzáféréssel rendelkeznek, és a saját megőrzési házirendeket is alkalmazhat.

Ezek a naplók is betöltési módja, ha a saját analytics rendszer [Azure napló integrációs](https://aka.ms/AzLog). WAF naplók integrálva vannak [Operations Management Suite (OMS)](https://www.microsoft.com/cloud-platform/operations-management-suite) , OMS naplóelemzési segítségével kifinomult részletes lekérdezéseket hajt végre.

#### <a name="azure-web-application-firewall-waf"></a>Az Azure webalkalmazási tűzfal (WAF)

Webalkalmazások egyre közös ismert biztonsági rések, például az SQL injektálási, helyközi parancsfájlok futtatására és más támadásoknak, amelyek megjelennek a biztonsági rések elleni támadásokat céljai a [OWASP első 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Szigorú karbantartási, javítását és az alkalmazás topológia rétege ellenőrzés megakadályozza az ilyen biztonsági réseket az alkalmazásban van szükség.

 ![Az Azure webalkalmazási tűzfal (WAF)](./media/azure-network-security/azure-network-security-fig-13.png)

Egy központi [webalkalmazási tűzfal (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) webes támadások ellen, és a biztonságkezelés leegyszerűsíti az alkalmazások módosítása nélkül.

Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

#### <a name="network-availability-controls"></a>Hálózati rendelkezésre állás vezérlők

A Microsoft Azure-ral többféleképpen lehet elosztani a hálózati forgalmat. Ezek a lehetőségek különböző működési elveken alapulnak, különböző funkciókészlettel rendelkeznek és különböző forgatókönyveket támogatnak. Elkülönítve vagy egymással kombinálva egyaránt használhatók.

Az alábbiakban a hálózati rendelkezésre állás vezérlők:

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Az Azure terheléselosztó**

Magas rendelkezésre állás és a hálózati teljesítményt nyújt az alkalmazások. A réteg 4 (TCP, UDP) terheléselosztó bejövő forgalmat egy elosztott terhelésű készlet definiált kifogástalan szolgáltatáspéldányok között ellátó.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Az Azure Load Balancer beállítható úgy, hogy:

-   Egyenleg bejövő internetes forgalmat a virtuális gépek betölteni. Ez a konfiguráció az úgynevezett [internetre terheléselosztási](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Betöltési oszthatja el a forgalmat egy virtuális hálózatban lévő virtuális gépek, virtuális gépek felhőszolgáltatások közötti vagy a helyszíni számítógépek és a létesítmények közötti virtuális hálózatban lévő virtuális gépek között. Ez a konfiguráció az úgynevezett [belső terheléselosztás](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Egy adott virtuális gép a külső forgalom továbbítására.

A felhőben található összes erőforrást egy nyilvános IP-címet az internetről elérhetőnek kell lennie kell. A felhőalapú infrastruktúra az Azure-ban nem elérhető IP-címet használ az erőforrások. Azure hálózati címfordítás (NAT) használ a kommunikálnak az interneten a nyilvános IP-címeket.

 **Alkalmazásátjáró**

 Alkalmazásátjáró működik. az alkalmazási rétegre (a OSI hálózati hivatkozás verem réteg 7). Fordított proxyszolgáltatásként működik, az ügyfélkapcsolatok leállítását és a kérelmek háttérvégpontokhoz való továbbítását végzi.

 **Forgalomkezelő**

A Microsoft Azure Traffic Manager szolgáltatás végpontok különböző adatközpontokban felhasználói forgalom terjesztését teszi lehetővé. Traffic Manager által támogatott szolgáltatás végpontok közé tartoznak az Azure virtuális gépeken, Web Apps, és a felhőalapú szolgáltatások. A Traffic Manager külső, nem Azure-végpontokkal együtt is használható.

A TRAFFIC Manager használ a tartománynévrendszer (DNS) át tudja irányítani a legmegfelelőbb végpontra irányuló kéréseket a [forgalom-útválasztási módszer](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) és a végpontok állapotát. A TRAFFIC Manager forgalom-útválasztási módszer különböző különböző alkalmazás igényeinek, végpont állapota megfelelő számos biztosít [figyelési](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring), és automatikus feladatátvételt. A TRAFFIC Manager esetén is lehetséges legyen, beleértve a teljes Azure-régiót hibája.

Az Azure Traffic Manager lehetővé teszi a forgalom eloszlása vezérelni az alkalmazási végpontokat. A végpont bármilyen internetre-szolgáltatott belül vagy kívül Azure.

A TRAFFIC Manager biztosít két fő előnnyel jár:

-   A forgalmat több valamelyikével [forgalom-útválasztási módszert](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Folyamatosan figyelje a végpont állapotfigyelő](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) és automatikus feladatátvétel, ha a végpontok sikertelen.

Amikor egy ügyfél próbál kapcsolódni a szolgáltatáshoz, a szolgáltatás DNS-nevét először azt kell tartoznia IP-címet. Az ügyfél ezután kapcsolódik az IP-címet, a szolgáltatás eléréséhez. A TRAFFIC Manager DNS-ügyfelek a forgalom-útválasztási módszer a szabályok adott Szolgáltatásvégpontok használja. Ügyfelek közvetlenül a kijelölt végponthoz kapcsolódni. A TRAFFIC Manager nincs olyan proxy vagy az átjáró. A TRAFFIC Manager nem látja az ügyfél és a szolgáltatás között áthaladó forgalmat.

### <a name="azure-network-validation"></a>Azure-hálózat ellenőrzése

Azure-hálózat ellenőrzése, hogy győződjön meg arról, hogy az az Azure-hálózatot módon van konfigurálva, és érvényesítési végezhető működik-e a szolgáltatások és a hálózat elérhető szolgáltatások segítségével. Azure hálózati figyelőt, végezheti el a naplózási formátum és diagnosztikai képességek, amelyekkel meg, amelyen a hálózati teljesítmény- és állapot megértése. Ezek a képességek a portálhoz, a PowerShell, a CLI, a Rest API-t és a SDK keresztül érhetők el.

Az Azure Operational biztonsági hivatkozik a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások védelmére az adatok, alkalmazások és egyéb eszközök a Microsoft Azure-ban. Az Azure Operational biztonsági egy keretrendszer, amely magában foglalja a tapasztalatok teszik ki egy egyedi, a Microsoftnak, beleértve a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft biztonsági válasz Center program és részletes tájékoztatást nyújthatnak a számítógépes biztonsági veszélyforrásainak tükrében megfigyelhető a különböző képességeket keresztül épül.

-   [Az Azure Operations Management Suite szolgáltatásban](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Az Azure hálózati figyelőt](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Az Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Az Azure erőforrás-kezelő

A személyek és folyamatok, a Microsoft Azure-t üzemeltető is lehet, hogy a legfontosabb, a platform biztonsági szolgáltatás. Ez a szakasz ismerteti a Microsoft globális adatközpont infrastruktúrájában javíthatja és karbantartása a biztonsági, folytonosságának és adatvédelmi funkciókat.

Az alkalmazás infrastruktúrája általában számos összetevőből áll – például egy virtuális gépből, tárfiókot, és virtuális hálózati vagy egy webalkalmazást, adatbázis, adatbázis-kiszolgáló és harmadik féltől származó szolgáltatással épül fel. Ezeket az összetevőket nem külön entitásokként látja, hanem egyetlen entitás kapcsolódó és egymással összefüggő részeiként. Csoportként érdemes telepíteni, kezelni és megfigyelni őket. Az Azure Resource Manager lehetővé teszi, hogy a megoldásában az erőforrásokkal egy csoportként dolgozzon.

A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. A telepítéshez egy sablon használatos, amely különböző, például tesztelési, átmeneti és üzemi környezetben is képes működni. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően.

**A Resource Manager használatának előnyei**

A Resource Manager számos előnyt kínál:

-   A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

-   A megoldást ismételten telepítheti a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

-   Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.

-   Meghatározhatja az erőforrások, hogy azok a megfelelő sorrendben legyenek telepítve közti függőségeket.

-   Hozzáférés-vezérlést alkalmazhat az összes szolgáltatásra az erőforráscsoportban, mivel a szerepköralapú hozzáférés-vezérlés (RBAC) natív módon integrálva van a felügyeleti platformba.

-   Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.

-   Tisztázhatja a szervezete egy címke megosztása erőforrások csoportja számára költségeinek megtekintésével.

> [!Note]
> A Resource Manager egy új módot kínál a megoldások telepítésére és kezelésére. Ha a korábbi telepítési modellt használta, és további információkat kíván megtudni a változásokról, tekintse meg [A Resource Manager telepítés és a hagyományos telepítés ismertetése](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) című cikket.

## <a name="azure-network-logging-and-monitoring"></a>Azure-hálózat naplózás és figyelés

Azure biztosít több segédprogramot is figyelni, megakadályozása, észlelésében és kezelésében a hálózati biztonsági eseményekre. Ez a terület érhető el a leghatékonyabb eszköze többek között:

-   Network Watcher

-   Hálózati erőforrás szolgáltatásiszint-figyelés

-   Log Analytics

### <a name="network-watcher"></a>Hálózati figyelőt

[Hálózati figyelő](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -forgatókönyv-alapú figyelési által biztosított szolgáltatások a hálózati figyelőt. A szolgáltatás része a csomagrögzítéssel, a következő ugrás, az IP-adatfolyam győződjön meg arról, biztonsági csoport megtekintése, NSG folyamata naplókat. Forgatókönyv szintű figyelési jeleníti meg egy végpontok közötti hálózati erőforrások ellentétben egyes hálózati erőforrás-figyelése.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Hálózati figyelőt olyan regionális szolgáltatás, amely lehetővé teszi, hogy figyelése és diagnosztizálása szintjén feltételek egy hálózati forgatókönyv, hogy, és az Azure-ból. Hálózati diagnosztika és a képi megjelenítés eszközök is elérhetők a hálózati figyelőt segítenek megérteni, diagnosztizálása és információt kaphat a hálózathoz, az Azure-ban.

Hálózati figyelőt jelenleg a következő képességekkel rendelkezik:

#### <a name="topology"></a>topológia

[Topológia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) adja vissza a hálózati erőforrások egy grafikonon egy virtuális hálózatot. Az ábra a végpontok közötti hálózati kapcsolatot képviselő erőforrások összekapcsolása ábrázolja. A portálon topológia adja vissza az erőforrás-objektumból az adott virtuális hálózati alapján. A kapcsolatok ezek ábrázolva kapcsolaton kívül a hálózati figyelőt régió erőforrások között, akkor is, ha az erőforrás a csoport nem jelenik meg. Az erőforrások, az eredmény abban a portál nézet a hálózati összetevők, amelyek grafikon részhalmazát jelentik. A hálózati erőforrások teljes listájának megtekintéséhez használja [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) vagy [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Erőforrásokat ad vissza, a kapcsolatot, akkor a két van modellezve.

- **A befoglaltsági** -virtuális hálózat tartalmaz egy alhálózatot, amely tartalmazza a hálózati adaptert.

- **Kapcsolódó** – A hálózati adapter kapcsolódik a virtuális gépek.

#### <a name="variable-packet-capture"></a>Változó csomagrögzítéssel

Hálózati figyelő [változó csomagrögzítéssel](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) lehetővé teszi a csomag rögzítési munkamenetek nyomon követéséhez forgalma és a virtuális gép létrehozásához. Csomag rögzítési segítségével mindkét hálózati rendellenességeket reaktív diagnosztizálásához és proactivity. Egyéb felhasználásra tartalmazzák a hálózati statisztikákat, hálózati behatolások, ügyfél-kiszolgáló közötti kommunikációt, és még sok más hibakeresési információkat való összegyűjtéséhez.

Csomagrögzítéssel a virtuálisgép-bővítmény hálózati figyelőt keresztül távolról elindult. Ez a funkció megkönnyíti a csomagrögzítéssel manuálisan futó a használni kívánt virtuális gépet, amely értékes időt takaríthat meg okozta terheket. A portál, a PowerShell, a parancssori felület vagy a REST API-n keresztül is elindítható a csomagrögzítéssel. Például hogyan csomagrögzítéssel is elindítható a virtuális gép a riasztások van.

#### <a name="ip-flow-verify"></a>IP-adatfolyam ellenőrzése

[IP-adatfolyamok ellenőrizze](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) ellenőrzi, ha egy csomag engedélyezett vagy megtagadott vagy a virtuális gép 5 rekordos adatok alapján. Ez az információ irányát, protokoll, helyi IP, távoli IP, helyi port és távoli port áll. Ha a csomagot a rendszer megtagadja egy biztonsági csoport, a szabály, amely megtagadja a csomag nevét adja vissza. Minden cél- és IP-választható ki, miközben a Ez a szolgáltatás segítségével a rendszergazdák gyorsan eseményadatokat kapcsolat a vagy az internethez és a vagy a helyszíni környezetben.

IP-adatfolyamok ellenőrizze a hálózati adaptert egy virtuális gép célozza. Forgalom majd ellenőrizve a konfigurált beállításokat, vagy arról, hogy a hálózati illesztő alapján. Ezzel a képességgel olyan hasznos, erősítse meg, ha egy szabály a hálózati biztonsági csoport blokkolja a belépési vagy kilépési bejövő és kimenő forgalmat a virtuális gép.

#### <a name="next-hop"></a>Következő ugrás

Meghatározza, hogy a [következő Ugrás](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) így lehetővé teszi bármely diagnosztizálhatja a csomagok irányítása a Azure hálózati hálóban, felhasználó által definiált útvonalak konfigurálva. VM forgalmat elküldi a hatékony társított hálózati útvonalak alapján célhelyét. Következő ugrás lekérése a következő ugrás típusa és a csomagok IP-cím egy adott virtuális gép és a hálózati adaptert. Ez segít meghatározni, ha a csomag van irányítja a cél, vagy éppen fekete forgalom furatos van.

Következő ugrás is a következő ugrás hozzárendelt útválasztási táblázatot ad vissza. Lekérdezésekor a következő ugrás, ha egy felhasználó által megadott útvonalat az útvonal van meghatározva, az adott útvonal eredmény. Egyéb esetben a következő ugrás "Rendszerútvonal" ad vissza.

#### <a name="security-group-view"></a>Biztonsági csoport megtekintése

Lekérdezi a hatékony és alkalmazott biztonsági szabályokat, amelyek érvényesek a virtuális gép. Hálózati biztonsági csoportok társított alhálózati szinten, vagy egy hálózati adapter szintjén. Hozzárendelt alhálózat szinten, ha az alhálózat összes Virtuálisgép-példányára vonatkozik. Hálózati [biztonsági csoport megtekintése](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) adja vissza az összes beállított NSG-ket és egy virtuális gépet, a konfiguráció betekintést biztosít a hálózati és alhálózati szinten társított szabályokat. Emellett a hatékony biztonsági szabályok adja vissza minden virtuális gép hálózati adapterei. Hálózati biztonsági csoport használatával nézet felmérheti a virtuális gépek, a hálózati biztonsági réseket, például a megnyitott portok. Ha a hálózati biztonsági csoport a várt módon működik alapján is ellenőrzéséhez egy [a beállított és a hatékony biztonsági szabályok összehasonlítása](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>NSG Flow naplózás

 Adatfolyam-naplókat a hálózati biztonsági csoportok lehetővé teszik a engedélyezett vagy megtagadott a csoport biztonsági szabályai forgalmi naplók rögzítése. A folyamat egy 5 rekordos információkat – a forrás IP-cím, a cél IP-cím, a forrásport, a célport és a protokoll határozzák meg.

[Hálózati biztonsági csoport folyamata naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) azon szolgáltatása, amely lehetővé teszi, hogy tekintse meg a hálózati biztonsági csoporton keresztül bemenő és kimenő IP-forgalom hálózati figyelőt.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Virtuális hálózati átjáró és a kapcsolat hibaelhárítása

Hálózati figyelőt sok képességeket biztosít, a hálózati erőforrások az Azure-ban ismertetése vonatkozik. Ezek a képességek egyik erőforrás hibaelhárítás. [Erőforrások hibaelhárítása](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) PowerShell, a parancssori felületen vagy a REST API hívása. Meghívásakor, a hálózati figyelőt megvizsgálja a virtuális hálózati átjáró vagy a kapcsolat állapotát, és visszaadja az eredményekről.

Ez a szakasz végigvezeti Önt a különböző felügyeleti feladatok, amelyek erőforrás hibaelhárítási aktuálisan elérhető.

-   [A virtuális hálózati átjáró hibaelhárítása](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Végezzen hibaelhárítást a kapcsolaton](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Hálózati előfizetési korlátozásait

[Előfizetési korlátozásait a hálózati](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) biztosít a hálózati erőforráshoz, a rendelkezésre álló erőforrások maximális számának elleni régióban előfizetés használatának részleteit.

#### <a name="configuring-diagnostics-log"></a>Diagnosztikai naplófájl konfigurálása

Hálózati figyelőt tartalmaz egy [diagnosztikai naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) nézet. Ez a nézet az összes hálózati erőforrások, amelyek támogatják a diagnosztikai naplózás tartalmazza. Ebben a nézetben engedélyezése, és tiltsa le a hálózati erőforrások egyszerűen és gyorsan.

### <a name="network-resource-level-monitoring"></a>Hálózati erőforrás szolgáltatásiszint-figyelés

A következő funkciók érhetők el erőforrás-szintű figyelése:

#### <a name="audit-log"></a>Napló

A hálózatok konfiguráció részeként műveleteket a rendszer naplózza. Ezek a naplók alapvető különböző megfelelőségi létrehozásához. Ezek a naplók megtekinthetők az Azure portálon, vagy visszavonni a Microsoft eszközök, például a Power BI és a külső eszközök használatával. Naplókat a portálon, a PowerShell, a CLI és a Rest API-n keresztül érhetők el.

> [!Note]
> A naplókat további információkért lásd: [naplózási műveletek a Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Naplók az összes hálózati erőforrás végzett műveletek érhetők el.


#### <a name="metrics"></a>Mérőszámok

Adatok gyűjtése le TELJESÍTMÉNYMÉRÉSEK és egy meghatározott időtartam során gyűjtött adatait. Adatok gyűjtése le jelenleg elérhető az Alkalmazásátjáró. Mérőszámok segítségével aktiváltak riasztásokat küszöbérték alapján. Azure Application Gateway alapértelmezés szerint a háttér-készlet összes erőforrások állapotát figyeli, és automatikusan eltávolítja az összes erőforrást, a készlet megfelelő állapotúnak számít. Alkalmazásátjáró továbbra is figyeli a nem megfelelő példányok, és hozzáadja őket a megfelelő háttér-készlet számára, amennyiben az rendelkezésre állására, és állapotteljesítmény válaszolni. Alkalmazásátjáró küldi az állapot-mintavételi csomagjai a meghatározott a háttér-HTTP-beállítások ugyanazt a portot. Ez a konfiguráció biztosítja, hogy a mintavétel ellenőrzi, hogy az ügyfelek akkor használja a háttérrendszerhez való csatlakozáshoz ugyanazt a portot.

> [!Note]
> Lásd: [átjáró Alkalmazásdiagnosztika](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) megtekintéséhez, hogyan metrikák értesítések létrehozására használható.

#### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Rendszeres és önkéntes események hálózati erőforrások által létrehozott, és a storage-fiókok, egy Eseményközpontba vagy Naplóelemzési küldött bejelentkezve. Ezek a naplók erőforrás állapotának betekintést. Ezek a naplók eszközöket, például a Power BI és a Naplóelemzési tekintheti meg. Diagnosztikai naplók megtekintése megismeréséhez látogasson el [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Diagnosztikai naplók érhetők el [terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), útvonalakat, és [Alkalmazásátjáró](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Hálózati figyelőt biztosít a diagnosztikai naplók megtekintése. Ez a nézet az összes hálózati erőforrások, amelyek támogatják a diagnosztikai naplózás tartalmazza. Ebben a nézetben engedélyezése, és tiltsa le a hálózati erőforrások egyszerűen és gyorsan.

### <a name="log-analytics"></a>Log Analytics

[Naplófájl Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) szolgáltatás a [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) , amely figyeli a felhőben és a helyszíni környezetek karbantartásához azok rendelkezésre állását és teljesítményét. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson.

Naplóelemzési a hálózatok figyeléséhez a következő megoldásokat nyújtja:

-   Hálózati teljesítmény figyelése (NPM)

-   Az Azure Application Gateway elemzés

-   Azure hálózati biztonsági csoport elemzés

#### <a name="network-performance-monitor-npm"></a>Hálózati teljesítmény figyelése (NPM)
A [hálózati Teljesítményfigyelő](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) felügyeleti megoldás egy olyan hálózati felügyeleti megoldás, amely az állapot, a rendelkezésre állás és a hálózatok reachability figyeli.

Közötti kapcsolat figyelésére szolgál:

-   Nyilvános felhő és a helyszíni

-   Adatközpontok és a felhasználó helye (fiókirodákban)

-   Egy többrétegű alkalmazást a különböző rétegeket tartalmazó alhálózat.


#### <a name="azure-application-gateway-analytics-in-log-analytics"></a>Az Azure alkalmazás átjáró elemzés a naplóelemzési

A következő naplók kapcsolódnak Alkalmazásátjárót támogatja:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

A következő mérőszámokat Alkalmazásátjárót támogatja:

-   5 perces átviteli sebesség

#### <a name="azure-network-security-group-analytics-in-log-analytics"></a>Az Azure hálózati biztonsági csoport elemzés a naplóelemzési

A következő naplók kapcsolódnak támogatottak [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** mely NSG-t a szabályok vonatkoznak a virtuális gépek és -példányt szerepkörök a MAC-címe alapján bejegyzést tartalmaz. Ezek a szabályok állapota 60 másodpercenként gyűjti.

- **NetworkSecurityGroupRuleCounter:** Contains bejegyzéseket hányszor minden NSG-szabályok forgalom engedélyezése vagy megtagadása vonatkozik.

## <a name="next-steps"></a>Következő lépések
Többet szeretne tudni biztonsági ehhez beolvassa a részletes biztonsági témakörök:

-   [Naplóelemzési a hálózati biztonsági csoportokkal (NSG-k)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [A felhő megszűnésének meghajtó innovációk hálózat](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: A hálózati kapcsoló szoftvert, hogy a Microsoft globális felhő powers](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Hogyan hoz létre a Microsoft a gyors és megbízható globális hálózati](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Megvilágítási hálózati innováció mentése](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
