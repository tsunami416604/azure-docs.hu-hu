---
title: Az Azure Network Watcher – Azure portal-kapcsolatok hibaelhárítása |} A Microsoft Docs
description: Ismerje meg, hogyan használható a kapcsolat hibaelhárítása az Azure portal segítségével Azure Network Watcher képességét.
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
ms.openlocfilehash: 21e004e12a5111eb0e5fc7764c1e07fcb68c447d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986200"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Kapcsolatok hibaelhárítása az Azure Network Watcher az Azure portal használatával

> [!div class="op_single_selector"]
> - [Portál](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Az Azure REST API-val](network-watcher-connectivity-rest.md)

Ismerje meg, hogyan használja a kapcsolat ellenőrzése, hogy közvetlen TCP-kapcsolatot a virtuális gépről egy adott végpontot is létesíthető hibaelhárítása.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy az alábbi forrásanyagokat:

* A Network Watcher-kapcsolat hibaelhárítása szeretné a régióban egy példányát.
* Virtuális gépek a kapcsolatok hibaelhárítása.

> [!IMPORTANT]
> Kapcsolódási hibák elhárítása megköveteli, hogy rendelkezik-e a virtuális gép a hibaelhárítás a `AzureNetworkWatcherExtension` Virtuálisgép-bővítmény telepítve van. A bővítmény telepítését egy Windows virtuális gépen látogasson el [Azure Network Watcher-ügynök virtuálisgép-bővítmény Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és Linux rendszerű virtuális gép látogasson el a [Azure Network Watcher-ügynök virtuálisgép-bővítmény Linuxhoz](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A bővítmény nem szükséges a cél-végponton.

## <a name="check-connectivity-to-a-virtual-machine"></a>Ellenőrizze a kapcsolatot a virtuális géphez

Ebben a példában a cél virtuális gép kapcsolatának ellenőrzi a 80-as porton keresztül.

A Network Watcher keresse meg és kattintson **kapcsolódási hibák elhárítása**. Válassza ki a virtuális gép – ellenőrizze a kapcsolatot. Az a **cél** szakaszban válasszon **válasszon ki egy virtuális gépet** , és válassza ki a megfelelő virtuális gép és a tesztelendő portot.

Miután rákattint **ellenőrizze**, a megadott port virtuális gépei közötti kapcsolat be van jelölve. A példában a cél virtuális gép nem érhető el, útválasztók ugrásainak listája látható.

![Virtuális gép kapcsolat eredmények ellenőrzése][1]

## <a name="check-remote-endpoint-connectivity"></a>Ellenőrizze a kapcsolatot a távoli végpont

A kapcsolat és a egy távoli végpontra késés ellenőrzéséhez válassza a **megadás manuálisan** választógomb a **cél** szakaszt, adjon meg az URL-címet és portot, és kattintson a **Ellenőrizze**.  Ez szolgál, például a websites és a storage végpontok távoli végpontokhoz.

![A webhely csatlakozási eredmények ellenőrzése][2]

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan automatizálhatja a virtuális gép riasztások csomagrögzítés megtekintésével [hozzon létre egy aktivált riasztás csomagrögzítés](network-watcher-alert-triggered-packet-capture.md)

Keresse meg, ha bizonyos van engedélyezi a forgalmat a virtuális gép vagy a funkcionáló [ellenőrizze IP-folyamat ellenőrzése](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png