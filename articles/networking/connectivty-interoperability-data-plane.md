---
title: 'Az ExpressRoute, a Site-to-site VPN és a virtuális hálózatok közötti Társviszony - együttműködési adatok Adatsík elemzési: Azure Háttérkapcsolathoz együttműködési funkciók |} A Microsoft Docs'
description: Ez az oldal nyújt az adatok adatsík elemzése a teszt beállítása, amely jön létre az együttműködést a ExpressRoute, a Site-to-site VPN és a virtuális hálózatok közötti Társviszony szolgáltatások elemzésére.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: c9f3824b1e0f44338696ba3c2e434d60eee3af8b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947265"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---data-plane-analysis"></a>Együttműködés az ExpressRoute, a Site-to-site VPN és a virtuális hálózatok közötti társviszony-létesítési - adatsík adatelemzés

Ebben a cikkben vegyük végig az adatelemzés adatsík a vizsgálat beállítása. A tesztelési beállítások áttekintéséhez tekintse meg a [teszt telepítő][Setup]. A teszt telepítő konfiguráció részletei áttekintéséhez lásd: [telepítő Tesztkonfiguráció][Configuration]. A teszt beállítása vezérlési sík elemzési áttekintéséhez lásd: [vezérlési sík elemzési][Control-Analysis].

Adatsík adatelemzés megvizsgálja a másikra a topológia egy helyi hálózati (LAN/VNet) áthaladó csomagok által útvonalat. Előfordulhat, hogy két helyi hálózat között, az adatok elérési útja nem feltétlenül szimmetrikus. Ezért ebben a cikkben hozzunk elemezze a továbbítási útvonal a helyi hálózatról egy másikra külön-külön fordított elérési úton található.

##<a name="data-path-from-hub-vnet"></a>Az agyi virtuális hálózat adatok elérési útja

###<a name="path-to-spoke-vnet"></a>Küllő virtuális hálózatok közötti elérési útja

Virtuális hálózatok közötti társviszony emulálja a hálózati híd funkciók között a két virtuális hálózat társviszonyba. A traceroute kimenete egy agyi virtuális hálózat egy virtuális géphez a küllő virtuális hálózat az alábbi listában lévőknek:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

A következő képernyő klipet a grafikus kapcsolat nézet az agyi virtuális hálózat és a küllő virtuális hálózat Azure Network Watcher által bemutatott:


[![1]][1]

###<a name="path-to-branch-vnet"></a>Ág VNet elérési útja

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

A fenti a traceroute az első Ugrás a VPN-Átjáró az agyi virtuális hálózat, a. A második Ugrás végrehajtása a VPN-GW ág virtuális hálózat, amelynek IP-cím nem hirdetménnyel telepítették a központi virtuális hálózaton belül. A harmadik Ugrás a virtuális gép a fiókiroda a virtuális hálózaton.

A következő képernyő klipet az agyi virtuális hálózat és a fiókiroda Azure Network Watcher által bemutatott virtuális hálózatok közötti kapcsolat grafikus nézetét:

[![2]][2]

Következő képernyő klip ugyanazt a kapcsolatot az Azure Network Watcher által bemutatott rácsnézet:

[![3]][3]

###<a name="path-to-on-premises-location-1"></a>A helyszíni helyre – 1 elérési útja

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

A fenti traceroute az első Ugrás az ExpressRoute-Átjáró bújtatás végpontot MSEE. A második és a harmadik Ugrás rendre CE útválasztó és a helyszíni hely 1 helyi hálózati IP-címek, ezen IP-címek nincsenek meghirdetve az agyi virtuális hálózat belül. A negyedik Ugrás a virtuális gép a helyszíni helyre – 1.


###<a name="path-to-on-premises-location-2"></a>A helyszíni helyre – 2 elérési útja

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

A fenti traceroute az első Ugrás az ExpressRoute-Átjáró bújtatás végpontot MSEE. A második és a harmadik Ugrás rendre CE útválasztó és a helyszíni hely 2 helyi hálózati IP-címek, ezen IP-címek nincsenek meghirdetve az agyi virtuális hálózat belül. A negyedik Ugrás a helyszíni helyre – 2. a virtuális gép.

###<a name="path-to-remote-vnet"></a>Távoli virtuális hálózat elérési útja

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

A fenti traceroute az első Ugrás az ExpressRoute-Átjáró bújtatás végpontot MSEE. A második Ugrás végrehajtása a távoli virtuális hálózati Átjáró IP. A második ugrás IP-címtartomány nem a központi virtuális hálózaton belül hirdetménnyel telepítették. A harmadik Ugrás a virtuális Géphez a távoli virtuális hálózaton.

##<a name="data-path-from-spoke-vnet"></a>Adatok elérési útja a küllő virtuális hálózaton

