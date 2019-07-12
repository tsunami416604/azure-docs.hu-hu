---
title: Az Azure Portalon a műveleti csoportok létrehozása és kezelése
description: Ismerje meg, hogyan hozhat létre és kezelheti az Azure Portalon Műveletcsoportok.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/08/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 842965aa49ae4cd546fe9c107107d2a2ceebebbb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705251"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Az Azure Portalon a műveleti csoportok létrehozása és kezelése
Műveletcsoport gyűjteménye, Azure-előfizetés tulajdonosa által megadott értesítési beállításokat. Azure Monitor és a Service Health-riasztások Műveletcsoportok használatával értesítheti a felhasználókat, hogy egy riasztás aktiválódott-e. Az azonos műveletcsoport vagy a felhasználó követelményeitől függően különböző Műveletcsoportok különböző riasztások használhatja. Az előfizetéshez legfeljebb 2000 Műveletcsoportok konfigurálásával.

Egy olyan műveletet, kapnak egy megerősítő jelző műveleti csoporthoz hozzáadott egy személy e-mailben vagy SMS-értesítés megadása

Ez a cikk bemutatja, hogyan hozhat létre és kezelheti az Azure Portalon Műveletcsoportok.

Minden művelet a következő tulajdonságok tevődik össze:

* **Név**: A műveletcsoport belül egyedi azonosítója.  
* **Művelet típusa**: A művelet végre. Ilyenek például a voice hívás, SMS, e-mailt küld; vagy automatizált műveletek különböző típusú elindítása. Tekintse meg a cikk későbbi részében típusokat.
* **Részletek**: A vonatkozó részletekkel, végezhet *művelettípus*.

