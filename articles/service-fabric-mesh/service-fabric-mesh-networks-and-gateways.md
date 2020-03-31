---
title: Bevezetés az Azure Service Fabric hálózati szolgáltatásába
description: Ismerje meg a hálózatok, átjárók és intelligens forgalomútválasztás a Service Fabric Mesh.
author: dkkapur
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: dc793e2991783cc9b7b46d92fcc8e0267feb529b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75459139"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Bevezetés a hálózatépítésbe a Service Fabric Mesh alkalmazásokban
Ez a cikk ismerteti a különböző típusú terheléselosztók, hogyan átjárók csatlakoztassa a hálózatot az alkalmazások más hálózatokhoz, és hogyan forgalom között irányítják a szolgáltatások az alkalmazásokban.

## <a name="layer-4-vs-layer-7-load-balancers"></a>4. réteg vs. 7.
A terheléselosztás az [OSI modell](https://en.wikipedia.org/wiki/OSI_model) különböző rétegeiben végezhető el a hálózatépítéshez, gyakran a 4.  Általában kétféle terheléselosztó létezik:

- Az L4-es terheléselosztó a hálózati átviteli rétegen dolgozik, amely a csomagok kézbesítésével foglalkozik, tekintet nélkül a csomagok tartalmára. A terheléselosztó csak a csomagfejléceket vizsgálja, így az elosztási feltételek az IP-címekre és portokra korlátozódnak. Például egy ügyfél TCP-kapcsolatot létesít a terheléselosztóval. A terheléselosztó megszakítja a kapcsolatot (közvetlenül a SYN-nek válaszolva), kiválaszt egy háttérrendszert, és új TCP-kapcsolatot hoz létre a háttérrendszerhez (új SYN-t küld). Az L4-es terheléselosztó általában csak az L4 TCP/UDP-kapcsolat vagy -munkamenet szintjén működik. Így a terheléselosztás átirányítja bájt körül, és gondoskodik arról, hogy bájt ugyanabból a munkamenetből a szél ugyanabban a háttérben. Az L4-es terheléselosztó nem ismeri az alkalmazás részleteit a bájtról, amelyet mozog. A bájtok bármilyen alkalmazásprotokoll lehetnek.

- Az L7-es terheléselosztó az alkalmazásrétegen működik, amely az egyes csomagok tartalmával foglalkozik. Megvizsgálja a csomagok tartalmát, mert megérti az olyan protokollokat, mint a HTTP, HTTPS vagy WebSockets. Ez lehetővé teszi a terheléselosztó számára a speciális útválasztás elvégzését. Például egy ügyfél egyetlen HTTP/2 TCP-kapcsolatot létesít a terheléselosztóval. A terheléselosztó ezután két háttérkapcsolat létesítése után folytatja. Amikor az ügyfél két HTTP/2 streamet küld a terheléselosztónak, az egyik streamelése az első háttérrendszerre, a kettő pedig a második háttérrendszerre kerül. Így még a túlnyomómértékben eltérő kérésterheléssel rendelkező multiplexügyfelek is hatékonyan kiegyensúlyozottak lesznek a háttérrendszereken keresztül. 

## <a name="networks-and-gateways"></a>Hálózatok és átjárók
A [Service Fabric erőforrás-modellben](service-fabric-mesh-service-fabric-resources.md)a hálózati erőforrás egy egyedileg telepíthető erőforrás, függetlenül egy alkalmazástól vagy szolgáltatáserőforrástól, amely függőségként hivatkozhat rá. Arra használják, hogy hozzon létre egy hálózatot az alkalmazások, amely nyitott az interneten. A különböző alkalmazásokból származó több szolgáltatás ugyanannak a hálózatnak a része lehet. Ezt a magánhálózatot a Service Fabric hozta létre és kezeli, és nem egy Azure virtuális hálózat (VNET). A virtuális hálózat internetkapcsolatának engedélyezéséhez és letiltásához az alkalmazások dinamikusan hozzáadhatók és eltávolíthatók a hálózati erőforrásból. 

Az átjáró két hálózat áthidalására szolgál. Az átjáró erőforrás olyan [Envoy proxyt](https://www.envoyproxy.io/) telepít, amely L4-es útválasztást biztosít bármely protokollhoz, és L7 útválasztást a speciális HTTP(S) alkalmazások útválasztásához. Az átjáró egy külső hálózatról irányítja a forgalmat a hálózatba, és meghatározza, hogy melyik szolgáltatáshoz irányítsa a forgalmat.  A külső hálózat lehet egy nyílt hálózat (lényegében a nyilvános internet) vagy egy Azure virtuális hálózat, amely lehetővé teszi, hogy csatlakozzon a többi Azure-alkalmazások és erőforrások. 

![Hálózat és átjáró][Image1]

Amikor a hálózati erőforrást a segítségével `ingressConfig`hozza létre, a hálózati erőforráshoz nyilvános IP-cím van hozzárendelve. A nyilvános IP lesz kötve a hálózati erőforrás élettartama.

Mesh alkalmazás létrehozásakor egy meglévő hálózati erőforrásra kell hivatkoznia. Új nyilvános portok is hozzáadhatók, vagy meglévő portok eltávolíthatók a be- ésakozókonfigurációból. A hálózati erőforrás törlése sikertelen lesz, ha egy alkalmazás-erőforrás hivatkozik rá. Az alkalmazás törlésekor a rendszer eltávolítja a hálózati erőforrást.

## <a name="next-steps"></a>További lépések 
Ha többet szeretne megtudni a Service Fabric Mesh-ről, olvassa el az áttekintést:
- [A szolgáltatásháló hálója – áttekintés](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png