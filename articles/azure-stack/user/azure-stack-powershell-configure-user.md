---
title: Az Azure-verem felhasználói PowerShell környezet beállítása |} Microsoft Docs
description: Az Azure-verem felhasználói PowerShell környezet konfigurálása
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/15/2018
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: 2655b682d35dd1879c649ed58d524ecd80808896
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258022"
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Az Azure-verem felhasználói PowerShell környezet konfigurálása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A PowerShell-környezet egy Azure verem felhasználó konfigurálásához kövesse a cikkben a utasításokat.
Miután konfigurálta a környezetben, használhatja a PowerShell verem Azure-erőforrások kezeléséhez. Például a PowerShell használatával is ajánlatok előfizetni, virtuális gépek létrehozása és telepítése Azure Resource Manager-sablonok.

>[!NOTE]
>Ez a cikk hatóköre Azure verem felhasználói környezetben. Ha a felhőkörnyezet operátor PowerShell beállítása, olvassa el a [konfigurálása az Azure-verem operátor PowerShell környezet](../azure-stack-powershell-configure-admin.md) cikk.

## <a name="prerequisites"></a>Előfeltételek

Beállíthatja, hogy az ezekről az előfeltételekről a [szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-en keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Telepítés [Azure verem-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).
* Töltse le a [az Azure veremnek megfelelő működéséhez szükséges eszközök](azure-stack-powershell-download.md).

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>A felhasználói környezet beállításait, és jelentkezzen be Azure verem

Azure verem a központi telepítés (Azure AD vagy AD FS), futtassa az alábbi parancsfájlok PowerShell konfigurálása az Azure-verem típusa alapján.

Ellenőrizze, hogy a következő parancsfájl-változókat cserélje a Azure verem konfigurációból értékeket:

* Az AAD tenantName
* GraphAudience végpont
* ArmEndpoint

### <a name="azure-active-directory-aad-based-deployments"></a>Az Azure Active Directory (AAD)-alapú telepítések

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory összevonási szolgáltatások (AD FS)-alapú telepítések

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
  ```

## <a name="register-resource-providers"></a>Erőforrás-szolgáltató regisztrálása

Erőforrás-szolgáltató nem automatikusan regisztrálja az új felhasználói előfizetések, amelyek nem rendelkeznek a portálon keresztül telepített erőforrásokat. Egy erőforrás-szolgáltató a következő parancsfájl futtatásával explicit módon regisztrálhatja:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Van minden állítsa be, amikor a kapcsolat tesztelése erőforrások létrehozásához Azure verem a PowerShell használatával. Egy tesztet hozzon létre egy erőforráscsoportot az alkalmazás, és adja hozzá a virtuális gépet. Futtassa a "Contoso.com" nevű erőforráscsoport létrehozásához a következő parancsot:

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések

* [Az Azure-verem sablonok fejlesztése](azure-stack-develop-templates.md)
* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
