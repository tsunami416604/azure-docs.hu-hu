---
title: A hálózati adapter le van tiltva, nem lehet a távoli asztal az Azure Virtual Machines |} A Microsoft Docs
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
ms.openlocfilehash: 6b14530bd6b4c1b6617cb1d5c88d710a32e5372c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634702"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>A hálózati adapter le van tiltva, nem tudja egy virtuális géphez a távoli asztal

Ez a cikk bemutatja, hogyan, amelyben nem lehet a távoli asztal az Azure Windows Virtual Machines (VM) a hálózati adapter le van tiltva. a probléma megoldása érdekében.

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

    Például ha a interwork felület "Ethernet 2"-neveket, futtassa a következő parancsot:

        netsh interface set interface name=""Ethernet 2" admin=enabled
    

4.  Ellenőrizze a hálózati adapter újra, győződjön meg arról, hogy engedélyezve van-e a hálózati adapter állapotát.

        netsh interface show interface

    Nem kell ezen a ponton a virtuális gép újraindítására. A virtuális gép ismét elérhető lesz.
        
5.  Csatlakozzon a virtuális Géphez, és a probléma megoldódott.

## <a name="reset-network-interface"></a>Hálózati adapter alaphelyzetbe állítása

Hálózati adapter alaphelyzetbe állítása, módosítsa az IP-cím, amely az alhálózaton elérhető az Azure portal vagy az Azure PowerShell használatával egy másik IP-címre. További információkért lásd: [hálózati adapter alaphelyzetbe állítása](reset-network-interface.md). 