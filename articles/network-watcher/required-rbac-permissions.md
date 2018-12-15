---
title: Az Azure Network Watcher képességeinek használatához szükséges engedélyek |} A Microsoft Docs
description: Ismerje meg, mely az Azure szerepköralapú hozzáférés-vezérlési engedélyekkel Network watcherről használata szükséges.
services: network-watcher
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jdial
ms.openlocfilehash: 429f7862901814fbd2017c395706fbfa2c345f72
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434579"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Szerepköralapú hozzáférés-vezérlési engedélyekkel kell használnia a Network watcherről

Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy csak az adott műveletek hozzárendelése hozzárendelt feladataik elvégzéséhez szükséges a szervezet tagjai. A Network watcherről használja, jelentkezzen be Azure-ban, a fiókot hozzá kell rendelni a [tulajdonosa](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), vagy [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) beépített szerepkörök, vagy hozzárendelt egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) rendelt egyes Network Watcher funkció az alábbi szakaszok a felsorolt műveleteket. A Network Watcher képességeivel kapcsolatos további tudnivalókért lásd: [Mi az a Network Watcher?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | A network watcher beolvasása                                          |
| Microsoft.Network/networkWatchers/write                             | Létrehozni vagy frissíteni a network watchert                             |
| Microsoft.Network/networkWatchers/delete                            | A hálózati figyelő törlése                                       |

## <a name="nsg-flow-logs"></a>NSG-Folyamatnaplók

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Konfiguráljon egy folyamatot napló                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Egy flow-log állapotának lekérdezése                                    |

## <a name="connection-troubleshoot"></a>Kapcsolódási hibák elhárítása

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Egy kapcsolat kezdeményezése teszt hibáinak elhárítása
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Lekérdezési eredmények kapcsolat teszt hibáinak elhárítása                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Futtassa a kapcsolat tesztelése hibaelhárítása                             |

## <a name="connection-monitor"></a>A kapcsolatfigyelő

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | A kapcsolatfigyelő indítása                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | A kapcsolatfigyelő leállítása                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Lekérdezés a kapcsolatmonitorok                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Egy kapcsolat figyelő lekérése                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Kapcsolatfigyelő létrehozása                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | A kapcsolatfigyelő törlése                                    |

## <a name="packet-capture"></a>Csomagrögzítés

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Csomagrögzítés állapotának lekérdezése                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Csomagrögzítés leállítása                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Csomagrögzítés beolvasása                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | csomagrögzítés létrehozása                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Csomagrögzítés törlése                                        |

## <a name="ip-flow-verify"></a>IP-forgalom ellenőrzése

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Az IP-forgalom ellenőrzése                                              |

## <a name="next-hop"></a>Következő ugrás

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | A következő ugrás beszerzése egy virtuális gépről                                     |

## <a name="network-security-group-view"></a>Hálózati biztonsági csoport nézet

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Biztonsági csoportok megtekintése                                           |

## <a name="topology"></a>Topológia

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Topológia beolvasása                                                   |

## <a name="reachability-report"></a>Elérhetőség jelentés

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Egy Azure-lemezekről jelentés megtekintése                               |

## <a name="additional-actions"></a>További műveletek

Network Watcher-funkciókkal is szükséges a következő műveleteket:

- Microsoft.Authorization/ \* /olvasási
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachines/extensions/Read
- Microsoft.Compute/virtualMachines/extensions/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Compute/virtualMachineScaleSets/extensions/Read
- Microsoft.Compute/virtualMachineScaleSets/extensions/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*
