---
title: Statikus IP-cím miatt nem lehet távoli asztalt az Azure virtuális gépekre. Microsoft dokumentumok
description: Megtudhatja, hogy miként háríthatja el a Microsoft Azure statikus IP-címe által okozott RDP-problémát.| Microsoft dokumentumok
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918189"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Statikus IP-cím miatt nem lehet távoli asztalt az Azure virtuális gépekre

Ez a cikk egy olyan problémát ismertet, amelyben nem lehet távoli asztalt az Azure Windows virtuális gépek (VM), miután egy statikus IP van konfigurálva a virtuális gép.


## <a name="symptoms"></a>Probléma

Amikor RDP-kapcsolatot hoz létre egy virtuális géptel az Azure-ban, a következő hibaüzenet jelenik meg:

**A Távoli asztal a következő okok miatt nem tud csatlakozni a távoli számítógéphez:**

1. **A kiszolgáló távoli elérése nincs engedélyezve**

2. **A távoli számítógép ki van kapcsolva**

3. **A távoli számítógép nem érhető el a hálózaton**

**Ellenőrizze, hogy a távoli számítógép be van-e kapcsolva, és csatlakozik-e a hálózathoz, és hogy a távelérés engedélyezve van-e.**

Amikor ellenőrzi a képernyőképet a [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) az Azure Portalon, a virtuális gép általában megjelenik, és várja a hitelesítő adatokat a bejelentkezési képernyőn.

## <a name="cause"></a>Ok

A virtuális gép rendelkezik egy statikus IP-címmel, amely a Windows hálózati adapterén van definiálva. Ez az IP-cím eltér az Azure Portalon megadott címtől.

## <a name="solution"></a>Megoldás

Az alábbi lépések végrehajtása előtt készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.

A probléma megoldásához használja a Soros vezérlőt a virtuális gép DHCP-címének engedélyezéséhez vagy a [hálózati kapcsolat alaphelyzetbe állításához.](reset-network-interface.md)

### <a name="use-serial-control"></a>Soros vezérlő használata

1. Csatlakozzon a [Soros konzolhoz, és nyissa meg a CMD-példányt.](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
) Ha a soros konzol nincs engedélyezve a virtuális gépen, olvassa el [a Hálózati adapter alaphelyzetbe állítása című témakört.](reset-network-interface.md)
2. Ellenőrizze, hogy a DHCP le van-e tiltva a hálózati adapteren:

        netsh interface ip show config
3. Ha a DHCP le van tiltva, a hálózati adapter konfigurációját visszakell állítani a DHCP használatához:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Ha például a munkaközi csatoló neve "Ethernet 2", futtassa a következő parancsot:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Ismét kérdezze le az IP-konfigurációt, és győződjön meg arról, hogy a hálózati adapter megfelelően van beállítva. Az új IP-címnek meg kell egyeznie az Azure által biztosított nak.

        netsh interface ip show config

    Ezen a ponton nem kell újraindítania a virtuális gép. A virtuális gép újra elérhető lesz.

Ezt követően, ha szeretné konfigurálni a statikus IP-cím a virtuális gép, [lásd: Statikus IP-címek konfigurálása a virtuális gép.](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)