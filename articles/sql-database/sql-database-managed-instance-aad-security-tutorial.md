---
title: Felügyelt példányok biztonsága az Azure AD-kiszolgáló rendszerbiztonsági azonosítójával (bejelentkezések)
description: Ismerje meg a Azure SQL Database felügyelt példányainak biztonságossá tételéhez szükséges technikákat és szolgáltatásokat, valamint az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) használatát
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: bd65a21c2aa21643c76966410931949db7d17ad6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73822785"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>Oktatóanyag: felügyelt példányok biztonsága Azure SQL Database az Azure AD-kiszolgáló résztvevői (bejelentkezések) használatával

A felügyelt példány szinte minden olyan biztonsági funkciót biztosít, amelyet a legújabb SQL Server helyszíni (Enterprise Edition) adatbázismotor tartalmaz:

- A hozzáférés korlátozása elkülönített környezetben
- Identitást igénylő hitelesítési mechanizmusok használata (Azure AD, SQL-hitelesítés)
- Hitelesítés használata szerepköralapú tagságokkal és engedélyekkel
- Biztonsági funkciók engedélyezése

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Felügyelt példányhoz tartozó Azure Active Directory (AD) kiszolgálói rendszerbiztonsági tag (login) létrehozása
> - Engedélyek megadása az Azure AD-kiszolgáló résztvevői számára (bejelentkezések) felügyelt példányokban
> - Azure AD-felhasználók létrehozása Azure AD Server-rendszerbiztonsági tagokból (bejelentkezések)
> - Engedélyek kiosztása az Azure AD-felhasználók számára és az adatbázis biztonságának kezelése
> - Megszemélyesítés használata az Azure AD-felhasználók számára
> - Adatbázisok közötti lekérdezések használata az Azure AD-felhasználókkal
> - Ismerje meg a biztonsági funkciókat, például a fenyegetések elleni védelmet, a naplózást, az adatmaszkolást és a titkosítást

További tudnivalókat a [Azure SQL Database felügyelt példány áttekintése](sql-database-managed-instance-index.yml) és [képességei](sql-database-managed-instance.md) című cikkben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Azure SQL Database felügyelt példány
  - Kövesse ezt a cikket: gyors útmutató [: Azure SQL Database felügyelt példány létrehozása](sql-database-managed-instance-get-started.md)
