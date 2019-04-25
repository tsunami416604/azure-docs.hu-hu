---
title: Bevezetés az Azure Network Watcher kapcsolati hibáinak elhárítása |} A Microsoft Docs
description: Ez az oldal áttekintést a Network Watcher kapcsolódási hibaelhárítási funkció
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 4b1164c3dedc5d8a2fa02d70f66ff00afe604836
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532471"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Bevezetés a kapcsolatot az Azure Network Watcher hibaelhárítása

A Kapcsolatellenőrzés funkció a Network Watcher lehetővé teszi, hogy közvetlen TCP-kapcsolatot a virtuális gépről egy virtuális gépet (VM), teljesen minősített tartománynevét (FQDN), URI-t, ellenőrizze vagy IPv4-címet. Hálózati forgatókönyvek bonyolult, vannak megvalósítva a hálózati biztonsági csoportok, tűzfalak, felhasználó által megadott útvonalak és az Azure által biztosított erőforrásokat használ. Összetett konfigurációkkal ellenőrizze kihívást hibaelhárítási kapcsolódási problémák. A Network Watcher segítségével csökkentheti a kapcsolati hibáinak keresési és észlelési idejét. Adatsorban visszaadott e platform vagy felhasználó konfigurációs probléma okozza-e a kapcsolódási probléma kapcsolatos információkat is tartalmaznak. Kapcsolat ellenőrizni kell a [PowerShell](network-watcher-connectivity-powershell.md), [Azure CLI-vel](network-watcher-connectivity-cli.md), és [REST API-val](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Kapcsolódási hibák elhárítása megköveteli, hogy rendelkezik-e a virtuális gép a hibaelhárítás a `AzureNetworkWatcherExtension` Virtuálisgép-bővítmény telepítve van. A bővítmény telepítését egy Windows virtuális gépen látogasson el [Azure Network Watcher-ügynök virtuálisgép-bővítmény Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és Linux rendszerű virtuális gép látogasson el a [Azure Network Watcher-ügynök virtuálisgép-bővítmény Linuxhoz](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A bővítmény nem szükséges a cél-végponton.

## <a name="response"></a>Válasz

Az alábbi táblázatban láthatók a Tulajdonságok visszaadott Ha kapcsolódási hibák elhárítása befejezését követően.

|Tulajdonság  |Leírás  |
|---------|---------|
|ConnectionStatus     | A kapcsolat-ellenőrzés állapota. Lehetséges eredmények **elérhető** és **nem elérhető**.        |
|AvgLatencyInMs     | Átlagos késés (MS) a kapcsolat ellenőrzése során. (Csak akkor jelenik meg ha a jelölőnégyzet állapotának érhető el)        |
|MinLatencyInMs     | Minimális késés (MS) a kapcsolat ellenőrzése során. (Csak akkor jelenik meg ha a jelölőnégyzet állapotának érhető el)        |
|MaxLatencyInMs     | Maximális késés (MS) a kapcsolat ellenőrzése során. (Csak akkor jelenik meg ha a jelölőnégyzet állapotának érhető el)        |
|ProbesSent     | Az ellenőrzés során küldött mintavételek száma. Maximális érték pedig a 100.        |
|ProbesFailed     | Az ellenőrzés során sikertelen mintavételek száma. Maximális érték pedig a 100.        |
|Ugrások     | Ugrás Ugrás elérési út a forrás célhelyre.        |
|Hops[].Type     | Az erőforrás típusát. Lehetséges értékek a következők **forrás**, **VirtualAppliance**, **VnetLocal**, és **Internet**.        |
|Hops[].Id | Az Ugrás egyedi azonosítója.|
|Hops[].Address | Az Ugrás IP-címe.|
|Hops[].ResourceId | Egy Azure-erőforrás esetén az Ugrás az Ugrás az erőforrás-azonosító. Ha egy internet-erőforrást, az erőforrás-azonosító értéke **Internet**. |
|Hops[].NextHopIds | A következő ugrás végrehajtott egyedi azonosítója.|
|[] Ugrások. Problémák | Az adott ugrástól ellenőrzése során felmerült problémákat gyűjteménye. Ha nincsenek problémák vannak, az érték üres.|
|Hops[].Issues[].Origin | Jelenleg az aktuális ugrások, ahol probléma lépett fel. Lehetséges értékek:<br/> **Bejövő** -probléma van az a jelenlegi Ugrás az előző Ugrás történő hivatkozás<br/>**Kimenő** -probléma van az a következő Ugrás az aktuális ugrások történő hivatkozás<br/>**Helyi** -probléma van a jelenlegi Ugrás.|
|Hops[].Issues[].Severity | A probléma súlyosságát. Lehetséges értékek a következők **hiba** és **figyelmeztetés**. |
|[] Ugrások. [] Problémák. Típusa |A probléma található típusát. Lehetséges értékek: <br/>**CPU**<br/>**Memória**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hops[].Issues[].Context |Talált a probléma részletei.|
|Hops[].Issues[].Context[].key |A kulcs-érték pár kulcsát adja vissza.|
|Hops[].Issues[].Context[].value |A kulcs-érték pár értékét adja vissza.|

Az alábbiakban látható egy példa található egy Ugrás a problémát.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Tartalék típusok

Kapcsolat Problématípusok értéket ad vissza hibát a kapcsolat. A következő táblázat felsorolja a jelenlegi tartalék típusú adja vissza.

|Typo  |Leírás  |
|---------|---------|
|CPU     | Magas CPU-kihasználtság.       |
|Memory (Memória)     | Magas memóriakihasználtság.       |
|GuestFirewall     | Forgalom egy virtuális gép tűzfal-konfiguráció miatt le van tiltva.        |
|DNSResolution     | DNS-feloldás sikertelen volt a cél címe.        |
|NetworkSecurityRule    | Egy NSG-szabály blokkolja a forgalmat (szabály visszaadott)        |
|UserDefinedRoute|Forgalomkiesés a felhasználó által megadott vagy a rendszer útvonal miatt. |

### <a name="next-steps"></a>További lépések

Megtudhatja, hogyan használó kapcsolatok hibaelhárítása a [az Azure portal](network-watcher-connectivity-portal.md), [PowerShell](network-watcher-connectivity-powershell.md), a [Azure CLI](network-watcher-connectivity-cli.md), vagy [REST API](network-watcher-connectivity-rest.md).