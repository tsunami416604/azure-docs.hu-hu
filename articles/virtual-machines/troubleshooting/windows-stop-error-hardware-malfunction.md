---
title: Windows leállási hiba – Hardverhiba
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekben a Windows Server 2008 rendszerű virtuális gépek olyan hibaüzenettel ütköznek, amely a hardver meghibásodását jelzi.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: 18622d60f3a33658fadfd28c53c93a07b4b438a9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488646"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Windows leállási hiba – Hardverhiba

Ez a cikk olyan problémák megoldását ismerteti, amelyekben a Windows Server 2008 rendszerű virtuális gépek olyan hibaüzenettel ütköznek, amely a hardver meghibásodását jelzi.

## <a name="symptoms"></a>Hibajelenségek

Ha [rendszerindítási diagnosztika](./boot-diagnostics.md) használatával tekinti meg a virtuális gép képernyőképét, láthatja, hogy a képernyőképen egy kék képernyő jelenik meg a következő üzenettel:

**\*\*\* Hardver meghibásodása**

**A gyártó meghívása támogatásra**

**\*\*\* A szolgáltatás leállt \*\*\***

#### <a name="blue-screen"></a>Kék képernyő

![A képernyőképen egy kék képernyős hardver meghibásodási összeomlása látható.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>Soros konzol

![A képernyőképen a soros konzol funkciójának "hardverhiba" üzenete jelenik meg, ha a soros konzol engedélyezve van.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>Ok

Ez a képernyő akkor jelenik meg, ha a vendég operációs rendszer nem megfelelően lett beállítva, és nem maszkolt megszakítást (NMI) küldtek. A hibaüzenet azt jelzi, hogy egy kernel módú program kivételt generált, amelyet a kezelő nem tudott megfogni. Megadhatja, hogy melyik kivételt hozta létre a memóriakép begyűjtésével.

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése 

1. A nem maszkolásra szolgáló megszakítás (NMI) beállításkulcs beállítása 
2. Javítási virtuális gép létrehozása és elérése 
3. Soros konzol és memóriakép-gyűjtemény engedélyezése 
4. A virtuális gép újraépítése 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>A nem maszkolásra szolgáló megszakítás (NMI) beállításkulcs beállítása

1. A Azure Portal használatával indítsa újra a virtuális gépet úgy, hogy a vendég operációs rendszer rendesen elindul. 
2. Ha a virtuális géphez való hozzáférés vissza lett állítva, nyisson meg egy rendszergazda jogú parancssort (Futtatás rendszergazdaként). 
3. Állítsa be a NMI beállításkulcsot a következő paranccsal:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [További információk megtekintése a REG ADD paranccsal](/windows-server/administration/windows-commands/reg-add)
4. *(Nem kötelező)* Telepítési memóriakép gyűjteménye:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(Nem kötelező)* Soros konzolhoz való hozzáférés beállítása:

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [További információk megtekintése a BCDEDIT paranccsal kapcsolatban](/windows-server/administration/windows-commands/bcdedit)
6. Indítsa újra a virtuális gépet a következő paranccsal:

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [További információk megtekintése a leállítási parancsról](/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> A problémát most meg kell oldani!

> [!NOTE]
> Az újraindítás után tesztelje a virtuális gépet, és győződjön meg róla, hogy működik, mint a normál. Ha továbbra is problémákat tapasztal, további útmutatásért folytassa a következő szakasszal.

> [!TIP]
> Ajánlott a nem maszkolt megszakítás (NMI) beállításkulcs beállítása a fenti szakaszban, azonban ha a virtuális gép nem indul el rendesen, akkor előfordulhat, hogy a vendég operációs rendszer beállításjegyzékének módosítása nem történt meg. Ha ez a helyzet, az alábbi utasításokat követve manuálisan is hozzáadhatja a beállításjegyzék beállításait.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 1-3-es lépéseit használva készítse elő a javítási virtuális gépet.
2. Kapcsolódjon a javítási virtuális géphez Távoli asztali kapcsolat használatával.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Soros konzol és memóriakép-gyűjtemény engedélyezése

A virtuális gép újraépítése előtt ajánlott engedélyezni a memóriakép-gyűjtést és a soros konzolt. Ehhez futtassa a következő parancsfájlt: 

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (Futtatás rendszergazdaként). 
2. Sorolja fel a BCD-tároló adatait, és határozza meg a rendszerindítási betöltő azonosítóját, amelyet a következő lépésben fog használni. 
    1. 1. generációs virtuális gépek esetén írja be a következő parancsot, és jegyezze fel a felsorolt azonosítót: 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        A parancsban cserélje le a `<BOOT PARTITON>` betűt a partíció betűjelére a csatlakoztatott lemezen, amely tartalmazza a rendszerindító mappát. 

        ![A képernyőképen a BCD-tároló egy 1. generációs virtuális gépen való listázásának kimenete látható, amely a Windows rendszerindítási azonosító alatt található.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. A 2. generációs virtuális gépek esetében írja be a következő parancsot, és jegyezze fel a felsorolt azonosítót:
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * A parancsban cserélje le az `<LETTER OF THE EFI SYSTEM PARTITION>` EFI rendszerpartíció betűjét.
        * Hasznos lehet a Lemezkezelés konzol elindítása az *EFI rendszerpartícióként* címkézett megfelelő rendszerpartíció azonosításához.
        * Az azonosító lehet egyedi GUID, vagy lehet az alapértelmezett *Csizmadia*.
3. Futtassa a következő parancsokat a soros konzol engedélyezéséhez:

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * A parancsban cserélje le a helyére a `<VOLUME LETTER WHERE THE BCD FOLDER IS>` BCD-mappa betűjét.
    * A parancsban cserélje le `<BOOT LOADER IDENTIFIER>` az parancsot az előző lépésben megtalált azonosítóra.
4. Győződjön meg arról, hogy az operációsrendszer-lemez szabad területe nagyobb, mint a virtuális gép memóriájának mérete (RAM). 
    1. Ha nincs elég hely az operációsrendszer-lemezen, akkor módosítania kell a memóriakép fájljának helyét. Ahelyett, hogy a fájlt az operációsrendszer-lemezen hozza létre, áttekintheti a virtuális géphez csatlakoztatott bármely más adatlemezre, amely elegendő szabad hellyel rendelkezik. A hely módosításához cserélje le a **% systemroot%** betűjelet az alábbi parancsokban szereplő adatlemez meghajtóbetűjelére (például **F:**). 
    2. Adja meg az alábbi parancsokat (a javasolt memóriakép konfigurációja):

    **Töltse be a beállításjegyzék-struktúrát a hibás operációsrendszer-lemezről:**

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    **Engedélyezés a ControlSet001:**

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    **Engedélyezés a ControlSet002:**

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    **Sérült operációsrendszer-lemez eltávolítása:**

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```
### <a name="rebuild-the-virtual-machine"></a>A virtuális gép újraépítése

* A virtuális gép újraépítéséhez használja [a virtuális gép javítási parancsainak 5. lépését](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek rendszerindítási hibáinak elhárítása](./boot-error-troubleshoot.md)