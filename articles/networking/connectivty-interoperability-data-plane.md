---
title: 'Interoperabilitás az Azure-ban : Adatsík-elemzés'
description: Ez a cikk az ExpressRoute, a helyek közötti VPN és az Azure-beli virtuális hálózati társviszony-létesítés közötti együttműködés elemzéséhez használható tesztbeállítás adatsík-elemzését tartalmazza.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: fe7b74b0d4d065d4f222fefbbdc4a1d434d1163b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518265"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Interoperabilitás az Azure-ban : Adatsík-elemzés

Ez a cikk a [tesztbeállítás][Setup]adatsík-elemzését ismerteti. Áttekintheti a [teszt beállítási konfigurációját][Configuration] és a tesztbeállítás [vezérlősík-elemzését][Control-Analysis] is.

Az adatsík-elemzés az egyik helyi hálózatról (LAN-ról vagy virtuális hálózatról) egy topológián belül áthaladó csomagok által megtett útvonalat vizsgálja. A két helyi hálózat közötti adatelérési út nem feltétlenül szimmetrikus. Ezért ebben a cikkben elemezzük a továbbítási útvonalat a helyi hálózatról egy másik hálózatra, amely elkülönül a fordított útvonaltól.

## <a name="data-path-from-the-hub-vnet"></a>Adatelérési út a központi virtuális hálózatról

### <a name="path-to-the-spoke-vnet"></a>A küllővirtuális hálózat elérési útja

Virtuális hálózati (VNet) társviszony-létesítés emulálja a hálózati híd funkcióa két virtuális hálózatok, amelyek társviszony-létesített. Traceroute kimenet egy központi virtuális hálózat egy virtuális gép a küllő virtuális hálózat itt látható:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

Az alábbi ábra a központi virtuális hálózat és a küllővirtuális hálózat grafikus kapcsolatnézetét mutatja be az Azure Network Watcher szemszögéből:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Az ág virtuális hálózatának elérési útja

Traceroute kimenet egy központi virtuális hálózat egy virtuális gép az ág virtuális hálózat itt látható:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Ebben a traceroute-ban az első ugrás a VPN-átjáró az Azure VPN-átjáró a központi virtuális hálózat. A második ugrás a virtuális hálózat ág VPN-átjárója. Az ág virtuális hálózatának VPN-átjárójának IP-címe nem lesz meghirdetve a központi virtuális hálózatban. A harmadik ugrás a virtuális gép az ág virtuális hálózat.

Az alábbi ábra a központi virtuális hálózat és az ágvirtuális hálózat grafikus kapcsolatnézetét mutatja be a Hálózati figyelő szemszögéből:

![2][2]

Ugyanebben a kapcsolatban az alábbi ábra a Hálózati figyelő rácsnézetét mutatja:

![3][3]

### <a name="path-to-on-premises-location-1"></a>A helyszíni helyszín 1-

Traceroute kimenet egy központi virtuális hálózat egy virtuális gép a helyszíni helyen 1 itt látható:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Ebben a traceroute,az első ugrás az Azure ExpressRoute átjáró-csatorna végpont egy Microsoft Enterprise Edge router (MSEE). A második és a harmadik ugrás az ügyfél peremhálózati útválasztója és a helyszíni hely 1 LAN IP-cím. Ezek az IP-címek nem hirdetett a központi virtuális hálózatban. A negyedik ugrás a virtuális gép a helyszíni helyen 1.


### <a name="path-to-on-premises-location-2"></a>A helyszíni hely 2.

Traceroute kimenet egy központi virtuális hálózat egy virtuális gép a helyszíni 2.

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

Ebben a traceroute az első ugrás az ExpressRoute átjáró-alagút végpont egy MSEE. A második és a harmadik ugrás a CE router és a helyszíni Location 2 LANPs. Ezek az IP-címek nem hirdetett a központi virtuális hálózatban. A negyedik ugrás a virtuális gép a helyszíni 2.

