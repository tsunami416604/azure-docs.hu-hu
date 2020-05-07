---
title: Műveleti csoportok létrehozása és kezelése a Azure Portal
description: Megtudhatja, hogyan hozhat létre és kezelhet műveleti csoportokat a Azure Portalban.
author: dkamstra
ms.topic: conceptual
ms.date: 4/17/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 5c8808450f8baa6d395ee9c24dbc59dfa919b66d
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801008"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Műveleti csoportok létrehozása és kezelése a Azure Portal
A műveleti csoport az Azure-előfizetés tulajdonosa által meghatározott értesítési beállítások gyűjteménye. Azure Monitor és Service Health riasztások használata műveleti csoportok segítségével értesíti a felhasználókat arról, hogy riasztást váltott ki. A különböző riasztások ugyanazt a műveleti csoportot vagy különböző műveleti csoportokat használhatják a felhasználó igényeitől függően. Egy előfizetésben akár 2 000 műveleti csoportot is beállíthat.

Egy olyan műveletet konfigurálhat, amely e-mailben vagy SMS-ben értesíti a személyeket, és megerősíti, hogy hozzá lettek adva a műveleti csoporthoz.

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet műveleti csoportokat a Azure Portalban.

Az egyes műveletek a következő tulajdonságokből állnak:

* **Name (név**): a műveleti csoporton belüli egyedi azonosító.  
* **Művelettípus**: a művelet végrehajtása megtörtént. Ilyenek például a hanghívások, SMS-üzenetek, e-mailek küldése, vagy különböző típusú automatizált műveletek elindítása. Lásd a cikk későbbi részében található típusokat.
* **Részletek**: a *művelet típusa*szerint változó megfelelő részletek.

