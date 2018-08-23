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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 445628679a09a1884f63cdce446adec476af39af
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060631"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>A sablon üzembe helyezése az Azure Stack PowerShell-lel

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Azure Stack üzembe helyezése Azure Resource Manager-sablonok a PowerShell használatával is. Ez a cikk bemutatja, hogyan-sablon üzembe helyezése a PowerShell használatával.

## <a name="run-azurerm-powershell-cmdlets"></a>Futtassa az AzureRM PowerShell-parancsmagok

Ebben a példában az AzureRM PowerShell-parancsmagok és a githubon tárolt sablonból használja. A sablon létrehoz egy Windows Server 2012 R2 Datacenter virtuális gépet.

>[!NOTE]
>Előtt meg ebben a példában, győződjön meg arról, hogy [PowerShell konfigurált](azure-stack-powershell-configure-user.md) az Azure Stack felhasználói számára.

1. Lépjen a <http://aka.ms/AzureStackGitHub> , és keresse meg a **101-egyszerű-windows-vm** sablont. A sablon mentése ezen a helyen: C:\\sablonok\\azuredeploy-101-egyszerű – a windows-vm.json.
2. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.
3. Cserélje le *felhasználónév* és *jelszó* a felhasználónév és jelszó és futtassa a parancsfájlt az alábbi szkriptben.

   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
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
   >Futtassa ezt a parancsfájlt, amikor valaki növelni az üzemelő példány felülírásának elkerülése érdekében a "$myNum" paraméter értékét.

4. Nyissa meg az Azure Stack portálon, válassza **Tallózás**, majd válassza ki **virtuális gépek** keresése az új virtuális gépet (*myDeployment001*).

## <a name="next-steps"></a>További lépések

[Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
