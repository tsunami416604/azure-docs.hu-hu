---
title: "Bevezetés a kapcsolat be Azure hálózati figyelőt |} Microsoft Docs"
description: "Ezen a lapon a hálózati figyelőt kapcsolat funkció áttekintése"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 16ceef9c923b6a933a5caf752991b466346e0ebc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-connectivity-check-in-azure-network-watcher"></a>Bevezetés az Azure hálózati figyelőt a kapcsolat ellenőrzése

A hálózati figyelőt kapcsolat szolgáltatása lehetővé teszi egy közvetlen TCP-kapcsolatot a virtuális gépről a virtuális gép (VM), teljesen minősített tartománynevét (FQDN), URI, ellenőrizze vagy IPv4-címet. Hálózati forgatókönyvek a következők összetett, végrehajtásuk hálózati biztonsági csoportok, tűzfalak, felhasználó által definiált útvonalak és az Azure által biztosított erőforrásokhoz. Összetett konfigurációk ellenőrizze a hibaelhárítási problémák kihívást. Hálózati figyelőt csökkenthető ennyi idő alatt található, és problémák észlelése. A visszaadott eredmények hogy platformot vagy felhasználó konfigurációs probléma okozza-e a egy hálózati probléma betekintést biztosít. Kapcsolat ellenőrizhetők a [PowerShell](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md), és [REST API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Kapcsolat ellenőrzése van szükség a virtuálisgép-bővítmény `AzureNetworkWatcherExtension`. A bővítmény telepítése a Windows virtuális gép a Microsoft [a Windows Azure hálózati figyelő ügynök virtuálisgép-bővítmény](../virtual-machines/windows/extensions-nwa.md) és a Linux virtuális gép helyezést [Azure hálózati figyelő ügynök virtuálisgép-bővítmény Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="response"></a>Válasz

Az alábbi táblázat a tulajdonságait, vissza, ha a kapcsolat-ellenőrzés futása befejeződött.

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
|[] Az útválasztók. Azonosítója | Az Ugrás egyedi azonosítója.|
|[] Az útválasztók. Cím | Az Ugrás IP-címe.|
|[] Az útválasztók. ResourceId | Az Ugrás, ha az Ugrás az Azure erőforrás ResourceID. Ha egy internetes erőforrást, van-e a ResourceID **Internet**. |
|[] Az útválasztók. NextHopIds | A következő ugrás végrehajtott egyedi azonosítója.|
|[] Az útválasztók. Problémák | Az adott Ugrás a ellenőrzése során felmerült problémákat gyűjteménye. Ha nincs probléma, az érték üres.|
|[] Az útválasztók. [] Ad ki. Forrás | : Az aktuális Ugrás, ahol probléma lépett fel. Lehetséges értékek:<br/> **Bejövő** -probléma van a hivatkozásra kattintva a jelenlegi Ugrás az előző Ugrás a<br/>**Kimenő** -probléma van a következő Ugrás az aktuális Ugrás az alábbi hivatkozásra<br/>**Helyi** -probléma van az aktuális Ugrás.|
|[] Az útválasztók. [] Ad ki. Súlyosság: | Az észlelt probléma súlyossága. A lehetséges értékek: **hiba** és **figyelmeztetés**. |
|[] Az útválasztók. [] Ad ki. Típusa |A probléma található típusa. Lehetséges értékek: <br/>**PROCESSZOR**<br/>**Memória**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|[] Az útválasztók. [] Ad ki. A környezetben |A probléma található kapcsolatos részleteket.|
|[] Az útválasztók. [] Ad ki. [] .key környezetben |A kulcs-érték pár kulcsa vissza.|
|[] Az útválasztók. [] Ad ki. [] .value környezetben |A kulcs-érték pár értékét adja vissza.|

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

A kapcsolat ellenőrzése a kapcsolatra vonatkozó hiba típusok adja vissza. A következő táblázat felsorolja a típusú aktuális hibát adott vissza.

|Típus  |Leírás  |
|---------|---------|
|CPU     | Magas fokú Processzorhasználatot tapasztalható.       |
|Memory (Memória)     | Magas memóriahasználat.       |
|GuestFirewall     | Forgalom le van tiltva, mert a virtuális gép tűzfal konfigurációja.        |
|DNSResolution     | DNS-feloldás a célcím nem sikerült.        |
|NetworkSecurityRule    | Forgalmát blokkolja egy NSG-szabály (szabály visszaadott)        |
|UserDefinedRoute|Egy felhasználó által definiált vagy rendszerútvonal miatt megszakad a forgalmat. |

### <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan ellátogatva erőforrás ellenőrzésére: [ellenőrizze a kapcsolatot az Azure hálózati figyelőt](network-watcher-connectivity-powershell.md).

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png

