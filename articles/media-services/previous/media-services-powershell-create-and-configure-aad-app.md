---
title: Az Azure Media Services API eléréséhez az Azure AD-alkalmazás létrehozása a PowerShell használatával |} A Microsoft Docs
description: Útmutató a PowerShell segítségével hozzon létre egy Azure Active Directory (Azure AD-) alkalmazást, és állítsa be a hozzáférést az Azure Media Services API.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 2aa777cf0c2068a5ee2382a9e75fc65db8a27207
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957301"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Az Azure Media Services API használata az Azure AD-alkalmazás létrehozása a PowerShell használatával

Ismerje meg, hogyan hozhat létre az Azure Media Services-erőforrások eléréséhez az Azure Active Directory (Azure AD-) alkalmazás és egyszerű szolgáltatás egy PowerShell-parancsfájlt használja.  

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik fiókkal, kezdje egy [Azure ingyenes próbaverziója](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. További információkért lásd: [Azure Media Services-fiók létrehozása az Azure Portalon](media-services-portal-create-account.md).
- Az Azure PowerShell-verzió 0.8.8 vagy újabb verziója. További információkért lásd: [használata az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview).
- Az Azure Resource Manager-parancsmagokkal.  

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Az Azure AD-alkalmazás létrehozása a PowerShell használatával  

```powershell
Connect-AzureRmAccount
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

- [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Szerepköralapú hozzáférés-vezérlés kezelése az Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)
- [Démon alkalmazások konfigurálásáról tanúsítványok használatával](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>További lépések

Ismerkedés a [fájlokat tölthet fel a fiók](media-services-portal-upload-files.md).