A visszaírási, hogy a küllő virtuális hálózatok közötti megosztása az agyi virtuális hálózat hálózati nézetét. VNet-társviszonyon keresztül a küllő virtuális hálózatok közötti, mintha közvetlenül csatlakozik a küllő virtuális hálózatok közötti használja az agyi virtuális hálózat távoli átjáró csatlakoztatásához.

###<a name="path-to-hub-vnet"></a>Agyi virtuális hálózat elérési útja

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

###<a name="path-to-branch-vnet"></a>Ág VNet elérési útja

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

A fenti a traceroute az első Ugrás a VPN-Átjáró az agyi virtuális hálózat, a. A második Ugrás végrehajtása a VPN-GW ág virtuális hálózat, amelynek IP-cím nem hirdetménnyel telepítették a Hub/küllő virtuális hálózaton belül. A harmadik Ugrás a virtuális gép a fiókiroda a virtuális hálózaton.

###<a name="path-to-on-premises-location-1"></a>A helyszíni helyre – 1 elérési útja

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

A fenti traceroute az első Ugrás az agyi virtuális hálózat ExpressRoute-Átjáró bújtatás végpont egyet msee-hez. A második és a harmadik Ugrás rendre CE útválasztó és a helyszíni hely 1 helyi hálózati IP-címek, ezen IP-címek nincsenek meghirdetve a Hub/küllő virtuális hálózaton belül. A negyedik Ugrás a virtuális gép a helyszíni helyre – 1.

###<a name="path-to-on-premises-location-2"></a>A helyszíni helyre – 2 elérési útja

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

A fenti traceroute az első Ugrás az agyi virtuális hálózat ExpressRoute-Átjáró bújtatás végpont egyet msee-hez. A második és a harmadik Ugrás rendre CE útválasztó és a helyszíni hely 2 helyi hálózati IP-címek, ezen IP-címek nincsenek meghirdetve a Hub/küllő virtuális hálózatokon belüli. A negyedik Ugrás a helyszíni helyre – 2. a virtuális gép.

###<a name="path-to-remote-vnet"></a>Távoli virtuális hálózat elérési útja

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

A fenti traceroute az első Ugrás az agyi virtuális hálózat ExpressRoute-Átjáró bújtatás végpont egyet msee-hez. A második Ugrás végrehajtása a távoli virtuális hálózati Átjáró IP. A második ugrás IP-címtartomány nem a Hub/küllő virtuális hálózaton belül hirdetménnyel telepítették. A harmadik Ugrás a virtuális Géphez a távoli virtuális hálózaton.

##<a name="data-path-from-branch-vnet"></a>Adatok elérési útja a fiókiroda virtuális hálózaton

###<a name="path-to-hub-vnet"></a>Agyi virtuális hálózat elérési útja

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

A fenti traceroute az első Ugrás a fiókiroda virtuális hálózat VPN-GW. A második Ugrás a VPN-Átjáró az agyi virtuális hálózat, amelynek IP-cím nem hirdetménnyel telepítették a távoli virtuális hálózaton belül, a. A harmadik Ugrás az agyi virtuális hálózat a virtuális gép.

###<a name="path-to-spoke-vnet"></a>Küllő virtuális hálózatok közötti elérési útja

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

A fenti traceroute az első Ugrás a fiókiroda virtuális hálózat VPN-GW. A második Ugrás végrehajtása a VPN-Átjáró az agyi virtuális hálózat, amelynek IP-cím nincs közzététel a távoli virtuális hálózaton belül, a és a harmadik Ugrás a virtuális Gépet a küllő virtuális hálózat.

###<a name="path-to-on-premises-location-1"></a>A helyszíni helyre – 1 elérési útja

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

A fenti traceroute az első Ugrás a fiókiroda virtuális hálózat VPN-GW. A második Ugrás a VPN-Átjáró az agyi virtuális hálózat, amelynek IP-cím nem hirdetménnyel telepítették a távoli virtuális hálózaton belül, a. A harmadik Ugrás a a virtuális Magánhálózati alagút végpont az elsődleges CE útválasztón. A negyedik Ugrás a helyszíni hely-1 helyi hálózati IP-cím, amely nem CE útválasztón hirdetménnyel telepítették egy belső IP-címét. Az ötödik Ugrás a cél virtuális gép a helyszíni helyre – 1.

###<a name="path-to-on-premises-location-2-and-remote-vnet"></a>A helyszíni hely-2 és a távoli virtuális hálózat elérési útja

A vezérlési sík elemzés előzetes már volt szó, a fiókiroda VNet nem látható-e a helyszíni helyre – 2 vagy a hálózati konfiguráció egy távoli virtuális hálózat rendelkezik. A következő ping eredményeket a tény erősítse meg. 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

