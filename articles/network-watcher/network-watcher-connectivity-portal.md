---
title: Azure hálózati figyelőt - Azure-portálon-kapcsolatok hibáinak elhárítása |} Microsoft Docs
description: Ismerje meg, hogyan használható a kapcsolati hibáinak elhárítása az Azure portál használatával Azure hálózati figyelőt képességét.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 55c8e6763b0c3d8bd3884a44695c25e62b8d9900
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Kapcsolatok hibaelhárítása az Azure hálózati figyelőt az Azure portál használatával

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Megtudhatja, hogyan használja a kapcsolat ellenőrzése, hogy egy közvetlen TCP-kapcsolatot a virtuális gép egy adott végpont is hozható létre hibaelhárítása.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik-e a következőket:

* Végezzen hibaelhárítást a kapcsolaton kívánt hálózati figyelőt régióban példánya.
* Virtuális gépek kapcsolatok hibaelhárításához.

> [!IMPORTANT]
> Csatlakozási hibáinak elhárítása az szükséges, hogy a virtuális gép a hibaelhárítás a `AzureNetworkWatcherExtension` telepített virtuális gépi bővítményt. A bővítmény telepítése a Windows virtuális gép a Microsoft [a Windows Azure hálózati figyelő ügynök virtuálisgép-bővítmény](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és a Linux virtuális gép helyezést [Azure hálózati figyelő ügynök virtuálisgép-bővítmény Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A bővítmény nem szükséges a cél-végponthoz.

## <a name="check-connectivity-to-a-virtual-machine"></a>Ellenőrizze a kapcsolatot a virtuális géphez

Ebben a példában a cél virtuális gép kapcsolatát ellenőrzi a 80-as porton keresztül.

Nyissa meg a hálózati figyelőt, és kattintson a **kapcsolati hibáinak elhárítása**. Válassza ki a virtuális gép kapcsolat ellenőrzéséhez. Az a **cél** rész válassza **válasszon ki egy virtuális gépet** , és válassza ki a megfelelő virtuális gép és a port teszteléséhez.

Miután rákattintott **ellenőrizze**, a virtuális gépeket a megadott port közötti kapcsolat be van jelölve. A példában a cél virtuális gép nem érhető el, útválasztók ugrásainak listája látható.

![A virtuális gép kapcsolat eredmények ellenőrzése][1]

## <a name="check-remote-endpoint-connectivity"></a>Ellenőrizze a kapcsolatot a távoli végpont

Ellenőrizze a kapcsolatot és a távoli végpont várakozási ideje, válassza ki a **adja meg manuálisan** választógomb a **cél** szakaszban, adja meg az URL-címet és portot, és kattintson a **Ellenőrizze**.  Például a webhelyek és a tárolási végpontok távoli végpontok szolgál.

![A webhely csatlakozási eredmények ellenőrzése][2]

## <a name="next-steps"></a>További lépések

Csomag rögzíti a virtuális gép a riasztások megtekintésével automatizálása [riasztási kiváltott csomagrögzítéssel létrehozása](network-watcher-alert-triggered-packet-capture.md)

Keresése, ha bizonyos adatforgalom engedélyezett a virtuális gép kívül vagy belül ellátogatva [ellenőrizze IP folyamat ellenőrzése](network-watcher-check-ip-flow-verify-portal.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png