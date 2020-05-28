---
title: SQL felügyelt példányok biztonsága az Azure AD-kiszolgáló rendszerbiztonsági azonosítójával (bejelentkezések)
description: Ismerje meg az Azure SQL felügyelt példányának védelmét szolgáló technikákat és szolgáltatásokat, valamint az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) használatát
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 54195652cfdf476f364a31bf710852dab33fb154
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053482"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>Oktatóanyag: az Azure SQL felügyelt példányának biztonsága Azure AD Server-rendszerbiztonsági tag használatával (bejelentkezések)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az SQL felügyelt példánya szinte minden olyan biztonsági funkciót biztosít, amelyet a legújabb SQL Server helyszíni (Enterprise Edition) adatbázismotor tartalmaz:

- A hozzáférés korlátozása elkülönített környezetben
- Identitást igénylő hitelesítési mechanizmusok használata (Azure AD, SQL-hitelesítés)
- Hitelesítés használata szerepköralapú tagságokkal és engedélyekkel
- Biztonsági funkciók engedélyezése

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Hozzon létre egy Azure Active Directory (AD) kiszolgálói rendszerbiztonsági tag (login) egy SQL felügyelt példányhoz
> - Engedélyek megadása az Azure AD-kiszolgáló rendszerrésztvevői számára (bejelentkezések) a felügyelt SQL-példányokban
> - Azure AD-felhasználók létrehozása Azure AD Server-rendszerbiztonsági tagokból (bejelentkezések)
> - Engedélyek kiosztása az Azure AD-felhasználók számára és az adatbázis biztonságának kezelése
> - Megszemélyesítés használata az Azure AD-felhasználók számára
> - Adatbázisok közötti lekérdezések használata az Azure AD-felhasználókkal
> - Ismerje meg a biztonsági funkciókat, például a fenyegetések elleni védelmet, a naplózást, az adatmaszkolást és a titkosítást

További információ: az [Azure SQL felügyelt példányának áttekintése](sql-managed-instance-paas-overview.md). 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- SQL felügyelt példány
  - Kövesse ezt a cikket: gyors útmutató [: SQL felügyelt példány létrehozása](instance-create-quickstart.md)
