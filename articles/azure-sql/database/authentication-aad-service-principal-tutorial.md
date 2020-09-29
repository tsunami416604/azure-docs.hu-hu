---
title: Azure AD-felhasználók létrehozása egyszerű szolgáltatásnév használatával
description: Ez az oktatóanyag végigvezeti az Azure AD-felhasználók Azure AD-alkalmazásokkal (egyszerű szolgáltatásokkal) való létrehozásának lépésein Azure SQL Database és az Azure szinapszis Analyticsben
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/17/2020
ms.openlocfilehash: 453821e99f53a90a076ff13f010f2031a055cbf6
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91444179"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>Oktatóanyag: Azure AD-felhasználók létrehozása az Azure AD-alkalmazásokkal

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> Ez a cikk **nyilvános előzetes**verzióban érhető el. További információ: [Azure Active Directory egyszerű szolgáltatásnév az Azure SQL](authentication-aad-service-principal.md)-ben. Ez a cikk Azure SQL Databaset használ a szükséges oktatóanyagi lépések bemutatására, de az [Azure szinapszis Analytics szolgáltatáshoz](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)hasonlóan is alkalmazható.

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure AD-felhasználókat a Azure SQL Databaseban az Azure-szolgáltatások (Azure AD-alkalmazások) használatával. Ez a funkció már létezik az Azure SQL felügyelt példányában, de most már bevezetésre kerül Azure SQL Database és az Azure szinapszis Analytics szolgáltatásban. Ennek a forgatókönyvnek a támogatásához Azure AD-identitást kell létrehozni és hozzárendelni az Azure SQL logikai kiszolgálóhoz.

Az Azure SQL Azure AD-hitelesítéssel kapcsolatos további információkért tekintse meg a [Azure Active Directory hitelesítés használata](authentication-aad-overview.md)című cikket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Identitás társítása az Azure SQL logikai kiszolgálóhoz
> - Directory-olvasók engedélyeinek kiosztása az SQL logikai kiszolgáló identitásához
> - Egyszerű szolgáltatásnév (Azure AD-alkalmazás) létrehozása az Azure AD-ben
> - Egyszerű szolgáltatásnév felhasználójának létrehozása Azure SQL Database
> - Egy másik Azure AD-felhasználó létrehozása SQL Database Azure AD-szolgáltatás egyszerű felhasználójának használatával

## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő [Azure SQL Database](single-database-create-quickstart.md) vagy az [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) üzembe helyezése. Feltételezzük, hogy van egy működő SQL Database ebben az oktatóanyagban.
- Hozzáférés egy már létező Azure Active Directoryhoz.
- Az [az. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) modul vagy újabb verzióra van szükség, amikor a PowerShell használatával állítson be egy egyéni Azure ad-alkalmazást Azure ad-rendszergazdaként az Azure SQL-hez. Győződjön meg arról, hogy a legújabb modulra van frissítve.

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>Identitás társítása az Azure SQL logikai kiszolgálóhoz

