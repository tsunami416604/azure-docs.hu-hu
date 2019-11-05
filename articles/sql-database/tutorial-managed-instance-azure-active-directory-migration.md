---
title: SQL Server helyszíni Windows-felhasználók és-csoportok migrálása a felügyelt példányok Azure SQL Database a T-SQL DDL-szintaxis használatával | Microsoft Docs
description: Ismerje meg, hogyan telepítheti át SQL Server helyszíni Windows-felhasználókat és-csoportokat a felügyelt példányra
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: b27c9991fd86334c87806772cbd641dd72aad1f6
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163969"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Oktatóanyag: SQL Server helyszíni Windows-felhasználók és-csoportok migrálása Azure SQL Database felügyelt példányra T-SQL DDL-szintaxis használatával

> [!NOTE]
> A jelen cikkben a felhasználók és csoportok felügyelt példányra való átirányításához használt szintaxis **nyilvános előzetes**verzióban érhető el.

Ez a cikk végigvezeti a helyi Windows-felhasználók és-csoportok áttelepítésének folyamatán a SQL Server egy meglévő Azure SQL Database felügyelt példányon a T-SQL szintaxis használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Bejelentkezések létrehozása a SQL Serverhoz
> - Tesztelési adatbázis létrehozása az áttelepítéshez
> - Bejelentkezések, felhasználók és szerepkörök létrehozása
> - Az adatbázis biztonsági mentése és visszaállítása felügyelt példányra (MI)
> - Felhasználók manuális migrálása az ALTER USER szintaxis használatával
> - A hitelesítés tesztelése az új leképezett felhasználókkal

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő előfeltételek érvényesek:

