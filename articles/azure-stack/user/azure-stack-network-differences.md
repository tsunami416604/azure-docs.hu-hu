---
title: "Az Azure verem hálózati: Különbségek és szempontok"
description: "Információ különbségek és szempontok az Azure-készletben hálózatkezelés használatakor."
services: azure-stack
keywords: 
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 02/28/2018
ms.topic: article
ms.service: azure-stack
ms.openlocfilehash: 4c881a5f5e64ddc9fc67060208f3bef6ae0f5028
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="considerations-for-azure-stack-networking"></a>Azure verem hálózati szempontjai

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Hálózatkezelés Azure verem biztosít számos funkció tisztában kell lennie ahhoz, hogy telepítsen eltérések az Azure-ban található.


Ez a cikk áttekintést a hálózat és a szolgáltatások, Azure verem egyedi szempontjai. Azure verem és az Azure közötti magas szintű különbségek kapcsolatos további tudnivalókért lásd: a [szempontok kulcs](azure-stack-considerations.md) témakör.


## <a name="cheat-sheet-networking-differences"></a>Lap cheat: hálózat különbségek

|Szolgáltatás | Szolgáltatás | Azure (global) | Azure Stack |
| --- | --- | --- | --- |
| DNS | Több-bérlős DNS | Támogatott| Még nem támogatott.|
| |DNS AAAA records|Támogatott|Nem támogatott|
| |DNS-zónák előfizetésenként|100 (alapértelmezett)<br>A kérelem növelhető.|100|
| |DNS-rekord beállítja az egyes zónák|5000 (alapértelmezett)<br>A kérelem növelhető.|5000|
||Name servers for zone delegation|Azure négy névkiszolgálók létrehozott felhasználó (bérlő) zónákkal adja meg.|Azure verem két névkiszolgálók biztosít minden felhasználó (bérlő) zónához, amely jön létre.|
| Virtuális hálózat|Társviszony létesítése virtuális hálózatok között|Ugyanabban a régióban két virtuális hálózatok csatlakoztatása az Azure gerincét a hálózaton keresztül.|Még nem támogatott.|
| |Az IPv6-címek|Az IPv6-címek részeként rendelhet a [hálózati konfigurációja](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions).|Kizárólag az IPv4 használata támogatott.|
|VPN-átjárók|Pont – hely típusú VPN-átjáró|Támogatott|Még nem támogatott.|
| |Vnet – Vnet átjáró|Támogatott|Még nem támogatott.|
| |VPN Gateway SKU-n|Alapszintű, GW1, GW2, GW3, Standard szintű, nagy teljesítményű, igen nagy teljesítményű támogatása. |Alapszintű, Standard és nagy teljesítményű termékváltozat támogatása.|
|Terheléselosztó|IPv6 nyilvános IP-címek|IPv6 nyilvános IP-cím hozzárendelése egy terhelés-kiegyenlítő támogatása.|Kizárólag az IPv4 használata támogatott.|
|Network Watcher|Hálózati figyelő bérlői hálózati figyelési képességek|Támogatott|Még nem támogatott.|
|Tartalomkézbesítési hálózat (CDN)|Content Delivery Network profilok|Támogatott|Még nem támogatott.|
|Alkalmazásátjáró|Terheléselosztási réteg-7|Támogatott|Még nem támogatott.|
|Traffic Manager|Bejövő forgalom útvonal optimális alkalmazások teljesítményéről és megbízhatóságáról.|Támogatott|Még nem támogatott.|
|Express Route|Állítsa be a gyors, személyes kapcsolatot a helyszíni infrastruktúra vagy a közös elhelyezést eszközökhöz a Microsoft más felhőszolgáltatásaival.|Támogatott|Csatlakozás Azure verem az Express Route-körhöz támogatása.|

## <a name="next-steps"></a>További lépések

[DNS az Azure Stackben](azure-stack-dns.md)
