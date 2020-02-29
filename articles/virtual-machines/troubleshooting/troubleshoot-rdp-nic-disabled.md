---
title: Nem lehet távoli csatlakozás az Azure Virtual Machines a hálózati adapter le van tiltva, mert |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a problémát, amely RDP meghiúsul, mert a hálózati adapter le van tiltva, az Azure virtuális Gépen |} A Microsoft Docs
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918240"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>A hálózati adapter le van tiltva, nem tudja egy virtuális géphez a távoli asztal

Ez a cikk bemutatja, hogyan, amelyben nem lehet a távoli asztali kapcsolat az Azure Windows Virtual Machines (VM) a hálózati adapter le van tiltva, ha a probléma megoldása érdekében.


## <a name="symptoms"></a>Probléma

Nem lehet RDP-kapcsolatok vagy bármilyen más típusú bármely más portok csatlakozni egy virtuális géphez az Azure-ban a hálózati adaptert a virtuális gép le van tiltva.

## <a name="solution"></a>Megoldás

Mielőtt végrehajtaná ezeket a lépéseket, az érintett virtuális gép operációsrendszer-lemezének pillanatkép készítése a biztonsági mentéséhez. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).

A virtuális gép felületének engedélyezéséhez használja a soros vezérlés vagy a [hálózati adapter alaphelyzetbe állítása](#reset-network-interface) lehetőséget a virtuális géphez.

### <a name="use-serial-control"></a>Soros vezérlőelem használata

1. Kapcsolódjon a [soros konzolhoz, és nyissa meg a cmd-példányt](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Ha a soros konzol nincs engedélyezve a virtuális gépen, tekintse meg a [hálózati adapter alaphelyzetbe állítása](#reset-network-interface)című témakört.
2. A hálózati adapter állapotának ellenőrzése:

        netsh interface show interface

    Vegye figyelembe a letiltott hálózati kapcsolat neve.

3. Engedélyezze a hálózati adapter:

        netsh interface set interface name="interface Name" admin=enabled

    Például ha a interwork felület "Ethernet 2" nevű, futtassa a következő parancsot:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Ellenőrizze a hálózati adapter újra, győződjön meg arról, hogy engedélyezve van-e a hálózati adapter állapotát.

        netsh interface show interface

    Nem kell ezen a ponton a virtuális gép újraindítására. A virtuális gép ismét elérhető lesz.

5.  Csatlakozzon a virtuális Géphez, és tekintse meg, hogy megoldódott-e a probléma.

## <a name="reset-network-interface"></a>Hálózati adapter alaphelyzetbe állítása

Hálózati adapter alaphelyzetbe állítása, váltson egy másik alhálózaton elérhető IP-címet az IP-cím. Ehhez használja az Azure portal vagy Azure PowerShell-lel. További információ: a [hálózati adapter alaphelyzetbe állítása](reset-network-interface.md).
