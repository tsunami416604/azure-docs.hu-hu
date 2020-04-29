---
title: A virtuális gép nem válaszol a "Csoportházirend helyi felhasználók & csoportok" házirend alkalmazása során
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekkel a betöltési képernyő egy Azure-beli virtuális gép (VM) indításakor elakadt egy szabályzat alkalmazásával.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80620856"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>A virtuális gép nem válaszol a "Csoportházirend helyi felhasználók & csoportok" házirend alkalmazása során

Ez a cikk azokat a problémákat ismerteti, amelyekkel elháríthatja a betöltési képernyőt, ha a rendszerindítás során egy Azure-beli virtuális gép (VM) használatával elakadt egy házirend alkalmazása.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztikát](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használ a virtuális gép képernyőképének megtekintéséhez, látni fogja, hogy a képernyő betöltődik a következő üzenettel: *csoportházirend helyi felhasználók és csoportok házirend alkalmazása*.

![ALT text: képernyő, amely Csoportházirend helyi felhasználók és csoportok betöltésére vonatkozó házirend betöltését mutatja be (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Helyettesítő szöveg: Csoportházirend helyi felhasználók és csoportok alkalmazásával kapcsolatos házirend betöltését bemutató képernyő (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Ok

A befagyasztás tüneteit a Windows-profil szolgáltatás dinamikus csatolású kódtára (*profsvc. dll*) által okozott hibakód okozza.

> [!NOTE]
> Ez a hiba csak a Windows Server 2012-es és a Windows Server 2012 R2-es verzióra vonatkozik.

### <a name="the-policy-in-question"></a>A szóban forgó szabályzat

Az alkalmazott házirend nem fejeződik be a folyamatok:

* *Számítógép Computer sablonok \ rendszer/felhasználó Profiles\Delete felhasználói profilok régebbi, mint a rendszer újraindításakor megadott számú nap.*

Ez a szabályzat csak akkor jelenik meg, ha a következő hat feltétel teljesül:

* A *rendszer-újraindítási házirendben megadott számú napnál régebbi felhasználói profilok törlése* engedélyezve van.
* Olyan profilokkal rendelkezik, amelyek eleget tettek a tisztításhoz szükséges életkori követelményeknek.
* Vannak olyan összetevői, amelyek regisztráltak a profilok törlési értesítésére.
* Az összetevők olyan hívásokat kezdeményeznek (közvetlen vagy közvetett), amelyeknek adatokat kell bekérniük a Windows Service Control Manager (SCM) összetevőiről, például az indítás, a Leállítás vagy a szolgáltatás lekérdezési információit.
* Van egy olyan szolgáltatás, amely *automatikus*indításra van konfigurálva.
* A szolgáltatás egy tartományi fiók környezetében való futtatásra van beállítva (a beépített fiók, például egy helyi rendszer használata helyett).

### <a name="the-code-defect"></a>A kód hibája

A kód hibáját a szolgáltatásvezérlő kezelője (SCM) és a profiling Services egyidejű zárolások alkalmazására irányuló kísérlete okozza. A zárolások nem akadályozzák meg, hogy egyszerre több szolgáltatás is módosítsa ugyanazon az adatokon, ami a sérülést okozhatja. Általában több zárolási kérelem nem okoz problémát. Mivel azonban ez a rendszerindítás során történik, egyik szolgáltatás sem tudja befejezni a folyamatokat, mivel azok egymásra várnak.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OPERÁCIÓSRENDSZER-hiba 5880648 – a Service Control Manager holtpontja a "felhasználói profilok törlése az újraindításkor" házirendben

Két művelet átfedésben van, így:

* Az 1. művelet megszerzi a profil zárolását, de még nem szerezte be az SCM zárolását.

  **ÉS**

* A 2. művelet megszerzi az SCM-zárolást, de még nem szerezte be a profil zárolását.

Ha ez a holtpont következik be, a második szükséges zárolás beolvasására tett kísérlet lefagy a művelettel.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>1. művelet – régi profil törlési értesítése ( **profil zárolása**, **SCM-zárolás**szükséges)

1. Először is a régi profilok törlésére beállított házirend egy belső profil szolgáltatás zárolását szerzi be.

   * Ez a zárolás megakadályozza, hogy két szál kommunikáljon a profilokkal, miközben a *törlési művelet* folyamatban van.

2. A házirend olyan profilokat talál, amelyek elég elavultak a törléshez.
3. A profil törlésének részeként egy olyan összetevő, amely egy profil törlési értesítéseire van regisztrálva, megkísérli **elindítani a szolgáltatást**.
4. A szolgáltatás elindítása előtt a Service Control Manager (SCM) egy **belső SCM-zárolást** igényel, amelyet a **2. művelet**szálai tárolnak.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>2 **. művelet – profil**betöltése/létrehozása a felhasználó által megadott adatértékekhez (az **SCM zárolása**szükséges)

1. A rendszerindításkor az SCM-nek meg kell rendelnie az összes *automatikus indítási* szolgáltatást a csoportjuk alapján, valamint a szolgáltatástól függő szolgáltatásokat.

2. Az **SCM egy belső SCM-zárolást szerez** be, amellyel szabályozható a szolgáltatások indítása, leállítása vagy konfigurálása a szolgáltatások megrendelése során.

3. A szolgáltatások megrendelése után az SCM hurkok az egyes szolgáltatásokon keresztül indulnak el.

4. Ha a szolgáltatás egy tartományi fiók környezete alatt fut, akkor a profilt be kell tölteni, vagy létre kell hozni a tartományi fiókhoz, hogy az képes legyen a felhasználói specifikus adat tárolására.

5. Ezt a kérést a rendszer elküldi a **profil szolgáltatásnak**.

6. A profil szolgáltatásnak hozzá kell férnie az 1. **műveletben**beszerzett **belső zároláshoz** .

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése

1. Javítási virtuális gép létrehozása és elérése
2. Soros konzol és memóriakép-gyűjtemény engedélyezése
3. A virtuális gép újraépítése
4. A memóriakép fájljának összegyűjtése

   > [!NOTE]
   > Ha ezt a rendszerindítási hibát tapasztalja, a vendég operációs rendszer nem működik. A probléma megoldásához offline módban hibaelhárítást hajthat végre.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok 1-3-es lépéseit](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) használva készítse elő a javítási virtuális gépet.
2. A Távoli asztali kapcsolat kapcsolódása a javítási virtuális géphez.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Soros konzol és memóriakép-gyűjtemény engedélyezése

A memóriakép-gyűjtés és a soros konzol engedélyezéséhez futtassa az alábbi szkriptet:

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (Futtatás rendszergazdaként).
2. Futtassa az alábbi parancsot:

   * Soros konzol engedélyezése:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Győződjön meg arról, hogy az operációsrendszer-lemezen lévő szabad terület a virtuális gép memóriájának méretétől (RAM) függ.

   * Ha nincs elég hely az operációsrendszer-lemezen, akkor módosítania kell a memóriakép fájljának helyét, és a virtuális géphez csatlakoztatott minden olyan adatlemezre, amely elegendő szabad hellyel rendelkezik. A hely módosításához cserélje le `%SystemRoot%` az adatlemez meghajtóbetűjelét (például "F:") az alábbi parancsokban található adatlemezre.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Az operációs rendszer kiírásának engedélyezése javasolt konfiguráció

**Sérült operációsrendszer-lemez betöltése:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Engedélyezés a ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Engedélyezés a ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>A virtuális gép újraépítése

A virtuális gép újraösszeállításához használja [a virtuális gép javítási parancsainak 5. lépését](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) .

### <a name="collect-the-memory-dump-file"></a>A memóriakép fájljának összegyűjtése

A probléma megoldásához először össze kell gyűjtenie az összeomláshoz tartozó memóriakép-fájlt, és kapcsolatba kell lépnie az ügyfélszolgálattal a memóriakép fájljával. A memóriakép-fájl összegyűjtéséhez kövesse az alábbi lépéseket:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Az operációsrendszer-lemez csatlakoztatása egy új javítási virtuális géphez

1. Egy új javítási virtuális gép előkészítéséhez használja [a virtuális gép javítási parancsainak 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) . lépéseit.

2. A Távoli asztali kapcsolat kapcsolódása a javítási virtuális géphez.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>A memóriaképfájl megkeresése és támogatási jegy beküldése

1. A javítás virtuális gépen nyissa meg a Windows mappát a csatolt operációsrendszer-lemezen. Ha a csatlakoztatott operációsrendszer-lemezhez hozzárendelt illesztőprogram betűjele F, akkor a F:\Windows. kell lépnie.

2. Keresse meg a Memory. dmp fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriakép fájljával.

3. Ha nem sikerül megkeresni a Memory. dmp fájlt, előfordulhat, hogy [nem maszkolt megszakítási (NMI) hívásokat kíván használni a soros konzolon](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . Az útmutatót követve [létrehozhat egy kernelt vagy elvégezheti az összeomlási memóriakép](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) fájlját a NMI-hívások használatával.
