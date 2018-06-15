---
title: Bevezetés az Azure hálózati figyelőt a következő ugrás |} Microsoft Docs
description: Ez a cikk áttekintést a hálózati figyelőt következő ugrás funkció.
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
ms.openlocfilehash: bbb782e700781dcfedbbd340c7d10db53767b035
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32180385"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Használja a következő ugrás a virtuális gép útválasztási problémák diagnosztizálásához

A virtuális gép (VM) forgalmat elküldi egy cél, a hálózati kártya (NIC) társított hatékony útvonalak alapján. Következő ugrás lekérése a következő ugrás típusa és a csomagok IP-cím egy adott virtuális gép és egy hálózati adaptert. A következő ugrás ismerete segít meghatározni, ha a rendeltetési fordítani, a forgalom, vagy ha a forgalom sehol küldi. Az útvonalak, ahol forgalom irányítja a rendszer egy helyi helyre, vagy a virtuális készülék, egy nem megfelelő konfigurációs problémák vezethet. Következő ugrás is a következő ugrás hozzárendelt útválasztási táblázatot ad vissza. Ha egy felhasználó által megadott útvonalat az útvonal van meghatározva, a rendszer, hogy az útvonal adja vissza. Ellenkező esetben a következő ugrás értéket ad vissza **Rendszerútvonal**.

![a következő ugrás – áttekintés](./media/network-watcher-next-hop-overview/figure1.png)

A következő ugrások beállított esetleg a következő ugrás által visszaadott a következők:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* VnetPeering
* None

Minden egyes következő ugrás típusa kapcsolatos további információkért lásd: [Útválasztás – áttekintés](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>További lépések

Következő Ugrás használata a virtuális gép hálózati útválasztási problémák diagnosztizálásához kapcsolatban [diagnosztizálása Virtuálisgép-hálózat útválasztási problémák](diagnose-vm-network-routing-problem.md).
