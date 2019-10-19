---
title: Office 365 felügyeleti megoldás az Azure-ban | Microsoft Docs
description: Ez a cikk részletesen ismerteti az Office 365-megoldás konfigurálását és használatát az Azure-ban.  A Azure Monitorban létrehozott Office 365-rekordok részletes leírását tartalmazza.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 032d52961b4867cad94d06802adb0a1f3eb00f5f
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553950"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Office 365 felügyeleti megoldás az Azure-ban (előzetes verzió)

![Office 365 embléma](media/solution-office-365/icon.png)


> [!NOTE]
> Az Office 365-megoldás telepítéséhez és konfigurálásához ajánlott módszer az [office 365-összekötő](../../sentinel/connect-office-365.md) engedélyezése az [Azure sentinelben](../../sentinel/overview.md) a jelen cikk lépéseinek használata helyett. Ez az Office 365-megoldás frissített verziója, amely továbbfejlesztett konfigurációs felülettel rendelkezik. Az Azure AD-naplók összekapcsolásához használhatja az [Azure Sentinel Azure ad-összekötőt](../../sentinel/connect-azure-active-directory.md) , vagy [konfigurálhatja az Azure ad diagnosztikai beállításait](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), amely gazdagabb naplófájlokat biztosít, mint az Office 365 felügyeleti naplói. 
>
> Az [Azure Sentinel](../../sentinel/quickstart-onboard.md)bevezetéséhez válassza ki azt a log Analytics munkaterületet, amelyre telepíteni kívánja az Office 365-megoldást. Miután engedélyezte az összekötőt, a megoldás elérhető lesz a munkaterületen, és pontosan ugyanazok lesznek, mint a telepített többi figyelési megoldás.
>
> Az Azure Government Cloud felhasználóinak telepíteniük kell az Office 365-et a jelen cikkben ismertetett lépések végrehajtásával, mivel az Azure Sentinel még nem érhető el a kormányzati felhőben.

Az Office 365 felügyeleti megoldás lehetővé teszi az Office 365-környezet figyelését Azure Monitorban.

- Az Office 365-fiókok felhasználói tevékenységeinek monitorozásával elemezheti a használati mintákat, valamint azonosíthatja a viselkedési trendeket. Kinyerheti például bizonyos használati forgatókönyveket, például a szervezeten kívül megosztott fájlokat vagy a legnépszerűbb SharePoint-webhelyeket.
- Rendszergazdai tevékenységek figyelése a konfigurációs változások és a magas jogosultsági szintű műveletek nyomon követéséhez.
- A nem kívánt felhasználói viselkedés észlelése és vizsgálata, amely testreszabható a szervezeti igényeknek megfelelően.
- Auditálás és megfelelőség bemutatása. Például figyelheti a fájl-hozzáférési műveleteket a bizalmas fájlokon, ami segíthet a naplózási és megfelelőségi folyamatokban.
- Hajtsa végre a működés közbeni hibaelhárítást a szervezete Office 365-tevékenység adataihoz tartozó [lekérdezések](../log-query/log-query-overview.md) használatával.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

A megoldás telepítése és konfigurálása előtt a következőkre van szükség.

