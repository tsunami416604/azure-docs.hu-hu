---
title: Az Azure-beli virtuális gép indításakor a kritikus szolgáltatás nem sikerült | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a "0x0000005A-CRITICal SERVICE FAILed" hibaüzenetet, amely a rendszerindítás során fordul elő | Microsoft Docs
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
ms.openlocfilehash: 8c3e76f1a7edffefc8773dfa548773ec0932fae6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86129851"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>A Windows egy Azure-beli virtuális gép indításakor a "kritikus szolgáltatás sikertelen" állapotot jeleníti meg a kék képernyőn
Ez a cikk a "kritikus szolgáltatás nem sikerült" hibát mutatja be, amely akkor fordulhat elő, amikor Windows rendszerű virtuális gépet (VM) indít el Microsoft Azure. Hibaelhárítási lépéseket biztosít a problémák megoldásához. 


## <a name="symptom"></a>Hibajelenség 

Egy Windows rendszerű virtuális gép nem indul el. Amikor bejelöli a rendszerindítási [diagnosztika](./boot-diagnostics.md)rendszerindítási funkcióit, a következő hibaüzenetek egyike jelenik meg egy kék képernyőn:

- "A számítógép hibát észlelt, és újra kell indítania. Újra lehet indítani. További információt erről a problémáról és a lehetséges javításokról a következő webhelyen talál: https://windows.com/stopcode . Ha egy támogatási személyt hív meg, adja meg nekik ezt az információt: leállítási kód: a kritikus szolgáltatás nem sikerült. 
- "A számítógép hibát észlelt, és újra kell indítania. Most gyűjtünk néhány hibaüzenetet, majd újraindulunk. Ha további információra van szüksége, később is megkeresheti a következő hibaüzenetet: CRITICAL_SERVICE_FAILED "

## <a name="cause"></a>Ok

A leállítási hibák számos oka lehet. A leggyakoribb okok a következők:
- Probléma egy illesztőprogrammal
- Sérült rendszerfájl vagy memória
- Az alkalmazás a memória tiltott szektorához fér hozzá

## <a name="solution"></a>Megoldás 

A probléma megoldásához [forduljon az ügyfélszolgálathoz, és küldjön el egy memóriaképfájl-fájlt](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), amely segít a probléma gyorsabb diagnosztizálásában, vagy próbálja ki a következő önsegítő megoldást.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Az operációsrendszer-lemez csatlakoztatása egy helyreállítási virtuális géphez

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).
2. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez](./troubleshoot-recovery-disks-portal-windows.md). 
3. Hozzon létre egy távoli asztali kapcsolatot a helyreállítási virtuális géppel.

### <a name="enable-dump-logs-and-serial-console"></a>Memóriakép-naplók és soros konzol engedélyezése

A memóriakép naplója és a [soros konzol](./serial-console-windows.md) segítséget nyújt a további hibaelhárításhoz.

A memóriaképek és a soros konzol engedélyezéséhez futtassa az alábbi szkriptet.

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (Futtatás rendszergazdaként).
2. Futtassa a következő parancsfájlt:

    Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel F. A virtuális gép megfelelő értékét cserélje le.

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

1. A helyreállítási virtuális gépen futtassa a következő parancsot egy rendszergazda jogú parancssorból. Ezzel a paranccsal az érintett operációsrendszer-lemez úgy állítható be, hogy csökkentett módban induljon el a következő rendszerindításkor:

    ```console
    bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal
    ```

    Ha például a csatlakoztatott operációsrendszer-lemez az F meghajtó, futtassa a következő parancsot:

    ```console
    bcdedit /store F: boot\bcd /set {default} safeboot minimal
    ```

2. [Válassza le az operációsrendszer-lemezt, majd csatlakoztassa újra az operációsrendszer-lemezt az érintett virtuális géphez](troubleshoot-recovery-disks-portal-windows.md). A virtuális gép biztonságos módba fog indulni. Ha továbbra is tapasztalja a hibát, lépjen a választható lépésre.
3. Nyissa meg a **Futtatás** mezőt, és futtassa a vezérlőt **az illesztőprogram-ellenőrző** kezelő eszköz elindításához.
4. Jelölje be az **aláíratlan illesztőprogramok automatikus kiválasztása**jelölőnégyzetet, majd kattintson a **tovább**gombra.
5. Ekkor megjelenik az Aláíratlan illesztőprogram-fájlok listája. Jegyezze fel a fájlneveket.
6. Másolja a fájlok ugyanazon verzióját egy működő virtuális gépről, majd cserélje le ezeket a nem aláírt fájlokat. 

7. Távolítsa el a biztonságos rendszerindítás beállításait:

    ```console
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
    ```

8.  Indítsa újra a virtuális gépet. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Nem kötelező: a memóriaképi naplók elemzése a memóriakép összeomlása módban

A memóriakép-naplók elemzéséhez kövesse az alábbi lépéseket:

1. Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez.
2. A csatlakoztatott operációsrendszer-lemezen keresse meg a **\Windows\System32\Config**. Másolja az összes fájlt biztonsági másolatként arra az esetre, ha visszaállításra van szükség.
3. Indítsa el a **Beállításszerkesztőt** (regedit.exe).
4. Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot. A menüben válassza a **fájl**  >  **Load struktúra**elemet.
5. Keresse meg a **\windows\system32\config\SYSTEM** mappát a csatlakoztatott operációsrendszer-lemezen. A struktúra neveként írja be a következőt: **BROKENSYSTEM**. Az új beállításjegyzék-struktúra a **HKEY_LOCAL_MACHINE** kulcs alatt jelenik meg.
6. Tallózással keresse meg a **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** , és végezze el a következő módosításokat:

    Autoújraindítás = 0

    CrashDumpEnabled = 2
7.  Válassza a **BROKENSYSTEM**lehetőséget. A menüből válassza ki a **fájl**  >  **kitöltése struktúrát**.
8.  Módosítsa a BCD-telepítőt hibakeresési módba való indításra. Futtassa a következő parancsokat egy rendszergazda jogú parancssorból:

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
9. [Válassza le az operációsrendszer-lemezt, majd csatlakoztassa újra az operációsrendszer-lemezt az érintett virtuális géphez](troubleshoot-recovery-disks-portal-windows.md).
10. Indítsa el a virtuális gépet, és ellenőrizze, hogy látható-e a memóriakép elemzése. Keresse meg a nem betölteni kívánt fájlt. Ezt a fájlt a működő virtuális gép egy fájljával kell helyettesítenie. 

    A következő példa a memóriakép elemzésére szolgál. Láthatja, hogy a **hiba** filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

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

11. Ha a virtuális gép megfelelően működik, és a szokásos módon indítja el a rendszert, távolítsa el a dump Crash beállításait:

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
