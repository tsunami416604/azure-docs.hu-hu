---
title: Statikus IP-cím miatt nem lehet a távoli asztal az Azure Virtual Machines |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a statikus IP-cím a Microsoft Azure által okozott probléma RDP. |} A Microsoft Docs
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
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: c219b2fb58d46d9280ef5c022140e0499e3ac54c
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51347689"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Az Azure Virtual Machines a távoli asztal nem statikus IP-cím miatt

Ez a cikk ismerteti a problémát, amelyben nem lehet a távoli asztal az Azure Windows Virtual Machines (VM) a virtuális gép statikus IP-cím konfigurálása után.

> [!NOTE] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a Resource Manager üzemi modell, amely az új központi telepítéseknél helyett a klasszikus üzemi modell használatát javasoljuk. 

## <a name="symptoms"></a>Probléma 

Az Azure-beli virtuális gép RDP-kapcsolatának hajt végre, amikor a következő hibaüzenet jelenhet meg:

**A távoli asztal az alábbi okok valamelyike nem tud kapcsolódni a távoli számítógéphez:**

1. **Távoli hozzáférés a kiszolgálón nincs engedélyezve**

2. **A távoli számítógép ki van kapcsolva.**

3. **A távoli számítógép nem érhető el a hálózaton**

**Ellenőrizze, hogy a távoli számítógéphez van kapcsolva, és csatlakozik a hálózathoz, és, hogy a távoli hozzáférés engedélyezve van-e.**

Amikor ellenőrizheti a képernyőképen a a [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) az Azure Portalon, megjelenik a virtuális gép indul el megfelelően, és a hitelesítő adatokat a bejelentkezési képernyőn vár.

## <a name="cause"></a>Ok

A virtuális gép rendelkezik definiált Windows belül a hálózati adapter statikus IP-címet. Az IP-cím eltér a cím, amelyet az Azure Portalon van definiálva.

## <a name="solution"></a>Megoldás 

Mielőtt végrehajtaná ezeket a lépéseket, az érintett virtuális gép operációsrendszer-lemezének pillanatkép készítése a biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).

A probléma megoldásához, soros vezérlőelem használatával engedélyezze a DHCP- vagy [hálózati adapter alaphelyzetbe állítása](reset-network-interface.md) a virtuális gép számára.

### <a name="use-serial-control"></a>Soros vezérlőelem használata

1. Csatlakozás [soros konzolon és a nyílt CMD-példány](./serial-console-windows.md#open-cmd-or-powershell-in-serial-console
). Ha a soros konzol nincs engedélyezve a virtuális Gépen, [hálózati adapter alaphelyzetbe állítása](reset-network-interface.md).
2. Ellenőrizze, hogy ha a DHCP a hálózati adapter le van tiltva:

        netsh interface ip show config
3. Ha a DHCP le van tiltva, állítsa vissza a DHCP használatára a hálózati adapter konfigurációja:

        netsh interface ip set address name="<NIC Name>" source=dhc
        
    Például ha a interwork felület "Ethernet 2"-neveket, futtassa a következő parancsot:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. A lekérdezés az IP-konfigurációt, győződjön meg arról, hogy a hálózati adapter már helyesen van beállítva, hogy újra. Az új IP-cím egyeznie kell a az Azure által biztosított.

        netsh interface ip show config

    Nem kell ezen a ponton a virtuális gép újraindítására. A virtuális gép ismét elérhető lesz.

Ezt követően Ha azt szeretné, a statikus IP-cím konfigurálása a virtuális gép számára, lásd: [statikus IP-címek konfigurálása a virtuális gép](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).