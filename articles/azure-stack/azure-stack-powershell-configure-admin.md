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
ms.date: 06/22/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 74a5a9408a78dd0da12fb3f8ed721774030cc438
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749861"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Az Azure verem PowerShell-környezet konfigurálása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Beállíthatja, hogy a PowerShell használatával kezelheti az erőforrásokat, például ajánlatokat, tervek, kvóták és riasztásokat hozhat létre Azure-készlet. Ez a témakör segítséget nyújt a operátor környezet konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

Futtassa a következő előfeltételek származhatnak a [szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-en keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

 - Telepítés [Azure verem-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).  
 - Töltse le a [az Azure veremnek megfelelő működéséhez szükséges eszközök](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>A kezelő-környezet beállításait, és jelentkezzen be Azure verem

Az Azure-verem operátor környezet konfigurálása a PowerShell használatával. Futtassa az alábbi parancsfájlok egyikét: az Azure AD tenantName, GraphAudience végpont és ArmEndpoint értékek cserélje le a saját környezet konfigurációjának.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
````

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Most, hogy minden van beállításról, Azure verem erőforrások létrehozása a PowerShell használatával. Például hozzon létre egy erőforráscsoportot az alkalmazáshoz, és adja hozzá a virtuális gépet. Az alábbi parancs segítségével hozzon létre egy erőforráscsoportot nevű **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések

 - [Az Azure-verem sablonok fejlesztése](user/azure-stack-develop-templates.md)
 - [Sablonok üzembe helyezése a PowerShell-lel](user/azure-stack-deploy-template-powershell.md)
