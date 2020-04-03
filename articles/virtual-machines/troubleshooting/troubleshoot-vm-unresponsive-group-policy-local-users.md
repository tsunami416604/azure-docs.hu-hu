---
title: A Virtuálisgép nem válaszol a "Csoportházirend helyi felhasználói & csoportok" házirend alkalmazása közben
description: Ez a cikk lépéseket tartalmaz a problémák megoldásához, ahol a betöltési képernyő beragadt egy szabályzat alkalmazása során egy Azure virtuális gép (VM) rendszerindításakor.
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
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620856"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>A Virtuálisgép nem válaszol a "Csoportházirend helyi felhasználói & csoportok" házirend alkalmazása közben

Ez a cikk lépéseket tartalmaz a problémák megoldásához, ahol a betöltési képernyő beragadt egy szabályzat alkalmazása, a rendszerindítás során, egy Azure virtuális gép (VM).

## <a name="symptom"></a>Hibajelenség

Amikor [a rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) segítségével tekinti meg a virtuális gép képernyőképének megtekintését, látni fogja, hogy a képernyő beragadt a következő üzenettel: *Csoportházirend helyi felhasználók és csoportok házirend alkalmazása*.

![Helyettesítő szöveg: A Csoportházirend helyi felhasználók és csoportok házirendjének betöltését (Windows Server 2012) látható képernyőn.](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Helyettesítő szöveg: A Csoportházirend helyi felhasználók és csoportok házirendjének betöltését (Windows Server 2012 R2) látható képernyőn.](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Ok

A fagyás tüneteit a Windows Profilszolgáltatás dinamikus csatolású függvénykönyvtárának *(profsvc.dll)* kódhibája okozza.

> [!NOTE]
> Ez a hiba csak Windows Server 2012 és Windows Server 2012 R2 rendszeren alkalmazható.

### <a name="the-policy-in-question"></a>A szóban forgó politika

A folyamatokat nem lefolytató házirend a következő:

* *Számítógép konfigurációja\Házirendek\Felügyeleti sablonok\Rendszer/felhasználói profilok\A rendszer újraindításakor megadott számú napnál régebbi felhasználói profilok törlése*

Ez a házirend csak akkor függ le, ha a következő hat feltétel teljesül:

* A *megadott számú napnál régebbi felhasználói profilok törlése a rendszer újraindítási házirendje* engedélyezve van.
* Olyan profilokkal rendelkezik, amelyek megfelelnek a törléshez szükséges életkori követelményeknek.
* Olyan összetevőket tartalmaz, amelyek törlési értesítésre vannak regisztrálva a profilokhoz.
* Az összetevők olyan (közvetlen vagy közvetett) hívásokat kezdeményeznek, amelyeknek adatokat kell beszerezniük a Windows Szolgáltatásvezérlő (SCM) összetevőitől, például egy szolgáltatás indítási, leállítási vagy lekérdezési információitól.
* Van egy szolgáltatás, amely *automatikus*indításra van beállítva.
* Ez a szolgáltatás úgy van beállítva, hogy egy tartományi fiók környezetében fusson (szemben a beépített fiók, például egy helyi rendszer használatával).

### <a name="the-code-defect"></a>A kódhiba

A kódhiba annak köszönhető, hogy a Szolgáltatásvezérlő (SCM) és a Profil szolgáltatások egyszerre próbálnak zárolásokat alkalmazni egymásra. Zárolások azért léteznek, hogy több szolgáltatás ne módosítson ugyanazon az adaton egyszerre, ami sérüléshez vezethet. Általában több zárolási kérelem nem okoz problémát. Mivel azonban ez a rendszerindítás során történik, egyik szolgáltatás sem tudja befejezni a folyamatait, mivel egymásra várnak.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS Bug 5880648 – A Szolgáltatásvezérlés-kezelő holtpontra jutott a "Felhasználói profilok törlése újraindításkor" házirenddel

Két olyan művelet létezik, amelyek átfedik egymást, hogy:

* Az 1.

  **És**

* A 2.

Ha ez a holtpont bekövetkezik, a második szükséges zárolás beszerzésére tett kísérlet lefagy a művelet.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>1. művelet - Régi profiltörlési értesítés **(profilzárolással**rendelkezik, **SCM-zárolást**igényel)

1. Először a régi profilok törlésére beállított házirend beszerzi a belső profil szolgáltatászárolását.

   * Ez a zárolás megakadályozza, hogy két szál kölcsönhatásba lépjen a profilokkal, amíg a *törlési művelet* folyamatban van.

2. A házirend megkeresi azokat a profilokat, amelyek elég idősek a törléshez.
3. A profil törlésének részeként egy olyan összetevő, amely regisztrált egy profil törléséről szóló értesítésekre, megpróbál **elindítani egy szolgáltatást.**
4. A szolgáltatás megkezdése előtt a Szolgáltatásvezérlőnek (SCM) be kell szereznie egy **belső SCM-zárat,** amelyet a **2.**

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>2. művelet - Profilbetöltés/létrehozás felhasználóspecifikus adatokhoz **(SCM lock**, profile **lock**szükséges)

1. A rendszerindításkor az SCM-nek meg kell rendelnie az összes *automatikus indítási* szolgáltatást a csoportjukonként, valamint minden olyan szolgáltatást, amelytől ezek a szolgáltatások függnek.

2. **Az SCM beszerez egy belső SCM-zárolást,** amely a szolgáltatások indítása, leállítása vagy konfigurálása érdekében használható.

3. Miután a szolgáltatások rendben vannak, az SCM végighalad az egyes szolgáltatásokon, és elindítja azt.

4. Ha a szolgáltatás egy tartományi fiók környezetében fut, a profilt be kell tölteni vagy létre kell hozni a tartományi fiókhoz, hogy felhasználói adatokat tárolhasson.

5. Ezt a kérést a rendszer elküldi a **Profilszolgáltatásnak.**

6. A profilszolgáltatásnak hozzá kell férnie **az** **1.**

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése

1. Virtuális gép létrehozása és elérése
2. Soros konzol és memóriakép-gyűjtemény engedélyezése
3. A virtuális gép újraépítése
4. A memóriaképfájl gyűjtése

   > [!NOTE]
   > A rendszerindítási hiba esetén a vendég operációs rendszer nem működik. A probléma megoldásához offline módban kell hibaelhárítást elhárítania.

### <a name="create-and-access-a-repair-vm"></a>Virtuális gép létrehozása és elérése

1. A [virtuális gép javítási parancsainak 1-3.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)
2. A Távoli asztali kapcsolat használatával csatlakozzon a javítási virtuális géphez.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Soros konzol és memóriakép-gyűjtemény engedélyezése

A memóriakép-gyűjtemény és a Soros konzol engedélyezéséhez futtassa az alábbi parancsfájlt:

1. Nyisson meg egy rendszergazdai parancssori munkamenetet (Futtatás rendszergazdaként).
2. Futtassa az alábbi parancsot:

   * Soros konzol engedélyezése:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Ellenőrizze, hogy az operációs rendszer lemezén lévő szabad terület legalább annyira megvan-e, mint a virtuális gép memóriamérete (RAM).

   * Ha nincs elég hely az operációs rendszer lemezén, meg kell változtatnia a helyet, ahol a memóriakép fájl jön létre, és olvassa el, hogy minden adatlemez csatlakozik a virtuális gép, amely elegendő szabad terület. A hely módosításához `%SystemRoot%` cserélje le az alábbi parancsokban lévő adatlemez meghajtóbetűjelét (például "F:").

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

### <a name="rebuild-the-vm"></a>A virtuális gép újraépítése

A [virtuális gép javítási parancsainak 5.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)

### <a name="collect-the-memory-dump-file"></a>A memóriaképfájl gyűjtése

A probléma megoldásához először össze kell gyűjtenie az összeomlás memóriaképfájlját, és kapcsolatba kell lépnie a memóriaképfájl lal. A memóriaképfájl összegyűjtéséhez hajtsa végre az alábbi lépéseket:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Az operációs rendszer lemezének csatolása új javítási virtuális géphez

1. A [virtuális gép javítási parancsainak 1-3.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)

2. A Távoli asztali kapcsolat használatával csatlakozzon a javítási virtuális géphez.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Keresse meg a memóriaképfájlt, és küldje el a támogatási jegyet

1. A javítási virtuális gép, ugrás a Windows mappába a csatlakoztatott operációs rendszer lemezén. Ha a csatlakoztatott operációsrendszer-lemezhez rendelt illesztőprogram-betű "F" betű, akkor az F:\Windows rendszerre kell lépnie.

2. Keresse meg a memory.dmp fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriaképfájllal.

3. Ha nem találja a memory.dmp fájlt, célszerű lehet [nem maszkolható megszakítási (NMI) hívásokat használni a soros konzolon.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) Az útmutató tkövetően [létrehozhat egy kernelt vagy egy teljes összeomlási memóriaképfájlt](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) NMI-hívások használatával.
