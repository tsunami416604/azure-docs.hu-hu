---
title: Az SQL Server adatbázis biztonsági mentése az Azure Backup hibaelhárítása |} A Microsoft Docs
description: Hibaelhárítási információk az Azure Backup szolgáltatással az Azure virtuális gépeken futó SQL Server-adatbázisok biztonsági mentése.
services: backup
author: anuragm
manager: shivamg
ms.service: backup
ms.topic: article
ms.date: 05/27/2019
ms.author: anuragm
ms.openlocfilehash: 8459bb451c4ff462ee816b986cafdbf776603917
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306970"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Az Azure SQL Server biztonsági mentése – hibaelhárítás

Ez a cikk a hibaelhárítási információk védelme érdekében az SQL Server virtuális gépek az Azure-ban.

## <a name="feature-consideration-and-limitations"></a>A szolgáltatás szempontok és korlátozások

A szolgáltatás figyelmet megtekintése, tekintse meg a cikket [Azure virtuális gépeken futó SQL Server backup](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>SQL Server engedélyei

Az SQL Server-adatbázis védelme nem konfigurálható a virtuális gépen a **AzureBackupWindowsWorkload** bővítményt telepíteni kell, hogy a virtuális gép. Ha a hibaüzenetet kapja **UserErrorSQLNoSysadminMembership**, ez azt jelenti, hogy az SQL-példány nem rendelkezik a szükséges biztonsági engedélyeket. Ez a hiba elhárításához kövesse [állítsa be a marketplace-az SQL virtuális gépek](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).


## <a name="backup-type-unsupported"></a>Biztonsági mentés típusa nem támogatott

| Severity | Leírás | Lehetséges okok | Javasolt művelet |
|---|---|---|---|
| Figyelmeztetés | Ez az adatbázis jelenlegi beállításai nem támogatják bizonyos típusú biztonsági mentési típusok szerepelnek a tartozó házirend. | <li>**A master DB**: Csak a teljes adatbázis biztonsági mentési művelet hajtható végre a főadatbázisban; sem **különbözeti** biztonsági mentési, sem tranzakciós **naplók** biztonsági mentésre lehetőség. </li> <li>Bármilyen adatbázishoz **egyszerű helyreállítási modellt** nem engedélyezi a tranzakció **naplók** kell venni a biztonsági mentés.</li> | Módosítsa az adatbázis beállításait, úgy, hogy a házirend az összes biztonsági mentési típusok támogatottak. Azt is megteheti módosítsa az aktuális házirend csak a támogatott biztonsági mentési típusok tartalmazza. Ellenkező esetben a nem támogatott biztonsági mentési típusok kihagyja az ütemezett biztonsági mentés során, vagy a biztonsági mentési feladat alkalmi biztonsági mentés sikertelen lesz.


## <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL-adatbázis nem támogatja a kért biztonsági mentés típusát. | Akkor következik be, amikor az adatbázis helyreállítási modelljét nem teszi lehetővé a kért biztonsági mentési típust. A hiba a következő esetekben fordulhat elő: <br/><ul><li>Egy egyszerű helyreállítási modellt használó adatbázis nem engedélyezi a naplóalapú biztonsági mentés.</li><li>Különbségi és naplóalapú biztonsági mentések nem engedélyezettek a Master adatbázisban.</li></ul>További részletekért tekintse meg a [SQL helyreállítási modellek](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentációját. | Ha az adatbázis egyszerű helyreállítási modellben a naplóalapú biztonsági mentés sikertelen, próbálja meg az alábbi lehetőségek egyikét:<ul><li>Ha az adatbázis egyszerű helyreállítási módban van, tiltsa le a naplóalapú biztonsági mentések.</li><li>Használja a [SQL-dokumentáció](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) módosítása az adatbázis helyreállítási modelljét teljes vagy Tömegesen naplózott. </li><li> Ha nem szeretné módosítani a helyreállítási modellt, és a egy standard szintű szabályzatot, amely nem módosítható több adatbázisok biztonsági mentése, hagyja figyelmen kívül a hibát. A teljes és különbségi biztonsági mentés ütemezése szerint fog működni. A naplóalapú biztonsági mentések kimarad, amely ebben az esetben várt.</li></ul>Ha a Master adatbázisban, és konfigurálta különbségi vagy a napló biztonsági mentési, használja az alábbi műveletek közül:<ul><li>A portál a biztonsági mentési szabályzat ütemezését a Master adatbázis, teljes használja.</li><li>Ha egy standard szintű házirend, amely nem módosítható több adatbázisok biztonsági mentése, hagyja figyelmen kívül a hibát. A teljes biztonsági mentés ütemezése szerint fog működni. Különbségi vagy a napló biztonsági mentések nem történik meg, amely ebben az esetben várt.</li></ul> |
| A művelet megszakadt, mert egy ütköző művelet már futott ugyanazon az adatbázison. | Tekintse meg a [blogbejegyzés kapcsolatos biztonsági mentésére és visszaállítására vonatkozó korlátozások](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) , amelyek egyidejűleg futtathatók.| [Az SQL Server Management Studio (SSMS) használatával a biztonsági mentési feladatok monitorozása.](manage-monitor-sql-database-backup.md) Miután az ütköző művelet sikertelen, indítsa újra a műveletet.|

## <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL-adatbázis nem létezik. | Az adatbázist törölték vagy átnevezték. | Ellenőrizze, ha az adatbázis véletlenül törölték vagy átnevezték.<br/><br/> Ha véletlenül törölte az adatbázist, továbbra is a biztonsági másolatok, állítsa vissza az adatbázist az eredeti helyre.<br/><br/> Ha törli az adatbázist, és nem kell jövőbeli biztonsági mentések, majd a Recovery Services-tárolóba, az ["Törlés és adatok megőrzése" biztonsági mentés leállítása](manage-monitor-sql-database-backup.md).

## <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A naplólánc megszakadt. | Az adatbázis vagy a virtuális gép biztonsági másolat egy másik biztonsági mentési megoldást, amely a naplózási láncban csonkolja használatával.|<ul><li>Annak ellenőrzése, hogy egy másik biztonsági mentési megoldás vagy parancsfájl van használatban. Ha igen, állítsa le a többi biztonsági mentési megoldást. </li><li>Ha a biztonsági másolat volt egy ad-hoc biztonsági mentését, aktiválása egy teljes biztonsági mentés egy új naplólánca elindításához. Az ütemezett naplóalapú biztonsági mentések nem kell módosítania, az Azure Backup szolgáltatás automatikusan elindítja a teljes biztonsági mentést a probléma megoldásához.</li>|

## <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az Azure Backup nem tud csatlakozni az SQL-példányhoz. | Az Azure Backup nem tud kapcsolódni az SQL-példányhoz. | Használja a további részletek a hiba az Azure portál menüjében oka szűkítéséhez. Tekintse meg [SQL biztonsági mentésének hibaelhárítása](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) a hiba javításához.<br/><ul><li>Ha az alapértelmezett SQL-beállítások nem engedélyezik a távoli kapcsolatokat, módosítsa a beállításokat. Lásd az alábbi cikkeket a beállítások megváltoztatásával kapcsolatos információkat.<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Bejelentkezési problémák esetén tekintse meg az alábbi hivatkozások a javításhoz:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

## <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Ez az adatforrás első teljes biztonsági mentés hiányzik. | Az adatbázis teljes biztonsági mentés hiányzik. Napló- és különbségi biztonsági másolatok az szülője egy teljes biztonsági mentést, így teljes biztonsági mentést kell eljárnia elindítása előtt különbözeti vagy biztonsági másolataihoz. | Indítson egy ad-hoc teljes biztonsági mentést.   |

## <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Nem lehet biztonsági mentés, mert az adatforrás tranzakciónaplója megtelt. | Szabad hely az adatbázis tranzakciós napló megtelt. | A probléma megoldásához tekintse meg a [SQL-dokumentáció](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

## <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Ilyen nevű adatbázis már létezik a célhelyen | A cél visszaállítási célhelyének már van egy azonos nevű adatbázis.  | <ul><li>Módosítsa a céladatbázis neve</li><li>Vagy használja a force felülírása lehetőséget a visszaállítás oldal</li> |

## <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A visszaállítás nem sikerült, mert az adatbázist nem lehetett offline állapotba helyezni. | A visszaállítás közben, cél-adatbázist kell offline állapotba kerül. Az Azure Backup nem sikerül az adatok offline állapotba. | Használja a további részletek a hiba az Azure portál menüjében oka szűkítéséhez. További információkért lásd: a [SQL-dokumentáció](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

##  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Nem található az ujjlenyomattal rendelkező kiszolgálói tanúsítvány a célhelyen. | A fő cél-példányon adatbázist nem rendelkezik egy érvényes ujjlenyomatot. | Importálja a cél-példányhoz a forráspéldányra használt érvényes tanúsítvány-ujjlenyomatot. |

## <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A helyreállításhoz használt naplóalapú biztonsági mentés tömegesen naplózott módosításokat tartalmaz. Az SQL-irányelveknek megfelelően nem használható tetszőleges időpontban való leállításhoz. | Ha egy adatbázis Tömegesen naplózott helyreállítási módban van, az adatok között egy tömegesen naplózott tranzakciós és a következő log tranzakció nem állítható helyre. | Válasszon egy másik időpontra időpontot a helyreállításhoz. [További információ](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))


## <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL Always On rendelkezésre állási csoport biztonsági mentési beállítása nem teljesíthető, mert a rendelkezésre állási csoport egyes csomópontjai nincsenek regisztrálva. | Biztonsági mentések végrehajtásához szükséges csomópontok nincsenek regisztrálva, vagy nem érhető el. | <ul><li>Győződjön meg arról, hogy az adatbázis biztonsági mentések végrehajtásához szükséges összes csomópontja regisztrált és megfelelő állapotú, és próbálkozzon újra a művelettel.</li><li>Változás SQL Always On rendelkezésre állási csoport biztonsági mentési preferenciáját.</li></ul> |

## <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL Servert futtató virtuális gép leállt, és az Azure Backup szolgáltatás nem érhető el. | Virtuális gép leállt | Győződjön meg arról, hogy az SQL server fut-e. |

## <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az Azure Backup szolgáltatás az Azure VM-vendégügynök használja a biztonsági mentéshez, de a vendégügynök nem érhető el a célkiszolgálón. | A vendégügynök nem engedélyezett, vagy nem megfelelő állapotban | [A VM-vendégügynök telepítése](../virtual-machines/extensions/agent-windows.md) manuálisan. |

## <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Automatikus védelmi leképezésének vagy eltávolították, vagy nem több nem érvényes. | SQL-példány automatikus védelmének engedélyezésekor **biztonsági mentés konfigurálása** feladatok futtatása a példányon található összes adatbázis. Ha az automatikus védelem letiltásához, amíg a feladatok futnak, akkor a **folyamatban** feladatok törölve lesznek az erről a hibakódról. | Engedélyezze az automatikus védelem még egyszer, a fennmaradó adatbázisok védelméhez. |

## <a name="re-registration-failures"></a>Újraregisztrálás hibák

Egy vagy több keresése a [tünetek](#symptoms) a regisztrálja újra a művelet elindítása előtt.

### <a name="symptoms"></a>Probléma

* Minden művelet, mint a biztonsági mentés, visszaállítás, és konfigurálja a biztonsági mentés sikertelen a virtuális gépen a következő hibakódok egyikét: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**
* A **biztonsági mentés állapota** a biztonsági mentés elem jelenik-e meg **nem érhető el**. Bár Ön kell zárja ki az összes a más okokból is eredményezhet egy:

  * Nincs engedélye a virtuális gép biztonsági mentési kapcsolódó műveletek végrehajtásához  
  * Virtuális gép le lett állítva miatt, amelyek biztonsági mentések nem kerül sor
  * Hálózati problémák  

    ![Regisztrálja újra a virtuális gép](./media/backup-azure-sql-database/re-register-vm.png)

* Always on rendelkezésre állási csoport, esetén a biztonsági mentések kezdenek mutatkozni, után módosította a biztonsági mentési preferenciáját, vagy ha túl nagy volt a feladatátvétel

### <a name="causes"></a>Okok
Ezek a problémák merülhetnek fel a következő okok legalább egyike miatt:

  * Kiterjesztés törölve lett vagy eltávolítása a portál 
  * Bővítmény el lett távolítva a **Vezérlőpult** alatt a virtuális gép **programok módosítása vagy eltávolítása** felhasználói felület
  * Virtuális gép időbeli helyben (eke) t visszaállítással vissza lett állítva
  * Virtuális gép le lett állítva, ami miatt a bővítmény konfigurációja, lejárt hosszabb ideig
  * Virtuális gép törölve lett, és a egy másik virtuális gép létrejött, ezzel a névvel, és ugyanazt az erőforráscsoportot, a törölt virtuális gép
  * Az egyik rendelkezésre állási csoport csomópontot nem kapta meg a teljes biztonsági mentési konfiguráció, ez akkor fordulhat elő, vagy a rendelkezésre állási csoport regisztrációját, hogy a tároló időpontjában, vagy ha egy új csomópont hozzáadása  <br>
    A fenti esetekben ajánlott művelet regisztrálja újra a virtuális gép indításához. Ez a beállítás csak Powershellen keresztül érhető el, és hamarosan az Azure Portalon elérhető lesz.

## <a name="files-size-limit-beyond-which-restore-happens-to-default-path"></a>Fájl maximális mérete túl melyik visszaállítás történik az alapértelmezett elérési utat,

Fájlok összesített karakterlánc mérete nem csupán módja attól függ, a fájlok száma, hanem a nevek és elérési utak. Minden egyes az adatbázisfájlokat használhatja a logikai fájl nevét és a fizikai elérési útját.
Az alábbi SQL-lekérdezést is használhatja:

  ```
  SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
                 INNER JOIN sys.databases db ON db.database_id = mf.database_id
                 WHERE db.name = N'<Database Name>'"
 ```

Most már rendezze őket az alábbi formátumban:

  ```[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
  ```

Példa:

  ```[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
  ```

Ha a fent megadott tartalom a karakterlánc mérete nagyobb, mint 20 000 bájt, majd az adatbázis fájljai eltérően, és a helyreállítás során nem tudja beállítani a célfájl elérési útja a visszaállításhoz. A fájlok, SQL Server által biztosított alapértelmezett SQL elérési lesz visszaállítva.

### <a name="override-the-default-target-restore-file-path"></a>Bírálja felül az alapértelmezett cél visszaállítási fájl elérési útja

A visszaállítási művelet során a cél visszaállítási fájl elérési útja felülbírálhatja egy JSON-fájlt, amely tartalmazza a leképezés a cél visszaállítási elérési úton adatbázis fájl való elhelyezésével. Ehhez hozzon létre egy fájlt `database_name.json` és a helyre *C:\Program Files\Azure munkaterhelés Backup\bin\plugins\SQL*.

A fájl tartalmát az alábbi formátumban kell lennie:
  ```[
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

Példa:

  ```[
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

 
A fenti tartalomra a logikai neve, az alábbi SQL-lekérdezést használ az adatbázisfájl kaphat:

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


Ez a fájl a visszaállítási művelet elindítása előtt kell elhelyezni.

## <a name="next-steps"></a>További lépések

Az SQL Server virtuális gépek (nyilvános előzetes verzió) az Azure Backuppal kapcsolatos további információkért lásd: [Azure Backup az SQL virtuális gépek (nyilvános előzetes verzió)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
