---
title: "SQL-hitelesítés: Azure SQL Database-tűzfalak, hitelesítés, hozzáférés | Microsoft Docs"
description: "Ez a kezdeti lépéseket ismertető oktatóanyag bemutatja, hogyan használható az SQL Server Management Studio és a Transact-SQL kiszolgáló- és adatbázisszintű tűzfalszabályokkal, SQL-hitelesítéssel, -bejelentkezésekkel, -felhasználókkal és -szerepkörökkel az Azure SQL Database-kiszolgálókra és adatbázisokra vonatkozó hozzáférési és felügyeleti engedélyek megadásához."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 356cc4c6d8e25d36880e4b12bf471326e61990c3
ms.openlocfilehash: 275a33567fa1472573bc8abc87948ad306e853f0


---
# <a name="sql-database-tutorial-sql-server-authentication-logins-and-user-accounts-database-roles-permissions-server-level-firewall-rules-and-database-level-firewall-rules"></a>SQL Database oktatóanyag: SQL Server-hitelesítés, bejelentkezések és felhasználói fiókok, adatbázis-szerepkörök, engedélyek, kiszolgálószintű tűzfalszabályok és adatbázisszintű tűzfalszabályok
Ez a kezdeti lépéseket ismertető oktatóanyag bemutatja, hogyan használható az SQL Server Management Studio SQL-hitelesítéssel, -bejelentkezésekkel, -felhasználókkal és adatbázis-szerepkörökkel, amelyek hozzáférést és jogosultságokat biztosítanak az Azure SQL Database-kiszolgálókra és adatbázisokra vonatkozóan. Az alábbiakat sajátítja majd el:

- Felhasználói engedélyek megtekintése a master és a felhasználói adatbázisokban
- Bejelentkezések és felhasználók létrehozása SQL Server-hitelesítés alapján
- A teljes kiszolgálóra vonatkozó és adatbázis-specifikus engedélyek kiosztása felhasználóknak
- Bejelentkezés felhasználói adatbázisba nem rendszergazda jogosultságú felhasználóval
- Adatbázisszintű tűzfalszabályok létrehozása adatbázis-felhasználók számára
- Kiszolgálószintű tűzfalszabályok létrehozása kiszolgálói rendszergazdák számára

