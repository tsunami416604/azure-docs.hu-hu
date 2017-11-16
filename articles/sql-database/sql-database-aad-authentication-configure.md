---
title: "Azure Active Directory-hitelesítés - SQL konfigurálása |} Microsoft Docs"
description: "Megtudhatja, hogyan Csatlakozás SQL Database és az SQL Data Warehouse Azure Active Directory-hitelesítés használatával."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 7e2508a1-347e-4f15-b060-d46602c5ce7e
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 07/10/2017
ms.author: rickbyh
ms.openlocfilehash: f0c9578217beff22b4a322b363c7499943311d88
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-or-sql-data-warehouse"></a>Konfigurálhatja és kezelheti az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse

Ez a cikk bemutatja, létrehozása és feltöltése az Azure AD és az Azure AD az Azure SQL Database és az SQL Data Warehouse majd használni. Megtudhatja, [Azure Active Directory-hitelesítéssel](sql-database-aad-authentication.md).

>  [!NOTE]  
>  Egy Azure virtuális Gépen futó SQL Server nem támogatott a Kapcsolódás egy Azure Active Directory-fiókkal. A tartomány Active Directory-fiókot használni.

## <a name="create-and-populate-an-azure-ad"></a>Létrehozása és feltöltése az Azure AD
Hozzon létre egy Azure AD és a felhasználókat és csoportokat. Az Azure AD is lehet a kezdeti az Azure AD által felügyelt tartományokhoz. Az Azure AD egy a helyszíni Active Directory tartományi szolgáltatások, amelyek össze van vonva az Azure AD-val is lehet.

További információk a következő témakörökben találhatók: [Helyszíni identitások integrálása az Azure Active Directoryval](../active-directory/active-directory-aadconnect.md), [Saját tartománynév hozzáadása az Azure AD-hez](../active-directory/active-directory-domains-add-azure-portal.md), [A Microsoft Azure mostantól támogatja a Windows Server Active Directoryval való összevonást](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Az Azure AD-címtár felügyelete](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Az Azure AD kezelése Windows PowerShell használatával](/powershell/azure/overview?view=azureadps-2.0) és [Hibrid identitás – szükséges portok és protokollok](../active-directory/active-directory-aadconnect-ports.md).

