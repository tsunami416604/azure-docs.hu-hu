---
title: 'Együttműködés az Azure háttér-kapcsolati funkciók: Adatok elemzése adatsík |} A Microsoft Docs'
description: Ez a cikk az adatok adatsík elemzése a teszt beállítása segítségével elemezheti az együttműködés között ExpressRoute, a site-to-site VPN és a virtuális hálózati társviszony az Azure-ban.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 5e648c1f1b051d7b65d9b007a69aece2d99d9df4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176182"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Együttműködés az Azure háttér-kapcsolati funkciók: Adatsík adatelemzés

Ez a cikk ismerteti az adatok adatsík a [beállítások ellenőrzése][Setup]. Is a [telepítő tesztkonfiguráció] [ Configuration] és a [szabályozhatja az adatsík elemzési] [ Control-Analysis] a vizsgálat beállítása.

Adatsík adatelemzés megvizsgálja a másikra a topológia egy helyi hálózati (LAN vagy a virtuális hálózat) áthaladó csomagok által útvonalat. Két helyi hálózat között, az adatok elérési útja nem feltétlenül szimmetrikus. Ezért ez a cikk elemezzük a továbbítási útvonal helyi hálózaton egy másik hálózathoz, amely nem azonos a fordított elérési.

## <a name="data-path-from-the-hub-vnet"></a>Az agyi virtuális hálózat az adatok elérési útja

### <a name="path-to-the-spoke-vnet"></a>A küllő virtuális hálózatok közötti elérési útja

Virtuális hálózatok (VNet) közötti társviszony emulálja a hálózati híd funkciókat a két virtuális hálózat társviszonyba között. A traceroute kimenete egy agyi virtuális hálózat, a küllő virtuális hálózatok közötti Itt láthatók a virtuális géphez:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

A következő ábrán látható, a grafikus kapcsolat nézete az agyi virtuális hálózat és a küllő virtuális hálózat Azure Network Watcher szempontjából:


[![1]][1]

### <a name="path-to-the-branch-vnet"></a>A virtuális hálózatok közötti ág elérési útja

A traceroute kimenetének egy agyi virtuális hálózat egy virtuális géphez az ág VNet itt jelenik meg:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

A traceroute az első Ugrás az agyi virtuális hálózat az Azure VPN Gateway a VPN-átjáróhoz. A második Ugrás a virtuális hálózatok közötti ág a VPN-átjáró. Az ág virtuális hálózatok közötti VPN-átjáró IP-címét az agyi virtuális hálózat nincs meghirdetve. A harmadik Ugrás a virtuális Gépet a virtuális hálózatok közötti ág a rendszer.

A következő ábrán látható, a grafikus kapcsolat nézete az agyi virtuális hálózat és az ág VNet szempontjából a Network Watcher:

[![2]][2]

Ugyanazt a kapcsolatot a rácsnézet az alábbi ábra a Network Watcher látható:

[![3]][3]

### <a name="path-to-on-premises-location-1"></a>1. a helyszíni hely elérési útja

A traceroute kimenete egy agyi virtuális hálózat egy virtuális géphez a helyszíni hely 1 az itt látható:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

A traceroute a az első Ugrás az Azure ExpressRoute átjáró bújtatás végpontjához, a Microsoft vállalati peremhálózati útválasztó (MSEE) a. A második és harmadik útválasztók ugrásainak a következők: az ügyfél (CE) peremhálózati útválasztója és a helyszíni hely 1 helyi hálózati IP-címek. Ezen IP-címek nincsenek meghirdetve az agyi virtuális hálózat. A negyedik Ugrás a helyszíni hely 1. a virtuális gép.


### <a name="path-to-on-premises-location-2"></a>2. a helyszíni hely elérési útja

A traceroute kimenete egy agyi virtuális hálózat egy virtuális géphez a helyszíni hely 2 az itt látható:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

Az első Ugrás a traceroute a ExpressRoute átjáró bújtatás végpontot, hogy egy MSEE. A második és harmadik útválasztók ugrásainak a CE útválasztó és a helyszíni hely 2 helyi hálózati IP-címek. Ezen IP-címek nincsenek meghirdetve az agyi virtuális hálózat. A negyedik Ugrás a virtuális Gépet, a 2. a helyszíni hely.

