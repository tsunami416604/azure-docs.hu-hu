---
title: Felügyelt példány T-SQL-eltérései
description: Ez a cikk az Azure SQL Database és az SQL Server felügyelt példányai közötti T-SQL-különbségeket ismerteti
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: e01c61ca4f415ffbb46c86034d4b7441bc2617d9
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365494"
---
# <a name="managed-instance-t-sql-differences-and-limitations"></a>Felügyelt példány T-SQL-különbségei és korlátai

Ez a cikk összefoglalja és ismerteti az Azure SQL Database felügyelt példánya és a helyszíni SQL Server Database Engine szintaxisa és viselkedése közötti különbségeket. A felügyelt példányok központi telepítése nagy fokú kompatibilitást biztosít a helyszíni SQL Server Database Engine rendszerrel. Az SQL Server adatbázis-motor legtöbb szolgáltatása támogatott egy felügyelt példányban.

![Migrálás](./media/sql-database-managed-instance/migration.png)

Vannak paaS-korlátozások, amelyek a felügyelt példányban vannak bevezetve, és néhány viselkedésbeli változás az SQL Server kiszolgálóhoz képest. A különbségek a következő kategóriákba sorolhatók:<a name="Differences"></a>

- [A rendelkezésre állás](#availability) tartalmazza a [Mindig rendelkezésre állási csoportok](#always-on-availability-groups) és biztonsági mentések közötti [különbségeket.](#backup)
- [A biztonság](#security) magában foglalja a [naplózás](#auditing), [a tanúsítványok](#certificates), a hitelesítő adatok , a [kriptográfiai szolgáltatók](#cryptographic-providers), a bejelentkezések és a felhasználók , valamint a szolgáltatáskulcs és a szolgáltatás [főkulcsának](#service-key-and-service-master-key)különbségeit . [credentials](#credential) [logins and users](#logins-and-users)
- [A konfiguráció](#configuration) magában foglalja a [pufferkészlet kiterjesztésének](#buffer-pool-extension), [az illesztésnek,](#collation)a [kompatibilitási szinteknek](#compatibility-levels), [az adatbázis-tükrözésnek](#database-mirroring), [az adatbázis-beállításoknak](#database-options), az [SQL Server Agentnek](#sql-server-agent)és a [táblabeállításoknak a különbségeit.](#tables)
- [A funkciók](#functionalities) közé tartozik [a BULK INSERT/OPENROWSET,](#bulk-insert--openrowset) [CLR,](#clr) [DBCC,](#dbcc) [elosztott tranzakciók,](#distributed-transactions) [kiterjesztett események,](#extended-events) [külső könyvtárak,](#external-libraries) [filestream és FileTable,](#filestream-and-filetable) [teljes szöveges szemantikai keresés,](#full-text-semantic-search) [csatolt kiszolgálók,](#linked-servers) [PolyBase](#polybase), [Replikáció](#replication), [RESTORE](#restore-statement), [Service Broker,](#service-broker) [tárolt eljárások, függvények és eseményindítók.](#stored-procedures-functions-and-triggers)
- [Környezeti beállítások,](#Environment) például virtuális hálózatok és alhálózati konfigurációk.

A legtöbb ilyen szolgáltatás architekturális kényszerek, és szolgáltatásfunkciókat képviselnek.

A felügyelt példányban felfedezett és a jövőben megoldandó ideiglenes ismert problémákat a [kiadási megjegyzések lap](sql-database-release-notes.md)ismerteti.

## <a name="availability"></a>Rendelkezésre állás

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Mindig a rendelkezésre állási csoportok

[A magas rendelkezésre állás](sql-database-high-availability.md) a felügyelt példányba van beépítve, és a felhasználók nem vezérelhetik. A következő utasítások nem támogatottak:

- [VÉGPONT LÉTREHOZÁSA ... A DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT LÉTREHOZÁSA](/sql/t-sql/statements/create-availability-group-transact-sql)
- [A RENDELKEZÉSRE ÁLLÁSI CSOPORT MÓDOSÍTÁSA](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT KIDOBÁSA](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Az [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) utasítás [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) záradéka

### <a name="backup"></a>Backup

A felügyelt példányok automatikus biztonsági másolatokkal `COPY_ONLY` rendelkeznek, így a felhasználók teljes adatbázis-biztonsági mentést készíthetnek. A különbözeti, a napló- és a fájlpillanatkép biztonsági mentésenem támogatott.

- Felügyelt példány esetén csak egy Azure Blob-tárfiókba mentheti a példányok adatbázisát:
  - Csak `BACKUP TO URL` támogatott.
  - `FILE`, `TAPE`és a biztonsági mentési eszközök nem támogatottak.
- Az általános `WITH` beállítások többsége támogatott.
  - `COPY_ONLY`kötelező.
  - `FILE_SNAPSHOT`nem támogatott.
  - Szalagos `REWIND`beállítások: `UNLOAD`, `NOUNLOAD` `NOREWIND`, , és nem támogatottak.
  - Naplóspecifikus beállítások: `NORECOVERY` `STANDBY`, `NO_TRUNCATE` , és nem támogatottak.

Korlátozások: 

- Felügyelt példány esetén biztonsági másolatot készíthet egy példányadatbázisról egy legfeljebb 32 csíkot tartalmazó biztonsági másolatba, ami biztonsági mentési tömörítés esetén legfeljebb 4 TB-os adatbázisok számára elegendő.
- Nem hajtható `BACKUP DATABASE ... WITH COPY_ONLY` végre olyan adatbázison, amely szolgáltatás által felügyelt transzparens adattitkosítással (TDE) van titkosítva. A szolgáltatás által felügyelt TDE kényszeríti a biztonsági mentéseket egy belső TDE-kulccsal. A kulcs nem exportálható, így nem lehet visszaállítani a biztonsági mentést. Használjon automatikus biztonsági mentéseket és időponthoz való visszavisszaállítást, vagy használja [inkább az ügyfél által felügyelt (BYOK) TDE-t.](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) Az adatbázis titkosítását is letilthatja.
- A felügyelt példányban a `BACKUP` parancs használatával a biztonsági mentési csík maximális mérete 195 GB, amely a maximális blobméret. Növelje a sávok számát a biztonsági mentési parancsban az egyes sávok méretének csökkentése és e korláton belül maradva.

    > [!TIP]
    > A korlátozás megkerüléséhez, ha helyszíni környezetben vagy virtuális gépen biztonsági másolatot ad egy adatbázisról az SQL Server kiszolgálóról, a következőket teheti:
    >
    > - Biztonsági másolat `DISK` készítése a `URL`biztonsági mentés helyett a hoz.
    > - Töltse fel a biztonsági másolat fájljait a Blob storage-ba.
    > - Visszaállítás a felügyelt példányba.
    >
    > A `Restore` felügyelt példányban lévő parancs támogatja a biztonsági másolat fájlok nagyobb blobméretét, mert egy másik blobtípust használnak a feltöltött biztonsági másolat fájlok tárolására.

A T-SQL használatával készült biztonsági mentésekről a [BACKUP című témakörben talál](/sql/t-sql/statements/backup-transact-sql)további információt.

## <a name="security"></a>Biztonság

### <a name="auditing"></a>Naplózás

Az Azure SQL Database és az SQL Server adatbázisainak naplózása közötti legfontosabb különbségek a következők:

- A felügyelt példány üzembe helyezését lehetőség az Azure SQL Database, naplózás működik a kiszolgáló szintjén. A `.xel` naplófájlok az Azure Blob storage-ban tárolódnak.
- Az egyetlen adatbázis és a rugalmas készlet telepítési lehetőségek az Azure SQL Database, naplózás működik az adatbázis szintjén.
- A helyszíni SQL Server vagy a virtuális gépek en a naplózás a kiszolgáló szintjén működik. Az eseményeket a fájlrendszer vagy a Windows eseménynaplója tárolja.
 
XEvent naplózás a felügyelt példány támogatja az Azure Blob storage-célokat. A fájl- és Windows-naplók nem támogatottak.

Az Azure Blob `CREATE AUDIT` storage naplózásának főbb különbségei a következők:

- Egy új `TO URL` szintaxis, amely segítségével megadhatja az Azure Blob storage-tároló, ahol a `.xel` fájlok kerülnek url-címét.
- A szintaxis `TO FILE` nem támogatott, mert egy felügyelt példány nem fér hozzá a Windows fájlmegosztásokhoz.

További információkért lásd: 

- [KISZOLGÁLÓNAPLÓZÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER KISZOLGÁLÓ NAPLÓZÁSA](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Naplózás](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Tanúsítványok

A felügyelt példányok nem férnek hozzá a fájlmegosztásokhoz és a Windows-mappákhoz, ezért a következő korlátozások érvényesek:

- `CREATE FROM` / A `BACKUP TO` fájl nem támogatott a tanúsítványok esetében.
- `CREATE` / A `BACKUP` `FILE` tanúsítvány / `ASSEMBLY` nem támogatott. A személyes kulcsfájlok nem használhatók. 

Lásd: [TANÚSÍTVÁNY-](/sql/t-sql/statements/create-certificate-transact-sql) és [BIZTONSÁGITANÚSÍTVÁNY LÉTREHOZÁSA](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Megoldás:** Ahelyett, hogy biztonsági másolatot készítene a tanúsítványról és visszaállítana a biztonsági másolatot, [beszerezne a tanúsítvány bináris tartalmát és személyes kulcsát, tárolhassa .sql fájlként, és bináris fájlból hozza létre:](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Hitelesítő adat

Csak az Azure `SHARED ACCESS SIGNATURE` Key Vault és identitások támogatottak. A Windows-felhasználók nem támogatottak.

Lásd: [HITELESÍTŐ ADATOK LÉTREHOZÁSA](/sql/t-sql/statements/create-credential-transact-sql) ÉS A HITELESÍTŐ ADATOK [MÓDOSÍTÁSA](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Kriptográfiai szolgáltatók

A felügyelt példányok nem férnek hozzá a fájlokhoz, ezért a kriptográfiai szolgáltatók nem hozhatók létre:

- `CREATE CRYPTOGRAPHIC PROVIDER`nem támogatott. Lásd: [Kriptográfiai szolgáltató létrehozása](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER`nem támogatott. Lásd: [Alter Cryptographic provider](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Bejelentkezések és felhasználók

- A program `FROM CERTIFICATE`malajával `FROM ASYMMETRIC KEY`létrehozott `FROM SID` SQL-bejelentkezések támogatottak. Lásd: [BEJELENTKEZÉS LÉTREHOZÁSA](/sql/t-sql/statements/create-login-transact-sql).
- Az Azure Active Directory (Azure AD) kiszolgálóinak egyszerű felhasználói (bejelentkezési címkét) a [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) szintaxissal vagy a [CREATE USER FROM LOGIN [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) szintaxissal létrehozott kiszolgálói kontitúra támogatottak. Ezek a bejelentkezések a kiszolgáló szintjén jönnek létre.

    A felügyelt példány támogatja az Azure AD-adatbázis-tagokat a szintaxissal. `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` Ez a funkció azure AD-ben található adatbázis-felhasználók néven is ismert.

- A `CREATE LOGIN ... FROM WINDOWS` szintaxissal létrehozott Windows-bejelentkezések nem támogatottak. Használja az Azure Active Directory bejelentkezések és a felhasználók.
- A példányt létrehozó Azure AD-felhasználó [korlátlan rendszergazdai jogosultságokkal rendelkezik.](sql-database-manage-logins.md)
- Nem rendszergazdai Azure AD adatbázis-szintű felhasználók `CREATE USER ... FROM EXTERNAL PROVIDER` a szintaxis használatával hozhatók létre. Lásd: [Felhasználó létrehozása ... KÜLSŐ SZOLGÁLTATÓTÓL](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
- Az Azure AD-kiszolgálói egyszerű (bejelentkezési adatok) csak egy felügyelt példányon belül támogatják az SQL-szolgáltatásokat. A több példányok közötti interakciót igénylő funkciók, függetlenül attól, hogy ugyanabban az Azure AD-bérlőben vagy különböző bérlőkön belül vannak, nem támogatottak az Azure AD-felhasználók számára. Ilyen jellemzők például a következők:

  - SQL tranzakciós replikáció.
  - Link szerver.

- Az Azure AD-csoporthoz rendelt Azure AD-bejelentkezés beállítása nem támogatott.
- Az Azure AD-kiszolgálószintű rendszertagok megszemélyesítése más Azure AD-rendszertagok használatával támogatott, például az [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) záradék. A EXECUTE AS-korlátozások a következők:

  - EXECUTE AS USER nem támogatott az Azure AD-felhasználók számára, ha a név eltér a bejelentkezési nevet. Ilyen például az, amikor a felhasználó create user [myAadUser]john@contoso.comFROM LOGIN [ ] szintinaxissal jön létre, és a megszemélyesítést az EXEC AS USER = _myAadUser_. Amikor egy Azure AD-kiszolgáló egyszerű felhasználójából (bejelentkezés) hoz létre **egy USERt,** adja meg a user_name ugyanazzal a login_name a **LOGIN-ból.**
  - Csak az SQL Server-szintű egyszerű staféták `sysadmin` (bejelentkezések), amelyek részét képezik a szerepkör futtathatja a következő műveleteket, amelyek az Azure AD-rendszerbiztonsági tagok:

    - VÉGREHAJTÁS FELHASZNÁLÓKÉNT
    - VÉGREHAJTÁS BEJELENTKEZÉSKÉNT

- A bacpac-fájlokat használó adatbázis-exportálás/-importálás az [SSMS V18.4-es vagy újabb](/sql/ssms/download-sql-server-management-studio-ssms)vagy [az SQLPackage.exe](/sql/tools/sqlpackage-download)fájlokkal felügyelt példányban támogatja az Azure AD-felhasználók számára.
  - A következő konfigurációk támogatottak az adatbázis bacpac fájljával: 
    - Adatbázis exportálása/importálása az azonos Azure AD-tartományon belüli különböző kezelési példányok között.
    - Exportáljon egy adatbázist a felügyelt példányból, és importáljon az SQL-adatbázisba ugyanazon az Azure AD-tartományon belül. 
    - Exportáljon egy adatbázist az SQL Database-ből, és importálja a felügyelt példányugyanabban az Azure AD-tartományban.
    - Exportáljon egy adatbázist a felügyelt példányból, és importáljon az SQL Server kiszolgálóra (2012-es vagy újabb verzióba).
      - Ebben a konfigurációban az összes Azure AD-felhasználó sql-adatbázis-egyszerű felhasználóként (felhasználóként) jön létre bejelentkezések nélkül. A felhasználók típusa SQL-ként jelenik meg (a sys.database_principals fájlban SQL_USER). Engedélyeik és szerepköreik az SQL Server adatbázis metaadataiban maradnak, és megszemélyesítésre használhatók. Az SQL Server hitelesítő adataival való hozzáféréshez és bejelentkezéshez azonban nem használhatók.

- Csak a kiszolgálószintű egyszerű bejelentkezés, amely a felügyelt példány létesítési folyamat által `securityadmin` létrehozott, a kiszolgálói szerepkörök tagjai, például vagy `sysadmin`, vagy más bejelentkezések ALTER bármely LOGIN-engedéllyel a kiszolgáló szintjén hozhat létre Azure AD-kiszolgáló isznek (bejelentkezési adatok) a felügyelt példány fő adatbázisában.
- Ha a bejelentkezés i. egyszerű, csak a `sysadmin` szerepkör részét használó bejelentkezések a create paranccsal hozhat létre bejelentkezéseket egy Azure AD-fiókhoz.
- Az Azure AD bejelentkezési tagjának kell lennie egy Azure AD ugyanabban a könyvtárban, amely az Azure SQL Database felügyelt példány.
- Az Azure AD-kiszolgálóinak egyszerű (bejelentkezési adatok) az SQL Server Management Studio 18.0 5-ös verziójával kezdődő objektumkezelőben láthatók.
- Egymást átfedő Azure AD-kiszolgálói egyszerű (bejelentkezési) egy Azure AD-rendszergazdai fiókkal engedélyezett. Az Azure AD-kiszolgálói nak (bejelentkezési adatoknak) az Azure AD-rendszergazdával szemben elsőbbséget élveznek, amikor feloldja a fő tagházirendet, és engedélyeket alkalmaz a felügyelt példányra.
- A hitelesítés során a következő sorrendet alkalmazza a rendszer a hitelesítő tag feloldásához:

    1. Ha az Azure AD-fiók létezik, közvetlenül az Azure AD-kiszolgáló egyszerű (bejelentkezés), amely jelen van a sys.server_principals típus "E", hozzáférési engedélyt, és alkalmazza az engedélyeket az Azure AD-kiszolgáló egyszerű (bejelentkezés).
    2. Ha az Azure AD-fiók egy Azure AD-csoport tagja, amely le van képezve az Azure AD-kiszolgáló egyszerű (bejelentkezés), amely jelen van a sys.server_principals típus "X", adjon hozzáférést, és alkalmazza az Azure AD-csoport bejelentkezési engedélyeit.
    3. Ha az Azure AD-fiók egy speciális, portáláltal konfigurált Azure AD-rendszergazda felügyelt példányhoz, amely nem létezik a felügyelt példány rendszernézeteiben, alkalmazzon speciális rögzített engedélyeket az Azure AD-rendszergazda felügyelt példányra (örökölt módban).
    4. Ha az Azure AD-fiók létezik, közvetlenül leképezve egy Azure AD-felhasználó egy adatbázisban, amely jelen van a sys.database_principals típus "E", hozzáférési engedélyt, és alkalmazza az Azure AD-adatbázis-felhasználó engedélyeit.
    5. Ha az Azure AD-fiók egy Azure AD-csoport tagja, amely egy Azure AD-felhasználó egy adatbázisban, amely jelen van a sys.database_principals típus "X", adjon hozzáférést, és alkalmazza az Azure AD-csoport bejelentkezési engedélyeit.
    6. Ha van egy Azure AD-bejelentkezési egy Azure AD felhasználói fiókhoz vagy egy Azure AD-csoportfiókhoz van rendelve, amely a hitelesítő felhasználónak szól, az Azure AD-bejelentkezésösszes engedélye lesz alkalmazva.

### <a name="service-key-and-service-master-key"></a>Szolgáltatáskulcs és szolgáltatásfőkulcs

- [A főkulcs biztonsági mentése](/sql/t-sql/statements/backup-master-key-transact-sql) nem támogatott (az SQL Database szolgáltatás kezeli).
- [A főkulcs-visszaállítás](/sql/t-sql/statements/restore-master-key-transact-sql) nem támogatott (az SQL Database szolgáltatás kezeli).
- [A szolgáltatásfőkulcs biztonsági mentése](/sql/t-sql/statements/backup-service-master-key-transact-sql) nem támogatott (az SQL Database szolgáltatás kezeli).
- [A szolgáltatásfőkulcs-visszaállítás](/sql/t-sql/statements/restore-service-master-key-transact-sql) nem támogatott (az SQL Database szolgáltatás kezeli).

## <a name="configuration"></a>Konfiguráció

### <a name="buffer-pool-extension"></a>Pufferkészlet-bővítmény

- [A pufferkészlet-bővítmény](/sql/database-engine/configure-windows/buffer-pool-extension) nem támogatott.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`nem támogatott. Lásd: [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Rendezés

Az alapértelmezett példányrendezés `SQL_Latin1_General_CP1_CI_AS` létrehozási paraméterként adható meg, és megadható. Lásd: [Rendezések](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Kompatibilitási szintek

- A támogatott kompatibilitási szintek a következők: 100, 110, 120, 130, 140 és 150.
- A 100 alatti kompatibilitási szintek nem támogatottak.
- Az új adatbázisok alapértelmezett kompatibilitási szintje 140. A visszaállított adatbázisok esetében a kompatibilitási szint változatlan marad, ha 100 vagy annál magasabb volt.

Lásd: [ALTER ADATBÁZIS-kompatibilitási szint](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Adatbázis-tükrözés

Az adatbázis-tükrözés nem támogatott.

- `ALTER DATABASE SET PARTNER`és `SET WITNESS` a beállítások nem támogatottak.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`nem támogatott.

További információ: [ALTER DATABASE SET PARTNER and SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) and CREATE [ENDPOINT ... A DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Adatbázis beállításai

- Több naplófájl nem támogatott.
- A memórián belüli objektumok nem támogatottak az általános célú szolgáltatási szint. 
- Általános célú példányonként legfeljebb 280 fájl lehet, ami adatbázisonként legfeljebb 280 fájlt jelent. Az általános célú rétegben lévő adatok és naplófájlok is beleszámítanak ebbe a korlátba. [Az üzleti legkritikusabb szint adatbázisonként 32 767 fájlt támogat.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)
- Az adatbázis nem tartalmazhat fájlfolyam-adatokat tartalmazó fájlcsoportokat. A visszaállítás sikertelen, `FILESTREAM` ha a .bak adatokat tartalmaz. 
- Minden fájl az Azure Blob storage-ba kerül. Az IO és az átviteli-cím fájlonként az egyes fájlok méretétől függ.

#### <a name="create-database-statement"></a>CREATE ADATBÁZIS utasítás

A következő korlátozások `CREATE DATABASE`vonatkoznak:

- A fájlok és fájlcsoportok nem definiálhatók. 
- A `CONTAINMENT` beállítás nem támogatott. 
- `WITH`beállítások nem támogatottak. 
   > [!TIP]
   > Kerülő megoldásként `ALTER DATABASE` az `CREATE DATABASE` utána adja meg az adatbázis beállításait fájlok hozzáadásához vagy elszigeteléshez. 

- A `FOR ATTACH` beállítás nem támogatott.
- A `AS SNAPSHOT OF` beállítás nem támogatott.

További információt az [ADATBÁZIS LÉTREHOZÁSA](/sql/t-sql/statements/create-database-sql-server-transact-sql)című témakörben talál.

#### <a name="alter-database-statement"></a>ALTER ADATBÁZIS utasítás

Egyes fájltulajdonságok nem állíthatók be és nem módosíthatók:

- A Fájl elérési útja nem adható `ALTER DATABASE ADD FILE (FILENAME='path')` meg a T-SQL utasításban. Eltávolítás `FILENAME` a parancsfájlból, mert egy felügyelt példány automatikusan elhelyezi a fájlokat. 
- A fájlnév nem módosítható az `ALTER DATABASE` utasítás használatával.

Alapértelmezés szerint a következő beállítások vannak beállítva, és nem módosíthatók:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

A következő beállítások nem módosíthatók:

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

További információ: [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Az SQL Server Agent engedélyezése és letiltása jelenleg nem támogatott a felügyelt példányban. Az SQL-ügynök mindig fut.
- Az SQL Server Agent beállításai írásvédettek. Az `sp_set_agent_properties` eljárás nem támogatott felügyelt példányban. 
- Feladatok
  - A T-SQL feladat lépései támogatottak.
  - A következő replikációs feladatok támogatottak:
    - Tranzakciónapló-olvasó
    - Pillanatkép
    - Forgalmazó
  - Az SSIS-feladatlépései támogatottak.
  - A feladatlépések egyéb típusai jelenleg nem támogatottak:
    - Az egyesítési replikációs feladat lépése nem támogatott. 
    - A várólista-olvasó nem támogatott. 
    - A parancshéj még nem támogatott.
  - A felügyelt példányok nem férhetnek hozzá a külső erőforrásokhoz, például a hálózati megosztásokhoz robotmásolaton keresztül. 
  - Az SQL Server Analysis Services nem támogatott.
- Az értesítések részben támogatottak.
- Az e-mailes értesítés támogatott, bár ehhez adatbázis-mail profilt kell konfigurálnia. Az SQL Server Agent csak egy Adatbázis-levelezési `AzureManagedInstance_dbmail_profile`profilt használhat, ezért meg kell hívni. 
  - A személyhívó nem támogatott.
  - A NetSend nem támogatott.
  - A riasztások még nem támogatottak.
  - A proxyk nem támogatottak.
- Az EventLog nem támogatott.

A következő SQL-ügynök-szolgáltatások jelenleg nem támogatottak:

- Legközelebbi
- Feladatok ütemezése tétlen processzoron
- Ügynök engedélyezése vagy letiltása
- Riasztások

Az SQL Server Agent szolgáltatásról az [SQL Server Agent című témakörben](/sql/ssms/agent/sql-server-agent)talál további információt.

### <a name="tables"></a>Táblák

Az alábbi táblázattípusok nem támogatottak:

- [FÁJLFOLYAM](/sql/relational-databases/blob/filestream-sql-server)
- [FÁJLTÁBLÁZAT](/sql/relational-databases/blob/filetables-sql-server)
- [KÜLSŐ ASZTAL](/sql/t-sql/statements/create-external-table-transact-sql) (Polibázis)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (nem csak az általános célú szinttámogatja)

A táblák létrehozásáról és módosításáról a [TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-table-transact-sql) és az ALTER TÁBLA [CÍMŰ témakörben talál](/sql/t-sql/statements/alter-table-transact-sql)további információt.

## <a name="functionalities"></a>Funkciók

### <a name="bulk-insert--openrowset"></a>Ömlesztett lapka / OPENROWSET

Egy felügyelt példány nem tud hozzáférni a fájlmegosztásokhoz és a Windows-mappákhoz, ezért a fájlokat az Azure Blob storage-ból kell importálni:

- `DATASOURCE`az Azure `BULK INSERT` Blob storage-ból származó fájlok importálása közben szükséges a parancsban. Lásd: [TÖMEGES BESZÚRÁS](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`a `OPENROWSET` függvényben van szükség, amikor egy fájl tartalmát olvassa az Azure Blob storage-ból. Lásd [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET`más Azure SQL egyetlen adatbázisokból, felügyelt példányokból vagy SQL Server-példányokból származó adatok olvasására használható. Más források, például oracle adatbázisok vagy Excel-fájlok nem támogatottak.

### <a name="clr"></a>Clr

A felügyelt példányok nem férnek hozzá a fájlmegosztásokhoz és a Windows-mappákhoz, ezért a következő korlátozások érvényesek:

- Csak `CREATE ASSEMBLY FROM BINARY` támogatott. Lásd: [ASSEM BLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE`nem támogatott. Lásd: [ÖSSZEÁLLÍTÁS FÁJLBÓL LÉTREHOZÁSA](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY`nem lehet fájlokra hivatkozni. Lásd [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Adatbázis-levelezés (db_mail)
 - `sp_send_dbmail`paraméterrel @file_attachments nem lehet mellékleteket küldeni. A helyi fájlrendszer és a külső megosztások vagy az Azure Blob Storage nem érhetők el ebből az eljárásból.
 - Tekintse meg a `@query` paraméterrel és a hitelesítéssel kapcsolatos ismert problémákat.
 
### <a name="dbcc"></a>Dbcc

Az SQL Server kiszolgálón engedélyezett nem dokumentált DBCC-utasítások nem támogatottak a felügyelt példányokban.

- Csak korlátozott számú globális nyomkövetési jelző támogatott. A munkamenet-szint `Trace flags` nem támogatott. Lásd: [Nyomkövetési jelzők](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [A DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) és a [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) korlátozott számú globális nyomkövetési jelzővel működik.
- [DbCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) a REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST és REPAIR_REBUILD kapcsolókkal nem `SINGLE_USER` használható, mert az adatbázis nem állítható be módban - lásd [ALTER DATABASE differences](#alter-database-statement). A lehetséges adatbázis-sérüléseket az Azure támogatási csapata kezeli. Lépjen kapcsolatba az Azure-támogatással, ha olyan adatbázis-sérülésről van szó, amelyet ki kell javítani.

### <a name="distributed-transactions"></a>Elosztott tranzakciók

AZ MSDTC és [a rugalmas tranzakciók](sql-database-elastic-transactions-overview.md) jelenleg nem támogatottak a felügyelt példányokban.

### <a name="extended-events"></a>Bővített események

A kiterjesztett események (XEvents) egyes Windows-specifikus céljai nem támogatottak:

- A `etw_classic_sync` cél nem támogatott. Fájlokat tárolhat `.xel` az Azure Blob storage-ban. Lásd [etw_classic_sync célt](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- A `event_file` cél nem támogatott. Fájlokat tárolhat `.xel` az Azure Blob storage-ban. Lásd [event_file célt](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Külső kódtárak

Az adatbázison belüli R és Python, a külső kódtárak még nem támogatottak. Lásd: [SQL Server Machine Learning Services](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Filestream és FileTable

- A fájlfolyam-adatok nem támogatottak.
- Az adatbázis nem tartalmazhat adatokat tartalmazó `FILESTREAM` fájlcsoportokat.
- `FILETABLE`nem támogatott.
- A tábláknak `FILESTREAM` nem lehetnek típusai.
- A következő függvények nem támogatottak:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

További információt a [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) és [a FileTables című témakörben talál.](/sql/relational-databases/blob/filetables-sql-server)

### <a name="full-text-semantic-search"></a>Teljes szöveges szemantikai keresés

[A szemantikai keresés](/sql/relational-databases/search/semantic-search-sql-server) nem támogatott.

### <a name="linked-servers"></a>Társított kiszolgálók

A felügyelt példányok összekapcsolt kiszolgálói korlátozott számú célt támogatnak:

- A támogatott példányok a felügyelt példányok, az egységes adatbázisok és az SQL Server-példányok. 
- A csatolt kiszolgálók nem támogatják az elosztott írható tranzakciókat (MS DTC).
- A nem támogatott célok a fájlok, az Analysis Services és más RDBMS. Próbálja meg használni a natív CSV-importálást az Azure Blob Storage-ból a fájlimportálás használatával `BULK INSERT` vagy `OPENROWSET` alternatívaként.

Műveletek

- A keresztpéldányok írási tranzakciói nem támogatottak.
- `sp_dropserver`a csatolt kiszolgáló eldobása támogatott. Lásd [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- A `OPENROWSET` függvény csak SQL Server-példányokon futtathat lekérdezéseket. Ezek lehetnek felügyelt, helyszíni, vagy a virtuális gépeken. Lásd [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- A `OPENDATASOURCE` függvény csak SQL Server-példányokon futtathat lekérdezéseket. Ezek lehetnek felügyelt, helyszíni, vagy a virtuális gépeken. Szolgáltatóként `SQLNCLI` `SQLNCLI11`csak `SQLOLEDB` a , és az értékek támogatottak. Például: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Lásd [OpenDataSOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- A csatolt kiszolgálók nem használhatók fájlok (Excel, CSV) hálózati megosztásokból történő olvasására. Próbálja meg használni bulk insert vagy [OPENROWSET,](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) amely beolvassa a CSV-fájlokat az Azure Blob Storage.Try to use [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) or OPENROWSET that reads CSV files from Azure Blob Storage. A kérelmek nyomon követése a [felügyelt példány visszajelzési elemén](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

A HDFS-ben vagy az Azure Blob storage-ban lévő fájlokra hivatkozó külső táblák nem támogatottak. A PolyBase-ről további információt a [PolyBase című](/sql/relational-databases/polybase/polybase-guide)témakörben talál.

### <a name="replication"></a>Replikáció

- A pillanatkép- és kétirányú replikációtípusok támogatottak. A replikáció, a társközi replikáció és a updatable előfizetések egyesítése nem támogatott.
- [A tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md) bizonyos megkötésekkel rendelkező felügyelt példányon érhető el nyilvános előzetes verzióhoz:
    - A replikációs résztvevők (kiadó, forgalmazó, lekéréses előfizető és lekéréses előfizető) minden típusa elhelyezhető felügyelt példányokon, de a közzétevőnek és a forgalmazónak a felhőben vagy a helyszínen lévőnek kell lennie.
    - A felügyelt példányok kommunikálhatnak az SQL Server legújabb verzióival. További információt a [támogatott verziómátrixban](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems) talál.
    - A tranzakciós replikációnak [további hálózati követelményei vannak.](sql-database-managed-instance-transactional-replication.md#requirements)

A tranzakciós replikáció konfigurálásáról az alábbi útmutatókban talál további információt:
- [Replikáció a hibajelző közzétevője és előfizetője között](replication-with-sql-database-managed-instance.md)
- [Replikáció a hibajelző-közzétevő, a hibajelző-forgalmazó és az SQL Server-előfizető konkretárja között](sql-database-managed-instance-configure-replication-tutorial.md)

### <a name="restore-statement"></a>RESTORE utasítás 

- Támogatott szintaxis:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nem támogatott szintaxis:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Forrás: 
  - `FROM URL`(Azure Blob storage) az egyetlen támogatott lehetőség.
  - `FROM DISK`/`TAPE`A /backup eszköz nem támogatott.
  - A biztonsági másolatkészlet nem támogatott.
- `WITH`beállítások nem támogatottak, `DIFFERENTIAL` például nem vagy `STATS`.
- `ASYNC RESTORE`: A visszaállítás akkor is folytatódik, ha az ügyfélkapcsolat megszakad. Ha a kapcsolat megszakad, ellenőrizheti a `sys.dm_operation_status` nézetben a visszaállítási művelet állapotát, valamint a CREATE és DROP adatbázist. Lásd [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

A következő adatbázis-beállítások vannak beállítva vagy felülbírálva, és később nem módosíthatók: 

- `NEW_BROKER`ha a közvetítő nincs engedélyezve a .bak fájlban. 
- `ENABLE_BROKER`ha a közvetítő nincs engedélyezve a .bak fájlban. 
- `AUTO_CLOSE=OFF`ha a .bak fájlban `AUTO_CLOSE=ON`lévő adatbázis . 
- `RECOVERY FULL`ha a .bak fájlban `SIMPLE` `BULK_LOGGED` lévő adatbázis vagy helyreállítási mód dala van.
- A rendszer hozzáad egy memóriaoptimalizált fájlcsoportot, amelyet XTP-nek hív, ha az nem szerepel a forrás .bak fájlban. 
- Minden meglévő, memóriaoptimalizált fájlcsoportot xtp-re nevezünk át. 
- `SINGLE_USER`és `RESTRICTED_USER` a beállítások `MULTI_USER`at konvertálják .

Korlátozások: 

- Előfordulhat, hogy a sérült adatbázisok biztonsági másolatai a sérülés típusától függően visszaállíthatók, de az automatikus biztonsági mentések nem kerülnek rögzítésre, amíg a sérülés nem történik meg. Győződjön meg `DBCC CHECKDB` arról, hogy fut `WITH CHECKSUM` a forráspéldány, és használja a biztonsági mentés, hogy megakadályozzák ezt a problémát.
- A `.BAK` dokumentumban leírt korlátozásokat (például `FILESTREAM` objektumokat) `FILETABLE` tartalmazó adatbázis fájljának visszaállítása nem állítható vissza a felügyelt példányon.
- `.BAK`a több biztonsági másolat-készletet tartalmazó fájlok nem állíthatók vissza. 
- `.BAK`a több naplófájlt tartalmazó fájlok nem állíthatók vissza.
- A 8 TB-nál nagyobb adatbázisokat, a memóriában lévő aktív OLTP-objektumokat vagy a példányonként 280-at meghaladó fájlok számát tartalmazó biztonsági mentések nem állíthatók vissza általános célú példányon. 
- A 4 TB-nál nagyobb adatbázisokat vagy a memóriában lévő OLTP-objektumokat tartalmazó biztonsági mentések, amelyek teljes mérete nagyobb az [erőforráskorlátokban](sql-database-managed-instance-resource-limits.md) leírt méretnél, nem állíthatók vissza az üzleti legkritikusabb példányon.
A visszaállítási utasításokról a RESTORE utasítások című [témakörben](/sql/t-sql/statements/restore-statements-transact-sql)talál további információt.

 > [!IMPORTANT]
 > Ugyanezek a korlátozások vonatkoznak a beépített időpontban visszavisszaállítási műveletre is. Például a 4 TB-nál nagyobb általános célú adatbázis nem állítható vissza az üzleti legkritikusabb példányon. Üzleti kritikus adatbázis a memóriában OLTP fájlokat, vagy több mint 280 fájlokat nem lehet visszaállítani az általános célú példány.

### <a name="service-broker"></a>Szolgáltató bróker

A többpéldányos szolgáltatásközvetítő nem támogatott:

- `sys.routes`: Előfeltételként ki kell választania a címet a sys.routes listából. A címnek minden útvonalon HELYInek kell lennie. Lásd [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Nem használható `CREATE ROUTE` más, `ADDRESS` `LOCAL`mint a. Lásd: [ÚTVONAL LÉTREHOZÁSA](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Nem használható `ALTER ROUTE` más, `ADDRESS` `LOCAL`mint a. Lásd: [ÚTVONAL MÓDOSÍTÁSA](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Tárolt eljárások, funkciók és eseményindítók

- `NATIVE_COMPILATION`nem támogatott az általános célú rétegben.
- A következő [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) beállítások nem támogatottak: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`nem támogatott. Lásd [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell`nem támogatott. Lásd [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`nem támogatottak, `sp_addextendedproc`  amely `sp_dropextendedproc`magában foglalja a és a. Lásd: [Kiterjesztett tárolt eljárások](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db`és `sp_detach_db` nem támogatottak. Lásd [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)és [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Rendszerfüggvények és -változók

A következő változók, függvények és nézetek eltérő eredményeket adnak eredményül:

- `SERVERPROPERTY('EngineEdition')`a 8 értéket adja vissza. Ez a tulajdonság egyedileg azonosítja a felügyelt példányt. Lásd [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`NULL értéket ad vissza, mert az SQL Server példánya nem vonatkozik a felügyelt példányokra. Lásd: [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`teljes DNS "csatlakoztatható" nevet ad vissza, például my-managed-instance.wcus17662feb9ce98.database.windows.net. Lásd [@SERVERNAME: @](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`teljes DNS "csatlakoztatható" nevet ad vissza, például `myinstance.domain.database.windows.net` a "név" és a "data_source" tulajdonságokhoz. Nézd meg [SYS-t. KISZOLGÁLÓKAT.](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)
- `@@SERVICENAME`NULL értéket ad vissza, mert az SQL Server szolgáltatás ának fogalma nem vonatkozik egy felügyelt példányra. Lásd [@SERVICENAME: @](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID`támogatott. Null értéket ad vissza, ha az Azure AD bejelentkezésnem a sys.syslogins. Lásd [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID`nem támogatott. A rendszer nem a megfelelő adatokat adja vissza, ami átmenetileg ismert probléma. Lásd [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Környezeti megkötések

### <a name="subnet"></a>Alhálózat
-  Más erőforrásokat (például virtuális gépeket) nem helyezhet el abban az alhálózatban, ahol a felügyelt példányt telepítette. Telepítse ezeket az erőforrásokat egy másik alhálózat használatával.
- Az alhálózatnak elegendő számú rendelkezésre álló [IP-címmel kell rendelkeznie.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) A minimum 16, míg a javaslat az, hogy legalább 32 IP-címet az alhálózatban.
- [A szolgáltatásvégpontok nem társíthatók a felügyelt példány alhálózatához.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) Győződjön meg arról, hogy a szolgáltatásvégpontok beállítás le van tiltva a virtuális hálózat létrehozásakor.
- A virtuális magok és a régiókban telepíthető példánytípusok száma bizonyos [korlátokkal és korlátokkal rendelkezik.](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)
- Vannak olyan [biztonsági szabályok, amelyeket alkalmazni kell az alhálózaton.](sql-database-managed-instance-connectivity-architecture.md#network-requirements)

### <a name="vnet"></a>Net
- Virtuális hálózat üzembe helyezhető erőforrás modell használatával - Klasszikus modell virtuális hálózat nem támogatott.
- Felügyelt példány létrehozása után a felügyelt példány vagy virtuális hálózat áthelyezése egy másik erőforráscsoportba vagy előfizetésbe nem támogatott.
- Egyes szolgáltatások, például az App Service-környezetek, a logikai alkalmazások és a felügyelt példányok (georeplikációra, tranzakciós replikációra vagy csatolt kiszolgálókon keresztül) nem férnek hozzá a különböző régiókban lévő felügyelt példányokhoz, ha a virtuális hálózatok [globális társviszony-létesítéssel](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)csatlakoznak. Ezekhez az erőforrásokhoz az ExpressRoute vagy a VNet-vNet virtuális hálózatok on keresztül virtuális hálózatok keresztül csatlakozhat.

### <a name="tempdb"></a>TEMPDB

A maximális fájlméret `tempdb` nem lehet nagyobb, mint 24 GB/core egy általános célú rétegen. Az `tempdb` üzleti legkritikusabb szint maximális méretét a példány tárolási mérete korlátozza. `Tempdb`a naplófájl mérete az Általános célú rétegen 120 GB-ra korlátozódik. Egyes lekérdezések hiba visszaadása, ha magonként `tempdb` több mint 24 GB-ra van szükségük, vagy ha 120 GB-nál több naplóadatot állítanak elő.

### <a name="msdb"></a>MSDB

A felügyelt példányban a következő MSDB sémáknak a megfelelő előre definiált szerepkörök tulajdonában kell lenniük:

- Általános szerepek
  - TargetServersRole
- [Rögzített adatbázis-szerepkörök](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail szerepkörök](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - DatabaseMailUserRole
- [Integrációs szolgáltatások szerepei](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Az előre definiált szerepkörnevek, sémanevek és sématulajdonosok ügyfelek általi módosítása hatással lesz a szolgáltatás normál működésére. Az ezeken végrehajtott módosítások at az észlelt, vagy legkésőbb a következő szolgáltatásfrissítéskor visszaállítjuk az előre meghatározott értékekre, hogy biztosítsuk a normál szolgáltatásműködést.

### <a name="error-logs"></a>Hibanaplók

A felügyelt példány részletes adatokat helyez el a hibanaplókban. A hibanaplóban számos belső rendszeresemény van naplózva. Egyéni eljárással olyan hibanaplókat olvashat, amelyek kiszűrik a nem releváns bejegyzéseket. További információ: [felügyelt példány – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) vagy [felügyelt példánybővítmény(előzetes verzió)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) az Azure Data Studio számára.

## <a name="next-steps"></a>További lépések

- A felügyelt példányokról a [Mi a felügyelt példány?](sql-database-managed-instance.md)
- A szolgáltatások és az összehasonlító lista az [Azure SQL Database szolgáltatás összehasonlítása](sql-database-features.md)című témakörben található.
- A kiadási frissítéseket és az ismert problémák állapotát az [SQL Database kiadási megjegyzések című témaköre tartalmazza.](sql-database-release-notes.md)
- Az új felügyelt példány okainak létrehozásáról a [Felügyelt példány létrehozása című](sql-database-managed-instance-get-started.md)témakörben van.
