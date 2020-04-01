---
title: Az Azure Active Directory-fiókon alapuló hitelesítés konfigurálása
description: Ismerje meg, hogyan csatlakozhat az SQL Database-hez, a felügyelt példányhoz és az Azure Synapse Analytics-hez az Azure Active Directory-hitelesítés használatával – az Azure AD konfigurálása után.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 0e244ea185011bbb7d9f0facad399bb9b577bbc2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419893"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Az Azure Active Directory-hitelesítés konfigurálása és kezelése SQL-el

Ez a cikk bemutatja, hogyan hozhat létre és népethet fel Azure AD-t, majd használhatja az Azure AD-t az Azure [SQL Database (SQL DB)](sql-database-technical-overview.md), [a felügyelt példány (MI)](sql-database-managed-instance.md)és [az Azure Synapse Analytics (korábban Azure SQL Data Warehouse)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)használatával. Az áttekintést az Azure Active Directory hitelesítése című [témakörben találja.](sql-database-aad-authentication.md)

> [!NOTE]
> Ez a cikk az Azure SQL-kiszolgálóra, valamint az SQL Database és az Azure Synapse szolgáltatásra is vonatkozik. Az egyszerűség kedvéért az SQL Database az SQL Database és az Azure Synapse hivatkozva használatos.

> [!IMPORTANT]  
> Az Azure Virtuális gépen futó SQL Server-kiszolgálóhoz való csatlakozás nem támogatott Egy Azure Active Directory-fiók használatával. Használjon helyette tartományi Active Directory-fiókot.

## <a name="azure-ad-authentication-methods"></a>Azure AD hitelesítési módszerek

Az Azure AD-hitelesítés a következő hitelesítési módszereket támogatja:

- Csak Azure AD felhőalapú identitások
- Az Azure AD hibrid identitások, amelyek támogatják:
  - Felhőalapú hitelesítés két lehetőséggel, valamint zökkenőmentes egyszeri bejelentkezés (SSO)
    - Azure AD jelszókivonat-hitelesítés
    - Azure AD áthaladási hitelesítés
  - Összevont hitelesítés

Az Azure AD hitelesítési módszereiről és a választható képpen kapcsolatos további információkért tekintse meg a következő cikket:
- [Válassza ki az Azure Active Directory hibrid identitáskezelési megoldásának megfelelő hitelesítési módszert](../active-directory/hybrid/choose-ad-authn.md)

Az Azure AD hibrid identitásairól, a beállításról és a szinkronizálásról az alábbi cikkekben talál további információt:

- Jelszókivonat-hitelesítés – [Jelszókivonat-szinkronizálás megvalósítása az Azure AD Connect szinkronizálásával](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Átadó hitelesítés – [Azure Active Directory átadó hitelesítése](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Összevont hitelesítés – [Active Directory összevonási szolgáltatások telepítése az Azure-ban](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) és az [Azure AD Connectben és összevonásban](../active-directory/hybrid/how-to-connect-fed-whatis.md)

A fenti hitelesítési módszerek támogatottak az SQL DB (egyetlen adatbázis- és adatbáziskészletek), a felügyelt példány és az Azure Synapse.

## <a name="create-and-populate-an-azure-ad"></a>Azure AD létrehozása és feltöltése

Hozzon létre egy Azure AD-t, és feltöltse azt a felhasználókkal és csoportokkal. Az Azure AD lehet a kezdeti Azure AD felügyelt tartomány. Az Azure AD is lehet egy helyszíni Active Directory tartományi szolgáltatások, amely az Azure AD-vel összevont.

További információk a következő témakörökben találhatók: [Helyszíni identitások integrálása az Azure Active Directoryval](../active-directory/hybrid/whatis-hybrid-identity.md), [Saját tartománynév hozzáadása az Azure AD-hez](../active-directory/active-directory-domains-add-azure-portal.md), [A Microsoft Azure mostantól támogatja a Windows Server Active Directoryval való összevonást](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Az Azure AD-címtár felügyelete](../active-directory/fundamentals/active-directory-administer.md), [Az Azure AD kezelése Windows PowerShell használatával](/powershell/azure/overview) és [Hibrid identitás – szükséges portok és protokollok](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz

1. Társítsa Az Azure-előfizetést az Azure Active Directoryhoz azáltal, hogy a címtár megbízható könyvtáraz adatbázist üzemeltető Azure-előfizetéshez. További információt a [Hogyan társítják az Azure-előfizetések az Azure AD-hez.](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

2. Az Azure Portalon a címtár-kapcsolóval váltson a tartományhoz társított előfizetésre.

   > [!IMPORTANT]
   > Minden Azure-előfizetés bizalmi kapcsolattal rendelkezik egy Azure AD-példányhoz. Ez azt jelenti, hogy megbízik ebben a címtárban a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy címtárban bízhat meg. Ez az előfizetés és a címtár közötti bizalmi kapcsolat nem olyan, mint ami az előfizetés és az Azure összes többi erőforrása (webhelyek, adatbázisok stb.) között áll fenn, amelyek inkább az előfizetések gyermekerőforrásainak számítanak. Ha egy előfizetés lejár, akkor az előfizetéssel társított ilyen egyéb erőforrások hozzáférése is lejár. De a címtár az Azure-ban elérhető marad, és más előfizetéseket társíthat ezzel a címtárral, és folytathatja a címtár felhasználóinak kezelését. Az erőforrásokkal kapcsolatos további információkért [olvassa el az Erőforrások elérésének ismertetése az Azure-ban című témakört.](../active-directory/active-directory-b2b-admin-add-users.md) Ha többet szeretne megtudni erről a megbízható kapcsolatról, olvassa [el Az Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz.](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Azure AD-rendszergazda létrehozása az Azure SQL Server hez

Minden Azure SQL-kiszolgáló (amely sql-adatbázist vagy Azure Synapse-t üzemeltet) egyetlen kiszolgálórendszergazdai fiókkal kezdődik, amely a teljes Azure SQL-kiszolgáló rendszergazdája. Létre kell hozni egy második SQL Server-rendszergazdát, amely egy Azure AD-fiók. Ez a biztonsági tag a fő adatbázisban tárolt adatbázis-felhasználóként jön létre. Rendszergazdaként a kiszolgálórendszergazdai fiókok minden felhasználói adatbázis ban a **db_owner** szerepkör tagjai, és minden felhasználói adatbázist **dbo-felhasználóként** beírnak. A kiszolgálói rendszergazdai fiókokról az [Adatbázisok és bejelentkezések kezelése](sql-database-manage-logins.md)az Azure SQL Database alkalmazásban című témakörben talál további információt.

Ha az Azure Active Directoryt georeplikációval használja, az Azure Active Directory rendszergazdáját mind az elsődleges, mind a másodlagos kiszolgálókhoz konfigurálni kell. Ha egy kiszolgáló nem rendelkezik Azure Active Directory-rendszergazdával, akkor az Azure Active Directory bejelentkezések és a felhasználók kapnak egy "Nem lehet csatlakozni" a kiszolgáló hiba.

> [!NOTE]
> Azok a felhasználók, akik nem Azure AD-fiókon alapulnak (beleértve az Azure SQL server rendszergazdai fiókját is), nem hozhatnak létre Azure AD-alapú felhasználókat, mert nem rendelkeznek engedéllyel a javasolt adatbázis-felhasználók érvényesítéséhez az Azure AD-vel.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Azure Active Directory-rendszergazda kiépítése a felügyelt példányhoz

> [!IMPORTANT]
> Csak kövesse ezeket a lépéseket, ha felügyelt példányt létesít. Ezt a műveletet csak globális/vállalati rendszergazda vagy az Azure AD kiemelt szerepkörrel rendelkező rendszergazdája hajthatja végre. Az alábbi lépések a címtárban különböző jogosultságokkal rendelkező felhasználók engedélyeinek megadását ismertetik.

> [!NOTE]
> Az Azure AD-rendszergazdák számára a Megfelelő hibaszolgáltatás előtt létrehozott, de továbbra is működik a ga utáni, nincs funkcionális változás a meglévő viselkedés. További információkért tekintse meg az [új Azure AD-rendszergazdai funkció a hibajelző szolgáltatás](#new-azure-ad-admin-functionality-for-mi) további részletekért.

A felügyelt példány nak engedélyeket kell olvasnia az Azure AD-hez a feladatok sikeres elvégzéséhez, például a felhasználók hitelesítéséhez a biztonsági csoporttagságon keresztül vagy új felhasználók létrehozása. Ahhoz, hogy ez működjön, engedélyeket kell adnia a felügyelt példány nak az Azure AD olvasásához. Ezt kétféleképpen teheti meg: a Portálról és a PowerShellből. A következő lépések mindkét módszer.

1. Az Azure Portalon a jobb felső sarokban válassza ki a kapcsolatot a lehetséges aktív könyvtárak listájának legördülő listájához.

2. Válassza ki a megfelelő Active Directoryt alapértelmezett Azure AD-ként.

   Ez a lépés az Active Directoryhoz társított előfizetést a felügyelt példányhoz kapcsolja, ügyelve arra, hogy ugyanazt az előfizetést használja az Azure AD és a Felügyelt példány.

3. Keresse meg a Felügyelt példányt, és válassza ki az Azure AD-integrációhoz használni kívánt példányt.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Jelölje ki a szalagcímet az Active Directory felügyeleti lapja tetején, és adjon engedélyt az aktuális felhasználónak. Ha az Azure AD globális/vállalati rendszergazdaként van bejelentkezve, megteheti az Azure Portalon vagy a PowerShell használatával az alábbi parancsfájllal.

    ![engedélyek megadása-portál](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

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
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. A művelet sikeres befejezése után a következő értesítés jelenik meg a jobb felső sarokban:

    ![sikeres](./media/sql-database-aad-authentication/success.png)

6. Most kiválaszthatja az Azure AD-rendszergazda a felügyelt példány. Az Active Directory rendszergazdai lapján válassza a **Rendszergazda beállítása** parancsot.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Az AAD rendszergazda lapján keressen meg egy felhasználót, jelölje ki a rendszergazdaként részt vevő felhasználót vagy csoportot, majd válassza **a Kijelölés lehetőséget.**

   Az Active Directory felügyeleti lapja az Active Directory összes tagját és csoportját megjeleníti. A szürkén kirendelt felhasználók vagy csoportok nem választhatók ki, mert nem támogatottak Az Azure AD-rendszergazdák számára. Tekintse meg a támogatott rendszergazdák listáját az [Azure AD-funkciók és korlátozások.](sql-database-aad-authentication.md#azure-ad-features-and-limitations) Szerepköralapú hozzáférés-vezérlés (RBAC) csak az Azure Portalon, és nem propagálva az SQL Server.

    ![Azure Active Directory-rendszergazda hozzáadása](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. Az Active Directory felügyeleti lapjának tetején válassza a **Mentés lehetőséget.**

    ![save](./media/sql-database-aad-authentication/save.png)

    A rendszergazda módosításának folyamata több percet is igénybe vehet. Ezután az új rendszergazda megjelenik az Active Directory felügyeleti mezőjében.

Miután kiépített egy Azure AD-rendszergazdát a felügyelt példányhoz, megkezdheti az Azure AD-kiszolgálói egyszerű (bejelentkezési) létrehozása a <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> szintaxissal. További információt a [Felügyelt példány okának áttekintése című témakörben talál.](sql-database-managed-instance.md#azure-active-directory-integration)

> [!TIP]
> A rendszergazda eltávolításához az Active Directory felügyeleti lapjának tetején válassza a **Rendszergazda eltávolítása**lehetőséget, majd a **Mentés**lehetőséget.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Új Azure AD-rendszergazdai funkció az mi-hez

Az alábbi táblázat összefoglalja a funkciókat a nyilvános előzetes verziójú Azure AD bejelentkezési rendszergazda a mi, szemben egy új funkció szállított GA for Azure AD bejelentkezések.

| Az Azure AD bejelentkezési rendszergazdája az MI számára a nyilvános előzetes verzió során | GA-funkciók az Azure AD-rendszergazdához az MI-hez |
| --- | ---|
| Hasonlóan viselkedik, mint az Azure AD-rendszergazda az SQL Database, amely lehetővé teszi az Azure AD-hitelesítést, de az Azure AD-rendszergazda nem hozhat létre Azure AD vagy SQL bejelentkezések a fő adatbázis mi. | Az Azure AD-rendszergazda rendszergazdai engedéllyel rendelkezik, és létrehozhat AAD és SQL bejelentkezéseket az MI fő adatbázisában. |
| Nincs jelen a sys.server_principals nézetben | Jelen van a sys.server_principals nézetben |
| Lehetővé teszi, hogy az egyes Azure AD-vendégfelhasználók azure-beli AD-rendszergazdaként állíthatók be a mi szolgáltatása. További információ: [Add Azure Active Directory B2B együttműködési felhasználók az Azure Portalon.](../active-directory/b2b/add-users-administrator.md) | Létre kell hozni egy Azure AD-csoportot a vendégfelhasználók tagként, hogy állítsa be ezt a csoportot, mint egy Azure AD-rendszergazda a mi. További információ: [Azure AD business to business support](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

A ga előtt létrehozott és a ga-t követően is működő mi meglévő Azure AD-rendszergazdák ajánlott eljárásaként állítsa alaphelyzetbe az Azure AD-rendszergazdát az Azure-portál "Admin eltávolítása" és "Rendszergazda beállítása" beállítás használatával ugyanahhoz az Azure AD-felhasználóhoz vagy -csoporthoz.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Ismert problémák az Azure AD bejelentkezési ga a mi

- Ha egy Azure AD bejelentkezési létezik a fő adatbázis mi, a T-SQL paranccsal `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`létrehozott, nem állítható be, mint egy Azure AD-rendszergazda a mi. Az Azure Portal, a PowerShell vagy a CLI-parancsok használatával az Azure AD-bejelentkezés létrehozásához hiba történik az Azure AD-rendszergazdaként történő bejelentkezés.
  - A bejelentkezést el kell dobni a `DROP LOGIN [myaadaccount]`fő adatbázisban a parancs használatával, mielőtt a fiók azure AD-rendszergazdaként létrehozható.
  - Állítsa be az Azure AD-rendszergazdai `DROP LOGIN` fiókot az Azure Portalon a siker után. 
  - Ha nem tudja beállítani az Azure AD-rendszergazdai fiókot, ellenőrizze a fő adatbázisa a felügyelt példány a bejelentkezéshez. Használja a következő parancsot:`SELECT * FROM sys.server_principals`
  - Az Azure AD-rendszergazda beállítása a mi automatikusan létrehoz egy bejelentkezési a fő adatbázisban ehhez a fiókhoz. Az Azure AD-rendszergazda eltávolítása automatikusan eldobja a bejelentkezést a fő adatbázisból.

- Egyéni Azure AD vendégfelhasználók nem támogatottak az Azure AD-rendszergazdák a mi. A vendégfelhasználóknak egy Azure AD-csoport része kell lenniük az Azure AD-rendszergazdaként való beállításhoz. Jelenleg az Azure Portal panel nem szürke ki vendégfelhasználók egy másik Azure AD, amely lehetővé teszi a felhasználók számára, hogy továbbra is a felügyeleti beállítás. A vendégfelhasználók Azure AD-rendszergazdaként való mentése a telepítés sikertelensedéséhez vezet.
  - Ha azt szeretné, hogy egy vendégfelhasználó egy Azure AD-rendszergazda a hibajelző, a vendégfelhasználó egy Azure AD-csoportba, és állítsa be ezt a csoportot, mint egy Azure AD-rendszergazda.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell sql felügyelt példányhoz

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell-parancsmagok futtatásához telepítenie és futtatnia kell az Azure PowerShellt. Részletes információk: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Az AzureRM-modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak. A kompatibilitásukról az [Új Azure PowerShell Az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakörben lehet további további további információkért.

Az Azure AD-rendszergazda kiépítéséhez hajtsa végre a következő Azure PowerShell-parancsokat:

- Connect-AzAccount
- Select-AzSubscription

Az Azure AD-rendszergazda kiépítéséhez és kezeléséhez használt parancsmagok az SQL felügyelt példányhoz:

| Parancsmag neve | Leírás |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Az Aktuális előfizetésben az SQL által felügyelt példányhoz egy Azure AD-rendszergazdát rendel. (Az aktuális előfizetésből kell származnia)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Eltávolítja az Azure AD-rendszergazda az SQL felügyelt példány az aktuális előfizetésben. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Információt ad vissza egy Azure AD-rendszergazda az SQL felügyelt példány az aktuális előfizetésben.|

A következő parancs információt kap egy Azure AD-rendszergazda egy felügyelt példány nevű ManagedInstance01, amely egy ResourceGroup01 nevű erőforráscsoport társított.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

A következő parancs egy Azure AD rendszergazdai csoportot rendel, amelynek neve DbA a ManagedInstance01 nevű felügyelt példányhoz. Ez a kiszolgáló a ResourceGroup01 erőforráscsoporthoz van társítva.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

A következő parancs eltávolítja az Azure AD-rendszergazdát a ResourceGroup01 erőforráscsoporthoz társított ManagedInstanceName01 nevű felügyelt példányhoz.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure AD-rendszergazdát is kiépíthet az SQL által felügyelt példányhoz a következő CLI-parancsok hívásával:

| Parancs | Leírás |
| --- | --- |
|[az sql mi ad-admin létrehozása](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Az Azure Active Directory rendszergazdája az SQL felügyelt példány. (Az aktuális előfizetésből kell származnia) |
|[az sql mi ad-admin törlés](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Eltávolítja az Azure Active Directory rendszergazdáját az SQL által felügyelt példányhoz. |
|[az sql mi ad-admin lista](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Információt ad vissza egy Azure Active Directory-rendszergazdáról, aki jelenleg SQL-felügyelt példányra van konfigurálva. |
|[az sql mi ad-admin frissítés](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Frissíti az Active Directory rendszergazdáját egy SQL által kezelt példányhoz. |

A CLI-parancsokról az [az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Azure Active Directory-rendszergazda kiépítése az Azure SQL Database-kiszolgálóhoz

> [!IMPORTANT]
> Csak kövesse ezeket a lépéseket, ha egy Azure SQL Database-kiszolgáló vagy az Azure Synapse Analytics kiépítése.

Az alábbi két eljárás bemutatja, hogyan építhet ki egy Azure Active Directory-rendszergazdát az Azure SQL-kiszolgálóhoz az Azure Portalon és a PowerShell használatával.

### <a name="azure-portal"></a>Azure Portal

1. Az [Azure Portalon](https://portal.azure.com/)a jobb felső sarokban válassza ki a kapcsolatot a lehetséges aktív könyvtárak listájának legördülő listájához. Válassza ki a megfelelő Active Directoryt alapértelmezett Azure AD-ként. Ez a lépés összekapcsolja az előfizetéshez társított Active Directoryt az Azure SQL-kiszolgálóval, és biztosítja, hogy ugyanazt az előfizetést használja az Azure AD és az SQL Server. (Az Azure SQL-kiszolgáló üzemeltetheti az Azure SQL Database vagy az Azure Synapse szolgáltatást.)

    ![választás-hirdetés][8]

2. Keresse meg és válassza az **SQL-kiszolgálót.**

    ![SQL-kiszolgálók keresése és kijelölése](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Ezen a lapon, mielőtt kiválasztja **az SQL-kiszolgálókat,** kiválaszthatja a kategória *kedvencének nevezett* név melletti **csillagot,** és **sql-kiszolgálókat** adhat hozzá a bal oldali navigációs sávhoz.

3. Az **SQL Server** lapon válassza az Active Directory **rendszergazdája**lehetőséget.

4. Az **Active Directory rendszergazda** lapján válassza a **Rendszergazda beállítása lehetőséget.**

    ![SQL-kiszolgálók beállítják az Active Directory rendszergazdáját](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. A **Rendszergazda hozzáadása** lapon keressen meg egy felhasználót, jelölje ki a rendszergazdaként részt vevő felhasználót vagy csoportot, és válassza a **Kijelölés lehetőséget.** (Az Active Directory felügyeleti lapja az Active Directory összes tagját és csoportját megjeleníti. A szürkén megjelenő felhasználók vagy csoportok nem választhatók ki, mert nem támogatottak Az Azure AD-rendszergazdák számára. (Tekintse meg a támogatott rendszergazdák listáját az [Azure Active Directory-hitelesítés használata az Azure Active Directory-hitelesítés használata az Azure-alapú Active Directory-hitelesítés](sql-database-aad-authentication.md)hez című **Azure AD-szolgáltatások és korlátozások** című szakaszban.) A szerepköralapú hozzáférés-vezérlés (RBAC) csak a portálra vonatkozik, és nem lesz propagálva az SQL Server kiszolgálóra.

    ![Az Azure Active Directory rendszergazdájának kiválasztása](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. Az **Active Directory felügyeleti** lapjának tetején válassza a **MENTÉS**lehetőséget.

    ![rendszergazda mentése](./media/sql-database-aad-authentication/save-admin.png)

A rendszergazda módosításának folyamata több percet is igénybe vehet. Ezután az új rendszergazda megjelenik az **Active Directory felügyeleti** mezőjében.

   > [!NOTE]
   > Az Azure AD-rendszergazda beállításakor az új rendszergazdai név (felhasználó vagy csoport) már nem lehet jelen a virtuális főadatbázisban, mint SQL Server hitelesítési felhasználó. Ha van, az Azure AD felügyeleti beállítása sikertelen lesz; visszaállítja a létrehozását, és jelzi, hogy ilyen rendszergazda (név) már létezik. Mivel egy ilyen SQL Server hitelesítési felhasználó nem része az Azure AD, minden erőfeszítést, hogy csatlakozzon a kiszolgálóhoz az Azure AD-hitelesítés sikertelen lesz.

A rendszergazda eltávolításához az Active **Directory felügyeleti** lapjának tetején válassza a **Rendszergazda eltávolítása**lehetőséget, majd a **Mentés**lehetőséget.

### <a name="powershell-for-azure-sql-database-and-azure-synapse"></a>PowerShell az Azure SQL Database és az Azure Synapse számára

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell-parancsmagok futtatásához telepítenie és futtatnia kell az Azure PowerShellt. Részletes információk: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása). Az Azure AD-rendszergazda kiépítéséhez hajtsa végre a következő Azure PowerShell-parancsokat:

- Connect-AzAccount
- Select-AzSubscription

Az Azure SQL Database és az Azure Synapse Azure AD-rendszergazdájának kiépítéséhez és kezeléséhez használt parancsmagok:

| Parancsmag neve | Leírás |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Az Azure Active Directory rendszergazdája az Azure SQL-kiszolgálóhoz vagy az Azure Synapse-hoz rendel. (Az aktuális előfizetésből kell származnia) |
| [Eltávolítás-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Eltávolítja az Azure Active Directory rendszergazdáját az Azure SQL-kiszolgálóhoz vagy az Azure Synapse-hoz. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Az Azure SQL-kiszolgálóhoz vagy az Azure Synapse-hez jelenleg konfigurált Azure Active Directory-rendszergazdával kapcsolatos információkat adja vissza. |

A PowerShell parancssúgójával további információkat kaphat az egyes parancsokról. Például: `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

A következő parancsfájl egy **DBA_Group** nevű Azure AD-rendszergazdai csoportot (objektumazonosító) `40b79501-b343-44ed-9ce7-da4c8cc7353f`ír a **demo_server** kiszolgálószámára egy **23-as csoport**nevű erőforráscsoportban:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

A **DisplayName** bemeneti paraméter elfogadja az Azure AD megjelenítendő nevét vagy az egyszerű felhasználónevet. Például, ``DisplayName="John Smith"`` ``DisplayName="johns@contoso.com"``és . Az Azure AD-csoportok csak az Azure AD megjelenítendő név támogatott.

> [!NOTE]
> Az Azure PowerShell parancs ```Set-AzSqlServerActiveDirectoryAdministrator``` nem akadályozza meg az Azure AD-rendszergazdák nem támogatott felhasználók számára történő kiépítését. Nem támogatott felhasználó kiépíthető, de nem tud csatlakozni az adatbázishoz.

A következő példa a választható **ObjectID azonosítót**használja:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Az Azure AD **ObjectID** szükséges, ha a **DisplayName** nem egyedi. Az **ObjectID** és a **DisplayName** értékek beolvasásához használja az Azure Classic Portal Active Directory szakaszát, és tekintse meg egy felhasználó vagy csoport tulajdonságait.

A következő példa az Azure SQL-kiszolgáló aktuális Azure AD-rendszergazdájával kapcsolatos információkat adja vissza:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

A következő példa eltávolítja az Azure AD-rendszergazdát:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure AD-rendszergazdát a következő CLI-parancsok hívásával építheti ki:

| Parancs | Leírás |
| --- | --- |
|[az sql server ad-admin létrehozása](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Az Azure Active Directory rendszergazdája az Azure SQL-kiszolgálóhoz vagy az Azure Synapse-hoz rendel. (Az aktuális előfizetésből kell származnia) |
|[az sql server ad-admin törlése](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Eltávolítja az Azure Active Directory rendszergazdáját az Azure SQL-kiszolgálóhoz vagy az Azure Synapse-hoz. |
|[az sql server ad-admin lista](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Az Azure SQL-kiszolgálóhoz vagy az Azure Synapse-hez jelenleg konfigurált Azure Active Directory-rendszergazdával kapcsolatos információkat adja vissza. |
|[az sql server ad-admin frissítés](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Frissíti az Active Directory rendszergazdáját egy Azure SQL-kiszolgálóhoz vagy az Azure Synapse-hoz. |

A CLI-parancsokról az [AZ SQL server](/cli/azure/sql/server)című témakörben talál további információt.

* * *

> [!NOTE]
> Azure Active Directory-rendszergazda is kiépíthető a REST API-k használatával. További információ: [Service Management REST API Reference and Operations for Azure SQL Database Operations for Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Az ügyfélszámítógépek konfigurálása

Minden ügyfélgépen, amelyről az alkalmazások vagy a felhasználók az Azure AD-identitások használatával csatlakoznak az Azure SQL Database-hez vagy az Azure Synapse-hoz, telepítenie kell a következő szoftvert:

- [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)A .
- Azure Active Directory hitelesítési könyvtár SQL Server (*ADAL. DLL).* Az alábbiakban az ADAL-t tartalmazó legújabb SSMS, ODBC és OLE DB illesztőprogram telepítéséhez szükséges letöltési hivatkozások *találhatók. DLL-könyvtár.*
    1. [SQL Server Management Stúdió](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [ODBC-illesztőprogram 17 az SQL Server kiszolgálóhoz](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [OLE DB Driver 18 az SQL Server kiszolgálóhoz](https://www.microsoft.com/download/details.aspx?id=56730)

Ezeket a követelményeket az alábbi konkretinálhatja:

- Az SQL Server [Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) vagy az [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) legújabb verziójának telepítése megfelel a .NET Framework 4.6 követelményének.
    - Az SSMS az ADAL x86-os verzióját *telepíti. DLL.*
    - Az SSDT az ADAL amd64 verzióját *telepíti. DLL.*
    - A Visual Studio downloads legújabb Visual [Studio-verziója](https://www.visualstudio.com/downloads/download-visual-studio-vs) megfelel a .NET Framework 4.6 követelménynek, de nem telepíti az ADAL szükséges amd64-es *verzióját. DLL.*

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Az Azure AD-identitásokhoz leképezett adatbázis-felhasználók létrehozása az adatbázisban

> [!IMPORTANT]
> A felügyelt példány mostantól támogatja az Azure AD-kiszolgálói egyszerű kiszolgálókat (bejelentkezések), amelyek lehetővé teszik, hogy bejelentkezéseket hozzon létre az Azure AD-felhasználóktól, csoportokból vagy alkalmazásokból. Az Azure AD-kiszolgálói egyszerű (bejelentkezési adatok) lehetővé teszi a felügyelt példány hitelesítését anélkül, hogy adatbázis-felhasználókat kell létrehozni, mint egy tartalmazott adatbázis-felhasználó. További információt a [Felügyelt példány – áttekintés című témakörben talál.](sql-database-managed-instance.md#azure-active-directory-integration) Az Azure AD-kiszolgálórendszeri rendszertagok (bejelentkezések) létrehozásának szintaxisát a LOGIN LÉTREHOZÁSA című témakörben <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">tetszetős sel.</a>

Az Azure Active Directory-hitelesítés hez adatbázis-felhasználókat kell létrehozni, mint a tartalmazott adatbázis-felhasználók. Egy azure-beli AD-identitáson alapuló, tartalmazott adatbázis-felhasználó, amely nem rendelkezik bejelentkezéssel a fő adatbázisban, és amely leképezi az identitás az Azure AD könyvtárban, amely az adatbázishoz van társítva. Az Azure AD-identitás lehet egyéni felhasználói fiók vagy csoport. A tartalmazott adatbázis-felhasználókról további információt a [Tartalmazott adatbázis-felhasználók – Az adatbázis hordozhatóvá tétele](https://msdn.microsoft.com/library/ff929188.aspx)című témakörben talál.

> [!NOTE]
> Adatbázis-felhasználók (a rendszergazdák kivételével) nem hozhatók létre az Azure Portal használatával. Az RBAC-szerepkörök nem lesznek propagálva az SQL Server, az SQL Database vagy az Azure Synapse rendszerbe. Az Azure RBAC szerepkörök az Azure Resources kezelésére szolgálnak, és nem vonatkoznak az adatbázis-engedélyekre. Például az **SQL Server Közreműködő** szerepkör nem ad hozzáférést az SQL-adatbázishoz vagy az Azure Synapse-hoz való csatlakozáshoz. A hozzáférési engedélyt közvetlenül az adatbázisban kell megadni transact-SQL utasításokkal.

> [!WARNING]
> A T-SQL CREATE LOGIN és CREATE USER utasításokban felhasználónévként megadott speciális karakterek, például kettőspont `:` vagy ampersand `&` nem támogatottak.

Hozzon létre egy Azure AD-alapú, tartalmazott adatbázis-felhasználó (kivéve a kiszolgáló rendszergazdája, amely az adatbázis tulajdonosa), csatlakozzon az adatbázishoz egy Azure AD-identitás, a felhasználó legalább az **ALTER ANY USER** engedéllyel. Ezután használja a következő Transact-SQL szintaxist:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* lehet egy Azure AD-felhasználó egyszerű felhasználóneve vagy egy Azure AD-csoport megjelenítendő neve.

**Példák:** Az Azure AD összevont vagy felügyelt tartományi felhasználót képviselő, tartalmazott adatbázis-felhasználó létrehozása:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Azure AD-t vagy összevont tartománycsoportot képviselő, tartalmazott adatbázis-felhasználó létrehozásához adja meg egy biztonsági csoport megjelenítendő nevét:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Egy Azure AD-jogkivonathasználatával csatlakozó alkalmazást képviselő, tartalmazott adatbázis-felhasználó létrehozása:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Ez a parancs megköveteli, hogy az SQL access Azure AD (a "külső szolgáltató") a bejelentkezett felhasználó nevében. Néha olyan körülmények merülnek fel, amelyek hatására az Azure AD visszaad egy kivételt az SQL-nek. Ezekben az esetekben a felhasználó a 33134-es SQL-hibát fogja látni, amelynek tartalmaznia kell az AAD-specifikus hibaüzenetet. Az idő nagy részében a hiba azt fogja mondani, hogy a hozzáférés megtagadva van, vagy hogy a felhasználónak be kell iratkoznia az MFA-ba az erőforrás eléréséhez, vagy hogy a belső alkalmazások közötti hozzáférést előzetes engedélyezéssel kell kezelni. Az első két esetben a problémát általában a feltételes hozzáférési házirendek okozzák, amelyek a felhasználó AAD-bérlőjében vannak beállítva: megakadályozzák, hogy a felhasználó hozzáférjen a külső szolgáltatóhoz. A "000000002-0000-0000-c0000-000000000000000000000000000" alkalmazáshoz való hozzáférés engedélyezésével a hitelesítésbiztosítási házirendek frissítése megoldja a problémát. Abban az esetben, ha a hiba azt mondja, hogy a belső alkalmazások közötti hozzáférést előzetes engedélyezéssel kell kezelni, a probléma azért van, mert a felhasználó egyszerű szolgáltatásként van bejelentkezve. A parancs nak sikeresnek kell lennie, ha azt egy felhasználó hajtja végre.

> [!TIP]
> Közvetlenül nem hozhat létre felhasználót az Azure-előfizetéshez társított Azure Active Directoryn kívüli Azure Active Directoryból. A társított Active Directoryban importált felhasználók (más néven külső felhasználók) azonban a bérlői Active Directory-csoporthoz hozzáadhatók más Active Directory-felhasználók tagjai. Ha az adott AD-csoporthoz létrehoz egy tartalmazott adatbázis-felhasználót, a külső Active Directory felhasználói hozzáférhetnek az SQL-adatbázishoz.

A tartalmazott adatbázis-felhasználók Azure Active Directory identitások alapján való létrehozásáról a [CREATE USER (Transact-SQL) című](https://msdn.microsoft.com/library/ms173463.aspx)témakörben talál további információt.

> [!NOTE]
> Az Azure Active Directory-rendszergazda eltávolítása az Azure SQL-kiszolgálóhoz megakadályozza, hogy bármely Azure AD-hitelesítési felhasználó csatlakozzon a kiszolgálóhoz. Szükség esetén a használhatatlan Azure AD-felhasználók manuálisan eldobhatók az SQL Database rendszergazdája által.

> [!NOTE]
> Ha lejárt **kapcsolati időtúllépést**kap, előfordulhat, hogy a `TransparentNetworkIPResolution` kapcsolati karakterlánc paraméterét hamisra kell állítania. További információ: [Kapcsolat időtúllépési probléma a .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Adatbázis-felhasználó létrehozásakor a felhasználó megkapja a **CONNECT** engedélyt, és a **NYILVÁNOS** szerepkör tagjaként csatlakozhat az adatbázishoz. Kezdetben a felhasználó számára csak a **NYILVÁNOS** szerepkörhöz megadott engedélyek, vagy bármely olyan Azure AD-csoport számára megadott engedélyek, amelyeknek tagjai. Miután egy Azure AD-alapú tárolt adatbázis-felhasználó kiépítése, adhat a felhasználónak további engedélyeket, ugyanúgy, mint bármely más típusú felhasználó. Általában engedélyeket ad adatbázis-szerepköröknek, és felhasználókat ad hozzá a szerepkörökhöz. További információt a [Database Engine engedélyekkel kapcsolatos alapjai című témakörben talál.](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx) A speciális SQL-adatbázis-szerepkörökről az [Adatbázisok és bejelentkezések kezelése az Azure SQL Database-ben](sql-database-manage-logins.md)című témakörben talál további információt.
A felügyelt tartományba külső felhasználóként importált összevont tartományi felhasználói fióknak a felügyelt tartomány identitását kell használnia.

> [!NOTE]
> Az Azure AD-felhasználók az adatbázis metaadatai E típusú (EXTERNAL_USER) és X (EXTERNAL_GROUPS típusú csoportok) vannak megjelölve. További információ: [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-azure-synapse-by-using-ssms-or-ssdt"></a>Csatlakozás a felhasználói adatbázishoz vagy az Azure Synapse-hez SSMS vagy SSDT használatával  

Annak ellenőrzéséhez, hogy az Azure AD-rendszergazda megfelelően van beállítva, csatlakozzon a **fő** adatbázishoz az Azure AD rendszergazdai fiók használatával.
Egy Azure AD-alapú alapú adatbázis-felhasználó (kivéve a kiszolgáló rendszergazdája, amely az adatbázis tulajdonosa), csatlakozzon az adatbázishoz egy Azure AD-identitás, amely hozzáfér az adatbázishoz.

> [!IMPORTANT]
> Az Azure Active Directory-hitelesítés támogatása a Visual Studio [2015 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) és SQL Server Data Tools [szolgáltatásában](https://msdn.microsoft.com/library/mt204009.aspx) érhető el. Az SSMS 2016 augusztusi kiadása támogatja az Active Directory univerzális hitelesítést is, amely lehetővé teszi a rendszergazdák számára, hogy többtényezős hitelesítést igényeljenek telefonhívással, szöveges üzenettel, pin-kóddal ellátott intelligens kártyákkal vagy mobilalkalmazás-értesítéssel.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Az Azure AD-identitás használata SSMS vagy SSDT használatával történő csatlakozáshoz

Az alábbi eljárások bemutatják, hogyan csatlakozhat egy SQL-adatbázishoz egy Azure AD-identitással az SQL Server Management Studio vagy az SQL Server Database Tools használatával. 

### <a name="active-directory-integrated-authentication"></a>Integrált Active Directory-hitelesítés

Akkor használja ezt a módszert, ha az Azure Active Directory hitelesítő adataival egy összevont tartományból vagy egy felügyelt tartományból, amely konfigurálva van a zökkenőmentes egyszeri bejelentkezés az átadó és jelszó kivonathitelesítés. További információt az [Azure Active Directory zökkenőmentes egyszeri bejelentkezés című témakörben talál.](../active-directory/hybrid/how-to-connect-sso.md)

1. Indítsa el a Felügyeleti stúdiót vagy az Adateszközöket, és a Csatlakozás a **kiszolgálóhoz** (vagy az **adatbázis-működtető höz)** párbeszédpanel **Hitelesítés** mezőjében válassza az **Azure Active Directory – Integrated**lehetőséget. Nincs szükség jelszóra, vagy megadható, mert a meglévő hitelesítő adatok jelennek meg a kapcsolathoz.

    ![Az AD integrált hitelesítés ének kiválasztása][11]

2. Válassza a **Beállítások gombot,** és a **Kapcsolat tulajdonságai** lap Csatlakozás az **adatbázishoz** mezőjébe írja be annak a felhasználói adatbázisnak a nevét, amelyhez csatlakozni szeretne. További információt az SSMS 17.x és a 18.x csatlakozási tulajdonságai közötti különbségekről szóló [Többtényezős AAD-hitelesítés](sql-database-ssms-mfa-authentication.md#azure-ad-domain-name-or-tenant-id-parameter) című cikkben talál. 

    ![Az adatbázis nevének kijelölése][13]

### <a name="active-directory-password-authentication"></a>Active Directory jelszó-hitelesítés

Ezt a módszert akkor használja, ha az Azure AD felügyelt tartomány használatával csatlakozik egy Azure AD egyszerű névhez. A tartományhoz való hozzáférés nélküli összevont fiókokhoz is használhatja, például távolról végzett munka esetén.

Ezzel a módszerrel hitelesítheti magát az SQL DB vagy a MI az Azure AD csak felhőalapú identitásfelhasználók, vagy azok, akik az Azure AD hibrid identitások hitelesítése. Ez a módszer támogatja azokat a felhasználókat, akik windowsos hitelesítő adataikat szeretnék használni, de a helyi számítógépük nem csatlakozik a tartományhoz (például távelérés használatával). Ebben az esetben a Windows-felhasználó jelezheti a tartományi fiók és a jelszó, és hitelesíthető az SQL DB, MI vagy az Azure Synapse.

1. Indítsa el a Felügyeleti stúdiót vagy az Adateszközöket, és a Csatlakozás a **kiszolgálóhoz** (vagy **az adatbázis-motorhoz)** párbeszédpanel **Hitelesítés** mezőjében válassza az **Azure Active Directory – Jelszó**lehetőséget.

2. A **Felhasználónév** mezőbe írja be az Azure Active Directory felhasználónevét a **felhasználónév\@domain.com**formátumban. A felhasználóneveknek az Azure Active Directoryból származó fióknak vagy az Azure Active Directory felügyelt vagy összevont tartományból származó fióknak kell lenniük.

3. A **Jelszó** mezőbe írja be az Azure Active Directory-fiók vagy a felügyelt/összevont tartományi fiók felhasználói jelszavát.

    ![AD jelszó-hitelesítés kiválasztása][12]

4. Válassza a **Beállítások gombot,** és a **Kapcsolat tulajdonságai** lap Csatlakozás az **adatbázishoz** mezőjébe írja be annak a felhasználói adatbázisnak a nevét, amelyhez csatlakozni szeretne. (Lásd az előző beállítás ábráit.)

### <a name="active-directory-interactive-authentication"></a>Active Directory interaktív hitelesítése

Ezzel a módszerrel interaktív hitelesítést végez többtényezős hitelesítéssel (MFA), és a jelszót interaktív módon kéri. Ez a módszer az SQL DB, a MI és az Azure Synapse hitelesítésére használható az Azure AD csak felhőalapú identitásfelhasználóihoz, illetve az Azure AD hibrid identitások használóihoz.

További információ: [A többtényezős AAD-hitelesítés használata az Azure SQL Database és az Azure Synapse Analytics (SSMS-támogatás az MFA-hoz) című témakörben.](sql-database-ssms-mfa-authentication.md)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Ügyfélalkalmazásból való csatlakozás Azure AD-identitás használatával

Az alábbi eljárások bemutatják, hogyan csatlakozhat egy SQL-adatbázishoz egy ügyfélalkalmazásból származó Azure AD-identitással.

### <a name="active-directory-integrated-authentication"></a>Integrált Active Directory-hitelesítés

Az integrált Windows-hitelesítés használatához a tartomány Active Directoryját össze kell egyeztetni az Azure Active Directoryval, vagy olyan felügyelt tartománynak kell lennie, amely zökkenőmentes egyszeri bejelentkezésre van konfigurálva az átadó vagy jelszókivonat-hitelesítéshez. További információt az [Azure Active Directory zökkenőmentes egyszeri bejelentkezés című témakörben talál.](../active-directory/hybrid/how-to-connect-sso.md)

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) az integrált Windows-hitelesítéshez nem támogatott a zökkenőmentes egyszeri bejelentkezéshez az átmenő és jelszókivonat-hitelesítéshez.

Az adatbázishoz csatlakozó ügyfélalkalmazásnak (vagy szolgáltatásnak) a felhasználó tartományi hitelesítő adatai alatt tartományhoz csatlakozó gépen kell futnia.

Ha integrált hitelesítéssel és Azure AD-identitással szeretne csatlakozni egy adatbázishoz, az `Active Directory Integrated`adatbázis-kapcsolati karakterlánc hitelesítési kulcsának a beállítására kell állítani. A következő C# kódminta az ADO .NET-et használja.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A kapcsolati `Integrated Security=True` karakterlánc kulcsszó nem támogatott az Azure SQL Database-hez való csatlakozáshoz. Az ODBC-kapcsolat létrehozásakor el kell távolítania a szóközöket, és a Hitelesítés t "ActiveDirectoryIntegrated" beállításra kell állítania.

### <a name="active-directory-password-authentication"></a>Active Directory jelszó-hitelesítés

Ha csak Azure AD-alapú identitásfelhasználói fiókokat vagy azure AD hibrid identitásokat használó adatbázishoz szeretne `Active Directory Password`csatlakozni, a hitelesítési kulcsszót a következőre kell állítani. A kapcsolati karakterláncnak tartalmaznia kell a Felhasználói azonosító/UID és a Password/PWD kulcsszavakat és értékeket. A következő C# kódminta az ADO .NET-et használja.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Tudjon meg többet az Azure AD hitelesítési módszerek ről az [Azure AD authentication GitHub Demo-ban](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)elérhető bemutatókódminták használatával.

## <a name="azure-ad-token"></a>Azure AD-token

Ez a hitelesítési módszer lehetővé teszi a középső rétegbeli szolgáltatások számára, hogy [json webtokenek (JWT)](../active-directory/develop/id-tokens.md) az Azure SQL Database vagy az Azure Synapse az Azure Active Directoryból (AAD) származó jogkivonat megszerzéséhez. Ez a módszer különböző alkalmazási forgatókönyveket tesz lehetővé, beleértve a szolgáltatásidentitásokat, a szolgáltatásnév- és alkalmazásalkalmazásokat a tanúsítványalapú hitelesítés használatával. Az Azure AD token hitelesítésének használatához négy alapvető lépést kell végrehajtania:

1. Regisztrálja az alkalmazást az Azure Active Directoryban, és a kód ügyfélazonosítóját.
2. Hozzon létre egy, az alkalmazást képviselő adatbázis-felhasználót. (A 6. lépés korábbi lépése.)
3. Hozzon létre egy tanúsítványt az ügyfélszámítógépen futtatja az alkalmazást.
4. Adja hozzá a tanúsítványt az alkalmazás kulcsaként.

Minta kapcsolati karakterlánc:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

További információt az [SQL Server biztonsági blogjában talál.](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/) A tanúsítványok hozzáadásáról az [Azure Active Directory tanúsítványalapú hitelesítésének első lépései című témakörben talál.](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)

### <a name="sqlcmd"></a>sqlcmd

A következő utasítások az sqlcmd 13.1-es verziójával csatlakozhatnak, amely a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=53591)érhető el.

> [!NOTE]
> `sqlcmd`a `-G` parancs nem működik a rendszer identitások, és előírja, hogy a felhasználó alapvető bejelentkezés.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Az Azure AD-hitelesítés hibáinak elhárítása

Az Azure AD-hitelesítéssel kapcsolatos problémák elhárítására vonatkozó útmutatás a következő blogban található:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>További lépések

- A bejelentkezések, a felhasználók, az adatbázis-szerepkörök és az SQL-adatbázis engedélyei áttekintését a [Bejelentkezések, a felhasználók, az adatbázis-szerepkörök és](sql-database-manage-logins.md)a felhasználói fiókok című témakörben találja.
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
