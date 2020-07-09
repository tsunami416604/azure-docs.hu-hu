---
title: Nem lehet távolról kapcsolódni az Azure Virtual Machineshoz, mert a hálózati adapter le van tiltva | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a problémát, amikor az RDP nem sikerül, mert a NIC le van tiltva az Azure-beli virtuális gépen | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 6bce1616ce0c7f7e42810a551acb2f02165ccf93
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078629"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Nem lehet a Távoli asztalt a virtuális géphez kapcsolni, mert a hálózati adapter le van tiltva

Ez a cikk azt ismerteti, hogyan oldható meg egy olyan probléma, amelyben nem lehet Távoli asztal kapcsolatot létesíteni az Azure Windows Virtual Machines (VM) szolgáltatással, ha a hálózati adapter le van tiltva.


## <a name="symptoms"></a>Probléma

Az Azure-beli virtuális gépekhez nem lehet RDP-kapcsolatot vagy bármilyen más típusú kapcsolatot létesíteni bármely más porttal, mert a virtuális gép hálózati adaptere le van tiltva.

## <a name="solution"></a>Megoldás

Az alábbi lépések elvégzése előtt készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).

A virtuális gép felületének engedélyezéséhez használja a soros vezérlés vagy a [hálózati adapter alaphelyzetbe állítása](#reset-network-interface) lehetőséget a virtuális géphez.

### <a name="use-serial-control"></a>Soros vezérlő használata

1. Kapcsolódjon a [soros konzolhoz, és nyissa meg a cmd-példányt](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Ha a soros konzol nincs engedélyezve a virtuális gépen, tekintse meg a [hálózati adapter alaphelyzetbe állítása](#reset-network-interface)című témakört.
2. Győződjön meg arról, hogy a hálózati adapter állapota:

    ```console
    netsh interface show interface
    ```

    Jegyezze fel a letiltott hálózati adapter nevét.

3. A hálózati adapter engedélyezése:

    ```console
    netsh interface set interface name="interface Name" admin=enabled
    ```

    Ha például az adatkapcsolati felület neve "Ethernet 2", futtassa a következő parancsot:

    ```console
    netsh interface set interface name="Ethernet 2" admin=enabled
    ```

4.  Ellenőrizze a hálózati adapter állapotát, és győződjön meg arról, hogy a hálózati adapter engedélyezve van.

    ```console
    netsh interface show interface
    ```

    Ezen a ponton nem kell újraindítani a virtuális gépet. A virtuális gép elérhető lesz.

5.  Kapcsolódjon a virtuális géphez, és ellenőrizze, hogy megoldódott-e a probléma.

## <a name="reset-network-interface"></a>Hálózati adapter alaphelyzetbe állítása

A hálózati adapter alaphelyzetbe állításához módosítsa az IP-címet egy másik, az alhálózaton elérhető IP-címhez. Ehhez használja Azure Portal vagy Azure PowerShell. További információ: a [hálózati adapter alaphelyzetbe állítása](reset-network-interface.md).
