---
title: Az Azure SQL Database felügyelt példány a T-SQL eltérései |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure SQL Database felügyelt példány és az SQL Server T-SQL eltérései
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 59599686b2a9ccee7250e33f0786d4c7af816983
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894309"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Az Azure SQL Database felügyelt példány T-SQL különbségek az SQL Serverről

A felügyelt példány üzembe helyezési lehetőséget biztosít nagy mértékben kompatibilis a helyszíni SQL Server Database Engine. Felügyelt példány az SQL Server adatbázismotor-funkciók a legtöbb támogatottak.

![Áttelepítés](./media/sql-database-managed-instance/migration.png)

Különbségek is vannak a továbbra is a szintaxist és a viselkedés, mivel ez a cikk összefoglalja, és ismerteti a különbségeket. <a name="Differences"></a>
- [Rendelkezésre állási](#availability) többek között a különbségek [Always-On](#always-on-availability) és [biztonsági mentések](#backup),
- [Biztonsági](#security) többek között a különbségek [naplózási](#auditing), [tanúsítványok](#certificates), [hitelesítő adatok](#credential), [kriptográfiai szolgáltatókat](#cryptographic-providers), [Bejelentkezések és felhasználók](#logins--users), [szolgáltatás kulcs és a szolgáltatás főkulcsát](#service-key-and-service-master-key),
- [Konfigurációs](#configuration) többek között a különbségek [kiterjesztés puffer](#buffer-pool-extension), [rendezést](#collation), [kompatibilitási szinteken](#compatibility-levels),[adatbázis tükrözés](#database-mirroring), [adatbázis-beállítások](#database-options), [SQL Server Agent](#sql-server-agent), [lehetőségek tábla](#tables),
- [Funkciók](#functionalities) beleértve [TÖMEGES beszúrási vagy OPENROWSET](#bulk-insert--openrowset), [CLR-beli](#clr), [DBCC](#dbcc), [elosztott tranzakciók](#distributed-transactions), [ Bővített események](#extended-events), [külső kódtáraiban](#external-libraries), [Filestream- és Filetable](#filestream-and-filetable), [szemantikai teljes szöveges keresés](#full-text-semantic-search), [társított kiszolgálók](#linked-servers), [Polybase](#polybase), [replikációs](#replication), [VISSZAÁLLÍTÁSA](#restore-statement), [Service Broker](#service-broker), [ Tárolt eljárások, függvények és eseményindítók](#stored-procedures-functions-triggers),
- [A felügyelt példányok eltérő viselkedéssel rendelkező szolgáltatások](#Changes)
- [Ideiglenes korlátozásai és ismert problémák](#Issues)

## <a name="availability"></a>Rendelkezésre állás

### <a name="always-on-availability"></a>Always-On

[Magas rendelkezésre állású](sql-database-high-availability.md) felügyelt példány be van építve, és a felhasználók nem vezérelhető. Az alábbi utasítások nem támogatottak:

- [VÉGPONT LÉTREHOZÁSA... A DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [KÖZVETLEN RENDELKEZÉSRE ÁLLÁSI CSOPORT](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) záradékában a [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) utasítás

### <a name="backup"></a>Backup

Felügyelt példányok rendelkeznek, automatikus biztonsági mentést, és lehetővé teszi a számára a teljes adatbázis létrehozásához `COPY_ONLY` biztonsági mentéseket. Különbségi, a napló és a fájl pillanatképes biztonsági mentései nem támogatottak.

- A felügyelt példány, készíthet biztonsági másolatot egy példány adatbázisa csak az Azure Blob Storage-fiók:
  - Csak `BACKUP TO URL` támogatott
  - `FILE`, `TAPE`, és a biztonsági mentési eszközök nem támogatottak.  
- A legtöbb általános `WITH` beállítások támogatottak.
  - `COPY_ONLY` kötelező megadni
  - `FILE_SNAPSHOT` nem támogatott
  - Szalagbeállítások: `REWIND`, `NOREWIND`, `UNLOAD`, és `NOUNLOAD` nem támogatottak
  - Napló-specifikus beállítások: `NORECOVERY`, `STANDBY`, és `NO_TRUNCATE` nem támogatottak

Korlátozások:  

- A felügyelt példány, meg is biztonsági másolatot-példány adatbázis legfeljebb 32 csíkokkal, amely elegendő az adatbázisok biztonsági akár 4 TB-os biztonságimásolat-tömörítési funkciók használata esetén.
- Maximális biztonsági mentési stripe mérete 195 GB (blob maximális mérete). A biztonsági mentési parancsban egyes stripe méret csökkentése és belül ezt a korlátot csíkokkal számának növelése.

> [!TIP]
> A korlátozás a helyszínen, a biztonsági mentés megkerüléséhez `DISK` történő biztonsági mentés helyett `URL`, blob-, majd állítsa vissza a biztonsági mentési fájl feltöltése. Visszaállítási támogatja a nagyobb méretű fájlokat, mert egy másik blob típust használja.  

Biztonsági másolatok a T-SQL használatával kapcsolatos információkért lásd: [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Biztonság

### <a name="auditing"></a>Naplózás

A naplózás az Azure SQL Database és az adatbázisok az SQL Server-adatbázisok közötti fő különbségeket a következők:

- Az Azure SQL Database felügyelt példány üzembe helyezési lehetősége, a naplózást a kiszolgáló szintjén, és a tárolók működését `.xel` naplófájlokat az Azure Blob storage-ban.
- Az önálló adatbázisok és az Azure SQL Database rugalmas készlet üzembe helyezési lehetőséget, a naplózás az adatbázis szintjén működik.
- A helyszíni SQL Server / virtuális gépek, naplózási működik a kiszolgálón. szintű, de a fájlok rendszer-vagy windows-eseménynaplók az események tárolja.
  
Az XEvent naplózási a felügyelt példány támogatja az Azure Blob storage tárolók. Fájl- és windows-naplók nem támogatottak.

A kulcs közötti különbségek a `CREATE AUDIT` szintaxist a naplózás az Azure Blob storage-vannak:

- Egy új szintaxis `TO URL` van megadva, és lehetővé teszi az Azure blob Storage-tároló URL-cím megadásához ahol `.xel` fájlok kerülnek.
- A szintaxist `TO FILE` nem támogatott, mert a felügyelt példány nem érhető el Windows-fájlmegosztásokon.

További információkért lásd:  

- [KISZOLGÁLÓ NAPLÓZÁSI LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [AZ ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Tanúsítványok

Felügyelt példány nem férnek hozzá, fájlmegosztások és Windows-mappák, így a következő korlátozások vonatkoznak:

- `CREATE FROM`/`BACKUP TO` fájl nem támogatott a tanúsítványok
- `CREATE`/`BACKUP` a tanúsítvány `FILE` / `ASSEMBLY` nem támogatott. Titkos kulcs fájljai nem használható.  

Lásd: [tanúsítvány létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) és [biztonsági MENTÉSI tanúsítvány](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
**Megkerülő megoldás**: tanúsítvány és titkos kulcs parancsfájl .sql fájlt tárolja, és a bináris létrehozása:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Hitelesítő adat

Csak az Azure Key Vault és `SHARED ACCESS SIGNATURE` identitások támogatottak. Windows-felhasználók nem támogatottak.

Lásd: [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) és [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Kriptográfiai szolgáltató

Felügyelt példány nem elérhető fájlok, így nem hozható létre a kriptográfiai szolgáltatók:

- `CREATE CRYPTOGRAPHIC PROVIDER` nem támogatott. Lásd: [létrehozása a TITKOSÍTÁSI SZOLGÁLTATÓ](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` nem támogatott. Lásd: [ALTER TITKOSÍTÁSI SZOLGÁLTATÓ](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins--users"></a>Bejelentkezések és felhasználók

- Létrehozott SQL-bejelentkezésekben `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, és `FROM SID` támogatottak. Lásd: [létrehozás bejelentkezési](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- A létrehozott Azure Active Directory (AAD) bejelentkezések [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) szintaxis vagy a [CREATE USER](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) szintaxis használata támogatott (**nyilvános előzetes verzióban**).
- A létrehozott Windows-bejelentkezések `CREATE LOGIN ... FROM WINDOWS` szintaxis használata nem támogatott. Használja az Azure Active Directory-bejelentkezések és felhasználók.
- A példányt hozott létre az Azure Active Directory (Azure AD) felhasználó [rendszergazdai jogosultságokat unrestricted](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Nem rendszergazdai Azure Active Directory (Azure AD) adatbázisszintű felhasználók hozható létre `CREATE USER ... FROM EXTERNAL PROVIDER` szintaxist. Lásd: [felhasználó létrehozása... A KÜLSŐ SZOLGÁLTATÓ](sql-database-manage-logins.md#non-administrator-users)

### <a name="service-key-and-service-master-key"></a>Kulcs és a szolgáltatás főkulcsának szolgáltatás

- [Master key backup](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) nem támogatott (az SQL Database szolgáltatás által felügyelt)
- [A főkulcs visszaállítási](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) nem támogatott (az SQL Database szolgáltatás által felügyelt)
- [Szolgáltatás főkulcsának biztonsági mentés](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) nem támogatott (az SQL Database szolgáltatás által felügyelt)
- [Szolgáltatás főkulcsának visszaállítása](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) nem támogatott (az SQL Database szolgáltatás által felügyelt)

## <a name="configuration"></a>Konfiguráció

### <a name="buffer-pool-extension"></a>A pufferkészlet-kiterjesztés

- [A puffer kiterjesztés](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nem támogatott.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` nem támogatott. Lásd: [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Rendezés

Az alapértelmezett példány rendezése nem `SQL_Latin1_General_CP1_CI_AS` és létrehozási paraméterként is megadhatók. Lásd: [rendezések](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Kompatibilitási szintek

- Támogatott kompatibilitási szintek a következők: 100, 110, 120, 130, 140  
- Nem támogatja a 100 alatti kompatibilitási szinteken.
- Új adatbázis alapértelmezett kompatibilitási szintje 140. A visszaállított adatbázis kompatibilitási szintje változatlan marad, ha 100 volt, vagy újabb.

Lásd: [ALTER adatbázis-kompatibilitási szint](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Adatbázis-tükrözés

Az adatbázis-tükrözés nem támogatott.

- `ALTER DATABASE SET PARTNER` és `SET WITNESS` beállítások nem támogatottak.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` nem támogatott.

További információkért lásd: [ALTER DATABASE SET PARTNER és a SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) és [VÉGPONT létrehozása... A DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Adatbázis-beállítások

- Több naplófájl nem támogatottak.
- Memóriában lévő objektumok nem támogatottak az általános célú szolgáltatási rétegben található.  
- Úgy adatbázisonként legfeljebb 280 fájlok példányonkénti 280 fájlok korlátozva van. Adatok és a naplófájlok számításba vesszük ezt a korlátot felé.  
- Adatbázis nem tartalmazhatnak filestream-adatokat tartalmazó fájlcsoportokat.  Helyreállítás sikertelen lesz, ha a .bak tartalmaz `FILESTREAM` adatokat.  
- Minden fájl kerül, az Azure Blob storage-ban. I/o és teljesítmény / fájl egyes fájlok méretét függenek.  

#### <a name="create-database-statement"></a>CREATE DATABASE utasítás

Az alábbi `CREATE DATABASE` korlátozások:

- Fájlok és fájlcsoportok nem definiálható.  
- `CONTAINMENT` a beállítás nem támogatott.  
- `WITH`beállítások nem támogatottak.  
   > [!TIP]
   > Áthidaló megoldásként használjon `ALTER DATABASE` után `CREATE DATABASE` -fájlok hozzáadása, vagy állítsa be a tartalmazási adatbázis beállításainak megadása.  

- `FOR ATTACH` a beállítás nem támogatott.
- `AS SNAPSHOT OF` a beállítás nem támogatott.

További információkért lásd: [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Az ALTER DATABASE utasítás

Egyes fájlok tulajdonságai nem állíthatók be, vagy nem módosította:

- Fájl elérési útja nem adható meg `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL-utasítással. Távolítsa el `FILENAME` a parancsfájlból, mert a felügyelt példány automatikusan helyezi a fájlokat.  
- Fájl neve nem módosítható az `ALTER DATABASE` utasítást.

Az alábbi lehetőségek alapértelmezés szerint vannak beállítva, és nem módosítható:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Nem lehet módosítani a következő beállításokat:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE`  
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Módosítsa neve nem támogatott.

További információkért lásd: [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- SQL-Ügyfélügynök-beállítások csak olvashatók. Az eljárás `sp_set_agent_properties` nem támogatott a figyelt példányokat.  
- Feladatok
  - T-SQL-feladat lépései támogatottak.
  - A következő replikációs feladatok támogatottak:
    - Replikálásitranzakciónapló-olvasó.  
    - Snapshot.
    - Terjesztő
  - Feladat lépései SSIS használata támogatott.
  - Feladat lépései más típusú jelenleg nem támogatottak, többek között:
    - Egyesítéses replikálási feladat lépésének nem támogatott.  
    - Üzenetsor-olvasó nem támogatott.  
    - Parancs-rendszerhéj még nem támogatott
  - Felügyelt példány nem fér hozzá a külső erőforrások (például a hálózati megosztások robocopy keresztül).  
  - PowerShell még nem támogatott.
  - Analysis Services nem támogatottak.
- Részlegesen támogatott értesítések
- E-mail értesítés használata támogatott, kell konfigurálni az adatbázisbeli levelezés profil. Csak egy adatbázisbeli levelezési profil lehet és kell meghívni `AzureManagedInstance_dbmail_profile` (átmeneti korlátozás) nyilvános előzetes verzióban érhető el.  
  - Személyi hívó nem támogatott.  
  - NetSend nem támogatott.
  - Riasztások nem még nem támogatottak.
  - Proxyk nem támogatottak.  
- Eseménynapló nem támogatott.

A következő funkciók jelenleg nem támogatottak, de engedélyezve lesz a jövőben:

- Proxyk
- A Processzor üresjárati feladatok ütemezése
- Az ügynök engedélyezése vagy letiltása
- Riasztások

Az SQL Server Agent kapcsolatos információkért lásd: [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Táblák

A következők nem támogatottak:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED`  

További információ a létrehozása és módosítása a táblák: [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) és [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkciók

### <a name="bulk-insert--openrowset"></a>Tömeges beszúrás / openrowset

Felügyelt példány nem elérhető fájlmegosztásokat és a Windows-mappák, így a fájlokat kell importálni az Azure Blob storage-ból:

- `DATASOURCE` a szükséges `BULK INSERT` parancsot az Azure Blob storage-ból fájlok importálása során. Lásd: [TÖMEGES Beszúrás](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` a szükséges `OPENROWSET` működni, amikor egy fájl tartalmát olvassa el az Azure Blob storage-ból. Lásd: [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR-BELI

Felügyelt példány nem férnek hozzá, fájlmegosztások és Windows-mappák, így a következő korlátozások vonatkoznak:

- Csak `CREATE ASSEMBLY FROM BINARY` használata támogatott. Lásd: [CREATE ASSEMBLY BINÁRISRÓL](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` nem támogatott. Lásd: [CREATE ASSEMBLY FÁJLBÓL](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` nem lehet hivatkozni a fájlokat. Lásd: [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).


### <a name="dbcc"></a>DBCC

Engedélyezve van az SQL Server nem dokumentált DBCC-utasítások nem támogatottak a figyelt példányokat.

- `Trace Flags` nem támogatottak. Lásd: [nyomkövetési jelzők](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` nem támogatott. Lásd: [DBCC traceoff utasítás használatával](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` nem támogatott. Lásd: [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Elosztott tranzakciók

Sem az MSDTC sem [rugalmas tranzakciók](sql-database-elastic-transactions-overview.md) a felügyelt példányok jelenleg támogatott.

### <a name="extended-events"></a>Bővített események

Egyes Windows-specifikus céljainak xevent típusú eseményekhez nem támogatottak:

- `etw_classic_sync target` nem támogatott. Store `.xel` fájlokat az Azure blob storage-ban. Lásd: [etw_classic_sync cél](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file target`nem támogatott. Store `.xel` fájlokat az Azure blob storage-ban. Lásd: [event_file cél](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Külső kódtárak

Az adatbázis-R és Python külső kódtáraiban még nem támogatottak. Lásd: [SQL Server Machine Learning-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>A FileStream és Filetable

- nem támogatott a FileStream-adatokat.
- Adatbázis nem tartalmazhat fájlcsoportok `FILESTREAM` adatok
- `FILETABLE` nem támogatott
- Táblák nem tartalmazhatnak `FILESTREAM` típusok
- A következő funkciók nem támogatottak:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

További információkért lásd: [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) és [fájltáblákon hozzáadni](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Teljes szöveges szemantikai keresés

[Szemantikai keresés](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) nem támogatott.

### <a name="linked-servers"></a>Társított kiszolgálók

A felügyelt példányok csatolt kiszolgálók célok korlátozott számú támogatják:

- Támogatott célok: Az SQL Server és SQL Database
- Nem támogatott tárolók: fájlok, Analysis Services és más relációsadatbázis-kezelő rendszer.

Műveletek

- Kereszt-példány írási tranzakciók nem támogatottak.
- `sp_dropserver` a csatolt kiszolgáló elvetését esetén támogatott. Lásd: [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` függvény csak az SQL Server-példányokat a lekérdezések végrehajtásához használható (akár felügyelt, a helyszínen, vagy a virtuális gépeken). Lásd: [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` függvény csak az SQL Server-példányokat a lekérdezések végrehajtásához használható (akár felügyelt, a helyszínen, vagy a virtuális gépeken). Csak `SQLNCLI`, `SQLNCLI11`, és `SQLOLEDB` szolgáltató értékek támogatottak. Például: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Lásd: [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

A HDFS- vagy Azure Blob storage-ban fájlok hivatkozó külső táblák nem támogatottak. A Polybase kapcsolatos információkért lásd: [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikáció

Replikációs felügyelt példányok nyilvános előzetes verziója érhető el. A replikációval kapcsolatos további információkért lásd: [SQL Server-replikáció](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>Utasítás VISSZAÁLLÍTÁSA

- Támogatott szintaxis
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nem támogatott szintaxist
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Forrás  
  - `FROM URL` (Az azure Blob storage) lehetőség csak támogatott.
  - `FROM DISK`/`TAPE`/ biztonsági mentési eszköz nem támogatott.
  - Biztonságimásolat-készletek nem támogatottak.
- `WITH` beállítások nem támogatottak (nem `DIFFERENTIAL`, `STATS`stb.)
- `ASYNC RESTORE` -Visszaállítás továbbra is fennáll, akkor is, ha az ügyfél kapcsolata megszakad. A kapcsolat megszakadásakor ellenőrizheti `sys.dm_operation_status` megtekintése a visszaállítási művelet állapota (valamint létrehozása és a DROP database). Lásd: [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  

A következő adatbázis-beállítások set/felül, és később nem módosítható:  

- `NEW_BROKER` (ha broker nincs engedélyezve a .bak-fájl)  
- `ENABLE_BROKER` (ha broker nincs engedélyezve a .bak-fájl)  
- `AUTO_CLOSE=OFF` (Ha a .bak-fájl egy adatbázis `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (Ha a .bak-fájl egy adatbázis `SIMPLE` vagy `BULK_LOGGED` helyreállítási módban)
- Memóriaoptimalizált fájlcsoportnak vesznek és XTP meghív, ha nem szerepel a .bak-forrásfájl  
- Minden olyan meglévő memóriaoptimalizált fájlcsoportnak XTP új neve  
- `SINGLE_USER` és `RESTRICTED_USER` beállítások alakulnak `MULTI_USER`

Korlátozások:  

- `.BAK` több biztonságimentés-készlet tartalmazó fájlok nem állíthatók vissza.
- `.BAK` több naplófájlt tartalmazó fájlok nem állíthatók vissza.
- Helyreállítás sikertelen lesz, ha a .bak tartalmaz `FILESTREAM` adatokat.
- Jelenleg az adatbázisokat, amelyek aktív memórián belüli objektumok tartalmazó biztonsági mentés nem állítható vissza.  
- Jelenleg Ha valamikor memórián belüli objektumok létezett adatbázisokat tartalmazó biztonsági mentés nem állítható vissza.
- Jelenleg csak olvasható módban adatbázisokat tartalmazó biztonsági mentés nem állítható vissza. Ez a korlátozás hamarosan eltávolítjuk.

Restore utasítások kapcsolatos információkért lásd: [VISSZAÁLLÍTÁSA utasítások](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker

Kereszt-példány service broker nem támogatott:

- `sys.routes` -Előfeltétel: Válasszon ki sys.routes címet. -Címnek kell lennie a helyi minden útvonalon. Lásd: [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE` – nem használható `CREATE ROUTE` a `ADDRESS` eltérő `LOCAL`. Lásd: [létrehozás ÚTVONAL](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE` nem lehet `ALTER ROUTE` a `ADDRESS` eltérő `LOCAL`. Lásd: [ALTER ÚTVONAL](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  

### <a name="stored-procedures-functions-triggers"></a>Tárolt eljárások, függvények, eseményindítók

- `NATIVE_COMPILATION` jelenleg nem támogatott.
- A következő [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) beállítások nem támogatottak:
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `max text repl size`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` nem támogatott. Lásd: [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` nem támogatott. Lásd: [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` nem támogatott, beleértve a `sp_addextendedproc`  és `sp_dropextendedproc`. Lásd: [bővített tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- `sp_attach_db`, `sp_attach_single_file_db`, és `sp_detach_db` nem támogatottak. Lásd: [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), és [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).
- `sp_renamedb` nem támogatott. Lásd: [sp_renamedb](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-renamedb-transact-sql).

## <a name="Changes"></a> Viselkedésbeli változások

A következő változók, functions és a nézetek különböző eredményeket adja vissza:

- `SERVERPROPERTY('EngineEdition')` tulajdonság értéke 8 értéket ad vissza. Ez a tulajdonság a felügyelt példány egyedileg azonosítja. Lásd: [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` NULL, értéket ad vissza, mert a fogalom, mert a példány az SQL Server nem vonatkozik a felügyelt példány létezik. Lásd: [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` értéket ad vissza teljes DNS-csatlakozási"nevét, például saját felügyelt instance.wcus17662feb9ce98.database.windows.net. Lásd: [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -adja vissza "csatlakozási" DNS-nevet, például a teljes `myinstance.domain.database.windows.net` Tulajdonságok "name" és "data_source". Lásd: [SYS. KISZOLGÁLÓK](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` NULL, értéket ad vissza, mert a fogalom, mert a szolgáltatás az SQL Server nem vonatkozik a felügyelt példány létezik. Lásd: [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` támogatott. NULL értéket ad vissza, ha az AAD-bejelentkezéséhez nem szerepel a sys.syslogins. Lásd: [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` nem támogatott. Értéket ad vissza helytelen adatokat (ideiglenes ismert probléma). Lásd: [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql).
- `GETDATE()` és más beépített dátum/idő függvények mindig időt adja vissza az UTC időzóna szerint. Lásd: [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Ismert problémák és korlátozások

### <a name="tempdb-size"></a>A TEMPDB mérete

`tempdb` van felosztva, amelyek 12 egyes rendelkező fájlok maximális mérete 14 GB fájlonként. Nem lehet módosítani a fájlonkénti a maximális méret és új fájlokat nem vehető fel `tempdb`. Ez a korlátozás hamarosan eltávolítjuk. Néhány lekérdezés előfordulhat, hogy vissza hibát, ha szükségük van a 168 GB-nál több `tempdb`.

### <a name="exceeding-storage-space-with-small-database-files"></a>Tárhely túllépése kis teljesítményigényű adatbázis-fájlokkal

Minden felügyelt példány rendelkezik az Azure prémium szintű lemez terület számára lefoglalt 35 TB tárterület, és egyes adatbázisfájlok külön fizikai lemezen kerül. Adatlemez-méretet 128 GB-os, 256 GB, 512 GB, 1 TB vagy 4 TB-os lehet. Nem használt területet a lemezen nem számítunk fel díjat, de az Azure prémium szintű lemezméretek teljes összege legfeljebb 35 TB. Bizonyos esetekben egy felügyelt példányt, amely nem kell 8 TB összesen meghaladhatja a 35 TB-os az Azure a belső töredezettség miatt a tárhely mérete korlátozza.

Például a felügyelt példány lehet egy 1,2 TB-os fájlt, amely el van helyezve a 4 TB-os lemez mérete, és 248-fájlok (minden 1 GB-os méretig) külön 128 GB-os lemezeken vannak elhelyezve. Ebben a példában:

- Az összes lefoglalt lemezterület tárméret: x 4 1 TB + 248 x 128 GB = 35 TB.
- a példányon adatbázisok teljes lefoglalt terület: x 1.2 1 TB + 248 x 1 GB = 1,4 TB.

Ez az ábra bemutatja, hogy bizonyos körülmény miatt a fájlok egy adott terjesztési csoportban, a felügyelt példány lehet elérni a csatolt Azure prémium szintű lemezes számára fenntartva, amikor előfordulhat, hogy nem a várt 35 TB.

Ebben a példában a meglévő adatbázisok továbbra is működni fog, és növelhető bármilyen probléma nélküli mindaddig, amíg az új fájlok nem lettek hozzáadva. Azonban új adatbázisokat nem kell létrehozni, vagy vissza, mert nem áll elég hely az új meghajtók, még akkor is, ha az összes adatbázis teljes mérete nem éri el a példány méretének korlátja. A visszaadott hiba ebben az esetben nem egyértelmű.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Adatbázis során SAS-kulcs helytelen konfiguráció visszaállítása

`RESTORE DATABASE` amely a .bak kiterjesztésű fájlt folyamatosan újrapróbálkozás előfordulhat, hogy hogy olvassa a .bak-fájl és visszatérési hiba hosszú idő után Ha beolvassa a közös hozzáférésű Jogosultságkód `CREDENTIAL` helytelen. Győződjön meg arról, hogy helyesek-e a SAS-kulcsának adatbázis visszaállítása előtt hajtsa végre a HEADERONLY VISSZAÁLLÍTÁSA.
Győződjön meg arról, hogy távolítsa el a vezető `?` az Azure portal használatával létrehozott SAS-kulcsot.

### <a name="tooling"></a>Eszköztámogatás

Az SQL Server Management Studio (SSMS) és az SQL Server Data Tools (SSDT) közben: a felügyelt példány néhány probléma lehet.

- Az Azure AD-bejelentkezések és felhasználók használata (**nyilvános előzetes verzióban**) és az SSDT együttes használata jelenleg nem támogatott.
- Az Azure AD-bejelentkezések és felhasználók-szkriptek (**nyilvános előzetes verzióban**) nem támogatottak az ssms-ben.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Az egyes nézetek, naplók és üzenetek helytelen adatbázis neve

Több rendszernézetek, teljesítményszámlálók, hibaüzenetek, xevent típusú eseményekhez és hibanapló-bejegyzést jelennek meg a GUID adatbázis-azonosítókat a tényleges adatbázis neve helyett. Ne támaszkodjon kizárólag ezek GUID azonosítókat, mert azok kellene írni a tényleges adatbázisnevek a jövőben.

### <a name="database-mail-profile"></a>Adatbázisbeli levelezési profil

Csak egy adatbázisbeli levelezési profil lehet és kell meghívni `AzureManagedInstance_dbmail_profile`.

### <a name="error-logs-are-not-persisted"></a>Hibanaplók nem megőrzött

Hibanaplókat a felügyelt példány nem rögzíti, és a méret nem szerepel a maximális méretkorlátot. Hibanaplók automatikusan törölni lehet, hogy a feladatátvétel esetén.

### <a name="error-logs-are-verbose"></a>Hibanaplók részletes.

Felügyelt példány hibanaplók helyezi részletes információkat, és nem vonatkoznak ezek közül számos. A jövőben a hibanaplók információ mennyisége csökkenni fog.

**Megkerülő megoldás**: Egy egyéni eljárással hibanaplókat, hogy néhány nem megfelelő bejegyzések szűrő kimenő olvasásához. További információkért lásd: [felügyelt példány – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-is-not-supported"></a>A két adatbázis belül ugyanazon tranzakció-hatókörben nem támogatott.

`TransactionScope` az osztály a .NET-es nem működik, ha két lekérdezést küld a két adatbázis ugyanazon a ugyanazon tranzakció-hatókörben alatt belül:

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Bár ez a kód ugyanazon adatok együttműködik az MSDTC megadása kötelező.

**Megkerülő megoldás**: Használjon [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) más database két kapcsolat használata helyett a kapcsolati környezet használata.

### <a name="clr-modules-and-linked-servers-sometime-cannot-reference-local-ip-address"></a>CLR-beli modulok és a egy ideig a csatolt kiszolgálók nem hivatkozhat helyi IP-cím

Felügyelt példány és a egy ideig hivatkoznak a jelenlegi példány csatolt kiszolgálók/elosztott lekérdezések CLR-beli modulok nem oldható fel az IP-címét a helyi példányát. Ez a hiba átmeneti jellegű probléma.

**Megkerülő megoldás**: Ha lehetséges használata helyi kapcsolatok CLR-beli modulban.

### <a name="tde-encrypted-databases-dont-support-user-initiated-backups"></a>Titkosított TDE adatbázisok nem támogatják a felhasználó által kezdeményezett biztonsági mentés

Nem hajtható végre `BACKUP DATABASE ... WITH COPY_ONLY` olyan adatbázisban, amely a transzparens adattitkosítás (TDE) van titkosítva. TDE kényszeríti biztonsági másolatának titkosítását belső TDE-kulcsokkal, és a kulcs nem exportálható, ezért nem lesz a biztonsági mentés visszaállításához.

**Megkerülő megoldás**: Automatikus biztonsági mentések és időponthoz visszaállítás használatához, vagy az adatbázis titkosításának letiltása.

## <a name="next-steps"></a>További lépések

- További információk a felügyelt példányok: [Mi az a felügyelt példány?](sql-database-managed-instance.md)
- Az a funkciók és összehasonlító listában, lásd: [általános SQL-szolgáltatások](sql-database-features.md).
- A rövid útmutató bemutatja, hogyan hozzon létre egy új felügyelt példányt, lásd: [hozzon létre egy felügyelt példányt](sql-database-managed-instance-get-started.md).
