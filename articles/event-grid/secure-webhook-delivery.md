---
title: Biztonságos webhook-kézbesítés az Azure AD-vel Azure Event Grid
description: Ismerteti, hogyan lehet eseményeket kézbesíteni a Azure Active Directory által védett HTTPS-végpontoknak Azure Event Grid használatával
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: b0503d7da9e191e9d6764076392ead8faa5109a1
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119123"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Események közzététele Azure Active Directory védett végpontok számára

Ez a cikk azt ismerteti, hogyan használhatja a Azure Active Directory az esemény-előfizetés és a webhook-végpont közötti kapcsolat biztonságossá tételéhez. Az Azure AD-alkalmazások és-szolgáltatások áttekintését lásd: [Microsoft Identity platform (v 2.0) – áttekintés](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

Ez a cikk a bemutató Azure Portal használja, de a funkció a CLI, a PowerShell vagy az SDK-k használatával is engedélyezhető.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Azure AD-alkalmazás létrehozása

Először hozzon létre egy Azure AD-alkalmazást a védett végpont számára. Lásd: https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Beállíthatja, hogy a védett API-t egy Daemon-alkalmazás hívja meg.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Az Azure AD-alkalmazás használatának engedélyezése Event Grid

Az alábbi PowerShell-szkripttel hozzon létre egy szerepkört és egy szolgáltatásnevet az Azure AD-alkalmazásban. Az Azure AD-alkalmazásban szüksége lesz a bérlői AZONOSÍTÓra és az objektum-AZONOSÍTÓra:

   > [!NOTE]
   > A szkript végrehajtásához az [Azure ad alkalmazás-rendszergazda szerepkör](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) tagjának kell lennie.
    
1. Módosítsa a PowerShell-parancsfájl $myTenantIdét az Azure AD-bérlő AZONOSÍTÓjának használatára.
1. A PowerShell-parancsfájl $myAzureADApplicationObjectId módosítása az Azure AD-alkalmazás objektumazonosítójának használatára
1. Futtassa a módosított parancsfájlt.

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
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
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
Write-Host "My Azure AD Application's Roles: "
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Az esemény-előfizetés konfigurálása

Az esemény-előfizetés létrehozási folyamatában válassza a "web Hook" típusú végpontot. Miután megadta a végponti URI-t, kattintson az esemény-előfizetések létrehozása panel tetején található további szolgáltatások fülre.

![Válassza ki a végpont típusát webhook](./media/secure-webhook-delivery/select-webhook.png)

A további szolgáltatások lapon jelölje be a "HRE-hitelesítés használata" jelölőnégyzetet, és konfigurálja a bérlői azonosítót és az alkalmazás AZONOSÍTÓját:

* Másolja az Azure AD-bérlő AZONOSÍTÓját a parancsfájl kimenetéről, és adja meg a HRE-bérlő azonosítója mezőben.
* Másolja az Azure AD-alkalmazás AZONOSÍTÓját a parancsfájl kimenetéről, majd adja meg a HRE alkalmazás-azonosító mezőjében.

    ![Biztonságos webhook művelet](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítésének figyeléséről: [Event Grid üzenet kézbesítésének figyelése](monitor-event-delivery.md).
* További információ a hitelesítési kulcsról: [Event Grid biztonság és hitelesítés](security-authentication.md).
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
