---
title: PowerShell telepítése és konfigurálása az Azure Stack gyors |} A Microsoft Docs
description: Ismerje meg a telepítése és konfigurálása a PowerShell az Azure Stackhez.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: mabrigg
ms.openlocfilehash: 75b7f9c78418883344ce3c066135fe0847f649ac
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981969"
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Első lépésekhez a PowerShell-lel az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A rövid útmutató segítségével telepítését és a egy Azure Stack-környezet konfigurálása a PowerShell használatával. A parancsfájl, amely ebben a cikkben kínálunk hatókörét a **Azure Stack-operátorokról** csak.

Ebben a cikkben ismertetett lépéseket a rövidített változata a [PowerShell telepítése]( azure-stack-powershell-install.md), [eszközök]( azure-stack-powershell-download.md), és [az Azure Stack-operátorokrólPowerShell-környezetkonfigurálása]( azure-stack-powershell-configure-admin.md) cikkeket. Ez a cikk a parancsfájlok használatával állíthat be PowerShell az Azure Active Directoryval vagy az Active Directory összevonási szolgáltatások (AD FS) üzembe helyezett Azure Stack-környezetekhez.  

## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Az Azure Active Directory-alapú környezetekben a PowerShell beállítása  

Jelentkezzen be az Azure Stack fejlesztői készletet, vagy egy Windows-alapú külső ügyfél Ha VPN-kapcsolaton keresztül kapcsolódik. Nyisson meg egy rendszergazda jogú PowerShell ISE-munkamenetet, és futtassa az alábbi parancsfájlok.

Győződjön meg arról, hogy frissítette a **TenantName**, **ArmEndpoint**, és **GraphAudience** változók szükség szerint az Ön környezetének konfigurációját:

```PowerShell  
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following commands:
Get-Module -ListAvailable -Name Azure* | Uninstall-Module
Get-Module Azs.* -ListAvailable | Uninstall-Module -force

# Install PowerShell for Azure Stack.
Install-Module -Name AzureRm.BootStrapper -Force
```

Az API-profil és a rendszergazda az Azure Stack verziójának moduljának betöltése.

  - Az Azure Stack 1808 vagy újabb verziója.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.5.0 -Force
  ```

  - Az Azure Stack 1807 vagy valamely korábbi rendszerrel.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.4.0 -Force
  ```

  - Az Azure Stack 1804 vagy valamely korábbi rendszerrel.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force
  ```

Töltse le az Azure Stack-eszközöket, és csatlakozzon.

```PowerShell  
# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

expand-archive master.zip -DestinationPath . -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId -AADTenantName $TenantName -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>PowerShell beállítása az AD FS-alapú központi telepítésekhez

Az alábbi parancsfájlt is használhatja, ha az Azure Stack, ha az internethez csatlakozó működik. Azonban ha az Azure Stack internetkapcsolat nélkül működnek, használja a [leválasztott telepítésére a PowerShell](azure-stack-powershell-install.md) és a konfigurálása a PowerShell-parancsmagok megmarad ugyanaz a szkriptben látható módon. Jelentkezzen be az Azure Stack fejlesztői készletet, vagy egy Windows-alapú külső ügyfél Ha VPN-kapcsolaton keresztül kapcsolódik. Nyisson meg egy rendszergazda jogú PowerShell ISE-munkamenetet, és futtassa az alábbi parancsfájlt. Győződjön meg arról, hogy frissítette a **ArmEndpoint** és **GraphAudience** változók szükség szerint az Ön környezetének konfigurációját:

```PowerShell  

# Set the module repository and the execution policy.
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module -Name AzureRm.BootStrapper -Force
```

Az API-profil és a rendszergazda az Azure Stack verziójának moduljának betöltése.

  - Az Azure Stack 1808 vagy újabb verziója.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ````

  - Az Azure Stack 1807 vagy valamely korábbi rendszerrel.

    > [!Note]  
    Frissítse a 1.2.11 verzióját, lásd: a [áttelepítési útmutató](https://aka.ms/azspowershellmigration).

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - Az Azure Stack 1804 vagy valamely korábbi rendszerrel.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.3.0
    ````

Töltse le az Azure Stack-eszközöket, és csatlakozzon.

```PowerShell  
# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

expand-archive master.zip -DestinationPath . -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Most, hogy konfigurálta a PowerShell, a konfiguráció tesztelheti hozzon létre egy erőforráscsoportot:

```PowerShell  
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> Adjon meg egy erőforráscsoportot, szüksége lesz rendelkezik erőforráscsoporttal az előfizetésében. Az előfizetésekkel kapcsolatos további információkért lásd: [csomag, ajánlat, kvóta és előfizetés áttekintése](azure-stack-plan-offer-quota-overview.md)

Az erőforráscsoport létrehozása után a **üzembe helyezési állapota** tulajdonsága **sikeres**.

## <a name="next-steps"></a>További lépések

 - [Telepítse és konfigurálja a parancssori felület](azure-stack-connect-cli.md)
 - [Sablonok fejlesztése](user/azure-stack-develop-templates.md)
