---
title: "Műveletek események és teljesítményszámlálók figyeli, hogy az NSG-k |} Microsoft Docs"
description: "Megtudhatja, hogyan számlálók, az események és az NSG-ket műveleti naplózás engedélyezése"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2017
ms.author: jdial
ms.openlocfilehash: 552f37dd704de25159bc0f0ad34fdae9ed8b73f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>Naplóelemzés hálózati biztonsági csoportokhoz

Az NSG-k engedélyezéséhez a következő diagnosztikai naplófájl kategóriák:

* **Esemény:** mely NSG-t a szabályok vonatkoznak a virtuális gépek és -példányt szerepkörök a MAC-címe alapján bejegyzést tartalmaz. Ezek a szabályok állapota 60 másodpercenként gyűjti.
* **A szabály számláló:** Contains bejegyzéseket hányszor minden NSG-szabályok forgalom engedélyezése vagy megtagadása vonatkozik.

> [!NOTE]
> Diagnosztikai naplók esetén csak az Azure Resource Manager deployment használatával telepített NSG-k érhetők el. Az NSG-ket a klasszikus üzembe helyezési modellben telepített diagnosztikai naplózás nem engedélyezhető. Szeretné jobban megismerni a két modell, lásd a [ismertetése Azure üzembe helyezési modellel](../resource-manager-deployment-model.md) cikk.

(Korábbi nevén naplózási és a műveleti naplókat) naplózása alapértelmezés szerint engedélyezve van a az NSG-k vagy az Azure-telepítés modell használatával létrehozott. Annak megállapításához, hogy mely műveletek a műveletnaplóban az NSG-ket befejeződött, keresse meg a következő erőforrás-típusokat tartalmazó bejegyzéseket: 

- Microsoft.ClassicNetwork/networkSecurityGroups 
- Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
- Microsoft.Network/networkSecurityGroups
- Microsoft.Network/networkSecurityGroups/securityRules 

Olvassa el a [az Azure tevékenységnapló áttekintése](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) cikk tevékenységi naplóit tájékozódhat. 

## <a name="enable-diagnostic-logging"></a>Diagnosztikai naplózás engedélyezése

Diagnosztikai naplózás engedélyezni kell a *minden* NSG szeretne adatokat gyűjteni. A [áttekintés az Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) a cikk azt ismerteti, ahol a diagnosztikai naplók küldhető. Ha nem rendelkezik egy meglévő NSG, hajtsa végre a a [hálózati biztonsági csoport létrehozása](virtual-networks-create-nsg-arm-pportal.md) hozhat létre egyet. Diagnosztikai naplózás a következő módszerekkel történő NSG engedélyezéséhez:

### <a name="azure-portal"></a>Azure Portal

A portál segítségével engedélyezi a naplózást, jelentkezzen be a [portal](https://portal.azure.com). Kattintson a **további szolgáltatások**, majd írja be a *hálózati biztonsági csoportok*. Válassza ki a naplózás engedélyezése kívánt NSG. Kövesse az utasításokat, amelyben nem számítási erőforrásokat a [engedélyezése a diagnosztikai naplókat a portálon](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs) cikk. Válassza ki **NetworkSecurityGroupEvent**, **NetworkSecurityGroupRuleCounter**, vagy mindkét kategóriák a naplók.

### <a name="powershell"></a>PowerShell

Naplózás engedélyezése a PowerShell használatával, kövesse a [PowerShell diagnosztikai naplók engedélyezése](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs) cikk. Értékelje ki a cikkben egy parancs megadása előtt az alábbi adatokat:

- Megadhatja a használandó értéket az `-ResourceId` azáltal, hogy a következő paraméter [szöveg] megfelelő, majd írja be a parancs `Get-AzureRmNetworkSecurityGroup -Name [nsg-name] -ResourceGroupName [resource-group-name]`. A parancs kimenetében azonosító hasonlít *következő [előfizetés Id]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG neve]*.
- Ha csak szeretne adatokat gyűjteni a napló kategória hozzáadása `-Categories [category]` a cikkben, ahol a kategóriát vagy a a parancs végéhez *NetworkSecurityGroupEvent* vagy *NetworkSecurityGroupRuleCounter*. Ha nem adja meg a `-Categories` paraméter, adatgyűjtés engedélyezve van, az kategóriák naplófájl.

### <a name="azure-command-line-interface-cli"></a>Azure parancssori felület (CLI)

A parancssori felület használatával naplózását, kövesse a [parancssori felületen keresztül diagnosztikai naplók engedélyezése](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs) cikk. Értékelje ki a cikkben egy parancs megadása előtt az alábbi adatokat:

- Megadhatja a használandó értéket az `-ResourceId` azáltal, hogy a következő paraméter [szöveg] megfelelő, majd írja be a parancs `azure network nsg show [resource-group-name] [nsg-name]`. A parancs kimenetében azonosító hasonlít *következő [előfizetés Id]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG neve]*.
- Ha csak szeretne adatokat gyűjteni a napló kategória hozzáadása `-Categories [category]` a cikkben, ahol a kategóriát vagy a a parancs végéhez *NetworkSecurityGroupEvent* vagy *NetworkSecurityGroupRuleCounter*. Ha nem adja meg a `-Categories` paraméter, adatgyűjtés engedélyezve van, az kategóriák naplófájl.

## <a name="logged-data"></a>A naplózott adatok

JSON-formátumú adatot ír a mindkét naplókhoz. Az egyes napló a megadott adatokat, az alábbiakban szerepel:

### <a name="event-log"></a>Eseménynapló
Ez a napló tartalmaz, mely NSG szabályok vonatkoznak, a virtuális gépek és a felhőalapú szolgáltatás szerepkörpéldányt beállítani, a MAC-címe alapján. A következő példa adatokat az egyes eseményekhez jegyzi fel:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-B791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"3389-3389",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter-log"></a>A szabály számláló napló

Ez a napló minden egyes szabály erőforrásokra alkalmazzák információkat tartalmaz. A következő példa adatokat a rendszer naplózza minden alkalommal, amikor egy szabály alkalmazásakor:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]TESTRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

## <a name="view-and-analyze-logs"></a>Megtekintése és elemzése a naplókat

Tevékenység-naplóadatok megtekintéséhez olvassa el a [áttekintése Azure tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) cikk. Diagnosztikai naplófájl adatainak megtekintése, olvassa el a [áttekintés az Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) cikk. Ha diagnosztikai adatokat küld a Naplóelemzési, használhatja a [Azure hálózati biztonsági csoport analytics](../log-analytics/log-analytics-azure-networking-analytics.md) továbbfejlesztett insights (előzetes verzió) felügyeleti megoldás. 
