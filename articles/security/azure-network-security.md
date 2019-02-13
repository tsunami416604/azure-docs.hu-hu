---
title: Azure hálózati biztonság |} A Microsoft Docs
description: Ismerje meg a felhőalapú számítástechnikai szolgáltatások széles választékával számítási példányokat tartalmazó & szolgáltatások, amelyek méretezhető felfelé és lefelé automatikusan az alkalmazás vagy a vállalat igényeinek.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: a405583503b75a64dda2bf277a4a50be4e926d28
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111288"
---
# <a name="azure-network-security"></a>Azure hálózati biztonság

Tudjuk, hogy biztonsági-e a feladatot a felhőben, és hogy mennyire fontos, hogy látja-e az Azure security pontos és időben információt. A legjobb okai használhatja az Azure-alkalmazásokat és szolgáltatásokat, az biztonsági eszközök és funkciók széles választékának az Azure előnyeit. A következő eszközök és képességek segítségével biztonságos megoldásokat hozhat létre az Azure platform lehetővé teszi.

Microsoft Azure lehetőséget kínál a bizalmas, integritás és rendelkezésre állását a vásárlói adatokat, átlátható accountability is engedélyezése közben. A hálózati biztonsági vezérlők az ügyfél szempontjából a Microsoft Azure-ban megvalósított gyűjteménye jobb megértése érdekében ez a cikk "Az Azure hálózati biztonság", írt szabályozási lehetőségeket biztosítanak az átfogó tekintse meg a hálózati biztonság elérhető a Microsoft Azure-ral.

A célja, hogy azonosítható, hogy számos hálózati vezérlők, amelyek úgy is konfigurálhatók, üzembe helyezése az Azure-megoldások biztonságának növelése. Ha érdekli a Microsoft használ a hálózati háló az Azure platform magát biztonságos, az Azure security a című a [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure).

## <a name="azure-platform"></a>Azure-platform

Az Azure egy nyilvános felhőszolgáltatás-platform, amely támogatja az operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök széles skálájából el.  Azt is Linux-tárolókat futtathat Docker-integrációval; a JavaScript, Python, .NET, PHP, Java és Node.js-alkalmazások készítéséhez háttérrendszereket készíthet iOS, Android és Windows eszközökhöz. Az Azure cloud services támogatja a technológiákat a fejlesztők és informatikai szakemberek számára már elnyerték.

Amikor épülnek, vagy át az IT-eszközeivel, nyilvános felhőszolgáltató, hagyatkoznia a annak a szervezetnek képességek az alkalmazások és a szolgáltatások és a vezérlőket, kezelheti a felhőalapú eszközeinek biztonságát biztosítanak az adatok védelme érdekében.

Az Azure infrastruktúráját úgy alakítottuk kiszolgáló alkalmazásokat üzemeltetni a képes legyen ügyfelek millióit egyidejűleg, és olyan megbízható alapot, amely megfelel a vállalatok a biztonsági követelményeket biztosít. Emellett az Azure biztosít számos konfigurálható biztonsági beállítások és irányítása őket, hogy a szervezet üzemelő példányok egyedi követelményeinek megfelelő biztonsági testre szabhatja.

## <a name="abstract"></a>Absztrakt

A Microsoft nyilvános felhőszolgáltatások biztosít, rendkívül nagy kapacitású szolgáltatások és az infrastruktúra, a vállalati szintű képességet és a számos választási való hibrid kapcsolathoz. Választhat, vagy az interneten keresztül, vagy az Azure expressroute használatával privát hálózati kapcsolatot biztosít a szolgáltatásokhoz való hozzáférést. A Microsoft Azure platform lehetővé teszi, hogy zökkenőmentesen az infrastruktúrát kiterjesztheti a felhőbe, és több rétegből álló architektúrákat hozhat létre. Emellett harmadik felek engedélyezéséhez fejlett funkciókat kínál a biztonsági szolgáltatások és virtuális berendezések.

Az Azure hálózati szolgáltatások elvárt rugalmasságot, rendelkezésre állás, rugalmasság, biztonságának és integritásának maximalizálása érdekében. Ez a tanulmány az Azure és az adatokat az ügyfelek használatát az Azure natív biztonsági funkciók információs védelme érdekében a hálózati funkciók részletesen.

Ez a tanulmány a kívánt célközönség a következők:

- Technikai kezelők, a hálózati rendszergazdák és fejlesztők számára, akik a biztonsági megoldások elérhetők és támogatottak az Azure-ban.

-   Kis-és középvállalkozások vagy üzleti folyamat vezetők, akik szeretnék elérni egy összefoglaló jellegű áttekintést nyújt az Azure hálózati technológiákat és szolgáltatásokat, amely körül a hálózati biztonság az Azure nyilvános felhő vitafórumokban.

## <a name="azure-networking-big-picture"></a>Azure networking átfogó kép
A Microsoft Azure egy robusztus hálózati infrastruktúra támogatja az alkalmazás és szolgáltatás hálózati kapcsolati követelményeinek tartalmaz. Hálózati kapcsolat a helyszíni között az Azure-ban található erőforrások között lehetőség, és az Azure-ban üzemeltetett erőforrásokhoz, és hogy, és az internetről és az Azure.

![Azure Networking átfogó kép](media/azure-network-security/azure-network-security-fig-1.png)

A [Azure hálózati infrastruktúra](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) lehetővé teszi, hogy biztonságosan csatlakoztathatja az Azure-erőforrások egymáshoz a virtuális hálózatok (Vnetek). Virtuális hálózat saját felhőbeli hálózatának egy reprezentációja. Virtuális hálózat egy logikai elkülönítése az Ön előfizetéséhez fenntartott Azure-felhő hálózat. Virtuális hálózatok csatlakozhat a helyszíni hálózatok.

