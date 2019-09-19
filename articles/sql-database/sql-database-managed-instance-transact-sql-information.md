---
title: Azure SQL Database felügyelt példány T-SQL eltérései | Microsoft Docs
description: Ez a cikk a Azure SQL Database és SQL Server felügyelt példányai közötti T-SQL-különbségeket ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 08/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: dc01f8556fb1c88899cae1a8767cb23d6b6041eb
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128874"
---
# <a name="managed-instance-t-sql-differences-limitations-and-known-issues"></a>Felügyelt példányok – T-SQL-különbségek, korlátozások és ismert problémák

Ez a cikk összefoglalja és ismerteti a Azure SQL Database felügyelt példány és a helyszíni SQL Server adatbázismotor közötti szintaxissal és viselkedéssel kapcsolatos különbségeket. A felügyelt példány központi telepítésének lehetősége magas kompatibilitást biztosít a helyszíni SQL Server adatbázis-motorral. A SQL Server adatbázis-működtető funkcióinak többsége felügyelt példányok esetén támogatott.

![Áttelepítés](./media/sql-database-managed-instance/migration.png)

Néhány, a felügyelt példányban bevezetett és néhány viselkedési változás a SQL Serverhoz képest. A különbségek a következő kategóriákba vannak osztva:<a name="Differences"></a>

