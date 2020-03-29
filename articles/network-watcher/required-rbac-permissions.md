---
title: A képességek használatához szükséges RBAC-engedélyek
titleSuffix: Azure Network Watcher
description: Ismerje meg, hogy milyen Azure szerepköralapú hozzáférés-vezérlési engedélyek szükségesek a Network Watcher képességeinek való munkához.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: damendo
ms.openlocfilehash: f8743f19d6cd262ad140659be55a4fc57e842564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840553"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>A Hálózatfigyelő képességeinek használatához szükséges szerepköralapú hozzáférés-vezérlési engedélyek

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy csak az adott műveleteket rendelje hozzá a szervezet tagjaihoz, amelyek a hozzárendelt feladatok teljesítéséhez szükséges. A Network Watcher képességeinek használatához az Azure-ba bejelentkező fiókot hozzá kell rendelni a [Tulajdonos,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner) [közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)vagy [a Hálózat közreműködője](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) beépített szerepkörhöz, vagy hozzá kell rendelni egy [olyan egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) amely az egyes Hálózati figyelői képességekhez rendelt műveleteket a következő szakaszokban. Ha többet szeretne megtudni a Network Watcher képességeiről, olvassa el [a Mi a Hálózati figyelő?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Hálózati figyelő beszerezni                                          |
| Microsoft.Network/networkWatchers/write                             | Hálózati figyelő létrehozása vagy frissítése                             |
| Microsoft.Network/networkWatchers/delete                            | Hálózati figyelő törlése                                       |

## <a name="nsg-flow-logs"></a>NSG-folyamat-naplók

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Folyamatnapló konfigurálása                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Folyamatnapló lekérdezési állapota                                    |

## <a name="connection-troubleshoot"></a>Kapcsolati hibaelhárító

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Kapcsolathiba-teszt kezdeményezése
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Kapcsolathiba-elhárítási teszt eredményeinek lekérdezése                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Kapcsolathiba-teszt futtatása                             |

## <a name="connection-monitor"></a>Kapcsolatfigyelő

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Kapcsolatfigyelő indítása                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Kapcsolatfigyelő leállítása                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Kapcsolatfigyelő lekérdezése                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Csatlakozásfigyelő beszereznie                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Kapcsolatfigyelő létrehozása                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Kapcsolatfigyelő törlése                                    |

## <a name="packet-capture"></a>Csomagrögzítés

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Csomagrögzítés állapotának lekérdezése                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Csomagrögzítés leállítása                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Csomagrögzítés beszerezni                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Csomagrögzítés létrehozása                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Csomagrögzítés törlése                                        |

## <a name="ip-flow-verify"></a>IP-forgalom ellenőrzése

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | IP-folyamat ellenőrzése                                              |

## <a name="next-hop"></a>Következő ugrás

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | A következő ugrás leigazolása virtuális gépről                                     |

## <a name="network-security-group-view"></a>Hálózati biztonsági csoport nézet

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Biztonsági csoportok megtekintése                                           |

## <a name="topology"></a>Topológia

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topológia/művelet                   | Topológia beszerezni                                                   |

## <a name="reachability-report"></a>Elérhetőségi jelentés

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Azure-elérhetőségi jelentés beszerezni                               |


## <a name="additional-actions"></a>További műveletek

A Hálózatfigyelő képességeihez a következő műveletekre is szükség van:

| Művelet(ek)                                                           | Leírás                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft.Authorization/\*/Read                                     | RBAC szerepkör-hozzárendelések és házirend-definíciók lehívására szolgál          |
| Microsoft.Resources/subscriptions/resourceGroups/Read               | Az előfizetés összes erőforráscsoportjának számbavételére szolgál    |
| Microsoft.Storage/storageAccounts/Read                              | A megadott tárfiók tulajdonságainak lekérni   |
| Microsoft.Storage/storageAccounts/listServiceSas/Action, </br> Microsoft.Storage/storageAccounts/listAccountsSas/Action, <br> Microsoft.Storage/storageAccounts/listKeys/Action| A megosztott hozzáférésű aláírások (SAS) lekéréséhez használható, amely biztonságos hozzáférést biztosít a [tárfiókhoz,](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) és írást biztosít a tárfiókba |
| Microsoft.Compute/virtualMachines/Read, </br> Microsoft.Compute/virtualMachines/Write| A virtuális gépbe való bejelentkezéshez, csomagrögzítéshez és a tárfiókba való feltöltéséhez|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| Annak ellenőrzésére szolgál, hogy a Network Watcher bővítmény jelen van-e, és szükség esetén telepítse |
| Microsoft.Compute/virtualMachineScaleSets/Read, </br> Microsoft.Compute/virtualMachineScaleSets/Írás| A virtuálisgép-méretezési csoportok eléréséhez, csomagrögzítésekhez és a tárfiókba való feltöltéséhez|
| Microsoft.Compute/virtualMachineScaleSets/extensions/Read, </br> Microsoft.Compute/virtualMachineScaleSets/extensions/Write| Annak ellenőrzésére szolgál, hogy a Network Watcher bővítmény jelen van-e, és szükség esetén telepítse |
| Microsoft.Insights/alertRules/*                                     | Metrikariasztások beállítására szolgál                                     |
| Microsoft.Támogatás/*                                                 | Támogatási jegyek létrehozására és frissítésére használható a Network Watcher szolgáltatásból |
