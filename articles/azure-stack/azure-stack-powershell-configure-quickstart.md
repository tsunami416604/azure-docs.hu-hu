---
title: "PowerShell telepítése és konfigurálása az Azure-verem gyors üzembe helyezés |} Microsoft Docs"
description: "További tudnivalók a telepítése és beállítása a PowerShell Azure verem."
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
ms.date: 10/24/2017
ms.author: sngun
ms.openlocfilehash: 039806e164be29b80e604bbcf0f2997e635664e5
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Amelyekből megismerheti a verem Azure PowerShell használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A gyors üzembe helyezés telepítése és konfigurálása az Azure-verem környezet a PowerShell segítségével. A parancsfájl, amely ebben a cikkben nyújtunk hatókörét a **Azure verem operátor** csak.

Ebben a cikkben ismertetett lépéseit tömörített verziója telepítve a [PowerShell telepítése]( azure-stack-powershell-install.md), [eszközök]( azure-stack-powershell-download.md), és [az Azure-verem operátor PowerShell környezetkonfigurálása]( azure-stack-powershell-configure-admin.md) cikkeket. Ez a témakör a parancsfájlok használatával állíthat be PowerShell Azure Active Directory vagy Active Directory összevonási szolgáltatások (AD FS) üzembe helyezett Azure verem környezetekben.  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Azure Active Directory-alapú telepítések PowerShell beállítása

Jelentkezzen be a Azure verem szoftverfejlesztői készlet, vagy a Windows-alapú külső ügyfél Ha VPN-en keresztül kapcsolódik. Nyisson meg egy emelt szintű PowerShell ISE-munkamenetet, és futtassa az alábbi parancsfájlt. Ne felejtse el frissíteni a **TenantName**, **ArmEndpoint**, és **GraphAudience** a környezet konfigurálása a megfelelő változók:

> [!IMPORTANT]
> A kiadási AzureRM 1.2.11 PowerShell modul megtörje a változások listájának tartalmaz. A 1.2.10 rendszerről verzióját, tekintse meg a [áttelepítési útmutató](https://aka.ms/azspowershellmigration).

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

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
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>AD FS-alapú telepítések PowerShell beállítása 

Jelentkezzen be a Azure verem szoftverfejlesztői készlet, vagy a Windows-alapú külső ügyfél Ha VPN-en keresztül kapcsolódik. Nyisson meg egy emelt szintű PowerShell ISE-munkamenetet, és futtassa az alábbi parancsfájlt. Ne felejtse el frissíteni a **ArmEndpoint** és **GraphAudience** a környezet konfigurálása a megfelelő változók:

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

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

Most, hogy a PowerShell konfigurálását, hozzon létre egy erőforráscsoportot tesztelheti a konfiguráció:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

Az erőforráscsoport létrehozása után a **üzembe helyezési állapota** tulajdonsága **sikeres**.

## <a name="next-steps"></a>Következő lépések

* [Telepítse és konfigurálja a parancssori felület](azure-stack-connect-cli.md)

* [Sablonok fejlesztése](user/azure-stack-develop-templates.md)







