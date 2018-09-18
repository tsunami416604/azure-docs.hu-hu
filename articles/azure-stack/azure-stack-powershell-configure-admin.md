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
ms.date: 09/17/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: aa86b44364ee84a9640fe0b39b6279982f5594ad
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982525"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Csatlakozás Azure Stack PowerShell-kezelőként

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack, például az ajánlatok, tervek, kvóták és riasztások létrehozása-erőforrások kezelése a PowerShell segítségével konfigurálhatja. Ez a témakör segít az üzemeltető környezet konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

Futtassa a következő előfeltételek vonatkoznak, az a [development Kitet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-kapcsolaton keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

 - Telepítés [Azure Stack-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).  
 - Töltse le a [az Azure Stack működéséhez szükséges eszközök](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Konfigurálja az operátor környezetet, és jelentkezzen be az Azure Stackhez

Az Azure Stack-üzemeltető környezet konfigurálása a PowerShell használatával. Futtassa az alábbi parancsfájlok egyikét: az Azure ad-ben tenantName, GraphAudience endpoint és ArmEndpoint értékeket cserélje le a saját környezetének konfigurációját.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
````

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Most, hogy mindent beállítottunk beállításról, belül az Azure Stack-erőforrások létrehozása a PowerShell használatával. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. Az alábbi parancs segítségével hozzon létre egy erőforráscsoportot **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések

 - [Sablonok fejlesztése az Azure Stackhez](user/azure-stack-develop-templates.md)
 - [Sablonok üzembe helyezése a PowerShell-lel](user/azure-stack-deploy-template-powershell.md)