---
title: Csatlakozhat az Azure Stack a PowerShell-lel kezelőként |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure Stack-kezelőként PowerShell használatával
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
ms.date: 11/08/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: f5df27df5901d6568b30e1ae4f40cae8b3de4c86
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51298819"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Csatlakozás Azure Stack PowerShell-kezelőként

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack, például az ajánlatok, tervek, kvóták és riasztások létrehozása-erőforrások kezelése a PowerShell segítségével konfigurálhatja. Ez a témakör segít az üzemeltető környezet konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

Futtassa a következő előfeltételek vonatkoznak, az a [development Kitet](.\asdk\asdk-connect.md#connect-with-rdp) vagy -ügyfélről Windows-alapú külső Ha [a ASDK VPN-kapcsolaton keresztül csatlakozó](.\asdk\asdk-connect.md#connect-with-vpn). 

 - Telepítés [Azure Stack-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).  
 - Töltse le a [az Azure Stack működéséhez szükséges eszközök](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Konfigurálja az operátor környezetet, és jelentkezzen be az Azure Stackhez

Az Azure Stack-üzemeltető környezet konfigurálása a PowerShell használatával. Futtassa az alábbi parancsfájlok egyikét: az Azure ad-ben tenantName, GraphAudience endpoint és ArmEndpoint értékeket cserélje le a saját környezetének konfigurációját.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRmEnvironment -Name "AzureStackAdmin" -ARMEndpoint $ArmEndpoint -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external


    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $tenantId
````

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Most, hogy mindent beállítottunk beállításról, belül az Azure Stack-erőforrások létrehozása a PowerShell használatával. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. Az alábbi parancs segítségével hozzon létre egy erőforráscsoportot **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések

 - [Sablonok fejlesztése az Azure Stackhez](user/azure-stack-develop-templates.md)
 - [Sablonok üzembe helyezése a PowerShell-lel](user/azure-stack-deploy-template-powershell.md)