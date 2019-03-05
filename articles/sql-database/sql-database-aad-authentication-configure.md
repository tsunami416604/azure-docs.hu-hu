---
title: Azure Active Directory-hitelesítés – az SQL konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az SQL Database felügyelt példány és az SQL Data warehouse-bA az Azure Active Directory-hitelesítés – az Azure AD konfigurálása után.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/04/2019
ms.openlocfilehash: e4ccb9be5d13ea72086fbaae2ffb2ec63ad55786
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340320"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Konfigurálhatja és kezelheti az Azure Active Directory-hitelesítés az SQL

Ez a cikk bemutatja, hogyan hozhat létre és töltse fel az Azure ad-ben, és majd az Azure AD használata az Azure-ral [SQL Database](sql-database-technical-overview.md), [felügyelt példány](sql-database-managed-instance.md), és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Áttekintéséhez lásd: [Azure Active Directory-hitelesítéssel](sql-database-aad-authentication.md).

> [!NOTE]
> Ez a cikk az Azure SQL-kiszolgálóhoz, és az SQL Database és az SQL Data Warehouse-adatbázisok az Azure SQL-kiszolgálón létrehozott vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.
> [!IMPORTANT]  
> Egy Azure virtuális gépeken futó SQL Serverhez való csatlakozáshoz nem támogatott az Azure Active Directory-fiók használatával. Használja helyette a tartomány Active Directory-fiókot.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-and-populate-an-azure-ad"></a>Létrehozása és feltöltése az Azure ad-ben

Hozzon létre egy Azure ad-ben, és a felhasználók és csoportok való feltöltéséhez. Az Azure AD lehetnek a kezdeti Azure ad-ben felügyelt tartományhoz. Az Azure AD lehetnek egy helyszíni Active Directory tartományi szolgáltatások, amelyek össze van vonva az Azure AD-vel is.

