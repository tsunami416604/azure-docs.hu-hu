---
title: Üzembe helyezheti a sablonokat az Azure-verem PowerShell használatával |} Microsoft Docs
description: A sablon üzembe helyezése Azure verem PowerShell használatával.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4af82deef029120aa2699e7c69c501ae61a1e8bd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359815"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>A sablon telepítéséhez Azure verem PowerShell használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

PowerShell központi telepítése Azure Resource Manager-sablonok Azure verem használható. Ez a cikk bemutatja, hogyan lehet egy sablon üzembe helyezése a PowerShell használatával.

## <a name="run-azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok

Ez a példa AzureRM PowerShell-parancsmagok és a Githubon tárolt sablont. A sablon létrehoz egy Windows Server 2012 R2 Datacenter virtuális gépet.

>[!NOTE]
>Ebben a példában előtt győződjön meg arról, hogy [PowerShell konfigurált](azure-stack-powershell-configure-user.md) Azure verem felhasználók.

1. Ugrás a <http://aka.ms/AzureStackGitHub> , és keresse a **101-egyszerű-windows-alapú virtuális gép** sablont. Menti a sablont, erre a helyre: C:\\sablonok\\azuredeploy-101-egyszerű-windows-vm.json.
2. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.
3. Cserélje le *felhasználónév* és *jelszó* a felhasználónévvel és jelszóval és majd futtassa a parancsfájlt a következő parancsfájlban.

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
   >A parancsfájl futtatása Everytime növelhető a "$myNum" paraméter, a központi telepítés felülírásának elkerülése érdekében.

4. Nyissa meg a verem Azure portálon válassza **Tallózás**, majd válassza ki **virtuális gépek** található a új virtuális gép (*myDeployment001*).

## <a name="next-steps"></a>További lépések

[Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
