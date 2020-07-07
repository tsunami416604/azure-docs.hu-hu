---
title: Windows Stop-hiba –
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80373361"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows Stop Error-#0x000000EF "kritikus folyamat meghalt"

Ez a cikk az Azure-beli virtuális gépek indításakor felmerülő problémák megoldásának lépéseit ismerteti.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használatával tekinti meg a virtuális gép képernyőképét, láthatja, hogy a képernyőképen megjelenik az üzenet *kritikus folyamatának elhalálozásakor* *#0x000000EF* hibaüzenet.

!["A számítógép hibát észlelt, és újra kell indítania. Most gyűjtünk néhány hibaüzenetet, majd újraindíthatjuk. (# #% kész) Ha további információra van szüksége, később is megkeresheti a következő hibaüzenetet: 0x000000EF "](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Ok

Ezt általában az okozza, hogy egy kritikus rendszerfolyamat nem működik a rendszerindítás során. A kritikus folyamatokkal kapcsolatos problémákról bővebben a "[hiba-ellenőrzési 0xEF: CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)" webhelyen olvashat.

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése:

1. Hozzon létre és nyissa meg a javítási virtuális gépet.
2. Javítsa ki az operációs rendszer sérülését.
3. **Ajánlott**: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét.
4. Hozza létre újra a virtuális gépet.

> [!NOTE]
> Ha ezt a rendszerindítási hibát tapasztalja, a vendég operációs rendszer nem működik. A probléma megoldásához offline módban hibaelhárítást hajthat végre.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok 1-3-es lépéseit](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) használva készítse elő a javítási virtuális gépet.
2. A Távoli asztali kapcsolat kapcsolódása a javítási virtuális géphez.

### <a name="fix-any-os-corruption"></a>Javítsa ki az operációs rendszer sérülését

1. Nyisson meg egy rendszergazda jogú parancssort.
2. Futtassa a következő rendszerfájl-ellenőrzési (SFC) parancsot:

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Ahol < rendszerindító lemezmeghajtó > a javítási virtuális gép rendszerindító kötete (jellemzően "C:"), és < sérült lemezmeghajtó > a csatlakoztatott lemez meghajtóbetűjele lesz a hibás virtuális gépről. Cserélje le a nagyobb vagy kisebb szimbólumot, valamint a bennük található szöveget, például: "< szöveg itt >", a megfelelő betűvel.

3. Ezután használja [a virtuális gép javítási parancsainak 5. lépését](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) a virtuális gép újraösszeállításához, és ellenőrizze, hogy elindul-e.
4. Ha a virtuális gép még nem indul el, folytassa a memóriakép fájljának gyűjtésével.

### <a name="collect-the-memory-dump-file"></a>A memóriakép fájljának összegyűjtése

Ha a probléma az SFC futtatása után sem szűnik meg, akkor a probléma okának megállapításához meg kell határozni egy memóriakép fájljának elemzését. A memóriakép fájljának összegyűjtéséhez kövesse az alábbi lépéseket:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Az operációsrendszer-lemez csatlakoztatása egy új javítási virtuális géphez

1. Egy új javítási virtuális gép előkészítéséhez használja [a virtuális gép javítási parancsainak 1-3. lépéseit](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) .
2. A Távoli asztali kapcsolat kapcsolódása a javítási virtuális géphez.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>A memóriaképfájl megkeresése és támogatási jegy beküldése

3. A javítás virtuális gépen nyissa meg a Windows mappát a csatolt operációsrendszer-lemezen. Ha a csatolt operációsrendszer-lemezhez hozzárendelt illesztőprogram betűjele *F*, akkor a *F:\Windows*-ben kell megadnia.
4. Keresse meg a *Memory. dmp* fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriakép fájljával.

   > [!NOTE]
   > Ha nem találja a memóriaképet, a memóriakép-gyűjtés és a soros konzol engedélyezéséhez hajtsa végre az alábbi lépéseket, majd térjen vissza ehhez a szakaszhoz, és ismételje meg a fenti feladat lépéseit a memóriakép fájljának összegyűjtéséhez.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Ajánlott: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriaképek gyűjtését

A memóriakép-gyűjtés és a soros konzol engedélyezéséhez futtassa a következő parancsfájlt:

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (Futtatás rendszergazdaként).
2. Futtassa az alábbi parancsot:

   Soros konzol engedélyezése

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Cserélje le a több vagy kevesebb szimbólumot, valamint a bennük található szöveget, például: "< szöveg itt >".

3. Győződjön meg arról, hogy az operációsrendszer-lemezen lévő szabad terület a virtuális gép memóriájának méretétől (RAM) függ.

Ha nincs elég hely az operációsrendszer-lemezen, akkor módosítania kell a memóriakép fájljának helyét, és a virtuális géphez csatlakoztatott minden olyan adatlemezre, amely elegendő szabad hellyel rendelkezik. A hely módosításához cserélje le a "% SystemRoot%" kifejezést az alábbi parancsokban található adatlemez meghajtóbetűjelére (például "F:").

#### <a name="suggested-configuration-to-enable-os-dump"></a>Az operációs rendszer kiírásának engedélyezése javasolt konfiguráció

**Sérült operációsrendszer-lemez betöltése**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Engedélyezés a ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Engedélyezés a ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Sérült operációsrendszer-lemez eltávolítása:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Az eredeti virtuális gép újraépítése

A virtuális gép újraösszeállításához használja [a virtuális gép javítási parancsainak 5. lépését](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) .