### <a name="path-to-the-remote-vnet"></a>A távoli virtuális hálózat elérési útja

A traceroute kimenete egy agyi virtuális hálózat egy virtuális géphez a távoli virtuális hálózat az itt látható:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

Az első Ugrás a traceroute a ExpressRoute átjáró bújtatás végpontot, hogy egy MSEE. A második Ugrás végrehajtása a távoli virtuális hálózati átjáró IP. A második ugrás IP-címtartományt az agyi virtuális hálózat nincs meghirdetve. A harmadik Ugrás a virtuális Géphez a távoli virtuális hálózaton.

## <a name="data-path-from-the-spoke-vnet"></a>A küllő virtuális hálózatok közötti származó adatok elérési útja

A küllő virtuális hálózatok közötti megosztja az agyi virtuális hálózat hálózati nézetét. VNet-társviszonyon keresztül a küllő virtuális hálózatok közötti, mintha közvetlenül csatlakozik a küllő virtuális hálózatok közötti használja az agyi virtuális hálózat távoli átjáró csatlakoztatásához.

### <a name="path-to-the-hub-vnet"></a>Az agyi virtuális hálózat elérési útja

A traceroute a küllő virtuális hálózatok közötti kimenete egy virtuális géphez az agyi virtuális hálózat itt jelenik meg:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>A virtuális hálózatok közötti ág elérési útja

A traceroute a küllő virtuális hálózatok közötti kimenete egy virtuális géphez az ág VNet itt jelenik meg:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Az első Ugrás a traceroute a VPN-átjárót, az agyi virtuális hálózat. A második Ugrás a virtuális hálózatok közötti ág a VPN-átjáró. Az ág virtuális hálózatok közötti VPN-átjáró IP-címét a hub/küllő virtuális hálózatok közötti belül nem hirdetni. A harmadik Ugrás a virtuális Gépet a virtuális hálózatok közötti ág a rendszer.

### <a name="path-to-on-premises-location-1"></a>1. a helyszíni hely elérési útja

A traceroute kimenete a küllő virtuális hálózat egy virtuális géphez a helyszíni hely 1 az itt látható:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

A traceroute a az első Ugrás az agyi virtuális hálózat ExpressRoute átjáró alagút végpont egy egyet msee-hez. A második és harmadik útválasztók ugrásainak a CE útválasztó és a helyszíni hely 1 helyi hálózati IP-címek. Ezen IP-címek nem hirdetésre a hub/küllő virtuális hálózat. A negyedik Ugrás a helyszíni hely 1. a virtuális gép.

### <a name="path-to-on-premises-location-2"></a>2. a helyszíni hely elérési útja

A traceroute kimenete a küllő virtuális hálózat egy virtuális géphez, a helyszíni hely 2 itt jelenik meg:


    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

A traceroute a az első Ugrás az agyi virtuális hálózat ExpressRoute átjáró alagút végpont egy egyet msee-hez. A második és harmadik útválasztók ugrásainak a CE útválasztó és a helyszíni hely 2 helyi hálózati IP-címek. Ezen IP-címek nem hirdetésre a hub/küllő virtuális hálózatokhoz. A negyedik Ugrás a helyszíni hely 2 virtuális gép.

### <a name="path-to-the-remote-vnet"></a>A távoli virtuális hálózat elérési útja

A traceroute kimenete a küllő virtuális hálózat egy virtuális géphez a távoli virtuális hálózat az itt látható:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

A traceroute a az első Ugrás az agyi virtuális hálózat ExpressRoute átjáró alagút végpont egy egyet msee-hez. A második Ugrás végrehajtása a távoli virtuális hálózati átjáró IP. A második ugrás IP-címtartomány a hub/küllő virtuális hálózat nincs meghirdetve. A harmadik Ugrás a virtuális Géphez a távoli virtuális hálózaton.

## <a name="data-path-from-the-branch-vnet"></a>A virtuális hálózatok közötti ágról adatok elérési útja

### <a name="path-to-the-hub-vnet"></a>Az agyi virtuális hálózat elérési útja

A traceroute a fiókiroda VNet kimenete egy virtuális géphez az agyi virtuális hálózat itt jelenik meg:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

