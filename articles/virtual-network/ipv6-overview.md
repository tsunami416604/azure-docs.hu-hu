---
title: IPv6-alapú Azure virtuális hálózat (előzetes verzió) áttekintése
titlesuffix: Azure Virtual Network
description: IPv6 végpont és az adatok elérési utak, egy Azure virtuális hálózaton IPv6 leírása.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130935"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Mi az IPv6 és az Azure Virtual Network? (Előzetes verzió)

Az Azure Virtual Network (VNET) az IPv6 lehetővé teszi alkalmazások üzemeltetése az Azure-ban is egy virtuális hálózaton belül, és az Internet és IPv6 és IPv4 összefüggő. Nyilvános IPv4-címek kimerülése, mert a mobilitás-és Internet of Things (IoT) új hálózatok gyakran épülnek IPv6. Még akkor is, a hosszú jön létre, az Internetszolgáltatók és mobil hálózatok IPv6 átalakításából származnak folyamatban van. Csak IPv4-alapú szolgáltatások találhatja meg magukat valós hátrányba meglévő és újonnan felbukkanó is piacokon. Kettős verem IPv4/IPv6-kapcsolatot lehetővé teszi, hogy az Azure-ban üzemeltetett szolgáltatások gyermekelemeinek a technológia gap azonnal is a meglévő IPv4 és az ezek új IPv6-alapú eszközöket és hálózatokat összekapcsolása globálisan elérhető, kettős-ig halmozott szolgáltatások segítségével.

Eredeti IPv6-kapcsolatot az Azure megkönnyíti a kettős verem (IPv4/IPv6) internetkapcsolat biztosít az Azure-ban üzemeltetett alkalmazásaikon. Lehetővé teszi a virtuális gépek egyszerű üzembe helyezés a bejövő és kimenő kezdeményezett kapcsolatok az elosztott terhelésű IPv6-kapcsolatot. Ez a funkció továbbra is elérhető, és további információk érhetők el [Itt](../load-balancer/load-balancer-ipv6-overview.md).
IPv6-alapú Azure virtuális hálózat teljes sokkal több kiemelt-engedélyezése a teljes IPv6-alapú megoldásarchitektúrák, Azure-ban üzembe helyezni.

> [!Important]
> IPv6 és az Azure Virtual Network jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az alábbi ábra bemutatja egy egyszerű kettős verem (IPv4/IPv6) központi telepítés az Azure-ban:

![Az IPv6 hálózati telepítési diagram](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Előnyök

Az Azure virtuális hálózat IPv6-előnyök:

- Segít bontsa ki az Azure-ban üzemeltetett alkalmazások a növekvő, mobil és IOT-piacokon hatókörét.
- Kettős halmozott IPv4/IPv6-alapú virtuális gépek maximális szolgáltatás üzembe helyezési rugalmasságot biztosítanak. Egyetlen szolgáltatáspéldány IPv4- és IPv6-kompatibilis internetes ügyfelek csatlakozhatnak.
- Interneten régóta, stabil Azure VM-az-buildek IPv6-kapcsolatot.
- Biztonságos alapértelmezés szerint, mert IPv6-kapcsolatot az interneten, csak akkor jön létre, ha explicit módon kér, a központi telepítésben.

## <a name="capabilities"></a>Funkciók

A virtuális gépek IPv6-támogatás a következő funkciókat biztosítja:

- Az Azure-ügyfelek a saját IPv6-alapú virtuális hálózat címtere alkalmazásaikat, az ügyfelek igényeinek, vagy zökkenőmentesen integrálhat saját helyszíni IP-címtér is meghatározhat.
- Kettős verem (IPv4 és IPv6-alapú) virtuális hálózatok kettős verem alhálózattal lehetővé teszik az alkalmazások a saját virtuális hálózatához vagy – az interneten IPv4- és IPv6-alapú erőforrásokhoz való kapcsolódás.
- Erőforrások védelme az IPv6-szabályokat a hálózati biztonsági csoportok
- Az IPv6-forgalom a virtuális hálózat a felhasználó által megadott útvonalak - útválasztást, különösen ha a hálózati virtuális berendezések, mivel megvédi a az alkalmazás testreszabása
- IPv6-terheléselosztó támogatási rugalmas, skálázható alkalmazásokat hozhat létre, amely tartalmazza az Azure DNS IPv6-alapú nyilvános IP-címek AAAA típusú rekordok támogatása.
- Egyszerűen hozzáadhat meglévő csak IPv4-alapú üzemelő példányok frissítése – helyi az IPv6-kapcsolatot.
- Hozzon létre kettős verem az alkalmazásokat, amelyek a terhelés virtuálisgép-méretezési csoportok automatikus skálázása.

## <a name="limitations"></a>Korlátozások
Az IPv6-alapú előzetes kiadását az Azure virtuális hálózat a következő korlátozások vonatkoznak:
- IPv6-alapú Azure virtuális hálózat (előzetes verzió) érhető el minden globális Azure-régióban, de csak a globális Azure - nem a kormányzati felhők.   
- A betekintő portál támogatása korlátozódik sok, de nem az összes IPv6-konfigurációhoz, csak a megtekintése, azonban az IPv6-alapú virtuális hálózat rendelkezik teljes körű támogatást és a dokumentáció (a minták) IPv6-alapú üzembe helyezés az Azure Powershell és a parancssori felület (CLI) használatával.
- Network Watcher támogatása az előzetes verzióra korlátozódik NSG-Folyamatnaplók és hálózati csomagrögzítéseket gyűjtsenek.
- Az előzetes betöltés terheléselosztás támogatása kezdetben korlátozódik alapszintű Load Balancert.
- Példányszintű nyilvános IP-címek (nyilvános IP-címek közvetlenül a virtuális gép) nem támogatott az előzetes verzióban érhető el.  
- Virtuális hálózatok közötti társviszony (regionális és globális) nem támogatott előzetes verzióban érhető el. 

## <a name="pricing"></a>Díjszabás

Az IPv6 Azure-erőforrások és a sávszélesség mint IPv4 azonos díjakon számoljuk el. Nem számítunk további vagy eltérő fel díjat, IPv6. A díjszabással kapcsolatos információk [nyilvános IP-címek](https://azure.microsoft.com/pricing/details/ip-addresses/), [hálózati sávszélesség](https://azure.microsoft.com/pricing/details/bandwidth/), vagy [terheléselosztó](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [Azure PowerShell-lel IPv6 kettős verem alkalmazás üzembe helyezése](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Ismerje meg, hogyan [Azure CLI-vel IPv6 kettős verem alkalmazás üzembe helyezése](virtual-network-ipv4-ipv6-dual-stack-cli.md).
