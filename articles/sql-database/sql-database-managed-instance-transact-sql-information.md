---
title: Azure SQL Database felügyelt példány T-SQL különbségek |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure SQL Database felügyelt példány és az SQL Server T-SQL eltérései
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 08920a25fc7213a773ef0d76a5daddbab3f765c2
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866867"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Az SQL Serverről Azure SQL Database felügyelt példány T-SQL különbségek

Ez a cikk összefoglalja, és ismerteti a különbségeket a szintaxist és a viselkedés az Azure SQL Database felügyelt példánya és a helyszíni SQL Server Database Engine között. A következő témákat tárgyalja: <a name="Differences"></a>

- [Rendelkezésre állási](#availability) tartalmazza a különbségeket [Always-On](#always-on-availability) és [biztonsági mentések](#backup).
- [Biztonsági](#security) tartalmazza a különbségeket [naplózás](#auditing), [tanúsítványok](#certificates), [hitelesítő adatok](#credential), [kriptográfiai szolgáltatókat](#cryptographic-providers), [bejelentkezések és felhasználók](#logins-and-users), és a [Szolgáltatáskulcs és a szolgáltatás főkulcsának](#service-key-and-service-master-key).
- [Konfigurációs](#configuration) tartalmazza a különbségeket [kiterjesztés puffer](#buffer-pool-extension), [rendezést](#collation), [kompatibilitási szinteken](#compatibility-levels), [adatbázis-tükrözés ](#database-mirroring), [adatbázis-beállítások](#database-options), [SQL Server Agent](#sql-server-agent), és [lehetőségek tábla](#tables).
- [Funkciók](#functionalities) tartalmaz [TÖMEGES beszúrási vagy OPENROWSET](#bulk-insert--openrowset), [CLR-beli](#clr), [DBCC](#dbcc), [elosztott tranzakciók](#distributed-transactions), [bővített események](#extended-events), [külső kódtáraiban](#external-libraries), [filestream- és FileTable](#filestream-and-filetable), [szemantikai teljes szöveges keresés](#full-text-semantic-search), [csatolt kiszolgálók](#linked-servers), [PolyBase](#polybase), [replikációs](#replication), [VISSZAÁLLÍTÁSA](#restore-statement), [Service Broker](#service-broker), [tárolt eljárások, függvények és eseményindítók](#stored-procedures-functions-and-triggers).
- [Az funkciók eltérő viselkedéssel rendelkező felügyelt példányok](#Changes).
- [Ideiglenes korlátozásai és ismert problémák](#Issues).

A felügyelt példány üzembe helyezési lehetőséget biztosít nagy mértékben kompatibilis a helyszíni SQL Server Database Engine. Felügyelt példány az SQL Server adatbázismotor-funkciók a legtöbb támogatottak.

![Migrálás](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Rendelkezésre állás

### <a name="always-on-availability"></a>Always On

[Magas rendelkezésre állású](sql-database-high-availability.md) felügyelt példányon be van építve, és a felhasználók nem vezérelhető. Az alábbi utasítások nem támogatottak:

- [VÉGPONT LÉTREHOZÁSA... A DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [RENDELKEZÉSRE ÁLLÁSI CSOPORT LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [KÖZVETLEN RENDELKEZÉSRE ÁLLÁSI CSOPORT](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- A [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) záradékában a [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) utasítás

### <a name="backup"></a>Backup

Felügyelt példány automatikus biztonsági mentést, rendelkezik, így a felhasználók létrehozhatnak a teljes adatbázis `COPY_ONLY` biztonsági mentéseket. Különbségi, a napló és a fájl pillanatképes biztonsági mentései nem támogatottak.

- Egy felügyelt példányt akkor készíthető-példány adatbázis csak egy Azure Blob storage-fiók:
  - Csak `BACKUP TO URL` használata támogatott.
  - `FILE`, `TAPE`, és a biztonsági mentési eszközök nem támogatottak.
- A legtöbb általános `WITH` beállítások támogatottak.
  - `COPY_ONLY` megadása kötelező.
  - `FILE_SNAPSHOT` nem támogatott.
  - Szalagbeállítások: `REWIND`, `NOREWIND`, `UNLOAD`, és `NOUNLOAD` nem támogatottak.
  - Napló-specifikus beállítások: `NORECOVERY`, `STANDBY`, és `NO_TRUNCATE` nem támogatottak.

Korlátozások: 

- Egy felügyelt példányt, akkor készíthető-példány adatbázis legfeljebb 32 csíkokkal, amely elegendő az adatbázisok biztonsági akár 4 TB-os biztonságimásolat-tömörítési funkciók használata esetén.
- A biztonsági mentési stripe maximális mérete használatával a `BACKUP` parancs a felügyelt példány 195 GB, amely a blob maximális mérete. A biztonsági mentési parancsban egyes stripe méret csökkentése és belül ezt a korlátot csíkokkal számának növelése.

    > [!TIP]
    > A probléma megoldásához, ha a helyszíni környezetben vagy egy virtuális gépen vagy SQL Server biztonsági másolat készítése, a következőket teheti:
    >
    > - Biztonsági másolat `DISK` helyett biztonsági mentést `URL`.
    > - A biztonságimásolat-fájlok feltöltése a Blob storage.
    > - A visszaállítási másolatát a felügyelt példányon.
    >
    > A `Restore` parancs a felügyelt példány támogatja a nagyobb méretű blobok a biztonságimásolat-fájlokat, mert egy másik blob típusa a feltöltött biztonságimásolat-fájlok tárolására szolgál.

Biztonsági másolatok a T-SQL használatával kapcsolatos információkért lásd: [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Biztonság

### <a name="auditing"></a>Naplózás

A naplózás az Azure SQL Database és az adatbázisok az SQL Server-adatbázisok közötti fő különbségeket a következők:

- Az Azure SQL Database felügyelt példány üzembe helyezési lehetősége, a naplózást a kiszolgáló szintjén működik. A `.xel` naplófájlok tárolási az Azure Blob storage-ban.
- Az önálló adatbázisok és az Azure SQL Database rugalmas készlet üzembe helyezési lehetőséget, a naplózás az adatbázis szintjén működik.
- A helyszíni SQL Server vagy a virtuális gépek, a naplózás a kiszolgáló szintjén működik. Események fájlrendszerből vagy a Windows-eseménynaplók tárolja.
 
Az XEvent naplózási a felügyelt példány támogatja az Azure Blob storage tárolók. A fájl- és Windows-naplók nem támogatottak.

A kulcs közötti különbségek a `CREATE AUDIT` vannak a naplózás az Azure Blob storage-szintaxissal:

- Egy új szintaxis `TO URL` feltéve, hogy a segítségével adja meg az URL-címét az Azure Blob storage-tárolóba, a `.xel` fájlok kerülnek.
- A szintaxist `TO FILE` nem támogatott, mert a felügyelt példány nem érhető el Windows-fájlmegosztásokon.

További információkért lásd: 

- [KISZOLGÁLÓ NAPLÓZÁSI LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [AZ ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Tanúsítványok

Felügyelt példány nem férnek hozzá, fájlmegosztások és Windows-mappák, így a következő korlátozások vonatkoznak:

- A `CREATE FROM` / `BACKUP TO` a fájl nem támogatott a tanúsítványok.
- A `CREATE` / `BACKUP` származó tanúsítvány `FILE` / `ASSEMBLY` nem támogatott. Titkos kulcs fájljai nem használható. 

Lásd: [tanúsítvány létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) és [biztonsági MENTÉSI tanúsítvány](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Megkerülő megoldás**: A tanúsítvány vagy a titkos kulcs parancsfájl, .sql fájlt tárolja és a bináris létrehozása:

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

### <a name="logins-and-users"></a>Bejelentkezések és felhasználók

- Létrehozott SQL-bejelentkezésekben `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, és `FROM SID` támogatottak. Lásd: [létrehozás bejelentkezési](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Az Azure Active Directory (Azure AD-) kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) használatával létrehozott a [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) szintaktikai vagy a [LÉTREHOZNI felhasználó a bejelentkezési [Azure AD bejelentkezési]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) szintaxis használata támogatott (nyilvános előzetes verzió). Ezek a bejelentkezések jönnek létre a kiszolgáló szintjén.

    Felügyelt példány támogatja az Azure ad-ben adatbázis résztvevőivel szintaxissal `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Ez a funkció Azure ad-ben tárolt adatbázis-felhasználók is nevezik.

- A létrehozott Windows-bejelentkezések a `CREATE LOGIN ... FROM WINDOWS` szintaxis nem támogatott. Használja az Azure Active Directory-bejelentkezések és felhasználók.
- Az Azure AD-felhasználó, aki létrehozta a példány rendelkezik [rendszergazdai jogosultságokat unrestricted](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Az Azure AD nem rendszergazdai adatbázisszintű felhasználók használatával hozható létre a `CREATE USER ... FROM EXTERNAL PROVIDER` szintaxist. Lásd: [felhasználó létrehozása... KÜLSŐ SZOLGÁLTATÓI](sql-database-manage-logins.md#non-administrator-users).
- Az Azure AD kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) belül csak egyetlen felügyelt példány az SQL-funkciók támogatása. Kereszt-példány kapcsolati, függetlenül attól, hogy e azok van belül ugyanazt az Azure AD-bérlővel vagy más bérlők igénylő szolgáltatások az Azure AD-felhasználók nem támogatottak. Ilyen szolgáltatások például a következők:

  - Az SQL tranzakciós replikáció.
  - Hivatkozás a kiszolgáló.

- A beállítás az Azure AD bejelentkezési leképezve az Azure AD-csoportok, az adatbázis tulajdonosa nem támogatott.
- Más Azure AD rendszerbiztonsági tagok az Azure ad-ben kiszolgálószintű rendszerbiztonsági tagok közül megszemélyesítési támogatják, mint például a [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) záradékban. Az EXECUTE AS korlátozások a következők:

  - Az EXECUTE AS USER nem támogatott az Azure AD-felhasználók, ha a neve eltér a bejelentkezési név. Ilyen például, amikor a felhasználó létrehozása [myAadUser] felhasználó létrehozása a LOGIN-szintaxis használatával [john@contoso.com] megszemélyesítési EXEC AS USER történik meg, és = _myAadUser_. Amikor létrehoz egy **felhasználói** , egy Azure ad-ben kiszolgálói tag (bejelentkezés), adja meg a felhasználónév az azonos login_name a **bejelentkezési**.
  - Csak az SQL kiszolgálószintű rendszerbiztonsági tagok (Bejelentkezések) részét képező a `sysadmin` szerepkör hajthat végre a következő műveleteket az Azure AD rendszerbiztonsági tagok célzó:

    - HAJTSA VÉGRE A FELHASZNÁLÓ NEVÉBEN
    - HAJTSA VÉGRE A BEJELENTKEZÉSSEL

- Az Azure AD-kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) nyilvános előzetes verzió korlátozásai:

  - Felügyelt példány Active Directory felügyeleti korlátozásai:

    - Az Azure AD-rendszergazda állíthatja be a következő felügyelt példányt az Azure ad-ben Server egyszerű (bejelentkezés) belül a felügyelt példány nem használható. Létre kell hoznia az első Azure AD kiszolgálói tag (bejelentkezés) SQL Server-fiók használatával meg egy `sysadmin` szerepkör. Miután az Azure AD-kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) általánosan elérhetővé válik a ez átmeneti korlátozás törlődni fog. Ha meg a bejelentkezés létrehozása az Azure AD rendszergazdai fiókot használja, a következő hiba jelenik meg: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Jelenleg a standard szintű SQL Server-fiók (nem Azure AD) használatával létre kell hozni az első Azure AD-bejelentkezés a master adatbázisban létrehozott meg egy `sysadmin` szerepkör használatával [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) az EXTERNAL PROVIDER. Általános elérhetőség utáni ezt a korlátozást törlődni fog. Létrehozhat egy kezdeti az Active Directory-rendszergazda által felügyelt példány használatával az Azure AD bejelentkezési.
    - Az Azure AD-bejelentkezések használják az SQL Server Management Studio vagy az SqlPackage DacFx (Exportálás/importálás) nem támogatott. Ezt a korlátozást a rendszer eltávolítja, miután az Azure AD-kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) általánosan elérhetővé válik.
    - Az SQL Server Management Studio használatával az Azure AD-kiszolgáló rendszerbiztonsági tagok (Bejelentkezések):

      - Az Azure AD bejelentkezések, amelyek minden hitelesített bejelentkezési parancsfájlok nem támogatott.
      - IntelliSense az EXTERNAL PROVIDER a bejelentkezés létrehozása utasítás nem ismeri fel, és a egy piros aláhúzás megjeleníti.

- Csak a kiszolgálószintű fő bejelentkezéssel, például a kiépítési folyamat, a kiszolgálói szerepkörök tagjai a felügyelt példány által létrehozott `securityadmin` vagy `sysadmin`, vagy más bejelentkezések ALTER ANY LOGIN engedéllyel a kiszolgáló szintjén hozhat létre az Azure ad-ben kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) a felügyelt példány a master adatbázisban.
- Ha a bejelentkezés egy SQL egyszerű, csak a bejelentkezések, amelyek részét képezik a `sysadmin` szerepkör létrehozás bejelentkezések a create parancs használható egy Azure AD-fiókot.
- Az Azure AD bejelentkezési belül ugyanabban a könyvtárban, amely Azure SQL Database felügyelt példány szolgál az Azure AD tagjának kell lennie.
- Az Azure AD kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) már az Object Explorerben kezdve az SQL Server Management Studio 18.0 preview 5 jelennek meg.
- Átfedésben lévő Azure AD kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) az Azure AD rendszergazdai fiók használata engedélyezett. Az Azure AD kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) elsőbbséget élveznek az Azure AD-rendszergazda oldja meg az egyszerű és engedélyek alkalmazása a következő felügyelt példányt.
- A hitelesítés során a rendszer alkalmazza a következő feladatütemezési oldja meg a hitelesítő egyszerű:

    1. Ha az Azure AD-fiók létezik közvetlenül leképezve a az Azure AD-kiszolgálói tag (bejelentkezés), amely megtalálható a sys.server_principals "E" típusú, és hozzáférést biztosít engedélyeit az Azure ad-ben kiszolgálói tag (bejelentkezés) a alkalmazni.
    2. Ha az Azure AD-fiók le van képezve a az Azure AD-kiszolgálói tag (bejelentkezés), amely megtalálható a sys.server_principals, írja be az "X", az Azure AD-csoport tagja hozzáférést, a alkalmazni az Azure AD-csoport bejelentkezési engedéllyel.
    3. Ha az Azure AD-fiók egy speciális portálon konfigurált Azure AD-rendszergazda számára a felügyelt példány, amely nem szerepel a felügyelt példány rendszernézetek, alkalmazása speciális rögzített engedélyeit az Azure AD-rendszergazda a felügyelt példány (örökölt mód) számára.
    4. Ha az Azure AD-fiók létezik, közvetlenül csatlakoztatott, az Azure AD-felhasználót egy adatbázis, amely megtalálható a sys.database_principals "E" típusú, a hozzáférést, engedélyeit az Azure ad-ben adatbázis-felhasználót a alkalmazni.
    5. Ha az Azure AD-fiók le van képezve egy adatbázis, amely megtalálható a sys.database_principals típusa "X", az Azure AD-felhasználót az Azure AD-csoport tagja hozzáférést, a alkalmazni az Azure AD-csoport bejelentkezési engedéllyel.
    6. Ha egy Azure AD bejelentkezési vagy egy Azure AD-felhasználói fiókot, vagy egy adott csoport oldja fel a felhasználó hitelesíti magát, az Azure AD fiókhoz csatlakoztatott minden engedélyeit az Azure AD bejelentkezési érvényesek.

### <a name="service-key-and-service-master-key"></a>Kulcs és a szolgáltatás főkulcsának szolgáltatás

- [Master key backup](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) (SQL Database szolgáltatás által kezelt) nem támogatott.
- [A főkulcs visszaállítási](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) (SQL Database szolgáltatás által kezelt) nem támogatott.
- [Szolgáltatás főkulcsának biztonsági mentés](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) (SQL Database szolgáltatás által kezelt) nem támogatott.
- [Szolgáltatás főkulcsának visszaállítása](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) (SQL Database szolgáltatás által kezelt) nem támogatott.

## <a name="configuration"></a>Konfiguráció

### <a name="buffer-pool-extension"></a>A pufferkészlet-kiterjesztés

- [A puffer kiterjesztés](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nem támogatott.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` nem támogatott. Lásd: [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Rendezés

Az alapértelmezett példány rendezése nem `SQL_Latin1_General_CP1_CI_AS` és létrehozási paraméterként is megadhatók. Lásd: [rendezések](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Kompatibilitási szintek

- Támogatott kompatibilitási szintek a következők: 100, 110-es, 120, 130 és 140.
- Nem támogatottak a 100 alatti kompatibilitási szinteken.
- Új adatbázis alapértelmezett kompatibilitási szintje 140. A visszaállított adatbázis kompatibilitási szintje változatlan marad, ha 100 volt, vagy újabb.

Lásd: [ALTER adatbázis-kompatibilitási szint](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Adatbázis-tükrözés

Az adatbázis-tükrözés nem támogatott.

- `ALTER DATABASE SET PARTNER` és `SET WITNESS` beállítások nem támogatottak.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` nem támogatott.

További információkért lásd: [ALTER DATABASE SET PARTNER és a SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) és [VÉGPONT létrehozása... A DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Adatbázis-beállítások

- Több naplófájl nem támogatottak.
- Az általános célú szolgáltatásszinten lévő memórián belüli objektumok nem támogatottak. 
- Általános célú példányonként, ami azt jelenti, 280 fájlt adatbázisonként legfeljebb 280 fájlok korlátozva van. Az általános célú csomagban egyaránt adathoz és naplófájlhoz számításba vesszük ezt a korlátot felé. [Üzletileg kritikus támogatja – 32 767 fájlt adatbázisonként](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Az adatbázis nem tartalmazhatnak filestream-adatokat tartalmazó fájlcsoportokat. Ha .bak tartalmaz visszaállítása `FILESTREAM` adatokat. 
- Minden fájl kerül, az Azure Blob storage-ban. I/o és teljesítmény / fájl egyes fájlok méretét függenek.

#### <a name="create-database-statement"></a>CREATE DATABASE utasítás

Az alábbi korlátozások érvényesek a `CREATE DATABASE`:

- Fájlok és fájlcsoportok nem definiálható. 
- A `CONTAINMENT` beállítás nem támogatott. 
- `WITH` beállítások nem támogatottak. 
   > [!TIP]
   > Áthidaló megoldásként használja `ALTER DATABASE` után `CREATE DATABASE` -fájlok hozzáadása, vagy állítsa be a tartalmazási adatbázis beállításainak megadása. 

- A `FOR ATTACH` beállítás nem támogatott.
- A `AS SNAPSHOT OF` beállítás nem támogatott.

További információkért lásd: [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Az ALTER DATABASE utasítás

Egyes fájlok tulajdonságai nem állíthatók be, vagy nem módosította:

- Egy fájl elérési útja nem adható meg a `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL-utasítással. Távolítsa el `FILENAME` a parancsfájlból, mert a felügyelt példány automatikusan helyezi a fájlokat. 
- A fájl neve nem módosítható az a `ALTER DATABASE` utasítást.

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

További információkért lásd: [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Az SQL Server Agent-beállítások csak olvashatók. Az eljárás `sp_set_agent_properties` a felügyelt példány nem támogatott. 
- Feladatok
  - T-SQL-feladat lépései támogatottak.
  - A következő replikációs feladatok támogatottak:
    - Replikálásitranzakciónapló-olvasó
    - Pillanatkép
    - Terjesztő
  - Feladat lépései SSIS használata támogatott.
  - Feladat lépései más típusú jelenleg nem támogatottak:
    - Az egyesítéses replikáció feladat lépésének nem támogatott. 
    - Üzenetsor-olvasó nem támogatott. 
    - Parancs-rendszerhéj még nem támogatott.
  - Felügyelt példány nem fér hozzá a külső erőforrásokhoz, például hálózati megosztásokon keresztül robocopy. 
  - SQL Server Analysis Services nem támogatottak.
- Értesítések részlegesen támogatott.
- E-mail-értesítés támogatott, de ehhez szükséges, hogy az adatbázisbeli levelezés profil konfigurálása. Az SQL Server Agent használhatja az adatbázisbeli levelezés csak egy profil, és kell meghívni `AzureManagedInstance_dbmail_profile`. 
  - Személyi hívó nem támogatott. 
  - NetSend nem támogatott.
  - Riasztások még nem támogatottak.
  - Proxyk nem támogatottak. 
- Eseménynapló nem támogatott.

A következő funkciók jelenleg nem támogatottak, de engedélyezve lesz a jövőben:

- Proxyk
- Feladatütemezés egy tétlen processzor
- Engedélyezés vagy letiltás ügynök
- Riasztások

Az SQL Server Agent kapcsolatos információkért lásd: [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Táblák

Az alábbi táblázatok nem támogatottak:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

Hozzon létre és táblák kapcsolatos információkért lásd: [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) és [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkciók

### <a name="bulk-insert--openrowset"></a>Tömeges beszúrás / openrowset

Felügyelt példány nem elérhető fájlmegosztásokat és a Windows-mappák, így a fájlokat kell importálni az Azure Blob storage-ból:

- `DATASOURCE` azért van szükség a `BULK INSERT` parancsot, miközben importálja a fájlokat az Azure Blob storage-ból. Lásd: [TÖMEGES Beszúrás](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` azért van szükség a `OPENROWSET` működni, amikor egy fájl tartalmát olvassa el az Azure Blob storage-ból. Lásd: [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR-BELI

Felügyelt példány nem férnek hozzá, fájlmegosztások és Windows-mappák, így a következő korlátozások vonatkoznak:

- Csak `CREATE ASSEMBLY FROM BINARY` használata támogatott. Lásd: [CREATE ASSEMBLY BINÁRISRÓL](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` nem támogatott. Lásd: [CREATE ASSEMBLY FÁJLBÓL](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` nem lehet hivatkozni a fájlokat. Lásd: [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Felügyelt példányok, amelyek engedélyezve vannak az SQL Server nem dokumentált DBCC-utasítások nem támogatottak.

- `Trace flags` nem támogatottak. Lásd: [nyomkövetési jelzők](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` nem támogatott. Lásd: [DBCC traceoff utasítás használatával](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` nem támogatott. Lásd: [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Elosztott tranzakciók

Az MSDTC és [rugalmas tranzakciók](sql-database-elastic-transactions-overview.md) a felügyelt példányok jelenleg nem támogatottak.

### <a name="extended-events"></a>Bővített események

Bővített események (XEvents) egyes Windows-specifikus célok nem támogatottak:

- A `etw_classic_sync` cél nem támogatott. Store `.xel` fájlokat az Azure Blob storage-ban. Lásd: [etw_classic_sync cél](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- A `event_file` cél nem támogatott. Store `.xel` fájlokat az Azure Blob storage-ban. Lásd: [event_file cél](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Külső kódtárak

Adatbázis-R és Python, a támogatott, még külső kódtáraiban nem. Lásd: [SQL Server Machine Learning-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>A FileStream és FileTable

- nem támogatott a FileStream-adatokat.
- Az adatbázis nem tartalmazhat fájlcsoportok `FILESTREAM` adatokat.
- `FILETABLE` nem támogatott.
- Táblák nem tartalmazhatnak `FILESTREAM` típusokat.
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

- Támogatott célok a következők: SQL Server és SQL Database.
- Tárolók nem támogatottak olyan fájlok, Analysis Services és más relációsadatbázis-kezelő rendszer.

Műveletek

- Kereszt-példány írási tranzakciók nem támogatottak.
- `sp_dropserver` a csatolt kiszolgáló elvetését esetén támogatott. Lásd: [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- A `OPENROWSET` függvény csak az SQL Server-példányokat a lekérdezések végrehajtásához használható. Ezek lehetnek a felügyelt, a helyszínen, vagy a virtuális gépeken. Lásd: [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- A `OPENDATASOURCE` függvény csak az SQL Server-példányokat a lekérdezések végrehajtásához használható. Ezek lehetnek a felügyelt, a helyszínen, vagy a virtuális gépeken. Csak a `SQLNCLI`, `SQLNCLI11`, és `SQLOLEDB` szolgáltatóként értékek támogatottak. Például: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Lásd: [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

Külső táblák a HDFS- vagy Azure Blob storage-ban a fájlok nem támogatottak a hivatkozás. A PolyBase kapcsolatos információkért lásd: [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikáció

Replikáció a felügyelt példány nyilvános előzetes verziója érhető el. A replikációval kapcsolatos további információkért lásd: [SQL Server-replikáció](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>Utasítás VISSZAÁLLÍTÁSA 

- Támogatott szintaxist:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nem támogatott szintaxist:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Forrás: 
  - `FROM URL` (Az azure Blob storage) az egyetlen támogatott lehetőség.
  - `FROM DISK`/`TAPE`/ biztonsági mentési eszköz nem támogatott.
  - Biztonságimásolat-készletek nem támogatottak.
- `WITH` beállítások nem támogatottak, például nem `DIFFERENTIAL` vagy `STATS`.
- `ASYNC RESTORE`: Visszaállítás továbbra is fennáll, akkor is, ha az ügyfél kapcsolata megszakad. Ha a kapcsolat megszakad, ellenőrizheti a `sys.dm_operation_status` egy visszaállítási művelet állapotának és a egy létrehozása és a DROP database megtekintése. Lásd: [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Az alábbi adatbázis beállítása vagy felülírni, és később nem módosítható: 

- `NEW_BROKER` Ha a közvetítő a .bak-fájl nincs engedélyezve. 
- `ENABLE_BROKER` Ha a közvetítő a .bak-fájl nincs engedélyezve. 
- `AUTO_CLOSE=OFF` Ha egy adatbázist a .bak kiterjesztésű fájlt a `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` Ha egy adatbázist a .bak kiterjesztésű fájlt a `SIMPLE` vagy `BULK_LOGGED` helyreállítási módot.
- A memóriaoptimalizált fájlcsoportnak vesznek és XTP meghív, ha ez nem a .bak forrásfájl. 
- Minden olyan meglévő memóriaoptimalizált fájlcsoportnak XTP új neve. 
- `SINGLE_USER` és `RESTRICTED_USER` beállítások alakulnak `MULTI_USER`.

Korlátozások: 

- `.BAK` több biztonságimentés-készlet tartalmazó fájlok nem állíthatók vissza. 
- `.BAK` több naplófájlt tartalmazó fájlok nem állíthatók vissza.
- Ha .bak tartalmaz visszaállítása `FILESTREAM` adatokat.
- Egy általános célú példányon nem lehet visszaállítani a biztonsági mentések, amelyek aktív memórián belüli objektumok, amelyek adatbázisokat tartalmaznak. Restore utasítások kapcsolatos információkért lásd: [RESTORE utasítások](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker

Kereszt-példány service broker nem támogatott:

- `sys.routes`: Előfeltételként sys.routes listából kell kiválasztani a címet. A címnek kell lennie a helyi minden útvonalon. Lásd: [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Nem használhat `CREATE ROUTE` a `ADDRESS` eltérő `LOCAL`. Lásd: [létrehozás ÚTVONAL](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Nem használhat `ALTER ROUTE` a `ADDRESS` eltérő `LOCAL`. Lásd: [ALTER ÚTVONAL](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Tárolt eljárások, függvények és eseményindítók

- `NATIVE_COMPILATION` az általános célú csomagban nem támogatott.
- A következő [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) beállítások nem támogatottak: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` nem támogatott. Lásd: [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` nem támogatott. Lásd: [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` nem támogatott, mert az tartalmazza `sp_addextendedproc`  és `sp_dropextendedproc`. Lásd: [bővített tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db`, és `sp_detach_db` nem támogatottak. Lásd: [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), és [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Changes"></a> Viselkedésbeli változások

A következő változók, functions és a nézetek különböző eredményeket adja vissza:

- `SERVERPROPERTY('EngineEdition')` 8 értékét adja vissza. Ez a tulajdonság a felügyelt példány egyedileg azonosítja. Lásd: [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` NULL értéket ad vissza, mert a fogalom, mert a példány az SQL Server nem vonatkozik a felügyelt példány létezik. Lásd: [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` adja vissza "csatlakozási" nevű, például saját felügyelt instance.wcus17662feb9ce98.database.windows.net. Lásd: [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` például adja vissza "csatlakozási" nevű, `myinstance.domain.database.windows.net` a Tulajdonságok "name" és "data_source." Lásd: [SYS. KISZOLGÁLÓK](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` NULL értéket ad vissza, mert a fogalom, mert a szolgáltatás az SQL Server nem vonatkozik a felügyelt példány létezik. Lásd: [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` támogatott. Az Azure AD bejelentkezési sys.syslogins nem null értékű vissza. Lásd: [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` nem támogatott. A helytelen adatokat adja vissza, amely egy ismert hiba ideiglenes. Lásd: [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Issues"></a> Ismert problémák és korlátozások

### <a name="tempdb-size"></a>A TEMPDB mérete

A fájl maximális mérete `tempdb` egy általános célú szintjén magonként 24 GB-nál nagyobb lehet. A maximális `tempdb` üzletileg kritikus szintet mérete korlátozott, a példány tárolási mérettel. A `tempdb` adatbázis mindig van felosztva, amelyek 12 adatfájlokat. A maximális méret fájlonként nem módosítható, és új fájlokat is hozzáadhatók `tempdb`. Néhány lekérdezés előfordulhat, hogy vissza hibát, ha szükségük van a magonként 24 GB-nál több `tempdb`.

### <a name="cant-restore-contained-database"></a>Tartalmazott adatbázis nem állítható vissza.

Felügyelt példány nem állítható vissza [tartalmazott adatbázisok](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). A már létező tartalmazott adatbázisok időponthoz visszaállítás nem működik a felügyelt példányon. A probléma a hamarosan megszűnik. Addig is javasoljuk, hogy tartalmazási beállítását eltávolítja az adatbázisok, amelyek a felügyelt példányhoz kerülnek. Ne használja az éles adatbázis tartalmazási beállítását. 

### <a name="exceeding-storage-space-with-small-database-files"></a>Tárhely túllépése kis teljesítményigényű adatbázis-fájlokkal

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, és `RESTORE DATABASE` utasítások sikertelen lehet, mert a példány elérheti az Azure Storage-korlátot.

Egyes általános célú felügyelt példányok akár 35 TB tárterületet, az Azure Premium lemezterület számára fenntartott rendelkezik. Egyes adatbázisfájlok külön fizikai lemezen kerül. Adatlemez-méretet 128 GB-os, 256 GB, 512 GB, 1 TB vagy 4 TB-os lehet. A lemezen lévő szabad terület nem kell fizetnie, de az Azure prémium szintű lemezméretek teljes összege legfeljebb 35 TB. Bizonyos esetekben a felügyelt példány, amelyhez nincs szükség a 8 TB összesen meghaladhatja a 35 TB-os Azure korlátozza a tároló mérete miatt belső töredezettsége.

Ha például egy általános célú felügyelt példány előfordulhat 1,2 TB-os mérete 4 TB-os lemezt elhelyezett található egy fájl. Előfordulhat, hogy is rendelkezik 248 fájlt, amely minden 1 GB méretű külön 128 GB-os lemezeken vannak elhelyezve. Ebben a példában:

- Az összes lefoglalt lemezterület tárméret: x 4 1 TB + 248 x 128 GB = 35 TB.
- a példányon adatbázisok teljes lefoglalt terület: x 1.2 1 TB + 248 x 1 GB = 1,4 TB.

Ez a példa szemlélteti, hogy bizonyos körülmények esetén egy adott terjesztési fájlok, mert a felügyelt példány elérje előfordulhat, hogy a 35 TB-os korlátot, amely egy csatlakoztatott Azure prémium szintű lemez számára van fenntartva, amikor előfordulhat, hogy nem a várt.

Ebben a példában a meglévő adatbázisok továbbra is működni bármilyen probléma nélküli is megnő, mindaddig, amíg nem adja hozzá az új fájlokat. Új adatbázisok nem hozható létre vagy visszaállítani, mert nincs elég hely az új meghajtók, még akkor is, ha a példány maximális mérete nem éri el az összes adatbázis teljes méretét. A visszaadott hiba ebben az esetben nem egyértelmű.

Is [hátralévő fájlok számának meghatározásához](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) rendszernézetek használatával. Ha eléri a korlátot, próbálja meg [üres, és töröljön néhány a kisebb fájlok a DBCC SHRINKFILE utasítással](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) vagy váltson át a [fontos üzleti szint, amely ezt a korlátot](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>Az SAS-kulcs helytelen konfigurációja során adatbázis visszaállítása

`RESTORE DATABASE` hogy a .bak kiterjesztésű fájlt folyamatosan újrapróbálkozás előfordulhat, hogy a .bak olvasásához olvasási fájlt, és a egy hosszú idő után vissza hibát, ha a közös hozzáférésű jogosultságkód található a `CREDENTIAL` helytelen. Hajtsa végre a HEADERONLY VISSZAÁLLÍTÁSA előtt győződjön meg arról, hogy helyesen szerepel-e a SAS-kulcsának adatbázis visszaállítása.
Győződjön meg arról, hogy távolítsa el a vezető `?` az SAS-kulcsot, amely az Azure portal segítségével generálja a.

### <a name="tooling"></a>Eszköztámogatás

SQL Server Management Studio és az SQL Server Data Tools a közben férnek hozzá a felügyelt példány lehet a problémák.

- Az Azure AD-kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) és a felhasználók (nyilvános előzetes verzió) az SQL Server Data Tools használata jelenleg nem támogatott.
- Azure AD-kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) és a felhasználók számára (nyilvános előzetes verzió) parancsfájlok nem támogatott az SQL Server Management Studióban.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Az egyes nézetek, naplók és üzenetek helytelen adatbázis neve

Több rendszernézetek, teljesítményszámlálók, hibaüzenetek, xevent típusú eseményekhez és hibanapló-bejegyzést jelennek meg a GUID adatbázis-azonosítókat a tényleges adatbázis neve helyett. Ne hagyatkozzon ezeket az GUID azonosítókat, mivel azok már írni a tényleges adatbázisnevek a jövőben.

### <a name="database-mail"></a>Adatbázisbeli levelezés

A `@query` paramétert a [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) eljárás nem működik.

### <a name="database-mail-profile"></a>Adatbázisbeli levelezési profil

A Database Mail profil az SQL Server ügynök által használt kell meghívni `AzureManagedInstance_dbmail_profile`. Nem vonatkoznak korlátozások az egyéb az adatbázisbeli levelezés profil neve.

### <a name="error-logs-arent-persisted"></a>Hibanaplók nem megőrzött.

A felügyelt példány hibanaplókat nem megőrzött, és a méretét nem található meg a maximálisan megengedettet. Előfordulhat, hogy hibanaplók automatikusan törölni, ha a feladatátvételt.

### <a name="error-logs-are-verbose"></a>Hibanaplók részletes.

Felügyelt példány hibanaplók részletes információkat helyezi, és többsége nem kell figyelembe venni. Hibanaplók információ mennyisége a jövőben csökkennek.

**Megkerülő megoldás:** Egy egyéni eljárás segítségével olvassa el az egyes irreleváns bejegyzések kiszűrő hibanaplókat. További információkért lásd: [felügyelt példány – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>A két adatbázis belül ugyanazon tranzakció-hatókörben nem támogatott.

A `TransactionScope` osztály a .NET-es nem működik, ha a két adatbázis ugyanazon a ugyanazon tranzakció-hatókörben alatt belül küldi a két lekérdezéseket:

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

Bár ez a kód belül ugyanazon adatokkal dolgozik, ez szükséges az MSDTC.

**Megkerülő megoldás:** Használjon [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) egy másik database két kapcsolat használata helyett egy kapcsolati környezet használata.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR-beli modulokat, és néha a csatolt kiszolgálók nem hivatkozhat egy helyi IP-cím

Felügyelt példány és a csatolt kiszolgálók vagy az elosztott lekérdezések néha a jelenlegi példány hivatkozó helyezett CLR-beli modulok nem oldható fel az IP-címét egy helyi példányát. Ez a hiba átmeneti jellegű probléma.

**Megkerülő megoldás:** Ha lehetséges használata helyi kapcsolatok egy CLR-beli modulban.

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>Szolgáltatás által felügyelt kulccsal TDE-titkosítású adatbázisok nem támogatják a felhasználó által létrehozott biztonsági másolatokból

Nem hajtható végre `BACKUP DATABASE ... WITH COPY_ONLY` olyan adatbázisban, amely a szolgáltatás által kezelt transzparens adattitkosítási (TDE) van titkosítva. TDE szolgáltatás által kezelt biztonsági másolatának titkosítását egy belső kulccsal TDE kényszeríti. A kulcs nem exportálható, így a biztonsági mentés nem állítható vissza.

**Megkerülő megoldás:** Használja az automatikus biztonsági mentések és időponthoz visszaállítási vagy [ügyfél által felügyelt (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) helyette. Az adatbázis titkosítási is letilthatja.

## <a name="next-steps"></a>További lépések

- Felügyelt példányok kapcsolatos további információkért lásd: [Mi az a felügyelt példány?](sql-database-managed-instance.md)
- Az a funkciók és összehasonlító listában, lásd: [Azure SQL Database funkcióinak összehasonlítása](sql-database-features.md).
- Ehhez a rövid útmutatóhoz, amely bemutatja, hogyan hozhat létre egy új felügyelt példányt, lásd: [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
