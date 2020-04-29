---
title: Rendszerállapot biztonsági mentésének hibáinak megoldása
description: Ebből a cikkből megtudhatja, hogyan lehet elhárítani a rendszerállapot biztonsági mentésével kapcsolatos problémákat a helyszíni Windows-kiszolgálókon.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 28647b72334d592692c5fe1b031735330d1a0509
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78969577"
---
# <a name="troubleshoot-system-state-backup"></a>Rendszerállapot biztonsági mentésének hibáinak megoldása

Ez a cikk a rendszerállapot biztonsági mentésének használata során felmerülő problémák megoldásait ismerteti.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Javasoljuk, hogy a rendszerállapot biztonsági mentésének megkezdése előtt végezze el az alábbi érvényesítést:

- [Győződjön meg arról, Microsoft Azure Recovery Services (MARS) ügynök naprakész](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Győződjön meg arról, hogy a MARS-ügynök és az Azure között van hálózati kapcsolat](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Győződjön meg arról, hogy a Microsoft Azure Recovery Services fut (a Szolgáltatás konzolon). Szükség esetén indítsa újra, és ismételje meg a műveletet.
- [Győződjön meg arról, hogy 5-10% szabad hellyel rendelkezik az ideiglenes mappa helyén](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Ellenőrizze, hogy egy másik folyamat vagy víruskereső szoftver nem zavarja-e az Azure Backup működését](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Az ütemezett biztonsági mentés meghiúsul, de a manuális biztonsági mentés sikeres](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Ellenőrizze, hogy az operációs rendszer rendelkezik-e a legújabb frissítésekkel
- [Győződjön meg arról, hogy a nem támogatott, nem támogatott attribútumokkal rendelkező meghajtók és fájlok ki vannak zárva a biztonsági mentésből](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Győződjön meg arról, hogy a védett rendszer **Rendszerórájának** beállítása helyes időzóna <br>
- [Győződjön meg arról, hogy a kiszolgáló rendelkezik a .NET-keretrendszer 4.5.2-es vagy annál frissebb verziójával](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Ha a kiszolgálót egy tárolóba próbálja újra **regisztrálni** , akkor: <br>
  - Győződjön meg arról, hogy az ügynök el lett távolítva a-kiszolgálón, és a portálról törlődik <br>
  - Használja ugyanazt a jelszót, amelyet kezdetben használt a kiszolgáló regisztrálásához <br>
- Ha ez egy offline biztonsági mentés, győződjön meg arról, hogy a 3.7.0 Azure PowerShell verziója a forrás-és a másolási számítógépen is telepítve van az offline biztonsági mentési művelet megkezdése előtt.
- [A biztonsági mentési ügynök Azure-beli virtuális gépen való futtatásakor megfontolandó szempontok](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Korlátozás

- A Microsoft nem javasolja, hogy más hardverre próbáljon helyreállítást végezni a rendszerállapot-helyreállítással
- A rendszerállapot biztonsági mentése jelenleg a "helyszíni" Windows-kiszolgálókat támogatja. Ez a funkció nem érhető el az Azure-beli virtuális gépekhez.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt a rendszerállapot biztonsági mentését a Azure Backupával hárítsa el, hajtsa végre az alábbi előfeltételek ellenőrzését.  

### <a name="verify-windows-server-backup-is-installed"></a>Windows Server biztonsági másolat telepítésének ellenőrzése

Győződjön meg arról, hogy a Windows Server biztonsági másolat telepítve van és engedélyezve van a kiszolgálón. A telepítés állapotának megtekintéséhez futtassa a következő PowerShell-parancsot:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Ha a kimenet a telepítési **állapotot** **elérhetőként**jeleníti meg, akkor az azt jelenti, hogy a Windows Server biztonsági másolat szolgáltatás elérhető a telepítéshez, de nincs telepítve a kiszolgálón. Ha azonban Windows Server biztonsági másolat nincs telepítve, a telepítéshez használja az alábbi módszerek egyikét.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>1. módszer: Windows Server biztonsági másolat telepítése a PowerShell használatával

Windows Server biztonsági másolat a PowerShell használatával történő telepítéséhez futtassa az alábbi parancsot:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>2. módszer: Windows Server biztonsági másolat telepítése a Kiszolgálókezelő használatával

Windows Server biztonsági másolat telepítéséhez a Kiszolgálókezelő használatával hajtsa végre az alábbi lépéseket:

1. A **Server Managerben**kattintson a **szerepkörök és szolgáltatások hozzáadása**elemre. Megjelenik a **szerepkörök és szolgáltatások hozzáadása varázsló** .

    ![Irányítópult](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Válassza a **telepítés típusa** lehetőséget, majd kattintson a **tovább**gombra.

    ![Telepítés típusa](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Válasszon ki egy kiszolgálót a kiszolgáló-készletből, és kattintson a **tovább**gombra. A kiszolgálói szerepkörben hagyja meg az alapértelmezett beállítást, és kattintson a **tovább**gombra.
4. Válassza a **Windows Server biztonsági másolat** elemet a **szolgáltatások** lapon, és kattintson a **tovább**gombra.

    ![funkciók](./media/backup-azure-system-state-troubleshoot/features.png)

5. A **megerősítés** lapon kattintson a **telepítés** gombra a telepítési folyamat elindításához.
6. A **Results (eredmények** ) lapon megjelenik a Windows Server biztonsági másolat funkció a Windows Serverre való telepítése sikeres volt.

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Rendszerkötet információi engedély

Győződjön meg arról, hogy a helyi rendszer teljes hozzáféréssel rendelkezik a **rendszerkötet-információ** mappájában, amely abban a kötetben található, amelyben a Windows telepítve van. Általában ez a **C:\System**. A Windows Server biztonsági másolat sikertelen lehet, ha a fenti engedélyek nincsenek megfelelően beállítva

### <a name="dependent-services"></a>Függő szolgáltatások

Győződjön meg arról, hogy az alábbi szolgáltatások fut állapotban vannak:

**Szolgáltatásnév** | **Indítási típus**
--- | ---
Távoli eljáráshívás (RPC) | Automatikus
COM+ Event System (EventSystem) | Automatikus
Rendszeresemény-értesítési szolgáltatás (SENS) | Automatikus
Kötet árnyékmásolata (VSS) | Kézi
Microsoft szoftver árnyékmásolata szolgáltató (SWPRV) | Kézi

### <a name="validate-windows-server-backup-status"></a>Windows Server biztonsági másolat állapotának ellenőrzése

Windows Server biztonsági másolat állapotának ellenőrzéséhez hajtsa végre a következő lépéseket:

- Győződjön meg arról, hogy a WSB PowerShell fut

  - Futtasson `Get-WBJob` egy emelt szintű PowerShell-lel, és győződjön meg róla, hogy nem adja vissza a következő hibát:

    > [!WARNING]
    > Get-WBJob: a "Get-WBJob" kifejezés nem ismerhető fel parancsmag, függvény, parancsfájl vagy működőképes program neveként. Ellenőrizze a név helyesírását, vagy ha egy elérési utat tartalmaz, ellenőrizze, hogy helyes-e az elérési út, és próbálkozzon újra.

    - Ha ez a hiba meghiúsul, akkor telepítse újra a Windows Server biztonsági másolat szolgáltatást a kiszolgáló gépen az előfeltételek 1. lépésében leírtak szerint.

  - Győződjön meg arról, hogy a WSB biztonsági mentése megfelelően működik, és futtassa az alábbi parancsot a rendszergazda jogú parancssorból:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Az X helyére írja be annak a kötetnek a betűjelét, amelyben a rendszerállapot biztonsági másolatát szeretné tárolni.

    - A feladatok állapotának rendszeres ellenőrzéséhez futtassa `Get-WBJob` a parancsot a rendszergazda jogú powershellből
    - A biztonsági mentési feladatok befejezése után a parancs futtatásával `Get-WBJob -Previous 1` vizsgálja meg a feladatok végső állapotát.

Ha a feladat meghiúsul, egy WSB-problémát jelez, amely a MARS-ügynök rendszerállapot-biztonsági Mentéseinak meghibásodását eredményezné.

## <a name="common-errors"></a>Gyakori hibák

### <a name="vss-writer-timeout-error"></a>VSS-író időtúllépési hibája

| Hibajelenség | Ok | Megoldás:
| -- | -- | --
| -A MARS-ügynök a következő hibaüzenettel meghiúsul: "a WSB-feladatban hiba történt VSS-hibákkal. A hiba elhárításához keresse meg a VSS-eseménynaplókat.<br/><br/> -A VSS-alkalmazás eseménynaplójában a következő hibanapló szerepel: "A VSS-író elutasította az eseményt hibás 0x800423f2, az író időtúllépése lejárt a befagyasztási és felolvasztási események között."| A VSS-író nem tud időben befejezni a számítógép processzor-és memória-erőforrásainak hiánya miatt. <br/><br/> Egy másik biztonsági mentési szoftver már használja a VSS-írót, mert nem sikerült befejezni a biztonsági mentés eredményének pillanatkép-műveletét | Várjon, amíg a CPU vagy a memória fel lesz szabadítva a rendszeren, vagy szakítsa meg a folyamatokat túl sok memóriát/PROCESSZORt, majd próbálja megismételni a műveletet. <br/><br/>  Várjon, amíg a folyamatban lévő biztonsági mentés befejeződik, majd próbálja megismételni a műveletet, amikor a gépen nem fut biztonsági másolat.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Nincs elegendő lemezterület az árnyékmásolatok növeléséhez

| Hibajelenség | Megoldás:
| -- | --
| -A MARS-ügynök a következő hibaüzenettel meghiúsul: a biztonsági mentés nem sikerült, mert az árnyékmásolat-kötet nem tudott növekedni, mert nincs elég szabad lemezterület a rendszerfájlokat tartalmazó köteteken. <br/><br/> -A következő hiba/figyelmeztetési napló szerepel a VolSnap rendszeresemény-naplóiban: "nincs elég szabad lemezterület a C köteten: az árnyékmásolat-tároló növelése a C-beli árnyékmásolatok esetében – ennek a hibának a hatására a c kötet összes árnyékmásolatai-példánya törlődik:" | – Szabadítson fel lemezterületet a kijelölt köteten az eseménynaplóban, hogy elegendő lemezterület álljon rendelkezésre az árnyékmásolatok növekedéséhez, miközben a biztonsági mentés folyamatban van. <br/><br/> – Az árnyékmásolat-terület konfigurálásával korlátozható az árnyékmásolat-használathoz felhasznált terület mennyisége. További információkért tekintse meg ezt a [cikket](https://docs.microsoft.com/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>EFI-partíció zárolva

| Hibajelenség | Megoldás:
| -- | --
| A MARS-ügynök a következő hibaüzenettel meghiúsul: "a rendszerállapot biztonsági mentése nem sikerült, mert az EFI rendszerpartíció zárolva van. Ezt az okozhatja, hogy a rendszerpartíciók a harmadik féltől származó biztonsági vagy biztonsági mentési szoftverhez férnek hozzá. | – Ha a problémát egy külső gyártótól származó biztonsági szoftver okozza, akkor kapcsolatba kell lépnie a víruskereső gyártójával, hogy engedélyezze a MARS-ügynököt <br/><br/> – Ha fut egy külső gyártótól származó biztonsági mentési szoftver, várjon, amíg befejeződik, majd próbálkozzon újra a biztonsági mentéssel

## <a name="next-steps"></a>További lépések

- További információ a Windows rendszerállapotáról a Resource Manager üzembe helyezésében: a [Windows Server rendszerállapotának biztonsági mentése](backup-azure-system-state.md)
