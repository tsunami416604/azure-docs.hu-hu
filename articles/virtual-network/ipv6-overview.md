---
title: Az IPv6 for Azure Virtual Network (előzetes verzió) áttekintése
titlesuffix: Azure Virtual Network
description: IPv6-végpontok és adatelérési útvonalak IPv6-leírása egy Azure-beli virtuális hálózaton.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 5093b74484cd04a0c0c7afed8e2ebc725af033f5
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249829"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Mi az az IPv6 for Azure Virtual Network? (Előzetes verzió)

Az IPv6 for Azure Virtual Network (VNet) lehetővé teszi, hogy az Azure-ban IPv6-és IPv4-kapcsolaton keresztül is üzemeltetheti az alkalmazásokat a virtuális hálózaton belül és az internetről. A nyilvános IPv4-címek kimerítése miatt az új mobilitási és eszközök internetes hálózatai hálózatok (IoT) gyakran az IPv6-ra épülnek. Még a hosszú ideig létesített ISP-és mobil hálózatok is át lettek alakítva IPv6-ra. Az IPv4-alapú szolgáltatások a meglévő és a feltörekvő piacokon is valós hátrányban találhatják magukat. A Dual stack IPv4/IPv6-kapcsolat lehetővé teszi, hogy az Azure által üzemeltetett szolgáltatások belépjék ezt a technológiai rést olyan globálisan elérhető, kettős halmozású szolgáltatásokkal, amelyek könnyedén csatlakozhatnak a meglévő IPv4-hez és az új IPv6-eszközökhöz és hálózatokhoz.

Az Azure eredeti IPv6-kapcsolata megkönnyíti az Azure-ban üzemeltetett alkalmazások számára a kettős verem (IPv4/IPv6) internetkapcsolatának megadását. Lehetővé teszi a virtuális gépek egyszerű üzembe helyezését elosztott terhelésű IPv6-kapcsolattal a bejövő és a kimenő kezdeményezett kapcsolatokhoz. Ez a funkció továbbra is elérhető, és további információk [itt](../load-balancer/load-balancer-ipv6-overview.md)érhetők el.
Az IPv6 az Azure Virtual networkhez sokkal teljesebb funkcionalitással rendelkezik, így teljes körű IPv6-megoldási architektúrák helyezhetők üzembe az Azure-ban.

> [!Important]
> Az Azure Virtual Networkhez készült IPv6 jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az alábbi ábrán egy egyszerű, kettős verem (IPv4/IPv6) üzemelő példány látható az Azure-ban:

