---
title: Csatlakozhat az Azure Stack felhasználói PowerShell használatával |} A Microsoft Docs
description: A felhasználó Azure Stack-példányhoz való csatlakozáshoz lépéseket.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2018
ms.author: sethm
ms.reviewer: Balsu.G
ms.openlocfilehash: acdad9788737f4f552cedc1b5f42e03e2288dba8
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159079"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Csatlakozás a PowerShell-lel az Azure Stack felhasználói

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A cikk ismerteti a lépéseket az Azure Stack-példányhoz való csatlakozáshoz. Csatlakoznia kell a PowerShell-lel az Azure Stack-erőforrások kezeléséhez. Ha például a PowerShell használatával is előfizethetnek azokra, virtuális gépek létrehozása és üzembe helyezése Azure Resource Manager-sablonok. PowerShell-parancsmagok végrehajtásához.

Beállításához:
  - Győződjön meg arról, hogy a követelmények.
  - Csatlakozás az Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS). 
  - Erőforrás-szolgáltatók regisztrálásával.
  - Tesztelje a kapcsolatot.

## <a name="prerequisites"></a>Előfeltételek

Beállíthatja, hogy az Előfeltételek a [development Kitet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-kapcsolaton keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Telepítés [Azure Stack-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).
* Töltse le a [az Azure Stack működéséhez szükséges eszközök](azure-stack-powershell-download.md).

Győződjön meg arról, hogy a következő parancsfájl-változókat cserélje az Azure Stack-konfigurációból értékeket:

- **Az Azure AD-bérlő neve**  
  Az Azure Stack, például yourdirectory.onmicrosoft.com kezelésére szolgáló Azure AD-bérlő neve.
- **Az Azure Resource Manager-végpont**  
  Az Azure Stack development Kitet, az alapérték https://management.local.azurestack.external. Az Azure Stack integrált rendszerek Ez az érték beszerzéséhez forduljon a szolgáltatójához.

## <a name="connect-with-azure-ad"></a>Az Azure AD Connect

  ```PowerShell
  $AADTenantName = "yourdirectory.onmicrosoft.com"
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantId
   ```

## <a name="connect-with-ad-fs"></a>Csatlakozás az AD FS-sel

  ```PowerShell  
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $tenantId
  ```

## <a name="register-resource-providers"></a>Erőforrás-szolgáltató regisztrálása

Erőforrás-szolgáltatók automatikusan az új felhasználói előfizetések, amelyek nem rendelkeznek olyan erőforrások a portálon keresztül telepített aspektusnevek regisztrálva. Erőforrás-szolgáltató explicit módon regisztrálhatja a következő szkript futtatásával:

```PowerShell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Ha mindent beállítottunk, a kapcsolat tesztelése az Azure Stack-erőforrások létrehozása a PowerShell használatával. Egy tesztet hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. A következő paranccsal hozzon létre egy erőforráscsoportot "MyResourceGroup" nevű:

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések

- [Sablonok fejlesztése az Azure Stackhez](azure-stack-develop-templates.md)
- [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
- Ha a PowerShell beállítása a felhőkörnyezet operátor szeretne, olvassa el a [konfigurálása a PowerShell-környezet az Azure Stack-operátorokról](../azure-stack-powershell-configure-admin.md) cikk.