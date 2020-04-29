---
title: A Windows rendszerű virtuális gép nem indítható el a Windows rendszerindítási kezelője miatt
description: Ez a cikk az Azure-beli virtuális gépek rendszerindítását megakadályozó problémák megoldásának lépéseit ismerteti.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5d2fb62870e2c41af635627f5d692f08c67f8394
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80373348"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>A Windows rendszerű virtuális gép a Windows rendszerindítási kezelője miatt nem indítható el

Ez a cikk az Azure-beli virtuális gépek (VM) rendszerindítását megakadályozó problémák megoldásának lépéseit ismerteti.

## <a name="symptom"></a>Hibajelenség

A virtuális gép egy felhasználói kérésre várakozik, és nem indul el, hacsak manuálisan nem utasította.

Ha [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használatával tekinti meg a virtuális gép képernyőképét, láthatja, hogy a képernyőkép megjeleníti a Windows rendszerindítási kezelőt az üzenet alapján, majd a *TAB billentyű lenyomásával kijelöl egy eszközt:*.

1. ábra
 
![A Windows rendszerindítási kezelőjének "válasszon operációs rendszert az indításhoz, vagy nyomja le a TAB billentyűt egy eszköz kiválasztásához:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Ok

A hiba oka a Windows rendszerindító kezelőjében található BCD-jelző *displaybootmenu* . Ha a jelző engedélyezve van, a Windows rendszerindító kezelője a rendszerindítási folyamat során felszólítja a felhasználót a futtatni kívánt betöltő kiválasztására, ami a rendszerindítás késleltetését okozza. Az Azure-ban ez a szolgáltatás hozzá tud adni egy virtuális gép rendszerindításához szükséges időt.

## <a name="solution"></a>Megoldás

Folyamat áttekintése:

1. A soros konzollal történő gyorsabb rendszerindítás beállítása.
2. Hozzon létre és nyissa meg a javítási virtuális gépet.
3. Konfigurálja a gyorsabb rendszerindítási időt a javítási virtuális gépen.
4. **Ajánlott**: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét.
5. Hozza létre újra a virtuális gépet.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Konfigurálás gyorsabb rendszerindítás esetén a soros konzollal

Ha rendelkezik hozzáféréssel a soros konzolhoz, kétféleképpen érheti el gyorsabban a rendszerindítási időpontokat. Csökkentse a *displaybootmenu* várakozási idejét, vagy távolítsa el a jelzőt teljesen.

1. Kövesse az [Azure soros konzoljának a Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) rendszerhez való elérésének lépéseit, hogy hozzáférjen a szöveges konzolhoz.

   > [!NOTE]
   > Ha nem fér hozzá a soros konzolhoz, ugorjon előre a [javítási virtuális gép létrehozásához és eléréséhez](#create-and-access-a-repair-vm).

2. " **A" lehetőség: A**várakozási idő csökkentése

   a. A várakozási idő alapértelmezés szerint 30 másodpercre van beállítva, de gyorsabb lehet (például 5 másodperc).

   b. Az időtúllépési érték beállításához használja az alábbi parancsot a soros konzolon:

      `bcdedit /set {bootmgr} timeout 5`

3. **B. lehetőség**: a BCD jelző eltávolítása

   a. Ha meg szeretné akadályozni, hogy a rendszerindítási menü ne jelenjen meg, írja be a következő parancsot:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Ha a fenti lépésekben nem sikerült a soros konzol használatával konfigurálni a gyorsabb rendszerindítási időt, az alábbi lépéseket követve folytathatja a műveletet. A probléma megoldásához mostantól a kapcsolat nélküli módban hibaelhárítást hajthat végre.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok 1-3-es lépéseit](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) használva készítse elő a javítási virtuális gépet.
2. Távoli asztali kapcsolat kapcsolódjon a javítási virtuális géphez.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Gyorsabb rendszerindítási idő beállítása a javítási virtuális gépen

1. Nyisson meg egy rendszergazda jogú parancssort.
2. A DisplayBootMenu engedélyezéséhez adja meg a következőt:

   Használja ezt a parancsot az **1. generációs virtuális gépekhez**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Használja ezt a parancsot a **2. generációs virtuális gépekhez**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Cserélje le a több vagy kevesebb szimbólumot, valamint a bennük található szöveget, például: "< szöveg itt >".

3. Módosítsa az időtúllépési értéket 5 másodpercre:

   Használja ezt a parancsot az **1. generációs virtuális gépekhez**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Használja ezt a parancsot a **2. generációs virtuális gépekhez**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Cserélje le a több vagy kevesebb szimbólumot, valamint a bennük található szöveget, például: "< szöveg itt >".

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