### <a name="path-to-the-remote-vnet"></a>A távoli virtuális hálózat elérési útja

Traceroute kimenet egy központi virtuális hálózat egy virtuális gép a távoli virtuális hálózat itt látható:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

Ebben a traceroute az első ugrás az ExpressRoute átjáró-alagút végpont egy MSEE. A második ugrás a távoli virtuális hálózat átjáró IP-címe. A második ugrás IP-tartománya nem hirdetett a hub virtuális hálózat. A harmadik ugrás a távoli virtuális hálózat virtuális gép.

## <a name="data-path-from-the-spoke-vnet"></a>A küllővirtuális hálózat adatelérési útja

A küllős virtuális hálózat megosztja a központi virtuális hálózat hálózati nézetét. A virtuális hálózat társviszony-létesítés, a küllővirtuális hálózat a központi virtuális hálózat távoli átjáró kapcsolatát használja, mintha közvetlenül csatlakozik a küllővirtuális hálózathoz.

### <a name="path-to-the-hub-vnet"></a>A központi virtuális hálózat elérési útja

Traceroute kimenet a küllővirtuális hálózat egy virtuális gép a hub virtuális hálózat itt látható:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Az ág virtuális hálózatának elérési útja

Traceroute kimenet a küllővirtuális hálózat egy virtuális gép az ág virtuális hálózat itt látható:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Ebben a traceroute-ban az első ugrás a központi virtuális hálózat VPN-átjárója. A második ugrás a virtuális hálózat ág VPN-átjárója. Az ág virtuális hálózatának VPN-átjárójának IP-címe nem hirdetett a hub/küllővirtuális virtuális hálózaton belül. A harmadik ugrás a virtuális gép az ág virtuális hálózat.

### <a name="path-to-on-premises-location-1"></a>A helyszíni helyszín 1-

Traceroute kimenet a küllővirtuális hálózat egy virtuális gép a helyszíni helyen 1 itt látható:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Ebben a traceroute,az első ugrás a hub VNet ExpressRoute átjáró-alagút végpont egy MSEE. A második és a harmadik ugrás a CE router és a helyszíni Hely 1 LANPs. Ezek az IP-címek nem hirdetett a hub/küllő virtuális hálózat. A negyedik ugrás a virtuális gép a helyszíni helyen 1.

### <a name="path-to-on-premises-location-2"></a>A helyszíni hely 2.

Traceroute kimenet a küllővirtuális hálózat egy virtuális gép a helyszíni 2.


    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    76 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

Ebben a traceroute,az első ugrás a hub VNet ExpressRoute átjáró-alagút végpont egy MSEE. A második és a harmadik ugrás a CE router és a helyszíni Location 2 LANPs. Ezek az IP-címek nem hirdetett a hub/küllő virtuális hálózatok. A negyedik ugrás a virtuális gép a helyszíni 2.

### <a name="path-to-the-remote-vnet"></a>A távoli virtuális hálózat elérési útja

Traceroute kimenet a küllő virtuális hálózat egy virtuális gép a távoli virtuális hálózat itt látható:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

Ebben a traceroute,az első ugrás a hub VNet ExpressRoute átjáró-alagút végpont egy MSEE. A második ugrás a távoli virtuális hálózat átjáró IP-címe. A második ugrás IP-tartománya nem hirdetett a hub/küllő virtuális hálózat. A harmadik ugrás a távoli virtuális hálózat virtuális gép.

## <a name="data-path-from-the-branch-vnet"></a>Az ág virtuális hálózatának adatelérési útja

### <a name="path-to-the-hub-vnet"></a>A központi virtuális hálózat elérési útja

Traceroute kimenet az ág virtuális hálózat egy virtuális gép a központi virtuális hálózat itt látható:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

