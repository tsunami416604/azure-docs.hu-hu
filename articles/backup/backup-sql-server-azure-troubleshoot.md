---
title: Sql Server adatbázis biztonsági mentése – problémamegoldás
description: Az Azure Backup szolgáltatással az Azure-beli virtuális gépeken futó SQL Server-adatbázisok biztonsági mentésével kapcsolatos hibák elhárítása.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 8d49adb0ab741903ccb2989cfeb4ceaef2e8a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408616"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Sql Server adatbázis-biztonsági mentésének – problémamegoldás az Azure Backup használatával

Ez a cikk az Azure virtuális gépeken futó SQL Server-adatbázisok hibaelhárítási információit tartalmazza.

A biztonsági mentési folyamatról és a korlátozásokról az [SQL Server biztonsági mentése az Azure virtuális gépeken](sql-support-matrix.md#feature-consideration-and-limitations)című témakörben talál további információt.

## <a name="sql-server-permissions"></a>SQL Server-engedélyek

Az SQL Server-adatbázis virtuális gépen történő védelmének konfigurálásához telepítenie kell az **AzureBackupWindowsWorkload** bővítményt a virtuális gépen. Ha a **UserErrorSQLNoSysadminMembership**hibaüzenetet kapja, az azt jelenti, hogy az SQL Server-példány nem rendelkezik a szükséges biztonsági mentési engedélyekkel. A hiba kijavításához kövesse a [Virtuálisgép-engedélyek beállítása](backup-azure-sql-database.md#set-vm-permissions)című részben leírt lépéseket.

## <a name="troubleshoot-discover-and-configure-issues"></a>Problémák felderítése és konfigurálása

A Recovery Services-tároló létrehozása és konfigurálása után az adatbázisok felderítése és a biztonsági mentés konfigurálása két lépésből áll.<br>

![sql](./media/backup-azure-sql-database/sql.png)

A biztonsági mentési konfiguráció során, ha az SQL virtuális gép és példányai nem láthatók a **felderítési db-ek** a virtuális gépek és a **Biztonsági másolat konfigurálása** (lásd a fenti lemezkép) győződjön meg arról, hogy:

### <a name="step-1-discovery-dbs-in-vms"></a>1. lépés: Felderítési db-k a virtuális gépeken

- Ha a virtuális gép nem szerepel a felderített virtuális gép listában, és nem regisztrált az SQL biztonsági mentés egy másik tárolóban, majd kövesse a [Discovery SQL Server biztonsági mentési](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases) lépéseit.

### <a name="step-2-configure-backup"></a>2. lépés: A biztonsági mentés konfigurálása

- Ha a tároló, amelyben az SQL virtuális gép regisztrálva van ugyanabban a tárolóban az adatbázisok védelmére használt, majd kövesse a [Biztonsági mentés konfigurálása](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup) lépéseket.

Ha az SQL virtuális gép regisztrálva kell lennie az új tárolóban, majd azt nem kell regisztrálni a régi tárolóból.  Az SQL virtuális gép trezorból való regisztrációjának megszüntetéséhez az összes védett adatforrást le kell állítani, és ezután törölheti a biztonsági másolatot. A biztonsági másolatok adatainak törlése romboló művelet.  Miután áttekintette, és minden óvintézkedést megtett az SQL virtuális gép regisztrációjának megszüntetése érdekében, majd regisztrálja ugyanazt a virtuális gépet egy új tárolóval, és próbálkozzon újra a biztonsági mentési művelettel.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Biztonsági mentési és helyreállítási problémák elhárítása  

Időnként vagy véletlenszerű hibák fordulhatnak elő a biztonsági mentési és visszaállítási műveletekben, vagy ezek a műveletek elakadhatnak. Ennek oka lehet a víruskereső programok a virtuális gép. Ajánlott eljárásként a következő lépéseket javasoljuk:

1. Zárja ki a következő mappákat a víruskereső késedelméből:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.Edp.AzureBackupWindowsWorkload`

    Cserélje `C:\` le a *SystemDrive*betűjét.

1. Zárja ki a virtuális gépen futó következő három folyamatot a víruskereső késedelmi beolvasásából:

    - IaasWLPluginSvc.exe
    - IaasWorkloadCoordinaorService.exe
    - TriggerExtensionJob.exe

1. Az SQL néhány irányelvet is kínál a víruskereső programokkal való kapcsolatról. A részleteket ebben a [cikkben](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) találja.

## <a name="error-messages"></a>Hibaüzenetek

### <a name="backup-type-unsupported"></a>A biztonsági mentés típusa nem támogatott

| Severity | Leírás | Lehetséges okok | Javasolt művelet |
|---|---|---|---|
| Figyelmeztetés | Az adatbázis jelenlegi beállításai nem támogatják a társított házirendben szereplő bizonyos biztonsági mentési típusokat. | <li>A fő adatbázison csak teljes adatbázis-biztonsági mentési művelet hajtható végre. Sem különbözeti biztonsági mentés, sem tranzakciós napló biztonsági mentése nem lehetséges. </li> <li>Az egyszerű helyreállítási modellben lévő adatbázisok nem teszik lehetővé a tranzakciós naplók biztonsági mentését.</li> | Módosítsa az adatbázis-beállításokat úgy, hogy a házirend összes biztonságimentési típusa támogatott. Vagy módosítsa az aktuális házirendet úgy, hogy csak a támogatott biztonsági mentési típusokat tartalmazza. Ellenkező esetben a nem támogatott biztonsági mentési típusok kimaradnak az ütemezett biztonsági mentés során, vagy a biztonsági mentési feladat sikertelen lesz az igény szerinti biztonsági mentésnél.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Ez az SQL-adatbázis nem támogatja a kért típusú biztonsági mentést. | Akkor következik be, amikor az adatbázis-helyreállítási modell nem engedélyezi a kért biztonsági másolat típusát. A hiba a következő esetekben fordulhat elő: <br/><ul><li>Egy egyszerű helyreállítási modellt használó adatbázis nem teszi lehetővé a napló biztonsági mentését.</li><li>A főadatbázisban nem engedélyezettak a különbözeti és a naplóbiztonsági mentések.</li></ul>További részleteket az [SQL Server helyreállítási modellek](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentációjában talál. | Ha az egyszerű helyreállítási modellben az adatbázis naplóbiztonsági mentése sikertelen, próbálkozzon az alábbi lehetőségekkel:<ul><li>Ha az adatbázis egyszerű helyreállítási módban van, tiltsa le a naplóbiztonsági mentéseket.</li><li>Az [SQL Server dokumentációjával](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) módosítsa az adatbázis-helyreállítási modellt teljes vagy tömeges naplózóra. </li><li> Ha nem szeretné módosítani a helyreállítási modellt, és rendelkezik egy szabványos házirenddel, amely több olyan adatbázisbiztonsági biztonsági mentést, amely nem módosítható, hagyja figyelmen kívül a hibát. A teljes és a különbözeti biztonsági mentések ütemezésszerint fognak működni. A napló biztonsági mentések kimaradnak, ami ebben az esetben várható.</li></ul>Ha fő adatbázisról van szó, és konfigurálta a különbözeti vagy a naplóbiztonsági mentést, kövesse az alábbi lépéseket:<ul><li>A portál segítségével módosíthatja a fő adatbázis biztonsági mentési házirend-ütemezését teljesre.</li><li>Ha rendelkezik egy szabványos házirenddel, amely több olyan adatbázisról is biztonsági másolatot szeretne, amelyek nem módosíthatók, hagyja figyelmen kívül a hibát. A teljes biztonsági mentés ütemezésszerint fog működni. Különbözeti vagy napló biztonsági mentések nem történik meg, ami ebben az esetben várható.</li></ul> |
| Az ütköző művelet megszakadt, mert már futott ugyanazon az adatbázison. | Tekintse meg az egyidejűleg futó [biztonsági mentési és visszaállítási korlátozásokról szóló blogbejegyzést.](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/)| [Az SQL Server Management Studio (SSMS) segítségével figyelheti a biztonsági mentési feladatokat.](manage-monitor-sql-database-backup.md) Az ütköző művelet sikertelensítése után indítsa újra a műveletet.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL-adatbázis nem létezik. | Az adatbázist törölték vagy átnevezték. | Ellenőrizze, hogy véletlenül törölték vagy átnevezték-e az adatbázist.<br/><br/> Ha az adatbázist véletlenül törölték, a biztonsági mentések folytatásához állítsa vissza az adatbázist az eredeti helyre.<br/><br/> Ha törölte az adatbázist, és nincs szüksége jövőbeli biztonsági mentésre, akkor a Helyreállítási szolgáltatások tárolójában válassza a **Biztonsági mentés leállítása** a biztonsági **mentési adatok megtartásával** vagy **a Biztonsági másolat adatainak törlése**lehetőséget. További információt az [SQL Server-adatbázisok kezelése és figyelése című témakörben talál.](manage-monitor-sql-database-backup.md)

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A naplólánc megszakadt. | Az adatbázis vagy a virtuális gép biztonsági mentést egy másik biztonsági mentési megoldás, amely csonkolja a naplóláncot.|<ul><li>Ellenőrizze, hogy van-e másik biztonsági mentési megoldás vagy parancsfájl. Ha igen, állítsa le a másik biztonsági mentési megoldást. </li><li>Ha a biztonsági mentés igény szerinti naplóbiztonsági mentés volt, indítsa el a teljes biztonsági mentést egy új naplólánc elindításához. Ütemezett naplóbiztonsági mentések esetén nincs szükség műveletre, mert az Azure Backup szolgáltatás automatikusan elindítja a teljes biztonsági mentést a probléma megoldásához.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az Azure Backup nem tud csatlakozni az SQL-példányhoz. | Az Azure Backup nem tud csatlakozni az SQL Server-példányhoz. | Az Azure Portal hibamenüjének további részleteivel szűkítheti a kiváltó okokat. A hiba megoldásához olvassa el az [SQL biztonsági mentési hibaelhárítását.](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)<br/><ul><li>Ha az alapértelmezett SQL-beállítások nem engedélyezik a távoli kapcsolatokat, módosítsa a beállításokat. A beállítások módosításáról az alábbi cikkekben olvashat:<ul><li>[MSSQLSERVER_-1](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error?view=sql-server-ver15)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Bejelentkezési problémák esetén az alábbi hivatkozásoksegítségével javíthatja ki azokat:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Hiányzik az adatforrás első teljes biztonsági mentése. | Hiányzik az adatbázis teljes biztonsági mentése. A napló- és különbözeti biztonsági mentések a teljes biztonsági mentés szülők, ezért a különbözeti vagy naplóbiztonsági mentések elrendelése előtt mindenképpen készítsen teljes biztonsági mentést. | Igény szerinti teljes biztonsági mentés aktiválása.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Nem lehet biztonsági másolatot készíteni, mert az adatforrás tranzakciónaplója megtelt. | Az adatbázis tranzakciós naplóterülete megtelt. | A probléma megoldásához olvassa el az [SQL Server dokumentációját.](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error) |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az azonos nevű adatbázis már létezik a célhelyen | A cél-visszaállítási cél már rendelkezik ugyanazzal a névvel rendelkező adatbázissal.  | <ul><li>Módosítsa a céladatbázis nevét.</li><li>Vagy használja a visszaállítási lapon az erőfelülírás i.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A visszaállítás nem sikerült, mert az adatbázist nem lehetett offline állapotba helyezni. | A visszaállítás közben a céladatbázist offline állapotba kell hozni. Az Azure Backup nem tudja ezeket az adatokat offline állapotba hozni. | Az Azure Portal hibamenüjének további részleteivel szűkítheti a kiváltó okokat. További információért tekintse meg [az SQL Server dokumentációját](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Nem található a kiszolgálói tanúsítvány ujjlenyomattal a célon. | A célpéldány fő adatbázisa nem rendelkezik érvényes titkosítási ujjlenyomattal. | Importálja a forráspéldányon használt érvényes tanúsítványujjlenyomatot a célpéldányba. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A helyreállításhoz használt naplóalapú biztonsági mentés tömegesen naplózott módosításokat tartalmaz. Az SQL-irányelveknek megfelelően nem használható tetszőleges időpontban való leállításhoz. | Ha egy adatbázis tömegesen naplózott helyreállítási módban van, a tömegesen naplózott tranzakció és a következő naplótranzakció közötti adatok nem hajthatók helyre. | Válasszon egy másik időpontot a helyreállításhoz. [További információ](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL Always On rendelkezésre állási csoport biztonsági mentési beállítása nem teljesíthető, mert a rendelkezésre állási csoport egyes csomópontjai nincsenek regisztrálva. | A biztonsági mentések végrehajtásához szükséges csomópontok nincsenek regisztrálva vagy nem érhetőel el. | <ul><li>Győződjön meg arról, hogy az adatbázis biztonsági másolatainak végrehajtásához szükséges összes csomópont regisztrálva és kifogástalan állapotban van, majd próbálkozzon újra a művelettel.</li><li>Módosítsa az SQL Server Always On rendelkezésre állási csoportjának biztonsági mentési beállítását.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| AZ SQL server virtuális gép vagy leáll, és nem érhető el az Azure Backup szolgáltatás számára. | A virtuális gép le van állítva. | Győződjön meg arról, hogy az SQL Server-példány fut. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az Azure Backup szolgáltatás az Azure VM vendégügynöke a biztonsági mentéshez, de a vendégügynök nem érhető el a célkiszolgálón. | A vendégügynök nincs engedélyezve, vagy nem kifogástalan. | [Telepítse a virtuális gép vendégügynök](../virtual-machines/extensions/agent-windows.md) manuálisan. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az automatikus védelmi szándék ot eltávolították, vagy már nem érvényes. | Ha engedélyezi az automatikus védelmet egy SQL Server-példányon, a Biztonsági mentési feladatok **konfigurálása** az adott példány összes adatbázisán fut. Ha a feladatok futása közben letiltja az automatikus védelmet, akkor ezzel a hibakóddal megszakítja a **folyamatban lévő** feladatokat. | Engedélyezze ismét az automatikus védelmet az összes többi adatbázis védelme érdekében. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitreached

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
A művelet blokkolva van, mivel elérte a 24 óra alatt engedélyezett műveletek számának korlátját. | Ha 24 órán belül elérte egy művelet maximális megengedett korlátját, ez a hiba jelentkezik. <br> Például: Ha elérte a napi aktiválható biztonsági mentési feladatok konfigurálása korlátját, és megpróbálja beállítani a biztonsági mentést egy új elemen, akkor ez a hiba jelenik meg. | A művelet 24 óra elteltével történt újbóli megkísérlése általában megoldja ezt a problémát. Ha azonban a probléma továbbra is fennáll, segítségért forduljon a Microsoft támogatási szolgálatához.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
A művelet blokkolva van, mivel a tároló elérte a 24 órán belül engedélyezett műveletek maximális határát. | Ha 24 órán belül elérte egy művelet maximális megengedett korlátját, ez a hiba jelentkezik. Ez a hiba általában akkor jelentkezik, ha vannak olyan nagy léptékű műveletek, mint például a házirend módosítása vagy automatikus védelem. A CloudDosAbsoluteLimitReached esetében ellentétben nem sokat tehet az állapot feloldásához, sőt, az Azure Backup szolgáltatás újra megpróbálja a műveleteket belsőleg az összes kérdéses elem.<br> Például: Ha nagy számú adatforrásvédett egy házirenddel, és megpróbálja módosítani a házirendet, akkor a védett elemek mindegyikéhez konfigurálási feladatokat indít el, és néha előfordulhat, hogy eléri az ilyen műveletek napi maximális megengedett korlátját.| Az Azure Backup szolgáltatás 24 óra elteltével automatikusan újrapróbálkozik ezzel a művelettel.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
A virtuális gép internetkapcsolati problémák miatt nem tud kapcsolatba lépni az Azure Backup szolgáltatással. | A virtuális gépnek kimenő kapcsolatra van szüksége az Azure Backup Service, az Azure Storage vagy az Azure Active Directory-szolgáltatásokhoz.| - Ha nsg-t használ a kapcsolat korlátozására, majd az AzureBackup szolgáltatáscímkét kell használnia az Azure Backup azure biztonsági mentési szolgáltatáshoz, az Azure Storage-hoz vagy az Azure Active Directory-szolgáltatásokhoz való kimenő hozzáférés lehetővé teszi. A hozzáférés engedélyezéséhez kövesse az alábbi [lépéseket.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags)<br>- Győződjön meg arról, hogy a DNS megoldja az Azure-végpontokat.<br>- Ellenőrizze, hogy a virtuális gép mögött egy terheléselosztó blokkolja az internet-hozzáférést. Nyilvános IP-cím hozzárendelésével a virtuális gépek, felderítés ida fog működni.<br>- Ellenőrizze, hogy nincs-e tűzfal / víruskereső / proxy, amely blokkolja a hívásokat a fenti három cél szolgáltatások.

## <a name="re-registration-failures"></a>Újraregisztrációs hibák

Az újbóli regisztrálási művelet megkezdése előtt ellenőrizze, hogy van-e az alábbi jelenség:

* Minden művelet (például a biztonsági mentés, visszaállítás és a biztonsági mentés konfigurálása) sikertelen a virtuális számítógépen a következő hibakódok egyikével: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* Ha a **biztonsági másolat állapota** területen látható a Nem érhető **el,** zárja ki az összes többi okot, amely ugyanazt az állapotot eredményezheti:

  * A virtuális gépbiztonsági mentéssel kapcsolatos műveletek végrehajtására vonatkozó engedély hiánya.
  * A virtuális gép leállítása, így a biztonsági mentések nem történhetnek meg.
  * Hálózati problémák.

   ![virtuális gép újbóli regisztrálása](./media/backup-azure-sql-database/re-register-vm.png)



* Mindig rendelkezésre állási csoport esetén a biztonsági mentések sikertelenül kezdődtek, miután módosította a biztonsági mentési beállításokat vagy egy feladatátvételt.

Ezek a tünetek a következő okok miatt merülhetnek fel:

* Egy bővítményt töröltek vagy eltávolítottak a portálról.
* Egy bővítményt eltávolítottak a **Vezérlőpultról** a virtuális gépen a **Program eltávolítása vagy módosítása**csoportban.
* A virtuális gép visszaállt az időben a helyszíni lemez-visszaállítás révén.
* A virtuális gép hosszabb időre leállt, így a bővítmény konfigurációja lejárt.
* A virtuális gép törölve lett, és egy másik virtuális gép jött létre ugyanazzal a névvel, és ugyanabban az erőforráscsoportban, mint a törölt virtuális gép.
* A rendelkezésre állási csoport csomópontok egyike nem kapta meg a teljes biztonsági mentési konfigurációt. Ez akkor fordulhat elő, ha a rendelkezésre állási csoport regisztrálva van a tárolóban, vagy ha egy új csomópont ot ad hozzá.

Az előző forgatókönyvekben azt javasoljuk, hogy indítsa el a virtuális gép újraregisztrálási műveletet. A feladat PowerShellben való végrehajtásáról [itt](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) olvashat.

## <a name="size-limit-for-files"></a>Fájlok méretkorlátja

A fájlok teljes karakterláncmérete nem csak a fájlok számától függ, hanem a nevüktől és az elérési útjuktól is. Minden adatbázisfájlhoz bekell szereznie a logikai fájl nevét és a fizikai elérési utat. Ezt az SQL-lekérdezést használhatja:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Most rendezze el őket a következő formátumban:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Például:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Ha a tartalom karakterláncmérete meghaladja a 20 000 bájtot, az adatbázisfájlok tárolása eltérő módon történik. A helyreállítás során nem tudja beállítani a célfájl elérési útját a visszaállításhoz. A fájlok visszaállnak az SQL Server által biztosított alapértelmezett SQL elérési útra.

### <a name="override-the-default-target-restore-file-path"></a>Az alapértelmezett cél-visszaállítási fájl elérési útjának felülbírálása

A cél-visszaállítási fájl elérési útját a visszaállítási művelet során felülbírálhatja, ha egy JSON-fájlt helyez el, amely az adatbázisfájl nak a cél-visszaállítási útvonalhoz való hozzárendelését tartalmazza. Hozzon `database_name.json` létre egy fájlt, és helyezze el a *C:\Program Files\Azure Workload Backup\bin\plugins\SQL*helyen.

A fájl tartalmának a következő formátumban kell lennie:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Például:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

Az előző tartalomban az adatbázisfájl logikai nevét a következő SQL-lekérdezés segítségével szerezheti be:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

Ezt a fájlt a visszaállítási művelet megkezdése előtt el kell helyezni.

## <a name="next-steps"></a>További lépések

Az Azure Backup for SQL Server virtuális gépek (nyilvános előzetes verzió) című témakörben talál további információt az Azure Backup for SQL virtuális gépekről című [témakörben.](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)