További információ arról, hogyan használhatók Azure Resource Manager sablonok a műveleti csoportok konfigurálásához: [Action Group Resource Manager-sablonok](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Műveleti csoport létrehozása a Azure Portal használatával

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza a **figyelő**elemet. A **figyelő** ablaktábla egyetlen nézetben összesíti az összes figyelési beállítást és az adatait.

1. Válassza a **Riasztások**, majd a **Műveletek kezelése** lehetőséget.

    ![Műveletek kezelése gomb](./media/action-groups/manage-action-groups.png)
    
1. Válassza a **műveleti csoport hozzáadása**lehetőséget, és töltse ki a mezőket.

    ![A "műveleti csoport hozzáadása" parancs](./media/action-groups/add-action-group.png)
    
1. Írjon be egy nevet a **műveleti csoport neve** mezőbe, és adjon meg egy nevet a **rövid név** mezőben. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

      ![A műveleti csoport hozzáadása párbeszédpanel](./media/action-groups/action-group-define.png)

1. Az **előfizetés** mezője az aktuális előfizetéssel együtt kitölti az előfizetést. Ez az előfizetés az a művelet, amelyben a műveleti csoport mentve lett.

1. Válassza ki azt az **erőforráscsoportot** , amelyben a műveleti csoportot menti.

1. Adja meg a műveletek listáját. Minden művelethez adja meg a következőket:

    1. **Név**: adjon meg egy egyedi azonosítót ehhez a művelethez.

    1. **Művelettípus**: válassza az Automation Runbook, az Azure Function, az e-mailek Azure Resource Manager a szerepkör, a levelezés/SMS/leküldés/hang, a ITSM, a Logic apps, a biztonságos webhook és a webhook lehetőséget.

    1. **Részletek**: a művelet típusa alapján adja meg a telefonszámot, az e-mail-címet, a webhook URI-ját, az Azure-alkalmazást, a ITSM-kapcsolat vagy az Automation-runbook. A ITSM művelethez emellett adja meg a **munkaelemet** és a ITSM eszköz által igényelt egyéb mezőket.
    
    1. **Gyakori riasztási séma**: engedélyezheti a [közös riasztási sémát](https://aka.ms/commonAlertSchemaDocs), amely lehetővé teszi, hogy egyetlen bővíthető és egységesített riasztási adattartalmat biztosítson a Azure monitor összes riasztási szolgáltatásában.

1. A műveleti csoport létrehozásához kattintson **az OK gombra** .

## <a name="manage-your-action-groups"></a>A műveleti csoportok kezelése

A műveleti csoport létrehozása után megtekintheti a **műveleti csoportokat** a **figyelés** ablaktábla **riasztások** kezdőlapján a **műveletek kezelése** lehetőség kiválasztásával. Válassza ki a kezelni kívánt műveleti csoportot:

* Műveletek hozzáadása, szerkesztése vagy eltávolítása.
* A műveleti csoport törlése.

## <a name="action-specific-information"></a>Műveletre vonatkozó információk

> [!NOTE]
> Az alábbi elemeken megtekintheti az [előfizetési szolgáltatás korlátozásait](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) a numerikus korlátok figyeléséhez.  

### <a name="automation-runbook"></a>Automation-Runbook
Tekintse meg az [Azure-előfizetési szolgáltatási](../../azure-resource-manager/management/azure-subscription-service-limits.md) korlátokat a Runbook-adattartalomra vonatkozó korlátozásokkal kapcsolatban.

A műveleti csoportban korlátozott számú Runbook művelet lehet. 

### <a name="azure-app-push-notifications"></a>Azure-alkalmazás leküldéses értesítései
Előfordulhat, hogy egy műveleti csoportban korlátozott számú Azure-alkalmazási művelet van.

### <a name="email"></a>E-mail
A rendszer e-maileket küld a következő e-mail-címekről. Győződjön meg arról, hogy az e-mail-szűrés megfelelően van konfigurálva
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

A műveleti csoportban korlátozott számú e-mail művelet lehet. Tekintse meg a [díjszabási információkat](./../../azure-monitor/platform/alerts-rate-limiting.md) ismertető cikket.

### <a name="email-azure-resource-manager-role"></a>E-mail-Azure Resource Manager szerepkör
E-mail küldése az előfizetés szerepkörének tagjainak. A rendszer csak az **Azure ad-felhasználók** számára küld e-mailt a szerepkörhöz. A rendszer nem küldi el az e-mailt az Azure AD-csoportoknak vagy egyszerű szolgáltatásoknak.

A műveleti csoportban korlátozott számú e-mail művelet lehet. Tekintse meg a [díjszabási információkat](./../../azure-monitor/platform/alerts-rate-limiting.md) ismertető cikket.

### <a name="function"></a>Függvény
Meghívja a meglévő HTTP-trigger végpontját [Azure Functionsban](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

A műveleti csoportban korlátozott számú Function művelet lehet.

### <a name="itsm"></a>ITSM
A ITSM művelethez ITSM-kapcsolat szükséges. Megtudhatja, hogyan hozhat létre [ITSM-kapcsolatokat](../../azure-monitor/platform/itsmc-overview.md).

A műveleti csoportban korlátozott számú ITSM művelet lehet. 

### <a name="logic-app"></a>Logikai alkalmazás
A műveleti csoportban korlátozott számú Logic app-művelet lehet.

### <a name="secure-webhook"></a>Biztonságos webhook
A műveleti csoportok webhook művelettel kihasználhatja a Azure Active Directory előnyeit a műveleti csoport és a védett webes API (webhook-végpont) közötti kapcsolat biztonságossá tételéhez. A funkció kihasználásának általános munkafolyamata alább olvasható. Az Azure AD-alkalmazások és-szolgáltatások áttekintését lásd: [Microsoft Identity platform (v 2.0) – áttekintés](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Hozzon létre egy Azure AD-alkalmazást a védett webes API-hoz. Lásd: https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Beállíthatja, hogy a védett API-t egy Daemon-alkalmazás hívja meg.
    
1. Engedélyezze a műveleti csoportokat az Azure AD-alkalmazás használatához.

    > [!NOTE]
    > A szkript végrehajtásához az [Azure ad alkalmazás-rendszergazda szerepkör](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) tagjának kell lennie.
    
    - Módosítsa a PowerShell-parancsfájl összekapcsolási AzureAD hívását az Azure AD-bérlői azonosító használatára.
    - Módosítsa a PowerShell-parancsfájl változóját $myAzureADApplicationObjectId az Azure AD-alkalmazás objektumazonosítójának használatára
    - Futtassa a módosított parancsfájlt.
    
1. Konfigurálja a műveleti csoport biztonságos webhook műveletét.
    - Másolja a $myApp. ObjectId értéket a parancsfájlból, és adja meg a webhook műveleti definíciójának Application Object ID mezőjében.
    
    ![Biztonságos webhook művelet](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Webhook PowerShell-parancsfájl biztonságossá tétele

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
További fontos információk: az információk és az [SMS-riasztások viselkedésének](../../azure-monitor/platform/alerts-sms-behavior.md) [korlátozása](./../../azure-monitor/platform/alerts-rate-limiting.md) . 

A műveleti csoportban korlátozott számú SMS-művelet lehet.

> [!NOTE]
> Ha az Azure Portal Művelettípus felhasználói felülete nem teszi lehetővé az országkód kiválasztását, akkor az SMS nem támogatott az Ön országában.  Ha az országkód nem érhető el, szavazzon arra, hogy az országot felveszi a [felhasználói hangon](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). Addig is a megoldás, hogy a műveleti csoport meghívja a webhookot egy harmadik féltől származó SMS-szolgáltatóra, amely támogatja az országot.  

A támogatott országok díjszabását a [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)találja.
  

### <a name="voice"></a>Hang
További fontos viselkedésért tekintse meg a [díjszabási információkat](./../../azure-monitor/platform/alerts-rate-limiting.md) ismertető cikket.

A műveleti csoportban korlátozott számú hangművelet lehet.

> [!NOTE]
> Ha az Azure Portal műveleti csoport felhasználói felülete nem teszi lehetővé az országkód kiválasztását, akkor a hanghívások nem támogatottak az országában. Ha az országkód nem érhető el, szavazzon arra, hogy az országot felveszi a [felhasználói hangon](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  Addig is a megoldás, hogy a műveleti csoport meghívja a webhookot egy külső gyártótól származó hanghívási szolgáltatóra, amely támogatja az országot.  

A támogatott országok díjszabását a [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)találja.

### <a name="webhook"></a>Webhook
A webhookok a következő szabályok alapján lesznek újrapróbálkozva. A webhook hívása legfeljebb 2 alkalommal próbálkozik a következő HTTP-állapotkódok visszaadásakor: 408, 429, 503, 504 vagy a HTTP-végpont nem válaszol. Az első újrapróbálkozás 10 másodperc után történik. A második újrapróbálkozás 100 másodperc után történik. Két hiba után a műveleti csoport 30 percig nem fogja hívni a végpontot. 

Forrás IP-címtartományok
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Ha frissítéseket szeretne kapni ezen IP-címek változásairól, javasoljuk, hogy állítson be egy Service Health riasztást, amely figyeli a műveleti csoportok szolgáltatással kapcsolatos tájékoztató értesítéseket.

Előfordulhat, hogy egy műveleti csoportban korlátozott számú webhook-művelet van.



## <a name="next-steps"></a>További lépések
* További információ az [SMS-riasztás viselkedéséről](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Ismerkedjen meg [a tevékenység naplójának riasztása webhook sémával](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* További információ a [ITSM-csatoló](../../azure-monitor/platform/itsmc-overview.md)
* További információ a riasztások [díjszabásának korlátozásáról](../../azure-monitor/platform/alerts-rate-limiting.md) .
* [Tekintse át a tevékenységek naplójának riasztásait](../../azure-monitor/platform/alerts-overview.md), és Ismerje meg, hogyan fogadhat riasztásokat.  
* Megtudhatja, hogyan [konfigurálhatja a riasztásokat, amikor egy szolgáltatás állapotáról értesítést küldenek](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
