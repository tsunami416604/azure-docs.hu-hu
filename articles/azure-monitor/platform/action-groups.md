---
title: Műveletcsoportok létrehozása és kezelése az Azure Portalon
description: Ismerje meg, hogyan hozhat létre és kezelhet műveletcsoportokat az Azure Portalon.
author: dkamstra
ms.topic: conceptual
ms.date: 2/18/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 6ba48f3c40e45afa02e03a7589e968cca723118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249515"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Műveletcsoportok létrehozása és kezelése az Azure Portalon
A műveletcsoport az Azure-előfizetés tulajdonosa által meghatározott értesítési beállítások gyűjteménye. Az Azure Monitor és a Service Health riasztások műveletcsoportok használatával értesíti a felhasználókat, hogy egy riasztás tanusította. A felhasználó igényeitől függően különböző riasztások használhatják ugyanazt a műveletcsoportot vagy különböző műveletcsoportokat. Egy előfizetésben legfeljebb 2000 műveletcsoportot konfigurálhat.

Ha úgy állít be egy műveletet, hogy e-mailben vagy SMS-ben értesítsen egy személyt, akkor megerősítést kap arról, hogy hozzá lett adva a műveletcsoporthoz.

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet műveletcsoportokat az Azure Portalon.

Minden művelet a következő tulajdonságokból áll:

* **Név**: Egyedi azonosító a műveletcsoporton belül.  
* **Művelettípusa**: Az végrehajtott művelet. Ilyen például a hanghívás, sms, e-mail küldése; vagy különböző típusú automatizált műveleteket indít. Lásd a cikk későbbi típusait.
* **Részletek**: A megfelelő részletek, amelyek *művelettípusonként*változnak .

