---
title: netvsc.sys probléma hibaelhárítása, amikor távolról csatlakozik egy Windows 10 vagy Windows Server 2016 rendszerű virtuális géphez az Azure-ban | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a netsvc.sys kapcsolatos RDP-problémákat, amikor Windows 10-es vagy Windows Server 2016 rendszerű virtuális géphez csatlakozik az Azure-ban.
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
ms.openlocfilehash: 0f5a414f00ffa50114f090fc19f37b8a85428547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86509001"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Nem lehet távolról csatlakozni a Windows 10 vagy Windows Server 2016 rendszerű virtuális géphez az Azure-ban netvsc.sys

Ez a cikk azt ismerteti, hogyan lehet elhárítani egy olyan problémát, amelyben nincs hálózati kapcsolat, ha Windows 10 vagy Windows Server 2016 Datacenter rendszerű virtuális géphez (VM) csatlakozik a Hyper-V Server 2016 gazdagépen.

## <a name="symptoms"></a>Hibajelenségek

RDP protokoll (RDP) használatával nem lehet csatlakozni Azure Windows 10 vagy Windows Server 2016 rendszerű virtuális géphez. A [rendszerindítási diagnosztika](boot-diagnostics.md)képernyőn a képernyő piros keresztet mutat a hálózati adapteren (NIC). Ez azt jelzi, hogy a virtuális gépnek nincs kapcsolata, miután az operációs rendszer teljesen betöltődött.

Ez a probléma általában a Windows [build 14393](https://support.microsoft.com/help/4093120/) -es és a 15063-es [verziójában](https://support.microsoft.com/help/4015583/)fordul elő. Ha az operációs rendszer verziója a verziónál újabb, akkor ez a cikk nem vonatkozik a forgatókönyvre. A rendszer verziójának vizsgálatához nyisson meg egy CMD-munkamenetet [a soros hozzáférési konzol szolgáltatásban](serial-console-windows.md), majd futtassa a **ver**parancsot.

## <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a telepített netvsc.sys rendszerfájl verziója **10.0.14393.594** vagy **10.0.15063.0**. A netvsc.sys ezen verziói megakadályozhatják, hogy a rendszer interakcióba kerüljön az Azure platformmal.


## <a name="solution"></a>Megoldás

Az alábbi lépések [elvégzése előtt készítsen pillanatképet](../windows/snapshot-copy-managed-disk.md) az érintett virtuális gép rendszerlemezéről biztonsági másolatként. A probléma elhárításához használja a soros konzolt, vagy [javítsa ki a virtuális gépet](#repair-the-vm-offline) úgy, hogy a virtuális gép rendszerlemezét egy helyreállítási virtuális géphez csatolja.


### <a name="use-the-serial-console"></a>A soros konzol használata

Kapcsolódjon [a soros konzolhoz, nyisson meg egy PowerShell-példányt](serial-console-windows.md), majd kövesse az alábbi lépéseket.

> [!NOTE]
> Ha a soros konzol nincs engedélyezve a virtuális gépen, lépjen a [virtuális gép kijavítása offline](#repair-the-vm-offline) szakaszra.

1. Futtassa a következő parancsot egy PowerShell-példányban a fájl verziójának lekéréséhez (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Töltse le a megfelelő frissítést egy olyan új vagy meglévő adatlemezre, amely ugyanahhoz a régióhoz tartozó működő virtuális géphez van csatolva:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562)   vagy újabb frissítés
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) vagy újabb frissítés

3. Válassza le a segédprogram lemezét a működő virtuális gépről, majd csatolja a hibás virtuális géphez.

4. Futtassa a következő parancsot a frissítés telepítéséhez a virtuális gépen:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Indítsa újra a virtuális gépet.

### <a name="repair-the-vm-offline"></a>A virtuális gép kijavítása kapcsolat nélküli üzemmódban

1. [Csatlakoztassa a rendszerlemezt egy helyreállítási virtuális géphez](./troubleshoot-recovery-disks-portal-windows.md).

2. Távoli asztal-Kapcsolódás elindítása a helyreállítási virtuális géphez.

3. Győződjön meg arról, hogy a lemez **online** állapotban van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott rendszerlemezhez rendelt meghajtóbetűjelet.

4. Hozzon létre egy másolatot a **\Windows\System32\config** mappából abban az esetben, ha a módosítások visszaállítására van szükség.

5. A mentési virtuális gépen indítsa el a Beállításszerkesztőt (regedit.exe).

6. Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot, majd válassza a **fájl**  >  **Load kaptár** elemet a menüből.

7. Keresse meg a rendszerfájlt a **\Windows\System32\config** mappában.

8. Válassza a **Megnyitás**elemet, írja be a **BROKENSYSTEM** nevet, bontsa ki a **HKEY_LOCAL_MACHINE** kulcsot, majd keresse meg a **BROKENSYSTEM**nevű további kulcsot.

9. Lépjen a következő helyre:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Minden alkulcsban (például 0000) ellenőrizze a **Microsoft Hyper-V hálózati adapterként**megjelenő **DriverDesc** értéket.

11. Az alkulcsban vizsgálja meg a virtuális gép hálózati adapterének illesztőprogram-verzióját tartalmazó **DriverVersion** értéket.

12. Töltse le a megfelelő frissítést:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562)   vagy újabb frissítés
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

16. [Válassza le a rendszerlemezt, és hozza létre újra a virtuális gépet](./troubleshoot-recovery-disks-portal-windows.md).

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az Azure támogatási szolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , hogy gyorsan feloldja a problémát.
