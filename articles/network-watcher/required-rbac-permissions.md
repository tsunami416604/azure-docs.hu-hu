---
title: A képességek használatához szükséges RBAC engedélyek
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogy mely Azure szerepköralapú hozzáférés-vezérlési engedélyekre van szükség a Network Watcher képességekkel való együttműködéshez.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: kumud
ms.openlocfilehash: 9d56865a558f027a044e990a2da697dc53e7a311
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277704"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>A Network Watcher képességek használatához szükséges szerepköralapú hozzáférés-vezérlési engedélyek

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy csak a szervezet azon tagjai számára rendeljen hozzá konkrét műveleteket, amelyekre a hozzárendelt felelősségük teljesítéséhez szükségük van. Network Watcher képességek használatához az Azure-ba bejelentkezett fiókot hozzá kell rendelni a [tulajdonoshoz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), a [közreműködőhöz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)vagy a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) beépített szerepköreihez, vagy hozzá kell rendelni egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) , amely az alábbi szakaszokban felsorolt, az egyes Network Watcher képességekhez tartozó műveletekhez van rendelve. Ha többet szeretne megtudni a Network Watcher képességeiről, tekintse meg a [Mi az a Network Watcher?](network-watcher-monitoring-overview.md)című témakört.

## <a name="network-watcher"></a>Network Watcher

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Network Watcher beszerzése                                          |
| Microsoft.Network/networkWatchers/write                             | Network Watcher létrehozása vagy frissítése                             |
| Microsoft.Network/networkWatchers/delete                            | Hálózati figyelő törlése                                       |

## <a name="nsg-flow-logs"></a>NSG

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Folyamat naplójának konfigurálása                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Folyamat naplójának lekérdezési állapota                                    |

## <a name="connection-troubleshoot"></a>Kapcsolatok – problémamegoldás

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | A kapcsolatok hibakeresési tesztelésének kezdeményezése
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Kapcsolatok hibakeresési tesztelési eredményeinek lekérdezése                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Kapcsolatok hibakeresési tesztelésének futtatása                             |

## <a name="connection-monitor"></a>Csatlakozáskezelő

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Csatlakozáskezelő indítása                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Csatlakozáskezelő leállítása                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Csatlakozáskezelő lekérdezése                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Csatlakozáskezelő beszerzése                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Kapcsolatfigyelő létrehozása                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Csatlakozáskezelő törlése                                    |

## <a name="packet-capture"></a>Csomagrögzítés

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | A csomagok rögzítési állapotának lekérdezése                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Csomagok rögzítésének leállítása                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Csomag rögzítésének beolvasása                                           |
| Microsoft. Network/networkWatchers/packetCaptures/Write              | Csomag rögzítésének létrehozása                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Csomagok rögzítésének törlése                                        |

## <a name="ip-flow-verify"></a>IP-forgalom ellenőrzése

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | IP-forgalom ellenőrzése                                              |

## <a name="next-hop"></a>Következő ugrás

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Virtuális gép következő ugrásának beolvasása                                     |

## <a name="network-security-group-view"></a>Hálózati biztonsági csoport nézet

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Biztonsági csoportok megtekintése                                           |

## <a name="topology"></a>Topológia

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Topológia beolvasása                                                   |

## <a name="reachability-report"></a>Elérhetőségi jelentés

| Műveletek                                                              | Leírás                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Azure-beli elérhetőségi jelentés beszerzése                               |


## <a name="additional-actions"></a>További műveletek

Network Watcher képességekhez a következő műveletek is szükségesek:

| Művelet (ek)                                                           | Leírás                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft. Authorization/\*/READ                                     | RBAC szerepkör-hozzárendelések és szabályzat-definíciók beolvasásához használatos          |
| Microsoft. Resources/Subscriptions/resourceGroups/READ               | Egy előfizetésben lévő összes erőforráscsoport enumerálására használatos    |
| Microsoft.Storage/storageAccounts/Read                              | A megadott Storage-fiók tulajdonságainak beolvasására szolgál   |
| Microsoft. Storage/storageAccounts/listServiceSas/művelet, </br> Microsoft. Storage/storageAccounts/listAccountSas/művelet, <br> Microsoft.Storage/storageAccounts/listKeys/Action| A [Storage-fiók biztonságos elérését](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) és a Storage-fiókba való írást lehetővé tévő közös hozzáférésű aláírások (SAS) beolvasására szolgál. |
| Microsoft. számítás/virtualMachines/olvasás, </br> Microsoft.Compute/virtualMachines/Write| A virtuális gépre való bejelentkezéshez használatos, a csomagok rögzítése és feltöltése a Storage-fiókba|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| Annak vizsgálatára használatos, hogy Network Watcher-bővítmény megtalálható-e, és szükség esetén telepítse a telepítést |
| Microsoft. számítás/virtualMachineScaleSets/olvasás, </br> Microsoft.Compute/virtualMachineScaleSets/Write| A virtuálisgép-méretezési csoportok elérésére használatos, a csomagok rögzítése és a Storage-fiókba való feltöltése|
| Microsoft. számítási/virtualMachineScaleSets/bővítmények/olvasás, </br> Microsoft.Compute/virtualMachineScaleSets/extensions/Write| Annak vizsgálatára használatos, hogy Network Watcher-bővítmény megtalálható-e, és szükség esetén telepítse a telepítést |
| Microsoft.Insights/alertRules/*                                     | Metrikai riasztások beállításához használatos                                     |
| Microsoft.Support/*                                                 | Támogatási jegyek létrehozásához és frissítéséhez használható Network Watcher |
