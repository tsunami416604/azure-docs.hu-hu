---
title: Az IPv6 használatának áttekintése Azure Load Balancer
titlesuffix: Azure Load Balancer
description: A Azure Load Balancer és a terheléselosztást használó virtuális gépek IPv6-támogatásának ismertetése.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6-alapú, az azure load balancer, kettős verem, nyilvános IP-cím, natív ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: b276766d69c187e2268f5896f23e3bd435ed63c3
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274825"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Az IPv6 használatának áttekintése Azure Load Balancer


>[!NOTE] 
>Azure Load Balancer két különböző típust támogat: alap- és standard szinten. Ez a cikk az Alapszintű Load Balancert ismerteti. További információ a standard Load Balancerről: [standard Load Balancer áttekintése](load-balancer-standard-overview.md).

Az internetre irányuló terheléselosztó IPv6-cím használatával telepíthető. Az IPv4-kapcsolaton kívül ez a következő képességeket teszi lehetővé:

* Natív, végpontok közötti IPv6-kapcsolat a nyilvános internetes ügyfelek és az Azure-Virtual Machines (VM-EK) között a terheléselosztó használatával.
* Natív, végpontok közötti IPv6 kimenő kapcsolat a virtuális gépek és a nyilvános internet IPv6-kompatibilis ügyfelek között.

Az alábbi ábrán a Azure Load Balancer IPv6-funkcióit mutatjuk be.

![Azure Load Balancer IPv6-tal](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Az üzembe helyezést követően egy IPv4-vagy IPv6-kompatibilis internetes ügyfél képes kommunikálni az Azure internetre irányuló Load Balancer nyilvános IPv4-vagy IPv6-címeivel (vagy a gazdagépekkel). A terheléselosztó a hálózati címfordítás (NAT) használatával irányítja az IPv6-csomagokat a virtuális gépek magánhálózati IPv6-címeire. Az IPv6-alapú internetes ügyfél nem tud közvetlenül kommunikálni a virtuális gépek IPv6-címeként.

## <a name="features"></a>Szolgáltatások

A Azure Resource Manager által üzembe helyezett virtuális gépek natív IPv6-támogatása a következőket biztosítja:

1. Elosztott terhelésű IPv6-szolgáltatások IPv6-ügyfelek számára az interneten
2. Natív IPv6-és IPv4-végpontok a virtuális gépeken ("kettős halmozott")
3. Bejövő és kimenő irányú natív IPv6-kapcsolatok
4. A támogatott protokollok, például a TCP, az UDP és a HTTP (S) teljes körű szolgáltatási architektúrát tesznek lehetővé

## <a name="benefits"></a>Előnyök

Ez a funkció a következő fő előnyöket biztosítja:

* Ismerje meg, hogy az új alkalmazások csak az IPv6-alapú ügyfelek számára legyenek elérhetők
* A mobil-és IOT-fejlesztők számára lehetővé teszi a kéthalmozott (IPv4 + IPv6) Azure Virtual Machines használatát a növekvő mobil & IOT-piacok kezeléséhez

## <a name="details-and-limitations"></a>Részletek és korlátozások

Részletek

* A Azure DNS szolgáltatás az IPv4 és az IPv6 AAAA név rekordokat is tartalmazza, és a terheléselosztó mindkét rekordjával válaszol. Az ügyfél kiválasztja, hogy melyik címnek (IPv4 vagy IPv6) kell kommunikálnia.
* Amikor egy virtuális gép kapcsolatot kezdeményez egy nyilvános internet IPv6-kapcsolattal rendelkező eszközzel, a virtuális gép forrásának IPv6-címe hálózati címfordítás (NAT) a terheléselosztó nyilvános IPv6-címeként.
* A Linux operációs rendszert futtató virtuális gépeket úgy kell konfigurálni, hogy DHCP-n keresztül fogadjon IPv6 IP-címet. Az Azure Gallery számos Linux-lemezképe már konfigurálva van az IPv6 támogatására módosítás nélkül. További információ: a [DHCPv6 konfigurálása Linux rendszerű virtuális gépekhez](load-balancer-ipv6-for-linux.md)
* Ha úgy dönt, hogy a terheléselosztó használatával állapot-mintavételt használ, hozzon létre egy IPv4-mintavételt, és használja az IPv4-és IPv6-végpontokkal. Ha a virtuális gépen lévő szolgáltatás leáll, az IPv4-és IPv6-végpontok is elvesznek a rotációból.

Korlátozások

* A Azure Portal nem adhat hozzá IPv6-terheléselosztási szabályokat. A szabályok csak a sablon, a CLI, a PowerShell segítségével hozhatók létre.
* A meglévő virtuális gépeket nem lehet IPv6-címek használatára frissíteni. Új virtuális gépeket kell telepítenie.
* Egyetlen IPv6-cím is rendelhető egyetlen hálózati adapterhez minden virtuális gépen.
* A nyilvános IPv6-címeket nem lehet virtuális géphez rendelni. Csak a terheléselosztó számára rendelhetők hozzá.
* A fordított DNS-címkeresés nem konfigurálható a nyilvános IPv6-címekhez.
* Az IPv6-címmel rendelkező virtuális gépek nem lehetnek az Azure Cloud Service tagjai. Csatlakozhatnak egy Azure-Virtual Networkhoz (VNet), és kommunikálhatnak egymással az IPv4-címeken keresztül.
* A magánhálózati IPv6-címek egy erőforráscsoport egyes virtuális gépei számára is üzembe helyezhetők, de a méretezési csoportokon keresztül nem helyezhetők üzembe az erőforráscsoporthoz.
* Az Azure-beli virtuális gépek nem tudnak IPv6-kapcsolaton keresztül csatlakozni más virtuális gépekhez, egyéb Azure-szolgáltatásokhoz vagy helyszíni eszközökhöz. Az Azure Load balancert csak IPv6-kapcsolaton keresztül lehet kommunikálni. Az IPv4 használatával azonban ezekkel a más erőforrásokkal is kommunikálhatnak.
* Az IPv4-hez készült hálózati biztonsági csoport (NSG) védelme kettős veremben (IPv4 és IPv6) üzemelő példányokban támogatott. A NSG nem alkalmazhatók az IPv6-végpontokra.
* A virtuális gépen lévő IPv6-végpont nem érhető el közvetlenül az internetre. A terheléselosztó mögött található. Csak a terheléselosztó szabályaiban megadott portok érhetők el IPv6-kapcsolaton keresztül.
* Az IPv6 IdleTimeout-paraméterének módosítása **jelenleg nem támogatott**. Az alapértelmezett érték négy perc.
* Az IPv6 loadDistributionMethod-paraméterének módosítása **jelenleg nem támogatott**.
* A fenntartott IPv6 IP-címek (ahol a IP = statikus) **jelenleg nem támogatottak**.
* A NAT64 (IPv6-ról IPv4-re való fordítás) nem támogatott.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan helyezhet üzembe egy Load balancert IPv6-tal.

* [IPv6-hozzáférés régiónként](https://go.microsoft.com/fwlink/?linkid=828357)
* [Terheléselosztó üzembe helyezése IPv6-sablonnal sablon használatával](load-balancer-ipv6-internet-template.md)
* [Terheléselosztó üzembe helyezése IPv6 használatával Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Terheléselosztó üzembe helyezése IPv6 használatával az Azure CLI-vel](load-balancer-ipv6-internet-cli.md)