További információk a következő témakörökben találhatók: [Helyszíni identitások integrálása az Azure Active Directoryval](../active-directory/hybrid/whatis-hybrid-identity.md), [Saját tartománynév hozzáadása az Azure AD-hez](../active-directory/active-directory-domains-add-azure-portal.md), [A Microsoft Azure mostantól támogatja a Windows Server Active Directoryval való összevonást](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Az Azure AD-címtár felügyelete](../active-directory/fundamentals/active-directory-administer.md), [Az Azure AD kezelése Windows PowerShell használatával](/powershell/azure/overview?view=azureadps-2.0) és [Hibrid identitás – szükséges portok és protokollok](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Hozzárendelése vagy Azure-előfizetés hozzáadása az Azure Active Directoryhoz

1. Az Azure Active Directory Azure-előfizetés társítása azáltal, hogy a könyvtár az adatbázist üzemeltető Azure-előfizetéshez tartozó megbízható könyvtár. További információkért lásd: [kapcsolódnak az Azure AD-Azure-előfizetések](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. A címtárváltóval az Azure Portalon ahhoz az előfizetéshez társított tartományt váltani.

   **További információ:** Minden Azure-előfizetés bizalmi kapcsolattal rendelkezik egy Azure AD-példányhoz. Ez azt jelenti, hogy megbízik ebben a címtárban a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy címtárban bízhat meg. Ez az előfizetés és a címtár közötti bizalmi kapcsolat nem olyan, mint ami az előfizetés és az Azure összes többi erőforrása (webhelyek, adatbázisok stb.) között áll fenn, amelyek inkább az előfizetések gyermekerőforrásainak számítanak. Ha egy előfizetés lejár, akkor az előfizetéssel társított ilyen egyéb erőforrások hozzáférése is lejár. De a címtár az Azure-ban elérhető marad, és más előfizetéseket társíthat ezzel a címtárral, és folytathatja a címtár felhasználóinak kezelését. Erőforrásokkal kapcsolatos további információkért lásd: [az Azure-beli erőforrás-hozzáférésének megismerése](../active-directory/active-directory-b2b-admin-add-users.md). További tudnivalók ezzel kapcsolatban lásd: megbízható további [társításával vagy Azure-előfizetés hozzáadása az Azure Active Directoryhoz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Hozzon létre egy Azure SQL Serverhez készült Azure AD-rendszergazda

Minden Azure SQL-kiszolgáló (amely egy SQL Database vagy az SQL Data Warehouse) elindul egy egyetlen kiszolgálói rendszergazdai fiókkal, amely a rendszergazda a teljes Azure SQL-kiszolgáló. Egy másik SQL Server rendszergazdájának kell létrehozni, amely egy Azure AD-fiókot. Ezt az egyszerű felhasználónevet egy tartalmazottadatbázis-felhasználó a master adatbázisban jön létre. Rendszergazdák, a kiszolgáló-rendszergazdai fiókok tagjai a **db_owner** minden felhasználói szerepkörben adatbázisából, és adja meg az összes felhasználói adatbázishoz a **dbo** felhasználói. A kiszolgáló rendszergazdai fiókokkal kapcsolatos további információkért lásd: [adatbázisok és bejelentkezések Azure SQL Database-ben kezelésével](sql-database-manage-logins.md).

Ha az Azure Active Directoryval végzett földrajzi replikációval, mind az elsődleges és a másodlagos kiszolgálók az Azure Active Directory-rendszergazda kell konfigurálni. A kiszolgáló nem rendelkezik Azure Active Directory-rendszergazda, ha az Azure Active Directory-bejelentkezések és felhasználók megkapják a "sikertelen csatlakozás" kiszolgálóhiba.

> [!NOTE]
> A felhasználók, amelyek nem egy Azure AD-fiókot (beleértve az Azure SQL server-rendszergazdai fiók) alapuló, az Azure AD-alapú felhasználók nem hozható létre, mert nem rendelkeznek engedéllyel az Azure AD-vel javasolt adatbázis felhasználóinak.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Az Azure Active Directory-rendszergazda, a felügyelt példány üzembe helyezése

> [!IMPORTANT]
> Csak kövesse az alábbi lépéseket, ha egy felügyelt példányt szeretne kiépíteni. Ez a művelet csak hajtható végre globális/vállalati rendszergazda által az Azure ad-ben. Következő lépések bemutatják, a folyamat az engedélyek könyvtárban különböző jogosultságokkal rendelkező felhasználók számára.

A felügyelt példány sikeresen elvégezhető a feladatok, például a felhasználók biztonsági csoport tagsága keresztül hitelesítést és az új felhasználók létrehozása az Azure AD olvasási engedélyre. Ennek működéséhez szeretne engedélyt biztosíthat a felügyelt példány, olvassa el az Azure ad-ben. Kétféleképpen teheti meg: a portál és PowerShell. A következő lépéseket a két módszert.

1. Az Azure Portalon, a jobb felső sarokban válassza ki a kapcsolat a legördülő lista lehetővé aktív könyvtárak listáját.
2. Válassza ki a megfelelő Active Directory, az alapértelmezett Azure ad-ben.

   Ebben a lépésben összekapcsolja az előfizetéshez társított Active Directory és a felügyelt példány gondoskodik róla, hogy, hogy ugyanahhoz az előfizetéshez is szolgál az Azure ad-ben és a felügyelt példányhoz.
3. Navigáljon a felügyelt példány, és adja meg, hogy az Azure AD-integrációhoz használni kívánt.

   ![aad-ben](./media/sql-database-aad-authentication/aad.png)

4. A szalagcím felett az Active Directory-rendszergazda lapon válassza ki, és engedélyezi az aktuális felhasználónak. Ha van jelentkezve globális/vállalati rendszergazdaként az Azure ad-ben, ezt megteheti az Azure Portalon vagy a PowerShell használata az alábbi parancsfájlt.

    ![biztosítson az engedélyeket – portál](./media/sql-database-aad-authentication/grant-permissions.png)

    ```PowerShell
    # Gives Azure Active Directory read permission to a Service Principal representing the Managed Instance.
    # Can be executed only by a "Company Administrator" or "Global Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null)
    {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1))
    {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null)
    {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else
    {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Miután a művelet sikeresen befejeződött, a következő értesítés jelenik meg a jobb felső sarokban található:

    ![sikeres](./media/sql-database-aad-authentication/success.png)

6. Most már kiválaszthatja a felügyelt példány az Azure AD-rendszergazda. Ehhez az Active Directory felügyeleti oldalon válassza ki **rendszergazda beállítása** parancsot.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Az aad-ben rendszergazdai lapon keresse meg a felhasználót a rendszergazdai felhasználót vagy csoportot, majd válassza ki és **kiválasztása**.

   Az Active Directory-rendszergazda lap megjeleníti az összes tag és Active Directory-csoportokat. Felhasználók vagy csoportok szürkén jelennek meg, nem választható, mert azokat az Azure AD-rendszergazdák nem támogatottak. A támogatott rendszergazdák listájának megtekintéséhez [az Azure AD-funkciók és korlátozások](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Szerepköralapú hozzáférés-vezérlés (RBAC) csak azokra az Azure Portalon, és nem vonatkoznak az SQL Server.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. Az Active Directory-rendszergazda lap tetején válassza **mentése**.

    ![mentés](./media/sql-database-aad-authentication/save.png)

    A változó a rendszergazda a folyamat eltarthat néhány percig. Az új rendszergazda ezután az Active Directory felügyeleti mezőjében jelenik meg.

Hozhat létre az Azure AD kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) megkezdése után üzembe helyezés az Azure AD-rendszergazdát a felügyelt példány esetében (**nyilvános előzetes verzióban**) az a <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> szintaxist. További információkért lásd: [felügyelt példány áttekintése](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Későbbi távolítsa el a rendszergazda az Active Directory-rendszergazda lap tetején válassza **rendszergazda eltávolítása**, majd válassza ki **mentése**.

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Az Azure Active Directory-rendszergazda, az Azure SQL Database-kiszolgáló üzembe helyezése

> [!IMPORTANT]
> Csak kövesse az alábbi lépéseket, ha egy Azure SQL Database-kiszolgáló és a Data Warehouse üzembe helyezése.

A következő két eljárás bemutatják, hogyan helyezhet üzembe egy Azure Active Directory-rendszergazda az Azure SQL Serverhez az Azure Portalon, és a PowerShell használatával.

### <a name="azure-portal"></a>Azure Portal

1. Az a [az Azure portal](https://portal.azure.com/), a jobb felső sarokban válassza a legördülő lista lehetővé aktív címtárak listájának létesített kapcsolatot. Válassza ki a megfelelő Active Directory, az alapértelmezett Azure ad-ben. Ebben a lépésben az Azure SQL server gondoskodik róla, hogy, hogy ugyanahhoz az előfizetéshez is szolgál az előfizetéshez tartozó Active Directory hivatkozásokat tartalmaz az Azure AD és az SQL Server. (Az Azure SQL-kiszolgáló is lehet futtató, Azure SQL Database vagy Azure SQL Data Warehouse.) ![ad kiválasztása][8]

2. Válassza a bal oldali fejléc **minden szolgáltatás**, a szűrő be a következőt a **az SQL server**. Válassza ki **Sql Server-kiszolgálók**.

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > Ezen az oldalon, mielőtt választja **SQL Server-kiszolgálók**, kiválaszthatja a **csillag** a név melletti *kedvenc* a kategóriát, és adja hozzá **SQL Server-kiszolgálók**, a bal oldali navigációs sávon.

3. A **SQL Server** lapon jelölje be **Active Directory-rendszergazda**.
4. Az a **Active Directory-rendszergazda** lapon jelölje be **rendszergazda beállítása**.  ![az active directory kiválasztása](./media/sql-database-aad-authentication/select-active-directory.png)  

5. Az a **rendszergazda hozzáadása** lapon, keresse meg a felhasználó, válassza ki a felhasználó vagy csoport rendszergazdája lesz, és válassza **kiválasztása**. (Az Active Directory-rendszergazda lap megjeleníti az összes tag és Active Directory-csoportokat. Felhasználók vagy csoportok szürkén jelennek meg, nem választható, mert ez nem támogatott az Azure AD-rendszergazdaként. (A támogatott rendszergazdák listájának megtekintéséhez a **az Azure AD-funkciók és korlátozások** szakaszában [használata az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse-hitelesítéshez](sql-database-aad-authentication.md).) Szerepköralapú hozzáférés-vezérlés (RBAC) csak azokra a portálon, és az SQL Server nem propagálja.
    ![Válassza ki a rendszergazda](./media/sql-database-aad-authentication/select-admin.png)  

6. Felső részén a **Active Directory-rendszergazda** lapon jelölje be **mentése**.
    ![rendszergazdai mentése](./media/sql-database-aad-authentication/save-admin.png)

A változó a rendszergazda a folyamat eltarthat néhány percig. Az új rendszergazda megjelenik a **Active Directory-rendszergazda** mezőbe.

   > [!NOTE]
   > Az Azure AD-rendszergazda beállításakor az új felügyeleti name (felhasználó vagy csoport) nem már megtalálható a virtuális master adatbázishoz az SQL Server hitelesítési felhasználóként. Ha van ilyen, az Azure AD felügyeleti telepítés sikertelen lesz; visszaállítása a létrehozása, és amely azt jelzi, hogy az ilyen rendszergazdai (név) már létezik. Például egy SQL Server authentication felhasználóhoz nem része az Azure ad-ben, mivel minden annak érdekében, hogy csatlakozzon a kiszolgálóhoz az Azure AD-hitelesítés használatával sikertelen lesz.

Később eltávolítja a rendszergazda, felső részén a **Active Directory-rendszergazda** lapon jelölje be **rendszergazda eltávolítása**, majd válassza ki **mentése**.

### <a name="powershell"></a>PowerShell

PowerShell-parancsmagok futtatásához szüksége lesz az Azure PowerShell telepítenie és futtatnia. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview). Üzembe helyez egy Azure AD-rendszergazda, hajtsa végre a következő Azure PowerShell-parancsokat:

- Connect-AzAccount
- Select-AzSubscription

Parancsmagok üzembe helyezése és kezelése az Azure AD-rendszergazda segítségével:

| A parancsmag neve | Leírás |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Az Azure Active Directory-rendszergazda, az Azure SQL server- vagy Azure SQL Data Warehouse kiépítése. (Kell lennie az aktuális előfizetésben.) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Eltávolítja az Azure Active Directory-rendszergazda, az Azure SQL server- vagy Azure SQL Data warehouse-bA. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Az Azure SQL-kiszolgáló vagy az Azure SQL Data Warehouse jelenleg konfigurált Azure Active Directory-rendszergazda adatait adja vissza. |

További információ az egyes, az alábbi parancsok például a PowerShell-parancs-get-help használatával ``get-help Set-AzSqlServerActiveDirectoryAdministrator``.

Az alábbi parancsfájl egy Azure AD felügyeleti csoport neve rendelkezések **DBA_Group** (objektumazonosító: `40b79501-b343-44ed-9ce7-da4c8cc7353f`) esetében a **demo_server** nevű erőforráscsoportot a kiszolgáló **csoport – 23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

A **DisplayName** bemeneti paramétert fogad el, vagy az Azure ad-ben megjelenített neve, vagy az egyszerű felhasználónév. Ha például ``DisplayName="John Smith"`` és ``DisplayName="johns@contoso.com"``. Az Azure AD-csoportokat az Azure AD a megjelenítendő név használata támogatott.

> [!NOTE]
> Az Azure PowerShell-paranccsal ```Set-AzSqlServerActiveDirectoryAdministrator``` nem akadályozzák meg kiépítése az Azure AD-rendszergazdái a felhasználók nem támogatott. Egy nem támogatott felhasználó bővítheti, de nem tud kapcsolódni egy adatbázist.

Az alábbi példa használja az opcionális **ObjectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Az Azure AD **ObjectID** kötelező megadni, ha a **DisplayName** , nem egyedi. Beolvasni a **ObjectID** és **DisplayName** értékek, használja a klasszikus Azure portálon az Active Directory szakaszában, és a egy felhasználó vagy csoport tulajdonságainak megtekintéséhez.

Az alábbi példában az aktuális kapcsolatos információkat ad vissza az Azure SQL Serverhez készült Azure AD-rendszergazda:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

A következő példa eltávolítja az Azure AD-rendszergazda:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Azure Active Directory-rendszergazda a REST API-k használatával is kiépíthetők. További információkért lásd: [műveletek az Azure SQL adatbázis-műveletek az Azure SQL Database és Service Management REST API-referencia](https://docs.microsoft.com/rest/api/sql/)

### <a name="cli"></a>parancssori felület  

Azure AD-rendszergazdát a következő CLI-parancsok meghívásával is kiépíthetők:
| Parancs | Leírás |
| --- | --- |
|[az sql server-ad-rendszergazda létrehozása](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Az Azure Active Directory-rendszergazda, az Azure SQL server- vagy Azure SQL Data Warehouse kiépítése. (Kell lennie az aktuális előfizetésben.) |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Eltávolítja az Azure Active Directory-rendszergazda, az Azure SQL server- vagy Azure SQL Data warehouse-bA. |
|[az sql server ad-rendszergazdák listájának](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Az Azure SQL-kiszolgáló vagy az Azure SQL Data Warehouse jelenleg konfigurált Azure Active Directory-rendszergazda adatait adja vissza. |
|[az sql server ad-rendszergazda frissítése](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Frissíti egy Azure SQL-kiszolgáló vagy az Azure SQL Data Warehouse számára az Active Directory-rendszergazda. |

CLI-parancsokkal kapcsolatos további információkért lásd: [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server).  

## <a name="configure-your-client-computers"></a>Az ügyfélszámítógépek konfigurálása

Az összes ügyfél gépen, amelyen az alkalmazások vagy felhasználók csatlakozhat Azure SQL Database vagy Azure SQL Data Warehouse használatával az Azure AD-identitások, a következő szoftvereket kell telepítenie:

- .NET-keretrendszer 4.6-os vagy újabb verzióját az [ https://msdn.microsoft.com/library/5a4x27ek.aspx ](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Az Azure Active Directory hitelesítési tár SQL Serverhez (**ADALSQL. DLL**) több nyelven is elérhető (x86 és amd64) a letöltőközpontján [Microsoft Active Directory Authentication Library for Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

Ezek a követelmények szerint teljesítheti:

- Telepítése vagy [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) vagy [SQL Server Data Tools for Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) megfelel-e a .NET-keretrendszer 4.6 követelménynek.
- Ssms-t telepíti a x86 verziója **ADALSQL. DLL**.
- Az SSDT AMD64-es verzióját telepíti **ADALSQL. DLL**.
- A legújabb Visual Studio [Visual Studio letölti](https://www.visualstudio.com/downloads/download-visual-studio-vs) megfelel-e a .NET-keretrendszer 4.6 követelménynek, de nem telepíti a szükséges AMD64-es verzióját **ADALSQL. DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Hozzon létre tartalmazottadatbázis-felhasználók az Azure AD-identitások leképezett adatbázis

>[!IMPORTANT]
>Felügyelt példány mostantól támogatja az Azure AD-kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) (**nyilvános előzetes verzióban**), amely lehetővé teszi a bejelentkezések létrehozása az Azure AD felhasználók, csoportok és alkalmazások. Az Azure AD kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) lehetővé teszi a anélkül, hogy létrehozni egy tartalmazottadatbázis-felhasználó, az adatbázis-felhasználók a felügyelt példányokra való hitelesítéséhez. További információkért lásd: [felügyelt példány áttekintése](sql-database-managed-instance.md#azure-active-directory-integration). A szintaxis az Azure AD-kiszolgáló rendszerbiztonsági tagok (Bejelentkezések) létrehozásával, tekintse meg <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

Az Azure Active Directory-hitelesítés szükséges, tartalmazottadatbázis-felhasználókat létrehozni az adatbázis-felhasználók. Egy Azure AD identity alapuló tartalmazottadatbázis-felhasználó egy adatbázis-felhasználót, amely nem rendelkezik bejelentkezési adatokat a master adatbázisban, és amely leképezi a az adatbázishoz társított Azure AD-címtárat az identitást. Az Azure AD identity lehet egyedi felhasználói fiók vagy csoport. Tartalmazottadatbázis-felhasználókkal kapcsolatos további információkért lásd: [tartalmazott adatbázis-felhasználók – így az adatbázis hordozható](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Adatbázis-felhasználók (a rendszergazdák) kivételével nem hozhatók létre az Azure portal használatával. RBAC-szerepkörök nem vonatkoznak az SQL Server, SQL Database vagy az SQL Data warehouse-bA. Azure RBAC-szerepkörök kezeléséhez az Azure-erőforrások használatban vannak, és adatbázis-engedélyek nem vonatkoznak. Ha például a **SQL Server Közreműködője** szerepkör nem biztosít hozzáférést az SQL Database vagy az SQL Data warehouse-ba való csatlakozáshoz. A hozzáférési engedélyt közvetlenül az adatbázist a Transact-SQL-utasítások használatával.
> [!WARNING]
> Különleges karaktereket, például kettőspont `:` vagy és szimbólumot `&` való használata nem támogatott a T-SQL-CREATE LOGIN és a CREATE USER utasítást felhasználóneveket belefoglalva.

Hozzon létre egy Azure AD-alapú tartalmazott adatbázis-felhasználó (nem a kiszolgáló rendszergazdája, amely az adatbázis tulajdonosa), csatlakozzon az adatbázishoz egy Azure ad-ben identitással rendelkező felhasználóként legalább a **bármely felhasználó ALTER** engedéllyel. Ezután használja a következő Transact-SQL szintaxist:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* lehet egy Azure AD-felhasználó egyszerű felhasználónevét vagy egy Azure AD-csoport megjelenítendő nevét.

**Példák:** Hozzon létre egy tartalmazott adatbázisban felhasználó képviselő Azure AD összevont, vagy felügyelt tartományi felhasználó:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Hozzon létre egy tartalmazottadatbázis-felhasználó, az Azure AD jelölő vagy összevont tartományi csoport, adja meg a biztonsági csoport megjelenítendő nevét:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Az Azure AD-jogkivonat használatával csatlakozó alkalmazást jelölő tartalmazottadatbázis-felhasználó létrehozása:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!TIP]
> Az Azure Active Directoryból az Azure Active Directory, az Azure-előfizetéséhez társított eltérő közvetlenül a felhasználó nem hozható létre. Azonban más aktív könyvtárak, amelyek a társított Active Directory (más néven a külső felhasználók) az importált felhasználók tagjai lehet hozzáadni egy Active Directory-csoportot az Active Directory-bérlőben. Egy adatbázis-felhasználót, hogy az AD-csoport létrehozásával a felhasználók a külső Active Directoryból hozzáférhet az SQL Database.

További információ található adatbázis-felhasználók az Azure Active Directory-identitások alapján című témakörben talál [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Az Azure Active Directory-rendszergazda az Azure SQL server eltávolítása megakadályozza, hogy Azure AD-hitelesítés felhasználók kapcsolódni a kiszolgálóhoz. Ha szükséges, használhatatlan Azure AD-felhasználók manuálisan az SQL-adatbázis-rendszergazda által törölhetők.
> [!NOTE]
> Ha megjelenik egy **kapcsolat időkorlátja lejárt**, szükség lehet beállítani a `TransparentNetworkIPResolution` paraméter false értékre a kapcsolati karakterlánc. További információkért lásd: [kapcsolat időtúllépési problémát a .NET-keretrendszer 4.6.1-es - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Adatbázis-felhasználó létrehozásakor, hogy a felhasználó megkapja a **CONNECT** engedély tagjaként csatlakozhat az adatbázishoz, és a **nyilvános** szerepkör. Kezdetben az elérhetővé válik a felhasználó csak engedélyek-e bármely adott engedélyek a **nyilvános** szerepkör, illetve bármely engedélyeken minden olyan Azure AD-csoportokat, hogy azok a tagja. Miután üzembe helyezi az Azure AD-alapú tartalmazott adatbázis-felhasználó, megadhatja az engedélyeket a felhasználó további, ugyanúgy, engedélyezi a felhasználók bármilyen más típusú. Általában az adatbázis-szerepkörök, engedélyek, és felhasználók hozzáadása szerepkörökhöz. További információkért lásd: [Database Engine engedély alapjai](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Speciális SQL adatbázis-szerepkörökkel kapcsolatos további információkért lásd: [adatbázisok és bejelentkezések Azure SQL Database-ben kezelésével](sql-database-manage-logins.md).
Összevont tartományi felhasználói fiókot, amelyet importáltak a felügyelt tartomány külső felhasználóként, a felügyelt tartomány identitást kell használnia.

> [!NOTE]
> Az Azure AD-felhasználók lesznek megjelölve az adatbázis metaadatai típusú E (EXTERNAL_USER) és a csoportok típusú X (EXTERNAL_GROUPS). További információkért lásd: [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Csatlakozás a felhasználói adatbázis sem az adattárházra ssms-ben vagy az SSDT használatával  

Győződjön meg róla, az Azure AD-rendszergazda megfelelően beállíthassa, csatlakoznia kell a **fő** adatbázis-rendszergazdai fiókkal az Azure ad-ben.
Üzembe helyez egy Azure AD-alapú tartalmazott adatbázis-felhasználó (nem a kiszolgáló rendszergazdája, amely az adatbázis tulajdonosa), csatlakozzon az adatbázishoz az Azure AD identitás, amely hozzáféréssel rendelkezik az adatbázishoz.

> [!IMPORTANT]
> Az Azure Active Directory-hitelesítés támogatása érhető el a [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) és [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) a Visual Studio 2015. SSMS megjelenésével 2016 augusztusától az Active Directory univerzális hitelesítéssel, amely lehetővé teszi a rendszergazdák számára, hogy a többtényezős hitelesítést használ a telefonhívás, szöveges üzenetet, intelligens kártyák PIN-kód vagy mobilalkalmazásbeli értesítés támogatását is magában foglalja.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Az Azure AD-identitás segítségével SSMS és az SSDT-

A következő eljárások bemutatják, hogyan csatlakozhat egy SQL-adatbázis egy SQL Server Management Studio vagy SQL Server adatbázis-eszközök használata az Azure AD-identitással.

### <a name="active-directory-integrated-authentication"></a>Active Directory integrált hitelesítést

Ezt a módszert akkor használja, ha a Windows összevont tartományok az Azure Active Directory hitelesítő adatokkal van bejelentkezve.

1. Indítsa el a Management Studio vagy a Data-eszközök és a **kapcsolódás a kiszolgálóhoz** (vagy **kapcsolódás az adatbázismotorhoz**) párbeszédpanel a **hitelesítési** jelölje ki  **Active Directory – integrált**. Jelszó nélküli van szükség, vagy lehet megadni, mert a kapcsolat megjelenik a meglévő hitelesítő adatait.

    ![Válassza ki az AD integrált hitelesítés][11]
2. Válassza ki a **beállítások** gombra, majd a a **kapcsolat tulajdonságai** lap a **csatlakozhat az adatbázishoz** írja be a nevét, a felhasználói adatbázis, amelyhez csatlakozni kíván. (A **AD tartomány felhasználónév vagy a bérlői azonosító**"beállítás csak a támogatott **univerzális MFA-kapcsolattal rendelkező** lehetőségeket, ellenkező esetben azt szürkén jelenik meg.)  

    ![Válassza ki az adatbázis neve][13]

## <a name="active-directory-password-authentication"></a>Active Directorybeli jelszavas hitelesítés

Ezt a módszert használja, amikor egy Azure AD-szolgáltatásnevet, az Azure AD-vel csatlakoztatása a felügyelt tartomány. Is használhatja, például amikor távolról dolgozó a tartományhoz való hozzáférés nélkül összevont fiókok esetében.

Ezt a módszert használja a hitelesítéshez, az SQL DB/DW natív Azure AD-vel vagy összevont felhasználók Azure ad-ben. Egy natív felhasználói explicit módon az Azure AD-ben létrehozott-használ felhasználónevét és jelszavát, amíg egy összevont felhasználó egy Windows felhasználói tartományok össze van vonva az Azure ad-ben. Az utóbbi módszerhez (a felhasználó & jelszó használatával) használható, amikor egy felhasználó azt szeretné, a windows hitelesítő adatok használatára, de a helyi számítógép nem csatlakozik tartományhoz, azzal a tartománnyal (például egy távoli elérés). Ebben az esetben egy Windows felhasználói adhatja meg, a tartományi fiókot és jelszót, és az SQL DB/DW összevont hitelesítő adatokkal hitelesítheti.

1. Indítsa el a Management Studio vagy a Data-eszközök és a **kapcsolódás a kiszolgálóhoz** (vagy **kapcsolódás az adatbázismotorhoz**) párbeszédpanel a **hitelesítési** jelölje ki  **Az Active Directory - jelszó**.
2. Az a **felhasználónév** a következő formátumban írja be az Azure Active Directory felhasználónevet **username@domain.com**. Felhasználó nevének kell lennie az Azure Active Directory-fiók, vagy egy fiókot a tartomány összevonása az Azure Active Directoryval.
3. Az a **jelszó** mezőbe írja be a felhasználó jelszavát az Azure Active Directory-fiók vagy összevont tartományi fiókot.

    ![Válassza ki az AD jelszó-hitelesítés][12]
4. Válassza ki a **beállítások** gombra, majd a a **kapcsolat tulajdonságai** lap a **csatlakozhat az adatbázishoz** írja be a nevét, a felhasználói adatbázis, amelyhez csatlakozni kíván. (Lásd az előző parancsban a kép).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Egy ügyfélalkalmazás csatlakozni egy Azure AD identity használatával

A következő eljárások bemutatják, hogyan csatlakozhat egy SQL-adatbázis egy Azure AD-identitással a ügyfélalkalmazás.

### <a name="active-directory-integrated-authentication"></a>Active Directory integrált hitelesítést

Integrált Windows-hitelesítés használatára, a tartomány Active Directory kell összevont, az Azure Active Directoryval. Tartományhoz csatlakoztatott gépen a felhasználó tartományi hitelesítő adatokkal csatlakozik az adatbázishoz az ügyfélalkalmazás (vagy egy szolgáltatás) kell futnia.

Adatbázishoz való kapcsolódáshoz a beépített hitelesítéssel és a egy Azure AD identity, az adatbázis-kapcsolati karakterláncot a hitelesítési kulcsszót kell állítani az Active Directory integrált. Az alábbi C# kódminta forrásoszlopokat használ.

```C#
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A kapcsolati karakterlánc kulcsszó ``Integrated Security=True`` csatlakozik az Azure SQL Database esetében nem támogatott. Ha egy ODBC-kapcsolat, távolítsa el a szóközöket, és állítsa be a "ActiveDirectoryIntegrated" hitelesítési kell.

### <a name="active-directory-password-authentication"></a>Active Directorybeli jelszavas hitelesítés

Adatbázishoz való kapcsolódáshoz a beépített hitelesítéssel és a egy Azure AD identity, a hitelesítés kulcsszót kell állítani az Active Directory-jelszó. A kapcsolati karakterláncot a felhasználói azonosító/UID és a jelszó/PWD kulcsszavakat és értékeket kell tartalmaznia. Az alábbi C# kódminta forrásoszlopokat használ.

```C#
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

További információ az Azure AD-hitelesítési módszerek elérhető bemutató kódminták segítségével [GitHub bemutató az Azure AD hitelesítési](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Az Azure AD-token

Ez a hitelesítési módszer lehetővé teszi, hogy a középső rétegű szolgáltatásokhoz való csatlakozáshoz az Azure SQL Database vagy Azure SQL Data Warehouse egy token beszerzése az Azure Active Directory (AAD). Lehetővé teszi a kifinomult forgatókönyvek, beleértve a tanúsítványalapú hitelesítést. Az Azure AD alkalmazástoken-hitelesítésének használata négy alapvető lépéseket kell elvégeznie:

1. Regisztrálja az alkalmazást az Azure Active Directory és az ügyfél-azonosító beszerzése a kódhoz.
2. Hozzon létre egy adatbázis-felhasználót az alkalmazás jelző. (Korábban a 6. lépés befejeződött.)
3. Hozzon létre egy tanúsítványt az ügyfél-számítógépen fut a az alkalmazás.
4. Vegye fel a tanúsítványt az alkalmazáshoz kulcsként.

Mintául szolgáló kapcsolati karakterlánc:

```c#
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

További információkért lásd: [SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). A tanúsítvány hozzáadásával kapcsolatos további információkért lásd: [Ismerkedés az Azure Active Directory ügyféltanúsítvány-alapú hitelesítés](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Az alábbi utasításokat, csatlakozzon az Sqlcmd-t, amely elérhető a 13.1 verzióját használja a [letöltőközpontból](https://go.microsoft.com/fwlink/?LinkID=825643).

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>További lépések

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