## <a name="optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>Választható lehetőség: Hozzárendelése vagy az active directory jelenleg az Azure-előfizetéshez társított módosítása
Az adatbázis társítja a szervezet Azure AD-címtárral, hajtsa végre a könyvtár egy megbízható könyvtárat az Azure-előfizetés az adatbázis tárolásához. További információkért lásd: [How Azure subscriptions are associated with Azure AD?](https://msdn.microsoft.com/library/azure/dn629581.aspx) (Hogyan kapcsolódnak az Azure-előfizetések az Azure AD-hoz?).

**További információ:** minden Azure-előfizetéshez az Azure AD-példányban megbízhatósági kapcsolatban áll. Ez azt jelenti, hogy megbízik ebben a címtárban a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy címtárban bízhat meg. Láthatja, melyik címtárban bízik meg az előfizetése a **beállítások** lapot [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Ez az előfizetés és a címtár közötti bizalmi kapcsolat nem olyan, mint ami az előfizetés és az Azure összes többi erőforrása (webhelyek, adatbázisok stb.) között áll fenn, amelyek inkább az előfizetések gyermekerőforrásainak számítanak. Ha egy előfizetés lejár, akkor az előfizetéssel társított ilyen egyéb erőforrások hozzáférése is lejár. De a címtár az Azure-ban elérhető marad, és más előfizetéseket társíthat ezzel a címtárral, és folytathatja a címtár felhasználóinak kezelését. Erőforrások kapcsolatos további információkért lásd: [az az Azure-erőforrások hozzáférésének megismerése](https://msdn.microsoft.com/library/azure/dn584083.aspx).

A következő eljárások bemutatják az adott előfizetéshez tartozó könyvtárat szeretne váltani.
1. Csatlakozás a [klasszikus Azure portál](https://manage.windowsazure.com/) Azure-előfizetési rendszergazda segítségével.
2. Válassza ki a bal oldali szalagcím **beállítások**.
3. Az előfizetések a Beállítások képernyő jelenik meg. Ha nem jelenik meg a kívánt előfizetés, kattintson a **előfizetések** a lap tetején legördülő listán a **szűrő által DIRECTORY** mezőbe, és válassza ki azt a címtárat, amely tartalmazza az előfizetések, és kattintson **ALKALMAZ**.
   
    ![Válassza ki az előfizetést][4]
4. Az a **beállítások** területen kattintson az előfizetéshez, majd **könyvtár szerkesztése** az oldal alján.
   
    ![ad-beállítások-portálon][5]
5. Az a **könyvtár szerkesztése** mezőben, válassza ki az Azure Active Directory, az SQL Server vagy az SQL Data Warehouse társított, majd kattintson a nyílra a Tovább gombra.
   
    ![Edit directory jelölje ki][6]
6. Az a **megerősítése** directory leképezési párbeszédpanelen ellenőrizze, hogy "**összes társrendszergazdák törlődni fog.**"
   
    ![Edit directory megerősítése][7]
7. Kattintson a töltse be újra a portálon.

   > [!NOTE]
   > Ha a könyvtár hozzáférési társrendszergazdák, az Azure Active Directory-felhasználók és csoportok, módosításához és könyvtár biztonsági erőforrás-felhasználókat törlődnek, és már nem rendelkeznek hozzáféréssel az előfizetés vagy az erőforrások. Csak, szolgáltatás-rendszergazdaként, konfigurálja a hozzáférést a rendszerbiztonsági tagoknak az új könyvtár alapján. Ez a változás jelentős mennyiségű időt propagálódik az összes erőforrást is igénybe vehet. Is módosítása a könyvtárban, az Azure AD-rendszergazda módosította SQL Database és az SQL Data Warehouse és minden meglévő Azure Active Directory-felhasználók adatbázis-hozzáférés letiltása. Az Azure AD admin kell lennie (alább leírtak) alaphelyzetbe állítása és az új Azure AD-felhasználók kell létrehozni.
   >  

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Az Azure AD-rendszergazda Azure SQL-kiszolgáló létrehozása
Minden Azure SQL-kiszolgáló (amely egy SQL Database vagy az SQL Data Warehouse) kezdődik-e egy önálló kiszolgáló-rendszergazdai fiók, amely a teljes Azure SQL-kiszolgáló rendszergazdájához. A második SQL kiszolgáló rendszergazdája kell létrehozni, amely egy Azure AD-fiókot. Ez egyszerű tartalmazott adatbázis-felhasználó a master adatbázisban jön létre. A rendszergazdáknak, a kiszolgáló rendszergazdai fiók sem tagja a **db_owner** minden felhasználói szerepkör adatbázisából, és adja meg az egyes felhasználói az adatbázisban, mint a **dbo** felhasználó. A kiszolgáló rendszergazdai fiókokkal kapcsolatos további információkért lásd: [kezelése adatbázisok és bejelentkezések az Azure SQL Database](sql-database-manage-logins.md).

Az Azure Active Directoryval a georeplikációt, amikor az Azure Active Directory rendszergazdája úgy kell beállítani, az elsődleges és másodlagos kiszolgálók. Ha a kiszolgáló nem rendelkezik egy Azure Active Directory-rendszergazda, a Azure Active Directory bejelentkezési és a felhasználók fogadni a "nem lehet csatlakozni" kiszolgálóhiba.

> [!NOTE]
> A felhasználók, amelyek nem az Azure AD-fiókot (beleértve az Azure SQL server rendszergazdai fiók) alapuló, az Azure AD-alapú felhasználók nem hozható létre, mert nem rendelkeznek engedéllyel az Azure AD-val javasolt adatbázis felhasználóinak.
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server"></a>Az Azure SQL server Azure Active Directory-rendszergazda kiépítése

Az alábbi két módszer megjelenítése kiépítése az Azure portálon, és a PowerShell használatával az Azure SQL server Azure Active Directory-rendszergazda.

### <a name="azure-portal"></a>Azure Portal
1. Az a [Azure-portálon](https://portal.azure.com/), jobb felső sarokban, kattintson a legördülő listán lehetséges aktív könyvtárainak listáját a kapcsolatot. Válassza ki a megfelelő Active Directory az Azure AD alapértelmezés szerint. Ez a lépés az előfizetés társítását, és az Active Directory kapcsolatokhoz meggyőződött arról, hogy, hogy ugyanahhoz az előfizetéshez nem használja mindkét Azure SQL server az Azure AD és az SQL Server. (Az Azure SQL-kiszolgáló is szolgáltató vagy az Azure SQL Database vagy az Azure SQL Data Warehouse.)   
    ![Válasszon ad][8]   
    
2. Válassza ki a bal oldali szalagcím **SQL Server-kiszolgálók**, jelölje be a **SQL server**, majd a a **SQL Server** panelen kattintson **Active Directory-rendszergazda**.   
3. Az a **Active Directory-rendszergazda** panelen kattintson a **-rendszergazda beállítása**.   
    ![az active directory kiválasztása](./media/sql-database-aad-authentication/select-active-directory.png)  
    
4. Az a **adja hozzá a rendszergazda** panelen keresse meg a felhasználót, válassza ki a felhasználó vagy csoport rendszergazdának lennie, és kattintson a **válasszon**. (Az Active Directory felügyeleti panelt jeleníti meg minden tagok és Active Directory-csoport. Felhasználók vagy csoportok használhatók, nem állítható be, mert nem támogatják az Azure AD rendszergazdai szerepkörrel. (Lásd a támogatott rendszergazdái a **az Azure AD-funkciókat és korlátozások** szakasza [Azure Active Directory hitelesítés használata a hitelesítés és az SQL Database vagy az SQL Data Warehouse](sql-database-aad-authentication.md).) Szerepköralapú hozzáférés-vezérlést (RBAC) csak a portál vonatkozik, és az SQL Server nem propagálja.   
    ![Válassza ki a rendszergazda](./media/sql-database-aad-authentication/select-admin.png)  
    
5. Felső részén a **Active Directory-rendszergazda** panelen kattintson a **mentése**.   
    ![Mentse admin](./media/sql-database-aad-authentication/save-admin.png)   

A rendszergazda folyamatát több percig is eltarthat. Ezt követően megjelenik az új rendszergazda a **Active Directory-rendszergazda** mezőbe.

   > [!NOTE]
   > Az Azure AD admin beállításakor az új felügyeleti neve (felhasználó vagy csoport) nem már megtalálható a virtuális master adatbázis SQL Server hitelesítési felhasználóként. Ha jelen van, az Azure AD rendszergazdai telepítés sikertelen lesz; visszaállítása a létrehozása és, amely jelzi, hogy az ilyen rendszergazda (név) már létezik. Mivel az ilyen egy SQL Server-hitelesítés felhasználó nem része az Azure AD, bármely annak érdekében, hogy csatlakozzon a kiszolgálóhoz, az Azure AD-hitelesítéssel sikertelen lesz.
   > 


Később eltávolítja a rendszergazda tetején a **Active Directory-rendszergazda** panelen kattintson a **távolítsa el a felügyeleti**, és kattintson a **mentése**.

### <a name="powershell"></a>PowerShell
PowerShell-parancsmagok futtatásához szükség van telepítve és fut az Azure PowerShell. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

Az Azure AD rendszergazdai kiépíthetők, hajtsa végre a következő Azure PowerShell-parancsokat:

* Add-AzureRmAccount
* SELECT-AzureRmSubscription

Parancsmagok segítségével telepíteni és kezelni a Azure AD rendszergazdai:

| Parancsmag neve | Leírás |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/set-azurermsqlserveractivedirectoryadministrator) |Azure Active Directory-rendszergazda Azure SQL server vagy az Azure SQL Data Warehouse kiépítése. (Kell lennie az aktuális előfizetésben.) |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/remove-azurermsqlserveractivedirectoryadministrator) |Eltávolítja az Azure Active Directory-rendszergazda Azure SQL server vagy az Azure SQL Data warehouse-bA. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator) |Egy Azure Active Directory-rendszergazda az Azure SQL server vagy az Azure SQL Data Warehouse jelenleg konfigurált információt ad vissza. |

További részleteket az egyes parancsok, például a get-help PowerShell parancs segítségével ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Az alábbi parancsfájl egy Azure AD felügyeleti csoport neve rendelkezések **DBA_Group** (objektumazonosító: `40b79501-b343-44ed-9ce7-da4c8cc7353f`) számára a **demo_server** server nevű erőforráscsoportban **csoport – 23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

A **DisplayName** bemeneti paramétert fogad el, vagy az Azure Active Directory megjelenítendő nevét, vagy az egyszerű felhasználónév. Például ``DisplayName="John Smith"`` és ``DisplayName="johns@contoso.com"``. Az Azure AD-csoportok csak az Azure AD megjelenített név támogatott.

> [!NOTE]
> Az Azure PowerShell-paranccsal ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` nem akadályozza meg a felhasználók nem támogatott az Azure AD-rendszergazdái kiosztásakor. Egy nem támogatott felhasználói telepíthető, de nem tud kapcsolódni egy adatbázis. 
> 

Az alábbi példa használja az opcionális **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Az Azure AD **ObjectID** megadása kötelező, ha a **DisplayName** eleme nem egyedi. Beolvasni a **ObjectID** és **DisplayName** értékeket, az Active Directory a szakaszban a klasszikus Azure portálon, és egy felhasználó vagy csoport tulajdonságainak megtekintése.
> 

Az alábbi példában az aktuális információt ad vissza az Azure SQL server az Azure AD admin:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Az alábbi példa az Azure AD-rendszergazdaként eltávolítja:

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Egy Azure Active Directory-rendszergazda a REST API-k használatával is kiépíthetők. További információkért lásd: [az Azure SQL-adatbázisok Azure SQL-adatbázisok műveletek műveletek és a Service Management REST API-referencia](https://msdn.microsoft.com/library/azure/dn505719.aspx)

### <a name="cli"></a>parancssori felület  
Az Azure AD-rendszergazda a következő parancsok parancssori felület meghívásával is kiépíthetők:
| Parancs | Leírás |
| --- | --- |
|[az sql server ad-rendszergazda létrehozása](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) |Azure Active Directory-rendszergazda Azure SQL server vagy az Azure SQL Data Warehouse kiépítése. (Kell lennie az aktuális előfizetésben.) |
|[az sql server ad-rendszergazda törlése](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) |Eltávolítja az Azure Active Directory-rendszergazda Azure SQL server vagy az Azure SQL Data warehouse-bA. |
|[az sql server ad-rendszergazda listája](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) |Egy Azure Active Directory-rendszergazda az Azure SQL server vagy az Azure SQL Data Warehouse jelenleg konfigurált információt ad vissza. |
|[az sql server ad-rendszergazda frissítése](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) |Az Active Directory-rendszergazda az Azure SQL server vagy az Azure SQL Data Warehouse frissíti. |

Parancssori felület parancsait kapcsolatos további információkért lásd: [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server).  


## <a name="configure-your-client-computers"></a>Állítsa be az ügyfélszámítógépen
Az összes ügyfélszámítógépre, amelyről alkalmazás vagy felhasználó kapcsolódni az Azure SQL Database vagy az Azure SQL Data Warehouse az Azure AD identitásokkal, telepítenie kell a következő szoftvereket:

* .NET-keretrendszer 4.6-os vagy újabb a [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
* Az Azure Active Directory hitelesítési tár SQL Serverhez (**ADALSQL. DLL**) több nyelven is elérhető (x86 és amd64) a letöltőközpontján [Microsoft Active Directory Authentication Library Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

E követelményeknek szerint:

* Telepítése vagy [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) vagy [SQL Server Data Tools a Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) megfelel-e a .NET-keretrendszer 4.6 követelménynek.
* SSMS telepíti a x86 verziójának **ADALSQL. DLL**.
* Az SSDT AMD64-es verzióját telepíti **ADALSQL. DLL**.
* A legújabb Visual Studio [Visual Studio letöltések](https://www.visualstudio.com/downloads/download-visual-studio-vs) megfelel-e a .NET-keretrendszer 4.6 követelménynek, de nem telepíti a szükséges AMD64-es verziójának **ADALSQL. DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Tartalmazott adatbázis-felhasználók létrehozása az Azure AD-identitások leképezve az adatbázisban

Az Azure Active Directory-hitelesítés használatakor a felhasználóknak adatbázis tartalmazott adatbázis-felhasználók hozhatók létre. Az Azure AD identity alapján tartalmazott adatbázis-felhasználó az adatbázis-felhasználó nem rendelkezik a master adatbázisban, és amely az Azure AD-címtár társított az adatbázis egyik identitása van leképezve. Az Azure AD identity vagy az egyéni felhasználói fiókot, vagy a csoport lehet. További információ a tartalmazott adatbázis-felhasználók: [tartalmazott adatbázis-felhasználók – hogy a saját adatbázis hordozható](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Adatbázis-felhasználók (kivéve a rendszergazdák) portal nem hozható létre. Az RBAC-szerepkörök nem vonatkoznak az SQL Server, SQL-adatbázis vagy az SQL Data Warehouse. Az Azure RBAC-szerepkörök Azure-erőforrások kezelésére használt, és nem vonatkoznak az adatbázis-engedélyek. Például a **SQL Server közreműködői** szerepkör hozzáférést az SQL Database vagy az SQL Data Warehouse való csatlakozáshoz. A hozzáférési engedélyt kell adni, közvetlenül a Transact-SQL-utasítások segítségével adatbázisban.
>

Hozzon létre egy Azure AD-alapú tartalmazott adatbázis-felhasználó (nem a kiszolgáló rendszergazdája, amely az adatbázis tulajdonosa), csatlakozás az Azure AD identity adatbázishoz rendelkező felhasználóként legalább a **bármely felhasználó ALTER** engedéllyel. Kövesse a következő Transact-SQL-szintaxis:

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* lehet a felhasználó egyszerű felhasználóneve, az Azure AD-felhasználó vagy egy Azure AD-csoport nevét.

**Példák:** tartalmazott adatbázist hozhat létre az Azure AD közti felhasználói összevont vagy kezelt tartományi felhasználói:
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Hozzon létre egy Azure AD közti vagy tartományi csoport összevont tartalmazott adatbázis-felhasználó, adja meg a biztonsági csoport megjelenítendő nevét:
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Egy alkalmazás, amely összeköti az Azure AD token használatával képviselő tartalmazott adatbázis-felhasználó létrehozása:

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  A felhasználó egy Azure Active Directory, nem az Azure Active Directory, az Azure-előfizetéshez társított nem tud közvetlenül létrehozni. Azonban más aktív könyvtárak, amelyek a társított Active Directoryban (más néven a külső felhasználók) importált felhasználók tagjai is hozzáadhatók az Active Directory-csoporthoz az Active Directory-bérlőnél. Hozzon létre egy tartalmazott adatbázis-felhasználót, hogy AD-csoport, a a felhasználók a külső Active Directoryból férhet hozzá az SQL-adatbázis.   

Tartalmazott létrehozásával kapcsolatos további információért adatbázis-felhasználók Azure Active Directory identitások alapján című [felhasználó létrehozása (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Az Azure Active Directory-rendszergazda az Azure SQL server eltávolítása megakadályozza, hogy az Azure AD hitelesítési munkamenetből a kiszolgálóhoz való kapcsolódás. Ha szükséges, nem használható az Azure Active Directory-felhasználók manuálisan SQL adatbázis-rendszergazda általi törölhetők.   

>  [!NOTE]
>  Ha megjelenik egy **kapcsolat időkorlátja lejárt**, szükség lehet beállítani a `TransparentNetworkIPResolution` paraméter false értékre a kapcsolati karakterlánc. További információkért lásd: [kapcsolat időtúllépési problémát .NET-keretrendszer 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).   

   
Amikor létrehoz egy adatbázis-felhasználó, az, hogy a felhasználó megkapja a **CONNECT** engedéllyel és csatlakozni tud-e, hogy az adatbázis tagjaként a **nyilvános** szerepkör. Kezdetben elérhetővé válik a felhasználó csak engedélyek a számára megadott engedélyeket a **nyilvános** szerepkör, illetve bármely engedélyeket bármely Azure AD-csoportok tagjai legyenek. Miután kiépítése az Azure AD-alapú tartalmazott adatbázis-felhasználó, a felhasználó további engedélyek megadásához, ugyanúgy, engedélyezi a felhasználók bármilyen más típusú. Általában megadni az engedélyeket az adatbázis-szerepkörök, és felhasználók hozzáadása szerepkörökhöz. További információkért lásd: [Database Engine engedély alapjai](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). További információ a speciális SQL adatbázis-szerepkörök: [kezelése adatbázisok és bejelentkezések az Azure SQL Database](sql-database-manage-logins.md).
Külső felhasználóként, egy felügyelt tartományba importált összevont tartományi felhasználói fiókot kell használnia a felügyelt tartomány identitása.

> [!NOTE]
> Az Azure Active Directory-felhasználók jelöli az adatbázis metaadatai típusú E (EXTERNAL_USER) és a csoportok X (EXTERNAL_GROUPS) típus. További információkért lásd: [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx). 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Csatlakozás a felhasználói adatbázis vagy a data warehouse szolgáltatáshoz az SSMS vagy az SSDT használatával  
Győződjön meg róla, az Azure AD-rendszergazda helyesen van beállítva, csatlakozzon a **fő** adatbázis, az Azure AD rendszergazdai fiók használatával.
Kiépítéséhez az Azure AD-alapú tartalmazott adatbázis-felhasználó (nem a kiszolgáló rendszergazdája, amely az adatbázis tulajdonosa), az adatbázis egy Azure AD-identitás, amely hozzáféréssel rendelkezik az adatbázishoz csatlakozni.

> [!IMPORTANT]
> Az Azure Active Directory-hitelesítés támogatásának érhető [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) és [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) a Visual Studio 2015-öt. SSMS megjelenésével 2016 augusztusától is támogatja az Active Directory univerzális hitelesítést, amely lehetővé teszi a rendszergazdák számára, hogy a többtényezős hitelesítést használ a telefonhívás, szöveges üzenetet, intelligens kártyák PIN-kód és az értesítést a mobilalkalmazásban.
 
## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Az Azure AD identity használatával csatlakozni a szolgáltatáshoz az SSMS vagy az SSDT segítségével  

A következő eljárások megmutatják, hogyan egy SQL Server Management Studio vagy SQL Server adatbázis-eszközt használ az Azure AD-identitás SQL-adatbázishoz való kapcsolódáshoz.

### <a name="active-directory-integrated-authentication"></a>Active Directory integrált hitelesítést

Ezt a módszert akkor használja, ha a Windows összevont tartományhoz az Azure Active Directory hitelesítő adatokkal van bejelentkezve.

1. Indítsa el a Management Studio vagy az eszközök és a a **kapcsolódás a kiszolgálóhoz** (vagy **kapcsolódás az adatbázismotorhoz**) párbeszédpanelen a **hitelesítési** mezőben válassza **Active Directory - integrált**. Nem kell jelszót van szükség, vagy megadható, mert a meglévő hitelesítő adatok számára jelenik meg a kapcsolathoz.   

    ![AD integrált hitelesítés kiválasztása][11]
2. Kattintson a **beállítások** gombra, majd a a **kapcsolat tulajdonságai** lap a **Kapcsolódás adatbázis** mezőjébe írja be a nevet a felhasználói adatbázis, amelyhez csatlakozni kíván. (A **AD tartomány neve vagy a bérlői azonosító**"beállítás csak a támogatott **univerzális MFA kapcsolattal rendelkező** beállítások, ellenkező esetben azt szürkén jelenik meg.)  

    ![Válassza ki az adatbázis neve][13]

## <a name="active-directory-password-authentication"></a>Active Directory jelszavas hitelesítést

Ezt a módszert használja, ha összekapcsolása egy Azure AD egyszerű felhasználónév használata az Azure AD tartományi kezelése. Is használhatja az összevont fiók, például amikor működik-e távolról a tartományhoz való hozzáférés nélkül.

Ezt a módszert használja, ha Windows hitelesítő adatok használatával, amely nincs összevonva a Azure-tartomány van bejelentkezve, vagy ha az Azure AD-alapú hitelesítés az Azure AD használatával az eredeti vagy az ügyfél tartomány alapján.

1. Indítsa el a Management Studio vagy az eszközök és a a **kapcsolódás a kiszolgálóhoz** (vagy **kapcsolódás az adatbázismotorhoz**) párbeszédpanelen a **hitelesítési** mezőben válassza **Active Directory - jelszó**.
2. Az a **felhasználónév** mezőbe írja be az Azure Active Directory felhasználó formátumban  **username@domain.com** . Ennek az az Azure Active Directory-fiók kell lennie, vagy az Azure Active Directory összevonni az olyan tartományi fiók.
3. Az a **jelszó** mezőben adja meg a felhasználó jelszavát az Azure Active Directory-fiókkal vagy tartományi fiók összevont.

    ![Válassza ki az AD-jelszó-hitelesítés][12]
4. Kattintson a **beállítások** gombra, majd a a **kapcsolat tulajdonságai** lap a **Kapcsolódás adatbázis** mezőjébe írja be a nevet a felhasználói adatbázis, amelyhez csatlakozni kíván. (Lásd az előző beállítás a képet.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Az Azure AD identity a a ügyfélalkalmazás csatlakozáshoz

A következő eljárások bemutatják a ügyfélalkalmazás egy Azure AD identity SQL Database adatbázishoz való kapcsolódáshoz.

###  <a name="active-directory-integrated-authentication"></a>Active Directory integrált hitelesítést

Integrált Windows-hitelesítés használatára, a tartomány Active Directory kell összevont, az Azure Active Directoryban. Az adatbázishoz való kapcsolódás az ügyfélalkalmazást (vagy egy szolgáltatás) a felhasználó tartományi hitelesítő adatok a tartományhoz csatlakoztatott számítógépen kell futnia.

Adatbázishoz való kapcsolódáshoz a beépített hitelesítéssel és az Azure AD identity, adatbázis-kapcsolati karakterláncot a hitelesítési kulcsszót Active Directory integrált értékre kell állítani. A következő C# kódminta ADO .NET használja.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A kapcsolati karakterlánc kulcsszó ``Integrated Security=True`` nem támogatott az Azure SQL-adatbázishoz szeretne csatlakozni. Ha egy ODBC-kapcsolat, akkor távolítsa el a szóközöket, és állítsa be a "ActiveDirectoryIntegrated" hitelesítési.

### <a name="active-directory-password-authentication"></a>Active Directory jelszavas hitelesítést

Adatbázishoz való kapcsolódáshoz a beépített hitelesítéssel és az Azure AD identity, a hitelesítési kulcsszó Active Directory-jelszó értékre kell állítani. A kapcsolati karakterláncnak tartalmaznia kell a felhasználói azonosító vagy UID és a jelszó/PWD kulcsszavak és az értékeket. A következő C# kódminta ADO .NET használja.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

További tudnivalók az Azure AD hitelesítési módszerek használatával a rendelkezésre álló bemutató mintakódok [GitHub bemutató az Azure AD hitelesítési](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Az Azure AD-jogkivonat
Ez a hitelesítési módszer lehetővé teszi, hogy a középső rétegbeli szolgáltatásokhoz való csatlakozáshoz az Azure SQL Database vagy az Azure SQL Data Warehouse jogkivonat beszerzése az Azure Active Directory (AAD). Ez lehetővé teszi a kifinomult forgatókönyvek például a tanúsítvány alapú hitelesítés. Hajtsa végre az Azure AD-tokent használó hitelesítés használatára négy alapvető lépéseket:

1. Az alkalmazás regisztrálása az Azure Active Directoryban, és az ügyfél-azonosító lekérése a kódot. 
2. Hozzon létre egy adatbázis-felhasználó, az alkalmazás jelző. (A korábbi 6. lépés befejeződött.)
3. Hozzon létre egy tanúsítványt az ügyfél számítógépen fut a az alkalmazás.
4. A tanúsítvány hozzáadása az alkalmazáshoz kulcsként.

A minta kapcsolati karakterlánc:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

További információkért lásd: [SQL Server biztonsági Blog](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). A tanúsítvány hozzáadásával kapcsolatos további információkért lásd: [Ismerkedés az Azure Active Directory-alapú hitelesítés](../active-directory/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

A következő utasításokat csatlakozni az Sqlcmd használatával, amely elérhető a 13.1 verzióját használja a [letöltőközpontból](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Következő lépések
- Az SQL Database hozzáféréseinek és felügyeletének áttekintéséről az [SQL Database-hozzáférés és -felügyelet](sql-database-control-access.md) részben olvashat.
- Az SQL Database bejelentkezéseinek, felhasználóinak és adatbázis-szerepköreinek áttekintését a [Bejelentkezések, felhasználók és adatbázis-szerepkörök](sql-database-manage-logins.md) részben találja.
- További információ az adatbázis résztvevőivel kapcsolatban: [Résztvevők](https://msdn.microsoft.com/library/ms181127.aspx).
- További információ az adatbázis-szerepkörökkel kapcsolatban: [Adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx).
- További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png

