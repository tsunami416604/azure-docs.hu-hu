---
title: "Sablonok a verem Azure PowerShell telepítése |} Microsoft Docs"
description: "Útmutató Resource Manager-sablon és a PowerShell segítségével a virtuális gép telepítése."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e4837be016b569dbd0b4bf8e071e6381b8daa85f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-powershell"></a>A PowerShell használata Azure verem sablonok telepítése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

PowerShell használatával telepítse az Azure verem szoftverfejlesztői készlet Azure Resource Manager-sablonok.  Resource Manager-sablonok telepítése, és jogosultságok kiosztása egyetlen, koordinált műveletben az alkalmazás összes erőforrást.

## <a name="run-azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok
Ebben a példában a parancsfájl futtatásakor a virtuális gép üzembe helyezése Azure verem szoftverfejlesztői készlet Resource Manager-sablon használatával.  A folytatás előtt ellenőrizze, hogy [PowerShell konfigurálva](azure-stack-powershell-configure-user.md)  

Ez a példa sablon szerepel a VHD WindowsServer-2012-R2 – Datacenter.

1. Nyissa meg a <http://aka.ms/AzureStackGitHub>, keresse meg a **101-egyszerű-windows-alapú virtuális gép** sablont, és mentse a következő helyen: c:\\sablonok\\ azuredeploy-101-egyszerű-windows-vm.json.
2. A PowerShellben futtassa a következő telepítési parancsfájl. Cserélje le *felhasználónév* és *jelszó* a felhasználónévvel és jelszóval. A későbbi felhasználás értéke növelhető a *$myNum* paraméter a központi telepítés felülírásának elkerülése érdekében.
   
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
3. Nyissa meg a verem Azure portálon kattintson **Tallózás**, kattintson a **virtuális gépek**, és keresse meg az új virtuális gép (*myDeployment001*).


## <a name="next-steps"></a>Következő lépések
[Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)

