---
title: A Windows virtuális gép nem tud elindulni a Windows rendszerindítás-kezelő miatt
description: Ez a cikk olyan problémák megoldásához, amelyeknél a Windows Boot Manager megakadályozza az Azure virtuális gép indítását.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373348"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>A Windows virtuális gép nem tud elindulni a Windows rendszerindítás-kezelő miatt

Ez a cikk olyan problémák megoldásához tartalmaz lépéseket, amelyek miatt a Windows Rendszerindítási Kezelő megakadályozza az Azure virtuális gép (VM) indítását.

## <a name="symptom"></a>Hibajelenség

A virtuális gép beragadt vár egy felhasználó kérdés, és nem indul el, kivéve, ha manuálisan utasítják.

Amikor a [Rendszerindításdiagomba-diagnosztikával](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) tekinti meg a virtuális gép képernyőképét, látni fogja, hogy a képernyőképen megjelenik a Windows rendszerindítási kezelője a *Start operációs rendszer kiválasztása üzenettel, vagy a TAB billentyű lenyomásával válasszon ki egy eszközt:*.

1. ábra
 
![A Windows rendszerindítási kezelője a következőt tartalmazza: "Válasszon egy indításhoz kívánt operációs rendszert, vagy nyomja le a TAB billentyűt az eszköz kiválasztásához:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Ok

A hiba oka a BcD jelző *displaybootmenu* a Windows Boot Manager. Ha a jelző engedélyezve van, a Windows Rendszerindítás-kezelő a rendszerindítási folyamat során kéri a felhasználót, hogy válassza ki, melyik betöltőt szeretné futtatni, ami rendszerindítási késleltetést okoz. Az Azure-ban ez a funkció adhat hozzá a virtuális gép indításához szükséges időt.

## <a name="solution"></a>Megoldás

Folyamat áttekintése:

1. Konfigurálja a gyorsabb rendszerindítási időt a Soros konzol használatával.
2. Hozzon létre és férjen hozzá egy javítási virtuális géphez.
3. Konfigurálja a gyorsabb rendszerindítási időt a javítási virtuális gépen.
4. **Ajánlott:** A virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét.
5. Építse újra a virtuális gép.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Konfigurálás a soros konzol lal történő gyorsabb rendszerindításhoz

Ha rendelkezik a soros konzollal, kétféleképpen érhetel el gyorsabb rendszerindítási időt. Csökkentse a *displaybootmenu várakozási* idejét, vagy távolítsa el teljesen a jelzőt.

1. Kövesse az utasításokat az [Azure Serial Console for Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) eléréséhez a szövegalapú konzol eléréséhez.

   > [!NOTE]
   > Ha nem tudja elérni a soros konzolt, ugorjon előre [a Virtuális gép létrehozása és elérése](#create-and-access-a-repair-vm)című területre.

2. **A lehetőség:** A várakozási idő csökkentése

   a. A várakozási idő alapértelmezés szerint 30 másodperc, de gyorsabb időre (pl. 5 másodpercre) módosítható.

   b. Az időtúlérték módosításához használja a következő parancsot a soros konzolon:

      `bcdedit /set {bootmgr} timeout 5`

3. **B. lehetőség:** A BCD-jelző eltávolítása

   a. A Megjelenítési rendszerindító menü parancssorának teljes megakadályozásához írja be a következő parancsot:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Ha a fenti lépésekben nem tudta használni a soros konzolt a gyorsabb rendszerindítási idő konfigurálására, a következő lépésekkel folytathatja. A probléma megoldásához offline módban kell hibaelhárítást elhárítania.

### <a name="create-and-access-a-repair-vm"></a>Virtuális gép létrehozása és elérése

1. A [virtuális gép javítási parancsainak 1-3.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)
2. Használja a Távoli asztali kapcsolat csatlakozása a javítási virtuális gép.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Konfigurálja a gyorsabb rendszerindítási időt javítási virtuális gépen

1. Nyisson meg egy rendszergazda jogú parancssort.
2. A DisplayBootMenu engedélyezéséhez írja be a következőket:

   Az **1.**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   A **2.**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Cserélje ki a szimbólumoknál nagyobb vagy kisebb szimbólumokat, valamint a bennük lévő szöveget, például a "< szöveg itt >".

3. Módosítsa az időtúlértéket 5 másodpercre:

   Az **1.**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   A **2.**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Cserélje ki a szimbólumoknál nagyobb vagy kisebb szimbólumokat, valamint a bennük lévő szöveget, például a "< szöveg itt >".

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