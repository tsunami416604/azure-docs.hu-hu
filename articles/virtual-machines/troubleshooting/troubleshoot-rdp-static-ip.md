---
title: Statikus IP-cím miatt nem lehet a távoli asztal az Azure Virtual Machines |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a statikus IP-cím a Microsoft Azure által okozott probléma RDP. |} A Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 92ad33fbc759605ae901c3bcf09283c8e0b1c4b5
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918189"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Az Azure Virtual Machines a távoli asztal nem statikus IP-cím miatt

Ez a cikk ismerteti a problémát, amelyben nem lehet a távoli asztal az Azure Windows Virtual Machines (VM) a virtuális gép statikus IP-cím konfigurálása után.


## <a name="symptoms"></a>Probléma

Az Azure-beli virtuális gép RDP-kapcsolatának hajt végre, amikor a következő hibaüzenet jelenhet meg:

**Távoli asztal nem tud csatlakozni a távoli számítógéphez a következő okok valamelyike miatt:**

1. **A kiszolgálóhoz való távoli hozzáférés nincs engedélyezve**

2. **A távoli számítógép ki van kapcsolva**

3. **A távoli számítógép nem érhető el a hálózaton**

**Ellenőrizze, hogy a távoli számítógép be van-e kapcsolva és csatlakoztatva van-e a hálózathoz, és hogy engedélyezve van-e a távoli hozzáférés.**

Amikor bejelöli a képernyőképet a Azure Portal [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) eszközében, a virtuális gép rendesen elindul, és megvárja a hitelesítő adatokat a bejelentkezési képernyőn.

## <a name="cause"></a>Ok

A virtuális gép rendelkezik definiált Windows belül a hálózati adapter statikus IP-címet. Az IP-cím eltér a cím, amelyet az Azure Portalon van definiálva.

## <a name="solution"></a>Megoldás

Mielőtt végrehajtaná ezeket a lépéseket, az érintett virtuális gép operációsrendszer-lemezének pillanatkép készítése a biztonsági mentéséhez. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).

A probléma megoldásához a soros vezérlőelem használatával engedélyezze a DHCP-t, vagy állítsa vissza a virtuális gép [hálózati adapterét](reset-network-interface.md) .

### <a name="use-serial-control"></a>Soros vezérlőelem használata

1. Kapcsolódjon a [soros konzolhoz, és nyissa meg a cmd-példányt](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Ha a soros konzol nincs engedélyezve a virtuális gépen, tekintse meg a [hálózati adapter alaphelyzetbe állítása](reset-network-interface.md)című témakört.
2. Ellenőrizze, hogy ha a DHCP a hálózati adapter le van tiltva:

        netsh interface ip show config
3. Ha a DHCP le van tiltva, állítsa vissza a DHCP használatára a hálózati adapter konfigurációja:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Például ha a interwork felület "Ethernet 2"-neveket, futtassa a következő parancsot:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. A lekérdezés az IP-konfigurációt, győződjön meg arról, hogy a hálózati adapter már helyesen van beállítva, hogy újra. Az új IP-cím egyeznie kell a az Azure által biztosított.

        netsh interface ip show config

    Nem kell ezen a ponton a virtuális gép újraindítására. A virtuális gép ismét elérhető lesz.

Ezt követően, ha a statikus IP-címet szeretné konfigurálni a virtuális géphez, tekintse meg a [statikus IP-címek konfigurálása virtuális géphez](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)című témakört.