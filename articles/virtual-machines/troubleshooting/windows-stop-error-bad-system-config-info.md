---
title: Windows Stop Error-0x00000074 rossz rendszerkonfigurációs információ
description: Ez a cikk azokat a problémákat ismerteti, amelyekkel a Windows nem indítható el, és az Azure-beli virtuális gépek (VM) hibás rendszerkonfigurációs adatai miatt újra kell indítani a rendszert.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: 071b5786127af31a2ad3266c128dbfb7cacad656
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88942123"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Windows Stop Error-0x00000074 rossz rendszerkonfigurációs információ

Ez a cikk azokat a problémákat ismerteti, amelyekkel a Windows nem indítható el, és az Azure-beli virtuális gépek (VM) hibás rendszerkonfigurációs adatai miatt újra kell indítani a rendszert.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztikát](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használ a virtuális gép képernyőképének megtekintéséhez, látni fogja, hogy a képernyőképen a Windows leállítási kódja **#0x00000074** vagy **BAD_SYSTEM_CONFIG_INFO**jelenik meg.

*A számítógép hibát észlelt, és újra kell indítania. Újra lehet indítani.* 
 *A problémával és a lehetséges javításokkal kapcsolatos további információkért http://windows.com/stopcode látogasson el a következő webhelyre:* 
 *Ha egy támogatási személyt hív meg, adja meg nekik ezt az információt:* 
 *Kód leállítása: BAD_SYSTEM_CONFIG_INFO*

  ![A Windows stop Code 0x00000074, amely "BAD_SYSTEM_CONFIG_INFO" néven is látható. A Windows értesíti a felhasználót, hogy a SZÁMÍTÓGÉPe problémát észlelt, és újra kell indítani.](./media/windows-stop-error-bad-system-config-info/1.png)

## <a name="cause"></a>Ok

A **BAD_SYSTEM_CONFIG_INFO** leállítási kód akkor fordul elő, ha **a beállításjegyzék-** struktúra sérültnek tűnik. Ezt a hibát a következő okok okozhatják:

- A beállításjegyzék-struktúra nem zárult megfelelően.
- A beállításjegyzék struktúrája sérült.
- Hiányoznak beállításkulcsok vagy értékek.

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése:

1. Hozzon létre és nyissa meg a javítási virtuális gépet.
1. A kaptár sérülésének keresése.
1. A soros konzol és a memóriakép gyűjteményének engedélyezése.
1. Hozza létre újra a virtuális gépet.

> [!NOTE]
> A hiba észlelésekor a vendég operációs rendszer (OS) nem működik. A probléma megoldásához offline módban kell elhárítani a problémát.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 1-3-es lépéseit használva készítse elő a javítási virtuális gépet.
1. A kaptár sérülésének keresése.
1. A Távoli asztali kapcsolat használatával csatlakozhat a javítási virtuális géphez.
1. Másolja a `\windows\system32\config` mappát, és mentse azt az egészséges lemezpartíció vagy egy másik biztonságos helyen. A mappa biztonsági mentése óvintézkedésként, mivel a kritikus beállításjegyzék-fájlokat fogja szerkeszteni.

> [!NOTE]
> Készítsen másolatot a `\windows\system32\config` mappáról biztonsági másolatként abban az esetben, ha vissza kell állítania a beállításjegyzékben végzett módosításokat.

### <a name="check-for-hive-corruption"></a>A kaptár sérülésének keresése

Az alábbi utasítások segítenek megállapítani, hogy az ok a kaptár sérülése miatt volt-e, vagy ha a struktúra nem lett megfelelően lezárva. Ha a struktúra nem megfelelően lett bezárva, a fájl zárolását és a virtuális gép kijavítását is lehetővé teszi.

1. A javítási virtuális gépen nyissa meg a **Rendszerleíróadatbázis-szerkesztő** alkalmazást. A megkereséséhez írja be a "regedit" kifejezést a Windows keresősávba.
1. A Rendszerleíróadatbázis-szerkesztőben válassza a **HKEY_LOCAL_MACHINE** elemet a kiemeléséhez, majd válassza a **fájl > a struktúra betöltése..** . lehetőséget. a menüből.
1. Tallózással keresse `\windows\system32\config\SYSTEM` meg és válassza a **Megnyitás**lehetőséget.
1. Amikor a rendszer kéri, hogy adjon meg egy nevet, adja meg a **BROKENSYSTEM**.

   1. Ha a struktúra nem nyílik meg, vagy ha üres, akkor a struktúra sérült. Ha a struktúra sérült, [Nyisson meg egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

     ![Hiba történt, amely azt jelzi, hogy a Beállításszerkesztő nem tudja betölteni a struktúrát.](./media/windows-stop-error-bad-system-config-info/2.png)

   1. Ha a struktúra általában megjelenik, akkor a struktúra nem zárult megfelelően. Folytassa az 5. lépéssel.

1. Ha nem megfelelően zárt struktúrát szeretne kijavítani, jelölje ki a **BROKENSYSTEM** , majd válassza a **fájl > a struktúra eltávolítása..** . lehetőséget. a fájl zárolásának feloldásához.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>A soros konzol és a memóriakép gyűjteményének engedélyezése

**Ajánlott**: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét a következő parancsfájl futtatásával:

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet.
1. Futtassa az alábbi parancsot:

   **A soros konzol engedélyezése**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Győződjön meg arról, hogy az operációsrendszer-lemez szabad területe nagyobb, mint a virtuális gép memóriájának mérete (RAM).

   Ha nincs elég hely az operációsrendszer-lemezen, akkor módosítsa a memóriakép fájljának helyét, és ezt a helyet a virtuális géphez csatolt, elegendő szabad területtel rendelkező adatlemezre kell hivatkoznia. A hely módosításához cserélje le a **% systemroot%** betűjelet az adatlemez meghajtóbetűjelére (pl **. F:**) a következő parancsokban.

   Az operációs rendszer memóriaképének engedélyezéséhez javasolt konfiguráció:

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
   
### <a name="rebuild-the-vm"></a>A virtuális gép újraépítése

A virtuális gép újraépítéséhez használja [a virtuális gép javítási parancsainak 5. lépését](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) .
