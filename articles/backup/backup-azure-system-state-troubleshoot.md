---
title: Rendszerállapot biztonsági mentésének hibáinak megoldása Azure Backup
description: A rendszerállapot biztonsági mentésével kapcsolatos problémák elhárítása.
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
keywords: biztonsági mentés; rendszerállapot biztonsági mentése
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: dacurwin
ms.openlocfilehash: 26ba811eba1a25dacddd04814f8e0d2805360920
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018781"
---
# <a name="troubleshoot-system-state-backup"></a>Rendszerállapot biztonsági mentésének hibáinak megoldása

Ez a cikk a rendszerállapot biztonsági mentésének használata során felmerülő problémák megoldásait ismerteti.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás
Javasoljuk, hogy a rendszerállapot biztonsági mentésének megkezdése előtt végezze el az alábbi érvényesítést:

- [Győződjön meg arról, Microsoft Azure Recovery Services (MARS) ügynök naprakész](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Győződjön meg arról, hogy a MARS-ügynök és az Azure között van hálózati kapcsolat](https://aka.ms/AB-A4dp50)
- Győződjön meg arról, hogy a Microsoft Azure Recovery Services fut (a Szolgáltatás konzolon). Szükség esetén indítsa újra, és ismételje meg a műveletet.
- [Győződjön meg arról, hogy 5-10% szabad hellyel rendelkezik az ideiglenes mappa helyén](https://aka.ms/AB-AA4dwtt)
- [Ellenőrizze, hogy egy másik folyamat vagy víruskereső szoftver nem zavarja-e az Azure Backup működését](https://aka.ms/AB-AA4dwtk)
- [Az ütemezett biztonsági mentés meghiúsul, de a manuális biztonsági mentés sikeres](https://aka.ms/ScheduledBackupFailManualWorks)
- Ellenőrizze, hogy az operációs rendszer rendelkezik-e a legújabb frissítésekkel
- [Győződjön meg arról, hogy a nem támogatott, nem támogatott attribútumokkal rendelkező meghajtók és fájlok ki vannak zárva a biztonsági mentésből](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Győződjön meg arról, hogy a védett rendszer **rendszerórája** a megfelelő időzónára van állítva <br>
- [Győződjön meg arról, hogy a kiszolgáló rendelkezik a .NET-keretrendszer 4.5.2-es vagy annál frissebb verziójával](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Ha a **kiszolgálót újra regisztrálni** szeretné egy tárolóhoz, akkor: <br>
  - Győződjön meg arról, hogy az ügynök el lett távolítva a kiszolgálóról és törölve lett a portálról <br>
  - Használja ugyanazt a jelszót, amelyet kezdetben használt a kiszolgáló regisztrálásához <br>
- Offline biztonsági mentés esetén győződjön meg arról, hogy a 3.7.0 Azure PowerShell verziója a forrás-és a másolási számítógépen is telepítve van az offline biztonsági mentési művelet megkezdése előtt.
- [A biztonsági mentési ügynök Azure-beli virtuális gépen való futtatásakor megfontolandó szempontok](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>Korlátozás
- A Microsoft nem javasolja, hogy más hardverre próbáljon helyreállítást végezni a rendszerállapot-helyreállítással
- A rendszerállapot biztonsági mentése jelenleg a "helyszíni" Windows-kiszolgálókat támogatja, ez a funkció nem érhető el az Azure-beli virtuális gépekhez.

## <a name="pre-requisite"></a>Előfeltétel

Mielőtt elhárítja a rendszerállapot biztonsági mentését Azure Backupval, hajtsa végre az alábbi előfeltételek ellenőrzését.  

### <a name="verify-windows-server-backup-is-installed"></a>Windows Server biztonsági másolat telepítésének ellenőrzése

Győződjön meg arról, hogy a Windows Server biztonsági másolat telepítve van és engedélyezve van a kiszolgálón. A telepítés állapotának megtekintéséhez futtassa a következő PowerShell-parancsot:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```
Ha a kimenet a telepítési **állapotot** **elérhetőként**jeleníti meg, akkor az azt jelenti, hogy a Windows Server biztonsági másolat szolgáltatás elérhető a telepítéshez, de nincs telepítve a kiszolgálón. Ha azonban Windows Server biztonsági másolat nincs telepítve, a telepítéshez használja az alábbi módszerek egyikét.

**1. módszer: Windows Server biztonsági másolat telepítése a PowerShell használatával**

Windows Server biztonsági másolat a PowerShell használatával történő telepítéséhez futtassa az alábbi parancsot:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

**2. módszer: Windows Server biztonsági másolat telepítése a Kiszolgálókezelő használatával**

Windows Server biztonsági másolat telepítéséhez a Kiszolgálókezelő használatával hajtsa végre az alábbi lépéseket:

1. A **Server Managerben**kattintson a **szerepkörök és szolgáltatások hozzáadása**elemre. Megjelenik a **szerepkörök és szolgáltatások hozzáadása varázsló** .

    ![Irányítópult](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Válassza a **telepítés típusa** lehetőséget, majd kattintson a **tovább**gombra.

    ![Telepítés típusa](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Válasszon ki egy kiszolgálót a kiszolgáló-készletből, és kattintson a **tovább**gombra. A kiszolgálói szerepkörben hagyja meg az alapértelmezett beállítást, és kattintson a **tovább**gombra.
4. Válassza a **Windows Server biztonsági másolat** elemet a **szolgáltatások** lapon, és kattintson a **tovább**gombra.

    ![funkciókkal](./media/backup-azure-system-state-troubleshoot/features.png)

5. A **megerősítés** lapon kattintson a **telepítés** gombra a telepítési folyamat elindításához.
6. A **Results (eredmények** ) lapon megjelenik a Windows Server biztonsági másolat funkció a Windows Serverre való telepítése sikeres volt.

    ![Eredmény](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>Rendszerkötet információi engedély

Győződjön meg arról, hogy a helyi RENDSZERnek teljes hozzáférése van a **rendszerkötet információs** mappájában, amely abban a kötetben található, ahol a Windows telepítve van. Általában ez a **C:\System**. A Windows Server biztonsági másolat sikertelen lehet, ha a fenti engedélyek nincsenek megfelelően beállítva

### <a name="dependent-services"></a>Függő szolgáltatások

Győződjön meg arról, hogy az alábbi szolgáltatások fut állapotban vannak:

**Szolgáltatás neve** | **Indítási típus**
--- | ---
Távoli eljáráshívás (RPC) | Automatikus
COM+ Event System (EventSystem) | Automatikus
Rendszeresemény-értesítési szolgáltatás (SENS) | Automatikus
Kötet árnyékmásolata (VSS) | Manuális
Microsoft szoftver árnyékmásolata szolgáltató (SWPRV) | Manuális

### <a name="validate-windows-server-backup-status"></a>Windows Server biztonsági másolat állapotának ellenőrzése

Windows Server biztonsági másolat állapotának ellenőrzéséhez hajtsa végre az alábbi műveleteket:

  * Győződjön meg arról, hogy a WSB PowerShell fut

    -   Futtassa `Get-WBJob` a parancsot egy emelt szintű PowerShell-lel, és győződjön meg arról, hogy az nem adja vissza a következő hibát:

    > [!WARNING]
    > Get-WBJob: A "Get-WBJob" kifejezés nem ismerhető fel parancsmag, függvény, parancsfájl vagy működőképes program neveként. Ellenőrizze a név helyesírását, vagy ha egy elérési utat tartalmaz, ellenőrizze, hogy helyes-e az elérési út, és próbálkozzon újra.

    -   Ha ez a hiba meghiúsul, akkor telepítse újra a Windows Server biztonsági másolat szolgáltatást a kiszolgálói gépen az 1. lépés előfeltételeiben leírtak szerint.

  * Győződjön meg arról, hogy a WSB biztonsági mentése megfelelően működik, és futtassa az alábbi parancsot a rendszergazda jogú parancssorból:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Az X helyére írja be annak a kötetnek a betűjelét, amelyben a rendszerállapot biztonsági másolatát szeretné tárolni.

    - A feladatok állapotának rendszeres ellenőrzéséhez futtassa `Get-WBJob` a parancsot a rendszergazda jogú powershellből        
    - A biztonsági mentési feladatok befejezése után a parancs futtatásával `Get-WBJob -Previous 1` vizsgálja meg a feladatok végső állapotát.

Ha a feladat meghiúsul, egy WSB-problémát jelez, amely a MARS-ügynök rendszerállapot-biztonsági Mentéseinak meghibásodását eredményezné.

## <a name="common-errors"></a>Gyakori hibák

### <a name="vss-writer-timeout-error"></a>VSS-író időtúllépési hibája

| Jelenség | Ok | Megoldás:
| -- | -- | --
| -A MARS-ügynök a következő hibaüzenettel meghiúsul: "A WSB-feladatok VSS-hibákkal meghiúsultak. A hiba elhárításához keresse meg a VSS-eseménynaplókat.<br/><br/> – A következő hibanapló szerepel a VSS-alkalmazás eseménynaplójában: "A VSS-író visszautasított egy hiba 0x800423f2 eseményt, az író időtúllépése lejárt a befagyasztási és a felolvasztási események között."| A VSS-író nem tud időben befejezni a számítógép processzor-és memória-erőforrásainak hiánya miatt. <br/><br/> Egy másik biztonsági mentési szoftver már használja a VSS-írót, mert nem sikerült befejezni a biztonsági mentés eredményének pillanatkép-műveletét | Várjon, amíg a CPU/memória fel lesz szabadítva a rendszerre, vagy szakítsa meg a folyamatokat túl sok memóriát vagy PROCESSZORt, majd próbálja megismételni a műveletet. <br/><br/>  Várjon, amíg a folyamatban lévő biztonsági mentés befejeződik, majd próbálja megismételni a műveletet, amikor a gépen nem fut biztonsági másolat.


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Nincs elegendő lemezterület az árnyékmásolatok növeléséhez

| Jelenség | Megoldás:
| -- | --
| -A MARS-ügynök a következő hibaüzenettel meghiúsul: A biztonsági mentés nem sikerült, mert az árnyékmásolat-kötet nem nő, mert nincs elég szabad lemezterület a rendszerfájlokat tartalmazó köteteken <br/><br/> -A VolSnap rendszeresemény-naplókban a következő hiba/figyelmeztető napló szerepel: "Nincs elég szabad lemezterület a C köteten: Ha az árnyékmásolat-tárolót növelni szeretné a C árnyékmásolatok számára, a hiba oka, hogy a C kötet összes árnyékmásolat-másolata törlődik a következő helyen:" | – Szabadítson fel lemezterületet a kijelölt köteten az eseménynaplóban, hogy elegendő lemezterület álljon rendelkezésre az árnyékmásolatok növekedéséhez, miközben a biztonsági mentés folyamatban van. <br/><br/> – Az árnyékmásolat-terület konfigurálásával korlátozható az árnyékmásolat-használathoz felhasznált terület mennyisége. További információkért tekintse meg ezt a [cikket](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)


### <a name="efi-partition-locked"></a>EFI-partíció zárolva

| Jelenség | Megoldás:
| -- | --
| A MARS-ügynök a következő hibaüzenettel meghiúsul: "A rendszerállapot biztonsági mentése nem sikerült, mert az EFI rendszerpartíció zárolva van. Ezt az okozhatja, hogy a rendszerpartíciók a harmadik féltől származó biztonsági vagy biztonsági mentési szoftverhez férnek hozzá. | – Ha a problémát egy külső gyártótól származó biztonsági szoftver okozza, akkor kapcsolatba kell lépnie a víruskereső gyártójával, hogy engedélyezze a MARS-ügynököt <br/><br/> – Ha fut egy külső gyártótól származó biztonsági mentési szoftver, várjon, amíg befejeződik, majd próbálkozzon újra a biztonsági mentéssel


## <a name="next-steps"></a>További lépések

- További információ a Windows rendszerállapotáról a Resource Manager üzembe helyezésében: a [Windows Server rendszerállapotának biztonsági mentése](backup-azure-system-state.md)