Műveletcsoportok konfigurálása Azure Resource Manager-sablonok használatáról további információért lásd: [műveleti csoport Resource Manager-sablonok](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Műveletcsoport létrehozása az Azure portal használatával

1. Az a [az Azure portal](https://portal.azure.com)válassza **figyelő**. A **figyelő** ablaktábla összesíti az összes figyelési beállítást és adatokat egyetlen nézetben.

    ![A "Figyelés" szolgáltatás](./media/action-groups/home-monitor.png)
    
1. Válassza ki **riasztások** majd **kezelheti**.

    ![Műveletek gomb kezelése](./media/action-groups/manage-action-groups.png)
    
1. Válassza ki **műveleti csoport hozzáadása**, és töltse ki a mezőket.

    ![A "Csoport hozzáadása művelet" parancs](./media/action-groups/add-action-group.png)
    
1. Adjon meg egy nevet a a **műveletcsoport neve** mezőbe, majd adjon meg egy nevet a a **rövid, nevet** mezőbe. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

      ![A műveleti csoport hozzáadása"párbeszédpanel](./media/action-groups/action-group-define.png)

1. A **előfizetés** az aktuális előfizetéshez autofills mezőbe. Ebben az előfizetésben a műveletcsoport elmentett.

1. Válassza ki a **erőforráscsoport** a a műveletcsoport mentve.

1. Műveletek listájának meghatározását. Adja meg a következő műveletek:

    1. **Név**: Adja meg egy egyedi azonosítót ehhez a művelethez.

    1. **Művelet típusa**: Válassza ki az e-mailek és SMS és leküldéses/Hangvétel, a logikai alkalmazás, Webhookot, ITSM vagy Automation-Runbook.

    1. **Részletek**: A művelet típusa alapján, adjon meg egy telefonszám, e-mail címét, webhook URI-t, az Azure app, ITSM-kapcsolat vagy Automation-runbook. ITSM-művelet, továbbá adja meg **munkaelem** és a többi mező az ITSM-eszközhöz van szükség.
    
    1. **Gyakori riasztási séma**: Ha szeretné engedélyezni a [gyakori riasztási séma](https://aka.ms/commonAlertSchemaDocs), amely biztosítja az előnyt, hogy a bővíthető egyetlen és egységes riasztási hasznos között az összes riasztás services, az Azure monitorban.

1. Válassza ki **OK** a műveletcsoport létrehozásához.

## <a name="manage-your-action-groups"></a>A műveleti csoportok kezelése

Miután létrehozta a műveletcsoport, is látható, az a **Műveletcsoportok** szakaszában a **figyelő** ablaktáblán. Válassza ki a kezelni kívánt műveletcsoport:

* Adja hozzá, szerkeszthet és eltávolíthat műveleteket.
* A műveletcsoport törlése.

## <a name="action-specific-information"></a>A művelet adott információk

> [!NOTE]
> Lásd: [előfizetési szolgáltatási korlátok a figyelés](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) numerikus korlátait az alábbi elemek mindegyike számára.  

### <a name="azure-app-push-notifications"></a>Azure-alkalmazás leküldéses értesítések
Az Azure app-műveletek csak korlátozott számú műveletcsoport lehet.

### <a name="email"></a>Email
E-mailt küld a következő e-mail-címekről. Győződjön meg arról, hogy az e-mailek szűrése megfelelően vannak konfigurálva
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Előfordulhat, hogy e-mailes műveletek csak korlátozott számú tartalmaz műveletcsoport. Tekintse meg a [sebessége korlátozza az információk](./../../azure-monitor/platform/alerts-rate-limiting.md) cikk.

### <a name="itsm"></a>ITSM
ITSM-művelethez egy ITSM-kapcsolat szükséges. Ismerje meg, hogyan hozhat létre egy [ITSM-kapcsolat](../../azure-monitor/platform/itsmc-overview.md).

ITSM-műveletek csak korlátozott számú műveletcsoport lehet. 

### <a name="logic-app"></a>Logikai alkalmazás
Előfordulhat, hogy a Logic App-műveletek csak korlátozott számú a műveletcsoport.

### <a name="function"></a>Függvény
A Functions API-val, amelyhez jelenleg a függvényalkalmazások v2 "AzureWebJobsSecretStorageType" beállítást "fájlok" az alkalmazás konfigurálásához szükséges műveletek konfigurált alkalmazások esetében a funkcióbillentyűket olvasható. További információkért lásd: [kulcskezelés a Functions V2 vált]( https://aka.ms/funcsecrets).

Előfordulhat, hogy a függvény műveletek csak korlátozott számú a műveletcsoport.

### <a name="automation-runbook"></a>Automation-Runbook
Tekintse meg a [az Azure-előfizetési szolgáltatási korlátok](../../azure-subscription-service-limits.md) a korlátok a Runbook is észleltünk adattartalmakat.

Előfordulhat, hogy a Runbook-műveletek csak korlátozott számú a műveletcsoport. 

### <a name="sms"></a>SMS
Tekintse meg a [sebessége korlátozza az információk](./../../azure-monitor/platform/alerts-rate-limiting.md) és [SMS-riasztás viselkedése](../../azure-monitor/platform/alerts-sms-behavior.md) további fontos információkat.

Előfordulhat, hogy az SMS-műveletek csak korlátozott számú tartalmaz műveletcsoport.  

### <a name="voice"></a>Hang
Tekintse meg a [sebessége korlátozza az információk](./../../azure-monitor/platform/alerts-rate-limiting.md) cikk.

Műveletcsoport hangalapú műveletek csak korlátozott számú lehet.

### <a name="webhook"></a>Webhook
Webhookok a rendszer újra próbálkozik a következő szabályok alkalmazásával. A webhook hívása rendszer legfeljebb 2 idők esetén a következő HTTP-állapotkódok adja vissza: 408, 429, 503, 504, illetve ha a HTTP-végpont nem válaszol. Az első újrapróbálkozás 10 másodperc után történik. A második újrapróbálkozási 100 másodperc múlva történik. Két hiba után nincs műveletcsoport meghívja a végpont 30 percig. 

Forrás IP-címtartományok
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Ezen IP-címek érintő változásokról a frissítések fogadásához javasoljuk egy szolgáltatás állapotát figyeli a tájékoztató értesítések, a Műveletcsoportok szolgáltatással kapcsolatos riasztás konfigurálja.

Előfordulhat, hogy a Webhook-műveletek csak korlátozott számú a műveletcsoport.

#### <a name="secure-webhook"></a>Secure Webhook
**A Webhook biztonságos a funkció jelenleg előzetes verzióban érhető el.**

A műveleti csoportok Webhook művelettel lehetővé teszi, hogy igénybe vehesse az Azure Active Directory biztonságossá tételéhez a műveleti csoport és a védett webes API-t (webhook-végpontot) közötti kapcsolat. A teljes munkafolyamat esetében ez a funkció kihasználásával az alábbiakban ismertetjük. Az Azure AD-alkalmazások és az egyszerű szolgáltatások áttekintését lásd: [a Microsoft identity platform (2.0-s verzió) – áttekintés](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Hozzon létre egy Azure AD-alkalmazást a védett webes API-hoz. Lásd: https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Konfigurálja a védett API meghívása egy démon alkalmazást.
    
1. Műveletcsoportok használata az Azure AD-alkalmazás engedélyezése.

    > [!NOTE]
    > Tagjának kell lennie a [Azure AD alkalmazás-rendszergazda szerepkör](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) Ez a szkript végrehajtásához.
    
    - Módosítása a PowerShell-parancsfájl Connect-AzureAD hívás használata az Azure AD-bérlő azonosítóját.
    - Módosítsa a PowerShell-parancsfájl változót $myAzureADApplicationObjectId Objektumazonosítóját az Azure AD-alkalmazás használata
    - Futtassa a módosított szkriptet.
    
1. Konfigurálja a műveleti csoport Webhook művelettel.
    - Az érték $myApp.ObjectId másolja a szkriptet, és írja be az alkalmazásobjektum azonosítója a mező a Webhook művelet definíciójában.
    
    ![Biztonságos Webhook művelettel](./media/action-groups/action-groups-secure-webhook.png)

##### <a name="secure-webhook-powershell-script"></a>Biztonságos Webhook PowerShell-parancsfájl

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


## <a name="next-steps"></a>További lépések
* Tudjon meg többet [SMS-riasztás viselkedése](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Szerezzen egy [megértése a tevékenység log riasztási webhookséma](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Tudjon meg többet [ITSM-összekötő](../../azure-monitor/platform/itsmc-overview.md)
* Tudjon meg többet [sebességkorlátozással](../../azure-monitor/platform/alerts-rate-limiting.md) a riasztásokat.
* Get- [tevékenységnapló-riasztások áttekintése](../../azure-monitor/platform/alerts-overview.md), és a riasztások fogadása.  
* Ismerje meg, hogyan [riasztások konfigurálása, ha a szolgáltatás állapotával kapcsolatos értesítés közzétételekor](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
