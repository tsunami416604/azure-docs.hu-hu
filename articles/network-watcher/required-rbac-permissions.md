---
title: A képességek használatához szükséges RBAC engedélyek
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogy mely Azure szerepköralapú hozzáférés-vezérlési engedélyekre van szükség a Network Watcher képességekkel való együttműködéshez.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76840553"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>A Network Watcher képességek használatához szükséges szerepköralapú hozzáférés-vezérlési engedélyek

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy csak a szervezet azon tagjai számára rendeljen hozzá konkrét műveleteket, amelyekre a hozzárendelt felelősségük teljesítéséhez szükségük van. Network Watcher képességek használatához az Azure-ba bejelentkezett fiókot hozzá kell rendelni a [tulajdonoshoz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), a [közreműködőhöz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)vagy a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) beépített szerepköreihez, vagy hozzá kell rendelni egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) , amely az alábbi szakaszokban felsorolt, az egyes Network Watcher képességekhez tartozó műveletekhez van rendelve. Ha többet szeretne megtudni a Network Watcher képességeiről, tekintse meg a [Mi az a Network Watcher?](network-watcher-monitoring-overview.md)című témakört.

## <a name="network-watcher"></a>Network Watcher

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/READ                              | Network Watcher beszerzése                                          |
| Microsoft. Network/networkWatchers/Write                             | Network Watcher létrehozása vagy frissítése                             |
| Microsoft. Network/networkWatchers/delete                            | Hálózati figyelő törlése                                       |

## <a name="nsg-flow-logs"></a>NSG-folyamat-naplók

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/configureFlowLog/Action           | Folyamat naplójának konfigurálása                                           |
| Microsoft. Network/networkWatchers/queryFlowLogStatus/Action         | Folyamat naplójának lekérdezési állapota                                    |

## <a name="connection-troubleshoot"></a>Kapcsolati hibaelhárító

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/connectivityCheck/Action          | A kapcsolatok hibakeresési tesztelésének kezdeményezése
| Microsoft. Network/networkWatchers/queryTroubleshootResult/Action    | Kapcsolatok hibakeresési tesztelési eredményeinek lekérdezése                |
| Microsoft. Network/networkWatchers/hibakeresés/művelet               | Kapcsolatok hibakeresési tesztelésének futtatása                             |

## <a name="connection-monitor"></a>Csatlakozáskezelő

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/connectionMonitors/Start/művelet   | Csatlakozáskezelő indítása                                     |
| Microsoft. Network/networkWatchers/connectionMonitors/leállítás/művelet    | Csatlakozáskezelő leállítása                                      |
| Microsoft. Network/networkWatchers/connectionMonitors/Query/Action   | Csatlakozáskezelő lekérdezése                                     |
| Microsoft. Network/networkWatchers/connectionMonitors/READ           | Csatlakozáskezelő beszerzése                                       |
| Microsoft. Network/networkWatchers/connectionMonitors/Write          | Kapcsolatfigyelő létrehozása                                    |
| Microsoft. Network/networkWatchers/connectionMonitors/delete         | Csatlakozáskezelő törlése                                    |

## <a name="packet-capture"></a>Csomagrögzítés

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/packetCaptures/queryStatus/művelet | A csomagok rögzítési állapotának lekérdezése                           |
| Microsoft. Network/networkWatchers/packetCaptures/leállítás/művelet        | Csomagok rögzítésének leállítása                                          |
| Microsoft. Network/networkWatchers/packetCaptures/READ               | Csomag rögzítésének beolvasása                                           |
| Microsoft. Network/networkWatchers/packetCaptures/Write              | Csomag rögzítésének létrehozása                                        |
| Microsoft. Network/networkWatchers/packetCaptures/delete             | Csomagok rögzítésének törlése                                        |

## <a name="ip-flow-verify"></a>IP-forgalom ellenőrzése

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/ipFlowVerify/Action               | IP-forgalom ellenőrzése                                              |

## <a name="next-hop"></a>Következő ugrás

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/nextHop/Action                    | Virtuális gép következő ugrásának beolvasása                                     |

## <a name="network-security-group-view"></a>Hálózati biztonsági csoport nézet

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/securityGroupView/Action          | Biztonsági csoportok megtekintése                                           |

## <a name="topology"></a>Topológia

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/topológia/művelet                   | Topológia beolvasása                                                   |

## <a name="reachability-report"></a>Elérhetőségi jelentés

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/azureReachabilityReport/Action    | Azure-beli elérhetőségi jelentés beszerzése                               |


## <a name="additional-actions"></a>További műveletek

Network Watcher képességekhez a következő műveletek is szükségesek:

| Művelet (ek)                                                           | Leírás                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft. Authorization/\*/READ                                     | RBAC szerepkör-hozzárendelések és szabályzat-definíciók beolvasásához használatos          |
| Microsoft. Resources/Subscriptions/resourceGroups/READ               | Egy előfizetésben lévő összes erőforráscsoport enumerálására használatos    |
| Microsoft. Storage/storageAccounts/olvasás                              | A megadott Storage-fiók tulajdonságainak beolvasására szolgál   |
| Microsoft. Storage/storageAccounts/listServiceSas/művelet, </br> Microsoft. Storage/storageAccounts/listAccountSas/művelet, <br> Microsoft. Storage/storageAccounts/Listkeys műveletének beolvasása/művelet| A [Storage-fiók biztonságos elérését](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) és a Storage-fiókba való írást lehetővé tévő közös hozzáférésű aláírások (SAS) beolvasására szolgál. |
| Microsoft. számítás/virtualMachines/olvasás, </br> Microsoft. számítás/virtualMachines/írás| A virtuális gépre való bejelentkezéshez használatos, a csomagok rögzítése és feltöltése a Storage-fiókba|
| Microsoft. számítás/virtualMachines/bővítmények/olvasás </br> Microsoft. számítás/virtualMachines/bővítmények/írás| Annak vizsgálatára használatos, hogy Network Watcher-bővítmény megtalálható-e, és szükség esetén telepítse a telepítést |
| Microsoft. számítás/virtualMachineScaleSets/olvasás, </br> Microsoft. számítás/virtualMachineScaleSets/írás| A virtuálisgép-méretezési csoportok elérésére használatos, a csomagok rögzítése és a Storage-fiókba való feltöltése|
| Microsoft. számítási/virtualMachineScaleSets/bővítmények/olvasás, </br> Microsoft. számítás/virtualMachineScaleSets/bővítmények/írás| Annak vizsgálatára használatos, hogy Network Watcher-bővítmény megtalálható-e, és szükség esetén telepítse a telepítést |
| Microsoft. bepillantások/alertRules/*                                     | Metrikai riasztások beállításához használatos                                     |
| Microsoft. support/*                                                 | Támogatási jegyek létrehozásához és frissítéséhez használható Network Watcher |