Ebben a traceroute az első ugrás a VPN-átjáró az ág virtuális hálózat. A második ugrás a központi virtuális hálózat VPN-átjárója. A központi virtuális hálózat VPN-átjárójának IP-címe nincs meghirdetve a távoli virtuális hálózatban. A harmadik ugrás a virtuális gép a központi virtuális hálózat.

### <a name="path-to-the-spoke-vnet"></a>A küllővirtuális hálózat elérési útja

Traceroute kimenet az ág virtuális hálózat egy virtuális gép a küllő virtuális hálózat itt látható:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

Ebben a traceroute az első ugrás a VPN-átjáró az ág virtuális hálózat. A második ugrás a központi virtuális hálózat VPN-átjárója. A központi virtuális hálózat VPN-átjárójának IP-címe nincs meghirdetve a távoli virtuális hálózatban. A harmadik ugrás a küllővirtuális hálózat virtuális gép.

### <a name="path-to-on-premises-location-1"></a>A helyszíni helyszín 1-

Traceroute kimenet az ág virtuális hálózat egy virtuális gép a helyszíni helyen 1 itt látható:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

Ebben a traceroute az első ugrás a VPN-átjáró az ág virtuális hálózat. A második ugrás a központi virtuális hálózat VPN-átjárója. A központi virtuális hálózat VPN-átjárójának IP-címe nincs meghirdetve a távoli virtuális hálózatban. A harmadik ugrás az elsődleges CE-útválasztó VPN-alagút-végpontja. A negyedik ugrás a helyszíni 1. Ezt a LAN IP-címet a CE-útválasztón kívül nem hirdetik. Az ötödik ugrás a cél virtuális gép a helyszíni helyen 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>A helyszíni 2.

Ahogy azt a vezérlősík elemzésében tárgyaltuk, az ág virtuális hálózata nem rendelkezik láthatósággal sem a helyszíni 2. A következő pingeredmények megerősítik: 

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

## <a name="data-path-from-on-premises-location-1"></a>A helyszíni 1.

### <a name="path-to-the-hub-vnet"></a>A központi virtuális hálózat elérési útja

Traceroute kimenet a helyszíni hely 1 egy virtuális gép a központi virtuális hálózat itt látható:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

Ebben a nyomkövetési útvonalon az első két ugrás a helyszíni hálózat része. A harmadik ugrás az elsődleges MSEE interfész, amely a CE router felé néz. A negyedik ugrás a hub virtuális hálózat ExpressRoute-átjárója. A központi virtuális hálózat ExpressRoute-átjárójának IP-tartománya nincs meghirdetve a helyszíni hálózaton. Az ötödik ugrás a cél virtuális gép.

A Network Watcher csak Azure-központú nézetet biztosít. A helyszíni perspektíva, az Azure Network Performance Monitor. A Network Performance Monitor olyan ügynököket biztosít, amelyeket az Azure-on kívüli hálózatok kiszolgálóira telepíthet az adatútvonal-elemzéshez.

Az alábbi ábra a helyszíni 1.

![4][4]

Ahogy azt korábban már tárgyaltuk, a teszttelepítő egy helyek közötti VPN-t használ az ExpressRoute biztonsági másolataként a helyszíni 1. A biztonsági mentési adatok elérési útjának teszteléséhez váltson ki egy ExpressRoute-kapcsolathibát a helyszíni 1. Az ExpressRoute-kapcsolat meghibásodásának kiváltásához állítsa le az MSEE felé mutató CE-felületet:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

Az alábbi ábra a helyszíni 1.

![5][5]

### <a name="path-to-the-spoke-vnet"></a>A küllővirtuális hálózat elérési útja

Traceroute kimenet a helyszíni hely 1 egy virtuális gép a küllő virtuális hálózat itt látható:

Hozzuk vissza az ExpressRoute elsődleges kapcsolatot, hogy az adatelérési út elemzése felé küllővirtuális hálózat:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Az elsődleges ExpressRoute 1-kapcsolat beállítása az adatelérési út további részéhez.

