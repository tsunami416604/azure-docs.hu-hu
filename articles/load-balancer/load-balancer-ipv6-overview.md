---
title: Az IPv6 – Azure terheléselosztó áttekintése
description: Ezzel a tanulási útvonallal ismerkedje meg az Azure Load Balancer iPv6-támogatásával és a terheléselosztással rendelkező virtuális gépek használatával.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azúrkék terheléselosztó, kettős verem, nyilvános ip, natív ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: 6bc7f45d84d525156a3d25bdceef4d1012844afa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931987"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Az IPv6 áttekintése az Azure Load Balancer-hez


>[!NOTE] 
>Ezt a tartalmat az [IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)váltotta fel az Azure virtuális hálózat áttekintéséhez . Az Azure azt javasolja, hogy az új IPv6-telepítések az Azure virtuális hálózatok új IPv6-szolgáltatásait használják.

>[!NOTE]
>Az Azure Load Balancer két különböző típust támogat: Alapszintű és Standard. Ez a cikk az Alapszintű Load Balancert ismerteti. A Standard Load Balancer-ről a [Standard Load Balancer overview című témakörben olvashat bővebben.](load-balancer-standard-overview.md)

Az alapvető termékváltozatok internetes terheléselosztói IPv6-címmel telepíthetők. Az IPv4-kapcsolat mellett ez a következő képességeket teszi lehetővé:

* Natív, végpontok közötti IPv6-kapcsolat a nyilvános internetes ügyfelek és az Azure virtuális gépek (VM-ek) között a terheléselosztón keresztül.
* Natív végpontok közötti IPv6 kimenő kapcsolat a virtuális gépek és a nyilvános internetes IPv6-kompatibilis ügyfelek között.

Az alábbi képen az Azure Load Balancer IPv6-funkciói láthatók.

