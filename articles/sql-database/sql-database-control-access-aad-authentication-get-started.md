---
title: "AAD-hitelesítés: Azure SQL Database-tűzfalak, hitelesítés, hozzáférés | Microsoft Docs"
description: "Ez a kezdeti lépéseket ismertető oktatóanyag bemutatja, hogyan használható az SQL Server Management Studio és a Transact-SQL kiszolgáló- és adatbázisszintű tűzfalszabályokkal, Azure Active Directory-hitelesítéssel, -bejelentkezésekkel, -felhasználókkal és -szerepkörökkel az Azure SQL Database-kiszolgálókra és adatbázisokra vonatkozó hozzáférési és felügyeleti engedélyek megadásához."
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
ms.sourcegitcommit: 4ef415b7c0e7079da9930ecc6d8375dfc5a3c0a9
ms.openlocfilehash: f3c8b487f23b5d1642de90d795eb2b41bfdb674d


---
# <a name="sql-database-tutorial-aad-authentication-logins-and-user-accounts-database-roles-permissions-server-level-firewall-rules-and-database-level-firewall-rules"></a>SQL Database oktatóanyag: AAD-hitelesítés, bejelentkezések és felhasználói fiókok, adatbázis-szerepkörök, engedélyek, kiszolgálószintű tűzfalszabályok és adatbázisszintű tűzfalszabályok
Ez a kezdeti lépéseket ismertető oktatóanyag bemutatja, hogyan használható az SQL Management Studio Azure Active Directory-hitelesítéssel, -bejelentkezésekkel, -felhasználókkal és adatbázis-szerepkörökkel, amelyek hozzáférést és jogosultságokat biztosítanak az Azure SQL Database-kiszolgálókra és adatbázisokra vonatkozóan. Az alábbiakat sajátítja majd el:

- Felhasználói engedélyek megtekintése a master és a felhasználói adatbázisokban
- Bejelentkezések és felhasználók létrehozása Azure Active Directory-hitelesítés alapján
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
   > Az SQL Server-hitelesítéssel kapcsolatos oktatóanyag ([SQL Database oktatóanyag: SQL Server-hitelesítés, bejelentkezések és felhasználói fiókok, adatbázis-szerepkörök, engedélyek, kiszolgálószintű tűzfalszabályok és adatbázisszintű tűzfalszabályok](sql-database-control-access-sql-authentication-get-started.md)) teljesítése nem kötelező, azonban az oktatóanyag olyan fogalmakat is ismertet, amelyeket itt nem ismétlünk meg. Az oktatóanyagban tárgyalt, a kiszolgáló- és adatbázisszintű tűzfalakra vonatkozó eljárások nem szükségesek, ha a kapcsolódó oktatóanyagot ugyanazokon a számítógépeken (ugyanazokról az IP-címekről) végezte el. Emiatt ezek a részek választhatóként vannak megjelölve. A jelen oktatóanyagban található képernyőképek feltételezik, hogy elvégezte a kapcsolódó oktatóanyagot. 
   >

