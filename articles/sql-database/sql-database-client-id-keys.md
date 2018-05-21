---
title: Értékek lekérése app hitelesítés – az Azure SQL Database |} Microsoft Docs
description: Hozzon létre egy egyszerű szolgáltatást kód SQL-adatbázis eléréséhez.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: f939f059ce2777eb32c869d8a18994da1f9873de
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Az alkalmazás kódjában SQL-adatbázis eléréséhez szükséges értékek beolvasása
Létrehozásához és kezeléséhez az SQL-adatbázis kódból regisztrálnia kell az az alkalmazás hol létrejöttek-e az Azure-erőforrások Azure Active Directory (AAD) tartományban az előfizetést.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Egy egyszerű szolgáltatásnév létrehozása hozzáférését az erőforrásokhoz az adott alkalmazásból
Telepíteni kell a legújabb [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) telepíteni és futtatni. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

A következő PowerShell-parancsprogram létrehozza az Active Directory (AD)-alkalmazást, és a C#-alkalmazás hitelesítéséhez szükséges egyszerű szolgáltatást. A parancsfájl kimenetének értékeire szükség lesz az előbb említett C#-mintához. Részletes információk: [Egyszerű szolgáltatás létrehozása erőforrások eléréséhez az Azure PowerShell használatával](../azure-resource-manager/resource-group-authenticate-service-principal.md).

    # Sign in to Azure.
    Connect-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Lásd még
* [SQL-adatbázis létrehozása a C#](sql-database-get-started-csharp.md)
* [Csatlakozás az SQL Database az Azure Active Directory-hitelesítés használatával](sql-database-aad-authentication.md)

