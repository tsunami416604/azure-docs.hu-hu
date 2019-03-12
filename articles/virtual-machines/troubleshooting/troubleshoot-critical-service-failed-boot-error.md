---
title: Kritikus fontosságú szolgáltatás nem TUDTA egy Azure virtuális gép indításakor |} A Microsoft Docs
description: Ismerje meg, amikor indul el a "Kritikus 0x0000005A szolgáltatás sikertelen" hiba elhárítása |} A Microsoft Docs
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
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: 4d450bc4e05a70ff8c1698dd2760d0f71140db21
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763640"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Windows látható "Kritikus szolgáltatás sikertelen" kék képernyő egy Azure virtuális gép indításakor
Ez a cikk ismerteti a "Kritikus szolgáltatás sikertelen" hibát tapasztalhat, amikor elindítja a Windows virtuális gép (VM) a Microsoft Azure-ban. A problémák megoldása érdekében hibaelhárítási lépéseket biztosít. 

> [!NOTE] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti, javasoljuk, hogy az új központi telepítéseknél helyett a klasszikus üzemi modell használatával Resource Manager üzemi modell használatával.

## <a name="symptom"></a>Jelenség 

Windows virtuális gép nem indul el. Amikor ellenőrizheti a rendszerindító képernyőképek a [rendszerindítási diagnosztika](./boot-diagnostics.md), kék képernyőn láthatja az alábbi hibaüzenetek valamelyike:

- "A számítógép hibába ütközött a problémát, és indítsa újra kell. Újraindíthatja. További információt a problémáról és a lehetséges javításokat, http://windows.com/stopcode. Ha kapcsolatba lép a támogatási szakember, tegye lehetővé számukra a ezt az információt: Állítsa le a kódot: NEM SIKERÜLT A KRITIKUS FONTOSSÁGÚ SZOLGÁLTATÁS" 
- "A számítógép hibába ütközött a problémát, és indítsa újra kell. Csak adatok néhány hiba adatait, és hogy fog indítsa újra az Ön számára. Ha tudni szeretné, használja a keresési online később esetében ez a hiba: CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>Ok

A leállási hibáiról különböző oka is van. A leggyakoribb okok a következők:
- Egy illesztőprogram-probléma
- A rendszer sérült fájl vagy a memória
- Alkalmazás eléri egy tiltott szektor, a memória

## <a name="solution"></a>Megoldás 

Ez a probléma megoldásához [forduljon az ügyfélszolgálathoz, és küldje el a memóriakép-fájl](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), amelyek segítenek a probléma gyorsabb diagnosztizálása, vagy próbálja ki a következő önsegítő megoldást.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez

1. Pillanatkép készítése az operációsrendszer-lemez az érintett virtuális gépek biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).
2. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](./troubleshoot-recovery-disks-portal-windows.md). 
3. A helyreállítási virtuális Gépet egy távoli asztali kapcsolatot létesíteni.

### <a name="enable-dump-logs-and-serial-console"></a>Memóriakép naplók és a soros konzol engedélyezése

A memóriakép naplót és [soros konzol](./serial-console-windows.md) segíthet számunkra hajtsa végre a további hibaelhárítási.

Memóriakép naplók és a soros konzol engedélyezéséhez futtassa a következő szkriptet.

1. Nyisson meg egy rendszergazda jogú parancssort munkamenetet (Futtatás rendszergazdaként).
2. Futtassa a következő parancsfájlt:

    Ez a szkript feltételezzük, hogy a meghajtóbetűjel van rendelve a csatlakoztatott operációsrendszer-lemez-e f Cserélje le a megfelelő értékkel a virtuális géphez.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>Cserélje le az aláírás nélküli illesztőprogramok

1. A helyreállítási virtuális Gépet futtassa a következő parancsot egy rendszergazda jogú parancssorból. Ez a parancs beállítja az érintett operációsrendszer-lemez csökkentett módban a következő rendszerindítás idején történő elindításához:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Például ha az operációsrendszer-lemez csatlakoztatott meghajtó F, futtassa a következő parancsot:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Az operációsrendszer-lemez leválasztása, és ezután csatlakoztassa újra az érintett virtuális gépre az operációsrendszer-lemez](troubleshoot-recovery-disks-portal-windows.md). A virtuális gép csökkentett üzemmódban fog elindulni. Ha a hiba továbbra is működik, nyissa meg a választható lépés.
3. Nyissa meg a **futtatása** mezőbe, majd futtassa **ellenőrző** az illesztőprogram-ellenőrző eszköz elindításához.
4. Válassza ki **automatikusan kiválasztja az aláírás nélküli illesztőprogramok**, és kattintson a **tovább**.
5. Az illesztőprogram-fájlok, amelyek aláíratlan listája megjelenik. Ne feledje, hogy a fájl nevét.
6. Másolja ezeket a fájlokat ugyanazon verziója egy működő virtuális Gépet, és ezek aláíratlan fájlok esetén cserélje. 

7. A biztonságos rendszerindítási beállítások eltávolítása:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Indítsa újra a virtuális gépet. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Nem kötelező: A memóriakép-naplók módban összeomlási memóriakép elemzése

A memóriakép-naplók elemzése saját magának, kövesse az alábbi lépéseket:

1. Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez.
2. A csatolt operációsrendszer-lemezen tallózzon a **\windows\system32\config**. Az összes fájl másolása biztonsági mentéséhez, abban az esetben egy visszaállítási megadása kötelező.
3. Indítsa el **beállításjegyzék-szerkesztővel** (regedit.exe).
4. Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot. Válassza a menü **fájl** > **a struktúra betöltése**.
5. Keresse meg a **\windows\system32\config\SYSTEM** a csatolt operációsrendszer-lemez egy mappájába. A hive-neveként, írja be a **BROKENSYSTEM**. Az új beállításjegyzék-struktúrát alatt jelenik meg a **HKEY_LOCAL_MACHINE** kulcsot.
6. Keresse meg a **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** és a következő módosításokat:

    AutoReboot = 0

    CrashDumpEnabled = 2
7.  Válassza ki **BROKENSYSTEM**. Válassza a menü **fájl** > **struktúra**.
8.  Módosítsa a BCD-telepítő hibakeresési módban indul. Futtassa a következő parancsokat egy rendszergazda jogú parancssorból:

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Az operációsrendszer-lemez leválasztása, és ezután csatlakoztassa újra az érintett virtuális gépre az operációsrendszer-lemez](troubleshoot-recovery-disks-portal-windows.md).
10. Indítsa el a virtuális Gépet, hogy látható-e a memóriakép elemzése. Keresse meg a fájlt, amely nem sikerül betölteni. Cserélje le ezt a fájlt egy fájlt a működő virtuális kell. 

    Memóriakép elemzése mintája a következő: Láthatja, hogy a **hiba** filecrypt.sys van: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: http://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. A virtuális gép működik, és a normál módon történő indítása után távolítsa el az összeomlási memóriakép beállításokat:

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