### <a name="path-to-the-branch-vnet"></a>Az ág virtuális hálózatának elérési útja

Traceroute kimenet a helyszíni hely 1 egy virtuális gép az ág virtuális hálózat itt látható:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>A helyszíni hely 2.

Ahogy azt a [vezérlősík elemzésében][Control-Analysis]tárgyaljuk, az 1. A következő pingeredmények megerősítik: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>A távoli virtuális hálózat elérési útja

Traceroute kimenet a helyszíni hely 1 egy virtuális gép a távoli virtuális hálózat itt látható:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>A helyszíni 2.

### <a name="path-to-the-hub-vnet"></a>A központi virtuális hálózat elérési útja

Traceroute kimenet a helyszíni hely 2 egy virtuális gép a központi virtuális hálózat itt látható:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>A küllővirtuális hálózat elérési útja

Traceroute kimenet a helyszíni hely 2 egy virtuális gép a küllő virtuális hálózat itt látható:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Az ág virtuális hálózatának elérési útja, a helyszíni 1.

Ahogy azt a [vezérlősík elemzés,][Control-Analysis]a helyszíni hely 1 nincs láthatóaz ág virtuális hálózat, a helyszíni hely 1, vagy a távoli virtuális hálózat a hálózati konfiguráció. 

## <a name="data-path-from-the-remote-vnet"></a>Adatelérési út a távoli virtuális hálózatról

### <a name="path-to-the-hub-vnet"></a>A központi virtuális hálózat elérési útja

Traceroute kimenet a távoli virtuális hálózat egy virtuális gép a központi virtuális hálózat itt látható:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>A küllővirtuális hálózat elérési útja

Traceroute kimenet a távoli virtuális hálózat egy virtuális gép a küllő virtuális hálózat itt látható:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Az ág virtuális hálózatának és a helyszíni 2-es helynek az elérési útja

Ahogy azt a [vezérlősík elemzés,][Control-Analysis]a távoli virtuális hálózat nem rendelkezik láthatósággal, hogy az ág virtuális hálózat vagy a helyszíni hely 2 a hálózati konfiguráción ként. 

### <a name="path-to-on-premises-location-1"></a>A helyszíni helyszín 1-

Traceroute kimenet a távoli virtuális hálózat egy virtuális gép a helyszíni helyen 1 itt látható:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute és helyek közötti VPN-kapcsolat párhuzamosan

###  <a name="site-to-site-vpn-over-expressroute"></a>Helyek közötti VPN az ExpressRoute-on keresztül

A helyek közötti VPN konfigurálható az ExpressRoute Microsoft társviszony-létesítésével a helyszíni hálózat és az Azure virtuális hálózatok közötti személyes adatcseréhez. Ezzel a konfigurációval bizalmasan, hitelesen és sértetlenül cserélhet adatokat. Az adatcsere is anti-visszajátszás. A helyek közötti IPsec VPN bújtatási módban az ExpressRoute Microsoft társviszony-létesítésével történő konfigurálásáról további információt a Helyek közötti [VPN expressroute-i Microsoft-társviszony-létesítés című][S2S-Over-ExR]témakörben talál. 

A Microsoft társviszony-létesítést használó helyek közötti VPN konfigurálásának elsődleges korlátozása az átviteli érték. Az IPsec-alagúton keresztüli átviteli kapacitást a VPN-átjáró kapacitása korlátozza. A VPN-átjáró átviteli-ventilátor a ExpressRoute átviteli. Ebben a forgatókönyvben az IPsec-alagút használata a rendkívül biztonságos forgalom és a privát társviszony-létesítés használata az összes többi forgalom segít optimalizálni az ExpressRoute sávszélesség-kihasználtsága.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Helyek közötti VPN az ExpressRoute biztonságos feladatátvételi útvonalaként

