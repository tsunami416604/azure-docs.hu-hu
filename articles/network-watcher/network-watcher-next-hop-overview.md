---
title: Bevezetés a következő ugrásba az Azure Network Watcherben | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt a Network Watcher következő ugrási képességéről.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 33c22b5b68b6677e8cf271dc185007316ec44500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844058"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>A következő ugrás használata a virtuálisgépek útválasztási problémáinak diagnosztizálására

A virtuális gépről (VM) érkező forgalmat a rendszer a hálózati adapterhez (NIC) társított tényleges útvonalak alapján küldi el a célhelyre. Következő ugrás leáll a következő ugrás típusa és IP-címe egy csomag egy adott virtuális gép és a hálózati adapter. A következő ugrás ismerete segít meghatározni, hogy a forgalom a kívánt célhelyre irányul-e, vagy hogy a forgalom nem kerül-e sehova. Az útvonalak nem megfelelő konfigurációja, ahol a forgalom egy helyszíni helyre vagy egy virtuális berendezésre irányul, kapcsolódási problémákhoz vezethet. A következő ugrás a következő ugráshoz társított útvonaltáblát is visszaadja. Ha az útvonal felhasználó által definiált útvonalként van definiálva, a rendszer visszaadja azt. Ellenkező esetben a következő ugrás a **Rendszerútvonal értéket**adja vissza.

![következő ugrás áttekintése](./media/network-watcher-next-hop-overview/figure1.png)

A következő ugrási képesség által visszaadott következő ugrások a következők:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* None

Ha többet szeretne tudni az egyes következő ugrástípusokról, olvassa [el az Útválasztás – áttekintés című témakört.](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)

## <a name="next-steps"></a>További lépések

A virtuális gép hálózati útválasztási problémáinak diagnosztizálása a következő ugrás használatával a Virtuálisgép-hálózati útválasztási problémák diagnosztizálása az [Azure Portalon](diagnose-vm-network-routing-problem.md), a [PowerShell](diagnose-vm-network-routing-problem-powershell.md)vagy az [Azure CLI](diagnose-vm-network-routing-problem-cli.md)használatával című témakörben olvashat.