Az Azure Resource Manager-sablonok műveletcsoportok konfigurálásával kapcsolatos tudnivalókért olvassa el az [Erőforráskezelő-sablonok műveletcsoport-sablonjait.](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Műveletcsoport létrehozása az Azure Portal használatával

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a Figyelő parancsot, és válassza a **Figyelparancsot.** A **Figyelő** ablaktábla egyetlen nézetben összesíti az összes figyelési beállítást és adatot.

1. Válassza a **Riasztások**, majd a **Műveletek kezelése** lehetőséget.

    ![Műveletek kezelése gomb](./media/action-groups/manage-action-groups.png)
    
1. Válassza **a Műveletcsoport hozzáadása**lehetőséget, és töltse ki a mezőket.

    ![A "Műveletcsoport hozzáadása" parancs](./media/action-groups/add-action-group.png)
    
1. Írjon be egy nevet a **Műveletcsoport neve** mezőbe, és írjon be egy nevet a **Rövid név** mezőbe. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

      ![A Műveletcsoport hozzáadása párbeszédpanel](./media/action-groups/action-group-define.png)

1. Az **Előfizetés** mező automatikusan kitölti az aktuális előfizetését. Ez az előfizetés az, amelyben a műveletcsoport menti.

1. Jelölje ki azt az **erőforráscsoportot,** amelyben a műveletcsoportot menti.

1. Műveletek listájának meghatározása. Adja meg a következőket minden művelethez:

    1. **Név**: Adja meg a művelet egyedi azonosítóját.

    1. **Művelettípusa:** Válassza az E-mail/SMS/Push/Voice, logic app, webhook, ITSM vagy Automation Runbook lehetőséget.

    1. **Részletek:** A művelet típusa alapján adja meg a telefonszámot, e-mail-címet, webhook URI, Azure app, ITSM-kapcsolat, vagy Automation runbook. Az ITSM-művelethez adja meg a **Munkaelemet** és az ITSM eszköz által igényelt egyéb mezőket is.
    
    1. **Közös riasztási séma**: Engedélyezheti a [közös riasztási sémát,](https://aka.ms/commonAlertSchemaDocs)amely biztosítja azt az előnyt, hogy egyetlen bővíthető és egységes riasztási hasznos adataz Azure Monitor összes riasztási szolgáltatásában.

1. A műveletcsoport létrehozásához válassza az **OK gombot.**

## <a name="manage-your-action-groups"></a>A műveletcsoportok kezelése

Miután létrehozott egy műveletcsoportot, a **Műveletcsoportokat** úgy tekintheti meg, hogy a **Figyelő** ablaktábla **Riasztások** céllapján a **Műveletek kezelése** lehetőséget választja. Jelölje ki a kezelni kívánt műveletcsoportot:

* Műveletek hozzáadása, szerkesztése vagy eltávolítása.
* A műveletcsoport törlése.

## <a name="action-specific-information"></a>Műveletspecifikus információk

> [!NOTE]
> Lásd: [Előfizetési szolgáltatás korlátai figyelése](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) numerikus korlátok az egyes alábbi elemeket.  

### <a name="automation-runbook"></a>Automatizálási runbook
Tekintse meg az [Azure-előfizetési szolgáltatás korlátait](../../azure-resource-manager/management/azure-subscription-service-limits.md) a Runbook-rakományok korlátozásai.

Előfordulhat, hogy egy műveletcsoportban korlátozott számú Runbook-művelet van. 

### <a name="azure-app-push-notifications"></a>Azure alkalmazás leküldéses értesítései
Előfordulhat, hogy korlátozott számú Azure-alkalmazásművelettel rendelkezik egy műveletcsoportban.

### <a name="email"></a>E-mail
Az e-maileket a következő e-mail címekről küldjük el. Az e-mailek szűrésének megfelelő beállítása
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Előfordulhat, hogy egy műveletcsoportban korlátozott számú e-mail művelet et hajtanak végre. Tekintse meg a [sebességkorlátozó információkat](./../../azure-monitor/platform/alerts-rate-limiting.md) szóló cikket.

### <a name="email-azure-resource-manager-role"></a>E-mail az Azure Erőforrás-kezelő szerepkörrel
E-mail küldése az előfizetés szerepkörének tagjainak. E-mail ben csak a szerepkör **Azure AD-felhasználó** tagjai. E-mail nem küldi el az Azure AD-csoportok vagy egyszerű szolgáltatás.

Előfordulhat, hogy egy műveletcsoportban korlátozott számú e-mail művelet et hajtanak végre. Tekintse meg a [sebességkorlátozó információkat](./../../azure-monitor/platform/alerts-rate-limiting.md) szóló cikket.

### <a name="function"></a>Függvény
Megad egy meglévő HTTP-trigger végpontot az [Azure Functions ben.](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app)

Előfordulhat, hogy egy műveletcsoportban korlátozott számú függvényművelet van.

### <a name="itsm"></a>ITSM
Az ITSM-művelethez ITSM-kapcsolat szükséges. További információ az [ITSM-kapcsolat létrehozásáról.](../../azure-monitor/platform/itsmc-overview.md)

Előfordulhat, hogy egy műveletcsoportban korlátozott számú ITSM-művelet van. 

### <a name="logic-app"></a>Logikai alkalmazás
Előfordulhat, hogy egy műveletcsoportban korlátozott számú Logikai alkalmazás-művelet et hajtanak végre.

### <a name="secure-webhook"></a>Biztonságos webhook
A műveletcsoportok webhook művelet lehetővé teszi, hogy kihasználják az Azure Active Directory a műveletcsoport és a védett webes API (webhook-végpont) közötti kapcsolat biztonságossá tétele. A funkció kihasználásának általános munkafolyamatát az alábbiakban ismertetjük. Az Azure AD-alkalmazások és egyszerű szolgáltatásért tekintse meg a [Microsoft identity platform (2.0-s verzió) áttekintését.](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

1. Hozzon létre egy Azure AD-alkalmazást a védett webes API-hoz. Lásd: https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Konfigurálja a védett API-t egy démonalkalmazás által hívandó.
    
1. Engedélyezze a műveletcsoportokat az Azure AD-alkalmazás használatához.

    > [!NOTE]
    > A parancsfájl végrehajtásához az [Azure AD alkalmazás-rendszergazdai szerepkör](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) tagjának kell lennie.
    
    - Módosítsa a PowerShell-parancsfájl Connect-AzureAD-hívását az Azure AD-bérlői azonosító használatához.
    - A PowerShell-parancsfájl $myAzureADApplicationObjectId módosítása az Azure AD-alkalmazás objektumazonosítójának használatához
    - Futtassa a módosított parancsfájlt.
    
1. Konfigurálja a műveletcsoport biztonságos webhook művelet.
    - Másolja az $myApp.ObjectId értéket a parancsfájlból, és írja be a Webhook műveletdefiníció Alkalmazásobjektum-azonosító mezőjébe.
    
    ![Biztonságos Webhook művelet](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Biztonságos Webhook PowerShell-parancsfájl

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
További fontos információkért tekintse meg a [sebességkorlátozó információkat](./../../azure-monitor/platform/alerts-rate-limiting.md) és az [SMS-riasztások viselkedését.](../../azure-monitor/platform/alerts-sms-behavior.md)

Előfordulhat, hogy egy műveletcsoportban korlátozott számú SMS-művelet van.  

### <a name="voice"></a>Hang
Tekintse meg a [sebességkorlátozó információkat](./../../azure-monitor/platform/alerts-rate-limiting.md) szóló cikket.

Előfordulhat, hogy egy műveletcsoportban korlátozott számú Hangművelet van.

### <a name="webhook"></a>Webhook
A webhookokat a rendszer a következő szabályok szerint kísérli meg újra. A webhook-hívás legfeljebb 2 alkalommal, amikor a következő HTTP-állapotkódok at adja vissza: 408, 429, 503, 504 vagy a HTTP-végpont nem válaszol. Az első újrapróbálkozás 10 másodperc után történik. A második újrapróbálkozás 100 másodperc után történik. Két hiba után egyetlen műveletcsoport sem hívja meg a végpontot 30 percig. 

Forrás IP-címtartományai
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

Az IP-címek változásaival kapcsolatos frissítések fogadásához javasoljuk, hogy állítson be egy Service Health riasztást, amely a Műveletcsoportok szolgáltatással kapcsolatos információs értesítéseket figyeli.

Előfordulhat, hogy egy műveletcsoportban korlátozott számú Webhook-művelet van.



## <a name="next-steps"></a>További lépések
* További információ az [SMS-riasztások viselkedéséről.](../../azure-monitor/platform/alerts-sms-behavior.md)  
* A [tevékenységnapló-riasztási webhook-séma megismerése.](../../azure-monitor/platform/activity-log-alerts-webhook.md)  
* További információ az [ITSM-csatlakozóról](../../azure-monitor/platform/itsmc-overview.md)
* További információ a riasztások [sebességkorlátozásáról.](../../azure-monitor/platform/alerts-rate-limiting.md)
* Áttekintést [kaphat a tevékenységnapló-riasztásokról,](../../azure-monitor/platform/alerts-overview.md)és megtudhatja, hogyan fogadhat értesítéseket.  
* További információ arról, hogyan konfigurálhatja a [riasztásokat a szolgáltatás állapotáról szóló értesítés feladásakor.](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)
