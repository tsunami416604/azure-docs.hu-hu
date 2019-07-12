---
title: Az Azure Backup szolgáltatással a rendszerállapot biztonsági mentésének hibaelhárítása
description: Hibaelhárítás a rendszerállapot biztonsági mentését.
services: backup
author: srinathvasireddy
manager: sivan
keywords: biztonsági mentés a; biztonsági mentési rendszer állapota
ms.service: backup
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: srinathv
ms.openlocfilehash: 87b5fff58ecf9e89bc94f31a0bc3a591c146c88f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705002"
---
# <a name="troubleshoot-system-state-backup"></a>Rendszerállapot biztonsági mentésének hibaelhárítása

Ez a cikk ismerteti a rendszerállapot biztonsági mentését használata során előforduló problémák megoldásait.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás
Azt javasoljuk, hogy hajtsa végre az alábbi érvényesítési, mielőtt használatba hibáinak elhárítása a rendszerállapot biztonsági mentése:

- [Győződjön meg arról a Microsoft Azure Recovery Services (MARS) ügynöke naprakész](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Győződjön meg arról, hogy a MARS-ügynök és az Azure között van hálózati kapcsolat](https://aka.ms/AB-A4dp50)
- Győződjön meg arról, hogy a Microsoft Azure Recovery Services fut (a Szolgáltatás konzolon). Ha szükséges, indítsa újra a gépet, és próbálja meg újra a műveletet
- [Győződjön meg arról, hogy 5-10% szabad hellyel rendelkezik az ideiglenes mappa helyén](https://aka.ms/AB-AA4dwtt)
- [Ellenőrizze, hogy egy másik folyamat vagy víruskereső szoftver nem zavarja-e az Azure Backup működését](https://aka.ms/AB-AA4dwtk)
- [Az ütemezett biztonsági mentés meghiúsul, de a manuális biztonsági mentés sikeres](https://aka.ms/ScheduledBackupFailManualWorks)
- Ellenőrizze, hogy az operációs rendszer rendelkezik-e a legújabb frissítésekkel
- [Győződjön meg, hogy a biztonsági mentés nem támogatott meghajtókkal és nem támogatott attribútumokkal rendelkező fájlok kizárva](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Győződjön meg arról, hogy a védett rendszer **rendszerórája** a megfelelő időzónára van állítva <br>
- [Győződjön meg arról, hogy a kiszolgáló rendelkezik a .NET-keretrendszer 4.5.2-es vagy annál frissebb verziójával](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Ha a **kiszolgálót újra regisztrálni** szeretné egy tárolóhoz, akkor: <br>
  - Győződjön meg arról, hogy az ügynök el lett távolítva a kiszolgálóról és törölve lett a portálról <br>
  - Használja ugyanazt a jelszót, amelyet kezdetben használt a kiszolgáló regisztrálásához <br>
- Offline biztonsági mentés esetén ellenőrizze, hogy az Azure PowerShell verziója 3.7.0 telepítve van a forrás- és másolási számítógépen offline biztonsági mentés megkezdése előtt
- [Figyelembe veszi, amikor biztonsági mentést ügynököt egy Azure virtuális gépen fut.](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>Korlátozás
- A Microsoft nem javasolja, hogy más hardverre próbáljon helyreállítást végezni a rendszerállapot-helyreállítással
- A rendszerállapot biztonsági mentése jelenleg támogatja a "helyszíni" Windows-kiszolgálók, ez a funkció nem érhető el az Azure virtuális gépek.

## <a name="pre-requisite"></a>Előfeltétel

Azt a rendszerállapot biztonsági mentése az Azure Backup hibaelhárítása, előtt győződjön meg arról, hogy végrehajtása az alábbi előfeltételek ellenőrzése.  

### <a name="verify-windows-server-backup-is-installed"></a>Ellenőrizze, hogy telepítve van a Windows Server biztonsági másolat

Győződjön meg arról, a Windows Server biztonsági másolat telepítve és engedélyezve van a kiszolgálón. A telepítés állapotának ellenőrzéséhez futtassa az alábbi PowerShell-parancsot:

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
Ha a kimenet megjeleníti a **telepítési állapot** , **elérhető**, azt jelenti, hogy a Windows Server biztonsági másolat szolgáltatás elérhető, a telepítéshez, de nincs telepítve a kiszolgálón. Azonban ha a Windows Server biztonsági másolat nincs telepítve, majd valamelyikét használja az alábbi módszerek a telepítéshez.

**1. módszer: Telepítse a Windows Server biztonsági másolat a PowerShell használatával**

Telepítse a PowerShell használatával a Windows Server biztonsági másolat, futtassa az alábbi parancsot:

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**2. módszer: Telepítse a Windows Server biztonsági másolat a Kiszolgálókezelő használatával**

Telepítése a Kiszolgálókezelővel a Windows Server biztonsági másolat, hajtsa végre az alábbi:

1. Az a **Server Manager** kattintson **szerepkörök és szolgáltatások hozzáadása**. A **felvétele szerepkörök és szolgáltatások varázsló** jelenik meg.

    ![Irányítópult](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Válassza ki **telepítési típus** kattintson **tovább**.

    ![Telepítés típusa](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Kiszolgáló kijelölése a kiszolgálókészletből, és kattintson a **tovább**. A kiszolgálói szerepkör, hagyja meg az alapértelmezett, és kattintson a **tovább**.
4. Válassza ki **Windows Server biztonsági másolat** a **funkciók** fülre, és **tovább**.

    ![funkciókkal](./media/backup-azure-system-state-troubleshoot/features.png)

5. Az a **megerősítő** lapra, majd **telepítése** a telepítési folyamat elindításához.
6. Az a **eredmények** lapon ekkor megjelenik a szolgáltatás sikeresen telepítve van a Windows Server a Windows Server biztonsági másolat.

    ![Eredmény](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>A System Volume információk engedély

Győződjön meg arról, hogy a helyi rendszer teljes körű vezérléssel rendelkezik **rendszerkötet-információkat** a kötetet, amelyen telepítve van a windows mappában található. Általában ez a **C:\System Volume Information**. A Windows Server biztonsági másolat meghiúsulhat, ha a fenti engedélyek nem megfelelően vannak beállítva

### <a name="dependent-services"></a>Függő szolgáltatások

Győződjön meg, hogy a szolgáltatás alatt futó állapotban van:

**Szolgáltatás neve** | **Indítási típus**
--- | ---
Távoli eljáráshívási | Automatikus
A COM + esemény System(EventSystem) | Automatikus
Rendszer Event Notification Service(SENS) | Automatikus
Kötet árnyékmásolata Copy(VSS) | Kézi
Microsoft-szoftverek árnyékmásolat másolási Provider(SWPRV) | Kézi

### <a name="validate-windows-server-backup-status"></a>A Windows Server biztonsági másolat állapotának ellenőrzése

A Windows Server biztonsági másolat állapotának ellenőrzéséhez hajtsa végre az alábbi:

  * Ellenőrizze, fut-e a WSB PowerShell

    -   Futtatás `Get-WBJob` egy emelt szintű PowerShell-és ellenőrizze, hogy azt nem ad vissza a következő hibával:

    > [!WARNING]
    > Get-WBJob: A "Get-WBJob" kifejezés nem ismerhető fel egy parancsmag, a függvény, a parancsfájl vagy a működtethető program nevét. Ellenőrizze a helyesírást, a neve, vagy ha egy elérési út része, ellenőrizze, hogy az elérési út helyes, és próbálkozzon újra.

    -   Ha ez a hiba miatt sikertelen telepítse újra a Windows Server biztonsági másolat szolgáltatás a kiszolgáló számítógépen az 1. lépés előfeltételek között ismertetett módon.

  * Győződjön meg, hogy a WSB backup működik megfelelően, futtassa az alábbi parancsot rendszergazda jogú parancssorból:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >X cserélje le, ahol a rendszer állapotáról tárolni szeretné a kötet meghajtóbetűjellel lemezképet készíteni.

    - Rendszeres időközönként a feladat állapotának ellenőrzéséhez futtassa `Get-WBJob` parancsot rendszergazda jogú PowerShell        
    - Biztonsági mentési feladat befejezése után ellenőrizze a feladat végső állapotúvá futtatásával `Get-WBJob -Previous 1` parancs

A feladat sikertelen lesz, azt jelzi a WSB probléma, amely a MARS-ügynök rendszerállapot biztonsági mentéseit hibát eredményez.

## <a name="common-errors"></a>Gyakori hibák

### <a name="vss-writer-timeout-error"></a>VSS-író időtúllépési hiba

| Jelenség | Ok | Megoldás:
| -- | -- | --
| -A MARS ügynök hibaüzenettel meghiúsul: "A WSB-feladat nem sikerült a VSS-hibák. Ellenőrizze a hiba megoldásához VSS-eseménynaplók"<br/><br/> -Hiba történt a következő log megtalálható a VSS alkalmazás eseménynaplóit: "VSS-író elutasított egy eseményt 0x800423f2 hibával, az író túllépte az időkorlátot a lefagyását tapasztaló és felengedés események között."| VSS-író nem tudja a gépen a CPU és memória-erőforrások hiánya miatt időben végrehajtani <br/><br/> A VSS-író már használja egy másik biztonsági mentési szoftver, ezért pillanatkép-készítési művelet nem sikerült végrehajtani a biztonsági mentés | Várjon, amíg a rendszer szabadulnak fel vagy megszakíthatja a folyamat túl sok memóriát és CPU véve, és próbálja megismételni a műveletet CPU/memória <br/><br/>  Várjon, amíg a folyamatban lévő biztonsági mentés befejeződését, ismételje meg a műveletet később, ha a gépen futó nem készült biztonsági másolat


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Nincs elegendő szabad lemezterület árnyékmásolatok növekedésért

| Jelenség | Megoldás:
| -- | --
| -A MARS ügynök hibaüzenettel meghiúsul: Biztonsági mentés nem sikerült, mert az árnyékmásolat-kötet nem tudta növelni elégtelen lemezterület miatt a rendszer fájljait tartalmazó köteteken <br/><br/> – A következő hiba/figyelmeztető log megtalálható a rendszer eseménynaplóit a volsnap: "Nem volt elegendő szabad lemezterület a köteten C: nő, ez a hiba miatt: C: árnyékmásolatainak az árnyékmásolat-tároló kötet C: feltölthetné törlése folyamatban van az összes árnyékmásolatot" | -Szabadítson fel lemezterületet a kijelölt kötet az eseménynaplóban, hogy elegendő terület az árnyékmásolatok nő, míg a biztonsági mentés folyamatban van <br/><br/> – Való konfigurálása során árnyékmásolat másolási terület azt korlátozhatja az árnyékmásolat számára felhasznált lemezterület mennyiségét, további információ: Ez [cikk](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)


### <a name="efi-partition-locked"></a>EFI-partíciót zárolva van

| Jelenség | Megoldás:
| -- | --
| A MARS-ügynök hibaüzenettel meghiúsul: "A rendszerállapot biztonsági akár sikertelen volt, mert az EFI-rendszerpartíció zárolva van. Ez rendszerpartíció elérését egy külső Security okozhatják, vagy készítsen biztonsági másolatot a szoftver" | – Ha a probléma miatt egy külső biztonsági szoftverben, majd szeretné a kártevőirtó vírus eladójától, így lehetővé teszik a MARS-ügynök <br/><br/> – Ha egy külső biztonsági mentési szoftver fut, majd várjon, amíg befejeződnek, és próbálkozzon újra a biztonsági mentése


## <a name="next-steps"></a>További lépések

- A Resource Manager-alapú Windows rendszer állapotával kapcsolatos további információkért lásd: [Windows Server rendszerállapotának biztonsági mentése](backup-azure-system-state.md)
