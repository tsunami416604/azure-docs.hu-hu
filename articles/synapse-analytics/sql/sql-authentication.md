---
title: SQL-hitelesítés
description: Ismerje meg az SQL-hitelesítést az Azure Synapse Analytics szolgáltatásban.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 2b80efa30ac7e04b9eb21dd6f8a39ab4ee90adf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424852"
---
# <a name="sql-authentication"></a>SQL-hitelesítés

Az Azure Synapse Analytics két SQL-űrlaptényezővel rendelkezik, amelyek lehetővé teszik az erőforrás-felhasználás szabályozását. Ez a cikk azt ismerteti, hogy a két űrlaptényező hogyan szabályozza a felhasználói hitelesítést.

A Synapse SQL engedélyezéséhez két engedélyezési típust használhat:

- AAD-engedélyezés
- SQL-hitelesítés

Az AAD-engedélyezés az Azure Active Directoryra támaszkodik, és lehetővé teszi, hogy egyetlen hely legyen a felhasználókezeléshez. Az SQL-engedélyezés lehetővé teszi, hogy az örökölt alkalmazások jól ismert módon használják a Synapse SQL-t.

## <a name="administrative-accounts"></a>Felügyeleti számlák

Kettő rendszergazdaként működő felügyeleti fiók létezik (**Kiszolgálói rendszergazdai** és **Active Directory-rendszergazdai**). Az SQL-kiszolgáló rendszergazdai fiókjainak azonosításához nyissa meg az Azure Portalt, és keresse meg a Synapse SQL Tulajdonságok lapját.

![SQL Server-rendszergazdák](./media/sql-authentication/sql-admins.png)

- **Kiszolgálói rendszergazda**

  Amikor létrehoz egy Azure Synapse Analytics, ki kell jelölnie egy **kiszolgáló rendszergazdai bejelentkezés.** Az SQL-kiszolgáló ekkor létrehozza a fiókot a master adatbázis egyik bejelentkezési neveként. Ez a fiók SQL Server-hitelesítéssel csatlakozik (felhasználónévvel és jelszóval). Ilyen fiókból csak egy létezhet.

- **Azure Active Directory-rendszergazda**

  Egy Azure Active Directory-fiók (különálló vagy biztonságicsoport-fiók) is konfigurálható rendszergazdaként. Nem kötelező konfigurálni egy Azure AD-rendszergazda, de egy Azure AD-rendszergazda **konfigurálni kell,** ha azure AD-fiókok at szeretne használni a Synapse SQL-hez való csatlakozáshoz.

A **kiszolgáló-rendszergazda** és az **Azure AD-rendszergazdai** fiókok a következő jellemzőkkel rendelkeznek:

- Csak azok a fiókok, amelyek automatikusan csatlakozhatnak a kiszolgáló bármely SQL-adatbázisához. (Felhasználói adatbázishoz történő csatlakozáshoz a többi fióknak vagy az adatbázis tulajdonosának kell lennie, vagy felhasználói fiókkal kell rendelkeznie az adatbázisban.)
- Ezek a fiókok `dbo`-felhasználóként lépnek be a felhasználói adatbázisokba, és minden engedéllyel rendelkeznek az adatbázison belül. (A felhasználói adatbázis tulajdonosa szintén `dbo`-felhasználóként jelentkezik be.)
- Ne lépjen `master` be az `dbo` adatbázisba felhasználóként, és ne legyen ek a főkiszolgálón korlátozott engedélyekkel.
- **Nem** tagjai a szabványos `sysadmin` SQL Server rögzített kiszolgálói szerepkörnek, amely nem érhető el az SQL-adatbázisban.  
- Adatbázisokat, bejelentkezéseket, felhasználókat hozhat létre, módosíthat és dobhat le a főkiszolgálói IP-tűzfalszabályokban.
- Tagokat vehet fel `dbmanager` és `loginmanager` távolíthat el a szerepkörökhöz.
- Megtekintheti a `sys.sql_logins` rendszertáblát.

## <a name="sql-on-demand-preview"></a>SQL igény szerinti (előzetes verzió)