![IPv6 hálózati telepítési diagram](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Előnyök

Az Azure Virtual Network IPv6 előnyei:

- Segít kiterjeszteni az Azure által üzemeltetett alkalmazások elérhetőségét a növekvő mobil-és eszközök internetes hálózata piacokon.
- A kettős halmozott IPv4-/IPv6-alapú virtuális gépek maximális rugalmasságot biztosítanak a szolgáltatás számára. Egyetlen szolgáltatási példány is csatlakozhat IPv4-és IPv6-kompatibilis internetes ügyfelekhez.
- Hosszú távú, stabil Azure-alapú virtuális gépek közötti IPv6-kapcsolaton alapul.
- Alapértelmezés szerint biztonságos, mivel az internethez való IPv6-kapcsolat csak akkor áll fenn, ha explicit módon megkéri azt a központi telepítésben.

## <a name="capabilities"></a>Funkciók

A VNet-hez készült IPv6 a következő képességeket tartalmazza:

- Az Azure-ügyfelek meghatározhatják saját IPv6-alapú virtuális hálózati címüket, hogy megfeleljenek alkalmazásaik, ügyfeleik igényeinek, vagy zökkenőmentesen integrálhatók a helyszíni IP-helyükbe.
- A Dual stack alhálózatokkal rendelkező kettős verem (IPv4 és IPv6) virtuális hálózatok lehetővé teszik, hogy az alkalmazások IPv4-és IPv6-erőforrásokkal csatlakozhassanak a virtuális hálózatban vagy az interneten.
- Az erőforrások védelme a hálózati biztonsági csoportokra vonatkozó IPv6-szabályokkal
- Testre szabhatja az IPv6-forgalom útválasztását a virtuális hálózaton a felhasználó által megadott útvonalakkal – különösen a hálózati virtuális berendezések kihasználása az alkalmazások bővítéséhez.
- Lehetővé teszi, hogy az internetes ügyfelek zökkenőmentesen férjenek hozzá a kettős stack-alkalmazásokhoz, és a Azure DNS IPv6-(AAAA-) rekordokat támogatják. 
- Standard IPv6 nyilvános Load Balancer támogatás rugalmas, méretezhető alkalmazások létrehozásához, amelyek a következőket tartalmazzák:
    - Opcionális IPv6-alapú állapot-mintavétel, amely meghatározza, hogy mely háttérbeli készlet-példányok állapota, így az új folyamatok fogadására is képes. .  
    - Opcionális kimenő szabályok, amelyek teljes körű deklaratív vezérlést biztosítanak a kimenő kapcsolaton keresztül az adott igényeknek megfelelően méretezhető és hangolható módon.
    - Nem kötelező több előtér-konfiguráció, amely lehetővé teszi, hogy egyetlen terheléselosztó több IPv6 nyilvános IP-címet használjon – ugyanezt a frontend-protokollt és portot újra felhasználhatja a frontend-címek között.
- A példány szintű nyilvános IP-cím IPv6-alapú internetkapcsolatot biztosít közvetlenül az egyes virtuális gépek számára.
- Egyszerűen hozzáadhat IPv6-kapcsolatot a meglévő IPv4-alapú üzemelő példányokhoz a verziófrissítéssel.
- Hozzon létre olyan kettős stack-alkalmazásokat, amelyek automatikusan méretezhetők a terheléshez a virtuálisgép-méretezési csoportokkal.
- Az előzetes verzióhoz készült portál támogatja a kettős verem (IPv4 + IPv6) virtuális hálózatok és alhálózatok, az IPv6 hálózati biztonsági csoportra vonatkozó szabályok, az IPv6-felhasználó által megadott útvonalak és az IPv6 nyilvános IP-címek interaktív létrehozását/szerkesztését és törlését.  

## <a name="limitations"></a>Korlátozások
Az IPv6 az Azure Virtual networkhez szolgáltatás előzetes kiadása a következő korlátozásokkal rendelkezik:
- Az Azure Virtual Network (előzetes verzió) IPv6 az összes globális Azure-régióban elérhető, de csak a globális Azure-ban – nem a kormányzati felhők.
- A standard Load Balancer-összetevők portálon való támogatása csak megtekintést biztosít.  Az Azure PowerShell és a parancssori felület (CLI) segítségével azonban teljes körű támogatást és dokumentációt (példákkal) is elérhetővé standard Load Balancer üzemelő példányokhoz.   
- Network Watcher az előzetes verzióra vonatkozó támogatás a NSG és a hálózati csomagok rögzítésére korlátozódik.
- Az előzetes verzióban nem támogatott a virtuális hálózatok társítása (regionális vagy globális).
- Standard IPv6 külső Load Balancer használata esetén a következő korlátozások érvényesek: 
  - A kimenő szabályok több előtér-nyilvános IP-címet is hivatkozhatnak, de **nem** hivatkozhatnak IPv6 nyilvános előtagra. Az IP-nyilvános előtag csak IPv4-előtagokat támogat.
  - Az IPv6-terheléselosztási szabályok **nem** használhatják a *lebegő IP-* szolgáltatást. A háttérbeli példányokon a portok újrafelhasználása csak az IPv4 protokollal támogatott.
- Az Azure nyilvános IP-cím előtag szolgáltatása nem támogatja az internetre irányuló IPv6-címek egy blokkjának lefoglalását.

## <a name="pricing"></a>Díjszabás

Az IPv6-os Azure-erőforrások és a sávszélesség az IPv4-vel megegyező díjszabás szerint történik. Az IPv6 esetében nincs további vagy eltérő díj. A [nyilvános IP-címekre](https://azure.microsoft.com/pricing/details/ip-addresses/), a [hálózati sávszélességre](https://azure.microsoft.com/pricing/details/bandwidth/)vagy a [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)díjszabására vonatkozó részletekért tekintse meg a következőt:.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [helyezhet üzembe egy IPv6-alapú kettős verem-alkalmazást Azure PowerShell használatával](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Ismerje meg, hogyan [helyezhet üzembe egy IPv6-alapú kettős verem-alkalmazást az Azure CLI használatával](virtual-network-ipv4-ipv6-dual-stack-cli.md).
