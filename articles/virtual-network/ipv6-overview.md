---
title: Az IPv6 áttekintése az Azure virtuális hálózathoz
titlesuffix: Azure Virtual Network
description: IPv6-végpontok és adatelérési utak IPv6-szolgáltatás leírása egy Azure virtuális hálózatban.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 887f9e100a60561271a4c15777e7131ea1f1f722
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631437"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>Mi az IPv6 az Azure virtuális hálózathoz?

Az IPv6 for Azure Virtual Network (VNet) lehetővé teszi, hogy az Azure-ban iPv6- és IPv4-kapcsolattal rendelkező alkalmazásokat üzemeltetjen virtuális hálózaton belül, valamint az internetről és az internetről. A nyilvános IPv4-címek kimerülése miatt a mobilitásés az eszközök internete (IoT) új hálózatai gyakran az IPv6-ra épülnek. Még a régóta fennálló internetszolgáltató és a mobilhálózatok is iPv6-ra alakulnak át. A csak IPv4-szolgáltatások valódi hátrányba hozhatják magukat mind a meglévő, mind a feltörekvő piacokon. A kettős veremi IPv4/IPv6-kapcsolat lehetővé teszi, hogy az Azure által üzemeltetett szolgáltatások átlépjenek ezen a technológiai résen a globálisan elérhető, kéthalmozott szolgáltatásokkal, amelyek könnyen kapcsolódnak mind a meglévő IPv4-hez, mind az új IPv6-eszközökhöz és -hálózatokhoz.

Az Azure eredeti IPv6-kapcsolata megkönnyíti a kettős halmok (IPv4/IPv6) internetkapcsolatának biztosítását az Azure-ban üzemeltetett alkalmazások számára. Lehetővé teszi a terheléselosztásos IPv6-kapcsolattal rendelkező virtuális gépek egyszerű telepítését mind a bejövő, mind a kimenő kezdeményezett kapcsolatokhoz. Ez a funkció továbbra is elérhető, és további információk [itt](../load-balancer/load-balancer-ipv6-overview.md)érhetők el.
Az IPv6 for Azure virtuális hálózat sokkal teljesebb funkcionalitású, lehetővé teszi a teljes IPv6-megoldásarchitektúrák azure-beli üzembe helyezését.


Az alábbi ábra egy egyszerű kettős verem (IPv4/IPv6) üzembe helyezést ábrázol az Azure-ban:

![IPv6-hálózat telepítési diagramja](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Előnyök

IPv6 az Azure VNET előnyei:

- Segít kiterjeszteni az Azure által üzemeltetett alkalmazások elérhetőségét a növekvő mobil- és tárgyak internetes piacokra.
- A két halmozott IPv4/IPv6 virtuális gépek maximális szolgáltatástelepítési rugalmasságot biztosítanak. Egyetlen szolgáltatáspéldány is csatlakozhat az IPv4-és az IPv6-kompatibilis internetes ügyfelekhez.
- Régóta fennálló, stabil Azure-virtuális gép-internet IPv6-kapcsolatra épül.
- Alapértelmezés szerint biztonságos, mivel az IPv6-kapcsolat csak akkor jön létre, ha kifejezetten kéri a központi telepítésben.

## <a name="capabilities"></a>Funkciók

Az IPv6 for Azure virtuális hálózat a következő képességeket tartalmazza:

- Az Azure-ügyfelek saját IPv6 virtuális hálózati címterületet határozhatnak meg, hogy megfeleljenek az alkalmazások, az ügyfelek igényeinek, vagy zökkenőmentesen integrálhatók a helyszíni IP-tárhelyükbe.
- A kettős veremű (IPv4 és IPv6) virtuális hálózatok kettős veremalhálózattal lehetővé teszik, hogy az alkalmazások a virtuális hálózatukban vagy az interneten lévő IPv4- és IPv6-erőforrásokhoz is csatlakozzanak.
    > [!IMPORTANT]
    > Az IPv6 alhálózatának pontosan /64 méretűnek kell lennie.  Ez biztosítja a jövőbeli kompatibilitást, ha úgy dönt, hogy engedélyezi az alhálózat irányítását egy helyszíni hálózatra, mivel egyes útválasztók csak /64 IPv6 útvonalakat tudnak elfogadni.  
- Az erőforrások védelme a hálózati biztonsági csoportokIPv6-szabályaival.
    - Az Azure platform elosztott szolgáltatásmegtagadási (DDoS) védelme pedig az internetre néző nyilvános IP-
- Testreszabhatja az IPv6-forgalom útválasztását a virtuális hálózatban a felhasználó által definiált útvonalakkal, különösen akkor, ha a hálózati virtuális berendezéseket használja az alkalmazás bővítéséhez.
- A Linux és a Windows virtuális gépek mind használhatják az IPv6-ot az Azure VNET-hez
- [Standard IPv6 nyilvános terheléselosztó](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) támogatás rugalmas, méretezhető alkalmazások létrehozásához, amelyek a következők:
    - Választható IPv6-állapotminta annak meghatározására, hogy mely háttérkészlet-példányok állapotúak, és így új folyamatokat kaphatnak.
    - Opcionális kimenő szabályok, amelyek teljes deklaratív vezérlést biztosítanak a kimenő kapcsolatok felett, hogy ezt a képességet az adott igényekhez méretezhetők és hangolják.
    - Opcionális több előtér-konfigurációk, amelyek lehetővé teszik, hogy egy terheléselosztó több Nyilvános IPv6 IP-cím használatát teszi lehetővé- ugyanaz az előtér-protokoll és port újra felhasználható az előtér-címek között.
    - A választható IPv6-portok a terheléselosztási szabályok *lebegő IP-szolgáltatásával* újra felhasználhatók háttérpéldányokon 
    - Megjegyzés: A terheléselosztás nem hajt végre protokollfordítást (nat64 nem). 
- [Standard IPv6 belső terheléselosztó](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) támogatás rugalmas többrétegű alkalmazások létrehozásához az Azure VNET-kben.   
- Alapvető IPv6 nyilvános terheléselosztó támogatása az örökölt telepítésekkel való kompatibilitás érdekében
- [A fenntartott IPv6 nyilvános IP-címek és címtartományok](ipv6-public-ip-address-prefix.md) stabil, kiszámítható IPv6-címeket biztosítanak, amelyek megkönnyítik az azure-ban tárolt alkalmazások engedélyezési listáját a vállalat és az ügyfelek számára.
- A példányszintű nyilvános IP-cím közvetlenül biztosít IPv6-internetkapcsolatot az egyes virtuális gépekhez.
- [IPv6 hozzáadása a meglévő IPv4-telepítésekhez](ipv6-add-to-existing-vnet-powershell.md)– ez a szolgáltatás lehetővé teszi, hogy könnyedén adjon hozzá IPv6-kapcsolatot a meglévő, csak IPv4-telepítésekhez anélkül, hogy újra létre kellene hoznia a központi telepítéseket.  Az IPv4 hálózati forgalmat ez a folyamat nem befolyásolja, így az alkalmazástól és az operációs rendszertől függően előfordulhat, hogy az iPv6-ot még az élő szolgáltatásokhoz is hozzá tudja adni.    
- Az IPv6-rekordokhoz való Azure DNS-támogatással zökkenőmentesen férhetnek hozzá az internetes ügyfelek a kétveremes alkalmazáshoz az általuk választott protokoll használatával. 
- Hozzon létre kétverű alkalmazásokat, amelyek automatikusan a terheléshez méretezhetők az IPv6 virtuálisgép-méretezési készletekkel.
- [Virtuális hálózati (VNET) társviszony-létesítés](virtual-network-peering-overview.md) – mind a regionális és globális társviszony-létesítés – lehetővé teszi, hogy látszólag csatlakozni két verem VNETs- mind az IPv4 és IPv6 végpontok a virtuális gépek a társviszony-létesített hálózatok képes lesz kommunikálni egymással. Az IPv4-alapú VNE-k segítségével akár két veremben is társviszonyt létesíthet, miközben a központi telepítéseket kettős veremre helyezi át. 
- Az IPv6 hibaelhárítása és diagnosztikája a terheléselosztó metrikákkal/riasztással és a Hálózatfigyelő olyan funkcióival érhető el, mint a csomagrögzítés, az NSG-folyamatnaplók, a kapcsolathibaelhárítás és a kapcsolatfigyelés.   

## <a name="scope"></a>Hatókör
Az IPv6 for Azure VNET egy alapvető szolgáltatáskészlet, amely lehetővé teszi az ügyfelek számára, hogy kéthalomban (IPv4+IPv6) alkalmazásokat üzemeltetjenek az Azure-ban.  Az IPv6-támogatást több Azure hálózati funkcióhoz kívánjuk hozzáadni az idő múlásával, és végül az Azure PaaS-szolgáltatások két veremverzióit kínáljuk, de addig is az összes Azure PaaS-szolgáltatás elérhető az IPv4-végpontokon keresztül két veremű virtuális gépeken.   

## <a name="limitations"></a>Korlátozások
Az Azure virtuális hálózati kiadásához való jelenlegi IPv6-kiadásra a következő korlátozások vonatkoznak:
- Az IPv6 for Azure virtuális hálózat az összes globális Azure Commercial régióban elérhető az összes telepítési módszer használatával.  Az Egyesült Államok kormányzati felhőjében történő üzembe helyezés ideiglenesen az ARM (JSON) sablonra, a parancssori felületre (CLI) és a Powershellre korlátozódik.  Az IPv6-támogatás az Egyesült Államok kormánya felhőportálján hamarosan elérhető lesz.  
- Az ExpressRoute-átjárók csak IPv4-forgalomhoz használhatók olyan virtuális hálózatban, amelyen engedélyezve van az IPv6.  Az IPv6-forgalom támogatása szerepel az ütemtervben.   
- A VPN-átjárók nem használhatók olyan virtuális hálózatban, amelyben az IPv6 engedélyezve van, közvetlenül vagy társviszonyban a "UseRemoteGateway" funkcióval.
- Az Azure platform (AKS, stb. ) nem támogatja az IPv6-kommunikáció t.  

## <a name="pricing"></a>Díjszabás

Az IPv6 Azure-erőforrások és a sávszélesség díja az IPv4-vel megegyező díjakat számít fel. Az IPv6-nak nincsenek további vagy eltérő díjai. A [nyilvános IP-címek](https://azure.microsoft.com/pricing/details/ip-addresses/), a [hálózati sávszélesség](https://azure.microsoft.com/pricing/details/bandwidth/)vagy a [terheléselosztó](https://azure.microsoft.com/pricing/details/load-balancer/)díjszabásáról részletesen ismertheti.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogy [miként telepíthet egy IPv6-alapú kettős veremalapú alkalmazást az Azure PowerShell használatával.](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)
- Ismerje meg, hogyan [telepíthet egy IPv6-alapú kettős veremalapú alkalmazást az Azure CLI használatával.](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)
- Az [IPv6-alapú kettős veremalapú alkalmazások üzembe helyezése az Erőforrás-kezelősablonokkal (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
