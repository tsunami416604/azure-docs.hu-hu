---
title: Rendszerállapot biztonsági mentése – problémamegoldás
description: Ebből a cikkből megtudhatja, hogy miként háríthatók el a helyszíni Windows-kiszolgálók rendszerállapot-biztonsági másolatában.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 28647b72334d592692c5fe1b031735330d1a0509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969577"
---
# <a name="troubleshoot-system-state-backup"></a>Rendszerállapot biztonsági mentése – problémamegoldás

Ez a cikk a Rendszerállapot biztonsági másolat használata során felmerülő problémák megoldásait ismerteti.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Javasoljuk, hogy a rendszerállapot biztonsági mentésének hibaelhárítása megkezdése előtt hajtsa végre az alábbi érvényesítést:

- [Annak biztosítása, hogy a Microsoft Azure Recovery Services (MARS) ügynök naprakész legyen](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Győződjön meg arról, hogy a MARS-ügynök és az Azure között van hálózati kapcsolat](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Győződjön meg arról, hogy a Microsoft Azure Recovery Services fut (a Szolgáltatás konzolon). Szükség esetén indítsa újra, majd próbálkozzon újra a művelettel
- [Győződjön meg arról, hogy 5-10% szabad hellyel rendelkezik az ideiglenes mappa helyén](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Ellenőrizze, hogy egy másik folyamat vagy víruskereső szoftver nem zavarja-e az Azure Backup működését](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Az ütemezett biztonsági mentés meghiúsul, de a manuális biztonsági mentés sikeres](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Ellenőrizze, hogy az operációs rendszer rendelkezik-e a legújabb frissítésekkel
- [Annak biztosítása, hogy a nem támogatott meghajtók és a nem támogatott attribútumokkal rendelkező fájlok ki legyenek zárva a biztonsági mentésből](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Győződjön meg arról, hogy a **rendszeróra** a védett rendszeren az időzóna javítására van beállítva <br>
- [Győződjön meg arról, hogy a kiszolgáló rendelkezik a .NET-keretrendszer 4.5.2-es vagy annál frissebb verziójával](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Ha újra regisztrálni szeretné **a kiszolgálót** egy trezorba, akkor: <br>
  - Az ügynök eltávolítása a kiszolgálón, és a portálról való törlés <br>
  - Használja ugyanazt a jelszót, amelyet kezdetben használt a kiszolgáló regisztrálásához <br>
- Ha ez egy offline biztonsági másolat, győződjön meg arról, hogy az Azure PowerShell 3.7.0-s verziója telepítve van a forrás- és a másolószámítógépen is, mielőtt elkezdené az offline biztonsági mentési műveletet
- [Figyelembe venni, ha a biztonságimásolat-ügynök azure-beli virtuális gépen fut](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Korlátozás

- A Microsoft nem javasolja, hogy más hardverre próbáljon helyreállítást végezni a rendszerállapot-helyreállítással
- A rendszerállapot biztonsági mentése jelenleg a "helyszíni" Windows-kiszolgálókat támogatja. Ez a funkció nem érhető el az Azure virtuális gépek.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hibaelhárítási rendszerállapot biztonsági mentés az Azure Backup, végezze el az alábbi előfeltételek ellenőrzése.  

### <a name="verify-windows-server-backup-is-installed"></a>A Windows Server biztonsági másolat telepítése

Győződjön meg arról, hogy a Windows Server biztonsági másolat telepítve van és engedélyezve van a kiszolgálón. A telepítési állapot ellenőrzéséhez futtassa ezt a PowerShell-parancsot:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Ha a kimenet **elérhetőként**jeleníti meg a **telepítési állapotot,** akkor ez azt jelenti, hogy a Windows Server biztonsági mentési szolgáltatása elérhető a telepítéshez, de nincs telepítve a kiszolgálóra. Ha azonban a Windows Server biztonsági másolat nincs telepítve, az alábbi módszerek egyikével telepítse azt.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>1. módszer: A Windows Server biztonsági másolat telepítése a PowerShell használatával

A Windows Server biztonsági másolat PowerShell használatával történő telepítéséhez futtassa az alábbi parancsot:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>2. módszer: A Windows Server biztonsági másolat telepítése a Kiszolgálókezelő vel

A Windows Server biztonsági másolat kiszolgálókezelővel történő telepítéséhez hajtsa végre az alábbi lépéseket:

1. A **Kiszolgálói segédben**kattintson a **Szerepkörök és szolgáltatások hozzáadása gombra.** Megjelenik **a Szerepkörök és szolgáltatások hozzáadása varázsló.**

    ![Irányítópult](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Válassza a **Telepítés típusa lehetőséget,** és kattintson a **Tovább**gombra.

    ![Telepítés típusa](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Jelöljön ki egy kiszolgálót a kiszolgálókészletből, és kattintson a **Tovább**gombra. A kiszolgálói szerepkörben hagyja meg az alapértelmezett beállítást, és kattintson a **Tovább**gombra.
4. Válassza a **Windows Server biztonsági másolat lehetőséget** a Szolgáltatások **lapon,** és kattintson a **Tovább**gombra.

    ![funkciók](./media/backup-azure-system-state-troubleshoot/features.png)

5. A **telepítési** folyamat elindításához kattintson a **Megerősítés** lapon a Telepítés gombra.
6. Az **Eredmények** lapon megjelenik a Windows Server biztonsági másolat szolgáltatás sikeresen telepítve a Windows Server rendszeren.

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Rendszerkötet-információs engedély

Győződjön meg arról, hogy a Helyi rendszer teljes hozzáféréssel rendelkezik **a** Windows rendszert tartalmazó kötetinformációs mappában. Ez általában **a C:\Rendszerkötet-információ**. A Windows Server biztonsági mentése sikertelen lehet, ha a fenti engedélyek nincsenek megfelelően beállítva

### <a name="dependent-services"></a>Függő szolgáltatások

Győződjön meg arról, hogy az alábbi szolgáltatások futó állapotban vannak:

**Szolgáltatásnév** | **Indítás típusa**
--- | ---
Távoli eljáráshívás(RPC) | Automatikus
COM+ eseményrendszer(EventSystem) | Automatikus
Rendszeresemény-értesítési szolgáltatás (SENS) | Automatikus
Kötet árnyékmásolata(VSS) | Kézi
Microsoft szoftver árnyékmásolat-szolgáltatója (SWPRV) | Kézi

### <a name="validate-windows-server-backup-status"></a>A Windows Server biztonsági másolat állapotának ellenőrzése

A Windows Server biztonsági másolat állapotának ellenőrzéséhez hajtsa végre az alábbi lépéseket:

- Annak ellenőrzése, hogy fut-e a WSB PowerShell

  - Fusson `Get-WBJob` egy emelt szintű PowerShellből, és győződjön meg arról, hogy nem adja vissza a következő hibát:

    > [!WARNING]
    > Get-WBJob: A "Get-WBJob" kifejezés nem ismerhető fel parancsmag, függvény, parancsfájl vagy működőképes program neveként. Ellenőrizze a név helyesírását, vagy ha elérési utat is tartalmaz, ellenőrizze, hogy helyes-e az elérési út, majd próbálkozzon újra.

    - Ha ez a hiba sikertelen, telepítse újra a Windows Server biztonsági másolat szolgáltatást a kiszolgálón az előfeltételek 1.

  - Győződjön meg arról, hogy a WSB biztonsági mentése megfelelően működik, ha az alábbi parancsot a rendszergazda jogú parancssorból futtatja:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Cserélje le az X-et annak a kötetnek a meghajtóbetűjelére, amelyen a rendszerállapot biztonsági másolatot szeretne kapni a lemezképről.

    - Rendszeresen ellenőrizze a feladat állapotát `Get-WBJob` a rendszergazda powershellből származó parancs futtatásával
    - A biztonsági mentési feladat befejezése után ellenőrizze `Get-WBJob -Previous 1` a feladat végleges állapotát a parancs futtatásával

Ha a feladat sikertelen, azt jelzi, hogy a WSB probléma, amely a MARS-ügynök rendszerállapot-biztonsági mentések hiba.

## <a name="common-errors"></a>Gyakori hibák

### <a name="vss-writer-timeout-error"></a>VSS Writer idő-elállási hiba

| Hibajelenség | Ok | Megoldás:
| -- | -- | --
| - A MARS-ügynök a következő hibaüzenettel sikertelen: "A WSB-feladat VSS-hibákkal nem sikerült. Ellenőrizze a VSS eseménynaplókat a hiba megoldásához"<br/><br/> - A következő hibanapló a VSS alkalmazás eseménynaplóiban található: "A VSS-író elutasította a 0x800423f2 hibával rendelkező eseményt, az író időtúlszerződése lejárt a Freeze és a Thaw események között."| A VSS-író nem tudja időben befejezni a processzor- és memóriaerőforrások hiánya miatt a számítógépen <br/><br/> Egy másik biztonsági másolat készítő szoftver már használja a VSS írót, ennek eredményeként a pillanatkép-művelet nem fejeződött be ehhez a biztonsági mentéshez | Várja meg, amíg a CPU/memória felszabadul a rendszeren, vagy szakítsa meg a túl sok memóriát/CPU-t elvevő folyamatokat, és próbálja meg újra a műveletet. <br/><br/>  Várja meg, amíg a folyamatban lévő biztonsági mentés befejeződik, és próbálja meg a műveletet egy későbbi időpontban, amikor nem fut nak biztonsági mentések a gépen.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Nincs elég lemezterület az árnyékmásolatok bővítéséhez

| Hibajelenség | Megoldás:
| -- | --
| - A MARS-ügynök sikertelen a hibaüzenettel: A biztonsági mentés nem sikerült, mert az árnyékmásolat kötete nem növekedett, mert nincs elegendő lemezterület a rendszerfájlokat tartalmazó köteteken <br/><br/> - A következő hiba/figyelmeztetésnapló jelenik meg a volsnap rendszer eseménynaplóiban: "A C köteten nem volt elegendő lemezterület: a C árnyékmásolatainak tárolásának növelése: e hiba miatt a C kötet összes árnyékmásolata: fennáll a törlés veszélye" | - Szabadítson fel helyet a kiemelt köteten az eseménynaplóban, hogy elegendő hely legyen az árnyékmásolatok növekedéséhez, miközben a biztonsági mentés folyamatban van <br/><br/> - Az árnyékmásolat-terület konfigurálása során korlátozhatjuk az árnyékmásolat számára felhasznált helyet. További információt ebben a [cikkben](https://docs.microsoft.com/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage) talál.

### <a name="efi-partition-locked"></a>EFI partíció zárolva

| Hibajelenség | Megoldás:
| -- | --
| A MARS-ügynök a következő hibaüzenettel sikertelen: "A rendszerállapot biztonsági mentése nem sikerült, mert az EFI rendszerpartíció zárolva van. Ennek oka lehet a rendszerpartíció-hozzáférés egy külső biztonsági rendszer, vagy szoftver biztonsági másolatot készíteni" | - Ha a probléma miatt egy harmadik fél biztonsági szoftver, akkor meg kell, hogy forduljon az Anti Virus eladó, hogy azok lehetővé teszik mars ügynök <br/><br/> - Ha egy harmadik féltől származó biztonsági mentési szoftver fut, akkor várja meg, amíg befejeződik, majd próbálja meg újra biztonsági másolatot készíteni

## <a name="next-steps"></a>További lépések

- A Windows rendszerállapotáról az Erőforrás-kezelő központi telepítésében a [Windows Server rendszerállapotának biztonsági és biztonsági üzembenmaradása című](backup-azure-system-state.md) témakörben talál további információt.
