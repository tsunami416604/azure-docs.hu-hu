---
title: Nem lehet távolról csatlakozni az Azure virtuális gépekhez, mert a hálózati adapter le van tiltva | Microsoft dokumentumok
description: Megtudhatja, hogyan háríthatja el azt a problémát, amelynek során az RDP meghibásodik, mert a hálózati adapter le van tiltva az Azure VM- ben| Microsoft dokumentumok
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
ms.openlocfilehash: 315974e4995630eb3af055ac0e1c44f7d8dd0737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918240"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Az asztal nem távolítható el a virtuális géphez, mert a hálózati adapter le van tiltva

Ez a cikk bemutatja, hogyan oldható meg az a probléma, amelyben nem tud távoli asztali kapcsolatot létesíteni az Azure Windows virtuális gépekkel ,, ha a hálózati adapter le van tiltva.


## <a name="symptoms"></a>Probléma

Nem hozhat létre RDP-kapcsolatot vagy más típusú kapcsolatot más portokkal az Azure-beli virtuális géphez, mert a virtuális gép hálózati illesztője le van tiltva.

## <a name="solution"></a>Megoldás

Az alábbi lépések végrehajtása előtt készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.

A virtuális gép összeköttetésének engedélyezéséhez használja a Soros vezérlőt, vagy [állítsa alaphelyzetbe](#reset-network-interface) a virtuális gép hálózati összeköttetését.

### <a name="use-serial-control"></a>Soros vezérlő használata

1. Csatlakozzon a [Soros konzolhoz, és nyissa meg a CMD-példányt.](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
) Ha a soros konzol nincs engedélyezve a virtuális gépen, olvassa el a [hálózati adapter alaphelyzetbe állítása című témakört.](#reset-network-interface)
2. Ellenőrizze a hálózati adapter állapotát:

        netsh interface show interface

    Jegyezze fel a letiltott hálózati adapter nevét.

3. A hálózati adapter engedélyezése:

        netsh interface set interface name="interface Name" admin=enabled

    Ha például a munkaközi felület neve "Ethernet 2", futtassa a következő parancsot:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Ellenőrizze újra a hálózati adapter állapotát, és győződjön meg arról, hogy a hálózati csatoló engedélyezve van.

        netsh interface show interface

    Ezen a ponton nem kell újraindítania a virtuális gép. A virtuális gép újra elérhető lesz.

5.  Csatlakozzon a virtuális géphez, és nézze meg, hogy a probléma megoldódott-e.

## <a name="reset-network-interface"></a>Hálózati adapter alaphelyzetbe állítása

A hálózati adapter alaphelyzetbe állításához módosítsa az IP-címet egy másik, az alhálózatban elérhető IP-címre. Ehhez használja az Azure Portalon vagy az Azure PowerShell. További információt a [Hálózati adapter visszaállítása című témakörben talál.](reset-network-interface.md)
