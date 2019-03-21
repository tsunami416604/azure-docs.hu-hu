---
title: Egy netvsc.sys probléma elhárításához, ha távolról csatlakozik a Windows 10-es vagy Windows Server 2016 virtuális gép az Azure-ban |} A Microsoft Docs
description: Megtudhatja, hogyan háríthatók el a netsvc.sys kapcsolatos RDP adja ki, mikor, Windows 10-es vagy Windows Server 2016 az Azure-beli virtuális gép csatlakozik.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: e6685a5e77d92bb9e05ab9578e48c99e80a64b74
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57994624"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Nem lehet távoli csatlakozás a Windows 10-es vagy Windows Server 2016 virtuális gép az Azure-ban netvsc.sys miatt

Ez a cikk azt ismerteti, hogyan háríthatók el a problémát, amelyben nincs hálózati kapcsolat a Windows 10-es vagy Windows Server 2016 Datacenter virtuális gép (VM) a Hyper-V Server 2016 gazdagépen való csatlakozáskor.

## <a name="symptoms"></a>Probléma

Nem lehet csatlakoztatni az Azure Windows 10-es vagy Windows Server 2016 virtuális gép távoli asztali protokoll (RDP) használatával. A [rendszerindítási diagnosztika](boot-diagnostics.md), a képernyőn látható vörös keresztre keresztül a hálózati kártya (NIC). Ez azt jelzi, hogy a virtuális gép nem rendelkezik kapcsolattal az operációs rendszer teljes betöltése után.

Általában a probléma akkor fordul elő, a Windows [összeállítása 14393](https://support.microsoft.com/help/4093120/) és [15063 összeállítása](https://support.microsoft.com/help/4015583/). Ha az operációs rendszer verziója újabb, mint a ezeket a verziókat, ez a cikk nem vonatkozik a forgatókönyvéhez. Ellenőrizze a verziót, a rendszer, nyissa meg a CMD munkamenet [a soros hozzáférés funkció](serial-console-windows.md), majd futtassa a **Ver**.

## <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha a telepített netvsc.sys rendszerfájl verziója **10.0.14393.594** vagy **10.0.15063.0**. Ezen verziói netvsc.sys megakadályozhatja, hogy a rendszer és az Azure platform használatához.


## <a name="solution"></a>Megoldás

Ezek a lépések végrehajtása előtt [pillanatkép készítése a rendszerlemez](../windows/snapshot-copy-managed-disk.md) az érintett virtuális gép biztonsági mentéséhez. A probléma elhárításához, használja a soros konzol vagy [javítsa ki a virtuális Gépet offline](#repair-the-vm-offline) egy helyreállítási virtuális Géphez a rendszer a virtuális gép lemezének csatolásával.


### <a name="use-the-serial-console"></a>A soros konzol használata

Csatlakozás [a soros konzol, nyisson meg egy PowerShell-példány](serial-console-windows.md), és kövesse az alábbi lépéseket.

> [!NOTE]
> Ha a soros konzol nincs engedélyezve a virtuális Gépen, nyissa meg a [javítsa ki a virtuális Gépet offline](#repair-the-vm-offline) szakaszban.

1. Az alábbi parancsot egy PowerShell-példány beolvasása a fájl verziója (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Töltse le a frissítést, amely csatolva van egy működő virtuális gép ugyanabban a régióban az új vagy meglévő adatok lemezre:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) vagy újabb frissítése
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) vagy újabb frissítése

3. Válassza le a segédprogram a működő virtuális lemezt, és mellékelje a hibás virtuális Gépet.

4. Futtassa a következő parancsot a frissítés telepítése a virtuális gépen:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Indítsa újra a virtuális gépet.

### <a name="repair-the-vm-offline"></a>Javítsa ki a virtuális Gépet kapcsolat nélküli módban

1. [A rendszer lemez csatolása egy helyreállítási virtuális Géphez](../windows/troubleshoot-recovery-disks-portal.md).

2. Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot.

3. Győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatolt rendszerlemez van rendelve.

4. Másolatot készít a **\Windows\System32\config** abban az esetben egy visszaállítási a módosításokat a mappa szükség.

5. A virtuális gép mentési indítsa el a Beállításszerkesztőt (regedit.exe).

6. Válassza ki a **HKEY_LOCAL_MACHINE** kulcsát, és válassza ki **fájl** > **a struktúra betöltése** a menüből.

7. Keresse meg a rendszer fájlt a **\Windows\System32\config** mappát.

8. Válassza ki **nyílt**, típusa **BROKENSYSTEM** nevét, bontsa ki a **HKEY_LOCAL_MACHINE** kulcsát, és keresse meg a további kulcs nevű **BROKENSYSTEM** .

9. Nyissa meg a következő helyen:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Minden egyes alkulcsban (például a 0000), vizsgálja meg a **DriverDesc** jelzéssel megjelenített érték **Microsoft HYPER-V hálózati Adapter**.

11. Tekintse át az alkulcs a **DriverVersion** érték, amely a hálózati adaptert a virtuális gép illesztőprogram verziója.

12. Töltse le a frissítést:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) vagy újabb frissítése
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) vagy újabb frissítése

13. A rendszer lemez csatolása egy mentési virtuális gépen, amelyen a frissítés letölthető adatlemezként.

14. Futtassa a következő parancsot a frissítés telepítése a virtuális gépen:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Futtassa a struktúrák leválasztása a következő parancsot:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Válassza le a rendszer lemezt, és hozza létre újra a virtuális gép](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [Azure támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
