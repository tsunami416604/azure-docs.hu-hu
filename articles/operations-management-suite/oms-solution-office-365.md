---
title: Az Office 365 felügyeleti megoldás az Azure-ban |} Microsoft Docs
description: Ez a cikk részletesen konfigurációs és használja az Office 365-megoldás az Azure-ban.  A Naplóelemzési létrehozott Office 365-rekordok részletes leírását tartalmazza.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: bwren
ms.openlocfilehash: e93860328ed6a31b7a06cc3420fb50ab4af9a00c
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236103"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Az Office 365 felügyeleti megoldás az Azure (előzetes verzió)

![Az Office 365-embléma](media/oms-solution-office-365/icon.png)

Az Office 365 felügyeleti megoldás az Office 365-környezethez az Naplóelemzési figyelését teszi lehetővé.

- Az Office 365-fiókok elemezheti a használati szokásokat, valamint a viselkedési trendek azonosításához felhasználói tevékenység figyelésének. Például is kibonthat egy konkrét használati forgatókönyvek, például a szervezet vagy a legnépszerűbb SharePoint-webhelyek kívül megosztott fájlokat.
- Rendszergazdai tevékenységek nyomon követéséhez a konfigurációs módosítások vagy magas jogosultsági műveletek figyelését.
- Észleli, és vizsgálja meg a nem kívánt felhasználói viselkedés, amely a szervezet igényeinek testre is szabható.
- Naplózási és megfelelőségi bemutatása. Például fájl hozzáférési műveleteket bizalmas fájlokat, amely segíthet a naplózási és megfelelőségi eljárásait a figyelheti.
- A működési hibaelhárítás [keresések jelentkezzen](../log-analytics/log-analytics-log-search.md) Office 365-tevékenységek adatait a szervezet felett.

## <a name="prerequisites"></a>Előfeltételek
A következő szükség, mielőtt a megoldás telepítve és konfigurálva.

