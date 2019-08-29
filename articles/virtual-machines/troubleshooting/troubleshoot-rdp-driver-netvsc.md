---
title: Netvsc. sys probléma megoldása, ha távolról csatlakozik egy Windows 10 vagy Windows Server 2016 rendszerű virtuális géphez az Azure-ban | Microsoft Docs
description: Ismerje meg, hogyan lehet elhárítani a Netsvc. sys-sel kapcsolatos RDP-problémákat, amikor Windows 10-es vagy Windows Server 2016 rendszerű virtuális géphez csatlakozik az Azure-ban.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 6e68aac07379de142968b85884e7dbd95e73195f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103475"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Nem lehet távolról csatlakozni a Windows 10 vagy Windows Server 2016 rendszerű virtuális géphez az Azure-ban a netvsc. sys miatt

Ez a cikk azt ismerteti, hogyan lehet elhárítani egy olyan problémát, amelyben nincs hálózati kapcsolat, ha Windows 10 vagy Windows Server 2016 Datacenter rendszerű virtuális géphez (VM) csatlakozik a Hyper-V Server 2016 gazdagépen.

## <a name="symptoms"></a>Probléma

RDP protokoll (RDP) használatával nem lehet csatlakozni Azure Windows 10 vagy Windows Server 2016 rendszerű virtuális géphez. A [rendszerindítási diagnosztika](boot-diagnostics.md)képernyőn a képernyő piros keresztet mutat a hálózati adapteren (NIC). Ez azt jelzi, hogy a virtuális gépnek nincs kapcsolata, miután az operációs rendszer teljesen betöltődött.

Ez a probléma általában a Windows [build 14393](https://support.microsoft.com/help/4093120/) -es és a [15063](https://support.microsoft.com/help/4015583/)-es verziójában fordul elő. Ha az operációs rendszer verziója a verziónál újabb, akkor ez a cikk nem vonatkozik a forgatókönyvre. A rendszer verziójának vizsgálatához nyisson meg egy CMD-munkamenetet [a soros hozzáférési konzol szolgáltatásban](serial-console-windows.md), majd futtassa a **ver**parancsot.

## <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a telepített netvsc. sys rendszerfájl verziója **10.0.14393.594** vagy **10.0.15063.0**. A netvsc. sys ezen verziói megakadályozhatják, hogy a rendszer kommunikáljon az Azure platformmal.


## <a name="solution"></a>Megoldás

Az alábbi lépések elvégzése [](../windows/snapshot-copy-managed-disk.md) előtt készítsen pillanatképet az érintett virtuális gép rendszerlemezéről biztonsági másolatként. A probléma elhárításához, használja a soros konzol vagy [javítsa ki a virtuális Gépet offline](#repair-the-vm-offline) egy helyreállítási virtuális Géphez a rendszer a virtuális gép lemezének csatolásával.


### <a name="use-the-serial-console"></a>A soros konzol használata

Kapcsolódjon [a soros konzolhoz, nyisson meg egy PowerShell-példányt](serial-console-windows.md), majd kövesse az alábbi lépéseket.

> [!NOTE]
> Ha a soros konzol nincs engedélyezve a virtuális Gépen, nyissa meg a [javítsa ki a virtuális Gépet offline](#repair-the-vm-offline) szakaszban.

1. Futtassa a következő parancsot egy PowerShell-példányban a fájl verziójának (**c:\windows\system32\drivers\netvsc.sys**) lekéréséhez:

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Töltse le a megfelelő frissítést egy olyan új vagy meglévő adatlemezre, amely ugyanahhoz a régióhoz tartozó működő virtuális géphez van csatolva:

   - **10.0.14393.594**: [](https://support.microsoft.com/help/4073562)KB4073562 vagy újabb frissítés
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) vagy újabb frissítés

3. Válassza le a segédprogram lemezét a működő virtuális gépről, majd csatolja a hibás virtuális géphez.

4. Futtassa a következő parancsot a frissítés telepítéséhez a virtuális gépen:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Indítsa újra a virtuális gépet.

### <a name="repair-the-vm-offline"></a>Javítsa ki a virtuális Gépet kapcsolat nélküli módban

1. [A rendszer lemez csatolása egy helyreállítási virtuális Géphez](../windows/troubleshoot-recovery-disks-portal.md).

2. Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot.

3. Győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatolt rendszerlemez van rendelve.

4. Hozzon létre egy másolatot a **\Windows\System32\config** mappából abban az esetben, ha a módosítások visszaállítására van szükség.

5. A mentési virtuális gépen indítsa el a Beállításszerkesztőt (Regedit. exe).

6. Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot, majd válassza a **fájl** > **Load kaptár** lehetőséget a menüből.

7. Keresse meg a rendszerfájlt a **\Windows\System32\config** mappában.

8. Válassza a **Megnyitás**elemet, írja be a **BROKENSYSTEM** nevet, bontsa ki a **HKEY_LOCAL_MACHINE** kulcsot, majd keresse meg a **BROKENSYSTEM**nevű további kulcsot.

9. Lépjen a következő helyre:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Minden alkulcsban (például 0000) ellenőrizze a **Microsoft Hyper-V hálózati adapterként**megjelenő **DriverDesc** értéket.

11. Az alkulcsban vizsgálja meg a virtuális gép hálózati adapterének illesztőprogram-verzióját tartalmazó **DriverVersion** értéket.

12. Töltse le a megfelelő frissítést:

    - **10.0.14393.594**: [](https://support.microsoft.com/help/4073562)KB4073562 vagy újabb frissítés
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) vagy újabb frissítés

13. Csatlakoztassa a rendszerlemezt adatlemezként egy olyan mentési virtuális gépen, amelyen le tudja tölteni a frissítést.

14. Futtassa a következő parancsot a frissítés telepítéséhez a virtuális gépen:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Futtassa a következő parancsot a kaptárak leválasztásához:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Válassza le a rendszerlemezt, és hozza létre újra a virtuális gépet](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha továbbra is segítségre van szüksége, [forduljon az Azure támogatási szolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , hogy gyorsan feloldja a problémát.