##<a name="data-path-from-on-premises-location-1"></a>Adatok elérési útja a helyszíni hely-1

###<a name="path-to-hub-vnet"></a>Agyi virtuális hálózat elérési útja

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

A fenti a traceroute a az első két útválasztók ugrásainak a helyszíni hálózat részét képezik. A harmadik Ugrás az elsődleges MSEE-kapcsolat a CE útválasztó irányuló. A negyedik Ugrás az ExpressRoute az agyi virtuális hálózat, amelynek IP-címtartomány nem a helyszíni hálózathoz hirdetménnyel telepítették, a G/W. Az ötödik Ugrás a cél virtuális gép.

Az Azure Network Watcher csak Azure-központú nézetet nyújt. Ezért a helyszíni központú nézet rendelkezik használtuk az Azure Network Performance monitort (NPM). Az NPM ügynökök telepített kiszolgálók az Azure-on kívülről hálózati és adatútvonal elemzést biztosít.

A következő képernyő klip az agyi virtuális hálózat expressroute-on keresztül a virtuális gép a helyszíni helyre – 1 virtuális gép kapcsolatának topológia megtekintése.

[![4]][4]

Már ismert, a teszt beállítása Site-to-Site VPN használja az expressroute-hoz, a helyszíni hely-1 és az agyi virtuális hálózat között a biztonsági mentési kapcsolat. Vissza datapath teszteléséhez hozzunk idéz elő a helyszíni helyre – 1 elsődleges CE útválasztó és a megfelelő MSEE közötti kapcsolat ExpressRoute hibát állítja le a az MSEE irányuló CE felület.

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

A következő képernyő klip a topológia e nézetében, a virtuális gép a helyszíni helyre – 1 virtuális gép kapcsolatának a Site-to-Site VPN-kapcsolat használatával az agyi virtuális hálózat esetén az ExpressRoute-kapcsolat nem működik.

[![5]][5]

###<a name="path-to-spoke-vnet"></a>Küllő virtuális hálózatok közötti elérési útja

Ossza meg velünk térnek vissza az elsődleges az ExpressRoute-kapcsolat segítségével a datapath elemzéseket végezhet küllő virtuális hálózat felé.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Ossza meg velünk az elsődleges ExpressRoute-1 kapcsolat datapath elemzése a többi vizualizációjára.

###<a name="path-to-branch-vnet"></a>Ág VNet elérési útja

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

###<a name="path-to-on-premises-location-2"></a>A helyszíni helyre – 2 elérési útja

A vezérlési sík elemzés előzetes már volt szó, mint a helyszíni hely 1 rendelkezik nem látható-e a helyszíni hely-2 szerint a hálózati konfiguráció. A következő ping eredményeket a tény erősítse meg. 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

###<a name="path-to-remote-vnet"></a>Távoli virtuális hálózat elérési útja

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

##<a name="data-path-from-on-premises-location-2"></a>Adatok elérési útja a helyi hely – 2

###<a name="path-to-hub-vnet"></a>Agyi virtuális hálózat elérési útja

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Küllő virtuális hálózatok közötti elérési útja

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

###<a name="path-to-branch-vnet-on-premises-location-1-and-remote-vnet"></a>Virtuális hálózat, a helyszíni helyre – 1 és a távoli virtuális hálózat ágban elérési útja

A vezérlési sík elemzés előzetes írtaknak a helyszíni hely 1 ágra virtuális hálózat nem látható, a helyi hely – 1 és a távoli virtuális hálózat / a hálózati konfiguráció. 

##<a name="data-path-from-remote-vnet"></a>A távoli virtuális hálózat adatok elérési útja

###<a name="path-to-hub-vnet"></a>Agyi virtuális hálózat elérési útja

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Küllő virtuális hálózatok közötti elérési útja

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-branch-vnet-and-on-premises-location-2"></a>Ág virtuális hálózat és a helyszíni helyre – 2 elérési útja

A vezérlési sík elemzés előzetes már volt szó, a távoli virtuális hálózat rendelkezik ág virtuális hálózathoz, és a helyszíni helyre – 2 / a hálózati konfiguráció nem látható. 


### <a name="path-to-on-premises-location-1"></a>A helyszíni helyre – 1 elérési útja

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="further-reading"></a>További információ

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute- és helyek közötti VPN-kapcsolat használatával területtel

####<a name="site-to-site-vpn-over-expressroute"></a>Site-to-Site VPN expressroute-on keresztül