**Becsült időtartam**: Az oktatóanyag teljesítése körülbelül 45 percet vesz igénybe (feltéve, hogy megfelel a szükséges előfeltételeknek).

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell Azure-fiókkal. [Nyithat egy ingyenes Azure-fiókot](/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Az előfizetés-tulajdonosi vagy közreműködői szerepkörhöz tartozó fiókok képesek csatlakozni az Azure Portalhoz. A szerepköralapú hozzáférés-vezérlésről (RBAC) többet is megtudhat az [Azure Portalon végzett hozzáférés-vezérlés alapvető tudnivalóit ismertető](../active-directory/role-based-access-control-what-is.md) témakörben.

* Ön elvégezte [Az Azure SQL Database-kiszolgálók, -adatbázisok és -tűzfalszabályok Azure Portallal és az SQL Server Management Studióval történő használatának első lépései](sql-database-get-started.md) oktatóanyagot vagy az azzal egyenértékű [PowerShell-verziót](sql-database-get-started-powershell.md). Ha még nem tette, végezze el ezt az előfeltételként szolgáló oktatóanyagot, vagy hajtsa végre az oktatóanyag [PowerShell-verziójának](sql-database-get-started-powershell.md) végén található PowerShell-szkriptet a továbblépés előtt.

> [!NOTE]
> Ez az oktatóanyag a következő témakörök tartalmát segít elsajátítani: [SQL Database-hozzáférés és -felügyelet](sql-database-control-access.md), [Bejelentkezések, felhasználók és adatbázis-szerepkörök](sql-database-manage-logins.md), [Résztvevők](https://msdn.microsoft.com/library/ms181127.aspx), [Adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx) és [SQL Database-tűzfalszabályok](sql-database-firewall-configure.md).
>  

## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Jelentkezzen be az Azure Portalra az Azure-fiókkal.
[Meglévő előfizetés](https://account.windowsazure.com/Home/Index) használata esetén kövesse az alábbi lépéseket az Azure Portalhoz való csatlakozáshoz.

1. Nyisson meg egy tetszőleges böngészőt, és csatlakozzon az [Azure Portalhoz](https://portal.azure.com/).
2. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
3. A **Bejelentkezés** oldalon adja meg az előfizetése hitelesítő adatait.
   
   ![Bejelentkezés](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="view-information-about-the-security-configuration-for-your-logical-server"></a>A logikai kiszolgáló biztonsági konfigurációjával kapcsolatos információk megtekintése

Az oktatóanyag ezen szakaszában a logikai kiszolgáló biztonsági konfigurációjával kapcsolatos információkat tekintjük meg az Azure Portalon.

1. Nyissa meg a logikai kiszolgáló **SQL Server** paneljét, és tekintse át az információkat az **Áttekintés** lapon.

   ![Kiszolgálói rendszergazdai fiók az Azure Portalon](./media/sql-database-control-access-sql-authentication-get-started/sql_admin_portal.png)

2. Jegyezze fel a logikai kiszolgáló Kiszolgálói rendszergazdai fiókjának nevét. Ha nem emlékszik a jelszóra, kattintson a **Jelszó alaphelyzetbe állítása** gombra egy új jelszó megadásához.

> [!NOTE]
> A kiszolgáló kapcsolódási információinak áttekintéséhez lépjen a [Kiszolgálóbeállítások megtekintése és frissítése](sql-database-view-update-server-settings.md) szakaszra. A jelen oktatóanyag-sorozat esetében a teljes kiszolgálónév „sqldbtutorialserver.database.windows.net”.
>

## <a name="connect-to-sql-server-using-sql-server-management-studio-ssms"></a>Csatlakozás az SQL Serverhez az SQL Server Management Studio (SSMS) használatával

1. Amennyiben ezt még nem tette meg, töltse le és telepítése az SSMS legújabb verzióját az [SQL Server Management Studio letöltését lehetővé tévő](https://msdn.microsoft.com/library/mt238290.aspx) weboldalon. Ahhoz, hogy naprakész maradhasson, az SSMS legújabb verziója értesíti arról, ha egy új verzió válik letölthetővé.

2. Telepítés után írja be a Windows keresőmezőbe a **Microsoft SQL Server Management Studio** kifejezést, majd nyomja le az **Enter** billentyűt az SSMS megnyitásához.

   ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)

3. Adja meg a megfelelő adatokat a **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen, hogy SQL Server-hitelesítéssel és a Kiszolgálói rendszergazdai fiókkal csatlakozhasson az SQL Serverhez.

   ![kapcsolódás a kiszolgálóhoz](./media/sql-database-get-started/connect-to-server.png)

4. Kattintson a **Connect** (Csatlakozás) gombra.

   ![csatlakoztatva a kiszolgálóhoz](./media/sql-database-get-started/connected-to-server.png)

## <a name="view-the-server-admin-account-and-its-permissions"></a>Rendszergazdai kiszolgálói fiók és engedélyeinek megtekintése 
Az oktatóanyag ezen szakaszában a kiszolgálói rendszergazdai fiókkal és annak engedélyeivel kapcsolatos információkat tekintjük meg a master és a felhasználói adatbázisokban.

1. Az Object Explorerben bontsa ki a **Security** (Biztonság), majd a **Logins** (Bejelentkezések) menüt az Azure SQL Database kiszolgáló meglévő bejelentkezéseinek megtekintéséhez. Megjelenik egy bejelentkezés az üzembe helyezés során meghatározott Kiszolgálói rendszergazdai fiókhoz – az oktatóanyag-sorozat esetében ez az sqladmin bejelentkezés.

   ![Kiszolgáló-rendszergazdai bejelentkezés](./media/sql-database-control-access-sql-authentication-get-started/server_admin_login.png)

2. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok), a **System Databases** (Rendszeradatbázisok), a **master**, a **Security** (Biztonság), majd a **Users** (Felhasználók) elemet. Létrejött egy felhasználói fiók a master adatbázisban a Kiszolgálói rendszergazdai bejelentkezés számára, ugyanazon a néven (a neveknek nem kell egyezniük, de a keveredés elkerülése érdekében ajánlott ezt a gyakorlatot követni).

   ![master adatbázis felhasználó fiókja a kiszolgálói rendszergazda számára](./media/sql-database-control-access-sql-authentication-get-started/master_database_user_account_for_server_admin.png)

   > [!NOTE]
   > A többi megjelenő felhasználó fiókkal kapcsolatos információkért lásd: [Résztvevők](https://msdn.microsoft.com/library/ms181127.aspx).
   >

3. Az Object Explorerben kattintson jobb gombbal a **master** elemre, majd kattintson a **New Query** (Új lekérdezés) gombra a master adatbázishoz kapcsolódó lekérdezésablak megnyitásához.
4. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely a lekérdezést végrehajtó felhasználóval kapcsolatos információkat ad vissza. A rendszer az sqladmin eredményt adja vissza a lekérdezést végrehajtó felhasználói fiókhoz (amikor az eljárás során később egy felhasználói adatbázist kérdezünk le, más eredményt kapunk majd).

   ```
   SELECT USER;
   ```

   ![felhasználólekérdezés kiválasztása a master adatbázisban](./media/sql-database-control-access-sql-authentication-get-started/select_user_query_in_master_database.png)

5. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely az sqladmin-felhasználó engedélyeivel kapcsolatos információkat ad vissza. Az sqladmin a következő engedélyekkel rendelkezik: kapcsolódás a master adatbázishoz, bejelentkezések és felhasználók létrehozása, információk kiválasztása a sys.sql_logins táblából, valamint felhasználók hozzáadása a dbmanager és dbcreator adatbázis-szerepkörökhöz. Ezekkel az engedélyekkel a nyilvános szerepkörhöz rendelt engedélyeken túl rendelkezik, amelytől mindegyik felhasználó örökli az engedélyeit (például az egyes táblákon szereplő információk kiválasztására vonatkozó engedélyeket). További információkért lásd az [Engedélyek](https://msdn.microsoft.com/library/ms191291.aspx) szakaszt.

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals p
   JOIN sys.database_permissions prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'sqladmin';
   ```

   ![kiszolgálói rendszergazdai engedélyek a master adatbázisban](./media/sql-database-control-access-sql-authentication-get-started/server_admin_permissions_in_master_database.png)

6. Az Object Explorerben bontsa ki a **blankdb**, a **Security** (Biztonság), majd a **Users** (Felhasználók) elemet. Figyelje meg, hogy nincs sqladmin nevű felhasználói fiók az adatbázisban.

   ![felhasználói fiókok a blankdb adatbázisban](./media/sql-database-control-access-sql-authentication-get-started/user_accounts_in_blankdb.png)

7. Az Object Explorerben kattintson a jobb gombbal a **blankdb** adatbázisra, majd kattintson a **New Query** (Új lekérdezés) elemre.

8. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely a lekérdezést végrehajtó felhasználóval kapcsolatos információkat ad vissza. A rendszer a dbo eredményt adja vissza a lekérdezést végző felhasználói fiókra (alapértelmezés szerint a Kiszolgálói rendszergazdai bejelentkezés mindegyik felhasználói adatbázisban a dbo felhasználói fiókra van leképezve).

   ```
   SELECT USER;
   ```

   ![felhasználólekérdezés kiválasztása a blankdb adatbázisban](./media/sql-database-control-access-sql-authentication-get-started/select_user_query_in_blankdb_database.png)

9. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely a dbo-felhasználó engedélyeivel kapcsolatos információkat ad vissza. A dbo a nyilvános szerepkör, valamint a db_owner rögzített adatbázis-szerepkör tagja. További információkért lásd: [Adatbázisszintű szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx).

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals AS p
   JOIN sys.database_permissions AS prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals AS p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals AS p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'dbo';
   ```

   ![kiszolgálói rendszergazdai engedélyek a blanksb adatbázisban](./media/sql-database-control-access-sql-authentication-get-started/server_admin_permissions_in_blankdb_database.png)

10. Ha szükséges, ismételje meg az előző három lépést az AdventureWorksLT falhasználói adatbázisára vonatkozóan.

## <a name="create-a-new-user-in-the-adventureworkslt-database-with-select-permissions"></a>Új felhasználó létrehozása az AdventureWorksLT adatbázisban SELECT (kiválasztás) engedélyekkel

Az oktatóanyag jelen szakaszában egy felhasználói fiókot hoz létre az AdventureWorksLT adatbázisban, leteszteli a felhasználó a nyilvános szerepkör tagjaként kapott engedélyeit, SELECT (kiválasztás) engedélyeket oszt ki a felhasználónak, majd ismét leteszteli a felhasználó engedélyeit.

> [!NOTE]
> Az adatbázisszintű felhasználók ([tartalmazott felhasználók](https://msdn.microsoft.com/library/ff929188.aspx)) növelik az adatbázis hordozhatóságát – ezt a képességet későbbi oktatóanyagokban tárgyaljuk majd.
>

1. Az Object Explorerben kattintson jobb gombbal az **AdventureWorksLT** elemre, majd kattintson a **New Query** (Új lekérdezés) gombra az AdventureWorksLT adatbázishoz kapcsolódó lekérdezésablak megnyitásához.
2. Hajtsa végre a következő utasítást egy user1 nevű felhasználó létrehozásához az AdventureWorksLT adatbázisban.

   ```
   CREATE USER user1
   WITH PASSWORD = 'p@ssw0rd';
   ```
   ![new user user1 AdventureWorksLT](./media/sql-database-control-access-sql-authentication-get-started/new_user_user1_aw.png)

3. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely a user1 engedélyeivel kapcsolatos információkat ad vissza. A user1 felhasználó kizárólag a nyilvános szerepkörből örökölt engedélyekkel rendelkezik.

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals AS p
   JOIN sys.database_permissions AS prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals AS p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals AS p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'user1';
   ```

   ![új felhasználó engedélyei a felhasználói adatbázisokban](./media/sql-database-control-access-sql-authentication-get-started/new_user_permissions_in_user_database.png)

4. A következő lekérdezések végrehajtásával kíséreljen meg lekérdezni egy táblát a user1 felhasználóval az AdventureWorksLT adatbázisban.

   ```
   EXECUTE AS USER = 'user1';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![nincsenek select engedélyek](./media/sql-database-control-access-sql-authentication-get-started/no_select_permissions.png)

5. A következő utasítás végrehajtásával adjon SELECT (kiválasztás) engedélyeket a SalesLT séma ProductCategory tábláján a user1 felhasználó számára.

   ```
   GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to user1;
   ```

   ![select engedélyek kiosztása](./media/sql-database-control-access-sql-authentication-get-started/grant_select_permissions.png)

6. A következő lekérdezések végrehajtásával kíséreljen meg lekérdezni egy táblát a user1 felhasználóval az AdventureWorksLT adatbázisban.

   ```
   EXECUTE AS USER = 'user1';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![select engedélyek](./media/sql-database-control-access-sql-authentication-get-started/select_permissions.png)

## <a name="create-a-database-level-firewall-rule-for-an-adventureworkslt-database-user"></a>Adatbázisszintű tűzfalszabály létrehozása az AdventureWorksLT adatbázis egy felhasználója számára

Az oktatóanyag jelen szakaszában megkísérel majd bejelentkezni egy másik IP-címmel rendelkező számítógépről, létrehoz egy adatbázisszintű tűzfalszabályt Kiszolgálói rendszergazdaként, majd bejelentkezik az új adatbázisszintű tűzfalszabály használatával. 

> [!NOTE]
> Az [adatbázisszintű tűzfalszabályok](sql-database-firewall-configure.md) növelik az adatbázis hordozhatóságát – ezt a képességet későbbi oktatóanyagokban tárgyaljuk majd.
>

1. Egy másik számítógépen, amelyen még nem hozott létre kiszolgálószintű tűzfalszabályt, nyissa meg az SQL Server Management Studiót.

   > [!IMPORTANT]
   > Mindig az SSMS legújabb verzióját használja, amely az [SQL Server Management Studio letöltési](https://msdn.microsoft.com/library/mt238290.aspx) weboldalán érhető el. 
   >

2. Adja meg a kiszolgáló nevét és hitelesítési adatait a **Connect to Server** (Kapcsolódás a kiszolgálóhoz) ablakban, hogy SQL Server-hitelesítéssel és csatlakozhasson a user1 fiók használatával. 
    
   ![Csatlakozás a user1 felhasználóval a rule1 tűzfalszabály nélkül](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule1.png)

3. Kattintson az **Options** (Beállítások) lehetőségre az adatbázis megadásához, amelyhez kapcsolódni szeretne, majd írja be az **AdventureWorksLT** kifejezést a **Connect to Database** (Csatlakozás az adatbázishoz) legördülő mezőbe a **Connection Properties** (Kapcsolat tulajdonságai) lapon.
   
   ![Csatlakozás a user1 felhasználóval a rule2 tűzfalszabály nélkül](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule2.png)

4. Kattintson a **Connect** (Csatlakozás) gombra. Egy párbeszédpanel jelenik meg, amely tájékoztatja, hogy a számítógép, amelyről csatlakozni kísérel meg az SQL Database adatbázishoz, nem rendelkezik az adatbázishoz hozzáférést engedő tűzfalszabállyal. A megjelenő párbeszédpanelnek két változata van, attól függően, hogy korábban melyik, tűzfalakkal kapcsolatos lépéseket hajtotta végre, de általában az első párbeszédpanel jelenik meg.

   ![Csatlakozás a user1 felhasználóval a rule3 tűzfalszabály nélkül](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule3.png)

   ![Csatlakozás a user1 felhasználóval a rule4 tűzfalszabály nélkül](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule4.png)

   > [!NOTE]
   > Az SSMS legújabb verzióiban elérhető egy funkció, amellyel az előfizetések tulajdonosai és a közreműködők bejelentkezhetnek a Microsoft Azure-ba és létrehozhatnak kiszolgálószintű tűzfalszabályokat.
   > 

4. Másolja ki az ügyfél IP-címét a párbeszédpanelről, mivel majd szükség lesz rá a 7. lépésben.
5. Kattintson a **Mégse** gombra, de ne zárja be a **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelt.
6. Váltson vissza egy olyan számítógépre, amelyen már létrehozott kiszolgálószintű tűzfalszabályt, és csatlakozzon a kiszolgálóra a Kiszolgálói rendszergazdai fiókkal.
7. Egy, az AdventureWorksLT adatbázishoz Kiszolgálói rendszergazdaként csatlakozó új lekérdezési ablakban hajtsa végre a következő utasítást egy adatbázisszintű tűzfal létrehozásához az [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) utasítás végrehajtásával a 4. lépésben felírt IP-cím használatával:

   ```
   EXEC sp_set_database_firewall_rule @name = N'AdventureWorksLTFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

   ![tűzfalszabály hozzáadása](./media/sql-database-control-access-sql-authentication-get-started/user1_add_rule_aw.png)

8. Váltson megint számítógépet, és kattintson a **Connect** (Kapcsolódás) gombra a **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen az AdventureWorksLT adatbázishoz való kapcsolódáshoz a user1 felhasználóval. 

   ![Csatlakozás a user1 felhasználóval a rule1 tűzfalszabállyal](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_rule1.png)

9. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok), az **AdventureWorksLT**, majd a **Tables** (Táblák) elemet. A user1 csak egyetlen tábla, a **SalesLT.ProductCategory** tábla megtekintéséhez rendelkezik engedéllyel. 

   ![Csatlakozás a user1 felhasználóval és az objects1 megtekintése](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_view_objects1.png)

10. Az Object Explorerben kattintson jobb gombbal a **SalesLT.ProductCategory** táblára, és kattintson a **Select Top 1000 Rows** (Első 1000 sor kiválasztása) elemre.   

   ![user1 query1](./media/sql-database-control-access-sql-authentication-get-started/user1_query1.png)

   ![user1 query1 eredményei](./media/sql-database-control-access-sql-authentication-get-started/user1_query1_results.png)

## <a name="create-a-new-user-in-the-blankdb-database-with-dbowner-database-role-permissions-and-a-database-level-firewall-rule"></a>Új felhasználó létrehozása a blankdb adatbázisban a db_owner database szerepkör engedélyeivel és egy adatbázisszintű tűzfalszabállyal

Az oktatóanyag ezen szakaszában egy új felhasználót hoz létre a blankdb adatbázisban a db_owner database szerepkör engedélyeivel, valamint létrehoz egy adatbázisszintű tűzfalszabályt az adatbázishoz a Kiszolgálói rendszergazdai fiókkal. 

1. Váltson arra a számítógépre, amely a Kiszolgálói rendszergazdai fiókkal kapcsolódik az SQL Database-hez.
2. Nyisson meg egy, a blankdb adatbázishoz kapcsolódó lekérdezési ablakot, és hajtsa végre a következő utasítást egy blankdbadmin nevű felhasználó létrehozásához a blankdb adatbázisban.

   ```
   CREATE USER blankdbadmin
   WITH PASSWORD = 'p@ssw0rd';
   ```

3. Ugyanebben a lekérdezési ablakban hajtsa végre a következő utasítást a blankdbadmin felhasználó db_owner adatbázis-szerepkörhöz történő hozzáadáshoz. A felhasználó mostantól képes a blankdb adatbázis kezeléséhez szükséges összes műveletet végrehajtani.

   ```
   ALTER ROLE db_owner ADD MEMBER blankdbadmin; 
   ```

4. Ugyanebben a lekérdezési ablakban hajtsa végre a következő utasítást egy adatbázisszintű tűzfal létrehozásához az [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) utasítás végrehajtásával. Használja az előző eljárás 4. lépésében felírt IP-címet (vagy az adatbázis felhasználóinak IP-címtartományát):

   ```
   EXEC sp_set_database_firewall_rule @name = N'blankdbFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

5. Váltson számítógépet (egy olyan számítógépre lépjen át, amelyhez már hozott létre adatbázisszintű tűzfalszabályt), és csatlakozzon a blankdb adatbázishoz a blankdbadmin felhasználói fiók használatával.
6. Nyisson meg egy, a blankdb adatbázishoz kapcsolódó lekérdezési ablakot, és hajtsa végre a következő utasítást egy blankdbuser1 nevű felhasználó létrehozásához a blankdb adatbázisban.

   ```
   CREATE USER blankdbuser1
   WITH PASSWORD = 'p@ssw0rd';
   ```
 
7. Amennyiben az oktatási környezetben szükséges, hozzon létre egy újabb adatbázisszintű tűzfalszabályt ehhez a felhasználóhoz. 

## <a name="create-a-new-login-and-user-in-the-master-database-with-dbmanager-permissions-and-create-a-server-level-firewall-rule"></a>Új bejelentkezés és felhasználó létrehozása a master adatbázisban dbmanager engedélyekkel, valamint kiszolgálószintű tűzfalszabály létrehozása

Az oktatóanyag jelen szakaszában egy bejelentkezést hoz létre a master adatbázisban az új felhasználó adatbázisok létrehozására és felügyeletére jogosító engedélyekkel. Emellett létrehoz egy további kiszolgálószintű tűzfalszabályt is a Transact-SQL használatával az [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) utasítással.

> [!NOTE]
> Az adatbázis létrehozására vonatkozó engedélyek másik felhasználóra delegálásához a Kiszolgálói rendszergazdai fiók tulajdonosának létre kell hoznia felhasználókat a master adatbázisban, valamint egy felhasználót is létre kell hozni az egyik bejelentkezésből. A bejelentkezések és az azokra épülő felhasználók létrehozása csökkenti az adatbázis hordozhatóságát, aminek a következményeit későbbi oktatóanyagokban tárgyaljuk majd – beleértve ennek előrejelzését és kezelését a vészhelyreállítási tervek készítése során.
>

1. Váltson arra a számítógépre, amely a Kiszolgálói rendszergazdai fiókkal kapcsolódik az SQL Database-hez.
2. Nyisson meg egy, a master adatbázishoz kapcsolódó lekérdezési ablakot, és hajtsa végre a következő utasítást egy dbcreator nevű bejelentkezés létrehozásához a master adatbázisban.

   ```
   CREATE LOGIN dbcreator
   WITH PASSWORD = 'p@ssw0rd';
   ```

3. Ugyanebben a lekérdezési ablakban 

   ```
   CREATE USER dbcreator
   FROM LOGIN dbcreator;
   ```

3. Ugyanebben a lekérdezési ablakban hajtsa végre a következő lekérdezést, amely hozzáadja a dbcreator felhasználót a dbmanager adatbázis-szerepkörhöz. Ez a felhasználó mostantól létrehozhat adatbázisokat, és felügyelheti azokat.

   ```
   ALTER ROLE dbmanager ADD MEMBER dbcreator; 
   ```

4. Ugyanebben a lekérdezési ablakban hajtsa végre a következő lekérdezést, amely egy kiszolgálószintű tűzfalat hoz létre az [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) utasítással. Használja az adott környezet IP-címét:

   ```
   EXEC sp_set_firewall_rule @name = N'dbcreatorFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

5. Váltson számítógépet (egy olyan számítógépre, amelyhez már hozott létre kiszolgálószintű tűzfalszabályt), és csatlakozzon a master adatbázishoz a dbcreator felhasználói fiók használatával.
6. Nyisson meg egy, a master adatbázishoz kapcsolódó lekérdezési ablakot, és hajtsa végre a következő lekérdezést egy foo nevű adatbázis létrehozásához.

   ```
   CREATE DATABASE FOO (EDITION = 'basic');
   ```
 7. Szükség esetén törölje ezt az adatbázist (így pénzt takaríthat meg) a következő utasítás használatával:

   ```
   DROP DATABASE FOO;
   ```

## <a name="complete-script"></a>Teljes szkript

A bejelentkezések és felhasználók létrehozásához, szerepkörökhöz rendeléséhez, engedélyeik kiosztásához, valamint adatbázisszintű és kiszolgálószintű tűzfalszabályok létrehozásához hajtsa végre a következő utasításokat a megfelelő adatbázisokban a kiszolgálón.

### <a name="master-database"></a>master adatbázis
Hajtsa végre a következő utasításokat a master adatbázisban a Kiszolgálói rendszergazdai fiók használatával. Adja hozzá a megfelelő IP-címeket vagy a címtartományt.

```
CREATE LOGIN dbcreator WITH PASSWORD = 'p@ssw0rd';
CREATE USER dbcreator FROM LOGIN dbcreator;
ALTER ROLE dbmanager ADD MEMBER dbcreator;
EXEC sp_set_firewall_rule @name = N'dbcreatorFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
```

### <a name="adventureworkslt-database"></a>AdventureWorksLT adatbázis
Hajtsa végre a következő utasításokat az AdventureWorksLT adatbázisban a Kiszolgálói rendszergazdai fiók használatával. Adja hozzá a megfelelő IP-címeket vagy a címtartományt.

```
CREATE USER user1 WITH PASSWORD = 'p@ssw0rd';
GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to user1;
EXEC sp_set_database_firewall_rule @name = N'AdventureWorksLTFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
```

### <a name="blankdb-database"></a>blankdb adatbázis
Hajtsa végre a következő utasításokat a blankdb adatbázisban a Kiszolgálói rendszergazdai fiók használatával. Adja hozzá a megfelelő IP-címeket vagy -címtartományt.

```
CREATE USER blankdbadmin
   WITH PASSWORD = 'p@ssw0rd';
ALTER ROLE db_owner ADD MEMBER blankdbadmin;
EXEC sp_set_database_firewall_rule @name = N'blankdbFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
CREATE USER blankdbuser1
   WITH PASSWORD = 'p@ssw0rd';
```

## <a name="next-steps"></a>Következő lépések
- Az SQL Database hozzáféréseinek és felügyeletének áttekintéséről az [SQL Database-hozzáférés és -felügyelet](sql-database-control-access.md) részben olvashat.
- Az SQL Database bejelentkezéseinek, felhasználóinak és adatbázis-szerepköreinek áttekintését a [Bejelentkezések, felhasználók és adatbázis-szerepkörök](sql-database-manage-logins.md) részben találja.
- További információ az adatbázis résztvevőivel kapcsolatban: [Résztvevők](https://msdn.microsoft.com/library/ms181127.aspx).
- További információ az adatbázis-szerepkörökkel kapcsolatban: [Adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx).
- További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](sql-database-firewall-configure.md).
- Az Azure Active Directory-hitelesítés használatával foglalkozó oktatóanyagért lásd: [SQL Database-oktatóanyag: AAD-hitelesítés, bejelentkezések és felhasználói fiókok, adatbázis-szerepkörök, engedélyek, kiszolgálószintű tűzfalszabályok és adatbázisszintű tűzfalszabályok](sql-database-control-access-sql-authentication-get-started.md).




<!--HONumber=Jan17_HO3-->


