---
title: Ügynök- és bővítményproblémák elhárítása
description: Az Azure Biztonsági mentési hibák az ügynökkel, a bővítménysel és a lemezekkel kapcsolatos hibák tünetei, okai és megoldásai.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: a3eedb5440711c7a45a13dcd53dd489c490588fc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677407"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Az Azure biztonsági mentési hibájának elhárítása: Problémák az ügynökkel vagy a bővítménylel kapcsolatban

Ez a cikk hibaelhárítási lépéseket tartalmaz, amelyek segíthetnek az Azure Backup virtuálisgép-ügynökkel és -bővítménylel való kommunikációval kapcsolatos hibáinak megoldásában.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – A virtuálisgép-ügynök nem tud kommunikálni az Azure Backup szolgáltatással

**Hibakód**: UserErrorGuestAgentStatusUnavailable <br>
**Hibaüzenet**: A virtuálisgép-ügynök nem tud kommunikálni az Azure Backup szolgáltatással<br>

Előfordulhat, hogy az Azure virtuálisgép-ügynök le van állítva, elavult, inkonzisztens állapotban, vagy nincs telepítve. Ezek az állapotok megakadályozzák, hogy az Azure Backup szolgáltatás pillanatképeket indítson.

- **Nyissa meg az Azure Portalt, > a virtuális gép > a Beállítások > tulajdonságok ablaktábláját,** > győződjön meg arról, hogy a virtuális gép **állapota** **fut,** és az **ügynök állapota** **készen áll.** Ha a virtuálisgép-ügynök le van állítva, vagy inkonzisztens állapotban van, indítsa újra az ügynököt<br>
  - Windows virtuális gépek esetén kövesse az alábbi [lépéseket](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) a vendégügynök újraindításához.<br>
  - Linux os virtuális gépek esetén kövesse az alábbi [lépéseket](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) a vendégügynök újraindításához.
- **Nyissa meg az Azure Portalt, > virtuális gép > a beállítások > a bővítmények** győződjön meg arról, > hogy minden bővítmény **kiépítés sikeres** állapotban van. Ha nem, kövesse az alábbi [lépéseket](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) a probléma megoldásához.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – Nem lehetett kommunikálni a virtuálisgép-ügynökkel a pillanatfelvétel állapotáról

**Hibakód**: GuestAgentSnapshotTaskStatusError<br>
**Hibaüzenet:** Nem lehet kommunikálni a virtuális gép ügynökével a pillanatkép állapotának állapotáról <br>

Miután regisztrálta és ütemezi az Azure Backup szolgáltatás virtuális gépét, a biztonsági mentés elindítja a feladatot a virtuális gép biztonsági mentési bővítményével való kommunikációval, hogy egy időpontban pillanatképet készítsen. Az alábbi feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha a pillanatkép nem aktiválódik, biztonsági mentési hiba léphet fel. Hajtsa végre az alábbi hibaelhárítási lépéseket a felsorolt sorrendben, majd próbálkozzon újra a művelettel:  

