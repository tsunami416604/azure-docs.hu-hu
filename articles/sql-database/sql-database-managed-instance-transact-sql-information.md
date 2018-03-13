---
title: "Az Azure SQL adatbázis felügyelt példány T-SQL különbségek |} Microsoft Docs"
description: "A cikk ismerteti az Azure SQL Database felügyelt-példányt és az SQL Server T-SQL különbségei."
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab, bonova
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/09/2018
ms.author: jovanpop
manager: cguyer
ms.openlocfilehash: 27be2b9a5f2b9aaf2d4464a6c927ec2a1694131a
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Az SQL Serverről az Azure SQL adatbázis felügyelt példány T-SQL különbségek 

Az Azure SQL adatbázis felügyelt példány (előzetes verzió) a helyszíni SQL Server adatbázis-kezelő magas kompatibilitást biztosít. Az SQL Server adatbázismotor-szolgáltatások többsége felügyelt példány támogatottak. Még vannak eltérések szintaxis és a viselkedés, mivel ez a cikk foglalja össze, és ezeket a különbségeket ismerteti.
 - [Nem támogatott funkciókat és a T-SQL különbségeket](#Differences)
 - [Felügyelt példány különböző viselkedés rendelkező szolgáltatások](#Changes)
 - [Ideiglenes korlátozások és ismert problémák](#Issues)

## <a name="Differences"></a> Az SQL Server T-SQL különbségek 

Ez a szakasz a kulcs T-SQL-szintaxis és a viselkedés közötti különbségek felügyelt példány és a helyszíni SQL Server adatbázismotor, valamint a nem támogatott funkciókat foglalja össze.

### <a name="always-on-availability"></a>Folyamatos rendelkezésre állás

[Magas rendelkezésre állású](sql-database-high-availability.md) felügyelt példány be van építve, és nem határozza meg a felhasználók. A következő utasítások nem támogatottak:
 - [-VÉGPONT LÉTREHOZÁSA... A DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql.md)
 - [RENDELKEZÉSRE ÁLLÁSI CSOPORT LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql.md)
 - [ALTER RENDELKEZÉSRE ÁLLÁSI CSOPORT](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql.md)
 - [KÖZVETLEN RENDELKEZÉSRE ÁLLÁSI CSOPORT](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql.md)
 - [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr.md) záradékában a [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) utasítás

### <a name="auditing"></a>Naplózás 
 
SQL-naplózási példány felügyelt, az Azure SQL Database és SQL Server helyszíni közötti legfőbb különbségek a következők:
- Felügyelt példányában SQL-naplózási működik. a kiszolgáló és tárolók `.xel` fájlok az Azure blob storage-fiók.  
- Az Azure SQL-adatbázis SQL-naplózás az adatbázis szintjén működik.
- A helyszíni SQL Server / a virtuális gép, SQL-naplózás a kiszolgáló szintjén működik, de fájlok rendszer vagy a windows eseménynaplóiban keresse meg az események tárolja.  
  
Az Azure blob storage tárolók XEvent naplózási felügyelt példányban támogatja. Fájl- és windows-naplók nem támogatottak.    
 
A kulcs közötti különbségek a `CREATE AUDIT` az Azure blob storage a naplózás szintaxis vannak:
- Egy új szintaxis `TO URL` valósul meg, és megadhatja az URL-CÍMÉT az Azure blob Storage tárolót ahol `.xel` fájlok kerülnek. 
- A szintaxis `TO FILE` nem támogatott, mert a példány felügyelt nem fér hozzá a Windows-fájlmegosztásokon. 
 
További információkért lásd:  
- [KISZOLGÁLÓ NAPLÓZÁSI LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [AZ ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql) 
- [Naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)     

### <a name="backup"></a>Biztonsági mentés 

Felügyelt példány automatikus biztonsági mentésekhez van, és lehetővé teszi a felhasználóknak teljes adatbázis létrehozásához `COPY_ONLY` biztonsági mentéseket. Eltérés, a napló és a fájl pillanatképes biztonsági nem támogatottak.  
- Felügyelt példány is adatbázisának biztonsági mentése csak az Azure Blob Storage-fiókhoz: 
 - Csak `BACKUP TO URL` támogatott 
 - `FILE`, `TAPE`, és a biztonsági mentési eszközök nem támogatottak.  
- A legtöbb általános `WITH` beállítások támogatottak. 
 - `COPY_ONLY` kötelező megadni
 - `FILE_SNAPSHOT` Nem támogatott  
 - Szalagbeállítások: `REWIND`, `NOREWIND`, `UNLOAD`, és `NOUNLOAD` nem támogatottak 
 - Naplózási beállítások: `NORECOVERY`, `STANDBY`, és `NO_TRUNCATE` nem támogatottak 
 
Korlátozások vonatkoznak:  
- Felügyelt példányt készíthet biztonsági másolatot egy adatbázist egy biztonsági másolatának legfeljebb 32 szétterítése, amely elegendő az adatbázisok biztonsági másolatok tömörítését használata akár 4 TB.
- Maximális biztonsági mentési paritásos mérete 195 GB (a blob maximális mérete). Az egyes paritásos méret csökkentéséhez és a határidőn belül maradnak a biztonsági mentési parancsban szétterítése számának növeléséhez. 

> [!TIP]
> Ez a korlátozás a helyszíni történő biztonsági mentés kerülő `DISK` történő biztonsági mentés helyett `URL`, a blob-, majd állítsa vissza biztonságimásolat-fájl feltöltése. Visszaállítás nagyobb fájlokat támogatja, mert egy másik blob típust használja.  

Biztonsági mentések T-SQL használatával kapcsolatos információkért lásd: [biztonsági MENTÉS](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

### <a name="buffer-pool-extension"></a>A pufferkészlet-kiterjesztés 
 
- [A puffer kiterjesztés](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nem támogatott.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` nem támogatott. Lásd: [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql). 
 
### <a name="bulk-insert--openrowset"></a>Tömeges beszúrás / openrowset

Felügyelt példány nem fér hozzá, fájlmegosztások és a Windows-mappák, így a fájlokat importálni kell az Azure blob storage.
- `DATASOURCE` a szükséges `BULK INSERT` során az Azure blob storage parancsot. Lásd: [TÖMEGES Beszúrás](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` a szükséges `OPENROWSET` működik, ha az Azure blob storage olvassa el a fájl tartalmát. Lásd: [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
 
### <a name="certificates"></a>Tanúsítványok 

Felügyelt példány nem tud hozzáférni a fájlmegosztások és a Windows-mappák, így ha a következő korlátozások vonatkoznak: 
- `CREATE FROM`/`BACKUP TO` a tanúsítványok nem támogatja a fájl
- `CREATE`/`BACKUP` a tanúsítvány `FILE` / `ASSEMBLY` nem támogatott. Titkos kulcs fájlok nem használható.  
 
Lásd: [hozzon létre TANÚSÍTVÁNYT](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) és [biztonsági tanúsítvány](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
> [!TIP]
> Megkerülő megoldás: parancsfájl-tanúsítvány és titkos kulcs, .sql fájlként tárolja, és a bináris létrehozása: 
> 
> ``` 
CREATE CERTIFICATE  
 FROM BINARY = asn_encoded_certificate    
WITH PRIVATE KEY ( <private_key_options> ) 
>```   
 
### <a name="clr"></a>CLR 

Felügyelt példány nem tud hozzáférni a fájlmegosztások és a Windows-mappák, így ha a következő korlátozások vonatkoznak: 
- Csak `CREATE ASSEMBLY FROM BINARY` esetén támogatott. Lásd: [CREATE ASSEMBLY BINÁRISRÓL](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` nem támogatott. Lásd: [CREATE ASSEMBLY FÁJLBÓL](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` nem lehet hivatkozni a fájlokat. Lásd: [a szerelvény megváltoztatása](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).
 
### <a name="compatibility-levels"></a>Kompatibilitási szintek 
 
- Támogatott kompatibilitási szintek a következők: 100, 110, 120, 130, 140  
- Kompatibilitási szint alatt 100 nem támogatottak. 
- Az új adatbázisokat alapértelmezett kompatibilitási szintje nem 140. A visszaállított adatbázis kompatibilitási szintje változatlan marad Ha 100 volt, vagy újabb.

Lásd: [ALTER adatbázis kompatibilitási szintje](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
 
### <a name="credential"></a>Hitelesítő adat 
 
Csak az Azure Key Vault és `SHARED ACCESS SIGNATURE` identitások támogatottak. Windows-felhasználók nem támogatottak.
 
Lásd: [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) és [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql). 
 
### <a name="cryptographic-providers"></a>Kriptográfiai szolgáltatók

Felügyelt példány nem tud hozzáférni a fájlokat, így nem hozható létre a kriptográfiai szolgáltatók:
- `CREATE CRYPTOGRAPHIC PROVIDER` nem támogatott. Lásd: [létrehozás KRIPTOGRÁFIAI SZOLGÁLTATÓ](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` nem támogatott. Lásd: [ALTER KRIPTOGRÁFIAI SZOLGÁLTATÓ](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql). 

### <a name="collation"></a>Rendezés 
 
Kiszolgálói rendezés megkülönbözteti `SQL_Latin1_General_CP1_CI_AS` és nem módosítható. Lásd: [rendezések](https://docs.microsoft.com/sql/t-sql/statements/collations).
 
### <a name="database-options"></a>Adatbázis-beállítások 
 
- Több naplófájl nem támogatottak. 
- Az általános célú szolgáltatási rétegben a memórián belüli objektumok nem támogatottak.  
- A maximális 280 fájlt adatbázisonként úgy példányonként 280 fájlok korlátozva van. Adatok és a naplófájlok felé ezt a határt bájtjai számítanak.  
- Adatbázis nem tartalmazhatnak filestream-adatokat tartalmazó fájlcsoportokat.  Visszaállítás sikertelen lesz, ha a .bak tartalmaz `FILESTREAM` adatokat.  
- Prémium szintű Azure storage minden fájl kerül. IO és átviteli fájlonként függ minden egyes fájl méretét ugyanúgy, mint a prémium szintű Azure Storage-lemezeket. Lásd: [prémium szintű lemez teljesítménye](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)  
 
#### <a name="create-database-statement"></a>CREATE DATABASE utasítás

A következő `CREATE DATABASE` korlátozások vonatkoznak: 
- Fájlok és a fájlcsoport nem definiálható.  
- `CONTAINMENT` a beállítás nem támogatott.  
- `WITH`beállítások nem támogatottak.  
   > [!TIP]
   > A probléma megoldásához használjon `ALTER DATABASE` után `CREATE DATABASE` fájlok hozzáadása vagy állíthat be a tartalmazási adatbázis beállításainak megadásához.  

- `FOR ATTACH` a beállítás nem támogatott. 
- `AS SNAPSHOT OF` a beállítás nem támogatott. 

További információkért lásd: [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Az ALTER DATABASE utasítás

Egyes fájlok tulajdonságai nem állíthatók be vagy nem változott:
- Fájl elérési útja nem adható meg a `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL-utasításban. Távolítsa el `FILENAME` a parancsfájlból, mivel felügyelt példány automatikusan helyezi a fájlokat.  
- Fájlnév használatával nem módosítható `ALTER DATABASE` utasítást.

A következő beállítások alapértelmezés szerint vannak beállítva, és nem lehet módosítani: 
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

Módosítsa név nem támogatott.

További információkért lásd: [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="database-mirroring"></a>Adatbázis-tükrözés

Az adatbázis-tükrözés nem támogatott.
 - `ALTER DATABASE SET PARTNER` és `SET WITNESS` beállítások nem támogatottak.
 - `CREATE ENDPOINT … FOR DATABASE_MIRRORING` nem támogatott.

További információkért lásd: [ALTER DATABASE SET PARTNER és a SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) és [VÉGPONT létrehozása... A DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="dbcc"></a>DBCC 
 
Engedélyezve van az SQL Server nem dokumentált DBCC utasítások nem támogatottak a felügyelt példány.
- `Trace Flags` nem támogatottak. Lásd: [nyomkövetési jelzők](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` nem támogatott. Lásd: [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` nem támogatott. Lásd: [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="extended-events"></a>Bővített események 

Egyes Windows-specifikus célok xevent típusú eseményekhez nem támogatottak:
- `etw_classic_sync target` nem támogatott. Tároló `.xel` fájlok az Azure blob-tároló. Lásd: [etw_classic_sync cél](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etwclassicsynctarget-target). 
- `event_file target`nem támogatott. Tároló `.xel` fájlok az Azure blob-tároló. Lásd: [event_file cél](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#eventfile-target).

### <a name="external-libraries"></a>Külső függvénytárak

Az adatbázis-R és Python külső szalagtárak még nem támogatott. Lásd: [SQL Server gépi tanulási szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>A FileStream és Filetable

- nem támogatott a FileStream-adatokat. 
- Adatbázis fájlcsoportok nem tartalmazhat `FILESTREAM` adatok
- `FILETABLE` nem támogatott
- Táblák nem tartalmazhatnak `FILESTREAM` típusok
- A következő funkciók nem támogatottak:
 - `GetPathLocator()` 
 - `GET_FILESTREAM_TRANSACTION_CONTEXT()` 
 - `PathName()` 
 - `GetFileNamespacePath()` 
 - `FileTableRootPath()` 

További információkért lásd: [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) és [ellátottak](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>A szemantikai teljes szöveges keresés

[A szemantikai keresési](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) nem támogatott.

### <a name="linked-servers"></a>Társított kiszolgálók
 
Csatolt kiszolgálók felügyelt példányban célok korlátozott számú támogatja: 
- Támogatott célok: SQL Server, az SQL-adatbázis, a példány által felügyelt és a SQL Server virtuális gépen.
- Nem támogatott a célok: fájlok, az Analysis Services és az egyéb RDBMS.

Műveletek

- Kereszt-példány írási tranzakciók nem támogatottak.
- `sp_dropserver` a csatolt kiszolgáló eldobása esetén támogatott. Lásd: [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` függvény csak az SQL Server-példányokat a lekérdezések végrehajtásához használható (akár felügyelt, a helyszíni vagy a virtuális gépeken). Lásd: [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` függvény csak az SQL Server-példányokat a lekérdezések végrehajtásához használható (akár felügyelt, a helyszíni vagy a virtuális gépeken). Csak `SQLNCLI`, `SQLNCLI11`, és `SQLOLEDB` értékek szolgáltató is támogatottak. Például: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Lásd: [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
 
### <a name="logins--users"></a>Bejelentkezések / felhasználók 

- SQL-bejelentkezésekben létrehozott `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, és `FROM SID` támogatottak. Lásd: [hozzon létre bejelentkezési](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- A Windows-bejelentkezések létre `CREATE LOGIN ... FROM WINDOWS` szintaxis használata nem támogatott.
- A példányt hozott létre az Azure Active Directory (Azure AD) felhasználó [teljes rendszergazdai jogosultságokkal](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#unrestricted-administrative-accounts).
- Rendszergazdai jogokkal nem rendelkező Azure Active Directory (Azure AD) adatbázis-felhasználók segítségével hozhatók létre `CREATE USER ... FROM EXTERNAL PROVIDER` szintaxist. Lásd: [a felhasználó létrehozása... A KÜLSŐ SZOLGÁLTATÓ](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users)
 
### <a name="polybase"></a>PolyBase

Külső táblákra hivatkozó HDFS vagy Azure blob Storage tárolóban lévő fájlok nem támogatottak. A Polybase kapcsolatos információkért lásd: [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikáció 
 
Replikáció jelenleg nem támogatott. A replikációval kapcsolatos információkért lásd: [SQL Server-replikáció](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication).
 
### <a name="restore-statement"></a>A RESTORE utasítás 
 
- Támogatott szintaxis  
   - `RESTORE DATABASE` 
   - `RESTORE FILELISTONLY ONLY` 
   - `RESTORE HEADER ONLY` 
   - `RESTORE LABELONLY ONLY` 
   - `RESTORE VERIFYONLY ONLY` 
- Nem támogatott szintaxis 
   - `RESTORE LOG ONLY` 
   - `RESTORE REWINDONLY ONLY`
- Forrás  
 - `FROM URL` (Az azure blob-tároló) beállítás csak támogatott.
 - `FROM DISK`/`TAPE`/ biztonsági mentési eszköz nem támogatott.
 - Biztonsági mentés nem támogatottak. 
- `WITH` beállítások nem támogatottak (nem `DIFFERENTIAL`, `STATS`stb.)     
- `ASYNC RESTORE` -Visszaállítási továbbra is fennáll, akkor is, ha az ügyfél kapcsolata megszakad. Ha a kapcsolat megszakad, ellenőrizheti a `sys.dm_operation_status` nézet a visszaállítási művelet állapotának (valamint a létrehozása és az vetett adatbázis). Lásd: [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  
 
A következő adatbázis-beállítások set/felül, és később már nem módosítható:  
- `NEW_BROKER` (ha broker nem engedélyezett a .bak-fájl)  
- `ENABLE_BROKER` (ha broker nem engedélyezett a .bak-fájl)  
- `AUTO_CLOSE=OFF` (Ha a .bak-fájl egy adatbázis `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (Ha a .bak-fájl egy adatbázis `SIMPLE` vagy `BULK_LOGGED` helyreállítási módban)
- Memóriaoptimalizált fájlcsoport felvett és XTP meghívni, ha a forrás .bak-fájl nem volt  
- Bármely létező memóriaoptimalizált Fájlcsoport neve módosult az XTP  
- `SINGLE_USER` és `RESTRICTED_USER` beállítások alakulnak `MULTI_USER`   
Korlátozások vonatkoznak:  
- `.BAK` több biztonsági mentés tartalmazó fájlok nem állíthatók vissza. 
- `.BAK` több naplófájlokat tartalmazó fájlok nem állíthatók vissza. 
- Visszaállítás sikertelen lesz, ha a .bak tartalmaz `FILESTREAM` adatokat.
- Jelenleg biztonsági mentés tartalmazó adatbázisok esetén, amelyek aktív memórián belüli objektumok nem állítható vissza.  
- Jelenleg tartalmazó adatbázisok, ahol egy bizonyos ponton memórián belüli objektumok már létezett a biztonsági mentés nem állítható vissza.   
- Jelenleg csak olvasható módú adatbázisok tartalmazó biztonsági mentés nem állítható vissza. Ez a korlátozás hamarosan törlődni fognak.   
 
További információ a Restore utasítás: [RESTORE utasítás](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker 
 
- Kereszt-példány service broker nem támogatott. 
 - `sys.routes` -Előfeltétel: a sys.routes cím kiválasztásához. Címnek kell lennie a helyi minden útvonalon. Lásd: [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
 - `CREATE ROUTE` – nem lehet `CREATE ROUTE` rendelkező `ADDRESS` eltérő `LOCAL`. Lásd: [létrehozás ÚTVONAL](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
 - `ALTER ROUTE` nem lehet `ALTER ROUTE` rendelkező `ADDRESS` eltérő `LOCAL`. Lásd: [ALTER ÚTVONAL](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  
 
### <a name="service-key-and-service-master-key"></a>Szolgáltatás és a szolgáltatás főkulcsának 
 
- [Minta kulcs biztonsági mentése](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által kezelt) 
- [A főkulcs visszaállítási](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által kezelt) 
- [Szolgáltatás főkulcsának biztonsági mentése](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által kezelt) 
- [Szolgáltatás főkulcsának visszaállítása](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által kezelt) 
 
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
- `Extended stored procedures` nem támogatja, beleértve a `sp_addextendedproc` és `sp_dropextendedproc`. Lásd: [bővített tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- `sp_attach_db`, `sp_attach_single_file_db`, és `sp_detach_db` nem támogatottak. Lásd: [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), és [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).
- `sp_renamedb` nem támogatott. Lásd: [sp_renamedb](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-renamedb-transact-sql).

### <a name="sql-server-agent"></a>SQL Server Agent 
 
- Az SQL Agent-beállítások csak olvashatók. Az eljárás `sp_set_agent_properties` felügyelt példány nem támogatott.  
- Feladatok - csak a T-SQL projekt lépéseket jelenleg támogatott (további lépések megkapja nyilvános előzetes).
 - SSIS jelenleg nem támogatott. 
 - Replikáció jelenleg nem támogatott  
  - Replikálásitranzakciónapló-olvasó jelenleg nem támogatott.  
  - Pillanatkép jelenleg nem támogatott.  
  - Terjesztőnél jelenleg nem támogatott.  
  - Egyesítés nem támogatott.  
  - Várólista-olvasó nem támogatott.  
 - Parancs-rendszerhéj jelenleg nem támogatott. 
  - Felügyelt példány nem fér hozzá a külső erőforrások (például hálózati megosztásokon keresztül robocopy).  
 - PowerShell jelenleg nem támogatott.
 - Analysis Services nem támogatja.  
- Értesítések részben támogatottak.
 - E-mailben értesítést támogatott, az adatbázisbeli levelezés profil beállítását igényli. Nem határozható meg csak egy adatbázisbeli levelezési profil és kell meghívni `AzureManagedInstance_dbmail_profile` nyilvános előzetes verziójában (átmeneti korlátozás).  
 - Személyhívó nem támogatott.  
 - NetSend nem támogatott. 
 - Riasztások nem még nem támogatottak.
 - Proxyk nem támogatottak.  
- Az eseménynaplóban nem támogatott. 
 
Az alábbi szolgáltatások jelenleg nem támogatottak, de a jövőben engedélyezve:  
- Proxyk
- Processzor üresjárati feladatok ütemezése 
- Ügynök engedélyezése vagy tiltása
- Riasztások

További információ az SQL Server Agent: [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).
 
### <a name="tables"></a>Táblák 

A következők nem támogatottak: 
- `FILESTREAM` 
- `FILETABLE` 
- `EXTERNAL TABLE` 
- `MEMORY_OPTIMIZED`  

További információ a létrehozásának és módosításának táblák: [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) és [az ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).
 
## <a name="Changes"></a> Viselkedésváltozások 
 
A következő változók, funkciók és a nézetek különböző eredményeket:  
- `SERVERPROPERTY('EngineEdition')` értéket ad vissza értéket 8. Ez a tulajdonság felügyelt példány egyedi azonosítására szolgál. Lásd: [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` a rövid példány nevét, például "myserver" adja vissza. Lásd: [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` értéket ad vissza "összekapcsolható" szolgáltatásnév, például saját felügyelt instance.wcus17662feb9ce98.database.windows.net. Lásd: [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -adja vissza "összekapcsolható" DNS-nevét, például a teljes `myinstance.domain.database.windows.net` Tulajdonságok "name" és "data_source". Lásd: [SYS. KISZOLGÁLÓK](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql). 
- `@@SERVERNAME` értéket ad vissza "összekapcsolható" DNS-nevét, például a teljes `my-managed-instance.wcus17662feb9ce98.database.windows.net`. Lásd: [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -adja vissza "összekapcsolható" DNS-nevét, például a teljes `myinstance.domain.database.windows.net` Tulajdonságok "name" és "data_source". Lásd: [SYS. KISZOLGÁLÓK](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql). 
- `@@SERVICENAME` NULL, értéket ad vissza, mert nincs értelme példány felügyelt környezetben. Lásd: [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).   
- `SUSER_ID` támogatott. NULL értéket ad vissza, ha AAD bejelentkezési sys.syslogins kívül esik. Lásd: [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` nem támogatott. Beolvasása helytelen adatokat (ideiglenes ismert probléma). Lásd: [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 
- `GETDATE()` és más beépített dátum/idő funkciók mindig adja vissza időpontja UTC időzóna. Lásd: [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Ismert problémák és korlátozások

### <a name="tempdb-size"></a>A TEMPDB mérete

`tempdb` 12 oszlik minden rendelkező fájlok maximális mérete 14 GB fájlonként. A maximális méretet a fájl nem módosítható, és új fájlok nem vehető fel `tempdb`. Ez a korlátozás hamarosan törlődni fognak. Néhány lekérdezést hibát előfordulhat, hogy vissza, ha szükségük van a 168 GB-nál több `tempdb`.

### <a name="exceeding-storage-space-with-small-database-files"></a>Kis adatbázisfájlok meghaladó tárhelyen

Minden felügyelt példány mentése 35 TB lefoglalt tárhely, és minden adatbázisfájlt kezdetben el van helyezve 128 GB tárhely foglalási egység. A sok kisméretű fájlok adatbázisok túllépő összesen 35 TB 128 GB-os egységekben lévő előfordulhat, hogy elhelyezni. Ebben az esetben új adatbázisok nem hozható létre vagy visszaállításra, még akkor is, ha az összes adatbázis teljes mérete nem éri el a példány méretkorlátot. A visszaadott hiba ebben az esetben nem feltétlenül törölje a jelet.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>SAS-kulcs helytelen konfigurációja során adatbázis visszaállítása

`RESTORE DATABASE` amely beolvassa a .bak-fájl előfordulhat, hogy lehet folyamatosan újra megpróbálja majd beolvasni .bak-fájl- és visszatérési hiba hosszú idő után ha a közös hozzáférésű Jogosultságkód `CREDENTIAL` helytelen. RESTORE HEADERONLY végrehajtása előtt győződjön meg arról, hogy helyesek-e a SAS-kulcs egy adatbázis visszaállításához.
Győződjön meg arról, hogy távolítsa el a bevezető `?` az Azure-portálon létrehozott SAS-kulcsot.

### <a name="tooling"></a>Tooling eszköz

SQL Server Management Studio és az SQL Server Data Tools bizonyos problémák állhatnak felügyelt példány elérése közben. Minden tooling probléma általánosan rendelkezésre álló fog figyelembe venni.

### <a name="incorrect-database-names"></a>Helytelen adatbázis neve

Felügyelt példány előfordulhat, hogy megjelenítése adatbázis neve helyett a guid azonosítót, vagy bizonyos hibaüzenetek a visszaállítás során. Ezek a problémák kijavítjuk általánosan rendelkezésre álló előtt.

### <a name="database-mail-profile"></a>Adatbázisbeli levelezési profil
Nem határozható meg csak egy adatbázisbeli levelezési profil és kell meghívni `AzureManagedInstance_dbmail_profile`. Ez az egy átmeneti korlátozás, amely hamarosan törlődni fognak.

## <a name="next-steps"></a>További lépések

- Felügyelt példány kapcsolatos részletekért lásd: [Mi az, hogy a felügyelt példánya?](sql-database-managed-instance.md)
- A szolgáltatások és összehasonlító listáját lásd: [általános SQL-szolgáltatások](sql-database-features.md).
- Az oktatóanyagok esetén lásd: [hozzon létre egy felügyelt példányt](sql-database-managed-instance-tutorial-portal.md).