![Azure terheléselosztó IPv6-tal](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Üzembe helyezés után az IPv4- vagy IPv6-kompatibilis internetes ügyfél kommunikálhat az Azure internetre néző terheléselosztó nyilvános IPv4- vagy IPv6-címével (vagy állomásneveivel). A terheléselosztó a hálózati címfordítás (NAT) segítségével irányítja az IPv6-csomagokat a virtuális gépek privát IPv6-címeihez. Az IPv6 internetes ügyfél nem tud közvetlenül kommunikálni a virtuális gépek IPv6-címével.

## <a name="features"></a>Szolgáltatások

Az Azure Resource Manager en keresztül telepített virtuális gépek natív IPv6-támogatása a következőket biztosítja:

1. Kiegyensúlyozott Terhelésű IPv6-szolgáltatások IPv6-ügyfelek számára az interneten
2. Natív IPv6- és IPv4-végpontok a virtuális gépeken ("kettős halmozott")
3. Bejövő és kimenő natív IPv6-kapcsolatok
4. A támogatott protokollok, például a TCP, az UDP és a HTTP(S) a szolgáltatásarchitektúrák teljes skáláját teszik lehetővé

## <a name="benefits"></a>Előnyök

Ez a funkció a következő kulcsfontosságú előnyöket teszi lehetővé:

* Feleljen meg a kormányzati előírásoknak, amelyek előírják, hogy az új alkalmazások csak iPv6-ügyfelek számára legyenek elérhetők
* Lehetővé teszi a mobil- és a dolgok internete (IOT) fejlesztőiszámára, hogy kéthalmozott (IPv4+IPv6) Azure-os virtuális gépeket használjanak a növekvő mobil & IOT-piacok kezelésére

## <a name="details-and-limitations"></a>Részletek és korlátozások

Részletek

* Az Azure DNS-szolgáltatás iPv4 A és IPv6 AAAA névrekordokat is tartalmaz, és a terheléselosztó mindkét rekordjával válaszol. Az ügyfél kiválasztja, hogy melyik címmel (IPv4 vagy IPv6) kommunikáljon.
* Amikor egy virtuális gép kapcsolatot kezdeményez egy nyilvános internethez csatlakoztatott IPv6-alapú eszközzel, a virtuális gép forrásIPv6-címe hálózati cím (NAT) a terheléselosztó nyilvános IPv6-címére.
* A Linux operációs rendszert futtató virtuális gépeket úgy kell konfigurálni, hogy DHCP-n keresztül iPv6 IP-címet kapjanak. Az Azure-katalógusban található Linux-lemezképek közül sok már konfigurálva van az IPv6 módosítás nélküli támogatására. További információ: [DHCPv6 konfigurálása Linuxos virtuális gépekhez](load-balancer-ipv6-for-linux.md)
* Ha úgy dönt, hogy egy állapotminta a terheléselosztó, hozzon létre egy IPv4-mintavétel, és használja azt az IPv4 és az IPv6 végpontok. Ha a szolgáltatás a virtuális gép leáll, mind az IPv4 és az IPv6-végpontok kikerülnek az elforgatás.

Korlátozások

* Az Azure Portalon nem adhat hozzá IPv6-terheléselosztási szabályokat. A szabályok csak a sablonon, cli- és PowerShell-en keresztül hozhatók létre.
* Előfordulhat, hogy nem frissíti a meglévő virtuális gépeket IPv6-címek használatára. Új virtuális gépeket kell üzembe helyeznie.
* Egyetlen IPv6-cím rendelhető hozzá egyetlen hálózati adapterhez minden virtuális gépben.
* A nyilvános IPv6-címek nem rendelhetők hozzá virtuális géphez. Csak terheléselosztóhoz rendelhetők hozzá.
* A nyilvános IPv6-címek névfeloldási DNS-keresése nem konfigurálható.
* Az IPv6-címekkel rendelkező virtuális gépek nem lehetnek az Azure Felhőszolgáltatás tagjai. Csatlakoztathatók egy Azure virtuális hálózathoz (VNet), és kommunikálhatnak egymással az IPv4-címükön keresztül.
* A privát IPv6-címek egy erőforráscsoport egyes virtuális gépein telepíthetők, de nem telepíthetők erőforráscsoportba a Méretezési csoportokon keresztül.
* Az Azure virtuális gépek nem tudnak csatlakozni az IPv6-on keresztül más virtuális gépekhez, más Azure-szolgáltatásokhoz vagy helyszíni eszközökhöz. Csak az Azure terheléselosztóval kommunikálhatnak az IPv6-on keresztül. Az IPv4 használatával azonban kommunikálhatnak ezekkel az erőforrásokkal.
* Az IPv4 hálózati biztonsági csoport (NSG) védelmét a kétverű (IPv4+IPv6) telepítések támogatják. Az NSG-k nem vonatkoznak az IPv6-végpontokra.
* A virtuális gép IPv6-végpontja nincs közvetlenül elérhető az interneten. Ez mögött a terheléselosztó. Csak a terheléselosztó szabályokban megadott portok érhetők el az IPv6-on keresztül.
* Az IPv6 IDleTimeout paraméterének módosítása **jelenleg nem támogatott.** Az alapértelmezett érték négy perc.
* Az IPv6 loadDistributionMethod paraméterének módosítása **jelenleg nem támogatott.**
* A fenntartott IPv6 IP-címek (ahol az IPAllocationMethod = static) **jelenleg nem támogatottak.**
* A NAT64 (az IPv6 fordítása IPv4-re) nem támogatott.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan helyezhet üzembe terheléselosztót az IPv6-tal.

* [Az IPv6 elérhetősége régiónként](https://go.microsoft.com/fwlink/?linkid=828357)
* [Terheléselosztó üzembe helyezése IPv6-tal sablon használatával](load-balancer-ipv6-internet-template.md)
* [Terheléselosztó üzembe helyezése iPv6-tal az Azure PowerShell használatával](load-balancer-ipv6-internet-ps.md)
* [Terheléselosztó üzembe helyezése IPv6-tal az Azure CLI használatával](load-balancer-ipv6-internet-cli.md)
