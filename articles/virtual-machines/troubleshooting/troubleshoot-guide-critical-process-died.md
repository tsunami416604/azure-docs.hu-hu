---
title: Windows stop hiba -
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 9e4c4b9c809a626c71b4a7e9235d917b442be160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373361"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows Stop Error - #0x000000EF "Kritikus folyamat meghalt"

Ez a cikk olyan problémák megoldásához, ahol egy kritikus folyamat meghal az Azure-beli virtuális gép rendszerindítása során.

## <a name="symptom"></a>Hibajelenség

Amikor a [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) segítségével tekinti meg a virtuális gép képernyőképét, látni fogja, hogy a képernyőkép a kritikus folyamat meghalt üzenetével *#0x000000EF* hibát jeleníti *meg.*

!["A számítógép egy problémába ütközött, és újra kell indítania. Csak néhány hibainformációt gyűjtünk, és újraindíthatja. (##% kész) Ha szeretne többet megtudni, kereshet online később ezt a hibát: 0x000000EF"](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Ok

Ez általában annak köszönhető, hogy a rendszer rendszerfolyamata a rendszerindítás során meghibásodott. A kritikus folyamatokkal kapcsolatos problémákról a "[Bug Check 0xEF: CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)" című helyen olvashat bővebben.

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése:

1. Hozzon létre és férjen hozzá egy javítási virtuális géphez.
2. Javítsa ki az operációs rendszer sérülését.
3. **Ajánlott:** A virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét.
4. Építse újra a virtuális gép.

> [!NOTE]
> A rendszerindítási hiba esetén a vendég operációs rendszer nem működik. A probléma megoldásához kapcsolat nélküli módban kell hibaelhárítást elhárítania.

### <a name="create-and-access-a-repair-vm"></a>Virtuális gép létrehozása és elérése

1. A [virtuális gép javítási parancsainak 1-3.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)
2. A Távoli asztali kapcsolat használatával csatlakozzon a javítási virtuális géphez.

### <a name="fix-any-os-corruption"></a>Az operációs rendszer bármely sérülésének javítása

1. Nyisson meg egy rendszergazda jogú parancssort.
2. Futtassa a következő Rendszerfájl-ellenőrző (SFC) parancsot:

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Ahol < BOOT DISK DRIVE > a javítási virtuális gép (általában "C:") rendszerindító kötete, és < TÖRÖTT LEMEZMEGHAJTÓ > a csatlakoztatott lemez meghajtóbetűjelje lesz a hibás virtuális gépről. Cserélje ki a nagyobb, mint / kevesebb, mint a szimbólumok, valamint a bennük lévő szöveget, pl. "< szöveg itt >", a megfelelő betűvel.

3. Ezután használja [a virtuális gép javítási parancsainak 5.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)
4. Ha a virtuális gép továbbra sem indul el, folytassa a memóriaképfájl gyűjtését.

### <a name="collect-the-memory-dump-file"></a>A memóriaképfájl gyűjtése

Ha a probléma az SFC futtatása után is fennáll, a probléma okának meghatározásához memóriaképfájl elemzésére lesz szükség. A memóriaképfájl összegyűjtéséhez hajtsa végre az alábbi lépéseket:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Az operációs rendszer lemezének csatolása új javítási virtuális géphez

1. A [virtuális gép javítási parancsainak 1-3.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)
2. A Távoli asztali kapcsolat használatával csatlakozzon a javítási virtuális géphez.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Keresse meg a memóriaképfájlt, és küldje el a támogatási jegyet

3. A javítási virtuális gép, ugrás a Windows mappába a csatlakoztatott operációs rendszer lemezén. Ha a csatlakoztatott operációsrendszer-lemezhez rendelt illesztőprogram-betű *F,* akkor az *F:\Windows*ablakot kell megvásárolnia.
4. Keresse meg a *memory.dmp* fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriaképfájllal.

   > [!NOTE]
   > Ha nem találja a memóriaképfájlt, hajtsa végre az alábbi lépéseket a memóriakép és a Soros konzol engedélyezéséhez, majd térjen vissza ehhez a szakaszhoz, és ismételje meg a fenti feladat lépéseit a memóriaképfájl összegyűjtéséhez.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Ajánlott: A virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét

A memóriakép-gyűjtemény és a Soros konzol engedélyezéséhez futtassa a következő parancsfájlt:

1. Nyisson meg egy rendszergazdai parancssori munkamenetet (Futtatás rendszergazdaként).
2. Futtassa az alábbi parancsot:

   Soros konzol engedélyezése

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Cserélje ki a szimbólumoknál nagyobb vagy kisebb szimbólumokat, valamint a bennük lévő szöveget, például a "< szöveg itt >".

3. Ellenőrizze, hogy az operációs rendszer lemezén lévő szabad terület legalább annyira megvan-e, mint a virtuális gép memóriamérete (RAM).

Ha nincs elég hely az operációs rendszer lemezén, meg kell változtatnia a helyet, ahol a memóriakép fájl jön létre, és olvassa el, hogy minden adatlemez csatlakozik a virtuális gép, amely elegendő szabad terület. A hely módosításához cserélje le a(z) "%SystemRoot%" parancsot az alábbi parancsokban lévő adatlemez meghajtóbetűjelére (például "F:").

#### <a name="suggested-configuration-to-enable-os-dump"></a>Javasolt konfiguráció az operációs rendszer memóriaképengedélyezéséhez

**Hibás operációsrendszer-lemez betöltése:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Engedélyezés a ControlSet001-en:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Engedélyezés a ControlSet002-n:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Hibás operációsrendszer-lemez eltávolítása:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Az eredeti virtuális gép újraépítése

A [virtuális gép javítási parancsainak 5.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)
