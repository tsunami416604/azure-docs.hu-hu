---
title: Azure AD-alkalmazás létrehozása a PowerShell használatával a Azure Media Services API eléréséhez | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Azure Active Directory (Azure AD-) alkalmazást a PowerShell használatával, és hogyan állíthatja be a Azure Media Services API eléréséhez.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: a8339ed5f6b570f392a76d8e4109f015c951b9df
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065604"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>A PowerShell használatával hozzon létre egy Azure AD-alkalmazást a Azure Media Services API-val való használatra

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-from-v2-to-v3.md)

Megtudhatja, hogyan használhat PowerShell-parancsfájlt Azure Active Directory (Azure AD) alkalmazás és egyszerű szolgáltatásnév létrehozásához Azure Media Services erőforrásainak eléréséhez.  

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik fiókkal, kezdje egy [ingyenes Azure-próbaverzióval](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. További információ: [Azure Media Services fiók létrehozása a Azure Portalban](media-services-portal-create-account.md).

- Azure PowerShell. További információ: [a Azure PowerShell használata](/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Azure AD-alkalmazás létrehozása a PowerShell használatával  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

További információkért tekintse át a következő cikkeket:

- [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Szerepköralapú Access Control kezelése Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)
- [Daemon-alkalmazások manuális konfigurálása tanúsítványok használatával](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)

## <a name="next-steps"></a>További lépések

Ismerkedés a [fájlok feltöltésével a fiókjába](media-services-portal-upload-files.md).
