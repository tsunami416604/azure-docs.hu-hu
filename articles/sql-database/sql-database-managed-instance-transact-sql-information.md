---
title: Felügyelt példányok – T-SQL eltérések
description: Ez a cikk a Azure SQL Database és SQL Server felügyelt példányai közötti T-SQL-különbségeket ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: 636fd5fd17838c729cdbc9e2a322c1f991d93948
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186437"
---
# <a name="managed-instance-t-sql-differences-limitations-and-known-issues"></a>Felügyelt példányok – T-SQL-különbségek, korlátozások és ismert problémák

Ez a cikk összefoglalja és ismerteti a Azure SQL Database felügyelt példány és a helyszíni SQL Server adatbázismotor közötti szintaxissal és viselkedéssel kapcsolatos különbségeket. A felügyelt példány központi telepítésének lehetősége magas kompatibilitást biztosít a helyszíni SQL Server adatbázis-motorral. A SQL Server adatbázis-működtető funkcióinak többsége felügyelt példányok esetén támogatott.

![Migrálás](./media/sql-database-managed-instance/migration.png)

Néhány, a felügyelt példányban bevezetett és néhány viselkedési változás a SQL Serverhoz képest. A különbségek a következő kategóriákba vannak osztva:<a name="Differences"></a>

- A [rendelkezésre állás](#availability) magában foglalja a [mindig](#always-on-availability) és a [biztonsági mentések](#backup)közötti különbségeket.
- A [Biztonság](#security) magában foglalja a [naplózás](#auditing), [a tanúsítványok](#certificates), a [hitelesítő adatok](#credential), a [kriptográfiai szolgáltatók](#cryptographic-providers), a [bejelentkezések és a felhasználók](#logins-and-users), valamint a [szolgáltatás kulcsa és a szolgáltatás főkulcsa](#service-key-and-service-master-key)közötti különbségeket.
- A [konfiguráció](#configuration) magában foglalja a [puffer-bővítmények](#buffer-pool-extension), a [Rendezés](#collation), a [kompatibilitási szintek](#compatibility-levels), az [adatbázis-tükrözés](#database-mirroring), az adatbázis- [Beállítások](#database-options), a [SQL Server Agent](#sql-server-agent)és a [tábla beállításainak](#tables)különbségeit.
- A [funkciók](#functionalities) közé tartozik a [bulk INSERT/OpenRowset](#bulk-insert--openrowset), a [CLR](#clr), a [DBCC](#dbcc), az [Elosztott tranzakciók](#distributed-transactions), a [bővített események](#extended-events), a [külső kódtárak](#external-libraries), a [FileStream és](#filestream-and-filetable)a lefoglalható, a [teljes szöveges Szemantikai keresés](#full-text-semantic-search), [csatolt kiszolgálók](#linked-servers), [alapszintű](#polybase), [replikálás](#replication), [visszaállítás](#restore-statement), [Service Broker](#service-broker), [tárolt eljárások, függvények és eseményindítók](#stored-procedures-functions-and-triggers).
- [Környezeti beállítások](#Environment) , például virtuális hálózatok és alhálózati konfigurációk.

Ezeknek a funkcióknak a többsége építészeti korlátozás, és a szolgáltatás funkcióit képviseli.

Ezen a lapon a felügyelt példányban felderített [ideiglenes ismert problémák](#Issues) is megtalálhatók, amelyeket később megoldják a rendszer.

## <a name="availability"></a>Rendelkezésre állás

### <a name="always-on-availability"></a>Always on

A [magas rendelkezésre állás](sql-database-high-availability.md) a felügyelt példányba van beépítve, és a felhasználók nem vezérelhetők. A következő utasítások nem támogatottak:

- [VÉGPONT LÉTREHOZÁSA... DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT LÉTREHOZÁSA](/sql/t-sql/statements/create-availability-group-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT MÓDOSÍTÁSA](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT ELDOBÁSA](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql) utasítás [set HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) záradéka

### <a name="backup"></a>Biztonsági mentés

A felügyelt példányok automatikus biztonsági mentéssel rendelkeznek, így a felhasználók létrehozhatnak teljes adatbázis-`COPY_ONLY` biztonsági mentést. A különbözeti, a napló és a fájl pillanatképének biztonsági mentése nem támogatott.

- Felügyelt példány esetén a példány-adatbázis biztonsági mentését csak Azure Blob Storage-fiókba végezheti el:
  - Csak `BACKUP TO URL` támogatott.
  - a `FILE`, a `TAPE`és a biztonsági mentési eszközök nem támogatottak.
- Az általános `WITH` lehetőségek többsége támogatott.
  - a `COPY_ONLY` megadása kötelező.
  - a `FILE_SNAPSHOT` nem támogatott.
  - Szalagos beállítások: `REWIND`, `NOREWIND`, `UNLOAD`és `NOUNLOAD` nem támogatott.
  - A naplózásra jellemző beállítások: a `NORECOVERY`, a `STANDBY`és a `NO_TRUNCATE` nem támogatott.

Korlátozások 

- Felügyelt példány esetén biztonsági mentést készíthet egy példány-adatbázisról olyan biztonsági másolatra, amelyen akár 32 sáv is található, ami elegendő a 4 TB-ig terjedő adatbázisokhoz, ha biztonsági mentési tömörítést használ.
- Nem hajtható végre `BACKUP DATABASE ... WITH COPY_ONLY` a szolgáltatás által felügyelt transzparens adattitkosítás (TDE) titkosított adatbázison. A szolgáltatás által felügyelt TDE a biztonsági mentések belső TDE-kulccsal lesznek titkosítva. A kulcs nem exportálható, így a biztonsági mentés nem állítható vissza. Használjon automatikus biztonsági mentést és időponthoz való visszaállítást, vagy használja helyette az [ügyfél által felügyelt (BYOK) TDE](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) . Emellett letilthatja a titkosítást az adatbázison.
- A biztonsági mentési sávok maximális mérete a felügyelt példány `BACKUP` parancsának használatával 195 GB, amely a blob maximális mérete. Növelje meg a sávok számát a Backup parancsban, hogy csökkentse az egyes sávok méretét, és a korláton belül maradjon.

    > [!TIP]
    > Ha ezt a korlátozást szeretné megkerülni, amikor egy helyszíni környezetben vagy egy virtuális gépen SQL Server egy adatbázisról készít biztonsági másolatot, a következőkre van lehetősége:
    >
    > - `DISK` biztonsági mentést készíthet a `URL`re való biztonsági mentés helyett.
    > - Töltse fel a biztonságimásolat-fájlokat a blob Storage-ba.
    > - Állítsa vissza a felügyelt példányt.
    >
    > A felügyelt példány `Restore` parancsa támogatja a nagyobb blob-méreteket a biztonságimásolat-fájlokban, mert egy másik blob-típust használ a feltöltött biztonságimásolat-fájlok tárolásához.

A T-SQL használatával történő biztonsági mentéssel kapcsolatos információkért lásd: [biztonsági mentés](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Biztonság

### <a name="auditing"></a>Naplózás

A Azure SQL Database és a SQL Server adatbázisaiban található adatbázisok naplózása közötti fő különbségek a következők:

- A Azure SQL Database felügyelt példányok központi telepítésének beállításával a naplózás a kiszolgáló szintjén működik. Az `.xel` naplófájlok tárolása az Azure Blob Storage-ban történik.
- A Azure SQL Database az önálló adatbázis és a rugalmas készlet üzembe helyezési lehetőségeivel a naplózás az adatbázis szintjén működik.
- SQL Server helyszíni vagy virtuális gépeken a naplózás a kiszolgáló szintjén működik. Az eseményeket fájlrendszer vagy Windows-eseménynaplók tárolják.
 
A felügyelt példány XEvent-naplózása támogatja az Azure Blob Storage-célokat. A fájl-és Windows-naplók nem támogatottak.

Az Azure Blob Storage-ba való naplózás `CREATE AUDIT` szintaxisának főbb eltérései a következők:

- A rendszer új szintaxist `TO URL` biztosít, amellyel megadhatja annak az Azure Blob Storage-tárolónak az URL-címét, amelyben a `.xel` fájlok vannak elhelyezve.
- A `TO FILE` szintaxis nem támogatott, mert a felügyelt példányok nem férnek hozzá a Windows-fájlmegosztás számára.

További információkért lásd: 

- [KISZOLGÁLÓ NAPLÓZÁSÁNAK LÉTREHOZÁSA](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Naplózás](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Tanúsítványok

A felügyelt példányok nem férnek hozzá a fájlmegosztás és a Windows-mappák számára, így a következő korlátozások érvényesek:

- A `CREATE FROM`/`BACKUP TO` fájl nem támogatott tanúsítványokhoz.
- A `CREATE`/`BACKUP` tanúsítvány `FILE`/`ASSEMBLY` nem támogatott. A titkos kulcs fájljai nem használhatók. 

Lásd: tanúsítvány és [biztonsági mentési tanúsítvány](/sql/t-sql/statements/backup-certificate-transact-sql) [létrehozása](/sql/t-sql/statements/create-certificate-transact-sql) . 
 
**Áthidaló megoldás**: a tanúsítvány biztonsági másolatának létrehozása és a biztonsági mentés visszaállítása helyett [szerezze be a tanúsítvány bináris tartalmát és titkos kulcsát, tárolja. SQL-fájlként, és hozzon létre a bináris fájlból](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Hitelesítő adat

Csak Azure Key Vault és `SHARED ACCESS SIGNATURE` identitás támogatott. A Windows-felhasználók nem támogatottak.

Lásd: [hitelesítő adatok létrehozása](/sql/t-sql/statements/create-credential-transact-sql) és a [hitelesítő adatok módosítása](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Kriptográfiai szolgáltatók

A felügyelt példányok nem férhetnek hozzá a fájlokhoz, így a titkosítási szolgáltatók nem hozhatók létre:

- a `CREATE CRYPTOGRAPHIC PROVIDER` nem támogatott. Lásd: [titkosítási szolgáltató létrehozása](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- a `ALTER CRYPTOGRAPHIC PROVIDER` nem támogatott. Lásd: a [kriptográfiai szolgáltató módosítása](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Bejelentkezések és felhasználók

- A `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`és `FROM SID` használatával létrehozott SQL-bejelentkezések támogatottak. Lásd: [create login (bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql)).
- A [bejelentkezési szintaxissal](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) létrehozott Azure Active Directory (Azure ad) kiszolgálói rendszerbiztonsági tag (bejelentkezések) vagy a [felhasználó létrehozása a bejelentkezéshez [Azure ad login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) szintaxis támogatott. Ezek a bejelentkezések a kiszolgálói szinten jönnek létre.

    A felügyelt példány a `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`szintaxissal támogatja az Azure AD-adatbázis rendszerbiztonsági tagait. Ez a szolgáltatás az Azure AD-ben tárolt adatbázis-felhasználók néven is ismert.

- A `CREATE LOGIN ... FROM WINDOWS` szintaxissal létrehozott Windows-bejelentkezések nem támogatottak. Azure Active Directory bejelentkezések és felhasználók használata.
- Az Azure AD-felhasználó, aki létrehozta a példányt, [korlátlan rendszergazdai jogosultságokkal](sql-database-manage-logins.md#unrestricted-administrative-accounts)rendelkezik.
- A nem rendszergazda Azure AD adatbázis-szintű felhasználók a `CREATE USER ... FROM EXTERNAL PROVIDER` szintaxis használatával hozhatók létre. Lásd: [felhasználó létrehozása... KÜLSŐ SZOLGÁLTATÓtól](sql-database-manage-logins.md#non-administrator-users).
- Az Azure AD-kiszolgáló résztvevői (bejelentkezések) csak egy felügyelt példányon belül támogatják az SQL-funkciókat. Azok a funkciók, amelyek eltérő interakciót igényelnek, függetlenül attól, hogy ugyanazon az Azure AD-bérlőn vagy más bérlőn belül vannak, nem támogatottak az Azure AD-felhasználók számára. Ilyen funkciók például a következők:

  - SQL tranzakciós replikáció.
  - Kiszolgáló csatolása.

- Egy Azure ad-csoportra leképezett Azure AD-bejelentkezés beállítása, mivel az adatbázis tulajdonosa nem támogatott.
- Az Azure AD-kiszolgáló rendszerbiztonsági tagjainak más Azure AD-rendszerbiztonsági tag használatával történő megszemélyesítése támogatott, például a [Execute as](/sql/t-sql/statements/execute-as-transact-sql) záradékkal. A végrehajtás korlátozásként:

  - Az Azure AD-felhasználók nem támogatják a (z) rendszerbeli VÉGREHAJTÁSt, ha a név eltér a bejelentkezési névvel. Ilyen eset például, ha a felhasználó a következő szintaxissal jön létre: CREATE USER [myAadUser] a LOGIN [john@contoso.com] és a megszemélyesítés a EXEC AS USER = _myAadUser_. Amikor létrehoz egy **felhasználót** egy Azure ad-kiszolgálói rendszerbiztonsági tag (login) alapján, a **bejelentkezéshez**ugyanazt a login_name kell megadnia a user_name.
  - Csak a `sysadmin` szerepkör részét képező SQL Server szintű rendszerbiztonsági tag (login) hajthatja végre a következő műveleteket, amelyek az Azure AD-rendszerrésztvevőket célozzák meg:

    - VÉGREHAJTÁS FELHASZNÁLÓKÉNT
    - VÉGREHAJTÁS BEJELENTKEZÉSKÉNT

- A bacpac-fájlok használatával történő adatbázis-Exportálás/Importálás támogatott a felügyelt példányban lévő Azure AD-felhasználók számára a [SSMS v 18.4 vagy újabb](/sql/ssms/download-sql-server-management-studio-ssms), vagy a [SQLPackage. exe](/sql/tools/sqlpackage-download)használatával.
  - Az adatbázis-bacpac fájl a következő konfigurációkat támogatja: 
    - Adatbázis exportálása/importálása ugyanazon Azure AD-tartományban lévő különböző felügyeleti példányok között.
    - Exportáljon egy adatbázist a felügyelt példányból, és importálja SQL Databaseba ugyanazon az Azure AD-tartományon belül. 
    - Exportáljon egy adatbázist a SQL Databaseból, és importálja a felügyelt példányba ugyanazon az Azure AD-tartományon belül.
    - Exportáljon egy adatbázist a felügyelt példányból, és importálja SQL Serverre (2012-es vagy újabb verzióra).
      - Ebben a konfigurációban az összes Azure AD-felhasználó a bejelentkezések nélkül SQL Database rendszerbiztonsági tagként (Users) jön létre. A felhasználók típusa SQL (a sys. database_principals) SQL_USER jelenik meg. Engedélyeik és szerepköreik a SQL Server adatbázis-metaadatokban maradnak, és megszemélyesítésre is használhatók. Azonban nem használhatók a SQL Server való hozzáférésre és bejelentkezésre a hitelesítő adataik használatával.

- Az Azure AD-kiszolgálót csak a felügyelt példány létesítési folyamata által létrehozott kiszolgálói szintű rendszerbiztonsági tag (például `securityadmin` vagy `sysadmin`, illetve más, a bejelentkezési engedélyekkel rendelkező bejelentkezések megváltoztatására jogosult bejelentkezések) tagjai hozhatnak létre. a felügyelt példány főadatbázisában lévő rendszerbiztonsági tag (Logins).
- Ha a bejelentkezés egy SQL-rendszerbiztonsági tag, csak a `sysadmin` szerepkör részét képező bejelentkezések használhatók a Create paranccsal az Azure AD-fiókhoz tartozó bejelentkezések létrehozásához.
- Az Azure AD-bejelentkezésnek egy Azure AD-tagnak kell lennie, amely a Azure SQL Database felügyelt példányához használt könyvtárban található.
- Az Azure AD-kiszolgáló résztvevői (bejelentkezések) a Object Explorer SQL Server Management Studio 18,0 Preview 5 verziótól kezdődően láthatók.
- Az egymást átfedő Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések) Azure AD-rendszergazdai fiókkal való használata engedélyezett. Az Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések) elsőbbséget élveznek az Azure AD-rendszergazdával szemben a rendszerbiztonsági tag feloldásakor és az engedélyek a felügyelt példányra való alkalmazásakor.
- A hitelesítés során a rendszer a következő sorozatot alkalmazza a hitelesítő tag feloldására:

    1. Ha az Azure AD-fiók létezik közvetlenül leképezve az Azure AD-kiszolgáló rendszerbiztonsági tagja (login) számára, amely megtalálható a sys-ben. server_principals az "E" típussal, adja meg a hozzáférést, és alkalmazza az Azure AD-kiszolgáló rendszerbiztonsági tag (login) engedélyeit.
    2. Ha az Azure AD-fiók tagja egy olyan Azure ad-csoportnak, amely az Azure AD-kiszolgáló rendszerbiztonsági tagjához (login) van társítva, amely megtalálható a sys-ben. server_principals X típusúként, adja meg a hozzáférést, és alkalmazza az Azure AD-csoport bejelentkezési engedélyeit.
    3. Ha az Azure AD-fiók egy speciális, felügyelt példányhoz konfigurált Azure AD-rendszergazda, amely nem létezik a felügyelt példányok rendszernézeteiben, alkalmazza az Azure AD-rendszergazda speciális rögzített engedélyeit a felügyelt példányhoz (örökölt mód).
    4. Ha az Azure AD-fiók létezik közvetlenül hozzárendelve egy Azure AD-felhasználóhoz egy adatbázisban, amely szerepel a sys-ben. database_principals az "E" típussal, adjon hozzáférést és alkalmazza az Azure AD-adatbázis felhasználójának engedélyeit.
    5. Ha az Azure AD-fiók tagja egy olyan Azure ad-csoportnak, amely egy Azure AD-felhasználóhoz van hozzárendelve egy adatbázisban, amely szerepel a sys-ben. database_principals "X" típusúként, adja meg a hozzáférést, és alkalmazza az Azure AD-csoport bejelentkezési engedélyeit.
    6. Ha egy Azure ad-bejelentkezés egy Azure AD-felhasználói fiókhoz vagy egy Azure AD-csoport fiókjához van rendelve, amely a hitelesítést végző felhasználóra van feloldva, az Azure AD-bejelentkezés összes engedélyét alkalmazza a rendszer.

### <a name="service-key-and-service-master-key"></a>Szolgáltatási kulcs és szolgáltatás főkulcsa

- A [főkulcs biztonsági mentése](/sql/t-sql/statements/backup-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által felügyelt).
- A [főkulcs visszaállítása](/sql/t-sql/statements/restore-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által felügyelt).
- A [szolgáltatás főkulcsának biztonsági mentése](/sql/t-sql/statements/backup-service-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által felügyelt).
- A [szolgáltatás főkulcsának visszaállítása](/sql/t-sql/statements/restore-service-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által felügyelt).

## <a name="configuration"></a>Konfiguráció

### <a name="buffer-pool-extension"></a>Puffer-készlet kiterjesztése

- A [puffer-készlet kiterjesztése](/sql/database-engine/configure-windows/buffer-pool-extension) nem támogatott.
- a `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` nem támogatott. Lásd: a [kiszolgáló konfigurációjának módosítása](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Rendezés

A példányok alapértelmezett rendezése `SQL_Latin1_General_CP1_CI_AS`, és megadható létrehozási paraméterként. Lásd: [rendezések](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Kompatibilitási szintek

- A támogatott kompatibilitási szintek a következők: 100, 110, 120, 130, 140 és 150.
- Az 100 alatti kompatibilitási szintek nem támogatottak.
- Az új adatbázisok alapértelmezett kompatibilitási szintje 140. A visszaállított adatbázisok esetében a kompatibilitási szint változatlan marad, ha 100-s vagy újabb.

Lásd: az [adatbázis kompatibilitási szintjének módosítása](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Adatbázis-tükrözés

Az adatbázis-tükrözés nem támogatott.

- `ALTER DATABASE SET PARTNER` és `SET WITNESS` lehetőségek nem támogatottak.
- a `CREATE ENDPOINT … FOR DATABASE_MIRRORING` nem támogatott.

További információ: [Alter Database set partner és set tanúsító](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) és [végpont létrehozása... DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Adatbázis-beállítások

- Több naplófájl nem támogatott.
- A memóriában tárolt objektumok nem támogatottak a általános célú szolgáltatási rétegben. 
- A általános célú-példányok száma legfeljebb 280, amely adatbázis-kiszolgálónként legfeljebb 280 fájlt jelent. A általános célú szinten található adatfájlok és naplófájlok is beleszámítanak a korlátba. [Az üzletileg kritikus szinten az adatbázis 32 767-es fájljait támogatja](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Az adatbázis nem tartalmazhat FileStream-adatkészletet tartalmazó fájlcsoportok. A visszaállítás sikertelen, ha a. bak `FILESTREAM`-adatadatot tartalmaz. 
- Minden fájl az Azure Blob Storage-ba kerül. A fájl i/o-és átviteli sebessége az egyes fájlok méretétől függ.

#### <a name="create-database-statement"></a>ADATBÁZIS-utasítás létrehozása

A következő korlátozások vonatkoznak a `CREATE DATABASE`re:

- A fájlok és a Fájlcsoportok nem definiálhatók. 
- A `CONTAINMENT` beállítás nem támogatott. 
- `WITH` beállítások nem támogatottak. 
   > [!TIP]
   > Áthidaló megoldásként használja a `ALTER DATABASE`t a `CREATE DATABASE` után, hogy az adatbázis-beállításokat a fájlok hozzáadásához vagy a tároló beállításához adja meg. 

- A `FOR ATTACH` beállítás nem támogatott.
- A `AS SNAPSHOT OF` beállítás nem támogatott.

További információ: [Create Database (adatbázis létrehozása](/sql/t-sql/statements/create-database-sql-server-transact-sql)).

#### <a name="alter-database-statement"></a>ALTER DATABASE utasítás

Bizonyos fájltulajdonságok nem állíthatók be és nem módosíthatók:

- Nem adható meg a fájl elérési útja a `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL-utasításban. Távolítsa el `FILENAME` a parancsfájlból, mert a felügyelt példányok automatikusan elhelyezik a fájlokat. 
- A fájlnév nem módosítható a `ALTER DATABASE` utasítás használatával.

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

További információ: [Alter Database](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- A SQL Server Agent engedélyezése és letiltása jelenleg nem támogatott a felügyelt példányokban. Az SQL-ügynök mindig fut.
- SQL Server Agent beállítások csak olvashatók. A felügyelt példányon nem támogatott a `sp_set_agent_properties` eljárás. 
- Feladatok
  - A T-SQL-feladatok lépései támogatottak.
  - A következő replikációs feladatok támogatottak:
    - Tranzakciónapló-olvasó
    - Pillanatkép
    - Terjesztő
  - A SSIS-feladatok lépései támogatottak.
  - Más típusú feladatok lépései jelenleg nem támogatottak:
    - Az egyesítéses replikálási feladattípus nem támogatott. 
    - A várólista-olvasó nem támogatott. 
    - A parancs rendszerhéja még nem támogatott.
  - A felügyelt példányok nem férnek hozzá a külső erőforrásokhoz, például hálózati megosztások a Robocopy használatával. 
  - A SQL Server Analysis Services nem támogatottak.
- Az értesítések részben támogatottak.
- Az e-mailes értesítések támogatottak, de a Database Mail profil konfigurálását igényli. SQL Server Agent csak egy Database Mail profilt használhat, ezért `AzureManagedInstance_dbmail_profile`nak kell lennie. 
  - A személyhívó nem támogatott.
  - A NetSend nem támogatott.
  - A riasztások még nem támogatottak.
  - A proxyk nem támogatottak.
- Az Eseménynapló nem támogatott.

A következő SQL Agent-funkciók jelenleg nem támogatottak:

- Legközelebbi
- Feladatok ütemezése üresjárati PROCESSZORon
- Ügynök engedélyezése vagy letiltása
- Riasztások

További információ a SQL Server Agentről: [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Táblák

A következő táblázat típusok nem támogatottak:

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [Külső tábla](/sql/t-sql/statements/create-external-table-transact-sql) (alapszintű)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (csak általános célú szinten támogatott)

A táblázatok létrehozásával és módosításával kapcsolatos további információkért lásd: [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) és [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Functionalities

### <a name="bulk-insert--openrowset"></a>Tömeges Beszúrás/OPENROWSET

A felügyelt példányok nem férnek hozzá a fájlmegosztás és a Windows-mappák eléréséhez, így a fájlokat az Azure Blob Storage-ból kell importálni:

- Az Azure Blob Storage-ból származó fájlok importálásakor `DATASOURCE` szükséges a `BULK INSERT` parancsban. Lásd: [bulk INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- Az Azure Blob Storage-ból származó fájl tartalmának olvasása során `DATASOURCE` szükséges a `OPENROWSET` függvényben. Lásd: [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql).
- a `OPENROWSET` használhatók más Azure SQL-adatbázisokból, felügyelt példányokból vagy SQL Server példányokból származó adatok olvasásához. Más források, például Oracle-adatbázisok vagy Excel-fájlok nem támogatottak.

### <a name="clr"></a>CLR

A felügyelt példányok nem férnek hozzá a fájlmegosztás és a Windows-mappák számára, így a következő korlátozások érvényesek:

- Csak `CREATE ASSEMBLY FROM BINARY` támogatott. Lásd: [az adatforrások létrehozása a bináris fájlból](/sql/t-sql/statements/create-assembly-transact-sql). 
- a `CREATE ASSEMBLY FROM FILE` nem támogatott. Lásd: [szerelvény létrehozása fájlból](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` nem hivatkozhat fájlokat. Lásd: az [Alter Assembly](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail` nem tud mellékleteket küldeni a @file_attachments paraméterrel. A helyi fájlrendszer és a külső megosztások vagy az Azure Blob Storage nem érhetők el ebből az eljárásból.
 - Tekintse meg `@query` paraméterrel és hitelesítéssel kapcsolatos ismert problémákat.
 
### <a name="dbcc"></a>DBCC

A SQL Serverban engedélyezett nem dokumentált DBCC utasítások nem támogatottak a felügyelt példányokban.

- Csak korlátozott számú globális nyomkövetési jelző támogatott. A munkamenet-szintű `Trace flags` nem támogatottak. Lásd: [nyomkövetési jelzők](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- A [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) és a [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) korlátozott számú globális nyomkövetési jelzővel működik.
- A REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST és REPAIR_REBUILD beállításokkal rendelkező [DBCC](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) nem használhatók, mert az adatbázis nem állítható be `SINGLE_USER` módban – lásd: [adatbázis-eltérések módosítása](#alter-database-statement). A lehetséges adatbázis-sérüléseket az Azure-támogatási csapat kezeli. Vegye fel a kapcsolatot az Azure támogatási szolgálatával, ha az adatbázis sérülését rögzíti.

### <a name="distributed-transactions"></a>Elosztott tranzakciók

Az MSDTC és a [rugalmas tranzakciók](sql-database-elastic-transactions-overview.md) jelenleg nem támogatottak a felügyelt példányokban.

### <a name="extended-events"></a>Bővített események

Bizonyos Windows-specifikus célok a kiterjesztett eseményekhez (Xevent típusú eseményekhez) nem támogatottak:

- A `etw_classic_sync` cél nem támogatott. `.xel`-fájlok tárolása az Azure Blob Storage-ban. Lásd: [etw_classic_sync cél](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- A `event_file` cél nem támogatott. `.xel`-fájlok tárolása az Azure Blob Storage-ban. Lásd: [event_file cél](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Külső kódtárak

Adatbázison belüli R és Python esetén a külső könyvtárak még nem támogatottak. Lásd: [SQL Server Machine learning Services](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream és lefilézett

- A FileStream-adatértékek nem támogatottak.
- Az adatbázis nem tartalmazhat `FILESTREAM` adattal rendelkező fájlcsoportok.
- a `FILETABLE` nem támogatott.
- A táblák nem rendelkezhetnek `FILESTREAM` típusokkal.
- A következő függvények nem támogatottak:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

További információ: [FileStream](/sql/relational-databases/blob/filestream-sql-server) és [objektumnak](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Teljes szöveges szemantikai keresés

A [szemantikai keresés](/sql/relational-databases/search/semantic-search-sql-server) nem támogatott.

### <a name="linked-servers"></a>Társított kiszolgálók

A felügyelt példányokban lévő csatolt kiszolgálók korlátozott számú célt támogatnak:

- A támogatott célok a felügyelt példányok, az önálló adatbázisok és a SQL Server példányok. 
- A csatolt kiszolgálók nem támogatják az elosztott írható tranzakciókat (MS DTC).
- A nem támogatott célok a fájlok, a Analysis Services és az egyéb RDBMS. Próbálja meg a natív CSV-importálást használni az Azure-Blob Storage a fájlok importálásakor `BULK INSERT` vagy `OPENROWSET` használatával.

Műveletek

- A példányok közötti írási tranzakciók nem támogatottak.
- a `sp_dropserver` a csatolt kiszolgálók eldobása támogatott. Lásd: [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- A `OPENROWSET` függvényt csak SQL Server példányokon lehet lekérdezéseket végrehajtani. Lehetnek felügyelt, helyszíni vagy virtuális gépek. Lásd: [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql).
- A `OPENDATASOURCE` függvényt csak SQL Server példányokon lehet lekérdezéseket végrehajtani. Lehetnek felügyelt, helyszíni vagy virtuális gépek. Szolgáltatóként csak a `SQLNCLI`, a `SQLNCLI11`és a `SQLOLEDB` értékek támogatottak. Például: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Lásd: [index](/sql/t-sql/functions/opendatasource-transact-sql).
- A csatolt kiszolgálók nem használhatók a hálózati megosztásokból származó fájlok (Excel, CSV) olvasásához. Próbáljon meg olyan [bulk INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) vagy [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) használni, amely CSV-fájlokat olvas be az Azure Blob Storageból. A kérelmek nyomon követése [felügyelt példányok visszajelzési elemének](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

A HDFS vagy az Azure Blob Storage-ban található fájlokra hivatkozó külső táblák nem támogatottak. További információ [a következőről: Base](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikáció

- A pillanatképek és a kétirányú replikációs típusok támogatottak. Az egyesítéses replikáció, a társ-társ replikáció és a frissíthető előfizetések nem támogatottak.
- A [tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md) a felügyelt példány nyilvános előzetes verziójához érhető el, néhány korlátozással:
    - A replikációs résztvevők (kiadó, terjesztő, lekéréses előfizető és leküldéses előfizető) minden típusa felügyelt példányokon helyezhető el, de a közzétevőnek és a terjesztőnek egyaránt a felhőben vagy mindkét helyszínen kell lennie.
    - A felügyelt példányok kommunikálhatnak a SQL Server legújabb verzióival. Tekintse meg a [támogatott verziókat](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems).
    - A tranzakciós replikáció [további hálózati követelményekkel](sql-database-managed-instance-transactional-replication.md#requirements)rendelkezik.

A replikáció konfigurálásával kapcsolatos információkért tekintse meg a [replikálási oktatóanyagot](replication-with-sql-database-managed-instance.md).


Ha a replikáció engedélyezve van egy [feladatátvételi csoportban](sql-database-auto-failover-group.md)lévő adatbázison, a felügyelt példány rendszergazdájának törölnie kell a régi elsődleges összes kiadványt, és újra kell konfigurálnia azokat az új elsődlegesen a feladatátvételt követően. Ebben a forgatókönyvben a következő tevékenységek szükségesek:

1. Állítsa le az adatbázison futó összes replikációs feladatot, ha vannak ilyenek.
2. Az előfizetés metaadatainak eldobása a közzétevőtől a következő parancsfájl futtatásával a közzétevő adatbázisán:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Az előfizetés metaadatainak eldobása az előfizetőtől. Futtassa a következő parancsfájlt az előfizetői példány előfizetési adatbázisában:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Az összes replikációs objektum kényszerített eldobása a közzétevőtől a közzétett adatbázisban a következő parancsfájl futtatásával:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Az eredeti elsődleges példánytól származó régi terjesztő kényszerített eldobása (ha a feladatátvételt egy olyan régi elsődlegesre végzi, amely a terjesztőhöz lett használva). Futtassa a következő parancsfájlt a főadatbázisban a régi terjesztő által felügyelt példányon:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

### <a name="restore-statement"></a>VISSZAÁLLÍTÁSi utasítás 

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
  - a `FROM URL` (Azure Blob Storage) az egyetlen támogatott lehetőség.
  - `FROM DISK`/`TAPE`/Backup-eszköz nem támogatott.
  - A biztonságimásolat-készletek nem támogatottak.
- `WITH` a beállítások nem támogatottak, például nem `DIFFERENTIAL` vagy `STATS`.
- `ASYNC RESTORE`: a visszaállítás akkor is folytatódik, ha az ügyfél-kapcsolatok megszakadnak. Ha a rendszer eldobta a kapcsolatokat, megtekintheti a visszaállítási művelet állapotának `sys.dm_operation_status` nézetét, valamint egy LÉTREHOZÁSi és eldobási adatbázist. Lásd: [sys. dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

A következő adatbázis-beállítások vannak beállítva vagy felülbírálva, és később nem módosíthatók: 

- `NEW_BROKER`, ha a közvetítő nincs engedélyezve a. bak fájlban. 
- `ENABLE_BROKER`, ha a közvetítő nincs engedélyezve a. bak fájlban. 
- `AUTO_CLOSE=OFF`, ha a. bak fájlban található adatbázis `AUTO_CLOSE=ON`. 
- `RECOVERY FULL`, hogy a. bak fájlban lévő egyik adatbázis `SIMPLE` vagy `BULK_LOGGED` helyreállítási módban van-e.
- A memóriára optimalizált fájlcsoportja hozzáadása és neve XTP, ha nem szerepel a forrás. bak fájlban. 
- A rendszer a meglévő memória-optimalizált fájlcsoportja átnevezi a XTP. 
- a `SINGLE_USER` és az `RESTRICTED_USER` beállítások `MULTI_USER`re lesznek konvertálva.

Korlátozások 

- Előfordulhat, hogy a sérült adatbázisok biztonsági másolatait a rendszer a sérülés típusától függően visszaállítja, az automatikus biztonsági mentések azonban nem lesznek elvégezve, amíg a sérülés nem lesz kijavítva. A probléma elkerülése érdekében győződjön meg arról, hogy a forrás példányon `DBCC CHECKDB` futtat, és a biztonsági mentési `WITH CHECKSUM` használja.
- A jelen dokumentumban leírt korlátozásokat tartalmazó adatbázis `.BAK` fájljának visszaállítása (például `FILESTREAM` vagy `FILETABLE` objektum) nem állítható vissza a felügyelt példányon.
- `.BAK` több biztonságimásolat-készletet tartalmazó fájlok nem állíthatók vissza. 
- `.BAK` több naplófájlt tartalmazó fájlok nem állíthatók vissza.
- A 8 TB-nál nagyobb adatbázisok, a memóriában tárolt OLTP-objektumok, illetve a 280-nál több fájlt meghaladó fájlok száma nem állítható vissza általános célú példányon. 
- A 4 TB-nál nagyobb vagy memóriában lévő OLTP-objektumokba tartozó, az [erőforrás-korlátokban](sql-database-managed-instance-resource-limits.md) leírt méretnél nagyobb méretű adatbázisokat tartalmazó biztonsági másolatok nem állíthatók vissza üzletileg kritikus példányon.
További információ a visszaállítási utasításokról: [Restore utasítások](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Ugyanezek a korlátozások érvényesek a beépített időpontra történő visszaállítási műveletre. Például általános célú-adatbázis 4 TB-nál nagyobb mértékben nem állítható vissza üzletileg kritikus példányon. Üzletileg kritikus adatbázis memóriában tárolt OLTP-fájlokkal vagy több mint 280 fájllal nem állítható vissza általános célú példányon.

### <a name="service-broker"></a>Service Broker

A több példányban elérhető Service Broker nem támogatott:

- `sys.routes`: előfeltételként ki kell választania a (z) sys. Routes címeket. A címnek minden útvonalon HELYInek kell lennie. Lásd: [sys. Routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: nem használhatja a (z) `LOCAL`nem használható `ADDRESS` `CREATE ROUTE`. Lásd: [útvonal létrehozása](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: nem használhatja a (z) `LOCAL`nem használható `ADDRESS` `ALTER ROUTE`. Lásd: [útvonal módosítása](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Tárolt eljárások, függvények és eseményindítók

- a `NATIVE_COMPILATION` nem támogatott a általános célú szinten.
- A következő [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) lehetőségek nem támogatottak: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- a `sp_execute_external_scripts` nem támogatott. Lásd: [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- a `xp_cmdshell` nem támogatott. Lásd: [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- a `Extended stored procedures` nem támogatottak, beleértve a `sp_addextendedproc` és `sp_dropextendedproc`. Lásd: [kiterjesztett tárolt eljárások](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- a `sp_attach_db`, a `sp_attach_single_file_db`és a `sp_detach_db` nem támogatott. Lásd: [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)és [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Rendszerfunkciók és változók

A következő változók, függvények és nézetek eltérő eredményeket adnak vissza:

- `SERVERPROPERTY('EngineEdition')` a 8-as értéket adja vissza. Ez a tulajdonság egyedileg azonosítja a felügyelt példányt. Lásd: [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- a `SERVERPROPERTY('InstanceName')` NULL értéket ad vissza, mert az SQL Server esetében a példány fogalma nem vonatkozik felügyelt példányra. Lásd: [SERVERPROPERTY ("példánynév")](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` egy teljes DNS "csatlakoztatható" nevet ad vissza, például my-managed-instance.wcus17662feb9ce98.database.windows.net. Lásd: [@@SERVERNAME](/sql/t-sql/functions/servername-transact-sql). 
- a `SYS.SERVERS` egy teljes DNS "csatlakoztatható" nevet ad vissza, például `myinstance.domain.database.windows.net` a "Name" és a "data_source" tulajdonsághoz. Lásd: [sys. KISZOLGÁLÓK](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- a `@@SERVICENAME` NULL értéket ad vissza, mert az SQL Serverhoz használt koncepció nem vonatkozik felügyelt példányra. Lásd: [@@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql).
- a `SUSER_ID` támogatott. NULL értéket ad vissza, ha az Azure AD-bejelentkezés nem a sys. syslogins. Lásd: [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- a `SUSER_SID` nem támogatott. A rendszer a helytelen adatmennyiséget adja vissza, ami egy ideiglenes ismert probléma. Lásd: [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Environment"></a>Környezeti korlátozások

### <a name="subnet"></a>Alhálózat
-  Nem helyezhet el semmilyen más erőforrást (például virtuális gépeket) abban az alhálózatban, amelyben a felügyelt példányt telepítette. Ezeket az erőforrásokat egy másik alhálózattal telepítse.
- Az alhálózatnak elegendő számú elérhető [IP-címmel](sql-database-managed-instance-connectivity-architecture.md#network-requirements)kell rendelkeznie. A minimum 16, míg a javaslat szerint legalább 32 IP-címmel kell rendelkeznie az alhálózatban.
- [A szolgáltatási végpontok nem társíthatók a felügyelt példány alhálózatához](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Győződjön meg arról, hogy a szolgáltatás-végpontok beállítás le van tiltva a virtuális hálózat létrehozásakor.
- A régiókban üzembe helyezhető virtuális mag és típusok száma bizonyos [korlátozásokkal és korlátozásokkal](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)rendelkezik.
- Vannak olyan [biztonsági szabályok, amelyeket az alhálózaton kell alkalmazni](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>VNET
- A VNet az Resource Model használatával telepíthető – a VNet klasszikus modellje nem támogatott.
- Felügyelt példány létrehozása után a felügyelt példány vagy VNet másik erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott.
- Egyes szolgáltatások, például a App Service környezetek, a Logic apps és a felügyelt példányok (földrajzi replikálás, tranzakciós replikálás vagy csatolt kiszolgálókon keresztül) nem férnek hozzá a felügyelt példányokhoz különböző régiókban, ha a virtuális hálózatok [globálisan vannak csatlakoztatva peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Ezekhez az erőforrásokhoz a ExpressRoute vagy a VNet – VNet használatával csatlakozhat a VNet-átjárók segítségével.

### <a name="tempdb"></a>TEMPDB

Általános célú szinten a `tempdb` maximális fájlmérete nem lehet nagyobb, mint 24 GB. Egy üzletileg kritikus szinten a `tempdb` maximális méretét a példány tárolási mérete korlátozza. `Tempdb` naplófájl mérete általános célú szinten legfeljebb 120 GB lehet. Előfordulhat, hogy egyes lekérdezések hibát jeleznek, ha a `tempdb` Core-nál több mint 24 GB-nál több adatra van szükségük, vagy ha több mint 120 GB adatnaplót hoznak létre.

### <a name="error-logs"></a>Hibanaplók

A felügyelt példányok részletes információkat helyeznek el a hibák naplóiban. A hibanapló számos belső rendszereseményt naplóz. Egyéni eljárással olvashatja el a nem releváns bejegyzéseket kiszűrő hibákat. További információ: [felügyelt példány – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) vagy [felügyelt példányok bővítménye (előzetes verzió)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) Azure Data studiohoz.

## <a name="Issues"></a>Ismert problémák

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>A memóriában tárolt OLTP-korlátok nincsenek alkalmazva

**Dátum:** TOT 2019

Üzletileg kritikus a szolgáltatási réteg nem fogja megfelelően alkalmazni [a memóriára optimalizált objektumok maximális memóriára vonatkozó korlátozásait](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) bizonyos esetekben. A felügyelt példányok lehetővé tehetik, hogy a számítási feladatok nagyobb memóriát használjanak a memóriában tárolt OLTP-műveletekhez, ami hatással lehet a példány rendelkezésre állására és stabilitására. Előfordulhat, hogy a korlátokat elérő memóriabeli OLTP-lekérdezések nem lesznek azonnal sikertelenek. Ezt a problémát hamarosan megjavítja a rendszer. A memóriában lévő OLTP memóriát használó lekérdezések hamarabb sikertelenek lesznek, ha elérik a [határértékeket](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Áthidaló megoldás:** [figyelje a MEMÓRIÁban tárolt OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) a [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) használatával, hogy a számítási feladatok ne használják a rendelkezésre álló memóriát. Növelje a memória korlátait, amelyek a virtuális mag számától függenek, vagy optimalizálja a számítási feladatokat kevesebb memória használatára.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Helytelen hiba történt a nem üres fájl eltávolítására tett kísérlet során.

**Dátum:** TOT 2019

SQL Server/felügyelt példány nem [engedi, hogy a felhasználó eldobjon egy nem üres fájlt](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Ha nem üres adatfájlt próbál meg eltávolítani `ALTER DATABASE REMOVE FILE` utasítás használatával, a rendszer nem küldi el azonnal a hibát, `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` nem. A felügyelt példányok továbbra is megpróbálják eldobni a fájlt, és a művelet sikertelen lesz, miután 30min az `Internal server error`.

**Áthidaló megoldás**: távolítsa el a fájl tartalmát `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` parancs használatával. Ha ez az egyetlen fájl a fájlcsoportja, akkor törölnie kell az ehhez a fájlcsoportja tartozó táblából vagy partícióból származó adatait a fájl zsugorítása előtt, és opcionálisan be kell töltenie ezeket az adatok egy másik táblába/partícióba.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>A szolgáltatási szintek módosítása és a példány-létrehozási műveletek blokkolva vannak az adatbázis-visszaállítás folyamatban

**Dátum:** Sep 2019

A folyamatban lévő `RESTORE` utasítás, az adatáttelepítési szolgáltatás áttelepítési folyamata és a beépített időpontok visszaállítása letiltja a szolgáltatási réteg frissítését vagy a meglévő példány átméretezését, és új példányokat hoz létre, amíg a visszaállítási folyamat be nem fejeződik. A visszaállítási folyamat letiltja ezeket a műveleteket a felügyelt példányokon és példány-készleteken ugyanabban az alhálózatban, ahol a visszaállítási folyamat fut. A példányok példányai nem érintettek. A szolgáltatási réteg műveleteinek létrehozása vagy módosítása nem fog sikerülni vagy időtúllépés – a visszaállítási folyamat befejeződése vagy megszakítása után folytatódni fog.

**Áthidaló megoldás**: Várjon, amíg a visszaállítási folyamat befejeződik, vagy szakítsa meg a visszaállítási folyamatot, ha a Service-réteg létrehozása vagy frissítése magasabb prioritással rendelkezik.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Előfordulhat, hogy a üzletileg kritikus szolgáltatási szinten újra kell konfigurálni a Resource Governor a feladatátvétel után

**Dátum:** Sep 2019

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) funkció, amely lehetővé teszi a felhasználói munkaterheléshez rendelt erőforrások korlátozását, a feladatátvétel vagy a felhasználó által kezdeményezett változás (például a maximális virtuális mag vagy a maximális példány) módosítása után helytelenül osztályozhatja a felhasználói munkaterheléseket. a tárterület mérete).

**Megkerülő megoldás**: futtasson `ALTER RESOURCE GOVERNOR RECONFIGURE` rendszeresen vagy SQL Agent-feladat részeként, amely végrehajtja az SQL-feladatot, amikor a példány elindul, ha [Resource Governor](/sql/relational-databases/resource-governor/resource-governor)használ.

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>A szolgáltatási réteg frissítése után újra kell inicializálni a több adatbázis Service Broker párbeszédpaneleit.

**Dátum:** Augusztus 2019

Az adatbázisok közötti Service Broker párbeszédpanelek nem teszik elérhetővé az üzeneteket más adatbázisokban lévő szolgáltatásoknak a szolgáltatási réteg módosítása után. Az üzenetek **nem vesznek** el, és a küldő várólistáján találhatók. A felügyelt példányban a virtuális mag vagy a példány tárolási méretének változása `service_broke_guid` értéket okoz a [sys. Databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) nézetben, hogy az összes adatbázisra módosítható legyen. Minden olyan `DIALOG`, amely a [BEGIN Dialog](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) utasítással lett létrehozva, amely a Service Broker szolgáltatásra hivatkozik más adatbázisban, nem állítja le az üzeneteket a célként megadott szolgáltatásnak.

**Áthidaló megoldás:** Állítson le minden olyan tevékenységet, amely több adatbázison Service Broker párbeszédet használ a szolgáltatási réteg frissítése előtt, majd újra inicializálja őket. Ha a szolgáltatási szintek változása után még nem elérhető üzenetek vannak, olvassa el a forrás-üzenetsor üzeneteit, és küldje el újra a cél várólistára.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Az Azure AD bejelentkezési típusok megszemélyesítése nem támogatott

**Dátum:** Július 2019

A következő HRE `EXECUTE AS USER` vagy `EXECUTE AS LOGIN` használatával történő megszemélyesítés nem támogatott:
-   Alias HRE-felhasználók. Ebben az esetben a következő hibaüzenetet adja vissza `15517`.
- HRE-bejelentkezések és felhasználók HRE-alkalmazások vagy egyszerű szolgáltatások alapján. Ebben az esetben a következő hibákat adja vissza `15517` és `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query paraméter nem támogatott a sp_send_db_mail

**Dátum:** Április 2019

A [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) eljárásban szereplő `@query` paraméter nem működik.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A tranzakciós replikációt újra kell konfigurálni a Geo-feladatátvétel után

**Dátum:** Mar 2019

Ha a tranzakciós replikáció engedélyezve van egy automatikus feladatátvételi csoportban lévő adatbázison, a felügyelt példány rendszergazdájának törölnie kell a régi elsődleges összes kiadványt, és újra kell konfigurálnia azokat az új elsődlegesen a feladatátvételt követően egy másik régióba. További részletekért lásd: [replikáció](#replication) .

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>A HRE-bejelentkezések és a felhasználók nem támogatottak a SSDT

**Dátum:** November 2019

SQL Server Data Tools nem támogatja teljes mértékben az Azure Active Directory-bejelentkezéseket és-felhasználókat.

### <a name="temporary-database-is-used-during-restore-operation"></a>A VISSZAÁLLÍTÁSi művelet során a rendszer ideiglenes adatbázist használ

Ha egy adatbázist felügyelt példányon állít vissza, a visszaállítási szolgáltatás először egy üres adatbázist hoz létre a kívánt névvel a példányon a név lefoglalásához. Némi idő elteltével ez az adatbázis el lesz dobva, és a rendszer elindítja a tényleges adatbázis visszaállítását. A *visszaállítási* állapotban lévő adatbázis neve helyett véletlenszerű GUID-értékkel fog rendelkezni. Az ideiglenes név a `RESTORE` utasításban megadott kívánt névre változik a visszaállítási folyamat befejezése után. A kezdeti fázisban a felhasználó hozzáférhet az üres adatbázishoz, és akár táblákat is létrehozhat, vagy betöltheti az adatbázist. Ezt az ideiglenes adatbázist a rendszer elveti, ha a visszaállítási szolgáltatás elindítja a második fázist.

**Megkerülő megoldás**: ne nyissa meg a visszaállítani kívánt adatbázist, amíg nem látja, hogy a visszaállítás befejeződött.

### <a name="tempdb-structure-and-content-is-re-created"></a>A TEMPDB szerkezete és tartalma újra létrejön

A `tempdb` adatbázis mindig 12 adatfájlra van bontva, és a fájl szerkezete nem módosítható. A fájlok maximális mérete nem módosítható, és az új fájlok nem vehetők fel `tempdb`ba. a `Tempdb` mindig üres adatbázisként jön létre, amikor a példány elindul vagy feladatátvételt végez, és a `tempdb` végrehajtott módosítások nem őrződnek meg.

### <a name="exceeding-storage-space-with-small-database-files"></a>Kis méretű adatbázisfájlok esetén a tárterület nagyobb

a `CREATE DATABASE`, `ALTER DATABASE ADD FILE`és `RESTORE DATABASE` utasítások sikertelenek lehetnek, mert a példány elérheti az Azure Storage-korlátot.

Az egyes általános célú felügyelt példányok esetében akár 35 TB tárterület is rendelkezésre áll az Azure Premium lemezterület számára. Minden adatbázisfájl külön fizikai lemezre kerül. A lemezek mérete 128 GB, 256 GB, 512 GB, 1 TB vagy 4 TB lehet. A lemez nem használt lemezterülete nem számít fel díjat, de az Azure Premium-lemezek teljes mérete nem haladhatja meg a 35 TB-ot. Bizonyos esetekben előfordulhat, hogy egy felügyelt példány, amely nem igényel 8 TB-ot, a belső töredezettség miatt túllépheti a 35 TB-os Azure-korlátot a tárterületen.

Előfordulhat például, hogy egy általános célú felügyelt példány egy 4 TB-os lemezre helyezett 1,2 TB-os méretű fájllal rendelkezik. Az is előfordulhat, hogy a 248-es fájlok mérete 1 GB, amely külön 128 GB-os lemezekre van helyezve. Ebben a példában:

- A lefoglalt tárterület teljes mérete 1 x 4 TB + 248 x 128 GB = 35 TB.
- A példányon lévő adatbázisok teljes lefoglalt területe 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Ez a példa azt szemlélteti, hogy bizonyos körülmények között a fájlok adott eloszlása miatt a felügyelt példányok elérheti a csatlakoztatott Azure Premium-lemez számára fenntartott 35 TB-os korlátot, ha előfordulhat, hogy nem várható.

Ebben a példában a meglévő adatbázisok továbbra is működőképesek maradnak, és bármilyen probléma nélkül növekednek, amíg új fájlok nem lettek hozzáadva. Új adatbázisok nem hozhatók létre és nem állíthatók vissza, mert nincs elég hely az új lemezmeghajtók számára, még akkor is, ha az összes adatbázis teljes mérete nem éri el a példány méretkorlát-korlátját. Az ebben az esetben visszaadott hiba nem egyértelmű.

[A fennmaradó fájlok számát](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) a rendszernézetek használatával is meghatározhatja. Ha eléri ezt a korlátot, próbálja meg [üresen hagyni, és töröljön néhány kisebb fájlt a DBCC SHRINKFILE utasítás használatával](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) , vagy váltson a [üzletileg kritikus szintjére, amely nem rendelkezik ezzel a korláttal](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Az adatbázis neve helyett GUID-értékek láthatók

A rendszernézetek, a teljesítményszámlálók, a hibaüzenetek, a Xevent típusú eseményekhez és a hibanapló-bejegyzések a tényleges adatbázis neve helyett GUID-adatbázis-azonosítókat jelenítenek meg. Ne használja ezeket a GUID azonosítókat, mert azokat a rendszer a későbbiekben a tényleges adatbázis-nevekkel cseréli le.

### <a name="error-logs-arent-persisted"></a>A hibanapló nem marad meg

A felügyelt példányban elérhető naplók nem maradnak meg, és a méreteik nem szerepelnek a maximális tárolási korlátban. Előfordulhat, hogy a naplók automatikusan törlődnek, ha a feladatátvétel történik. Hiányosságok merülhetnek fel a hibák naplózási előzményeiben, mivel a felügyelt példányok többször is át lettek helyezve több virtuális gépre.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Ugyanazon példányon belül két adatbázis tranzakciós hatóköre nem támogatott

A .NET `TransactionScope` osztálya nem működik, ha két lekérdezést küldenek ugyanazon példányon belül két adatbázisnak ugyanazon tranzakció hatókörében:

```csharp
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

Bár ez a kód ugyanazon a példányon belüli adatszolgáltatásokkal működik, az MSDTC szükséges.

**Áthidaló megoldás:** A [SqlConnection. ChangeDatabase (string)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) használatával használjon másik adatbázist kapcsolati környezetben a két kapcsolat használata helyett.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>A CLR-modulok és a csatolt kiszolgálók időnként nem hivatkozhatnak helyi IP-címekre.

A felügyelt példányba helyezett CLR-modulok, valamint az aktuális példányra hivatkozó elosztott lekérdezések nem tudják feloldani a helyi példányok IP-címét. Ez a hiba átmeneti probléma.

**Áthidaló megoldás:** Ha lehetséges, használjon egy CLR-modul környezeti kapcsolatait.

## <a name="next-steps"></a>Következő lépések

- A felügyelt példányokkal kapcsolatos további információkért lásd: [Mi az a felügyelt példány?](sql-database-managed-instance.md)
- A szolgáltatások és összehasonlítások listájáért lásd: [Azure SQL Database szolgáltatások összehasonlítása](sql-database-features.md).
- Az új felügyelt példányok létrehozásának módját bemutató rövid útmutató: [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
