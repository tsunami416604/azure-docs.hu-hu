---
title: Az Azure-beli virtuális gép nem válaszol a szabályzat alkalmazásakor
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekben a betöltési képernyő beragadt, amikor egy Azure-beli virtuális gépen indít egy házirendet.
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
ms.openlocfilehash: 30f833bc49f92dcabfc75f0a1507c6f540bdea24
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749276"
---
# <a name="vm-becomes-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>A virtuális gép nem válaszol a "Csoportházirend helyi felhasználók & csoportok" házirend alkalmazása során

Ez a cikk olyan problémák megoldását ismerteti, amelyekben a betöltési képernyő beragadt, amikor egy Azure-beli virtuális gépen indít egy házirendet.

## <a name="symptoms"></a>Probléma

Ha [rendszerindítási diagnosztikát](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használ a virtuális gép képernyőképének megtekintéséhez, a képernyő betöltődik a következő üzenettel: "*csoportházirend helyi felhasználók és csoportok házirend alkalmazása*".

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Képernyőkép a Csoportházirend helyi felhasználók és csoportok betöltésének alkalmazásáról (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Képernyőkép a Csoportházirend helyi felhasználók és csoportok betöltésének alkalmazásáról (Windows Server 2012).":::

## <a name="cause"></a>Ok

Ütköző zárolások állnak fenn, ha a házirend megkísérli a régi felhasználói profilok törlését.

> [!NOTE]
> Ez csak a Windows Server 2012 és a Windows Server 2012 R2 rendszerre vonatkozik.

A problémás szabályzat:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Megoldás:

### <a name="process-overview"></a>Folyamat áttekintése

1. [Javítási virtuális gép létrehozása és elérése](#step-1-create-and-access-a-repair-vm)
2. [A szabályzat letiltása](#step-2-disable-the-policy)
3. [Soros konzol és memóriakép-gyűjtemény engedélyezése](#step-3-enable-serial-console-and-memory-dump-collection)
4. [A virtuális gép újraépítése](#step-4-rebuild-the-vm)

> [!NOTE]
> Ha ezt a rendszerindítási hibát tapasztalja, a vendég operációs rendszer nem működik. Kapcsolat nélküli módban kell elhárítani a hibakeresést.

### <a name="step-1-create-and-access-a-repair-vm"></a>1. lépés: a javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok 1-3-es lépéseit](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) használva készítse elő a javítási virtuális gépet.
2. Távoli asztali kapcsolat kapcsolódjon a javítási virtuális géphez.

### <a name="step-2-disable-the-policy"></a>2. lépés: a szabályzat letiltása

1. A javítási virtuális gépen nyissa meg a beállításszerkesztőt.
2. Keresse meg a kulcsot **HKEY_LOCAL_MACHINE** , és válassza a **fájl**  >  **betöltése struktúra...** lehetőséget a menüből.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="A képernyőképen a Kiemelt HKEY_LOCAL_MACHINE és a Load kaptárt tartalmazó menü látható.":::

    - A betöltési struktúra lehetővé teszi a beállításkulcsok offline rendszerből való betöltését, ebben az esetben a javítási virtuális géphez csatolt sérült lemezt.
    - A rendszerszintű beállítások tárolása a (z) rendszeren történik `HKEY_LOCAL_MACHINE` , és a "HKLM" rövidítéssel rövidíthető le.
3. A csatolt lemezen lépjen a `\windows\system32\config\SOFTWARE` fájlra, és nyissa meg.

    1. A rendszer kérni fogja a nevét. Adja meg a BROKENSOFTWARE.<br/>
    2. A BROKENSOFTWARE betöltésének ellenőrzéséhez bontsa ki **HKEY_LOCAL_MACHINE** és keresse meg a hozzáadott BROKENSOFTWARE kulcsot.
4. Keresse meg a BROKENSOFTWARE, és ellenőrizze, hogy létezik-e a CleanupProfile kulcs a betöltött struktúrában.

    1. Ha a kulcs létezik, a CleanupProfile házirend be van állítva, az érték a megőrzési szabályt napokban adja meg. Folytassa a kulcs törlését.<br/>
    2. Ha a kulcs nem létezik, a CleanupProfile házirend nincs beállítva. [Küldjön be egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), beleértve a mellékelt operációsrendszer-lemez Windows-könyvtárában található Memory. dmp fájlt.

5. Törölje a CleanupProfiles kulcsot a következő parancs használatával:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
6.  Távolítsa el a BROKENSOFTWARE-struktúrát a következő parancs használatával:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>3. lépés: a soros konzol és a memóriaképek gyűjtésének engedélyezése

A memóriakép-gyűjtés és a soros konzol engedélyezéséhez futtassa a következő parancsfájlt:

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (Futtatás rendszergazdaként).
2. Futtassa a következő parancsokat:

    **Soros konzol engedélyezése**: 
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
3. Ellenőrizze, hogy az operációsrendszer-lemez szabad területe eléri-e a virtuális gép memóriájának méretét (RAM).

    Ha nincs elég hely az operációsrendszer-lemezen, módosítsa a memóriakép helyét, és hivatkozzon egy olyan csatolt adatlemezre, amely elegendő szabad hellyel rendelkezik. A hely módosításához cserélje le a "% SystemRoot%" betűjelet az alábbi parancsokban lévő adatlemez meghajtóbetűjelére (pl. "F:").

    **Az operációs rendszer memóriaképének engedélyezéséhez javasolt konfiguráció**:

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

A virtuális gép újraösszeállításához használja [a virtuális gép javítási parancsainak 5. lépését](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) .

Ha a probléma kijavítva van, a házirend helyileg le van tiltva. Állandó megoldásként ne használjon CleanupProfiles házirendet a virtuális gépeken. Használjon más módszert a profilok tisztításának elvégzéséhez.

Ne használja ezt a házirendet:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>További lépések

Ha a Windows Update alkalmazása során problémák merülnek fel, tekintse meg [a virtuális gép nem válaszol "C01A001D" hibaüzenettel a Windows Update alkalmazásakor](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update).