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
ms.date: 10/16/2017
ms.author: mabrigg
ms.openlocfilehash: 44a5030bd9dfb694968a6b0f64ff9198ecccd84a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Az Azure-verem felhasználói PowerShell környezet konfigurálása

Azure verem felhasználóként konfigurálhatja az Azure verem szoftverfejlesztői készlet PowerShell környezetben. Miután konfigurálta, PowerShell Azure verem ajánlatokat, például előfizetés erőforrások kezeléséhez használhatja virtuális gépek létrehozásához, központi telepítése Azure Resource Manager-sablonok, stb. Ez a témakör hatóköre a következő környezetekben csak, ha azt szeretné, hogy a felhőkörnyezet operátor PowerShell beállítása tekintse meg a felhasználó használja a [konfigurálása az Azure-verem operátor PowerShell környezet](../azure-stack-powershell-configure-admin.md) cikk. 

## <a name="prerequisites"></a>Előfeltételek 

Futtassa a következő előfeltételek származhatnak a [szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-en keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Telepítés [Azure verem-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).  
* Töltse le a [az Azure veremnek megfelelő működéséhez szükséges eszközök](azure-stack-powershell-download.md). 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>A felhasználói környezet beállításait, és jelentkezzen be Azure verem

Az alapján a központi telepítés (Azure AD vagy AD FS), PowerShell konfigurálása az Azure-vermet (Ügyeljen rá, hogy cserélje le a AAD tenantName, GraphAudience végpont és a környezet konfigurációjának megfelelően ArmEndpoint értékek) az alábbi parancsfájlok egyikét futtatja:

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
  Connect-AzureRmAccount `
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
  Connect-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
  ```

## <a name="register-resource-providers"></a>Erőforrás-szolgáltató regisztrálása

Ha egy újonnan létrehozott felhasználó előfizetés, amely nem rendelkezik olyan erőforrásokkal, a portálon keresztül telepített, a nem automatikusan regisztrálja az erőforrás-szolgáltató. Explicit módon regisztrálnia kell őket az alábbi parancsfájl használatával:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Most, hogy minden van beállításról, tegyük a PowerShell használatával létrehozni Azure verem erőforrásokat. Például hozzon létre egy erőforráscsoportot az alkalmazáshoz, és adja hozzá a virtuális gépet. Az alábbi parancs segítségével hozzon létre egy erőforráscsoportot a "Contoso.com" nevű:

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések
* [Az Azure-verem sablonok fejlesztése](azure-stack-develop-templates.md)
* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
