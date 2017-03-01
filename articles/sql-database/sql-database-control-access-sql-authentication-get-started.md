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
ms.date: 02/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c70b3b23fa95af6614c34bd951943f0559409220
ms.openlocfilehash: cf43790c329ef156ae17579d2281c861533ec201


---
# <a name="sql-server-authentication-access-and-database-level-firewall-rules"></a>SQL Server-hitelesítés, hozzáférés és adatbázisszintű tűzfalszabályok

Ez az oktatóanyag bemutatja, hogyan használható az SQL Server Management Studio SQL-hitelesítéssel, -bejelentkezésekkel, -felhasználókkal és adatbázis-szerepkörökkel, amelyek hozzáférést és jogosultságokat biztosítanak az Azure SQL Database-kiszolgálókra és adatbázisokra vonatkozóan. Az oktatóanyag végére elsajátíthatja a következőket:

- Bejelentkezések és felhasználók létrehozása SQL Server-hitelesítés alapján
- Felhasználók hozzáadása szerepkörökhöz, valamint engedélyek megadása szerepköröknek
- Adatbázisszintű és kiszolgálószintű tűzfalszabály létrehozása T-SQL használatával 
- Csatlakozás felhasználóként egy adott adatbázishoz SSMS használatával
- Felhasználói engedélyek megtekintése a master és a felhasználói adatbázisokban

**Becsült időtartam**: Az oktatóanyag teljesítése körülbelül 45 percet vesz igénybe (feltéve, hogy megfelel a szükséges előfeltételeknek).