Az Azure támogatja a dedikált WAN-kapcsolaton kapcsolat a helyszíni hálózat és az Azure Virtual Networkhöz [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Az Azure és a hely közötti kapcsolat, amely nem a nyilvános interneten keresztül halad dedikált kapcsolatot használ. Ha az Azure-alkalmazásokat több adatközpontban fut, akkor használhatja [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) kérelmeket a felhasználóktól nyelvelemző, mind az alkalmazás-példányok között. Nem fut az Azure-ban, ha az internetről elérhető szolgáltatások is irányíthatja a forgalmat.

## <a name="enterprise-view-of-azure-networking-components"></a>Vállalati megtekintése az Azure hálózati összetevők
Az Azure számos olyan hálózati összetevők, amely a hálózati biztonsági hozzászólások rendelkezik. azt írja le ezeket a hálózati összetevők és a hozzájuk kapcsolódó biztonsági problémák összpontosíthat.

> [!Note]
> Az Azure-hálózatok nem minden aspektusát leírása – csak azokat a tervezési és a szolgáltatások és alkalmazások telepítése az Azure-ban egy biztonságos hálózati infrastruktúra megtervezése pivotal tekinthető szó.

Ebből a cikkből lesz lefedik az alábbi Azure hálózatkezelés nagyvállalati funkciókat:

-   Alapvető hálózati kapcsolat

-   Hibrid kapcsolat

-   Biztonsági vezérlők

-   A hálózat érvényességének ellenőrzése

### <a name="basic-network-connectivity"></a>Alapvető hálózati kapcsolat

A [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) szolgáltatás lehetővé teszi, hogy biztonságosan csatlakoztathatja az Azure-erőforrások egymáshoz a virtuális hálózatok (VNet). Virtuális hálózat saját felhőbeli hálózatának egy reprezentációja. Virtuális hálózat egy logikai elkülönítés az Azure-beli hálózati infrastruktúra dedikált az előfizetéshez. Is csatlakozhat virtuális hálózatok egymáshoz, és a helyszíni hálózatokhoz való kapcsolódásának site-to-site VPN-eket, és a dedikált [WAN-kapcsolatok](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Alapvető hálózati kapcsolat](media/azure-network-security/azure-network-security-fig-2.png)

A feltétellel, hogy a kiszolgálók az Azure-beli virtuális gépek használjon, a kérdés hogyan ezeket a virtuális gépeket csatlakoztatni a hálózathoz. A válasz az, hogy virtuális gépek csatlakozni egy [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

A virtuális hálózatok Azure virtuális hálózatok hasonlítanak a helyszíni használata a saját virtualizálási platform megoldások, például a Microsoft Hyper-V vagy VMware-alapú.

#### <a name="intra-vnet-connectivity"></a>Belüli virtuális hálózatok közötti kapcsolat

Kapcsolódás virtuális hálózatok egymáshoz, erőforrások kommunikálhassanak egymással virtuális hálózatokat vagy a vneten engedélyezése. Használhatja az alábbi beállítások egyikében vagy egymáshoz virtuális hálózatok csatlakoztatása:

- **Társviszony-létesítés:** Lehetővé teszi az erőforrások különböző Azure vnetekhez csatlakoztatni az azonos Azure-helyen belül kommunikálni egymással. A sávszélességet és a virtuális hálózatok közötti késés megegyezik, mintha az erőforrásokat ugyanabban a Vnetben csatlakoztatva. Társviszony-létesítés kapcsolatos további információkért olvassa el [virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

 ![Társviszony-létesítés](media/azure-network-security/azure-network-security-fig-3.png)

- **VNet – VNet kapcsolat:** Lehetővé teszi az erőforrások különböző Azure a vneten belül az azonos vagy eltérő Azure-helyen. Társviszony-létesítéshez, ellentétben a sávszélesség azért virtuális hálózatok közötti adatforgalom kell egy Azure VPN Gatewayen keresztül.

![A virtuális hálózatok közötti kapcsolat](media/azure-network-security/azure-network-security-fig-4.png)


Virtuális hálózatok csatlakoztatása a virtuális hálózatok közötti kapcsolattal kapcsolatos további információkért olvassa el a [konfigurálja a VNet – VNet kapcsolat cikk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Az Azure virtual network képességek:

Ahogy láthatjuk, az Azure Virtual Network biztosít a virtuális gépek képesek csatlakozni más hálózati erőforrások biztonságos módon, hogy csatlakozhassanak a hálózathoz. Azonban a hálózati kapcsolat nem csak a kezdet. Az Azure Virtual Network szolgáltatás a következő képességeket teszi közzé az Azure Virtual Network security jellemzői:

-   Elkülönítés

-   Internetkapcsolat

-   Azure-erőforrás-kapcsolat

-   Virtuális hálózatok közötti kapcsolat

-   Helyszíni kapcsolatok

-   Forgalomszűrés

-   Útválasztás

**Elkülönítés**

Virtuális hálózat [elkülönített](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) egymástól. Létrehozhat önálló virtuális hálózatok a fejlesztési, tesztelési és éles ugyanaz, mint [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) blokkok cím. Ezzel szemben, amely eltérő a CIDR címblokkokat használja, és kapcsolhatja össze a hálózatok virtuális hálózatokat hozhat létre. Több alhálózatra is oszthatja a virtuális hálózathoz.

Az Azure belső névfeloldást biztosít a virtuális gépek és [Cloud Services](https://azure.microsoft.com/services/cloud-services/) szerepkörpéldányok csatlakozik virtuális hálózathoz. Igény szerint konfigurálható egy Vnetet az Azure belső névfeloldást használata helyett a saját DNS-kiszolgálók használatához.

Minden egyes Azure-on belüli virtuális hálózatokat valósíthat meg [előfizetés](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) és az Azure [régió](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json). Minden egyes virtuális hálózat el különítve a többi virtuális hálózatok. Az egyes virtuális hálózatok a következőket teheti:

-   Megadhat egy egyéni magánhálózati IP-címteret nyilvános és magánhálózati (RFC 1918) címek használatával. Hozzárendelése az Azure-erőforrások a Vnethez csatlakozó magánhálózati IP-cím, a címtér, rendeli.

-   A virtuális hálózat szegmentálása egy vagy több alhálózatra, és foglaljon le egy minden egyes alhálózathoz a virtuális hálózat címtere része.

-   Használja az Azure által biztosított névfeloldást, vagy adja meg a saját DNS-kiszolgáló használatra egy virtuális hálózathoz csatlakozó erőforrás. Virtuális hálózatok a névfeloldással kapcsolatos további tudnivalókért olvassa el a [névfeloldás virtuális gépek és Felhőszolgáltatások](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Internetkapcsolat**

Az összes [Azure Virtual Machines (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) és a egy virtuális hálózathoz csatlakozó Felhőszolgáltatásiszerepkör-példányokat alapértelmezés szerint Internet-hozzáféréssel rendelkezik. Bejövő hozzáférést bizonyos erőforrásokhoz, igény szerint is engedélyezheti. (VM) és a egy virtuális hálózathoz csatlakozó Felhőszolgáltatásiszerepkör-példányokat alapértelmezés szerint Internet-hozzáféréssel rendelkezik. Bejövő hozzáférést bizonyos erőforrásokhoz, igény szerint is engedélyezheti.

Egy virtuális hálózathoz csatlakozó összes erőforrás alapértelmezés szerint az internetre irányuló kimenő kapcsolattal rendelkezik. A magánhálózati IP-cím az erőforrás az adatforrás hálózati cím lefordított (SNAT) által az Azure-infrastruktúra nyilvános IP-címre. Egyéni Útválasztás és a forgalom szűrése az életbe léptetésével módosíthatja az alapértelmezett kapcsolat. Kimenő internetkapcsolattal kapcsolatos további információkért olvassa el a [az Azure kimenő kapcsolatainak ismertetése](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Bejövő kommunikációt az Azure-erőforrásokhoz az internetről, vagy kimenő kommunikációját az SNAT az interneten, kell egy erőforráshoz hozzárendelt nyilvános IP-cím. Nyilvános IP-címek kapcsolatos további információkért olvassa el a [nyilvános IP-címek](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Azure-erőforrás-kapcsolat**

[Azure-erőforrások](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) , mint például a Cloud Services és a virtuális gépek is csatlakoztathatók, ugyanazon a vneten. Az erőforrások is csatlakozni egymáshoz magánhálózati IP-címeket, még akkor is, ha külön alhálózatokon vannak. Az Azure biztosítja az alapértelmezett alhálózatokat, virtuális hálózatok és a helyszíni hálózatok közötti útválasztást, így konfigurálhatja és kezelheti az útvonalak nem kell.

Több Azure-erőforrások virtuális hálózathoz, például a virtuális gépek (VM), a Cloud Services, az App Service Environment-környezetek és a Virtual Machine Scale Sets csatlakozhatnak. Virtuális gépek csatlakozni egy alhálózatot egy virtuális hálózaton keresztül egy hálózati adapter (NIC). Hálózati adapterek kapcsolatos további információkért olvassa el a [hálózati adapterek](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Virtuális hálózatok közötti kapcsolat**

[Virtuális hálózatok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) csatlakozhat egymással kommunikálni bármely erőforráshoz, a másik virtuális hálózat bármely virtuális hálózathoz csatlakozó erőforrás engedélyezése.

Kapcsolódás virtuális hálózatok egymáshoz, erőforrások kommunikálhassanak egymással virtuális hálózatokat vagy a vneten engedélyezése. Használhatja az alábbi beállítások egyikében vagy egymáshoz virtuális hálózatok csatlakoztatása:

- **Társviszony-létesítés:** Lehetővé teszi az erőforrások különböző Azure vnetekhez csatlakoztatni az azonos Azure-helyen belül kommunikálni egymással. A sávszélesség és a virtuális hálózatok közötti késés ugyanaz, mintha az erőforrásokat az azonos VNet.To csatlakoztatott tudjon meg többet a társviszony-létesítés, olvassa el a [virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **VNet – VNet kapcsolat:** Lehetővé teszi az erőforrások különböző Azure a vneten belül az azonos vagy eltérő Azure-helyen. Társviszony-létesítéshez, ellentétben a sávszélesség azért virtuális hálózatok közötti adatforgalom kell egy Azure VPN Gatewayen keresztül. További információ a virtuális hálózatok csatlakoztatása a VNet – VNet kapcsolattal. További tudnivalókért olvassa el a [VNet – VNet kapcsolat konfigurálása](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Helyszíni kapcsolatok**

Lehet csatlakoztatni virtuális hálózatok [helyszíni](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) hálózatok, hálózati és az Azure között magánhálózati kapcsolatokat, vagy az interneten keresztül site-to-site VPN-kapcsolaton keresztül.

A helyszíni hálózat csatlakozhat egy Vnethez a következő lehetőségek közül:

- **Pont – hely virtuális magánhálózati (VPN):** Egyetlen számítógép csatlakozik a hálózathoz és a VNet között létesített kapcsolat. Ez a kapcsolattípus remek választás, ha csak most ismerkedik az Azure szolgáltatással, illetve a fejlesztők számára, mert a meglévő hálózatot csak kis mértékben vagy egyáltalán nem kell módosítani. A kapcsolat az SSTP protokollt használja, titkosított kommunikációt biztosít a számítógép és a virtuális hálózat között az interneten keresztül. A késés, a pont – hely VPN-hez nem határozható meg előre, mivel az a forgalom az interneten halad át.

- **Helyek közötti VPN:** A VPN-eszköz és a egy Azure VPN Gateway átjáró között létesített kapcsolat. Ez a kapcsolattípus lehetővé teszi, hogy bármilyen helyszíni erőforráshoz is engedélyezni szeretné a VNet eléréséhez. A kapcsolat egy IPsec/IKE VPN, amely titkosított kommunikációt biztosít a helyszíni eszközök és az Azure VPN gateway között az interneten. A késés egy helyek közötti kapcsolat nem határozható meg előre, mivel az a forgalom az interneten halad át.

- **Azure ExpressRoute:** Akkor jön létre a hálózat és az Azure között egy ExpressRoute-partneren keresztül. Ez a kapcsolat nem nyilvános. Forgalom sem halad át az interneten. A késés, az ExpressRoute-kapcsolatok az már előre jelezhető, mert a forgalom nem haladnak át az interneten. Az előző kapcsolat lehetőségekkel kapcsolatos további tudnivalókért olvassa el a [kapcsolati topológia-diagramok](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Forgalomszűrés**

Virtuális gép és a Felhőszolgáltatások szerepkörpéldányok [hálózati forgalom](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) szűrni lehet a bejövő és kimenő forrás IP-cím és port, cél IP-cím és port és protokoll.

A hálózati forgalom alhálózatok közötti szűrése az alábbi lehetőségek egyikével vagy akár mindkettővel elvégezhető:

- **Hálózati biztonsági csoportok (NSG):** Minden NSG tartalmazhat több bejövő és kimenő biztonsági szabály lehetővé teszi, hogy a forrás és cél IP-cím, port és protokoll szerint forgalom szűrésére. Az NSG-KET a virtuális gép minden hálózati adapterhez is alkalmazhat. Az NSG-KET is alkalmazhat az alhálózat egy hálózati Adaptert, vagy más Azure-erőforrás csatlakoztatva van. NSG-kkel kapcsolatos további információkért olvassa el a [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Virtuális hálózati berendezések:** Egy hálózati virtuális berendezés, amely hálózati funkciót, például tűzfal-szoftvereket futtató virtuális gép. Az Azure piactéren elérhető nva-k listájának megtekintéséhez. Az nva-k is elérhetők, amely a WAN-optimalizálás és az egyéb hálózati forgalom funkciókat biztosítanak. Jellemzően nva-k a felhasználó által megadott vagy BGP-útvonalak. Is használhatja az NVA virtuális hálózatok közötti forgalom szűrésére.

**Útválasztás**

Az Azure alapértelmezett útválasztás konfigurálása a saját útvonalakat, vagy egy hálózati átjárón keresztül a BGP-útvonalak segítségével igény szerint felül lehet bírálni.

Az Azure létrehozza az útválasztási táblázatokat, amelyek lehetővé teszik az erőforrások bármely kommunikálnak egymással, alapértelmezés szerint a virtuális hálózat egyetlen alhálózatának sem kapcsolódik. Az Azure által létrehozott alapértelmezett útvonalak felülírásához valósítsa meg az alábbi lehetőségek egyikét, vagy akár mindkettőt:

- **Felhasználó által megadott útvonalakat:** Létrehozhat egyéni útválasztási táblázatokat az útvonalakat a vezérlőelemre, hova érkezzen a forgalom az egyes alhálózatokon. Felhasználó által megadott útvonalakkal kapcsolatos további információkért olvassa el a [felhasználó által megadott útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **BGP-útvonalakat:** Ha a helyszíni hálózathoz egy VPN-átjáró vagy ExpressRoute-kapcsolat használatával csatlakoztatja a virtuális hálózat, a virtuális hálózatok propagálható BGP-útvonalak.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Hibrid internetkapcsolat: A helyszíni hálózat csatlakoztatása
A helyszíni hálózat csatlakozhat egy Vnethez a következő lehetőségek közül:

-   Internetkapcsolat

-   Pont – hely VPN (P2S VPN)

-   Site-to-Site VPN (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Internetkapcsolat

Ahogy a neve is sugallja, internetkapcsolat teszi a számítási feladatok az interneten, a számítási feladatok, amelyek a virtuális hálózaton belüli élő eltérő nyilvános végpontokat tesznek közzé kellene. Ilyen számítási feladat használatával kerülhetnek [internetkapcsolattal rendelkező Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) vagy egyszerűen csak egy nyilvános IP-cím hozzárendelése a virtuális gép. Így lehetségessé válik az interneten keresztül képes elérni a virtuális gép által biztosított a gazdagép tűzfal összes adat [hálózati biztonsági csoportok (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg), és [felhasználó által megadott útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) lehetővé teszi, hogy a a legtöbb esetben.

Ebben a forgatókönyvben egy alkalmazás, amelyet nem az interneten nyilvános és tud csatlakozni a bárhol, vagy a számítási feladatok konfigurációjától függően meghatározott helyeiről származó teheti ki.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Pont – hely VPN- vagy Site-to-Site VPN
Ez a két kategóriába esik. Mindkét szükségük van a virtuális hálózathoz, hogy a VPN-átjáró, és csatlakozhat a vagy a VPN-ügyfél a munkaállomás részeként a [pont – hely konfiguráció](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) vagy konfigurálhatja a helyszíni [VPN-eszköz](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)kell tudni leállítani a site-to-site VPN. Ezzel a módszerrel a virtuális hálózaton belüli erőforrások a helyszíni eszközök csatlakozhatnak.

A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat egy SSTP (Secure Socket Tunneling Protocol) használatával működő VPN-kapcsolat.

![Pont – hely VPN](media/azure-network-security/azure-network-security-fig-5.png)

Pont – hely kapcsolatok akkor hasznosak, ha a virtuális hálózathoz való csatlakozáshoz távoli helyről, például otthonról vagy egy konferenciáról center szeretne, vagy akkor, ha csak néhány ügyfelet kíván csatlakoztatni egy virtuális hálózathoz.

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. A VPN-kapcsolatot az ügyfélszámítógépről hozhatja létre. P2S, ezért nem ajánlott abban az esetben az Azure-hálózatok számos a helyszíni eszközök és számítógépek állandó kapcsolatot kell az Azure-hoz csatlakozhat.

![Site-to-Site VPN](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Pont – hely kapcsolatok kapcsolatos további információkért lásd: a [pont – hely be v Q](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül.

Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. Ezt a kapcsolatot az interneten keresztül történik, és lehetővé teszi, hogy "csatorna" információ belül egy titkosított kapcsolatot a hálózati és az Azure között. Site-to-site VPN egy olyan biztonságos, érett technológia, évtizedes a vállalatok által központilag telepített. Alagút titkosítás használatával történik [IPsec-alagút módú](https://technet.microsoft.com/library/cc786385.aspx).

Egy megbízható, megbízható és meglévő technológiai pedig a site-to-site VPN a forgalmat az alagút belül haladnak át az interneten. Emellett a sávszélesség viszonylag korlátozza egy legfeljebb körülbelül 200 MB/s.

Ha a kivételes szintű biztonság és a teljesítmény a létesítmények közötti kapcsolatok szükségesek, javasoljuk, Azure ExpressRoute használata a létesítmények közötti kapcsolatok. Az ExpressRoute dedikált WAN a helyszíni hely és a egy Exchange-szolgáltató közötti kapcsolat. Mivel ez egy telekommunikációs kapcsolatot, az adatok nem az interneten keresztül továbbítani, és ezért nem érhető el az internetes kommunikáció a járó esetleges kockázatokat.

> [!Note]
> VPN-átjárókkal kapcsolatos további információkért lásd: [VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Dedikált WAN-kapcsolaton
A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait az Azure-ba, amelyet egy kapcsolatszolgáltató biztosít egy dedikált privát kapcsolaton keresztül.

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.

![ Dedikált WAN-kapcsolaton](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> A hálózat csatlakoztatása ExpressRoute-tal Microsoft információkért lásd: [ExpressRoute kapcsolati modellek](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) és [ExpressRoute technikai áttekintése](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Mint a helyek közötti VPN-beállításai az ExpressRoute is lehetővé teszi, amelyek nem feltétlenül csak egy virtuális hálózaton található erőforrások eléréséhez. Sőt attól függően, a Termékváltozat csatlakozhat 10 virtuális hálózatok. Ha rendelkezik a [prémium bővítmény](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), legfeljebb 100 virtuális hálózatok kapcsolatokat is lehetségesek, attól függően, a sávszélesség. Az ilyen típusú kapcsolatok tekintse meg hasonló kapcsolatos további információkért olvassa el [kapcsolati topológia-diagramok](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Biztonsági vezérlők
Az Azure Virtual Network biztosít egy biztonságos, logikai hálózat el különítve a többi virtuális hálózattól, amely támogatja az számos biztonsági intézkedés a helyszíni hálózatokon használt. Ügyfelek létrehozása a saját szerkezet használatával: alhálózatok – azok a saját privát IP-címtartományt használjon, konfigurálása útválasztási táblázatokat, hálózati biztonsági csoportok, hozzáférés-vezérlési listák (ACL), az átjárók és a számítási feladatok futtatásához a felhőben virtuális készülékek.

Használhatja az Azure virtuális hálózatok biztonsági vezérlők a következők:

-   Hálózati hozzáférés-vezérléssel

-   Felhasználó által megadott útvonalak

-   Hálózati biztonsági berendezéssel

-   Application Gateway

-   Az Azure webalkalmazás-tűzfal

-   Hálózati rendelkezésre állási szabályozása

#### <a name="network-access-controls"></a>Hálózati hozzáférés-vezérléssel
Az Azure Virtual Network (VNet) az Azure hálózati modell alappillére és elkülönítési és védelmet biztosít a [hálózati biztonsági csoportok (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) kényszerítése, és szabályozhatja a hálózati forgalomra vonatkozó szabályokat, a legfontosabb eszközei a hálózati szintű.

![ Hálózati hozzáférés-vezérléssel](media/azure-network-security/azure-network-security-fig-8.png)


Hozzáférést engedélyező vagy tagadja meg a kommunikációt egy virtuális hálózatban, az ügyfél-hálózatokon keresztül létesítmények közötti kapcsolatok, a rendszer a számítási feladatok között, vagy közvetlen Internet-kommunikációt.

Az ábrán virtuális hálózatok és az NSG-ket is találhatók, az Azure átfogó biztonsági verem, ahol az NSG-k, az udr-t és a hálózati virtuális berendezések segítségével az alkalmazások központi telepítésének a védett hálózat védelme érdekében biztonsági határokat hozzon létre egy adott réteg.

NSG-ket egy 5-ször több segítségével kiértékelheti az adatforgalom (és a szabályokat az NSG-hez konfigurált használatban vannak):

-   [Forrás és cél IP-cím](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Forrás és cél-port](https://technet.microsoft.com/library/dd197515)

-   Protokoll: [Transmission Control Protocol (TCP)](https://technet.microsoft.com/library/cc940037.aspx) vagy [User Datagram Protocol (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Ez azt jelenti, hogy szabályozhatja a hozzáférést, egyetlen virtuális gép és a egy csoportot a virtuális gépeket, és a egy másik egyetlen virtuális gép egy virtuális Gépen, vagy teljes alhálózatok között. Újra vegye figyelembe, hogy ez a szűrés, egyszerű állapot-nyilvántartó csomag nem teljes csomagvizsgálatot. Nincs protokollok ellenőrzése vagy hálózati szintek Azonosítói vagy IP-CÍMEK a hálózati biztonsági csoport funkciót.

Egy NSG-t, akkor célszerű tisztában lennie a beépített szabályokat tartalmaz. Ezek a következők:

-   **Egy adott virtuális hálózaton belüli összes forgalom engedélyezése:** Az azonos Azure virtuális hálózatban lévő összes virtuális gép kommunikálhatnak egymással.

-   **Lehetővé teszi a bejövő Azure terheléselosztási:** Ez a szabály lehetővé teszi, hogy a forgalmat bármilyen forrás-címről bármely címre az Azure load balancer számára.

-   **Az összes bejövő megtagadása:** Ez a szabály az internetről, kifejezetten engedélyezett sourcing minden forgalmat blokkol.

-   **Minden forgalmat az internetre kimenő engedélyezése:** Ez a szabály lehetővé teszi, hogy a virtuális gépek, az internetes kapcsolatok kezdeményezésének. Ha nem szeretné ezeket a kapcsolatokat kezdeményezni, hozzon létre egy szabályt, amely ezeket a kapcsolatokat letiltása vagy kényszerítése a kényszerített bújtatás szeretné.

#### <a name="system-routes-and-user-defined-routes"></a>Rendszerútvonalakat és a felhasználó által megadott útvonalak

Ha a virtuális gépek (VM-EK) ad hozzá egy virtuális hálózatot (VNet) az Azure-ban, megfigyelheti, hogy a virtuális gépek képesek kommunikálni egymással a hálózaton keresztül, automatikusan. Nem kell megadni átjárót, akkor sem, ha a virtuális gépek külön alhálózatokon vannak.

Ugyanez vonatkozik a virtuális gépek és a nyilvános internet közötti kommunikációra, és akár a helyszíni hálózatra is, ha jelen van egy hibrid kapcsolat az Azure és a saját adatközpont között.

![Rendszerútvonalak](media/azure-network-security/azure-network-security-fig-9.png)

A kommunikáció ilyen típusú áramlása azért lehetséges, mert az Azure rendszerútvonalak sorát használja az IP-cím adatforgalmának meghatározására. A rendszerútvonalak az alábbi helyzetekben irányítják a kommunikáció áramlását:

-   Ha ugyanazon az alhálózaton belül történik.

-   Két alhálózat között történik egy virtuális hálózaton belül.

-   Virtuális gépek és az internet között történik.

-   Két VNet között történik egy VPN-átjárón keresztül.

-   Egy vnet és a egy másik virtuális hálózat virtuális hálózatok közötti társviszony-Létesítésen keresztül ([Szolgáltatásláncolás](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Egy VNet és a helyszíni hálózat között történik egy VPN-átjárón keresztül.

Sok vállalat szigorú biztonsági és a házirendek kényszerítésére meghatározott összes hálózati csomag ellenőrzése a helyszíni igénylő megfelelőségi követelmények. Az Azure lehetővé teszi a nevű [kényszerített bújtatás](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) , amely átirányítja a forgalmat a virtuális gépekről a helyszíni egy egyéni útvonalat hoz létre, vagy a [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) hirdetmények expressroute-on keresztül vagy VPN.

Kényszerített bújtatás az Azure-beli virtuális hálózati felhasználó által megadott útvonalak (UDR) keresztül van konfigurálva. Egy alapértelmezett útvonalat az Azure VPN gateway kifejezése egy helyszíni helyre irányítja a forgalmat.

A következő szakaszban azok az Azure Virtual Network az útválasztási tábla-és útvonalak a jelenlegi korlátozás:

-   Minden egyes virtuális hálózat alhálózatához rendelkezik egy beépített, rendszer-útválasztási táblázatához. A rendszer útválasztási tábla az útvonalak a következő három csoport rendelkezik:

 -  **Helyi VNet-útvonal:** Közvetlenül az a cél virtuális gépek ugyanazon a virtuális hálózaton

 - **A helyi útvonalak:** Az Azure VPN gatewayhez

 -  **Alapértelmezett útvonal:** Közvetlenül a az interneten. Nem fedi le az előző két útvonalak privát IP-címekre irányuló csomagokat a rendszer elveti.

-   Felhasználó által megadott útvonalak kiadása hozzon létre egy útválasztási táblázatot, adjon hozzá egy alapértelmezett útvonalat, és majd társítsa az útválasztási táblázatban az ezekhez az alhálózatokhoz kényszerített bújtatás engedélyezése a virtuális hálózat alhálózatához.

-   Meg kell állítania egy "alapértelmezett webhely" a létesítmények közötti helyek között a virtuális hálózathoz csatlakozik.

-   Kényszerített bújtatás kell rendelni egy virtuális hálózattal, amely dinamikus útválasztási VPN-átjáró (nem a statikus átjárók) rendelkezik.

- Kényszerített bújtatás ExpressRoute nincs konfigurálva ez a mechanizmus keresztül, de ehelyett szerint engedélyezve van a társviszony-létesítési ExpressRoute BGP-munkamenetek használatával egy alapértelmezett útvonalat hirdet.

> [!Note]
> További információkért lásd: a [az ExpressRoute dokumentációja](https://azure.microsoft.com/documentation/services/expressroute/) további információt.

#### <a name="network-security-appliances"></a>Hálózati biztonsági berendezéseket
Hálózati biztonsági csoportok és a felhasználó által megadott útvonalak megadhat egy bizonyos szintű hálózati biztonság: a hálózati és átviteli rétegeket, míg a [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), létezhetnek olyan helyzetek, ahol azt szeretné, vagy biztonsági, engedélyeznie kell a a hálózati verem magasabb szintű. Ilyen esetekben javasoljuk, hogy a virtuális hálózati biztonsági berendezéseket az Azure-partnerek által biztosított üzembe.

![Hálózati biztonsági berendezéseket](./media/azure-network-security/azure-network-security-fig-10.png)

Azure-beli hálózati biztonsági berendezéseket a virtuális hálózat biztonsági és hálózati funkciók javítása, és számos gyártók keresztül elérhető a [Azure Marketplace-en](https://azuremarketplace.microsoft.com). Ezek a virtuális biztonsági készülékek számára telepíthető:

-   Magas rendelkezésre állású tűzfalak

-   Behatolás-megelőzési

-   Behatolás felderítése

-   Webalkalmazás-tűzfalak (alsóbb)

-   WAN-optimalizálás

-   Útválasztás

-   Terheléselosztás

-   VPN

-   Tanúsítványok kezelése

-   Active Directory

-   Többtényezős hitelesítés

#### <a name="application-gateway"></a>Alkalmazásátjáró

[A Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) egy dedikált virtuális berendezés, amely az alkalmazáskézbesítési vezérlőt (ADC) szolgáltatásként is biztosít.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Az Application Gateway lehetővé teszi a processzorigényes SSL-lezárások az Alkalmazásátjáró (SSL-tehermentesítést) felé történő kiszervezésével a web farm teljesítmény és rendelkezésre állás optimalizálása érdekében. Egyéb 7. rétegbeli útválasztási lehetőségeket, beleértve a is biztosít:

-   Bejövő forgalom Ciklikus időszeleteléses elosztását

-   Cookie-alapú munkamenet-affinitás

-   URL-cím-alapú útválasztás

-   Egyetlen Application Gateway mögött több webhelyet is üzemeltethet.


A [webalkalmazási tűzfal (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) is az application gateway részeként van megadva. Ez védelmet biztosít a webalkalmazások számára a gyakori internetes biztonsági rések és az azokat kihasználó támadások ellen. Az Application Gateway konfigurálható egy internetkapcsolattal rendelkező, átjáró, csak belső használatú átjáróként vagy a kettő kombinációja szerint.

Az Application Gateway WAF észlelés vagy megelőzés üzemmódban futtatható. Egy gyakori alkalmazási helyzet van, a rendszergazdák megfigyelni a kártevő minták forgalmát észlelési módban történő futtatására. Potenciális biztonsági réseket észlel, miután megelőzés üzemmód műsorhálózatok blokkolja a gyanús bejövő forgalmat.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

Emellett az Application Gateway WAF segítségével monitorozzák a webalkalmazásokat, valós idejű WAF-napló használatával, amely integrálva van a támadások elleni [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) és [az Azure Security Center](https://azure.microsoft.com/services/security-center/) nyomon követéséhez a WAF-riasztások és könnyedén figyelheti a trendeket.

A JSON-formátumú napló közvetlenül az ügyfél tárfiókja kerül. Ezek a naplók teljes hozzáféréssel rendelkezik, és saját adatmegőrzési szabályzatok alkalmazása.

Ezek a naplók képes feldolgozni a saját elemzési rendszer használatával történő [Azure Log Integration](https://aka.ms/AzLog). WAF-naplók integrálva vannak az [Log Analytics](../log-analytics/log-analytics-overview.md) így használhatja a Log Analytics kifinomult a minden részletre kiterjedő lekérdezések végrehajtásához.

#### <a name="azure-web-application-firewall-waf"></a>Az Azure webalkalmazási tűzfal (WAF)

Webalkalmazások egyre inkább ki rosszindulatú támadásoknak, amelyek a biztonsági rés kiaknázása elleni közös ismert biztonsági réseket, például az SQL-injektálás, webhelyek közötti parancsfájlok futtatására és egyéb támadásokat, amelyek megjelennek a [OWASP top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Szigorúan betartandó, javítását és az alkalmazás topológiájának több rétegén figyelés az alkalmazásban az ilyen támadások megakadályozása van szükség.

 ![Az Azure webalkalmazási tűzfal (WAF)](./media/azure-network-security/azure-network-security-fig-13.png)

Egy központosított [webalkalmazási tűzfal (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) webes támadások ellen, és biztonsági felügyeleti leegyszerűsíti az alkalmazások módosítása nélkül.

Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

#### <a name="network-availability-controls"></a>Hálózati rendelkezésre állási vezérlők

A Microsoft Azure-ral többféleképpen lehet elosztani a hálózati forgalmat. Ezek a lehetőségek különböző működési elveken alapulnak, különböző funkciókészlettel rendelkeznek és különböző forgatókönyveket támogatnak. Elkülönítve vagy egymással kombinálva egyaránt használhatók.

Az alábbiakban a rendelkezésre állási hálózati vezérlők:

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Az Azure Load balancer**

Magas rendelkezésre állást és hálózati teljesítményt biztosít alkalmazásai számára. 4. réteg (TCP, UDP) terheléselosztó bejövő forgalmat egy elosztott terhelésű készlet definiált szolgáltatások kifogástalan példányai között osztja el.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Az Azure Load Balancer konfigurálható:

-   Töltse be a bejövő internetes forgalom terheléselosztása virtuális gépekhez. Ez a konfiguráció az úgynevezett [internetre irányuló terheléselosztási](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Forgalom terheléselosztása virtuális hálózatban lévő virtuális gépek közötti, virtuális gépek, cloud Services vagy a helyszíni számítógépek és létesítmények közötti virtuális hálózatban lévő virtuális gépek között. Ez a konfiguráció az úgynevezett [belső terheléselosztás](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Egy adott virtuális gép külső forgalom továbbítása.

A felhőben lévő összes erőforrást kell lennie az internetről elérhető, nyilvános IP-cím. Az Azure-ban a felhő-infrastruktúra erőforrásainak nem átirányítható IP-címeket használ. Az Azure nyilvános IP-címmel rendelkező hálózati címfordítás (NAT) használatával kommunikáljon az internettel.

 **Az Application gateway**

 Az Alkalmazásátjáró (7 réteg az OSI hálózatireferencia-veremben) alkalmazási rétegben működik. Fordított proxyszolgáltatásként működik, az ügyfélkapcsolatok leállítását és a kérelmek háttérvégpontokhoz való továbbítását végzi.

 **A TRAFFIC manager**

A Microsoft Azure Traffic Manager lehetővé teszi különböző adatközpontokban szolgáltatásvégpontokra érkező felhasználói forgalom elosztását. A Traffic Manager által támogatott szolgáltatóvégpontok közé tartozik az Azure virtuális gépek, webalkalmazások, és felhőszolgáltatásokat fejleszthet. A Traffic Manager külső, nem Azure-végpontokkal együtt is használható.

A TRAFFIC Manager a tartománynévrendszer (DNS) használatával a leginkább megfelelő végpontra alapján az ügyfélkéréseket a [forgalom-útválasztási módszer](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) és a végpontok állapotát. A TRAFFIC Manager egy sor különböző alkalmazás igényeinek megfelelően, a végpontonkénti állapotot forgalom-útválasztási módszert biztosít [figyelési](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring), és automatikus feladatátvételt. A Traffic Manager ellenáll a meghibásodásoknak, beleértve akár egy egész Azure-régió meghibásodását is.

Az Azure Traffic Manager lehetővé teszi, hogy az alkalmazás végpontok közötti forgalom elosztását. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás.

A TRAFFIC Manager két fő előnyöket nyújtja:

-   Több egyikére forgalom elosztását [forgalom-útválasztási módszerek](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Végpont állapotának folyamatos figyelése](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) és az Automatikus feladatátvétel, ha a végpontok sikertelen.

Amikor egy ügyfél megpróbál kapcsolódni egy szolgáltatáshoz, a szolgáltatás DNS-nevét először azt kell feloldani egy IP-címet. Az ügyfél ezután csatlakozik a szolgáltatáshoz való hozzáféréshez IP-címet. A TRAFFIC Manager DNS-ügyfelek számára, hogy a szabályok a forgalom-útválasztási módszer alapján meghatározott Szolgáltatásvégpontok közvetlen használja. Az ügyfelek közvetlenül kapcsolódni a kiválasztott végpont. A TRAFFIC Manager nem a proxy- vagy egy átjárót. A TRAFFIC Manager nem látja a forgalom az ügyfél és a szolgáltatás között.

### <a name="azure-network-validation"></a>Azure-hálózat érvényesítése

Azure-hálózat ellenőrzése, győződjön meg arról, hogy működik-e az Azure-hálózatot, erre van konfigurálva, és érvényesítési teheti meg a szolgáltatások és a hálózat elérhető szolgáltatások használatával. Az Azure Network Watcher férhet hozzá áll naplózási és diagnosztikai képességeket biztosít, amelyekkel, a hálózati teljesítményt és állapotot megértése elemzésekkel. Ezek a képességek a Portal, PowerShell, CLI, Rest API-t és az SDK-n keresztül érhetők el.

Az Azure Operational Security hivatkozik a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások védelme érdekében az adatokat, alkalmazásokat és egyéb eszközök, Microsoft Azure-ban. Az Azure Operational Security olyan keretrendszer, amely magában foglalja a különböző képességeket, amelyek a egyedülálló rendszereiből, például a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft Security Response központ program keresztül szerzett ismeretek épül , és az internetes biztonsági fenyegetések világának.

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Az Azure Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Az Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Az Azure resource manager

Talán a legfontosabb biztonsági funkció, a platform olyan személyek és folyamatokat, amelyek működnek a Microsoft Azure. Ez a szakasz ismerteti a Microsoft globális adatközpont-infrastruktúrába használatát javíthatja és biztonsági, folytonosságának és adatvédelmi funkcióit.

Az alkalmazás infrastruktúrája általában számos összetevőből áll – például egy virtuális gépből, tárfiókból és virtuális hálózatból, vagy egy webalkalmazásból, adatbázisból, adatbázis-kiszolgálóból és harmadik féltől származó szolgáltatásokból. Ezeket az összetevőket nem külön entitásokként látja, hanem egyetlen entitás kapcsolódó és egymással összefüggő részeiként. Csoportként érdemes telepíteni, kezelni és megfigyelni őket. Az Azure Resource Manager lehetővé teszi, hogy a megoldásában az erőforrásokkal egy csoportként dolgozzon.

A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. A telepítéshez egy sablon használatos, amely különböző, például tesztelési, átmeneti és üzemi környezetben is képes működni. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően.

**A Resource Manager használatának előnyei**

A Resource Manager számos előnyt kínál:

-   A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

-   A megoldást ismételten telepítheti a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

-   Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.

-   Megadhatja, hogy azok a megfelelő sorrendben legyenek telepítve erőforrások közti függőségeket.

-   Hozzáférés-vezérlést alkalmazhat az összes szolgáltatásra az erőforráscsoportban, mivel a szerepköralapú hozzáférés-vezérlés (RBAC) natív módon integrálva van a felügyeleti platformba.

-   Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.

-   Egy címke osztozó erőforrások csoportjának költségeit megtekintve jól átláthatók a szervezet számlái.

> [!Note]
> A Resource Manager egy új módot kínál a megoldások telepítésére és kezelésére. Ha a korábbi telepítési modellt használta, és további információkat kíván megtudni a változásokról, tekintse meg [A Resource Manager telepítés és a hagyományos telepítés ismertetése](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) című cikket.

## <a name="azure-network-logging-and-monitoring"></a>Azure-beli hálózati naplózás és figyelés

Az Azure a figyelése, megelőzését, észlelését és válaszadás a biztonsági események számos eszközt kínál. Ezen a területen elérhető leghatékonyabb eszköz a következők:

-   Network Watcher

-   Hálózati erőforrások szintű monitorozása

-   Log Analytics

### <a name="network-watcher"></a>Hálózati figyelő

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -forgatókönyv-alapú figyelés áll rendelkezésre a Network Watcher funkciókat. A szolgáltatás része, csomagrögzítés, következő ugrási, IP-folyamat ellenőrzése, a biztonsági csoport nézet NSG-Folyamatnaplók. Forgatókönyv szintű monitorozása biztosítja a teljes körű képet szakembereket egyes hálózati erőforrások monitorozása a hálózati erőforrásokhoz.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

A Network Watcher egy regionális szolgáltatás, amely lehetővé teszi a figyelési és diagnosztizálási tevékenységet végezhet hálózati forgatókönyvek szintjén, a, és az Azure-ból. A hálózati diagnosztikai és vizualizációs eszközök a Network Watcherrel elérhető segítenek megérteni, diagnosztizálása és betekintést nyerhet az Azure-ban a hálózati.

A Network Watcher jelenleg a következő képességekkel rendelkezik:

#### <a name="topology"></a>Topológia

[Topológia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) adja vissza egy grafikont a hálózati erőforrásokat a virtuális hálózatban. A gráf ábrázol, amelyek a végpontok közötti hálózati kapcsolat az erőforrások közötti kapcsolatot biztosítja. A portálon topológia található objektumokat adja vissza erőforrás a virtuális hálózat alapját megfelelően. A kapcsolatok vannak kitaláltak a Network Watcher régión kívül az erőforrások közötti vonalak jelölik, még akkor is, ha az erőforrás a csoport nem lesznek megjelenítve. Az erőforrások a portál nézetének adott vissza a hálózati összetevők, amelyek ábrázolt vannak a részhalmazát képezik. Hálózati erőforrások teljes listájának megtekintéséhez használja [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) vagy [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Visszaadott erőforrások, az azok közötti kapcsolatot két kapcsolat alatt modellezése eltér.

- **Tartalmazottsági** – a virtuális hálózat tartalmaz egy alhálózatot, amely tartalmazza a hálózati adapteren.

- **Kapcsolódó** – A hálózati adapter társítva egy virtuális Gépet.

#### <a name="variable-packet-capture"></a>Változó csomagrögzítés

Network Watcher [változó csomagrögzítés](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) hozhat létre csomagrögzítési munkamenetet, és a virtuális gépről érkező forgalom nyomon követéséhez. Csomag rögzítési alapján diagnosztizálhatja a hálózat rendellenességek mindkét reaktív és proactivity. Más használati módjai többek között, hálózati statisztika, azonosítsa a hálózati behatolásokat, hibakeresése, ügyfél-kiszolgáló közötti kommunikációt, és még sok más információk összegyűjtéséhez.

A csomagrögzítés egy virtuálisgép-bővítmény, amely keretében a Network Watcher távoli elindult. Ez a funkció egyszerűsíti a csomagrögzítés értékes időt takarít meg használni kívánt virtuális gépet manuálisan futó terhe. A csomagrögzítés is elindítható a portal, PowerShell, CLI vagy REST API használatával. Például hogyan csomagrögzítés is elindítható a virtuális gép riasztások van.

#### <a name="ip-flow-verify"></a>IP-forgalom ellenőrzése

[IP-forgalmának ellenőrzése](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) ellenőrzi, ha egy csomag engedélyezett vagy tiltott, vagy egy virtuális gépből 5-ször több információt alapján. Ezt az információt iránya, a protokoll, a helyi IP-cím, a távoli IP, a helyi port és a távoli port áll. A csomagot a rendszer megtagadja egy biztonsági csoport is, ha a csomagot letiltó szabály nevét adja vissza. Minden forrás vagy cél IP-választhatók, amíg ez a funkció segít a rendszergazdák gyorsan diagnosztizálhatja a kapcsolódási problémák, vagy az internethez és a vagy a helyszíni környezetben.

IP-forgalmának ellenőrzése célozza meg, a virtuális gép hálózati adapter. Forgalom ellenőrzése majd, illetve a hálózati illesztőt a konfigurált beállítások alapján. Ez a funkció hasznos megerősítve, ha egy szabályt a hálózati biztonsági csoport blokkolja a bejövő és kimenő forgalmat, vagy a virtuális gépről.

#### <a name="next-hop"></a>Következő ugrás

Meghatározza, hogy a [következő Ugrás](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) , hogy bármely diagnosztizálása lehetővé teszi a csomagok irányítása az Azure hálózati hálóban, felhasználó által megadott útvonalak konfigurációja. Virtuális gép adatforgalom egy célhelyre társított egy hálózati adapteren. az érvényes útvonalak alapján Következő ugrás olvas be a következő ugrás típusa és a egy csomag IP-címét egy adott virtuális gép és a hálózati adapteren. Ez segít meghatározni, ha a csomag van irányítja a cél vagy a forgalmat, fekete folyamatban furatos van.

Következő ugrás is az a következő ugrás társított útvonaltábla adja vissza. Lekérdezésekor a következő ugrás, ha egy felhasználó által megadott útvonalat az útvonal van meghatározva, hogy vissza kell. Ellenkező esetben a következő ugrás "Rendszerútvonal" adja vissza.

#### <a name="security-group-view"></a>biztonsági csoport Nézet

A hatékony és alkalmazott biztonsági szabályok, amelyek érvényesek a virtuális gép beolvasása. Hálózati biztonsági csoportok társítva, egy alhálózatot vagy egy hálózati adapterek szintjén. Ha kapcsolódik egy alhálózat szintjén, az alhálózat összes Virtuálisgép-példány vonatkozik. Hálózati [biztonsági csoport nézet](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) adja vissza az összes beállított NSG-k és szabályokat, amelyek a konfiguráció betekintést nyújtó virtuális gép hálózati adapter és az alhálózat szintjén vannak társítva. Emellett az érvényben lévő biztonsági szabályokat a rendszer minden virtuális gép hálózati adapterei adja vissza. Használatával a hálózati biztonsági csoport nézet, felmérheti a hálózati biztonsági réseket, például a portok megnyitása virtuális Géphez. Ha a hálózati biztonsági csoport a várt módon működik alapján is ellenőrizheti a [a konfigurált és érvényben lévő biztonsági szabályok összehasonlítása](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Hálózati biztonsági csoportok naplózásának

 Hálózati biztonsági csoportok folyamatnaplóit engedélyezze a forgalmat, amely engedélyezi vagy megtagadja a csoport biztonsági szabályai szerint naplók rögzítését. A folyamat egy 5-ször több információt – forrás IP-címe, cél IP-cím, forrásoldali portszám, céloldali Port és protokoll határozza meg.

[Hálózati biztonsági csoportok folyamatnaplóit](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) érhetők el a Network Watcher, amely lehetővé teszi a bejövő és kimenő IP-forgalom hálózati biztonsági csoport használatával kapcsolatos információk megtekintéséhez.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Virtuális hálózati átjáró és a kapcsolat hibaelhárítása

A Network Watcher számos funkciót kínál a ismertetése az Azure-ban a hálózati erőforrások vonatkozik. Ezek a képességek egyik erőforrás hibaelhárítás. [Erőforrás hibaelhárítás](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) PowerShell, CLI és REST API nem hívható. Meghívni, Network Watcher megvizsgálja a virtuális hálózati átjáró vagy a kapcsolat állapotát, és a csapatával az eredményeket adja vissza.

Ez a szakasz végigvezeti a különböző felügyeleti feladatok, amelyek jelenleg az erőforrás hibaelhárítás.

-   [A virtuális hálózati átjáró hibáinak elhárítása](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Kapcsolat hibaelhárítása](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Hálózati előfizetési korlátok

[Hálózati előfizetési korlátozásait](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) biztosítanak a hálózati erőforrás ellen rendelkezésre álló erőforrások maximális számát egy régióban egy adott előfizetés használatának részleteit.

#### <a name="configuring-diagnostics-log"></a>Konfiguruje se diagnostika napló

A Network Watcher lehetővé egy [diagnosztikai naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) megtekintése. Ez a nézet az összes hálózati erőforrások, amelyek támogatják a diagnosztikai naplózás tartalmazza. Ebben a nézetben engedélyezheti és letilthatja a hálózati erőforrásokat, gyorsan és kényelmesen.

### <a name="network-resource-level-monitoring"></a>Hálózati erőforrások szintű monitorozása

A következő funkciók érhetők el az erőforrás-szolgáltatói figyelése:

#### <a name="audit-log"></a>Napló

Hálózatok konfigurációjának részeként végrehajtott műveleteket a rendszer naplózza. Ezek a naplók alapvető különböző előírások létesíteni. Ezek a naplók tekinthetnek meg az Azure Portalon, vagy használja a Microsoft eszközök, például a Power bi-ban vagy harmadik féltől származó eszközökkel. Auditnaplók – a portal, PowerShell, CLI és Rest API-val érhetők el.

> [!Note]
> A naplók riasztásaihoz további információkért lásd: [auditálási műveletek a Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Auditnaplók minden hálózati erőforrásokon végzett műveletek érhetők el.


#### <a name="metrics"></a>Mérőszámok

Mérőszám játszik TELJESÍTMÉNYMÉRÉSEK és a egy időszakban gyűjtött teljesítményszámlálók. Metrikák érhetők el jelenleg az Application Gateway számára. Mérőszámok segítségével küszöbérték alapuló riasztások aktiválása céljából. Az Azure Application Gateway alapértelmezés szerint a háttérkészlet összes erőforrásának állapotát figyeli, és automatikusan eltávolítja az összes erőforrást a készletből sérültnek. Az Application Gateway továbbra is figyeli a nem megfelelő állapotú példányokat, és hozzáadja őket a megfelelő háttér-készlet számára, elérhetővé válnak, és a állapotadat-mintavételek válaszolni. Az Application gateway küld az állapotadat-mintavételek a definiált a háttér-HTTP-beállítások ugyanazt a portot. Ez a konfiguráció biztosítja, hogy a mintavétel ellenőrzi, hogy az ügyfelek szokott használni a háttérrendszerhez való csatlakozáshoz ugyanazt a portot.

> [!Note]
> Lásd: [Application Gateway-diagnosztika](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) megtekintéséhez, hogyan metrikák riasztások létrehozásához használható.

#### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Rendszeres és spontán események hálózati erőforrások által létrehozott és a storage-fiókok küldött egy Eseményközpontba, vagy a Log Analytics naplózza. Ezek a naplók rávilágítanak az erőforrás állapotát. Ezek a naplók az eszközök, például a Power bi-ban és a Log Analytics is megtekinthetők. Diagnosztikai naplók megtekintése talál [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Diagnosztikai naplók érhetők el [terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), útvonalakat, és [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

A Network Watcher biztosít a diagnosztikai naplók megtekintése. Ez a nézet az összes hálózati erőforrások, amelyek támogatják a diagnosztikai naplózás tartalmazza. Ebben a nézetben engedélyezheti és letilthatja a hálózati erőforrásokat, gyorsan és kényelmesen.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) egy szolgáltatás az Azure-ban, amely figyeli a felhőbeli és helyszíni környezeteket a rendelkezésre állás és teljesítmény fenntartása érdekében. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson.

A log Analytics a hálózatok figyelése a következő megoldásokat kínál:

-   Network Performance Monitor (NPM)

-   Az Azure Application Gateway analytics

-   Azure-beli hálózati biztonsági csoport analytics

#### <a name="network-performance-monitor-npm"></a>A Network performance monitor (NPM)
A [Network Performance Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) felügyeleti megoldás, egy Hálózatfigyelő megoldás, amely figyeli a állapot, a rendelkezésre állás és a hálózatok lemezekről.

Közötti kapcsolat figyelésére szolgál:

-   Nyilvános felhő és helyi környezetekben

-   Az adatközpontok és a felhasználó tartózkodási helye (fiókirodákban)

-   Egy többrétegű alkalmazás különböző szinteken futtató alhálózatok.


#### <a name="azure-application-gateway-analytics-in-log-analytics"></a>A log analytics az Azure application gateway analytics

Az Application Gateway átjárók támogatottak a következő naplók kapcsolódnak:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

A következő metrikákat az Application Gateway átjárók támogatottak:

-   5 perces átviteli sebesség

#### <a name="azure-network-security-group-analytics-in-log-analytics"></a>Azure-beli hálózati biztonsági csoport elemzési a log analyticsben

A következő naplók kapcsolódnak támogatottak [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** Melyik NSG szabályok érvényesek virtuális gépeket és példányszerepköröket MAC-cím alapján bejegyzést tartalmaz. Ezek a szabályok állapota gyűjtött minden 60 másodpercben.

- **NetworkSecurityGroupRuleCounter:** Az egyes NSG-szabályokat alkalmaznak-forgalom engedélyezése vagy megtagadása hány alkalommal bejegyzést tartalmaz.

## <a name="next-steps"></a>További lépések
További információ biztonságával kapcsolatban olvassa el részletes biztonsági témakörök el:

-   [Naplóelemzés hálózati biztonsági csoportok (NSG-k)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Hálózatkezelés innovációkról a felhőalapú megszakítások időtartamát befolyásoló](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: A hálózati kapcsoló szoftvert, hogy a Microsoft globális felhő powers](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Hogyan hoz létre a Microsoft a gyors és megbízható globális hálózatán](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Világítás hálózati innováció mentése](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