- Szervezeti Office 365-előfizetés.
- Egy globális rendszergazda felhasználói fiók hitelesítő adatai.
- A naplózási adatai fogadásához [konfigurálnia kell a naplózást](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) az Office 365-előfizetésben.  Vegye figyelembe, hogy a [postaláda-naplózás](https://technet.microsoft.com/library/dn879651.aspx) külön van konfigurálva.  Továbbra is telepítheti a megoldást, és más adatokat is gyűjthet, ha a naplózás nincs konfigurálva.
 

## <a name="management-packs"></a>Felügyeleti csomagok

Ez a megoldás nem telepít felügyeleti csomagokat a [csatlakoztatott felügyeleti csoportokban](../platform/om-agents.md).
  

## <a name="install-and-configure"></a>Telepítés és konfigurálás

Először adja hozzá az [Office 365-megoldást az előfizetéséhez](solutions.md#install-a-monitoring-solution). A hozzáadást követően el kell végeznie a konfigurációs lépéseket ebben a szakaszban, hogy hozzáférést biztosítson az Office 365-előfizetéséhez.

### <a name="required-information"></a>Szükséges információk

Az eljárás megkezdése előtt Gyűjtse össze a következő információkat.

Az Log Analytics munkaterületről:

- Munkaterület neve: az Office 365-adatokat összegyűjtő munkaterület.
- Erőforráscsoport neve: a munkaterületet tartalmazó erőforráscsoport.
- Azure-előfizetés azonosítója: a munkaterületet tartalmazó előfizetés.

Az Office 365-előfizetésből:

- Username: rendszergazdai fiók e-mail-címe.
- Bérlő azonosítója: az Office 365-előfizetés egyedi azonosítója.
- Ügyfél-azonosító: 16 karakterből álló karakterlánc, amely az Office 365-ügyfelet jelöli.
- Ügyfél titka: a hitelesítéshez szükséges titkosított karakterlánc.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Office 365-alkalmazás létrehozása Azure Active Directory

Első lépésként létre kell hoznia egy alkalmazást Azure Active Directory, amelyet a felügyeleti megoldás az Office 365-megoldás elérésére fog használni.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) címen.
1. Válassza ki **Azure Active Directory** majd **Alkalmazásregisztrációk**.
1. Kattintson az **új regisztráció**elemre.

    ![Alkalmazás regisztrációjának hozzáadása](media/solution-office-365/add-app-registration.png)
1. Adja meg az alkalmazás **nevét**. Válassza a **fiókok lehetőséget bármely szervezeti címtárban (bármely Azure ad-címtár – több-bérlő)** a **támogatott fiókok típusaihoz**.
    
    ![Alkalmazás létrehozása](media/solution-office-365/create-application.png)
1. Kattintson a **regisztrálás** gombra, és ellenőrizze az alkalmazás adatait.

    ![Regisztrált alkalmazás](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Az Office 365 alkalmazás konfigurálása

1. Válassza a **hitelesítés** lehetőséget, és győződjön meg arról, hogy a **fiókok bármelyik szervezeti címtárban (bármely Azure ad-címtár – több-bérlő)** a **támogatott fióktípus**területen van kiválasztva.

    ![Beállítások több-bérlős](media/solution-office-365/settings-multitenant.png)

1. Válassza az **API-engedélyek** lehetőséget, majd **adjon hozzá egy engedélyt**.
1. Kattintson az **Office 365 felügyeleti API**-k elemre. 

    ![API kiválasztása](media/solution-office-365/select-api.png)

1. **Milyen típusú engedélyek szükségesek az alkalmazáshoz?** az **alkalmazás engedélyei** és a **delegált engedélyek**esetében válassza az alábbi beállításokat:
   - A szervezethez tartozó szolgáltatás állapotával kapcsolatos információk olvasása
   - A szervezet tevékenységi adatai olvasása
   - Tevékenységgel kapcsolatos jelentések olvasása a szervezet számára

     ![API kiválasztása](media/solution-office-365/select-permissions-01.png)![API kiválasztása](media/solution-office-365/select-permissions-02.png)

1. Kattintson az **engedélyek hozzáadása**lehetőségre.
1. Kattintson a **rendszergazdai jóváhagyás megadása** lehetőségre, majd kattintson az **Igen** gombra, amikor a rendszer ellenőrzést kér.


### <a name="add-a-secret-for-the-application"></a>Titkos kód hozzáadása az alkalmazáshoz

1. Válassza a **tanúsítványok & titkok** , majd az **új ügyfél titka**lehetőséget.

    ![Kulcsok](media/solution-office-365/secret.png)
 
1. Adja meg az új kulcs **leírását** és **időtartamát** .
1. Kattintson a **Hozzáadás** elemre, majd másolja ki a generált **értéket** .

    ![Kulcsok](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Rendszergazdai engedély hozzáadása

A rendszergazdai fiók első engedélyezéséhez meg kell adnia az alkalmazáshoz való rendszergazdai beleegyezett. Ezt egy PowerShell-parancsfájllal teheti meg. 

1. Mentse a következő parancsfájlt *office365_consent. ps1*néven.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
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

2. Futtassa a szkriptet a következő paranccsal. A rendszer kétszer kéri a hitelesítő adatok megadását. Először adja meg a Log Analytics munkaterület hitelesítő adatait, majd az Office 365-bérlő globális rendszergazdai hitelesítő adatait.

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    Példa:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Az alábbihoz hasonló ablak jelenik meg. Kattintson az **elfogadás**gombra.
    
    ![Rendszergazdai jóváhagyás](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Előfizetés Log Analytics munkaterületre

Az utolsó lépés az alkalmazás Log Analytics munkaterületre való előfizetése. Ezt egy PowerShell-parancsfájllal is elvégezheti.

1. Mentse a következő parancsfájlt *office365_subscription. ps1*néven.

    ```powershell
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
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
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
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
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

2. Futtassa a szkriptet a következő paranccsal:

    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```

    Példa:

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>Hibakeresés

Ha az alkalmazás már előfizetett erre a munkaterületre, vagy ha a bérlő egy másik munkaterületre van előfizetve, a következő hibaüzenet jelenhet meg:

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Ha érvénytelen paraméterérték van megadva, a következő hibaüzenet jelenhet meg:

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Eltávolítás

Az Office 365 felügyeleti megoldás eltávolításához használja a [felügyeleti megoldás eltávolítása](solutions.md#remove-a-monitoring-solution)című eljárást. Ez nem állítja le az Office 365-ből gyűjtött adatokat a Azure Monitor mégis. Kövesse az alábbi eljárást az Office 365 lemondásához és az adatok gyűjtésének leállításához.

1. Mentse a következő parancsfájlt *office365_unsubscribe. ps1*néven.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
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

2. Futtassa a szkriptet a következő paranccsal:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Példa:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Adatgyűjtés

### <a name="supported-agents"></a>Támogatott ügynökök

Az Office 365-megoldás nem kéri le az összes [log Analytics ügynök](../platform/agent-data-sources.md)adatait.  Az adatok közvetlenül az Office 365-ből kérhetők le.

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

Az adatok kezdetben való gyűjtése igénybe vehet néhány órát. A gyűjtés megkezdése után az Office 365 egy [webhook-értesítést](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) küld a részletes adatokkal, hogy Azure monitor minden alkalommal, amikor létrejön egy rekord. Ez a rekord a kézhezvételt követően néhány percen belül Azure Monitor érhető el.

## <a name="using-the-solution"></a>A megoldás használata

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Amikor hozzáadja az Office 365-megoldást a Log Analytics munkaterülethez, az **office 365** csempe hozzá lesz adva az irányítópulthoz. Ez a csempe a környezetben jelenleg elérhető számítógépek számát és grafikus ábrázolását jeleníti meg, valamint a frissítési megfelelőségi állapotukat.<br><br>
![Office 365 összefoglaló csempe ](media/solution-office-365/tile.png)  

Kattintson az **office 365** csempére az **Office 365** irányítópultjának megnyitásához.

![Office 365 irányítópult](media/solution-office-365/dashboard.png)  

Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Minden oszlop felsorolja az első tíz riasztást az oszlopnak a megadott hatókörhöz és időtartományhoz tartozó feltételeinek megfelelő számokkal. Futtathat egy naplóbeli keresést, amely a teljes listát az oszlop alján található az összes megjelenítése elemre kattintva vagy az oszlop fejlécére kattintva jeleníti meg.

| Column | Leírás |
|:--|:--|
| Műveletek | Az összes figyelt Office 365-előfizetés aktív felhasználóival kapcsolatos információkat nyújt. Megtekintheti az idő múlásával zajló tevékenységek számát is.
| Adatcsere | Megjeleníti az Exchange Server-tevékenységek részletezését, például a Add-Mailbox engedélyt vagy a set-Mailbox műveletet. |
| SharePoint | Megjeleníti a felhasználók által a SharePoint-dokumentumokon végrehajtott legfontosabb tevékenységeket. Ha ezen a csempén végez részletezést, a Keresés lap megjeleníti a tevékenységek részleteit, például a célként megadott dokumentumot és a tevékenység helyét. Egy fájlhoz hozzáférő esemény esetében például megtekintheti az elérni kívánt dokumentumot, a hozzá tartozó fióknevet és IP-címet. |
| Azure Active Directory | Magában foglalja a leggyakoribb felhasználói tevékenységeket, például a felhasználói jelszavak visszaállítását és a bejelentkezési kísérleteket. A részletezés során látni fogja a tevékenységek részleteit, például az eredmény állapotát. Ez általában akkor hasznos, ha a Azure Active Directory gyanús tevékenységeket szeretne figyelni. |




## <a name="azure-monitor-log-records"></a>Naplóbejegyzések Azure Monitor

Az Office 365-megoldás által Azure Monitor Log Analytics munkaterületen létrehozott összes rekord **típusa** **OfficeActivity**.  A **OfficeWorkload** tulajdonság határozza meg, hogy melyik Office 365-szolgáltatás a rekord a-Exchange, a AzureActiveDirectory, a SharePoint vagy a OneDrive kifejezésre hivatkozik.  A **rekordtípus** tulajdonság meghatározza a művelet típusát.  A tulajdonságok minden egyes Művelettípus esetében eltérőek lesznek, és az alábbi táblázatokban jelennek meg.

### <a name="common-properties"></a>Általános tulajdonságok

A következő tulajdonságok minden Office 365-rekordnál közösek.

| Tulajdonság | Leírás |
|:--- |:--- |
| Type (Típus) | *OfficeActivity* |
| Ügyfélip | Annak az eszköznek az IP-címe, amelyet a tevékenység naplózása során használt. Az IP-cím IPv4-vagy IPv6-cím formátumban jelenik meg. |
| OfficeWorkload | Az Office 365 szolgáltatás, amelyre a rekord hivatkozik.<br><br>AzureActiveDirectory<br>Adatcsere<br>SharePoint|
| Művelet | A felhasználó vagy a rendszergazda tevékenység neve.  |
| OrganizationId | A szervezet Office 365-bérlője GUID azonosítója. Ez az érték mindig ugyanaz lesz a szervezetnél, függetlenül attól, hogy melyik Office 365-szolgáltatásban történik. |
| Rekordtípus | A végrehajtott művelet típusa. |
| ResultStatus | Azt jelzi, hogy a művelet (a műveleti tulajdonságban megadott művelet) sikeres volt-e. A lehetséges értékek sikeresek, PartiallySucceeded vagy sikertelenek. Exchange-rendszergazdai tevékenység esetén az érték igaz vagy hamis. |
| userId | A naplózni kívánt műveletet végrehajtó felhasználó UPN-neve (egyszerű felhasználónév); például my_name@my_domain_name. Vegye figyelembe, hogy a rendszerfiókok (például SHAREPOINT\system vagy NTAUTHORITY\SYSTEM) által végzett tevékenységekre vonatkozó rekordok is szerepelnek. | 
| UserKey | A felhasználóazonosító tulajdonságban azonosított felhasználó alternatív azonosítója.  Ez a tulajdonság például a Passport egyedi azonosítójával (PUID) van feltöltve a felhasználók által a SharePointban, a vállalati OneDrive és az Exchange-ben végrehajtott eseményekhez. Ez a tulajdonság a felhasználóazonosító tulajdonsággal megegyező értéket is megadhat a más szolgáltatásokban és a rendszerfiókokban végrehajtott eseményekben előforduló események esetében.|
| userType | A műveletet végrehajtó felhasználó típusa.<br><br>Felügyeleti<br>Jelentkezés<br>DcAdmin<br>Rendszeres<br>Foglalt<br>ServicePrincipal<br>Rendszer |


### <a name="azure-active-directory-base"></a>Azure Active Directory alapja

A következő tulajdonságok minden Azure Active Directory rekordnál közösek.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| Rekordtípus     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Az Azure AD-esemény típusa. |
| Extendedproperties példányt paraméterként | Az Azure AD-esemény kiterjesztett tulajdonságai. |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory fiók bejelentkezése

Ezek a rekordok akkor jönnek létre, amikor egy Active Directory felhasználó megpróbál bejelentkezni.

| Tulajdonság | Leírás |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Az alkalmazás, amely elindítja a fiók bejelentkezési eseményét, például: Office 15. |
| `Client` | A fiók bejelentkezési eseményéhez használt ügyféleszközök, eszköz operációs rendszer és eszköz böngésző adatai. |
| `LoginStatus` | Ez a tulajdonság a OrgIdLogon. LoginStatus közvetlenül származik. A különböző érdekes bejelentkezési hibák leképezése a riasztási algoritmusokkal végezhető el. |
| `UserDomain` | A bérlői azonosító információi (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Ezek a rekordok akkor jönnek létre, amikor módosítást vagy kiegészítést végeznek Azure Active Directory objektumokhoz.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| Rekordtípus     | AzureActiveDirectory |
| AADTarget | A felhasználó, aki a műveletet (a művelet tulajdonsága azonosítja) elvégezte. |
| Színész | Az a felhasználó vagy szolgáltatásnév, amely végrehajtotta a műveletet. |
| ActorContextId | Annak a szervezetnek a GUID azonosítója, amelyhez a szereplő tartozik. |
| ActorIpAddress | A szereplő IP-címe IPV4-vagy IPV6-cím formátumban. |
| InterSystemsId | A GUID-azonosító, amely az Office 365 szolgáltatásban található összetevők közötti műveleteket nyomon követi. |
| IntraSystemId |   A Azure Active Directory által generált GUID a művelet nyomon követéséhez. |
| SupportTicketId | Az ügyfél-támogatási jegy azonosítója a művelethez "működés közbeni" helyzetekben. |
| TargetContextId | Annak a szervezetnek a GUID azonosítója, amelyhez a megcélozott felhasználó tartozik. |


### <a name="data-center-security"></a>Adatközpont biztonsága

Ezeket a rekordokat az adatközpont biztonsági naplózási adatai alapján hozza létre a rendszer.  

| Tulajdonság | Leírás |
|:--- |:--- |
| EffectiveOrganization | Annak a bérlőnek a neve, amelyhez a Jogosultságszint-emelés vagy a parancsmag irányult. |
| ElevationApprovedTime | A Jogosultságszint-emelés jóváhagyásának időbélyegzője. |
| ElevationApprover | A Microsoft Manager neve. |
| ElevationDuration | Az az időtartam, ameddig a Jogosultságszint-emelés aktív volt. |
| ElevationRequestId |  A jogosultságszint-emelési kérelem egyedi azonosítója. |
| ElevationRole | A jogosultságszint-emelést kérő szerepkör. |
| ElevationTime | A Jogosultságszint-emelés kezdési időpontja. |
| Start_Time | A parancsmag végrehajtásának kezdési időpontja. |


### <a name="exchange-admin"></a>Exchange-rendszergazda

Ezek a rekordok akkor jönnek létre, amikor módosításokat végeznek az Exchange-konfigurációban.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Adatcsere |
| Rekordtípus     | ExchangeAdmin |
| ExternalAccess |  Megadja, hogy a parancsmagot a szervezet egy felhasználója, a Microsoft Datacenter személyzete vagy egy adatközpont-szolgáltatásfiók vagy egy meghatalmazott rendszergazda futtatta-e. A False érték azt jelzi, hogy a parancsmagot a szervezet egy személye futtatta. Az igaz érték azt jelzi, hogy a parancsmagot az adatközpont munkatársai, egy adatközpont-szolgáltatásfiók vagy meghatalmazott rendszergazda futtatták. |
| ModifiedObjectResolvedName |  Ez a parancsmag által módosított objektum felhasználóbarát neve. Ezt csak akkor naplózza a rendszer, ha a parancsmag módosítja az objektumot. |
| Szervezetnév | A bérlő neve. |
| OriginatingServer | Annak a kiszolgálónak a neve, amelyből a parancsmag végre lett hajtva. |
| Paraméterek | Az Operations tulajdonságban azonosított parancsmaggal használt összes paraméter neve és értéke. |


### <a name="exchange-mailbox"></a>Exchange-postaláda

Ezek a rekordok akkor jönnek létre, amikor az Exchange-postaládák módosításai vagy kiegészítései történnek.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Adatcsere |
| Rekordtípus     | ExchangeItem |
| ClientInfoString | A művelet végrehajtásához használt e-mail-ügyféllel kapcsolatos információk, például a böngésző verziószáma, az Outlook verziója és a mobileszköz-információ. |
| Client_IPAddress | Annak az eszköznek az IP-címe, amelyet a művelet naplózásakor használt. Az IP-cím IPv4-vagy IPv6-cím formátumban jelenik meg. |
| ClientMachineName | Az Outlook-ügyfelet futtató számítógépnév. |
| ClientProcessName | A postaláda elérésére használt e-mail ügyfélprogram. |
| ClientVersion | Az e-mail ügyfélprogram verziója. |
| InternalLogonType | Belső használatra fenntartva. |
| Logon_Type | Megadja a postaládához hozzáférő felhasználó típusát, és végrehajtotta a naplózott műveletet. |
| LogonUserDisplayName |    A műveletet végrehajtó felhasználó felhasználóbarát neve. |
| LogonUserSid | A műveletet végrehajtó felhasználó biztonsági azonosítója. |
| MailboxGuid | Az elérni kívánt postaláda Exchange GUID azonosítója. |
| MailboxOwnerMasterAccountSid | A postaláda-tulajdonos fiókjának fő fiókja SID. |
| MailboxOwnerSid | A postaláda tulajdonosának biztonsági azonosítója. |
| MailboxOwnerUPN | Az elérni kívánt postaládát birtokló személy e-mail-címe. |


### <a name="exchange-mailbox-audit"></a>Exchange-postaláda naplózása

Ezek a rekordok a postaláda-naplózási bejegyzések létrehozásakor jönnek létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Adatcsere |
| Rekordtípus     | ExchangeItem |
| Tétel | Azt az elemeket jelöli, amelyen a műveletet elvégezték | 
| SendAsUserMailboxGuid | Azon postaláda Exchange GUID azonosítója, amelyet a rendszer az e-mailek küldésére használt. |
| SendAsUserSmtp | A megszemélyesíteni kívánt felhasználó SMTP-címe. |
| SendonBehalfOfUserMailboxGuid | Azon postaláda Exchange GUID azonosítója, amelyet a rendszer a levél nevében küldött e-mailek küldéséhez. |
| SendOnBehalfOfUserSmtp | Annak a felhasználónak az SMTP-címe, akinek az e-mail-címét elküldi. |


### <a name="exchange-mailbox-audit-group"></a>Exchange postaláda-naplózási csoport

Ezek a rekordok akkor jönnek létre, amikor változtatásokat vagy kiegészítéseket végeznek az Exchange-csoportokban.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Adatcsere |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Információk a csoport egyes elemeiről. |
| CrossMailboxOperations | Azt jelzi, hogy a művelet több postaládát is érintett-e. |
| DestMailboxId | Csak akkor állítható be, ha a CrossMailboxOperations paraméter értéke TRUE (igaz). Megadja a cél postaláda GUID azonosítóját. |
| DestMailboxOwnerMasterAccountSid | Csak akkor állítható be, ha a CrossMailboxOperations paraméter értéke TRUE (igaz). Meghatározza a postaláda tulajdonosának fő fiókjának SID-azonosítóját. |
| DestMailboxOwnerSid | Csak akkor állítható be, ha a CrossMailboxOperations paraméter értéke TRUE (igaz). Megadja a cél postaláda SID-azonosítóját. |
| DestMailboxOwnerUPN | Csak akkor állítható be, ha a CrossMailboxOperations paraméter értéke TRUE (igaz). Megadja a cél postaláda tulajdonosának egyszerű felhasználónevét. |
| DestFolder | A célmappa olyan műveletek esetén, mint például az áthelyezés. |
| Mappa | Az a mappa, ahol az elemek csoportja található. |
| Mappák |     A műveletben érintett forrás mappákkal kapcsolatos információk; például ha a mappák elem ki van választva, majd törölve lett. |


### <a name="sharepoint-base"></a>SharePoint-alap

Ezek a tulajdonságok az összes SharePoint-rekord esetében azonosak.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| eventSource | Azt azonosítja, hogy egy esemény történt a SharePointban. A lehetséges értékek a következők: SharePoint vagy ObjectModel. |
| ItemType | Az elért vagy módosított objektum típusa. Az objektumok típusairól a ItemType táblázatban talál további információt. |
| MachineDomainInfo | Az eszköz-szinkronizálási műveletekkel kapcsolatos információk. Ezek az adatok csak akkor jelennek meg, ha szerepelnek a kérelemben. |
| MachineId |   Az eszköz-szinkronizálási műveletekkel kapcsolatos információk. Ezek az adatok csak akkor jelennek meg, ha szerepelnek a kérelemben. |
| Hely | Annak a helynek a GUID azonosítója, ahol a felhasználó által elért fájl vagy mappa található. |
| Source_Name | Az a entitás, amely kiváltotta a naplózott műveletet. A lehetséges értékek a következők: SharePoint vagy ObjectModel. |
| UserAgent | A felhasználó ügyfelének vagy böngészőjének adatai. Ezt az információt az ügyfél vagy a böngésző biztosít. |


### <a name="sharepoint-schema"></a>SharePoint-séma

Ezek a rekordok akkor jönnek létre, amikor konfigurációs módosításokat végeznek a SharePointban.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Nem kötelező karakterlánc az egyéni eseményekhez. |
| Event_Data |  Nem kötelező adattartalom az egyéni eseményekhez. |
| ModifiedProperties | A tulajdonságot a felügyeleti események tartalmazzák, például a felhasználók hozzáadása egy helyhez vagy egy webhelycsoport-felügyeleti csoport tagjaként. A tulajdonság tartalmazza a módosított tulajdonság nevét (például a hely felügyeleti csoportját), a módosított tulajdonság új értékét (például a hely rendszergazdájaként hozzáadott felhasználót) és a módosított objektum előző értékét. |


### <a name="sharepoint-file-operations"></a>SharePoint-fájl műveletei

Ezek a rekordok a SharePoint-fájl műveleteire adott válaszként jönnek létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | A másolt vagy áthelyezett fájl kiterjesztése. Ez a tulajdonság csak a FileCopied és a FileMoved eseményekhez jelenik meg. |
| DestinationFileName | A másolt vagy áthelyezett fájl neve. Ez a tulajdonság csak a FileCopied és a FileMoved eseményekhez jelenik meg. |
| DestinationRelativeUrl | Annak a célmappanek az URL-címe, ahová a fájl másolása vagy áthelyezése történik. A SiteURL, a DestinationRelativeURL és a DestinationFileName paraméterek értékeinek kombinációja megegyezik a ObjectID tulajdonság értékével, amely a másolt fájl teljes elérési útja. Ez a tulajdonság csak a FileCopied és a FileMoved eseményekhez jelenik meg. |
| SharingType | A felhasználóhoz hozzárendelt megosztási engedélyek típusa, amelyekkel az erőforrás meg lett osztva. Ezt a felhasználót a UserSharedWith paraméter azonosítja. |
| Site_Url | Annak a helynek az URL-címe, ahol a felhasználó által elért fájl vagy mappa található. |
| SourceFileExtension | A felhasználó által elért fájl kiterjesztése. Ez a tulajdonság üres, ha az elérni kívánt objektum egy mappa. |
| SourceFileName |  A felhasználó által elért fájl vagy mappa neve. |
| SourceRelativeUrl | A felhasználó által elért fájlt tartalmazó mappa URL-címe. A SiteURL, a SourceRelativeURL és a SourceFileName paraméterek értékeinek kombinációja megegyezik a ObjectID tulajdonság értékével, amely a felhasználó által elért fájl teljes elérési útja. |
| UserSharedWith |  Az a felhasználó, aki az erőforrást megosztotta. |




## <a name="sample-log-searches"></a>Naplókeresési minták

A következő táblázat a megoldás által összegyűjtött frissítési rekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

| Lekérdezés | Leírás |
| --- | --- |
|Az Office 365-előfizetés összes műveletének száma |OfficeActivity &#124; összesített száma () művelet szerint |
|SharePoint-webhelyek használata|OfficeActivity &#124; , ahol a OfficeWorkload = ~ " &#124; SharePoint" összesítő darabszám () SiteUrl szerint \| rendezése az ASC szám szerint|
|Fájl-hozzáférési műveletek felhasználói típus szerint|Keresés a következőben: (OfficeActivity) OfficeWorkload = ~ "azureactivedirectory" és "MyTest"|
|Keresés adott kulcsszóval|Type = OfficeActivity OfficeWorkload = azureactivedirectory "MyTest"|
|Külső műveletek figyelése az Exchange-ben|OfficeActivity &#124; , ahol OfficeWorkload = ~ "Exchange" és ExternalAccess = = True|



## <a name="next-steps"></a>Következő lépések

* A részletes frissítési információk megtekintéséhez használja [a Azure monitor a naplózási lekérdezéseket](../log-query/log-query-overview.md) .
* [Saját irányítópultok létrehozásával](../learn/tutorial-logs-dashboards.md) megjelenítheti kedvenc Office 365 keresési lekérdezéseit.
* [Riasztásokat hozhat létre](../platform/alerts-overview.md) , amelyekkel proaktívan értesítheti a fontos Office 365-tevékenységeket.  
