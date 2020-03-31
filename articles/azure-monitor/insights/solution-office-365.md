---
title: Office 365 felügyeleti megoldás az Azure-ban | Microsoft dokumentumok
description: Ez a cikk az Office 365-megoldás Azure-beli konfigurálásáról és használatáról nyújt tájékoztatást.  Az Azure Monitorban létrehozott Office 365-rekordok részletes leírását tartalmazza.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/08/2019
ms.openlocfilehash: 0018ae55ab74e691577a34a397c15355587e0fac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663255"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Az Office 365 felügyeleti megoldása az Azure-ban (előzetes verzió)

![Office 365-embléma](media/solution-office-365/icon.png)


> [!IMPORTANT]
> ## <a name="solution-update"></a>Megoldás frissítése
> Ezt a megoldást az [Azure Sentinel](../../sentinel/overview.md) [Office 365](../../sentinel/connect-office-365.md) általános rendelkezésre állási megoldása és az Azure [AD jelentéskészítési és figyelési megoldásváltotta](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md)fel. Együtt biztosítják az előző Azure Monitor Office 365-megoldás frissített verzióját továbbfejlesztett konfigurációs felülettel. A meglévő megoldást 2020.
> 
> Az Azure Sentinel egy natív, natív biztonsági információkkal és eseményekkezelésével kapcsolatos megoldás, amely betöltődik a naplókba, és további SIEM-funkciókat biztosít, beleértve az észleléseket, a vizsgálatokat, a vadászatot és a gépi tanulásvezérelt elemzéseket. Az Azure Sentinel használatával mostantól az Office 365 SharePoint-tevékenység- és Exchange-felügyeleti naplók betöltését biztosítjuk.
> 
> Az Azure AD-jelentés átfogóbb képet nyújt az Azure AD-tevékenységből származó naplókról a környezetében, beleértve a bejelentkezési eseményeket, a naplózási eseményeket és a címtár változásait. Az Azure AD-naplók csatlakoztatásához használhatja az [Azure Sentinel Azure AD-összekötőt,](../../sentinel/connect-azure-active-directory.md) vagy konfigurálhatja az [Azure AD-naplók integrációját az Azure Monitorlal.](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 
>
> Az Azure AD-napló gyűjteménye az Azure Monitor díjszabása.  További információkért lásd az [Azure Monitor díjszabását.](https://azure.microsoft.com/pricing/details/monitor/)
>
> Az Azure Sentinel Office 365-ös megoldás használata:
> 1. Az Office 365-összekötő azure Sentinel használata hatással van a munkaterület díjszabására. További információ: [Azure Sentinel díjszabás.](https://azure.microsoft.com/pricing/details/azure-sentinel/)
> 2. Ha már használja az Azure Monitor Office 365-ös megoldást, először el kell távolítania azt az [alábbi Eltávolítás szakaszban](#uninstall)található parancsfájl használatával.
> 3. [Engedélyezze az Azure Sentinel-megoldást](../../sentinel/quickstart-onboard.md) a munkaterületen.
> 4. Nyissa meg az Azure Sentinel **Adatösszekötők lapját,** és engedélyezze az **Office 365-összekötőt.**
>
> ## <a name="frequently-asked-questions"></a>Gyakori kérdések
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-april-30th"></a>K: Lehetséges-e az Office 365 Azure Monitor megoldásának fedélzetre kapcsolása mostantól április 30-ig?
> Nem, az Azure Monitor Office 365-megoldás bevezetési parancsfájljai már nem érhetők el. A megoldást április 30-án távolítják el.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>K: Megváltoznak a táblák és a sémák?
> Az **OfficeActivity** tábla neve és sémája ugyanaz marad, mint az aktuális megoldásban. Továbbra is használhatja ugyanazokat a lekérdezéseket az új megoldásban, kivéve az Azure AD-adatokat hivatkozó lekérdezéseket.
> 
> Az új [Azure AD-jelentési és figyelési megoldásnaplók](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) az **OfficeActivity**helyett a [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) és [auditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) táblákba kerülnek. További információkért tekintse meg, hogyan elemezheti az [Azure AD-naplókat,](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md)ami az Azure Sentinel és az Azure Monitor felhasználói számára is fontos.
> 
> Az alábbiakban mintákat tartalmaz a lekérdezések **OfficeActivity-ból** **SigninLogs-ra**történő konvertálásához:
> 
> **A lekérdezés nem sikerült bejelentkezések, felhasználó szerint:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId 
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Az Azure AD-műveletek megtekintése:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>K: Hogyan tudom a fedélzeti Azure Sentinel?
> Az Azure Sentinel egy olyan megoldás, amely et új vagy meglévő Log Analytics-munkaterületen is engedélyezhet. További információ: [Azure Sentinel beszállási dokumentáció.](../../sentinel/quickstart-onboard.md)
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>K: Szükségem van az Azure Sentinelre az Azure AD-naplók csatlakoztatásához?
> Konfigurálhatja [az Azure AD-naplók integrációját az Azure Monitorlal,](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)amely nem kapcsolódik az Azure Sentinel-megoldáshoz. Az Azure Sentinel egy natív összekötőt és beépített tartalmat biztosít az Azure AD-naplókhoz. További információt az alábbi, használatra alkalmas, biztonságorientált tartalommal kapcsolatos kérdésben talál.
>
> ###   <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>K: Miak a különbségek az Azure Sentinel ből és az Azure Monitorból származó Azure AD-naplók csatlakoztatásakor?
> Az Azure Sentinel és az Azure Monitor ugyanazon [Azure AD-jelentéskészítési és figyelési megoldáson](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md)alapuló Azure AD-naplókhoz csatlakozik. Az Azure Sentinel egy kattintással elérhető, natív összekötőt biztosít, amely ugyanazokat az adatokat kapcsolja össze, és figyelési információkat biztosít.
>
> ###   <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>K: Mit kell módosítanom, amikor az új Azure AD-jelentési és figyelési táblákra költözik?
> Az Azure AD-adatokat használó összes lekérdezést, beleértve a riasztásokban, irányítópultokban és az Office 365 Azure AD-adatokkal létrehozott tartalmakat, újra létre kell hozni az új táblák használatával.
>
> Az Azure Sentinel és az Azure AD beépített tartalmat biztosít, amelyet az Azure AD jelentéskészítési és figyelési megoldásra való áttéréskor használhat. További információt a beépített biztonságorientált tartalommal és az [Azure Active Directory-jelentésekhez tartozó Azure Monitor-munkafüzetek használatával](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md)kapcsolatos következő kérdésben talál. 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>K: Hogyan használhatom az Azure Sentinel beépített biztonságorientált tartalmat?
> Az Azure Sentinel az Office 365- és az Azure AD-naplók on-the-box ban található, biztonságorientált irányítópultokat, egyéni riasztási lekérdezéseket, vadászati lekérdezéseket, lekérdezéseket és automatikus válaszadási lehetőségeket biztosít. További információért ismerje meg az Azure Sentinel GitHubot és az oktatóanyagokat:
>
> - [A fenyegetések észlelése beépített fenyegetések](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Egyéni elemzési szabályok létrehozása gyanús fenyegetések észleléséhez](../../sentinel/tutorial-detect-threats-custom.md)
> - [Adatok monitorozása](../../sentinel/tutorial-monitor-your-data.md)
> - [Incidensek kivizsgálása az Azure Sentinellel](../../sentinel/tutorial-investigate-cases.md)
> - [Automatikus fenyegetésre adott válaszok beállítása az Azure Sentinelben](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Azure Sentinel GitHub-közösség](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>K: Az Azure Sentinel további összekötők a megoldás részeként?
> Igen, lásd: [Azure Sentinel connect adatforrások](../../sentinel/connect-data-sources.md).
> 
> ###   <a name="q-what-will-happen-on-april-30-do-i-need-to-offboard-beforehand"></a>K: Mi történik április 30-án? Előre le kell mondanom?
> 
> - Az **Office365-megoldásból** nem fog tudni adatokat fogadni. A megoldás a továbbiakban nem lesz elérhető a Marketplace-en
> - Az Azure Sentinel-ügyfelek számára az **Office365** Log Analytics-munkaterületi megoldás az Azure Sentinel **SecurityInsights-megoldás** része lesz.
> - Ha nem veszi ki manuálisan a megoldást, az adatok automatikusan lelesznek választva április 30-án.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>K: Átfog-e az adattovábbítás az új megoldásba?
> Igen. Amikor eltávolítja az **Office 365-megoldást** a munkaterületről, az adatok ideiglenesen elérhetetlenné válnak, mert a séma eltávolításra kerül. Ha engedélyezi az új **Office 365-összekötőt** a Sentinelben, a séma visszaáll a munkaterületre, és a már összegyűjtött adatok elérhetővé válnak. 
 

Az Office 365 felügyeleti megoldás lehetővé teszi az Office 365-környezet figyelését az Azure Monitorban.

- Az Office 365-fiókok felhasználói tevékenységeinek figyelésével elemezheti a használati mintákat, valamint azonosíthatja a viselkedési trendeket. Kibonthat például bizonyos használati eseteket, például a szervezeten kívüli megosztott fájlokat vagy a legnépszerűbb SharePoint-webhelyeket.
- A rendszergazdai tevékenységek figyelése a konfigurációs módosítások vagy a magas jogosultsági szintű műveletek nyomon követéséhez.
- Észlelheti és megvizsgálhatja a nem kívánt felhasználói viselkedést, amely a szervezeti igényeknek megfelelően testreszabható.
- Az audit és a megfelelőség bemutatása. Figyelheti például a bizalmas fájlok fájlhozzáférési műveleteit, amelyek segíthetnek a naplózási és megfelelőségi folyamatban.
- Működési hibaelhárítást végezhet a szervezet Office 365-tevékenységi adatain felüli [naplólekérdezésekkel.](../log-query/log-query-overview.md)


## <a name="uninstall"></a>Eltávolítás

Az Office 365 felügyeleti megoldása a [Felügyeleti megoldás eltávolítása](solutions.md#remove-a-monitoring-solution)című folyamatban található folyamattal távolítható el. Ez azonban nem állítja le az Office 365-ből az Azure Monitorba gyűjtött adatokat. Az alábbi eljárással leiratkozhat az Office 365-ről, és leállíthatja az adatgyűjtést.

1. Mentse a következő parancsfájlt *office365_unsubscribe.ps1*néven.

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
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
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

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

A rendszer kérni fogja a hitelesítő adatokat. Adja meg a Hitelesítő adatokat a Log Analytics-munkaterülethez.

## <a name="data-collection"></a>Adatgyűjtés

Az adatok első gyűjtése néhány órát is igénybe vehet. Az adatgyűjtés megkezdése után az Office 365 egy webhook-értesítést küld részletes adatokat tartalmazó [webhook-értesítésről](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) az Azure Monitornak minden alkalommal, amikor egy rekordot létrehoz. Ez a rekord a fogadást követő néhány percen belül elérhető az Azure Monitorban.

## <a name="using-the-solution"></a>A megoldás használata

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Amikor hozzáadja az Office 365-megoldást a Log Analytics-munkaterülethez, az **Office 365** csempe hozzáadódik az irányítópulthoz. Ez a csempe a környezetben jelenleg elérhető számítógépek számát és grafikus ábrázolását jeleníti meg, valamint a frissítési megfelelőségi állapotukat.<br><br>
![Az Office 365 összefoglaló csempéje](media/solution-office-365/tile.png)  

Kattintson az **Office 365** csempére az **Office 365** irányítópultjának megnyitásához.

![Az Office 365 irányítópultja](media/solution-office-365/dashboard.png)  

Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Minden oszlop felsorolja az első tíz riasztást az adott oszlop adott hatókörre és időtartományra vonatkozó feltételeinek megfelelő szám szerint. A teljes listát tartalmazó naplókeresést úgy futtathatja, hogy az oszlop alján található Összes megtekintése gombra vagy az oszlopfejlécre kattint.

| Oszlop | Leírás |
|:--|:--|
| Műveletek | Tájékoztatást nyújt az összes figyelt Office 365-előfizetés aktív felhasználóiról. Azt is látni fogja, hogy az idő múlásával végzett tevékenységek száma.
| Exchange | Az Exchange Server tevékenységeinek ( postaláda-hozzáadása engedély vagy Set-Mailbox) bontását jeleníti meg. |
| SharePoint | A felhasználók által a SharePoint-dokumentumokon végzett legfontosabb tevékenységek et jeleníti meg. Ha leás erről a csempéről, a keresőoldal megjeleníti ezeknek a tevékenységeknek a részleteit, például a céldokumentumot és a tevékenység helyét. Egy fájlhoz hozzáféréssel rendelkező esemény esetén például láthatja az elért dokumentumot, a hozzá tartozó fióknevét és IP-címét. |
| Azure Active Directory | Tartalmazza a legnépszerűbb felhasználói tevékenységeket, például a Felhasználói jelszó alaphelyzetbe állítását és a bejelentkezési kísérleteket. Amikor leás, akkor képes lesz arra, hogy a részleteket ezek a tevékenységek, mint például az eredmény állapota. Ez leginkább akkor hasznos, ha az Azure Active Directoryban szeretné figyelni a gyanús tevékenységeket. |




## <a name="azure-monitor-log-records"></a>Az Azure Monitor naplórekordjai

Az Office 365-megoldás által az Azure Monitor Log Analytics-munkaterületén létrehozott összes rekord **office-tevékenység** **típussal**rendelkezik.  Az **OfficeWorkload** tulajdonság határozza meg, hogy a rekord melyik Office 365-szolgáltatásra hivatkozik – Exchange, AzureActiveDirectory, SharePoint vagy OneDrive.  A **RecordType** tulajdonság a művelet típusát adja meg.  A tulajdonságok az egyes művelettípusoktól függően változnak, és az alábbi táblázatokban láthatók.

### <a name="common-properties"></a>Közös tulajdonságok

Az alábbi tulajdonságok az összes Office 365-rekordban közösek.

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus | *OfficeActivity tevékenység* |
| ÜgyfélIP-címe | Az esemény naplózásakor használt eszköz IP-címe. Az IP-cím IPv4 vagy IPv6 formátumban jelenik meg. |
| OfficeWorkload | Az Office 365 szolgáltatás, amelyre a rekord hivatkozik.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Művelet | A felhasználói vagy rendszergazdai tevékenység neve.  |
| OrganizationId (Szervezeti azonosító) | A szervezet Office 365-ös bérlőjének GUID azonosítója. Ez az érték mindig ugyanaz lesz a szervezet számára, függetlenül attól, hogy melyik Office 365 szolgáltatásban fordul elő. |
| RecordType (Rekordtípus) | Az elvégzett művelet típusa. |
| ResultStatus (Eredmény állapota) | Azt jelzi, hogy a (Művelet tulajdonságban megadott) művelet sikeres volt-e. A lehetséges értékek sikeresek, részlegesen sikeresek vagy sikertelenek. Exchange felügyeleti tevékenység esetén az érték értéke Igaz vagy Hamis. |
| UserId (Felhasználóazonosító) | Annak a felhasználónak az egyszerű felhasználóneve (egyszerű felhasználónév), aki a rekordot naplózott műveletet eredményezte; például my_name@my_domain_name. Vegye figyelembe, hogy a rendszerfiókok által végzett tevékenységek (például sharepoint\system vagy NTAUTHORITY\SYSTEM) bejegyzései is szerepelnek. | 
| UserKey (Felhasználói kulcs) | A UserId tulajdonságban azonosított felhasználó alternatív azonosítója.  Ez a tulajdonság például a SharePoint, a OneDrive Vállalati verzió és az Exchange felhasználói által végrehajtott események hez tartalmazza az útlevél egyedi azonosítóját (PUID). Ez a tulajdonság a UserID tulajdonsággal megegyező értéket is megadhat a rendszerfiókok által végrehajtott egyéb szolgáltatásokban és eseményekben bekövetkező eseményeknél.|
| UserType (Felhasználótípus) | A műveletet végrehajtó felhasználó típusa.<br><br>Rendszergazda<br>Alkalmazás<br>DcAdmin<br>Hagyományos<br>Foglalt<br>Szolgáltatásnév<br>Rendszer |


### <a name="azure-active-directory-base"></a>Az Azure Active Directory alapja

A következő tulajdonságok az összes Azure Active Directory-rekord közösek.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType (Rekordtípus)     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Az Azure AD-esemény típusa. |
| Bővített tulajdonságok | Az Azure AD-esemény bővített tulajdonságai. |


### <a name="azure-active-directory-account-logon"></a>Az Azure Active Directory-fiók bejelentkezése

Ezek a rekordok akkor jönnek létre, amikor az Active Directory felhasználó megpróbál bejelentkezni.

| Tulajdonság | Leírás |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | A fiókbejelentkezési eseményt kiváltó alkalmazás, például az Office 15. |
| `Client` | A fiókbejelentkezési eseményhez használt ügyféleszköz, eszközoperációs rendszer és eszközböngésző részletei. |
| `LoginStatus` | Ez a tulajdonság közvetlenül az OrgIdLogon.LoginStatus fájlból származik. A különböző érdekes bejelentkezési hibák leképezése algoritmusok riasztásával végezhető el. |
| `UserDomain` | A bérlői identitásinformáció (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Ezek a rekordok akkor jönnek létre, amikor az Azure Active Directory-objektumok módosítása vagy kiegészítése történik.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType (Rekordtípus)     | AzureActiveDirectory |
| AADCél | Az a felhasználó, akire a műveletet (az Operation tulajdonság azonosította) végrehajtotta. |
| Színész | A műveletet végző felhasználó vagy egyszerű szolgáltatás. |
| ActorContextId (Színészkörnyezetazonosító) | Annak a szervezetnek a GUID azonosítója, amelyhez az aktor tartozik. |
| ActorIpAddress cím | Az aktor IP-címe IPV4 vagy IPV6 címformátumban. |
| InterSystemsId | Az Office 365 szolgáltatás összetevőiközötti műveleteket nyomon követő GUID azonosító. |
| IntraSystemId |   Az Azure Active Directory által a művelet nyomon követéséhez generált GUID. |
| SupportTicketId azonosító | Az ügyfélszolgálati jegy azonosítója a művelethez a "cselekvés a nevében" helyzetekben. |
| TargetContextId azonosító | Annak a szervezetnek a GUID azonosítója, amelyhez a megcélzott felhasználó tartozik. |


### <a name="data-center-security"></a>Adatközpont biztonsága

Ezek a rekordok az adatközpont-biztonsági naplózási adatokból jönnek létre.  

| Tulajdonság | Leírás |
|:--- |:--- |
| Hatékony szervezet | Annak a bérlőnek a neve, amelyre a magasság/parancsmag irányult. |
| ElevationApprovedTime | A jogosultságszint-emelés jóváhagyásának időbélyegzője. |
| ElevationApprover | A Microsoft-kezelő neve. |
| MagasságIdőtartam | Az az időtartam, amelynél a magasság aktív volt. |
| Magasságkérése |  A magassági kérelem egyedi azonosítója. |
| ElevationRole (Magassági szerepkör) | A jogosultságszint-emelés kért szerepkör. |
| Magassági idő | A magasság kezdési időpontja. |
| Start_Time | A parancsmag végrehajtásának kezdő időpontja. |


### <a name="exchange-admin"></a>Exchange-rendszergazda

Ezek a rekordok az Exchange-konfiguráció módosításakor jönnek létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType (Rekordtípus)     | ExchangeAdmin |
| ExternalAccess |  Itt adható meg, hogy a parancsmackét a szervezet egyik felhasználója, a Microsoft adatközpont munkatársai vagy egy adatközpont-szolgáltatási fiók vagy egy delegált rendszergazda futtatta-e. A Hamis érték azt jelzi, hogy a parancsmatot a szervezet egy tagja futtatta. Az Igaz érték azt jelzi, hogy a parancsmac-ot adatközpont-személyzet, egy adatközpont-szolgáltatásfiók vagy egy delegált rendszergazda futtatta. |
| ModifiedObjectResolvedName |  Ez annak az objektumnak a felhasználóbarát neve, amelyet a parancsmag módosított. Ez csak akkor kerül naplózásra, ha a parancsmag módosítja az objektumot. |
| OrganizationName | A bérlő neve. |
| Származókiszolgáló | Annak a kiszolgálónak a neve, amelyről a parancsmag ot végrehajtották. |
| Paraméterek | Az Operations tulajdonságban azonosított parancsmaggal használt összes paraméter neve és értéke. |


### <a name="exchange-mailbox"></a>Exchange-postaláda

Ezek a rekordok akkor jönnek létre, amikor az Exchange-postaládákban módosításokat vagy kiegészítéseket hajtvégre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType (Rekordtípus)     | ExchangeItem elem |
| ClientInfoString | Információ a művelet végrehajtásához használt levelezőprogramról, például a böngésző verziójáról, az Outlook verziójáról és a mobileszköz-adatokról. |
| Client_IPAddress | Annak az eszköznek az IP-címe, amelyet a művelet naplózásakor használtak. Az IP-cím IPv4 vagy IPv6 formátumban jelenik meg. |
| ClientMachineName | Az Outlook-ügyfelet tartalmazó számítógépnév. |
| ClientProcessName | A postaláda eléréséhez használt levelezőprogram. |
| Ügyfélverzió | Az e-mail ügyfél verziója . |
| InternalLogonType típus | Belső használatra fenntartva. |
| Logon_Type | Azt jelzi, hogy ki kit feknek hozzá a postaládához, és milyen típusú a naplózott művelet. |
| LogonUserDisplayName |    A műveletet végző felhasználó felhasználóbarát neve. |
| LogonUserSid | A műveletet végző felhasználó biztonsági azonosítója. |
| MailboxGuid (Postaláda guid) | Az elérni kívánt postaláda Exchange GUID azonosítója. |
| MailboxOwnerMasterAccountSid | A postaláda-tulajdonosi fiók fő fiókjának biztonsági azonosítója. |
| PostaládaTulajdonosSid | A postaláda-tulajdonos biztonsági azonosítója. |
| Postaláda-tulajdonos | Az elért postaládát birtokló személy e-mail címe. |


### <a name="exchange-mailbox-audit"></a>Exchange-postaláda-naplózás

Ezek a rekordok a postaláda-naplózási bejegyzés létrehozásakor jönnek létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType (Rekordtípus)     | ExchangeItem elem |
| Elem | Azt az elemet jelöli, amelyen a műveletet végrehajtották. | 
| SendAsUserMailboxGuid | Az e-mailek küldéséhez hozzáfért postaláda Exchange GUID azonosítója. |
| SendAsUserSmtp | A megszemélyesített felhasználó SMTP-címe. |
| SendonBehalfOfUserMailboxGuid | Annak a postaláda-guid-nak az Exchange GUID azonosítója, amelyhez a nevében e-mailt küldhetett. |
| SendOnBehalfOfUserSmtp | Annak a felhasználónak az SMTP-címe, akinek nevében az e-mailt küldik. |


### <a name="exchange-mailbox-audit-group"></a>Exchange-postaláda-naplózási csoport

Ezek a rekordok akkor jönnek létre, amikor az Exchange-csoportokban módosításokat vagy kiegészítéseket hajtvégre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| Érintett elemek | Információ a csoport egyes cikkeiről. |
| CrossMailboxOperations (KeresztpostaI Műveletek) | Azt jelzi, hogy a művelet egynél több postaládát érintett-e. |
| DestMailboxId azonosító | Csak akkor állítsa be, ha a CrossMailboxOperations paraméter Igaz. Megadja a célpostaláda GUID azonosítóját. |
| DestMailboxOwnerMasterAccountSid | Csak akkor állítsa be, ha a CrossMailboxOperations paraméter Igaz. Megadja a célpostafiók tulajdonosának főfiók-biztonsági azonosítójának sid-jét. |
| DestMailboxOwnerSid | Csak akkor állítsa be, ha a CrossMailboxOperations paraméter Igaz. A célpostafiók biztonsági azonosítóját adja meg. |
| DestMailboxOwnerUPN | Csak akkor állítsa be, ha a CrossMailboxOperations paraméter Igaz. Megadja a célpostaláda tulajdonosának upn-ját. |
| DestFolder mappa | A célmappa az olyan műveletekhez, mint az Áthelyezés. |
| Mappa | Az a mappa, ahol egy elemcsoport található. |
| Mappák |     Információ a műveletben részt vevő forrásmappákról; például ha a mappák ki vannak jelölve, majd törlődnek. |


### <a name="sharepoint-base"></a>SharePoint Alap

Ezek a tulajdonságok minden SharePoint-rekordban közösek.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource (Eseményforrás) | Azt határozza meg, hogy történt-e esemény a SharePointban. A lehetséges értékek a SharePoint vagy az ObjectModel. |
| ItemType | Az elért vagy módosított objektum típusa. Az objektumtípusokról az ItemType táblában talál részleteket. |
| MachineDomainInfo | Információ az eszközszinkronizálási műveletekről. Ez eket az adatokat csak akkor jelenti a program, ha az szerepel a kérésben. |
| MachineId |   Információ az eszközszinkronizálási műveletekről. Ez eket az adatokat csak akkor jelenti a program, ha az szerepel a kérésben. |
| Site_ | Annak a helynek a GUID azonosítója, ahol a felhasználó által elért fájl vagy mappa található. |
| Source_Name | A naplózott műveletet aktiváló entitás. A lehetséges értékek a SharePoint vagy az ObjectModel. |
| Useragent | Információ a felhasználó ügyfél- vagy böngészőjéről. Ezt az információt az ügyfél vagy a böngésző biztosítja. |


### <a name="sharepoint-schema"></a>SharePoint-séma

Ezek a rekordok akkor jönnek létre, amikor a SharePoint konfigurációs módosításatörténik.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| Egyéni esemény | Választható karakterlánc egyéni eseményekhez. |
| Event_Data |  Választható hasznos adat egyéni eseményekhez. |
| Módosítotttulajdonságok | A tulajdonság rendszergazdai eseményekhez, például egy felhasználó hozzáadása webhely vagy webhelycsoport felügyeleti csoportjának tagjaként. A tulajdonság tartalmazza a módosított tulajdonság nevét (például a Webhely gazdák csoportját), a módosított tulajdonság új értékét (például a webhely adminisztrátoraként hozzáadott felhasználót) és a módosított objektum korábbi értékét. |


### <a name="sharepoint-file-operations"></a>SharePoint-fájlműveletek

Ezek a rekordok a SharePoint fájlműveleteire válaszul jönnek létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | A másolt vagy áthelyezett fájl kiterjesztése. Ez a tulajdonság csak A FileCopied és FileMoved események esetén jelenik meg. |
| Célfájlneve | A másolt vagy áthelyezett fájl neve. Ez a tulajdonság csak A FileCopied és FileMoved események esetén jelenik meg. |
| DestinationRelativeUrl | Annak a célmappának az URL-címe, ahová a fájlt másolják vagy áthelyezik. A SiteURL, a DestinationRelativeURL és a DestinationFileName paraméterek értékeinek kombinációja megegyezik az ObjectID tulajdonság értékével, amely a másolt fájl teljes elérési útja. Ez a tulajdonság csak A FileCopied és FileMoved események esetén jelenik meg. |
| Megosztástípusa | Az erőforrást megosztó felhasználóhoz rendelt megosztási engedélyek típusa. Ezt a felhasználót a UserSharedWith paraméter azonosítja. |
| Site_Url | Annak a webhelynek az URL-címe, ahol a felhasználó által elért fájl vagy mappa található. |
| SourceFileExtension | A fájl kiterjesztése a fájl, amely a felhasználó által elért. Ez a tulajdonság üres, ha az elért objektum egy mappa. |
| Forrásfájlneve |  A felhasználó által elért fájl vagy mappa neve. |
| SourceRelativeUrl | Annak a mappának az URL-címe, amely a felhasználó által elért fájlt tartalmazza. A SiteURL, SourceRelativeURL és SourceFileName paraméterek értékeinek kombinációja megegyezik az ObjectID tulajdonság értékével, amely a felhasználó által elért fájl teljes elérési útja. |
| UserSharedWith |  Az a felhasználó, akivel egy erőforrást megosztottak. |




## <a name="sample-log-queries"></a>Mintanapló-lekérdezések

Az alábbi táblázat mintanapló-lekérdezéseket tartalmaz a megoldás által gyűjtött frissítési rekordokhoz.

| Lekérdezés | Leírás |
| --- | --- |
|Az Office 365-előfizetés összes műveletének megszámlálása |OfficeAktivitás &#124; a count() összegzése művelet szerint |
|SharePoint-webhelyek használata|OfficeActivity &#124; ahol az OfficeWorkload =~ "sharepoint" &#124; \| összegezi a count() -t SiteUrl szerint, asc gróf szerint rendezve|
|Fájlelérési műveletek felhasználótípus szerint | OfficeAktivitás &#124; összegezi a count() programot a UserType szerint |
|Külső műveletek figyelése az Exchange-en|OfficeActivity &#124; ahol OfficeWorkload =~ "exchange" és ExternalAccess == igaz|



## <a name="next-steps"></a>További lépések

* Az [Azure Monitor naplólekérdezései](../log-query/log-query-overview.md) segítségével részletes frissítési adatokat tekinthet meg.
* [Saját irányítópultokat hozhat létre](../learn/tutorial-logs-dashboards.md) kedvenc Office 365-keresési lekérdezéseinek megjelenítéséhez.
* Riasztások létrehozása, hogy proaktív módon [értesülhes](../platform/alerts-overview.md) az Office 365 fontos tevékenységeiről.  