> [!NOTE]
> Ez az oktatóanyag a következő témakörök tartalmát segít elsajátítani: [SQL Database-hozzáférés és -felügyelet](sql-database-control-access.md), [Bejelentkezések, felhasználók és adatbázis-szerepkörök](sql-database-manage-logins.md), [Résztvevők](https://msdn.microsoft.com/library/ms181127.aspx), [Adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx) és [SQL Database-tűzfalszabályok](sql-database-firewall-configure.md). Az Azure Active Directory-hitelesítés oktatóanyagát az [Azure AD-hitelesítés első lépéseivel](sql-database-control-access-aad-authentication-get-started.md) foglalkozó témakörben találja.
>  

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-fiók**. Rendelkeznie kell Azure-fiókkal. [Nyithat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* **Azure-beli létrehozási engedélyek**. Az előfizetés-tulajdonosi vagy közreműködői szerepkörhöz tartozó fiókok képesek csatlakozni az Azure Portalhoz. A szerepköralapú hozzáférés-vezérlésről (RBAC) többet is megtudhat az [Azure Portalon végzett hozzáférés-vezérlés alapvető tudnivalóit ismertető](../active-directory/role-based-access-control-what-is.md) témakörben.

* **SQL Server Management Studio**. Az SQL Server Management Studio (SSMS) telepítéséhez a legújabb verziót az [SQL Server Management Studio letöltését lehetővé tévő](https://msdn.microsoft.com/library/mt238290.aspx) weboldalon töltheti le. Mindig az SSMS legújabb verzióját használja, amikor az Azure SQL Database-hez csatlakozik, mivel folyamatosan új funkciók jelennek meg.

* **Az alapszintű oktatóanyag lépéseinek elsajátítása**. Ön elvégezte [Az Azure SQL Database-kiszolgálók, -adatbázisok és -tűzfalszabályok Azure Portallal és az SQL Server Management Studióval történő használatának első lépései](sql-database-get-started.md) oktatóanyagot vagy az azzal egyenértékű [PowerShell-verziót](sql-database-get-started-powershell.md). Ha még nem tette, végezze el ezt az előfeltételként szolgáló oktatóanyagot, vagy hajtsa végre az oktatóanyag [PowerShell-verziójának](sql-database-get-started-powershell.md) végén található PowerShell-szkriptet a továbblépés előtt.



## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Jelentkezzen be az Azure Portalra az Azure-fiókkal.
Az eljárás ismerteti, hogyan csatlakozhat az Azure Portalhoz az Azure-fiókjával (https://account.windowsazure.com/Home/Index).

1. Nyisson meg egy tetszőleges böngészőt, és csatlakozzon az [Azure Portalhoz](https://portal.azure.com/).
2. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
3. A **Bejelentkezés** oldalon adja meg az előfizetése hitelesítő adatait.
   
   ![Bejelentkezés](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="view-logical-server-security-information-in-the-azure-portal"></a>Logikai kiszolgáló biztonsági információinak megtekintése az Azure Portalon

Az eljárás ismerteti, hogyan tekintheti meg a logikai kiszolgáló biztonsági konfigurációjával kapcsolatos információkat az Azure Portalon.

1. Nyissa meg a kiszolgáló **SQL Server** paneljét, és tekintse át az információkat az **Áttekintés** lapon.

   ![Kiszolgálói rendszergazdai fiók az Azure Portalon](./media/sql-database-control-access-sql-authentication-get-started/sql_admin_portal.png)

2. Jegyezze fel a logikai kiszolgáló kiszolgálói rendszergazdájának nevét. 

3. Ha nem emlékszik a jelszóra, kattintson a **Jelszó alaphelyzetbe állítása** gombra egy új jelszó megadásához.

4. Ha a kiszolgáló kapcsolódási adataira van szüksége, kattintson a **Tulajdonságok** elemre.

## <a name="view-server-admin-permissions-using-ssms"></a>Kiszolgálói rendszergazda engedélyek megtekintése az SSMS-sel

Az eljárás ismerteti, hogyan tekintheti meg a kiszolgálói rendszergazdai fiókkal és annak engedélyeivel kapcsolatos információkat a master és a felhasználói adatbázisokban.

1. Nyissa meg az SQL Server Management Studiót, és csatlakozzon a kiszolgálóhoz kiszolgálói rendszergazdaként SQL Server-hitelesítéssel és a kiszolgálói rendszergazdai fiókkal.

   ![kapcsolódás a kiszolgálóhoz](./media/sql-database-get-started/connect-to-server.png)

2. Kattintson a **Connect** (Csatlakozás) gombra.

   ![csatlakoztatva a kiszolgálóhoz](./media/sql-database-get-started/connected-to-server.png)

3. Az Object Explorerben bontsa ki a **Security** (Biztonság), majd a **Logins** (Bejelentkezések) menüt a kiszolgáló meglévő bejelentkezéseinek megtekintéséhez. Egy új kiszolgálón az egyedüli elérhető bejelentkezési adatok a kiszolgálói rendszergazdai fiókhoz tartoznak.

   ![Kiszolgáló-rendszergazdai bejelentkezés](./media/sql-database-control-access-sql-authentication-get-started/server_admin_login.png)

4. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok), a **System Databases** (Rendszeradatbázisok), a **master**, a **Security** (Biztonság), majd a **Users** (Felhasználók) elemet az adatbázisban a kiszolgáló-rendszergazdai bejelentkezéshez létrehozott felhasználói fiók megtekintéséhez.

   ![master adatbázis felhasználó fiókja a kiszolgálói rendszergazda számára](./media/sql-database-control-access-sql-authentication-get-started/master_database_user_account_for_server_admin.png)

   > [!NOTE]
   > A Felhasználók csomópontban megjelenő többi felhasználói fiókkal kapcsolatos információkért lásd a [résztvevőkkel](https://msdn.microsoft.com/library/ms181127.aspx) foglalkozó témakört.
   >

5. Az Object Explorerben kattintson jobb gombbal a **master** elemre, majd kattintson a **New Query** (Új lekérdezés) gombra a master adatbázishoz kapcsolódó lekérdezésablak megnyitásához.
6. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely a lekérdezést végrehajtó felhasználóval kapcsolatos információkat ad vissza. 

   ```
   SELECT USER;
   ```

   ![felhasználólekérdezés kiválasztása a master adatbázisban](./media/sql-database-control-access-sql-authentication-get-started/select_user_query_in_master_database.png)

7. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely arról ad vissza információkat, hogy az sqladmin-felhasználó milyen engedélyekkel rendelkezik a **master** adatbázisban. 

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

   >[!NOTE]
   > A kiszolgáló rendszergazdája a következő engedélyekkel rendelkezik: kapcsolódás a master adatbázishoz, bejelentkezések és felhasználók létrehozása, információk kiválasztása a sys.sql_logins táblából, valamint felhasználók hozzáadása a dbmanager és dbcreator adatbázis-szerepkörökhöz. Ezekkel az engedélyekkel a nyilvános szerepkörhöz rendelt engedélyeken túl rendelkezik, amelytől mindegyik felhasználó örökli az engedélyeit (például az egyes táblákon szereplő információk kiválasztására vonatkozó engedélyeket). További információkért lásd az [Engedélyek](https://msdn.microsoft.com/library/ms191291.aspx) szakaszt.
   >

8. Az Object Explorerben bontsa ki a **blankdb**, a **Security** (Biztonság), majd a **Users** (Felhasználók) elemet annak a felhasználói fióknak a megtekintéséhez, amelyet az adatbázisban (és az egyes felhasználói adatbázisokban) a kiszolgáló-rendszergazdai bejelentkezéshez hoztak létre.

   ![felhasználói fiókok a blankdb adatbázisban](./media/sql-database-control-access-sql-authentication-get-started/user_accounts_in_blankdb.png)

9. Az Object Explorerben kattintson a jobb gombbal a **blankdb** adatbázisra, majd kattintson a **New Query** (Új lekérdezés) elemre.

10. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely a lekérdezést végrehajtó felhasználóval kapcsolatos információkat ad vissza.

   ```
   SELECT USER;
   ```

   ![felhasználólekérdezés kiválasztása a blankdb adatbázisban](./media/sql-database-control-access-sql-authentication-get-started/select_user_query_in_blankdb_database.png)

11. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely a dbo-felhasználó engedélyeivel kapcsolatos információkat ad vissza. 

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

   > [!NOTE]
   > A dbo-felhasználó a nyilvános szerepkör, valamint a db_owner rögzített adatbázis-szerepkör tagja. További információkért lásd: [Adatbázisszintű szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx).
   >

## <a name="create-a-new-user-with-select-permissions"></a>SELECT (kiválasztás) engedélyekkel rendelkező új felhasználó létrehozása

Ez az eljárás azt ismerteti, hogyan hozhat létre egy adatbázisszintű felhasználót, tesztelheti egy új felhasználó alapértelmezett engedélyeit (a nyilvános szerepkörön keresztül), adhat a felhasználóknak **SELECT** (Kiválasztás) engedélyeket, illetve hogyan tekintheti meg ezeket a módosított engedélyeket.

> [!NOTE]
> Az adatbázisszintű, más néven [tartalmazott felhasználók](https://msdn.microsoft.com/library/ff929188.aspx) növelik az adatbázis hordozhatóságát. A hordozhatóság előnyeire vonatkozó további információkért lásd [az Azure SQL Database-biztonság geo-visszaállításhoz vagy másodlagos kiszolgálóra irányuló feladatátadáshoz történő konfigurálásával és kezelésével](sql-database-geo-replication-security-config.md) foglalkozó témakört.
>

1. Az Object Explorerben kattintson a jobb gombbal az **sqldbtutorialdb** adatbázisra, majd kattintson a **New Query** (Új lekérdezés) elemre.
2. Hajtsa végre a következő utasítást ebben a lekérdezésablakban, hogy létrejöjjön egy **user1** nevű felhasználó az sqldbtutorialdb adatbázisban.

   ```
   CREATE USER user1
   WITH PASSWORD = 'p@ssw0rd';
   ```
   ![új felhasználó user1 sqldbtutorialdb](./media/sql-database-control-access-sql-authentication-get-started/new_user_user1_aw.png)

3. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely a user1 engedélyeivel kapcsolatos információkat ad vissza.

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

   > [!NOTE]
   > Az adatbázis új felhasználói csak a nyilvános szerepkörből örökölt engedélyekkel rendelkeznek.
   >

4. Hajtsa végre a következő lekérdezéseket az **EXECUTE AS USER** utasítással, hogy megpróbálja lekérdezni az sqldbtutorialdb adatbázis SalesLT.ProductCategory tábláját csak a nyilvános szerepkörből örökölt engedélyekkel rendelkező **user1** felhasználóként.

   ```
   EXECUTE AS USER = 'user1';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![nincsenek select engedélyek](./media/sql-database-control-access-sql-authentication-get-started/no_select_permissions.png)

   > [!NOTE]
   > Alapértelmezés szerint a nyilvános szerepkör nem biztosít **SELECT** (Kiválasztás) engedélyeket a felhasználói objektumokon.
   >

5. A következő utasítás végrehajtásával adjon **SELECT** (kiválasztás) engedélyeket a SalesLT.ProductCategory táblában a **user1** felhasználónak.

   ```
   GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to user1;
   ```

   ![select engedélyek kiosztása](./media/sql-database-control-access-sql-authentication-get-started/grant_select_permissions.png)

6. Hajtsa végre a következő lekérdezéseket, hogy sikeresen lekérdezze az sqldbtutorialdb adatbázis SalesLT.ProductCategory tábláját **user1** felhasználóként.

   ```
   EXECUTE AS USER = 'user1';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![select engedélyek](./media/sql-database-control-access-sql-authentication-get-started/select_permissions.png)

## <a name="create-a-database-level-firewall-rule-using-t-sql"></a>Adatbázisszintű tűzfalszabály létrehozása a T-SQL használatával

Az eljárás azt ismerteti, hogyan hozhat létre adatbázisszintű tűzfalszabályt az [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) rendszerszintű tárolt eljárással. Egy adatbázisszintű tűzfalszabály lehetővé teszi a kiszolgálói rendszergazdának, hogy csak adott adatbázisok esetén engedjen át felhasználókat az Azure SQL Database tűzfalon.

> [!NOTE]
> Az [adatbázisszintű tűzfalszabályok](sql-database-firewall-configure.md) növelik az adatbázis hordozhatóságát. A hordozhatóság előnyeire vonatkozó további információkért lásd [az Azure SQL Database-biztonság geo-visszaállításhoz vagy másodlagos kiszolgálóra irányuló feladatátadáshoz történő konfigurálásával és kezelésével](sql-database-geo-replication-security-config.md) foglalkozó témakört.
>

> [!IMPORTANT]
> Kiszolgálószintű tűzfalszabály teszteléséhez csatlakozzon egy másik számítógépről (vagy törölje a kiszolgálószintű tűzfalszabályt az Azure Portalon).
>

1. Nyissa meg az SQL Server Management Studiót egy olyan számítógépen, amelyen nem határozott meg kiszolgálószintű tűzfalszabályt.

2. Adja meg a kiszolgáló nevét és hitelesítési adatait a **Connect to Server** (Kapcsolódás a kiszolgálóhoz) ablakban, hogy SQL Server-hitelesítéssel és a **user1** fiók használatával csatlakozhasson. 
    
   ![Csatlakozás a user1 felhasználóval a rule1 tűzfalszabály nélkül](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule1.png)

3. Kattintson az **Options** (Beállítások) elemre az adatbázis megadásához, amelyhez kapcsolódni szeretne, majd írja be az **sqldbtutorialdb** kifejezést a **Connect to Database** (Csatlakozás az adatbázishoz) legördülő mezőbe a **Connection Properties** (Kapcsolat tulajdonságai) lapon.
   
   ![Csatlakozás a user1 felhasználóval a rule2 tűzfalszabály nélkül](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule2.png)

4. Kattintson a **Connect** (Csatlakozás) gombra. 

   Egy párbeszédpanel jelenik meg, amely tájékoztatja, hogy a számítógép, amelyről csatlakozni kísérel meg az SQL Database adatbázishoz, nem rendelkezik az adatbázishoz hozzáférést engedő tűzfalszabállyal. 

   ![Csatlakozás a user1 felhasználóval a rule4 tűzfalszabály nélkül](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_no_rule4.png)


5. Másolja ki az ügyfél IP-címét a párbeszédpanelről, mivel majd szükség lesz rá a 8. lépésben.
6. Kattintson az **OK** gombra a hiba párbeszédpanelének bezárásához, de ne zárja be a **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelt.
7. Váltson vissza egy olyan számítógépre, amelyen már van kiszolgálószintű tűzfalszabály. 
8. Csatlakozzon az sqldbtutorialdb adatbázishoz az SSMS-ben kiszolgálói rendszergazdaként, és hajtsa végre a következő utasítást egy adatbázisszintű tűzfal létrehozásához az 5. lépésben található IP-cím (vagy címtartomány) használatával.  

   ```
   EXEC sp_set_database_firewall_rule @name = N'sqldbtutorialdbFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

   ![tűzfalszabály hozzáadása](./media/sql-database-control-access-sql-authentication-get-started/user1_add_rule_aw.png)

9. Váltson megint számítógépet, és kattintson a **Connect** (Kapcsolódás) gombra a **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen, hogy user1 felhasználóként csatlakozzon az sqldbtutorialdb adatbázishoz. 

   > [!NOTE]
   > Miután létrejött az adatbázisszintű tűzfalszabály, az aktiválása akár 5 percig is eltarthat.
   >

10. A sikeres csatlakozás után bontsa ki a **Databases** (Adatbázisok) elemet az Object Explorerben. Figyelje meg, hogy **user1** csak az **sqldbtutorialdb** adatbázist tekintheti meg.

   ![Csatlakozás a user1 felhasználóval a rule1 tűzfalszabállyal](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_rule1.png)

11. Bontsa ki az **sqldbtutorialdb**, majd a **Tables** (Táblák) elemet. A user1 csak egyetlen tábla, a **SalesLT.ProductCategory** tábla megtekintéséhez rendelkezik engedéllyel. 

   ![Csatlakozás a user1 felhasználóval és az objects1 megtekintése](./media/sql-database-control-access-sql-authentication-get-started/connect-user1_view_objects1.png)

## <a name="create-a-new-user-as-dbowner-and-a-database-level-firewall-rule"></a>Új felhasználó létrehozása db_owner szerepkörrel és egy adatbázisszintű tűzfalszabály meghatározása

Az eljárás azt ismerteti, hogyan hozhat létre egy felhasználót egy másik adatbázisban a db_owner adatbázis-szerepkör engedélyeivel, valamint egy adatbázisszintű tűzfalat ehhez a másik adatbázishoz. A **db_owner** szerepkörtagsággal rendelkező új felhasználók csak ehhez az önálló adatbázishoz csatlakozhatnak és csak ezt kezelhetik.

1. Váltson arra a számítógépre, amely a kiszolgálói rendszergazdai fiókkal kapcsolódik az SQL Database-hez.
2. Nyisson meg egy, a **blankdb** adatbázishoz kapcsolódó lekérdezési ablakot, és hajtsa végre a következő utasítást egy blankdbadmin nevű felhasználó létrehozásához a blankdb adatbázisban.

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
 
7. Amennyiben az oktatási környezetben szükséges, hozzon létre egy újabb adatbázisszintű tűzfalszabályt ehhez a felhasználóhoz. Ha azonban az adatbázisszintű tűzfalszabályt IP-címtartománnyal hozta létre, ezt a lépést kihagyhatja.

## <a name="grant-dbmanager-permissions-and-create-a-server-level-firewall-rule"></a>Dbmanager engedélyek megadása és kiszolgálószintű tűzfalszabály létrehozása

Az eljárás azt ismerteti, hogyan hozhat létre egy bejelentkezést a master adatbázisban az új felhasználói adatbázisok létrehozására és felügyeletére jogosító engedélyekkel. Annak bemutatására is sor kerül, hogyan hozhat létre egy további kiszolgálószintű tűzfalszabályt a Transact-SQL-lel és az [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) utasítással. 

> [!IMPORTANT]
>Az első kiszolgálószintű tűzfalszabályt mindig az Azure-ban kell létrehozni (az Azure Portalon, a PowerShell vagy a REST API segítségével).
>

> [!IMPORTANT]
> Ha a kiszolgálói rendszergazda adatbázis-létrehozási engedélyeket szeretne delegálni egy másik felhasználónak, akkor a bejelentkezéseket a master adatbázisban, a felhasználói fiókokat pedig a bejelentkezések alapján kell létrehozni. A bejelentkezések, majd az azokra épülő felhasználók létrehozása azonban csökkenti az adatbázis hordozhatóságát.
>

1. Váltson arra a számítógépre, amely a kiszolgálói rendszergazdai fiókkal kapcsolódik az SQL Database-hez.
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

4. Ugyanebben a lekérdezési ablakban hajtsa végre a következő lekérdezést, amely egy kiszolgálószintű tűzfalat hoz létre az [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) utasítással. Használja az adott környezet IP-címét:

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

### <a name="sqldbtutorialdb-database"></a>sqldbtutorialdb adatbázis
Hajtsa végre a következő utasításokat az sqldbtutorialdb adatbázisban a kiszolgálói rendszergazdai fiók használatával. Adja meg a megfelelő IP-címeket vagy címtartományt.

```
CREATE USER user1 WITH PASSWORD = 'p@ssw0rd';
GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to user1;
EXEC sp_set_database_firewall_rule @name = N'sqldbtutorialdbFirewallRule', 
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
- Az Azure Active Directory-hitelesítés használatával foglalkozó oktatóanyagért lásd: [Azure AD-hitelesítés és -engedélyek](sql-database-control-access-aad-authentication-get-started.md).




<!--HONumber=Feb17_HO3-->


