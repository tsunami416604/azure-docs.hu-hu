---
title: Bevezetés az Azure Network Watcher connection troubleshoot hibáiba | Microsoft dokumentumok
description: Ez a lap áttekintést nyújt a Hálózatfigyelő kapcsolatával kapcsolatos hibaelhárítási képességről
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: cae3072a3468b232e95d7c1949948b71059695ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283276"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Bevezetés a kapcsolatokkal kapcsolatos hibaelhárításba az Azure Network Watcherben

A Network Watcher kapcsolathibaelhárítási szolgáltatása lehetővé teszi a virtuális gép és a virtuális gép, a teljesen minősített tartománynév (FQDN), az URI vagy az IPv4-cím közvetlen TCP-kapcsolatának ellenőrzését. A hálózati forgatókönyvek összetettek, hálózati biztonsági csoportok, tűzfalak, felhasználó által definiált útvonalak és az Azure által biztosított erőforrások használatával valósulnak meg. Az összetett konfigurációk kihívást jelentenek a kapcsolódási problémák elhárításában. A Network Watcher segít csökkenteni a kapcsolódási problémák keresésére és észlelésére szolgáló időt. A visszaadott eredmények betekintést nyújthatnak abba, hogy egy kapcsolódási probléma platform vagy felhasználói konfigurációs probléma miatt. A kapcsolat ellenőrizhető a [PowerShell,](network-watcher-connectivity-powershell.md) [az Azure CLI](network-watcher-connectivity-cli.md)és a [REST API segítségével.](network-watcher-connectivity-rest.md)

> [!IMPORTANT]
> A kapcsolathiba elhárításához szükség van `AzureNetworkWatcherExtension` arra, hogy a virtuális gép, amelyről hibaelhárítási, telepítve van a virtuális gép bővítménye. A bővítmény Windows virtuális gépen történő telepítéséhez látogasson el az [Azure Network Watcher Agent windowsos](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és Linuxos virtuális gépbővítménybe, és látogasson el az [Azure Network Watcher Agent linuxos virtuálisgép-bővítménybe.](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) A bővítmény nem szükséges a célvégponton.

## <a name="response"></a>Válasz

Az alábbi táblázat a kapcsolathibaelhárítás futtatásakor visszaadott tulajdonságokat mutatja be.

|Tulajdonság  |Leírás  |
|---------|---------|
|ConnectionStatus kapcsolat állapota     | A kapcsolat-ellenőrzés állapota. A lehetséges eredmények **elérhetők** és **nem érhető el.**        |
|AvgLatencyInM-ek     | Átlagos késés a kapcsolat ellenőrzése során ezredmásodpercben. (Csak akkor jelenik meg, ha az ellenőrzés állapota elérhető)        |
|MinLatencyInM-ek     | Minimális késés a kapcsolat ellenőrzése során ezredmásodpercben. (Csak akkor jelenik meg, ha az ellenőrzés állapota elérhető)        |
|MaxLatencyInM-ek     | Maximális késés a kapcsolat ellenőrzése során ezredmásodpercben. (Csak akkor jelenik meg, ha az ellenőrzés állapota elérhető)        |
|ProbesSent (Küldetés: mintavétel)     | Az ellenőrzés során küldött szondák száma. A maximális érték 100.        |
|MintavételekSikertelen     | Az ellenőrzés során meghibásodott mintavételek száma. A maximális érték 100.        |
|Ugrások     | Ugrás ugrási útvonal a forrástól a célig.        |
|Komló[]. Típus     | Az erőforrás típusa. A lehetséges értékek a **következők: Forrás**, **VirtualAppliance**, **VnetLocal**és **Internet**.        |
|Komló[]. Id | Az ugrás egyedi azonosítója|
|Komló[]. Cím | Az ugrás IP-címe.|
|Komló[]. Erőforrásazonosító | Erőforrás-azonosító, ha az ugrás egy Azure-erőforrás. Ha ez egy internetes erőforrás, ResourceID **az internet**. |
|Komló[]. NextHopIds | A következő felvett ugrás egyedi azonosítója.|
|Komló[]. Kérdések | Az adott ugrásnál az ellenőrzés során észlelt problémák gyűjteménye. Ha nem voltak problémák, az érték üres.|
|Komló[]. Problémák[]. Eredetű | Az aktuális ugrásnál, ahol probléma merült fel. Lehetséges értékek:<br/> **Bejövő** – A kiadás az előző ugrás és az aktuális ugrás közötti kapcsolaton található<br/>**Kimenő** – A kiadás az aktuális ugrásés a következő ugrás közötti kapcsolaton található<br/>**Helyi** – a kiadás az aktuális ugráson van.|
|Komló[]. Problémák[]. Súlyossága | A feltárt probléma súlyossága. A lehetséges értékek a **Következők: Hiba** és **Figyelmeztetés**. |
|Komló[]. Problémák[]. Típus |A talált probléma típusa. Lehetséges értékek: <br/>**Cpu**<br/>**Memory (Memória)**<br/>**Vendégtűzfal**<br/>**DnsFelbontás**<br/>**NetworkSecurityRule (Hálózati biztonsági szabály)**<br/>**UserDefinedRoute** |
|Komló[]. Problémák[]. Összefüggésben |Részletek a probléma található.|
|Komló[]. Problémák[]. Környezet[].kulcs |A visszaadott kulcsérték-pár kulcsa.|
|Komló[]. Problémák[]. Környezet[].érték |A visszaadott kulcsérték-pár értéke.|

Az alábbi példa egy ugrással kapcsolatos problémát mutat be.

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
## <a name="fault-types"></a>Hibatípusok

A kapcsolathiba elhárítása hibatípusokat ad vissza a kapcsolatról. Az alábbi táblázat a visszaadott aktuális hibatípusok listáját tartalmazza.

|Típus  |Leírás  |
|---------|---------|
|CPU     | Magas CPU-kihasználtság.       |
|Memory (Memória)     | Nagy memória kihasználtság.       |
|Vendégtűzfal     | A forgalom a virtuális gép tűzfalának konfigurációja miatt blokkolva van.        |
|DNS-feloldás     | A DNS-feloldás nem sikerült a célcímhez.        |
|NetworkSecurityRule (Hálózati biztonsági szabály)    | A forgalmat egy NSG-szabály blokkolja (a szabályt visszaadja)        |
|UserDefinedRoute|A rendszer egy felhasználó által definiált vagy rendszerútvonal miatt elesik a forgalom. |

### <a name="next-steps"></a>További lépések

Megtudhatja, hogyan háríthatja el a kapcsolatok hibáit az [Azure Portalon,](network-watcher-connectivity-portal.md)a [PowerShellen,](network-watcher-connectivity-powershell.md)az [Azure CLI-n](network-watcher-connectivity-cli.md)vagy a [REST API-n](network-watcher-connectivity-rest.md)keresztül.