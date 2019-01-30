---
title: Az Azure SQL Database felügyelt példány biztonsági az Azure AD bejelentkezési adatok használata |} A Microsoft Docs
description: Információ a módszerek és szolgáltatások az Azure SQL Database felügyelt példány biztonságos, és használja az Azure AD-bejelentkezések
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 01/18/2019
ms.openlocfilehash: f96b2853b887836a94091dcba0ceaf6f8dd43d12
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55229477"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-logins"></a>Oktatóanyag: Felügyelt példány biztonsági az Azure SQL Database az Azure AD bejelentkezési adatok használata

Az Azure SQL Database felügyelt példányain gyakorlatilag az összes biztonsági szolgáltatásokat kínál a legújabb SQL Server helyi (Enterprise Edition) adatbázismotor rendelkezik:

- Izolált környezetben a hozzáférés korlátozása
- (Az Azure Active Directory, SQL-hitelesítés) identitást igénylő hitelesítési mechanizmusok használata
- Engedélyezési használata szerepköralapú tagságok és engedélyek
- Biztonsági szolgáltatások engedélyezése

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Hozzon létre egy Azure Active Directory (AD) bejelentkezést felügyelt példányok
> - Az Azure AD-felhasználónévként a felügyelt példányok engedélyek megadása
> - Az Azure AD-felhasználók létrehozása az Azure AD-bejelentkezések
> - Engedélyek hozzárendelése az Azure AD-felhasználók és a felügyelt adatbázis-biztonság
> - A megszemélyesítést használhatja az Azure AD-felhasználók
> - Adatbázisközi lekérdezések használata az Azure AD-felhasználók
> - További információ a biztonsági funkciókat, például veszélyforrások elleni védelem, naplózás, adatmaszkolás és titkosítás

> [!NOTE]
> Az Azure AD bejelentkezés az SQL Database felügyelt példánya van **nyilvános előzetes verzióban**.

További tudnivalókért tekintse meg a [áttekintése az Azure SQL Database felügyelt példányain](sql-database-managed-instance-index.yml) és [képességek](sql-database-managed-instance.md) cikkek.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, ellenőrizze, hogy a következő előfeltételek vonatkoznak:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Az Azure SQL Database felügyelt példánya
    - Ez a cikk kövesse: [Rövid útmutató: Hozzon létre egy Azure SQL Database felügyelt példány](sql-database-managed-instance-get-started.md)
