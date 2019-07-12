---
title: Az SQL Server adatbázis biztonsági mentésének hibaelhárítása az Azure Backup használatával |} A Microsoft Docs
description: Hibaelhárítási információk az Azure Backup szolgáltatással az Azure virtuális gépeken futó SQL Server-adatbázisok biztonsági mentése.
services: backup
author: anuragm
manager: sivan
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: b2822a3c7dfa23065f2cbd35ef4e506efae026f2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704851"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Az SQL Server adatbázis biztonsági mentésének hibaelhárítása az Azure Backup használatával

Ez a cikk az Azure virtual machines szolgáltatásban futó SQL Server-adatbázisok hibaelhárítási információkat szolgáltat.

A biztonsági mentési folyamatból és a korlátozásokkal kapcsolatos további információkért lásd: [Azure virtuális gépeken futó SQL Server backup](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>SQL Server engedélyei

SQL Server-adatbázis védelme nem konfigurálható a virtuális gépen, telepítenie kell a **AzureBackupWindowsWorkload** bővítményt a virtuális gépen. Ha megjelenik a hibaüzenet **UserErrorSQLNoSysadminMembership**, ez azt jelenti, hogy az SQL Server-példány nem rendelkezik a szükséges biztonsági engedélyeket. Ez a hiba elhárításához kövesse [Virtuálisgép-engedélyek](backup-azure-sql-database.md#set-vm-permissions).

## <a name="error-messages"></a>Hibaüzenetek

### <a name="backup-type-unsupported"></a>Biztonsági mentés típusa nem támogatott

| Severity | Leírás | Lehetséges okok | Javasolt művelet |
|---|---|---|---|
| Figyelmeztetés | Ez az adatbázis jelenlegi beállításai nem támogatják bizonyos biztonsági mentési típusok szerepelnek a tartozó házirend. | <li>Csak a teljes adatbázis biztonsági mentési művelet végrehajtható a főadatbázison való futtatásával. Sem a különbségi biztonsági másolat, sem a tranzakciónapló biztonsági mentését nem lehetséges. </li> <li>Minden adatbázis az egyszerű helyreállítási modellben nem teszi lehetővé a tranzakciós naplók biztonsági mentése.</li> | Módosítsa az adatbázis beállításait, úgy, hogy a házirend az összes biztonsági mentési típusok támogatottak. Vagy módosítsa a jelenlegi házirend csak a támogatott biztonsági mentési típusok tartalmazza. Ellenkező esetben a nem támogatott biztonsági mentési típusok kihagyja az ütemezett biztonsági mentés során, vagy a biztonsági mentési feladat alkalmi biztonsági mentés sikertelen lesz.


### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL-adatbázis nem támogatja a kért biztonsági mentés típusát. | Akkor következik be, amikor az adatbázis helyreállítási modelljét nem teszi lehetővé a kért biztonsági mentési típust. A hiba a következő esetekben fordulhat elő: <br/><ul><li>Egy adatbázis, amely egy egyszerű helyreállítási modellt használó nem engedélyezi a naplóalapú biztonsági mentés.</li><li>Különbségi és naplóalapú biztonsági mentések nem engedélyezettek a master adatbázisban.</li></ul>További részletekért tekintse meg a [SQL Server helyreállítási modell](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentációját. | Ha az adatbázis az egyszerű helyreállítási modellben a naplóalapú biztonsági mentés sikertelen, próbálja meg az alábbi lehetőségek egyikét:<ul><li>Ha az adatbázis egyszerű helyreállítási módban van, tiltsa le a naplóalapú biztonsági mentések.</li><li>Használja a [SQL Server dokumentációjában](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) módosítása az adatbázis helyreállítási modelljét teljes vagy Tömegesen naplózott. </li><li> Ha nem szeretné módosítani a helyreállítási modellt, és a egy standard szintű szabályzatot, amely nem módosítható több adatbázisok biztonsági mentése, hagyja figyelmen kívül a hibát. A teljes és különbségi biztonsági mentés ütemezése szerint fog működni. A naplóalapú biztonsági mentések kimarad, amely ebben az esetben várt.</li></ul>Ha egy fő adatbázisra, és konfigurálta a különbségi, vagy a log backup, használja a következő lépések egyikét:<ul><li>A portál használatával módosítsa a biztonsági mentési szabályzat ütemezését a master adatbázisban, teljes.</li><li>Ha egy standard szintű házirend, amely nem módosítható több adatbázisok biztonsági mentése, hagyja figyelmen kívül a hibát. A teljes biztonsági mentés ütemezése szerint fog működni. Különbségi vagy a napló biztonsági mentések nem történik meg, amely ebben az esetben várt.</li></ul> |
| A művelet megszakadt, mert egy ütköző művelet már futott ugyanazon az adatbázison. | Tekintse meg a [blogbejegyzést: biztonsági mentés és visszaállítás korlátai](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) , amelyek egyidejűleg futtathatók.| [Az SQL Server Management Studio (SSMS) használatával figyelheti a biztonsági mentési feladatok](manage-monitor-sql-database-backup.md). Miután az ütköző művelet sikertelen, indítsa újra a műveletet.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL-adatbázis nem létezik. | Az adatbázist törölték vagy átnevezték. | Ellenőrizze, ha az adatbázis véletlenül törölték vagy átnevezték.<br/><br/> Ha véletlenül törölte az adatbázist, továbbra is a biztonsági másolatok, állítsa vissza az adatbázist az eredeti helyre.<br/><br/> Ha törli az adatbázist, és nem kell jövőbeli biztonsági mentések, majd a Recovery Services-tároló az **biztonsági mentés leállítása** a **biztonsági mentési adatok megőrzése** vagy **biztonsági másolatok adatainak törlése**. További információkért lásd: [kezelése és megfigyelése készíteni az SQL Server-adatbázisok](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A naplólánc megszakadt. | Az adatbázis vagy a virtuális gép biztonsági másolat egy másik biztonsági mentési megoldást, amely a naplózási láncban csonkolja keresztül.|<ul><li>Annak ellenőrzése, hogy egy másik biztonsági mentési megoldás vagy parancsfájl van használatban. Ha igen, állítsa le a többi biztonsági mentési megoldást. </li><li>Ha a biztonsági másolat volt egy ad-hoc biztonsági mentését, aktiválása egy teljes biztonsági mentés egy új naplólánca elindításához. Az ütemezett naplóalapú biztonsági mentések nem kell módosítania, mert az Azure Backup szolgáltatás automatikusan elindítja a teljes biztonsági mentést a probléma megoldásához.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az Azure Backup nem tud csatlakozni az SQL-példányhoz. | Az Azure Backup nem tud kapcsolódni az SQL Server-példányt. | Használja a további részletek a hiba az Azure portal menüben szűkítheti az az oka. Tekintse meg [SQL biztonsági mentésének hibaelhárítása](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) a hiba javításához.<br/><ul><li>Ha az alapértelmezett SQL-beállítások nem engedélyezi a távoli kapcsolatokat, módosítsa a beállításokat. A beállítások megváltoztatásával kapcsolatos információkat a következő cikkekben talál:<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Bejelentkezési problémák vannak, ha ezek a hivatkozások használatával javítsa ki őket:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Ez az adatforrás első teljes biztonsági mentés hiányzik. | Az adatbázis teljes biztonsági mentés hiányzik. Napló- és különbségi biztonsági mentések teljes biztonsági mentést a szülők, ezért ügyeljen arra, hogy teljes biztonsági másolat készítése elindítása előtt különbözeti vagy biztonsági másolataihoz. | Indítson egy ad-hoc teljes biztonsági mentést.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Nem lehet biztonsági mentés, mert az adatforrás tranzakciónaplója megtelt. | Szabad hely az adatbázis tranzakciós napló megtelt. | A probléma megoldásához tekintse meg a [SQL Server dokumentációjában](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Ilyen nevű adatbázis már létezik a célhelyen | A cél visszaállítási célhelyének már van ilyen nevű adatbázis.  | <ul><li>Módosítsa a céladatbázis neve.</li><li>Vagy használja a force Felülírás lehetőséget, a visszaállítás oldal.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A visszaállítás nem sikerült, mert az adatbázist nem lehetett offline állapotba helyezni. | Amíg a visszaállítás végez, a céladatbázis kell offline állapotba kerül. Az Azure Backup használata nem offline ezeket az adatokat. | Használja a további részletek a hiba az Azure portal menüben szűkítheti az az oka. További információkért lásd: a [SQL Server dokumentációjában](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Nem található az ujjlenyomattal rendelkező kiszolgálói tanúsítvány a célhelyen. | A cél-példányon a master adatbázis nem rendelkezik egy érvényes ujjlenyomatot. | Importálja a cél-példányhoz a forráspéldányra használt érvényes tanúsítvány-ujjlenyomatot. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A helyreállításhoz használt naplóalapú biztonsági mentés tömegesen naplózott módosításokat tartalmaz. Az SQL-irányelveknek megfelelően nem használható tetszőleges időpontban való leállításhoz. | Ha egy adatbázis Tömegesen naplózott helyreállítási módban van, a tömegesen naplózott tranzakciók és a következő log tranzakció adatait nem lehet helyreállítani. | Válasszon egy másik időpontra időpontot a helyreállításhoz. [További információk](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105)).


### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL Always On rendelkezésre állási csoport biztonsági mentési beállítása nem teljesíthető, mert a rendelkezésre állási csoport egyes csomópontjai nincsenek regisztrálva. | Biztonsági mentések végrehajtásához szükséges csomópontok nincsenek regisztrálva, vagy nem érhető el. | <ul><li>Győződjön meg arról, hogy az adatbázis biztonsági mentések végrehajtásához szükséges összes csomópontja regisztrált és megfelelő állapotú, majd próbálja megismételni a műveletet.</li><li>A biztonsági mentési preferenciáját az SQL Server Always On rendelkezésre állási csoport módosítása</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL Servert futtató virtuális gép leállt, és az Azure Backup szolgáltatás nem érhető el. | A virtuális gép leállt. | Győződjön meg arról, hogy az SQL Server-példány fut-e. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az Azure Backup szolgáltatás az Azure VM-vendégügynök használja a biztonsági mentéshez, de a vendégügynök nem érhető el a célkiszolgálón. | A vendégügynök nem engedélyezett, vagy nem megfelelő állapotban. | [A VM-vendégügynök telepítése](../virtual-machines/extensions/agent-windows.md) manuálisan. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Automatikus védelmi leképezésének vagy eltávolították, vagy nem több nem érvényes. | Az SQL Server-példány automatikus védelmének engedélyezésekor **biztonsági mentés konfigurálása** feladatok futtatása a példányon található összes adatbázis. Ha az automatikus védelem letiltásához, amíg a feladatok futnak, akkor a **folyamatban** feladatok törölve lesznek az erről a hibakódról. | Engedélyezze az automatikus védelem még egyszer, a fennmaradó adatbázisok védelme érdekében. |

## <a name="re-registration-failures"></a>Újraregisztrálás hibák

Ellenőrizze az alábbi jelenségek közül legalább egyet a regisztrálja újra a művelet elindítása előtt:

* Minden művelet (például a biztonsági mentés, visszaállítás és a biztonsági mentés konfigurálása) sikertelen a virtuális gépen a következő hibakódok egyikét: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* A **biztonsági mentés állapota** terület a biztonsági mentési elem jelenik-e meg **nem érhető el**. Zárja ki az összes többi okot, amely azt eredményezheti, hogy az azonos állapota:

  * Nincs engedélye a virtuális gép biztonsági mentésével kapcsolatos műveletek végrehajtása  
  * A virtuális gép, így a biztonsági mentések nem kerülhet sor leállítása
  * Hálózati problémák  

  ![A virtuális gép regisztrálása "Nem érhető el" állapota](./media/backup-azure-sql-database/re-register-vm.png)

* Tartozik egy Always On rendelkezésre állási csoportban, akkor a biztonsági másolatok elindítása után módosította a biztonsági mentési preferenciáját, és a egy feladatátvétel után sikertelen.

Ezek a problémák merülhetnek fel egy vagy több, a következő okok miatt:

* Egy bővítmény törölték, vagy eltávolítja a portálról. 
* Egy bővítmény eltávolítva a **Vezérlőpult** alatt a virtuális gépen **programok módosítása vagy eltávolítása**.
* A virtuális gép időbeli visszaállítást a helyi lemez keresztül vissza lett állítva.
* A virtuális gép le lett állítva hosszabb ideig, így a bővítmény konfigurációja, lejárt.
* A virtuális Gépet törölték, és a egy másik virtuális gép létrejött, ezzel a névvel, és ugyanazt az erőforráscsoportot, a törölt virtuális gép.
* Az egyik rendelkezésre állási csoport csomópontot nem kapta meg a teljes biztonsági mentési konfigurációját. Ez akkor fordulhat elő, amikor a rendelkezésre állási csoport regisztrálva van a tárolóba, vagy ha egy új csomópontra kerül.

A fenti esetekben javasoljuk, hogy a virtuális gép egy újraregisztrálni műveletet indít el. Most ezt a beállítást csak a Powershellen keresztül érhető el.

## <a name="size-limit-for-files"></a>A fájlok maximális mérete

Fájlok összesített karakterlánc mérete attól függ, nem csak a fájlok száma, hanem a nevek és elérési utak. Minden egyes adatbázisfájlhoz lekérése a logikai fájl nevét és a fizikai elérési útját. Használhatja az SQL-lekérdezést:

```
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Most már rendezze őket a következő formátumban:

```
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Például:

```
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Ha a tartalom a karakterlánc mérete nagyobb, mint 20 000 bájt, az adatbázisfájlok eltérően tárolódnak. A helyreállítás során nem fogunk állíthat be a célfájl elérési útja a visszaállításhoz. Az SQL Server által biztosított alapértelmezett SQL-elérési visszaállítja a fájlokat.

### <a name="override-the-default-target-restore-file-path"></a>Bírálja felül az alapértelmezett cél visszaállítási fájl elérési útja

A visszaállítási művelet során a cél visszaállítási fájl elérési útja felülbírálhatja egy JSON-fájlt, amely tartalmazza a leképezés a cél visszaállítási elérési utat az adatbázisfájl való elhelyezésével. Hozzon létre egy `database_name.json` fájlt, és a helyre *C:\Program Files\Azure munkaterhelés Backup\bin\plugins\SQL*.

A fájl tartalmát a következő formátumban kell lennie:
```
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

```
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

Az előző tartalom esetén a logikai neve, az adatbázis-fájl a következő SQL-lekérdezés használatával kaphat:

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


Ez a fájl a visszaállítási művelet elindítása előtt kell elhelyezni.

## <a name="next-steps"></a>További lépések

Az SQL Server virtuális gépek (nyilvános előzetes verzió) az Azure Backuppal kapcsolatos további információkért lásd: [Azure Backup az SQL virtuális gépek](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