- A [rendelkezésre állás](#availability) magában foglalja a [mindig](#always-on-availability) és a [biztonsági mentések](#backup)közötti különbségeket.
- A [Biztonság](#security) magában foglalja a [naplózás](#auditing), a [tanúsítványok](#certificates), a [hitelesítő adatok](#credential), a kriptográfiai [szolgáltatók](#cryptographic-providers), a bejelentkezések [és a felhasználók](#logins-and-users), valamint a [szolgáltatás kulcsa és a szolgáltatás](#service-key-and-service-master-key)főkulcsa közötti különbségeket.
- A [konfiguráció](#configuration) magában foglalja a [puffer](#buffer-pool-extension)-bővítmények, a [Rendezés](#collation), a [kompatibilitási szintek](#compatibility-levels), az [adatbázis-tükrözés](#database-mirroring), az adatbázis- [Beállítások](#database-options), a [SQL Server Agent](#sql-server-agent)és a [tábla beállításainak](#tables)különbségeit.
- A [funkciók](#functionalities) közé tartozik a [bulk INSERT/OpenRowset](#bulk-insert--openrowset), a [CLR](#clr), a [DBCC](#dbcc), az [Elosztott tranzakciók](#distributed-transactions), a [bővített események](#extended-events), a [külső kódtárak](#external-libraries), a [FileStream és](#filestream-and-filetable)a lefoglalható, a [teljes szöveges Szemantikai keresés](#full-text-semantic-search), [csatolt kiszolgálók](#linked-servers), [alapszintű](#polybase), [replikálás](#replication), [visszaállítás](#restore-statement), [Service Broker](#service-broker), [tárolt eljárások, függvények és eseményindítók](#stored-procedures-functions-and-triggers).
- [Környezeti beállítások](#Environment) , például virtuális hálózatok és alhálózati konfigurációk.

Ezeknek a funkcióknak a többsége építészeti korlátozás, és a szolgáltatás funkcióit képviseli.

Ezen a lapon a felügyelt példányban felderített [ideiglenes ismert problémák](#Issues) is megtalálhatók, amelyeket később megoldják a rendszer.

## <a name="availability"></a>Rendelkezésre állás

### <a name="always-on-availability"></a>Always on

A [magas rendelkezésre állás](sql-database-high-availability.md) a felügyelt példányba van beépítve, és a felhasználók nem vezérelhetők. A következő utasítások nem támogatottak:

- [VÉGPONT LÉTREHOZÁSA... DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT ELDOBÁSA](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- Az [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) utasítás [set HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) záradéka

### <a name="backup"></a>Tartalék

A felügyelt példányok automatikus biztonsági mentéssel rendelkeznek, így `COPY_ONLY` a felhasználók teljes adatbázis-biztonsági másolatokat hozhatnak létre. A különbözeti, a napló és a fájl pillanatképének biztonsági mentése nem támogatott.

- Felügyelt példány esetén a példány-adatbázis biztonsági mentését csak Azure Blob Storage-fiókba végezheti el:
  - Csak `BACKUP TO URL` a támogatott.
  - `FILE`, `TAPE`és a biztonsági mentési eszközök nem támogatottak.
- Az általános `WITH` lehetőségek többsége támogatott.
  - `COPY_ONLY`kötelező.
  - `FILE_SNAPSHOT`nem támogatott.
  - A szalag beállításai `REWIND`: `NOREWIND`, `UNLOAD`,, `NOUNLOAD` és nem támogatottak.
  - A naplózásra jellemző beállítások `NORECOVERY`: `STANDBY`, és `NO_TRUNCATE` nem támogatottak.

Korlátozások 

- Felügyelt példány esetén biztonsági mentést készíthet egy példány-adatbázisról olyan biztonsági másolatra, amelyen akár 32 sáv is található, ami elegendő a 4 TB-ig terjedő adatbázisokhoz, ha biztonsági mentési tömörítést használ.
- Nem hajtható végre `BACKUP DATABASE ... WITH COPY_ONLY` olyan adatbázis, amely a szolgáltatás által felügyelt transzparens adattitkosítással (TDE) van titkosítva. A szolgáltatás által felügyelt TDE a biztonsági mentések belső TDE-kulccsal lesznek titkosítva. A kulcs nem exportálható, így a biztonsági mentés nem állítható vissza. Használjon automatikus biztonsági mentést és időponthoz való visszaállítást, vagy használja helyette az [ügyfél által felügyelt (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) . Emellett letilthatja a titkosítást az adatbázison.
- A biztonsági mentési sávok maximális mérete a felügyelt `BACKUP` példányok parancsának használatával 195 GB, amely a blob maximális mérete. Növelje meg a sávok számát a Backup parancsban, hogy csökkentse az egyes sávok méretét, és a korláton belül maradjon.

    > [!TIP]
    > Ha ezt a korlátozást szeretné megkerülni, amikor egy helyszíni környezetben vagy egy virtuális gépen SQL Server egy adatbázisról készít biztonsági másolatot, a következőkre van lehetősége:
    >
    > - Biztonsági mentést készíthet a biztonsági mentés `URL` helyett.`DISK`
    > - Töltse fel a biztonságimásolat-fájlokat a blob Storage-ba.
    > - Állítsa vissza a felügyelt példányt.
    >
    > A `Restore` felügyelt példányban lévő parancs a biztonsági mentési fájlokban nagyobb blob-méreteket támogat, mert a feltöltött biztonságimásolat-fájlok tárolására egy másik blob-típust használ a rendszer.

A T-SQL használatával történő biztonsági mentéssel kapcsolatos információkért lásd: [biztonsági mentés](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Biztonság

### <a name="auditing"></a>Naplózás

A Azure SQL Database és a SQL Server adatbázisaiban található adatbázisok naplózása közötti fő különbségek a következők:

- A Azure SQL Database felügyelt példányok központi telepítésének beállításával a naplózás a kiszolgáló szintjén működik. A `.xel` naplófájlok tárolása az Azure Blob Storage-ban történik.
- A Azure SQL Database az önálló adatbázis és a rugalmas készlet üzembe helyezési lehetőségeivel a naplózás az adatbázis szintjén működik.
- SQL Server helyszíni vagy virtuális gépeken a naplózás a kiszolgáló szintjén működik. Az eseményeket fájlrendszer vagy Windows-eseménynaplók tárolják.
 
A felügyelt példány XEvent-naplózása támogatja az Azure Blob Storage-célokat. A fájl-és Windows-naplók nem támogatottak.

Az Azure Blob Storage- `CREATE AUDIT` ba való naplózás szintaxisának főbb eltérései a következők:

- Új szintaxissal `TO URL` megadható az Azure Blob Storage-tároló URL-címe, ahol a `.xel` fájlok el vannak helyezve.
- A szintaxis `TO FILE` nem támogatott, mert a felügyelt példányok nem férnek hozzá a Windows-fájlmegosztás számára.

További információkért lásd: 

- [KISZOLGÁLÓ NAPLÓZÁSÁNAK LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Tanúsítványok

A felügyelt példányok nem férnek hozzá a fájlmegosztás és a Windows-mappák számára, így a következő korlátozások érvényesek:

- A `CREATE FROM` / fájl nem támogatott tanúsítványokhoz.`BACKUP TO`
- A `CREATE` tanúsítvány/ nemtámogatott/ . `FILE` `BACKUP` `ASSEMBLY` A titkos kulcs fájljai nem használhatók. 

Lásd: tanúsítvány és [biztonsági mentési tanúsítvány](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql) [létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) . 
 
**Áthidaló megoldás**: A tanúsítvány vagy a titkos kulcs parancsfájlja, tárolás. SQL-fájlként, és létrehozás bináris fájlból:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Hitelesítő adat

Csak a Azure Key Vault `SHARED ACCESS SIGNATURE` és az identitások támogatottak. A Windows-felhasználók nem támogatottak.

Lásd: [hitelesítő adatok létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) és a [hitelesítő adatok módosítása](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Kriptográfiai szolgáltatók

A felügyelt példányok nem férhetnek hozzá a fájlokhoz, így a titkosítási szolgáltatók nem hozhatók létre:

- `CREATE CRYPTOGRAPHIC PROVIDER`nem támogatott. Lásd: [titkosítási szolgáltató létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER`nem támogatott. Lásd: a [kriptográfiai szolgáltató módosítása](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Bejelentkezések és felhasználók

- A, `FROM CERTIFICATE` `FROM ASYMMETRIC KEY`a és`FROM SID` a használatával létrehozott SQL-bejelentkezések támogatottak. Lásd: [create login (bejelentkezés létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)).
- A [bejelentkezési szintaxissal](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) létrehozott Azure Active Directory (Azure ad) kiszolgálói rendszerbiztonsági tag (bejelentkezések) vagy a [felhasználó létrehozása a bejelentkezéskor [Azure ad login]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) szintaxis támogatott (nyilvános előzetes verzió). Ezek a bejelentkezések a kiszolgálói szinten jönnek létre.

    A felügyelt példány a szintaxissal `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`támogatja az Azure ad-adatbázisok résztvevőit. Ez a szolgáltatás az Azure AD-ben tárolt adatbázis-felhasználók néven is ismert.

- A `CREATE LOGIN ... FROM WINDOWS` szintaxissal létrehozott Windows-bejelentkezések nem támogatottak. Azure Active Directory bejelentkezések és felhasználók használata.
- Az Azure AD-felhasználó, aki létrehozta a példányt, [korlátlan rendszergazdai jogosultságokkal](sql-database-manage-logins.md#unrestricted-administrative-accounts)rendelkezik.
- A nem rendszergazda Azure ad adatbázis-szintű felhasználókat a `CREATE USER ... FROM EXTERNAL PROVIDER` szintaxis használatával lehet létrehozni. Lásd [: felhasználó létrehozása... KÜLSŐ SZOLGÁLTATÓTÓL](sql-database-manage-logins.md#non-administrator-users).
- Az Azure AD-kiszolgáló résztvevői (bejelentkezések) csak egy felügyelt példányon belül támogatják az SQL-funkciókat. Azok a funkciók, amelyek eltérő interakciót igényelnek, függetlenül attól, hogy ugyanazon az Azure AD-bérlőn vagy más bérlőn belül vannak, nem támogatottak az Azure AD-felhasználók számára. Ilyen funkciók például a következők:

  - SQL tranzakciós replikáció.
  - Kiszolgáló csatolása.

- Egy Azure ad-csoportra leképezett Azure AD-bejelentkezés beállítása, mivel az adatbázis tulajdonosa nem támogatott.
- Az Azure AD-kiszolgáló rendszerbiztonsági tagjainak más Azure AD-rendszerbiztonsági tag használatával történő megszemélyesítése támogatott, például a [Execute as](/sql/t-sql/statements/execute-as-transact-sql) záradékkal. A végrehajtás korlátozásként:

  - Az Azure AD-felhasználók nem támogatják a (z) rendszerbeli VÉGREHAJTÁSt, ha a név eltér a bejelentkezési névvel. Ilyen eset például, amikor a felhasználó létrehozása a [myAadUser] szintaxissal történik a login [john@contoso.com] típusból, és a megszemélyesítést a User = _myAadUser_exec használatával kísérli meg. Amikor létrehoz egy felhasználót egy Azure AD-kiszolgálói rendszerbiztonsági tag (login) alapján, a **felhasználónévvel** azonos login_name kell megadnia a **bejelentkezéshez**.
  - Csak a `sysadmin` szerepkör részét képező SQL Server szintű rendszerbiztonsági tag (login) futhat a következő, az Azure ad-résztvevőket megcélzó műveletek:

    - VÉGREHAJTÁS FELHASZNÁLÓKÉNT
    - VÉGREHAJTÁS BEJELENTKEZÉSKÉNT

- Az Azure AD Server-rendszerbiztonsági tag (Logins) nyilvános előzetes verziójának korlátai:

  - A felügyelt példányok rendszergazdai korlátozásai Active Directory:

    - A felügyelt példány beállításához használt Azure AD-rendszergazda nem használható Azure AD Server-rendszerbiztonsági tag (login) létrehozásához a felügyelt példányon belül. Létre kell hoznia az első Azure ad-kiszolgálói rendszerbiztonsági tag (login) `sysadmin` szerepkört egy SQL Server fiók használatával. Ez az ideiglenes korlátozás el lesz távolítva az Azure AD-kiszolgáló résztvevői (bejelentkezések) általánosan elérhetővé válása után. Ha egy Azure AD-beli rendszergazdai fiókot próbál használni a bejelentkezés létrehozásához, a következő hibaüzenet jelenik meg:`Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Jelenleg a főadatbázisban létrehozott első Azure ad-bejelentkezést a standard SQL Server fióknak (nem Azure ad) kell létrehoznia, amely egy `sysadmin` szerepkör a külső szolgáltató használatával létrehozott [bejelentkezéssel](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) . Az általános elérhetőség után ez a korlátozás el lesz távolítva. Ezután létrehozhat egy kezdeti Azure AD-bejelentkezést a felügyelt példány Active Directory rendszergazdája használatával.
    - Az SQL Server Management Studio vagy SqlPackage szolgáltatással használt DacFx (Exportálás/Importálás) nem támogatott az Azure AD-bejelentkezések esetében. Ezt a korlátozást az Azure AD-kiszolgáló résztvevői (bejelentkezések) általánosan elérhetővé válása után távolítja el a rendszer.
    - Azure AD Server-rendszerbiztonsági tag (bejelentkezések) használata a SQL Server Management Studio használatával:

      - A bármely hitelesített bejelentkezést használó parancsfájlok Azure AD-bejelentkezések nem támogatottak.
      - Az IntelliSense nem ismeri fel a CREATE LOGIN utasítást a külső szolgáltató utasításban, és piros aláhúzást mutat.

- Az Azure ad-t `securityadmin` csak a felügyelt példányok üzembe helyezési folyamata által létrehozott, a kiszolgálói szerepkörök, például a vagy `sysadmin`a, illetve más bejelentkezési engedélyekkel rendelkező, a kiszolgálói szinten megváltoztatható bejelentkezési jogosultsággal rendelkező kiszolgálói szintű rendszerbiztonsági tag hozhatja létre a felügyelt példány Master adatbázisában található kiszolgálói rendszerbiztonsági tag (Logins).
- Ha a bejelentkezés egy SQL-rendszerbiztonsági tag, csak a `sysadmin` szerepkör részét képező bejelentkezések használhatják a Create parancsot az Azure ad-fiókhoz tartozó bejelentkezések létrehozásához.
- Az Azure AD-bejelentkezésnek egy Azure AD-tagnak kell lennie, amely a Azure SQL Database felügyelt példányához használt könyvtárban található.
- Az Azure AD-kiszolgáló résztvevői (bejelentkezések) a Object Explorer SQL Server Management Studio 18,0 Preview 5 verziótól kezdődően láthatók.
- Az egymást átfedő Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések) Azure AD-rendszergazdai fiókkal való használata engedélyezett. Az Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések) elsőbbséget élveznek az Azure AD-rendszergazdával szemben a rendszerbiztonsági tag feloldásakor és az engedélyek a felügyelt példányra való alkalmazásakor.
- A hitelesítés során a rendszer a következő sorozatot alkalmazza a hitelesítő tag feloldására:

    1. Ha az Azure AD-fiók létezik közvetlenül leképezve az Azure AD-kiszolgáló rendszerbiztonsági tagja (login) számára, amely szerepel a sys. server_principals "E" típusban, adja meg a hozzáférést és alkalmazza az Azure AD-kiszolgáló rendszerbiztonsági tag (login) engedélyeit.
    2. Ha az Azure AD-fiók tagja egy olyan Azure ad-csoportnak, amely az Azure AD-kiszolgáló rendszerbiztonsági tagjához (login) van társítva, amely a sys. server_principals "X" típusként van leképezve, adjon hozzáférést és alkalmazza az Azure AD-csoport bejelentkezési engedélyeit.
    3. Ha az Azure AD-fiók egy speciális, felügyelt példányhoz konfigurált Azure AD-rendszergazda, amely nem létezik a felügyelt példányok rendszernézeteiben, alkalmazza az Azure AD-rendszergazda speciális rögzített engedélyeit a felügyelt példányhoz (örökölt mód).
    4. Ha az Azure AD-fiók létezik közvetlenül leképezve egy Azure AD-felhasználóhoz egy adatbázisban, amely szerepel a sys. database_principals "E" típusban, adjon hozzáférést és alkalmazza az Azure AD-adatbázis felhasználójának engedélyeit.
    5. Ha az Azure AD-fiók tagja egy olyan Azure ad-csoportnak, amely egy Azure AD-felhasználóhoz van hozzárendelve egy adatbázisban, amely szerepel a sys. database_principals "X" típusban, adja meg a hozzáférést, és alkalmazza az Azure AD-csoport bejelentkezési engedélyeit.
    6. Ha egy Azure ad-bejelentkezés egy Azure AD-felhasználói fiókhoz vagy egy Azure AD-csoport fiókjához van rendelve, amely a hitelesítést végző felhasználóra van feloldva, az Azure AD-bejelentkezés összes engedélyét alkalmazza a rendszer.

### <a name="service-key-and-service-master-key"></a>Szolgáltatási kulcs és szolgáltatás főkulcsa

- A [főkulcs biztonsági mentése](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által felügyelt).
- A [főkulcs visszaállítása](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által felügyelt).
- A [szolgáltatás főkulcsának biztonsági mentése](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által felügyelt).
- A [szolgáltatás főkulcsának visszaállítása](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) nem támogatott (SQL Database szolgáltatás által felügyelt).

## <a name="configuration"></a>Konfiguráció

### <a name="buffer-pool-extension"></a>Puffer-készlet kiterjesztése

- A [puffer-készlet kiterjesztése](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nem támogatott.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`nem támogatott. Lásd: a [kiszolgáló konfigurációjának módosítása](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Rendezés

A példányok alapértelmezett rendezése `SQL_Latin1_General_CP1_CI_AS` a (z) és a (z) létrehozási paraméterként adható meg. Lásd [](https://docs.microsoft.com/sql/t-sql/statements/collations): rendezések.

### <a name="compatibility-levels"></a>Kompatibilitási szintek

- A támogatott kompatibilitási szintek a következők: 100, 110, 120, 130, 140 és 150.
- Az 100 alatti kompatibilitási szintek nem támogatottak.
- Az új adatbázisok alapértelmezett kompatibilitási szintje 140. A visszaállított adatbázisok esetében a kompatibilitási szint változatlan marad, ha 100-s vagy újabb.

Lásd: az [adatbázis kompatibilitási szintjének módosítása](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Adatbázis-tükrözés

Az adatbázis-tükrözés nem támogatott.

- `ALTER DATABASE SET PARTNER`és `SET WITNESS` a beállítások nem támogatottak.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`nem támogatott.

További információ: [Alter Database set partner és set tanúsító](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) és [végpont létrehozása... DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Adatbázis-beállítások

- Több naplófájl nem támogatott.
- A memóriában tárolt objektumok nem támogatottak a általános célú szolgáltatási rétegben. 
- A általános célú-példányok száma legfeljebb 280, amely adatbázis-kiszolgálónként legfeljebb 280 fájlt jelent. A általános célú szinten található adatfájlok és naplófájlok is beleszámítanak a korlátba. [Az üzletileg kritikus szinten az adatbázis 32 767](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)-es fájljait támogatja.
- Az adatbázis nem tartalmazhat FileStream-adatkészletet tartalmazó fájlcsoportok. A visszaállítás sikertelen, ha a `FILESTREAM` . bak tartalmaz egy adatkészletet. 
- Minden fájl az Azure Blob Storage-ba kerül. A fájl i/o-és átviteli sebessége az egyes fájlok méretétől függ.

#### <a name="create-database-statement"></a>ADATBÁZIS-utasítás létrehozása

A következő korlátozások érvényesek `CREATE DATABASE`:

- A fájlok és a Fájlcsoportok nem definiálhatók. 
- A `CONTAINMENT` beállítás nem támogatott. 
- `WITH`a beállítások nem támogatottak. 
   > [!TIP]
   > Áthidaló megoldásként használja `ALTER DATABASE` `CREATE DATABASE` a következőt: az adatbázis-beállítások megadása a fájlok hozzáadásához vagy a tároló beállításához. 

- A `FOR ATTACH` beállítás nem támogatott.
- A `AS SNAPSHOT OF` beállítás nem támogatott.

További információ: [Create Database (adatbázis létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)).

#### <a name="alter-database-statement"></a>ALTER DATABASE utasítás

Bizonyos fájltulajdonságok nem állíthatók be és nem módosíthatók:

- A `ALTER DATABASE ADD FILE (FILENAME='path')` t-SQL-utasításban nem adható meg a fájl elérési útja. Távolítsa el `FILENAME` a parancsfájlt, mert a felügyelt példányok automatikusan elhelyezik a fájlokat. 
- A fájl neve nem módosítható az `ALTER DATABASE` utasítás használatával.

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

További információ: [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- A SQL Server Agent engedélyezése és letiltása jelenleg nem támogatott a felügyelt példányokban. Az SQL-ügynök mindig fut.
- SQL Server Agent beállítások csak olvashatók. Az eljárás `sp_set_agent_properties` felügyelt példányban nem támogatott. 
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
- Az e-mailes értesítések támogatottak, de a Database Mail profil konfigurálását igényli. SQL Server Agent csak egy Database Mail profilt használhat, ezért a hívása `AzureManagedInstance_dbmail_profile`kötelező. 
  - A személyhívó nem támogatott.
  - A NetSend nem támogatott.
  - A riasztások még nem támogatottak.
  - A proxyk nem támogatottak.
- Az Eseménynapló nem támogatott.

A következő SQL Agent-funkciók jelenleg nem támogatottak:

- Proxyk
- Feladatok ütemezése üresjárati PROCESSZORon
- Ügynök engedélyezése vagy letiltása
- Riasztások

További információ a SQL Server Agentről: [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Táblák

A következő táblázat típusok nem támogatottak:

- [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)
- [külső tábla](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) Polybase
- [MEMORY_OPTIMIZED](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (csak általános célú szinten támogatott)

A táblázatok létrehozásával és módosításával kapcsolatos további információkért lásd: [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) és [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Functionalities

### <a name="bulk-insert--openrowset"></a>Tömeges Beszúrás/OPENROWSET

A felügyelt példányok nem férnek hozzá a fájlmegosztás és a Windows-mappák eléréséhez, így a fájlokat az Azure Blob Storage-ból kell importálni:

- `DATASOURCE`Az Azure Blob Storage `BULK INSERT` -ból származó fájlok importálásakor szükséges a parancsban. Lásd: [bulk INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`Az Azure Blob Storage `OPENROWSET` -ból származó fájl tartalmának olvasása során szükséges a függvényben. Lásd: [OpenRowset](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

A felügyelt példányok nem férnek hozzá a fájlmegosztás és a Windows-mappák számára, így a következő korlátozások érvényesek:

- Csak `CREATE ASSEMBLY FROM BINARY` a támogatott. Lásd: [szerelvény létrehozása bináris fájlból](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE`nem támogatott. Lásd: [szerelvény létrehozása fájlból](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY`nem lehet hivatkozni a fájlokra. Lásd: az [Alter Assembly](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail`a @file_attachments mellékletek nem küldhetők paraméterrel. A helyi fájlrendszer és a külső megosztások vagy az Azure Blob Storage nem érhetők el ebből az eljárásból.
 - Tekintse meg `@query` a paraméterrel és a hitelesítéssel kapcsolatos ismert problémákat.
 
### <a name="dbcc"></a>DBCC

A SQL Serverban engedélyezett nem dokumentált DBCC utasítások nem támogatottak a felügyelt példányokban.

- Csak korlátozott számú globális nyomkövetési jelző támogatott. A munkamenet- `Trace flags` szint nem támogatott. Lásd [](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql): nyomkövetési jelzők.
- A [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) és a [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) korlátozott számú globális nyomkövetési jelzővel működik.
- A REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST és REPAIR_REBUILD beállításokkal rendelkező [DBCC-CHECKDB utasítást](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) nem használhatók, mert az adatbázis `SINGLE_USER` nem állítható be a mode-ban – lásd az [adatbázis-eltérések módosításait](#alter-database-statement). A lehetséges adatbázis-sérüléseket az Azure-támogatási csapat kezeli. Vegye fel a kapcsolatot az Azure támogatási szolgálatával, ha az adatbázis sérülését rögzíti.

### <a name="distributed-transactions"></a>Elosztott tranzakciók

Az MSDTC és a [rugalmas tranzakciók](sql-database-elastic-transactions-overview.md) jelenleg nem támogatottak a felügyelt példányokban.

### <a name="extended-events"></a>Bővített események

Bizonyos Windows-specifikus célok a kiterjesztett eseményekhez (Xevent típusú eseményekhez) nem támogatottak:

- A `etw_classic_sync` cél nem támogatott. Fájlok `.xel` tárolása az Azure Blob Storage-ban. Lásd: [etw_classic_sync-cél](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- A `event_file` cél nem támogatott. Fájlok `.xel` tárolása az Azure Blob Storage-ban. Lásd: [event_file-cél](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Külső kódtárak

Adatbázison belüli R és Python esetén a külső könyvtárak még nem támogatottak. Lásd: [SQL Server Machine learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream és lefilézett

- A FileStream-adatértékek nem támogatottak.
- Az adatbázis nem tartalmazhat adatforrással rendelkező `FILESTREAM` fájlcsoportok.
- `FILETABLE`nem támogatott.
- A táblák nem `FILESTREAM` rendelkezhetnek típusokkal.
- A következő függvények nem támogatottak:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

További információ: [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) és [objektumnak](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Teljes szöveges szemantikai keresés

A [szemantikai keresés](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) nem támogatott.

### <a name="linked-servers"></a>Társított kiszolgálók

A felügyelt példányokban lévő csatolt kiszolgálók korlátozott számú célt támogatnak:

- A támogatott célok a felügyelt példányok, az önálló adatbázisok és a SQL Server példányok. 
- A csatolt kiszolgálók nem támogatják az elosztott írható tranzakciókat (MS DTC).
- A nem támogatott célok a fájlok, a Analysis Services és az egyéb RDBMS. Próbálja meg a natív CSV-importálást használni az `BULK INSERT` Azure `OPENROWSET` blob Storage használatával vagy a fájl importálása alternatívájaként.

Műveletek

- A példányok közötti írási tranzakciók nem támogatottak.
- `sp_dropserver`egy csatolt kiszolgáló eldobása esetén támogatott. Lásd: [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- A `OPENROWSET` függvényt csak SQL Server példányokon lehet lekérdezéseket végrehajtani. Lehetnek felügyelt, helyszíni vagy virtuális gépek. Lásd: [OpenRowset](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- A `OPENDATASOURCE` függvényt csak SQL Server példányokon lehet lekérdezéseket végrehajtani. Lehetnek felügyelt, helyszíni vagy virtuális gépek. Szolgáltatóként `SQLNCLI`csak `SQLNCLI11`a, `SQLOLEDB` , és értékek támogatottak. Például: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Lásd: [index](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
- A csatolt kiszolgálók nem használhatók a hálózati megosztásokból származó fájlok (Excel, CSV) olvasásához. Próbáljon meg olyan [bulk INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) vagy [OpenRowset](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) használni, amely CSV-fájlokat olvas be az Azure Blob Storageból. A kérelmek nyomon követése a [felügyelt példányok visszajelzési elemén](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

A HDFS vagy az Azure Blob Storage-ban található fájlokra hivatkozó külső táblák nem támogatottak. További információ a következőről [: Base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikálás

- A pillanatképek és a kétirányú replikációs típusok támogatottak. Az egyesítéses replikáció, a társ-társ replikáció és a frissíthető előfizetések nem támogatottak.
- A [tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md) a felügyelt példány nyilvános előzetes verziójához érhető el, néhány korlátozással:
    - A replikációs résztvevők (kiadó, terjesztő, lekéréses előfizető és leküldéses előfizető) a felügyelt példányokon helyezhetők el, de a közzétevő és a terjesztő nem helyezhető el különböző példányokra.
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
  - `FROM URL`(Az Azure Blob Storage) az egyetlen támogatott lehetőség.
  - `FROM DISK`/`TAPE`a/Backup-eszköz nem támogatott.
  - A biztonságimásolat-készletek nem támogatottak.
- `WITH`a beállítások nem támogatottak, például `DIFFERENTIAL` a `STATS`nem vagy a.
- `ASYNC RESTORE`: A visszaállítás akkor is folytatódik, ha az ügyfél-kapcsolatok megszakadnak. Ha a rendszer eldobta a-kapcsolatokat, `sys.dm_operation_status` megtekintheti a visszaállítási művelet állapotát, valamint egy létrehozási és eldobási adatbázist. Lásd: [sys. DM _operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

A következő adatbázis-beállítások vannak beállítva vagy felülbírálva, és később nem módosíthatók: 

- `NEW_BROKER`Ha a közvetítő nincs engedélyezve a. bak fájlban. 
- `ENABLE_BROKER`Ha a közvetítő nincs engedélyezve a. bak fájlban. 
- `AUTO_CLOSE=OFF`Ha a. bak fájlban `AUTO_CLOSE=ON`található adatbázis. 
- `RECOVERY FULL`Ha a. bak fájlban található adatbázis vagy `SIMPLE` `BULK_LOGGED` helyreállítási mód van.
- A memóriára optimalizált fájlcsoportja hozzáadása és neve XTP, ha nem szerepel a forrás. bak fájlban. 
- A rendszer a meglévő memória-optimalizált fájlcsoportja átnevezi a XTP. 
- `SINGLE_USER`és `RESTRICTED_USER` a`MULTI_USER`beállítások a következőre lesznek konvertálva:.

Korlátozások 

- Előfordulhat, hogy a sérült adatbázisok biztonsági másolatait a rendszer a sérülés típusától függően visszaállítja, az automatikus biztonsági mentések azonban nem lesznek elvégezve, amíg a sérülés nem lesz kijavítva. Győződjön meg arról, hogy `DBCC CHECKDB` a forrás példányon fut, és `WITH CHECKSUM` használja a biztonsági mentést a probléma megelőzése érdekében.
- A dokumentumban ismertetett korlátozásokat ( `FILESTREAM` például vagy `FILETABLE` objektumokat) tartalmazó adatbázis fájljánakvisszaállításanemállíthatóvisszaafelügyeltpéldányon.`.BAK`
- `.BAK`a több biztonságimásolat-készletet tartalmazó fájlok nem állíthatók vissza. 
- `.BAK`a több naplófájlt tartalmazó fájlok nem állíthatók vissza.
- A 8 TB-nál nagyobb adatbázisok, a memóriában tárolt OLTP-objektumok, illetve a 280-nál több fájlt meghaladó fájlok száma nem állítható vissza általános célú példányon. 
- A 4 TB-nál nagyobb vagy memóriában lévő OLTP-objektumokba tartozó, az [erőforrás-korlátokban](sql-database-managed-instance-resource-limits.md) leírt méretnél nagyobb méretű adatbázisokat tartalmazó biztonsági másolatok nem állíthatók vissza üzletileg kritikus példányon.
További információ a visszaállítási utasításokról: [Restore utasítások](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Ugyanezek a korlátozások érvényesek a beépített időpontra történő visszaállítási műveletre. Például általános célú-adatbázis 4 TB-nál nagyobb mértékben nem állítható vissza üzletileg kritikus példányon. Üzletileg kritikus adatbázis memóriában tárolt OLTP-fájlokkal vagy több mint 280 fájllal nem állítható vissza általános célú példányon.

### <a name="service-broker"></a>Service Broker

A több példányban elérhető Service Broker nem támogatott:

- `sys.routes`: Előfeltételként ki kell választania a-címeket a sys. Routes elemből. A címnek minden útvonalon HELYInek kell lennie. Lásd: [sys. Routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: A nem használható `CREATE ROUTE` a `ADDRESS` alkalmazással `LOCAL`. Lásd: [útvonal létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: A nem használható `ALTER ROUTE` a `ADDRESS` alkalmazással `LOCAL`. Lásd: [útvonal módosítása](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Tárolt eljárások, függvények és eseményindítók

- `NATIVE_COMPILATION`nem támogatott a általános célú szinten.
- A következő [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) -beállítások nem támogatottak: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`nem támogatott. Lásd: [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell`nem támogatott. Lásd: [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`nem támogatottak, köztük `sp_addextendedproc`  a `sp_dropextendedproc`és a. Lásd: [kiterjesztett tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` és`sp_detach_db` nem támogatott. Lásd: [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)és [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Rendszerfunkciók és változók

A következő változók, függvények és nézetek eltérő eredményeket adnak vissza:

- `SERVERPROPERTY('EngineEdition')`a 8-as értéket adja vissza. Ez a tulajdonság egyedileg azonosítja a felügyelt példányt. Lásd: [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`NULL értéket ad vissza, mert a példány fogalma a SQL Server esetében nem vonatkozik a felügyelt példányokra. Lásd: [SERVERPROPERTY ("példánynév")](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`egy teljes DNS "csatlakoztatható" nevet ad vissza, például my-managed-instance.wcus17662feb9ce98.database.windows.net. Lásd [:@SERVERNAME@](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`egy teljes DNS "csatlakoztatható" nevet ad vissza, például `myinstance.domain.database.windows.net` a "Name" és a "data_source" tulajdonsághoz. Lásd: [sys. KISZOLGÁLÓK](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`NULL értéket ad vissza, mert a szolgáltatáshoz tartozó fogalma SQL Server nem vonatkozik felügyelt példányra. Lásd [:@SERVICENAME@](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID`támogatott. NULL értéket ad vissza, ha az Azure AD-bejelentkezés nem a sys. syslogins. Lásd: [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID`nem támogatott. A rendszer a helytelen adatmennyiséget adja vissza, ami egy ideiglenes ismert probléma. Lásd: [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Environment"></a>Környezeti korlátozások

### <a name="subnet"></a>Subnet
-  Nem helyezhet el semmilyen más erőforrást (például virtuális gépeket) abban az alhálózatban, amelyben a felügyelt példányt telepítette. Ezeket az erőforrásokat egy másik alhálózattal telepítse.
- Az alhálózatnak elegendő számú elérhető [IP-címmel](sql-database-managed-instance-connectivity-architecture.md#network-requirements)kell rendelkeznie. A minimum 16, míg a javaslat szerint legalább 32 IP-címmel kell rendelkeznie az alhálózatban.
- [A szolgáltatási végpontok nem társíthatók a felügyelt példány](sql-database-managed-instance-connectivity-architecture.md#network-requirements)alhálózatához. Győződjön meg arról, hogy a szolgáltatás-végpontok beállítás le van tiltva a virtuális hálózat létrehozásakor.
- A régiókban üzembe helyezhető virtuális mag és típusok száma bizonyos [korlátozásokkal és korlátozásokkal](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)rendelkezik.
- Vannak olyan [biztonsági szabályok, amelyeket az alhálózaton kell alkalmazni](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>VNET
- A VNet az Resource Model használatával telepíthető – a VNet klasszikus modellje nem támogatott.
- Felügyelt példány létrehozása után a felügyelt példány vagy VNet másik erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott.
- Egyes szolgáltatások, például a App Service környezetek, a Logic apps és a felügyelt példányok (földrajzi replikálás, tranzakciós replikálás vagy csatolt kiszolgálókon keresztül) nem férnek hozzá a felügyelt példányokhoz különböző régiókban, ha a virtuális hálózatok [globálisan vannak csatlakoztatva peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Ezekhez az erőforrásokhoz a ExpressRoute vagy a VNet – VNet használatával csatlakozhat a VNet-átjárók segítségével.

### <a name="tempdb"></a>TEMPDB

A maximális fájlméret nem lehet `tempdb` nagyobb, mint 24 GB általános célú szinten. A üzletileg kritikus `tempdb` szinten lévő maximális méretet a példány tárolási mérete korlátozza. `Tempdb`a naplófájl mérete általános célú és üzletileg kritikus szinten egyaránt 120 GB-ra van korlátozva. Előfordulhat, hogy egyes lekérdezések hibát jeleznek, ha legalább 24 GB-nál több `tempdb` adatra van szükségük, vagy ha több mint 120 GB adatnaplót hoznak létre.

### <a name="error-logs"></a>Hibanaplók

A felügyelt példányok részletes információkat helyeznek el a hibák naplóiban. A hibanapló számos belső rendszereseményt naplóz. Egyéni eljárással olvashatja el a nem releváns bejegyzéseket kiszűrő hibákat. További információ: [felügyelt példány – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

## <a name="Issues"></a>Ismert problémák

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongioing-database-restore"></a>A ongioing adatbázis-visszaállítás blokkolja a szolgáltatási szintet és a példányok létrehozásának műveleteit.

**Dátum** Sep 2019

A `RESTORE` folyamatos utasítás, az adatáttelepítési szolgáltatás áttelepítési folyamata és a beépített idővisszaállítási funkció letiltja a meglévő példány frissítési vagy átméretezését, valamint új példányok létrehozását, amíg a visszaállítási folyamat be nem fejeződik. A visszaállítási folyamat letiltja ezeket a műveleteket a felügyelt példányokon és példány-készleteken ugyanabban az alhálózatban, ahol a visszaállítási folyamat fut. A példányok példányai nem érintettek. A szolgáltatási réteg műveleteinek létrehozása vagy módosítása nem fog sikerülni vagy időtúllépés – a visszaállítási folyamat befejeződése vagy megszakítása után folytatódni fog.

**Áthidaló megoldás**: Várjon, amíg a visszaállítási folyamat befejeződik, vagy szakítsa meg a visszaállítási folyamatot, ha a Service-réteg létrehozása vagy frissítése magasabb prioritással rendelkezik.

### <a name="missing-validations-in-restore-process"></a>Hiányzó érvényességek a visszaállítási folyamatban

**Dátum** Sep 2019

`RESTORE`az utasítás és a beépített időponthoz tartozó visszaállítás nem hajt végre bizonyos nessecary-ellenőrzéseket a visszaállított adatbázison:
- **A DBCC CHECKDB utasítást**  -  `RESTORE` utasítás nem `DBCC CHECKDB` hajtható végre a visszaállított adatbázisban. Ha egy eredeti adatbázis sérült, vagy sérült a biztonságimásolat-fájl az Azure Blob Storage-ba való másoláskor, az automatikus biztonsági mentések nem lesznek elvégezve, és az Azure-támogatás kapcsolatba lép az ügyféllel. 
- A beépített időponthoz tartozó visszaállítási folyamat nem jelöli meg, hogy az üzletileg kritikus példány automatikus biztonsági mentése tartalmazza [-e a memóriában lévő OLTP objektumokat](sql-database-in-memory.md#in-memory-oltp). 

**Áthidaló megoldás**: Győződjön meg arról, hogy a forrásadatbázis `DBCC CHECKDB` a biztonsági másolat készítése előtt van végrehajtva, és a `WITH CHECKSUM` biztonsági mentés lehetőség használatával elkerülheti a felügyelt példányon visszaállítható esetleges sérüléseket. Győződjön meg róla, hogy a forrásadatbázis nem tartalmaz [memóriában tárolt OLTP objektumokat](sql-database-in-memory.md#in-memory-oltp) , ha általános célú szinten állítja vissza.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Előfordulhat, hogy a üzletileg kritikus szolgáltatási szinten újra kell konfigurálni a Resource Governor a feladatátvétel után

**Dátum** Sep 2019

[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) funkció, amely lehetővé teszi a felhasználói munkaterheléshez rendelt erőforrások korlátozását, a feladatátvétel vagy a felhasználó által kezdeményezett változás (például a maximális virtuális mag vagy a maximális példány) módosítása után helytelenül osztályozhatja a felhasználói munkaterheléseket. a tárterület mérete).

**Áthidaló megoldás**: Rendszeres `ALTER RESOURCE GOVERNOR RECONFIGURE` időközönként vagy SQL Agent-feladat részeként futtassa, amely végrehajtja az SQL-feladatot, amikor a példány elindul, ha [Resource Governort](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)használ.

### <a name="cannot-authenticate-to-external-mail-servers-using-secure-connection-ssl"></a>Biztonságos kapcsolat (SSL) használatával nem végezhető el a hitelesítés a külső levelezési kiszolgálókon

**Dátum** Augusztus 2019

A [biztonságos kapcsolat (SSL) használatával konfigurált](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-database-mail) adatbázis-levelezés nem tud hitelesíteni az Azure-on kívüli e-mail-kiszolgálókon. Ez a biztonsági konfigurációs probléma hamarosan megoldódik.

**Workaround** Ideiglenesen távolítsa el a biztonságos kapcsolatokat (SSL) az adatbázis levelezési konfigurációjától, amíg a probléma meg nem oldódik. 

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>A szolgáltatási réteg frissítése után újra kell inicializálni a több adatbázis Service Broker párbeszédpaneleit.

**Dátum** Augusztus 2019

Az adatbázisok közötti Service Broker párbeszédpanelek nem teszik elérhetővé az üzeneteket más adatbázisokban lévő szolgáltatásoknak a szolgáltatási réteg módosítása után. Az üzenetek **nem vesznek** el, és a küldő várólistáján találhatók. A felügyelt példányban a virtuális mag vagy a példány tárolási méretének változása miatt `service_broke_guid` a [sys. Databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) nézetet fogja megváltoztatni az összes adatbázisra vonatkozóan. A `DIALOG` más adatbázisban található Service Broker szolgáltatásra hivatkozó [BEGIN Dialog](https://docs.microsoft.com/en-us/sql/t-sql/statements/begin-dialog-conversation-transact-sql) utasítással létrehozott minden egyes létrehozott üzenet nem fog üzeneteket kézbesíteni a célként megadott szolgáltatásnak.

**Workaround** Állítson le minden olyan tevékenységet, amely több adatbázison Service Broker párbeszédet használ a szolgáltatási réteg frissítése előtt, majd újra inicializálja őket. Ha a szolgáltatási szintek változása után még nem elérhető üzenetek vannak, olvassa el a forrás-üzenetsor üzeneteit, és küldje el újra a cél várólistára.

### <a name="impersonification-of-aad-login-types-is-not-supported"></a>A HRE bejelentkezési típusok megszemélyesítése nem támogatott

**Dátum** Július 2019

A következő HRE- `EXECUTE AS USER` rendszerbiztonsági tag vagy `EXECUTE AS LOGIN` a használatával történő megszemélyesítés nem támogatott:
-   Alias HRE-felhasználók. Ebben az esetben `15517`a következő hibaüzenetet adja vissza.
- HRE-bejelentkezések és felhasználók HRE-alkalmazások vagy egyszerű szolgáltatások alapján. Ebben az esetben `15517` a következő hibákat adja vissza: `15406`és.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@querya paraméter nem támogatott a sp_send_db_mail

**Dátum** Április 2019

A `@query` [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) eljárásban szereplő paraméter nem működik.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A tranzakciós replikációt újra kell konfigurálni a Geo-feladatátvétel után

**Dátum** Mar 2019

Ha a tranzakciós replikáció engedélyezve van egy automatikus feladatátvételi csoportban lévő adatbázison, a felügyelt példány rendszergazdájának törölnie kell a régi elsődleges összes kiadványt, és újra kell konfigurálnia azokat az új elsődlegesen a feladatátvételt követően egy másik régióba. További részletekért lásd: [replikáció](#replication) .

### <a name="aad-logins-and-users-are-not-supported-in-tools"></a>A HRE-bejelentkezések és a felhasználók nem támogatottak az eszközökön

**Dátum** Jan 2019

SQL Server Management Studio és SQL Server Data Tools nem támogatja teljes mértékben az Azure Active Directory-bejelentkezéseket és-felhasználókat.
- Az Azure AD Server-rendszerbiztonsági tag (Logins) és a felhasználók (nyilvános előzetes verzió) használata SQL Server Data Tools jelenleg nem támogatott.
- SQL Server Management Studio nem támogatja az Azure AD-kiszolgáló résztvevői (bejelentkezések) és a felhasználók (nyilvános előzetes verzió) parancsfájlkezelését.

### <a name="temporary-database-is-used-during-restore-operation"></a>A VISSZAÁLLÍTÁSi művelet során a rendszer ideiglenes adatbázist használ

Ha egy adatbázist felügyelt példányon állít vissza, a visszaállítási szolgáltatás először egy üres adatbázist hoz létre a kívánt névvel a példányon a név lefoglalásához. Némi idő elteltével ez az adatbázis el lesz dobva, és a rendszer elindítja a tényleges adatbázis visszaállítását. A *visszaállítási* állapotban lévő adatbázis neve helyett véletlenszerű GUID-értékkel fog rendelkezni. Az ideiglenes név az `RESTORE` utasításban megadott kívánt névre változik a visszaállítási folyamat befejeződése után. A kezdeti fázisban a felhasználó hozzáférhet az üres adatbázishoz, és akár táblákat is létrehozhat, vagy betöltheti az adatbázist. Ezt az ideiglenes adatbázist a rendszer elveti, ha a visszaállítási szolgáltatás elindítja a második fázist.

**Áthidaló megoldás**: Ne nyissa meg a visszaállítani kívánt adatbázist, amíg nem látja, hogy a visszaállítás befejeződött.

### <a name="tempdb-structure-and-content-is-re-created"></a>A TEMPDB szerkezete és tartalma újra létrejön

Az `tempdb` adatbázis mindig 12 adatfájlra van bontva, és a fájl szerkezete nem módosítható. A fájlok maximális mérete nem módosítható, és az új fájlok nem adhatók hozzá `tempdb`a következőhöz:. `Tempdb`a rendszer mindig üres adatbázisként hozza létre újra, amikor a példány elindul vagy feladatátvételt végez, és a folyamatban `tempdb` lévő módosításokat nem őrzi meg a rendszer.

### <a name="exceeding-storage-space-with-small-database-files"></a>Kis méretű adatbázisfájlok esetén a tárterület nagyobb

`CREATE DATABASE`a `ALTER DATABASE ADD FILE`, és `RESTORE DATABASE` az utasítások sikertelenek lehetnek, mert a példány elérheti az Azure Storage-korlátot.

Az egyes általános célú felügyelt példányok esetében akár 35 TB tárterület is rendelkezésre áll az Azure Premium lemezterület számára. Minden adatbázisfájl külön fizikai lemezre kerül. A lemezek mérete 128 GB, 256 GB, 512 GB, 1 TB vagy 4 TB lehet. A lemez nem használt lemezterülete nem számít fel díjat, de az Azure Premium-lemezek teljes mérete nem haladhatja meg a 35 TB-ot. Bizonyos esetekben előfordulhat, hogy egy felügyelt példány, amely nem igényel 8 TB-ot, a belső töredezettség miatt túllépheti a 35 TB-os Azure-korlátot a tárterületen.

Előfordulhat például, hogy egy általános célú felügyelt példány egy 4 TB-os lemezre helyezett 1,2 TB-os méretű fájllal rendelkezik. Az is előfordulhat, hogy a 248-es fájlok mérete 1 GB, amely külön 128 GB-os lemezekre van helyezve. Ebben a példában:

- A lefoglalt tárterület teljes mérete 1 x 4 TB + 248 x 128 GB = 35 TB.
- A példányon lévő adatbázisok teljes lefoglalt területe 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Ez a példa azt szemlélteti, hogy bizonyos körülmények között a fájlok adott eloszlása miatt a felügyelt példányok elérheti a csatlakoztatott Azure Premium-lemez számára fenntartott 35 TB-os korlátot, ha előfordulhat, hogy nem várható.

Ebben a példában a meglévő adatbázisok továbbra is működőképesek maradnak, és bármilyen probléma nélkül növekednek, amíg új fájlok nem lettek hozzáadva. Új adatbázisok nem hozhatók létre és nem állíthatók vissza, mert nincs elég hely az új lemezmeghajtók számára, még akkor is, ha az összes adatbázis teljes mérete nem éri el a példány méretkorlát-korlátját. Az ebben az esetben visszaadott hiba nem egyértelmű.

[A fennmaradó fájlok számát](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) a rendszernézetek használatával is meghatározhatja. Ha eléri ezt a korlátot, próbálja meg [üresen hagyni, és töröljön néhány kisebb fájlt a DBCC SHRINKFILE utasítás használatával](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) , vagy váltson a [üzletileg kritikus szintjére, amely nem rendelkezik ezzel](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)a korláttal.

### <a name="guid-values-shown-instead-of-database-names"></a>Az adatbázis neve helyett GUID-értékek láthatók

A rendszernézetek, a teljesítményszámlálók, a hibaüzenetek, a Xevent típusú eseményekhez és a hibanapló-bejegyzések a tényleges adatbázis neve helyett GUID-adatbázis-azonosítókat jelenítenek meg. Ne használja ezeket a GUID azonosítókat, mert azokat a rendszer a későbbiekben a tényleges adatbázis-nevekkel cseréli le.

### <a name="error-logs-arent-persisted"></a>A hibanapló nem marad meg

A felügyelt példányban elérhető naplók nem maradnak meg, és a méreteik nem szerepelnek a maximális tárolási korlátban. Előfordulhat, hogy a naplók automatikusan törlődnek, ha a feladatátvétel történik. Hiányosságok merülhetnek fel a hibák naplózási előzményeiben, mivel a felügyelt példányok többször is át lettek helyezve több virtuális gépre.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Ugyanazon példányon belül két adatbázis tranzakciós hatóköre nem támogatott

A `TransactionScope` .net-osztály nem működik, ha két lekérdezést küldenek ugyanazon példányon belül két adatbázisnak ugyanazon tranzakció hatókörében:

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

**Workaround** A [SqlConnection. ChangeDatabase (string)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) használatával használjon másik adatbázist kapcsolati környezetben a két kapcsolat használata helyett.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>A CLR-modulok és a csatolt kiszolgálók időnként nem hivatkozhatnak helyi IP-címekre.

A felügyelt példányba helyezett CLR-modulok, valamint az aktuális példányra hivatkozó elosztott lekérdezések nem tudják feloldani a helyi példányok IP-címét. Ez a hiba átmeneti probléma.

**Workaround** Ha lehetséges, használjon egy CLR-modul környezeti kapcsolatait.

## <a name="next-steps"></a>További lépések

- A felügyelt példányokkal kapcsolatos további információkért lásd: [Mi az a felügyelt példány?](sql-database-managed-instance.md)
- A szolgáltatások és összehasonlítások listájáért lásd: [Azure SQL Database szolgáltatások összehasonlítása](sql-database-features.md).
- Az új felügyelt példányok létrehozásának módját bemutató rövid útmutató: [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