- Képes hozzáférni a felügyelt példányhoz, és [kiépített egy Azure ad-rendszergazdát a felügyelt példányhoz](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). További tudnivalókért lásd:
    - [Az alkalmazás összekötése felügyelt példányokkal](sql-database-managed-instance-connect-app.md) 
    - [Felügyelt példány kapcsolati architektúrája](sql-database-managed-instance-connectivity-architecture.md)
    - [Azure Active Directory hitelesítés konfigurálása és kezelése SQL-sel](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>A felügyelt példányhoz való hozzáférés korlátozása

A felügyelt példányok magánhálózati IP-címen keresztül érhetők el. Az elszigetelt SQL Server helyszíni környezetekhez hasonlóan az alkalmazásoknak és a felhasználóknak a kapcsolat létesítése előtt hozzá kell férniük a felügyelt példány hálózatához (VNet). További információt a következő cikkben talál az [alkalmazás felügyelt példányhoz való összekapcsolásához](sql-database-managed-instance-connect-app.md).

A felügyelt példányon is konfigurálhat szolgáltatási végpontot, amely lehetővé teszi a nyilvános kapcsolatok használatát, ugyanúgy, mint a Azure SQL Database. További információkért tekintse meg a [nyilvános végpont konfigurálása Azure SQL Database felügyelt példányban](sql-database-managed-instance-public-endpoint-configure.md)című cikket.

> [!NOTE] 
> Még ha engedélyezve vannak a szolgáltatási végpontok, [SQL Database tűzfalszabályok](sql-database-firewall-configure.md) nem érvényesek. A felügyelt példány saját [beépített tűzfallal](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) rendelkezik a kapcsolatok kezeléséhez.

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>Felügyelt példányhoz tartozó Azure AD-kiszolgáló rendszerbiztonsági tag (login) létrehozása a SSMS használatával

Az első Azure AD-kiszolgálói rendszerbiztonsági tag (login) a standard SQL Server fiók (nem Azure AD) `sysadmin`vagy az üzembe helyezési folyamat során létrehozott felügyelt példányhoz tartozó Azure ad-rendszergazda által hozható létre. További információ: Azure Active Directory- [rendszergazda kiépítése a felügyelt példányhoz](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Ez a funkció az [Azure ad-kiszolgáló rendszerbiztonsági tagjai](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi)óta megváltoztak.

A felügyelt példányhoz való kapcsolódásra vonatkozó példákat a következő cikkekben találja:

- [Gyors útmutató: Azure virtuális gép konfigurálása felügyelt példányhoz való kapcsolódáshoz](sql-database-managed-instance-configure-vm.md)
- [Gyors útmutató: pont – hely kapcsolat konfigurálása felügyelt példányhoz a helyszíni környezetből](sql-database-managed-instance-configure-p2s.md)

1. Jelentkezzen be a felügyelt példányba egy standard SQL Server fiókkal (nem Azure AD), `sysadmin` amely egy vagy Azure ad-rendszergazda a mi számára, a [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance)használatával.

2. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.

3. A lekérdezési ablakban a következő szintaxis használatával hozzon létre egy helyi Azure AD-fiókhoz tartozó bejelentkezési azonosítót:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Ez a példa egy bejelentkezési azonosítót hoz létre nativeuser@aadsqlmi.onmicrosoft.coma fiókhoz.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Az eszköztáron kattintson az **Execute (végrehajtás** ) elemre a bejelentkezés létrehozásához.

5. A következő T-SQL-parancs végrehajtásával keresse meg az újonnan hozzáadott bejelentkezést:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![Native-login. png](media/sql-database-managed-instance-security-tutorial/native-login.png)

További információ: [create login (bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Engedélyek megadása a felügyelt példányok bejelentkezésének engedélyezéséhez

Más Azure AD-kiszolgálói rendszerbiztonsági tag (login) létrehozásához SQL Server szerepköröket vagy engedélyeket kell megadnia a rendszerbiztonsági tag számára (SQL vagy Azure AD).

### <a name="sql-authentication"></a>SQL-hitelesítés

- Ha a bejelentkezés egy SQL-rendszerbiztonsági tag, csak a `sysadmin` szerepkör részét képező bejelentkezések használhatják a Create parancsot az Azure ad-fiókhoz tartozó bejelentkezések létrehozásához.

### <a name="azure-ad-authentication"></a>Azure AD-hitelesítés

- Ha engedélyezni szeretné az újonnan létrehozott Azure AD-kiszolgáló rendszerbiztonsági tag (login) számára más bejelentkezési adatok létrehozását más Azure AD-felhasználók,-csoportok vagy-alkalmazások számára `sysadmin` , `securityadmin` adja meg a bejelentkezési vagy kiszolgálói szerepkört. 
- Az Azure AD-kiszolgáló rendszerbiztonsági tagjának (bejelentkezési azonosítójának) minden más Azure AD-kiszolgáló-rendszerbiztonsági tag (login) létrehozásához meg kell adni legalább a **bejelentkezési** engedélyeket. 
- Alapértelmezés szerint az újonnan létrehozott Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések) számára biztosított szabványos engedély a következő: **SQL-kapcsolat** és **bármely adatbázis megtekintése**.
- A `sysadmin` kiszolgálói szerepkör a felügyelt példányokon belül számos Azure ad-kiszolgálói rendszerbiztonsági tag (bejelentkezés) számára biztosítható.

A bejelentkezés hozzáadása a `sysadmin` kiszolgálói szerepkörhöz:

1. Jelentkezzen be újra a felügyelt példányba, vagy használja a meglévő Azure AD-rendszergazdai vagy SQL-rendszerbiztonsági `sysadmin`tag-t.

1. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.

1. A következő T-SQL szintaxissal adja meg a `sysadmin` kiszolgálói szerepkört az Azure ad-kiszolgáló (login) számára:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    Az alábbi példa megadja a `sysadmin` kiszolgálói szerepkört a bejelentkezésheznativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>További Azure AD Server-rendszerbiztonsági tag (bejelentkezések) létrehozása a SSMS használatával

Ha az Azure AD-kiszolgáló rendszerbiztonsági tag (login) létrejött, és `sysadmin` jogosultságokkal rendelkezik, a bejelentkezés létrehozhat további bejelentkezéseket a **from External Provider** záradék használatával a **create login**paranccsal.

1. Kapcsolódjon a felügyelt példányhoz az Azure AD-kiszolgáló rendszerbiztonsági azonosítójával (login) SQL Server Management Studio használatával. Adja meg a felügyelt példány gazdagépének nevét. A SSMS-ben való hitelesítéshez három lehetőség közül választhat az Azure AD-fiókkal való bejelentkezéskor:

   - Active Directory – MFA-támogatással rendelkező univerzális
   - Active Directory – jelszó
   - Active Directory – integrált </br>

     ![SSMS-login-prompt. png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     További információkért tekintse meg a következő cikket: [univerzális hitelesítés SQL Database és SQL Data Warehouse (SSMS-támogatás az MFA-hoz)](sql-database-ssms-mfa-authentication.md)

1. Válassza **a Active Directory-Universal lehetőséget MFA-támogatással**. Ez egy Multi-Factor Authentication (MFA) bejelentkezési ablakot hoz létre. Jelentkezzen be az Azure AD-jelszavával.

    ![MFA-login-prompt. png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. A SSMS **Object Explorer**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.
1. A lekérdezési ablakban a következő szintaxis használatával hozzon létre egy másik Azure AD-fiókhoz tartozó bejelentkezési azonosítót:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Ez a példa egy olyan bejelentkezési azonosítót hoz létre az bob@aadsqlmi.netAzure ad-felhasználó számára, amelynek tartományi Aadsqlmi.net az Azure ad-aadsqlmi.onmicrosoft.com összevonták.

    Hajtsa végre a következő T-SQL-parancsot. Az összevont Azure AD-fiókok felügyelt példányok a helyszíni Windows-bejelentkezések és-felhasználók számára.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Hozzon létre egy adatbázist a felügyelt példányban az [adatbázis létrehozása](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) szintaxis használatával. Ezt az adatbázist fogjuk használni a felhasználói bejelentkezések teszteléséhez a következő szakaszban.
    1. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.
    1. A lekérdezési ablakban használja a következő szintaxist egy **MyMITestDB**nevű adatbázis létrehozásához.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Felügyelt példányok bejelentkezésének létrehozása egy Azure AD-beli csoport számára. A csoportnak léteznie kell az Azure AD-ben ahhoz, hogy hozzá lehessen adni a bejelentkezést a felügyelt példányhoz. Lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Hozzon létre egy csoportot _mygroup_ , és vegyen fel tagokat ebbe a csoportba.

1. Nyisson meg egy új lekérdezési ablakot a SQL Server Management Studioban.

    Ez a példa feltételezi, hogy létezik egy _mygroup_ nevű csoport az Azure ad-ben. Hajtsa végre a következő parancsot:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Tesztként jelentkezzen be a felügyelt példányba az újonnan létrehozott bejelentkezéssel vagy csoporttal. Nyisson meg egy új kapcsolódást a felügyelt példányhoz, és használja az új bejelentkezést hitelesítéskor.
1. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, majd válassza az új **lekérdezés** lehetőséget az új kapcsolatban.
1. A következő parancs végrehajtásával keresse meg a kiszolgáló engedélyeit az újonnan létrehozott Azure AD-kiszolgáló rendszerbiztonsági tag (login) számára:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Az Azure AD vendég felhasználói csak akkor támogatottak a felügyelt példányok bejelentkezésekor, ha az Azure AD-csoport részeként van hozzáadva. Az Azure AD vendég felhasználója egy olyan fiók, amelyet a felügyelt példányhoz tartozó Azure AD-hoz egy másik Azure AD-ből kell meghívni. Például: joe@contoso.com (Azure ad-fiók) vagy steve@outlook.com (MSA-fiók) hozzáadhatók egy csoporthoz az Azure ad-aadsqlmi. A felhasználók egy csoportba való felvétele után a bejelentkezési szintaxis **létrehozásával** létrehozhat egy bejelentkezést a **master** csoport felügyelt példány főadatbázisában. Azok a vendég felhasználók, akik tagjai ennek a csoportnak, csatlakozhatnak a felügyelt példányhoz az aktuális bejelentkezésük joe@contoso.com ( steve@outlook.compéldául vagy) használatával.

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>Azure AD-felhasználó létrehozása az Azure AD-kiszolgáló rendszerbiztonsági tag (login) segítségével, és engedélyek megadása

Az önálló adatbázisokhoz való engedélyezés ugyanúgy működik, mint a felügyelt példányon, mint SQL Server a helyszínen. A felhasználók létrehozhatók egy adatbázis meglévő bejelentkezési adataiból, és megadhatók az adott adatbázisra vonatkozó engedélyekkel, vagy hozzáadhatók az adatbázis-szerepkörökhöz.

Most, hogy létrehoztunk egy **MyMITestDB**nevű adatbázist, és egy olyan bejelentkezési azonosítót, amely csak az alapértelmezett engedélyekkel rendelkezik, a következő lépés egy felhasználó létrehozása a bejelentkezésből. Jelenleg a bejelentkezés kapcsolódhat a felügyelt példányhoz, és megtekintheti az összes adatbázist, de nem tud kommunikálni az adatbázisokkal. Ha az alapértelmezett engedélyekkel rendelkező Azure AD-fiókkal jelentkezik be, és megpróbálja kibontani az újonnan létrehozott adatbázist, a következő hibaüzenet jelenik meg:

![SSMS-db-not-accessible. png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

További információ az adatbázis-engedélyek megadásáról: [első lépések az adatbázismotor engedélyeivel](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Azure AD-felhasználó létrehozása és minta tábla létrehozása

1. Jelentkezzen be a felügyelt példányba egy olyan fiókkal, amely `sysadmin` SQL Server Management Studiot használ.
1. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.
1. A lekérdezési ablakban a következő szintaxissal hozhat létre Azure AD-felhasználót egy Azure AD-kiszolgáló rendszerbiztonsági tag (login) használatával:

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    A következő példa létrehoz egy felhasználót bob@aadsqlmi.net a bejelentkezésbőlbob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Azt is támogatja, hogy Azure AD-felhasználót hozzon létre egy csoportba tartozó Azure AD-kiszolgálói rendszerbiztonsági tag (login) használatával.

    A következő példa létrehoz egy bejelentkezést az Azure ad-beli Azure AD-csoport _mygroup_ .

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    A **mygroup** -hez tartozó összes felhasználó hozzáférhet a **MyMITestDB** -adatbázishoz.

    > [!IMPORTANT]
    > Amikor egy **felhasználót** egy Azure ad-kiszolgáló rendszerbiztonsági tagja (login) alapján hoz létre, a **bejelentkezéshez**ugyanazt a login_name kell megadnia a user_name.

    További információt a [felhasználó létrehozása](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current)című témakörben talál.

1. Egy új lekérdezési ablakban hozzon létre egy teszt táblát a következő T-SQL parancs használatával:

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

1. Hozzon létre egy SSMS a létrehozott felhasználóval. Megfigyelheti, hogy a `sysadmin` korábban létrehozott **TestTable** tábla nem látható. A felhasználónak meg kell adnia az adatoknak az adatbázisból való beolvasásához szükséges engedélyeket.

1. A következő parancs végrehajtásával ellenőrizhető, hogy az aktuális engedély van-e a felhasználó számára:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Felhasználók hozzáadása adatbázis szintű szerepkörökhöz

Ahhoz, hogy a felhasználó megjelenjen az adatbázisban lévő adat, [adatbázis-szintű szerepköröket](/sql/relational-databases/security/authentication-access/database-level-roles) biztosíthatunk a felhasználónak.

1. Jelentkezzen be a felügyelt példányba egy olyan fiókkal, amely `sysadmin` SQL Server Management Studiot használ.

1. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.

1. Adja meg az Azure AD- `db_datareader` felhasználó számára az adatbázis-szerepkört a következő T-SQL szintaxis használatával:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    A következő bob@aadsqlmi.net példa a felhasználó és a csoport _mygroup_ , amely `db_datareader` a **MyMITestDB** -adatbázisra vonatkozó engedélyekkel rendelkezik:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. A következő parancs végrehajtásával győződjön meg arról, hogy az adatbázisban létrehozott Azure AD-felhasználó létezik:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Hozzon létre egy új kapcsolódást a felügyelt példányhoz azzal a felhasználóval, `db_datareader` aki hozzá lett adva a szerepkörhöz.
1. A tábla megjelenítéséhez bontsa ki **Object Explorer** az adatbázist.

    ![SSMS-test-table. png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Nyisson meg egy új lekérdezési ablakot, és hajtsa végre a következő SELECT utasítást:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Meg tudja-e tekinteni a tábla adatait? Ekkor a visszaadott oszlopok láthatók.

    ![SSMS-test-Table-Query. png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Azure AD-kiszolgáló szintű rendszerbiztonsági tag (bejelentkezések) megszemélyesítése

A felügyelt példány támogatja az Azure AD-kiszolgáló szintű rendszerbiztonsági tag (login) megszemélyesítését.

### <a name="test-impersonation"></a>Megszemélyesítés tesztelése

1. Jelentkezzen be a felügyelt példányba egy olyan fiókkal, amely `sysadmin` SQL Server Management Studiot használ.

1. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.

1. A lekérdezési ablakban a következő parancs használatával hozzon létre egy új tárolt eljárást:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. A következő parancs használatával láthatja, hogy a tárolt eljárás végrehajtásakor megszemélyesített felhasználó **bob\@aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Megszemélyesítés tesztelése a végrehajtás mint bejelentkezési utasítás használatával:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Csak a `sysadmin` szerepkör részét képező SQL Server-szintű rendszerbiztonsági tag (login) hajthatja végre a következő műveleteket az Azure ad résztvevői számára: 
> - VÉGREHAJTÁS FELHASZNÁLÓKÉNT
> - VÉGREHAJTÁS BEJELENTKEZÉSKÉNT

## <a name="using-cross-database-queries-in-managed-instances"></a>Adatbázisok közötti lekérdezések használata felügyelt példányokban

Az Azure ad-fiókok és az Azure AD-kiszolgáló résztvevői (bejelentkezések) esetében az adatbázisok közötti lekérdezések támogatottak. Egy Azure AD-csoporttal rendelkező adatbázis-lekérdezés teszteléséhez egy másik adatbázist és táblát kell létrehoznia. Ha már létezik, kihagyhatja egy másik adatbázis és egy tábla létrehozását.

1. Jelentkezzen be a felügyelt példányba egy olyan fiókkal, amely `sysadmin` SQL Server Management Studiot használ.
1. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.
1. A lekérdezési ablakban a következő parancs használatával hozzon létre egy **MyMITestDB2** nevű adatbázist és egy **TestTable2**nevű táblát:

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

1. Az új lekérdezési ablakban hajtsa végre a következő parancsot a felhasználói _mygroup_ létrehozásához az új adatbázis- **MyMITestDB2**, és adja meg az adott adatbázishoz tartozó Select engedélyeket a _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Jelentkezzen be a felügyelt példányra SQL Server Management Studio használatával az Azure AD-csoport _mygroup_tagjaként. Nyisson meg egy új lekérdezési ablakot, és hajtsa végre a több adatbázisra vonatkozó SELECT utasítást:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Ekkor meg kell jelennie a tábla eredményeinek a **TestTable2**.

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins"></a>További forgatókönyvek támogatottak az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) esetén

- Az SQL-ügynök kezelése és a feladatok végrehajtása az Azure AD Server-rendszerbiztonsági tag (login) esetében támogatott.
- Az adatbázis biztonsági mentési és visszaállítási műveleteit az Azure AD-kiszolgáló résztvevői (bejelentkezések) hajtják végre.
- Az Azure AD-kiszolgáló rendszertagjaihoz (bejelentkezésekhez) és a hitelesítési eseményekhez kapcsolódó összes utasítás [naplózása](sql-database-managed-instance-auditing.md) .
- Dedikált rendszergazdai kapcsolatok az Azure AD-kiszolgálói rendszerbiztonsági tagok (bejelentkezések) számára, amelyek `sysadmin` a kiszolgáló szerepkör tagjai.
- Az Azure AD-kiszolgáló résztvevői (bejelentkezések) a [Sqlcmd segédprogram](/sql/tools/sqlcmd-utility) és a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) eszköz használatával támogatottak.
- A bejelentkezési eseményindítók az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) felől érkező bejelentkezési események esetén támogatottak.
- Az Service Broker-és adatbázis-levelezés beállítható az Azure AD-kiszolgáló résztvevői (bejelentkezések) használatával.


## <a name="next-steps"></a>További lépések

### <a name="enable-security-features"></a>Biztonsági funkciók engedélyezése

Az adatbázis biztonságossá tételének részletes listáját a következő [felügyelt példányok biztonsági funkcióit](sql-database-managed-instance.md#azure-sql-database-security-features) ismertető cikkben találja. A következő biztonsági funkciókat tárgyaljuk:

- [Felügyelt példány naplózása](sql-database-managed-instance-auditing.md) 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Fenyegetések észlelése](sql-database-managed-instance-threat-detection.md) 
- [Dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking)
- [Sorszintű biztonság](/sql/relational-databases/security/row-level-security) 
- [Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Felügyelt példány képességei

A felügyelt példányok képességeinek teljes áttekintését lásd:

> [!div class="nextstepaction"]
> [Felügyelt példány képességei](sql-database-managed-instance.md)