**1. ok: [Az ügynök telepítve van a virtuális gép, de nem válaszol (windows virtuális gépek)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**2. ok: [A virtuális gépbe telepített ügynök elavult (Linux os virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**3. ok: [A pillanatkép állapota nem olvasható be, vagy nem lehet pillanatképet venni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**4. ok: [A VM-ügynök konfigurációs beállításai nincsenek beállítva (Linux os virtuális gépekesetén)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed – A virtuális gép sikertelen kiépítési állapotban van

**Hibakód**: UserErrorVmProvisioningStateFailed<br>
**Hibaüzenet:** A virtuális gép sikertelen kiépítési állapotban van<br>

Ez a hiba akkor fordul elő, ha a bővítmény egyik hibája a virtuális gép kiépítési sikertelen állapotba kerül.<br>**Nyissa meg az Azure Portal> a virtuális gép > a Beállítások > a bővítmények >-bővítmények állapotát,** és ellenőrizze, hogy az összes bővítmény kiépítés sikeres állapotban **van-e.** További információ: [Kiépítési állapotok.](https://docs.microsoft.com/azure/virtual-machines/windows/states-lifecycle#provisioning-states)

- Ha a VMSnapshot-bővítmény sikertelen állapotban van, kattintson a jobb gombbal a sikertelen bővítményre, és távolítsa el. Igény szerinti biztonsági mentés aktiválása. Ez a művelet újratelepíti a bővítményeket, és futtatja a biztonsági mentési feladatot.  <br>
- Ha bármely más bővítmény hibás állapotban van, akkor zavarhatja a biztonsági mentést. Győződjön meg arról, hogy ezek a bővítményproblémák megoldódtak, és próbálkozzon újra a biztonsági mentési művelettel.
- Ha a virtuális gép létesítési állapota frissítési állapotban van, zavarhatja a biztonsági mentést. Győződjön meg arról, hogy kifogástalan állapotú, és próbálja meg újra a biztonsági mentési műveletet.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – Elérte a visszaállítási pont gyűjteményének korlátját

**Hibakód**: UserErrorRpCollectionLimitReached <br>
**Hibaüzenet:** Elérte a Visszaállítási pont gyűjtemény maximális korlátját. <br>

- Ez a probléma akkor fordulhat elő, ha a helyreállítási pont erőforráscsoport zárolja a helyreállítási pontok automatikus karbantartását.
- Ez a probléma akkor is előfordulhat, ha naponta több biztonsági mentés aktiválódik. Jelenleg azt javasoljuk, csak egy biztonsági mentés naponta, mivel az azonnali visszaállítási pontok megmaradnak 1-5 napig a konfigurált pillanatkép-megőrzési és csak 18 azonnali RPs társítható egy virtuális gép egy adott időpontban. <br>
- A visszaállítási pontok száma a visszaállítási pont gyűjtemények és erőforráscsoportok egy virtuális gép nem haladhatja meg a 18. Új visszaállítási pont létrehozásához törölje a meglévő visszaállítási pontokat.

Ajánlott művelet:<br>
A probléma megoldásához távolítsa el a zárolást a virtuális gép erőforráscsoportjában, és próbálja meg újra a műveletet a karbantartás elindításához.
> [!NOTE]
> A biztonsági mentési szolgáltatás a visszaállítási pontgyűjtemény tárolására a virtuális gép erőforráscsoportjánál külön erőforráscsoportot hoz létre. Az ügyfeleknek azt tanácsoljuk, hogy ne zárják le a Biztonsági másolat szolgáltatás által használatra létrehozott erőforráscsoportot. A Backup szolgáltatás által létrehozott erőforráscsoport elnevezési formátuma: AzureBackupRG_`<Geo>`_`<number>` Pl. AzureBackupRG_northeurope_1

**1. lépés: [Zárolás eltávolítása a visszaállítási pont erőforráscsoportból](#remove_lock_from_the_recovery_point_resource_group)** <br>
**2. lépés: [A visszaállítási pont gyűjteményének karbantartása](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - A biztonsági másolat nem rendelkezik a titkosított virtuális gépek biztonsági mentéséhez szükséges hozzáférésű kulcstartóhoz szükséges engedélyekkel

**Hibakód**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Hibaüzenet:** A biztonsági másolat nem rendelkezik a titkosított virtuális gépek biztonsági mentéséhez szükséges hozzáférés-előtűné a key vaulthoz szükséges engedélyekkel. <br>

Ahhoz, hogy egy biztonsági mentési művelet sikeres legyen a titkosított virtuális gépeken, rendelkeznie kell a key vault eléréséhez szükséges engedélyekkel. Az engedélyek az [Azure Portalon](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) vagy a [PowerShellen](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection)keresztül állíthatók be.

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork – Nem sikerült elkészíteni a pillanatképet, mert nincs hálózati kapcsolat a virtuális gépen

**Hibakód**: ExtensionSnapshotFailedNoNetwork<br>
**Hibaüzenet**: A pillanatkép-művelet nem sikerült, mert nincs hálózati kapcsolat a virtuális gépen<br>

Miután regisztrálta és ütemezi az Azure Backup szolgáltatás virtuális gépét, a biztonsági mentés elindítja a feladatot a virtuális gép biztonsági mentési bővítményével való kommunikációval, hogy egy időpontban pillanatképet készítsen. Az alábbi feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha a pillanatkép nem aktiválódik, biztonsági mentési hiba léphet fel. Hajtsa végre a következő hibaelhárítási lépést, majd próbálkozzon újra a művelettel:

**[A pillanatkép állapota nem olvasható be, vagy nem lehet pillanatképet venni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks - A VMSnapshot bővítmény művelete nem sikerült

**Hibakód**: ExtensionOperationFailedForManagedDisks <br>
**Hibaüzenet**: A VMSnapshot bővítmény művelete nem sikerült<br>

Miután regisztrálta és ütemezi az Azure Backup szolgáltatás virtuális gépét, a biztonsági mentés elindítja a feladatot a virtuális gép biztonsági mentési bővítményével való kommunikációval, hogy egy időpontban pillanatképet készítsen. Az alábbi feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha a pillanatkép nem aktiválódik, biztonsági mentési hiba léphet fel. Hajtsa végre az alábbi hibaelhárítási lépéseket a felsorolt sorrendben, majd próbálkozzon újra a művelettel:  
**1. ok: [A pillanatkép állapota nem olvasható be, vagy nem lehet pillanatképet venni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**2. ok: [Az ügynök telepítve van a virtuális gép, de nem válaszol (windows virtuális gépek)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**3. ok: [A virtuális gépbe telepített ügynök elavult (Linux os virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 – A biztonsági mentés sikertelen, belső hiba

**Hibakód**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Hibaüzenet**: A biztonsági mentés belső hibával nem sikerült - Próbálkozzon újra a művelettel néhány perc múlva <br>

Miután regisztrálta és ütemezi az Azure Backup szolgáltatás virtuális gépét, a biztonsági mentés elindítja a feladatot a virtuális gép biztonsági mentési bővítményével való kommunikációval, hogy egy időpontban pillanatképet készítsen. Az alábbi feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha a pillanatkép nem aktiválódik, biztonsági mentési hiba léphet fel. Hajtsa végre az alábbi hibaelhárítási lépéseket a felsorolt sorrendben, majd próbálkozzon újra a művelettel:  
**1. ok: [A virtuális gépbe telepített ügynök, de nem válaszol (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. ok: [A virtuális gépbe telepített ügynök elavult (Linux os virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [A pillanatkép állapota nem olvasható be, vagy nem lehet pillanatképet venni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**4. ok: [A biztonsági másolat szolgáltatásnem rendelkezik engedéllyel a régi visszaállítási pontok törléséhez egy erőforráscsoport zárolása miatt](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize – A beállított lemezméret(eke)t az Azure Backup jelenleg nem támogatja

**Hibakód**: UserErrorUnsupportedDiskSize <br>
**Hibaüzenet:** A beállított lemezméret(ek) jelenleg nem támogatja az Azure Backup. <br>

A biztonsági mentési művelet sikertelen lehet, ha 32 TB-nál nagyobb lemezméretű virtuális gépről készít biztonsági mentést. Emellett a 4 TB-nál nagyobb méretű titkosított lemezek biztonsági mentése jelenleg nem támogatott. A lemez(ek) felosztásával győződjön meg arról, hogy a lemez méret(ek) kisebb vagy egyenlő a támogatott korláttal.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress – Nem lehet elindítani a biztonsági mentést, mert egy másik biztonsági mentési művelet van folyamatban

**Hibakód**: UserErrorBackupOperationInProgress <br>
**Hibaüzenet**: Nem lehet elindítani a biztonsági mentést, mert egy másik biztonsági mentési művelet van folyamatban<br>

A legutóbbi biztonsági mentési feladat nem sikerült, mert van egy meglévő biztonsági mentési feladat folyamatban van. Nem indíthat új biztonsági mentési feladatot, amíg az aktuális feladat be nem fejeződik. Győződjön meg arról, hogy a jelenleg folyamatban lévő biztonsági mentési művelet befejeződött, mielőtt újabb biztonsági mentési műveleteket indítana vagy ütemezne. A biztonsági mentési feladatok állapotának ellenőrzéséhez tegye a következő lépéseket:

1. Jelentkezzen be az Azure Portalra, és kattintson **a Minden szolgáltatás**elemre. Írja be a Recovery Services kifejezést, és kattintson a **Recovery Services-tárolók** elemre. A Recovery Services-tárolók listája megjelenik.
2. A helyreállítási szolgáltatások tárolóinak listájából válassza ki azt a tárolót, amelyben a biztonsági másolat konfigurálva van.
3. A tároló irányítópultjának menüjében kattintson a **Biztonsági mentési feladatok** parancsra, és az összes biztonsági mentési feladatot megjeleníti.
   - Ha egy biztonsági mentési feladat folyamatban van, várja meg, amíg befejeződik, vagy megszakítja a biztonsági mentési feladatot.
     - A biztonsági mentési feladat megszakításához kattintson a jobb gombbal a biztonsági mentési feladatra, és kattintson a **Mégse gombra,** vagy használja a [PowerShellt.](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
   - Ha újrakonfigurálta a biztonsági mentést egy másik tárolóban, majd győződjön meg arról, hogy nincsenek biztonsági mentési feladatok a régi tárolóban. Ha létezik, akkor szakítsa meg a biztonsági mentési feladatot.
     - A biztonsági mentési feladat megszakításához kattintson a jobb gombbal a biztonsági mentési feladatra, majd a **Mégse** elemre, vagy használja a [PowerShellt](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Próbálja meg újra a biztonsági mentési műveletet.

Ha az ütemezett biztonsági mentési művelet hosszabb időt vesz igénybe, ütközik a következő biztonsági másolat konfigurációjával, tekintse át az [ajánlott eljárások,](backup-azure-vms-introduction.md#best-practices)a [Biztonsági másolat teljesítménye](backup-azure-vms-introduction.md#backup-performance)és a Visszaállítás című [részt.](backup-azure-vms-introduction.md#backup-and-restore-considerations)

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - A biztonsági mentés hiba miatt nem sikerült. További részletek: Feladathibaüzenet részletei

**Hibakód**: UserErrorCrpReportedUserError <br>
**Hibaüzenet**: A biztonsági mentés hiba miatt nem sikerült. További információt a Feladathibaüzenet részletei.

Ezt a hibát az IaaS virtuális gép jelenti. A probléma kiváltó okának azonosításához nyissa meg a Recovery Services-tároló beállításait. A **Figyelés** csoportban válassza **a Biztonsági mentési feladatok lehetőséget** az állapot szűréséhez és megtekintéséhez. Kattintson a **Hibák gombra** az alapul szolgáló hibaüzenet részleteinek áttekintéséhez. További műveleteket tehet a hibarészletek oldalon található ajánlásoknak megfelelően.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent – A biztonsági mentés nem sikerült: Ezt a virtuális gépet nem (aktívan) védi az Azure Backup

**Hibakód**: UserErrorBcmDatasourceNotPresent <br>
**Hibaüzenet**: Biztonsági mentés nem sikerült: Ez a virtuális gép nem (aktív) által védett Azure Backup.

Ellenőrizze, hogy az adott virtuális gép aktívan (nem szüneteltetési állapotban) az Azure Backup által védett. A probléma megoldásához győződjön meg arról, hogy a virtuális gép aktív, majd próbálkozzon újra a művelettel.

## <a name="causes-and-solutions"></a>Okok és megoldások

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Az ügynök telepítve van a virtuális gépben, de nem válaszol (Windows virtuális gépek esetén)

#### <a name="solution"></a>Megoldás

Lehet, hogy a virtuális gép ügynöke sérült, vagy a szolgáltatás leállt. A virtuálisgép-ügynök újratelepítése segít a legújabb verzió bekéselésén. Ez is segít újraindítani a kommunikációt a szolgáltatással.

1. Határozza meg, hogy a Windows Azure vendégügynök szolgáltatás fut-e a virtuális gép szolgáltatások (services.msc). Próbálja meg újraindítani a Windows Azure vendégügynök szolgáltatást, és kezdeményezze a biztonsági mentést.
2. Ha a Windows Azure vendégügynök szolgáltatás a szolgáltatásokban nem látható, a Vezérlőpulton a **Programok és szolgáltatások** lapon állapítsa meg, hogy telepítve van-e a Windows Azure vendégügynök szolgáltatása.
3. Ha a Windows Azure vendégügynök e programok **és szolgáltatások,** távolítsa el a Windows Azure vendégügynök.
4. Töltse le és telepítse [az MSI ügynök legújabb verzióját.](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) A telepítés befejezéséhez rendszergazdai jogosultságokkal kell rendelkeznie.
5. Ellenőrizze, hogy a Windows Azure vendégügynök-szolgáltatások megjelennek-e a szolgáltatásokban.
6. Igény szerinti biztonsági mentés futtatása:
   - A portálon válassza a **Biztonsági mentés most**lehetőséget.

Ellenőrizze azt is, hogy [a Microsoft .NET 4.5 telepítve van-e](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) a virtuális gépben. A virtuálisgép-ügynök nek a szolgáltatással való kommunikációhoz .

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>A virtuális gépbe telepített ügynök elavult (Linux os virtuális gépek esetén)

#### <a name="solution"></a>Megoldás

A legtöbb ügynökkel vagy a kiterjesztetttel kapcsolatos linuxos virtuális gépek hibák okozta problémák, amelyek befolyásolják az elavult virtuálisgép-ügynök. A probléma elhárításához kövesse az alábbi általános irányelveket:

1. Kövesse a [Linux virtuálisgép-ügynök frissítésére](../virtual-machines/linux/update-agent.md)vonatkozó utasításokat.

   > [!NOTE]
   > *Javasoljuk,* hogy az ügynök frissítése csak egy terjesztési tárház. Nem javasoljuk, hogy az ügynökkódját közvetlenül a GitHubról töltse le és frissítse. Ha a legújabb ügynök a terjesztés nem érhető el, forduljon a terjesztési támogatási szolgálathoz a telepítésével kapcsolatos utasításokért. A legújabb ügynök ellenőrzéséhez keresse fel a [Windows Azure Linux-ügynök](https://github.com/Azure/WALinuxAgent/releases) lapját a GitHub-tárházban.

2. Győződjön meg arról, hogy az Azure-ügynök fut a virtuális gép en a következő parancs futtatásával:`ps -e`

   Ha a folyamat nem fut, indítsa újra a következő parancsokkal:

   - Ubuntu esetén:`service walinuxagent start`
   - Egyéb disztribúciók esetében:`service waagent start`

3. [Konfigurálja az automatikus újraindítási ügynököt](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Futtasson le egy új tesztbiztonsági mentést. Ha a hiba továbbra is fennáll, gyűjtse össze a következő naplókat a virtuális gépről:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Ha részletes naplózásra van szüksége a waagent hez, kövesse az alábbi lépéseket:

1. Az /etc/waagent.conf fájlban keresse meg a következő sort: **Részletes naplózás engedélyezése (y|n)**
2. Módosítsa a **Logs.Verbose** értéket *n-ről* *y-ra.*
3. Mentse a módosítást, majd indítsa újra a waagent-t az ebben a szakaszban ismertetett lépések végrehajtásával.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>A VM-ügynök konfigurációs beállításai nincsenek beállítva (Linuxos virtuális gépekesetén)

A konfigurációs fájl (/etc/waagent.conf) szabályozza a waagent műveleteket. Konfigurációs fájl beállításai **Extensions.Enable** és **Provisioning.Agent** kell állítani **y** biztonsági mentés működéséhez.
A virtuálisgép-ügynök konfigurációs fájlbeállításainak teljes listáját lásd:<https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>A pillanatkép állapota nem olvasható be, vagy nem lehet pillanatképet venni

A virtuális gép biztonsági mentése egy pillanatkép-parancs kiadására támaszkodik az alapul szolgáló tárfiókszámára. A biztonsági mentés sikertelen lehet, mert nem rendelkezik hozzáféréssel a tárfiókhoz, vagy mert a pillanatkép-feladat végrehajtása késik.

#### <a name="solution"></a>Megoldás

A következő feltételek a pillanatkép-feladat sikertelensítéséhez fordulhatnak elő:

| Ok | Megoldás |
| --- | --- |
| A virtuális gép állapota helytelenül van jelentve, mert a virtuális gép le van állítva a Távoli asztali protokoll (RDP) protokollban. | Ha leállítja a virtuális gép rdp, ellenőrizze a portálon, hogy a virtuális gép állapota helyes-e. Ha ez nem helyes, állítsa le a virtuális gép a portálon a **leállítási** lehetőség a virtuális gép irányítópultján. |
| A virtuális gép nem tudja levenni az állomás vagy a háló címét a DHCP-től. | Az IaaS virtuális gép biztonsági mentésének működéséhez engedélyezni kell a DHCP-t a vendégen belül. Ha a virtuális gép nem tudja leadni az állomás vagy a háló címét a 245-ös DHCP-válaszból, nem tud letölteni vagy futtatni egyetlen bővítményt sem. Ha statikus privát IP-címre van szüksége, konfigurálja azt az **Azure Portalon** vagy a **PowerShellen** keresztül, és győződjön meg arról, hogy a virtuális gépen belüli DHCP-beállítás engedélyezve van. [További információ](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) a statikus IP-cím PowerShell használatával való beállításáról.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Zárolás eltávolítása a helyreállításipont erőforráscsoportból

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg **a Minden erőforrás lehetőséget,** válassza ki a`<Geo>``<number>`visszaállítási pont gyűjtési erőforráscsoportot a következő formátumban, AzureBackupRG_ _ .
3. A **Beállítások csoportban** válassza a **Zárolások** lehetőséget a zárolások megjelenítéséhez.
4. A zárolás eltávolításához jelölje ki a három pontot, és kattintson a **Törlés gombra.**

    ![Zárolás törlése](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a>Visszaállítási pont gyűjteményének karbantartása

A zárolás eltávolítása után a visszaállítási pontokat meg kell tisztítani.

Ha törli a virtuális gép erőforráscsoportját, vagy magát a virtuális gépet, a felügyelt lemezek azonnali visszaállítási pillanatképei aktívak maradnak, és a megőrzési készletnek megfelelően lejárnak. A visszaállítási pontgyűjteményben tárolt azonnali visszaállítási pillanatképek törléséhez (ha már nincs rájuk szüksége), tisztítsa meg a visszaállítási pontgyűjteményt az alábbi lépéseknek megfelelően.

A visszaállítási pontok megtisztításához kövesse a következő módszerek valamelyikét:<br>

- [Visszaállítási pontgyűjtemény karbantartása igény szerinti biztonsági mentés futtatásával](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Visszaállítási pontgyűjtemény karbantartása az Azure Portalról](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Visszaállítási pontgyűjtemény karbantartása igény szerinti biztonsági mentés futtatásával

A zárolás eltávolítása után indítsa el az igény szerinti biztonsági mentést. Ez a művelet biztosítja, hogy a visszaállítási pontok automatikusan törlődjenek. Az igény szerinti művelet első sikertelensére számíthat; azonban ez biztosítja az automatikus karbantartást a visszaállítási pontok manuális törlése helyett. A karbantartás után a következő ütemezett biztonsági mentés sikeresnek kell lennie.

> [!NOTE]
> Az automatikus karbantartás az igény szerinti biztonsági mentés néhány órás aktiválása után történik. Ha az ütemezett biztonsági mentés továbbra is sikertelen, próbálja meg manuálisan törölni a visszaállítási pontgyűjteményt az [itt](#clean-up-restore-point-collection-from-azure-portal)felsorolt lépésekkel.

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Visszaállítási pontgyűjtemény karbantartása az Azure Portalról <br>

Ha manuálisan szeretné törölni a visszaállítási pontok gyűjteményét, amely az erőforráscsoport zárolása miatt nincs törölve, próbálkozzon az alábbi lépésekkel:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A **Központ** menüben kattintson a **Minden erőforrás parancsra,** és válassza ki a következő formátumú erőforráscsoportot, AzureBackupRG_`<Geo>`_`<number>` , ahol a virtuális gép található.

    ![Zárolás törlése](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Kattintson az Erőforrás csoport elemre, és megjelenik az **Áttekintő** ablaktábla.
4. Válassza **a Rejtett típusok megjelenítése** lehetőséget az összes rejtett erőforrás megjelenítéséhez. Jelölje ki a visszaállítási pontgyűjteményeket a következő formátumban, AzureBackupRG_`<VMName>`_`<number>`.

    ![Zárolás törlése](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. A **visszaállítási** pontgyűjtemény tisztításához kattintson a Törlés gombra.
6. Próbálkozzon újra a biztonsági mentési művelettel.

> [!NOTE]
 >Ha az erőforrás (RP Gyűjtemény) nagy számú visszaállítási ponttal rendelkezik, akkor a portálról való törlésük időtúltöltést eredményezhet, és sikertelen lehet. Ez egy ismert CRP-probléma, ahol az összes visszaállítási pont nem törlődik az előírt időben, és a működés időtúljár; azonban a törlési művelet általában 2 vagy 3 újrapróbálkozás után sikeres.