- Tudni elérni az Azure SQL Database felügyelt példányába és [kiépítve a felügyelt példány az Azure AD-rendszergazda](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). További tudnivalókért lásd:
    - [Alkalmazás csatlakoztatása egy felügyelt Azure SQL Database-példányhoz](sql-database-managed-instance-connect-app.md) 
    - [Felügyelt Azure SQL Database-példány – Kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md)
    - [Konfigurálhatja és kezelheti az Azure Active Directory-hitelesítés az SQL](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>A felügyelt példányhoz való hozzáférés korlátozása

Felügyelt példányok csak egy magánhálózati IP-címen keresztül érhető el. Nincsenek nem szeretne csatlakozni egy felügyelt példány a felügyelt példány hálózaton kívülről elérhető végpontok. Sokkal elkülönített SQL Server helyszíni környezetben, például alkalmazások vagy felhasználók a felügyelt példány hálózaton (VNet) való hozzáférés előtt kell egy kapcsolatot. További információkért tekintse meg a következő cikkben [alkalmazását az Azure SQL Database felügyelt példányába való csatlakozás](sql-database-managed-instance-connect-app.md).

> [!NOTE] 
> Mivel a virtuális hálózaton belül csak el felügyelt példányok [SQL Database-tűzfalszabályok](sql-database-firewall-configure.md) nem érvényesek. Felügyelt példány rendelkezik a saját [beépített tűzfal](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

## <a name="create-an-azure-ad-login-for-a-managed-instance-using-ssms"></a>Hozzon létre egy Azure AD bejelentkezési a felügyelt példányhoz az SSMS használatával

Az első Azure AD bejelentkezési kell létrehoznia a szokásos SQL Server fiók (nem azure AD), amely egy `sysadmin`. Példák a felügyelt példány kapcsolódni a következő cikkekben talál:

- [Rövid útmutató: Azure-beli virtuális gép csatlakozni egy Azure SQL Database felügyelt példányába konfigurálása](sql-database-managed-instance-configure-vm.md)
- [Rövid útmutató: Pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányába való helyszíni](sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> A felügyelt példány beállítására szolgáló Azure AD-rendszergazda hozhat létre egy Azure AD bejelentkezési belül a felügyelt példány nem használható. Az első Azure AD bejelentkezési SQL Server-fiókot, amely segítségével létre kell hoznia egy `sysadmin`. Ez egy átmeneti korlátozás, amely a rendszer eltávolítja, miután az Azure AD-bejelentkezések válnak általánosan elérhető a Ha megpróbálja használni az Azure AD rendszergazdai fiókot létrehozni a bejelentkezési adatait a következő hiba jelenik meg: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`

1. Jelentkezzen be a felügyelt példány használatával a szokásos SQL Server-fiók (nem azure AD), amely egy `sysadmin`révén [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

2. A **Object Explorer**, kattintson a jobb gombbal a kiszolgáló, és válassza a **új lekérdezés**.

3. A lekérdezésablakban, használja a következő szintaxist egy helyi bejelentkezés létrehozása az Azure AD-fiók:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Ez a példa létrehoz egy fiók bejelentkezési nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Válassza az eszköztár **Execute** létrehozni a bejelentkezési adatait.

5. Az újonnan hozzáadott bejelentkezési ellenőrzéséhez futtassa az alábbi T-SQL-parancsot:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

További információkért lásd: [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Engedélyek lehetővé teszik a felügyelt példány bejelentkezések létrehozása

Más Azure AD bejelentkezési megadásához, az SQL Server-szerepkörök vagy engedélyek rendelkeznie kell a résztvevő (SQL- vagy Azure ad-ben).

### <a name="sql-authentication"></a>SQL-hitelesítés

- Ha a bejelentkezés, egy SQL egyszerű, csak azok a bejelentkezések, amelyek részei a `sysadmin` szerepkör létrehozás bejelentkezések a create parancs használható egy Azure AD-fiókot.

### <a name="azure-ad-authentication"></a>Azure AD-hitelesítés

- Hogy az újonnan létrehozott Azure AD bejelentkezési segítségével más bejelentkezések a más Azure AD-felhasználók, csoportok vagy alkalmazások esetén adja meg a bejelentkezési `sysadmin` vagy `securityadmin` kiszolgálói szerepkört. 
- Minimális **ALTER ANY LOGIN** más Azure ad-ben bejelentkezéseket hozhasson létre az Azure AD bejelentkezési engedélyt kell adni. 
- Alapértelmezés szerint a standard szintű engedély nyújtott az újonnan létrehozott Azure ad-ben bejelentkezéseket a master, a következő: **Csatlakozás SQL** és **MEGTEKINTHETI bármely olyan ADATBÁZISÁBA**.
- A `sysadmin` kiszolgálói szerepkör számos Azure AD-felhasználónevekhez belül a felügyelt példány is megadható.

A bejelentkezés hozzáadása a `sysadmin` kiszolgálói szerepkör:

1. Jelentkezzen be a felügyelt példány újra, vagy használja a meglévő kapcsolatot a az SQL egyszerű, amely egy `sysadmin`.

1. A **Object Explorer**, kattintson a jobb gombbal a kiszolgáló, és válassza a **új lekérdezés**.

1. Adja meg az Azure AD bejelentkezési a `sysadmin` kiszolgálói szerepkör a következő T-SQL-szintaxis használatával:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    Az alábbi példában engedélyezi a `sysadmin` kiszolgálói szerepkört, a bejelentkezés nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-logins-using-ssms"></a>További létrehozása az SSMS használatával az Azure AD-bejelentkezések

Miután az Azure AD bejelentkezési létrehozott, és a megadott `sysadmin` jogokkal, az, hogy a bejelentkezés további bejelentkezések használatával hozhat létre a **az EXTERNAL PROVIDER** záradékot a **CREATE LOGIN**.

1. A felügyelt példány kiszolgáló az az Azure AD-bejelentkezéskor, az SQL Server Management Studióval csatlakozhat. Adja meg a felügyelt példány kiszolgáló nevét. A hitelesítéshez, az ssms-ben a rendszer három lehetőség közül választhat, ha az Azure AD-fiókkal jelentkezett be:

    - Az Active Directory - Universal az MFA-támogatással
    - Active Directory – jelszó
    - Active Directory – integrált </br>

    ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

    További információkért tekintse meg a következő cikket: [Univerzális hitelesítés az SQL Database és az SQL Data Warehouse használatával (SSMS-támogatás az MFA-hoz)](sql-database-ssms-mfa-authentication.md)

1. Válassza ki **Active Directory - MFA-támogatással rendelkező univerzális**. Ekkor megjelenik a multi-factor Authentication (MFA) bejelentkezési ablak. Jelentkezzen be az Azure AD-jelszó.

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. Az ssms-ben **Object Explorer**, kattintson a jobb gombbal a kiszolgáló, és válassza a **új lekérdezés**.
1. A lekérdezési ablakban használja egy másik Azure AD-fiók bejelentkezési adatokat létrehozni a következő szintaxist:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Ez a példa létrehoz egy bejelentkezés az Azure AD-felhasználó bob@aadsqlmi.net, amelynek tartomány aadsqlmi.net össze van vonva az Azure ad-ben aadsqlmi.onmicrosoft.com.

    Futtassa a következő T-SQL-parancsot. Összevont Azure AD-fiókok a helyi Windows-bejelentkezések és felhasználók számára a felügyelt példány cseréjére.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. A felügyelt példány használatával hozzon létre egy adatbázist a [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) szintaxist. Ez az adatbázis felhasználói bejelentkezések teszteléséhez a következő szakaszban használható.
    1. A **Object Explorer**, kattintson a jobb gombbal a kiszolgáló, és válassza a **új lekérdezés**.
    1. A lekérdezési ablakban a következő szintaxis használatával hozzon létre egy adatbázist **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. A felügyelt példány bejelentkezési csoport létrehozása az Azure ad-ben. A csoport az Azure ad-ben a bejelentkezés hozzáadása egy felügyelt példányra előtt léteznie kell. Lásd: [létrehozásához, és adja hozzá az Azure Active Directoryval tagok](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Hozzon létre egy csoportot _mygroup_ és tagokat vehet fel ebbe a csoportba.

1. Az SQL Server Management Studióban nyissa meg egy új lekérdezési ablak.

    Ez a példa feltételezi, hogy létezik egy nevű csoportot _mygroup_ az Azure AD-ben. Hajtsa végre a következő parancsot:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Egy teszt jelentkezik be a felügyelt példányhoz az újonnan létrehozott bejelentkezés vagy csoporttal. Nyissa meg a felügyelt példány új kapcsolatot, és az új bejelentkezés hitelesítéséhez használt.
1. A **Object Explorer**, kattintson a jobb gombbal a kiszolgáló, és válassza a **új lekérdezés** az új kapcsolat.
1. Az újonnan létrehozott kiszolgáló engedélyeinek ellenőrzése az Azure AD-bejelentkezés a következő parancs végrehajtásával:

    ```sql
    SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
    GO
    ```

> [!NOTE]
> Az Azure AD-vendégfelhasználók támogatottak a felügyelt példány bejelentkezéseket, csak akkor, ha az Azure AD-csoport részeként hozzá. Egy Azure ad-ben vendégfelhasználó egy fiók, amely az Azure ad-ben a felügyelt példányhoz tartozó, egy másik Azure AD-ből a cégbe. Ha például joe@contoso.com (Azure AD-fiókot) vagy steve@outlook.com (MSA-fiók) is hozzáadhatók az Azure ad-ben aadsqlmi csoporthoz. Miután a felhasználók egy csoportba kerülnek, a bejelentkezési hozhat létre a felügyelt példány **fő** adatbázis csoportot használó a **CREATE LOGIN** szintaxist. Ez a csoport tagjai vendégfelhasználók csatlakozhat a felügyelt példányhoz az aktuális bejelentkezési adatok használata (például joe@contoso.com vagy steve@outlook.com).

## <a name="create-an-azure-ad-user-from-the-azure-ad-login-and-give-permissions"></a>Egy Azure AD-felhasználó létrehozása az Azure AD bejelentkezési és a jogosultságok megadása

Az egyes adatbázisok engedélyezési működik szinte ugyanúgy a felügyelt példány, mint a helyszíni SQL Server. A felhasználó is hozható létre egy meglévő bejelentkezést egy adatbázisban, és a megadott engedélyekkel az adatbázishoz, vagy egy adatbázis-szerepkörhöz hozzáadott.

Most, hogy létrehoztuk a nevű adatbázist **MyMITestDB**, és a egy bejelentkezés, amely csak az alapértelmezett engedélyekkel rendelkezik, az a következő lépés, hogy hozzon létre egy felhasználót az, hogy a bejelentkezés. Jelenleg a bejelentkezési csatlakozhat a felügyelt példányhoz, és tekintse meg az összes adatbázist, de nem lehet kommunikálni az adatbázisokat. Ha jelentkezzen be az Azure AD-fiókot, amely az alapértelmezett engedélyekkel rendelkezik, és bontsa ki az újonnan létrehozott adatbázist próbál, akkor a következő hiba jelenik meg:

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Az adatbázis-engedélyek megadásának további információkért lásd: [– első lépések a Database Engine engedélyekkel](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Hozzon létre egy Azure AD-felhasználót és a egy mintául szolgáló tábla létrehozásához

1. Jelentkezzen be a felügyelt példány használatával egy `sysadmin` fiók az SQL Server Management Studio segítségével.
1. A **Object Explorer**, kattintson a jobb gombbal a kiszolgáló, és válassza a **új lekérdezés**.
1. A lekérdezésablakban használja a következő szintaxist egy Azure AD-felhasználó létrehozása az Azure AD bejelentkezési identitás:

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    A következő példában létrehozunk egy felhasználó bob@aadsqlmi.net a bejelentkezési identitás bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Az Azure AD-felhasználó létrehozása az Azure AD bejelentkezési identitás, amely a csoport is támogatott.

    A következő példában létrehozunk egy bejelentkezés az Azure AD-csoport _mygroup_ , amely megtalálható az Azure ad-ben.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Minden felhasználó tartozó **mygroup** hozzáférhet a **MyMITestDB** adatbázis.

    > [!IMPORTANT]
    > Létrehozásakor egy **felhasználói** egy Azure AD bejelentkezési identitás, adja meg a felhasználónév az ugyanazon a login_name **bejelentkezési**.

    További információkért lásd: [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. Egy új lekérdezési ablakban hozzon létre egy teszt táblázatot a következő T-SQL-parancsot:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Kapcsolat létrehozása az ssms-ben létrehozott felhasználóval. Láthatja, hogy a tábla nem látható **TestTable** által létrehozott, amely a `sysadmin` korábban. Adja meg a felhasználó jogosult olvasni az adatokat az adatbázisból kell.

1. Az aktuális engedély a felhasználó nem rendelkezik a következő parancs végrehajtásával ellenőrizheti:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Felhasználók hozzáadása és adatbázisszintű szerepkörökhöz

A felhasználó számára az adatbázisban található adatok megtekintéséhez, megadhatnánk [adatbázisszintű szerepkörök](/sql/relational-databases/security/authentication-access/database-level-roles) a felhasználó számára.

1. Jelentkezzen be a felügyelt példány használatával egy `sysadmin` fiók az SQL Server Management Studio segítségével.

1. A **Object Explorer**, kattintson a jobb gombbal a kiszolgáló, és válassza a **új lekérdezés**.

1. Adja meg az Azure AD-felhasználót a `db_datareader` adatbázis-szerepkörhöz a következő T-SQL-szintaxis használatával:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    Az alábbi példa tartalmaz a felhasználó bob@aadsqlmi.net és a csoport _mygroup_ a `db_datareader` engedélyeit a **MyMITestDB** adatbázis:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Ellenőrizze az Azure AD-felhasználót az adatbázisban létrehozott létezik a következő parancs végrehajtásával:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. A felügyelt példánynak egy új kapcsolat létrehozása a felhasználót, hogy a rendszer hozzáadta a `db_datareader` szerepkör.
1. Bontsa ki az adatbázist a **Object Explorer** , lásd a táblázatot.

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Nyisson meg egy új lekérdezési ablakot, és hajtsa végre az alábbi utasítást:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Ön láthatja a tábla adatait? A visszaadott oszlopokat kell megjelennie.

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Az Azure AD-kiszolgálószintű rendszerbiztonsági tagok (Bejelentkezések) megszemélyesítése

Felügyelt példány az Azure AD-kiszolgálószintű rendszerbiztonsági tagok (Bejelentkezések) a megszemélyesítési paramétereket támogatja.

### <a name="test-impersonation"></a>Megszemélyesítési tesztelése

1. Jelentkezzen be a felügyelt példány használatával egy `sysadmin` fiók az SQL Server Management Studio segítségével.

1. A **Object Explorer**, kattintson a jobb gombbal a kiszolgáló, és válassza a **új lekérdezés**.

1. A lekérdezési ablakban a következő paranccsal hozzon létre egy új tárolt eljárást:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. A következő paranccsal tekintheti meg, hogy a felhasználó akkor van megszemélyesítésekor, a tárolt eljárás végrehajtása esetén **bob@aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Megszemélyesítési tesztelje a végrehajtás AS bejelentkezési utasítás segítségével:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Csak az SQL kiszolgálószintű rendszerbiztonsági tagok (Bejelentkezések) részét képező a `sysadmin` szerepkör hajthat végre a következő műveleteket hajthatja végre a Azure AD rendszerbiztonsági tagok: 
> - HAJTSA VÉGRE A FELHASZNÁLÓ NEVÉBEN
> - HAJTSA VÉGRE A BEJELENTKEZÉSSEL

## <a name="using-cross-database-queries-in-managed-instances"></a>Adatbázisközi lekérdezések segítségével a felügyelt példány

Adatbázisközi lekérdezések az Azure AD-fiókok az Azure AD-bejelentkezések esetén támogatottak. Az Azure AD-csoport adatbázisközi lekérdezéseket tesztelheti, kell egy másik adatbázis és tábla létrehozása. Kihagyhatja, egy másik adatbázis és tábla létrehozása, ha egy már létezik.

1. Jelentkezzen be a felügyelt példány használatával egy `sysadmin` fiók az SQL Server Management Studio segítségével.
1. A **Object Explorer**, kattintson a jobb gombbal a kiszolgáló, és válassza a **új lekérdezés**.
1. A lekérdezési ablakban az alábbi parancs használatával hozzon létre egy adatbázist **MyMITestDB2** és nevű táblát **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. Egy új lekérdezési ablakban hajtsa végre a következő parancsot a felhasználó létrehozásához _mygroup_ az új adatbázisban található **MyMITestDB2**, és adja meg, hogy az adatbázis VÁLASSZA engedélyeket _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Jelentkezzen be a felügyelt példányhoz az SQL Server Management Studio az Azure AD-csoport tagjaként _mygroup_. Nyisson meg egy új lekérdezési ablakot, és hajtsa végre az adatbázisok közötti utasítást:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Megjelenik a táblázat eredményeinek **TestTable2**.

## <a name="additional-scenarios-supported-for-azure-ad-logins-public-preview"></a>További, az Azure AD-bejelentkezések (nyilvános előzetes verzió) támogatott alkalmazási helyzetek 

- Az SQL Agent felügyeleti és feladat-végrehajtások az Azure AD-bejelentkezések esetén támogatottak.
- Adatbázis biztonsági mentési és visszaállítási műveleteket hajthat végre az Azure AD-bejelentkezések.
- [Naplózás](sql-database-managed-instance-auditing.md) az Azure AD bejelentkezési és hitelesítési események kapcsolódó összes utasítást.
- A dedikált rendszergazdai kapcsolat az Azure AD-bejelentkezések, amelyek tagjai a `sysadmin` kiszolgáló-szerepkör.
- Az Azure AD-bejelentkezések használatával támogatottak a [sqlcmd segédprogram](/sql/tools/sqlcmd-utility) és [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) eszközt.
- Az Azure AD-bejelentkezések érkező bejelentkezési események bejelentkezési eseményindítók támogatják.
- Service Broker és a DB levelezés beállítása az Azure AD bejelentkezési adatok használata is lehet.


## <a name="next-steps"></a>További lépések

### <a name="enable-security-features"></a>Biztonsági szolgáltatások engedélyezése

A következő [felügyelt példány képességek biztonsági funkciók](sql-database-managed-instance.md#azure-sql-database-security-features) módon adatbázis biztonságossá tétele a cikk átfogó listája. A következő biztonsági szolgáltatásokat tárgyalja:

- [Felügyelt példány naplózása](sql-database-managed-instance-auditing.md) 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Fenyegetések észlelése](sql-database-managed-instance-threat-detection.md) 
- [Dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking)
- [Sorszintű biztonság](/sql/relational-databases/security/row-level-security) 
- [Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Felügyelt példány képességek

Egy Azure SQL Database felügyelt példányába képességek teljes körű áttekintést lásd:

> [!div class="nextstepaction"]
> [Felügyelt példány képességek](sql-database-managed-instance.md)