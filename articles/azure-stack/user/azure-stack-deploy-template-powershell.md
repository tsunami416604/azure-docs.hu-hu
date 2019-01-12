---
title: A sablonok PowerShell használatával az Azure Stack üzembe helyezése |} A Microsoft Docs
description: A sablon üzembe helyezése az Azure Stack PowerShell használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 7a56b18130ffbeccc4756ef52c285633770d009b
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243605"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>A sablon üzembe helyezése az Azure Stack PowerShell-lel

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Azure Stack üzembe helyezése Azure Resource Manager-sablonok a PowerShell használatával is. Ez a cikk ismerteti a sablon üzembe helyezése a PowerShell használatával.

## <a name="run-azurerm-powershell-cmdlets"></a>Futtassa az AzureRM PowerShell-parancsmagok

Ez a példa **AzureRM** a Githubon tárolt PowerShell-parancsmagok és a egy sablont. A sablon létrehoz egy Windows Server 2012 R2 Datacenter virtuális gépet.

>[!NOTE]
>Előtt meg ebben a példában, győződjön meg arról, hogy [PowerShell konfigurált](azure-stack-powershell-configure-user.md) az Azure Stack felhasználói számára.

1. Lépjen a [ https://aka.ms/AzureStackGitHub ](https://aka.ms/AzureStackGitHub) , és keresse meg a **101-egyszerű-windows-vm** sablont. A sablon mentése ezen a helyen: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.
3. Cserélje le `username` és `password` az a felhasználónév és jelszó és futtassa a parancsfájlt a következő parancsfájlt:

    ```PowerShell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "local"
   
    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
        -NewStorageAccountName mystorage$myNum `
        -DnsNameForPublicIP mydns$myNum `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
        -VmName myVM$myNum `
        -WindowsOSVersion 2012-R2-Datacenter
    ```

    >[!IMPORTANT]
    >Ez a szkript minden futtatásakor értékét növelje a `$myNum` paraméter az üzembe helyezés felülírásának elkerülése érdekében.

4. Nyissa meg az Azure Stack portálon, válassza **Tallózás**, majd válassza ki **virtuális gépek** keresése az új virtuális gépet (**myDeployment001**).

## <a name="next-steps"></a>További lépések

- [Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
