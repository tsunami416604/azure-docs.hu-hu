---
title: A virtuális gép nem válaszol a naplózási házirend konfigurációs házirendjének alkalmazása közben
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekkel a virtuális gép (VM) nem válaszol a naplózási házirend konfigurációs házirendjének alkalmazása során, ami megakadályozza az Azure-beli virtuális gépek indítását.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3f6383b5-81fa-49ea-9434-2fe475e4cbef
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: bc41783bf977806b5f9bba5b953f1f581ad07f18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89299524"
---
# <a name="virtual-machine-is-unresponsive-while-applying-audit-policy-configuration-policy"></a>A virtuális gép nem válaszol a naplózási házirend konfigurációs házirendjének alkalmazása közben

Ez a cikk olyan problémák megoldását ismerteti, amelyekkel a virtuális gép (VM) nem válaszol a naplózási házirend konfigurációs házirendjének alkalmazása során, ami megakadályozza az Azure-beli virtuális gépek indítását.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használatával tekinti meg a virtuális gép képernyőképét, látni fogja, hogy az operációs rendszer (os) nem válaszol a **naplózási házirend konfigurációs házirendjét alkalmazó**üzenet indításakor.

  ![Az operációs rendszer indítása a következő üzenettel: "a naplózási házirend konfigurációs szabályzatának alkalmazása"](./media/vm-unresponsive-applying-audit-configuration-policy/1.png)

  ![Az operációs rendszer indítása a Windows Server 2012-ben a következő üzenettel: "a naplózási házirend konfigurációs szabályzatának alkalmazása"](./media/vm-unresponsive-applying-audit-configuration-policy/2.png)

## <a name="cause"></a>Ok

Ütköző zárolások állnak fenn, ha a házirend megpróbálja törölni a régi felhasználói profilokat.

> [!NOTE]
> Ez csak a Windows Server 2012 és a Windows Server 2012 R2 rendszerre vonatkozik.

A problémás szabályzat: a *számítógép-Computer sablonok \ rendszer/felhasználó Profiles\Delete felhasználói profilok régebbiek, mint a rendszer újraindításakor megadott számú nap.*

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése

1. Hozzon létre és nyissa meg a javítási virtuális gépet.
1. Tiltsa le a házirendet.
1. A soros konzol és a memóriakép gyűjteményének engedélyezése.
1. Hozza létre újra a virtuális gépet.
1. Gyűjtse össze a memóriakép fájlját, és küldjön be egy támogatási jegyet.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 1-3-es lépéseit használva készítse elő a javítási virtuális gépet.
1. A Távoli asztali kapcsolat használatával kapcsolódjon a javítási virtuális géphez.

### <a name="disable-the-policy"></a>A szabályzat letiltása

1. A javítási virtuális gépen nyissa meg a **Beállításszerkesztőt**.
1. Keresse meg a kulcsot **HKEY_LOCAL_MACHINE** és válassza a **fájl > betöltési struktúra** lehetőséget a menüből.

   ![A beállításjegyzék-szerkesztőben a struktúra betöltéséhez szükséges navigálás.](./media/vm-unresponsive-applying-audit-configuration-policy/3.png)

   - A Load kaptár használatával a beállításkulcsok egy offline rendszerből tölthetők be. Ebben az esetben a rendszer a helyreállító virtuális géphez csatolt sérült lemez.
   - A rendszerszintű beállítások tárolása **HKEY_LOCAL_MACHINE** történik, és a **HKLM**-ként rövidíthető le.

1. A csatolt lemezen nyissa meg a `\windows\system32\config\SOFTWARE` fájlt.

   - Ha a rendszer kéri a név megadását, írja be a következőt: **BROKENSOFTWARE**.
   - A **BROKENSOFTWARE** betöltésének ellenőrzéséhez bontsa ki **HKEY_LOCAL_MACHINE** és keresse meg a hozzáadott **BROKENSOFTWARE** kulcsot.

