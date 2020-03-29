---
title: Azure Media Services API eléréséhez hozzon létre egy Azure AD-alkalmazást a PowerShell használatával | Microsoft dokumentumok
description: Megtudhatja, hogy miként hozhat létre egy Azure Active Directory (Azure AD) alkalmazást, és hogyan állíthatja be az Azure Media Services API-jának eléréséhez.
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
ms.openlocfilehash: ff7f8bc27d358c667b10c0bd3383e78b20494303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64680132"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>A PowerShell használatával azure AD-alkalmazást hozhat létre az Azure Media Services API-val való használatra

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Megtudhatja, hogyan hozhat létre egy Azure Active Directory (Azure AD) alkalmazást és egyszerű szolgáltatást az Azure Media Services-erőforrások eléréséhez Egy PowerShell-parancsfájl használatával.  

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik fiókkal, kezdje egy [ingyenes Azure-próbaverzióval.](https://azure.microsoft.com/pricing/free-trial/) 
- Egy Media Services-fiók. További információ: [Azure Media Services-fiók létrehozása az Azure Portalon.](media-services-portal-create-account.md)

- Azure PowerShell. További információ: [Az Azure PowerShell használata.](https://docs.microsoft.com/powershell/azure/overview)

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
- [Szerepköralapú hozzáférés-vezérlés kezelése az Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)
- [A démonalkalmazások manuális konfigurálása tanúsítványok használatával](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>További lépések

Kezdje el feltölteni a [fájlokat a fiókjába.](media-services-portal-upload-files.md)
