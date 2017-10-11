---
title: "Az Azure Media Services API eléréséhez az Azure AD-alkalmazás létrehozása a PowerShell használatával |} Microsoft Docs"
description: "Tudnivalók a PowerShell segítségével hozzon létre egy Azure Active Directory (Azure AD) alkalmazást, és állítsa be a hozzáférés az Azure Media Services API."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: eea0f3a03dd77ce56484f32b192299bd97c05300
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Az Azure Media Services API használata az Azure AD-alkalmazás létrehozása a PowerShell használatával

Útmutató az Azure Media Services-erőforrások eléréséhez Azure Active Directory (Azure AD) alkalmazás és egyszerű szolgáltatás létrehozása a PowerShell parancsfájl segítségével.  

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nincs fiókja, kezdje egy [Azure ingyenes próbát](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. További információkért lásd: [Azure Media Services-fiók létrehozása az Azure portálon](media-services-portal-create-account.md).
- Az Azure PowerShell-verzió 0.8.8 vagy újabb verziója. További információkért lásd: [Azure PowerShell használata](https://docs.microsoft.com/powershell/azure/overview).
- Az Azure Resource Manager parancsmagok.  

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Az Azure AD-alkalmazás létrehozása a PowerShell használatával  

```powershell
Login-AzureRmAccount
Import-Module AzureRM.Resources
Set-AzureRmContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

További információkért tekintse át a következő cikkeket:

- [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával](../azure-resource-manager/resource-group-authenticate-service-principal.md)
- [Szerepköralapú hozzáférés-vezérlés kezelése az Azure PowerShell használatával](../active-directory/role-based-access-control-manage-access-powershell.md)
- [Saját kezű konfigurálásáról démon alkalmazások tanúsítványok használatával](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Következő lépések

Ismerkedés a [fájlok feltöltése a fiókjához](media-services-portal-upload-files.md).
