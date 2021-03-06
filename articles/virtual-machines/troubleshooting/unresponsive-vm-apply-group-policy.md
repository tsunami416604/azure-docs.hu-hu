---
title: Az Azure-beli virtuális gép nem válaszol a szabályzat alkalmazásakor
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekben a betöltési képernyő nem válaszol, amikor egy Azure-beli virtuális gépen indít egy házirendet.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: cbf2fe491e1fe0b553eab04ca7190da0413a3ba6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526010"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>A virtuális gép nem válaszol Csoportházirend helyi felhasználók és csoportok házirend alkalmazásakor

Ez a cikk olyan problémák megoldását ismerteti, amelyekben a betöltési képernyő nem válaszol, ha egy Azure-beli virtuális gép (VM) az indításkor alkalmazza a házirendet.

## <a name="symptoms"></a>Hibajelenségek

Ha [rendszerindítási diagnosztikát](./boot-diagnostics.md) használ a virtuális gép képernyőképének megtekintéséhez, a képernyő betöltődik a következő üzenettel: "csoportházirend helyi felhasználók és csoportok házirend alkalmazása".

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Képernyőkép a Csoportházirend helyi felhasználók és csoportok betöltésének alkalmazásáról (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Képernyőkép a Csoportházirend helyi felhasználók és csoportok betöltésének alkalmazásáról (Windows Server 2012 R2).":::

## <a name="cause"></a>Ok

Ütköző zárolások állnak fenn, ha a házirend megpróbálja törölni a régi felhasználói profilokat.

> [!NOTE]
> Ez csak a Windows Server 2012 és a Windows Server 2012 R2 rendszerre vonatkozik.

A problémás szabályzat:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Feloldás

### <a name="process-overview"></a>Folyamat áttekintése

1. [Javítási virtuális gép létrehozása és elérése](#step-1-create-and-access-a-repair-vm)
1. [A szabályzat letiltása](#step-2-disable-the-policy)
1. [Soros konzol és memóriakép-gyűjtemény engedélyezése](#step-3-enable-serial-console-and-memory-dump-collection)
1. [A virtuális gép újraépítése](#step-4-rebuild-the-vm)

> [!NOTE]
> Ha ezt a rendszerindítási hibát tapasztalja, a vendég operációs rendszer nem működik. Kapcsolat nélküli módban kell elhárítani a hibakeresést.

### <a name="step-1-create-and-access-a-repair-vm"></a>1. lépés: a javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok 1-3-es lépéseit](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) használva készítse elő a javítási virtuális gépet.
2. A Távoli asztali kapcsolat használatával csatlakozhat a javítási virtuális géphez.

### <a name="step-2-disable-the-policy"></a>2. lépés: a szabályzat letiltása

1. A javítási virtuális gépen nyissa meg a beállításszerkesztőt.
1. Keresse meg a kulcsot **HKEY_LOCAL_MACHINE** , és válassza a **fájl**  >  **Load struktúra** elemet a menüből.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="Képernyőkép a Csoportházirend helyi felhasználók és csoportok betöltésének alkalmazásáról (Windows Server 2012 R2)." /v CleanupProfiles /f
    ```
1.  Távolítsa el a BROKENSOFTWARE-struktúrát a következő parancs használatával:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>3. lépés: a soros konzol és a memóriaképek gyűjtésének engedélyezése

A memóriakép-gyűjtés és a soros konzol engedélyezéséhez futtassa a következő parancsfájlt:

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet. (Futtatás rendszergazdaként.)
1. Futtassa ezeket a parancsokat a soros konzol engedélyezéséhez:
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. Ellenőrizze, hogy az operációsrendszer-lemez szabad területe eléri-e a virtuális gép memóriájának méretét (RAM).

    Ha nincs elég hely az operációsrendszer-lemezen, módosítsa a memóriakép helyét, és hivatkozzon egy olyan csatolt adatlemezre, amely elegendő szabad hellyel rendelkezik. A hely módosításához cserélje le a "% SystemRoot%" kifejezést a meghajtóbetűjelre (például "F:") az adatlemezre a következő parancsokban.

    **Az operációs rendszer kiírásának engedélyezése javasolt konfiguráció**

    Sérült operációsrendszer-lemez betöltése:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Engedélyezés a ControlSet001:
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    Engedélyezés a ControlSet002:
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```
    
    Sérült operációsrendszer-lemez eltávolítása:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="step-4-rebuild-the-vm"></a>4. lépés: a virtuális gép újraépítése

A virtuális gép újraösszeállításához használja [a virtuális gép javítási parancsainak 5. lépését](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .

Ha a probléma kijavítva van, a házirend mostantól helyileg le lesz tiltva. Állandó megoldásként ne használja a CleanupProfiles házirendet a virtuális gépeken. Használjon más módszert a profilok tisztításának elvégzéséhez.

Ne használja ezt a házirendet:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>További lépések

Ha a Windows Update alkalmazása során problémák merülnek fel, tekintse meg [a virtuális gép nem válaszol "C01A001D" hibaüzenettel a Windows Update alkalmazásakor](./unresponsive-vm-apply-windows-update.md).