1. Kapcsolódjon a Azure Active Directoryhoz. Meg kell találnia a bérlő AZONOSÍTÓját. Ez a [Azure Portal](https://portal.azure.com), majd a **Azure Active Directory** -erőforrásra megy. Az **Áttekintés** ablaktáblán látnia kell a **bérlő azonosítóját**. Futtassa az alábbi PowerShell-parancsot:

    - Cserélje le a helyére `<TenantId>` a **bérlő azonosítóját**.

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    Jegyezze `TenantId` fel a jövőbeli használatot ebben az oktatóanyagban.

1. Azure AD-identitás létrehozása és kiosztása az Azure SQL logikai kiszolgálóval. Hajtsa végre a következő PowerShell-parancsot:

    - Cserélje `<resource group>` le `<server name>` a és az erőforrásait. Ha a kiszolgáló neve `myserver.database.windows.net` , cserélje le a (z) értékre `<server name>` `myserver` .

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    További információ: [set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) parancs.

    > [!IMPORTANT]
    > Ha Azure AD-identitás van beállítva az Azure SQL logikai kiszolgálóhoz, a címtár- [**olvasók**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) engedélyt meg kell adni az identitásnak. Ezt a lépést a következő szakaszban fogjuk átjárni. Ne hagyja ki ezt a lépést, mert az Azure AD-hitelesítés **nem** fog működni.

    - Ha a [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) parancsot használta `AssignIdentity` egy új SQL Server-létrehozási paraméterrel a múltban, akkor a [set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) parancsot később külön parancsként kell végrehajtania, hogy engedélyezze ezt a tulajdonságot az Azure-hálóban.

1. Győződjön meg arról, hogy a kiszolgáló identitása sikeresen hozzá lett rendelve. Hajtsa végre a következő PowerShell-parancsot:

    - Cserélje `<resource group>` le `<server name>` a és az erőforrásait. Ha a kiszolgáló neve `myserver.database.windows.net` , cserélje le a (z) értékre `<server name>` `myserver` .
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    A kimenetnek meg kell jelenítenie `PrincipalId` , `Type` és `TenantId` . A hozzárendelt identitás a `PrincipalId` .

1. Az identitást a [Azure Portal](https://portal.azure.com)is megtekintheti.

    - A **Azure Active Directory** erőforrás területen lépjen a **vállalati alkalmazások**elemre. Írja be az SQL logikai kiszolgáló nevét. Látni fogja, hogy rendelkezik az erőforráshoz csatolt **objektumazonosító-azonosítóval** .
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="Object-ID":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>Directory-olvasók engedélyeinek kiosztása az SQL logikai kiszolgáló identitásához

Ahhoz, hogy az Azure AD-beli hozzárendelt identitás megfelelően működjön az Azure SQL-hez, az Azure AD- `Directory Readers` engedélyt meg kell adni a kiszolgáló identitásának.

A szükséges engedély megadásához futtassa az alábbi szkriptet.

> [!NOTE] 
> Ezt a szkriptet egy Azure AD-nek `Global Administrator` vagy egy-nak kell végrehajtania `Privileged Roles Administrator` .
>
> A **nyilvános előzetes**verzióban hozzárendelheti a `Directory Readers` szerepkört egy csoporthoz az Azure ad-ben. A csoport tulajdonosai ezt követően hozzáadhatják a felügyelt identitást a csoport tagjaként, ami megkerüli a `Global Administrator` vagy a `Privileged Roles Administrator` szerepkör megadásának szükségességét `Directory Readers` . A szolgáltatással kapcsolatos további információkért lásd: az [Azure SQL Azure Active Directory Directory-olvasók szerepköre](authentication-aad-directory-readers-role.md).

- Cserélje le a- `<TenantId>` t a `TenantId` korábban összegyűjtöttre.
- Cserélje le az `<server name>` kifejezést az SQL logikai kiszolgáló nevére. Ha a kiszolgáló neve `myserver.database.windows.net` , cserélje le a (z) értékre `<server name>` `myserver` .

```powershell
# This script grants Azure "Directory Readers" permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or "Privileged Roles Administrator" type of user.
# To check if the "Directory Readers" permission was granted, execute this script again

Import-Module AzureAD
Connect-AzureAD -TenantId "<TenantId>"    #Enter your actual TenantId
$AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
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
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$($AssignIdentityName)', make sure that AssignIdentityName parameter was entered correctly."
    exit
}

if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
 
if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($AssignIdentityName)' to 'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to 'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
} else {
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> A fenti szkript kimenete arra utal, hogy a címtár-olvasók engedélyt kapott-e az identitásnak. Ha nem biztos abban, hogy megadták az engedélyt, futtassa újra a parancsfájlt.

Az SQL felügyelt példányra vonatkozó **címtár** -olvasási engedélyek beállításával kapcsolatos hasonló módszerért lásd: az [Azure ad-rendszergazda kiépítése (SQL felügyelt példány)](authentication-aad-configure.md#powershell).

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>Egyszerű szolgáltatásnév (Azure AD-alkalmazás) létrehozása az Azure AD-ben

1. Kövesse az alábbi útmutatót az [alkalmazás regisztrálásához és az engedélyek beállításához](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions).

    Ügyeljen arra, hogy hozzáadja az **alkalmazás engedélyeit** , valamint a **delegált engedélyeket**.

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="Object-ID":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="Object-ID":::

2. Létre kell hoznia egy ügyfél-titkot is a bejelentkezéshez. Kövesse az alábbi útmutatót a [tanúsítvány feltöltéséhez vagy a bejelentkezéshez szükséges titkos kód létrehozásához](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options).

3. Jegyezze fel a következőt az alkalmazás regisztrációjában. Az **Áttekintés** panelen elérhetőnek kell lennie:
    - **Alkalmazásazonosító**
    - **Bérlő azonosítója** – ennek a következőnek kell lennie:

Ebben az oktatóanyagban a *AppSP* -t fogjuk használni a fő szolgáltatásnévként, és *SAJÁTPR* az Azure SQL-ben a *AppSP*által létrehozott második egyszerű szolgáltatásként. Létre kell hoznia két alkalmazást, a *AppSP* -t és a *SajátPr*-t.

Az Azure AD-alkalmazások létrehozásával kapcsolatos további információkért tekintse meg a következő cikket [: útmutató: a portál használata az erőforrásokhoz hozzáférő Azure ad-alkalmazás és egyszerű szolgáltatásnév létrehozásához](../../active-directory/develop/howto-create-service-principal-portal.md).


## <a name="create-the-service-principal-user-in-azure-sql-database"></a>Egyszerű szolgáltatásnév-felhasználó létrehozása Azure SQL Databaseban

Miután létrehozta a szolgáltatásnevet az Azure AD-ben, hozza létre a felhasználót SQL Databaseban. Ahhoz, hogy a felhasználók az adatbázisban legyenek létrehozva, a SQL Databasehoz kell csatlakoznia egy érvényes bejelentkezési engedéllyel.

1. Hozza létre a felhasználói *AppSP* a SQL Database a következő T-SQL parancs használatával:

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. Adjon `db_owner` engedélyt a *AppSP*, amely lehetővé teszi, hogy a felhasználó más Azure ad-felhasználókat hozzon létre az adatbázisban.

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    További információ: [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    Másik lehetőségként `ALTER ANY USER` engedélyt is kaphat a szerepkör megadása helyett `db_owner` . Ez lehetővé teszi, hogy az egyszerű szolgáltatás további Azure AD-felhasználókat adjon hozzá.

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > A fenti beállítás nem szükséges, ha a *AppSP* a-kiszolgáló Azure ad-rendszergazdájaként van beállítva. Ha a szolgáltatásnevet az SQL logikai kiszolgálóhoz tartozó AD-rendszergazdaként szeretné beállítani, használhatja a Azure Portal, a PowerShell vagy az Azure CLI-parancsokat. További információ: az [Azure ad-rendszergazda kiépítése (SQL Database)](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse).

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>Azure AD-felhasználó létrehozása SQL Database Azure AD-szolgáltatásnév használatával

> [!IMPORTANT]
> Az SQL Databaseba való bejelentkezéshez használt egyszerű szolgáltatásnak ügyfél-titkos kulccsal kell rendelkeznie. Ha még nem rendelkezik ilyennel, kövesse az [egyszerű szolgáltatás létrehozása (Azure ad-alkalmazás) az Azure ad-ben](#create-a-service-principal-an-azure-ad-application-in-azure-ad)című 2. lépését. Ezt az ügyfél-titkos kulcsot hozzá kell adni bemeneti paraméterként az alábbi parancsfájlban.

1. Az alábbi szkript használatával hozzon létre egy egyszerű Azure AD-beli felhasználói *SajátPr* az egyszerű szolgáltatásnév *AppSP*használatával.

    - Cserélje le a- `<TenantId>` t a `TenantId` korábban összegyűjtöttre.
    - Cserélje le a- `<ClientId>` t a `ClientId` korábban összegyűjtöttre.
    - Cserélje le a- `<ClientSecret>` t a korábban létrehozott ügyfél-titkos kulcsra.
    - Cserélje le az `<server name>` kifejezést az SQL logikai kiszolgáló nevére. Ha a kiszolgáló neve `myserver.database.windows.net` , cserélje le a (z) értékre `<server name>` `myserver` .
    - Cserélje le a helyére `<database name>` a SQL Database nevét.

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret

    $tenantId = "<TenantId>"   #  tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   #  AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   #  client secret for AppSP 
    $Resource = "https://database.windows.net/"
    
    $adalPath  = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\5.8.3"
    # To install the latest AzureRM.profile version execute  -Install-Module -Name AzureRM.profile
    $adal      = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal) | Out-Null
      $resourceAppIdURI = 'https://database.windows.net/'

      # Set Authority to Azure AD Tenant
      $authority = 'https://login.windows.net/' + $tenantId

      $ClientCred = [Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential]::new($clientId, $clientSecret)
      $authContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]::new($authority)
      $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI,$ClientCred)
      $Tok = $authResult.Result.CreateAuthorizationHeader()
      $Tok=$Tok.Replace("Bearer ","")
      Write-host "token"
      $Tok
      Write-host  " "

    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    Write-Host "Create SQL connectionstring"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $DatabaseName = "<database name>"     # Azure SQL database name
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()  
    ``` 

    Azt is megteheti, hogy a blogbejegyzésben az [Azure ad szolgáltatás egyszerű hitelesítését használja az SQL db-Code mintában](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467). Módosítsa a parancsfájlt a DDL-utasítás végrehajtásához `CREATE USER [myapp] FROM EXTERNAL PROVIDER` . Ugyanezt a szkriptet használhatja az Azure AD-felhasználók egy csoportjának létrehozására SQL Databaseban.

    > [!NOTE]
    > Ha telepítenie kell a AzureRM. Profile modult, akkor a PowerShellt rendszergazdaként kell megnyitnia. A következő parancsokkal automatikusan telepítheti a legújabb AzureRM. profil verziót, és beállíthatja `$adalpath` a fenti parancsfájlt:
    > 
    > ```powershell
    > Install-Module AzureRM.profile -force
    > Import-Module AzureRM.profile
    > $version = (Get-Module -Name AzureRM.profile).Version.toString()
    > $adalPath = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\${version}"
    > ```
    
2. Ellenőrizze, hogy a felhasználó *SajátPr* létezik-e az adatbázisban a következő parancs végrehajtásával:

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>További lépések

- [Azure Active Directory egyszerű szolgáltatásnév az Azure SQL-sel](authentication-aad-service-principal.md)
- [Mik azok az Azure-erőforrások felügyelt identitásai?](../../active-directory/managed-identities-azure-resources/overview.md)
- [Felügyelt identitások használata App Service és Azure Functions](../../app-service/overview-managed-identity.md)
- [Azure AD-szolgáltatás egyszerű hitelesítése az SQL DB-code minta használatával](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)
- [Alkalmazás- és szolgáltatásnév-objektumok az Azure Active Directoryban](../../active-directory/develop/app-objects-and-service-principals.md)
- [Azure-beli szolgáltatásnév létrehozása az Azure PowerShell használatával](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
- [Az Azure SQL Azure Active Directory Directory-olvasói szerepköre](authentication-aad-directory-readers-role.md)
