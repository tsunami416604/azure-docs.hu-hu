---
title: IPv6 és az Azure Load Balancer áttekintése
titlesuffix: Azure Load Balancer
description: IPv6-támogatás az Azure Load Balancer és az elosztott terhelésű virtuális gépek ismertetése.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6-alapú, az azure load balancer, kettős verem, nyilvános IP-cím, natív ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: kumud
ms.openlocfilehash: 894a56c2e51e8fa8a2d72253563d218416ace4cb
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161933"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>IPv6 és az Azure Load Balancer áttekintése


>[!NOTE] 
>Az Azure Load Balancer két különböző típust támogat: alap- és standard szinten. Ez a cikk az Alapszintű Load Balancert ismerteti. A Standard Load Balancer kapcsolatos további információkért lásd: [Standard Load Balancer áttekintése](load-balancer-standard-overview.md).

Internetkapcsolattal rendelkező load Balancer terheléselosztók is üzembe helyezhetők az IPv6-címet. IPv4-kapcsolatot, felül lehetővé teszi a következő képességekkel:

* Natív teljes körű IPv6-kapcsolatot nyilvános internetes ügyfelek és az Azure Virtual Machines (VM-EK) között a terheléselosztón keresztül.
* Natív teljes körű IPv6 kimenő kapcsolat virtuális gépek és a nyilvános interneten IPv6-kompatibilis ügyfelek között.

A következő képen látható az IPv6 működése az Azure Load Balancerhez mutatja be.