* Létrehozott és feltöltött egy Azure Active Directory-könyvtárat. További információk a következő témakörökben találhatók: [Helyszíni identitások integrálása az Azure Active Directoryval](../active-directory/active-directory-aadconnect.md), [Saját tartománynév hozzáadása az Azure AD-hez](../active-directory/active-directory-add-domain.md), [A Microsoft Azure mostantól támogatja a Windows Server Active Directoryval való összevonást](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Az Azure AD-címtár felügyelete](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Az Azure AD kezelése Windows PowerShell használatával](https://msdn.microsoft.com/library/azure/jj151815.aspx) és [Hibrid identitás – szükséges portok és protokollok](../active-directory/active-directory-aadconnect-ports.md).

> [!NOTE]
> Ez az oktatóanyag a következő témakörök tartalmát segít elsajátítani: [SQL Database-hozzáférés és -felügyelet](sql-database-control-access.md), [Bejelentkezések, felhasználók és adatbázis-szerepkörök](sql-database-manage-logins.md), [Résztvevők](https://msdn.microsoft.com/library/ms181127.aspx), [Adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx), [SQL Database-tűzfalszabályok](sql-database-firewall-configure.md) és [Azure Active Directory-hitelesítés](sql-database-aad-authentication.md). 
>  

## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Jelentkezzen be az Azure Portalra az Azure-fiókkal.
[Meglévő előfizetés](https://account.windowsazure.com/Home/Index) használata esetén kövesse az alábbi lépéseket az Azure Portalhoz való csatlakozáshoz.

1. Nyisson meg egy tetszőleges böngészőt, és csatlakozzon az [Azure Portalhoz](https://portal.azure.com/).
2. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
3. A **Bejelentkezés** oldalon adja meg az előfizetése hitelesítő adatait.
   
   ![Bejelentkezés](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="provision-an-azure-active-directory-admin-for-your-sql-logical-server"></a>Azure Active Directory-rendszergazda üzembe helyezése az SQL logikai kiszolgálón

Az oktatóanyag ezen szakaszában a logikai kiszolgáló biztonsági konfigurációjával kapcsolatos információkat tekintjük meg az Azure Portalon.

1. Nyissa meg a logikai kiszolgáló **SQL Server** paneljét, és tekintse át az információkat az **Áttekintés** lapon. Figyelje meg, hogy még nincs Azure Active Directory-rendszergazda konfigurálva.

   ![Kiszolgálói rendszergazdai fiók az Azure Portalon](./media/sql-database-control-access-aad-authentication-get-started/sql_admin_portal.png)

2. Az **Alapok** panel **Nincs konfigurálva** elemére kattintva nyissa meg az **Active Directory-rendszergazda** panelt.

   ![AAD panel](./media/sql-database-control-access-aad-authentication-get-started/aad_blade.png)

3. Kattintson a **Rendszergazda beállítása** lehetőségre a **Rendszergazda hozzáadása** panel megnyitásához, majd válasszon ki egy Active Directory-felhasználói vagy -csoportfiókot a kiszolgáló Active Directory-rendszergazdájaként.

   ![AAD-rendszergazdai fiók kiválasztása](./media/sql-database-control-access-aad-authentication-get-started/aad_admin.png)

4. Kattintson a **Kiválasztás**, majd a **Mentés** gombra.

   ![Kiválasztott AAD-rendszergazdai fiók mentése](./media/sql-database-control-access-aad-authentication-get-started/aad_admin_save.png)

> [!NOTE]
> A kiszolgáló kapcsolódási információinak áttekintéséhez lépjen a [Kiszolgálóbeállítások megtekintése és frissítése](sql-database-view-update-server-settings.md) szakaszra. A jelen oktatóanyag-sorozat esetében a teljes kiszolgálónév „sqldbtutorialserver.database.windows.net”.
>

## <a name="connect-to-sql-server-using-sql-server-management-studio-ssms"></a>Csatlakozás az SQL Serverhez az SQL Server Management Studio (SSMS) használatával

1. Amennyiben ezt még nem tette meg, töltse le és telepítése az SSMS legújabb verzióját az [SQL Server Management Studio letöltését lehetővé tévő](https://msdn.microsoft.com/library/mt238290.aspx) weboldalon. Ahhoz, hogy naprakész maradhasson, az SSMS legújabb verziója értesíti arról, ha egy új verzió válik letölthetővé.

2. Telepítés után írja be a Windows keresőmezőbe a **Microsoft SQL Server Management Studio** kifejezést, majd nyomja le az **Enter** billentyűt az SSMS megnyitásához.

   ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)

3. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen válassza ki az egyik Active Directory-hitelesítési módszert, majd adja meg a szükséges hitelesítő adatokat. A módszer kiválasztásával kapcsolatos információk: [Azure Active Directory-hitelesítés](sql-database-aad-authentication.md) és [SSMS-támogatás az Azure AD MFA-ban](sql-database-ssms-mfa-authentication.md).

   ![kapcsolódás a kiszolgálóhoz az aad használatával](./media/sql-database-control-access-aad-authentication-get-started/connect_to_server_with_aad.png)

4. Adja meg a megfelelő adatokat, hogy SQL Server-hitelesítéssel és a Kiszolgálói rendszergazdai fiókkal csatlakozhasson az SQL Serverhez.

5. Kattintson a **Connect** (Csatlakozás) gombra.

   ![kapcsolódva a kiszolgálóhoz az aad használatával](./media/sql-database-control-access-aad-authentication-get-started/connected_to_server_with_aad.png)

## <a name="view-the-server-admin-account-and-its-permissions"></a>Rendszergazdai kiszolgálói fiók és engedélyeinek megtekintése 
Az oktatóanyag ezen szakaszában a kiszolgálói rendszergazdai fiókkal és annak engedélyeivel kapcsolatos információkat tekintjük meg a master és a felhasználói adatbázisokban.

1. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok), a **System Databases** (Rendszeradatbázisok), a **master**, a **Security** (Biztonság), majd a **Users** (Felhasználók) elemet. Létrejött egy felhasználói fiók a master adatbázisban az Active Directory-rendszergazda számára. Figyelje meg, hogy az Active Directory-rendszergazdai felhasználói fiókhoz nem lett bejelentkezés létrehozva.

   ![master adatbázis felhasználó fiókja az AAD-rendszergazda számára](./media/sql-database-control-access-aad-authentication-get-started/master_database_user_account_for_AAD_admin.png)

   > [!NOTE]
   > A többi megjelenő felhasználó fiókkal kapcsolatos információkért lásd: [Résztvevők](https://msdn.microsoft.com/library/ms181127.aspx).
   >

2. Az Object Explorerben kattintson jobb gombbal a **master** elemre, majd kattintson a **New Query** (Új lekérdezés) gombra a master adatbázishoz kapcsolódó lekérdezésablak megnyitásához.
3. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely a lekérdezést végrehajtó felhasználóval kapcsolatos információkat ad vissza. A rendszer az user@microsoft.com eredményt adja vissza a lekérdezést végrehajtó felhasználói fiókhoz (amikor az eljárás során később egy felhasználói adatbázist kérdezünk le, más eredményt kapunk majd).

   ```
   SELECT USER;
   ```

   ![felhasználólekérdezés kiválasztása a master adatbázisban](./media/sql-database-control-access-aad-authentication-get-started/select_user_query_in_master_database.png)

4. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely a Active Directory-rendszergazdai felhasználó engedélyeivel kapcsolatos információkat ad vissza. Az Active Directory-rendszergazdai felhasználó a következő engedélyekkel rendelkezik: kapcsolódás a master adatbázishoz, bejelentkezések és felhasználók létrehozása, információk kiválasztása a sys.sql_logins táblán, valamint felhasználók hozzáadása a dbmanager és dbcreator adatbázis-szerepkörökhöz. Ezekkel az engedélyekkel a nyilvános szerepkörhöz rendelt engedélyeken túl rendelkezik, amelytől mindegyik felhasználó örökli az engedélyeit (például az egyes táblákon szereplő információk kiválasztására vonatkozó engedélyeket). További információkért lásd az [Engedélyek](https://msdn.microsoft.com/library/ms191291.aspx) szakaszt.

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
   WHERE p.name = 'user@microsoft.com';
   ```

   ![aad-rendszergazdai engedélyek a master adatbázisban](./media/sql-database-control-access-aad-authentication-get-started/aad_admin_permissions_in_master_database.png)

6. Az Object Explorerben bontsa ki a **blankdb**, a **Security** (Biztonság), majd a **Users** (Felhasználók) elemet. Figyelje meg, hogy nincs user@microsoft.com nevű felhasználói fiók az adatbázisban.

   ![felhasználói fiókok a blankdb adatbázisban](./media/sql-database-control-access-aad-authentication-get-started/user_accounts_in_blankdb.png)

7. Az Object Explorerben kattintson a jobb gombbal a **blankdb** adatbázisra, majd kattintson a **New Query** (Új lekérdezés) elemre.

8. A lekérdezési ablakban hajtsa végre a következő lekérdezést, amely a lekérdezést végrehajtó felhasználóval kapcsolatos információkat ad vissza. A rendszer a dbo eredményt adja vissza a lekérdezést végző felhasználói fiókra (alapértelmezés szerint a Kiszolgálói rendszergazdai bejelentkezés mindegyik felhasználói adatbázisban a dbo felhasználói fiókra van leképezve).

   ```
   SELECT USER;
   ```

   ![felhasználólekérdezés kiválasztása a blankdb adatbázisban](./media/sql-database-control-access-aad-authentication-get-started/select_user_query_in_blankdb_database.png)

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

   ![kiszolgálói rendszergazdai engedélyek a blanksb adatbázisban](./media/sql-database-control-access-aad-authentication-get-started/aad_admin_permissions_in_blankdb_database.png)

10. Ha szükséges, ismételje meg az előző három lépést az AdventureWorksLT falhasználói adatbázisára vonatkozóan.

## <a name="create-a-new-user-in-the-adventureworkslt-database-with-select-permissions"></a>Új felhasználó létrehozása az AdventureWorksLT adatbázisban SELECT (kiválasztás) engedélyekkel

Az oktatóanyag jelen szakaszában egy felhasználói fiókot hoz létre az AdventureWorksLT adatbázisban egy felhasználó egyszerű Azure AD-felhasználóneve vagy egy Azure AD-csoport megjelenítendő neve alapján, leteszteli a felhasználó a nyilvános szerepkör tagjaként kapott engedélyeit, SELECT (kiválasztás) engedélyeket oszt ki a felhasználónak, majd ismét leteszteli a felhasználó engedélyeit.

> [!NOTE]
> Az adatbázisszintű felhasználók ([tartalmazott felhasználók](https://msdn.microsoft.com/library/ff929188.aspx)) növelik az adatbázis hordozhatóságát – ezt a képességet későbbi oktatóanyagokban tárgyaljuk majd.
>

1. Az Object Explorerben kattintson jobb gombbal az **AdventureWorksLT** elemre, majd kattintson a **New Query** (Új lekérdezés) gombra az AdventureWorksLT adatbázishoz kapcsolódó lekérdezésablak megnyitásához.
2. Hajtsa végre a következő utasítást, amely létrehoz egy felhasználói fiókot az AdventureWorksLT adatbázisban egy aaduser1 nevű, a Microsoft-tartományhoz tartozó felhasználó számára.

   ```
   CREATE USER [aaduser1@microsoft.com]
   FROM EXTERNAL PROVIDER;
   ```
   ![new user aaduser1@microsoft.com AdventureWorksLT](./media/sql-database-control-access-aad-authentication-get-started/new_user_aaduser1@microsoft.com_aw.png)

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
   WHERE p.name = 'aaduser1@microsoft.com';
   ```

   ![új felhasználó engedélyei a felhasználói adatbázisokban](./media/sql-database-control-access-aad-authentication-get-started/new_user_permissions_in_user_database.png)

4. A következő lekérdezések végrehajtásával kíséreljen meg lekérdezni egy táblát a user1 felhasználóval az AdventureWorksLT adatbázisban.

   ```
   EXECUTE AS USER = 'aaduser1@microsoft.com';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![nincsenek select engedélyek](./media/sql-database-control-access-aad-authentication-get-started/no_select_permissions.png)

5. A következő utasítás végrehajtásával adjon SELECT (kiválasztás) engedélyeket a SalesLT séma ProductCategory tábláján a user1 felhasználó számára.

   ```
   GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to [aaduser1@microsoft.com];
   ```

   ![select engedélyek kiosztása](./media/sql-database-control-access-aad-authentication-get-started/grant_select_permissions.png)

6. A következő lekérdezések végrehajtásával kíséreljen meg lekérdezni egy táblát a user1 felhasználóval az AdventureWorksLT adatbázisban.

   ```
   EXECUTE AS USER = 'aaduser1@microsoft.com';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![select engedélyek](./media/sql-database-control-access-aad-authentication-get-started/select_permissions.png)

## <a name="create-a-database-level-firewall-rule-for-adventureworkslt-database-users"></a>Adatbázisszintű tűzfalszabály létrehozása az AdventureWorksLT adatbázis felhasználói számára

> [!NOTE]
> Ezt az eljárást nem kell végrehajtania, ha végrehajtotta az ezzel egyenértékű eljárást a kapcsolódó SQL Server-hitelesítési oktatóanyagban ([SQL Database oktatóanyag: SQL Server-hitelesítés, bejelentkezések és felhasználói fiókok, adatbázis-szerepkörök, engedélyek, kiszolgálószintű tűzfalszabályok és adatbázisszintű tűzfalszabályok](sql-database-control-access-sql-authentication-get-started.md)), és ugyanazon a számítógépen, ugyanarról az IP-címről folytatja a tanulást.
>

Az oktatóanyag jelen szakaszában megkísérel majd bejelentkezni az új felhasználói fiókkal egy másik IP-címmel rendelkező számítógépről, létrehoz egy adatbázisszintű tűzfalszabályt Kiszolgálói rendszergazdaként, majd sikeresen bejelentkezik az új adatbázisszintű tűzfalszabály használatával. 

> [!NOTE]
> Az [adatbázisszintű tűzfalszabályok](sql-database-firewall-configure.md) növelik az adatbázis hordozhatóságát – ezt a képességet későbbi oktatóanyagokban tárgyaljuk majd.
>

1. Egy másik számítógépen, amelyen még nem hozott létre kiszolgálószintű tűzfalszabályt, nyissa meg az SQL Server Management Studiót.

   > [!IMPORTANT]
   > Mindig az SSMS legújabb verzióját használja, amely az [SQL Server Management Studio letöltési](https://msdn.microsoft.com/library/mt238290.aspx) weboldalán érhető el. 
   >

2. Adja meg a kiszolgáló nevét és hitelesítési adatait a **Connect to Server** (Kapcsolódás a kiszolgálóhoz) ablakban, hogy SQL Server-hitelesítéssel is csatlakozhasson a aaduser1@microsoft.com fiók használatával. 
    
   ![Csatlakozás a aaduser1@microsoft.com felhasználóval a rule1 tűzfalszabály nélkül](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule1.png)

3. Kattintson az **Options** (Beállítások) lehetőségre az adatbázis megadásához, amelyhez kapcsolódni szeretne, majd írja be az **AdventureWorksLT** kifejezést a **Connect to Database** (Csatlakozás az adatbázishoz) legördülő mezőbe a **Connection Properties** (Kapcsolat tulajdonságai) lapon.
   
   ![Csatlakozás az aaduser1 felhasználóval a rule2 tűzfalszabály nélkül](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule2.png)

4. Kattintson a **Connect** (Csatlakozás) gombra. Egy párbeszédpanel jelenik meg, amely tájékoztatja, hogy a számítógép, amelyről csatlakozni kísérel meg az SQL Database adatbázishoz, nem rendelkezik az adatbázishoz hozzáférést engedő tűzfalszabállyal. A megjelenő párbeszédpanelnek két változata van, attól függően, hogy korábban melyik, tűzfalakkal kapcsolatos lépéseket hajtotta végre, de általában az első párbeszédpanel jelenik meg.

   ![Csatlakozás a user1 felhasználóval a rule3 tűzfalszabály nélkül](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule3.png)

   ![Csatlakozás a user1 felhasználóval a rule4 tűzfalszabály nélkül](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule4.png)

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

   ![rule4 adatbázisszintű tűzfalszabály hozzáadása](./media/sql-database-control-access-aad-authentication-get-started/aaduser1_add_rule_aw.png)

8. Váltson megint számítógépet, és kattintson a **Connect** (Kapcsolódás) gombra a **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen az AdventureWorksLT adatbázishoz való kapcsolódáshoz az aaduser1 felhasználóval. 

9. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok), az **AdventureWorksLT**, majd a **Tables** (Táblák) elemet. A user1 csak egyetlen tábla, a **SalesLT.ProductCategory** tábla megtekintéséhez rendelkezik engedéllyel. 

10. Az Object Explorerben kattintson jobb gombbal a **SalesLT.ProductCategory** táblára, és kattintson a **Select Top 1000 Rows** (Első 1000 sor kiválasztása) elemre.   

## <a name="next-steps"></a>Következő lépések
- Az SQL Database hozzáféréseinek és felügyeletének áttekintéséről az [SQL Database-hozzáférés és -felügyelet](sql-database-control-access.md) részben olvashat.
- Az SQL Database bejelentkezéseinek, felhasználóinak és adatbázis-szerepköreinek áttekintését a [Bejelentkezések, felhasználók és adatbázis-szerepkörök](sql-database-manage-logins.md) részben találja.
- További információ az adatbázis résztvevőivel kapcsolatban: [Résztvevők](https://msdn.microsoft.com/library/ms181127.aspx).
- További információ az adatbázis-szerepkörökkel kapcsolatban: [Adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx).
- További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](sql-database-firewall-configure.md).




<!--HONumber=Jan17_HO2-->


