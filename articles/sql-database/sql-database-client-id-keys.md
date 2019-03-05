---
title: Alkalmazás-hitelesítés – Azure SQL Database értékek beolvasása |} A Microsoft Docs
description: Egyszerű szolgáltatás létrehozása a code-ból SQL-adatbázis eléréséhez.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/23/2018
ms.openlocfilehash: 6c4c001fc538e5ad93a5c4fc3d6405209be7fc53
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309366"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Code-ból SQL-adatbázis eléréséhez alkalmazás hitelesítéséhez szükséges értékek beolvasása

Létrehozásához és kezeléséhez az SQL Database-kódból regisztrálnia kell az alkalmazását az Azure Active Directory (AAD) tartományban az előfizetésben, az Azure-erőforrások létrejöttek.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Egyszerű szolgáltatás létrehozása erőforrások eléréséhez az alkalmazásból

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Szüksége lesz a legújabb [Azure PowerShell-lel](/powershell/azure) telepíteni és futtatni. Részletes információkért lásd: [Azure PowerShell telepítése](/powershell/azure/install-az-ps).

A következő PowerShell-parancsprogram létrehozza az Active Directory (AD)-alkalmazást, és a C#-alkalmazás hitelesítéséhez szükséges egyszerű szolgáltatást. A parancsfájl kimenetének értékeire szükség lesz az előbb említett C#-mintához. Részletes információk: [Egyszerű szolgáltatás létrehozása erőforrások eléréséhez az Azure PowerShell használatával](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

    # Sign in to Azure.
    Connect-AzAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Lásd még
* [SQL-adatbázis létrehozása a C# használatával](sql-database-get-started-csharp.md)
* [Csatlakozás az SQL Database az Azure Active Directory-hitelesítés használatával](sql-database-aad-authentication.md)

