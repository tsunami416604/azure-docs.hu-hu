---
title: SQL Server adatbázis biztonsági mentésének hibáinak megoldása
description: Hibaelhárítási információk az Azure-beli virtuális gépeken futó SQL Server adatbázisok biztonsági mentéséhez Azure Backup-mel.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 69cae196e7fad70d75fb12709e5bf0d618bbc81c
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602322"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>SQL Server adatbázis biztonsági mentésének hibáinak megoldása Azure Backup használatával

Ez a cikk az Azure Virtual Machines szolgáltatásban futó SQL Server adatbázisok hibaelhárítási információit tartalmazza.

További információ a biztonsági mentési folyamatról és a korlátozásokról: [SQL Server biztonsági mentés az Azure-beli virtuális gépeken](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Engedélyek SQL Server

Ha egy SQL Server adatbázis védelmét szeretné konfigurálni egy virtuális gépen, telepítenie kell a **AzureBackupWindowsWorkload** bővítményt a virtuális gépen. Ha a **UserErrorSQLNoSysadminMembership**hibaüzenetet kap, az azt jelenti, hogy az SQL Server-példány nem rendelkezik a szükséges biztonsági mentési engedélyekkel. A hiba elhárításához kövesse a [virtuális gép engedélyeinek beállítása](backup-azure-sql-database.md#set-vm-permissions)című témakör lépéseit.

## <a name="troubleshoot-discover-and-configure-issues"></a>Problémák felderítésével és konfigurálásával kapcsolatos hibák elhárítása
Recovery Services-tároló létrehozása és konfigurálása után az adatbázisok felfedése és a biztonsági mentés konfigurálása két lépésből álló folyamat.<br>

![SQL](./media/backup-azure-sql-database/sql.png)

A biztonsági mentési konfiguráció során, ha az SQL-alapú virtuális gép és annak példányai nem láthatók a **virtuális gépek felderítési** adatbázisaiban, és a **biztonsági mentés konfigurálása** (lásd a fenti képen), ügyeljen a következőkre:

### <a name="step-1-discovery-dbs-in-vms"></a>1\. lépés: a virtuális gépeken futó felderítési adatbázisok

- Ha a virtuális gép nem szerepel a felderített virtuális gépek listájában, és nem regisztrált az SQL Backup szolgáltatásban egy másik tárolóban, kövesse a [felderítési SQL Server biztonsági mentési](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases) lépéseit.

### <a name="step-2-configure-backup"></a>2\. lépés: biztonsági mentés konfigurálása

- Ha az a tároló, amelyben az SQL virtuális gép regisztrálva van az adatbázisok védelméhez használt tárban, kövesse a [biztonsági mentés konfigurálása](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup) lépéseit.

Ha az SQL virtuális gépet regisztrálni kell az új tárolóban, akkor azt törölni kell a régi tárból.  Ha az SQL-alapú virtuális gépet a tárolóból törli, az összes védett adatforrást le kell állítani, és ezután törölheti a biztonsági másolatba mentett adatok védelmét. A biztonsági másolatba mentett adathalmazok törlése romboló művelet.  Miután áttekintette és elvégezte az összes óvintézkedést az SQL virtuális gép regisztrációjának megszüntetéséhez, regisztrálja ugyanezt a virtuális gépet egy új tárolóval, és próbálkozzon újra a biztonsági mentési művelettel.



## <a name="error-messages"></a>Hibaüzenetek

### <a name="backup-type-unsupported"></a>A biztonsági mentés típusa nem támogatott

| Severity | Leírás | Lehetséges okok | Javasolt művelet |
|---|---|---|---|
| Figyelmeztetés | Az adatbázis jelenlegi beállításai nem támogatják a társított szabályzatban található egyes biztonsági mentési típusokat. | <li>A Master adatbázison csak a teljes adatbázis biztonsági mentési művelete hajtható végre. Sem a különbözeti biztonsági mentés, sem a tranzakciós napló biztonsági mentése nem lehetséges. </li> <li>Az egyszerű helyreállítási modellben lévő adatbázisok nem teszik lehetővé a tranzakciónaplók biztonsági mentését.</li> | Módosítsa az adatbázis beállításait úgy, hogy a szabályzat összes biztonsági mentési típusa támogatott legyen. Vagy módosítsa a jelenlegi szabályzatot úgy, hogy csak a támogatott biztonsági mentési típusokat tartalmazza. Ellenkező esetben a rendszer kihagyja a nem támogatott biztonsági mentési típusokat az ütemezett biztonsági mentés során, vagy a biztonsági mentési feladat sikertelen lesz az igény szerinti biztonsági mentés során.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Ez az SQL-adatbázis nem támogatja a kért típusú biztonsági mentést. | Akkor következik be, amikor az adatbázis-helyreállítási modell nem engedélyezi a kért biztonsági mentési típust. A hiba a következő helyzetekben fordulhat elő: <br/><ul><li>Az egyszerű helyreállítási modellt használó adatbázisok nem teszik lehetővé A naplók biztonsági mentését.</li><li>A Master adatbázis esetében nem engedélyezett a különbségi és a napló biztonsági mentése.</li></ul>További részletekért tekintse meg a [SQL Server Recovery models](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentációját. | Ha a napló biztonsági mentése nem sikerül az adatbázishoz az egyszerű helyreállítási modellben, próbálja ki a következő lehetőségek egyikét:<ul><li>Ha az adatbázis egyszerű helyreállítási módban van, tiltsa le a naplók biztonsági mentését.</li><li>A [SQL Server dokumentációjának](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) használatával megváltoztathatja az adatbázis-helyreállítási modellt teljes vagy tömegesen naplózott értékre. </li><li> Ha nem szeretné módosítani a helyreállítási modellt, és rendelkezik egy szabványos házirenddel a nem módosítható adatbázisok biztonsági mentésére, hagyja figyelmen kívül a hibát. A teljes és a különbözeti biztonsági mentések ütemezés szerint fognak működni. A rendszer kihagyja a naplók biztonsági másolatait, ami ebben az esetben várható.</li></ul>Ha ez egy Master adatbázis, és beállította a különbözeti vagy a napló biztonsági mentését, használja a következő lépések egyikét:<ul><li>A portál használatával módosíthatja a biztonsági mentési szabályzat ütemezését a Master adatbázisra vonatkozóan, a teljes értékre.</li><li>Ha olyan általános házirenddel rendelkezik, amely nem módosítható több adatbázis biztonsági mentésére van beállítva, hagyja figyelmen kívül a hibát. A teljes biztonsági mentés ütemterv szerint fog működni. A különbözeti vagy a napló biztonsági mentése nem történik meg, ami ebben az esetben várható.</li></ul> |
| A művelet megszakadt, mert egy ütköző művelet már futott ugyanazon az adatbázison. | A párhuzamosan futó [biztonsági mentési és visszaállítási korlátozásokról a blogbejegyzésben](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) talál további információt.| [A biztonsági mentési feladatok figyeléséhez használja a SQL Server Management Studio (SSMS)](manage-monitor-sql-database-backup.md)szolgáltatást. Az ütköző művelet sikertelensége után indítsa újra a műveletet.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL-adatbázis nem létezik. | Az adatbázist törölték vagy átnevezték. | Ellenőrizze, hogy a rendszer véletlenül törölte vagy átnevezte-e az adatbázist.<br/><br/> Ha az adatbázist véletlenül törölték, a biztonsági mentések folytatásához állítsa vissza az adatbázist az eredeti helyére.<br/><br/> Ha törölte az adatbázist, és nem igényel jövőbeli biztonsági mentést, akkor a Recovery Services-tárolóban válassza a biztonsági mentés **leállítása** a biztonsági **mentési adat megőrzése** vagy a **biztonsági mentési adat törlése**lehetőséget. További információ: a [biztonsági másolatok SQL Server adatbázisainak kezelése és figyelése](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A naplólánc megszakadt. | Az adatbázis vagy a virtuális gép biztonsági mentést készít egy másik biztonsági mentési megoldással, amely levágja a naplózási láncot.|<ul><li>Ellenőrizze, hogy van-e használatban másik biztonsági mentési megoldás vagy parancsfájl. Ha igen, állítsa le a másik biztonsági mentési megoldást. </li><li>Ha a biztonsági másolat egy igény szerinti napló biztonsági másolata volt, indítson el egy teljes biztonsági mentést egy új naplózási lánc elindításához. Ütemezett naplók biztonsági mentése esetén nincs szükség beavatkozásra, mert a Azure Backup szolgáltatás automatikusan elindítja a teljes biztonsági mentést a probléma megoldásához.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Azure Backup nem tud csatlakozni az SQL-példányhoz. | Azure Backup nem tud csatlakozni a SQL Server példányhoz. | A Azure Portali hiba menüben található további részletek segítségével Szűkítse le a kiváltó okokat. A hiba elhárításához tekintse meg az [SQL biztonsági mentéssel kapcsolatos hibaelhárítást](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) .<br/><ul><li>Ha az alapértelmezett SQL-beállítások nem engedélyezik a távoli kapcsolatokat, módosítsa a beállításokat. A beállítások módosításával kapcsolatos információkért tekintse meg a következő cikkeket:<ul><li>[MSSQLSERVER_ – 1](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error?view=sql-server-ver15)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Ha bejelentkezési problémák léptek fel, a következő hivatkozásokat használhatja a kijavításához:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az adatforrás első teljes biztonsági mentése hiányzik. | Hiányzik az adatbázis teljes biztonsági mentése. A log és a különbözeti biztonsági mentések a szülők teljes biztonsági mentésére szolgálnak, ezért ügyeljen arra, hogy teljes biztonsági mentést készítsen a különböző biztonsági mentések elindítása előtt. | Igény szerinti teljes biztonsági mentés indítása.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A biztonsági mentés nem végezhető el, mert az adatforrás tranzakciónaplója megtelt. | Az adatbázis tranzakciós naplózási területe megtelt. | A probléma megoldásához tekintse meg a [SQL Server dokumentációját](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Már létezik ilyen nevű adatbázis a célhelyen | A cél-visszaállítás célhelyének már van azonos nevű adatbázisa.  | <ul><li>Módosítsa a céladatbázis nevét.</li><li>Vagy a visszaállítás lapon használja a kényszerített felülírás lehetőséget.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A visszaállítás nem sikerült, mert az adatbázist nem lehetett offline állapotba helyezni. | A visszaállítás során a célként megadott adatbázist offline állapotba kell állítani. Azure Backup nem tud offline állapotba hozni ezeket az adatfájlokat. | A Azure Portali hiba menüben található további részletek segítségével Szűkítse le a kiváltó okokat. További információért tekintse meg [az SQL Server dokumentációját](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Nem található az ujjlenyomattal rendelkező kiszolgálói tanúsítvány a célhelyen. | A célként megadott példány főadatbázisa nem rendelkezik érvényes titkosítási ujjlenyomattal. | Importálja a forrás példányon használt érvényes tanúsítvány-ujjlenyomatot a cél példányra. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A helyreállításhoz használt naplóalapú biztonsági mentés tömegesen naplózott módosításokat tartalmaz. Az SQL-irányelveknek megfelelően nem használható tetszőleges időpontban való leállításhoz. | Ha egy adatbázis tömegesen naplózott helyreállítási módban van, a tömegesen naplózott tranzakció és a következő naplózási tranzakció közötti adatmennyiség nem állítható helyre. | Válasszon egy másik időpontot a helyreállításhoz. [További információk](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL Always On rendelkezésre állási csoport biztonsági mentési beállítása nem teljesíthető, mert a rendelkezésre állási csoport egyes csomópontjai nincsenek regisztrálva. | A biztonsági mentések végrehajtásához szükséges csomópontok nincsenek regisztrálva, vagy nem érhetők el. | <ul><li>Győződjön meg arról, hogy az adatbázis biztonsági másolatainak végrehajtásához szükséges összes csomópont regisztrálva és kifogástalan állapotban van, majd próbálja megismételni a műveletet.</li><li>Módosítsa a SQL Server always on rendelkezésre állási csoport biztonsági mentési beállításait.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL Server rendszerű virtuális gép le van állítva, és nem érhető el Azure Backup szolgáltatás számára. | A virtuális gép le van állítva. | Győződjön meg arról, hogy a SQL Server példány fut. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Azure Backup szolgáltatás az Azure VM Guest agentet használja a biztonsági mentéshez, de a vendég ügynök nem érhető el a célkiszolgálón. | A vendég ügynök nincs engedélyezve vagy sérült. | [Telepítse manuálisan a virtuális gép vendég ügynökét](../virtual-machines/extensions/agent-windows.md) . |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az automatikus védelem szándéka el lett távolítva vagy nem érvényes. | Ha engedélyezi az automatikus védelmet egy SQL Server-példányon, **konfigurálja a biztonsági mentési** feladatok futtatását az adott példány összes adatbázisához. Ha letiltja az automatikus védelmet a feladatok futása közben, akkor a **folyamatban** lévő feladatok megszakadnak ezzel a hibakódtal. | Engedélyezze újra az automatikus védelmet az összes fennmaradó adatbázis védelme érdekében. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
A művelet le van tiltva, mert elérte a 24 órán belül engedélyezett műveletek számának korlátját. | Ha elérte a maximális megengedett korlátot egy 24 órás span művelethez, a hiba a következő:. <br> Például: ha elérte a napi indítású biztonsági mentési feladatok konfigurálásának korlátját, és egy új elemen kísérli meg a biztonsági mentés konfigurálását, akkor ez a hibaüzenet jelenik meg. | A művelet 24 óra elteltével történő újrapróbálkozása általában megoldja ezt a problémát. Ha azonban nem szűnik meg a probléma, segítségért forduljon a Microsoft támogatási szolgálatához.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
A művelet le van tiltva, mert a tároló elérte a maximális korlátot az ilyen műveletekhez, amely 24 órás időtartam alatt engedélyezett. | Ha elérte a maximális megengedett korlátot egy 24 órás span művelethez, a hiba a következő:. Ez a hiba általában akkor következik be, amikor vannak olyan méretezési műveletek, mint például a házirend módosítása vagy az automatikus védelem. A CloudDosAbsoluteLimitReached esetében nem sok mindent megtehet ennek az állapotnak a megoldásához, ugyanis Azure Backup a szolgáltatás a szóban forgó összes elemre vonatkozóan újra megpróbálja végrehajtani a műveleteket.<br> Például: Ha nagy számú adatforrással védett egy szabályzat, és megpróbálja módosítani ezt a házirendet, a rendszer elindítja a védelmi feladatok konfigurálását az egyes védett elemekhez, és esetenként az ilyen műveletek esetében a maximálisan megengedett határértéket is elérheti naponta.| A Azure Backup szolgáltatás 24 óra elteltével automatikusan újrapróbálkozik a művelettel.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
Az internetkapcsolattal kapcsolatos problémák miatt a virtuális gép nem tud kapcsolatba lépni Azure Backup szolgáltatással. | A virtuális gépnek kimenő kapcsolatra van szüksége Azure Backup szolgáltatáshoz, az Azure Storage-hoz vagy Azure Active Directory-szolgáltatásokhoz.| – Ha a NSG-t használja a kapcsolat korlátozására, akkor a AzureBackup szolgáltatás címkével kell rendelkeznie a Azure Backup szolgáltatáshoz, az Azure Storage-hoz vagy a Azure Active Directory-szolgáltatásokhoz való kimenő Azure Backup hozzáféréshez. A hozzáférés engedélyezéséhez kövesse az alábbi [lépéseket](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags) .<br>– Győződjön meg arról, hogy a DNS feloldja az Azure-végpontokat.<br>– Ellenőrizze, hogy a virtuális gép az internet-hozzáférést blokkoló terheléselosztó mögött van-e. Ha nyilvános IP-címet rendel a virtuális gépekhez, a felderítés működni fog.<br>– Győződjön meg arról, hogy nincs olyan tűzfal/víruskereső/proxy, amely blokkolja a fenti három cél szolgáltatás hívásait.


## <a name="re-registration-failures"></a>Ismételt regisztrálási hibák

A következő tünetek közül egyet vagy többet kell megvizsgálnia az ismételt regisztrálási művelet elindítása előtt:

* Az összes művelet (például a biztonsági mentés, a visszaállítás és a biztonsági mentés konfigurálása) a virtuális gépen meghiúsul a következő hibakódok egyikével: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* Ha a biztonsági mentési elem **biztonsági mentési állapota** területen **nem érhető el**, akkor zárja ki az összes többi olyan okot, amely ugyanazokat az állapotot eredményezheti:

  * Nincs engedélye a biztonsági mentéssel kapcsolatos műveletek elvégzésére a virtuális gépen.
  * A virtuális gép leállítása, így a biztonsági mentések nem hajthatók végre.
  * Hálózati problémák.

   ![virtuális gép újbóli regisztrálása](./media/backup-azure-sql-database/re-register-vm.png)



* Always On rendelkezésre állási csoport esetén a biztonsági mentések sikertelenek voltak, miután módosította a biztonsági mentési beállításokat vagy feladatátvételt követően.

Ezek a tünetek a következő okok valamelyike miatt merülhetnek fel:

* A bővítmény törölve lett vagy el lett távolítva a portálról.
* A rendszer eltávolít egy bővítményt a virtuális gépen a **Vezérlőpultról** a **program eltávolítása vagy módosítása**lehetőség alatt.
* A virtuális gép időben vissza lett állítva a helyi lemez visszaállítása révén.
* A virtuális gép leállt egy hosszabb ideig, így lejárt a bővítmény konfigurációja.
* A virtuális gép törölve lett, és egy másik virtuális gép lett létrehozva ugyanazzal a névvel és ugyanabban az erőforráscsoporthoz, mint a törölt virtuális gép.
* A rendelkezésre állási csoport csomópontjainak egyike nem kapta meg a teljes biztonsági mentési konfigurációt. Ez akkor fordulhat elő, ha a rendelkezésre állási csoport regisztrálva van a tárolóban, vagy új csomópont van hozzáadva.

Az előző forgatókönyvekben azt javasoljuk, hogy a virtuális gépen újra regisztrálja a műveletet. A feladat PowerShellben történő végrehajtásával kapcsolatban [itt](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) talál útmutatást.

## <a name="size-limit-for-files"></a>Fájlok maximális mérete

A fájlok teljes mérete nem csak a fájlok számától, hanem a nevükön és az elérési utakon is függ. Az egyes adatbázisfájlok esetében szerezze be a logikai fájl nevét és a fizikai elérési utat. Ezt az SQL-lekérdezést használhatja:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Most rendezze a következő formátumot:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Például:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Ha a tartalom karakterláncának mérete meghaladja a 20 000 bájtot, az adatbázisfájlok tárolása másképp történik. A helyreállítás során nem fogja tudni beállítani a célfájl elérési útját a visszaállításhoz. A fájlok a SQL Server által megadott alapértelmezett SQL-elérési útra lesznek visszaállítva.

### <a name="override-the-default-target-restore-file-path"></a>Az alapértelmezett cél-visszaállítási fájl elérési útjának felülbírálása

A visszaállítási művelet során felülbírálhatja a cél-visszaállítási fájl elérési útját úgy, hogy egy olyan JSON-fájlt helyez el, amely tartalmazza az adatbázisfájl hozzárendelését a cél-visszaállítási útvonalra. Hozzon létre egy `database_name.json` fájlt, és helyezze el a helyet a *C:\Program Files\Azure munkaterhelés Backup\bin\plugins\SQL*helyen.

A fájl tartalmának a következő formátumúnak kell lennie:

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

Az előző tartalomban az adatbázisfájl logikai nevét a következő SQL-lekérdezéssel kérheti le:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

Ezt a fájlt a visszaállítási művelet elindítása előtt kell elhelyezni.

## <a name="next-steps"></a>Következő lépések

A SQL Server virtuális gépek (nyilvános előzetes verzió) Azure Backupával kapcsolatos további információkért lásd: [Azure Backup SQL virtuális gépekhez](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