- A Windows-tartományt összevonták a Azure Active Directory (Azure AD) szolgáltatással.
- Hozzáférés Active Directory felhasználók/csoportok létrehozásához.
- Meglévő SQL Server a helyszíni környezetben.
- Egy meglévő felügyelt példány. Lásd [: gyors útmutató: Azure SQL Database felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
  - Azure AD-bejelentkezések létrehozásához a felügyelt példányon `sysadmin` kell használni.
- [Hozzon létre egy Azure ad-rendszergazdát a felügyelt példányhoz](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).
- Kapcsolódhat a felügyelt példányhoz a hálózaton belül. További információkat a következő cikkekben talál: 
    - [Az alkalmazás összekötése Azure SQL Database felügyelt példánnyal](sql-database-managed-instance-connect-app.md)
    - [Gyors útmutató: pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányhoz a helyszíni környezetből](sql-database-managed-instance-configure-p2s.md)
    - [Nyilvános végpont konfigurálása felügyelt Azure SQL Database-példányban](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>A T-SQL DDL szintaxisa

Az alábbiakban látható a T-SQL DDL szintaxisa, amely a felügyelt példányok Azure AD-hitelesítéssel történő áttelepítésének támogatásához használható SQL Server helyszíni Windows-felhasználók és-csoportok számára.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Argumentumok

_domainName_</br>
A felhasználó tartománynevét adja meg.

_userName_</br>
Megadja az adatbázison belül azonosított felhasználó nevét.

_= loginName\@domainName.com_</br>
Felhasználó újraleképezése az Azure AD-bejelentkezésre

_groupName_</br>
Megadja az adatbázison belül azonosított csoport nevét.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>1\. rész: bejelentkezések létrehozása SQL Server helyszíni felhasználók és csoportok számára

> [!IMPORTANT]
> A következő szintaxis egy felhasználót és egy csoportos bejelentkezést hoz létre a SQL Serverban. Az alábbi szintaxis végrehajtása előtt meg kell győződnie arról, hogy a felhasználó és a csoport a Active Directory (AD) belül található. </br> </br>
> Felhasználók: testUser1, testGroupUser </br>
> Csoport: Migrálás – a testGroupUser az Active Directory-beli áttelepítési csoporthoz kell tartoznia

Az alábbi példa létrehoz egy bejelentkezést SQL Server a _testUser1_ nevű fiókhoz a _aadsqlmi_tartomány alatt. 

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

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>2\. rész: Windows-felhasználók és-csoportok létrehozása, szerepkörök és engedélyek hozzáadása

A teszt felhasználó létrehozásához használja a következő szintaxist.

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

A felhasználói engedélyek keresése:

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

Hozzon létre egy szerepkört, és rendelje hozzá a tesztelési felhasználót a következő szerepkörhöz:

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

A következő lekérdezés használatával jelenítheti meg az adott szerepkörhöz rendelt felhasználóneveket:

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

Csoport létrehozásához használja a következő szintaxist. Ezután vegye fel a csoportot a szerepkör `db_owner`ba.

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

Hozzon létre egy tesztelési táblát, és adjon hozzá néhányat a következő szintaxis használatával:

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

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>3\. rész: az egyéni felhasználói adatbázis biztonsági mentése és visszaállítása felügyelt példányra

Készítsen biztonsági másolatot az áttelepítési adatbázisról az [adatbázisok másolása biztonsági mentéssel és visszaállítással](/sql/relational-databases/databases/copy-databases-with-backup-and-restore), vagy használja a következő szintaxist:

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

Kövesse a rövid útmutatót [: adatbázis visszaállítása felügyelt példányra](sql-database-managed-instance-get-started-restore.md).

## <a name="part-4-migrate-users-to-managed-instance"></a>4\. rész: a felhasználók migrálása felügyelt példányra

Hajtsa végre az ALTER USER parancsot az áttelepítési folyamat végrehajtásához a felügyelt példányon.

1. Jelentkezzen be a felügyelt példányba a felügyelt példányhoz tartozó SQL-rendszergazdai fiók használatával. Ezután hozza létre az Azure AD-bejelentkezést a felügyelt példányon a következő szintaxissal. További információ: [oktatóanyag: felügyelt példányok biztonsága Azure SQL Database az Azure ad-kiszolgáló résztvevői (bejelentkezések) használatával](sql-database-managed-instance-aad-security-tutorial.md).

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

1. Győződjön meg arról, hogy a megfelelő adatbázishoz, táblához és résztvevőhöz tartozó áttelepítés megvalósul.

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premise Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. Használja az ALTER USER szintaxist a helyszíni felhasználó Azure AD-bejelentkezésre való leképezéséhez.

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

1. Használja az ALTER USER szintaxist a helyszíni csoport Azure AD-bejelentkezésre való leképezéséhez.

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

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>5\. rész: az Azure AD-felhasználó vagy-csoport hitelesítésének tesztelése

Tesztelje a felügyelt példány hitelesítését az ALTER USER szintaxissal korábban az Azure AD-bejelentkezéshez hozzárendelt felhasználó használatával.
 
1. Jelentkezzen be az összevont virtuális gépre a MI előfizetése használatával `aadsqlmi\testUser1`
1. SQL Server Management Studio (SSMS) használatával jelentkezzen be a felügyelt példányba **Active Directory integrált** hitelesítéssel, és csatlakozzon az adatbázishoz `migration`.
    1. A testUser1@aadsqlmi.net hitelesítő adataival is bejelentkezhet a SSMS beállítással **Active Directory – univerzális, MFA-támogatással**. Ebben az esetben azonban nem használhatja az egyszeri bejelentkezési mechanizmust, és jelszót kell beírnia. Nem kell összevont virtuális gépet használnia a felügyelt példányba való bejelentkezéshez.
1. A **szerepkör tagjaként válassza ki**a `test` táblát.

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


A felügyelt példányok hitelesítésének tesztelése egy Windows-csoport tagjainak `migration`használatával. Az áttelepítés előtt fel kell venni a felhasználót `aadsqlmi\testGroupUser` a csoportba `migration`.

1. Jelentkezzen be az összevont virtuális gépre a MI előfizetése használatával `aadsqlmi\testGroupUser` 
1. A SSMS használata **Active Directory integrált** hitelesítéssel, kapcsolódás a mi kiszolgálóhoz és az adatbázishoz `migration`
    1. A testGroupUser@aadsqlmi.net hitelesítő adataival is bejelentkezhet a SSMS beállítással **Active Directory – univerzális, MFA-támogatással**. Ebben az esetben azonban nem használhatja az egyszeri bejelentkezési mechanizmust, és jelszót kell beírnia. Nem kell összevont virtuális gépet használnia a felügyelt példányba való bejelentkezéshez. 
1. A `db_owner` szerepkör részeként létrehozhat egy új táblázatot.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> Az Azure SQL DB ismert tervezési problémái miatt a csoport tagjaként végrehajtott Create an Table utasítás sikertelen lesz a következő hibával: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> Az aktuális megkerülő megoldás egy meglévő sémával rendelkező tábla létrehozása a fenti esetben < dbo. New >

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: SQL Server migrálása Azure SQL Database felügyelt példányra a DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)