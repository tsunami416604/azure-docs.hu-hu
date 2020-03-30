---
title: Biztonságos WebHook-kézbesítés az Azure AD-vel az Azure Event Gridben
description: Ez a témakör azt ismerteti, hogy miként lehet eseményeket kézbesíteni az Azure Active Directory által az Azure Event Grid használatával védett HTTPS-végpontokra
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 074378668b0516936e11968ea8c800d3daa667bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931544"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Események közzététele az Azure Active Directory által védett végpontokon

Ez a cikk ismerteti, hogyan azure Active Directory az esemény-előfizetés és a webhook-végpont közötti kapcsolat biztonságossá tétele. Az Azure AD-alkalmazások és egyszerű szolgáltatásért tekintse meg a [Microsoft identity platform (2.0-s verzió) áttekintését.](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

Ez a cikk az Azure Portalon a bemutató, azonban a funkció is engedélyezhető cli, PowerShell vagy az SDK-k használatával.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Azure AD-alkalmazás létrehozása

Kezdje azzal, hogy létrehoz egy Azure AD-alkalmazást a védett végponthoz. Lásd: https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Konfigurálja a védett API-t egy démonalkalmazás által hívandó.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Az Event Grid engedélyezése az Azure AD-alkalmazás használatához

Használja az alábbi PowerShell-parancsfájlt egy szerepkör- és szolgáltatáselv létrehozásához az Azure AD-alkalmazásban. Szüksége lesz a bérlői azonosítóra és az objektumazonosítóra az Azure AD-alkalmazásból:

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Módosítsa a PowerShell-parancsfájl $myTenantId az Azure AD-bérlői azonosító használatához.
1. A PowerShell-parancsfájl $myAzureADApplicationObjectId módosítása az Azure AD-alkalmazás objektumazonosítójának használatához
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
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Az esemény-előfizetés konfigurálása

Az esemény-előfizetés létrehozási folyamatában válassza a "Web Hook" végponttípust. Miután megadta a végpont URI-ját, kattintson a további funkciók fülre az esemény-előfizetések létrehozása panel tetején.

![Végponttípusú webhook kiválasztása](./media/secure-webhook-delivery/select-webhook.png)

A további szolgáltatások lapon jelölje be az "AAD-hitelesítés használata" jelölőnégyzetet, és konfigurálja a bérlői azonosítót és az alkalmazásazonosítót:

* Másolja az Azure AD-bérlői azonosítót a parancsfájl kimenetéről, és adja meg azt az AAD-bérlői azonosító mezőbe.
* Másolja az Azure AD-alkalmazás azonosítóját a parancsfájl kimenetéről, és írja be az AAD-alkalmazásazonosító mezőbe.

    ![Biztonságos Webhook művelet](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>További lépések

* Az eseménykézbesítésfigyelésről az [Eseményrács üzenetkézbesítésének figyelése](monitor-event-delivery.md)című témakörben talál további információt.
* A hitelesítési kulcsról további információt az [Event Grid biztonsága és hitelesítése](security-authentication.md)című témakörben talál.
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.