ExpressRoute szolgál redundáns áramkör pár magas rendelkezésre állás biztosítása érdekében. Georedundáns ExpressRoute-kapcsolatot konfigurálhat különböző Azure-régiókban. Amint azt a tesztbeállítás is mutatja, egy Azure-régión belül, a helyek közötti VPN segítségével hozzon létre egy feladatátvételi útvonalat az ExpressRoute-kapcsolathoz. Ha ugyanazokat az előtagokat az ExpressRoute és a helyek közötti VPN-en keresztül is meghirdeti, az Azure prioritásként kezeli az ExpressRoute-ot. Az ExpressRoute és a helyek közötti VPN közötti aszimmetrikus útválasztás elkerülése érdekében a helyszíni hálózati konfigurációnak az ExpressRoute-kapcsolat használatával is viszonoznia kell, mielőtt a helyek közötti VPN-kapcsolatot használná.

Az ExpressRoute és a helyek közötti VPN-kapcsolatok együtt létező kapcsolatainak konfigurálásáról az ExpressRoute és a helyek közötti [együttélés][ExR-S2S-CoEx]című témakörben talál további információt.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Háttérkapcsolat kiterjesztése a küllővirtuális hálózatokra és az elágazási helyekre

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Küllős virtuális kapcsolat virtuális hálózattal a virtuális hálózatok társviszony-létesítésével

Hub és küllős virtuális hálózat architektúra széles körben használják. A hub egy virtuális hálózat az Azure-ban, amely a küllővirtuális hálózatok és a helyszíni hálózat közötti kapcsolat központi pontjaként működik. A küllők olyan virtuális hálózatok, amelyek a hubkal egyenrangúak, és amelyek segítségével elkülönítheti a számítási feladatokat. A helyszíni adatközpont és a hub közötti forgalom expresszroute- vagy VPN-kapcsolaton keresztül áramlik. Az architektúráról további információt a [Küllős hálózati topológia megvalósítása az Azure-ban című témakörben talál.][Hub-n-Spoke]

A virtuális hálózatok egy régión belüli társviszony-létesítés, küllővirtuális hálózatok segítségével hub virtuális hálózatok (VPN és ExpressRoute átjárók) a távoli hálózatokkal való kommunikációhoz.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Branch VNet-kapcsolat helyek közötti VPN-kapcsolat használatával

Előfordulhat, hogy a különböző régiókban található és a helyszíni hálózatokban lévő fiókvirtuális hálózatokat egy hub virtuális hálózaton keresztül szeretné kommunikálni egymással. A konfiguráció natív Azure-megoldása a helyek közötti VPN-kapcsolat VPN használatával. Egy másik lehetőség egy hálózati virtuális berendezés (NVA) használata a hubban történő útválasztáshoz.

További információ: [Mi a VPN-átjáró?][VPN] [Deploy a highly available NVA][Deploy-NVA]


## <a name="next-steps"></a>További lépések

Lásd az [ExpressRoute gyIK-et:][ExR-FAQ]
-   Ismerje meg, hogy hány ExpressRoute-kapcsolatcsoport csatlakozhat egy ExpressRoute-átjáróhoz.
-   Ismerje meg, hogy hány ExpressRoute-átjárócsatlakozhat egy ExpressRoute-kapcsolathoz.
-   További információ az ExpressRoute egyéb méretezési korlátairól.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "A hálózati figyelő nézete a hub virtuális hálózatáról a küllővirtuális hálózattal való kapcsolatról"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "A hálózati figyelő nézete a hub virtuális hálózatáról egy fiókvirtuális hálózatra"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "A hálózati figyelő rácsnézete a hub virtuális hálózatáról egy fiókvirtuális hálózatra való kapcsolatról"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Az 1.virtuális gép helye és a hub virtuális hálózata közötti kapcsolat hálózati teljesítményfigyelő nézete az ExpressRoute 1-en keresztül"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Az 1. hely virtuális gépéről a hub virtuális hálózatára irányuló kapcsolat hálózati teljesítményfigyelő nézete egy helyek közötti VPN-en keresztül"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
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


