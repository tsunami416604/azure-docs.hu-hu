---
title: Kritikus szolgáltatás sikertelen azure-beli virtuális gép indításakor | Microsoft dokumentumok
description: Ismerje meg, hogyan hárítható el a rendszerindításkor előforduló "0x00000005A-CRITICAL SERVICE FAILED" hiba | Microsoft dokumentumok
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: 54ba87b681a055bb46b81ca81d2bcdd103491f27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921453"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>A Windows a "KRITIKUS SZOLGÁLTATÁS SIKERTELEN" kifejezést jeleníti meg a kék képernyőn az Azure virtuális gép indításakor
Ez a cikk a "KRITIKUS SZOLGÁLTATÁS SIKERTELEN" hibaüzenetet ismerteti, amely a Windows virtuális gép (VM) Microsoft Azure-beli indításakor jelenhet meg. Hibaelhárítási lépéseket tartalmaz a problémák megoldásához. 


## <a name="symptom"></a>Hibajelenség 

A Windows virtuális gép nem indul el. Amikor a rendszerindítási képernyőképeket a [Rendszerindítási diagnosztika](./boot-diagnostics.md)alkalmazásban ellenőrzi, kék képernyőn az alábbi hibaüzenetek egyike jelenik meg:

- "A számítógép egy problémába ütközött, és újra kell indítania. Újraindíthatja. A problémával és a lehetséges megoldásokkal kapcsolatos további információkért látogasson el a . https://windows.com/stopcode Ha ön hív egy támogat személy, ad őket ez értesít: Megáll kód: BÍRÁLÓ SZOLGÁLTATÁS MEGBUKIK" 
- "A számítógép egy problémába ütközött, és újra kell indítania. Csak néhány hibainformációt gyűjtünk, majd újraindítjuk. Ha többet szeretne tudni, később rákereshet az internetre a következő hibára: CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>Ok

A leállási hibáknak számos oka lehet. A leggyakoribb okok a következők:
- Probléma a vezető
- Sérült rendszerfájl vagy memória
- Az alkalmazás hozzáfér a memória tiltott szektorához

## <a name="solution"></a>Megoldás 

A probléma megoldásához lépjen kapcsolatba az [ügyfélszolgálattal, és küldjön be egy memóriaképfájlt](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), amely segít a probléma gyorsabb diagnosztizálásában, vagy próbálkozzon az alábbi önsegítő megoldással.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Az operációs rendszer lemezének csatolása helyreállítási virtuális géphez

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.
2. [Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális géphez.](./troubleshoot-recovery-disks-portal-windows.md) 
3. Hozzon létre egy távoli asztali kapcsolatot a helyreállítási virtuális géppel.

### <a name="enable-dump-logs-and-serial-console"></a>Kiírási naplók és soros konzol engedélyezése

A memóriaképnapló és [a Soros konzol](./serial-console-windows.md) segít a további hibaelhárításban.

A memóriaképnaplók és a Soros konzol engedélyezéséhez futtassa a következő parancsfájlt.

1. Nyisson meg egy rendszergazdai jogú parancssori munkamenetet (rendszergazdaként futva).
2. Futtassa a következő parancsfájlt:

    Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel az F. Cserélje le a virtuális gép megfelelő értékére.

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

### <a name="replace-the-unsigned-drivers"></a>Az aláíratlan illesztőprogramok cseréje

1. A helyreállítási virtuális gép, futtassa a következő parancsot egy rendszergazda jogú parancssorból. Ez a parancs beállítja, hogy az érintett operációsrendszer-lemez a következő rendszerindításkor csökkentett módba induljon:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Ha például a csatlakoztatott operációsrendszer-lemez az F meghajtó, futtassa a következő parancsot:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Válassza le az operációs rendszer lemezét, majd csatlakoztassa újra az operációs rendszer lemezét az érintett virtuális géphez](troubleshoot-recovery-disks-portal-windows.md). A virtuális gép csökkentett módban indul. Ha továbbra is tapasztalja a hibát, folytassa a választható lépéssel.
3. Nyissa **meg** a Futtatás mezőt, és futtassa a **hitelesítőt** az Illesztőprogram-ellenőrző kezelő eszköz elindításához.
4. Jelölje **be az Aláíratlan illesztőprogramok automatikus kijelölése**lehetőséget, majd kattintson a **Tovább**gombra.
5. Az aláíratlan illesztőprogram-fájlok listáját kapja meg. Ne feledje a fájlneveket.
6. Másolja a fájlok ugyanazokat a fájlokat egy működő virtuális gépről, majd cserélje le ezeket az aláíratlan fájlokat. 

7. Távolítsa el a biztonságos rendszerindítási beállításokat:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Indítsa újra a virtuális gépet. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Nem kötelező: A memóriaképnaplók elemzése dump összeomlási módban

A memóriaképnaplók saját kezű elemzéséhez kövesse az alábbi lépéseket:

1. Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez.
2. A csatlakoztatott operációs rendszer lemezén tallózással keresse meg a **\windows\system32\config mappát.** Másolja az összes fájlt biztonsági másolatként arra az esetre, ha visszaállításra lenne szükség.
3. Indítsa el **a Rendszerleíróadatbázis-szerkesztőt** (regedit.exe).
4. Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot. A menüben válassza a > **Fájlbetöltő hive parancsot.** **File**
5. Tallózással keresse meg a csatolt operációsrendszer-lemez **\windows\system32\config\SYSTEM** mappáját. A struktúra nevéhez írja be a **BROKENSYSTEM**. Az új beállításstruktúra a **HKEY_LOCAL_MACHINE** kulcs alatt jelenik meg.
6. Tallózással keresse meg **a HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl mappát,** és hajtsa végre a következő módosításokat:

    Autoreboot = 0

    CrashDumpEnabled = 2
7.  Válassza **a BROKENSYSTEM**lehetőséget. A menüben válassza a **Fájl** > **eltávolítása a hive parancsot.**
8.  Módosítsa a BCD telepítőt hibakeresési módba történő rendszerindításra. Futtassa a következő parancsokat emelt szintű parancssorból:

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
9. [Válassza le az operációs rendszer lemezét, majd csatlakoztassa újra az operációs rendszer lemezét az érintett virtuális géphez](troubleshoot-recovery-disks-portal-windows.md).
10. Indítsa el a virtuális gép, hogy ha azt mutatja, dump elemzés. Keresse meg a betöltést nem tartalmazó fájlt. Ezt a fájlt le kell cserélnie a működő virtuális gépről származó fájlra. 

    A következő minta a kiöntés elemzés. Láthatja, hogy a **hiba** a filecrypt.sys fájlon van: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

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
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. Miután a virtuális gép megfelelően működik és elindul, távolítsa el a Dump Crash beállításait:

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
