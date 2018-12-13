---
title: Az Office 365 felügyeleti megoldás az Azure-ban |} A Microsoft Docs
description: Ez a cikk részletesen konfigurációját és használatát az Office 365-megoldás az Azure-ban.  Az Office 365 rekordok létrehozott Log Analytics részletes leírását tartalmazza.
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
ms.date: 08/15/2018
ms.author: bwren
ms.openlocfilehash: 1e819f819561c5fac722ca6954a10d80a482b5cd
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52889361"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Az Office 365 felügyeleti megoldás az Azure-ban (előzetes verzió)

![Office 365-embléma](media/solution-office-365/icon.png)

Az Office 365 felügyeleti megoldás a Log Analytics az Office 365-környezete figyelését teszi lehetővé.

- Az Office 365-fiókkal, a használati minták elemzése, valamint viselkedési trendek azonosítása a felhasználói tevékenység monitorozása. Ha például is kibonthat egy konkrét használati forgatókönyvek, például a szervezet vagy a legnépszerűbb SharePoint-webhelyek kívül megosztott fájlok.
- Konfigurációs módosítások vagy magas jogosultságú műveleteket nyomon követéséhez rendszergazdai tevékenységek figyelése.
- Észlelése és vizsgálata a nem kívánt felhasználói viselkedés, amely a szervezet igényeinek megfelelően testre szabható.
- Naplózási és megfelelőségi bemutatása. Figyelheti például fájl bizalmas fájlokat, amelyek segítségére lehet a naplózást és megfelelőségi folyamat a hozzáférési műveleteket.
- Az operatív hibaelhárítás elvégzése érdekében [naplókereséseket](../../azure-monitor/log-query/log-query-overview.md) a szervezet az Office 365 adatok felett.

## <a name="prerequisites"></a>Előfeltételek
A következő kötelező végezniük a megoldás telepítve és konfigurálva.