- Elérheti az SQL felügyelt példányát, és [üzembe helyezhet egy Azure ad-rendszergazdát a felügyelt SQL-példányhoz](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). További tudnivalókért lásd:
  - [Az alkalmazás összekötése egy felügyelt SQL-példánnyal](connect-application-instance.md)
  - [SQL felügyelt példány kapcsolati architektúrája](connectivity-architecture-overview.md)
  - [Azure Active Directory hitelesítés konfigurálása és kezelése SQL-sel](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>Hozzáférés korlátozása 

A felügyelt SQL-példányok magánhálózati IP-címen keresztül érhetők el. Az elszigetelt SQL Server helyszíni környezetekhez hasonlóan az alkalmazásoknak és a felhasználóknak az SQL felügyelt példány hálózatához (VNet) is hozzá kell férniük a kapcsolat létrehozása előtt. További információt a következő cikkben talál az [alkalmazás összekapcsolásához egy SQL-felügyelt példányhoz](connect-application-instance.md).

Egy szolgáltatás-végpontot is beállíthat egy SQL felügyelt példányon, amely lehetővé teszi a nyilvános kapcsolatok használatát, ugyanúgy, mint a Azure SQL Database.
További információkért tekintse meg a [nyilvános végpont konfigurálása az Azure SQL felügyelt példányon](public-endpoint-configure.md)című cikket.

> [!NOTE]
> Még ha engedélyezve vannak a szolgáltatási végpontok, [Azure SQL Database tűzfalszabályok](../database/firewall-configure.md) nem érvényesek. Az Azure SQL felügyelt példányának saját [beépített tűzfala](management-endpoint-verify-built-in-firewall.md) van a kapcsolat kezeléséhez.

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>Azure AD-kiszolgáló rendszerbiztonsági tag (login) létrehozása a SSMS használatával

Az első Azure AD-kiszolgálói rendszerbiztonsági tag (login) a szabványos SQL-rendszergazdai fiók (nem Azure AD) `sysadmin` , vagy a létesítési folyamat során létrehozott SQL felügyelt példány Azure ad-rendszergazdája által hozható létre. További információ: Azure Active Directory- [rendszergazda létesítése az SQL felügyelt példányához](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Az SQL felügyelt példányhoz való kapcsolódásra vonatkozó példákat a következő cikkekben talál:

- [Gyors útmutató: az Azure virtuális gép konfigurálása egy felügyelt SQL-példányhoz való kapcsolódáshoz](connect-vm-instance-configure.md)
- [Gyors útmutató: pont – hely kapcsolat konfigurálása egy SQL felügyelt példányhoz a helyszíni környezetből](point-to-site-p2s-configure.md)

1. Jelentkezzen be az SQL felügyelt példányára egy szabványos SQL bejelentkezési fiókkal (nem Azure AD), amely egy `sysadmin` vagy Azure ad-rendszergazda a mi számára, [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms)használatával.

2. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.

3. A lekérdezési ablakban a következő szintaxis használatával hozzon létre egy helyi Azure AD-fiókhoz tartozó bejelentkezési azonosítót:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Ez a példa egy bejelentkezési azonosítót hoz létre a fiókhoz nativeuser@aadsqlmi.onmicrosoft.com .

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

    ![Native-login. png](./media/aad-security-configure-tutorial/native-login.png)

További információ: [create login (bejelentkezés létrehozása](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)).

## <a name="grant-permissions-to-create-logins"></a>Engedélyek megadása bejelentkezések létrehozásához

Más Azure AD-kiszolgálói rendszerbiztonsági tag (login) létrehozásához SQL Server szerepköröket vagy engedélyeket kell megadnia a rendszerbiztonsági tag számára (SQL vagy Azure AD).

### <a name="sql-authentication"></a>SQL-hitelesítés

- Ha a bejelentkezés egy SQL-rendszerbiztonsági tag, csak a szerepkör részét képező bejelentkezések `sysadmin` használhatják a Create parancsot az Azure ad-fiókhoz tartozó bejelentkezések létrehozásához.

### <a name="azure-ad-authentication"></a>Azure AD-hitelesítés

- Ha engedélyezni szeretné az újonnan létrehozott Azure AD-kiszolgáló rendszerbiztonsági tag (login) számára más bejelentkezési adatok létrehozását más Azure AD-felhasználók,-csoportok vagy-alkalmazások számára, adja meg a bejelentkezési `sysadmin` vagy `securityadmin` kiszolgálói szerepkört.
- Az Azure AD-kiszolgáló rendszerbiztonsági tagjának (bejelentkezési azonosítójának) minden más Azure AD-kiszolgáló-rendszerbiztonsági tag (login) létrehozásához meg kell adni legalább a **bejelentkezési** engedélyeket.
- Alapértelmezés szerint az újonnan létrehozott Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések) számára biztosított szabványos engedély a következő: **SQL-kapcsolat** és **bármely adatbázis megtekintése**.
- A `sysadmin` kiszolgálói szerepkör az SQL felügyelt példányain belül számos Azure ad-kiszolgálói tag (login) számára biztosítható.

A bejelentkezés hozzáadása a `sysadmin` kiszolgálói szerepkörhöz:

1. Jelentkezzen be újra az SQL felügyelt példányba, vagy használja a meglévő Azure AD-rendszergazdai vagy SQL-rendszerbiztonsági tag-t `sysadmin` .

1. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.

1. A `sysadmin` következő T-SQL szintaxissal adja meg a kiszolgálói szerepkört az Azure ad-kiszolgáló (login) számára:

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

1. Kapcsolódjon az SQL felügyelt példányhoz az Azure AD-kiszolgáló rendszerbiztonsági azonosítójával (login) SQL Server Management Studio használatával. Adja meg az SQL felügyelt példány állomásneve nevét. A SSMS-ben való hitelesítéshez három lehetőség közül választhat az Azure AD-fiókkal való bejelentkezéskor:

   - Active Directory – MFA-támogatással rendelkező univerzális
   - Active Directory – jelszó
   - Active Directory – integrált </br>

     ![SSMS-login-prompt. png](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     További információt a következő cikkben talál: [univerzális hitelesítés (SSMS-támogatás az MFA-hoz)](../database/authentication-mfa-ssms-overview.md)

1. Válassza **a Active Directory-Universal lehetőséget MFA-támogatással**. Ez egy Multi-Factor Authentication (MFA) bejelentkezési ablakot hoz létre. Jelentkezzen be az Azure AD-jelszavával.

    ![MFA-login-prompt. png](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. A SSMS **Object Explorer**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.
1. A lekérdezési ablakban a következő szintaxis használatával hozzon létre egy másik Azure AD-fiókhoz tartozó bejelentkezési azonosítót:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Ez a példa egy olyan bejelentkezési azonosítót hoz létre az Azure AD-felhasználó számára bob@aadsqlmi.net , amelynek tartományi aadsqlmi.net az Azure ad-aadsqlmi.onmicrosoft.com összevonták.

    Hajtsa végre a következő T-SQL-parancsot. Az összevont Azure AD-fiókok a helyi Windows-bejelentkezések és-felhasználók számára az SQL által felügyelt példányok helyére kerülnek.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Hozzon létre egy adatbázist az SQL felügyelt példányában az [adatbázis létrehozása](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) szintaxis használatával. Ezt az adatbázist fogjuk használni a felhasználói bejelentkezések teszteléséhez a következő szakaszban.
    1. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.
    1. A lekérdezési ablakban használja a következő szintaxist egy **MyMITestDB**nevű adatbázis létrehozásához.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. SQL felügyelt példányok bejelentkezésének létrehozása egy Azure AD-beli csoport számára. A csoportnak léteznie kell az Azure AD-ben, mielőtt felveszi a bejelentkezést az SQL felügyelt példányba. Lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Hozzon létre egy csoportot _mygroup_ , és vegyen fel tagokat ebbe a csoportba.

1. Nyisson meg egy új lekérdezési ablakot a SQL Server Management Studioban.

    Ez a példa feltételezi, hogy létezik egy _mygroup_ nevű csoport az Azure ad-ben. Hajtsa végre a következő parancsot:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Tesztként jelentkezzen be az SQL által felügyelt példányba az újonnan létrehozott bejelentkezéssel vagy csoporttal. Nyisson meg egy új kapcsolódást az SQL felügyelt példányához, és használja az új bejelentkezést hitelesítéskor.
1. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, majd válassza az új **lekérdezés** lehetőséget az új kapcsolatban.
1. A következő parancs végrehajtásával keresse meg a kiszolgáló engedélyeit az újonnan létrehozott Azure AD-kiszolgáló rendszerbiztonsági tag (login) számára:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Az Azure AD vendég felhasználói csak akkor támogatottak az SQL felügyelt példányok bejelentkezéséhez, ha az Azure AD-csoport részeként van hozzáadva. Az Azure AD vendég felhasználója egy olyan fiók, amely az SQL által felügyelt példányhoz tartozó Azure AD-hez tartozik, egy másik Azure AD-ből. Például: joe@contoso.com (Azure ad-fiók) vagy steve@outlook.com (MSA-fiók) hozzáadhatók egy csoporthoz az Azure ad-aadsqlmi. A felhasználók egy csoportba való felvétele után a bejelentkezési szintaxis **létrehozásával** létrehozhat egy bejelentkezést a csoport SQL felügyelt példány **Master** adatbázisában. Azok a vendég felhasználók, akik tagjai ennek a csoportnak, csatlakozhatnak az SQL felügyelt példányához az aktuális bejelentkezésük (például joe@contoso.com vagy steve@outlook.com ) használatával.

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Azure AD-felhasználó létrehozása az Azure AD-kiszolgáló rendszerbiztonsági azonosítójával (bejelentkezés)

Az egyes adatbázisokhoz való engedélyezés ugyanúgy működik, mint az SQL felügyelt példánya, mivel SQL Server a helyszínen. A felhasználók létrehozhatók egy adatbázis meglévő bejelentkezési adataiból, és megadhatók az adott adatbázisra vonatkozó engedélyekkel, vagy hozzáadhatók az adatbázis-szerepkörökhöz.

Most, hogy létrehoztunk egy **MyMITestDB**nevű adatbázist, és egy olyan bejelentkezési azonosítót, amely csak az alapértelmezett engedélyekkel rendelkezik, a következő lépés egy felhasználó létrehozása a bejelentkezésből. Ebben a pillanatban a bejelentkezés csatlakozhat az SQL felügyelt példányához, és megtekintheti az összes adatbázist, de nem tud kommunikálni az adatbázisokkal. Ha az alapértelmezett engedélyekkel rendelkező Azure AD-fiókkal jelentkezik be, és megpróbálja kibontani az újonnan létrehozott adatbázist, a következő hibaüzenet jelenik meg:

![SSMS-db-not-accessible. png](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

További információ az adatbázis-engedélyek megadásáról: [első lépések az adatbázismotor engedélyeivel](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Azure AD-felhasználó létrehozása és minta tábla létrehozása

1. Jelentkezzen be az SQL felügyelt példányára egy, a `sysadmin` SQL Server Management Studio használatával rendelkező fiók használatával.
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

1. Hozzon létre egy SSMS a létrehozott felhasználóval. Megfigyelheti, hogy a korábban létrehozott **TestTable** tábla nem látható `sysadmin` . A felhasználónak meg kell adnia az adatoknak az adatbázisból való beolvasásához szükséges engedélyeket.

1. A következő parancs végrehajtásával ellenőrizhető, hogy az aktuális engedély van-e a felhasználó számára:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Felhasználók hozzáadása adatbázis szintű szerepkörökhöz

Ahhoz, hogy a felhasználó megjelenjen az adatbázisban lévő adat, [adatbázis-szintű szerepköröket](/sql/relational-databases/security/authentication-access/database-level-roles) biztosíthatunk a felhasználónak.

1. Jelentkezzen be az SQL felügyelt példányára egy, a `sysadmin` SQL Server Management Studio használatával rendelkező fiók használatával.

1. A **Object Explorerban**kattintson a jobb gombbal a kiszolgálóra, és válassza az **Új lekérdezés**elemet.

1. Adja meg az Azure AD-felhasználó számára az `db_datareader` adatbázis-szerepkört a következő T-SQL szintaxis használatával:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    A következő példa a felhasználó bob@aadsqlmi.net és a csoport _mygroup_ , amely a `db_datareader` **MyMITestDB** -adatbázisra vonatkozó engedélyekkel rendelkezik:

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

1. Hozzon létre egy új kapcsolódást a felügyelt SQL-példányhoz a szerepkörhöz hozzáadott felhasználóval `db_datareader` .
1. A tábla megjelenítéséhez bontsa ki **Object Explorer** az adatbázist.

    ![SSMS-test-table. png](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. Nyisson meg egy új lekérdezési ablakot, és hajtsa végre a következő SELECT utasítást:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Meg tudja-e tekinteni a tábla adatait? Ekkor a visszaadott oszlopok láthatók.

    ![SSMS-test-Table-Query. png](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Azure AD-kiszolgáló szintű rendszerbiztonsági tag (bejelentkezések) megszemélyesítése

Az SQL felügyelt példánya támogatja az Azure AD-kiszolgáló szintű rendszerbiztonsági tag (login) megszemélyesítését.

### <a name="test-impersonation"></a>Megszemélyesítés tesztelése

1. Jelentkezzen be az SQL felügyelt példányára egy, a `sysadmin` SQL Server Management Studio használatával rendelkező fiók használatával.

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

1. A következő parancs használatával láthatja, hogy a tárolt eljárás végrehajtásakor megszemélyesített felhasználó **bob \@ aadsqlmi.net**.

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
> Csak a szerepkör részét képező SQL Server-szintű rendszerbiztonsági tag (login) `sysadmin` hajthatja végre a következő műveleteket az Azure ad résztvevői számára:
>
> - VÉGREHAJTÁS FELHASZNÁLÓKÉNT
> - VÉGREHAJTÁS BEJELENTKEZÉSKÉNT

## <a name="use-cross-database-queries"></a>Adatbázisok közötti lekérdezések használata

Az Azure ad-fiókok és az Azure AD-kiszolgáló résztvevői (bejelentkezések) esetében az adatbázisok közötti lekérdezések támogatottak. Egy Azure AD-csoporttal rendelkező adatbázis-lekérdezés teszteléséhez egy másik adatbázist és táblát kell létrehoznia. Ha már létezik, kihagyhatja egy másik adatbázis és egy tábla létrehozását.

1. Jelentkezzen be az SQL felügyelt példányára egy, a `sysadmin` SQL Server Management Studio használatával rendelkező fiók használatával.
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

1. Jelentkezzen be az SQL felügyelt példányára a SQL Server Management Studio használatával az Azure AD Group _mygroup_tagjaként. Nyisson meg egy új lekérdezési ablakot, és hajtsa végre a több adatbázisra vonatkozó SELECT utasítást:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Ekkor meg kell jelennie a tábla eredményeinek a **TestTable2**.

## <a name="additional-supported-scenarios"></a>További támogatott forgatókönyvek

- Az SQL-ügynök kezelése és a feladatok végrehajtása az Azure AD Server-rendszerbiztonsági tag (login) esetében támogatott.
- Az adatbázis biztonsági mentési és visszaállítási műveleteit az Azure AD-kiszolgáló résztvevői (bejelentkezések) hajtják végre.
- Az Azure AD-kiszolgáló rendszertagjaihoz (bejelentkezésekhez) és a hitelesítési eseményekhez kapcsolódó összes utasítás [naplózása](auditing-configure.md) .
- Dedikált rendszergazdai kapcsolatok az Azure AD-kiszolgálói rendszerbiztonsági tagok (bejelentkezések) számára, amelyek a `sysadmin` kiszolgáló szerepkör tagjai.
- Az Azure AD-kiszolgáló résztvevői (bejelentkezések) a [Sqlcmd segédprogram](/sql/tools/sqlcmd-utility) és a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) eszköz használatával támogatottak.
- A bejelentkezési eseményindítók az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) felől érkező bejelentkezési események esetén támogatottak.
- Az Service Broker-és adatbázis-levelezés beállítható az Azure AD-kiszolgáló résztvevői (bejelentkezések) használatával.

## <a name="next-steps"></a>További lépések

### <a name="enable-security-features"></a>Biztonsági funkciók engedélyezése

Tekintse meg az [SQL felügyelt példányának következő biztonsági funkcióit](sql-managed-instance-paas-overview.md#security-features) ismertető cikket, amely átfogó listát nyújt az adatbázis biztonságossá tételéhez. A következő biztonsági funkciókat tárgyaljuk:

- [SQL felügyelt példányok naplózása](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Fenyegetések észlelése](threat-detection-configure.md)
- [Dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking)
- [Sorszintű biztonság](/sql/relational-databases/security/row-level-security)
- [Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Felügyelt példányok képességei

A felügyelt SQL-példányok képességeinek teljes áttekintését lásd:

> [!div class="nextstepaction"]
> [SQL felügyelt példány képességei](sql-managed-instance-paas-overview.md)
