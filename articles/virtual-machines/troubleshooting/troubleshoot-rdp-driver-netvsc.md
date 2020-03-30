---
title: Netvsc.sys probléma elhárítása, ha távolról csatlakozik egy Windows 10 vagy Windows Server 2016 virtuális géphez az Azure-ban | Microsoft dokumentumok
description: Megtudhatja, hogy miként háríthatja el a netsvc.sys fájllal kapcsolatos RDP-problémát, amikor Windows 10 vagy Windows Server 2016 virtuális géphez csatlakozik az Azure-ban.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 4c10a2dcd55c1605cfafe6c67cfefd9d8a3c5f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057988"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>A netvsc.sys miatt nem lehet távolról csatlakozni Windows 10 vagy Windows Server 2016 virtuális géphez az Azure-ban

Ez a cikk azt ismerteti, hogy miként hárítható el az a hiba, amely miatt nincs hálózati kapcsolat, amikor Hyper-V Server 2016-állomáson Windows 10 vagy Windows Server 2016 datacenter virtuális géphez (VM) csatlakozik.

## <a name="symptoms"></a>Probléma

Az Azure Windows 10 vagy a Windows Server 2016 virtuális géphez nem csatlakozhat a Távoli asztali protokoll (RDP) használatával. A [Rendszerindításdia](boot-diagnostics.md)képernyőn piros kereszt látható a hálózati csatolókártyán (NIC). Ez azt jelzi, hogy a virtuális gép nem rendelkezik kapcsolattal az operációs rendszer teljes betöltése után.

Ez a probléma általában a [Windows 14393 és](https://support.microsoft.com/help/4093120/) [15063-as buildben](https://support.microsoft.com/help/4015583/)fordul elő. Ha az operációs rendszer verziója későbbi, mint ezek a verziók, ez a cikk nem vonatkozik a forgatókönyvre. A rendszer verziójának ellenőrzéséhez nyisson meg egy CMD-munkamenetet [a Soros access konzol szolgáltatásban,](serial-console-windows.md)majd futtassa a **Ver**programot.

## <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a telepített netvsc.sys rendszerfájl verziója **10.0.14393.594** vagy **10.0.15063.0**. A netvsc.sys ezen verziói megakadályozhatják, hogy a rendszer kölcsönhatásba lépjen az Azure platformmal.


## <a name="solution"></a>Megoldás

Az alábbi lépések végrehajtása előtt készítsen pillanatképet az érintett virtuális gép [rendszerlemezéről](../windows/snapshot-copy-managed-disk.md) biztonsági másolatként. A probléma elhárításához használja a soros konzolt, vagy [javítsa ki a virtuális gép offline](#repair-the-vm-offline) állapotba helyezését a virtuális gép rendszerlemezének helyreállítási virtuális géphez csatlakoztatásával.


### <a name="use-the-serial-console"></a>A soros konzol használata

Csatlakozzon [a soros konzolhoz, nyisson meg egy PowerShell-példányt,](serial-console-windows.md)majd kövesse az alábbi lépéseket.

> [!NOTE]
> Ha a soros konzol nincs engedélyezve a virtuális gép, nyissa meg a [javítás a virtuális gép offline](#repair-the-vm-offline) szakaszban.

1. Futtassa a következő parancsot egy PowerShell-példányban a fájl verziójának levételéhez (**c:\windows\system32\drivers\netvsc.sys):**

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Töltse le a megfelelő frissítést egy új vagy meglévő adatlemezre, amely ugyanabból a régióból származó működő virtuális géphez van csatolva:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) vagy újabb frissítés
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) vagy újabb frissítés

3. Válassza le a közüzemi lemezt a működő virtuális gépről, majd csatlakoztassa a hibás virtuális géphez.

4. Futtassa a következő parancsot a frissítés telepítéséhez a virtuális gépre:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Indítsa újra a virtuális gépet.

### <a name="repair-the-vm-offline"></a>A virtuális gép offline javítása

1. [Csatlakoztassa a rendszerlemezt egy helyreállítási virtuális géphez.](../windows/troubleshoot-recovery-disks-portal.md)

2. Távoli asztali kapcsolat indítása a helyreállítási virtuális géppel.

3. Győződjön meg arról, hogy a lemez **online** ként van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott rendszerlemezhez rendelt meghajtóbetűjelet.

4. Hozzon létre másolatot a **\Windows\System32\config** mappáról arra az esetre, ha a módosítások visszaállítása szükséges.

5. A mentési virtuális gépen indítsa el a Rendszerleíróadatbázis-szerkesztőt (regedit.exe).

6. Jelölje ki a **HKEY_LOCAL_MACHINE** kulcsot, majd válassza a menü**Fájlbetöltési** **File** > struktúra parancsát.

7. Keresse meg a RENDSZER fájlt a **\Windows\System32\config** mappában.

8. Válassza a **Megnyitás**lehetőséget, írja be a **névhez** a BROKENSYSTEM parancsot, bontsa ki a **HKEY_LOCAL_MACHINE** kulcsot, majd keresse meg a **BROKENSYSTEM**nevű további kulcsot.

9. Ugrás a következő helyre:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Minden alkulcsban (például 0000-ben) vizsgálja meg a **Microsoft HYPER-V hálózati adapterként**megjelenő **DriverDesc** értéket.

11. Az alkulcsban vizsgálja meg a **DriverVersion** értéket, amely a virtuális gép hálózati adapterének illesztőprogram-verziója.

12. Töltse le a megfelelő frissítést:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) vagy újabb frissítés
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) vagy újabb frissítés

13. Csatlakoztassa a rendszerlemezt adatlemezként egy mentési virtuális géphez, amelyre letöltheti a frissítést.

14. Futtassa a következő parancsot a frissítés telepítéséhez a virtuális gépre:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. A csalánkiütés leválasztásához futtassa a következő parancsot:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Válassza le a rendszerlemezt, és hozza létre újra a virtuális gép .](../windows/troubleshoot-recovery-disks-portal.md)

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, lépjen kapcsolatba az [Azure-támogatással](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldásához.
