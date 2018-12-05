---
title: Azure SQL-bejelentkezések és -felhasználók | Microsoft Docs
description: Ismerje meg az SQL Database és SQL Data Warehouse biztonsági felügyeletét, különös tekintettel a kiszolgálószintű elsődleges fiókon keresztül hozzáférés és a bejelentkezési biztonság adatbázis kezeléséhez.
keywords: sql database biztonság,adatbázis biztonságának felügyelete,bejelentkezési biztonság,adatbázis biztonsága,adatbázis-hozzáférés
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 11/29/2018
ms.openlocfilehash: c234ac95d0e02857fe87afe3a734d77f00954477
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52864944"
---
# <a name="controlling-and-granting-database-access-to-sql-database-and-sql-data-warehouse"></a>Szabályozása és adatbázis-hozzáférés biztosítása az SQL Database és SQL Data warehouse-bA

Tűzfal-szabályok konfiguráció után csatlakozhat az Azure [SQL Database](sql-database-technical-overview.md) és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) a rendszergazdai fiókok egyikeként, az adatbázis tulajdonosa, vagy az adatbázis egy adatbázis-felhasználót.  

>  [!NOTE]  
>  Ez a témakör az Azure SQL Serverhez és az Azure SQL Serveren létrehozott SQL Database és az SQL Data Warehouse adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database. 

> [!TIP]
> Foglalkozó oktatóanyagért lásd: [biztonságossá tétele az Azure SQL Database](sql-database-security-tutorial.md). Ebben az oktatóanyagban nem vonatkozik a **Azure SQL Database felügyelt példányába**.

## <a name="unrestricted-administrative-accounts"></a>Nem korlátozott rendszergazdai fiókok
Kettő rendszergazdaként működő felügyeleti fiók létezik (**Kiszolgálói rendszergazdai** és **Active Directory-rendszergazdai**). Ha azonosítani szeretné ezeket a rendszergazdai fiókokat az SQL-kiszolgáló esetében, nyissa meg az Azure Portalt, és lépjen az SQL-kiszolgáló tulajdonságaira.

![SQL Server-rendszergazdák](./media/sql-database-manage-logins/sql-admins.png)

- **Kiszolgálói rendszergazda**   
Azure SQL Server-kiszolgáló létrehozásakor ki kell jelölnie egy **kiszolgáló-rendszergazdai felhasználónevet**. Az SQL-kiszolgáló ekkor létrehozza a fiókot a master adatbázis egyik bejelentkezési neveként. Ez a fiók SQL Server-hitelesítéssel csatlakozik (felhasználónévvel és jelszóval). Ilyen fiókból csak egy létezhet.   
- **Azure Active Directory-rendszergazda**   
Egy Azure Active Directory-fiók (különálló vagy biztonságicsoport-fiók) is konfigurálható rendszergazdaként. Azure AD-rendszergazda, de az Azure AD-rendszergazda konfigurálása nem kötelező **kell** kell konfigurálni, ha azt szeretné, az Azure AD-fiókok használatához az SQL-adatbázishoz való csatlakozáshoz. Az Azure Active Directory hozzáférésének konfigurálásáról további információért lásd [az SQL Database-hez vagy az SQL Data Warehouse-hoz az Azure Active Directory-hitelesítéssel történő csatlakozást](sql-database-aad-authentication.md), illetve [az Azure AD MFA és az SQL Database, valamint az SQL Data Warehouse együttes támogatását](sql-database-ssms-mfa-authentication.md) ismertető cikket.
 

A **kiszolgáló-rendszergazdai** és **Azure AD-rendszergazdai** fiókok az alábbi jellemzőkkel rendelkeznek:
- A csak fiókokat, amelyek képesek automatikusan csatlakozni bármely SQL-adatbázis a kiszolgálón vannak. (Felhasználói adatbázishoz történő csatlakozáshoz a többi fióknak vagy az adatbázis tulajdonosának kell lennie, vagy felhasználói fiókkal kell rendelkeznie az adatbázisban.)
- Ezek a fiókok `dbo`-felhasználóként lépnek be a felhasználói adatbázisokba, és minden engedéllyel rendelkeznek az adatbázison belül. (A felhasználói adatbázis tulajdonosa szintén `dbo`-felhasználóként jelentkezik be.) 
- Ne adja meg a `master` adatbázisba, a `dbo` felhasználó, és korlátozott engedélyekkel a főadatbázisban. 
- Vannak **nem** tagjai a standard SQL Server `sysadmin` rögzített kiszolgálói szerepkör, amely nem érhető el az SQL database-ben.  
- Létrehozhat, alter, és dobja el az adatbázisok, bejelentkezések, felhasználók a master és a kiszolgálószintű tűzfalszabályokat.
- Hozzáadhat és eltávolíthat tagokat a `dbmanager` és `loginmanager` szerepköröket.
- Megtekintheti a `sys.sql_logins` rendszertáblában.

### <a name="configuring-the-firewall"></a>A tűzfal konfigurálása
Ha a kiszolgálószintű tűzfal egy önálló IP-címhez vagy -tartományhoz van konfigurálva, az **SQL Server-rendszergazda** és az **Azure Active Directory-rendszergazda** a master adatbázishoz és az összes felhasználói adatbázishoz csatlakozhat. A kezdeti kiszolgálószintű tűzfal az [Azure Portalon](sql-database-get-started-portal.md) konfigurálható a [PowerShell](sql-database-powershell-samples.md) vagy a [REST API](https://msdn.microsoft.com/library/azure/dn505712.aspx) segítségével. A kapcsolat létrehozása után további kiszolgálószintű tűzfalszabályok is konfigurálhatók a [Transact-SQL](sql-database-configure-firewall-settings.md) segítségével.

### <a name="administrator-access-path"></a>Rendszergazdai hozzáférés elérési útja
Ha a kiszolgálószintű tűzfal megfelelően van konfigurálva, az **SQL Server-rendszergazda** és az **Azure Active Directory-rendszergazda** olyan ügyféleszközökkel csatlakozhat, mint az SQL Server Management Studio vagy az SQL Server Data Tools. Csak a legújabb verziójú eszközök teszik elérhetővé az összes rendelkezésre álló funkciót és képességet. Az alábbi ábrán a két rendszergazdai fiók tipikus konfigurációja látható.

![Rendszergazdai hozzáférés elérési útja](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Amikor nyitott portot használ a kiszolgálószintű tűzfalon, a rendszergazdák bármely SQL Database-adatbázishoz csatlakozhatnak.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Csatlakozás egy adatbázishoz az SQL Server Management Studióval
Ha útmutatót szeretne egy kiszolgáló, adatbázis vagy kiszolgálószintű tűzfalszabályok létrehozásához és az SQL Server Management Studio adatbázis-lekérdezéshez való használatához tekintse meg a következőt: [Az Azure SQL Database-kiszolgálók, -adatbázisok és -tűzfalszabályok Azure Portallal és az SQL Server Management Studióval történő használatának első lépései](sql-database-get-started-portal.md).

> [!IMPORTANT]
> Javasoljuk, hogy mindig a Management Studio legfrissebb verzióját használja, hogy kihasználhassa a Microsoft Azure és az SQL Database legújabb frissítései által nyújtott előnyöket. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="additional-server-level-administrative-roles"></a>További kiszolgálószintű rendszergazdai szerepkörök

>[!IMPORTANT]
>Ez a szakasz nem vonatkozik a **Azure SQL Database felügyelt példányába** , ezek a szerepkörök kifejezetten egy **Azure SQL Database**.

A korábban már tárgyalt kiszolgálószintű rendszergazdai szerepkörökön kívül az SQL Database két korlátozott rendszergazdai szerepkört tesz elérhetővé a master adatbázisban, amelyekhez felhasználói fiókok adhatók, és amelyek hozzáférést biztosítanak adatbázisok létrehozásához vagy bejelentkezések kezeléséhez.

### <a name="database-creators"></a>Adatbázis-létrehozók
Ezen rendszergazdai szerepkörök egyike a **dbmanager** szerepkör. Ezen szerepkör tagjai létrehozhatnak új adatbázisokat. A szerepkör használatához hozzon létre egy felhasználót az `master` adatbázisban, majd adja hozzá a felhasználót a **dbmanager** adatbázis-szerepkörhöz. Adatbázis létrehozásához a felhasználónak SQL Server-bejelentkezésen alapuló felhasználónak kell lennie a master adatbázisban, vagy Azure Active Directory-felhasználón alapuló tartalmazottadatbázis-felhasználónak kell lennie.

1. Egy rendszergazdai fiókkal csatlakozzon a master adatbázishoz.
2. Nem kötelező lépés: Hozzon létre egy SQL Server-hitelesítési bejelentkezést a [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) utasítással. Mintautasítás:
   
   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```
   
   > [!NOTE]
   > Használjon erős jelszót a bejelentkezési vagy tartalmazottadatbázis-felhasználó létrehozásakor. További információkért lásd az [erős jelszavak](https://msdn.microsoft.com/library/ms161962.aspx) létrehozását ismertető cikket.
    
   A teljesítmény javítása érdekében a bejelentkezéseket (a kiszolgálószintű elsődleges fiókokat) átmenetileg adatbázisszinten is gyorsítótárazza a rendszer. A hitelesítési gyorsítótár frissítésével kapcsolatban lásd a [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx) használatát ismertető cikket.

3. A master adatbázisban hozzon létre egy felhasználót a [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) utasítással. A felhasználó Azure Active Directory-hitelesítésű tartalmazottadatbázis-felhasználó lehet (ha az Azure AD-hitelesítéshez konfigurálta a környezetét), vagy egy SQL Server-hitelesítésű tartalmazottadatbázis-felhasználó, illetve egy SQL Server-hitelesítésű felhasználó, az SQL Server-hitelesítési bejelentkezéstől függően (amelyet az előző lépésben hozott létre). Mintautasítások:
   
   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Adja az új felhasználót a **dbmanager** adatbázis-szerepkörhöz az [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx) utasítással. Mintautasítások:
   
   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```
   
   > [!NOTE]
   > A dbmanager egy, a master adatbázisban található adatbázis-szerepkör, így csak adatbázis-felhasználót adhat hozzá a dbmanager szerepkörhöz. Nem adhat kiszolgálószintű bejelentkezést az adatbázisszintű szerepkörökhöz.
    
5. Szükség esetén konfiguráljon egy tűzfalszabályt úgy, hogy az új felhasználó csatlakozhasson. (Előfordulhat, hogy az új felhasználóra már vonatkozik létező tűzfalszabály.)

Most a felhasználó már csatlakozhat a master adatbázishoz, és létrehozhat új adatbázisokat. Az adatbázist létrehozó fiók az adatbázis tulajdonosává válik.

### <a name="login-managers"></a>Bejelentkezéskezelők
A másik rendszergazdai szerepkör a bejelentkezéskezelői szerepkör. Ezen szerepkör tagjai létrehozhatnak új bejelentkezéseket a master adatbázisban. Ha szeretné, elvégezheti ugyanezen lépéseket (bejelentkezést és felhasználót hozhat létre, és a felhasználót a **loginmanager** szerepkörhöz adhatja) annak érdekében, hogy egy felhasználó új bejelentkezéseket hozhasson létre a master adatbázisban. Bejelentkezésre általában nincs szükség, mivel a Microsoft tartalmazottadatbázis-felhasználók használatát javasolja, amelyek az adatbázis szintjén hitelesíthetők a bejelentkezéseken alapuló felhasználók használata helyett. További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](https://msdn.microsoft.com/library/ff929188.aspx) cikkben talál.

## <a name="non-administrator-users"></a>Nem rendszergazdai felhasználók
Általában a nem rendszergazdai fiókoknak nincs szükségük a master adatbázis elérésére. Hozzon létre tartalmazottadatbázis-felhasználókat az adatbázis szintjén a [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx) utasítással. A felhasználó Azure Active Directory-hitelesítésű tartalmazottadatbázis-felhasználó lehet (ha az Azure AD-hitelesítéshez konfigurálta a környezetét), vagy egy SQL Server-hitelesítésű tartalmazottadatbázis-felhasználó, illetve egy SQL Server-hitelesítésű felhasználó, az SQL Server-hitelesítési bejelentkezéstől függően (amelyet az előző lépésben hozott létre). További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](https://msdn.microsoft.com/library/ff929188.aspx) cikkben talál. 

Felhasználók létrehozásához csatlakozzon az adatbázishoz, és hajtson végre a következő példákhoz hasonló utasításokat:

```sql
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

A kezdetekben csak a rendszergazdák vagy az adatbázis tulajdonosa hozhat létre felhasználókat. Ha további felhasználókat szeretne felhatalmazni új felhasználók létrehozására, adja a kiválasztott felhasználónak az `ALTER ANY USER` engedélyt egy, a következőhöz hasonló utasítással:

```sql
GRANT ALTER ANY USER TO Mary;
```

Ha további felhasználóknak szeretne teljes elérést biztosítani az adatbázishoz, tegye őket a **db_owner** rögzített adatbázis-szerepkör tagjává az `ALTER ROLE` utasítással.

```sql
ALTER ROLE db_owner ADD MEMBER Mary; 
```

> [!NOTE]
> Hozzon létre egy logikai kiszolgáló-bejelentkezésen alapuló felhasználót egyik gyakori oka van, a felhasználók számára, akiknek több adatbázishoz kell hozzáférniük. Mivel a tartalmazott adatbázis-felhasználók önálló entitások, minden egyes adatbázis kezeli a saját felhasználó és a saját jelszavát. Ez terhelést okozhat, a felhasználó akkor ne feledje az egyes adatbázisokhoz minden jelszót, és válhat tarthatatlan során számos adatbázis több jelszó módosítani kellene. Azonban az SQL Server bejelentkezési és a magas rendelkezésre állású (aktív georeplikációs és feladatátvételi csoportok) használata esetén az SQL Server bejelentkezési állítson be manuálisan minden egyes kiszolgálón. Ellenkező esetben az adatbázis-felhasználó már nem kell hozzárendelni a kiszolgálói bejelentkezésen után feladatátvétel történik, és nem fogja tudni elérni az adatbázis-feladatátvétel után. Bejelentkezések a georeplikáció konfigurálásáról további információért tekintse meg [konfigurálása és kezelése az Azure SQL Database biztonsági geo-visszaállítás vagy a feladatátvételi](sql-database-geo-replication-security-config.md).

### <a name="configuring-the-database-level-firewall"></a>Adatbázisszintű tűzfal konfigurálása
Ajánlott eljárásként a nem rendszergazdai felhasználóknak csak az általuk használt adatbázisokhoz kell hozzáféréssel rendelkezniük a tűzfalon keresztül. Ahelyett, hogy a kiszolgálószintű tűzfalon keresztül hitelesítené az IP-címüket, és hozzáférést adna nekik az összes adatbázishoz, az [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) utasítással konfigurálja az adatbázisszintű tűzfalat. Az adatbázisszintű tűzfal nem konfigurálható a portálon keresztül.

### <a name="non-administrator-access-path"></a>Nem rendszergazdai hozzáférés elérési útja
Ha az adatbázisszintű tűzfal megfelelően van konfigurálva, az adatbázis felhasználói olyan ügyféleszközökkel csatlakozhatnak, mint az SQL Server Management Studio vagy az SQL Server Data Tools. Csak a legújabb verziójú eszközök teszik elérhetővé az összes rendelkezésre álló funkciót és képességet. Az alábbi ábrán egy tipikus nem rendszergazdai elérési út látható.

![Nem rendszergazdai hozzáférés elérési útja](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Csoportok és szerepkörök
A hatékony hozzáférés-kezelés egyéni bejelentkezési adatok helyett csoportokhoz és szerepkörökhöz rendelt engedélyeket használ. 

- Azure Active Directory-hitelesítés használatakor az Azure Active Directory-felhasználókat helyezze egy Azure Active Directory-csoportba. Hozzon létre a csoportban egy adatbázis-felhasználót. Helyezzen egy vagy több adatbázis-felhasználót egy [adatbázis-szerepkörbe](https://msdn.microsoft.com/library/ms189121), majd rendeljen [engedélyeket](https://msdn.microsoft.com/library/ms191291.aspx) az adatbázis-szerepkörhöz.

- SQL Server-hitelesítés használata esetén hozzon létre tartalmazottadatbázis-felhasználókat az adatbázisban. Helyezzen egy vagy több adatbázis-felhasználót egy [adatbázis-szerepkörbe](https://msdn.microsoft.com/library/ms189121), majd rendeljen [engedélyeket](https://msdn.microsoft.com/library/ms191291.aspx) az adatbázis-szerepkörhöz.

Az adatbázis-szerepkörök lehetnek beépített szerepkörök, mint például a **db_owner**, a **db_ddladmin**, a **db_datawriter**, a **db_datareader**, a **db_denydatawriter** vagy a **db_denydatareader**. A **db_owner** általában teljes körű engedélyek biztosítására szolgál néhány felhasználó számára. A többi rögzített adatbázis-szerepkör hasznos az egyszerű adatbázisok fejlesztésének gyors elkezdéséhez, de a legtöbb éles környezetbeli adatbázishoz nem ajánlott. A **db_datareader** rögzített adatbázis-szerepkör csak olvasási hozzáférést biztosít az adatbázis minden táblájához, ami általában több a feltétlenül szükségesnél. Sokkal jobb megoldás a [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) utasítás használata saját felhasználói adatbázis-szerepkörök létrehozásához, majd az üzleti igényekhez minimálisan szükséges engedélyek beállítása az egyes szerepkörökhöz. Ha a felhasználó egyszerre több szerepkörnek is tagja, akkor a rendszer összesíti az engedélyeket.

## <a name="permissions"></a>Engedélyek
Az SQL Database-ben több mint 100 engedély adható vagy tagadható meg külön-külön. Ezek közül számos engedély beágyazott. Egy sémában található `UPDATE` engedély például a séma minden táblájára vonatkozó `UPDATE` engedélyt tartalmazza. A legtöbb engedélyrendszerhez hasonlóan az engedély megtagadása felülírja a megadását. Az engedélyek beágyazott jellege és száma miatt lehetséges, hogy alapos tervezés szükséges az adatbázis megfelelő védelmét biztosító engedélyrendszer kialakításához. Kezdje az [Engedélyek (Adatbázismotor)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) szakaszban felsorolt engedélyek listájával, majd tekintse át az engedélyek [poszterméretű ábráját](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png).


### <a name="considerations-and-restrictions"></a>Megfontolandó szempontok és korlátozások
Az SQL Database bejelentkezéseinek és felhasználóinak kezelésekor vegye figyelembe a következőket:

* A **utasítások futtatásához csatlakoznia kell a**master adatbázishoz`CREATE/ALTER/DROP DATABASE`.   
* A **kiszolgáló-rendszergazdai** bejelentkezéshez tartozó felhasználó nem módosítható és nem vethető el. 
* A **kiszolgáló-rendszergazdai** bejelentkezés alapértelmezett nyelve az amerikai angol (US-English).
* Csak a rendszergazdák (**kiszolgáló-rendszergazdai** bejelentkező vagy Azure AD-rendszergazda) és a **master** adatbázis **dbmanager** adatbázis-szerepkörének tagjai rendelkeznek a `CREATE DATABASE` és a `DROP DATABASE` utasítások futtatásához szükséges engedéllyel.
* A `CREATE/ALTER/DROP LOGIN` utasítások futtatásához csatlakoznia kell a master adatbázishoz. A bejelentkezési adatok használata azonban nem javasolt. Helyette használja a tartalmazott adatbázis felhasználóit.
* A felhasználói adatbázishoz történő csatlakozáshoz adja meg a kapcsolati sztringben szereplő adatbázis nevét.
* Csak a kiszolgálószintű fő bejelentkező és a **master adatbázis** **loginmanager** adatbázis-szerepkörének tagjai rendelkeznek a `CREATE LOGIN`, `ALTER LOGIN` és `DROP LOGIN` utasítások futtatásához szükséges engedéllyel.
* A `CREATE/ALTER/DROP LOGIN` és `CREATE/ALTER/DROP DATABASE` utasítások ADO.NET alkalmazáson belüli futtatásakor a paraméteres parancsok futtatása nem engedélyezett. További információkért lásd: [Parancsok és paraméterek](https://msdn.microsoft.com/library/ms254953.aspx).
* A `CREATE/ALTER/DROP DATABASE` és `CREATE/ALTER/DROP LOGIN` utasítások futtatásakor csak az egyes utasítások lehetnek a Transact-SQL kötegben szereplő egyetlen utasítások. Különben hiba történik. Például a következő Transact-SQL utasítás azt ellenőrzi, hogy az adatbázis létezik-e. Ha igen, akkor meghívja a `DROP DATABASE` utasítást az adatbázis eltávolításához. Mivel a `DROP DATABASE` utasítás nem a köteg egyetlen utasítása, a következő Transact-SQL utasítás futtatása hibát eredményez.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```

* A `CREATE USER` utasítás `FOR/FROM LOGIN` beállítással történő futtatásakor ez lehet a Transact-SQL kötegben szereplő egyetlen utasítás.
* A `ALTER USER` utasítás `WITH LOGIN` beállítással történő futtatásakor ez lehet a Transact-SQL kötegben szereplő egyetlen utasítás.
* A `CREATE/ALTER/DROP` utasítás használatához a felhasználónak `ALTER ANY USER` engedéllyel kell rendelkeznie az adatbázisban.
* Ha az adatbázis-szerepkör tulajdonosa szeretne hozzáadni vagy eltávolítani egy felhasználót az adott szerepkörből, akkor a következő hiba léphet fel: **A „Név” felhasználó vagy szerepkör nem található ebben az adatbázisban.** Ez a hiba akkor következik be, ha a felhasználó a tulajdonos számára nem látható. A probléma megoldása érdekében ruházza fel a szerepkör tulajdonosát a `VIEW DEFINITION` engedéllyel. 


## <a name="next-steps"></a>További lépések

- A tűzfalszabályokkal kapcsolatos további információk: [Azure SQL Database-tűzfal](sql-database-firewall-configure.md).
- Az SQL Database összes biztonsági szolgáltatásáról [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít áttekintést.
- Foglalkozó oktatóanyagért lásd: [biztonságossá tétele az Azure SQL Database](sql-database-security-tutorial.md).
- Információk a nézetekről és a tárolt eljárásokról: [Nézetek és tárolt eljárások létrehozása](https://msdn.microsoft.com/library/ms365311.aspx)
- Információk adatbázis-objektumhoz való hozzáférés biztosításáról: [Adatbázis-objektumhoz való hozzáférés biztosítása](https://msdn.microsoft.com/library/ms365327.aspx)
