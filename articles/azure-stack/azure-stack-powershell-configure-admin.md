---
title: Az Azure verem PowerShell-környezet konfigurálása |} Microsoft Docs
description: Megtudhatja, hogyan konfigurálja az Azure verem PowerShell környezetet.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 86608ef8b3623682bd10498605f8b7b62c377ff1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076470"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Az Azure verem PowerShell-környezet konfigurálása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Beállíthatja, hogy a PowerShell használatával kezelheti az erőforrásokat, például ajánlatokat, tervek, kvóták és riasztásokat hozhat létre Azure-készlet. Ez a témakör segítséget nyújt a operátor környezet konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

Futtassa a következő előfeltételek származhatnak a [szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-en keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

 - Telepítés [Azure verem-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).  
 - Töltse le a [az Azure veremnek megfelelő működéséhez szükséges eszközök](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>A kezelő-környezet beállításait, és jelentkezzen be Azure verem

Az Azure-verem operátor környezet konfigurálása a PowerShell használatával. Azure AD vagy AD FS-ben, a telepítési típus alapján futtassa az alábbi parancsfájlok egyikét: az Azure AD tenantName, GraphAudience végpont és ArmEndpoint értékek cserélje le a saját környezet konfigurációjának.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Az Azure Active Directory (Azure AD-) alapú telepítések

````PowerShell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory összevonási szolgáltatások (AD FS)-alapú telepítések

````PowerShell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Most, hogy minden van beállításról, tegyük a PowerShell használatával létrehozni Azure verem erőforrásokat. Például hozzon létre egy erőforráscsoportot az alkalmazáshoz, és adja hozzá a virtuális gépet. Az alábbi parancs segítségével hozzon létre egy erőforráscsoportot a "Contoso.com" nevű:

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések
 - [Az Azure-verem sablonok fejlesztése](user/azure-stack-develop-templates.md)
 - [Sablonok üzembe helyezése a PowerShell-lel](user/azure-stack-deploy-template-powershell.md)
