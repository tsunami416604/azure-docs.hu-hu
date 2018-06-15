---
title: IPv6-alapú, az Azure Load Balancer áttekintése |} Microsoft Docs
description: IPv6-támogatás az Azure terheléselosztó és a virtuális gépek elosztott terhelésű ismertetése.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
keywords: IPv6-alapú, azure load balancer, kettős verem, nyilvános IP-cím, natív ipv6, mobil, iot
ms.assetid: 6a1d583f-a305-40fd-a94b-fa42e1943bbb
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 9622ad4922aa98efe093e7f809a490a8797eb1fd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30189664"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>IPv6-alapú, az Azure Load Balancer áttekintése


>[!NOTE] 
>Az Azure Load Balancer két különböző típust támogat: Alapszintű és Standard. Ez a cikk az Alapszintű Load Balancert ismerteti. Standard terheléselosztó kapcsolatos további információkért lásd: [szabványos Load Balancer áttekintése](load-balancer-standard-overview.md).

Az Internet felé néző terheléselosztók üzembe helyezhetők IPv6-címet. IPv4-kapcsolatot, valamint lehetővé teszi a következő lehetőségeket:

* Natív végpont IPv6-kapcsolatot nyilvános internetes ügyfelek és az Azure virtuális gépek (VM) között a terheléselosztó keresztül.
* Natív végpont IPv6-alapú kimenő kapcsolatok virtuális gépek és a nyilvános Internet IPv6-kompatibilis ügyfelek között.

A következő kép bemutatja, az IPv6 működése az Azure Load Balancer.

![Azure Load Balancer IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Amennyiben telepített, egy IPv4- vagy IPv6-kompatibilis internetes ügyfél tud kommunikálni a nyilvános IPv4- vagy IPv6-címek (vagy állomásnevek) Azure Internet felé néző terheléselosztó. A terheléselosztó az IPv6-csomagokat a saját IPv6-címek a virtuális gépek használata a hálózati címfordítás (NAT) továbbítja. Az IPv6-alapú internetes ügyfél nem tud közvetlenül kommunikálni az IPv6-címet a virtuális gépek.

## <a name="features"></a>Szolgáltatások

Azure Resource Manager használatával telepített virtuális gépek natív IPv6-támogatást biztosít:

1. IPv6-alapú szolgáltatások terhelésű az IPv6-ügyfelek az interneten
2. Natív IPv6 és IPv4-alapú végpontok ("kettős halmozott") virtuális gépeken
3. Bejövő és kimenő által kezdeményezett natív IPv6-kapcsolatok
4. Támogatott protokollok-pl.: TCP, UDP és HTTP (S) engedélyezése számos különféle szolgáltatás architektúrák

## <a name="benefits"></a>Előnyök

Ez a funkció lehetővé teszi, hogy a fő előnyei a következők:

* Felel meg, hogy új alkalmazások számára elérhető, hogy csak IPv6-alapú ügyfelek igénylő kormányzati szabályzat
* Enable mobil és az eszközök internetes hálózata (IOT) fejlesztők kettős halmozott (IPv4 + IPv6) Azure virtuális gépek használata a növekvő mobile & IOT piacok megoldására

## <a name="details-and-limitations"></a>Részletek és korlátozások

Részletek

* Az Azure DNS szolgáltatást tartalmaz, mind az IPv4 és IPv6 AAAA neve rekordok, és válaszol, a terheléselosztóhoz tartozó bejegyzéseket. Az ügyfél úgy dönt, hogy mely címet (IPv4 vagy IPv6) való kommunikációhoz.
* Ha egy virtuális Gépet egy nyilvános Internet IPv6-csatlakozóval csatlakoztatott eszközök kapcsolatot kezdeményez, a virtuális gép forrás IPv6-cím, hálózati cím lefordított (NAT) a terheléselosztó a nyilvános IPv6-címére.
* A Linux operációs rendszert futtató virtuális gépek kell állítani a DHCP IPv6-alapú IP-címét. A Linux-lemezképek, az Azure katalógusában számos már konfigurált IPv6-alapú módosítás nélkül támogatja. További információkért lásd: [DHCPv6 konfigurálása Linux virtuális gépekhez](load-balancer-ipv6-for-linux.md)
* Ha egy állapotmintáihoz használni a terheléselosztóhoz, egy IPv4-alapú mintavétel létrehozása, és az IPv4 és IPv6-végpontok együtt használja azt. Ha a virtuális gépre a szolgáltatás leáll, az IPv4 és IPv6-végpontok kiveszik elforgatás.

Korlátozások

* Nem adható hozzá az IPv6 terheléselosztási szabályok az Azure portálon. A szabályok csak a sablon, CLI-t, a PowerShell segítségével hozhatók létre.
* Előfordulhat, hogy nem frissít meglévő virtuális gépek IPv6-címek használatát. Új virtuális gépeket kell telepítenie.
* Minden virtuális gép egyetlen hálózati adapter egy IPv6-cím is hozzárendelhető.
* A nyilvános IPv6-címek egy virtuális Gépet nem lehet hozzárendelni. Is csak rendelni egy terheléselosztót.
* A DNS-címkeresés a nyilvános IPv6-címek nem konfigurálható.
* Az IPv6-címeket a virtuális gépek nem lehetnek tagjai az Azure-Felhőszolgáltatásban. Akkor lehet csatlakoztatni az Azure Virtual Network (VNet), és az IPv4-címét keresztül kommunikálnak egymással.
* Saját IPv6-címek egy erőforráscsoportot az egyes virtuális gépek telepíthetők, de nem telepíthető az egy erőforráscsoport keresztül méretezési készlet.
* Azure virtuális gépek más virtuális gépek, más Azure-szolgáltatások vagy a helyszíni eszközök IPv6-kapcsolaton keresztül nem tud csatlakozni. Csak kommunikálhatnak az Azure load balancer IPv6-kapcsolaton keresztül. Azonban amelyekkel kommunikálhatnak IPv4 használatával ezek más erőforrások.
* Hálózati biztonsági csoport (NSG) a védelem IPv4 protokoll kettős verem (IPv4 + IPv6) telepítések esetén támogatott. Az NSG-k nem vonatkoznak az IPv6-végpontot.
* Az IPv6-végpont a virtuális gép nincs kitéve közvetlenül az interneten. Egy terheléselosztó mögött van. Csak azokat a portokat a terheléselosztási szabály megadott IPv6-kapcsolaton keresztül érhetők el.
* Az IPv6 az megváltoztatása a IdleTimeout paraméter **jelenleg nem támogatott**. Az alapértelmezett érték négy perc.
* Az IPv6 az megváltoztatása a loadDistributionMethod paraméter **jelenleg nem támogatott**.
* IPv6-alapú IP-címek fenntartott (ahol IP = static) vannak **jelenleg nem támogatott**.

## <a name="next-steps"></a>További lépések

A terheléselosztó IPv6 telepítésének megismerése.

* [IPv6 régiónként rendelkezésre állása](https://go.microsoft.com/fwlink/?linkid=828357)
* [Központi telepítése egy terheléselosztó IPv6-sablon használatával](load-balancer-ipv6-internet-template.md)
* [A terheléselosztó és az IPv6 az Azure PowerShell telepítése](load-balancer-ipv6-internet-ps.md)
* [Egy Azure CLI-vel rendelkező IPv6-alapú terheléselosztó telepítése](load-balancer-ipv6-internet-cli.md)