- Szervezeti Office 365-előfizetéssel.
- Egy felhasználói fiókot, amely globális rendszergazda hitelesítő adatait.
- Naplózási adatok fogadására kell [naplózásának konfigurálása](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) az Office 365-előfizetésben.  Vegye figyelembe, hogy [postaláda-naplózás](https://technet.microsoft.com/library/dn879651.aspx) külön kell beállítani.  Továbbra is telepítheti a megoldás, és be más adatokat, ha a naplózás nincs konfigurálva.
 

## <a name="management-packs"></a>Felügyeleti csomagok
Ez a megoldás nem telepíti a felügyeleti csomagok a [csatlakoztatott felügyeleti csoportok](../log-analytics/log-analytics-om-agents.md).
  
## <a name="install-and-configure"></a>Telepítés és konfigurálás
Először vegyen fel a [Office 365-megoldás az előfizetéséhez](/monitoring/monitoring-solutions.md#install-a-management-solution). Hozzáadott, ebben a szakaszban a hozzáférést az Office 365-előfizetéshez hajtsa végre a konfigurációs lépéseket.

### <a name="required-information"></a>Szükséges információk
Ez az eljárás megkezdése előtt gyűjtse össze az alábbi adatokat.

A Naplóelemzési munkaterület:

- Munkaterület neve: A munkaterület hova legyenek összegyűjtve az Office 365-adatokat.
- Az erőforráscsoport neve: a munkaterületet tartalmazó erőforráscsoportot.
- Az Azure előfizetés-azonosító: az előfizetés, amely tartalmazza a munkaterületen.

Az Office 365 előfizetésből:

- Felhasználónév: Egy rendszergazdai fiókjának E-mail címe.
- Bérlő azonosítója: Office 365-előfizetéssel egyedi azonosítója.
- Ügyfél-azonosító: 16 karakterből álló karakterlánc, amely az Office 365-ügyfél jelöli.
- Ügyfélkulcs: Titkosított karakterlánc hitelesítéshez szükséges.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Az Office 365-alkalmazás létrehozása az Azure Active Directoryban
Az első lépés az alkalmazás létrehozása az Azure Active Directoryban, amely a felügyeleti megoldás az Office 365-megoldás elérésére fogja használni.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) címen.
1. Válassza ki **Azure Active Directory** , majd **App regisztrációk**.
1. Kattintson az **Új alkalmazásregisztráció** elemre.

    ![Adja hozzá az alkalmazás regisztrálása](media/oms-solution-office-365/add-app-registration.png)
1. Adja meg az alkalmazás **neve** és **bejelentkezési URL-cím**.  A névnek kell lennie leíró.  Használjon _http://localhost_ az URL-címet, és csak a _webalkalmazás / API_ a a **alkalmazás típusa**
    
    ![Alkalmazás létrehozása](media/oms-solution-office-365/create-application.png)
1. Kattintson a **létrehozása** és az alkalmazás adatainak ellenőrzése.

    ![Regisztrált alkalmazás](media/oms-solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Az Office 365 alkalmazás konfigurálása

1. Kattintson a **beállítások** megnyitásához a **beállítások** menü.
1. Válassza ki **tulajdonságok**. Változás **Multi-központjaként** való _Igen_.

    ![Több-bérlős beállítások](media/oms-solution-office-365/settings-multitenant.png)

1. Válassza ki **szükséges engedélyek** a a **beállítások** menüre, majd majd **hozzáadása**.
1. Kattintson a **API kiválasztása** , majd **Office 365 felügyeleti API-k**. Kattintson a **Office 365 felügyeleti API-k**. Kattintson a **Kiválasztás** gombra.

    ![API kiválasztása](media/oms-solution-office-365/select-api.png)

1. A **engedélyként válassza** válassza a következő beállításokat is **Alkalmazásengedélyek** és **delegált engedélyekkel**:
    - A munkahelyhez tartozó szolgáltatásállapot-adatok olvasása
    - A munkahelyi tevékenységadatok olvasása
    - A munkahelyre vonatkozó tevékenységjelentések olvasása

    ![API kiválasztása](media/oms-solution-office-365/select-permissions.png)

1. Kattintson a **válasszon** , majd **végzett**.
1. Kattintson a **engedélyeket** majd **Igen** kérdésnél az ellenőrzéshez.

    ![Engedélyek megadása](media/oms-solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Az alkalmazás kulcs hozzáadása

1. Válassza ki **kulcsok** a a **beállítások** menü.
1. Írja be a **leírás** és **időtartam** az új kulcs.
1. Kattintson a **mentése** , és másolja a **érték** , amely jön létre.

    ![Kulcsok](media/oms-solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Adja hozzá a rendszergazda jóváhagyását
A rendszergazdai fiók engedélyezéséhez először meg kell adnia a felügyeleti hozzájárulási az alkalmazáshoz. Ez egy PowerShell-parancsfájllal teheti meg. 

1. A következő parancsfájlt mentése *office365_consent.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
        
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocationShort= $Workspace.PortalUrl.Split("/",$option)[1].Split(".",$option)[0]
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    $mmsDomain = 'login.mms.microsoft.com'
    $WorkspaceLocationShort= $Workspace.PortalUrl.Split("/",$option)[1].Split(".",$option)[0]
    $WorkspaceLocationShort
    $WorkspaceLocation
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us';
                             $mmsDomain = 'usbn1.login.oms.microsoft.us'; break} # US Gov Virginia
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $OfficeAppRedirectUrl="https://$($WorkspaceLocationShort).$($mmsDomain)/Office365/Authorize"
        $OfficeAppRedirectUrl
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345&redirect_uri=$($OfficeAppRedirectUrl)"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Futtassa a parancsfájlt a következő paranccsal.
    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```
    Példa:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Az alábbihoz hasonló ablak meg fog jelenni. Kattintson a **elfogadása**.
    
    ![Rendszergazdai jóváhagyás](media/oms-solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Fizessen elő a Naplóelemzési munkaterület
Az utolsó lépés a Naplóelemzési munkaterület az alkalmazás előfizetni. Akkor is ugyanezt a műveletet egy PowerShell-parancsfájlt.

1. A következő parancsfájlt mentése *office365_subscription.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    # Load ADAL Azure AD Authentication Library Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    $null = [System.Reflection.Assembly]::LoadFrom($adal)
    $null = [System.Reflection.Assembly]::LoadFrom($adalforms)
     
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. Futtassa a parancsfájlt a következő paranccsal:
    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```
    Példa:

    ```
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troublshooting"></a>Troublshooting

Ha megpróbálja előfizetés létrehozása után már létezik a következő hiba jelenhet meg.

```
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Ha az értékek érvénytelen paraméter a következő hiba jelenhet meg.

```
Select-AzureRmSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzureRmSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureRmContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzureRMContextCommand

```

## <a name="uninstall"></a>Eltávolítás
Eltávolíthatja az Office 365 megoldás a folyamatot követve [távolítsa el a felügyeleti megoldás](../monitoring/monitoring-solutions.md#remove-a-management-solution). Ezzel leállítja összegyűjtött adatokat az Office 365 szolgáltatásból történő Naplóelemzési azonban nem. Office 365 lemondani és adatgyűjtés leállításához az alábbi eljárást követve.

1. A következő parancsfájlt mentése *office365_unsubscribe.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    # Load ADAL Azure AD Authentication Library Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    $null = [System.Reflection.Assembly]::LoadFrom($adal)
    $null = [System.Reflection.Assembly]::LoadFrom($adalforms)
     
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Futtassa a parancsfájlt a következő paranccsal:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Példa:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Adatgyűjtés
### <a name="supported-agents"></a>Támogatott ügynökök
Az Office 365-megoldás nem adatainak lekérése bármelyikét a [OMS-ügynököt](../log-analytics/log-analytics-data-sources.md).  Office 365-ről, lekéri az adatokat.

### <a name="collection-frequency"></a>A gyűjtés gyakorisága
Összegyűjtendő adatok kezdetben néhány órát is igénybe vehet. Gyűjtése kezdődik, ha az Office 365 küld egy [webhook értesítési](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) szükséges részletes adatok Naplóelemzési minden alkalommal létrejön egy bejegyzés. Ez a bejegyzés érhető Naplóelemzési fogadása után néhány percen belül.

## <a name="using-the-solution"></a>A megoldás használata
Ha az Office 365-megoldás hozzáadni a Naplóelemzési munkaterület a **Office 365** csempe nem kerülnek be az irányítópulton. Ez a csempe a környezetben jelenleg elérhető számítógépek számát és grafikus ábrázolását jeleníti meg, valamint a frissítési megfelelőségi állapotukat.<br><br>
![Az Office 365 összefoglalás Csempéje](media/oms-solution-office-365/tile.png)  

Kattintson a **Office 365** csempére kattintva nyissa meg a **Office 365** irányítópult.

![Az Office 365 irányítópult](media/oms-solution-office-365/dashboard.png)  

Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Minden oszlop felső tíz riasztások száma a megadott hatókör és időtartomány adott oszlop feltételeknek megfelelő sorolja fel. A napló-keresés, amely teljes listáját jeleníti meg, tekintse meg az összes oszlop alján kattintva vagy az oszlop fejlécére kattintva futtathatja.

| Oszlop | Leírás |
|:--|:--|
| Műveletek | Az összes figyelt Office 365-előfizetés az aktív felhasználók információkat biztosít. Fordulhat elő, időbeli tevékenységek számát is lesz meg.
| Exchange | Az Exchange Server tevékenységek, például az Add-postaláda engedély, vagy a Set-postaláda részletes információkat tartalmazza. |
| SharePoint | A felső tevékenységek jeleníti meg, hogy a felhasználók végrehajtani a SharePoint-dokumentumok. Ha ez a csempe a Lehatolás a lapon ezeket a tevékenységeket, például a céldokumentumban és helyét, ezt a tevékenységet részleteit jeleníti meg. Például a fájl elérhető esemény, akkor fog tudni tekintse meg a dokumentumot, is hozzáférnek, hozzárendelt fiók nevét és IP-címet. |
| Azure Active Directory | Felső felhasználói tevékenységek, például a felhasználói jelszó alaphelyzetbe állítása és a bejelentkezési kísérletek tartalmazza. Ha le, lesz, ezek a tevékenységek, például az eredmény állapot részleteinek megtekintéséhez. Főleg bizonyulhat hasznosnak, ha azt szeretné, hogy az Azure Active Directory gyanús tevékenységek figyelésére. |




## <a name="log-analytics-records"></a>Log Analytics-rekordok

A Naplóelemzési munkaterület az Office 365-megoldás által létrehozott összes rekord rendelkezik egy **típus** a **OfficeActivity**.  A **OfficeWorkload** tulajdonság határozza meg, melyik Office 365 szolgáltatás a rekord hivatkozik - Exchange, az AzureActiveDirectory, a SharePoint vagy a onedrive-on.  A **RecordType** tulajdonság határozza meg a műveletet.  A tulajdonságok az egyes művelet függ, és az alábbi táblázatban láthatók.

### <a name="common-properties"></a>Általános tulajdonságai
A következő tulajdonságok megegyeznek az összes Office 365 bejegyzésre.

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus | *OfficeActivity* |
| ClientIP | Az eszköz, amikor a tevékenység naplózott használt IP-címét. Az IP-cím IPv4 vagy IPv6 cím formátumban jelenik meg. |
| OfficeWorkload | Az Office 365 szolgáltatás, amely a rekord hivatkozik.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Művelet | A felhasználói vagy rendszergazdai tevékenység nevét.  |
| A szervezeti | A szervezet Office 365-bérlő GUID azonosítója. Ez az érték mindig lesz ugyanaz a szervezet az Office 365 szolgáltatást, amely függetlenül. |
| recordType | A végrehajtott művelet típusa. |
| ResultStatus | Azt jelzi, hogy (a művelet tulajdonságban megadott) a művelet sikeres volt-e vagy sem. Lehetséges értékek: sikeres, PartiallySucceded vagy sikertelen. Az Exchange felügyeleti tevékenység, értéke pedig IGAZ vagy hamis. |
| Felhasználói azonosító | Az UPN (egyszerű felhasználónév) a felhasználó hajtotta végre a műveletet, amelyek a rekord, a naplózott; például my_name@my_domain_name. Vegye figyelembe, hogy rendszer fiókok (például SHAREPOINT\system vagy NTAUTHORITY\SYSTEM) által végzett tevékenység rekordok is szerepelnek. | 
| UserKey | A UserId tulajdonság a megadott felhasználó alternatív azonosító.  Például ezt a tulajdonságot a passport egyedi azonosítója (PUID) és az Exchange a SharePoint, a onedrive vállalati verzió a felhasználók által végrehajtott események telepítéskor. Ez a tulajdonság is meghatározza, ugyanazt az értéket a UserID tulajdonság a szolgáltatások és rendszerfiókok által végzett események előforduló események|
| UserType | A felhasználó által végrehajtott a művelet típusát.<br><br>Adminisztratív körzet<br>Alkalmazás<br>DcAdmin<br>Rendszeres<br>Foglalt<br>Szolgáltatásnév<br>Rendszer |


### <a name="azure-active-directory-base"></a>Az Azure Active Directory-alap
A következő tulajdonságok megegyeznek az összes Azure Active Directory bejegyzésre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| recordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Az Azure AD-esemény típusa. |
| Az ExtendedProperties | A kiterjesztett tulajdonságok az Azure AD-esemény. |


### <a name="azure-active-directory-account-logon"></a>Az Azure Active Directory-fiók bejelentkezési
Ezeket a rekordokat hoz létre egy Active Directory-felhasználó megpróbál bejelentkezni.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| recordType     | AzureActiveDirectoryAccountLogon |
| Alkalmazás | Az alkalmazás, amely elindítja a fiók bejelentkezési esemény, például az Office 15. |
| Ügyfél | Az ügyfél adatait eszközt, az eszköz operációs rendszere és a használt eszköz böngészőjében a fiók bejelentkezési esemény. |
| LoginStatus | Ez a tulajdonság közvetlenül OrgIdLogon.LoginStatus származik. Különböző érdekes bejelentkezési hibák leképezése által algoritmusok riasztási megteheti. |
| UserDomain | A bérlői azonosító adatok (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory-objektumok végrehajtott módosítás vagy kiegészítés érkezett ezeket a rekordokat hoz létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| recordType     | AzureActiveDirectory |
| AADTarget | A felhasználó, aki a (a művelet tulajdonság által azonosított) műveletet hajtott végre. |
| Aktor | A felhasználó vagy szolgáltatás egyszerű, amely hajtotta végre a műveletet. |
| ActorContextId | A GUID Azonosítót a szervezet, amely a szereplő tartozik. |
| ActorIpAddress | A szereplő IP-cím IPV4 vagy IPv6-cím formátuma. |
| InterSystemsId | A GUID, amelyek nyomon követik a műveletek az Office 365 szolgáltatáson belül lévő összetevői között. |
| IntraSystemId |   Az Azure Active Directory nyomon követéséhez a művelet által létrehozott GUID. |
| SupportTicketId | Az ügyfélszolgálati Jegyazonosító "művelet" helyzetekben a művelethez. |
| TargetContextId | A GUID azonosítója, amely a célként megadott felhasználó tagja a szervezet. |


### <a name="data-center-security"></a>Adatközpont-biztonsági
Ezeket a rekordokat Data Center biztonsági naplózási adatokból jönnek létre.  

| Tulajdonság | Leírás |
|:--- |:--- |
| EffectiveOrganization | A bérlő a jogosultságszint-emelés parancsmagot célzott a neve. |
| ElevationApprovedTime | Ha a jogosultsági szint emeléséhez jóváhagyva időbélyegzőjét. |
| ElevationApprover | A Microsoft-kezelő neve. |
| ElevationDuration | Az időtartam, amely a jogosultsági szint emeléséhez aktív volt. |
| ElevationRequestId |  A jogosultságszint-emelési kérelem egyedi azonosítója. |
| ElevationRole | A szerepkör a jogosultságszint-emelés szükséges. |
| ElevationTime | A jogosultsági szint emeléséhez kezdési idejét. |
| Start_Time | A kezdési időt a parancsmagok végrehajtása. |


### <a name="exchange-admin"></a>Exchange-rendszergazda
Ezeket a rekordokat hoz létre módosításai az Exchange konfigurálása.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| recordType     | ExchangeAdmin |
| ExternalAccess |  Meghatározza, hogy a parancsmag futtatása egy felhasználó a szervezete a Microsoft datacenter személyzet vagy datacenter szolgáltatásfiók, vagy a meghatalmazott rendszergazda. A hamis értéket jelzi, hogy a parancsmag futott-e valaki a szervezetében. Az IGAZ értéket jelzi, hogy a parancsmag datacenter csoporthoz, datacenter szolgáltatásfiók vagy a meghatalmazott rendszergazda futtatták. |
| ModifiedObjectResolvedName |  Ez az az objektum, amelyet a parancsmag által felhasználóbarát nevét. Ez a rendszer naplózza csak akkor, ha a parancsmag módosítja az objektum. |
| Szervezetnév | A bérlő neve. |
| OriginatingServer | A kiszolgáló, amelyből végre lett hajtva a parancsmag neve. |
| Paraméterek | A név- és az összes paramétert, a parancsmag Operations tulajdonságában azonosított használt. |


### <a name="exchange-mailbox"></a>Exchange postaláda
Exchange-postaládák végrehajtott módosításait és kiegészítéseit ezeket a rekordokat hoz létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| recordType     | ExchangeItem |
| ClientInfoString | Az e-mail ügyfélprogramokban, például egy böngésző verziója, az Outlook verzióját és a mobileszköz-információk a művelet végrehajtásához használt információk. |
| Client_IPAddress | Az eszközt, hogy a művelet naplózásakor használt IP-címét. Az IP-cím IPv4 vagy IPv6 cím formátumban jelenik meg. |
| ClientMachineName | A számítógép neve, amelyen az Outlook ügyfélnek. |
| ClientProcessName | Az e-mail ügyfélprogramban a postaládájuk eléréséhez használt. |
| ClientVersion | Az e-mailek ügyfél verzióját. |
| InternalLogonType | Belső használatra fenntartva. |
| Logon_Type | A postaláda érhető el, és a naplózott műveletet végre felhasználó típusát jelöli. |
| LogonUserDisplayName |    A műveletet végző felhasználó felhasználóbarát neve. |
| LogonUserSid | A művelet elvégző felhasználó SID. |
| MailboxGuid | Az Exchange GUID azonosítója, amelynek a postaládát. |
| MailboxOwnerMasterAccountSid | Postaláda tulajdonos fiók fő fiók SID azonosítója. |
| MailboxOwnerSid | A postaláda-tulajdonos biztonsági azonosítója. |
| MailboxOwnerUPN | A postaláda, amelynek tulajdonosának e-mail címe. |


### <a name="exchange-mailbox-audit"></a>Exchange postaláda naplózása
Ezeket a rekordokat hoz létre egy postaláda naplóbejegyzés jön létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| recordType     | ExchangeItem |
| Elem | Az elem, amelyre a művelet végrehajtása jelenti. | 
| SendAsUserMailboxGuid | Az Exchange GUID azonosítója a postaládát, amelynek elküldeni e-mailekhez. |
| SendAsUserSmtp | SMTP-cím megszemélyesített felhasználó. |
| SendonBehalfOfUserMailboxGuid | Az Exchange GUID nevében üzenetküldésre használt postafiók. |
| SendOnBehalfOfUserSmtp | A felhasználó, akinek a nevében az e-mailt küld az SMTP-cím. |


### <a name="exchange-mailbox-audit-group"></a>Exchange postaláda naplózási csoport
Exchange-csoportok végrehajtott módosításait és kiegészítéseit ezeket a rekordokat hoz létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | A csoport összes elemére vonatkozó adatokat. |
| CrossMailboxOperations | Azt jelzi, ha a művelet csak egy postaláda vesz-e. |
| DestMailboxId | Állítsa be, csak akkor, ha a CrossMailboxOperations paraméter értéke True. Megadja a cél postaláda GUID. |
| DestMailboxOwnerMasterAccountSid | Állítsa be, csak akkor, ha a CrossMailboxOperations paraméter értéke True. Meghatározza a fő fiók SID azonosítója a target postaláda tulajdonosának a SID-AZONOSÍTÓT. |
| DestMailboxOwnerSid | Állítsa be, csak akkor, ha a CrossMailboxOperations paraméter értéke True. Megadja a cél postaláda biztonsági azonosítója. |
| DestMailboxOwnerUPN | Állítsa be, csak akkor, ha a CrossMailboxOperations paraméter értéke True. Megadja a cél postaláda tulajdonosának az egyszerű Felhasználónevet. |
| DestFolder | A célmappa, például az áthelyezési műveletek. |
| Mappa | A tároló mappát cikkek csoportja. |
| Mappák |     A forrás; egy műveletben érintett mappák kapcsolatos információk Ha például mappák van kiválasztva, és a törölt. |


### <a name="sharepoint-base"></a>SharePoint-alap
Ezek a Tulajdonságok megegyeznek a minden SharePoint-rekordot.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Meghatározza, hogy az esemény történt-e a SharePoint. Lehetséges értékek a következők: a SharePoint vagy ObjectModel. |
| ItemType | A típusú objektum, amely nem érhető el, vagy módosították. Részletekért tekintse meg az ItemType táblázatot objektumok típusával. |
| MachineDomainInfo | Eszköz szinkronizálási műveleteire vonatkozó adatokat. Ezek az információk csak akkor, ha a kérelem szerepel az elvártnak. |
| MachineId |   Eszköz szinkronizálási műveleteire vonatkozó adatokat. Ezek az információk csak akkor, ha a kérelem szerepel az elvártnak. |
| Site_ | A GUID azonosítója a hely, ahol a fájl vagy mappa a felhasználók férjenek hozzá. |
| Source_Name | A rendszervizsgálati módban levő műveletet kiváltó entitás. Lehetséges értékek a következők: a SharePoint vagy ObjectModel. |
| Felhasználói ügynök | A felhasználó ügyfél vagy a böngésző információt. Ez az információ az ügyfél vagy a böngésző által biztosított. |


### <a name="sharepoint-schema"></a>SharePoint-séma
Konfiguráció módosításai SharePoint ezeket a rekordokat hoz létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Egyéni események választható karakterláncot. |
| Event_Data |  Egyéni események választható hasznos. |
| ModifiedProperties | A tulajdonság része a rendszergazda eseményeket, mint például a felhasználó hozzáadása egy helyhez vagy egy hely gyűjtési felügyeleti csoport tagjaként. A tulajdonság módosításának (például a webhely-felügyeleti csoport), az új érték a módosított tulajdonság (például a felhasználó hozzá lett adva, a webhely rendszergazdájától), és a módosított objektum az előző érték a tulajdonság nevét tartalmazza. |


### <a name="sharepoint-file-operations"></a>SharePoint fájlműveletek
Ezeket a rekordokat a SharePoint fájlműveletek válaszul jönnek létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | A fájl kiterjesztése egy fájlt másol vagy áthelyezték. Ez a tulajdonság csak FileCopied és FileMoved esemény jelenik meg. |
| DestinationFileName | A fájl másolása vagy áthelyezett neve. Ez a tulajdonság csak FileCopied és FileMoved esemény jelenik meg. |
| DestinationRelativeUrl | A célmappa, ahol egy fájlt másolja vagy áthelyezték URL-CÍMÉT. A SiteURL DestinationRelativeURL és DestinationFileName paraméter értékének kombinációja megegyezik a ObjectID tulajdonság, amely a másolt fájl teljes elérési útja neve értékét. Ez a tulajdonság csak FileCopied és FileMoved esemény jelenik meg. |
| SharingType | A megosztási engedélyek a felhasználót, hogy az erőforrás lett megosztva rendelt típusa. Ez a felhasználó a UserSharedWith paraméter azonosítja. |
| Site_Url | A hely, ahol a fájl vagy mappa a felhasználók férjenek hozzá URL-CÍMÉT. |
| SourceFileExtension | A fájl kiterjesztése a fájlt, amely a felhasználó hozzáfért. A tulajdonság üres, ha az objektum, amelynek egy mappát. |
| SourceFileName |  A fájl vagy mappa a felhasználók férjenek hozzá a neve. |
| SourceRelativeUrl | A mappa, amelyben a felhasználók férjenek hozzá a fájl URL-CÍMÉT. A SiteURL SourceRelativeURL és SourceFileName paraméterek érték megegyezik a ObjectID tulajdonság, amely a fájl, a felhasználók férjenek hozzá a teljes elérési út értéke. |
| UserSharedWith |  A felhasználó a megosztott erőforrás. |




## <a name="sample-log-searches"></a>Naplókeresési minták
A következő táblázat a megoldás által összegyűjtött frissítési rekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

| Lekérdezés | Leírás |
| --- | --- |
|Az Office 365-előfizetés az összes műveletek száma |OfficeActivity &#124; művelet count() összefoglalója |
|SharePoint-webhelyek használata|OfficeActivity &#124; ahol OfficeWorkload = ~ "sharepoint" &#124; által SiteUrl count() összefoglalója | Rendezze a Count asc|
|Hozzáférés fájlműveletek felhasználói típus szerint|Keresés helye (OfficeActivity) OfficeWorkload = ~ "azureactivedirectory" és "MyTest"|
|Egy adott kulcsszóval keresése|Típus OfficeActivity OfficeWorkload = "MyTest" azureactivedirectory =|
|A figyelő külső műveletek Exchange|OfficeActivity &#124; ahol OfficeWorkload = ~ ExternalAccess és az "exchange" == true|



## <a name="next-steps"></a>További lépések
* A részletes frissítési adatokat a [Log Analytics](../log-analytics/log-analytics-log-searches.md) Naplókeresés funkciójával is megtekintheti.
* [Hozzon létre egy saját irányítópultok](../log-analytics/log-analytics-dashboards.md) kedvenc Office 365 keresési lekérdezések megjelenítéséhez.
* [Hozzon létre a riasztások](../log-analytics/log-analytics-alerts.md) proaktív értesíti a fontos Office 365-tevékenységek.  