Site-to-Site VPN konfigurálható az ExpressRoute Microsoft közvetlenül a Microsoftnak Exchange bizalmas, visszajátszás, hitelességét és integritásának a helyszíni hálózat és az Azure virtuális hálózatok közötti társviszony-létesítésen keresztül. Helyek közötti IPSec VPN konfigurálása bújtatási mód az ExpressRoute a Microsoft társviszony-létesítésen keresztül kapcsolatos további információkért lásd: [Site-to-site VPN ExpressRoute Microsoft-társviszony-létesítésen keresztül][S2S-Over-ExR]. 

A fő szolgáltatásoknak a S2S VPN konfigurálása Microsoft társviszony-létesítésen keresztül az átviteli sebességet. Az IPSec-alagúton keresztül átviteli sebességet a VPN-Átjáró kapacitása korlátozza. A VPN-Átjáró átviteli sebesség, kisebb ExpressRoute átviteli képest. Ilyen esetekben az IPSec-alagút használatával nagy biztonságos forgalmat és a privát társviszony-létesítést az összes többi forgalom segít az ExpressRoute sávszélesség-használat optimalizálása.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-Site VPN biztonságos feladatátvételi útvonalként az expressroute-hoz
Az ExpressRoute kapcsolatcsoport redundáns pár magas rendelkezésre állás biztosítása érdekében érhető el. Konfigurálhatja a georedundáns az ExpressRoute-kapcsolat másik Azure-régióban. Emellett a teszt a telepítés indításához, mint egy adott Azure-régióban, ha feladatátvételi útvonalként az ExpressRoute-kapcsolat használni kívánt megteheti Site-to-Site VPN használatával. Ha ugyanazokat az előtagokat vannak hirdetve az ExpressRoute- és S2S VPN, Azure ExpressRoute S2S VPN-kapcsolaton keresztül részesíti előnyben. Aszimmetrikus útválasztás az ExpressRoute és S2S VPN között elkerülése érdekében a helyszíni hálózati konfiguráció is reciprocate előnyben részesítve – ExpressRoute keresztül S2S VPN-kapcsolat.

Az ExpressRoute és Site-to-Site VPN egyidejű kapcsolatok konfigurálása kapcsolatos további információkért lásd: [ExpressRoute- és helyek közötti együttműködés][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>A küllő virtuális hálózatok és helyek ág Háttérkapcsolathoz kiterjesztése

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Használatával a virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti küllőkapcsolat

Központ-küllő virtuális hálózatok közötti architektúra széles körben használt. A hub egy virtuális hálózatot (VNet) az Azure-ban, amely egy központi csatlakozási pontra van szükség a küllő virtuális hálózatok között, és a helyszíni hálózathoz funkcionál. A küllők az agyhoz, és a számítási feladatok elkülönítésére használhatók virtuális hálózatok. A forgalom a helyszíni adatközpont és a egy expressroute-on vagy VPN-kapcsolaton keresztül a hub között. További architektúrájával kapcsolatos további információkért lásd: [és-Küllős architektúra][Hub-n-Spoke]

Virtuális hálózatok közötti társviszony-létesítés egy adott régión belül lehetővé teszi, hogy a küllő virtuális hálózatok hub virtuális hálózati átjáró (VPN- és ExpressRoute-átjárók) használatával kommunikál távoli hálózatok.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Virtuális hálózatok közötti kapcsolat Site-to-Site VPN használatával ág

Ha azt szeretné, hogy a virtuális hálózatok (különböző régiókban) ágat, és a helyszíni hálózatok egy agyi virtuális hálózat keresztül kommunikálnak egymással, a natív Azure-megoldás a site-to-site VPN-kapcsolat VPN használatával. Egy másik lehetőség, hogy az NVA-útválasztási az agyban.

VPN-átjárók konfigurálásához lásd: [VPN-átjáró konfigurálása][VPN]. Magas rendelkezésre állású NVA telepítése, lásd: [üzembe helyezése magas rendelkezésre állású NVA][Deploy-NVA].

## <a name="next-steps"></a>További lépések

Ismerje meg, hány ExpressRoute-kapcsolatcsoporttal csatlakozhat egy ExpressRoute-átjárót vagy ExpressRoute-átjárók számát egy ExpressRoute-kapcsolatcsoporttal csatlakozhat, vagy más skálázási korlátait expressroute további, lásd: [ExpressRoute – gyakori kérdések][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "agyi virtuális hálózat kapcsolat küllő virtuális hálózat és a network Watcher nézete"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "agyi virtuális hálózat kapcsolat ág virtuális hálózat és a network Watcher nézete"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "agyi virtuális hálózat kapcsolat ág virtuális hálózat és a network Watcher rácsnézet"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "helye – 1 virtuális gép kapcsolat agyi virtuális hálózat ExpressRoute 1 keresztül és a network Performance Monitor nézete"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "helye – 1 virtuális gép kapcsolat S2S VPN-en keresztül agyi virtuális hálózat és a network Performance Monitor nézete"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering




