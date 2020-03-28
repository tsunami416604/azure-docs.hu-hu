---
title: SQL ServerWindows felhasználók és csoportok áttelepítése felügyelt példányba a T-SQL használatával
description: Tudnivalók az SQL Server helyszíni Windows-felhasználók és -csoportok felügyelt példányba való áttelepítéséről
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: 2c8d7252b4e4ca8caa465727c0d2328c4aafaefb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227925"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Oktatóanyag: Sql Server áttelepítése helyszíni Windows-felhasználók és -csoportok áttelepítése az Azure SQL Database felügyelt példányába a T-SQL DDL szintaxis használatával

> [!NOTE]
> A felhasználók és csoportok felügyelt példányba való áttelepítéséhez használt szintaxis **nyilvános előzetes verzióban**érhető el.

Ez a cikk végigvezeti a folyamat áttelepítése a helyszíni Windows-felhasználók és csoportok az SQL Server egy meglévő Azure SQL Database felügyelt példány t-SQL szintaxis használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Bejelentkezések létrehozása az SQL Server kiszolgálóhoz
> - Tesztadatbázis létrehozása áttelepítéshez
> - Bejelentkezések, felhasználók és szerepkörök létrehozása
> - Az adatbázis biztonsági mentése és visszaállítása felügyelt példányba (MI)
> - Felhasználók manuális áttelepítése a hibajelzőbe az ALTER USER szintaxis használatával
> - Hitelesítés tesztelése az új leképezett felhasználókkal

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez a következő előfeltételek érvényesek:

- A Windows-tartomány az Azure Active Directory (Azure AD) összevont.
- Hozzáférés az Active Directoryhoz felhasználók/csoportok létrehozásához.
- A helyszíni környezetben már meglévő SQL Server.
- Egy meglévő felügyelt példány. Lásd: [Rövid útmutató: Hozzon létre egy Azure SQL Database felügyelt példányt.](sql-database-managed-instance-get-started.md)
  - A `sysadmin` a felügyelt példányban azure AD bejelentkezések létrehozásához kell használni.
- [Hozzon létre egy Azure AD-rendszergazdát a felügyelt példányhoz.](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)
- Csatlakozhat a felügyelt példányhoz a hálózaton belül. További információt az alábbi cikkekben talál: 
    - [Alkalmazás csatlakoztatása felügyelt Azure SQL Database-példányhoz](sql-database-managed-instance-connect-app.md)
    - [Rövid útmutató: Pont-hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányhoz a helyszíni környezetből](sql-database-managed-instance-configure-p2s.md)
    - [Nyilvános végpont konfigurálása felügyelt Azure SQL Database-példányban](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>T-SQL DDL szintaxis

Az alábbiakban a T-SQL DDL szintaxis támogatja az SQL Server helyszíni Windows-felhasználók és csoportok áttelepítése felügyelt példány az Azure AD-hitelesítéssel.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Argumentumok

_Tartománynév_</br>
Megadja a felhasználó tartománynevét.

_Felhasználónév_</br>
Megadja az adatbázisban azonosított felhasználó nevét.

_= loginName\@domainName.com_</br>
Felhasználó újraképezése az Azure AD bejelentkezéshez

_csoportneve_</br>
Megadja az adatbázisban azonosított csoport nevét.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>1. rész: Bejelentkezések létrehozása az SQL Server helyszíni felhasználói és csoportjai számára

> [!IMPORTANT]
> A következő szintaxis létrehoz egy felhasználót és egy csoport bejelentkezést az SQL Server kiszolgálón. Az alábbi szintaxis végrehajtása előtt meg kell győződnie arról, hogy a felhasználó és a csoport az Active Directoryban (AD) belül van. </br> </br>
> Felhasználók: testUser1, testGroupUser </br>
> Csoport: áttelepítés - a testGroupUser nek az AD áttelepítési csoportjához kell tartoznia

Az alábbi példa létrehoz egy bejelentkezési sql server egy fiókot nevű _testUser1_ tartományban _aadsqlmi_. 

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases
 
use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;   
go; 

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser]. 
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;    
go; 


-- Check logins were created
select * from sys.server_principals; 
go; 
```

Hozzon létre egy adatbázist ehhez a teszthez.

```sql
-- Create a database called [migration]
create database migration 
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>2. rész: Windows-felhasználók és -csoportok létrehozása, majd szerepkörök és engedélyek hozzáadása

A tesztfelhasználó létrehozásához használja az alábbi szintaxist.

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

Ellenőrizze a felhasználói engedélyeket:

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

Hozzon létre egy szerepkört, és rendelje hozzá a tesztfelhasználót ehhez a szerepkörhöz:

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

Az alábbi lekérdezéssel megjelenítheti az adott szerepkörhöz rendelt felhasználóneveket:

```sql
-- Display user name assigned to a specific role 
SELECT DP1.name AS DatabaseRoleName,    
   isnull (DP2.name, 'No members') AS DatabaseUserName    
 FROM sys.database_role_members AS DRM   
 RIGHT OUTER JOIN sys.database_principals AS DP1   
   ON DRM.role_principal_id = DP1.principal_id   
 LEFT OUTER JOIN sys.database_principals AS DP2   
   ON DRM.member_principal_id = DP2.principal_id   
WHERE DP1.type = 'R' 
ORDER BY DP1.name; 
```

Csoport létrehozásához használja az alábbi szintaxist. Ezután adja hozzá `db_owner`a csoportot a szerepkörhöz.

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration]; 
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration'; 
go 

--Check the db_owner role for 'aadsqlmi\migration' group 
select is_rolemember('db_owner', 'aadsqlmi\migration')   
go 
-- Output  ( 1 means YES) 
```

Hozzon létre egy teszttáblát, és adjon hozzá néhány adatot az alábbi szintaxissal:

```sql
-- Create a table and add data 
create table test ( a int, b int); 
go 

insert into test values (1,10) 
go 

-- Check the table values
select * from test; 
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>3. rész: Az egyes felhasználói adatbázis biztonsági mentése és visszaállítása a felügyelt példányba

Készítsen biztonsági másolatot az áttelepítési adatbázisról az [Adatbázisok másolása biztonsági másolattal és visszaállítással](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)című cikksegítségével, vagy használja az alábbi szintaxist:

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

Kövesse [a rövid útmutató: Adatbázis visszaállítása felügyelt példányra.](sql-database-managed-instance-get-started-restore.md)

## <a name="part-4-migrate-users-to-managed-instance"></a>4. rész: Felhasználók áttelepítése felügyelt példányba

> [!NOTE]
> Az Azure AD-rendszergazda felügyelt példány funkció létrehozása után megváltozott. További információ: [New Azure AD admin functionality for MI.](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi)

Az ALTER USER parancs végrehajtása az áttelepítési folyamat befejezéséhez a felügyelt példányon.

1. Jelentkezzen be a felügyelt példány az Azure AD-rendszergazdai fiók felügyelt példány használatával. Ezután hozza létre az Azure AD bejelentkezést a felügyelt példányban a következő szintaxis használatával. További információ: [Oktatóanyag: Felügyelt példány biztonsága az Azure SQL Database-ben az Azure AD-kiszolgálói egyszerű (bejelentkezési adatok) használatával című témakörben.](sql-database-managed-instance-aad-security-tutorial.md)

    ```sql
    use master 
    go 
    
    -- Create login for AAD user [testUser1@aadsqlmi.net] 
    create login [testUser1@aadsqlmi.net] from external provider 
    go
    
    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net] 
    create login [migration] from external provider 
    go 
     
    --Check the two new logins 
    select * from sys.server_principals 
    go
    ```

1. Ellenőrizze az áttelepítést a megfelelő adatbázis, tábla és rendszerbiztonsági tagok között.

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. Az ALTER USER szintaxissal leképezze a helyszíni felhasználót az Azure AD bejelentkezéshez.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net]; 
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
    -- New user testUser1@aadsqlmi.net should be there instead 
     
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go

    -- Check a specific role   
    -- Display Db user name assigned to a specific role 
    SELECT DP1.name AS DatabaseRoleName,    
    isnull (DP2.name, 'No members') AS DatabaseUserName    
    FROM sys.database_role_members AS DRM   
    RIGHT OUTER JOIN sys.database_principals AS DP1   
    ON DRM.role_principal_id = DP1.principal_id   
    LEFT OUTER JOIN sys.database_principals AS DP2   
    ON DRM.member_principal_id = DP2.principal_id   
    WHERE DP1.type = 'R' 
    ORDER BY DP1.name;
    ```

1. Az ALTER USER szintaxissal rendelje le a helyszíni csoportot az Azure AD bejelentkezéshez.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration]; 
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
     
    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go 
     
    --Check the db_owner role for 'aadsqlmi\migration' user 
    select is_rolemember('db_owner', 'migration')   
    go 
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>5. rész: Az Azure AD-felhasználó vagy -csoport hitelesítésének tesztelése

Tesztelje a felügyelt példány hitelesítését az Azure AD bejelentkezéshez korábban leképezett felhasználó használatával az ALTER USER szintaxis használatával.
 
1. Jelentkezzen be az összevont virtuális gépbe az MI-előfizetés használatával`aadsqlmi\testUser1`
1. Az SQL Server Management Studio (SSMS) használatával jelentkezzen be a felügyelt `migration`példányba az Active Directory **integrált** hitelesítésével, amely csatlakozik az adatbázishoz.
    1. A testUser1@aadsqlmi.net hitelesítő adatokkal is bejelentkezhet az **Active Directory – Universal sSMS opcióval, amely mfa-támogatással rendelkezik.** Ebben az esetben azonban nem használhatja az egyszeri bejelentkezés i mechanizmust, és be kell írnia egy jelszót. Nem kell egy összevont virtuális gép a felügyelt példányba való bejelentkezéshez.
1. A **SELECT**szerepkörtag részeként a `test` táblából választhatunk.

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Felügyelt példány hitelesítésének tesztelése egy Windows-csoport `migration`tagjával . A `aadsqlmi\testGroupUser` felhasználót az áttelepítés `migration` előtt hozzá kellett volna adni a csoporthoz.

1. Jelentkezzen be az összevont virtuális gépbe az MI-előfizetés használatával`aadsqlmi\testGroupUser` 
1. Az SSMS active **directory integrált** hitelesítéssel történő használata, csatlakozás az mi-kiszolgálóhoz és az adatbázishoz`migration`
    1. A testGroupUser@aadsqlmi.net hitelesítő adatokkal is bejelentkezhet az **Active Directory – Universal sSMS opcióval, amely mfa-támogatással rendelkezik.** Ebben az esetben azonban nem használhatja az egyszeri bejelentkezés i mechanizmust, és be kell írnia egy jelszót. Nem kell egy összevont virtuális gép a felügyelt példány ba való bejelentkezéshez. 
1. A `db_owner` szerepkör részeként új táblát hozhat létre.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> Az Azure SQL DB ismert tervezési problémája miatt a csoport tagjaként végrehajtott táblautasítás létrehozása a következő hibával sikertelen lesz: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> Az aktuális megoldás az, hogy hozzon létre egy táblázatot egy meglévő sémát a fenti esetben <dbo.new>

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az SQL Server áttelepítése egy Azure SQL Database felügyelt példányba offline állapotban a DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)