Az első Ugrás a traceroute a VPN-átjáró a virtuális hálózatok közötti ág. A második Ugrás végrehajtása a az agyi virtuális hálózat VPN-átjáróra. Az agyi virtuális hálózat, a VPN-átjáró IP-címét a távoli virtuális hálózat nincs meghirdetve. A harmadik Ugrás az agyi virtuális hálózat a virtuális gép.

### <a name="path-to-the-spoke-vnet"></a>A küllő virtuális hálózatok közötti elérési útja

A traceroute a küllő virtuális hálózatok közötti Itt láthatók a virtuális géphez a virtuális hálózatok közötti ágról kimeneti:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

Az első Ugrás a traceroute a VPN-átjáró a virtuális hálózatok közötti ág. A második Ugrás végrehajtása a az agyi virtuális hálózat VPN-átjáróra. Az agyi virtuális hálózat, a VPN-átjáró IP-címét a távoli virtuális hálózat nincs meghirdetve. A harmadik Ugrás a küllő virtuális hálózat a virtuális gép.

### <a name="path-to-on-premises-location-1"></a>1. a helyszíni hely elérési útja

A traceroute kimenete az ág virtuális hálózat egy virtuális géphez, a helyszíni hely 1 itt jelenik meg:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

Az első Ugrás a traceroute a VPN-átjáró a virtuális hálózatok közötti ág. A második Ugrás végrehajtása a az agyi virtuális hálózat VPN-átjáróra. Az agyi virtuális hálózat, a VPN-átjáró IP-címét a távoli virtuális hálózat nincs meghirdetve. A harmadik Ugrás a a virtuális Magánhálózati alagút végpont az elsődleges CE útválasztón. A negyedik Ugrás a helyszíni hely 1 egy belső IP-címét. A helyi hálózati IP-cím a CE útválasztó kívül nincs meghirdetve. Az ötödik Ugrás a cél virtuális gép a helyszíni helyre az 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>A helyszíni hely 2 és a távoli virtuális hálózat elérési útja

Beszéltünk a vezérlési sík elemzés, a fiókiroda virtuális hálózat rendelkezik nem látható-e a helyszíni hely 2 vagy a távoli virtuális hálózat / a hálózati konfigurációt. Erősítse meg a következő ping eredmények: 

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

## <a name="data-path-from-on-premises-location-1"></a>A helyszíni hely 1 adatok elérési útja

### <a name="path-to-the-hub-vnet"></a>Az agyi virtuális hálózat elérési útja

A traceroute a helyszíni hely 1 kimenete egy virtuális géphez az agyi virtuális hálózat itt jelenik meg:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

A traceroute a az első két útválasztók ugrásainak a a helyszíni hálózat részei. A harmadik Ugrás a az elsődleges MSEE-kapcsolat, amely a CE útválasztó arcokat. A negyedik Ugrás a az agyi virtuális hálózat az ExpressRoute-átjárót. Az agyi virtuális hálózat az ExpressRoute-átjárót, az IP-címtartományt a helyszíni hálózathoz nincs meghirdetve. Az ötödik Ugrás a cél virtuális gép.

A Network Watcher csak egy Azure-központú nézetet nyújt. Egy helyszíni perspektíva használ az Azure Network Performance Monitor. A Network Performance Monitor található adatok elérési út elemzése kívül az Azure-hálózatok kiszolgálókon telepíthető ügynökök biztosít.

A következő ábrán látható, a helyszíni hely 1 virtuális gépek kapcsolatai a topológia e nézetében az agyi virtuális hálózat expressroute-on keresztül a virtuális géphez:

[![4]][4]

Ahogy arra már korábban, a teszt beállítása a helyszíni hely 1 és az agyi virtuális hálózat között ExpressRoute biztonsági mentési kapcsolat site-to-site VPN használja. Ha tesztelni szeretné az adatok biztonsági másolatának elérési útja, nézzük idéz elő a helyszíni hely 1 elsődleges CE útválasztó és a megfelelő MSEE közötti kapcsolat ExpressRoute hibát. Az ExpressRoute-kapcsolat hibát idéz elő, állítsa le a az MSEE arcokat CE-kapcsolat:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

A következő ábrán látható, a virtuális Gépet a site-to-site VPN-kapcsolat használatával az agyi virtuális hálózat a topológia e nézetében a a helyszíni hely 1 virtuális gép hálózati kapcsolat, ha az ExpressRoute-kapcsolat nem működik:

