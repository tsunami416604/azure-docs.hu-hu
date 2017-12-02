---
title: "Az Azure-verem operátor PowerShell környezet konfigurálása |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálja az Azure-verem operátor PowerShell környezetet."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: sngun
ms.openlocfilehash: 54f0305afb94b8b19ea7fada837a73f7c6a1963c
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Az Azure-verem operátor PowerShell környezet konfigurálása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure verem kezelőként konfigurálhatja az Azure verem szoftverfejlesztői készlet PowerShell környezetben. Miután konfigurálta, a PowerShell használatával kezelheti az Azure-verem erőforrásokat, például létrehozása ajánlatokat, tervek, kvótákat, kezelése, riasztások stb. Ez a témakör a környezetben csak, ha a PowerShell beállítása a felhasználói környezet olvassa el a felhő üzemeltetője használandó hatóköre [konfigurálása az Azure-verem felhasználói PowerShell környezet](user/azure-stack-powershell-configure-user.md) témakör. 

## <a name="prerequisites"></a>Előfeltételek

Futtassa a következő előfeltételek származhatnak a [szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-en keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

* Telepítés [Azure verem-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).  
* Töltse le a [az Azure veremnek megfelelő működéséhez szükséges eszközök](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>A kezelő-környezet beállításait, és jelentkezzen be Azure verem

A központi telepítés (Azure AD vagy AD FS), futtassa a következő parancsfájlt, és az Azure-verem operátor környezet konfigurálása a PowerShell segítségével (Ügyeljen rá, hogy cserélje le a AAD tenantName, a GraphAudience végpont és az adott környezettől ArmEndpoint értékeket egyikét alapján a konfiguráció):

### <a name="azure-active-directory-aad-based-deployments"></a>Az Azure Active Directory (AAD)-alapú telepítések
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”


  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    
  Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience 

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackAdmin"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
  ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory összevonási szolgáltatások (AD FS)-alapú telepítések
         
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”


  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint


  # Get the Active Directory tenantId that is used to deploy Azure Stack     
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
  ```

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Most, hogy minden beállítással van, lehetővé PowerShell használatával létrehozni Azure verem erőforrásokat. Például hozzon létre egy erőforráscsoportot az alkalmazáshoz, és adja hozzá a virtuális gépet. Az alábbi parancs segítségével hozzon létre egy erőforráscsoportot a "Contoso.com" nevű:

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Következő lépések
* [Az Azure-verem sablonok fejlesztése](user/azure-stack-develop-templates.md)
* [Sablonok üzembe helyezése a PowerShell-lel](user/azure-stack-deploy-template-powershell.md)
