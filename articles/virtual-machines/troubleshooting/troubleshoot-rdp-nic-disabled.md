---
title: Nem lehet távoli csatlakozás az Azure Virtual Machines a hálózati adapter le van tiltva, mert |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a problémát, amely RDP meghiúsul, mert a hálózati adapter le van tiltva, az Azure virtuális Gépen |} A Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: f9e4731146409f51d16a8c92c01d07a8ff11a7ea
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314524"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>A hálózati adapter le van tiltva, nem tudja egy virtuális géphez a távoli asztal

Ez a cikk bemutatja, hogyan, amelyben nem lehet a távoli asztali kapcsolat az Azure Windows Virtual Machines (VM) a hálózati adapter le van tiltva, ha a probléma megoldása érdekében.

> [!NOTE] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a Resource Manager üzemi modell, amely az új központi telepítéseknél helyett a klasszikus üzemi modell használatát javasoljuk. 

## <a name="symptoms"></a>Probléma 

Nem lehet RDP-kapcsolatok vagy bármilyen más típusú bármely más portok csatlakozni egy virtuális géphez az Azure-ban a hálózati adaptert a virtuális gép le van tiltva.

## <a name="solution"></a>Megoldás 

Mielőtt végrehajtaná ezeket a lépéseket, az érintett virtuális gép operációsrendszer-lemezének pillanatkép készítése a biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).

A virtuális gép a felület engedélyezéséhez használja a soros vezérlőelem vagy [hálózati adapter alaphelyzetbe állítása](##reset-network-interface) a virtuális gép számára.

### <a name="use-serial-control"></a>Soros vezérlőelem használata

1. Csatlakozás [soros konzolon és a nyílt CMD-példány](./serial-console-windows.md#open-cmd-or-powershell-in-serial-console
). Ha a soros konzol nincs engedélyezve a virtuális Gépen, [hálózati adapter alaphelyzetbe állítása](#reset-network-interface).
2. A hálózati adapter állapotának ellenőrzése:

        netsh interface show interface

    Vegye figyelembe a letiltott hálózati kapcsolat neve. 

3. Engedélyezze a hálózati adapter:

        netsh interface set interface name="interface Name" admin=enabled

    Például ha a interwork felület "Ethernet 2" nevű, futtassa a következő parancsot:

        netsh interface set interface name=""Ethernet 2" admin=enabled
    

4.  Ellenőrizze a hálózati adapter újra, győződjön meg arról, hogy engedélyezve van-e a hálózati adapter állapotát.

        netsh interface show interface

    Nem kell ezen a ponton a virtuális gép újraindítására. A virtuális gép ismét elérhető lesz.
        
5.  Csatlakozzon a virtuális Géphez, és tekintse meg, hogy megoldódott-e a probléma.

## <a name="reset-network-interface"></a>Hálózati adapter alaphelyzetbe állítása

Hálózati adapter alaphelyzetbe állítása, váltson egy másik alhálózaton elérhető IP-címet az IP-cím. Ehhez használja az Azure portal vagy Azure PowerShell-lel. További információkért lásd: [hálózati adapter alaphelyzetbe állítása](reset-network-interface.md). 