[![5]][5]

### <a name="path-to-the-spoke-vnet"></a>A küllő virtuális hálózatok közötti elérési útja

A traceroute a küllő virtuális hálózatok közötti Itt láthatók a virtuális géphez a helyszíni hely 1 kimenete:

Nézzük térnek vissza az elsődleges az ExpressRoute-kapcsolat segítségével az adatok elérési útja elemzéseket végezhet a küllő virtuális hálózat felé:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Nyissa meg az elsődleges ExpressRoute 1 kapcsolat az adatok elérési út elemzése további részében.

### <a name="path-to-the-branch-vnet"></a>A virtuális hálózatok közötti ág elérési útja

A traceroute a helyszíni hely 1 kimenete egy virtuális géphez az ág VNet itt jelenik meg:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>2. a helyszíni hely elérési útja

A ismertetésén a [szabályozhatja az adatsík elemzési][Control-Analysis], a helyszíni hely 1 rendelkezik a helyszíni hely 2 / a hálózati konfiguráció nem láthatóságát. Erősítse meg a következő ping eredmények: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>A távoli virtuális hálózat elérési útja

A traceroute kimenete a helyszíni hely 1 egy virtuális géphez a távoli virtuális hálózat az itt látható:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>A helyszíni hely 2 adatok elérési útja

### <a name="path-to-the-hub-vnet"></a>Az agyi virtuális hálózat elérési útja

A traceroute a helyszíni hely 2 kimenete egy virtuális géphez az agyi virtuális hálózat itt jelenik meg:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>A küllő virtuális hálózatok közötti elérési útja

A traceroute a küllő virtuális hálózatok közötti Itt láthatók a virtuális géphez a helyszíni hely 2 kimenete:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Elérési útját a virtuális hálózathoz az ág a helyszíni hely 1 és a távoli virtuális hálózat

A ismertetésén a [szabályozhatja az adatsík analysis][Control-Analysis], a helyszíni hely 1 rendelkezik nem látható-e arra az ágára, virtuális hálózat, a helyszíni hely 1-re vagy a hálózati konfiguráció szerint a távoli virtuális hálózathoz. 

## <a name="data-path-from-the-remote-vnet"></a>A távoli virtuális hálózat az adatok elérési útja

### <a name="path-to-the-hub-vnet"></a>Az agyi virtuális hálózat elérési útja

A traceroute a távoli virtuális hálózat kimenete egy virtuális géphez az agyi virtuális hálózat itt jelenik meg:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>A küllő virtuális hálózatok közötti elérési útja

A traceroute a küllő virtuális hálózatok közötti Itt láthatók a virtuális géphez a távoli virtuális hálózat kimenete:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>A virtuális hálózatok közötti ág elérési útja és a helyszíni hely 2

A ismertetésén a [szabályozhatja az adatsík elemzési][Control-Analysis], a távoli virtuális hálózat nem látható-e az ág virtuális hálózat vagy a helyszíni hely 2 / a hálózati konfigurációs van. 

### <a name="path-to-on-premises-location-1"></a>1. a helyszíni hely elérési útja

A traceroute kimenete a távoli virtuális hálózat egy virtuális géphez a helyszíni hely 1 az itt látható:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Területtel ExpressRoute- és helyek közötti VPN-kapcsolat

###  <a name="site-to-site-vpn-over-expressroute"></a>Site-to-site VPN expressroute-on keresztül

A site-to-site VPN ExpressRoute Microsoft társviszony-létesítést úgy, hogy privát módon exchange-adatok a helyszíni hálózat és az Azure virtuális hálózatok közötti használatával konfigurálhatja. Ezzel a konfigurációval adatokat cserélni bizalmas hitelességét és integritását. Adatcsere során a visszajátszás. IPsec helyek közötti VPN konfigurálása bújtatási mód a Microsoft társviszony-létesítési ExpressRoute használatával kapcsolatos további információkért lásd: [Site-to-site VPN ExpressRoute Microsoft társviszony-létesítésen keresztül][S2S-Over-ExR]. 

