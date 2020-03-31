---
title: Kapcsolatok – Azure Portal – Azure Portal
titleSuffix: Azure Network Watcher
description: Ismerje meg, hogyan használhatja az Azure Network Watcher kapcsolathibaelhárítási képességét az Azure Portalon.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: damendo
ms.openlocfilehash: e405a91b1ea541b4ed3328fdb3bf80ca82731c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283237"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Az Azure Network Watcherrel létesített kapcsolatok – az Azure Portal használatával – problémamegoldás

> [!div class="op_single_selector"]
> - [Portál](network-watcher-connectivity-portal.md)
> - [Powershell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Ismerje meg, hogyan használható a kapcsolathiba elhárítása annak ellenőrzésére, hogy létre lehet-e hozni egy közvetlen TCP-kapcsolatot egy virtuális gépről egy adott végpontra.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy a következő erőforrásokkal rendelkezik:

* A Hálózatfigyelő egy példánya abban a régióban, ahol el szeretné hárítani a kapcsolatot.
* Virtuális gépek a kapcsolatok elhárításához.

> [!IMPORTANT]
> A kapcsolathiba elhárításához szükség van `AzureNetworkWatcherExtension` arra, hogy a virtuális gép, amelyről hibaelhárítási, telepítve van a virtuális gép bővítménye. A bővítmény Windows virtuális gépen történő telepítéséhez látogasson el az [Azure Network Watcher Agent windowsos](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és Linuxos virtuális gépbővítménybe, és látogasson el az [Azure Network Watcher Agent linuxos virtuálisgép-bővítménybe.](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) A bővítmény nem szükséges a célvégponton.

## <a name="check-connectivity-to-a-virtual-machine"></a>Virtuális gép csatlakoztatásának ellenőrzése

Ez a példa a 80-as porton keresztül ellenőrzi a célvirtuális géphez való kapcsolódást.

Nyissa meg a Hálózatfigyelőt, és kattintson a **Kapcsolathiba gombra.** Válassza ki azt a virtuális gépet, amelyből ellenőrizni szeretné a kapcsolatot. A **Cél** szakaszban válassza **ki a virtuális gépet,** és válassza ki a megfelelő virtuális gépet és portot.

Ha az **Ellenőrzés**gombra kattint, a megadott porton lévő virtuális gépek közötti kapcsolat be van jelölve. A példában a cél virtuális gép nem érhető el, az ugrások listája jelenik meg.

![Virtuális gép kapcsolódási eredményeinek ellenőrzése][1]

## <a name="check-remote-endpoint-connectivity"></a>Távoli végpontkapcsolat ellenőrzése

A távoli végpont kapcsolatának és késésének ellenőrzéséhez válassza a **Cél** szakasz **Manuálisan megadása** gombját, adja meg az URL-címet és a portot, és kattintson az Ellenőrzés **gombra.**  Ez a távoli végpontok, például a webhelyek és a tárolási végpontok.

![Webhely kapcsolódási eredményeinek ellenőrzése][2]

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan automatizálhatja a csomagrögzítéseket a virtuálisgép-riasztások segítségével a [Riasztás aktivált csomagrögzítésének létrehozásával](network-watcher-alert-triggered-packet-capture.md)

Annak megállapítása, hogy bizonyos forgalom engedélyezett-e a virtuális gépbe vagy a virtuális gépből, az [IP-folyamat ellenőrzése című ellátogat.Find](diagnose-vm-network-traffic-filtering-problem.md) if certain traffic is allowed in or out in your VM by check IP flow verify

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png