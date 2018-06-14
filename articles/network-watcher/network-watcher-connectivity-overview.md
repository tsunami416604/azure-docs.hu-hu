---
title: Bevezetés az Azure-hálózat megfigyelő kapcsolati hibáinak elhárítása |} Microsoft Docs
description: Ezen a lapon a hálózati figyelőt kapcsolat hibaelhárítási funkció áttekintése
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
ms.openlocfilehash: 0268c7e54aa82df12243f98fd72de836fbc82070
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30833999"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Bevezetés a kapcsolati hibáinak elhárítása az Azure hálózati figyelőt

A kapcsolat hibaelhárítása a hálózati figyelőt szolgáltatása lehetővé teszi egy közvetlen TCP-kapcsolatot a virtuális gépről a virtuális gép (VM), teljesen minősített tartománynevét (FQDN), URI, ellenőrizze vagy IPv4-címet. Hálózati forgatókönyvek a következők összetett, végrehajtásuk hálózati biztonsági csoportok, tűzfalak, felhasználó által definiált útvonalak és az Azure által biztosított erőforrásokhoz. Összetett konfigurációk ellenőrizze a hibaelhárítási problémák kihívást. Hálózati figyelőt csökkenthető ennyi idő alatt található, és problémák észlelése. A visszaadott eredmények hogy platformot vagy felhasználó konfigurációs probléma okozza-e a egy hálózati probléma betekintést biztosít. Kapcsolat ellenőrizhetők a [PowerShell](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md), és [REST API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Csatlakozási hibáinak elhárítása az szükséges, hogy a virtuális gép a hibaelhárítás a `AzureNetworkWatcherExtension` telepített virtuális gépi bővítményt. A bővítmény telepítése a Windows virtuális gép a Microsoft [a Windows Azure hálózati figyelő ügynök virtuálisgép-bővítmény](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és a Linux virtuális gép helyezést [Azure hálózati figyelő ügynök virtuálisgép-bővítmény Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A bővítmény nem szükséges a cél-végponthoz.

## <a name="response"></a>Válasz

Az alábbi táblázatban láthatók a Tulajdonságok visszaadott Ha csatlakozási hibáinak elhárítása futása befejeződött.

|Tulajdonság  |Leírás  |
|---------|---------|
|ConnectionStatus     | A kapcsolat ellenőrzése állapota. Lehetséges eredményei **elérhető** és **Unreachable**.        |
|AvgLatencyInMs     | Átlagos késleltetése ezredmásodpercben a kapcsolat ellenőrzése során. (Csak jelenik meg, ha a jelölőnégyzet állapota érhető el)        |
|MinLatencyInMs     | Minimális késésre ezredmásodpercben a kapcsolat ellenőrzése során. (Csak jelenik meg, ha a jelölőnégyzet állapota érhető el)        |
|MaxLatencyInMs     | Maximális késleltetés milliszekundumban a kapcsolat ellenőrzése során. (Csak jelenik meg, ha a jelölőnégyzet állapota érhető el)        |
|ProbesSent     | Az ellenőrzés során küldött mintavételt száma. Maximális érték 100.        |
|ProbesFailed     | Az ellenőrzés sikertelen mintavételek menüpontban száma. Maximális érték 100.        |
|Az ugrásszám     | Ugrás a Ugrás elérési úttal forrás célhelyre.        |
|[] Az útválasztók. Típusa     | Az erőforrás típusát. A lehetséges értékek: **forrás**, **VirtualAppliance**, **VnetLocal**, és **Internet**.        |
|Hops[].Id | Az Ugrás egyedi azonosítója.|
|[] Az útválasztók. Cím | Az Ugrás IP-címe.|
|Hops[].ResourceId | Az Ugrás, ha az Ugrás az Azure erőforrás ResourceID. Ha egy internetes erőforrást, van-e a ResourceID **Internet**. |
|Hops[].NextHopIds | A következő ugrás végrehajtott egyedi azonosítója.|
|[] Az útválasztók. Problémák | Az adott Ugrás a ellenőrzése során felmerült problémákat gyűjteménye. Ha nincs probléma, az érték üres.|
|Hops[].Issues[].Origin | : Az aktuális Ugrás, ahol probléma lépett fel. Lehetséges értékek:<br/> **Bejövő** -probléma van a hivatkozásra kattintva a jelenlegi Ugrás az előző Ugrás a<br/>**Kimenő** -probléma van a következő Ugrás az aktuális Ugrás az alábbi hivatkozásra<br/>**Helyi** -probléma van az aktuális Ugrás.|
|Hops[].Issues[].Severity | Az észlelt probléma súlyossága. A lehetséges értékek: **hiba** és **figyelmeztetés**. |
|Hops[].Issues[].Type |A probléma található típusa. Lehetséges értékek: <br/>**CPU**<br/>**Memória**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hops[].Issues[].Context |A probléma található kapcsolatos részleteket.|
|Hops[].Issues[].Context[].key |A kulcs-érték pár kulcsa vissza.|
|Hops[].Issues[].Context[].value |A kulcs-érték pár értékét adja vissza.|

Egy példa található egy Ugrás a problémát a következő:

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
## <a name="fault-types"></a>Hiba típusa

Kapcsolat hibaelhárítása beolvasása tartalék típusok a kapcsolatról. A következő táblázat felsorolja a típusú aktuális hibát adott vissza.

|Típus  |Leírás  |
|---------|---------|
|CPU     | Magas fokú Processzorhasználatot tapasztalható.       |
|Memory (Memória)     | Magas memóriahasználat.       |
|GuestFirewall     | Forgalom le van tiltva, mert a virtuális gép tűzfal konfigurációja.        |
|DNSResolution     | DNS-feloldás a célcím nem sikerült.        |
|NetworkSecurityRule    | Forgalmát blokkolja egy NSG-szabály (szabály visszaadott)        |
|UserDefinedRoute|Egy felhasználó által definiált vagy rendszerútvonal miatt megszakad a forgalmat. |

### <a name="next-steps"></a>További lépések

Kapcsolatot hibaelhárítása a [Azure-portálon](network-watcher-connectivity-portal.md), [PowerShell](network-watcher-connectivity-powershell.md), a [Azure CLI](network-watcher-connectivity-cli.md), vagy [REST API](network-watcher-connectivity-rest.md).