1. Nyissa meg a **BROKENSOFTWARE** , és ellenőrizze, hogy létezik-e a **CleanupProfiles** kulcs a betöltött struktúrában.

   - Ha a kulcs létezik, a **CleanupProfiles** házirend van beállítva. Az érték a napokon mért adatmegőrzési házirendet jelöli.
   - Ha a kulcs nem létezik, a **CleanupProfiles** házirend nincs beállítva. Ebben az esetben ugorjon előre [egy támogatási jegyet egy memóriakép-fájllal való elküldéséhez](#collect-the-memory-dump-file-and-submit-a-support-ticket).

1. Törölje a **CleanupProfiles** kulcsot a következő parancs használatával:

   `reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f`

1. Távolítsa el a **BROKENSOFTWARE** -struktúrát a következő parancs használatával:

   `reg unload HKLM\BROKENSOFTWARE`

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>A soros konzol és a memóriakép gyűjteményének engedélyezése

**Ajánlott**: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét a következő parancsfájl futtatásával:

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet.
1. Sorolja fel a BCD-tároló adatait, és határozza meg a rendszerindítási betöltő azonosítóját, amelyet a következő lépésben fog használni.

   1. 1. generációs virtuális gépek esetén írja be a következő parancsot, és jegyezze fel a felsorolt azonosítót:

      `bcdedit /store <BOOT PARTITON>:\boot\bcd /enum`

      - A parancsban cserélje le a `<BOOT PARTITON>` betűt a partíció betűjelére a csatlakoztatott lemezen, amely tartalmazza a rendszerindító mappát.

        ![A 4. ábrán a BCD-tároló egy 1. generációs virtuális gépen való listázásának kimenetét jeleníti meg, amely a Windows rendszerindítási azonosító alatt található.](./media/vm-unresponsive-applying-audit-configuration-policy/4.png)

   1. A 2. generációs virtuális gépek esetében írja be a következő parancsot, és jegyezze fel a felsorolt azonosítót:

      `bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

      - A parancsban cserélje le az `<LETTER OF THE EFI SYSTEM PARTITION>` EFI rendszerpartíció betűjét.
      - Hasznos lehet a Lemezkezelés konzol elindítása az **EFI rendszerpartícióként**címkézett megfelelő rendszerpartíció azonosításához.
      - Az azonosító lehet egyedi GUID, vagy lehet az alapértelmezett **Csizmadia**.

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
   
### <a name="rebuild-the-virtual-machine"></a>A virtuális gép újraépítése

1. A virtuális gép újraépítéséhez használja [a virtuális gép javítási parancsainak 5. lépését](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) .

1. Ellenőrizze, hogy a virtuális gép rendesen elindul-e, hogy a probléma kijavította-e a problémát.

   - Ha a probléma még nincs kijavítva, folytassa a [memóriakép-fájl gyűjtésével és a támogatási jegy elküldésével](#collect-the-memory-dump-file-and-submit-a-support-ticket).
   - Ha a probléma kijavítva van, akkor nincs szükség további lépésekre.

Ha a probléma kijavítva lett, a házirend mostantól helyileg le van tiltva. Állandó megoldásként ne használja a CleanupProfiles házirendet a virtuális gépeken, mivel az automatikusan törli a felhasználói profilokat. Használjon más módszert a profilok törlésének elvégzéséhez, például ütemezett feladathoz vagy parancsfájlhoz.

**Ne használja ezt a házirendet:** 
 A *Machine\Admin configuration\policies\administrative templates\system\user a rendszer újraindításakor a megadott számú napnál régebbi felhasználói profilokat Profiles\Delete.*

### <a name="the-issue-should-now-be-fixed"></a>A problémát most meg kell oldani

Tesztelje a virtuális gépet, és győződjön meg róla, hogy működik, mint a normál. Ha továbbra is problémákat tapasztal, további segítségért folytassa a következő szakasszal.

### <a name="collect-the-memory-dump-file-and-submit-a-support-ticket"></a>A memóriakép fájljának összegyűjtése és támogatási jegy beküldése

A probléma megoldásához először össze kell gyűjtenie az összeomláshoz tartozó memóriakép-fájlt, majd kapcsolatba kell lépnie a támogatási szolgálattal a memóriakép fájljával. A memóriakép-fájl összegyűjtéséhez kövesse az alábbi lépéseket:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Az operációsrendszer-lemez csatlakoztatása egy új javítási virtuális géphez

1. Egy új javítási virtuális gép előkészítéséhez használja a [virtuális gép javítási parancsainak](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 1-3. lépéseit.
1. A Távoli asztali kapcsolat kapcsolódása a javítási virtuális géphez.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>A memóriaképfájl megkeresése és támogatási jegy beküldése

1. A javítás virtuális gépen nyissa meg a Windows mappát a csatolt operációsrendszer-lemezen. Ha a csatolt operációsrendszer-lemezhez rendelt illesztőprogram-betűjel *F*-ként van megjelölve, akkor a következőt kell megadnia: `F:\Windows` .
1. Keresse meg a `memory.dmp` fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriakép fájljával.
1. Ha nem sikerül megkeresni a `memory.dmp` fájlt, akkor inkább a [nem maszkolt megszakítás (NMI) hívásokat használja a soros konzolon](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . Az [összeomlási memóriakép fájljának NMI-hívásokkal történő létrehozásához](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump)kövesse az útmutatót.