- Szervezeti Office 365-előfizetéssel.
- Egy felhasználói fiókot, amely globális rendszergazda hitelesítő adatait.
- Naplózási adatok fogadására, kell [naplózás konfigurálása](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) az Office 365-előfizetéssel.  Vegye figyelembe, hogy [postaláda-naplózás](https://technet.microsoft.com/library/dn879651.aspx) külön van konfigurálva.  Továbbra is a megoldás telepítése és egyéb adatok gyűjtése, ha naplózás nincs konfigurálva.
 

## <a name="management-packs"></a>Felügyeleti csomagok
Ez a megoldás nem telepíti a minden felügyeleti csomagot [csatlakoztatott felügyeleti csoportok](../../azure-monitor/platform/om-agents.md).
  
## <a name="install-and-configure"></a>Telepítés és konfigurálás
Először adja hozzá a [az előfizetés az Office 365-megoldás](solutions.md#install-a-management-solution). Miután hozzáadta, ebben a szakaszban neki hozzáférést az Office 365-előfizetéssel, hajtsa végre a konfigurációs lépéseket.

### <a name="required-information"></a>Szükséges információk
Ez az eljárás megkezdése előtt gyűjtse össze a következő információkat.

A Log Analytics-munkaterület:

- Munkaterület neve: A munkaterület az Office 365-adatok hova legyenek összegyűjtve.
- Erőforráscsoport neve: az erőforráscsoport, amely tartalmazza a munkaterületen.
- Azure-előfizetés azonosítója: az a munkaterületet tartalmazó előfizetés.

Az Office 365-előfizetés:

- Felhasználónév: E-mail-cím egy rendszergazdai fiók.
- Bérlőazonosító: Egyedi azonosítója az Office 365-előfizetéssel.
- Ügyfél-azonosító: 16 karakter hosszúságú karakterlánc, amely az Office 365-ügyfél jelöli.
- Titkos Ügyfélkód: Titkosított karakterlánc-hitelesítéshez szükséges.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Az Office 365-alkalmazás létrehozása az Azure Active Directoryban
Alkalmazás létrehozása az Azure Active Directoryban, amely a felügyeleti megoldás használatával fogja elérni az Office 365-megoldás első lépéseként.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) címen.
1. Válassza ki **Azure Active Directory** , majd **alkalmazásregisztrációk**.
1. Kattintson az **Új alkalmazásregisztráció** elemre.

    ![Alkalmazásregisztráció hozzáadása](media/solution-office-365/add-app-registration.png)
1. Adja meg az alkalmazás **neve** és **bejelentkezési URL-**.  A név legyen leíró.  Használat _http://localhost_ URL-címet, és tarthatja _webalkalmazás / API_ a a **alkalmazástípus**
    
    ![Alkalmazás létrehozása](media/solution-office-365/create-application.png)
1. Kattintson a **létrehozás** , és ellenőrizze az alkalmazás adatait.

    ![Regisztrált alkalmazás](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Office 365-höz készült alkalmazás konfigurálása

1. Kattintson a **beállítások** megnyitásához a **beállítások** menü.
1. Válassza ki **tulajdonságok**. Változás **több-bérlős** való _Igen_.

    ![Több-bérlős beállításai](media/solution-office-365/settings-multitenant.png)

1. Válassza ki **szükséges engedélyek** a a **beállítások** menüben, majd kattintson **Hozzáadás**.
1. Kattintson a **API kiválasztása** , majd **az Office 365 felügyeleti API-k**. Kattintson a **Office 365 felügyeleti API-k**. Kattintson a **Kiválasztás** gombra.

    ![API kiválasztása](media/solution-office-365/select-api.png)

1. A **engedélyek kiválasztása** válassza ki a következő beállításokat is **Alkalmazásengedélyek** és **delegált engedélyek**:
    - A munkahelyhez tartozó szolgáltatásállapot-adatok olvasása
    - A munkahelyi tevékenységadatok olvasása
    - A munkahelyre vonatkozó tevékenységjelentések olvasása

    ![API kiválasztása](media/solution-office-365/select-permissions.png)

1. Kattintson a **kiválasztása** , majd **kész**.
1. Kattintson a **engedélyeket** majd **Igen** az ellenőrző kérdés.

    ![Engedélyek megadása](media/solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Az alkalmazás kulcs hozzáadása

1. Válassza ki **kulcsok** a a **beállítások** menü.
1. Írjon be egy **leírás** és **időtartama** az új kulcs.
1. Kattintson a **mentése** , majd másolja a **érték** , amely akkor jön létre.

    ![Kulcsok](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Rendszergazdai jóváhagyás hozzáadása
A rendszergazdai fiók engedélyezéséhez először, rendszergazdai jóváhagyás kell adnia az alkalmazáshoz. Ehhez egy PowerShell-parancsprogrammal. 

1. Mentse a következő szkriptet, *office365_consent.ps1*.

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
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
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
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
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

1. Megjelenik egy ablak, az alábbihoz hasonló. Kattintson a **fogadja el**.
    
    ![Rendszergazdai jóváhagyás](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Log Analytics-munkaterület előfizetés
Az utolsó lépés, hogy az alkalmazás a Log Analytics-munkaterület előfizetés. Azt is megteheti egy PowerShell-parancsprogrammal.

1. Mentse a következő szkriptet, *office365_subscription.ps1*.

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
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.Sharepoint'
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

### <a name="troubleshooting"></a>Hibaelhárítás

Ha megpróbál létrehozni egy előfizetés után az előfizetés már létezik a következő hiba jelenhet meg.

```
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Ha érvénytelen paraméterértékeket vannak megadva a következő hiba jelenhet meg.

```
Select-AzureRmSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzureRmSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureRmContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzureRMContextCommand

```

## <a name="uninstall"></a>Eltávolítás
Az Office 365 felügyeleti megoldás részben ismertetett eljárással eltávolíthatja [távolítsa el a felügyeleti megoldás](solutions.md#remove-a-management-solution). A begyűjtött adatok Office 365-ből azokat a Log Analytics azonban nem leáll. Mondja le az Office 365 és az adatgyűjtés leállításához az alábbi eljárást követve.

1. Mentse a következő szkriptet, *office365_unsubscribe.ps1*.

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
Az Office 365-megoldás nem adatlekéréshez bármelyikét a [Log Analytics-ügynökök](../../azure-monitor/platform/agent-data-sources.md).  Lekéri az adatokat közvetlenül az Office 365-höz.

### <a name="collection-frequency"></a>A gyűjtés gyakorisága
Kezdetben gyűjtendő adatokat, több óráig is eltarthat. Miután gyűjtése kezdődik, az Office 365 küld egy [webhook értesítési](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) részletes adatokat a Log Analyticshez való minden alkalommal, amikor létrejön egy rekord. Ez a rekord fogadását követően néhány percen belül a Log Analytics érhető el.

## <a name="using-the-solution"></a>A megoldás használata
A Log Analytics-munkaterületet, az Office 365-megoldás hozzáadásakor az **Office 365** csempe felkerül az irányítópulton. Ez a csempe a környezetben jelenleg elérhető számítógépek számát és grafikus ábrázolását jeleníti meg, valamint a frissítési megfelelőségi állapotukat.<br><br>
![Az Office 365-összefoglaló csempe](media/solution-office-365/tile.png)  

Kattintson a **Office 365** csempére kattintva nyissa meg a **Office 365** irányítópult.

![Az Office 365-irányítópult](media/solution-office-365/dashboard.png)  

Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Mindegyik oszlop felsorolja a 10 leggyakoribb riasztások a megadott hatóköri és időtartományi az adott oszlop feltételeknek megfelelő száma szerint. Naplókeresést, amely biztosítja a teljes listát lásd: kattintva az összes oszlop alján, vagy az oszlop fejlécére kattintva futtathatja.

| Oszlop | Leírás |
|:--|:--|
| Műveletek | Az aktív felhasználók az összes figyelt Office 365-előfizetések információkat biztosít. Azt is láthatja, hogy idővel történik a tevékenységek számát.
| Exchange | Az Exchange Server-tevékenységek, például az Add-postaláda engedély, vagy a Set-postaláda bontását mutatja. |
| SharePoint | A felső tevékenységek jeleníti meg, hogy a felhasználók a SharePoint-dokumentumok hajtható végre. Amikor részletezi az erre a csempére, a keresési lap ezeket a tevékenységeket, például a céldokumentumban és helyét, ez a tevékenység részleteit jeleníti meg. Például egy fájl elérhető esemény fogja látni a dokumentumot, amelyek hozzáférnek, a társított fiók neve és IP-címet. |
| Azure Active Directory | Leggyakoribb felhasználói tevékenységek, például a felhasználói jelszó alaphelyzetbe állítása és a bejelentkezési kísérletek tartalmazza. Amikor részletezi, lesz ezeket a tevékenységeket, például az eredmény állapot részleteinek megtekintéséhez. Ez a leginkább hasznos, ha szeretné figyelni a gyanús tevékenységek az Azure Active Directoryban. |




## <a name="log-analytics-records"></a>Log Analytics-rekordok

A Log Analytics-munkaterületet az Office 365-megoldás által létrehozott összes rekordok egy **típus** , **OfficeActivity**.  A **OfficeWorkload** tulajdonság határozza meg, melyik a rekord hivatkozik – Exchange, az AzureActiveDirectory, a SharePoint vagy a onedrive vállalati verzió az Office 365 szolgáltatás.  A **RecordType** tulajdonság határozza meg a művelet típusát.  A tulajdonságok az egyes művelet eltérőek, és az alábbi táblázatban láthatók.

### <a name="common-properties"></a>Közös tulajdonságok
A következő tulajdonságok megegyeznek az összes Office 365-rekord.

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus | *OfficeActivity* |
| ClientIP | A tevékenység naplózásakor használt eszköz IP-címét. Az IP-cím IPv4 vagy IPv6 cím formátumban jelenik meg. |
| OfficeWorkload | Az Office 365 szolgáltatás, amely a rekord hivatkozik.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Művelet | A felhasználói vagy rendszergazdai tevékenység neve.  |
| A szervezeti | A szervezet Office 365-bérlőhöz tartozó GUID azonosítója. Ez az érték mindig lesz azonos, a szervezete számára, függetlenül az Office 365 szolgáltatás, amelyben történik. |
| RecordType | Végrehajtott művelet típusát. |
| ResultStatus | Azt jelzi, hogy a (művelet tulajdonságban megadott) művelet sikeres volt-e vagy sem. Lehetséges értékek: Succeeded, PartiallySucceded vagy sikertelen. Az Exchange-rendszergazdai tevékenységhez, értéke pedig IGAZ vagy hamis. |
| Felhasználói azonosító | A rekordnaplózást eredményező naplózott; műveletet végrehajtó felhasználó egyszerű Felhasználóneve (egyszerű felhasználónév) Ha például my_name@my_domain_name. Vegye figyelembe, hogy a Rendszerfiókok (például a SHAREPOINT\system vagy NTAUTHORITY\SYSTEM) által végrehajtott tevékenységek rekordjai is szerepelnek. | 
| UserKey | Egy alternatív Azonosítót a felhasználó a UserId tulajdonság azonosítja.  Például ez a tulajdonság megjelenik a passport egyedi azonosító (PUID), és az Exchange a SharePoint, a onedrive-on a felhasználók által végrehajtott eseményeket. Ez a tulajdonság is megadható ugyanazt az értéket a UserID tulajdonság számára a más szolgáltatások és a rendszer fiókok által végrehajtott eseményeket bekövetkező események|
| UserType | A műveletet végrehajtó felhasználó típusa.<br><br>Adminisztratív körzet<br>Alkalmazás<br>DcAdmin<br>Rendszeres<br>Foglalt<br>ServicePrincipal<br>Rendszer |


### <a name="azure-active-directory-base"></a>Az Azure Active Directory alap
A következő tulajdonságok megegyeznek az összes Azure Active Directory-rekordok.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Az Azure AD-esemény típusa. |
| ExtendedProperties | Az Azure ad-ben eseményt további tulajdonságait. |


### <a name="azure-active-directory-account-logon"></a>Az Azure Active Directory-fiók bejelentkezési
Az Active Directory-felhasználó megpróbál bejelentkezni, ezeket a rekordokat hoz létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Alkalmazás | Az alkalmazás, amely a fiók bejelentkezési esemény, például az Office 15. |
| Ügyfél | Az ügyfél adatainak eszköz-, eszköz operációs rendszere és a használt eszköz böngésző az a fiók bejelentkezési esemény. |
| LoginStatus | Ez a tulajdonság közvetlenül OrgIdLogon.LoginStatus származik. Különféle érdekes bejelentkezési hibák hozzárendelése sikerült végzi a riasztás algoritmusokat. |
| UserDomain | A bérlői azonosító adatok (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Ezek a rekordok módosítása vagy hozzáadása az Azure Active Directory-objektumok végrehajtott hoz létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | A felhasználó, amely a (művelet tulajdonság által azonosított) műveletet hajtott végre. |
| Aktor | A felhasználó vagy szolgáltatás, amely a műveletet. |
| ActorContextId | A szervezet, amely tagja az aktor GUID Azonosítóját. |
| ActorIpAddress | Az aktor IP-cím IPV4 vagy IPv6-cím formátumú. |
| InterSystemsId | A GUID, a műveletek nyomon követése a különböző összetevők az Office 365 szolgáltatásban. |
| IntraSystemId |   Az Azure Active Directory-nyomon követheti a művelet által létrehozott GUID. |
| SupportTicketId | Az ügyfélszolgálati Jegyazonosító "act" helyzetekben a művelethez. |
| TargetContextId | A GUID azonosítóját a szervezet, amely a megcélzott felhasználó tartozik. |


### <a name="data-center-security"></a>Data Center Adatbiztonság
Ezeket a rekordokat Data Center biztonsági naplózási adatok alapján jönnek létre.  

| Tulajdonság | Leírás |
|:--- |:--- |
| EffectiveOrganization | A jogosultságszint-emelési parancsmag érintett, a bérlő nevével. |
| ElevationApprovedTime | Ha a jogosultságszint-emelés jóváhagyása jelölő időbélyegző. |
| ElevationApprover | A Microsoft vezető neve. |
| ElevationDuration | Az időtartam, amelynek a jogosultságszint-emelési volt aktív. |
| ElevationRequestId |  A jogosultságszint-emelési kérés egyedi azonosítója. |
| ElevationRole | A szerepkör a jogosultságszint-emelési szükséges. |
| ElevationTime | A jogosultságszint-emelési kezdési idejét. |
| Start_Time | A parancsmag végrehajtásának kezdete. |


### <a name="exchange-admin"></a>Az Exchange-rendszergazda
Ezeket a rekordokat jönnek létre, amikor a módosítások Exchange-konfiguráció.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Itt adhatja meg, hogy a parancsmag futott-e egy felhasználó a szervezetben, a Microsoft datacenter ezért felelős munkatársai vagy datacenter szolgáltatásfiók vagy a meghatalmazott rendszergazda. A hamis értéket, az azt jelzi, hogy a parancsmag futott-e valaki a szervezetben. Igaz érték azt jelzi, hogy a parancsmag adatközpont csoporthoz, az Adatközpont szolgáltatásfiók vagy a meghatalmazott rendszergazda által futtatott. |
| ModifiedObjectResolvedName |  Ez az az objektum, a parancsmag által módosított felhasználóbarát neve. Ez a rendszer csak akkor, ha a parancsmag módosítja az objektum naplózza. |
| Cégnév | A bérlő nevével. |
| OriginatingServer | A kiszolgáló, amelyről a rendszer a parancsmagot hajtott végre neve. |
| Paraméterek | A név és a parancsmaggal a műveletek tulajdonságban azonosított használt összes paraméter értékét. |


### <a name="exchange-mailbox"></a>Exchange-postaláda
Ezek a rekordok módosítása vagy bővítése végrehajtott Exchange-postaládák hoz létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Az e-mail-ügyfél, például egy böngésző verziója, az Outlook verziója és a mobileszköz-információk a művelet végrehajtásához használt információk. |
| Client_IPAddress | A művelet naplózásakor használt eszköz IP-címét. Az IP-cím IPv4 vagy IPv6 cím formátumban jelenik meg. |
| ClientMachineName | A gép nevét, amelyen az Outlook ügyfélnek. |
| ClientProcessName | A postaláda eléréséhez használt e-mail-ügyfél. |
| ClientVersion | Az e-mail-ügyfél verziója. |
| InternalLogonType | Belső használatra fenntartva. |
| Logon_Type | A postaláda érhető el, és a naplózott műveletet végre felhasználó típusát jelzi. |
| LogonUserDisplayName |    A műveletet végrehajtó felhasználó felhasználóbarát neve. |
| LogonUserSid | A műveletet végrehajtó felhasználó SID. |
| MailboxGuid | Az Exchange GUID fértek hozzá postaláda. |
| MailboxOwnerMasterAccountSid | Postaláda-tulajdonos a fő fiók biztonsági azonosítója. |
| MailboxOwnerSid | A postaláda-tulajdonos biztonsági azonosítója. |
| MailboxOwnerUPN | A postaláda, amely fértek hozzá tulajdonosának e-mail címe. |


### <a name="exchange-mailbox-audit"></a>Exchange postaláda-naplózás
Ezeket a rekordokat egy postaláda naplóbejegyzést létrehozásakor jönnek létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Elem | Az elem, amelyre hajtottak végre a műveletet jelenti. | 
| SendAsUserMailboxGuid | A postaláda, küldjön e-maileket elért Exchange GUID azonosítója. |
| SendAsUserSmtp | SMTP-cím megszemélyesített felhasználó. |
| SendonBehalfOfUserMailboxGuid | A postaláda nevében e-mailek küldésére elért Exchange GUID azonosítója. |
| SendOnBehalfOfUserSmtp | SMTP-címét a felhasználó, akinek a nevében az e-mailt küld. |


### <a name="exchange-mailbox-audit-group"></a>Exchange postaláda-naplózás csoport
Ezek a rekordok módosítása vagy bővítése végrehajtott Exchange-csoportok jönnek létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Információ arról, hogy a csoport összes elemére. |
| CrossMailboxOperations | Azt jelzi, ha a művelet több postaláda vesz-e. |
| DestMailboxId | Állítsa be, csak akkor, ha a CrossMailboxOperations paraméter értéke True. Adja meg a céloldali postaládához GUID. |
| DestMailboxOwnerMasterAccountSid | Állítsa be, csak akkor, ha a CrossMailboxOperations paraméter értéke True. Meghatározza a fő fiók SID azonosítója a target postaládájának tulajdonosa biztonsági AZONOSÍTÓJÁNAK. |
| DestMailboxOwnerSid | Állítsa be, csak akkor, ha a CrossMailboxOperations paraméter értéke True. Adja meg a céloldali postaládához biztonsági azonosítója. |
| DestMailboxOwnerUPN | Állítsa be, csak akkor, ha a CrossMailboxOperations paraméter értéke True. Adja meg a céloldali postaládához tulajdonosának Felhasználóneve. |
| DestFolder | A célmappa, és áthelyezés hasonló műveletek céljából. |
| Mappa | A mappa, ahol elemek csoportja megtalálható. |
| Mappák |     A forrás egy műveletben; érintett mappák kapcsolatos információk Ha például mappák kiválasztva, és ezután törölni. |


### <a name="sharepoint-base"></a>A SharePoint alapja
Ezek a Tulajdonságok megegyeznek az összes SharePoint-rekordok.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Meghatározza, hogy az esemény történt-e a Sharepointban. Lehetséges értékek a következők: a SharePoint vagy ObjectModel. |
| ItemType | A típusú objektum, amely lett vagy nem módosítottak. Részletekért tekintse meg a ItemType táblázatot az azon objektumok típusait. |
| MachineDomainInfo | Eszköz szinkronizálási műveletek vonatkozó adatokat. Ez az információ csak akkor, ha a kérelemben szereplő jelentett. |
| Gépazonosító |   Eszköz szinkronizálási műveletek vonatkozó adatokat. Ez az információ csak akkor, ha a kérelemben szereplő jelentett. |
| Site_ | A hely, ahol a fájl vagy mappa a felhasználó által hozzáfért megtalálható GUID Azonosítóját. |
| Source_Name | Az entitás, amely kiváltotta a naplózott műveletet. Lehetséges értékek a következők: a SharePoint vagy ObjectModel. |
| UserAgent | A felhasználó ügyfél vagy a böngésző vonatkozó adatokat. Ez az információ az ügyfél vagy a böngésző által biztosított. |


### <a name="sharepoint-schema"></a>A SharePoint-séma
Konfigurációs módosítások a SharePoint ezeket a rekordokat hoz létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Egyéni események karakterlánca nem kötelező. |
| Event_Data |  Egyéni események választható adattartalma. |
| ModifiedProperties | A tulajdonság tartalmazza a felügyeleti események, mint például a felhasználó hozzáadása egy webhelyhez vagy a hely gyűjtemény rendszergazdai csoport tagjaként. A tulajdonság tartalmazza (például a hely felügyeleti csoport), a módosított (például a felhasználó hely rendszergazdaként lett hozzáadva) tulajdonság új értéke pedig az előző értéket a módosított objektum módosított tulajdonság nevére. |


### <a name="sharepoint-file-operations"></a>A SharePoint-fájl
Ezeket a rekordokat a Sharepointban fájlműveletek válaszul jönnek létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | A fájl kiterjesztése egy fájlt másol vagy áthelyezték. Ez a tulajdonság csak FileCopied és FileMoved esemény jelenik meg. |
| DestinationFileName | Neve a fájlt, másolja vagy áthelyezték. Ez a tulajdonság csak FileCopied és FileMoved esemény jelenik meg. |
| DestinationRelativeUrl | Az URL-címét a rendeltetési mappára, ahol egy fájlt, másolja vagy áthelyezték. SiteURL DestinationRelativeURL és DestinationFileName paraméterek értékei kombinációja megegyezik az érték ObjectID tulajdonság, amely az átmásolt fájl teljes elérési útja neve. Ez a tulajdonság csak FileCopied és FileMoved esemény jelenik meg. |
| SharingType | A megosztási engedélyeket a felhasználót, hogy megosztották az erőforráshoz rendelt típusát. Ez a felhasználó a UserSharedWith paraméter által azonosított. |
| Site_Url | A hely, ahol a fájl vagy mappa a felhasználó által hozzáfért az URL-címe |
| SourceFileExtension | A fájl kiterjesztése, a fájl, amely a felhasználó hozzáfért. Ez a tulajdonság üresen, ha az objektum, amely fértek hozzá egy mappában. |
| SourceFileName |  A fájl vagy a felhasználó által hozzáfért mappa neve. |
| SourceRelativeUrl | Az URL-címét a felhasználó által hozzáfért a fájlt tartalmazó mappát. A SiteURL SourceRelativeURL és SourceFileName paraméterek értékei kombinációja megegyezik az érték ObjectID tulajdonság, amely a felhasználó által hozzáfért fájl teljes elérési útja neve. |
| UserSharedWith |  A felhasználó a megosztott erőforrás. |




## <a name="sample-log-searches"></a>Naplókeresési minták
A következő táblázat a megoldás által összegyűjtött frissítési rekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

| Lekérdezés | Leírás |
| --- | --- |
|Az Office 365-előfizetéshez a műveletek száma |OfficeActivity &#124; művelet count() összegzése |
|SharePoint-webhelyek használatát|OfficeActivity &#124; ahol OfficeWorkload = ~ "sharepoint" &#124; count() by SiteUrl összefoglalója | Rendezés növekvő száma|
|Felhasználó típusa fájl hozzáférés-műveletek|Keresés a (OfficeActivity) OfficeWorkload = ~ "azureactivedirectory" és "MyTest"|
|Egy adott kulcsszót tartalmazó keresése|Típus OfficeActivity OfficeWorkload = "MyTest" azureactivedirectory =|
|Az Exchange külső műveletek figyelése|OfficeActivity &#124; ahol OfficeWorkload = ~ "Exchange-hez" és a ExternalAccess igaz ==|



## <a name="next-steps"></a>További lépések
* A részletes frissítési adatokat a [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) Naplókeresés funkciójával is megtekintheti.
* [Saját irányítópult létrehozásával](../../azure-monitor/platform/dashboards.md) kedvenc Office 365 keresési lekérdezések megjelenítéséhez.
* [Riasztások létrehozása](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) proaktívan értesíti a fontos Office 365-tevékenységek.  