![Az Azure Load Balancer konfigurálása IPv6-tal](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Miután üzembe helyezte egy IPv4- vagy IPv6-kompatibilis internetes ügyfél kommunikálhat a nyilvános IPv4 vagy IPv6-cím (vagy a gazdanév) az Azure internetkapcsolattal rendelkező Load Balancer. A load balancer továbbítja az IPv6-csomagokat a magánhálózati IPv6-címek a virtuális gépek hálózati címfordítás (NAT) használatával. Az IPv6-alapú internetes ügyfél nem tud közvetlenül kommunikálni az IPv6-címet a virtuális gépek.

## <a name="features"></a>Szolgáltatások

Az Azure Resource Manageren keresztül üzembe helyezett virtuális gépek natív IPv6 protokoll támogatását biztosítja:

1. Elosztott terhelésű IPv6-alapú szolgáltatások az IPv6-ügyfelek az interneten
2. ("Kettős-ig halmozott") virtuális gépek natív IPv6 és IPv4 végpontok
3. Bejövő és kimenő által kezdeményezett natív IPv6-kapcsolatok
4. Támogatott protokollok, mint például a TCP, UDP és HTTP (S) engedélyezése a szolgáltatásarchitektúrák széles köre

## <a name="benefits"></a>Előnyök

Ez a funkció lehetővé teszi, hogy a fő előnyei a következők:

* Felel meg, hogy új alkalmazások elérhetők lesznek a csak IPv6-alapú ügyfelek igénylő hatósági szabályozás alá eső
* Enable mobil- és Internet of things (IOT) fejlesztők számára, hogy kettős-ig halmozott (IPv4 + IPv6) az Azure Virtual Machines használatával oldja meg az egyre bővülő mobil- és IOT-piacokon

## <a name="details-and-limitations"></a>Részletek és korlátozások

Részletek

* Az Azure DNS szolgáltatással mind az IPv4 és IPv6-alapú AAAA típusú neve rekordokat tartalmaz, és mindkét rögzíti a terheléselosztó fűzi hozzá. Az ügyfél úgy dönt, hogy mely címet (IPv4 vagy IPv6) való kommunikációhoz.
* Amikor egy virtuális Gépet egy nyilvános internetes IPv6-csatlakoztatott eszközök-kapcsolatot kezdeményez, a virtuális gép forrás IPv6-cím az hálózati cím lefordított (NAT) a terheléselosztó a nyilvános IPv6-címére.
* DHCP IPv6-alapú IP-címet kapnak a Linux operációs rendszert futtató virtuális gépeket kell konfigurálni. A Linux-rendszerképeket az Azure katalógusában számos már konfigurálva vannak az IPv6 támogatása módosítás nélkül. További információkért lásd: [DHCPv6 konfigurálása Linux rendszerű virtuális gépekhez](load-balancer-ipv6-for-linux.md)
* Ha a terheléselosztó egy állapotminta használata mellett dönt, egy IPv4-mintavételt hozzon létre, és vele az IPv4- és IPv6-végponttal rendelkező. Ha a virtuális Gépen a szolgáltatás leáll, az IPv4- és IPv6-végpontok vannak a rotációból.

Korlátozások

* IPv6-alapú terheléselosztási szabályok nem adhat hozzá az Azure Portalon. A szabályok csak a sablon, CLI, a PowerShell segítségével lehet létrehozni.
* Előfordulhat, hogy nem frissít a meglévő virtuális gép IPv6-címek használata. Új virtuális gépeket kell telepítenie.
* Egyetlen IPv6-címet minden virtuális gép egyetlen hálózati adapter is hozzárendelhető.
* Nyilvános IPv6-címek nem rendelhetők a virtuális géphez. Is csak rendelni egy terheléselosztó.
* A fordított irányú DNS-címkeresés nem konfigurálhatja a nyilvános IPv6-címek esetén.
* Az IPv6-címekkel rendelkező virtuális gépek nem lehetnek tagjai az Azure-Felhőszolgáltatásban. Akkor csatlakozhat egy Azure virtuális hálózaton (VNet), és IPv4-címeik keresztül kommunikálnak egymással.
* Magánhálózati IPv6-címekhez egy erőforráscsoportba tartozó egyes virtuális gépekhez is telepíthetők, de nem helyezhető üzembe helyezzen egy erőforráscsoportban méretezési csoportok segítségével.
* Az Azure virtuális gépek más virtuális gépeket, más Azure-szolgáltatások vagy a helyszíni eszközök IPv6 protokollon keresztül nem tud kapcsolódni. Ezek csak kommunikálhat az Azure load balancer IPv6 protokollon keresztül. Azonban, amellyel kommunikálhat a IPv4 használata a többi erőforrás.
* Kettős vermű (IPv4 + IPv6) központi telepítések IPv4 védelmét a hálózati biztonsági csoport (NSG) használata támogatott. Az NSG-k nem vonatkoznak az IPv6-os végpontjaiig.
* A virtuális gépen az IPv6 végpont nem érintkező közvetlenül az interneten. Egy terheléselosztó mögött van. Csak azokat a portokat, a load balancer-szabályok megadott IPv6 protokollon keresztül érhetők el.
* IPv6-os protokoll az IdleTimeout paraméter módosítása **jelenleg nem támogatott**. Az alapértelmezett érték négy percet.
* IPv6-os protokoll loadDistributionMethod paraméter módosítása **jelenleg nem támogatott**.
* IPv6-alapú IP-címek fenntartott (ahol címekre statikus =) vannak **jelenleg nem támogatott**.
* NAT64 (fordítás, IPv4, IPv6) nem támogatott.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan helyezhet üzembe egy terheléselosztó konfigurálása IPv6-tal.

* [IPv6-alapú régiónkénti elérhetőségét](https://go.microsoft.com/fwlink/?linkid=828357)
* [A terheléselosztó üzembe helyezéséhez és az IPv6-sablon használatával](load-balancer-ipv6-internet-template.md)
* [A terheléselosztó üzembe helyezéséhez és az IPv6 Azure PowerShell-lel](load-balancer-ipv6-internet-ps.md)
* [A terheléselosztó üzembe helyezéséhez és az IPv6 Azure CLI használatával](load-balancer-ipv6-internet-cli.md)