Az SQL-hez igény szerinti hozzáféréssel rendelkező felhasználók kezeléséhez kövesse az alábbi utasításokat.

Az SQL igény szerinti bejelentkezéséhez használja az alábbi szintaxist:

```sql
CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
-- or
CREATE LOGIN Mary@domainname.net FROM EXTERNAL PROVIDER;
```
Miután a bejelentkezés létezik, létrehozhat felhasználókat az egyes adatbázisok ban az SQL igény szerinti végpont, és adja meg a szükséges engedélyeket ezek a felhasználók számára. Használat létrehozásához a következő szintaxist használhatja:
```sql
CREATE USER Mary FROM LOGIN Mary;
-- or
CREATE USER Mary FROM LOGIN Mary@domainname.net;
-- or
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

A bejelentkezés és a felhasználó létrehozása után a normál SQL Server szintaxissal jogokat adhat meg.

## <a name="sql-pool"></a>SQL-készlet

### <a name="administrator-access-path"></a>Rendszergazdai hozzáférés elérési útja

Ha a kiszolgálószintű tűzfal megfelelően van konfigurálva, az **SQL Server-rendszergazda** és az **Azure Active Directory-rendszergazda** olyan ügyféleszközökkel csatlakozhat, mint az SQL Server Management Studio vagy az SQL Server Data Tools. Csak a legújabb verziójú eszközök teszik elérhetővé az összes rendelkezésre álló funkciót és képességet. 

Az alábbi ábra a két rendszergazdai fiók tipikus konfigurációját mutatja be:
 
![a két felügyeleti fiók konfigurációja](./media/sql-authentication/1sql-db-administrator-access.png)

Amikor nyitott portot használ a kiszolgálószintű tűzfalon, a rendszergazdák bármely SQL Database-adatbázishoz csatlakozhatnak.

### <a name="database-creators"></a>Adatbázis-létrehozók

Ezen rendszergazdai szerepkörök egyike a **dbmanager** szerepkör. Ezen szerepkör tagjai létrehozhatnak új adatbázisokat. A szerepkör használatához hozzon létre egy felhasználót az `master` adatbázisban, majd adja hozzá a felhasználót a **dbmanager** adatbázis-szerepkörhöz. 

Adatbázis létrehozásához a felhasználónak az `master` adatbázisba való SQL Server-bejelentkezésen alapuló felhasználónak kell lennie, vagy egy Azure Active Directory-felhasználó alapján tárolt adatbázis-felhasználónak kell lennie.

1. Rendszergazdai fiók használatával csatlakozzon az `master` adatbázishoz.
2. SQL Server hitelesítési bejelentkezés létrehozása a [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utasítás használatával. Mintautasítás:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Használjon erős jelszót a bejelentkezési vagy tartalmazottadatbázis-felhasználó létrehozásakor. További információkért lásd az [erős jelszavak](/sql/relational-databases/security/strong-passwords?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) létrehozását ismertető cikket.

   A teljesítmény javítása érdekében a bejelentkezéseket (a kiszolgálószintű elsődleges fiókokat) átmenetileg adatbázisszinten is gyorsítótárazza a rendszer. A hitelesítési gyorsítótár frissítésével kapcsolatban lásd a [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) használatát ismertető cikket.

3. Az `master` adatbázisban hozzon létre egy felhasználót a [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utasítás használatával. A felhasználó lehet egy Azure Active Directory-hitelesítést tartalmazó adatbázis-felhasználó (ha konfigurálta a környezetet az Azure AD-hitelesítéshez), vagy egy SQL Server-hitelesítést tartalmazó adatbázis-felhasználó, vagy sql server hitelesítési felhasználó sql server hitelesítési bejelentkezés alapján (az előző lépésben létrehozott) néven. Mintautasítások:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Adja hozzá az új felhasználót a `master` **dbmanager** adatbázisszerepkörhöz az [ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utasítás használatával. Mintautasítások:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary;
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```

   > [!NOTE]
   > A dbmanager egy, a master adatbázisban található adatbázis-szerepkör, így csak adatbázis-felhasználót adhat hozzá a dbmanager szerepkörhöz. Nem adhat kiszolgálószintű bejelentkezést az adatbázisszintű szerepkörökhöz.

5. Szükség esetén konfiguráljon egy tűzfalszabályt úgy, hogy az új felhasználó csatlakozhasson. (Előfordulhat, hogy az új felhasználóra már vonatkozik létező tűzfalszabály.)

Most a felhasználó csatlakozhat `master` az adatbázishoz, és új adatbázisokat hozhat létre. Az adatbázist létrehozó fiók az adatbázis tulajdonosává válik.

### <a name="login-managers"></a>Bejelentkezéskezelők

A másik rendszergazdai szerepkör a bejelentkezéskezelői szerepkör. Ezen szerepkör tagjai létrehozhatnak új bejelentkezéseket a master adatbázisban. Ha szeretné, elvégezheti ugyanezen lépéseket (bejelentkezést és felhasználót hozhat létre, és a felhasználót a **loginmanager** szerepkörhöz adhatja) annak érdekében, hogy egy felhasználó új bejelentkezéseket hozhasson létre a master adatbázisban. Bejelentkezésre általában nincs szükség, mivel a Microsoft tartalmazottadatbázis-felhasználók használatát javasolja, amelyek az adatbázis szintjén hitelesíthetők a bejelentkezéseken alapuló felhasználók használata helyett. További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) cikkben talál.

## <a name="non-administrator-users"></a>Nem rendszergazdai felhasználók

Általában a nem rendszergazdai fiókoknak nincs szükségük a master adatbázis elérésére. Hozzon létre tartalmazottadatbázis-felhasználókat az adatbázis szintjén a [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx) utasítással. 

A felhasználó Azure Active Directory-hitelesítésű tartalmazottadatbázis-felhasználó lehet (ha az Azure AD-hitelesítéshez konfigurálta a környezetét), vagy egy SQL Server-hitelesítésű tartalmazottadatbázis-felhasználó, illetve egy SQL Server-hitelesítésű felhasználó, az SQL Server-hitelesítési bejelentkezéstől függően (amelyet az előző lépésben hozott létre).  

Felhasználók létrehozásához csatlakozzon az adatbázishoz, és hajtson végre a következő példákhoz hasonló utasításokat:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

A kezdetekben csak a rendszergazdák vagy az adatbázis tulajdonosa hozhat létre felhasználókat. Ha további felhasználókat szeretne felhatalmazni új felhasználók létrehozására, adja a kiválasztott felhasználónak az `ALTER ANY USER` engedélyt egy, a következőhöz hasonló utasítással:

```sql
GRANT ALTER ANY USER TO Mary;
```

Ha további felhasználók nak is teljes körű encikót szeretne biztosítani az adatbázis felett, tegye őket a **db_owner** rögzített adatbázisszerepkör tagjává.

Az Azure SQL Database-ben használja a `ALTER ROLE` kimutatást.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

Az SQL készletben használja [az EXEC sp_addrolemember.](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```

> [!NOTE]
> Az SQL Database-kiszolgáló bejelentkezési bejelentkezésén alapuló adatbázis-felhasználó létrehozásának egyik gyakori oka az olyan felhasználók számára, akiknek több adatbázishoz kell hozzáférnie. Mivel a tartalmazott adatbázis-felhasználók egyéni entitások, minden adatbázis saját felhasználót és saját jelszót tart fenn. Ez többletterhelést okozhat, mivel a felhasználónak minden egyes adatbázishoz emlékeznie kell minden egyes jelszóra, és tarthatatlanná válhat, ha számos adatbázishoz több jelszót kell módosítania. Az SQL Server bejelentkezések és a magas rendelkezésre állás (aktív georeplikációs és feladatátvételi csoportok) használatakor azonban az SQL Server bejelentkezéseket manuálisan kell beállítani minden kiszolgálón. Ellenkező esetben az adatbázis-felhasználó a feladatátvétel t követően már nem lesz leképezve a kiszolgálóbejelentkezésre, és nem fogja tudni elérni az adatbázis posta-feladatátvételt követően. 

A bejelentkezések georeplikációra történő konfigurálásáról az [Azure SQL Database biztonságának konfigurálása és kezelése geo-visszaállításhoz vagy feladatátvételhez](../../sql-database/sql-database-geo-replication-security-config.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)című témakörben talál további információt.

### <a name="configuring-the-database-level-firewall"></a>Adatbázisszintű tűzfal konfigurálása

Ajánlott eljárásként a nem rendszergazdai felhasználóknak csak az általuk használt adatbázisokhoz kell hozzáféréssel rendelkezniük a tűzfalon keresztül. Ahelyett, hogy a kiszolgálószintű tűzfalon keresztül hitelesítené az IP-címüket, és hozzáférést adna nekik az összes adatbázishoz, az [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utasítással konfigurálja az adatbázisszintű tűzfalat. Az adatbázisszintű tűzfal nem konfigurálható a portálon keresztül.

### <a name="non-administrator-access-path"></a>Nem rendszergazdai hozzáférés elérési útja

Ha az adatbázisszintű tűzfal megfelelően van konfigurálva, az adatbázis felhasználói olyan ügyféleszközökkel csatlakozhatnak, mint az SQL Server Management Studio vagy az SQL Server Data Tools. Csak a legújabb verziójú eszközök teszik elérhetővé az összes rendelkezésre álló funkciót és képességet. Az alábbi ábrán egy tipikus nem rendszergazdai elérési út látható.

![Nem rendszergazdai hozzáférés elérési útja](./media/sql-authentication/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Csoportok és szerepkörök

A hatékony hozzáférés-kezelés egyéni bejelentkezési adatok helyett csoportokhoz és szerepkörökhöz rendelt engedélyeket használ.

- Azure Active Directory-hitelesítés használatakor az Azure Active Directory-felhasználókat helyezze egy Azure Active Directory-csoportba. Hozzon létre a csoportban egy adatbázis-felhasználót. Helyezzen egy vagy több adatbázis-felhasználót egy [adatbázis-szerepkörbe](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), majd rendeljen [engedélyeket](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) az adatbázis-szerepkörhöz.

- SQL Server-hitelesítés használata esetén hozzon létre tartalmazottadatbázis-felhasználókat az adatbázisban. Helyezzen egy vagy több adatbázis-felhasználót egy [adatbázis-szerepkörbe](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), majd rendeljen [engedélyeket](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) az adatbázis-szerepkörhöz.

Az adatbázis-szerepkörök lehetnek beépített szerepkörök, mint például a **db_owner**, a **db_ddladmin**, a **db_datawriter**, a **db_datareader**, a **db_denydatawriter** vagy a **db_denydatareader**. A **db_owner** általában teljes körű engedélyek biztosítására szolgál néhány felhasználó számára. A többi rögzített adatbázis-szerepkör hasznos az egyszerű adatbázisok fejlesztésének gyors elkezdéséhez, de a legtöbb éles környezetbeli adatbázishoz nem ajánlott. 

A **db_datareader** rögzített adatbázis-szerepkör csak olvasási hozzáférést biztosít az adatbázis minden táblájához, ami általában több a feltétlenül szükségesnél. 

Sokkal jobb megoldás a [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) utasítás használata saját felhasználói adatbázis-szerepkörök létrehozásához, majd az üzleti igényekhez minimálisan szükséges engedélyek beállítása az egyes szerepkörökhöz. Ha a felhasználó egyszerre több szerepkörnek is tagja, akkor a rendszer összesíti az engedélyeket.

## <a name="permissions"></a>Engedélyek

Az SQL Database-ben több mint 100 engedély adható vagy tagadható meg külön-külön. Ezek közül számos engedély beágyazott. Egy sémában található `UPDATE` engedély például a séma minden táblájára vonatkozó `UPDATE` engedélyt tartalmazza. A legtöbb engedélyrendszerhez hasonlóan az engedély megtagadása felülírja a megadását. 

Az engedélyek beágyazott jellege és száma miatt lehetséges, hogy alapos tervezés szükséges az adatbázis megfelelő védelmét biztosító engedélyrendszer kialakításához. 

Kezdje az [Engedélyek (Adatbázismotor)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) szakaszban felsorolt engedélyek listájával, majd tekintse át az engedélyek [poszterméretű ábráját](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png).

### <a name="considerations-and-restrictions"></a>Megfontolandó szempontok és korlátozások

Az SQL Database bejelentkezések és felhasználók kezelése során vegye figyelembe a következő pontokat:

- Az utasítások végrehajtásakor **master** kapcsolódnia `CREATE/ALTER/DROP DATABASE` kell a fő adatbázishoz.
- A **kiszolgáló-rendszergazdai** bejelentkezéshez tartozó felhasználó nem módosítható és nem vethető el.
- A **kiszolgáló-rendszergazdai** bejelentkezés alapértelmezett nyelve az amerikai angol (US-English).
- Csak a rendszergazdák (**kiszolgáló-rendszergazdai** bejelentkező vagy Azure AD-rendszergazda) és a **master** adatbázis **dbmanager** adatbázis-szerepkörének tagjai rendelkeznek a `CREATE DATABASE` és a `DROP DATABASE` utasítások futtatásához szükséges engedéllyel.
- A `CREATE/ALTER/DROP LOGIN` utasítások futtatásához csatlakoznia kell a master adatbázishoz. A bejelentkezési adatok használata azonban nem javasolt. Helyette használja a tartalmazott adatbázis felhasználóit.
- A felhasználói adatbázishoz történő csatlakozáshoz adja meg a kapcsolati sztringben szereplő adatbázis nevét.
- Csak a kiszolgálószintű fő bejelentkező és a **master adatbázis****loginmanager** adatbázis-szerepkörének tagjai rendelkeznek a `CREATE LOGIN`, `ALTER LOGIN` és `DROP LOGIN` utasítások futtatásához szükséges engedéllyel.
- A `CREATE/ALTER/DROP LOGIN` és `CREATE/ALTER/DROP DATABASE` utasítások ADO.NET alkalmazáson belüli futtatásakor a paraméteres parancsok futtatása nem engedélyezett. További információkért lásd: [Parancsok és paraméterek](/dotnet/framework/data/adonet/commands-and-parameters?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- A `CREATE/ALTER/DROP DATABASE` és `CREATE/ALTER/DROP LOGIN` utasítások futtatásakor csak az egyes utasítások lehetnek a Transact-SQL kötegben szereplő egyetlen utasítások. Különben hiba történik. Például a következő Transact-SQL utasítás azt ellenőrzi, hogy az adatbázis létezik-e. Ha igen, akkor meghívja a `DROP DATABASE` utasítást az adatbázis eltávolításához. Mivel a `DROP DATABASE` utasítás nem a köteg egyetlen utasítása, a következő Transact-SQL utasítás futtatása hibát eredményez.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```
  
  Ehelyett használja a következő Transact-SQL utasítást:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- A `CREATE USER` utasítás `FOR/FROM LOGIN` beállítással történő futtatásakor ez lehet a Transact-SQL kötegben szereplő egyetlen utasítás.
- A `ALTER USER` utasítás `WITH LOGIN` beállítással történő futtatásakor ez lehet a Transact-SQL kötegben szereplő egyetlen utasítás.
- A `CREATE/ALTER/DROP` utasítás használatához a felhasználónak `ALTER ANY USER` engedéllyel kell rendelkeznie az adatbázisban.
- Ha az adatbázis-szerepkör tulajdonosa szeretne hozzáadni vagy eltávolítani egy felhasználót az adott szerepkörből, akkor a következő hiba léphet fel: **A „Név” felhasználó vagy szerepkör nem található ebben az adatbázisban.** Ez a hiba akkor következik be, ha a felhasználó a tulajdonos számára nem látható. A probléma megoldása érdekében ruházza fel a szerepkör tulajdonosát a `VIEW DEFINITION` engedéllyel. 

## <a name="next-steps"></a>További lépések

További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](https://msdn.microsoft.com/library/ff929188.aspx) cikkben talál.
 
