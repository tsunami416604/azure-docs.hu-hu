---
title: Az Azure Network Watcher következő ugrás bemutatása |} A Microsoft Docs
description: Ez a cikk áttekintést a Network Watcher következő ugrás képesség.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 28eacdce922e26d391cf34f78cb03ead9c6887a1
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391266"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Használja a következő ugrás virtuális gép útválasztási problémáinak diagnosztizálása

Egy virtuális gépet (VM) érkező forgalmat küld egy cél hálózati adapter (NIC) tartozó érvényes útvonalak alapján. Következő ugrás olvas be a következő ugrás típusa és a egy csomag IP-címét egy adott virtuális Gépről vagy hálózati adapterhez. A következő ugrás ismerete segít meghatározni, hogy ha a rendeltetési átirányítja folyamatban van a forgalmat, vagy ha forgalmat küld a rendszer sehol. Kapcsolódási problémák útvonalakat, ahol átirányítja a forgalmat egy a helyszíni helyre, vagy egy olyan virtuális berendezésre, egy nem megfelelő konfigurációja vezethet. Következő ugrás is az a következő ugrás társított útvonaltábla adja vissza. Ha az útvonal egy felhasználó által definiált útvonal van meghatározva, azt a módot adja vissza. Ellenkező esetben a következő Ugrás vissza **Rendszerútvonal**.

![a következő ugrás áttekintése](./media/network-watcher-next-hop-overview/figure1.png)

A következő ugrásokkal együtt, amely a következő ugrás funkció által visszaadott előfordulhat, hogy a következők:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* None

Következő ugrások típusaira kapcsolatos további információkért lásd: [Útválasztás áttekintése](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>További lépések

VM-hálózat útválasztási problémáinak diagnosztizálása a következő ugrás használatával kapcsolatban lásd: [diagnosztizálhatja a Virtuálisgép-hálózat útválasztási problémáinak](diagnose-vm-network-routing-problem.md).
