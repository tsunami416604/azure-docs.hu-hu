---
title: "Ellenőrizze a kapcsolatot az Azure hálózati figyelőt - Azure-portál |} Microsoft Docs"
description: "Ezen a lapon ismerteti, hogyan használható az Azure portál használatával hálózati figyelőt kapcsolat ellenőrzése"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 1f19da71731039e1a39c4440f925b1369886a993
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="check-connectivity-with-azure-network-watcher-using-the-azure-portal"></a>Ellenőrizze a kapcsolatot az Azure hálózati figyelőt az Azure portál használatával

> [!div class="op_single_selector"]
> - [Portál](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [Az Azure REST API-n](network-watcher-connectivity-rest.md)

Megtudhatja, hogyan ellenőrizheti, ha egy közvetlen TCP-kapcsolatot a virtuális gép egy adott végpont is hozható létre kapcsolat használatára.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik-e a következőket:

* Ellenőrizze a kapcsolatot szeretne hálózati figyelőt régióban példánya.

* Ellenőrizze a kapcsolatot a virtuális gépeket.

> [!IMPORTANT]
> Kapcsolat ellenőrzése van szükség a virtuálisgép-bővítmény `AzureNetworkWatcherExtension`. A bővítmény telepítése a Windows virtuális gép a Microsoft [a Windows Azure hálózati figyelő ügynök virtuálisgép-bővítmény](../virtual-machines/windows/extensions-nwa.md) és a Linux virtuális gép helyezést [Azure hálózati figyelő ügynök virtuálisgép-bővítmény Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="check-connectivity-to-a-virtual-machine"></a>Ellenőrizze a kapcsolatot a virtuális géphez

Ebben a példában a cél virtuális gép kapcsolatát ellenőrzi a 80-as porton keresztül.

Nyissa meg a hálózati figyelőt, és kattintson a **kapcsolat ellenőrzése (előzetes verzió)**. Válassza ki a virtuális gép kapcsolat ellenőrzéséhez. Az a **cél** rész válassza **válasszon ki egy virtuális gépet** , és válassza ki a megfelelő virtuális gép és a port teszteléséhez.

Miután rákattintott **ellenőrizze**, a virtuális gépeket a megadott port közötti kapcsolatot ellenőrzi. A példában a cél virtuális gép nem érhető el, útválasztók ugrásainak listája látható.

![A virtuális gép kapcsolat eredmények ellenőrzése][1]

## <a name="check-remote-endpoint-connectivity"></a>Ellenőrizze a kapcsolatot a távoli végpont

Ellenőrizze a kapcsolatot és a távoli végpont várakozási ideje, válassza ki a **adja meg manuálisan** választógomb a **cél** szakaszban, adja meg az URL-címet és portot, és kattintson a **Ellenőrizze**.  Például a webhelyek és a tárolási végpontok távoli végpontok szolgál.

![A webhely csatlakozási eredmények ellenőrzése][2]

## <a name="next-steps"></a>Következő lépések

Csomag rögzíti a virtuális gép a riasztások megtekintésével automatizálása [riasztási kiváltott csomagrögzítéssel létrehozása](network-watcher-alert-triggered-packet-capture.md)

Keresése, ha bizonyos adatforgalom engedélyezett a virtuális gép kívül vagy belül ellátogatva [ellenőrizze IP folyamat ellenőrzése](network-watcher-check-ip-flow-verify-portal.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png