Az elsődleges korlátozása a Microsoft társviszony-létesítést használó site-to-site VPN konfigurálása az átviteli sebességet. Az IPsec-alagúton keresztül átviteli sebességet a VPN-átjáró kapacitása korlátozza. A VPN gateway teljesítménye is kevesebbet, mint az ExpressRoute átviteli sebességet. Ebben a forgatókönyvben a rendkívül biztonságos forgalmat az IPsec-alagút használatával, és privát társviszony-létesítést az összes többi forgalom használatával segít optimalizálni a ExpressRoute sávszélesség-felhasználás.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-site VPN biztonságos feladatátvételi útvonalként az expressroute-hoz

Az ExpressRoute kapcsolatcsoport redundáns két, magas rendelkezésre állás biztosítása érdekében szolgálja ki. Konfigurálhatja a georedundáns az ExpressRoute-kapcsolat másik Azure-régióban. Is ahogyan az a teszt beállítása egy Azure-régióban is használhatja a site-to-site VPN feladatátvételi útvonalként az ExpressRoute-kapcsolat létrehozása. Ha ugyanazokat az előtagokat vannak hirdetve az ExpressRoute- és a egy site-to-site VPN, az Azure ExpressRoute rangsorolja. Aszimmetrikus útválasztás az ExpressRoute és a site-to-site VPN közötti elkerülése érdekében a helyszíni hálózati konfiguráció is kell reciprocate site-to-site VPN-kapcsolat használata előtt az ExpressRoute-kapcsolat használatával.

Az ExpressRoute és site-to-site VPN egyidejű kapcsolatok konfigurálásával kapcsolatos további információkért lásd: [ExpressRoute és site-to-site párhuzamossági][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Háttér-küllő virtuális hálózatok és a fiókirodában dolgozó csatlakozásának kiterjesztése

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>A virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti küllőkapcsolat

Küllős architektúra virtuális hálózatok közötti szokták gyakran használni. A hub egy Vnetet az Azure-ban, amely egy központi csatlakozási pontra van szükség a küllő virtuális hálózatok között, és a helyszíni hálózathoz funkcionál. A küllők az agyhoz kapcsolódó virtuális hálózatok, és amelyekkel számítási feladatok elkülönítésére. A forgalom a helyszíni adatközpont és a egy expressroute-on vagy VPN-kapcsolaton keresztül a hub között. Az architektúrával kapcsolatos további információkért lásd: [küllős hálózati topológia implementálása az Azure-ban][Hub-n-Spoke].

Virtuális hálózatok közötti társviszony-létesítés egy adott régión belül, a küllő virtuális hálózatok használatával hub virtuális hálózati átjáró (VPN- és ExpressRoute-átjárók) kommunikálni a távoli hálózatokhoz.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Virtuális hálózatok közötti kapcsolat ágban site-to-site VPN használatával

Érdemes lehet virtuális hálózatokat, amelyek a különböző régiókban, és a egy VNet-központon keresztül kommunikálnak egymással a helyszíni hálózatok ág. Ehhez a konfigurációhoz a natív Azure-megoldás közötti VPN használatával site-to-site VPN-kapcsolat. Alternatív, hogy egy hálózati virtuális készüléket (NVA) útválasztás az agyban.

További információkért lásd: [Mi az a VPN-átjáró?] [ VPN] és [üzembe helyezése egy magas rendelkezésre állású nva-t][Deploy-NVA].


## <a name="next-steps"></a>További lépések

Tekintse meg a [ExpressRoute – gyakori kérdések] [ ExR-FAQ] való:
-   Ismerje meg, hány ExpressRoute-kapcsolatcsoporttal csatlakozhat egy ExpressRoute-átjárót.
-   Ismerje meg, hogy hány ExpressRoute-átjáró egy ExpressRoute-kapcsolatcsoporttal csatlakozhat.
-   Ismerje meg a többi skálázási korlátait expressroute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "kapcsolat egy agyi virtuális hálózat egy küllő virtuális hálózat és a network Watcher nézete"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "kapcsolat egy agyi virtuális hálózat virtuális hálózatok közötti ág és a network Watcher nézete"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "kapcsolat egy agyi virtuális hálózat virtuális hálózatok közötti ág és a network Watcher rácsnézet"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "az agyi virtuális hálózat ExpressRoute-1-n keresztül kapcsolódni a hely 1 virtuális gépről a network Performance Monitor nézete"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "network Performance Monitor nézetét a helyre 1 virtuális gép kapcsolat és az agyi virtuális hálózat egy helyek közötti VPN-en keresztül"

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


