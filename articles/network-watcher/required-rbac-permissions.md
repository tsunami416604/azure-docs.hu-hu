---
title: Azure hálózati figyelőt lehetőségek szükséges engedélyek |} Microsoft Docs
description: Ismerje meg, hogy melyik Azure szerepköralapú hozzáférés-vezérlési engedélyek szükségesek a hálózati figyelőt szolgáltatásával együtt használhatók.
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
ms.openlocfilehash: 09f3a1e1d9c6796cb55ae8f0ab18bf8e1b3fa198
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077877"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Szerepköralapú hozzáférés-vezérlési engedélyek hálózati figyelőt képességek használatához szükséges

Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy csak a konkrét műveletek hozzárendelni, amely hozzárendelt feladataik elvégzéséhez szükséges a szervezet tagjaira. Hálózati figyelőt képességek használatához jelentkezzen be a Azure, a fiókot hozzá kell rendelni a [tulajdonos](/role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [közreműködő](/role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), vagy [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) beépített szerepkörök vagy hozzárendelt egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) rendelt minden hálózati figyelőt funkció a következő szakaszok a felsorolt műveleteket. Hálózati figyelőt képességeivel kapcsolatos további tudnivalókért lásd: [Mi az hálózati figyelőt?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | A hálózati figyelőt beolvasása                                          |
| Microsoft.Network/networkWatchers/write                             | Létrehozni vagy frissíteni a hálózati figyelőt                             |
| Microsoft.Network/networkWatchers/delete                            | A hálózati figyelőt törlése                                       |

## <a name="nsg-flow-logs"></a>NSG folyamata naplók

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | A folyamat napló konfigurálása                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Az Attribútumfolyam naplók lekérdezés állapota                                    |

## <a name="connection-troubleshoot"></a>Csatlakozási hibáinak elhárítása

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Egy kapcsolat a lekérdezési eredmények teszt hibáinak elhárítása                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Futtassa a kapcsolat tesztelése hibáinak elhárítása                             |

## <a name="connection-monitor"></a>Kapcsolat-figyelő

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Egy kapcsolat figyelő elindítása                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Állítsa le a kapcsolat figyelője                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | A lekérdezés egy kapcsolat figyelője                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Egy kapcsolat figyelő beolvasása                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Egy kapcsolat figyelő létrehozása                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Figyelő kapcsolat törlése                                    |

## <a name="packet-capture"></a>Csomagrögzítés

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | A csomagrögzítéssel állapotának lekérdezése                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | A csomagrögzítéssel leállítása                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | A csomagrögzítéssel beolvasása                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | a csomagrögzítéssel létrehozása                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | A csomagrögzítéssel törlése                                        |

## <a name="ip-flow-verify"></a>IP-adatfolyam ellenőrzése

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Ellenőrizze az IP-folyamat                                              |

## <a name="next-hop"></a>Következő ugrás

| Műveletek                                                              | Name (Név)                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | A következő ugrás beszerzése egy virtuális gép                                     |

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
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Egy Azure reachability jelentés megtekintése                               |

## <a name="additional-actions"></a>További műveletek

Hálózati figyelő képességeket is szükséges a következő műveleteket:

- Microsoft.Storage/Read
- Microsoft.Authorization/Read
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*